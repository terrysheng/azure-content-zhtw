<properties 
	pageTitle="佈建 Microsoft 資料科學虛擬機器 | Microsoft Azure" 
	description="在 Azure 上設定和建立資料科學虛擬機器以進行分析和機器學習服務。" 
	services="machine-learning" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/17/2015" 
	ms.author="bradsev" />


# 佈建 Microsoft 資料科學虛擬機器

## 簡介

Microsoft 資料科學虛擬機器是預先安裝並設定數個常用於資料分析和機器學習之熱門工具的 Azure 虛擬機器 (VM) 映像。這些工具包括：

- Microsoft R Server Developer Edition
- Anaconda Python 散佈
- Visual Studio Community 版本
- Power BI 桌面
- SQL Server Express 版本
- Azure SDK


執行資料科學涉及反覆進行一連串的工作：尋找、載入和前置處理資料、建置與測試模型，以及部署模型以在智慧型應用程式中使用。對於資料科學家而言，使用各種工具來完成這些工作很常見。尋找適當版本的軟體然後下載並安裝它們是相當耗費時間的工作。Microsoft 資料科學虛擬機器可以減輕這樣的負擔。

Microsoft 資料科學虛擬機器可快速啟動分析專案。它能讓您處理多種語言的工作，包含 R、Python、SQL 與 C#。Visual Studio 提供整合式開發環境 (IDE) 以開發和測試您的程式碼，很容易使用。包含在 VM 的 Azure SDK 可讓您使用 Microsoft 的雲端平台上的各種服務，建置您的應用程式。

