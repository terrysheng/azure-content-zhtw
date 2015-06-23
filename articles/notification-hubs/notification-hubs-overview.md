<properties 
	pageTitle="Azure 通知中樞" 
	description="了解如何在 Azure 中使用推播通知。程式碼範例是以 C# 撰寫並使用 .NET API。" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor="" 
	services="notification-hubs" 
	documentationCenter=""/>

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="multiple" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="02/24/2015" 
	ms.author="wesmc"/>


# Azure 通知中樞

## 概觀

Azure 中的推播通知支援可讓您存取易於使用、多重平台的大規模推播基礎結構，而大幅簡化消費者和企業應用程式在行動平台上的推播通知實作。

## 什麼是推播通知？

智慧型手機和平板電腦都具有在事件發生時「通知」使用者的功能。在 Windows 市集應用程式中，通知可能會導致 _toast_：具有音效的非強制回應視窗出現，以指出有新的推播。在 Apple iOS 裝置上，推播同樣會以對話方塊的形式蹦現，而要求使用者檢視或關閉通知。按一下 [**檢視**] 即會開啟接收訊息的應用程式。

推播通知有助於行動裝置在保有足夠資源的情況下顯示最新資訊。推播通知是消費者應用程式的重要元件，可用來增加應用程式的應用面和使用性。通知對企業也有幫助，因為掌握最新資訊的員工對於商務活動更能處理得當。 

行動應用案例的特定範例包括：

1.  以目前的財務資訊更新 Windows 8 或 Windows Phone 上的磚。
2.  在以工作流程為基礎的企業應用程式中，透過已將某個工作項目指派給使用者的快顯通知，對該名使用者發出警示。
3.  在 CRM 應用程式 (例如 Microsoft Dynamics CRM) 中，顯示具有目前潛在客戶數目的徽章。

## 推播通知的運作方式

推播通知可透過名為 _Platform Notification Systems_ (PNS) 的平台特定基礎結構來傳遞。PNS 可提供準系統功能 (即不支援廣播和個人化的系統)，不具共同介面。例如，若要傳送通知給 Windows 市集應用程式，開發人員必須連絡 WNS (Windows 通知服務) 以將通知傳送至 iOS 裝置，且必須連絡 APNS (Apple 推播通知服務)，而再次傳送訊息。

但綜觀而言，所有平台通知系統都依循相同的模式：

1.  用戶端應用程式連絡 PNS 以擷取其_控制代碼_。控制代碼類型視系統而定。WNS 的控制代碼類型為 URI 或「通知通道」。APNS 的控制代碼則為權杖。
2.  用戶端應用程式會將此控制代碼儲存在應用程式的 _back-end_，以供後續使用。WNS 的後端通常是雲端服務。就 Apple 而言，則是名為 _provider_ 的系統。
3.  若要傳送推播通知，應用程式後端必須使用控制代碼連絡 PNS，以將特定的用戶端應用程式執行個體訂為目標。
4.  PNS 會將通知轉送至控制代碼所指定的裝置。

![][0]

## 推播通知的挑戰

儘管這些系統功能強大，但即便應用程式開發人員想要實作一般的推播通知工作 (例如廣播或傳送推播通知給使用者)，都有許多工作有待處理。

就行動應用程式而言，推播通知是雲端服務中最常被要求的功能之一。原因是，其運作所需的基礎結構頗為複雜，且大部分與應用程式的主要商業邏輯沒有關聯。建置隨選推播基礎結構的挑戰包括：

- **平台相依性。**若要將通知傳送給不同平台上的裝置，必須在後端進行多個介面的編碼。不只低階細節不同，通知的呈現方式 (磚、快顯通知或徽章) 也是因平台而異。這些差異導致後端程式碼的維護既複雜又困難。

