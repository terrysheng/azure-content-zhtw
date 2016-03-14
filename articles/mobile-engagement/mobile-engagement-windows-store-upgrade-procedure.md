<properties 
	pageTitle="Windows 通用 app SDK 升級程序" 
	description="適用於 Azure Mobile Engagement 的 Windows 通用 app SDK 升級程序" 					
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="piyushjo" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/29/2016" 
	ms.author="piyushjo" />

#Windows 通用 app SDK 升級程序

如果您已經整合舊版 Engagement 到您的應用程式，在升級 SDK 時您必須考慮以下幾點。

如果您有錯過幾個版本的 SDK，您必須遵循幾個步驟。例如，如果您要從 0.10.1 移轉到 0.11.0 您必須先遵循「從 0.9.0 到 0.10.1」的程序，然後「從 0.10.1 到 0.11.0」的程序。

##從 3.2.0 到 3.3.0

### 資源
此步驟僅涉及自訂資源。如果您已自訂透過 SDK (html、影像、重疊) 提供的資源，則您必須先備份這些資源，然後在已升級的資源上升級和重新套用您的自訂。

##從 3.1.0 到 3.2.0

### 資源
此步驟僅涉及自訂資源。如果您已自訂透過 SDK (html、影像、重疊) 提供的資源，則您必須先備份這些資源，然後在已升級的資源上升級和重新套用您的自訂。

### Web 檢視整合
部分改善以符合此版本中引進的其他裝置版型規格。確認您的 Web 檢視符合下列條件：

在您的 XAML page () 中：

			<WebView x:Name="engagement_notification_content" Visibility="Collapsed" Height="80" HorizontalAlignment="Right" VerticalAlignment="Top"/>
			<WebView x:Name="engagement_announcement_content" Visibility="Collapsed" HorizontalAlignment="Right" VerticalAlignment="Top"/> 

且在您相關聯的 .cs 檔案中：

    using Microsoft.Azure.Engagement;
    using System;
    using Windows.ApplicationModel.Core;
    using Windows.UI.ViewManagement;
    using Windows.UI.Xaml;
    using Windows.UI.Xaml.Navigation;

    namespace My.Namespace.Example
    {
			/// <summary>
			/// An empty page that can be used on its own or navigated to within a Frame.
			/// </summary>
			public sealed partial class ExampleEngagementReachPage : EngagementPage
			{
			  public ExampleEngagementReachPage()
			  {
			    this.InitializeComponent();
			
			    /* Set your webview elements to the correct size. */
			    SetWebView(width, height);
			  }
			
			  #region to implement
              /* Attach events when page is navigated. */
              protected override void OnNavigatedTo(NavigationEventArgs e)
              {
                /* Update the webview when the app window is resized. */
                Window.Current.SizeChanged += DisplayProperties_OrientationChanged;

                /* Update the webview when the app/status bar is resized. */
    #if WINDOWS_PHONE_APP || WINDOWS_UWP
                ApplicationView.GetForCurrentView().VisibleBoundsChanged += DisplayProperties_VisibleBoundsChanged; 
    #endif
                base.OnNavigatedTo(e);
              }

			  /* When page is left ensure to detach SizeChanged handler. */
			  protected override void OnNavigatedFrom(NavigationEventArgs e)
			  {
			    Window.Current.SizeChanged -= DisplayProperties_OrientationChanged;
    #if WINDOWS_PHONE_APP || WINDOWS_UWP
                ApplicationView.GetForCurrentView().VisibleBoundsChanged -= DisplayProperties_VisibleBoundsChanged;
    #endif
			    base.OnNavigatedFrom(e);
			  }
			  
			  /* "width" and "height" are the current size of your application display. */
    #if WINDOWS_PHONE_APP || WINDOWS_UWP
			  double width = ApplicationView.GetForCurrentView().VisibleBounds.Width;
			  double height = ApplicationView.GetForCurrentView().VisibleBounds.Height;
    #else
			  double width =  Window.Current.Bounds.Width;
			  double height =  Window.Current.Bounds.Height;
    #endif
			
			  /// <summary>
			  /// Set your webview elements to the correct size.
			  /// </summary>
			  /// <param name="width">The width of your current display.</param>
			  /// <param name="height">The height of your current display.</param>
			  private void SetWebView(double width, double height)
			  {
			    #pragma warning disable 4014
			    CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal,
			            () =>
			            {
			              this.engagement_notification_content.Width = width;
			              this.engagement_announcement_content.Width = width;
			              this.engagement_announcement_content.Height = height;
			            });
			  }
			
			  /// <summary>
			  /// Handler that takes the Windows.Current.SizeChanged and indicates that webviews have to be resized.
			  /// </summary>
			  /// <param name="sender">Original event trigger.</param>
			  /// <param name="e">Window Size Changed Event arguments.</param>
			  private void DisplayProperties_OrientationChanged(object sender, Windows.UI.Core.WindowSizeChangedEventArgs e)
			  {
			    double width = e.Size.Width;
			    double height = e.Size.Height;
			
			    /* Set your webview elements to the correct size. */
			    SetWebView(width, height);
			  }

    #if WINDOWS_PHONE_APP || WINDOWS_UWP			  
			  /// <summary>
			  /// Handler that takes the ApplicationView.VisibleBoundsChanged and indicates that webviews have to be resized
			  /// </summary>
			  /// <param name="sender">The related application view.</param>
			  /// <param name="e">Related event arguments.</param>
			  private void DisplayProperties_VisibleBoundsChanged(ApplicationView sender, Object e)
			  {
			    double width = sender.VisibleBounds.Width;
			    double height = sender.VisibleBounds.Height;
			
			    /* Set your webview elements to the correct size. */
			    SetWebView(width, height);
			  }
    #endif
			  #endregion
			}
    }

