<properties 
	pageTitle="開始在 Windows Phone 上使用 Azure Mobile Engagement" 
	description="了解如何在 Windows Phone 上搭配分析與推播通知來使用 Azure Mobile Engagement。"
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="kpiteira" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="C#" 
	ms.topic="article" 
	ms.date="02/11/2015" 
	ms.author="kapiteir" />
	
# 開始使用 Mobile Engagement

<div class="dev-center-tutorial-selector sublanding"><a href="/documentation/articles/mobile-engagement-windows-store-dotnet-get-started/" title="Windows Universal">Windows 市集</a><a href="/documentation/articles/mobile-engagement-windows-phone-get-started/" title="Windows Phone" class="current">Windows Phone</a><a href="/documentation/articles/mobile-engagement-ios-get-started/" title="iOS">iOS</a><a href="/documentation/articles/mobile-engagement-android-get-started/" title="Android">Android</a></div>

本主題說明如何使用 Azure Mobile Engagement 來了解您的應用程式使用量，並傳送推播通知給已分組的 Windows Phone 應用程式使用者。 
本教學課程將示範使用 Mobile Engagement 的簡單廣播案例。在課程中，您將建立一個空白的 Windows Phone 應用程式，以使用 Microsoft 通知服務 (MPNS) 來收集基本資料及接收推播通知。完成時，您便能夠依據使用者的裝置特性，向所有裝置或目標特定使用者廣播推播通知。請務必搭配下一個教學課程來了解如何使用 Mobile Engagement 解決特定使用者與裝置群組。


本教學課程需要下列項目：

+ Visual Studio 2013
+ [Mobile Engagement Windows Phone SDK]

> [AZURE.IMPORTANT] 完成本教學課程是與 Windows Phone 應用程式有關之所有其他 Mobile Engagement 教學課程的先決條件，而且若要完成本教學課程，您必須擁有有效的 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fzh-tw%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Azure 免費試用</a>。

##<a id="setup-azme"></a>為您的應用程式設定 Mobile Engagement

1. 登入 [Azure 管理入口網站]，然後按一下畫面底部的 [**+新增**]。

2. 依序按一下 [應用程式服務]****、[Mobile Engagement]****，以及 [建立]****。

   	![][7]

3. 在顯示的快顯視窗中，輸入以下資訊：
 
   	![][8]

	1. **應用程式名稱**：您可以輸入您應用程式的名稱。可自由使用任何字元。
	2. **平台**：為該應用程式選擇目標平台 (如果您的應用程式是針對多種平台，請針對每種平台重複參與此教學課程)。
	3. **應用程式資源名稱**：這是此應用程式可透過 API 與 URL 存取時的名稱。我們建議您僅使用傳統的 URL 字元：自動產生的名稱應可提供您有力的依據。我們也建議您附加平台名稱，以避免發生名稱衝突，因為名稱必須是唯一的。
	4. **位置**：選擇將裝載此應用程式的資料中心 (更重要的是其「集合」- 請參閱下面的資訊)。
	5. **集合**：如果您已經建立應用程式，請選擇之前建立的集合，或選擇新集合。
	6. **集合名稱**：這代表您的應用程式群組。它也可以確保您所有的應用程式都在將允許彙總計算的群組中。我們強烈建議您使用公司名稱或部門名稱。


	當您完成時，請按一下檢查按鈕以完成您應用程式的建立作業。

4. 現在請在 [應用程式]**** 索引標籤中按一下/選取您剛剛建立的應用程式。
 
   	![][9]

5. 然後按一下 [連線資訊]**** 以顯示要置入您 SDK 整合中的集合設定。
 
   	![][10]

6. 最後，請寫下 [連線字串]****，您將需要此字串來從您的應用程式程式碼中識別此應用程式。

   	![][11]

	>[AZURE.TIP] 您可以使用連線字串右邊的複製圖示來將它複製到剪貼簿以方便使用。

##<a id="connecting-app"></a>將您的應用程式連線至 Mobile Engagement 後端

本教學課程將說明「基本整合」，這是收集資料及傳送推播通知時必要的最低設定。完整的整合文件可以在 [Mobile Engagement Windows Phone SDK 文件中]找到。

我們將會使用 Visual Studio 建立基本應用程式來示範整合。

###建立新的 Windows Phone 專案

如果您已經有應用程式且已經熟悉 Windows Phone 開發，您可以略過此步驟。

1. 啟動 Visual Studio，並在主畫面上選取 [新增專案...]****。

   	![][12]

2. 在快顯視窗中，(1) 選取 [Windows Phone] ``，然後 (2) 選取 [Phone 應用程式] ``。然後 (3) 填入應用程式 [名稱] ``及 (4) [方案名稱] ``，然後按一 [確定]****。

   	![][13]

