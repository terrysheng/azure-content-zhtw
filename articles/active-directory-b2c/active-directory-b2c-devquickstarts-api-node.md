<properties
	pageTitle="B2C 預覽：使用 Node.js 保護 Web API 安全 | Microsoft Azure"
	description="如何建置可接受來自 B2C 租用戶之權杖的 Node.js Web API"
	services="active-directory-b2c"
	documentationCenter=""
	authors="brandwe"
	manager="msmbaldwin"
	editor=""/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
  	ms.tgt_pltfrm="na"
	ms.devlang="javascript"
	ms.topic="article"
	ms.date="02/17/2016"
	ms.author="brandwe"/>

# B2C 預覽：使用 Node.js 保護 Web API 安全

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]


> [AZURE.NOTE] 本文不涵蓋如何使用 Azure AD B2C 實作登入、註冊和管理設定檔，而是著重在已驗證使用者之後呼叫 Web API。您應該先從 [.NET Web 應用程式使用者入門教學課程](active-directory-b2c-devquickstarts-web-dotnet.md)開始 (如果還沒有進行)，以了解 Azure Active Directory B2C 的基本概念。


> [AZURE.NOTE]	此範例設計為使用我們的 [iOS B2C 範例應用程式](active-directory-b2c-devquickstarts-ios.md)來連接。請先執行本逐步解說，然後遵循該範例操作。

**Passport** 是 Node.js 的驗證中介軟體。您可以暗中將極具彈性且模組化的 Passport 安裝在任何 Express 或 Resitify Web 應用程式中。有一套完整的策略支援以使用者名稱和密碼、Facebook、Twitter 等來進行驗證。我們已為 Azure Active Directory (Azure AD) 開發一套策略。您將會安裝此模組，然後加入 Azure AD `passport-azure-ad` 外掛程式。

若要這樣做，您需要執行下列動作：

1. 向 Azure AD 註冊應用程式。
2. 設定您的應用程式來使用 Passport 的 `azure-ad-passport` 外掛程式。
3. 設定用戶端應用程式呼叫「待辦事項清單」Web API。

