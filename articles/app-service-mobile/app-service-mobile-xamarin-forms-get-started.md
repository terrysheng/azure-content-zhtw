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
	ms.date="11/23/2015"
	ms.author="normesta"/>

#建立 Xamarin.Forms 應用程式

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]&nbsp;[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

##概觀

本教學課程說明如何使用 Azure 行動應用程式後端，將雲端型後端服務新增到 Xamarin.Forms 行動應用程式。您將同時建立新的行動應用程式後端，以及在 Azure 中儲存應用程式資料的簡單 _Todo list_ Xamarin.Forms 應用程式。

完成本教學課程是所有其他 Xamarin.Forms 應用程式的行動應用程式教學課程的必要條件。

##必要條件

若要完成此教學課程，您需要下列項目：

* 使用中的 Azure 帳戶。如果您沒有帳戶，可以註冊 Azure 試用版並取得最多 10 個免費的行動應用程式，即使在試用期結束之後仍可繼續使用這些應用程式。如需詳細資訊，請參閱 [Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。
 
* [Visual Studio Community 2013] 或更新版本。如果您安裝 Visual Studio Community 2013，請個別安裝 [Xamarin]。當您安裝 Visual Studio 2015 時，可以安裝 Xamarin 工具。

* 已安裝 [Xcode] v7.0 或更新版本，以及 [Xamarin Studio] 的 Mac。如果您打算在 Windows 電腦上使用 Visual Studio 建置您的應用程式，您仍會需要存取已加入網路的 Mac 才能這麼做。
 
>[AZURE.NOTE] 如果您想在註冊 Azure 帳戶之前先開始使用 Azure App Service，請前往[試用 App Service](https://tryappservice.azure.com/?appServiceName=mobile)，讓您能立刻在 App Service 中建立短期的入門行動應用程式。不需要信用卡；無需承諾。

## 建立新的 Azure 行動應用程式後端

依照下列步驟建立新的行動應用程式後端。

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]


您現在已佈建 Azure 行動應用程式後端，可供您的行動用戶端應用程式使用。接下來，您將下載簡易「待辦事項清單」後端的伺服器專案，然後將專案發佈至 Azure。

## 設定伺服器專案

請遵循下列步驟來設定伺服器專案使用 Node.js 或 .NET 後端。

[AZURE.INCLUDE [app-service-mobile-configure-new-backend](../../includes/app-service-mobile-configure-new-backend.md)]

 
## (選擇性) 在本機測試您的後端專案

如果您選擇上述的 .NET 後端組態，您可以在本機選擇性地測試後端。

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-test-local-service](../../includes/app-service-mobile-dotnet-backend-test-local-service.md)]


##下載並執行 Xamarin.Forms 方案

您在這裡有幾個選擇。您可以將方案下載到 Mac 並在 Xamarin Studio 中開啟，或者您可以下載方案到 Windows 電腦並在 Visual Studio 開啟。您也可以兩種環境都使用，並在其間來回切換。請考量下列事項：

* 在 Mac 上執行方案的 iOS 專案會比較容易。如果想要，您可以在 Windows 電腦上使用 Visual Studio，但這樣會比較複雜，因為您必須連線到已加入網路的 Mac。如果您想這麼做，請參閱[在 Windows 上安裝 Xamarin.iOS]。
* 您可以在 Mac 或 Windows 電腦上執行 Android 專案。
* 您只能在 Windows 電腦上使用 Visual Studio 執行 Windows 專案。

了解以上事項之後，請繼續進行。

 1. 在您的 Mac 或 Windows 電腦上，於瀏覽器視窗中開啟 [Azure 入口網站]。
 2. 在行動應用程式的設定刀鋒視窗上，按一下 [開始使用] (在 [行動] 之下) > [Xamarin.Forms]。在步驟 3 中，按一下 [建立新的應用程式] (如果尚未選取的話)。接著按一下 [下載] 按鈕。

    這會下載包含連線到您行動應用程式之用戶端應用程式的專案。將此壓縮專案檔案儲存到您的本機電腦，並記錄儲存位置。

 3. 將您下載的專案解壓縮，並在 Xamarin Studio 或 Visual Studio 中開啟。

	![][9]

	![][8]

##(選擇性) 執行 iOS 專案

這一節適用於對 iOS 裝置執行 Xamarin iOS 專案。如果未使用 iOS 裝置，可以略過這一節。

####在 Xamarin Studio 中

1. 以滑鼠右鍵按一下 iOS 專案，然後按一下 [設為起始專案]。
2. 在 [執行] 功能表上，按一下 [開始偵錯] 以建置專案並在 iPhone 模擬器中啟動應用程式。

