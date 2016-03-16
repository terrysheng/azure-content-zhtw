<properties
	pageTitle="用以檢視 JSON 的 DocumentDB Document Explorer | Microsoft Azure"
	description="深入了解 DocumentDB Document Explorer，它是一種 Azure 入口網站的工具，可用來檢視、編輯、建立和上傳使用 DocumentDB 的 JSON 文件、NoSQL 文件資料庫。"
    keywords="檢視 json"
	services="documentdb"
	authors="AndrewHoh"
	manager="jhubbard"
	editor="monicar"
	documentationCenter=""/>

<tags
	ms.service="documentdb"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="02/23/2016"
	ms.author="anhoh"/>

# 使用 DocumentDB Document Explorer 檢視、編輯、建立和上傳 JSON 文件

本篇文章提供 [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) Document Explorer 的概觀，它是一種 Azure 入口網站的工具，可讓您檢視、編輯、建立、上傳和篩選使用 DocumentDB 的 JSON 文件。

## 啟動 Document Explorer

1. 在 Azure 入口網站的動態工具列中，按一下 [DocumentDB 帳戶]。如果看不到 [DocumentDB 帳戶]，請按一下 [瀏覽]，然後按一下 [DocumentDB 帳戶]。

2. 在 [DocumentDB 帳戶] 刀鋒視窗的頂端按一下 [Document Explorer]。
 
	![[Document Explorer] 命令的螢幕擷取畫面](./media/documentdb-view-JSON-document-explorer/documentexplorercommand.png)

 	>[AZURE.NOTE] [查詢總管] 也會出現在資料庫和集合刀鋒視窗。

    在 [Document Explorer] 刀鋒視窗中，會根據您在其中啟動 [查詢總管] 的內容預先填入 [資料庫] 和 [集合] 下拉式清單。

	![[Document Explorer] 刀鋒視窗的螢幕擷取畫面](./media/documentdb-view-JSON-document-explorer/documentexplorerinitial.png)

## 建立文件

