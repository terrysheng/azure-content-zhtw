<properties 
	pageTitle="沒有要進行疑難排解的資料 - Application Insights for .NET" 
	description="在 Visual Studio Application Insights 中沒有看到資料？ 試試這裡。" 
	services="application-insights" 
    documentationCenter=".net"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/29/2016" 
	ms.author="awills"/>
 
# 沒有要進行疑難排解的資料 - Application Insights for .NET


## 狀態監視器問題

*我[安裝狀態監視器](app-insights-monitor-performance-live-website-now.md)在我的 Web 伺服器上以監視現有的應用程式。我沒有看到任何結果。*

請參閱[疑難排解狀態監視器](app-insights-monitor-performance-live-website-now.md#troubleshooting)。防火牆連接埠是最常見的問題。


## <a name="q01"></a>在 Visual Studio 中沒有「新增 Application Insights」選項

*當我在 Visual Studio 中建立新的專案，或當我以滑鼠右鍵按一下 [方案總管] 中的現有專案時，我沒有看到任何 Application Insights 選項。*

+ 工具並非支援所有類型的 .NET 專案。支援 Web 和 WCF 專案。對於例如桌面或服務應用程式的專案，您仍然可以[手動將 Application Insights SDK 新增至您的專案](app-insights-windows-desktop.md)。
+ 請確定您有 [Visual Studio 2013 Update 3 或更新版本](http://go.microsoft.com/fwlink/?LinkId=397827)。這會隨 Application Insights 工具預先安裝。
+ 選取 [工具]、[擴充功能和更新]，並檢查 [Application Insights Tools] 是否已安裝並啟用。如果是的話，按一下 [更新] 以查看是否有可用的更新。
+ 開啟 [新增專案] 對話方塊，並且選擇 [ASP.NET Web 應用程式]。如果您在那裡看到 Application Insights 選項，則工具已安裝。如果沒有，請嘗試解除安裝 Application Insights Tools，再重新安裝。


## <a name="q02"></a>新增 Application Insights 失敗

*當我建立一個新的 Web 專案，或當我嘗試將 Application Insights 新增至現有的專案時，我看到錯誤訊息。*

可能的原因：

* 與 Application Insights 入口網站通訊失敗；或者
* 您的 Azure 帳戶發生問題；
* 您只具備[您嘗試建立新資源之訂用帳戶或群組的讀取權限](app-insights-resources-roles-access-control.md)。

修正：

+ 請檢查您為正確的 Azure 帳戶提供登入認證。 
+ 在瀏覽器中，檢查您是否可以存取 [Azure 入口網站](https://portal.azure.com)。開啟 [設定] 並查看是否有任何限制。
+ [將 Application Insights 加入至您現有的專案](app-insights-asp-net.md)：在 [方案總管] 中以滑鼠右鍵按一下您的專案，並選擇 [加入 Application Insights]。
+ 如果仍然無法運作，請依照下列[手動程序](app-insights-start-monitoring-app-health-usage.md)，在入口網站中加入資源，然後將 SDK 加入至您的專案。 

## <a name="emptykey"></a>我收到「檢測金鑰不能是空白」的錯誤

可能是您在安裝 Application Insights 或記錄配接器時發生問題。

在 [方案總管] 中，以滑鼠右鍵按一下 `ApplicationInsights.config`，然後選擇 [設定 Application Insights]。將會出現對話方塊邀請您登入 Azure，並建立 Application Insights 資源或重複使用現有的資源。


##<a name="NuGetBuild"></a>我的組建伺服器上「NuGet 封裝遺失」

*我在我的開發機器上偵錯時所有組建都沒有問題，但是在組建伺服器上收到 NuGet 錯誤。*

請參閱 [NuGet 封裝還原](http://docs.nuget.org/Consume/Package-Restore) (英文) 和[自動封裝還原](http://docs.nuget.org/Consume/package-restore/migrating-to-automatic-package-restore) (英文)。

## 遺失從 Visual Studio 開啟 Application Insights 的功能表命令

*當我以滑鼠右鍵按一下專案 [方案總管] 時，我沒有看到任何 Application Insights 命令，或沒有看到開啟 Application Insights 命令。*

可能的原因：

* 如果您是以手動方式建立 Application Insights 資源，或專案是 Application Insights 工具不支援的類型。
* Application Insights 工具在您的 Visual Studio 中停用。
* 您的 Visual Studio 版本比 2013 Update 3 還舊。

修正：

* 請確定您的 Visual Studio 版本是 2013 Update 3 或更新版本。
* 選取 [工具]、[擴充功能和更新]，並檢查 [Application Insights Tools] 是否已安裝並啟用。如果是的話，按一下 [更新] 以查看是否有可用的更新。
* 以滑鼠右鍵按一下 [方案總管] 中的專案。如果您看到命令 **Configure Application Insights**，使用它來將專案連接至 Application Insights 服務中的資源。


否則，Application Insights 工具不會直接支援您的專案類型。若要查看您的遙測，請登入 [Azure 入口網站](https://portal.azure.com)，在左側的導覽列中，選擇 [Application Insights]，然後選取您的應用程式。

## 從 Visual Studio 開啟 Application Insights 時「拒絕存取」

*[開啟 Application Insights] 功能表命令帶我前往 Azure 入口網站，但是我收到「拒絕存取」錯誤。*

![](./media/app-insights-asp-net-troubleshoot-no-data/access-denied.png)

您上一次在預設瀏覽器中使用的 Microsoft 登入沒有 [Application Insights 新增至這個應用程式時所建立的資源](app-insights-asp-net.md)的存取權。有兩個可能的原因：

* 您有一個以上的 Microsoft 帳戶 - 可能是工作和個人 Microsoft 帳戶？ 您上一次在預設瀏覽器上使用的登入，是與具有[將 Application Insights 新增至專案](app-insights-asp-net.md)的存取權的帳戶不同的帳戶。 

 * 修正：按一下瀏覽器視窗右上方的您的名稱並且登出。然後以具有存取權的帳戶登入。然後在左側的導覽列中，按一下 [Application Insights]，並選取您的應用程式。

* 其他人將 Application Insights 新增至專案，且他們忘記給予您所建立的[資源群組的存取權](app-insights-resources-roles-access-control.md)。

 * 修正：如果他們使用組織帳戶，他們可以將您加入小組；或者，他們可以授與您資源群組的個別存取權。



## 從 Visual Studio 開啟 Application Insights 時「找不到資產」

*[開啟 Application Insights] 功能表命令帶我前往 Azure 入口網站，但是我收到「找不到資產」錯誤。*

可能的原因：

* 您的應用程式的 Application Insights 資源已刪除；或
* 檢測金鑰已在 ApplicationInsights.config 中直接編輯以設定或變更，而不需更新專案檔案。 

ApplicationInsights.config 中的檢測金鑰會控制遙測傳送的位置。專案檔案中的資料行控制當您在 Visual Studio 中使用命令時會開啟哪一個資源。

修正：

* 在 [方案總管] 中以滑鼠右鍵按一下專案，然後選擇 [Application Insights]、[設定 Application Insights]。在對話方塊中，您可以選擇將遙測傳送至現有的資源，或建立新的資源。或：
* 直接開啟資源。請登入 [Azure 入口網站](https://portal.azure.com)，在左側的導覽列中，按一下 [Application Insights]，然後選取您的應用程式。



## 哪裡可以找到我的遙測？

*我登入 [Microsoft Azure 入口網站](https://portal.azure.com)，我正在查看 Azure 主儀表板。那麼我可以在哪裡找到我的 Application Insights 資料？*

* 在左側的導覽列中，按一下 [Application Insights]，然後選取您的應用程式名稱。如果您在其中沒有任何專案，則需要[在您的 Web 專案中新增或設定 Application Insights](app-insights-asp-net.md)。

    您可以從中看到一些摘要圖表。您可以逐一點選以查看更詳細的資料。

* 在 Visual Studio 中偵錯應用程式時，按一下 [Application Insights] 按鈕。


## <a name="q03"></a>沒有任何伺服器資料 (或根本沒有資料)

*我執行我的應用程式，然後在 Microsoft Azure 中開啟 Application Insights 服務，但是所有圖表都顯示「了解如何收集...」或「未設定」。* 或者，*只有頁面檢視和使用者資料，沒有任何伺服器資料。*

+ 在 Visual Studio 中以偵錯模式執行您的應用程式 (F5)。使用您的應用程式以產生一些遙測。請檢查您可以在 Visual Studio 的 [輸出] 視窗中看到所記錄的事件。 

    ![](./media/app-insights-asp-net-troubleshoot-no-data/output-window.png)

+ 在 Application Insights 入口網站中，開啟[診斷搜尋](app-insights-diagnostic-search.md)。這裡通常會先顯示資料。
+ 按一下 [重新整理] 按鈕。刀鋒視窗會定期自行重新整理，但您也可以手動重新整理。時間範圍越大，重新整理的間隔就越長。
+ 檢查檢測金鑰相符。在 Application Insights 入口網站的應用程式的主要刀鋒視窗中，在 [基本功能] 下拉式清單中，查看 [檢測金鑰]。然後，在您的 Visual Studio 的專案中，開啟 ApplicationInsights.config，並尋找 `<instrumentationkey>`。請檢查兩個金鑰是否相等。如果不是：
 + 在入口網站中，按一下 [Application Insights]，並且尋找具有正確金鑰的應用程式資源；或
 + 在 Visual Studio [方案總管] 中，以滑鼠右鍵按一下專案，然後選擇 [Application Insights]、[設定]。重設應用程式，以將遙測傳送至正確的資源。
 + 如果找不到相符的金鑰，請確認您在 Visual Studio 中使用與入口網站相同的登入認證。

    ![](./media/app-insights-asp-net-troubleshoot-no-data/ikey-check.png)
    
+ 在 [Microsoft Azure 主儀表板中](https://portal.azure.com)，查看服務健全狀況對應。如果看到一些警示指示，請等待它們恢復 [正常]，然後關閉再重新開啟 Application Insights 應用程式刀鋒視窗。
+ 也請查閱[我們的狀態部落格](http://blogs.msdn.com/b/applicationinsights-status/)。
+ 您是否針對[伺服器端 SDK](app-insights-api-custom-events-metrics.md) 撰寫任何程式碼，可能會變更 `TelemetryClient` 執行個體或 `TelemetryContext` 中的檢測金鑰？ 或者您是否撰寫[篩選或取樣組態](app-insights-api-filtering-sampling.md)，可能會篩選過多？
+ 如果您編輯過 ApplicationInsights.config，請仔細檢查 [TelemetryInitializers 和 TelemetryProcessors](app-insights-api-filtering-sampling.md) 的設定。不當命名的類型或參數可能導致 SDK 不會傳送任何資料。

## <a name="q04"></a>頁面檢視、瀏覽器、使用量沒有任何資料

*我在「伺服器回應時間」和「伺服器要求」圖表中看到資料，但是 [頁面檢視載入] 時間，或 [瀏覽器] 或 [使用量] 刀鋒視窗中沒有任何資料。*

資料來自網頁中的指令碼。

+ 如果您將 Application Insights 加入至現有的 Web 專案，則[您必須手動加入指令碼](app-insights-javascript.md)。
+ 確定 Internet Explorer 並非以相容性模式顯示您的網站。
+ 若要確認資料確實傳送至 `dc.services.visualstudio.com`，請使用瀏覽器的偵錯功能 (部分瀏覽器是使用 F12 鍵，然後選擇 [網路])。

## 沒有相依性或例外狀況資料

請參閱[相依性遙測](app-insights-asp-net-dependencies.md)和[例外狀況遙測](app-insights-asp-net-exceptions.md)。

## 我發佈應用程式到我的伺服器之後卻沒有 (伺服器) 資料

+ 請確認您實際上已複製所有 Microsoft.ApplicationInsights DLL 到伺服器，並且連帶 Microsoft.Diagnostics.Instrumentation.Extensions.Intercept.dll ApplicationInsights Dll 一併複製。
+ 在防火牆中，您可能必須開啟 TCP 連接埠 80 和 443，以允許連出流量送往 dc.services.visualstudio.com 和 f5.services.visualstudio.com。
+ 如果您必須使用 Proxy 在貴公司網路之外傳送內容，請設定 Web.config 中的 [defaultProxy](https://msdn.microsoft.com/library/aa903360.aspx)
+ Windows Server 2008：確定您已安裝以下更新：[KB2468871](https://support.microsoft.com/kb/2468871)、[KB2533523](https://support.microsoft.com/kb/2533523)、[KB2600217](https://support.microsoft.com/kb/2600217)。


## 我曾經看到資料，但是已停止

* 檢查[狀態部落格](http://blogs.msdn.com/b/applicationinsights-status/)。
* 您有達到資料點的每月配額嗎？ 開啟 [設定/配額和定價] 即可查看。若有達到配額，您可以升級您的方案，或付費取得額外容量。請參閱[定價配置](https://azure.microsoft.com/pricing/details/application-insights/)。


## 我並沒有看到預期的所有資料

如果您的應用程式傳送大量資料，且您是使用 Application Insights SDK for ASP.NET 版本 2.0.0-beta3 或更新版本，則[調適性取樣](app-insights-sampling.md)功能可能會運作，並只傳送一部分的遙測資料。

您可以停用它，但是這不是建議的作法。取樣經過設計，以便能正確傳輸相關的遙測，供診斷之用。

## 使用者遙測中錯誤的地理資料

城市、區域和國家/地區維度衍生自 IP 位址，而且不一定準確。

<!---HONumber=AcomDC_0204_2016-->