<properties 
   pageTitle="使用 Node.js | Azure 的 Azure SDK 管理 Azure 資料湖存放區" 
   description="了解如何管理資料湖存放區帳戶以及檔案系統。" 
   services="data-lake-store" 
   documentationCenter="" 
   authors="mumian" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="12/11/2015"
   ms.author="jgao"/>

# 使用 Node.js 的 Azure SDK 管理 Azure 資料湖存放區

> [AZURE.SELECTOR]
- [Using Portal](data-lake-store-get-started-portal.md)
- [Using PowerShell](data-lake-store-get-started-powershell.md)
- [Using .NET SDK](data-lake-store-get-started-net-sdk.md)
- [Using Azure CLI](data-lake-store-get-started-cli.md)
- [Using Node.js](data-lake-store-manage-use-nodejs.md)


Node.js 的 Azure ADK 可用於管理 Azure 資料湖存放區帳戶，以及檔案系統：

- 帳戶管理：建立、取得、列出、更新及刪除。
- 檔案系統管理：建立、取得、上傳、附加、下載、讀取、刪除、列出。

**必要條件**

開始進行本教學課程之前，您必須具備下列條件：

- **Azure 訂用帳戶**。請參閱[取得 Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。
- **Azure 資料湖存放區帳戶**。請參閱[使用 Azure 入口網站開始使用 Azure 資料湖存放區](data-lake-store-get-started-portal.md)，以建立帳戶。
- **具備存取資料湖分析帳戶權限的服務主體**。請參閱[使用 Azure 資源管理員驗證服務主體](resource-group-authenticate-service-principal.md)。

## 安裝 SDK

若要安裝 SDK，請使用下列步驟：

1. 安裝 [Node.js](https://nodejs.org/)。
2. 從命令提示字元、終端機視窗或 Bash 視窗，執行下列命令：

		npm install async
		npm install adal-node
		npm install azure-common
		npm install azure-arm-datalake-store
	
## Node.js 範例

下列範例會在資料湖存放區帳戶中建立檔案，並將資料附加到該檔案。

	var async = require('async');
	var adalNode = require('adal-node');
	var azureCommon = require('azure-common');
	var azureDataLakeStore = require('azure-arm-datalake-store');
	
	var resourceUri = 'https://management.core.windows.net/';
	var loginUri = 'https://login.windows.net/'
	
	var clientId = 'application_id_(guid)';
	var clientSecret = 'application_password';
	
	var tenantId = 'aad_tenant_id';
	var subscriptionId = 'azure_subscription_id';
	var resourceGroup = 'adls_resourcegroup_name';
	
	var accountName = 'adls_account_name';
	
	var context = new adalNode.AuthenticationContext(loginUri+tenantId);
	
	var client;
	var response;
	
	var destinationFilePath = '/newFileName.txt';
	var content = 'desired file contents';
	
	async.series([
		function (next) {
			context.acquireTokenWithClientCredentials(resourceUri, clientId, clientSecret, function(err, result){
				if (err) throw err;
				response = result;
				next();
			});
		},
		function (next) {
			var credentials = new azureCommon.TokenCloudCredentials({
				subscriptionId : subscriptionId,
				authorizationScheme : response.tokenType,
				token : response.accessToken
			});
		
			client = azureDataLakeStore.createDataLakeStoreFileSystemManagementClient(credentials, 'azuredatalakestore.net');
	
			next();
		},
		function (next) {
			client.fileSystem.directCreate(destinationFilePath, accountName, content, function(err, result){
				if (err) throw err;
			});
		}
	]);


##另請參閱 

- [Azure SDK for Node.js](http://azure.github.io/azure-sdk-for-node/)
- [使用 Node.js 管理 Azure 資料湖分析](data-lake-analytics-use-nodejs.md)

<!---HONumber=AcomDC_1217_2015-->