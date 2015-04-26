<properties 
	pageTitle="在 HDInsight 中將 Hadoop 工作的資料上傳 | Azure" 
	description="了解如何使用 Azure 儲存體總管、Azure PowerShell、Hadoop 命令列或 Sqoop 在 HDInsight 中上傳和存取資料。" 
	services="storage, hdinsight" 
	documentationCenter="" 
	authors="mumian" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/12/2014" 
	ms.author="jgao"/>



# 在 HDInsight 中將 Hadoop 工作的資料上傳

Azure HDInsight 在 Azure Blob 儲存體之上提供全功能的 Hadoop 分散式檔案系統 (HDFS)。它設計為 HDFS 延伸模組，可讓 Hadoop 生態系統中的全部元件直接處理它所管理資料，客戶操作起來順暢無比。Azure Blob 儲存體和 HDFS 是不同的檔案系統，非常適合儲存資料和計算該資料。如需有關使用 Azure Blob 儲存體的優點，請參閱[使用 Azure Blob 儲存體搭配 HDInsight][hdinsight-storage]。 

部署 Azure HDInsight 叢集通常是為了執行 MapReduce 工作，並於這些工作完成時卸除。將計算之後的資料保留在 HDFS 叢集的成本較高。對於使用 HDInsight 來處理的資料，Azure Blob 儲存體是一種高可用性、高延展性、大容量、低成本且可共用的儲存方案。將資料儲存在 Blob 中，可安全地釋放做為計算用途的 HDInsight 叢集，而不會遺失資料。 

可透過 [AzCopy][azure-azcopy]、[Azure PowerShell][azure-powershell]、[Azure Storage Client Library for .NET][azure-storage-client-library] 或總管工具來存取 Azure Blob 儲存體。以下是一些可用的工具：

