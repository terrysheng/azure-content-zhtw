
<properties
	pageTitle="自動調整 Azure 批次集區中的運算節點"
	description="若要完成自動調整，請在集區上啟用自動調整，然後讓公式與用來計算處理應用程式所需的運算節點數目的集區產生關聯。"
	services="batch"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="batch"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="multiple"
	ms.date="07/21/2015"
	ms.author="davidmu"/>

# 自動調整 Azure 批次集區中的運算節點

自動調整 Azure 批次集區中的運算節點就是動態調整應用程式所使用的處理能力。調整方式很容易，可節省時間與金錢。若要深入了解運算節點和集區，請參閱 [Azure 批次技術概觀](batch-technical-overview.md)。

自動調整會在您於集區上啟用時進行，而且公式會與集區產生關聯。公式用來決定處理應用程式所需的運算節點的數目。自動調整可以在建立集區時設定，也可以稍後針對現有的集區設定。此公式也可以在啟用自動調整所在的集區上更新。

啟用自動調整時，根據公式，每隔 15 分鐘會調整一次可用的運算節點數目。公式是以每隔 5 秒所收集的範例為對象，但在收集範例和提供給公式之間有 75 秒的延遲。使用以下所述的 GetSample 方法時，必須考量這些時間因素。

將公式指派給集區之前進行評估永遠是非常好的做法，因此，監視自動調整執行的狀態非常重要。

> [AZURE.NOTE]每個 Azure 批次帳戶限制為可用於處理的運算節點的數目上限。系統會建立高達該限制的節點，而且可能無法達到公式所指定的目標數目。

## 定義公式

您定義的公式用來決定集區中下一個處理間隔的可用運算節點數目。此公式是一個字串，大小不得超過 8KB，且最多可以包含 100 個陳述式，並以分號分隔。

公式中的陳述式是任意格式的運算式。這些陳述式可能包含任何系統定義的變數、使用者定義的變數、常數值，以及這些變數或常數支援的作業：

	VAR = Function(System defined variables, user-defined variables);

您可以結合變數以產生複雜的公式：

	VAR₀ = Function₀(system-defined variables);
	VAR₁ = Function₁(system-defined variables, VAR₀);

### 變數

系統定義的變數和使用者定義的變數可以在公式中使用。您可以設定這些系統定義的變數值，以管理集區中的運算節點。

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
    <td>$TVMDeallocationOption</td>
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

您僅能讀取這些系統定義的變數值，根據範例中運算節點的度量，進行調整。

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
    <td>$SampleTVMCount</td>
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

在公式中支援以下類型：

- double
- doubleVec
- 字串
- timestamp
- timeinterval
	- TimeInterval_Zero
	- TimeInterval_100ns
	- TimeInterval_Microsecond
	- TimeInterval_Millisecond
	- TimeInterval_Second
	- TimeInterval_Minute
	- TimeInterval_Hour
	- TimeInterval_Day
	- TimeInterval_Week
	- TimeInterval_Year

### 作業

以上列出的類型允許這些作業。

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

這些預先定義的函式可用來定義自動調整公式。

