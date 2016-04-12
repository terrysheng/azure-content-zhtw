<properties
	pageTitle="使用 Machine Learning 建立的信用風險預測解決方案 | Microsoft Azure"
	description="詳細的逐步解說說明如何在 Azure Machine Learning 中為信用風險評估建立預測分析解決方案。"
	keywords="信用風險, 預測性分析解決方案, 風險評估"
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
	ms.topic="get-started-article"
	ms.date="03/09/2016"
	ms.author="garye"/>


# 逐步解說：在 Azure Machine Learning 中為信用風險評估開發預測分析解決方案

假設您必須根據某人在信用申請書上提供的資訊預測其信用風險。

當然，信用風險評估是一個複雜的問題，但是我們可以稍微簡化問題的參數。然後，我們可以將其做為範例，讓您據以搭配 Machine Learning Studio 與 Machine Learning Web 服務使用 Microsoft Azure Machine Learning，以建立此類的預測分析解決方案。

在此詳細的逐步解說中，我們將遵循在 Machine Learning Studio 中開發預測性分析模型的程序，然後將其部署為 Azure 機器學習 Web 服務。我們將從公開的信用風險資料開始，根據這項資料開發並訓練預測性模型，然後將模型部署為可供其他人用於信用風險評估的 Web 服務。

[AZURE.INCLUDE [電腦-學習-免費-試用](../../includes/machine-learning-free-trial.md)]

>[AZURE.TIP] 若要下載並列印提供 Machine Learning Studio 功能概觀的圖表，請參閱 [Azure Machine Learning Studio 功能的概觀圖](machine-learning-studio-overview-diagram.md)。

我們將依照以下步驟建立信用風險評估解決方案：

1.	[建立機器學習服務工作區](machine-learning-walkthrough-1-create-ml-workspace.md)
2.	[上傳現有資料](machine-learning-walkthrough-2-upload-data.md)
3.	[建立新實驗](machine-learning-walkthrough-3-create-new-experiment.md)
4.	[訓練及評估模型](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5.	[部署 Web 服務](machine-learning-walkthrough-5-publish-web-service.md)
6.	[存取 Web 服務](machine-learning-walkthrough-6-access-web-service.md)

此逐步解說以 [Cortana Intelligence Gallery](http://gallery.cortanaintelligence.com/) 中的簡化版[二進位分類：信用風險預測](http://go.microsoft.com/fwlink/?LinkID=525270)範例實驗為基礎。

<!---HONumber=AcomDC_0406_2016-->