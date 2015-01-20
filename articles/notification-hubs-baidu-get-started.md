<properties urlDisplayName="Get Started" pageTitle="開始使用 Azure 通知中樞" metaKeywords="" description="了解如何使用 Azure 通知中樞來推播通知。" metaCanonical="" services="notification-hubs" documentationCenter="Mobile" title="Get started with Notification Hubs" authors="piyushjo" solutions="" manager="dwrede" editor="" />

<tags ms.service="" ms.devlang="" ms.topic="article" ms.tgt_pltfrm="" ms.workload="" ms.date="10/03/2014" ms.author="piyushjo" />

# 開始使用通知中心

<div class="dev-center-tutorial-selector sublanding"><a href="/zh-tw/documentation/articles/notification-hubs-windows-store-dotnet-get-started/" title="Windows Store C#">Windows 市集 C#</a><a href="/zh-tw/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/zh-tw/documentation/articles/notification-hubs-ios-get-started/" title="iOS">iOS</a><a href="/zh-tw/documentation/articles/notification-hubs-android-get-started/" title="Android">Android</a><a href="/zh-tw/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle">Kindle</a><a href="/zh-tw/documentation/articles/notification-hubs-baidu-get-started/" title="Baidu" class="current">百度</a><a href="/zh-tw/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS">Xamarin.iOS</a><a href="/zh-tw/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android">Xamarin.Android</a></div>

百度雲端推播是中文的雲端服務，可用於將推播通知傳送至行動服務。此服務在中國特別有用，在該地要將推播通知提供到 Android 是件複雜的事，因為有不同的應用程式市集和推播服務，還需考量一般不會連線至 GCM (Google Cloud Messaging) 之 Android 裝置的可用性。 

本教學課程需要下列項目：

+ Android SDK (假設您將使用 Eclipse)，您可以從 <a href="http://go.microsoft.com/fwlink/?LinkId=389797">此處下載</a>
+ [行動服務 Android SDK]
+ [百度推播 Android SDK]

>[WACOM.NOTE] 若要完成此教學課程，您必須具備有效的 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 <a href="http://www.windowsazure.com/zh-tw/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fzh-tw%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Azure 免費試用</a>。

本教學課程將逐步引導您完成下列啟用推播通知的基本步驟：

