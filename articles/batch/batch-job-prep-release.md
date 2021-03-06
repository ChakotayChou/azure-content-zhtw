<properties
	pageTitle="Batch 中的工作準備和清理| Microsoft Azure"
	description="採用工作層級準備任務以減少傳輸到 Azure Batch 計算節點的資料，並在工作完成時解除任務以清理節點。"
	services="batch"
	documentationCenter=".net"
	authors="mmacy"
	manager="timlt"
	editor="" />

<tags
	ms.service="batch"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="big-compute"
	ms.date="06/22/2016"
	ms.author="marsma" />

# 在 Azure Batch 計算節點上執行準備和完成的工作

Azure Batch 作業通常在執行之前需要某種形式的安裝，在作業的工作完成之後也需要某種作業後維護。Batch 以選擇性的作業準備和作業解除任務的形式提供此準備和維護機制。

在任何作業的工作執行之前，作業準備工作會在排定要執行工作的所有計算節點上執行。作業一旦完成，作業解除工作會在集區中的每個節點上執行，集區至少會執行一項工作。如同一般的 Batch 工作，您可以將作業準備或解除工作的命令列指定為在工作執行時叫用。這些特殊的工作會提供其他熟悉的工作功能，例如檔案下載、提升權限的執行、自訂環境變數、最大執行持續時間、重試計數、檔案保留時間。

在接下來幾節中，您將了解如何在 [Batch .NET][api_net] API 中利用 [JobPreparationTask][net_job_prep] 類別和 [JobReleaseTask][net_job_release] 類別來使用這兩種特殊工作類型。

> [AZURE.TIP] 作業準備和作業解除工作在「共用集區」環境中特別有用；在這種環境中，計算節點的集區會在作業執行之間保存，且在許多不同作業之間共用。

## 使用作業準備和作業解除工作的時機

每當您需要為節點準備作業特定的組態或資料 (以及清除或保存工作結果資料) 時，便適合使用作業準備並解除工作。這類情況的範例如下︰

**常見工作資料的傳輸**

Batch 作業通常需要一組常用的資料做為作業工作的輸入。例如，在每日風險分析計算中，市場資料是作業專屬的，也是作業中所有工作通用的。這個市場資料 (通常是幾個 GB 的大小) 應該只下載到各個計算節點一次，讓在節點上執行的任何工作可以使用它。在作業的其他工作執行之前使用**作業準備工作**將資料下載到每個節點。

**作業資料刪除**

在共用集區環境中，集區的計算節點不會在作業之間被解除委任，為了要節省節點上的磁碟空間或可能為了滿足組織的安全性原則，可能需要在執行之間刪除作業資料。使用**作業解除工作**來刪除作業準備工作所下載的資料或在工作執行期間產生的資料。

**記錄保留**

您可能想要保留一份工作產生的記錄檔，或失敗應用程式所產生的損毀傾印檔案。在這類情況下使用**作業解除工作**來將這項資料壓縮並上傳至 [Azure 儲存體][azure_storage]帳戶。

## 工作準備任務

執行作業的工作之前，在排定執行工作的每個計算節點上會執行作業準備工作。依預設，Batch 服務會等作業準備工作完成，才執行節點上排定的工作。不過，您可以設定服務不要等待。如果計算節點重新啟動，在該節點上工作準備任務會再次執行，但您也可以停用此行為。

作業準備工作只會在排定執行工作的節點上執行。這可避免未指派工作的節點執行不必要的準備工作。這種情況會發生在當作業的工作數目小於集區中的節點數目時。此外，也適用於已啟用[並行工作執行](batch-parallel-node-tasks.md)時，而如果作業計數小於可能的並行工作總數，則會讓一些節點閒置。藉由不在閒置的節點上執行作業準備工作，您在資料傳輸費用上可以花費更少金錢。

> [AZURE.NOTE] [JobPreparationTask][net\_job\_prep\_cloudjob] 與 [CloudPool.StartTask][pool_starttask] 的不同之處在於，JobPreparationTask 在每個作業開始時執行，而 StartTask 只在計算節點第一次加入集區或重新啟動時執行。

