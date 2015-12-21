
<properties
	pageTitle="自動調整 Azure Batch 集區中的運算節點 | Microsoft Azure"
	description="在雲端集區上啟用自動調整，以動態調整集區中的運算節點數目。"
	services="batch"
	documentationCenter=""
	authors="mmacy"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="batch"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="multiple"
	ms.date="12/04/2015"
	ms.author="marsma"/>

# 自動調整 Azure Batch 集區中的運算節點

自動調整 Azure Batch 集區中的運算節點就是動態調整應用程式所使用的處理能力。調整方式很容易，可節省時間與金錢。若要深入了解計算節點和集區，請參閱 [Azure Batch 基本知識](batch-technical-overview.md)。

當集區上啟用自動調整且集區有相關聯的公式時，就會進行自動調整。公式用來決定處理應用程式所需的運算節點的數目。使用定期收集的範例時，集區中可用的運算節點數目會根據相關聯的公式，每隔 15 分鐘調整一次。

自動調整可以在建立集區時設定，或稍後在現有的集區啟用。此公式也可以在先前啟用自動調整的集區上更新。將公式指派給集區之前進行評估永遠是非常好的做法，因此，監視自動調整執行的狀態非常重要。以下我們將討論其中每一個主題。

> [AZURE.NOTE]每個 Azure Batch 帳戶限制為可用於處理的運算節點的數目上限。系統建立的節點數目最多達到該限制，因此不會達到公式所指定的目標數目。

## 自動調整運算資源

