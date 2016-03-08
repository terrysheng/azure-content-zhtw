<properties
	pageTitle="將登入功能加入至 Azure B2C 預覽的 Node.js Web 應用程式 | Microsoft Azure"
	description="如何建置使用 B2C 租用戶登入使用者的 Node.js Web 應用程式。"
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
	ms.topic="hero-article"
	ms.date="02/25/2016"
	ms.author="brandwe"/>


# B2C 預覽：將登入功能加入至 Node.js Web 應用程式

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

**Passport** 是 Node.js 的驗證中介軟體。您可以暗中將極具彈性且模組化的 Passport 安裝在任何 Express 或 Resitify Web 應用程式中。有一套完整的策略支援以使用者名稱和密碼、Facebook、Twitter 等來進行驗證。

> [AZURE.NOTE] 本文並未納入如何使用 Azure Active Directory B2C 實作登入、註冊和設定檔管理的說明，而是著重在如何在使用者通過驗證後呼叫 Web API。您應該先從 [.NET Web 應用程式使用者入門教學課程](active-directory-b2c-devquickstarts-web-dotnet.md)開始 (如果還沒有進行)，以了解 Azure AD B2C 的基本概念。

我們已為 Azure Active Directory (Azure AD) 開發一套策略。您將會安裝此模組，然後加入 Azure AD `passport-azure-ad` 外掛程式。

若要這樣做，您需要：

1. 使用 Azure AD 註冊應用程式。
2. 設定應用程式以使用 `passport-azure-ad` 外掛程式。
3. 使用 Passport，向 Azure AD 發出登入和登出要求。
4. 列印使用者資料。

本教學課程的程式碼保留在 [GitHub](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS) 上。若要跟著做，您可以[下載應用程式基本架構的 .zip 檔](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS/archive/skeleton.zip)。您也可以複製基本架構：

```git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS.git```

本教學課程最後會提供完整的應用程式。

