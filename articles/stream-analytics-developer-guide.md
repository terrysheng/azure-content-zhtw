<properties title="Azure Stream Analytics developer guide" pageTitle="資料流分析開發人員指南 |Azure" description="Learn how to develop Azure Stream Analytics applications" metaKeywords="" services="stream analytics" solutions="" documentationCenter="" authors="jgao" videoId="" scriptId="" manager="paulettm" editor="cgronlun"/>

<tags ms.service="stream-analytics" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="data-services" ms.date="10/28/2014" ms.author="jgao" />


# Azure 資料流分析開發人員指南 

[此為發行前文件，其內容將在未來的版本中變更。] 

Azure 資料流分析是完全受管理的服務，可用來對雲端中的串流資料進行低延遲、高可用性、可延展的複雜事件處理。在預覽版本中，資料流分析可讓客戶設定串流工作以分析資料流，並可讓客戶以接近即時的方式進行分析。  

資料流分析的目標案例：

- 對高容量和高速資料執行複雜事件處理   
- 從分散在世界各地的資產或設備 (例如連線的汽車或電網) 收集事件資料 
- 處理遙測資料以進行接近即時的監視和診斷 
- 擷取和封存即時事件供後續處理

如需詳細資訊，請參閱 [Azure 資料流分析簡介][stream.analytics.introduction]。 

資料流分析工作會定義為一或多個輸入來源、對傳入資料流資料的查詢，和輸出目標。  


##本文內容

