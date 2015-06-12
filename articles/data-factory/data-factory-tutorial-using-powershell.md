<properties 
	pageTitle="使用 Azure Data Factory 移動和處理記錄檔" 
	description="此進階教學課程說明接近現實情況的案例，並補充說明使用 Azure Data Factory 服務的案例。" 
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
	ms.date="04/14/2015" 
	ms.author="spelluru"/>

# 教學課程： 移動並處理記錄檔使用資料 Factory [PowerShell]
本文章提供的端對端逐步解說，是有關使用 Azure Data Factory，將記錄檔的資料轉換成見解的記錄檔處理程序標準案例。

## 案例
Contoso 是建立多個平台遊戲的遊戲公司： 遊戲主控台、 手持裝置與個人電腦 (Pc)。每個遊戲都會產生大量記錄檔。Contoso 的目標是要收集和分析這些遊戲所產生的記錄檔以取得使用量資訊、識別向上銷售與交叉銷售機會、開發新的強大功能等，以改善業務並為客戶提供更好的經驗。
 
在本逐步解說中，我們會收集範例記錄檔、處理並以參考資料加以充實，然後轉換資料以評估 Contoso 最近推出的行銷活動有效性。

## 準備開始教學課程
1.	讀取 [簡介 Azure 資料 Factory][adfintroduction] 取得 Azure 資料 Factory 的概觀，以及最上層概念的了解。
2.	您必須擁有 Azure 訂閱，才能執行本教學課程。如需取得訂用帳戶的詳細資訊，請參閱[購買選項][azure-purchase-options]、[成員優惠][azure-member-offers]或[免費試用][azure-free-trial]。
3.	您必須下載並安裝 [Azure PowerShell][download-azure-powershell] 在電腦上。 
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
端對端工作流程會如下所示： ![教學課程的端對端流程][image-data-factory-tutorial-end-to-end-flow]

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

1.	登入之後 [Azure 預覽入口網站][azure-preview-portal], ，按一下 **新增** 從左下角，然後按一下 **資料 Factory** 上 **新增** 分頁。 

	![新-> DataFactory][image-data-factory-new-datafactory-menu]
	
	如果看不到 **資料 Factory** 上 **新增** blade 中，向下捲動。
	
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

> [AZURE.NOTE]此文件會使用 Azure PowerShell 建立連結的服務、 表格和管線。請參閱 [教學課程使用資料 Factory 編輯器][adftutorial-using-editor] 如果您想要執行本教學課程中使用 Azure 入口網站，特別是資料 Factory 編輯器。

在此步驟中，您將建立下列連結的服務： StorageLinkedService、 AzureSqlLinkedService、 HDInsightStorageLinkedService 和 HDInsightLinkedService。


1.	在 **LogProcessingFactory** blade 中，按一下 **連結服務** 並排顯示。

	![連結的服務磚][image-data-factory-tutorial-linkedservice-tile]

2. 在 **連結服務** blade 中，按一下 **+ 資料存放區** 從命令列。

	![連結的服務-新增存放區][image-data-factory-tutorial-linkedservices-add-datstore]

3. 在 **新的資料存放區** blade 中，輸入 **StorageLinkedService** 的 **名稱**, ，按一下 **型別 (所需的設定)**, ，然後選取 **Azure 儲存體帳戶**。

	![資料存放區類型-Azure 儲存體][image-data-factory-tutorial-datastoretype-azurestorage]

4. 在 **新的資料存放區** blade 中，您會看到兩個新的欄位： **帳戶名稱** 和 **帳戶金鑰**。輸入帳戶名稱和帳戶金鑰您 **Azure 儲存體帳戶**。

	![Azure 儲存體設定][image-data-factory-tutorial-azurestorage-settings]

	您可以從入口網站取得您的 Azure 儲存體帳戶的帳戶名稱和帳戶金鑰，如下所示：

	![儲存體金鑰][image-data-factory-tutorial-storage-key]
  
5. 按一下之後 **確定** 針對新的資料存放區索引標籤，您應該會看到 **StorageLinkedService** 在清單中 **資料存放區** 上 **連結服務** 分頁。檢查 **通知** 中樞 (左側) 若要查看任何訊息。

	![連結的服務與儲存體的分頁][image-data-factory-tutorial-linkedservices-blade-storage]
   
