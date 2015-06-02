<properties 
	pageTitle="預覽版本中的資料流分析限制 | Azure" 
	description="了解公開預覽版本之 Azure Stream Analytics 工作的限制。" 
	services="stream-analytics" 
	documentationCenter="" 
	authors="mumian" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="03/05/2015"
	ms.author="jgao"/>

# Azure 資料流分析預覽版的限制與已知問題

本文說明 [Azure 串流分析][stream.analytics.documentation]在預覽版本期間的限制與已知問題。大多數情況下，這些限制可根據您及早意見反應進行修改，或是根據目前的容量條件約束來實施。
<!--Table of contents for topic, the words in brackets must match the heading wording exactly-->


## 限制

### 區域可用性
在預覽版本中，串流分析工作只能在美國中部和西歐地區佈建。

### 調整 
**串流處理單元配額**

由於目前的容量限制，每個訂用帳戶在每個區域的串流處理單元配額規定為 12 個。如需詳細資訊，請參閱[調整 Azure 串流分析工作][stream.analytics.scale.jobs]。如果您有需要放寬此限制的商務需求，請連絡 [Microsoft 支援服務][microsoft.support]，我們會在公開提供項目的條件約束內盡可能依您的需求進行調整。

**串流處理單元使用率**

在此預覽版本中，提供給工作的串流處理單元數目有時可能會高於選取或計費的數量。此外，串流處理單元不會降低速度，這表示觀察到的效能可能會高於保證效能，這取決於運算資源的可用性。

**資料分割索引鍵**

使用 **Partition By** 橫向擴充您的查詢時，要分割的欄位必定是 **PartitionId**。在未來的版本時，將會在其他使用者定義的欄位上啟用資料分割。如需調整工作的詳細資訊，請參閱[調整 Azure 串流分析工作][stream.analytics.scale.jobs]。

### 輸入



**字元編碼**

就 CSV 和 JSON 輸入來源而言，UTF-8 是唯一支援的編碼格式。


### 查詢複雜性
串流分析工作查詢定義中支援的彙總函數數目上限是 7 個。

### 查詢數目
指定輸入來源支援的查詢數目上限是 5 個。


### 生命週期管理

**工作升級**

Stream Analytics 目前無法對執行中工作的定義或組態進行即時編輯。若要變更執行中工作的輸入、輸出、查詢、調整或組態，您必須先停止工作。

**工作停止和重新啟動**

停止工作時並不會保留任何關於工作進度的狀態，這表示目前沒有辦法設定重新啟動的工作，使其從上次停止之處繼續執行。在未來版本中將會解決此限制。如需啟動和停止工作的最佳作法，請參閱 [Azure 串流分析開發人員指南][stream.analytics.developer.guide]。

### 監視
與工作使用情形和效能相關的某些度量 (例如延遲) 在預覽版本中無法使用。預覽版本也只會以事件計數 (而非大小) 的角度提供工作輸送量資訊。

## 版本資訊/已知問題

本節包含 Azure 串流分析的已知問題清單。本節內容將隨著我們從清單中移除項目、發現新問題或進一步了解現有問題時變更。


### 必須提高事件中樞權限
目前在使用串流分析時，事件中樞的輸入來源和輸出目標必須要有具備「管理」權限的共用存取原則。

### Azure 儲存體帳戶組態的延遲
第一次在某個區域中建立 Stream Analytics 工作時，系統會提示您建立新的儲存體帳戶或指定現有帳戶，以監視該區域中的 Stream Analytics 工作。由於設定監視作業會有所延遲，若在 30 分鐘內於相同區域中建立另一個串流分析工作，系統將提示您指定第二個儲存體帳戶，而不會在 [監視儲存體帳戶]**** 下拉式清單中顯示最近設定的帳戶。若要避免建立不必要的儲存體帳戶，請在第一次於某地區中建立工作的 30 分鐘後，再於該區域中佈建其他工作。

### 工作都應該使用各自的事件中樞取用者群組
每一個 Azure Stream Analytics 工作都應該設定自己的事件中樞用戶群組。當工作包含自我聯結或多個輸出時，某些輸出就有可能供一個以上的讀取器使用，這樣會造成單個用戶群組中的讀取器總數，超過事件中樞規定的每一個用戶群組最多 5 個讀取器。在這種情況下，就需要將查詢細分成多個查詢並透過其他事件中樞，傳遞中繼結果。請注意，每一個事件中樞另外還有 20 個用戶群組的限制。如需詳細資訊，請參閱[事件中樞開發人員指南][azure.event.hubs.developer.guide]。

