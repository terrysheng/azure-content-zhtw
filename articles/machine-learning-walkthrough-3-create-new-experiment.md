<properties title="Step 3: Create a new Azure Machine Learning experiment" pageTitle="Step 3: Create a new Machine Learning experiment | Azure" description="Step 3: Create a new training experiment in Azure Machine Learning Studio" metaKeywords="" services="" solutions="" documentationCenter="" authors="garye" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="garye" />

這是[使用 Azure ML 開發預測方案][使用 Azure ML 開發預測方案]逐步解說的第三個步驟：

1.  [建立 ML 工作區][建立 ML 工作區]
2.  [上傳現有資料][上傳現有資料]
3.  **建立新實驗**
4.  [訓練及評估模型][訓練及評估模型]
5.  [發佈 Web 服務][發佈 Web 服務]
6.  [存取 Web 服務][存取 Web 服務]

------------------------------------------------------------------------

# 步驟 3：建立新的 Azure Machine Learning 實驗

我們需要在 ML Studio 中建立新的實驗，並讓它使用我們上傳的資料集。

1.  在 ML Studio 中，按一下視窗底部的 [+新增]。
2.  選取 [實驗]。
3.  在實驗畫布左側的模組調色盤中，展開 [Saved Datasets]。
4.  找出您已建立的資料集，並將其拖曳到畫布上。您也可以在調色盤上方的 [搜尋] 方塊中輸入名稱，以尋找資料集。

## 準備資料

您可以在資料集的輸出埠上按一下滑鼠右鍵，然後選取 [視覺化]，以檢視資料的前 100 個資料列，以及整個資料集的部分統計資訊。請注意，ML Studio 已識別每個資料行的資料類型。它也會為資料行指定一般標題，因為資料檔案並未隨附資料行標題。

資料行標題並非必要，但這些標題可讓您更容易使用模型中的資料。此外，當我們最終在 Web 服務中發行此模型時，標題將有助於服務的使用者識別資料行。

我們可以使用 [中繼資料編輯器] 模型來新增資料行標題。

1.  在模組調色盤的 [搜尋] 方塊中，輸入 "metadata"。您會在模組清單中看見 [中繼資料編輯器]。
2.  按住 [中繼資料編輯器] 模組並將其拖曳到畫布上，放在資料集下方。
3.  將資料集連接到 [中繼資料編輯器]：按一下資料集的輸出埠、拖曳到 [中繼資料編輯器] 的輸入埠上，然後放開滑鼠按鍵。即使您在畫布上移動資料集或模組，這兩者仍會保持連線。
4.  在畫布右側的 [屬性] 窗格中，按一下 [Launch column selector]。
5.  確認已在下拉式清單中選取 [所有資料行]，然後按一下 [確定]。這會指示 [中繼資料編輯器] 在資料的所有資料行上運作。
6.  針對 [New column name] 參數，輸入資料集中 21 個資料行的名稱清單，並以逗號加以分隔，且依據資料行順序。您可以從 UCI 網站上的資料集文件中取得資料行名稱，或為求簡便，您可以複製並貼上下列內容：

    檢查帳戶的狀態、以月為單位的持續期間、信用記錄、用途、信用額度、存款帳戶/債券、現職年資起始日、自由支配收入的分期付款利率 (百分比)、個人身分和性別、其他債務人、目前居住地起始日、財產、年齡、其他分期付款計劃、住宅、現有信用數目、工作、扶養人數、電話、外國工作者、信用風險

[屬性] 窗格將如下所示：

![Properties for Metadata Editor][Properties for Metadata Editor]

> 秘訣 - 如果您要確認資料行標題，請執行實驗 (按一下實驗畫布下方的 [執行])、以滑鼠右鍵按一下 [中繼資料編輯器] 模組的輸出埠，然後選取 [視覺化]。您可以用相同方式檢視任何模組的輸出，以檢視資料在實驗中的執行進度。

實驗此時看起來應如下所示：

![Adding Metadata Editor][Adding Metadata Editor]

## 建立訓練和測試資料集

實驗的下一個步驟，是產生將用來訓練及測試模型的個別資料集。為此，我們將使用 [分割] 模組。

1.  找出 [分割] 模組、將其拖曳到畫布上，然後連接到最後一個 [中繼資料編輯器] 模組。
2.  根據預設，分割率會是 0.5，並且會設定 [Randomized split] 參數。這表示資料有一半會隨機透過 [分割] 模組的一個連接埠輸出，一半透過另一個連接埠輸出。您可以調整這些參數和 [隨機種子] 參數，以變更訓練與評分資料之間的分割。在此範例中，我們會將其保留為原狀。

    > 秘訣 - 分割率基本上會決定有多少資料會透過左側輸出埠來輸出。例如，如果您將分割率設為 0.7，則會有 70% 的資料透過左側連接埠輸出，30% 透過右側連接埠輸出。

我們可以依自身需求使用 [分割] 模組的輸出，但在範例中，我們要選擇以左側輸出作為訓練資料，右側輸出作為評分資料。

如 UCI 網站上所說明，將高信用風險誤判為低風險的成本，會比將低信用風險誤判為高風險的成本多出 5 倍。為了說明這一點，我們將產生新資料集以反映此成本函數。在此新資料集中，每個高風險範例都會複寫 5 次，而每個低風險範例則不複寫。

我們可以使用 R 程式碼來執行此複寫：

1.  找出 [執行 R 指令碼] 模組並將其拖曳到實驗畫布上，然後連接到 [分割] 模組的左側輸出埠。
2.  在 [屬性] 窗格中，刪除 [指令碼] 參數中的預設文字，然後輸入此指令碼：

        dataset1 <- maml.mapInputPort(1)
        data.set<-dataset1[dataset1[,21]==1,]
        pos<-dataset1[dataset1[,21]==2,]
        for (i in 1:5) data.set<-rbind(data.set,pos)
        maml.mapOutputPort("data.set")

我們必須為 [分割] 模組的每個輸出執行此一相同複寫作業，使訓練和評分資料具有相同的成本調整。

1.  以滑鼠右鍵按一下 [執行 R 指令碼] 模組，然後選取 [複製]。
2.  以滑鼠右鍵按一下實驗畫布，然後選取 [貼上]。
3.  將此 [執行 R 指令碼] 模組連接到 [分割] 模組的右側輸出埠。

> 秘訣 - [執行 R 指令碼] 模組的複本會包含與原始模組相同的指令碼。當您在畫布上複製並貼上模組時，複本將會保留原本的所有屬性。
>
> 實驗此時看起來會如下所示：

![Adding Split module and R scripts][Adding Split module and R scripts]

  [使用 Azure ML 開發預測方案]: ../machine-learning-walkthrough-develop-predictive-solution/
  [建立 ML 工作區]: ../machine-learning-walkthrough-1-create-ml-workspace/
  [上傳現有資料]: ../machine-learning-walkthrough-2-upload-data/
  [訓練及評估模型]: ../machine-learning-walkthrough-4-train-and-evaluate-models/
  [發佈 Web 服務]: ../machine-learning-walkthrough-5-publish-web-service/
  [存取 Web 服務]: ../machine-learning-walkthrough-6-access-web-service/
  [Properties for Metadata Editor]: ./media/machine-learning-walkthrough-3-create-new-experiment/create1.png
  [Adding Metadata Editor]: ./media/machine-learning-walkthrough-3-create-new-experiment/create2.png
  [Adding Split module and R scripts]: ./media/machine-learning-walkthrough-3-create-new-experiment/create3.png