> [AZURE.WARNING] 	在這個 B2C 預覽中，您必須對 Web API 工作伺服器和與其連接的用戶端，使用相同的**用戶端識別碼**/**應用程式識別碼**和原則。在 iOS 及 Android 教學課程中也必須如此。如果您先前已在任何一個快速入門中建立應用程式，請直接使用那些值，不要再建立新的值。

## 取得 Azure AD B2C 目錄

您必須先建立目錄或租用戶，才可使用 Azure AD B2C。目錄是適用於所有使用者、app、群組等項目的容器。如果您還沒有此資源，請先[建立 B2C 目錄](active-directory-b2c-get-started.md)，再繼續進行本指南。

## 建立應用程式

接著，您必須在 B2C 目錄中建立應用程式。這會提供必要資訊給 Azure AD，以便與應用程式安全地通訊。因為用戶端應用程式和 Web API 會組成一個邏輯應用程式，所以將由單一**應用程式識別碼**表示。如果要建立應用程式，請遵循[這些指示](active-directory-b2c-app-registration.md)。請務必：

- 在應用程式中加入 **Web 應用程式**/**Web API**。
- 在 [回覆 URL] 中輸入 `http://localhost/TodoListService`。這是此程式碼範例的預設 URL。
- 為您的應用程式建立**應用程式密碼**，並複製起來。稍後您將會用到此資訊。請注意，這個值在使用之前必須經過 [XML 逸出](https://www.w3.org/TR/2006/REC-xml11-20060816/#dt-escape)。
- 複製指派給您的應用程式的**應用程式識別碼**。稍後您也會需要此資訊。

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## 建立您的原則

在 Azure AD B2C 中，每個使用者經驗皆由[原則](active-directory-b2c-reference-policies.md)所定義。此應用程式包含三種身分識別體驗：註冊、登入，以及使用 Facebook 登入。您必須為每個類型建立一個原則，如[原則參考文章](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy)所述。建立您的三個原則時，請務必：

- 在註冊原則中，選擇 [顯示名稱] 和其他註冊屬性。
- 在每個原則中，選擇 [顯示名稱] 和 [物件識別碼] 應用程式宣告。您也可以選擇其他宣告。
- 建立每個原則之後，請複製原則的**名稱**。其前置詞應該為 `b2c_1_`。稍後您將需要這些原則名稱。

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

建立您的三個原則後，就可以開始建置您的應用程式。

請注意，本文不會說明如何使用您剛才建立的原則。如需了解 Azure AD B2C 中的原則如何運作，請從 [.NET Web 應用程式使用者入門教學課程](active-directory-b2c-devquickstarts-web-dotnet.md)開始。

## 在目錄中新增必要條件

從命令列將目錄變更至根資料夾 (如果您尚未在此目錄下)。執行以下命令：

- `npm install express`
- `npm install ejs`
- `npm install ejs-locals`
- `npm install restify`
- `npm install mongoose`
- `npm install bunyan`
- `npm install assert-plus`
- `npm install passport`
- `npm install webfinger`
- `npm install body-parser`
- `npm install express-session`
- `npm install cookie-parser`

此外，我們在快速入門的基本架構中為預覽功能使用 `passport-azure-ad`。

- `npm install passport-azure-ad`

這會安裝 `passport-azure-ad` 所仰賴的程式庫。

## 設定您的應用程式以使用 Passport-Node.js 策略
設定 Express 中介軟體以使用 OpenID Connect 驗證通訊協定。Passport 會用來發出登入和登出要求、管理使用者工作階段，以及取得使用者相關資訊等其他動作。

開啟專案根目錄中的 `config.js` 檔案，並在 `exports.creds` 區段中輸入應用程式的組態值。
- `clientID`：在註冊入口網站中指派給應用程式的**應用程式識別碼**。
- `returnURL`：在入口網站中輸入的**重新導向 URI**。
- `tenantName`：應用程式的租用戶名稱，例如 **contoso.onmicrosoft.com**。

[AZURE.INCLUDE [active-directory-b2c-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

開啟專案根目錄中的 `app.js` 檔案。新增下列呼叫來叫用 `passport-azure-ad` 隨附的 `OIDCStrategy` 策略。


```JavaScript
var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

// Add some logging
var log = bunyan.createLogger({
    name: 'Microsoft OIDC Example Web Application'
});
```

使用剛剛參考的策略來處理登入要求。

```JavaScript
// Use the OIDCStrategy in Passport (Section 2).
//
//   Strategies in Passport require a "validate" function that accepts
//   credentials (in this case, an OpenID identifier), and invokes a callback
//   by using a user object.
passport.use(new OIDCStrategy({
    callbackURL: config.creds.returnURL,
    realm: config.creds.realm,
    clientID: config.creds.clientID,
    oidcIssuer: config.creds.issuer,
    identityMetadata: config.creds.identityMetadata,
    skipUserProfile: config.creds.skipUserProfile,
    responseType: config.creds.responseType,
    responseMode: config.creds.responseMode,
    tenantName: config.creds.tenantName
  },
  function(iss, sub, profile, accessToken, refreshToken, done) {
    log.info('Example: Email address we received was: ', profile.email);
    // asynchronous verification, for effect...
    process.nextTick(function () {
      findByEmail(profile.email, function(err, user) {
        if (err) {
          return done(err);
        }
        if (!user) {
          // "Auto-registration"
          users.push(profile);
          return done(null, profile);
        }
        return done(null, user);
      });
    });
  }
));
```
Passport 會對其所有策略 (包括 Twitter 和 Facebook) 所有類似的模式。所有策略寫入器均遵守此模式。當您查看策略時，您會發現您對其傳遞了以權杖和 `done` 做為參數的 `function()`。策略在完成所有工作之後會回到您這邊。請儲存使用者並隱藏權杖，這樣一來，您就不必再次索取。

> [AZURE.IMPORTANT]
上述程式碼會取得伺服器驗證的所有使用者。這是自動註冊程序。當您使用實際執行伺服器時，您只會想讓已經完成您所設定之註冊程序的使用者進入。供消費者使用的應用程式經常會看見這種模式。這些應用程式可讓您使用 Facebook 進行註冊，但是接著會要求您填寫其他資訊。如果我們的應用程式不是範例應用程式，我們可以從傳回的權杖物件中擷取電子郵件地址，然後要求使用者填寫其他資訊。由於這是測試伺服器，因此我們只會將使用者加入記憶體中的資料庫。

新增方法以讓您可以按照 Passport 所要求地追蹤已登入的使用者。這包括將使用者資訊序列化和還原序列化：

```JavaScript

// Passport session setup. (Section 2)

//   To support persistent sign-in sessions, Passport needs to be able to
//   serialize users into and deserialize users out of sessions. Typically,
//   this is as simple as storing the user ID when Passport serializes a user
//   and finding the user by ID when Passport deserializes that user.
passport.serializeUser(function(user, done) {
  done(null, user.email);
});

passport.deserializeUser(function(id, done) {
  findByEmail(id, function (err, user) {
    done(err, user);
  });
});

// Array to hold users who have signed in
var users = [];

var findByEmail = function(email, fn) {
  for (var i = 0, len = users.length; i < len; i++) {
    var user = users[i];
    log.info('we are using user: ', user);
    if (user.email === email) {
      return fn(null, user);
    }
  }
  return fn(null, null);
};

```

加入可載入 Express 引擎的程式碼。您可以在下列命令中看到，我們使用 Express 提供的預設 `/views` 和 `/routes` 模式。

```JavaScript

// configure Express (Section 2)

var app = express();


app.configure(function() {
  app.set('views', __dirname + '/views');
  app.set('view engine', 'ejs');
  app.use(express.logger());
  app.use(express.methodOverride());
  app.use(cookieParser());
  app.use(expressSession({ secret: 'keyboard cat', resave: true, saveUninitialized: false }));
  app.use(bodyParser.urlencoded({ extended : true }));
  // Initialize Passport!  Also use passport.session() middleware to support
  // persistent sign-in sessions (recommended).
  app.use(passport.initialize());
  app.use(passport.session());
  app.use(app.router);
  app.use(express.static(__dirname + '/../../public'));
});

```

加入 `POST` 路由以將實際的登入要求遞交給 `passport-azure-ad` 引擎：

```JavaScript

// Our Auth routes (Section 3)

// GET /auth/openid
//   Use passport.authenticate() as route middleware to authenticate the
//   request. The first step in OpenID authentication involves redirecting
//   the user to an OpenID provider. After the user is authenticated,
//   the OpenID provider redirects the user back to this application at
//   /auth/openid/return

app.get('/auth/openid',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {
    log.info('Authentication was called in the Sample');
    res.redirect('/');
  });

// GET /auth/openid/return
//   Use passport.authenticate() as route middleware to authenticate the
//   request. If authentication fails, the user will be redirected back to the
//   sign-in page. Otherwise, the primary route function will be called.
//   In this example, it redirects the user to the home page.
app.get('/auth/openid/return',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {

    res.redirect('/');
  });

// POST /auth/openid/return
//   Use passport.authenticate() as route middleware to authenticate the
//   request. If authentication fails, the user will be redirected back to the
//   sign-in page. Otherwise, the primary route function will be called.
//   In this example, it will redirect the user to the home page.

app.post('/auth/openid/return',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {

    res.redirect('/');
  });
```

## 使用 Passport，向 Azure AD 發出登入和登出要求

您的應用程式現已正確設定，將使用 OpenID Connect 驗證通訊協定來與 2.0 版端點進行通訊。`passport-azure-ad` 已處理包括製作驗證訊息、驗證 Azure AD 權杖和維護使用者工作階段的細節。剩下的工作就是讓使用者有辦法登入和登出，以及收集關於已登入使用者的其他資訊。

首先，在 `app.js` 檔案中加入預設、登入、帳戶和登出方法：

```JavaScript

//Routes (Section 4)

app.get('/', function(req, res){
  res.render('index', { user: req.user });
});

app.get('/account', ensureAuthenticated, function(req, res){
  res.render('account', { user: req.user });
});

app.get('/login',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {
    log.info('Login was called in the Sample');
    res.redirect('/');
});

app.get('/logout', function(req, res){
  req.logout();
  res.redirect('/');
});

```

若要詳細檢閱這些方法：
- `/` 會在要求中傳遞使用者 (若有的話) 以將重新導向路由至 `index.ejs` 檢視。
- `/account` 路由會先確認您已通過驗證 (下面有這個程序的實作)。然後在要求中傳遞使用者，以讓您取得使用者的其他資訊。
- `/login` 會從 `passport-azure-ad` 將呼叫路由到 `azuread-openidconnect` 驗證器。如果沒有成功，路由便會將使用者重新導向回 `/login`。
- `/logout` 只會呼叫 `logout.ejs` (和其路由)。這會清除 Cookie，然後讓使用者回到 `index.ejs`。


`app.js` 的最後一個部分是加入 `/account` 路由中使用的 `EnsureAuthenticated` 方法。

```JavaScript

// Simple route middleware to ensure that the user is authenticated. (Section 4)

//   Use this route middleware on any resource that needs to be protected. If
//   the request is authenticated (typically via a persistent sign-in session),
//   then the request will proceed. Otherwise, the user will be redirected to the
//   sign-in page.
function ensureAuthenticated(req, res, next) {
  if (req.isAuthenticated()) { return next(); }
  res.redirect('/login')
}

```

最後，在 `app.js` 中建立伺服器本身。

```JavaScript

app.listen(3000);

```


## 在 Express 中建立檢視和路由以呼叫原則

`app.js` 現已完成。您只需要加入路由和檢視以讓您呼叫登入和註冊原則。這些項目也會處理您所建立的 `/logout` 和 `/login` 路由。

在根目錄下方建立 `/routes/index.js` 路由。

```JavaScript

/*
 * GET home page.
 */

exports.index = function(req, res){
  res.render('index', { title: 'Express' });
};
```

在根目錄下方建立 `/routes/user.js` 路由。

```JavaScript

/*
 * GET users listing.
 */

exports.list = function(req, res){
  res.send("respond with a resource");
};
```

這些簡單的路由會傳遞要求到您的檢視。要求中包含使用者 (如果有的話)。

在根目錄下方建立 `/views/index.ejs` 檢視。這是呼叫登入和登出原則的簡單網頁。您也可以用它來擷取帳戶資訊。請注意，因為會在要求中傳遞使用者以提供使用者已登入的證明，因此您可以使用條件式 `if (!user)`。

```JavaScript
<% if (!user) { %>
	<h2>Welcome! Please sign in.</h2>
	<a href="/login/?p=your facebook policy">Sign in with Facebook</a>
	<a href="/login/?p=your email sign-in policy">Sign in with email</a>
	<a href="/login/?p=your email sign-up policy">Sign up with email</a>
<% } else { %>
	<h2>Hello, <%= user.displayName %>.</h2>
	<a href="/account">Account info</a></br>
	<a href="/logout">Log out</a>
<% } %>
```

在根目錄下方建立 `/views/account.ejs` 檢視，如此即可檢視 `passport-azure-ad` 放置於使用者要求的其他資訊。

```Javascript
<% if (!user) { %>
	<h2>Welcome! Please sign in.</h2>
	<a href="/login">Sign in</a>
<% } else { %>
<p>displayName: <%= user.displayName %></p>
<p>givenName: <%= user.name.givenName %></p>
<p>familyName: <%= user.name.familyName %></p>
<p>UPN: <%= user._json.upn %></p>
<p>Profile ID: <%= user.id %></p>
<p>Full Claims</p>
<%- JSON.stringify(user) %>
<p></p>
<a href="/logout">Log Out</a>
<% } %>
```

現在您可以建立並執行您的應用程式。

執行 `node app.js` 並瀏覽至 `http://localhost:3000`


使用電子郵件或 Facebook 註冊或登入應用程式。登出後，再以不同使用者身分重新登入。

##後續步驟

為了方便參考，[會以 .zip 檔案](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS/archive/complete.zip)提供完整的範例 (不含您的組態值)。您也可以從 Github 複製它：

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-nodejs.git```

您現在可以進入更進階的主題。您可以嘗試：

[使用 Node.js 中的 B2C 模型保護 Web API 安全](active-directory-b2c-devquickstarts-api-node.md)

<!--

For additional resources, check out:
You can now move on to more advanced B2C topics. You might try:

[Call a Node.js web API from a Node.js web app]()

[Customizing the your B2C App's UX >>]()

-->

<!---HONumber=AcomDC_0302_2016-->