您定義的調整公式會決定集區中可供下一個處理間隔的調整的運算節點數目。自動調整公式只是在要求主體中指派給集區的 [autoScaleFormula](https://msdn.microsoft.com/library/azure/dn820173.aspx) 元素 (REST API) 或指派給 [CloudPool.AutoScaleFormula](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleformula.aspx) 屬性 (.NET API) 的字串值。此公式字串大小不得超過 8KB、最多只能包含 100 個陳述式 (以分號隔開)，且可以包含換行和註解。

公式中的陳述式是任意格式的運算式。這些陳述式可能包含任何系統定義的變數、使用者定義的變數、常數值，以及這些變數或常數支援的作業。

	VAR = Expression(system-defined variables, user-defined variables);

使用多個陳述式和變數可建立複雜的公式：

	VAR₀ = Expression₀(system-defined variables);
	VAR₁ = Expression₁(system-defined variables, VAR₀);

> [AZURE.NOTE]自動調整公式由 [Batch REST](https://msdn.microsoft.com/library/azure/dn820158.aspx) API 變數、類型、作業和函式所組成。甚至在使用 [Batch .NET](https://msdn.microsoft.com/library/azure/mt348682.aspx) 程式庫時，這些也都會用在公式字串中。

### 變數

系統定義的變數和使用者定義的變數可以在公式中使用。

*取得*和*設定*這些**系統定義的變數**值，以管理集區中的運算節點。

<table>
  <tr>
    <th>變數</th>
    <th>說明</th>
  </tr>
  <tr>
    <td>$TargetDedicated</td>
    <td>集區的專用運算節點的目標數目。這個值可以根據工作的實際使用量而變更。</td>
  </tr>
  <tr>
    <td>$NodeDeallocationOption</td>
    <td>運算節點從集區移除時所發生的動作。可能的值包括：
      <br/>
      <ul>
        <li><p><b>requeue</b>：立即終止工作，並將這些工作放回工作佇列，為它們重新排程。</p></li>
        <li><p><b>terminate</b>：立即終止工作，並從工作佇列移除這些工作。</p></li>
        <li><p><b>taskcompletion</b>：等待目前執行中的工作完成，然後再從集區中移除該節點。</p></li>
        <li><p><b>retaineddata</b>：等待所有本機工作保留在節點上的資料先清除，再從集區移除節點。</p></li>
      </ul></td>
   </tr>
</table>

*取得*這些**系統定義的變數**值，以根據範例中運算節點的度量，進行調整。這些變數是唯讀的。

<table>
  <tr>
    <th>變數</th>
    <th>說明</th>
  </tr>
  <tr>
    <td>$CPUPercent</td>
    <td>CPU 使用量的平均百分比</td>
  </tr>
  <tr>
    <td>$WallClockSeconds</td>
    <td>已耗用的秒數</td>
  </tr>
  <tr>
    <td>$MemoryBytes</td>
    <td>已使用的平均 MB 數目</td>
  <tr>
    <td>$DiskBytes</td>
    <td>已在本機磁碟上使用的平均 GB 數目</td>
  </tr>
  <tr>
    <td>$DiskReadBytes</td>
    <td>已讀取的位元組數目</td>
  </tr>
  <tr>
    <td>$DiskWriteBytes</td>
    <td>已寫入的位元組數目</td>
  </tr>
  <tr>
    <td>$DiskReadOps</td>
    <td>已執行的讀取磁碟作業計數</td>
  </tr>
  <tr>
    <td>$DiskWriteOps</td>
    <td>已執行的寫入磁碟作業計數</td>
  </tr>
  <tr>
    <td>$NetworkInBytes</td>
    <td>輸入位元組的數目</td>
  </tr>
  <tr>
    <td>$NetworkOutBytes</td>
    <td>輸出位元組的數目</td>
  </tr>
  <tr>
    <td>$SampleNodeCount</td>
    <td>運算節點的計數</td>
  </tr>
  <tr>
    <td>$ActiveTasks</td>
    <td>處於作用中狀態的工作數目</td>
  </tr>
  <tr>
    <td>$RunningTasks</td>
    <td>處於執行中狀態的工作數目</td>
  </tr>
  <tr>
    <td>$SucceededTasks</td>
    <td>已成功完成的工作數目</td>
  </tr>
  <tr>
    <td>$FailedTasks</td>
    <td>失敗的工作數目</td>
  </tr>
  <tr>
    <td>$CurrentDedicated</td>
    <td>目前的專用運算節點數目</td>
  </tr>
</table>

### 類型

公式中支援以下**類型**。

- double
- doubleVec
- 字串
- timestamp：timestamp 是包含下列成員的複合結構：
	- 年
	- month (1-12)
	- day (1-31)
	- weekday (以數字格式表示，例如 1 代表星期一)
	- hour (以 24 小時制數字格式表示，例如 13 代表下午 1 點)
	- minute (00-59)
	- second (00-59)
- timeinterval
	- TimeInterval\_Zero
	- TimeInterval\_100ns
	- TimeInterval\_Microsecond
	- TimeInterval\_Millisecond
	- TimeInterval\_Second
	- TimeInterval\_Minute
	- TimeInterval\_Hour
	- TimeInterval\_Day
	- TimeInterval\_Week
	- TimeInterval\_Year

### 作業

以上列出的類型允許這些**作業**。

<table>
  <tr>
    <th>作業</th>
    <th>允許的運算子</th>
  </tr>
  <tr>
    <td>double &lt;operator> double => double</td>
    <td>+、-、*、/</td>
  </tr>
  <tr>
    <td>double &lt;operator> timeinterval => timeinterval</td>
    <td>*</td>
  </tr>
  <tr>
    <td>doubleVec &lt;operator> double => doubleVec</td>
    <td>+、-、*、/</td>
  </tr>
  <tr>
    <td>doubleVec &lt;operator> doubleVec => doubleVec</td>
    <td>+、-、*、/</td>
  </tr>
  <tr>
    <td>timeinterval &lt;operator> double => timeinterval</td>
    <td>*、/</td>
  </tr>
  <tr>
    <td>timeinterval &lt;operator> timeinterval => timeinterval</td>
    <td>+、-</td>
  </tr>
  <tr>
    <td>timeinterval &lt;operator> timestamp => timestamp</td>
    <td>+</td>
  </tr>
  <tr>
    <td>timestamp &lt;operator> timeinterval => timestamp</td>
    <td>+</td>
  </tr>
  <tr>
    <td>timestamp &lt;operator> timestamp => timeinterval</td>
    <td>-</td>
  </tr>
  <tr>
    <td>&lt;operator>double => double</td>
    <td>-、!</td>
  </tr>
  <tr>
    <td>&lt;operator>timeinterval => timeinterval</td>
    <td>-</td>
  </tr>
  <tr>
    <td>double &lt;operator> double => double</td>
    <td>&lt;、&lt;=、==、>=、>、!=</td>
  </tr>
  <tr>
    <td>string &lt;operator> string => double</td>
    <td>&lt;、&lt;=、==、>=、>、!=</td>
  </tr>
  <tr>
    <td>timestamp &lt;operator> timestamp => double</td>
    <td>&lt;、&lt;=、==、>=、>、!=</td>
  </tr>
  <tr>
    <td>timeinterval &lt;operator> timeinterval => double</td>
    <td>&lt;、&lt;=、==、>=、>、!=</td>
  </tr>
  <tr>
    <td>double &lt;operator> double => double</td>
    <td>&amp;&amp;、||</td>
  </tr>
  <tr>
    <td>test double only (non-zero is true, zero is false)</td>
    <td>? :</td>
  </tr>
</table>

### 函式

這些預先定義的**函式**可用來定義自動調整公式。

<table>
  <tr>
    <th>函式</th>
    <th>說明</th>
  </tr>
  <tr>
    <td>double <b>avg</b>(doubleVecList)</td>
    <td>DoubleVecList 中所有值的平均值。</td>
  </tr>
  <tr>
    <td>double <b>len</b>(doubleVecList)</td>
    <td>從 doubleVecList 建立的向量的長度。</td>
  <tr>
    <td>double <b>lg</b>(double)</td>
    <td>對數底數 2。</td>
  </tr>
  <tr>
    <td>doubleVec <b>lg</b>(doubleVecList)</td>
    <td>逐元對數底數 2。vec(double) 必須針對單一 double 參數明確傳遞，否則會假設為 double lg(double) 版本。</td>
  </tr>
  <tr>
    <td>double <b>ln</b>(double)</td>
    <td>自然對數。</td>
  </tr>
  <tr>
    <td>doubleVec <b>ln</b>(doubleVecList)</td>
    <td>逐元對數底數 2。vec(double) 必須針對單一 double 參數明確傳遞，否則會假設為 double lg(double) 版本。</td>
  </tr>
  <tr>
    <td>double <b>log</b>(double)</td>
    <td>對數底數 10。</td>
  </tr>
  <tr>
    <td>doubleVec <b>log</b>(doubleVecList)</td>
    <td>逐元對數底數 10。vec(double) 必須針對單一 double 參數明確傳遞，否則會假設為 double log(double) 版本。</td>
  </tr>
  <tr>
    <td>double <b>max</b>(doubleVecList)</td>
    <td>doubleVecList 中的最大值。</td>
  </tr>
  <tr>
    <td>double <b>min</b>(doubleVecList)</td>
    <td>doubleVecList 中的最小值。</td>
  </tr>
  <tr>
    <td>double <b>norm</b>(doubleVecList)</td>
    <td>從 doubleVecList 建立的向量的 2-norm。
  </tr>
  <tr>
    <td>double <b>percentile</b>(doubleVec v, double p)</td>
    <td>向量 v 的百分位數元素。</td>
  </tr>
  <tr>
    <td>double <b>rand</b>()</td>
    <td>介於 0.0 到 1.0 之間的隨機值。</td>
  </tr>
  <tr>
    <td>double <b>range</b>(doubleVecList)</td>
    <td>doubleVecList 中最小和最大值之間的差異。</td>
  </tr>
  <tr>
    <td>double <b>std</b>(doubleVecList)</td>
    <td>doubleVecList 中值的標準差範例。</td>
  </tr>
  <tr>
    <td><b>stop</b>()</td>
    <td>停止自動調整運算式評估。</td>
  </tr>
  <tr>
    <td>double <b>sum</b>(doubleVecList)</td>
    <td>doubleVecList 所有元件的總和。</td>
  </tr>
  <tr>
    <td>timestamp <b>time</b>(string dateTime="")</td>
    <td>若未傳遞參數，為目前時間的時間戳記，否則為 dateTime 字串的時間戳記。支援的 dateTime 格式為 W3CDTF 和 RFC1123。</td>
  </tr>
  <tr>
    <td>double <b>val</b>(doubleVec v, double i)</td>
    <td>向量 v 中位置 i 的元素值，起始索引為零。</td>
  </tr>
</table>

上表中所述的某些函式可以接受清單做為引數。逗號分隔清單是 *double* 和 *doubleVec* 的任意組合。例如：

`doubleVecList := ( (double | doubleVec)+(, (double | doubleVec) )* )?`

評估之前，*doubleVecList* 值會轉換成單一的 *doubleVec*。例如，如果 `v = [1,2,3]`，則呼叫 `avg(v)` 相當於呼叫 `avg(1,2,3)`，而呼叫 `avg(v, 7)` 相當於呼叫 `avg(1,2,3,7)`。

### 取得範例資料

上述系統定義的變數是可提供方法來存取相關聯資料的物件。例如，下列運算式顯示取得最後五分鐘的 CPU 使用量的要求：

`$CPUPercent.GetSample(TimeInterval_Minute * 5)`

這些方法可以用來取得範例資料。

<table>
  <tr>
    <th>方法</th>
    <th>說明</th>
  </tr>
  <tr>
    <td>Count()</td>
    <td>傳回度量歷程記錄中的範例總數。</td>
  </tr>
  <tr>
    <td>GetSample()</td>
    <td><p>傳回資料向量範例。
	<p>一個樣本有 30 秒的度量資料。換句話說，每 30 秒取得樣本，但如下所述，從收集樣本到可用於公式之間會延遲。因此，並非一段指定時間內的所有樣本都可供公式評估。
        <ul>
          <li><p><b>doubleVec GetSample(double count)</b>：指定要從最近收集的樣本中取得的樣本數。</p>
				  <p>Getsample (1) 會傳回最後一個可用的樣本。不過，這不適用於 $CPUPercent 之類的計量，因為不可能知道 <em>何時</em>收集到樣本 (樣本可能是很新)，或因為系統問題 (樣本可能很舊)。在此情況下，最好使用如下所示的時間間隔。</p></li>
          <li><p><b>doubleVec GetSample((timestamp | timeinterval) startTime [, double samplePercent])</b>：指定收集樣本資料的時間範圍，並選擇性地指定在要求的時間範圍內必須可用的樣本數百分比。</p>
          <p>如果 CPUPercent 歷程記錄中有最後十分鐘的所有樣本，則 <em>$CPUPercent.GetSample(TimeInterval_Minute * 10)</em> 會傳回 20 個樣本。不過，如果最後一分鐘的歷程記錄無法使用，則只會傳回 18 個樣本，在此情況下：<br/>
		  &#160;&#160;&#160;&#160;<em>$CPUPercent.GetSample(TimeInterval_Minute * 10, 95)</em> 會失敗，因為只有 90% 的樣本可用，但是<br/>
		  &#160;&#160;&#160;&#160;<em>$CPUPercent.GetSample(TimeInterval_Minute * 10, 80)</em> 會成功。</p></li>
          <li><p><b>doubleVec GetSample((timestamp | timeinterval) startTime, (timestamp | timeinterval) endTime [, double samplePercent])</b>：使用開始時間和結束時間指定收集資料的時間範圍。</p></li></ul>
		  <p>如上所述，從收集樣本到可用於公式之間會延遲。使用 <em>GetSample</em> 方法時必須考量此因素 - 請參閱下方的 <em>GetSamplePercent</em>。</td>
  </tr>
  <tr>
    <td>GetSamplePeriod()</td>
    <td>傳回歷史範例資料集中採取範例的期間。</td>
  </tr>
  <tr>
    <td>HistoryBeginTime()</td>
    <td>傳回度量的最舊可用資料範例的時間戳記。</td>
  </tr>
  <tr>
    <td>GetSamplePercent()</td>
    <td><p>傳回歷程記錄目前在指定的時間間隔內所擁有的範例的百分比。例如：</p>
    <p><b>doubleVec GetSamplePercent( (timestamp | timeinterval) startTime [, (timestamp | timeinterval) endTime] )</b>
	<p>因為 GetSample 方法在傳回範例的百分比小於指定的 samplePercent 時會失敗，因此，您可以使用 GetSamplePercent 方法進行第一次檢查，然後在範例不足時執行替代動作，而不暫停其自動調整評估。</p></td>
  </tr>
</table>

### 度量

您可以在定義公式時使用資源和工作**度量**，這些度量可用來管理集區中的計算節點。

<table>
  <tr>
    <th>度量</th>
    <th>說明</th>
  </tr>
  <tr>
    <td>資源</td>
    <td><p>資源度量是根據 CPU 使用率、頻寬使用量、記憶體使用量和運算節點的數目。公式中會使用這些系統定義的變數 (上述**變數**中所述) 來管理集區中的運算節點：</p>
    <p><ul>
      <li>$TargetDedicated</li>
      <li>$NodeDeallocationOption</li>
    </ul></p>
    <p>這些系統定義的變數用於根據節點資源度量進行調整：</p>
    <p><ul>
      <li>$CPUPercent</li>
      <li>$WallClockSeconds</li>
      <li>$MemoryBytes</li>
      <li>$DiskBytes</li>
      <li>$DiskReadBytes</li>
      <li>$DiskWriteBytes</li>
      <li>$DiskReadOps</li>
      <li>$DiskWriteOps</li>
      <li>$NetworkInBytes</li>
      <li>$NetworkOutBytes</li></ul></p>
  </tr>
  <tr>
    <td>工作</td>
    <td><p>根據工作的狀態，例如作用中、擱置和已完成。</p>
    <p>這些系統定義的變數用於根據工作度量進行調整：</p>
    <p><ul>
      <li>$ActiveTasks</li>
      <li>$RunningTasks</li>
      <li>$SucceededTasks</li>
      <li>$FailedTasks</li>
      <li>$CurrentDedicated</li></ul></p></td>
  </tr>
</table>

## 建立自動調整公式

自動調整公式的建構方式是使用上述元素撰寫陳述式，再將這些陳述式結合成完整的公式。比方說，我們在這裡建構公式是先定義公式的需求：

1. 如果 CPU 使用率偏高，則增加集區中運算節點的目標數目
2. 當 CPU 使用率偏低時，減少集區中運算節點的目標數目
3. 一律以 400 為節點的數目上限

針對在高 CPU 使用率期間*增加*節點，我們將陳述式定義為如果在過去 10 分鐘期間 CPU 使用率的平均最小值大於 70%，則在使用者定義的變數 ($TotalNodes) 中填入節點目前目標數目的 110%：

	$TotalNodes = (min($CPUPercent.GetSample(TimeInterval_Minute*10)) > 0.7) ? ($CurrentDedicated * 1.1) : $CurrentDedicated;

下一個陳述式定義如果過去 60 分鐘的平均 CPU 使用率*低於* 20%，則將相同的變數設定為節點目前目標數目的 90%，將低 CPU 使用率期間的目標數目降低。請注意，此陳述式也會參考上述陳述式中使用者定義的變數 *$TotalNodes*。

	$TotalNodes = (avg($CPUPercent.GetSample(TimeInterval_Minute*60)) < 0.2) ? ($CurrentDedicated * 0.9) : $TotalNodes;

現在將專用計算節點的目標數目設定為**上限** 400 個：

	$TargetDedicated = min(400, $TotalNodes)

以下是完整的公式：

	$TotalNodes = (min($CPUPercent.GetSample(TimeInterval_Minute*10)) > 0.7) ? ($CurrentDedicated * 1.1) : $CurrentDedicated;
	$TotalNodes = (avg($CPUPercent.GetSample(TimeInterval_Minute*60)) < 0.2) ? ($CurrentDedicated * 0.9) : $TotalNodes;
	$TargetDedicated = min(400, $TotalNodes)

## 在自動調整啟用時建立集區

若要在建立集區時啟用自動調整，請採用下列其中一項技術：

- [New-AzureBatchPool](https://msdn.microsoft.com/library/azure/mt125936.aspx)：此 Azure PowerShell Cmdlet 會使用 AutoScaleFormula 參數指定自動調整公式。
- [BatchClient.PoolOperations.CreatePool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx)：呼叫這個 .NET 方法以建立集區之後，您接著要設定集區的 [CloudPool.AutoScaleEnabled](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleenabled.aspx) 和 [CloudPool.AutoScaleFormula](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleformula.aspx) 屬性，以啟用自動調整。
- [將集區加入至帳戶](https://msdn.microsoft.com/library/azure/dn820174.aspx)：在這個 REST API 要求中會使用 enableAutoScale 和 autoScaleFormula 元素，以便在集區建立時設定集區的自動調整。

> [AZURE.NOTE]如果您使用上述其中一項技術在建立集區時設定自動調整，則建立時不會 (且不可以) 指定集區的 *targetDedicated* 參數。也請注意，如果您想要對已啟用自動調整的集區手動調整大小 (例如使用 [BatchClient.PoolOperations.ResizePool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.resizepool.aspx))，則必須先在集區停用自動調整，然後調整集區的大小。

下列程式碼片段顯示如何使用 [Batch .NET](https://msdn.microsoft.com/library/azure/mt348682.aspx) 程式庫建立啟用自動調整的 [CloudPool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx)，其公式在星期一將節點目標數目設定為 5，在一週的其他每天設定為 1。在程式碼片段中，"myBatchClient" 是適當初始化的 [BatchClient 執行個體](http://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx))：

		CloudPool pool myBatchClient.PoolOperations.CreatePool("mypool", "3", "small");
		pool.AutoScaleEnabled = true;
		pool.AutoScaleFormula = "$TargetDedicated = (time().weekday==1?5:1);";
		pool.Commit();

## 集區建立之後啟用自動調整

如果您已經使用 *targetDedicated* 參數設定具有指定計算節點數目的集區，則您稍後可以更新現有的集區以自動調整。使用以下其中一種方式執行這項操作：

- [BatchClient.PoolOperations.EnableAutoScale](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.enableautoscale.aspx)：這個 .NET 方法需要現有集區的識別碼，以及要套用至集區的自動調整公式。
- [在集區上啟用自動調整](https://msdn.microsoft.com/library/azure/dn820173.aspx)：這個 REST API 要求需要 URI 中現有集區的識別碼，以及要求主體中的自動調整公式。

> [AZURE.NOTE]如果建立集區時指定 *targetDedicated* 參數的值，則評估自動調整公式時會忽略此值。

此程式碼片段示範使用 [Batch .NET](https://msdn.microsoft.com/library/azure/mt348682.aspx) 程式庫在現有的集區啟用自動調整。請注意，在現有的集區上啟用和更新公式都使用相同的方法。因此，如果自動調整已啟用，則這項技術會在指定的集區*更新*公式。此程式碼片段假設 "myBatchClient" 是適當初始化的 [BatchClient](http://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx) 執行個體，"mypool" 是現有 [CloudPool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx) 的識別碼。

		 // Define the autoscaling formula. In this snippet, the  formula sets the target number of nodes to 5 on
		 // Mondays, and 1 on every other day of the week
		 string myAutoScaleFormula = "$TargetDedicated = (time().weekday==1?5:1);";

		 // Update the existing pool's autoscaling formula by calling the BatchClient.PoolOperations.EnableAutoScale
		 // method, passing in both the pool's ID and the new formula.
		 myBatchClient.PoolOperations.EnableAutoScale("mypool", myAutoScaleFormula);

## 評估自動調整公式

在應用程式中使用公式之前先進行評估永遠是非常好的做法。公式的評估方式是針對現有集區的公式執行「測試回合」。使用下列方法：

- [BatchClient.PoolOperations.EvaluateAutoScale](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.evaluateautoscale.aspx) 或 [BatchClient.PoolOperations.EvaluateAutoScaleAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.evaluateautoscaleasync.aspx)：這些 .NET 方法需要現有集區的識別碼，以及包含自動調整公式的字串。呼叫的結果會包含在 [AutoScaleEvaluation](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscaleevaluation.aspx) 類別的執行個體中。
- [評估自動調整公式](https://msdn.microsoft.com/library/azure/dn820183.aspx)：在這個 REST 要求中，集區識別碼會在 URI 中指定，而自動調整公式則會在要求主體的 *autoScaleFormula* 元素中指定。作業的回應會包含可能與公式相關的任何錯誤資訊。

> [AZURE.NOTE]若要評估自動調整公式，您必須先使用有效的公式在集區啟用自動調整。

在這個使用 [Batch .NET](https://msdn.microsoft.com/library/azure/mt348682.aspx) 程式庫的程式碼片段中，我們在將公式套用至 [CloudPool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx) 之前先評估公式。

		// First obtain a reference to the existing pool
		CloudPool pool = myBatchClient.PoolOperations.GetPool("mypool");

		// We must ensure that autoscaling is enabled on the pool prior to evaluating a formula
		if (pool.AutoScaleEnabled.HasValue && pool.AutoScaleEnabled.Value)
		{
			// The formula to evaluate - adjusts target number of nodes based on day of week and time of day
			string myFormula = @"
				$CurTime=time();
				$WorkHours=$CurTime.hour>=8 && $CurTime.hour<18;
				$IsWeekday=$CurTime.weekday>=1 && $CurTime.weekday<=5;
				$IsWorkingWeekdayHour=$WorkHours && $IsWeekday;
				$TargetDedicated=$IsWorkingWeekdayHour?20:10;
			";

			// Perform the autoscale formula evaluation. Note that this does not actually apply the formula to
			// the pool.
			AutoScaleEvaluation eval = client.PoolOperations.EvaluateAutoScale(pool.Id, myFormula);

			if (eval.AutoScaleRun.Error == null)
			{
				// Evaluation success - print the results of the AutoScaleRun. This will display the values of each
				// variable as evaluated by the the autoscaling formula.
				Console.WriteLine("AutoScaleRun.Results: " + eval.AutoScaleRun.Results);

				// Apply the formula to the pool since it evaluated successfully
				client.PoolOperations.EnableAutoScale(pool.Id, myFormula);
			}
			else
			{
				// Evaluation failed, output the message associated with the error
				Console.WriteLine("AutoScaleRun.Error.Message: " + eval.AutoScaleRun.Error.Message);
			}
		}

成功評估此程式碼片段中的公式會產生類似下列的輸出：

		AutoScaleRun.Results: $TargetDedicated = 10;$NodeDeallocationOption = requeue;$CurTime = 2015 - 08 - 25T20: 08:42.271Z;$IsWeekday = 1;$IsWorkingWeekdayHour = 0;$WorkHours = 0

## 取得自動調整執行的相關資訊

應該定期對公式檢查自動調整回合的結果如預期般執行。使用以下其中一種方式執行這項操作：

- [CloudPool.AutoScaleRun](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscalerun.aspx)：使用 .NET 程式庫時，集區的這個屬性會提供 [AutoScaleRun](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.aspx) 類別的執行個體，以提供最近執行的自動調整的下列屬性：
  - [AutoScaleRun.Error](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.error.aspx)
  - [AutoScaleRun.Results](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.results.aspx)
  - [AutoScaleRun.Timestamp](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.timestamp.aspx)
- [取得集區的相關資訊](https://msdn.microsoft.com/library/dn820165.aspx)：這個 REST API 要求會傳回集區的相關資訊，其中包含最近執行的自動調整。

## 公式範例

讓我們看看一些範例，其中只顯示公式可用來自動調整集區中的運算資源的幾種方式。

### 範例 1

或許您想要根據星期幾和時段來調整集區大小，以相應地增加或減少集區中的節點數目：

```
$CurTime=time();
$WorkHours=$CurTime.hour>=8 && $CurTime.hour<18;
$IsWeekday=$CurTime.weekday>=1 && $CurTime.weekday<=5;
$IsWorkingWeekdayHour=$WorkHours && $IsWeekday;
$TargetDedicated=$IsWorkingWeekdayHour?20:10;
```

此公式會先取得目前的時間。如果是工作日 (1-5) 且在上班時間內 (8AM-6PM)，則將目標集區大小設為 20 個節點。否則目標集區大小會設為 10 個節點。

### 範例 2

在此範例中，是根據佇列中的工作數目調整集區大小。請注意，公式字串中接受註解和換行。

```
// Get pending tasks for the past 15 minutes.
$Samples = $ActiveTasks.GetSamplePercent(TimeInterval_Minute * 15);
// If we have less than 70% data points, we use the last sample point, otherwise we use the maximum of
// last sample point and the history average.
$Tasks = $Samples < 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1), avg($ActiveTasks.GetSample(TimeInterval_Minute * 15)));
// If number of pending tasks is not 0, set targetVM to pending tasks, otherwise half of current dedicated.
$TargetVMs = $Tasks > 0? $Tasks:max(0, $TargetDedicated/2);
// The pool size is capped at 20, if target VM value is more than that, set it to 20. This value
// should be adjusted according to your use case.
$TargetDedicated = max(0,min($TargetVMs,20));
// Set node deallocation mode - keep nodes active only until tasks finish
$NodeDeallocationOption = taskcompletion;
```

### 範例 3

根據工作數目調整集區大小的另一個範例，此公式也會考慮集區已設定的 [MaxTasksPerComputeNode](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.maxtaskspercomputenode.aspx) 值。在需要於運算節點上平行執行工作的情況下，這特別有用。

```
// Determine whether 70% of the samples have been recorded in the past 15 minutes; if not, use last sample
$Samples = $ActiveTasks.GetSamplePercent(TimeInterval_Minute * 15);
$Tasks = $Samples < 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1),avg($ActiveTasks.GetSample(TimeInterval_Minute * 15)));
// Set the number of nodes to add to one-fourth the number of active tasks (the MaxTasksPerComputeNode
// property on this pool is set to 4, adjust this number for your use case)
$Cores = $TargetDedicated * 4;
$ExtraVMs = (($Tasks - $Cores) + 3) / 4;
$TargetVMs = ($TargetDedicated+$ExtraVMs);
// Attempt to grow the number of compute nodes to match the number of active tasks, with a maximum of 3
$TargetDedicated = max(0,min($TargetVMs,3));
// Keep the nodes active until the tasks finish
$NodeDeallocationOption = taskcompletion;
```

### 範例 4

此範例示範自動調整公式在初始期間將集區大小設為一定的節點數目，然後在初始期間經過之後，再根據執行中和作用中的工作數目來調整集區大小。

```
string now = DateTime.UtcNow.ToString("r");
string formula = string.Format(@"

	$TargetDedicated = {1};
	lifespan         = time() - time(""{0}"");
	span             = TimeInterval_Minute * 60;
	startup          = TimeInterval_Minute * 10;
	ratio            = 50;

	$TargetDedicated = (lifespan > startup ? (max($RunningTasks.GetSample(span, ratio), $ActiveTasks.GetSample(span, ratio)) == 0 ? 0 : $TargetDedicated) : {1});
	", now, 4);
```

上述程式碼片段中的公式具有下列特性：

- 將初始的集區大小設為 4 個節點
- 在集區生命週期的最初 10 分鐘內不調整集區大小
- 10 分鐘後，取得過去 60 分鐘內執行中和作用中工作數目的最大值
  - 如果這兩個值都是 0 (表示過去 60 分鐘沒有執行或作用中的工作)，集區大小就設為 0
  - 如果其中一個值大於零，則不進行任何變更

## 後續步驟

1. 您可能需要存取運算節點，才能完整評估您應用程式的效率。若要使用遠端存取，必須將使用者帳戶新增至您想要存取的節點，而且必須擷取該節點的 RDP 檔案。
    - 使用以下其中一種方式新增使用者帳戶：
        * [New-AzureBatchVMUser](https://msdn.microsoft.com/library/mt149846.aspx)：此 PowerShell Cmdlet 會採用集區名稱、計算節點名稱、帳戶名稱和密碼做為參數。
        * [BatchClient.PoolOperations.CreateComputeNodeUser](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createcomputenodeuser.aspx)：此 .NET 方法會建立 [ComputeNodeUser](https://msdn.microsoft.com/library/microsoft.azure.batch.computenodeuser.aspx) 類別的執行個體，其中可設定計算節點的帳戶名稱和密碼，然後在執行個體上呼叫 [ComputeNodeUser.Commit](https://msdn.microsoft.com/library/microsoft.azure.batch.computenodeuser.commit.aspx)，在該節點上建立使用者。
        * [將使用者帳戶加入至節點](https://msdn.microsoft.com/library/dn820137.aspx)：集區的名稱與計算節點是在 URI 中指定，而帳戶名稱和密碼則會傳送到這個 REST API 要求的要求主體中的節點。
    - 取得 RDP 檔案：
        * [BatchClient.PoolOperations.GetRDPFile](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.getrdpfile.aspx)：這個 .NET 方法需要集區的識別碼、節點識別碼，以及要建立之 RDP 檔案的名稱。
        * [從節點取得遠端桌面通訊協定檔案](https://msdn.microsoft.com/library/dn820120.aspx)：這個 REST API 要求需要集區的名稱和計算節點的名稱。回應會包含 RDP 檔案的內容。
        * [Get AzureBatchRDPFile](https://msdn.microsoft.com/library/mt149851.aspx)：此 PowerShell Cmdlet 會從指定的計算節點取得 RDP 檔案，並將其儲存至指定的檔案位置或串流。
2.	有些應用程式會產生可能難以處理的大量資料。解決方法之一是透過[有效率的清單查詢](batch-efficient-list-queries.md)。

<!---HONumber=AcomDC_1210_2015-->