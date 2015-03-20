<properties 
	pageTitle="開始使用 Azure Mobile Engagement" 
	description="了解如何搭配 Azure Mobile Engagement 使用分析與推播通知。" 					services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="kpiteira" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="02/11/2015" 
	ms.author="kapiteir" />
	
# 開始使用 Mobile Engagement

<div class="dev-center-tutorial-selector sublanding"><a href="/documentation/articles/mobile-engagement-windows-store-dotnet-get-started/" title="Windows Store">Windows 市集</a><a href="/documentation/articles/mobile-engagement-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/documentation/articles/mobile-engagement-ios-get-started/" title="iOS">iOS</a><a href="/documentation/articles/mobile-engagement-android-get-started/" title="Android" class="current">Android</a></div>

本主題說明如何使用 Azure Mobile Engagement 來了解您應用程式的使用情形，以及傳送推播通知給 Android 應用程式的分段使用者。 
本教學課程會示範使用 Mobile Engagement 的簡單廣播案例。在此案例中，您先建立空白的 Android 應用程式，使用 Google 雲端通訊 (GCM) 來收集基本資料並接收推播通知。完成後，您將可以廣播推播通知給所有裝置或根據裝置屬性鎖定特定使用者。請務必依照下一個教學課程的步驟進行，以查看如何使用 Mobile Engagement 來處理特定使用者和裝置群組。


本教學課程需要下列項目：

