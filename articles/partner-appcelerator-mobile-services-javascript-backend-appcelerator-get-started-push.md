<properties pageTitle="Get started with push notifications (Appcelerator) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your Appcelerator app." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="Appcelerator team;mahender" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-appcelerator" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="Appcelerator team;mahender"></tags>

# 開始在行動服務中使用推播通知 (舊式推播)

<div class="dev-center-tutorial-selector sublanding">
<a href="/zh-tw/documentation/articles/mobile-services-windows-store-dotnet-get-started-push" title="Windows 市集 C#">Windows 市集 C#</a>
<a href="/zh-tw/documentation/articles/mobile-services-windows-store-javascript-get-started-push" title="Windows 市集 JavaScript">Windows 市集 JavaScript</a>
<a href="/zh-tw/documentation/articles/mobile-services-windows-phone-get-started-push" title="Windows Phone">Windows Phone</a>
<a href="/zh-tw/documentation/articles/mobile-services-ios-get-started-push" title="iOS">iOS</a>
<a href="/zh-tw/documentation/articles/mobile-services-android-get-started-push" title="Android">Android</a>
<!--    <a href="/zh-tw/documentation/articles/partner-xamarin-mobile-services-ios-get-started-push" title="Xamarin.iOS">Xamarin.iOS</a>     <a href="/zh-tw/documentation/articles/partner-xamarin-mobile-services-android-get-started-push" title="Xamarin.Android">Xamarin.Android</a> -->
<a href="/zh-tw/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push" title="Appcelerator" class="current">Appcelerator</a>
</div>

本主題說明如何使用 Windows Azure 行動服務，傳送推播通知至透過 Appcelerator Titanium Studio 開發的 iOS 和 Android 應用程式。在本教學課程中，您會使用 Apple 推播通知服務 (APNS) 和 Google 雲端通訊將推播通知新增至快速入門專案。完成後，行動服務就會在每次插入記錄時傳送推播通知。

> [WACOM.NOTE] 行動服務會與 Azure 通知中心整合，以支援其他推播通知功能，例如範本、多個平台和改良式範圍。本主題支援尚未升級到使用通知中心整合的現有行動服務。建立新的行動服務時，此整合式功能便會自動啟用。您應升級服務，盡可能使用通知中心。**針對使用 Appcelerator 推播的通知中心，我們會儘快設法取得合適的教學課程。**

1.  [產生憑證簽署要求][產生憑證簽署要求]
2.  [註冊您的應用程式並啟用推播通知][註冊您的應用程式並啟用推播通知]
3.  [建立應用程式的佈建設定檔][建立應用程式的佈建設定檔]
4.  [啟用 Google 雲端通訊][啟用 Google 雲端通訊]
5.  [建立 Titanium 的 GCM 模組][建立 Titanium 的 GCM 模組]
6.  [設定行動服務][設定行動服務]
7.  [新增推播通知至應用程式][新增推播通知至應用程式]
8.  [更新指令碼來傳送推播通知][更新指令碼來傳送推播通知]
9.  [插入資料以接收通知][插入資料以接收通知]

本教學課程需要下列各項：

-   Appcelerator Azure 行動服務模組
-   Appcelerator Titanium Studio 3.2.1 或更新版本
-   iOS 7.0 (或更新版本) 與 Android 4.3 (或更新版本) 功能裝置
-   iOS Developer Program 成員資格
-   有效的 Google 帳戶

> [WACOM.NOTE] 基於推播通知組態需求，您必須在 iOS 功能裝置 (iPhone 或 iPad) 而非在模擬器上部署和測試推播通知。

本教學課程會以行動服務快速入門為基礎。在開始本教學課程之前，您必須首先完成[開始使用行動服務][開始使用行動服務]。

[WACOM.INCLUDE [啟用 Apple 推播通知](../includes/enable-apple-push-notifications.md)]

## <a name="register-gcm"></a>啟用 Google 雲端通訊

> [WACOM.NOTE]若要完成此程序，您必須具有已通過電子郵件地址驗證的 Google 帳戶。若要建立新的 Google 帳戶，請前往 [accounts.google.com][accounts.google.com]。

[WACOM.INCLUDE [啟用 GCM](../includes/mobile-services-enable-Google-cloud-messaging.md)]

## <a name="gcm-module"></a>建立 Titanium 的 GCM 模組

### 準備 Appcelerator Titanium Studio 進行模組建立

