<properties
	pageTitle="如何使用適用於 Azure Mobile Apps 的 Apache Cordova 外掛程式"
	description="如何使用適用於 Azure Mobile Apps 的 Apache Cordova 外掛程式"
	services="app-service\mobile"
	documentationCenter="javascript"
	authors="adrianhall"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-html"
	ms.devlang="javascript"
	ms.topic="article"
	ms.date="02/17/2016"
	ms.author="adrianha"/>

# 如何使用適用於 Azure Mobile Apps 的 Apache Cordova 用戶端程式庫

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

本指南說明如何使用最新的[適用於 Azure Mobile Apps 的 Apache Cordova 外掛程式]執行一般案例。如果您是 Azure Mobile Apps 的新手，請先完成 [Azure Mobile Apps 快速啟動]以建立後端、建立資料表及下載預先建置的 Apache Cordova 專案。在本指南中，我們會著重於用戶端 Apache Cordova 外掛程式。

##<a name="Setup"></a>設定和必要條件

本指南假設您已建立包含資料表的後端。本指南假設資料表的結構描述與這些教學課程中的資料表相同。本指南也假設您已將 Apache Cordova 外掛程式加入至程式碼。如果您還沒有這樣做，您可以在命令列將 Apache Cordova 外掛程式加入至您的專案：

```
cordova plugin add ms-azure-mobile-apps
```

如需有關建立[第一個 Apache Cordova 應用程式]的詳細資訊，請參閱其文件。

##<a name="create-client"></a>作法：建立用戶端

建立 `WindowsAzure.MobileServicesClient` 物件來建立用戶端連接。以您的行動應用程式 URL 取代 `appUrl`。

```
var client = WindowsAzure.MobileServicesClient(appUrl);
```

##<a name="table-reference"></a>作法：建立資料表參考

若要存取或更新資料，請建立後端資料表的參考。以您的資料表名稱取代 `tableName`

```
var table = client.getTable(tableName);
```

##<a name="querying"></a>作法：查詢資料表參考

取得資料表參考之後，您可以使用它來查詢伺服器上的資料。您可以使用「類似 LINQ」的語言來撰寫查詢。若要從資料表傳回所有資料，使用下列方法：

```
/**
 * Process the results that are received by a call to table.read()
 *
 * @param {Object} results the results as a pseudo-array
 * @param {int} results.length the length of the results array
 * @param {Object} results[] the individual results
 */
function success(results) {
   var numItemsRead = results.length;

   for (var i = 0 ; i < results.length ; i++) {
       var row = results[i];
       // Each row is an object - the properties are the columns
   }
}

function failure(error) {
    throw new Error('Error loading data: ', error);
}

table
    .read()
    .then(success, failure);
```

搭配 results 呼叫 success 函式。請勿在 success 函式中使用 `for (var i in results)`，因為當使用其他查詢函式時 (例如 `.includeTotalCount()`)，這樣會反覆檢查 resuls 中包含的資訊。

如需 Query 語法的詳細資訊，請參閱 [Query 物件文件]。

### 在伺服器上的篩選資料

您可以在資料表參考上使用 `where` 子句：

```
table
    .where({ userId: user.userId, complete: false })
    .read()
    .then(success, failure);
```

您也可以使用函式來篩選物件。在此案例中，`this` 變數指派給目前篩選的物件。以下在功能上等同於先前的範例：

```
function filterByUserId(currentUserId) {
    return this.userId === currentUserId && this.complete === false;
}

table
    .where(filterByUserId, user.userId)
    .read()
    .then(success, failure);
```

### 逐頁查看資料

利用 take() 和 skip() 方法。例如，如果您想要將資料表分割成 100 列的記錄：

```
var totalCount = 0, pages = 0;

// Step 1 - get the total number of records
table.includeTotalCount().take(0).read(function (results) {
    totalCount = results.totalCount;
    pages = Math.floor(totalCount/100) + 1;
    loadPage(0);
}, failure);

function loadPage(pageNum) {
    let skip = pageNum * 100;
    table.skip(skip).take(100).read(function (results) {
        for (var i = 0 ; i < results.length ; i++) {
            var row = results[i];
            // Process each row
        }
    }
}
```

`.includeTotalCount()` 方法用來將 totalCount 欄位加入至 results 物件。totalCount 欄位中填入未使用分頁時會傳回的記錄總數。

