<properties 
	pageTitle="什麼是 Azure Machine Learning Studio？| Azure" 
	description="Azure Machine Learning Studio 概觀和基本文件" 
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
	ms.date="09/11/2014" 
	ms.author="garye"/>

# 什麼是 Azure Machine Learning Studio？

Microsoft Azure Machine Learning Studio 是共同作業的視覺化開發環境，可讓您建置、測試和部署以您的資料為對象的預測分析解決方案。機器學習服務和開發環境以雲端為架構，提供運算資源和記憶體彈性，因為是透過網頁瀏覽器來運作，不必擔心設定和安裝問題。 

ML Studio 讓資料科學、預測分析、雲端資源和您的資料齊聚一堂！

## ML Studio 互動式工作區

若要開發預測分析模型，您通常會使用一或多個來源的資料，透過各種資料操作和統計函數來轉換和分析該資料，然後產生一組結果。開發此類模型是一種反覆式過程 - 您修改各種函數及其參數，結果會不斷收斂，直到您對已訓練的有效模型感到滿意為止。

**ML Studio** 提供互動式的視覺化工作區，讓您輕鬆建置、測試和反覆運算預測分析模型。您可以將***資料集***和分析***模組***拖放到互動式***畫布***，將它們連接在一起以構成***實驗***，然後在 ML Studio 中***執行***。若要反覆調整模型設計，請***編輯***實驗，***儲存***複本 (若需要)，然後重新提交。當您準備好時，您將實驗***發行***為 ***Web 服務***，讓其他人可以存取您的模型。 

不需要設計程式，只要在視覺上連接資料集和模組，即可建構預測分析模型。

![ML Studio Overview][ml-studio-overview]

## 開始使用 ML Studio

第一次進入 ML Studio 時，您在左邊會看到下列索引標籤：

- **Studio 首頁** - 文件和其他資源的一組連結
- **實驗** - 已建立、執行和儲存為草稿的實驗。 
- **WEB 服務** - 您已發行的實驗清單。 
- **設定** - 可用來設定帳戶和資源的一組設定。 

>[WACOM.NOTE] 當您建構實驗時，可用的資料集和模組的工作清單	會顯示在畫布左邊。這是您用來建置模型的元件清單。

## 實驗的元件

實驗由資料集組成，資料集提供資料給分析模組，將模組連接起就能建構預測分析模型。明確地說，有效的實驗有三個特性：

- 至少有一個資料集和一個模組。 
- 資料集只能連接到模組。 
- 模組可以連接到資料集或其他模組。 
- 模組的所有輸入埠必須有資料流程的某些連線。 
- 模組的所有必要參數都必須設定。 

如需建立簡易實驗的範例，請參閱[在 Azure Machine Learning Studio 中建立簡易實驗](http://azure.microsoft.com/documentation/articles/machine-learning-create-experiment/)。 
如需建立預測性分析方案的更完整逐步解說，請參閱[使用 Azure Machine Learning 開發預測方案](http://azure.microsoft.com/documentation/articles/machine-learning-walkthrough-develop-predictive-solution/)。

### 資料集

資料集是指已上傳至 ML Studio 而可供模型化程序中使用的資料。ML Studio 隨附許多範例資料集供您實驗，您可以在需要時上傳更多資料集。以下是隨附資料集的一些例子：

- **各種汽車的 MPG 資料** - 汽車的 MPG 值，以汽缺數、馬力等來識別。 
- **乳癌資料** - 乳癌診斷資料 
- **森林火災資料** - 葡萄牙西北部的森林火災範圍 

當您建置實驗時，畫布左邊會顯示可用的資料集清單。 

### 模組

模組是指您在資料上可執行的演算法。ML Studio 有許多模組，從資料輸入函數到訓練、評分和驗證程序都有。以下是隨附模組的一些例子：

- **轉換為 ARFF** - 將 .NET 序列化資料集轉換為 ARFF 格式 
- **基本統計資料** - 計算基本統計資料，例如平均值、標準差等。 
- **線性迴歸** - 建立線上梯度下降線性迴歸模型 
- **評分模型** - 給訓練的分類或迴歸模型評分 

當您建置實驗時，畫布左邊會顯示可用的模組清單。 

模組可能有一組參數可用來設定模組的內部演算法。當您在畫布上選取模組時，模組的參數會顯示在畫布右邊的窗格中。  您可以在此窗格中修改參數來調整模型。


[ml-studio-overview]:./media/machine-learning-what-is-ml-studio/context.jpg

<!--HONumber=46--> 