####在 Visual Studio 中
1. 以滑鼠右鍵按一下 iOS 專案，然後按一下 [設為起始專案]。
2. 從 [建置] 功能表中，按一下 [組態管理員]。 
3. 在 [組態管理員] 對話方塊中，選取 iOS 專案的 [建置] 和 [部署] 核取方塊。
4. 按 **F5** 鍵，以建置專案並在 iPhone 模擬器中啟動 app。

在應用程式中，輸入有意義的文字 (例如「了解 Xamarin (Learn Xamarin)」)，然後按一下 **+** 按鈕。

![][10]

如此會傳送 POST 要求到 Azure 中代管的新行動應用程式後端。要求中的資料會插入 TodoItem 資料表中。行動應用程式後端會傳回資料表中儲存的項目，而該資料會顯示在清單中。

> [AZURE.NOTE]
您將會看到在您方案的可攜式類別庫專案的 TodoItemManager.cs C# 檔案中，存取行動應用程式後端的程式碼。

##(選擇性) 執行 Android 專案

這一節適用於對 Android 裝置執行 Xamarin Adroid 專案。如果未使用 Android 裝置，可以略過這一節。

####在 Xamarin Studio 中

1. 以滑鼠右鍵按一下 Android 專案，然後按一下 [設為起始專案]。
2. 在 [執行] 功能表上按一下 [開始偵錯] 以建置專案，並在 Android 模擬器中啟動應用程式。

####在 Visual Studio 中
1. 以滑鼠右鍵按一下 Android 專案，然後按一下 [設為起始專案]。
4. 從 [建置] 功能表中，按一下 [組態管理員]。 
5. 在 [組態管理員] 對話方塊中，選取 Android 專案的 [建置] 和 [部署] 核取方塊。
6. 按 **F5** 鍵，以建置專案並在 Android 模擬器中啟動應用程式。

在應用程式中，輸入有意義的文字 (例如「了解 Xamarin (Learn Xamarin)」)，然後按一下 **+** 按鈕。

![][11]

如此會傳送 POST 要求到 Azure 中代管的新行動應用程式後端。要求中的資料會插入 TodoItem 資料表中。行動應用程式後端會傳回資料表中儲存的項目，而該資料會顯示在清單中。

> [AZURE.NOTE]
您將會看到在您方案的可攜式類別庫專案的 TodoItemManager.cs C# 檔案中，存取行動應用程式後端的程式碼。


##(選擇性) 執行 Windows 專案


這一節適用於對 Windows 裝置執行 WinApp 專案。如果未使用 Windows 裝置，可以略過這一節。


####在 Visual Studio 中
1. 以滑鼠右鍵按一下任何一個 Windows 專案，然後按一下 [設為起始專案]。
4. 從 [建置] 功能表中，按一下 [組態管理員]。 
5. 在 [組態管理員] 對話方塊中，選取所選之 Windows 專案的 [建置] 和 [部署] 核取方塊。
6. 按 **F5** 鍵，以建置專案並在 Windows 模擬器中啟動應用程式。

在應用程式中，輸入有意義的文字 (例如「了解 Xamarin (Learn Xamarin)」)，然後按一下 **+** 按鈕。
	
如此會傳送 POST 要求到 Azure 中代管的新行動應用程式後端。要求中的資料會插入 TodoItem 資料表中。行動應用程式後端會傳回資料表中儲存的項目，而該資料會顯示在清單中。

![][12]
	
> [AZURE.NOTE]
您將會看到在您方案的可攜式類別庫專案的 TodoItemManager.cs C# 檔案中，存取行動應用程式後端的程式碼。

<!-- Anchors. -->
[Getting started with mobile app backends]: #getting-started
[Create a new mobile app backend]: #create-new-service
[Next Steps]: #next-steps


<!-- Images. -->
[6]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart.png
[8]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart-vs.png
[9]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart-xs.png
[10]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-android.png
[12]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-windows.png


<!-- URLs. -->
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[Mobile app SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Azure 入口網站]: https://portal.azure.com/


[Xamarin Studio]: http://xamarin.com/download
[Xamarin]: http://xamarin.com/download
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532&clcid=0x409
[Xamarin for Windows]: https://go.microsoft.com/fwLink/?LinkID=330242&clcid=0x409
[在 Windows 上安裝 Xamarin.iOS]: http://developer.xamarin.com/guides/ios/getting_started/installation/windows/
 

<!---HONumber=AcomDC_0128_2016-->