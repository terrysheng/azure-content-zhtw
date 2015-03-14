<properties 
	pageTitle="使用 Machine Learning 開發預測方案 | Azure" 
	description="逐步解說如何在 Azure Machine Learning Studio 中建立預測分析實驗" 
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
	ms.date="08/06/2014" 
	ms.author="garye"/>

# 使用 Azure Machine Learning 開發預測方案

假設您必須根據某人在信用申請書上提供的資訊預測其信用風險。

這當然是個複雜的問題，但我們可以略為簡化此問題的參數，並將其做為範例，讓您據以搭配使用 Microsoft Azure Machine Learning 和 ML Studio 與 ML API 服務，以建立此類的預測分析方案。

在此逐步解說中，我們將遵循在 ML Studio 中開發預測分析模型的程序，然後將其發佈至 ML API 服務。我們將從公開的信用風險資料開始，根據這項資料開發並訓練預測模型，然後將模型發佈為可供其他人使用的 Web 服務。

我們將遵循下列步驟：

1.  [建立 ML 工作區][建立 ML 工作區]
2.  [上傳現有資料][上傳現有資料]
3.  [建立新實驗][建立新實驗]
4.  [訓練及評估模型][訓練及評估模型]
5.  [發佈 Web 服務][發佈 Web 服務]
6.  [存取 Web 服務][存取 Web 服務]

此逐步解說以隨附於 ML Studio 的簡化版
[信用風險預測範例實驗][信用風險預測範例實驗]為基礎。

  [建立 ML 工作區]: ../machine-learning-walkthrough-1-create-ml-workspace/
  [上傳現有資料]: ../machine-learning-walkthrough-2-upload-data/
  [建立新實驗]: ../machine-learning-walkthrough-3-create-new-experiment/
  [訓練及評估模型]: ../machine-learning-walkthrough-4-train-and-evaluate-models/
  [發佈 Web 服務]: ../machine-learning-walkthrough-5-publish-web-service/
  [存取 Web 服務]: ../machine-learning-walkthrough-6-access-web-service/
  [信用風險預測範例實驗]: ../machine-learning-sample-credit-risk-prediction/

<!--HONumber=46--> 
