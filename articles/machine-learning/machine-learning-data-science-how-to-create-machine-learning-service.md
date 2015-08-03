<properties 
	pageTitle="在 Azure 中建置進階分析方案 | Microsoft Azure" 
	description="使用本指南來了解針對雲端中的資料科學和機器學習服務使用 Azure 的步驟。" 
	services="machine-learning,hdinsight" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" />

<tags 
	ms.service="machine-learning" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="required" 
	ms.date="06/03/2015" 
	ms.author="bradsev;sachouks" />

# 在 Azure 中建置進階分析方案

此進階分析程序和技術 (ADAPT) 指南概要說明使用 Azure Machine Learning 建置與部署預測性模型通常會涉及的資料科學工作。其中示範 Azure 平台如何啟動這些工作，例如擷取來自不同來源的資料、將資料準備用於 Azure Machine Learning，然後使用 Azure Machine Learning 實驗來建立使用者應用程式可以取用的操作化模型 (以程式設計方式或其他方式)。儘管此圖會示範一般端對端資料科學練習中所涉及的核心系列步驟，但並非所有步驟都是必要的，而它們的順序會根據資料的位置、大小及複雜度而有所不同。

[AZURE.INCLUDE [電腦-學習-免費-試用](../../includes/machine-learning-free-trial.md)]

標題為 **Azure 中的進階資料處理**的步驟會連結至第二個指南，該指南會逐步解說流程，以準備 Azure Machine Learning 實驗中要使用的資料。如果您從該指南返回，請從本指南的後續步驟繼續進行，以擷取在 Azure Machine Learning Studio 中處理過的資料，直到取用模型的最後一個步驟為止。

<object type="image/svg+xml" data="https://sidneyhcontent.blob.core.windows.net/documentation/machine-learning-how-to-create-ml-service.svg" width="100%" height="100%"> </object>
 

<!---HONumber=July15_HO4-->