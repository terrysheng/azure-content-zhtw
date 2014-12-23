<properties title="Scale Azure Stream Analytics jobs" pageTitle="調整資料流分析工作 | Azure" description="Learn how to scale Stream Analytics jobs" metaKeywords="" services="stream-analytics" solutions="" documentationCenter="" authors="jgao" videoId="" scriptId="" manager="paulettm" editor="cgronlun"/>

<tags ms.service="stream-analytics" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="data-services" ms.date="10/28/2014" ms.author="jgao" />

# 調整 Azure 資料流分析工作 

了解如何計算資料流分析工作的*串流處理單元*，以及如何藉由設定輸入資料分割、微調查詢定義，及設定工作串流處理單元來調整資料流分析工作。

Azure 資料流分析工作定義包含輸入、查詢和輸出。輸入是工作從中讀取資料流之處，輸出則是工作將工作結果傳送到的位置，而查詢可用來轉換輸入資料流。一個工作至少需要一個資料流輸入來源。資料流輸入來源可以是 Azure 服務匯流排事件中心或 Azure Blob 儲存體。如需詳細資訊，請參閱 [Azure 資料流分析簡介][stream.analytics.introduction]、[開始使用 Azure 資料流分析][stream.analytics.get.started]和 [Azure 資料流分析開發人員指南][stream.analytics.developer.guide]。 

可用來處理資料流分析工作的資源會以串流處理單元來測量。每個串流處理單元最多可提供每秒 1 MB 的輸送量。每項工作至少需要一個串流處理單元，這是所有工作的預設值。您可以使用 Azure 管理入口網站，為一項資料流分析工作設定最多 12 個串流處理單元。特定區域中，每個 Azure 訂閱的所有工作最多只能有 12 個串流處理單元。若要將訂閱的串流處理單元增加到 100 個單元，請連絡 [Microsoft 支援服務][microsoft.support]。

工作可以使用的串流處理單元數目，取決於輸入的磁碟分割設定和為工作定義的查詢。本文將說明如何計算及微調查詢，以增加輸送量。

