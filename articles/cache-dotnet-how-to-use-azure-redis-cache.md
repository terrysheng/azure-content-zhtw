<properties pageTitle="如何使用 Azure Redis 快取" metaKeywords="" description="了解如何在 Azure Redis Cache 中使用快取" metaCanonical="" services="cache" documentationCenter="API Management" title="How to Use Azure Redis Cache" authors="sdanie" solutions="" manager="dwrede" editor="" />

<tags ms.service="cache" ms.workload="tbd" ms.tgt_pltfrm="cache-redis" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="sdanie" />

# 如何使用 Azure Redis 快取

本指南說明如何開始使用 
**Azure Redis 快取**.這些範例均以 C\# 程式碼撰寫並
使用 .NET API。涵蓋的案例包括**建立和設定快取**、**設定快取用戶端**、**加入和移除快取中的物件**，以及**將 ASP.NET 工作階段狀態儲存在快取中**。如需
使用 Azure Redis 快取的詳細資訊，請參閱[後續步驟][]一節。

## 目錄

-   [何謂 Azure Redis 快取？][]
-	[開始使用 Azure Redis 快取][]
	-	[建立快取][]
	-	[設定快取用戶端][]
-	[使用快取][]
	-	[連接到快取][]
	-   [從快取加入和擷取物件][]
	-   [指定快取中物件的到期時間][]
	-   [將 ASP.NET 工作階段狀態儲存在快取中][]
-   [後續步驟][]

<a name="what-is"></a>
## 何謂 Azure Redis 快取？

Microsoft Azure Redis 快取是基於受歡迎的開放原始碼 Redis 快取。它可讓您存取由 Microsoft 管理的安全、專用 Redis 快取。使用 Azure Redis 快取建立的快取，可透過 Microsoft Azure 內的任何應用程式加以存取。

Microsoft Azure Redis 快取有兩個階層：

-	**基本** - 單一節點。多種大小，最多為 53 GB。
-	**標準** - 兩個節點，主/從屬性。多種大小，最高為 53 GB。99.9% SLA。

每一個層級都有不同的功能和定價。本指南稍後將探討這些功能，如需定價的詳細資訊，請參閱[快取定價詳細資料][]。

本指南提供開始使用 Azure Redis 快取的概觀。如需本入門指南涵蓋範圍外之功能的詳細資訊，請參閱 [Azure Redis 快取概觀][]。

<a name="getting-started-cache-service"></a>
## 開始使用 Azure Redis 快取

開始使用 Azure Redis 快取相當簡單。若要開始，請佈建並設定快取。接著，設定快取用戶端，以便它們可以存取快取。一旦設定了快取用戶端，就可以開始使用它們。

-	[建立快取][]
-	[設定快取][]
-	[設定快取用戶端][]

<a name="create-cache"></a>
## 建立快取

若要建立快取，請先登入 Azure 管理預覽入口網站，然後按一下 [**新增**]、[**Redis 快取**]。

![New cache][NewCacheMenu]

在 [**新增 Redis 快取**] 分頁中，指定所需的快取組態。

![Create cache][CacheCreate]

在 [**DNS 名稱**] 中，輸入要用於快取端點的子網域名稱。端點必須是介於 6 到 20 個字元之間的字串、僅包含小寫數字和字母，而且必須以字母開頭。

使用 [**定價層**] 來選取所需的快取大小和功能。Redis 快取有下列兩個層級。

-	**基本** - 單一節點，多種大小，最多為 53 GB。
-	**標準** - 兩個節點，主/從屬性，99.9% SLA，多種大小，最多為 53 GB。

在 [**訂閱**] 中，選取您要用於快取的 Azure 訂閱。

>[AZURE.NOTE] 如果您的帳戶僅有一個訂閱，則會自動加以選取，而且不會顯示 [訂閱] 下拉式清單。

在 [**資源群組**] 中，選取或建立快取的資源群組。

