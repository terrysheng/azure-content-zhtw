<properties urlDisplayName="Get Started" pageTitle="開始使用 Azure 通知中心 " metaKeywords="" description="Learn how to use Azure Notification Hubs to push notifications." metaCanonical="" services="notification-hubs" documentationCenter="Mobile" title="Get started with Notification Hubs" authors="piyushjo" solutions="" manager="dwrede" editor="" />

<tags ms.service="" ms.devlang="" ms.topic="article" ms.tgt_pltfrm="" ms.workload="" ms.date="10/03/2014" ms.author="piyushjo" />

# 開始使用通知中心

<div class="dev-center-tutorial-selector sublanding"><a href="/zh-tw/documentation/articles/notification-hubs-windows-store-dotnet-get-started/" title="Windows Store C#">Windows Store C#</a><a href="/zh-tw/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/zh-tw/documentation/articles/notification-hubs-ios-get-started/" title="iOS">iOS</a><a href="/zh-tw/documentation/articles/notification-hubs-android-get-started/" title="Android">Android</a><a href="/zh-tw/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle">Kindle</a><a href="/zh-tw/documentation/articles/notification-hubs-baidu-get-started/" title="Baidu" class="current">Baidu</a><a href="/zh-tw/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS">Xamarin.iOS</a><a href="/zh-tw/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android">Xamarin.Android</a></div>

百度雲端推播是可將推播通知傳送至行動裝置的中文雲端服務。由於存在著不同的應用程式市集、推播服務及通常不會連線到 GCM (Google 雲端通訊) 的 Android 裝置可用性，在中國要將推播通知傳送至 Android 相當複雜，因此該服務在中國特別有用。 

本教學課程需要下列各項：

+ Android SDK (假設您將使用 Eclipse)，您可以從<a href="http://go.microsoft.com/fwlink/?LinkId=389797">此處</a>下載。
+ [行動服務 Android SDK]
+ [Baidu Push Android SDK]

>[WACOM.NOTE] 若要完成此教學課程，您必須具備有效的 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 <a href="http://www.windowsazure.com/zh-tw/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fzh-tw%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Azure 免費試用</a>.

本教學課程將逐步引導您完成下列啟用推播通知的基本步驟：

