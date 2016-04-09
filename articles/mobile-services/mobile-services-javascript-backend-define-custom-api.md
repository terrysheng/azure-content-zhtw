<properties
	pageTitle="如何在 JavaScript 後端行動服務中定義自訂 API | Azure 行動服務"
	description="了解如何在 JavaScript 後端行動服務中定義自訂 API 端點。"
	services="mobile-services"
	documentationCenter=""
	authors="ggailey777"
	manager="erikre"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-multiple"
	ms.devlang="javascript"
	ms.topic="article"
	ms.date="03/06/2016"
	ms.author="glenga"/>


# 如何：在 .NET 後端行動服務中定義自訂 JavaScript 端點

> [AZURE.SELECTOR]
- [JavaScript 後端](./mobile-services-javascript-backend-define-custom-api.md)
- [.NET 後端](./mobile-services-dotnet-backend-define-custom-api.md)

&nbsp;

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]
> 如需此主題對等的行動應用程式版本，請參閱[做法：定義自訂 API 控制器](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#CustomAPI)。

本主題示範如何在 JavaScript 後端行動服務中定義自訂 API 端點。自訂 API 可讓您定義具有伺服器功能的自訂端點，但無法對應資料庫的插入、更新、刪除或讀取作業。您可以藉由使用自訂 API 進一步控制訊息，包括 HTTP 標頭和主體格式。

[AZURE.INCLUDE [mobile-services-create-custom-api](../../includes/mobile-services-create-custom-api.md)]

如需如何使用行動服務用戶端程式庫叫用 App 之自訂 API 的相關資訊，請參閱用戶端 SDK 參考中的[呼叫自訂 API](mobile-services-windows-dotnet-how-to-use-client-library.md#custom-api)。


<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->

<!---HONumber=AcomDC_0309_2016-->