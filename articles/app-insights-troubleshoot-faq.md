<properties title="Troubleshooting and Q & A about Application Insights" pageTitle="Application Insights 的疑難排解和問答集" description="Tips and troubleshooting" metaKeywords="analytics monitoring" authors="awills"  manager="kamrani" />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="2014-09-24" ms.author="awills" />
 
# 疑難排解及問與答 - Microsoft Azure 預覽的應用程式分析

+ [我在 Visual Studio 中沒有看到任何選項可將 Application Insights 加入至我的專案](#q01)
+ [已建立新的 Web 專案，但加入 Application Insights 失敗。](#q02)
+ [我已成功加入 Application Insights 並執行我的應用程式，但在入口網站中從未看到資料。](#q03)
+ [我在流量分析下沒有看到資料](#q04)
+ [我查看 Microsoft Azure 預覽開始面板。我如何在 Application Insights 中尋找我的資料？](#q05)
+ [如何變更在哪個 Azure 資源下顯示我的資料？](#update)
+ [我收到「檢測機碼不能是空白」的錯誤](#emptykey)
+ [在 Microsoft Azure 預覽主畫面上，該對應會顯示我的應用程式狀態嗎？](#q06)
+ [當我使用 [將 Application Insights 加我至我的應用程式] 並開啟 Application Insights 入口網站時，看起來與螢幕擷取畫面完全不同。](#q07)
+ [我可以使用 Application Insights 來監視內部網路 Web 伺服器嗎？](#q08)
+ [我如何取得 Windows Phone 或 Windows 市集的資料？](#q09)
+ [我如何在程式碼中查看我記錄的事件和頁面檢視？](#q10)
+ [為何會有兩個 Application Insights 版本？](#q11)
+ [我如何取回原本在 Visual Studio Online 版本的 Application Insights 中的所有功能？](#q13)
+ [Application Insights 在我的專案中修改什麼？](#q14)
+ [我如何在 Application Insights 中尋找我的結果？](#q15)
+ [在我的防火牆應該開啟哪些連接埠？](#q16)
+ [我已啟用在 Application Insights 中的所有項目嗎？](#q17)
+ [詳細資訊](#next)



## <a name="q01"></a>我在 Visual Studio 中沒有看到任何選項可將 Application Insights 加入至我的專案

+ 請確定您有 [Visual Studio Update 3](http://go.microsoft.com/fwlink/?LinkId=397827)。它會預先安裝 Application Insights Tools，您應該可以在擴充管理員中看到此工具。
+ Application Insights on Microsoft Azure Preview 目前僅適用於 C# 或 Visual Basic 的 ASP.NET Web 專案。
+ 如果您有現有的專案，請移至 [方案總管]，並確定您按一下 Web 專案 (不是其他專案或方案)。您應該會看到功能表項目 [Add Application Insights Telemetry to Project]。
+ 如果是建立新專案，請在 Visual Studio 中開啟 [檔案] > [新增專案]，然後選取 {Visual C#|Visual Basic} > [Web] > [ASP.NET Web 應用程式]。應該會有 [Add Application Insights to Project] 的選項。

## <a name="q02"></a>已建立新的 Web 專案，但加入 Application Insights 失敗。

如果與 Application Insights 入口網站通訊失敗，或您的帳戶有問題，就可能發生此情形。

+ 請檢查您為正確的 Azure 帳戶提供登入認證。Microsoft Azure 認證 (在 [新增專案] 對話方塊中) 與 Visual Studio Online 認證 (在 Visual Studio 右上方) 可能不同。
+ 請稍待片刻，然後[將 Application Insights 加入至現有的專案][start]。
+ 移至 Microsoft Azure 帳戶設定來檢查限制。檢查您是否可以手動加入 Application Insights 應用程式。


## <a name="q03"></a>我已成功加入 Application Insights 並執行我的應用程式，但在入口網站中從未看到資料。

+ 您必須關閉再開啟您在等待資料的任何分頁。在目前版本中，分頁的內容不會自動重新整理。
+ 在 Microsoft Azure 開始面板中，查看服務狀態對應。如果看到一些警示指示，請等待它們恢復 [正常]，然後關閉再重新開啟 Application Insights 應用程式分頁。
+ 在防火牆中，您可能必須開啟 TCP 連接埠 80 和 443，以允許連出流量送往 dc.services.visualstudio.com 和 f5.services.visualstudio.com。

## <a name="q04"></a>我在流量分析下沒有看到資料

+ 資料來自網頁中的指令碼。如果您將 Application Insights 加入至現有的 Web 專案，則[您必須手動加入指令碼][start]。


## <a name="q05"></a>我查看 Microsoft Azure 預覽開始面板。我如何在 Application Insights 中尋找我的資料？

下列其中一種方法：

* 選擇 [瀏覽]、[Application Insights]、您的專案名稱。如果您在其中沒有任何專案，則需要[在 Visual Studio 中將 Application Insights 加入至 Web 專案][start]。

* 在 Visual Studio [方案總管] 中，以滑鼠右鍵按一下 Web 專案，然後選擇 [Open Application Insights Portal]。

## <a name="update"></a>如何變更我的專案將資料傳送到哪一個 Azure 資源？

在 [方案總管] 中，以滑鼠右鍵按一下 `ApplicationInsights.config`，然後選擇 [**Update Application Insights**]。您可以將資料傳送至 Azure 中的現有資源或新資源。更新精靈會變更 ApplicationInsights.config 中的檢測機碼，這決定伺服器 SDK 將您的資料送往何處。除非您取消選取 [全部更新]，否則它也會變更機碼出現在您網頁中的位置。

## <a name="emptykey"></a>我收到「檢測機碼不能是空白」的錯誤

可能是您在安裝 Application Insights 或記錄配接器時發生問題。

在 [方案總管] 中，以滑鼠右鍵按一下 `ApplicationInsights.config`，然後選擇 [**Update Application Insights**]。將會出現對話方塊邀請您登入 Azure，並建立 Application Insights 資源或重複使用現有的資源。

## <a name="q06"></a>在 Microsoft Azure 預覽主畫面上，該對應會顯示我的應用程式狀態嗎？

不是！它是顯示 Azure 服務的狀態。若要查看 Web 測試結果，請選擇 [瀏覽] > [Application Insights] > (您的應用程式)，然後查看 Web 測試結果。 


## <a name="q07"></a>當我使用 [將 Application Insights 加我至我的應用程式] 並開啟 Application Insights 入口網站時，看起來與螢幕擷取畫面完全不同。

您可能是使用[舊版的 Application Insights Tools](http://msdn.microsoft.com/library/dn793604.aspx)，它會連接至 Visual Studio Online 版本。

您查看的說明頁面是關於 [Application Insights for Microsoft Azure Preview][start]，這在 Visual Studio Update 3 中已開啟。 

## <a name="q08"></a>我可以使用 Application Insights 來監視內部網路 Web 伺服器嗎？

是，如果您的伺服器可以傳送資料至公用網際網路，您可以監視健全狀況和使用量。

但如果您想要對服務執行 Web 測試，則必須能夠從公用網際網路存取它。

## <a name="q09"></a>我如何取得 Windows Phone 或 Windows 市集的資料？

在 Microsoft Azure 版本中尚未支援。請使用 [Visual Studio Online 版本][older]。


## <a name="q10"></a>我如何在程式碼中查看我記錄的事件和頁面檢視？

我們在 Microsoft Azure 版本中還不支援此功能。敬請期待。目前，您可以嘗試使用[舊版本][older]。


## <a name="q11"></a>為何會有兩個 Application Insights 版本？

舊版入口網站是 Visual Studio Online 的一部分。我們不再對此版本進行大幅變更。如果您有舊版的 Application Insights Tools for Visual Studio，它們會連接至 Visual Studio Online 入口網站。

Visual Studio Update 3 附隨預先安裝的新版 Application Insights Tools。它們會連接至新的 Application Insights 入口網站 (Microsoft Azure 預覽的一個元件)。我們正在將 Application Insights 移植到這個新環境。工作尚未完成。

## <a name="q13"></a>我如何取回原本在 Visual Studio Online 版本的 Application Insights 中的所有功能？

1. 進入 Visual Studio 的擴充管理員。 
2. 解除安裝 Application Insights Tools。
3. 執行[舊版工具的安裝程式](http://visualstudiogallery.msdn.microsoft.com/82367b81-3f97-4de1-bbf1-eaf52ddc635a)，並閱讀其[入門指南][older]。

## <a name="q14"></a>Application Insights 在我的專案中修改什麼？

詳細資料視專案類型而定。Web 應用程式：+


+ 在您的專案中加入這些檔案：

 + ApplicationInsights.config。 
 + ai.js


+ 安裝這些 NuGet 套件：

 -  *Application Insights API* - 核心 API

 -  *Application Insights API for Web Applications* - 用來從伺服器傳送遙測

 -  *Application Insights API for JavaScript Applications* - 用來從用戶端傳送遙測

    套件包含這些組件：

 - Microsoft.ApplicationInsights

 - Microsoft.ApplicationInsights.Platform

+ 將項目插入至：

 - Web.config

 - packages.config

+ (僅限新專案 - 如果您[將 Application Insights 加入至現有的專案][start]，則必須手動執行。)將片段插入至用戶端和伺服器程式碼中，以使用 Application Insights 資源識別碼將它們初始化。例如，在 MVC 應用程式中，程式碼會插入至主版頁面 Views/Shared/_Layout.cshtml


## <a name="q15"></a>我如何在 Application Insights 中尋找我的結果？
1. 開啟 Microsoft Azure：
 - 在 Visual Studio 中，以滑鼠右鍵按一下 Web 應用程式專案，然後選擇 [**Open Azure Preview Portal**]。
 - 或者，在網頁瀏覽器中，您可以在 Microsoft Azure 預覽中開啟您的帳戶。

2. 選擇 [瀏覽]、[Application Insights]，然後選取您的專案。

## <a name="q16"></a>我的伺服器或開發用途電腦與公用網際網路之間有防火牆。若要啟用 Application Insights，我應該允許哪些流量？

效能和使用情況資料會傳送至 dc.services.visualstudio.com 和 f5.services.visualstudio.com 的 TCP 連接埠 80 和 443。

網頁可用性測試取決於 Web 伺服器連接埠 80 上的連入存取權。

## <a name="q17"></a> 我已啟用在 Application Insights 中的所有項目嗎？

<table border="1">
<tr><th>顯示的項目</th><th>取得方式</th><th>用途</th></tr>
<tr><td>可用性圖表</td><td><a href="../app-insights-monitor-web-app-availability/">Web 測試</a></td><td>確認 Web 應用程式已啟動</td></tr>
<tr><td>伺服器應用程式效能：回應時間、...
</td><td><a href="../app-insights-start-monitoring-app-health-usage/">將 Application Insights 新增至專案中</a><br/>或<br/><a href="../app-insights-monitor-performance-live-website-now/">在伺服器上安裝 AI 狀態監視器</a></td><td>偵測效能問題</td></tr>
<!-- ####future#### <tr><td>相依性遙測</td><td><a href="../app-insights-monitor-performance-live-website-now/">在伺服器上安裝 AI 狀態監視器</a></td><td>診斷資料庫或其他外部元件的問題</td></tr> -->
<!-- #####74.1#### <tr><td>伺服器全域變數：CPU、記憶體、...</td><td><a href="../app-insights-monitor-performance-live-website-now/">在伺服器上安裝 AI 狀態監視器</a></td><td>診斷容量問題</td></tr> --> 
<tr><td>搜尋記錄追蹤</td><td><a href="../app-insights-search-diagnostic-logs/">加入記錄配接器</a></td><td>診斷例外狀況、效能問題</td></tr>
<tr><td>用戶端使用基本概念：頁面檢視、傳回值、...</td><td><a href="../app-insights-start-monitoring-app-health-usage/#webclient">網頁中的 JavaScript 初始設定式</a></td><td>流量分析</td></tr>
<tr><td>用戶端自訂度量</td><td><a href="../app-insights-web-track-usage-custom-events-metrics/">在網頁中追蹤呼叫</a></td><td>增強使用者體驗</td></tr>
<tr><td>伺服器自訂度量</td><td><a href="../app-insights-web-track-usage-custom-events-metrics/">在伺服器程式碼中追蹤呼叫</a></td><td>商務智慧</td></tr>
</table>

如果您的 Web 服務在 Azure VM 中執行，您也可以從那裡[取得診斷][azurediagnostic]。



[AZURE.INCLUDE [app-insights-learn-more](../includes/app-insights-learn-more.md)]




[azurediagnostic]: ../insights-how-to-use-diagnostics/

[older]: http://www.visualstudio.com/get-started/get-usage-data-vs

