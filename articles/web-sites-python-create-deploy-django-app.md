<properties 
	pageTitle="採用 Django 的 Python 網站 - Azure 教學課程" 
	description="介紹在 Azure 上執行 Python 網站的教學課程。" 
	services="web-sites" 
	documentationCenter="python" 
	authors="huguesv" 
	manager="" 
	editor=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="12/17/2014" 
	ms.author="huvalo"/>




# 使用 Django 建立網站

本教學課程說明如何開始在 Azure 網站上執行 Python。Azure 網站提供有限的免費主控和快速的部署，而現在您可以使用 Python！隨著應用程式規模增加，您可以切換為付費主控，也可以與其他所有 Azure 服務整合。

您將建立使用 Django Web 架構的應用程式 (請參閱本教學課程適用於 [Flask](../web-sites-python-create-deploy-flask-app) 與 [Bottle] 的其他版本(../web-sites-python-create-deploy-bottle-app))。您會從 Azure 組件庫建立網站、設定 Git 部署，並於本機複製儲存機制。然後您會在本機執行應用程式、進行變更、認可和推送至 Azure。本教學課程示範如何從 Windows 或 Mac/Linux 執行這項操作。

> [AZURE.NOTE]
> 若要完成此教學課程，您需要 Azure 帳戶。您可以<a href="http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/">啟用自己的 MSDN 訂戶權益</a>或是<a href="http://azure.microsoft.com/pricing/free-trial/">申請免費試用</a>。
> 
> 如果您要在註冊帳戶前開始使用 Azure 網站，請移至 <a href="https://trywebsites.azurewebsites.net/?language=python">https://trywebsites.azurewebsites.net</a>，您可以在 Azure 網站中立即建立短期的免費 ASP.NET 簡易版網站。無需使用信用卡，也不用簽約。


