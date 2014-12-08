<properties title="Publishing Azure ML Web Services to the Azure Marketplace" pageTitle="將 Azure ML Web 服務發佈至 Azure Marketplace | Azure" description="Publishing Azure ML Web Services to the Azure Marketplace" metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" manager="paulettm" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/03/2014" ms.author="garye" />

# 將 Azure ML Web 服務發行至 Azure Marketplace 

本文內容：

- [簡介]
- [發行程序概觀]
- [發行至 Azure Marketplace 的指引]
- [機器學習專用選項] 

<!--Anchors-->
[簡介]: #introduction
[發佈程序概觀]: #overview-of-the-publishing-process
[發佈至 Azure Marketplace 的指引]: #guidelines-for-publishing-to-azure-marketplace
[機器學習專用選項]: #machine-learning-specific-options 

## 簡介

Azure Marketplace 可讓您發佈 Azure Machine Learning Web 服務，作為供外部客戶付費或免費使用的服務。本文將提供該程序的概觀，以及入門使用的指引連結。透過此程序，您將可讓您的 Web 服務成為可供其他開發人員運用在其應用程式中的服務。

## 發行程序概觀 

以下是將 Azure ML Web 服務發行至 Azure Marketplace 的步驟：

1.	建立及發佈 Azure ML RRS (要求-回應服務) Web 服務。
2.	從 Azure 管理入口網站，將服務部署至生產環境中。
3.	使用已發行之 Web 服務的 URL，發行至 Azure Marketplace。
4.	發佈程序概觀：http://msdn.microsoft.com/zh-tw/library/azure/hh580725.aspx 
5.	您的產品在提交之後必須經過審閱和核准，才可供客戶購買。發行程序可能需要數個工作天。我們正設法盡可能縮短此時程，且將在日後的通訊中提供更新。

## 發行至 Azure Marketplace 的指引

1.	您必須註冊為發佈者。如需詳細資訊，請參閱：<http://msdn.microsoft.com/zh-tw/library/azure/hh563872.aspx>
2.	您必須提供產品的相關資訊，包括價格方案。決定您要提供免費還是付費服務。如需詳細資訊，請參閱：<http://msdn.microsoft.com/zh-tw/library/azure/hh563873.aspx> 
3.	若要收費，您必須提供付款資訊，例如您的銀行和稅務資訊。如需詳細資訊，請參閱：<http://msdn.microsoft.com/zh-tw/library/azure/hh563873.aspx>

## 機器學習專用選項


1.	建立新產品時，請選取 [**資料服務**]，然後按一下 [**建立新的資料服務**]。 
 
	![Azure Marketplace][image1]

	<br />

2. 在 [**資料服務**] 索引標籤中，按一下 [**Web 服務**] 以作為資料來源。

	![Azure Marketplace][image2]

3.	從 Azure 管理入口網站中取得 Web 服務 URL 和 API 金鑰：
	1.	在不同的瀏覽器視窗或索引標籤中，登入 Azure 管理入口網站 ([https://manage.windowsazure.com](https://manage.windowsazure.com)) 
	2.	選取左側功能表中的 [**機器學習**]
	3.	按一下 [**Web 服務**]，然後按一下您要發佈的 web 服務
	4.	將 [**API 金鑰**] 複製到暫存位置 (例如 [記事本])
	5.	按一下要求/回應服務類型的 [**API 說明頁面**]
	6.	將 [**OData 端點位址**] 複製到暫存位置

	<br />

3.	在 [Marketplace 資料服務] 設定對話方塊中，將 OData 端點位址貼入 [**服務 URL**]。

	<br />

4. 在 [驗證] 中，選擇 [**標頭**] 作為 [**驗證配置**]。

	- 輸入 "Authorization" 作為 [**標頭名稱**]
	- 在 [**標頭值**] 中輸入 "Bearer" (不含引號)、再加上空格，然後貼上 API 金鑰
	- 勾選 [**This Service is OData**] 核取方塊
	- 按一下 [**測試連線**] 以測試連線

	<br />

5.	在目錄下：
	- 請確定已勾選 [**機器學習**]



[image1]:./media/machine-learning-publish-web-service-to-azure-marketplace/image1.png
[image2]:./media/machine-learning-publish-web-service-to-azure-marketplace/image2.png
