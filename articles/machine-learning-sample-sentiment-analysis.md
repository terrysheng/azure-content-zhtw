<properties title="Azure Machine Learning Sample: Sentiment analysis" pageTitle="Machine Learning Sample: Sentiment analysis | Azure" description="A sample Azure Machine Learning experiment that uses sentiment classification to predict product reviews." metaKeywords="" services="" solutions="" documentationCenter="" authors="garye" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="garye"></tags>

# Azure Machine Learning 範例：情感分析

*您可以在 <b>[範例]</b> 索引標籤下的 <b>[實驗]</b> 區段中，找到與 ML Studio 中的這個模型相關聯的範例實驗。實驗名稱為：*

    Sample Experiment - Sentiment Classification - Development

## 問題說明

預測產品評價的評等。評等分為 1,2,3,4,5。這是序數迴歸問題，也可視為迴歸問題和多類別分類問題來求解。

## 資料

Amazon 中的書籍評論，由 UPenn 研究人員摘錄自 Amazon 網站 ([][]<http://www.cs.jhu.edu/~mdredze/datasets/sentiment/></a>)。原始資料集含有給予評等 1,2,3,4,5 的 975K 個評論。為了加快實驗速度，我們將資料集的樣本數降為一萬個評論。所有評論皆採用英文格式。這些評論撰寫於 1997-2007 年之間。

## 模型

我們使用特性雜湊模組，將英文文字轉換為整數值特性。我們比較了三個模型：

1.  線性迴歸
2.  以雙類別羅吉斯迴歸作為基本分類器的序數迴歸
3.  使用多類別羅吉斯迴歸分類器的多類別分類

## 結果

演算法| 平均絕對誤差| 均方根誤差
---------|  
序數迴歸 | 0.82| 1.41|
線性迴歸 | 1.04| 1.36|
多類別迴歸 | 0.85 | 1.57

根據這些結果，我們選擇了序數迴歸模型，並據以建置 Web 服務。

<!-- Removed until this part is fixed ##API We have built a web service that takes a plain text review and predicts its rating. -->

  []: http://www.cs.jhu.edu/~mdredze/datasets/sentiment/
