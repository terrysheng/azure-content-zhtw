<properties 
	pageTitle="Azure Mobile Engagement Windows 市集 SDK 概觀" 
	description="適用於 Azure Mobile Engagement 的 Windows 市集 SDK 之最新的更新與程序" 					
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="kpiteira" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="" 
	ms.topic="article" 
	ms.date="02/12/2015" 
	ms.author="kapiteir" />

#2.0.0

從這裡開始了解所有在 Windows 市集應用程式中整合 Azure Mobile Engagement 的細節。如果您想要先試用一下，請確認完成我們的 [15 分鐘教學課程](mobile-engagement-windows-store-dotnet-get-started.md)。

按一下來查看 [SDK 內容](mobile-engagement-windows-store-sdk-content.md)

##整合程序

1. 從這裡開始：[如何在您的 Windows 市集應用程式整合 Mobile Engagement](mobile-engagement-windows-store-integrate-engagement.md)

2. 針對通知：[如何在您的 Windows 市集應用程式整合 Reach (通知)](mobile-engagement-windows-store-integrate-engagement-reach.md)

3. 標記計畫實作：[如何在您的 Windows 市集應用程式使用進階的 Mobile Engagement 標記 API](mobile-engagement-windows-store-use-engagement-api.md)

##版本資訊


##2.0.0 (2015/02/17)

-   Azure Mobile Engagement 的最初發行版本
-   連接字串組態取代 appId/sdkKey 組態。
-   增強安全性。

如需較早版本，請參閱[完整版本資訊](mobile-engagement-windows-store-release-notes.md)

##升級程序

如果您已經整合舊版 Engagement 到您的應用程式，在升級 SDK 時您必須考慮以下幾點。

如果您有錯過幾個版本的 SDK，您必須遵循幾個步驟，請參閱完整的[升級程序](mobile-engagement-windows-store-upgrade-procedure.md)。例如，如果您要從 0.10.1 移轉到 0.11.0 您必須先遵循「從 0.9.0 到 0.10.1」的程序，然後「從 0.10.1 到 0.11.0」的程序。

##從 1.1.1 到 2.0.0

以下說明如何將 SDK 整合從 Capptain SAS 提供的 Capptain 服務，移轉到由 Azure Mobile Engagement 提供的應用程式內。 

>[Azure.IMPORTANT] Capptain 和 Mobile Engagement 是不同的服務，而以下程序只專注在移轉用戶端應用程式。移轉應用程式中的 SDK「不會」將您的資料從 Capptain 伺服器移轉到 Mobile Engagement 伺服器

如果您要從舊版移轉，請先參閱 Capptain 網站以移轉到 1.1.1，然後再遵循以下程序

### Nuget 封裝

使用封存套件內，[lib] 資料夾中的 azuresdk-mobileengagement-windows-VERSION.nupkg 取代 Capptain.WindowsPhone.nupkg。

### 套用 Mobile Engagement

該 SDK 使用 ` Engagement `這個詞。您需要更新您的專案，以符合此變更。

您需要解除安裝目前的 Capptain nuget 封裝。請考慮您在 [Capptain Resources] 資料夾中所有的變更將會移除。如果您想要保留這些檔案，請將它們複製一份。

接下來，在您的專案上安裝新的 Microsoft Azure Engagement nuget 封裝。您可以直接在 [nuget 網站]或此處的索引找到它。此動作會取代所有 Engagement 使用的資源檔，並將新的 Engagement DLL 新增到您專案的「參考」。

您必須刪除 Capptain DLL 參考來清除您專案的參考。如果沒有這樣做，Capptain 的版本會有衝突並發生錯誤。

若您有自訂的 Capptain 資源，請複製您舊檔案的內容，並在新的 Engagement 檔案中貼上它們。請注意，xaml 和 cs 檔案兩者都必須更新。

完成這些步驟之後，您只需要用新的 Engagement 參考取代舊的 Capptain 參考。

所有的 Capptain 命名空間都必須更新。

移轉前：

			using Capptain.Agent;
			using Capptain.Reach;

移轉後：

			using Microsoft.Azure.Engagement;

所有包含 "Capptain" 的 Capptain 類別應該要包含 "Engagement"。

移轉前：

			public sealed partial class MainPage : CapptainPage
			{
			  protected override string GetCapptainPageName()
			  {
			    return "Capptain Demo";
			  }
			  ...
			}

移轉後：

			public sealed partial class MainPage : EngagementPage
			{
			  protected override string GetEngagementPageName()
			  {
			    return "Engagement Demo";
			  }
			  ...
			}

對於 xaml 檔案，Capptain 命名空間和屬性也會變更。

移轉前：

			<capptain:CapptainPage
			...
			xmlns:capptain="using:Capptain.Agent"
			...
			</capptain:CapptainPage>

移轉後：

			<engagement:EngagementPage
			...
			xmlns:engagement="using:Microsoft.Azure.Engagement"
			...
			</engagement:EngagementPage>

> [AZURE.IMPORTANT] 重疊項目也會變更。它的新命名空間為  `Microsoft.Azure.Engagement.Overlay`。在 xaml 和 cs 檔案中都必須使用它。此外， `CapptainGrid` 命名為 `EngagementGrid`， `capptain_notification_content` 和 `capptain_announcement_content` 命名為 `engagement_notification_content` 和 `engagement_announcement_content`。

針對重疊：

			<capptain:CapptainPageOverlay
			  xmlns:capptain="using:Capptain.Overlay"
			  ...
			</capptain:CapptainPageOverlay>

它會變成：

			<EngagementPageOverlay
			  engagement="using:Microsoft.Azure.Engagement.Overlay"
			  ...
			</engagement:EngagementPageOverlay>

針對其他資源，例如 Capptain 圖片和 HTML 檔案，請注意它們也已重新命名使用 "Engagement"。

### 專案宣告

Package.appxmanifest 上的 `File Type Associations` 已經更新自：

> -   capptain\_reach\_content to engagement\_reach\_content
> -   capptain\_log\_file to engagement\_log\_file

### 應用程式 ID / SDK 金鑰

Engagement 使用連接字串。您不需要為 Mobile Engagement 指定應用程式 ID 和 SDK 金鑰，您只需要指定連接字串。您可以在您的 EngagementConfiguration 檔案中設定它。

Engagement 組態可以在您專案中的 `Resources\EngagementConfiguration.xml` 檔案內設定。

編輯此檔案來指定：

-   您應用程式在 `<connectionString>` 和 `<\connectionString>` 標記之間的連接字串。

若想要改為在執行階段指定它，您可以在 Engagement 代理程式初始化之前呼叫下列方法：

			/* Engagement configuration. */
			EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
			engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
			
			/* Initialize Engagement agent with above configuration. */
			EngagementAgent.Instance.Init(args, engagementConfiguration);

您應用程式的連接字串會顯示在 Azure 入口網站。

### 項目名稱變更

所有名為「...capptain...」的項目已命名為「...engagement...」。Capptain 和 Engagement 也是如此。

常用 Capptain 項目的範例：

> -   CapptainConfiguration 現在名為 EngagementConfiguration
> -   CapptainAgent 現在名為 EngagementAgent
> -   CapptainReach 現在名為 EngagementReach
> -   CapptainHttpConfig 現在名為 EngagementHttpConfig
> -   GetCapptainPageName 現在名為 GetEngagementPageName

請注意，重新命名也會影響已覆寫的方法。

[nuget 網站]:http://www.nuget.org/packages/Capptain.WindowsCS

<!--HONumber=47-->
