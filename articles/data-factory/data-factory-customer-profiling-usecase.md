<properties 
	pageTitle="使用案例 - 客戶分析" 
	description="了解如何使用 Azure Data Factory 建立資料驅動的工作流程 (管線) 以分析遊戲客戶。" 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/26/2016" 
	ms.author="spelluru"/>

# 使用案例 - 客戶分析

Azure Data Factory 是許多服務之一，可用來實作解決方案加速器的 Cortana 分析套件。如需有關 Cortana 分析的詳細資訊，請造訪 [Cortana 分析套件](http://www.microsoft.com/cortanaanalytics)。在本文中，我們會說明簡單的使用案例，以幫助您開始著手了解 Azure Data Factory 如何解決常見的分析問題。

若要存取並嘗試這個簡單的使用案例，您只需要 [Azure 訂用帳戶](https://azure.microsoft.com/pricing/free-trial/)。您可以藉由遵循[範例](data-factory-samples.md)一文所述的步驟，部署實作此使用案例的範例。

## 案例

Contoso 是為多個平台建立遊戲的遊戲公司，包含遊戲主機、手持裝置與個人電腦 (PC)。當使用者玩這些遊戲時，會產生大量的記錄檔資料，可追蹤使用模式、遊戲風格與使用者的喜好設定。結合人口統計、區域和產品資料時，Contoso 可以執行分析，引導他們如何增強每個使用者的體驗，並使他們達到升級及在遊戲中購買的目標。

Contoso 的目標是要根據遊戲使用者的遊戲歷程記錄設定檔識別向上銷售和交叉銷售的機會，並開發新的強大功能來推動業務成長以及為客戶提供更好的體驗。對於此使用案例，我們使用遊戲公司做為想要最佳化使用者行為的企業範例，但是這些原則適用於所有想要建立客戶及產品和服務之關聯，並強化其客戶體驗的所有企業。

## 挑戰

在嘗試實作這種類型的使用案例時，遊戲公司面臨許多挑戰。首先，必須從多個資料來源 (包括內部部署和雲端) 擷取不同大小和形狀的資料，擷取使用者在多個平台上玩遊戲時的產品資料、歷程記錄使用者行為資料和使用者資料。第二，遊戲使用模式必須合理且精確地計算。第三，遊戲公司需要追蹤整體的向上銷售及交叉銷售設定檔至遊戲內購買成功等情況，以測量其方法的效率並調整未來的行銷活動。

## 解決方案概觀

這個簡單的使用案例可以當做您如何使用 Azure Data Factory 擷取、準備、轉換、分析和發佈資料的範例。

![端對端工作流程](./media/data-factory-customer-profiling-usecase/EndToEndWorkflow.png) 上圖說明部署資料管線之後，它們會如何出現在 Azure 傳統入口網站 UI 中。

1.	**PartitionGameLogsPipeline** 從 Blob 儲存體讀取原始遊戲事件，並根據年、月和日建立分割區。
2.	**EnrichGameLogsPipeline** 聯結分割的遊戲事件與地區代碼參考資料，並將 IP 位址對應到相對應的地理位置來充實資料。
3.	**AnalyzeMarketingCampaignPipeline** 管線運用已充實的資料並加上廣告資料來處理，以建立包含行銷活動成效的最終輸出。

在此範例使用案例中，Azure Data Factory 可用來協調數個活動，包括複製輸入資料、利用 HDInsight 活動 (Hive 和 Pig 轉換) 轉換和處理資料，以及將最後的資料輸出至 Azure SQL Database。您也可以視覺化資料管線的網路、管理它們，並從 UI 監視其狀態。

## 優點

藉由最佳化其使用者設定檔分析並將其與企業目標對齊，遊戲公司可以快速收集使用模式，並且為其所有不同的遊戲產品分析其行銷活動的效益。

<!---HONumber=AcomDC_0128_2016-->