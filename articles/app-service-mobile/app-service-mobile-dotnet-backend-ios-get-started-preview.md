<properties
	pageTitle="在 Azure Mobile Apps 上建立 iOS 應用程式"
	description="遵循此教學課程，開始使用 Azure 行動應用程式後端進行 Objective-C 或 Swift 的 iOS 開發。"
	services="app-service\mobile"
	documentationCenter="ios"
	authors="krisragh"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="hero-article"
	ms.date="08/11/2015"
	ms.author="krisragh"/>

#建立 iOS 應用程式

[AZURE.INCLUDE [app-service-mobile-selector-get-started-preview](../../includes/app-service-mobile-selector-get-started-preview.md)]&nbsp;[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]

##概觀

本教學課程說明如何使用 Azure 行動應用程式後端，將雲端型後端服務新增到 iOS 行動應用程式。您將建立新的行動應用程式後端，以及在 Azure 中儲存應用程式資料的簡單 _Todo 清單_ iOS 應用程式。

完成本教學課程是 iOS 應用程式的所有其他 Mobile Apps 教學課程的先決條件。

##必要條件

若要完成此教學課程，您需要下列項目：

* 使用中的 Azure 帳戶。如果您沒有帳戶，可以註冊 Azure 試用版並取得多達 10 個免費的 Mobile Apps，即使在試用期結束之後仍可繼續使用。如需詳細資訊，請參閱 [Azure 免費試用](http://azure.microsoft.com/pricing/free-trial/)。
 
* [Visual Studio Community 2013] 或更新版本。

* 具有 Xcode v7.0 或更新版本的 Mac 電腦。

>[AZURE.NOTE]如果您想在註冊 Azure 帳戶前開始使用 Azure App Service，請移至[嘗試 App Service](http://go.microsoft.com/fwlink/?LinkId=523751&appServiceName=mobile)，即可在 App Service 中立即建立短期入門行動應用程式。不需要信用卡；沒有承諾。

## 建立新的 Azure 行動應用程式後端

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service-preview](../../includes/app-service-mobile-dotnet-backend-create-new-service-preview.md)]

## 下載伺服器專案

1. 在您的電腦上造訪 [Azure 入口網站]。按一下 [全部瀏覽] > [Mobile Apps]，然後按一下您剛才建立的行動應用程式後端。
 
2. 在行動應用程式刀鋒視窗中，按一下 [設定]，並在 [行動應用程式] 底下按一下 [快速入門] >[ iOS (Objective-C)]。如果您喜歡 Swift，請改為按一下 [快速入門] > [iOS (Swift)]。
 
3. 在 [下載並執行您的伺服器專案] 底下，按 [下載]。將壓縮的專案檔案解壓縮至您的電腦，並在 Visual Studio 中開啟方案。

## 將伺服器專案發佈至 Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]

## 下載並執行 iOS 應用程式

[AZURE.INCLUDE [app-service-mobile-ios-run-app-preview](../../includes/app-service-mobile-ios-run-app-preview.md)]


<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-completed-ios.png
[1]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-steps-vs.png

[6]: ./media/app-service-mobile-dotnet-backend-ios-get-started-preview/ios-quickstart.png

[7]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-steps-ios.png
[8]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-xcode-project.png

[10]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-data-browse.png

[Azure 入口網站]: https://portal.azure.com/
[XCode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
 
[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203

<!---HONumber=August15_HO8-->