如果您打算建立 Android 模組，您將需要在 Appcelerator Titanium Studio 內安裝 Java 支援。請參閱 Appcelerator 的[安裝 Java 開發工具][安裝 Java 開發工具]，以取得簡短步驟 (如果您尚未這樣做)。

您將需要安裝 Android NDK。從 [][]<http://developer.android.com/sdk/ndk/index.html></a> 下載適當的 .zip 檔案，並將它解壓縮到磁碟上的某個位置。記位此位置。

### 建立新模組

1.  開啟 Appcelerator Titanium studio。

2.  依序按一下 [檔案] -\> [新增] -\> [行動模組專案]。

    ![][]

3.  在下個視窗中輸入專案設定的資料：

    -   **專案名稱：** 在此案例中，我們使用 "notificationhub" (可以相同)。

    -   **模組識別碼：** 在此案例中，我們使用 "com.winwire.notificationhub"。此值也必須與應用程式識別碼相符。

    -   **部署目標：** 在此案例中，我們選取 Android。

    > [WACOM.NOTE] 請注意，工作區的名稱不得包含空格，否則建立編譯時將會發生問題。

    ![][1]

4.  按 [下一步]，並填入模組的資訊。

    ![][2]

5.  最後，按 [完成]。

6.  開啟 timodule.xml 檔案。然後在 android 標籤中加入下列變更。

        <manifest package="com.winwire.notificationhub" android:versionCode="1" android:versionName="1.0">
        <supports-screens android:anyDensity="true"/>
        <uses-sdk android:minSdkVersion="8"/>
        <uses-permission android:name= "com.google.android.c2dm.permission.RECEIVE"/>
        <permission android:name= "${tiapp.properties['id']}.permission.C2D_MESSAGE" android:protectionLevel="signature"/>
        <uses-permission android:name= "${tiapp.properties['id']}.permission.C2D_MESSAGE"/>
        <uses-permission android:name= "android.permission.WAKE_LOCK"/>
        <uses-permission android:name= "android.permission.VIBRATE"/>
        <uses-permission android:name= "android.permission.INTERNET"/>
        <uses-permission android:name= "android.permission.GET_ACCOUNTS"/>
        <uses-permission android:name= "android.permission.USE_CREDENTIALS"/>
        <uses-permission android:name= "android.permission.ACCESS_NETWORK_STATE"/>
        <application>
        <service android:name= "com.winwire.notificationhub.GCMIntentService" />
        <receiver android:name= "com.google.android.gcm.GCMBroadcastReceiver" android:permission= "com.google.android.c2dm.permission.SEND">
        <!-- Start receiver on boot -->
        <intent-filter>
        <action android:name= "android.intent.action.BOOT_COMPLETED"/>
        <category android:name= "android.intent.category.HOME"/>
        </intent-filter>
        <!-- Receive the actual message -->
        <intent-filter>
        <action android:name= "com.google.android.c2dm.intent.RECEIVE" />
        <category android:name= "${tiapp.properties['id']}" />
        </intent-filter>
        <!-- Receive the registration id -->
        <intent-filter>
        <action android:name= "com.google.android.c2dm.intent.REGISTRATION" />
        <category android:name= "${tiapp.properties['id']}" />
        </intent-filter>
        </receiver>
        </application>
        </manifest>

> [WACOM.NOTE] 在上述程式碼中，您必須使用應用程式的封裝名稱 (模組識別碼) 來取代 *com.winwire.notificationhub* 文字的所有執行個體。

1.  在通知中心模組中，以滑鼠右鍵按一下 "src" 資料夾，移至 [新增]，然後選取 [資料夾]。提供如 com.google.android.gcm 的資料夾名稱。

> [WACOM.NOTE] 如果您在 [新增] 選項中看不到 [資料夾]，則請選取 [其他] 並展開 [一般]，然後選取 [資料夾]。

1.  現在，您可以在這裡下載 ".java" 檔案 (gcm.zip)，並將這些檔案複製到剛剛建立的資料夾 (com.google.android.gcm)。

2.  現在，尋找以模組識別碼命名的資料夾，然後將它展開。在該資料夾中，您可以看到 ".java" 檔案的清單。在這些檔案中，開啟以專案名稱+module.java 命名的檔案 (例如，如果您的專案名稱是 notificationhub，則看起來會像是 "NotificationhubModule.java")，然後在開頭處新增下列程式碼行。

        private static NotificationhubModule _THIS;
        private KrollFunction successCallback;
        private KrollFunction errorCallback;
        private KrollFunction messageCallback;

3.  在相同的檔案中，尋找建構函式，並使用下列程式碼來取代該建構函式。

        public NotificationhubModule() {
            super();
            _THIS = this;
        }

