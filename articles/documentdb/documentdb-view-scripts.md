<properties
	pageTitle="DocumentDB 指令碼總管，JavaScript 編輯器 |Microsoft Azure"
	description="了解 DocumentDB 指令碼總管，它是 Azure 入口網站工具，可讓您管理 DocumentDB 伺服器端程式設計成品 (包括預存程序、觸發程序和使用者定義的函式)。"
	keywords="javascript 編輯器"
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
	ms.topic="article"
	ms.date="02/23/2016"
	ms.author="anhoh"/>

# 使用 DocumentDB 指令碼總管建立和執行預存程序、觸發程序和使用者定義函式

本文提供 [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) 指令碼總管的概述，它是 Azure 入口網站中的 JavaScript 編輯器，可讓您檢視並執行 DocumentDB 伺服器端程式設計成品 (包括預存程序、觸發程序和使用者定義的函式)。請閱讀[預存程序、資料庫觸發程序和 UDF](documentdb-programming.md) 一文中更多有關 DocumentDB 伺服器端程式設計的資訊。

## 啟動指令碼總管

1. 在 Azure 入口網站的動態工具列中，按一下 [DocumentDB 帳戶]。如果看不到 [DocumentDB 帳戶]，可依序按一下 [瀏覽] 和 [DocumentDB 帳戶]。

2. 在 [DocumentDB 帳戶] 刀鋒視窗的頂端，按一下 [指令碼總管]。

	![[指令碼總管] 命令的螢幕擷取畫面](./media/documentdb-view-scripts/scriptexplorercommand.png)
 
    >[AZURE.NOTE] [指令碼總管] 也會出現在資料庫和集合刀鋒視窗上。

    [資料庫] 和 [集合] 下拉式清單方塊會根據您要啟動 [指令碼總管] 的內容預先填入。例如，如果您要從資料庫刀鋒視窗啟動，則系統會預先填入目前資料庫。如果您要從集合刀鋒視窗啟動，則系統會預先填入目前集合。

	![[指令碼總管] 的螢幕擷取畫面](./media/documentdb-view-scripts/scriptexplorerinitial.png)

4.  使用 [資料庫] 和 [集合] 下拉式清單方塊，輕鬆地變更目前正在檢視文件的集合，而無需關閉並重新啟動 [指令碼總管]。

5. [指令碼總管] 還支援根據 id 屬性篩選目前載入的指令碼集。只需在篩選方塊中輸入，即會根據您提供的準則篩選 [指令碼總管] 清單中的結果。

	![[指令碼總管] 的螢幕擷取畫面和篩選結果](./media/documentdb-view-scripts/scriptexplorerfilterresults.png)


	> [AZURE.IMPORTANT] [指令碼總管] 篩選功能只會從***目前***載入的指令碼集篩選，不會自動重新整理目前選取的集合。

5. 若要重新整理 [指令碼總管] 所載入的指令碼清單，您只需要按一下刀鋒視窗頂端的 **[重新整理]** 命令即可。

	![[指令碼總管] 重新整理命令螢幕擷取畫面](./media/documentdb-view-scripts/scriptexplorerrefresh.png)


## 建立、監視及編輯預存程序、觸發程序和使用者定義的函式

[指令碼總管] 可讓您輕鬆地在 DocumentDB 伺服器端程式設計成品上執行 CRUD 作業。

- 若要建立指令碼，只要在 [指令碼總管] 內按一下適用的建立命令、提供識別碼、輸入指令碼的內容，然後按一下 [儲存] 即可。

	![[指令碼總管] 建立選項的螢幕擷取畫面，顯示 JavaScript 編輯器](./media/documentdb-view-scripts/scriptexplorercreatecommand.png)

- 在建立觸發程序時，您也必須指定觸發程序類型和觸發程序作業。

	![[指令碼總管] 觸發程序選項螢幕擷取畫面](./media/documentdb-view-scripts/scriptexplorercreatetrigger.png)

- 若要檢視指令碼，只要按一下您想要的指令碼即可。

	![[指令碼總管] 檢視指令碼體驗螢幕擷取畫面](./media/documentdb-view-scripts/scriptexplorerviewscript.png)

