<properties
	pageTitle="疑難排解：Web 應用程式的效能降低"
	description="本文可協助您疑難排解 Azure App Service 所託管之 Web 應用程式的效能問題。"
	services="app-service\web"
	documentationCenter=""
	authors="cephalin"
	manager="wpickett"
	editor=""
	tags="top-support-issue"/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/23/2015"
	ms.author="cephalin"/>

# 疑難排解：Web 應用程式的效能降低

本文可協助您疑難排解 [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) 所託管之 Web 應用程式的效能問題。

若您對本文中的任何步驟有需要進一步協助的地方，請連絡 [MSDN Azure 和堆疊溢位論壇](http://azure.microsoft.com/support/forums/)上的 Azure 專家。或者，您也可以提出 Azure 支援事件。請移至 [Azure 支援網站](http://azure.microsoft.com/support/options/)，然後按一下 [取得支援]。

## 徵狀

當您瀏覽 Web 應用程式時，頁面載入緩慢且有時候會逾時。

## 原因

此問題通常是因為應用程式層級問題所造成，例如：

-	要求耗費過長的時間
-	應用程式的記憶體/CPU 使用率過高
-	應用程式因例外狀況導致損毀

## 疑難排解步驟

疑難排解可以分成三種不同的工作，依序為：

1.	[觀察和監視應用程式行為](#observe)
2.	[收集資料](#collect)
3.	[減輕問題](#mitigate)

[App Service Web Apps](/services/app-service/web/) 在每個步驟均提供您各種選項。

<a name="observe" />
### 1\.觀察和監視應用程式行為

#### 追蹤服務的健康狀態

每次發生服務中斷或效能降低時，Microsoft Azure 就會發出公告。您可以在 [Azure Preview 入口網站](https://portal.azure.com/)上追蹤服務健康狀態 。如需詳細資訊，請參閱[追蹤服務健康狀態](insights-service-health.md)。

#### 監視 Web 應用程式

此選項可讓您了解應用程式是否有任何問題。在 Web 應用程式刀鋒視窗中，按一下 [**要求和錯誤**] 磚。[**度量**] 刀鋒視窗將顯示所有可以加入的計量。

某些您可能想用以監視 Web 應用程式的計量為

-	平均記憶體工作集
-	平均回應時間
-	CPU 時間
-	記憶體工作集
-	要求

![](./media/app-service-web-troubleshoot-performance-degradation/1-monitor-metrics.png)

如需詳細資訊，請參閱：

-	[監視 Azure App Service 中的 Web Apps](web-sites-monitor.md)
-	[接收警示通知](insights-receive-alert-notifications.md)

#### 監視 Web 端點狀態

若您在**標準**定價層中執行 Web 應用程式，Web Apps 可讓您從 3 個地理位置監視 2 個端點。

端點監視能讓您設定從不同地理位置執行，且用來測試 Web URL 之回應時間和運作時間的 Web 測試。這項測試會針對 Web URL 執行 HTTP GET 作業，以從每個位置判斷回應時間和執行時間。各個已設定的位置會每隔五分鐘執行一次測試。

運作時間是透過 HTTP 回應碼來加以監視，而回應時間的測量單位是毫秒。如果 HTTP 回應碼大於或等於 400，或是當回應時間超過 30 秒時，監視測試即告失敗。如果所有指定位置上的端點監視測試全都成功，表示該端點可用。

若要設定，請參閱[如何監視 Web 端點狀態](web-sites-monitor.md#webendpointstatus)。

同時，亦請參閱[讓 Azure 網站保持運作以及端點監視 - 對談來賓 Stefan Schackow](/documentation/videos/azure-web-sites-endpoint-monitoring-and-staying-up/)的端點監視影片。

#### 使用擴充功能的應用程式效能監視

您也可以利用_網站擴充功能_監視應用程式的效能。

每個 App Service Web 應用程式都提供可擴充的管理端點，讓您得以運用一套以網站擴充功能形式部署的強大工具。這些工具從原始程式碼編輯器 (例如 [Visual Studio Online](https://www.visualstudio.com/products/what-is-visual-studio-online-vs.aspx))，到已連接資源的管理工具 (例如連接至 Web 應用程式的 MySQL 資料庫)，應有盡有。

[Azure Application Insights](/services/application-insights/) 和 [New Relic](/marketplace/partners/newrelic/newrelic/) 是兩個可用的效能監視網站擴充功能。若要使用 New Relic，您需要在執行階段安裝代理程式。若要使用 Azure Application Insights，請透過 SDK 重建程式碼，您也可以安裝可存取其他資料的擴充功能。SDK 可讓您撰寫程式碼來監視應用程式的詳細使用狀況和效能。

若要使用 Application Insights，請參閱[監視 Web 應用程式的效能](app-insights-web-monitor-performance.md)。

若要使用 New Relic，請參閱 [Azure 上的 New Relic 應用程式效能管理](store-new-relic-cloud-services-dotnet-application-performance-management.md)。

<a name="collect" />
### 2\.收集資料

####	為 Web 應用程式啟用診斷記錄

Web Apps 環境會針對來自 Web 伺服器和 Web 應用程式的記錄資訊提供診斷功能。這些資訊邏輯上可區分為 [Web 伺服器診斷]與 [應用程式診斷]。

##### Web 伺服器診斷

您可以啟用或停用下列各種記錄：

-	**詳細的錯誤記錄** - 對於表示失敗的 HTTP 狀態碼 (狀態碼 400 或更大) 的詳細錯誤資訊。這當中包含的資訊可協助您判斷為何伺服器傳回錯誤碼。
-	**失敗的要求追蹤** - 關於失敗要求的詳細資訊，包括用於處理要求的 IIS 元件追蹤，以及每個元件所花費的時間。若您嘗試提升 Web 應用程式的效能，或是想要隔離造成特定 HTTP 錯誤的原因，這個方法將有所助益。
-	**Web 伺服器記錄** - 使用 W3C 擴充記錄檔格式的 HTTP 交易相關資訊。當您需要判斷整體 Web 應用程式計量 (例如，所處理的要求數量，或來自特定 IP 位址要求的數量) 時，這個方法將有所助益。

##### 應用程式診斷

應用程式診斷功能可讓您擷取 Web 應用程式所產生的資訊。ASP.NET 應用程式會使用 `System.Diagnostics.Trace` 類別將資訊記錄到應用程式診斷記錄。

如需如何設定應用程式記錄功能的詳細指示，請參閱[在 Azure App Service 中針對 Web 應用程式啟用診斷記錄功能](web-sites-enable-diagnostic-log.md)。

#### 使用遠端分析

您可在 Azure App Service、Web Apps、API Apps 和 WebJobs 進行遠端分析。若您的處理序執行速度比預期緩慢，或 HTTP 要求的延遲情形高於一般且處理序的 CPU 使用量偏高，您可以從遠端分析處理序，取得 CPU 取樣呼叫堆疊，來分析處理序活動和程式碼忙碌路徑。

如需詳細資訊，請參閱 [Azure App Service 中的遠端分析支援](/blog/remote-profiling-support-in-azure-app-service)。


#### 使用 Azure App Service 支援入口網站

Web Apps 透過查看 HTTP 記錄檔、事件記錄檔、處理序傾印等，提供您疑難排解 Web 應用程式相關問題的能力。您可以利用我們位於 **http://&lt;your app name>.scm.azurewebsites.net/Support** 的支援入口網站，存取所有這方面的資訊。

Azure App Service 支援入口網站提供三個不同的索引標籤，支援常見疑難排解案例的三個步驟：

1.	觀察目前的行為
2.	藉由收集診斷資訊與執行內建分析器進行分析
3.	減輕問題

若問題現在正好發生，請按一下 [**分析**] > [**診斷**] > [**立即診斷**]，將為您建立診斷工作階段，該工作階段會收集 HTTP 記錄檔、事件檢視器記錄檔、記憶體傾印、PHP 錯誤記錄檔和 PHP 處理序報告。

完成資料收集後，將對資料執行分析，產生一份 HTML 報告。

若您想下載資料，根據預設，資料會儲存在 D:\\home\\data\\DaaS 資料夾中。

如需有關 Azure App Service 支援入口網站的詳細資訊，請參閱[支援 Azure 網站的網站擴充功能最新更新](/blog/new-updates-to-support-site-extension-for-azure-websites)。

#### 使用 Kudu 偵錯主控台

Web Apps 隨附可用於偵錯、探索、上傳檔案的偵錯主控台，以及可以取得您環境相關資訊的 JSON 端點。這稱為Web 應用程式的 _Kudu 主控台_ 或 _SCM 儀表板_。

您可以前往連結 **https://&lt;Yourapp name>.scm.azurewebsites.net/** 存取此儀表板。

Kudu 提供的部分項目為：

-	應用程式的環境設定
-	記錄檔串流
-	診斷傾印
-	您可以執行 Powershell Cmdlet 和基本 DOS 命令的偵錯主控台。


Kudu 的另一項實用功能是，如果應用程式擲回第一次例外狀況，您可以使用 Kudu 和 SysInternals 工具 Procdump 建立記憶體傾印。這些記憶體傾印是處理序的快照集，通常可以協助您疑難排解 Web 應用程式較複雜的問題。

如需有關 Kudu 可用功能的詳細資訊，請參閱[您應該知道的 Azure 網站線上工具](/blog/windows-azure-websites-online-tools-you-should-know-about/)。

<a name="mitigate" />
### 3\.減輕問題

####	調整 Web 應用程式大小

在 Azure App Service 中，為提高效能和輸送量，您可以調整所執行之應用程式的大小。相應增加 Web 應用程式規模牽涉到兩個相關動作：將 App Service 方案變更為較高的定價層，以及在改為較高的定價層後進行某些設定。

如需有關調整的詳細資訊，請參閱[在 Azure App Service 中調整 Web 應用程式規模](web-sites-scale.md)。

此外，您可以選擇在多個執行個體上執行應用程式。這不僅提供您更強大的處理能力，同時也提供您一定程度的容錯量。若處理序在某個執行個體上中斷，其他執行個體仍將繼續處理要求。

您可以將調整設定為手動或自動。

####	使用 AutoHeal

AutoHeal 會根據您選擇的設定 (例如組態變更、要求、以記憶體為基礎的限制或執行要求所需的時間)，回收應用程式的背景工作角色處理序。在大部分情況下，回收處理序是從問題中復原的最快方式。雖然您永遠可以從 Azure Preview 入口網站中直接重新啟動 Web 應用程式，AutoHeal 會自動為您完成。您只需要在 Web 應用程式的根目錄 web.config 中加入某些觸發程序。請注意，即使您的應用程式並非 .Net，這些設定的運作方式仍相同。

如需詳細資訊，請參閱[自動修復 Azure 網站](/blog/auto-healing-windows-azure-web-sites/)。

####	重新啟動 Web 應用程式

若要從一次性問題中復原，這通常是最簡單的方式。在 [Azure Preview 入口網站](https://portal.azure.com)上的 Web 應用程式刀鋒視窗中有提供停止或重新啟動應用程式的選項。

 ![](./media/app-service-web-troubleshoot-performance-degradation/2-restart.png)

您也可以使用 Azure Powershell 管理 Web 應用程式。如需詳細資訊，請參閱[將 Azure PowerShell 與 Azure 資源管理員搭配使用](powershell-azure-resource-manager.md)。

<!---HONumber=Nov15_HO1-->