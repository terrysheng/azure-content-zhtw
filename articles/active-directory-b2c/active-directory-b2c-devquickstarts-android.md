<properties pageTitle="Azure Active Directory B2C 預覽：從 Android 應用程式呼叫 Web API | Microsoft Azure" description="本文將示範如何建立 Android「待辦事項清單」應用程式，以使用 OAuth 2.0 持有人權杖呼叫 Node.js Web API。Android 應用程式與 Web API 會使用 Azure Active Directory B2C 來管理使用者身分識別與驗證使用者。" services="active-directory-b2c" documentationCenter="android" authors="brandwe" manager="msmbaldwin" editor=""/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="02/17/2016"
	ms.author="brandwe"/>

# Azure AD B2C 預覽：從 Android 應用程式呼叫 Web API

Azure Active Directory (Azure AD) B2C 可讓您在 Android 應用程式和 Web API 中加入強大的自助式身分識別管理功能，只要幾個簡短步驟即可完成。本文將示範如何建立 Android「待辦事項清單」應用程式，以使用 OAuth 2.0 持有人權杖呼叫 Node.js Web API。Android 應用程式與 Web API 將會使用 Azure AD B2C 來管理使用者身分識別與驗證使用者。

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

此快速啟動會要求您必須擁有受 Azure AD (含 B2C) 保護的 Web API，才可完整運作。我們已經為 .NET 與 Node.js 建立一個 Web API 供您使用。本逐步解說假設已設定 Node.js Web API 範例。如需詳細資訊，請參閱 [Azure AD B2C Web API for Node.js 教學課程](active-directory-b2c-devquickstarts-api-node.md)。

對於需要存取受保護資源的 Android 用戶端，Azure AD 提供 Active Directory 驗證程式庫 (ADAL)。ADAL 的唯一目的是為了讓您的應用程式輕鬆取得存取權杖。為了示範究竟多麼簡單，我們將在本指南中建置一個執行下列動作的 Android 待辦事項清單應用程式：

