<properties urlDisplayName="Get Started" pageTitle="開始使用 Azure 通知中心 " metaKeywords="" description="了解如何使用 Azure 通知中心推播通知。" metaCanonical="" services="notification-hubs" documentationCenter="Mobile" title="Get started with Notification Hubs" authors="piyushjo" solutions="" manager="timlt" editor="" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-nokia-x" ms.devlang="java" ms.topic="article" ms.date="01/01/1900" ms.author="piyushjo" />
# 開始使用通知中心

<div class="dev-center-tutorial-selector sublanding"><a href="/zh-tw/documentation/articles/notification-hubs-windows-store-dotnet-get-started/" title="Windows Universal">Windows Universal</a><a href="/zh-tw/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/zh-tw/documentation/articles/notification-hubs-ios-get-started/" title="iOS">iOS</a><a href="/zh-tw/documentation/articles/notification-hubs-android-get-started/" title="Android">Android</a><a href="/zh-tw/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle">Kindle</a><a href="/zh-tw/documentation/articles/notification-hubs-nokia-x-get-started/" title="Nokia X" class="current">Nokia X</a><a href="/zh-tw/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS">Xamarin.iOS</a><a href="/zh-tw/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android">Xamarin.Android</a></div>

本主題說明如何使用 [**Azure 通知中心**] 傳送推播通知至 **Nokia X** 上的 Android 應用程式。在本教學課程中，您將建立可使用 Nokia 通知服務接收推播通知的空白 Android 應用程式。完成時，您便能夠使用通知中心，將推播通知廣播到所有正在執行您應用程式的裝置。

本教學課程將逐步引導您完成下列啟用推播通知的基本步驟：

