<properties linkid="develop-python-install-python" urlDisplayName="Install Python" pageTitle="Install Python and the SDK - Azure" metaKeywords="Azure Python SDK" description="Learn how to install Python and the SDK to use with Azure." metaCanonical="" services="" documentationCenter="Python" title="Installing Python and the SDK" authors="" solutions="" manager="" editor="" />

安裝 Python 和 SDK
==================

您可以在 Windows 上輕易地設定 Python；此外，它也預先安裝於 Mac 和 Linux 中。本指南將逐步引導您完成安裝作業，並讓機器做好搭配 Azure 的準備。本指南將提供以下事項的協助：

-   Python Azure SDK 含有哪些內容？
-   該使用哪個 Python 和哪個版本
-   在 Windows 上進行安裝
-   在 Mac 和 Linux 上進行安裝

Python Azure SDK 含有哪些內容？
-------------------------------

Azure SDK for Python 內含的元件可讓您開發、部署及管理適用於 Azure 的 Python 應用程式。尤其是 Azure SDK for Python 包含下列各項：

-   **適用於 Azure 的 Python 用戶端程式庫**。這些類別庫所提供的介面可供存取 Azure 功能，例如資料管理服務和雲端服務。
-   **適用於 Mac 和 Linux 的 Azure 命令列工具**。這是一組命令列工具，可用於部署和管理 Azure 服務，例如 Azure 網站和 Azure 虛擬機器。這些工具可在任何平台上運作，包括 Mac、Linux 和 Windows。
-   **適用於 Azure 的 PowerShell (僅限 Windows)**。這是一組 PowerShell Cmdlet，可用於部署和管理 Azure 服務，例如雲端服務和虛擬機器。
-   **Azure 模擬器 (僅限 Windows)**。計算和儲存體模擬器都是雲端服務和資料管理服務的本機模擬器，可讓您在本機測試應用程式。Azure 模擬器只能在 Windows 上執行。

此版本的核心案例包括：

-   **Windows**：雲端服務 -- 如使用 Webroles 的 Django 網站
-   **Mac/Linux**：IaaS -- 在 VM 中執行所需的項目；透過 Python 取用 Azure 服務

該使用哪個 Python 和哪個版本
----------------------------

可用的 Python 解譯器有數種，範例包括：

-   CPython - 標準和最常見的 Python 解譯器
-   IronPython - 可在 .Net/CLR 上運作的 Python 解譯器
-   Jython - 可在 JVM 上運作的 Python 解譯器

基於本教學課程的目的，我們只會測試及支援 **CPython**。我們也建議您採用 2.7 以上的版本。此外，我們即將在近期內加入 **IronPython** 支援。

可在哪裡取得 Python？
---------------------

取得 CPython 的方法有數種：

-   直接從 www.python.org 取得
-   從聲譽良好的散發網站取得，如 www.enthought.com 或 www.ActiveState.com
-   從原始碼組建！

除非您有特定的需求，否則我們建議您採用前兩個選項，如下文所述。

在 Windows 上進行安裝
---------------------

