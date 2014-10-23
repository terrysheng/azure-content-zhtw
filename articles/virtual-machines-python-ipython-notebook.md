<properties linkid="develop-python-ipython-notebook" urlDisplayName="IPython Notebook" pageTitle="IPython Notebook - Azure tutorial" metaKeywords="" description="A tutorial that shows how to deploy the IPython Notebook on Azure, using Linux or Windows virtual machines (VMs)." metaCanonical="" services="virtual-machines" documentationCenter="Python" title="IPython Notebook on Azure" authors="huvalo" solutions="" manager="" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-multiple" ms.devlang="python" ms.topic="article" ms.date="01/01/1900" ms.author="huvalo"></tags>

# Azure 上的 IPython Notebook

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p><a href="http://ipython.org">IPython 專案</a> 提供用於科學運算的工具集合，其中包括強大的互動式殼層、高效能且易於使用的平行庫，以及稱為 IPython Notebook 的 Web 型環境。對於將程式碼執行與即時運算文件的建立相互結合的互動式運算，Notebook 提供工作環境。這些 Notebook 文件可以包含任何文字、數學公式、輸入程式碼、結果、圖形、視訊，以及新型 Web 瀏覽器能夠顯示的其他任何類型的媒體。</p>
</div>

<div class="dev-onpage-video-wrapper"><a href="http://go.microsoft.com/fwlink/?LinkID=254535&amp;clcid=0x409" target="_blank" class="label">觀看教學課程</a> <a style="background-image: url('/media/devcenter/python/ipy-youtube2.png') !important;" href="http://go.microsoft.com/fwlink/?LinkID=254535&amp;clcid=0x409" target="_blank" class="dev-onpage-video"><span class="icon">播放影片</span></a> <span class="time">5:22</span></div>

</div>

無論您是第一次使用 Python 並希望在有趣的互動式環境中了解它，還是執行一些重要的平行/技術運算，IPython Notebook 都是相當好的選擇。為了說明其中的功能，下列螢幕擷取畫面顯示使用 IPython Notebook搭配 SciPy 和 matplotlib 封裝來分析錄音結構：

![螢幕擷取畫面][]

本文件將說明如何使用 Linux 或 Windows 虛擬機器 (VM)在 Azure 上部署 IPython Notebook。在 Azure 上使用 IPython Notebook 之後，即可對於具備 Python 及其許多程式庫所有功能的可擴充運算資源提供 Web 存取的介面。由於所有安裝都是在雲端進行的，因此使用者只需要最新的網頁瀏覽器即可存取這些資源，完全不需要其他任何本機組態。

[WACOM.INCLUDE [create-account-and-vms-note][]]

## 在 Azure 上建立並設定 VM

第一個步驟是建立在 Azure 上執行的虛擬機器 (VM)。此 VM 是雲端中完整的作業系統，且將用於執行 IPython Notebook。Azure 能夠同時執行 Linux 和 Windows 虛擬機器，我們將介紹如何在這兩種類型的虛擬機器上設定 IPython。

### Linux VM

按照[此處][]提供的指示建立 *OpenSUSE* 或 *Ubuntu* 散發套件。本教學課程使用 OpenSUSE 12.3 及 Ubuntu Server 13.04。我們假設預設使用者名稱是 *azureuser*。

### Windows VM

按照[此處][1]提供的指示建立 *Windows Server 2012 Datacenter* 散發套件的虛擬機器。在本教學課程中，我們假設使用者名稱是 *azureuser*。

## 建立 IPython Notebook 的端點

此步驟適用於 Linux 和 Windows VM 兩者。稍後，我們將設定IPython 在連接埠 9999 上執行其 Notebook 伺服器。 若要對外提供這個連接埠，我們必須在 Azure 管理入口網站中建立端點。此端點會在 Azure 防火牆中開啟連接埠，並將公用連接埠 (HTTPS，443) 對應到 VM 上的私人連接埠 (9999)。

若要建立端點，請移至 VM 儀表板，按一下 [端點] ，並按一下 [加入端點] ，然後建立新端點 (在此範例中稱為 `ipython_nb`)。選擇 TCP 通訊協定，為公用連接埠選擇 443，並為私人連接埠選擇 9999：

![螢幕擷取畫面][2]

在此步驟後，[端點] 儀表板索引標籤將如下所示：

