<properties
	pageTitle="開始使用適用於 Xamarin.Android 應用程式的Azure 行動應用程式"
	description="遵循此教學課程，可開始使用 Azure Mobile Apps 進行 Xamarin Android 開發。"
	services="app-service\mobile"
	documentationCenter="xamarin"
	authors="ggailey777"
	manager="dwrede"
	editor="" />

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-android"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="11/17/2015"
	ms.author="glenga" />

#建立 Xamarin.Android 應用程式

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]
&nbsp;  
[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]
 
##概觀

本教學課程將示範如何將雲端後端服務加入至 Xamarin.Android 應用程式。如需詳細資訊，請參閱[什麼是 Mobile Apps？](app-service-mobile-value-prop.md)。

以下是完成應用程式的螢幕擷取畫面：

![][0]

完成本教學課程是 Xamarin Android 應用程式所有其他行動應用程式教學課程的必要條件。
 
##必要條件

若要完成此教學課程，您需要下列項目：

* 使用中的 Azure 帳戶。如果您沒有帳戶，可以註冊 Azure 試用版並取得最多 10 個免費的行動應用程式，即使在試用期結束之後仍可繼續使用這些應用程式。如需詳細資訊，請參閱 [Azure 免費試用](http://azure.microsoft.com/pricing/free-trial/)。
 
* [Visual Studio Community 2013] 或更新版本。如果您安裝 Visual Studio Community 2013，請個別安裝 [Xamarin]。當您安裝 Visual Studio 2015 時，可以安裝 Xamarin 工具。
 
>[AZURE.NOTE]如果您想在註冊 Azure 帳戶之前先開始使用 Azure App Service，請前往[試用 App Service](https://tryappservice.azure.com/?appServiceName=mobile)，讓您能立刻在 App Service 中建立短期的入門行動應用程式。不需要信用卡；無需承諾。

## 建立新的 Azure 行動應用程式後端

依照下列步驟建立新的行動應用程式後端。

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

您現在已佈建 Azure 行動應用程式後端，可供您的行動用戶端應用程式使用。接下來，您將下載簡易「待辦事項清單」後端的伺服器專案，然後將專案發佈至 Azure。

## 設定伺服器專案

[AZURE.INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## 下載並執行 Xamarin.Android 應用程式

1. 在 [下載並執行 Xamarin.Android 專案] 底下，按一下 [下載] 按鈕。

  	這會下載包含連線到您行動應用程式之用戶端應用程式的專案。將此壓縮專案檔案儲存到您的本機電腦，並記錄儲存位置。

2. 按 **F5** 鍵，以重建專案並啟動應用程式。

3. 在應用程式中輸入有意義的文字 (例如 _Complete the tutorial_)，然後按一下 [加入] 按鈕。

	![][10]

	如此會傳送 POST 要求到 Azure 中代管的新行動應用程式後端。要求中的資料會插入 TodoItem 資料表中。行動應用程式後端會傳回資料表中儲存的項目，而該資料會顯示在清單中。

	> [AZURE.NOTE]您可以檢閱存取行動應用程式後端以查詢與插入資料的程式碼，您可在 ToDoActivity.cs C# 檔案中找到此程式碼。

##後續步驟

* [加入驗證至應用程式](app-service-mobile-xamarin-android-get-started-users.md) <br/>了解如何使用身分識別提供者驗證應用程式的使用者。


<!-- Images. -->
[0]: ./media/app-service-mobile-xamarin-android-get-started/mobile-quickstart-completed-android.png
[6]: ./media/app-service-mobile-xamarin-android-get-started/mobile-portal-quickstart-xamarin.png
[8]: ./media/app-service-mobile-xamarin-android-get-started/mobile-xamarin-project-android-vs.png
[9]: ./media/app-service-mobile-xamarin-android-get-started/mobile-xamarin-project-android-xs.png
[10]: ./media/app-service-mobile-xamarin-android-get-started/mobile-quickstart-startup-android.png

<!-- URLs. -->
[Azure Portal]: https://azure.portal.com/
[Xamarin]: http://xamarin.com/download
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532&clcid=0x409
[Xamarin for Windows]: https://go.microsoft.com/fwLink/?LinkID=330242&clcid=0x409
 
[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203

<!---HONumber=AcomDC_1203_2015--->