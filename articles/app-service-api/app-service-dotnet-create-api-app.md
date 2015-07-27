<properties
	pageTitle="在 Azure App Service 中建立 ASP.NET API 應用程式"
	description="了解如何在 Azure App Service 使用 Visual Studio 2013 建立 ASP.NET API 應用程式"
	services="app-service\api"
	documentationCenter=".net"
	authors="bradygaster"
	manager="wpickett"
	editor="jimbe"/>

<tags
	ms.service="app-service-api"
	ms.workload="web"
	ms.tgt_pltfrm="dotnet"
	ms.devlang="na"
	ms.topic="get-started-article" 
	ms.date="05/19/2015"
	ms.author="bradyg;tarcher"/>

# 在 Azure App Service 中建立 ASP.NET API 應用程式

> [AZURE.SELECTOR]
- [Visual Studio 2013](app-service-dotnet-create-api-app.md)
- [Visual Studio 2015 RC](app-service-dotnet-create-api-app-vs2015.md)
- [Visual Studio Code](app-service-create-aspnet-api-app-using-vscode.md)

## 概觀

本教學課程顯示如何使用 Visual Studio 2013 樣本建立 ASP.NET Web API 專案，而此範本設定將專案部署至雲端，以做為 [Azure App Service](../app-service/app-service-value-prop-what-is.md) 中的 [API 應用程式](app-service-api-apps-why-best-platform.md)。如需如何將現有 Web API 專案設定成部署為 API 應用程式的相關資訊，請參閱[將 Web API 專案設定為 API 應用程式](app-service-dotnet-create-api-app-visual-studio.md)。

系列中的後續教學課程顯示如何[部署](app-service-dotnet-deploy-api-app.md)和[偵錯](../app-service-dotnet-remotely-debug-api-app.md)您已在本教學課程中建立的 API 應用程式專案。

[AZURE.INCLUDE [install-sdk-2013-only](../../includes/install-sdk-2013-only.md)]

本教學課程需要 2.5.1 版或更新版本的 Azure SDK for.NET。

## 建立 API 應用程式專案

指示您輸入專案名稱時，請輸入 *ContactsList*。

[AZURE.INCLUDE [app-service-api-create](../../includes/app-service-api-create.md)]

[AZURE.INCLUDE [app-service-api-review-metadata](../../includes/app-service-api-review-metadata.md)]

[AZURE.INCLUDE [app-service-api-define-api-app](../../includes/app-service-api-define-api-app.md)]

[AZURE.INCLUDE [app-service-api-direct-deploy-metadata](../../includes/app-service-api-direct-deploy-metadata.md)]

## 後續步驟

現在已準備好部署您的 API 應用程式，而且您可以依照[部署 API 應用程式](app-service-dotnet-deploy-api-app.md)教學課程進行部署。
 

<!---HONumber=July15_HO3-->