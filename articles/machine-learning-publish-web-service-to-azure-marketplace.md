<properties title="Publishing Azure ML Web Services to the Azure Marketplace" pageTitle="Publishing Azure ML Web Services to the Azure Marketplace | Azure" description="Publishing Azure ML Web Services to the Azure Marketplace" metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="garye"></tags>

# 將 Azure ML Web 服務發行至 Azure Marketplace

本文內容：

-   [簡介][簡介]
-   [發行程序概觀][發行程序概觀]
-   [發行至 Azure Marketplace 的指引][發行至 Azure Marketplace 的指引]
-   [機器學習專用選項][機器學習專用選項]

<!--Anchors-->

## 簡介

Azure Marketplace 可讓您發行 Azure Machine Learning Web 服務，作為供外部客戶付費或免費使用的服務。本文將提供該程序的概觀，以及入門使用的指引連結。透過此程序，您將可讓您的 Web 服務成為可供其他開發人員運用在其應用程式中的服務。

## 發行程序概觀

以下是將 Azure ML Web 服務發行至 Azure Marketplace 的步驟：

1.  建立及發行 Azure ML RRS (要求回應服務) 或 BES (批次執行服務) Web 服務。
2.  從 Azure 管理入口網站，將服務部署至生產環境中。
3.  使用已發行之 Web 服務的 URL，發行至 Azure Marketplace。
4.  發行程序概觀：<http://msdn.microsoft.com/zh-tw/library/azure/hh580725.aspx>
5.  您的產品在提交之後必須經過審閱和核准，才可供客戶購買。發行程序可能需要數個工作天。我們正設法盡可能縮短此時程，且將在日後的通訊中提供更新。

## 發行至 Azure Marketplace 的指引

1.  您必須註冊為發行者。如需詳細資訊，請參閱：<http://msdn.microsoft.com/zh-tw/library/azure/hh563872.aspx>
2.  您必須提供產品的相關資訊，包括價格方案。決定您要提供免費還是付費服務。如需詳細資訊，請參閱：<http://msdn.microsoft.com/zh-tw/library/azure/hh563873.aspx>
3.  若要收費，您必須提供付款資訊，例如您的銀行和稅務資訊。如需詳細資訊，請參閱：<http://msdn.microsoft.com/zh-tw/library/azure/hh563873.aspx>

## 機器學習專用選項

1.  建立新產品時，請選取 **[資料服務]**，然後按一下 **[建立新的資料服務]**。

    ![Azure Marketplace][Azure Marketplace]

2.  在 **[資料服務]** 索引標籤中，按一下 **[Web 服務]** 以作為資料來源。

    ![Azure Marketplace][1]

3.  針對 **[服務 URL]**，請使用您 Web 服務的 URL：

    -   在 Azure ML Studio 的左功能表中，按一下 **[Web 服務]**。
    -   按一下您要在 Marketplace 中發行的 Web 服務。
    -   在 **[儀表板]** 頁面上，按一下 RRS 服務的 **[API 說明頁面]**。
    -   複製 OData 端點位址。

	<br />

4.  在 [驗證] 中，選擇 **[標頭]** 作為 **[驗證配置]**。

    -   輸入 "Authorization" 作為 **[標頭名稱]**。
    -   針對 **[標頭值]**：
        -   在 **[儀表板]** 頁面上，為您在 ML Studio 中的 Web 服務複製 **[API 金鑰]**。
        -   在 **[標頭值]** 欄位中輸入 "Bearer" (不含引號)、再加上空格，然後貼上 API 金鑰。
    -   勾選 **[This Service is OData]** 核取方塊。

	<br />

5.  類別：

    -   確定已核取 **[機器學習]**。

  [簡介]: #introduction
  [發行程序概觀]: #overview-of-the-publishing-process
  [發行至 Azure Marketplace 的指引]: #guidelines-for-publishing-to-azure-marketplace
  [機器學習專用選項]: #machine-learning-specific-options
  [Azure Marketplace]: ./media/machine-learning-publish-web-service-to-azure-marketplace/image1.png
  [1]: ./media/machine-learning-publish-web-service-to-azure-marketplace/image2.png
