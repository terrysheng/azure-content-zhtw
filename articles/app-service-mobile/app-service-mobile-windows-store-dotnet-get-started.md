<properties
	pageTitle="在 Azure App Service Mobile Apps 上建立 Windows Runtime 8.1 通用應用程式 | Microsoft Azure"
	description="遵循此教學課程，可開始使用 Azure 行動應用程式後端進行 C#、Visual Basic 或 JavaScript 的 Windows 市集開發。"
	services="app-service\mobile"
	documentationCenter="windows"
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="11/16/2015"
	ms.author="glenga"/>

#建立 Windows 應用程式

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]&nbsp;[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

##概觀

本教學課程顯示如何將雲端型後端服務加入到通用 Windows 應用程式。如需詳細資訊，請參閱[什麼是 Mobile Apps？](app-service-mobile-value-prop.md)。

[AZURE.INCLUDE [app-service-mobile-windows-universal-get-started](../../includes/app-service-mobile-windows-universal-get-started.md)]

##必要條件

若要完成此教學課程，您需要下列項目：

* 使用中的 Azure 帳戶。如果您沒有帳戶，可以註冊 Azure 試用版並取得最多 10 個免費的行動應用程式，即使在試用期結束之後仍可繼續使用這些應用程式。如需詳細資訊，請參閱 [Azure 免費試用](http://azure.microsoft.com/pricing/free-trial/)。

* [Visual Studio Community 2013] 或更新版本。

>[AZURE.NOTE]如果您想要在註冊 Azure 帳戶之前先開始使用 Azure App Service，請移至[試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751&appServiceName=mobile)。您可以於該處，在 App Service 中立即建立短期的入門行動 app - 不需信用卡，不需任何承諾。

##建立新的 Azure 行動應用程式後端

依照下列步驟建立新的行動應用程式後端。

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

您現在已佈建 Azure 行動應用程式後端，可供您的行動用戶端應用程式使用。接下來，您將下載簡易「待辦事項清單」後端的伺服器專案，然後將專案發佈至 Azure。

## 設定伺服器專案

[AZURE.INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

##下載並執行用戶端專案

設定行動應用程式後端之後，您就可以建立新用戶端應用程式或修改現有的應用程式，以連接到 Azure。在本節中，您會下載通用 Windows 應用程式範本專案，而該專案已自訂要連接到行動應用程式後端。

1. 回到行動應用程式後端的 [開始使用] 刀鋒視窗中，按一下您的 [建立新的應用程式] > [下載]，然後解壓縮壓縮的專案檔案至本機電腦。

3. (選擇性) 將通用 Windows 應用程式專案加入至包含伺服器專案的方案。如此即可更輕鬆地在相同的 Visual Studio 方案中偵錯及測試應用程式和後端 (如果您選擇這麼做的話)。

4. 以 Windows 市集應用程式做為啟始專案，按下 F5 鍵以重建專案並啟動 Windows 市集應用程式。

5. 在應用程式的 [插入 TodoItem] 文字方塊中輸入有意義的文字 (例如「完成教學課程」(Complete the tutorial)」)，然後按一下 [儲存]。

	![](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-startup.png)

	如此會傳送 POST 要求到 Azure 中代管的新行動應用程式後端。

6. 停止偵錯，以滑鼠右鍵按一下 `<your app name>.WindowsPhone` 專案、按一下 [設定為啟始專案]，然後再次按 F5 鍵。

	![](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-completed-wp8.png)

	請注意，從先前步驟中儲存的資料，會在 Windows 應用程式啟動後從行動應用程式載入。

##後續步驟

* [加入驗證至應用程式](app-service-mobile-windows-store-dotnet-get-started-users.md) <br/>了解如何使用身分識別提供者驗證應用程式的使用者。

* [將推播通知新增至您的應用程式](app-service-mobile-windows-store-dotnet-get-started-push.md) <br/>了解如何將非常基本的推播通知傳送至應用程式。

<!-- Anchors. -->
<!-- Images. -->
<!-- URLs. -->
[Mobile App SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Azure portal]: https://portal.azure.com/
[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203

<!---HONumber=AcomDC_1125_2015-->