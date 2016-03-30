<properties
	pageTitle="如何搭配使用 Azure Redis 快取與 Node.js | Microsoft Azure"
	description="開始搭配使用 Azure Redis 快取與 Node.js 和 node_redis。"
	services="redis-cache"
	documentationCenter=""
	authors="steved0x"
	manager="erikre"
	editor="v-lincan"/>

<tags
	ms.service="cache"
	ms.devlang="nodejs"
	ms.topic="hero-article"
	ms.tgt_pltfrm="cache-redis"
	ms.workload="tbd"
	ms.date="03/09/2016"
	ms.author="sdanie"/>

# 如何搭配使用 Azure Redis 快取與 Node.js

> [AZURE.SELECTOR]
- [.Net](cache-dotnet-how-to-use-azure-redis-cache.md)
- [Node.js](cache-nodejs-get-started.md)
- [Java](cache-java-get-started.md)
- [Python](cache-python-get-started.md)

Azure Redis 快取可讓您存取 Microsoft 所管理的專用安全 Redis 快取。從 Microsoft Azure 內的任何應用程式都可以存取您的快取。

本主題說明如何開始搭配使用 Azure Redis 快取與 Node.js。如需搭配使用 Azure Redis 快取與 Node.js 的另一個範例，請參閱[在 Azure 網站上使用 Socket.IO 建置 Node.js 聊天應用程式][]。


## 必要條件

安裝 [node\_redis](https://github.com/mranney/node_redis)：

    npm install redis

本教學課程使用 [node\_redis](https://github.com/mranney/node_redis)，但是您可以使用列在 [http://redis.io/clients](http://redis.io/clients) 的任何 Node.js 用戶端。

## 在 Azure 上建立 Redis 快取

在 [Azure 入口網站](http://go.microsoft.com/fwlink/?LinkId=398536)中，依序按一下 [新增] 和 [資料 + 儲存體]，然後選取 [Redis 快取]。

  ![][1]

輸入 DNS 主機名稱。它的形式為 `<name>.redis.cache.windows.net`。按一下 [建立]。

  ![][2]


  建立快取之後，請[瀏覽至該快取](cache-configure.md#configure-redis-cache-settings)以檢視其設定。按一下 [金鑰] 下方的連結，並複製主要金鑰。您需要有此金鑰才能驗證要求。

  ![][4]

  ## 在快取中加入項目並擷取該項目

```js
  var redis = require("redis");

  // Add your cache name and access key.
var client = redis.createClient(6380,'<name>.redis.cache.windows.net', {auth_pass: '<key>', tls: {servername: '<name>.redis.cache.windows.net'}});

client.set("key1", "value", function(err, reply) {
	    console.log(reply);
	});

client.get("key1",  function(err, reply) {
	    console.log(reply);
	});
```

Output:

	OK
	value


## 後續步驟

- [啟用快取診斷](cache-how-to-monitor.md#enable-cache-diagnostics)，以[監視](cache-how-to-monitor.md)您快取的健全狀況。
- 閱讀官方 [Redis 文件](http://redis.io/documentation)。


<!--Image references-->
[1]: ./media/cache-nodejs-get-started/cache01.png
[2]: ./media/cache-nodejs-get-started/cache02.png
[3]: ./media/cache-nodejs-get-started/cache03.png
[4]: ./media/cache-nodejs-get-started/cache04.png

[在 Azure 網站上使用 Socket.IO 建置 Node.js 聊天應用程式]: ../app-service-web/web-sites-nodejs-chat-app-socketio.md

<!---HONumber=AcomDC_0323_2016-->