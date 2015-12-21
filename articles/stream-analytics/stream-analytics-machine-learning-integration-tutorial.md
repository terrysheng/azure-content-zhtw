<properties 
	pageTitle="教學課程：Azure 串流分析和 Azure Machine Learning 整合 | Microsoft Azure" 
	description="如何在串流分析作業中利用 UDF 和機器學習服務"
	keywords=""
	documentationCenter=""
	services="stream-analytics"
	authors="jeffstokes72" 
	manager="paulettm" 
	editor="cgronlun"
/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="12/10/2015" 
	ms.author="jeffstok"
/>

# 教學課程：串流分析和機器學習服務整合簡介 #

本教學課程旨在協助您透過機器學習服務整合，快速設定簡單的串流分析作業。我們將利用 Cortana 分析資源庫的情緒分析機器學習服務模型，來分析串流文字資料並即時判斷情緒分數。本教學課程很適合用來了解下列各種案例，例如對串流 twitter 資料進行即時情緒分析、由支援人員分析客戶聊天記錄、論壇/部落格/影片上的評論，以及其他許多即時預測評分案例。
  
本教學課程提供附有文字的範例 CSV 檔 (如下圖 1 所示) 做為 Azure Blob 存放區的輸入。作業時會將情緒分析模型做為使用者定義的函式 (UDF) 套用到 Blob 存放區中的範例文字資料。最終結果則會放置在相同 Azure Blob 存放區中的另一個 CSV 檔中。下圖 2 提供此設定的圖表。如需更真實的案例，可將此 Blob 存放區的輸入換成 Azure 事件中樞輸入內的串流 twitter 資料。此外，也可針對彙總情緒建置 [Power BI](https://powerbi.microsoft.com/) 即時視覺效果。本文日後重新編寫時將會包含這類擴充功能。

圖 1：

![串流分析機器學習服務教學課程圖 1](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-1.png)

圖 2：

![串流分析機器學習服務教學課程圖 2](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-2.png)

## 先決條件

本文的先決條件如下：

1.	有效的 Azure 訂用帳戶
2.	內附資料的 CSV 檔。這裡有圖 2 中的 CSV 檔可供下載，或者您也可以自行建立。本教學課程在編寫時是假設您會使用可供下載的 CSV 檔。

概括而言，我們將會執行下列步驟：

1.	將 CSV 輸入檔案上傳至 Blob 儲存體
2.	將 Cortana 分析資源庫的情緒分析模型加入到機器學習服務工作區
3.	將此模型部署為 Azure Machine Learning 工作區中的 Web 服務
4.	建立串流分析作業，以函式形式呼叫此 Web 服務來判斷所輸入文字的情緒。
5.	啟動串流分析作業並觀察輸出 


## 將 CSV 輸入檔上傳至 Blob 儲存體

在此步驟中，您可以使用包括簡介中所指定的檔案在內的任何 CSV 檔。若要上傳檔案，可使用 [Azure 儲存體總管](http://storageexplorer.com/)或 Visual Studio 以及自訂程式碼。本教學課程所提供的是適用於 Visual Studio 的範例。

1.	展開 [Azure]，並以滑鼠右鍵按一下 [儲存體]。選擇 [附加外部儲存體]，並提供 [帳戶名稱] 和 [帳戶金鑰]。  

    ![串流分析機器學習服務教學課程伺服器總管](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-server-explorer.png)

2.	展開剛才附加的儲存體並選擇 [建立 Blob 容器]，然後提供邏輯名稱。對建立好的容器按兩下以檢視其內容 (此時會是空的)。

    ![串流分析機器學習服務教學課程建立 Blob](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-create-blob.png)

3.	按一下 [上傳 Blob] 圖示以上傳 CSV 檔，然後選擇 [本機磁碟中的檔案]。

## 新增 Cortana 分析資源庫中的情緒分析模型

1.	下載 Cortana 分析資源庫中的[預測情緒分析模型](https://gallery.cortanaanalytics.com/Experiment/Predictive-Mini-Twitter-sentiment-analysis-Experiment-1)。  
2.	按一下 [在 Studio 中開啟]：  

    ![串流分析機器學習服務教學課程開啟機器學習服務 Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-open-ml-studio.png)

3.	登入以前往工作區。選擇最適合您所在地的位置。
4.	現在按一下 Studio 底部的 [執行]  
5.	一旦順利執行，請按一下 [部署 Web 服務]。
6.	現在情緒分析模型已可供使用。若要驗證，請按一下 [測試] 按鈕，並提供文字輸入 (例如 "I love Microsoft")，然後測試作業應該就會傳回類似下面的結果：

`'Predictive Mini Twitter sentiment analysis Experiment' test returned ["4","0.715057671070099"]...`

![串流分析機器學習服務教學課程分析資料](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-analysis-data.png)

按一下 [Excel 2010 或較舊版本] 活頁簿連結，以取得稍後要用來設定串流分析作業的 API 金鑰和 URL (如果要利用另一個 Azure 帳戶之工作區中的機器學習服務模型，才必須執行此步驟。本教學課程假設這正是我們為了解決此案例而必須執行的動作)。

![串流分析機器學習服務教學課程分析實驗](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-analysis-experiement.png)

記下所下載 Excel 檔中的 Web 服務 URL 和存取金鑰，如下所示：

![串流分析機器學習服務教學課程快速概覽](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-quick-glance.png)

## 建立使用機器學習服務模型的串流分析作業

1.	瀏覽至 [Azure 管理入口網站](https://manage.windowsazure.com)。  
2.	依序按一下 [新增]、[資料服務]、[串流分析] 和 [快速建立]。提供 [作業名稱] 和適當的作業 [區域]，然後選擇 [區域監視儲存體帳戶]。    
3.	作業建立好之後，瀏覽至 [輸入] 索引標籤，然後按一下 [加入輸入]。  

    ![串流分析機器學習服務教學課程資料新增機器學習服務輸入](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-add-input-screen.png)

4.	在 [加入輸入] 精靈視窗的第一頁上，選取 [資料串流] 並按 [下一步]。在第二頁上選取 [Blob 儲存體] 做為輸入，然後按 [下一步]。
5.	在精靈的 [Blob 儲存體設定] 頁面上，提供先前上傳資料時所定義的儲存體帳戶 Blob 容器名稱。按 [下一步]。選擇 [CSV] 做為 [事件序列化格式]。接受 [序列化設定] 的其餘預設值。按一下 [確定]。  
6.	瀏覽至 [輸出] 索引標籤，然後按一下 [新增輸出]。  

    ![串流分析機器學習服務教學課程新增輸出](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-add-output-screen.png)

7.	選擇 [Blob 儲存體]，並提供相同的參數 (但容器除外)。我們先前已設定為要從 **CSV** 檔所上傳到的 "test" 容器中讀取**輸入**。至於**輸出**，則指定為 “testoutput”。容器名稱必須不同，並請確認此容器存在。
8.	按 [下一步] 以設定輸出的 [序列化設定]。和在輸入時一樣，選擇 [CSV] 並按一下 [確定] 按鈕。
9.	瀏覽至 [函式] 索引標籤，然後按一下 [新增機器學習服務函式]。  

    ![串流分析機器學習服務教學課程新增機器學習服務函式](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-add-ml-function.png)

10.	在 [機器學習服務 Web 服務設定] 頁面上，找到機器學習服務工作區、Web 服務和預設端點。在本教學課程中，如果您知道 URL 而且擁有金鑰，請手動套用設定，以便熟悉工作區 Web 服務的設定方式。提供端點的 [URL] 和 [API 金鑰]。然後按一下 [確定]。

    ![串流分析機器學習服務教學課程機器學習服務 Web 服務](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-ml-web-service.png)

11.	瀏覽至 [查詢] 索引標籤，並如下所示修改查詢：

    ![串流分析機器學習服務教學課程機器學習服務查詢](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-ml-query.png)

12. 按一下 [儲存] 以儲存查詢。

## 啟動串流分析作業並觀察輸出

1.	按一下作業底部的 [啟動]。 
2.	在 [開始查詢對話方塊] 上，選擇 [自訂時間]，然後選取將 CSV 上傳至 Blob 儲存體之前的時間。按一下 [確定]。  

    ![串流分析機器學習服務教學課程自訂時間](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-custom-time.png)

3.	使用上傳 CSV 檔時所使用的工具瀏覽至 Blob 儲存體。本教學課程使用 Visual Studio。
4.	在作業啟動後經過幾分鐘的時間，就會建立輸出容器並於其中上傳 CSV 檔。  
5.	按兩下該檔案將會開啟預設的 CSV 編輯器，並且應該會顯示如下內容：  

    ![串流分析機器學習服務教學課程 csv 檢視](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-csv-view.png)

## 結論

本教學課程建立了串流分析作業，此作業會讀取串流文字資料，並對其套用即時情緒分析。您不必擔心麻煩的情緒分析模型建立工作，就能完成全部的工作。這是 Cortana 分析套件的其中一項優點。

您也能觀察 Azure 機器語言函式的相關度量。按一下 [監視] 索引標籤。此時會出現三個函式的相關度量。
  
- 「函式要求」指出機器學習服務 Web 服務的要求數目。  
- 「函式事件」指出要求中的事件數目：依預設，每個 ML Web 服務的要求可包含多達 1000 個事件。  
- 「失敗的函式要求」指出機器學習服務 Web 服務的失敗要求數目。  

    ![串流分析機器學習服務教學課程 ml 監視檢視](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-ml-monitor-view.png)

<!-------HONumber=AcomDC_1210_2015--->