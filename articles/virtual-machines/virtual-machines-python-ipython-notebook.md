<properties
	pageTitle="建立 Jupyter/IPython Notebook | Microsoft Azure"
	description="了解如何在 Azure 中使用資源管理員部署模型建立的 Linux 虛擬機器上，部署 Jupyter/IPython Notebook。"
	services="virtual-machines"
	documentationCenter="python"
	authors="crwilcox"
	manager="wpickett"
	editor=""
	tags=“azure-service-management,azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="python"
	ms.topic="article"
	ms.date="11/10/2015"
	ms.author="crwilcox"/>

# Azure 上的 Jupyter Notebook

[Jupyter 專案](http://jupyter.org) (先前稱為 [IPython 專案](http://ipython.org)) 使用功能強大的互動式殼層，來提供適用於科學運算的工具集合，結合了程式碼執行與即時運算文件的建立。這些 Notebook 文件可以包含任何文字、數學公式、輸入程式碼、結果、圖形、視訊，以及新型 Web 瀏覽器能夠顯示的其他任何類型的媒體。無論您是第一次使用 Python 並希望在有趣的互動式環境中了解它，還是執行一些重要的平行/技術運算，Jupyter Notebook 都是相當好的選擇。

![螢幕擷取畫面](./media/virtual-machines-python-ipython-notebook/ipy-notebook-spectral.png) 使用 SciPy 和 Matplotlib 封裝來分析錄音的結構。


## Jupyter 的兩個方法：Azure Notebook 或自訂部署
Azure 提供您可以用來[快速開始使用 Jupyter](http://blogs.technet.com/b/machinelearning/archive/2015/07/24/introducing-jupyter-notebooks-in-azure-ml-studio.aspx) 的服務。使用 Azure Notebook 服務，即可對具備 Python 及其許多程式庫所有功能的可調整運算資源，輕鬆獲取 Jupyter Web 存取介面的存取權。由於安裝是透過服務來處理，因此，使用者可以存取這些資源，而不需依使用者進行系統管理與設定。

如果筆記本服務不適用您的案例，請繼續閱讀本文，本文將示範如何使用 Linux 虛擬機器 (VM)，在 Microsoft Azure 上部署 Jupyter Notebook。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]傳統部署模型。

[AZURE.INCLUDE [create-account-and-vms-note](../../includes/create-account-and-vms-note.md)]

## 在 Azure 上建立並設定 VM

第一步是建立在 Azure 上運作的虛擬機器 (VM)。此 VM 是雲端中的完整作業系統，它將用來執行 Jupyter Notebook。Azure 能夠同時執行 Linux 和 Windows 虛擬機器，而我們將介紹如何在這兩種類型的虛擬機器上設定 Jupyter。

### 建立 Linux VM 並開啟適用於 Jupyter 的連接埠

按照[此處][portal-vm-linux]提供的指示來建立 *Ubuntu* 散發套件的虛擬機器。本教學課程使用 Ubuntu Server 14.04 LTS。我們假設預設使用者名稱是 *azureuser*。

部署虛擬機器之後，我們需要開啟關於網路安全性群組的安全性規則。從 Azure 入口網站移至 [網路安全性群組]，然後開啟對應到您 VM 的安全性群組索引標籤。您必須使用下列設定來新增輸入安全性規則：針對通訊協定輸入 **TCP**、針對來源 (公用) 連接埠輸入 *****，以及針對目的地 (私人) 連接埠輸入 **9999** 。

![螢幕擷取畫面](./media/virtual-machines-python-ipython-notebook/azure-add-endpoint.png)

在網路安全性群組中，按一下 [網路介面]， 並記下**公用 IP 位址**，您在下一步驟中連接到 VM 時需要用到此位址。

## 在 VM 上安裝必要軟體

若要在我們的 VM 上執行 Jupyter Notebook，必須先安裝 Jupyter 及其相依性。使用 ssh 以及您在建立 VM 時選擇的使用者名稱/密碼組，連接到您的 linux vm。在本教學課程中，我們將使用 PuTTY 並從 Windows 連接。

### 在 Ubuntu 上安裝 Jupyter
使用 [Continuum Analytics](https://www.continuum.io/downloads) 提供的其中一個連結來安裝 Anaconda，此為常用的資料科學 Python 散發套件。撰寫本文件時，下列連結是最新日期的版本。

#### 適用於 Linux 的 Anaconda 安裝
<table>
  <th>Python 3.4</th>
  <th>Python 2.7</th>
  <tr>
    <td>
		<a href='https://3230d63b5fc54e62148e-c95ac804525aac4b6dba79b00b39d1d3.ssl.cf1.rackcdn.com/Anaconda3-2.3.0-Linux-x86_64.sh'>64 位元</href>
	</td>
    <td>
		<a href='https://3230d63b5fc54e62148e-c95ac804525aac4b6dba79b00b39d1d3.ssl.cf1.rackcdn.com/Anaconda-2.3.0-Linux-x86_64.sh'>64 位元</href>
	</td>
  </tr>
  <tr>
    <td>
		<a href='https://3230d63b5fc54e62148e-c95ac804525aac4b6dba79b00b39d1d3.ssl.cf1.rackcdn.com/Anaconda3-2.3.0-Linux-x86.sh'>32 位元</href>
	</td>
    <td>
		<a href='https://3230d63b5fc54e62148e-c95ac804525aac4b6dba79b00b39d1d3.ssl.cf1.rackcdn.com/Anaconda-2.3.0-Linux-x86.sh'>32 位元</href>
	</td>  
  </tr>
</table>


#### 在 Ubuntu 上安裝 Anaconda3 2.3.0 64 位元
例如，這是您可以在 Ubuntu 上安裝 Anaconda 的方式

	# install anaconda
	cd ~
	mkdir -p anaconda
	cd anaconda/
	curl -O https://3230d63b5fc54e62148e-c95ac804525aac4b6dba79b00b39d1d3.ssl.cf1.rackcdn.com/Anaconda3-2.3.0-Linux-x86_64.sh
	sudo bash Anaconda3-2.3.0-Linux-x86_64.sh -b -f -p /anaconda3

	# clean up home directory
	cd ..
	rm -rf anaconda/

	# Update Jupyter to the latest install and generate its config file
	sudo /anaconda3/bin/conda install -f jupyter -y
	/anaconda3/bin/jupyter-notebook --generate-config


![螢幕擷取畫面](./media/virtual-machines-python-ipython-notebook/anaconda-install.png)


### 設定 Jupyter 並使用 SSL
安裝之後，我們需要花點時間，針對 Jupyter 安裝組態檔。如果您在設定 Jupyter 時遇到麻煩，查看[適用於執行 Notebook 伺服器的 Jupyter 文件](http://jupyter-notebook.readthedocs.org/en/latest/public_server.html)可能會有幫助。

然後，透過 `cd` 切換到設定檔目錄，來建立 SSL 憑證並編輯設定檔組態檔案。

在 Linux 上使用下列命令。

    cd ~/.jupyter

使用下列命令建立 SSL 憑證 (Linux 和 Windows)。

    openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem

請注意，由於我們建立的是自我簽署 SSL 憑證，因此在連線至 Notebook 時，您的瀏覽器將發出安全性警告。若要進行長期生產使用，您將需要使用與您的組織相關聯的正確簽署憑證。由於憑證管理不在本展示的範圍，因此目前只討論自我簽署憑證。

除了使用憑證之外，您也必須提供密碼，以防未經授權而使用您的 Notebook。基於安全考量，Jupyter 會在其組態檔中使用加密密碼，因此您需要先加密您的密碼。IPython 提供一個公用程式來進行此作業；在命令提示字元下，執行下列命令。

    /anaconda3/bin/python -c "import IPython;print(IPython.lib.passwd())"

這將提示您提供密碼並確認，接著將會顯示密碼。請記下此密碼，以便在下一個步驟中使用。

    Enter password:
    Verify password:
    sha1:b86e933199ad:a02e9592e59723da722.. (elided the rest for security)

接著，我們將編輯設定檔的組態檔，也就是您所在目錄中的 `jupyter_notebook_config.py` 檔案。請注意，這個檔案可能不存在 -- 如果不存在，只需建立它即可。此檔案有許多欄位，預設為注釋排除所有欄位。您可以使用您喜好的任何文字編輯器開啟此檔案，並且應該確保其中至少有以下內容。請務必使用前一個步驟中的 sha1 來取代密碼。

    c = get_config()

    # You must give the path to the certificate file.
    c.NotebookApp.certfile = u'/home/azureuser/.jupyter/mycert.pem'

    # Create your own password as indicated above
    c.NotebookApp.password = u'sha1:b86e933199ad:a02e9592e5 etc... '

    # Network and browser details. We use a fixed port (9999) so it matches
    # our Azure setup, where we've allowed :wqtraffic on that port
    c.NotebookApp.ip = '*'
    c.NotebookApp.port = 9999
    c.NotebookApp.open_browser = False

### 執行 Jupyter Notebook

此時，我們已準備好啟動 Jupyter Notebook。若要這麼做，請瀏覽至您想要在其中儲存 Notebook 的目錄，並使用下列命令來啟動 Jupyter Notebook 伺服器。

    /anaconda3/bin/jupyter-notebook

您現在應該可以在 `https://[PUBLIC-IP-ADDRESS]:9999` 的網址中存取您的 Jupyter Notebook。

您第一次存取 Notebook 時，登入頁面會詢問您的密碼。登入後，您會看見「Jupyter Notebook 儀表板」，它是所有 Notebook 作業的控制中心。從此頁面中，您可以建立新 Notebook 和開啟現有 Notebook。

![螢幕擷取畫面](./media/virtual-machines-python-ipython-notebook/jupyter-tree-view.png)

### 使用 Jupyter Notebook

如果按一下 [新增] 按鈕，您將看到以下開啟頁面。

![螢幕擷取畫面](./media/virtual-machines-python-ipython-notebook/jupyter-untitled-notebook.png)

標記有 `In []:` 提示的區域是輸入區域，您可以在其中輸入任何有效的 Python 程式碼，而它會在您按 `Shift-Enter` 鍵或按一下 [播放] 圖示 (工具欄中的向右三角形) 時執行。

## 強大的典範：具有多樣化媒體的即時運算文件

Notebook 本身對曾經使用 Python 和文字處理器的人而言應該感覺相當自然，因為它在某些方面是這兩者的結合：您可以執行 Python 程式碼區塊，但是也可以使用工具列中的下拉式功能表，將儲存格的樣式從 [程式碼] 更改為 [Markdown] 來保留記事及其他文字。

Jupyter 不只是文字處理器，因為它能夠混合運算和多樣化媒體 (文字、圖形、視訊及新型網頁瀏覽器可以顯示的任何內容)。您可以混合文字、程式碼、影片及更多項目！

![螢幕擷取畫面](./media/virtual-machines-python-ipython-notebook/jupyter-editing-experience.png)

利用 Python 許多用於科學和科技運算的絕佳程式庫功能，在下列螢幕擷取畫面中，可進行與複雜網路分析一樣輕鬆的簡單運算，全部都在一個環境中完成。

這個將最新 Web 的功能與即時運算結合的典範提供許多可能性，並且相當適合雲端；Notebook 可以用來：

* 做為運算記錄器，用來記錄對於問題進行的探究工作。

* 以「即時」運算形式或檔案格式 (HTML、PDF)，與同事分享結果。

* 用於散佈和顯示涉及運算的即時教學材料，以便學生可以立即以互動方式試驗、修改和重新執行實際的程式碼。

* 提供以可由其他人立即重現、驗證和擴展的方式顯示研究結果的「可執行文件」。

* 做為共同運算的平台：多位使用者可以登入同一部 Notebook 伺服器來共享即時運算工作階段。


如果您進入 IPython 原始程式碼[儲存機制][]，將可找到提供 Notebook 範例的整個目錄，您可以下載範例，然後在自己的 Azure Jupyter VM 上進行試驗。只需從網站下載 `.ipynb` 檔案，並將這些檔案上傳至您的 Notebook Azure VM 儀表板即可 (也可以將這些檔案直接下載至 VM)。

## 結論

Jupyter Notebook 可為互動存取 Azure 上 Python 生態系統的功能提供強大的介面。它涵蓋的用途範圍相當廣泛，包括簡單的探究和學習 Python、資料分析和視覺化、模擬和平行運算。最終產生的 Notebook 文件會完整記錄已執行且能夠與其他 Jupyter 使用者共享的運算。Jupyter Notebook 可以用來做為本機應用程式，不過它相當適合用於 Azure 上的雲端部署

您也可以透過 [Python Tools for Visual Studio][] (PTVS)，在 Visual Studio 中使用 Jupyter 的核心功能。PTVS 是 Microsoft 提供的免費開放原始碼外掛程式，可以將 Visual Studio 轉變為進階 Python 開發環境，其中包括具有 IntelliSense、偵錯、剖析和平行運算整合功能的進階編輯器。

## 後續步驟

如需詳細資訊，請參閱 [Python 開發人員中心](/develop/python/)。

[portal-vm-linux]: https://azure.microsoft.com/zh-TW/documentation/articles/virtual-machines-linux-tutorial-portal-rm/
[儲存機制]: https://github.com/ipython/ipython
[Python Tools for Visual Studio]: http://aka.ms/ptvs

<!---HONumber=AcomDC_1203_2015-->