<properties 
	pageTitle="使用機器學習 Web 服務 | Microsoft Azure" 
	description="機器學習服務發佈之後，就可以使用 RESTFul Web 服務做為要求-回應服務或批次執行服務。" 
	services="machine-learning" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="tbd" 
	ms.date="06/29/2015" 
	ms.author="bradsev" />


# 如何使用已從機器學習實驗發佈的 Azure Machine Learning Web 服務

## 簡介

發佈為 Web 服務時，Azure Machine Learning 實驗所提供的 REST API，可供各種裝置和平台使用。這是因為簡單的 REST API 可接受並回應 JSON 格式化的訊息。Azure Machine Learning 入口網站提供的程式碼可用來呼叫 R、C# 和 Python 的 Web 服務。但是要能夠使用任何程式設計語言並從任何裝置呼叫這些服務，必須滿足三個準則：

* 具備網路連線
* 具備執行 HTTPS 要求的 SSL 功能
* 具備剖析 JSON 的能力 (藉由手動或支援程式庫)

這表示可從 Web 應用程式、行動應用程式、自訂桌面應用程式和甚至從 Excel 使用服務！

[AZURE.INCLUDE [電腦-學習-免費-試用](../../includes/machine-learning-free-trial.md)]

使用 Azure Machine Learning Web 服務有兩種不同方式，可以做為要求-回應服務，或者批次執行服務。在這兩個案例中，一旦發佈實驗之後，就可以使用透過 RESTFul Web 服務提供的功能。在 Azure 中使用 Azure Web 服務端點部署機器學習 Web 服務，服務會根據使用量自動調整，您可以避免預先且持續付出硬體資源成本。

<!-- When this article gets published, fix the link and uncomment
For more information on how to manage Azure Machine Learning web service endpoints using the REST API, see **Azure machine learning web service endpoints**. 
-->

如需關於如何建立及發佈 Azure Machine Learning Web 服務的資訊，請參閱[發佈 Azure Machine Learning Web 服務][publish]。如需建立機器學習實驗和發佈實驗的逐步解說，請參閱[使用 Azure Machine Learning 開發預測解決方案][walkthrough]。

[publish]: machine-learning-publish-a-machine-learning-web-service.md
[walkthrough]: machine-learning-walkthrough-develop-predictive-solution.md


## 要求回應服務 (RRS)

「要求回應服務 (RRS)」是一種低延遲、高度可擴充的 Web 服務，可用來為從 Azure Machine Learning Studio 實驗建立並發佈的無狀態模型提供介面。

RRS 接受單一資料列的輸入參數，並會產生單一資料列作為輸出內容。輸出資料列可包含多個資料行。

RRS 範例驗證應用程式的真確性。您可以將絕大多數應用程式安裝為此種狀況。應用程式啟動時，它會使用相關輸入呼叫 RRS 服務。然後應用程式會接收來自服務的驗證回應，允許或封鎖應用程式執行。


## 批次執行服務 (BES)
 
「批次執行服務 (BES)」是一種可為大量的一批資料記錄進行非同步計分的服務。BES 的輸入包含來自各種來源 (例如 blob、Azure 中的表格、SQL Azure、HDInsight (例如 Hive 查詢的結果) 和 HTTP 來源) 的一批記錄。BES 的輸出則包含計分的結果。結果會輸出至位於 Azure blob 儲存體中的檔案，並在回應中傳回儲存體端點的資料。

當您不需要立即收到回應時 (例如為個人或物聯網 (IOT) 裝置定期排程計分)，BES 就很有用。

## 範例
為了同時顯示 RRS 和 BES 兩者如何運作，我們使用 Azure Web 服務範例。此服務將用於 IOT (Internet Of Things) 案例。為了簡單起見，我們的裝置只會傳送一個值，`cog_speed`，並取得單一的回應。