6. 重複 **步驟 2 到 5** 建立另一個連結的服務名稱為： **HDInsightStorageLinkedService**。這是您的 HDInsight 叢集所使用的儲存體。
7. 確認您看到兩個 **StorageLinkedService** 和 **HDInsightStorageLinkedService** 在連結的服務刀鋒伺服器的清單中。
8. 在 **連結服務** blade 中，按一下 **加號 (+) 資料存放區** 從命令列。
9. 輸入 **AzureSqlLinkedService** 的名稱。
10. 按一下 **型別 (所需的設定)**, ，請選取 **Azure SQL Database**。
11. 現在，您應該下列額外的欄位上 **新的資料存放區** 分頁。輸入 Azure SQL 資料庫的名稱 **伺服器**, ，**資料庫** 名稱， **使用者名稱**, ，和 **密碼**, ，然後按一下 **確定**。
	1. 輸入 **MarketingCampaigns** 的 **資料庫**。這是您在步驟 1 中執行的指令碼所建立的 Azure SQL 資料庫。您應該確認此資料庫已確實建立由指令碼 (如果有錯誤)。
		
 		![Azure SQL 設定][image-data-factory-tutorial-azuresql-settings]

		若要從 Azure 管理入口網站取得這些值： 按一下 MarketingCampaigns 資料庫的 View SQL Database 連接字串

		![Azure SQL 資料庫連接字串][image-data-factory-tutorial-azuresql-database-connection-string]

12. 確認您看到您所建立的所有三個資料存放區： **StorageLinkedService**, ，**HDInsightStorageLinkedService**, ，和 **AzureSqlLinkedService**。
13. 您必須建立另一個連結的服務，但這一個計算服務，特別是 **Azure HDInsight 叢集**。入口網站尚不支援建立連結的運算服務。因此，您必須使用 Azure PowerShell 來建立此連結的服務。 
14. 切換至 **Azure PowerShell** 如果您已經開啟它 (或) 啟動 **Azure PowerShell**。如果您必須關閉並重新開啟 Azure PowerShell，您需要執行下列命令： 
	- 執行 **Add-azureaccount** 並輸入使用者名稱和您用來登入 Azure 預覽入口網站的密碼。  
	- 執行 **Get-azuresubscription** 若要檢視此帳戶的所有訂閱。
	- 執行 **Select-azuresubscription** 來選取您想要使用的訂閱。此訂用帳戶應該與您在「Azure Preview 入口網站」中使用的相同。 
15. 切換至 **AzureResourceManager** 模式為 Azure 資料 Factory 指令程式會在此模式中使用。

		Switch-AzureMode AzureResourceManager

16. 瀏覽至 **LinkedServices** 子資料夾中的 **C:\ADFWalkthrough** (或) 從資料夾解壓縮檔案的位置。
17. 開啟 **HDInsightLinkedService.json** 您最愛的編輯器，請注意，型別設定為在 **HDInsightOnDemandLinkedService**。


	> [AZURE.NOTE]Azure Data Factory 服務支援建立隨選叢集，並使用它處理輸入來產生輸出資料。您也可以使用自己的叢集執行相同作業。當您使用隨選 HDInsight 叢集時，系統會為每個配量建立叢集。然而，當您使用自己的 HDInsight 叢集時，叢集就可以立即處理配量。因此，在使用隨選叢集時，可能無法像使用自己的叢集那麼快看到輸出資料。基於範例的目的，讓我們使用隨選叢集。HDInsightLinkedService 連結至資料處理站的隨選 HDInsight 叢集。若要使用您自己的 HDInsight 叢集，請更新 HDInsightLinkedService.json 檔案的 [屬性] 區段 (取代 clustername、 使用者名稱和密碼與適當的值) 如下所示：
	> 
			"Properties": 
			{
        		"Type": "HDInsightBYOCLinkedService",
	        	"ClusterUri": "https://<clustername>.azurehdinsight.net/",
    	    	"UserName": "<username>",
    	    	"Password": "<password>",
    	    	"LinkedServiceName": "HDInsightStorageLinkedService"
    		}
		

18. 使用下列命令，將 $df 變數設定為 Data Factory 的名稱。

		$df = “LogProcessingFactory”