### 新增輸入/輸出 - 事件中樞 
事件中樞來源的 [新增輸入]**** 和 [新增輸出]**** 對話方塊的第三頁有一個名為 [事件中樞]**** 的下拉式清單，其中同時包含目前訂用帳戶中的服務匯流排命名空間清單，和用以連接到不同訂用帳戶事件中樞的選項。如果您想要連接到相同訂用帳戶中的事件中樞，請在此處選取其服務匯流排命名空間。如果您想要連接該訂用帳戶以外的事件中樞 ，請選取 [使用其他訂用帳戶中的事件中樞]****。


### 無法參考相同的查詢步驟多次
在此預覽版本中，透過 **WITH** 關鍵字定義的特定子查詢步驟無法多次參考。這常會影響到使用相同步驟之別名的自我聯結。若要解決這個問題，請使用相同的子查詢與不同的名稱建立個別的兩個步驟。

### 不支援的類型轉換會導致 Null 值
任何事件值只要具有 [Azure 串流分析查詢語言參考][stream.analytics.query.language.reference]的「資料類型」一節中不支援的類型轉換，就會導致 Null 值。在此預覽版本中，這些轉換例外狀況並不會有相關的錯誤記錄。

### 記憶體不足問題
Stream Analytics 工作如果可以容忍大量的失控事件和/或存在大量狀態的複雜查詢，可能會導致工作耗盡記憶體，進而造成工作重新開始。啟動作業和停止作業會顯示在工作的作業記錄中。若要避免此行為，請將查詢橫向擴充至多個資料分割。在未來版本中，將會藉由降低受影響之工作的效能 (而不是加以重新啟動)，來解決這項限制。

### 在分割的查詢中，空的事件中樞分區不會產生輸出
當您正在執行以非分割子查詢做為第二個步驟的分割查詢時，如果輸入上有某個事件中樞資料分割完全是空的，則查詢將不會產生結果。此錯誤將會反映在工作的作業記錄中。請確定所有事件中樞資料分割隨時都有傳入事件，以避免此問題發生。

### SQL 資料庫事件容量限制
使用 SQL 資料庫做為輸出來源時，非常大量的輸出資料可能會導致 Stream Analytics 工作無法在限定的時間內完成。若要解決這個問題，請使用彙總或聯結運算子減少輸出量，或改以 Azure Blob 儲存體或事件中樞做為輸出來源。

### 大型 Blob 輸入不受支援
從 Blob 儲存體取用大型檔案時，可能會導致串流分析工作停止運作。要避免此問題，請讓每個 Blob 的大小保持在 10 MB 以下。

### 資料行標頭中有空白字元會導致 Null 輸出項目
串流分析不會修剪資料行標題中的空白字元。在資料行名稱開頭或結尾包含空白字元將導致工作輸出中會有 Null 項目。


## 取得支援
如需其他支援，請參閱 [Azure 串流分析論壇](stream-analytics-forum.md)。


## 後續步驟

- [Azure Stream Analytics 介紹](stream-analytics-introduction.md)
- [開始使用 Azure Stream Analytics](stream-analytics-get-started.md)
- [調整 Azure Stream Analytics 工作](stream-analytics-scale-jobs.md)
- [Azure Stream Analytics 查詢語言參考](stream-analytics-query-language-reference.md)
- [Azure Stream Analytics 管理 REST API 參考](stream-analytics-rest-api-reference.md) 

<!--Anchors-->
[Limitations]: #Limitations
[Release notes and known issues]: #Release-notes-and-known-issues
[Next steps]: #next-steps

<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->

[stream.analytics.documentation]: http://go.microsoft.com/fwlink/?LinkId=512093
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.developer.guide]: stream-analytics-developer-guide.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.limitations]: stream-analytics-limitations.md


[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301


[microsoft.support]: http://support.microsoft.com/
[azure.event.hubs.developer.guide]: http://msdn.microsoft.com/library/azure/dn789972.aspx

[Link 1 to another azure.microsoft.com documentation topic]: virtual-machines-windows-tutorial.md
[Link 2 to another azure.microsoft.com documentation topic]: web-sites-custom-domain-name.md
[Link 3 to another azure.microsoft.com documentation topic]: storage-whatis-account.md

<!--HONumber=54-->