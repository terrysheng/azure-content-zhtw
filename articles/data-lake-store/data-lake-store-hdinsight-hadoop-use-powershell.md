<properties 
   pageTitle="使用 PowerShell 設定 HDInsight 叢集與 Azure 資料湖存放區 |Azure" 
   description="使用 Azure PowerShell 設定和使用 HDInsight Hadoop 叢集與 Azure 資料湖" 
   services="data-lake" 
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
   ms.date="10/29/2015"
   ms.author="nitinme"/>

# 使用 Azure PowerShell 佈建 HDInsight 叢集與資料湖存放區

> [AZURE.SELECTOR]
- [Using Portal](data-lake-store-hdinsight-hadoop-use-portal.md)
- [Using PowerShell](data-lake-store-hdinsight-hadoop-use-powershell.md)


了解如何使用 Azure PowerShell 設定 HDInsight 叢集 (Hadoop、HBase 或 Storm) 以使用 Azure 資料湖存放區。此版本的一些重要考量：

* * **對於 Hadoop 和 Storm 叢集 (Windows 和 Linux)**，資料湖存放區只能做為額外的儲存體帳戶。這類叢集的預設儲存體帳戶仍是 Azure 儲存體 Blob (WASB)。

* **對於 HBase 叢集 (Windows 和 Linux)**，您可以使用資料湖存放區做為預設儲存體或額外的儲存體。


在本文中，我們佈建 Hadoop 叢集與資料湖存放區做為額外的儲存體。

使用 PowerShell 以設定 HDInsight 來搭配資料湖存放區使用，包含下列步驟：

* 建立 Azure 資料湖存放區
* 設定資料湖存放區以角色為基礎的存取的驗證
* 建立具有資料湖存放區驗證的 HDInsight 叢集
* 在叢集上執行測試工作

## 先決條件

開始進行本教學課程之前，您必須具備下列條件：