4.  在相同的檔案中，在結尾處新增下列程式碼行。

        @Kroll.method
        public void registerC2dm(HashMap options) {
            successCallback = (KrollFunction) options.get("success");
            errorCallback = (KrollFunction) options.get("error");
            messageCallback = (KrollFunction) options.get("callback");
            String registrationId = getRegistrationId();
            if (registrationId != null && registrationId.length() > 0) {
                sendSuccess(registrationId);
            } else {
                GCMRegistrar.register(TiApplication.getInstance(), getSenderId());
            }
        }
        @Kroll.method
        public void unregister() {
            GCMRegistrar.unregister(TiApplication.getInstance());
        }
        @Kroll.method
        public String getRegistrationId() {
            return GCMRegistrar.getRegistrationId(TiApplication.getInstance());
        }
        @Kroll.method
        public String getSenderId() {
            return TiApplication.getInstance().getAppProperties()
            .getString("com.winwire.notificationhub.sender_id", "");
        }

        public void sendSuccess(String registrationId) {
            if (successCallback != null) {
                HashMap data = new HashMap();
                data.put("registrationId", registrationId);
                successCallback.callAsync(getKrollObject(), data);
            }
        }

        public void sendError(String error) {
            if (errorCallback != null) {
                HashMap data = new HashMap();
                data.put("error", error);
                errorCallback.callAsync(getKrollObject(), data);
            }
        }

        public void sendMessage(HashMap messageData) {
            if (messageCallback != null) {
                HashMap data = new HashMap();
                data.put("data", messageData);
                messageCallback.call(getKrollObject(), data);
            }
        }
        public static NotificationhubModule getInstance() {
            return _THIS;
        }

5.  現在下載 module.zip，並將檔案複製到以模組識別碼作為其名稱的資料夾。

> [WACOM.NOTE] 在上述檔案中，您必須使用應用程式的封裝名稱 (模組識別碼) 來取代 *com.winwire.notificationhub* 文字的所有執行個體。並使用 ProjectName+Module (例如 "NotificationhubModule") 來取代 "NotificationhubModule"。

### 建置/封裝模組

依序選擇 [部署] \> [封裝] - [Android 模組]。您無法使用 Studio (也無法使用 BlackBerry NDK CLI 工具或 Momentics IDE) 來建置 BlackBerry 模組。

![][3]

您可能接著會選擇為所有專案或特定專案部署此模組。這符合如 [Using Titanium Modules][Using Titanium Modules] 中所述的安裝規則，但還是做個總結：

-   若是所有專案：則會將模組 .zip 檔案拖放到 Titanium SDK 安裝位置的根目錄。

-   若是特定專案：則會將模組 .zip 檔案拖放到專案的根目錄。

## <a name="configure"></a>設定行動服務來傳送推播要求

[WACOM.INCLUDE [mobile-services-apns-configure-push](../includes/mobile-services-apns-configure-push.md)]

1.  輸入在上一個程序中從 GCM 取得的 [API Key] 值，然後按一下 [儲存]。

    ![][4]

您的行動服務現在已設定成與 APNS 和 GCM 搭配使用。

## <a name="add-push"></a>新增推播通知至應用程式

1.  在 tiapp.xml 中，選取 tiapp.xml 索引標籤 (您可以在底部的 [概觀] 索引標籤旁邊找到此索引標籤)，然後尋找 `<android>` 標籤。在此標籤下面新增下列程式碼：

        <modules>
            <module platform="android">ModuleId</module>
        </modules>
        <property name="ApplicationId.sender_id" type="string">Provide your GCM sender ID</property>
        <property name="ApplicationId.icon type="int">2130837504</property>
        <property name="ApplicationId.component" type="string">ApplicationId/ApplicationId.AppNameActivity</property>

> [WACOM.NOTE] 在上方的程式碼中，您必須取代 **ModuleId**、**ApplicationId**。模組識別碼是指您在建立 GCM 模組時所收到的識別碼，而應用程式識別碼是指建立專案時所輸入的識別碼。另請確定 **ModuleId** 和 **ApplicationId** 相同。您還需要變更 **ApplicationId.AppNameActivity**。它看來應像這樣：com.winwire.notificationhub/ com.winwire.notificationhub.NotificationhubActivity。

