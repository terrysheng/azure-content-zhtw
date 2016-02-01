
<properties
   pageTitle="具狀態服務組合模式 | Microsoft Azure"
   description="Service Fabric Reliable Actors 設計模式使用具狀態的動作項目，以維護服務之間的呼叫和快取先前服務結果的狀態。"
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/05/2015"
   ms.author="vturecek"/>

# Reliable Actors 設計模式：可設定狀態的服務組合

開發人員花費了最近 15 年建置在企業中多層式架構的無狀態服務。他們在資料庫上建置服務。他們在其他服務建置高序位服務。更建置協調流程引擎和訊息導向中介軟體以協調這些服務。當使用者工作負載擴大時，若要處理更多互動性或更大級別的要求，無狀態服務導向架構 (SOA) 開始曝露缺點。

## 舊有方式：SOA 服務

雖然 SOA 服務基於其無狀態的本質，能順暢地進行水平調整，其卻建立了儲存層中並行處理和輸送量的瓶頸。這讓存取儲存體越來越難。大部分開發人員的常見作法是引進快取，作為減少對儲存體需求的解決方案。但該方案不是沒有缺點。像是另一個需要管理的層、並行存取快取、語意的限制及變更，還有一致性問題。如同[智慧快取模式](service-fabric-reliable-actors-pattern-smart-cache.md)中詳述的，虛擬動作項目模型為此問題提供完美的解決方案。有些開發人員嘗試藉由複寫其儲存層以解決其 SOA 問題。但是這種方法並不容易調整，而且它很快就達到常見的警示通訊協定界限。

第二個挑戰來自變更的需求。使用者和企業都要求互動式服務回應要求的速度以毫秒為單位，而不是秒，以此做為基準。為了符合此需求，開發人員已經開始在其他服務上建置外觀機制服務。在某些情況下，已建置許多外觀服務機制來建立若要建立以使用者為中心的服務。但是加入多個下游服務也迅速造成延遲問題。

開發人員也轉向快取和記憶體中的物件存放區。在某些情況下，這些方式會以不同的實作符合效能需求。以這種方式而言，開發人員通常會建置後端背景工作處理序，以定期建置快取。這樣可以減少昂貴的隨選快取擴展。他們開始解構他們的工作負載，將非同步作業與同步作業隔離。這樣可以取得更多互動作業的空間，以回應狀態的變更，這在 SOA 中尤其困難。

他們通常也會進一步介紹佇列和背景工作等層。這樣會讓解決方案更為複雜。

基本上，開發人員已開始尋求能夠建置「具狀態服務」的解決方案，它可以共置「狀態」和「服務行為」來解決以使用者為中心的互動式體驗。這就是 Azure Service Fabric Reliable Actors 模型作為服務組合層進場的時候，而非用於取代這些服務項目。

下圖將說明這一點：

![Reliable Actors、服務組合和狀態持續性][1]

## 利用動作項目實作更好的解決方案

對於撰寫服務，動作項目可以是無狀態或具狀態。

* 無狀態的動作項目可以做為基礎服務的 Proxy。這些動作項目可以動態調整 Service Fabric 叢集，並能夠快取與服務相關的特定資訊。這包含發現它時的端點。
* 具狀態的動作項目可以維護服務之間的呼叫，以及快取先前服務結果的狀態。狀態可以是持續性或暫時性。

此模式適用於許多情況。大部分情況下，動作項目需要進行外部呼叫以叫用特定服務上的作業。讓我們使用現代電子商務應用程式的範例來說明。這些應用程式會建置在提供各種功能的服務上，包含使用者設定檔管理、建議、購物籃管理、願望清單管理與購買。

大部分的電子商務開發人員嘗試將以使用者為中心的方法用於其架構--非常類似開發共享體驗。這是因為電子商務體驗主要也是與使用者和產品相關。基於效能原因，通常會藉由傳送快取最有可能支援的服務外觀機制來達成開發人員解決方案。

相反的方法是以動作項目為基礎的方法。使用者動作項目可以代表使用者的行為 (例如瀏覽目錄、對產品按讚、將項目加入購物籃、或是推薦產品給朋友)。但是它也代表使用者的組成狀態，包含使用者的設定檔、購物籃中的物品、朋友推薦的物品、使用者的購買歷程記錄、以及使用者的目前地區位置。

## 藉由使用可設定狀態的動作項目填入狀態

