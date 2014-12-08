<properties title="Azure Machine Learning Sample: Sentiment analysis" pageTitle="Machine Learning 範例：情感分析 | Azure" description="A sample Azure Machine Learning experiment that uses sentiment classification to predict product reviews." metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" manager="paulettm" editor="cgronlun"  videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/23/2014" ms.author="garye" />
 

# Azure Machine Learning 範例：情感分析

>[AZURE.NOTE]
>在 ML Studio 中可取得與此模型相關聯的[範例實驗]和[範例資料集]。如需詳細資訊，請參閱下列各項。
[範例實驗]: #sample-experiment
[範例資料集]: #sample-dataset


##問題說明
預測產品評價的評等。評等分為 1,2,3,4,5。這是序數迴歸問題，也可視為迴歸問題和多類別分類問題來求解。
 
##資料
Amazon 中的書籍評論，由 UPenn 研究人員摘錄自 Amazon 網站 ([http://www.cs.jhu.edu/~mdredze/datasets/sentiment/](http://www.cs.jhu.edu/~mdredze/datasets/sentiment/))。原始資料集有 975K 排名1,2,3,4,5 的評論。為了加速進行實驗，我們將資料集的取樣縮小為 10K 評論。所有評論皆採用英文格式。這些評論撰寫於 1997-2007 年之間。 
 
##模型
我們使用特性雜湊模組，將英文文字轉換為整數值特性。我們比較了三個模型：  
 
1. 線性迴歸   
2. 以雙類別羅吉斯迴歸作為基本分類器的序數迴歸
3. 使用多類別羅吉斯迴歸分類器的多類別分類
 
##結果

演算法                 | 平均絕對誤差 | 均方根誤差
:---------                | :-----------------: | :--------------------:
序數迴歸        | 0.82                | 1.41
線性迴歸         | 1.04                | 1.36
多類別分類 | 0.85                | 1.57
 
根據這些結果，我們選擇了序數迴歸模型，並據以建置 Web 服務。
 
<!-- Removed until this part is fixed
##API
We have built a web service that takes a plain text review and predicts its rating.
-->


## 範例實驗

在 ML Studio 中可取得與此模型相關聯的下列範例實驗：[**範例**] 索引標籤之下的 [**實驗**] 區段。

> **範例實驗 - 情感分類 - 開發**


## 範例資料集

在 ML Studio 中可取得此實驗所使用的下列範例資料集：[**儲存的資料集**] 之下的模組調色板。

###來自 Amazon 的書籍評論
[AZURE.INCLUDE [machine-learning-sample-dataset-book-reviews-from-amazon](../includes/machine-learning-sample-dataset-book-reviews-from-amazon.md)]
