<properties title="Azure Notification Hubs Notify Users" pageTitle="Azure Notification Hubs Notify Users" metaKeywords="Azure push notifications, Azure notification hubs" description="Learn how to send secure push notifications in Azure. Code samples written in C# using the .NET API." documentationCenter="Mobile" metaCanonical="" disqusComments="1" umbracoNaviHide="0" authors="elioda" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-android" ms.devlang="java" ms.topic="article" ms.date="01/01/1900" ms.author="elioda"></tags>

# Azure 通知中心通知使用者

<div class="dev-center-tutorial-selector sublanding"> 
<a href="/zh-tw/documentation/articles/notification-hubs-windows-dotnet-notify-users/" title="Windows Universal">Windows Universal</a><a href="/zh-tw/documentation/articles/notification-hubs-aspnet-backend-ios-notify-users/" title="iOS">iOS</a>
<a href="/zh-tw/documentation/articles/notification-hubs-aspnet-backend-android-notify-users/" title="Android" class="current">Android</a>
</div>

Azure 中的推播通知支援可讓您存取易於使用、多重平台的大規模推播基礎結構，而大幅簡化消費者和企業應用程式在行動平台上的推播通知實作。本教學課程將示範如何使用 Azure 通知中心，來將推播通知傳送到特定裝置上的特定應用程式使用者。ASP.NET WebAPI 後端可用來驗證用戶端並產生通知，如指引主題[從您的應用程式後端註冊][從您的應用程式後端註冊]中所示。本教學課程會以您在**開始使用通知中心**教學課程中所建立的通知中心為基礎。

> [AZURE.NOTE] 本教學課程假設您已建立並設定通知中心，如[開始使用通知中心 (Android)][開始使用通知中心 (Android)] 中所述。

