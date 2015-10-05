<properties
	pageTitle="在 Azure App Service | Microsoft Azure 中建立 ASP.NET API 應用程式"
	description="了解如何在 Azure App Service 使用 Visual Studio 2013 建立 ASP.NET API 應用程式。"
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
	ms.topic="hero-article"
	ms.date="08/14/2015"
	ms.author="tdykstra"/>

# 在 Azure App Service 中建立 ASP.NET API 應用程式

> [AZURE.SELECTOR]
- [.NET - Visual Studio 2015](app-service-dotnet-create-api-app.md)
- [.NET - Visual Studio Code](app-service-create-aspnet-api-app-using-vscode.md)
- [Node.js](app-service-api-nodejs-api-app.md)
- [Java](app-service-api-java-api-app.md)

## 概觀

本教學課程顯示如何建立 ASP.NET Web API 專案，而此專案設定要部署到雲端成為 [Azure App Service 中的 API 應用程式](app-service-api-apps-why-best-platform.md)。如需如何將現有 Web API 專案設定成部署為 API 應用程式的相關資訊，請參閱[將 Web API 專案設定為 API 應用程式](app-service-dotnet-create-api-app-visual-studio.md)。

系列中的後續教學課程顯示如何[部署](app-service-dotnet-deploy-api-app.md)和[偵錯](../app-service-dotnet-remotely-debug-api-app.md)您已在本教學課程中建立的 API 應用程式專案。

[AZURE.INCLUDE [install-sdk-2015-2013](../../includes/install-sdk-2015-2013.md)]

本教學課程需要 2.6 版或更新版本的 Azure SDK for.NET。

## 建立 API 應用程式專案

指示您輸入專案名稱時，請輸入 **ContactsList**。

[AZURE.INCLUDE [app-service-api-create](../../includes/app-service-api-create.md)]

[AZURE.INCLUDE [app-service-api-review-metadata](../../includes/app-service-api-review-metadata.md)]

[AZURE.INCLUDE [app-service-api-define-api-app](../../includes/app-service-api-define-api-app.md)]

[AZURE.INCLUDE [app-service-api-direct-deploy-metadata](../../includes/app-service-api-direct-deploy-metadata.md)]

## 後續步驟

現在已準備好部署您的 API 應用程式，而且您可以依照[部署 API 應用程式](app-service-dotnet-deploy-api-app.md)教學課程進行部署。

<!---HONumber=Sept15_HO4-->