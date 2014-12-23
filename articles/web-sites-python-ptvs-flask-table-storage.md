<properties linkid="web-sites-python-ptvs-flask-table-storage" title="Flask and Azure Table Storage on Azure with Python Tools 2.1 for Visual Studio" pageTitle="Azure 上採用 Python Tools 2.1 for Visual Studio 的 Flask 和 Azure 資料表儲存體" description="Learn how to use the Python Tools for Visual Studio to create a Flask application that stores data in Azure Table Storage and can be deployed to a web site." metaKeywords="" services="" solutions="" documentationCenter="Python" authors="huvalo" videoId="" scriptId="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="10/10/2014" ms.author="huvalo" />




# Azure 上採用 Python Tools 2.1 for Visual Studio 的 Flask 和 Azure 資料表儲存體 

在本教學課程中，我們將使用其中一個 PTVS 範例範本建立簡單的民調應用程式。

民調應用程式會為其儲存機制定義一個抽象概念，讓您輕鬆地切換不同類型的儲存機制 (記憶體內部、Azure 資料表儲存體、MongoDB)。

我們將學習如何建立 Azure 儲存體帳戶、如何設定應用程式以使用 Azure 資料表儲存體，以及如何將應用程式發佈至 Azure 網站。

如需更多相關文章 (說明透過使用 Bottle、Flask 和 Django 架構的 PTVS、透過 MongoDB、Azure 資料表儲存體、MySQL 和 SQL Database 服務進行 Azure 網站開發)，請參閱 [Python 開發人員中心][]。雖然本文著重於 Azure 網站，但其開發步驟類似於開發 [Azure 雲端服務][]。

