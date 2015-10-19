<properties
	pageTitle="開始使用 Windows 市集 App 的行動服務 (C#) | Microsoft Azure"
	description="遵循此教學課程，開始使用 Azure 行動服務進行 C# 的 Windows 市集開發。"
	services="mobile-services"
	documentationCenter="windows"
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows"
	ms.devlang="dotnet"
	ms.topic="get-started-article" 
	ms.date="10/06/2015"
	ms.author="glenga"/>

# <a name="getting-started"> </a>開始使用行動服務

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]&nbsp;

[AZURE.INCLUDE [mobile-services-hero-slug](../../includes/mobile-services-hero-slug.md)]

本教學課程顯示如何使用 Azure 行動服務，將雲端型後端服務新增到通用 Windows 應用程式。通用 Windows 應用程式解決方案包括 Windows 市集 8.1 和 Windows Phone 市集 8.1 應用程式的專案，以及一般共用專案。如需詳細資訊，請參閱[建置目標為 Windows 和 Windows Phone 的通用 Windows 應用程式](http://msdn.microsoft.com/library/windows/apps/xaml/dn609832.aspx)。

在本教學課程中，您將建立新的行動服務，並建立可在新的行動服務中儲存應用程式資料的簡單*待辦事項*應用程式。您將建立的行動服務會使用 JavaScript 建立伺服器端商務邏輯。若要建立可讓您使用 Visual Studio 並以支援的 .NET 語言來撰寫伺服器端商務邏輯的行動服務，請參閱本主題的 .NET 後端版本。

[AZURE.INCLUDE [mobile-services-windows-universal-get-started](../../includes/mobile-services-windows-universal-get-started.md)]

若要完成此教學課程，您需要下列項目：

* 使用中的 Azure 帳戶。如果您沒有帳戶，您可以註冊 Azure 試用版並取得高達 10 項的免費行動服務。此外，在試用期間結束後您仍可繼續使用這些服務。如需詳細資訊，請參閱 [Azure 免費試用](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fzh-TW%2Fdocumentation%2Farticles%2Fmobile-services-javascript-backend-windows-store-javascript-get-started%2F)。
* [Visual Studio 2013 Express for Windows]

## 建立新的行動服務

[AZURE.INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

## 建立新的通用 Windows 應用程式

在建立您的行動服務之後，您可以依照 [管理入口網站] 中的簡易快速入門，來建立新的通用 Windows 應用程式或修改現有的 Windows 市集或 Windows Phone 應用程式專案，以連接到您的行動服務。

在本節中，您將建立與行動服務連線的新通用 Windows 應用程式。

1.  在管理入口網站中，按一下 **[行動服務]**，然後按一下您剛剛建立的行動服務。


2. 在快速入門索引標籤中，按一下 [Choose platform] 下的 [Windows]，並展開 [Create a new Windows Store app]。

   	這將顯示三個簡單步驟，可用來建立連接到您行動服務的 Windows 市集應用程式。

  	![行動服務快速入門步驟](./media/mobile-services-javascript-backend-windows-store-dotnet-get-started/mobile-quickstart-steps.png)

3. 如果您尚未這麼做，請在您的本機電腦或虛擬機器下載並安裝 [Visual Studio 2013 Express for Windows]。

4. 按一下 [Create TodoItem table] 以建立儲存應用程式資料的資料表。

5. 在 [Download and run your app] 中，選取您的應用程式語言，然後按一下 [下載]。

  	這將會下載與行動服務連接的範例*待辦事項*應用程式專案。將此壓縮專案檔案儲存到您的本機電腦，並記錄儲存位置。

## 執行您的 Windows 應用程式

[AZURE.INCLUDE [mobile-services-javascript-backend-run-app](../../includes/mobile-services-javascript-backend-run-app.md)]

>[AZURE.NOTE]您可以檢閱存取行動服務來查詢和插入資料的程式碼，這可以在 MainPage.xaml.cs 檔案中找到。

## 後續步驟
請注意，您已完成快速入門，並了解如何執行行動服務中的其他重要工作：

* [開始使用離線資料同步] 了解如何使用離線資料同步，讓您的 App 更穩健及具備回應力。

* [將驗證新增至行動服務 App][Get started with authentication] 了解如何向身分識別提供者驗證 App 的使用者。

* [將推播通知新增至您的App][Get started with push notifications] 了解如何將非常基本的推播通知傳送至 App。

* [如何使用.NET 用戶端程式庫](mobile-services-windows-dotnet-how-to-use-client-library.md) 了解如何查詢行動服務、處理資料，以及存取自訂 API。

[AZURE.INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]

<!-- Anchors. -->
[Getting started with Mobile Services]: #getting-started
[Create a new mobile service]: #create-new-service
[Define the mobile service instance]: #define-mobile-service-instance
[Next Steps]: #next-steps

<!-- Images. -->



<!-- URLs. -->
[開始使用離線資料同步]: mobile-services-windows-store-dotnet-get-started-offline-data.md
[Get started with authentication]: ../mobile-services-windows-store-dotnet-get-started-users.md
[Get started with push notifications]: ../mobile-services-javascript-backend-windows-store-dotnet-get-started-push.md
[Visual Studio 2013 Express for Windows]: http://go.microsoft.com/fwlink/?LinkId=257546
[Mobile Services SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Management Portal]: https://manage.windowsazure.com/
 

<!---HONumber=Oct15_HO2-->