19. 使用 cmdlet **新增 AzureDataFactoryLinkedService** 來建立連結的服務，如下所示。開始使用儲存體帳戶：

		New-AzureDataFactoryLinkedService -ResourceGroupName ADF -DataFactoryName $df -File .\HDInsightLinkedService.json
 
	如果您對 ResourceGroupName、DataFactoryName 或 LinkedService 名稱使用不同的名稱，請在上述 Cmdlet 加以參考。另外，只在找不到檔案時，才提供連結的服務 JSON 檔案的完整檔案路徑。
20. 您應該會看到所有四個連結中的服務 **連結服務** 分頁，如下所示。如果未開啟連結服務分頁，則按一下連結的服務在 **資料 FACTORY** 頁，以查看 **LogProcessingFactory**。可能需要幾秒鐘的時間，[連結的服務] 分頁才能重新整理。

	![連結服務所有][image-data-factory-tutorial-linkedservices-all]
 

## <a name="MainStep4"></a> 步驟 4： 建立資料表 
在此步驟中，您將建立下列資料表：

- RawGameEventsTable
- PartitionedGameEventsTable
- RefGeoCodeDictionaryTable
- RefMarketingCampaignTable
- EnrichedGameEventsTable
- MarketingCampaignEffectivenessSQLTable
- MarketingCampaignEffectivenessBlobTable

	![教學課程的端對端流程][image-data-factory-tutorial-end-to-end-flow]
 
上圖顯示的管線在中間資料列，而資料表在頂端和底部資料列。

Azure 入口網站尚不支援建立資料集/資料表，因此在此版本中，您必須使用 Azure PowerShell 來建立資料表。

### 建立資料表

1.	在 Azure PowerShell 中，瀏覽至 **資料表** 資料夾 (* * C:\ADFWalkthrough\Tables**) 從解壓縮範例的位置。 
2.	使用 cmdlet **新增 AzureDataFactoryTable** 以建立資料表，如下所示的 **RawGameEventsTable**.json	


		New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df –File .\RawGameEventsTable.json

	如果您對 ResourceGroupName 和 DataFactoryName 使用不同的名稱，請在上述 Cmdlet 加以參考。另外，只在 Cmdlet 找不到檔案時，才提供資料表 JSON 檔案的完整檔案路徑。

3. 重複上述步驟來建立下列資料表：
		
		New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df –File .\PartitionedGameEventsTable.json
		
		New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df –File .\RefGeoCodeDictionaryTable.json
			
		New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df –File .\RefMarketingCampaignTable.json
			
		New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df –File .\EnrichedGameEventsTable.json
			
		New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df –File .\MarketingCampaignEffectivenessSQLTable.json
			
		New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df –File .\MarketingCampaignEffectivenessBlobTable.json



4. 在 **Azure 預覽入口網站**, ，按一下 **資料集** 中 **資料 FACTORY** 刀鋒伺服器 **LogProcessingFactory** 並確認您看到所有資料集 (資料表都是矩形的資料集)。

	![設定所有資料][image-data-factory-tutorial-datasets-all]

	您也可以從 Azure PowerShell 使用下列命令：
			
		Get-AzureDataFactoryTable –ResourceGroupName ADF –DataFactoryName $df

	


## <a name="MainStep5"></a> 步驟 5： 建立和排程管線
在此步驟中，您將建立下列管線： PartitionGameLogsPipeline、 EnrichGameLogsPipeline 和 AnalyzeMarketingCampaignPipeline。

1. 在 **Windows 檔案總管**, ，瀏覽至 **管線** 子資料夾中的 **C:\ADFWalkthrough** 資料夾 (或從已解壓縮範例所在的位置)。
2.	開啟 **PartitionGameLogsPipeline.json** 在您最愛的編輯器中，資料儲存體帳戶資訊的儲存體帳戶取代反白顯示並儲存檔案。
			
		"RAWINPUT": "wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/logs/rawgameevents/",
		"PARTITIONEDOUTPUT": "wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/logs/partitionedgameevents/",

3. 重複步驟來建立下列管線：
	1. **EnrichGameLogsPipeline**.json (3 個相符項目)
	2. **AnalyzeMarketingCampaignPipeline**.json (3 個相符項目)

	**重要事項：** 確認您已經取代了所有 <storageaccountname> 與儲存體帳戶名稱。
 