接著，您可以使用 pages 變數和一些 UI 按鈕來提供頁面清單。您可以使用 loadPage() 載入每個頁面的新記錄。您應該實作一些快取來加速存取已載入的記錄。


###<a name="sorting-data"></a>作法：傳回已排序的資料

使用 .orderBy() 或 .orderByDescending() 查詢方法：

```
table
    .orderBy('name')
    .read()
    .then(success, failure);
```

如需 Query 物件的詳細資訊，請參閱 [Query 物件文件]。

##<a name="inserting"></a>作法：插入資料

以適當的日期建立 JavaScript 物件，並以非同步方式呼叫 table.insert()：

```
var newItem = {
    name: 'My Name',
    signupDate: new Date()
};

table
    .insert(newItem)
    .done(function (insertedItem) {
        var id = insertedItem.id;
    }, failure);
```

在成功插入時，插入的項目及同步處理作業所需的其他欄位會一起傳回。您應該以這項資訊更新您自己的快取，後續更新時才會正確。

請注意，Azure Mobile Apps Node.js Server SDK 支援的動態結構描述適用於開發用途。就動態結構描述而言，資料表結構描述會即時更新您只要在插入或更新作業中指定資料行，就能將資料行加入至資料表。在將應用程式移至生產環境之前，我們建議您關閉動態結構描述。

##<a name="modifying"></a>作法：修改資料

類似於 .insert() 方法，您應該建立 Update 物件，然後呼叫 .update()。Update 物件必須包含要更新的記錄的識別碼 - 這是在讀取記錄或呼叫 .insert() 時取得。

```
var updateItem = {
    id: '7163bc7a-70b2-4dde-98e9-8818969611bd',
    name: 'My New Name'
};

table
    .update(updateItem)
    .done(function (updatedItem) {
        // You can now update your cached copy
    }, failure);
```

##<a name="deleting"></a>作法：刪除資料

呼叫 .del() 方法來刪除記錄。將識別碼傳入物件參考中：

```
table
    .del({ id: '7163bc7a-70b2-4dde-98e9-8818969611bd' })
    .done(function () {
        // Record is now deleted - update your cache
    }, failure);
```

##<a name="auth"></a>作法：驗證使用者

Azure App Service 支援使用各種外部識別提供者 (Facebook、Google、Microsoft 帳戶以及 Twitter) 來驗證與授權應用程式使用者。您可以在資料表上設定權限，以限制僅有通過驗證使用者可以存取特定操作。您也可以在伺服器指令碼中，使用驗證的使用者的身分識別來實作授權規則。如需詳細資訊，請參閱[開始使用驗證]教學課程。

在 Apache Cordova 應用程式中使用驗證時，下列 Cordova 外掛程式必須可用：

* [cordova-plugin-device]
* [cordova-plugin-inappbrowser]

支援兩個驗證流程：伺服器流程和用戶端流程。由於伺服器流程採用提供者的 Web 驗證介面，因此所提供的驗證體驗也最為簡單。用戶端流程依賴提供者專屬的裝置專用 SDK，可以與裝置特有的功能深入整合，例如單一登入。

##<a name="server-auth"></a>作法：向提供者驗證 (伺服器流程)

若要讓行動服務管理 Windows 市集或 HTML5 應用程式中的驗證程序，您必須向身分識別提供者註冊應用程式。接著在您的 Azure App Service 中，您必須設定提供者所提供的應用程式識別碼和密碼。如需詳細資訊，請參閱[將驗證新增至您的應用程式]教學課程。

註冊身分識別提供者之後，請直接以提供者的名稱來呼叫 .login() 方法。例如，若要以 Facebook 登入，請使用下列程式碼。

```
client.login("facebook").done(function (results) {
     alert("You are now logged in as: " + results.userId);
}, function (err) {
     alert("Error: " + err);
});
```

如果您使用的身分識別提供者不是 Facebook，請將傳給上述 login 方法的值變更為下列其中一個：`microsoftaccount`、`facebook`、`twitter`、`google` 或 `aad`。

在此案例中，Azure App Service 透過顯示所選提供者的登入頁面，並在使用識別提供者成功登入後產生 App Service 驗證權杖的方式，以管理 OAuth 2.0 驗證流程。login 函式完成時會傳回 JSON 物件 (user)，此物件會在 userId 和 authenticationToken 欄位中分別顯示使用者識別碼和 App Service 驗證權杖。您可以快取並重複使用此權杖，直到它到期為止。

