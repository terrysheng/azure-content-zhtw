<properties
	pageTitle="機器學習服務模型如何從實驗進展為實際運作的 Web 服務 | Microsoft Azure"
	description="機制的概觀，說明 Azure Machine Learning 模型如何從開發實驗進展為實際運作的 Web 服務。"
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
	ms.date="11/10/2015"
	ms.author="garye"/>


# 機器學習服務模型如何從實驗進展為實際運作的 Web 服務

***實驗***是 Azure Machine Learning Studio 中的畫布，可讓您在建立預測性分析模型時，以互動方式開發、執行、測試及逐一查看。有各種不同的模組可供您用來將資料帶入實驗、操作資料、使用機器學習演算法定型模型、對模型計分、評估結果和輸出最終值。

一旦滿意實驗，您可以將其部署為 ***Azure Web 服務***，讓使用者可以將新的資料傳送給它並接收的結果。

在本文中我們將提供機制的概觀，說明 Machine Learning 模型如何從開發實驗進展為實際運作的 Web 服務。

>[AZURE.NOTE]有其他方式可開發和部署機器學習模型，但本文著重在如何使用 Machine Learning Studio。如需如何使用 R 建立預測性 Web 服務的討論，請參閱部落格文章[使用 RStudio 和 Azure ML 建置和部署預測性 Web Apps](http://blogs.technet.com/b/machinelearning/archive/2015/09/25/build-and-deploy-a-predictive-web-app-using-rstudio-and-azure-ml.aspx)。

雖然 Azure Machine Learning Studio 的設計主要是為了協助您開發及部署*預測性分析模型*，但可以使用 Studio 來開發未包含預測性分析模型的實驗。比方說，實驗可能只是輸入資料、操作資料，然後輸出結果。如同預測性分析實驗，您可以將這個非預測性實驗部署為 Web 服務，但此程序更簡單，因為實驗不會對機器學習模型進行定型或計分。雖然這不是 Studio 的一般使用方式，我們將會在下面的討論包含它，如此我們可以提供 Studio 運作方式的完整說明。

## 開發及部署預測性 Web 服務

以下是使用 Machine Learning Studio 進行開發及部署時一般解決方案所遵循的階段：

![](media\machine-learning-model-progression-experiment-to-web-service\model-stages-from-experiment-to-web-service.png)

*圖 1 - 一般預測性分析模型的階段*

### 訓練實驗

***訓練實驗***是 Machine Learning Studio 中的初始實驗畫布。訓練實驗的目的是要提供您開發、測試、逐一查看和最終定型機器學習模型的位置。尋求最佳的解決方案時，您甚至可以同時定型多個模型，但是一旦完成實驗，您將選取單一定型的模型，並從實驗消除其餘部分。如需開發預測性分析實驗的範例，請參閱[在 Azure Machine Learning 中為信用風險評估開發預測性分析解決方案](machine-learning-walkthrough-develop-predictive-solution.md)。

### 預測性實驗

一旦在訓練實驗中有定型的模型，在 Machine Learning Studio 和 Studio 中按一下 [設定 Web 服務]，會經歷將您的訓練實驗轉換為***預測性實驗***的程序。預測性實驗的目的是要使用您的定型模型對新資料進行計分，以最終目標成為實際運作的 Azure Web 服務。

會透過下列步驟為您完成這項轉換：

-   轉換用於定型成單一模組的模組集，並將它儲存為定型的模型

-   排除與計分無關的任何多餘模組

-   加入最終的 Web 服務將使用的輸入和輸出連接埠

可能有您想要進行的變更，以讓預測性實驗備妥供部署為 Web 服務。例如，如果想要 Web 服務輸出結果的子集，您可以在輸出連接埠之前加入篩選模組。

在此轉換程序中不會捨棄訓練實驗。程序完成時您在 Studio 中將有兩個索引標籤：一個用於訓練實驗，而一個用於預測性實驗。以此方式，在部署您的 Web 服務之前，您可以對訓練實驗進行變更，並重建預測性實驗。或者您可以儲存一份訓練實驗，以開始另一行的實驗。

>[AZURE.NOTE]按一下 [設定 Web 服務] 時，會開始自動化的程序，將訓練實驗轉換為預測性實驗，而且這在大部分的情況下可正常運作。但如果訓練實驗很複雜 (例如，聯結在一起的定型有多個路徑)，您可能會想要手動進行這項轉換。如需如何進行此轉換程序工作的詳細資料，請參閱[將機器學習服務訓練實驗轉換成預測性實驗](machine-learning-convert-training-experiment-to-scoring-experiment.md)。

### Web 服務

一旦認為您的預測性實驗就緒，您會按一下 [部署 Web 服務]，藉由將它部署為 ***Azure Web 服務***讓模型實際運作。使用者現在可以使用 Web 服務 REST API 將資料傳送至您的模型中並收回結果。如需如何進行此動作的詳細資料，請參閱[如何使用已從機器學習實驗部署的 Azure Machine Learning Web 服務](machine-learning-consume-web-services.md)。

一旦您部署 Web 服務，預測性實驗和 Web 服務會保持連線，而在可以在兩者間往返：

|***從這個頁面...***|***按一下此...***|***以開啟此頁面...***|
| ------------------- | --------------- | ---------------------- |
|Studio 中的實驗畫布|**前往 Web 服務**|Studio 中的 Web 服務組態|
|Studio 中的 Web 服務組態|**檢視最新版本**|Studio 中的實驗畫布|
|Studio 中的 Web 服務組態|**管理端點...**|Azure 傳統入口網站中的端點管理|
|Azure 傳統入口網站中的端點管理|**在 Studio 中編輯**|Studio 中的實驗畫布|

![](media\machine-learning-model-progression-experiment-to-web-service\connections-between-experiment-and-web-service.png)

*圖 2 - 實驗和 Web 服務之間的連接*

## 非一般的情況：建立非預測性 Web 服務

如果實驗不會定型預測性分析模型，則您不需要同時建立訓練實驗和評分實驗 - 只有一個實驗，而您可以將它部署為 Web 服務 (Machine Learning Studio 可藉由分析您所使用的模組來偵測您的實驗是否包含預測性模型)。

逐一查看實驗並滿足於該實驗之後：

1.  按一下 [設定 Web 服務] - 會自動加入輸入和輸出節點

2.  按一下 [執行]

3.  按一下 [部署 Web 服務]

現在已部署您的 Web 服務，而且您可以如同預測性 Web 服務一般存取及管理它。

## Web 服務按鈕

在 Machine Learning Studio 中，[Web 服務] 按鈕 - [設定 Web 服務] 和 [部署 Web 服務] - 根據您在開發程序中的位置變更名稱和功能。

**實驗包含預測性模型**

如果實驗會對預測性模型進行定型及計分，則 Web 服務按鈕會執行這些功能：

|**實驗的類型**|**按鈕**|**作用**|
| -------------------- | -------- | -------------- |
|實驗開發中|**設定 Web 服務**|提供兩個選項|
|&nbsp;|- **預測性 Web 服務**|將實驗轉換成訓練實驗和評分實驗。|
|&nbsp;|- **重新定型 Web 服務**|將實驗轉換成重新訓練實驗 (請參閱以下的「更新」一節)|
|訓練實驗|**設定 Web 服務**|提供兩個選項|
|&nbsp;|- **更新預測性實驗**|以您對訓練實驗所做的變更更新相關聯的預測性實驗|
|&nbsp;|- **重新定型 Web 服務**|將訓練實驗轉換成重新訓練實驗 (請參閱以下的「更新」一節)|
|&nbsp;|-*或*- **部署 Web 服務**|如果您已為部署設定訓練實驗，則會將其部署為 Web 服務|
|預測性實驗|**部署 Web 服務**|將預測實驗部署為 Web 服務|

**實驗並*不*包含預測性模型**

如果實驗不會對預測性模型進行定型及計分，則 Web 服務按鈕會容易的多：

|**實驗的類型**|**按鈕**|**作用**|
| -------------------- | -------- | -------------- |
|實驗開發中|**設定 Web 服務**|準備實驗供部署為 Web 服務|
|實驗已備妥供部署|**部署 Web 服務**|將實驗部署為 Web 服務，開啟 Web 服務組態頁面|

## 使用您的 Web 服務

現在您已將實驗部署為 Web 服務，如果需要更新它，該怎麼做？

視您需要更新的項目而定：

**您想要變更輸入或輸出，或您想要修改 Web 服務操縱資料的方式**

如果您不變更模型，但只是要變更 Web 服務處理資料的方式，您可以編輯預測性實驗，然後再次按一下 [部署 Web 服務]。Web 服務將會停止、將部署更新的預測性實驗，以及 Web 服務會再次啟動。

以下是範例：假設預測性實驗會傳回輸入資料的整個資料列與預測結果。您可以決定您想要 Web 服務只傳回結果。因此，您可以在預測性實驗中加入**專案資料行**模組，緊接在輸出連接埠之前，以便排除資料行而非結果。再次按一下 [部署 Web 服務] 時，即會更新 Web 服務。

**您想要使用新資料重新定型模型**

如果您想要保留您的機器學習模型，但您想要以新的資料重新定型，您有兩個選擇：

1.  **Web 服務執行時重新定型模型** - 如果您想要在預測性 Web 服務執行時重新定型模型，您可以藉由對訓練實驗進行幾個修改，使它成為***重新訓練實驗***，然後可以將它部署為***重新定型 Web* 服務**。如需如何執行這項操作的指示，請參閱[以程式設計方式重新定型機器學習服務模型](machine-learning-retrain-models-programmatically.md)。

2.  **返回原始訓練實驗並使用不同的定型資料來開發您的模型** - 您的預測性實驗連結至 Web 服務，但訓練實驗未以這種方式直接連結。如果您修改原始的訓練實驗，並按一下 [設定 Web 服務]，它會建立*新*預測性實驗，部署時將會建立*新* Web 服務。它不只是更新原始的 Web 服務。

    所以，如果您需要修改訓練實驗，請開啟它並按一下 [另存新檔] 以製作複本。這將會原始的訓練實驗、預測性實驗和 Web 服務維持不變。您現在可以利用您的變更建立新的 Web 服務。部署新的 Web 服務之後，可以再決定是否要停止先前的 Web 服務，或讓它隨著新的服務一起執行。

**您想要定型不同的模型**

如果您想要對原始預測性實驗進行變更，例如選取不同的機器學習演算法、嘗試不同的定型方法等，則您必須遵循上述用於重新定型模型的第二個程序：開啟訓練實驗、按一下 [另存新檔] 來製作副本，然後開始開發模型的新路徑、建立預測性實驗和部署 Web 服務。這會建立與原始無關的新 Web 服務 - 您可以決定要繼續執行哪一個或兩者。

## 進階閱讀

如需這個程序的詳細資訊，請參閱下列文章：

-   轉換實驗 - [將機器學習服務訓練實驗轉換成預測性實驗](machine-learning-convert-training-experiment-to-scoring-experiment.md)

-   部署 Web 服務 - [部署 Azure Machine Learning Web 服務](machine-learning-publish-a-machine-learning-web-service.md)

-   重新定型模型 - [以程式設計方式重新定型機器學習服務模型](machine-learning-retrain-models-programmatically.md)

如需整個程序的範例，請參閱：

-   [機器學習教學課程：在 Azure Machine Learning Studio 中建立您的第一個實驗](machine-learning-create-experiment.md)

-   [逐步解說：在 Azure Machine Learning 中為信用風險評估開發預測分析解決方案](machine-learning-walkthrough-develop-predictive-solution.md)

<!---HONumber=AcomDC_1203_2015-->