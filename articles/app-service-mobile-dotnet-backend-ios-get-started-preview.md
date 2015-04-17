<properties
	pageTitle="開始使用 Azure 應用程式服務行動 iOS 應用程式"
	description="遵循此教學課程，可開始使用 Azure 應用程式服務進行 iOS 開發。"
	services="app-service\mobile"
	documentationCenter="ios"
	authors="yuaxu"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="hero-article"
	ms.date="02/20/2015"
	ms.author="yuaxu"/>

# <a name="getting-started"> </a>建立 iOS 應用程式

[AZURE.INCLUDE [app-service-mobile-selector-get-started-preview](../includes/app-service-mobile-selector-get-started-preview.md)]

本教學課程說明如何使用 Azure 應用程式服務行動應用程式建立 iOS 應用程式。您將建立新的行動應用程式後端，和儲存應用程式資料的簡單_待辦事項清單_應用程式。本教學課程會將 .NET 和 Visual Studio 用於伺服器端邏輯。

若要完成此教學課程，您需要下列項目：

* 使用中的 Azure 帳戶。如果您沒有帳戶，可以註冊 Azure 試用版並取得多達 10 個免費的行動應用程式，即使在試用期結束之後仍可繼續使用這些應用程式。如需詳細資訊，請參閱 [Azure 免費試用](http://azure.microsoft.com/pricing/free-trial/)。
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a>.

>[AZURE.NOTE] 如果您在註冊 Azure 帳戶之前想要開始使用 Azure 應用程式服務，請移至 [試用應用程式服務](http://go.microsoft.com/fwlink/?LinkId=523751&appServiceName=mobile)，在此您將可立即在應用程式服務中建立存留期較短的入門行動應用程式。不需要信用卡，無需承諾。

## <a name="create-new-service"> </a>建立新的行動應用程式後端

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service-preview](../includes/app-service-mobile-dotnet-backend-create-new-service-preview.md)]

## 建立新的 iOS 應用程式

在建立您的行動後端之後，您可以依照 [Azure 預覽入口網站] 中的簡易快速入門，建立新的應用程式或修改現有的應用程式，以連接到您的行動應用程式後端。

1. 在 Azure 入口網站中，按一下 [**行動應用程式**]，然後按一下您剛才建立的行動應用程式後端。

2. 在分頁頂端，按一下 [新增用戶端]，然後展開 iOS。

	![][6]

	這會顯示建立連接到行動應用程式後端的 iOS 應用程式所需的步驟。

3. 在您的本機電腦或虛擬機器下載並安裝 <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a> (如果您尚未這麼做)。 

4. 下載並安裝 [Xcode] v4.4 或更新版本和 [Xamarin Studio]。您也可以使用 Xamarin for Visual Studio。

5. 在 [**Download and publish you service to the cloud**] 下，按一下 [**下載**]。

 這會下載一個方案，其中包含行動應用程式後端的專案，以及與行動應用程式後端連接的範例_待辦事項清單_應用程式的專案。將此壓縮專案檔案儲存到您的本機電腦，並記錄儲存位置。

6. 下載您的發行設定檔、將下載的檔案儲存至本機電腦，並記下儲存位置。

## 測試行動應用程式

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-test-local-service-preview](../includes/app-service-mobile-dotnet-backend-test-local-service-preview.md)]

## 發佈行動應用程式

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]

## 執行 iOS 應用程式

[AZURE.INCLUDE [app-service-mobile-ios-run-app-preview](../includes/app-service-mobile-ios-run-app-preview.md)]



<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-completed-ios.png
[1]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-steps-vs.png

[6]: ./media/app-service-mobile-dotnet-backend-ios-get-started-preview/ios-quickstart.png

[7]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-steps-ios.png
[8]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-xcode-project.png

[10]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-data-browse.png

[管理入口網站]: https://manage.windowsazure.com/
[XCode]: https://go.microsoft.com/fwLink/p/?LinkID=266532

<!--HONumber=49-->