1.  複製您稍早建立的 GCM 模組，並將它放入以下位置。

    <table>
    <tr>
    <td>
    OSX

    </td>
    <td>
    /Library/Application Support/Titanium or ~/Library/Application Support/Titanium

    </td>
    </tr>
    <td>
    Windows 7

    </td>
    <td>
    C:\\Users\\username\\AppData\\Roaming (或 C:\\ProgramData\\Titanium on Titanium Studio 1.0.1 and earlier)

    </td>
    </tr>
    <td>
    Windows XP

    </td>
    <td>
    C:\\Documents and Settings\\username\\Application Data (或 C:\\Documents and Settings\\All Users\\Application Data\\Titanium on Titanium Studio 1.0.1 and earlier)

    </td>
    </tr>
    <td>
    Linux

    </td>
    <td>
    ~/.titanium

    </td>
    </tr>
    </tr>
    </table>

> [WACOM.NOTE] 在 Mac OS 中，Library 是隱藏資料夾。若要使其可見，您必須執行下列命令，然後重新啟動 Finder： `**defaults write com.apple.finder AppleShowAllFiles TRUE**`

1.  在 Appcelerator Titanium Studio 中，開啟 index.js 檔案，並在結尾處新增下列程式碼。此程式碼將會為您的裝置註冊推播通知。

            Alloy.Globals.tempRegId = '';
                if (OS_ANDROID) {
                var gcm = require('com.winwire.notificationhub');
            gcm.registerC2dm({
            success : function(e) {
            var regId = e.registrationId;
            Alloy.Globals.tempRegId = regId;
            },
            error : function(e) {
            alert("Error during registration : " + e.error);
            var message;
            if (e.error == "ACCOUNT_MISSING") {
            message = "No Google account found; you will need to add on in order to activate notifications";
            }
            Titanium.UI.createAlertDialog({
            title : 'Push Notification Setup',
            message : message,
            buttonNames : ['OK']
            }).show();
            },
            callback : function(e)// called when a push notification is received
            {
            var data = JSON.stringify(e.data);
            var intent = Ti.Android.createIntent({
            action : Ti.Android.ACTION_MAIN,
            className : 'com.winwire.notificationhub. WindowsAzureActivity',
            flags : Ti.Android.FLAG_ACTIVITY_RESET_TASK_IF_NEEDED | Ti.Android.FLAG_ACTIVITY_SINGLE_TOP
            });
            intent.addCategory(Titanium.Android.CATEGORY_LAUNCHER);
            var NotificationClickAction = Ti.Android.createPendingIntent({
            activity : Ti.Android.currentActivity,
            intent : intent,
            flags : Ti.Android.FLAG_UPDATE_CURRENT,
            type : Ti.Android.PENDING_INTENT_FOR_ACTIVITY
            });
            var NotificationMembers = {
            contentTitle : 'Notification Hub Demo',
            contentText : e.data.message,
            defaults : Titanium.Android.NotificationManager.DEFAULT_SOUND,
            tickerText : 'Notification Hub Demo',
            icon : Ti.App.Android.R.drawable.appicon,
            when : new Date(),
            flags : Ti.Android.FLAG_AUTO_CANCEL,
            contentIntent : NotificationClickAction
            };
            Ti.Android.NotificationManager.notify(1, .Android.createNotification(NotificationMembers));
            var toast = Titanium.UI.createNotification({
            duration : 2000,
            message : e.data.message
            });
            toast.show();
            alert(e.data.message);
            }
            });
            } else  if (OS_IOS) {
            Titanium.Network.registerForPushNotifications({
            types : [Titanium.Network.NOTIFICATION_TYPE_BADGE, .Network.NOTIFICATION_TYPE_ALERT, .Network.NOTIFICATION_TYPE_SOUND],
            success : function(e) {
            deviceToken = e.deviceToken;
            Alloy.Globals.tempRegId = deviceToken;
            },
            error : function(e) {
            Ti.API.info("Error during registration: " + e.error);
            },
            callback : function(e) {
            var data = e.data.inAppMessage;
            if (data != null && data != '') {
            alert(data);
            }
            }
            });
            }

2.  現在請開啟 TableData.js 檔案，並在函數 **addOrUpdateDataFromAndroid** 中找到下列程式碼行

        var request = {
            'text' : alertTextField.getValue(),
            'complete' : false
        };

    請僅取代 ELSE 條件 (適用於插入新項目) 中的上述程式碼。

3.  若是 Android，請使用下列程式碼來取代上述程式碼：

           var request = {
            'text' :alertTextField.getValue(),
            'complete' :false,
            'handle' :Alloy.Globals.tempRegId
    	 };

