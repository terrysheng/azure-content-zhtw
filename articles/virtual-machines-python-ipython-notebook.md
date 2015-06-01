<properties 
	pageTitle="IPython Notebook - Azure 教學課程" 
	description="本教學課程說明如何使用執行 Linux 或 Windows 的虛擬機器 (VM)，在 Azure 上部署 IPython Notebook。" 
	services="virtual-machines" 
	documentationCenter="python" 
	authors="huguesv" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-multiple" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="02/05/2015" 
	ms.author="huvalo"/>





# Azure 上的 IPython Notebook

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>這個 <a href="http://ipython.org">IPython 專案</a> 提供用於科學運算的工具集合，其中包括強大的互動式殼層、高效能且易於使用的平行庫，以及稱為 IPython Notebook 的 Web 型環境。對於將程式碼執行與即時運算文件的建立相互結合的互動式運算，Notebook 提供工作環境。這些 Notebook 文件可以包含任何文字、數學公式、輸入程式碼、結果、圖形、視訊，以及新型 Web 瀏覽器能夠顯示的其他任何類型的媒體。</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://go.microsoft.com/fwlink/?LinkID=254535&amp;clcid=0x409" target="_blank" class="label">觀看教學課程</a> <a style="background-image: url('/media/devcenter/python/ipy-youtube2.png') !important;" href="http://go.microsoft.com/fwlink/?LinkID=254535&amp;clcid=0x409" target="_blank" class="dev-onpage-video"><span class="icon">播放影片</span></a> <span class="time">上午 05:22</span></div>
</div>

無論您完全不熟悉 Python 而想要在有趣、
互動的環境中學習或執行一些重大的平行/技術運算，
IPython Notebook 都是絕佳的選擇。為了示範其功能，
下列螢幕擷取畫面顯示使用 IPython Notebook 結合
SciPy 和 matplotlib 套件，分析錄音的
結構：

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-notebook-spectral.png)

本文件將說明如何使用 Linux or Windows 虛擬機器 (VM)，將 IPython Notebook 部署於 Microsoft
Azure。在 Azure 使用 IPython
Notebook 即可針對可擴充的運算資源提供可透過 Web 存取的介面，
並發揮 Python 及其眾多
程式庫的功用。由於所有安裝都是在雲端完成，因此使用者可以存取這些
資源而不需要目前網頁瀏覽器之外的任何本機
組態。

[AZURE.INCLUDE [create-account-and-vms-note](../includes/create-account-and-vms-note.md)]

## 在 Azure 上建立並設定 VM

第一步是建立在 Azure 上運作的虛擬機器 (VM)。
此 VM 是雲端中的完整作業系統，它將用來
執行 IPython Notebook。Azure 能夠執行 Linux 和 Windows
虛擬機器，而且我們將討論在這兩種虛擬機器設定 IPython 的過程。

### Linux VM

按照 [此處][portal-vm-linux] 提供的指示建立 *OpenSUSE* 或 *Ubuntu* 散發套件的虛擬機器。本教學課程使用 OpenSUSE 13.2 及 Ubuntu Server 14.04 LTS。我們假設預設使用者名稱是 *azureuser*。

### Windows VM

按照 [此處][portal-vm-windows] 提供的指示建立 *Windows Server 2012 R2 Datacenter* 散發套件的虛擬機器。在本教學課程中，我們假設使用者名稱是 *azureuser*。

## 建立 IPython Notebook 的端點

此步驟適用於 Linux 和 Windows VM 兩者。稍後我們將設定
IPython 在連接埠 9999 上運作其 Notebook 伺服器。若要對外開放此連接埠，
必須在 Azure 管理入口網站建立端點。此
端點會開啟 Azure 防火牆中的連接埠，並且將公用連接埠 (HTTPS，
443) 對應至 VM 的私人連接埠 (9999)。

