<properties
	pageTitle="在 Log Analytics 中使用 Active Directory 評估方案進行環境最佳化 | Microsoft Azure"
	description="您可以使用 Active Directory 評估方案定期評估伺服器環境的風險和健全狀況。"
	services="log-analytics"
	documentationCenter=""
	authors="bandersmsft"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="log-analytics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/28/2016"
	ms.author="banders"/>

# 在 Log Analytics 中使用 Active Directory 評估方案進行環境最佳化

您可以使用 Active Directory 評估方案定期評估伺服器環境的風險和健全狀況。本文將協助您安裝和使用方案，讓您可以針對潛在問題採取修正動作。

此方案能針對已部署的伺服器基礎結構提供依照優先順序排列的具體建議清單。建議分為四個焦點區域，它們可以幫助您快速了解風險並採取動作。

建議乃源自 Microsoft 工程師數千次客戶拜訪所得到的知識和經驗。每項建議均提供問題的影響層面，以及如何實作建議變更等相關指引。

您可以選擇對組織而言最重要的焦點區域，同時追蹤經營無風險且健康狀態良好之環境的進度。

加入方案且評估完成之後，系統會將焦點區域的摘要資訊顯示在環境之基礎結構的 [AD 評估] 儀表板中。下列章節說明如何使用 [AD 評估] 儀表板上的資訊，您可以在這裡檢視 Active Directory 伺服器基礎結構的建議動作並予以實施。

![SQL 評估磚的影像](./media/log-analytics-ad-assessment/ad-tile.png)

![SQL 評估儀表板的影像](./media/log-analytics-ad-assessment/ad-assessment.png)


## 安裝和設定方案
請使用下列資訊來安裝和設定方案。

- Active Directory 評估方案需要具有 OMS 代理程式的每部電腦上都安裝 .NET Framework 4。
- 使用[從方案庫加入 Log Analytics 方案](log-analytics-add-solutions.md)中的程序，將 Active Directory 評估方案加入您的 OMS 工作區中。不需要進一步的組態。

    >[AZURE.NOTE] 您加入方案之後，AdvisorAssessment.exe 檔案會以代理程式加入伺服器中。組態資料會先讀取後再傳送至雲端中的 OMS 服務，以便進行處理。會將邏輯套用至接收的資料，且雲端服務會記錄資料。

## 評估資料收集詳細資料

下表顯示代理程式的資料收集方法、是否需要 Operations Manager (SCOM)，以及如何代理程式收集資料的頻率。

| 平台 | 直接代理程式 | SCOM 代理程式 | Azure 儲存體 | SCOM 是否為必要項目？ | 透過管理群組傳送的 SCOM 代理程式資料 | 收集頻率 |
|---|---|---|---|---|---|---|
|Windows|![是](./media/log-analytics-ad-assessment/oms-bullet-green.png)|![是](./media/log-analytics-ad-assessment/oms-bullet-green.png)|![否](./media/log-analytics-ad-assessment/oms-bullet-red.png)|	![否](./media/log-analytics-ad-assessment/oms-bullet-red.png)|![是](./media/log-analytics-ad-assessment/oms-bullet-green.png)|	7 天|


## 了解建議的排列方式

智慧套件會為每項建議指派加權值，該值能顯現建議的相對重要性。唯有重要性排行前十名的建議會出現在清單中。

### 加權的計算方式

加權是彙集以下三個重要因素的值：

- 識別之疑難引發問題的*機率*。機率較高等同於建議的整體分數較高。

- 疑難對組織的*影響力* (如果確實引發問題)。影響力較高等同於建議的整體分數較高。

- 實作建議所需的*勞力*。勞力較高等同於建議的整體分數較低。

每項建議之加權的表示採用每個焦點區域之總分的百分比。例如，如果針對安全性和法務遵循焦點區域之建議的分數為 5%，代表實作該項建議能增加 5% 的安全性和法務遵循整體分數。

### 焦點區域

**安全性和法務遵循** - 這個焦點區域顯示下列項目的建議：潛在安全性威脅和填補缺口、公司原則，以及技術、法律和法務遵循要求。

**可用性和業務續航力** - 這個焦點區域顯示下列項目的建議：服務可用性、基礎結構備援和企業保護。

**效能和延展性** - 這個焦點區域顯示建議來協助您組織的 IT 基礎結構成長、確定您的 IT 環境是否符合目前的效能需求，而且能夠回應不斷變動的基礎結構需求。

**升級、移轉和部署** - 這個焦點區域顯示建議來協助您升級、移轉和部署 Active Directory 到您的現有基礎結構。

### 我應該為每個焦點區域訂定 100% 的分數嗎？

不一定。建議乃源自 Microsoft 工程師上千次客戶拜訪所得到的知識和經驗。然而，世界上沒有兩個一模一樣的伺服器基礎結構，因此特定建議與您的關聯性可能會有所增減。例如，如果您的虛擬機器並未暴露在網際網路中，某些安全性建議的關聯性就會降低。對於提供低優先順序臨機操作資料收集和報告的服務來說，某些可用性建議的關聯性就會降低。會對成熟企業造成重大影響的問題，不見得會對新公司造成同等嚴重的影響。因此，建議您先找出自己的優先焦點區域，然後觀察一段時間內的分數變化。

