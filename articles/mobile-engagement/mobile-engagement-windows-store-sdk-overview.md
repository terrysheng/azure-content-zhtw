<properties 
	pageTitle="Windows 通用 SDK 概觀" 
	description="適用於 Azure Mobile Engagement 的 Windows 通用 SDK 概觀。" 									
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
	ms.date="08/10/2015" 
	ms.author="piyushjo" />

#適用於 Azure Mobile Engagement 的 Windows 通用 SDK 概觀

從這裡開始了解所有在 Windows 通用 app 中整合 Azure Mobile Engagement 的細節。如果您想要先試用一下，請務必先完成我們的 [15 分鐘教學課程](mobile-engagement-windows-store-dotnet-get-started.md)。

按一下以查看 [SDK 內容](mobile-engagement-windows-store-sdk-content.md)

##整合程序

1. 從這裡開始：[如何在 Windows 通用 app 中整合 Mobile Engagement](mobile-engagement-windows-store-integrate-engagement.md)

2. 通知：[如何在 Windows 通用 app 中整合 Reach (通知)](mobile-engagement-windows-store-integrate-engagement-reach.md)

3. 標記計劃實作：[如何在 Windows 通用 app 中使用進階的 Mobile Engagement 標記 API](mobile-engagement-windows-store-use-engagement-api.md)。

##版本資訊

###3\.2.0 (11/20/2015)

-   新增對 Windows 10 通用 Windows 平台應用程式的支援。
-   新增推播通到共用功能，以修正通道衝突 (現已與 Azure 通知中樞相容)。
-   修正初始化之後立即要求裝置 ID 時發生的當機。
-   主控台記錄檔改善。
-   修正解析未處理的例外狀況時發生的當機。

如需較早版本，請參閱[完整版本資訊](mobile-engagement-windows-store-release-notes.md)

##升級程序

如果您已經整合舊版 Engagement 到您的應用程式，在升級 SDK 時您必須考慮以下幾點。

如果您錯過數個版本的 SDK，可能必須遵循幾個程序步驟，請參閱完整的[升級程序](mobile-engagement-windows-store-upgrade-procedure.md)。例如，如果您要從 0.10.1 移轉到 0.11.0 您必須先遵循「從 0.9.0 到 0.10.1」的程序，然後「從 0.10.1 到 0.11.0」的程序。

###從 3.1.0 到 3.2.0

#### 資源
此步驟僅涉及自訂資源。如果您已自訂透過 SDK (html、影像、重疊) 提供的資源，則您必須先備份這些資源，然後在已升級的資源上升級和重新套用您的自訂。

#### Web 檢視整合
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

### 從舊版升級

請參閱[升級程序](mobile-engagement-windows-store-upgrade-procedure/)

<!---HONumber=AcomDC_1125_2015-->