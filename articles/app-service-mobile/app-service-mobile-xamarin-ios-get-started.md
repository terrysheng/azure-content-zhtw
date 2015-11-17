<properties
	pageTitle="在 Xamarin.iOS app 中開始使用 Azure App Service Mobile Apps | Microsoft Azure"
	description="遵循本教學課程，開始使用 Mobile Apps 進行 Xamarin.iOS 開發。"
	services="app-service\mobile"
	documentationCenter="xamarin"
	authors="wesmc7777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="na"
	ms.tgt_pltfrm="mobile-xamarin-ios"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="11/03/2015"
	ms.author="normesta"/>


#建立 Xamarin.iOS 應用程式

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]
&nbsp;  
[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

##概觀

本教學課程說明如何使用 Azure 行動 app 後端，將雲端型後端服務加入至 Xamarin.iOS 行動 app。您將建立新的行動 app 後端，以及簡單的 _Todo list_ Xaamrin.iOS 應用程式，後者會在 Azure 中儲存 app 資料。

您必須先完成此教學課程，才能進行所有其他在 Azure App Service 中使用 Mobile Apps 的 Xamarin.iOS 相關教學課程。

##先決條件

若要完成此教學課程，您需要下列項目：

* 使用中的 Azure 帳戶。如果您沒有帳戶，可以註冊 Azure 試用版並取得最多 10 個免費的行動應用程式，即使在試用期結束之後仍可繼續使用這些應用程式。如需詳細資料，請參閱 [Azure 免費試用](http://azure.microsoft.com/pricing/free-trial/)。

* [Visual Studio Community 2013] 或更新版本。如果您安裝 Visual Studio Community 2013，請個別安裝 [Xamarin]。當您安裝 Visual Studio 2015 時，可以安裝 Xamarin 工具。

* 已安裝 [Xcode] v7.0 或更新版本，以及 [Xamarin Studio] 的 Mac。

     >[AZURE.NOTE]如果您打算在 Windows 電腦上使用 Visual Studio 建置您的 app，您仍需要存取已加入網路的 Mac，才能執行此動作。

>[AZURE.NOTE]如果您想要在註冊 Azure 帳戶之前先開始使用 Azure App Service，請移至[試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751&appServiceName=mobile)。您可以於該處，在 App Service 中立即建立短期的入門行動 app - 不需信用卡，不需任何承諾。

## 建立新的 Azure 行動應用程式後端

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## 下載伺服器專案

1. 在您的電腦上瀏覽 [Azure 入口網站]。按一下 [全部瀏覽] > [Mobile Apps]，然後按一下您剛建立的行動應用程式後端。

2. 在 [行動應用程式] 刀鋒視窗中，按一下 [設定]，然後在 [行動應用程式] 底下按一下 [快速入門] > [Xamarin.iOS]。

3. 在 [下載並執行您的伺服器專案] 底下，按一下 [下載]。將壓縮的專案檔案解壓縮至您的電腦，並在 Visual Studio 中開啟方案。

## 在本機測試您的後端專案

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-test-local-service](../../includes/app-service-mobile-dotnet-backend-test-local-service.md)]

## 將伺服器專案發佈至 Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service](../../includes/app-service-mobile-dotnet-backend-publish-service.md)]

## 下載並執行 Xamarin.iOS 應用程式

1. 在 Mac 上，於瀏覽器視窗中開啟 [Azure 入口網站]。

>[AZURE.NOTE]在 Mac 上執行 Xamarin.iOS 應用程式會比較容易。如果想要，您可以在 Windows 電腦上使用 Visual Studio 來執行 Xamarin.iOS 應用程式，但這樣會比較複雜，因為您必須連線到已加入網路的 Mac。如果您想這麼做，請參閱[在 Windows 上安裝 Xamarin.iOS]。

2. 在 [下載並執行 Xamarin.iOS 專案] 底下，按一下 [下載] 按鈕。

  	這會下載包含連線到您行動應用程式之用戶端應用程式的專案。將此壓縮專案檔案儲存到您的本機電腦，並記錄儲存位置。

3. 將您下載的專案解壓縮，並在 Xamarin Studio (或 Visual Studio) 中開啟。

	![][9]

	![][8]

4. 按 F5 鍵，以建置專案並在 iPhone 模擬器中啟動 app。

5. 在 app 中，輸入有意義的文字 (例如 _Learn Xamarin_)，然後按一下 **+** 按鈕。

	![][10]

	如此會傳送 POST 要求到 Azure 中代管的新行動應用程式後端。要求中的資料會插入 TodoItem 資料表中。行動應用程式後端會傳回資料表中儲存的項目，而該資料會顯示在清單中。

>[AZURE.NOTE]您可以在 QSTodoService.cs C# 檔案中檢閱用來存取行動應用程式後端以查詢和插入資料的程式碼。

##後續步驟

* [將驗證新增至應用程式](app-service-mobile-xamarin-ios-get-started-users.md) <br/>了解如何使用身分識別提供者來驗證應用程式的使用者。

* [將推播通知新增至您的應用程式](app-service-mobile-xamarin-ios-get-started-push.md) <br/>了解如何將非常基本的推播通知傳送至應用程式。

<!-- Anchors. -->
[Getting started with mobile app backends]: #getting-started
[Create a new mobile app backend]: #create-new-service
[Next Steps]: #next-steps



<!-- Images. -->
[6]: ./media/app-service-mobile-xamarin-ios-get-started/xamarin-ios-quickstart.png
[8]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-xamarin-project-ios-vs.png
[9]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-xamarin-project-ios-xs.png
[10]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-quickstart-startup-ios.png

<!-- URLs. -->
[Azure 入口網站]: https://portal.azure.com/


[Xamarin Studio]: http://xamarin.com/download
[Xamarin]: http://xamarin.com/download
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532
[Xamarin for Windows]: https://go.microsoft.com/fwLink/?LinkID=330242&clcid=0x409
[在 Windows 上安裝 Xamarin.iOS]: http://developer.xamarin.com/guides/ios/getting_started/installation/windows/

<!---HONumber=Nov15_HO3-->