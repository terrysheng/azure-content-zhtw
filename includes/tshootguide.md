
# Azure 中的疑難排解

*疑難排解*是指尋找、了解並更正未預期應用程式行為的一般工作。開發應用程式時，開發人員會先測試、執行及偵錯他們的應用程式，之後才會將應用程式部署到實際執行環境。無論應用程式會在桌上型電腦或雲端的伺服器上執行，這都是正確的程序。不過，四處分散且具有多重執行個體、專為向外延展所設計的應用程式可能會難以偵錯，因此需要有標準以上的其他工具和方法。

基於這個原因，設計雲端應用程式時應考量疑難排解。在應用程式中加上疑難排解支援的設計方式，首先會根據應用程式的執行位置，其次會根據建立或使用應用程式的語言或執行階段。

如果您打算建立在 Azure
虛擬機器上執行的應用程式，在許多情況下，您可以處理應用程式的疑難排解設計以及偵錯，就像是在自己伺服器的作業系統上執行一樣。

在 Azure
上執行的應用程式都是四處分散且具有多重執行個體的應用程式，因此可能難以偵錯。需要有標準以上的工具和方法才能疑難排解這類應用程式。本主題將討論部分已經實證的疑難排解作法，並包含有關這些作法的深入資訊連結。

**注意**：本主題假設您正在設計您的應用程式，或您已順利部署 Azure 應用程式，但出現意外狀況。本主題不會討論如何在 Azure
上部署應用程式。如需開發與部署 Azure 應用程式的詳細資訊，請參閱
[https://www.windowsazure.com/en-us/develop/overview/][1]。

本主題將首先說明可協助您設計應用程式的一些最佳作法，方便您在發生問題時有效地進行疑難排解。(如果您的應用程式設計未事先允許您依照程式碼流程，當發生問題時，有可能很難找到問題所在。)無論使用的應用程式模型或語言為何，這些最佳作法適用於所有在
Azure 上執行的應用程式類型。

下列各節將說明開發具支援性 Azure 應用程式的特定方法，不論應用程式的類型為何：網站、雲端服務或虛擬機器。

每個章節將說明高層級的最佳作法，並包含可詳細示範最佳作法或說明如何實作最佳作法的資源指標。

## 本文件內容

* [在 Azure 中疑難排解的最佳作法](#BestPractices)
* [Azure 網站](#Websites)
* [Azure 雲端服務](#CloudServices)
* [Azure 虛擬機器](#Vms)
* [Azure 服務](#PlatformServices)
* [SQL Database 疑難排解](#SQLTroubleshooting)

<h2><a id="BestPractices" ></a>在 Azure 中疑難排解的最佳作法</h2>


無論您所使用的裝載模型或語言為何，本節將說明適用於 Azure 應用程式的最佳作法。它包含可更深入討論這些作法的資源。

若要在 Azure 中建立有效疑難排解的基礎，請將您的精力集中在下列三個主要領域：

* 適當地處理錯誤 - *每個元件服務都必須能夠承受相依服務或基礎結構的錯誤*。
* 追蹤、記錄及監視 - *每個元件服務都必須具備正確的偵錯、追蹤、事件及錯誤記錄功能。*
* 可以偵測錯誤的位置 - *在升級到實際執行環境之前，以及在元件和網路層級執行時。*

在設計應用程式時謹記這些想法，該應用程式便可在發生未預期行為時提供您追蹤所需的資訊。

### 專為適當地處理錯誤所設計

如果可能的話，應用程式應該適當地處理錯誤狀況。基於 Azure
的分散式本質，這一點更是重要。有效的疑難排解會從良好的暫時性失敗處理方式設計開始。因為網際網路應用程式固有的暫時性錯誤狀況，而導致雲端應用程式無法依預期執行的主要區域之一是暫時性錯誤。

暫時性錯誤的失敗與網際網路上共用資源固有的延遲和間歇性網路連線有關。部分範例如下：

* 共用電腦資源 (以 Azure 雲端服務和 SQL Database 為例) 可能因不同的時間而稍微緩慢或迅速回應。
* 因為提供服務的持久性而導致回應能力延遲。例如，SQL Azure 會將多個資料庫副本保持一致來提供持久性，這會對回應能力產生影響。
* 因 HTTP 或其他通訊協定連線所造成的延遲會在完成工作之前將工作結束。例如，可能無法在逾時期限之前，將 HTTP 要求送達端點並傳回。

若要協助減輕暫時性錯誤所帶來的影響，Azure 應用程式應：

* 可鬆散偶合，因此本機上的元件不會仰賴比在內部部署環境中更容易失敗的服務。
* 儘可能進行非同步呼叫，以防止程序逐漸受到立即回應的影響。
* 使用暫時性錯誤處理方法來偵測特定類別的失敗，並根據某些可設定重試原則，實作這些失敗的重試行為。

呼叫服務應建立或使用暫時性錯誤處理層來偵測常見失敗案例，並根據組態設定來重試呼叫。針對 .NET 開發人員，我們推薦的程式庫為
[Microsoft Enterprise Library 5.0 Integration Pack for
Azure][2]。Microsoft Enterprise Library
是可重複使用的應用程式區塊集合，可對付企業軟體開發中的常見程式碼橫切入顧慮。Microsoft Enterprise Library
Integration Pack for Azure 是 Microsoft Enterprise Library 5.0 的擴充功能，可與 Azure 搭配使用。其中包括自動調整應用程式區塊、暫時性錯誤處理應用程式區塊、blob
組態來源、受保護的組態提供者和學習材料。另一個擁有較少功能的簡化 .NET 程式庫為[雲端應用程式架構與擴充功能
(CloudFx)][3]。CloudFx 提供一組生產品質元件和建置組塊，可快速開始實作功能豐富、可靠及可擴充的 Azure
架構解決方案和服務。

### 執行正確的追蹤和記錄

因為分散式、向外延展應用程式的複雜性，在調查應用程式執行期間所發生的問題時，比較不會用到根據一個程序工作的傳統偵錯程式。因此，追蹤和記錄會是最重要的項目。執行應用程式及其資料的功能會在裝載於許多不同機器的許多不同服務中共用。在大規模的分散式應用程式中，很難
(或無法) 判斷附加目標及偵錯的服務執行個體。追蹤和記錄可讓您追蹤應用程式執行和資料流程，方便您深入了解應用程式的狀態。

成功的 Azure 應用程式設計一開始便會包含記錄和追蹤策略。這會縮短尋找任何問題所需的時間，並在無需致電 Microsoft
尋求支援的情況下快速修復問題。

**注意**：廣泛寫入追蹤和記錄會對效能造成影響，密集地執行會造成更大的影響。因此，應用程式的設計應包括可設定的追蹤和記錄原則，此原則可視需要進行調整。理想的情況下，應可從
**ServiceConfiguration.cscfg** 檔案中調整記錄層級，方便您在無需重新部署的情況下進行變更。

擁有大量記錄並不保證能夠快速進行錯誤偵測和修復，大量的資料需要很長的時間進行解碼，且收集大量資料會影響應用程式的效能。可調整的記錄能夠控制記錄資料的大小和儲存成本。

在 MSDN 雜誌文章＜[控制 Azure 中的記錄和追蹤][4]＞中，Mike Kelly 說明要考量的四個診斷輸出類型：

* 偵錯輸出 - 只能在偵錯組建中使用 (包括判斷提示)
* 追蹤 - 追蹤執行期間的控制流程
* 事件記錄 - 程式執行中的主要事件
* 錯誤記錄 - 例外狀況或危險狀況

其他語言、應用程式平台及作業系統可能會有不同的追蹤和記錄術語。如果您打算在 Azure
上使用其中一個開發平台，請為使用的語言或平台使用同等的策略和工具。

在 Azure
虛擬機器、網站和雲端服務的組合中所執行的應用程式稱為混合模式應用程式。建立這個類型的應用程式時，由於他們四處分散的特性，追蹤和記錄變得更為重要。若要疑難排解這些混合模式應用程式，必須遵循整體資料和執行流程才能找出任何問題。追蹤和記錄混合模式應用程式的機制會視元件的裝載模型而定。

### 監視應用程式

追蹤和記錄會顯示在監視的較大區域。監視可讓您執行下列動作：

* 探索 Azure 應用程式
* 判斷每個角色執行個體的狀態
* 收集與監視包括延遲和輸送量的效能資訊
* 收集與監視事件
* 收集與監視追蹤訊息
* 監視資源使用量
* 監視服務度量的品質
* 執行容量規劃
* 執行流量分析 (使用者、檢視、尖峰時間)
* 估計計費
* 執行稽核

使用工具或工具組來完成監視。使用的工具取決於應用程式所使用的平台及/或語言，以及您的監視目標與需求。

**Microsoft System Center Azure 應用程式監視組件**

此[監視組件][5]可讓您使用 [Microsoft System Center Operations Manager][6] 來監視
Azure 應用程式的可用性與效能。

若要監視 Azure 應用程式的健康情況，最好的方式就是使用 Microsoft System Center Operations
Manager 2007。

**Azure 平台管理工具**

另一個工具是 [Azure 平台管理工具 (MMC)][7]，可讓您管理 Azure
代管服務和儲存體帳戶。此工具會以範例的方式與完整的原始程式碼一起提供，方便您查看如何使用 Azure 管理和診斷 API
來執行各種管理和組態工作。

**Cerebrata 工具**

Cerebrata 提供許多可讓您監視與管理 Azure 應用程式的工具。這些工具包括 [Azure 診斷管理員][8]、[Cloud Storage Studio][9] 和 [Azure 管理 Cmdlet][10]。

Azure 診斷管理員 2 是用來管理 Azure 診斷的 Windows (WPF) 架構用戶端。它可讓您檢視、下載及管理在 Azure
中執行之應用程式所收集的診斷資料。如需這些產品的詳細資訊，請參閱 [http://www.cerebrata.com][11]。

Cloud Storage Studio 2 是用來管理 Azure 儲存體的 Windows (WPF) 架構用戶端。

Azure 管理 Cmdlet 是用來管理 Azure 儲存體、代管服務、SQL Database 執行個體和診斷的一組 Windows PowerShell Cmdlet。它也提供備份與還原儲存體帳戶的 Cmdlet。

**網路監視：AlertBot、Gomez、Keynote、Pingdom**

Compuware 的 [Gomez][12] 應用程式效能管理、[Keynote][13]、[Pingdom][14] 和
[AlertBot][15] 是遠端監視 Azure 應用程式的解決方案。他們可讓您監視應用程式的可用性並最佳化效能。部分服務 (例如
Pingdom) 可在偵測到錯誤時，透過電子郵件、SMS 或桌面應用程式啟用通知。這個監視類型可模擬若要順利監視應用程式的使用者執行動作。

**Apica 工具**

Apica 提供可「從外部」監視 Azure Web 應用程式的工具。如需詳細資訊，請參閱
[http://www.apicasystem.com/integration-partners/][16]。

**AVIcode**

Microsoft 購買了 AVIcode，AVIcode 現在是 Microsoft System Center
的一部分。[AVIcode][17] 提供 .NET 應用程式效能監視功能，以及一套完整的應用程式監視功能。

**效能程式碼剖析**

當在 Azure 中執行 Azure 應用程式時，您可以對 Azure 應用程式進行[程式碼剖析][18]，以找出任何效能問題。當您從
Visual Studio 發行 Azure 應用程式時，您可以選擇對應用程式進行程式碼剖析，並選取所需的程式碼剖析設定。

**Azure VM 小幫手**

[VM 小幫手][19]工具是 CodePlex
專案，當您遠端桌面連線到虛擬機器執行個體時，可將所有相關疑難排解資料收集在一個位置。**VM 健康情況**
按鈕可提供執行個體的目前狀態。

### 可以偵測錯誤的位置

在將應用程式部署到 Azure 之前，最好的作法是在本機上對應用程式進行偵錯。Azure SDK 包含可模仿 Azure
雲端環境的模擬器，可讓您在無需部署應用程式的情況下執行應用程式並進行基本測試。使用的偵錯工具會因使用的程式設計語言和開發工具而有所不同。

在部署應用程式之後，您可以使用偵錯程式 (如 Visual Studio)
在雲端中進行偵錯。這需要建立並部署偵錯組建。為了要執行此動作，您必須連線到特定的角色執行個體。如果您有包含多個角色和角色執行個體的複雜應用程式，則判斷要連線到哪個角色執行個體是非常困難的。Visual
Studio Ultimate 支援允許 .NET 角色追蹤偵錯資訊的 IntelliTrace。發生問題時，您可以下載此資訊並將它載入 Visual Studio。您可查看每個角色執行個體的 IntelliTrace
記錄，來研判發生問題的位置。雖然在雲端中偵錯仍然有些缺點，但在部分情況下偵錯仍是必要的。不是所有的 Azure 服務都會有模擬器
(例如服務匯流排)，而且不是所有支援的開發工具 (例如 Mac 和 Linux) 都會隨附模擬器。

一旦您開始在本機上對應用程式進行偵錯，您很有可能必須要仰賴應用程式內建的工具，來找出問題發生的位置。

**Node.js 偵錯**

如需對 Node.JS 應用程式進行偵錯，您可以使用 [GitHub][20] 所提供的 [節點偵測器] 工具。透過 Azure
儲存模擬器，您可以在開發機器上本機執行節點偵測器。如需詳細資訊，請參閱 Jim Wang 的部落格：[在 Azure
模擬器中對節點進行偵錯][21]。

如果您打算在 Azure 上對應用程式進行偵錯，請在您的 Web 角色、背景工作角色或 VM 執行個體上安裝 [GitHub][22] 的
IISNode
完整版。如同稍早的討論，我們不建議在實際執行環境中對應用程式進行偵錯，並擴大到多個執行個體，因為您可能不知道要偵錯哪個角色執行個體或 VM。

若要在 Web 角色上使用 [節點偵測器]，請在 Web 角色中安裝此套件，並依照平常方式來使用該 [節點偵測器] 工具。如需使用
[節點偵測器] 進行偵錯的詳細資訊，請參閱[使用節點偵測器進行偵錯][23]。

**IntelliTrace**

Microsoft Visual Studio Ultimate 包含
[IntelliTrace][24]，在將應用程式部署到實際執行環境之前，您可以啟用 IntelliTrace
來對應用程式進行偵錯。IntelliTrace 支援 ASP.NET 和 WCF 應用程式。生產服務不支援
IntelliTrace，但在應用程式部署到 Azure 之後，您可以使用 IntelliTrace 來取得應用程式例外狀況。Jim Nakashima 的部落格貼文說明如何使用 [IntelliTrace 來對 Azure 雲端服務進行偵錯]。

**Fiddler**

[Fiddler][25] 是 Web 偵錯 Proxy，可記錄電腦與網際網路之間的所有 HTTP(S) 流量。Fiddler
可讓您檢查流量、設定中斷點及「弄亂」連入或連出資料。Fiddler 在疑難排解 Azure 儲存時特別管用。

若要在本機開發網狀架構中使用 Fiddler，請使用 ipv4.fiddler 而不是 127.0.0.1：

* 啟動 Fiddler。
* 在開發網狀架構中啟動服務。
* 瀏覽到 http://ipv4.fiddler:/。Fiddler 應會追蹤要求。

若要在本機開發儲存體中使用 Fiddler，請修改服務組態檔案來指向 Fiddler：

開啟 ServiceConfiguration.cscfg 檔案，然後將連線字串變更為：

    Value="UseDevelopmentStorage=true;DevelopmentStorageProxyUri=http://ipv4.fiddler"

* 啟動 Fiddler。
* 啟動服務。Fiddler 應會追蹤任何儲存體要求。

## 疑難排解與 Azure 主控模型

本節將討論使用不同的 Azure 主控模型來對應用程式進行偵錯的最佳作法。

<h2><a id="Websites" ></a>Azure 網站</h2>


設計具支援性的 Azure 網站時，請儘可能地遵循本文稍早提出的建議。這包括檢查與處理錯誤、套用暫時性失敗重試邏輯、追蹤及記錄。若要疑難排解
Azure 網站，可透過設定網站顯示應用程式錯誤、為網站設定診斷、收集診斷資料，然後分析收集的資料以找出並解決問題來完成。

Azure 網站可啟用下列診斷選項的組態：

* Web 伺服器記錄
* 詳細的錯誤訊息
* 失敗的要求追蹤

如需這些主題的相關資訊，請參閱：[疑難排解 Azure
網站](/en-us/develop/net/best-practices/troubleshooting-web-sites/)。

當 Azure 網站的 Web 伺服器記錄已啟用時，網站將會使用 [W3C 擴充記錄檔格式][26]，在記錄檔中記錄所有的 HTTP
交易。您接著可以使用[記錄檔剖析器][27]來查詢記錄檔。您可以在 [記錄檔剖析器 Plus][28] 和 [TechNet
記錄檔剖析器範例][29]上找到一些記錄檔剖析器查詢範例。如果您想要在執行 Office 2007/2010 的電腦上產生 CHART
輸出類型，請依照[記錄檔剖析器 Plus][30] 上的指示安裝 [Office 2003 Web 元件][31]。

Azure 網站會使用 IIS 7.0 及更新版本所提供的相同失敗要求追蹤功能。不過，此功能不像 IIS 失敗要求追蹤一樣可進行設定。當您啟用 Azure 網站的失敗要求追蹤時，系統會擷取**所有**失敗要求。

<h2><a id="Cloudservices" ></a>Azure 雲端服務</h2>


如上所述，基於 Azure 雲端服務的分散式本質，防止應用程式進行非同步呼叫並處理暫時性失敗的重試是很重要的。

Azure 雲端服務所使用的偵錯技巧會視您所遇到的問題類型而定。調查涉及特定角色或角色執行個體的問題 (例如角色無法啟動或循環)，最好使用
[遠端桌面]。在這些情況下，您將得知哪個角色或角色執行個體有問題，您還可以連線到受影響的角色。當發生問題，但您不確定是哪個角色執行個體造成該問題時，疑難排解的最佳方法是追蹤和記錄。Azure
診斷提供一個可收集並管理追蹤與記錄資訊的機制。

Azure SDK 1.7 中已加入某些新的偵錯功能，包括發生例外狀況時更容易尋找堆疊追蹤，以及改善 [遠端桌面]
的連線能力。Windows 事件記錄中現在包含堆疊追蹤，讓您更輕鬆地查看問題到底出在什麼地方。此外，[遠端桌面]
連線能力也已得到改善。如果您的角色正在循環或已中止，您將能夠使用 [遠端桌面] 連線到有問題的角色並調查問題。

Azure 入口網站提供監視資料的存取權，該資料可協助 IT 專業人員和開發人員參與並診斷 Windows Azure
雲端服務中的問題。依預設，主機 VM 會收集如 [CPU 百分比]、[資料輸入]、[資料輸出]、[磁碟讀取輸送量] 和
[磁碟寫入輸送量]
等值。為角色執行個體啟用這些度量時不需要組態，而且對客戶不會有成本影響。您還可以收集其他效能資訊。若要收集詳細診斷資訊，您必須具備有效的診斷連線字串，因為此資訊會儲存在
Azure 儲存中，因此將會產生額外的儲存成本。當使用者啟用詳細資訊監視時，入口網站將會遠端設定角色執行個體，以便收集預設的效能計數器集。

### Azure 診斷

原始 Azure SDK 1.0 的隨附功能，可用來收集診斷資料並將資料儲存在 Azure 儲存體中，這些功能統稱為 Azure
診斷。此軟體建構在 Windows 事件追蹤 (ETW) 架構上，滿足了 Azure 向外延展架構所提出的兩個設計需求：

* 儲存在執行個體重新安裝映像期間可能會遺失的診斷資料。
* 針對多個執行個體的診斷提供中央儲存機制。

在角色中設定診斷之後，診斷會收集來自該特定角色的所有執行個體診斷資料。診斷資料可用來進行偵錯和疑難排解、測量效能、監視資源使用量、流量分析和容量規劃以及稽核。您可以排定或隨選傳輸至
Azure 儲存體帳戶以保有持續性。

Azure 診斷會使用下列四種重要方式來變更伺服器範例：

* 在應用程式建立時，必須已啟用診斷。
* 視覺化診斷結果所需的特定工具/步驟。
* 當機將會導致診斷資料的遺失，除非已將資料寫入持久的儲存體 (Azure 儲存體，而非位在每個執行個體上)。
* 當診斷儲存在 Azure 儲存體中時，診斷儲存體會產生每月成本。

因為 Azure
的一個主要優點是減少成本，所以成本尤其重要。目前消除使用診斷之成本的唯一方式，是將資料留在虛擬機器上。這在小型部署中或許可行，但在擁有許多執行個體的環境中則不切實際。以下是將財務影響降到最低的幾個方式：

* 確定儲存體帳戶與應用程式在相同的資料中心裡。如果因為某些原因他們不在相同的資料中心裡，請明智選擇排定傳輸的間隔。較短的傳輸時間將會提高資料相關性，但該抉擇可能不足以證明額外頻寬和處理負荷是否值得。
* 定期複製與清除 Azure 儲存體的診斷資料。診斷資料將會透過 Azure
  儲存體進行傳輸，但沒有必要便不會常駐在儲存體中。若要這樣做，以下許多工具可以使用：適用於 Azure 的 System Center
  監視組件、Cerebrata 的 Azure 診斷管理員及 Azure PowerShell Cmdlet。
* 僅選擇疑難排解與監視應用程式需要的診斷資料。除了大幅增加成本以外，擷取太多的資料還可能會讓疑難排解更加困難。
* 透過在應用程式中實作一個隨選參數，來控制診斷資料的收集和程度。
* 利用記錄層級 (詳細資訊、資訊、警告、錯誤) 來提供所有資訊，然後利用後續部署診斷設定來選擇性地收集資料。

<h2><a id="Vms" ></a>Azure 虛擬機器</h2>


對 Azure 虛擬機器上所執行的應用程式進行疑難排解時，通常會涉及在使用作業系統與平台時會用到的相同疑難排解技術。例如：

* Windows Server 2008 R2 x64 (RTM 和 SP1)、Windows 8 Service x64
  (Datacenter 和 Core)。有關該主題的可用資訊多得不可勝數，且許多可用工具可協助您快速完成工作。

* 如需有關這些方法的詳細資訊，請參閱[作法：對 Windows 服務應用程式進行偵錯][32]。

* 如需最佳方法的說明影片，請參閱 [Daniel Pearson 的簡報：Windows 偵錯與疑難排解][33]。

* 開啟 Suse Linux。在 [Suse Linux 文件][34]和網際網路上也有龐大的資源可用來疑難排解 Suse Linux
  上的應用程式。
* Ubuntu Linux。同樣地，您可以找到大量的資訊。若要開始使用產品文件，請參閱
  [https://help.ubuntu.com/][35]。
* CentOS Linux。如需詳細資訊，請參閱 [http://centos.org/][36]。

<h2><a id="PlatformServices" ></a>疑難排解 Azure 服務</h2>


不論應用程式是否在 Azure 上執行、它是使用什麼程式設計語言或程式庫建構而成，或是否在非 Microsoft 作業系統上執行，許多
Azure 服務 (例如 Azure SQL Database、Azure Active Directory 和 Azure 儲存體)
都有其用法特定的疑難排解建議。下列資訊提供了其中部分服務特定的最佳作法。

如本文設計部分中所概述，有許多針對非同步呼叫、追蹤及事件記錄實作最佳作法的支援程式庫。

#### Azure 儲存體疑難排解

下列連結將討論一些設計或作法，來減輕需要疑難排解或位置應加入追蹤或記錄工作等問題。

* 儲存體狀態和錯誤碼：[http://msdn.microsoft.com/en-us/library/windowsazure/dd179382.aspx][37]

* 儲存體分析：[http://blogs.msdn.com/b/windowsazurestorage/archive/2011/08/03/windows-azure-storage-analytics.aspx][38]
  (請參閱底部的三個「取得詳細資訊」連結。)

* Azure
  儲存體用戶端程式庫中的重試原則概觀：[http://blogs.msdn.com/b/windowsazurestorage/archive/2011/02/03/overview-of-retry-policies-in-the-windows-azure-storage-client-library.aspx][39]

* 如何充分利用 Azure
  資料表：[http://blogs.msdn.com/b/windowsazurestorage/archive/2010/11/06/how-to-get-most-out-of-windows-azure-tables.aspx][40]
  
  * 請參閱這些章節：「最佳作法」、「程式設計 Azure 的秘訣」，以及「逾時和 ServerBusy - 這是正常現象嗎？」

* 保護您的 Blob
  以免發生應用程式錯誤：[http://blogs.msdn.com/b/windowsazurestorage/archive/2010/04/30/protecting-your-blobs-against-application-errors.aspx][41]

**Azure 儲存體總管**

有許多方式可以探索 Azure 儲存體。Azure 儲存體團隊整理出一份[儲存體總管清單][42]。任一項都可讓您查看診斷檔案和 Azure
儲存體分析檔案。Cloudberry 實驗室的 [Azure Blob 儲存體的總管][43]提供一個使用者介面，按一下
**儲存體設定** 即可在應用程式中直接啟用儲存體分析。

<h2><a id="Misc" ></a>Azure 服務匯流排疑難排解</h2>


有關如何以強固和可維護性來減少常見問題的方式，開發使用 Azure
服務匯流排的應用程式，本節提供了高階指引。它還提供了有關如何找到並解決常見服務匯流排錯誤的詳細資料。

### 一般指引

服務匯流排是網際網路規模的[企業服務匯流排][44]，支援轉送和代理訊息功能。針對這兩種訊息類型，服務匯流排會實作系統層級的配額和閾值。如果應用程式超過這些配額，該應用程式會開始進行節流，否則您的要求或訊息便會遭到拒絕。如需服務匯流排配額以及超過配額時的行為等完整詳細資料，請參閱＜[Azure
服務匯流排配額][45]＞。部分是使用者定義的配額，例如佇列或主題的大小會在實體建立時進行定義。

若要查看服務匯流排訊息實體中的資料以及該資料的處理方式，您可以使用 Azure Tools for Visual Studio (1.7
版或更高版本)
中的[服務匯流排總管][46]或伺服器總管，來建立、刪除及測試佇列、主題、訂閱和規則。針對正在執行但未依照預期的方式處理資料的應用程式，這是一個絕佳的疑難排解方式。這些工具所包含的功能可讓您測試佇列、主題、轉送實體、追蹤個別傳送者與接收者工作所執行的作業、監視進行中測試執行的進度和效能，以及產生詳細的結果記錄
(包括任何錯誤訊息)。

### Service Bus 轉送

服務匯流排「轉送」服務會在雲端執行，並支援各種不同的傳輸通訊協定和 Web 服務標準，包括 SOAP、WS-\* 和
REST。您可以使用服務匯流排轉送作為委派，來接聽傳送至 WCF
服務的連入工作階段和要求。在轉送訊息模式中，內部部署或雲端架構服務會透過輸出連接埠連接到轉送服務，並且針對繫結至特定聚合位址的通訊建立雙向通訊端。用戶端不需要知道服務所在的位置，而且內部部署服務不需要在防火牆上開啟任何輸入連接埠。不過，視您的網路組態而定，從防火牆後方或透過
Proxy 伺服器連線到服務匯流排轉送時可能會遇到問題。[利用服務匯流排在防火牆後面進行主控][47]說明如何疑難排解並解決此類連線問題。

### 服務匯流排佇列和主題

服務匯流排佇列和主題可提供代理訊息功能，訊息會被推送並好好保留在服務匯流排佇列或主題中，直到接收者準備好使用他們為止。訊息傳送者和接收者不需要同時處於線上狀態；在取用方準備好接收訊息之前，訊息基礎結構都會可靠地儲存訊息。訊息
API 可能會遇到影響應用程式的各種錯誤。這些錯誤可大致分為下列類別：

* 使用者錯誤。例如，指出引數無效的代碼。建議的動作：請先嘗試修正此代碼，再繼續執行。
* 設定/組態錯誤。例如，與此動作相關的佇列或主題不存在或已遭刪除。建議的動作：檢閱您的組態並視需要進行變更。
* 暫時性錯誤。例如，指出服務目前無法處理要求的回應。建議的動作：重試此操作或通知使用者。如需相關資訊，請參閱[處理暫時性通訊錯誤][48]。
* 其他錯誤。例如，逾時錯誤或指出失去訊息鎖定的錯誤。建議的動作：您通常不會處理這些執行清理或中止的例外狀況。它們可能會用於追蹤。

[訊息例外狀況][49]提供適用於 .NET
的服務匯流排用戶端程式庫使用者可能遇到的例外狀況概觀，以及如何處理每種例外狀況類型的建議。因為適用於 .NET
的用戶端程式庫會與其他語言的服務匯流排程式庫結構緊密整合，即使您不是採用 .NET 語言進行程式設計，本指引還是有所助益。在某些情況下
(例如出現暫時性錯誤)，您可以重試此動作。您可以遵循本文先前概述的暫時性錯誤處理方針，有效地處理暫時性錯誤。此外，如需更多詳細資料、最佳作法及範例程式碼來示範如何處理
.NET 應用程式中的暫時性服務匯流排錯誤，請參閱 Azure 開發人員指引文章＜[運用 Azure
服務匯流排代理訊息應用程式開發介面的最佳作法][50]＞中的＜[處理暫時性通訊錯誤][48]＞一節。

開發使用代理訊息的應用程式時，另一個要著重的區域是確保實作可靠的訊息接收邏輯，以便準確並有效地處理訊息中的異常。[運用 Azure
服務匯流排代理訊息應用程式開發介面的最佳作法][50]文章中的「實作可靠的訊息接收迴圈」一節將說明使用 **PeekLock**
接收模式的多項技術，如果在第一次嘗試時未能順利傳遞訊息，此種接收模式可支援多次訊息傳遞。本文將建議可協助確保應用程式不會處理重複訊息的最佳作法。它還可協助防止由於鎖定逾時而發生的問題，並提升在
**PeekLock** 模式下的整體效能，方法是確保您會立即處理訊息。本文還提供使用適用於 .NET 之服務匯流排用戶端程式庫的範例程式碼。

### 其他疑難排解資源

如需有關常見服務匯流排錯誤以及如何調查與解決錯誤的其他詳細資料，請參閱[疑難排解服務匯流排][51]。

## Azure Active Directory 存取控制服務 (ACS)

疑難排解

* 錯誤碼：[http://msdn.microsoft.com/en-us/library/windowsazure/gg185949.aspx][52]
* ACS
  服務限制：[http://msdn.microsoft.com/en-us/library/windowsazure/gg185909.aspx][53]

<h2><a id="SQLTroubleshooting" ></a>Azure SQL Database 疑難排解</h2>


與 Azure SQL Database 進行互動時，由於 Azure SQL Database
應用程式的分散式本質，處理上需格外謹慎。本節將討論值得注意的幾個區域。這絕不代表完整清單。編寫具支援性 Azure SQL Database
程式碼的一個重點是檢查傳回碼，並確保您已具有可靠的重試程式碼以便處理失敗。

應用程式必須適當地處理登入失敗。SQL Database 執行個體需要使用 SQL
驗證。如果您無法成功登入，可能是您的認證無效，或是您要求的資料庫無法使用。

應用程式必須處理服務無法存取問題。如果已佈建伺服器且 Azure SQL Database 服務可以使用 (您可以使用 [Azure
健康情況狀態][54]頁面查看這點)，則可能的原因會是您的駐點安裝發生組態問題。例如，您可能無法解析名稱 (這種情況可以使用 tracert
這類工具進行測試)，防火牆可能封鎖 SQL Database 使用的連接埠 1433，或是您可能使用未正確設定的 Proxy
伺服器。請使用您會對 SQL Server 採用的相同技巧來疑難排解這些困難。如需詳細資訊，請參閱《[SQL Database
連線疑難排解指南][55]》和[疑難排解 SQL Database][56]。

應用程式必須處理一般網路錯誤。因為 Azure SQL Database 可能會在下列情況下中斷使用者的連線，這時您可能會收到一般網路錯誤：

* 當連線長時間處於閒置狀態時
* 當連線消耗大量資源或是花費相當長的時間處理某項交易時
* 如果伺服器太過忙碌

若要提升 Azure SQL Database 的效能，請使用您會對 SQL Server 採用的相同技巧。如需詳細資訊，請參閱下列主題：

* 《SQL Server 線上叢書》中的＜[疑難排解查詢][57]＞
* [疑難排解並最佳化 SQL Database 的查詢][58]
* [SQL Database 效能考量和疑難排解][59]
* [提升您的 I/O 效能][60]
* [System Center SQL Database 監視組件][61]

Azure SQL Database 會使用 SQL Server 錯誤訊息的子集。如需 SQL Server 錯誤的詳細資訊，請參閱《SQL Server 線上叢書》中的＜[錯誤和事件參考 (資料庫引擎)][62]＞

如果您需要復原登入名稱或密碼，請連絡您服務管理員，該服務管理員可授與您適當的伺服器和資料庫存取權。服務管理員也可以使用 Azure
管理入口網站來重設自己的密碼。

SQL Database 查詢可能會因為各種原因而失敗 -
格式錯誤的查詢、網路問題等等。部分的錯誤是暫時性的，這表示問題通常會自己消失。若是這種錯誤子集，在很短的間隔內重試查詢是很合理的。如果數次重試後查詢仍然失敗，則會報告錯誤。當然，不是所有的錯誤都是暫時性的。無論提交幾次相同的查詢，SQL
錯誤 102「不正確的語法」都不會消失。簡而言之，實作強固的重試邏輯需要幾點考量。在中斷使用者的連線之前，會盡可能先傳送表格式資料流 (TDS)
錯誤 Token。為了改善應用程式經驗，建議您在 SQL Database
應用程式中實作重試邏輯以擷取這些錯誤。發生錯誤時，請重新建立連線，然後重新執行失敗的命令或查詢。如需詳細資訊，請參閱下列連結：

* [SQL Database 中暫時性失敗的重試邏輯][63]
* [SQL Database 重試邏輯範例][64]
* [暫時性錯誤處理應用程式區塊][65]

### Azure SQL 備份與還原策略

基於可用的環境和工具，Azure SQL Database 需要它自己的備份與還原策略。在許多方面，風險已由處於 Microsoft
資料中心的資料庫居中協調。我們現有的工具足以涵蓋其他風險因子，不過，更好的工具即將推出，讓工作更加容易。Red-gate
最近發行了一套可備份與還原 SQL Database
的免費工具，您可以在下列連結中找到此工具：[http://www.red-gate.com/products/dba/sql-azure-backup/][66]。

SQL Data Sync 可讓您從 Data Sync
網站上輕鬆地建立並排定雙向同步處理，而無需編寫單行程式碼。您可以在下列連結中找到更多詳細資訊：[http://msdn.microsoft.com/en-us/library/windowsazure/hh456371.aspx][67]。

如需 SQL Database 備份與還原策略的詳細資訊，請參閱下列文章：

* 本主題提供 SQL Database
  備份與還原策略的概觀：[http://social.technet.microsoft.com/wiki/contents/articles/1792.sql-azure-backup-and-restore-strategy.aspx][68]
* 本主題說明如何將資料庫備份到相同伺服器上的另一個資料庫：[http://msdn.microsoft.com/en-us/library/windowsazure/ff951631.aspx][69]
* 本主題說明如何將現有的 SQL Database 執行個體匯出到指定儲存體帳戶上的
  Blob：[http://msdn.microsoft.com/en-us/library/windowsazure/hh335292.aspx][70]
* 本主題說明如何從儲存於 Blob 的 bacpac 檔案中匯入現有的 SQL Database
  執行個體：[http://msdn.microsoft.com/en-us/library/windowsazure/hh335291.aspx][71]
* 本主題說明由 SQL Database
  所提供的業務續航力功能：[http://msdn.microsoft.com/en-us/library/windowsazure/hh852669.aspx][72]

<h2><a id="Cache" ></a>Azure 快取</h2>


Azure 快取有兩種類別：Azure 共用快取和角色型 Azure 快取 (預覽)。共用快取是提供快取服務的多租用戶 Azure
服務。Azure 快取 (預覽) 會在角色上主控快取，方法是從主控角色執行個體的虛擬機器中使用一部分的記憶體。若要疑難排解 Azure
快取，請透過檢查錯誤碼和擷取例外狀況來觀察快取行為。使用角色型快取 (預覽) 時，您還可以使用效能計數器。快取問題通常屬於下列其中一個類別：

* 配額相關錯誤 - 超過配額 (共用快取)
* 節流 - 當沒有足夠的實體記憶體可以支援額外的快取項目時，便會發生節流
* 收回 - 以傷害應用程式效能的方式強制收回項目，以挪出空間給新的項目
* 到期 - 設定太短或太長的到期時間

如需配額相關錯誤的詳細資訊，請參閱[了解配額][73]。



[1]: https://www.windowsazure.com/en-us/develop/overview/
[2]: http://msdn.microsoft.com/en-us/library/hh680918%28v=pandp.50%29.aspx
[3]: http://nuget.org/packages/Microsoft.Experience.CloudFx
[4]: http://msdn.microsoft.com/en-us/magazine/ff714589.aspx
[5]: http://www.microsoft.com/download/en/details.aspx?id=11324
[6]: http://www.microsoft.com/en-us/server-cloud/system-center/operations-manager.aspx
[7]: http://wapmmc.codeplex.com/
[8]: http://cerebrata.com/Products/AzureDiagnosticsManager/
[9]: http://cerebrata.com/Products/CloudStorageStudio/
[10]: http://cerebrata.com/Products/AzureManagementCmdlets/
[11]: http://www.cerebrata.com
[12]: http://www.compuware.com/application-performance-management/
[13]: http://www.keynote.com/solutions/
[14]: http://pingdom.com/
[15]: http://www.alertbot.com/products/website-monitoring/default.aspx
[16]: http://www.apicasystem.com/integration-partners/
[17]: http://www.microsoft.com/en-us/server-cloud/system-center/avicode.aspx
[18]: http://msdn.microsoft.com/en-us/library/windowsazure/hh369930.aspx
[19]: http://azurevmassist.codeplex.com/
[20]: https://github.com/dannycoates/node-inspector
[21]: http://weblogs.asp.net/jimwang/archive/2012/04/17/debugging-node-node-inspector-in-the-azure-emulator.aspx
[22]: https://github.com/windowsazure/iisnode/downloads
[23]: http://howtonode.org/debugging-with-node-inspector
[24]: http://msdn.microsoft.com/en-us/library/dd264915.aspx
[25]: http://www.fiddler2.com/fiddler2/
[26]: http://go.microsoft.com/fwlink/?LinkID=90561
[27]: http://go.microsoft.com/fwlink/?LinkId=246619
[28]: http://logparserplus.com/Examples
[29]: http://technet.microsoft.com/en-us/library/ee692659.aspx
[30]: http://logparserplus.com/article/2
[31]: http://www.microsoft.com/downloads/en/details.aspx?familyid=7287252C-402E-4F72-97A5-E0FD290D4B76&displaylang=enBlockquote
[32]: http://msdn.microsoft.com/en-us/library/7a50syb3%28v=vs.90%29.aspx
[33]: http://technet.microsoft.com/en-us/edge/Video/hh867800
[34]: https://www.suse.com/documentation/
[35]: https://help.ubuntu.com/
[36]: http://centos.org/
[37]: http://msdn.microsoft.com/en-us/library/windowsazure/dd179382.aspx
[38]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/08/03/windows-azure-storage-analytics.aspx
[39]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/02/03/overview-of-retry-policies-in-the-windows-azure-storage-client-library.aspx
[40]: http://blogs.msdn.com/b/windowsazurestorage/archive/2010/11/06/how-to-get-most-out-of-windows-azure-tables.aspx
[41]: http://blogs.msdn.com/b/windowsazurestorage/archive/2010/04/30/protecting-your-blobs-against-application-errors.aspx
[42]: http://blogs.msdn.com/b/windowsazurestorage/archive/2010/04/17/windows-azure-storage-explorers.aspx
[43]: http://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx
[44]: http://en.wikipedia.org/wiki/Enterprise_service_bus
[45]: http://msdn.microsoft.com/en-us/library/windowsazure/ee732538.aspx
[46]: http://code.msdn.microsoft.com/Service-Bus-Explorer-f2abca5a
[47]: http://msdn.microsoft.com/en-us/library/windowsazure/ee706729.aspx
[48]: http://msdn.microsoft.com/en-us/library/hh851746(VS.103).aspx
[49]: http://msdn.microsoft.com/en-us/library/hh418082.aspx
[50]: http://msdn.microsoft.com/en-us/library/windowsazure/hh545245.aspx
[51]: http://msdn.microsoft.com/en-us/library/windowsazure/ee706702.aspx
[52]: http://msdn.microsoft.com/en-us/library/windowsazure/gg185949.aspx
[53]: http://msdn.microsoft.com/en-us/library/windowsazure/gg185909.aspx
[54]: http://go.microsoft.com/fwlink/p/?LinkId=168847
[55]: http://social.technet.microsoft.com/wiki/contents/articles/sql-azure-connectivity-troubleshooting-guide.aspx
[56]: http://msdn.microsoft.com/en-us/library/ee730906.aspx
[57]: http://msdn.microsoft.com/en-us/library/ms186351(SQL.100).aspx
[58]: http://social.technet.microsoft.com/wiki/contents/articles/1104.troubleshoot-and-optimize-queries-with-sql-azure.aspx
[59]: http://channel9.msdn.com/Events/TechEd/NorthAmerica/2011/DBI314
[60]: http://blogs.msdn.com/b/sqlazure/archive/2010/07/27/10043069.aspx?PageIndex=2#comments
[61]: http://www.microsoft.com/en-us/download/details.aspx?id=10631
[62]: http://go.microsoft.com/fwlink/p/?LinkId=166622
[63]: http://social.technet.microsoft.com/wiki/contents/articles/4235.retry-logic-for-transient-failures-in-sql-azure.aspx
[64]: http://code.msdn.microsoft.com/windowsazure/SQL-Azure-Retry-Logic-2d0a8401
[65]: http://msdn.microsoft.com/en-us/library/hh680934(PandP.50).aspx
[66]: http://www.red-gate.com/products/dba/sql-azure-backup/
[67]: http://msdn.microsoft.com/en-us/library/windowsazure/hh456371.aspx
[68]: http://social.technet.microsoft.com/wiki/contents/articles/1792.sql-azure-backup-and-restore-strategy.aspx
[69]: http://msdn.microsoft.com/en-us/library/windowsazure/ff951631.aspx
[70]: http://msdn.microsoft.com/en-us/library/windowsazure/hh335292.aspx
[71]: http://msdn.microsoft.com/en-us/library/windowsazure/hh335291.aspx
[72]: http://msdn.microsoft.com/en-us/library/windowsazure/hh852669.aspx
[73]: http://msdn.microsoft.com/en-us/library/gg185683.aspx