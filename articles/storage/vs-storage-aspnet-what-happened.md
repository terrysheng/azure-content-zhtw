<properties
	pageTitle="我的 ASP.NET 專案 (Visual Studio Azure 儲存體連接的服務) 發生什麼狀況 | Microsoft Azure | Visual Studio 已連接服務"
	description="說明使用 Visual Studio 已連接服務將 Azure 儲存體新增至 ASP.NET 專案後，會發生什麼事"
	services="storage"
	documentationCenter=""
	authors="patshea123"
	manager="douge"
	editor="tglee"/>

<tags
	ms.service="storage"
	ms.workload="web"
	ms.tgt_pltfrm="vs-what-happened"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/03/2015"
	ms.author="patshea"/>

# 我的 ASP.NET 專案 (Visual Studio Azure 儲存體連接的服務) 發生什麼狀況？

> [AZURE.SELECTOR]
> - [Getting started](vs-storage-aspnet-getting-started-blobs.md)
> - [What happened](vs-storage-aspnet-what-happened.md)

## 加入參考

Azure 儲存體 NuGet 封裝已加入至 Visual Studio 專案。這個封裝會加入下列 .NET 參考：

- **Microsoft.Data.Edm**
- **Microsoft.Data.OData**
- **Microsoft.Data.Services.Client**
- **Microsoft.WindowsAzure.Configuration**
- **Microsoft.WindowsAzure.Storage**
- **Newtonsoft.Json**
- **System.Data**
- **System.Spatial**

##加入 Azure 儲存體的連接字串
在專案的 web.config 檔案中，已使用所選儲存體帳戶的連接字串和金鑰建立一個元素。

如需詳細資訊，請參閱 [ASP.NET](http://www.asp.net)。

<!---HONumber=Oct15_HO3-->