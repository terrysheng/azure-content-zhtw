<properties linkid="dev-nodejs-website-with-socketio" urlDisplayName="Website Using Socket.IO" pageTitle="Node.js Website using Socket.io - Azure tutorial" metaKeywords="Azure Node.js socket.io tutorial, Azure Node.js socket.io, Azure Node.js tutorial" description="A tutorial that demonstrates using socket.io in a node.js website hosted on Azure." metaCanonical="" services="web-sites" documentationCenter="nodejs" title="Build a Node.js Chat Application with Socket.IO on an Azure Website" authors="larryfr" solutions="" videoId="" scriptId="" manager="paulettm" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr"></tags>

# 在 Azure 網站上使用 Socket.IO 建立 Node.js 交談應用程式

Socket.IO 使用 WebSocket 提供 node.js 伺服器與用戶端之間的即時通訊。此外它還能支援遞補其他適用於舊版瀏覽器的傳輸 (例如長輪詢)。本教學課程將逐步引導您以 Azure 網站來託管 Socket.IO 架構的交談應用程式。如需 Socket.IO 的詳細資訊，請參閱 [][]<http://socket.io/></a>。

> [WACOM.NOTE] 此工作中的程序適用於 Azure 網站；若是使用雲端服務，請參閱[在 Azure 雲端服務上使用 Socket.IO 建立 Node.js 交談應用程式][]。

## <span id="Download"></span></a>下載聊天範例

在此專案中，我們將使用 [Socket.IO GitHub 儲存機制][]中的交談範例。請執行下列步驟來下載範例，並將它加入至您先前建立的專案。

1.  下載 Socket.IO 專案 (版本 1.0.6 已用於此文件) 的 [ZIP 或 GZ 封存版本][]

2.  將此封存解壓縮並將 **examples\\chat** 目錄複製到新的位置。例如，**\\node\\chat**。

## <span id="Modify"></span></a>修改 App.js 和安裝模組

在將應用程式部署至 Azure 之前，必須稍做一些修改。

1.  將 **index.js** 檔案重新命名為 **app.js**。如此能讓 Azure 偵測到這是 Node.js 應用程式。

2.  在記事本或其他文字編輯器中開啟 **app.js** 檔案。

3.  在 app.js 開頭找出 **Module dependencies** 區段，將含有 `var io = require('../..')(server);` 的那一行變更為 `var io = require('socket.io')(server);`，如下所示：

        var express = require('express');
        var app = express();
        var server = require('http').createServer(app);
        // var io = require('../..')(server);
        var io = require('socket.io')(server);
        var port = process.env.PORT || 3000;

將變更儲存至 app.js 之後，使用下列步驟來安裝必要的模組：

1.  從命令列中，切換至 **\\node\\chat** 目錄，使用下列命令來安裝此應用程式所需的模組：

        npm install

    這將會安裝 package.json 檔案中列出的模組。命令完成之後，您應該會看到類似下列的輸出：

        express@3.4.8 node_modules\express
        ├── methods@0.1.0
        ├── merge-descriptors@0.0.1
        ├── debug@0.8.1
        ├── cookie-signature@1.0.1
        ├── range-parser@0.0.4
        ├── fresh@0.2.0
        ├── buffer-crc32@0.2.1
        ├── cookie@0.1.0
        ├── mkdirp@0.3.5
        ├── commander@1.3.2 (keypress@0.1.0)
        ├── send@0.1.4 (mime@1.2.11)
        └── connect@2.12.0 (uid2@0.0.3, pause@0.0.1, qs@0.6.6, bytes@0.2.1, raw-body@1.1.2, batch@0.5.0, negotiator@0.3.0, multiparty@2.2.0)

2.  因為此範例原本是 Socket.IO GitHub 儲存機制的一部分，並以相對路徑直接參考 Socket.IO 程式庫，package.json 檔案中並未參考 Socket.IO，因此，我們必須發出下列命令來安裝它：

        npm install socket.io@1.0.6 -save

    > [WACOM.NOTE] 雖然較新版本的 Socket.IO 可能適用於本文章中的這些步驟，但還是已在版本 1.0.6 中經過測試。

## <span id="Publish"></span></a>建立 Azure 網站

請遵循以下步驟來建立 Azure 網站，接著為該網站啟用 Git 發佈功能，再啟用 WebSocket 支援。

> [WACOM.NOTE] 若要完成此教學課程，您需要 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 [Azure 免費試用][]。

