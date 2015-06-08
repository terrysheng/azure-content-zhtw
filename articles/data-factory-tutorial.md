<properties 
	pageTitle="使用 Azure Data Factory 移動和處理記錄檔" 
	description="這個進階的教學課程說明幾近真實世界的實例，並實作使用 Azure 資料處理站服務和資料處理站編輯器的案例。" 
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
	ms.date="05/18/2015" 
	ms.author="spelluru"/>

# 教學課程： 衡量的行銷活動的效果  
Contoso 是建立多個平台遊戲的遊戲公司： 遊戲主控台、 手持裝置與個人電腦 (Pc)。這些遊戲會產生大量的記錄檔，Contoso 的目標是要收集並分析這些記錄檔，以深入瞭解客戶的喜好設定、 人口統計資料、 使用方式行為等向上銷售與交叉銷售機會、 開發新的強大功能，可以推動業務成長和識別為客戶提供更好的經驗。

在本教學課程中，您將建立資料處理站管線，以評估 Contoso 最近收集範例記錄、 處理和豐富它們與參考資料和轉換資料所發動的行銷活動的有效性。它有下列三個管線：

1.	 **PartitionGameLogsPipeline** 從 blob 儲存體讀取未經處理的遊戲事件，並建立依據年、 月和日的資料分割。
2.	 **EnrichGameLogsPipeline** 聯結與地理區域的程式碼參考資料的資料分割遊戲事件，並透過將 IP 位址對應到相對應的地理位置來充實資料。
3.	 **AnalyzeMarketingCampaignPipeline** 管線運用豐富的資料和處理與廣告資料來建立包含行銷活動成效的最終輸出。