[WACOM.INCLUDE [notification-hubs-aspnet-backend-notifyusers](../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## 建立 Android 專案

下一個步驟說明如何建立 Android 應用程式。

1.  依照[開始使用通知中心 (Android)][開始使用通知中心 (Android)] 教學課程來建立並設定您的應用程式，以接收來自 GCM 的推播通知。

2.  開啟您的 res/layout/activity\_main.xml 檔案，並替換成下列內容：

        <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
            xmlns:tools="http://schemas.android.com/tools"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:paddingBottom="@dimen/activity_vertical_margin"
            android:paddingLeft="@dimen/activity_horizontal_margin"
            android:paddingRight="@dimen/activity_horizontal_margin"
            android:paddingTop="@dimen/activity_vertical_margin"
            tools:context="com.example.notifyusers.MainActivity"
            android:orientation="vertical">
            <EditText
                android:id="@+id/usernameText"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:ems="10"
                android:hint="@string/usernameHint" >
            </EditText>
            <EditText
                android:id="@+id/passwordText"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:ems="10"
                android:hint="@string/passwordHint"
                android:inputType="textPassword" />
            <Button
                android:id="@+id/buttonLogin"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="@string/loginButton"
                android:onClick="login" />
            <Button
                android:id="@+id/buttonSend"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="@string/sendButton"
                android:onClick="sendPush" />
        </LinearLayout>

3.  開啟您的 res/values/strings.xml 檔案，並新增下列程式碼行：

        <string name="usernameHint">Username</string>
        <string name="passwordHint">Password</string>
        <string name="loginButton">1. Log in</string>
        <string name="sendButton">2. Send push</string>

    您的 main\_activity.xml 圖形配置看起來應該如下所示：

    ![][]

4.  現在，在與 **MainActivity** 類別相同的套件中建立 **RegisterClient** 類別。請確定使用在上一節中所取得的後端端點來取代 `{backend endpoint}`。

        import java.io.IOException;
        import java.io.UnsupportedEncodingException;
        import java.util.Set;

        import org.apache.http.HttpResponse;
        import org.apache.http.HttpStatus;
        import org.apache.http.client.ClientProtocolException;
        import org.apache.http.client.HttpClient;
        import org.apache.http.client.methods.HttpPost;
        import org.apache.http.client.methods.HttpPut;
        import org.apache.http.client.methods.HttpUriRequest;
        import org.apache.http.entity.StringEntity;
        import org.apache.http.impl.client.DefaultHttpClient;
        import org.apache.http.util.EntityUtils;
        import org.json.JSONArray;
        import org.json.JSONException;
        import org.json.JSONObject;

        import android.content.Context;
        import android.content.SharedPreferences;
        import android.util.Log;

        public class RegisterClient {
            private static final String PREFS_NAME = "ANHSettings";
            private static final String REGID_SETTING_NAME = "ANHRegistrationId";
            private static final String BACKEND_ENDPOINT = "{backend endpoint}/api/register";
            SharedPreferences settings;
            protected HttpClient httpClient;
            private String authorizationHeader;

            public RegisterClient(Context context) {
                super();
                this.settings = context.getSharedPreferences(PREFS_NAME, 0);
                httpClient =  new DefaultHttpClient();
            }

            public String getAuthorizationHeader() {
                return authorizationHeader;
            }

            public void setAuthorizationHeader(String authorizationHeader) {
                this.authorizationHeader = authorizationHeader;
            }

            public void register(String handle, Set<String> tags) throws ClientProtocolException, IOException, JSONException {
                String registrationId = retrieveRegistrationIdOrRequestNewOne(handle);

                JSONObject deviceInfo = new JSONObject();
                deviceInfo.put("Platform", "gcm");
                deviceInfo.put("Handle", handle);
                deviceInfo.put("Tags", new JSONArray(tags));

                int statusCode = upsertRegistration(registrationId, deviceInfo);

                if (statusCode == HttpStatus.SC_OK) {
                    return;
                } else if (statusCode == HttpStatus.SC_GONE){
                    settings.edit().remove(REGID_SETTING_NAME).commit();
                    registrationId = retrieveRegistrationIdOrRequestNewOne(handle);
                    statusCode = upsertRegistration(registrationId, deviceInfo);
                    if (statusCode != HttpStatus.SC_OK) {
                        Log.e("RegisterClient", "Error upserting registration: " + statusCode);
                        throw new RuntimeException("Error upserting registration");
                    }
                } else {
                    Log.e("RegisterClient", "Error upserting registration: " + statusCode);
                    throw new RuntimeException("Error upserting registration");
                }
            }

            private int upsertRegistration(String registrationId, JSONObject deviceInfo)
                    throws UnsupportedEncodingException, IOException,
                    ClientProtocolException {
                HttpPut request = new HttpPut(BACKEND_ENDPOINT+"/"+registrationId);
                request.setEntity(new StringEntity(deviceInfo.toString()));
                request.addHeader("Authorization", "Basic "+authorizationHeader);
                request.addHeader("Content-Type", "application/json");
                HttpResponse response = httpClient.execute(request);
                int statusCode = response.getStatusLine().getStatusCode();
                return statusCode;
            }

            private String retrieveRegistrationIdOrRequestNewOne(String handle) throws ClientProtocolException, IOException {
                if (settings.contains(REGID_SETTING_NAME))
                    return settings.getString(REGID_SETTING_NAME, null);

                HttpUriRequest request = new HttpPost(BACKEND_ENDPOINT+"?handle="+handle);
                request.addHeader("Authorization", "Basic "+authorizationHeader);
                HttpResponse response = httpClient.execute(request);
                if (response.getStatusLine().getStatusCode() != HttpStatus.SC_OK) {
                    Log.e("RegisterClient", "Error creating registrationId: " + response.getStatusLine().getStatusCode());
                    throw new RuntimeException("Error creating Notification Hubs registrationId");
                }
                String registrationId = EntityUtils.toString(response.getEntity());
                registrationId = registrationId.substring(1, registrationId.length()-1);

                settings.edit().putString(REGID_SETTING_NAME, registrationId).commit();

                return registrationId;
            }
        }

    為註冊推播通知，此元件會實作連絡應用程式後端所需的 REST 呼叫。它也會在本機儲存通知中心所建立的 *registrationIds*，如[從您的應用程式後端註冊][從您的應用程式後端註冊]中的詳細說明。請注意，當您按一下 **[Log in and register]** 按鈕時，系統便會使用儲存在本機儲存體中的授權權杖。

5.  在您的 **MainActivity** 類別中，將 **NotificationHub** 的私用欄位移除，並新增 **RegisterClient** 欄位：

        //private NotificationHub hub;
        private RegisterClient registerClient;

6.  接著，在 **onCreate** 方法中，將 **hub** 欄位的初始設定和 **registerWithNotificationHubs** 方法移除。然後加入下列可初始化 **RegisterClient** 類別執行個體的程式碼行。此方法應包含下列程式碼行：

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);

            NotificationsManager.handleNotifications(this, SENDER_ID,
                    MyHandler.class);
            gcm = GoogleCloudMessaging.getInstance(this);

            registerClient = new RegisterClient(this);

            setContentView(R.layout.activity_main);
        }

