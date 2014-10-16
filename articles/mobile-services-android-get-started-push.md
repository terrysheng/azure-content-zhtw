<properties linkid="develop-mobile-tutorials-get-started-with-push-android" urlDisplayName="Get Started with Push (Android)" pageTitle="Get started with push notifications (Android) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your Android app." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="ricksal" solutions="" manager="dwrede" editor="" />

# 開始使用行動服務中的推播通知

 
<div  class="dev-center-tutorial-selector sublanding">
	<a href="/en-us/develop/mobile/tutorials/get-started-with-push-dotnet" title="Windows 市集 C#">Windows 市集 C#</a><a href="/en-us/develop/mobile/tutorials/get-started-with-push-js" title="Windows 市集 JavaScript">Windows 市集 JavaScript</a><a href="/en-us/develop/mobile/tutorials/get-started-with-push-wp8" title="Windows Phone">Windows Phone</a><a href="/en-us/develop/mobile/tutorials/get-started-with-push-ios" title="iOS">iOS</a><a href="/en-us/develop/mobile/tutorials/get-started-with-push-android" title="Android" class="current">Android</a><a href="/en-us/develop/mobile/tutorials/get-started-with-push-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a><a href="/en-us/develop/mobile/tutorials/get-started-with-push-xamarin-android" title="Xamarin.Android">Xamarin.Android</a>
</div>

 本主題說明如何使用 Azure 行動服務傳送推播通知至 Android 應用程式。在本教學課程中，您會使用 Google 雲端通訊 (GCM)
服務將推播通知新增至快速入門專案。完成後，您的行動服務將在每次插入記錄時傳送推播通知。


> [WACOM.NOTE] 本主題說明如何使用行動服務所提供的舊版支援來啟用推播通知。Azure
> 通知中心會與行動服務整合，讓您可以將範本型的跨平台推播通知傳送至數百萬個裝置。預設不會啟用使用通知中心進行推播通知，且行動服務程式庫中目前沒有
> Android
> 適用的通知中心支援。不過，您可以使用通知中心程式庫，從自己的行動服務傳送推播通知。如需詳細資訊，請參閱[開始使用通知中心](/zh-tw/documentation/articles/notification-hubs-android-get-started/)。

本教學課程將引導您逐步完成以下啟用推播通知的基本步驟：

