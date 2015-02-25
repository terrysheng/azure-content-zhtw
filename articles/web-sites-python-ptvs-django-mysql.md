<properties 
	pageTitle="Azure 上採用 Python Tools 2.1 for Visual Studio 的 Django 和 MySQL" 
	description="了解如何使用 Python Tools for Visual Studio 建立 Django 應用程式，以在 MySQL 資料庫儲存個體中儲存資料並佈署到網站。 
	services="" 
	documentationCenter="python" 
	authors="huguesv" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="10/10/2014" 
	ms.author="huvalo"/>




# Azure 上採用 Python Tools 2.1 for Visual Studio 的 Django 和 MySQL 

在本教學課程中，我們將使用其中一個 PTVS 範例範本來建立簡單的民調應用程式。本教學課程也提供教學[影片](https://www.youtube.com/watch?v=oKCApIrS0Lo)。

我們將了解如何使用 Azure 上託管的 MySQL 服務、如何將此應用程式設定為使用 MySQL，以及如何將此應用程式發行至 Azure 網站。

如需更多相關文章 (說明透過使用 Bottle、Flask 和 Django Web 架構的 PTVS、透過 MongoDB、Azure 資料表儲存體、MySQL 和 SQL Database 服務進行 Azure 網站開發)，請參閱 [Python 開發人員中心][]。雖然這篇文章著重於 Azure 網站，但開發 [Azure 雲端服務][]時的步驟很類似。

+ [先決條件](#prerequisites)
+ [建立專案](#create-the-project)
+ [建立 MySQL 資料庫](#create-a-mysql-database)
+ [設定專案](#configure-the-project)
+ [發行至 Azure 網站](#publish-to-an-azure-website)
+ [後續步驟](#next-steps)

##<a name="prerequisites"></a>先決條件

 - Visual Studio 2012 或 2013
 - [Python Tools 2.1 for Visual Studio][]
 - [Python Tools 2.1 for Visual Studio 範例 VSIX][]
 - [Azure SDK Tools for VS 2013][] 或 [Azure SDK Tools for VS 2012][]
 - [Python 2.7 32 位元][]

> [AZURE.NOTE]
> 若要完成此教學課程，您需要 Azure 帳戶。您可以<a href="http://azure.microsoft.com/zh-tw/pricing/member-offers/msdn-benefits-details/">啟用自己的 訂戶權益</a>或<a href="http://azure.microsoft.com/zh-tw/pricing/free-trial/">申請免費試用</a>。
> 
> 如果您要在註冊帳戶前開始使用 Azure 網站，請移至 <a href="https://trywebsites.azurewebsites.net/?language=python">https://trywebsites.azurewebsites.net</a>，您可以在 Azure 網站中立即建立短期的免費 ASP.NET 簡易版網站。無需使用信用卡，也不用簽約。

##<a name="create-the-project"></a>建立專案

在這一節中，我們將使用範例範本建立 Visual Studio 專案。我們將建立虛擬環境並安裝必要的套件。我們將使用 sqlite 建立本機資料庫。然後會在本機執行此應用程式。

1.  在 Visual Studio 中，選取 [檔案]****、[新增專案]****。

1.  在 [Python]****、[範例]**** 之下可取得 PTVS 範例 VSIX 中的專案範本。選取 [Polls Django Web Project]****，然後按一下 [確定] 以建立專案。

  	![New Project Dialog](./media/web-sites-python-ptvs-django-mysql/PollsDjangoNewProject.png)

1.  系統會提示您安裝外部套件。選取 [安裝到虛擬環境]****。

  	![External Packages Dialog](./media/web-sites-python-ptvs-django-mysql/PollsDjangoExternalPackages.png)

1.  選取 [Python 2.7]**** 做為基礎解譯器。

  	![Add Virtual Environment Dialog](./media/web-sites-python-ptvs-django-mysql/PollsCommonAddVirtualEnv.png)

1.  在專案節點上按一下滑鼠右鍵，然後選取 [Python]****、[Django Sync DB]****。

  	![Django Sync DB Command](./media/web-sites-python-ptvs-django-mysql/PollsDjangoSyncDB.png)

1.  此舉會開啟 Django 管理主控台。依照提示建立使用者。

    這會在專案資料夾中建立 sqlite 資料庫。

  	![Django Management Console Window](./media/web-sites-python-ptvs-django-mysql/PollsDjangoConsole.png)

1.  按下 <kbd>F5</kbd>，以確認應用程式可運作。

1.  從頂端的導覽列按一下 [登入]****。

  	![Web Browser](./media/web-sites-python-ptvs-django-mysql/PollsDjangoCommonBrowserLocalMenu.png)

1.  為您同步處理資料庫時建立的使用者輸入認證。

  	![Web Browser](./media/web-sites-python-ptvs-django-mysql/PollsDjangoCommonBrowserLocalLogin.png)

1.  按一下 [Create Sample Polls]****。

  	![Web Browser](./media/web-sites-python-ptvs-django-mysql/PollsDjangoCommonBrowserNoPolls.png)

1.  按一下某項民調並投票。

  	![Web Browser](./media/web-sites-python-ptvs-django-mysql/PollsDjangoSqliteBrowser.png)

##<a name="create-a-mysql-database"></a>建立 MySQL 資料庫

對於資料庫，我們將在 Azure 上建立 ClearDB MySQL 代管資料庫。

此外，您可以在 Azure 中建立自己的虛擬機器，然後自行安裝及管理 MySQL。

您可以依照下列步驟來建立具有免費計畫的資料庫。

1.  登入 [Azure 管理入口網站][]。

1.  在瀏覽窗格的底部，按一下 [新增]****。

  	![New Button](./media/web-sites-python-ptvs-django-mysql/PollsCommonAzurePlusNew.png)

1.  按一下 [儲存]****，然後按一下 [ClearDB MySQL 資料庫]****。

  	![Choose Add-on Dialog](./media/web-sites-python-ptvs-django-mysql/PollsDjangoClearDBAddon1.png)

1.  在 [名稱] 中，輸入要用於資料庫服務的名稱。

1.  選擇要將資料庫服務放置於的「區域/同質群組」。如果您會從 Azure 應用程式使用資料庫，請選取您會在其中部署應用程式的相同區域。

  	![Personalize Add-on Dialog](./media/web-sites-python-ptvs-django-mysql/PollsDjangoClearDBAddon2.png)

1.  按一下 [購買]****。

##<a name="configure-the-project"></a>設定專案

在這一節中，我們會將應用程式設定為使用我們剛才建立的 MySQL 資料庫。我們將可了解如何從 Azure 入口網站取得連線設定。我們也將安裝搭配使用 MySQL 資料庫與 Django 時所需的其他 Python 封裝。然後會在本機執行此應用程式。

1.  在 [Azure 管理入口網站][]中，按一下 [附加元件]****，然後按一下您先前建立的 ClearDB MySQL Database 服務。

1.  按一下 [連線資訊]****。您可以使用複製按鈕，將 **CONNECTIONSTRING** 的值放在剪貼簿上。

  	![Connection Info Dialog](./media/web-sites-python-ptvs-django-mysql/PollsDjangoMySQLConnectionInfo.png)

1.  在 Visual Studio 中，從  *ProjectName* 資料夾開啟 **settings.py**。暫時將連接字串貼到編輯器中。連接字串的格式如下：

        Database=<NAME>;Data Source=<HOST>;User Id=<USER>;Password=<PASSWORD>

    變更預設資料庫 **ENGINE** 以使用 MySQL，然後設定 **CONNECTIONSTRING** 的 [名稱]****、[使用者]****、[密碼]**** 與 [主機]**** 值。

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


1.  在 [方案總管] 的 [Python 環境]**** 下，以滑鼠右鍵按一下虛擬環境並選取 [安裝 Python 封裝]****。

1. 使用 **easy_install** 安裝封裝  `mysql-python`。

  	![Install Package Dialog](./media/web-sites-python-ptvs-django-mysql/PollsDjangoMySQLInstallPackage.png)

1.  在專案節點上按一下滑鼠右鍵，然後選取 [Python]****、[Django Sync DB]****。  

    此舉會為我們在上一節中建立的 MySQL 資料庫建立資料表。依照提示建立使用者，該使用者不需符合在第一節中建立之 sqlite 資料庫中的使用者。

  	![Django Management Console Window](./media/web-sites-python-ptvs-django-mysql/PollsDjangoConsole.png)

1.  使用 <kbd>F5</kbd> 執行應用程式。使用 [Create Sample Polls]**** 建立的民調以及投票所提交的資料將會在 MySQL 資料庫中序列化。

##<a name="publish-to-an-azure-website"></a>發行至 Azure 網站

PTVS 提供簡單的方法將 Web 應用程式部署至 Azure 網站。

1.  在 [方案總管]**** 中，以滑鼠右鍵按一下專案節點並選取 [發行]****。

  	![Publish Web Dialog](./media/web-sites-python-ptvs-django-mysql/PollsCommonPublishWebSiteDialog.png)

1.  按一下 [Microsoft Azure 網站]****。

1.  按一下 [新增]**** 以建立新網站。

1.  選取 [網站名稱]**** 與 [區域]****，然後按一下 [建立]****。

  	![Create Site on Microsoft Azure Dialog](./media/web-sites-python-ptvs-django-mysql/PollsCommonCreateWebSite.png)

1.  接受其他所有預設值並按一下 [發行]****。

1.  您的 Web 瀏覽器將會自動開啟到發行的網站。您應看到應用程式如預期般運作，並且使用 Azure 上代管的 **MySQL** 資料庫。

    恭喜！

  	![Web Browser](./media/web-sites-python-ptvs-django-mysql/PollsDjangoAzureBrowser.png)

##<a name="next-steps"></a>後續步驟

遵循下列連結，進一步了解 Python Tools for Visual Studio、Django 和 MySQL。

- [Python Tools for Visual Studio 文件][]
  - [Web 專案][]
  - [雲端服務專案][]
  - [在 Microsoft Azure 上進行遠端偵錯][]
- [Django 文件][]
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
[Python 2.7 32 位元]: http://go.microsoft.com/fwlink/?LinkId=517190 
[Python Tools for Visual Studio 文件]: http://pytools.codeplex.com/documentation
[在 Microsoft Azure 上進行遠端偵錯]: http://pytools.codeplex.com/wikipage?title=Features%20Azure%20Remote%20Debugging
[Web 專案]: http://pytools.codeplex.com/wikipage?title=Features%20Web%20Project
[雲端服務專案]: http://pytools.codeplex.com/wikipage?title=Features%20Cloud%20Project
[Django 文件]: https://www.djangoproject.com/
[MySQL]: http://www.mysql.com/


<!--HONumber=42-->
