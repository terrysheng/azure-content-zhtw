<properties
	pageTitle="使用 Xamarin.Forms 搭配行動應用程式入門"
	description="遵循此教學課程，開始使用 Azure 行動應用程式進行 Xamarin.Forms 開發。"
	services="app-service\mobile"
	documentationCenter="xamarin"
	authors="wesmc7777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin"
	ms.devlang="dotnet"
	ms.topic="get-started-article"
	ms.date="08/12/2015"
	ms.author="normesta"/>

#建立 Xamarin.Forms 應用程式

[AZURE.INCLUDE [app-service-mobile-selector-get-started-preview](../../includes/app-service-mobile-selector-get-started-preview.md)]&nbsp;[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]

##概觀

本教學課程說明如何使用 Azure 行動應用程式後端，將雲端型後端服務新增到 Xamarin.Forms 行動應用程式。您將同時建立新的行動應用程式後端，以及在 Azure 中儲存應用程式資料的簡單 _Todo list_ Xamarin.Forms 應用程式。

完成本教學課程是所有其他 Xamarin.Forms 應用程式的行動應用程式教學課程的先決條件。

##必要條件

若要完成此教學課程，您需要下列項目：

* 使用中的 Azure 帳戶。如果您沒有帳戶，可以註冊 Azure 試用版並取得最多 10 個免費的行動應用程式，即使在試用期結束之後仍可繼續使用這些應用程式。如需詳細資訊，請參閱 [Azure 免費試用](http://azure.microsoft.com/pricing/free-trial/)。
 
* [Visual Studio Community 2013] 或更新版本。如果您安裝 Visual Studio Community 2013，請個別安裝 [Xamarin]。當您安裝 Visual Studio 2015 時，可以安裝 Xamarin 工具。

* 已安裝 [Xcode] v7.0 或更新版本，以及 [Xamarin Studio] 的 Mac。
 
     >[AZURE.NOTE]如果您打算在 Windows 電腦上使用 Visual Studio 建置您的應用程式，您仍會需要存取已加入網路的 Mac 才能這麼做。
 
>[AZURE.NOTE]如果您想在註冊 Azure 帳戶前開始使用 Azure App Service，請移至[試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751&appServiceName=mobile)，即可在 App Service 中立即建立短期入門行動應用程式。不需要信用卡；沒有承諾。

## 建立新的 Azure 行動應用程式後端

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service-preview](../../includes/app-service-mobile-dotnet-backend-create-new-service-preview.md)]

## 下載伺服器專案

1. 在您的電腦上瀏覽 [Azure 入口網站]。按一下 [瀏覽全部] > [行動應用程式]，然後按一下您剛才建立的行動應用程式後端。

2. 在 [行動應用程式] 刀鋒視窗中，按一下 [設定]，並在 [行動應用程式] 底下按一下 [快速入門] > [Xamarin.Forms]。
 
3. 在 [下載並執行您的伺服器專案] 底下，按一下 [下載]。將壓縮的專案檔案解壓縮至您的電腦，並在 Visual Studio 中開啟方案。
 
## 在本機測試您的後端專案

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-test-local-service-preview](../../includes/app-service-mobile-dotnet-backend-test-local-service-preview.md)]

## 將伺服器專案發行至 Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]

##下載並執行 Xamarin.Forms 方案

您在這裡有幾個選擇。您可以將方案下載到 Mac 並在 Xamarin Studio 中開啟，或者您可以下載方案到 Windows 電腦並在 Visual Studio 開啟。您也可以兩種環境都使用，並在其間來回切換。請考量下列事項：

* 在 Mac 上執行方案的 iOS 專案會比較容易。如果想要，您可以在 Windows 電腦上使用 Visual Studio，但這樣會比較複雜，因為您必須連線到已加入網路的 Mac。如果您想這麼做，請參閱[在 Windows 上安裝 Xamarin.iOS] (英文)。
* 您可以在 Mac 或 Windows 電腦上執行 Android 專案。
* 您只能在 Windows 電腦上使用 Visual Studio 執行 Windows 專案。

了解以上事項之後，請繼續進行。

 1. 在您的 Mac 或 Windows 電腦上，於瀏覽器視窗中開啟 [Azure 入口網站]。
 2. 在 [下載並執行 Xamarin.Forms 專案] 底下，按一下 [下載] 按鈕。

    這會下載包含連線到您行動應用程式之用戶端應用程式的專案。將此壓縮專案檔案儲存到您的本機電腦，並記錄儲存位置。

 3. 將您下載的專案解壓縮，並在 Xamarin Studio 或 Visual Studio 中開啟。

	![][9]

	![][8]

###執行 iOS 專案

####在 Xamarin Studio 中

1. 以滑鼠右鍵按一下 iOS 專案，然後按一下 [設為起始專案]。
2. 在 [執行] 功能表上，按一下 [開始偵錯] 以建置專案並在 iPhone 模擬器中啟動應用程式。

####在 Visual Studio 中
1. 以滑鼠右鍵按一下 iOS 專案，然後按一下 [設為起始專案]。
2. 從 [建置] 功能表中，按一下 [組態管理員]。 
3. 在 [組態管理員] 對話方塊中，選取 iOS 專案的 [建置] 和 [部署] 核取方塊。
4. 按 **F5** 鍵，以建置專案並在 iPhone 模擬器中啟動應用程式。

