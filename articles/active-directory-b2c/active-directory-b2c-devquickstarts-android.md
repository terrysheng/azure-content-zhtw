<properties pageTitle="Azure AD B2C 預覽：從 Android 應用程式呼叫 Web API | Microsoft Azure" description="本文將示範如何建立使用 OAuth 2.0 持有人權杖呼叫 node.js Web API 的 Android「待辦事項清單」應用程式。Android 應用程式與 Web API 將會使用 Azure AD B2C 來管理使用者身分識別與驗證使用者。" services="active-directory-b2c" documentationCenter="andriod" authors="brandwe" manager="msmbaldwin" editor=""/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="02/17/2016"
	ms.author="brandwe"/>

# Azure AD B2C 預覽：從 Android 應用程式呼叫 Web API

有了 Azure AD B2C，您可以將強大的自助式身分識別管理功能加入 Android 應用程式和 Web API，只要幾個簡短步驟即可完成。本文將示範如何建立使用 OAuth 2.0 持有人權杖呼叫 node.js Web API 的 Android「待辦事項清單」應用程式。Android 應用程式與 Web API 將會使用 Azure AD B2C 來管理使用者身分識別與驗證使用者。

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

 	
> [AZURE.NOTE]
	使用此快速入門的先決條件為，您必須擁有受 Azure AD (含 B2C) 保護的 Web API，才可完整運作。我們已經為 .NET 與 node.js 建立一個 Web API 供您使用。本逐步解說假設您已設定 node.js Web API 範例。請參閱 [Node.js 的 Azure AD B2C Web API 教學課程](active-directory-b2c-devquickstarts-api-node.md)。

 
> [AZURE.NOTE]
	本文不會說明如何使用 Azure AD B2C 實作登入、註冊和設定檔管理。而會著重在如何在使用者已通過驗證後呼叫 Web API。您應該先從 [.NET Web 應用程式使用者入門教學課程](active-directory-b2c-devquickstarts-web-dotnet.md)開始 (如果還沒有進行)，以了解 Azure AD B2C 的基本概念。


對於需要存取受保護資源的 Android 用戶端，Azure AD 提供 Active Directory 驗證程式庫 (ADAL)。ADAL 存在的唯一目的是為了讓您的應用程式輕鬆取得存取權杖。為了示範究竟多麼簡單，我們將建置一個執行下列動作的 Android 待辦事項清單應用程式：

