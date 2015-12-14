<properties
	pageTitle="Azure Mobile Apps 中的驗證與授權 | Microsoft Azure"
	description="Azure Mobile Apps 驗證 / 授權功能的概念性參考與概觀"
	services="app-service\mobile"
	documentationCenter=""
	authors="mattchenderson" 
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="11/30/2015"
	ms.author="mahender"/>

# Azure Mobile Apps 中的驗證與授權

[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

## 什麼是 App Service 驗證 / 授權？

App Service 驗證 / 授權是一項功能，可讓您的應用程式登入使用者，而不需在 app 後端進行任何程式碼變更。它提供簡單的方法來保護您的應用程式，以及使用每位使用者的資料。

App Service 會使用同盟身分識別，第 3 方**身分識別提供者** ("IDP") 會在其中儲存帳戶和驗證使用者，而應用程式會使用這個身分識別，而不是它自己的身分識別。App Service 支援五個現成的身分識別提供者：_Azure Active Directory_、_Facebook_、_Google_、_Microsoft 帳戶_及 _Twitter_。您也可以藉由整合其他身分識別提供者或您自己自訂的身分識別解決方案，針對您的 app 延伸此支援。

您的 app 可以利用任意數目的這類身分識別提供者，因此可為您的使用者提供登入選項。

如果您希望立即開始，請參閱下列其中一個教學課程：

- [將驗證新增至您的 iOS 應用程式]
- [將驗證新增至 Xamarin.iOS 應用程式]
- [將驗證新增至 Xamarin.Android 應用程式]
- [將驗證新增至您的 Windows App]

## 驗證的運作方式

若要使用其中一個身分識別提供者進行驗證，您首先需要設定身分識別提供者來了解您的應用程式。身分識別提供者接著將為您提供識別碼和密碼，您需要將這兩者再次提供給應用程式。這會完成的信任關係，並讓 App Service 能夠驗證其所提供的身分識別。

這些步驟將於下列主題中詳細說明：

- [如何設定您的 App 以使用 Azure Active Directory 登入]
- [如何設定 App 以使用 Facebook 登入]
- [如何設定 App 以使用 Google 登入]
- [如何設定 App 以使用 Microsoft 帳戶登入]
- [如何設定 App 以使用 Twitter 登入]

在後端將一切設定就緒之後，您就可以修改用來登入的用戶端。有以下兩種方法：

- 使用單一行程式碼，讓 Mobile Apps 用戶端 SDK 登入使用者。
- 利用指定的身分識別提供者發佈的 SDK 來建立身分識別，然後取得 App Service 的存取權。

>[AZURE.TIP]大部分的應用程式都應該使用提供者 SDK 來獲得更自然的登入體驗，以及利用重新整理支援和其他提供者專屬的權益。

### 不使用提供者 SDK 進行驗證的運作方式

如果您不想設定提供者 SDK，則可允許 Mobile Apps 來為您執行登入。Mobile Apps 用戶端 SDK 將為您選擇的提供者開啟網頁檢視，然後完成登入。您有時會在部落格和論壇上看到這稱為「伺服器流程」或「伺服器導向的流程」，因為伺服器正在管理登入，而用戶端 SDK 永遠都不會收到提供者權杖。

啟動此流程所需的程式碼會包含於適用於每個平台的驗證教學課程中。在流程結束時，用戶端 SDK 會擁有一個 App Service 權杖，而該權杖會自動附加至對後端的所有要求。

### 使用提供者 SDK 進行驗證的運作方式

使用提供者 SDK 可讓登入體驗與 app 執行所在的平台作業系統更緊密地進行互動。這也會提供您一個提供者權杖以及用戶端上的一些使用者資訊，讓它更容易取用圖表 API 和自訂使用者體驗。您有時會在部落格和論壇上看到這稱為「用戶端流程」或「用戶端導向的流程」，因為用戶端上的程式碼正在處理登入，而且用戶端程式碼可以存取提供者權杖。

一旦取得提供者權杖之後，它需要傳送給 App Service 以進行驗證。在流程結束時，用戶端 SDK 會擁有一個 App Service 權杖，而該權杖會自動附加至對後端的所有要求。如果開發人員選擇這樣做，他們也可以保留對提供者權杖的參考。

## 授權的運作方式

App Service 驗證 / 授權會公開**未驗證要求時要採取的動作**的數個選項。在您的程式碼收到指定的要求之前，您可以進行 App Service 檢查來查看是否會驗證要求，如果沒有，請加以拒絕，並先嘗試讓使用者登入，然後再試一次。

其中一個選項是讓未經驗證的要求重新導向至其中一個身分識別提供者。在 Web 瀏覽器中，這實際上會將使用者帶到新頁面。不過，如此一來，您的行動用戶端就無法重新導向，而且未經驗證的回應將會收到 HTTP「401 未經授權」回應。考慮到這一點，您的用戶端所提出的第一個要求應一律為登入端點，而您接著可對任何其他 API 進行呼叫。如果您嘗試在登入之前呼叫另一個 API，您的用戶端將會收到錯誤。

如果您想要更細微地控制哪些端點需要驗證，您也可以針對未經驗證的要求挑選「沒有動作 (允許要求)」。在此情況下，所有驗證決策都會延後至您的應用程式程式碼。這也可以讓您根據自訂的授權規則來允許存取特定的使用者。

## 文件

下列教學課程示範如何使用 App Service，將驗證新增至您的行動用戶端：

- [將驗證新增至您的 iOS 應用程式]
- [將驗證新增至 Xamarin.iOS 應用程式]
- [將驗證新增至 Xamarin.Android 應用程式]
- [將驗證新增至您的 Windows App]

下列教學課程示範如何設定 App Service 來運用不同的驗證提供者：

- [如何設定您的 App 以使用 Azure Active Directory 登入]
- [如何設定 App 以使用 Facebook 登入]
- [如何設定 App 以使用 Google 登入]
- [如何設定 App 以使用 Microsoft 帳戶登入]
- [如何設定 App 以使用 Twitter 登入]

如果您想要使用此處提供之身分識別系統以外的身分識別系統，也可以利用[預覽 .NET 伺服器 SDK 中的自訂驗證支援](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#custom-auth)。

您也可以在 [App Service 驗證概觀](app-service-authentication-overview.md)中找到一些有關上述流程的更詳盡資訊。本主題也包含 App Service 閘道器的相關資訊，儘管 Mobile Apps 中已不再使用此功能，但概念性內容仍然適用。

[將驗證新增至您的 iOS 應用程式]: app-service-mobile-ios-get-started-users.md
[將驗證新增至 Xamarin.iOS 應用程式]: app-service-mobile-xamarin-ios-get-started-users.md
[將驗證新增至 Xamarin.Android 應用程式]: app-service-mobile-xamarin-android-get-started-users.md
[將驗證新增至您的 Windows App]: app-service-mobile-windows-store-dotnet-get-started-users.md

[如何設定您的 App 以使用 Azure Active Directory 登入]: app-service-mobile-how-to-configure-active-directory-authentication.md
[如何設定 App 以使用 Facebook 登入]: app-service-mobile-how-to-configure-facebook-authentication.md
[如何設定 App 以使用 Google 登入]: app-service-mobile-how-to-configure-google-authentication.md
[如何設定 App 以使用 Microsoft 帳戶登入]: app-service-mobile-how-to-configure-microsoft-authentication.md
[如何設定 App 以使用 Twitter 登入]: app-service-mobile-how-to-configure-twitter-authentication.md

<!---HONumber=AcomDC_1203_2015-->