<properties
	pageTitle="從行動服務升級為 Azure App Service - Node.js"
	description="了解如何輕鬆地將您的行動服務應用程式升級為 App Service 行動 App"
	services="app-service\mobile"
	documentationCenter=""
	authors="christopheranderson"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile"
	ms.devlang="node"
	ms.topic="article"
	ms.date="12/02/2015"
	ms.author="chrande"/>

# 將您現有的 Node.js Azure 行動服務升級為 App Service

App Service Mobile 是一種使用 Microsoft Azure 建置行動應用程式的新方式。若要深入了解，請參閱[何謂 Mobile Apps？]

本主題說明如何將現有的 Node.js 後端應用程式從 Azure 行動服務升級為新的 App Service Mobile Apps。執行此升級時，您現有的行動服務應用程式可以繼續運作。

當行動後端升級為 Azure App Service 時，就會具備所有 App Service 功能的存取權，而且會根據 [App Service 定價]而不是行動服務定價進行計費。

## 移轉與升級

[AZURE.INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

>[AZURE.TIP]建議您先[執行移轉](app-service-mobile-dotnet-backend-migrating-from-mobile-services.md)，然後再升級。如此一來，您就能夠在同一個 App Service 方案中放置兩個版本的應用程式，而不需支付額外成本。

### Mobile Apps Node.js 伺服器 SDK 中的改進功能

升級至新的 [Mobile Apps SDK](https://www.npmjs.com/package/azure-mobile-apps) 提供了許多改進功能，包括：

- 根據 [Express 架構](http://expressjs.com/en/index.html)，新的節點 SDK 是輕量型，而其設計目的是在它們發佈時可以跟上新的節點版本。您可以利用 Express 中介軟體來自訂應用程式行為。

- 相較於行動服務 SDK，有顯著的效能改進。

- 您現在可以將網站和您的行動後端裝載在一起。同樣地，很容易就能將 Azure Mobile SDK 新增至任何現有的 expressv4 應用程式。

- Mobile Apps SDK 是建置來進行跨平台和本機開發，可在 Windows、Linux 和 OSX 平台上進行本機開發與執行。現在常見的節點開發技術非常容易使用，像是在部署之前執行 [Mocha](https://mochajs.org/) 測試。

- 您可以將 Redis 與原生模組搭配使用，例如 [hiredis](https://www.npmjs.com/package/hiredis)；此外，由於 App Service 將會安裝您的 npm 封裝，因此，在您部署時，就不需要在部署封裝中包含二進位檔。

## <a name="overview"></a>基本升級概觀

不同於 .NET Mobile Apps SDK，將節點後端從行動服務升級到 Mobile Apps 不只是換掉封裝而已。您現在擁有整個應用程式堆疊，而不是由 Azure 來控制它，因此，您必須建立基本的 Express app 來主控您的行動後端。對於資料表和 API 控制器的概念很相似，但您現在必須匯出資料表物件，而且必須對函式 API 進行些微變更。本文將逐步解說升級的基本策略，但在移轉之前，您會想要在開始之前先閱讀[節點後端操作說明](app-service-mobile-node-backend-how-to-use-server-sdk.md)。

>[AZURE.TIP]建議您先閱讀並徹底了解本主題的其餘部分，再開始升級。請記下您使用的下列任何功能。

行動服務用戶端 SDK 與新的 Mobile Apps 伺服器 SDK「不」相容。為了提供您 app 的服務持續性，您不應該將變更發佈至目前正在服務已發佈之用戶端的網站。而是應該建立新的行動應用程式做為重複項目。您可以在同一個 App Service 方案中放置此應用程式，以避免產生額外的財務成本。

您之後會有兩個版本的應用程式：一個維持不變並為已發佈的現有 app 提供服務，另一個則可升級且目標為新的用戶端版本。您可依自己的步調移動並測試程式碼，但應該確定您所進行的任何錯誤修正都會套用到這兩個版本。當您覺得已將現有用戶端 app 的所需數量升級到最新版本，就可以視需要刪除原本移轉的 app。如果裝載於與您行動 app 相同的 App Service 方案中，就不會產生任何額外的金錢成本。

完整的升級程序大致如下：

1. 建立新的行動 App
2. 更新專案以使用新的伺服器 SDK
3. 發行新版的用戶端應用程式
4. (選擇性) 刪除原始已移轉的行動服務 App

## <a name="mobile-app-version"></a> 開始升級
升級的第一個步驟是建立將主控新版應用程式的行動 App 資源。如果您已經移轉現有的行動服務，則您會想要在同一個主控方案中建立這個版本。開啟 [Azure 入口網站]，並瀏覽到您移轉的應用程式。請記下其正在其上執行的 App Service 方案。

### 建立第二個應用程式執行個體
接著，建立第二個應用程式執行個體。當系統提示選取您的 App Service 方案或「主控方案」時，請選擇已移轉之應用程式的方案。

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

您可能會想要使用與行動服務中相同的資料庫和通知中心。您可以複製這些值，方法是開啟 [Azure 入口網站]、瀏覽至原始的應用程式，然後按一下 [設定] > [應用程式設定]。在 [**連接字串**] 下，複製 `MS_NotificationHubConnectionString` 和 `MS_TableConnectionString`。瀏覽至新的升級網站並將它們貼上，覆寫任何現有的值。針對您的 app 需要的任何其他應用程式設定重複執行此程序。如果不使用已移轉的服務，您可以從 [Azure 入口網站]上 [行動服務] 區段的 [設定] 索引標籤，讀取連接字串和 app 設定。

### 利用節點建立基本的行動 App 後端

每個 Azure App Service Mobile App Node.js 後端都會以 ExpressJS 應用程式的形式啟動。您可以依照下列方式來建立基本的 [Express](http://expressjs.com/en/index.html) 應用程式：

1. 在命令或 PowerShell 視窗中，為您的專案建立新的目錄。

        mkdir basicapp

2. 執行 npm init 以初始化封裝結構。

        cd basicapp
        npm init

    Npm init 命令會詢問一組問題以初始化專案。請參閱以下的範例輸出

    ![npm init 輸出][0]

3. 從 npm 儲存機制安裝 express 和 azure-mobile-apps 資源庫。

        npm install --save express azure-mobile-apps

4. 建立 app.js 檔案以實作基本行動伺服器。

        var express = require('express'),
            azureMobileApps = require('azure-mobile-apps');

        var app = express(),
            mobile = azureMobileApps();

        // Important all tables in the 'tables' directory
        mobile.tables.import('./tables');
        mobile.api.import('./api');

        // Provide initialization of any tables that are statically defined
        mobile.tables.initialize().then(function () {
           // Add the mobile API so it is accessible as a Web API
           app.use(mobile);

           // Start listening on HTTP
           app.listen(process.env.PORT || 3000);
           console.log('Now listening on ' + (process.env.PORT || 3000)));
        });


## 更新伺服器專案

Mobile Apps 提供了新的 [行動 App 伺服器 SDK]，其中提供許多與行動服務執行階段相同的功能，但您現在擁有完整的執行階段。Mobile Apps 不會為您強制執行節點版本或任何程式碼更新。如果您已遵循上述步驟，就會有節點行動執行階段的基本版本可供使用。您現在可以開始將資料表和 API 邏輯從行動服務移至行動 App、自訂伺服器組態、啟用推播、設定驗證，以及其他作業。

### 基本組態

伺服器具有大量組態設定，但有各種不同的預設值，可讓您輕鬆地開始使用。許多設定將在 [Azure 入口網站]中，透過 [資料]、[驗證/授權] 及 [推送] 設定功能表加以設定。對於本機開發，如果您想要使用資料、驗證和推送，可能就需要設定您的本機開發環境。

您可以透過環境變數設定伺服器組態，您可以在行動 App 後端中透過 [App 設定] 來設定。

您可以藉由將[組態物件](http://azure.github.io/azure-mobile-apps-node/global.html#configuration)傳遞至初始設定式，或者在專案的根目錄中[建立一個名為 azureMobile.js 檔案](app-service-mobile-node-backend-how-to-use-server-sdk/#howto-config-localdev)，進一步自訂 Mobile Apps SDK。

### 使用日期與資料表

SDK 隨附記憶體內部的資料提供者，以便取得快速且輕鬆開始使用的體驗。您應該一開始就改用 SQL DB，因為記憶體內部的提供者將會在重新啟動時遺失所有資料，而且不會在多個執行個體間保持一致。

若要開始將商務邏輯從您的行動服務移到 Mobile Apps，首先要在 `./tables` 目錄中利用資料表的名稱 (加上 '.js') 來建立檔案。您可以在 [GitHub](https://github.com/Azure/azure-mobile-apps-node/blob/master/samples/todo/tables/TodoItem.js) 上看到行動 App 資料表的完整範例。最簡單的版本如下：

    var azureMobileApps = require('azure-mobile-apps');

    // Create a new table definition
    var table = azureMobileApps.table();

    module.exports = table;

若要開始移植部分邏輯，針對您的每一個 `<tablename>.<operation>.js`，您需要適用於資料表的函式。讓我們加入一個讀取函式做為範例。

在含有 TodoItem 資料表及一個讀取操作的行動服務中 (其會根據使用者識別碼來篩選)，如下：

  function(query, user, request) { query.where({ userId: user.userId}); request.execute(); }

我們加入至 Azure Mobile Apps 資料表程式碼的函式如下所示：

    table.read(function (context) {
        context.query.where({ userId: context.user.id });
        return context.execute();
    });

檢查程式碼，您可以看到大部分的函式參數

### CORS

您可以透過 SDK 中的 [CORS 組態設定](http://azure.github.io/azure-mobile-apps-node/global.html#corsConfiguration)來啟用 CORS。

是否使用 CORS 的主要考量範疇是必須允許 `eTag` 和 `Location` 標頭，用戶端 SDK 才能正常運作。

### 推播通知

Azure 通知中樞 SDK 具有一些自從行動服務以來的重大更新，因此部分通知中樞函式簽章可能會不同。否則，此功能會與行動服務類似。如果通知中樞的 App 設定存在，Azure Mobile SDK 將會佈建通知中樞執行個體，並將它公開在 `context.push`。您可以在 [GitHub](https://github.com/Azure/azure-mobile-apps-node/blob/master/samples/push-on-insert/tables/TodoItem.js) 上找到含有相關區段的範例，如下所示：

    table.insert(function (context) {
        // For details of the Notification Hubs JavaScript SDK,
        // see https://azure.microsoft.com/zh-TW/documentation/articles/notification-hubs-nodejs-how-to-use-notification-hubs/
        logger.silly('Running TodoItem.insert');

        // This push uses a template mechanism, so we need a template/
        var payload = '<toast><visual><binding template="Toast01"><text id="1">INSERT</text></binding></visual></toast>';

        // Execute the insert.  The insert returns the results as a Promise,
        // Do the push as a post-execute action within the promise flow.
        return context.execute()
            .then(function (results) {
                // Only do the push if configured
                if (context.push) {
                    context.push.wns.send(null, payload, 'wns/toast', function (error) {
                        if (error) {
                            logger.error('Error while sending push notification: ', error);
                        } else {
                            logger.silly('Push notification sent successfully!');
                        }
                    });
                }
                // Don't forget to return the results from the context.execute()
                return results;
            })
            .catch(function (error) {
                logger.error('Error while running context.execute: ', error);
            });
    });


### 排程的工作
Mobile Apps 中並未內建排程的工作，因此您在行動服務後端中的任何現有工作都必須個別升級。其中一個選項是在行動應用程式程式碼網站上建立排程 [Web 工作]。您也可以設定用來保存工作程式碼的 API，並設定依預期的排程在端點上執行的 [Azure 排程器]。

## <a name="authentication"></a>驗證考量

行動服務的驗證元件現在已移到 App Service 驗證/授權功能中。您可以閱讀[在您的行動服務應用程式中新增驗證](app-service-mobile-ios-get-started-users.md)主題，以了解為網站啟用此功能的方式。

對於某些提供者 (例如 AAD、Facebook 及 Google)，您應該能夠運用來自您複製應用程式的現有註冊。您只需瀏覽至身分識別提供者的入口網站，並將新的重新導向 URL 新增至註冊即可。接著，利用用戶端識別碼和密碼來設定 App Service 驗證/授權。

### 控制器動作授權和使用者識別碼

若要限制存取您的資料表，您可以在資料表層級中利用 `table.access = 'authenticated';` 來設定它。您可以在 [GitHub](https://github.com/Azure/azure-mobile-apps-node/blob/master/samples/authentication/tables/TodoItem.js) 上看到完整的範例。

您可以透過[這裡](http://azure.github.io/azure-mobile-apps-node/module-azure-mobile-apps_auth_user.html#~getIdentity)所述的 `user.getIdentity` 方法，來存取使用者身分識別資訊。

## <a name="updating-clients"></a>更新用戶端
在您擁有可運作的行動 App 後端之後，就能在取用它的新版用戶端應用程式上運作。Mobile Apps 也會包含新版的用戶端 SDK，而且與上述的伺服器升級類似，您必須先移除所有對行動服務 SDK 的參考，然後安裝 Mobile Apps 版本。

版本間的其中一個主要變更是建構函式不再需要應用程式金鑰。您現在只需傳入行動 App 的 URL。例如，在 .NET 用戶端上，`MobileServiceClient` 建構函式現在如下：

        public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://contoso.azurewebsites.net", // URL of the Mobile App
        );

您可以透過下列連結，閱讀有關安裝新的 SDK 以及使用新結構的相關資訊：

- [iOS 3.0.0 版或更新版本](app-service-mobile-ios-how-to-use-client-library.md)
- [.NET (Windows/Xamarin) 2.0.0 版或更新版本](app-service-mobile-dotnet-how-to-use-client-library.md)

如果您的應用程式會使用推播通知，請記下每個平台特定的註冊指示，因為也已有一些變更。

當您準備好新的用戶端版本時，請嘗試對已升級的伺服器專案執行該版本。驗證它的運作方式之後，您就能將新版的應用程式發行給客戶。最後，在您的客戶有機會接收這些更新後，您就能刪除 app 的行動服務版本。現在，您已使用最新的 Mobile Apps 伺服器 SDK 完全升級為 App Service 行動應用程式。

<!-- Images -->
[0]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/npm-init.png

<!-- URLs. -->

[Azure portal]: https://portal.azure.com/
[Azure classic portal]: https://manage.windowsazure.com/
[何謂 Mobile Apps？]: app-service-mobile-value-prop.md
[I already use web sites and mobile services – how does App Service help me?]: /zh-TW/documentation/articles/app-service-mobile-value-prop-migration-from-mobile-services
[行動 App 伺服器 SDK]: https://www.npmjs.com/package/azure-mobile-apps
[Create a Mobile App]: app-service-mobile-xamarin-ios-get-started.md
[Add push notifications to your mobile app]: app-service-mobile-xamarin-ios-get-started-push.md
[Add authentication to your mobile app]: app-service-mobile-xamarin-ios-get-started-users.md
[Azure 排程器]: /zh-TW/documentation/services/scheduler/
[Web 工作]: ../app-service-web/websites-webjobs-resources.md
[Send cross-platform push notifications]: app-service-mobile-xamarin-ios-push-notifications-to-user.md
[How to use the .NET server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Migrate from Mobile Services to an App Service Mobile App]: app-service-mobile-dotnet-backend-migrating-from-mobile-services.md
[Migrate your existing Mobile Service to App Service]: app-service-mobile-dotnet-backend-migrating-from-mobile-services.md
[App Service 定價]: https://azure.microsoft.com/zh-TW/pricing/details/app-service/
[.NET server SDK overview]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md

[Azure 入口網站]: https://portal.azure.com/
[OData]: http://www.odata.org
[Promise]: https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Promise
[basicapp sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/basic-app
[todo sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/todo
[samples directory on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples
[static-schema sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/static-schema
[QueryJS]: https://github.com/Azure/queryjs
[Node.js Tools 1.1 for Visual Studio]: https://github.com/Microsoft/nodejstools/releases/tag/v1.1-RC.2.1
[mssql Node.js package]: https://www.npmjs.com/package/mssql
[Microsoft SQL Server 2014 Express]: http://www.microsoft.com/zh-TW/server-cloud/Products/sql-server-editions/sql-server-express.aspx
[ExpressJS Middleware]: http://expressjs.com/guide/using-middleware.html
[Winston]: https://github.com/winstonjs/winston

<!---HONumber=AcomDC_1203_2015-->