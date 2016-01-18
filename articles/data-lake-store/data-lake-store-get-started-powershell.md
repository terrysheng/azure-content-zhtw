<properties 
   pageTitle="開始使用資料湖存放區 | Azure" 
   description="使用 Azure PowerShell 建立資料湖存放區帳戶，並執行基本作業" 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="01/04/2016"
   ms.author="nitinme"/>

# 使用 Azure PowerShell 開始使用 Azure 資料湖分析存放區

> [AZURE.SELECTOR]
- [Using Portal](data-lake-store-get-started-portal.md)
- [Using PowerShell](data-lake-store-get-started-powershell.md)
- [Using .NET SDK](data-lake-store-get-started-net-sdk.md)
- [Using Azure CLI](data-lake-store-get-started-cli.md)
- [Using Node.js](data-lake-store-manage-use-nodejs.md)

了解如何使用 Azure PowerShell 建立 Azure 資料湖存放區帳戶並執行基本作業，例如建立資料夾、上傳和下載資料檔案、刪除您的帳戶等等。如需有關資料湖存放區的詳細資訊，請參閱[資料湖存放區概觀](data-lake-store-overview.md)。

## 必要條件

開始進行本教學課程之前，您必須具備下列條件：

- **Azure 訂用帳戶**。請參閱[取得 Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。
- **啟用您的 Azure 訂用帳戶**以使用資料湖存放區公開預覽版。請參閱[指示](data-lake-store-get-started-portal.md#signup)。


##安裝 Azure PowerShell 1.0 或更新版本

請參閱[搭配使用 Azure PowerShell 與 Azure 資源管理員](powershell-azure-resource-manager.md#prerequisites)的「必要條件」一節。

## 建立 Azure 資料湖存放區帳戶

1. 從您的桌面上開啟新的 Azure PowerShell 視窗，輸入下列程式碼片段登入 Azure 帳戶、設定訂用帳戶，然後註冊資料湖存放區提供者。系統提示您登入時，請使用其中一個訂用帳戶管理員/擁有者身分登入：

        # Log in to your Azure account
		Login-AzureRmAccount
        
		# List all the subscriptions associated to your account
		Get-AzureRmSubscription
		
		# Select a subscription 
		Set-AzureRmContext -SubscriptionId <subscription ID>
        
		# Register for Azure Data Lake Store
		Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.DataLakeStore" 


2. Azure 資料湖存放區帳戶與 Azure 資源群組相關聯。從建立 Azure 資源群組開始。

		$resourceGroupName = "<your new resource group name>"
    	New-AzureRmResourceGroup -Name $resourceGroupName -Location "East US 2"

	![建立 Azure 資源群組](./media/data-lake-store-get-started-powershell/ADL.PS.CreateResourceGroup.png "建立 Azure 資源群組")

2. 建立 Azure 資料湖存放區帳戶。您指定的名稱必須只包含小寫字母和數字。

		$dataLakeStoreName = "<your new Data Lake Store name>"
    	New-AzureRmDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStoreName -Location "East US 2"

	![建立 Azure 資料湖存放區帳戶](./media/data-lake-store-get-started-powershell/ADL.PS.CreateADLAcc.png "建立 Azure 資料湖存放區帳戶")

3. 確認已成功建立帳戶。

		Test-AzureRmDataLakeStoreAccount -Name $dataLakeStoreName

	輸出應為 **True**。

## 在您的 Azure 資料湖存放區中建立目錄結構

您可以在您的 Azure 資料湖存放區帳戶下建立用於管理與儲存資料的目錄。

1. 指定根目錄。

		$myrootdir = "/"

2. 在指定的根目錄下建立名為 **mynewdirectory** 的新目錄。

		New-AzureRmDataLakeStoreItem -Folder -AccountName $dataLakeStoreName -Path $myrootdir/mynewdirectory

3. 確認已成功建立新目錄。

		Get-AzureRmDataLakeStoreChildItem -AccountName $dataLakeStoreName -Path $myrootdir

	應該會顯示類似下面的輸出畫面：

	![確認目錄](./media/data-lake-store-get-started-powershell/ADL.PS.Verify.Dir.Creation.png "確認目錄")


## 將資料上傳至 Azure 資料湖存放區

您可以在根層級直接將資料上傳至資料湖存放區，或上傳至您在帳戶內建立的目錄。下列程式碼範例說明如何將一些範例資料上傳至您在上一節中建立的目錄 (**mynewdirectory**)。

如果您正在尋找一些可上傳的範例資料，您可以從 [Azure 資料湖 Git 儲存機制](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData)取得 **Ambulance Data** 資料夾。下載檔案並將它儲存在電腦的本機目錄上，例如 C:\\sampledata。

	Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path "C:\sampledata\vehicle1_09142014.csv" -Destination $myrootdir\mynewdirectory\vehicle1_09142014.csv


## 重新命名、下載與刪除資料湖存放區中的資料

若要重新命名檔案，請使用下列命令：

    Move-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $myrootdir\mynewdirectory\vehicle1_09142014.csv -Destination $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv

若要下載檔案，請使用下列命令：

	Export-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv -Destination "C:\sampledata\vehicle1_09142014_Copy.csv"

若要刪除檔案，請使用下列命令：

	Remove-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Paths $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv 
	
出現提示時，請輸入 **Y** 刪除項目。如果您要刪除多個檔案，可以提供所有的路徑並以逗號分隔。

	Remove-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Paths $myrootdir\mynewdirectory\vehicle1_09142014.csv, $myrootdir\mynewdirectoryvehicle1_09142014_Copy.csv

## 刪除 Azure 資料湖存放區帳戶

使用下列命令刪除資料湖存放區帳戶。

	Remove-AzureRmDataLakeStoreAccount -Name $dataLakeStoreName

出現提示時，請輸入 **Y** 刪除帳戶。


## 其他建立資料湖存放區帳戶的方法

- [使用入口網站開始使用資料湖存放區](data-lake-store-get-started-portal.md)
- [使用 .NET SDK 開始使用資料湖存放區](data-lake-store-get-started-net-sdk.md)
- [使用 Azure CLI 開始使用資料湖存放區](data-lake-store-get-started-cli.md)


## 後續步驟

- [保護資料湖存放區中的資料](data-lake-store-secure-data.md)
- [搭配資料湖存放區使用 Azure 資料湖分析](data-lake-analytics-get-started-portal.md)
- [搭配資料湖存放區使用 Azure HDInsight](data-lake-store-hdinsight-hadoop-use-portal.md)

<!---HONumber=AcomDC_0107_2016-->