首先，讓我們看看使用者動作項目需要從多個服務填入其狀態的範例。我們不會為此項目提供程式碼範例，因為所有我們[在智慧快取模式](service-fabric-reliable-actors-pattern-smart-cache.md)討論過的項目，也適用於此處。使用者動作項目可以在登入時啟動，並填入來自後端服務的充足資料。全部及部分狀態可以預先填入隨選、計時器、或兩者的一部分，並在動作項目中快取。這個範例中，**設定檔**和**願望清單**服務如下圖所示：

![「設定檔和願望清單」服務][2]

開發人員可以預先填入常見使用者的狀態，並在他們登入時備妥。開發人員也可以在每個月瀏覽服務的使用者登入時填入狀態。您也可以在＜智慧快取＞一節中看到這些模式。

當使用者 23 登入時 (如上圖所示)，如果使用者動作項目 (23) 尚未啟動，則會將其啟動。然後使用者項目會從後端服務擷取相關的使用者設定檔資訊和願望清單。使用者動作項目也很可能會快取資訊以進行後續呼叫。如果 (舉例而言) 項目必須新增至願望清單，可透過延遲寫入或直接寫入來達成，如稍早所述。

現在讓我們看看使用者按一下 [讚] 按鈕喜歡產品的範例。這個動作可能需要多項服務的多個引動過程。這些動作包括傳送「讚」至目錄服務、觸發下一組建議，並張貼更新到社交網路。

其程式碼如下所示：

![對產品和「願望清單、設定檔以及目錄」服務按讚][3]

## 採用動作項目組合和非同步通訊
當開發人員要同時撰寫要求/回應樣式作業和非同步作業時，Service Fabric Actor 程式設計模型即會發揮功效。例如，雖然對產品按讚會立即將按讚的項目新增至使用者的願望清單中、張貼到社交網路上，並觸發下一組建議作業，也可以使用緩衝處理和計時器來以非同步方式執行。

另一個使用服務的使用者動作項目之主要優點是，動作項目會提供已快取的狀態一個自然的位置。最重要的是，動作項目也會以非同步方式回應其狀態的變更。這是無狀態服務非常棘手的狀況。例如，使用者可以執行一系列的動作，做為「使用者旅程」的一部分，而這些事件可在一個動作項目中即時快取。接著可以組合串流，可在事件時間或以同步方式在計時器上查詢，以變更動作項目的行為。

此時 SOA 純化論者肯定注意到這些不是動作項目的服務，因為端點是透過獨立語言的通訊協定公開。Service Fabric Reliable Actors 模型並非互通的元件或服務交互操作的平台。不過，沒有什麼東西阻止開發人員以 SOA 樣式服務的資料粒度方式思考，並以相同的方式模組化他們的動作項目或個別考量。這種服務稱為微服務。另外也沒有任何東西阻止開發人員將 REST 或 SOAP 端點做為交互操作層放在 Service Fabric Reliable Actors 的前面。

此外，具狀態服務的組合也適用於工作流程，並不限於如電子商務的交易式案例。Service Fabric 設計做為工作流程/協調流程引擎。它可以用來模組化涉及服務互動的工作流程，並維護這些互動的狀態。

您們可以從建置可提供動態體驗的可調整服務中，看到「無狀態服務」的缺點。透過結合狀態和行為，Service Fabric Reliable Actors 程式設計模型可協助開發人員在現有的投資上建置可調整及互動的體驗。


## 後續步驟

[模式：智慧型快取](service-fabric-reliable-actors-pattern-smart-cache.md)

[模式：分散式網路和圖形](service-fabric-reliable-actors-pattern-distributed-networks-and-graphs.md)

[模式：資源管理](service-fabric-reliable-actors-pattern-resource-governance.md)

[模式：物聯網](service-fabric-reliable-actors-pattern-internet-of-things.md)

[模式：分散式計算](service-fabric-reliable-actors-pattern-distributed-computation.md)

[某些反向模式](service-fabric-reliable-actors-anti-patterns.md)

[Service Fabric Reliable Actors 簡介](service-fabric-reliable-actors-introduction.md)


<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-pattern-stateful-service-composition/stateful-service-composition-1.png
[2]: ./media/service-fabric-reliable-actors-pattern-stateful-service-composition/stateful-service-composition-2.png
[3]: ./media/service-fabric-reliable-actors-pattern-stateful-service-composition/stateful-service-composition-3.png

<!---HONumber=AcomDC_0121_2016-->