<properties
	pageTitle="如何在 .NET 後端行動服務中定義自訂 API | Azure 行動服務"
	description="了解如何在 .NET 後端行動服務中定義自訂 API 端點。"
	services="mobile-services"
	documentationCenter=""
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-multiple"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="12/07/2015"
	ms.author="glenga"/>


# 如何：在 .NET 後端行動服務中定義自訂 API 端點

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


> [AZURE.SELECTOR]
- [JavaScript backend](./mobile-services-javascript-backend-define-custom-api.md)
- [.NET backend](./mobile-services-dotnet-backend-define-custom-api.md)

本主題示範如何在 .NET 後端行動服務中定義自訂 API 端點。自訂 API 可讓您定義具有伺服器功能的自訂端點，但無法對應資料庫的插入、更新、刪除或讀取作業。您可以藉由使用自訂 API 進一步控制訊息，包括 HTTP 標頭和主體格式。

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-custom-api](../../includes/mobile-services-dotnet-backend-create-custom-api.md)]

如需如何使用行動服務用戶端程式庫叫用 App 之自訂 API 的相關資訊，請參閱用戶端 SDK 參考中的[呼叫自訂 API](mobile-services-windows-dotnet-how-to-use-client-library.md#custom-api)。


<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->

<!-------HONumber=AcomDC_1210_2015--->