<properties 
	pageTitle="Data Factory - .NET API 變更記錄 | Microsoft Azure" 
	description="描述 Azure Data Factory 的特定版本 NET API 中的重大變更、新增功能、錯誤修正等等..." 
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
	ms.date="11/10/2015" 
	ms.author="spelluru"/>

# Azure Data Factory - .NET SDK 變更記錄 
本文章提供 Azure Data Factory SDK 在特定版本中有何變更的相關資訊。您可以在[這裡](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactories)找到 Azure Data Factory 的最新 Nuget 封裝

## 4\.2.0 版
發行日期：2015-11-10

### 新增功能

- 新增下列新的活動類型：[AzureMLUpdateResourceActivity](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremlupdateresourceactivity.aspx)。如需有關活動的詳細資訊，請參閱[使用更新資源活動更新 Azure ML 模型](https://azure.microsoft.com/documentation/articles/data-factory-azure-ml-batch-execution-activity/#updating-azure-ml-models-using-the-update-resource-activity)。
- 新的選擇性屬性 [updateResourceEndpoint](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremllinkedservice.updateresourceendpoint.aspx) 已加入 [AzureMLLinkedService 類別](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremllinkedservice.aspx)。 
- [LongRunningOperationInitialTimeout](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.longrunningoperationinitialtimeout.aspx) 和 [LongRunningOperationRetryTimeout](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.longrunningoperationretrytimeout.aspx) 屬性已加入 [DataFactoryManagementClient](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.aspx) 類別。 
- 允許設定用戶端呼叫 Data Factory 服務的逾時值。 


## 4\.1.0 版
發行日期：2015 年 10 月 28 日

### 新增功能
* 已加入下列連結服務類型： 
    * [AzureDataLakeStoreLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx)
    * [AzureDataLakeAnalyticsLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)
* 已加入下列活動類型： 
    * [DataLakeAnalyticsUSQLActivity](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datalakeanalyticsusqlactivity.aspx)
* 已加入下列資料集類型： 
    * [AzureDataLakeStoreDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoredataset.aspx)
* 已加入複製活動的下列來源和接收器類型：
    * [AzureDataLakeStoreSource](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoresource.aspx)
    * [AzureDataLakeStoreSink](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoresink.aspx)


## 4\.0.1 版
發行日期：2015-10-13

### 重大變更
下列類別已重新命名。新名稱為類別在 4.0.0 版之前的原始名稱。
 
4\.0.0 中的名稱 | 4\.0.1 中的名稱
:------------ | :------------ 
AzureSqlDataWarehouseDataset | [AzureSqlDataWarehouseTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuresqldatawarehousetabledataset.aspx)
AzureSqlDataset | [AzureSqlTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuresqltabledataset.aspx)
AzureDataset | [AzureTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuretabledataset.aspx)
OracleDataset | [OracleTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.oracletabledataset.aspx)
RelationalDataset | [RelationalTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.relationaltabledataset.aspx)
SqlServerDataset | [SqlServerTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.sqlservertabledataset.aspx)


## 4\.0.0 版
發行日期：2015-10-02

### 重大變更



- 下列類別/介面已重新命名。

| 舊名稱 | 新名稱 |
| :-------- | :-------- |
| ITableOperations | [IDatasetOperations](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.idatasetoperations.aspx) |  
| 資料表 | [Dataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.dataset.aspx) | 
| TableProperties | [DatasetProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetproperties.aspx) | 
| TableTypeProprerties | [DatasetTypeProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasettypeproperties.aspx) |
| TableCreateOrUpdateParameters | [DatasetCreateOrUpdateParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdateparameters.aspx) | 
| TableCreateOrUpdateResponse | [DatasetCreateOrUpdateResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdateresponse.aspx) | 
| TableGetResponse | [DatasetGetResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetgetresponse.aspx) | 
| TableListResponse | [DatasetListResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetlistresponse.aspx) |
| CreateOrUpdateWithRawJsonContentParameters | [DatasetCreateOrUpdateWithRawJsonContentParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdatewithrawjsoncontentparameters.aspx) | 
    
- 這個發行版本的 **API 版本**為：**2015-10-01**。

- **List** 方法現在會傳回分頁式結果。如果回應包含非空白的 **NextLink** 屬性，用戶端應用程式需要繼續擷取下一個頁面，直到傳回了所有頁面為止。下列是一個範例：

		PipelineListResponse response = client.Pipelines.List("ResourceGroupName", "DataFactoryName");
	    var pipelines = new List<Pipeline>(response.Pipelines);
	
	    string nextLink = response.NextLink;
	    while (string.IsNullOrEmpty(response.NextLink))
	    {
	        PipelineListResponse nextResponse = client.Pipelines.ListNext(nextLink);
	        pipelines.AddRange(nextResponse.Pipelines);
	
	        nextLink = nextResponse.NextLink;
	    }
	
- **List** 管線 API 只會傳回管線的摘要，而不是完整的詳細資料。例如，管線摘要中的活動只包含名稱和類型。

### 新增功能
- [SqlDWSink](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsink.aspx) 類別支援兩個新屬性 (**SliceIdentifierColumnName** 和 **SqlWriterCleanupScript**)，以支援將資料等冪複製到 Azure SQL 資料倉儲。如需這些屬性的詳細資訊，請參閱 [Azure SQL 資料倉儲](data-factory-azure-sql-data-warehouse-connector.md)一文，尤其是[機制 1](data-factory-azure-sql-data-warehouse-connector.md#mechanism-1) 和 [機制 2](data-factory-azure-sql-data-warehouse-connector.md#mechanism-2) 小節。

- 我們現在支援對 Azure SQL Database 和 Azure SQL 資料倉儲的來源執行預存程序，做為複製活動的一部分。[SqlSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqlsource.aspx) 和 [SqlDWSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsource.aspx) 類別具有下列屬性，以支援此功能：**SqlReaderStoredProcedureName** 和 **StoredProcedureParameters**。如需這些屬性的詳細資訊，請參閱 Azure.com 上的 [Azure SQL Database](data-factory-azure-sql-connector.md#sqlsource) 和 [Azure SQL 資料倉儲](data-factory-azure-sql-data-warehouse-connector.md#sqldwsource)文章。

<!---HONumber=Nov15_HO3-->