<properties linkid="web-sites-python-ptvs-bottle-mongodb" title="Bottle and MongoDB on Azure with Python Tools 2.1 for Visual Studio" pageTitle="Azure 上採用 Python Tools 2.1 for Visual Studio 的 Bottle 和 MongoDB" description="Learn how to use the Python Tools for Visual Studio to create a Bottle application that stores data in a MongoDB database instance and can be deployed to a web site." metaKeywords="" services="web-sites" solutions="" documentationCenter="Python" authors="huvalo" videoId="" scriptId="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="10/10/2014" ms.author="huvalo" />




# Azure 上採用 Python Tools 2.1 for Visual Studio 的 Bottle 和 MongoDB

在本教學課程中，我們將使用其中一個 PTVS 範例範本建立簡單的民調應用程式。

民調應用程式會為其儲存機制定義一個抽象概念，讓您輕鬆地切換不同類型的儲存機制 (記憶體內部、Azure 資料表儲存體、MongoDB)。

我們將學習如何在 Azure 上使用其中一項代管的 MongoDB 服務、如何設定應用程式來使用 MongoDB，以及如何將應用程式發佈至 Azure 網站。

如需更多相關文章 (說明透過使用 Bottle、Flask 和 Django 架構的 PTVS、透過 MongoDB、Azure 資料表儲存體、MySQL 和 SQL Database 服務進行 Azure 網站開發)，請參閱 [Python 開發人員中心][]。雖然本文著重於 Azure 網站，但其開發步驟類似於開發 [Azure 雲端服務][]。

