<properties 
	pageTitle="在 ASP.NET 應用程式中使用 Application Insights 的診斷失敗和例外狀況" 
	description="擷取從 ASP.NET 應用程式與所要求遙測的例外狀況。" 
	services="application-insights" 
    documentationCenter=".net"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/17/2015" 
	ms.author="awills"/>


# 設定 Application Insights：診斷例外狀況

[AZURE.INCLUDE [app-insights-selector-get-started-dotnet](../../includes/app-insights-selector-get-started-dotnet.md)]


藉由使用 [Visual Studio Application Insights][start] 監視您的應用程式，就可以在用戶端和伺服器端讓失敗的要求與例外狀況和其他事件相互關聯，如此您就可以快速地診斷原因。

若要監視 ASP.NET 應用程式，您必須在應用程式中[加入 Application Insights SDK][greenbrown]，或[在 IIS 伺服器上安裝狀態監視器][redfield]，或者，如果您的應用程式是 Azure Web 應用程式，加入 [Application Insights 延伸模組][azure]。

## 診斷錯誤 

從 [概觀] 刀鋒視窗上，失敗磚會顯示例外狀況和失敗的 HTTP 要求的圖表，以及導致最頻繁失敗的要求 URL 的清單。

![選取失敗](./media/app-insights-asp-net-exceptions/012-start.png)

點選清單中其中一個失敗要求類型，以取得失敗的個別發生次數。從該處按一下例外狀況或任何與其相關聯的追蹤資料：

![選取失敗要求的執行個體，並在例外狀況詳細資料底下，取得例外狀況的執行個體。](./media/app-insights-asp-net-exceptions/030-req-drill.png)


**或者**，您可以從例外狀況清單開始，您會在該處進一步發現 [失敗] 刀鋒視窗。請按一下直到最終到達個別例外狀況。


![鑽研](./media/app-insights-asp-net-exceptions/040-exception-drill.png)

