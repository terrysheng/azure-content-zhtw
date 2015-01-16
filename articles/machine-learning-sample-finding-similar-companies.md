<properties title="Azure Machine Learning Sample: Finding similar companies" pageTitle="Machine Learning 範例：尋找類似的公司 | Azure" description="使用叢集模型找出類似公司的範例 Azure Machine Learning 實驗。" metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" manager="paulettm" editor="cgronlun"  videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/23/2014" ms.author="garye" />


# Azure Machine Learning 範例：尋找類似的公司

>[AZURE.NOTE]
>在 ML Studio 中可取得與此模型相關聯的[範例實驗]和[範例資料集]。如需詳細資訊，請參閱下列各項。
[範例實驗]: #sample-experiment
[範例資料集]: #sample-dataset


## 問題說明 ##
根據每家公司的 Wikipedia 文章，將 S&P 500 指數中的公司叢集化。舉例來說，此模型可用來根據公司與現有 S&P 500 公司的相似度，將具有潛力的公司標示出來。

## 資料 ##
資料衍生自 Wikipedia XML 傾印資料會在 ML Studio 外進行前處理，以擷取每家公司的文字內容、移除 wiki 格式、移除非英數字元，並將所有文字轉換為小寫。此外還新增了知名公司類別。請注意，有些公司可能找不到相關文章。因此記錄數會小於 500。

## 模型 ##
首先，文章內容會傳至「特性雜湊」模組，以根據每個權杖的雜湊值將文字資料轉換為數值格式。這項資料的維度太高，且過於稀疏，因此無法直接用在 K-means 叢集中。因此，我們在「執行 R 指令碼」模組使用 PCA，將維度降為 10 個變數。請注意，只要開啟 R 模組的右側輸出，即可檢視 PCA 結果。 

透過反覆嘗試，我們發現 PCA 轉換資料的第一個變數 (變異度最高的變數) 可能對叢集有不利的影響。因此，我們使用「專案資料行」模組將此變數叢特性集中移除。

接著，這項資料透過 K-Means 演算法叢集化為 3 個叢集。透過反覆嘗試，此數值最終可產生合理的結果；後續也嘗試使用 4 和 5 個叢集。

最後，我們使用中繼資料編輯器將叢集標籤變數轉換為類別變數。此外，結果也轉換為 CSV 檔案以供下載。

## 結果 ##
藉由呈現中繼資料編輯器的輸出，並根據 [指派] 資料行繪製 [類別] 資料行 (從 Wikipedia 資料得知)，即可檢視結果。3 個叢集大致上會對應至下列已知類別。

叢集 0：可選擇消費品、必需消費品、金融、醫療保健

叢集 1：資訊技術

叢集 2：能源、公共事業、電信服務

**注意**：叢集分類並不明確。叢集 0 含有數家資訊技術和能源公司，而工業和原料業則歸類在叢集 0 與叢集 1 之間

**注意**：每次執行叢集化的結果可能並不相同。 



## 範例實驗

在 ML Studio 中可取得與此模型相關聯的下列範例實驗：[**範例**] 索引標籤之下的 [**實驗**] 區段。

> **範例實驗 - S & P 500 公司叢集 - 開發**


## 範例資料集

在 ML Studio 中可取得此實驗所使用的下列範例資料集：[**儲存的資料集**] 之下的模組調色板。

###Wikipedia SP 500 資料集
[AZURE.INCLUDE [machine-learning-sample-dataset-wikipedia-sp-500](../includes/machine-learning-sample-dataset-wikipedia-sp-500.md)]