- **Azure 訂用帳戶**。請參閱[取得 Azure 免費試用](https://azure.microsoft.com/zh-TW/pricing/free-trial/)。
- **啟用您的 Azure 訂用帳戶**以便公開預覽資料湖存放區。請參閱[指示](data-lake-store-get-started-portal.md#signup)。
- **Windows SDK**。您可以從[這裡](https://dev.windows.com/zh-TW/downloads)安裝。您使用它來建立安全性憑證。
- **Azure PowerShell 1.0**。從[這裡](https://github.com/MicrosoftBigData/AzureDataLake/releases/download/AzurePowerShell_2015_10_30/AzurePowerShell.msi)安裝。
 

## 建立 Azure 資料湖存放區

依照這些步驟建立資料湖存放區。

1. 從您的桌面開啟新的 Azure PowerShell 視窗，並輸入下列程式碼片段。系統提示您登入時，請以 admininistrators/owner 其中一個訂用帳戶身分登入：

        # Log in to your Azure account
		Login-AzureRmAccount
        
		# List all the subscriptions associated to your account
		Get-AzureRmSubscription
		
		# Select a subscription 
		Set-AzureRmContext -SubscriptionId <subscription ID>

		# Register for Data Lake Store
		Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

3. Azure 資料湖存放區帳戶與 Azure 資源群組相關聯。從建立 Azure 資源群組開始。

		$resourceGroupName = "<your new resource group name>"
    	New-AzureRmResourceGroup -Name $resourceGroupName -Location "East US 2"

	![建立 Azure 資源群組](./media/data-lake-store-hdinsight-hadoop-use-powershell/ADL.PS.CreateResourceGroup.png "建立 Azure 資源群組")

2. 建立 Azure 資料湖存放區帳戶。您指定的帳戶名稱必須只包含小寫字母和數字。

		$dataLakeStoreName = "<your new Data Lake Store name>"
    	New-AzureRmDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStoreName -Location "East US 2"

	![建立 Azure 資料湖帳戶](./media/data-lake-store-hdinsight-hadoop-use-powershell/ADL.PS.CreateADLAcc.png "建立 Azure 資料湖帳戶")

3. 確認已成功建立帳戶。

		Test-AzureRmDataLakeStoreAccount -Name $dataLakeStoreName

	此輸出應為 **True**。

4. 將一些範例資料上傳至 Azure 資料湖。我們將在本文稍後使用這個項目來確認資料可以從 HDInsight 叢集存取。如果您要尋找一些可上傳的範例資料，您可以從 [Azure 資料湖 Git 儲存機制](https://github.com/MicrosoftBigData/ProjectKona/tree/master/SQLIPSamples/SampleData/AmbulanceData)取得 **Ambulance Data** 資料夾。

		
		$myrootdir = "/"
		Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path "C:<path to data>\vehicle1_09142014.csv" -Destination $myrootdir\vehicle1_09142014.csv


## 設定資料湖存放區以角色為基礎的存取的驗證

每一個 Azure 訂閱都與 Azure Active Directory 相關聯。透過 Azure 入口網站或是 Azure 資源管理員 API 來存取訂用帳戶資源的使用者與服務，都必須先向 Azure Active Directory 進行驗證。您可以在 Azure 資源上為 Azure 訂用帳戶和服務指派適當的角色，以授與其存取權限。對於服務，服務主體會識別 Azure Active Directory (AAD) 中的服務。本章節將說明如何將 Azure 資源 (您稍早建立的 Azure 資料湖存放區帳戶) 的存取權授與像是 HDInsight 的應用程式服務，方法是建立應用程式的服務主體，並透過 Azure PowerShell 將角色指派給它。

若要設定 Azure 資料湖的 Active Directory 驗證，您必須執行下列工作。

* 建立自我簽署憑證
* 在 Azure Active Directory 和服務主體中建立應用程式

### 建立自我簽署憑證

進行本節中的步驟之前，請確定您已安裝 [Windows SDK](https://dev.windows.com/zh-TW/downloads)。您也必須建立一個目錄，例如 **C:\\mycertdir**，憑證會在其中建立。

1. 在 PowerShell 視窗中，瀏覽至您安裝 Windows SDK 的位置 (通常是 `C:\Program Files (x86)\Windows Kits\10\bin\x86`，並使用 [MakeCert][makecert] 公用程式來建立自我簽署的憑證和私密金鑰。使用下列命令。

		$certificateFileDir = "<my certificate directory>"
		cd $certificateFileDir
		$startDate = (Get-Date).ToString('MM/dd/yyyy')
		$endDate = (Get-Date).AddDays(365).ToString('MM/dd/yyyy')

		makecert -sv mykey.pvk -n "cn=HDI-ADL-SP" CertFile.cer -b $startDate -e $endDate -r -len 2048

	系統會提示您輸入私密金鑰密碼。命令成功執行之後，您應該會在您指定的憑證目錄中看到 **CertFile.cer** 和 **mykey.pvk**。

4. 使用 [Pvk2Pfx][pvk2pfx] 公用程式將 MakeCert 建立的 .pvk 和 .cer 檔案轉換成 .pfx 檔案。執行下列命令。

		pvk2pfx -pvk mykey.pvk -spc CertFile.cer -pfx CertFile.pfx -po <password>

	系統提示時，輸入您稍早指定的私密金鑰密碼。您針對 **-po** 參數指定的值是與 .pfx 檔案相關聯的密碼。命令成功完成之後，您應該也會在您指定的憑證目錄中看到 CertFile.pfx。

###  建立 Azure Active Directory 和服務主體

在這一節中，您將執行相關步驟來建立 Azure Active Directory 應用程式的服務主體、指派角色給服務主體，並藉由提供憑證驗證為服務主體。執行下列命令以在 Azure Active Directory 中建立應用程式。

1. 在 PowerShell 主控台視窗中貼上下列 Cmdlet。請確定您針對 **-DisplayName** 屬性指定的值是唯一的。此外，**-HomePage** 和 **-IdentiferUris** 的值是預留位置值，不會驗證。 

		$certificateFilePath = "$certificateFileDir\CertFile.pfx"
		
		$password = Read-Host –Prompt "Enter the password" # This is the password you specified for the .pfx file
		
		$certificatePFX = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($certificateFilePath, $password)
		
		$rawCertificateData = $certificatePFX.GetRawCertData()
		
		$credential = [System.Convert]::ToBase64String($rawCertificateData)

		$application = New-AzureRmADApplication `
					-DisplayName "HDIADL" ` 
					-HomePage "https://contoso.com" `
					-IdentifierUris "https://mycontoso.com" `
					-KeyValue $credential  `
					-KeyType "AsymmetricX509Cert"  `
					-KeyUsage "Verify"  `
					-StartDate $startDate  `
					-EndDate $endDate

		$applicationId = $application.ApplicationId

2. 使用應用程式識別碼建立服務主體。

		$servicePrincipal = New-AzureRmADServicePrincipal -ApplicationId $applicationId
		
		$objectId = $servicePrincipal.Id

3. 將服務主體的存取權授與您稍早建立的資料湖存放區。
		
		Set-AzureRmDataLakeStoreItemAclEntry -AccountName $dataLakeStoreName -Path / -AceType User -Id $objectId -Permissions All

	系統提示時，輸入 **Y** 以確認。

## 建立具有資料湖存放區驗證的 HDInsight 叢集

在本節中，我們會建立 HDInsight Hadoop 叢集。對於此版本，HDInsight 叢集和資料湖存放區必須位於相同位置 (美國東部 2)。

1. 開始擷取訂用帳戶租用戶識別碼。稍後您將會需要此資訊。

		$tenantID = (Get-AzureRmContext).Tenant.TenantId

2. 在此版本中，對於 Hadoop 叢集，資料湖存放區只能做為叢集的額外儲存體。預設儲存體仍是 Azure 儲存體 Blob (WASB)。所以，我們要先建立叢集所需的儲存體帳戶和儲存體容器。

		# Create an Azure storage account
		$location = "East US 2"
		$storageAccountName = "<StorageAcccountName>"   # Provide a Storage account name
		
		New-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName -Location $location -Type Standard_GRS
 
		# Create an Azure Blob Storage container
		$containerName = "<ContainerName>"              # Provide a container name
		$storageAccountKey = Get-AzureRmStorageAccountKey -Name $storageAccountName -ResourceGroupName $resourceGroupName | %{ $_.Key1 }
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
		New-AzureStorageContainer -Name $containerName -Context $destContext

3. 建立 HDInsight 叢集。使用下列 Cmdlet。

		# Set these variables
		$clusterName = $containerName                   # As a best practice, have the same name for the cluster and container
		$clusterNodes = <ClusterSizeInNodes>            # The number of nodes in the HDInsight cluster
		$httpCredentials = Get-Credential
		$rdpCredentials = Get-Credential
		
		New-AzureRmHDInsightCluster -ClusterName $clusterName -ResourceGroupName $resourceGroupName -HttpCredential $httpCredentials -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainer $containerName  -ClusterSizeInNodes $clusterNodes -ClusterType Hadoop -Version "3.2" -RdpCredential $rdpCredentials -RdpAccessExpiry (Get-Date).AddDays(14) -ObjectID $objectId -AadTenantId $tenantID -CertificateFilePath $certificateFilePath -CertificatePassword $password

	Cmdlet 成功完成後，您應該會看到如下的輸出：

		Name                      : hdiadlcluster
		Id                        : /subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/hdiadlgroup/providers/Mi
		                            crosoft.HDInsight/clusters/hdiadlcluster
		Location                  : East US 2
		ClusterVersion            : 3.2.7.707
		OperatingSystemType       : Windows
		ClusterState              : Running
		ClusterType               : Hadoop
		CoresUsed                 : 16
		HttpEndpoint              : hdiadlcluster.azurehdinsight.net
		Error                     :
		DefaultStorageAccount     :
		DefaultStorageContainer   :
		ResourceGroup             : hdiadlgroup
		AdditionalStorageAccounts : 

## 在 HDInsight 叢集上執行測試工作以使用資料湖存放區

設定 HDInsight 叢集之後，您可以在叢集上執行測試工作，以測試 HDInsight 叢集是否可以存取資料湖存放區。為了完成這個操作，我們將會執行範例 Hive 工作，該工作會使用您稍早上傳至資料湖存放區的範例資料建立資料表。

使用下列 Cmdlet 以執行 Hive 查詢。在這個查詢中，我們將會從資料湖存放區中的資料建立資料表，然後在建立的資料表上執行 select 查詢。

	$queryString = "DROP TABLE vehicles;" + "CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://$dataLakeStoreName.azuredatalakestore.net:443/';" + "SELECT * FROM vehicles LIMIT 10;"
	
	$hiveJobDefinition = New-AzureRmHDInsightHiveJobDefinition -Query $queryString

	$hiveJob = Start-AzureRmHDInsightJob -ResourceGroupName $resourceGroupName -ClusterName $clusterName -JobDefinition $hiveJobDefinition -ClusterCredential $httpCredentials

	Wait-AzureRmHDInsightJob -ResourceGroupName $resourceGroupName -ClusterName $clusterName -JobId $hiveJob.JobId -ClusterCredential $httpCredentials

輸出如下。輸出中 **ExitValue** 為 0 表示工作成功完成。

	Cluster         : hdiadlcluster.
	HttpEndpoint    : hdiadlcluster.azurehdinsight.net
	State           : SUCCEEDED
	JobId           : job_1445386885331_0012
	ParentId        :
	PercentComplete :
	ExitValue       : 0
	User            : admin
	Callback        :
	Completed       : done

使用下列 Cmdlet 從工作擷取輸出：

	Get-AzureRmHDInsightJobOutput -ClusterName $clusterName -JobId $hiveJob.JobId -DefaultContainer $containerName -DefaultStorageAccountName $storageAccountName -DefaultStorageAccountKey $storageAccountKey -ClusterCredential $httpCredentials

工作輸出類似下面：

	1,1,2014-09-14 00:00:03,46.81006,-92.08174,51,S,1
	1,2,2014-09-14 00:00:06,46.81006,-92.08174,13,NE,1
	1,3,2014-09-14 00:00:09,46.81006,-92.08174,48,NE,1
	1,4,2014-09-14 00:00:12,46.81006,-92.08174,30,W,1
	1,5,2014-09-14 00:00:15,46.81006,-92.08174,47,S,1
	1,6,2014-09-14 00:00:18,46.81006,-92.08174,9,S,1
	1,7,2014-09-14 00:00:21,46.81006,-92.08174,53,N,1
	1,8,2014-09-14 00:00:24,46.81006,-92.08174,63,SW,1
	1,9,2014-09-14 00:00:27,46.81006,-92.08174,4,NE,1
	1,10,2014-09-14 00:00:30,46.81006,-92.08174,31,N,1


	

## 使用 HDFS 命令存取資料湖存放區

一旦您已設定 HDInsight 叢集使用資料湖存放區，您可以使用 HDFS 殼層命令來存取存放區。

1. 登入新的 [Azure Preview 入口網站](https://portal.azure.com)。

2. 按一下 [瀏覽]，然後依序按一下 [HDInsight 叢集] 和您建立的 HDInsight 叢集。

3. 在 [叢集] 刀鋒視窗中，按一下 [遠端桌面]，然後在 [遠端桌面] 刀鋒視窗中，按一下 [連接]。

	![遠端至 HDI 叢集](./media/data-lake-store-hdinsight-hadoop-use-powershell/ADL.HDI.PS.Remote.Desktop.png "建立 Azure 資源群組")

	出現提示時，請輸入為遠端桌面使用者提供的認證。

4. 在遠端工作階段中，啟動 Windows PowerShell，並使用 HDFS 檔案系統命令來列出 Azure 資料湖中的檔案。

	 	hdfs dfs -ls adl://<Data Lake Store account name>.azuredatalakestore.net:443/

	這樣應該會列出您稍早上傳至資料湖存放區的檔案。

		15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
		Found 1 items
		-rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestore.azuredatalakestore.net:443/vehicle1_09142014.csv

	您也可以使用 `hdfs dfs -put` 命令將一些檔案上傳至 Azure 資料湖，然後使用 `hdfs dfs -ls` 以確認是否成功上傳檔案。

## 另請參閱

* [入口網站：建立 HDInsight 叢集以使用資料湖存放區](data-lake-store-hdinsight-hadoop-use-portal.md)

[makecert]: https://msdn.microsoft.com/zh-TW/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/zh-TW/library/windows/desktop/ff550672(v=vs.85).aspx

<!---HONumber=Nov15_HO2-->