*未顯示例外狀況？ 請參閱[擷取例外狀況](#exceptions)。*

您可以從那裡查看堆疊追蹤和每個例外狀況的詳細屬性，並且找到相關的記錄追蹤或其他事件。


![鑽研](./media/app-insights-asp-net-exceptions/050-exception-properties.png)

[深入了解診斷搜尋][diagnostic]。

## 相依性失敗

*相依性*是您的應用程式呼叫的服務，通常透過 REST API 或資料庫連線。[Application Insights 狀態監視器][redfield]會自動監視各種類型的相依性呼叫，測量呼叫持續時間及成功或失敗。

若要取得相依性資料，您必須在 IIS 伺服器上[安裝狀態監視器][redfield]，或者，如果您的應用程式是 Azure Web 應用程式，則使用 [Application Insights 延伸模組][azure]。

失敗的相依性呼叫列在 [失敗] 刀鋒視窗，您也可以在要求詳細資料和例外狀況詳細資料中的 [相關項目] 底下找到它們。

*沒有相依性失敗？ 非常好。但是，若要確認您取得相依性資料，請開啟 [效能] 刀鋒視窗，並查看 [相依性持續時間] 圖表。*

 

## 自訂追蹤和記錄資料

若要取得您的 app 的特定診斷資料，您可以插入程式碼以傳送您自己的遙測資料。這會隨著要求、頁面檢視和其他自動收集的資料顯示在診斷搜尋中。

您有幾種選項：

* [TrackEvent()](app-insights-api-custom-events-metrics.md#track-event) 通常用來監視使用模式，但它傳送的資料也會出現在診斷搜尋的 [自訂事件] 下。事件具有名稱，並且可以包含字串屬性和數值度量，您可以對其[篩選診斷搜尋][diagnostic]。
* [TrackTrace()](app-insights-api-custom-events-metrics.md#track-trace) 可讓您傳送較長的資料，例如 POST 資訊。
* [TrackException()](#exceptions) 會傳送堆疊追蹤。[深入了解例外狀況](#exceptions)。
* 如果您已經使用 Log4Net 或 NLog 等記錄架構，您可以[擷取這些記錄][netlogs]，並且在診斷搜尋中隨著要求和例外狀況資料查看記錄。

若要查看這些事件，請開啟[搜尋][diagnostic]，開啟 [篩選]，然後選擇 [自訂事件]、[追蹤] 或 [例外狀況]。


![鑽研](./media/app-insights-asp-net-exceptions/viewCustomEvents.png)


> [AZURE.NOTE]如果您的應用程式會產生大量遙測，調適性取樣模型會自動藉由僅傳送事件代表性片段，減少傳送到入口網站的量。為相同作業之一部分的事件會選取或取消選取為群組，讓您可以在相關事件之間瀏覽。[了解取樣。](app-insights-sampling.md)

### 如何查看要求 POST 資料

要求詳細資料不包括在 POST 呼叫中傳送至您的應用程式的資料。若要報告此資料：

* 在應用程式專案中[安裝 SDK][greenbrown]。
* 將程式碼插入您的應用程式以呼叫 [Microsoft.ApplicationInsights.TrackTrace()][api]。在訊息參數中傳送 POST 資料。允許的大小有限制，所以您應該嘗試只傳送基本的資料。
* 當您調查失敗的要求時，會發現相關聯的追蹤。  

![鑽研](./media/app-insights-asp-net-exceptions/060-req-related.png)


## <a name="exceptions"></a> 擷取例外狀況和相關的診斷資料

一開始，您不會在入口網站看到應用程式中造成失敗的所有例外狀況。您會看到任何瀏覽器例外狀況 (如果您在網頁中使用 [JavaScript SDK][client])。但是 IIS 會攔截大部分的伺服器例外狀況，而且您必須撰寫一段程式碼才能查看它們。

您可以：

* **明確記錄例外狀況**，方法是將程式碼插入例外狀況處理常式中，以報告例外狀況。
* **自動擷取例外狀況**，方法是設定您的 ASP.NET 架構。架構類型不同，則必要的新增項目也不同。

## 明確報告例外狀況

最簡單的方法是在例外狀況處理常式中插入 TrackException() 的呼叫。

JavaScript

    try 
    { ...
    }
    catch (ex)
    {
      appInsights.TrackException(ex, "handler loc",
        {Game: currentGame.Name, 
         State: currentGame.State.ToString()});
    }

C#

    var telemetry = new TelemetryClient();
    ...
    try 
    { ...
    }
    catch (Exception ex)
    {
       // Set up some properties:
       var properties = new Dictionary <string, string> 
         {{"Game", currentGame.Name}};

       var measurements = new Dictionary <string, double>
         {{"Users", currentGame.Users.Count}};

       // Send the exception telemetry:
       telemetry.TrackException(ex, properties, measurements);
    }

VB

    Dim telemetry = New TelemetryClient
    ...
    Try
      ...
    Catch ex as Exception
      ' Set up some properties:
      Dim properties = New Dictionary (Of String, String)
      properties.Add("Game", currentGame.Name)

      Dim measurements = New Dictionary (Of String, Double)
      measurements.Add("Users", currentGame.Users.Count)
  
      ' Send the exception telemetry:
      telemetry.TrackException(ex, properties, measurements)
    End Try

屬性和度量的參數是選擇性，但很適用於[篩選和加入][diagnostic]額外的資訊。比方說，如果您有一個應用程式可以執行數個遊戲，則您可以找到與特定遊戲相關的所有例外狀況報告。您可以將許多項目加入至每個字典，且項目數量不限。

## 瀏覽器例外狀況

大部分的瀏覽器例外狀況都會報告。

如果您的網頁包含來自內容傳遞網路或其他網域的指令碼檔案，請確定指令碼標記具有屬性 ```crossorigin="anonymous"```，而且伺服器會傳送 [CORS 標頭](http://enable-cors.org/)。這可讓您從這些資源取得未處理 JavaScript 例外狀況的堆疊追蹤和詳細資料。

## Web Form

對於 Web Form，HTTP 模組能夠在未使用 CustomErrors 設定重新導向時收集例外狀況。

但是，如果您有使用中的重新導向，將下列程式碼新增至 Global.asax.cs 中的 Application\_Error 函式。(如果您還沒有檔案，請加入 Global.asax 檔案。)

*C#*

    void Application_Error(object sender, EventArgs e)
    {
      if (HttpContext.Current.IsCustomErrorEnabled && Server.GetLastError  () != null)
      {
         var ai = new TelemetryClient(); // or re-use an existing instance

         ai.TrackException(Server.GetLastError());
      }
    }


## MVC

如果 [CustomErrors](https://msdn.microsoft.com/library/h0hfz6fc.aspx) 組態是 `Off`，則例外狀況將可供 [HTTP 模組](https://msdn.microsoft.com/library/ms178468.aspx) 收集。不過，如果它是 `RemoteOnly` (預設值) 或 `On`，則會清除例外狀況，且不適用於讓 Application Insights 自動收集。您可以藉由覆寫 [System.Web.Mvc.HandleErrorAttribute 類別](http://msdn.microsoft.com/library/system.web.mvc.handleerrorattribute.aspx)，並且針對以下不同的 MVC 版本如下所示的套用已覆寫的類別，來進行修正 ([github 來源](https://github.com/AppInsightsSamples/Mvc2UnhandledExceptions/blob/master/MVC2App/Controllers/AiHandleErrorAttribute.cs))：

    using System;
    using System.Web.Mvc;
    using Microsoft.ApplicationInsights;

    namespace MVC2App.Controllers
    {
      [AttributeUsage(AttributeTargets.Class | AttributeTargets.Method, Inherited = true, AllowMultiple = true)] 
      public class AiHandleErrorAttribute : HandleErrorAttribute
      {
        public override void OnException(ExceptionContext filterContext)
        {
            if (filterContext != null && filterContext.HttpContext != null && filterContext.Exception != null)
            {
                //If customError is Off, then AI HTTPModule will report the exception
                if (filterContext.HttpContext.IsCustomErrorEnabled)
                {   //or reuse instance (recommended!). see note above  
                    var ai = new TelemetryClient();
                    ai.TrackException(filterContext.Exception);
                } 
            }
            base.OnException(filterContext);
        }
      }
    }

#### MVC 2

使用您的控制器中的新屬性取代 HandleError 屬性。

    namespace MVC2App.Controllers
    {
       [AiHandleError]
       public class HomeController : Controller
       {
    ...

[範例](https://github.com/AppInsightsSamples/Mvc2UnhandledExceptions)

#### MVC 3

註冊 `AiHandleErrorAttribute` 做為 Global.asax.cs 中的全域篩選器：

    public class MyMvcApplication : System.Web.HttpApplication
    {
      public static void RegisterGlobalFilters(GlobalFilterCollection filters)
      {
         filters.Add(new AiHandleErrorAttribute());
      }
     ...

[範例](https://github.com/AppInsightsSamples/Mvc3UnhandledExceptionTelemetry)



#### MVC 4、MVC5

註冊 AiHandleErrorAttribute 做為 FilterConfig.cs 中的全域篩選器：

    public class FilterConfig
    {
      public static void RegisterGlobalFilters(GlobalFilterCollection filters)
      {
        // Default replaced with the override to track unhandled exceptions
        filters.Add(new AiHandleErrorAttribute());
      }
    }

[範例](https://github.com/AppInsightsSamples/Mvc5UnhandledExceptionTelemetry)

## Web API 1.x


覆寫 System.Web.Http.Filters.ExceptionFilterAttribute：

    using System.Web.Http.Filters;
    using Microsoft.ApplicationInsights;

    namespace WebAPI.App_Start
    {
      public class AiExceptionFilterAttribute : ExceptionFilterAttribute
      {
        public override void OnException(HttpActionExecutedContext actionExecutedContext)
        {
            if (actionExecutedContext != null && actionExecutedContext.Exception != null)
            {  //or reuse instance (recommended!). see note above 
                var ai = new TelemetryClient();
                ai.TrackException(actionExecutedContext.Exception);    
            }
            base.OnException(actionExecutedContext);
        }
      }
    }

您可以將此覆寫的屬性加入到特定的控制器，或將它加入至 WebApiConfig 類別中的全域篩選組態：

    using System.Web.Http;
    using WebApi1.x.App_Start;

    namespace WebApi1.x
    {
      public static class WebApiConfig
      {
        public static void Register(HttpConfiguration config)
        {
            config.Routes.MapHttpRoute(name: "DefaultApi", routeTemplate: "api/{controller}/{id}",
                defaults: new { id = RouteParameter.Optional });
            ...
            config.EnableSystemDiagnosticsTracing();

            // Capture exceptions for Application Insights:
            config.Filters.Add(new AiExceptionFilterAttribute());
        }
      }
    }

[範例](https://github.com/AppInsightsSamples/WebApi_1.x_UnhandledExceptions)

有一些無法處理的例外狀況篩選器案例。例如：

* 從控制器建構函式擲回的例外狀況。 
* 從訊息處理常式擲回的例外狀況。 
* 在路由期間擲回的例外狀況。 
* 在回應內容序列化期間擲回的例外狀況。 

## Web API 2.x

新增 IExceptionLogger 的實作：

    using System.Web.Http.ExceptionHandling;
    using Microsoft.ApplicationInsights;

    namespace ProductsAppPureWebAPI.App_Start
    {
      public class AiExceptionLogger : ExceptionLogger
      {
        public override void Log(ExceptionLoggerContext context)
        {
            if (context !=null && context.Exception != null)
            {//or reuse instance (recommended!). see note above 
                var ai = new TelemetryClient();
                ai.TrackException(context.Exception);
            }
            base.Log(context);
        }
      }
    }

將其新增至 WebApiConfig 中的服務：

    using System.Web.Http;
    using System.Web.Http.ExceptionHandling;
    using ProductsAppPureWebAPI.App_Start;

    namespace WebApi2WithMVC
    {
      public static class WebApiConfig
      {
        public static void Register(HttpConfiguration config)
        {
            // Web API configuration and services

            // Web API routes
            config.MapHttpAttributeRoutes();

            config.Routes.MapHttpRoute(
                name: "DefaultApi",
                routeTemplate: "api/{controller}/{id}",
                defaults: new { id = RouteParameter.Optional }
            );
            config.Services.Add(typeof(IExceptionLogger), new AiExceptionLogger()); 
        }
      }
  }

[範例](https://github.com/AppInsightsSamples/WebApi_2.x_UnhandledExceptions)

做為替代方案，您可以：

2. 以 IExceptionHandler 的自訂實作取代唯一的 ExceptionHandler。這只會在架構仍然可以選擇要傳送的回應訊息時呼叫 (不會在針對執行個體中止連接時呼叫) 
3. 例外狀況篩選器 (如以上的 Web API 1.x 控制器章節所述) - 在所有案例中均不會呼叫。


## WCF

新增類別，該類別會擴充屬性和實作 IErrorHandler 和 IServiceBehavior。

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.ServiceModel.Description;
    using System.ServiceModel.Dispatcher;
    using System.Web;
    using Microsoft.ApplicationInsights;

    namespace WcfService4.ErrorHandling
    {
      public class AiLogExceptionAttribute : Attribute, IErrorHandler, IServiceBehavior
      {
        public void AddBindingParameters(ServiceDescription serviceDescription,
            System.ServiceModel.ServiceHostBase serviceHostBase,
            System.Collections.ObjectModel.Collection<ServiceEndpoint> endpoints,
            System.ServiceModel.Channels.BindingParameterCollection bindingParameters)
        {
        }

        public void ApplyDispatchBehavior(ServiceDescription serviceDescription, 
            System.ServiceModel.ServiceHostBase serviceHostBase)
        {
            foreach (ChannelDispatcher disp in serviceHostBase.ChannelDispatchers)
            {
                disp.ErrorHandlers.Add(this);
            }
        }

        public void Validate(ServiceDescription serviceDescription, 
            System.ServiceModel.ServiceHostBase serviceHostBase)
        {
        }

        bool IErrorHandler.HandleError(Exception error)
        {//or reuse instance (recommended!). see note above 
            var ai = new TelemetryClient();

            ai.TrackException(error);
            return false;
        }

        void IErrorHandler.ProvideFault(Exception error, 
            System.ServiceModel.Channels.MessageVersion version, 
            ref System.ServiceModel.Channels.Message fault)
        {
        }
      }
    }

將屬性新增至服務實作：

    namespace WcfService4
    {
        [AiLogException]
        public class Service1 : IService1 
        { 
         ...

[範例](https://github.com/AppInsightsSamples/WCFUnhandledExceptions)

## 例外狀況效能計數器

若您已在伺服器上[安裝狀態監視器][redfield]，則可取得由 .NET 測量的例外狀況比率圖表。這包括已處理和未處理的 .NET 例外狀況。

開啟 [計量瀏覽器] 刀鋒視窗、加入新圖表，然後選取 [效能計數器] 下方所列的 [例外狀況比率]。

.NET framework 會計算間隔中的例外狀況次數並除以間隔長度，以計算得出例外狀況比率。

請注意，其與 Application Insights 入口網站執行 TrackException 報告計數算得的「例外狀況」計數不同。取樣間隔不同，且 SDK 不會針對所有已處理與未處理的例外狀況傳送 TrackException 報告。

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[azure]: ../insights-perf-analytics.md
[client]: app-insights-javascript.md
[diagnostic]: app-insights-diagnostic-search.md
[greenbrown]: app-insights-start-monitoring-app-health-usage.md
[netlogs]: app-insights-asp-net-trace-logs.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-overview.md

 

<!---HONumber=AcomDC_1125_2015-->