## 工作解除任務

作業一旦標示為完成，作業解除工作會在集區中的每個節點上執行，集區至少會執行一項工作。透過發出終止要求將工作標示為完成。然後 Batch 服務將作業狀態設定為 *終止*，終止與作業相關聯的任何作用中或執行中任務，並執行作業解除任務。於是工作便進入*完成*狀態。

> [AZURE.NOTE] 工作刪除也會執行工作解除任務。不過，如果先前已終止作業，當後來刪除該作業時，解除工作不會執行第二次。

## 使用 Batch .NET 進行作業準備和作業解除工作

若要使用作業準備工作，您可建立及設定 [JobPreparationTask][net_job_prep] 物件並將它指派給作業的 [CloudJob.JobPreparationTask][net_job_prep_cloudjob] 屬性。同樣地，初始化 [JobReleaseTask][net_job_release] 並將它指派給作業的 [CloudJob.JobReleaseTask][net_job_prep_cloudjob] 屬性，即可設定作業的解除工作。

在此程式碼片段中，`myBatchClient` 是 [BatchClient][net_batch_client] 完全初始化的執行個體，`myPool` 是 Batch 帳戶內的現有集區。

		// Create the CloudJob for CloudPool "myPool"
		CloudJob myJob = myBatchClient.JobOperations.CreateJob("JobPrepReleaseSampleJob",
															   new PoolInformation() { PoolId = "myPool" });

		// Specify the command lines for the job preparation and release tasks
		string jobPrepCmdLine = "cmd /c echo %AZ_BATCH_NODE_ID% > %AZ_BATCH_NODE_SHARED_DIR%\\shared_file.txt";
		string jobReleaseCmdLine = "cmd /c del %AZ_BATCH_NODE_SHARED_DIR%\\shared_file.txt";

		// Assign the job preparation task to the job
		myJob.JobPreparationTask = new JobPreparationTask { CommandLine = jobPrepCmdLine };

		// Assign the job release task to the job
		myJob.JobReleaseTask = new JobPreparationTask { CommandLine = jobReleaseCmdLine };

		await myJob.CommitAsync();

如上所述，終止或刪除工作時會執行解除任務。呼叫 [JobOperations.TerminateJobAsync][net_job_terminate]，即可進行透過 Batch .NET API 終止作業。刪除作業則是透過 [JobOperations.DeleteJobAsync][net_job_delete] 執行。上述兩個動作通常是在作業的工作完成或達到定義之逾時時進行。

		// Terminate the job to mark it as Completed; this will initiate the Job Release Task on any node
		// that executed job tasks. Note that the Job Release Task is also executed when a job is deleted,
		// thus you need not call Terminate if you typically delete your jobs upon task completion.
		await myBatchClient.JobOperations.TerminateJobAsync("JobPrepReleaseSampleJob");

## GitHub 上的程式碼範例

請看 GitHub 上的範例專案 [JobPrepRelease][job_prep_release_sample]，了解工作準備和工作解除的運作。此主控台應用程式會做這些事：

1. 建立包含兩個「小」節點的集區。
2. 建立具有作業準備、解除和標準工作的作業。
3. 執行作業準備工作，將節點識別碼第一次寫入節點的「共用」目錄的文字檔中。
4. 在每個節點上執行工作，將其工作識別碼寫入同一文字檔案中。
5. 一旦完成所有工作 (或達到逾時)，會列印每個節點的文字檔案的內容到主控台。
6. 在作業完成時，執行作業解除工作會將該檔案從節點中刪除。
6. 列印執行作業準備和解除工作的每個節點上這些工作的結束代碼。
7. 暫停執行以允許確認刪除作業和/或集區。

範例應用程式的輸出類似這樣：

