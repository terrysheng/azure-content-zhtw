<properties urlDisplayName="Install Python" pageTitle="安裝 Python 和 SDK - Azure" metaKeywords="Azure Python SDK" description="了解如何安裝 Python 和 SDK 用於 Azure。" metaCanonical="" services="" documentationCenter="Python" title="Installing Python and the SDK" authors="huvalo" solutions="" manager="wpickett" editor="" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="11/10/2014" ms.author="huvalo" />




# 安裝 Python 和 SDK
您可以在 Windows 上輕易地設定 Python；此外，它也預先安裝於 Mac 和 Linux 中。本指南將逐步引導您完成安裝作業，並讓機器做好搭配 Azure 的準備。本指南將提供以下事項的協助：

* Python Azure SDK 含有哪些內容？
* 該使用哪個 Python 和哪個版本
* 在 Windows 上進行安裝
* 在 Mac 和 Linux 上進行安裝

## Python Azure SDK 含有哪些內容？

Azure SDK for Python 內含的元件可讓您開發、部署及管理適用於 Azure 的 Python 應用程式。尤其是 Azure SDK for Python 包含下列各項：

* **適用於 Azure 的 Python 用戶端程式庫**。這些類別庫所提供的介面可供存取 Azure 功能，例如資料管理服務和雲端服務。  
* **Azure 模擬器 (僅限 Windows)**。計算和儲存體模擬器都是雲端服務和資料管理服務的本機模擬器，可讓您在本機測試應用程式。Azure 模擬器只能在 Windows 上執行。


此版本的核心案例包括：

* **Windows**：雲端服務，網站 -- 例如，使用 Web 角色的 Django 網站
* **Mac/Linux**：IaaS -- 在 VM 中執行所需的項目；透過 Python 取用 Azure 服務

## 該使用哪個 Python 和哪個版本

可用的 Python 解譯器有數種，範例包括：

* CPython - 標準和最常見的 Python 解譯器
* IronPython - 可在 .Net/CLR 上運作的 Python 解譯器
* Jython - 可在 JVM 上運作的 Python 解譯器

基於本教學課程的目的，我們只會測試及支援 **CPython**。我們建議的版本為 2.7 或 3.4。

## 可在哪裡取得 Python？

取得 CPython 的方法有數種：

* 直接從 www.python.org 取得
* 從聲譽良好的散發網站取得，例如 www.enthought.com、www.ActiveState.com、www.continuum.io
* 從原始碼組建！

除非您有特定的需求，否則我們建議您採用前兩個選項，如下文所述。

## 在 Windows 上進行安裝

對於 Windows，您可以使用 Web Platform Installer 加速安裝。這些包括 www.python.org 提供的 CPython。

* [Microsoft Azure SDK for Python 2.7][]
* [Microsoft Azure SDK for Python 3.4][]

**注意：**在 Windows Server 上，若要下載 WebPI 安裝程式，您可能需要設定 IE ESC 設定 ([開始]/[系統管理工具]/[伺服器管理員]/[本機伺服器]，然後按一下 [**IE 增強式安全性設定**]，將其設定為 [關閉])


### Python 2.7

WebPI 安裝程式提供開發 Python Azure 應用程式所需的任何項目。

![how-to-install-python-webpi-27-1](./media/python-how-to-install/how-to-install-python-webpi-27-1.png)

完成後，您應該可以看見這個確認安裝選擇的畫面：

![how-to-install-python-webpi-27-2](./media/python-how-to-install/how-to-install-python-webpi-27-2.png)

安裝完成後，請在提示字元中輸入 python 以確認安裝作業順利完成。由於安裝方法不盡相同，您可能需要設定 "path" 變數才能找到 (正確版本的) Python：

![how-to-install-python-win-run-27](./media/python-how-to-install/how-to-install-python-win-run-27.png)

安裝完成後，您應該可以在預設位置取用 Python 和用戶端程式庫：

		C:\Python27\Lib\site-packages\azure


### Python 3.4

WebPI 安裝程式提供開發 Python Azure 應用程式所需的任何項目。

![how-to-install-python-webpi-34-1](./media/python-how-to-install/how-to-install-python-webpi-34-1.png)
 
完成後，您應該可以看見這個確認安裝選擇的畫面：

![how-to-install-python-webpi-34-2](./media/python-how-to-install/how-to-install-python-webpi-34-2.png)

安裝完成後，請在提示字元中輸入 python 以確認安裝作業順利完成。由於安裝方法不盡相同，您可能需要設定 "path" 變數才能找到 (正確版本的) Python：

![how-to-install-python-win-run-34](./media/python-how-to-install/how-to-install-python-win-run-34.png)

安裝完成後，您應該可以在預設位置取用 Python 和用戶端程式庫：

		C:\Python34\Lib\site-packages\azure


### 取得更多封裝

由於此版本主要著重於 Web 應用程式，因此您可以隨時瀏覽 [Python 封裝索引 (PyPI)][] (英文) 來選擇其他種類豐富的軟體。如果您選擇安裝散發版本，便擁有從 Web 開發到工程運算等多樣化案例中令人感興趣的主要部分。


### Python Tools for Visual Studio

Python Tools for Visual Studio (PTVS) 是 Microsoft 提供的免費/OSS 外掛程式，它能將 VS 轉變為成熟的 Python IDE：

![how-to-install-python-ptvs](./media/python-how-to-install/how-to-install-python-ptvs.png)