![螢幕擷取畫面][3]

## 在 VM 上安裝必要軟體

若要在我們的 VM 上執行 IPython Notebook，必須先安裝 IPython 及其相依性。

### Linux (OpenSUSE)

若要安裝 IPython 及其相依性，請透過 SSH進入 Linux VM 並執行以下步驟。

安裝 [NumPy][]、[Matplotlib][]、[Tornado][] 以及 IPython 的其他相依性：

    sudo zypper install python-matplotlib
    sudo zypper install python-tornado
    sudo zypper install ipython

### Linux (Ubuntu)

若要安裝 IPython 及其相依性，請透過 SSH進入 Linux VM 並執行以下步驟。

安裝 [NumPy][]、[Matplotlib][]、[Tornado][] 以及 IPython 的其他相依性：

    sudo apt-get install python-matplotlib
    sudo apt-get install python-tornado
    sudo apt-get install ipython
    sudo apt-get install ipython-notebook

### Windows

若要在 Windows VM 上安裝 IPython 及其相依性，請透過遠端桌面連線至 VM。然後進行以下步驟，使用 Windows PowerShell 執行所有命令行動作。

**注意**：為了使用 Internet Explorer 下載任何項目，您需要變更部分安全設定。從 [伺服器管理員] 中，按一下 [本機伺服器]，然後在 [IE 增強式安全性設定] 中為管理員將它關閉。安裝 IPython 完成之後，您即可再次啟用它。

1.  從 [python.org][] (英文) 安裝 Python 2.7.5 (32 位元)。
    此外您還需要將 `C:\Python27` 和 `C:\Python27\Scripts` 加入您的 `PATH` 環境變數中。

2.  從 [python-distribute.org][] 下載 **distribute\_setup.py** 檔案，
    然後執行以下命令來安裝散發套件：

        python distribute_setup.py

3.  執行以下命令來安裝 [Tornado][] 和 [PyZMQ][]：

        easy_install tornado
        easy_install pyzmq

4.  使用這些網站上提供的 `.exe` 二進位安裝程式，
    下載並安裝 [NumPy][]。截至撰寫時為止，最新版為 **numpy-1.7.1-win32-superpack-python2.7.exe**。

5.  使用這些網站上提供的 `.exe` 二進位安裝程式，
    下載並安裝 [Matplotlib][]。截至撰寫時為止，最新版為 **matplotlib-1.2.1.win32-py2.7.exe**。

6.  下載並安裝 OpenSSL。您可以在 [][]<http://slproweb.com/products/Win32OpenSSL.html></a> (英文) 找到 Windows 版的 OpenSSL。

    -   若您安裝 **Light** 版本，也必須安裝 **Visual C++ 2008 Redistributable** (一樣可在此頁面中取得)。

    -   您必須將 `C:\OpenSSL-Win32\bin` 加入 `PATH` 環境變數中。

    > [WACOM.NOTE] 安裝 OpenSSL 時，請使用 1.0.1g 或更新版，因為其中包含對 Heartbleed 安全性弱點的修正。

7.  使用以下命令下載 IPython：

        easy_install ipython

8.  開啟 Windows 防火牆中的連接埠。在 Windows Server 2012 上，防火牆預設會阻擋傳入的連線。若要開啟連接埠 9999，請依照下列步驟執行：

    -   從 [開始] 畫面啟動 [具備進階安全性的 Windows 防火牆]。

    -   在左側窗格中，按一下 [內送規則]。

    -   在 [動作] 窗格中，按一下 [新增規則...]。

    -   在 [新內送規則精靈] 中，選取 [連接埠]。

    -   在下一個畫面中，選取 [TCP]，並且在 [指定本機連接埠] 中輸入 **9999**。

    -   接受預設值，並且將規則命名，然後按一下 [完成]。

### 設定 IPython Notebook

接著，我們將設定 IPython Notebook第一步是建立自訂 IPython 組態設定檔來封裝組態資訊：

    ipython profile create nbserver

然後，透過 `cd` 切換到設定檔目錄，建立 SSL 憑證並編輯設定檔組態檔案。

在 Linux (OpenSUSE) 上：

    cd ~/.config/ipython/profile_nbserver/

在 Linux (Ubuntu) 上：

    cd ~/.ipython/profile_nbserver/