## 準備開始教學課程
1.	讀取 [簡介 Azure 資料 Factory][adfintroduction] 取得 Azure 資料 Factory 的概觀，以及最上層概念的了解。
2.	您必須擁有 Azure 訂閱，才能執行本教學課程。如需取得訂用帳戶的詳細資訊，請參閱[購買選項](http://azure.microsoft.com/pricing/purchase-options/)、[成員優惠](http://azure.microsoft.com/pricing/member-offers/)或[免費試用](http://azure.microsoft.com/pricing/free-trial/)。
3.	您必須下載並安裝 [Azure PowerShell][download-azure-powershell] 在電腦上。您將會執行資料處理站 cmdlet 來上傳範例資料和 pig/hive 指令碼至 blob 儲存體。 
2.	**(建議選項)** 檢閱和練習的教學課程 [開始使用 Azure 資料 Factory][adfgetstarted] 簡單的教學課程中，若要熟悉的入口網站和 cmdlet 的發行項。
3.	**(建議選項)** 檢閱和練習中的逐步解說 [使用 Pig 和 Hive 與 Azure 資料處理站][usepigandhive] 建立管線來將資料從內部部署資料來源移至 Azure blob 存放區的逐步解說的發行項。
4.	下載 [ADFWalkthrough][adfwalkthrough-download] 檔案 **C:\ADFWalkthrough** 資料夾 **保留的資料夾結構**：
	- **管線：** 它包含 JSON 檔案，其中包含管線的定義。
	- **資料表：** 它包含 JSON 檔案，其中包含資料表的定義。
	- **LinkedServices：** 它包含 JSON 檔案，其中包含定義您的儲存體和計算 (HDInsight) 的叢集 
	- **指令碼：** 它包含用於處理資料並從管線叫用 Hive 和 Pig 的指令碼
	- **SampleData：** 它包含在這個逐步解說的範例資料
	- **與內部部署：** 它包含 JSON 檔案和指令碼，用於示範存取內部資料
	- **uploadSampleDataAndScripts.ps1：** 這個指令碼將上傳範例資料 (& s) 指令碼至 Azure。
5. 請確定您已建立下列 Azure 資源：			
	- Azure 儲存體帳戶。
	- Azure SQL Database
	- Azure HDInsight 叢集 3.1 版或以上 (或使用隨選資料處理站服務將會自動建立的 HDInsight 叢集)	
7. Azure 資源建立之後，請確定您有連接到每個資源所需的資訊。
 	- **Azure 儲存體帳戶** -帳戶名稱和帳戶金鑰。  
	- **Azure SQL Database** -伺服器、 資料庫、 使用者名稱和密碼。
	- **Azure HDInsight 叢集**。-HDInsight 叢集的名稱、 使用者名稱、 密碼和帳戶名稱以及與此叢集相關聯的 Azure 儲存體帳戶金鑰。如果您想要使用隨選 HDInsight 叢集，而不是您自己的 HDInsight 叢集可以略過此步驟。  
8. 啟動 **Azure PowerShell** 並執行下列命令。保持開啟 Azure PowerShell。如果您關閉並重新開啟，您需要再次執行這些命令。
	- 執行 **Add-azureaccount** 並輸入使用者名稱和您用來登入 Azure 預覽入口網站的密碼。  
	- 執行 **Get-azuresubscription** 若要檢視此帳戶的所有訂閱。
	- 執行 **Select-azuresubscription** 來選取您想要使用的訂閱。此訂用帳戶應該與您在「Azure Preview 入口網站」中使用的相同。	

## 概觀
端對端工作流程的說明如下：

![教學課程的端對端流程][image-data-factory-tutorial-end-to-end-flow]

1.  **PartitionGameLogsPipeline** 讀取未經處理的遊戲事件從 blob 儲存體 (RawGameEventsTable)，並建立年、 月和日 (PartitionedGameEventsTable) 為基礎的資料分割。
2.  **EnrichGameLogsPipeline** 地理區域的程式碼 (RefGetoCodeDictionaryTable) 與聯結資料分割的遊戲事件 (PartitionedGameEvents 資料表，也就是輸出 PartitionGameLogsPipeline) 並對應至相對應的地理位置 (EnrichedGameEventsTable) 的 IP 位址，藉以豐富資料。
3.  **AnalyzeMarketingCampaignPipeline** 管線運用豐富的資料 (EnrichedGameEventTable EnrichGameLogsPipeline 所產生)，而且會處理與廣告資料 (RefMarketingCampaignnTable) 建立行銷活動成效的最終輸出複製到 Azure SQL database (MarketingCampainEffectivensessSQLTable) 和分析 Azure blob 儲存體 (MarketingCampaignEffectivenessBlobTable)。
    
## 逐步解說： 建立、 部署和監視工作流程
1. [步驟 1： 上傳範例資料和指令碼](#MainStep1)。在此步驟中，您將上傳所有範例資料 (包括所有記錄檔和參考資料) 和將由工作流程執行的 Hive/Pig 指令碼。您執行的指令碼也會建立 Azure SQL 資料庫 (名為 MarketingCampaigns)、資料表、使用者定義型別和預存程序。
2. [步驟 2： 建立 Azure 的資料處理站](#MainStep2)。在此步驟中，您將建立名為 LogProcessingFactory 的 Azure Data Factory。
3. [步驟 3： 建立連結的服務](#MainStep3)。在此步驟中，您將建立下列連結的服務： 
	
	- 	**StorageLinkedService**。Azure 儲存體位置的連結，其中包含原始遊戲事件、分割的遊戲事件、充實的遊戲事件、行銷活動有效性資訊、參考資料的地區代碼，以及 LogProcessingFactory 行銷活動資料的參考資料   
	- 	**AzureSqlLinkedService**。連結 Azure SQL 資料庫，其中包含行銷活動有效性資訊。 
	- 	**HDInsightStorageLinkedService**。連結與 HDInsightLinkedService 所參照 HDInsight 叢集相關聯的 Azure Blob 儲存體。 
	- 	**HDInsightLinkedService**。將Azure HDInsight 叢集與 LogProcessingFactory 連結。這個叢集用來對資料執行 pig/hive 處理。 
 		
4. [步驟 4： 建立資料表](#MainStep4)。在此步驟中，您將建立下列資料表：
	
	- **RawGameEventsTable**。此資料表指定 StorageLinkedService 所定義的 Azure Blob 儲存體中，原始遊戲事件資料的位置 (adfwalkthrough/logs/rawgameevents/)。 
	- **PartitionedGameEventsTable**。此資料表指定 StorageLinkedService 所定義的 Azure Blob 儲存體中分割的遊戲事件資料的位置 (adfwalkthrough/logs/partitionedgameevents/)。 
	- **RefGeoCodeDictionaryTable**。此資料表指定 StorageLinkedService 所定義的 Azure Blob 儲存體中，參考資料地區代碼的位置 (adfwalkthrough/refdata/refgeocodedictionary/)。
	- **RefMarketingCampaignTable**。此資料表指定 StorageLinkedService 所定義的 Azure Blob 儲存體中，參考行銷活動資料的位置 (adfwalkthrough/refdata/refmarketingcampaign/)。
	- **EnrichedGameEventsTable**。此資料表指定 StorageLinkedService 所定義的 Azure Blob 儲存體中，充實遊戲事件資料的位置 (adfwalkthrough/logs/enrichedgameevents/)。
	- **MarketingCampaignEffectivenessSQLTable**。這份資料表，其中包含行銷促銷活動效率資料 AzureSqlLinkedService 所定義的 Azure SQL 資料庫中指定 SQL 資料表 (MarketingCampaignEffectiveness)。 
	- **MarketingCampaignEffectivenessBlobTable**。此資料表指定 StorageLinkedService 所定義的 Azure Blob 儲存體中，行銷活動有效性資料的位置 (adfwalkthrough/marketingcampaigneffectiveness/)。 

	
5. [步驟 5： 建立和排程管線](#MainStep5)。在此步驟中，您將建立下列管線：
	- **PartitionGameLogsPipeline**。管線會從 Blob 儲存體 (RawGameEventsTable) 讀取原始遊戲事件，並建立以年、月和日為基礎的資料分割 (PartitionedGameEventsTable)。 


		![PartitionGamesLogs 管線][image-data-factory-tutorial-partition-game-logs-pipeline]


	- **EnrichGameLogsPipeline**。此管線會聯結分割的遊戲事件 (PartitionedGameEvents 資料表，其為 PartitionGameLogsPipeline 的輸出) 與地區代碼 (RefGetoCodeDictionaryTable) 並藉由將 IP 位址對應到相對應的地理位置 (EnrichedGameEventsTable) 的來充實資料。

		![EnrichedGameLogsPipeline][image-data-factory-tutorial-enrich-game-logs-pipeline]

	- **AnalyzeMarketingCampaignPipeline**。此管線運用充實的資料 (EnrichGameLogsPipeline 所產生的 EnrichedGameEventTable)，並將它與廣告資料 (RefMarketingCampaignnTable) 處理，以建立行銷活動有效性的最終輸出，該輸出會複製到 Azure SQL 資料庫 (MarketingCampainEffectivensessSQLTable) 和 Azure Blob 儲存體 (MarketingCampaignEffectivenessBlobTable) 供分析


		![MarketingCampaignPipeline][image-data-factory-tutorial-analyze-marketing-campaign-pipeline]


6. [步驟 6： 監視管線和資料配量](#MainStep6)。在此步驟中，您將使用 Azure 入口網站來監視管線、資料表和資料配量。

## <a name="MainStep1"></a> 步驟 1： 上傳範例資料和指令碼
在此步驟中，您將上傳所有範例資料 (包括所有記錄檔和參考資料) 和將由工作流程叫用的 Hive/Pig 指令碼。您也執行這些指令碼建立呼叫 Azure SQL database **MarketingCampaigns**, 、 資料表、 使用者定義型別和預存程序。

將行銷活動有效性結果從 Azure Blob 儲存體移至 Azure SQL 資料庫時，會使用資料表、使用者定義型別和預存程序。

1. 開啟 **uploadSampleDataAndScripts.ps1** 從 **C:\ADFWalkthrough** 資料夾 (或包含所擷取的檔案的資料夾) 在您最愛的編輯器中，您的叢集資訊取代反白顯示並儲存檔案。


		$storageAccount = <storage account name>
		$storageKey = <storage account key>
		$azuresqlServer = <sql azure server>.database.windows.net
		$azuresqlUser = <sql azure user>@<sql azure server>
		$azuresqlPassword = <sql azure password>

 
	> [AZURE.NOTE][下載][sqlcmd-install]   
2. 確認您的本機電腦可以存取 Azure SQL Database。若要啟用存取，請使用 **Azure 管理入口網站** 或 **sp_set_firewall_rule** 上 master 資料庫才能建立的防火牆規則，您的電腦的 IP 位址。可能需要五分鐘的時間，這項變更才會生效。請參閱 [Azure sql 設定防火牆規則][azure-sql-firewall]。
4. 在 Azure PowerShell 中，瀏覽至已解壓縮範例所在的位置 (例如： **C:\ADFWalkthrough**)
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
		6/6/2014 11:54:36 AM 3. Created ‘MarketingCampaigns’ Azure SQL database and tables.
		6/6/2014 11:54:36 AM You are ready to deploy Linked Services, Tables and Pipelines. 

## <a name="MainStep2"></a> 步驟 2： 建立 Azure 的資料處理站
在此步驟中，您會建立名為 Azure 資料 factory **LogProcessingFactory**。

1.	登入之後 [Azure 預覽入口網站][azure-preview-portal], ，按一下 **新增** 從左下角，按一下 **資料分析** 中 **建立** blade 中，然後按一下 [ **資料 Factory** 上 **資料分析** 分頁。 

	![新-> DataFactory][image-data-factory-new-datafactory-menu]

5. 在 **新的資料處理站** blade 中，輸入 **LogProcessingFactory** 的 **名稱**。

	![資料處理站分頁][image-data-factory-tutorial-new-datafactory-blade]

6. 如果您尚未建立名為 Azure 資源群組 **ADF** ，請執行下列動作：
	1. 按一下 **資源群組名稱**, ，然後按一下 **建立新的資源群組**。
	
		![資源群組分頁][image-data-factory-tutorial-resourcegroup-blade]
	2. 在 **建立資源群組** blade 中，輸入 **ADF** 資源群組，然後按一下 [名稱 **確定**。
	
		![建立資源群組][image-data-factory-tutorial-create-resourcegroup]
7. 選取 **ADF** 的 **資源群組名稱**。  
8.	在 **新的資料處理站** blade 中，請注意， **新增到儀表板** 預設為選取狀態。這會將連結加入開始面板上的 Data Factory (登入 Azure 預覽入口網站時會看見)。

	![建立資料處理站分頁][image-data-factory-tutorial-create-datafactory]

9.	在 **新的資料處理站** blade 中，按一下 **建立** 建立資料處理站。
10.	建立資料處理站之後，您應該會看到 **資料 FACTORY** 刀鋒標題為 **LogProcessingFactory**。

	![資料處理站首頁][image-data-factory-tutorial-datafactory-homepage]

	
	如果未看見，請執行下列其中一項動作：

	- 按一下 **LogProcessingFactory** 上 **儀表板** (首頁)
	- 按一下 **瀏覽** 的左側，按一下 **一切**, ，按一下 **資料處理站**, ，然後按一下 [資料處理站。
 
	> [AZURE.NOTE]Azure Data Factory 的名稱在全域必須是唯一的。如果您收到錯誤： **資料 factory 名稱"LogProcessingFactory"不是使用**, ，變更名稱 (例如，yournameLogProcessingFactory)。使用此名稱來取代 LogProcessingFactory 時執行本教學課程中的步驟。
 
## <a name="MainStep3"></a> 步驟 3： 建立連結的服務

> [AZURE.NOTE]這篇文章會使用 Azure 入口網站中，特別是資料 Factory 編輯器，建立連結的服務、 表格和管線。請參閱 [教學課程使用 Azure PowerShell][adftutorial-using-powershell] 如果您想要執行本教學課程中使用 Azure PowerShell。

在此步驟中，您將建立下列連結的服務：

- StorageLinkedService
- AzureSqlLinkedService
- HDInsightStorageLinkedService
- HDInsightLinkedService。 

### 建立 StorageLinkedService 和 HDInsightStorageLinkedService

1.	在 **資料 FACTORY** blade 中，按一下 **作者和部署** 磚來啟動 **編輯器** 資料處理站。

	![[製作和部署] 磚][image-author-deploy-tile]

	> [AZURE.NOTE]請參閱 [資料 Factory 編輯器][data-factory-editor] 資料 Factory 編輯器的詳細概觀主題。

2.  在 **編輯器**, ，按一下 **新的資料存放區** 按鈕工具列，然後選取 **Azure 儲存體** 從下拉式功能表。您應該會看到在右窗格中建立連結的 Azure 儲存體服務的 JSON 範本。
	
	![編輯器新增資料存放區] 按鈕][image-editor-newdatastore-button]

3. 取代 **accountname** 和 **accountkey** 帳戶名稱與 Azure 儲存體帳戶的帳戶金鑰值。

	![Blob 儲存體 JSON 編輯器][image-editor-blob-storage-json]
	
	> [AZURE.NOTE]請參閱 [JSON 指令碼參考](http://go.microsoft.com/fwlink/?LinkId=516971) 如需詳細資訊 JSON 內容。

4. 按一下 **部署** 部署 StorageLinkedService] 工具列上。確認您看到訊息 **連結服務建立成功** 標題列上。

	![部署編輯器 Blob 儲存體][image-editor-blob-storage-deploy]

5. 重複執行步驟來建立另一個 Azure 儲存體連結服務名稱為： **HDInsightStorageLinkedService** 與 HDInsight 叢集相關聯的儲存體。連結服務 JSON 指令碼中的值變更 **名稱** 屬性設為 **HDInsightStorageLinkedService**。

### 建立 AzureSqlLinkedService
1. 在 **資料 Factory 編輯器** , ，按一下 **新的資料存放區** 按鈕工具列，然後選取 **Azure SQL database** 從下拉式功能表。您應該會看到在右窗格中建立連結的 Azure SQL 服務的 JSON 範本。
2. 取代 **servername**, ，**username@servername**, ，和 **密碼** 與 Azure SQL server、 使用者帳戶和密碼的名稱。 3. 取代 **databasename** 與 **MarketingCampaigns**。這是您在步驟 1 中執行的指令碼所建立的 Azure SQL 資料庫。您應該確認此資料庫已確實建立由指令碼 (如果有錯誤)。 
3. 按一下 **部署** 來建立及部署 AzureSqlLinkedService 工具列上。

### 建立 HDInsightLinkedService
Azure Data Factory 服務支援建立隨選叢集，並使用它處理輸入來產生輸出資料。您也可以使用自己的叢集執行相同作業。當您使用隨選 HDInsight 叢集時，系統會為每個配量建立叢集。然而，當您使用自己的 HDInsight 叢集時，叢集就可以立即處理配量。因此，在使用隨選叢集時，可能無法像使用自己的叢集那麼快看到輸出資料。基於範例的目的，讓我們使用隨選叢集。

#### 若要使用隨選 HDInsight 叢集
1. 按一下 **新計算** 從命令列，然後選取 **隨選 HDInsight 叢集** 從功能表。
2. 依下列方式在 JSON 指令碼中： 
	1. 針對 **clusterSize** 屬性，指定 HDInsight 叢集的大小。
	2. 針對 **jobsContainer** 屬性，指定將儲存的叢集記錄檔的預設容器的名稱。基於本教學課程的目的指定 **adfjobscontainer**。
	3. 針對 **timeToLive** 屬性，指定多久之前將會刪除該客戶可以處於閒置狀態。 
	4. 針對 **版本** 屬性，指定您想要使用的 HDInsight 版本。如果您排除此屬性時，會使用最新版本。  
	5. 針對 **linkedServiceName**, ，指定 **HDInsightStorageLinkedService** 您必須建立在 Get 入門教學課程。 

			{
		    	"name": "HDInsightLinkedService",
				    "properties": {
		    	    "type": "HDInsightOnDemandLinkedService",
		    	    "clusterSize": "4",
		    	    "jobsContainer": "adfjobscontainer",
		    	    "timeToLive": "00:05:00",
		    	    "version": "3.1",
		    	    "linkedServiceName": "HDInsightStorageLinkedService"
		    	}
			}

		請注意， **類型** 連結服務會設定為 **HDInsightOnDemandLinkedService**。

2. 按一下 **部署** 命令列上的部署連結的服務。
   
   
#### 若要使用您自己的 HDInsight 叢集： 

1. 按一下 **新計算** 從命令列，然後選取 **HDInsight 叢集** 從功能表。
2. 依下列方式在 JSON 指令碼中： 
	1. 針對 **clusterUri** 屬性中，輸入您的 HDInsight 的 URL。例如： https://<clustername>.azurehdinsight.net/     
	2. 針對 **UserName** 屬性中，輸入能夠存取 HDInsight 叢集的使用者名稱。
	3. 針對 **密碼** 屬性中，輸入使用者的密碼。 
	4. 針對 **LinkedServiceName** 屬性中，輸入 **StorageLinkedService**。這是您必須在入門教學課程中建立連結的服務。 

	Nore， **類型** 連結服務會設定為 **HDInsightBYOCLinkedService** (BYOC 代表帶您自己的叢集)。

2. 按一下 **部署** 命令列上的部署連結的服務。


## <a name="MainStep4"></a> 步驟 4： 建立資料表
 
在此步驟中，您將建立下表的資料處理站：

- RawGameEventsTable
- PartitionedGameEventsTable
- RefGeoCodeDictionaryTable
- RefMarketingCampaignTable
- EnrichedGameEventsTable
- MarketingCampaignEffectivenessSQLTable
- MarketingCampaignEffectivenessBlobTable

	![教學課程的端對端流程][image-data-factory-tutorial-end-to-end-flow]
 
上圖顯示的管線在中間資料列，而資料表在頂端和底部資料列。

### 建立資料表
	
1. 在 **編輯器** 資料處理站中，按一下 **新的資料集** 工具列上按一下按鈕 **Azure Blob 儲存體** 從下拉式功能表。 
2. 在右窗格中的 JSON 取代 JSON 指令碼從 **RawGameEventsTable.json** 檔案從 **C:\ADFWalkthrough\Tables** 資料夾。
3. 按一下 **部署** 來建立及部署資料表工具列上。確認您看到 **資料表成功建立** 編輯器的標題列上的訊息。
4. 重複步驟 1-3 的下列檔案內容： 
	1. PartitionedGameEventsTable.json
	2. RefGeoCodeDictionaryTable.json
	3. RefMarketingCampaignTable.json
	4. EnrichedGameEventsTable.json
	5. MarketingCampaignEffectivenessBlobTable.json 
5. 重複步驟 1-3 的下列檔案內容。但選取 **Azure Sql** 按一下之後 **新的資料集**。
	1. MarketingCampaignEffectivenessSQLTable.json
	

## <a name="MainStep5"></a> 步驟 5： 建立和排程管線
在此步驟中，您將建立下列管線：

- PartitionGameLogsPipeline
- EnrichGameLogsPipeline
- AnalyzeMarketingCampaignPipeline

### 若要建立管線

1. 在 **Data Factory 編輯器**中，按一下工具列的 [**新增管線**] 按鈕。如果沒看到此按鈕，請按一下工具列 **...(省略符號)**。或者，您也可以在樹狀檢視中，以滑鼠右鍵按一下 [**管線**]，再按一下 [**新增管線**]。
2. 在右窗格中的 JSON 取代 JSON 指令碼從 **PartitionGameLogsPipeline.json** 檔案從 **C:\ADFWalkthrough\Pipelines** 資料夾。
3. 在 JSON 中，在**右方括弧 (']')** 尾端加上**逗點 (',')**，然後在右方括弧之後增加下列三行。 

        "start": "2014-05-01T00:00:00Z",
        "end": "2014-05-05T00:00:00Z",
        "isPaused": false

	[AZURE.NOTE]請注意，開始和結束時間設為 05/01/2014 和 05/05/2014，因為此逐步解說中的範例資料是從 05/01/2014 到 05/05/2014。
 
3. 按一下工具列的 [**部署**]，建立並部署管線。確認您在編輯器的標題列看到 [**已成功建立管線**] 訊息。
4. 重複步驟 1-3 的下列檔案內容： 
	1. EnrichGameLogsPipeline.json
	2. AnalyzeMarketingCampaignPipeline.json
4. 按下關閉資料 Factory 刀 **X** (右上角)，若要查看 [首頁] 頁面 (* * 資料 FACTORY **分頁) 資料處理站。 
### 圖表檢視

1. 在 **資料 FACTORY** 刀鋒伺服器 **LogProcessingFactory**, ，按一下 **圖表**。 

	![圖表連結][image-data-factory-tutorial-diagram-link]

2. 您可以重新排列所看見的圖表，這裡是重新排列的圖表，在頂端顯示直接輸入，在底端則是輸出。您可以看到的輸出 **PartitionGameLogsPipeline** 做為輸入至 EnrichGameLogsPipeline 和輸出的傳入 **EnrichGameLogsPipeline** 傳遞至 **AnalyzeMarketingCampaignPipeline**。按兩下某個標題，以查看分頁所代表之成品的詳細資訊。

	![圖表檢視][image-data-factory-tutorial-diagram-view]

3. 以滑鼠右鍵按一下 **AnalyzeMarketingCampaignPipeline**, ，然後按一下 **開放的管線**。您應該會看到活動的輸入和輸出資料集以及管線中的所有活動。
 
	![開啟管線](./media/data-factory-tutorial/AnalyzeMarketingCampaignPipeline-OpenPipeline.png)

	按一下 **資料 factory** 軌跡中若要回到所有管線的圖表檢視的左上角。


**恭喜！** 您已成功建立 Azure Data Factory、連結的服務、管線、資料表，並開始工作流程。


## <a name="MainStep6"></a> 步驟 6： 監視管線和資料配量 

1.	如果您沒有 **資料 FACTORY** 刀鋒伺服器 **LogProcessingFactory** 開啟時，您可以執行下列其中之一：
	1.	按一下 **LogProcessingFactory** 上 **儀表板**。在建立資料處理站時 **新增到儀表板** 自動選取選項。

		![監視儀表板][image-data-factory-monitoring-startboard]

	2. 按一下 **瀏覽** 集線器，然後按一下 [ **一切**。
	 	
		![監視中樞所有項目][image-data-factory-monitoring-hub-everything]

		在 **瀏覽** blade 中，選取 **資料 factory** ，然後選取 **LogProcessingFactory** 中 **資料 factory** 分頁。

		![監視瀏覽 Datafactories][image-data-factory-monitoring-browse-datafactories]
2. 您可以以數種方式監視您的 Data Factory。您可以從管線或資料集開始。讓我們從管線開始，並進一步向下鑽研。 
3.	按一下 **管線** 上 **資料 FACTORY** 分頁。 
4.	按一下 **PartitionGameLogsPipeline** 管線刀鋒伺服器中。 
5.	在 **管線** 刀鋒伺服器 **PartitionGameLogsPipeline**, ，您會看到，管線會消耗 **RawGameEventsTable** 資料集。按一下 **RawGameEventsTable**。

	![取用和生產資料的管線][image-data-factory-monitoring-pipeline-consumed-produced]

6. 在資料表刀鋒伺服器 **RawGameEventsTable**, ，您會看到所有的扇區。在下列螢幕擷取畫面，所有的扇區是 **準備** 狀態有任何問題配量。這表示資料已就緒可供處理。

	![RawGameEventsTable 資料表分頁][image-data-factory-monitoring-raw-game-events-table]

	同時 **最近更新配量** 和 **最近無法配量** 清單會依照 **上次更新時間**。配量的更新時間是在下列情況中變更。

	-  您扇區的狀態手動更新，例如，使用 **組 AzureDataFactorySliceStatus** (或) 依序按一下 **執行** 上 **配量** 配量的分頁。
	-  配量因為執行的狀態變更 (例如啟動測試回合、 結束，而且無法執行、 測試回合結束，而且已成功，等等)。
 
	按一下標題的清單或 **...(橢圓形)** 若要查看較大配量的清單。按一下 **篩選** 篩選配量] 工具列上。
	
	若要檢視的資料配量，而是依照配量的開始/結束時間，請按一下 **(藉由配量的時間) 的資料配量** 並排顯示。

	![依時間配量的資料配量][DataSlicesBySliceTime]
 
7. 現在，在 **管線** 刀鋒伺服器 **PartiionGameLogsPipeline**, ，按一下 **Produced**。
8. 您應該會看到這個管線產生的資料集的清單： 
9. 按一下 **PartitionedGameEvents** 資料表中 **產生資料集** 分頁。 
10.	確認已選取 **狀態** 所有配量會設定為 **準備**。 
11.	按一下其中一個會將配量 **準備** 查看 **資料配量** 該扇區的分頁。

	![RawGameEventsTable 資料配量分頁][image-data-factory-monitoring-raw-game-events-table-dataslice-blade]

	如果發生錯誤，您會看到 **失敗 **這裡的狀態。您也可能會看到狀態的其中一個這兩個配量 **準備**, ，或兩者皆擁有狀態 **PendingValidation**, ，端視配量處理的速度。

	如果配量不在 **準備** 狀態，您可以看到，未就緒並封鎖目前的配量，從執行中的上游配量 **上游未就緒的配量** 清單。
 
	請參閱 [Azure 資料工廠開發人員參考][developer-reference] 來了解所有可能的配量的狀態。

12.	在 **資料配量** blade 中，按一下 [從執行 **活動執行** 清單。您應該會看到該配量的「活動執行」分頁。您應該會看到下列 **活動執行詳細資料** 分頁。

	![活動執行詳細資料] 分頁][image-data-factory-monitoring-activity-run-details]

13.	按一下 **下載** 下載檔案。對 HDInsight 處理的錯誤進行疑難排解時，這個畫面會特別有用。
	 
	
當所有管線都完成執行時，您可以看看 **MarketingCampaignEffectivenessTable** 中 **MarketingCampaigns** Azure SQL 資料庫來檢視結果。

**恭喜！** 您現在可以監視和疑難排解工作流程。您已經學會如何使用 Azure Data Factory 來處理資料，並取得分析。

## 擴充本教學課程使用的內部資料
在記錄處理程序從這份文件中的逐步解說的案例的最後一個步驟中，行銷促銷活動效率輸出已複製到 Azure SQL database。您也可以在組織內，將此資料移動到內部部署 SQL Server 以進行分析。
 
若要從 Azure Blob 複製行銷促銷活動效能資料的內部部署 SQL Server、 您需要建立額外的內部連結服務、 資料表和管線中導入這篇文章中的逐步解說。

練習 [逐步解說： 使用內部部署資料來源][tutorial-onpremises] 以了解如何建立管線來行銷促銷活動的效率資料複製到一個在內部部署 SQL Server 資料庫。


[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456
[data-factory-editor]: data-factory-editor.md

[adfsamples]: data-factory-samples.md
[adfgetstarted]: data-factory-get-started.md
[adftutorial-using-powershell]: data-factory-tutorial-using-powershell.md
[adfintroduction]: data-factory-introduction.md
[useonpremisesdatasources]: data-factory-use-onpremises-datasources.md
[usepigandhive]: data-factory-pig-hive-activities.md
[tutorial-onpremises]: data-factory-tutorial-extend-onpremises.md
[download-azure-powershell]: powershell-install-configure.md

[azure-preview-portal]: http://portal.azure.com
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[sqlcmd-install]: http://www.microsoft.com/download/details.aspx?id=35580
[azure-sql-firewall]: http://msdn.microsoft.com/library/azure/jj553530.aspx


[adfwalkthrough-download]: http://go.microsoft.com/fwlink/?LinkId=517495
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908

[DataSlicesBySliceTime]: ./media/data-factory-tutorial/DataSlicesBySliceTime.png
[image-author-deploy-tile]: ./media/data-factory-tutorial/author-deploy-tile.png
[image-editor-newdatastore-button]: ./media/data-factory-tutorial/editor-newdatastore-button.png
[image-editor-blob-storage-json]: ./media/data-factory-tutorial/editor-blob-storage-json.png
[image-editor-blob-storage-deploy]: ./media/data-factory-tutorial/editor-blob-storage-deploy.png

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

<!---HONumber=GIT-SubDir-->