4.  在 **Azure PowerShell**, ，瀏覽至 **管線** 子資料夾中的 **C:\ADFWalkthrough** 資料夾 (或從已解壓縮範例所在的位置)。
5.  使用 cmdlet **新增 AzureDataFactoryPipeline** ，如下所示為建立管線 **PartitionGameLogspeline**.json	 
			
		New-AzureDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName $df –File .\PartitionGameLogsPipeline.json

	如果您對 ResourceGroupName、DataFactoryName 或 Pipeline 名稱使用不同的名稱，請在上述 Cmdlet 加以參考。另外，提供管線 JSON 檔案的完整檔案路徑。
6. 重複上述步驟來建立下列管線：
	1. **EnrichGameLogsPipeline**
			
			New-AzureDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName $df –File .\EnrichGameLogsPipeline.json

	2. **AnalyzeMarketingCampaignPipeline**
				
			New-AzureDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName $df –File .\AnalyzeMarketingCampaignPipeline.json

7. 使用 cmdlet **Get AzureDataFactoryPipeline** 取得管線的清單。
			
		Get-AzureDataFactoryPipeline –ResourceGroupName ADF –DataFactoryName $df

8. 管線建立之後，您可以指定將發生資料處理的持續時間。藉由指定管線的作用期間，依據對每個 ADF 資料表所定義之可用性屬性，您會定義將處理資料配量的持續時間。

若要指定管線的作用期間，您可以使用 Cmdlet Set-AzureDataFactoryPipelineActivePeriod。在本逐步解說中，範例資料是從 05/01 05/05。使用 2014年-05-01 為 StartDateTime。EndDateTime 是選擇性的。
			
		Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADF -DataFactoryName $df -StartDateTime 2014-05-01Z -EndDateTime 2014-05-05Z –Name PartitionGameLogsPipeline
  
9. 確認以設定管線的作用期間。
			
			Confirm
			Are you sure you want to set pipeline 'PartitionGameLogsPipeline' active period from '05/01/2014 00:00:00' to '05/05/2014 00:00:00'?
			[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): n

10. 重複上述的兩個步驟來設定下列管線的作用期間。
	1. **EnrichGameLogsPipeline**
			
			Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADF -DataFactoryName $df -StartDateTime 2014-05-01Z –EndDateTime 2014-05-05Z –Name EnrichGameLogsPipeline

	2. **AnalyzeMarketingCampaignPipeline**
			
			Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADF -DataFactoryName $df -StartDateTime 2014-05-01Z -EndDateTime 2014-05-05Z –Name AnalyzeMarketingCampaignPipeline

11. 在 **Azure 預覽入口網站**, ，按一下 **管線** (不是在管線的名稱)] 磚中 **資料 FACTORY** 刀鋒伺服器 **LogProcessingFactory**, ，您應該會看到您所建立的管線。

	![所有的管線][image-data-factory-tutorial-pipelines-all]

12. 在 **資料 FACTORY** 刀鋒伺服器 **LogProcessingFactory**, ，按一下 **圖表**。

	![圖表連結][image-data-factory-tutorial-diagram-link]

13. 您可以重新排列所看見的圖表，這裡是重新排列的圖表，在頂端顯示直接輸入，在底端則是輸出。您可以看到的輸出 **PartitionGameLogsPipeline** 做為輸入至 EnrichGameLogsPipeline 和輸出的傳入 **EnrichGameLogsPipeline** 傳遞至 **AnalyzeMarketingCampaignPipeline**。按兩下某個標題，以查看分頁所代表之成品的詳細資訊。

	![圖表檢視][image-data-factory-tutorial-diagram-view]

	**恭喜！** 您已成功建立 Azure Data Factory、連結的服務、管線、資料表，並開始工作流程。


## <a name="MainStep6"></a> 步驟 6： 監視管線和資料配量 

1.	如果您的 LogProcessingFactory 的 Data Factory 分頁未開啟，您可以執行下列其中一項：
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
 
