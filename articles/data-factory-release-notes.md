<properties 
	pageTitle="Data Factory - 版本資訊 | Azure" 
	description="資料處理站版本資訊" 
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
	ms.date="04/10/2015" 
	ms.author="spelluru"/>

# Azure Data Factory 版本資訊

## 04/10/2015年版本的資料處理站資訊
您會看到 **最近更新配量** 和 **最近無法配量** 上列出 **資料表** 現在分頁。這些清單會依照配量的更新時間。配量的更新時間是在下列情況中變更。

-  您扇區的狀態手動更新，例如，使用 **組 AzureDataFactorySliceStatus** (或) 依序按一下 **執行** 上 **配量** 配量的分頁。
-  配量因為執行的狀態變更 (例如啟動測試回合、 結束，而且無法執行、 測試回合結束，而且已成功，等等)。

按一下標題的清單或 **...(橢圓形)** 若要查看較大配量的清單。按一下 **篩選** 篩選配量] 工具列上。
 
您仍然可以檢視，即可依照配量的時間配量 **(藉由配量的時間) 的資料配量** 並排顯示。在這些集合中的扇區配量的時間來排序。例如，如果是每小時的排程，配量會是：-4/4/2015年正在進行中-4/4/2015年 4 下午 5 點 pm 成功-4/4/2015年 3 pm 失敗

但是，如果重新執行較舊的配量，就會不顯示頂端這份清單，即使這可能是使用者何謂更感興趣。

