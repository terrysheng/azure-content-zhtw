<properties linkid="web-sites-python-ptvs-django-mysql" title="Django and MySQL on Azure with Python Tools 2.1 for Visual Studio" pageTitle="Azure 上採用 Python Tools 2.1 for Visual Studio 的 Django 和 MySQL" description="Learn how to use the Python Tools for Visual Studio to create a Django application that stores data in a MySQL database instance and can be deployed to a web site." metaKeywords="" services="" solutions="" documentationCenter="Python" authors="huvalo" videoId="" scriptId="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="10/10/2014" ms.author="huvalo" />




# Azure 上採用 Python Tools 2.1 for Visual Studio 的 Django 和 MySQL 

在本教學課程中，我們將使用其中一個 PTVS 範例範本建立簡單的民調應用程式。

我們將學習如何如何 Azure 上代管的 MySQL 服務、如何設定應用程式來使用 MySQL，以及如何將應用程式發佈至 Azure 網站。

如需更多相關文章 (說明透過使用 Bottle、Flask 和 Django 架構的 PTVS、透過 MongoDB、Azure 資料表儲存體、MySQL 和 SQL Database 服務進行 Azure 網站開發)，請參閱 [Python 開發人員中心][]。雖然本文著重於 Azure 網站，但其開發步驟類似於開發 [Azure 雲端服務][]。