- **調整。**此基礎結構的調整可分為兩個層面：
1. 根據 PNS 準則，在每次啟動應用程式時，都必須重新整理裝置權杖。在此情況下，光是為了保有最新的裝置權杖，就會產生大量的流量 (和隨之而來的資料庫存取)。當裝置數目增加時 (可能達百萬以上)，建立及維護此基礎結構的成本將會很可觀。
2.  大部分的 PNS 並不支援廣播至多個裝置。因此，若要廣播至數百萬個裝置，就必須對 PNS 呼叫數百萬次。要能夠調整這些要求可不是容易的事，因為應用程式開發人員通常都會想要維持較低的整體延遲性 (例如，最後一個接收訊息的裝置，不應在通知送出的 30 分鐘後才收到通知，因為這樣很可能就失去推播通知的意義了)。
- **路由。**PNS 可讓您傳送訊息至裝置。不過，大部分的應用程式通知都是以使用者及/或相關群組為目標 (例如，所有指派給特定客戶帳戶的員工)。因此，若要將通知路由到正確的裝置，應用程式後端必須維護將相關群組與裝置權杖產生關聯的註冊。這項負擔將使應用程式的上市時程拉長並增加維護成本。

## 為何要使用通知中樞？

通知中樞提供支援下列項目且容易使用的推播通知基礎結構：

- **多個平台。**通知中樞提供可將通知傳送至所有支援平台的通用介面。應用程式後端可以用平台特定或平台通用的格式傳送通知。通知中樞可以傳送推播通知給 Windows 市集、iOS、Android 和 Windows Phone 應用程式。
- **發佈/訂閱路由。**每個裝置在傳送其控制代碼到通知中樞時，都可指定一或多個 _tags_。如需標籤的詳細資訊，請參閱下一節。標籤不需要預先佈建或處置。標籤可讓您輕鬆地傳送通知給使用者或相關群組。因為標籤可以包含任何應用程式專屬的識別項 (例如使用者或群組識別碼)，其運用使得應用程式後端無須再耗用資源儲存和管理裝置控制代碼。
- **調整。**通知中樞可因應數百萬個裝置的需求，而無需重新架構或分區。

通知中樞採用完整的多重平台、擴充的推播通知基礎結構，可大幅減少應用程式後端所執行的推播專用程式碼。通知中樞可實作推播基礎結構的所有功能。裝置只需註冊其 PNS 控制代碼即可，後端會負責將平台共用格式訊息傳送給使用者或相關群組。

![][1]

## 後續步驟

您可以在以下主題找到「通知中樞」的詳細資訊：

+ **[客戶如何使用通知中樞]**

+ **[通知中樞教學課程和指南]**

+ **通知中樞快速入門教學課程** ([iOS]、[Android]、[Windows Universal]、[Windows Phone]、[Kindle]、[Xamarin.iOS]、[Xamarin.Android])

針對推播通知，相關的 .NET 受管理 API 參考位於此處：

+ [Microsoft.WindowsAzure.Messaging.NotificationHub]
+ [Microsoft.ServiceBus.Notifications] 


  [0]: ./media/notification-hubs-overview/SBPushNotifications1.gif
  [1]: ./media/notification-hubs-overview/SBPushNotifications2.gif
  [客戶如何使用通知中樞]: http://azure.microsoft.com/services/notification-hubs
  [通知中樞教學課程和指南]: http://azure.microsoft.com/documentation/services/notification-hubs
  [iOS]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started
  [Android]: http://azure.microsoft.com/documentation/articles/notification-hubs-android-get-started
  [Windows Universal]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started
  [Windows Phone]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-phone-get-started
  [Kindle]: http://azure.microsoft.com/documentation/articles/notification-hubs-kindle-get-started
  [Xamarin.iOS]: http://azure.microsoft.com/documentation/articles/partner-xamarin-notification-hubs-ios-get-started
  [Xamarin.Android]: http://azure.microsoft.com/documentation/articles/partner-xamarin-notification-hubs-android-get-started
  [Microsoft.WindowsAzure.Messaging.NotificationHub]: http://msdn.microsoft.com/library/microsoft.windowsazure.messaging.notificationhub.aspx
  [Microsoft.ServiceBus.Notifications]: http://msdn.microsoft.com/library/microsoft.servicebus.notifications.aspx
  

<!--HONumber=49--> 