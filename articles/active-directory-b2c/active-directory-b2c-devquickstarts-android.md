<properties pageTitle="Azure AD B2C 預覽：從 Android 應用程式呼叫 Web API | Microsoft Azure" description="本文將示範如何建立使用 OAuth 2.0 持有人權杖呼叫 node.js Web API 的 Android「待辦事項清單」應用程式。Android 應用程式與 Web API 將會使用 Azure AD B2C 來管理使用者身分識別與驗證使用者。" services="active-directory-b2c" documentationCenter="andriod" authors="brandwe" manager="msmbaldwin" editor=""/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="09/16/2015"
	ms.author="brandwe"/>

# Azure AD B2C 預覽：從 Android 應用程式呼叫 Web API

有了 Azure AD B2C，您可以將強大的自助式身分識別管理功能加入 Android 應用程式和 Web API，只要幾個簡短步驟即可完成。本文將示範如何建立使用 OAuth 2.0 持有人權杖呼叫 node.js Web API 的 Android「待辦事項清單」應用程式。Android 應用程式與 Web API 將會使用 Azure AD B2C 來管理使用者身分識別與驗證使用者。

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]
	
> [AZURE.NOTE]使用此快速入門的先決條件為，您必須擁有受 Azure AD (含 B2C) 保護的 Web API，才可完整運作。我們已經為 .NET 與 node.js 建立一個 Web API 供您使用。本逐步解說假設您已設定 node.js Web API 範例。請參閱 [Node.js 的 Azure AD B2C Web API 教學課程](active-directory-b2c-devquickstarts-api-node.md)。

> [AZURE.NOTE]本文不會說明如何使用 Azure AD B2C 實作登入、註冊和設定檔管理。而會著重在如何在使用者已通過驗證後呼叫 Web API。您應該先從 [.NET Web 應用程式使用者入門教學課程](active-directory-b2c-devquickstarts-web-dotnet.md)開始 (如果還沒有進行)，以了解 Azure AD B2C 的基本概念。

對於需要存取受保護資源的 Android 用戶端，Azure AD 提供 Active Directory 驗證程式庫 (ADAL)。ADAL 存在的唯一目的是為了讓您的應用程式輕鬆取得存取權杖。為了示範究竟多麼簡單，我們將建置一個執行下列動作的 Android 待辦事項清單應用程式：