這個資料科學 VM 映像沒有任何軟體費用。您只需針對要使用此 VM 映像佈建的虛擬機器大小支付 Azure 使用費。您可以在[這裡](https://azure.microsoft.com/marketplace/partners/microsoft-ads/standard-data-science-vm/)找到更多有關運算費用的詳細資料。


## 必要條件

您必須先具有下列項目，才可以建立 Microsoft 資料科學虛擬機器：

- **Azure 訂用帳戶**：若要取得訂用帳戶，請參閱[取得 Azure 免費試用](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。

*   **Azure 儲存體帳戶**：若要建立帳戶，請參閱[建立 Azure 儲存體帳戶](storage-create-storage-account.md#create-a-storage-account)。或者，如果您不想使用現有帳戶，可以在建立 VM 時建立儲存體帳戶。


## 建立 Microsoft 資料科學虛擬機器

建立 Microsoft 資料科學虛擬機器執行個體的步驟如下：

1.	瀏覽至 [Azure 入口網站](https://ms.portal.azure.com/#create/microsoft-ads.standard-data-science-vmstandard-data-science-vm)上的虛擬機器清單。
2.	 按一下底部的 [建立] 按鈕以進入精靈。![configure-data-science-vm](./media/machine-learning-data-science-provision-vm/configure-data-science-virtual-machine.png)
3.	 下列各節提供用來建立 Microsoft 資料科學虛擬機器的精靈中每個步驟 (共有 **5 個步驟**列舉在上圖的右邊) 的**輸入**。以下是設定每個步驟所需的輸入：

  **a.基本**：

   - **名稱**：您建立的資料科學伺服器名稱。
   - **使用者名稱**：系統管理員帳戶登入識別碼。
   - **密碼**：系統管理員帳戶密碼。
   - **訂用帳戶**：如果您有多個訂用帳戶，請選取要在上面建立機器和開立帳單的訂用帳戶。
   - **資源群組**：您可以建立新群組或使用現有的群組。
   - **位置**：選取最適合的資料中心。它通常是擁有您大部分的資料或者是最接近您的實際位置以取得最快速度的網路存取的資料中心

  **b.大小**：

   - 選取其中一個符合您的功能性需求和成本條件約束的伺服器類型。您可以藉由選取 [檢視全部] 取得更多的 VM 大小的選項

  **c.設定**

   - **磁碟類型**：如果您偏好固態硬碟 (SSD)，請選擇「高階」，否則請選擇「標準」。
   - **儲存體帳戶**：您可以在您的訂用帳戶中建立新的 Azure 儲存體帳戶，或使用在精靈的 [基本] 步驟中選擇的相同 [位置] 中的現有儲存體帳戶。
   - **其他參數**：在大部分情況下只會使用預設值。如果您考慮使用非預設值，您可以將滑鼠停留在特定欄位的資訊連結上以取得說明。

  **d.摘要**：

   - 請確認您輸入的所有資訊都正確無誤。

  **e.購買**：

   - 按一下 [購買] 以開始佈建。會提供一個交易條款的連結。VM 除了計算您在 [大小] 步驟中所選擇的伺服器大小之外，不會收取任何其他費用。 


佈建大約 10-20 分鐘。佈建的狀態會顯示在 Azure 入口網站。

## 如何存取 Microsoft 資料科學虛擬機器

建立 VM 之後，您可以使用遠端桌面與您在步驟 4 的基本區段中建立的系統管理員帳戶認證登入。

一旦建立並佈建您的 VM，您已準備好開始使用在上面安裝及設定的工具。我們為許多工具提供了開始功能表磚和桌面圖示。

## 如何在 Jupyter Notebook 伺服器上建立強式密碼 

在資料科學虛擬機器上的命令提示字元中輸入下列命令，來建立您在該虛擬機器上安裝之 Jupyter Notebook 伺服器的專屬強式密碼。

	c:\anaconda\python.exe -c "import IPython;print IPython.lib.passwd()"

在看到提示時選擇強式密碼。

您會在輸出中看到格式為 "sha1:xxxxxx" 的密碼雜湊。複製此密碼雜湊並取代您的 Notebook 設定檔中含參數名稱 ***c.NotebookApp.password*** 的現有雜湊，設定檔位於：**C:\\ProgramData\\jupyter\\jupyter\_notebook\_config.py**。

您只應取代引號內現有的雜湊值。必須保留參數值的引號與 ***sha1:*** 前置詞。

最後，您必須停止並重新啟動 IPython 伺服器，即 VM 上名為「Start\_IPython\_Notebook」的 Windows 排程工作。如果重新啟動此工作後不接受您的新密碼，請嘗試重新啟動虛擬機器。

## Microsoft 資料科學虛擬機器上所安裝的工具

### Microsoft R Server Developer Edition
如果您想要將 R 用於分析，VM 已安裝 Microsoft R Server Developer Edition。Microsoft R Server 是一個能夠廣泛部署的企業級分析平台，以支援的 R 為基礎，可調整規模且安全性。支援各種巨量資料統計資料、預測性模型化和機器學習能力、R Server 支援全範圍分析 – 瀏覽、分析、視覺化及模型化。透過使用及擴充開放原始碼 R，Microsoft R Server 能夠與 R 指令碼、函式及 CRAN 封裝完全相容，以分析企業規模的資料。它也透過在 Microsoft R Server 中加入資料的平行和區塊處理，解決了開放原始碼 R 的記憶體內部限制問題，讓使用者能夠執行遠大於主記憶體可負荷之資料量的資料分析。VM 中也封裝了適用於 R 的整合式開發環境 (IDE)，可透過按一下開始功能表或桌面上的「Revolution R Enterprise 8.0」圖示來存取。您也可以自由下載並使用其他整合式開發環境 (IDE)，例如 [RStudio](http://www.rstudio.com)。

### Python
為了能夠使用 Python 進行開發，我們已安裝了 Anaconda Python 散佈 2.7 與 3.5。這個散佈包含基本的 Python 以及大約 300 個最受歡迎的數學運算、工程設計和資料分析封裝。您可以使用安裝在 Visual Studio 2015 Community 版本內的 Python Tools for Visual Studio (PTVS)，或隨附於 Anaconda 的其中一個整合式開發環境 (IDE)，像是 IDLE 或 Spyder。您可以藉由在搜尋列 (**Win** + **S** 鍵) 上搜尋以啟動其中一個。**注意**：若要在 Anaconda Python 2.7 和 3.5 指向 Python Tools for Visual Studio，您必須為每個版本建立自訂環境，方法是瀏覽至 [工具]-> [Python 工具]-> [Python 環境]，然後按一下 Visual Studio 2015 Community 版本中的 [+ 自訂] 並設定環境路徑。Anaconda Python 2.7 安裝在 C:\\Anaconda 之下，Anaconda Python 3.5 則安裝在 c:\\Anaconda\\envs\\py35 之下。如需詳細步驟，請參閱 [PTVS 文件](https://github.com/Microsoft/PTVS/wiki/Selecting-and-Installing-Python-Interpreters#hey-i-already-have-an-interpreter-on-my-machine-but-ptvs-doesnt-seem-to-know-about-it)。

### Jupyter Notebook
Jupyter Notebook 中也隨附 Anaconda 散佈，這是一個共用程式碼與分析的環境。Jupyter Notebook 伺服器已經預先設定 Python 2、Python 3 及 R 核心。有一個名為 Jupyter Notebook 的桌面圖示可以啟動瀏覽器來存取 Notebook 伺服器。如果您是透過遠端桌面登入 VM，您也可以造訪 [https://localhost:9999/](https://localhost:9999/) 以存取 Jupyter Notebook 伺服器 (附註：如果您收到任何憑證警告，只要繼續作業即可。)我們已封裝範例筆記本 - 一個使用 Python，另一個使用 R。當您使用在較早步驟中建立的密碼向 Jupyter Notebook 驗證之後，您就可以在 Notebook 首頁看到範例的連結。

### Visual Studio 2015 Community 版本
VM 上安裝的 Visual Studio Community 版本。它是 Microsoft 提供的熱門整合式開發環境 (IDE)E 的免費版本，您可以用於評估用途，適合非常小型的團隊。您可以在[這裡](https://www.visualstudio.com/support/legal/mt171547)查看授權條款。按兩下桌面圖示或 [開始] 功能表以開啟 Visual Studio。您也可以使用 **Win** + **S** 並輸入「Visual Studio」來搜尋程式。之後您就可以使用像是 C#、Python 等語言來建立專案。您也將會看到已安裝的外掛程式，它們可以讓您更方便使用 Azure 服務，例如 Azure 資料目錄、Azure HDInsight (Hadoop、Spark) 及 Azure 資料湖。

附註：您可能會收到訊息，表示您的評估期間已過期。您可以輸入 Microsoft 帳戶認證或建立認證並輸入，以取得 Visual Studio Community 版本的存取權。

### SQL Server Express
受限的 SQL Server 版本也會與 Visual Studio Community 版本 一起封裝。您可以藉由啟動 [SQL Server Management Studio] 來存取 SQL Server。您的 VM 名稱會填入做為伺服器名稱。在 Windows 上以系統管理員身分登入時，使用 Windows 驗證。當您在 SQL Server Management Studio 中，可以建立其他使用者、建立資料庫、匯入資料以及執行 SQL 查詢。

### Azure 
VM 上已安裝數個 Azure 工具：- 有一個桌面捷徑可以存取 Azure SDK 文件。- **AzCopy** 是用來將資料移入和移出您的 Microsoft Azure 儲存體帳戶。- **Azure 儲存體總管**是用來瀏覽您儲存在 Azure 儲存體帳戶內的物件。- **Microsoft Azure Powershell** - 用來以 Powershell 指令碼語言管理 Azure 資源的工具，此工具也會安裝在 VM 上。

###Power BI

為了協助您建立儀表板和絕佳的視覺效果，已安裝 **Power BI Desktop**。使用此工具以從不同來源提取資料，來撰寫您的儀表板和報告並發佈至雲端。如需詳細資訊，請參閱 [Power BI](http://powerbi.microsoft.com) 網站。

附註：您需要 Office 365 帳戶才能存取 Power BI。

## 其他 Microsoft 開發工具
[**Microsoft Web Platform Installer**](https://www.microsoft.com/web/downloads/platform.aspx) 可以用來探索並下載其他 Microsoft 開發工具。另外在 Microsoft 資料科學虛擬機器桌面上也有提供工具的捷徑。

## 後續步驟
以下是繼續您的學習和探索的一些後續步驟。

* 按一下開始功能表並查看功能表中列出的工具，來探索資料科學 VM 上的各種資料科學工具。
* 瀏覽至 **C:\\Program Files\\Microsoft\\MRO-for-RRE\\8.0\\R-3.2.2\\library\\RevoScaleR\\demoScripts** 以取得使用 R 中支援企業規模資料分析之 RevoScaleR 程式庫的範例。  
* 了解如何使用[資料科學程序](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/)以系統化方式建置端對端分析方案
* 請造訪 [Cortana 分析資源庫](http://gallery.cortanaanalytics.com)以取得使用 Cortana Analytics Suite 的機器學習和資料分析範例。我們也已經在虛擬機器的開始功能表與桌面上提供圖示以方便存取 

<!---HONumber=AcomDC_0128_2016-->