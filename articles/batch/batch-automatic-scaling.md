
<properties
	pageTitle="自動調整 Azure Batch 集區中的計算節點 | Microsoft Azure"
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
	ms.date="01/08/2015"
	ms.author="marsma"/>

# 自動調整 Azure Batch 集區中的運算節點

Azure Batch 中的自動調整功能會自動調整您的應用程式所使用的處理能力，並在作業執行期間以動態方式新增或移除計算節點。此自動調整可以節省時間和金錢。

讓自動調整公式與集區 (例如 [Batch .NET](batch-dotnet-get-started.md) 程式庫中的 [PoolOperations.EnableAutoScale][net_enableautoscale] 方法) 產生關聯，以在計算節點的集區上啟用自動調整。Batch 服務會接著使用此公式來判斷要執行您的工作負載所需的計算節點數目。使用定期收集的服務度量資料樣本時，集區中的計算節點數目會根據相關聯的公式在可設定的間隔進行調整。

自動調整可以在建立集區時啟用，或在現有的集區上啟用，而且您可以在已啟用自動調整的集區上變更現有的公式。Batch 可讓您在將公式指派給集區之前先評估公式，以及監視自動調整回合的狀態。

## 自動調整公式

自動調整公式是包含一或多個陳述式的字串值，已指派給集區的 [autoScaleFormula][rest_autoscaleformula] 元素 (Batch REST API) 或 [CloudPool.AutoScaleFormula][net_cloudpool_autoscaleformula] 屬性 (Batch .NET API)。您可定義這些公式，若已指派給集區，公式則會決定集區中可供下一個處理間隔使用的計算節點數目 (稍後會詳細說明間隔)。公式字串的大小不得超過 8KB、最多只能包含 100 個陳述式 (以分號隔開)，而且可以包含換行和註解。

您可以將自動調整公式視為使用 Batch 自動調整「語言」。 公式陳述式會任意格式的運算式，可以包含系統與使用者定義的變數和常數，並可使用內建類型、運算子和函數對這些值執行各種作業。例如，陳述式可能會採用下列格式：

`VAR = Expression(system-defined variables, user-defined variables);`

公式通常包含多個陳述式，其可對在先前陳述式中取得的值執行作業：

```
VAR₀ = Expression₀(system-defined variables);
VAR₁ = Expression₁(system-defined variables, VAR₀);
```

