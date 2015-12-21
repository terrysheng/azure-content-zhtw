<properties 
   pageTitle="使用入口網站佈建 HDInsight Hadoop 叢集與 Azure 資料湖存放區 |Azure" 
   description="使用 Azure 入口網站設定和使用 HDInsight Hadoop 叢集與 Azure 資料湖存放區" 
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
   ms.date="11/13/2015"
   ms.author="nitinme"/>

# 使用 Azure 入口網站佈建 HDInsight 叢集與資料湖存放區

> [AZURE.SELECTOR]
- [Using Portal](data-lake-store-hdinsight-hadoop-use-portal.md)
- [Using PowerShell](data-lake-store-hdinsight-hadoop-use-powershell.md)


了解如何使用 Azure 入口網站設定 HDInsight 叢集 (Hadoop、HBase 或 Storm) 以使用 Azure 資料湖存放區。此版本的一些重要考量：

* * **對於 Hadoop 和 Storm 叢集 (Windows 和 Linux)**，資料湖存放區只能做為額外的儲存體帳戶。這類叢集的預設儲存體帳戶仍是 Azure 儲存體 Blob (WASB)。

* **對於 HBase 叢集 (Windows 和 Linux)**，您可以使用資料湖存放區做為預設儲存體或額外的儲存體。


在本文中，我們佈建 Hadoop 叢集與資料湖存放區做為額外的儲存體。使用 Azure 入口網站以設定 HDInsight 來搭配資料湖存放區使用，包含下列步驟：

* 建立具有 Azure Active Directory 服務主體驗證的 HDInsight 叢集
* 使用相同的服務主體設定資料湖存放區存取
* 在叢集上執行測試工作

## 必要條件

開始進行本教學課程之前，您必須具備下列條件：

