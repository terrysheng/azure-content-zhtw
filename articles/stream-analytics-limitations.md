<properties title="Azure Stream Analytics limitations in the preview release" pageTitle=" 預覽版本中的資料流分析限制 | Azure" description="Learn the limitations in the public preview release of Azure Stream Analytics jobs" metaKeywords="" services="stream analytics" solutions="" documentationCenter="" authors="jgao" videoId="" scriptId="" manager="paulettm" editor="cgronlun"/>

<tags ms.service="stream-analytics" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="data-services" ms.date="10/28/2014" ms.author="jgao" />

#Azure 資料流分析預覽版的限制與已知問題

本文說明 [Azure 資料流分析][stream.analytics.documentation]在預覽版本期間的限制與已知問題。大多數情況下，這些限制可根據您及早意見反應進行修改，或是根據目前的容量條件約束來實施。 
<!--Table of contents for topic, the words in brackets must match the heading wording exactly-->

##本文內容
+ [限制](#limitations) 
+ [版本資訊和已知問題](#knownissues)
+ [後續步驟]

##<a name="limitations"></a> 限制

###區域可用性
在預覽版本中，資料流分析工作只能在美國中部和西歐地區佈建。

###調整 
**串流處理單元配額**

由於目前的容量限制，每個訂閱在每個區域的串流處理單元配額規定為 12 個。如需詳細資訊，請參閱[調整 Azure 資料流分析工作][stream.analytics.scale.jobs]。如果您有需要放寬此限制的商務需求，請連絡 [Microsoft 支援服務][microsoft.support]，我們會在公開提供項目的條件約束內盡可能進行調整。 

**串流處理單元使用率**

在此預覽版本中，提供給工作的串流處理單元數目有時可能會高於選取或計費的數量。此外，串流處理單元不會降低速度，這表示觀察到的效能可能會高於保證效能，這取決於運算資源的可用性。

**資料分割索引鍵**

使用 PARTITION BY 橫向擴充您的查詢時，要分割的欄位必定是資料分割識別碼。在未來的版本時，將會在其他使用者定義的欄位上啟用資料分割。
如需調整工作的詳細資訊，請參閱[調整 Azure 資料流分析工作][stream.analytics.scale.jobs]。

###輸入



**字元編碼**

就 CSV 和 JSON 輸入來源而言，UTF-8 是唯一支援的編碼格式。


###查詢複雜性
資料流分析工作查詢定義中支援的彙總函數數目上限是七個。

###生命週期管理

**工作升級**

資料流分析目前並不支援對執行中工作的定義或組態進行即時編輯。若要變更執行中工作的輸入、輸出、查詢、調整或組態，您必須先停止工作。

**工作停止和重新啟動**

停止工作時並不會保留任何關於工作進度的狀態，這表示目前沒有辦法設定重新啟動的工作，使其從上次停止之處繼續執行。在未來版本中將會解決此限制。如需啟動和停止工作的最佳作法，請參閱 [Azure 資料流分析開發人員指南][stream.analytics.developer.guide]。 

###監控
與工作使用情形和效能相關的某些度量 (例如延遲) 在預覽版本中無法使用。預覽版本也只會以事件計數 (而非大小) 的角度提供工作輸送量資訊。

##<a name="knownissues"></a>版本資訊 / 已知問題

這包含 Azure 資料流分析的已知問題清單。本節內容將隨著我們從清單中移除項目、發現新問題或進一步了解現有問題時變更。


###必須提高事件中心權限
目前在使用資料流分析時，事件中心的輸入和輸出來源必須要有具備「管理」全縣的共用存取原則。

###Azure 儲存體帳戶組態的延遲
第一次在某個區域中建立資料流分析工作時，系統會提示您建立新的儲存體帳戶或指定現有帳戶，以監視該區域中的資料流分析工作。由於設定監視作業會有所延遲，若在 30 分鐘內於相同區域中建立另一個資料流分析工作，系統將不會提示您指定第二個儲存體帳戶，而是會在 [監視儲存體帳戶] 下拉式清單中顯示最近設定的帳戶。若要避免建立不必要的儲存體帳戶，請在第一次於某地區中建立工作的 30 分鐘後，再於該區域中佈建其他工作。 

###工作會在事件中心使用預設的取用者群組
當事件中心指定為輸入時，資料流分析工作會使用預設取用者群組來擷取事件中心的資料。執行此動作時若未進行額外設定，表示沒有其他接收者可以存取事件中心。若要讓事件中心有多個接收者，必須設定其他取用者群組。如需詳細資訊，請參閱[事件中心開發人員指南][azure.event.hubs.developer.guide]。

###新增輸入/輸出 - 事件中心 
事件中心來源的 [新增輸入] 和 [新增輸出] 對話方塊的第三頁有一個名為 [事件中心] 的下拉式清單，其中同時包含目前訂閱中的服務匯流排命名空間清單，和用以連接到不同訂閱之事件中心的選項。如果您想要連接到相同訂閱中的事件中心，請在此處選取其服務匯流排命名空間。如果您想要連接該訂閱以外的事件中心，請選取 [使用其他訂閱中的事件中心]。  


###無法參考相同的查詢步驟多次
在此預覽版本中，使用 WITH 關鍵字定義的特定子查詢步驟無法多次參考。這常會影響到使用相同步驟之別名的自我聯結。若要解決這個問題，請使用相同的子查詢與不同的名稱建立兩個個別步驟。

###不支援的類型轉換會導致 NULL 值
任何事件值只要具有 [Azure 資料流分析查詢語言參考][stream.analytics.query.language.reference]的「資料類型」一節中不支援的類型轉換，就會導致 NULL 值。在此預覽版本中，這些轉換例外狀況並不會有相關的錯誤記錄。 

###記憶體不足問題
串流分析工作若針對維護大量狀態的次序錯誤事件和 (或) 複雜查詢使用較大的容許時間範圍，可能會導致工作耗盡記憶體，進而造成工作重新啟動。啟動和停止作業將會顯示在工作的作業記錄中。若要避免此行為，請將查詢橫向擴充至多個資料分割。在未來版本中，將會藉由降低受影響之工作的效能 (而不是加以重新啟動)，來解決這項限制。

###在分割的查詢中，空的事件中心分區不會產生輸出
在執行以非分割子查詢做為第二個步驟的分割查詢時，如果輸入上有某個事件中心資料分割完全是空的，則查詢將不會產生結果。此錯誤將會反映在工作的作業記錄中。請確定所有事件中心資料分割隨時都有傳入事件，以避免此問題發生。

###SQL 資料庫事件容量限制
使用 SQL 資料庫做為輸出來源時，非常大量的輸出資料可能會導致資料流分析工作逾時。若要解決這個問題，請使用彙總或聯結運算子減少輸出量，或改以 Blob 儲存體或事件中心做為輸出來源。

###大型 Blob 輸入不受支援
從 Blob 儲存體取用大型檔案時，可能會導致資料流分析工作停止運作。若要避免此問題，請讓每個 Blob 的大小保持在 10 MB 以下。

###「左外部聯結」不受支援
「左外部聯結」作業在資料流分析查詢語言中是啟用的，但不受支援。使用「左外部聯結」執行查詢數分鐘，將導致記憶體消耗問題。建議除了短期性質的查詢實驗以外，不要在其他情況下使用這項作業。即將推出的版本將會解決這項限制。


##<a name="nextsteps"></a>後續步驟

- [Azure 資料流分析簡介][stream.analytics.introduction]
- [開始使用 Azure 資料流分析][stream.analytics.get.started]
- [調整 Azure 資料流分析工作][stream.analytics.scale.jobs]
- [Azure 資料流分析的限制與已知問題][stream.analytics.limitations]
- [Azure 資料流分析查詢語言參考][stream.analytics.query.language.reference]
- [Azure 資料流分析管理 REST API 參考][stream.analytics.rest.api.reference] 

<!--Anchors-->
[限制]: #Limitations
[版本資訊和已知問題]: #Release-notes-and-known-issues
[後續步驟]: #next-steps

<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->

[stream.analytics.documentation]: http://go.microsoft.com/fwlink/?LinkId=512093
[stream.analytics.scale.jobs]: ../stream-analytics-scale-jobs/
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide/
[stream.analytics.introduction]: ../stream-analytics-introduction/
[stream.analytics.get.started]: ../stream-analytics-get-started/
[stream.analytics.limitations]: ../stream-analytics-limitations/


[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301


[microsoft.support]: http://support.microsoft.com/
[azure.event.hubs.developer.guide]: http://msdn.microsoft.com/zh-tw/library/azure/dn789972.aspx

[其他 azure.microsoft.com 文件主題的連結 1]: ../virtual-machines-windows-tutorial/
[其他 azure.microsoft.com 文件主題的連結 2]: ../web-sites-custom-domain-name/
[其他 azure.microsoft.com 文件主題的連結 3]: ../storage-whatis-account/