+ [必要條件](#prerequisites)
+ [建立專案](#create-the-project)
+ [建立 SQL 資料庫](#create-a-mongodb-database)
+ [設定專案](#configure-the-project)
+ [瀏覽 MongoDB 資料庫](#explore-the-mongodb-database)
+ [發佈至 Azure 網站](#publish-to-an-azure-website)
+ [設定 Azure 網站](#configure-the-azure-website)
+ [後續步驟](#next-steps)

##<a name="prerequisites"></a>必要條件

 - Visual Studio 2012 或 2013
 - [Python Tools 2.1 for Visual Studio][]
 - [Python Tools 2.1 for Visual Studio 範例 VSIX][]
 - [Azure SDK Tools for VS 2013][] 或 [Azure SDK Tools for VS 2012][]
 - [Python 2.7 32 位元][]或 [Python 3.4 32 位元][]
 - [RoboMongo][] (選擇性)

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

##<a name="create-the-project"></a>建立專案

在本節中，我們將使用範例範本建立 Visual Studio 專案。我們將建立虛擬環境並安裝必要的套件。然後我們將使用預設的記憶體內部儲存機制在本機執行應用程式。

1.  在 Visual Studio 中，選取 [**檔案**]、[**新增專案**]。 

在 [1.  **Python**]、[**範例**] 之下可以取得 PTVS 範例 VSIX 中的專案範本。選取 [**Polls Bottle Web Project**]，然後按一下 [確定] 以建立專案。

  	![New Project Dialog](./media/web-sites-python-ptvs-bottle-mongodb/PollsBottleNewProject.png)

1.  系統會提示您安裝外部套件。選取 [**Install into a virtual environment**]。

  	![External Packages Dialog](./media/web-sites-python-ptvs-bottle-mongodb/PollsBottleExternalPackages.png)

1.  選取 [**Python 2.7**] 或 [**Python 3.4**] 作為基底解譯器。

  	![Add Virtual Environment Dialog](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonAddVirtualEnv.png)

1.  按 <kbd>F5</kbd>，確認應用程式可運作。根據預設，應用程式會使用記憶體內部儲存機制，而不需要進行任何設定。所有資料會在 Web 伺服器停止時遺失。

1.  按一下 [**Create Sample Polls**]，然後按一下一項民調並投票。

  	![Web Browser](./media/web-sites-python-ptvs-bottle-mongodb/PollsBottleInMemoryBrowser.png)

##<a name="create-a-mongodb-database"></a>建立 SQL 資料庫

對於資料庫，我們將在 Azure 上建立 MongoLab 主控的資料庫。

或者，您可以為自己建立在 Azure 上執行的虛擬機器，然後自行安裝和管理 MongoDB。

您可以依照下列步驟，透過 MongoLab 建立免費試用版。

1.  登入 [Azure 管理入口網站][]。

1.  在導覽窗格的底端，按一下 [**新增**]。

  	![New Button](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonAzurePlusNew.png)

1.  按一下 [**市集**]，然後按一下 [**MongoLab**]。

  	![Choose Add-on Dialog](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonMongoLabAddon1.png)

1.  在 [名稱] 中輸入要用於資料庫服務的名稱。

1.  選擇要將資料庫服務放置於的「區域/同質群組」。如果您會從 Azure 應用程式使用資料庫，請選取您會在其中部署應用程式的相同區域。

  	![Personalize Add-on Dialog](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonMongoLabAddon2.png)

1.  按一下 [**購買**]。

##<a name="configure-the-project"></a>設定專案

在本節中，我們將設定應用程式使用剛才建立的 MongoDB 資料庫。我們將了解如何從 Azure 入口網站取得連線設定。然後我們會在本機執行應用程式。

1.  在 [Azure 管理入口網站][]中，按一下 [**附加元件**]，然後按一下您稍早建立的 MongoLab 服務。

1.  按一下 [**連接資訊**]。您可以使用複製按鈕，將 **MONGOLAB\_URI** 的值放在剪貼簿上。

  	![Connection Info Dialog](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonMongoLabConnectionInfo.png)

1.  在 Visual Studio 中，在 [方案總管] 中您的專案節點上按一下滑鼠右鍵，然後選取 [**屬性**]。按一下 [**偵錯**] 索引標籤。

  	![Project Debug Settings](./media/web-sites-python-ptvs-bottle-mongodb/PollsBottleMongoDBProjectDebugSettings.png)

1.  在 [**Debug Server Command**]、[**環境**] 中設定應用程式所需的環境變數值。

        REPOSITORY_NAME=mongodb
        MONGODB_HOST=<value of MONGOLAB_URI>
        MONGODB_DATABASE=<database name>

    這會在您 [**開始偵錯**] 時設定環境變數。如果您要在 [**啟動但不偵錯**] 時設定變數，請同時在 [**Run Server Command**] 底下設定相同的值。

    或者，您可以使用 Windows 控制台定義環境變數。如果想要避免將認證儲存在原始碼 / 專案檔案中，這是比較好的選項。請注意，您必須重新啟動 Visual Studio，新的環境值才可用於應用程式。

1.  實作 MongoDB 儲存機制的程式碼位於 **models/mongodb.py**。

1.  使用 <kbd>F5</kbd> 執行應用程式。使用 [**Create Sample Polls**] 建立的民調以及投票所提交的資料將會在 MongoDB 中序列化。

1.  瀏覽至 [**關於**] 頁面，確認應用程式是使用 **MongoDB** 儲存機制。

  	![Web Browser](./media/web-sites-python-ptvs-bottle-mongodb/PollsBottleMongoDBAbout.png)

##<a name="explore-the-mongodb-database"></a>瀏覽 MongoDB 資料庫

您可以使用 [RoboMongo][] 之類的應用程式來查詢並且編輯 MongoDB 資料庫。在本節中，我們將使用 RoboMongo 來檢視民調應用程式資料庫的內容。

1.  建立新的連線。您將需要我們在上一節中擷取的 **MONGOLAB\_URI**。

    請注意 URI 的格式：`mongodb://<name>:<password>@<address>:<port>/<name>`

    此名稱符合您使用 Azure 建立服務時輸入的名稱。它同時用於資料庫名稱和使用者名稱。

1.  在連線頁面中，將 [**名稱**] 設定為任何您要用於連線的名稱。此外，將 [**位址**] 和 [**連接埠**] 欄位設定為 **MONGOLAB\_URI** 中的*地址* 和*連接埠*。

  	![Connection Settings Dialog](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonRobomongoCreateConnection1.png)

1.  在驗證頁面中，將 [**資料庫**] 和 [**使用者名稱**] 設定為 **MONGOLAB\_URI** 中的*名稱*。此外，將 [**密碼**] 設定為 **MONGOLAB\_URI** 中的*密碼*。

  	![Connection Settings Dialog](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonRobomongoCreateConnection2.png)

1.  儲存並連接到資料庫。您現在可以查詢民調集合。

  	![RoboMongo Query Results](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonRobomongoQuery.png)

##<a name="publish-to-an-azure-website"></a>發佈至 Azure 網站

PTVS 提供簡單的方法將 Web 應用程式部署至 Azure 網站。

1.  在 [**方案總管**] 中，以滑鼠右鍵按一下專案節點，然後選取 [**發佈**]。

  	![Publish Web Dialog](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonPublishWebSiteDialog.png)

1.  按一下 [**Microsoft Azure 網站**]。

1.  按一下 [**新增**] 以建立新網站。

1.  選取 [**網站名稱**] 和 [**區域**]，然後按一下 [**建立**]。

  	![Create Site on Microsoft Azure Dialog](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonCreateWebSite.png)

1.  接受所有其他預設值，然後按一下 [**發佈**]。

1.  網頁瀏覽器會自動開啟已發佈的網站。如果您瀏覽至 [關於] 頁面，您會看到它使用 [**記憶體內部**] 儲存機制，而非 [**MongoDB**] 儲存機制。

    這是因為 Azure 網站上未設定環境變數，所以使用在 **settings.py** 中指定的預設值。

##<a name="configure-the-azure-website"></a>設定 Azure 網站

在本節中，我們將設定網站的環境變數。

1.  在 [ Azure 管理入口網站][]中，按一下在上一節中建立的網站。

1.  在頂端功能表中，按一下 [**設定**]。

  	![Top Menu](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonWebSiteTopMenu.png)

1.  向下捲動到 [**應用程式設定**] 區段，並如上一節所述設定 **REPOSITORY\_NAME**、**MONGODB\_HOST** 和 **MONGODB\_DATABASE** 的值。

  	![App Settings](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonWebSiteConfigureSettingsMongoDB.png)

1.  在底部功能表中，依序按一下 [**儲存**]、[**重新啟動**]，最後按一下 [**瀏覽**]。

  	![Bottom Menu](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonWebSiteConfigureBottomMenu.png)

1.  您應該會看到應用程式如預期般運作 (使用 **MongoDB** 儲存機制)。

    恭喜！

  	![Web Browser](./media/web-sites-python-ptvs-bottle-mongodb/PollsBottleAzureBrowser.png)

##<a name="next-steps"></a>後續步驟

請遵循下列連結以深入了解 Python Tools for Visual Studio、Bottle 和 MongoDB。

- [Python Tools for Visual Studio 說明文件][]
  - [Web 專案][]
  - [雲端服務專案][]
  - [在 Microsoft Azure 上進行遠端偵錯][]
- [Bottle 說明文件][]
- [MongoDB][]
- [PyMongo 說明文件][]
- [PyMongo][]


<!--Link references-->
[Python 開發人員中心]: /zh-tw/develop/python/
[Azure 雲端服務]: ../cloud-services-python-ptvs/

<!--External Link references-->
[Azure 管理入口網站]: https://manage.windowsazure.com
[RoboMongo]: http://robomongo.org/
[Python Tools 2.1 for Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
[Python Tools 2.1 for Visual Studio 範例 VSIX]: http://go.microsoft.com/fwlink/?LinkId=517189
[Azure SDK Tools for VS 2013]: http://go.microsoft.com/fwlink/?LinkId=323510
[Azure SDK Tools for VS 2012]: http://go.microsoft.com/fwlink/?LinkId=323511
[Python 2.7 (32 位元)]: http://go.microsoft.com/fwlink/?LinkId=517190 
[Python 3.4 (32 位元)]: http://go.microsoft.com/fwlink/?LinkId=517191
[Python Tools for Visual Studio 說明文件]: http://pytools.codeplex.com/documentation
[Bottle 說明文件]: http://bottlepy.org/docs/dev/index.html
[MongoDB]: http://www.mongodb.org/
[PyMongo 說明文件]: http://api.mongodb.org/python/current/
[PyMongo]: https://github.com/mongodb/mongo-python-driver
[在 Microsoft Azure 上進行遠端偵錯]: http://pytools.codeplex.com/wikipage?title=Features%20Azure%20Remote%20Debugging
[Web 專案]: http://pytools.codeplex.com/wikipage?title=Features%20Web%20Project
[雲端服務專案]: http://pytools.codeplex.com/wikipage?title=Features%20Cloud%20Project