在 Windows 上：

    cd \users\azureuser\.ipython\profile_nbserver

在這兩個平台上，如下所示建立 SSL 憑證：

    openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem

請注意，由於我們建立的是自我簽署 SSL 憑證，因此在連線至Notebook 時，您的瀏覽器將發出安全性警告。若要長期生產使用，您需要使用與您組織相關聯的正確簽署憑證。由於憑證管理不在此示範的範圍，因此目前只討論自我簽署憑證。

除了使用憑證之外，您也必須提供密碼，以防未經授權而使用您的 Notebook。基於安全性考量，IPython 在其組態檔中使用加密密碼，因此您將需要先加密您的密碼。IPython 提供一個公用程式來進行此作業；在命令提示字元下，執行：

    python -c "import IPython;print IPython.lib.passwd()"

這將提示您提供密碼並確認，然後會輸出密碼，如下所示：

    Enter password: 
    Verify password: 
    sha1:b86e933199ad:a02e9592e59723da722.. (elided the rest for security)

接著，我們將編輯組態檔，也就是您所在的設定檔目錄中的`ipython_notebook_config.py` 檔案。此檔案有許多欄位，預設為注釋排除所有欄位。您可以使用您喜好的任何文字編輯器開啟此檔案，並且應該確保其中至少有以下內容：

    c = get_config()

    # This starts plotting support always with matplotlib
    c.IPKernelApp.pylab = 'inline'

    # You must give the path to the certificate file.

    # If using a Linux VM (OpenSUSE):
    c.NotebookApp.certfile = u'/home/azureuser/.config/ipython/profile_nbserver/mycert.pem'

    # If using a Linux VM (Ubuntu):
    c.NotebookApp.certfile = u'/home/azureuser/.ipython/profile_nbserver/mycert.pem'

    # And if using a Windows VM:
    c.NotebookApp.certfile = r'C:\Users\azureuser\.ipython\profile_nbserver\mycert.pem'

    # Create your own password as indicated above
    c.NotebookApp.password = u'sha1:b86e933199ad:a02e9592e5 etc... '

    # Network and browser details. We use a fixed port (9999) so it matches
    # our Azure setup, where we've allowed traffic on that port

    c.NotebookApp.ip = '*'
    c.NotebookApp.port = 9999
    c.NotebookApp.open_browser = False

### 執行 IPython Notebook

此時，我們已準備啟動 IPython Notebook。若要這麼做，請瀏覽至要在其中儲存 Notebook 的目錄，並啟動 IPython Notebook 伺服器：

    ipython notebook --profile=nbserver

您現在應該可以在位址`https://[Your Chosen Name Here].cloudapp.net` 存取您的 IPython Notebook。

您第一次存取 Notebook 時，登入頁面會詢問您的密碼：

![螢幕擷取畫面][4]

登入後，您會看見「Python Notebook 儀表板」，它是所有 Notebook 作業的控制中心。從此頁面中，您可以建立新的 Notebook、開啟現有 Notebook 等：

![螢幕擷取畫面][5]

如果按一下 [新增 Notebook] 按鈕，您將看到如下所示的開啟頁面：

![螢幕擷取畫面][6]

標記有 `In []:` 提示的區域是輸入區域，您可以在其中輸入任何有效的 Python 程式碼，而且它會在您按 `Shift-Enter` 鍵，或按一下 [播放] 圖示 (工具欄中的向右三角形) 時執行。

由於我們已將 Notebook 設定為自動啟動 NumPy 和 matplotlib 支援，因此您甚至可以產生圖形，例如：

![螢幕擷取畫面][7]

## 強大的典範：具有多樣化媒體的即時運算文件

Notebook 本身對曾經使用 Python 和文字處理器的任何人而言應該感覺相當自然，因為它在某些方面是這兩者的結合：您可以執行 Python 程式碼區塊，但也可以使用工具列中的下拉式功能表，將儲存格的樣式從 [程式碼] 更改為 [Markdown] 來保留記事及其他文字：

![螢幕擷取畫面][8]

不過 IPython Notebook 不只是文字處理器，因為它能夠混合運算和多樣化媒體 (文字、圖形、視訊及新型網頁瀏覽器可以顯示的任何內容)。例如，您可以基於教育目的混合說明性視訊和運算：

![螢幕擷取畫面][9]

也可以在 Notebook 檔案中嵌入始終有效且可用的外部網站：