- 取得存取權杖，以使用 [OAuth 2.0 驗證通訊協定](https://msdn.microsoft.com/library/azure/dn645545.aspx)呼叫待辦事項清單 API。
- 取得使用者的待辦事項清單。
- 登出使用者。

> [AZURE.NOTE] 本文不會說明如何使用 Azure AD B2C 實作登入、註冊和管理設定檔，而是著重如何在驗證使用者之後呼叫 Web API。您應該先從 [.NET Web 應用程式使用者入門教學課程](active-directory-b2c-devquickstarts-web-dotnet.md)開始 (如果還沒有進行)，以了解 Azure AD B2C 的基本概念。

## 取得 Azure AD B2C 目錄

您必須先建立目錄或租用戶，才可使用 Azure AD B2C。目錄是適用於所有使用者、app、群組等項目的容器。如果您還沒有此資源，請先[建立 B2C 目錄](active-directory-b2c-get-started.md)，再繼續進行本指南。

## 建立應用程式

接著，您必須在 B2C 目錄中建立應用程式。這會提供必要資訊給 Azure AD，讓它與應用程式安全地通訊。在此案例中，因為應用程式與 Web API 會組成一個邏輯應用程式，所以將由單一**應用程式識別碼**代表。如果要建立應用程式，請遵循[這些指示](active-directory-b2c-app-registration.md)。請務必：

- 在應用程式中加入 **Web 應用程式**/**Web API**。
- 在 [回覆 URL] 中輸入 `urn:ietf:wg:oauth:2.0:oob`。這是此程式碼範例的預設 URL。
- 為您的應用程式建立**應用程式密碼**，並複製起來。稍後您將會用到此資訊。請注意，這個值在使用之前必須經過 [XML 逸出](https://www.w3.org/TR/2006/REC-xml11-20060816/#dt-escape)。
- 複製指派給您的應用程式的**應用程式識別碼**。稍後您也會需要此資訊。

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## 建立您的原則

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

在 Azure AD B2C 中，每個使用者經驗皆由[原則](active-directory-b2c-reference-policies.md)所定義。此應用程式包含三種身分識別體驗：註冊、登入，以及使用 Facebook 登入。您必須為每個類型建立一個原則，如[原則參考文章](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy)所述。建立您的三個原則時，請務必：

- 在註冊原則中，選擇 [顯示名稱] 和其他註冊屬性。
- 在每個原則中，選擇 [顯示名稱] 和 [物件識別碼] 應用程式宣告。您也可以選擇其他宣告。
- 建立每個原則之後，請複製其**名稱**。其前置詞應該為 `b2c_1_`。稍後您將需要這些原則名稱。

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

建立您的三個原則後，就可以開始建置您的應用程式。

請注意，本文不會說明如何使用您剛才建立的原則。如需了解 Azure AD B2C 中的原則如何運作，請從 [.NET Web 應用程式使用者入門教學課程](active-directory-b2c-devquickstarts-web-dotnet.md)開始。

## 下載程式碼

本教學課程的程式碼保留在 [GitHub](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android) 上。若要遵循指示建立範例，您可以[下載基本架構專案的 .zip 檔案](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android/archive/skeleton.zip)。您也可以複製基本架構：

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-NativeClient-Android.git
```

> [AZURE.NOTE] **您必須下載基本架構才能完成本教學課程。** 由於實作完整運作的 Android 應用程式有其複雜性，因此基本架構所包含的 UX 程式碼將於您完成本教學課程之後執行。如此將能節省開發人員的時間。UX 程式碼與如何將 B2C 加入至 Android 應用程式的主題無關。

完整的 App 也[提供 .zip 檔案格式](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android/archive/complete.zip)，或放在相同儲存機制的 `complete` 分支中。

若要使用 Maven 來建置，您可以使用最上層的 `pom.xml`。

  1. 請依照[必要條件一節的步驟，為 Android 設定 Maven](https://github.com/MSOpenTech/azure-activedirectory-library-for-android/wiki/Setting-up-maven-environment-for-Android)
  2. 使用 SDK 21 設定模擬器。
  3. 移至您已複製儲存機制的根資料夾。
  4. 執行命令 `mvn clean install`。
  5. 將目錄切換至快速入門範例 `cd samples\hello`。
  6. 執行命令 `mvn android:deploy android:run`。

您應該會看到應用程式啟動。輸入測試使用者認證來測試一下。

除了 Android Archive (AAR) 封裝，也會提交 Java Archive (JAR) 封裝。

## 下載 Android ADAL 並將它加入您的 Android Studio 工作區

您有多個選項可供選擇，以在 Android 專案中使用此程式庫：

* 您可以使用原始程式碼將此程式庫匯入到 Eclipse，並連結至您的應用程式。
* 如果您使用 Android Studio，可以使用 AAR 封裝格式並參考二進位檔。

### 選項 1：透過 Gradle 取得二進位檔 (建議選項)

您可以從 Maven 中央儲存機制取得二進位檔。在 Android Studio 中，可將 AAR 封裝納入您的專案 (例如，在 `build.gradle` 中)，如下所示：

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

### 選項 2：透過 Maven 取得 AAR

如果您在 Eclipse 中使用 `m2e` 外掛程式，可以在 `pom.xml` 檔案中指定相依性：

```xml
<dependency>
    <groupId>com.microsoft.aad</groupId>
    <artifactId>adal</artifactId>
    <version>2.0.1-alpha</version>
    <type>aar</type>
</dependency>
```

### 選項 3：透過 Git 取得原始檔 (最後手段)

若要透過 Git 取得 SDK 的原始程式碼，請輸入：

    git clone git@github.com:AzureAD/azure-activedirectory-library-for-android.git
    cd ./azure-activedirectory-library-for-android/src

使用分支**聚合**。

## 設定組態檔

使用您先前在 B2C 入口網站中設定的組態來設定 Android 專案。

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
- `SCOPES`：您傳給伺服器的範圍，而在使用者登入時，您想要向伺服器要求此範圍。在 B2C 預覽版本中，您會傳遞 `client_id`。不過，未來這預計會變更為 `read scopes`。屆時會更新本文件。
- `ADDITIONAL_SCOPES`：您想要用於應用程式的其他範圍。未來預計會使用於這些範圍。
- `CLIENT_ID`：您從入口網站取得的應用程式識別碼。
- `REDIRECT_URL`：您預期會回傳權杖的重新導向。
- `EXTRA_QP`：您想要以 URL 編碼格式傳給伺服器的任何額外參數。
- `FB_POLICY`：您所叫用的原則。這是本逐步解說最重要的部分。
- `EMAIL_SIGNIN_POLICY`：您所叫用的原則。這是本逐步解說最重要的部分。
- `EMAIL_SIGNUP_POLICY`：您所叫用的原則。這是本逐步解說最重要的部分。

## 將 Android ADAL 參考加入至您的專案

> [AZURE.NOTE]	ADAL Android 使用以意圖為基礎的模型叫用驗證。意圖「舖陳」於應用程式上運作。這整個範例 (全部使用 ADAL for Android) 著重於如何管理意圖並傳遞它們之間的資訊。

首先，將應用程式的配置告知 Android，包括您想要使用的意圖。稍後將在本教學課程中詳細說明這些意圖。

更新專案的 `AndroidManifest.xml` 檔案來包含所有意圖：

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

如您所見，您會定義五個活動。您將會使用所有的活動。

- `AuthenticationActivity`：這是來自 ADAL，並提供登入 Web 檢視。
- `LoginActivity`：這可顯示登入原則和每個原則的按鈕。
- `SettingsActivity`：您可在執行階段用於變更應用程式設定。
- `AddTaskActivity`：您可用於將工作加入至 Azure AD 所保護的 REST API。
- `ToDoActivity`：這是顯示工作的主要活動。

## 建立 sign-in 活動

建立主要活動並將它命名為 `LoginActivity`。

建立名為 `LoginActivity.java` 的檔案。

您必須初始化活動，並加入一些可控制 UI 的按鈕。若您先前撰寫過 Android 程式碼，這對您而言應很熟悉。

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
您現在已建立按鈕來呼叫您的 `ToDoActivity` 意圖 (該意圖會在您需要權杖時呼叫 ADAL)。這些按鈕會使用您的活動做為參考和額外參數。這個額外的參數會由 `intent.putExtra()` 方法傳遞。您可使用在 `Constants.java` 中指定的項目來定義 `"thePolicy"`。這可讓意圖得知在驗證期間所要叫用的原則。

## 建立 Settings 活動

這是一個會填入設定 UI 的活動。

建立名為 `SettingsActivity.java` 的檔案，以便進行簡單的建立、讀取、更新和刪除 (CRUD) 作業。

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

## 建立 add-task 活動

您可以使用此活動將工作加入至 REST API 端點。

建立名為 `AddTaskActivity.java` 的檔案並寫入下列程式碼。

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

## 建立 to-do list 活動

這是最重要的活動。您可以利用這項活動，從 Azure AD 取得原則的權杖，然後使用該權杖呼叫工作 REST API 伺服器。

建立名為 `ToDoActivity.java` 的檔案
並寫入下列程式碼。(稍後將會說明呼叫。)

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


 您可能已注意到這會依賴尚未撰寫的方法。其中包括 `updateLoggedInUser()`、`clearSessionCookie()` 和 `getTasks()`。您稍後會在本指南中撰寫這些方法。您目前可以放心地忽略 Android Studio 中的錯誤。

參數的說明：

  - `SCOPES`：必要參數，代表您嘗試要求存取的範圍。在 B2C 預覽中，這等同於 `client_id`，但預計未來會變更。
  - `POLICY`：您想要用來驗證使用者的原則。
  - `CLIENT_ID`：必要參數，來自 AzureAD 入口網站。
  - `redirectUri`：可以設為您的封裝名稱。不一定需要對 `acquireToken` 呼叫提供此參數。
  - `getUserInfo()`：用來查詢使用者是否已在快取中的方法。此參數也會說明在找不到該使用者或使用者的存取權杖無效時，如何提示使用者。稍後會在本指南中撰寫這個方法。
  - `PromptBehavior.always`：有助於要求認證時略過快取和 Cookie。
  - `Callback`：在授權碼兌換成權杖之後呼叫。它會有 `AuthenticationResult` 物件，其中包含存取權杖、到期日和 ID 權杖資訊。

> [AZURE.NOTE]	Microsoft Intune 公司入口網站應用程式提供訊息代理程式元件，其可安裝在使用者的裝置上。應用程式會在裝置上提供所有應用程式的單一登入 (SSO) 存取。開發人員應做好準備以便使用 Intune。如果驗證器中已建立一個使用者帳戶，ADAL for Android 會使用訊息代理程式帳戶。如果要使用訊息代理程式，開發人員必須註冊特殊的 `redirectUri` 供訊息代理程式使用。`redirectUri` 的格式為 msauth://packagename/Base64UrlencodedSignature。您可以使用指令碼 `brokerRedirectPrint.ps1` 或使用 API 呼叫 `mContext.getBrokerRedirectUri()`，以取得應用程式的 `redirectUri`。簽章與您從 Google Play 商店取得的簽署憑證有關。

 您可以使用下列方法來略過訊息代理程式使用者：

    ```java
     AuthenticationSettings.Instance.setSkipBroker(true);
    ```
> [AZURE.NOTE] 為了降低此 B2C 快速入門的複雜性，我們在範例中已選擇略過訊息代理程式。

接下來，建立一些協助程式方法，以便於對工作 API 執行驗證呼叫期間單獨取得權杖。

在相同的 `ToDoActivity.java` 檔案中，寫入下列程式碼。

```
    private void getToken(final AuthenticationCallback callback) {

        String policy = getIntent().getStringExtra("thePolicy");

        // one of the acquireToken overloads
        mAuthContext.acquireToken(ToDoActivity.this, Constants.SCOPES, Constants.ADDITIONAL_SCOPES,
                policy, Constants.CLIENT_ID, Constants.REDIRECT_URL, getUserInfo(),
                PromptBehavior.Always, "nux=1&" + Constants.EXTRA_QP, callback);
    }
```

此外，加入將「設定」及「取得」`AuthenticationResult` (其中包含您的權杖) 至全域 `Constants` 的方法。即使 `ToDoActivity.java` 在其流程中使用 `sResult`，您還是需要加入這些方法。如果您未這麼做，其他活動將無法存取權杖來執行工作 (例如在 `AddTaskActivity.java` 中新增工作)。

```

    private AuthenticationResult getLocalToken() {
        return Constants.CURRENT_RESULT;
    }

    private void setLocalToken(AuthenticationResult newToken) {


        Constants.CURRENT_RESULT = newToken;
    }


```
## 建立方法以傳回使用者識別碼

ADAL for Android 會以 `UserIdentifier` 物件的形式來代表使用者。這可管理使用者。您可以使用此物件來識別您的呼叫中是否使用相同的使用者。使用此資訊，您即可依賴快取，而不用對伺服器進行新的呼叫。為了簡化起見，我們建立了可傳回 `UserIdentifier`的 `getUserInfo()` 方法。您可以使用此方法搭配 `acquireToken()`。我們也建立了 `getUniqueId()` 方法，以傳回快取中 `UserIdentifier` 的識別碼。

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

### 撰寫協助程式方法

接下來，撰寫一些協助程式方法來協助您清除 cookie 並提供 `AuthenticationCallback`。這些方法純粹是為了範例而用，以確保您在呼叫 `ToDo` 活動時處於初始狀態。

在名為 `ToDoActivity.java` 的相同檔案中，寫入下列程式碼。

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

## 呼叫工作 API

您準備好讓活動擷取權杖之後，您可以撰寫您的 API 來存取工作伺服器。

`getTasks` 提供一個陣列以代表伺服器中的工作。

開始使用 `getTasks`

在名為 `ToDoActivity.java` 的相同檔案中，寫入下列程式碼。

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

此外，請撰寫一個方法，以便在首次執行時初始化資料表。

在名為 `ToDoActivity.java` 的相同檔案中，寫入下列程式碼。

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

您可以看見，這段程式碼還需要一些其他方法才能運作。接下來撰寫這些方法。

### 建立端點 URL 產生器

您需要產生要連接的端點 URL。在相同的類別檔案中這樣做。

在名為 `ToDoActivity.java` 的相同檔案中，寫入下列程式碼。

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


請注意，您會在下一節討論的程式碼中，將存取權杖加入至要求。

## 撰寫一些 UX 方法

Android 需要您處理某些回呼來操作應用程式。這些回呼是 `createAndShowDialog` 和 `onResume()`。若您先前撰寫過 Android 程式碼，這對您而言應很熟悉。

在名為 `ToDoActivity.java` 的相同檔案中，寫入下列程式碼。

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

接著，管理您的對話方塊回呼。

在名為 `ToDoActivity.java` 的相同檔案中，寫入下列程式碼。

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

您現在應該有一個可編譯的 `ToDoActivity.java` 檔案。整個專案現在也應該可以編譯。

## 執行範例應用程式

最後，在 Android Studio 或 Eclipse 中建置並執行應用程式。註冊或登入應用程式。為登入的使用者建立工作。登出後，再以不同使用者重新登入，然後為該使用者建立工作。

請注意，這些工作會依每位使用者儲存於 API，因為 API 會從它收到的存取權杖中擷取使用者的身分識別。

為了方便參考，[會以 .zip 檔案提供](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android/archive/complete.zip)完整的範例。您也可以從 Github 複製它：

```
git clone --branch complete https://github.com/AzureADQuickStarts/B2C-NativeClient-Android
```


## 重要資訊


### 加密

根據預設，ADAL 會加密權杖並儲存在 `SharedPreferences` 中。您可以在 `StorageHelper` 類別查看詳細資料。Android 引進 **AndroidKeyStore 4.3(API18)** 安全儲存體來存放私密金鑰。ADAL 對 API18 的和更新版本使用此機制。如果您想要使用較低 SDK 版本的 ADAL，您需要在 `AuthenticationSettings.INSTANCE.setSecretKey` 提供秘密金鑰

### Web 檢視中的工作階段 Cookie

在應用程式關閉後，Android Web 檢視不會清除工作階段 Cookie。您可以使用以下範例程式碼來處理這部分。
```
CookieSyncManager.createInstance(getApplicationContext());
CookieManager cookieManager = CookieManager.getInstance();
cookieManager.removeSessionCookie();
CookieSyncManager.getInstance().sync();
```
[深入了解 Cookie](http://developer.android.com/reference/android/webkit/CookieSyncManager.html)。

<!---HONumber=AcomDC_0302_2016-------->