在公式中使用陳述式，您的目標是要達到應該調整集區時的計算節點數目，也就是**專用節點**的**目標**數目。此「目標專用」數目可能會更高、更低，或與集區中目前的節點數目相同。Batch 服務會在特定間隔評估集區的自動調整公式 (下面會討論[自動調整間隔](#interval))，以及將集區中的目標節點數目調整為自動調整公式在評估時指定的數目。

舉一個快速範例，以下兩行自動調整公式會根據作用中的工作數目指定應該調整的節點數目 (最多 10 個計算節點)：

```
$averageActiveTaskCount = avg($ActiveTasks.GetSample(TimeInterval_Minute * 15));
$TargetDedicated = min(10, $averageActiveTaskCount);
```

本文的後續幾節會討論將構成自動調整公式的各種實體，包括變數、運算子、作業和函數。您將了解如何取得 Batch 內的各種計算資源和工作度量，您便可根據資源使用量和工作狀態以智慧方式調整集區的節點計數。然後，您將了解如何使用 Batch REST 和 .NET API，建立公式以及對集區啟用自動調整，而我們最後將以幾個範例公式加以說明。

> [AZURE.NOTE]每個 Azure Batch 帳戶限制為可用於處理的運算節點的數目上限。Batch 服務建立的節點數目最多達到該限制，因此不會達到公式所指定的目標數目。如需檢視和增加帳戶配額的相關資訊，請參閱 [Azure Batch 服務的配額和限制](batch-quota-limit.md)。

## <a name="variables"></a>變數

系統和使用者定義的變數都可以在公式中使用。在上述兩行範例公式中，`$TargetDedicated` 是系統定義的變數，而 `$averageActiveTaskCount` 是使用者定義的變數。下表顯示 Batch 服務所定義的讀寫和唯讀變數。

取得並設定這些**系統定義的變數**值，以管理集區中的計算節點：

<table>
  <tr>
    <th>變數 (讀寫)</th>
    <th>說明</th>
  </tr>
  <tr>
    <td>$TargetDedicated</td>
    <td>集區的<b>專用運算節點</b>的<b>目標</b>數目。這是應該調整集區時的計算節點數目。它是「目標」數目，因為集區可能不會達到此目標節點數目。如果在集區達到初始目標之前，後續的自動調整評估再次修改目標節點數目，或由於在達到目標節點數目前便達到 Batch 帳戶節點或核心配額，可能會發生這種情形。</td>
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

取得這些**系統定義的變數**值，以根據 Batch 服務提供的度量進行調整：

<table>
  <tr>
    <th>變數 (唯讀)</th>
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

> [AZURE.TIP]上述系統定義的唯讀變數是可提供各種方法來存取相關聯資料的「物件」。如需詳細資訊，請參閱下面的[取得樣本資料](#getsampledata)。

## 類型

公式中支援以下**類型**。

- double
- doubleVec
- doubleVecList
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

## 作業

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

## 函式

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

## <a name="getsampledata"></a>取得樣本資料

自動調整公式會使用 Batch 服務所提供的度量資料 (樣本)，並根據公式從服務取得的值擴大或縮減集區大小。上述系統定義的變數是可提供各種方法來存取與該物件相關聯資料的物件。例如，下列運算式顯示取得最後五分鐘的 CPU 使用量的要求：

`$CPUPercent.GetSample(TimeInterval_Minute * 5)`

<table>
  <tr>
    <th>方法</th>
    <th>說明</th>
  </tr>
  <tr>
    <td>GetSample()</td>
    <td><p><b>GetSample()</b> 方法會傳回資料樣本的向量。
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
		<td>Count()</td>
		<td>傳回度量歷程記錄中的範例總數。</td>
	</tr>
  <tr>
    <td>HistoryBeginTime()</td>
    <td>傳回度量的最舊可用資料範例的時間戳記。</td>
  </tr>
  <tr>
    <td>GetSamplePercent()</td>
    <td><p>傳回指定的時間間隔內可用的樣本百分比。例如：</p>
    <p><b>doubleVec GetSamplePercent( (timestamp | timeinterval) startTime [, (timestamp | timeinterval) endTime] )</b>
	<p>因為 GetSample 方法在傳回樣本的百分比小於指定的 samplePercent 時會失敗，因此，您可以使用 GetSamplePercent 方法進行第一次檢查，然後在樣本不足時執行替代動作，而不暫停自動調整評估。</p></td>
  </tr>
</table>

### 樣本、樣本百分比和 *GetSample()* 方法

取得工作和資源度量資料並根據該資料調整集區大小，是自動調整公式的核心作業。因此，請務必清楚了解自動調整公式如何與度量資料或「樣本」互動。

**範例**

Batch 服務會定期取得工作和資源度量的樣本，使其可供自動調整公式使用。Batch 服務會每隔 30 秒記錄一次這些樣本，不過，通常會有一些延誤，以致記錄樣本的時間與樣本可供自動調整公式使用 (與讀取) 的時間之間有所延遲。此外，由於各種因素 (例如網路或其他基礎結構問題)，可能未在特定間隔內記錄樣本，進而導致樣本「遺失」。

**樣本百分比**

將 `samplePercent` 傳遞至 `GetSample()` 方法，或呼叫 `GetSamplePercent()` 方法時，"percent" 是指 Batch 服務「可能」記錄的樣本總數，與自動調整公式實際「可用」的樣本數目之間的比較。

讓我們以 10 分鐘的時間範圍為例。因為會每隔 30 秒記錄一次樣本，所以在 10 分鐘的時間範圍內，Batch 服務所記錄的樣本總數就已達到 20 個樣本 (每分鐘 2 個)。不過，由於回報機制固有的延遲，或 Azure 基礎結構中的一些其他問題，可能只有 15 個樣本可供自動調整公式讀取。這表示，在這 10 分鐘的期間內，記錄的樣本總數只有 **75%** 實際可供您的公式使用。

**GetSample() 和樣本範圍**

自動調整公式會讓您的集區擴大和縮減 (新增或移除其中的節點)，而因為您需對節點付費，所以您想確保您的公式是根據足夠的資料做出明智的決策。因此，建議您在公式中使用趨勢類型分析，並根據所收集樣本的範圍來擴大和縮減集區。

若要這樣做，請使用 `GetSample(interval look-back start, interval look-back end)` 傳回樣本的**向量**：

`runningTasksSample = $RunningTasks.GetSample(1 * TimeInterval_Minute, 6 * TimeInterval_Minute);`

Batch 評估上述程式碼後，它會以值的向量形式傳回樣本範圍，例如：

`runningTasksSample=[1,1,1,1,1,1,1,1,1,1];`

收集樣本向量後，您便可使用 `min()`、`max()` 和 `avg()` 等函數從所收集的範圍衍生出有意義的值。

若要增加安全性，如果特定一段時間可用的樣本小於特定百分比，您可以強制公式評估為*失敗*。如果無法使用指定的樣本百分比，而且將不會變更集區大小，則強制公式評估為失敗會指示 Batch 停止進一步評估公式。若要指定評估成功所需的樣本百分比，請將其指定為 `GetSample()` 的第三個參數。以下指定了 75% 的樣本需求：

`runningTasksSample = $RunningTasks.GetSample(60 * TimeInterval_Second, 120 * TimeInterval_Second, 75);`

由於先前提到的樣本可用性延遲，所以也請務必記得指定回顧開始時間早於一分鐘的時間範圍。這是因為樣本需要花大約一分鐘的時間才能傳播到整個系統，所以通常無法使用 `(0 * TimeInterval_Second, 60 * TimeInterval_Second)` 範圍中的樣本。同樣地，您可以使用 `GetSample()` 的百分比參數來強制特定樣本百分比需求。

> [AZURE.IMPORTANT]**強烈建議**您**避免只依賴自動調整公式中的 `GetSample(1)`**。這是因為 `GetSample(1)` 基本上會向 Batch 服務表示：「不論您多久以前取得最後一個樣本，請將它提供給我。」 因為它只是單一樣本，而且可能是較舊的樣本，所以可能無法代表最近工作或資源狀態的全貌。如果您使用 `GetSample(1)`，請確定它是較大的陳述式，而且不是您的公式所依賴的唯一資料點。

## 度量

在定義公式、根據您取得和評估的度量資料來調整集區中專用節點的目標數目時，您可以同時使用**資源**和**工作度量**。如需每個度量的詳細資訊，請參閱上面的[變數](#variables)一節。

<table>
  <tr>
    <th>度量</th>
    <th>說明</th>
  </tr>
  <tr>
    <td><b>Resource</b></td>
    <td><p><b>資源度量</b>是以計算節點的 CPU、頻寬和記憶體使用量以及節點數目為基礎的度量。</p>
		<p> 這些系統定義的變數適合用於根據節點計數進行調整：</p>
    <p><ul>
      <li>$TargetDedicated</li>
			<li>$CurrentDedicated</li>
			<li>$SampleNodeCount</li>
    </ul></p>
    <p>這些系統定義的變數適合用於根據節點資源使用量進行調整：</p>
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
    <td><b>Task</b></td>
    <td><p><b>工作度量</b>是以工作狀態 (例如使用中、暫止和已完成) 為基礎的度量。下列系統定義的變數適合用於根據工作度量進行集區大小調整：</p>
    <p><ul>
      <li>$ActiveTasks</li>
      <li>$RunningTasks</li>
      <li>$SucceededTasks</li>
			<li>$FailedTasks</li></ul></p>
		</td>
  </tr>
</table>

## 建立自動調整公式

自動調整公式的建構方式是使用上述元素撰寫陳述式，再將這些陳述式結合成完整的公式。比方說，我們在這裡建構公式是先定義公式的需求：

1. 如果 CPU 使用率偏高，則增加集區中運算節點的目標數目
2. 當 CPU 使用率偏低時，減少集區中運算節點的目標數目
3. 一律以 400 為節點的數目上限

針對在高 CPU 使用率期間*增加*節點，我們將陳述式定義為如果在過去 10 分鐘期間 CPU 使用率的平均最小值大於 70%，則在使用者定義的變數 ($TotalNodes) 中填入節點目前目標數目的 110%：

`$TotalNodes = (min($CPUPercent.GetSample(TimeInterval_Minute*10)) > 0.7) ? ($CurrentDedicated * 1.1) : $CurrentDedicated;`

下一個陳述式定義如果過去 60 分鐘的平均 CPU 使用率*低於* 20%，則將相同的變數設定為節點目前目標數目的 90%，將低 CPU 使用率期間的目標數目降低。請注意，此陳述式也會參考上述陳述式中使用者定義的變數 *$TotalNodes*。

`$TotalNodes = (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ? ($CurrentDedicated * 0.9) : $TotalNodes;`

現在將專用計算節點的目標數目設定為**上限** 400 個：

`$TargetDedicated = min(400, $TotalNodes)`

以下是完整的公式：

```
$TotalNodes = (min($CPUPercent.GetSample(TimeInterval_Minute*10)) > 0.7) ? ($CurrentDedicated * 1.1) : $CurrentDedicated;
$TotalNodes = (avg($CPUPercent.GetSample(TimeInterval_Minute*60)) < 0.2) ? ($CurrentDedicated * 0.9) : $TotalNodes;
$TargetDedicated = min(400, $TotalNodes)
```

> [AZURE.NOTE]自動調整公式由 [Batch REST][rest_api] API 變數、類型、作業和函式所組成。甚至在使用 [Batch .NET][net_api] 程式庫時，這些也都會用在公式字串中。

## 在自動調整啟用時建立集區

若要在建立集區時啟用自動調整，請採用下列其中一項技術：

- [New-AzureBatchPool](https://msdn.microsoft.com/library/azure/mt125936.aspx)：此 Azure PowerShell Cmdlet 會使用 AutoScaleFormula 參數指定自動調整公式。
- [BatchClient.PoolOperations.CreatePool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx)：呼叫這個 .NET 方法以建立集區之後，您接著要設定集區的 [CloudPool.AutoScaleEnabled](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleenabled.aspx) 和 [CloudPool.AutoScaleFormula](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleformula.aspx) 屬性，以啟用自動調整。
- [將集區加入至帳戶](https://msdn.microsoft.com/library/azure/dn820174.aspx)：在這個 REST API 要求中會使用 enableAutoScale 和 autoScaleFormula 元素，以便在集區建立時設定集區的自動調整。

> [AZURE.IMPORTANT]如果您使用上述其中一項技術建立已啟用自動調整的集區，則**不**得指定集區的 *targetDedicated* 參數。也請注意，如果您想要對已啟用自動調整的集區手動調整大小 (例如使用 [BatchClient.PoolOperations.ResizePool][net_poolops_resizepool])，則必須先在集區**停用**自動調整，然後調整其大小。

下列程式碼片段顯示如何使用 [Batch .NET][net_api] 程式庫建立啟用自動調整的 [CloudPool][net_cloudpool]，其公式在星期一將節點目標數目設定為 5，在一週的其他每天設定為 1。此外，自動調整間隔會設定為 30 分鐘 (請參閱下面的[自動調整間隔](#interval))。在本文中的此部分與其他 C# 程式碼片段中，"myBatchClient" 是適當初始化的 [BatchClient][net_batchclient] 執行個體。

```
CloudPool pool = myBatchClient.PoolOperations.CreatePool("mypool", "3", "small");
pool.AutoScaleEnabled = true;
pool.AutoScaleFormula = "$TargetDedicated = (time().weekday==1?5:1);";
pool.AutoScaleEvaluationInterval = TimeSpan.FromMinutes(30);
pool.Commit();
```

### <a name="interval"></a>自動調整間隔

依預設，Batch 服務會根據其自動調整公式每隔 **15 分鐘**調整集區的大小。不過，可使用下列的集區屬性設定此間隔：

- REST API - [autoScaleEvaluationInterval][rest_autoscaleinterval]
- .NET API - [CloudPool.AutoScaleEvaluationInterval][net_cloudpool_autoscaleevalinterval]

最小間隔為 5 分鐘，而最大間隔為 168 小時。如果指定此範圍以外的時間間隔，則 Batch 服務會傳回「不正確的要求 (400)」錯誤。

> [AZURE.NOTE]自動調整目前不適合做為低於一分鐘的變更回應，但反而要在您執行工作負載時逐步調整您的集區大小。

## 集區建立之後啟用自動調整

如果您已經使用 *targetDedicated* 參數設定具有指定計算節點數目的集區，則您稍後可以更新現有的集區以自動調整。使用以下其中一種方式執行這項操作：

- [BatchClient.PoolOperations.EnableAutoScale][net_enableautoscale]：這個 .NET 方法需要現有集區的識別碼，以及要套用至集區的自動調整公式。
- [在集區上啟用自動調整][rest_enableautoscale]：這個 REST API 要求需要 URI 中現有集區的識別碼，以及要求主體中的自動調整公式。

> [AZURE.NOTE]如果建立集區時指定 *targetDedicated* 參數的值，則評估自動調整公式時會忽略此值。

此程式碼片段示範使用 [Batch .NET][net_api] 程式庫在現有的集區啟用自動調整。請注意，在現有的集區上啟用和更新公式都使用相同的方法。因此，如果自動調整已啟用，則這項技術會在指定的集區*更新*公式。程式碼片段會假設 "mypool" 是現有 [CloudPool][net_cloudpool] 的識別碼。

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

在這個使用 [Batch .NET][net_api] 程式庫的程式碼片段中，我們在將公式套用至 [CloudPool][net_cloudpool] 之前先評估公式。

```
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
```

成功評估此程式碼片段中的公式會產生類似下列的輸出：

`AutoScaleRun.Results: $TargetDedicated = 10;$NodeDeallocationOption = requeue;$CurTime = 2015 - 08 - 25T20: 08:42.271Z;$IsWeekday = 1;$IsWorkingWeekdayHour = 0;$WorkHours = 0`

## 取得自動調整執行的相關資訊

定期檢查自動調整回合的結果，以確保公式如預期般執行。

- [CloudPool.AutoScaleRun](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscalerun.aspx)：使用 .NET 程式庫時，集區的這個屬性會提供 [AutoScaleRun](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.aspx) 類別的執行個體，以提供最近執行的自動調整的下列屬性：
  - [AutoScaleRun.Error](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.error.aspx)
  - [AutoScaleRun.Results](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.results.aspx)
  - [AutoScaleRun.Timestamp](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.timestamp.aspx)
- [取得集區的相關資訊](https://msdn.microsoft.com/library/dn820165.aspx)：這個 REST API 要求會傳回集區的相關資訊，其中包含最近執行的自動調整。

## <a name="examples"></a>公式範例

讓我們看看一些範例，其中只顯示公式可用來自動調整集區中的運算資源的幾種方式。

### 範例 1：以時間為基礎的調整

或許您想要根據星期幾和時段來調整集區大小，以相應地增加或減少集區中的節點數目：

```
$CurTime=time();
$WorkHours=$CurTime.hour>=8 && $CurTime.hour<18;
$IsWeekday=$CurTime.weekday>=1 && $CurTime.weekday<=5;
$IsWorkingWeekdayHour=$WorkHours && $IsWeekday;
$TargetDedicated=$IsWorkingWeekdayHour?20:10;
```

此公式會先取得目前的時間。如果是工作日 (1-5) 且在上班時間內 (8AM-6PM)，則將目標集區大小設為 20 個節點。否則目標集區大小會設為 10 個節點。

### 範例 2：以工作為基礎的調整

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

### 範例 3：考量平行工作

根據工作數目調整集區大小的另一個範例，此公式也會考慮集區已設定的 [MaxTasksPerComputeNode][net_maxtasks] 值。已在集區上啟用[平行工作執行](batch-parallel-node-tasks.md)的情況下，這特別有用。

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

### 範例 4：設定初始集區大小

此範例顯示的 C# 程式碼片段具有自動調整公式，其在初始期間將集區大小設為一定的節點數目，然後在初始期間經過之後，再根據執行中和作用中的工作數目來調整集區大小。

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

[net_api]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[net_batchclient]: http://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_cloudpool_autoscaleformula]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleformula.aspx
[net_cloudpool_autoscaleevalinterval]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleevaluationinterval.aspx
[net_enableautoscale]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.enableautoscale.aspx
[net_maxtasks]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.maxtaskspercomputenode.aspx
[net_poolops_resizepool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.resizepool.aspx

[rest_api]: https://msdn.microsoft.com/library/azure/dn820158.aspx
[rest_autoscaleformula]: https://msdn.microsoft.com/library/azure/dn820173.aspx
[rest_autoscaleinterval]: https://msdn.microsoft.com/en-us/library/azure/dn820173.aspx
[rest_enableautoscale]: https://msdn.microsoft.com/library/azure/dn820173.aspx

<!---HONumber=AcomDC_0114_2016-->