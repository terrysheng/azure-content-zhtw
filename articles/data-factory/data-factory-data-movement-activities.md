<properties 
	pageTitle="資料移動活動" 
	description="了解您可以在 Data Factory 管線用來移動資料的 Data Factory 實體。" 
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
	ms.date="07/29/2015" 
	ms.author="spelluru"/>

# 資料移動活動
資料處理站有[供全域使用服務](#global)以跨下方所列之不同資料存放區使用[複製活動](#copyactivity)支援資料移動。資料處理站也有[安全地在內部部署位置與雲端之間移動資料](#moveonpremtocloud)的內建支援，使用資料管理閘道器。

## 複製活動支援的資料存放區
複製活動會將資料從**來源**資料存放區複製到**接收**資料存放區。資料處理站支援下列資料存放區和來源接收組合。按一下資料存放區以了解如何從該存放區複製資料以及將資料複製到該存放區。

| **來源** | **接收** |
| ------ | ---- |
| [Azure Blob](data-factory-azure-blob-connector.md) | Azure Blob、Azure 資料表、Azure SQL Database、內部部署 SQL Server、IaaS 上的 SQL Server、Azure DocumentDB |
| [Azure 資料表](data-factory-azure-table-connector.md) | Azure Blob、Azure 資料表、Azure SQL Database、內部部署 SQL Server、IaaS 上的 SQL Server、Azure DocumentDB |
| [Azure SQL Database](data-factory-azure-sql-connector.md) | Azure Blob、Azure 資料表、Azure SQL Database、內部部署 SQL Server、IaaS 上的 SQL Server、Azure DocumentDB |
| [Azure DocumentDB](data-factory-azure-documentdb-connector.md) | Azure Blob、Azure 資料表、Azure SQL Database |
| [IaaS 上的 SQL Server](data-factory-sqlserver-connector.md) | Azure Blob、Azure 資料表、Azure SQL Database、內部部署 SQL Server、IaaS 上的 SQL Server |
| [內部部署檔案系統](data-factory-onprem-file-system-connector.md) | Azure Blob、Azure 資料表、Azure SQL Database、內部部署 SQL Server、IaaS 上的 SQL Server |
| [內部部署 SQL Server](data-factory-sqlserver-connector.md) | Azure Blob、Azure 資料表、Azure SQL Database、內部部署 SQL Server、IaaS 上的 SQL Server |
| [內部部署 Oracle 資料庫](data-factory-onprem-oracle-connector.md) | Azure Blob、Azure 資料表、Azure SQL Database、內部部署 SQL Server、IaaS 上的 SQL Server |
| [內部部署 MySQL 資料庫](data-factory-onprem-mysql-connector.md) | Azure Blob、Azure 資料表、Azure SQL Database、內部部署 SQL Server、IaaS 上的 SQL Server |
| [內部部署 DB2 資料庫](data-factory-onprem-db2-connector.md) | Azure Blob、Azure 資料表、Azure SQL Database、內部部署 SQL Server、IaaS 上的 SQL Server |
| [內部部署 Teradata 資料庫](data-factory-onprem-teradata-connector.md) | Azure Blob、Azure 資料表、Azure SQL Database、內部部署 SQL Server、IaaS 上的 SQL Server |
| [內部部署 Sybase 資料庫](data-factory-onprem-sybase-connector.md) | Azure Blob、Azure 資料表、Azure SQL Database、內部部署 SQL Server、IaaS 上的 SQL Server |
| [內部部署 PostgreSQL 資料庫](data-factory-onprem-postgresql-connector.md) | Azure Blob、Azure 資料表、Azure SQL Database、內部部署 SQL Server、IaaS 上的 SQL Server |

## <a name="copyactivity"></a>複製活動
複製活動採用輸入資料集 (**來源**) 並將每個活動組態的資料複製到輸出資料集 (**接收**)。資料複製是根據活動上指定的排程以批次方式完成。

> [AZURE.NOTE]若要了解高層級的整體活動定義，例如各種 JSON 區段和所有活動都可用的屬性，請參閱[了解管線 & 活動](data-factory-create-pipelines.md)一文。

複製活動提供下列功能：

### <a name="global"></a>全域可用的資料移動
資料移動服務支援的複製活動可供下列區域和地理位置全域使用。全域可用性的拓撲可確保有效資料移動，避免大部分情況下的跨區域躍點。

| 區域 | [地理位置] |
| ------ | --------- | 
| 美國中部 | US |
| 美國東部 | US |
| 美國東部 2 | US |
| 美國中北部 | US |
| 美國中南部 | US |
| 美國西部 | US |
| 巴西南部 | 拉丁美洲 |
| 北歐 | EMEA |
| 西歐 | EMEA |
| 東南亞 | APAC |
| 日本東部 | APAC |

### <a name="moveonpremtocloud"></a>安全地在內部部署位置與雲端之間移動資料
現代資料整合的挑戰之一是順暢地在內部部署和雲端之間來回移動資料。資料管理閘道器是您可以安裝內部部署以啟用混合式資料管線的代理程式。

資料閘道器提供下列功能：

1.	安全地管理內部部署資料存放區的存取權。
2.	在相同資料處理站內建立內部部署資料存放區和雲端資料存放區的模型及移動資料。
3.	具有用於監視和管理的單一窗格，可利用資料處理站雲端為基礎的儀表板看見閘道器的狀態。


請參閱[在內部部署和雲端之間移動資料](data-factory-move-data-between-onprem-and-cloud.md)以取得詳細資料。

### 可靠且符合成本效益的資料移動
複製活動設計為以可靠的方式移動大量資料，可跨各種資料來源抵抗暫時性錯誤。資料可以符合成本效益的方式利用此選項複製，以透過線路啟用壓縮。

### 跨不同類型系統的類型轉換
不同的資料存放區有不同的原生類型系統。複製活動會利用下列 2 個步驟的方法執行自動類型轉換，從來源類型到接收類型：

1. 從原生來源類型轉換成 .NET 類型
2. 從 .NET 類型轉換成原生接收類型

您可以在各自的資料存放區連接器文章中的資料存放區找到指定原生類型系統到 .NET 的對應。您可以在建立資料表時使用這些對應來判斷適當的類型，就會在複製活動期間執行正確的轉換。

### 使用不同的檔案格式。
對於以檔案為基礎的來源，複製活動支援各種不同的檔案格式，包括二進位、文字和 Avro 格式。

### 複製活動屬性
名稱、描述、輸入和輸出資料表、各種原則等屬性都適用於所有活動類型。另一方面，活動的 **typeProperties** 區段中可用的屬性會隨著每項活動類型而有所不同。

以複製活動而言，**typeProperties** 區段會根據來源和接收等類型而有所不同。以上所列的每個資料存放區特定頁面都會記錄這些資料存放區類型專屬的屬性。

<!---HONumber=August15_HO6-->