## 3/31/2015年版本的資料處理站資訊
- 更新 **資料管理閘道器** 安裝套件已張貼至 [Microsoft 下載中心取得][adf-gateway-download]。
- 從複製 **內部檔案系統到 Azure blob** 目前支援。請參閱下列主題取得詳細資訊。
	-  [內部檔案系統連結服務](https://msdn.microsoft.com/library/dn930836.aspx)
	-  [資料表 JSON 的 OnPremisesFileSystemLocation 屬性](https://msdn.microsoft.com/library/dn894089.aspx#OnPremFileSystem)
	-  [支援來源與接收](https://msdn.microsoft.com/library/dn894007.aspx)。請參閱 ＜ 更新的複本矩陣和 **FileSystemSource** 屬性。 
-  從複製 **在內部部署至 Azure blob 的 Oracle 資料庫** 目前支援。請參閱下列主題以取得詳細資料。 
	-  [在內部部署 Oracle 連結服務](https://msdn.microsoft.com/library/dn948537.aspx)
	-  [資料表 JSON 的 OnPremisesOracleTableLocation 屬性](https://msdn.microsoft.com/library/dn894089.aspx#Oracle) 
	-  [支援來源與接收](https://msdn.microsoft.com/library/dn894007.aspx)。請參閱 ＜ 更新的複本矩陣和 **OracleSource** 屬性。
-  您可以指定 Azure Blob 中的文字檔案的編碼方式。請參閱新 [encodingName 屬性](https://msdn.microsoft.com/library/dn894089.aspx#AzureBlob)。 
- 複製到 SQL Sink 時，您可以叫用與其他參數的預存程序。請參閱 [叫用預存程序 SQL Sink][adf-copy-advanced] 如需詳細資訊。   

請參閱張貼的 blob： [Azure 資料 Factory 更新新的資料存放區](http://azure.microsoft.com/blog/2015/03/30/azure-data-factory-update-new-data-stores/) 如需詳細資訊，包括範例。

## 資料處理站的 2/27/2015年版本資訊

### 新的改進功能
- **Azure 資料 Factory 編輯器**。資料處理站編輯器，這是 Azure 預覽入口網站的一部分，可讓您建立、 編輯及部署定義連結的服務、 資料集和管線的 JSON 檔案。[編輯器] 中的主要目標是提供快速且輕量級使用者介面 (UI)，而不需要您在上使用 PowerShell cmdlet 安裝 Azure PowerShell 和 ramp 上建立 Azure 資料 Factory 成品。請參閱 [Azure 資料 Factory 編輯器 Light 加權 Web 編輯器][adf-editor-blog] 部落格文章的快速概觀和影片資料 Factory 編輯器上。編輯器的詳細概觀，請參閱 [資料 Factory 編輯器][adf-editor] 發行項。          

### 變更

## 資料處理站的 1/26/2015年版本資訊 ##

### 變更
- 更新 **資料管理閘道器** 安裝套件已張貼至 [Microsoft 下載中心取得][adf-gateway-download]。從這個版本開始，您可以找到最新的資料管理閘道器来使用此下載位置的 Azure 資料處理站。此安裝套件既提供 Azure 資料 Factory Power BI for Office 365 服務。如果您使用這兩種服務，請注意資料處理站和 Power BI 的閘道器必須安裝在不同電腦上，並根據從資料處理站或 Power BI 文件的指導方針以不同方式設定。
-  **複製活動** 現在支援在內部部署 SQL Server 資料庫與 Azure SQL 資料庫之間複製資料。請參閱 [複製活動][adf-copy-activity] 如需詳細資訊和 [GitHub][adf-github-samples] JSON 範例。
- **SqlSink** 支援新的屬性： **WriteBatchTimeout**。這個屬性可讓您彈性地設定等候作業逾時前完成批次插入作業的時間長度。如需混合式複本 (包括在內部部署資料來源和雲端資料來源複製作業)，您必須擁有 1.4 版或更高版本，若要使用這個屬性的閘道。 
- **SQL Server 連結服務** 現在支援 **Windows 驗證**。 
	- 當建立 SQL Server 連結使用入口網站服務時，您現在可以選擇使用 Windows 驗證，並設定適當的認證。這需要您具有 1.4 版或更高的閘道。 
	- 當建立 SQL Server 連結服務使用 Azure PowerShell 時，您可以指定連接資訊以純文字或加密連接資訊，使用更新 [新增 AzureDataFactoryEncryptValue cmdlet][adf-encrypt-value-cmdlet] 連結的服務 JSON 裝載中的連接字串屬性然後使用加密的字串。請參閱 [連結服務][adf-msdn-linked-services] 如需詳細資訊 JSON 中定義的連結的服務。加密功能不尚未支援新增 AzureDataFactoryEncryptValue 指令程式。 

## 資料處理站 12/11/2014年版本資訊 ##

### 新的改進功能

- Azure Machine Learning 整合
	- 這一版的 Azure 資料處理站服務可讓您藉由整合 Azure 的資料處理站與 Azure 機器學習 (ML) **AzureMLLinkedService** 和 **AzureMLBatchScoringActivity**。請參閱 [建立預測的管線，使用資料處理站和 Azure 機器學習][adf-azure-ml] 如需詳細資訊。 
- 提供閘道版本狀態
	- 如果有比目前安裝之版本還要新的閘道，「Azure Preview 入口網站」和 Get-AzureDataFactoryGateway Cmdlet 的輸出中就會顯示 "NewVersionAvailable" 狀態。。您可以接著依照入口網站程序下載新的安裝檔案 (.msi)，然後執行它來安裝最新的閘道。不需要進行其他設定。

### 變更

- 已移除 HdInsightOnDemandLinkedService 中的 JobsContainer。
	- 在針對 HDInsightOnDemandLinkedService JSON 定義中，您不需要指定 **jobsContainer** 屬性淪陷了。如果您有為隨選連結服務指定的屬性，則會忽略此屬性。您可以從連結服務的 JSON 定義中移除屬性，然後使用 New-AzureDataFactoryLinkedService Cmdlet 來更新連結服務的定義。
- HDInsightOnDemandLinkedService 的選擇性組態參數
	- 此版本針對 HDInsightOnDemandLinked (隨選 HDInsight 叢集) 導入一些選擇性組態參數支援。請參閱 [ClusterCreateParameters 屬性][on-demand-hdi-parameters] 如需詳細資訊。
- 已移除閘道位置
	- 透過入口網站或 PowerShell (New-AzureDataFactoryGateway) 建立 Azure Data Factory 閘道時，您不再需要指定閘道的位置。將會繼承 Data Factory 地區。同樣地，若要使用 JSON 來設定 SQL Server 連結服務，也不再需要 "gatewayLocation" 屬性。Data Factory .NET SDK 也已更新來反映這些變更。
	- 如果您使用較舊版的 SDK 和 Azure PowerShell，則仍然必須提供位置設定。
 
     

#### 重大變更
	
- CustomActivity 變更為 DotNetActivity
	- **ICustomActivity** 介面已重新命名為 **IDotNetActivity**。您將必須更新 Data Factory NuGet 封裝，將您自訂活動之原始程式碼中的 ICustomActivity 變更為 IDotNetActivity。  
	- 在您自訂活動的 JSON 定義中的自訂活動的型別必須變更從 **CustomActivity** 至 **DotNetActivity**。 
	-  **CustomActivity** 和 **CustomActivityProperties** 類別已重新命名為 **DotNetActivity** 和 **DotNetActivityProperties** 與一組相同的屬性。

		如果您使用舊版 SDK 和 Azure PowerShell，則可以繼續使用 CustomActivity，而不需改成 DotNetActivity。
    
  		請參閱 [Azure 資料 Factory 管線中使用自訂活動][adf-custom-activities] 如需有關如何建立自訂的活動，並使用 Azure 資料 Factory 管線中的逐步解說。

[adf-azure-ml]: data-factory-create-predictive-pipelines.md
[adf-custom-activities]: data-factory-use-custom-activities.md
[adf-copy-activity]: data-factory-copy-activity.md
[adf-editor]: data-factory-editor.md
[adf-copy-advanced]: data-factory-copy-activity-advanced.md

[adf-editor-video]: http://channel9.msdn.com/Blogs/Windows-Azure/New-Azure-Data-Factory-Editor-UI
[adf-editor-blog]: http://azure.microsoft.com/blog/2015/03/02/azure-data-factory-editor-a-light-weight-web-editor/
[on-demand-hdi-parameters]: http://msdn.microsoft.com/library/microsoft.windowsazure.management.hdinsight.clustercreateparameters_properties.aspx
[adf-gateway-download]: http://www.microsoft.com/download/details.aspx?id=39717
[adf-github-samples]: https://github.com/Azure/Azure-DataFactory/tree/master/Samples/JSON
[adf-msdn-linked-services]: https://msdn.microsoft.com/library/dn834986.aspx
[adf-encrypt-value-cmdlet]: https://msdn.microsoft.com/library/dn834940.aspx

<!---HONumber=GIT-SubDir-->