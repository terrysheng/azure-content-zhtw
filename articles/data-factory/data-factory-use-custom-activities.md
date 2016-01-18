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
	ms.date="01/05/2016"
	ms.author="spelluru"/>

# 在 Azure 資料處理站管線中使用自訂活動
Azure Data Factory 支援在管線中使用內建活動來移動和處理資料，例如**複製活動**和 **HDInsight 活動**。您也可以使用您自己的轉換/處理邏輯建立自訂的 .Net 活動，並在管線中使用該活動。您可以將活動設定為使用 **Azure HDInsight** 叢集或 **Azure Batch** 服務來執行。

本文說明如何建立自訂活動，並在 Azure 資料處理站管線中使用它。本文也提供建立及使用自訂活動的詳細逐步解說與逐步指示。本逐步解說會使用 HDInsight 連結服務。若要改用 Azure Batch 連結服務，您需要建立 **AzureBatch** 類型的連結服務，並將它用於管線 JSON 的活動區段 (**linkedServiceName**)。如需有關使用 Azure Batch 搭配自訂活動的詳細資訊，請參閱 [Azure Batch 連結服務](#AzureBatch)一節。


## <a name="walkthrough" /> 逐步解說：
本逐步解說為您提供建立自訂活動以及在 Azure Data Factory 管線中使用該活動的逐步指示。本逐步解說延伸[開始使用 Azure Data Factory][adfgetstarted] 中的教學課程。如果您想要看自訂活動運作，您需要先依照入門教學課程操作，然後執行此逐步解說。

### 必要條件


- [開始使用 Azure Data Factory][adfgetstarted] 中的教學課程。您必須完成本文中的教學課程，再執行本逐步解說。
- Visual Studio 2012 或 2013
- 下載並安裝 [Azure .NET SDK][azure-developer-center]
- 下載並安裝 [Azure Data Factory 的 NuGet 封裝](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactories/)最新版本。逐步解說中包含相關指示。
- 下載並安裝 Azure 儲存體的 NuGet 封裝。逐步解說中有指示，因此您可以略過此步驟。

### 高階步驟 
1.	**建立自訂活動** 以在 Data Factory 解決方案中使用。自訂活動包含資料處理邏輯。 
	1.	在 Visual Studio (或選擇的程式碼編輯器) 中，建立 .NET 類別庫專案，加入程式碼以處理輸入的資料，並編譯專案。	
	2.	壓縮輸出資料夾中所有的二進位檔和 PDB (選擇性) 檔案。	
	3.	將 zip 檔案上傳至 Azure Blob 儲存體。在建立自訂活動一節中有詳細的步驟。 
2. **建立使用自訂活動的 Azure Data Factory**：
	1. 建立 Azure Data Factory。
	2. 建立連結的服務。
		1. StorageLinkedService：提供用於存取 blob 的儲存體認證。
		2. HDInsightLinkedService：指定做為計算的 Azure HDInsight。
	3. 建立資料集。
		1. InputDataset：指定輸入 blob 的儲存體容器和資料夾。
		1. OuputDataset：指定輸出 blob 的儲存體容器和資料夾。
	2. 建立使用自訂活動的管線。
	3. 執行與測試管線。
	4. 偵錯管線。

## 建立自訂活動
若要建立您可以在 Azure Data Factory 管線中使用的 .NET 自訂活動，您必須利用實作 **IDotNetActivity** 介面的類別建立 **.NET 類別庫**專案。這個介面只有一個方法：執行。以下是該方法的簽章：

	public IDictionary<string, string> Execute(
            IEnumerable<LinkedService> linkedServices, 
            IEnumerable<Dataset> datasets, 
            Activity activity, 
            IActivityLogger logger)
        
此方法有幾個您必須了解的關鍵元件。

- 此方法會採用四個參數：
	- **linkedServices**。這是將輸入/輸出資料來源 (例如：Azure Blob 儲存體) 連結到 Data Factory 的連結服務列舉清單。在此範例中，只有一個用於輸入和輸出的 Azure 儲存體類型連結服務。 
	- **資料集**。這是資料集的可列舉清單。您可以使用這個參數取得輸入和輸出資料集定義的位置和結構描述。
	- **activity**。這個參數代表目前的計算實體 - 在此情況下為 Azure HDInsight。
	- **logger**。記錄器可讓您撰寫會呈現為管線的「使用者」記錄檔的偵錯註解。 

- 此方法會傳回可用來將自訂活動鏈結在一起的字典。我們不會在此範例解決方案中使用這項功能。

### 程序： 
1.	建立 .NET 類別庫專案。
	<ol type="a">
	<li>啟動 <b>Visual Studio 2012</b> 或 <b>Visual Studio 2013</b>。</li>
	<li>按一下 [<b>檔案</b>]，指向 [<b>新增</b>]，然後按一下 [<b>專案</b>]。</li>
	<li>展開 [範本]<b></b>，然後選取 [Visual C#]<b></b>。在此逐步解說中，您使用 C# 中，但您可以使用任何 .NET 語言來開發自訂活動。</li>
	<li>從右邊的專案類型清單中選取 [類別庫]<b></b>。</li>
	<li>針對 [名稱]<b></b> 輸入 <b>MyDotNetActivity</b>。</li>
	<li>在 [<b>位置</b>] 中選取 <b>C:\ADFGetStarted</b>。</li>
	<li>按一下 [確定]<b></b> 以建立專案。</li>
</ol>
2.  按一下 [<b>工具</b>]，指向 [<b>NuGet 封裝管理員</b>]，然後按一下 [<b>封裝管理員主控台</b>]。
3.	在 [封裝管理員主控台]<b></b> 中，執行下列命令匯入 <b>Microsoft.Azure.Management.DataFactories</b>。

		Install-Package Microsoft.Azure.Management.DataFactories

4. 將 Azure 儲存體 NuGet 封裝匯入專案。

		Install-Package Azure.Storage

5. 將下列 **using** 陳述式加入至專案的原始程式檔。

		using System.IO;
		using System.Globalization;
		using System.Diagnostics;
		using System.Linq;

		using Microsoft.Azure.Management.DataFactories.Models;
		using Microsoft.Azure.Management.DataFactories.Runtime;

		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Blob;

6. 將 **namespace** 的名稱變更為 **MyDotNetActivityNS**。

		namespace MyDotNetActivityNS

7. 將類別的名稱變更為 **MyDotNetActivity**，並從 **IDotNetActivity** 介面延伸它，如下所示。

		public class MyDotNetActivity : IDotNetActivity

8. 對 **MyDotNetActivity** 類別實作 (加入) **IDotNetActivity** 介面的 **Execute** 方法，並將下列範例程式碼複製到方法。

	下列範例程式碼會計算輸入 Blob 中的行數，並在輸出 Blob 中產生下列內容：Blob 的路徑、Blob 中的行數、執行活動的電腦、目前的日期時間。

		/// <summary>
        /// Execute method is the only method of IDotNetActivity interface you must implement. 
        /// In this sample, the method invokes the Calculate method to perform the core logic.  
		/// </summary>

        public IDictionary<string, string> Execute(
            IEnumerable<LinkedService> linkedServices,
            IEnumerable<Dataset> datasets,
            Activity activity,
            IActivityLogger logger)
        {
			// to get extended properties (for example: SliceStart)
			DotNetActivity dotNetActivity = (DotNetActivity)activity.TypeProperties;
            string sliceStartString = dotNetActivity.ExtendedProperties["SliceStart"];

			// to log all extended properties			
			IDictionary<string, string> extendedProperties = dotNetActivity.ExtendedProperties;
			logger.Write("Logging extended properties if any...");
			foreach (KeyValuePair<string, string> entry in extendedProperties)
			{
				logger.Write("<key:{0}> <value:{1}>", entry.Key, entry.Value);
			}
		

            // declare types for input and output data stores
            AzureStorageLinkedService inputLinkedService;

            // declare dataset types
            CustomDataset inputLocation;
            AzureBlobDataset outputLocation;

            Dataset inputDataset = datasets.Single(dataset => dataset.Name == activity.Inputs.Single().Name);
            inputLocation = inputDataset.Properties.TypeProperties as CustomDataset;

            foreach (LinkedService ls in linkedServices)
                logger.Write("linkedService.Name {0}", ls.Name);

            // using First method instead of Single since we are using the same 
            // Azure Storage linked service for input and output. 
            inputLinkedService = linkedServices.First(
                linkedService =>
                linkedService.Name ==
                inputDataset.Properties.LinkedServiceName).Properties.TypeProperties
                as AzureStorageLinkedService;

            string connectionString = inputLinkedService.ConnectionString; // To create an input storage client.
            string folderPath = GetFolderPath(inputDataset);
            string output = string.Empty; // for use later.

            // create storage client for input. Pass the connection string.
            CloudStorageAccount inputStorageAccount = CloudStorageAccount.Parse(connectionString);
            CloudBlobClient inputClient = inputStorageAccount.CreateCloudBlobClient();

            // initialize the continuation token before using it in the do-while loop.
            BlobContinuationToken continuationToken = null;
            do
            {   // get the list of input blobs from the input storage client object.
                BlobResultSegment blobList = inputClient.ListBlobsSegmented(folderPath,
                                         true,
                                         BlobListingDetails.Metadata,
                                         null,
                                         continuationToken,
                                         null,
                                         null);
                
                // Calculate method returns the number of occurrences of 
                // the search term (“Microsoft”) in each blob associated
       			// with the data slice. 
        		// 
        	    // definition of the method is shown in the next step.
 
                output = Calculate(blobList, logger, folderPath, ref continuationToken, "Microsoft");

            } while (continuationToken != null);

            // get the output dataset using the name of the dataset matched to a name in the Activity output collection.
            Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);
            // convert to blob location object.
            outputLocation = outputDataset.Properties.TypeProperties as AzureBlobDataset;

            folderPath = GetFolderPath(outputDataset);

            logger.Write("Writing blob to the folder: {0}", folderPath);

            // create a storage object for the output blob.
            CloudStorageAccount outputStorageAccount = CloudStorageAccount.Parse(connectionString);
            // write the name of the file. 
            Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));

            logger.Write("output blob URI: {0}", outputBlobUri.ToString());
            // create a new blob and upload the output text.
            CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
            logger.Write("Writing {0} to the output blob", output);
            outputBlob.UploadText(output);

            // return a new Dictionary object (unused in this code).
            return new Dictionary<string, string>();
        }

