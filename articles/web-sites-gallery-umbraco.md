<properties title="Create an Umbraco website from the gallery in Microsoft Azure" pageTitle="Create an Umbraco website from the gallery in Microsoft Azure" description="required" metaKeywords="Azure, gallery, Umbraco, web site, website" services="web-sites" solutions="web" documentationCenter="" authors="timamm" manager="paulettm" editor="mollybos" videoId="" scriptId="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="timamm" />

# 在 Microsoft Azure 中從映像庫建立 Umbraco 網站

Umbraco CMS 是個功能齊全的開放原始碼內容管理系統，可用來建立從小型到複雜等各式各樣的應用程式。Azure 網站應用程式映像庫提供由 Microsoft、協力廠商公司及開放原始碼軟體計劃所開發的各種熱門 Web 應用程式。透過套用入門套件或整合自己的設計，映像庫可讓您在幾分鐘內建立 Umbraco CMS 網站。

本文展示了可大幅簡化資源管理的新 Azure 預覽入口網站。新的 Azure 入口網站旨在加快您的軟體交付程序速度，方法是在單一工作區中放置 Microsoft 及其夥伴的跨平台工具、技術和服務。與其使用獨立資源 (如 Azure 網站、Visual Studio 專案或資料庫)，您可以單一資源群組的方式來建立、管理及分析整個應用程式。

在本教學課程中，您將了解：

-   如何使用新的 Azure 預覽入口網站，透過映像庫來建立新網站
-   如何使用 Umbraco CMS 來建置部落格網站

## 在 Azure 入口網站中從映像庫建立網站

1.  登入 [Microsoft Azure 管理入口網站][Microsoft Azure 管理入口網站]。

2.  選擇 [Azure Gallery] 圖示。

    ![Choose Web Gallery][Choose Web Gallery]

3.  在 [映像庫] 中，選取 [Web] 索引標籤，然後選取 [Umbraco CMS]。

    ![Select Umbraco in the Web Gallery][Select Umbraco in the Web Gallery]

4.  若要建立新的 Umbraco CMS 網站，請按一下 [建立]。

    ![Click Create][Click Create]

5.  下一個步驟是設定所有與 Umbraco CMS 相關的資源。在此案例中的資源是網站和 SQL Server 資料庫。首先，選取 [網站] 以進行網站設定，例如網站 [URL]、[虛擬主機方案]、[Web App 設定] 和 [位置]。

    ![Configure resources][Configure resources]

6.  現在開始設定資料庫。選取 [資料庫]，然後選擇 [建立新的資料庫]。本範例將為 Azure 上的資料庫建立 SQL Server。

    ![Create a SQL Server on Azure][Create a SQL Server on Azure]

7.  現在已設定好網站和資料庫，您可以開始部署應用程式，方法是在第一個 [Umbraco CMS] 分頁 (上圖中所示) 的底部上，按一下 [建立]。

    ![Click Create][1]

完成部署後，入口網站中的開始面板會顯示已建立 Umbraco CMS 的資源群組，在此案例中是 **UmbracoCMSgroup**。在 [摘要] 區段中，按一下網站名稱 (在此案例中是 **umbracocmsgroup**) 以查看網站的屬性。另外，在 [摘要] 區段中，您還可以選取資料庫資源，以查看相關資料庫的屬性。

![][0]

## 啟動和設定 Umbraco CMS 網站

1.  在網站的詳細資料分頁上，按一下 [瀏覽] 以瀏覽網站 (在此案例中是 umbracocmsgroup.azurewebsites.net)。

    ![Browse to your site][Browse to your site]

2.  瀏覽網站時，Umbraco CMS 會啟動其安裝程式精靈。輸入要求的資訊，然後按一下 [自訂]。

    ![Install Umbraco wizard][Install Umbraco wizard]

3.  輸入 Umbraco 即將使用的資料庫連線和驗證詳細資料。在資料庫類型中選取 [Microsoft SQL Azure]。您可以從網站的 [網站設定] 區段中取得資料庫的連接字串。

    ![Configure your database][Configure your database]

