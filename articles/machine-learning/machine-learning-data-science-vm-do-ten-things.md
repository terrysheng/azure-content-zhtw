<properties
	pageTitle="您可以在 Data Science Virtual Machine 上做的十件事 | Microsoft Azure"
	description="在 Data science Virtual Machine 上執行各種資料探索和模型分析工作。"
	services="machine-learning"
	documentationCenter=""
	authors="bradsev"
	manager="paulettm"
	editor="cgronlun"  />

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/13/2016"
	ms.author="gokuma;weig;bradsev" />

# 您可以在 Data Science Virtual Machine 上做的十件事 

Microsoft Data Science Virtual Machine (DSVM) 是強大的資料科學開發環境，可讓您執行各種資料探索和模型分析工作。此環境已內建數個熱門的資料分析工具，以便您快速開始進行內部部署或混合式部署的分析。DSVM 與多項 Azure 服務密切合作，並且能夠讀取及處理已儲存在 Azure SQL 資料倉儲、Azure 資料湖、Azure 儲存體或 DocumentDB 中的資料。它也可以利用 Azure Machine Learning 和 Azure Data Factory 等其他分析工具。


在本文中，我們會逐步引導您使用 DSVM 來執行各種資料科學工作，並與其他 Azure 服務互動。以下是您可以在 DSVM 上做的一些事：

1. 使用 Microsoft R Server 或 Python 來探索資料並在 DSVM 本機開發模型
2. 使用 Jupyter Notebook，在採用 Python 2、Python 3、Microsoft R (針對延展性和效能設計的 R 企業就緒版) 的瀏覽器上試驗您的資料
3. 在 Azure Machine Learning 上實作使用 R 和 Python 建置的模型，以便用戶端應用程式使用簡單的 Web 服務介面存取您的模型
4. 使用 Azure 入口網站或 Powershell 管理您的 Azure 資源 
5. 建立 Azure 檔案儲存體做為 DSVM 上可掛接的磁碟機，以擴充您的儲存空間並與整個小組共用大型資料集 / 程式碼 
6. 使用 Github 來與您的小組共用程式碼，並使用預先安裝的 Git 用戶端 (Git Bash、Git GUI) 來存取您的儲存機制。
7. 存取各種 Azure 資料和分析服務，例如 Azure Blob 儲存體，Azure 資料湖、Azure HDInsight (Hadoop)、Azure DocumentDB、Azure SQL 資料倉儲和資料庫
8. 使用在 DSVM 上預先安裝的 Power BI Desktop 來建立報告和儀表板並雲端上加以部署
9. 動態調整 DSVM 以符合您的專案需求 
10. 在虛擬機器上安裝其他工具