1.  從命令列中，切換至 **\\node\\chat** 目錄，使用下列命令來建立新的 Azure 網站，並啟用網站和本機目錄的 Git 儲存機制。這也會建立名為 'azure' 的 Git 遠端。

        azure site create mysitename --git

    您必須將 'mysitename' 改成您的網站的唯一名稱。

2.  使用下列命令，將現有的檔案認可到本機儲存機制：

        git add .
        git commit -m "Initial commit"

3.  使用下列命令，將檔案推送至 Azure 網站儲存機制：

        git push azure master

    在伺服器上匯入模組時會顯示狀態訊息。此程序完成之後，應用程式將在 Azure 網站上託管。

    > [WACOM.NOTE] 在模組安裝期間，您可能會看到「找不到匯入的專案...」的錯誤。請放心忽略這項錯誤訊息。

4.  Socket.IO 使用 WebSockets (在 Azure 上依預設不會啟用)。若要啟用 Web Sockets，請使用下列命令：

        azure site set -w

    如果出現提示，請輸入網站的名稱。

    > [WACOM.NOTE]
    > 'azure site set -w' 命令僅適用於 Azure 跨平台命令列介面 0.7.4 版或更高版本。您也可以使用 Azure 管理入口網站來啟用 WebSocket 支援。
    >
    > 若要使用 [Azure 管理入口網站][]來啟用 WebSockets，請選取網站的 [設定] 頁面，在 Web Socket 項目上選取 [開啟]，然後按一下 [儲存]。
    >
    > ![websockets][]

5.  若要在 Azure 上檢視網站，請使用下列命令來啟動網頁瀏覽器，並瀏覽至託管的網站：

        azure site browse

您的應用程式現在於 Azure 上執行，且可使用 Socket.IO 來轉送不同用戶端之間的交談訊息。

> [WACOM.NOTE] 為了簡化起見，此範例只限於連線至相同執行個體的使用者之間的交談。這表示如果雲端服務建立兩個背景工作角色執行個體，則使用者只能夠與連線至相同背景工作角色執行個體的其他使用者交談。若要擴充應用程式來處理多個角色執行個體，您可以使用服務匯流排之類的技術，以便跨執行個體來共用 Socket.IO 儲存狀態。相關範例請參閱 [Azure SDK for Node.js GitHub 儲存機制][] (英文) 中的服務匯流排佇列和主題使用範例。

## 橫向擴充

Socket.IO 應用程式可以使用**配接器**來橫向擴充，以在多個應用程式執行個體之間散佈訊息和事件。由於市面上已有數種配接器，因此可以輕鬆使用 [socket.io-redis][] 配接器來搭配 Azure Redis 快取功能。

> [WACOM.NOTE] 橫向擴充 Socket.IO 解決方案的額外需求是支援黏性工作階段。Azure 網站的黏性工作階段預設是由 Azure 要求路由來啟用。如需詳細資訊，請參閱 [Azure 網站的執行個體同質性][]。

### 建立 Redis 快取

執行[在 Azure Redis 快取中建立快取][]中的步驟以建立新的快取。

> [WACOM.NOTE] 儲存快取的**主機名稱**和**主要金鑰**，因為後續步驟將會需要這些項目。

### 新增 redis 和 socket.io-redis 模組

1.  從命令列中，切換至 **\\node\\cha** 目錄，然後執行下列命令：

        npm install socket.io-redis@0.1.3 redis@0.11.0 --save

    > [WACOM.NOTE] 此命令中指定的版本是測試本文章所使用的版本。

2.  修改 **app.js** 檔案以在 `var io = require('socket.io')(server);` 之後緊接下列行

        var pub = require('redis').createClient(6379,'redishostname', {auth_pass: 'rediskey', return_buffers: true});
        var sub = require('redis').createClient(6379,'redishostname', {auth_pass: 'rediskey', return_buffers: true});

        var redis = require('socket.io-redis');
        io.adapter(redis({pubClient: pub, subClient: sub}));

    將 **redishostname** 和 **rediskey** 取代為 Redis 快取的主機名稱和金鑰。

    如此將會為先前建立的 Redis 快取建立一個發佈和訂閱用戶端。之後該用戶端會搭配配接器使用，以將 Socket.IO 設定為使用 Redis 快取在應用程式的執行個體之間傳遞訊息和事件。

    > [WACOM.NOTE] 雖然 **socket.io-redis** 配接器可以直接與 Redis 通訊，但是目前版本 (截至 7/14/2014) 並不支援 Azure Redis 快取所需的驗證。因此，系統會使用 **redis** 模組來建立初始連線，然後將該用戶端傳遞至 **socket.io-redis** 配接器。
    >
    > 雖然 Azure Redis 快取使用連接埠 6380 來支援安全的連線，但是本例中使用的模組截至 2014 年 7 月 14 日為止並不支援安全的連線。以上代碼使用的是預設不安全的 6380 連接埠。