對於 Windows，您可以使用隨附的 [WebPI 安裝程式](http://go.microsoft.com/fwlink/?LinkId=254281&clcid=0x409)，從主要的 Python 開發人員中心進行簡化安裝 (安裝程式會從 www.python.org 取得 CPython)。

**注意：**在 Windows Server 上，若要下載 WebPI 安裝程式，您可能需要設定 IE ESC 設定 ([開始]/[系統管理工具]/[伺服器管理員]，然後按一下 **[設定 IE ESC]**，將其設定為 [關閉])

![how-to-install-python-webpi-1](./media/python-how-to-install/how-to-install-python-webpi-1.png)

WebPI 安裝程式提供解譯 Azure 應用程式所需的任何項目，以及特定的 Django 應用程式支援：

![how-to-install-Python-webpi-2](./media/python-how-to-install/how-to-install-python-webpi-2.png)

完成後，您應該可以看見這個確認安裝選擇的畫面：

![how-to-install-python-webpi-3](./media/python-how-to-install/how-to-install-python-webpi-3.png)

安裝完成後，請在提示字元中輸入 python 以確認安裝作業順利完成。由於安裝方法不盡相同，您可能需要設定 "path" 變數才能找到 (正確版本的) Python：

![how-to-install-python-win-run](./media/python-how-to-install/how-to-install-python-win-run.png)

由於此版本主要著重於 Django Web 應用程式，因此您可以隨時瀏覽 [Python 套件索引 (PyPI)](http://pypi.python.org/pypi) (英文) 以選擇其他種類豐富的軟體。如果您選擇安裝散發版本，便擁有從 Web 開發到工程運算等多樣化案例中令人感興趣的主要部分。

若要查看安裝於 **site-packages** 之 Python 套件含有哪些項目，請輸入以下內容以尋找其位置：

![how-to-install-python-win-site](./media/python-how-to-install/how-to-install-python-win-site.png)

如此能以清單顯示安裝於系統中的內容。

安裝完成後，您應該可以在預設位置取用 Python、Django、用戶端程式庫：

     C:\Python27\Lib\site-packages\windowsazure
        C:\Python27\Lib\site-packages\django

### Python Tools for Visual Studio

Python Tools for Visual Studio 是 Microsoft 提供的免費/OSS 外掛程式，它能將 VS 轉變為成熟的 Python IDE：

![how-to-install-python-ptvs](./media/python-how-to-install/how-to-install-python-ptvs.png)

您可以選擇是否要使用 Python Tools for Visual Studio，不過我們建議您使用，因為它能提供 Python 和 Django 專案/方案支援、偵錯、程式碼剖析、範本編輯和 IntelliSense、部署至雲端等。此增益集適用於現有的 VS2010 安裝。如果您沒有 VS2010，WebPI 會安裝免費的 Integrated Shell + PTVS，它們能提供**完全免費**的 "VS Python Express" IDE。如需詳細資訊，請參閱 [CodePlex 上的 Python Tools for Visual Studio](http://pytools.codeplex.com) (英文)。

注意：雖然 PTVS 外掛程式很小，不過 Integrated Shell 會增加您的下載時間。目前的 Integrated Shell 版本不支援「新增 Azure 部署專案」功能。

Windows 解除安裝
----------------

就傳統上來說，**Azure SDK for Python June 2012** WebPI 產品並不是應用程式，而是將多個不同產品 (如 32 位元 Python 2.7、適用於 Python 的 Azure 用戶端 API、Django 等) 一起搭售的集合。這樣的做法導致 WebPI 本身沒有傳統的解除安裝程式，因此您需要從 Windows 控制台分別移除 WebPI 安裝的程式。

如果您想要重新安裝 **Azure SDK for Python**，只要以系統管理員身分開啟 PowerShell 命令提示字元，然後再執行以下命令即可：

    rm -force "HKLM:\SOFTWARE\Microsoft\Windows Azure SDK for Python - June 2012"

接著再重新執行 WebPI。

在 Linux 和 MacOS 上進行安裝
----------------------------

您的 Dev 機器很可能已安裝 Python。您可以輸入以下內容來加以查看：

![how-to-install-python-linux-run](./media/python-how-to-install/how-to-install-python-linux-run.png)

我們可以看見，Azure Suse VM 已安裝 CPython 2.7.2，該版本可用來執行 Azure 教學課程和 Django 範例。如果您想要升級，請遵循 OS 的建議套件升級指示。然而請注意，在一般情況下，建議您最好保留系統 Python (因為其他版本可能需要仰賴該版本) 並透過 [Virtualenv](http://pypi.python.org/pypi/virtualenv) 安裝新版本。

若要安裝 Python Azure 用戶端程式庫，請使用 **pip** 從 **PyPI** 取得：

    curl https://raw.github.com/pypa/pip/master/contrib/get-pip.py | sudo python

上述命令會以無訊息的方式提示您輸入根密碼。請輸入密碼並按 Enter。接著：

    sudo /usr/local/bin/pip-2.7 install azure

您現在應該能看見用戶端程式庫已安裝在 **site-packages** 下。在 MacOS 上：

![MacOS site-packages](./media/python-how-to-install/how-to-install-python-mac-site.png)

在 Mac/Linux 上進行開發時，此版本支援兩個主要案例：

1.  使用 Python 的用戶端程式庫取用 Azure 服務

2.  在 Linux VM 上執行應用程式

第一個案例可讓您透過適用於 Azure REST API 的 Pythonic 包裝函式撰寫運用 Azure PaaS 功能 (如 Blob 儲存體、佇列等) 之優勢且內容豐富的 Web 應用程式。這些功用在 Windows、Mac 及 Linux 上均相同。如需範例，請參閱教學課程和作法指南。您也可以在 Linux VM 內部使用這些用戶端程式庫。

對於 VM 案例，您只需要啟動選擇的 Linux VM (Ubuntu、CentOS、Suse)，便能執行/管理所需的項目。例如，您可以在 Windows/Mac/Linux 機器上執行 [IPython](http://ipython.org) REPL/notebook，然後將瀏覽器指向在 Azure 上執行 IPython Engine 的 Linux 或 Windows 多重處理器 VM。如需 IPython 安裝的詳細資訊，請參閱 IPython 教學課程。

如需設定 Linux VM 的相關資訊，請參閱 [Linux 管理小節](/en-us/manage/linux/)。

其他軟體和資源：
----------------

-   [Enthought Python 散發 (英文)](http://www.enthought.com)
-   [ActiveState Python 散發 (英文)](http://www.activestate.com)
-   [SciPy - Scientific Python 程式庫套件 (英文)](http://www.scipy.org)
-   [NumPy - Python 的數值程式庫 (英文)](http://www.numpy.org)
-   [Django 專案 - 成熟的 Web 架構/CMS (英文)](http://www.djangoproject.com)
-   [IPython - 先進的 Python REPL/Notebook (英文)](http://ipython.org)
-   [Azure 上的 IPython Notebook (英文)](http://windowsazure.com/zh-tw/documentation/articles/virtual-machines-python-ipython-notebook)
-   [CodePlex 上的 Python Tools for Visual Studio (英文)](http://pytools.codeplex.com)
-   [Virtualenv (英文)](http://pypi.python.org/pypi/virtualenv)