9. 新增下列 Helper 方法。**Execute** 方法會叫用這些 Helper 方法。**GetConnectionString** 方法會擷取 Azure 儲存體連接字串，**GetFolderPath** 方法會擷取 Blob 位置。最重要的是，**Calculate** 方法會隔離逐一查看每個 blob 的程式碼。

        /// <summary>
        /// Gets the folderPath value from the input/output dataset.   
		/// </summary>

		private static string GetFolderPath(Dataset dataArtifact)
        {
            if (dataArtifact == null || dataArtifact.Properties == null)
            {
                return null;
            }

            AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
            if (blobDataset == null)
            {
                return null;
            }

            return blobDataset.FolderPath;
        }

        /// <summary>
        /// Gets the fileName value from the input/output dataset.   
        /// </summary>

        private static string GetFileName(Dataset dataArtifact)
        {
            if (dataArtifact == null || dataArtifact.Properties == null)
            {
                return null;
            }

            AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
            if (blobDataset == null)
            {
                return null;
            }

            return blobDataset.FileName;
        }

        /// <summary>
        /// Iterates through each blob (file) in the folder, counts the number of instances of search term in the file, 
        /// and prepares the output text that will be written to the output blob. 
        /// </summary>

        public static string Calculate(BlobResultSegment Bresult, IActivityLogger logger, string folderPath, ref BlobContinuationToken token, string searchTerm)
        {
            string output = string.Empty;
            logger.Write("number of blobs found: {0}", Bresult.Results.Count<IListBlobItem>());
            foreach (IListBlobItem listBlobItem in Bresult.Results)
            {
                CloudBlockBlob inputBlob = listBlobItem as CloudBlockBlob;
                if ((inputBlob != null) && (inputBlob.Name.IndexOf("$$$.$$$") == -1))
                {
                    string blobText = inputBlob.DownloadText(Encoding.ASCII, null, null, null);
                    logger.Write("input blob text: {0}", blobText);
                    string[] source = blobText.Split(new char[] { '.', '?', '!', ' ', ';', ':', ',' }, StringSplitOptions.RemoveEmptyEntries);
                    var matchQuery = from word in source
                                     where word.ToLowerInvariant() == searchTerm.ToLowerInvariant()
                                     select word;
                    int wordCount = matchQuery.Count();
                    output += string.Format("{0} occurrences(s) of the search term "{1}" were found in the file {2}.\r\n", wordCount, searchTerm, inputBlob.Name);
                }
            }
            return output;
        }

	GetFolderPath 方法會將路徑傳回資料集所指向的資料夾，而 GetFileName 方法會傳回資料集指向的 blob/檔案名稱。
	
		    "name": "InputDataset",
		    "properties": {
		        "type": "AzureBlob",
		        "linkedServiceName": "StorageLinkedService",
		        "typeProperties": {
		            "fileName": "file.txt",
		            "folderPath": "mycontainer/inputfolder/",
	
	Calculate 方法會在輸入檔案 (資料夾中的 blob) 計算關鍵字 Microsoft 的執行個體數目。搜尋詞彙 ("Microsoft") 已在程式碼中硬式編碼。

10. 編譯專案。按一下功能表中的 [建置]，然後按一下 [建置方案]。
11. 啟動「Windows 檔案總管」，瀏覽至 **bin\\debug** 或 **bin\\release** 資料夾，根據建置類型而定。
12. 建立 zip 檔案 **MyDotNetActivity.zip**，檔案中包含 <project folder>\\bin\\Debug 資料夾中的所有二進位檔。您可能會想加入 **MyDotNetActivity.pdb** 檔案，讓您可以取得額外的詳細資訊，例如在失敗時，原始程式碼中引起問題的程式碼行號。

	![二進位輸出檔案](./media/data-factory-use-custom-activities/Binaries.png)
13. 將 **MyDotNetActivity.zip** 當做 Blob 上傳至 Blob 容器：Azure Blob 儲存體中的 **customactvitycontainer**，由 **ADFTutorialDataFactory** 中的 **StorageLinkedService** 連結服務使用。如果不存在 Blob 容器 **customactivitycontainer**，請自行建立。

> [AZURE.NOTE]如果您將這個 .NET 活動專案加入 Visual Studio 中包含 Data Factory 專案的方案，您就不需要執行最後兩個步驟，也就是建立 zip 檔案，和手動上傳到 Azure Blob 儲存體。當您使用 Visual Studio 發佈 Data Factory 實體時，發佈程序會自動完成這些步驟。請參閱[使用 Visual Studio 建置您的第一個管線](data-factory-build-your-first-pipeline-using-vs.md)和[從 Azure Blob 複製資料到 Azure SQL](data-factory-get-started-using-vs.md)一文，以了解如何使用 Visual Studio 建立並發佈 Data Factory 實體。

### 執行方法

本節提供 **Execute** 方法中與程式碼相關的詳細資料和注意事項。
 
1. 逐一查看輸入集合的成員可在 [Microsoft.WindowsAzure.Storage.Blob](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.aspx) 命名空間中找到。逐一查看 blob 集合需要使用 **BlobContinuationToken** 類別。基本上，您必須搭配使用 do-while 迴圈和權杖做為結束迴圈的機制。如需詳細資訊，請參閱[如何從 .NET 使用 Blob 儲存體](../storage/storage-dotnet-how-to-use-blobs.md)。基本迴圈如下所示：

		// Initialize the continuation token.
		BlobContinuationToken continuationToken = null;
		do
		{   
			// Get the list of input blobs from the input storage client object.
			BlobResultSegment blobList = inputClient.ListBlobsSegmented(folderPath,
    					              true,
    	                              BlobListingDetails.Metadata,
    	                              null,
    	                              continuationToken,
    	                              null,
    	                              null);
			// Return a string derived from parsing each blob.
    		output = Calculate(blobList, logger, folderPath, ref continuationToken, "Microsoft");
		} while (continuationToken != null);

	請參閱 [ListBlobsSegmented](https://msdn.microsoft.com/library/jj717596.aspx) 方法的文件以了解詳細資料。

2.	以邏輯方式逐一查看 blob 集的程式碼會在 do-while 迴圈中執行。在 **Execute** 方法中，執行 do-while 迴圈會將 blob 清單傳遞至名為 **Calculate** 的方法。此方法會傳回名為 **output** 的字串變數，也就是在區段中逐一查看所有 blob 的結果。

	它會在傳遞給 **Calculate** 方法的 blob 中，傳回搜尋詞彙 (**Microsoft**) 的出現次數。

			output += string.Format("{0} occurrences of the search term "{1}" were found in the file {2}.\r\n", wordCount, searchTerm, inputBlob.Name);

3.	一旦 **Calculate** 方法完成此工作，它必須寫入至新的 blob。因此對於每個處理過的 blob 集，都可以利用結果撰寫新的 blob。若要寫入新的 blob，請先找到輸出資料集。

			// Get the output dataset using the name of the dataset matched to a name in the Activity output collection.
			Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);

			// Convert to blob location object.
			outputLocation = outputDataset.Properties.TypeProperties as AzureBlobDataset;

4.	程式碼也會呼叫 helper 方法：**GetFolderPath** 來擷取資料夾路徑 (儲存體容器名稱)。
 
			folderPath = GetFolderPath(outputDataset);

	**GetFolderPath** 會將 DataSet 物件轉換成 AzureBlobDataSet，其具有一個名為 FolderPath 的屬性。
			
			AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
			
			return blobDataset.FolderPath;

5.	程式碼會呼叫 **GetFileName** 方法來擷取檔案名稱 (blob 名稱)。程式碼取得資料夾路徑的方式類似上述程式碼。

			AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;

			return blobDataset.FileName;

6.	藉由建立新的 URI 物件寫入檔案的名稱。URI 建構函式使用 **BlobEndpoint** 屬性傳回容器名稱。新增資料夾路徑和檔案名稱以建構輸出 blob URI。

			// Write the name of the file. 
			Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));

