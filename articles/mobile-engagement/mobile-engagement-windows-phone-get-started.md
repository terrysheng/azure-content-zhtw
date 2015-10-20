<properties
	pageTitle="開始使用適用於 Windows Phone Silverlight 應用程式的 Azure Mobile Engagement"
	description="了解如何使用適用於 Windows Phone Silverlight 應用程式的 Azure Mobile Engagement 執行分析和傳送推播通知。"
	services="mobile-engagement"
	documentationCenter="windows"
	authors="piyushjo"
	manager="dwrede"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows-phone"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="09/22/2015"
	ms.author="piyushjo" />

# 開始使用適用於 Windows Phone Silverlight 應用程式的 Azure Mobile Engagement

> [AZURE.SELECTOR]
- [Windows Universal](mobile-engagement-windows-store-dotnet-get-started.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-get-started.md)
- [iOS | Obj C](mobile-engagement-ios-get-started.md)
- [iOS | Swift](mobile-engagement-ios-swift-get-started.md)
- [Android](mobile-engagement-android-get-started.md)
- [Cordova](mobile-engagement-cordova-get-started.md)

本主題說明如何使用 Azure Mobile Engagement 了解您的應用程式使用狀況，以及傳送推播通知給 Windows Phone Silverlight 應用程式的分佈使用者。本教學課程將示範使用 Mobile Engagement 的簡單廣播案例。在課程中，您將建立一個空白的 Windows Phone Silverlight 應用程式，以使用 Microsoft 推播通知服務 (MPNS) 來收集基本資料及接收推播通知。

> [AZURE.NOTE]如果您的目標是 Windows Phone 8.1 (非 Silverlight)，請參閱 [Windows 通用教學課程](mobile-engagement-windows-store-dotnet-get-started.md)。

本教學課程需要下列各項：

+ Visual Studio 2013
+ [MicrosoftAzure.MobileEngagement] Nuget 封裝

> [AZURE.IMPORTANT]完成本教學課程是與 Windows Phone Silverlight 應用程式有關之所有其他 Mobile Engagement 教學課程的先決條件，而且若要完成本教學課程，您必須擁有有效的 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fzh-TW%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Azure 免費試用</a>。

##<a id="setup-azme"></a>設定 Windows Phone 應用程式的 Mobile Engagement

[AZURE.INCLUDE [在入口網站中建立 Mobile Engagement 應用程式](../../includes/mobile-engagement-create-app-in-portal.md)]

##<a id="connecting-app"></a>將您的應用程式連線至 Mobile Engagement 後端

本教學課程將說明「基本整合」，這是收集資料及傳送推播通知時必要的最低設定。您可以在 [Mobile Engagement Windows Phone SDK 整合](../mobile-engagement-windows-phone-sdk-overview/)文件中找到完整的整合文件。

我們將會使用 Visual Studio 建立基本應用程式來示範整合。

###建立新的 Windows Phone Silverlight 專案

即使下列步驟與舊版 Visual Studio 中的步驟類似，但這些步驟假設使用的是 Visual Studio 2015。

1. 啟動 Visual Studio，並在 [首頁] 畫面上選取 [新增專案]。

2. 在快顯視窗中，依序選取 [Windows 8] -> [Windows Phone] -> [空白應用程式 (Windows Phone Silverlight)]。輸入應用程式的 [名稱] 和 [方案名稱]，然後按一下 [確定]。

    ![][1]

3. 您可以選擇要將目標設為 **Windows Phone 8.0** 或 **Windows Phone 8.1**。

現在已建立新的 Windows Phone Silverlight 應用程式，接著我們要將 Azure Mobile Engagement SDK 整合至其中。

###將您的應用程式連線至 Mobile Engagement 後端

1. 在您的專案中安裝 [MicrosoftAzure.MobileEngagement] NuGet 封裝。

2. 在 Properties 資料夾中開啟 `WMAppManifest.xml`，然後確認 `<Capabilities />` 標記中已宣告下列項目 (如果沒有宣告，請自行新增)：

		<Capability Name="ID_CAP_NETWORKING" />
		<Capability Name="ID_CAP_IDENTITY_DEVICE" />

    ![][2]

