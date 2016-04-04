<properties 
	pageTitle="使用邏輯應用程式功能 |Microsoft Azure" 
	description="了解如何使用邏輯應用程式的進階功能。" 
	authors="stepsic-microsoft-com" 
	manager="erikre" 
	editor="" 
	services="app-service\logic" 
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/11/2016"
	ms.author="stepsic"/>
	
# 使用 Logic Apps 功能

在[上一個主題](app-service-logic-create-a-logic-app.md)中，您已建立第一個邏輯應用程式。現在我們將說明如何使用 App Services Logic Apps，來建立更完整的程序。本主題將介紹下列新的 Logic Apps 概念：

- 條件式邏輯，只有在符合特定條件時，才會執行動作。
- 用以編輯現有邏輯應用程式的程式碼檢視。
- 啟動工作流程的選項。

在完成本主題之前，您應先完成[建立新的邏輯應用程式](app-service-logic-create-a-logic-app.md)中的步驟。在 [Azure 入口網站] 中，瀏覽至您的邏輯應用程式，然後按一下摘要中的 [**觸發程序和動作**]，以編輯邏輯應用程式定義。

## 參考資料

您可能會發現下列文件很有用：

- [管理和執行階段 REST API](https://msdn.microsoft.com/library/azure/mt643787.aspx) - 包括如何直接叫用邏輯應用程式
- [語言參考](https://msdn.microsoft.com/library/azure/mt643789.aspx) - 所有支援的函式/運算式完整清單
- [觸發程序和動作類型](https://msdn.microsoft.com/library/azure/mt643939.aspx) - 不同類型的動作及其採用的輸入
- [App Service 概觀](../app-service/app-service-value-prop-what-is.md) - 說明建置方案時可選擇哪些元件

## 新增條件式邏輯

雖然原始流程也可運作，但有某些區域是可以改善的。


### 條件式
此邏輯應用程式可能會導致您收到許多電子郵件。下列步驟會加入邏輯，以確定您只有在推文是來自有特定粉絲人數的某人時，才會收到電子郵件。

1. 按一下加號，然後尋找 Twitter 的 [取得使用者] 動作。

2. 從觸發程序傳入 [推文者] 欄位，以取得 Twitter 使用者的相關資訊。

	![取得使用者](./media/app-service-logic-use-logic-app-features/getuser.png)

3. 同樣按一下加號，但這次請選取 [新增條件]

4. 在第一個方塊中，按一下 [取得使用者] 下方的 [...]，以尋找 [粉絲計數] 欄位。

5. 在下拉式清單中，選取 [大於]

6. 在第二個方塊中，輸入您希望使用者擁有的粉絲數目。

	![條件式](./media/app-service-logic-use-logic-app-features/conditional.png)

7.  最後，將電子郵件方塊拖放到 [如果是] 方塊中。這表示您只會在符合粉絲計數時收到電子郵件。

## 使用程式碼檢視編輯邏輯應用程式

除了設計工具以外，您也可以直接編輯定義邏輯應用程式的程式碼，如下所示。

1. 按一下命令列中的 [**程式碼檢視**] 按鈕。 

	這會開啟一個完整的編輯器，顯示您剛建立的定義。

	![程式碼檢視](./media/app-service-logic-use-logic-app-features/codeview.png)

    藉由使用文字編輯器，您可以在相同的邏輯應用程式或邏輯應用程式之間複製並貼上任何數量的動作。您也可以輕鬆地在定義中新增或移除整個區段，以及與其他人共用定義。

2. 在程式碼檢視中進行變更之後，請直接按一下 [**儲存**]。

### 參數
Logic Apps 的某些功能只能在程式碼檢視中使用。例如，參數就是其中之一。參數可讓您輕鬆地在整個邏輯應用程式中重複使用值。例如，如果您想要在數個動作中使用同一個電子郵件地址，您應將該地址定義為參數。

下列作業會更新您現有的邏輯應用程式，而將參數用於查詢詞彙。

1. 在程式碼檢視中找出 `parameters : {}` 物件，並插入下列主題物件：

	    "topic" : {
		    "type" : "string",
		    "defaultValue" : "MicrosoftAzure"
	    }
    
2. 捲動至 `twitterconnector` 動作、找出查詢值，並使用 `#@{parameters('topic')}` 來取代它。您也可以使用 **concat** 函式來結合兩個或多個字串，例如：`@concat('#',parameters('topic'))` 等同於上述程式碼。
 
參數很適合用來提取您很可能經常變更的值。當您需要在不同環境中覆寫參數時，參數特別有用。如需有關如何根據環境覆寫參數的詳細資訊，請參閱我們的 [REST API 文件](https://msdn.microsoft.com/library/mt643787.aspx)。

現在，當您按一下 [儲存] 時，每小時都會有回推數超過 5 個的新推文傳遞到您 Dropbox 中名為 [推文] 的資料夾。

若要深入了解邏輯應用程式定義，請參閱[撰寫邏輯應用程式定義](app-service-logic-author-definitions.md)。

## 啟動邏輯應用程式工作流程
有數個不同的選項可用來啟動您的邏輯應用程式中定義的工作流程。工作流程一律可在 [Azure 入口網站]中隨選啟動。

### 循環觸發程序
循環觸發程序會依照您指定的間隔執行。當觸發程序具有條件式邏輯時，觸發程序會判斷工作流程是否需要執行。觸發程序透過傳回 `200` 狀態碼，表示應執行。如果不需要執行，則會傳回 `202` 狀態碼。

### 使用 REST API 回呼
服務可以呼叫邏輯應用程式端點以啟動工作流程。如需詳細資訊，請參閱[作為可呼叫端點的邏輯應用程式](app-service-logic-connector-http.md)。若要隨選啟動該種邏輯應用程式，請按一下命令列上的 [立即執行] 按鈕。

<!-- Shared links -->
[Azure 入口網站]: https://portal.azure.com

<!---HONumber=AcomDC_0323_2016-->