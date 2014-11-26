<properties title="Azure Machine Learning API service operations" pageTitle="Machine Learning API service operations | Azure" description="Creating and managing Azure Machine Learning web services" metaKeywords="" services="" solutions="" documentationCenter="" authors="derrickv" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="derrickv" />

# Azure 機器學習 API 服務作業

傳統的 Microsoft Azure 機器學習 (Azure ML) 專案包含下列高階步驟：

1.  取得、分析及準備資料
2.  建立運用各種 ML 演算法的機器學習實驗
3.  定型、測試及產生定型模型
4.  使用定型模型建立作業流程，並將工作流程部署到生產環境
5.  監視模型的效能和後續更新

> 「實驗」一詞可用來說明一套互動流程，其中包括資料輸入和操作、定型程式和採用定向非循環圖 (DAG) 的評分程式。一旦工作流程發佈為 Azure Web 服務後，它將不再具有互動性，意即若要進行變更，您必須更新此模型然後重新發佈，才能更新 Web 服務及其行為。

資料科學家通常會透過多次反覆運算來執行步驟 1-3，最後將 ML 模型交給工程師和作業團隊以整合至生產系統供生產環境使用。

將 ML 模型整合與部署到生產系統的傳統程序可能需要數週甚至數個月的時間，這會視用來建置模型的程式碼 (例如 R、Python、C# 或 Java)、平台整合與基礎結構考量，及部署規劃而定。

Azure ML 透過首先實現簡易及直觀的模型建立和評估經驗，然後提供可在 Azure 中將實驗部署為 Web 服務的簡單程序，因此可大幅降低從模型實驗到在生產環境中執行模型作為 Web 服務的總時間。

本文件說明從 ML 實驗來設定 Azure ML Web 服務的概念和步驟。

# Azure ML 程序概觀

Azure ML 允許從 Azure Machine Learning Studio (ML Studio) 中所定義的 ML 實驗來建立 Web 服務。Azure ML Web 服務可用來根據實際輸入資料，在即時或批次模式下進行預測。

下圖將分兩個部分說明高階步驟：第一部分是建置模型，第二部分是將它發佈為 Web 服務。本文件會著重在圖 1 的右側圖表 – 發佈評分 Web 服務 - 以及說明該程序所涉及的概念。

![][0]

圖 1：佈建、建置及發佈評分 Web 服務

# Azure ML Web 服務

在機器學習 (ML) 的環境中，Web 服務是指可針對外部應用程式與機器學習工作流程之間提供通訊的軟體介面。它提供一個可與評分模型即時通訊的方法，以便取得預測結果，並將結果納入外部用戶端應用程式中。Azure ML 會運用 Microsoft Azure 進行 Azure ML Web 服務的部署、代管及管理。使用 Azure ML 建立的服務類型有兩種。

## 要求回應服務 (RRS)

「要求回應服務 (RRS)」是低延遲性的高階 Web 服務，可用來為從實驗環境建立並發佈的無狀態模型提供介面。

-   REST API：RRS 是一種 RESTFul Web 服務。
-   服務介面：在 Azure ML Studio 實驗中，RRS Web 服務介面會被定義為使用輸入/輸出連接埠之實驗設定的一部分。
-   開發階段：RRS 服務是 Azure ML 工作流程的一部分，首先會在預備環境中產生，然後在那裡進行測試。一旦它被視為完成且可供生產環境使用後，便會被部署到生產環境中。
-   已在 Azure 中部署：部署 RRS 的結果是一個 Azure Web 服務端點。
-   介面參數：RRS 服務要求是指資料必須使用 Studio 中已定義的實驗進行評分。回應是指模型預測的結果。
-   回應值：RRS 接受單一資料列的輸入參數，並會產生單一資料列作為輸出內容。輸出資料列可包含多個資料行。

## 批次執行服務 (BES)

「批次執行服務 (BES)」是指可為一批資料記錄進行非同步評分的服務。BES 的輸入與 RRS 中使用的資料輸入類似，主要的差異在於 BES 會從各種來源讀取批次記錄，來源範例包括 Blob、Azure 中的資料表、SQL Azure、HDInsight (Hive 查詢) 和 HTTP 來源。評分的結果會輸出至位於 Azure Blob 儲存體中的檔案，並在回應中傳回儲存體端點的資料。

BES 還提供介面，可用來取得執行評分程序及取消要求的狀態。BES 具有針對龐大資料執行模型封裝的能力。

-   REST API：BES 是一種 RESTFul Web 服務。
-   服務介面：與 RRS 類似，在 Azure ML Studio 實驗中，BES Web 服務介面會定義為使用輸入/輸出連接埠之模型設定的一部分。
-   開發階段：BES 服務是建立 BES 的部分工作流程，首先會在預備環境中產生，然後在那裡進行測試。在完成且可供生產環境使用後，它便會被部署到生產環境中。
-   已在 Azure 中部署：部署 BES 的結果是一個 Azure Web 服務端點。
-   介面參數：BES 服務要求是指位於 Azure Blob 中檔案的 URL，或是要評分之記錄的 SAS 輸入。回應會被寫入 Azure Blob 中，並傳回回應儲存體端點的 URL。

# 發佈 Azure ML Web 服務

Azure ML Studio 提供了瀏覽器式的應用程式，方便在圖形化使用者介面中使用各種資料來源、資料操作和 ML 演算法，輕鬆建立及執行機器學習實驗。在 ML Studio 中的實驗會被建構成資料處理模組的定向非循環圖 (DAG)。

在設定並成功執行實驗以在資料上定型後，它便可儲存為定型模型並供評分使用。接著，此定型模型便可用於評分實驗或工作流程，並發佈為 Azure Web 服務。

## 定型實驗

實驗可以包括用來載入和操作資料、套用機器學習演算法及評估結果的各種模組。定型模組會使用定型資料集和學習演算法來預測回應。

在順利完成模型的執行後，便可將定型模型儲存為可重複使用的元件，以供評分測試資料集和查詢使用。

![][1]

圖 2：範例說明在實驗中儲存定型模型

接著，便可在應用程式的定型模型區段中使用所儲存的定型模型。

![][2]

圖 3：顯示模型清單的定型模型區段

## 評分實驗

評分實驗會使用定型模型和範例資料來產生預測。

上圖 1 說明評分模型在實驗中的用途。在 Studio 中，它是機器學習模組的一部分。

![][3]

圖 4：評分模型

### 要求回應服務與批次執行服務的比較

建置將發佈為 Web 服務的評分實驗時，僅可以在這兩個服務中選取一種服務，視評分案例而定。如果評分要求涉及為單一記錄評分，例如，判斷客戶 A 是否即將更換貨運公司的要求 (客戶變動預測)，此要求可以即時進行評分並建立為 RRS Web 服務。服務將即時傳回預測模型的結果 – 如果是上述變動預測範例，結果可能為 [是] 或 [沒有回應]。

若是必須在一個要求中為多份記錄評分的評分作業 (例如，包含客戶資料的一批記錄已傳送到服務等候評分)，則 BES 是適當的服務。在此案例中的要求將會是非同步要求，在所有處理完成之後並在傳回回應之前，所有記錄會進行處理並儲存在 Azure Blob 中。

### 使用定型模型

若要設定評分實驗，實驗中將會加入定型模型 (圖 3 中的「成人收入預測工具」)。現在，可將用來定型定型模型的其他模組移除。最後的工作流程看起來會如下面圖 5 所示。

### 輸入和輸出連接埠

在使用定型模型 (請參閱上節以取得詳細資料) 設定實驗及為更新實驗評分之後，您必須設定輸入和輸出連接埠，這將會指定資料預測模型的資料進入點和預測結果的資料結束點，並充當已發佈 Web 服務的介面定義。在進入點上按一下滑鼠右鍵，便可設定評分模型的輸入連接埠，如下所示。

![][4]

圖 5：設定評分輸入連接埠

同樣地，評分模型的輸出連接埠可設定如下。

![][5]

圖 6：設定輸出連接埠

## 發佈服務

在設定連接埠且執行實驗之後，可將模型發佈為 Web 服務。第一個步驟是將服務發佈至預備環境並在那裡進行測試，以確保在可供生產部署之前，它會傳回預期的結果。

### 發佈到預備環境

按一下 [發佈 Web 服務] 圖示會將 Web 服務部署到預備環境。

![][6]

圖 7：發佈 Web 服務按鈕

在將模型作為 Web 服務發佈到預備環境之後，您可以使用輸入參數測試模型，並將它標示為部署到生產環境。儀表板會顯示測試的連結。

![][7]

圖 8：Web 服務儀表板

透過按一下 [測試] 連結並提供評分的參數，便可在預備環境中測試 Web 服務。使用模型並根據所提供的資料來評分測試要求，便會傳回評分結果。

### 發佈至生產環境

將 Web 服務發佈至生產環境，讓其他應用程式能夠使用此服務來進行預測和評分。在完成部署到預備環境並成功測試後，Web 服務便會被標示為部署到生產環境。

![][8]

圖 9：將 Web 服務標示為可供生產部署

這並不會實際執行部署，而是建立使用者通知，內容包含將服務部署到生產環境的適當部署權限。

![][9]

圖 10：部署到生產環境的部署通知和選項

## 呼叫 Web 服務

#### RRS

RRS Web 服務是一個 REST 端點，可在用戶端應用程式中使用各種程式設計語言進行呼叫。API 說明頁面可提供呼叫新 Web 服務的範例程式碼連結，提供的範例包括 C#、R 和 Python 語言。

![][10]

圖 11：呼叫 RRS 的範例程式碼

## 非評分實驗

除了建置評分 Web 服務以外，您還可以建立實驗來執行如資料擷取和轉換等其他工作。在此情況下，Web 服務將不會執行機器學習作業。它會使用 Azure ML Studio 的資料操作功能，以讀取各種資料來源、轉換資料類型，或篩選和套用資料及數學操作。

### 發佈非評分 Web 服務

發佈非評分 Web 服務的步驟與上述的評分服務類似。主要差異在於評分模型上不會定義輸出連接埠。

# 更新已發佈的服務

基於各種原因，可能需要更新已發佈的 Web 服務，例如更新定型資料、變更用於定型和評分的資料結構描述、需要改進演算法，或對原始 ML 模型所做的其他變更。這些變更將會對定型模型和評分結果造成影響，因此需要發佈更新的 Web 服務。

![][11]

圖 12：編輯模型並發佈更新的評分 Web 服務

## 更新定型模型

對定型實驗進行變更將會需要對定型模型重新定型。若要執行此動作，則必須編輯已發佈的模型。以下範例顯示移除現有定型模型之後的評分工作流程 (如上圖 5 所示)。

![][12]

圖 13：從工作流程中移除的定型模型

下一個步驟會新增所需模組，將資料切分為定型和測試、套用學習演算法、定型模型、為定型資料評分及評估結果。請注意，所需模組可能會依實驗所需的其他變更 (例如，需要套用其他學習演算法) 而異。(圖 14)

在加入新的模組後，您必須視需要設定這些模組，才能重新執行實驗。例如，以下範例中的紅色圓圈表示尚未設定定型模型的標籤資料行。

![][13]

圖 14：新增模組以重新定型模型。

按一下 [定型模型] 並設定標籤資料行名稱便可解決問題。

![][14]

圖 15：選取收入資料行作為標籤

## 儲存更新的定型模型

在正確設定所有新模組之後，實驗應可在儲存後順利重新執行。接著便可儲存定型模型 (如上圖 2 所示)。不同之處在於，在此情況下必須勾選核取方塊才能更新現有的定型模型。

![][15]

圖 16：更新現有的定型模型

## 發佈更新的服務

更新定型模型後，將重複以上有關發佈 **[Azure ML Web 服務]** 一節中的所述步驟：

-   在評分實驗中使用 (現已更新) 定型模型
-   設定輸入/輸出連接埠
-   發佈至預備環境
-   發佈到生產環境

在更新實驗且建立及評分新的定型模型後，按一下 [發佈 Web 服務] 將會發佈此服務。新建立的服務將會覆寫現有服務。

<!--Image references-->

<!--Link references-->

  [0]: ./media/machine-learning-overview-of-azure-ml-process/oamlp1.png
  [1]: ./media/machine-learning-overview-of-azure-ml-process/oamlp2.png
  [2]: ./media/machine-learning-overview-of-azure-ml-process/oamlp3.png
  [3]: ./media/machine-learning-overview-of-azure-ml-process/oamlp4.png
  [4]: ./media/machine-learning-overview-of-azure-ml-process/oamlp5.png
  [5]: ./media/machine-learning-overview-of-azure-ml-process/oamlp6.png
  [6]: ./media/machine-learning-overview-of-azure-ml-process/oamlp7.png
  [7]: ./media/machine-learning-overview-of-azure-ml-process/oamlp8.png
  [8]: ./media/machine-learning-overview-of-azure-ml-process/oamlp9.png
  [9]: ./media/machine-learning-overview-of-azure-ml-process/oamlp10.png
  [10]: ./media/machine-learning-overview-of-azure-ml-process/oamlp11.png
  [11]: ./media/machine-learning-overview-of-azure-ml-process/oamlp12.png
  [12]: ./media/machine-learning-overview-of-azure-ml-process/oamlp13.png
  [13]: ./media/machine-learning-overview-of-azure-ml-process/oamlp14.png
  [14]: ./media/machine-learning-overview-of-azure-ml-process/oamlp15.png
  [15]: ./media/machine-learning-overview-of-azure-ml-process/oamlp16.png
