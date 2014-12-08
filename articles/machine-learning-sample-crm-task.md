<properties title="Azure Machine Learning Sample: CRM task" pageTitle="Machine Learning 範例：CRM 工作 | Azure" description="A sample Azure Machine Learning experiment to develop multiple models that predict customer churn, upsell, and propensity to buy a new product." metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" manager="paulettm" editor="cgronlun"  videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/23/2014" ms.author="garye" />


# Azure Machine Learning 範例：CRM 工作

>[AZURE.NOTE]
>在 ML Studio 中可取得與此模型相關聯的[範例實驗]和[範例資料集]。如需詳細資訊，請參閱下列各項。
[範例實驗]: #sample-experiment
[範例資料集]: #sample-datasets

<!--
- [Problem Description]
- [Data]
- [Model]
- [Results]
- [API]
- [Sample Experiment]
-->
[問題說明]: #problem-description
[資料]: #data
[模型]: #model
[結果]: #results
[API]: #api

## 問題說明 ##

預測客戶流失 (更換提供者)、向上銷售 (購買升級或附加元件) 和購買新產品的傾向 (慾望) 

## 資料 ##

法國 French Telecom Company Orange 5 萬個客戶的資料集。每個客戶有 230 項匿名特性。這項資料取自於 KDD Cup 2009。 

其中包含數值和字串特性。這些特性非常稀疏。
 
## 模型 ##

處理遺漏值是這項資料唯一的前處理。我們以 "0" 取代字串特性中的遺漏值。幾乎所有數值特性皆具有非負值。我們在數值特性的現有值中加入 1，並以 0 取代項目中的遺漏值。如此，我們即可分辨實際原有的 0 與表示遺漏值的 0。為此，我們先針對數值特性套用數學運算 (+1)。接著，我們將所有的遺漏值取代為 0 (或用於字串的 "0")。 

由於我們同時有數值和類別特性，因此我們使用促進式決策樹分類器。這 3 個問題皆不平衡，因為正範例屬於少數。在某些情況下，這可能會導致分類器忽略特性空間中具有較少正範例的區域。為了加以測試，我們為每個問題訓練了兩個模型：一個使用原始資料，另一個則複寫正範例，使新的正範例數目約略等於負測試範例的數目。我們先使用簡易 R 指令碼分割正範例與負範例以完成此動作，然後將 13 個正集合複本附加到負集合。 

## 結果 ##

模型輸出機率的相關評分，作為每項工作的正結果。我們可以選取任意截止臨界值將觀測值標示為正值，因此我們依據接收者操作特性 (ROC) 曲線 (AUC) 下的區域來測量效能。請注意，就「流失」和「向上銷售」而言，複寫正範例以建立較平衡的集合將可微幅改善模型的效能。  

<table border="1">
<tr><td>Churn</td><td>AUC</td></tr>
<tr style="background-color: #fff"><td>no replication</td><td>0.711</td></tr>
<tr><td>複寫正集合</td><td>0.728</td></tr>
</table>


<table border="0">
<tr><td>向上銷售</td><td>AUC</td></tr>
<tr style="background-color: #fff"><td>no replication</td><td>0.853</td></tr>
<tr><td>複寫正集合</td><td>0.865</td></tr>
</table>


<table border="0">
<tr><td>慾望</td><td>AUC</td></tr>
<tr style="background-color: #fff"><td>no replication</td><td>0.805</td></tr>
<tr><td>複寫正集合</td><td>0.8</td></tr>
</table>

## API ##

我們並未實作模型，因為此模型具有匿名特性。



## 範例實驗

在 ML Studio 中可取得與此模型相關聯的下列範例實驗：[**範例**] 索引標籤之下的 [**實驗**] 區段。

> **範例實驗 - CRM - 開發**

## 範例資料集

在 ML Studio 中可取得此實驗所使用的下列範例資料集：**儲存的資料集**之下的模組調色板。

###共用 CRM 資料集
[AZURE.INCLUDE [machine-learning-sample-dataset-crm-dataset-shared](../includes/machine-learning-sample-dataset-crm-dataset-shared.md)]

###共用 CRM Appetency 標籤
[AZURE.INCLUDE [machine-learning-sample-dataset-crm-appetency-labels-shared](../includes/machine-learning-sample-dataset-crm-appetency-labels-shared.md)]

###共用 CRM 流失標籤
[AZURE.INCLUDE [machine-learning-sample-dataset-crm-churn-labels-shared](../includes/machine-learning-sample-dataset-crm-churn-labels-shared.md)]

###共用 CRM 向上銷售標籤
[AZURE.INCLUDE [machine-learning-sample-dataset-crm-upselling-labels-shared](../includes/machine-learning-sample-dataset-crm-upselling-labels-shared.md)]