+ [輸入](#inputs) 
+ [查詢](#query)
+ [輸出](#output)
+ [調整工作](#scale)
+ [工作的監視和疑難排解](#monitor)
+ [管理工作](#manage)
+ [後續步驟](#nextsteps)



##<a name="inputs"></a>輸入

### 資料流

每個資料流分析工作定義都必須包含至少一個要由工作取用和轉換的資料流輸入來源。[Azure Blob 儲存體][azure.blob.storage]和 [Azure 服務匯流排事件中心][azure.event.hubs]是受支援的資料流輸入來源。事件中心輸入來源可用來從多個不同的裝置和服務收集事件資料流，而 Blob 儲存體則可做為擷取大量資料的輸入來源。由於 Blob 不會串流處理資料，因此除非 Blob 中的記錄包含時間戳記，否則對 Blob 的資料流分析工作在本質上將不是暫時的。

### 參考資料

資料流分析也支援第二種輸入來源類型：參考資料。這是用來執行相互關聯與查閱的輔助資料，此處的資料通常是靜態或不常變更的。在預覽版本中，Blob 儲存體是唯一支援參考資料的輸入來源。

### 序列化
為確保查詢有正確的行為，資料流分析必須能夠辨識在傳入的資料流上使用的序列化格式。目前支援的格式為 JSON、CSV、Avro for Streaming Data 和 CSV for Reference Data。

### 產生的屬性
根據工作中使用的輸入類型，將會產生一些含有事件中繼資料的其他欄位。您可以查詢這些欄位，就像查詢其他輸入資料行一樣。如果現有事件的某一欄位具有與下列其中一個屬性相同的名稱，此欄位將會以輸入中繼資料進行覆寫。

<table border="1">
	<tr>
		<th></th>
		<th>屬性</th>
		<th>說明</th>
	</tr>
	<tr>
		<td rowspan="4" valign="top"><strong>Blob</strong></td>
		<td>BlobName</td>
		<td>事件之來源輸入 Blob 的名稱</td>
	</tr>
	<tr>
		<td>EventProcessedUtcTime</td>
		<td>處理 Blob 記錄的時間與日期</td>
	</tr>
	<tr>
		<td>BlobLastModifiedUtcTime</td>
		<td>上次修改 Blob 的時間與日期</td>
	</tr>
	<tr>
		<td>PartitionId</td>
		<td>輸入配接器以零起始的資料分割識別碼</td>
	</tr>
	<tr>
		<td rowspan="3" valign="top"><b>事件中心</b></td>
		<td>EventProcessedUtcTime</td>
		<td>處理事件的日期與時間</td>
	</tr>
	<tr>
		<td>EventEnqueuedUtcTime</td>
		<td>事件中心收到事件的日期與時間</td>
	</tr>
	<tr>
		<td>PartitionId</td>
		<td>輸入配接器以零起始的資料分割識別碼</td>
	</tr>
</table>



###其他資源
如需建立輸入來源的詳細資訊，請參閱 [Azure 事件中心開發人員指南][azure.event.hubs.developer.guide]和[使用 Azure Blob 儲存體][azure.blob.storage.use]。  



##<a name="query"></a>查詢
篩選、操作和處理傳入資料的邏輯定義於「資料流分析的查詢」工作中。查詢是以資料流分析查詢語言撰寫的，這是一種類似 SQL 的語言，主要由標準 T-SQL 語法的子集構成，再搭配暫時查詢的某些特定延伸模組。

###時間範圍
時間範圍延伸模組可讓您對特定期間內的事件子集執行彙總與計算。時間範圍函數可使用 GROUP BY 陳述式來叫用。例如，下列查詢會計算每秒接收到的事件： 

	SELECT Count(*) 
	FROM Input1 
	GROUP BY TumblingWindow(second, 1) 

###執行步驟
針對更複雜的查詢，可以使用標準 SQL 子句 WITH 來指定暫時性具名結果集。例如，下列查詢會使用 WITH，以兩個執行步驟執行轉換：
 
	WITH step1 AS ( 
		SELECT Avg(Reading) as avr 
		FROM temperatureInput1 
		GROUP BY Building, TumblingWindow(hour, 1) 
	) 

	SELECT Avg(avr) AS campus_Avg 
	FROM step1 
	GROUP BY TumblingWindow (day, 1) 

若要深入了解查詢語言，請參閱 [Azure 資料流分析查詢語言參考][stream.analytics.query.language.reference]。 

##<a name="output"></a>輸出
輸出來源是資料流分析工作的結果所將寫入到的位置。當工作處理輸入事件時，結果會持續寫入至輸出來源。支援的輸出來源如下：

- Azure 服務匯流排事件中心：當有多個串流管線需要建構在一起時 (例如發出命令至裝置時)，請選擇事件中心做為輸出來源。
- Azure 儲存體 Blob：請將 Blob 儲存體用於輸出的長期封存，或用來儲存資料以進行後續處理。
- Azure SQL Database：此輸出來源適用於本質上具有關聯性的資料，或是對主控於資料庫中的內容具有相依性的應用程式。


##<a name="scale"></a>調整工作

資料流分析工作可以透過串流處理單元的設定來調整，此單元會定義工作接收的處理能力。每個串流處理單元會對應至約 1 MB/秒的輸送量。每個訂閱的每個區域會有 12 個串流處理單元的配額配置到該區域的工作。

如需詳細資訊，請參閱[調整 Azure 資料流分析工作][stream.analytics.scale.jobs]。


##<a name="monitor"></a>工作的監視和疑難排解

###區域監視儲存體帳戶

若要啟用工作監視，資料流分析會要求您指定用來在每個包含資料流分析工作的區域中監視資料的 Azure 儲存體帳戶。這會在工作建立時設定。  

###度量
下列度量可用來監視資料流分析工作的使用情形和效能：

- 傳入輸送量：資料流分析工作接收到的資料量，以事件計數表示
- 傳出輸送量：資料流分析工作傳送至輸出來源的資料量，以事件計數表示
- 錯誤計數：資料流分析工作所造成的錯誤訊息數目

###作業記錄
偵錯或疑難排解資料流分析工作的最佳方法是透過 Azure 作業記錄。作業記錄可以從入口網站的 [管理服務] 區段下存取。若要檢查您的工作記錄，請將 [服務類型] 設為 [資料流分析]，將 [服務名稱] 設為工作的名稱。


##<a name="manage"></a>管理工作 

###啟動和停止工作
在預覽版本中的資料流分析中停止工作後，並不會保留與工作所取用的最後一個事件相關的任何狀態。因此，重新啟動已停止的工作可能會導致事件遭到捨棄或資料重複。如果工作必須暫時停止，最好的作法是檢查輸出，並使用最後一筆記錄的插入時間來估計工作停止的時間。然後，在工作重新啟動時，在 [設定] 索引標籤上的 [啟動輸出] 設定中指定此時間。
這是暫時性的限制，在未來的版本中，會優先修正能在不遺失資料的情況下啟動和停止工作的功能。  

###設定工作
您可以為資料流分析工作調整下列最上層設定：

- 啟動輸出：指定此工作何時會開始產生結果輸出。如果相關聯的查詢包含時間範圍，工作將會在必要的持續期間開始時從輸入來源開始收取輸入，以在指定的時間產生的第一個輸出事件。有兩個選項可供選擇：[工作開始時間] 和 [自訂]。預設值是 [工作開始時間]。使用 [自訂] 選項時，您必須指定日期和時間。此設定可用來指定輸入來源中有多少歷史資料會從特定時間開始取用或收取資料擷取，例如在工作前次停止時。 
- 次序錯誤原則：用以處理事件未依序送達資料流分析工作的設定。您可以指定容許的時間範圍，以指定可在其間重新排序事件的時間臨界值，同時指定要對此時間範圍外的事件採取的動作：捨棄或調整。「捨棄」將會捨棄所有以錯誤順序接收的事件，而「調整」會將次序錯誤之事件的 System.Timestamp 變更為最近收到之排序事件的時間戳記。  
- 地區設定：使用此設定，可指定資料流分析工作的國際化喜好設定。雖然資料的時間戳記是不分地區設定的，但此處的設定將會影響工作剖析、比較和排序資料的方式。預覽版本僅支援 en-US。


##<a name="support"></a>取得支援
如需其他支援，請參閱 [Azure 資料流分析論壇][stream.analytics.forum]。 


##<a name="nextsteps"></a>後續步驟

- [Azure 資料流分析簡介][stream.analytics.introduction]
- [開始使用 Azure 資料流分析][stream.analytics.get.started]
- [調整 Azure 資料流分析工作][stream.analytics.scale.jobs]
- [Azure 資料流分析的限制與已知問題][stream.analytics.limitations]
- [Azure 資料流分析查詢語言參考][stream.analytics.query.language.reference]
- [Azure 資料流分析管理 REST API 參考][stream.analytics.rest.api.reference] 



<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->
[azure.blob.storage]: http://azure.microsoft.com/zh-tw/documentation/services/storage/
[azure.blob.storage.use]: http://azure.microsoft.com/zh-tw/documentation/articles/storage-dotnet-how-to-use-blobs/

[azure.event.hubs]: http://azure.microsoft.com/zh-tw/services/event-hubs/
[azure.event.hubs.developer.guide]: http://msdn.microsoft.com/zh-tw/library/azure/dn789972.aspx

[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.forum]: http://go.microsoft.com/fwlink/?LinkId=512151

[stream.analytics.introduction]: ../stream-analytics-introduction/
[stream.analytics.get.started]: ../stream-analytics-get-started/
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide/
[stream.analytics.scale.jobs]: ../stream-analytics-scale-jobs/
[stream.analytics.limitations]: ../stream-analytics-limitations/
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
