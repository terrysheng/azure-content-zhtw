<properties
	pageTitle="開始使用 Azure Mobile Engagement"
	description="了解如何使用 Android 應用程式的 Azure Mobile Engagement 與分析和推播通知。"
	services="mobile-engagement"
	documentationCenter="android"
	authors="piyushjo"
	manager=""
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="Java"
	ms.topic="hero-article"
	ms.date="03/25/2016"
	ms.author="piyushjo" />

# 開始使用適用於 Android 應用程式的 Azure Mobile Engagement

[AZURE.INCLUDE [Hero 教學課程切換器](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

本主題說明如何使用 Azure Mobile Engagement 來了解您應用程式的使用情形，以及如何傳送推播通知給 Android 應用程式的分段使用者。本教學課程將示範使用 Mobile Engagement 的簡單廣播案例。在此案例中，您先建立空白的 Android 應用程式，使用 Google 雲端通訊 (GCM) 來收集基本資料並接收推播通知。

本教學課程需要下列各項：

+ Android SDK (假設您將使用 Android Studio)，您可以從[這裡](http://go.microsoft.com/fwlink/?LinkId=389797)下載
+ [Mobile Engagement Android SDK]

> [AZURE.NOTE] 若要完成此教學課程，您必須具備有效的 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 [Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fzh-TW%2Fdocumentation%2Farticles%2Fmobile-engagement-android-get-started)。

##<a id="setup-azme"></a>為您的 Android 應用程式設定 Mobile Engagement

[AZURE.INCLUDE [在入口網站中建立 Mobile Engagement App](../../includes/mobile-engagement-create-app-in-portal.md)]

##<a id="connecting-app"></a>將您的應用程式連線至 Mobile Engagement 後端

本教學課程將說明「基本整合」，這是收集資料及傳送推播通知時必要的最低設定。完整的整合文件位於 [Mobile Engagement Android SDK 整合](../mobile-engagement-android-sdk-overview/)中。

我們將使用 Android Studio 建立一個基本應用程式來示範整合。

###建立新的 Android 專案

1. 啟動 **Android Studio**，然後在快顯視窗中選取 [開始新的 Android Studio 專案]。

    ![][1]

2. 提供 App 名稱與公司網域。記下您填入的內容，因為您將會在稍後用到。按 [下一步]。

    ![][2]

3. 選取目標尺寸和 API 層級，然後按 [下一步]。

	>[AZURE.NOTE] Mobile Engagement 至少需要 API 層級 10 (Android 2.3.3)。

    ![][3]

4. 在此處選取 [空白活動]，這是僅適用於此 App 的畫面，然後按 [下一步]。

    ![][4]

5. 最後，依原樣保留預設值，然後按一下 [完成]。

    ![][5]

Android Studio 現在將建立要用於整合 Mobile Engagement 的示範應用程式。

###在您的專案中包含 SDK 程式庫

1. 下載 [Mobile Engagement Android SDK]。
2. 將封存檔案解壓縮至電腦中的資料夾。
3. 找出此 SDK 目前版本的 .jar 程式庫，並將它複製到剪貼簿。

	  ![][6]

4. 瀏覽到 [專案] 區段 (1)，然後將 .jar 貼到 libs 資料夾 (2)。

	  ![][7]

5. 同步專案以載入程式庫。

	  ![][8]

###使用「連線字串」將您的應用程式連線到 Mobile Engagement 後端

1. 將下列程式碼行複製到活動建立中 (必須只在應用程式中的一個位置完成，通常是主要活動)。針對此範例 App，開啟 src -> main -> java 資料夾下方的 MainActivity，然後新增下列內容：

		EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
		engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
		EngagementAgent.getInstance(this).init(engagementConfiguration);

2. 按 Alt + Enter 鍵或新增下列匯入陳述式來解析參考：

		import com.microsoft.azure.engagement.EngagementAgent;
		import com.microsoft.azure.engagement.EngagementConfiguration;

3. 回到 Azure 傳統入口網站中您應用程式的 [連線資訊] 頁面，並複製 [連接字串]。

	  ![][9]

4. 將此字串貼到 `setConnectionString` 參數以取代所提供的範例，如下所示：

		engagementConfiguration.setConnectionString("Endpoint=my-company-name.device.mobileengagement.windows.net;SdkKey=********************;AppId=*********");

###新增權限和服務宣告

1. 將這些權限加入至您專案的 Manifest.xml 中，緊接在 `<application>` 標記之前或之後：

		<uses-permission android:name="android.permission.INTERNET"/>
		<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
		<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
		<uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
		<uses-permission android:name="android.permission.VIBRATE" />
		<uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION"/>

2. 在 `<application>` 和 `</application>` 標記之間加入下列內容，宣告代理程式服務：

		<service
 			android:name="com.microsoft.azure.engagement.service.EngagementService"
 			android:exported="false"
 			android:label="<Your application name>"
 			android:process=":Engagement"/>

3. 在您剛貼上的程式碼中，更換標籤中的 `"<Your application name>"`。這會顯示於 [設定] 功能表，使用者可在其中看到裝置上正在執行的服務。例如，您可以在該標籤中加入「服務」這個字。

###傳送畫面到 Mobile Engagement

若要開始傳送資料並確定使用者正在使用，您必須至少傳送一個畫面 (活動) 到 Mobile Engagement 後端。

請前往 **MainActivity.java**，然後新增下列項目，以便將 **MainActivity** 的基底類別取代為 **EngagementActivity**：

	public class MainActivity extends EngagementActivity {

您應該針對此簡單範例案例註解掉 (排除) 下列一行：

    // setSupportActionBar(toolbar);

如果您想保留此行，則應查看[進階 Android 整合](mobile-engagement-android-integrate-engagement.md/#basic-reporting)中的「基本報告」案例

##<a id="monitor"></a>結合應用程式與即時監視功能

[AZURE.INCLUDE [將 App 與即時監視連接](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>啟用推播通知與應用程式內傳訊的功能

Mobile Engagement 可讓您透過「推播通知」和「應用程式內傳訊」，於活動進行時與使用者互動和「觸達」。此模組在 Mobile Engagement 入口網站中稱為觸達 (REACH)。以下各節將設定您的應用程式來接收它們。

### 複製您專案中的 SDK 資源
	
1. 瀏覽回到您的 SDK 下載內容，並且複製 **res** 資料夾。

	![][10]

2. 返回 Android Studio，選取專案檔案的 **main** 目錄，然後貼上以將資源加入您的專案。

	![][11]

[AZURE.INCLUDE [啟用 Google Cloud Messaging](../../includes/mobile-engagement-enable-google-cloud-messaging.md)]

[AZURE.INCLUDE [啟用應用程式內傳訊](../../includes/mobile-engagement-android-send-push.md)]

[AZURE.INCLUDE [從入口網站傳送通知](../../includes/mobile-engagement-android-send-push-from-portal.md)]

<!-- URLs. -->
[Mobile Engagement Android SDK]: https://aka.ms/vq9mfn

<!-- Images. -->
[1]: ./media/mobile-engagement-android-get-started/android-studio-new-project.png
[2]: ./media/mobile-engagement-android-get-started/android-studio-project-props.png
[3]: ./media/mobile-engagement-android-get-started/android-studio-project-props2.png
[4]: ./media/mobile-engagement-android-get-started/android-studio-add-activity.png
[5]: ./media/mobile-engagement-android-get-started/android-studio-activity-name.png
[6]: ./media/mobile-engagement-android-get-started/sdk-content.png
[7]: ./media/mobile-engagement-android-get-started/paste-jar.png
[8]: ./media/mobile-engagement-android-get-started/sync-project.png
[9]: ./media/mobile-engagement-android-get-started/app-connection-info-page.png
[10]: ./media/mobile-engagement-android-get-started/copy-resources.png
[11]: ./media/mobile-engagement-android-get-started/paste-resources.png

<!---HONumber=AcomDC_0330_2016-->