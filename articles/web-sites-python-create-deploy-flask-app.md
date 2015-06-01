<properties 
	pageTitle="採用 Flask 的 Python 網站 - Azure 教學課程" 
	description="介紹在 Azure 上執行 Python 網站的教學課程。" 
	services="app-service\web" 
	documentationCenter="python" 
	tags="python"
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




# 使用 Flask 建立網站

本教學課程說明如何開始在 Azure 網站上執行 Python。Azure 網站提供有限的免費主控和快速的部署，而現在您可以使用 Python！隨著應用程式規模增加，您可以切換為付費主控，也可以與其他所有 Azure 服務整合。

您將建立使用 Flask Web 架構的應用程式 (請參閱本教學課程適用於 [Django](web-sites-python-create-deploy-django-app.md) 與 [Bottle](web-sites-python-create-deploy-bottle-app.md) 的其他版本)。您會從 Azure 組件庫建立網站、設定 Git 部署，並於本機複製儲存機制。然後您會在本機執行應用程式、進行變更、認可和推送至 Azure。本教學課程示範如何從 Windows 或 Mac/Linux 執行這項操作。

[AZURE.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]


## 必要條件

- Windows、Mac 或 Linux
- Python 2.7 或 3.4
- setuptools、pip、virtualenv (僅 Python 2.7)
- Git
- [Python Tools 2.1 for Visual Studio][] (選擇性)

**注意**：Python 專案目前不支援 TFS 發行。

### Windows

如果您還沒有安裝 Python 2.7 或 3.4 (32 位元)，建議您使用 Web Platform Installer 安裝 [Azure SDK for Python 2.7][] 或 [Azure SDK for Python 3.4][]。這會安裝 32 位元版本的 Python、setuptools、pip、virtualenv 等 (32 位元 Python 安裝於 Azure 主機電腦上)。或者，您可以從 [python.org][] 取得 Python。

針對 Git，建議您安裝 [Git for Windows][] 或 [GitHub for Windows][]。如果您使用 Visual Studio，您可以使用整合式的 Git 支援。

我們也建議您安裝 [Python Tools 2.1 for Visual Studio][]。這是選擇性的，但如果您有 [Visual Studio][] (包含免費的 Visual Studio Community 2013 或 Visual Studio Express 2013 for Web)，它會提供您絕佳的 Python IDE。

### Mac/Linux

您應該已經安裝 Python 與 Git，但請確定您擁有的是 Python 2.7 或 3.4。


## 在入口網站上建立網站

建立應用程式的第一步是透過 Azure 管理入口網站建立網站。若要建立網站，您需要登入入口網站並按一下左下角的 [新增] 按鈕。隨即會出現一個視窗。依序按一下 [計算]、[網站] 然後 [從組件庫]。

![](./media/web-sites-python-create-deploy-flask-app/portal-create-site.png)

會出現一個視窗，列出組件庫中可用的應用程式。按一下左側的 [應用程式架構] 類別，然後選取 [Flask]。

![](./media/web-sites-python-create-deploy-flask-app/portal-gallery-flask.png)

在下一頁中，輸入您網站的名稱和地區，然後按一下 [完成] 按鈕。

系統隨即會迅速地設定網站。您可以按一下底部工具列中的 [瀏覽]按鈕，您會看到新的 Flask 應用程式在 Azure 上執行。

![](./media/web-sites-python-create-deploy-flask-app/portal-website-flask.png)
 
接下來，您需要新增透過 Git 發行的支援功能。您可以藉由選擇 [設定從原始檔控制進行部署] 來完成操作。

![](./media/web-sites-python-create-deploy-flask-app/portal-site-created.png)

從 [設定部署] 對話方塊中，向下捲動並選取 [本機 Git 儲存機制] 選項。按一下向右箭頭以繼續操作。

![](./media/web-sites-python-create-deploy-flask-app/portal-setup-deployment.png)

