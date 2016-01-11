<properties 
	pageTitle="Application Insights 中的資料保留和儲存" 
	description="保留和隱私權原則聲明" 
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
	ms.date="12/17/2015" 
	ms.author="awills"/>

# Application Insights 中的資料收集、保留和儲存 

*Application Insights 目前僅供預覽。*

當您在應用程式中安裝 [Visual Studio Application Insights][start] SDK 時，它會將關於應用程式的遙測傳送至雲端。當然，負責任的開發人員會想要確切得知所傳送的資料為何、資料的後續情況，以及如何控制資料。特別是，是否可能傳送敏感資料？資料儲存於何處？其安全性為何？


首先提供簡短的答案：

* 「現成可用」的標準遙測模組不太可能將敏感資料傳送至服務。遙測會考量負載、效能和使用度量、例外狀況報告和其他診斷資料。診斷報告中顯示的主要使用者資料的 URL；但是，您的應用程式在任何情況下都應該不會將敏感資料以純文字形式放在 URL 中。
* 您可以撰寫會傳送其他自訂遙測的程式碼，以利診斷與監視使用情形。(此擴充性是 Application Insights 的絕佳功能之一)。 在撰寫使程式碼時有可能會不慎包含個人資料和其他敏感資料。如果您的應用程式會使用這類資料，您應對您撰寫的程式碼採用嚴密的檢閱程序。
* 在開發及測試您的應用程式時，可以輕易地檢查由 SDK 傳送的內容。資料會出現在 IDE 和瀏覽器的偵錯輸出視窗中。 
* 資料會保存在美國的 [Microsoft Azure](http://azure.com) 伺服器中。Azure 有[嚴密的安全性程序，並符合各種法規標準](https://azure.microsoft.com/support/trust-center/)。只有您和您指定的小組可以存取您的資料。Microsoft 工作人員只有在您知情的特定有限情況下，才具有其限定存取權。它在傳輸時會加密，但在伺服器中不會加密。

本文的其餘部分將詳細說明上述問題的答案。本文設計為自助式，以便您可以將其顯示給不屬於您直屬小組的同事。


## 什麼是 Application Insights？

[Visual Studio Application Insights][start] 是 Microsoft 所提供的一項服務，可協助您改進即時應用程式的效能和可用性。它會在您的應用程式執行時全程加以監視，包括測試期間，以及您加以發佈或部署之後。Application Insights 會建立圖表和資料表為您顯示多種資訊，例如，您在一天中的哪些時間有最多使用者、應用程式的回應性如何，以及它所依存的任何外部服務是否順暢地為其提供服務。如果有當機、失敗或效能問題，您可以搜尋詳細的遙測資料，以診斷原因。此外，如果應用程式的可用性和效能有任何變更，服務將會傳送電子郵件給您。

若要取得這項功能，您必須在應用程式中安裝 Application Insights SDK，這會成為其程式碼的一部分。當您的應用程式執行時，SDK 會監視其作業，並將遙測傳送至 Application Insights 服務。這是由 [Microsoft Azure](http://azure.com) 裝載的雲端服務。(但 Application Insights 適用於任何應用程式，而不只是 Azure 中裝載的應用程式)。

![應用程式中的 SDK 會將遙測傳送至 Application Insights 服務。](./media/app-insights-data-retention-privacy/01-scheme.png)

Application Insights 服務會儲存並分析遙測。若要查看分析或搜尋已儲存的遙測，您可以登入您的 Azure 帳戶，並開啟您的 Application Insights 資源。您也可以與小組的其他成員或指定的 Azure 訂閱者共用資料的存取權。

您可以從 Application Insights 服務匯出資料，例如，匯出至資料庫或外部工具。您必須為每項工具提供您從服務取得的特殊金鑰。如有必要，可以撤銷此金鑰。

Application Insights SDK 可用於多種應用程式類型：裝載於您自己的 J2EE 或 ASP.NET 伺服器或是 Azure 中的 Web 服務；Web 用戶端 - 也就是在網頁上執行的程式碼；桌面應用程式和服務；裝置應用程式，例如 Windows Phone、iOS 和 Android。它們都會將遙測傳送至相同的服務。

## 它會收集哪些資料？

### 收集的資料的方式為何？

來源資料共有三種：

* SDK，可與您的應用程式在[開發](app-insights-asp-net.md)或[在執行階段](app-insights-monitor-performance-live-website-now.md)中整合。不同的應用程式類型有不同的 SDK。此外還有[網頁 SDK](app-insights-javascript.md)，會連同頁面載入至使用者的瀏覽器中。

 * 每個 SDK 各有多種[模組](app-insights-configuration-with-applicationinsights-config.md)，可使用不同的技術來收集不同類型的遙測。
 * 如果您在開發環境中安裝 SDK，則您可以使用其 API，在標準模組以外傳送您自己的遙測。此自訂遙測可包含您想要傳送的任何資料。
* 在某些網頁伺服器中，也有與應用程式一起執行，並傳送關於 CPU、記憶體和網路佔用量之遙測的代理程式。例如，Azure VM、Docker 主機和 [J2EE 伺服器](app-insights-java-agent.md)都可能有這類代理程式。
* [可用性測試](app-insights-monitor-web-app-availability.md) 是 Microsoft 所執行程序，會定期將要求傳送至您的 Web 應用程式。結果會傳送至 Application Insights 服務。

### 會收集哪些類型的資料？

主要類別如下：

* [Web 伺服器遙測](app-insights-asp-net.md) - HTTP 要求。URI、處理要求所花費的時間、回應碼、用戶端 IP 位址。工作階段識別碼。
* [網頁](articles/app-insights-javascript.md) - 頁面、使用者和工作階段計數。頁面載入時間。例外狀況。
* 效能計數器 - 記憶體、CPU、IO、網路佔用量。
* 用戶端和伺服器內容 - OS、地區設定、裝置類型、瀏覽器和螢幕解析度。
* [例外狀況](app-insights-asp-net-exceptions.md)和當機 - **堆疊傾印**、組建識別碼、CPU 類型。 
* [相依性](app-insights-asp-net-dependencies.md) - 對外部服務的呼叫，例如 REST、SQL、AJAX。URI 或連接字串、持續時間、成功、命令。
* [可用性測試](app-insights-monitor-web-app-availability.md) - 測試的持續時間、步驟、回應。
* [追蹤記錄檔](app-insights-search-diagnostic-logs.md)和[自訂遙測](app-insights-api-custom-events-metrics.md) - **任何以程式碼撰寫到記錄檔或遙測中的項目**。

[詳細資訊](#data-sent-by-application-insights)。

## 如何確認收集到什麼？

如果您使用 Visual Studio 開發應用程式，請在偵錯模式 (F5) 中執行應用程式。遙測會出現在 [輸出] 視窗中。在這裡，您可以將其複製並格式化為 JSON，以便進行檢查。

![](./media/app-insights-data-retention-privacy/06-vs.png)

針對網頁，請開啟瀏覽器的偵錯視窗。

![按 F12 鍵，然後開啟 [網路] 索引標籤。](./media/app-insights-data-retention-privacy/08-browser.png)

### 是否可以撰寫程式碼來篩選遙測，然後才傳送出去?

這可以藉由撰寫[遙測處理器外掛程式](app-insights-api-filtering-sampling.md)來達成。



## 資料保留多久？ 

這取決於您的[定價方案](http://azure.microsoft.com/pricing/details/application-insights/)。

未處理的資料點 (即您可以在 [診斷搜尋] 中檢查的項目)：7 至 30 天。

彙總的資料 (即您在計量瀏覽器中看到的計數、平均和其他統計資料) 在 1 分鐘的資料粒度中保存 30 天，而 1 小時或 1 天 (視類型而定) 則保存至少 13 個月。


## 誰可以存取資料？

您和您的小組成員 (如果您有組織帳戶) 可以看到資料。

它可以由您和您的小組成員匯出，也可以複製到其他位置，並傳遞給其他人員。

#### Microsoft 如何處理我的應用程式傳送至 Application Insights 的資訊？

Microsoft 只會使用這項資料，以將服務提供給您。


## 資料存放在哪裡？ 

* 在美國。 

#### 可以儲存在其他某個地方 (例如歐洲) 嗎？ 

* 尚未提供。 

## 我的資料有多安全？  

Application Insights 是目前僅供預覽的 Azure 服務。僅供預覽時，我們致力於根據 [Azure 安全性、隱私權和法務遵循白皮書](http://go.microsoft.com/fwlink/?linkid=392408)中所述的原則來保護資料。


資料會儲存在 Microsoft Azure 伺服器中。如果是 Azure 入口網站中的帳戶，帳戶限制如 [Azure 安全性、隱私權和法規遵循文件](http://go.microsoft.com/fwlink/?linkid=392408)中所述。如果是 Visual Studio Team Services 入口網站中的帳戶，則適用 [Visual Studio Team Services 資料保護](http://download.microsoft.com/download/8/E/E/8EE6A61C-44C2-4F81-B870-A267F1DF978C/MicrosoftVisualStudioOnlineDataProtection.pdf)文件。

Microsoft 人員對您的資料存取會受到限制。我們只有在獲得您的許可及為了支援您使用 Application Insights 而有必要時，才會存取您的資料。

跨所有客戶應用程式 (例如資料速率和平均追蹤大小) 的彙總資料用於改善 Application Insights。

#### 其他人的遙測是否會干擾我的 Application Insights 資料？

他們可以使用檢測金鑰，將額外的遙測傳送至您的帳戶，而檢測金鑰位於您網頁的程式碼中。有足夠的額外資料，您的計量將不會正確地代表您應用程式的效能和使用方式。

如果您與其他專案共用程式碼，請務必移除您的檢測金鑰。

## 資料是否會加密？ 

目前不在伺服器內。

所有資料在資料中心之間移動時會予以加密。

#### 將資料從我的應用程式傳輸到 Application Insights 伺服器時是否進行加密？

是，我們使用 https，將資料從幾乎所有 SDK (包括網頁伺服器、裝置和 HTTPS 網頁) 傳送至入口網站。唯一的例外是從純 HTTP 網頁傳送的資料。

## 個人識別資訊

#### 個人識別資訊 (PII) 會傳送到 Application Insights 嗎？ 

是，可以的。

一般指引為：

* 大部分的標準遙測 (即，在未撰寫任何程式碼的情況下所傳送的遙測) 都不包括明確的 PII。不過，從事件集合推斷，即可識別個人。
* 例外狀況和追蹤訊息可能包含 PII
* 自訂遙測 (即，使用 API 以程式碼撰寫的呼叫 (例如 TrackEvent) 或記錄檔追蹤) 可以包含您選擇的任何資料。


這份文件結尾的資料表包含所收集資料的更詳細描述。



#### 我是否要負責遵守有關 PII 的法規？

是。您必須負責確保資料的收集與使用符合法規和 Microsoft Online Services 條款。

您應該適當地通知您的客戶有關您應用程式所收集的資料和資料使用方式。

#### 我的使用者是否可以關閉 Application Insights？

無法直接進行。我們不提供您的使用者可以操作來關閉 Application Insights 的參數。

不過，您可以在應用程式中實作這類功能。所有 SDK 都包括關閉遙測收集的 API 設定。

#### 我的應用程式不小心收集到機密資訊。Application Insights 是否可以刪除此資料，不予保留？

Application Insights 不會篩選或刪除資料。您應該適當地管理資料，並避免將這類資料傳送至 Application Insights。



## Application Insights 所傳送的資料

不同的平台會有不同的 SDK，而且有數個可安裝的元件 (請參閱 [Application Insights - 開始使用][start])。 每個元件都會傳送不同的資料。

#### 不同情況下所傳送資料的類別

您的動作 | 收集的資料類別 (請參閱下一個資料表)
---|---
[將 Application Insights SDK 新增至 .NET Web 專案][greenbrown] | ServerContext<br/>推斷<br/>效能計數器<br/>要求<br/>**例外狀況**<br/>工作階段<br/>使用者
[在 IIS 上安裝狀態監視器][redfield]<br/>[將 AI 延伸模組新增至 Azure VM 或 Web 應用程式][azure]|相依性<br/>ServerContext<br/>推斷<br/>效能計數器
[將 Application Insights SDK 新增至 Java Web 應用程式][java]|ServerContext<br/>推斷<br/>要求<br/>工作階段<br/>使用者
[將 JavaScript SDK 新增至網頁][client]|ClientContext <br/>推斷<br/>頁面<br/>ClientPerf
[將 SDK 新增至 Windows 市集應用程式][windows]|DeviceContext<br/>使用者<br/>損毀資料
[定義預設屬性][apiproperties]|所有標準和自訂事件上的**屬性**
[呼叫 TrackMetric][api]|數值<br/>**屬性**
[呼叫追蹤*][api]|事件名稱<br/>**屬性**
[呼叫 TrackException][api]|**例外狀況**<br/>堆疊傾印<br/>**屬性**
SDK 無法收集資料。例如：<br/> - 無法存取效能計數器<br/> - 遙測初始設定式中的例外狀況 | SDK 診斷
 

如需[適用於其他平台的 SDK][platforms]，請參閱其文件。



#### 所收集資料的類別

收集的資料類別 | 包含 (不是詳盡的清單) 
---|---
**屬性**|**任何資料 - 取決於您的程式碼**
DeviceContext |識別碼、IP、地區設定、裝置型號、網路、網路類型、OEM 名稱、螢幕解析度、角色執行個體、角色名稱、裝置類型
ClientContext |作業系統、地區設定、語言、網路、視窗解析度
工作階段 | 工作階段識別碼
ServerContext |電腦名稱、地區設定、作業系統、裝置、使用者工作階段、使用者內容、作業 
推斷 |從 IP 位址的地區位置、時間戳記、作業系統、瀏覽器
度量 | 計量名稱和值
事件 | 事件名稱和值
PageViews | URL 和頁面名稱或螢幕名稱
用戶端效能 | URL/頁面名稱、瀏覽器載入時間
要求 |URL、持續時間、回應碼
相依項目|類型 (SQL、HTTP、...)、連接字串或 URI、同步/非同步、持續時間、成功、SQL 陳述式 (含狀態監視器)
**例外狀況** | 類型、**訊息**、呼叫堆疊、來源檔案和行號、執行緒識別碼
損毀 | 處理序識別碼、父處理序識別碼、損毀執行緒識別碼；應用程式修補程式、識別碼、組建；例外狀況類型、位址、原因；模糊符號和暫存器、二進位開始和結束位址、二進位檔名稱和路徑、CPU 類型
追蹤 | **訊息**和嚴重性層級
效能計數器 | 處理器時間、可用記憶體、要求率、例外狀況率、處理序私用位元組、IO 率、要求持續期間、要求佇列長度
Availability | Web 測試回應程式碼、每個測試步驟的持續時間、測試名稱、時間戳記、成功、回應時間、測試位置
SDK 診斷 | 追蹤訊息或例外狀況 

您可以[編輯 ApplicationInsights.config 來關閉某些資料][config]


## 感謝

此產品包含由 MaxMind 建立的 GeoLite2 資料，可從 [http://www.maxmind.com](http://www.maxmind.com) 取得。

## <a name="video"></a>影片

#### 簡介

> [AZURE.VIDEO application-insights-introduction]

#### 開始使用

> [AZURE.VIDEO getting-started-with-application-insights]




<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[azure]: ../insights-perf-analytics.md
[client]: app-insights-javascript.md
[config]: app-insights-configuration-with-applicationinsights-config.md
[greenbrown]: app-insights-asp-net.md
[java]: app-insights-java-get-started.md
[platforms]: app-insights-platforms.md
[pricing]: http://azure.microsoft.com/pricing/details/application-insights/
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-overview.md
[windows]: app-insights-windows-get-started.md

 

<!---HONumber=AcomDC_1223_2015-->