呼叫 RRS 或 BES 服務需要四種資訊。發佈實驗之後，就可以在 [Azure Machine Learning 服務頁面](https://studio.azureml.net)中取得這項資訊。按一下畫面左側的 [Web 服務] 連結，您會看到已發佈的服務。若要尋找特定服務的相關資訊，RRS 和 BES 兩者都有 API 說明頁面連結。

1.	**服務 API 金鑰**位於服務主要頁面
2.	**服務 URI** 位於所選服務的 API 說明頁面
3.	預期的 **API 要求本文**位於所選服務的 API 說明頁面
4.	預期的 **API 回應本文**位於所選服務的 API 說明頁面

在下列兩個範例中，我們使用 C# 語言說明所需的程式碼，目標平台是 Windows 8 桌面。

### RRS 範例
在 API 說明頁面上，除了 URI，您要輸入和輸出定義和程式碼範例。API 輸入是特別針對這項服務呼叫，且為 API 呼叫的內容。

**範例要求**

	{
	  "Inputs": {
	    "input1": {
	      "ColumnNames": [
	        "cog_speed"
	      ],
	      "Values": [
	        [
	          "0"
	        ],
	        [
	          "1"
	        ]
	      ]
	    }
	  },
	  "GlobalParameters": {}
	}


同樣地，API 回應也是特別針對這項服務呼叫。

**範例回應**

	{
	  "Results": {
	    "output1": {
	      "type": "DataTable",
	      "value": {
	        "ColumnNames": [
	          "cog_speed"
	        ],
	        "ColumnTypes": [
	          "Numeric"
	        ].
	      "Values": [
	        [
	          "0"
	        ],
	        [
	          "1"
	        ]
	      ]
	    }
	  },
	  "GlobalParameters": {}
	}

您會在頁面底部找到程式碼範例。下面是 C# 實作的程式碼範例
                   
**範例程式碼**

	using System;
	using System.Collections.Generic;
	using System.IO;
	using System.Net.Http;
	using System.Net.Http.Formatting;
	using System.Net.Http.Headers;
	using System.Text;
	using System.Threading.Tasks;
	
	namespace CallRequestResponseService
	{
	    public class StringTable
	    {
	        public string[] ColumnNames { get; set; }
	        public string[,] Values { get; set; }
	    }
	
	    class Program
	    {
	        static void Main(string[] args)
	        {
	            InvokeRequestResponseService().Wait();
	        }
	
	        static async Task InvokeRequestResponseService()
	        {
	            using (var client = new HttpClient())
	            {
	                var scoreRequest = new
	                {
	                    Inputs = new Dictionary<string, StringTable> () { 
	                        { 
	                            "input1", 
	                            new StringTable() 
	                            {
	                                ColumnNames = new string[] {"cog_speed"},
	                                Values = new string[,] {  { "0"},  { "1"}  }
	                            }
	                        },
	                    GlobalParameters = new Dictionary<string, string>() { }
	                };
	                
	                const string apiKey = "abc123"; // Replace this with the API key for the web service
	                client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue( "Bearer", apiKey);
	
	                client.BaseAddress = new Uri("https://ussouthcentral.services.azureml.net/workspaces/<workspace id>/services/<service id>/execute?api-version=2.0&details=true");
	                
	                // WARNING: The 'await' statement below can result in a deadlock if you are calling this code from the UI thread of an ASP.Net application.
	                // One way to address this would be to call ConfigureAwait(false) so that the execution does not attempt to resume on the original context.
	                // For instance, replace code such as:
	                //      result = await DoSomeTask()
	                // with the following:
	                //      result = await DoSomeTask().ConfigureAwait(false)

	                HttpResponseMessage response = await client.PostAsJsonAsync("", scoreRequest);
	
	                if (response.IsSuccessStatusCode)
	                {
	                    string result = await response.Content.ReadAsStringAsync();
	                    Console.WriteLine("Result: {0}", result);
	                }
	                else
	                {
	                    Console.WriteLine("Failed with status code: {0}", response.StatusCode);
	                }
	            }
	        }
	    }
	}

### BES 範例
在 API 說明頁面上，除了 URI，您會發現幾個可用呼叫的相關資訊。與 RRS 服務不同，BES 服務是非同步的。這表示 BES API 只是將要執行的工作排入佇列，而呼叫者會輪詢工作的狀態以查看工作何時完成。以下是目前支援的批次工作作業：

1. 建立 (提交) 批次工作
1. 啟動此批次工作
1. 取得批次工作的狀態或結果
1. 取消執行中的批次工作

**1.建立批次執行工作**

建立 Azure Machine Learning 服務端點的批次工作時，可以指定將定義此批次執行的數個參數：

* **Input**：代表儲存批次工作輸入位置的 Blob 參考。
* **GlobalParameters**：代表可用於定義實驗的全域參數集。Azure Machine Learning 實驗可以具有必要和選用參數，以自訂服務的執行，而呼叫者預期會提供所有必要的參數 (如果有這些參數)。這些參數會以索引鍵-值組的集合形式來指定。
* **Outputs**：如果服務已定義一或多個輸出，我們允許呼叫者將任一輸出重新導向至其所選的 Azure Blob 位置。這麼做可讓您將服務的輸出，以可預測的名稱儲存在慣用位置，否則輸出的 Blob 名稱會隨機產生。**注意**：服務會預期輸出內容是根據其類型儲存為支援的格式：
  - 資料集輸出：可以儲存為 **.csv、.tsv、.arff**
  - 定型模型輸出：可以儲存為 **.ilearner**
  
  輸出位置覆寫會指定為 *<output name  blob reference>* 組的集合，其中 *output name* 是特定輸出節點的使用者定義名稱 (服務的 API 說明頁面中也有顯示)，而 *blob reference* 則是 Azure Blob 位置的參考，也是輸出要重新導向的目的地位置。
  
所有這些建立工作的參數均可視您的服務性質來選擇性地使用。例如，沒有定義輸入節點的服務不需要傳入 *Input* 參數，且完全可以選擇是否使用輸出位置覆寫功能；因為如果不使用，輸出也會儲存在您針對 Azure Machine Learning 工作區所設定的預設儲存體帳戶中。以下針對只傳入輸入資訊的服務，示範傳遞至 REST API 的要求承載：

**範例要求**

	{
	  "Input": {
	    "ConnectionString":     
	    "DefaultEndpointsProtocol=https;AccountName=mystorageacct;AccountKey=mystorageacctKey",
	    "RelativeLocation": "/mycontainer/mydatablob.csv",
	    "BaseLocation": null,
	    "SasBlobToken": null
	  },
	  "Outputs": null,
	  "GlobalParameters": null
	}

批次工作建立 API 的回應是與您工作相關聯的唯一工作 ID這個 ID 非常重要，因為它是讓您針對其他作業參考系統中此工作的唯一方法。
  
**範例回應**

	"539d0bc2fde945b6ac986b851d0000f0" // The JOB_ID

**2.啟動批次執行工作**

建立批次工作只會將它登錄在系統內，並呈現「未啟動」狀態。若要實際排程工作來執行，您必須依照服務端點的 API 說明頁面敘述，呼叫 **start** API，並提供建立工作時所取得的工作 ID。
  
**3.取得批次執行工作的狀態**

您可以隨時將工作 ID 傳遞至 GetJobStatus API，藉此輪詢非同步批次工作的狀態。API 回應會包含指標，指出工作的目前狀態，以及實際的批次工作結果 (如果批次工作順利完成)。如果發生錯誤，則會在 *Details* 屬性中傳回失敗背後的實際原因詳細資訊。
 
**回應承載**

	{
	    "StatusCode": STATUS_CODE,
	    "Results": RESULTS,
	    "Details": DETAILS
	}

*StatusCode* 可以是下列其中一項：

* 未啟動
* 執行中
* Failed
* Cancelled
* 已完成

只有工作已順利完成時，才會填入 *Results* 屬性 (否則它會是 **null**)。當工作完成，且服務至少定義一個輸出節點時，傳回的結果會是 [輸出名稱, Blob 參考] 組的集合，其中的 Blob 參考是包含實際結果之 Blob 的 SAS 唯讀參考。

**範例回應**

	{
	    "Status Code": "Finished",
	    "Results":
	    {
	        "dataOutput":
	        {              
	            "ConnectionString": null,
	            "RelativeLocation": "outputs/dataOutput.csv",
	            "BaseLocation": "https://mystorageaccount.blob.core.windows.net/",
	            "SasBlobToken": "?sv=2013-08-15&sr=b&sig=ABCD&st=2015-04-04T05%3A39%3A55Z&se=2015-04-05T05%3A44%3A55Z&sp=r"              
	        },
	        "trainedModelOutput":
	        {              
	            "ConnectionString": null,
	            "RelativeLocation": "models/trainedModel.ilearner",
	            "BaseLocation": "https://mystorageaccount.blob.core.windows.net/",
	            "SasBlobToken": "?sv=2013-08-15&sr=b&sig=EFGH%3D&st=2015-04-04T05%3A39%3A55Z&se=2015-04-05T05%3A44%3A55Z&sp=r"              
	        },           
	    },
	    "Details": null
	}

**4.取消批次執行工作**

您可以隨時呼叫指定的 CancelJob API 並傳入工作 ID，以取消執行中的批次工作。我們可能基於各種原因而這麼做，例如工作花太長時間而無法完成。



#### 使用 [BES SDK](machine-learning-consume-web-services.md#batch-execution-service-sdk)

[BES SDK Nuget 封裝](http://www.nuget.org/packages/Microsoft.Azure.MachineLearning/)提供可以批次模式簡化呼叫 BES 來進行評分的功能。若要安裝 Nuget 封裝，請在 Visual Studio 中移至 [工具]，選取 [Nuget 套件管理員]，然後按一下 [Package Manager Console]。

發佈為 Web 服務的 AzureML 實驗可以包含 Web 服務輸入模組，這表示需要透過 Web 服務呼叫，以 Blob 位置參考的形式提供輸入。另外還有不使用 Web 服務輸入模組的選項，那就是改為使用「讀取器」模組。在此情況下，讀取器通常會在執行階段使用查詢，從 SQL DB 讀取以取得資料。Web 服務參數可用來動態指向其他伺服器或資料表等。SDK 支援以上兩種模式。

下列程式碼範例示範如何使用 BES SDK，針對 Azure Machine Learning 服務端點提交和監視批次工作。請注意有關設定和呼叫的註解。

#### **範例程式碼**

	// This code requires the Nuget package Microsoft.Azure.MachineLearning to be installed.
	// Instructions for doing this in Visual Studio:
	// Tools -> Nuget Package Manager -> Package Manager Console
	// Install-Package Microsoft.Azure.MachineLearning 
	
	  using System;
	  using System.Collections.Generic;
	  using System.Threading.Tasks;
	  
	  using Microsoft.Azure.MachineLearning;
	  using Microsoft.Azure.MachineLearning.Contracts;
	  using Microsoft.Azure.MachineLearning.Exceptions;
	
	namespace CallBatchExecutionService
	{
	    class Program
	    {
	        static void Main(string[] args)
	        {	            
	            InvokeBatchExecutionService().Wait();
	        }
	
	        static async Task InvokeBatchExecutionService()
	        {
	            // First collect and fill in the URI and access key for your web service endpoint.
	            // These are available on your service's API help page.
	            var endpointUri = "https://ussouthcentral.services.azureml.net/workspaces/YOUR_WORKSPACE_ID/services/YOUR_SERVICE_ENDPOINT_ID/";
	            string accessKey = "YOUR_SERVICE_ENDPOINT_ACCESS_KEY";
	
	            // Create an Azure Machine Learning runtime client for this endpoint
	            var runtimeClient = new RuntimeClient(endpointUri, accessKey);
	
	            // Define the request information for your batch job. This information can contain:
	            // -- A reference to the AzureBlob containing the input for your job run
	            // -- A set of values for global parameters defined as part of your experiment and service
	            // -- A set of output blob locations that allow you to redirect the job's results
	
	            // NOTE: This sample is applicable, as is, for a service with explicit input port and
	            // potential global parameters. Also, we choose to also demo how you could override the
	            // location of one of the output blobs that could be generated by your service. You might 
	            // need to tweak these features to adjust the sample to your service.
	            //
	            // All of these properties of a BatchJobRequest shown below can be optional, depending on
	            // your service, so it is not required to specify all with any request.  If you do not want to
	            // use any of the parameters, a null value should be passed in its place.
	            
	            // Define the reference to the blob containing your input data. You can refer to this blob by its
                    // connection string / container / blob name values; alternatively, we also support references 
                    // based on a blob SAS URI
                    
                    BlobReference inputBlob = BlobReference.CreateFromConnectionStringData(connectionString:                                         "DefaultEndpointsProtocol=https;AccountName=YOUR_ACCOUNT_NAME;AccountKey=YOUR_ACCOUNT_KEY",
                        containerName: "YOUR_CONTAINER_NAME",
                        blobName: "YOUR_INPUT_BLOB_NAME");
                              
                    // If desired, one can override the location where the job outputs are to be stored, by passing in
                    // the storage account details and name of the blob where we want the output to be redirected to.
                    
                    var outputLocations = new Dictionary<string, BlobReference>
                        {
                          {
                           "YOUR_OUTPUT_NODE_NAME", 
                           BlobReference.CreateFromConnectionStringData(                                     connectionString: "DefaultEndpointsProtocol=https;AccountName=YOUR_ACCOUNT_NAME;AccountKey=YOUR_ACCOUNT_KEY",
                                containerName: "YOUR_CONTAINER_NAME",
                                blobName: "YOUR_DESIRED_OUTPUT_BLOB_NAME")
                           }
                        };
	            
	            // If applicable, you can also set the global parameters for your service
	            var globalParameters = new Dictionary<string, string>
	            {
	                { "YOUR_GLOBAL_PARAMETER", "PARAMETER_VALUE" }
	            };
	                
	            var jobRequest = new BatchJobRequest
	            {
	                Input = inputBlob,
	                GlobalParameters = globalParameters,
	                Outputs = outputLocations
	            };
	
	            try
	            {
	                // Register the batch job with the system, which will grant you access to a job object
	                BatchJob job = await runtimeClient.RegisterBatchJobAsync(jobRequest);
	
	                // Start the job to allow it to be scheduled in the running queue
	                await job.StartAsync();
	
	                // Wait for the job's completion and handle the output
	                BatchJobStatus jobStatus = await job.WaitForCompletionAsync();
	                if (jobStatus.JobState == JobState.Finished)
	                {
	                    // Process job outputs
	                    Console.WriteLine(@"Job {0} has completed successfully and returned {1} outputs", job.Id, jobStatus.Results.Count);
	                    foreach (var output in jobStatus.Results)
	                    {
	                        Console.WriteLine(@"\t{0}: {1}", output.Key, output.Value.AbsoluteUri);
	                    }
	                }
	                else if (jobStatus.JobState == JobState.Failed)
	                {
	                    // Handle job failure
	                    Console.WriteLine(@"Job {0} has failed with this error: {1}", job.Id, jobStatus.Details);
	                }
	            }
	            catch (ArgumentException aex)
	            {
	                Console.WriteLine("Argument {0} is invalid: {1}", aex.ParamName, aex.Message);
	            }
	            catch (RuntimeException runtimeError)
	            {
	                Console.WriteLine("Runtime error occurred: {0} - {1}", runtimeError.ErrorCode, runtimeError.Message);
	                Console.WriteLine("Error details:");
	                foreach (var errorDetails in runtimeError.Details)
	                {
	                    Console.WriteLine("\t{0} - {1}", errorDetails.Code, errorDetails.Message);
	                }
	            }
	            catch (Exception ex)
	            {
	                Console.WriteLine("Unexpected error occurred: {0} - {1}", ex.GetType().Name, ex.Message);
	            }
	        }
	    }
	}

 

<!---HONumber=July15_HO2-->