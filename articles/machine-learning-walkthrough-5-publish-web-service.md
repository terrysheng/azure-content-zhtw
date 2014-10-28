<properties title="Step 5: Publish the Azure Machine Learning web service" pageTitle="Step 5: Publish the Machine Learning web service | Azure" description="Step 5: Publish a scoring experiment in Azure Machine Learning Studio as an ML API web service" metaKeywords="" services="" solutions="" documentationCenter="" authors="garye" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="garye"></tags>

這是逐步解說[使用 Azure ML 開發預測方案][使用 Azure ML 開發預測方案]的第五個步驟：

1.  [建立 ML 工作區][建立 ML 工作區]
2.  [上傳現有資料][上傳現有資料]
3.  [建立新實驗][建立新實驗]
4.  [訓練及評估模型][訓練及評估模型]
5.  **發佈 Web 服務**
6.  [存取 Web 服務][存取 Web 服務]

------------------------------------------------------------------------

# 步驟 5：發行 Azure 機器學習 Web 服務

為了讓其他人可以使用此預測模型，我們將它發行到 Azure 上當作 Web 服務。使用者將可以傳送一組信用貸款申請資料給此服務，而服務會傳回信用風險的預測。

為了這樣做，我們需要：

-   將實驗準備好，以便發行
-   將它發行到預備伺服器，以便測試
-   準備妥當時，將它提升至即時伺服器

在繼續進行之前，您應該建立此實驗的複本來編輯。如此，當您您需要進一步調整模型時，隨時都可以回到訓練實驗。

1.  按一下畫布下方的 **[另存新檔]**。
2.  以實用的名稱來命名實驗複本。預設會在原始實驗名稱後面加上「- 複製」。在我們的案例中，假設命名為「信用風險預測 - 評分實驗」。
3.  按一下 **[確定]**。

現在，您可以看到原始實驗和複本都列在 ML Studio 的 [實驗] 清單中。

![Experiments list][Experiments list]

## 準備評分實驗

我們需要做兩件事，才能準備好將模型發行為 Web 服務。

首先，我們需要將實驗從「*訓練實驗」*轉換成*「評分實驗」*。目前為止，我一直在試驗訓練我們的模型。但發行後的服務不會再進行訓練 - 而是會給使用者的輸入評分。因此，我們要儲存已訓練的模型的複本，然後刪除實驗中專用於訓練的所有元件。

其次，Azure ML Web 服務將會接受使用者的輸入並傳回結果，所以我們需要在實驗中識別這些輸入和輸出點。

### 從訓練實驗轉換成評分實驗

假設我們已判斷出推進式樹狀模型是較適合使用的模型。所以，第一件事就是移除 SVM 訓練模組。

1.  刪除 **[Two-Class Support Vector Machine]**
2.  刪除連接到此模組的 **[訓練模型]**和 **[評分模型]** 模組
3.  刪除 **[Transform Data By Scaling]** 模組

現在我們要儲存已訓練的推進式樹狀模型。然後，我們可以從實驗中移除用於訓練的剩餘模組，換成已訓練的模型。

1.  以滑鼠右鍵按一下剩餘 **[訓練模型]** 模組的輸出埠，並選取 **[Save as Trained Model]**。
2.  輸入已訓練模型的名稱和描述。在此範例中，我們將它命名為「信用風險預測」。

    > **注意**：儲存此訓練過的模型之後，它會出現在模組選用區中，可供其他實驗中使用。

3.  在 **[搜尋]** 方塊中輸入「信用風險」，從模組選用區中找出此模型，然後將 **[信用風險預測]** 訓練模型拖曳到實驗畫布上。
4.  刪除 **[Two-Class Boosted Decision Tree]** 和 **[訓練模型]** 模組。
5.  將 **[信用風險預測]** 模型的輸出連接到 **[評分模型]** 模組的左側輸入。

現在，我們在實驗中有已儲存受過訓練的模型版本，可取代原始訓練模組。

實驗中還有我們為了訓練和評估兩個模型演算法而特別加入的其他元件。我們也可以移除它們：

-   **分割**
-   兩個 **[Execute R Script]** 模組
-   **評估模型**

還有一件事：原始信用卡資料包含 [信用風險] 資料行。我們將此資料行傳入 **[訓練模型]** 模組中，以便訓練模型來預測這些值。但現在已訓練過模型，我們不想再繼續傳遞該資料行 - 已訓練的模型會預測此值。為了從資料流程中移除該資料行，我們使用 **[Project Column]** 模組。

1.  找出 **[Project Column]** 模組並拖曳到畫布上。
2.  將此模組連接到 **[中繼資料編輯器]** 模組的輸出 (現在已移除 **[分割]** 和 **[Execute R Script]** 模組)
3.  選取 **[Project Columns]** 模組，按一下 **[Launch column selector]**。
4.  保留下拉式清單中的 [所有資料行]。
5.  按一下加號 (+)，立即建立新的下拉式清單。
6.  在此新的下拉式清單中，選取 [Exclude column names]，然後在文字欄位中輸入「信用風險」(您也可以使用資料行編號 21 來指定此資料行)。
7.  按一下 **[確定]**。

    > 要訣 - 資料行選取器遵循下拉式清單的顯示順序邏輯。在此案例中，我們指示 **[Project Columns]** 模組「通過所有資料行，但 [信用風險] 資料行除外」。如果我們離開第一個下拉式清單，則模組完全不會通過任何資料行。