-	取得存取權杖來使用 [OAuth 2.0 驗證通訊協定](https://msdn.microsoft.com/library/azure/dn645545.aspx)呼叫待辦事項清單 API。
-	取得使用者的待辦事項清單
-	將使用者登出。



### 步驟 1：取得 Azure AD B2C 目錄

您必須先建立目錄或租用戶，才可使用 Azure AD B2C。目錄為所有使用者、應用程式、群組等項目的容器。如果您尚未擁有目錄，請先[建立 B2C 目錄](active-directory-b2c-get-started.md)，再繼續下一個步驟。

### 步驟 2：建立應用程式

您現在需要在 B2C 目錄中建立應用程式，以提供一些必要資訊給 Azure AD，讓它與應用程式安全地通訊。在此案例中，因為應用程式與 Web API 會組成一個邏輯應用程式，所以將由單一**應用程式識別碼**代表。若要建立應用程式，請遵循[這些指示](active-directory-b2c-app-registration.md)。請務必

- 在應用程式中加入 **Web 應用程式/Web API**
- 輸入 `urn:ietf:wg:oauth:2.0:oob` 作為**回覆 URL**。此 URL 為此程式碼範例的預設 URL。
- 為您的應用程式建立**應用程式密碼**，並複製起來。稍後您將會用到此資訊。請注意，這個值在使用之前必須先變成 [XML 逸出](https://www.w3.org/TR/2006/REC-xml11-20060816/#dt-escape)字元。
- 複製指派給您應用程式的**應用程式識別碼**。稍後您也會用到此資訊。

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

### 步驟 3：建立您的原則

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

在 Azure AD B2C 中，每個使用者經驗皆是由某個[**原則**](active-directory-b2c-reference-policies.md)定義的。此應用程式包含三種身分識別體驗 - 註冊、登入，以及使用 Facebook 登入。如[原則參考文章](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy)所述，您必須為每個類型建立一個原則。建立您的三個原則時，請務必：

- 在註冊原則中，選擇 [**顯示名稱**] 和其他一些註冊屬性。
- 在每個原則中選擇 [**顯示名稱**] 和 [**物件識別碼**] 應用程式宣告。您也可以選擇其他宣告。
- 建立每個原則後，請複製原則的**名稱**。其前置詞應該為 `b2c_1_`。稍後您將需要這些原則名稱。 

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

當您成功建立三個原則後，就可以開始建置您的應用程式。

請注意，本文不會說明如何使用您剛才建立的原則。如果您想要了解 Azure AD B2C 中的原則如何運作，應該從 [.NET Web 應用程式使用者入門教學課程](active-directory-b2c-devquickstarts-web-dotnet.md)開始。

### 步驟 4：下載程式碼

本教學課程的程式碼保留在 [GitHub](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android) 上。若要遵循指示建立範例，您可以[下載 .zip 格式的基本架構專案](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android/archive/skeleton.zip)，或複製基本架構：

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-NativeClient-Android.git
```

> [AZURE.NOTE] **若要完成此教學課程，您必須下載基本架構。** 由於在 Android 上實作完整運作的應用程式有其複雜性，因此**基本架構**所包含的 UX 程式碼將於您完成下方教學課程之後執行。如此將能節省開發人員的時間。UX 程式碼與加入 B2C 至 Android 應用程式的主題無關。

完整的應用程式也提供 [.zip 格式](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android/archive/complete.zip)；您也可以在相同儲存機制的 `complete` 分支取得。


若要使用 Maven 來建置，您可以使用最上層的 pom.xml


  * 請依照 [Prerequests 一節的步驟，為 Android 設定您的 Maven](https://github.com/MSOpenTech/azure-activedirectory-library-for-android/wiki/Setting-up-maven-environment-for-Android)
  * 使用 SDK 21 設定模擬器
  * 移至您已複製儲存機制的根資料夾
  * 執行命令：mvn clean install
  * 將目錄切換至快速入門範例：cd samples\\hello
  * 執行命令：mvn android:deploy android:run
  * 您應該會看到應用程式正在啟動
  * 輸入測試使用者認證來測試一下！

除了 aar 封裝，也會提交 jar 封裝。

### 步驟 5：下載 Android ADAL，並將它加入您的 Android Studio 工作區

我們提供多個選項，讓您輕鬆地在 Android 專案中使用此程式庫：

* 您可以使用原始程式碼將此程式庫匯入到 Eclipse，並連結至您的應用程式。
* 如果是使用 Android Studio，您可以使用 *aar* 封裝格式，並參考二進位檔。



####選項 1：透過 Gradle 取得二進位檔 (建議選項)

您可以從 Maven 中央儲存機制取得二進位檔。在 AndroidStudio 中，您可將 AAR 封裝加入您的專案，如下所示 (範例：在 `build.gradle` 中)：

```gradle
repositories {
    mavenCentral()
    flatDir {
        dirs 'libs'
    }
    maven {
        url "YourLocalMavenRepoPath\\.m2\\repository"
    }
}
dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    compile('com.microsoft.aad:adal:2.0.1-alpha') {
        exclude group: 'com.android.support'
    } // Recent version is 2.0.1-alpha
}
```

####選項 2：透過 Maven 取得 aar

如果您在 Eclipse 中使用 m2e 外掛程式，可以在 `pom.xml` 檔案中指定相依性：

```xml
<dependency>
    <groupId>com.microsoft.aad</groupId>
    <artifactId>adal</artifactId>
    <version>2.0.1-alpha</version>
    <type>aar</type>
</dependency>
```

####選項 3：透過 Git 取得原始檔 (最後手段)

若要透過 git 取得 SDK 的原始程式碼，只要輸入：

    git clone git@github.com:AzureAD/azure-activedirectory-library-for-android.git
    cd ./azure-activedirectory-library-for-android/src
    
    use the branch "convergence"


### 步驟 6：設定組態檔

我們將使用您在 B2C 入口網站中如上設定的組態來設定 Android 專案。

開啟 `helpes/Constants.java` 並填入下列各項的值：

```

package com.microsoft.aad.taskapplication.helpers;

import com.microsoft.aad.adal.AuthenticationResult;

public class Constants {

    public static final String SDK_VERSION = "1.0";
    public static final String UTF8_ENCODING = "UTF-8";
    public static final String HEADER_AUTHORIZATION = "Authorization";
    public static final String HEADER_AUTHORIZATION_VALUE_PREFIX = "Bearer ";

    // -------------------------------AAD
    // PARAMETERS----------------------------------
    public static String AUTHORITY_URL = "https://login.microsoftonline.com/hypercubeb2c.onmicrosoft.com/";
    public static String CLIENT_ID = "<your application id>";
    public static String[] SCOPES = {"<your application id>"};
    public static String[] ADDITIONAL_SCOPES = {""};
    public static String REDIRECT_URL = "<redirect uri>";
    public static String CORRELATION_ID = "";
    public static String USER_HINT = "";
    public static String EXTRA_QP = "";
    public static String FB_POLICY = "B2C_1_<your policy>";
    public static String EMAIL_SIGNIN_POLICY = "B2C_1_<your policy>";
    public static String EMAIL_SIGNUP_POLICY = "B2C_1_<your policy>";
    public static boolean FULL_SCREEN = true;
    public static AuthenticationResult CURRENT_RESULT = null;
    // Endpoint we are targeting for the deployed WebAPI service
    public static String SERVICE_URL = "http://localhost:3000/tasks";

    // ------------------------------------------------------------------------------------------

    static final String TABLE_WORKITEM = "WorkItem";
    public static final String SHARED_PREFERENCE_NAME = "com.example.com.test.settings";


}


```
**SCOPES** - 是我們傳給伺服器的範圍，而在使用者登入時，我們想要向伺服器要求此範圍。在 B2C 預覽中，我們傳遞 client\_id。不過，這在未來將寫成 scopes。屆時會更新本文件。**ADDITIONAL\_SCOPES** - 這些是您可能想要用於應用程式的其他範圍。這將在未來使用。**CLIENT\_ID** - 您從入口網站取得的應用程式 ID。**REDIRECT\_URL** - 我們預期會回傳權杖的重新導向。**EXTRA\_QP** - 您想要以 URL 編碼格式傳遞給伺服器的任何額外參數。**FB\_POLICY** - 您叫用的原則。本逐步解說的後續重要部分。**EMAIL\_SIGNIN\_POLICY** - 您叫用的原則。本逐步解說的後續重要部分。**EMAIL\_SIGNUP\_POLICY** - 您叫用的原則。本逐步解說的後續重要部分。

### 步驟 7：將 Android ADAL 參考加入至您的專案


> [AZURE.NOTE]	ADAL Android 使用以意圖為基礎的模型叫用驗證。意圖「舖陳」於應用程式上運作。這個整個範例 (確實全部使用 ADAL for Android) 就是在管理意圖並傳遞它們之間的資訊。


第一件要做的事是將應用程式的配置告知 Android，包括我們想要使用的 Intents()。稍後我會詳細說明這些意圖。

更新專案的 AndroidManifest.xml 檔案來包含所有意圖：

```
   <?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.microsoft.aad.taskapplication"
    android:versionCode="1"
    android:versionName="1.0" >

    <uses-sdk
        android:minSdkVersion="11"
        android:targetSdkVersion="19" />

    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />

    <application
        android:allowBackup="true"
        android:icon="@drawable/ic_launcher"
        android:label="@string/app_name"
        android:theme="@style/AppTheme" >
        <activity
            android:name="com.microsoft.aad.adal.AuthenticationActivity"
            android:configChanges="orientation|keyboardHidden|screenSize"
            android:exported="true"
            android:label="@string/title_login_hello_app" >
        </activity>
        <activity
            android:name="com.microsoft.aad.taskapplication.LoginActivity"
            android:configChanges="orientation|keyboardHidden|screenSize"
            android:label="@string/app_name" >
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
        <activity
            android:name="com.microsoft.aad.taskapplication.UsersListActivity"
            android:label="@string/title_activity_feed" >
        </activity>
        <activity
            android:name="com.microsoft.aad.taskapplication.SettingsActivity"
            android:label="@string/title_activity_settings" >
        </activity>
        <activity
            android:name="com.microsoft.aad.taskapplication.AddTaskActivity"
            android:label="@string/title_activity_settings" >
        </activity>
        <activity
            android:name="com.microsoft.aad.taskapplication.ToDoActivity"
            android:label="@string/app_name" >
        </activity>
    </application>

</manifest>    
```

如您所見，我們定義將會使用的 5 個活動。

**AuthenticationActivity** - 來自 ADAL 並提供登入 Web 檢視。

**LoginActivity** - 顯示登入原則和每個原則的按鈕。

**SettingsActivity** - 可讓我們在執行階段變更應用程式設定。

**AddTaskActivity** - 可讓我們將工作加入至 Azure AD 所保護的 REST API。

**ToDoActivity** - 可顯示工作的主要活動。



### 步驟 8：建立登入活動

讓我們來建立主要活動，並將其命名為 `LoginActivity`。

建立名為 `LoginActivity.java` 的檔案

我們必須初始化活動，並加入一些可控制 UI 的按鈕。此外，若您先前撰寫過 Android 程式碼，此步驟對您而言應相當簡單且熟悉：

```
import android.app.Activity;
import android.app.ProgressDialog;
import android.content.Intent;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.Toast;

import com.microsoft.aad.adal.AuthenticationContext;
import com.microsoft.aad.taskapplication.helpers.Constants;
import com.microsoft.aad.taskapplication.helpers.WorkItemAdapter;

/**
 * Created by brwerner on 9/17/15.
 */
public class LoginActivity extends Activity {

    private final static String TAG = "ToDoActivity";

    private AuthenticationContext mAuthContext;

    /**
     * Adapter to sync the items list with the view
     */
    private WorkItemAdapter mAdapter = null;

    /**
     * Show this dialog when activity first launches to check if user has login
     * or not.
     */
    private ProgressDialog mLoginProgressDialog;

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_signin);
        Toast.makeText(getApplicationContext(), TAG + "LifeCycle: OnCreate", Toast.LENGTH_SHORT)
                .show();

        Button button = (Button) findViewById(R.id.signin_facebook);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(LoginActivity.this, ToDoActivity.class);
                intent.putExtra("thePolicy", Constants.FB_POLICY);
                startActivity(intent);

            }
        });

        button = (Button) findViewById(R.id.signin_email);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(LoginActivity.this, ToDoActivity.class);
                intent.putExtra("thePolicy", Constants.EMAIL_SIGNIN_POLICY);
                startActivity(intent);

            }
        });

        button = (Button) findViewById(R.id.signup_email);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(LoginActivity.this, ToDoActivity.class);
                intent.putExtra("thePolicy", Constants.EMAIL_SIGNUP_POLICY);
                startActivity(intent);

            }
        });

    }

}


