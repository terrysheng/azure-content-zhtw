<properties
   pageTitle="Publish-WebApplicationWebSite (Windows PowerShell 指令碼) | Microsoft Azure"
   description="了解如何將 Web 專案發佈至 Azure 網站。此指令碼會在您的 Azure 訂用帳戶中建立所需的資源 (如果它們不存在)。"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="12/17/2015"
   ms.author="tarcher" />

# Publish-WebApplicationWebSite (Windows PowerShell 指令碼)

##語法

將 Web 專案發佈至 Azure 網站。指令碼會在您的 Azure 訂用帳戶中建立所需的資源 (如果它們不存在)。

	Publish-WebApplicationWebSite
	–Configuration <configuration>
	-SubscriptionName <subscriptionName>
	-WebDeployPackage <packageName>
	-DatabaseServerPassword @{Name = "name"; Password = "password"}
	-SendHostMessagesToOutput
	-Verbose


## 組態

描述部署詳細資訊的 JSON 組態檔路徑。

|參數|預設值|
|---|---|
|別名|無|
|必要？|true|
|位置|已命名|
|預設值|無|
|接受管線輸入？|false|
|接受萬用字元？|false|

## SubscriptionName

您要建立網站的 Azure 訂用帳戶名稱。

|參數|預設值|
|---|---|
|別名|無|
|必要？|false|
|位置|已命名|
|預設值|無|
|接受管線輸入？|false|
|接受萬用字元？|false|

## WebDeployPackage

要發佈至網站的 Web 部署封裝路徑。您可以使用 Visual Studio 的 [發佈 Web] 精靈來建立此封裝。如需詳細資訊，請參閱[開始使用 Azure 雲端服務和 ASP.NET](http://go.microsoft.com/fwlink/p/?LinkID=623089)。

|參數|預設值|
|---|---|
|別名|無|
|必要？|false|
|位置|已命名|
|預設值|無|
|接受管線輸入？|false|
|接受萬用字元？|false|

## DatabaseServerPassword

在 Azure 中 SQL Database 的使用者名稱和密碼。

|參數|預設值|
|---|---|
|別名|無|
|必要？|false|
|位置|已命名|
|預設值|無|
|接受管線輸入？|false|
|接受萬用字元？|false|

## SendHostMessagesToOutput

如果為 true，將訊息從指令碼列印至輸出資料流。

|參數|預設值|
|---|---|
|別名|無|
|必要？|false|
|位置|已命名|
|預設值|false|
|接受管線輸入？|false|
|接受萬用字元？|false|

## 備註

如需如何使用指令碼來建立開發和測試環境的完整說明，請參閱[使用 Windows PowerShell 指令碼來發佈至開發和測試環境](vs-azure-tools-publishing-using-powershell-scripts.md)。

JSON 組態檔會指定待部署項目的詳細資料。它會包含您在建立專案時所指定的資訊，例如網站的名稱和使用者名稱。它還包含要佈建的資料庫 (如果有的話)。下列程式碼片段將顯示一個 JSON 組態檔範例：

	{
	    "environmentSettings": {
	        "webSite": {
	            "name": "WebApplication10554",
	            "location": "West US"
	        },
	        "databases": [
	            {
	                "connectionStringName": "DefaultConnection",
	                "databaseName": "WebApplication10554_db",
	                "serverName": "iss00brc88",
	                "user": "sqluser2",
	                "password": "",
	                "edition": "",
	                "size": "",
	                "collation": "",
	                "location": "West US"
	            }
	        ]
	    }
	}

您可以編輯 JSON 組態檔來變更部署項目。[網站] 區段是必要項目，但 [資料庫] 區段是選用項目。

## 後續步驟

如需詳細資訊，請參閱 [Publish-WebApplicationVM (Windows PowerShell 指令碼)](vs-azure-tools-publish-webapplicationvm.md)

<!---HONumber=AcomDC_1223_2015-->