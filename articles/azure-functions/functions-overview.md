<properties
   pageTitle="Azure Functions 概觀 | Microsoft Azure"
   description="了解如何使用 Azure Functions，在數分鐘內最佳化非同步的工作負載。"
   services="functions"
   documentationCenter="na"
   authors="mattchenderson"
   manager="erikre"
   editor=""
   tags=""
   keywords="azure functions, 函數, 事件處理, webhook, 動態計算, 無伺服器架構"/>

<tags
   ms.service="functions"
   ms.devlang="multiple"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="08/29/2016"
   ms.author="cfowler;mahender;glenga"/>
   
   
# Azure Functions 概觀

Azure Functions 是可在雲端輕鬆執行程式碼片段或「函數」的解決方案。您可以只撰寫處理手邊問題所需的程式碼，而不需擔心要執行它的整個應用程式或基礎結構。這會讓開發更有生產力，而且您可以使用您選擇的開發語言，例如 C#、Node.js、Python 或 PHP。只需對您的程式碼執行的時間付費，並信任 Azure 視需要調整。

本主題提供 Azure Functions 的高階概觀。如果您想要直接進入正題並開始使用 Azure Functions，請從[建立您的第一個Azure Functions](functions-create-first-azure-function.md) 著手。如果您要尋找更多有關 Functions 的技術資訊，請參閱[開發人員參考](functions-reference.md)。

## 特性

以下是 Azure Functions 的一些主要功能︰
    
* **選擇的語言** - 使用 C#、Node.js、Python、F#、PHP、batch、bash、Java 或任何可執行檔撰寫函數。
* **使用即付費價格模式** - 只對執行您的程式碼所花的時間付費。請參閱底下[價格](#pricing)一節中的「動態 App Service 方案」選項。
* **自備相依性** - Functions 支援 NuGet 和 NPM，以便您使用您最愛的程式庫。
* **整合式安全性** - 利用 OAuth 提供者 (如 Azure Active Directory、Facebook、Google、Twitter 和 Microsoft 帳戶) 保護 HTTP 觸發的函數。
* **簡化整合** - 輕鬆地利用 Azure 服務和軟體即服務 (SaaS) 供應項目。請參閱底下[整合](#integrations)一節中的相關範例。
* **彈性開發** - 直接在入口網站中撰寫函數的程式碼，或透過 GitHub、Visual Studio Team Services 和其他[支援的開發工具](../app-service-web/web-sites-deploy.md#deploy-using-an-ide)設定連續整合和部署程式碼。
* **開放原始碼** - Functions 執行階段是開放原始碼的平台並[可在 GitHub 上取得](https://github.com/azure/azure-webjobs-sdk-script)。

## 我可以用 Functions 來做什麼？

Azure Functions 是處理資料、整合系統、使用物聯網 (IoT)，以及建置簡單 API 和微服務的絕佳解決方案。將 Functions 視為如下的工作：映像或訂單處理、檔案維護、您要在背景執行緒中執行的長期執行工作，或者您要排程執行的任何工作。

Functions 提供範本，可讓您開始使用重要的案例，包括下列案例︰

* **BlobTrigger** - 在新增至容器時，處理 Azure 儲存體 blob。您可以針對調整映像大小使用。
* **EventHubTrigger** - 回應傳送到 Azure 事件中樞的事件。特別適合用於應用程式檢測、使用者經驗或工作流程處理及物聯網 (IoT) 案例。
* **泛型 webhook** - 處理來自支援 webhook 的任何服務的 webhook HTTP 要求。
* **GitHub webhook** - 回應您的 GitHub 儲存機制中發生的事件。如需範例，請參閱[建立 Webhook 或 API 函數](functions-create-a-web-hook-or-api-function.md)。
* **HTTPTrigger** - 使用 HTTP 要求觸發程式碼的執行。
* **QueueTrigger** - 在訊息送達 Azure 儲存體佇列中時回應。如需範例，請參閱[建立繫結至 Azure 服務的 Azure 函數](functions-create-an-azure-connected-function.md)。
* **ServiceBusQueueTrigger** - 將程式碼連接至其他 Azure 服務或內部部署服務，方法是接聽訊息佇列。
* **ServiceBusTopicTrigger** - 將程式碼連接至其他 Azure 服務或內部部署服務，方法是訂閱主題。
* **TimerTrigger** - 在預先定義的排程執行清除或其他批次工作。如需範例，請參閱[建立事件處理函數](functions-create-an-event-processing-function.md)。

Azure Functions 支援「觸發」，這是開始執行您的程式碼的方式，以及「繫結」，這是針對輸入和輸出資料簡化編碼的方式。如需 Azure Functions 提供的觸發和繫結的詳細說明，請參閱 [Azure Functions 觸發和繫結開發人員參考](functions-triggers-bindings.md)。


## <a name="integrations"></a>整合

Azure Functions 可以與各種 Azure 和第三方服務整合。您可以利用這些整合來觸發您的函數並開始執行，或做為您的程式碼的輸入和輸出。Azure Functions 支援下列服務整合。

* Azure DocumentDB
* Azure 事件中心
* Azure Mobile Apps (資料表)
* Azure 通知中心
* Azure 服務匯流排 (佇列和主題)
* Azure 儲存體 (Blob、佇列和資料表)
* GitHub (webhook)
* 內部部署 (使用服務匯流排)

## <a name="pricing"></a>Functions 的計費方式

Azure Functions 有兩種價格方案，選擇一個最適合您的需求的方案︰

* **動態主控方案** - 當您的函數執行時，Azure 會提供所有必要的運算資源。您不必擔心資源管理，您只需為您的程式碼執行時間支付費用。在 [Functions 價格](/pricing/details/functions)頁面上可取得完整的價格詳細資料。

* **App Service 方案** - 可讓您如同 Web、行動及 API 應用程式一樣執行函數。當您已準備對其他應用程式使用 App Service 時，您可以在相同方案上執行您的函數，不會產生額外的費用。如需完整的詳細資訊，請參閱 [App Service 價格](/pricing/details/app-service/)頁面。

如需有關調整函數的詳細資訊，請參閱[如何調整 Azure Functions](functions-scale.md)。

##後續步驟

+ [建立您的第一個 Azure 函數](functions-create-first-azure-function.md) 直接進入正題並使用 Azure Functions 快速入門建立您的第一個函數。
+ [Azure Functions 開發人員參考](functions-reference.md) 提供更多有關 Azure Functions 執行階段的技術資訊，以及可供撰寫函數程式碼及定義觸發程序和繫結時參考。
+ [測試 Azure Functions](functions-test-a-function.md) 說明可用於測試函數的各種工具和技巧。
+ [如何調整 Azure Functions](functions-scale.md) 討論 Azure Functions 可用的服務方案，包括動態服務方案，以及如何選擇正確的方案。
+ [深入了解 Azure App Service](../app-service/app-service-value-prop-what-is.md) Azure Functions 會利用 Azure App Service 平台執行核心功能，例如部署、環境變數和診斷。

<!---HONumber=AcomDC_0907_2016-->