<table>
  <tr>
    <th>函式</th>
    <th>說明</th>
  </tr>
  <tr>
    <td>double avg(doubleVecList)</td>
    <td>DoubleVecList 中所有值的平均值。</td>
  </tr>
  <tr>
    <td>double len(doubleVecList)</td>
    <td>從 doubleVecList 建立的向量的長度。</td>
  <tr>
    <td>double lg(double)</td>
    <td>對數底數 2。</td>
  </tr>
  <tr>
    <td>doubleVec lg(doubleVecList)</td>
    <td>逐元對數底數 2。vec(double) 必須針對單一 double 參數明確傳遞，否則會假設為 double lg(double) 版本。</td>
  </tr>
  <tr>
    <td>double ln(double)</td>
    <td>自然對數。</td>
  </tr>
  <tr>
    <td>doubleVec ln(doubleVecList)</td>
    <td>逐元對數底數 2。vec(double) 必須針對單一 double 參數明確傳遞，否則會假設為 double lg(double) 版本。</td>
  </tr>
  <tr>
    <td>double log(double)</td>
    <td>對數底數 10。</td>
  </tr>
  <tr>
    <td>doubleVec log(doubleVecList)</td>
    <td>逐元對數底數 10。vec(double) 必須針對單一 double 參數明確傳遞，否則會假設為 double log(double) 版本。</td>
  </tr>
  <tr>
    <td>double max(doubleVecList)</td>
    <td>doubleVecList 中的最大值。</td>
  </tr>
  <tr>
    <td>double min(doubleVecList)</td>
    <td>doubleVecList 中的最小值。</td>
  </tr>
  <tr>
    <td>double norm(doubleVecList)</td>
    <td>從 doubleVecList 建立的向量的 2-norm。
  </tr>
  <tr>
    <td>double percentile(doubleVec v, double p)</td>
    <td>向量 v 的百分位數元素。</td>
  </tr>
  <tr>
    <td>double rand()</td>
    <td>介於 0.0 到 1.0 之間的隨機值。</td>
  </tr>
  <tr>
    <td>double range(doubleVecList)</td>
    <td>doubleVecList 中最小和最大值之間的差異。</td>
  </tr>
  <tr>
    <td>double std(doubleVecList)</td>
    <td>doubleVecList 中值的標準差範例。</td>
  </tr>
  <tr>
    <td>stop()</td>
    <td>停止自動調整運算式評估。</td>
  </tr>
  <tr>
    <td>double sum(doubleVecList)</td>
    <td>doubleVecList 所有元件的總和。</td>
  </tr>
  <tr>
    <td>timestamp time(string dateTime="")</td>
    <td>若未傳遞參數，為目前時間的時間戳記，否則為 dateTime 字串的時間戳記。支援的 dateTime 格式為 W3CDTF 和 RFC1123。</td>
  </tr>
  <tr>
    <td>double val(doubleVec v, double i)</td>
    <td>向量 v 中位置 i 的元素值，起始索引為零。</td>
  </tr>
  <tr>
    <td>doubleVec vec(doubleVecList)</td>
    <td>從 doubleVecList 明確地建立單一 doubleVec。</td>
  </tr>
</table>

資料表中所述的某些函式可以接受清單做為引數。逗號分隔清單是 double 和 doubleVec 的任意組合。例如：

	doubleVecList := ( (double | doubleVec)+(, (double | doubleVec) )* )?

評估之前，doubleVecList 值會轉換成單一的 doubleVec。例如，如果 v = [1,2,3]，則呼叫 avg(v) 相當於呼叫 avg(1,2,3)，而呼叫 avg(v, 7) 相當於呼叫 avg(1,2,3,7)。

### 範例資料

系統定義的變數是可提供方法來存取相關聯資料的物件。例如，下列運算式顯示取得最後五分鐘的 CPU 使用量的要求：

	$CPUPercent.GetSample(TimeInterval_Minute*5)

可以用來取得範例資料的這些方法。

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
    <td><p>傳回資料向量範例。例如：</p>
        <ul>
          <li><p><b>doubleVec GetSample(double count)</b>：指定最近範例中所需範例的數目。</p>
				  <p>一個範例值得 5 秒的度量資料。GetSample(1) 會傳回最後一個可用的範例，但是您不得對 $CPUPercent 之類的度量使用這個函式，因為您無法得知收集範例的時間。此範例可能是最新的，也可能因為系統問題，是更舊的。最好使用如下所示的時間間隔。</p></li>
          <li><p><b>doubleVec GetSample((timestamp | timeinterval) startTime [, double samplePercent])</b>：指定收集範例資料的時間範圍，並選擇性地指定必須在要求的範圍內的範例百分比。</p>
          <p>如果 CPU Percent 歷程記錄中有最後十分鐘的所有範例，則 $CPUPercent.GetSample(TimeInterval_Minute*10) 應該會傳回 200 個範例。如果最新的歷程記錄仍不存在，則只會傳回 180 個範例。</p>
					<p>$CPUPercent.GetSample (TimeInterval_Minute\ * 10，80) 成功，且 $CPUPercent.GetSample(TimeInterval_Minute*10,95) 失敗。</p></li>
          <li><p><b>doubleVec GetSample((timestamp | timeinterval) startTime, (timestamp | timeinterval) endTime [, double samplePercent])</b>：使用開始時間和結束時間指定收集資料的時間範圍。</p></li></ul></td>
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
    <p><b>doubleVec GetSamplePercent( (timestamp | timeinterval) startTime [, (timestamp | timeinterval) endTime] )</b>：因為 GetSample 方法在傳回範例的百分比小於指定的 samplePercent 時會失敗，因此，您可以使用 GetSamplePercent 方法進行第一次檢查，然後在範例不足時執行替代動作，而不暫停其自動調整評估。</p></td>
  </tr>