+ [先決條件](#prerequisites)
+ [在入口網站上建立網站](#website-creation-on-portal)
+ [應用程式概觀](#application-overview)
+ 網站開發
  + [Windows - Python Tools for Visual Studio](#website-development-windows-ptvs)
  + [Windows - 命令列](#website-development-windows-command-line)
  + [Mac/Linux - 命令列](#website-development-mac-linux-command-line)
+ [疑難排解 - 部署](#troubleshooting-deployment)
+ [疑難排解 - 封裝安裝](#troubleshooting-package-installation)
+ [疑難排解 - 虛擬環境](#troubleshooting-virtual-environment)
+ [疑難排解 - 靜態檔案](#troubleshooting-static-files)
+ [疑難排解 - 設定](#troubleshooting-settings)
+ [使用資料庫](#using-a-database)
+ [Django 管理介面](#django-admin-interface)
+ [後續步驟](#next-steps)


<h2><a name="prerequisites"></a>先決條件</h2>

- Windows、Mac 或 Linux
- Python 2.7 或 3.4
- setuptools、pip、virtualenv (僅 Python 2.7)
- Git
- Python Tools for Visual Studio (選用)

**注意**：Python 專案目前不支援 TFS 發行。

### Windows

如果您還沒有安裝 Python 2.7 或 3.4 (32 位元)，建議您使用 Web Platform Installer 安裝 [Azure SDK for Python 2.7](http://go.microsoft.com/fwlink/?linkid=254281&clcid=0x409) 或 [Azure SDK for Python 3.4](http://go.microsoft.com/fwlink/?LinkID=516990&clcid=0x409)。這會安裝 32 位元版本的 Python、setuptools、pip、virtualenv 等 (32 位元 Python 安裝於 Azure 主機電腦上)。或者，您可以從 [python.org](http://www.python.org/) 取得 Python。

針對 Git，建議您使用 [Git for Windows](http://msysgit.github.io/)。本教學課程使用 Git for Windows 的 Git Shell。如果您使用 Visual Studio，您可以使用整合式的 Git 支援。

我們也建議您安裝 [Python Tools for Visual Studio](http://pytools.codeplex.com)。這是選擇性的，但如果您有 [Visual Studio](http://www.visualstudio.com/) (包含免費的 Visual Studio Express 2013 for Web)，它會提供您絕佳的 Python IDE。

### Mac/Linux

您應該已經安裝 Python 與 Git，但請確定您擁有的是 Python 2.7 或 3.4。


<h2><a name="website-creation-on-portal"></a>在入口網站上建立網站</h2>

建立應用程式的第一步是透過 Azure 管理入口網站建立網站。若要建立網站，您需要登入入口網站並按一下左下角的 [新增]**** 按鈕。隨即會出現一個視窗。依序按一下 [計算]****、[網站]**** 然後 [從組件庫]****。

![](./media/web-sites-python-create-deploy-django-app/portal-create-site.png)

會出現一個視窗，列出組件庫中可用的應用程式。按一下左側的 [應用程式架構]**** 類別，然後選取 [Django]****。

![](./media/web-sites-python-create-deploy-django-app/portal-gallery-django.png)

在下一頁中，輸入您網站的名稱和地區，然後按一下 [完成] 按鈕。

系統隨即會迅速地設定網站。您可以按一下底部工具列中的 [瀏覽]**** 按鈕，您會看到新的 Django 應用程式在 Azure 上執行。

![](./media/web-sites-python-create-deploy-django-app/portal-website-django.png)
 
接下來，您需要新增透過 Git 發行的支援功能。您可以藉由選擇 [設定從原始檔控制進行部署]**** 來完成操作。

![](./media/web-sites-python-create-deploy-django-app/portal-site-created.png)

從 [設定部署]**** 對話方塊中，向下捲動並選取 [本機 Git 儲存機制]**** 選項。按一下向右箭頭以繼續操作。

![](./media/web-sites-python-create-deploy-django-app/portal-setup-deployment.png)

設定好 Git 發行後，您會短暫地看到一個頁面，通知您系統正在建立儲存機制。準備就緒時，您會看到關於如何連接的指示。  

![](./media/web-sites-python-create-deploy-django-app/portal-repo-created.png)

在後續章節中，我們會遵循這些指示。


<h2><a name="application-overview"></a>應用程式概觀</h2>

### Git 儲存機制內容

以下是您會在初始的 Git 儲存機制中找到的檔案概觀，我們將在下一節中複製。

    \app\__init__.py
    \app\forms.py
    \app\models.py
    \app\tests.py
    \app\views.py
    \app\static\content\
    \app\static\fonts\
    \app\static\scripts\
    \app\templates\about.html
    \app\templates\contact.html
    \app\templates\index.html
    \app\templates\layout.html
    \app\templates\login.html
    \app\templates\loginpartial.html
    \DjangoWebProject\__init__.py
    \DjangoWebProject\settings.py
    \DjangoWebProject\urls.py
    \DjangoWebProject\wsgi.py

應用程式的主要來源。包含 3 個主要的版面配置頁面 (index、about、contact)。靜態內容和指令碼，包含啟動程序、jquery、modernizr 和回應。

    \manage.py

本機管理和開發伺服器支援。使用這個選項在本機執行應用程式、同步處理資料庫等等。

    \db.sqlite3

預設資料庫。包含所需的資料表，應用程式才能執行，但不包含任何使用者 (同步處理資料庫來建立使用者)。

    \DjangoWebProject.pyproj
    \DjangoWebProject.sln

搭配 [Python Tools for Visual Studio](http://pytools.codeplex.com) 使用的專案檔。

    \ptvs_virtualenv_proxy.py

虛擬環境和 PTVS 遠端偵錯支援的 IIS Proxy。

    \requirements.txt

此應用程式所需的外部封裝。部署指令碼將 pip 安裝在這個檔案中所列的封裝。
 
    \web.2.7.config
    \web.3.4.config

IIS 組態檔。部署指令碼會使用適當的 web.x.y.config，並將它複製為 web.config。

### 選用的檔案 - 自訂部署

[AZURE.INCLUDE [web-sites-python-customizing-deployment](../includes/web-sites-python-customizing-deployment.md)]

### 選用的檔案 - Python 執行階段

[AZURE.INCLUDE [web-sites-python-customizing-runtime](../includes/web-sites-python-customizing-runtime.md)]

### 在伺服器上的其他檔案

某些檔案存在於伺服器上，但未加入至 Git 儲存機制。這些檔案由部署指令碼建立。

    \web.config

IIS 組態檔。從 web.x.y.config 建立於每個部署上。

    \env\

Python 虛擬環境。如果網站上不存在相容的虛擬環境，會於部署期間建立。requirements.txt 中所列封裝為 pip 安裝，但是如果封裝已安裝，pip 會跳過安裝。

接下來的 3 小節會說明如何在 3 個不同環境中繼續進行網站開發：

- Windows，使用 Python Tools for Visual Studio
- Windows，使用命令列
- Mac/Linux，使用命令列


<h2><a name="website-development-windows-ptvs"></a>網站開發 - Windows - Python Tools for Visual Studio</h2>

### 複製儲存機制

首先，使用 Azure 入口網站上提供的 URL 複製儲存機制。

![](./media/web-sites-python-create-deploy-django-app/ptvs-git-clone.png)

開啟包含在儲存機制根目錄中的方案檔 (.sln)。

![](./media/web-sites-python-create-deploy-django-app/ptvs-solution-django.png)

### 建立虛擬環境

現在我們要建立本機開發的虛擬環境。以滑鼠右鍵按一下 [Python 環境]****，選取 [加入虛擬環境...]****。

- 請確定環境的名稱是  `env`。

- 選取基礎解譯器。請務必使用針對網站選取之相同版本的 Python (在 runtime.txt 或網站組態頁面中)。

- 確定已勾選下載並安裝封裝的選項。

![](./media/web-sites-python-create-deploy-django-app/ptvs-add-virtual-env-27.png)

按一下 [建立]****。這會建立虛擬環境，並安裝 requirements.txt 中列出的相依性。

### 建立超級使用者

應用程式包含的資料庫並沒有定義任何超級使用者。若要使用應用程式登入功能或 Django 管理介面 (如果您決定要啟用它)，您必須建立超級使用者。

從您專案資料夾的命令列執行：

    env\scripts\python manage.py createsuperuser

請依照下列提示來設定使用者名稱、密碼等等。

### 使用開發伺服器來執行

按 F5 開始偵錯，並且網頁瀏覽器會自動開啟在本機執行的頁面。

![](./media/web-sites-python-create-deploy-django-app/windows-browser-django.png)

您可以在來源中設定中斷點、使用監看式視窗等等。如需各種功能的詳細資訊，請參閱 [PTVS 文件](http://pytools.codeplex.com/documentation)。

### 進行變更

現在您可以嘗試對應用程式來源和/或範本進行變更。

測試過您的變更之後，請將它們認可至 Git 儲存機制：

![](./media/web-sites-python-create-deploy-django-app/ptvs-commit-django.png)

### 安裝其他封裝

您的應用程式可能會擁有 Python 和 Django 之外的相依性。

您可以使用 pip 安裝其他封裝。若要安裝封裝，以滑鼠右鍵按一下虛擬環境，然後選取 [安裝 Python 封裝]****。

例如，若要安裝 Azure SDK for Python，讓您可存取 Azure 儲存體、服務匯流排和其他 Azure 服務，請輸入  `azure`：

![](./media/web-sites-python-create-deploy-django-app/ptvs-install-package-dialog.png)

以滑鼠右鍵按一下虛擬環境，然後選取 [產生 requirements.txt]**** 更新 requirements.txt。

然後，將變更認可到 Git 儲存機制的 requirements.txt。

### 部署至 Azure

若要觸發部署，按一下 [同步]**** 或 [推送]****。同步處理會推送和提取。

![](./media/web-sites-python-create-deploy-django-app/ptvs-git-push.png)

第一次部署將需要一些時間，因為它會建立虛擬環境、安裝封裝等。

Visual Studio 不會顯示部署進度。如果您想要檢閱輸出，請參閱[疑難排解 - 部署]一節(#troubleshooting-deployment)。

瀏覽至 Azure URL，以檢視您的變更。


<h2><a name="website-development-windows-command-line"></a>網站開發 - Windows - 命令列</h2>

### 複製儲存機制

首先，使用 Azure 入口網站上提供的 URL 複製儲存機制，並將 Azure 儲存機制加入為遠端。

    git clone <repo-url>
    cd <repo-folder>
    git remote add azure <repo-url> 

### 建立虛擬環境

我們要建立開發用途的新虛擬環境 (不加入至儲存機制)。Python 虛擬環境不可重置，因此每位使用該應用程式的開發人員都會在本機建立。

請務必使用針對網站選取之相同版本的 Python (在 runtime.txt 或網站組態頁面中)。

針對 Python 2.7：

    c:\python27\python.exe -m virtualenv env

針對 Python 3.4：

    c:\python34\python.exe -m venv env

安裝應用程式所需的任何外部封裝。您可以在儲存機制的根目錄使用 requirements.txt 檔案，在虛擬環境中安裝封裝：

    env\scripts\pip install -r requirements.txt

### 建立超級使用者

應用程式包含的資料庫並沒有定義任何超級使用者。若要使用應用程式登入功能或 Django 管理介面 (如果您決定要啟用它)，您必須建立超級使用者。

從您專案資料夾的命令列執行：

    env\scripts\python manage.py createsuperuser

請依照下列提示來設定使用者名稱、密碼等等。

### 使用開發伺服器來執行

您可以使用下列命令，在開發伺服器下啟動應用程式：

    env\scripts\python manage.py runserver

主控台會顯示 URL，以及伺服器接聽的通訊埠：

![](./media/web-sites-python-create-deploy-django-app/windows-run-local-django.png)

然後，開啟網頁瀏覽器指向該 URL。

![](./media/web-sites-python-create-deploy-django-app/windows-browser-django.png)

### 進行變更

現在您可以嘗試對應用程式來源和/或範本進行變更。

測試過您的變更之後，請將它們認可至 Git 儲存機制：

    git add <modified-file>
    git commit -m "<commit-comment>"

### 安裝其他封裝

您的應用程式可能會擁有 Python 和 Django 之外的相依性。

您可以使用 pip 安裝其他封裝。例如，若要安裝 Azure SDK for Python，讓您可存取 Azure 儲存體、服務匯流排和其他 Azure 服務，請輸入：

    env\scripts\pip install azure

請務必更新 requirements.txt：

    env\scripts\pip freeze > requirements.txt

認可變更：

    git add requirements.txt
    git commit -m "Added azure package"

### 部署至 Azure

若要觸發部署，將變更推送至 Azure：

    git push azure master

您會看到部署指令碼的輸出，包括虛擬環境建立、安裝封裝、建立 web.config。

瀏覽至 Azure URL，以檢視您的變更。


<h2><a name="website-development-mac-linux-command-line"></a>網站開發 - Mac/Linux - 命令列</h2>

### 複製儲存機制

首先，使用 Azure 入口網站上提供的 URL 複製儲存機制，並將 Azure 儲存機制加入為遠端。

    git clone <repo-url>
    cd <repo-folder>
    git remote add azure <repo-url> 

### 建立虛擬環境

我們要建立開發用途的新虛擬環境 (不加入至儲存機制)。Python 虛擬環境不可重置，因此每位使用該應用程式的開發人員都會在本機建立。

請務必使用針對網站選取之相同版本的 Python (在 runtime.txt 或網站組態頁面中)。

針對 Python 2.7：

    python -m virtualenv env

針對 Python 3.4：

    python -m venv env

安裝應用程式所需的任何外部封裝。您可以在儲存機制的根目錄使用 requirements.txt 檔案，在虛擬環境中安裝封裝：

    env/bin/pip install -r requirements.txt

### 建立超級使用者

應用程式包含的資料庫並沒有定義任何超級使用者。若要使用應用程式登入功能或 Django 管理介面 (如果您決定要啟用它)，您必須建立超級使用者。

從您專案資料夾的命令列執行：

    env/bin/python manage.py createsuperuser

請依照下列提示來設定使用者名稱、密碼等等。

### 使用開發伺服器來執行

您可以使用下列命令，在開發伺服器下啟動應用程式：

    env/bin/python manage.py runserver

主控台會顯示 URL，以及伺服器接聽的通訊埠：

![](./media/web-sites-python-create-deploy-django-app/mac-run-local-django.png)

然後，開啟網頁瀏覽器指向該 URL。

![](./media/web-sites-python-create-deploy-django-app/mac-browser-django.png)

### 進行變更

現在您可以嘗試對應用程式來源和/或範本進行變更。

測試過您的變更之後，請將它們認可至 Git 儲存機制：

    git add <modified-file>
    git commit -m "<commit-comment>"

### 安裝其他封裝

您的應用程式可能會擁有 Python 和 Django 之外的相依性。

您可以使用 pip 安裝其他封裝。例如，若要安裝 Azure SDK for Python，讓您可存取 Azure 儲存體、服務匯流排和其他 Azure 服務，請輸入：

    env/bin/pip install azure

請務必更新 requirements.txt：

    env/bin/pip freeze > requirements.txt

認可變更：

    git add requirements.txt
    git commit -m "Added azure package"

### 部署至 Azure

若要觸發部署，將變更推送至 Azure：

    git push azure master

您會看到部署指令碼的輸出，包括虛擬環境建立、安裝封裝、建立 web.config。

瀏覽至 Azure URL，以檢視您的變更。


<h2><a name="troubleshooting-deployment"></a>疑難排解 - 部署</h2>

[AZURE.INCLUDE [web-sites-python-troubleshooting-deployment](../includes/web-sites-python-troubleshooting-deployment.md)]


<h2><a name="troubleshooting-package-installation"></a>疑難排解 - 封裝安裝</h2>

[AZURE.INCLUDE [web-sites-python-troubleshooting-package-installation](../includes/web-sites-python-troubleshooting-package-installation.md)]


<h2><a name="troubleshooting-virtual-environment"></a>疑難排解 - 虛擬環境</h2>

[AZURE.INCLUDE [web-sites-python-troubleshooting-virtual-environment](../includes/web-sites-python-troubleshooting-virtual-environment.md)]


<h2><a name="troubleshooting-static-files"></a>疑難排解 - 靜態檔案</h2>

Django 具有收集靜態檔案的概念。這會從原始位置取得所有靜態檔案，並將它們複製到單一資料夾。針對此應用程式，它們會複製到  `/static`。

這是因為靜態檔案可能來自不同的 Django  'apps'。例如，Django 管理介面的靜態檔案位於虛擬環境中的 Django 程式庫子資料夾。此應用程式所定義的靜態檔案位於  `/app/static`。當您使用多個 Django  'apps'，您必須擁有位於多個位置中的靜態檔案。

當在偵錯模式中執行應用程式，應用程式會從其原始位置提供靜態檔案。

在發行模式中執行應用程式，應用程式**不會**提供靜態檔案。Web 伺服器的責任就是提供檔案。針對此應用程式，IIS 將會從  `/static` 提供靜態檔案。

靜態檔案的集合會做為部署指令碼的一部分自動完成，清除先前收集的檔案。這表示此集合會發生在每個部署上、稍微降低部署速度，但它可確保已過時的檔案無法使用，避免潛在的安全性問題。 

如果您想要跳過 Django 應用程式的靜態檔案收集：

    \.skipDjango 

那麼您必須要在本機電腦，手動執行收集：

    env\scripts\python manage.py collectstatic

然後從  `.gitignore` 移除  `\static` 資料夾，並將它加入至 Git 儲存機制。


<h2><a name="troubleshooting-settings"></a>疑難排解 - 設定</h2>

應用程式的各種設定可以在  `DjangoWebProject/settings.py` 變更

為了開發人員方便起見，已啟用偵錯模式。其中一項不錯的副作用是，您能在本機執行時看見映像和其他靜態內容，而不需要收集靜態檔案。

若要停用偵錯模式：

    DEBUG = False

停用偵錯時，需要更新  `ALLOWED_HOSTS` 值以包含 Azure 主機名稱。例如：

    ALLOWED_HOSTS = (
        'pythonapp.azurewebsites.net',
    )

或啟用任何：

    ALLOWED_HOSTS = (
        '*',
    )
 
在實務上，您可能希望進行更複雜的操作，以應付在偵錯和發行模式之間切換，以及取得主機名稱。

您可以透過 Azure 入口網站中的 [設定]**** 頁面，設定環境變數 (在 [應用程式設定]**** 一節中)。這對設定您不想要顯示於來源 (連接字串、密碼等等) 中的值，或是您想要 Azure 與本機電腦有不同設定時很有幫助。在  `settings.py` 中，您可以使用  `os.getenv` 查詢環境變數。


<h2><a name="using-a-database"></a>使用資料庫</h2>

應用程式隨附的資料庫為 SQLite 資料庫這是用於開發、方便實用的預設資料庫，因為它幾乎不需要設定。資料庫會儲存在專案資料夾中的 db.sqlite3 檔案。

Azure 提供了資料庫服務，可從 Django 應用程式輕鬆使用。從 Django 應用程式使用 [SQL Database](../web-sites-python-ptvs-django-sql) 和 [MySQL](../web-sites-python-ptvs-django-mysql) 的教學課程，顯示了建立資料庫服務、於  `DjangoWebProject/settings.py` 中變更資料庫設定，以及必須安裝之組件庫的必要步驟。

當然，如果您偏好管理您自己的資料庫伺服器，您可以使用在 Azure 上執行的 Windows 或 Linux 的虛擬機器做到這點。


<h2><a name="django-admin-interface"></a>Django 管理介面</h2>

一旦您開始建置您的模型，您會想要在資料庫內填入一些資料。使用 Django 管理介面可利用互動的方式，輕鬆新增並管理內容。

已取消註解應用程式來源中的管理介面程式碼，但已清楚標示，您可以輕鬆啟用它 (搜尋  'admin')。

啟用後，同步處理資料庫、執行應用程式並瀏覽至  `/admin`。


<h2><a name="next-steps"></a>後續步驟</h2>

請遵循下列連結以深入了解 Django 和 Python Tools for Visual Studio： 
 
- [Django 文件][]
- [Python Tools for Visual Studio 文件][] 

如需使用 SQL Database 和 MySQL 的資訊：

- [Azure 上採用 Python Tools 2.1 for Visual Studio 的 Django 和 SQL Database][]
- [Azure 上採用 Python Tools 2.1 for Visual Studio 的 Django 和 MySQL][]


<!--Link references-->
[Azure 上採用 Python Tools 2.1 for Visual Studio 的 Django 和 MySQL]: ../web-sites-python-ptvs-django-mysql
[Azure 上採用 Python Tools 2.1 for Visual Studio 的 Django 和 SQL Database]: ../web-sites-python-ptvs-django-sql

<!--External Link references-->
[Python Tools for Visual Studio 文件]: http://pytools.codeplex.com/documentation 
[Django 文件]: https://www.djangoproject.com/



<!--HONumber=42-->