7.	已寫入檔案名稱，現在您可以從 Calculate 方法將輸出字串寫入新的 blob：

			// Create a new blob and upload the output text.
			CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
			logger.Write("Writing {0} to the output blob", output);
			outputBlob.UploadText(output);

## 建立 Data Factory

在 [建立自訂活動] 區段中，您建立自訂活動，並將包含二進位檔和 PDB 檔案的 zip 檔案上傳到 Azure blob 容器。在本節中，您將透過使用**自訂活動**的**管線**建立 Azure **Data Factory**。
 
自訂活動的輸入資料集代表 blob 儲存體中輸入資料夾 (mycontainer\\inputfolder) 的 blob (檔案)。活動的輸出資料集代表 blob 儲存體中輸出資料夾 (mycontainer\\outputfolder) 的輸出 blob。

利用下列內容建立名為 file.txt 的檔案並將它上傳至 mycontainer\\inputfolder (mycontainer 是 Azure blob 容器的名稱而 inputfolder 是該容器中的資料夾名稱)。

	test custom activity Microsoft test custom activity Microsoft

即使資料夾有 2 個以上的檔案，輸入資料夾還是會對應至 Azure Data Factory 中的配量。管線處理每個配量時，自訂活動會為該配量逐一查看輸入資料夾中的所有 blob。

