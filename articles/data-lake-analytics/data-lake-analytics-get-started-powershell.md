<properties 
   pageTitle="透過 Azure PowerShell 開始使用 Azure 資料湖分析 | Azure" 
   description="了解如何透過 Azure PowerShell 建立資料湖存放區帳戶、使用 U-SQL 建立資料湖分析工作，以及提交工作。" 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="mumian" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="12/01/2015"
   ms.author="jgao"/>

# 教學課程：透過 Azure PowerShell 開始使用 Azure 資料湖分析

[AZURE.INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

了解如何透過 Azure PowerShell 建立 Azure 資料湖分析帳戶、在 [U-SQL](data-lake-analytics-u-sql-get-started.md) 中定義資料湖分析工作，以及將工作提交至資料湖分析帳戶。如需有關資料湖分析的詳細資訊，請參閱 [Azure 資料湖分析概觀](data-lake-analytics-overview.md)。

在本教學課程中，您將會開發一個工作以讀取定位鍵分隔值 (TSV) 檔案，並將該檔案轉換為逗點分隔值 (CSV) 檔案。若要使用其他支援的工具進行同一個教學課程，請按一下此區段最上方的索引標籤。

**基本資料湖分析程序：**

![Azure 資料湖分析程序流程圖](./media/data-lake-analytics-get-started-portal/data-lake-analytics-process.png)

1. 建立資料湖分析帳戶。
2. 準備來源資料。資料湖分析工作可讀取 Azure 資料湖存放區帳戶或 Azure Blob 儲存體帳戶資料中的資料。   
3. 開發 U SQL 指令碼。
4. 將工作 (U-SQL 指令碼) 提交至資料湖分析帳戶。此工作會讀取來源資料、依照 U-SQL 指令碼中的指示處理資料，然後將輸出儲存至資料湖存放區帳戶或 Blob 儲存體帳戶。


**必要條件**

開始進行本教學課程之前，您必須具備下列條件：

- **Azure 訂用帳戶**。請參閱[取得 Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。
- **具有 Azure PowerShell 的工作站**。請參閱[搭配使用 Azure PowerShell 與 Azure 資源管理員](powershell-azure-resource-manager.md#prerequisites)的「必要條件」一節。
	
##建立資料湖分析帳戶

您必須擁有資料湖分析帳戶，才能執行工作。若要建立資料湖分析帳戶，您必須指定下列項目：

- **Azure 資源群組**：資料湖分析帳戶必須建立在 Azure 資源群組內。[Azure 資源管理員](resource-group-overview.md)可讓您將應用程式中的資源做為群組使用。您可以透過單一、協調的作業來部署、更新或刪除應用程式的所有資源。  

	若要列舉您訂用帳戶中的資源群組：
    
    	Get-AzureRmResourceGroup
    
	若要建立新的資源群組：

    	New-AzureRmResourceGroup `
			-Name "<Your resource group name>" `
			-Location "<Azure Data Center>" # For example, "East US 2"

- **資料湖分析帳戶名稱**
- **位置**：其中一個支援資料湖分析的 Azure 資料中心。
- **預設資料湖帳戶**：每個資料湖分析帳戶都有一個預設的資料湖帳戶。

	若要建立新的資料湖帳戶：

	    New-AzureRmDataLakeStoreAccount `
	        -ResourceGroupName "<Your Azure resource group name>" `
	        -Name "<Your Data Lake account name>" `
	        -Location "<Azure Data Center>"  # For example, "East US 2"

	> [AZURE.NOTE]資料湖帳戶名稱只能包含小寫字母和數字。



**建立資料湖分析帳戶**

1. 從您的 Windows 工作站開啟 PowerShell ISE。
2. 執行下列指令碼：

		$resourceGroupName = "<ResourceGroupName>"
		$dataLakeStoreName = "<DataLakeAccountName>"
		$dataLakeAnalyticsName = "<DataLakeAnalyticsAccountName>"
		$location = "East US 2"
		
		Write-Host "Create a resource group ..." -ForegroundColor Green
		New-AzureRmResourceGroup `
			-Name  $resourceGroupName `
			-Location $location
		
		Write-Host "Create a Data Lake account ..."  -ForegroundColor Green
		New-AzureRmDataLakeStoreAccount `
			-ResourceGroupName $resourceGroupName `
			-Name $dataLakeStoreName `
			-Location $location 
		
		Write-Host "Create a Data Lake Analytics account ..."  -ForegroundColor Green
		New-AzureRmDataLakeAnalyticsAccount `
			-Name $dataLakeAnalyticsName `
			-ResourceGroupName $resourceGroupName `
			-Location $location `
			-DefaultDataLake $dataLakeStoreName
		
		Write-Host "The newly created Data Lake Analytics account ..."  -ForegroundColor Green
		Get-AzureRmDataLakeAnalyticsAccount `
			-ResourceGroupName $resourceGroupName `
			-Name $dataLakeAnalyticsName  

##將資料上傳至資料湖

在本教學課程中，您將會處理一些搜尋記錄檔。搜尋記錄檔可以儲存在資料湖存放區或 Azure Blob 儲存體中。

系統已將搜尋記錄檔範例複製到公用 Azure Blob 容器。使用下列 PowerShell 指令碼將該檔案下載到您的工作站，然後再將其上傳到您資料湖分析帳戶的預設資料湖存放區帳戶中。

	$dataLakeStoreName = "<The default Data Lake Store account name>"
	
	$localFolder = "C:\Tutorials\Downloads" # A temp location for the file. 
	$storageAccount = "adltutorials"  # Don't modify this value.
	$container = "adls-sample-data"  #Don't modify this value.

	# Create the temp location	
	New-Item -Path $localFolder -ItemType Directory -Force 

	# Download the sample file from Azure Blob storage
	$context = New-AzureStorageContext -StorageAccountName $storageAccount -Anonymous
	$blobs = Azure\Get-AzureStorageBlob -Container $container -Context $context
	$blobs | Get-AzureStorageBlobContent -Context $context -Destination $localFolder

	# Upload the file to the default Data Lake Store account	
	Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $localFolder"SearchLog.tsv" -Destination "/Samples/Data/SearchLog.tsv"

下列 PowerShell 指令碼示範如何取得資料湖分析帳戶的預設資料湖存放區名稱：


	$resourceGroupName = "<ResourceGroupName>"
	$dataLakeAnalyticsName = "<DataLakeAnalyticsAccountName>"
	$dataLakeStoreName = (Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticName).Properties.DefaultDataLakeAccount

>[AZURE.NOTE]Azure 入口網站的使用者介面，可讓使用者將資料檔案範例複製到預設的資料湖存放區帳戶。如需相關指示，請參閱[使用 Azure 入口網站開始使用 Azure 資料湖分析](data-lake-analytics-get-started-portal.md#upload-data-to-the-default-data-lake-store-account)。

資料湖分析也可存取 Azure Blob 儲存體。如需有關上傳資料至 Azure Blob 儲存體的資訊，請參閱[搭配使用 Azure PowerShell 與 Azure 儲存體](storage-powershell-guide-full.md)。

##提交資料湖分析工作

資料湖分析工作是以 U-SQL 語言撰寫。若要深入了解 U-SQL，請參閱[開始使用 U-SQL 語言](data-lake-analytics-u-sql-get-started.md)和 [U-SQL 語言參考](http://go.microsoft.com/fwlink/?LinkId=691348)。

**建立資料湖分析工作指令碼**

- 使用下列 U-SQL 指令碼建立文字檔，並將該檔案儲存到您的工作站：

        @searchlog =
            EXTRACT UserId          int,
                    Start           DateTime,
                    Region          string,
                    Query           string,
                    Duration        int?,
                    Urls            string,
                    ClickedUrls     string
            FROM "/Samples/Data/SearchLog.tsv"
            USING Extractors.Tsv();
        
        OUTPUT @searchlog   
            TO "/Output/SearchLog-from-Data-Lake.csv"
        USING Outputters.Csv();

	此 U-SQL 指令碼會使用 **Extractors.Tsv()** 讀取來源資料檔案，然後使用 **Outputters.Csv()** 建立 CSV 檔案。
    
    除非您將來源檔案複製到其他位置，否則請勿修改這兩個路徑。資料湖分析將建立輸出資料夾 (若尚未建立)。
	
	使用儲存在預設資料湖帳戶中檔案的相對路徑，是比較容易的方法。您也可以使用絕對路徑。例如
    
        adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv
        
    您必須使用絕對路徑存取連結儲存體帳戶中的檔案。儲存在連結 Azure 儲存體帳戶中之檔案的語法是：
    
        wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv

    >[AZURE.NOTE]目前不支援具有公用 Blob 或公用容器存取權限的 Azure Blob 容器。
    
	
**提交工作**

1. 從您的 Windows 工作站開啟 PowerShell ISE。
2. 執行下列指令碼：

		$dataLakeAnalyticsName = "<DataLakeAnalyticsAccountName>"
		$usqlScript = "c:\tutorials\data-lake-analytics\copyFile.usql"
		
		Submit-AzureRmDataLakeAnalyticsJob -Name "convertTSVtoCSV" -AccountName $dataLakeAnalyticsName –ScriptPath $usqlScript 
		                
		While (($t = Get-AzureRmDataLakeAnalyticsJob -AccountName $dataLakeAnalyticsName -JobId $job.JobId).State -ne "Ended"){
			Write-Host "Job status: "$t.State"..."
			Start-Sleep -seconds 5
		}
		
		Get-AzureRmDataLakeAnalyticsJob -AccountName $dataLakeAnalyticsName -JobId $job.JobId

	在指令碼中，U-SQL 指令碼檔案儲存在 c:\\tutorials\\data-lake-analytics\\copyFile.usql 中。相應地更新檔案路徑。
 
工作完成之後，您可以使用下列 Cmdlet 列出該檔案，並下載檔案：
	
	$resourceGroupName = "<Resource Group Name>"
	$dataLakeAnalyticName = "<Data Lake Analytic Account Name>"
	$destFile = "C:\tutorials\data-lake-analytics\SearchLog-from-Data-Lake.csv"
	
	$dataLakeStoreName = (Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticName).Properties.DefaultDataLakeAccount
	
	Get-AzureRmDataLakeStoreChildItem -AccountName $dataLakeStoreName -path "/Output"
	
	Export-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path "/Output/SearchLog-from-Data-Lake.csv" -Destination $destFile

## 另請參閱

- 若要使用其他工具檢視同一個教學課程，請按一下頁面最上方的索引標籤選取器。
- 若要了解更複雜的查詢，請參閱[使用 Azure 資料湖分析分析網站記錄檔](data-lake-analytics-analyze-weblogs.md)。
- 若要開始開發 U-SQL 應用程式，請參閱[使用適用於 Visual Studio 的資料湖工具開發 U-SQL 指令碼](data-lake-analytics-data-lake-tools-get-started.md)。
- 若要了解 U-SQL，請參閱[開始使用 Azure 資料湖分析 U-SQL 語言](data-lake-analytics-u-sql-get-started.md)。
- 針對管理工作，請參閱[使用 Azure 入口網站管理 Azure 資料湖分析](data-lake-analytics-manage-use-portal.md)。
- 若要取得資料湖分析概觀，請參閱 [Azure 資料湖分析概觀](data-lake-analytics-overview.md)。

<!---HONumber=AcomDC_0107_2016-->