設定好 Git 發行後，您會短暫地看到一個頁面，通知您系統正在建立儲存機制。準備就緒時，您會看到關於如何連接的指示。  

![](./media/web-sites-python-create-deploy-flask-app/portal-repo-created.png)

在後續章節中，我們會遵循這些指示。


## 應用程式概觀

### Git 儲存機制內容

以下是您會在初始的 Git 儲存機制中找到的檔案概觀，我們將在下一節中複製。

    \FlaskWebProject__init__.py
    \FlaskWebProject\views.py
    \FlaskWebProject\static\content\
    \FlaskWebProject\static\fonts\
    \FlaskWebProject\static\scripts\
    \FlaskWebProject\templates\about.html
    \FlaskWebProject\templates\contact.html
    \FlaskWebProject\templates\index.html
    \FlaskWebProject\templates\layout.html

應用程式的主要來源。包含 3 個主要的版面配置頁面 (index、about、contact)。靜態內容和指令碼，包含啟動程序、jquery、modernizr 和回應。

    \runserver.py

本機開發伺服器支援。使用此項在本機執行應用程式。

    \FlaskWebProject.pyproj
    \FlaskWebProject.sln

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


## 網站開發 - Windows - 適用於 Visual Studio 的 Python 工具

### 複製儲存機制

首先，使用 Azure 入口網站上提供的 URL 複製儲存機制。

![](./media/web-sites-python-create-deploy-flask-app/ptvs-git-clone.png)

開啟包含在儲存機制根目錄中的方案檔 (.sln)。

![](./media/web-sites-python-create-deploy-flask-app/ptvs-solution-flask.png)

### 建立虛擬環境

現在我們要建立本機開發的虛擬環境。以滑鼠右鍵按一下 [Python 環境]，選取 [加入虛擬環境...]。

- 請確定環境的名稱是  `env`。

- 選取基礎解譯器。請務必使用針對網站選取之相同版本的 Python (在 runtime.txt 或網站組態頁面中)。

- 確定已勾選下載並安裝封裝的選項。

![](./media/web-sites-python-create-deploy-flask-app/ptvs-add-virtual-env-27.png)

按一下 [**建立**]。這會建立虛擬環境，並安裝 requirements.txt 中列出的相依性。

### 使用開發伺服器來執行

按 F5 開始偵錯，並且網頁瀏覽器會自動開啟在本機執行的頁面。

![](./media/web-sites-python-create-deploy-flask-app/windows-browser-flask.png)

您可以在來源中設定中斷點、使用監看式視窗等等。如需各種功能的詳細資訊，請參閱 [PTVS 文件][]。

### 進行變更

現在您可以嘗試對應用程式來源和/或範本進行變更。

測試過您的變更之後，請將它們認可至 Git 儲存機制：

![](./media/web-sites-python-create-deploy-flask-app/ptvs-commit-flask.png)

### 安裝其他封裝

您的應用程式可能會擁有 Python 和 Flask 之外的相依性。

您可以使用 pip 安裝其他封裝。若要安裝封裝，以滑鼠右鍵按一下虛擬環境，然後選取 [安裝 Python 封裝]。

例如，若要安裝 Azure SDK for Python，讓您可存取 Azure 儲存體、服務匯流排和其他 Azure 服務，請輸入  `azure`：

![](./media/web-sites-python-create-deploy-flask-app/ptvs-install-package-dialog.png)

以滑鼠右鍵按一下虛擬環境，然後選取 [產生 requirements.txt] 更新 requirements.txt。

然後，將變更認可到 Git 儲存機制的 requirements.txt。

### 部署至 Azure

若要觸發部署，按一下 [同步] 或 [推送]。同步處理會推送和提取。

![](./media/web-sites-python-create-deploy-flask-app/ptvs-git-push.png)

第一次部署將需要一些時間，因為它會建立虛擬環境、安裝封裝等。

