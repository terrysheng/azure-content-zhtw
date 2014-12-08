<properties title="Tutorial: Move and process log files using Azure Data Factory" pageTitle="使用 Azure Data Factory 移動和處理記錄檔" description="This advanced tutorial describes a near real-world scenario and implements the scenario using Azure Data Factory service." metaKeywords=""  services="data-factory" solutions=""  documentationCenter="" authors="spelluru" manager="jhubbard" editor="monicar" />

<tags ms.service="data-factory" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="spelluru" />

# 教學課程：使用 Data Factory 移動和處理記錄檔
本文章提供的端對端逐步解說，是有關使用 Azure Data Factory，將記錄檔的資料轉換成見解的記錄檔處理程序標準案例。 

## 案例
Contoso 是為多個平台建立遊戲的遊戲公司：平台包括遊戲主控台、手持裝置和個人電腦 (PC)。每個遊戲都會產生大量記錄檔。Contoso 的目標是要收集和分析這些遊戲所產生的記錄檔以取得使用量資訊、識別向上銷售與交叉銷售機會、開發新的強大功能等，以改善業務並為客戶提供更好的經驗。
 
在本逐步解說中，我們會收集範例記錄檔、處理並以參考資料加以充實，然後轉換資料以評估 Contoso 最近推出的行銷活動有效性。

## 準備開始教學課程
1.	閱讀 [Azure Data Factory 的概觀][adfintroduction]，以取得 Azure Data Factory 的簡介並了解最頂層概念。
2.	您必須擁有 Azure 訂閱，才能執行本教學課程。如需取得訂閱的相關資訊，請參閱[購買選項][azure-purchase-options]、[成員優惠][azure-member-offers]或[免費試用。][azure-free-trial]。
3.	您必須在電腦上下載並安裝 [Azure PowerShell][download-azure-powershell]。 
2.	**(建議)** 檢閱並練習[開始使用 Azure Data Factory][adfgetstarted] 文章中的教學課程，以取得用於熟悉入口網站和 Cmdlet 的簡易教學課程。
3.	**(建議)** 檢閱並練習[對 Azure Data Factory 使用 Pig 和 Hive][usepigandhive] 文章中的逐步解說，以取得有關建立管線以將資料從內部部署資料來源移至 Azure Blob 存放區的逐步解說。
4.	將 [ADFWalkthrough][adfwalkthrough-download] 檔案下載至 **C:\ADFWalkthrough** 資料夾，**並保留資料夾結構**：
	- **Pipelines：**它包含   JSON 檔案，其中包含管線的定義。
	- **Tables：**它包含   JSON 檔案，其中包含資料表的定義。
	- **LinkedServices：** 它包含 JSON 檔案，其中包含您的儲存體和計算 (HDInsight) 叢集的定義 
	- **Scripts：** 它包含Hive 和 Pig 的指令碼，用於處理資料並從管線叫用
	- **SampleData：** 它包含此逐步解說的範例資料
	- **OnPremises：** 它包含 JSON 檔案和指令碼，用於示範存取內部部署資料
	- **AzureEnvironmentSetup.ps1：** 這是用於設定 Azure 環境的 PowerShell 指令碼
	- **uploadSampleDataAndScripts.ps1：** 此指令碼會將範例資料和指令碼上傳至 Azure。
5. 請確定您已建立下列 Azure 資源：			
	- Azure 儲存體帳戶。
	- Azure SQL Database
	- Azure HDInsight 叢集 3.1 版或以上	
7. Azure 資源建立之後，請確定您有連接到每個資源所需的資訊。
 	- **Azure 儲存體帳戶** - 帳戶名稱和帳戶金鑰。  
	- **Azure SQL Database** - 伺服器、資料庫、使用者名稱和密碼。
	- **Azure HDInsight 叢集** - HDInsight 叢集的名稱、使用者名稱、密碼以及與此叢集相關聯的 Azure 儲存體的帳戶名稱和帳戶金鑰。  
 8. 啟動 **Azure PowerShell** 並導覽至 **C:\ADFWalkthrough** 然後執行準備指令碼 .**\AzureEnvironmentSetup.ps1**。
 
		執行指令碼之前，您應該有訂閱名稱和與訂閱相關聯的 Microsoft 帳戶。

		每次啟動   Azure PowerShell，您都需要使用上述指令碼來設定 Azure 環境。組態不會在工作階段之間保存，即使同時使用多個工作階段，也必須分別執行。

		或者，您可以使用 **Add-AzureAccount** 登入 Azure，並使用 **Select-AzureSubscription** Cmdlet 選取訂閱 (如果您有多個訂閱)。
	

## 概觀
端對端工作流程的說明如下：
	![Tutorial End to End Flow][image-data-factory-tutorial-end-to-end-flow]

1. **PartitionGameLogsPipeline** 會從 Blob 儲存體 (RawGameEventsTable) 讀取原始遊戲事件，並建立以年、月和日為基礎的資料分割 (PartitionedGameEventsTable)。
2. **EnrichGameLogsPipeline** 會聯結分割的遊戲事件 (PartitionedGameEvents 資料表，其為 PartitionGameLogsPipeline 的輸出) 與地區代碼 (RefGetoCodeDictionaryTable) 並藉由將 IP 位址對應到相對應的地理位置 (EnrichedGameEventsTable) 的來充實資料。
3. **AnalyzeMarketingCampaignPipeline** 管線運用充實的資料 (EnrichGameLogsPipeline 所產生的 EnrichedGameEventTable)，並將它與廣告資料 (RefMarketingCampaignnTable) 處理，以建立行銷活動有效性的最終輸出，該輸出會複製到 Azure SQL 資料庫 (MarketingCampainEffectivensessSQLTable) 和 Azure Blob 儲存體 (MarketingCampaignEffectivenessBlobTable) 供分析。
    
