<properties 
   pageTitle="有關 Data Lake 存放區的資料案例 | Microsoft Azure" 
   description="了解用來內嵌、處理、下載及視覺化 Data Lake 存放區之資料的各種案例和工具" 
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
   ms.date="03/09/2016"
   ms.author="nitinme"/>

# 有關 Azure Data Lake 存放區的資料案例

巨量資料處理有四個主要階段︰

* 即時或以批次形式將大量資料內嵌到存放區
* 處理資料
* 下載資料
* 將資料視覺化


在本文中，我們探討這些與 Azure Data Lake 存放區有關的階段，以了解可用來滿足您巨量資料需求的選項與工具。

## 將資料內嵌到 Data Lake 存放區

本章節強調不同的資料來源，以及將資料內嵌到 Data Lake 存放區帳戶的各種方式。

![將資料內嵌到 Data Lake 存放區](./media/data-lake-store-data-scenarios/ingest-data.png "將資料內嵌到 Data Lake 存放區")

### 臨機操作資料

代表用來建立巨量資料應用程式原型的較小型資料集。內嵌臨機操作資料的方式會因資料來源不同而有所差異。

| 資料來源 | 內嵌方式 |
|--------------------|----------------------------------------------------------------------------------------|
| 本機電腦 | <ul> <li>[Azure 入口網站](/data-lake-store-get-started-portal.md)</li> <li>[Azure PowerShell](data-lake-store-get-started-powershell.md)</li> <li>[Azure 跨平台 CLI](data-lake-store-get-started-cli.md)</li> <li>[使用適用於 Visual Studio 的 Data Lake 工具](../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md#upload-source-data-files) </li></ul> |
| Azure 儲存體 Blob | <ul> <li>[Azure Data Factory](../data-factory/data-factory-azure-datalake-connector.md#sample-copy-data-from-azure-blob-to-azure-data-lake-store)</li> <li>[AdlCopy 工具](data-lake-store-copy-data-azure-storage-blob.md)</li> </ul> |

 
### 串流資料

代表可能由應用程式、裝置、感應器等各種來源產生的資料。這些資料可透過多種工具內嵌到 Data Lake 存放區。這些工具通常能以事件為基礎即時擷取及處理資料，然後再以批次將事件寫入 Data Lake 存放區，以供進一步處理。

以下是您可以使用的工具︰
 
* [Azure 串流分析](../stream-analytics-data-lake-output)：內嵌到事件中樞的事件可以使用 Azure Data Lake Store 輸出寫入 Azure Data Lake。
* [Azure HDInsight Storm](../hdinsight/hdinsight-storm-write-data-lake-store.md)：您可以從 Storm 叢集將資料直接寫入 Data Lake Store。
* [EventProcessorHost](../event-hubs/event-hubs-csharp-ephcs-getstarted.md#receive-messages-with-eventprocessorhost)：您可以接收來自事件中樞的事件，然後使用[Data Lake Store .NET SDK](data-lake-store-get-started-net-sdk.md) 寫入 Data Lake Store。

### 關聯式資料

您也可以從關聯式資料庫取得資料。每經過一段時間，關聯式資料庫就會收集大量資料，在經過巨量資料管線處理後，這些資料將可提供重要情資。您可以使用下列工具，將這類資料移動到 Data Lake 存放區。

* [Apache Sqoop](data-lake-store-data-transfer-sql-sqoop.md)
* [Azure Data Factory](../data-factory/data-factory-data-movement-activities.md)

### Web 伺服器記錄資料 (使用自訂應用程式上傳)

我們會特別強調這類資料集的原因在於，因為 Web 伺服器記錄資料是巨量資料應用程式的常見使用案例，且需要將大量記錄檔上傳到 Data Lake 存放區。您可以使用以下任何工具來撰寫自己的指令碼或應用程式，以便上傳這類資料。

* [Azure 跨平台 CLI](data-lake-store-get-started-cli.md)
* [Azure PowerShell](data-lake-store-get-started-powershell.md)
* [Azure Data Lake 存放區 .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Azure Data Factory](../data-factory/data-factory-data-movement-activities.md)

若要上傳 Web 伺服器記錄資料及上傳其他類型的資料 (如社交情緒資料)，撰寫自己的自訂指令碼/應用程式是個不錯方法，因為您可以彈性地將自己的資料上傳元件納入較大型的巨量資料應用程式中。在某些情況下，這段程式碼可能會採用指令碼或簡易命令列公用程式的形式。在其他情況下，程式碼可用來將巨量資料處理整合到商務應用程式或解決方案中。


### 與 Azure HDInsight 叢集相關聯的資料

大部分的 HDInsight 叢集類型 (Hadoop、HBase、Storm) 能以資料儲存存放機制的形式支援 Data Lake 存放區。HDInsight 叢集能從 Azure 儲存體 Blob (WASB) 存取資料。為了提高效能，您可以將資料從 WASB 複製到與叢集相關聯的 Data Lake 存放區帳戶。您可以使用下列工具來複製資料。

* [Apache DistCp](data-lake-store-copy-data-wasb-distcp.md)
* [AdlCopy 服務](data-lake-store-copy-data-azure-storage-blob.md)
* [Azure Data Factory](../data-factory/data-factory-azure-datalake-connector.md#sample-copy-data-from-azure-blob-to-azure-data-lake-store)

### 大型資料集

若要上傳動輒數 TB 的資料集，使用上述方法有時候可能會過於緩慢且昂貴。此時，您可以使用下列選項。

* **「離線」上傳資料**。您可以使用 [Azure 匯入/匯出服務](../storage/storage-import-export-service.md)將容納資料的硬碟運送到 Azure 資料中心，之後再上傳到 Azure 儲存體 Blob。接下來，您可以使用 [Azure Data Factory](../data-factory/data-factory-azure-datalake-connector.md#sample-copy-data-from-azure-blob-to-azure-data-lake-store) 或 [AdlCopy 工具](data-lake-store-copy-data-azure-storage-blob.md)將資料從 Azure 儲存體 Blob 移動到 Data Lake Store。

	>[AZURE.NOTE] 在使用匯入/匯出服務時，運送到 Azure 資料中心之磁碟上的檔案大小不得大於 200 GB。

* **使用 Azure ExpressRoute**。Azure ExpressRoute 可讓您在 Azure 資料中心與內部部署的基礎結構之間建立私人連線。這是傳輸大量資料的可靠選項。如需詳細資訊，請參閱 [Azure ExpressRoute 文件](../expressroute/expressroute-introduction.md)。

## 處理儲存在 Data Lake 存放區中的資料

一旦可以取用 Data Lake 存放區中的資料後，您就可以使用支援的巨量資料應用程式來針對這些資料執行分析。目前，您可以使用 Azure HDInsight 和 Azure Data Lake 分析來針對儲存在 Data Lake 存放區中的資料執行資料分析工作。

![分析 Data Lake 存放區中的資料](./media/data-lake-store-data-scenarios/analyze-data.png "分析 Data Lake 存放區中的資料")

您可以查看下列範例。

* [建立以 Data Lake 存放區當做儲存體的 HDInsight 叢集](data-lake-store-hdinsight-hadoop-use-portal.md)
* [搭配 Data Lake 存放區使用 Azure Data Lake 分析](../data-lake-analytics/data-lake-analytics-get-started-portal.md)



## 從 Data Lake 存放區下載資料

在以下案例中，您可能也會想要從 Azure Data Lake 存放區下載資料或移動資料：

* 將資料移動到其他儲存機制，以便與現有的資料處理管線連結。例如，您可能會想要將資料從 Data Lake 存放區移動到 Azure SQL Database 或內部部署 SQL Server。
* 在建置應用程式原型時，將資料下載到本機電腦，以便在 IDE 環境中處理。

![從 Data Lake 存放區輸出資料](./media/data-lake-store-data-scenarios/egress-data.png "從 Data Lake 存放區輸出資料")

在這些案例中，您可以使用下列任何選項。

* [Apache Sqoop](data-lake-store-data-transfer-sql-sqoop.md)
* [Azure Data Factory](../data-factory/data-factory-data-movement-activities.md)
* [Apache DistCp](data-lake-store-copy-data-wasb-distcp.md)

您也可以使用下列方法來撰寫自己的指令碼/應用程式，以便從 Data Lake 存放區下載資料。

* [Azure 跨平台 CLI](data-lake-store-get-started-cli.md)
* [Azure PowerShell](data-lake-store-get-started-powershell.md)
* [Azure Data Lake 存放區 .NET SDK](data-lake-store-get-started-net-sdk.md)

## 將 Data Lake 存放區中的資料視覺化

您可以混合使用多種服務，利用視覺化的方式呈現儲存在 Data Lake 存放區中的資料。

![將 Data Lake 存放區中的資料視覺化](./media/data-lake-store-data-scenarios/visualize-data.png "將 Data Lake 存放區中的資料視覺化")

* 您可以從使用 [Azure Data Factory 將資料從 Data Lake Store 移動到 Azure SQL 資料倉儲](../data-factory/data-factory-data-movement-activities.md#supported-data-stores)開始
* 之後，您可以[整合 Power BI 與 SQL 資料倉儲](../sql-data-warehouse/sql-data-warehouse-integrate-power-bi)，利用視覺化的方式呈現資料。

<!---HONumber=AcomDC_0323_2016-->