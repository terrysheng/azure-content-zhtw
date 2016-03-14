<properties 
	pageTitle="使用 Data Factory SDK 來建立、監視及管理 Azure Data Factory" 
	description="了解如何使用 Data Factory .NET SDK，以程式設計方式建立、監視和管理 Azure Data Factory。" 
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
	ms.date="01/26/2016" 
	ms.author="spelluru"/>

# 使用 Data Factory .NET SDK 來建立、監視及管理 Azure Data Factory
## 概觀
您可以使用 Data Factory .NET SDK，以程式設計方式建立、監視及管理 Azure Data Factory本文包含指導您建立範例 .NET 主控台應用程式的逐步解說，此應用程式將會建立並監視 Data Factory。請參閱 [Data Factory 類別庫參考][adf-class-library-reference]，以取得有關 Data Factory .NET SDK 的詳細資料。



## 必要條件

- Visual Studio 2012 或 2013
- 下載並安裝 [Azure .NET SDK][azure-developer-center]。
- 下載並安裝適用於 Azure Data Factory 的 NuGet 封裝逐步解說中包含相關指示。

## 逐步介紹
1. 使用 Visual Studio 2012 或 2013 來建立 C# .NET 主控台應用程式。
	<ol type="a">
		<li>啟動 <b>Visual Studio 2012</b> 或 <b>Visual Studio 2013</b>。</li>
		<li>按一下 [<b>檔案</b>]，指向 [<b>新增</b>]，然後按一下 [<b>專案</b>]。</li> 
		<li>展開 [範本]<b></b>，然後選取 [Visual C#]<b></b>。在此逐步解說中，您使用的是 C#，但您可以使用任何 .NET 語言。</li> 
		<li>從右邊的專案類型清單中選取 [主控台應用程式]<b></b>。</li>
		<li>在 [名稱]<b></b> 輸入 <b>DataFactoryAPITestApp</b>。</li> 
		<li>在 [<b>位置</b>] 中選取 <b>C:\ADFGetStarted</b>。</li>
		<li>按一下 [確定]<b></b> 以建立專案。</li>
	</ol>
2. 按一下 [<b>工具</b>]，指向 [<b>NuGet 封裝管理員</b>]，然後按一下 [<b>封裝管理員主控台</b>]。
3.	在 [Package Manager Console]<b></b> 中，逐一執行下列命令。</b>。 

		Install-Package Microsoft.Azure.Management.DataFactories
		Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
6. 將下列 **appSetttings** 區段加入 **App.config** 檔案。Helper 方法使用的是：**Microsoft.identitymodel.waad.preview.graph.graphinterface**。 

	以您的 Azure 訂用帳戶與租用戶識別碼取代 **SubscriptionId** 和 **ActiveDirectoryTenantId** 的值。從 Azure PowerShell 執行 **Get-AzureAccount** 即可取得這些值 (您可能需要先使用 Add-AzureAccount 登入)。
 
		<appSettings>
		    <!--CSM Prod related values-->
		    <add key="ActiveDirectoryEndpoint" value="https://login.windows.net/" />
		    <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
		    <add key="WindowsManagementUri" value="https://management.core.windows.net/" />
		    <add key="AdfClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
		    <add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
		    <!--Make sure to write your own tenenat id and subscription ID here-->
		    <add key="SubscriptionId" value="your subscription ID" />
    		<add key="ActiveDirectoryTenantId" value="your tenant ID" />
		</appSettings>
6. 將下列 **using** 陳述式加入專案的原始程式檔 (Program.cs) 中。

		using System.Threading;
		using System.Configuration;
		using System.Collections.ObjectModel;
		
		using Microsoft.Azure.Management.DataFactories;
		using Microsoft.Azure.Management.DataFactories.Models;
		using Microsoft.Azure.Management.DataFactories.Common.Models;
		
		using Microsoft.IdentityModel.Clients.ActiveDirectory;
		using Microsoft.Azure;
6. 將下列會建立 **DataPipelineManagementClient** 類別執行個體的程式碼加入 **Main** 方法中。您將使用此物件來建立 Data Factory、連結的服務、輸入和輸出資料集，以及管線。您也將使用此物件來監視執行階段的資料集配量。    

        // create data factory management client
        string resourceGroupName = "resourcegroupname";
        string dataFactoryName = "APITutorialFactorySP";

        TokenCloudCredentials aadTokenCredentials =
            new TokenCloudCredentials(
                ConfigurationManager.AppSettings["SubscriptionId"],
                GetAuthorizationHeader());

        Uri resourceManagerUri = new Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

        DataFactoryManagementClient client = new DataFactoryManagementClient(aadTokenCredentials, resourceManagerUri);

	> [AZURE.NOTE] 用您的 Azure 資源群組名稱取代 **resourcegroupname**。若要建立資源群組，請使用 [New-AzureResourceGroup](https://msdn.microsoft.com/library/Dn654594.aspx) Cmdlet。

7. 將下列會建立 **Data Factory** 的程式碼加入 **Main** 方法中。

        // create a data factory
        Console.WriteLine("Creating a data factory");
        client.DataFactories.CreateOrUpdate(resourceGroupName,
            new DataFactoryCreateOrUpdateParameters()
            {
                DataFactory = new DataFactory()
                {
                    Name = dataFactoryName,
                    Location = "westus",
                    Properties = new DataFactoryProperties() { }
                }
            }
        );

8. 將下列會建立**連結服務**的程式碼加入 **Main** 方法中。

	> [AZURE.NOTE] 使用您 Azure 儲存體帳戶的**帳戶名稱**和**帳戶金鑰**做為 **ConnectionString**。

        // create a linked service
        Console.WriteLine("Creating a linked service");
        client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
            new LinkedServiceCreateOrUpdateParameters()
            {
                LinkedService = new LinkedService()
                {
                    Name = "LinkedService-AzureStorage",
                    Properties = new LinkedServiceProperties
                    (
                        new AzureStorageLinkedService("DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>")
                    )
                }
            }
        );
9. 將下列會建立**輸入和輸出資料集**的程式碼加入 **Main** 方法中。 

	請注意，輸入 Blob 的 **FolderPath** 設定為 **adftutorial/**，其中 **adftutorial** 是 Blob 儲存體中的容器名稱。如果 Azure Blob 儲存體中沒有此容器，請以下列名稱建立容器：**adftutorial**，並將文字檔上傳至容器。
	
	請注意，輸出 Blob 的 FolderPath 設為：**adftutorial/apifactoryoutput/{Slice}**，其中 **Slice** 是根據 **SliceStart** (每個配量的開始日期時間) 的值自動計算而得。

 
        // create input and output datasets
        Console.WriteLine("Creating input and output datasets");
        string Dataset_Source = "DatasetBlobSource";
        string Dataset_Destination = "DatasetBlobDestination";

        client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,
            new DatasetCreateOrUpdateParameters()
            {
                Dataset = new Dataset()
                {
                    Name = Dataset_Source,
                    Properties = new DatasetProperties()
                    {
                        LinkedServiceName = "LinkedService-AzureStorage",
                        TypeProperties = new AzureBlobDataset()
                        {
                            FolderPath = "adftutorial/",
                            FileName = "emp.txt"
                        },
                        External = true,
                        Availability = new Availability()
                        {
                            Frequency = SchedulePeriod.Hour,
                            Interval = 1,
                        },

                        Policy = new Policy()
                        {
                            Validation = new ValidationPolicy()
                            {
                                MinimumRows = 1
                            }
                        }
                    }
                }
            });

        client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,
            new DatasetCreateOrUpdateParameters()
            {
                Dataset = new Dataset()
                {
                    Name = Dataset_Destination,
                    Properties = new DatasetProperties()
                    {

                        LinkedServiceName = "LinkedService-AzureStorage",
                        TypeProperties = new AzureBlobDataset()
                        {
                            FolderPath = "adftutorial/apifactoryoutput/{Slice}",
                            PartitionedBy = new Collection<Partition>()
                            {
                                new Partition()
                                {
                                    Name = "Slice",
                                    Value = new DateTimePartitionValue()
                                    {
                                        Date = "SliceStart",
                                        Format = "yyyyMMdd-HH"
                                    }
                                }
                            }
                        },

                        Availability = new Availability()
                        {
                            Frequency = SchedulePeriod.Hour,
                            Interval = 1,
                        },
                    }
                }
            });

11. 將下列會**建立並啟用管線**的程式碼加入 **Main** 方法中。此管線有一個 **CopyActivity**，它以 **BlobSource** 為來源，**BlobSink** 為接收器。

複製活動會在 Azure Data Factory 中執行資料移動，而此活動是由全域可用的服務所提供，可以使用安全、可靠及可調整的方式，在各種不同的資料存放區之間複製資料。如需複製活動的詳細資訊，請參閱[資料移動活動](data-factory-data-movement-activities.md)文章。

            // create a pipeline
        Console.WriteLine("Creating a pipeline");
        DateTime PipelineActivePeriodStartTime = new DateTime(2014, 8, 9, 0, 0, 0, 0, DateTimeKind.Utc);
        DateTime PipelineActivePeriodEndTime = PipelineActivePeriodStartTime.AddMinutes(60);
        string PipelineName = "PipelineBlobSample";

        client.Pipelines.CreateOrUpdate(resourceGroupName, dataFactoryName,
            new PipelineCreateOrUpdateParameters()
            {
                Pipeline = new Pipeline()
                {
                    Name = PipelineName,
                    Properties = new PipelineProperties()
                    {
                        Description = "Demo Pipeline for data transfer between blobs",

                        // Initial value for pipeline's active period. With this, you won't need to set slice status
                        Start = PipelineActivePeriodStartTime,
                        End = PipelineActivePeriodEndTime,

                        Activities = new List<Activity>()
                        {                                
                            new Activity()
                            {   
                                Name = "BlobToBlob",
                                Inputs = new List<ActivityInput>()
                                {
                                    new ActivityInput() {
                                        Name = Dataset_Source
                                    }
                                },
                                Outputs = new List<ActivityOutput>()
                                {
                                    new ActivityOutput()
                                    {
                                        Name = Dataset_Destination
                                    }
                                },
                                TypeProperties = new CopyActivity()
                                {
                                    Source = new BlobSource(),
                                    Sink = new BlobSink()
                                    {
                                        WriteBatchSize = 10000,
                                        WriteBatchTimeout = TimeSpan.FromMinutes(10)
                                    }
                                }
                            }

                        },
                    }
                }
            });

	

12. 將 **Main** 方法所使用的下列 Helper 方法加入 **Program** 類別中。此方法會顯示一個對話方塊，讓您提供登入 Azure 傳統入口網站的**使用者名稱**和**密碼**。 
 
		public static string GetAuthorizationHeader()
        {
            AuthenticationResult result = null;
            var thread = new Thread(() =>
            {
                try
                {
                    var context = new AuthenticationContext(ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] + ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);

                    result = context.AcquireToken(
                        resource: ConfigurationManager.AppSettings["WindowsManagementUri"],
                        clientId: ConfigurationManager.AppSettings["AdfClientId"],
                        redirectUri: new Uri(ConfigurationManager.AppSettings["RedirectUri"]),
                        promptBehavior: PromptBehavior.Always);
                }
                catch (Exception threadEx)
                {
                    Console.WriteLine(threadEx.Message);
                }
            });

            thread.SetApartmentState(ApartmentState.STA);
            thread.Name = "AcquireTokenThread";
            thread.Start();
            thread.Join();

            if (result != null)
            {
                return result.AccessToken;
            }

            throw new InvalidOperationException("Failed to acquire token");
        }  
 
13. 將下列程式碼加入 **Main** 方法中，以取得輸出資料集的資料配量狀態。在此範例中只預期有配量。
 
        // Pulling status within a timeout threshold
        DateTime start = DateTime.Now;
        bool done = false;

        while (DateTime.Now - start < TimeSpan.FromMinutes(5) && !done)
        {
            Console.WriteLine("Pulling the slice status");
            // wait before the next status check
            Thread.Sleep(1000 * 12);

            var datalistResponse = client.DataSlices.List(resourceGroupName, dataFactoryName, Dataset_Destination,
                new DataSliceListParameters()
                {
                    DataSliceRangeStartTime = PipelineActivePeriodStartTime.ConvertToISO8601DateTimeString(),
                    DataSliceRangeEndTime = PipelineActivePeriodEndTime.ConvertToISO8601DateTimeString()
                });

            foreach (DataSlice slice in datalistResponse.DataSlices)
            {
                if (slice.State == DataSliceState.Failed || slice.State == DataSliceState.Ready)
                {
                    Console.WriteLine("Slice execution is done with status: {0}", slice.State);
                    done = true;
                    break;
                }
                else
                {
                    Console.WriteLine("Slice status is: {0}", slice.State);
                }
            }
        }

14. **(選用)** 將下列會取得資料配量之執行詳細資料的程式碼加入 **Main** 方法中。

        Console.WriteLine("Getting run details of a data slice");

		// give it a few minutes for the output slice to be ready
        Console.WriteLine("\nGive it a few minutes for the output slice to be ready and press any key.");
        Console.ReadKey();

        var datasliceRunListResponse = client.DataSliceRuns.List(
                resourceGroupName,
                dataFactoryName,
                Dataset_Destination,
                new DataSliceRunListParameters()
                {
                    DataSliceStartTime = PipelineActivePeriodStartTime.ConvertToISO8601DateTimeString()
                }
            );
        
        foreach (DataSliceRun run in datasliceRunListResponse.DataSliceRuns)
        {
            Console.WriteLine("Status: \t\t{0}", run.Status);
            Console.WriteLine("DataSliceStart: \t{0}", run.DataSliceStart);
            Console.WriteLine("DataSliceEnd: \t\t{0}", run.DataSliceEnd);
            Console.WriteLine("ActivityId: \t\t{0}", run.ActivityName);
            Console.WriteLine("ProcessingStartTime: \t{0}", run.ProcessingStartTime);
            Console.WriteLine("ProcessingEndTime: \t{0}", run.ProcessingEndTime);
            Console.WriteLine("ErrorMessage: \t{0}", run.ErrorMessage);
        }

        Console.WriteLine("\nPress any key to exit.");
        Console.ReadKey();

15. 在 [方案總管] 中展開專案 (**DataFactoryAPITestApp**)，以滑鼠右鍵按一下 [參考]，然後按一下 [加入參考]。選取 **System.Configuration** 組件的核取方塊，然後按一下 [確定]。
16. 建置主控台應用程式。按一下功能表上的 [建置]，再按一下 [建置方案]。 
16. 確認您 Azure Blob 儲存體之 adftutorial 容器中至少有一個檔案。如果沒有，請在「記事本」中以下列內容建立 Emp.txt 檔案，然後將它上傳至 adftutorial 容器。

        John, Doe
		Jane, Doe
	 
17. 按一下功能表上的 [偵錯] -> [開始偵錯]，執行範例。當您看到 [取得資料配量的執行詳細資料]，請等待數分鐘再按 **ENTER**。
18. 使用 Azure 入口網站確認 Data Factory：**APITutorialFactory** 是使用下列成品所建立： 
	- 連結服務：**LinkedService\_AzureStorage** 
	- 資料集：**DatasetBlobSource** 和 **DatasetBlobDestination**。
	- 管線：**PipelineBlobSample** 
18. 確認輸出檔案已建立在 **adftutorial** 容器的 **apifactoryoutput** 資料夾中。



> [AZURE.NOTE] 上述範例程式碼會啟動一個對話方塊供您輸入 Azure 認證。如果您需要以程式設計的方式登入，而不使用對話方塊，請參閱[使用 Azure 資源管理員驗證服務主體](resource-group-authenticate-service-principal.md#authenticate-service-principal-with-certificate---powershell)。


[data-factory-introduction]: data-factory-introduction.md
[adf-getstarted]: data-factory-get-started.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
 
[adf-class-library-reference]: http://go.microsoft.com/fwlink/?LinkID=521877
[azure-developer-center]: http://azure.microsoft.com/downloads/
 

<!---HONumber=AcomDC_0302_2016-->