```
我們所做的是建立按鈕，以我們自己的活動當做參考及一個額外的參數，透過這些按鈕呼叫我們的 ToDoActivity 意圖 (在我們需要權杖時呼叫 ADAL)。這個額外的參數會由 `intent.putExtra()` 方法傳遞。這裡，我們以您在 `Constants.java` 中指定的值定義 "thePolicy"。這可讓意圖在驗證期間知道要叫用的原則。

### 步驟 9：建立 Settings 活動

這只是一個會填入設定 UI 的活動。

建立名為 `SettingsActivity.java` 的檔案

以下是很簡單的 CRUD：

```
 package com.microsoft.aad.taskapplication;

import android.app.Activity;
import android.content.SharedPreferences;
import android.content.SharedPreferences.Editor;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.CheckBox;
import android.widget.Switch;
import android.widget.TextView;

import com.microsoft.aad.taskapplication.helpers.Constants;

/**
 * Settings page to try broker by options
 */
public class SettingsActivity extends Activity {

	//private CheckBox checkboxAskBroker, checkboxCheckBroker;
    private Switch fullScreenSwitch;

	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.activity_settings);

        loadSettings();
//		checkboxAskBroker = (CheckBox) findViewById(R.id.askInstall);
//		checkboxCheckBroker = (CheckBox) findViewById(R.id.useBroker);

        Button save = (Button) findViewById(R.id.settingsSave);

        save.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                TextView textView = (TextView)findViewById(R.id.authority);
                Constants.AUTHORITY_URL = textView.getText().toString();

                textView = (TextView)findViewById(R.id.resource);
            //    Constants.SCOPES = textView.getText().toString();

                textView = (TextView)findViewById(R.id.clientId);
                Constants.CLIENT_ID = textView.getText().toString();

                textView = (TextView)findViewById(R.id.extraQueryParameters);
                Constants.EXTRA_QP = textView.getText().toString();

                textView = (TextView)findViewById(R.id.redirectUri);
                Constants.REDIRECT_URL = textView.getText().toString();

                textView = (TextView)findViewById(R.id.serviceUrl);
                Constants.SERVICE_URL = textView.getText().toString();

                textView = (TextView)findViewById(R.id.serviceUrl);
                textView.setText(Constants.SERVICE_URL);

                textView = (TextView)findViewById(R.id.fb_signin);
                textView.setText(Constants.FB_POLICY);

                textView = (TextView)findViewById(R.id.email_signin);
                textView.setText(Constants.EMAIL_SIGNIN_POLICY);

                textView = (TextView)findViewById(R.id.email_signup);
                textView.setText(Constants.EMAIL_SIGNUP_POLICY);

                textView = (TextView)findViewById(R.id.correlationId);
                textView.setText(Constants.CORRELATION_ID);

                fullScreenSwitch = (Switch)findViewById(R.id.fullScreen);
                Constants.FULL_SCREEN = fullScreenSwitch.isChecked();

                finish();
            }
        });


	}

    private void loadSettings() {
        TextView textView = (TextView)findViewById(R.id.authority);
        textView.setText(Constants.AUTHORITY_URL);
        textView = (TextView)findViewById(R.id.resource);
        textView.setText(Constants.SCOPES[0]);
        textView = (TextView)findViewById(R.id.clientId);
        textView.setText(Constants.CLIENT_ID);
        textView = (TextView)findViewById(R.id.extraQueryParameters);
        textView.setText(Constants.EXTRA_QP);
        textView = (TextView)findViewById(R.id.redirectUri);
        textView.setText(Constants.REDIRECT_URL);
        textView = (TextView)findViewById(R.id.serviceUrl);
        textView.setText(Constants.SERVICE_URL);
        textView = (TextView)findViewById(R.id.fb_signin);
        textView.setText(Constants.FB_POLICY);
        textView = (TextView)findViewById(R.id.email_signin);
        textView.setText(Constants.EMAIL_SIGNIN_POLICY);
        textView = (TextView)findViewById(R.id.email_signup);
        textView.setText(Constants.EMAIL_SIGNUP_POLICY);
        textView = (TextView)findViewById(R.id.correlationId);
        textView.setText(Constants.CORRELATION_ID);

        fullScreenSwitch = (Switch)findViewById(R.id.fullScreen);
        fullScreenSwitch.setChecked(Constants.FULL_SCREEN);
    }

	private void saveSettings(String key, boolean value) {
		SharedPreferences prefs = SettingsActivity.this.getSharedPreferences(
				Constants.SHARED_PREFERENCE_NAME, Activity.MODE_PRIVATE);
		Editor prefsEditor = prefs.edit();
		prefsEditor.putBoolean(key, value);
		prefsEditor.commit();
	}
}
```

### 步驟 10：建立 AddTask 活動

這可讓我們將工作加入至 REST API 端點。同樣相當簡單。

建立名為 `AddTaskActivity.java` 的檔案

撰寫：

```
package com.microsoft.aad.taskapplication;