</table>

### 度量

可以在公式中定義的這些度量。

<table>
  <tr>
    <th>度量</th>
    <th>說明</th>
  </tr>
  <tr>
    <td>資源</td>
    <td><p>根據 CPU 使用量、頻寬使用量、記憶體使用量和運算節點的數目。上述的這些系統變數會在公式中使用，以管理集區中的運算節點：</p>
    <p><ul>
      <li>$TargetDedicated</li>
      <li>$TVMDeallocationOption</li>
    </ul></p>
    <p>這些系統變數用於根據節點度量進行調整：</p>
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
    <p>此範例所顯示的公式是在過去 10 分鐘的平均 CPU 使用量下限高於 70% 時，用於將集區中的運算節點數目設定為目前節點目標數目的 110%：</p>
    <p><b>totalTVMs = (min($CPUPercent.GetSample(TimeInterval_Minute*10)) > 0.7) ? ($CurrentDedicated * 1.1) : $CurrentDedicated;</b></p>
    <p>此範例所顯示的公式是在過去 60 分鐘的平均 CPU 使用量低於 20% 時，用於將集區中的運算節點數目設定為目前節點目標數目的 90%：</p>
    <p><b>totalTVMs = (avg($CPUPercent.GetSample(TimeInterval_Minute*60)) &lt; 0.2) ? ($CurrentDedicated * 0.9) : totalTVMs;</b></p>
    <p>此範例將專用運算節點的目標數目設定為上限 400 個：</p>
    <p><b>$TargetDedicated = min(400, totalTVMs);</b></p></td>
  </tr>
  <tr>
    <td>工作</td>
    <td><p>根據工作的狀態，例如作用中、擱置和已完成。</p>
    <p>這些系統變數用於根據工作度量進行調整：</p>
    <p><ul>
      <li>$ActiveTasks</li>
      <li>$RunningTasks</li>
      <li>$SucceededTasks</li>
      <li>$FailedTasks</li>
      <li>$CurrentDedicated</li></ul></p>
    <p>此範例所顯示的公式會偵測是否已在過去 15 分鐘內記錄 70% 的範例。如果沒有，它會使用最後一個範例。它會嘗試增加運算節點的數目 (最多 3 個) 以符合作用中工作的數目。由於集區的 MaxTasksPerVM 屬性設為 4，因此，它會將節點數目設定為作用中工作數目的四分之一。它也會將 Deallocation 選項設定為 "taskcompletion" 以保留機器，直到工作完成為止。</p>
    <p><b>$Samples = $ActiveTasks.GetSamplePercent(TimeInterval_Minute * 15); $Tasks = $Samples &lt; 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1),avg($ActiveTasks.GetSample(TimeInterval_Minute * 15))); $Cores = $TargetDedicated * 4; $ExtraVMs = ($Tasks - $Cores) / 4; $TargetVMs = ($TargetDedicated+$ExtraVMs);$TargetDedicated = max(0,min($TargetVMs,3)); $TVMDeallocationOption = taskcompletion;</b></p></td>
  </tr>
</table>

## 評估自動調整公式

在應用程式中使用公式之前先進行評估永遠是非常好的做法。公式的評估方式是針對現有的集區執行測試。使用下列其中一種方法執行這項操作：