3.  儲存已修改的 **app.js**

### 認可變更並重新部署

從 **\\node\\chat** 目錄的命令列中，使用下列命令來認可變更並重新部署應用程式。

    git add .
    git commit -m "implementing scale out"
    git push azure master

一旦將變更推送至伺服器，您就可以使用下列命令在多個執行個體間擴充網站。

    azure site scale instances --instances #

其中 **\#** 是要建立的執行個體數目。

您可以從多個瀏覽器或多部電腦中連線至網站，以確認該訊息已正確傳送至所有用戶端。

## <span id="tshooting"></span></a>疑難排解

### 連線限制

Azure 網站可用於多個 SKU，SKU 可以決定您網站適用的資源。這包含允許 WebSocket 連線的數目。如需詳細資訊，請參閱 [Azure 網站定價][]頁面。

### 使用 WebSockets 而未傳送的訊息

如果用戶端瀏覽器持續遞補長輪詢，而非使用 WebSockets，可能是下列原因所致。

-   **試圖將傳輸僅限制到 WebSockets**

    為了讓 Socket.IO 使用 WebSockets 作為訊息傳輸，伺服器和用戶端都必須支援 WebSockets。如果其中一方不支援 WebSockets，則 Socket.IO 將會交涉其他傳輸，例如長輪詢。Socket.IO 使用的預設傳輸清單為 `websocket, htmlfile, xhr-polling, jsonp-polling`。您可以將下列代碼新增至含有 `, nicknames = {};` 那一行後面的 **app.js** 檔案，以便強制讓 Socket.IO 僅使用 WebSockets。

        io.configure(function() {
          io.set('transports', ['websocket']);
        });

    > [WACOM.NOTE] 請注意，不支援 WebSockets 的舊版瀏覽器將無法在以上代碼作用中時連線至該網站，因為此一代碼會將通訊僅限制到 WebSockets。

-   **使用 SSL**

    WebSockets 依賴某些較少使用的 HTTP 標頭，例如 **Upgrade** 標頭。某些中繼網路裝置，例如 Web Proxy，可能會移除這些標頭。為了避免發生這種問題，您可以建立經由 SSL 的 WebSocket 連線。

    若要這麼做，最簡單的方式就是將 Socket.IO 設定為 `match origin protocol`。此一代碼會將 Socket.IO 指引至安全的 WebSockets 通訊，如同網頁原始的 HTTP/HTTPS 要求一般。如果瀏覽器使用 HTTPS URL 造訪您的網站，則後續通過 Socket.IO 的 WebSocket 通訊將會在經由 SSL 時受到保護。

    若要修改本例以啟用這項設定，請將下列代碼新增至含有 `, nicknames = {};` 那一行後面的 **app.js** 檔案。

        io.configure(function() {
          io.set('match origin protocol', true);
        });