import android.app.Activity;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.EditText;

import com.microsoft.aad.taskapplication.helpers.Constants;
import com.microsoft.aad.taskapplication.helpers.TodoListHttpService;

public class AddTaskActivity extends Activity {

    EditText textField;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_add_task);
        textField = (EditText) findViewById(R.id.taskToAdd);
        Button button = (Button) findViewById(R.id.postTaskbutton);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (textField.getText().toString() != null
                        && !textField.getText().toString().trim().isEmpty()
                        && Constants.CURRENT_RESULT != null) {

                    TodoListHttpService service = new TodoListHttpService();
                    try {
                        service.addItem(textField.getText().toString(), Constants.CURRENT_RESULT.getAccessToken());
                    } catch (Exception e) {
                        SimpleAlertDialog.showAlertDialog(getApplicationContext(), "Exception caught", e.getMessage());
                    }
                    finish();
                }
            }
        });
    }

}

```

### 步驟 11：建立 ToDoList 活動

現在我們有一個最重要的活動，可讓我們從 Azure AD 取得原則的權杖，然後使用該權杖呼叫工作 REST API 伺服器。

建立名為 `ToDoActivity.java` 的檔案

撰寫下列方法 (我隨後會解釋這些呼叫)：

```

package com.microsoft.aad.taskapplication;