4.  如果您不熟悉 Umbraco CMS，您可以選取網站入門套件。您也可以按一下 [No thanks, I do not want to install a starter website] 。

    ![Install a starter website][Install a starter website]

5.  Umbraco 安裝程式將會完成設定您的應用程式。在設定您的應用程式之後，您將會被重新導向至 Umbraco CMS 系統管理儀表板。

    ![Umbraco CMS dashboard][Umbraco CMS dashboard]

6.  您現在可以建立您將發行的範例文字頁面。依序選取 [連線]、[溢位] 及 [TextPage]。

    ![Create a text page][Create a text page]

7.  輸入文字頁面的標題和部分內容，如下所示。結束時，請按一下 [儲存並發行]。

    ![Enter a title and some content][Enter a title and some content]

8.  等候發行您的頁面。發行完成時，您將在畫面的右下角看到一則小警示。您現在可以開始瀏覽網站上的新內容。

    ![Published web site page][Published web site page]

這樣就大功告成了！只需要幾分鐘的時間，您已成功使用 Umbraco CMS 建立部落格網站。

## 其他資源

[Umbraco 文件][Umbraco 文件]

[Umbraco 影片教學課程][Umbraco 影片教學課程]

[Microsoft Azure 預覽入口網站概觀][Microsoft Azure 預覽入口網站概觀]

[Microsoft Azure 預覽入口網站文件][Microsoft Azure 預覽入口網站文件]

[Azure 預覽入口網站 (第 9 頻道)][Azure 預覽入口網站 (第 9 頻道)]

[Microsoft Azure 網站文件][Microsoft Azure 網站文件]

<!-- IMAGES -->

  [Microsoft Azure 管理入口網站]: https://portal.azure.com/
  [Choose Web Gallery]: ./media/web-sites-gallery-umbraco/01Startboard.PNG
  [Select Umbraco in the Web Gallery]: ./media/web-sites-gallery-umbraco/02WebGallery.PNG
  [Click Create]: ./media/web-sites-gallery-umbraco/03UmbracoCMS.PNG
  [Configure resources]: ./media/web-sites-gallery-umbraco/04AppSettings.PNG
  [Create a SQL Server on Azure]: ./media/web-sites-gallery-umbraco/05NewServer.PNG
  [1]: ./media/web-sites-gallery-umbraco/06UmbracoCMSGroup.PNG
  [0]: ./media/web-sites-gallery-umbraco/07UmbracoCMSGroupBlade.PNG
  [Browse to your site]: ./media/web-sites-gallery-umbraco/08UmbracoCMSGroupRunning.PNG
  [Install Umbraco wizard]: ./media/web-sites-gallery-umbraco/09InstallUmbraco7.png
  [Configure your database]: ./media/web-sites-gallery-umbraco/10ConfigureYourDatabase.png
  [Install a starter website]: ./media/web-sites-gallery-umbraco/11InstallAStarterWebsite.png
  [Umbraco CMS dashboard]: ./media/web-sites-gallery-umbraco/14FriendlyCMS.PNG
  [Create a text page]: ./media/web-sites-gallery-umbraco/15CreateItemUnderOverflow.PNG
  [Enter a title and some content]: ./media/web-sites-gallery-umbraco/16EnterAName.PNG
  [Published web site page]: ./media/web-sites-gallery-umbraco/17MyPage.PNG
  [Umbraco 文件]: http://our.umbraco.org/documentation
  [Umbraco 影片教學課程]: https://umbraco.com/help-and-support/video-tutorials.aspx
  [Microsoft Azure 預覽入口網站概觀]: http://azure.microsoft.com/zh-tw/overview/preview-portal/
  [Microsoft Azure 預覽入口網站文件]: http://azure.microsoft.com/zh-tw/documentation/preview-portal/
  [Azure 預覽入口網站 (第 9 頻道)]: http://channel9.msdn.com/Blogs/Windows-Azure/Azure-Preview-portal
  [Microsoft Azure 網站文件]: http://azure.microsoft.com/zh-tw/documentation/services/web-sites/
