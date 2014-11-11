<properties title="Azure Machine Learning Sample: Prediction of student performance" pageTitle="Machine Learning Sample: Predict student performance | Azure" description="A sample Azure Machine Learning experiment to develop a model that predicts student performance on tests." metaKeywords="" services="" solutions="" documentationCenter="" authors="garye" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="garye" />

# Azure Machine Learning 範例：預測學習成效

*您可以在 <b>[範例]</b> 索引標籤下的 <b>[實驗]</b> 區段中，找到與 ML Studio 中的這個模型相關聯的範例實驗。實驗名稱為：*

    Sample Experiment - Student Performance - Development

## 資料集和問題說明

在此實驗中，我們的資料集是取自 KDD Cup 2010 的 Algebra\_2008\_2009 訓練集。此資料集可從 [][]<https://pslcdatashop.web.cmu.edu/KDDCup/downloads.jsp></a> 下載取得。資料集中包含教學課程系統的記錄檔。提供的特性包括問題識別碼及其簡短說明、學員識別碼、時間戳記，以及學員以正確方式解出問題之間的嘗試次數。學習問題是為了要預測學員是否可在第一次嘗試時就解出給定的問題。根據 KDD Cup 規則，我們會使用[均方根誤差][均方根誤差] (RMSE) 測量學習演算法的精確性。原始資料集含有八百九十萬筆記錄。為了加快實驗速度，我們將資料集的樣本數降為前十萬個資料列。資料集具有 23 個不同類型的資料行：數值、類別和時間戳記。這些資料行會以定位鍵分隔。

## 開發工作流程

資料集含有無法以 **[套用 R 運算]** 模組處理的非 ASCII 字元。在 Passau 中使用資料集之前，我們使用下列 Powershell 命令移除了非 ASCII 字元：

    gc algebra_2008_2009_train.txt –head 100000 | sc algebra_train_small.txt
    sc tmp.txt -Encoding ASCII -Value (gc algebra_train_small.txt)
    cat tmp.txt | %{$_ -replace "\?\?sqrt","+sqrt"} | sc algebra_train_small.txt_ascii  

產生的檔案 algebra\_train\_small.txt\_ascii 仍然很大，佔用了 36M。我們將此檔案儲存到 Azure Blob 儲存體中，然後使用 **[讀取器]** 模組將檔案載入實驗中。在 Blob 儲存體中儲存檔案的 Powershell 命令為：

    Add-AzureAccount
    $key = Get-AzureStorageKey -StorageAccountName <your storage account name>
    $ctxt = New-AzureStorageContext -StorageAccountName $key.StorageAccountName -StorageAccountKey $key.Primary
    Set-AzureStorageBlobContent –Container <container name in your storage account> -File "algebra_train_small.txt_ascii" –Context $ctxt

![][0]

