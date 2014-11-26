<properties title="Microsoft Azure Machine Learning 常見問題集 (FAQ)" pageTitle="Azure Machine Learning 常見問題集 | Azure" description="關於 Microsoft Azure Machine Learning 的常見問題" metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="pamehta" manager="paulettm" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/06/2014" ms.author="pamehta" />

# Microsoft Azure Machine Learning 常見問題集 (FAQ)

### 一般

**1. 什麼是 Microsoft Azure Machine Learning？**

Microsoft Azure Machine Learning 是受到完整管理的服務，可用來建立、測試、推動及管理雲端中的預測分析方案。您現在只需使用一個瀏覽器，即可註冊 Azure Machine Learning、上傳資料和立即啟動機器學習實驗。視覺化結構、大型模組和用以啟動範本的程式庫，讓您得以簡便而快速地執行一般機器學習工作。要將模型轉換為 Web 服務十分容易 - 點選幾下，在 ML Studio 中建置的預測模型即可轉換為公用 REST API，其中包含自訂資料轉換邏輯和精密的機器學習模型。

**2. 什麼是 Azure Machine Learning Studio？**

Azure ML Studio 是可透過網頁瀏覽器來存取的 Workbench 環境。ML Studio 包含一組具有視覺化結構介面的模組，可讓您以實驗的形式建置端對端資料科學工作流程。資料擷取、轉換、功能選取都有其相關模組，用以建置、評分及評估預測模型。ML Studio 中也建置了一些最先進的演算法，可支援 Bing 和 Xbox 中的機器學習。此外也包含可調整的開放原始碼機器學習封裝，例如 Vowpal Wabbit。ML Studio 支援 R。您可以加入現有的 R 程式碼，並將其合併到您的實驗中。ML Studio 可讓您將這些演算法與 R 程式碼結合，以建置預測模型。ML Studio 可讓您邀請團隊成員到您的工作區 (他們可在此處檢視及修改您的實驗)，以方便共同作業。

**3. 什麼是 Azure ML API 服務？**

ML API 服務可讓您以可調整、容錯 Web 服務的形式，部署在 ML Studio 中建置的預測模型。ML API 服務所建立的 Web 服務是 REST API，此類 API 提供的介面可用於外部應用程式與您的預測分析模型之間的通訊。Web 服務提供的相關機制可用來與預測模型即時通訊，以接收預測結果並將其合併到任何外部用戶端應用程式中。ML API 服務會使用 Microsoft Azure 進行 Azure ML REST API 的部署、主控和管理。使用 Azure ML API 服務可建立兩種類型的服務。批次執行服務可用於非同步批次存取，要求回應服務可用於低延遲同步回應。

預測模型可放入您工作區中的預備環境。ML API 服務也會產生 Web 服務的說明頁面。Web 服務說明頁面會提供在 C#、R 和 Python 中叫用 Web 服務的程式碼範例。您可以對 Web 服務發出互動式呼叫，以測試您的服務。其後，只須點選幾下，即可將預備 Web 服務放入實際執行環境中。進入實際執行環境後，您可以在 Azure 入口網站中監控已部署的服務及追蹤使用情形和錯誤。要更新 Web 服務，只需在 ML Studio 中更新模型，再將變更發送至預備服務即可。

**4. 如何存取 Microsoft Azure Machine Learning？**

若要開始使用 Azure Machine Learning，請瀏覽[開始使用頁面][開始使用頁面]。請瀏覽 [Azure Machine Learning 中心][Azure Machine Learning 中心]以取得服務的更新、閱讀最新的 ML 團隊部落格、透過論壇參加我們的機器學習社群、存取產生說明、檢視模型庫，以及提供服務的意見反映以協助我們建構產品藍圖。

### 計費

**5. 機器學習如何計費？**

Azure ML Studio 服務會依作用中實驗的計算時數計費，不足一小時則按比例計費。Azure ML API 服務每 1,000 次預測 API 呼叫計費一次，正在執行的預測則依計算時數計費。不到 1,000 次的預測數量和零碎計算時數，會按比例計費。

費用會依您訂閱的每個工作區彙總計算。在每個工作區內，您會看見三個項目的費用

-   Studio 實驗時數 - 此計量會彙總所有因為在 ML Studio 中執行實驗和在預備環境中執行預測而產生的計算費用。
-   API 服務預測時數 - 此計量會包含在實際執行環境中執行的 Web 服務所產生的計算費用。
-   API 服務預測 (以 1000 次為單位) - 此計量會包含對實際執行 Web 服務的呼叫所產生的費用。

