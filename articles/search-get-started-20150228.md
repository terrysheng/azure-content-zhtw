<properties 
	pageTitle="在 .NET 中建立第一個 Azure 搜尋服務應用程式" 
	description="從 Azure 搜尋服務 .NET SDK 使用 .NET 用戶端程式庫建置方案的教學課程。" 
	services="search" 
	documentationCenter="" 
	authors="HeidiSteen" 
	manager="mblythe" 
	editor=""/>

<tags 
	ms.service="search" 
	ms.devlang="rest-api" 
	ms.workload="search" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.date="03/05/2015" 
	ms.author="heidist"/>

#在 .NET 中建立第一個 Azure 搜尋服務應用程式#

本教學課程會在 Visual Studio 2013 或更高版本中建置自訂 Web 搜尋應用程式，並使用 Azure 搜尋服務提供搜尋體驗。本教學課程使用 [Azure 搜尋服務 .NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx) 為此範例中所用的物件和作業建置類別。

您可以在[使用 USGS 資料的 Azure 搜尋服務示範](https://azsearchdemos.codeplex.com/SourceControl/latest)中下載 codeplex 的範例，即可依循本教學課程的步驟執行。此範例應用程式使用的[美國地理服務中心 (USGS)](http://geonames.usgs.gov/domestic/download_data.htm) 資料，已依據華盛頓州進行篩選。我們將使用此資料，並分別依據地標建築物 (例如醫院和學校) 及地理特徵 (例如河流、湖泊和山峰) 等相關資料，建置搜尋應用程式。

若要執行此範例，必須要有 Azure Search 服務，您可以在 [Azure 入口網站](https://portal.azure.com)中註冊此服務。

如果您需要佈建及驗證服務可用性的協助，可以從[在入口網站建立服務](../search-create-service-portal/)開始。此外，本文還說明了如何尋找在每個教學課程和含有 Azure 搜尋服務的方案中使用的服務名稱和系統管理金鑰。

> [AZURE.TIP]建議先更新 NuGet 封裝，再建置任何專案，以避免建置發生錯誤。以滑鼠右鍵按一下方案，然後選擇 [管理 NuGet 封裝]****。更新在此方案中使用的所有封裝。

##建置索引##

1. 從 [Azure 入口網站](https://portal.azure.com)複製服務名稱和系統管理金鑰，然後貼入 **DataIndexer** | **App.config**。
1. 以滑鼠右鍵按一下 **DataIndexer** 專案，將其設為起始專案。
1. 建置並執行專案。

您應該會看見含有這些訊息的主控台視窗。

![][1]

在入口網站中，您應該會看見新的 features 索引以及 xx 和 xx 的資料。入口網站要花數分鐘才能抓到資料，因此請在數分鐘後重新整理畫面以查看結果。

![][2]

##建置應用程式##


1. 從 [Azure 入口網站](https://portal.azure.com)複製服務名稱和系統管理金鑰，然後貼入 **SimpleSearchMVCApp** | **Web.config**。
1. 以滑鼠右鍵按一下 **SimpleSearchMVCApp** 專案，將其設為起始專案。
1. 建置並執行專案。

您應該會在預設瀏覽器中看到一個提供搜尋方塊的網頁，您可以由此搜尋方塊存取儲存在 Azure Search 服務之索引中的 USGS 資料。

![][3]

##搜尋 USGS 資料##

USGS 資料集包含與華盛頓州相關的記錄。如果您在空白的搜尋方塊中按一下 [搜尋]****，預設會出現前 50 個項目。

輸入搜尋字詞，讓搜尋引擎運作一下。試著輸入地區名稱。"Snoqualmie" 是華盛頓州的一座城市。同時也是河流、景觀瀑布、隘口和州立公園的名稱。

![][4]

此外，您也可以試著使用這些字詞：

- Seattle
- Rainier
- Seattle and Rainier
- Seattle +Rainier -Mount (會取得包含 Rainier 街的地標或 Rainier 俱樂部的搜尋結果，這些結果均在 Seattle 市的限制內)。

##探索程式碼##

若要了解 .NET SDK 的基本知識，可以參閱[如何以 .NET 使用 Azure 搜尋服務](../search-howto-dotnet-sdk/)，其中說明用戶端程式庫中最常使用的類別。

本節的其餘部分涵蓋各專案的一些重點。我們會依情況向您提供一些使用更多進階功能的替代方法。

**DataIndexer 專案**

為方便起見，會使用一個從[美國地理服務中心(USGS) 網站](http://geonames.usgs.gov/domestic/download_data.htm)資料產生的文字檔案，將資料內嵌在方案中

內嵌資料的替代方法包含 [DocumentDB 的索引子](documentdb-search-indexer.md)或 [Azure SQL Database 的索引子](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)。索引子會將資料提取至您的 Azure 搜尋服務索引，這麼做可以簡化您必須撰寫及維護的程式碼。

您也可以從內部部署的 SQL Server 資料庫載入資料。[此教學課程](http://azure.microsoft.com/blog/2014/11/10/how-to-sync-sql-server-data-with-azure-search/)會教您如何操作。

**DataIndexer** 程式包含搜尋和篩選資料的 **SearchDocuments** 方法。此方法為一種驗證步驟，支援將狀態訊息輸出至主控台視窗，也就是說會顯示搜尋結果和篩選行為。在那些為建立並載入索引而撰寫的程式碼中，通常不會用到這類方法。

**SimpleSearchMVCApp 專案**

MVC 專案使用檢視和控制器將輸入和輸出傳送至展示層。**Index.cshtml** 提供用於呈現搜尋結果的 HTML。這目前還只是一種簡單資料表，可整理資料集的資料。但對原型設計和測試很實用，您可以輕鬆改進展示結果。如需如何在頁面批次處理結果及放上計數的秘訣，請參閱 [Azure 搜尋服務中的頁面結果和分頁](search-pagination-page-layout.md)。

**FeatureSearch.cs** 檔案中定義了與 Azure 搜尋服務的連接還有查詢要求的執行。

最後請注意，如果您對 .NET SDK 的價值和簡單性仍有懷疑，請將此範例的原始程式檔和以 REST API 為基礎的此檔案：[Azure 搜尋服務 Adventure Works 示範](https://azuresearchadventureworksdemo.codeplex.com/)加以比較。本教學課程所述的 .NET SDK 版本較為簡化，程式碼的行數也較少。

##後續步驟##

這是第一個以 USGS 資料集為基礎的 Azure 搜尋服務教學課程。我們會漸漸擴充本教學課程，並建立新課程，以示範您可能會想用在自訂方案中的搜尋功能。

如果您已有一些 Azure 搜尋服務的背景知識，可以利用此範例做為試用建議工具 (預先輸入或自動完成查詢)、篩選及多面向導覽的跳板。您也可以新增計數和批次處理文件，讓使用者可以逐頁查看結果，藉此改進搜尋結果頁面。

不熟悉 Azure 搜尋服務嗎？ 建議您嘗試學習其他教學課程，深入了解您還可以建立哪些東西。請瀏覽我們的[文件頁面](http://azure.microsoft.com/documentation/services/search/)以尋找更多資源。您也可以查看我們[影片和教學課程清單](https://msdn.microsoft.com/library/azure/dn798933.aspx)中的連結，以存取更多資訊。

<!--Image references-->
[1]: ./media/search-get-started-20150228/consolemessages.png
[2]: ./media/search-get-started-20150228/portalindexstatus.png
[3]: ./media/search-get-started-20150228/usgssearchbox.png
[4]: ./media/search-get-started-20150228/snoqualmie.png
<!--HONumber=54-->