* [啟用 Google 雲端通訊](#register)
* [設定行動服務](#configure)
* [新增推播通知至應用程式](#add-push)
* [更新指令碼來傳送推播通知](#update-scripts)
* [插入資料來接收通知](#test)

本教學課程需使用：

* [行動服務 Android SDK][1]
* 有效的 Google 帳戶

本教學課程以行動服務快速入門為基礎。在開始本教學課程之前，請務必先完成[開始使用行動服務](/en-us/develop/mobile/tutorials/get-started-android)。

## <a id="register" ></a>啟用 Google 雲端通訊

[WACOM.INCLUDE [啟用 GCM](../includes/mobile-services-enable-Google-cloud-messaging.md)]

接下來，您將使用此 API 金鑰值，讓行動服務能夠使用 GCM 進行驗證並代表您的應用程式傳送推播通知。

## <a id="configure" ></a>設定行動服務來傳送推播要求

1.  登入 **Azure 管理入口網站][2]，按一下 [行動服務**，然後按一下您的應用程式。
    
	![][18]

2.  按一下 **發送** 索引標籤，輸入前一個程序中從 GCM 取得的 **API Key** 值，然後按一下
    **儲存**。
    
	![][19]

您的行動服務現在已設定成使用 GCM 來傳送推播通知。

## <a id="add-push" ></a>新增推播通知至應用程式

### 新增 Google Play 服務至專案

[WACOM.INCLUDE [新增 Play 服務](../includes/mobile-services-add-Google-play-services.md)]

### 新增程式碼

1.  開啟專案檔案 **AndroidManifest.xml**。Google 雲端通訊在開發和測試方面有一些 API 層級的最低需求，這些是資訊清單中的 **minSdkVersion** 屬性所必須遵守。如果您因為是使用舊款裝置而需要將此值設為低於 16，請參閱[設定 Google Play 服務 SDK][3] (英文)判斷可設到多低。請適當設定此屬性。

2.  在 `uses-sdk` 元素中，確定 **targetSdkVersion** 已設為步驟 1 所安裝 SDK 平台的版本號碼。最好是設為最新可用版本。

3.  **uses-sdk** 標籤看起來可能如下，視您在先前步驟中的選擇而定：
    
         <uses -sdk
        android:minSdkVersion="17"
        android:targetSdkVersion="19" />

4.  在下兩個步驟的程式碼中，以專案的應用程式套件名稱 (即 `manifest` 標籤中的 `package` 屬性值) 取代 _`**my_app_package**`_。

5.  在現有 `uses-permission` 元素後新增下列新權限：
    
         <permission  android:name="**my_app_package**.permission.C2D_MESSAGE" 
        android:protectionLevel="signature" />
        <uses -permission android:name="**my_app_package**.permission.C2D_MESSAGE" /> 
        <uses -permission android:name="com.google.android.c2dm.permission.RECEIVE" />
        <uses -permission android:name="android.permission.GET_ACCOUNTS" />
        <uses -permission android:name="android.permission.WAKE_LOCK" />

6.  在 `application` 起始標籤後新增下列程式碼：
    
        <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
            android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <a ction android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="**my_app_package**" />
            </intent-filter>
        </receiver>

7.  開啟 ToDoItem.java 檔案，新增下列程式碼至 **TodoItem** 類別：
    
             	@com.google.gson.annotations.SerializedName("handle")
			private String mHandle;
		
			public String getHandle() {
				return mHandle;
			}
		
			public final void setHandle(String handle) {
				mHandle = handle;
			}
    
    此程式碼會建立用來存放註冊 ID 的新屬性。
	<div class="dev-callout"><b>注意</b>
	<p>當行動服務上已啟用動態結構描述，且插入的新項目含有此屬性時，<strong>TodoItem</strong> 資料表中就會自動新增 <strong>handle</strong> 欄。</p>
	</div>


8.  下載並解壓縮[行動服務 Android SDK][1]，開啟 	**notifications** 資料夾，將 **notifications-1.0.1.jar** 檔案複製到您 Eclipse 專案的 *libs* 資料夾，然後重新整理 *libs* 資料夾。
	<div class="dev-callout"><b>注意</b>
	<p>檔案名稱結尾的數字在後續 SDK 版本中可能會變更。</p>
	</div>


9.  開啟 *ToDoItemActivity.java* 檔案，新增下列 import 陳述式：
    
        import com.microsoft.windowsazure.notifications.NotificationsManager;

10. 新增下列私用變數至類別，其中 *`<PROJECT _NUMBER>`* 是先前程序中由 Google 指派給您應用程式的專案編號：
    
        public static final String SENDER_ID = "<PROJECT _NUMBER>";

11. 在 **onCreate** 方法中，在 MobileServiceClient 具現化之前，新增此程式碼來註冊裝置的通知處理常式：
    
        NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);

12. 在 **addItem** 方法中，在將項目插入資料表之前，新增下面這行程式碼：
    
        item.setHandle(MyHandler.getHandle());
    
    此程式碼會將項目的 `handle` 屬性設為裝置的註冊 ID。

13. 在 Package Explorer 中，以滑鼠右鍵按一下封裝 (在 [src]`` 節點下)，按一下 **New**，按一下 **Class**。

14. 在 **Name** 輸入 `MyHandler`，在 **Superclass** 輸入 `com.microsoft.windowsazure.notifications.NotificationsHandler`，然後按一下 **Finish**
    
    ![](./media/mobile-services-android-get-started-push/mobile-services-android-create-class.png)
    
    如此即會建立新的 MyHandler 類別。

15. 新增下列 import 陳述式：
    
        import android.content.Context;
        	
        import com.microsoft.windowsazure.notifications.NotificationsHandler;

16. 新增下列程式碼：
    
        @com.google.gson.annotations.SerializedName("handle")
        private static String mHandle;
        
        public static String getHandle() {
        return mHandle;
        }
        
        public static final void setHandle(String handle) {
        mHandle = handle;
        }

17. 以下列程式碼取代現有 **onRegistered** 方法：
    
        @Override
        public void onRegistered(Context context, String gcmRegistrationId) {
        super.onRegistered(context, gcmRegistrationId);
        		
        setHandle(gcmRegistrationId);
        }

您的應用程式現在已更新為支援推播通知。

## <a id="update-scripts" ></a>在管理入口網站中更新已註冊的 insert 指令碼

1.  在管理入口網站中，按一下 **資料** 索引標籤，然後按一下 **TodoItem** 資料表。
    
	![][21]

2.  在 **todoitem** 中，按一下 **指令碼** 索引標籤，然後選取 **插入**。

	![](./media/mobile-services-android-get-started-push/mobile-insert-script-push2.png) 

	如此即會顯示 **TodoItem** 資料表中發生插入時所叫用的函數。

1. 使用下列程式碼來取代 insert 函數，然後按一下 **儲存**：
    
		function insert(item, user, request) {
			request.execute({
				success: function() {
					// Write to the response and then send the notification in the background
					request.respond();
					push.gcm.send(item.handle, item.text, {
						success: function(response) {
							console.log('Push notification sent: ', response);
						}, error: function(error) {
							console.log('Error sending push notification: ', error);
						}
					});
				}
			});
		}
        

	如此即會註冊新的 insert 指令碼，該指令碼會使用 [gcm 物件] 將推播通知 (插入的文字) 傳送給插入要求中所提供的裝置。
 

## <a id="test" ></a>在應用程式中測試推播通知
<div class="dev-callout"><b>注意</b>
	<p>當您在模擬器中執行此應用程式時，請務必使用支援 Google API 的 Android 虛擬裝置 (AVD)。</p>
</div>

1.  重新啟動 Eclipse，然後在 Package Explorer 中以滑鼠右鍵按一下專案，按一下 **Properties**，按一下 **Android**，核取 **Google API**，然後按一下 **OK**。
    
    ![](./media/mobile-services-android-get-started-push/mobile-services-import-android-properties.png)

 	如此會使專案的目標變成 Google API。

1.	從 **Window** 中選取 **Android Virtual Device Manager**，選取您的裝置，按一下 **Edit**。
    
	![](./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager.png)

2.  在 **Target** 中選取 **Google API**，然後按一下 [OK]。
    
	![][25]
    
    如此會使 AVD 變成使用 Google API。

3.  在 **Run** 功能表中按一下 **Run** 啟動應用程式。

4.	在應用程式中，輸入有意義的文字，如 *A new Mobile Services task*，然後按一下 **加入** 按鈕。

 	![](./media/mobile-services-android-get-started-push/mobile-quickstart-push1-android.png) 

3.	您將在螢幕的下半部看到黑色通知方塊短暫出現。

 	![](./media/mobile-services-android-get-started-push/mobile-push-icon.png) 

	<!--7. Tap on the icon and swipe down to display the notification, which appears in the graphic below.	![][27]-->

	您已成功完成此教學課程。

## <a name="next-steps"></a>後續步驟

在這個簡單範例中，使用者收到推播通知，其中含有剛插入的資料。用戶端會在要求中提供 GCM 所使用的裝置權杖給行動服務。在下一個教學課程[推播通知至應用程式使用者](/en-us/develop/mobile/tutorials/push-notifications-to-users-android)中，您將另行建立 Devices 資料表來儲存裝置權杖，並在發生插入動作時傳送推播通知給已儲存的所有通道。

<!-- Images. -->

<!-- URLs. -->



[1]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[2]: https://manage.windowsazure.com/
[3]: http://go.microsoft.com/fwlink/?LinkId=389801

[1]: ./media/mobile-services-android-get-started-push/mobile-services-google-developers.png
[2]: ./media/mobile-services-android-get-started-push/mobile-services-google-create-server.png
[3]: ./media/mobile-services-android-get-started-push/mobile-services-google-create-server2.png
[4]: ./media/mobile-services-android-get-started-push/mobile-services-google-create-server3.png
[5]: ./media/mobile-services-android-get-started-push/mobile-services-android-sdk-manager.png
[6]: ./media/mobile-services-android-get-started-push/mobile-services-android-create-class.png
[7]: ./media/mobile-services-android-get-started-push/mobile-google-cloud-new-project.png
[8]: ./media/mobile-services-android-get-started-push/mobile-google-cloud-sms-verify.png
[9]: ./media/mobile-services-android-get-started-push/mobile-google-cloud-project-created.png
[10]: ./media/mobile-services-android-get-started-push/mobile-google-choose-play-api.png
[18]: ./media/mobile-services-android-get-started-push/mobile-services-selection.png
[19]: ./media/mobile-services-android-get-started-push/mobile-push-tab-android.png
[21]: ./media/mobile-services-android-get-started-push/mobile-portal-data-tables.png
[22]: ./media/mobile-services-android-get-started-push/mobile-insert-script-push2.png
[23]: ./media/mobile-services-android-get-started-push/mobile-services-import-android-properties.png
[24]: ./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager.png
[25]: ./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager-edit.png
[26]: ./media/mobile-services-android-get-started-push/mobile-quickstart-push1-android.png
[27]: ./media/mobile-services-android-get-started-push/mobile-quickstart-push2-android.png
[28]: ./media/mobile-services-android-get-started-push/mobile-push-icon.png
[29]: ./media/mobile-services-android-get-started-push/mobile-eclipse-import-Play-library.png