7.  接著，新增下列方法，並確定使用在上一節中所取得的後端端點來取代 `{backend endpoint}`。

        @Override
        protected void onStart() {
            super.onStart();
            Button sendPush = (Button) findViewById(R.id.buttonSend);
            sendPush.setEnabled(false);
        }

        public void login(View view) throws UnsupportedEncodingException {
            this.registerClient.setAuthorizationHeader(getAuthorizationHeader());

            final Context context = this;
            new AsyncTask<Object, Object, Object>() {
                @Override
                protected Object doInBackground(Object... params) {
                    try {
                        String regid = gcm.register(SENDER_ID);
                        registerClient.register(regid, new HashSet<String>());
                    } catch (Exception e) {
                        Log.e("MainActivity", "Failed to register - " + e.getMessage());
                        return e;
                    }
                    return null;
                }

                protected void onPostExecute(Object result) {
                    Button sendPush = (Button) findViewById(R.id.buttonSend);
                    sendPush.setEnabled(true);
                    Toast.makeText(context, "Logged in and registered.",
                            Toast.LENGTH_LONG).show();
                }
            }.execute(null, null, null);
        }

        public void sendPush(View view) throws ClientProtocolException, IOException {
            new AsyncTask<Object, Object, Object>() {
                @Override
                protected Object doInBackground(Object... params) {
                    try {
                        HttpUriRequest request = new HttpPost("{backend endpoint}/api/notifications");
                        request.addHeader("Authorization", "Basic "+getAuthorizationHeader());
                        HttpResponse response = new DefaultHttpClient().execute(request);
                        if (response.getStatusLine().getStatusCode() != HttpStatus.SC_OK) {
                            Log.e("MainActivity", "Error sending notification" + response.getStatusLine().getStatusCode());
                            throw new RuntimeException("Error sending notification");
                        }
                    } catch (Exception e) {
                        Log.e("MainActivity", "Failed to send notification - " + e.getMessage());
                        return e;
                    }
                    return null;
                }
            }.execute(null, null, null);
        }

        private String getAuthorizationHeader() throws UnsupportedEncodingException {
            EditText username = (EditText) findViewById(R.id.usernameText);
            EditText password = (EditText) findViewById(R.id.passwordText);
            String basicAuthHeader = username.getText().toString()+":"+password.getText().toString();
            basicAuthHeader = Base64.encodeToString(basicAuthHeader.getBytes("UTF-8"), Base64.NO_WRAP);
            return basicAuthHeader;
        }

    **Log in** 的回呼會根據輸入使用者名稱和密碼 (請注意，這代表驗證結構描述使用的任何權杖) 產生基本驗證權杖，然後使用 `RegisterClient` 呼叫後端。**Send push** 的回呼會呼叫後端，以觸發安全通知到這位使用者的所有裝置。

## 執行應用程式

若要執行應用程式，請執行下列動作：

1.  確定 Azure 中已部署 **AppBackend**。如果使用 Visual Studio，則執行 **AppBackend** Web API 應用程式。[ASP.NET Web] 頁面便會隨即顯示。

2.  在 Eclipse 中，在實體 Android 裝置或模擬器上執行此應用程式。

3.  在 Android 應用程式 UI 中，輸入使用者名稱和密碼。這些可以是任何字串，但必須是相同值。

4.  在 Android 應用程式 UI 中，按一下 **[登入]**。然後按一下 **[傳送推播]**。

  [Windows Universal]: /zh-tw/documentation/articles/notification-hubs-windows-dotnet-notify-users/ "Windows Universal"
  [iOS]: /zh-tw/documentation/articles/notification-hubs-aspnet-backend-ios-notify-users/ "iOS"
  [Android]: /zh-tw/documentation/articles/notification-hubs-aspnet-backend-android-notify-users/ "Android"
  [從您的應用程式後端註冊]: http://msdn.microsoft.com/zh-tw/library/dn743807.aspx
  [開始使用通知中心 (Android)]: /zh-tw/documentation/articles/notification-hubs-android-get-started/
  [notification-hubs-aspnet-backend-notifyusers]: ../includes/notification-hubs-aspnet-backend-notifyusers.md
  []: ./media/notification-hubs-aspnet-backend-android-notify-users/android-notify-users1.PNG