本教學課程的程式碼[保留在 GitHub](https://github.com/AzureADQuickStarts/B2C-WebAPI-nodejs)。若要遵循執行，您可以[下載應用程式基本架構的 .zip 檔](https://github.com/AzureADQuickStarts/B2C-WebAPI-nodejs/archive/skeleton.zip)，或複製基本架構：

```git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebAPI-nodejs.git```

本教學課程最後會提供完整的應用程式。

> [AZURE.WARNING] 	在我們的 B2C 預覽中，您必須對 Web API 工作伺服器和與其連接之用戶端，使用相同的**用戶端識別碼**/**應用程式識別碼**和原則。在我們的 iOS 及 Android 教學課程中也必須如此。如果您先前已在任何一個快速入門中建立應用程式，請直接使用那些值，不要再建立新的值。


## 取得 Azure AD B2C 目錄

您必須先建立目錄或租用戶，才可使用 Azure AD B2C。目錄為所有使用者、應用程式、群組等項目的容器。如果您還沒有此資源，請先[建立 B2C 目錄](active-directory-b2c-get-started.md)再繼續進行。

## 建立應用程式

接下來，您需要在 B2C 目錄中建立應用程式，以提供一些必要資訊給 Azure AD，讓它與應用程式安全地通訊。在此案例中，因為用戶端應用程式和 Web API 會組成一個邏輯應用程式，所以將由單一**應用程式識別碼**表示。如果要建立應用程式，請遵循[這些指示](active-directory-b2c-app-registration.md)。請務必：

- 在應用程式中加入 **Web 應用程式/Web API**
- 在 [回覆 URL] 中輸入 `http://localhost/TodoListService`。這是此程式碼範例的預設 URL。
- 為您的應用程式建立**應用程式密碼**，並複製下來。稍後您將會用到此資訊。稍後您將會用到此資訊。請注意，這個值在使用之前必須經過 [XML 逸出](https://www.w3.org/TR/2006/REC-xml11-20060816/#dt-escape)。
- 複製指派給您的應用程式的**應用程式識別碼**。稍後您也會需要此資訊。

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## 建立您的原則

在 Azure AD B2C 中，每個使用者經驗皆由[原則](active-directory-b2c-reference-policies.md)所定義。此應用程式包含三種身分識別體驗：註冊、登入，以及使用 Facebook 登入。您必須為每個類型建立一個原則，如[原則參考文章](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy)所述。建立您的三個原則時，請務必：

- 在註冊原則中，選擇 [顯示名稱] 和其他註冊屬性。
- 在每個原則中，選擇 [顯示名稱] 和 [物件識別碼] 應用程式宣告。您也可以選擇其他宣告。
- 建立每個原則後，請複製原則的**名稱**。其前置詞應該為 `b2c_1_`。稍後您將需要這些原則名稱。

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

建立您的三個原則後，就可以開始建置您的應用程式。

請注意，本文不涵蓋如何使用您剛才建立的原則。如需了解 Azure AD B2C 中的原則如何運作，請從 [.NET Web 應用程式使用者入門教學課程](active-directory-b2c-devquickstarts-web-dotnet.md)開始。

## 下載適用於您平台的 Node.js

若要成功使用此範例，您必須具備已成功安裝的 Node.js。

從 [nodejs.org](http://nodejs.org) 安裝 Node.js。

## 安裝適用於您平台的 MongoDB

若要成功使用此範例，您也必須具備已成功安裝的 MongoDB。您將會使用 MongoDB 讓 REST API 得以在不同伺服器執行個體之間持續使用。

從 [mongodb.org](http://www.mongodb.org) 安裝 MongoDB。

> [AZURE.NOTE] 本逐步解說假設您會使用 MongoDB 的預設安裝和伺服器端點，在撰寫本文時為：`mongodb://localhost`。

## 在您的 Web API 中安裝 Restify 模組

您將使用 Restify 來建置 REST API。Restify 是衍生自 Express 的最小且具彈性的 Node.js 應用程式架構。它有一組強大的功能，可在 Connect 之上建置 REST API。

### 安裝 Restify

從命令列，切換至 `azuread` 目錄。如果 `azuread` 目錄不存在，請建立它。

`cd azuread` 或 `mkdir azuread;`

輸入下列命令：

`npm install restify`

此命令會安裝 Restify。

#### 您有收到錯誤訊息嗎？

在某些作業系統上使用 `npm` 時，您可能會收到 `Error: EPERM, chmod '/usr/local/bin/..'` 的錯誤，並且要求您以系統管理員身分執行帳戶。若發生這個情況，請使用 `sudo` 命令，以更高的權限層級執行 `npm`。

#### 有發生 DTrace 錯誤嗎？

安裝 Restify 時，您可能會看到類似下面的內容：

```Shell
clang: error: no such file or directory: 'HD/azuread/node_modules/restify/node_modules/dtrace-provider/libusdt'
make: *** [Release/DTraceProviderBindings.node] Error 1
gyp ERR! build error
gyp ERR! stack Error: `make` failed with exit code: 2
gyp ERR! stack     at ChildProcess.onExit (/usr/local/lib/node_modules/npm/node_modules/node-gyp/lib/build.js:267:23)
gyp ERR! stack     at ChildProcess.EventEmitter.emit (events.js:98:17)
gyp ERR! stack     at Process.ChildProcess._handle.onexit (child_process.js:789:12)
gyp ERR! System Darwin 13.1.0
gyp ERR! command "node" "/usr/local/lib/node_modules/npm/node_modules/node-gyp/bin/node-gyp.js" "rebuild"
gyp ERR! cwd /Volumes/Development HD/azuread/node_modules/restify/node_modules/dtrace-provider
gyp ERR! node -v v0.10.11
gyp ERR! node-gyp -v v0.10.0
gyp ERR! not ok
npm WARN optional dep failed, continuing dtrace-provider@0.2.8
```

Restify 提供強大機制來使用 DTrace 追蹤 REST 呼叫。不過，許多作業系統沒有 DTrace 可以使用。您可以放心地忽略這些錯誤。

此命令的輸出應類似這樣：

	restify@2.6.1 node_modules/restify
	├── assert-plus@0.1.4
	├── once@1.3.0
	├── deep-equal@0.0.0
	├── escape-regexp-component@1.0.2
	├── qs@0.6.5
	├── tunnel-agent@0.3.0
	├── keep-alive-agent@0.0.1
	├── lru-cache@2.3.1
	├── node-uuid@1.4.0
	├── negotiator@0.3.0
	├── mime@1.2.11
	├── semver@2.2.1
	├── spdy@1.14.12
	├── backoff@2.3.0
	├── formidable@1.0.14
	├── verror@1.3.6 (extsprintf@1.0.2)
	├── csv@0.3.6
	├── http-signature@0.10.0 (assert-plus@0.1.2, asn1@0.1.11, ctype@0.5.2)
	└── bunyan@0.22.0 (mv@0.0.5)

## 在您的 Web API 中安裝 Passport

[Passport](http://passportjs.org/) 是 Node.js 的驗證中介軟體。您可以暗中將極具彈性且模組化的 Passport 安裝在任何 Express 或 Resitify Web 應用程式中。有一套完整的策略支援以使用者名稱和密碼、Facebook、Twitter 等來進行驗證。我們已為 Azure AD 開發一套策略。您將會安裝此模組，然後加入 Azure AD 策略外掛程式。

從命令列，切換至 `azuread` 目錄 (如果還不在此目錄下)。

輸入下列命令以安裝 Passport：

`npm install passport`

此命令的輸出應類似這樣：

	passport@0.1.17 node_modules\passport
	├── pause@0.0.1
	└── pkginfo@0.2.3

## 將 passport-azuread 加入您的 Web API

接下來，使用 `passport-azuread` 來加入 OAuth 策略，這是一套將 Azure AD 連接到 Passport 的策略。在這個 Rest API 範例中，請針對持有人權杖使用此策略。

> [AZURE.NOTE] 雖然 OAuth2 提供可發行任何已知權杖類型的架構，但只會普遍使用特定的權杖類型。用於保護端點的權杖是持有者權杖。這些是 OAuth2 中最普遍發行的權杖類型。許多實作假設持有者權杖會是唯一發行的權杖類型。

從命令列，切換至 `azuread` 目錄 (如果還不在此目錄下)。

輸入下列命令以安裝 Passport `passport-azure-ad` 模組：

`npm install passport-azure-ad`

此命令的輸出應類似這樣：

``
passport-azure-ad@1.0.0 node_modules/passport-azure-ad
├── xtend@4.0.0
├── xmldom@0.1.19
├── passport-http-bearer@1.0.1 (passport-strategy@1.0.0)
├── underscore@1.8.3
├── async@1.3.0
├── jsonwebtoken@5.0.2
├── xml-crypto@0.5.27 (xpath.js@1.0.6)
├── ursa@0.8.5 (bindings@1.2.1, nan@1.8.4)
├── jws@3.0.0 (jwa@1.0.1, base64url@1.0.4)
├── request@2.58.0 (caseless@0.10.0, aws-sign2@0.5.0, forever-agent@0.6.1, stringstream@0.0.4, tunnel-agent@0.4.1, oauth-sign@0.8.0, isstream@0.1.2, extend@2.0.1, json-stringify-safe@5.0.1, node-uuid@1.4.3, qs@3.1.0, combined-stream@1.0.5, mime-types@2.0.14, form-data@1.0.0-rc1, http-signature@0.11.0, bl@0.9.4, tough-cookie@2.0.0, hawk@2.3.1, har-validator@1.8.0)
└── xml2js@0.4.9 (sax@0.6.1, xmlbuilder@2.6.4)
``

## 將 MongoDB 模組加入您的 Web API

您將使用 MongoDB 作為資料存放區。基於這個理由，您必須同時安裝 Mongoose (廣泛用於管理模型和結構描述的外掛程式) 和 MongoDB 的資料庫驅動程式 (也稱為 MongoDB)。

* `npm install mongoose`
* `npm install mongodb`

## 安裝其他模組

接下來，安裝其餘所需的模組。

從命令列，切換至 `azuread` 目錄 (如果還不在此目錄下)：

`cd azuread`

輸入下列命令，在您的 `node_modules` 目錄中安裝模組：

* `npm install crypto`
* `npm install assert-plus`
* `npm install posix-getopt`
* `npm install util`
* `npm install path`
* `npm install connect`
* `npm install xml-crypto`
* `npm install xml2js`
* `npm install xmldom`
* `npm install async`
* `npm install request`
* `npm install underscore`
* `npm install grunt-contrib-jshint@0.1.1`
* `npm install grunt-contrib-nodeunit@0.1.2`
* `npm install grunt-contrib-watch@0.2.0`
* `npm install grunt@0.4.1`
* `npm install xtend@2.0.3`
* `npm install bunyan`
* `npm update`


## 使用您的相依性建立 server.js 檔案

`server.js` 檔案將提供您的 Web API 伺服器的大部分功能。您將在此檔案中加入大部分的程式碼。基於生產目的，您應該將功能重整成較小的檔案，例如單獨分開的路由和控制器。基於此教學課程的目的，請使用 server.js 開發此功能。

從命令列，切換至 `azuread` 目錄 (如果還不在此目錄下)：

`cd azuread`

在編輯器中建立 `server.js` 檔案。加入下列資訊：

```Javascript
'use strict';
/**
* Module dependencies.
*/
var fs = require('fs');
var path = require('path');
var util = require('util');
var assert = require('assert-plus');
var mongoose = require('mongoose/');
var bunyan = require('bunyan');
var restify = require('restify');
var config = require('./config');
var passport = require('passport');
var OIDCBearerStrategy = require('passport-azure-ad').BearerStrategy;
```

儲存檔案。您稍後會再回到此檔案。

## 建立 config.js 檔案來儲存您的 Azure AD 設定

這個程式碼檔會將設定參數從您的 Azure AD 入口網站傳遞到 `Passport.js` 檔案。在本逐步解說的第一個部分，您在將 Web API 加入入口網站時已建立這些組態值。在您複製程式碼之後，我們將說明要放入這些參數值的內容。

從命令列，切換至 `azuread` 目錄 (如果還不在此目錄下)：

`cd azuread`

在編輯器中建立 `config.js` 檔案。加入下列資訊：

```Javascript
// Don't commit this file to your public repos. This config is for first-run
exports.creds = {
mongoose_auth_local: 'mongodb://localhost/tasklist', // Your mongo auth uri goes here
audience: '<your audience URI>',
identityMetadata: 'https://login.microsoftonline.com/common/.well-known/openid-configuration', // For using Microsoft you should never need to change this.
tenantName:'<tenant name>',
policyName:'b2c_1_<sign in policy name>',
};

```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

### 必要值

`IdentityMetadata`：`passport-azure-ad` 會在這裡尋找識別提供者的設定資料。它也會在這裡尋找金鑰來驗證 JSON Web 權杖。如果您使用 Azure AD，您可能不想變更此項目。

`audience`：入口網站上的統一資源識別項 (URI)，用於識別您的服務。我們的範例會使用 `http://localhost/TodoListService`。

`tenantName`：您的租用戶名稱 (例如 **contoso.onmicrosoft.com**)。

`policyName`：您想用來驗證傳入伺服器之權杖的原則。此原則應與您針對用戶端應用程式登入所用的原則相同。

> [AZURE.NOTE] 在我們的 B2C 預覽中，請將相同的原則用於用戶端與伺服器設定。如果您已完成逐步解說並建立這些原則，則不需要再做一次。由於您已完成此逐步解說，您應該不需要在網站上為用戶端逐步解說建立新的原則。

## 將設定加入 server.js 檔案

您必須在整個應用程式中讀取您剛建立的 `config.js` 檔案中的值。若要這樣做，請在應用程式中加入 `.config` 檔案作為必要資源，然後將全域變數設定為 `config.js` 文件中的那些值。

從命令列，切換至 `azuread` 目錄 (如果還不在此目錄下)：

`cd azuread`

在編輯器中開啟 `server.js` 檔案。加入下列資訊：

```Javascript
var config = require('./config');
```
將包含下列程式碼的新區段加入 `server.js`：

```Javascript
// We pass these options in to the ODICBearerStrategy.
var options = {
// The URL of the metadata document for your app. We will put the keys for token validation from the URL found in the jwks_uri tag of the in the metadata.
    identityMetadata: config.creds.identityMetadata,
    clientID: config.creds.clientID,
    tenantName: config.creds.tenantName,
    policyName: config.creds.policyName,
    validateIssuer: config.creds.validateIssuer,
    audience: config.creds.audience
};
// array to hold logged-in users and the current logged-in user (owner)
var users = [];
var owner = null;
// Our logger
var log = bunyan.createLogger({
name: 'Microsoft Azure Active Directory Sample'
});
```

## 使用 Moongoose 新增 MongoDB 模型和結構描述資訊

及早準備有助於在 REST API 服務中組合這三個檔案。

在此逐步解說中，請使用 MongoDB 來儲存工作，稍早所述。

在您建立的 `config.js` 檔案中，您將資料庫命名為 **tasklist**。這也是您在 `mongoose_auth_local` 連線 URL 結尾處輸入的內容。您不需要在 MongoDB 中事先建立此資料庫。第一次執行應用程式伺服器時為您建立資料庫 (如果不存在)。

在您告訴伺服器要使用哪個 MongoDB 資料庫之後，您必須撰寫一些額外程式碼，以建立伺服器工作的模型和結構描述。

### 擴充模型

此結構描述模型非常簡單。您可以視需要來擴充它。

`name`：指派給工作的人員。這是**字串**。

`task`：工作本身。這是**字串**。

`date`：工作到期的日期。這是 **datetime**。

`completed`：工作是否已完成。這是**布林**。

### 在程式碼中建立結構描述

從命令列，切換至 `azuread` 目錄 (如果還不在此目錄下)：

`cd azuread`

在編輯器中開啟 `server.js` 檔案。在組態項目下方加入下列資訊：

```Javascript
// MongoDB setup
// Set up some configuration
var serverPort = process.env.PORT || 8080;
var serverURI = (process.env.PORT) ? config.creds.mongoose_auth_mongohq : config.creds.mongoose_auth_local;
// Connect to MongoDB
global.db = mongoose.connect(serverURI);
var Schema = mongoose.Schema;
log.info('MongoDB Schema loaded');
```
這會連線到 MongoDB 伺服器，並傳回結構描述物件。

### 使用結構描述在程式碼中建立模型

以下是您之前撰寫的程式碼，請加入下列程式碼：

```Javascript
// Here we create a schema to store our tasks and users. Pretty simple schema for now.
var TaskSchema = new Schema({
owner: String,
task: String,
completed: Boolean,
date: Date
});
// Use the schema to register a model
mongoose.model('Task', TaskSchema);
var Task = mongoose.model('Task');
```
您首先建立結構描述，然後建立模型物件，在您定義**路由**時用來儲存整個程式碼中的資料。

## 新增 REST API 工作伺服器的路由

既然您已經有可以使用的資料庫模型，請新增將用於 REST API 伺服器的路由。

### 關於 Restify 中的路由

路由在 Restify 中的運作與它們使用 Express 堆疊時的運作方式完全相同。您可以使用您預期用戶端應用程式呼叫的 URI 來定義路由。在大部分情況下，您應該在個別的檔案中定義您的路由。就本教學課程的目的而言，您會將路由放在 `server.js` 檔案中。在生產環境中，建議您將這些路由分散到其各自的檔案。

Restify 路由的典型模式是：

```Javascript
function createObject(req, res, next) {
// do work on Object
_object.name = req.params.object; // passed value is in req.params under object
///...
return next(); // keep the server going
}
....
server.post('/service/:add/:object', createObject); // calls createObject on routes that match this.
```

這是最基本層級的模式。Resitfy 及 Express 可提供更深入的功能，例如定義應用程式類型和執行不同端點之間的複雜路由。基於本教學課程的目的，我們會將這些路由保持簡單。

#### 將預設路由加入伺服器

您現在可以新增**建立**、**擷取**、**更新**和**刪除**的基本 CRUD 路由。

從命令列，切換至 `azuread` 目錄 (如果還不在此目錄下)：

`cd azuread`

在編輯器中開啟 `server.js` 檔案。在您先前輸入的資料庫項目底下加入下列資訊：

```Javascript
/**
*
* APIs for our REST task server
*/
// Create a task
function createTask(req, res, next) {
// Restify currently has a bug that doesn't allow you to set default headers
// The headers comply with CORS and allow us to mongodbServer our response to any origin
res.header("Access-Control-Allow-Origin", "*");
res.header("Access-Control-Allow-Headers", "X-Requested-With");
// Create a new task model, fill it up and save it to Mongodb
var _task = new Task();
if (!req.params.task) {
req.log.warn({
params: p
}, 'createTodo: missing task');
next(new MissingTaskError());
return;
}
_task.owner = owner;
_task.task = req.params.task;
_task.date = new Date();
_task.save(function(err) {
if (err) {
req.log.warn(err, 'createTask: unable to save');
next(err);
} else {
res.send(201, _task);
}
});
return next();
}
// Delete a task by name
function removeTask(req, res, next) {
Task.remove({
task: req.params.task,
owner: owner
}, function(err) {
if (err) {
req.log.warn(err,
'removeTask: unable to delete %s',
req.params.task);
next(err);
} else {
log.info('Deleted task:', req.params.task);
res.send(204);
next();
}
});
}
// Delete all tasks
function removeAll(req, res, next) {
Task.remove();
res.send(204);
return next();
}
// Get a specific task based on name
function getTask(req, res, next) {
log.info('getTask was called for: ', owner);
Task.find({
owner: owner
}, function(err, data) {
if (err) {
req.log.warn(err, 'get: unable to read %s', owner);
next(err);
return;
}
res.json(data);
});
return next();
}
/// Simple returns the list of TODOs that were loaded.
function listTasks(req, res, next) {
// Restify currently has a bug that doesn't allow you to set default headers
// The headers comply with CORS and allow us to mongodbServer our response to any origin
res.header("Access-Control-Allow-Origin", "*");
res.header("Access-Control-Allow-Headers", "X-Requested-With");
log.info("listTasks was called for: ", owner);
Task.find({
owner: owner
}).limit(20).sort('date').exec(function(err, data) {
if (err)
return next(err);
if (data.length > 0) {
log.info(data);
}
if (!data.length) {
log.warn(err, "There is no tasks in the database. Add one!");
}
if (!owner) {
log.warn(err, "You did not pass an owner when listing tasks.");
} else {
res.json(data);
}
});
return next();
}
```

#### 新增路由的錯誤處理

您應該加入一些錯誤處理，以便您將遇到的任何問題，以用戶端可了解的方式傳回給用戶端。

在您之前撰寫的程式碼底下加入下列程式碼：

```Javascript
///--- Errors for communicating something interesting back to the client
function MissingTaskError() {
restify.RestError.call(this, {
statusCode: 409,
restCode: 'MissingTask',
message: '"task" is a required parameter',
constructorOpt: MissingTaskError
});
this.name = 'MissingTaskError';
}
util.inherits(MissingTaskError, restify.RestError);
function TaskExistsError(owner) {
assert.string(owner, 'owner');
restify.RestError.call(this, {
statusCode: 409,
restCode: 'TaskExists',
message: owner + ' already exists',
constructorOpt: TaskExistsError
});
this.name = 'TaskExistsError';
}
util.inherits(TaskExistsError, restify.RestError);
function TaskNotFoundError(owner) {
assert.string(owner, 'owner');
restify.RestError.call(this, {
statusCode: 404,
restCode: 'TaskNotFound',
message: owner + ' was not found',
constructorOpt: TaskNotFoundError
});
this.name = 'TaskNotFoundError';
}
util.inherits(TaskNotFoundError, restify.RestError);
```


## 建立伺服器

您現在已定義資料庫並備妥路由。最後一件事是加入伺服器執行個體，以管理您的呼叫。

Restify 及 Express 為 REST API 伺服器提供進階的自訂功能，但您將使用最基本的設定。

```Javascript
/**
* Our server
*/
var server = restify.createServer({
name: "Microsoft Azure Active Directory TODO Server",
version: "2.0.1"
});
// Ensure that we don't drop data on uploads
server.pre(restify.pre.pause());
// Clean up sloppy paths like //todo//////1//
server.pre(restify.pre.sanitizePath());
// Handle annoying user agents (curl)
server.pre(restify.pre.userAgentConnection());
// Set a per request bunyan logger (with requestid filled in)
server.use(restify.requestLogger());
// Allow five requests/second by IP, and burst to 10
server.use(restify.throttle({
burst: 10,
rate: 5,
ip: true,
}));
// Use the common stuff you probably want
server.use(restify.acceptParser(server.acceptable));
server.use(restify.dateParser());
server.use(restify.queryParser());
server.use(restify.gzipResponse());
server.use(restify.bodyParser({
mapParams: true
}));
```
## 將路由加入伺服器 (不含驗證)

```Javascript
/// Now the real handlers. Here we just CRUD
/**
/*
/* Each of these handlers is protected by our OIDCBearerStrategy by invoking 'oidc-bearer'
/* in the passport.authenticate() method. We set 'session: false' as REST is stateless and
/* we don't need to maintain session state. You can experiment with removing API protection
/* by removing the passport.authenticate() method like this:
/*
/* server.get('/tasks', listTasks);
/*
**/
server.get('/tasks', listTasks);
server.get('/tasks', listTasks);
server.get('/tasks/:owner', getTask);
server.head('/tasks/:owner', getTask);
server.post('/tasks/:owner/:task', createTask);
server.post('/tasks', createTask);
server.del('/tasks/:owner/:task', removeTask);
server.del('/tasks/:owner', removeTask);
server.del('/tasks', removeTask);
server.del('/tasks', removeAll, function respond(req, res, next) {
res.send(204);
next();
});
// Register a default '/' handler
server.get('/', function root(req, res, next) {
var routes = [
'GET /',
'POST /tasks/:owner/:task',
'POST /tasks (for JSON body)',
'GET /tasks',
'PUT /tasks/:owner',
'GET /tasks/:owner',
'DELETE /tasks/:owner/:task'
];
res.send(200, routes);
next();
});
server.listen(serverPort, function() {
var consoleMessage = '\n Microsoft Azure Active Directory Tutorial';
consoleMessage += '\n +++++++++++++++++++++++++++++++++++++++++++++++++++++';
consoleMessage += '\n %s server is listening at %s';
consoleMessage += '\n Open your browser to %s/tasks\n';
consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n';
consoleMessage += '\n !!! why not try a $curl -isS %s | json to get some ideas? \n';
consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n\n';
});
```
## 加入 OAuth 支援之前執行伺服器

加入驗證之前，您應該先測試您的伺服器。

若要這樣做的最簡單方法是使用命令列中的 `curl`。但在這麼做之前，您需要一個可以將輸出剖析為 JSON 的簡單公用程式。首先，安裝 JSON 工具。

`$npm install -g jsontool`

這會全域安裝 JSON 工具。安裝 JSON 工具之後，測試伺服器：

請確定您的 MongoDB 執行個體正在執行。

`$sudo mongodb`

切換至 `azuread` 目錄，然後使用 `curl`。

`$ cd azuread` `$ node server.js`

`$ curl -isS http://127.0.0.1:8080 | json`

```Shell
HTTP/1.1 200 OK
Connection: close
Content-Type: application/json
Content-Length: 171
Date: Tue, 14 Jul 2015 05:43:38 GMT
[
"GET /",
"POST /tasks/:owner/:task",
"POST /tasks (for JSON body)",
"GET /tasks",
"PUT /tasks/:owner",
"GET /tasks/:owner",
"DELETE /tasks/:owner/:task"
]
```

新增工作：

`$ curl -isS -X POST http://127.0.0.1:8080/tasks/brandon/Hello`

回應應為：

```Shell
HTTP/1.1 201 Created
Connection: close
Access-Control-Allow-Origin: *
Access-Control-Allow-Headers: X-Requested-With
Content-Type: application/x-www-form-urlencoded
Content-Length: 5
Date: Tue, 04 Feb 2014 01:02:26 GMT
Hello
```
您可以透過以下方式列出 "Brandon" 的工作：

`$ curl -isS http://127.0.0.1:8080/tasks/brandon/`

如果沒有問題，您就可以開始將 OAuth 加入 REST API 伺服器。

您有一個含有 MongoDB 的 REST API 伺服器。

## 將驗證加入 REST API 伺服器

既然您已經有執行中的 REST API 伺服器，您可以讓它在 Azure AD 中發揮價值。

從命令列，切換至 `azuread` 目錄 (如果還不在此目錄下)：

`cd azuread`

### 使用 passport-azure-ad 隨附的 OIDCBearerStrategy

到目前為止，您已經建置典型的 REST ToDo 伺服器，但無任何一種授權。現在您可以開始將授權加進來。

首先，您必須指出您想要使用 Passport。在其他伺服器設定的正下方加入此設定：

```Javascript
// Let's start using Passport.js

server.use(passport.initialize()); // Starts Passport
server.use(passport.session()); // Provides session support
```

> [AZURE.TIP]
撰寫 API 時，您應一律將資料連結到使用者無法證明其在權杖中是唯一的項目。伺服器儲存 ToDo 項目時，它會根據放在 [擁有者] 欄位的權杖 (透過 token.oid 呼叫) 中使用者的**物件識別碼**來儲存這些項目。這確保只有使用者可以存取他的 ToDo 項目，其他人都無法存取已輸入的 ToDo 項目。不會在「擁有者」API 中公開，因此，外部使用者可以要求其他人的 ToDo 項目，即使它們已經過驗證也一樣。

接下來，使用隨附於 `passport-azure-ad` 的持有人策略。(我們現在只看一下程式碼。) 將這段程式碼放在您貼上上述內容的後方：

```Javascript
/**
/*
/* Calling the OIDCBearerStrategy and managing users
/*
/* Passport pattern provides the need to manage users and info tokens
/* with a FindorCreate() method that must be provided by the implementer.
/* Here we just autoregister any user and implement a FindById().
/* You'll want to do something smarter.
**/
var findById = function(id, fn) {
for (var i = 0, len = users.length; i < len; i++) {
var user = users[i];
if (user.sub === id) {
log.info('Found user: ', user);
return fn(null, user);
}
}
return fn(null, null);
};
var oidcStrategy = new OIDCBearerStrategy(options,
function(token, done) {
log.info('verifying the user');
log.info(token, 'was the token retrieved');
findById(token.sub, function(err, user) {
if (err) {
return done(err);
}
if (!user) {
// "Auto-registration"
log.info('User was added automatically as they were new. Their sub is: ', token.sub);
users.push(token);
owner = token.sub;
return done(null, token);
}
owner = token.sub;
return done(null, user, token);
});
}
);
passport.use(oidcStrategy);
```

Passport 的所有策略 (包括 Twitter 和 Facebook) 都採用一種模式，類似於所有策略作者都遵守的策略。您需要傳遞以 `token` 和 `done` 為參數的 `function()` 給它。策略完成所有工作之後會傳回給您。然後，您應該儲存使用者和權杖，如此就不需再次要求它。

> [AZURE.IMPORTANT]
上述程式碼會接受正好向您的伺服器驗證的任何使用者。這就是所謂的自動註冊。在生產伺服器中，任何使用者除非先通過您已決定的註冊程序，否則您不會想讓他們進來。這種模式常見於一些取用者應用程式中，先是可讓您使用 Facebook 來註冊，但接著會要求您填寫其他資訊。如果這不是命令列程式，我們可以從傳回的權杖物件中擷取電子郵件，然後要求使用者填寫其他資訊。由於這是測試伺服器，因此，我們直接將它們加入記憶體中的資料庫。

### 保護端點

您可以使用想要的通訊協定來指定 `passport.authenticate()` 呼叫，以保護端點。

您可以在伺服器程式碼中編輯路由，以執行更有趣的作業：

```Javascript
server.get('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), listTasks);
server.get('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), listTasks);
server.get('/tasks/:owner', passport.authenticate('oauth-bearer', {
session: false
}), getTask);
server.head('/tasks/:owner', passport.authenticate('oauth-bearer', {
session: false
}), getTask);
server.post('/tasks/:owner/:task', passport.authenticate('oauth-bearer', {
session: false
}), createTask);
server.post('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), createTask);
server.del('/tasks/:owner/:task', passport.authenticate('oauth-bearer', {
session: false
}), removeTask);
server.del('/tasks/:owner', passport.authenticate('oauth-bearer', {
session: false
}), removeTask);
server.del('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), removeTask);
server.del('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), removeAll, function respond(req, res, next) {
res.send(204);
next();
});
```

## 再次執行伺服器應用程式，確認它會拒絕您的要求

您可以再次使用 `curl`，以檢查現在是否以 OAuth2 保護您的端點。請在針對這個端點執行任何用戶端 SDK 之前執行此動作。傳回的標頭應該足以說明您執行的作業正確無誤。

請確定您的 MongoDB 執行個體正在執行：

	$sudo mongodb

切換至目錄，然後使用 `curl`：

	$ cd azuread
	$ node server.js

嘗試基本的 POST 方法：

`$ curl -isS -X POST http://127.0.0.1:8080/tasks/brandon/Hello`

```Shell
HTTP/1.1 401 Unauthorized
Connection: close
WWW-Authenticate: Bearer realm="Users"
Date: Tue, 14 Jul 2015 05:45:03 GMT
Transfer-Encoding: chunked
```

401 錯誤是您想要的回應。它指出 Passport 層正嘗試重新導向到授權端點。


## 您現在擁有一項使用 OAuth2 的 REST API 服務

在未使用 OAuth2 相容用戶端的情況下，您已經儘可能地使用此伺服器的所有功能。為此，您還需要另一個逐步解說。

如果您只需有關如何使用 Restify 和 OAuth2 實作 REST API 的相關資訊，則您現在已經有足夠的程式碼可以繼續開發服務，並以此範例為基礎進行建置。

[這裡以 .zip 檔案提供](https://github.com/AzureADQuickStarts/B2C-WebAPI-nodejs/archive/complete.zip)完整範例供您參考 (不含您的組態值)。您也可以從 Github 複製它：

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-WebAPI-nodejs.git```


## 後續步驟

您現在可以進入更進階的主題，例如：

[使用 iOS 搭配 B2C 連線至 Web API](active-directory-b2c-devquickstarts-ios.md)

<!---HONumber=AcomDC_0224_2016-->