import android.app.Activity;
import android.app.AlertDialog;
import android.app.ProgressDialog;
import android.content.Intent;
import android.os.Build;
import android.os.Bundle;
import android.view.View;
import android.view.Window;
import android.webkit.CookieManager;
import android.webkit.CookieSyncManager;
import android.widget.ArrayAdapter;
import android.widget.Button;
import android.widget.ListView;
import android.widget.TextView;
import android.widget.Toast;

import com.microsoft.aad.adal.AuthenticationCallback;
import com.microsoft.aad.adal.AuthenticationContext;
import com.microsoft.aad.adal.AuthenticationResult;
import com.microsoft.aad.adal.AuthenticationSettings;
import com.microsoft.aad.adal.UserIdentifier;
import com.microsoft.aad.adal.PromptBehavior;
import com.microsoft.aad.taskapplication.helpers.Constants;
import com.microsoft.aad.taskapplication.helpers.InMemoryCacheStore;
import com.microsoft.aad.taskapplication.helpers.TodoListHttpService;
import com.microsoft.aad.taskapplication.helpers.Utils;
import com.microsoft.aad.taskapplication.helpers.WorkItemAdapter;


import java.io.UnsupportedEncodingException;
import java.net.MalformedURLException;
import java.net.URL;
import java.security.NoSuchAlgorithmException;
import java.security.spec.InvalidKeySpecException;
import java.util.ArrayList;
import java.util.List;
import java.util.UUID;

public class ToDoActivity extends Activity {



    private final static String TAG = "ToDoActivity";

    private AuthenticationContext mAuthContext;
    private static AuthenticationResult sResult;

    /**
     * Adapter to sync the items list with the view
     */
    private WorkItemAdapter mAdapter = null;

    /**
     * Show this dialog when activity first launches to check if user has login
     * or not.
     */
    private ProgressDialog mLoginProgressDialog;