>[AZURE.NOTE] 如需詳細資訊，請參閱[使用資源群組管理您的 Azure 資源][]。 

使用 [**地理位置**] 來指定裝載快取的地理位置。為獲得最佳效能，Microsoft 強烈建議您在與快取用戶端應用程式相同的區域中建立快取。

設定新的快取選項之後，按一下 [**建立**]。建立快取可能需要數分鐘的時間。若要檢查狀態，您可以監視開始面板上的進度。建立快取之後，新快取的狀態會是「**執行中**」狀態，而且以預設值就可直接使用。

![Cache created][CacheCreated]

建立快取之後，您可以從 [**瀏覽**] 分頁來存取它。

![Browse blade][BrowseCaches]

按一下 [**快取**] 來檢視您的快取。

![Caches][Caches]

<a name="NuGet"></a>
## 設定快取用戶端

使用 Azure Redis 快取建立的快取，可透過任何 Azure 應用程式來存取。Visual Studio 中開發的 .NET 應用程式可以使用 **StackExchange.Redis** 快取用戶端，該用戶端可使用能簡化快取用戶端應用程式組態的 NuGet 封裝來加以設定。 

>[AZURE.NOTE] 如需詳細資訊，請參閱 [StackExchange.Redis][] github 頁面和 [StackExchange.Redis 快取用戶端文件][]。

若要在 Visual Studio 中使用 StackExchange.Redis NuGet 封裝來設定用戶端應用程式，請在 [**方案總管**] 中的專案上按一下滑鼠右鍵，然後選擇 [**管理 NuGet 封裝**]。 

![Manage NuGet packages][NuGetMenu]

在 [**線上搜尋**] 文字方塊中，輸入 **StackExchange.Redis** 或 **StackExchange.Redis.StrongName**，從結果中選取所需的版本，然後按一下 [**安裝**]。

>[AZURE.NOTE] 如果您偏好使用強式名稱版本的 **StackExchange.Redis** 用戶端程式庫，請選擇 **StackExchange.Redis.StrongName**，否則選擇 **StackExchange.Redis**。

![StackExchange.Redis NuGet package][StackExchangeNuget]

NuGet 封裝會為您的用戶端應用程式下載並加入必要的組件參考，以利用 StackExchange.Redis 快取用戶端來存取 Azure Redis 快取。

一旦設定用戶端專案的快取功能，您就可以使用下列幾節中描述的技術來使用快取。

<a name="working-with-caches"></a>
## 使用快取

本節中的步驟描述如何利用快取執行常見工作。

-	[連接到快取][]
-   [從快取加入和擷取物件][]
-   [將 ASP.NET 工作階段狀態儲存在快取中][]

<a name="connect-to-cache"></a>
## 連接到快取

為了能夠以程式設計方式使用快取，您需要快取的參考。在您想要使用 StackExchange.Redis 用戶端來存取 Azure Redis 快取的檔案中，在頂端加入下面這一行：

    using StackExchange.Redis;

>[AZURE.NOTE] StackExchange.Redis 用戶端需要 .NET Framework 4 或更高版本。

Azure Redis 快取的連線是由 `ConnectionMultiplexer` 類別來管理。此類別的設計是供整個用戶端應用程式內共用和重複使用，而不需要針對每個操作來建立。 

若要連接至 Azure Redis 快取，並傳回已連接之 `ConnectionMultiplexer` 的執行個體，請呼叫靜態 `Connect` 方法，並傳入快取端點和金鑰，如以下範例所示。使用從服務入口網站產生的 Azure 金鑰，作為密碼參數。

	ConnectionMultiplexer connection = ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,ssl=true,password=...");

>[AZURE.NOTE] 警告：請勿將認證儲存在原始程式碼中。為了讓這個範例簡單明瞭，我會以原始程式碼來呈現認證內容。請參閱 [Windows Azure 網站：應用程式字串與連接字串的運作方式][] (英文)，以取得如何儲存認證的詳細資訊。