##<a name="client-auth"></a>作法：向提供者驗證 (用戶端流程)

您的應用程式也可以個別連絡識別提供者，然後將傳回的權杖提供給 App Service 進行驗證。此用戶端流程可讓您為使用者提供單一登入體驗，或從識別提供者擷取其他使用者資料。

### 社交驗證基本範例

這個範例會使用 Facebook 用戶端 SDK 進行驗證：

```
client.login(
     "facebook",
     {"access_token": token})
.done(function (results) {
     alert("You are now logged in as: " + results.userId);
}, function (err) {
     alert("Error: " + err);
});
```
此範例假設個別提供者 SDK 所提供的權杖儲存在 token 變數中。

### Microsoft 帳戶範例

下列範例使用 Live SDK，支援讓 Windows 市集應用程式使用 Microsoft 帳戶來執行單一登入：

```
WL.login({ scope: "wl.basic"}).then(function (result) {
      client.login(
            "microsoftaccount",
            {"authenticationToken": result.session.authentication_token})
      .done(function(results){
            alert("You are now logged in as: " + results.userId);
      },
      function(error){
            alert("Error: " + err);
      });
});
```

這個範例從 Live Connect 取得權杖，然後呼叫 login 函式來提供權杖給 App Service。

##<a name="auth-getinfo"></a>作法：取得已驗證使用者的相關資訊

您可以使用任何 AJAX 方法從 `/.auth/me` 端點擷取目前使用者的驗證資訊。例如，若要使用提取 API：

```
var url = client.applicationUrl + '/.auth/me';
fetch(url)
    .then(function (data) {
        return data.json()
    }).then(function (user) {
        // The user object contains the claims for the authenticated user
    });
```

您也可以使用 jQuery 或另一個 AJAX API 擷取資訊。將會以 JSON 物件形式接收資料。

##<a name="register-for-push"></a>作法：註冊推播通知

安裝 [phonegap-plugin-push] 來處理推播通知。在命令列中使用 `cordova plugin add`，或在 Visual Studio 內透過 Git 外掛程式安裝程式，即可輕鬆加入。以下在 Apache Cordova 應用程式中的程式碼將為您的裝置註冊推播通知：

```
var pushOptions = {
    android: {
        senderId: '<from-gcm-console>'
    },
    ios: {
        alert: true,
        badge: true,
        sound: true
    },
    windows: {
    }
};
pushHandler = PushNotification.init(pushOptions);

pushHandler.on('registration', function (data) {
    registrationId = data.registrationId;
    // For cross-platform, you can use the device plugin to determine the device
    // Best is to use device.platform
    var name = 'gcm'; // For android - default
    if (device.platform.toLowerCase() === 'ios')
        name = 'apns';
    if (device.platform.toLowerCase().substring(0, 3) === 'win')
        name = 'wns';
    client.push.register(name, registrationId);
});

pushHandler.on('notification', function (data) {
    // data is an object and is whatever is sent by the PNS - check the format
    // for your particular PNS
});

pushHandler.on('error', function (error) {
    // Handle errors
});
```

使用通知中樞 SDK 從伺服器傳送推播通知。絕對不要直接從用戶端傳送推播通知，因為用戶端可能被利用來對通知中樞或 PNS 發動阻絕服務攻擊。

<!-- URLs. -->
[Azure Mobile Apps 快速啟動]: app-service-mobile-cordova-get-started.md
[開始使用驗證]: app-service-mobile-cordova-get-started-users.md
[將驗證新增至您的應用程式]: app-service-mobile-cordova-get-started-users.md

[適用於 Azure Mobile Apps 的 Apache Cordova 外掛程式]: https://www.npmjs.com/package/cordova-plugin-ms-azure-mobile-apps
[第一個 Apache Cordova 應用程式]: http://cordova.apache.org/#getstarted
[phonegap-facebook-plugin]: https://github.com/wizcorp/phonegap-facebook-plugin
[phonegap-plugin-push]: https://www.npmjs.com/package/phonegap-plugin-push
[cordova-plugin-device]: https://www.npmjs.com/package/cordova-plugin-device
[cordova-plugin-inappbrowser]: https://www.npmjs.com/package/cordova-plugin-inappbrowser
[Query 物件文件]: https://msdn.microsoft.com/zh-TW/library/azure/jj613353.aspx

<!---HONumber=AcomDC_0224_2016-->