- 若要編輯指令碼，只要在 JavaScript 編輯器中進行所需的變更，然後按一下 [儲存] 即可。

	![[指令碼總管] 檢視指令碼體驗螢幕擷取畫面](./media/documentdb-view-scripts/scriptexplorereditscript.png)

- 若要捨棄任何指令碼的暫止變更，只要按一下 [捨棄] 命令即可。

	![[指令碼總管] 捨棄變更體驗螢幕擷取畫面](./media/documentdb-view-scripts/scriptexplorerdiscardchanges.png)

- [指令碼總管] 還可讓您輕鬆地檢視目前已載入指令碼的系統屬性，您只需按一下 **[屬性]** 命令即可。

	![[指令碼總管] 指令碼屬性檢視螢幕擷取畫面](./media/documentdb-view-scripts/scriptproperties.png)

	> [AZURE.NOTE] 時間戳記 (\_ts) 屬性在內部會以 Epoch 時間表示，但是 [指令碼總管] 會以一般人可判讀的 GMT 格式顯示此值。

- 若要刪除指令碼，請在 [指令碼總管] 中選取，然後按一下 [刪除] 命令。

	![[指令碼總管] 刪除命令螢幕擷取畫面](./media/documentdb-view-scripts/scriptexplorerdeletescript1.png)

- 按一下 [是] 以確認刪除動作，或按一下 [否] 以取消刪除動作。

	![[指令碼總管] 刪除命令螢幕擷取畫面](./media/documentdb-view-scripts/scriptexplorerdeletescript2.png)

## 執行預存程序

[指令碼總管] 可讓您從 Azure 入口網站執行伺服器端預存程序。

- 開啟新建立的預存程序刀鋒視窗時，就已經提供了預設的指令碼 (*prefix*)。若要執行 *prefix* 指令碼或您自己的指令碼，請加入 *id* 和 *inputs*。如果是接受多個參數的預存程序，所有的輸入都必須位於陣列內 (例如 *["foo", "bar"]*)。

	![[指令碼總管預存程序] 刀鋒視窗的螢幕擷取畫面，可加入輸入並執行預存程序](./media/documentdb-view-scripts/documentdb-execute-a-stored-procedure-input.png)

- 若要執行預存程序，只要在指令碼編輯器窗格內按一下 [儲存並執行] 命令即可。

	> [AZURE.NOTE] [儲存並執行] 命令將會在執行您的預存程序之前先加以儲存，這表示它將會覆寫預存程序先前儲存的版本。

- 成功的預存程序執行必須具備 [成功儲存並執行預存程序] 狀態，且傳回的結果將會填入 [結果] 窗格中。

	![[指令碼總管預存程序] 刀鋒視窗的螢幕擷取畫面，可執行預存程序](./media/documentdb-view-scripts/documentdb-execute-a-stored-procedure.png)

- 如果執行時發生錯誤，錯誤將會填入 [結果] 窗格中。

	![[指令碼總管] 指令碼屬性檢視的螢幕擷取畫面執行預存程序時產生錯誤](./media/documentdb-view-scripts/documentdb-execute-a-stored-procedure-error.png)

## 使用入口網站外部的指令碼

Azure 入口網站中的 [指令碼總管] 只是在 DocumentDB 中使用預存程序、觸發程序和 使用者定義函式的一種方式。您也可以使用 REST API 或[用戶端 SDK](documentdb-sdk-dotnet.md) 處理指令碼。REST API 文件包含處理[使用 REST 的預存程序](https://msdn.microsoft.com/library/azure/mt489092.aspx)、[使用 REST 的使用者函式](https://msdn.microsoft.com/library/azure/dn781481.aspx)及[使用 REST 的觸發程序](https://msdn.microsoft.com/library/azure/mt489116.aspx)的範例。範例也可用來顯示如何[使用 C# 處理指令碼](documentdb-dotnet-samples.md#server-side-programming-examples)和[使用 Node.js 處理指令碼](documentdb-nodejs-samples.md#server-side-programming-examples)。

## 後續步驟

深入了解[預存程序、資料庫觸發程序和 UDF](documentdb-programming.md) 一文中有關 DocumentDB 伺服器端程式設計的資訊。

[學習路徑](https://azure.microsoft.com/documentation/learning-paths/documentdb/)也是很實用的資源，可引導您深入了解 DocumentDB。

<!---HONumber=AcomDC_0224_2016-->