<properties 
	pageTitle="從 Azure Data Factory 在 Azure 資料湖分析上執行 U-SQL 指令碼" 
	description="了解如何在 Azure 資料湖分析計算服務上執行 U-SQL 指令碼來處理資料。" 
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
	ms.date="01/19/2016" 
	ms.author="spelluru"/>

# 從 Azure Data Factory 在 Azure 資料湖分析上執行 U-SQL 指令碼 
Azure Data Factory 中的「管線」會使用連結的計算服務，來處理連結的儲存體服務中的資料。它包含一系列活動，其中每個活動都會執行特定的處理作業。本文將說明**資料湖分析 U-SQL 活動**，它在 **Azure 資料湖分析**計算連結的服務上執行 **U-SQL** 指令碼。

> [AZURE.NOTE] 
您必須先建立 Azure 資料湖分析帳戶，才能使用資料湖分析 U-SQL 活動建立管線。若要深入了解 Azure 資料湖分析，請參閱[開始使用 Azure 資料湖分析](../data-lake-analytics/data-lake-analytics-get-started-portal.md)。
>  
> 請檢閱[建置您的第一個管線教學課程](data-factory-build-your-first-pipeline.md)，以了解建立 Data Factory、連結服務、資料集和管線的詳細步驟。搭配使用 JSON 片段和 Data Factory 編輯器或 Visual Studio 或 Azure PowerShell 來建立 Data Factory 實體。

## Azure 資料湖分析連結服務
您建立 **Azure 資料湖分析**連結服務，將 Azure 資料湖分析計算服務連結至 Azure Data Factory，再使用管線中的資料湖分析 U-SQL 活動。

下列範例提供 Azure 資料湖分析連結服務的 JSON 定義。

	{
	    "name": "AzureDataLakeAnalyticsLinkedService",
	    "properties": {
	        "type": "AzureDataLakeAnalytics",
	        "typeProperties": {
	            "accountName": "adftestaccount",
	            "dataLakeAnalyticsUri": "datalakeanalyticscompute.net",
	            "authorization": "<authcode>",
				"sessionId": "<session ID>", 
	            "subscriptionId": "<subscription id>",
	            "resourceGroupName": "<resource group name>"
	        }
	    }
	}


下表提供 JSON 定義中所使用之屬性的描述：

屬性 | 說明 | 必要
-------- | ----------- | --------
類型 | type 屬性應設為：**AzureDataLakeAnalytics**。 | 是
accountName | Azure 資料湖分析帳戶名稱。 | 是
dataLakeAnalyticsUri | Azure 資料湖分析 URI。 | 否 
authorization | 按一下 Data Factory 編輯器中的 [授權] 按鈕並完成 OAuth 登入後，即會自動擷取授權碼。 | 是 
subscriptionId | Azure 訂用帳戶識別碼 | 否 (如果未指定，便會使用 Data Factory 的訂用帳戶)。 
resourceGroupName | Azure 資源群組名稱 | 否 (若未指定，便會使用 Data Factory 的資源群組)。
sessionId | OAuth 授權工作階段的工作階段識別碼。每個工作階段識別碼都是唯一的，只能使用一次。這是在 Data Factory 編輯器中自動產生。 | 是

您使用 [授權] 按鈕所產生的授權碼在一段時間後會到期。請參閱下表以了解不同類型的使用者帳戶的到期時間。當驗證**權杖到期**時，您可能會看到下列錯誤訊息：認證作業發生錯誤：invalid\_grant - AADSTS70002：驗證認證時發生錯誤。AADSTS70008：提供的存取授權已過期或撤銷。追蹤識別碼：d18629e8-af88-43c5-88e3-d8419eb1fca1 相互關連識別碼：fac30a0c-6be6-4e02-8d69-a776d2ffefd7 時間戳記：2015-12-15 21:09:31Z

 
| 使用者類型 | 到期時間 |
| :-------- | :----------- | 
| 不受 Azure Active Directory 管理的使用者帳戶 (@hotmail.com、@live.com 等) | 12 小時 |
| 受 Azure Active Directory (AAD) 管理的使用者帳戶 | | 最後一次執行配量後的 14 天。<p>如果以 OAuth 式連結服務為基礎的配量至少每 14 天執行一次，則為 90 天。</p> |

