<properties
	pageTitle="Azure Mobile Engagement Android SDK 整合"
	description="Android SDK for Azure Mobile Engagement 的最新更新和程序"
	services="mobile-engagement"
	documentationCenter="mobile"
	authors="piyushjo"
	manager="dwrede"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="Java"
	ms.topic="article"
	ms.date="03/10/2016"
	ms.author="piyushjo" />

#如何整合 GCM 與 Mobile Engagement

> [AZURE.IMPORTANT] 您必須遵循＜如何在 Android 上整合＞文件中所述的整合程序，才能接著遵循本指南。
>
> 本文件只有在您已經整合觸達模組並打算推送 Google Play 裝置時才適用。若要在應用程式中整合 Reach 促銷活動，請先閱讀「如何在 Android 上整合 Engagement Reach」。

##簡介

整合 GCM 可讓您推播應用程式。

推送到 GCM 裝載的 SDK 一律包含資料物件中的 `azme` 金鑰。因此，如果您在應用程式中因為其他目的使用 GCM，可以根據該金鑰篩選推送。

> [AZURE.IMPORTANT] 只有執行 Android 2.2 或更高版本，且已安裝 Google Play 並已啟用 Google 背景連線的裝置，才能使用 GCM 推播；不過，您仍可將這段程式碼安全地整合不支援 GCM 的裝置 (只使用對應方式)。

##利用 API 金鑰建立 Google 雲端通訊專案

[AZURE.INCLUDE [mobile-engagement-enable-Google-cloud-messaging](../../includes/mobile-engagement-enable-google-cloud-messaging.md)]

> [AZURE.IMPORTANT] 「專案編號」不應與「專案識別碼」混淆。

##SDK 整合

### 管理裝置註冊

每個裝置都必須傳送註冊命令給 Google 伺服器，否則無法連線該裝置。

也可以從 GCM 通知取消註冊裝置 (解除安裝應用程式時會自動取消裝置的註冊)。

如果您未使用 [Google Play SDK]，或者尚未自行傳送註冊對應方式，您可以讓 Engagement 自動註冊您的裝置。

若要啟用此功能，請將下列內容加入 `AndroidManifest.xml` 檔案的 `<application/>` 標記中：

			<!-- If only 1 sender, don't forget the \n, otherwise it will be parsed as a negative number... -->
			<meta-data android:name="engagement:gcm:sender" android:value="<Your Google Project Number>\n" />

### 將註冊識別碼傳遞給 Engagement 推播服務，並接收通知

若要將裝置的註冊識別碼傳遞給 Engagement 推播服務並接收其通知，請將下列內容加入 `AndroidManifest.xml` 檔案的 `<application/>` 標記中 (即使您自行管理裝置註冊，也請這麼做)：

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
			    <category android:name="<your_package_name>" />
			  </intent-filter>
			</receiver>

確定您在 `AndroidManifest.xml` 中具有下列權限 (在 `</application>` 標記之後)。

			<uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
			<uses-permission android:name="<your_package_name>.permission.C2D_MESSAGE" />
			<permission android:name="<your_package_name>.permission.C2D_MESSAGE" android:protectionLevel="signature" />

##授與 Mobile Engagement 的存取權給 GCM API 金鑰

遵循[本指南](mobile-engagement-android-get-started.md#grant-mobile-engagement-access-to-your-gcm-api-key)來授與 Mobile Engagement 的存取權給 GCM API 金鑰。

[Google Play SDK]: https://developers.google.com/cloud-messaging/android/start

<!---HONumber=AcomDC_0316_2016-->