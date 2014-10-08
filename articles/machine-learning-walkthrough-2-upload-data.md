<properties title="Step 2: Upload existing data into an Azure Machine Learning experiment" pageTitle="Step 2: Upload data into a Machine Learning experiment | Azure" description="Step 2: Upload existing public data into Azure Machine Learning Studio" metaKeywords="" services="" solutions="" documentationCenter="" authors="garye" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="garye"></tags>

這是逐步解說的第二個步驟[使用 Azure ML 開發預測方案][]：

1.  [建立 ML 工作區][]
2.  **上傳現有資料**
3.  [建立新實驗][]
4.  [訓練及評估模型][]
5.  [發佈 Web 服務][]
6.  [存取 Web 服務][]

------------------------------------------------------------------------

# 步驟 2：將現有資料上傳至 Azure Machine Learning 實驗中

為了開發信用風險的預測模型，我們將使用 UCI 機器學習儲存機制中的「UCI Statlog (德國信用資料) 資料集」。您可以在下列位置找到此儲存機制：
<http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)>

您可以使用名為 **german.data** 的檔案。將此檔案下載至您的本機硬碟。

此資料集包含過去 1000 名信用申請者的 20 個變數的資料列。這 20 個變數代表資料集的功能向量，分別提供每個信用申請者的識別特性。每個資料列另外會有一個資料行代表申請者的信用風險，其中有 700 名申請者被認定為低信用風險，300 名為高風險。

UCI 網站提供了功能向量的屬性說明，這些屬性包括財務資訊、信用記錄、工作狀態和個人資訊。每個申請者都會有一個二進位評等，指出他們屬於低信用風險還是高風險。

我們將使用這項資料來訓練預測分析模型。完成之後，我們的模型應能夠接受新申請者的資訊，並預測他們屬於低信用風險還是高風險。

以下提供一個有趣的論點。資料集的說明指出，對金融機構而言，將某個實際上屬於高信用風險的人誤判為低信用風險者，會比將低信用風險者誤判為高風險者多耗費 5 倍的成本。要在我們的實驗中將此點納入考量，將代表高信用風險者的項目加倍 ( 5 次)，會是一個簡單的方式。如此，若模型將高信用風險誤判為低風險，模型將會誤判 5 次，每次加倍各一次。這會在訓練結果中增加此誤差的成本。

## 轉換資料集格式

原始資料集使用以空格分隔的格式。ML Studio 在使用逗號分隔 (CSV) 檔案時更能適當運作，因此我們將以逗號取代空格，進行資料集轉換。

我們可以使用下列 PowerShell 命令執行此動作：

    cat german.data | %{$_ -replace " ",","} | sc german.csv  

我們也可以使用 Unix sed 命令執行此動作：

    sed 's/ /,/g' german.data > german.csv  

## 將資料集上傳至 ML Studio

在資料轉換為 CSV 格式後，我們必須將其上傳至 ML Studio 中。

1.  在 ML Studio 中，按一下視窗底部的 [+新增]
2.  選取 [資料集]
3.  選取 [從本機檔案]
4.  在 [上傳新的資料集對話方塊] 中按一下 [瀏覽]，然後尋找您建立的 **german.csv** 檔案
5.  輸入資料集的名稱 - 在此範例中，我們將其命名為「UCI 德國信用卡資料」
6.  針對資料類型，請選取「不具標頭的一般 CSV 檔案 (.nh.csv)」
7.  視需要新增說明
8.  按一下 [確定]  
    ![Upload the dataset][]

這會將資料上傳至我們可在實驗中使用的 [資料集] 模組。

  [使用 Azure ML 開發預測方案]: ../machine-learning-walkthrough-develop-predictive-solution/
  [建立 ML 工作區]: ../machine-learning-walkthrough-1-create-ml-workspace/
  [建立新實驗]: ../machine-learning-walkthrough-3-create-new-experiment/
  [訓練及評估模型]: ../machine-learning-walkthrough-4-train-and-evaluate-models/
  [發佈 Web 服務]: ../machine-learning-walkthrough-5-publish-web-service/
  [存取 Web 服務]: ../machine-learning-walkthrough-6-access-web-service/
  [Upload the dataset]: ./media/machine-learning-walkthrough-2-upload-data/upload1.png