* [建立百度帳戶](#createBaiduAccount)
* [註冊為百度開發者](#registerBaiduDeveloper)
* [建立百度雲端推播專案](#createBaiduPushProject)
* [設定您的通知中心](#configure-hub)
* [將您的應用程式連接到通知中心](#connecting-app)
* [將通知傳送至您的應用程式](#send)

##<a id="createBaiduAccount"></a>建立百度帳戶

若要使用百度，您必須建立帳戶。如果您已經有帳戶，請使用您的百度帳戶登入[百度入口網站]並跳到下一步；否則請參閱下方關於如何建立新的百度帳戶的指示。  

1. 移至[百度入口網站]，然後按一下 [登入] 連結。按一下 [立即註冊] 以開始新的帳戶註冊程序。 

   	![][1]

2. 輸入必要的詳細資料 - 電話/電子郵件地址、密碼和驗證程式碼，然後按一下 [註冊]。

   	![][2]

3. 您輸入的電子郵件地址會收到一封電子郵件，其中包含啟用您的百度帳戶的連結。 

   	![][3]

4. 請登入您的電子郵件帳戶，開啟百度啟用郵件，然後按一下啟用連結來啟用百度帳戶。 

   	![][4]

在您啟用百度帳戶後，使用您的帳戶登入[百度入口網站]。 

##<a id="registerBaiduDeveloper"></a>註冊為百度開發者

1. 登入[百度入口網站]後，按一下 [更多 >>]****。

  	![][5]

2. 向下捲動 [站長與開發者服務]**** 區段，然後按一下 [百度開放雲平台]****。 

  	![][6]

3. 在下一個頁面上，按一下右上角的 [開發者服務]****。 

  	![][7]

4. 在下一個頁面上，按一下右上角功能表中的 [註冊開發者]****。 

  	![][8]

5. 輸入您的名稱、描述和用於接收驗證文字訊息的行動電話號碼，然後按一下 [送驗證碼]****。請注意，如果是國際電話號碼，必須以括號括住國碼 ，例如，若是美國的號碼將會是 **(1)1234567890**。

  	![][9]

6. 您接著應該會收到含有驗證數字的文字訊息，如下列範例所示：

  	![][10] 

7. 在 [驗證碼]**** 中輸入訊息中的驗證數字。 

8. 最後，接受百度協議，然後按一下 [提交]**** 以完成開發者註冊。成功完成註冊，您會看到下列頁面：

  	![][11] 

##<a id="createBaiduPushProject"></a>建立百度雲端推播專案

當您建立百度雲端推播專案時，會收到應用程式識別碼、API 金鑰和祕密金鑰。

1. 登入 [百度入口網站] 後，按一下 [更多 >>]****。

  	![][5]

2. 向下捲動 [站長與開發者服務]**** 區段，然後按一下 [百度開放雲平台]****。 

  	![][6]

3. 在下一個頁面上，按一下右上角的 [開發者服務]****。 

  	![][7]

4. 在下一個頁面上，按一下 [雲服務]**** 區段中的 [雲推送]****。 

  	![][12]

5. 成為註冊開發者後，您會看到上方功能表的 [管理控制台]****。按一下 [開發者服務管理]****。 

  	![][13]

6. 在下一個頁面上，按一下 [創建工程]****。

  	![][14]

7. 輸入應用程式名稱，然後按一下 [創建]****。

  	![][15]

8. 成功建立後，您會看到一個含有 [應用程式識別碼]****、[API 金鑰] ****和 [秘密金鑰]**** 的頁面。記下稍後將使用的 [API 金鑰]**** 和 [秘密金鑰]****。 

  	![][16]

9. 按一下左窗格中的 [雲推送]**** 以設定推播通知的專案。 

  	![][31]

10. 在下一個頁面上，按一下 [推送設置]**** 按鈕。

	![][32]  

11. On the configuration page, add the package name that you will be using in your Android project in the **应用包名 (Application package)** field and click **保存设置 (Save)**  

	![][33]

您會看到 [保存成功！]**** 訊息。

##<a id="configure-hub"></a>設定您的通知中心

1. 登入 [Azure 管理入口網站]，然後按一下畫面底部的 [+新增]****。

2. 依序按一下 [應用程式服務 (App Services)]****、[服務匯流排 (Service Bus)]****、[通知中樞 (Notification Hub)]****、[快速建立 (Quick Create)]****。
 
3. 提供 [通知中樞 (Notification Hub)]**** 的名稱，選取 [地區 (Region)]**** 和將建立此通知匯流排的 [命名空間 (Namespace)]****，然後按一下 [建立新的通知中樞 (Create a New Notification Hub)]****。  

  	![][17]

4. 按一下您要在其中建立通知中樞的命名空間，然後按一下上方的 [通知中樞 (Notification Hubs)]****。 

  	![][18]

5. 選取您建立的「通知中樞」，然後按一下上方功能表的 [設定 (Configure)]****。

  	![][19]

6. 向下捲動至 [百度通知設定 (baidu notification settings)]**** 區段，輸入先前從百度控制台為您的百度雲端推播專案取得的 [API 金鑰 (API Key)]**** 和 [秘密金鑰 (Secret Key)]****。輸入這些值後，按一下 [儲存 (Save)]****。 

  	![][20]

7. 按一下 [通知中樞 (Notification Hubs)] 上方的 [儀表板 (Dashboard)]**** 索引標籤，然後按一下 [檢視連接字串 (檢視連接字串)]****。

  	![][21]

8. 記下 [存取連線資訊 (Access connection information)] 視窗中的 [DefaultListenSharedAccessSignature]**** 和 [DefaultFullSharedAccessSignature]****。 

    ![][22]

##<a id="connecting-app"></a>將您的應用程式連接到通知中心

1. 在 Eclipse ADT 中建立新的 Android 專案 ([檔案] -> [新增] -> [Android 應用程式])。

    ![][23]

2. 輸入 [應用程式名稱 (Application Name)]****，確定 [最低 SDK (Minimum Required SDK)]**** 版本設為 **API 16：Android 4.1**。

    ![][24]

3. 按一下 [下一步 (Next)]**** 繼續下列精靈，直到看見 [建立活動 (Create Activity)]**** 視窗。確定 [空白活動 (Blank Activity)]**** 已選取，最後選取 [完成]**** 以建立新的 Android 應用程式。 

    ![][25]

4. 確定 [專案建置目標 (Project Build Target)]**** 已正確設定。

    ![][26]

5. 下載並解壓縮[行動服務 Android SDK]，開啟 **notificationhubs** 資料夾，將 **notification-hubs-x.y.jar** 檔案複製到 Eclipse 專案的 *libs* 資料夾，然後重新整理 *libs* 資料夾。

6. 下載並解壓縮[百度推播 Android SDK]，開啟 **libs** 資料夾，在您的 Android 應用程式的 **libs** 資料夾中複製 *pushservice x.y.z* jar 檔案和 *armeabi* 及 *mips* 資料夾。 

7. 開啟您的 Android 專案的 **AndroidManifest.xml**，並新增百度 SDK 所需的權限。

	    <uses-permission android:name="android.permission.INTERNET" />
	    <uses-permission android:name="android.permission.READ_PHONE_STATE" />
	    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
	    <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
	    <uses-permission android:name="android.permission.WRITE_SETTINGS" />
	    <uses-permission android:name="android.permission.VIBRATE" />
	    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
	    <uses-permission android:name="android.permission.DISABLE_KEYGUARD" />
	    <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
	    <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
	    <uses-permission android:name="android.permission.ACCESS_DOWNLOAD_MANAGER" />
	    <uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION" />

8. 在 **AndroidManifest.xml** 中，將 *android:name* 屬性新增至 *application* 元素以取代 *yourprojectname*，例如 **com.example.BaiduTest**。確定此專案名稱符合您在百度控制台中設定的名稱。 

		<application android:name="yourprojectname.DemoApplication"

9. 在應用程式元素內，於 .MainActivity activity 元素之後新增下列組態以取代 *yourprojectname*，例如 **com.example.BaiduTest**：

		<receiver android:name="yourprojectname.MyPushMessageReceiver">
		    <intent-filter>
		        <action android:name="com.baidu.android.pushservice.action.MESSAGE" />
		        <action android:name="com.baidu.android.pushservice.action.RECEIVE" />
		        <action android:name="com.baidu.android.pushservice.action.notification.CLICK" />
		    </intent-filter>
		</receiver>
		
		<receiver android:name="com.baidu.android.pushservice.PushServiceReceiver"
		    android:process=":bdservice_v1">
		    <intent-filter>
		        <action android:name="android.intent.action.BOOT_COMPLETED" />
		        <action android:name="android.net.conn.CONNECTIVITY_CHANGE" />
				<action android:name="com.baidu.android.pushservice.action.notification.SHOW" />
		    </intent-filter>
		</receiver>
        
        <receiver android:name="com.baidu.android.pushservice.RegistrationReceiver"
            android:process=":bdservice_v1">
            <intent-filter>
                <action android:name="com.baidu.android.pushservice.action.METHOD" />
                <action android:name="com.baidu.android.pushservice.action.BIND_SYNC" />
            </intent-filter>
            <intent-filter>
                <action android:name="android.intent.action.PACKAGE_REMOVED"/>
                <data android:scheme="package" />
            </intent-filter>                   
        </receiver>
        
        <service
            android:name="com.baidu.android.pushservice.PushService"
            android:exported="true"
            android:process=":bdservice_v1"  >
            <intent-filter>
                <action android:name="com.baidu.android.pushservice.action.PUSH_SERVICE" />
            </intent-filter>
        </service>

9. 將名為 **ConfigurationSettings.java** 的新類別新增至專案。 

    ![][28]

    ![][29]

10. 在其中新增下列程式碼：

		public class ConfigurationSettings {
		        public static String API_KEY = "...";
				public static String NotificationHubName = "...";
				public static String NotificationHubConnectionString = "...";
			}
	
	使用您先前從百度雲端專案中擷取的值設定 *API_KEY* 的值，使用 Azure 入口網站中的通知中樞名稱設定 *NotificationHubName*，然後使用 Azure 入口網站中的 DefaultListenSharedAccessSignature 設定 *NotificationHubConnectionString*。 

11. 新增名為 **DemoApplication.java** 的類別，並在其中新增下列程式碼：

		import com.baidu.frontia.FrontiaApplication;
		
		public class DemoApplication extends FrontiaApplication {
		    @Override
		    public void onCreate() {
		        super.onCreate();
		    }
		}

12. 新增另一個名為 **MyPushMessageReceiver.java** 的類別，並在其中新增下列程式碼：此類別會處理接收自百度推播伺服器的推播通知：

		import java.util.List;
		import android.content.Context;
		import android.os.AsyncTask;
		import android.util.Log;
		import com.baidu.frontia.api.FrontiaPushMessageReceiver;
		import com.microsoft.windowsazure.messaging.NotificationHub;
		
		public class MyPushMessageReceiver extends FrontiaPushMessageReceiver {
		    /** TAG to Log */
			public static NotificationHub hub = null;
			public static String mChannelId, mUserId;
		    public static final String TAG = MyPushMessageReceiver.class
		            .getSimpleName();
		    
			@Override
		    public void onBind(Context context, int errorCode, String appid,
		            String userId, String channelId, String requestId) {
		        String responseString = "onBind errorCode=" + errorCode + " appid="
		                + appid + " userId=" + userId + " channelId=" + channelId
		                + " requestId=" + requestId;
		        Log.d(TAG, responseString);
		        mChannelId = channelId;
		        mUserId = userId;
		        
		        try {
		       	 if (hub == null) {
		                hub = new NotificationHub(
		                		ConfigurationSettings.NotificationHubName, 
		                		ConfigurationSettings.NotificationHubConnectionString, 
		                		context);
		                Log.i(TAG, "Notification hub initialized");
		            }
		        } catch (Exception e) {
		           Log.e(TAG, e.getMessage());
		        }
		        
		        registerWithNotificationHubs();
			}
		    
		    private void registerWithNotificationHubs() {
		       new AsyncTask<Void, Void, Void>() {
		          @Override
		          protected Void doInBackground(Void... params) {
		             try {
		            	 hub.registerBaidu(mUserId, mChannelId);
		            	 Log.i(TAG, "Registered with Notification Hub - '" 
		     	    			+ ConfigurationSettings.NotificationHubName + "'" 
		     	    			+ " with UserId - '"
		     	    			+ mUserId + "' and Channel Id - '"
		     	    			+ mChannelId + "'");
		             } catch (Exception e) {
		            	 Log.e(TAG, e.getMessage());
		             }
		             return null;
		         }
		       }.execute(null, null, null);
		    }
		    
		    @Override
		    public void onSetTags(Context context, int errorCode,
		            List<String> sucessTags, List<String> failTags, String requestId) {
		        String responseString = "onSetTags errorCode=" + errorCode
		                + " sucessTags=" + sucessTags + " failTags=" + failTags
		                + " requestId=" + requestId;
		        Log.d(TAG, responseString);
		    }
		
		    @Override
		    public void onDelTags(Context context, int errorCode,
		            List<String> sucessTags, List<String> failTags, String requestId) {
		        String responseString = "onDelTags errorCode=" + errorCode
		                + " sucessTags=" + sucessTags + " failTags=" + failTags
		                + " requestId=" + requestId;
		        Log.d(TAG, responseString);
		    }
		
		    @Override
		    public void onListTags(Context context, int errorCode, List<String> tags,
		            String requestId) {
		        String responseString = "onListTags errorCode=" + errorCode + " tags="
		                + tags;
		        Log.d(TAG, responseString);
		    }
		
		    @Override
		    public void onUnbind(Context context, int errorCode, String requestId) {
		        String responseString = "onUnbind errorCode=" + errorCode
		                + " requestId = " + requestId;
		        Log.d(TAG, responseString);
		    }
		
		    @Override
		    public void onNotificationClicked(Context context, String title,
		            String description, String customContentString) {
		        String notifyString = "title=\"" + title + "\" description=\""
		                + description + "\" customContent=" + customContentString;
		        Log.d(TAG, notifyString);
		    }
		
		    @Override
		    public void onMessage(Context context, String message,
		            String customContentString) {
		        String messageString = "message=\"" + message + "\" customContentString=" + customContentString;
		        Log.d(TAG, messageString);
		    }
		}

13. 開啟 **MainActivity.java**，並將下列程式碼新增至 **onCreate** 方法：

	        PushManager.startWork(getApplicationContext(),
	                PushConstants.LOGIN_TYPE_API_KEY, ConfigurationSettings.API_KEY);

然後在上方新增下列 import 陳述式：
			import com.baidu.android.pushservice.PushConstants;
			import com.baidu.android.pushservice.PushManager;

##<a id="send"></a>將通知傳送至您的應用程式

您可以從任何使用 <a href="http://msdn.microsoft.com/zh-tw/library/windowsazure/dn223264.aspx">REST 介面的後端，透過通知中樞來傳送通知</a>。在本教學課程中，我們將使用 .NET 主控台應用程式加以說明。 

1. 建立新的 Visual C# 主控台應用程式：

	![][30]

2. 使用 <a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">WindowsAzure.ServiceBus NuGet 封裝來新增 Azure 服務匯流排 SDK 的參照</a>。在 Visual Studio 主功能表中，依序按一下 [工具]****、[Library Package Manager]****、[Package Manager Console]****。接著，在主控台視窗中，輸入下列程式碼並按 Enter 鍵：

        Install-Package WindowsAzure.ServiceBus

3. 開啟檔案 Program.cs，並新增下列 using 陳述式：

        using Microsoft.ServiceBus.Notifications;

4. 在 `Program` 類別中新增下列方法，並使用您的值取代 *DefaultFullSharedAccessSignatureSASConnectionString* 與 *NotificationHubName*。 

		private static async void SendNotificationAsync()
		{
			NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("DefaultFullSharedAccessSignatureSASConnectionString", "NotificationHubName");
			string message = "{\"title\":\"((Notification title))\",\"description\":\"Hello from Azure\"}";
			var result = await hub.SendBaiduNativeNotificationAsync(message);
		}

5. 接著在您的 Main 方法中新增下列程式碼行：

         SendNotificationAsync();
		 Console.ReadLine();

##<a name="test-app"></a>測試應用程式

若要使用真正的電話來測試應用程式，您只需使用 USB 纜線來將它連接到電腦即可。

若要使用模擬器測試應用程式：

1. 在 Eclipse 上方工具列中，按一下 [執行]，然後選取您的應用程式。 

2. 這會將您的應用程式載入連接的電話中，或者啟動模擬器的位置，然後載入並執行此應用程式。

3. 應用程式會從百度推播通知服務擷取 'userId' 和 'channelId'，並向通知中樞註冊。
	
4.	若要使用 .Net 主控台應用程式傳送測試通知，您只需在 Visual Studio 中按 F5 鍵以執行應用程式，它便會傳送即將顯示在裝置或模擬器上方通知區域的通知。 


<!-- Images. -->
[1]: ./media/notification-hubs-baidu-get-started/BaiduRegistration.png
[2]: ./media/notification-hubs-baidu-get-started/BaiduRegistrationInput.png
[3]: ./media/notification-hubs-baidu-get-started/BaiduConfirmation.png
[4]: ./media/notification-hubs-baidu-get-started/BaiduActivationEmail.png
[5]: ./media/notification-hubs-baidu-get-started/BaiduRegistrationMore.png
[6]: ./media/notification-hubs-baidu-get-started/BaiduOpenCloudPlatform.png
[7]: ./media/notification-hubs-baidu-get-started/BaiduDeveloperServices.png
[8]: ./media/notification-hubs-baidu-get-started/BaiduDeveloperRegistration.png
[9]: ./media/notification-hubs-baidu-get-started/BaiduDevRegistrationInput.png
[10]: ./media/notification-hubs-baidu-get-started/BaiduDevRegistrationConfirmation.png
[11]: ./media/notification-hubs-baidu-get-started/BaiduDevConfirmationFinal.png
[12]: ./media/notification-hubs-baidu-get-started/BaiduCloudPush.png
[13]: ./media/notification-hubs-baidu-get-started/BaiduDevSvcMgmt.png
[14]: ./media/notification-hubs-baidu-get-started/BaiduCreateProject.png
[15]: ./media/notification-hubs-baidu-get-started/BaiduCreateProjectInput.png
[16]: ./media/notification-hubs-baidu-get-started/BaiduProjectKeys.png
[17]: ./media/notification-hubs-baidu-get-started/AzureNHCreation.png
[18]: ./media/notification-hubs-baidu-get-started/NotificationHubs.png
[19]: ./media/notification-hubs-baidu-get-started/NotificationHubsConfigure.png
[20]: ./media/notification-hubs-baidu-get-started/NotificationHubBaiduConfigure.png
[21]: ./media/notification-hubs-baidu-get-started/NotificationHubsConnectionStringView.png
[22]: ./media/notification-hubs-baidu-get-started/NotificationHubsConnectionString.png
[23]: ./media/notification-hubs-baidu-get-started/EclipseNewProject.png
[24]: ./media/notification-hubs-baidu-get-started/EclipseProjectCreation.png
[25]: ./media/notification-hubs-baidu-get-started/EclipseBlankActivity.png
[26]: ./media/notification-hubs-baidu-get-started/EclipseProjectBuildProperty.png
[27]: ./media/notification-hubs-baidu-get-started/EclipseBaiduReferences.png
[28]: ./media/notification-hubs-baidu-get-started/EclipseNewClass.png
[29]: ./media/notification-hubs-baidu-get-started/EclipseConfigSettingsClass.png
[30]: ./media/notification-hubs-baidu-get-started/ConsoleProject.png
[31]: ./media/notification-hubs-baidu-get-started/BaiduPushConfig1.png
[32]: ./media/notification-hubs-baidu-get-started/BaiduPushConfig2.png
[33]: ./media/notification-hubs-baidu-get-started/BaiduPushConfig3.png

<!-- URLs. -->
[行動服務 Android SDK] https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[百度推播 Android SDK]： http://developer.baidu.com/wiki/index.php?title=docs/cplat/push/sdk/clientsdk
[Azure 管理入口網站]： https://manage.windowsazure.com/
[百度入口網站]： http://www.baidu.com/


<!--HONumber=35.2-->