您會看到 mycontainer\\output 資料夾中的一個輸出檔案具有 1 行或更多行 (和輸入資料夾中的 blob 數目相同)：
 
	2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.


以下是您將在此節中執行的步驟：

1. 建立 **Data Factory**。
2. 建立 HDInsight 叢集的**連結服務**，自訂活動將在其中以僅限對應的作業執行，並建立保留輸入/輸出 blob 的 Azure 儲存體。 
2. 建立輸入和輸出**資料集**，代表自訂活動的輸入和輸出。 
3. 建立並執行使用自訂活動的**管線**。

### 步驟 1：建立 Data Factory

1.	登入 Azure 入口網站之後，執行下列動作：
	1.	按一下左側功能表上的 [新增]。
	2.	按一下 [新增] 刀鋒視窗中的 [資料 + 分析]。
	3.	按一下 [資料分析] 刀鋒視窗上的 [Data Factory]。
2.	在 [新增 Data Factory] 刀鋒視窗中，輸入 **CustomActivityFactory** 做為 [名稱]。Azure Data Factory 的名稱在全域必須是唯一的。如果您收到錯誤：**Data Factory 名稱 “CustomActivityFactory” 無法使用**，請變更 Data Factory 名稱 (例如 **yournameCustomActivityFactory**)，然後試著重新建立。
3.	按一下 [資源群組名稱]，並選取現有的資源群組，或建立一個新群組。 
4.	請確認您使用的是要在其中建立 Data Factory 的正確**訂用帳戶**和**區域**。 
5.	按一下 [新增 Data Factory] 刀鋒視窗上的 [建立]。
6.	您會看到 Data Factory 建立在 Azure 入口網站的 [儀表板] 中。
7.	在 Data Factory 成功建立後，您會看到 Data Factory 刀鋒視窗，顯示 Data Factory 的內容。

### 步驟 2：建立連結服務

連結服務會將資料存放區或計算服務連結至 Azure Data Factory。在此步驟中，您將您的 Azure 儲存體帳戶和 Azure HDInsight 叢集連結到您的 Data Factory。

#### 建立 Azure 儲存體連結服務

1.	按一下 **CustomActivityFactory** 的 **DATA FACTORY** 刀鋒視窗上的 [作者和部署] 磚。這會啟動 Data Factory 編輯器。
2.	在命令列上按一下 [新增資料儲存區]，然後選擇 [Azure 儲存體]。在編輯器中，您應該會看到用來建立 Azure 儲存體連結服務的 JSON 指令碼。
3.	使用您的 Azure 儲存體帳戶名稱取代**帳戶名稱**，並使用 Azure 儲存體帳戶的存取金鑰取代**帳戶金鑰**。若要了解如何取得儲存體存取金鑰，請參閱[檢視、複製和重新產生儲存體存取金鑰](../storage/storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys)
4.	按一下命令列上的 [部署]，部署連結服務。


