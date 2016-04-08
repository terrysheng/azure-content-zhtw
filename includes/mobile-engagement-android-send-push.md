
###更新資訊清單檔案以啟用通知

將下列應用程式內傳訊資源複製到您 Manifest.xml 的 `<application>` 和 `</application>` 標記之間。

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
		<activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementLoadingActivity" android:theme="@android:style/Theme.Dialog">
			<intent-filter>
				<action android:name="com.microsoft.azure.engagement.reach.intent.action.LOADING"/>
				<category android:name="android.intent.category.DEFAULT"/>
			</intent-filter>
		</activity>
		<receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver" android:exported="false">
			<intent-filter>
				<action android:name="android.intent.action.BOOT_COMPLETED"/>
				<action android:name="com.microsoft.azure.engagement.intent.action.AGENT_CREATED"/>
				<action android:name="com.microsoft.azure.engagement.intent.action.MESSAGE"/>
				<action android:name="com.microsoft.azure.engagement.reach.intent.action.ACTION_NOTIFICATION"/>
				<action android:name="com.microsoft.azure.engagement.reach.intent.action.EXIT_NOTIFICATION"/>
				<action android:name="com.microsoft.azure.engagement.reach.intent.action.DOWNLOAD_TIMEOUT"/>
			</intent-filter>
		</receiver>
		<receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachDownloadReceiver">
			<intent-filter>
				<action android:name="android.intent.action.DOWNLOAD_COMPLETE"/>
			</intent-filter>
		</receiver>

###指定通知的圖示

請將下列 XML 程式碼片段貼到您 Manifest.xml 的 `<application>` 和 `</application>` 標記之間。

		<meta-data android:name="engagement:reach:notification:icon" android:value="engagement_close"/>

這會定義顯示於系統和 App 內通知中的圖示。這是 App 內通知的選用功能，但對系統通知是必要功能。Android 將會拒絕具有無效圖示的系統通知。

請確定您使用的圖示存在於其中一個**可繪製**資料夾 (例如 ``engagement_close.png``) 中。不支援 **Mipmap** 資料夾。

>[AZURE.NOTE] 您不應該使用**啟動程式**圖示。它的解析度不同，而且通常是在 mipmap 資料夾中，我們不支援該資料夾。

對於真正的應用程式，您可以根據 [Android 設計指導方針](http://developer.android.com/design/patterns/notifications.html)使用適合通知功能的圖示。

>[AZURE.TIP] 若要確保您使用了正確的圖示解析度，您可以查看[這些範例](https://www.google.com/design/icons)。請向下捲動至 [Notification] \(通知) 區段、按一下某個圖示，然後按一下 `PNGS` 來下載可繪製圖示集。您可看到對於每個版本的圖示要使用哪種解析度的可繪製資料夾。

###啟用應用程式接收 GCM 推播通知

1. 請先用得自 Google Play 控制台的 `project number` 取代下列內容的星號之後，再把下列內容貼到 Manifest.xml 的 `<application>` 和 `</application>` 標記之間。請務必在專案編號後面加上 \\n。

		<meta-data android:name="engagement:gcm:sender" android:value="************\n" />

2. 將下列程式碼貼到 Manifest.xml 的 `<application>` 和 `</application>` 標記之間。請別忘了取代封裝名稱 <Your package name>。

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
				<category android:name="<Your package name>" />
			</intent-filter>
		</receiver>

3. 將反白顯示的最後一組權限集新增到 `<application>` 標記之前。請使用應用程式的實際封裝名稱來取代 `<Your package name>`。

		<uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
		<uses-permission android:name="<Your package name>.permission.C2D_MESSAGE" />
		<permission android:name="<Your package name>.permission.C2D_MESSAGE" android:protectionLevel="signature" />

<!----HONumber=AcomDC_0330_2016-->