如需定價的詳細資訊，請瀏覽 [定價] 頁面：<http://azure.microsoft.com/zh-tw/pricing/details/machine-learning/>。

**6. Azure Machine Learning 是否有免費試用版？**

Azure ML 包含在 Azure 免費試用版中。在註冊 Azure 免費試用版後，您可以試用任何 Azure 服務一個月。若要深入了解 Azure 免費試用版，請瀏覽 <http://azure.microsoft.com/zh-tw/pricing/free-trial-faq/>。

### MACHINE LEARNING STUDIO

**7. Azure ML 支援哪些資料來源？**

資料可透過兩種方式載入 ML Studio 中：以資料集的形式上傳本機檔案，或使用讀取器模組匯入資料。本機檔案可以資料集的形式上傳，方法是在 ML Studio 中新增資料集。請參閱 ML Studio 中的說明主題「取得資料」，以深入了解支援的檔案格式。

[讀取器] 模組可從 Azure 資料表、Azure Blob、SQL Database (Azure) 或 HDInsight 讀取資料。您也可以透過 HTTP 從資料來源提取資料。如需詳細資訊，請在 ML Studio 中參閱 [讀取器] 模組的說明主題。

**8. 我可以有多大的資料集？**

ML Studio 最多可支援 10GB 的訓練資料集。Web 服務的資料集大小則沒有限制。此外，也支援在擷取之前透過 Hive 或 SQL 查詢對較大的資料集取樣。如果您要使用大於 10GB 的資料，您可以建立多個資料集，並使用「資料分割和樣本」、「分割」或「聯結」模組將這些資料集重新結合到 ML Studio 中，以建立用來建置預測模型的訓練集。若要深入了解這些模組，請參閱 ML Studio 中的模組說明。

針對大於 2 GB 的資料集，建議的方法是將資料上傳至 Azure 儲存體或 SQL Database (Azure)，或是使用 HDInsight，而不要直接從本機檔案上傳。

**9. 在 ML Studio 中支援哪些現有的 ML 演算法？**

ML Studio 提供最新的 ML 演算法，例如 Scalable Boosted Decision 樹、Bayesian Recommendation 系統、Deep Neural Networks 和 Decision Jungles (由 Microsoft Research 開發)。此外也包含可調整的開放原始碼機器學習封裝，例如 Vowpal Wabbit。ML Studio 支援多類別與二進位分類、迴歸和叢集的機器學習演算法。如需機器學習演算法的完整清單，請參閱 ML Studio 說明。

**10. 我想要的機器學習演算法、資料來源、資料格式、資料轉換作業不在 Azure ML Studio 中，我有哪些選擇？**

您可以瀏覽[使用者意見反應論壇][使用者意見反應論壇]，以檢視我們所追蹤的功能要求。如果您要找的功能已有人要求，請投票給該要求。如果您要找的功能不存在，請建立新要求。您也可以在此論壇中檢視您的要求狀態。我們會密切追蹤此清單，並經常更新功能可用性的狀態。

**11. 是否可將現有的程式碼放入 ML Studio 中？**

ML Studio 現在已支援 R，您可以在 ML Studio 中放入現有的 R 程式碼，並使用 Azure ML 提供的學習器在相同的實驗中加以執行，然後透過 Azure ML 發佈為 Web 服務。要將 R 中的分析資產轉換為企業等級實際執行 Web 服務，使用 Azure ML 是最快速的方式。請參閱 ML Studio 說明主題「R 的擴充性」，以深入了解如何在 ML Studio 中加入您的 R 程式碼和視覺化。

**12. ML Studio 中有哪些可用的 R 封裝？**

ML Studio 目前支援 350 個以上的 R 封裝，且數量仍在持續增加。請參閱 ML Studio 說明主題「R 的擴充性」，以深入了解如何取得支援的 R 封裝清單。如果您要的封裝不在此清單中，請在[使用者意見反映論壇][使用者意見反應論壇]中提供封裝名稱。

### ML API 服務

**13. 何時該以「批次執行服務」的形式執行預測模型？何時該以要求/回應 Web 服務的形式執行？**