如果您不想使用 SSL，請設定 `ssl=false` 或是直接傳入端點和金鑰。

	connection = ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,password=...");

>[AZURE.NOTE] 如需進階連線組態選項的詳細資訊，請參閱 [StackExchange.Redis 設定模型][] (英文)。

快取端點和金鑰可自您的快取執行個體的 Azure 管理預覽入口網站分頁取得。

![Cache properties][CacheProperties]

![Manage keys][ManageKeys]

建立連線後，呼叫 `ConnectionMultiplexer.GetDatabase` 方法來傳回 Redis 快取資料庫的參考。

	// connection referes to a previously configured ConnectionMultiplexer
	IDatabase cache = connection.GetDatabase();

>[AZURE.NOTE] 從 `GetDatabase` 方法傳回的物件是輕量型傳遞物件，而且不需要儲存。

	ConnectionMultiplexer connection = ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,ssl=true,password=...");

	IDatabase cache = connection.GetDatabase();

	// Perform cache operations using the cache object...
	// Simple put of integral data types into the cache
	cache.StringSet("key1", "value");
	cache.StringSet("key2", 25);

	// Simple get of data types from the cache
	string key1 = cache.StringGet("key1");
	int key2 = (int)cache.StringGet("key2");

現在您知道如何連線至 Azure Redis 快取執行個體，並傳回快取資料庫的參考，讓我們看看如何使用快取。



<a name="add-object"></a>
## 從快取加入和擷取物件

您可以使用 `StringSet` 和 `StringGet` 方法，在快取中儲存或擷取項目。

	// If key1 exists, it is overwritten.
	cache.StringSet("key1", "value1");

	string value = cache.StringGet("key1");

>[AZURE.NOTE] Redis 會將多數資料儲存為 Redis 字串，但這些字串可能包含許多類型的資料，包括序列化的二進位資料 (在快取中儲存 .NET 物件時可能會用到)。

呼叫 `StringGet` 時，如果物件已存在，即會傳回，如果物件不存在，則會傳回 Null。在此情況下，您可以從需要的資料來源中擷取值，並將它儲存在快取中供後續使用。這稱為另行快取模式。

    string value = cache.StringGet("key1");
    if (value == null)
    {
        // The item keyed by "key1" is not in the cache. Obtain
        // it from the desired data source and add it to the cache.
        value = GetValueFromDataSource();

        cache.StringSet("key1", value);
    }

>[AZURE.NOTE] Azure Redis 快取可以快取 .NET 物件及基本資料類型，但必須先將 .NET 物件序列化，才能加以快取。這是應用程式開發人員的責任，同時賦與開發人員選擇序列化程式的彈性。如需詳細資訊，請參閱[在快取中使用 .NET 物件][]。

<a name="specify-expiration"></a>
## 指定快取中項目的到期時間

若要指定快取中項目的到期時間，請使用 `StringSet` 的 `TimeSpan` 參數。

	cache.StringSet("key1", "value1", TimeSpan.FromMinutes(90));


<a name="store-session"></a>
## 將 ASP.NET 工作階段狀態儲存在快取中

Azure Redis 快取提供工作階段狀態提供者，可讓您用來將工作階段狀態儲存在快取中，而不是記憶體內或 SQL Server 資料庫中。若要使用快取工作階段
狀態提供者，請先設定快取，再使用「Redis 快取工作階段狀態 NuGet 封裝」設定 ASP.NET 應用程式的快取。

若要在 Visual Studio 中使用「Redis 快取工作階段狀態 NuGet 封裝」來設定用戶端應用程式，請在 [**方案總管**] 中的專案上按一下滑鼠右鍵，然後選擇 [**管理 NuGet 封裝**]。 

![Manage NuGet packages][NuGetMenu]

