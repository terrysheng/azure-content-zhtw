<properties 
	pageTitle="在 Azure 資料處理站管線中使用自訂活動" 
	description="了解如何建立自訂活動，並在 Azure 資料處理站管線中使用這些活動。" 
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
	ms.date="06/02/2015" 
	ms.author="spelluru"/>

# 在 Azure 資料處理站管線中使用自訂活動
Azure 的資料處理站支援內建活動例如 **複製活動** 和 **HDInsight 活動** ，用於在管線中進行移動和處理資料。您也可以使用您自己的轉換/處理邏輯建立自訂的.NET 活動，並在管線中使用的活動。您可以設定用來執行使用活動 **Azure HDInsight** 叢集或 **Azure 批次** 服務。

本文說明如何建立自訂活動，並在 Azure 資料處理站管線中使用它。本文也提供建立及使用自訂活動的詳細逐步解說與逐步指示。本逐步解說會使用連結的 HDInsight 服務。若要使用 Azure 批次連結服務相反地，您建立連結的服務型別的 **AzureBatchLinkedService** 並將它用於管線 JSON 的活動區段 (* * linkedServiceName * *)。請參閱 [Azure 批次連結服務](#AzureBatch) ＞ 一節以取得詳細資料，在 Azure 批次中使用自訂活動。

## 必要條件
下載最新 [Azure 資料 Factory 的 NuGet 封裝][nuget-package] 並安裝它。中的指示 [逐步解說](#SupportedSourcesAndSinks) 在這篇文章。

## 建立自訂活動

若要建立自訂活動：
 
1.	建立 **類別庫** Visual Studio 2013 中的專案。
3. 在類別庫中原始程式檔的頂端加入下列 using 陳述式。
	
		using Microsoft.Azure.Management.DataFactories.Models;
		using Microsoft.DataFactories.Runtime; 

4. 更新類別來實作 **IDotNetActivity** 介面。
	<ol type='a'>
	<li>
		將類別衍生自 <b>IDotNetActivity</b>。
		<br/>
		範例： <br/>
		公用類別 <b>MyDotNetActivity： IDotNetActivity</b>
	</li>

	<li>
		實作 <b>Execute</b> 方法 <b>IDotNetActivity</b> 介面
	</li>

</ol>
5. 編譯專案。


## 在管線中使用自訂活動
若要在管線中使用自訂活動：

1.	**壓縮** 所有的二進位檔案從 **bin\debug** 或 **bin\release** 輸出專案的資料夾。 
2.	**上傳 zip** 檔案當作 blob 到您 **Azure blob 儲存體**。 
3.	更新 **管線 JSON** zip 檔案、 DLL 的自訂活動，活動類別，以及包含管線 JSON 中的 zip 檔案的 blob 參考的檔案。在 JSON 檔案中：
	<ol type ="a">
	<li><b>活動型別</b> 應該設定為 <b>DotNetActivity</b>。</li>
	<li><b>AssemblyName</b> 是 Visual Studio 專案的輸出 DLL 的名稱。</li>
	<li><b>EntryPoint</b> 指定 <b>命名空間</b> 和 <b>名稱</b> 的 <b>類別</b> 實作 <b>IDotNetActivity</b> 介面。</li>
	<li><b>PackageLinkedService</b> 是指的是 blob，其中包含的 zip 檔案的連結的服務。</li>
	<li><b>PackageFile</b> 指定的位置和已上傳至 Azure blob 儲存體的 zip 檔案的名稱。</li>
	<li><b>LinkedServiceName</b> 是連結的服務，用來連結 HDInsight 叢集的名稱 (隨選或您自己) 至資料處理站。自訂活動在指定 HDInsight 叢集上以僅限對應的作業執行。</li>
</ol>**部分 JSON 範例**

		"Name": "MyDotNetActivity",
    	"Type": "DotNetActivity",
    	"Inputs": [{"Name": "EmpTableFromBlob"}],
    	"Outputs": [{"Name": "OutputTableForCustom"}],
		"LinkedServiceName": "myhdinsightcluster",
    	"Transformation":
    	{
	    	"AssemblyName": "MyDotNetActivity.dll",
    	    "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
    	    "PackageLinkedService": "MyBlobStore",
    	    "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",

## 更新自訂活動
如果您更新的自訂活動的程式碼，建置它，並上傳至 blob 儲存體的新二進位檔案所在的 zip 檔案。

## <a name="walkthrough" /> 逐步解說
本逐步解說為您提供建立自訂活動及 Azure 資料 Factory 管線中使用活動的逐步指示。本逐步解說的教學課程的延伸 [開始使用 Azure 資料 Factory][adfgetstarted]。如果您想要看自訂活動運作，您需要先依照入門教學課程操作，然後執行此逐步解說。

**必要條件：**


- 從教學課程 [開始使用 Azure 資料 Factory][adfgetstarted]。在進行本逐步解說之前，您必須完成本教學課程，從這篇文章。
- Visual Studio 2012 或 2013
- 下載並安裝 [Azure.NET SDK][azure-developer-center]
- 下載最新 [Azure 資料 Factory 的 NuGet 封裝][nuget-package] 並安裝它。逐步解說中包含相關指示。
- 下載並安裝 Azure 儲存體的 NuGet 封裝。逐步解說中有指示，因此您可以略過此步驟。

## 步驟 1： 建立自訂活動

1.	建立 .NET 類別庫專案。
	<ol type="a">
	<li>啟動 <b>Visual Studio 2012</b> 或 <b>Visual Studio 2013</b>。</li>
	<li>按一下 <b>檔案</b>, ，指向 <b>新增</b>, ，然後按一下 <b>專案</b>。</li> 
	<li>展開 <b>範本</b>, ，然後選取 <b>Visual C#</b>。在此逐步解說中，您使用 C# 中，但您可以使用任何 .NET 語言來開發自訂活動。</li> 
	<li>選取 <b>類別庫</b> 從右邊的專案類型清單。</li>
	<li>輸入 <b>MyDotNetActivity</b> 的 <b>名稱</b>。</li> 
	<li>選取 <b>C:\ADFGetStarted</b> 的 <b>位置</b>。</li>
	<li>按一下 [確定]<b></b> 以建立專案。</li>
</ol>
2.  按一下 <b>工具</b>, ，指向 <b>NuGet 封裝管理員</b>, ，然後按一下 <b>Package Manager Console</b>。
3.	在 <b>Package Manager Console</b>, ，執行下列命令以匯入 <b>Microsoft.Azure.Management.DataFactories</b>。 

		Install-Package Microsoft.Azure.Management.DataFactories –Pre

3.	在 <b>Package Manager Console</b>, ，執行下列命令以匯入 <b>Microsoft.DataFactories.Runtime</b>。資料夾以包含下載的資料處理站 NuGet 封裝的位置取代。

		Install-Package Microsoft.DataFactories.Runtime –Pre

4. 匯入專案的 Azure 儲存體 NuGet 封裝中。

		Install-Package Azure.Storage

5. 加入下列 **使用** 陳述式，以在專案中的原始程式檔。

		using System.IO;
		using System.Globalization;
		using System.Diagnostics;
	
		using Microsoft.Azure.Management.DataFactories.Models;
		using Microsoft.DataFactories.Runtime; 
	
		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Blob;
  
6. 變更名稱 **命名空間** 至 **MyDotNetActivityNS**。

		namespace MyDotNetActivityNS

7. 變更類別名稱 **MyDotNetActivity** 和從它衍生出來 **IDotNetActivity** 介面，如下所示。

		public class MyDotNetActivity : IDotNetActivity

8. 實作 (加入) **Execute** 方法 **IDotNetActivity** 介面 **MyDotNetActivity** 類別，並將下列範例程式碼複製到方法。

	 **InputTables** 和 **outputTables** 參數代表之活動的輸入和輸出資料表，如名稱所示。您可以看到您登入使用的訊息 **記錄器** ，您可以從 Azure 入口網站或使用 cmdlet 下載記錄檔中的物件。 **ExtendedProperties** 字典包含您在活動和其值的 JSON 檔案中指定的擴充屬性的清單。

	下列範例程式碼會計算輸入 blob 中的行數，並產生輸出 blob 中的下列內容： blob，blob，在其的活動執行時，目前的日期時間的機器中的行數的路徑。

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
                                        "{0},{1},{2},{3},{4}\n",
                                        folderPath,
                                        inputBlob.Name,
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

9. 新增下列 Helper 方法。 **Execute** 方法會叫用這些協助程式方法。 **GetConnectionString** 方法會擷取 Azure 儲存體連接字串和 **像下面這樣** 方法會擷取 blob 的位置。


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
   


10. 編譯專案。按一下 **建置** 從功能表，然後按一下 **建置方案**。
11. 啟動 **Windows 檔案總管**, ，並瀏覽至 **bin\debug** 或 **bin\release** 資料夾相依類型的組建。
12. 建立 zip 檔案 **MyDotNetActivity.zip** ，其中包含在所有的二進位檔 <project folder>\bin\Debug 資料夾。
13. 上傳 **MyDotNetActivity.zip** 當作 blob 容器的 blob： **customactvitycontainer** 在 Azure blob 儲存體， **MyBlobStore** 連結中的服務 **ADFTutorialDataFactory** 使用。建立 blob 容器 **blobcustomactivitycontainer** 如果不存在。 


## 步驟 2： 在管線中使用自訂活動
以下是您將在此步驟中執行的步驟：

1. 建立連結的服務，以僅限對應的工作將執行的自訂活動的 HDInsight 叢集。 
2. 建立此範例中的管線將會產生輸出資料表。
3. 建立並執行管線，使用您在步驟 1 中建立的自訂活動。 
 
### 建立連結的服務將用來執行自訂活動的 HDInsight 叢集
Azure Data Factory 服務支援建立隨選叢集，並使用它處理輸入來產生輸出資料。您也可以使用自己的叢集執行相同作業。當您使用隨選 HDInsight 叢集時，系統會為每個配量建立叢集。然而，如果您使用自己的 HDInsight 叢集，叢集已經準備好立即處理配量。因此，在使用隨選叢集時，可能無法像使用自己的叢集那麼快看到輸出資料。

> [AZURE.NOTE]在執行階段，.NET 活動的執行個體只節點上執行一個背景工作在 HDInsight 叢集。它無法縮放在多個節點上執行。.NET 活動的多個執行個體可以平行執行 HDInsight 叢集的不同節點上。

如果您延伸 [開始使用 Azure 資料 Factory][adfgetstarted] 教學課程與逐步解說中，從 [使用 Pig 和 Hive 與 Azure 資料處理站][hivewalkthrough], ，您可以略過建立此連結的服務，並使用 ADFTutorialDataFactory 中已經有連結的服務。


#### 若要使用隨選 HDInsight 叢集

1. 在 **Azure 入口網站**, ，按一下 **作者和部署** 資料 Factory 首頁中。
2. 在資料處理站編輯器] 中，按一下 **新計算** 從命令列，然後選取 **隨選 HDInsight 叢集** 從功能表。
2. 依下列方式在 JSON 指令碼中： 
	1. 針對 **clusterSize** 屬性，指定 HDInsight 叢集的大小。
	2. 針對 **jobsContainer** 屬性，指定將儲存的叢集記錄檔的預設容器的名稱。基於本教學課程的目的指定 **adfjobscontainer**。
	3. 針對 **timeToLive** 屬性，指定多久之前將會刪除該客戶可以處於閒置狀態。 
	4. 針對 **版本** 屬性，指定您想要使用的 HDInsight 版本。如果您排除此屬性時，會使用最新版本。  
	5. 針對 **linkedServiceName**, ，指定 **StorageLinkedService** 您必須建立在 Get 入門教學課程。 

			{
		    	"name": "HDInsightOnDemandLinkedService",
				    "properties": {
		    	    "type": "HDInsightOnDemandLinkedService",
		    	    "clusterSize": "4",
		    	    "jobsContainer": "adfjobscontainer",
		    	    "timeToLive": "00:05:00",
		    	    "version": "3.1",
		    	    "linkedServiceName": "StorageLinkedService"
		    	}
			}

2. 按一下 **部署** 命令列上的部署連結的服務。
   
#### 若要使用您自己的 HDInsight 叢集： 

1. 在 **Azure 入口網站**, ，按一下 **作者和部署** 資料 Factory 首頁中。
2. 在 **資料 Factory 編輯器**, ，按一下 **新計算** 從命令列，然後選取 **HDInsight 叢集** 從功能表。
2. 依下列方式在 JSON 指令碼中： 
	1. 針對 **clusterUri** 屬性中，輸入您的 HDInsight 的 URL。例如： https://<clustername>.azurehdinsight.net/     
	2. 針對 **UserName** 屬性中，輸入能夠存取 HDInsight 叢集的使用者名稱。
	3. 針對 **密碼** 屬性中，輸入使用者的密碼。 
	4. 針對 **LinkedServiceName** 屬性中，輸入 **StorageLinkedService**。這是您必須在入門教學課程中建立連結的服務。 

2. 按一下 **部署** 命令列上的部署連結的服務。

### 建立輸出資料表

1. 在 **資料 Factory 編輯器**, ，按一下 **新的資料集**, ，然後按一下 **Azure Blob 儲存體** 從命令列。
2. 在右窗格中的 JSON 指令碼取代成下列 JSON 指令碼：

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


 	輸出位置是 **為 YYYYMMDDHH adftutorial/customactivityoutput/** 何處為 YYYYMMDDHH 是年、 月、 日和小時所產生的配量。請參閱 [開發人員參考][adf-developer-reference] 如需詳細資訊。

2. 按一下 **部署** 命令列部署資料表上。


### 建立及執行管線，使用自訂活動
   
1. 在資料處理站編輯器] 中，按一下 **新管線** 命令列上。如果看不到命令，按一下 [ **...(省略符號)** 若要查看它。 
2. 在右窗格中的 JSON 取代成下列 JSON 指令碼。如果您想要使用您自己的叢集，並遵循步驟來建立 **HDInsightLinkedService** 連結服務，取代 **HDInsightOnDemandLinkedService** 與 **HDInsightLinkedService** 下列 JSON 中。 

		{
    		"name": "ADFTutorialPipelineCustom",
    		"properties":
    		{
        		"description" : "Use custom activity",
        		"activities":
        		[
					{
                		"Name": "MyDotNetActivity",
                     	"Type": "DotNetActivity",
                     	"Inputs": [{"Name": "EmpTableFromBlob"}],
                     	"Outputs": [{"Name": "OutputTableForCustom"}],
						"LinkedServiceName": "HDInsightLinkedService",
                     	"Transformation":
                     	{
                        	"AssemblyName": "MyDotNetActivity.dll",
                            "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
                            "PackageLinkedService": "MyBlobStore",
                            "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",
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
        		],
				"start": "2015-02-13T00:00:00Z",
        		"end": "2015-02-14T00:00:00Z",
        		"isPaused": false
			}
		}

	> [AZURE.NOTE]取代 **StartDateTime** 值與目前日期之前三天和 **EndDateTime** 值與目前的日期。StartDateTime 和 EndDateTime 必須在 [ISO 格式](http://en.wikipedia.org/wiki/ISO_8601)。例如： 2014年-10-14T16:32:41Z。將輸出資料表排定為每天產生，因此將產生三個配量。

	請注意：

	- [活動] 區段中有一個活動，而且它的類型是： **DotNetActivity**。
	- 使用相同的輸入的資料表 **EmpTableFromBlob** 用於 Get 入門教學課程。
	- 使用新的輸出資料表 **OutputTableForCustom** 您將在下一個步驟中建立。
	- **AssemblyName** 會設定為 DLL 的名稱： **MyActivities.dll**。
	- **EntryPoint** 設為 **MyDotNetActivityNS.MyDotNetActivity**。
	- **PackageLinkedService** 設為 **MyBlobStore** 從教學課程所建立 [開始使用 Azure 資料 Factory][adfgetstarted]。此 Blob 存放區包含自訂活動 zip 檔案。
	- **PackageFile** 設為 **customactivitycontainer/MyDotNetActivity.zip**。
     
4. 按一下 **部署** 命令列來部署管線。
8. 確認輸出檔案會產生 blob 儲存體中 **adftutorial** 容器。

	![自訂活動的輸出][image-data-factory-ouput-from-custom-activity]

9. 如果您開啟輸出檔，您應該會看到類似下面的輸出：
	
	adftutorial/,emp.txt,2,WORKERNODE0,03/27/2015 19:23:28

	(blob 位置) (blob 的名稱)，(blob 中的行數)，(節點在其執行的活動)，(日期時間戳記)

10.	使用 [Azure 入口網站][azure-preview-portal] 或 Azure PowerShell cmdlet 來監視您的資料處理站、 管線和資料集。您可以看到訊息從 **ActivityLogger** 記錄檔中的自訂活動的程式碼中您可以從入口網站或使用 cmdlet 下載。

	![從自訂活動下載記錄檔][image-data-factory-download-logs-from-custom-activity]
   
請參閱 [開始使用 Azure 資料 Factory][adfgetstarted] 的監視資料集和管線的詳細步驟。
    
## <a name="AzureBatch"></a> 使用 Azure 的批次連結服務 
> [AZURE.NOTE]請參閱 [Azure 批次技術概觀][batch-technical-overview] 概觀 Azure 批次的服務，請參閱 [開始使用 Azure 批次 Library for.NET][batch-get-started] 來快速地開始使用 Azure 批次服務。

在上一節中所述的逐步解說中使用 Azure 批次連結服務的高階步驟如下：

1. 建立 Azure 批次帳戶使用 Azure 管理入口網站。請參閱 [Azure 批次技術概觀][batch-create-account] 指示的發行項。記下 Azure 批次帳戶名稱和帳戶金鑰。 

	您也可以使用 [新增 AzureBatchAccount][new-azure-batch-account] 指令程式可建立 Azure 批次帳戶。請參閱 [使用 Azure PowerShell 管理 Azure 批次帳戶][azure-batch-blog] 如需有關使用這個指令程式的詳細指示。 
2. 建立 Azure 批次集區。您可以下載並使用 [Azure 批次 Explorer 工具][batch-explorer] (或) 使用 [Azure 批次 Library for.NET][batch-net-library] 建立 Azure 批次集區。請參閱 [Azure 批次檔案總管範例逐步解說][batch-explorer-walkthrough] 如需使用 Azure 批次檔案總管的逐步指示。
	
	您也可以使用 [新增 AzureBatchPool][new-azure-batch-pool] 指令程式可建立 Azure 批次集區。

2. 建立 Azure 批次連結服務使用下列 JSON 範本。資料處理站編輯器會顯示類似的範本讓您開始使用。JSON 片段中指定的 Azure 批次帳戶名稱、 帳戶金鑰和集區名稱。

		{
		    "name": "AzureBatchLinkedService",
		    "properties": {
		        "type": "AzureBatchLinkedService",
		        "accountName": "<Azure Batch account name>",
		        "accessKey": "<Azure Batch account key>",
		        "poolName": "<Azure Batch pool name>",
		        "linkedServiceName": "<Specify associated storage linked service reference here>"
		  }
		}

	請參閱 [Azure 批次連結服務 MSDN 主題](https://msdn.microsoft.com/library/mt163609.aspx) 如需這些屬性的描述。

2.  在資料處理站編輯器] 中，開啟您在逐步解說和取代建立管線的 JSON 定義 **HDInsightLinkedService** 與 **AzureBatchLinkedService**。
3.  若要變更管線的開始和結束時間，讓您能夠測試案例與 Azure 批次服務。 
4.  您可以看到與下圖所示處理 Azure 批次 [總管] 中的扇區相關聯的 Azure 批次工作。

	![Azure 的批次工作][image-data-factory-azure-batch-tasks]

## 另請參閱

[Azure 資料 Factory 更新： 使用 Azure 的批次執行 ADF 自訂.NET 活動](http://azure.microsoft.com/blog/2015/05/01/azure-data-factory-updates-execute-adf-custom-net-activities-using-azure-batch/)。

[batch-net-library]: ../batch/batch-dotnet-get-started.md
[batch-explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[batch-explorer-walkthrough]: http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx
[batch-create-account]: ../batch/batch-technical-overview.md/#batch-concepts
[batch-technical-overview]: ../batch/batch-technical-overview.md
[batch-get-started]: ../batch/batch-dotnet-get-started.md
[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[use-pig-and-hive-with-data-factory]: data-factory-pig-hive-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md
[azure-powershell-install]: https://github.com/Azure/azure-sdk-tools/releases


[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

[new-azure-batch-account]: https://msdn.microsoft.com/library/mt125880.aspx
[new-azure-batch-pool]: https://msdn.microsoft.com/library/mt125936.aspx
[azure-batch-blog]: http://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx

[nuget-package]: http://go.microsoft.com/fwlink/?LinkId=517478
[azure-developer-center]: http://azure.microsoft.com/develop/net/
[adf-developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[azure-preview-portal]: https://portal.azure.com/

[adfgetstarted]: data-factory-get-started.md
[hivewalkthrough]: data-factory-pig-hive-activities.md

[image-data-factory-ouput-from-custom-activity]: ./media/data-factory-use-custom-activities/OutputFilesFromCustomActivity.png

[image-data-factory-download-logs-from-custom-activity]: ./media/data-factory-use-custom-activities/DownloadLogsFromCustomActivity.png

[image-data-factory-azure-batch-tasks]: ./media/data-factory-use-custom-activities/AzureBatchTasks.png

<!---HONumber=GIT-SubDir--> 