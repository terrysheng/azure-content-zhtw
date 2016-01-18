<properties 
    pageTitle="常見的 DocumentDB 使用案例 | Microsoft Azure" 
    description="深入了解 DocumentDB 的前五個使用案例：使用者產生的內容、事件記錄、目錄資料、使用者喜好設定資料，和物聯網 (Internet of Things，IoT)。" 
    services="documentdb" 
    authors="h0n" 
    manager="jhubbard" 
    editor="monicar" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/30/2015" 
    ms.author="hawong"/>

# 常見的 DocumentDB 使用案例
本文提供幾個常見的 DocumentDB 使用案例概觀。本文中的建議可以做為使用 DocumentDB 開發應用程式的起點。

閱讀本文後，您將能夠回答下列問題：
 
- DocumentDB 有哪些常見使用案例？
- 使用 DocumentDB 做為使用者產生的內容存放區有何優點？
- 使用 DocumentDB 做為目錄資料存放區有何優點？
- 使用 DocumentDB 做為事件記錄存放區有何優點？
- 使用 DocumentDB 做為使用者喜好設定資料存放區有何優點？
- 使用 DocumentDB 做為物聯網 (Internet of Things，IoT) 系統的資料存放區有何優點？

    
## 使用者產生的內容
DocumentDB 的常見使用案例是針對 Web 和行動應用程式儲存和查詢使用者產生的內容 (UGC)，尤其是社交媒體應用程式。一些 UGC 範例包括對談、推文、部落格文章、評等和註解。通常，社交媒體應用程式中的 UGC 會結合使用自由格式文字、屬性、標記和不受固定結構限制的關聯性。

內容 (例如聊天、註解和貼文) 可以儲存在 DocumentDB 中，而無需要求關聯式對應層的轉換或複雜物件。開發人員在反覆查看應用程式程式碼時，可以輕鬆地新增或修改資料屬性以符合需求，進而加快開發的速度。

與各種社交網路整合的應用程式必須回應這些網路不斷變更的結構描述。由於 DocumentDB 依預設會自動編製資料的索引，您可以隨時查詢資料。因此，這些應用程式可以有彈性地根據其各自的需求擷取投影。

