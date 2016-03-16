<properties
   pageTitle="將公用資料提取到 Azure 事件中樞 | Microsoft Azure"
   description="從 Web 範例匯入事件中樞的概觀"
   services="event-hubs"
   documentationCenter="na"
   authors="spyrossak"
   manager="timlt"
   editor=""/>

<tags 
   ms.service="event-hubs"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/05/2016"
   ms.author="spyros;sethm" />

# 將公用資料提取到 Azure 事件中樞

在一般物聯網 (IoT) 案例中，您會有數個裝置，可在其中設計程式，將資料提取到 Azure，即 Azure 事件中樞或 IoT 中樞。這兩個中樞皆為 Azure 的進入點，可利用 Microsoft Azure 提供的各式各樣工具，進行儲存、分析和視覺化。不過，這兩者都需要您推送資料，而這些資料會格式化為 JSON，並以特定方式加以保護。這會帶來下列問題。如果您想要從公用或私人來源 (在這裡資料會公開為 Web 服務或某種形式的摘要) 帶入資料，但無法變更資料發佈方式，該怎麼辦? 想想天氣、交通或股票報價 - 您無法要求 NOAA、WSDOT 或 NASDAQ 對事件中樞設定推送作業。為了解決這個問題，我們已撰寫開放原始碼的小型雲端範例，您可以加以修改並部署，從這類來源提取資料，並將資料推送至事件中樞。從這裡，您可以隨心所欲地使用這些開放原始碼，當然，必須遵循生產者的授權合約。您可以在[這裡](https://azure.microsoft.com/documentation/samples/event-hubs-dotnet-importfromweb/)找到應用程式。

請注意，此範例中的程式碼只會顯示如何從一般 Web 摘要提取資料，以及如何將資料寫入到 Azure 事件中樞。此範例不是實際執行應用程式，也不適用於這類環境 - 只是讓開發人員留意的 DIY 範例而已。此外，此範例的不表示建議您應該**提取**資料到 Azure，而非**推送**它。在決定端對端架構之前，您應該檢閱安全性、效能、功能及成本因素。

## 應用程式結構

應用程式是以 C# 撰寫，[範例說明](https://azure.microsoft.com/documentation/samples/event-hubs-dotnet-importfromweb/)包含您要修改、建置及發佈應用程式所需的一切資訊。下列各節提供應用程式用途的高階概觀。

我們一開始假設您有權存取資料摘要。例如，您可能想要從華盛頓州運輸部門提取交通資料，或是從 NOAA 提取天氣資料，來顯示自訂報告，或結合該資料與您應用程式中的其他資料。您也將需要設定 Azure 事件中樞，並且知道存取它所需的連接字串。

當 GenericWebToEH 解決方案啟動時，會讀取組態檔 (App.config) 來取得一些內容：

1. 發佈資料之網站的 URL 或 URL 清單。在理想情況下，這是以 JSON 發佈資料的網站，例如 WSDOT 在[這裡](http://www.wsdot.wa.gov/Traffic/api/)參考的網站。 
2. URL 的認證 (若需要)。許多公用來源不需要認證，或者您可以將認證放在 URL 字串中。有些則需要個別提供。(請注意，您只能在此應用程式中指定一組認證，好讓它只會在您指定一個 URL 而非 URL 清單時運作)。
3. 服務匯流排連接字串，以及該服務匯流排命名空間中的事件中樞名稱，也是您會推送資料的位置。您可在 Azure 傳統入口網站中找到此資訊。
4. 睡眠間隔 (以毫秒為單位)，表示輪詢公用資料網站之間的間隔。設定此項目需要思考一點重點。如果您不常進行輪詢，可能會遺失資料；另一方面，如果您太常進行輪詢，可能會得到許多重複的資料，或更糟的是，您可能會被貼上不法的標誌而遭到封鎖。考量更新資料來源的頻率 - 天氣或交通資料可每隔 15 分鐘重新整理，但股票報價可能每隔幾秒鐘即需重新整理，取決於您何處取得這些資料。 
5. 分辨應用程式資料是以 JSON 還是 XML 傳入的旗標。由於您需要將資料推送至事件中樞，應用程式在傳送之前，必須透過模組將 XML 轉換為 JSON。

在讀取組態檔之後，應用程式會進入迴圈 - 存取公用網站、在必要時轉換資料、將資料寫入至事件中樞，然後等待睡眠間隔結束之後再全部重新執行。具體而言：

  * 讀取公用網站。如需接收已備妥要傳送的資料，會從 Azure/GenericWebToEH/ApiReaders/RawXMLWithHeaderToJsonReader.cs 使用 RawXMLWithHeaderToJsonReader 類別的執行個體。也會讀取 getdata () 方法中的來源資料流，然後使用 GetXmlFromOriginalText，將它分割為較小的片段 (例如記錄)。這種方法會讀取 XML，以及正確格式的 JSON 或 JSON 陣列。接著，會從 App.config (default=empty) 使用 MergeToXML 組態啟動處理。
  * 在 Program.cs 的 Process() 方法中，會以迴圈方式實作接收和傳送資料。從 getdata () 接收輸出結果之後，此方法會將事件中樞的個別值加入佇列。

## 後續步驟

若要部署解決方案，請複製或下載 [GenericWebToEH](https://azure.microsoft.com/documentation/samples/event-hubs-dotnet-importfromweb/) 應用程式、編輯 App.config 檔案、加以建置並發佈。一旦您發佈了應用程式，就可以看到它正在 Azure 傳統入口網站的雲端服務下執行，而且您可以在 [設定] 索引標籤中變更某些組態設定 (例如事件中樞目標和睡眠間隔)。

請參閱 [Azure 範例資源庫](https://azure.microsoft.com/documentation/samples/?service=event-hubs)中和 [MSDN](https://code.msdn.microsoft.com/site/search?query=event%20hubs&f%5B0%5D.Value=event%20hubs&f%5B0%5D.Type=SearchText&ac=5) 上的其他事件中樞範例。

<!---HONumber=AcomDC_0211_2016-->