## 逐步介紹：建立、部署和監視工作流程
1. [步驟 1：上傳範例資料和指令碼](#MainStep1)。在此步驟中，您將上傳所有範例資料 (包括所有記錄檔和參考資料) 和將由工作流程執行的 Hive/Pig 指令碼。您執行的指令碼也會建立 Azure SQL 資料庫 (名為 MarketingCampaigns)、資料表、使用者定義型別和預存程序。
2. [步驟 2：建立 Azure Data Factory](#MainStep2)。在此步驟中，您將建立名為 LogProcessingFactory 的 Azure Data Factory。
3. [步驟 3：建立連結的服務](#MainStep3)。在此步驟中，您將建立下列連結的服務： 
	
	- 	**StorageLinkedService**。Azure 儲存體位置的連結，其中包含原始遊戲事件、分割的遊戲事件、充實的遊戲事件、行銷活動有效性資訊、參考資料的地區代碼，以及 LogProcessingFactory 行銷活動資料的參考資料   
	- 	**AzureSqlLinkedService**。連結 Azure SQL 資料庫，其中包含行銷活動有效性資訊。 
	- 	**HDInsightStorageLinkedService**。連結與 HDInsightLinkedService 所參照 HDInsight 叢集相關聯的 Azure Blob 儲存體。 
	- 	**HDInsightLinkedService**。將Azure HDInsight 叢集與 LogProcessingFactory 連結。這個叢集用來對資料執行 pig/hive 處理。 
 		
4. [步驟 4：建立資料表](#MainStep4)。在此步驟中，您將建立下列資料表：  	
	
	- **RawGameEventsTable**。此資料表指定 StorageLinkedService 所定義的 Azure Blob 儲存體中，原始遊戲事件資料的位置 (adfwalkthrough/logs/rawgameevents/)。 
	- **PartitionedGameEventsTable**。此資料表指定 StorageLinkedService 所定義的 Azure Blob 儲存體中分割的遊戲事件資料的位置 (adfwalkthrough/logs/partitionedgameevents/)。 
	- **RefGeoCodeDictionaryTable**。此資料表指定 StorageLinkedService 所定義的 Azure Blob 儲存體中，參考資料地區代碼的位置 (adfwalkthrough/refdata/refgeocodedictionary/)。
	- **RefMarketingCampaignTable**。此資料表指定 StorageLinkedService 所定義的 Azure Blob 儲存體中，參考行銷活動資料的位置 (adfwalkthrough/refdata/refmarketingcampaign/)。
	- **EnrichedGameEventsTable**。此資料表指定 StorageLinkedService 所定義的 Azure Blob 儲存體中，充實遊戲事件資料的位置 (adfwalkthrough/logs/enrichedgameevents/)。
	- **MarketingCampaignEffectivenessSQLTable**。此資料表指定 Azure SQL Database 中的 SQL 資料表 (MarketingCampaignEffectiveness)，其由 AzureSqlLinkedService 所定義，包含行銷活動有效性資料。 
	- **MarketingCampaignEffectivenessBlobTable**。此資料表指定 StorageLinkedService 所定義的 Azure Blob 儲存體中，行銷活動有效性資料的位置 (adfwalkthrough/marketingcampaigneffectiveness/)。 

	
5. [步驟 5：建立和排程管線](#MainStep5)。在此步驟中，您將建立下列管線：
	- **PartitionGameLogsPipeline**。管線會從 Blob 儲存體 (RawGameEventsTable) 讀取原始遊戲事件，並建立以年、月和日為基礎的資料分割 (PartitionedGameEventsTable)。 


		![PartitionGamesLogs pipeline][image-data-factory-tutorial-partition-game-logs-pipeline]


	- **EnrichGameLogsPipeline**。此管線會聯結分割的遊戲事件 (PartitionedGameEvents 資料表，其為 PartitionGameLogsPipeline 的輸出) 與地區代碼 (RefGetoCodeDictionaryTable) 並藉由將 IP 位址對應到相對應的地理位置 (EnrichedGameEventsTable) 的來充實資料。 

		![EnrichedGameLogsPipeline][image-data-factory-tutorial-enrich-game-logs-pipeline]

	- **AnalyzeMarketingCampaignPipeline**。此管線運用充實的資料 (EnrichGameLogsPipeline 所產生的 EnrichedGameEventTable)，並將它與廣告資料 (RefMarketingCampaignnTable) 處理，以建立行銷活動有效性的最終輸出，該輸出會複製到 Azure SQL 資料庫 (MarketingCampainEffectivensessSQLTable) 和 Azure Blob 儲存體 (MarketingCampaignEffectivenessBlobTable) 供分析


		![MarketingCampaignPipeline][image-data-factory-tutorial-analyze-marketing-campaign-pipeline]


6. [步驟 6：監視管線和資料配量](#MainStep6)。在此步驟中，您將使用 Azure 入口網站來監視管線、資料表和資料配量。

### <a name="MainStep1"></a>步驟 1：上傳範例資料和指令碼
在此步驟中，您將上傳所有範例資料 (包括所有記錄檔和參考資料) 和將由工作流程叫用的 Hive/Pig 指令碼。您執行的指令碼也會建立 Azure SQL 資料庫 (名為 **MarketingCampaigns**)、資料表、使用者定義型別和預存程序。 

將行銷活動有效性結果從 Azure Blob 儲存體移至 Azure SQL 資料庫時，會使用資料表、使用者定義型別和預存程序。

1. 在您喜好的編輯器中開啟 **uploadSampleDataAndScripts.ps1** (自 **C:\ADFWalkthrough** 資料夾，或包含解壓縮檔案的資料夾)，將反白顯示部分以您叢集的資訊取代，並儲存檔案。


		$storageAccount = <storage account name>
		$storageKey = <storage account key>
		$azuresqlServer = <sql azure server>.database.windows.net
		$azuresqlUser = <sql azure user>@<sql azure server>
		$azuresqlPassword = <sql azure password>

 
	> [WACOM.NOTE] 此指令碼要求您在電腦上安裝 sqlcmd 公用程式。如果您已安裝 SQL Server，則已具備。否則，請[下載][sqlcmd-install]並安裝此公用程式。 
	> 或者，您可以使用此資料夾中的檔案：C:\ADFWalkthrough\Scripts 以上傳 pig/hive 指令碼和範例檔案至 Blob 儲存體中的 adfwalkthrough 容器，並在 MarketingCamapaigns Azure SQL 資料庫中建立 MarketingCampaignEffectiveness 資料表。   
2. 確認您的本機電腦可以存取 Azure SQL Database。若要啟用存取，使用 **Azure 管理入口網站**或主資料庫上的 **sp_set_firewall_rule**，為您的電腦的 IP 位址建立防火牆規則。可能需要五分鐘的時間，這項變更才會生效。請參閱 [設定 Azure SQL 的防火牆規則][azure-sql-firewall]。
3. 啟動 **Azure PowerShell**。 
4. 導覽至解壓縮範例所在的位置 (例如：**C:\ADFWalkthrough**)
5. 執行 **uploadSampleDataAndScripts.ps1** 
6. 一旦指令碼執行成功，您會看到下列項目：

		$storageAccount = <storage account name>
		PS C:\ ADFWalkthrough> & '.\uploadSampleDataAndScripts.ps1'

		Name			PublicAccess		LastModified
		-----			--------		------
		ADFWalkthrough		off			6/6/2014 6:53:34 PM +00:00
	
		Uploading sample data and script files to the storage container [adfwalkthrough]

		Container Uri: https://<yourblobstorage>.blob.core.windows.net/adfwalkthrough

		Name                        BlobType   Length   ContentType               LastModified                        
		----                        --------   ------   -----------               ------------                        
		logs/rawgameevents/raw1.csv  BlockBlob  12308   application/octet-stream  6/6/2014 6:54:35 PM 
		logs/rawgameevents/raw2.csv  BlockBlob  16119   application/octet-stream  6/6/2014 6:54:35 PM 
		logs/rawgameevents/raw3.csv  BlockBlob  16062   application/octet-stream  6/6/2014 6:54:35 PM 
		logs/rawgameevents/raw4.csv  BlockBlob  16245   application/octet-stream  6/6/2014 6:54:35 PM 
		refdata/refgeocodedictiona.. BlockBlob  18647   application/octet-stream  6/6/2014 6:54:36 PM 
		refdata/refmarketingcampai.. BlockBlob  8808    application/octet-stream  6/6/2014 6:54:36 PM
		scripts/partitionlogs.hql    BlockBlob  2449    application/octet-stream  6/6/2014 6:54:36 PM 
		scripts/enrichlogs.pig       BlockBlob  1631    application/octet-stream  6/6/2014 6:54:36 PM
		scripts/transformdata.hql    BlockBlob  1753    application/octet-stream  6/6/2014 6:54:36 PM

		6/6/2014 11:54:36 AM Summary
		6/6/2014 11:54:36 AM 1. Uploaded Sample Data Files to blob container.
		6/6/2014 11:54:36 AM 2. Uploaded Sample Script Files to blob container.
		6/6/2014 11:54:36 AM 3. Created 'MarketingCampaigns' Azure SQL database and tables.
		6/6/2014 11:54:36 AM You are ready to deploy Linked Services, Tables and Pipelines. 


### <a name="MainStep2"></a>步驟 2：建立 Azure Data Factory
在此步驟中，您會建立名為 **LogProcessingFactory** 的 Azure Data Factory。

1.	登入 [[Azure 預覽入口網站][azure-preview-portal]] 後，按一下位於左下角的 [**新增**]，然後在 [**新增**] 分頁上按一下 [**Data Factory**]。 

	![New->DataFactory][image-data-factory-new-datafactory-menu] 
	
	如果沒有看見 **Data Factory** 出現在 [**新增**] 分頁上，請向下捲動。 
	
5. 在 [**新增 Data Factory**] 分頁中，輸入 **LogProcessingFactory** 做為 [**名稱**]。

	![Data Factory Blade][image-data-factory-tutorial-new-datafactory-blade]

6. 如果您尚未建立名為 **ADF** 的 Azure 資源群組，請執行下列動作：
	1. 按一下 [**資源群組名稱**]，然後按一下 [**建立新的資源群組**]。
	
		![Resource Group Blade][image-data-factory-tutorial-resourcegroup-blade]
	2. 在 [**建立資源群組**] 分頁中，輸入 **ADF** 做為資源群組的名稱，然後按一下 [**確定**]。
	
		![Create Resource Group][image-data-factory-tutorial-create-resourcegroup]
7. 選取 **ADF** 做為 [**資源群組名稱**]。  
8.	在 [**新增 Data Factory**] 分頁中，請注意，預設會選取 [**新增至開始面板**]。這會將連結加入開始面板上的 Data Factory (登入 Azure 預覽入口網站時會看見)。

	![Create Data Factory Blade][image-data-factory-tutorial-create-datafactory]

9.	在 [**新增 Data Factory**] 分頁中，按一下 [**建立**] 以建立 Data Factory。
10.	建立 Data Factory之後，您應該會看到 [**DATA FACTORY**] 分頁的標題為 **LogProcessingFactory**。

	![Data Factory Homepage][image-data-factory-tutorial-datafactory-homepage]

	
	如果未看見，請執行下列其中一項動作：

	- 按一下 **LogProcessingFactory** (在 [**開始面板**] 上，首頁)
	- 在左側按一下 [**瀏覽**]，按一下 [**所有項目**]、[**Data Factory**]，再按一下 Data Factory。
 

 
### <a name="MainStep3"></a>步驟 3：建立連結的服務

在此步驟中，您將建立下列連結的服務：StorageLinkedService、AzureSqlLinkedService、HDInsightStorageLinkedService 和 HDInsightLinkedService。

1.	在 [**LogProcessingFactory**] 分頁中，按一下 [**連結的服務**] 磚。

	![Linked Services Tile][image-data-factory-tutorial-linkedservice-tile]

2. 在 [**連結的服務**] 分頁，從命令列按一下 [**+ 資料存放區**]。	

	![Linked Services - Add Store][image-data-factory-tutorial-linkedservices-add-datstore]

3. 在 [**新增資料存放區**] 分頁中，輸入 **StorageLinkedService** 做為 [**名稱**]，按一下 [**型別 (需要設定)**]，然後選取 [**Azure 儲存體帳戶**]。

	![Data Store Type - Azure Storage][image-data-factory-tutorial-datastoretype-azurestorage]

4. 在 [**新增資料存放區**] 分頁中，您會看到兩個新的欄位：**帳戶名稱**和**帳戶金鑰**。輸入您的 **Azure 儲存體帳戶**的帳戶名稱和帳戶金鑰。

	![Azure Storage Settings][image-data-factory-tutorial-azurestorage-settings]

	您可以從入口網站取得您的 Azure 儲存體帳戶的帳戶名稱和帳戶金鑰，如下所示：

	![Storage Key][image-data-factory-tutorial-storage-key]
  
5. 在 [新增資料存放區] 分頁上按一下 [**確定**] 之後，您應該會看到 **StorageLinkedService** 出現在 [**資料存放區**] 的清單中 ([**連結的服務**] 分頁上。檢查**通知**中心 (左側) 中是否有任何訊息。

	![Linked Services Blade with Storage][image-data-factory-tutorial-linkedservices-blade-storage]
   
6. 重複**步驟 2 到 5**，建立另一個連結的服務，名為：**HDInsightStorageLinkedService**。這是您的 HDInsight 叢集所使用的儲存體。
7. 確認您看到 **StorageLinkedService** 和 **HDInsightStorageLinkedService** 出現在 [連結的服務] 分頁的清單中。
8. 在 [**連結的服務**] 分頁，從命令列按一下 [**新增 (+) 資料存放區**]。
9. 輸入 **AzureSqlLinkedService** 做為名稱。
10. 按一下 [**類型 (需要設定)**]，選取 [**Azure SQL Database**]。
11. 現在，您應該會在 [**新增資料存放區**] 分頁上看到下列額外的欄位。輸入 Azure SQL Database **伺服器**的名稱、**資料庫**名稱、**使用者名稱**和**密碼**，然後按一下 [**確定**]。
	1. 輸入 **MarketingCampaigns** 做為 [**資料庫**]。這是您在步驟 1 中執行的指令碼所建立的 Azure SQL 資料庫。您應該確認此資料庫確實是由此指令碼建立 (以免發生錯誤)。
		
 		![Azure SQL Settings][image-data-factory-tutorial-azuresql-settings]

		若要從 Azure 管理入口網站取得這些值：按一下 [檢視 MarketingCampaigns 資料庫的 SQL Database 連接字串]

		![Azure SQL Database Connection String][image-data-factory-tutorial-azuresql-database-connection-string]

12. 確認您看到您所建立的所有三個資料存放區：**StorageLinkedService**、**HDInsightStorageLinkedService** 和 **AzureSqlLinkedService**。
13. 您必須建立另一個連結的服務，但這是計算服務，專用於 **Azure HDInsight 叢集**。入口網站尚不支援建立連結的運算服務。因此，您必須使用 Azure PowerShell 來建立此連結的服務。 
14. 切換至 **Azure PowerShell** (如果您已開啟或啟動 **Azure PowerShell** 的話)。
15. 切換至 **AzureResourceManager** 模式，因為 Azure Data Factory Cmdlet 在此模式中可供使用。

		Switch-AzureMode AzureResourceManager

16. 導覽至 **LinkedServices** 子資料夾 ( **C:\ADFWalkthrough** 中或從您解壓縮檔案所在位置的資料夾)。
17. 在您喜好的編輯器中開啟 **HDInsightLinkedService.json**，將反白顯示部分以您叢集的資訊取代，並儲存檔案。

        "clusterUri": "https://<hdinsightclustername>.azurehdinsight.net/",
        "userName": "<hdiusername>",
        "location": "<hdiregion>",
        "password": "<hdipassword>",

18. 使用下列命令，將 $df 變數設定為 Data Factory 的名稱。

		$df = "LogProcessingFactory"
19. 使用 Cmdlet **New-AzureDataFactoryLinkedService** 來建立連結的服務，如下所示。開始使用儲存體帳戶：

		New-AzureDataFactoryLinkedService -ResourceGroupName ADF -DataFactoryName $df -File .\HDInsightLinkedService.json
 
	如果您對 ResourceGroupName、DataFactoryName 或 LinkedService 名稱使用不同的名稱，請在上述 Cmdlet 加以參考。另外，只在找不到檔案時，才提供連結的服務 JSON 檔案的完整檔案路徑。
20. 您應該會在 [**連結的服務**] 分頁中看到所有四個連結的服務，如下所示。如果未開啟 [連結的服務] 分頁，則按一下 [**DATA FACTORY**] 頁面中的 [連結的服務]，以取得 **LogProcessingFactory**。可能需要幾秒鐘的時間，[連結的服務] 分頁才能重新整理。

	![Linked Services All][image-data-factory-tutorial-linkedservices-all]
 

### <a name="MainStep4"></a>步驟 4：建立資料表 
在此步驟中，您將建立下列資料表： 

- RawGameEventsTable
- PartitionedGameEventsTable
- RefGeoCodeDictionaryTable
- RefMarketingCampaignTable
- EnrichedGameEventsTable
- MarketingCampaignEffectivenessSQLTable
- MarketingCampaignEffectivenessBlobTable

	![Tutorial End-to-End Flow][image-data-factory-tutorial-end-to-end-flow]
 
上圖顯示的管線在中間資料列，而資料表在頂端和底部資料列。 

Azure 入口網站尚不支援建立資料集/資料表，因此在此版本中，您必須使用 Azure PowerShell 來建立資料表。

#### 建立資料表

1.	在 Azure PowerShell 中，從解壓縮範例的位置，瀏覽至 **Tables** 資料夾 (**C:\ADFWalkthrough\Tables\**)。 
2.	使用 Cmdlet **New-AzureDataFactoryTable** 來建立資料表，如下所示 (針對 **RawGameEventsTable**.json	)


		New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df -File .\RawGameEventsTable.json

	如果您對 ResourceGroupName 和 DataFactoryName 使用不同的名稱，請在上述 Cmdlet 加以參考。另外，只在 Cmdlet 找不到檔案時，才提供資料表 JSON 檔案的完整檔案路徑。

3. 重複上述步驟來建立下列資料表：	
		
	- **PartitionedGameEventsTable**

			
			New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df -File .\PartitionedGameEventsTable.json

	- **RefGeoCodeDictionaryTable**

			
			New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df -File .\RefGeoCodeDictionaryTable.json

	- **RefMarketingCampaignTable**

			
			New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df -File .\RefMarketingCampaignTable.json

	- **EnrichedGameEventsTable**

			
			New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df -File .\EnrichedGameEventsTable.json

	- **MarketingCampaignEffectivenessSQLTable**

			
			New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df -File .\MarketingCampaignEffectivenessSQLTable.json

	- **MarketingCampaignEffectivenessBlobTable**
			
			New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df -File .\MarketingCampaignEffectivenessBlobTable.json



4. 在 **Azure 預覽入口網站**中，按一下 [**資料集]** (位於 **DATA FACTORY** 分頁，針對 **LogProcessingFactory**)，並確認您看的到所有資料集 (資料表為矩形的資料集)。 

	![Data Sets All][image-data-factory-tutorial-datasets-all]

	您也可以從 Azure PowerShell 使用下列命令：
			
		Get-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df

	


### <a name="MainStep5"></a>步驟 5：建立和排程管線
在此步驟中，您將建立下列管線：PartitionGameLogsPipeline、EnrichGameLogsPipeline 和 AnalyzeMarketingCampaignPipeline。

1. 在 **Windows 檔案總管**中，導覽至 **Pipelines** 子資料夾 (位於 **C:\ADFWalkthrough** 資料夾中或從您解壓縮範例所在位置)。
2.	在您喜好的編輯器中開啟 **PartitionGameLogsPipeline.json**，將反白顯示部分以您資料儲存體帳戶的資訊取代，並儲存檔案。
			
		"RAWINPUT": "wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/logs/rawgameevents/",
		"PARTITIONEDOUTPUT": "wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/logs/partitionedgameevents/",

3. 重複步驟來建立下列管線：
	1. **EnrichGameLogsPipeline**.json (3 個相符項目)
	2. **AnalyzeMarketingCampaignPipeline**.json (3 個相符項目)

	**重要事項：** 確認您已用您的儲存體帳戶名稱取代所有 <storageaccountname>。 
 
4.  在 [**Azure PowerShell**] 中，導覽至 **Pipelines** 子資料夾 (位於 **C:\ADFWalkthrough** 資料夾中或從您解壓縮範例所在位置)。
5.  使用 Cmdlet **New-AzureDataFactoryPipeline** 來建立管線，如下所示 (針對 **PartitionGameLogspeline**.json	 )
			
		New-AzureDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName $df -File .\PartitionGameLogsPipeline.json

	如果您對 ResourceGroupName、DataFactoryName 或 Pipeline 名稱使用不同的名稱，請在上述 Cmdlet 加以參考。另外，提供管線 JSON 檔案的完整檔案路徑。
6. 重複上述步驟來建立下列管線：
	1. **EnrichGameLogsPipeline**
			
			New-AzureDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName $df -File .\EnrichGameLogsPipeline.json

	2. **AnalyzeMarketingCampaignPipeline**
				
			New-AzureDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName $df -File .\AnalyzeMarketingCampaignPipeline.json

7. 使用 Cmdlet **Get-AzureDataFactoryPipeline** 來取得管線的清單。
			
		Get-AzureDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName $df

8. 管線建立之後，您可以指定將發生資料處理的持續時間。藉由指定管線的作用期間，依據對每個 ADF 資料表所定義之可用性屬性，您會定義將處理資料配量的持續時間。

若要指定管線的作用期間，您可以使用 Cmdlet Set-AzureDataFactoryPipelineActivePeriod。本逐步解說中，範例資料是從 05/01 到 05/05。使用 2014-05-01 為 StartDateTime。EndDateTime 是選擇性的。
			
		Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADF -DataFactoryName $df -StartDateTime 2014-05-01 -EndDateTime 2014-05-05 -Name PartitionGameLogsPipeline
  
9. 確認以設定管線的作用期間。
			
			Confirm
			Are you sure you want to set pipeline 'PartitionGameLogsPipeline' active period from '05/01/2014 00:00:00' to '05/05/2014 00:00:00'?
			[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): n

10. 重複上述的兩個步驟來設定下列管線的作用期間。
	1. **EnrichGameLogsPipeline**
			
			Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADF -DataFactoryName $df -StartDateTime 2014-05-01 -EndDateTime 2014-05-05 -Name EnrichGameLogsPipeline

	2. **AnalyzeMarketingCampaignPipeline** 
			
			Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADF -DataFactoryName $df -StartDateTime 2014-05-01 -EndDateTime 2014-05-05 -Name AnalyzeMarketingCampaignPipeline

11. 在 **Azure 預覽入口網站**中，按一下 [**管線**] 磚 (不在管線的名稱上)，位於 [**DATA FACTORY**] 分頁 (**LogProcessingFactory** 中)，應該會看到您所建立的管線。

	![All Pipelines][image-data-factory-tutorial-pipelines-all]

12. 在 [**DATA FACTORY**] 分頁中，針對 **LogProcessingFactory**，按一下 [**圖表**]。

	![Diagram Link][image-data-factory-tutorial-diagram-link]

13. 您可以重新排列所看見的圖表，這裡是重新排列的圖表，在頂端顯示直接輸入，在底端則是輸出。您可以看到 **PartitionGameLogsPipeline** 的輸出，被傳回做為 EnrichGameLogsPipeline 的輸入，而 **EnrichGameLogsPipeline** 的輸出則傳遞至 **AnalyzeMarketingCampaignPipeline**。按兩下某個標題，以查看分頁所代表之成品的詳細資訊。

	![Diagram View][image-data-factory-tutorial-diagram-view]

	**恭喜！** 您已成功建立 Azure Data Factory、連結的服務、管線、資料表，並開始工作流程。 


### <a name="MainStep6"></a>步驟 6：監視管線和資料配量 

1.	如果您的 LogProcessingFactory 的 Data Factory 分頁未開啟，您可以執行下列其中一項：
	1.	按一下 **LogProcessingFactory** (在 [**開始面板**] 上)。在建立 Data Factory 時，會自動勾選 [**新增至開始面板**] 選項。

		![Monitoring Startboard][image-data-factory-monitoring-startboard]

	2. 按一下 [**瀏覽**] 中心，然後按一下 [**所有項目**]。
	 	
		![Monitoring Hub Everything][image-data-factory-monitoring-hub-everything]

	3. 在 [**瀏覽**] 分頁中，選取 [**Data Factory**]，並選取 **LogProcessingFactory** (在 **Data Factory** 分頁中)。。

	![Monitoring Browse Datafactories][image-data-factory-monitoring-browse-datafactories]
2. 您可以以數種方式監視您的 Data Factory。您可以從管線或資料集開始。讓我們從管線開始，並進一步向下鑽研。 
3.	按一下 [**管線**] (**DATA FACTORY** 分頁上)。 
4.	按一下管線分頁中的 **PartitionGameLogsPipeline**。 
5.	在 [**管線**] 分頁，針對 **PartitionGameLogsPipeline**，您會看見管線耗用 **RawGameEventsTable** 資料集。  按一下 [**RawGameEventsTable**]。

	![Pipeline Consumed and Produced][image-data-factory-monitoring-pipeline-consumed-produced]

6. 在 **RawGameEventsTable** 的資料表分頁中，您會看見所有配量。在下列螢幕擷取畫面中，所有的配量都處於**就緒**狀態，沒有任何有問題的配量。這表示資料已就緒可供處理。	

	![RawGameEventsTable TABLE blade][image-data-factory-monitoring-raw-game-events-table]
 
7. 現在，在 [**管線**] 分頁中，針對 **PartiionGameLogsPipeline**，按一下 [**已產生**]。 
8. 您應該會看到這個管線產生的資料集的清單： 
9. 按一下 **PartitionedGameEvents** 資料表 ([**產生的資料集**] 分頁上)。 
10.	確認所有配量的 [**狀態**] 都已設為 [**就緒**]。 
11.	按一下其中一個**就緒**的配量，以查看該配量的**資料配量**分頁。

	![RawGameEventsTable DATA SLICE blade][image-data-factory-monitoring-raw-game-events-table-dataslice-blade]

	如果有錯誤發生，您會在這裡看到 ** 失敗 ** 狀態。  您也可能會看到兩個配量的狀態為**就緒**，或兩個配量的狀態為 **PendingValidation**，根據處理配量的速度而定。
 
	請參閱 [Azure Data Factory 開發人員參考][developer-reference]，以了解所有可能的配量狀態。

12.	在 [**資料配量**] 分頁中，從 [**活動執行**] 清單中按一下執行。您應該會看到該配量的「活動執行」分頁。您應該會看到下列的**活動執行詳細資料**分頁。

	![Activity Run Details blade][image-data-factory-monitoring-activity-run-details]

13.	按一下 [**下載**] 以下載檔案。對 HDInsight 處理的錯誤進行疑難排解時，這個畫面會特別有用。 
	 
	
當所有管線都完成執行時，您可以查看 **MarketingCampaignEffectivenessTable** (位於 **MarketingCampaigns** Azure SQL 資料庫中)，以檢視結果。 

**恭喜！**您現在可以監視和疑難排解工作流程。您已經學會如何使用 Azure Data Factory 來處理資料，並取得分析。

	 

## 逐步介紹：使用內部部署資料

**(建議)** 檢閱和練習[讓您的管線處理內部部署資料][useonpremisesdatasources]文章中的逐步解說，以取得建立管線，以將資料從內部部署 SQL Server 移至 Azure Blob 存放區的逐步解說。


在此逐步解說中，您將學習如何設定環境以啟用管線，來處理您的內部部署資料。
 
在記錄檔處理案例中的最後一個步驟，從第一個逐步解說與分割 -> 充實 -> 分析工作流程，行銷活動有效性輸出已複製到 Azure SQL 資料庫。您也可以在組織內，將此資料移動到內部部署 SQL Server 以進行分析。
 
為了從 Azure Blob 將行銷活動有效性資料複製到內部部署 SQL Server，您需要使用與在第一個逐步解說中所使用的一組相同 Cmdlet 建立額外的內部部署連結的服務、資料表和管線。

在本教學課程中，您將執行下列步驟： 

1. [步驟 1：建立資料管理閘道](#OnPremStep1)。資料管理閘道  是可讓您從雲端存取組織中內部部署資料來源的用戶端代理程式。閘道可以讓您在內部部署 SQL Server 和 Azure 的資料存放區之間傳送資料。	

	您必須在公司環境中至少安裝一個閘道，以及將它向其註冊 Azure Data Factory 註冊，之後才將內部部署 SQL Server 資料庫新增為 Azure Data Factory 的連結的服務。

2. [步驟 2：為內部部署 SQL Server 建立連結的服務](#OnPremStep2)。在此步驟中，您可以先在內部部署 SQL Server 電腦上建立資料庫和資料表，然後再建立連結的服務：**OnPremSqlLinkedService**。  
3. [步驟 3：建立資料表和管線](#OnPremStep3)。在此步驟中，您會建立資料表 **MarketingCampaignEffectivenessOnPremSQLTable** 和管線 **EgressDataToOnPremPipeline**。 

4. [步驟 4：監視管線和檢視結果](#OnPremStep4)。在此步驟中，您將使用 Azure 入口網站來監視管線、資料表和資料配量。


### <a name="OnPremStep1"></a>步驟 1：建立資料管理閘道

資料管理閘道是可讓您從雲端存取組織中內部部署資料來源的用戶端代理程式。閘道可以讓您在內部部署 SQL Server 和 Azure 的資料存放區之間傳送資料。
  
您必須在公司環境中至少安裝一個閘道，以及將它向其註冊 Azure Data Factory 註冊，之後才將內部部署 SQL Server 資料庫新增為 Azure Data Factory 的連結的服務。

如果您有可以使用的現有資料閘道，請略過此步驟。

1.	建立邏輯資料閘道。在 **Azure 預覽入口網站**中，按一下 [**連結的服務** ](**DATA FACTORY**分頁上)。
2.	按一下命令列上的 [**新增 (+) 資料閘道**]。  
3.	在 [**新增資料閘道**] 分頁中，按一下 [**建立**]。
4.	在 [**建立**] 分頁中，輸入 **MyGateway** 做為資料閘道的 [**名稱**]。
5.	按一下 [**選擇區域**]，並視需要予以變更。 
6.	按一下 [**確定**] (在 [**建立**] 分頁中)。 
7.	您應該會看到 [**設定**] 分頁。 
8.	在 [**設定**] 分頁中，按一下 [**直接安裝在此電腦上**]。這會在您的電腦上下載、安裝及設定閘道，並向服務註冊閘道。如果在您想要連結至這個邏輯閘道入口網站的電腦上已安裝現有的閘道，使用此分頁中的金鑰，使用資料管理閘道組態管理員 (預覽) 工具重新註冊您的閘道。

	![Data Management Gateway Configuration Manager][image-data-factory-datamanagementgateway-configuration-manager]

9. 按一下 [**確定**] 以關閉 [**設定**] 分頁及 [**確定**] 以關閉 [**建立**] 分頁。等候 **MyGateway** ([**連結的服務**] 分頁中) 的狀態變更為 **良好**。您也可以啟動 **資料管理閘道組態管理員 (預覽)** 工具來確認閘道的名稱符合入口網站中的名稱，以及 [**狀態**] 為 [**已註冊**]。您可能必須關閉再重新開啟 [連結的服務] 分頁，以查看最新狀態。可能需要幾分鐘的時間，畫面才會重新整理為最新狀態。	

### <a name="OnPremStep2"></a>步驟 2：為內部部署 SQL Server 建立連結的服務

在此步驟中，您可以先在內部部署 SQL Server 電腦上建立所需的資料庫和資料表，然後再建立連結的服務。

#### 準備內部部署資料庫和資料表

若要開始，您需要建立 SQL Server 資料庫、資料表、使用者定義型別和預存程序。這些將用於從 Azure Blob 移動 **MarketingCampaignEffectiveness** 結果到 SQL Server 資料庫。

1.	在 **Windows 檔案總管**中，導覽至 **OnPremises** 子資料夾 (位於 **C:\ADFWalkthrough** 或從您解壓縮範例的所在位置)。
2.	在您喜好的編輯器中開啟 **prepareOnPremDatabase&Table.ps1**，將反白顯示部分以您 SQL Server 的資訊取代，並儲存檔案 (請提供 **SQL 驗證** 詳細資訊)。針對教學課程的目的，為您的資料庫啟用 SQL 驗證。 
			
		$dbServerName = "<servername>"
		$dbUserName = "<username>"
		$dbPassword = "<password>"

3. 在 **Azure PowerShell** 中，導覽至 **C:\ADFWalkthrough\OnPremises** 資料夾。
4.	執行 **prepareOnPremDatabase&Table.ps1** ** (使用雙引號或如下所示)**。
			
		& '.\prepareOnPremDatabase&Table.ps1'

5. Once the script executes successfully, you will see the following:	
			
		PS E:\ADF\ADFWalkthrough\OnPremises> & '.\prepareOnPremDatabase&Table.ps1'
		6/10/2014 10:12:33 PM Script to create sample on-premises SQL Server Database and Table
		6/10/2014 10:12:33 PM Creating the database [MarketingCampaigns], table and stored procedure on [.]...
		6/10/2014 10:12:33 PM Connecting as user [sa]
		6/10/2014 10:12:33 PM Summary:
		6/10/2014 10:12:33 PM 1. Database 'MarketingCampaigns' created.
		6/10/2014 10:12:33 PM 2. 'MarketingCampaignEffectiveness' table and stored procedure 


#### 建立連結的服務

1.	在 **Azure 預覽入口網站**中，按一下 [**連結的服務**] 磚 (**DATA FACTORY** 分頁上，針對 **LogProcessingFactory**。
2.	在 [**連結的服務**] 分頁，按一下 [**新增 (+) 資料存放區**]。
3.	在 [**新增資料存放區**] 分頁中，輸入 **OnPremSqlLinkedService** 做為 [**名稱**]。 
4.	按一下 [**類型 (需要設定)**]，並選取 **SQL Server**。您現在應該會看到**資料閘道**、**伺服器**、**資料庫** 和**認證**設定 (於**新資料儲存區** 分頁)。 
5.	按一下**資料閘道 (設定必要的設定)**，然後選取您先前建立的 **MyGateway**。 
6.	輸入主控 ******MarketingCampaigns** 資料庫的資料庫伺服器名稱。 
7.	輸入 **MarketingCampaigns** 做為資料庫。 
8.	按一下 [**認證**]。 
9.	在 [**認證**] 分頁上，按一下 [**按一下這裡安全地設定認證**。
10.	它會在第一次時安裝按一次應用程式，並啟動 **設定認證** 對話方塊。 
11.	在 [**設定認證**] 對話方塊中，輸入**使用者名稱**和**密碼**，然後按一下 [**確定**]。請等到對話方塊關閉。 
12.	按一下 [**確定**] (在 [**新增資料存放區**] 分頁中)。 
13.	在 [**連結的服務**] 分頁中，確認 **OnPremSqlLinkedService** 顯示在清單中，以及連結的服務的 [**狀態**]為 [**良好**]。

### <a name="OnPremStep3"></a>步驟 3：建立資料表和管線

#### 建立內部部署邏輯資料表

1.	在 **Azure PowerShell** 中，切換至 **C:\ADFWalkthrough\OnPremises** 資料夾。 
2.	使用 Cmdlet **New-AzureDataFactoryTable** 來建立資料表，如下所示 (針對 **MarketingCampaignEffectivenessOnPremSQLTable.json**。

			
		New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df -File .\MarketingCampaignEffectivenessOnPremSQLTable.json
	 
#### 建立將資料從 Azure Blob 複製到 SQL Server 的管線

1.	使用 Cmdlet **New-AzureDataFactoryPipeline** 來建立管線，如下所示 (針對 **EgressDataToOnPremPipeline.json**。

			
		New-AzureDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName $df -File .\EgressDataToOnPremPipeline.json
	 
2. 使用 Cmdlet **Set-AzureDataFactoryPipelineActivePeriod** 來指定 **EgressDataToOnPremPipeline** 的作用期間。

			
		Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADF -DataFactoryName $df -StartDateTime 2014-05-01 -EndDateTime 2014-05-05 -Name EgressDataToOnPremPipeline

	請按 **'Y'** 繼續進行。
	
### <a name="OnPremStep4"></a>步驟 4：監視管線和檢視結果

您現在可以使用與以下步驟中介紹的相同步驟[步驟 6：監視資料表和管線](#MainStep6)  來監視新的管線和新的內部部署 ADF 資料表的資料配量。
 
當您看到資料表 **MarketingCampaignEffectivenessOnPremSQLTable** 的配量狀態變成就緒時，這表示管線已完成配量的執行。若要檢視結果，請查詢 **MarketingCampaignEffectiveness** 資料表 (位於 SQL Server 的 **MarketingCampaigns** 資料庫中)。
 
恭喜！您已成功已經完成使用內部部署資料的逐步解說。 
 
## 另請參閱

文章 | 描述
------ | ---------------
[讓您的管線可能與內部部署資料搭配使用][useonpremisesdatasources] | 本文章的逐步解說示範如何將資料從內部部署 SQL Server 資料庫複製到 Azure Blob。
[對 Data Factory 使用 Pig 和 Hive][usepigandhive] | 本文章的逐步解說示範如何使用 HDInsight 活動來執行 hive/pig 指令碼，以處理輸入資料來產生輸出資料。 
[在 Data Factory 中使用自訂活動][use-custom-activities] | 本文章提供的逐步解說具有建立自訂活動並將它用於管線中的逐步指示。 
[使用 PowerShell 監視和管理 Azure Data Factory][monitor-manage-using-powershell] | 此文章描述如何使用 Azure PowerShell Cmdlet 監視 Azure Data Factory。您可以在 ADFTutorialDataFactory 上嘗試文章中的範例。
[Data Factory 的疑難排解][troubleshoot] | 本文說明如何對 Azure Data Factory 的問題進行疑難排解。您可以透過引用一個錯誤 (刪除 Azure SQL Database 中的資料表)，嘗試本文中針對 ADFTutorialDataFactory 所做的逐步解說。 
[Azure Data Factory 開發人員參考][developer-reference] | 開發人員參考具有 Cmdlet、JSON 指令碼、函式等等的完整的參考內容
[Azure Data Factory Cmdlet 參考][cmdlet-reference] | 此參考內容具有所有 Data Factory Cmdlet 的相關詳細資料。 

[monitor-manage-using-powershell]: ../data-factory-monitor-manage-using-powershell
[use-custom-activities]: ../data-factory-use-custom-activities
[troubleshoot]: ../data-factory-troubleshoot
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456


[adfgetstarted]: ../data-factory-get-started
[adfintroduction]: ../data-factory-introduction
[useonpremisesdatasources]: ../data-factory-use-onpremises-datasources
[usepigandhive]: ../data-factory-pig-hive-activities

[azure-preview-portal]: http://portal.azure.com
[azure-purchase-options]: http://azure.microsoft.com/zh-tw/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/zh-tw/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/zh-tw/pricing/free-trial/

[sqlcmd-install]: http://www.microsoft.com/zh-tw/download/details.aspx?id=35580
[azure-sql-firewall]: http://msdn.microsoft.com/zh-tw/library/azure/jj553530.aspx

[download-azure-powershell]: http://azure.microsoft.com/zh-tw/documentation/articles/install-configure-powershell
[adfwalkthrough-download]: http://go.microsoft.com/fwlink/?LinkId=517495
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908


[image-data-factory-tutorial-end-to-end-flow]: ./media/data-factory-tutorial/EndToEndWorkflow.png

[image-data-factory-tutorial-partition-game-logs-pipeline]: ./media/data-factory-tutorial/PartitionGameLogsPipeline.png

[image-data-factory-tutorial-enrich-game-logs-pipeline]: ./media/data-factory-tutorial/EnrichGameLogsPipeline.png

[image-data-factory-tutorial-analyze-marketing-campaign-pipeline]: ./media/data-factory-tutorial/AnalyzeMarketingCampaignPipeline.png


[image-data-factory-tutorial-egress-to-onprem-pipeline]: ./media/data-factory-tutorial/EgreeDataToOnPremPipeline.png

[image-data-factory-tutorial-set-firewall-rules-azure-db]: ./media/data-factory-tutorial/SetFirewallRuleForAzureDatabase.png

[image-data-factory-tutorial-portal-new-everything]: ./media/data-factory-tutorial/PortalNewEverything.png

[image-data-factory-tutorial-datastorage-cache-backup]: ./media/data-factory-tutorial/DataStorageCacheBackup.png

[image-data-factory-tutorial-dataservices-blade]: ./media/data-factory-tutorial/DataServicesBlade.png

[image-data-factory-tutorial-new-datafactory-blade]: ./media/data-factory-tutorial/NewDataFactoryBlade.png

[image-data-factory-tutorial-resourcegroup-blade]: ./media/data-factory-tutorial/ResourceGroupBlade.png

[image-data-factory-tutorial-create-resourcegroup]: ./media/data-factory-tutorial/CreateResourceGroup.png

[image-data-factory-tutorial-datafactory-homepage]: ./media/data-factory-tutorial/DataFactoryHomePage.png

[image-data-factory-tutorial-create-datafactory]: ./media/data-factory-tutorial/CreateDataFactory.png

[image-data-factory-tutorial-linkedservice-tile]: ./media/data-factory-tutorial/LinkedServiceTile.png

[image-data-factory-tutorial-linkedservices-add-datstore]: ./media/data-factory-tutorial/LinkedServicesAddDataStore.png

[image-data-factory-tutorial-datastoretype-azurestorage]: ./media/data-factory-tutorial/DataStoreTypeAzureStorageAccount.png

[image-data-factory-tutorial-azurestorage-settings]: ./media/data-factory-tutorial/AzureStorageSettings.png

[image-data-factory-tutorial-storage-key]: ./media/data-factory-tutorial/StorageKeyFromAzurePortal.png

[image-data-factory-tutorial-linkedservices-blade-storage]: ./media/data-factory-tutorial/LinkedServicesBladeWithAzureStorage.png

[image-data-factory-tutorial-azuresql-settings]: ./media/data-factory-tutorial/AzureSQLDatabaseSettings.png

[image-data-factory-tutorial-azuresql-database-connection-string]: ./media/data-factory-tutorial/DatabaseConnectionString.png

[image-data-factory-tutorial-linkedservices-all]: ./media/data-factory-tutorial/LinkedServicesAll.png

[image-data-factory-tutorial-datasets-all]: ./media/data-factory-tutorial/DataSetsAllTables.png

[image-data-factory-tutorial-pipelines-all]: ./media/data-factory-tutorial/AllPipelines.png

[image-data-factory-tutorial-diagram-link]: ./media/data-factory-tutorial/DataFactoryDiagramLink.png

[image-data-factory-tutorial-diagram-view]: ./media/data-factory-tutorial/DiagramView.png

[image-data-factory-monitoring-startboard]: ./media/data-factory-tutorial/MonitoringStartBoard.png

[image-data-factory-monitoring-hub-everything]: ./media/data-factory-tutorial/MonitoringHubEverything.png

[image-data-factory-monitoring-browse-datafactories]: ./media/data-factory-tutorial/MonitoringBrowseDataFactories.png

[image-data-factory-monitoring-pipeline-consumed-produced]: ./media/data-factory-tutorial/MonitoringPipelineConsumedProduced.png

[image-data-factory-monitoring-raw-game-events-table]: ./media/data-factory-tutorial/MonitoringRawGameEventsTable.png

[image-data-factory-monitoring-raw-game-events-table-dataslice-blade]: ./media/data-factory-tutorial/MonitoringPartitionGameEventsTableDataSliceBlade.png

[image-data-factory-monitoring-activity-run-details]: ./media/data-factory-tutorial/MonitoringActivityRunDetails.png

[image-data-factory-datamanagementgateway-configuration-manager]: ./media/data-factory-tutorial/DataManagementGatewayConfigurationManager.png

[image-data-factory-new-datafactory-menu]: ./media/data-factory-tutorial/NewDataFactoryMenu.png

[image-data-factory-new-datafactory-create-button]: ./media/data-factory-tutorial/DataFactoryCreateButton.png