+ Android SDK (假設您將使用 Android Studio)，您可以從[這裡]下載(http://go.microsoft.com/fwlink/?LinkId=389797)
+ [Mobile Engagement Android SDK]

> [AZURE.IMPORTANT] 完成本教學課程是 Android 應用程式適用之所有其他 Mobile Engagement 教學課程的先決條件，若要完成此作業，您必須擁有有效的 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fzh-tw%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Azure 免費試用</a>。

<!--
##<a id="register"></a>Enable Google Cloud Messaging

[WACOM.INCLUDE [Enable GCM](../includes/mobile-services-enable-Google-cloud-messaging.md)]

You will use your GCM API key later when setting up your app for Mobile Engagement.
-->

##<a id="setup-azme"></a>為您的應用程式設定 Mobile Engagement

1. 登入 [Azure 管理入口網站]，然後按一下畫面底部的 [**+新增**]。

2. 依序按一下 [**應用程式服務**]、[**Mobile Engagement**] 和 [**建立**]。

   	![][7]

3. 在隨後出現的快顯視窗中，輸入下列資訊：
 
   	![][8]

	1. **應用程式名稱**：您可以輸入應用程式的名稱。並可隨意使用任何字元。
	2. **平台**：選取該應用程式的目標平台 (如果您的應用程式會以多個平台為目標，請為每個平台重複本教學課程)。
	3. **應用程式資源名稱**：這是可以透過 API 和 URL 存取此應用程式的名稱。建議您只使用傳統 URL 字元：自動產生的名稱應可提供強式基礎。另外也建議您附加上平台名稱，讓此名稱成為唯一名稱，以避免產生名稱衝突
	4. **位置**：選取裝載此應用程式 (以及更重要的：它的集合 - 請見下文) 的資料中心。
	5. **集合**：如果您已經建立應用程式，請選取先前建立的集合，否則請選取 [新集合]。
	6. **集合名稱**：這代表您的應用程式群組。這也能確保您所有的應用程式都位在允許進行彙總計算的群組中。強烈建議您使用您的公司名稱或部門。


	完成時，按一下核取按鈕以完成建立您的應用程式。

4. 現在在 [**應用程式**] 索引標籤中按一下/選取您剛才建立的應用程式。
 
   	![][9]

5. 接著按一下 [**連線資訊**] 以顯示要置入 SDK 整合的連線設定。
 
   	![][10]

6. 最後，請記下 [**連接字串**]，這是您在應用程式程式碼中識別此應用程式所需的字串。

   	![][11]

	>[AZURE.TIP] 為了方便起見，您可以使用連接字串右側的 [複製] 圖示，將它複製到剪貼簿。

##<a id="connecting-app"></a>將您的應用程式連線到 Mobile Engagement 後端

此教學課程提供「基本整合」，這是收集資料和傳送推播通知所需的最少設定。完整的整合文件位於 [Mobile Engagement Android SDK 文件]中。

我們將使用 Android Studio 建立一個基本應用程式來示範整合。

###建立新的 Android 專案

如果您已經有應用程式並且熟悉 Android 開發過程，可以略過此步驟。

1. 啟動 Android Studio，然後在快顯視窗中選取 [**開始新的 Android Studio 專案**]。

   	![][12]

2. 填入應用程式名稱與公司網域。寫下這些資料，因為稍後您會用到，然後按一下 [**下一步**]。

   	![][13]

3. 現在選取目標尺寸和 API 層級，然後按一下 [**下一步**]。 

	>[AZURE.NOTE] Mobile Engagement 至少需要 API 層級 10 (Android 2.3.3)。

   	![][14]

4. 我們現在要新增活動到這個簡單的應用程式中，做為它的主要、唯一畫面。請務必選取 [**空白活動**]，然後按一下 [**下一步**]。

   	![][15]

5. 在精靈的最後一個畫面中，您可以因應此教學課程的用途而保留所有項目，然後按一下 [**完成**]。

   	![][16]

Android Studio 現在將建立要用於整合 Mobile Engagement 的示範應用程式。

###在您的專案中加入 SDK 程式庫

下載並整合 SDK 程式庫

1. [Mobile Engagement Android SDK]
2. 將封存檔案解壓縮至電腦中的資料夾。
3. 找出此 SDK 目前版本的 .jar 程式庫 (本文採用 3.0.0 版) 並將它複製到剪貼簿。

	![][17]

4. 導覽至專案區段 (1) 並將 .jar 貼到 libs 資料夾 (2)。

	![][18]

5. 同步專案以載入程式庫。

	![][19]


###使用連接字串，將您的應用程式連線到 Mobile Engagement 後端

1. 將下列程式碼行複製到活動建立中 (必須只在應用程式中的一個位置完成，通常是主要活動)。

		EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
		engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
		EngagementAgent.getInstance(this).init(engagementConfiguration);

2. 回到 Azure 入口網站中，應用程式的 [**連線資訊**] 頁面並複製 [**連接字串**]。

	![][11]

3. 將此字串貼到 `setConnectionString` 參數中以取代所提供的範例，如下所示 (下方的 AppId 和 Sdkkey 已隱藏)。

		engagementConfiguration.setConnectionString("Endpoint=my-company-name.device.mobileengagement.windows.net;SdkKey=********************;AppId=*********");

4. EngagementConfiguration 和 EngagementAgent 可能會顯示為無法解析 (在程式碼中顯示為紅色)。按一下每個無法解析的類別，然後按 Alt-Enter 鍵進行自動解析。

	![][20]

###新增權限和服務宣告

1. 將這些權限加入至您專案的 Manifest.xml 中，緊接在 `<application>` 標記前：
	
		<uses-permission android:name="android.permission.INTERNET"/>
		<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
		<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
		<uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
		<uses-permission android:name="android.permission.VIBRATE" />
		<uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION"/>


2. 在 < application > 和 </application > 標記之間加入下列內容，宣告代理程式服務：

		<service
 			android:name="com.microsoft.azure.engagement.service.EngagementService"
 			android:exported="false"
 			android:label="<Your application name>Service"
 			android:process=":Engagement"/>

3. 在您剛才貼上的程式碼中，更換標籤中的 "< Your application name>"。例如：

		<service
 			android:name="com.microsoft.azure.engagement.service.EngagementService"
 			android:exported="false"
 			android:label="MySuperAppService"
 			android:process=":Engagement"/>

###傳送畫面至 Mobile Engagement

若要開始傳送資料並確保使用者為作用中，您必須至少傳送一個畫面 (活動) 至 Mobile Engagement 後端。我們使用 SDK 提供的 EngagementActivity 將活動子類別化，來達成此目的。
為了完成此作業，請將 ActionBarActivity 之前的 MainActivity 超級類別更換為 EngagementActivity，如下所示：

![][22]

>[AZURE.NOTE] 如果類別顯示為紅色，請不要忘記按一下該類別並按 Alt-Enter 鍵以解析類別。

##<a id="monitor"></a>如何使用即時監視功能檢查您的應用程式是否已連線

本節說明如何使用 Mobile Engagement 即時監視功能，確認您的應用程式已連線至 Mobile Engagement 後端。

1. 瀏覽至您的 Mobile Engagement 入口網站。

	在 Azure 入口網站，確定您位於我們用於此專案的應用程式中，然後按一下底部的 [**加入**] 按鈕。

	![][26]

2. 您將進入 Engagement 入口網站的應用程式設定頁面。在這裡按一下 [**監視器**] 索引標籤，如下所示。
	![][30]

3. 監視器就會即時顯示可以啟動您應用程式的任何裝置。
	![][31]

4. 回到 Android Studio，在監視器或連線的裝置中 (按一下綠色三角形，然後選取您的裝置) 啟動您的應用程式。
	![][32]

5. 如果可行，您現在應該會在監視器中看到一個工作階段！ 
	![][33]

**恭喜！**您已成功完成本教學課程的第一個步驟，也就是使用連線到 Mobile Engagement 後端的應用程式開始傳送資料。


##<a id="integrate-push"></a>啟用推播通知和應用程式內傳訊

Mobile Engagement 可讓您在促銷活動環境中，使用推播通知和應用程式內傳訊來與使用者互動。此模組在 Mobile Engagement 中稱為「到達」(REACH)。
下列各節將設定您的應用程式來接收通知。

### 啟用應用程式內傳訊

1. 將下列應用程式內傳訊資源複製到 Manifest.xml 的 < application > 和 </application > 標記之間。

		<activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementTextAnnouncementActivity" android:theme="@android:style/Theme.Light">
  			<intent-filter>
    			<action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
    			<category android:name="android.intent.category.DEFAULT" />
    			<data android:mimeType="text/plain" />
  			</intent-filter>
		</activity>
		<activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity" android:theme="@android:style/Theme.Light">
			<intent-filter>
				<action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
				<category android:name="android.intent.category.DEFAULT" />
				<data android:mimeType="text/html" />
			</intent-filter>
		</activity>
		<activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementPollActivity" android:theme="@android:style/Theme.Light">
			<intent-filter>
				<action android:name="com.microsoft.azure.engagement.reach.intent.action.POLL"/>
				<category android:name="android.intent.category.DEFAULT" />
			</intent-filter>
		</activity>
		<receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver" android:exported="false">
			<intent-filter>
				<action android:name="android.intent.action.BOOT_COMPLETED"/>
				<action android:name="com.microsoft.azure.engagement.intent.action.AGENT_CREATED"/>
				<action android:name="com.microsoft.azure.engagement.intent.action.MESSAGE"/>
				<action android:name="com.microsoft.azure.engagement.reach.intent.action.ACTION_NOTIFICATION"/>
				<action android:name="com.microsoft.azure.engagement.reach.intent.action.EXIT_NOTIFICATION"/>
				<action android:name="android.intent.action.DOWNLOAD_COMPLETE"/>
				<action android:name="com.microsoft.azure.engagement.reach.intent.action.DOWNLOAD_TIMEOUT"/>
			</intent-filter>
		</receiver>

2. 透過下列步驟，將資源複製到您的專案：
	1. 瀏覽回到您的 SDK 下載內容並開啟  'res' 資料夾。
	2. 選取這 2 個資料夾並將其複製到剪貼簿。

		![][23]

	4. 返回 Android Studio，選取專案的  'res' 部分並貼上，將資源新增至您的專案。

		![][24]

###在通知中指定預設圖示
下列程式碼會定義顯示在通知上的預設圖示。此處我們使用 Android Studio 所建立專案提供的圖示。此 xml 程式碼片段要貼到 Manifest.xml 的 < application > 和 </application > 標記之間

		<meta-data android:name="engagement:reach:notification:icon" android:value="ic_launcher" />

###啟用應用程式接收 GCM 推播通知

1. 將下列內容複製並貼入 Manifest.xml 的 < application > 和 </application > 標記之間，以輸入 gcm:sender 中繼資料。下面的隱藏值 (星號) 是取自 Google Play 主控台的  `project number`。

		<meta-data android:name="engagement:gcm:sender" android:value="************\n" />

2. 將下列程式碼貼到 Manifest.xml 的 < application > 和 </application > 標記之間。請注意在 `<category android:name="com.mycompany.mysuperapp" />` 中，我們使用專案的封裝名稱。在您實際執行的專案中，這個名稱應該不同。

		<receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMEnabler"
		android:exported="false">
			<intent-filter>
				<action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT" />
			</intent-filter>
		</receiver>
		
		<receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMReceiver" android:permission="com.google.android.c2dm.permission.SEND">
			<intent-filter>
				<action android:name="com.google.android.c2dm.intent.REGISTRATION" />
				<action android:name="com.google.android.c2dm.intent.RECEIVE" />
				<category android:name="com.mycompany.mysuperapp" />
			</intent-filter>
		</receiver>

3. 將下列反白顯示的最後一組權限集新增至 < application> 標記之前。再次重申，我們使用的是此專案封裝名稱，您的實際執行應用程式中必須加以替換。

		<uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
		<uses-permission android:name="com.mycompany.mysuperapp.permission.C2D_MESSAGE" />
		<permission android:name="com.mycompany.mysuperapp.permission.C2D_MESSAGE" android:protectionLevel="signature" />

###授與 GCM API 金鑰的存取權給 Mobile Engagement

若要讓 Mobile Engagement 以您的名義傳送推播通知，您必須授與 API 金鑰的存取權。完成此項作業的方法為在 Mobile Engagement 入口網站中設定並輸入您的金鑰。

1. 瀏覽至您的 Mobile Engagement 入口網站

	在 Azure 入口網站，確定您位於我們用於此專案的應用程式中，然後按一下底部的 [**加入**] 按鈕：

	![][26]

2. 您現在會在 Engagement 入口網站的設定頁面中。在這裡按一下 [**原生推播**] 區段以輸入 GCM 金鑰：
	![][27]

3. 按一下 [GCM 設定] 區段中 [**API 金鑰**] 前面的編輯圖示，如下所示：
	![][28]

4. 在快顯視窗中，貼上您從 [[啟用 Google 雲端訊息]] 區段中取得的 GCM 伺服器金鑰，(#register) 然後按一下 [**確定**]。

	![][29]

您已完成所有設定，現在我們要驗證您是否已正確完成此項基本整合。

> [AZURE.IMPORTANT] 請務必建置、使用此新程式碼啟動、結束應用程式、等待大約 1 分鐘，然後再執行下列動作

##<a id="send"></a>如何傳送通知至應用程式

我們現在要建立一個簡單的推播通知促銷活動，用來傳送推播通知給我們的應用程式。

1. 瀏覽至 Mobile Engagement 入口網站中的 [**REACH**] 索引標籤。
	![][34]

2. 按一下 [**新宣告**] 建立推播促銷活動。
	![][35]

3. 依照下列步驟，設定促銷活動的第一個欄位：
	![][36]

	1. 隨意命名您的促銷活動。
	2. 在 [**傳遞類型**] 選取  *System notification / Simple*：這是簡單 Android 推播通知類型，具備一個標題和一小行文字。
	3. 在 [**傳遞時間**] 選取  *Any time*，可允許應用程式無論是否啟動都可接收通知。
	4. 在通知文字中輸入標題，標題文字在推播中將以粗體顯示。
	5. 接著輸入您的訊息。

4. 向下捲動，在內容區段中選取 [**僅通知**]。
	![][37]

5. 您已完成最基本的促銷活動設定，現在可再次向下捲動、建立您的促銷活動，然後儲存起來！
![][38]

6. 最後一個步驟，啟動您的促銷活動。
![][39]


<!-- URLs. -->
[Mobile Engagement Android SDK]: http://go.microsoft.com/?linkid=9863935
[Mobile Engagement Android SDK 文件]: http://go.microsoft.com/?linkid=9874682
[Azure 管理入口網站]: https://manage.windowsazure.com

<!-- Images. -->
[7]: ./media/mobile-engagement-android-get-started/create-mobile-engagement-app.png
[8]: ./media/mobile-engagement-android-get-started/create-azme-popup.png
[9]: ./media/mobile-engagement-android-get-started/select-app.png
[10]: ./media/mobile-engagement-android-get-started/app-main-page-select-connection-info.png
[11]: ./media/mobile-engagement-android-get-started/app-connection-info-page.png
[12]: ./media/mobile-engagement-android-get-started/android-studio-new-project.png
[13]: ./media/mobile-engagement-android-get-started/android-studio-project-props.png
[14]: ./media/mobile-engagement-android-get-started/android-studio-project-props2.png
[15]: ./media/mobile-engagement-android-get-started/android-studio-add-activity.png
[16]: ./media/mobile-engagement-android-get-started/android-studio-activity-name.png
[17]: ./media/mobile-engagement-android-get-started/sdk-content.png
[18]: ./media/mobile-engagement-android-get-started/paste-jar.png
[19]: ./media/mobile-engagement-android-get-started/sync-project.png
[20]: ./media/mobile-engagement-android-get-started/resolve-classes.png
[22]: ./media/mobile-engagement-android-get-started/subclass-activity.png
[23]: ./media/mobile-engagement-android-get-started/copy-resources.png
[24]: ./media/mobile-engagement-android-get-started/paste-resources.png
[26]: ./media/mobile-engagement-android-get-started/engage-button.png
[27]: ./media/mobile-engagement-android-get-started/engagement-portal.png
[28]: ./media/mobile-engagement-android-get-started/native-push-settings.png
[29]: ./media/mobile-engagement-android-get-started/api-key.png
[30]: ./media/mobile-engagement-android-get-started/clic-monitor-tab.png
[31]: ./media/mobile-engagement-android-get-started/monitor.png
[32]: ./media/mobile-engagement-android-get-started/launch.png
[33]: ./media/mobile-engagement-android-get-started/monitor-trafic.png
[34]: ./media/mobile-engagement-android-get-started/reach-tab.png
[35]: ./media/mobile-engagement-android-get-started/new-announcement.png
[36]: ./media/mobile-engagement-android-get-started/campaign-first-params.png
[37]: ./media/mobile-engagement-android-get-started/campaign-content.png
[38]: ./media/mobile-engagement-android-get-started/campaign-create.png
[39]: ./media/mobile-engagement-android-get-started/campaign-activate.png

<!--HONumber=47-->