「要求回應服務 (RRS)」是低延遲性的高階 Web 服務，可用來為從實驗環境建立並發佈的無狀態模型提供介面。「批次執行服務 (BES)」是可為一批資料記錄進行非同步評分的服務。BES 的輸入類似於 RRS 中使用的資料輸入。主要差異在於，BES 會從多種來源讀取記錄區塊，例如 Blob、Azure 中的資料表、SQL Database (Azure)、HDInsight (Hive 查詢) 和 HTTP 來源。評分的結果會輸出至 Azure Blob 儲存體中的檔案，並且會在回應中傳回儲存體端點。

「批次執行服務」適用於有大量的資料點需要以批次方式評分的情況，或是您有許多資料已採用 Azure 儲存體或 Hadoop 叢集中的檔案格式的情況。Web 服務可將其讀取的資料先行轉換再傳送至模型，讓您可將一週以來的交易資料交由可在轉換後提供結果的批次服務處理。

「要求回應服務」適用於必須以近乎即時的方式進行預測分析，以支援即時儀表板、使用者動作指南，或透過行動或 Web 應用程式提供之內容的情況。

**14. 如何為已部署的服務生產更新模型？**

要為已部署的服務更新預測模型，只需修改並重新執行用來撰寫和儲存已訓練模型的實驗即可。在您有新版的已訓練模型後，ML Studio 會詢問您是否要更新預備 Web 服務。在更新套用至預備 Web 服務後，相同的更新也將可供您套用至實際執行 Web 服務。如需如何更新已部署之 Web 服務的詳細資訊，請參閱 ML Studio 說明主題「更新 Web 服務」。

### 安全性和可用性

**15. 根據預設，哪些人可以存取部署在實際執行環境之 Web 服務的 HTTP 端點？如何限制對此端點的存取？**

在預測模型放入實際執行環境中之後，Azure 入口網站即會列出已部署之 Web 服務的 URL。預備服務 URL 可從 Web 服務區段下的 ML Studio 環境存取，而實際執行服務 URL 可從 Azure 入口網站的 [機器學習] 區段下存取。預備和實際執行 Web 服務的存取金鑰，分別可從 ML Studio 中的 Web 服務儀表板和 Azure 入口網站環境取得。必須要有存取金鑰，才能呼叫實際執行和預備環境中的 Web 服務。

**16. 如何監控部署在實際執行環境中的 Web 服務？**

在預測模型放入實際執行環境中之後，您可以從 Azure 入口網站加以監控。每個已部署的服務都有其本身的儀表板，您可以在此處檢視該服務的監控資訊。

### 支援和訓練

**17. 哪裡可以取得 Azure ML 的訓練？**

[Azure Machine Learning 中心][Azure Machine Learning 中心]包含視訊教學課程和操作方式指南。這些逐步操作方式指南提供了服務的簡介，並且說明匯入資料、清除資料、建置預測模型以及使用 Azure ML 將其部署在實際執行環境中的資料科學生命週期。

視訊教學課程提供 ML Studio 和 ML API 服務的視覺化導覽。視訊教學課程會說明服務的範圍、最常用的資料入口，以及如何清除和處理模組、建置預測模型及部署預測模型。視訊教學課程也將涵蓋工作區佈建以及將預備模型部署至實際執行環境之類的工作。

我們將持續更新機器學習中心的內容。您可以在[使用者意見反應論壇][1]中，提交關於機器學習中心的其他學習內容要求。

**18. 如何取得 Azure ML 的支援？**

Azure ML 的支援含括在 Azure 支援項目中。若要取得 Azure ML 的技術支援，請選取「機器學習」做為服務，我們將為您提供您可以提交支援票證的主題類別。若要深入了解 Azure 支援項目，請瀏覽 <http://azure.microsoft.com/zh-tw/support/options/>

Azure Machine Learning 在 MSDN 上也設有社群論壇，可供您詢問 Azure ML 的相關問題。此論壇由 Azure ML 團隊控管。請瀏覽 [Azure 論壇][Azure 論壇]。

  [開始使用頁面]: http://go.microsoft.com/fwlink/?LinkId=404226
  [Azure Machine Learning 中心]: http://azure.microsoft.com/zh-tw/documentation/services/machine-learning/
  [使用者意見反應論壇]: http://go.microsoft.com/fwlink/?LinkId=404231
  [1]: https://windowsazure.uservoice.com/forums/257792-machine-learning
  [Azure 論壇]: http://social.msdn.microsoft.com/Forums/windowsazure/zh-tw/home?forum=MachineLearning
