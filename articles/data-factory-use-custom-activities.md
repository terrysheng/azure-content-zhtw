<properties title="Use custom activities in an Azure Data Factory pipeline" pageTitle="在 Azure 資料處理站管線中使用自訂活動" description="了解如何建立自訂活動，並在 Azure 資料處理站管線中使用這些活動。" metaKeywords=""  services="data-factory" solutions=""  documentationCenter="" authors="spelluru" manager="jhubbard" editor="monicar" />

<tags ms.service="data-factory" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="spelluru" />

# 在 Azure 資料處理站管線中使用自訂活動
Azure 資料處理站支援內建活動，例如**複製活動**和**HDInsight 活動**，用於在管線中進行移動和處理資料。您也可以使用您自己的轉換/處理邏輯建立自訂活動，並在管線中使用該活動。自訂活動在 HDInsight 叢集上以僅限對應的作業執行，因此您必須為您的管線中的自訂活動連結 HDInsight 叢集。
 
本文說明如何建立自訂活動，並在 Azure 資料處理站管線中使用它。本文也提供建立及使用自訂活動的詳細逐步解說與逐步指示。

## 建立自訂活動

若要建立自訂活動：
 
1.	在**Visual Studio 2013**中建立類別庫專案。
2.	下載並安裝 [Azure 資料處理站的 NuGet 封裝 ][nuget-package]。逐步解說中有安裝指示。
3. 在類別庫中原始程式檔的頂端加入下列 using 陳述式。
	
		using Microsoft.Azure.Management.DataFactories.Models;
		using Microsoft.DataFactories.Runtime; 

4. 更新類別以實作 **ICustomActivity** 介面。
	<ol type='a'>
		<li>
			從  <b>ICustomActivity</b>. 衍生的類別
			<br/>
			範例： <br/>
			public class <b>MyCustomActivity :ICustomActivity</b>
		</li>

		<li>
			實作  <b>ICustomActivity</b>  介面的 <b>Execute</b>  方法
		</li>

	</ol>
5. 編譯專案。


## 在管線中使用自訂活動
若要在管線中使用自訂活動：

