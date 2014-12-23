<properties linkid="web-sites-python-ptvs-django-sql" title="Django and SQL Database on Azure with Python Tools 2.1 for Visual Studio" pageTitle="Azure 上採用 Python Tools 2.1 for Visual Studio 的 Django 和 SQL Database" description="Learn how to use the Python Tools for Visual Studio to create a Django application that stores data in a SQL database instance and can be deployed to a web site." metaKeywords="" services="" solutions="" documentationCenter="Python" authors="huvalo" videoId="" scriptId="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="10/10/2014" ms.author="huvalo" />




# Azure 上採用 Python Tools 2.1 for Visual Studio 的 Django 和 SQL Database 

在本教學課程中，我們將使用其中一個 PTVS 範例範本建立簡單的民調應用程式。

我們將學習如何使用 Azure 上主控的 SQL 資料庫、如何設定應用程式來使用 SQL 資料庫，以及如何將應用程式發佈至 Azure 網站。

如需更多相關文章 (說明透過使用 Bottle、Flask 和 Django 架構的 PTVS、透過 MongoDB、Azure 資料表儲存體、MySQL 和 SQL Database 服務進行 Azure 網站開發)，請參閱 [Python 開發人員中心][]。雖然本文著重於 Azure 網站，但其開發步驟類似於開發 [Azure 雲端服務][]。