- [IPoolManager.EvaluateAutoScale 方法](https://msdn.microsoft.com/library/azure/dn931617.aspx)或[IPoolManager.EvaluateAutoScaleAsync 方法](https://msdn.microsoft.com/library/azure/dn931545.aspx)：這些 .NET 方法需要現有集區的名稱，以及包含自動調整公式的字串。呼叫的結果會包含 [AutoScaleEvaluation 類別](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscaleevaluation.aspx)的執行個體中。
- [評估自動調整公式](https://msdn.microsoft.com/library/azure/dn820183.aspx)：在這個 REST 作業中，集區名稱會在 URI 中指定，而自動調整公式則會在要求主體的 autoScaleFormula 元素中指定。作業的回應會包含可能與公式相關的任何錯誤資訊。

## 在自動調整啟用時建立集區

以下列其中一種方式建立集區：

- [New-AzureBatchPool](https://msdn.microsoft.com/library/azure/mt125936.aspx)：此 Cmdlet 會使用 AutoScaleFormula 參數指定自動調整公式。
- [IPoolManager.CreatePool 方法](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.ipoolmanager.createpool.aspx)：呼叫這個 .NET 方法以建立集區之後，會在集區上設定 [ICloudPool.AutoScaleEnabled 屬性](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.icloudpool.autoscaleenabled.aspx)和 [ICloudPool.AutoScaleFormula 屬性](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.icloudpool.autoscaleformula.aspx)，以啟用自動調整。
- [將集區加入至帳戶](https://msdn.microsoft.com/library/azure/dn820174.aspx)：在這個 REST API 中會使用 enableAutoScale 和 autoScaleFormula 元素，以便在集區建立時設定集區的自動調整。

> [AZURE.NOTE]如果您在集區建立時使用以上所提及的資源設定自動調整，則不會使用集區的 targetDedicated 參數。

## 集區建立之後啟用自動調整

如果您已經使用 targetDedicated 參數設定具有指定運算節點數目的集區，則您稍後可以更新現有的集區以自動調整。您可以使用以下其中一種方式執行這項操作：

- [IPoolManager.EnableAutoScale 方法](https://msdn.microsoft.com/library/azure/dn931709.aspx)：這個 .NET 方法需要現有集區的名稱和自動調整公式。
- [啟用/停用自動調整](https://msdn.microsoft.com/library/azure/dn820173.aspx)：這個 REST API 需要 URI 中現有集區的名稱和要求主體中的自動調整公式。

> [AZURE.NOTE]如果評估自動調整公式，則會忽略集區建立時，針對 targetDedicated 參數所指定的值。

## 取得自動調整執行的相關資訊

您應該定期檢查自動調整執行的結果。使用以下其中一種方式執行這項操作：

- [ICloudPool.AutoScaleRun 屬性](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.icloudpool.autoscalerun.aspx)：使用 .NET 程式庫時，集區的這個屬性會提供 [AutoScaleRun 類別](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.aspx)的執行個體，以提供 [AutoScaleRun.Error 屬性](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.error.aspx)、[AutoScaleRun.Results 屬性](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.results.aspx)和 [AutoScaleRun.Timestamp 屬性](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.timestamp.aspx)。
- [取得集區的相關資訊](https://msdn.microsoft.com/library/dn820165.aspx)：這個 REST API 會傳回集區的相關資訊，其中包含最新的自動調整執行。

## 後續步驟

1.	您可能需要存取運算節點，才能完整評估您應用程式的效率。若要使用遠端存取，必須將使用者帳戶新增至您想要存取的運算節點，而且必須從該節點擷取 RDP 檔案。使用以下其中一種方式新增使用者帳戶：

	- [New-AzureBatchVMUser](https://msdn.microsoft.com/library/mt149846.aspx)：此 Cmdlet 會採用集區名稱、運算節點名稱、帳戶名稱和密碼做為參數。
	- [IVM.CreateUser 方法](https://msdn.microsoft.com/library/microsoft.azure.batch.ivm.createuser.aspx)：這個 .NET 方法會建立可以為運算節點設定帳戶名稱和密碼所在 [IUser 介面](https://msdn.microsoft.com/library/microsoft.azure.batch.iuser.aspx)的執行個體。
	- [將使用者帳戶加入至節點](https://msdn.microsoft.com/library/dn820137.aspx)：集區的名稱與運算節點是在 URI 中指定，而帳戶名稱和密碼則會傳送到這個 REST API 的要求主體中的節點。

		取得 RDP 檔案：

	- [IVM.GetRDPFile 方法](https://msdn.microsoft.com/library/microsoft.azure.batch.ivm.getrdpfile.aspx)：這個 .NET 方法需要要建立的 RDP 檔案的名稱。
	- [從節點取得遠端桌面通訊協定檔案](https://msdn.microsoft.com/library/dn820120.aspx)：這個 REST API 需要集區的名稱和運算節點的名稱。回應會包含 RDP 檔案的內容。
	- [Get AzureBatchRDPFile](https://msdn.microsoft.com/library/mt149851.aspx)：此 Cmdlet 會從指定的運算節點取得 RDP 檔案，並將其儲存至指定的檔案位置或資料流。
2.	有些應用程式會產生可能難以處理的大量資料。解決這個問題的其中一種方式是透過[有效率的清單查詢](batch-efficient-list-queries.md)。

<!---HONumber=July15_HO4-->