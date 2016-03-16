<properties
	pageTitle="我的 WebJob 專案 (Visual Studio Azure 儲存體連接的服務) 發生什麼狀況？| Microsoft Azure"
	description="說明使用 Visual Studio 已連接服務連接到儲存體帳戶後，會在 Azure WebJob 專案中發生什麼事"
	services="storage"
	documentationCenter=""
	authors="TomArcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="storage"
	ms.workload="web"
	ms.tgt_pltfrm="vs-what-happened"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/21/2016"
	ms.author="tarcher"/>

# 我的 WebJob 專案 (Visual Studio Azure 儲存體連接的服務) 發生什麼狀況？

## 加入參考

Azure 儲存體 NuGet 封裝已加入至 Visual Studio 專案或在其中更新。這個封裝會加入下列 .NET 參考：

- **Microsoft.Data.Edm**
- **Microsoft.Data.OData**
- **Microsoft.Data.Services.Client**
- **Microsoft.WindowsAzure.ConfigurationManager**
- **Microsoft.WindowsAzure.Storage**
- **Newtonsoft.Json**
- **System.Data**
- **System.Spatial**

## 加入 Azure 儲存體的連接字串
在專案的 App.config 檔案中，已使用所選儲存體帳戶的連接字串和金鑰更新 **AzureWebJobsStorage** 和 **AzureWebJobsDashboard** 項目。

如需詳細資訊，請參閱 [Azure WebJobs 文件資源](http://go.microsoft.com/fwlink/?linkid=390226)。

<!---HONumber=AcomDC_0224_2016-->