現在已經使用示範應用程式建立您的專案，我們將會把它與 Mobile Engagement 整合。

###在您的專案中包含 SDK 程式庫

下載及整合 SDK 程式庫

1. 下載 [Mobile Engagement Windows Phone SDK]。
2. 將 .tar.gz 檔案解壓所到您電腦中的某個資料夾。
3. 前往  `PROJECT` 然後 `Manage NuGet Packages...`
4. 在快顯視窗中，按一下 [設定]
5. 然後按一下 `+` 按鈕，以建立新的來源
	![][17]

6. 按一下底部的  `...`  按鈕選取來源，並瀏覽到壓縮的 SDK 下載 (參閱步驟 2) 的 [lib] 資料夾，然後按一下 [選取] ``
	![][18]

7. SDK 會新增為來源。按一下 [確定] ``。
	![][19]


###使用「連線字串」將您的應用程式連線到 Mobile Engagement 後端

1. Engagement SDK 需要一些 Windows Phone SDK 的功能以正常運作。
開啟 WMAppManifest.xml 檔案，並確定 [功能] 面板中有宣告下列功能：
	- ID_CAP_NETWORKING
	- ID_CAP_IDENTITY_DEVICE


	![][20]

2. 回到 Azure 入口網站中您應用程式的 [連線資訊]**** 頁面，並複製 [連線字串]****。

	![][11]

3. 將它貼上到 `Resources\EngagementConfiguration.xml` 檔案的 `<connectionString>` 和 `</connectionString>` 標籤之間：

	![][21]

4. 在 `App.xaml.cs` 檔案：
	1. 新增 `using` 陳述式

			using Microsoft.Azure.Engagement;

	2. 在 `Application_Launching` 方法中初始化 SDK：
			
			private void Application_Launching(object sender, LaunchingEventArgs e)
			{
			  EngagementAgent.Instance.Init();
			}

	3. 插入下列 `Application_Activated`

			private void Application_Activated(object sender, ActivatedEventArgs e)
			{
			   EngagementAgent.Instance.OnActivated(e);
			}

###傳送螢幕到 Mobile Engagement

若要開始傳送資料並確定使用者正在使用，您必須至少傳送一個螢幕 (活動) 到 Mobile Engagement 後端。我們會使用 Mobile Engagement SDK 所提供的 `EngagementPage` 來將我們的 `MainPage` 分成子類別，以達到此目的。

1. 若要這麼做，請以 EngagementPage (如下所示) 取代 `MainPage` 的超級類別 (在 `PhoneApplicationPage` 之前)：

	![][22]

	>[AZURE.NOTE] 如果類別出現以紅色底線標示，別忘了將 `using Microsoft.Azure.Engagement;` 加入 `using` 子句來解析類別。

2. 在您的 `MainPage.xml` 檔案中：
	1. 加入命名空間宣告：

			xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"
	2. 將 xml 標籤名稱內的 `phone:PhoneApplicationPage` 以 `engagement:EngagementPage` 取代

##<a id="monitor"></a>如何檢查您的應用程式已與即時監視連線

本節說明如何使用 Mobile Engagement 的即時監視功能來檢查您的應用程式是否連線至 Mobile Engagement 後端。

1. 瀏覽至您的 Mobile Engagement 入口網站。

	在您的 Azure 入口網站，請確認您正位在用於此專案的應用程式內，然後按一下底部的 [接洽]**** 按鈕。

	![][26]

2. 您將登陸您應用程式的 Engagement 入口網站。從該處按一下 [監視器]****索引標籤，如下所示。
	![][30]

3. 監視器已經準備好為您即時顯示任何將啟動應用程式的裝置。
	![][31]

4. 在 Visual Studio 中，按一下綠色三角形然後選取您的裝置，以模擬器或已連線的裝置啟動您的應用程式。

5. 如果它能運作，您將在監視器中看到一個工作階段！ 
	![][33]

恭喜！****您已經完成本教學課程的第一步，並有一個已經連線至 Mobile Engagement 後端，且正在傳送資料的應用程式。


##<a id="integrate-push"></a>啟用推播通知與應用程式內傳訊

Mobile Engagement 可讓您透過「推播通知」和「應用程式內傳訊」，於活動進行時與使用者互動和 REACH。此模組在 Mobile Engagement 入口網站中稱為 REACH。
以下各節將設定您的用程式來接收它們。

###啟用您的應用程式接收 MPNS 推播通知

新增「功能」到您的 `WMAppManifest.xml` 檔案：

		ID_CAP_PUSH_NOTIFICATION
		ID_CAP_WEBBROWSERCOMPONENT