+ [必要條件](#prerequisites)
+ [建立專案](#create-the-project)
+ [建立 Azure 儲存體帳戶](#create-an-azure-storage-account)
+ [設定專案](#configure-the-project)
+ [探索 Azure 資料表儲存體](#explore-the-azure-table-storage)
+ [發佈至 Azure 網站](#publish-to-an-azure-website)
+ [設定 Azure 網站](#configure-the-azure-website)
+ [後續步驟](#next-steps)

##<a name="prerequisites"></a>必要條件

 - Visual Studio 2012 或 2013
 - [Python Tools 2.1 for Visual Studio][]
 - [Python Tools 2.1 for Visual Studio 範例 VSIX][]
 - [Azure SDK Tools for VS 2013][] 或 [Azure SDK Tools for VS 2012][]
 - [Python 2.7 32 位元][]或 [Python 3.4 32 位元][]

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

##<a name="create-the-project"></a>建立專案

在本節中，我們將使用範例範本建立 Visual Studio 專案。我們將建立虛擬環境並安裝必要的套件。然後我們將使用預設的記憶體內部儲存機制在本機執行應用程式。

1.  在 Visual Studio 中，選取 [**檔案**]、[**新增專案**]。

在 [1.  **Python**]、[**範例**] 之下可以取得 PTVS 範例 VSIX 中的專案範本。選取 [**Polls Flask Web Project**]，然後按一下 [確定] 以建立專案。

  	![New Project Dialog](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskNewProject.png)

1.  系統會提示您安裝外部套件。選取 [**Install into a virtual environment**]。

  	![External Packages Dialog](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskExternalPackages.png)

1.  選取 [**Python 2.7**] 或 [**Python 3.4**] 作為基底解譯器。

  	![Add Virtual Environment Dialog](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonAddVirtualEnv.png)

1.  按 <kbd>F5</kbd>，確認應用程式可運作。根據預設，應用程式會使用記憶體內部儲存機制，而不需要進行任何設定。所有資料會在 Web 伺服器停止時遺失。

1.  按一下 [**Create Sample Polls**]，然後按一下一項民調並投票。

  	![Web Browser](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskInMemoryBrowser.png)

##<a name="create-an-azure-storage-account"></a>建立 Azure 儲存體帳戶

若要使用儲存體作業，您需要 Azure 儲存體帳戶。您可以依照下列步驟來建立儲存體帳戶。

1.  登入 [Azure 管理入口網站][]。

1.  在導覽窗格的底端，按一下 [**新增**]。

  	![New Button](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonAzurePlusNew.png)

1.  按一下 [**資料服務**]、[**儲存體**]，然後按一下 [**快速建立**]。

  	![Quick Create](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonAzureStorageCreate.png)

1.  在 URL 中，為儲存體帳戶輸入要在 URI 中使用的子網域名稱。此項目可以包含 3 至 24 個小寫字母與數字。此值會成為 URI 內用來將訂閱的 Blob、「佇列」或「表格」資源定址的主機名稱。

1.  選擇要將儲存體放置於的「區域/同質群組」。如果您會從 Azure 應用程式使用儲存體，請選取您會在其中部署應用程式的相同區域。

1.  (選用) 您可以啟用地理區域複寫。透過地理區域複寫，Azure 儲存體現在可將您的資料持久保留在兩個位置。在兩個位置中，Azure 儲存體會持續維護多個狀況良好的資料複本。

1.  按一下 [**建立儲存體帳戶**]。

##<a name="configure-the-project"></a>設定專案

在本節中，我們將設定應用程式使用剛才建立的儲存體帳戶。我們將了解如何從 Azure 入口網站取得連線設定。然後我們會在本機執行應用程式。

1.  在 [ Azure 管理入口網站][]中，按一下在上一節中建立的儲存體帳戶。

1.  按一下 [**管理存取金鑰**]。

  	![Manage Access Keys Dialog](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonAzureTableStorageManageKeys.png)

1.  在 Visual Studio 中，在 [方案總管] 中您的專案節點上按一下滑鼠右鍵，然後選取 [**屬性**]。按一下 [**偵錯**] 索引標籤。

  	![Project Debug Settings](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskAzureTableStorageProjectDebugSettings.png)

1.  在 [**Debug Server Command**]、[**環境**] 中設定應用程式所需的環境變數值。

        REPOSITORY_NAME=azuretablestorage
        STORAGE_NAME=<storage account name>
        STORAGE_KEY=<primary access key>

    這會在您 [**開始偵錯**] 時設定環境變數。如果您要在 [**啟動但不偵錯**] 時設定變數，請同時在 [**Run Server Command**] 底下設定相同的值。

    或者，您可以使用 Windows 控制台定義環境變數。如果想要避免將認證儲存在原始碼 / 專案檔案中，這是比較好的選項。請注意，您必須重新啟動 Visual Studio，新的環境值才可用於應用程式。

1.  實作 Azure 資料表儲存體儲存機制的程式碼位於 **models/azuretablestorage.py**。有關如何使用 Python 資料表服務的詳細資訊，請參閱[說明文件]。

1.  使用 <kbd>F5</kbd> 執行應用程式。使用 [**Create Sample Polls**] 建立的民調以及投票所提交的資料將會在 Azure 資料表儲存體中序列化。

1.  瀏覽至 [**關於**] 頁面，確認應用程式是使用 **Azure 資料表儲存體**儲存機制。

  	![Web Browser](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskAzureTableStorageAbout.png)

##<a name="explore-the-azure-table-storage"></a>探索 Azure 資料表儲存體

使用 Visual Studio 中的 [伺服器總管] 即可輕易檢視和編輯儲存體資料表。在本節中，我們將使用 [伺服器總管] 來檢視民調應用程式資料表的內容。

> [WACOM.NOTE]這需要安裝 Microsoft Azure Tools (可在 [Azure SDK for .NET][] 中取得)。

1.  開啟 [**伺服器總管**]。依序展開 [**Azure**]、[**儲存體**]、您的儲存體帳戶和 [**資料表**]。

  	![Server Explorer](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonServerExplorer.png)

1.  連按兩下 [**民調**] 或 [**選擇**] 資料表可在文件視窗中檢視目錄，以及新增/移除/編輯實體。

  	![Table Query Results](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonServerExplorerTable.png)

##<a name="publish-to-an-azure-website"></a>發佈至 Azure 網站

PTVS 提供簡單的方法將 Web 應用程式部署至 Azure 網站。

1.  在 [**方案總管**] 中，以滑鼠右鍵按一下專案節點，然後選取 [**發佈**]。

  	![Publish Web Dialog](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonPublishWebSiteDialog.png)

1.  按一下 [**Microsoft Azure 網站**]。

1.  按一下 [**新增**] 以建立新網站。

1.  選取 [**網站名稱**] 和 [**區域**]，然後按一下 [**建立**]。

  	![Create Site on Microsoft Azure Dialog](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonCreateWebSite.png)

1.  接受所有其他預設值，然後按一下 [**發佈**]。

1.  網頁瀏覽器會自動開啟已發佈的網站。如果您瀏覽至 [關於] 頁面，您會看到它使用 [**記憶體內部**] 儲存機制，而非 [**Azure 資料表儲存體**] 儲存機制。

    這是因為 Azure 網站上未設定環境變數，所以使用在 **settings.py** 中指定的預設值。

##<a name="configure-the-azure-website"></a>設定 Azure 網站

在本節中，我們將設定網站的環境變數。

1.  在 [ Azure 管理入口網站][]中，按一下在上一節中建立的網站。

1.  在頂端功能表中，按一下 [**設定**]。

  	![Top Menu](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonWebSiteTopMenu.png)

1.  向下捲動到 [**應用程式設定**] 區段，並如上一節所述設定 **REPOSITORY\_NAME**、**STORAGE\_NAME** 和 **STORAGE\_KEY** 的值。

  	![App Settings](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonWebSiteConfigureSettingsTableStorage.png)

1. 在底部功能表中，依序按一下 [**儲存**]、[**重新啟動**]，最後按一下 [**瀏覽**]。

  	![Bottom Menu](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonWebSiteConfigureBottomMenu.png)

1.  您應該會看到應用程式如預期般運作 (使用 **Azure 資料表儲存體**儲存機制)。

    恭喜！

  	![Web Browser](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskAzureBrowser.png)

##<a name="next-steps"></a>後續步驟

請遵循下列連結以深入了解 Python Tools for Visual Studio、Flask 和 Azure 資料表儲存體。

- [Python Tools for Visual Studio 說明文件][]
  - [Web 專案][]
  - [雲端服務專案][]
  - [在 Microsoft Azure 上進行遠端偵錯][]
- [Flask 說明文件][]
- [Azure 儲存體][]
- [Azure SDK for Python][]
- [如何從 Python 使用資料表儲存體服務][]


<!--Link references-->
[Python 開發人員中心]: /zh-tw/develop/python/
[Azure 雲端服務]: ../cloud-services-python-ptvs/
[說明文件]: ../storage-python-how-to-use-table-storage/
[如何從 Python 使用資料表儲存體服務]: ../storage-python-how-to-use-table-storage/

<!--External Link references-->
[Azure 管理入口網站]: https://manage.windowsazure.com
[Azure SDK for .NET]: http://azure.microsoft.com/zh-tw/downloads/
[Python Tools 2.1 for Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
[Python Tools 2.1 for Visual Studio 範例 VSIX]: http://go.microsoft.com/fwlink/?LinkId=517189
[Azure SDK Tools for VS 2013]: http://go.microsoft.com/fwlink/?LinkId=323510
[Azure SDK Tools for VS 2012]: http://go.microsoft.com/fwlink/?LinkId=323511
[Python 2.7 (32 位元)]: http://go.microsoft.com/fwlink/?LinkId=517190 
[Python 3.4 (32 位元)]: http://go.microsoft.com/fwlink/?LinkId=517191
[Python Tools for Visual Studio 說明文件]: http://pytools.codeplex.com/documentation
[Flask 說明文件]: http://flask.pocoo.org/
[在 Microsoft Azure 上進行遠端偵錯]: http://pytools.codeplex.com/wikipage?title=Features%20Azure%20Remote%20Debugging
[Web 專案]: http://pytools.codeplex.com/wikipage?title=Features%20Web%20Project
[雲端服務專案]: http://pytools.codeplex.com/wikipage?title=Features%20Cloud%20Project
[Azure 儲存體]: http://azure.microsoft.com/zh-tw/documentation/services/storage/
[Azure SDK for Python]: https://github.com/Azure/azure-sdk-for-python
