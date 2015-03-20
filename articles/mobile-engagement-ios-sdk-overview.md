<properties 
	pageTitle="Azure Mobile Engagement iOS SDK 概觀" 
	description="Azure Mobile Engagement iOS SDK 的最新更新與程序"
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="kpiteira" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="objective-c" 
	ms.topic="article" 
	ms.date="02/12/2015" 
	ms.author="kapiteir" />

#2.0.0

從這裡開始取得有關如何在 iOS 應用程式中整合 Azure Mobile Engagement 的所有詳細資料。如果您想要先試用一下，請確認完成我們的 [15 分鐘教學課程](../mobile-engagement-ios-get-started/)。

按一下來查看 [SDK 內容](../mobile-engagement-ios-sdk-content)

##整合程序
1. 從這裡開始：[如何在 iOS 應用程式中整合 Mobile Engagement](../mobile-engagement-ios-integrate-engagement/)

2. 針對通知：[如何在 iOS 應用程式中整合 Reach (通知)](../mobile-engagement-ios-integrate-engagement-reach/)

3. 標記計畫實作：[如何在 iOS 應用程式中使用進階的 Mobile Engagement 標記 API](../mobile-engagement-ios-use-engagement-api/)


##版本資訊

###2.0.0 (2015/02/17)

-   Azure Mobile Engagement 的最初發行版本
-   連接字串組態取代 appId/sdkKey 組態。
-   已移除從傳送與接收任意 XMPP 實體之任意 XMPP 訊息的 API。
-   已移除傳送與接收裝置之間的訊息的 API。
-   增強安全性。
-   已移除 SmartAd 追蹤。

如需較早版本，請參閱[完整版本資訊](../mobile-engagement-ios-release-notes/)

##升級程序

如果您已經整合舊版 Engagement 到您的應用程式，在升級 SDK 時您必須考慮以下幾點。

如果您錯過數個版本的 SDK，您可能要遵循數個程序，請參閱完整的[升級程序](../mobile-engagement-ios-upgrade-procedure/)。

針對每個新版 SDK，您必須先取代 (在 xcode 中移除並重新匯入) EngagementSDK 與 EngagementReach 資料夾。

###從 1.16.0 到 2.0.0
以下說明如何將 SDK 整合從 Capptain SAS 提供的 Capptain 服務，移轉到由 Azure Mobile Engagement 提供的應用程式內。 

>[Azure.IMPORTANT] Capptain 和 Mobile Engagement 是不同的服務，而以下程序只專注在移轉用戶端應用程式。移轉應用程式中的 SDK「不會」將您的資料從 Capptain 伺服器移轉到 Mobile Engagement 伺服器

如果您是從較早版本移轉，請參閱 Capptain 網站以先移轉到 1.16 然後套用以下程序

#### 代理程式

`registerApp:` 方法已由新方法 `init:` 取代。您的應用程式委派必須隨之更新，並使用連接字串：

			- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
			{
			  [...]
			  [EngagementAgent init:@"YOUR_CONNECTION_STRING"];
			  [...]
			}

SmartAd 追蹤已從 SDK 移除，因此您必須移除  `AETrackModule` 類別的所有執行個體

#### 類別名稱變更

執行品牌再造時，有幾個類別/檔案名稱需要變更。

所有首碼為 "CP" 的類別，都使用 "AE" 首碼重新命名。

範例：

-   `CPModule.h` 已重新命名為  `AEModule.h`。

所有首碼為 "Capptain" 的類別，都使用 "Engagement" 首碼重新命名。

範例：

-    `CapptainAgent` 類別已重新命名為  `EngagementAgent`。
-    `CapptainTableViewController` 類別已重新命名為  `EngagementTableViewController`。
-    `CapptainUtils` 類別已重新命名為  `EngagementUtils`。
-    `CapptainViewController` 類別已重新命名為  `EngagementViewController`。

<!--HONumber=47-->
