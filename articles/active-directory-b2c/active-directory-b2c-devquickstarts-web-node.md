<properties
	pageTitle="將登入功能新增至適用於 Azure B2C 預覽的 Node.js Web 應用程式 | Microsoft Azure"
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
	ms.topic="article"
	ms.date="09/22/2015"
	ms.author="brandwe"/>

# B2C 預覽：將登入功能加入至 nodeJS Web 應用程式


[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

> [AZURE.NOTE]本文不涵蓋如何使用 Azure AD B2C 實作登入、註冊和管理設定檔。而會著重在如何在使用者已通過驗證後呼叫 Web API。您應該先從 [.NET Web 應用程式使用者入門教學課程](active-directory-b2c-devquickstarts-web-dotnet.md)開始 (如果還沒有進行)，以了解 Azure AD B2C 的基本概念。

**Passport** 是 Node.js 的驗證中介軟體。您可以暗中將極具彈性且模組化的 Passport 放入任何 Express 或 Resitify Web 應用程式。一組完整的策略可支援使用使用者名稱和密碼、Facebook、Twitter 及其他等驗證。我們已為 Microsoft Azure Active Directory 開發一項策略。我們將安裝此模組，然後加入 Microsoft Azure Active Directory `passport-azure-ad` 外掛程式。

若要執行此作業，您需要執行下列動作：

1. 向 Azure AD 註冊應用程式
2. 設定您的 App 來使用 Passport 的 azure-ad-passport 外掛程式。
3. 使用 Passport，向 Azure AD 發出登入和登出要求。
4. 列印出使用者的相關資料。

本教學課程的程式碼保留在 [GitHub](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS)。若要遵循執行，您可以[用 .zip 格式下載應用程式的基本架構](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS/archive/skeleton.zip)，或複製基本架構：

```git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS.git```

The completed application is provided at the end of this tutorial as well.

> [AZURE.WARNING] 	For our B2C Preview you must use the same client ID/Application ID and policies for both the Web-API task server and the client that connects to it. This is true for our iOS and Android tutorials. If you have previously created an application in either of those quickstarts, please use those values instead of creating new ones below.

## 1. Get an Azure AD B2C directory

Before you can use Azure AD B2C, you must create a directory, or tenant.  A directory is a container for all your users, apps, groups, and so on.  If you don't have
one already, go [create a B2C directory](active-directory-b2c-get-started.md) before moving on.

## 2. Create an application

Now you need to create an app in your B2C directory, which gives Azure AD some information that it needs to securely communicate with your app.  Both the client app and web API will be represented by a single **Application ID** in this case, since they comprise one logical app.  To create an app,
follow [these instructions](active-directory-b2c-app-registration.md).  Be sure to

- Include a **web app/web api** in the application
- Enter `http://localhost/TodoListService` as a **Reply URL** - it is the default URL for this code sample.
- Create an **Application Secret** for your application and copy it down.  You will need it shortly.
- Copy down the **Application ID** that is assigned to your app.  You will also need it shortly.

    > [AZURE.IMPORTANT]
    You cannot use applications registered in the **Applications** tab on the [Azure Portal](https://manage.windowsazure.com/) for this.

## 3. Create your policies

In Azure AD B2C, every user experience is defined by a [**policy**](active-directory-b2c-reference-policies.md).  This app contains three 
identity experiences - sign-up, sign-in, and sign-in with Facebook.  You will need to create one policy of each type, as described in the 
[policy reference article](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy).  When creating your three policies, be sure to:

- Choose the **Display Name** and a few other sign-up attributes in your sign-up policy.
- Choose the **Display Name** and **Object ID** application claims in every policy.  You can choose other claims as well.
- Copy down the **Name** of each policy after you create it.  It should have the prefix `b2c_1_`.  You'll need those policy names shortly. 

Once you have your three policies successfully created, you're ready to build your app.

Note that this article does not cover how to use the policies you just created.  If you want to learn about how policies work in Azure AD B2C,
you should start with the [.NET Web App getting started tutorial](active-directory-b2c-devquickstarts-web-dotnet.md).



## 4. Add pre-requisities to your directory

From the command-line, change directories to your root folder if not already there and run the following commands:

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

- In addition, we've use `passport-azure-ad` for our Preview in the skeleton of the quickstart. 

- `npm install passport-azure-ad`


This will install the libraries that passport-azure-ad depend on.

## 5. Set up your app to use the passport-node-js strategy
Here, we'll configure the Express middleware to use the OpenID Connect authentication protocol.  Passport will be used to issue sign-in and sign-out requests, manage the user's session, and get information about the user, amongst other things.

-	To begin, open the `config.js` file in the root of the project, and enter your app's configuration values in the `exports.creds` section.
    -	The `clientID:` is the **Application Id** assigned to your app in the registration portal.
    -	The `returnURL` is the **Redirect URI** you entered in the portal.
    - The `tenantName:` is the **tenant name** of your app, e.g. contoso.onmicrosoft.com


- Next open `app.js` file in the root of the proejct and add the follwing call to invoke the `OIDCStrategy` strategy that comes with `passport-azure-ad`


```JavaScript
var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

// Add some logging var log = bunyan.createLogger({ name: 'Microsoft OIDC Example Web Application' }); ```

- 之後，請使用我們僅供參考的策略來處理登入要求

```JavaScript
// Use the OIDCStrategy within Passport. (Section 2) 
// 
//   Strategies in passport require a `validate` function, which accept
//   credentials (in this case, an OpenID identifier), and invoke a callback
//   with a user object.
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
Passport 會使用適用於它的所有策略 (Twitter、Facebook 等) 且所有策略寫入器都依循的類似模式。查看此策略，您會看見我們將它當成 function() 來傳遞，其中含有一個 token 和一個 done 做為參數。一旦策略完成所有工作之後，便會盡責地返回。當它完成之後，我們會想要儲存使用者並隱藏權杖，因此我們不需再次要求它。

> [AZURE.IMPORTANT]上述程式碼會讓所有使用者經歷伺服器的驗證。這就是所謂的自動註冊。在生產伺服器中，您想要讓所有人都必須先經歷您所決定的註冊過程。這通常是您在取用者 App 中看到的模式，可讓您向 Facebook 註冊，但接著會要求您填寫其他資訊。如果這不是範例應用程式，我們就只能從傳回的權杖物件中擷取電子郵件，然後要求他們填寫其他資訊。由於這是測試伺服器，因此，我們直接將它們加入至記憶體中的資料庫。

- 接下來，我們會新增方法，依 Passport 所要求，允許我們持續追蹤已登入的使用者。這包括將使用者資訊序列化和還原序列化：

```JavaScript

// Passport session setup. (Section 2)

//   To support persistent login sessions, Passport needs to be able to
//   serialize users into and deserialize users out of the session.  Typically,
//   this will be as simple as storing the user ID when serializing, and finding
//   the user by ID when deserializing.
passport.serializeUser(function(user, done) {
  done(null, user.email);
});

passport.deserializeUser(function(id, done) {
  findByEmail(id, function (err, user) {
    done(err, user);
  });
});

// array to hold logged in users
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

- 接下來，加入可載入 express 引擎的程式碼。您會在此處看到我們使用 Express 所提供的預設 /views 和 /routes 模式。

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
  // Initialize Passport!  Also use passport.session() middleware, to support
  // persistent login sessions (recommended).
  app.use(passport.initialize());
  app.use(passport.session());
  app.use(app.router);
  app.use(express.static(__dirname + '/../../public'));
});