#### 建立 Azure HDInsight 連結服務 
Azure Data Factory 服務支援建立隨選叢集，並使用它處理輸入來產生輸出資料。您也可以使用自己的叢集執行相同作業。當您使用隨選 HDInsight 叢集時，系統會為每個配量建立叢集。然而，如果您使用自己的 HDInsight 叢集，叢集已經準備好立即處理配量。因此，在使用隨選叢集時，可能無法像使用自己的叢集那麼快看到輸出資料。

> [AZURE.NOTE]在執行階段，.NET 活動的執行個體只在 HDInsight 叢集的一個背景工作角色節點上執行，無法擴展到多個節點上執行。.NET 活動的多個執行個體可以在 HDInsight 叢集的不同節點上平行執行。

如果您已透過[搭配 Azure Data Factory 使用 Pig 和 Hive][hivewalkthrough] 的逐步解說來延伸[開始使用 Azure Data Factory][adfgetstarted] 的教學課程，則可以略過建立此連結服務，並使用 ADFTutorialDataFactory 中已有的連結服務。


##### 若要使用隨選 HDInsight 叢集

1. 在「Azure 入口網站」，按一下 Data Factory 首頁中的 [製作和部署]。
2. 在 Data Factory 編輯器中，從命令列按一下 [新增計算]，然後從功能表選取 [隨選 HDInsight 叢集]。
2. 在 JSON 指令碼中，執行下列動作：
	1. 在 **clusterSize** 屬性中，指定 HDInsight 叢集的大小。
	3. 在 **timeToLive** 屬性中，指定客戶閒置多久之後會被刪除。
	4. 在 **version** 屬性中，指定您要使用的 HDInsight 版本。如果您排除此屬性，則會使用最新版本。  
	5. 在 **linkedServiceName** 中，指定您在「開始使用」教學課程中建立的 **StorageLinkedService**。

			{
			  "name": "HDInsightOnDemandLinkedService",
			  "properties": {
			    "type": "HDInsightOnDemand",
			    "typeProperties": {
			      "clusterSize": "1",
			      "timeToLive": "00:05:00",
			      "version": "3.2",
			      "linkedServiceName": "StorageLinkedService"
			    }
			  }
			}

2. 按一下命令列上的 [部署]，部署連結服務。

##### 若要使用您自己的 HDInsight 叢集：

1. 在「Azure 入口網站」，按一下 Data Factory 首頁中的 [製作和部署]。
2. 在 [Data Factory 編輯器] 中，從命令列按一下 [新增計算]，然後從功能表選取 [HDInsight 叢集]。
2. 在 JSON 指令碼中，執行下列動作：
	1. 在 **clusterUri** 屬性中，輸入您的 HDInsight 的 URL。例如：https://<clustername>.azurehdinsight.net/     
	2. 在 **UserName** 屬性中，輸入具有 HDInsight 叢集存取權的使用者名稱。
	3. 在 **Password** 屬性中，輸入使用者的密碼。
	4. 在 **LinkedServiceName** 屬性中，輸入 **StorageLinkedService**。這是您在「開始使用」教學課程中建立的連結服務。

2. 按一下命令列上的 [部署]，部署連結服務。

### 步驟 3：建立資料集
在此步驟中，您將建立資料集來代表輸入和輸出資料。

#### 建立輸入資料集
1.	在 Data Factory 的**編輯器**中，按一下工具列上的 [**新增資料集**] 按鈕，然後從下拉式功能表中選取 [**Azure Blob 儲存體**]。
2.	使用下列 JSON 程式碼片段取代右窗格中的 JSON：

			{
			    "name": "InputDataset",
			    "properties": {
			        "type": "AzureBlob",
			        "linkedServiceName": "StorageLinkedService",
			        "typeProperties": {
			            "folderPath": "adftutorial/customactivityinput/",
			            "format": {
			                "type": "TextFormat"
			            }
			        },
			        "availability": {
			            "frequency": "Hour",
			            "interval": 1
			        },
			        "external": true,
			        "policy": {}
			    }
			}

	您稍後將在本逐步解說建立管線，開始時間為：2015-11-16T00:00:00Z，而結束時間為：2015-11-16T05:00:00Z。排程為每小時產生，將會有 5 個輸入/輸出配量 (在 **00**:00:00 -> **05**:00:00 之間)。

	輸入資料集的**頻率**和**間隔**設定為**小時**和 **1**，這表示每小時皆可使用輸入配量。在此範例中，它是 intputfolder 中的相同檔案 (file.txt)。

	以下是每個配量的開始時間，由上述 JSON 程式碼片段中的 SliceStart 系統變數代表。

	
3.	按一下工具列的 [部署]，建立並部署 **InputDataset**。確認您在編輯器的標題列看到 [已成功建立資料表] 訊息。


#### 建立輸出資料表