7. 現在，在 **管線** 刀鋒伺服器 **PartiionGameLogsPipeline**, ，按一下 **Produced**。
8. 您應該會看到這個管線產生的資料集的清單： 
9. 按一下 **PartitionedGameEvents** 資料表中 **產生資料集** 分頁。 
10.	確認已選取 **狀態** 所有配量會設定為 **準備**。 
11.	按一下其中一個會將配量 **準備** 查看 **資料配量** 該扇區的分頁。

	![RawGameEventsTable 資料配量分頁][image-data-factory-monitoring-raw-game-events-table-dataslice-blade]

	如果發生錯誤，您會看到 **失敗 **這裡的狀態。您也可能會看到狀態的其中一個這兩個配量 **準備**, ，或兩者皆擁有狀態 **PendingValidation**, ，端視配量處理的速度。
 
	請參閱 [Azure 資料工廠開發人員參考][developer-reference] 來了解所有可能的配量的狀態。

12.	在 **資料配量** blade 中，按一下 [從執行 **活動執行** 清單。您應該會看到該配量的「活動執行」分頁。您應該會看到下列 **活動執行詳細資料** 分頁。

	![活動執行詳細資料] 分頁][image-data-factory-monitoring-activity-run-details]

13.	按一下 **下載** 下載檔案。對 HDInsight 處理的錯誤進行疑難排解時，這個畫面會特別有用。
	 
	
當所有管線都完成執行時，您可以看看 **MarketingCampaignEffectivenessTable** 中 **MarketingCampaigns** Azure SQL 資料庫來檢視結果。

**恭喜！** 您現在可以監視和疑難排解工作流程。您已經學會如何使用 Azure Data Factory 來處理資料，並取得分析。

## 擴充本教學課程使用的內部資料
在記錄處理程序從這份文件中的逐步解說的案例的最後一個步驟中，行銷促銷活動效率輸出已複製到 Azure SQL database。您也可以在組織內，將此資料移動到內部部署 SQL Server 以進行分析。
 
若要從 Azure Blob 複製行銷促銷活動效能資料的內部部署 SQL Server、 您需要建立額外的內部連結服務、 資料表和管線中導入這篇文章中的逐步解說。

練習 [逐步解說： 使用內部部署資料來源][tutorial-onpremises-using-powershell] 以了解如何建立管線來行銷促銷活動的效率資料複製到一個在內部部署 SQL Server 資料庫。
 

