<properties
	pageTitle="使用 Azure Media Indexer 2 Preview 編製媒體檔案索引"
	description="Azure Media Indexer 讓您能將媒體檔案的內容變成可搜尋，並產生隱藏式字幕和關鍵字的全文檢索記錄。本主題說明如何使用 Media Indexer 2 Preview。"
	services="media-services"
	documentationCenter=""
	authors="Juliako"
	manager="erikre"
	editor=""/>

<tags
	ms.service="media-services"
	ms.workload="media"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="06/22/2016" 
	ms.author="adsolank;juliako;"/>


# 使用 Azure Media Indexer 2 Preview 編製媒體檔案索引

##概觀

**Azure Media Indexer 2 Preview** 媒體處理器 (MP) 可讓您將媒體檔案和內容設為可供搜尋，並產生隱藏式輔助字幕追蹤。相較於舊版的 [Azure Media Indexer](media-services-index-content.md)，**Azure Media Indexer 2 Preview** 編製索引速度更快，並提供更廣泛的語言支援。支援的語言包括英文、西班牙文、法文、德文、義大利文、中文、葡萄牙文和阿拉伯文。

**Azure Media Indexer 2 Preview** MP 目前為預覽功能。

本主題說明如何使用 **Azure Media Indexer 2 Preview** 建立索引工作。

>[AZURE.NOTE]您必須考量下列事項：
>
>Azure China 和 Azure Government 不支援 Indexer 2。
>
>Preview 有 ~10 分鐘處理的限制，但對所有客戶均免費。
>
>在編製內容索引時，請務必使用語音非常清楚的媒體檔案 (不含背景音樂、噪音、效果或麥克風雜音)。適當內容的一些範例有：錄製的會議、演講或簡報。下列內容可能不適合用來編製索引：電影、電視節目、任何具有混合音訊與音效的內容、錄製效果不良有背景噪音 (雜音) 的內容。


本主題提供有關 **Azure Media Indexer 2 Preview** 的詳細資訊，並示範如何搭配適用於 .NET 的媒體服務 SDK 來使用它。

##輸入和輸出檔案

###輸入檔案

音訊或視訊檔案

###輸出檔案

索引工作可以產生下列格式的隱藏式輔助字幕檔案：

- **SAMI**
- **TTML**
- **WebVTT**

這些格式的隱藏式輔助字幕 (CC) 檔案可以用來讓具有聽力障礙的人存取音訊和視訊檔案。

##工作組態 (預設)

以 **Azure 媒體索引器 2 預覽**建立索引工作時，您必須指定設定預設值。

下列 JSON 會設定可用的參數。

	{
	  "version":"1.0",
	  "Features":
	    [
	       {
	       "Options": {
	            "Formats":["WebVtt","ttml"],
	            "Language":"enUs",
	            "Type":"RecoOptions"
	       },
	       "Type":"SpReco"
	    }]
	}

##支援的語言  

Azure Media Indexer 2 Preview 支援下列語言的語音轉文字 (在工作設定中指定語言名稱時，請使用以括號括住的 4 字元程式碼，如下所示)：

- 英文 [EnUs]
- 西班牙文 [EsEs]
- 中文 [ZhCn]
- 法文 [FrFr]
- 德文 [DeDe]
- 義大利文 [ItIt]
- 葡萄牙文 [PtBr]
- 阿拉伯文 (埃及) [ArEg]


## 範例程式碼

下列程式將示範如何：

1. 建立資產並將媒體檔案上傳到資產。
1. 根據包含下列 JSON 預設值的組態檔案，建立編製索引工作的工作。
			
		{
		  "version":"1.0",
		  "Features":
		    [
		       {
		       "Options": {
		            "Formats":["WebVtt","ttml"],
		            "Language":"enUs",
		            "Type":"RecoOptions"
		       },
		       "Type":"SpReco"
		    }]
		}