+ [必要條件](#prerequisites)
+ [建立專案](#create-the-project)
+ [建立 SQL Database](#create-a-sql-database)
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

  	![New Project Dialog](./media/web-sites-python-ptvs-django-sql/PollsDjangoNewProject.png)

1.  系統會提示您安裝外部套件。選取 [**Install into a virtual environment**]。

  	![External Packages Dialog](./media/web-sites-python-ptvs-django-sql/PollsDjangoExternalPackages.png)

1.  選取 **Python 2.7** 作為基底解譯器。

  	![Add Virtual Environment Dialog](./media/web-sites-python-ptvs-django-sql/PollsCommonAddVirtualEnv.png)

1.  以滑鼠右鍵按一下專案節點，然後選取 [**Python**]、[**Django Sync DB**]。

  	![Django Sync DB Command](./media/web-sites-python-ptvs-django-sql/PollsDjangoSyncDB.png)

1.  這會開啟 Django 管理主控台。遵循提示以建立使用者。

    這會在專案資料夾中建立 sqlite 資料庫。

  	![Django Management Console Window](./media/web-sites-python-ptvs-django-sql/PollsDjangoConsole.png)

1.  按 <kbd>F5</kbd>，確認應用程式可運作。

1.  按一下頂端導覽列中的 [**登入**]。

  	![Web Browser](./media/web-sites-python-ptvs-django-sql/PollsDjangoCommonBrowserLocalMenu.png)

1.  為您在同步處理資料庫時建立的使用者輸入認證。

  	![Web Browser](./media/web-sites-python-ptvs-django-sql/PollsDjangoCommonBrowserLocalLogin.png)

1.  按一下 [**Create Sample Polls**]。

  	![Web Browser](./media/web-sites-python-ptvs-django-sql/PollsDjangoCommonBrowserNoPolls.png)

1.  按一下民調並進行投票。

  	![Web Browser](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqliteBrowser.png)

##<a name="create-a-sql-database"></a>建立 SQL Database

對於資料庫，我們將建立 Azure SQL 資料庫。

您可以依照下列步驟來建立資料庫。

1.  登入 [Azure 管理入口網站][]。

1.  在導覽窗格的底端，按一下 [**新增**]。

  	![New Button](./media/web-sites-python-ptvs-django-sql/PollsCommonAzurePlusNew.png)

1.  按一下 [**資料服務**]、[**SQL 資料庫**]，然後按一下 [**快速建立**]。

  	![Quick Create SQL Database](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlCreate.png)

1.  選擇建立新的 SQL 資料庫伺服器。

1.  選擇要將資料庫放置於的「區域/同質群組」。如果您會從 Azure 應用程式使用資料庫，請選取您會在其中部署應用程式的相同區域。

##<a name="configure-the-project"></a>設定專案

在本節中，我們將設定應用程式使用剛才建立的 SQL 資料庫。我們將了解如何從 Azure 入口網站取得連線設定。我們也將安裝搭配使用 SQL 資料庫與 Django 所需的其他 Python 套件。然後我們會在本機執行應用程式。

1.  在 [Azure 管理入口網站][]中，按一下 [**SQL 資料庫**]，然後按一下您稍早建立的資料庫。

1.  按一下 [**管理**]。

  	![Manage Button](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlManage.png)

1.  系統會提示您更新防火牆規則。按一下 [**是**]。這將允許從開發電腦連接至資料庫伺服器。

  	![Allow Connections](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlUpdateFirewall.png)

1.  依序按一下 [**SQL 資料庫**]、[**伺服器**]。對您的資料庫按一下此伺服器，然後按一下 [**設定**]。

1.  在此頁面中，您會看到能夠連接到資料庫伺服器的每部電腦的 IP 位址。您應該會看到您的電腦的 IP 位址。

    在底下 [**允許的服務**] 下，確定 Azure 服務能夠存取伺服器。當應用程式在 Azure 網站中執行 (我們將在本教學課程的下一節中進行) 時，它便能夠連接至資料庫。按一下 [**儲存**] 以套用變更。

  	![Allowed Services](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlAllowedServices.png)

1.  在 Visual Studio 中，從 *ProjectName* 資料夾開啟 **settings.py**。編輯 `DATABASES` 的定義。

        DATABASES = {
            'default': {
                'ENGINE': 'sql_server.pyodbc',
                'NAME': '<DatabaseName>',
                'USER': '<User>@<ServerName>',
                'PASSWORD': '<Password>',
                'HOST': '<ServerName>.database.windows.net',
                'PORT': '<ServerPort>',
                'OPTIONS': {
                    'driver': 'SQL Server Native Client 11.0',
                    'MARS_Connection': 'True',
                }
            }
        }

    `<DatabaseName>`、`<User>` 和 `<Password>` 是您建立資料庫和伺服器時所指定的值。

    `<ServerName>` 和 `<ServerPort>` 是由 Azure 在建立伺服器時所產生，可以在 [**建立您的資料庫**] 區段中找到。

1.  在 [方案總管] 的 [**Python 環境**] 之下，在虛擬環境上按一下滑鼠右鍵並選取 [**Install Python Package**]。

1.  使用 **easy_install** 安裝 `pyodbc` 套件。

  	![Install Python Package Dialog](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlInstallPackagePyodbc.png)

1.  使用 **pip** 安裝 `django-pyodbc-azure` 套件。

  	![Install Python Package Dialog](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlInstallPackageDjangoPyodbcAzure.png)

1.  以滑鼠右鍵按一下專案節點，然後選取 [**Python**]、[**Django Sync DB**]。  

    這會為我們在上一節中建立的 MySQL 資料庫建立資料表。請依照提示建立使用者，該使用者不一定要符合在第一節中建立的 sqlite 資料庫中的使用者。

  	![Django Management Console Window](./media/web-sites-python-ptvs-django-sql/PollsDjangoConsole.png)

1.  使用 <kbd>F5</kbd> 執行應用程式。使用 [**Create Sample Polls**] 建立的民調以及投票所提交的資料將會在 SQL 資料庫中序列化。


##<a name="publish-to-an-azure-website"></a>發佈至 Azure 網站

PTVS 提供簡單的方法將 Web 應用程式部署至 Azure 網站。

1.  在 [**方案總管**] 中，以滑鼠右鍵按一下專案節點，然後選取 [**發佈**]。

  	![Publish Web Dialog](./media/web-sites-python-ptvs-django-sql/PollsCommonPublishWebSiteDialog.png)

1.  按一下 [**Microsoft Azure 網站**]。

1.  按一下 [**新增**] 以建立新網站。

1.  選取 [**網站名稱**] 和 [**區域**]，然後按一下 [**建立**]。

  	![Create Site on Microsoft Azure Dialog](./media/web-sites-python-ptvs-django-sql/PollsCommonCreateWebSite.png)

1.  接受所有其他預設值，然後按一下 [**發佈**]。

1.  網頁瀏覽器會自動開啟已發佈的網站。您應該會看到應用程式如預期般運作 (使用 Azure 上主控的 **SQL** 資料庫)。

    恭喜！

  	![Web Browser](./media/web-sites-python-ptvs-django-sql/PollsDjangoAzureBrowser.png)

##<a name="next-steps"></a>後續步驟

請遵循下列連結以深入了解 Python Tools for Visual Studio、Django 和 SQL Database。

- [Python Tools for Visual Studio 說明文件][]
  - [Web 專案][]
  - [雲端服務專案][]
  - [在 Microsoft Azure 上進行遠端偵錯][]
- [Django 說明文件][]
- [SQL Database][]


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
[SQL Database]: /zh-tw/documentation/services/sql-database/