    /**
     * Initializes the activity
     */
    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_list_todo_items);
        CookieSyncManager.createInstance(getApplicationContext());
        Toast.makeText(getApplicationContext(), TAG + "LifeCycle: OnCreate", Toast.LENGTH_SHORT)
                .show();

        // Clear previous sessions
        clearSessionCookie();
        try {
            // Provide key info for Encryption
            if (Build.VERSION.SDK_INT < 18) {
                Utils.setupKeyForSample();
            }

        Button button = (Button) findViewById(R.id.addTaskButton);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(ToDoActivity.this, AddTaskActivity.class);
                startActivity(intent);
            }
        });

        button = (Button) findViewById(R.id.appSettingsButton);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(ToDoActivity.this, SettingsActivity.class);
                startActivity(intent);

            }
        });

        button = (Button) findViewById(R.id.switchUserButton);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(ToDoActivity.this, LoginActivity.class);
                startActivity(intent);

            }
        });


        final TextView name = (TextView)findViewById(R.id.userLoggedIn);


        mLoginProgressDialog = new ProgressDialog(this);
        mLoginProgressDialog.requestWindowFeature(Window.FEATURE_NO_TITLE);
        mLoginProgressDialog.setMessage("Login in progress...");
        mLoginProgressDialog.show();
        // Ask for token and provide callback
        try {
            mAuthContext = new AuthenticationContext(ToDoActivity.this, Constants.AUTHORITY_URL,
                    false);
            String policy = getIntent().getStringExtra("thePolicy");

            if(Constants.CORRELATION_ID != null &&
                    Constants.CORRELATION_ID.trim().length() !=0){
                mAuthContext.setRequestCorrelationId(UUID.fromString(Constants.CORRELATION_ID));
            }

            AuthenticationSettings.INSTANCE.setSkipBroker(true);

            mAuthContext.acquireToken(ToDoActivity.this, Constants.SCOPES, Constants.ADDITIONAL_SCOPES, policy, Constants.CLIENT_ID,
                    Constants.REDIRECT_URL, getUserInfo(), PromptBehavior.Always,
                    "nux=1&" + Constants.EXTRA_QP,
                    new AuthenticationCallback<AuthenticationResult>() {

                        @Override
                        public void onError(Exception exc) {
                            if (mLoginProgressDialog.isShowing()) {
                                mLoginProgressDialog.dismiss();
                            }
                            SimpleAlertDialog.showAlertDialog(ToDoActivity.this,
                                    "Failed to get token", exc.getMessage());
                        }

                        @Override
                        public void onSuccess(AuthenticationResult result) {
                            if (mLoginProgressDialog.isShowing()) {
                                mLoginProgressDialog.dismiss();
                            }

                            if (result != null && !result.getToken().isEmpty()) {
                                setLocalToken(result);
                                updateLoggedInUser();
                                getTasks();
                                ToDoActivity.sResult = result;
                                Toast.makeText(getApplicationContext(), "Token is returned", Toast.LENGTH_SHORT)
                                        .show();

                                if (sResult.getUserInfo() != null) {
                                    name.setText(result.getUserInfo().getDisplayableId());
                                    Toast.makeText(getApplicationContext(),
                                            "User:" + sResult.getUserInfo().getDisplayableId(), Toast.LENGTH_SHORT)
                                            .show();
                                }
                            } else {
                                //TODO: popup error alert
                            }
                        }
                    });
        } catch (Exception e) {
            SimpleAlertDialog.showAlertDialog(ToDoActivity.this, "Exception caught", e.getMessage());
        }
        Toast.makeText(ToDoActivity.this, TAG + "done", Toast.LENGTH_SHORT).show();
    } catch (InvalidKeySpecException e) {
            e.printStackTrace();
        } catch (NoSuchAlgorithmException e) {
            e.printStackTrace();
        } catch (UnsupportedEncodingException e) {
            e.printStackTrace();
        }}
   