4.  現在，在函數 **updateOrAddData** 中尋找下列程式碼行

           var request = {
            'text' : alertTextField.getValue(),
            'complete' : false
        };

    請僅取代 ELSE 條件 (適用於插入新項目) 中的上述程式碼。

5.  若是 iOS，請使用下列程式碼來取代上述程式碼：

           var request = {
            'text' :alertTextField.getValue(),
            'complete' :false,
            'deviceToken' :Alloy.Globals.tempRegId
        };

您在 iOS 和 Android 平台的應用程式現已更新為支援推播通知。

## <a name="update-scripts"></a>在管理入口網站中更新已註冊的插入指令碼

1.  在管理入口網站中，按一下 [資料] 索引標籤，然後按一下 [TodoItem] 資料表。

    ![][5]

2.  在 [TodoItem] 中，按一下 [指令碼] 索引標籤，然後選取 [插入]。

    ![][6]

    這會顯示 [TodoItem] 資料表中發生插入時所叫用的函數。

3.  使用下列程式碼來取代 insert 函數，然後按一下 **[儲存]**：

**若是 iOS：**

        function insert(item, user, request) { 
            request.execute();
            // Set timeout to delay the notification, to provide time for the  
            // app to be closed on the device to demonstrate toast notifications 
            setTimeout(function() { 
                push.apns.send(item.deviceToken, { 
                    alert: "Toast: " + item.text, 
                    payload: { 
                        inAppMessage: "Hey, a new item arrived: '" + item.text + "'" 
                    }
                 }); 
            }, 2500);
        }

    > [WACOM.NOTE] This script delays sending the notification to give you time to close the app to receive a push notification.  

**若是 Android：**

          function insert(item, user, request) {
            request.execute({ 
                success: function() {  
                    // Write to the response and then send the notification in the background 
                    request.respond();  
                    push.gcm.send(item.handle, item.text, {
                        success: function(response) { 
                            console.log('Push notification sent: ', response);
                        }, error: function(error)   { 
                            console.log('Error sending push notification: ', error);      
                            }      
                   }); 
                }  
          });  
        }

如此即會註冊新的 insert 指令碼，該指令碼會使用[行動服務推播物件][行動服務推播物件]將推播通知 (插入的文字) 傳送給插入要求中所提供的裝置。

<!-- Images. -->  
  [Windows 市集 C#]: /zh-tw/documentation/articles/mobile-services-windows-store-dotnet-get-started-push "Windows 市集 C#"
  [Windows 市集 JavaScript]: /zh-tw/documentation/articles/mobile-services-windows-store-javascript-get-started-push "Windows 市集 JavaScript"
  [Windows Phone]: /zh-tw/documentation/articles/mobile-services-windows-phone-get-started-push "Windows Phone"
  [iOS]: /zh-tw/documentation/articles/mobile-services-ios-get-started-push "iOS"
  [Android]: /zh-tw/documentation/articles/mobile-services-android-get-started-push "Android"
  [Appcelerator]: /zh-tw/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push "Appcelerator"
<!-- Anchors. -->
  [產生憑證簽署要求]: #certificates
  [註冊您的應用程式並啟用推播通知]: #register
  [建立應用程式的佈建設定檔]: #profile
  [啟用 Google 雲端通訊]: #register-gcm
  [建立 Titanium 的 GCM 模組]: #gcm-module
  [設定行動服務]: #configure
  [新增推播通知至應用程式]: #add-push
  [更新指令碼來傳送推播通知]: #update-scripts
  [插入資料以接收通知]: #test
<!-- URLs. -->
  [開始使用行動服務]: /zh-tw/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started
  [啟用 Apple 推播通知]: ../includes/enable-apple-push-notifications.md
  [accounts.google.com]: http://go.microsoft.com/fwlink/p/?LinkId=268302
  [啟用 GCM]: ../includes/mobile-services-enable-Google-cloud-messaging.md
  [安裝 Java 開發工具]: http://docs.appcelerator.com/titanium/latest/#!/guide/Installing_the_Java_Development_Tools
  []: http://developer.android.com/sdk/ndk/index.html
  []: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push/image0011.png
  [1]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push/image0031.png
  [2]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push/image0041.png
  [3]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push/image0061.png
  [Using Titanium Modules]: http://docs.appcelerator.com/titanium/latest/#!/guide/Using_Titanium_Modules
  [mobile-services-apns-configure-push]: ../includes/mobile-services-apns-configure-push.md
  [4]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push/image062.png
  [5]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push/image064.png
  [6]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push/image066.png
  [行動服務推播物件]: http://go.microsoft.com/fwlink/p/?linkid=272333&clcid=0x409