##從 2.0.0 到 3.0.0

### 資源
此步驟僅涉及自訂資源。如果您已自訂透過 SDK (html、影像、重疊) 提供的資源，則您必須先備份這些資源，然後在已升級的資源上升級和重新套用您的自訂。

##從 1.1.1 到 2.0.0

以下說明如何將 SDK 整合從 Capptain SAS 提供的 Capptain 服務，移轉到由 Azure Mobile Engagement 提供的應用程式內。

> [Azure.IMPORTANT] Capptain 和 Mobile Engagement 是不同的服務，而以下程序只專注在移轉用戶端應用程式。移轉應用程式中的 SDK「不會」將您的資料從 Capptain 伺服器移轉到 Mobile Engagement 伺服器

如果您要從舊版移轉，請先參閱 Capptain 網站以移轉到 1.1.1，然後再遵循以下程序

### Nuget 封裝

以 **MicrosoftAzure.MobileEngagement** Nuget 封裝取代 **Capptain.WindowsPhone**。

### 套用 Mobile Engagement

SDK 使用 `Engagement` 一詞。您需要更新您的專案，以符合此變更。

您需要解除安裝目前的 Capptain nuget 封裝。請考慮您在 [Capptain Resources] 資料夾中所有的變更將會移除。如果您想要保留這些檔案，請將它們複製一份。

接下來，在您的專案上安裝新的 Microsoft Azure Engagement nuget 封裝。您可以直接在 [nuget 網站] 或此處的索引找到它。此動作會取代所有 Engagement 使用的資源檔，並將新的 Engagement DLL 新增到您專案的「參考」。

您必須刪除 Capptain DLL 參考來清除您專案的參考。如果沒有這樣做，Capptain 的版本會有衝突並發生錯誤。

若您有自訂的 Capptain 資源，請複製您舊檔案的內容，並在新的 Engagement 檔案中貼上它們。請注意，xaml 和 cs 檔案兩者都必須更新。

完成這些步驟之後，您只需要用新的 Engagement 參考取代舊的 Capptain 參考。

1. 所有的 Capptain 命名空間都必須更新。

	移轉前：
	
		using Capptain.Agent;
		using Capptain.Reach;
	
	移轉後：
	
		using Microsoft.Azure.Engagement;

2. 所有包含 "Capptain" 的 Capptain 類別應該要包含 "Engagement"。

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

3. 對於 xaml 檔案，Capptain 命名空間和屬性也會變更。

	移轉前：
	
		<capptain:CapptainPage
		...
		xmlns:capptain="clr-namespace:Capptain.Agent;assembly=Capptain.Agent.WP"
		...
		</capptain:CapptainPage>
	
	移轉後：
	
		<engagement:EngagementPage
		...
		xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"
		...
		</engagement:EngagementPage>

4. 重疊項目頁面變更
	> [AZURE.IMPORTANT] 重疊項目也會變更。它的新命名空間為 `Microsoft.Azure.Engagement.Overlay`。在 xaml 和 cs 檔案中都必須使用它。此外，`CapptainGrid` 命名為 `EngagementGrid`，`capptain_notification_content` 和 `capptain_announcement_content` 命名為 `engagement_notification_content` 和 `engagement_announcement_content`。
	
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

5. 針對其他資源，例如 Capptain 圖片和 HTML 檔案，請注意它們也已重新命名使用 "Engagement"。

### 專案宣告

Package.appxmanifest 上的 `File Type Associations` 已經更新自：

 -   capptain\_reach\_content 至 engagement\_reach\_content
 -   capptain\_log\_file 至 engagement\_log\_file

### 應用程式 ID / SDK 金鑰

Engagement 使用連接字串。您不需要為 Mobile Engagement 指定應用程式 ID 和 SDK 金鑰，您只需要指定連接字串。您可以在您的 EngagementConfiguration 檔案中設定它。

您可以在專案的 `Resources\EngagementConfiguration.xml` 檔案中設定 Engagement 組態。

編輯此檔案來指定：

-   應用程式在 `<connectionString>` 和 `<\connectionString>` 標記之間的連接字串。

若想要改為在執行階段指定它，您可以在 Engagement 代理程式初始化之前呼叫下列方法：

	/* Engagement configuration. */
	EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
	engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
	
	/* Initialize Engagement agent with above configuration. */
	EngagementAgent.Instance.Init(args, engagementConfiguration);

應用程式的連接字串會顯示在 Azure 傳統入口網站中。

### 項目名稱變更

所有名為 *capptain* 的項目已命名為 *engagement* 。同樣地， *Capptain* 也已命名為 *Engagement* 。

常用 Capptain 項目的範例：

-   CapptainConfiguration 現在名為 EngagementConfiguration
-   CapptainAgent 現在名為 EngagementAgent
-   CapptainReach 現在名為 EngagementReach
-   CapptainHttpConfig 現在名為 EngagementHttpConfig
-   GetCapptainPageName 現在名為 GetEngagementPageName

請注意，重新命名也會影響覆寫方法。

 

<!---HONumber=AcomDC_0302_2016-------->