* [設定 Nokia 通知服務](#register)
* [設定您的通知中心](#configure-hub)
* [將您的應用程式連接到通知中心](#connect-hub)
* [如何傳送通知至應用程式](#send)
* [測試應用程式](#test-app)

<div class="dev-callout"><strong>注意</strong> <p>若要完成此教學課程，您必須具備有效的 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 <a href="http://www.windowsazure.com/zh-tw/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fzh-tw%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Azure 免費試用</a>。</p></div>

本教學課程需要下列各項：

1.	Nokia X 開發環境，您可依照<a href="http://developer.nokia.com/resources/library/nokia-x/getting-started/environment-setup.html">此處</a>的指示進行設定。請務必依照下列指示安裝 Nokia X 特定套件，並設定 Nokia X 模擬器。 
2.	Nokia X 裝置設定 (選擇性)，您可依照<a href="http://developer.nokia.com/resources/library/nokia-x/getting-started/device-setup.html">此處</a>的指示進行設定。
3.	Android SDK (假設您將使用 Eclipse)，您可以從<a href="http://go.microsoft.com/fwlink/?linkid=389797&clcid=0x409">此處</a>下載。
4.	行動服務 Android SDK，您可以從<a href="https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409">此處<a>下載。 

##<a id="register"></a>設定 Nokia 通知服務

1. 登入 <a href="https://console.push.nokia.com/ncm/Web/index.jsp">Nokia 通知 API 開發人員主控台</a>。

2. 移至 [**建立服務**] 索引標籤，並透過提供 [**寄件者識別碼**] 和 [**服務描述**] 建立新的服務

	![][11]

3. 成功建立服務之後，請記下 [**寄件者識別碼**] 和 [**授權金鑰**]。 

4. 您可以移至 [**我的服務**] 索引標籤，列出您所建立的所有服務及其各自的[ **寄件者識別碼**] 和 [**授權金鑰**]

	![][12]

5. 如需詳細資料，請參閱本<a href="http://developer.nokia.com/resources/library/nokia-x/nokia-notifications/nokia-notifications-developer-guide.html">連結</a>。 

##<a id="configure-hub"></a>設定您的通知中心

1. 登入 [Azure 管理入口網站]，然後按一下畫面底部的 [**+新增**]。

2. 依序按一下 [**應用程式服務**]、[**服務匯流排**]、[**通知中心**] 及 [**快速建立**]。

	![][3]

3. 為您的通知中心輸入名稱、選取所需的區域和選擇性命名空間，然後按一下 [**Create a new Notification Hub**]。

	![][2]

4. 移至您剛剛建立的通知中心。按一下左側的 [服務匯流排] 索引標籤，再按一下其上建立通知中心的命名空間，然後按一下 [通知中心] 索引標籤。 

	![][9]

5. 一旦位於您所建立的通知中心上，按一下頂端的 [**設定**] 索引標籤。

	![][7]

6. 向下捲動並尋找 [**Nokia x 通知設定**]，然後貼上您從 Nokia 通知服務取得的授權金鑰，並按一下頁面底部的 [**儲存**] 按鈕

	![][8]

7. 選取頂端的 [**儀表板**] 索引標籤，並按一下頁面底部的 [**連接資訊**]。 

	![][10]

8.	請記下 **DefaultListenSharedAccessSignature** 與 **DefaultFullSharedAccessSignature** 這兩個 SAS 連接字串，稍後在教學課程中您將會用到這些資訊。 

##<a id="connect-hub"></a>將您的應用程式連接到通知中心

###建立新的 Android 專案

1. 在 Eclipse ADT 中建立新的 Android 專案 ([檔案] -> [新增] -> [Android 應用程式])。

2. 請確定 [Minimum Required SDK] 已設為 **API 16:Android 4.1 (Jelly Bean)**，且接下來的兩個 SDK 項目已設定為最新的可用版本。選擇 [下一步]，依照精靈的指示進行，並確定已選取 [**建立活動**] 以建立**空白活動**。接受下一個方塊上預設的 [啟動器] 圖示，並按一下最後一個方塊中的 [**完成**]。

	![][5]

3. 請確定已正確設定專案建置目標 (在此範例中，[平台] = 4.1.2 且 [API 層級] = 16)。以滑鼠右鍵按一下 [專案]，選取 [屬性]，然後在 [專案屬性] 對話方塊中選取 [Android]。 
	
	![][13]

4.	將 Nokia 通知服務的 JAR 檔案新增至專案。此 Nokia 通知協助程式程式庫 **push.jar** 可提供類似 GCM 協助程式程式庫的 API。 
瀏覽至 [專案屬性] -> [Java 組建路徑] -> [程式庫] -> [新增外部 JAR]，並加入可在 **\extras\nokia\nokia_x_services\libs\nna\push.jar** 中找到的 **push.jar**。程式庫的 Javadoc 會位於 Eclipse IDE 的安裝位置 **\extras\nokia\nokia_x_services\javadocs\nna**。

5. 另請務必在 [封裝總管] 中，將此 push.jar 程式庫複製到專案的 \libs 目錄。 

6. 從<a href="https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409">此處</a>下載通知中心 Android SDK。解壓縮 .zip 檔案，並在 [封裝總管] 中將 notificationhubs\notification-hubs-0.3.jar 檔案複製到專案的 \libs 目錄。

    <div class="dev-callout"><b>注意</b>
	<p>檔案名稱結尾的數字在後續 SDK 版本中可能會變更。</p>
    </div>

###新增程式碼

1. 開啟 **AndroidManifest.xml** 檔案，並使用下列程式碼行來取代應用程式元素，並確定使用您在建立 Android 應用程式時所指定的套件名稱來取代 `[YourPackageName]`。
		
		<!-- Push Notifications connects to Internet services. -->
	    <uses-permission android:name="android.permission.INTERNET" />
	
	    <!-- Keeps the processor from sleeping when a message is received. -->
	    <uses-permission android:name="android.permission.WAKE_LOCK" />
	
		 <!--
	     Creates a custom permission so only this app can receive its messages.
	
	     NOTE: the permission *must* be called PACKAGE.permission.C2D_MESSAGE,
	           where PACKAGE is the application's package name.
	    -->
	    <permission 
	        android:name="[YourPackageName].permission.C2D_MESSAGE" 
	        android:protectionLevel="signature" />
	    <uses-permission android:name="[YourPackageName].permission.C2D_MESSAGE" />
	
	    <uses-permission android:name="com.nokia.pushnotifications.permission.RECEIVE" />
	    
	    <application
	        android:allowBackup="true"
	        android:icon="@drawable/ic_launcher"
	        android:label="@string/app_name"
	        android:theme="@style/AppTheme" >
	        <activity
	            android:name="[YourPackageName].MainActivity"
	            android:label="@string/app_name" >
	            <intent-filter>
	                <action android:name="android.intent.action.MAIN" />
	                <category android:name="android.intent.category.LAUNCHER" />
	            </intent-filter>
	        </activity>
	        
	        <receiver
	            android:name="com.nokia.push.PushBroadcastReceiver"
	            android:permission="com.nokia.pushnotifications.permission.SEND" >
	            <intent-filter>
	                
					<!-- Receives the actual messages. -->
	                <action android:name="com.nokia.pushnotifications.intent.RECEIVE" />
	                <!-- Receives the registration id. -->
	                <action android:name="com.nokia.pushnotifications.intent.REGISTRATION" />
	                <category android:name="[YourPackageName]" />
	            </intent-filter>
	        </receiver>
	
	        <service android:name="[YourPackageName].PushIntentService" />
	    </application> 

2. 在 [封裝總管] 中，以滑鼠右鍵按一下封裝 (在 [`src`] 節點下)，並依序按一下 [**新増**]、[**類別**]，然後建立名為 **ConfigurationSettings.java** 的新類別。
![][6]

	接著在其中新增下列程式碼，以定義此範例會用到的常數：

		public class ConfigurationSettings {
	    	public static String NotificationHubName = "";
	    	public static String NotificationHubConnectionString = "";
	    	public static String SenderId = "";
		}
	
	使用 Nokia 推播主控台的組態 **SenderId** 和管理入口網站的 **NotificationHubName** 和 **NotificationHubConnectionString** (DefaultListenSharedAccessSignature) 來填寫上述常數。 

3. 在 **MainActivity.java** 中新增下列 import 陳述式：
	
		import com.nokia.push.PushRegistrar;

	然後在 onCreate 方法中新增下列程式碼：
 
		// Make sure the device has the proper dependencies.
		PushRegistrar.checkDevice(this);
	        
		// Make sure the manifest was properly set - comment out this line
		// while developing the app, then uncomment it when it's ready
		PushRegistrar.checkManifest(this);
	        
		// Register the device with the Nokia Notification service
		PushRegistrar.register(this, ConfigurationSettings.SenderId);

4. 新增另一個名為 **PushIntentService.java** 的類別，並新增下列程式碼，來向通知中心註冊，並處理從這個通知中心接收的訊息。 

		import android.app.NotificationManager;
		import android.app.PendingIntent;
		import android.content.Context;
		import android.content.Intent;
		import android.os.Bundle;
		import android.support.v4.app.NotificationCompat;
		import android.util.Log;
		
		import com.microsoft.windowsazure.messaging.NotificationHub;
		import com.nokia.push.PushBaseIntentService;
		
		/**
		 * IntentService responsible for handling push notification messages.
		 */
		public class PushIntentService extends PushBaseIntentService {
			
			private NotificationManager mNotificationManager;
			private static NotificationHub hub;
		    public static final int NOTIFICATION_ID = 1;
		    private static final String TAG = "NokiaXPush/PushIntentService";
		
		    /**
		     * Constructor.
		     */
		    public PushIntentService() {
		    }

		    @Override
		    protected String[] getSenderIds(Context context) {
		        return new String[] { ConfigurationSettings.SenderId };
		    }   
		    
		    @Override
		    protected void onRegistered(Context context, String registrationId) {
		    	Log.i(TAG, "Nokia Registration ID \"" + registrationId + "\"");
		    	RegisterWithNotificationHub(context, registrationId);
		    }
		
		    public static void RegisterWithNotificationHub(Context context, String registrationId) {
		        if (hub == null) {
		            hub = new NotificationHub(
		            		ConfigurationSettings.NotificationHubName, 
		            		ConfigurationSettings.NotificationHubConnectionString, 
		            		context);
		        }
		        try {
					hub.register(registrationId);
			    	Log.i(TAG, "Registered with Notification Hub - '" 
			    			+ ConfigurationSettings.NotificationHubName + "'" 
			    			+ " with RegistrationID - '"
			    			+ registrationId + "'");
				} catch (Exception e) {
					e.printStackTrace();
				}
		    }
		
		    @Override
		    protected void onMessage(Context context, Intent intent) {
		    	String message = intentExtrasToString(intent.getExtras());
		    	Log.i(TAG, "Received message. Extras: " + message);
		    	generateNotification(context, message);
		    }
		    
		    /**
		     * Extracts the key-value pairs from the given Intent extras and returns
		     * them in a string.
		     */
		    private String intentExtrasToString(Bundle extras) {
		        StringBuilder sb = new StringBuilder();
		        sb.append("{ ");
		        
		        for (String key : extras.keySet()) {
		            sb.append(sb.length() <= 2 ? "" : ", ");
		            sb.append(key).append("=").append(extras.get(key));
		        }
		        
		        sb.append(" }");
		        return sb.toString();
		    }
		
		    /**
		     * Issues a notification to inform the user that server has sent a message.
		     */
		    private void generateNotification(Context context, String message)
		    {
		    	mNotificationManager = (NotificationManager)
		                context.getSystemService(Context.NOTIFICATION_SERVICE);
		
		        PendingIntent contentIntent = PendingIntent.getActivity(context, 0,
		            new Intent(context, MainActivity.class), 0);
		
		        NotificationCompat.Builder mBuilder =
		            new NotificationCompat.Builder(context)
		            .setSmallIcon(R.drawable.ic_launcher)
		            .setContentTitle("Notification Hub Demo")
		            .setStyle(new NotificationCompat.BigTextStyle()
		                     .bigText(message))
		            .setContentText(message);
		
		        mBuilder.setContentIntent(contentIntent);
		        mNotificationManager.notify(NOTIFICATION_ID, mBuilder.build());
		    }
		
			@Override
			protected void onError(Context arg0, String errorId) {
		        Log.i(TAG, "Received error:  " + errorId);
			}
		
			@Override
			protected void onUnregistered(Context arg0, String errorId) {
		        Log.i(TAG, "Received recoverable error: " + errorId);
			}
		}

##<a name="send"></a>如何傳送通知至應用程式

您可以從任何使用 <a href="http://msdn.microsoft.com/zh-tw/library/windowsazure/dn223264.aspx">REST 介面</a>的後端中，透過通知中心來傳送通知。在本教學課程中，我們將使用 .NET 主控台應用程式加以說明。 

1. 建立新的 Visual C# 主控台應用程式：

	![][4]

2. 使用 <a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">WindowsAzure.ServiceBus NuGet 封裝</a>來新增 Azure 服務匯流排 SDK 的參照。在 Visual Studio 主功能表中，依序按一下 [**工具**]、[**Library Package Manager**]、[**Package Manager Console**]。接著，在主控台視窗中，輸入下列程式碼並按 Enter 鍵：

        Install-Package WindowsAzure.ServiceBus

3. 開啟檔案 Program.cs，並新增下列 using 陳述式：

        using Microsoft.ServiceBus.Notifications;

4. 在 `Program` 類別中，新增下列方法：

		private static async void SendNotificationAsync()
		{
			NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<DefaultFullSharedAccessSignatureSASConnectionString>", "<hub name>");
			await hub.SendNokiaXNativeNotificationAsync("{\"data\" : {\"payload\":\"" + "Hello from Azure" + "\"}}");
		}

5. 接著在您的 Main 方法中新增下列程式碼行：

         SendNotificationAsync();
		 Console.ReadLine();

##<a name="test-app"></a>測試應用程式

若要使用真正的電話來測試應用程式，您只需使用 USB 纜線來將它連接到電腦即可。

若要使用模擬器測試應用程式：

1. 在 Eclipse 上方工具列中，按一下 [執行]，然後選取您的應用程式。 

2. 這會將您的應用程式載入連接的電話中，或者啟動模擬器的位置，然後載入並執行此應用程式。

3. 應用程式將從 Nokia 通知服務擷取 registrationId，並向通知中心註冊。

    <div class="dev-callout"><b>注意</b>
	<p>
	如果 Android 應用程式可以成功向通知中心註冊，您將會在 'Eclipse Logcat' 記錄中看到一則如下所示的訊息：
	'Registered with Notification Hub - '<yourNotificationHubName>' with RegistrationID - '<RegistrationIdReturnedByNokiaNotificationService''
	</p>
    </div>
	
4.	若要使用 .Net 主控台應用程式傳送測試通知，您只需在 Visual Studio 中按 F5 鍵以執行應用程式，它便會傳送即將顯示在裝置或模擬器上方通知區域的通知。 

<!-- Images. -->
[1]: ./media/notification-hubs-nokia-x-get-started/AndroidAppProperties.png
[2]: ./media/notification-hubs-nokia-x-get-started/AzureManagementCreateNH.png
[3]: ./media/notification-hubs-nokia-x-get-started/AzureManagementPortal.png
[4]: ./media/notification-hubs-nokia-x-get-started/ConsoleProject.png
[5]: ./media/notification-hubs-nokia-x-get-started/NewAndroidApp.png
[6]: ./media/notification-hubs-nokia-x-get-started/NewJavaClass.png
[7]: ./media/notification-hubs-nokia-x-get-started/NHConfigure.png
[8]: ./media/notification-hubs-nokia-x-get-started/NHConfigureNokiaX.png
[9]: ./media/notification-hubs-nokia-x-get-started/NHConfigureTopItem.png
[10]: ./media/notification-hubs-nokia-x-get-started/NHDashboard.png
[11]: ./media/notification-hubs-nokia-x-get-started/NokiaConsole.png
[12]: ./media/notification-hubs-nokia-x-get-started/NokiaConsoleService.png
[13]: ./media/notification-hubs-nokia-x-get-started/AndroidBuildTarget.png

<!-- URLs. -->
[Azure 管理入口網站]: https://manage.windowsazure.com/


