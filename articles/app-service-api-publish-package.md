<properties 
	pageTitle="建立 API 應用程式封裝" 
	description="了解如何將 API 應用程式封裝發佈至 Azure Marketplace。" 
	services="app-service\api" 
	documentationCenter=".net"
	authors="guangyang"
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/07/2015" 
	ms.author="guayan"/>

# 將 API 應用程式封裝發佈至 Azure Marketplace

## 概觀

本文說明如何將 API 應用程式封裝發佈至 [Azure Marketplace](http://azure.microsoft.com/marketplace/api-apps/)。

- 若要了解如何建立 API 應用程式，請參閱[使用 Visual Studio 建立 API 應用程式](app-service-dotnet-create-api-app.md)。
- 若要了解如何建立 API 應用程式封裝，請參閱 [建立 API 應用程式封裝](app-service-api-create-package)。

## 一般發佈流程

以下是一般發佈流程

1. 依照 [建立 API 應用程式封裝](app-service-api-create-package)教學課程中的指示，為您的 API 應用程式建立 Nuget 封裝。
2. 將它發佈至 Nuget 組件庫 (https://apiapps.nuget.org)。
3. 然後會自動同步至 [Azure Marketplace](http://azure.microsoft.com/marketplace/api-apps/)。
4. 移至 [Azure Marketplace](http://azure.microsoft.com/marketplace/api-apps/) 和 [Azure 預覽入口網站](https://portal.azure.com)以確認端點對端點體驗。

## 發佈至 Nuget 組件庫

1. 移至 https://apiapps.nuget.org。

    ![Nuget 組件庫首頁](./media/app-service-api-publish-package/nuget-homepage.png)

2. 按一下 [**登入**] 並使用您的 Azure 帳戶進行登入。
3. 按一下 [**API 應用程式**] 索引標籤，按一下 [**上傳 API 應用程式**]，然後上傳您的 API 應用程式封裝。

    ![Nuget 組件庫上傳封裝頁面](./media/app-service-api-publish-package/nuget-upload-package-page.png)

4. 針對 [**命名空間**]，下拉式清單會顯示您的 Azure 帳戶的 Azure Active Directory 租用戶的所有已驗證網域。請務必挑選一個符合 API 應用程式封裝的 apiapp.json 檔案中命名空間屬性的網域。這可確保發佈者為其 API 應用程式封裝宣告有效的命名空間。
5. 請確定未核取 [**取消列出此 API 應用程式**]。
6. 按一下 [發行]****。
7. 如有任何驗證錯誤，請加以修正並再次上傳。

## 在 Azure Marketplace 中檢視 API 應用程式封裝

幾分鐘後，您的 API 應用程式封裝就會同步至 Azure Marketplace。您可以移到 [這裡](http://azure.microsoft.com/marketplace/api-apps/)來確認標題、描述、圖示等。如果您需變更任何項目，只要在您的 API 應用程式封裝中進行變更並重新發佈。

![Azure Marketplace API 應用程式頁面](./media/app-service-api-publish-package/acom-marketplace-apiapp-page.png)

## 在 Azure 預覽入口網站中部署 API 應用程式封裝

您也可以使用 Azure 帳戶 (可與您用來發佈 API 應用程式封裝的帳戶不同) 登入 [Azure 預覽入口網站](https://portal.azure.com)。您可以在此驗證 Azure 預覽入口網站是否建立您的 API 應用程式封裝體驗。如果您需變更任何項目，只要在您的 API 應用程式封裝中進行變更並重新發佈。

如需如何在 Azure 入口網站中部署 API 應用程式封裝的詳細資訊，請[在此](app-service-api-connnect-your-app-to-saas-connector.md)參閱部署 DropboxConnector 的範例。

<!--HONumber=52-->
