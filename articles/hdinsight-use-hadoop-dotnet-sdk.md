<properties linkid="manage-services-hdinsight-howto-sdk" urlDisplayName="HDInsight SDK" pageTitle="How to use the HDInsight .NET libraries | Azure" metaKeywords="" description="Learn how to get the HDInsight NuGet packages and use them from your .NET application." metaCanonical="" services="hdinsight" documentationCenter="" title="Use the Hadoop .NET SDK with HDInsight" authors="bradsev" solutions="" manager="paulettm" editor="cgronlun" />

搭配使用 Hadoop .NET SDK 與 HDInsight
=====================================

Hadoop .NET SDK 所提供的 .NET 用戶端程式庫，可讓您輕鬆地從 .NET 使用 Hadoop。在此教學課程中，您將了解如何取得 Hadoop .NET SDK，並使用它來建置可透過 Azure HDInsight 服務執行 Hive 查詢的簡易 .NET 型應用程式。我們將根據 actors.txt 檔案撰寫應用程式，以找出得獎最多的演員。

若要啟用 HDInsight，請按一下[這裡](https://account.windowsazure.com/PreviewFeatures)。

本文內容
--------

-   [下載及安裝 Hadoop .NET SDK](#install)
-   [教學課程前置工作](#prepare)
-   [建立應用程式](#create)
-   [執行應用程式](#run)
-   [後續步驟](#nextsteps)

下載及安裝 Hadoop .NET SDK
--------------------------

您可以從 [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started) 安裝最新發佈的 SDK 組建。SDK 包含下列元件：

-   **MapReduce 程式庫** - 簡化使用 Hadoop 串流介面、以 .NET 語言撰寫 MapReduce 工作的作業。

-   **LINQ to Hive 用戶端程式庫** - 將 C\# 或 F\# LINQ 查詢轉譯為 HiveQL 查詢，並在 Hadoop 叢集上加以執行。此程式庫也可從 .NET 應用程式執行任何 HiveQL 查詢。

-   **WebClient 程式庫** - 包含 *WebHDFS* 和 *WebHCat* 的用戶端程式庫。

    -   **WebHDFS 用戶端程式庫** - 處理 HDFS 和 Azure 部落格儲存體中的檔案。

    -   **WebHCat 用戶端程式庫** - 管理 HDInsight 叢集中的工作排程和執行。

用以安裝程式庫的 NuGet 語法：

    install-package Microsoft.Hadoop.MapReduce
    install-package Microsoft.Hadoop.Hive 
    install-package Microsoft.Hadoop.WebClient 

這些命令會將程式庫及其參考新增至目前的 Visual Studio 專案。

教學課程前置工作
----------------

您必須具備 [Azure 訂閱](http://www.windowsazure.com/zh-tw/pricing/free-trial/) 和 [Azure 儲存體帳戶](http://www.windowsazure.com/zh-tw/manage/services/storage/how-to-create-a-storage-account/)，才能開始執行。您也必須知道您的 Azure 儲存體帳戶名稱和帳戶金鑰。有關取得此資訊的指示，請參閱[如何管理儲存體帳戶](/en-us/manage/services/storage/how-to-manage-a-storage-account/)的*作法：檢視、複製及重新產生儲存體存取金鑰*一節。

您還必須下載此教學課程中使用的 Actors.txt 檔案。請執行下列步驟，將此檔案下載至您的開發環境：

1.  在您的本機電腦上建立 C:\\Tutorials 資料夾。

2.  下載 [Actors.txt](http://www.microsoft.com/en-us/download/details.aspx?id=37003)，並將檔案儲存至 C:\\Tutorials 資料夾。

建立應用程式
------------

在本節中，您將了解如何以程式設計方式將檔案上傳至 Hadoop 叢集，以及如何使用 LINQ to Hive 執行 Hive 工作。

1.  開啟 Visual Studio 2012。

2.  在 [檔案] 功能表中按一下 **[新增]**，然後按一下 **[專案]**。

3.  在 [新增專案] 中，輸入或選取下列值：

	<table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
	<tr>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">屬性</th>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">值</th></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">類別</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">範本/Visual C#/Windows</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">範本</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">主控台應用程式</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">名稱</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">SimpleHiveJob</td></tr>
	</table>

4.  按一下 **[確定]** 以建立專案。

5.  在 **[工具]** 功能表中按一下 **[Library Package Manager]**，然後按一下 **[Package Manager Console]**。

6.  在主控台中執行下列命令，以安裝封裝。

        install-package Microsoft.Hadoop.Hive 
        install-package Microsoft.Hadoop.WebClient 

    這些命令會將 .NET 程式庫及其參考新增至目前的 Visual Studio 專案。

7.  在 [方案總管] 中，按兩下 **Program.cs** 加以開啟。

8.  在檔案頂端新增下列 using 陳述式：

        using Microsoft.Hadoop.WebHDFS.Adapters;
        using Microsoft.Hadoop.WebHDFS;
        using Microsoft.Hadoop.Hive;

9.  在 Main() 函數中，複製並貼上下列程式碼：

		// Upload actors.txt to Blob Storage
		var asvAccount = [Storage-account-name.blob.core.windows.net];
		var asvKey = [Storage account key];
		var asvContainer = [Container name];
		var localFile = "C:/Tutorials/Actors.txt";
		var hadoopUser = [Hadoop user name]; // The HDInsight cluster user
		var hadoopUserPassword = [Hadoop user password]; // The HDInsight cluster user password
		var clusterURI = [HDInsight cluster URL]; //"https://HDInsightCluster Name.azurehdinsight.net:563";
		
		var storageAdapter = new BlobStorageAdapter(asvAccount, asvKey, asvContainer, true);
		var HDFSClient = new WebHDFSClient(hadoopUser, storageAdapter);
		
		Console.WriteLine("Creating MovieData directory and uploading actors.txt...");
		HDFSClient.CreateDirectory("/user/hadoop/MovieData").Wait();
		HDFSClient.CreateFile(localFile, "/user/hadoop/MovieData/Actors.txt").Wait();
		
		// Create Hive connection
		var hiveConnection = new HiveConnection(
		  new System.Uri(clusterURI),
		  hadoopUser, 
		  hadoopUserPassword,
		  asvAccount, 
		  asvKey);
		
		// Drop any existing tables called Actors
		// Only needed if you wish to rerun this application
		// and drop a previous Actors table.
		//hiveConnection.GetTable<HiveRow>("Actors").Drop();

		Console.WriteLine("Creating a Hive table named 'Actors'...");
		string command =
		  @"CREATE TABLE Actors(
		            MovieId string, 
		            ActorId string,
		            Name string, 
		            AwardsCount int) 
		            row format delimited fields 
		        terminated by ',';";
		hiveConnection.ExecuteHiveQuery(command).Wait();
		
		Console.WriteLine("Load data from Actors.txt into the 'Actors' table in Hive...");
		command =
		  @"LOAD DATA INPATH 
		        '/user/hadoop/MovieData/Actors.txt'
		    OVERWRITE INTO TABLE Actors;";
		hiveConnection.ExecuteHiveQuery(command).Wait();
		
		Console.WriteLine("Performing Hive query...");
		var result = hiveConnection.ExecuteQuery(@"select name, awardscount
		          from actors sort by awardscount desc
		          limit 1");
		result.Wait();

		Console.WriteLine("The results are: {0}", result.Result.ReadToEnd());
        Console.WriteLine("\nPress any key to continue.");
        Console.ReadKey();

10. 更新應用程式中的限制。Azure HDInsight 服務會使用 Azure Blob 儲存體作為預設檔案系統。在 HDInsight 佈建程序中，會將 Blob 指定為預設檔案系統。您可以選擇使用預設的檔案系統容器，或是不同 Blob 儲存體中的容器。如需詳細資訊，請參閱[搭配 HDInsight 使用 Azure Blob 儲存體](/en-us/manage/services/hdinsight/howto-blob-store/)。

    如果您選擇使用預設的檔案系統容器，您可以從遠端連接到叢集，在 *c:\\apps\\dist\\hadoop-1.1.0-SNAPSHOT\\conf\>core-site.xml* 組態檔中尋找儲存體帳戶名稱、儲存體金鑰和容器名稱。作為預設檔案系統的容器可藉由搜尋 *fs.default.name* 來尋找，儲存體帳戶名稱和帳戶金鑰則可藉由搜尋 *fs.azure.account.key* 來尋找。

執行應用程式
------------

在 Visual Studio 中開啟應用程式後，按 **F5** 以執行應用程式。在資料上傳、儲存至 Hive 資料表中，而最終受到查詢時，主控台視窗應會開啟，並顯示應用程式所執行的步驟。當應用程式完成並傳回查詢結果後，請按任何鍵以終止應用程式。

![HDI.HadoopSDKOutput](./media/hdinsight-use-hadoop-dotnet-sdk/HDI.HadoopSDKOutput.PNG "Console Application")

**注意**

應用程式所執行的每個步驟可能需要數秒、甚至數分鐘才能完成。上傳 Actors.txt 檔案的所需時間會隨著 Azure 資料中心的網際網路連線而不同，其他步驟的所需時間則取決於叢集大小。

**注意**

LOAD DATA INPATH 作業是一項移動作業，可將 Actors.txt 資料移至 Hive 控制的檔案系統命名空間中。這可將 Actors.txt 檔案從上傳位置中有效移除。因此，在您每次執行此應用程式時，都必須上傳 Actors.txt 檔案。

如需將資料載入 Hive 中的詳細資訊，請參閱[開始使用 Hive](https://cwiki.apache.org/confluence/display/Hive/GettingStarted#GettingStarted-DMLOperations) (英文)。

後續步驟
--------

現在，您已了解如何使用 Hadoop .NET SDK 建立 .NET 應用程式。若要深入了解，請參閱下列文章：

-   [開始使用 Azure HDInsight](/en-us/manage/services/hdinsight/get-started-hdinsight/)
-   [搭配 HDInsight 使用 Pig](/en-us/manage/services/hdinsight/using-pig-with-hdinsight/)
-   [搭配 HDInsight 使用 MapReduce](/en-us/manage/services/hdinsight/using-mapreduce-with-hdinsight/)
-   [搭配 HDInsight 使用 Hive](/en-us/manage/services/hdinsight/using-hive-with-hdinsight/)