在 [**線上搜尋**] 文字方塊中輸入 **RedisSessionStateProvider**，從結果中選取它，然後按一下 [**安裝**]。

![Redis Cache Session State NuGet Package][SessionStateNuGet]

NuGet 封裝會下載並加入需要的組件參考，並將下列區段加入至您的 web.config 檔案，該檔案包含 ASP.NET 應用程式使用 Redis 快取工作階段狀態提供者所需的組態。

  <sessionState mode="Custom" customProvider="MySessionStateStore">
      <providers>
        <!--
          <add name="MySessionStateStore" 
            host = "127.0.0.1" [String]
            port = "" [number]
            accessKey = "" [String]
            ssl = "false" [true|false]
            throwOnError = "true" [true|false]
            retryTimeoutInMilliseconds = "0" [number]
            databaseId = "0" [number]
            applicationName = "" [String]
            connectionTimeoutInMilliseconds = "5000" [number]
            operationTimeoutInMilliseconds = "5000" [number]
          />
        -->
        <add name="MySessionStateStore" type="Microsoft.Web.Redis.RedisSessionStateProvider" host="127.0.0.1" accessKey="" ssl="false" />
      </providers>
    </sessionState>



標示註解的區段可提供屬性的範例和屬性的範例設定。

以來自預覽入口網站的快取分頁的值設定屬性，並視需要設定其他值。

	<sessionState mode="Custom" customProvider="MySessionStateStore">
      <providers>
        <!--
          <add name="MySessionStateStore" 
            host = "127.0.0.1" [String]
            port = "" [number]
            accessKey = "" [String]
            ssl = "false" [true|false]
            throwOnError = "true" [true|false]
            retryTimeoutInMilliseconds = "0" [number]
            databaseId = "0" [number]
            applicationName = "" [String]
            connectionTimeoutInMilliseconds = "5000" [number]
            operationTimeoutInMilliseconds = "5000" [number]
          />
        -->
        <add name="MySessionStateStore" type="Microsoft.Web.Redis.RedisSessionStateProvider" host="contoso5.redis.cache.windows.net" 
		accessKey="..." ssl="true" />
      </providers>
    </sessionState>

務必將標準的 **InProc** 工作階段狀態提供者註解化。

    <!-- <sessionState mode="InProc" customProvider="DefaultSessionProvider">
      <providers>
        <add name="DefaultSessionProvider" type="System.Web.Providers.DefaultSessionStateProvider, System.Web.Providers, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" connectionStringName="DefaultConnection" />
      </providers>
    </sessionState> -->

如需進行這些設定和使用 Azure Redis 工作階段狀態提供者的詳細資訊，請參閱 [Azure Redis 工作階段狀態提供者][]。

<a name="next-steps"></a>
## 後續步驟

現在，您已學到 Azure Redis 快取的基本概念，
請循著下列連結以了解如何執行更複雜的快取工作。

-	深入了解 StackExchange.Redis 用戶端：[StackExchange.Redis 快取用戶端文件][]
-	請參閱 [redis][] (英文) 文件，並閱讀有關 [Redis 資料類型][] (英文) 和 [Redis 資料類型的 15 分鐘簡介][] (英文)。
-   請參閱 MSDN 參考：[Azure Redis 快取][]


<!-- INTRA-TOPIC LINKS -->
[後續步驟]: #next-steps
[何謂 Azure Redis 快取？]: #what-is
[建立 Azure 快取]: #create-cache
[哪一種快取最適合我？]: #choosing-cache
[準備 Visual Studio 專案來使用 Azure 快取]: #prepare-vs
[設定應用程式以使用快取]: #configure-app
[開始使用 Azure Redis 快取]: #getting-started-cache-service
[建立快取]: #create-cache
[設定快取]: #enable-caching
[設定快取用戶端]: #NuGet
[使用快取]: #working-with-caches
[連接到快取]: #connect-to-cache
[從快取加入和擷取物件]: #add-object
[指定快取中物件的到期時間]: #specify-expiration
[將 ASP.NET 工作階段狀態儲存在快取中]: #store-session

  
<!-- IMAGES -->
[NewCacheMenu]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-new-cache-menu.png

