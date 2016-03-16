<properties
	pageTitle="使用 Visual Studio 連接服務加入行動服務之後，我的 .NET 專案發生什麼狀況 | Microsoft Azure"
	description="描述使用連接服務加入 Azure 行動服務之後，您的 Visual Studio.NET 專案發生什麼狀況"
	services="mobile-services"
	documentationCenter=""
	authors="mlhoop"
	manager="douge"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="01/05/2016"
	ms.author="mlearned"/>

# 使用連接服務加入 Azure 行動服務之後，我的 Visual Studio.NET 專案發生什麼狀況？

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

## 加入參考

Azure 行動服務 NuGet 封裝加入至專案。因此，下列 .NET 參考已加入至專案：

- **Microsoft.WindowsAzure.Mobile**
- **Microsoft.WindowsAzure.Mobile.Ext**
- **Newtonsoft.Json**
- **System.Net.Http.Extensions**
- **System.Net.Http.Primitives**

## 行動服務的連接字串值

在 App.xaml.cs 檔案中，已使用所選行動服務的應用程式 URL 和應用程式金鑰建立 **MobileServiceClient** 物件。

## 已加入行動服務專案

如果在連線服務提供者中建立 .NET 行動服務，則會建立行動服務專案並將其加入至方案。


[深入了解行動服務](https://azure.microsoft.com/documentation/services/mobile-services/)

<!---HONumber=AcomDC_0128_2016-->