-   **確認 web.config 設定**

    託管 Node.js 應用程式的 Azure 網站會使用 **web.config** 檔案將連入要求路由至 Node.js 應用程式。若要讓 WebSocket 能在 Node.js 應用程式中正確運作，**web.config** 必須包含下列項目。

        <webSocket enabled="false"/>

    此一代碼會停用 IIS WebSocket 模組，該模組中包含本身實作的 WebSocket 和與 Node.js 特定 WebSocket 模組 (例如 Socket.IO) 的衝突。如果沒有顯示此行，或將此行設為 `true`，就可能是 WebSocket 傳輸無法在您應用程式中運作的原因。

    通常 Node.js 應用程式並不包含 **web.config** 檔案，因此 Azure 網站會在部署 Node.js 應用程式時自動建立此一檔案。由於此檔案是在伺服器上自動產生的，因此您必須在網站使用 FTP 或 FTPS URL 才能檢視此檔案。您可以在 Azure 管理入口網站中選取您的網站，再選取 [儀表板] 連結，以找出該網站的 FTP 和 FTPS URL。URL 會顯示在 [快速概覽] 區段。

    > [WACOM.NOTE] 如果您的應用程式並未提供 **web.config** 檔案，那麼此一檔案只會由 Azure 網站來產生。如果您在應用程式專案的根目錄中提供 **web.config** 檔案，Azure 網站將會使用此一檔案。

    如果沒有顯示此項目，或此項目設為 `true` 的值，那麼您應該在 Node.js 應用程式的根目錄中建立一個 **web.config**，然後指定 `false` 的值。以下即為某種應用程式的預設 **web.config**，這種應用程式是使用 **app.js** 作為進入點，供您參考。

        <?xml version="1.0" encoding="utf-8"?>
        <!--
             This configuration file is required if iisnode is used to run node processes behind
             IIS or IIS Express.  For more information, visit:

             https://github.com/tjanczuk/iisnode/blob/master/src/samples/configuration/web.config
        -->

        <configuration>
          <system.webServer>
            <!-- Visit http://blogs.msdn.com/b/windowsazure/archive/2013/11/14/introduction-to-websockets-on-windows-azure-web-sites.aspx for more information on WebSocket support -->
            <webSocket enabled="false" />
            <handlers>
              <!-- Indicates that the server.js file is a node.js site to be handled by the iisnode module -->
              <add name="iisnode" path="app.js" verb="*" modules="iisnode"/>
            </handlers>
            <rewrite>
              <rules>
                <!-- Do not interfere with requests for node-inspector debugging -->
                <rule name="NodeInspector" patternSyntax="ECMAScript" stopProcessing="true">
                  <match url="^app.js\/debug[\/]?" />
                </rule>

                <!-- First we consider whether the incoming URL matches a physical file in the /public folder -->
                <rule name="StaticContent">
                  <action type="Rewrite" url="public{REQUEST_URI}"/>
                </rule>

                <!-- All other URLs are mapped to the node.js site entry point -->
                <rule name="DynamicContent">
                  <conditions>
                    <add input="{REQUEST_FILENAME}" matchType="IsFile" negate="True"/>
                  </conditions>
                  <action type="Rewrite" url="app.js"/>
                </rule>
              </rules>
            </rewrite>
            <!--
              You can control how Node is hosted within IIS using the following options:
                * watchedFiles: semi-colon separated list of files that will be watched for changes to restart the server
                * node_env: will be propagated to node as NODE_ENV environment variable
                * debuggingEnabled - controls whether the built-in debugger is enabled

              See https://github.com/tjanczuk/iisnode/blob/master/src/samples/configuration/web.config for a full list of options
            -->
            <!--<iisnode watchedFiles="web.config;*.js"/>-->
          </system.webServer>
        </configuration>

    > [WACOM.NOTE] 如果您的應用程式使用非 **app.js** 的進入點，則必須將 **app.js** 的所有出現項目取代為正確的進入點。例如，將 **app.js** 取代為 **server.js**。

## 後續步驟

在本教學課程中，您學到如何建立於 Azure 網站中託管的交談應用程式。您也可以將此應用程式交由 Azure 雲端服務託管。如需相關作法的步驟，請參閱＜[在 Azure 雲端服務上使用 Socket.IO 建立 Node.js 聊天應用程式][]＞。

  []: http://socket.io/
  [在 Azure 雲端服務上使用 Socket.IO 建立 Node.js 交談應用程式]: http://www.windowsazure.com/en-us/develop/nodejs/tutorials/app-using-socketio/
  [Socket.IO GitHub 儲存機制]: https://github.com/Automattic/socket.io
  [ZIP 或 GZ 封存版本]: https://github.com/Automattic/socket.io/releases
  [Azure 免費試用]: http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=A7171371E
  [Azure 管理入口網站]: https://manage.windowsazure.com
  [websockets]: ./media/web-sites-nodejs-chat-app-socketio/websockets.png
  [Azure SDK for Node.js GitHub 儲存機制]: https://github.com/WindowsAzure/azure-sdk-for-node
  [socket.io-redis]: https://github.com/automattic/socket.io-redis
  [Azure 網站的執行個體同質性]: http://azure.microsoft.com/blog/2013/11/18/disabling-arrs-instance-affinity-in-windows-azure-web-sites/
  [在 Azure Redis 快取中建立快取]: http://go.microsoft.com/fwlink/p/?linkid=398592&clcid=0x409
  [Azure 網站定價]: /en-us/pricing/details/web-sites/
  [在 Azure 雲端服務上使用 Socket.IO 建立 Node.js 聊天應用程式]: /en-us/develop/nodejs/tutorials/app-using-socketio/