[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

[adftutorial-using-editor]: data-factory-tutorial.md

[adfgetstarted]: data-factory-get-started.md
[adfintroduction]: data-factory-introduction.md
[useonpremisesdatasources]: data-factory-use-onpremises-datasources.md
[usepigandhive]: data-factory-pig-hive-activities.md
[tutorial-onpremises-using-powershell]: data-factory-tutorial-extend-onpremises-using-powershell.md
[download-azure-powershell]: ../powershell-install-configure.md

[azure-preview-portal]: http://portal.azure.com
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[sqlcmd-install]: http://www.microsoft.com/download/details.aspx?id=35580
[azure-sql-firewall]: http://msdn.microsoft.com/library/azure/jj553530.aspx


[adfwalkthrough-download]: http://go.microsoft.com/fwlink/?LinkId=517495
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908


[image-data-factory-tutorial-end-to-end-flow]: ./media/data-factory-tutorial-using-powershell/EndToEndWorkflow.png

[image-data-factory-tutorial-partition-game-logs-pipeline]: ./media/data-factory-tutorial-using-powershell/PartitionGameLogsPipeline.png

[image-data-factory-tutorial-enrich-game-logs-pipeline]: ./media/data-factory-tutorial-using-powershell/EnrichGameLogsPipeline.png

[image-data-factory-tutorial-analyze-marketing-campaign-pipeline]: ./media/data-factory-tutorial-using-powershell/AnalyzeMarketingCampaignPipeline.png


[image-data-factory-tutorial-egress-to-onprem-pipeline]: ./media/data-factory-tutorial-using-powershell/EgreeDataToOnPremPipeline.png

[image-data-factory-tutorial-set-firewall-rules-azure-db]: ./media/data-factory-tutorial-using-powershell/SetFirewallRuleForAzureDatabase.png

[image-data-factory-tutorial-portal-new-everything]: ./media/data-factory-tutorial-using-powershell/PortalNewEverything.png

[image-data-factory-tutorial-datastorage-cache-backup]: ./media/data-factory-tutorial-using-powershell/DataStorageCacheBackup.png

[image-data-factory-tutorial-dataservices-blade]: ./media/data-factory-tutorial-using-powershell/DataServicesBlade.png

[image-data-factory-tutorial-new-datafactory-blade]: ./media/data-factory-tutorial-using-powershell/NewDataFactoryBlade.png

[image-data-factory-tutorial-resourcegroup-blade]: ./media/data-factory-tutorial-using-powershell/ResourceGroupBlade.png

[image-data-factory-tutorial-create-resourcegroup]: ./media/data-factory-tutorial-using-powershell/CreateResourceGroup.png

[image-data-factory-tutorial-datafactory-homepage]: ./media/data-factory-tutorial-using-powershell/DataFactoryHomePage.png

[image-data-factory-tutorial-create-datafactory]: ./media/data-factory-tutorial-using-powershell/CreateDataFactory.png

[image-data-factory-tutorial-linkedservice-tile]: ./media/data-factory-tutorial-using-powershell/LinkedServiceTile.png

[image-data-factory-tutorial-linkedservices-add-datstore]: ./media/data-factory-tutorial-using-powershell/LinkedServicesAddDataStore.png

[image-data-factory-tutorial-datastoretype-azurestorage]: ./media/data-factory-tutorial-using-powershell/DataStoreTypeAzureStorageAccount.png

[image-data-factory-tutorial-azurestorage-settings]: ./media/data-factory-tutorial-using-powershell/AzureStorageSettings.png

[image-data-factory-tutorial-storage-key]: ./media/data-factory-tutorial-using-powershell/StorageKeyFromAzurePortal.png

[image-data-factory-tutorial-linkedservices-blade-storage]: ./media/data-factory-tutorial-using-powershell/LinkedServicesBladeWithAzureStorage.png

[image-data-factory-tutorial-azuresql-settings]: ./media/data-factory-tutorial-using-powershell/AzureSQLDatabaseSettings.png

[image-data-factory-tutorial-azuresql-database-connection-string]: ./media/data-factory-tutorial-using-powershell/DatabaseConnectionString.png

[image-data-factory-tutorial-linkedservices-all]: ./media/data-factory-tutorial-using-powershell/LinkedServicesAll.png

[image-data-factory-tutorial-datasets-all]: ./media/data-factory-tutorial-using-powershell/DataSetsAllTables.png

[image-data-factory-tutorial-pipelines-all]: ./media/data-factory-tutorial-using-powershell/AllPipelines.png

[image-data-factory-tutorial-diagram-link]: ./media/data-factory-tutorial-using-powershell/DataFactoryDiagramLink.png

[image-data-factory-tutorial-diagram-view]: ./media/data-factory-tutorial-using-powershell/DiagramView.png

[image-data-factory-monitoring-startboard]: ./media/data-factory-tutorial-using-powershell/MonitoringStartBoard.png

[image-data-factory-monitoring-hub-everything]: ./media/data-factory-tutorial-using-powershell/MonitoringHubEverything.png

[image-data-factory-monitoring-browse-datafactories]: ./media/data-factory-tutorial-using-powershell/MonitoringBrowseDataFactories.png

[image-data-factory-monitoring-pipeline-consumed-produced]: ./media/data-factory-tutorial-using-powershell/MonitoringPipelineConsumedProduced.png

[image-data-factory-monitoring-raw-game-events-table]: ./media/data-factory-tutorial-using-powershell/MonitoringRawGameEventsTable.png

[image-data-factory-monitoring-raw-game-events-table-dataslice-blade]: ./media/data-factory-tutorial-using-powershell/MonitoringPartitionGameEventsTableDataSliceBlade.png

[image-data-factory-monitoring-activity-run-details]: ./media/data-factory-tutorial-using-powershell/MonitoringActivityRunDetails.png

[image-data-factory-datamanagementgateway-configuration-manager]: ./media/data-factory-tutorial-using-powershell/DataManagementGatewayConfigurationManager.png

[image-data-factory-new-datafactory-menu]: ./media/data-factory-tutorial-using-powershell/NewDataFactoryMenu.png

[image-data-factory-new-datafactory-create-button]: ./media/data-factory-tutorial-using-powershell/DataFactoryCreateButton.png

<!---HONumber=GIT-SubDir--> 