如果要避免/解決此錯誤，您必須在**權杖到期**時使用 [**授權**] 按鈕重新授權，然後重新部署連結服務。您也可以使用下一節中的程式碼以程式設計方式產生 **sessionId** 和 **authorization** 屬性的值。

  
### 若要以程式設計方式產生 sessionId 與 authorization 的值 

    if (linkedService.Properties.TypeProperties is AzureDataLakeStoreLinkedService ||
        linkedService.Properties.TypeProperties is AzureDataLakeAnalyticsLinkedService)
    {
        AuthorizationSessionGetResponse authorizationSession = this.Client.OAuth.Get(this.ResourceGroupName, this.DataFactoryName, linkedService.Properties.Type);

        WindowsFormsWebAuthenticationDialog authenticationDialog = new WindowsFormsWebAuthenticationDialog(null);
        string authorization = authenticationDialog.AuthenticateAAD(authorizationSession.AuthorizationSession.Endpoint, new Uri("urn:ietf:wg:oauth:2.0:oob"));

        AzureDataLakeStoreLinkedService azureDataLakeStoreProperties = linkedService.Properties.TypeProperties as AzureDataLakeStoreLinkedService;
        if (azureDataLakeStoreProperties != null)
        {
            azureDataLakeStoreProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
            azureDataLakeStoreProperties.Authorization = authorization;
        }

        AzureDataLakeAnalyticsLinkedService azureDataLakeAnalyticsProperties = linkedService.Properties.TypeProperties as AzureDataLakeAnalyticsLinkedService;
        if (azureDataLakeAnalyticsProperties != null)
        {
            azureDataLakeAnalyticsProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
            azureDataLakeAnalyticsProperties.Authorization = authorization;
        }
    }

請參閱 [AzureDataLakeStoreLinkedService 類別](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx)、[AzureDataLakeAnalyticsLinkedService 類別](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)和 [AuthorizationSessionGetResponse 類別](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx)主題，以取得在程式碼中使用的 Data Factory 類別的詳細資料。您必須針對 WindowsFormsWebAuthenticationDialog 類別將參考新增至：Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll。
 
 
## 資料湖分析 U-SQL 活動 

下列 JSON 片段會定義具有資料湖分析 U-SQL 活動的管線。活動定義具有您稍早建立的 Azure 資料湖分析連結服務的參考。
  

	{
    	"name": "ComputeEventsByRegionPipeline",
    	"properties": {
        	"description": "This is a pipeline to compute events for en-gb locale and date less than 2012/02/19.",
        	"activities": 
			[
            	{
            	    "type": "DataLakeAnalyticsU-SQL",
                	"typeProperties": {
                    	"scriptPath": "scripts\\kona\\SearchLogProcessing.txt",
	                    "scriptLinkedService": "StorageLinkedService",
    	                "degreeOfParallelism": 3,
    	                "priority": 100,
    	                "parameters": {
    	                    "in": "/datalake/input/SearchLog.tsv",
    	                    "out": "/datalake/output/Result.tsv"
    	                }
    	            },
    	            "inputs": [
	    				{
	                        "name": "DataLakeTable"
	                    }
	                ],
	                "outputs": 
					[
	                    {
                    	    "name": "EventsByRegionTable"
                    	}
                	],
                	"policy": {
                    	"timeout": "06:00:00",
	                    "concurrency": 1,
    	                "executionPriorityOrder": "NewestFirst",
    	                "retry": 1
    	            },
    	            "scheduler": {
    	                "frequency": "Day",
    	                "interval": 1
    	            },
    	            "name": "EventsByRegion",
    	            "linkedServiceName": "AzureDataLakeAnalyticsLinkedService"
    	        }
    	    ],
    	    "start": "2015-08-08T00:00:00Z",
    	    "end": "2015-08-08T01:00:00Z",
    	    "isPaused": false
    	}
	}


下表描述此活動特有的屬性之名稱和描述。

