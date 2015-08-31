<properties 
	pageTitle="Application Insights 資料模型" 
	description="描述從 JSON 中的連續匯出匯出的屬性，並做為篩選器。" 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/12/2015" 
	ms.author="awills"/>

# Application Insights 匯出資料模型

此表列出從 [Application Insights](app-insights-overview.md) SDK 傳送至入口網站的遙測屬性。您會在[連續匯出](app-insights-export-telemetry.md)的資料輸出中看到這些屬性。它們也會出現在[度量總管](app-insights-metrics-explorer.md)和[診斷搜尋](app-insights-diagnostic-search.md)的屬性篩選中。

有數個[範例](app-insights-export-telemetry.md#code-samples)說明如何使用它們。

第一個區段的 "&lt;telemetryType&gt;" 是以下任何一個遙測類型名稱的預留位置：檢視、要求等等。


## &lt;telemetryType&gt;

**<measurement>**

    KVPs <string, double> <telemetryType>.measurement      Max: 100
* 
    金鑰值組 (KVP) 屬性包，提供 AppInsights 遙測項目上的擴充性以加入自訂度量。 

    *衍生：*測量名稱大小上限為 100

    *預設：*若為現有金鑰、遺漏值，則計數 = 1、值 = 0、下限/上限 = 0

**<property>**

    KVPs <string, string> <telemetryType>.properties      Max: 100
* 
    金鑰值組 (KVP) 屬性包，提供 AppInsights 遙測項目上的擴充性以加入自訂屬性。開發人員可提供與遙測項目相關聯的 KVP 清單。每個 AppInsights ikey (應用程式) 會追蹤每個金鑰並且最多可以提供 200 個唯一金鑰。金鑰的長度上限為 100 個字元。所有的值會被視為字串，且最多可提供 1000 個字元。每個屬性一開始都會分類為維度，可根據每個屬性的設定值啟用分割功能。每個屬性金鑰會追蹤每個設定值以取得其基數。當金鑰的基數超過 100 個唯一值時，會將屬性 (property) 分類為屬性 (attribute)。屬性可以搜尋，但不能是分割的目標 (彙總或分組依據)。 

    *衍生：*屬性名稱的大小上限為 100，屬性值的大小上限為 1024

    *預設：*若為現有金鑰、遺漏值，則值為 null

**計數**

    long <telemetryType>.count      
* 
    遙測項目的計數。   

    *衍生：*若為 null，計數 = 1

**duration**

    simpleMetric <telemetryType>.duration   ticks   
* 
    遙測項目的持續時間。若為要求，此為要求的執行時間。 

    *預設：*R1：若為檢視，此為選擇性欄位

**訊息**

    string <telemetrytype>.message      Max: 10240
* 
    遙測類型上的文字訊息。此字串可供全文檢索搜尋使用。 

**name**

    string <telemetrytype>.name      Max: 250
* 
    遙測項目的名稱。這個名稱在所有執行個體間是非唯一的，代表遙測類型的群組。若為檢視，則預設為 URLData.base。若為事件，則為開發人員提供的標籤。若為要求，則為要求的可讀取形式，例如 controller\\action。 

    *範例*<br/> 檢視名稱：<br/>70-486 測驗問題 1<br/>關於 - My ASP.NET 應用程式<br/><br/>範例要求名稱：<br/>POST /Components/WebHandlers/ItemCompare.ashx<br/>GET /signalr/poll<br/>GET /signalr/negotiate

**嚴重性**

    string <telemetryType>.severity      Max: 100
* 
    遙測項目的嚴重性。這對追蹤和例外狀況遙測項目是有效的 

**url**

    string <telemetrytype>.url      Max: 2048
* 
    Pageview、事件、要求或 RDD 的 URL。完整的 URL，且在全文檢索搜尋及匯出中受支援。此欄位可以有很高的基數且為屬性。它會剖析成一組 urlData 資料項目，可在計量瀏覽器中用於彙總。 

    *預設：*R2：在 remotedepencyType 上，如果 dependencyType = HTTP 此為必要欄位<br/>，則在 clientperformanceType 上，此為必要欄位

    *範例*<br/> https://icecream.contoso.com/main.aspx?etc=3&extraqs=%3fetc%3d3%26formid%3dc40d07a7-1cf1-4e1d-b00e-e61876d1284e&pagemode=iframe&pagetype=entityrecord<br/>http://fabrikam-oats.azurewebsites.net/index.htm

**urlData.base**

    string <telemetrytype>.urldata.base      Max: 2048
* 
    URL 資料項目的一部分，不包括主機、查詢 parms。它是根 URI。這個值可以用於分割/彙總。 

    *衍生：*查看 URL 轉換的附錄

    *範例*<br/> /main.aspx?etc=3&extraqs=%3fetc%3d3%26formid%3dc40d07a7-1cf1-4e1d-b00e-e61876d1284e&pagemode=iframe&pagetype=entityrecord<br/>/default.aspx<br/>/Patients/Search/<br/>

**urldata.hashTag**

    string <telemetrytype>.urldata.hashtag      Max: 100
* 
    URL 資料項目的雜湊標記文字 

    *衍生：*查看 URL 轉換的附錄

**urlData.host**

    string <telemetrytype>.urldata.host      Max: 200
* 
    URL 資料項目的主機。如果 URL 資料項目是本機的 URI，則以空白表示 

    *衍生：*查看 URL 轉換的附錄

    *範例*<br/> www.fabrikam.com<br/>www.contoso.com<br/>bretwpc711.azurewebsites.net<br/>

**urlData.port**

    string <telemetrytype>.urldata.port      Max: 100
* 
    如果在完整 URL 上表示則為 URL 資料項目的連接埠。否則為空白。 

    *衍生：*查看 URL 轉換的附錄

    *範例*<br/> 80<br/>443

**urlData.protocol**

    string <telemetrytype>.urldata.protocol      Max: 100
* 
    URL 資料項目的通訊協定 (HTTP、FTP 等) 

    *衍生：*查看 URL 轉換的附錄

    *範例*<br/> http<br/>https

**urlData.queryParameters.parameter**

    string <telemetrytype>.urldata.queryparameters.parameter      Max: 100
* 
    URL 資料項目的查詢參數名稱陣列 

    *衍生：*查看 URL 轉換的附錄

    *範例*<br/> etc<br/>extraqs<br/>pagemode<br/>pagetype

**urlData.queryParameters.value**

    string <telemetrytype>.urldata.queryparameters.value      Max: 100
* 
    從 URL 資料項目剖析的查詢參數值陣列。 

    *衍生：*查看 URL 轉換的附錄


## availability

**availability**

    simpleMetric availability.availability      
* 
    可用性測試的成功指標 

**dataSize**

    simpleMetric availability.datasize      
* 
    &lt;遙測&gt;.訊息文字訊息的大小 

**結果**

    enum (pass/fail) availability.result      
* 
    要擷取可用性 web 測試詳細資訊的指標 (HTTP 呼叫) 

**runLocation**

    string availability.runlocation      Max: 100
* 
    可用性測試的執行位置 

**testName**

    string availability.testname      Max: 1024
* 
    可用性測試的名稱 

**testRunId**

    string availability.testrunid      Max: 100
* 
    可用性測試執行之執行個體的唯一識別碼 

**testTimeStamp**

    datetime availability.testtimestamp      
* 
    可用性測試執行執行個體開始的時間戳記 


## basicexception

**assembly**

    string basicexception.assembly      Max: 100
**exceptionGroup**

    string basicException.exceptionGroup      
**exceptionType**

    string basicexception.exceptiontype      Max: 100
**failedUserCodeAssembly**

    string basicException.failedUserCodeAssembly      
**failedUserCodeMethod**

    string basicException.failedUserCodeMethod      
**handledAt**

    string basicexception.handledat      Max: 100
**innerMostExceptionMessage**

    string basicException.innermostExceptionMessage      
**innerMostExceptionThrownAtAssembly**

    string basicException.innermostExceptionThrownAtAssembly      
**innerMostExceptionThrownAtMethod**

    string basicException.innermostExceptionThrownAtMethod      
**innerMostExceptionType**

    string basicException.innermostExceptionType      
**方法**

    string basicexception.method      Max: 100
**outerMostExceptionMessage**

    string basicException.outerExceptionMessage      
**outerMostExceptionThrownAtAssembly**

    string basicException.outerExceptionThrownAtAssembly      
**outerMostExceptionTrownAtMethod**

    string basicException.outerExceptionThrownAtMethod      
**outerMostExceptionType**

    string basicException.outerExceptionType      
**problemid**

    string basicexception.problemid      Max: 100
* 
    *衍生：*查看呼叫堆疊剖析的附錄 

**Exceptions.Assembly**

    string basicexception.exceptions.assembly      Max: 100
**Exceptions.fileName**

    string basicexception.exceptions.filename      Max: 100
**Exceptions.hasFullStack**

    boolean basicexception.exceptions.hasfullstack      
**Exceptions.Level**

    string basicexception.exceptions.level      Max: 100
**Exceptions.Line**

    long basicexception.exceptions.line      
**exceptions.message**

    string basicexception.exceptions.message      Max: 10240
**Exceptions.Method**

    string basicexception.exceptions.method      Max: 100
**Exceptions.outerId**

    long basicexception.exceptions.outerid      
**Exceptions.parsedStack**

    List<StackFrame> basicexception.exceptions.parsedstack      
**Exceptions.Stack**

    string basicexception.exceptions.stack      Max: 10240
**Exceptions.typeName**

    string basicexception.exceptions.typename      Max: 100
**parsedStackAssembly**

    string basicException.parsedStack.assembly      
**parsedStackFilename**

    string basicException.parsedStack.fileName      
**parsedStackLevel**

    string basicException.parsedStack.level      
**parsedStackLine**

    string basicException.parsedStack.line      
**paseStackMethod**

    string basicException.parsedStack.method      

## clientperformance

**domProcessing**

    simpleMetric clientperformance.domprocessing   ms   
* 
    PerTotal 時間的一部分。這個部分代表應用程式處理回應所花費的時間。若為 web 用戶端，此為文件物件模型 (DOM) 處理時間。這個時間可使用現代瀏覽器的 perfTiming API 擷取。 

**networkConnect**

    simpleMetric clientperformance.networkconnect   ms   
* 
    PerTotal 時間的一部分。這個部分代表應用程式建立網路連線所花費的時間。這個時間可使用現代瀏覽器的 perfTiming API 擷取。 

**perfTotal**

    simpleMetric clientperformance.perftotal   ms   
* 
    載入檢視的總時間。若為 web 用戶端，此時間相當於「頁面載入時間」。這個時間可使用現代瀏覽器的 perfTiming API 擷取。 

**receiveResponse**

    simpleMetric clientperformance.receiveresponse   ms   
* 
    PerTotal 時間的一部分。這個部分代表應用程式接收要求回應所花費的時間。這個時間可使用現代瀏覽器的 perfTiming API 擷取。 

**sendRequest**

    simpleMetric clientperformance.sendrequest   ms   
* 
    PerTotal 時間的一部分。這個部分代表應用程式傳送要求給伺服器所花費的時間。這個時間可使用現代瀏覽器的 perfTiming API 擷取。 


## context

**applicationBuild**

    string context.application.build      Max: 100
* 
    應用程式的應用程式組建編號 

**applicationVersion**

    string context.application.version      Max: 100
* 
    用戶端應用程式的應用程式版本 

    *範例*<br/> 2015.5.21.3<br/>NokiaMailBye\_CD\_20150227.4

**telemetryType**

    string context.billing.telemetrytype      Max: 100
* 
    這表示計費記錄的遙測類型，以及使用的內部做為應用程式之可計費遙測項目的分割 

**dataId**

    string context.data.id      Max: 100
* 
    遙測項目的唯一識別碼。在資料集合端點指派。 

    *衍生：*產生的 UUID4

    *範例*<br/> edc6eaf3-3459-46a0-bb81-bedc24913864

**eventTime**

    datetime context.data.eventtime      
* 
    記錄在 UTC 的遙測事件時間。這通常會在用戶端填入。如果此欄位遺漏，它會在資料集合端點填入。此欄位的格式為 YYYY-MM-DDTHH:MM:SS.sssZ。    

    *範例*<br/> 2015-05-20T04:00:46.8338283Z

**samplingRate**

    string context.data.samplerate      Max: 100
* 
    資料產生者 (SDK) 的取樣率。1 以外的值在這裡表示與此遙測項目相關聯的度量代表取樣的值。因此，取樣率 0.05 會導致遙測項目 1 表示計數 20。 

**瀏覽器**

    string context.device.browser      Max: 100
* 
    用戶端的瀏覽器 

    *預設：*若為 null，則會根據使用者代理程式處理進行設定。請參閱使用者代理程式剖析的附錄

    *範例*<br/> Opera<br/>Mobile Safari<br/>Ovi Browser<br/>Chrome<br/>Firefox<br/>Internet Explorer

**browserVersion**

    string context.device.browserversion      Max: 100
* 
    用戶端的瀏覽器版本 

    *預設：*若為 null，則會根據使用者代理程式處理進行設定。請參閱使用者代理程式剖析的附錄

    *範例*<br/> Opera 12.17<br/>Mobile Safari 8.0<br/>Ovi Browser 5.5<br/>Chrome 37.0<br/>Firefox 21.0<br/>Internet Explorer 7.0

**deploymentId**

    string context.device.deploymentid      Max: 100
* 
    伺服器的部署識別碼 

**deviceId**

    string context.device.id      Max: 100
* 
    用戶端的唯一識別碼。產生的識別碼，應該儲存在本機裝置上，且不應為 PII，例如 MAC 位址或類似的不可變更識別碼。   

**deviceModel**

    string context.device.devicemodel      Max: 100
* 
    行動硬體用戶端的 devicemodel 

    *範例*<br/> 其他<br/>iPad<br/>Nokia 503s

**deviceName**

    string context.device.name      Max: 100
* 
    應用程式在其中執行的裝置名稱 

**deviceType**

    string context.device.type      Max: 100
* 
    用戶端硬體的裝置類型 

    *範例*<br/> 電腦<br/>行動裝置<br/>平板電腦

**語言**

    string context.device.language      Max: 100
* 
    用戶端上的應用程式語言。如果遙測項目上未明確提供，則以使用者代理程式欄位做為其來源。 

**地區設定**

    string context.device.locale      Max: 100
* 
    用戶端上的應用程式本機。如果遙測項目上未明確提供，則以使用者代理程式欄位做為其來源。 

    *範例*<br/> ru<br/>zh-tw<br/>de-DE<br/>未知

**machineName**

    string context.device.vmname      Max: 100
* 
    伺服器的機器名稱。若為虛擬化的運算，此資料項目相當於基礎主機。若為專用運算，則為機器名稱。 

**networkType**

    string context.device.network      Max: 100
* 
    用戶端的網路類型 

**oemName**

    string context.device.oemname      Max: 100
* 
    行動硬體用戶端的 oem 名稱 

**operatingSystem**

    string context.device.os      Max: 100
* 
    用戶端的作業系統 

    *預設：*若為 null，則會根據使用者代理程式處理進行設定。請參閱使用者代理程式剖析的附錄

    *範例*<br/> Windows<br/>iOS iPad<br/>Nokia

**operatingSystemVersion**

    string context.device.osversion      Max: 100
* 
    用戶端的作業系統版本 

    *預設：*若為 null，則會根據使用者代理程式處理進行設定。請參閱使用者代理程式剖析的附錄

    *範例*<br/> Windows XP<br/>iOS 8.3<br/>Nokia Series 40<br/>Windows 7<br/>Windows 8

**roleInstance**

    string context.device.roleinstance      Max: 100
* 
    伺服器運算的執行個體。在雲端/虛擬化案例中，這是運算執行個體的虛擬名稱。在專用運算執行個體中，則為機器名稱。若為 Azure 雲端服務，則應該預設為 PaaS 角色執行個體名稱或 IaaS 虛擬機器名稱  

**roleName**

    string context.device.rolename      Max: 100
* 
    群組伺服器運算執行個體的邏輯命名空間。若為 Azure 託管服務，PaaS 角色應該預設為 PaaS 角色名稱。IaaS 角色應該預設為虛擬機器名稱  

**screenHeight**

    string context.device.screenresolution.height      Max: 100
* 
    記錄遙測項目時用戶端硬體上應用程式的螢幕高度。如果未明確提供，則以 screenresolution 資料項目的轉換做為其來源。 

    *衍生：*從 context.device.screenresolution (如果有) 剖析

    *範例*<br/> 360<br/>1280<br/>1920

**screenResolution**

    string context.device.screenresolution      Max: 100
* 
    應用程式擷取遙測項目時的螢幕解析度。可在工作階段的過程中於直向和橫向之間切換。當這個屬性被帶到工作階段層級時，它會是擷取來代表完整工作階段的第一個螢幕解析度。 

    *範例*<br/> 螢幕解析度高度寬度<br/>360X640<br/>1280X800<br/>1920x1080

**screenWidth**

    string context.device.screenresolution.width      Max: 100
* 
    記錄遙測項目時用戶端硬體上應用程式的螢幕寬度。如果未明確提供，則以 screenresolution 資料項目的轉換做為其來源。 

    *衍生：*從 context.device.screenresolution (如果有) 剖析

    *範例*<br/> 640<br/>800<br/>1080

**userAgentString**

    string context.device.useragent      Max: 1000
* 
    用戶端瀏覽器的使用者代理程式 

    *預設：*若為 null，則設定為在資料收集端點擷取的 HTTP 使用者代理程式

    *範例*<br/> Opera/9.80 (Windows NT 5.1) Presto/2.12.388 版/12.17<br/>Mozilla/5.0 (iPad; CPU OS 8\_3 例如 Mac OS X) AppleWebKit/600.1.4 (KHTML，例如 Gecko) 版/8.0 Mobile/12F69 Safari/600.1.4<br/>Chrome/37.0.2062.124 Safari/537.36<br/>Mozilla/4.0 (相容; MSIE 7.0; Windows NT 6.1; Trident/7.0; SLCC2; .NET CLR 2.0.50727; .NET CLR 3.5.30729; .NET CLR 3.0.30729; Media Center PC 6.0; .NET4.0C; .NET4.0E)<br/>Safari/537.36<br/>+S89

**aiAgentVersion**

    string context.internal.agentversion      Max: 100
* 
    內部資料項目，表示產生遙測項目的 SDK 代理程式版本 

**city**

    string context.location.city      Max: 100
* 
    應用程式工作階段的城市。它可以直接在遙測項目上提供。如果不存在，會根據遙測項目上的 IPv4 填入。如果未提供任何 IPv4，此欄位保留空白。 

    *範例*<br/> Minsk<br/>Haarlem

**clientIpAddress**

    ipv4 context.location.clientip      
* 
    xxx.xxx.xxx.xxx 格式中的用戶端 IPv4 位址。   

    *預設：*若為 null，則設定為在資料收集端點擷取的 HTTP IP

    *範例*<br/> 0.123.63.143<br/>123.203.131.197

**continent**

    string context.location.continent      Max: 100
* 
    應用程式工作階段的洲。它可以直接在遙測項目上提供。如果不存在，會根據遙測項目上的 IPv4 填入。如果未提供任何 IPv4，此欄位保留空白。 

    *範例*<br/> 歐洲<br/>北美地區

**country**

    string context.location.country      Max: 100
* 
    應用程式工作階段的國家 (地區)。它可以直接在遙測項目上提供。如果不存在，會根據遙測項目上的 IPv4 填入。如果未提供任何 IPv4，此欄位保留空白。 

    *範例*<br/> 白俄羅斯<br/>荷蘭<br/>德國

**緯度**

    long context.location.point.lat      
* 
    *範例*<br/> 53.9<br/>45.7788 

**經度**

    long context.location.point.lon      
* 
    *範例*<br/> 27.5667<br/>-119.529 

**state**

    string context.location.province      Max: 100
* 
    應用程式工作階段的省/市。它可以直接在遙測項目上提供。如果不存在，會根據遙測項目上的 IPv4 填入。如果未提供任何 IPv4，此欄位保留空白。 

    *範例*<br/> 明斯克<br/>奧勒岡<br/>塞爾維亞中部<br/>奧里斯塔諾省

**operationId**

    string context.operation.id      Max: 100
* 
    Operation.id 是可以跨遙測項目使用的相互關聯識別碼。例如，要求識別碼可能填入所有相關遙測項目的 operation.id 中，跨遙測項目 (例如 rdd、例外狀況、事件等) 啟用相互關聯。  

**operationName**

    string context.operation.name      Max: 100
* 
    人類可讀取的作業名稱。查看作業識別碼。可允許像 Purchase Complete 等類似作業識別碼分組。   

**operationParentId**

     context.operation.parentid      
* 
    Operation.id 的父識別碼，允許使用遙測相互關聯識別碼的巢狀處理。   

**syntheticSource**

    string context.data.syntheticsource      Max: 100
* 
    如果 issynthetic=true，此資料項目代表綜合資料的來源。 

    *預設：*若為 null，則會檢查使用者代理程式是否有已知的綜合來源 (webcrawlers 等) 並且可能會據此設定來源。

**syntheticTransaction**

    boolean context.data.issynthetic      
* 
    遙測項目因為綜合測試 (而非真正的使用者活動) 而產生的指標。 

    *預設：*若為 null，則會檢查使用者代理程式是否有已知的綜合代理程式清單。如果找到符合項目，此值設為 true。<br/>如果使用者代理程式為 null，則設為 false

**session.Id**

    String context.session.id      Max: 100
* 
    與應用程式的真實使用者互動的唯一識別碼。這種互動是「工作階段」。在相同的 iKey 之下由應用程式所產生的所有遙測應該都包含這個唯一識別碼。<br/><br/>工作階段是在相同的使用者互動下由連續事件所定義。超過 30 分鐘沒有遙測事件的一段時間代表工作階段結束。   

    *預設：*在 MetricType、BillingType 上無效

    *範例*<br/> CFFC8B21-9828-4F56-AD7C-B6B5AC26B133

**accountAcquisitionDate**

    datetime context.user.accountAcquisitionDate      
**anonUserId**

    string context.user.anonId      Max: 100
* 
    在應用程式內定義匿名使用者的唯一識別碼。使用 SDK 時，會在用戶端上自動產生並保存。在相同登入下共用一個裝置時，卻不會在真實的使用者之間造成差異。它會在使用不同登入時，以及作業系統支援設定檔時，在真實的使用者之間造成差異。沒有驗證經驗時，它是唯一使用者的最佳 proxy。 

    *範例*<br/> f23854a1b01c4b1fa79fa2d9a5768526

**anonymousUserAcquisitionDate**

    datetime context.user.anonAcquisitionDate      
**authenticatedUserAcquisitionDate**

    datetime context.user.authAcquisitionDate      
**authUserId**

    string context.user.authId      Max: 100
* 
    在應用程式內定義已驗證使用者的唯一識別碼。由開發人員提供。已驗證使用者的好處是識別碼可在應用程式內跨裝置漫遊並仍然保留其唯一性。 

**storeRegion**

    string context.user.storeregion      Max: 100
* 
    做為應用程式來源的存放區區域。 

**userAcountId**

    string context.user.accountId      Max: 100
* 
    在應用程式內定義帳戶的唯一識別碼。由開發人員提供。 


## remotedependency

**async**

    boolean remotedependency.async      
* 
    遠端相依性呼叫是否為非同步的指標 

**commandName**

    string remotedependency.commandname      Max: 2048
* 
    遠端相依性為 SQL 時，遠端相依性的 SQL 命令名稱 

**dependencyTypeName**

    string remotedependency.dependencytypename      Max: 2048
* 
    遠端相依性的遠端相依性類型名稱 

**remoteDependencyName**

    string remotedependency.remotedependencyname      Max: 2048
* 
    遠端相依性的名稱 

    *衍生：*標準化為 &lt;telemetryType.name&gt;

**remoteDependencyType**

    string remotedependency.remotedependencytype      Max: 100
* 
    遠端相依性的類型。受支援的類型包括 SQL、AZURE 資源 (儲存體、佇列等) 和 HTTP。 

**成功**

    boolean remotedependency.success      
* 
    遠端相依性呼叫成功或失敗的指標。 


## 要求

**hasdetaileddata**

    boolean request.hasdetaileddata      
* 
    根據 operation.id 識別有沒有相關遙測項目的指標 

**httpMethod**

    string request.httpmethod      Max: 100
* 
    在要求上使用的 HTTP 方法。   

**id**

    string request.id      Max: 100
* 
    由 SDK 產生之要求的唯一識別碼。此識別碼可以進一步填入至作業識別碼屬性，讓相同要求所產生的遙測項目相互關聯。 

**responseCode**

    long request.responsecode      
* 
    要求的回應碼 

**成功**

    boolean request.success      
* 
    要求是否成功的指標。200 中的回應碼會視為成功。 

    *預設：*若為 null，則設為 true


## sessionmetric

**anonymousUserDurationSinceLastVisit**

    Long sessionmetric.anonymoususerdurationsincelastvisit      
* 
    這個匿名使用者識別項上次造訪時開始計算的時間。在第一次造訪時，這個值是空白的。在後續每次造訪時，即為每次造訪之間的時間，以天粒度表示。值為 3 表示從上一個工作階段執行個體到這個工作階段執行個體距離 3 天 

**anonymousUserSessionCount**

    Long sessionmetric.anonymoususersessioncount      
* 
    匿名使用者的造訪計數。它是此唯一匿名使用者識別碼之總歷程記錄工作階段的增量計數器。這個識別碼的每個工作階段都會讓計數器增量。如果 30 天內沒見到此使用者識別碼，此計數器就會清除，屆時計數器會再次重設，使用者識別碼的下一次造訪將會視為新的使用者。 

**authenticatedAccountDurationSinceLastVisit**

    Long sessionmetric.authenticatedaccountdurationsincelastvisit      
* 
    從此帳戶識別碼上一次造訪時開始計算的時間。在第一次造訪時，這個值是空白的。在後續每次造訪時，即為每次造訪之間的時間，以天粒度表示。值為 3 表示從上一個工作階段執行個體到這個工作階段執行個體距離 3 天 

**authenticatedAccountSessionCount**

    Long sessionmetric.authenticatedaccountsessioncount      
* 
    已驗證帳戶識別碼的造訪計數。它是此唯一帳戶識別碼之總歷程記錄工作階段的增量計數器。這個識別碼的每個工作階段都會讓計數器增量。如果 30 天內沒見到此使用者識別碼，此計數器就會清除，屆時計數器會再次重設，使用者識別碼的下一次造訪將會視為新的使用者。 

**authenticatedUserDurationSinceLastVisit**

    Long sessionmetric.authenticateduserdurationsincelastvisit      
* 
    從已驗證使用者識別碼上一次造訪時開始計算的時間。在第一次造訪時，這個值是空白的。在後續每次造訪時，即為每次造訪之間的時間，以天粒度表示。值為 3 表示從上一個工作階段執行個體到這個工作階段執行個體距離 3 天 

**authenticatedUserSessionCount**

    Long sessionmetric.authenticatedusersessioncount      
* 
    已驗證使用者識別碼的造訪計數。它是此唯一已驗證使用者識別碼之總歷程記錄工作階段的增量計數器。這個識別碼的每個工作階段都會讓計數器增量。如果 30 天內沒見到此使用者識別碼，此計數器就會清除，屆時計數器會再次重設，使用者識別碼的下一次造訪將會視為新的使用者。 

**crashCount**

    Long sessionmetric.crashcount      
* 
    在此工作階段執行個體期間發生的損毀計數。 

**duration**

    timespan sessionmetric.duration      
* 
    工作階段執行個體的持續時間 

**entryEvent**

    string sessionmetric.entryevent      Max: 200
* 
    工作階段的第一個事件。源自事件名稱並且可供 sessionMetric 度量做為分割/彙總使用 

    *衍生：*源自事件名稱

**entryUrl**

    string sessionmetric.entryurl      Max: 2048
* 
    工作階段的第一個 URL。源自 urlData.base 並且可供 sessionMetric 度量做為分割/彙總使用 

    *衍生：*源自 &lt;telemetryType&gt;.Url

**eventCount**

    Long sessionmetric.eventcount      
* 
    在此工作階段執行個體期間發生的事件計數 

**exceptionCount**

    Long sessionmetric.exceptioncount      
* 
    在這個工作階段執行個體期間發生的例外狀況計數 

**exitEvent**

    string sessionmetric.exitevent      Max: 200
* 
    工作階段的最後一個事件。源自事件名稱並且可供 sessionMetric 度量做為分割/彙總使用 

    *衍生：*源自事件名稱

**exitUrl**

    string sessionmetric.exiturl      Max: 2048
* 
    工作階段的最後一個 URL。源自 urlData.base 並且可供 sessionMetric 度量做為分割/彙總使用 

    *衍生：*源自 &lt;telemetryType&gt;.Url

**pageBounceCount**

    boolean sessionmetric.pagebouncecount      
* 
    此 sessionMetric 遙測項目代表之退回工作階段的計數。退回工作階段是根據單一檢視遙測項目建立的工作階段。 

    *衍生：*若 sessionMetric.viewCount + sessionMetric.requestCount = 1，則為 1，否則為 0

**pageCount**

    Long sessionmetric.pagecount      
* 
    在此工作階段執行個體期間發生的檢視計數 

**requestCount**

    Long sessionmetric.requestcount      
* 
    在此工作階段執行個體期間發生的要求計數 

**sessionCount**

    Long sessionmetric.sessioncount      
* 
    遙測的 sessionMetric 執行個體所表示的工作階段計數 


## trace

**context**

    string trace.context      Max: 100
* 
    追蹤/例外狀況時的 capp 內容 

**例外狀況**

    string trace.exception      Max: 10240
* 
    與追蹤遙測項目相關聯的例外狀況 

**摘要**

    string trace.excerpt      Max: 100
* 
    追蹤遙測項目的簡短文字訊息 

**formatMessage**

    string trace.formatmessage      Max: 100
* 
    追蹤遙測項目的格式訊息 

**formatProvider**

    string trace.formatprovider      Max: 100
* 
    追蹤遙測項目的格式提供者 

**hasStackTrace**

    boolean trace.hasstacktrace      
* 
    追蹤遙測項目是否包含堆疊追蹤的指標 

**層級**

    string trace.level      Max: 100
* 
    追蹤訊息的層級 

**loggerName**

    string trace.loggername      Max: 100
* 
    追蹤記錄器名稱 

**loggerShortName**

    string trace.loggershortname      Max: 100
* 
    記錄器的簡短名稱 

**訊息**

    string trace.message      Max: 10240
* 
    追蹤遙測項目的全文訊息 

**messageId**

    string trace.messageId      Max: 100
* 
    追蹤遙測項目的唯一識別碼 

**參數**

    string trace.parameters      Max: 100
* 
    這些是提供給追蹤遙測項目之追蹤記錄器的參數 

**processId**

    string trace.processId      Max: 100
* 
    記錄遙測項目時應用程式的處理序識別碼 

**sourceType**

    string trace.sourceType      Max: 100
* 
    追蹤遙測項目的 sourcetype 

**sqquenceId**

    string trace.sequenceid      Max: 100
* 
    追蹤提供者可能會用來記錄追蹤遙測項目序列的序列識別碼 

**stacktrace**

    string trace.stacktrace      Max: 100
* 
    針對追蹤遙測項目擷取的堆疊追蹤 

**threadId**

    string trace.threadId      Max: 100
* 
    記錄遙測項目時應用程式的 threadid 

**userStackframe**

    string trace.userstackframe      Max: 100
* 
    追蹤遙測項目的使用者堆疊框架 

**userStackframNum**

    string trace.userstackframenum      Max: 100
* 
    追蹤遙測項目的使用者堆疊框架數目 


## 檢視

**referrerDataUrl**

    string view.referralurl      Max: 2048
* 
    Pageview 的參考 URL。完整的 URL，且在全文檢索搜尋及匯出中受支援。此欄位可以有很高的基數且為屬性。它會剖析成一組 referralData 資料項目，可在計量瀏覽器中用於彙總。 

**referrerData.base**

    string view.referrerdata.base      Max: 2048
* 
    參考 URL 的一部分，不包括主機、查詢 parms。它是根 URI。這個值可以用於分割/彙總。 

    *衍生：*查看 URL 轉換的附錄

**referrerData.hashTag**

    string view.referrerdata.hashtag      Max: 100
* 
    參考 URL 的雜湊標記文字 

    *衍生：*查看 URL 轉換的附錄

**referrerData.host**

    string view.referrerdata.host      Max: 200
* 
    參考 URL 的主機。如果 URL 是本機的 URI，則以空白表示 

    *衍生：*查看 URL 轉換的附錄

**referrerData.port**

    string view.referrerdata.port      Max: 100
* 
    如果在完整 URL 上表示則為參考 URL 的連接埠。否則為空白。 

    *衍生：*查看 URL 轉換的附錄

**referrerData.protocol**

    string view.referrerdata.protocol      Max: 100
* 
    參考 URL 的通訊協定 (HTTP、FTP 等) 

    *衍生：*查看 URL 轉換的附錄

    *範例*<br/> http<br/>https

**referrerData.queryParameters.parameter**

    string view.referrerdata.queryparameters.parameter      Max: 100
* 
    參考 URL 的查詢參數名稱陣列 

    *衍生：*查看 URL 轉換的附錄

**referrerData.queryParameters.value**

    string view.referrerdata.queryparameters.value      Max: 100
* 
    從 referringData URL 剖析的查詢參數值陣列。 

    *衍生：*查看 URL 轉換的附錄



## 另請參閱

* [Application Insights](app-insights-overview.md) 
* [連續匯出](app-insights-export-telemetry.md)
* [程式碼範例](app-insights-export-telemetry.md#code-samples)

<!---HONumber=August15_HO8-->