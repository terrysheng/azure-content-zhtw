<properties
	pageTitle="如何使用適用於 Azure 行動應用程式的 JavaScript SDK"
	description="如何使用適用於 Azure 行動應用程式的 v"
	services="app-service\mobile"
	documentationCenter="javascript"
	authors="adrianhall"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="html"
	ms.devlang="javascript"
	ms.topic="article"
	ms.date="03/07/2016"
	ms.author="adrianha"/>

# 如何使用適用於 Azure 行動應用程式的 JavaScript 用戶端程式庫

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

本指南將教導您使用最新的「適用於 Azure 行動應用程式的 JavaScript SDK」執行一般案例。如果您不熟悉 Azure 行動應用程式，請先完成 [Azure 行動應用程式快速入門]建立後端，並建立資料表。在本指南中，我們會著重在使用 HTML/JavaScript Web 應用程式中的行動後端。

##<a name="Setup"></a>設定和必要條件

本指南假設您已建立包含資料表的後端。本指南假設資料表的結構描述與這些教學課程中的資料表相同。

安裝 Azure 行動應用程式 JavaScript SDK 可透過 `npm` 命令完成︰

```
npm install azure-mobile-apps-client --save
```

安裝之後，程式庫位於 `node_modules/azure-mobile-apps-client/dist/MobileServices.Web.js`。將這個檔案複製到您的網站區域。

```
<script src="path/to/MobileServices.Web.js"></script>
```

程式庫也可在 CommonJS 環境 (例如 Browserify 和 Webpack) 內部做為 ES2015 模組和 AMD 程式庫使用。例如：

```
# For ECMAScript 5.1 CommonJS
var WindowsAzure = require('azure-mobile-apps-client');
# For ES2015 modules
import * as WindowsAzure from 'azure-mobile-apps-client';
```

[AZURE.INCLUDE [app-service-mobile-html-js-library](../../includes/app-service-mobile-html-js-library.md)]

##<a name="auth"></a>作法：驗證使用者

Azure App Service 支援使用各種外部識別提供者 (Facebook、Google、Microsoft 帳戶以及 Twitter) 來驗證與授權應用程式使用者。您可以在資料表上設定權限，以限制僅有通過驗證使用者可以存取特定操作。您也可以在伺服器指令碼中，使用驗證的使用者的身分識別來實作授權規則。如需詳細資訊，請參閱[開始使用驗證]教學課程。

支援兩個驗證流程：伺服器流程和用戶端流程。由於伺服器流程採用提供者的 Web 驗證介面，因此所提供的驗證體驗也最為簡單。用戶端流程依賴提供者專屬的 SDK，可以與裝置特有的功能深入整合，例如單一登入。

[AZURE.INCLUDE [app-service-mobile-html-js-auth-library](../../includes/app-service-mobile-html-js-auth-library.md)]

##<a name="register-for-push"></a>作法：註冊推播通知

安裝 [phonegap-plugin-push] 來處理推播通知。在命令列中使用 `cordova plugin add` 命令，或在 Visual Studio 內透過 Git 外掛程式安裝程式，即可輕鬆加入。以下在 Apache Cordova 應用程式中的程式碼將為您的裝置註冊推播通知：

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
[Azure 行動應用程式快速入門]: app-service-mobile-cordova-get-started.md
[開始使用驗證]: app-service-mobile-cordova-get-started-users.md
[將驗證新增至您的應用程式]: app-service-mobile-cordova-get-started-users.md

[Apache Cordova Plugin for Azure Mobile Apps]: https://www.npmjs.com/package/cordova-plugin-ms-azure-mobile-apps
[your first Apache Cordova app]: http://cordova.apache.org/#getstarted
[phonegap-facebook-plugin]: https://github.com/wizcorp/phonegap-facebook-plugin
[phonegap-plugin-push]: https://www.npmjs.com/package/phonegap-plugin-push
[cordova-plugin-device]: https://www.npmjs.com/package/cordova-plugin-device
[cordova-plugin-inappbrowser]: https://www.npmjs.com/package/cordova-plugin-inappbrowser
[Query 物件文件]: https://msdn.microsoft.com/zh-TW/library/azure/jj613353.aspx

<!----HONumber=AcomDC_0309_2016-->