屬性 | 說明 | 必要
:-------- | :----------- | :--------
類型 | 類型屬性必須設為 **DataLakeAnalyticsU-SQL**。 | 是
scriptPath | 包含 U-SQL 指令碼的資料夾的路徑。請注意，檔案名稱區分大小寫。 | 否 (如果您使用指令碼)
scriptLinkedService | 連結服務會連結包含 Data Factory 的指令碼的儲存體 | 否 (如果您使用指令碼)
script | 指定內嵌指令碼而不是指定 scriptPath 和 scriptLinkedService。例如："script" : "CREATE DATABASE test"。 | 否 (如果您使用 scriptPath 和 scriptLinkedService)
degreeOfParallelism | 同時用來執行工作的節點數目上限。 | 否
優先順序 | 判斷應該選取排入佇列的哪些工作首先執行。編號愈低，優先順序愈高。 | 否 
參數 | U-SQL 指令碼的參數 | 否 

指令碼定義請參閱 [SearchLogProcessing.txt 指令碼定義](#script-definition)。

### 建立輸入和輸出資料集

#### 輸入資料集
在此範例中，輸入的資料是位於 Azure 資料湖存放區 (datalake/input 資料夾中的 SearchLog.tsv 檔案)。

	{
    	"name": "DataLakeTable",
	    "properties": {
	        "type": "AzureDataLakeStore",
    	    "linkedServiceName": "AzureDataLakeStoreLinkedService",
    	    "typeProperties": {
    	        "folderPath": "datalake/input/",
    	        "fileName": "SearchLog.tsv",
    	        "format": {
    	            "type": "TextFormat",
    	            "rowDelimiter": "\n",
    	            "columnDelimiter": "\t"
    	        }
    	    },
    	    "availability": {
    	        "frequency": "Day",
    	        "interval": 1
    	    }
    	}
	}	

#### 輸出資料集
在此範例中，U-SQL 指令碼所產生的輸出資料會儲存在 Azure 資料湖存放區 (datalake/output 資料夾)。

	{
	    "name": "EventsByRegionTable",
	    "properties": {
	        "type": "AzureDataLakeStore",
	        "linkedServiceName": "AzureDataLakeStoreLinkedService",
	        "typeProperties": {
	            "folderPath": "datalake/output/"
	        },
	        "availability": {
	            "frequency": "Day",
	            "interval": 1
	        }
	    }
	}

#### 範例 Azure 資料湖存放區連結服務
以下是上述輸入/輸出資料集所使用的範例 Azure 資料湖存放區連結服務的定義。

	{
	    "name": "AzureDataLakeStoreLinkedService",
	    "properties": {
	        "type": "AzureDataLakeStore",
	        "typeProperties": {
	            "dataLakeUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
				"sessionId": "<session ID>",
	            "authorization": "<authorization URL>"
	        }
	    }
	}

請參閱[移動 Azure 資料湖存放區的資料](data-factory-azure-datalake-connector.md)以取得上述的 Azure 資料湖存放區連結服務中的 JSON 屬性和資料集 JSON 片段的說明。

### 指令碼定義

	@searchlog =
	    EXTRACT UserId          int,
	            Start           DateTime,
	            Region          string,
	            Query           string,
	            Duration        int?,
	            Urls            string,
	            ClickedUrls     string
	    FROM @in
	    USING Extractors.Tsv(nullEscape:"#NULL#");
	
	@rs1 =
	    SELECT Start, Region, Duration
	    FROM @searchlog
	WHERE Region == "en-gb";
	
	@rs1 =
	    SELECT Start, Region, Duration
	    FROM @rs1
	    WHERE Start <= DateTime.Parse("2012/02/19");
	
	OUTPUT @rs1   
	    TO @out
	      USING Outputters.Tsv(quoting:false, dateTimeFormat:null);

ADF 會使用「parameters」區段動態傳遞上述 U-SQL 指令碼中 **@in** 和 **@out** 參數的值。請參閱上述管線定義中的 ‘parameters’ 一節。

您可以指定其他屬性 (即 degreeOfParallelism、priority 等)，以及 Azure 資料湖分析服務上執行之作業的管線定義中的屬性。

<!---HONumber=AcomDC_0302_2016-->