>[AZURE.NOTE] 額外的使用費會適用於許多在本文中列出的其他資料儲存體和分析服務。如需詳細資訊，請參閱 [Azure 價格](https://azure.microsoft.com/pricing/)頁面。

**必要條件**

- 您將需要 Azure 訂用帳戶。您可以[在這裡](https://azure.microsoft.com/free/)註冊免費試用。 

- 如需在 Azure 入口網站上佈建 Data Science Virtual Machine 的指示，請參閱[建立虛擬機器](https://ms.portal.azure.com/#create/microsoft-ads.standard-data-science-vmstandard-data-science-vm)。

## 1\.使用 Microsoft R Server 或 Python 探索資料和開發模型

您可以使用 R 和 Python 等語言在 DSVM 上進行資料分析。

對於 R，您可以使用稱為 "Revolution R Enterprise 8.0" 並可在 [開始] 功能表或桌面找到的 IDE。除了開放原始碼/CRAN-R 以外，Microsoft 提供了額外的程式庫以便進行可調整的分析，以及分析大於進行平行區塊分析允許之記憶體大小的資料。您也可以安裝您選擇的 R IDE，例如 [RStudio](https://www.rstudio.com/products/rstudio-desktop/)。

對於 Python，您可以使用已預先安裝 Python Tools for Visual Studio (PTVS) 延伸模組的 Visual Studio Community Edition 等 IDE。根據預設，PTVS 上只設定基本的 Python 2.7 (不含任何分析程式庫，如 SciKit、Pandas)。若要啟用 Anaconda Python 2.7 和 3.5，您必須執行下列作業：

* 為每個版本建立自訂環境，方法是瀏覽至 [工具] -> [Python 工具] -> [Python 環境]，然後按一下 Visual Studio 2015 Community Edition 中的 [+ 自訂]。
* 提供描述並將環境路徑前置詞設定為 *c:\\anaconda* (適用於 Anaconda Python 2.7) 或 *c:\\anaconda\\envs\\py35* (適用於 Anaconda Python 3.5) 
* 按一下 [自動偵測]，然後按一下 [套用] 來儲存環境。 

以下是自訂環境設定在 Visual Studio 中的外觀。

![PTVS 設定](./media/machine-learning-data-science-vm-do-ten-things/PTVSSetup.png)

如需有關如何建立 Python 環境的詳細資訊，請參閱 [PTVS 文件](https://github.com/Microsoft/PTVS/wiki/Selecting-and-Installing-Python-Interpreters#hey-i-already-have-an-interpreter-on-my-machine-but-ptvs-doesnt-seem-to-know-about-it)。
  
現在您將建立新的 Python 專案。瀏覽至 檔案] -> [新增] -> [專案] -> [Python]，然後選取您要建置的 Python 應用程式類型。您可以將目前專案的 Python 環境設為所需的版本 (Anaconda 2.7 或 3.5)：以滑鼠右鍵按一下 [Python 環境]、選取 [加入/移除 Python 環境]，然後選取要與專案相關聯的環境。您可以在[文件](https://github.com/Microsoft/PTVS/wiki)頁面找到在產品上使用 PTVS 的詳細資訊。

## 2\.使用 Jupyter Notebook 來探索您的資料，並以 Python 或 R 進行模型分析

Jupyter Notebook 是一個強大的環境，可提供以瀏覽器為基礎的 "IDE" 進行資料探索和模型分析。您可以在 Jupyter Notebook 使用 Python 2、Python 3 或 R (開放原始碼和 Microsoft R Server)。
 
若要啟動 Jupyter Notebook，請按一下標題為 [Jupyter Notebook] 的 [開始] 功能表圖示 / 桌面圖示。在 DSVM 上，您也可以瀏覽至 "https://localhost:9999/" 以存取 Jupyter Notebook。如果系統提示您輸入密碼，請使用 [DSVM 文件頁面](machine-learning-data-science-provision-vm.md/#how-to-create-a-strong-password-on-the-jupyter-notebook-server)上提供的指示，建立強式密碼以存取 Jupyter Notebook。

您開啟 Notebook 後，會看到一個目錄，其中包含一些已預先封裝到 DSVM 中的 Notebook 範例。現在您可以：

- 按一下 Notebook，查看程式碼。 
- 按 **SHIFT-ENTER** 執行每個資料格。 
- 按一下 [儲存格] -> [執行] 執行整個 Notebook。
- 按一下 Jupyter 圖示 (左上角)，然後按一下右邊的 [新增] 按鈕，再選擇 Notebook 語言 (也稱為核心)，建立新的 Notebook。 

>[AZURE.NOTE] 我們目前支援 Python 2.7、Python 3.5 和 R。R 核心支援以開放原始碼 R 以及企業可調整式 Microsoft R Server 進行程式設計。

一旦位於 Notebook，您即可探索資料、建立模型，以及使用您所選的程式庫測試模型。


## 3\.使用 R 或 Python 建置模型並且使用 Azure Machine Learning 實作

建置並驗證您的模型之後，下一個步驟通常是將它部署到生產環境中。這可讓您的用戶端應用程式以即時或分批模式方式叫用模型預測。Azure Machine Learning 提供一個機制來實作以 R 或 Python 建置的模型。

當您在 Azure Machine Learning 中實作模型時，系統會公開一項可讓用戶端進行 REST 呼叫的 Web 服務，以便傳入輸入參數以及接收來自模型的預測做為輸出。

>[AZURE.NOTE] 如果您尚未註冊 AzureML，請造訪 [AzureML Studio](https://studio.azureml.net/) 首頁並按一下 [開始使用]，即可取得免費的工作區或標準工作區。

### 建置和實作 Python 模型

以下是在 Python Jupyter Notebook 來開發的程式碼片段，它會使用 Scikit-learn 程式庫建置簡單的模型。
  
	#IRIS classification
	from sklearn import datasets
	from sklearn import svm
	clf = svm.SVC()
	iris = datasets.load_iris()
	X, y = iris.data, iris.target
	clf.fit(X, y) 
 
用來部署 python 模型到 Azure Machine Learning 的方法，會將模型預測包裝成函式，然後以預先安裝之 Azure Machine Learning python 程式庫所提供的屬性裝飾它，代表您的 Azure Machine Learning 工作區識別碼、API 金鑰，以及輸入和傳回參數。

	from azureml import services
	@services.publish(workspaceid, auth_token)
	@services.types(sep_l = float, sep_w = float, pet_l=float, pet_w=float)
	@services.returns(int) #0, or 1, or 2
	def predictIris(sep_l, sep_w, pet_l, pet_w):
 	inputArray = [sep_l, sep_w, pet_l, pet_w]
	return clf.predict(inputArray)

用戶端現在可以呼叫 Web 服務。有一個可建構 REST API 要求的便利包裝函式。以下是用來取用 Web 服務的程式碼範例。

	# Consume through web service URL and keys
	from azureml import services
	@services.service(url, api_key)
	@services.types(sep_l = float, sep_w = float, pet_l=float, pet_w=float)
	@services.returns(float)
	def IrisPredictor(sep_l, sep_w, pet_l, pet_w):
    pass

	IrisPredictor(3,2,3,4)


>[AZURE.NOTE] Azure Machine Learning 程式庫目前只在 Python 2.7 上支援。

### 建置和實作 R 模型

您可以用類似於處理 Python 的方式，將在 Data Science Virtual Machine 或其他位置建置的 R 模型部署到 Azure Machine Learning。步驟如下：

- 建立 settings.json 檔案 (如下所示)，以提供您的工作區 ID 和驗證權杖。
- 針對模型的預測函數，撰寫一個包裝函式。 
- 呼叫 Azure Machine Learning 程式庫中的 ```publishWebService``` 傳入函式包裝函式。  

以下是可用來安裝、建置、發佈和取用模型作為 Azure Machine Learning Web 服務的程序和程式碼片段。

#### 設定

1.  在 Revolution R Enterprise 8.0 整合式開發環境 (IDE) 或您的 R IDE 輸入 ```install.packages("AzureML")```安裝 AzureML R 封裝。 
2.  從[這裡](https://cran.r-project.org/bin/windows/Rtools/)下載 RTools。您需要在路徑中有 zip 公用程式 (和具名的 zip.exe)，才能將 R 封裝實作到 AzureML。 
3.  在主目錄下名為 ```.azureml``` 的目錄下建立 settings.json 檔案，並輸入來自 Azure ML 工作區的參數：

settings.json 檔案結構：

	{"workspace":{
	"id"                  : "ENTER YOUR AZUREML WORKSPACE ID",
	"authorization_token" : "ENTER YOUR AZUREML AUTH TOKEN"
	}}


#### 以 R 建置模型並在 Azure ML 中加以發佈

	library(AzureML)
	ws <- workspace(config="~/.azureml/settings.json")

	if(!require("lme4")) install.packages("lme4")
	library(lme4)
	set.seed(1)
	train <- sleepstudy[sample(nrow(sleepstudy), 120),]
	m <- lm(Reaction ~ Days + Subject, data = train)
 
	# Define a prediction function to publish based on the model:
	sleepyPredict <- function(newdata){
  		predict(m, newdata=newdata)
	}
 
	ep <- publishWebService(ws, fun = sleepyPredict, name="sleepy lm", inputSchema = sleepstudy, data.frame=TRUE)

#### 取用在 Azure ML 中部署的模型

為了從用戶端應用程式取用模型，我們使用 Azure Machine Learning 程式庫來透過 `services` API 呼叫依名稱查閱已發佈的 Web 服務，進而決定端點。然後您只要呼叫 `consume` 函數並傳入要預測的資料框架。下列程式碼用來取用已發佈為 Azure Machine Learning Web 服務的模型。


	library(AzureML)
	library(lme4)
	ws <- workspace(config="~/.azureml/settings.json")

	s <-  services(ws, name = "sleepy lm")
	s <- tail(s, 1) # use the last published function, in case of duplicate function names

	ep <- endpoints(ws, s)

	# OK, try this out, and compare with raw data
	ans = consume(ep, sleepstudy)$ans

Azure Machine Learning R 程式庫的詳細資訊可以在[這裡](https://cran.r-project.org/web/packages/AzureML/AzureML.pdf)找到。

## 4\.使用 Azure 入口網站或 Powershell 管理您的 Azure 資源

DSVM 不僅可讓您在虛擬機器本機建置分析解決方案，也可讓您存取 Microsoft Azure 雲端上的服務。Azure 提供數個可從 DSVM 管理和存取的計算、儲存、資料分析服務和其他服務。

若要管理 Azure 訂用帳戶和雲端資源，您可以使用瀏覽器並指向 [Azure 入口網站](portal.azure.com)。您也可以使用 Azure Powershell 並透過指令碼管理 Azure 訂用帳戶和資源。您可以從桌面上的捷徑或從標題為 "Microsoft Azure Powershell" 的 [開始] 功能表執行 Azure Powershell。如需有關如何使用 Windows Powershell 指令碼管理 Azure 訂用帳戶和資源的詳細資訊，請參閱 [Microsoft Azure Powershell 文件](../powershell-azure-resource-manager.md)。


## 5\.使用共用的檔案系統來擴充您的儲存空間

資料科學家可以在小組內共用大型資料集、程式碼或其他資源。DSVM 本身有大約 70GB 的可用空間。若要擴充您的儲存體，您可以使用 Azure 檔案服務，然後將它掛接於 DSVM 或透過 REST API 存取。

>[AZURE.NOTE] Azure 檔案服務共用的空間上限為 5TB，而個別的檔案大小限制為 1TB。

您可以使用 Azure PowerShell 建立 Azure 檔案服務共用。以下是可在 Azure PowerShell 下執行以建立 Azure 檔案服務共用的指令碼。

	# Authenticate to Azure. 
	Login-AzureRmAccount
	# Select your subscription
	Get-AzureRmSubscription –SubscriptionName "<your subscription name>" | Select-AzureRmSubscription
	# Create a new resource group. 
	New-AzureRmResourceGroup -Name <dsvmdatarg>
	# Create a new storage account. You can reuse existing storage account if you wish. 
	New-AzureRmStorageAccount -Name <mydatadisk> -ResourceGroupName <dsvmdatarg> -Location "<Azure Data Center Name For eg. South Central US>" -Type "Standard_LRS"
	# Set your current working storage account
	Set-AzureRmCurrentStorageAccount –ResourceGroupName "<dsvmdatarg>" –StorageAccountName <mydatadisk>
	
	# Create a Azure File Service Share
	$s = New-AzureStorageShare <<teamsharename>>
	# Create a directory under the FIle share. You can give it any name
	New-AzureStorageDirectory -Share $s -Path <directory name>
	# List the share to confirm that everything worked
	Get-AzureStorageFile -Share $s


您現在已建立 Azure 檔案共用，即可將它掛接在 Azure 的任何虛擬機器中。強烈建議 VM 位於與儲存體帳戶相同的 Azure 資料中心，以避免延遲和資料傳輸費用。以下是您可以在 Azure Powershell 上執行以在 DSVM 上掛接磁碟機的命令。


	# Get storage key of the storage account that has the Azure file share from Azure portal. Store it securely on the VM to avoid prompted in next command.
	cmdkey /add:<<mydatadisk>>.file.core.windows.net /user:<<mydatadisk>> /pass:<storage key>
	
	# Mount the Azure file share as Z: drive on the VM. You can chose another drive letter if you wish
	net use z:  \<mydatadisk>.file.core.windows.net<<teamsharename>>


您現在可以將此磁碟機當作 VM 上的任何一般磁碟機存取。

## 6\.使用 Github 與您的小組共用程式碼 

Github 是一種程式碼儲存機制，開發人員會分享使用各種技術，針對不同工具建立的大量範例程式碼和原始碼。它會使用 Git 技術來追蹤和儲存各版本的程式碼檔案。Github 也是一個平台，您可以在其中建立自己的儲存機制，以儲存您小組共用的程式碼和文件、實作版本控制，以及控制有權檢視和貢獻程式碼的人員。如需有關使用 Git 的詳細資訊，請瀏覽 [Github 說明網頁](https://help.github.com/)。您可以使用 Github 與您的小組共同作業、使用社群所開發的程式碼，以及將程式碼貢獻回社群。

DSVM 已在命令列和 GUI 上載入用戶端工具，以便存取 Github 儲存機制。可供使用 Git 及 Github 的命令列工具稱為 Git Bash。DSVM 上安裝的 Visual Studio 具有 Git 延伸模組。您可以在 [開始] 功能表和桌面上找到這些工具的啟動圖示。

若要從 Github 儲存機制下載程式碼，您可以使用 ```git clone``` 命令。例如，若要將 Microsoft 所發佈的資料科學儲存機制下載到目前的目錄中，您可以在 ```git-bash``` 中執行下列命令。

	git clone https://github.com/Azure/Azure-MachineLearning-DataScience.git

您可以在 Visual Studio 中執行相同的複製作業。以下的螢幕擷取畫面顯示如何在 Visual Studio 中存取 Git 和 Github 工具。

![Visual Studio 中的 Git](./media/machine-learning-data-science-vm-do-ten-things/VSGit.PNG)


從 github.com 上提供的幾個資源，即可找到透過 Git 來使用 Github 儲存機制的相關詳細資訊。[功能提要](https://training.github.com/kit/downloads/github-git-cheat-sheet.pdf)是有用的參考資料。


## 7\.存取各種 Azure 資料和分析服務

### Azure Blob

Azure blob 是可靠、划算的雲端儲存體，可存放大型和小型的資料。讓我們看一下如何將資料移至 Azure Blob 和存取 Azure Blob 中儲存的資料。

**必要條件**

- **從 [Azure 入口網站](http://portal.azure.com)建立 Azure Blob 儲存體帳戶。**

![Create\_Azure\_Blob](./media/machine-learning-data-science-vm-do-ten-things/Create_Azure_Blob.PNG)


- 確認預先安裝的命令列 AzCopy 工具位於 ```C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy.exe```。您可以將包含 azcopy.exe 的目錄加入至您的 PATH 環境變數，如此便不用在執行這項工具時輸入完整的命令路徑。如需 AzCopy 工具的詳細資訊，請參閱 [AzCopy 文件](../storage/storage-use-azcopy.md)

- 從 VM 桌面上的圖示啟動 Azure 儲存體總管。您也可以在目錄 ```C:\Program Files (x86)\Neudesic\Azure Storage Explorer 6``` 中找到它。

![AzureStorageExplorer\_v4](./media/machine-learning-data-science-vm-do-ten-things/AzureStorageExplorer_v4.png)

**將資料從 VM 移至 Azure Blob：AzCopy**

若要在本機檔案與 Blob 儲存體之間移動資料，您可以在命令列或 PowerShell 中使用 AzCopy：`AzCopy /Source:C:\myfolder /Dest:https://<mystorageaccount>.blob.core.windows.net/<mycontainer> /DestKey:<storage account key> /Pattern:abc.txt`

用您的檔案儲存路徑取代 **C:\\myfolder**，將 **mystorageaccount** 取代成您的 Blob 儲存體帳戶名稱、將 **mycontainer** 取代成容器名稱，並用您的 Blob 儲存體存取金鑰取代 **storage account key**。您可以在 [Azure 入口網站](http://portal.azure.com)中尋找您的儲存體帳戶認證。

![StorageAccountCredential\_v2](./media/machine-learning-data-science-vm-do-ten-things/StorageAccountCredential_v2.png)

在 PowerShell 或從命令提示字元執行 AzCopy 命令。以下是使用 AzCopy 命令的一些範例：


	# Copy *.sql from local machine to a Azure Blob
	"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:"c:\Aaqs\Data Science Scripts" /Dest:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /DestKey:[ENTER STORAGE KEY] /S /Pattern:*.sql
	
	# Copy back all files from Azure Blob container to Local machine
	
	"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Dest:"c:\Aaqs\Data Science Scripts\temp" /Source:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /SourceKey:[ENTER STORAGE KEY] /S
	


執行 AzCopy 命令來複製到 Azure Blob 之後，您會看到檔案隨即出現在 Azure 儲存體總管中。

![AzCopy\_run\_finshed\_Storage\_Explorer\_v3](./media/machine-learning-data-science-vm-do-ten-things/AzCopy_run_finshed_Storage_Explorer_v3.png)


**將資料從 VM 移至 Azure Blob：Azure 儲存體總管**

您也可以使用 Azure 儲存體總管，從 VM 中的本機檔案上載資料：

![](./media/machine-learning-data-science-vm-do-ten-things/AzureStorageExplorer_upload_v2.png)


**從 Azure Blob 讀取資料：AML 讀取器模組**

您可以在 Azure Machine Learning Studio 中，使用**讀取器模組**來讀取 Blob 中的資料。

![AML\_ReaderBlob\_Module\_v3](./media/machine-learning-data-science-vm-do-ten-things/AML_ReaderBlob_Module_v3.png)


**從 Azure Blob 讀取資料：Python ODBC**

您可以使用 **BlobService** 程式庫直接從 Jupyter Notebook 或 Python 程式中的 blob 讀取資料。

首先，匯入所需的封裝：

	import pandas as pd
	from pandas import Series, DataFrame
	import numpy as np
	import matplotlib.pyplot as plt
	from time import time
	import pyodbc
	import os
	from azure.storage import BlobService
	import tables
	import time
	import zipfile
	import random

然後插入您的 Azure Blob 帳戶認證並從 Blob 讀取資料：

    CONTAINERNAME = 'xxx'
	STORAGEACCOUNTNAME = 'xxxx'
	STORAGEACCOUNTKEY = 'xxxxxxxxxxxxxxxx'
	BLOBNAME = 'nyctaxidataset/nyctaxitrip/trip_data_1.csv'
	localfilename = 'trip_data_1.csv'
	LOCALDIRECTORY = os.getcwd()
	LOCALFILE =  os.path.join(LOCALDIRECTORY, localfilename)

	#download from blob
	t1 = time.time()
	blob_service = BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
	blob_service.get_blob_to_path(CONTAINERNAME,BLOBNAME,LOCALFILE)
	t2 = time.time()
	print(("It takes %s seconds to download "+BLOBNAME) % (t2 - t1))

	#unzipping downloaded files if needed
	#with zipfile.ZipFile(ZIPPEDLOCALFILE, "r") as z:
	#    z.extractall(LOCALDIRECTORY)

	df1 = pd.read_csv(LOCALFILE, header=0)
	df1.columns = ['medallion','hack_license','vendor_id','rate_code','store_and_fwd_flag','pickup_datetime','dropoff_datetime','passenger_count','trip_time_in_secs','trip_distance','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude']
	print 'the size of the data is: %d rows and  %d columns' % df1.shape
	
資料以資料框架的形式讀入：

![IPNB\_data\_readin](./media/machine-learning-data-science-vm-do-ten-things/IPNB_data_readin.PNG)


### Azure 資料湖

Azure 資料湖儲存體是巨量資料分析工作負載的超大規模儲存機制，與 Hadoop 分散式檔案系統 (HDFS) 相容。它適用於 Hadoop 生態系統以及 Azure 資料湖分析。我們會示範如何將資料移至 Azure 資料湖存放區，並使用 Azure 資料湖分析執行分析。

**必要條件**

- 在 [Azure 入口網站](http://portal.azure.com)中建立 Azure 資料湖分析。

![Azure\_Data\_Lake\_Create\_v2](./media/machine-learning-data-science-vm-do-ten-things/Azure_Data_Lake_Create_v2.png)


- 在此[連結](https://www.microsoft.com/download/details.aspx?id=49504)找到的 **Visual Studio** 中的 **Azure 資料湖工具**，已安裝於虛擬機器上的 Visual Studio Community Edition。啟動 Visual Studio 並登入您的 Azure 訂用帳戶之後，您會在 Visual Studio 的左面板中看到 Azure 分析資料帳戶和儲存體。 

![Azure\_Data\_Lake\_PlugIn\_v2](./media/machine-learning-data-science-vm-do-ten-things/Azure_Data_Lake_PlugIn_v2.PNG)


**將資料從 VM 移至資料湖：Azure 資料湖總管**

您可以使用 **Azure 資料湖總管**，將資料從虛擬機器中的本機檔案上傳至資料湖儲存體。

![Azure\_Data\_Lake\_UploadData](./media/machine-learning-data-science-vm-do-ten-things/Azure_Data_Lake_UploadData.PNG)

您也可以建立資料管線，將您的資料移動生產化，或從 Azure 資料湖使用 [Azure 資料 Factory(ADF)](https://azure.microsoft.com/services/data-factory/)。我們會將您帶到這篇[文章](https://azure.microsoft.com/blog/creating-big-data-pipelines-using-azure-data-lake-and-azure-data-factory/)，引導您完成建立資料管線的步驟。

**將資料從 Azure Blob 讀取至資料湖：U-SQL**

如果您的資料位於 Azure Blob 儲存體中，您可以在 U-SQL 查詢中從 Azure 儲存體 Blob 直接讀取資料。撰寫 U-SQL 查詢之前，請確定您的 Blob 儲存體帳戶已連結到您的 Azure 資料湖。移至 Azure 入口網站，尋找您的 Azure 資料湖分析儀表板，按一下 [新增資料來源]，選取 [Azure 儲存體] 做為儲存體類型，並插入您的 Azure 儲存體帳戶名稱和金鑰。然後您可以參考儲存體帳戶中儲存的資料。

![](./media/machine-learning-data-science-vm-do-ten-things/Link_Blob_to_ADLA_v2.PNG)


您可以在 Visual Studio 中，從 Blob 儲存體讀取資料、進行一些資料操作、功能工程，以及將結果資料輸出至 Azure 資料湖或 Azure Blob 儲存體。當您參考 Blob 儲存體中的資料時，請使用 ****wasb://**；當您參考 Azure 資料湖中的資料時，請使用 ****swbhdfs://**

![](./media/machine-learning-data-science-vm-do-ten-things/USQL_Read_Blob_v2.PNG)

您可以在 Visual Studio 中，使用下列 U-SQL 查詢：

	@a =
	    EXTRACT medallion string,
	            hack_license string,
	            vendor_id string,
	            rate_code string,
	            store_and_fwd_flag string,
	            pickup_datetime string,
	            dropoff_datetime string,
	            passenger_count int,
	            trip_time_in_secs double,
	            trip_distance double,
	            pickup_longitude string,
	            pickup_latitude string,
	            dropoff_longitude string,
	            dropoff_latitude string
	
	    FROM "wasb://<Container name>@<Azure Blob Storage Account Name>.blob.core.windows.net/<Input Data File Name>"
	    USING Extractors.Csv();
	
	@b = 
	    SELECT vendor_id, 
	    COUNT(medallion) AS cnt_medallion,
	    SUM(passenger_count) AS cnt_passenger,
	    AVG(trip_distance) AS avg_trip_dist,
	    MIN(trip_distance) AS min_trip_dist,
	    MAX(trip_distance) AS max_trip_dist,
	    AVG(trip_time_in_secs) AS avg_trip_time
	    FROM @a
	    GROUP BY vendor_id;
	
	OUTPUT @b   
	TO "swebhdfs://<Azure Data Lake Storage Account Name>.azuredatalakestore.net/<Folder Name>/<Output Data File Name>"
	USING Outputters.Csv();
	
	OUTPUT @b   
	TO "wasb://<Container name>@<Azure Blob Storage Account Name>.blob.core.windows.net/<Output Data File Name>"
	USING Outputters.Csv();
	


將查詢提交至伺服器之後，會顯示一個圖表來顯示您的作業狀態。

![](./media/machine-learning-data-science-vm-do-ten-things/USQL_Job_Status.PNG)


**查詢資料湖中的資料：U-SQL**

在資料集擷取到 Azure 資料湖之後，您可以使用 [U-SQL 語言](../data-lake-analytics/data-lake-analytics-u-sql-get-started.md)來查詢和探索資料。U-SQL 語言類似於 T-SQL，但結合了 C# 的一些功能，以便使用者撰寫自訂的模組、使用者定義的功能等。您可以在上一個步驟中使用指令碼。

將查詢提交到伺服器之後，很快就可以在 [Azure 資料湖總管] 中找到 tripdata\_summary.CSV，以滑鼠右鍵按一下該檔案即可預覽資料。

![](./media/machine-learning-data-science-vm-do-ten-things/USQL_create_summary.png)

若要查看檔案資訊：

![](./media/machine-learning-data-science-vm-do-ten-things/USQL_tripdata_summary.png)


### HDInsight Hadoop 叢集

Azure HDInsight 是在雲端上的受管理 Apache Hadoop、Spark、HBase 和 Storm 服務。您可以輕鬆地從資料科學虛擬機器使用 Azure HDInsight 叢集。

**必要條件**

- 從 [Azure 入口網站](http://portal.azure.com)建立 Azure Blob 儲存體帳戶。此儲存體帳戶用來儲存 HDInsight 叢集的資料。

![](./media/machine-learning-data-science-vm-do-ten-things/Create_Azure_Blob.PNG)

- 從 [Azure 入口網站](machine-learning-data-science-customize-hadoop-cluster.md)自訂 Azure HDInsight Hadoop 叢集

  - 您必須在建立時，將所建立的儲存體帳戶與 HDInsight 叢集連結。此儲存體帳戶用於存取可以在叢集內處理的資料。
	
![](./media/machine-learning-data-science-vm-do-ten-things/Create_HDI_v4.PNG)

  - 建立後，您必須對叢集的前端節點啟用**遠端存取**。請記住您在此處指定的遠端存取認證 (與建立時指定叢集的不同)：您在以下會需要該資訊。

![](./media/machine-learning-data-science-vm-do-ten-things/Create_HDI_dashboard_v3.PNG)

  - 建立 Azure ML 工作區。此 ML 工作區中，將會儲存您的機器學習服務實驗。在下列螢幕擷取畫面所示的入口網站中選取反白顯示的選項。 

![](./media/machine-learning-data-science-vm-do-ten-things/Create_ML_Space.PNG)


  - 然後輸入您的 Azure ML 工作區的參數

![](./media/machine-learning-data-science-vm-do-ten-things/Create_ML_Space_step2_v2.PNG)

  - 使用 IPython Notebook 上傳資料。先匯入所需的封裝、插入認證在儲存體帳戶中建立資料庫，然後將資料載入 HDI 叢集。 


		#Import required Packages
		import pyodbc
		import time as time
		import json
		import os
		import urllib
		import urllib2
		import warnings
		import re
		import pandas as pd
		import matplotlib.pyplot as plt
		from azure.storage.blob import BlobService
		warnings.filterwarnings("ignore", category=UserWarning, module='urllib2')


		#Create the connection to Hive using ODBC
		SERVER_NAME='xxx.azurehdinsight.net'
		DATABASE_NAME='nyctaxidb'
		USERID='xxx'
		PASSWORD='xxxx'
		DB_DRIVER='Microsoft Hive ODBC Driver'
		driver = 'DRIVER={' + DB_DRIVER + '}'
		server = 'Host=' + SERVER_NAME + ';Port=443'
		database = 'Schema=' + DATABASE_NAME
		hiveserv = 'HiveServerType=2'
		auth = 'AuthMech=6'
		uid = 'UID=' + USERID 
		pwd = 'PWD=' + PASSWORD
		CONNECTION_STRING = ';'.join([driver,server,database,hiveserv,auth,uid,pwd])
		connection = pyodbc.connect(CONNECTION_STRING, autocommit=True)
		cursor=connection.cursor()


		#Create Hive database and tables
		queryString = "create database if not exists nyctaxidb;"
		cursor.execute(queryString)
		
		queryString = """
		                create external table if not exists nyctaxidb.trip
		                ( 
		                    medallion string, 
		                    hack_license string,
		                    vendor_id string, 
		                    rate_code string, 
		                    store_and_fwd_flag string, 
		                    pickup_datetime string, 
		                    dropoff_datetime string, 
		                    passenger_count int, 
		                    trip_time_in_secs double, 
		                    trip_distance double, 
		                    pickup_longitude double, 
		                    pickup_latitude double, 
		                    dropoff_longitude double, 
		                    dropoff_latitude double)  
		                PARTITIONED BY (month int) 
		                ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\\n'
		                STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/trip' TBLPROPERTIES('skip.header.line.count'='1');
		            """
		cursor.execute(queryString)
		
		queryString = """
		                create external table if not exists nyctaxidb.fare 
		                ( 
		                    medallion string, 
		                    hack_license string, 
		                    vendor_id string, 
		                    pickup_datetime string, 
		                    payment_type string, 
		                    fare_amount double, 
		                    surcharge double,
		                    mta_tax double,
		                    tip_amount double,
		                    tolls_amount double,
		                    total_amount double)
		                PARTITIONED BY (month int) 
		                ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\\n'
		                STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/fare' TBLPROPERTIES('skip.header.line.count'='1');
		            """
		cursor.execute(queryString)
	
	
		#Upload data from blob storage to HDI cluster
		for i in range(1,13):
		    queryString = "LOAD DATA INPATH 'wasb:///nyctaxitripraw2/trip_data_%d.csv' INTO TABLE nyctaxidb2.trip PARTITION (month=%d);"%(i,i)
		    cursor.execute(queryString)
		    queryString = "LOAD DATA INPATH 'wasb:///nyctaxifareraw2/trip_fare_%d.csv' INTO TABLE nyctaxidb2.fare PARTITION (month=%d);"%(i,i)  
		    cursor.execute(queryString)


- 或者，您可以遵循此[逐步解說](machine-learning-data-science-process-hive-walkthrough.md)將 NYC 計程車資料上傳至 HDI 叢集。主要步驟包括：

	- AzCopy：從公用 Blob 將已壓縮的 CSV 下載到本機資料夾
	- AzCopy：從本機資料夾將已解壓縮的 CSV 上傳至 HDI 叢集
	- 登入 Hadoop 叢集的前端節點，並準備進行探索資料分析

在資料載入至 HDI 叢集之後，您可以在 Azure 儲存體總管中檢查您的資料。而您會在 HDI 叢集中建立資料庫 nyctaxidb。

![](./media/machine-learning-data-science-vm-do-ten-things/Upload_Data_to_HDI_cluster_Azure_Explorer.PNG)


**資料探索：Python 中的 Hive 查詢**

因為資料位於 Hadoop 叢集中，您可以使用 pyodbc 封裝連接到 Hadoop 叢集，並使用 Hive 查詢資料庫以便進行探索和設計功能。您可以檢視在先決條件步驟中建立的現有資料表。

	queryString = """
	    show tables in nyctaxidb2;
	    """
	pd.read_sql(queryString,connection)


![](./media/machine-learning-data-science-vm-do-ten-things/Python_View_Existing_Tables_Hive_v3.PNG)

讓我們看一下每個月的記錄筆數以及車程資料表中已付小費或未付小費的頻率：

	queryString = """
	    select month, count(*) from nyctaxidb.trip group by month;
	    """
	results = pd.read_sql(queryString,connection)
	
	%matplotlib inline
	
	results.columns = ['month', 'trip_count']
	df = results.copy()
	df.index = df['month']
	df['trip_count'].plot(kind='bar')


![](./media/machine-learning-data-science-vm-do-ten-things/Exploration_Number_Records_by_Month_v3.PNG)


	queryString = """
	    SELECT tipped, COUNT(*) AS tip_freq 
	    FROM 
	    (
	        SELECT if(tip_amount > 0, 1, 0) as tipped, tip_amount
	        FROM nyctaxidb.fare
	    )tc
	    GROUP BY tipped;
	    """
	results = pd.read_sql(queryString,connection)
	
	results.columns = ['tipped', 'trip_count']
	df = results.copy()
	df.index = df['tipped']
	df['trip_count'].plot(kind='bar')


![](./media/machine-learning-data-science-vm-do-ten-things/Exploration_Frequency_tip_or_not_v3.PNG)

我們也可以計算上車位置與下車位置之間的距離，然後再與車程距離比較。

	queryString = """
	                select pickup_longitude, pickup_latitude, dropoff_longitude, dropoff_latitude, trip_distance, trip_time_in_secs,
	                    3959*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
	                    *radians(180)/180/2),2)-cos(pickup_latitude*radians(180)/180)
	                    *cos(dropoff_latitude*radians(180)/180)*pow(sin((dropoff_longitude-pickup_longitude)*radians(180)/180/2),2)))
	                    /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*radians(180)/180/2),2)
	                    +cos(pickup_latitude*radians(180)/180)*cos(dropoff_latitude*radians(180)/180)*
	                    pow(sin((dropoff_longitude-pickup_longitude)*radians(180)/180/2),2))) as direct_distance 
	                    from nyctaxidb.trip 
	                    where month=1 
	                        and pickup_longitude between -90 and -30
	                        and pickup_latitude between 30 and 90
	                        and dropoff_longitude between -90 and -30
	                        and dropoff_latitude between 30 and 90;
	            """
	results = pd.read_sql(queryString,connection)
	results.head(5)


![](./media/machine-learning-data-science-vm-do-ten-things/Exploration_compute_pickup_dropoff_distance_v2.PNG)

	results.columns = ['pickup_longitude', 'pickup_latitude', 'dropoff_longitude', 
	                   'dropoff_latitude', 'trip_distance', 'trip_time_in_secs', 'direct_distance']
	df = results.loc[results['trip_distance']<=100] #remove outliers
	df = df.loc[df['direct_distance']<=100] #remove outliers
	plt.scatter(df['direct_distance'], df['trip_distance'])


![](./media/machine-learning-data-science-vm-do-ten-things/Exploration_direct_distance_trip_distance_v2.PNG)


現在讓我們準備縮減取樣 (1%) 資料集，以便進行模型分析。我們可以在 AML 讀取器模組中使用此資料。


		queryString = """
	    create  table if not exists nyctaxi_downsampled_dataset_testNEW (
	    medallion string,
	    hack_license string,
	    vendor_id string,
	    rate_code string,
	    store_and_fwd_flag string,
	    pickup_datetime string,
	    dropoff_datetime string,
	    pickup_hour string,
	    pickup_week string,
	    weekday string,
	    passenger_count int,
	    trip_time_in_secs double,
	    trip_distance double,
	    pickup_longitude double,
	    pickup_latitude double,
	    dropoff_longitude double,
	    dropoff_latitude double,
	    direct_distance double,
	    payment_type string,
	    fare_amount double,
	    surcharge double,
	    mta_tax double,
	    tip_amount double,
	    tolls_amount double,
	    total_amount double,
	    tipped string,
	    tip_class string
	    )
	    row format delimited fields terminated by ','
	    lines terminated by '\\n'
	    stored as textfile;
		"""
		cursor.execute(queryString)

		--- now insert contents of the join into the above internal table

		queryString = """
	    insert overwrite table nyctaxi_downsampled_dataset_testNEW
	    select
	    t.medallion,
	    t.hack_license,
	    t.vendor_id,
	    t.rate_code,
	    t.store_and_fwd_flag,
	    t.pickup_datetime,
	    t.dropoff_datetime,
	    hour(t.pickup_datetime) as pickup_hour,
	    weekofyear(t.pickup_datetime) as pickup_week,
	    from_unixtime(unix_timestamp(t.pickup_datetime, 'yyyy-MM-dd HH:mm:ss'),'u') as weekday,
	    t.passenger_count,
	    t.trip_time_in_secs,
	    t.trip_distance,
	    t.pickup_longitude,
	    t.pickup_latitude,
	    t.dropoff_longitude,
	    t.dropoff_latitude,
	    t.direct_distance,
	    f.payment_type,
	    f.fare_amount,
	    f.surcharge,
	    f.mta_tax,
	    f.tip_amount,
	    f.tolls_amount,
	    f.total_amount,
	    if(tip_amount>0,1,0) as tipped,
	    if(tip_amount=0,0,
	    if(tip_amount>0 and tip_amount<=5,1,
	    if(tip_amount>5 and tip_amount<=10,2,
	    if(tip_amount>10 and tip_amount<=20,3,4)))) as tip_class
	    from
	    (
	    select
	    medallion,
	    hack_license,
	    vendor_id,
	    rate_code,
	    store_and_fwd_flag,
	    pickup_datetime,
	    dropoff_datetime,
	    passenger_count,
	    trip_time_in_secs,
	    trip_distance,
	    pickup_longitude,
	    pickup_latitude,
	    dropoff_longitude,
	    dropoff_latitude,
	    3959*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
	    radians(180)/180/2),2)-cos(pickup_latitude*radians(180)/180)
	    *cos(dropoff_latitude*radians(180)/180)*pow(sin((dropoff_longitude-pickup_longitude)*radians(180)/180/2),2)))
	    /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*radians(180)/180/2),2)
	    +cos(pickup_latitude*radians(180)/180)*cos(dropoff_latitude*radians(180)/180)*pow(sin((dropoff_longitude-pickup_longitude)*radians(180)/180/2),2))) as direct_distance,
	    rand() as sample_key
	
	    from trip
	    where pickup_latitude between 30 and 90
	        and pickup_longitude between -90 and -30
	        and dropoff_latitude between 30 and 90
	        and dropoff_longitude between -90 and -30
	    )t
	    join
	    (
	    select
	    medallion,
	    hack_license,
	    vendor_id,
	    pickup_datetime,
	    payment_type,
	    fare_amount,
	    surcharge,
	    mta_tax,
	    tip_amount,
	    tolls_amount,
	    total_amount
	    from fare
	    )f
	    on t.medallion=f.medallion and t.hack_license=f.hack_license and t.pickup_datetime=f.pickup_datetime
	    where t.sample_key<=0.01
		"""
		cursor.execute(queryString)

不久之後，您可以看見資料已載入 Hadoop 叢集中：
		
	queryString = """
	    select * from nyctaxi_downsampled_dataset limit 10;
	    """
	cursor.execute(queryString)
	pd.read_sql(queryString,connection)


![](./media/machine-learning-data-science-vm-do-ten-things/DownSample_Data_For_Modeling_v2.PNG)


**使用 AML 從 HDI 讀取資料：讀取器模組**

您也可以在 AML Studio 中使用**讀取器**模組存取 Hadoop 叢集中的資料庫。插入 HDI 叢集和 Azure 儲存體帳戶的認證，您就能夠在 HDI 叢集中使用資料庫建置機器學習服務模型。

![](./media/machine-learning-data-science-vm-do-ten-things/AML_Reader_Hive.PNG)

接著可以檢視評分資料集：

![](./media/machine-learning-data-science-vm-do-ten-things/AML_Model_Results.PNG)


### Azure SQL 資料倉儲和資料庫

Azure SQL 資料倉儲是彈性的資料倉儲即服務，具有企業層級的 SQL Server 體驗。

您可以藉由遵循此[文章](../sql-data-warehouse/sql-data-warehouse-get-started-provision.md)中提供的指示，佈建您的 Azure SQL 資料倉儲。一旦您佈建 Azure SQL 資料倉儲，您可以使用這個[逐步解說](machine-learning-data-science-process-sqldw-walkthrough.md)進行資料上傳、探索和使用 SQL 資料倉儲中的資料建立模型。

#### Azure DocumentDB

Azure DocumentDB 是雲端中的 NoSQL 資料庫。它可讓您使用 JSON 等文件，並可讓您儲存和查詢文件。

您必須執行下列必要步驟，才能從 DSVM 存取 DocumentDB。

1. 安裝 DocumentDB Python SDK (從命令提示字元執行 ```pip install pydocumentdb```)
1. 從 [Azure 入口網站](https://portal.azure.com)建立 DocumentDB 帳戶和 Document DB 資料庫
1. 從[這裡](http://www.microsoft.com/downloads/details.aspx?FamilyID=cda7703a-2774-4c07-adcc-ad02ddc1a44d)下載「DocumentDB 移轉工具」並解壓縮至您所選的目錄
1. 使用下列命令參數或移轉工具 (DocumentDB 移轉工具安裝目錄中的 dtui.exe)，將儲存在[公用 Blob](https://cahandson.blob.core.windows.net/samples/volcano.json) 的 JSON 資料 (Volcano 資料) 匯入至 DocumentDB。輸入下面的來源和目標位置參數。 

	/s:JsonFile /s.Files:https://cahandson.blob.core.windows.net/samples/volcano.json /t:DocumentDBBulk /t.ConnectionString:AccountEndpoint=https://[DocDBAccountName].documents.azure.com:443/;AccountKey=[[KEY];Database=volcano /t.Collection:volcano1

一旦匯入資料，您即可移至 Jupyter 並開啟標題為 *DocumentDBSample* 且包含 python 程式碼的 Notebook，以存取 DocumentDB 及進行一些基本查詢。若要深入了解 DocumentDB，請參閱服務[文件頁面](https://azure.microsoft.com/documentation/learning-paths/documentdb/)。


## 8\.使用 Power BI Desktop 建立報表和儀表板 

讓我們將在 Power BI 的上述 DocumentDB 範例中看見的 Volcano JSON 檔案視覺化，以深入了解資料。在 [Power BI 文件](../documentdb/documentdb-powerbi-visualize.md)中可找到詳細的步驟。高階步驟如下：

1. 開啟 Power BI Desktop 並執行「取得資料」。將 URL 指定為：https://cahandson.blob.core.windows.net/samples/volcano.json
2. 您應該會看到匯入為清單的 JSON 記錄
3. 將清單轉換成資料表，以便 PowerBI 使用
4. 按一下展開圖示 (資料行左側具有「向左箭號與向右箭號」的圖示)，即可展開資料行
5. 請注意，該位置是「記錄」欄位。展開記錄，然後只選取座標。座標是清單資料行
6. 加入新的資料行，以將清單座標資料行轉換成逗號分隔的 LatLong 資料行，其使用公式 ```Text.From([coordinates]{1})&","&Text.From([coordinates]{0})``` 來串連座標清單欄位中的兩個元素。 
7. 最後將 ```Elevation``` 資料行轉換成 [十進位]，然後選取 [關閉] 和 [套用]。

除了上述步驟，您可以在 PowerBI 的 [進階編輯器] 中貼上出自上述步驟的下列程式碼，讓您以查詢語言撰寫資料轉換。


	let
	    Source = Json.Document(Web.Contents("https://cahandson.blob.core.windows.net/samples/volcano.json")),
	    #"Converted to Table" = Table.FromList(Source, Splitter.SplitByNothing(), null, null, ExtraValues.Error),
	    #"Expanded Column1" = Table.ExpandRecordColumn(#"Converted to Table", "Column1", {"Volcano Name", "Country", "Region", "Location", "Elevation", "Type", "Status", "Last Known Eruption", "id"}, {"Volcano Name", "Country", "Region", "Location", "Elevation", "Type", "Status", "Last Known Eruption", "id"}),
	    #"Expanded Location" = Table.ExpandRecordColumn(#"Expanded Column1", "Location", {"coordinates"}, {"coordinates"}),
	    #"Added Custom" = Table.AddColumn(#"Expanded Location", "LatLong", each Text.From([coordinates]{1})&","&Text.From([coordinates]{0})),
	    #"Changed Type" = Table.TransformColumnTypes(#"Added Custom",{{"Elevation", type number}})
	in
	    #"Changed Type"
		


您的 Power BI 資料模型中現在會有資料。您的 Power BI 桌面應該如下所示。

![](./media/machine-learning-data-science-vm-do-ten-things/PowerBIVolcanoData.png)

您可以開始使用資料模型來建立報告和視覺效果。您可以遵循此 [Power BI 文件](../documentdb/documentdb-powerbi-visualize.md#build-the-reports)中的步驟來建立報告。最後的結果會是如下所示的報告。

![Power BI Desktop 報告檢視 - Power BI 連接器](./media/machine-learning-data-science-vm-do-ten-things/power_bi_connector_pbireportview2.png)

## 9\.動態調整 DSVM 以符合您的專案需求

您可以相應增加和減少 DSVM 以符合您的專案需求。如果您在晚上或週末不需要使用 VM，從 [Azure 入口網站](https://portal.azure.com)就可以關閉 VM。

>[AZURE.NOTE]  如果您只使用 VM 上的作業系統關機按鈕，將需支付計算費用。

如果您需要處理一些大規模分析，而且需要更多 CPU 和/或記憶體和/或磁碟容量，您可以找到許多符合您的計算和預算需求的 VM 大小選擇 (從 CPU 核心、記憶體容量和磁碟類型 (包括固態硬碟機) 方面來看)。[Azure 虛擬機器價格](https://azure.microsoft.com/pricing/details/virtual-machines/)頁面會提供 VM 完整清單及其每小時的計算價格。

同樣地，如果您的 VM 處理容量需求降低 (例如：您將主要工作負載移至 Hadoop 或 Spark 叢集)，您可以從 [Azure 入口網站](https://portal.azure.com)移至您的 VM 執行個體設定來相應減少叢集。以下為螢幕擷取畫面。


![](./media/machine-learning-data-science-vm-do-ten-things/VMScaling.PNG)


## 10\.在虛擬機器上安裝其他工具

我們已封裝數個工具，我們相信這可以滿足許多常見的資料分析需求，並藉由避免必須逐一安裝和設定您的環境而節省您的時間，且只支付您所用的資源而節省您的成本。

您可以運用本文略述的其他 Azure 資料和分析資料服務，以增強您的分析環境。我們了解，在某些情況下您的需求可能需要額外的工具，包括一些專屬的協力廠商工具。您有完整的系統管理權限可在虛擬機器上安裝您所需的新工具。您也可以在 Python 和 R 中安裝其他未預先安裝的封裝。對於 Python，您可以使用 ```conda``` 或 ```pip```。對於 R，您可以在 R 主控台中使用 ```install.packages()```，或使用 IDE 並選擇 [封裝] -> [安裝封裝...]。

## 摘要
這些只是您可以在 Microsoft Data Science Virtual Machine 上可做的一些事情。您還可以做更多事情，讓它成為有效的分析環境。

<!---HONumber=AcomDC_0302_2016-------->