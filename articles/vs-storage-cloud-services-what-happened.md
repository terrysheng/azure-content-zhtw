<properties 
	pageTitle="開始使用 Azure 儲存體" 
	description="描述在 Visual Studio 雲端服務專案中建立 Azure 儲存體時會發生什麼情況"  
	services="storage" 
	documentationCenter="" 
	authors="kempb" 
	manager="douge" 
	editor="tglee"/>

<tags 
	ms.service="storage" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-what-happened" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/02/2015" 
	ms.author="kempb"/>

> [AZURE.SELECTOR]
> - [開始使用](vs-storage-cloud-services-getting-started-blobs.md)
> - [發生什麼情形](vs-storage-cloud-services-what-happened.md)

###<span id="whathappened">我的專案發生什麼情形？</span>

###### 加入參考

Azure 儲存體 NuGet 封裝已加入至 Visual Studio 專案。  
這個封裝會加入下列 .NET 參考：

- `Microsoft.Data.Edm`
- `Microsoft.Data.OData`
- `Microsoft.Data.Services.Client`
- `Microsoft.WindowsAzure.Configuration`
- `Microsoft.WindowsAzure.Storage`
- `Newtonsoft.Json`
- `System.Data`
- `System.Spatial`

######加入 Azure 儲存體的連接字串 
已使用所選儲存體帳戶的連接字串和金鑰建立元素。已修改下列檔案：

- `ServiceDefinition.csdef`
- `ServiceConfiguration.Cloud.cscfg`
- `ServiceConfiguration.Local.cscfg`


<!--HONumber=46--> 
