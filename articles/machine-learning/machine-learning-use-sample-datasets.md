<properties 
	pageTitle="在 Machine Learning Studio 中使用範例資料集 | Azure" 
	description="Azure Machine Learning Studio 所含之範例資料集的說明" 
	services="machine-learning" 
	documentationCenter="" 
	authors="garyericson" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/16/2014" 
	ms.author="garye"/>


# 在 Azure Machine Learning Studio 中使用範例資料集

ML Studio 包含一些可供您使用的範例資料集。這些是來自於公用網域的標準機器學習資料集。 

您在 ML Studio 中探索各種類型的分析機器學習模型時，可以在您的實驗中使用這些資料集。 

- 若要在 ML Studio 中檢視這些標準資料集的清單，請按一下 [**資料集**] 索引標籤。您可以檢視每個資料集的多種資訊，例如資料集的名稱、資料集的提交者，以及簡短說明。
 
    若要排序資料集，請按一下任何資料行標頭。例如，您可以按一下 [**提交者**]，對 Microsoft Corporation 所提供的所有範例資料集進行分組。這樣也可讓您輕鬆檢視您自己和工作區中的其他使用者已匯入的資料集。 

- 若要在實驗中使用資料集，請將模組調色盤的 [**儲存的資料集**] 區段展開至實驗畫布左側，或在調色盤上方的搜尋方塊中輸入名稱，以搜尋特定的資料集。將資料集拖曳到畫布上，以新增至您的實驗。 

##範例資料集

下表提供在您探索 ML Studio 的機器學習功能時可能會有幫助之資料集的其他相關資訊。此表格會列出每個資料集的下列項目：
 
- 每個資料集的名稱和來源。
- 資料集各種用途的說明，以及使用該資料集之機器學習研究的引用。
- 資料集所含之重要資料行的摘要，以及其他實用的屬性。