許多社交應用程式是以全球的規模運作，而且可能出現無法預期的使用模式。調整資料存放區的彈性十分重要，因為應用程式層會進行調整以符合使用需求。您可以透過在 DocumentDB 帳戶下新增其他資料分割區來進一步向外延伸。此外，您也可以跨多個區域建立其他 DocumentDB 帳戶。如需 DocumentDB 服務區域可用性，請參閱 [Azure 區域](http://azure.microsoft.com/regions/#services)。

## 目錄資料
目錄資料使用方式案例涉及儲存和查詢一組實體屬性，例如人員、地點和產品。目錄資料的一些範例包括使用者帳戶、產品目錄、IoT 的裝置註冊，和材料表系統。這項資料的屬性可能會有所不同，而且可以隨時間變更，以符合應用程式需求。

請細想汽車零件供應商產品目錄的範例。除了所有零件共用的通用屬性外，每個零件還可能會有自己的屬性。此外，用於特定零件的屬性可能在明年推出新的模型時變更。做為 JSON 文件存放區，DocumentDB 支援彈性的結構描述，並可讓您使用巢狀屬性呈現資料，因此它很適合用來儲存產品目錄資料。

## 記錄資料
應用程式記錄通常會大量發出，並且根據部署的應用程式版本或元件記錄事件可能會有不同的屬性。記錄資料不會受到複雜關聯性或固定結構的限制。由於 JSON 屬於輕量型且方便閱讀，採用 JSON 格式保存記錄資料便成為一種趨勢。
   
與事件記錄資料相關的主要典型使用案例有兩個。第一個使用案例是在資料子集上執行臨機操作查詢，以進行疑難排解。在疑難排解的過程中，資料子集會是第一個擷取的記錄檔，通常會按照時間序列進行排序。然後，透過篩選資料集的錯誤層級或錯誤訊息來執行向下鑽研。這是為什麼將事件記錄檔儲存在 DocumentDB 是一項優點的原因。根據預設，系統會自動為儲存在 DocumentDB 的記錄資料編製索引，因此它可以隨時提供查詢。此外，您可以按時間序列，保存跨資料分割的記錄資料。根據您的保留原則，較舊的記錄檔可以整合到冷儲存體。

第二個使用案例涉及在大量的記錄資料上，離線執行長時間執行的資料分析工作。這個使用案例的範例包括伺服器可用性分析、應用程式錯誤分析，和點選流資料分析。通常，您可以使用 Hadoop 來執行這些類型的分析。有了適用於 DocumentDB 的 Hadoop Connector，DocumentDB 資料庫可以當做 Pig、Hive 和 Map/Reduce 工作的資料來源與接收器。如需適用於 DocumentDB 的 Hadoop Connector 詳細資訊，請參閱[使用 DocumentDB 與 HDInsight 執行 Hadoop 工作](documentdb-run-hadoop-with-hdinsight.md)。

## 使用者喜好設定資料
現今，大部分的現代 Web 和行動應用程式具備複雜的檢視和體驗。這些檢視和體驗通常是動態的，根據使用者的喜好設定或情緒和品牌需求量身訂做。因此，應用程式必須要能夠有效地擷取個人化設定，以便快速呈現 UI 項目和體驗。

JSON 是呈現 UI 配置資料的有效格式，因為它不只是輕量，還可以輕鬆地透過 JavaScript 解譯。DocumentDB 提供可微調的一致性層級，允許快速讀取和低延遲寫入。因此，在 DocumentDB 中將包括個人化設定的 UI 配置資料儲存為 JSON 文件，是在不同的線路上取得這項資料的有效方法。

## 裝置感應器資料
IoT 使用案例在如何擷取、處理和儲存資料方面通常共用一些模式。首先，這些系統允許攝取資料，可從各種地區設定的裝置感應器中擷取暴增的資料量。接著，這些系統會處理並分析資料流資料，以衍生即時的資訊分析。最後同樣重要的一點，大部分的資料最終將會置入資料存放區，進行臨機操作查詢和離線分析。

Microsoft Azure 提供可在 IoT 使用案例中運用的各式各樣服務。Azure IoT 服務是一組包括 Azure 事件中樞、Azure DocumentDB、Azure 串流分析、Azure 通知中心、Azure 機器學習、Azure HDInsight 和 PowerBI 的服務。

Azure 事件中樞可以擷取暴增的資料量，因為它提供高輸送量資料擷取和低延遲。您可以將需要處理以取得即時資訊分析的擷取資料，使用漏斗方式倒入 Azure 串流分析以進行即時分析。您可以將資料載入 DocumentDB 以進行臨機操作查詢。將資料載入 DocumentDB 之後，這些資料便可提供查詢。DocumentDB 中的資料可以當做參考資料使用，以做為即時分析的一部分。此外，您還可以進一步地調整和處理資料，方法是將 DocumentDB 資料連線到 HDInsight，以進行 Pig、Hive 或 Map/Reduce 工作。接著，調整過的資料會被載回 DocumentDB 進行報告。

如需使用 DocumentDB、EventHubs 和 Storm 的 IoT 解決方案範例，請參閱 [GitHub 上的 hdinsight-storm-examples 儲存機制](https://github.com/hdinsight/hdinsight-storm-examples/)。

如需有關適用於 IoT 的 Azure 產品詳細資訊，請參閱[建立您的物聯網](http://www.microsoft.com/zh-TW/server-cloud/internet-of-things.aspx)。

## 後續步驟
 
若要開始使用 DocumentDB，您可以建立[帳戶](http://azure.microsoft.com/pricing/free-trial/)，然後依照我們的[學習路徑](https://azure.microsoft.com/documentation/learning-paths/documentdb/)執行，以了解 DocumentDB 並找到所需的資訊。

或者，如果您想要閱讀更多有關使用 DocumentDB 的客戶，您也可以使用下列客戶案例：

- [Breeze](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18602)。只需幾分鐘的時間，前置整合器即可使用富彈性的雲端技術來提供跨國企業的全球資訊分析。
- [News Republic](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18639)。為新聞加入智慧功能，提供以參加的公民為對象的資訊。 
- [SGS International](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18653)。為取得全球的一致性色彩，主要品牌會尋求 SGS 的協助。而 SGS 會求助於 Azure。
- [Telenor](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18608)。全球領導者 Telenor 借助雲端之力加快啟動速度。 
- [XOMNI](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18667)。未來存放區的運作基礎會是快速搜尋和簡單資料流程。
 

<!---HONumber=AcomDC_0107_2016-->