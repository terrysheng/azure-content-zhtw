<properties
	pageTitle="開始使用適用於 iOS App 的 Azure 行動服務 | JavaScript 後端"
	description="遵循此教學課程，可開始使用 Azure 行動服務進行 iOS 開發。"
	services="mobile-services"
	documentationCenter="ios"
	authors="krisragh"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="hero-article"
	ms.date="02/10/2016"
	ms.author="krisragh"/>

# <a name="getting-started"> </a>開始使用行動服務

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]

[AZURE.INCLUDE [mobile-services-hero-slug](../../includes/mobile-services-hero-slug.md)]


本教學課程說明如何使用 Azure 行動服務在 iOS 應用程式中新增雲端型後端服務。

在本教學課程中，您將建立新的行動服務，並建立可在新的行動服務中儲存應用程式資料的簡單_待辦事項_應用程式。您將建立的行動服務會使用 JavaScript 建立伺服器端商務邏輯。若要以 .NET 中伺服器端的商務邏輯來建立行動服務，請參閱本主題的 [.NET 後端版本]。

> [AZURE.NOTE] 若要完成此教學課程，您需要 Azure 帳戶。如果您沒有帳戶，可以註冊 Azure 試用版並取得[免費的行動服務，即使在試用期結束之後仍可繼續使用這些服務](https://azure.microsoft.com/pricing/details/mobile-services/)。如需詳細資訊，請參閱 [Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AE564AB28&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fzh-TW%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-ios%2F%20)。

## <a name="create-new-service"> </a>建立新的行動服務

[AZURE.INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

## 建立新的 iOS 應用程式

您可以依照 Azure 傳統入口網站中簡單的快速入門，來建立連線到您行動服務的新應用程式：

1. 在 [Azure 傳統入口網站]中，按一下 [行動服務]，然後按一下您剛建立的行動服務。

2. 在 [快速入門] 索引標籤中，按一下 [選擇平台] 下方的 [iOS]，然後展開 [建立新的 iOS 應用程式]。這會顯示步驟，用來建立與您行動服務連線的 iOS 應用程式。

3. 按一下 [Create TodoItem table] 以建立儲存應用程式資料的資料表。

4. 在 [Download and run your app] 下，按 [下載]。如此會下載與您行動服務連線之範例應用程式 _To do list_ 的專案，以及行動服務 iOS SDK。請將壓縮的專案檔案儲存至本機電腦，並記下儲存位置。

## 執行新的 iOS 應用程式

[AZURE.INCLUDE [mobile-services-ios-run-app](../../includes/mobile-services-ios-run-app.md)]

<ol start="4"><li><p>回到 [Azure 傳統入口網站]，按一下 [資料] 索引標籤，然後按一下 [TodoItems] 資料表。如此可讓您瀏覽由應用程式插入資料表中的資料。<p></li></ol></p>

## <a name="next-steps"> </a>後續步驟
了解如何在行動服務中執行其他重要工作：

* [開始使用離線資料同步] <br/>了解如何使用離線資料同步功能來讓應用程式的反應更快，且更健全。

* [在限有的應用程式中新增驗證功能] <br/>了解如何利用身分識別提供者來驗證您應用程式的使用者。

* [將推播通知新增至現有的應用程式中] <br/>了解如何將非常基本的推播通知傳送至應用程式。

[AZURE.INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]


<!-- Anchors. -->
[Getting started with Mobile Services]: #getting-started
[Create a new mobile service]: #create-new-service
[Define the mobile service instance]: #define-mobile-service-instance
[Next Steps]: #next-steps

<!-- Images. -->
[6]: ./media/mobile-services-ios-get-started/mobile-portal-quickstart-ios.png
[7]: ./media/mobile-services-ios-get-started/mobile-quickstart-steps-ios.png
[8]: ./media/mobile-services-ios-get-started/mobile-xcode-project.png

[10]: ./media/mobile-services-ios-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/mobile-services-ios-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-ios-get-started/mobile-data-browse.png


<!-- URLs. -->
[開始使用離線資料同步]: mobile-services-ios-get-started-offline-data.md
[在限有的應用程式中新增驗證功能]: mobile-services-dotnet-backend-ios-get-started-users.md
[將推播通知新增至現有的應用程式中]: mobile-services-dotnet-backend-ios-get-started-push.md


[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Azure 傳統入口網站]: https://manage.windowsazure.com/
[XCode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[.NET 後端版本]: mobile-services-dotnet-backend-ios-get-started.md

<!---HONumber=AcomDC_0211_2016-->