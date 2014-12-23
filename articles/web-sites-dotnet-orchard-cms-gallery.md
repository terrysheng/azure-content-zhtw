<properties urlDisplayName="Website from Gallery" pageTitle="從 Azure 的組件庫中建立 Orchard CMS 網站 " metaKeywords="Azure 建置網站, 管理網站 Azure" description="A tutorial that teaches you how to create a new website in Azure. Also learn how to launch and manage your site using the Management Portal." metaCanonical="" services="web-sites" documentationCenter=".NET" title="Create an Orchard CMS website from the gallery in Azure" authors="tomfitz" solutions="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="10/21/2014" ms.author="tomfitz" />

# 從 Azure 中的組件庫建立 Orchard CMS 網站

組件庫提供由 Microsoft、協力廠商公司及開放原始碼軟體計劃所開發的各種熱門 Web 應用程式。除了用來連線到 [Azure 管理入口網站](http://manage.windowsazure.com)的瀏覽器以外，從組件庫建立的 Web 應用程式不需要安裝任何軟體。如需組件庫中 Web 應用程式的詳細資訊，請參閱 [Windows Web 應用程式組件庫](http://www.microsoft.com/web/gallery/categories.aspx)。

在本教學課程中，您將了解：

- 如何從組件庫建立新網站

- 如何從管理入口網站啟動和管理您的網站
 
您將建立一個使用預設範本的 Orchard CMS 網站。[Orchard](http://www.orchardproject.net/) 是免費、開放原始碼、.NET 架構的 CMS 應用程式，可讓您建立內容導向的自訂網站。Orchard CMS 包含一個擴充性架構，可讓您[下載其他模組和佈景主題](http://gallery.orchardproject.net/)來自訂網站。下圖顯示您將建立的 Orchard CMS 網站。

![Orchard blog][13]

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

<h2>從組件庫建立 Orchard 網站</h2>

1. 登入 [Azure 管理入口網站](http://manage.windowsazure.com)。

2. 按一下入口網站左下方的 [**新增**] 圖示。
	
	![Create New][1]

3. 按一下 [**網站**] 圖示，然後按一下 [**從組件庫**]。
	
	![Create From Gallery][2]

4. 在清單中找出並按一下 [**Orchard CMS**] 圖示，然後按一下箭頭繼續。
	
	![Orchard from list][3]

5. 在 [**設定您的應用程式**] 頁面上，輸入或選取所有欄位的值：
	
- 輸入您選擇的 URL 名稱。	
- 選取最接近使用者的區域 (這可確保最佳效能)。

	![configure your app][4]

6. 按一下方塊右下角的勾選記號，以開始部署新的 Orchard CMS 網站。

Azure 將開始執行建立和部署作業。在建立和部署網站的同時，[網站管理入口網站] 底部會顯示這些操作的狀態。執行所有作業之後，將出現訊息指出已建立網站。

<h2>啟動和管理 Orchard 網站</h2>

1. 在 [**網站**] 頁面上按一下新網站的名稱，然後按一下入口網站底部的 [**瀏覽**]，以開啟網站的歡迎使用頁面。

	![launch dashboard][5]

	![browse button][12]

2. 輸入 Orchard 所需的組態資訊，然後按一下 [**完成設定**]，以完成設定並開啟網站的首頁。

	![login to Orchard][7]

您將會有類似以下螢幕擷取畫面的新 Orchard 網站。  

	![your Orchard site][13]

3. 若要深入了解 Orchard 和設定您的新網站，請參閱 [Orchard 文件](http://docs.orchardproject.net/) 中的詳細資料。

<h2>後續步驟</h2>
* [使用 Microsoft WebMatrix 來開發和部署網站](/zh-tw/develop/net/tutorials/website-with-webmatrix/) -- 了解如何在 WebMatrix 中編輯 Azure 網站。 
* [將使用成員資格、OAuth 和 SQL 資料庫的安全 ASP.NET MVC 應用程式部署至 Azure 網站](/zh-tw/develop/net/tutorials/web-site-with-sql-database/)-- 了解如何從 Visual Studio 建立新的網站。

[1]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-01.png
[2]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-02.png
[3]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-03.png
[4]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-04.png
[5]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-05.png
[7]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-07.png
[12]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-12.png
[13]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-08.png