您可以選擇是否要使用 PTVS，不過我們建議您使用，因為它能提供 Python 和 Django 專案/方案支援、偵錯、程式碼剖析、互動式視窗、範本編輯和 IntelliSense。

PTVS 也能讓您使用[部署至雲端服務和網站][]的支援，輕鬆部署至 Microsoft Azure。

PTVS 可以和您現有的 Visual Studio 2010、2012 或 2013 安裝一同運作。如需詳細資訊並進行下載，請參閱 [CodePlex 上的 Python Tools for Visual Studio][] (英文)。  

## Windows 解除安裝

就傳統上來說，**Azure SDK for Python** WebPI 產品並不是應用程式，而是將多個不同產品 (如 32 位元的 Python 2.7/3.4、適用於 Python 的 Azure 用戶端程式庫等) 一起搭售的集合。這樣的做法導致 WebPI 本身沒有傳統的解除安裝程式，因此您需要從 Windows 控制台分別移除 WebPI 安裝的程式。  

如果您想要重新安裝 **Azure SDK for Python**，只要以系統管理員身分開啟 PowerShell 命令提示字元，然後再執行以下命令即可：

	rm -force "HKLM:\SOFTWARE\Microsoft\Python Tools for Azure"

接著再重新執行 WebPI。

## 在 Linux 和 MacOS 上進行安裝

您的 Dev 機器很可能已安裝 Python。您可以輸入以下內容來加以查看：

![how-to-install-python-linux-run](./media/python-how-to-install/how-to-install-python-linux-run.png)

我們現在看到這個在 Azure 上執行的 Ubuntu Server 14.04 LTS VM 已安裝 CPython 2.7.6。如果您想要升級，請遵循 OS 的建議套件升級指示。

若要安裝 Python Azure 用戶端程式庫，請使用 **pip** 從 **PyPI** 取得：

	curl https://bootstrap.pypa.io/get-pip.py | sudo python
	
上述命令會以無訊息的方式提示您輸入根密碼。請輸入密碼並按 Enter。下一步：
	
	sudo pip install azure

您現在應該能看見用戶端程式庫已安裝在 **site-packages** 下。在 MacOS 上：

![how-to-install-python-mac-site](./media/python-how-to-install/how-to-install-python-mac-site.png)

在 Mac/Linux 上進行開發時，支援兩個主要案例：

1. 使用 Python 的用戶端程式庫取用 Azure 服務

2. 在 Linux VM 上執行應用程式

第一個案例可讓您透過適用於 Azure REST API 的 Pythonic 包裝函式撰寫運用 Azure PaaS 功能 (如 Blob 儲存體、佇列等) 之優勢且內容豐富的 Web 應用程式。這些功用在 Windows、Mac 及 Linux 上均相同。如需範例，請參閱教學課程和作法指南。您也可以在 Linux VM 內部使用這些用戶端程式庫。

對於 VM 案例，您只需要啟動選擇的 Linux VM (Ubuntu、CentOS、Suse)，便能執行/管理所需的項目。例如，您可以在 Windows/Mac/Linux 機器上執行 [IPython](http://ipython.org) REPL/notebook，然後將瀏覽器指向在 Azure 上執行 IPython Engine 的 Linux 或 Windows 多重處理器 VM。如需 IPython 安裝的詳細資訊，請參閱 IPython 教學課程。

如需設定 Linux VM 的相關資訊，請參閱 [Linux 管理小節。](/zh-tw/manage/linux/)

## 其他軟體和資源：

* [Enthought Python 散發 (英文)][]
* [ActiveState Python 散發 (英文)][]
* [SciPy - Scientific Python 程式庫套件 (英文)][]
* [NumPy - Python 的數值程式庫 (英文)][]
* [Django 專案 - 成熟的 Web 架構/CMS (英文)][]
* [IPython - 先進的 Python REPL/Notebook (英文)][]
* [Azure 上的 IPython Notebook][]
* [CodePlex 上的 Python Tools for Visual Studio (英文)][]
* [Virtualenv (英文)][]


[Enthought Python 散發 (英文)]: http://www.enthought.com 
[ActiveState Python 散發 (英文)]: http://www.activestate.com
[SciPy - Scientific Python 程式庫套件 (英文)]: http://www.scipy.org
[NumPy - Python 的數值程式庫 (英文)]: http://www.numpy.org
[Django 專案 - 成熟的 Web 架構/CMS (英文)]: http://www.djangoproject.com 
[IPython - 先進的 Python REPL/Notebook (英文)]: http://ipython.org
[Azure 上的 IPython Notebook]: /zh-tw/documentation/articles/virtual-machines-python-ipython-notebook
[部署至雲端服務和網站]: /zh-tw/documentation/articles/cloud-services-web-sites-python-django-app-with-ptvs/
[CodePlex 上的 Python Tools for Visual Studio (英文)]: http://pytools.codeplex.com 
[Python 封裝索引 (PyPI)]: http://pypi.python.org/pypi
[Virtualenv (英文)]: http://pypi.python.org/pypi/virtualenv 
[Microsoft Azure SDK for Python 2.7]: http://go.microsoft.com/fwlink/?LinkId=254281&clcid=0x409
[Microsoft Azure SDK for Python 3.4]: http://go.microsoft.com/fwlink/?LinkID=516990&clcid=0x409
[透過 Azure 入口網站設定 Linux VM]: ../../../shared/chunks/create-and-configure-opensuse-vm-in-portal
[如何使用適用於 Mac 和 Linux 的 Azure 命令列工具]: ../../shared/chunks/crossplat-cmd-tools

