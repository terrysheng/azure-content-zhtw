<properties 
   pageTitle="使用 Node.js | Azure 的 Azure SDK 管理 Azure 資料湖分析" 
   description="了解如何使用 Node.js 的 Azure SDK，管理資料湖分析帳戶、資料來源、工作與使用者" 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="mumian" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="12/11/2015"
   ms.author="jgao"/>

# 使用 Node.js 的 Azure SDK 管理 Azure 資料湖分析


[AZURE.INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Node.js 的 Azure ADK 可用於管理 Azure 資料湖分析帳戶、工作與目錄。若要使用其他工具查看管理主題，請按一下上方選取的索引標籤。

**必要條件**

開始進行本教學課程之前，您必須具備下列條件：

- **Azure 訂用帳戶**。請參閱[取得 Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。
- **Azure 資料湖分析帳戶**。請參閱[使用 Azure 入口網站開始使用 Azure 資料湖分析](data-lake-analytics-get-started-portal.md)，以建立帳戶。
- **具備存取資料湖分析帳戶權限的服務主體**。請參閱[使用 Azure 資源管理員驗證服務主體](resource-group-authenticate-service-principal.md)。

## 安裝 SDK

若要安裝 SDK，請使用下列步驟：

1. 安裝 [Node.js](https://nodejs.org/)。
2. 從命令提示字元、終端機視窗或 Bash 視窗，執行下列命令：

		npm install async
		npm install adal-node
		npm install azure-common
		npm install azure-arm-datalake-analytics
	
## Node.js 範例

下列範例會取得指定的 Azure 資料湖分析帳戶之作業清單。

	var async = require('async');
	var adalNode = require('adal-node');
	var azureCommon = require('azure-common');
	var azureDataLakeAnalytics = require('azure-arm-datalake-analytics');
	
	var resourceUri = 'https://management.core.windows.net/';
	var loginUri = 'https://login.windows.net/'
	
	var clientId = 'application_id_(guid)';
	var clientSecret = 'application_password';
	
	var tenantId = 'aad_tenant_id';
	var subscriptionId = 'azure_subscription_id';
	var resourceGroup = 'adla_resourcegroup_name';
	
	var accountName = 'adla_account_name';
	
	var context = new adalNode.AuthenticationContext(loginUri+tenantId);
	
	var client;
	var response;
	
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
		
			client = azureDataLakeAnalytics.createDataLakeAnalyticsJobManagementClient(credentials, 'azuredatalakeanalytics.net');
	
			next();
		},
		function (next) {
			client.jobs.list(resourceGroup, accountName, function(err, result){
				if (err) throw err;
				console.log(result);
			});
		}
	]);


##另請參閱 

- [Azure SDK for Node.js](http://azure.github.io/azure-sdk-for-node/)
- [Microsoft Azure 資料湖分析概觀](data-lake-analytics-overview.md)
- [使用 Azure 入口網站開始使用資料湖分析](data-lake-analytics-get-started-portal.md)
- [使用 Azure 入口網站管理 Azure 資料湖分析](data-lake-analytics-use-portal.md)
- [使用 Azure 入口網站監視和疑難排解 Azure 資料湖分析作業](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

<!---HONumber=AcomDC_1217_2015-->