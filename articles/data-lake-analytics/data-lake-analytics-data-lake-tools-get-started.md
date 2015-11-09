<properties 
   pageTitle="使用適用於 Visual Studio 的資料湖工具開發 U-SQL 指令碼 | Azure" 
   description="了解如何安裝適用於 Visual Studio 的資料湖工具，如何開發和測試 U-SQL 指令碼。" 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="mumian" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="10/28/2015"
   ms.author="jgao"/>

# 教學課程：使用適用於 Visual Studio 的資料湖工具開發 U-SQL 指令碼

[AZURE.INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]


了解如何安裝適用於 Visual Studio 的資料湖工具，和使用適用於 Visual Studio 的資料湖工具來撰寫和測試 U-SQL 指令碼。

U-SQL 是高度可擴充、高度可延伸的語言，用來準備、轉換和分析在資料湖的所有資料。如需詳細資訊，請參閱 [U-SQL 參考](http://go.microsoft.com/fwlink/p/?LinkId=691348)。


**必要條件**

- **已安裝 Visual C++ 的 Visual Studio 2015、Visual Studio 2013 更新 4 或 Visual Studio 2012** 
- **Microsoft Azure SDK for .NET 2.5 版或更新版本**。使用 [Web Platform Installer](http://www.microsoft.com/web/downloads/platform.aspx) 來進行安裝。
- **[適用於 Visual Studio 的資料湖工具](http://aka.ms/adltoolsvs)**。 

    適用於 Visual Studio 的資料湖工具安裝之後，您會在 Visual Studio 中看到資料湖功能表：
    
    ![U-SQL Visual Studio 功能表](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-menu.png)

- **完成[使用 Azure Preview 入口網站開始使用 Azure 資料湖分析](data-lake-analytics-get-started-portal.md)中的下列兩個小節**。

	- [建立 Azure 資料湖分析帳戶](data-lake-analytics-get-started-portal.md#create_adl_analytics_account)。
	- [將 SearchLog.tsv 上傳到預設資料湖儲存體帳戶](data-lake-analytics-get-started-portal.md#update-data-to-the-default-adl-storage-account)。

	資料湖工具不支援建立資料湖分析帳戶。因此您必須使用 Azure Preview 入口網站、Azure PowerShell、.NET SDK 或 Azure CLI 建立帳戶。若要執行資料湖分析工作，您需要一些資料。即使資料湖工具支援上傳資料，您將使用入口網站來上傳範例資料，以方便遵循本教學課程。

## 連接到 Azure

**連接到資料湖分析**

1. 開啟 Visual Studio。
2. 從 [U-SQL] 功能表上，按一下 [選項和設定]。
4. 按一下 [登入]，或者如果已有其他人登入則按一下 [變更使用者]，並遵循指示進行登入。
5. 按一下 [確定] 關閉 [選項和設定] 對話方塊。

**瀏覽您的資料湖分析帳戶**

1. 從 Visual Studio 中，按 **CTRL+ALT+S**，開啟 [伺服器總管]。
2. 從 [伺服器總管] 中，展開 [Azure]，然後展開 [資料湖分析]。如果有資料湖分析帳戶，您就會看到其清單。您無法從 Visual Studio 建立資料湖分析帳戶。若要建立帳戶，請參閱[使用 Azure Preview 入口網站開始使用 Azure 資料湖分析](data-lake-analytics-get-started-portal.md)或[使用 Azure PowerShell 開始使用 Azure 資料湖分析](knoa-get-started-powershell.md)。

## 上傳來源資料檔案

您已在本教學課程中稍早的 [必要條件] 小節上傳一些資料。

萬一您想要使用您自己的資料，以下是從資料湖工具上傳資料的程序。

**將檔案上傳至相依的 Azure 資料湖帳戶**

1. 從 [伺服器總管] 依序展開 [Azure]、[資料湖分析]、您的資料湖分析帳戶、[儲存體帳戶]。您應該會看到預設資料湖儲存體帳戶，和連結的資料湖儲存體帳戶，和連結的 Azure 儲存體帳戶。預設資料湖帳戶具有「預設儲存體帳戶」的標籤。
2. 以滑鼠右鍵按一下預設資料湖儲存體帳戶，然後按一下 [總管]。它會開啟 [適用於 Visual Studio 的資料湖工具總管] 窗格：它會在左邊顯示樹狀檢視，在右邊顯示內容檢視。
3. 瀏覽至您要上傳檔案的資料夾， 
4. 以滑鼠右鍵按一下任何空白區域，然後按一下 [上傳]。 

	![U-SQL Visual Studio 專案 U-SQL](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-upload-files.png)

**將檔案上傳至 Azure Blob 儲存體帳戶**

1. 從 [伺服器總管] 依序展開 [Azure]、[資料湖分析]、您的資料湖分析帳戶、[儲存體帳戶]。您應該會看到預設資料湖儲存體帳戶，和連結的資料湖儲存體帳戶，和連結的 Azure 儲存體帳戶。 
2. 展開 Azure 儲存體帳戶。
3. 以滑鼠右鍵按一下您想要用來上傳檔案的容器，然後按一下 [總管]。
4. 瀏覽至您要上傳檔案的資料夾， 
5. 以滑鼠右鍵按一下任何空白區域，然後按一下 [上傳]。 

## 開發和測試 U-SQL 指令碼 

資料湖分析工作以 U-SQL 語言撰寫。若要深入了解 U-SQL，請參閱[開始使用 U-SQL 語言](data-lake-analytics-u-sql-get-started.md)和 [U-SQL 語言參考](http://go.microsoft.com/fwlink/?LinkId=691348)。

**建立並提交資料湖分析工作**

1. 從 [檔案] 功能表中，按一下 [新增]，再按 [專案]。
2. 選取 [U-SQL 專案] 類型。

	![新的 U-SQL Visual Studio 專案](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-new-project.png)

3. 按一下 [確定]。Visual studio 會建立具有 **Script.usql** 檔案的解決方案。
4. 將下列指令碼輸入到 **Script.usql** 檔案中：

        @searchlog =
            EXTRACT UserId          int,
                    Start           DateTime,
                    Region          string,
                    Query           string,
                    Duration        int?,
                    Urls            string,
                    ClickedUrls     string
            FROM "/Samples/Data/SearchLog.tsv"
            USING Extractors.Tsv();
        
        OUTPUT @searchlog   
            TO "/Output/SearchLog-from-Data-Lake.csv"
        USING Outputters.Csv();

	此 U-SQL 指令碼會使用 **Extractors.Tsv()** 讀取來源資料檔案，然後使用 **Outputters.Csv()** 建立 csv 檔案。
    
    除非您將來源檔案複製到不同的位置，否則請勿修改這兩個路徑。資料湖分析將建立輸出資料夾 (如果沒有的話)。
	
	使用儲存在預設資料湖帳戶中檔案的相對路徑，是比較容易的方法。您也可以使用絕對路徑。例如
    
        adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv
        
    您必須使用絕對路徑來存取連結儲存體帳戶中的檔案。儲存在連結 Azure 儲存體帳戶中的檔案語法為：
    
        wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv

    >[AZURE.NOTE]目前不支援具有公用 Blob 或公用容器存取權的 Azure Blob 容器。

	請注意下列功能：

	- **IntelliSense**
	 
		名稱自動完成和成員會針對資料列集、類別、資料庫、結構描述和使用者定義物件 (UDO) 顯示。
		 
		目錄實體的 IntelliSense (資料庫、結構描述、資料表、UDO 等等) 與您的計算帳戶相關。您可以在頂端工具列中檢查目前作用中的計算帳戶、資料庫和結構描述，並且透過下拉式清單進行切換。
 
	- **自動格式化**
	
		使用者可以根據指令碼結構，在 [編輯] -> [進階] 底下變更範圍指令碼的縮排：
  
		- 格式化文件 (Ctrl+E、D)：格式化整份文件   
		- 格式化選取範圍 (Ctrl+K、Ctrl+F)：格式化選取範圍。如果未進行選取，這個快速鍵會格式化游標所在的行。  
		
		所有格式化規則可在 [工具] -> [選項] -> [文字編輯器] -> [SIP] -> [格式化] 底下進行設定。  
	- **智慧縮排**
	 
		適用於 Visual Studio 的資料湖工具能夠在您撰寫指令碼時自動縮排運算式。這項功能預設會停用，使用者需要透過檢查 [U-SQL] -> [選項和設定] -> [切換] -> [啟用智慧縮排] 來啟用它。

	- **移至定義並尋找所有參考**
	
		以滑鼠右鍵按一下資料列集/參數/資料行/UDO 等的名稱，然後按一下 [移至定義] (F12) 可讓您瀏覽至其定義。按一下 [尋找所有參考] (Shift + F12) 會顯示所有參考。

	- **插入 Azure 路徑**
		
		並非在撰寫指令碼時記住 Azure 檔案路徑並且手動輸入，適用於 Visual Studio 的資料湖工具提供簡單的方法：在編輯器中按一下滑鼠右鍵，按一下 [插入 Azure 路徑]。瀏覽至 Azure Blob 瀏覽器對話方塊中的檔案。按一下 [確定] 按鈕，檔案路徑就會插入您的程式碼。

5. 指定資料湖分析帳戶、資料庫和結構描述：

	![提交 U-SQL Visual Studio 專案](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-submit-job.png)

    如需詳細資訊，請參閱[使用 U-SQL 目錄](data-lake-analytics-use-u-sql-catalog.md)。

5. 從 [方案總管] 中，在 [Script.usql] 上按一下滑鼠右鍵，然後按一下 [建置指令碼]。確認 [輸出] 窗格中的結果。
6. 從 [方案總管] 中，在 [Script.usql] 上按一下滑鼠右鍵，然後按一下 [提交指令碼]。(選擇性) 您也可以從 Script.usql 窗格按一下 [提交]。請參閱上一個螢幕擷取畫面。按一下 [提交] 按鈕旁的向下箭號，使用進階選項提交：
7. 指定 [工作名稱]，確認 [分析帳戶]，然後按一下 [提交]。提交作業完成時，[適用於 Visual Studio 的資料湖工具結果] 視窗中便會出現提交結果和工作連結。

	![提交 U-SQL Visual Studio 專案](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-submit-job-advanced.png)

8. 您必須按一下 [重新整理] 按鈕，以查看最新的工作狀態並且重新整理畫面。當作業成功時，它會為您顯示**作業圖形**、**中繼資料作業**、**狀態記錄**、**診斷**：

	![U SQL Visual Studio 資料湖分析工作效能圖表](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-performance-graph.png)

	* 工作摘要。顯示目前工作的摘要資訊，例如：狀態、進度、執行時間、執行階段名稱、傳送者等。   
	* 工作詳細資料。提供這項工作的詳細資訊，包括指令碼、資源、頂點執行檢視。
	* 工作圖形。提供四個圖形以視覺化呈現工作的資訊：進度、資料讀取、資料寫入、執行時間、每個節點平均執行時間、輸入輸送量、輸出輸送量。
	* 中繼資料作業。它會顯示所有中繼資料作業。
	* 狀態記錄。 
	* 診斷。適用於 Visual Studio 的資料湖工具會自動診斷工作執行。當他們的工作中有一些錯誤或效能問題時您會收到警示。如需詳細資訊，請參閱「工作診斷」(連結 TBD)。 
	
**檢查工作狀態**

1. 從 [伺服器總管] 依序展開 [Azure]、[資料湖分析]、資料湖分析帳戶名稱
2. 按兩下 [工作] 以列出工作。
2. 按一下工作以查看狀態。

**查看工作輸出**

1. 從 [伺服器總管] 依序展開 [Azure]、[資料湖分析]、您的資料湖分析帳戶和 [儲存體帳戶]，以滑鼠右鍵按一下預設的 [資料湖儲存體帳戶]，然後按一下 [總管]。 
2.  按兩下 [輸出] 以開啟資料夾
3.  按兩下 [SearchLog-From-adltools.csv]。


###工作播放 

工作播放可讓您觀看工作執行進度和以視覺化方式偵測出效能異常和瓶頸。這項功能可以在工作完成執行 (亦即在工作正在執行的期間) 之前及完成執行之後使用。工作執行期間進行播放可讓使用者播放截至目前時間為止的進度。

**檢視工作執行進度**

1. 按一下右上角的 [載入設定檔]。請參閱上一個螢幕擷取畫面。
2. 按一下左下角的 [播放] 按鈕以檢閱工作執行進度。 
3. 在播放期間按一下 [暫停] 來停止它或直接將進度列拖曳至特定位置。 


###熱圖 

適用於 Visual Studio 的資料湖工具提供使用者可選取的工作檢視色彩覆疊，以表示每個階段的進度、資料 I/O、執行時間、I/O 輸送量。透過此方法，使用者可以直接且直覺地找出潛在的問題和工作屬性的分佈。您可以選擇要從下拉式清單中顯示的資料來源。



##另請參閱

若要使用不同的工具開始使用資料湖分析，請參閱：

- [使用 Azure Preview 入口網站開始使用資料湖分析](data-lake-analytics-get-started-portal.md)
- [使用 Azure PowerShell 開始使用資料湖分析](data-lake-analytics-get-started-powershell.md)
- [使用 .NET SDK 開始使用資料湖分析](data-lake-analytics-get-started-net-sdk.md)

若要查看更多開發主題：

- [使用資料湖分析來分析 weblog](data-lake-analytics-analyze-weblogs.md)
- [使用適用於 Visual Studio 的資料湖工具開發 U-SQL 指令碼](data-lake-analytics-data-lake-tools-get-started.md)
- [開始使用 Azure 資料湖分析 U-SQL 語言](data-lake-analytics-u-sql-get-started.md)
- [針對資料湖分析工作開發 U-SQL 使用者定義運算子](data-lake-analytics-u-sql-user-defined-operators.md)

<!---HONumber=Nov15_HO1-->