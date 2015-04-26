<properties title="Azure Data Factory - Release Notes" pageTitle="Data Factory - 版本資訊 | Azure" description="資料處理站版本資訊" metaKeywords=""  services="data-factory" solutions=""  documentationCenter="" authors="spelluru" manager="jhubbard" editor="monicar" />

<tags ms.service="data-factory" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/04/2014" ms.author="spelluru" />

# Azure Data Factory 版本資訊

## Data Factory 2014/12/11/ 版本的相關資訊 ##

### 新的改進功能

- Azure Machine Learning 整合
	- 這一版的 Azure Data Factory 服務可讓您使用 **AzureMLLinkedService** 和 **AzureMLBatchScoringActivity** 將 Azure Data Factory 與 Azure Machine Learning (ML) 整合。如需詳細資料，請參閱[使用 Data Factory 和 Azure Machine Learning 建立預測管線][adf-azure-ml]。 
- 提供閘道版本狀態
	- 如果有比目前安裝之版本還要新的閘道，「Azure Preview 入口網站」和 Get-AzureDataFactoryGateway Cmdlet 的輸出中就會顯示 "NewVersionAvailable" 狀態。。您可以接著依照入口網站程序下載新的安裝檔案 (.msi)，然後執行它來安裝最新的閘道。不需要進行其他設定。

### 變更

- 已移除 HdInsightOnDemandLinkedService 中的 JobsContainer。
	- 在 HDInsightOnDemandLinkedService 的 JSON 定義中，您已不再需要指定 **jobsContainer** 屬性。如果您有為隨選連結服務指定的屬性，則會忽略此屬性。您可以從連結服務的 JSON 定義中移除屬性，然後使用 New-AzureDataFactoryLinkedService Cmdlet 來更新連結服務的定義。
- HDInsightOnDemandLinkedService 的選擇性組態參數
	- 此版本針對 HDInsightOnDemandLinked (隨選 HDInsight 叢集) 導入一些選擇性組態參數支援。如需詳細資料，請參閱[ClusterCreateParameters 屬性][on-demand-hdi-parameters]。
- 已移除閘道位置
	- 透過入口網站或 PowerShell (New-AzureDataFactoryGateway) 建立 Azure Data Factory 閘道時，您不再需要指定閘道的位置。將會繼承 Data Factory 地區。同樣地，若要使用 JSON 來設定 SQL Server 連結服務，也不再需要 "gatewayLocation" 屬性。Data Factory .NET SDK 也已更新來反映這些變更。
	- 如果您使用較舊版的 SDK 和 Azure PowerShell，則仍然必須提供位置設定。
 
     

#### 重大變更
	
- CustomActivity 變更為 DotNetActivity
	- **ICustomActivity** 介面已重新命名為 **IDotNetActivity**。您將必須更新 Data Factory NuGet 封裝，將您自訂活動之原始程式碼中的 ICustomActivity 變更為 IDotNetActivity。  
	- 您自訂活動之 JSON 定義中的自訂活動類型必須從 **CustomActivity** 變更為 **DotNetActivity**。 
	- **CustomActivity** 和 **CustomActivityProperties** 類別已重新命名為具有一組相同屬性的 **DotNetActivity** 和 **DotNetActivityProperties**。

		如果您使用舊版 SDK 和 Azure PowerShell，則可以繼續使用 CustomActivity，而不需改成 DotNetActivity。
    
  		如需有關如何建立自訂活動並在 Azure Data Factory 管線中使用此活動的逐步解說，請參閱[在 Azure Data Factory 管線中使用自訂活動][adf-custom-activities]。  

[adf-azure-ml]: ../data-factory-create-predictive-pipelines
[adf-custom-activities]: ../data-factory-use-custom-activities
[on-demand-hdi-parameters]: http://msdn.microsoft.com/zh-tw/library/microsoft.windowsazure.management.hdinsight.clustercreateparameters_properties.aspx



<!--HONumber=35.2-->