```
Attempting to create pool: JobPrepReleaseSamplePool
Created pool JobPrepReleaseSamplePool with 2 small nodes
Checking for existing job JobPrepReleaseSampleJob...
Job JobPrepReleaseSampleJob not found, creating...
Submitting tasks and awaiting completion...
All tasks completed.

Contents of shared\job_prep_and_release.txt on tvm-2434664350_1-20160623t173951z:
-------------------------------------------
tvm-2434664350_1-20160623t173951z tasks:
  task001
  task004
  task005
  task006

Contents of shared\job_prep_and_release.txt on tvm-2434664350_2-20160623t173951z:
-------------------------------------------
tvm-2434664350_2-20160623t173951z tasks:
  task008
  task002
  task003
  task007

Waiting for job JobPrepReleaseSampleJob to reach state Completed
...

tvm-2434664350_1-20160623t173951z:
  Prep task exit code:    0
  Release task exit code: 0

tvm-2434664350_2-20160623t173951z:
  Prep task exit code:    0
  Release task exit code: 0

Delete job? [yes] no
yes
Delete pool? [yes] no
yes

Sample complete, hit ENTER to exit...
```

>[AZURE.NOTE] 因為新集區中各個節點的建立和啟動時間並不一樣 (某些節點比其他節點還早做好工作準備)，您可能會看到不同的輸出。具體而言，因為工作會快速完成，集區的某個節點可能會執行作業的所有工作。如果發生這種情況，您會發現未執行任何工作的節點不會有作業準備和作業解除工作存在。

### 在 Azure 入口網站中檢查作業準備和作業解除工作

當您執行上述範例應用程式時，您可以使用 [Azure 入口網站][portal]檢視作業和其工作的屬性，或甚至下載作業的工作修改過的共用文字檔案。

下面的螢幕擷取畫面顯示在執行範例應用程式之後，Azure 入口網站中所出現的**準備工作刀鋒視窗**。在工作完成之後 (但在刪除作業與集區之前) 瀏覽至「JobPrepReleaseSampleJob」屬性，然後按一下 [準備工作] 或 [解除工作] 以檢視其屬性。

![Azure 入口網站中的作業準備屬性][1]

## 後續步驟

### 應用程式封裝

除了作業準備工作外，您還可以使用 Batch 的[應用程式封裝](batch-application-packages.md)功能來為計算節點做好工作執行準備。這項功能特別適合用於部署不需要執行安裝程式的應用程式、包含許多 (100 個以上) 檔案的應用程式，或需要嚴格版本控制的應用程式。

### 安裝應用程式和預備資料

請查看 Azure Batch 論壇中的[在 Batch 計算節點安裝應用程式和預備資料][forum_post]文章，以取得準備節點以執行工作的各種方法概觀。這篇文章是由 Azure Batch 小組的其中一名成員所撰寫，非常適合做為入門指南，以讓您了解將檔案 (包括應用程式和工作的輸入資料) 放到計算節點的不同方法，以及每個方法所應納入的一些特殊考量。

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_listjobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[azure_storage]: https://azure.microsoft.com/services/storage/
[portal]: https://portal.azure.com
[job_prep_release_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/JobPrepRelease
[forum_post]: https://social.msdn.microsoft.com/Forums/zh-TW/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[net_batch_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_job_prep]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobpreparationtask.aspx
[net_job_prep_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobpreparationtask.aspx
[net\_job\_prep\_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobpreparationtask.aspx
[net_job_delete]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.deletejobasync.aspx
[net_job_terminate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.terminatejobasync.aspx
[net_job_release]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobreleasetask.aspx
[net_job_release_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobreleasetask.aspx
[pool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx

[net_list_certs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificateoperations.listcertificates.aspx
[net_list_compute_nodes]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listcomputenodes.aspx
[net_list_job_schedules]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobscheduleoperations.listjobschedules.aspx
[net_list_jobprep_status]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobpreparationandreleasetaskstatus.aspx
[net_list_jobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[net_list_nodefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listnodefiles.aspx
[net_list_pools]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listpools.aspx
[net_list_schedule_jobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobscheduleoperations.listjobs.aspx
[net_list_task_files]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listnodefiles.aspx
[net_list_tasks]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listtasks.aspx

[1]: ./media/batch-job-prep-release/portal-jobprep-01.png

<!---HONumber=AcomDC_0629_2016-->