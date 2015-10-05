<properties
    pageTitle="我的雲端服務專案發生什麼狀況？| Microsoft Azure | Visual Studio 已連接服務"
	description="說明使用 Visual Studio 已連接服務連接至 Azure 儲存體帳戶後，雲端服務專案發生的狀況"
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

# 我的雲端服務專案發生什麼狀況 (Visual Studio Azure 儲存體已連接服務)？

> [AZURE.SELECTOR]
> - [Getting started](vs-storage-cloud-services-getting-started-blobs.md)
> - [What happened](vs-storage-cloud-services-what-happened.md)


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

## 加入 Azure 儲存體的連接字串
已使用所選儲存體帳戶的連接字串和金鑰建立元素。已修改下列檔案：

- **ServiceDefinition.csdef**
- **ServiceConfiguration.Cloud.cscfg**
- **ServiceConfiguration.Local.cscfg**

<!---HONumber=Sept15_HO4-->