##本文內容
+ [計算工作的最大串流處理單元](#calculate)
+ [設定資料流分析工作資料分割](#configure)
+ [監視資料流分析工作效能](#monitor)
+ [後續步驟](#nextstep)


##<a name="calculate"></a>計算工作的最大串流處理單元
資料流分析工作可使用的串流處理單元總數，取決於為工作定義之查詢中的步驟數目，和每個步驟的資料分割數目。

### 查詢的步驟
一個查詢可以有一或多個步驟。每個步驟都是使用 WITH 關鍵字定義的子查詢。WITH 關鍵字之外唯一的查詢也會計為步驟。例如，下列查詢中的 SELECT 陳述式：

	WITH Step1 (
		SELECT COUNT(*) AS Count, TollBoothId
		FROM Input1 Partition By PartitionId 
		GROUP BY TumblingWindow(minute, 3), TollBoothId
	) 

	SELECT SUM(Count) AS Count, TollBoothId
	FROM Step1 
	GROUP BY TumblingWindow(minute,3), TollBoothId

上述查詢有兩個步驟。 

> [WACOM.NOTE] 在本文稍後將說明此範例查詢。

### 分割步驟

要分割步驟必須符合下列條件：

- 必須分割輸入來源。請參閱 [Azure 資料流分析開發人員指南][stream.analytics.developer.guide] 和 [Azure 事件中心開發人員指南][azure.event.hubs.developer.guide]。
- 查詢的 SELECT 陳述式必須讀取分割的輸入來源。 
- 步驟內的查詢必須有 Partition By 關鍵字 

分割查詢時將會處理輸入事件並將其彙總在個別的資料分割群組中，並且會為每個群組產生輸出事件。如果需要結合彙總，您必須建立第二個非資料分割的步驟以進行彙總。

預覽版本的 Azure 資料流分析並不支援依資料行名稱的資料分割。您只能依資料分割識別碼欄位進行分割，這是查詢中的內建欄位。資料分割識別碼欄位會指出事件來自於來源資料流的哪個資料分割。如需詳細資訊，請參閱 [Azure 資料流分析的限制和已知問題][stream.analytics.limitations]。

### 計算工作的最大串流處理單元

所有非資料分割的步驟可以整合，而將資料流分析工作的串流處理單元調整為最多 6 個。若要新增更多串流處理單元，則必須分割步驟。每個資料分割可以有 6 個串流處理單元。

<table border="1">
<tr><th>工作的查詢</th><th>工作的最大串流處理單元</th></td>

<tr><td>
<ul>
<li>查詢包含一個步驟。</li>
<li>步驟未分割。</li>
</ul>
</td>
<td>6</td></tr>

<tr><td>
<ul>
<li>輸入資料流分割時會除以 3。</li>
<li>查詢包含一個步驟。</li>
<li>步驟進行分割。</li>
</ul>
</td>
<td>18</td></tr>

<tr><td>
<ul>
<li>查詢包含兩個步驟。</li>
<li>兩個步驟都不會分割。</li>
</ul>
</td>
<td>6</td></tr>



<tr><td>
<ul>
<li>資料流輸入分割時會除以 3。</li>
<li>查詢包含兩個步驟。</li>
<li>SELECT 陳述式會讀取分割的輸入。</li>
</ul>
</td>
<td>24 (18 個用於分割的步驟 + 6 個用於非分割的步驟)</td></tr>
</table>

###調整的範例
下列查詢會以三個收費亭為準，計算在 3 分鐘的時間範圍內通過收費站的車流量。此查詢可以調整為最多 6 個串流處理單元。

	SELECT COUNT(*) AS Count, TollBoothId
	FROM Input1 
	GROUP BY TumblingWindow(minute, 3), TollBoothId

若要讓查詢使用更多串流處理單元，必須同時分割資料流輸入和查詢。假設資料流資料分割設為 3，則下列修改的查詢可以最多調整為 18 個串流處理單元。

	SELECT COUNT(*) AS Count, TollBoothId
	FROM Input1 Partition By PartitionId
	GROUP BY TumblingWindow(minute, 3), TollBoothId

分割查詢時將會處理輸入事件並將其彙總在個別的資料分割群組中，並且也會為每個群組產生輸出事件。當群組依據欄位不是資料劉輸入中的資料分割索引鍵時，資料分割可能會導致某些非預期的結果。例如，上述範例查詢中的 TollBoothId 欄位不是 Input1 資料分割索引鍵。收費亭 #1 中的資料可能分散在多個資料分割中。每個 Input1 資料分割都將由資料流分析個別處理，並且會建立相同的收費亭在相同輪動時間範圍內之車流量計數的多筆記錄。如果輸入資料分割索引鍵無法變更，此問題可藉由新增額外的非資料分割步驟來解決。例如：

	WITH Step1 (
		SELECT COUNT(*) AS Count, TollBoothId
		FROM Input1 Partition By PartitionId
		GROUP BY TumblingWindow(minute, 3), TollBoothId
	) 

	SELECT SUM(Count) AS Count, TollBoothId
	FROM Step1 
	GROUP BY TumblingWindow(minute, 3), TollBoothId

此查詢可以調整為 24 個串流處理單元。 

>[WACOM.NOTE] 如果您要聯結兩個資料流，請確定資料流是由執行聯結之資料行的分割索引鍵進行分割的，且兩個資料流中有相同數目的資料分割。


##<a name="configure"></a>設定資料流分析工作資料分割

**調整工作的串流處理單元**

1. 登入[管理入口網站][azure.management.portal]。
2. 按一下左側的 [**資料流分析**]。
3. 按一下您要調整的資料流分析工作。
4. 按一下頁面頂端的 [**調整**]。

![Azure Stream Analytics configure job scale][img.stream.analytics.configure.scale]


##<a name="monitor"></a>監視工作效能

使用管理入口網站可讓您追蹤工作的輸送量 (事件數/秒)：

![Azure Stream Analytics monitor jobs][img.stream.analytics.monitor.job]
 
計算工作負載的預期輸送量 (事件數/秒)。如果輸送量少於預期，請微調輸入資料分割、微調查詢，並為工作新增更多串流處理單元。

##<a name="nextstep"></a> 後續步驟
在本文中，您已了解如何計算串流處理單元以及如何調整資料流分析工作。若要閱讀更多關於資料流分析的資訊，請參閱：

- [Azure 資料流分析簡介][stream.analytics.introduction]
- [開始使用 Azure 資料流分析][stream.analytics.get.started]
- [Azure 資料流分析開發人員指南][stream.analytics.developer.guide]
- [Azure 資料流分析的限制和已知問題][stream.analytics.limitations]
- [Azure 資料流分析查詢語言參考][stream.analytics.query.language.reference]
- [Azure 資料流分析管理 REST API 參考][stream.analytics.rest.api.reference]

<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png

<!--Link references-->

[microsoft.support]: http://support.microsoft.com
[azure.management.portal]: http://manage.windowsazure.com
[azure.event.hubs.developer.guide]: http://msdn.microsoft.com/zh-tw/library/azure/dn789972.aspx

[stream.analytics.developer.guide]: ../stream-analytics-developer-guide/
[stream.analytics.limitations]: ../stream-analytics-limitations/
[stream.analytics.introduction]: ../stream-analytics-introduction/
[stream.analytics.get.started]: ../stream-analytics-get-started/
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301