[CacheCreate]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-cache-create.png

[StackExchangeNuget]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-stackexchange-redis.png

[NuGetMenu]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-manage-nuget-menu.png

[CacheProperties]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-properties.png

[ManageKeys]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-manage-keys.png

[SessionStateNuGet]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-session-state-provider.png

[BrowseCaches]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-browse-caches.png

[Caches]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-caches.png

[CacheCreated]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-cache-created.png




   
<!-- LINKS -->
[Azure Redis 工作階段狀態提供者]: http://go.microsoft.com/fwlink/?LinkId=398249
[Azure 管理入口網站]: http://windows.azure.com/
[作法：以程式設計方式設定快取用戶端]: http://msdn.microsoft.com/zh-tw/library/windowsazure/gg618003.aspx
[Azure 快取的工作階段狀態提供者]: http://go.microsoft.com/fwlink/?LinkId=320835
[Azure AppFabric 快取：快取工作階段狀態]: http://www.microsoft.com/zh-tw/showcase/details.aspx?uuid=87c833e9-97a9-42b2-8bb1-7601f9b5ca20
[Azure 快取的輸出快取提供者]: http://go.microsoft.com/fwlink/?LinkId=320837
[Azure 共用快取]: http://msdn.microsoft.com/zh-tw/library/windowsazure/gg278356.aspx
[小組部落格]: http://blogs.msdn.com/b/windowsazure/
[Azure 快取]: http://www.microsoft.com/zh-tw/showcase/Search.aspx?phrase=azure+caching
[如何設定虛擬機器大小]: http://go.microsoft.com/fwlink/?LinkId=164387
[Azure 快取容量規劃考量]: http://go.microsoft.com/fwlink/?LinkId=320167
[Azure 快取]: http://go.microsoft.com/fwlink/?LinkId=252658
[作法：以宣告方式設定 ASP.NET 網頁的快取性]: http://msdn.microsoft.com/zh-tw/library/zd1ysf1y.aspx
[作法：以程式設計方式設定頁面的快取性]: http://msdn.microsoft.com/zh-tw/library/z852zf6b.aspx

[StackExchange.Redis 設定模組]: http://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Configuration.md

[使用快取中的 .NET 物件]: http://msdn.microsoft.com/zh-tw/library/dn690521.aspx#Objects


[NuGet 封裝管理員安裝]: http://go.microsoft.com/fwlink/?LinkId=240311
[快取定價詳細資料]: http://www.windowsazure.com/zh-tw/pricing/details/cache/
[管理入口網站]: https://manage.windowsazure.com/

[Azure Redis 快取的概觀]: http://go.microsoft.com/fwlink/?LinkId=320830
[Azure Redis 快取]: http://go.microsoft.com/fwlink/?LinkId=398247

[移轉至 Azure Redis 快取]: http://go.microsoft.com/fwlink/?LinkId=317347
[Azure Redis 快取範例]: http://go.microsoft.com/fwlink/?LinkId=320840
[使用資源群組管理您的 Azure 資源]: http://azure.microsoft.com/zh-tw/documentation/articles/azure-preview-portal-using-resource-groups/

[StackExchange.Redis]: http://github.com/StackExchange/StackExchange.Redis
[StackExchange.Redis 快取用戶端文件]: http://github.com/StackExchange/StackExchange.Redis#documentation

[Redis]: http://redis.io/documentation
[Redis 資料類型]: http://redis.io/topics/data-types
[Redis 資料類型的 15 分鐘簡介]: http://redis.io/topics/data-types-intro

[Windows Azure 網站：應用程式字串與連接字串的運作方式]: http://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/ (英文)