有些說明節錄自來源所提供的文件 (通常是 [UC Irvine 機器學習儲存機制](http://archive.ics.uci.edu/ml/ "UC Irvine Machine Learning Repository"))，有些說明則是以其他調查為基礎，或反映這些範例中所做的變更。

<table>
<tr valign=top>
<th>資料集名稱</th>
<th>資料集說明</th>
<th>相關研究</th>
</tr>
<tr valign=top>
<td>成人收入普查二進位分類資料集</td>
<td>
<p>1994 年普查資料的子集，使用年齡 16 歲以上的成年工作者，和 > 100 的調整收入指數。</p>
<p><strong>Usage                 :</strong> 使用人口統計將人口分類，以預測個人年收入是否超過 5 萬元。</p>
</td>
<td>
<p>Kohavi, R., Becker, B., (1996)。UCI 機器學習儲存機制 [<a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>]。Irvine, CA：University of California, School of Information and Computer Science</p>
</td>
</tr>

<tr valign=top>
<td>
<p>汽車價格資料 (原始)</p>
</td>
<td>
<p>依構造和型號分類的汽車相關資訊，包括價格、性能 (例如汽缸數和油耗)，以及保險風險評分。</p>
<p>風險評分最初與汽車價格相關聯，然後在保險統計協會稱之為<legacyItalic>符號化</legacyItalic>的程序中根據實際風險進行調整。若值為 +3，表示該汽車屬於高風險，若值為 -3，表示大致而言很安全。</p>
<p><strong>Usage                 :</strong> 使用迴歸或多變量分類，依特性預測風險評分。</p>
</td>
<td>
<p>
Schlimmer, J.C. (1987)。UCI 機器學習儲存機制 [<a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>]。Irvine, CA：University of California, School of Information and Computer Science
</p>
</td>
</tr>

<tr valign=top>
<td>
<p>捐血資料</p>
</td>
<td>
<p>台灣省新竹市捐血服務中心的捐血資料庫包含的資料子集。</p>
  <p>捐血資料包括前次捐血後經過的月數、頻率、或捐血總次數、前次捐血後經過的時間，以及捐血的數量。</p>
  <p><strong>Usage                 :</strong> 其目標是要透過分類來預測捐血者是否是在 2007 年 3 月份捐血；1 表示在目標期間內有 1 名捐血者，0 表示沒有捐血者。</p>
</td>
<td>
<list class="bullet">
<listItem>
<p>
Yeh, I.C., (2008)。UCI 機器學習儲存機制 [<a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>]。Irvine, CA：University of California, School of Information and Computer Science
</p>
</listItem>
<listItem>
<p>
Yeh, I-Cheng, Yang, King-Jang, and Ting, Tao-Ming, "Knowledge discovery on RFM model using Bernoulli sequence, "Expert Systems with Applications, 2008, [<a href="http://dx.doi.org/10.1016/j.eswa.2008.07.018">http://dx.doi.org/10.1016/j.eswa.2008.07.018</a>]
</p>
</listItem>
</list>
</td>
</tr>

<tr valign=top>
<td>
<p>乳癌資料</p>
</td>
<td>
<p>Oncology Institute 所提供的三個癌症相關資料集之一，常出現在機器學習文獻中。結合診斷資訊與約 300 個生物組織樣本的實驗室分析中的特性。</p>
<p><strong>Usage                 :</strong> 根據 9 個屬性 (有些是線性，有些則為類別) 將癌症類型分類。</p>
</td>
<td>
<p>
Wohlberg, W.H., Street, W.N., &amp; Mangasarian, O.L. (1995)。UCI 機器學習儲存機制 [<a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>]。Irvine, CA：University of California, School of Information and Computer Science
</p>
</td>
</tr>

<tr valign=top>
<td>
<p>能量效益迴歸資料</p>
</td>
<td>
<p>模擬能量分佈曲線的集合，以 12 種不同的建築形狀為基礎。建築物以 8 項特性區分不同之處，例如玻璃區域、玻璃區域分佈和方向。</p>
<p><strong>Usage                 :</strong>使用迴歸或分類，可根據兩個實數值回應的其中之一來預測能量效益評等。就多類別分類而言，會將回應變數進位至最接近的整數。</p>
</td>
<td>
<p>
Xifara, A. &amp; Tsanas, A. (2012)。UCI 機器學習儲存機制   [<a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>]。Irvine, CA：University of California, School of Information and Computer Science
</p>
</td>
</tr>

<tr valign=top>
<td>
<p>航班準點率 (原始)</p>
</td>
<td>
<p>2011 年 10 月份美國境內飛機航班起降記錄。</p>
<p><strong>Usage                 :</strong> 預測航班誤點。</p>
</td>
<td>
<p>
取自美國交通部 [<a href="http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time">http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&amp;DB_Short_Name=On-Time</a>]
</p>
</td>
</tr>

<tr valign=top>
<td>
<p>森林火災資料</p>
</td>
<td>
<p>包含葡萄牙東北部地區的天候資料 (例如溫度和濕度指數以及風速)，再加上森林火災記錄。</p>
<p><strong>Usage                 :</strong> 這是一項困難的迴歸工作，其目的是要預測森林火災的燃燒區域。</p>
</td>
<td>
<list class="bullet">
<listItem>
<p>
Cortez, P., &amp; Morais, A. (2008)。UCI 機器學習儲存機制 [<a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>]。Irvine, CA：University of California, School of Information and Computer Science
</p>
</listItem>
<listItem>
<p>
[Cortez and Morais, 2007] P. Cortez and A. Morais. 
一個使用氣象資料預測森林火災的資料採礦方法。 
In J. Neves, M. F. Santos and J. Machado Eds., New Trends in Artificial Intelligence, 
Proceedings of the 13th EPIA 2007 - Portuguese Conference on Artificial Intelligence, 
December, Guimar&#227;es, Portugal, pp.512-523, 2007. APPIA, ISBN-13 978-989-95618-0-9。可從下列位置取得：
[<a href="http://www.dsi.uminho.pt/~pcortez/fires.pdf">http://www.dsi.uminho.pt/~pcortez/fires.pdf</a>]
</p>
</listItem>
</list>
</td>
</tr>

<tr valign=top>
<td>
<p>Iris 雙類別資料</p>
</td>
<td>
<p>這或許是型態辨識文獻中最著名的資料庫。此資料集並不大，內含 50 個範例，說明三個鳶尾花品種的花瓣尺寸。</p>
<p><strong>Usage                 :</strong> 從尺寸預測鳶尾花類型。 
<!-- I believe the following doesn't apply anymore, but I'm not sure so I'll remove it for now.
One class is linearly separable from the other two; but the latter are not linearly separable from each other.
-->
</p>
</td>
<td>
<p>
Fisher, R.A. (1988)。UCI 機器學習儲存機制   [<a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>]。Irvine, CA：University of California, School of Information and Computer Science
</p>
</td>
</tr>

<tr valign=top>
<td>
<p>不同汽車的油耗資料</p>
</td>
<td>
<p>此資料集是根據 Carnegie Mellon University 的 StatLib 圖書館所提供的資料集略做修改而成。此資料集用於 1983 American Statistical Association Exposition。</p>
<p>這項資料以每加侖的里程數為單位列出各種汽車的油耗，再加上汽缸數、引擎排氣量、馬力、總重量和加速等資訊。</p>
<p><strong>Usage                 :</strong> 根據 3 個多重值離散屬性和 5 個連續屬性預測燃料經濟效益。</p>
</td>
<td>
<p>
StatLib, Carnegie Mellon University, (1993)。UCI 機器學習儲存機制 [<a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>]。Irvine, CA：University of California, School of Information and Computer Science
</p>
</td>
</tr>

<tr valign=top>
<td>
<p>皮馬族印第安人糖尿病二進位分類資料集</p>
</td>
<td>
<p>National Institute of Diabetes and Digestive and Kidney Diseases 資料庫中的資料子集。此資料集經過篩選，主要著重於皮馬族印第安人遺傳疾病的女性病患。資料中包含葡萄糖和胰島素水平之類的醫學資料，以及生活方式因素。</p>
<p><strong>Usage                 :</strong> 預測受測者是否有糖尿病 (二進位分類)。</p>
</td>
<td>
<p>
Sigillito, V. (1990)。UCI 機器學習儲存機制 [<a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>]。Irvine, CA：University of California, School of Information and Computer Science
</p>
</td>
</tr>

<tr valign=top>
<td>
<p>餐廳顧客資料</p>
</td>
<td>
<p>一組關於顧客的中繼資料，包括人口統計和喜好。</p>
<p><strong>Usage                 :</strong> 將此資料集與其他兩個餐廳資料集搭配使用，可訓練及測試推薦系統。</p>
</td>
<td>
<p>
Bache, K. and Lichman, M. (2013)。UCI 機器學習儲存機制 
[<a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>]。
Irvine, CA:University of California, School of Information and Computer Science
</p>
</td>
</tr>

<tr valign=top>
<td>
<p>餐廳特色資料</p>
</td>
<td>
<p>一組關於餐廳及其特色的中繼資料，例如食物類型、用餐風格和地點等。</p>
<p><strong>Usage                 :</strong> 將此資料集與其他兩個餐廳資料集搭配使用，可訓練及測試推薦系統。</p>
</td>
<td>
<p>
Bache, K. and Lichman, M. (2013)。UCI 機器學習儲存機制 
[<a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>]。
Irvine, CA:University of California, School of Information and Computer Science
</p>
</td>
</tr>

<tr valign=top>
<td>
<p>餐廳評等</p>
</td>
<td>
<p>包含使用者給予餐廳的評等，最低為 0，最高為 2。</p>
<p><strong>Usage                 :</strong> 將此資料集與其他兩個餐廳資料集搭配使用，可訓練及測試推薦系統。</p>
</td>
<td>
<p>
Bache, K. and Lichman, M. (2013)。UCI 機器學習儲存機制 
[<a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>]。
Irvine, CA:University of California, School of Information and Computer Science
</p>
</td>
</tr>

<tr valign=top>
<td>
<p>煉鋼多類別資料集</p>
</td>
<td>
<p>此資料集包含以產生之鋼鐵類型的實際屬性 (寬度、厚度、類型 (鋼條圈、鋼片等)) 試煉鋼鐵的一系列記錄。</p>
<p><strong>Usage                 :</strong>預測任兩個數值類別屬性；硬度或強度。您也可以分析各屬性之間的關聯。</p>
<p>鋼鐵等級會遵循 SAE 和其他組織所定義的一組標準。您想要尋找特定的「等級」(類別變數)，並了解所需的值。</p>
</td>
<td>
<p>
Sterling, D. &amp; Buntine, W., (NA).UCI 機器學習儲存機制   [<a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>]。Irvine, CA：University of California, School of Information and Computer Science
</p>
<p>鋼鐵等級的實用指南：</p>
<p>[<a href="http://www.outokumpu.com/SiteCollectionDocuments/Outokumpu-steel-grades-properties-global-standards.pdf">http://www.outokumpu.com/SiteCollectionDocuments/Outokumpu-steel-grades-properties-global-standards.pdf>]
  </p>
</td>
</tr>

<tr valign=top>
<td>
<p>望遠鏡資料</p>
</td>
<td>
<p>高能伽瑪粒子爆與背景雜訊的記錄，兩者皆使用蒙地卡羅程序來模擬。</p>
<p>模擬的目的，是要使用統計方法區分所要的訊號 (切倫科夫射線波) 與背景雜訊 (高層大氣中的宇宙射線所引發的強子波)，以改善地面大氣切倫科夫伽瑪望遠鏡的精確性。</p>
<p>這項資料已經過前處理，而建立長軸指向攝相中心的狹長叢集。這個橢圓的特性 (通常稱為 Hillas 參數) 屬於可用於區別的影像參數。</p>
<p><strong>Usage                 :</strong> 預測某個波的影像是代表訊號還是背景雜訊。</p>
<p><strong>注意：</strong>簡單的分類精確性對這項資料並沒有幫助，因為將背景活動分類為訊號，會比將訊號活動分類為背景還要糟。若要比較不同的分類器，應使用 ROC 圖形。將背景活動認定為訊號的機率，必須低於下列其中一個臨界值：0.01、0.02、0.05、0.1 或 0.2。</p>
<p>此外請注意，背景活動的數目 (h 表示強子波) 會被低估，而在實際的測量中，h 或雜訊類別會佔活動的多數。</p>
</td>
<td>
<p>
Bock, R.K. (1995)。UCI 機器學習儲存機制 [<a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>]。Irvine, CA：University of California, School of Information and Computer Science
</p>
</td>
</tr>

</table>

以下列出您可能會看見的資料集類型。

<table>
<tr>
<th>資料格式</th>
<th>說明</th>
</tr>
<tr>
<td>
逗號分隔值 (CSV)
</td>
<td>
適用於所有資料平台的知名交換格式。ML Studio 會載入這項資料，並合併根據取樣自每個資料行中的資料來套用中繼資料的資料行取向類型猜測器。
</td>
</tr>
<tr>
<td>
屬性關係檔案格式 (ARFF)
</td>
<td>
WEKA 所定義的機器學習資料格式包含會直接轉譯為 ML Studio 資料表的中繼資料 (適用於額定、數值和字串類型)。
</td>
</tr>
<tr>
<td>
純文字
</td>
<td>
純文字經讀取後，可在下游前處理模組的輔助下分割為資料行。支援的主要純文字格式為 JSON。
</td>
</tr>
<tr>
<td>
DotNetTable
</td>
<td>
在 ML Studio 中的各模組之間傳遞的主要容器 (<i>資料集</i>) 的序列化版本。
</td>
</tr>
</table>

<!--HONumber=46--> 
 