8.  將 **[Project Columns]** 模組的輸出連接到 **[評分模型]** 模組的右側輸入。

實驗現在看起來如下：

![Scoring the trained model][Scoring the trained model]

### 選取服務輸入和輸出

在原始模型中，要評分的資料已傳入 **[評分模型]** 模組的右側輸入埠 ([資料集])，而評分結果出現在輸出埠 ("Scored Dataset")。當服務執行時，我們希望使用者的資料和結果使用這些相同的埠。

1.  以滑鼠右鍵按一下 **[評分模型]** 模組的右側輸入埠，然後選取 **[Set as Publish Input]**。使用者的資料必須包括特性向量的所有資料。

    > **要訣** - 在將使用者的資料傳給評分模組之前，如果您必須對資料執行任何操作 (就像我們使用 **[Transform Data By Scaling]** 模組來準備 SVM 模型的資料一樣)，請將模組保留在 Web 服務中，並將服務輸入設為該模組的輸入埠。

2.  以滑鼠右鍵按一下輸出埠並選取 **[Set as Publish Output]**。服務將傳回 [評分模型] 模組的輸出。其中包括特性向量，以及信用風險預測和評分機率值。

    > **要訣** - 如果希望 Web 服務只傳回此資料的一部分 (例如，您不想要傳回整個特性向量)，您可以在 **[評分模型]** 模組後面加入 **[Project Columns]** 模組，並設定它排除您不要的資料行，然後將 **[Project Columns]** 模組的輸出設定成 Web 服務輸出。

> **附註** - 您可能驚訝我們為什麼要任由「UCI 德國信用卡資料」資料集及其相關聯的模組連接到 **[評分模型]** 模組。服務使用的是使用者的資料，而不是原始資料集，為什麼放任它們繼續連接？

服務的確不需要原始信用卡資料。但卻需要該資料的結構描述，例如，有多少個資料行及哪些資料行是數值等資訊。需要此結構描述資訊才能解譯使用者的資料。我們保持連接這些元件，以便服務執行時，評分模型才會有資料集結構描述。不會使用資料，只是使用結構描述。

最後一次執行實驗 (按一下 **[執行]**)。如果要驗證模型仍然有效，請以滑鼠右鍵按一下 **[評分模型]** 模組，並選取 **[視覺化]**。您會看到原始資料出現，也會看到信用風險值 ("Scored Labels") 和評分機率值 ("Scored Probabilities").

## 發佈 Web 服務

若要發行從實驗中導出的 Web 服務，請按一下畫布下方的 **[PUBLISH WEB SERVICE]**，並於提示時按一下 **[是]**。ML Studio 會將實驗當做 Web 服務發行到 ML 預備伺服器上，並帶您前往服務儀表板。

> **要訣** - 發行 Web 服務之後，您可以更新它。例如，如果想要變更模型，請編輯您稍早儲存的訓練實驗，調整模型參數，然後儲存訓練的模型 (覆寫您先前儲存的模型)。重新開啟評分實驗時，將出現通知指出內容已變更 (就是您已訓練的模型)，而您可以更新實驗。重新發行實驗時，將會取代 Web 服務 (現在是使用已更新的模型)。

您可以按一下 **[設定]** 索引標籤來設定服務。您可以在這裡修改服務名稱 (預設會指定實驗名稱) 並輸入描述。您也可以為輸入和輸出資料行指定更好記的標籤。

我們稍後再處理 **[READY FOR PRODUCTION?]** 開關。

## 測試 Web 服務

在 **[儀表板]** 頁面上，按一下 **[Staging Services]** 下的 **[測試]** 連結。將會顯示對話方塊來要求您提供服務的輸入資料。這些就是在原始的德國信用風險資料集中出現的資料行。

輸入一組資料，然後按一下 **[確定]**。

Web 服務產生的結果會顯示在儀表板底部。我們如何設定服務，評分模型就會產生什麼結果。

## 將 Web 服務提升至即時伺服器

目前為止，服務一直都在 ML 預備伺服器上執行。準備讓它上線運作時，您可以要求將它提升至即時伺服器。

在 **[設定]** 索引標籤上，按一下 **[READY FOR PRODUCTION?]** 旁邊的 [是]這樣會傳送通知給 IT 管理員，表示此 Web 服務已準備好上線運作。接著，管理員就可以將它提升至即時伺服器。

![Promoting the service to the live environment][Promoting the service to the live environment]

------------------------------------------------------------------------

**接著：[存取 Web 服務][存取 Web 服務]**

  [使用 Azure ML 開發預測方案]: ../machine-learning-walkthrough-develop-predictive-solution/
  [建立 ML 工作區]: ../machine-learning-walkthrough-1-create-ml-workspace/
  [上傳現有資料]: ../machine-learning-walkthrough-2-upload-data/
  [建立新實驗]: ../machine-learning-walkthrough-3-create-new-experiment/
  [訓練及評估模型]: ../machine-learning-walkthrough-4-train-and-evaluate-models/
  [存取 Web 服務]: ../machine-learning-walkthrough-6-access-web-service/
  [Experiments list]: ./media/machine-learning-walkthrough-5-publish-web-service/publish1.png
  [Scoring the trained model]: ./media/machine-learning-walkthrough-5-publish-web-service/publish2.png
  [Promoting the service to the live environment]: ./media/machine-learning-walkthrough-5-publish-web-service/publish3.png