* [Azure 儲存體總管](http://azurestorageexplorer.codeplex.com/)
* [Cloud Storage Studio 2](http://www.cerebrata.com/Products/CloudStorageStudio/)
* [CloudXplorer](http://clumsyleaf.com/products/cloudxplorer)
* [Azure Explorer](http://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx)
* [Azure Explorer PRO](http://www.cloudberrylab.com/microsoft-azure-explorer-pro.aspx)

**必要條件**

開始進行本文之前，請注意下列需求：

* Azure HDInsight 叢集。如需指示，請參閱 [開始使用 Azure HDInsight][hdinsight-get-started] 或[佈建 HDInsight 叢集][hdinsight-provision]。

## 本文內容

* [使用 AzCopy 將資料上傳至 Blob 儲存體](#azcopy)
* [使用 Azure PowerShell 將資料上傳至 Blob 儲存體](#powershell)
* [使用 Azure 儲存體總管將資料上傳至 Blob 儲存體](#storageexplorer)
* [使用 Hadoop 命令列將資料上傳至 Blob 儲存體](#commandline)
* [使用 Hadoop 命令列將 Azure SQL Database 的資料匯入至 Blob 儲存體](#sqoop)

## <a id="azcopy"></a>使用 AzCopy 將資料上傳至 Blob 儲存體##

AzCopy 是一個命令列公用程式，可簡化在 Azure Storage 帳戶上進出傳送資料的工作。您可以將它當做獨立工具來使用，也可以將此公用程式納入現有的應用程式中。[下載 AzCopy][azure-azcopy-download]。

AzCopy 語法如下：

	AzCopy <Source> <Destination> [filePattern [filePattern...]] [Options]

如需詳細資訊，請參閱 [AzCopy - 上傳/下載 Azure Blob 的檔案][azure-azcopy]

## <a id="powershell"></a>使用 Azure PowerShell 將資料上傳至 Blob 儲存體##

Azure PowerShell 是功能強大的指令碼環境，可讓您在 Azure 中控制和自動化工作量的部署與管理。您可以使用 Azure PowerShell 將資料上傳至 Blob 儲存體，供 MapReduce 工作處理資料。如需有關設定工作站來執行 Azure PowerShell 的詳細資訊，請參閱[安裝並設定 Azure PowerShell][powershell-install-configure]。

**將本機檔案上傳至 Blob 儲存體**

1. 根據[安裝並設定 Azure PowerShell][powershell-install-configure] 的指示，開啟 Azure PowerShell 主控台視窗。
2. 在下列指令碼中設定前五個變數的值：

		$subscriptionName = "<AzureSubscriptionName>"
		$storageAccountName = "<StorageAccountName>"
		$containerName = "<ContainerName>"
		
		$fileName ="<LocalFileName>"
		$blobName = "<BlobName>"
		
		# Get the storage account key
		Select-AzureSubscription $subscriptionName
		$storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
		
		# Create the storage context object
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey
		
		# Copy the file from local workstation to the Blob container		
		Set-AzureStorageBlobContent -File $fileName -Container $containerName -Blob $blobName -context $destContext
		
3. 將指令碼貼到 Azure PowerShell 主控台視窗中來執行。

Blob 儲存容器以機碼/值組來儲存資料，沒有目錄階層。然而，機碼名稱中可使用 "/" 字元，使檔案變成好像儲存在目錄結構中一樣。例如，Blob 的機碼可能是 *input/log1.txt*。實際上不存在 "input" 目錄，只是因為機碼名稱中有 "/" 字元，才形成檔案路徑的外觀。在上述指令碼中，您可以設定 $blobname 變數來提供檔案的資料夾結構。例如 *$blobname="myfolder/myfile.txt"*。

使用 Azure Explorer 工具時，您可能會看到一些 0 位元組檔案。這些檔案有兩種用途：

- 若是空資料夾，則只是表示該資料夾存在。Blob 儲存體很聰明，如果有一個名為 foo/bar 的 Blob 存在，則知道有一個名為 foo 的資料夾。但是，如果您想要有一個名為 foo 的空資料夾，唯一的辦法就是保留這個特殊的 0 位元組檔案。
- 這些檔案中保存 Hadoop 檔案系統所需的一些特殊中繼資料，尤其是資料夾的權限和擁有者。








## <a id="storageexplorer"></a>使用 Azure 儲存體總管將資料上傳至 Blob 儲存體

*Azure 儲存體總管*是在一種 Azure 儲存體中檢查和變更資料的實用工具。它是免費工具，可從 [http://azurestorageexplorer.codeplex.com/](http://azurestorageexplorer.codeplex.com/ "Azure Storage Explorer") 下載。

使用此工具之前，必須先知道您的 Azure 儲存體帳戶名稱和帳戶金鑰。有關取得此資訊的指示，請參閱＜作法：檢視、複製和重新產生儲存體存取金鑰*小節，其屬於[建立、管理或刪除儲存體帳戶][azure-create-storage-account]。  

1. 執行 Azure 儲存體總管。

	![HDI.AzureStorageExplorer][image-azure-storage-explorer]

2. 按一下 [**加入帳戶**]。將帳戶加入至 Azure 儲存體總管之後，就不必再重複執行此步驟。 

	![HDI.ASEAddAccount][image-ase-addaccount]

3. 輸入 [**儲存體帳戶名稱**] 和 [**儲存體帳戶金鑰**]，然後按一下 [**加入儲存體帳戶**]。您可以加入多個儲存體帳戶，各帳戶會顯示在個別的索引標籤上。 
4. 在 [**儲存體類型**] 下，按一下 [**Blob**]。

	![HDI.ASEBlob][image-ase-blob]

5. 從 [**容器**] 中，按一下與 HDInsight 叢集相關聯的容器。建立 HDInsight 叢集時，必須指定容器。  否則，叢集建立程序會為您建立容器。
6. 在 [**Blob**] 下，按一下 [**上傳**]。
7. 指定要上傳的檔案，然後按一下 [**開啟**]。








































































## <a id="commandline"></a> 使用 Hadoop 命令列將資料上傳至 Blob 儲存體

若要使用 Hadoop 命令列，您必須先使用遠端桌面連線到叢集。 



1. 登入[管理入口網站][azure-management-portal]。
2. 按一下 [**HDInsight**]。將出現已部署的 Hadoop 叢集清單。
3. 按一下您要上傳資料的 HDInsight 叢集。
4. 按一下頁面頂端的 [**設定**]。
5. 如果尚未啟用遠端桌面，請按一下 [**啟用遠端**]，然後依指示進行。  否則請跳到下一步。
4. 按一下頁面底部的 [**連線**]。
7. 按一下 [開啟]。
8. 輸入認證，然後按一下 [**確定**]。
9. 按一下 [**是**]。
10. 從桌面上，按一下 [**Hadoop 命令列**]。
12. 下列範例示範如何從 HDInsight 前端節點上的本機檔案系統中，將 davinci.txt 檔案複製到 /example/data 目錄。

		hadoop dfs -copyFromLocal C:\temp\davinci.txt /example/data/davinci.txt

	因為預設檔案系統在 Azure Blob 儲存體上，所以 /example/datadavinci.txt 實際存在於 Azure Blob 儲存體上。  您也可以用下列語法來參考此檔案：

		wasb:///example/data/davinci.txt 

	或

		wasbs://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt

	使用 *wasbs* 時需要完整的網域名稱。

13. 使用下列命令來列出已上傳的檔案清單：

		hadoop dfs -lsr /example/data


## <a id="sqoop"></a> 使用 Sqoop 將資料從 SQL Database/SQL Server 匯入至 HDFS

Sqoop 是一種專門在 Hadoop 和關聯式資料庫之間傳送資料的工具。此工具可讓您從 SQL、MySQL 或 Oracle 等關聯式資料庫管理系統 (RDBMS) 中，將資料匯入至 Hadoop 分散式檔案系統 (HDFS)，在 Hadoop 中使用 MapReduce 或 Hive 傳送資料，然後將資料匯回到 RDBMS。如需詳細資訊，請參閱 [Sqoop 使用者指南][apache-sqoop-guide](英文)。

匯入資料之前，您必須知道 Azure SQL Database 伺服器名稱、資料庫帳戶名稱、帳戶密碼和資料庫名稱。 

依預設，Azure SQL 資料庫接受來自 Azure 服務 (例如 Azure HDinsight) 的連線。如果此防火牆設定停用，您必須從 Azure 管理入口網站中啟用它。如需建立 SQL 資料庫和設定防火牆規則的相關指示，請參閱[建立和設定 SQL 資料庫][sqldatabase-create-configue]。 

下列程序使用 PowerShell 來提交 Sqoop 工作。 

**使用 Sqoop 和 PowerShell 將資料匯入至 HDInsight**

1. 根據[安裝並設定 Azure PowerShell][powershell-install-configure] 的指示，開啟 Azure PowerShell 主控台視窗。
2. 在下列指令碼中設定前八個變數的值：

		$subscriptionName = "<AzureSubscriptionName>"
		$clusterName = "<HDInsightClusterName>"
		
		$sqlDatabaseServerName = "<SQLDatabaseServerName>"
		$sqlDatabaseUserName = "<SQLDatabaseDatabaseName>"
		$sqlDatabasePassword = "<SQLDatabasePassword>"
		$sqlDatabaseDatabaseName = "<SQLDatabaseDatabaseName>"
		$tableName = "<SQLDatabaseTableName>"
		
		$hdfsOutputDir = "<OutputPath>"  # This is the HDFS path for the output file, for example "/lineItemData".
		
		Select-AzureSubscription $subscriptionName		
		$sqoopDef = New-AzureHDInsightSqoopJobDefinition -Command "import --connect jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseUserName@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$sqlDatabaseDatabaseName --table $tableName --target-dir $hdfsOutputDir -m 1" 

		$sqoopJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $sqoopDef #-Debug -Verbose
		Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 -Job $sqoopJob
		
		Write-Host "Standard Error" -BackgroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardError
		Write-Host "Standard Output" -BackgroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardOutput

3. 將指令碼貼到 Azure PowerShell 主控台視窗中來執行。有關從 Azure Blob 儲存體擷取資料檔的 PowerShell 範例，請參閱[開始使用 HDInsight][hdinsight-get-started]。

如需有關使用 Sqoop 的詳細資訊，請參閱[使用 Sqoop 搭配 HDInsight][hdinsight-use-sqoop]。

## 後續步驟
您現在已了解如何將資料匯入至 HDInsight，請閱讀下列文章以了解如何執行分析：

* [開始使用 Azure HDInsight][hdinsight-get-started]
* [以程式設計方式提交 Hadoop 工作][hdinsight-submit-jobs]
* [使用 Hive 搭配 HDInsight][hdinsight-use-hive]
* [使用 Pig 搭配 HDInsight][hdinsight-use-pig]




[azure-management-portal]: https://manage.windowsazure.com
[azure-powershell]: http://msdn.microsoft.com/library/windowsazure/jj152841.aspx

[azure-storage-client-library]: /zh-tw/develop/net/how-to-guides/blob-storage/
[azure-create-storage-account]: ../storage-create-storage-account/
[azure-azcopy-download]: http://aka.ms/WaCopy
[azure-azcopy]: http://blogs.msdn.com/b/windowsazurestorage/archive/2012/12/03/azcopy-uploading-downloading-files-for-windows-azure-blobs.aspx

[hdinsight-use-sqoop]: ../hdinsight-use-sqoop/

[hdinsight-storage]: ../hdinsight-use-blob-storage/
[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight-get-started]: ../hdinsight-get-started/

[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-use-pig]: ../hdinsight-use-pig
[hdinsight-provision]: ../hdinsight-provision-clusters/

[sqldatabase-create-configue]: ../sql-database-create-configure/

[apache-sqoop-guide]: http://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html

[powershell-install-configure]: ../install-configure-powershell/


[image-azure-storage-explorer]: ./media/hdinsight-upload-data/HDI.AzureStorageExplorer.png
[image-ase-addaccount]: ./media/hdinsight-upload-data/HDI.ASEAddAccount.png
[image-ase-blob]: ./media/hdinsight-upload-data/HDI.ASEBlob.png


<!--HONumber=42-->