每項建議都包含其重要性的指引。在已知 IT 服務之本質和組織之商務需求的情況下，您應使用該指引來評估實作建議的適當性。

## 使用評估焦點區域建議

在使用 OMS 中的評估方案之前，您必須先安裝方案。如需閱讀安裝方案的更多資訊，請參閱[從方案庫加入 Log Analytics 方案](log-analytics-add-solutions.md)。安裝之後，您可以在 OMS 中使用 [概觀] 頁面上的 [評估] 圖格檢視建議摘要。

檢視基礎結構的總結法務遵循評估結果，然後再深入鑽研建議事項。


### 檢視的焦點區域的建議並採取更正措施

1. 在 [**概觀**] 頁面中按一下伺服器基礎結構的 [**評估**] 磚。
2. 在 [**評估**] 頁面中檢閱任一焦點區域分葉中的摘要資訊，然後按一下焦點區域以檢視建議。
3. 在任一焦點區域頁面中，您可以檢視針對環境且按照優先順序排列的建議。按一下 [受影響的物件] 下方的建議，可檢視建議提出原因的詳細資料。![SQL 評估建議的影像](./media/log-analytics-ad-assessment/ad-focus.png)
4. 您可以採取 [建議動作] 中所建議的更正動作。當您解決某個項目後，後續評估會記錄您實施的建議動作並提高法務遵循分數。更正後的項目將呈現為 [**通過的物件**]。

## 忽略建議

如果您有想要忽略的建議，則可以建立 OMS 將用來防止建議出現在您評估結果的文字檔。

### 識別您將忽略的建議

1.	登入您的工作區，並開啟記錄檔搜尋。使用下列查詢來列出您環境中電腦的失敗建議。

    ```
    Type=ADAssessmentRecommendation RecommendationResult=Failed | select  Computer, RecommendationId, Recommendation | sort  Computer
    ```

    以下是顯示記錄檔搜尋查詢的螢幕擷取畫面︰![失敗的建議](./media/log-analytics-ad-assessment/ad-failed-recommendations.png)

2.	選擇您想要忽略的建議。您將使用下一個程序中的 RecommendationId 值。


### 建立及使用 IgnoreRecommendations.txt 文字檔案

1.	建立名為 IgnoreRecommendations.txt 的檔案。
2.	在個別行上貼上或輸入您想要 Log Analytics 忽略之每個建議的各個 RecommendationId，然後儲存並關閉檔案。
3.	將檔案放在您想要 OMS 忽略建議之每一部電腦的下列資料夾中。
    - 在具有 Microsoft 監視代理程式的電腦 (直接或透過 Operations Manager 連線) 上 - *SystemDrive*:\\Program Files\\Microsoft Monitoring Agent\\Agent
    - 在 Operations Manager 管理伺服器上 - *SystemDrive*:\\Program Files\\Microsoft System Center 2012 R2\\Operations Manager\\Server

### 驗證已忽略建議

在下一個排定的評估執行之後，依預設是每隔 7 天執行一次，指定的建議會標示為「已忽略」，且不會出現在評估儀表板。

1. 您可以使用下列記錄搜尋查詢列出所有已忽略的建議。

    ```
    Type=ADAssessmentRecommendation RecommendationResult=Ignored | select  Computer, RecommendationId, Recommendation | sort  Computer
    ```

2.	如果您稍後決定想要查看忽略的建議，請移除任何 IgnoreRecommendations.txt 檔案，或從中移除 RecommendationID。

## AD 評估方案常見問題集

*評估的執行頻率為何？*
- 評估會每隔 7 天執行一次。

*是否有設定評估執行頻率的方法？*
- 目前沒有。

*如果我在加入評估方案後探索到另一部伺服器，方案也會評估這部伺服器嗎？*
- 是的。智慧套件會在探索到該伺服器之後每隔 7 天評估一次。

*如果把伺服器除役，何時能將它從評估中移除？*
- 如果伺服器在 3 週內未提交任何資料，智慧套件便會將其移除。

*負責收集資料之處理序的名稱為何？*
- AdvisorAssessment.exe

*收集資料需要花費多少時間？*
- 伺服器上的實際資料收集需費時約 1 小時。對於擁有大量 Active Directory 伺服器的伺服器，資料收集可能需要花費更久的時間。

*收集的資料類型為何？*
- 收集的資料類型如下：
    - WMI
    - 登錄
    - 效能計數器

*是否有設定資料收集時間的方法？*
- 目前沒有。

*為什麼只顯示前 10 項建議？*
- 與其提供鉅細靡遺的工作清單，我們建議您先著重於解決優先建議事項。解決後，智慧套件將會提供其他建議。如果您想要查看詳細清單，可以使用記錄檔搜尋來檢視所有建議。

*是否有忽略建議的方法？*
- 是，請參閱上面的[忽略建議](#ignore-recommendations)一節。


## 後續步驟

- 使用 [Log Analytics 中的記錄檔搜尋](log-analytics-log-searches.md)可檢視詳細的 AD 評估資料和建議。

<!---HONumber=AcomDC_0504_2016-->