```

        
 您可能已注意到這會依我們尚未撰寫的方法為主，例如 `updateLoggedInUser()`、`clearSessionCookie()` 和 `getTasks()`。接下來撰寫這些方法。您目前可以放心地忽略 Android Studio 中的錯誤。

參數的說明：

  * ***SCOPES*** 是必要參數，代表您嘗試要求存取的範圍。在 B2C 預覽中，這等同於 Clientid，但未來會變更。
  * ***POLICY*** 是您想要用來驗證使用者的原則。 
  * ***CLIENT\_ID*** 是必要參數，來自 AzureAD 入口網站。
  * 您可以將 redirectUri 設為您的 packagename。這在 acquireToken 呼叫中不需要提供。
  * ***getUserInfo()*** 是我們查詢使用者是否已在快取中的方式，如果找不到使用者或存取權杖無效，則會提示使用者。接下來撰寫這個方法。
  * ***PromptBehavior.always*** 有助於要求認證以略過快取和 Cookie。
  * 授權碼兌換成權杖之後，就會呼叫 ***Callback***。

  Callback 會有一個提供 accesstoken、過期日期和 idtoken 資訊的 AuthenticationResult 物件。

> [AZURE.NOTE]	Microsoft Intune 的公司入口網站應用程式提供訊息代理程式元件，可安裝在使用者的裝置上。開發人員應該接受使用此元件，因為它為裝置上的所有應用程式提供 SSO。如果驗證器中已建立一個使用者帳戶，ADAL for Android 會使用訊息代理程式帳戶。如果要使用訊息代理程式，開發人員必須註冊特殊的 redirectUri 供訊息代理程式使用。RedirectUri 的格式為 msauth://packagename/Base64UrlencodedSignature。您可以使用指令碼 `brokerRedirectPrint.ps1` 或使用 API 呼叫 `mContext.getBrokerRedirectUri()`，以取得應用程式的 redirecturi。簽章與您從 Google Play 商店取得的簽署憑證有關。

 開發人員可以使用下列方法來略過訊息代理程式使用者：

    ```java
     AuthenticationSettings.Instance.setSkipBroker(true);
    ```
> [AZURE.NOTE] 為了降低此 B2C 快速入門的複雜性，我們在範例中已選擇略過 Broker。

接下來讓我們建立一些協助程式方法，以便於我們對工作 API 執行驗證呼叫期間單獨取得權杖。

在名為 `ToDoActivity.java` 的**同一個檔案中**

```
    private void getToken(final AuthenticationCallback callback) {

        String policy = getIntent().getStringExtra("thePolicy");

        // one of the acquireToken overloads
        mAuthContext.acquireToken(ToDoActivity.this, Constants.SCOPES, Constants.ADDITIONAL_SCOPES,
                policy, Constants.CLIENT_ID, Constants.REDIRECT_URL, getUserInfo(),
                PromptBehavior.Always, "nux=1&" + Constants.EXTRA_QP, callback);
    }
```

我們也加入一些方法，在全域 CONSTANTS 中 "set" 和 "get" AuthenticationResult (其中包含權杖)。這是必要的，因為即使 `ToDoActivity.java` 在其流程中使用 **sResult**，其他活動也無法存取權杖來工作 (例如在 `AddTaskActivity.java` 中加入工作)

```

    private AuthenticationResult getLocalToken() {
        return Constants.CURRENT_RESULT;
    }

    private void setLocalToken(AuthenticationResult newToken) {


        Constants.CURRENT_RESULT = newToken;
    }

    
```
### 步驟 12：建立方法以傳回 UserIdentifier

ADAL for Android 以 **UserIdentifier** 物件的形式來代表使用者。這不僅可以管理使用者，還可讓我們知道呼叫時是否使用相同的使用者，以便決定依賴快取，還是對伺服器執行新的呼叫。為了簡化起見，我們建立 `getUserInfo()`，以傳回可用於 `acquireToken()` 的 UserIdentifier。我們也建立 getUniqueId() 方法，以快速從快取中傳回 UserIdentifier 的識別碼。

```
  private String getUniqueId() {
        if (sResult != null && sResult.getUserInfo() != null
                && sResult.getUserInfo().getUniqueId() != null) {
            return sResult.getUserInfo().getUniqueId();
        }

        return null;
    }

    private UserIdentifier getUserInfo() {

        final TextView names = (TextView)findViewById(R.id.userLoggedIn);
        String name = names.getText().toString();
        return new UserIdentifier(name, UserIdentifier.UserIdentifierType.OptionalDisplayableId);
    }
    
```
 
### 步驟 13：撰寫一些協助程式方法

我們需要撰寫一些協助程式方法，幫助我們清除 Cookie 並提供 AuthenticationCallback。這些純粹是為了範例而用，目的是確保我們在呼叫 ToDo 活動時處於初始狀態。

在名為 `ToDoActivity.java` 的**同一個檔案中**

```

    private void clearSessionCookie() {

        CookieManager cookieManager = CookieManager.getInstance();
        cookieManager.removeSessionCookie();
        CookieSyncManager.getInstance().sync();
    }
``` 

```
    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        mAuthContext.onActivityResult(requestCode, resultCode, data);
    }
    
