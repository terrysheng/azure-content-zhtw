<properties 
	pageTitle="將 ASP.NET 工作階段狀態與 Azure 網站搭配使用" 
	description="了解如何使用 Azure 快取服務來支援 ASP.NET 工作階段狀態快取。" 
	services="cache" 
	documentationCenter=".net" 
	authors="riande" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="1/13/2015" 
	ms.author="Rick-Anderson"/>


# 如何將 ASP.NET 工作階段狀態與 Azure 網站搭配使用


本主題說明如何將 Azure Redis 快取服務 (預覽) 用於工作階段狀態。

如果您的 ASP.NET Web 應用程式使用工作階段狀態，則將需要設定外部工作階段狀態提供者 (可為 Redis 快取服務或 SQL Server 工作階段狀態提供者)。如果您使用工作階段狀態，但並未使用外部提供者，則您的 Web 應用程式只能限定一個執行個體。Redis 快取服務是最快最簡單的啟用方式。

以下是使用快取服務 (預覽) 以因應工作階段狀態快取需要的基本步驟：

* [建立快取。](#createcache)
* [將 RedisSessionStateProvider NuGet 封裝新增至 Web 應用程式。](#configureproject)
* [修改 web.config 檔案。](#configurewebconfig)
* [使用工作階段物件來儲存與擷取快取項目。](#usesessionobject)

<h2><a id="createcache"></a>建立快取</h2>
遵循[這些指示](http://azure.microsoft.com/zh-tw/documentation/articles/cache-dotnet-how-to-use-azure-redis-cache/#create-cache)來建立快取。

<h2><a id="configureproject"></a>將 RedisSessionStateProvider NuGet 封裝新增至 Web 應用程式</h2>
安裝 NuGet `RedisSessionStateProvider` 封裝。從封裝管理員主控台 ([工具]**** > [NuGet 封裝管理員]**** > [Package Manager Console]****) 中使用下列命令來安裝：

  `PM> Install-Package RedisSessionStateProvider -IncludePrerelease`
  
若要從 [工具]**** > [NuGet 封裝管理員]**** > [管理解決方案的 NugGet 封裝]**** 安裝，請搜尋 `RedisSessionStateProvider`，並務必指定 [包含搶鮮版]****。

如需詳細資訊，請參閱 [NuGet RedisSessionStateProvider 頁面](http://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider/ )和[設定快取用戶端](http://azure.microsoft.com/zh-tw/documentation/articles/cache-dotnet-how-to-use-azure-redis-cache/#NuGet)。

<h2><a id="configurewebconfig"></a>修改 Web.Config 檔案</h2>
除了對快取進行組件參考之外，NuGet 封裝還能在 *web.config* 檔案中新增虛設常式項目。 

1. 開啟 *web.config*，然後尋找 **sessionState** 項目。

1. 輸入 `host`、 `accessKey`、 `port` (SSL 連接埠應為 6380) 的值，然後將 `SSL` 設為 `true`。這些值可自您快取執行個體的 Azure 管理預覽入口網站分頁取得。如需詳細資訊，請參閱[連接到快取](http://azure.microsoft.com/zh-tw/documentation/articles/cache-dotnet-how-to-use-azure-redis-cache/#connect-to-cache)。
下列標記顯示 *web.config* 檔案的變更。


  <pre class="prettyprint">  
    &lt;system.web&gt;
    &lt;customErrors mode="Off" /&gt;
    &lt;authentication mode="None" /&gt;
    &lt;compilation debug="true" targetFramework="4.5" /&gt;
    &lt;httpRuntime targetFramework="4.5" /&gt;
  &lt;sessionState mode="Custom" customProvider="RedisSessionProvider"&gt;
      &lt;providers&gt;  
          &lt;!--&lt;add name="RedisSessionProvider" 
            host = "127.0.0.1" [String]
            port = "" [number]
            accessKey = "" [String]
            ssl = "false" [true|false]
            throwOnError = "true" [true|false]
            retryTimeoutInMilliseconds = "0" [number]
            databaseId = "0" [number]
            applicationName = "" [String]
          /&gt;--&gt;
         &lt;add name="RedisSessionProvider" 
              type="Microsoft.Web.Redis.RedisSessionStateProvider" 
              <mark>port="6380"
              host="movie2.redis.cache.windows.net" 
              accessKey="m7PNV60CrvKpLqMUxosC3dSe6kx9nQ6jP5del8TmADk=" 
              ssl="true"</mark> /&gt;
      &lt;!--&lt;add name="MySessionStateStore" type="Microsoft.Web.Redis.RedisSessionStateProvider" host="127.0.0.1" accessKey="" ssl="false" /&gt;--&gt;
      &lt;/providers&gt;
    &lt;/sessionState&gt;
  &lt;/system.web&gt;</pre>


<h2><a id="usesessionobject"></a>在程式碼中使用工作階段物件</h2>
最後一步，則是開始在您的 ASP.NET 程式碼中使用工作階段物件。您可以使用 **Session.Add** 方法，將物件新增至工作階段狀態。此方法使用機碼值組，將項目儲存到工作階段狀態快取。

    string strValue = "yourvalue";
	Session.Add("yourkey", strValue);

以下程式碼會從工作階段狀態擷取此值。

    object objValue = Session["yourkey"];
    if (objValue != null)
       strValue = (string)obj;	

您也可以使用 Redis 快取來快取 Web 應用程式中物件。如需詳細資訊，請參閱 [15 分鐘學會包含 Azure Redis 快取的 MVC 影片應用程式](http://azure.microsoft.com/blog/2014/06/05/mvc-movie-app-with-azure-redis-cache-in-15-minutes/)。
如需如何使用 ASP.NET 工作階段狀態的詳細資訊，請參閱 [ASP.NET 工作階段狀態概觀][]。

  *作者：[Rick Anderson](https://twitter.com/RickAndMSFT)*
  
  [installed the latest]: http://www.windowsazure.com/zh-tw/downloads/?sdk=net  
  [ASP.NET Session State Overview]: http://msdn.microsoft.com/zh-tw/library/ms178581.aspx

  [NewIcon]: ./media/web-sites-dotnet-session-state-caching/CacheScreenshot_NewButton.png
  [NewCacheDialog]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CreateOptions.png
  [CacheIcon]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CacheIcon.png
  [NuGetDialog]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_NuGet.png
  [OutputConfig]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_OC_WebConfig.png
  [CacheConfig]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CacheConfig.png
  [EndpointURL]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_EndpointURL.png
  [ManageKeys]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_ManageAccessKeys.png


<!--HONumber=42-->