1. 下載輸出檔案。
	
		using System;
		using System.Configuration;
		using System.IO;
		using System.Linq;
		using Microsoft.WindowsAzure.MediaServices.Client;
		using System.Threading;
		using System.Threading.Tasks;
		
		namespace IndexContent
		{
		    class Program
		    {
		        // Read values from the App.config file.
		        private static readonly string _mediaServicesAccountName =
		            ConfigurationManager.AppSettings["MediaServicesAccountName"];
		        private static readonly string _mediaServicesAccountKey =
		            ConfigurationManager.AppSettings["MediaServicesAccountKey"];
		
		        // Field for service context.
		        private static CloudMediaContext _context = null;
		        private static MediaServicesCredentials _cachedCredentials = null;
		
		        static void Main(string[] args)
		        {
		
		            // Create and cache the Media Services credentials in a static class variable.
		            _cachedCredentials = new MediaServicesCredentials(
		                            _mediaServicesAccountName,
		                            _mediaServicesAccountKey);
		            // Used the cached credentials to create CloudMediaContext.
		            _context = new CloudMediaContext(_cachedCredentials);
		
		            // Run indexing job.
		            var asset = RunIndexingJob(@"C:\supportFiles\Indexer\BigBuckBunny.mp4",
		                                        @"C:\supportFiles\Indexer\config.json");
		
		            // Download the job output asset.
		            DownloadAsset(asset, @"C:\supportFiles\Indexer\Output");
		        }
		
		        static IAsset RunIndexingJob(string inputMediaFilePath, string configurationFile)
		        {
		            // Create an asset and upload the input media file to storage.
		            IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
		                "My Indexing Input Asset",
		                AssetCreationOptions.None);
		
		            // Declare a new job.
		            IJob job = _context.Jobs.Create("My Indexing Job");
		
		            // Get a reference to Azure Media Indexer 2 Preview.
		            string MediaProcessorName = "Azure Media Indexer 2 Preview";
		
		            var processor = GetLatestMediaProcessorByName(MediaProcessorName);
		
		            // Read configuration from the specified file.
		            string configuration = File.ReadAllText(configurationFile);
		
		            // Create a task with the encoding details, using a string preset.
		            ITask task = job.Tasks.AddNew("My Indexing Task",
		                processor,
		                configuration,
		                TaskOptions.None);
		
		            // Specify the input asset to be indexed.
		            task.InputAssets.Add(asset);
		
		            // Add an output asset to contain the results of the job.
		            task.OutputAssets.AddNew("My Indexing Output Asset", AssetCreationOptions.None);
		
		            // Use the following event handler to check job progress.  
		            job.StateChanged += new EventHandler<JobStateChangedEventArgs>(StateChanged);
		
		            // Launch the job.
		            job.Submit();
		
		            // Check job execution and wait for job to finish.
		            Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
		
		            progressJobTask.Wait();
		
		            // If job state is Error, the event handling
		            // method for job progress should log errors.  Here we check
		            // for error state and exit if needed.
		            if (job.State == JobState.Error)
		            {
		                ErrorDetail error = job.Tasks.First().ErrorDetails.First();
		                Console.WriteLine(string.Format("Error: {0}. {1}",
		                                                error.Code,
		                                                error.Message));
		                return null;
		            }
		
		            return job.OutputMediaAssets[0];
		        }
		
		        static IAsset CreateAssetAndUploadSingleFile(string filePath, string assetName, AssetCreationOptions options)
		        {
		            IAsset asset = _context.Assets.Create(assetName, options);
		
		            var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
		            assetFile.Upload(filePath);
		
		            return asset;
		        }
		
		        static void DownloadAsset(IAsset asset, string outputDirectory)
		        {
		            foreach (IAssetFile file in asset.AssetFiles)
		            {
		                file.Download(Path.Combine(outputDirectory, file.Name));
		            }
		        }
		
		        static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
		        {
		            var processor = _context.MediaProcessors
		                .Where(p => p.Name == mediaProcessorName)
		                .ToList()
		                .OrderBy(p => new Version(p.Version))
		                .LastOrDefault();
		
		            if (processor == null)
		                throw new ArgumentException(string.Format("Unknown media processor",
		                                                           mediaProcessorName));
		
		            return processor;
		        }
		
		        static private void StateChanged(object sender, JobStateChangedEventArgs e)
		        {
		            Console.WriteLine("Job state changed event:");
		            Console.WriteLine("  Previous state: " + e.PreviousState);
		            Console.WriteLine("  Current state: " + e.CurrentState);
		
		            switch (e.CurrentState)
		            {
		                case JobState.Finished:
		                    Console.WriteLine();
		                    Console.WriteLine("Job is finished.");
		                    Console.WriteLine();
		                    break;
		                case JobState.Canceling:
		                case JobState.Queued:
		                case JobState.Scheduled:
		                case JobState.Processing:
		                    Console.WriteLine("Please wait...\n");
		                    break;
		                case JobState.Canceled:
		                case JobState.Error:
		                    // Cast sender as a job.
		                    IJob job = (IJob)sender;
		                    // Display or log error details as needed.
		                    // LogJobStop(job.Id);
		                    break;
		                default:
		                    break;
		            }
		        }
		    }
		}


##媒體服務學習路徑

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


## 相關連結

[Azure 媒體服務分析概觀](media-services-analytics-overview.md)

[Azure 媒體分析示範](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)

<!---HONumber=AcomDC_0629_2016-->