```   

### 步驟 14：呼叫工作 API

既然已經架構好活動，也準備進行抓取權杖的艱鉅工程，讓我們開始撰寫 API 來存取工作伺服器。

`getTasks` 提供陣列以代表伺服器中的工作

讓我們先撰寫 `getTask`：

在名為 `ToDoActivity.java` 的**同一個檔案中**

```
    private void getTasks() {
        if (sResult == null || sResult.getToken().isEmpty())
            return;

        List<String> items = new ArrayList<>();
        try {
            items = new TodoListHttpService().getAllItems(sResult.getToken());
        } catch (Exception e) {
            items = new ArrayList<>();
        }

        ListView listview = (ListView) findViewById(R.id.listViewToDo);
        ArrayAdapter<String> adapter = new ArrayAdapter<String>(this,
                android.R.layout.simple_list_item_1, android.R.id.text1, items);
        listview.setAdapter(adapter);
    }

```


 
 我們也撰寫一個方法，以便在首次執行時初始化資料表：
 
 在名為 `ToDoActivity.java` 的**同一個檔案中**
 
```
    private void initAppTables() {
        try {
            ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
            listViewToDo.setAdapter(mAdapter);

        } catch (Exception e) {
            createAndShowDialog(new Exception(
                    "There was an error creating the Mobile Service. Verify the URL"), "Error");
        }
    }

```
 
 這段程式碼還需要一些其他方法才能運作。現在我們就來撰寫。
 
### 建立端點 URL 產生器
 
 我們需要產生要連接的端點 URL。讓我們在相同的類別檔案中這樣做：
 
 在名為 `ToDoActivity.java` 的**同一個檔案中**
 
```
    private URL getEndpointUrl() {
        URL endpoint = null;
        try {
            endpoint = new URL(Constants.SERVICE_URL);
        } catch (MalformedURLException e) {
            e.printStackTrace();
        }
        return endpoint;
    }

```


請注意，在下列程式碼中，我們將存取權杖加入至要求：

### 步驟 15：讓我們撰寫一些 UX 方法

Android 需要我們處理某些回呼來操作應用程式。這些回呼是 `createAndShowDialog` 和 `onResume()`。若您先前撰寫過 Android 程式碼，這對您而言應相當簡單且熟悉。

現在我們就來撰寫：

在名為 `ToDoActivity.java` 的**同一個檔案中**

```
    @Override
    public void onResume() {
        super.onResume(); // Always call the superclass method first

        updateLoggedInUser();
        // User can click logout, it will come back here
        // It should refresh list again
        getTasks();
    }

    
```

現在管理我們的對話方塊回呼：

在名為 `ToDoActivity.java` 的**同一個檔案中**


```
    /**
     * Creates a dialog and shows it
     *
     * @param exception The exception to show in the dialog
     * @param title     The dialog title
     */
    private void createAndShowDialog(Exception exception, String title) {
        createAndShowDialog(exception.toString(), title);
    }

    /**
     * Creates a dialog and shows it
     *
     * @param message The dialog message
     * @param title   The dialog title
     */
    private void createAndShowDialog(String message, String title) {
        AlertDialog.Builder builder = new AlertDialog.Builder(this);

        builder.setMessage(message);
        builder.setTitle(title);
        builder.create().show();
    }
    
```

就這麼簡單！ 您應該已經有一個可編譯的 `ToDoActivity.java` 檔案了。整個專案現在也應該可以成功編譯。
    


### 步驟 16：執行範例應用程式

最後，在 Android Studio 或 Eclipse 中同時建置並執行應用程式。註冊或登入應用程式，並為登入的使用者建立工作。登出後，再以不同使用者重新登入，並為該使用者建立工作。

請注意每位使用者的工作如何儲存於 API，因為 API 會從它收到的存取權杖中擷取使用者的身分識別。

為了方便參考，您可以[在此處取得 .zip 格式](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android/archive/complete.zip)的完整範例，或者從 GitHub 中複製：

```
git clone --branch complete https://github.com/AzureADQuickStarts/B2C-NativeClient-Android
```


### 重要資訊


#### 加密

根據預設，ADAL 會加密權杖並儲存在 SharedPreferences 中。您可以在 StorageHelper 類別查看詳細資料。Android 引進 AndroidKeyStore 4.3(API18) 安全儲存體來存放私密金鑰。ADAL 對 API18 的和更新版本使用此機制。如果您想要使用較低 SDK 版本的 ADAL，您需要在 AuthenticationSettings.INSTANCE.setSecretKey 提供秘密金鑰

#### Web 檢視中的工作階段 Cookie

在應用程式關閉後，Android Web 檢視不會清除工作階段 Cookie。您可以使用以下範例程式碼來處理這部分：```
CookieSyncManager.createInstance(getApplicationContext());
CookieManager cookieManager = CookieManager.getInstance();
cookieManager.removeSessionCookie();
CookieSyncManager.getInstance().sync();
``` 深入了解 Cookie：http://developer.android.com/reference/android/webkit/CookieSyncManager.html
 

<!---HONumber=AcomDC_0224_2016-->