若要建立端點，請移至 VM 儀表板，並按一下 [端點]，然後按一下 [新增
端點] 並建立新的端點 (在此範例中稱為 `ipython_nb`)。選擇
TCP 通訊協定、公用連接埠 443 和私人連接埠 9999：

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-azure-linux-005.png)

在此步驟後，[端點] 儀表板索引標籤將如下所示：

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-azure-linux-006.png)

## 在 VM 上安裝必要軟體

若要在我們的 VM 上執行 IPython Notebook，必須先安裝 IPython 及
其相依性。

### Linux (OpenSUSE)

若要安裝 IPython 及其相依性，請透過 SSH 進入 Linux VM 並執行 
以下步驟。

安裝 [NumPy][numpy]、[Matplotlib][matplotlib]、[Tornado][tornado] 以及 IPython 的其他相依性，方法是：

    sudo zypper install python-matplotlib
    sudo zypper install python-tornado
    sudo zypper install python-jinja2
    sudo zypper install ipython

### Linux (Ubuntu)

若要安裝 IPython 及其相依性，請透過 SSH 進入 Linux VM 並執行 
以下步驟。

首先，擷取新的套件清單：

    sudo apt-get update

安裝 [NumPy][numpy]、[Matplotlib][matplotlib]、[Tornado][tornado] 以及 IPython 的其他相依性，方法是：

    sudo apt-get install python-matplotlib
    sudo apt-get install python-tornado
    sudo apt-get install ipython
    sudo apt-get install ipython-notebook

### Windows

若要在 Windows VM 上安裝 IPython 及其相依性，請透過遠端桌面連線至 VM。然後執行下列步驟， 
使用 Windows PowerShell 執行所有命令列動作。

**注意**：為了使用 Internet Explorer 下載任何項目，您需要變更部分安全設定。從 [**伺服器管理員**] 中，按一下 [**本機伺服器**]，然後在 [**IE 增強式安全性設定**] 中為管理員將它關閉。安裝 IPython 完成之後，您即可再次啟用它。

1.  下載並安裝最新 32 位元版本的 [Python 2.7][]。您需要將 `C:\Python27` 和 `C:\Python27\Scripts` 新增到您的 `PATH` 環境變數。

1.  安裝 [Tornado][tornado] 和 [PyZMQ][pyzmq] 以及 IPython 的其他相依性，方法是：

        easy_install tornado
        easy_install pyzmq
        easy_install jinja2
        easy_install six
        easy_install python-dateutil
        easy_install pyparsing

1.  使用可在網站上取得的`.exe` 二進位安裝程式，下載並安裝 [NumPy][numpy]。截至撰寫時為止，最新版為 **numpy-1.9.1-win32-superpack-python2.7.exe**。

1.  安裝 [Matplotlib][matplotlib]，方法是：

        pip install matplotlib==1.4.2

1.  下載並安裝 [OpenSSL][]。

	* 您將在同一個下載頁面發現所需的 **Visual C++ 2008 可轉散發套件**。

	* 您必須將 `C:\OpenSSL-Win32\bin` 加入 `PATH` 環境變數中。

	> [AZURE.NOTE] 安裝 OpenSSL 時，請使用 1.0.1g 或更新版，因為其中包含對 Heartbleed 安全性弱點的修正。

1.  使用以下命令下載 IPython：

        pip install ipython==2.4

1.  開啟 Windows 防火牆中的連接埠。在 Windows Server 2012 上，防火牆預設會阻擋傳入的連線。若要開啟連接埠 9999，請依照下列步驟執行：

    - 從 [開始] 畫面啟動 [**具有進階安全性的 Windows 防火牆**]。

    - 在左側窗格中，按一下 [**輸入規則**]。

	- 在 [動作] 窗格中，按一下 [**新增規則...**]。

	- 在 [新增輸入規則精靈] 中，選取 [**連接埠**]。

	- 在下一個畫面中，選取 [**TCP**]，並且在 [**特定本機連接埠**] 中輸入 **9999**。

	- 接受預設值，並且將規則命名，然後按一下 [完成]。

