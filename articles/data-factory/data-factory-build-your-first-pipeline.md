<properties
	pageTitle="使用 Azure Data Factory 建置您的第一個管線"
	description="本教學課程示範如何建立使用 Azure HDInsight 轉換資料的範例資料管線。"
	services="data-factory"
	documentationCenter=""
	authors="spelluru"
	manager="jhubbard"
	editor="monicar"/>

<tags
	ms.service="data-factory"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="07/27/2015"
	ms.author="spelluru"/>

# 使用 Azure Data Factory 建置您的第一個管線
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-build-your-first-pipeline.md)
- [Using Data Factory Editor](data-factory-build-your-first-pipeline-using-editor.md)
- [Using PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Using Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)

本文章可協助您開始建置第一個管線，並將其部署到 Azure Data Factory。

> [AZURE.NOTE]本文不提供 Azure Data Factory 服務的概念性概觀。如需有關服務的詳細概觀，請參閱 [Azure Data Factory 簡介](data-factory-introduction.md)一文。

## 教學課程概觀
本教學課程將帶您執行所需步驟，建置您的第一個管線並加以執行。您將會建立管線，並從頭開始指定所有需要的資源。

如果您想快速地探索 Data Factory 的各種功能，而不從頭開始建立，可以使用我們在 Azure Preview 入口網站中所提供的範例。如需如何使用 Azure Preview 入口網站部署基於使用案例的範例，請參閱 [Azure Data Factory 更新：簡化的範例部署](http://azure.microsoft.com/blog/2015/04/24/azure-data-factory-update-simplified-sample-deployment/)

## 必要條件
開始進行本教學課程之前，您必須具備下列條件：

1.	**Azure 訂用帳戶** - 如果您沒有 Azure 訂用帳戶，只需要幾分鐘就可以建立免費試用帳戶。請參閱[免費試用](http://azure.microsoft.com/pricing/free-trial/)一文了解如何取得免費試用帳戶。

2.	**Azure 儲存體** – 在本教學課程中，您將使用 Azure 儲存體帳戶來儲存資料。如果您沒有 Azure 儲存體帳戶，請參閱〈[〉建立儲存體帳戶](../storage-create-storage-account/#create-a-storage-account)一文。建立儲存體帳戶之後，您必須取得用來存取儲存體的帳戶金鑰。請參閱〈[檢視、複製和重新產生儲存體存取金鑰](../storage-create-storage-account/#view-copy-and-regenerate-storage-access-keys)〉。

## 本教學課程涵蓋哪些內容？	
Azure Data Factory 可讓您以資料驅動型工作流程的方式，撰寫資料移動和資料處理工作。您將了解如何建置第一個管線，每個月使用 HDInsight 來轉換及分析 Web 記錄檔。

在本教學課程中，您將執行下列步驟：

1.	建立資料處理站。
2.	建立下列連結服務：
	1.	**Azure 儲存體帳戶**– Azure 儲存體帳戶將用來儲存隨選 HDInsight 叢集所使用的檔案。
	2.	**隨選 HDInsight 叢集** – HDInsight 叢集將會以隨選方式啟動以轉換及分析資料。
3.	建立輸出資料集 
4.	建立執行 Hive 指令碼的管線，且會將結果儲存在輸出資料集。Hive 指令碼會先建立外部資料表，參考儲存在 Azure blob 儲存體中未經處理的 Web 記錄資料。Hive 指令碼中的下一個步驟會將未經處理資料依年份和月份分割處理。

您的第一個管線 (名為 **MyFirstPipeline**) 會使用 Hive 活動，將部署為 HDInsight 叢集一部份的 Web 記錄檔加以轉換及分析，然後儲存在 **/HdiSamples/WebsiteLogSampleData/SampleLog/** 中。

![圖表檢視](./media/data-factory-build-your-first-pipeline/diagram-view.png)

Hive 指令碼執行後，其結果將會儲存在 Azure blob 儲存體容器：**data/partitioneddata**。

在 **AzureBlobOutput** 資料集中定義的可用性會決定 Hive 活動的執行頻率。在本教學課程中，此項目設定為每個月一次。

## 準備用於教學課程的 Azure 儲存體
開始教學課程之前，您必須準備內含教學課程所需檔案的 Azure 儲存體。

1. 啟動 [記事本]，貼上下列文字，然後將它以 **partitionweblogs.hql** 形式儲存到硬碟上的 C:\\adfgettingstarted 資料夾。此 Hive 指令碼會建立兩個外部資料表：**WebLogsRaw** 和 **WebLogsPartitioned**。

	> [AZURE.IMPORTANT]使用您的儲存體帳戶名稱取代最後一行中的 **storageaccountname**。

		set hive.exec.dynamic.partition.mode=nonstrict;

		DROP TABLE IF EXISTS WebLogsRaw; 
		CREATE EXTERNAL TABLE WebLogsRaw (
		  date  date,
		  time  string,
		  ssitename string,
		  csmethod  string,
		  csuristem  string,
		  csuriquery string,
		  sport int,
		  susername string,
		  cipcsUserAgent string,
		  csCookie string,
		  csReferer string,
		  cshost  string,
		  scstatus  int,
		  scsubstatus  int,
		  scwin32status  int,
		  scbytes int,
		  csbytes int,
		  timetaken int
		)
		ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
		LINES TERMINATED BY '\n' 
		LOCATION '/HdiSamples/WebsiteLogSampleData/SampleLog/'
		tblproperties ("skip.header.line.count"="2");
		
		DROP TABLE IF EXISTS WebLogsPartitioned ; 
		create external table WebLogsPartitioned (  
		  date  date,
		  time  string,
		  ssitename string,
		  csmethod  string,
		  csuristem  string,
		  csuriquery string,
		  sport int,
		  susername string,
		  cipcsUserAgent string,
		  csCookie string,
		  csReferer string,
		  cshost  string,
		  scstatus  int,
		  scsubstatus  int,
		  scwin32status  int,
		  scbytes int,
		  csbytes int,
		  timetaken int
		)
		partitioned by ( year int, month int)
		ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' 
		STORED AS TEXTFILE 
		LOCATION '${hiveconf:partitionedtable}';

		INSERT INTO TABLE WebLogsPartitioned  PARTITION( year , month) 
		SELECT
		  date,
		  time,
		  ssitename,
		  csmethod,
		  csuristem,
		  csuriquery,
		  sport,
		  susername,
		  cipcsUserAgent,
		  csCookie,
		  csReferer,
		  cshost,
		  scstatus,
		  scsubstatus,
		  scwin32status,
		  scbytes,
		  csbytes,
		  timetaken,
		  year(date),
		  month(date)
		FROM WebLogsRaw

	 
 
2. 為教學課程準備 Azure 儲存體：
	1. 下載[最新版本的 **AzCopy**](http://aka.ms/downloadazcopy)，或[最新預覽版本](http://aka.ms/downloadazcopypr)。請參閱〈[如何使用 AzCopy](../storage/storage-use-azcopy.md)〉文章以取得使用公用程式的指示。
	2. AzCopy 安裝之後，您可以在命令提示字元中執行下列命令，將其新增到系統路徑。 
	
			set path=%path%;C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy
	

	3. 瀏覽至 c:\\adfgettingstarted 資料夾，然後執行下列命令來將 Hive .HQL 檔案上傳到儲存體帳戶。使用您的儲存體帳戶名稱取代 **<StorageAccountName>**，並使用儲存體帳戶金鑰取代 **<Storage Key>**。

			AzCopy /Source:. /Dest:https://<StorageAccountName>.blob.core.windows.net/script /DestKey:<Storage Key>
	4. 檔案成功上傳之後，您會看見下列來自 AzCopy 的輸出。
	
			Finished 1 of total 1 file(s).
			[2015/06/15 15:47:13] Transfer summary:
			-----------------
			Total files transferred: 1
			Transfer successfully:   1
			Transfer skipped:        0
			Transfer failed:         0
			Elapsed time:            00.00:00:01

執行下列動作：

- 按一下頂端的[使用 Data Factory 編輯器](data-factory-build-your-first-pipeline-using-editor.md)連結，使用 Data Factory 編輯器 (這是 Azure 入口網站的一部分) 執行教學課程。
- 按一下頂端的[使用 PowerShell](data-factory-build-your-first-pipeline-using-powershell.md) 連結，使用 Azure PowerShell 執行教學課程。
- 按一下頂端的[使用 Visual Studio](data-factory-build-your-first-pipeline-using-vs.md) 連結以使用 Visual Studio 執行本教學課程。 

## 傳送意見
非常感謝您對本文的意見反應。請花幾分鐘的時間透過[電子郵件](mailto:adfdocfeedback@microsoft.com?subject=data-factory-build-your-first-pipeline.md)提交您的意見反應。

<!---HONumber=September15_HO1-->