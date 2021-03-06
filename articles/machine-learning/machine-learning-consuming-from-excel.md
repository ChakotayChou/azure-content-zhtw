<properties
	pageTitle="從 Excel 使用機器學習 Web 服務 | Microsoft Azure"
	description="從 Excel 使用 Azure Machine Learning Web 服務"
	services="machine-learning"
	documentationCenter=""
	authors="tedway"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/06/2016"
	ms.author="tedway"/>

# 從 Excel 使用 Azure Machine Learning Web 服務

 Azure Machine Learning Studio 可以讓您直接從 Excel 輕鬆呼叫 Web 服務，而不需要撰寫任何程式碼。

如果您使用 Excel 2013 (或更新版本) 或 Excel Online，我們建議您使用 [Excel 增益集](machine-learning-excel-add-in-for-web-services.md)。

[AZURE.INCLUDE [電腦-學習-免費-試用](../../includes/machine-learning-free-trial.md)]

## 步驟

發佈 Web 服務。[此頁面](machine-learning-walkthrough-5-publish-web-service.md)說明如何進行。目前，只針對具有單一輸出 (也就是單一計分標籤) 的要求/回應服務，才支援 Excel 活頁簿功能。

有了 Web 服務之後，請按一下 Studio 左側的 [Web 服務] 區段，然後選取要從 Excel 使用的 Web 服務。

**傳統 Web 服務**

1. Web 服務的 [儀表板] 索引標籤上有一列 [要求/回應] 服務。如果此服務有單一的輸出，您應該會在該列中看到 [下載 Excel 活頁簿] 連結。

	![][1]

2. 按一下 [下載 Excel 活頁簿]。

**新的 Web 服務**

1. 在 Azure Machine Learning Web Services 入口網站中，選取 [取用]。
2. 在 [取用] 頁面的 [Web 服務取用選項] 區段中，按一下 Excel 圖示。

**使用活頁簿**

1. 開啟活頁簿。

2. 此時會出現安全性警告；請按一下 [啟用編輯] 按鈕。

	![][2]

3. 此時會出現安全性警告。按一下 [啟用內容] 按鈕執行試算表上的巨集。

	![][3]
4. 啟用巨集之後，就會產生表格。輸入 RRS Web 服務時，必須輸入藍色的欄或 [參數]。請注意，RRS 服務的輸出：[預測值] 為綠色。當您填好指定列的所有欄之後，活頁簿會自動呼叫計分 API，並顯示計分的結果。

	![][4]

5. 若要計算超過一列的分數，請填寫第二列的資料，就會產生預測值。您甚至可以一次貼上多列。

有了預測值，您就可以使用任何 Excel 功能 (圖表、Power Map、設定格式化的條件等等) 協助將資料視覺化。


## 共用活頁簿

為了讓巨集能夠運作，試算表會儲存您的 API 金鑰。這表示您應該只與您信任的實體/個人共用活頁簿。

## 自動更新

下列兩種情況會產生 RRS 呼叫：

1. 當某一列的所有 [參數] 第一次有內容時。

2. 已在某一列輸入所有 [參數] 且其中任何 [參數] 有變更時。

[1]: ./media/machine-learning-consuming-from-excel/excellink.png
[2]: ./media/machine-learning-consuming-from-excel/enableeditting.png
[3]: ./media/machine-learning-consuming-from-excel/enablecontent.png
[4]: ./media/machine-learning-consuming-from-excel/sampletable.png

<!---HONumber=AcomDC_0720_2016-->