+ [必要條件](#prerequisites)
+ [建立專案](#create-the-project)
+ [建立 MySQL Database](#create-a-mysql-database)
+ [設定專案](#configure-the-project)
+ [發佈至 Azure 網站](#publish-to-an-azure-website)
+ [後續步驟](#next-steps)

##<a name="prerequisites"></a>必要條件

 - Visual Studio 2012 或 2013
 - [Python Tools 2.1 for Visual Studio][]
 - [Python Tools 2.1 for Visual Studio 範例 VSIX][]
 - [Azure SDK Tools for VS 2013][] 或 [Azure SDK Tools for VS 2012][]
 - [Python 2.7 (32 位元)][]

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

##<a name="create-the-project"></a>建立專案

在本節中，我們將使用範例範本建立 Visual Studio 專案。我們將建立虛擬環境並安裝必要的套件。我們將使用 sqlite 建立本機資料庫。然後我們會在本機執行應用程式。

1.  在 Visual Studio 中，選取 [**檔案**]、[**新增專案**]。

在 [1.  **Python**]、[**範例**] 之下可以取得 PTVS 範例 VSIX 中的專案範本。選取 [**Polls Django Web Project**]，然後按一下 [確定] 以建立專案。

  	![New Project Dialog](./media/web-sites-python-ptvs-django-mysql/PollsDjangoNewProject.png)

1.  系統會提示您安裝外部套件。選取 [**Install into a virtual environment**]。

  	![External Packages Dialog](./media/web-sites-python-ptvs-django-mysql/PollsDjangoExternalPackages.png)

1.  選取 **Python 2.7** 作為基底解譯器。

  	![Add Virtual Environment Dialog](./media/web-sites-python-ptvs-django-mysql/PollsCommonAddVirtualEnv.png)

1.  以滑鼠右鍵按一下專案節點，然後選取 [**Python**]、[**Django Sync DB**]。

  	![Django Sync DB Command](./media/web-sites-python-ptvs-django-mysql/PollsDjangoSyncDB.png)

1.  這會開啟 Django 管理主控台。遵循提示以建立使用者。

    這會在專案資料夾中建立 sqlite 資料庫。

  	![Django Management Console Window](./media/web-sites-python-ptvs-django-mysql/PollsDjangoConsole.png)

1.  按 <kbd>F5</kbd>，確認應用程式可運作。

1.  按一下頂端導覽列中的 [**登入**]。

  	![Web Browser](./media/web-sites-python-ptvs-django-mysql/PollsDjangoCommonBrowserLocalMenu.png)

1.  為您在同步處理資料庫時建立的使用者輸入認證。

  	![Web Browser](./media/web-sites-python-ptvs-django-mysql/PollsDjangoCommonBrowserLocalLogin.png)

1.  按一下 [**Create Sample Polls**]。

  	![Web Browser](./media/web-sites-python-ptvs-django-mysql/PollsDjangoCommonBrowserNoPolls.png)

1.  按一下民調並進行投票。

  	![Web Browser](./media/web-sites-python-ptvs-django-mysql/PollsDjangoSqliteBrowser.png)

##<a name="create-a-mysql-database"></a>建立 MySQL Database

對於資料庫，我們將在 Azure 上建立 ClearDB MySQL 主控的資料庫。

或者，您可以為自己建立在 Azure 中執行的虛擬機器，然後自行安裝和管理 MySQL。

您可以依照下列步驟，透過免費計畫建立資料庫。

1.  登入 [Azure 管理入口網站][]。

1.  在導覽窗格的底端，按一下 [**新增**]。

  	![New Button](./media/web-sites-python-ptvs-django-mysql/PollsCommonAzurePlusNew.png)

1.  按一下 [**市集**]，然後按一下 [**ClearDB MySQL 資料庫**]。

  	![Choose Add-on Dialog](./media/web-sites-python-ptvs-django-mysql/PollsDjangoClearDBAddon1.png)

1.  在 [名稱] 中輸入要用於資料庫服務的名稱。

1.  選擇要將資料庫服務放置於的「區域/同質群組」。如果您會從 Azure 應用程式使用資料庫，請選取您會在其中部署應用程式的相同區域。

  	![Personalize Add-on Dialog](./media/web-sites-python-ptvs-django-mysql/PollsDjangoClearDBAddon2.png)

1.  按一下 [**購買**]。

##<a name="configure-the-project"></a>設定專案

在本節中，我們將設定應用程式使用剛才建立的 MySQL 資料庫。我們將了解如何從 Azure 入口網站取得連線設定。我們也將安裝搭配使用 MySQL 資料庫與 Django 所需的其他 Python 套件。然後我們會在本機執行應用程式。

1.  在 [Azure 管理入口網站][]中，按一下 [**附加元件**]，然後按一下您稍早建立的 ClearDB MySQL 資料庫服務。

1.  按一下 [**連接資訊**]。您可以使用複製按鈕，將 **CONNECTIONSTRING** 的值放在剪貼簿上。

  	![Connection Info Dialog](./media/web-sites-python-ptvs-django-mysql/PollsDjangoMySQLConnectionInfo.png)

1.  在 Visual Studio 中，從 *ProjectName* 資料夾開啟 **settings.py**。暫時在編輯器中貼上連接字串。連接字串的格式格式：

        Database=<NAME>;Data Source=<HOST>;User Id=<USER>;Password=<PASSWORD>

    變更預設資料庫 **ENGINE** 以使用 MySQL，並設定 **CONNECTIONSTRING** 中 **NAME**、**USER**、**PASSWORD** 和 **HOST** 的值。

        DATABASES = {
            'default': {
                'ENGINE': 'django.db.backends.mysql',
                'NAME': '<Database>',
                'USER': '<User Id>',
                'PASSWORD': '<Password>',
                'HOST': '<Data Source>',
                'PORT': '',
            }
        }


1.  在 [方案總管] 的 [**Python 環境**] 之下，在虛擬環境上按一下滑鼠右鍵並選取 [**Install Python Package**]。

1. 使用 **easy_install** 安裝 `mysql-python` 套件。

  	![Install Package Dialog](./media/web-sites-python-ptvs-django-mysql/PollsDjangoMySQLInstallPackage.png)

1.  以滑鼠右鍵按一下專案節點，然後選取 [**Python**]、[**Django Sync DB**]。  

    這會為我們在上一節中建立的 MySQL 資料庫建立資料表。請依照提示建立使用者，該使用者不一定要符合在第一節中建立的 sqlite 資料庫中的使用者。

  	![Django Management Console Window](./media/web-sites-python-ptvs-django-mysql/PollsDjangoConsole.png)

1.  使用 <kbd>F5</kbd> 執行應用程式。使用 [**Create Sample Polls**] 建立的民調以及投票所提交的資料將會在 MySQL 資料庫中序列化。

##<a name="publish-to-an-azure-website"></a>發佈至 Azure 網站

PTVS 提供簡單的方法將 Web 應用程式部署至 Azure 網站。

1.  在 [**方案總管**] 中，以滑鼠右鍵按一下專案節點，然後選取 [**發佈**]。

  	![Publish Web Dialog](./media/web-sites-python-ptvs-django-mysql/PollsCommonPublishWebSiteDialog.png)

1.  按一下 [**Microsoft Azure 網站**]。

1.  按一下 [**新增**] 以建立新網站。

1.  選取 [**網站名稱**] 和 [**區域**]，然後按一下 [**建立**]。

  	![Create Site on Microsoft Azure Dialog](./media/web-sites-python-ptvs-django-mysql/PollsCommonCreateWebSite.png)

1.  接受所有其他預設值，然後按一下 [**發佈**]。

1.  網頁瀏覽器會自動開啟已發佈的網站。您應該會看到應用程式如預期般運作 (使用 Azure 上主控的 **MySQL** 資料庫)。

    恭喜！

  	![Web Browser](./media/web-sites-python-ptvs-django-mysql/PollsDjangoAzureBrowser.png)

##<a name="next-steps"></a>後續步驟

請遵循下列連結以深入了解 Python Tools for Visual Studio、Django 和 MySQL。

- [Python Tools for Visual Studio 說明文件][]
  - [Web 專案][]
  - [雲端服務專案][]
  - [在 Microsoft Azure 上進行遠端偵錯][]
- [Django 說明文件][]
- [MySQL][]


<!--Link references-->
[Python 開發人員中心]: /zh-tw/develop/python/
[Azure 雲端服務]: ../cloud-services-python-ptvs/

<!--External Link references-->
[Azure 管理入口網站]: https://manage.windowsazure.com
[Python Tools 2.1 for Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
[Python Tools 2.1 for Visual Studio 範例 VSIX]: http://go.microsoft.com/fwlink/?LinkId=517189
[Azure SDK Tools for VS 2013]: http://go.microsoft.com/fwlink/?LinkId=323510
[Azure SDK Tools for VS 2012]: http://go.microsoft.com/fwlink/?LinkId=323511
[Python 2.7 (32 位元)]: http://go.microsoft.com/fwlink/?LinkId=517190 
[Python Tools for Visual Studio 說明文件]: http://pytools.codeplex.com/documentation
[在 Microsoft Azure 上進行遠端偵錯]: http://pytools.codeplex.com/wikipage?title=Features%20Azure%20Remote%20Debugging
[Web 專案]: http://pytools.codeplex.com/wikipage?title=Features%20Web%20Project
[雲端服務專案]: http://pytools.codeplex.com/wikipage?title=Features%20Cloud%20Project
[Django 說明文件]: https://www.djangoproject.com/
[MySQL]: http://www.mysql.com/