在應用程式中，輸入有意義的文字 (例如 _Learn Xamarin_)，然後按一下加號 **+** 按鈕。

![][10]

如此會傳送 POST 要求到 Azure 中代管的新行動應用程式後端。要求中的資料會插入 TodoItem 資料表中。行動應用程式後端會傳回資料表中儲存的項目，而該資料會顯示在清單中。

> [AZURE.NOTE]您將會看到在您方案的可攜式類別庫專案的 ToDoActivity.cs C# 檔案中，存取行動應用程式後端的程式碼。

###執行 Android 專案

####在 Xamarin Studio 中

1. 以滑鼠右鍵按一下 Android 專案，然後按一下 [設為起始專案]。
2. 在 [Run] (執行) 功能表上，按一下 [Start Debugging] (開始偵錯) 以建置專案並在 Android 模擬器中啟動應用程式。

####在 Visual Studio 中
1. 以滑鼠右鍵按一下 Android 專案，然後按一下 [設為起始專案]。
4. 從 [建置] 功能表中，按一下 [組態管理員]。 
5. 在 [組態管理員] 對話方塊中，選取 Android 專案的 [建置] 和 [部署] 核取方塊。
6. 按 **F5** 鍵，以建置專案並在 Android 模擬器中啟動應用程式。

在應用程式中，輸入有意義的文字 (例如 _Learn Xamarin_)，然後按一下加號 **+** 按鈕。

![][11]

如此會傳送 POST 要求到 Azure 中代管的新行動應用程式後端。要求中的資料會插入 TodoItem 資料表中。行動應用程式後端會傳回資料表中儲存的項目，而該資料會顯示在清單中。

> [AZURE.NOTE]您將會看到在您方案的可攜式類別庫專案的 ToDoActivity.cs C# 檔案中，存取行動應用程式後端的程式碼。


###執行 Windows 專案

####在 Visual Studio 中
1. 以滑鼠右鍵按一下任何一個 Windows 專案，然後按一下 [設為起始專案]。
4. 從 [建置] 功能表中，按一下 [組態管理員]。 
5. 在 [組態管理員] 對話方塊中，選取所選之 Windows 專案的 [建置] 和 [部署] 核取方塊。
6. 按 **F5** 鍵，以建置專案並在 Windows 模擬器中啟動應用程式。

在應用程式中，輸入有意義的文字 (例如 _Learn Xamarin_)，然後按一下加號 **+** 按鈕。
	
如此會傳送 POST 要求到 Azure 中代管的新行動應用程式後端。要求中的資料會插入 TodoItem 資料表中。行動應用程式後端會傳回資料表中儲存的項目，而該資料會顯示在清單中。

![][12]
	
> [AZURE.NOTE]您將會看到在您方案的可攜式類別庫專案的 ToDoActivity.cs C# 檔案中，存取行動應用程式後端的程式碼。

<!-- Anchors. -->
[Getting started with mobile app backends]: #getting-started
[Create a new mobile app backend]: #create-new-service
[Next Steps]: #next-steps


<!-- Images. -->
[6]: ./media/app-service-mobile-dotnet-backend-xamarin-forms-get-started-preview/xamarin-forms-quickstart.png
[8]: ./media/app-service-mobile-dotnet-backend-xamarin-forms-get-started-preview/xamarin-forms-quickstart-vs.png
[9]: ./media/app-service-mobile-dotnet-backend-xamarin-forms-get-started-preview/xamarin-forms-quickstart-xs.png
[10]: ./media/app-service-mobile-dotnet-backend-xamarin-forms-get-started-preview/mobile-quickstart-startup-ios.png
[11]: ./media/app-service-mobile-dotnet-backend-xamarin-forms-get-started-preview/mobile-quickstart-startup-android.png
[12]: ./media/app-service-mobile-dotnet-backend-xamarin-forms-get-started-preview/mobile-quickstart-startup-windows.png


<!-- URLs. -->
[Get started with offline data sync]: app-service-mobile-xamarin-ios-get-started-offline-data-preview.md
[Get started with authentication]: ../app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview-users.md
[Get started with push notifications]: ../app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview-push.md
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[Mobile app SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Azure 入口網站]: https://portal.azure.com/
[JavaScript backend version]: ../partner-xamarin-mobile-services-ios-get-started.md
[Get started with data in app services using Visual Studio 2012]: ../app-service-mobile-windows-store-dotnet-get-started-data-vs2012-preview.md
[Troubleshoot a mobile app .NET backend]: ../app-service-mobile-dotnet-backend-how-to-troubleshoot-preview.md


[Xamarin Studio]: http://xamarin.com/download
[Xamarin]: http://xamarin.com/download
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532&clcid=0x409
[Xamarin for Windows]: https://go.microsoft.com/fwLink/?LinkID=330242&clcid=0x409
[在 Windows 上安裝 Xamarin.iOS]: http://developer.xamarin.com/guides/ios/getting_started/installation/windows/
 

<!---HONumber=Oct15_HO3-->