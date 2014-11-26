<properties title="Azure Machine Learning Sample: CRM task" pageTitle="Machine Learning Sample: CRM task | Azure" description="A sample Azure Machine Learning experiment to develop multiple models that predict customer churn, upsell, and propensity to buy a new product." metaKeywords="" services="" solutions="" documentationCenter="" authors="garye" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="garye" />

# Azure Machine Learning 範例：CRM 工作

*您可以在 <b>[範例]</b> 索引標籤下的 <b>[實驗]</b> 區段中，找到與 ML Studio 中的這個模型相關聯的範例實驗。實驗名稱為：*

    Sample Experiments - CRM - Development

## 問題說明

預測客戶流失 (更換提供者)、向上銷售 (購買升級或附加元件) 和購買新產品的傾向 (慾望)

## 資料

法國 French Telecom Company Orange 5 萬個客戶的資料集。每個客戶有 230 項匿名特性。這項資料取自於 KDD Cup 2009。

其中包含數值和字串特性。這些特性非常稀疏。

## 模型

處理遺漏值是這項資料唯一的前處理。我們以 "0" 取代字串特性中的遺漏值。幾乎所有數值特性皆具有非負值。我們在數值特性的現有值中加入 1，並以 0 取代項目中的遺漏值。如此，我們即可分辨實際原有的 0 與表示遺漏值的 0。為此，我們先針對數值特性套用數學運算 (+1)。接著，我們將所有的遺漏值取代為 0 (或用於字串的 "0")。

由於我們同時有數值和類別特性，因此我們使用促進式決策樹分類器。這 3 個問題皆不平衡，因為正範例屬於少數。在某些情況下，這可能會導致分類器忽略特性空間中具有較少正範例的區域。為了加以測試，我們為每個問題訓練了兩個模型：一個使用原始資料，另一個則複寫正範例，使新的正範例數目約略等於負測試範例的數目。我們先使用簡易 R 指令碼分割正範例與負範例以完成此動作，然後將 13 個正集合複本附加到負集合。

## 結果

模型輸出機率的相關評分，作為每項工作的正結果。我們可以選取任意截止臨界值將觀測值標示為正值，因此我們依據接收者操作特性 (ROC) 曲線 (AUC) 下的區域來測量效能。請注意，就「流失」和「向上銷售」而言，複寫正範例以建立較平衡的集合將可微幅改善模型的效能。


<table border="1">
<tr><td>流失</td><td>AUC</td></tr>
<tr style="background-color: #fff"><td>無複寫</td><td>0.711</td></tr>
<tr><td>複寫的正集合</td><td>0.728</td></tr>
</table>


<table border="0">
<tr><td>向上銷售</td><td>AUC</td></tr>
<tr style="background-color: #fff"><td>無複寫</td><td>0.853</td></tr>
<tr><td>複寫的正集合</td><td>0.865</td></tr>
</table>


<table border="0">
<tr><td>慾望</td><td>AUC</td></tr>
<tr style="background-color: #fff"><td>無複寫</td><td>0.805</td></tr>
<tr><td>複寫的正集合</td><td>0.8</td></tr>
</table>


## API

我們並未實作模型，因為此模型具有匿名特性。