### 設定 IPython Notebook

接著，我們將設定 IPython Notebook第一個步驟是建立自訂
IPython 組態設定檔來封裝組態資訊：

    ipython profile create nbserver

然後，透過 `cd` 切換到設定檔目錄，以建立 SSL 憑證並加以編輯
設定檔組態檔案。

在 Linux 上：

    cd ~/.ipython/profile_nbserver/

在 Windows 上：

    cd \users\azureuser.ipython\profile_nbserver

建立 SSL 憑證，如下所示 (Linux 和 Windows)：

    openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem

請注意，由於我們要建立自我簽署的 SSL 憑證，因此連接
到 Notebook 時，瀏覽器將發出安全性警告。若要進行長期
生產使用，您將需要使用與您的組織相關聯的
正確簽署憑證。由於憑證管理不在
此示範的範圍內，因此我們仍採用自我簽署的憑證。

除了使用憑證之外，您也必須提供密碼，以防
未經授權而使用您的 Notebook。基於安全性考量，IPython 使用
其組態檔中的加密密碼，因此您需要
將密碼加密。IPython 提供一個公用程式來進行此作業；在命令提示字元下，執行：

    python -c "import IPython;print IPython.lib.passwd()"

這將提示您提供密碼並確認，然後將輸出
密碼，如下所示：

    Enter password: 
    Verify password: 
    sha1:b86e933199ad:a02e9592e59723da722.. (elided the rest for security)
    
接下來，我們將編輯設定檔的組態檔，也就是
您所在設定檔目錄中的 `ipython_notebook_config.py` 檔案。請注意，這個檔案可能不存在，若是如此，請直接建立該檔案。此
檔案有許多欄位，預設為注釋排除所有欄位。您可以
使用您偏好的任何文字編輯器開啟這個檔案，而且您應該確定該檔案
至少有以下內容：

    c = get_config()
    
    # This starts plotting support always with matplotlib
    c.IPKernelApp.pylab = 'inline'
    
    # You must give the path to the certificate file.
    
    # If using a Linux VM:
    c.NotebookApp.certfile = u'/home/azureuser/.ipython/profile_nbserver/mycert.pem'
    
    # And if using a Windows VM:
    c.NotebookApp.certfile = r'C:\Users\azureuser.ipython\profile_nbserver\mycert.pem'
    
    # Create your own password as indicated above
    c.NotebookApp.password = u'sha1:b86e933199ad:a02e9592e5 etc... '
    
    # Network and browser details. We use a fixed port (9999) so it matches
    # our Azure setup, where we've allowed traffic on that port
    
    c.NotebookApp.ip = '*'
    c.NotebookApp.port = 9999
    c.NotebookApp.open_browser = False

### 執行 IPython Notebook

此時，我們已準備啟動 IPython Notebook。若要這樣做，
請瀏覽至要儲存 Notebook 的目錄，並啟動
IPython Notebook 伺服器：

    ipython notebook --profile=nbserver

您現在應該可以在位址
`https://[您選擇的名稱].cloudapp.net` 存取您的 IPython Notebook。

您第一次存取 Notebook 時，登入頁面會詢問您的密碼：

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-notebook-001.png)

登入後，您會看見「Python Notebook 儀表板」，它是
所有 Notebook 作業的控制中心。從這個頁面，您可以建立
新的 Notebook、開啟現有的 Notebook 等等：

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-notebook-002.png)

如果按一下 [新增筆記本] 按鈕，您將看到如下所示的開啟
頁面：

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-notebook-003.png)

標記 `In []:` 提示的區域便是輸入區域，您可以在這裡
輸入任何有效的 Python 程式碼，當您按下 `Shift-Enter` 或
按一下 [播放] 圖示 (工具列中的向右三角形) 時，程式碼將執行。

