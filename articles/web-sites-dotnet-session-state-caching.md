<properties linkid="video-center-index" urlDisplayName="index" pageTitle="Use ASP.NET session state with Azure Websites" metaKeywords="azure cache service session state" description="Learn how to use the Azure Cache Service to support ASP.NET session state caching." metaCanonical="" services="cache" documentationCenter=".NET" title="How to Use ASP.NET Session State with Azure Websites" authors="tdykstra"  solutions="" manager="wpickett" editor="mollybos"  />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="tdykstra"></tags>

# 如何將 ASP.NET 工作階段狀態與 Azure 網站搭配使用

*作者 [Rick Anderson][]。2014 年 7 月 1 日更新。*

本主題說明如何將 Azure Redis 快取服務 (預覽) 用於工作階段狀態。

如果您的 ASP.NET Web 應用程式使用工作階段狀態，則將需要設定外部工作階段狀態提供者 (可為 Redis 快取服務或 SQL Server 工作階段狀態提供者)。如果您使用工作階段狀態，但並未使用外部提供者，則您的 Web 應用程式只能限定一個執行個體。Redis 快取服務是最快最簡單的啟用方式。

以下是使用快取服務 (預覽) 以因應工作階段狀態快取需要的基本步驟：

-   [建立快取。][]
-   [將 RedisSessionStateProvider NuGet 封裝新增至 Web 應用程式。][]
-   [修改 web.config 檔案。][]
-   [使用工作階段物件來儲存與擷取快取項目。][]

## <span id="createcache"></span></a>建立快取

遵循[這些指示][]以建立快取。

## <span id="configureproject"></span></a>將 RedisSessionStateProvider NuGet 封裝新增至 Web 應用程式

安裝 NuGet `RedisSessionStateProvider` 封裝。從封裝管理員主控台 ([工具] \> [NuGet 封裝管理員] \> [封裝管理員主控台]) 中使用下列命令來安裝：

`PM> Install-Package RedisSessionStateProvider -IncludePrerelease`

若要從 [工具] \> [NuGet 封裝管理員] \> [管理解決方案的 NugGet 封裝] 安裝，請搜尋 `RedisSessionStateProvider`，然後務必指定 [包含搶鮮版]。

如需詳細資訊，請參閱 [NuGet RedisSessionStateProvider 頁面][]和[設定快取用戶端][] 。

## <span id="configurewebconfig"></span></a>修改 Web.Config 檔案

除了對快取進行組件參考之外，NuGet 封裝還能在 *web.config* 檔案中加入虛設常式項目。

1.  開啟 *web.config*，然後尋找 **sessionState** 元素。

2.  輸入 `host`、`accessKey`、`port` (SSL 連接埠應為 6380) 的值，然後將 `SSL` 設定為 `true`。這些值可自您快取執行個體的 Azure 管理預覽入口網站分頁取得。如需詳細資訊，請參閱[連接到快取][]。
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

## <span id="usesessionobject"></span></a>在程式碼中使用工作階段物件

最後一步，則是開始在您的 ASP.NET 程式碼中使用工作階段物件。您可以使用 **Session.Add** 方法，將物件新增至工作階段狀態。此方法使用機碼值組，將項目儲存到工作階段狀態快取。

    string strValue = "yourvalue";
    Session.Add("yourkey", strValue);

以下程式碼會從工作階段狀態擷取此值。

    object objValue = Session["yourkey"];
    if (objValue != null)
       strValue = (string)obj;  

您也可以使用 Redis 快取來快取 Web 應用程式中物件。如需詳細資訊，請參閱[使用 Azure Redis 快取的 MVC 電影應用程式 (15 分鐘)][]。
如需如何使用 ASP.NET 工作階段狀態的詳細資訊，請參閱 [ASP.NET 工作階段狀態概觀][]。

  [Rick Anderson]: https://twitter.com/RickAndMSFT
  [建立快取。]: #createcache
  [將 RedisSessionStateProvider NuGet 封裝新增至 Web 應用程式。]: #configureproject
  [修改 web.config 檔案。]: #configurewebconfig
  [使用工作階段物件來儲存與擷取快取項目。]: #usesessionobject
  [這些指示]: http://azure.microsoft.com/zh-TW/documentation/articles/cache-dotnet-how-to-use-azure-redis-cache/#create-cache
  [NuGet RedisSessionStateProvider 頁面]: http://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider/
  [設定快取用戶端]: http://azure.microsoft.com/zh-TW/documentation/articles/cache-dotnet-how-to-use-azure-redis-cache/#NuGet
  [連接到快取]: http://azure.microsoft.com/zh-TW/documentation/articles/cache-dotnet-how-to-use-azure-redis-cache/#connect-to-cache
  [使用 Azure Redis 快取的 MVC 電影應用程式 (15 分鐘)]: http://azure.microsoft.com/blog/2014/06/05/mvc-movie-app-with-azure-redis-cache-in-15-minutes/
  [ASP.NET 工作階段狀態概觀]: http://msdn.microsoft.com/zh-TW/library/ms178581.aspx
