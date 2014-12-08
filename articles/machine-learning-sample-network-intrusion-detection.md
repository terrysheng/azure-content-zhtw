<properties title="Azure Machine Learning Sample: Network intrusion detection" pageTitle="Machine Learning 範例：網路入侵偵測 | Azure" description="A sample Azure Machine Learning experiment that uses a classification model to determine which network activities are a part of network intrusion." metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" manager="paulettm" editor="cgronlun"  videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/23/2014" ms.author="garye" />



# Azure Machine Learning 範例：網路入侵偵測

>[AZURE.NOTE]
>在 ML Studio 中可取得與此模型相關聯的[範例實驗]和[範例資料集]。如需詳細資訊，請參閱下列各項。
[範例實驗]: #sample-experiment
[範例資料集]: #sample-dataset


## 問題說明 ##

範例將使用不同的網路特性來偵測哪些網路活動屬於入侵/攻擊。這種二進位分類問題並不困難，因為這些特性的類別頗容易預測，且類別分佈相當平均。由於有數項特性屬於標籤的一部分，因此在開始訓練分類器之前，務必要先移除標籤的任何部分。 

## 資料 ##

1. 資料取自 KDD Cup 1999，其中包含訓練和測試資料集。就過去的記錄來看，訓練資料集的分類精確性會高於測試資料集，因為測試集會導入新的網路入侵。訓練資料集約有 126K 個資料列和 43 個資料行，內含標籤。有三個資料行屬於標籤資訊的一部分，因此有 40 個資料行可用來訓練模型。這 40 個資料行大部分是具有若干字串/類別特性的數值。測試資料約有 22.5K 個測試範例 (具有與訓練資料相同的 43 個資料行)。 
1. 資料已上傳至公用 Blob 以方便存取。AzureML 所提供的讀取器模組，讓您只需點按幾下即可從不同的儲存體 (例如 Blob) 讀取資料，而無須撰寫任何程式碼。 

## 模型 ##

1. 模型中的第一個步驟是調整資料以進行後續的分類。[類別] 資料行包含要預測的標籤。  這些標籤是「正常」(沒有入侵) 或攻擊的名稱。  有些攻擊在訓練資料中沒有許多範例，而測試資料集中有新的攻擊，所以我們會將這些多類別的標籤轉換成二進位類別標籤，讓問題變合理。Studio 提供了內建模組以簡化此前處理步驟。此外會使用指標值模組將類別標籤二進位化，然後使用專案資料行模組來選取這個二進位化的類別標籤資料行 (原始類別標籤資料行除外)。  
1. 在開發此實驗的過程中，我們透過特性的選取/關聯發現有許多特性都與標籤高度相關。這種情況通常出現在綜合資料集中。透過 Cloud ML，您只需點按幾下即可檢視此關聯情形。若要執行此動作，請在專案模組輸出上選取視覺化選項，然後按一下任何特性的標頭以啟動視覺化檢視，再從比較下拉式清單中選取類別標籤。
1. 由於有少量特性與標籤高度相關，因此我們將分別在選取和不選取特性的情況下測試線性與非線性分類器的組合，以判斷出最佳模型。這樣的比較可透過評估模型模組來完成。    

## 結果 ##

1. 非線性分類器 (促進式決策樹) 的執行結果略優於線性分類器 (羅吉斯迴歸)： 

![][1]


現在，我們要比較選取特性的促進式決策樹。分類效能非常類似，但促進式決策樹在所有特性上的效能都略勝一籌，而將會用於評分工作流程。請注意，這種高分類 AUC 通常會出現在此資料集。 


![][2]

<!-- Removed until this part is fixed
## Operationalization ##


We wanted to see our model in action, to do this we wanted a request-response service around the model we just learned. Doing this is straightforward and can be achieved in few simple clicks in studio: 

1. First step is to save the learned model (by right clicking on the classifier module output) 
1. Now create a new experiment and search for saved model and drop it in the panel for new experiment 
1. Then we need to replicate the steps we did for pre-process, again we can just select the modules from experiment we need and copy and paste to the new experiment 
1. The last step is to plug in the test data and run the experiment, once the run is finished we can define the input and output points for the service 
1. Again, using one click we can publish the service end points that is deployed right away and can be tested using a friendly user interface - all within studio   
-->


## 範例實驗

在 ML Studio 中可取得與此模型相關聯的下列範例實驗：[**範例**] 索引標籤之下的 [**實驗**] 區段。

> **範例實驗 - 網路入侵偵測 - 開發**


## 範例資料集

在 ML Studio 中可取得此實驗所使用的下列範例資料集：[**儲存的資料集**] 之下的模組調色板。

###network_intrusion_detection.csv
[AZURE.INCLUDE [machine-learning-sample-dataset-network-intrusion](../includes/machine-learning-sample-dataset-network-intrusion.md)]




[1]: ./media/machine-learning-sample-network-intrusion-detection/network-intrusion-detection-1.png
[2]: ./media/machine-learning-sample-network-intrusion-detection/network-intrusion-detection-2.png