```

- 最後會加入 POST 路由，這會將實際的登入要求遞交至 `passport-azure-ad` 引擎：

```JavaScript

// Our Auth routes (Section 3)

// GET /auth/openid
//   Use passport.authenticate() as route middleware to authenticate the
//   request.  The first step in OpenID authentication will involve redirecting
//   the user to their OpenID provider.  After authenticating, the OpenID
//   provider will redirect the user back to this application at
//   /auth/openid/return

app.get('/auth/openid',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {
    log.info('Authenitcation was called in the Sample');
    res.redirect('/');
  });

// GET /auth/openid/return
//   Use passport.authenticate() as route middleware to authenticate the
//   request.  If authentication fails, the user will be redirected back to the
//   login page.  Otherwise, the primary route function function will be called,
//   which, in this example, will redirect the user to the home page.
app.get('/auth/openid/return',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {
    
    res.redirect('/');
  });

// POST /auth/openid/return
//   Use passport.authenticate() as route middleware to authenticate the
//   request.  If authentication fails, the user will be redirected back to the
//   login page.  Otherwise, the primary route function function will be called,
//   which, in this example, will redirect the user to the home page.

app.post('/auth/openid/return',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {
    
    res.redirect('/');
  });
```

## 4\.使用 Passport，向 Azure AD 發出登入和登出要求

您的應用程式現已正確設定，將使用 OpenID Connect 驗證通訊協定來與 v2.0 端點進行通訊。`passport-azure-ad` 已處理包括製作驗證訊息、驗證 Azure AD 權杖和維護使用者工作階段的一切繁瑣細節。所有剩餘的部分就是為使用者提供一種方式來登入、登出，以及收集關於已登入使用者的其他資訊。

- 首先，將預設、登入、帳戶及登出方法加入 `app.js` 檔案：

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

-	讓我們詳細檢閱這些方法：
    -	`/` 路由將重新導向至 index.ejs 檢視，其會在要求中傳遞使用者 (若有的話)
    - `/account` 路由會先***確保我們已通過驗證*** (我們將在下面實作)，然後在要求中傳遞使用者，以讓我們能夠取得關於該使用者的其他資訊。
    - `/login` 路由將從 `passport-azuread` 呼叫 azuread-openidconnect 驗證器，如果失敗，則會再次將使用者重新導向至 /login
    - `/logout` 會直接呼叫 logout.ejs (以及路由)，其會清除 Cookie，然後讓使用者返回至 index.ejs


- 針對 `app.js` 的最後一個部分，加入可在上述 `/account` 中使用的 EnsureAuthenticated 方法。

```JavaScript