**[讀取器]** 模組的參數顯示如上。在此範例中，儲存體帳戶名稱為 “datascience”，而資料集檔案 algebra\_train\_small.txt\_ascii 放置在容器 “sampleexperiments” 中。帳戶金鑰是 Azure 儲存體帳戶的存取金鑰。您可以使用 Azure 管理入口網站 ([][1]<https://manage.windowsazure.com></a>) 從您的帳戶擷取此金鑰。

![][2]

在後續步驟中 (顯示如上)，我們會進行一些轉換，使資料集成為與我們的學習演算法相符的格式。我們會使用 **[中繼資料編輯器]** 將時間戳記資料行「首次交易時間」轉換為字串格式。要產生分割的訓練/測試，就必須執行此轉換。接著，我們會使用 **[專案資料行]** 移除多個在實驗中不會用到的資料行。我們會使用「遺漏值清除程式」將所有的遺漏值取代為 0。在下一個步驟中，我們會將「單元名稱、區段名稱」資料行分割為「單元名稱」、「區段名稱」這兩個資料行。此分割可使用 **[執行 R]** 模組中的下列 R 程式碼來完成：

    dataset <- maml.mapInputPort(1)
    b <- data.frame(do.call(rbind,strsplit(as.vector(dataset[,3]),",")))
    names(b) <- c("Unit Name","Section Name")
    data.set <- cbind(dataset[,1:2],b,dataset[,4:15])
    maml.mapOutputPort("data.set")  

實驗的前幾個步驟包括載入資料和初始轉換，如前所述。在完成資料的傳訊後，我們將實驗分成 4 個分支。在每個分支中，我們會分別測試一個不同的特性集。有些特性集先前由 [1][1] 所使用。在第一個分支中 (說明如下)，我們的特性集包含「學員識別碼」、「單元  

![][3]

![][4]
 
 
名稱」、「區段名稱」、「問題名稱」、「問題檢視」和「機會」等特性，以及問題的「文字說明」。非整數特性會顯示為類別特性。在此分支中，首先我們會使用 **[專案資料行]** 移除 [提示] 資料行，因為在預測新學員是否成功時無法使用 [提示] 資料行。接著，我們使用 **[分割]** 模組將資料集分割為訓練集和測試集。由於所有範例皆有時間戳記，因此我們的分割將會以時間為準。[首次交易時間] 為 2008 年的所有資料列都會放在訓練集中，2009 年的資料列則放在測試集中。**[分割]** 模組的參數會顯示在右側。完成分割後，我們會使用促進式決策樹產生二進位分類模型，並為測試資料評分。**[訓練模型]** 模組中的標籤資料行為 [Correct First Attempt]。

![][5]

在 **[套用數學運算]** 和 **[Elementary Statistics]** 模組的下一個序列中 (顯示如上)，我們會使用原始評分和實際標籤來計算模型的 RMSE。請注意，針對迴歸模型，此度量會由 **[評估工具]** 模組來計算。但對於其他模型，則必須以手動方式計算此度量。在第一個 **[套用數學運算]** 模組中，我們會計算標籤資料行與 **[評分模型]** 模組所產生的評分資料行之間的差距。在第二個 **[套用數學運算]** 模組中，我們會計算此差距的平方。接著由 **[Elementary Statistics]** 模組計算差距平方的平均值。最後，會使用最後一個 **[套用數學運算]** 模組計算平方根。這四個模組的參數顯示如下。

![][6]

![][7]

![][8]

![][9]

在第二個分支中 (顯示如上)，我們同樣採用時間維度。除了第一個分支中的特性以外，我們還使用目前使用者解出之問題的最後兩個步驟的名稱，及其說明。在提供的資料集中，所有使用者的活動都會依時間戳記的遞增順序儲存。此外，在提供的資料集中，使用者的活動並不會交錯，也就是說，一開始全都會是第一個使用者的資料列，接著全都會是第二個使用者的資料列，依此類推。因此，若要尋找使用者的最後幾個步驟，我們必須使用 RowID 資料行。對於固定使用者，此資料行會作為時間軸。我們使用 [套用數學運算]\*\*\*\* 在此資料行中加上 1，將每一個資料列向前推移一個時間單位。接著，我們使用 [聯結] 模組，聯結原始資料集和以 RowID、StudentID、ProblemName 作為索引鍵的推移資料集。結果會產生擴充的資料集，且其中的每個資料列都含有相同的 StudentID 和 ProblemName 在 t 與 t-1 這兩個時間點的記錄。我們使用*左方外部聯結*，保存未包含具有相同 StudentID 和 ProblemID 之先前步驟的資料列。我們再次套用 **[套用數學運算]** 與 **[聯結]** 的組合，使特性回到兩個步驟前。**[套用數學運算]** 和 **[聯結]** 的確切參數顯示如下。

![][10]

![][11]

![][12]

執行兩個步驟後，會產生多個相同的資料行。例如，由於我們使用了 [聯結] 模組，ProblemName 資料行會針對步驟 t、t-1 和 t-2 複寫 3 次。我們使用 **[專案資料行]** 模組移除多餘的資料行。最後，由於我們使用了 *[左方外部聯結]*，因此聯結運算子所產生的部分資料列可能會有遺漏值。我們使用 **[遺漏值清除程式]**，將所有的遺漏值取代為 “0” 字串。**[遺漏值清除程式]** 的參數顯示如上。

在計算新的特性集後，第二個分支的工作流程會與第一個分支的工作流程完全相同。

![][13]

在第三個分支中，除了第二個分支中所使用的特性以外，我們也使用與第一個分支中的原始特性鄰接的二次方和三次方特性。二次方和三次方特性可使用 **[執行 R 運算]** 模組與 R 程式碼來計算，顯示如上。在計算新的特性集後，我們繼續以前兩個分支中的相同方式進行訓練、評分和評估。

在第四個分支中，我們使用與前三個分支中完全不同的特性。對於每個 StudentID，我們會計算時間 t 之前 (但不含此時間點) 之 [Correct First Attempt] 的平均值。我們將此值表示為 CFA(StudentID,t)。同理，我們以 Hints(StudentID,t) 表示固定 StudentID 在時間 t 之前 (但不含此時間點) 的提示平均值。為了加快這些平均值的計算速度，我們不將整個資料集納入考量，而僅計算時間 t 之前的 10 筆最新記錄。CFA(Problem Name,t)、CFA(Step Name,t)、CFA(Description,t)、Hints(Problem Name,t)、Hints(Step Name,t) 和 Hints(Description,t) 具有類似的定義。假設範例 x 的首次交易時間=t(x)，且具有 StudentID(x)、Problem Name(x)、Step Name(x) 和 Description(x) 等資料行值，則我們會產生前述 8 個 CFA 和提示特性：

    CFA(StudentID(x),t(x)), CFA(Problem Name(x),t(x)), CFA(Step Name(x),t(x)), CFA(Description(x),t(x)), 
    Hints(StudentID(x),t(x)), Hints(Problem Name(x),t(x)), Hints(Step Name(x),t(x)), Hints(Description(x),t(x))  

![][14]

同樣地，我們也會使用 StudentID 和「問題名稱」、「問題名稱」和「步驟名稱」、StudentId 和 UnitName、StudentID 和「問題說明」的鄰接，計算 8 個附加的 CFA 和提示特性。我們總共會產生 16 個特性，用以預測 [Correct First Attempt] 資料行的值。這 16 項特性可使用 **[執行 R 運算]** 模組內的 R 程式碼來計算。此程式碼既長又繁複，但已高度最佳化。在計算這些特性後，我們會移除由 R 程式碼新增的某些輔助資料行。為此，我們將使用 **[專案資料行]** 模組。第四個分支中的特性計算完整工作流程顯示如上。在計算新的特性集後，我們繼續以前三個分支中的相同方式進行訓練、評分和評估。

四個分支中皆完成 RMSE 值的計算後，我們會使用 **[新增資料列]** 模組來收集結果。此外，我們會使用 **[執行 R]** 模組產生註解。以下說明此一實驗最終部分的工作流程。

![][15]

實驗的最終輸出是下列資料表，第一個資料行是特性集的名稱，第二個資料列是測試範例中測量出來的 RSME：

![][16]

結論是，第四個特性集所產生的 RMSE 最低。

## 參考

H.-F. Yu 等人。Feature Engineering and Classifier Ensemble for KDD Cup 2010. KDD Cup 2010 Workshop, 2010。

  [0]: https://pslcdatashop.web.cmu.edu/KDDCup/downloads.jsp
  [均方根誤差]: http://en.wikipedia.org/wiki/Root-mean-square_deviation
  [0]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-1.jpg
  [1]: https://manage.windowsazure.com
  [2]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-2.jpg
  [3]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-3.jpg
  [4]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-4.jpg
  [5]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-5.jpg
  [6]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-6.jpg
  [7]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-7.jpg
  [8]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-8.jpg
  [9]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-9.jpg
  [10]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-10.jpg
  [11]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-11.jpg
  [12]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-12.jpg
  [13]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-13.jpg
  [14]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-14.jpg
  [15]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-15.jpg
  [16]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-16.jpg