* [建立百度帳戶](#createBaiduAccount)
* [註冊為百度開發人員](#registerBaiduDeveloper)
* [建立百度雲端推播專案](#createBaiduPushProject)
* [設定您的通知中心](#configure-hub)
* [將您的應用程式連接到通知中心](#connecting-app)
* [將通知傳送至您的應用程式](#send)

##<a id="createBaiduAccount"></a>建立百度帳戶

若要使用百度，您必須建立帳戶。如果您已經有一個百度帳戶，請使用該帳戶登入 [百度入口網站]，並跳到下一個步驟。否則，請參閱下方如何建立新百度帳戶的指示。  

1. 移至 [百度入口網站]，並按一下 [登录] (登入) 連結。按一下 [立即注册] 以啟動新的帳戶註冊程序。 

   	![][1]

2. 輸入必要的詳細資料 - 電話/電子郵件地址、密碼和驗證碼，然後按一下 [註冊]。

   	![][2]

3. 您所輸入的電子郵件地址將會收到一封電子郵件，內含啟用百度帳戶的連結。 

   	![][3]

4. 登入您的電子郵件帳戶，開啟百度啟用郵件，然後按一下啟動連結以啟動您的百度帳戶。 

   	![][4]

在啟動百度帳戶之後，請使用您的帳戶登入 [百度入口網站]。 

##<a id="registerBaiduDeveloper"></a>註冊為百度開發人員

1. 在登入 [百度入口網站] 之後，按一下 [**更多>> (詳細說明)**]。

  	![][5]

2. 向下捲動至 [**站长与开发者服务 (網站管理員和開發人員服務)**] 區段，並按一下 [**百度开放云平台 (百度開放雲端平台)**]。 

  	![][6]

3. 在下一個頁面上，按一下右上角的 [**开发者服务 (開發人員服務)**]。 

  	![][7]

4. 在下一個頁面上，按一下右上角功能表中的 [**注册开发者 (已註冊的開發人員)**]。 

  	![][8]

5. 輸入您的名稱、描述和可收到驗證文字訊息的行動電話號碼，然後按一下 [**送验证码 (傳送驗證碼)**]。請注意，若是國際電話號碼，您必須將國碼用大括號括住，例如，若是美國電話號碼，請輸入 **(1)1234567890**。

  	![][9]

6. 您接著應該會收到包含驗證號碼的簡訊，如下列範例所示：

  	![][10] 

7. 請在 [**验证码 (確認碼)**] 中輸入訊息內的驗證號碼。 

8. 最後，在接受百度合約並按一下 [**提交 (提交)**] 後，即可完成開發人員註冊。在成功完成註冊時，您將會看到下列頁面：

  	![][11] 

##<a id="createBaiduPushProject"></a>建立百度雲端推播專案

建立百度雲端推播專案時，您會收到應用程式識別碼、API 金鑰和秘密金鑰。

1. 在登入 [百度入口網站] 之後，按一下 [**更多>> (詳細說明)**]。

  	![][5]

2. 向下捲動至 [**站长与开发者服务 (網站管理員和開發人員服務)** ] 區段，並按一下 [**百度开放云平台 (百度開放雲端平台)**]。 

  	![][6]

3. 在下一個頁面上，按一下右上角的 [**开发者服务 (開發人員服務)**]。 

  	![][7]

4. 在下一個頁面上，按一下 [**云服务 (雲端服務)**] 區段中的 [**云推送 (雲端推播)**]。 

  	![][12]

5. 在成員已註冊的開發人員之後，您會在上方功能表中看到 [**管理控制台 (管理主控台)**]。按一下 [**开发者服务管理 (開發人員服務管理)**]。 

  	![][13]

6. 在下一個頁面上，按一下 [**创建工程 (建立專案)**]。

  	![][14]

7. 輸入應用程式名稱，然後按一下 [**创建 (建立)**]。

  	![][15]

8. 成功建立時，您將會看到包含 **AppID**、**API 金鑰**和**秘密金鑰**的頁面。請記下 **API 金鑰**和**秘密金鑰**，我們稍後將會用到這些資訊。 

  	![][16]

9. 按一下左窗格上的 [ **云推送 (雲端推播)** ]，即可設定推播通知的專案。 

  	![][31]

10. 在下一個頁面上，按一下 [**推送设置 (推播設定)** ] 按鈕。

	![][32]  

11. 在設定頁面上的 [**应用包名 (應用程式套件)** ] 欄位中，新增您要用於 Android 專案的專案名稱，然後按一下 [**保存设置 (儲存)**  ]。

	![][33]

您將會看到 [**保存成功！(成功儲存！)**] 訊息。

##<a id="configure-hub"></a>設定您的通知中心

1. 登入 [Azure 管理入口網站]，然後按一下畫面底部的 [**+新增**]。

2. 依序按一下 [**應用程式服務**]、[**服務匯流排**]、[**通知中心**] 及 [**快速建立**]。
 
3. 為您的 [**通知中心**] 提供一個名稱，選取要建立此通知中心的 [**區域** ] 和 [**命名空間**]，然後按一下 [**Create a New Notification Hub**  ]。

  	![][17]

4. 按一下您要建立通知中心的命名空間，然後按一下頂端的 [**通知中心**]。 

  	![][18]

5. 選取您所建立的通知中心，然後按一下上方功能表中的 [**設定**]。

  	![][19]

6. 向下捲動到 [**百度通知設定**] 區段，並輸入您先前在百度主控台中為您的百度雲端推播專案取得的 [**API 金鑰**] 和 [**秘密金鑰**]。在輸入這些值之後，按一下 [**儲存**]。 

  	![][20]

7. 按一下通知中心上方的 [**儀表板**] 索引標籤，然後按一下 [**檢視連接字串**]。

  	![][21]

8. 記下存取連接資訊視窗中的 **DefaultListenSharedAccessSignature** 和 **DefaultFullSharedAccessSignature**。 

    ![][22]

##<a id="connecting-app"></a>將您的應用程式連接到通知中心

1. 在 Eclipse ADT 中建立新的 Android 專案 ([檔案] -> [新增] -> [Android 應用程式])。

    ![][23]

2. 輸入 [**應用程式名稱**]，並確定 [**Minimum Required SDK**] 版本已設定為 [**API 16:Android 4.1**]。

    ![][24]

3. 按 [**下一步**] 並繼續遵循精靈的指示進行，直到出現 [**建立活動**] 視窗為止。請確定已選取 [**空白活動**]，最後，選取 [**完成**] 以建立新的 Android 應用程式。 

    ![][25]

4. 請確定已正確設定 [**Project Build Target**]。

    ![][26]

5. 下載並解壓縮 [[行動服務 Android SDK]]，開啟 **notificationhubs** 資料夾，將 **notification-hubs-x.y.jar** 檔案複製到 Eclipse 專案的 *libs* 資料夾，然後重新整理 *libs* 資料夾。

6. 下載並解壓縮 [[Baidu Push Android SDK]]，開啟 **libs** 資料夾，在您的 Android 應用程式的 **libs** 資料夾中複製 *pushservice-x.y.z* jar 檔案和 *armeabi* 與 *mips* 資料夾。 

    ![][27]

7. 開啟您的 Android 專案的 **AndroidManifest.xml**，並新增 Baidu SDK 所需的權限。

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

8. 將 *android:name* 屬性加入 **AndroidManifest.xml** 中的 *application* 元素，並取代 *yourprojectname* (例如 **com.example.BaiduTest**)。請確定此專案名稱與您在百度主控台中所設定的專案名稱相符。 

		<application android:name="yourprojectname.DemoApplication"

9. 將下列組態新增至應用程式元素內的 .MainActivity 活動元素後面，並取代 *yourprojectname* (例如 **com.example.BaiduTest**)：

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

10. 對其新增下列程式碼：

		public class ConfigurationSettings {
		        public static String API_KEY = "...";
				public static String NotificationHubName = "...";
				public static String NotificationHubConnectionString = "...";
			}
	
	使用您先前從百度雲端專案所擷取的金鑰來設定 *API_KEY* 的值，使用 Azure 入口網站中的通知中心名稱來設定 *NotificationHubName*，並使用 Azure 入口網站中的 DefaultListenSharedAccessSignature 來設定 *NotificationHubConnectionString*。 

11. 新增名為 **DemoApplication.java** 的類別，並將下列程式碼新增至該類別：

		import com.baidu.frontia.FrontiaApplication;
		
		public class DemoApplication extends FrontiaApplication {
		    @Override
		    public void onCreate() {
		        super.onCreate();
		    }
		}

12. 新增名為 **MyPushMessageReceiver.java** 的另一個類別，並將下列程式碼新增至該類别。這會是處理從百度推播伺服器收到之推播通知的類別：

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

13. 開啟 **MainActivity.java**，並將下列內容新增至 **onCreate** 方法：

	        PushManager.startWork(getApplicationContext(),
	                PushConstants.LOGIN_TYPE_API_KEY, ConfigurationSettings.API_KEY);

並在頂端新增下列 import 陳述式：
			import com.baidu.android.pushservice.PushConstants;
			import com.baidu.android.pushservice.PushManager;

##<a id="send"></a>將通知傳送至您的應用程式

您可以從任何使用 <a href="http://msdn.microsoft.com/zh-tw/library/windowsazure/dn223264.aspx">REST 介面</a>的後端中，透過通知中心來傳送通知。在本教學課程中，我們將使用 .NET 主控台應用程式加以說明。 

1. 建立新的 Visual C# 主控台應用程式：

	![][30]

2. 使用 <a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">WindowsAzure.ServiceBus NuGet 封裝</a>來新增 Azure 服務匯流排 SDK 的參照。在 Visual Studio 主功能表中，依序按一下 [**工具**]、[**Library Package Manager**]、[**Package Manager Console**]。接著，在主控台視窗中，輸入下列程式碼並按 Enter 鍵：

        Install-Package WindowsAzure.ServiceBus

3. 開啟檔案 Program.cs，並新增下列 using 陳述式：

        using Microsoft.ServiceBus.Notifications;

4. 在 `Program` 類別中新增下列方法，並使用您具有的值來取代 *DefaultFullSharedAccessSignatureSASConnectionString* 和 *NotificationHubName*。 

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

3. 應用程式會從百度推播通知服務中擷取 'userId' 和 'channelId'，並向通知中心註冊。
	
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
[行動服務 Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Baidu Push Android SDK]: http://developer.baidu.com/wiki/index.php?title=docs/cplat/push/sdk/clientsdk
[Azure 管理入口網站]: https://manage.windowsazure.com/
[百度入口網站]: http://www.baidu.com/








	