由於我們已設定 Notebook 自動先啟動 NumPy 和 matplotlib 支援，
因此您可以產生圖形，例如：

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-notebook-004.png)

## 強大的典範：具有多樣化媒體的即時運算文件

Notebook 本身應該讓曾經使用 Python 和
文書處理器的任何人感覺相當自然，因為它在某些方面是兩者的混合：您可以執行
Python 程式碼區塊，但是您也可以保留記事及其他文字，方法是
使用工具列中的下拉式功能表，將儲存格樣式「程式碼」變更為「Markdown」
：

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-notebook-005.png)


不過，這並非單純的文書處理器，因為 IPython Notebook 能夠
運算和處理多媒體 (文字、圖形、視訊和
現今網頁瀏覽器可以顯示的任何內容)。例如，您可以混合
說明影片和運算用於教學：

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-notebook-006.png)

也可以在 Notebook 檔案中嵌入始終有效且可用的外部網站
：

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-notebook-007.png)

而且，Python 的許多絕佳程式庫適用於科學和
技術運算，能夠輕鬆執行簡單的計算，
輕鬆程度和複雜的網路分析一樣，完全在一個環境中進行：

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-notebook-008.png)

現今網站功能與即時運算相互結合的這個典範
展現許多可能性，相當適用於雲端；Notebook
能夠

* 作為運算記錄器，用來記錄對於問題進行的探究工作，

* 以 'live' 運算形式或
  檔案格式 (HTML、PDF) 與同事分享結果，

* 用於散佈和顯示關於運算的即時教學材料，
  以便學生能夠立即試驗實際程式碼、修改程式碼，並且
  以互動方式重新執行程式碼，

* 提供「可執行文件」，透過
  可供其他人立即重現、驗證和延伸的方式呈現研究成果，

* 以作為共同運算的平台：多位使用者可以登入
  同一個 Notebook 伺服器共用即時運算工作階段，

* 不勝枚舉...

如果您進入 IPython 原始程式碼 [儲存機制][]，您將發現
包含 Notebook 範例的整個目錄，您可以下載這些範例並使用您自己的 Azure IPython VM 進行試驗。只需從網站下載 `.ipynb` 檔案，並將這些檔案上傳至您的 Notebook Azure VM 儀表板即可 (也可以將這些檔案直接下載至 VM)。

## 結論

IPython Notebook 提供功能強大的介面，可透過互動方式展現
Python 生態系統對於 Azure 所具備的功能。其中涵蓋廣泛的
使用案例，包括簡單的探究和學習 Python、資料分析和
視覺化、模擬和平行運算。產生的 Notebook
文件包含執行計算的完整記錄，
能夠與其他 IPython 使用者共用。IPython Notebook 可以作為
本機應用程式，不過最適合 Azure 的雲端部署

例外，也可以透過 
[Python Tools for Visual Studio][] (PTVS) 在 Visual Studio 中使用 IPython 的核心功能。PTVS 是免費的開放原始碼外掛程式， 
由 Microsoft 提供，可以將 Visual Studio 轉變為進階的 Python 開發 
環境，其中包含的進階編輯器具有 IntelliSense、偵錯、 
程式碼剖析和平行運算整合。



[tornado]:      http://www.tornadoweb.org/          "Tornado"
[PyZMQ]:        https://github.com/zeromq/pyzmq     "PyZMQ"
[NumPy]:        http://www.numpy.org/               "NumPy"
[Matplotlib]:   http://matplotlib.sourceforge.net/  "Matplotlib"

[portal-vm-windows]: /manage/windows/tutorials/virtual-machine-from-gallery/
[portal-vm-linux]: /manage/linux/tutorials/virtual-machine-from-gallery/

[儲存機制]: https://github.com/ipython/ipython
[Python Tools for Visual Studio]: http://aka.ms/ptvs

[Python 2.7]: http://www.python.org/download
[OpenSSL]: http://slproweb.com/products/Win32OpenSSL.html

<!--HONumber=47-->