-	取得存取權杖來使用 [OAuth 2.0 驗證通訊協定](https://msdn.microsoft.com/library/azure/dn645545.aspx)呼叫待辦事項清單 API。
-	取得使用者的待辦事項清單
-	將使用者登出。



### 步驟 1：取得 Azure AD B2C 目錄

您必須先建立目錄或租用戶，才可使用 Azure AD B2C。目錄為所有使用者、應用程式、群組等等項目的容器。如果您尚未擁有目錄，請先[建立 B2C 目錄](active-directory-b2c-get-started.md)，再繼續下一個步驟。

### 步驟 2：建立應用程式

您現在需要在 B2C 目錄中建立應用程式，以提供一些必要資訊給 Azure AD，讓它與應用程式安全地通訊。在此案例中，因為應用程式與 Web API 會組成一個邏輯應用程式，所以將由單一**應用程式識別碼**代表。若要建立應用程式，請遵循[這些指示](active-directory-b2c-app-registration.md)進行。請務必

- 在應用程式中包含 **Web 應用程式/Web API**
- 輸入 `http://localhost:3000/auth/openid/return` 做為**回覆 URL** - 它是此程式碼範例的預設 URL。
- 為您的應用程式建立**應用程式密碼**，並複製起來。稍後您將會用到此資訊。
- 複製指派給應用程式的**應用程式識別碼**。稍後您也會用到此資訊。

### 步驟 3：建立您的原則

在 Azure AD B2C 中，每個使用者經驗皆是由某個[**原則**](active-directory-b2c-reference-policies.md)定義的。此應用程式包含三種身分識別體驗 - 註冊、登入，以及使用 Facebook 登入。您必須為每個類型建立一個原則，如[原則參考文件](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy)所述。建立您的三個原則時，請務必：

- 在註冊原則中選擇 [顯示名稱] 和其他一些註冊屬性。
- 在每個原則中選擇 [顯示名稱] 和 [物件識別碼] 應用程式宣告。您也可以選擇其他宣告。
- 建立每個原則後，請複製原則的 [名稱]。其前置詞應該為 `b2c_1_`。稍後您將需要這些原則名稱。 

當您成功建立三個原則後，就可以開始建置您的應用程式。

請注意，本文不會說明如何使用您剛才建立的原則。若您想要了解 Azure AD B2C 中的原則如何運作，您應該從 [.NET Web 應用程式使用者入門教學課程](active-directory-b2c-devquickstarts-web-dotnet.md)開始。

### 步驟 4：下載程式碼

本教學課程的程式碼保留在 [GitHub](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android) 上。若要循著指示建立範例，您可以[下載 .zip 格式的基本架構專案](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android/archive/skeleton.zip)或複製基本架構：

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-NativeClient-Android.git
```

> [AZURE.NOTE]**若要完成此教學課程，您必須下載基本架構。** 由於在 Android 上實作完整運作的應用程式有其複雜性，因此**基本架構**包含 UX 程式碼，將於您完成下方教學課程之後執行。如此將能節省開發人員的時間。UX 程式碼與加入 B2C 至 Android 應用程式的主題無關。

完整的應用程式也有 [.zip 格式](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android/archive/complete.zip)；您也可以在相同儲存機制的 `complete` 分支取得。


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

### 步驟 5：下載 Android ADAL，並將其加入您的 Eclipse 工作區

我們提供多個選項，讓您輕鬆地在 Android 專案中使用此程式庫：

* 您可以使用原始程式碼將此程式庫匯入到 Eclipse，並連結至您的應用程式。
* 如果是使用 Android Studio，您可以使用 *aar* 封裝格式，並參考二進位檔。

####選項 1：透過 Git 取得原始檔

若要透過 git 取得 SDK 的原始程式碼，只要輸入：

    git clone git@github.com:AzureAD/azure-activedirectory-library-for-android.git
    cd ./azure-activedirectory-library-for-android/src
    
    use the branch "convergence"

####選項 2：透過 Gradle 取得二進位檔

您可以從 Maven 中央儲存機制取得二進位檔。AAR 封裝可以在 AndroidStudio 中加入您的專案中，如下所示：

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
    compile('com.microsoft.aad:adal:2.0-alpha') {
        exclude group: 'com.android.support'
    } // Recent version is 2.0-alpha
}
```

####選項 3：透過 Maven 取得 aar

如果您在 Eclipse 中使用 m2e 外掛程式，您可以在 pom.xml 檔案中指定相依性：

```xml
<dependency>
    <groupId>com.microsoft.aad</groupId>
    <artifactId>adal</artifactId>
    <version>2.0-alpha</version>
    <type>aar</type>
</dependency>
```


####選項 5：libs 資料夾內的 jar 封裝
您可以從 maven 儲存機制取得 jar 檔案，並放入專案的 *libs* 資料夾中。您也需要將必要的資源複製到專案，因為 jar 封裝不包含它們。


### 步驟 6：將 Android ADAL 參考加入至您的專案


2. 在專案中加入參考，並指定為 Android 程式庫。如果您不確定怎麼做，[按一下這裡取得詳細資訊](http://developer.android.com/tools/projects/projects-eclipse.html)

3. 加入專案相依性以偵錯您的專案設定

4. 更新專案的 AndroidManifest.xml 檔案來包含：

    ```Java
      <uses-permission android:name="android.permission.INTERNET" />
      <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
      <application
            android:allowBackup="true"
            android:debuggable="true"
            android:icon="@drawable/ic_launcher"
            android:label="@string/app_name"
            android:theme="@style/AppTheme" >

            <activity
                android:name="com.microsoft.aad.adal.AuthenticationActivity"
                android:label="@string/title_login_hello_app" >
            </activity>
      ....
      <application/>
    ```

### 步驟 7：加入程式碼以呼叫 Android 的 ADAL

讓我們來建立主要活動並將其命名為 `ToDoActivity`。

我們必須初始化活動，並加入部分可控制 UI 的按鈕。此外，若您先前撰寫過 Android 程式碼，此步驟對您而言應相當簡單且熟悉：

```
public class ToDoActivity extends Activity {

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

    /**
     * Initializes the activity
     */
    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_list_todo_items);
        Toast.makeText(getApplicationContext(), TAG + "LifeCycle: OnCreate", Toast.LENGTH_SHORT)
                .show();
```

接下來，在同一個類別中 (先不要加上結尾括號) 加入我們的 UI 按鈕。您將會看到我們加入的 Facebook 登入和電子郵件登入按鈕。這將會使用我們在 `constants.java` 檔案中定義的原則：

```
        Button button = (Button) findViewById(R.id.signin_facebook);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(ToDoActivity.this, SettingsActivity.class);
                startActivity(intent);

            }
        });

        button = (Button) findViewById(R.id.signin_email);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(ToDoActivity.this, SettingsActivity.class);
                startActivity(intent);

            }
        });

        button = (Button) findViewById(R.id.signup_email);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(ToDoActivity.this, SettingsActivity.class);
                startActivity(intent);

            }
        });

    ```
Create an instance of AuthenticationContext at your main Activity:

    ```Java
 		 mLoginProgressDialog = new ProgressDialog(this);
        mLoginProgressDialog.requestWindowFeature(Window.FEATURE_NO_TITLE);
        mLoginProgressDialog.setMessage("Login in progress...");
        mLoginProgressDialog.show();
        // Ask for token and provide callback
        try {
            mAuthContext = new AuthenticationContext(ToDoActivity.this, Constants.AUTHORITY_URL,
                    false, InMemoryCacheStore.getInstance());
            mAuthContext.getCache().removeAll();

            if(Constants.CORRELATION_ID != null &&
                    Constants.CORRELATION_ID.trim().length() !=0){
                mAuthContext.setRequestCorrelationId(UUID.fromString(Constants.CORRELATION_ID));
    ```
  * NOTE: mContext is a field in your activity
 
 Let's also define our Result method `onActivityResult` that will be called when we have a result from the callback we'll write later:
 
 ```
 @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        mAuthContext.onActivityResult(requestCode, resultCode, data);
    }
 ```

Next, let's define our callback:

    ```Java
    mAuthContext.acquireToken(ToDoActivity.this, Constants.SCOPES, Constants.ADDITIONAL_SCOPES, Constants.POLICY, Constants.CLIENT_ID, Constants.REDIRECT_URL, Constants.USER_HINT, Constants.PROMPT,
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

                            if (result != null && !result.getAccessToken().isEmpty()) {
                                setLocalToken(result);
                                updateLoggedInUser();
                                getTasks();
                            } else {
                                //TODO: popup error alert
                            }
                        }
                    });
        } catch (Exception e) {
            SimpleAlertDialog.showAlertDialog(getApplicationContext(), "Exception caught", e.getMessage());
        }
        Toast.makeText(getApplicationContext(), TAG + "done", Toast.LENGTH_SHORT).show();
    }
        ```
        
 You may notice that this relies on methods we haven't written yet, such as `updateLoggedInUser()` and `getTasks()`. We'll write those below.	You can safely ignore the errors in Android Studio for now.

Explanation of the parameters:

  * ***SCOPES*** is required and is the scopes you are trying to reqeust access for. For the B2C preview this is the same as the Clientid but will change in the future.
  * ***POLICY*** is the policy for while you wish to authenticate the user. 
  * ***CLIENT_ID*** is required and comes from the AzureAD Portal.
  * You can setup redirectUri as your packagename. It is not required to be provided for the acquireToken call.
  * ***USER_HINT*** is the way we look up if the user is already in the cache and prompt the user if they are not found or the access token is invalid.
  * ***PROMPT*** helps to ask for credentials to skip cache and cookie.
  * ***Callback*** will be called after authorization code is exchanged for a token.

  The Callback will have an object of AuthenticationResult which has accesstoken, date expired, and idtoken info.

> [AZURE.NOTE]	Microsoft Intune's Company portal app provides the broker component and may be installed on the user's device. Developers should be prepared to allow for it's use as it provides SSO across all applications on the device. ADAL for Android will use the broker account if there is one user account created in the Authenticator. To use the broker, the developer needs to register a special redirectUri for broker usage. RedirectUri is in the format of msauth://packagename/Base64UrlencodedSignature. You can get your redirecturi for your app using the script `brokerRedirectPrint.ps1` or use API call `mContext.getBrokerRedirectUri()`. The signature is related to your signing certificates from the Google Play store.

 A Developer can skip the broker user with:

    ```java
     AuthenticationSettings.Instance.setSkipBroker(true);
    ```
> [AZURE.WARNING] In order to reduce the complexity of this B2C Quickstart, we have opted in our sample to skip the broker. However, this is not recommended.

Next, let's create some helper methods that will get the token alone during our authentication calls to the Task API:

```
private void getToken(final AuthenticationCallback callback) {

        // one of the acquireToken overloads
        mAuthContext.acquireToken(onnstants.SCOPES, Constants.ADDITIONAL_SCOPES, Constants.POLICY,
                Constants.CLIENT_ID, Constants.REDIRECT_URL, Constants.USER_HINT, Constants.PROMPT,
                "nux=1&" + Constants.EXTRA_QP, callback);
    }

    private AuthenticationResult getLocalToken() {
        return Constants.CURRENT_RESULT;
    }

    private void setLocalToken(AuthenticationResult newToken) {
        Constants.CURRENT_RESULT = newToken;
    }
    
```

Finally, Your app manifest should have permissions to use AccountManager accounts: http://developer.android.com/reference/android/accounts/AccountManager.html

 * GET_ACCOUNTS
 * USE_CREDENTIALS
 * MANAGE_ACCOUNTS


### Step 8. Call the Task API

Now that we have our Activity wired up and ready to go to do the heavy lifting of grabbing tokens, let's write our API to access the task server.

Our `getTasks` provides an array that represents the tasks in our server 
Our `addTask` and `deleteTask` do the subsequent action and return TRUE or FALSE if it was successful.

Let's write our `getTask` first:

```
private void getTasks() { if (Constants.CURRENT\_RESULT == null || Constants.CURRENT\_RESULT.getAccessToken().isEmpty()) return;

        List<String> items = new ArrayList<>();
        try {
            items = new TodoListHttpService().getAllItems(Constants.CURRENT_RESULT.getAccessToken());
        } catch (Exception e) {
            items = new ArrayList<>();
        }

        ListView listview = (ListView) findViewById(R.id.listViewToDo);
        ArrayAdapter<String> adapter = new ArrayAdapter<String>(this,
                android.R.layout.simple_list_item_1, android.R.id.text1, items);
        listview.setAdapter(adapter);
    }
 ```
 
 我們也要撰寫方法以便在首次執行時將資料表初始化：
 
 ``` private void initAppTables() { try { // Get the Mobile Service Table instance to use // mToDoTable = mClient.getTable(WorkItem.class); // mToDoTable.TABvLES\_URL = "/api/"; //mTextNewToDo = (EditText)findViewById(R.id.listViewToDo);

            // Create an adapter to bind the items with the view
            //mAdapter = new WorkItemAdapter(ToDoActivity.this, R.layout.listViewToDo);
            ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
            listViewToDo.setAdapter(mAdapter);

        } catch (Exception e) {
            createAndShowDialog(new Exception(
                    "There was an error creating the Mobile Service. Verify the URL"), "Error");
        }
    }

```
 
 You'll see that this code requires some additional methods to do it's work. Let's write those now.
 
 ### Create endpoint URL generator
 
 We need to generate the endpoint URL that we'll be connecting to. Let's do that in the same class file:
 
 
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


Note that we add the access token to the request in the following code:

### Step 9: Let's write some UX methods

Android requires us to handle some callbacks in order to operate the app. These are `createAndShowDialog` and `onResume()`. This is pretty simple and very familiar if you've written Android code before. 

Let's write those now:

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

And now manage our dialog callbacks:


```
/** * Creates a dialog and shows it * * @param exception The exception to show in the dialog * @param title The dialog title */ private void createAndShowDialog(Exception exception, String title) { createAndShowDialog(exception.toString(), title); }

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
    


### 步驟 10：執行範例應用程式

最後，在 Android Studio 或 Eclipse 中同時建置並執行應用程式。註冊或登入應用程式，並為登入的使用者建立工作。登出後，再以不同使用者重新登入，並為該使用者建立工作。

請注意每位使用者之工作儲存於 API 的方法，因為 API 會從其收到的存取權杖中擷取使用者的身分識別。

為了方便參考，您可以[在此處取得 .zip 格式](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android/archive/complete.zip)的完整範例，或者從 GitHub 中複製：

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-NativeClient-Android```


<!--

### Next Steps

You can now move onto more advanced B2C topics.  You may want to try:

[Calling a node.js Web API from a node.js Web App >>]()

[Customizing the your B2C App's UX >>]()

-->

### Important Information


#### Encryption

ADAL encrypts the tokens and store in SharedPreferences by default. You can look at the StorageHelper class to see the details. Android introduced AndroidKeyStore for 4.3(API18) secure storage of private keys. ADAL uses that for API18 and above. If you want to use ADAL for lower SDK versions, you need to provide secret key at AuthenticationSettings.INSTANCE.setSecretKey

#### Session cookies in Webview

Android webview does not clear session cookies after app is closed. You can handle this with sample code below:
```java
CookieSyncManager.createInstance(getApplicationContext()); CookieManager cookieManager = CookieManager.getInstance(); cookieManager.removeSessionCookie(); CookieSyncManager.getInstance().sync(); ``` 深入了解 Cookie：http://developer.android.com/reference/android/webkit/CookieSyncManager.html
 

<!---HONumber=Sept15_HO3-->