Visual Studio 不會顯示部署進度。如果您想要檢閱輸出，請參閱[疑難排解 - 部署](#troubleshooting-deployment)一節。

瀏覽至 Azure URL，以檢視您的變更。


## 網站開發 - Windows - 命令列

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

### 使用開發伺服器來執行

您可以使用下列命令，在開發伺服器下啟動應用程式：

    env\scripts\python runserver.py

主控台會顯示 URL，以及伺服器接聽的通訊埠：

![](./media/web-sites-python-create-deploy-flask-app/windows-run-local-flask.png)

然後，開啟網頁瀏覽器指向該 URL。

![](./media/web-sites-python-create-deploy-flask-app/windows-browser-flask.png)

### 進行變更

現在您可以嘗試對應用程式來源和/或範本進行變更。

測試過您的變更之後，請將它們認可至 Git 儲存機制：

    git add <modified-file>
    git commit -m "<commit-comment>"

### 安裝其他封裝

您的應用程式可能會擁有 Python 和 Flask 之外的相依性。

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


## 網站開發 - Mac/Linux - 命令列

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

### 使用開發伺服器來執行

您可以使用下列命令，在開發伺服器下啟動應用程式：

    env/bin/python runserver.py

主控台會顯示 URL，以及伺服器接聽的通訊埠：

![](./media/web-sites-python-create-deploy-flask-app/mac-run-local-flask.png)

然後，開啟網頁瀏覽器指向該 URL。

![](./media/web-sites-python-create-deploy-flask-app/mac-browser-flask.png)

### 進行變更

現在您可以嘗試對應用程式來源和/或範本進行變更。

測試過您的變更之後，請將它們認可至 Git 儲存機制：

    git add <modified-file>
    git commit -m "<commit-comment>"

### 安裝其他封裝

您的應用程式可能會擁有 Python 和 Flask 之外的相依性。

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


## Troubleshooting - Deployment

[AZURE.INCLUDE [web-sites-python-troubleshooting-deployment](../includes/web-sites-python-troubleshooting-deployment.md)]


## 疑難排解 - 封裝安裝

[AZURE.INCLUDE [web-sites-python-troubleshooting-package-installation](../includes/web-sites-python-troubleshooting-package-installation.md)]


## 疑難排解 - 虛擬環境

[AZURE.INCLUDE [web-sites-python-troubleshooting-virtual-environment](../includes/web-sites-python-troubleshooting-virtual-environment.md)]


## 後續步驟

請遵循下列連結以深入了解 Flask 和 Python Tools for Visual Studio： 
 
- [Flask 文件][]
- [Python Tools for Visual Studio 文件][] 

如需有關使用 Azure 資料表儲存體和 MongoDB 的資訊：

- [Azure 上採用 Python Tools 2.1 for Visual Studio 的 Flask 和 MongoDB][]
- [Azure 上採用 Python Tools 2.1 for Visual Studio 的 Flask 和 Azure 資料表儲存體][]


<!--Link references-->
[Azure 上採用 Python Tools 2.1 for Visual Studio 的 Flask 和 MongoDB]: web-sites-python-ptvs-flask-table-storage.md
[Azure 上採用 Python Tools 2.1 for Visual Studio 的 Flask 和 Azure 資料表儲存體]: web-sites-python-ptvs-flask-mongodb.md

<!--External Link references-->
[Azure SDK for Python 2.7]: http://go.microsoft.com/fwlink/?linkid=254281
[Azure SDK for Python 3.4]: http://go.microsoft.com/fwlink/?linkid=516990
[python.org]: http://www.python.org/
[Git for Windows]: http://msysgit.github.io/
[GitHub for Windows]: https://windows.github.com/
[適用於 Visual Studio 的 Python 工具]: http://aka.ms/ptvs
[Python Tools 2.1 for Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
[Visual Studio]: http://www.visualstudio.com/
[PTVS 文件]: http://pytools.codeplex.com/documentation
[Python Tools for Visual Studio 文件]: http://pytools.codeplex.com/documentation 
[Flask 文件]: http://flask.pocoo.org/ 


<!--HONumber=52-->