3. 現在貼上您稍早為 Mobile Engagement 應用程式複製的連接字串，並將該字串貼在 `Resources\EngagementConfiguration.xml` 檔案中的 `<connectionString>` 和 `</connectionString>` 標記之間：

    ![][3]

4. 在 `App.xaml.cs` 檔案中：

	a.新增 `using` 陳述式：

			using Microsoft.Azure.Engagement;

	b.在 `Application_Launching` 方法中初始化 SDK：

			private void Application_Launching(object sender, LaunchingEventArgs e)
			{
			  EngagementAgent.Instance.Init();
			}

	c.在 `Application_Activated` 中插入以下內容：

			private void Application_Activated(object sender, ActivatedEventArgs e)
			{
			   EngagementAgent.Instance.OnActivated(e);
			}

##<a id="monitor"></a>啟用即時監視

若要開始傳送資料並確定使用者正在使用，您必須至少傳送一個畫面 (活動) 到 Mobile Engagement 後端。

1. 在 MainPage.xaml.cs 中，加入 `using` 陳述式：

    	using Microsoft.Azure.Engagement;

2. 請以 **EngagementPage** 取代 **MainPage** 的基礎類別 (在 **PhoneApplicationPage** 之前)：

    	class MainPage : EngagementPage 
	
3. 在您的 `MainPage.xml` 檔案中：

	a.新增至命名空間宣告：

	   	 xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"

	b.以 `engagement:EngagementPage` 取代 XML 標記名稱內的 `phone:PhoneApplicationPage`。

##<a id="monitor"></a>將應用程式與即時監視連接

[AZURE.INCLUDE [將應用程式與即時監視連接](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>啟用推播通知與應用程式內傳訊

Mobile Engagement 可讓您透過「推播通知」和「應用程式內傳訊」，於活動進行時與使用者互動和觸達。此模組在 Mobile Engagement 入口網站中稱為觸達 (REACH)。以下各節將設定您的用程式來接收它們。

###啟用您的應用程式接收 MPNS 推播通知

新增「功能」到您的 `WMAppManifest.xml` 檔案：

		ID_CAP_PUSH_NOTIFICATION
		ID_CAP_WEBBROWSERCOMPONENT

   ![][5]

###初始化 REACH SDK

1. 在 `App.xaml.cs` 中，於代理程式初始化後，在 **Application\_Launching** 函數中呼叫 `EngagementReach.Instance.Init();`：

		private void Application_Launching(object sender, LaunchingEventArgs e)
		{
		   EngagementAgent.Instance.Init();
		   EngagementReach.Instance.Init();
		}

2. 在 `App.xaml.cs` 中，於代理程式初始化後，在 **Application\_Activated** 函數中呼叫 `EngagementReach.Instance.OnActivated(e);`：

		private void Application_Activated(object sender, ActivatedEventArgs e)
		{
		   EngagementAgent.Instance.OnActivated(e);
		   EngagementReach.Instance.OnActivated(e);
		}

您全都準備好了。現在我們要驗證您已正確完成這項基本整合。

##<a id="send"></a>傳送通知至應用程式

[AZURE.INCLUDE [建立 Windows 推播宣傳活動](../../includes/mobile-engagement-windows-push-campaign.md)]

您現在應該會在裝置上看到通知，其會顯示為應用程式內通知 (如果應用程式已開啟)，否則會顯示為快顯通知，如下所示：

![][6]

<!-- URLs. -->
[MicrosoftAzure.MobileEngagement]: http://go.microsoft.com/?linkid=9874664
[Mobile Engagement Windows Phone SDK documentation]: ../mobile-engagement-windows-phone-integrate-engagement/

<!-- Images. -->
[1]: ./media/mobile-engagement-windows-phone-get-started/project-properties.png
[2]: ./media/mobile-engagement-windows-phone-get-started/wmappmanifest-capabilities.png
[3]: ./media/mobile-engagement-windows-phone-get-started/add-connection-string.png
[5]: ./media/mobile-engagement-windows-phone-get-started/reach-capabilities.png
[6]: ./media/mobile-engagement-windows-phone-get-started/push-screenshot.png

<!---HONumber=Oct15_HO3-->