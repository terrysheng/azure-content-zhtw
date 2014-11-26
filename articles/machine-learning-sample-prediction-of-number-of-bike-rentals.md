<properties title="Azure Machine Learning Sample: Prediction of the number of bike rentals" pageTitle="Machine Learning Sample: Prediction of bike rentals | Azure" description="A sample Azure Machine Learning experiment to develop a regression model that predicts the number of bike rentals hourly." metaKeywords="" services="" solutions="" documentationCenter="" authors="garye" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="garye" />

# Azure Machine Learning 範例：預測單車出租數量

*您可以在 <b>[範例]</b> 索引標籤下的 <b>[實驗]</b> 區段中，找到與 ML Studio 中的這個模型相關聯的範例實驗。實驗名稱為：*

    Sample Experiment - Demand Forecasting of Bikes

## 問題說明

在取得過去的出租記錄、每小時測量的天候、表示今天是假日/工作日/週末的指標後，預測今天每個小時將可租出的單車數量。每個小時的預測並不相同 (例如，
早上會有許多人租車，而晚上則幾乎沒有人租車)。

## 資料

根據在華盛頓特區維護單車出租網路的 Capital Bikeshare 公司所提供的實際資料而建立的「UCI 單車出租」資料集。在此資料集中，2011 和 2012 年每一天的每個小時分別有一個資料列，共計 17379 個資料列。每小時的單車出租量介於 1 到 977 部之間。

## 模型

我們以 2011 年的資料作為訓練集，並以 2012 年的資料作為測試集。我們比較了 4 組特性：

1.  預測日的天候 + 假日 + 工作日 + 週末特性
2.  過去的 12 小時以來，每小時租出的單車數量
3.  過去的 12 天以來，每天在同一個時間租出的單車數量
4.  過去的 12 週以來，在同一天同一個時間租出的單車數量

特性 B 會擷取最近的單車需求。特性 C 會擷取某一個小時的單車需求。特性 D 會擷取一週當中某一天某一個小時的單車需求。

由於標籤 (出租數量) 是實數值，因此我們採用迴歸設定。此外，由於特性數目相對較少 (低於 100)，且並不疏鬆，因此決策界限可能是非線性。基於這幾點，我們決定使用促進式決策樹迴歸演算法。

## 結果


<table border="1">
<tr><th>特性</th><th>平均絕對誤差</th> <th>均方根誤差</th> </tr>
<tr style="background-color: #fff"><td>A</td> <td> 89.7</td> <td>124.9 </td> </tr>
<tr style="background-color: #fff"><td>A+B</td><td>51.2 </td> <td>86.7 </td></tr>
<tr style="background-color: #fff"><td>A+B+C</td><td> 48.5</td> <td> 83.7 </td></tr>
<tr style="background-color: #fff"><td>A+B+C+D</td><td> 48.8 </td> <td>83.2 </td></tr>
</table>

</table>

特性 A+B+C 和 A+B+C+D 所呈現的結果最理想。令人意外的是，A+B+C 在加入特性 D 後並沒有重大改善。

