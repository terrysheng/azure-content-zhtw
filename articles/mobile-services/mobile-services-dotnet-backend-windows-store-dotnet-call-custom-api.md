<properties 
	pageTitle="從 Windows 市集用戶端呼叫自訂 API - 行動服務" 
	description="了解如何定義自訂 API 然後從使用 Azure 行動服務的 Windows 市集應用程式呼叫它。" 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	writer="glenga" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/26/2015" 
	ms.author="glenga"/>

# 從用戶端呼叫自訂 API

[AZURE.INCLUDE [mobile-services-selector-call-custom-api](../../includes/mobile-services-selector-call-custom-api.md)]

本主題將示範如何從 Windows 市集應用程式呼叫自訂 API。自訂 API 可讓您定義自訂端點，並用來公開無法對應插入、更新、刪除或讀取等操作的伺服器功能。透過使用自訂 API，您可以進一步控制訊息，包括讀取與設定 HTTP 訊息標頭，並定義除了 JSON 以外的訊息內文格式。

本主題中所建立的自訂 API 可讓您傳送單一 POST 要求，並將資料表中所有 todo 項目的已完成旗標設定為 `true`。若沒有此自訂 API，用戶端必須傳送個別要求，來為資料表中的每個 todo 項目更新旗標。

將此功能新增至您在完成[開始使用行動服務]或[開始使用資料]教學課程時所建立的應用程式。若要達到此目的，您將需要完成下列步驟：

1. [定義自訂 API]
2. [更新應用程式以呼叫自訂 API]
3. [測試應用程式] 

本教學課程會以行動服務快速入門為基礎。在開始本教學課程之前，您必須首先完成[開始使用行動服務]或[開始使用資料]。

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-custom-api](../../includes/mobile-services-dotnet-backend-create-custom-api.md)]

## <a name="update-app"></a>更新應用程式以呼叫自訂 API

[AZURE.INCLUDE [mobile-services-windows-store-dotnet-call-custom-api](mobile-services-windows-store-dotnet-call-custom-api.md)]


## 後續步驟

現在您已建立自訂 API，並可從您的 Windows 市集應用程式呼叫此 API，請考慮搜尋有關下列行動服務主題的更多資訊：

* [定義支援定期通知的自訂 API]<br/>了解如何在 Windows 市集應用程式中使用自訂 API 來支援定期通知。在啟用定期通知的情況下，Windows 將定期存取您的自訂 API 端點，並使用傳回的 XML (採用磚的特定格式) 來更新開始功能表上的應用程式磚。

* [行動服務伺服器指令碼參照] <br/> (英文) 深入了解建立自訂 API。

* [在來源控制中儲存伺服器指令碼] <br/>了解如何使用來源控制功能，來更輕鬆及安全地開發並發佈自訂 API 指令碼。

<!-- Anchors. -->
[定義自訂 API]: #define-custom-api
[更新應用程式以呼叫自訂 API]: #update-app
[測試應用程式]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->

<!-- URLs. -->
[行動服務伺服器指令碼參照]: http://go.microsoft.com/fwlink/?LinkId=262293
[開始使用行動服務]: ../mobile-services-windows-store-get-started.md
[開始使用資料]: ../mobile-services-dotnet-backend-windows-store-dotnet-get-started-data.md
[Get started with authentication]: ../mobile-services-dotnet-backend-windows-store-dotnet-get-started-users.md
[Get started with push notifications]: ../mobile-services-dotnet-backend-windows-store-dotnet-get-started-push.md

[定義支援定期通知的自訂 API]: mobile-services-windows-store-dotnet-create-pull-notifications.md
[在來源控制中儲存伺服器指令碼]: mobile-services-store-scripts-source-control.md

<!--HONumber=54--> 