// Simple route middleware to ensure user is authenticated. (Section 4)

//   Use this route middleware on any resource that needs to be protected.  If
//   the request is authenticated (typically via a persistent login session),
//   the request will proceed.  Otherwise, the user will be redirected to the
//   login page.
function ensureAuthenticated(req, res, next) {
  if (req.isAuthenticated()) { return next(); }
  res.redirect('/login')
}

```

- 最後，在 `app.js` 中實際建立伺服器本體：

```JavaScript

app.listen(3000);

```


## 5\.建立快速檢視和路由以呼叫原則

我們已完成 `app.js`。現在僅須新增路由和檢視即可，其可讓我們呼叫登入與註冊原則，並處理已建立的 `/logout` 和 `/login` 路由。

- 在根目錄下方建立 `/routes/index.js` 路由。

```JavaScript

/*
 * GET home page.
 */

exports.index = function(req, res){
  res.render('index', { title: 'Express' });
};
```

- 在根目錄下方建立 `/routes/user.js` 路由

```JavaScript

/*
 * GET users listing.
 */

exports.list = function(req, res){
  res.send("respond with a resource");
};
```

這些簡單路由只會將要求傳遞到我們的檢視，包括使用者 (如果有的話)。

- 在根目錄下方建立 `/views/index.ejs` 檢視。此簡單網頁會呼叫我們的登入和登出原則，並可讓我們擷取帳戶資訊。請注意，若透過要求傳遞的使用者已證實我們擁有已登入的使用者，就能使用條件式 `if (!user)`。

```JavaScript
<% if (!user) { %>
	<h2>Welcome! Please log in.</h2>
	<a href="/login/?p=your facebook policy">Sign In with Facebook</a>
	<a href="/login/?p=your email sign-in policy">Sign In With Email</a>
	<a href="/login/?p=your email sign-up policy">Sign Up With Email</a>
<% } else { %>
	<h2>Hello, <%= user.displayName %>.</h2>
	<a href="/account">Account Info</a></br>
	<a href="/logout">Log Out</a>
<% } %>
```

- 在根目錄下方建立 `/views/account.ejs` 檢視，如此即可檢視 `passport-azuread` 放置於使用者要求的其他資訊。

```Javascript
<% if (!user) { %>
	<h2>Welcome! Please log in.</h2>
	<a href="/login">Log In</a>
<% } else { %>
<p>displayName: <%= user.displayName %></p>
<p>givenName: <%= user.name.givenName %></p>
<p>familyName: <%= user.name.familyName %></p>
<p>UPN: <%= user._json.upn %></p>
<p>Profile ID: <%= user.id %></p>
<p>Full Claimes</p>
<%- JSON.stringify(user) %>
<p></p>
<a href="/logout">Log Out</a>
<% } %>
```

最後，建置並執行您的應用程式！

執行 `node app.js` 並瀏覽至 `http://localhost:3000`


使用電子郵件或 Facebook 註冊或登入應用程式。登出，再以另一個使用者身分重新登入。



##後續步驟

如需參考，[此處以 .zip 格式提供](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS/archive/complete.zip)完整範例 (不含您的組態值)，或者，您也可以從 GitHub 將其複製：

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-nodejs.git```

您現在可以進入更進階的主題。您可以嘗試：

[在 node.js 中使用 B2C 模型保護 Web API >>](active-directory-b2c-devquickstarts-webapi-nodejs.md)

<!--

For additional resources, check out:
You can now move onto more advanced B2C topics.  You may want to try:

[Calling a node.js Web API from a node.js Web App >>]()

[Customizing the your B2C App's UX >>]()

-->

<!---HONumber=Sept15_HO4-->