1. [啟動 Document Explorer](#launch-document-explorer)。

2. 在 [Document Explorer] 刀鋒視窗中，按一下 [建立文件]。

    [文件] 刀鋒視窗中會提供少量的 JSON 程式碼片段。

	![[Document Explorer] 建立文件體驗的螢幕擷取畫面，您可以在其中檢視 JSON 和編輯 JSON](./media/documentdb-view-JSON-document-explorer/createdocument.png)

2. 在 [文件] 刀鋒視窗中，輸入或貼上您想要建立的 JSON 文件內容，然後按一下 [儲存]，將您的文件認可至在 [Document Explorer] 刀鋒視窗中指定的資料庫和集合。

	![[Document Explorer] 儲存命令的螢幕擷取畫面](./media/documentdb-view-JSON-document-explorer/savedocument1.png)

	> [AZURE.NOTE] 如果您未提供 "id" 屬性，則 Document Explorer 會自動新增 id 屬性，並產生一個 GUID 做為識別碼值。

    如果您已經有來自 JSON 檔案、MongoDB、SQL Server、CSV 檔案、Azure 資料表儲存體、Amazon DynamoDB、HBase，或其他 DocumentDB 集合的資料，即可使用 DocumentDB 的[資料移轉工具](documentdb-import-data.md)快速匯入資料。

## 編輯文件

1. [啟動 Document Explorer](#launch-document-explorer)。

2. 若要編輯現有的文件，請在 [Document Explorer] 刀鋒視窗中加以選取，在 [文件] 刀鋒視窗中編輯該文件，然後按一下 [儲存]。

    ![用來檢視 JSON 之 [Document Explorer] 編輯文件功能的螢幕擷取畫面](./media/documentdb-view-JSON-document-explorer/editdocument.png)

    如果您正在編輯文件，並決定捨棄目前這組編輯，只需按一下 [文件] 刀鋒視窗中的 [捨棄]，確認捨棄動作，便會重新載入文件先前的狀態。

    ![[Document Explorer] 捨棄命令的螢幕擷取畫面](./media/documentdb-view-JSON-document-explorer/discardedit.png)

## 刪除文件

1. [啟動 Document Explorer](#launch-document-explorer)。

2. 在 [Document Explorer] 中選取文件，按一下 [刪除]，然後確認刪除。確認後，系統會立即將文件從 Document Explorer 清單中移除。

	![[Document Explorer] 刪除命令的螢幕擷取畫面](./media/documentdb-view-JSON-document-explorer/deletedocument.png)

## 使用 JSON 文件

Document Explorer 會驗證任何新的或已編輯文件是否包含有效 JSON。您甚至可藉由移至不正確的區段來檢視 JSON 錯誤，以取得有關驗證錯誤的詳細資訊。

![JSON 反白顯示無效之 [Document Explorer] 的螢幕擷取畫面](./media/documentdb-view-JSON-document-explorer/invalidjson1.png)

此外，Document Explorer 會防止您儲存包含無效 JSON 內容的文件。

![JSON 無效儲存錯誤之 [Document Explorer] 的螢幕擷取畫面](./media/documentdb-view-JSON-document-explorer/invalidjson2.png)

最後，Document Explorer 可讓您輕鬆地檢視目前載入之文件的系統屬性，您只需按一下 [屬性] 命令即可。

![[Document Explorer] 文件屬性檢視的螢幕擷取畫面](./media/documentdb-view-JSON-document-explorer/documentproperties.png)

> [AZURE.NOTE] 時間戳記 (\_ts) 屬性在內部會以 Epoch 時間表示，但是 Document Explorer 會以一般人可判讀的 GMT 格式顯示此值。

## 篩選文件
[文件總管] 支援許多導覽選項以及進階設定。

根據預設，Document Explorer 會在選取的集合中，依照建立日期 (最早到最晚) 載入前 100 個文件。您可以選取 [Document Explorer] 刀鋒視窗底部的 [載入更多] 選項，載入其他的文件 (每批 100 個文件)。您可以透過 [篩選] 命令來選擇要載入哪些文件。

1. [啟動 Document Explorer](#launch-document-explorer)。

2. 在 [Document Explorer] 刀鋒視窗的頂端，按一下 [篩選]。

    ![[Document Explorer 篩選設定] 的螢幕擷取畫面](./media/documentdb-view-JSON-document-explorer/documentexplorerfiltersettings.png)
  
3.  篩選設定會出現在命令列下方。在篩選設定中，提供 WHERE 子句及/或 ORDER BY 子句，然後再按一下 [篩選]。

	![[Document Explorer 設定] 刀鋒視窗的螢幕擷取畫面](./media/documentdb-view-JSON-document-explorer/documentexplorerfiltersettings2.png)

	Document Explorer 會以符合篩選查詢的文件自動重新整理結果。深入了解 [SQL 查詢和 SQL 語法](documentdb-sql-query.md)一文中的 DocumentDB SQL 文法，或列印一份 [SQL 查詢小祕技](documentdb-sql-query-cheat-sheet.md)。

    [資料庫] 和 [集合] 下拉式清單方塊可用來輕鬆地變更目前檢視文件的集合，而無需關閉並重新啟動 Document Explorer。

    Document Explorer 還支援根據 id 屬性篩選目前載入的文件集。您只需在 [依識別碼篩選文件] 方塊中輸入即可。

	![反白顯示篩選條件的 [Document Explorer] 螢幕擷取畫面](./media/documentdb-view-JSON-document-explorer/documentexplorerfilter.png)

	Document Explorer 清單中的結果便會根據您所提供的準則進行篩選。

	![含篩選結果的 [Document Explorer] 螢幕擷取畫面](./media/documentdb-view-JSON-document-explorer/documentexplorerfilterresults.png)

	> [AZURE.IMPORTANT] Document Explorer 篩選功能只會從**目前**載入的文件集進行篩選，而且不會針對目前選取的集合執行查詢。

4. 若要重新整理 Document Explorer 所載入的文件清單，請按一下刀鋒視窗頂端的 [重新整理]。

	![[Document Explorer] 重新整理命令的螢幕擷取畫面](./media/documentdb-view-JSON-document-explorer/documentexplorerrefresh.png)

## 大量新增文件

Document Explorer 支援大量擷取一或多個現有 JSON 文件，每個上傳作業最多 100 個 JSON 檔案。

1. [啟動 Document Explorer](#launch-document-explorer)。

2. 若要開始上傳程序，請按一下 [上傳文件]。

	![[Document Explorer] 大量擷取功能的螢幕擷取畫面](./media/documentdb-view-JSON-document-explorer/uploaddocument1.png)

    [上傳文件] 刀鋒視窗隨即開啟。

2. 按一下 [瀏覽] 按鈕以開啟檔案總管視窗，選取要上傳的一或多個 JSON 文件，然後按一下 [開啟]。

	![[Document Explorer] 大量擷取程序的螢幕擷取畫面](./media/documentdb-view-JSON-document-explorer/uploaddocument2.png)

	> [AZURE.NOTE] Document Explorer 的每個個別上傳作業目前支援多達 100 個 JSON 文件。

3. 對您的選取項目感到滿意後，請按一下 [上傳] 按鈕。文件會自動新增至 Document Explorer 方格，且上傳結果會顯示為作業進度。系統會針對個別檔案回報匯入失敗。

	![[Document Explorer] 大量擷取結果的螢幕擷取畫面](./media/documentdb-view-JSON-document-explorer/uploaddocument3.png)

4. 作業完成後，您可以另外選取多達 100 個文件進行上傳。

## 在入口網站外部使用 JSON 文件

Azure 入口網站中的 Document Explorer 只是一種處理 DocumentDB 中文件的方法。您也可以使用 [REST API](https://msdn.microsoft.com/library/azure/mt489082.aspx) 或[用戶端 SDK](documentdb-sdk-dotnet.md) 處理文件。如需範例程式碼，請參閱 [.NET SDK 文件範例](documentdb-dotnet-samples.md#document-examples)和 [Node.js SDK 文件範例](documentdb-nodejs-samples.md#document-examples)。

如果您需要從其他來源 (JSON 檔案、MongoDB、SQL Server、CSV 檔案、Azure 資料表儲存體、Amazon DynamoDB 或 HBase) 匯入或移轉檔案，您可以使用 DocumentDB [資料移轉工具](documentdb-import-data.md)，將您的資料快速匯入到 DocumentDB。

## 後續步驟

若要深入了解 Document Explorer 中支援的 DocumentDB SQL 文法，請參閱 [SQL 查詢和 SQL 語法](documentdb-sql-query.md)一文，或列印 [SQL 查詢小祕技](documentdb-sql-query-cheat-sheet.md)。

[學習路徑](https://azure.microsoft.com/documentation/learning-paths/documentdb/)也是很實用的資源，可引導您深入了解 DocumentDB。

<!---HONumber=AcomDC_0224_2016-->