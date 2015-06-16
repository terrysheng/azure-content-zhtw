<properties 
	pageTitle="Azure 上採用 Python Tools 2.1 for Visual Studio 的 Django 和 SQL Database" 
	description="了解如何使用 Python Tools for Visual Studio 建立 Django 應用程式，以在 SQL 資料庫儲存個體中儲存資料並部署到網站。" 
	services="app-service\web" 
	tags="python"
	documentationCenter="python" 
	authors="huguesv" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="02/09/2015" 
	ms.author="huguesv"/>




# Azure 上採用 Python Tools 2.1 for Visual Studio 的 Django 和 SQL Database 

在此教學課程中，我們將使用 [Python Tools for Visual Studio 文件][]，使用其中一個 PTVS 範例範本來建立簡單的民調應用程式。本教學課程也提供教學[影片](https://www.youtube.com/watch?v=ZwcoGcIeHF4)。

我們將了解如何使用 Azure 上代管的 SQL Database、如何將此應用程式設定為使用 SQL Database，以及如何將此應用程式發行至 Azure 網站。

如需更多相關文章 (說明透過使用 Bottle、Flask 和 Django Web 架構的 PTVS、透過 MongoDB、Azure 資料表儲存體、MySQL 和 SQL Database 服務進行 Azure 網站開發)，請參閱 [Python 開發人員中心][]。雖然這篇文章著重於 Azure 網站，但開發 [Azure 雲端服務][]時的步驟很類似。

## 必要條件

 - Visual Studio 2012 或 2013
 - [Python Tools 2.1 for Visual Studio][]
 - [Python Tools 2.1 for Visual Studio 範例 VSIX][]
 - [Azure SDK Tools for VS 2013][] 或 [Azure SDK Tools for VS 2012][]
 - [Python 2.7 32 位元][]

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

## 建立專案

在這一節中，我們將使用範例範本建立 Visual Studio 專案。我們將建立虛擬環境並安裝必要的套件。我們將使用 sqlite 建立本機資料庫。然後會在本機執行此應用程式。

1.  在 Visual Studio 中，選取 [檔案]、[新增專案]。

1.  在 [Python]、[範例] 之下可取得 PTVS 範例 VSIX 中的專案範本。選取 [Polls Django Web Project]，然後按一下 [確定] 以建立專案。

  	![新增專案對話方塊](./media/web-sites-python-ptvs-django-sql/PollsDjangoNewProject.png)

1.  系統會提示您安裝外部套件。選取 [安裝到虛擬環境]。

  	![外部套件對話方塊](./media/web-sites-python-ptvs-django-sql/PollsDjangoExternalPackages.png)

1.  選取 [Python 2.7] 做為基礎解譯器。

  	![新增虛擬環境對話方塊](./media/web-sites-python-ptvs-django-sql/PollsCommonAddVirtualEnv.png)

1.  在專案節點上按一下滑鼠右鍵，然後選取 [Python]、[Django Sync DB]。

  	![Django Sync DB 命令](./media/web-sites-python-ptvs-django-sql/PollsDjangoSyncDB.png)

1.  此舉會開啟 Django 管理主控台。依照提示建立使用者。

    這會在專案資料夾中建立 sqlite 資料庫。

  	![Django 管理主控台視窗](./media/web-sites-python-ptvs-django-sql/PollsDjangoConsole.png)

1.  按 <kbd>F5</kbd> 確認應用程式可運作。

1.  從頂端的導覽列按一下 [登入]。

  	![網頁瀏覽器](./media/web-sites-python-ptvs-django-sql/PollsDjangoCommonBrowserLocalMenu.png)

1.  為您同步處理資料庫時建立的使用者輸入認證。

  	![網頁瀏覽器](./media/web-sites-python-ptvs-django-sql/PollsDjangoCommonBrowserLocalLogin.png)

1.  按一下 [Create Sample Polls]。

  	![網頁瀏覽器](./media/web-sites-python-ptvs-django-sql/PollsDjangoCommonBrowserNoPolls.png)

1.  按一下某項民調並投票。

  	![網頁瀏覽器](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqliteBrowser.png)

## 建立 SQL Database

對於資料庫，我們將建立 Azure SQL Database。

您可以依照下列步驟來建立資料庫。

1.  登入 [Azure 管理入口網站][]。

1.  在瀏覽窗格的底部，按一下 [新增]。

  	![新增按鈕](./media/web-sites-python-ptvs-django-sql/PollsCommonAzurePlusNew.png)

1.  依序按一下 [資料服務] 及 [ SQL DATABASE ] ，然後按一下 [快速建立]。

  	![快速建立 SQL Database](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlCreate.png)

1.  選擇建立新的 SQL 資料庫伺服器。

1.  選擇要將資料庫放置於的「區域/同質群組」。如果您會從 Azure 應用程式使用資料庫，請選取您會在其中部署應用程式的相同區域。

## 設定專案

在這一節中，我們會將應用程式設定為使用我們之前建立的 SQL Database 
。我們將可了解如何從 Azure 
 入口網站取得連線設定。我們也將安裝搭配使用 SQL 資料庫與 Django 時 
所需的其他 Python 封裝。然後會在本機執行此應用程式。

1.  在 [Azure 管理入口網站][]中，按一下 [ SQL DATABASE ]，然後按一下您先前建立的資料庫。

1.  按一下 [管理]。

  	![管理按鈕](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlManage.png)

1.  系統會提示您更新防火牆規則。按一下 [是]。這樣可讓您從開發電腦連線至資料庫伺服器。

  	![允許連線](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlUpdateFirewall.png)

1.  按一下 [SQL 資料庫]，然後按一下 [伺服器]。按一下您的資料庫的伺服器，然後按一下 [確定]。

1.  在此頁面中，您會看見能夠連線到此資料庫伺服器的每部電腦的 IP 位址。您應會看見您電腦的 IP 位址。

    接著，在 [允許的服務] 下，確定 Azure 服務能夠存取該伺服器。當應用程式在 Azure 網站中執行時 (我們將在本教學課程的下一節中執行此作業)，該應用程式便能夠連線至資料庫。按一下 []儲存 以套用變更。

  	![允許的服務](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlAllowedServices.png)

1.  在 Visual Studio 中，從  *ProjectName* 資料夾開啟 **settings.py**。編輯  `DATABASES` 的定義。

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

    `<DatabaseName>`、`<User>` 與 `<Password>` 是建立資料庫和伺服器時指定的值。

    `<ServerName>` 與 `<ServerPort>` 的值是由 Azure 在建立伺服器時產生，您可以在 [連接到您的資料庫] 區段中找到這些值。

1.  在 [方案總管] 的 [Python 環境] 下，以滑鼠右鍵按一下虛擬環境並選取 [安裝 Python 封裝]。

1.  使用 **easy_install** 安裝封裝  `pyodbc`。

  	![安裝 Python 封裝對話方塊](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlInstallPackagePyodbc.png)

1.  使用 **pip**. 安裝封裝  `django-pyodbc-azure`。

  	![安裝 Python 封裝對話方塊](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlInstallPackageDjangoPyodbcAzure.png)

1.  在專案節點上按一下滑鼠右鍵，然後選取 [Python]、[Django Sync DB]。  

    此舉會為我們在上一節中建立的 SQL Database 建立資料表。依照提示建立使用者，該使用者不需符合在第一節中建立之 sqlite 資料庫中的使用者。

  	![Django 管理主控台視窗](./media/web-sites-python-ptvs-django-sql/PollsDjangoConsole.png)

1.  利用 <kbd>F5</kbd> 執行應用程式。使用 **Create Sample Polls** 建立的民調以及投票所提交的資料將會在 SQL 資料庫中序列化。


## 發行至 Azure 網站

PTVS 提供簡單的方法將 Web 應用程式部署至 Azure 網站。

1.  在 [方案總管] 中，以滑鼠右鍵按一下專案節點並選取 [發行]。

  	![發行 Web 對話方塊](./media/web-sites-python-ptvs-django-sql/PollsCommonPublishWebSiteDialog.png)

1.  按一下 [Microsoft Azure 網站]。

1.  按一下 [新增] 以建立新網站。

1.  選取 [網站名稱] 與 [區域]，然後按一下 [建立]。

  	![在 Microsoft Azure 上建立網站對話方塊](./media/web-sites-python-ptvs-django-sql/PollsCommonCreateWebSite.png)

1.  接受其他所有預設值並按一下 [發行]。

1.  您的 Web 瀏覽器將會自動開啟到發行的網站。您應看到應用程式如預期般運作，並且使用 Azure 上代管的 **SQL** 資料庫。

    恭喜！

  	![網頁瀏覽器](./media/web-sites-python-ptvs-django-sql/PollsDjangoAzureBrowser.png)

## 後續步驟

遵循下列連結，進一步了解 Python Tools for Visual Studio、Django 和 SQL Database。

- [Python Tools for Visual Studio 文件][]
  - [Web 專案][]
  - [雲端服務專案][]
  - [在 Microsoft Azure 上進行遠端偵錯][]
- [Django 文件][]
- [SQL Database][]


<!--Link references-->
[Python 開發人員中心]: /develop/python/
[Azure 雲端服務]: ../cloud-services-python-ptvs.md

<!--External Link references-->
[Azure 管理入口網站]: https://manage.windowsazure.com
[適用於 Visual Studio 的 Python 工具]: http://aka.ms/ptvs
[Python Tools 2.1 for Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
[Python Tools 2.1 for Visual Studio 範例 VSIX]: http://go.microsoft.com/fwlink/?LinkId=517189
[Azure SDK Tools for VS 2013]: http://go.microsoft.com/fwlink/?LinkId=323510
[Azure SDK Tools for VS 2012]: http://go.microsoft.com/fwlink/?LinkId=323511
[Python 2.7 32 位元]: http://go.microsoft.com/fwlink/?LinkId=517190 
[Python Tools for Visual Studio 文件]: http://pytools.codeplex.com/documentation
[在 Microsoft Azure 上進行遠端偵錯]: http://pytools.codeplex.com/wikipage?title=Features%20Azure%20Remote%20Debugging
[Web 專案]: http://pytools.codeplex.com/wikipage?title=Features%20Web%20Project
[雲端服務專案]: http://pytools.codeplex.com/wikipage?title=Features%20Cloud%20Project
[Django 文件]: https://www.djangoproject.com/
[SQL Database]: /documentation/services/sql-database/

<!--HONumber=52--> 