1. 在 [Data Factory 編輯器] 中，按一下 [新增資料集]，然後從命令列按一下 [Azure Blob 儲存體]。
2. 使用下列 JSON 指令碼取代右窗格中的 JSON 指令碼：

		{
		    "name": "OutputDataset",
		    "properties": {
		        "type": "AzureBlob",
		        "linkedServiceName": "StorageLinkedService",
		        "typeProperties": {
		            "fileName": "{slice}.txt",
		            "folderPath": "adftutorial/customactivityoutput",
		            "partitionedBy": [
		                {
		                    "name": "slice",
		                    "value": {
		                        "type": "DateTime",
		                        "date": "SliceStart",
		                        "format": "yyyy-MM-dd-HH"
		                    }
		                }
		            ]
		        },
		        "availability": {
		            "frequency": "Hour",
		            "interval": 1
		        }
		    }
		}

 	輸出位置是 **adftutorial/customactivityoutput/YYYYMMDDHH/**，其中 YYYYMMDDHH 是產生配量的年、月、日、時。如需詳細資訊，請參閱[開發人員參考資料][adf-developer-reference]。

	為每個輸入配量產生輸出 blob/檔案。以下是為每個配量命名輸出檔案的方式。所有的輸出檔案會產生於一個輸出資料夾：**adftutorial\\customactivityoutput**。

	| 配量 | 開始時間 | 輸出檔案 |
	| :---- | :--------- | :---------- | 
	| 1 | 2015-11-16T00:00:00 | 2015-11-16-00.txt |
	| 2 | 2015-11-16T01:00:00 | 2015-11-16-01.txt |
	| 3 | 2015-11-16T02:00:00 | 2015-11-16-02.txt |
	| 4 | 2015-11-16T03:00:00 | 2015-11-16-03.txt |
	| 5 | 2015-11-16T04:00:00 | 2015-11-16-04.txt |

	請記得輸入資料夾中的所有檔案都是包含上述開始時間之配量的一部分。處理此配量時，自訂活動會掃描每個檔案，並利用搜尋詞彙 (“Microsoft”) 的出現次數在輸出檔案中產生資料行。如果 inputfolder 中有三個檔案，每小時的配量會有三行在輸出檔案中：2015-11-16-00.txt、2015-11-16:01:00:00.txt等等...。


2. 按一下命令列上的 [部署] 來部署 **OutputDataset**。


### 建立並執行使用自訂活動的管線

1. 在 Data Factory 編輯器中，按一下工具列上的 [**新增管線**]。如果看不到此命令，請按一下 [...] (省略符號) 就可看到。
2. 使用下列 JSON 指令碼取代右窗格中的 JSON。如果您想要使用自己的叢集，且已遵循步驟建立 **HDInsightLinkedService** 連結服務，請在下列 JSON 中以 **HDInsightLinkedService** 取代 **HDInsightOnDemandLinkedService**。

		{
		  "name": "ADFTutorialPipelineCustom",
		  "properties": {
		    "description": "Use custom activity",
		    "activities": [
		      {
		        "Name": "MyDotNetActivity",
		        "Type": "DotNetActivity",
		        "Inputs": [
		          {
		            "Name": "InputDataset"
		          }
		        ],
		        "Outputs": [
		          {
		            "Name": "OutputDataset"
		          }
		        ],
		        "LinkedServiceName": "HDInsightOnDemandLinkedService",
		        "typeProperties": {
		          "AssemblyName": "MyDotNetActivity.dll",
		          "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
		          "PackageLinkedService": "StorageLinkedService",
		          "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",
		          "extendedProperties": {
		            "SliceStart": "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))"
		          }
		        },
		        "Policy": {
		          "Concurrency": 1,
		          "ExecutionPriorityOrder": "OldestFirst",
		          "Retry": 3,
		          "Timeout": "00:30:00",
		          "Delay": "00:00:00"
		        }
		      }
		    ],
    		"start": "2015-11-16T00:00:00Z",
			"end": "2015-11-16T05:00:00Z",
		    "isPaused": false
		  }
		}

	以目前日期的前三天取代 **StartDateTime** 值，並以目前日期取代 **EndDateTime** 值。StartDateTime 和 EndDateTime 都必須是 [ISO 格式](http://en.wikipedia.org/wiki/ISO_8601)。例如：2014-10-14T16:32:41Z。將輸出資料表排定為每天產生，因此將產生三個配量。

	請注意：

	- activities 區段中有一個活動，它的類型是：**DotNetActivity**。
	- 使用您在「開始使用」教學課程中所用的相同輸入資料表 **EmpTableFromBlob**。
	- 使用您將在下一個步驟中建立的新輸出資料表 **OutputTableForCustom**。
	- **AssemblyName** 設定為此 DLL 的名稱：**MyActivities.dll**。
	- **EntryPoint** 設定為 **MyDotNetActivityNS.MyDotNetActivity**。
	- **PackageLinkedService** 設為 **StorageLinkedService**，它會指向包含自訂活動 zip 檔案的 blob 儲存體。如果您將不同的 Azure 儲存體帳戶用於輸入/輸出檔案和自訂活動 zip 檔案，您必須建立另一個 Azure 儲存體連結服務。本文假設您使用相同的 Azure 儲存體帳戶。
	- **PackageFile** 設定為 **customactivitycontainer/MyDotNetActivity.zip**。它的格式為：<containerforthezip>/<nameofthezip.zip>。
	- 自訂活動會採用 **InputDataset** 做為輸入和 **OutputDataset** 做為輸出。
	- 自訂活動的 linkedServiceName 屬性指向 **HDInsightLinkedService**，這會告知 Azure Data Factory 自訂活動必須在 Azure HDInsight 叢集上執行。
	- **isPaused** 屬性預設為 **false**。在此範例中，管線會立即執行，因為配量已在過去開始。您可以將此屬性設為 true，以暫停管線，並將其設回 false，以重新啟動。 
	- **啟動**時間和**結束**時間距離 **5** 小時，而配量會每小時產生，因此管線會產生 5 個配量。 


4. 按一下命令列上的 [部署]，部署管線。

### 監視管線
 
8. 在 Azure 入口網站的 [Data Factory] 刀鋒視窗中，按一下 [圖表]。
	
	![[圖表] 磚](./media/data-factory-use-custom-activities/DataFactoryBlade.png)
 
9. 在 [圖表] 檢視中，現在按一下 OutputDataset。
 
	![圖表檢視](./media/data-factory-use-custom-activities/diagram.png)

10. 如果已產生 5 個輸出配量，您應該會看到它們都處於就緒狀態。

	![輸出配量](./media/data-factory-use-custom-activities/OutputSlices.png)
	
12. 確認 Blob 儲存體的 **adftutorial** 容器中已產生輸出檔案。

	![自訂活動的輸出][image-data-factory-ouput-from-custom-activity]

9. 如果您開啟輸出檔，您應該會看到類似下面的輸出：

	檔案 inputfolder/2015-11-16-00/file.txt 中找到搜尋詞彙 "Microsoft" 出現 2 次。

10.	使用 [Azure 入口網站][azure-preview-portal]或 Azure PowerShell Cmdlet 來監視您的 Data Factory、管線和資料集。在可從入口網站下載的記錄檔中 (尤其是 user-0.log)，您可以從程式碼中的 **ActivityLogger**，或使用 Cmdlet，以查看自訂活動的訊息。

	![從自訂活動下載記錄檔][image-data-factory-download-logs-from-custom-activity]


如需有關監視資料集和管線的詳細步驟，請參閱[監視和管理管線](data-factory-monitor-manage-pipelines.md)。

### 偵錯管線
偵錯包含一些基本技術：

1.	如果輸入配量不是設定為**就緒**，請確認輸入資料夾結構正確，且 file.txt 存在於輸入資料夾中。
2.	在自訂活動的 **Execute** 方法中，使用可協助您疑難排解問題的 **IActivityLogger** 物件記錄資訊。記錄的訊息會顯示在 user\_0.log 檔案中。 

	在 [OutputDataset] 刀鋒視窗中，按一下配量，以查看該配量的 [資料配量] 刀鋒視窗。您會看到該配量的**活動執行**。您會看到一個為該配量執行的活動。如果您按一下命令列中的 [執行]，您可以為相同的配量啟動另一個活動執行。

	當您按一下活動執行，您會看到包含記錄檔清單的 [活動執行詳細資料] 刀鋒視窗。您會在 user\_0.log 檔案中看到記錄的訊息。發生錯誤時，您會看到三個活動執行，因為管線/活動 JSON 中的重試計數設定為 3。當您按一下活動執行，您會看到您可以檢閱的記錄檔來疑難排解錯誤。

	在記錄檔清單中，按一下 [user-0.log]。在右窗格中的是使用 **IActivityLogger.Write** 方法的結果。

	您也應該檢查 **system-0.log** 是否有任何系統錯誤訊息和例外狀況。

3.	在 zip 檔案中包含 **PDB** 檔案，錯誤詳細資料才會在錯誤發生時包含**呼叫堆疊**等資訊。
4.	自訂活動之 zip 檔案中的所有檔案都必須位於**最上層**且不包含任何子資料夾。
5.	確認 **assemblyName** (MyDotNetActivity.dll)、**entryPoint**(MyDotNetActivityNS.MyDotNetActivity)、**packageFile** (customactivitycontainer/MyDotNetActivity.zip) 和 **packageLinkedService** (應指向包含 zip 檔案的 Azure blob 儲存體) 都設為正確的值。 
6.	如果您修正錯誤，並想要重新處理配量，請以滑鼠右鍵按一下 [OutputDataset] 刀鋒視窗中的配量，然後按一下 [執行]。 


## 更新自訂活動
如果您更新自訂活動的程式碼，請建置它，並將包含新二進位檔案的 zip 檔案上傳至 Blob 儲存體。

## 存取延伸屬性
您可以在活動 JSON 中宣告延伸屬性，如下所示：

	"typeProperties": {
	  "AssemblyName": "MyDotNetActivity.dll",
	  "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
	  "PackageLinkedService": "StorageLinkedService",
	  "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",
	  "extendedProperties": {
	    "SliceStart": "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))",
		"DataFactoryName": "CustomActivityFactory"
	  }
	},

上述範例中有兩個延伸屬性：**SliceStart** 和 **DataFactoryName**。SliceStart 的值以 SliceStart 系統變數為基礎。如需支援的系統變數清單，請參閱[系統變數](data-factory-scheduling-and-execution.md#data-factory-system-variables)。DataFactoryName 值為硬式編碼為 "CustomActivityFactory"。

若要以 **Execute** 方法存取這些延伸屬性，請使用如下的程式碼：

	// to get extended properties (for example: SliceStart)
	DotNetActivity dotNetActivity = (DotNetActivity)activity.TypeProperties;
	string sliceStartString = dotNetActivity.ExtendedProperties["SliceStart"];

	// to log all extended properties                               
    IDictionary<string, string> extendedProperties = dotNetActivity.ExtendedProperties;
    logger.Write("Logging extended properties if any...");
    foreach (KeyValuePair<string, string> entry in extendedProperties)
    {
    	logger.Write("<key:{0}> <value:{1}>", entry.Key, entry.Value);
	}


## <a name="AzureBatch"></a> 使用 Azure Batch 連結服務
> [AZURE.NOTE]請參閱 [Azure Batch 基本知識][batch-technical-overview]，以取得 Azure Batch 服務的概觀，另請參閱[開始使用適用於 .NET 的 Azure Batch 程式庫][batch-get-started]，以快速開始使用 Azure Batch 服務。

您可以將 Azure Batch 當作計算資源使用，執行您的自訂 .NET 活動。您必須建立自己的 Azure Batch 集區，並指定 VM 數量和其他組態。Azure Batch 集區為客戶提供下列功能：

1. 建立包含一至數千個核心的集區。
2. 根據公式自動調整 VM 數量
3. 支援任何大小的 VM
4. 每個 VM 均可設定工作數目
5. 佇列無限個工作


以下是上一節所述的逐步解說中使用 Azure Batch 連結服務的高階步驟：

1. 使用 [Azure 入口網站](http://manage.windowsazure.com)建立 Azure Batch 帳戶。請參閱[建立和管理 Azure Batch 帳戶][batch-create-account]文章以取得指示。記下 Azure Batch 帳戶名稱和帳戶金鑰。

	您也可以使用 [New-AzureBatchAccount][new-azure-batch-account] Cmdlet 建立 Azure Batch 帳戶。如需使用此 Cmdlet 的詳細指示，請參閱[使用 Azure PowerShell 管理 Azure Batch 帳戶][azure-batch-blog]。
2. 建立 Azure Batch 集區。您可以下載、編譯並使用 [Azure Batch 總管工具][batch-explorer]的原始碼，或使用[適用於 .NET 的 Azure Batch 程式庫][batch-net-library]來建立 Azure Batch 集區。如需有關使用 Azure Batch 總管的逐步指示，請參閱 [Azure Batch 總管範例逐步解說][batch-explorer-walkthrough]。

	您也可以使用 [New-AzureRmBatchPool](https://msdn.microsoft.com/library/mt628690.aspx) Cmdlet 建立 Azure Batch 集區。

	您可能會想要使用至少 2 個計算節點建立 Azure Batch 集區，以便以平行方式處理配量。如果您使用 Batch 總管：

	- 請輸入集區的識別碼 (**集區識別碼**)。請注意**集區的識別碼**；您將在建立 Data Factory 解決方案時需要它。 
	- 指定作業系統系列設定的 **Windows Server 2012 R2**。
	- 指定 **2** 做為**每個計算節點之最大工作**設定的值。
	- 指定 **2** 做為**目標專用數字**設定的值。 

	Data Factory 服務會在 Azure Batch 中建立作業，其名為：adf-<pool name>:job-xxx。配量的每個活動執行都會建立一個作業。如果有 10 個配量就緒可供處理，此作業中會建立 10 個作業。如果您在集區中有多個計算結點，您可以同時執行多個配量。如果每個計算結點的最大工作設為 > 1，您也可以在相同的計算中執行多個配量。
	
	![Batch 總管工作](./media/data-factory-use-custom-activities/BatchExplorerTasks.png)

	![Data Factory & Batch](./media/data-factory-use-custom-activities/DataFactoryAndBatch.png)

2. 使用下列 JSON 範本建立 Azure Batch 連結服務。Data Factory 編輯器會顯示類似的範本讓您開始使用。JSON 片段中指定的 Azure Batch 帳戶名稱、帳戶金鑰和集區名稱。

		{
		  "name": "AzureBatchLinkedService",
		  "properties": {
		    "type": "AzureBatch",
		    "typeProperties": {
		      "accountName": "<Azure Batch account name>",
			  "batchUri": "https://<region>.batch.azure.com",
		      "accessKey": "<Azure Batch account key>",
		      "poolName": "<Azure Batch pool name>",
		      "linkedServiceName": "<Specify associated storage linked service reference here>"
		    }
		  }
		}

	> [AZURE.IMPORTANT][Azure Batch 帳戶刀鋒視窗] 的 **URL** 格式如下：accountname.region.batch.azure.com。針對 JSON 中的 **batchUri** 屬性，您必須從該 URL **移除「帳戶名稱」**，並針對 **accountName** JSON 屬性使用該**帳戶名稱**。

	針對 **poolName** 屬性，您也可以指定該集區的 ID，而非集區名稱。

	如需這些屬性的描述，請參閱 [Azure Batch 連結服務 MSDN 主題](https://msdn.microsoft.com/library/mt163609.aspx)。

2.  在 Data Factory 編輯器 中，開啟您在逐步解說中建立的管線的 JSON 定義，並以 **AzureBatchLinkedService** 取代 **HDInsightLinkedService**。
3.  您可能希望變更管線的開始和結束時間，讓您能夠使用 Azure Batch 服務來測試案例。
4.  在 Azure Batch 總管中，您可以看到與處理配量相關聯的 Azure Batch 工作，如下圖所示。

	![Azure Batch 工作][image-data-factory-azure-batch-tasks]

> [AZURE.NOTE]與支援 HDInsight 的情況不同，Data Factory 服務不支援 Azure Batch 的隨選選項。您只能使用 Azure Data Factory 中自己的 Azure Batch 集區。

## 另請參閱

[Azure Data Factory 更新：使用 Azure Batch 執行 ADF 自訂 .NET 活動](http://azure.microsoft.com/blog/2015/05/01/azure-data-factory-updates-execute-adf-custom-net-activities-using-azure-batch/)。

[batch-net-library]: ../batch/batch-dotnet-get-started.md
[batch-explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[batch-explorer-walkthrough]: http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx
[batch-create-account]: ../batch/batch-account-create-portal.md
[batch-technical-overview]: ../batch/batch-technical-overview.md
[batch-get-started]: ../batch/batch-dotnet-get-started.md
[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
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
[hivewalkthrough]: data-factory-data-transformation-activities.md

[image-data-factory-ouput-from-custom-activity]: ./media/data-factory-use-custom-activities/OutputFilesFromCustomActivity.png

[image-data-factory-download-logs-from-custom-activity]: ./media/data-factory-use-custom-activities/DownloadLogsFromCustomActivity.png

[image-data-factory-azure-batch-tasks]: ./media/data-factory-use-custom-activities/AzureBatchTasks.png

<!---HONumber=AcomDC_0107_2016-->