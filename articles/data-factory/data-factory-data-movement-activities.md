<properties 
	pageTitle="資料移動活動" 
	description="了解您可以在 Data Factory 管線中用來移動資料的 Data Factory 實體。" 
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
	ms.topic="article" 
	ms.date="02/03/2016" 
	ms.author="spelluru"/>

# 資料移動活動
[複製活動](#copyactivity)會在 Azure Data Factory 中執行資料移動，而此活動是由[全域可用的服務](#global)所提供，可以使用安全、可靠及可調整的方式，在各種不同的資料存放區之間複製資料。服務會自動選擇最佳區域來執行資料移動。系統會使用最靠近接收資料存放區的區域。
 
讓我們來了解在不同情況下發生這個資料移動的方式。

## 在兩個雲端資料存放區之間複製資料
當來源和接收 (目的地) 資料存放區同時位於雲端時，複製活動就會經歷下列階段，將資料從來源複製/移動到接收資料存放區。支援複製活動的服務會執行下列作業：

1. 從來源資料存放區讀取資料
2.	根據輸入資料集、輸出資料集和複製活動的組態，執行序列化/還原序列化、壓縮/解壓縮、資料行對應及類型轉換 
3.	將資料寫入目的地資料存放區

![從雲端複製到雲端](.\media\data-factory-data-movement-activities\cloud-to-cloud.png)


## 在內部部署資料存放區和雲端資料存放區之間複製資料
若要[安全地在公司防火牆背後的內部部署資料存放區和雲端資料存放區之間移動資料](#moveonpremtocloud)，您必須安裝資料管理閘道，這是一個代理程式，能夠在內部部署的電腦上啟用混合式資料移動及處理。資料管理閘道可以和資料存放區本身安裝於同一部電腦上，或者安裝於具備可觸達該資料存放區之存取權的個別電腦上。在此案例中，序列化/還原序列化、壓縮/解壓縮、資料行對應及類型轉換都是透過資料管理閘道來執行。資料不流經 Azure Data Factory 服務就是這種情況。資料管理閘道會直接將資料寫入到目的地存放區。

![從內部部署複製到雲端](.\media\data-factory-data-movement-activities\onprem-to-cloud.png)

## 複製自/至 Azure Iaas VM 上資料存放區的資料 
您也可以使用資料管理閘道，將資料移出/移入裝載於 Azure IaaS VM (基礎結構即為服務的虛擬機器) 上支援的資料存放區。在此情況下，資料管理閘道可以和資料存放區本身安裝於同一部 Azure VM 上，或者安裝於具備可觸達該資料存放區之存取權的個別 VM 上。

## 支援的資料存放區
複製活動會將資料從**來源**資料存放區複製到**接收**資料存放區。Data Factory 支援下列資料存放區，且**來自任何來源的資料都可以寫入任何接收**。按一下資料存放區以了解如何從該存放區複製資料以及將資料複製到該存放區。

| 來源| 接收 |
|:------- | :---- |
| <ul><li>[Azure Blob](data-factory-azure-blob-connector.md)</li><li>[Azure 資料表](data-factory-azure-table-connector.md)</li><li>[Azure SQL Database](data-factory-azure-sql-connector.md)</li><li>[Azure SQL 資料倉儲](data-factory-azure-sql-data-warehouse-connector.md)</li><li>[Azure DocumentDB (請參閱下面附註)](data-factory-azure-documentdb-connector.md)</li><li>[Azure 資料湖存放區](data-factory-azure-datalake-connector.md)</li><li>[內部部署 SQL Server /Azure IaaS](data-factory-sqlserver-connector.md)</li><li>[內部部署檔案系統/Azure IaaS](data-factory-onprem-file-system-connector.md)</li><li>[內部部署 Oracle Database/Azure IaaS](data-factory-onprem-oracle-connector.md)</li><li>[內部部署 MySQL 資料庫/Azure IaaS ](data-factory-onprem-mysql-connector.md)</li><li>[內部部署 DB2 資料庫/Azure IaaS](data-factory-onprem-db2-connector.md)</li><li>[內部部署 Teradata 資料庫/Azure IaaS ](data-factory-onprem-teradata-connector.md)</li><li>[內部部署 Sybase 資料庫/Azure IaaS](data-factory-onprem-sybase-connector.md)</li><li>[內部部署 PostgreSQL 資料庫/Azure IaaS](data-factory-onprem-postgresql-connector.md)</li><li>[內部部署 ODBC 資料來源/Azure IaaS](data-factory-odbc-connector.md)</li><li>[內部部署 Hadoop 分散式檔案系統 (HDFS)/Azure IaaS](data-factory-hdfs-connector.md)</li><li>[OData 來源](data-factory-odata-connector.md)</li><li>[Web 資料表](data-factory-web-table-connector.md)</li></ul> | <ul><li>[Azure Blob](data-factory-azure-blob-connector.md)</li><li>[Azure 資料表](data-factory-azure-table-connector.md)</li><li>[Azure SQL 資料庫](data-factory-azure-sql-connector.md)</li><li>[Azure SQL 資料倉儲](data-factory-azure-sql-data-warehouse-connector.md)</li><li>[Azure DocumentDB (請參閱下面附註)](data-factory-azure-documentdb-connector.md)</li><li>[Azure 資料湖存放區](data-factory-azure-datalake-connector.md)</li><li>[內部部署 SQL Server /Azure IaaS](data-factory-sqlserver-connector.md)</li><li>[內部部署檔案系統/Azure IaaS](data-factory-onprem-file-system-connector.md)</li></ul> |


> [AZURE.NOTE] 您只可在 Azure DocumentDB 和其他 Azure 服務 (例如 Azure Blob、Azure 資料表、Azure SQL Database、Azure SQL 資料倉儲、Azure DocumentDB 和 Azure 資料湖存放區) 之間來回移動。短期內也會支援 Azure Document DB 的完整矩陣。

## 教學課程
如需使用複製活動的快速教學課程，請參閱[教學課程：在 Azure Data Factory 管線中使用複製活動](data-factory-get-started.md)。在教學課程中，您會使用複製活動將資料從 Azure Blob 儲存體複製到 Azure SQL 資料庫。

## <a name="copyactivity"></a>複製活動
複製活動會採用一個輸入資料集 (**來源**) 和一個輸出資料集 (**接收**)。資料複製是根據活動上指定的排程以批次方式完成。若要了解定義活動的概觀，請參閱[了解管線和活動](data-factory-create-pipelines.md)文章。

複製活動提供下列功能：

### <a name="global"></a>全域可用的資料移動
即使 Azure Data Factory 本身只能在美國西部和北歐地區使用，但是服務支援的複製活動可供下列區域和地理位置全域使用。全域可用性的拓撲可確保有效資料移動，避免大部分情況下的跨區域躍點。

在複製作業中，**資料管理閘道器**或 **Azure Data Factory** 會根據來源的位置和目的地資料存放區執行資料移動。請參閱下表以取得詳細資料：

來源資料存放區位置 | 目的地資料存放區位置 | 資料移動的執行者  
-------------------------- | ------------------------------- | ----------------------------- 
內部部署/Azure VM (IaaS) | 雲端 | 內部部署電腦/Azure VM 上的**資料管理閘道器**。資料不流經雲端服務。<p> 注意：資料管理閘道器可以位於資料存放區所處的內部部署電腦/Azure VM，也可以位於另一個內部部署電腦/Azure VM，只要可以連接至這兩個資料存放區即可。</p>
雲端 | 內部部署/Azure VM (IaaS) | 同上。 
內部部署/Azure VM (IaaS) | 內部部署/Azure VM | **與來源相關聯的資料管理閘道器**。資料不流經雲端服務。請參閱上面的注意事項。   
雲端 | 雲端 | <p>**支援複製活動的雲端服務**。Azure Data Factory 會在最接近相同地理位置之接收位置的區域中使用此服務部署。請參閱下表以了解對應：</p><table><tr><th>目的地資料存放區的區域</th> <th>用於資料移動的區域</th></tr><tr><td>美國東部</td><td>美國東部</td></tr><tr><td>美國東部 2</td><td>美國東部 2</td><tr/><tr><td>美國中部</td><td>美國中部</td><tr/><tr><td>美國西部</td><td>美國西部</td></tr><tr><td>美國中北部</td><td>美國中北部</td></tr><tr><td>美國中南部</td><td>美國中南部</td></tr><tr><td>北歐</td><td>北歐</td></tr><tr><td>西歐</td><td>西歐</td></tr><tr><td>東南亞</td><td>東南亞</td></tr><tr><td>東亞</td><td>東南亞</td></tr><tr><td>日本東部</td><td>日本東部</td></tr><tr><td>日本西部</td><td>日本東部</td></tr><tr><td>巴西南部</td><td>巴西南部</td></tr><tr><td>澳大利亞東部</td><td>澳大利亞東部</td></tr><tr><td>澳大利亞東南部</td><td>澳大利亞東南部</td></tr></table>


> [AZURE.NOTE] 如果目的地資料存放區的區域不在上述清單中，複製活動將會失敗而不是前往替代區域。



### <a name="moveonpremtocloud"></a>安全地在內部部署位置與雲端之間移動資料
現代資料整合的挑戰之一是順暢地在內部部署和雲端之間來回移動資料。資料管理閘道器是您可以安裝內部部署以啟用混合式資料管線的代理程式。

資料閘道器提供下列功能：

1.	安全地管理內部部署資料存放區的存取權。
2.	在相同資料處理站內建立內部部署資料存放區和雲端資料存放區的模型及移動資料。
3.	具有用於監視和管理的單一窗格，可利用資料處理站雲端為基礎的儀表板看見閘道器的狀態。

您應該將資料來源視為內部部署資料來源 (亦即在防火牆後面)，即使您使用 **ExpressRoute** 和**使用閘道**來建立服務與資料來源之間的連接也一樣。

如需詳細資料，請參閱[在內部部署和雲端之間移動資料](data-factory-move-data-between-onprem-and-cloud.md)。

### 可靠且符合成本效益的資料移動
複製活動是設計來利用可靠的方式移動大量資料，可跨各種資料來源抵抗暫時性錯誤。資料可以符合成本效益的方式利用此選項複製，以透過線路啟用壓縮。

### 跨不同類型系統的類型轉換
不同的資料存放區有不同的原生類型系統。複製活動會利用下列 2 個步驟的方法執行自動類型轉換，從來源類型到接收類型：

1. 從原生來源類型轉換成 .NET 類型
2. 從 .NET 類型轉換成原生接收類型

您可以在各自的資料存放區連接器文章中的資料存放區找到指定原生類型系統到 .NET 的對應。您可以在建立資料表時使用這些對應來判斷適當的類型，如此就能在複製活動期間執行正確的轉換。

### 使用不同的檔案格式。
複製活動支援各種不同的檔案格式，包括二進位、文字及 Avro 格式，以用於檔案式存放區。您可以使用複製活動，將資料從某種格式轉換為另一種。範例：文字 (CSV) 轉 Avro。如果是非結構化的資料，您可以省略 [資料集](data-factory-create-datasets.md) JSON 定義中的 **Structure** 屬性。

### 複製活動屬性
名稱、描述、輸入和輸出資料表、各種原則等屬性都適用於所有活動類型。另一方面，活動的 **typeProperties** 區段中可用的屬性會隨著每個活動類型而有所不同。

以複製活動而言，**typeProperties** 區段會根據來源和接收的類型而有所不同。以上所列的每個資料存放區特定頁面都會記錄這些資料存放區類型專屬的屬性。


### 複製活動的效能及微調 
請參閱「[複製活動的效能及微調指南](data-factory-copy-activity-performance.md)」一文，其中說明在 Azure Data Factory 中會影響資料移動 (複製活動) 效能的重要因素。它也列出在內部測試期間所觀察到的效能，並討論要讓複製活動效能最佳化的各種方式。

<!---HONumber=AcomDC_0302_2016-->