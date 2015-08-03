<properties 
	pageTitle="Microsoft Azure 上的應用程式架構" 
	description="涵蓋常見設計模式的架構概觀。" 
	services="" 
	documentationCenter="" 
	authors="Rboucher" 
	manager="jwhit" 
	editor="mattshel"/>

<tags 
	ms.service="multiple" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/06/2015" 
	ms.author="robb"/>

#Microsoft Azure 上的應用程式架構
可用於建置使用 Microsoft Azure 應用程式的資源。

##Azure 架構設計模式
Microsoft 發行了一系列的架構設計模式，可協助您撰寫自己的自訂設計。該模式旨在做為簡明扼要的架構指南，可井然有序地組合在一起，提供如何以最佳方式運用 Microsoft Azure 平台解決您的組織商務需求之指導方針。


[概觀](../azure-architectures-cpif-overview/) - [混合式網路功能](../azure-architectures-cpif-infrastructure-hybrid-networking/) - [異地批次處理](../azure-architectures-cpif-foundation-offsite-batch-processing-tier/) - [多站台資料層](../azure-architectures-cpif-foundation-multi-site-data-tier/) - [全域負載平衡的 Web 層](../azure-architectures-cpif-foundation-global-load-balanced-web-tier/) - [Azure 搜尋層](../azure-architectures-cpif-foundation-azure-search-tier/)
 
每個模式包含
 
- 服務描述
- 利用模式所需的 Azure 服務清單
- 架構圖表
- 架構相依性
- 可能會影響模式的設計限制或考量
- 介面和端點
- 反向模式
- 重要的高階架構考量包括可用性和復原、使用之服務的複合 SLA、規模和效能，以及成本和作業考量。

##Microsoft 架構藍圖

Microsoft 發佈一組高階架構藍圖，示範如何建置含有 Microsoft 產品和 Microsoft Azure 服務的特定類型的系統。每個藍圖皆包含一個可供您下載並修改的 2D Visio 型檔案、一個更生動的 3D PDF 藍圖介紹檔案，以及一段 3D 版本的逐步解說影片。請參閱 [Microsoft 架構藍圖](http://msdn.microsoft.com/dn630664)。

2D 藍圖圖表使用下面所提及的雲端和企業符號。

3D 藍圖 PDF 建立於非 Microsoft 工具中，但 Visio 範本正在開發中。請參閱 [這裏的範本 BETA 訓練影片](http://aka.ms/3dBlueprintTemplate)。若要取得 Visio 3d 藍圖範本 Beta，請將電子郵件寄到 [CnESymbols@microsoft.com](mailto:CnESymbols@microsoft.com)。

![Microsoft 架構藍圖 3D 圖表](./media/architecture-overview/BluePrintThumb.jpg)

##雲端和企業符號/圖示集

[下載雲端和企業符號/圖示集](http://aka.ms/CnESymbols)，建立可說明 Azure、Windows Server、SQL Server 和其他 Microsoft 產品的技術資料。您可以將它們用在架構圖表、訓練教材、簡報、資料工作表、資訊圖、白皮書，甚至是協力廠商書籍 (如果書籍訓練人員使用 Microsoft 產品)。符號都為 Visio 和 PNG 格式。包含如何在 PowerPoint 中使用 PNG 的指示。

符號集每季出貨，並隨著新服務的發行進行更新。如果您想要預覽最新版本 (可能包括其他 Azure 服務)，請將電子郵件寄至 [CnESymbols@microsoft.com](mailto:CnESymbols@microsoft.com)。

我們想要知道您的想法，因此下載中有些關於如何提供意見反應的指示。如果您已經使用符號，請填寫 5 個簡短的[問題調查](http://aka.ms/azuresymbolssurveyv2)，或將電子郵件寄至上面的地址，讓我們知道這些符號是否有用，以及您如何使用它們。

[Microsoft Office Visio 樣板](http://www.microsoft.com/zh-tw/download/details.aspx?id=35772)提供其他符號，雖然它們不是最適用於架構圖表，但是 CnE 集是。

![雲端和企業符號/圖示集](./media/architecture-overview/CnESymbols.png)

##設計模式
Microsoft Patterns and Practices 已出版[雲端設計模式](http://msdn.microsoft.com/library/dn568099.aspx)一書，在 MSDN 同時可供瀏覽和下載 PDF。另外，也有列出所有模式的大型海報。

![Patterns and Practices 雲端模式海報](./media/architecture-overview/PnPPatternPosterThumb.jpg)

##架構資訊圖
Microsoft 出版了數個與架構相關的海報/資訊圖。其中包括[建置真實世界的雲端應用程式](http://azure.microsoft.com/documentation/infographics/building-real-world-cloud-apps/)和[使用雲端服務調整](http://azure.microsoft.com/documentation/infographics/cloud-services/)。

![Azure 架構資訊圖](./media/architecture-overview/AzureArchInfographicThumb.jpg)

<!---HONumber=July15_HO4-->