![螢幕擷取畫面][10]

利用 Python 許多用於科學和科技運算的絕佳程式庫功能，即可進行比複雜網路分析更輕鬆的簡單運算，全部都在一個環境中完成：

![螢幕擷取畫面][11]

這個將最新 Web 的功能與即時運算結合的典範提供許多可能性，並且相當適合雲端；Notebook 可以用來：

-   做為運算記錄器，用來記錄對於問題進行的探究工作，

-   以「即時」運算形式或檔案格式 (HTML、PDF) 與同事分享結果，

-   用於散佈和顯示涉及運算的即時教學材料，以便學生可以立即以互動方式試驗、修改和重新執行實際的程式碼，

-   提供以可由其他人立即重現、驗證和擴展的方式顯示研究結果的「可執行文件」，

-   做為共同運算的平台：多位使用者可以登入同一個 Notebook 伺服器來共享即時運算工作階段，

-   不勝枚舉...

如果您進入 IPython 原始程式碼儲存庫，將可找到提供[Notebook範例][]的整個目錄，您可以下載範例，然後在自己的 Azure IPython VM 上進行試驗。只需從網站下載 `.ipynb` 檔案，並將檔案上傳至 Notebook Azure VM 的儀表板 (或直接將檔案下載至 VM 中) 即可。

## 結論

IPython Notebook 為交互存取 Azure 上 Python 生態系統的功能提供強大的介面。它涵蓋的用途範圍相當廣泛，包括簡單的探究和學習 Python、資料分析和視覺化、模擬和平行運算。最終產生的 Notebook 文件包含已執行且能夠與其他 IPython 使用者共用之運算的完整記錄。IPython Notebook 可以做為本機應用程式，不過相當適合 Azure 上的雲端部署。

例外，也可以透過[Python Tools for Visual Studio][] (PTVS) 在 Visual Studio 中使用 IPython 的核心功能。PTVS 是 Microsoft 提供的免費開放原始碼外掛程式，可以將 Visual Studio 轉變為進階 Python 開發環境，其中包括具有 IntelliSense、偵錯、剖析和平行運算整合功能的進階編輯器。

  [IPython 專案]: http://ipython.org
  [觀看教學課程]: http://go.microsoft.com/fwlink/?LinkID=254535&clcid=0x409
  [螢幕擷取畫面]: ./media/virtual-machines-python-ipython-notebook/ipy-notebook-spectral.png
  [create-account-and-vms-note]: ../includes/create-account-and-vms-note.md
  [此處]: /en-us/manage/linux/tutorials/virtual-machine-from-gallery/
  [1]: /en-us/manage/windows/tutorials/virtual-machine-from-gallery/
  [2]: ./media/virtual-machines-python-ipython-notebook/ipy-azure-linux-005.png
  [3]: ./media/virtual-machines-python-ipython-notebook/ipy-azure-linux-006.png
  [NumPy]: http://www.numpy.org/ "NumPy"
  [Matplotlib]: http://matplotlib.sourceforge.net/ "Matplotlib"
  [Tornado]: http://www.tornadoweb.org/ "Tornado"
  [python.org]: http://www.python.org/download
  [python-distribute.org]: http://python-distribute.org/
  [PyZMQ]: https://github.com/zeromq/pyzmq "PyZMQ"
  []: http://slproweb.com/products/Win32OpenSSL.html
  [4]: ./media/virtual-machines-python-ipython-notebook/ipy-notebook-001.png
  [5]: ./media/virtual-machines-python-ipython-notebook/ipy-notebook-002.png
  [6]: ./media/virtual-machines-python-ipython-notebook/ipy-notebook-003.png
  [7]: ./media/virtual-machines-python-ipython-notebook/ipy-notebook-004.png
  [8]: ./media/virtual-machines-python-ipython-notebook/ipy-notebook-005.png
  [9]: ./media/virtual-machines-python-ipython-notebook/ipy-notebook-006.png
  [10]: ./media/virtual-machines-python-ipython-notebook/ipy-notebook-007.png
  [11]: ./media/virtual-machines-python-ipython-notebook/ipy-notebook-008.png
  [Notebook範例]: https://github.com/ipython/ipython/tree/master/examples/notebooks
  [Python Tools for Visual Studio]: http://pytools.codeplex.com