![][34]

###初始化 REACH SDK

1. 在 `App.xaml.cs` 中，於 `Application_Launching` 函式內的代理程式初始化後面立即呼叫 `EngagementReach.Instance.Init();` ：

		private void Application_Launching(object sender, LaunchingEventArgs e)
		{
		   EngagementAgent.Instance.Init();
		   EngagementReach.Instance.Init();
		}

2. 在 `App.xaml.cs` 中，於 `Application_Activated`函式內的代理程式初始化後面立即呼叫 `EngagementReach.Instance.OnActivated(e);` ：

		private void Application_Activated(object sender, ActivatedEventArgs e)
		{
		   EngagementAgent.Instance.OnActivated(e);
		   EngagementReach.Instance.OnActivated(e);
		}

您都完成了，現在我們將驗證您是否正確完成這項基本的整合。

##<a id="send"></a>如何傳送通知給您的應用程式

我們將建立一個簡單的「推播通知」活動，它將會傳送推播通知給我們的應用程式。

1. 瀏覽至您 Mobile Engagement 入口網站的 [REACH]**** 索引標籤。
2. 按一下 [新增宣告]**** 來建立您的推播活動。
	![][35]

3. 遵循下列步驟來設定您活動的第一個欄位：
	![][36]

	1. 以任何您想要的名稱來命名活動。
	2. 將 [傳遞時間]**** 選取為 [任何時候] ( *Any time*)，這樣無論應用程式是否啟動都能接收通知。
	3. 在通知文字中，輸入 [標題]，這在推播中會以粗體顯示。
	4. 然後輸入您的訊息。

4. 向下捲動，並在 [內容] 區段選取 [只有通知]****。
![][37]

5. 您已經完成設定一個盡可能最基本的活動，現在向下捲動並建立您的活動以儲存它！
![][38]

6. 最後一步，「啟動」您的活動。
![][39]

您因該會在您的裝置上看到通知，恭喜您！****：
![][40]


<!-- URLs. -->
[Mobile Engagement Windows Phone SDK]: http://go.microsoft.com/?linkid=9874664

<!-- Images. -->
[7]: ./media/mobile-engagement-windows-phone-get-started/create-mobile-engagement-app.png
[8]: ./media/mobile-engagement-windows-phone-get-started/create-azme-popup.png
[9]: ./media/mobile-engagement-windows-phone-get-started/select-app.png
[10]: ./media/mobile-engagement-windows-phone-get-started/app-main-page-select-connection-info.png
[11]: ./media/mobile-engagement-windows-phone-get-started/app-connection-info-page.png
[12]: ./media/mobile-engagement-windows-phone-get-started/new-project.png
[13]: ./media/mobile-engagement-windows-phone-get-started/project-properties.png
[17]: ./media/mobile-engagement-windows-phone-get-started/manage-nuget-settings-new-source.png
[18]: ./media/mobile-engagement-windows-phone-get-started/manage-nuget-settings-new-source-add-lib.png
[19]: ./media/mobile-engagement-windows-phone-get-started/manage-nuget-settings-new-source-added.png
[20]: ./media/mobile-engagement-windows-phone-get-started/wmappmanifest-capabilities.png
[21]: ./media/mobile-engagement-windows-phone-get-started/add-connection-string.png
[22]: ./media/mobile-engagement-windows-phone-get-started/subclassing.png
[26]: ./media/mobile-engagement-windows-phone-get-started/engage-button.png
[27]: ./media/mobile-engagement-windows-phone-get-started/engagement-portal.png

[28]: ./media/mobile-engagement-windows-phone-get-started/native-push-settings.png
[29]: ./media/mobile-engagement-windows-phone-get-started/api-key.png
[30]: ./media/mobile-engagement-windows-phone-get-started/clic-monitor-tab.png
[31]: ./media/mobile-engagement-windows-phone-get-started/monitor.png
[32]: ./media/mobile-engagement-windows-phone-get-started/launch.png
[33]: ./media/mobile-engagement-windows-phone-get-started/monitor.png
[34]: ./media/mobile-engagement-windows-phone-get-started/reach-capabilities.png
[35]: ./media/mobile-engagement-windows-phone-get-started/new-announcement.png
[36]: ./media/mobile-engagement-windows-phone-get-started/campaign-first-params.png
[37]: ./media/mobile-engagement-windows-phone-get-started/campaign-content.png
[38]: ./media/mobile-engagement-windows-phone-get-started/campaign-create.png
[39]: ./media/mobile-engagement-windows-phone-get-started/campaign-activate.png
[40]: ./media/mobile-engagement-windows-phone-get-started/push-screenshot.png

<!--HONumber=47-->