1.	**壓縮**專案的 **bin\debug** 或 **bin\release** 輸出資料夾中的所有二進位檔案。 
2.	**將 zip** 檔案當做 Blob 上傳至您的 **Azure Blob 儲存體**。 
3.	更新**管線 JSON** 檔案，以參考 zip 檔案、自訂活動 DLL、活動類別，以及包含管線 JSON 中 zip 檔案的 Blob。在 JSON 檔案中：
	<ol type ="a">
		<li><b>活動類型</b>應設為 <b>CustomActivity</b>。</li>
		<li><b>AssemblyName</b> 是 Visual Studio 專案的輸出 DLL 的名稱。</li>
		<li><b>EntryPoint</b> 指定<b>實作 </b>ICustomActivity<b>介面</b>的<b>類別</b>的<b>命名空間</b>和</li>名稱。
		<li><b>PackageLinkedService</b> 是連結的服務，參考包含 zip 檔案的 Blob。 </li>
		<li><b>PackageFile</b> 指定已上傳至 Azure Blob 儲存體的 zip 檔案的位置和名稱 。</li>
		<li><b>LinkedServiceName</b> 是連結服務的名稱，此服務將 HDInsight 叢集 (隨選或您自己) 連結至資料處理站。自訂活動在指定 HDInsight 叢集上以僅限對應的作業執行。</li>
	</ol>

	

	**部分 JSON 範例**

		"Name": "MyCustomActivity",
    	"Type": "CustomActivity",
    	"Inputs": [{"Name": "EmpTableFromBlob"}],
    	"Outputs": [{"Name": "OutputTableForCustom"}],
		"LinkedServiceName": "myhdinsightcluster",
    	"Transformation":
    	{
	    	"AssemblyName": "MyCustomActivity.dll",
    	    "EntryPoint": "MyCustomActivityNS.MyCustomActivity",
    	    "PackageLinkedService": "MyBlobStore",
    	    "PackageFile": "customactivitycontainer/MyCustomActivity.zip",

## 若要更新自訂活動
如果您更新自訂活動的程式碼、建置它，並將包含新二進位檔案的 zip 檔案上傳至 Blob 儲存體，則必須執行 **New-AzureDataFactoryPipeline** 以自訂活動覆寫現有的管線。使用自訂活動建立管線時，zip 檔案會從您指定的 Blob 儲存體複製到 Azure 資料處理站容器，此容器位於連接到 HDInsight 叢集的 Blob 儲存體上。這只會在管線建立時進行。因此，在更新自訂活動時必須重新建立管線。 

下列逐步解說為您提供建立自訂活動以及在 Azure 資料處理站管線中使用該活動的逐步指示。本逐步解說是從[開始使用 Azure 資料處理站的教學課程 ][adfgetstarted] 延伸而來。如果您想要看自訂活動運作，您需要先依照入門教學課程操作，然後執行此逐步解說。 

## 逐步介紹
**必要條件：**


- [開始使用 Azure 資料處理站的教學課程 ][adfgetstarted]。您必須完成本文中的教學課程，再繼續進行本逐步解說。
- Visual Studio 2012 或 2013
- 下載並安裝 [Windows Azure .NET SDK][azure-developer-center]
- 下載 [Azure 資料處理站的 NuGet 封裝 ][nuget-package].
- 下載並安裝 Azure 儲存體的 NuGet 封裝。逐步解說中有指示，因此您可以略過此步驟。

### 步驟 1：建立自訂活動

1.	建立 .NET 類別庫專案。
	<ol type="a">
		<li>啟動 <b>Visual Studio 2012</b> 或 <b>Visual Studio 2013</b>。</li>
		<li>按一下<b> [檔案]</b>，指向<b> [新增]</b>，然後按一下<b> [專案]</b>。</li> 
		<li>展開<b> [範本]</b>，然後選取<b> [Visual C#]</b>。在此逐步解說中，您使用 C# 中，但您可以使用任何 .NET 語言來開發自訂活動。</li> 
		<li>從右邊的專案類型清單中選取<b> [類別庫]</b>。</li>
		<li>在<b> [名稱]</b> 輸入 <b>MyCustomActivity</b>。</li> 
		<li>替<b> [位置]</b> 選取 <b>C:\ADFGetStarted</b>。</li>
		<li>按一下<b> [確定]</b> 建立專案。</li>
	</ol>
2.  按一下<b> [工具]</b>，指向<b> [NuGet 封裝管理員]</b>，然後按一下<b> [封裝管理員主控台]</b>。
3.	在<b> [封裝管理員主控台]</b> 中，執行下列命令匯入您先前下載的 <b>Microsoft.Azure.Management.DataFactories</b>。資料夾以包含下載的資料處理站 NuGet 封裝的位置取代。

		Install-Package Microsoft.Azure.Management.DataFactories -Source d:\packages -Pre

3.	在<b> [封裝管理員主控台]</b> 中，執行下列命令匯入 <b>Microsoft.DataFactories.Runtime</b>。資料夾以包含下載的資料處理站 NuGet 封裝的位置取代。

		Install-Package Microsoft.DataFactories.Runtime -Source d:\packages -Pre

4. 將 Windows Azure 儲存體 NuGet 封裝匯入專案。

		Install-Package WindowsAzure.Storage

5. 在專案的原始程式檔中加入以下 **using** 陳述式。

		using System.IO;
		using System.Globalization;
	
		using Microsoft.Azure.Management.DataFactories.Models;
		using Microsoft.DataFactories.Runtime; 
	
		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Blob;
  
6. 將 **namespace** 的名稱變更為 **MyCustomActivityNS**。

		namespace MyCustomActivityNS

7. 將類別名稱變更為 **MyCustomActivity**，並從 **ICustomActivity** 介面衍生它，如下所示。

		public class MyCustomActivity : ICustomActivity

8. 實作 (新增) **ICustomActivity** 介面的 **Execute** 方法至 **MyCustomActivity** 類別，然後將以下範例程式碼加入方法。 

	**inputTables** 和 **outputTables** 參數代表活動的輸入和輸出資料表，名符其實。若要查看記錄的訊息，在可從 Azure 入口網站下載的記錄檔中使用 **logger** 物件，或使用指令程式。**extendedProperties** 字典包含您在活動和其值的 JSON 檔案中指定的延伸內容的清單。 

	下列範例程式碼會計算輸入 Blob 中的行數，並在輸出 Blob 中產生下列內容：Blob 的路徑、Blob 中的行數、活動執行的機器、目前的日期時間。

        public IDictionary<string, string> Execute(
                    IEnumerable<ResolvedTable> inputTables, 
                    IEnumerable<ResolvedTable> outputTables, 
                    IDictionary<string, string> extendedProperties, 
                    IActivityLogger logger)
        {
            string output = string.Empty;

            logger.Write(TraceEventType.Information, "Before anything...");

            logger.Write(TraceEventType.Information, "Printing dictionary entities if any...");
            foreach (KeyValuePair<string, string> entry in extendedProperties)
            {
                logger.Write(TraceEventType.Information, "<key:{0}> <value:{1}>", entry.Key, entry.Value);
            }

            foreach (ResolvedTable inputTable in inputTables)
            {
                string connectionString = GetConnectionString(inputTable.LinkedService);
                string folderPath = GetFolderPath(inputTable.Table);

                if (String.IsNullOrEmpty(connectionString) ||
                    String.IsNullOrEmpty(folderPath))
                {
                    continue;
                }

                logger.Write(TraceEventType.Information, "Reading blob from: {0}", folderPath);

                CloudStorageAccount inputStorageAccount = CloudStorageAccount.Parse(connectionString);
                CloudBlobClient inputClient = inputStorageAccount.CreateCloudBlobClient();

                BlobContinuationToken continuationToken = null;

                do
                {
                    BlobResultSegment result = inputClient.ListBlobsSegmented(folderPath, 
												true, 
												BlobListingDetails.Metadata, 
												null, 
												continuationToken, 
												null, 
												null);
                    foreach (IListBlobItem listBlobItem in result.Results)
                    {
                        CloudBlockBlob inputBlob = listBlobItem as CloudBlockBlob;
                        int count = 0;
                        if (inputBlob != null)
                        {
                            using (StreamReader sr = new StreamReader(inputBlob.OpenRead()))
                            {
                                while (!sr.EndOfStream)
                                {
                                    string line = sr.ReadLine();
                                    if (count == 0)
                                    {
                                        logger.Write(TraceEventType.Information, "First line: [{0}]", line);
                                    }
                                    count++;
                                }

                            }

                        }
                        output += string.Format(CultureInfo.InvariantCulture, 
										"{0},{1},{2},{3}\n", 
										folderPath, 
										count, 
										Environment.MachineName, 
										DateTime.UtcNow);
                    }
                    continuationToken = result.ContinuationToken;

                } while (continuationToken != null);
            }

            foreach (ResolvedTable outputTable in outputTables)
            {
                string connectionString = GetConnectionString(outputTable.LinkedService);
                string folderPath = GetFolderPath(outputTable.Table);

                if (String.IsNullOrEmpty(connectionString) ||
                    String.IsNullOrEmpty(folderPath))
                {
                    continue;
                }

                logger.Write(TraceEventType.Information, "Writing blob to: {0}", folderPath);

                CloudStorageAccount outputStorageAccount = CloudStorageAccount.Parse(connectionString);
                Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + Guid.NewGuid() + ".csv");

                CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
                outputBlob.UploadText(output);

            }
            return new Dictionary<string, string>();

        }

9. 新增下列 Helper 方法。**Execute** 方法會叫用這些 Helper 方法。**GetConnectionString** 方法會擷取 Azure 儲存體連接字串，**GetFolderPath** 方法會擷取 Blob 位置。 


        private static string GetConnectionString(LinkedService asset)
        {
            AzureStorageLinkedService storageAsset;
            if (asset == null)
            {
                return null;
            }

            storageAsset = asset.Properties as AzureStorageLinkedService;
            if (storageAsset == null)
            {
                return null;
            }

            return storageAsset.ConnectionString;
        }

        private static string GetFolderPath(Table dataArtifact)
        {
            AzureBlobLocation blobLocation;
            if (dataArtifact == null || dataArtifact.Properties == null)
            {
                return null;
            }

            blobLocation = dataArtifact.Properties.Location as AzureBlobLocation;
            if (blobLocation == null)
            {
                return null;
            }

            return blobLocation.FolderPath;
        }
   


10. 編譯專案。按一下功能表中的** [建置]**，然後按一下** [建議方案]**。
11. 啟動 **Windows 檔案總管**，瀏覽至 **bin\debug** 或 **bin\release** 資料夾，取決於建置類型。
12. 建立 zip 檔案 **MyCustomActivity.zip**，檔案中包含 <project folder>\bin\Debug 資料夾中的所有二進位檔。
	![zip output binaries][image-data-factory-zip-output-binaries]
13. 將 **MyCustomActivity.zip** 當做 Blob 上傳至 Blob 容器：Azure Blob 儲存體中的 **customactvitycontainer**，由 **ADFTutorialDataFactory** 中的 **MyBlobStore** 連結服務使用。  若 Blob 容器 **blobcustomactivitycontainer** 不存在，則建立之 。 
    ![upload zip to blob][image-data-factory-upload-zip-to-blob]

### 建立   HDInsight 叢集的連結服務，將會用來執行自訂活動
Azure Data Factory 服務支援建立隨選叢集，並使用它處理輸入來產生輸出資料。您也可以使用自己的叢集執行相同作業。當您使用隨選 HDInsight 叢集時，系統會為每個配量建立叢集。然而，如果您使用自己的 HDInsight 叢集，叢集已經準備好立即處理配量。因此，在使用隨選叢集時，可能無法像使用自己的叢集那麼快看到輸出資料。基於範例的目的，讓我們使用隨選叢集。 

> [WACOM.NOTE] 如果您已運用[使用 Pig 和 Hive 與 Azure 資料處理站的逐步解說][hivewalkthrough] 延伸 [開始使用 Azure 資料處理站的教學課程 ][adfgetstarted]，可以略過此連結服務的建立，並使用 ADFTutorialDataFactory 中已有的連結服務。 

#### 若要使用隨選 HDInsight 叢集

1. 建立名為 **HDInsightOnDemandCluster.json** 且包含下列內容的 JSON 檔案，並儲存至 **C:\ADFGetStarted\Custom** 資料夾。


		{
    		"name": "HDInsightOnDemandCluster",
    		"properties": 
    		{
        		"type": "HDInsightOnDemandLinkedService",
				"clusterSize": 4,
        		"jobsContainer": "adftutorialjobscontainer",
        		"timeToLive": "00:05:00",
        		"linkedServiceName": "MyBlobStore"
    		}
		}

2. 啟動 **Azure PowerShell**，並執行下列命令名，來切換 **AzureResourceManager** 模式。Azure Data Factory Cmdlets 可在 **AzureResourceManager** 模式中使用。

         switch-azuremode AzureResourceManager
		

3. 切換到 **C:\ADFGetstarted\Custom** 資料夾。
4. 執行下列命令，為隨選 HDInsight 叢集建立連結的服務。
 
		New-AzureDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -File .\HDInsightOnDemandCluster.json
  
   
#### 若要使用您自己的 HDInsight 叢集： 

1. 建立名為 **MyHDInsightCluster.json** 且包含下列內容的 JSON 檔案，並儲存至 **C:\ADFGetStarted\Custom** 資料夾。先以適當的值取代叢集名稱、使用者名稱和密碼，再儲存 JSON 檔案。  

		{
   			"Name": "MyHDInsightCluster",
    		"Properties": 
			{
        		"Type": "HDInsightBYOCLinkedService",
	        	"ClusterUri": "https://<clustername>.azurehdinsight.net/",
    	    	"UserName": "<username>",
    	    	"Password": "<password>",
    	    	"LinkedServiceName": "MyBlobStore"
    		}
		}

2. 啟動 **Azure PowerShell**，並執行下列命令名，來切換 **AzureResourceManager** 模式。Azure Data Factory Cmdlets 可在 **AzureResourceManager** 模式中使用。

         switch-azuremode AzureResourceManager
		

3. 切換到 **C:\ADFGetstarted\Custom** 資料夾。
4. 執行下列命令，為隨選 HDInsight 叢集建立連結的服務。
 
		New-AzureDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -File .\MyHDInsightCluster.json



### 步驟 2：在管線中使用自訂活動
讓我們延伸[開始使用 Azure 資料處理站的教學課程 ][adfgetstarted]，以建立另一個管線來測試這個自訂活動。

#### 建立 HDInsight 叢集的連結服務，用來執行自訂活動


1.	建立管線 JSON，如下列範例所示，並將它儲存為 **ADFTutorialPipelineCustom.json** (儲存於 **C:\ADFGetStarted\Custom** 資料夾)。將 **LinkedServiceName** 名稱變更為 HDInsight 叢集的名稱 (**HDInsightOnDemandCluster** 或 **MyHDInsightCluster**)


		{
    		"name": "ADFTutorialPipelineCustom",
    		"properties":
    		{
        		"description" : "Use custom activity",
        		"activities":
        		[
					{
                		"Name": "MyCustomActivity",
                     	"Type": "CustomActivity",
                     	"Inputs": [{"Name": "EmpTableFromBlob"}],
                     	"Outputs": [{"Name": "OutputTableForCustom"}],
						"LinkedServiceName": "MyHDInsightCluster",
                     	"Transformation":
                     	{
                        	"AssemblyName": "MyCustomActivity.dll",
                            "EntryPoint": "MyCustomActivityNS.MyCustomActivity",
                            "PackageLinkedService": "MyBlobStore",
                            "PackageFile": "customactivitycontainer/MyCustomActivity.zip",
                            "ExtendedProperties":
							{
								"SliceStart": "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))"
							}
                      	},
                        "Policy":
                        {
                        	"Concurrency": 1,
                            "ExecutionPriorityOrder": "OldestFirst",
                            "Retry": 3,
                            "Timeout": "00:30:00",
                            "Delay": "00:00:00"		
						}
					}
				]
			}
		}

	請注意： 

	- [活動] 區段中有一個活動，它的類型是：**CustomActivity**。
	- 使用您在開始使用教學課程中所用的同一輸入資料表 **EmpTableFromBlob**。
	- 使用您將在下一個步驟中建立的新輸出資料表 **OutputTableForCustom**。
	- **AssemblyName** 設定為 DLL 的名稱：**MyActivities.dll**。
	- **EntryPoint** 設為 **MyCustomActivityNS.MyCustomActivity**。
	- **PackageLinkedService** 設為 **MyBlobStore**，後者是在[開始使用 Azure 資料處理站的教學課程][adfgetstarted] 中建立。此 Blob 存放區包含自訂活動 zip 檔案。
	- **PackageFile** 設為 **customactivitycontainer/MyCustomActivity.zip**。
     

4. 建立輸出資料表 (**OutputTableForCustom**，由管線 JSON 參考) 的 JSON 檔案，並儲存為 C:\ADFGetStarted\Custom\OutputTableForCustom.json。

		

		{
    		"name": "OutputTableForCustom",
    		"properties":
    		{
        		"location": 
        		{
					"type": "AzureBlobLocation",
					"folderPath": "adftutorial/customactivityoutput/{Slice}",
					"partitionedBy": [ { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } }],

					"linkedServiceName": "MyBlobStore"
        		},
        		"availability": 
        		{
            		"frequency": "hour",
            		"interval": 1
        		}   
    		}
		}

 	輸出位置是 **adftutorial/customactivityoutput/YYYYMMDDHH/**，其中 YYYYMMDDHH 是配置的產生時間年、月、日、時。如需詳細資訊，請參閱[開發人員參考資料 ][adf-developer-reference]。 

5. 執行下列**命令**在 **ADFTutorialDataFactory** 中建立輸出資料表。
		
		

		New-AzureDataFactoryTable  -DataFactoryName ADFTutorialDataFactory -File C:\ADFGetStarted\Custom\OutputTableForCustom.json -ResourceGroupName ADFTutorialResourceGroup


6. 現在，執行下列命令**建立管線**。您已在先前的步驟中建立管線 JSON 檔案。

		New-AzureDataFactoryPipeline  -DataFactoryName ADFTutorialDataFactory -File C:\ADFGetStarted\Custom\ADFTutorialPipelineCustom.json -ResourceGroupName ADFTutorialResourceGroup



7. 執行下列 PowerShell 命令為您建立的管線**設定使用期間**。

		Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -StartDateTime 2014-09-29 -EndDateTime 2014-09-30 -Name ADFTutorialPipelineCustom

	> [WACOM.NOTE] 以目前的日期取代 **StartDateTime** 值，以隔天的日期取代 **EndDateTime** 值。StartDateTime 和 EndDateTime 都是 UTC 時間，且必須採用 [ISO 格式](http://en.wikipedia.org/wiki/ISO_8601)。例如：2014-10-14T16:32:41Z。**EndDateTime** 是選擇性的，但我們將在本教學課程中使用。 
	> 如果您未指定 **EndDateTime**，系統會計算為 "**StartDateTime + 48 小時**"。若要無限期地執行管線， 請將 **EndDateTime** 指定為 **9/9/9999**。



8. 確認是在 Blob 儲存體的 **adftutorial** 容器中產生輸出檔案。

	![output from custom activity][image-data-factory-ouput-from-custom-activity]

9. 如果您開啟輸出檔，您應該會看到類似下面的輸出：
	
	adftutorial/input,2,WORKERNODE0,10/07/2014 18:34:33 

	(Blob 的位置), (Blob 中的行數), (活動執行的節點), (日期時間戳記)

10.	使用 [Azure 入口網站 ][azure-preview-portal] 或 Azure PowerShell 指令程式來監視您的資料處理站、 管線和資料集。您可從自訂活動的程式碼中的 **ActivityLogger** 查看訊息 (在可從入口網站下載的記錄檔中)，或使用指令程式。

	![download logs from custom activity][image-data-factory-download-logs-from-custom-activity]
   
如需監視資料集和管線的詳細步驟，請參閱[開始使用 Azure 資料處理站 ][adfgetstarted]。      
    
## 另請參閱

文章 | 描述
------ | ---------------
[能讓您的管線使用內部部署資料 ][use-onpremises-datasources] | 本文逐步解說如何將資料從內部部署 SQL Server 資料庫複製到 Azure Blob。
[搭配使用 Pig 和 Hive 與 Azure Data Factory][use-pig-and-hive-with-data-factory] | 本文逐步解說如何使用 HDInsight 活動執行 hive/pig 指令碼來處理輸入的資料並產生輸出資料。 
[教學課程：使用 Data Factory 移動和處理記錄檔 ][adf-tutorial] | 本文章提供端對端逐步解說，示範如何實作類似真實的案例，使用 Azure Data Factory 將記錄檔的資料轉換成見解。
[在資料處理站中使用自訂活動 ][use-custom-activities] | 本文提供逐步解說，以及建立自訂活動並在管線中使用該活動的逐步指示。 
[使用 PowerShell 監視及管理 Azure 資料處理站 ][monitor-manage-using-powershell] | 本文描述如何使用 Azure PowerShell 指令程式監視 Azure 資料處理站。您可以在 ADFTutorialDataFactory 上嘗試文章中的範例。
[疑難排解資料處理站的問題 ][troubleshoot] | 本文說明如何對 Azure 資料處理站的問題進行疑難排解。您可以透過引用一個錯誤 (刪除 Azure SQL Database 中的資料表)，嘗試本文中針對 ADFTutorialDataFactory 所做的逐步解說。 
[Azure 資料處理站開發人員參考資料 ][developer-reference] | 開發人員參考資料之中有指令程式、JSON 指令碼、函式等等...的完整參考內容。 


[monitor-manage-using-powershell]: ../data-factory-monitor-manage-using-powershell
[use-onpremises-datasources]: ../data-factory-use-onpremises-datasources
[adf-tutorial]: ../data-factory-tutorial
[use-custom-activities]: ../data-factory-use-custom-activities
[use-pig-and-hive-with-data-factory]: ../data-factory-pig-hive-activities
[troubleshoot]: ../data-factory-troubleshoot
[data-factory-introduction]: ../data-factory-introduction


[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456




[nuget-package]: http://go.microsoft.com/fwlink/?LinkId=517478
[azure-developer-center]: http://azure.microsoft.com/zh-tw/develop/net/
[adf-developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[azure-preview-portal]: https://portal.azure.com/

[adfgetstarted]: ../data-factory-get-started
[hivewalkthrough]: ../data-factory-pig-hive-activities

[image-data-factory-zip-output-binaries]: ./media/data-factory-use-custom-activities/ZipOuputBinaries.png

[image-data-factory-upload-zip-to-blob]: ./media/data-factory-use-custom-activities/UploadZipToBlob.png

[image-data-factory-ouput-from-custom-activity]: ./media/data-factory-use-custom-activities/OutputFilesFromCustomActivity.png

[image-data-factory-download-logs-from-custom-activity]: ./media/data-factory-use-custom-activities/DownloadLogsFromCustomActivity.png