- **Azure 訂用帳戶**。請參閱[取得 Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。
- **啟用您的 Azure 訂用帳戶**以使用資料湖存放區公開預覽版。請參閱[指示](data-lake-store-get-started-portal.md#signup)。


## 建立具有 Azure Active Directory 服務主體驗證的 HDInsight 叢集

在本節中，您會建立 HDInsight Hadoop 叢集，它使用資料湖存放區做為額外的儲存體。在此版本中，對於 Hadoop 叢集，資料湖存放區只能做為叢集的額外儲存體。預設儲存體仍是 Azure 儲存體 Blob (WASB)。所以，我們要先建立叢集所需的儲存體帳戶和儲存體容器。

1. 登入新的 [Azure 入口網站](https://portal.azure.com)。

2. 請依照[在 HDInsight 中建立 Hadoop 叢集](../hdinsight/hdinsight-provision-clusters.md#create-using-the-preview-portal)中的步驟，開始佈建 HDInsight 叢集。
 
3. 在 [選擇性組態] 刀鋒視窗中，按一下 [資料來源]。在 [資料來源] 刀鋒視窗中，指定儲存體帳戶和儲存體容器的詳細資料、將 [位置] 指定為 [美國東部 2]，然後按一下 [叢集 AAD 身分識別]。

	![將服務主體新增至 HDInsight 叢集](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.png "將服務主體新增至 HDInsight 叢集")

4. 在 [叢集 AAD 身分識別] 刀鋒視窗中，您可以選擇選取現有的服務主體或建立一個新的服務主體。
	
	* **建立新的服務主體**。在 [叢集 AAD 身分識別] 刀鋒視窗中，按一下 [建立新的]。按一下 [服務主體]，然後在 [建立服務主體] 刀鋒視窗中，提供值以建立新的服務主體。在這個過程中，也會建立憑證和 Azure Active Directory 應用程式。按一下 [建立]。

		![將服務主體新增至 HDInsight 叢集](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.4.png "將服務主體新增至 HDInsight 叢集")

		在 [叢集 AAD 身分識別] 刀鋒視窗中，按一下 [選取]，利用將建立的服務主體繼續作業。

		![將服務主體新增至 HDInsight 叢集](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.5.png "將服務主體新增至 HDInsight 叢集")


	* **選擇現有的服務主體**。在 [叢集 AAD 身分識別] 刀鋒視窗中，按一下 [使用現有的]。按一下 [服務主體]，然後在 [選取服務主體] 刀鋒視窗中，搜尋現有的服務主體。按一下服務主體名稱，然後按一下 [選取]。

		![將服務主體新增至 HDInsight 叢集](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.2.png "將服務主體新增至 HDInsight 叢集")

		在 [叢集 AAD 身分識別] 刀鋒視窗中，上傳您稍早建立的憑證 (.pfx) 並提供您用來建立憑證的密碼。按一下 [選取]。如此即完成 HDInsight 叢集的 Azure Active Directory 組態。

		![將服務主體新增至 HDInsight 叢集](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3.png "將服務主體新增至 HDInsight 叢集")

6. 按一下 [資料來源] 刀鋒視窗上的 [選取]，並使用叢集佈建繼續作業，如[在 HDInsight 中建立 Hadoop 叢集](../hdinsight/hdinsight-provision-clusters.md#create-using-the-preview-portal)中所述。

7. 佈建叢集之後，您可以驗證服務主體是與 HDInsight 叢集相關聯。若要這樣做，從 [叢集] 刀鋒視窗中依序按一下 [設定] 和 [叢集 AAD 身分識別]，您應該會看到相關聯的服務主體。

	![將服務主體新增至 HDInsight 叢集](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6.png "將服務主體新增至 HDInsight 叢集")

## <a name="acl"></a>設定服務主體以存取資料湖存放區檔案系統

1. 登入新的 [Azure 入口網站](https://portal.azure.com)。

2. 如果您沒有資料湖存放區帳戶，請建立一個。遵循[使用 Azure 入口網站開始使用 Azure 資料湖存放區](data-lake-store-get-started-portal.md)的指示。

	如果您已經擁有資料湖存放區帳戶，可從左窗格中依序按一下 [瀏覽] 和 [資料湖存放區]，然後按一下您要授與存取權的帳戶名稱。

	在您的資料湖存放區帳戶底下執行下列工作。

	* [在資料湖存放區中建立資料夾](data-lake-store-get-started-portal.md#createfolder)。
	* [將檔案上傳至資料湖存放區](data-lake-store-get-started-portal.md#uploaddata)。如果您正在尋找一些可上傳的範例資料，可以從 [Azure 資料湖 Git 存放庫](https://github.com/MicrosoftBigData/AzureDataLake/tree/master/SQLIPSamples/SampleData/AmbulanceData)取得 **Ambulance Data** 資料夾。

	您稍後在測試資料湖存放區帳戶與 HDInsight 叢集時，將使用上傳的檔案。

3. 在 [資料湖存放區] 刀鋒視窗中，按一下 [資料總管]。

	![資料總管](./media/data-lake-store-hdinsight-hadoop-use-portal/adl.start.data.explorer.png "資料總管")

4. 在 [資料總管] 刀鋒視窗中，按一下帳戶的根，然後在 [帳戶] 刀鋒視窗中，按一下 [存取] 圖示。

	![設定資料湖檔案系統上的 ACL](./media/data-lake-store-hdinsight-hadoop-use-portal/adl.acl.1.png "設定資料湖檔案系統上的 ACL")

5. [存取] 刀鋒視窗會列出已指派給根的標準存取權和自訂存取權。按一下 [新增] 圖示以新增自訂層級 ACL，並包含您稍早建立的服務主體。

	![列出標準和自訂存取](./media/data-lake-store-hdinsight-hadoop-use-portal/adl.acl.2.png "列出標準和自訂存取")

6. 按一下 [新增] 圖示，以開啟 [新增自訂存取] 刀鋒視窗。在此刀鋒視窗中，按一下 [選取使用者或群組]，然後在 [選取使用者或群組] 刀鋒視窗中，搜尋您稍早在 Azure Active Directory 中建立的服務主體。稍早建立的服務主體名稱是 **HDIADL**。按一下服務主體名稱，然後按一下 [選取]。

	![新增群組](./media/data-lake-store-hdinsight-hadoop-use-portal/adl.acl.3.png "新增群組")

7. 按一下 [選取權限]，選取您想要指派給服務主體的權限，然後按一下 [確定]。

	![將權限指派至群組](./media/data-lake-store-hdinsight-hadoop-use-portal/adl.acl.4.png "將權限指派至群組")

8. 在 [新增自訂存取] 刀鋒視窗中，按一下 [確定]。具有相關權限的新增群組現在將列在 [存取] 刀鋒視窗中。

	![將權限指派至群組](./media/data-lake-store-hdinsight-hadoop-use-portal/adl.acl.5.png "將權限指派至群組")

7. 如果需要，您也可以在新增服務主體之後修改存取權限。根據您想要移除或指派該權限，清除或選取每個權限類型的核取方塊 (讀取、寫入、執行)。按一下 [儲存] 儲存變更，或按一下 [捨棄] 復原變更。



## 在 HDInsight 叢集上執行測試工作以使用 Azure 資料湖存放區

設定 HDInsight 叢集之後，您可以在叢集上執行測試工作，以測試 HDInsight 叢集是否可以存取 Azure 資料湖存放區中的資料。若要這樣做，我們將會執行目標為資料湖存放區的一些 hive 查詢。

1. 開啟您剛剛佈建的叢集的 [叢集] 刀鋒視窗，然後按一下 [儀表板]。

	![啟動叢集儀表板](./media/data-lake-store-hdinsight-hadoop-use-portal/hdiadlcluster1.png "啟動叢集儀表板")

	出現提示時，輸入叢集的系統管理員認證。

2. 這會開啟 Microsoft Azure HDInsight 查詢主控台。按一下 [Hive 編輯器]。

	![開啟 Hive 編輯器](./media/data-lake-store-hdinsight-hadoop-use-portal/hdiadlcluster2.png "開啟 Hive 編輯器")

3. 在 [Hive 編輯器] 中輸入下列查詢，然後按一下 [提交]。

		CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder'

	在此 Hive 查詢中，我們會根據儲存在 `adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder` 上資料湖存放區中的資料來建立資料表。這個位置具有您稍早應該已上傳的範例資料檔案。

	底部的 [工作工作階段] 資料表會顯示工作的狀態從 [正在初始化] 變更為 [執行中]，然後變更為 [已完成]。您也可以按一下 [檢視詳細資料] 以查看已完成工作的詳細資訊。

	![建立資料表](./media/data-lake-store-hdinsight-hadoop-use-portal/hdiadlcluster3.png "建立資料表")

4. 執行下列查詢來確認已建立資料表。

		SHOW TABLES;

	按一下對應至此查詢的 [檢視詳細資料]，輸出應該會顯示下列項目：

		hivesampletable
		vehicles

	**vehicles** 是您稍早建立的資料表。**hivesampletable** 是所有 HDInsight 叢集中預設可用的範例資料表。

5. 您也可以執行查詢，從 **vehicles** 擷取資料。

		SELECT * FROM vehicles LIMIT 5;

## 使用 HDFS 命令存取資料湖存放區

一旦您已設定 HDInsight 叢集使用資料湖存放區，您可以使用 HDFS 殼層命令來存取存放區。

1. 登入新的 [Azure 入口網站](https://portal.azure.com)。

2. 按一下 [瀏覽]，然後依序按一下 [HDInsight 叢集] 和您建立的 HDInsight 叢集。

3. 在 [叢集] 刀鋒視窗中，按一下 [遠端桌面]，然後在 [遠端桌面] 刀鋒視窗中，按一下 [連接]。

	![遠端至 HDI 叢集](./media/data-lake-store-hdinsight-hadoop-use-portal/ADL.HDI.PS.Remote.Desktop.png "建立 Azure 資源群組")

	出現提示時，請輸入為遠端桌面使用者提供的認證。

4. 在遠端工作階段中，啟動 Windows PowerShell，並使用 HDFS 檔案系統命令來列出 Azure 資料湖存放區中的檔案。

	 	hdfs dfs -ls adl://<Data Lake Store account name>.azuredatalakestore.net:443/

	這樣應該會列出您稍早上傳至資料湖存放區的檔案。

		15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
		Found 1 items
		-rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder

	您也可以使用 `hdfs dfs -put` 命令來將一些檔案上傳至資料湖存放區，然後使用 `hdfs dfs -ls` 以確認是否成功上傳檔案。

## 佈建 HBase 叢集 (使用資料湖存放區做為預設儲存體) 的考量

對於 HBase 叢集，您可以使用資料湖存放區帳戶做為預設儲存體。如果您選擇這樣做，若要成功佈建叢集，與叢集相關聯的服務主體**必須**具備資料湖存放區帳戶的存取權。您可以使用兩種方式確定：

* **如果您使用現有的服務主體**，就必須確定服務主體已新增至根層級的資料湖存放區檔案系統的 ACL，然後再開始佈建叢集。
* **如果您選擇建立新的服務主體**做為叢集佈建的一部分，只要叢集開始佈建，就必須將新建立的服務主體新增至根層級的資料湖存放區檔案系統。如果您無法執行這項操作，將會佈建叢集但是 HBase 服務將無法啟動。若要解決此問題，您必須再將服務主體新增至資料湖存放區帳戶的 ACL，然後使用 Ambari Web UI 重新啟動 HBase 服務。

如需如何將服務主體新增至資料湖存放區檔案系統的相關指示，請參閱[設定服務主體以存取資料湖存放區檔案系統](#acl)。



## 另請參閱

* [PowerShell：建立 HDInsight 叢集以使用資料湖存放區](data-lake-store-hdinsight-hadoop-use-powershell.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx

<!-------HONumber=AcomDC_1210_2015--->