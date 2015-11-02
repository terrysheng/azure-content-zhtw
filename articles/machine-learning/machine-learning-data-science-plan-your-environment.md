<properties 
	pageTitle="規劃機器學習服務進階分析環境 | Microsoft Azure" 
	description="考慮重要問題來規劃進階分析環境。" 
	services="machine-learning" 
	documentationCenter="" 
	authors="bradsev"
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/18/2015" 
	ms.author="bradsev" />


# 針對 Cortana 分析程序規劃您的環境

當您準備要設定環境進行進階分析處理時，什麼案例會符合您的分析問題？ 您針對所需的資源而進行決策的依據是資料的類型、大小和來源位置，以及此資料的目標目的地。本文將討論這些問題，協助您了解您的案例。

一旦您了解相關案例，[學習途徑：在 Azure 中建立進階分析解決方案](machine-learning-data-science-how-to-create-machine-learning-service.md)所呈現的 Cortana 分析程序工作流程將引導您逐步完成一系列的工作，例如透過以可供應用程式取用的 Azure Web 服務的形式來建立和發行一個模型，藉此取得資料集。

本主題也會列舉此進階分析程序所使用的一些資源和工具。

[AZURE.INCLUDE [電腦-學習-免費-試用](../../includes/machine-learning-free-trial.md)]

## 回答這些問題
在建立進階的分析環境之前，請先回答下列問題，以判斷您正在處理何種案例。

1. **您的資料位於何處？** (這個位置稱為***資料來源***。) 例如：
	- 資料可在 HTTP 位址上公開使用。
	- 資料位於本機或網路檔案位置中。
	- 資料是在 SQL Server 資料庫中。
	- 資料儲存於 Azure 儲存體容器中
2. **如何將資料格式化？** 例如：
    - 以逗號分隔或定位鍵分隔且未壓縮的檔案。
    - 以逗號分隔或定位鍵分隔且已壓縮的檔案。
	- 已壓縮或未壓縮的 Azure Blob。
	- SQL Server 資料表。
3. **您的資料大小為何？**
    - 小型：小於 2 GB
    - 中型：大於 2 GB 且小於 10 GB
	- 大型：大於 10 GB
	- 非常大型：上百 GB
4. **您對資料的熟悉程度為何？**
    - 您需要瀏覽資料來探索它的結構描述、變數分佈、遺漏值等項目嗎？ 
	- 資料是否需要預先處理，或在轉換至表格表示法之前加以清除？ 
5. **您是否打算 (或者您能夠) 將所有資料移至 Azure 儲存體？**
    - 是，正在規劃將整個資料集複製到雲端以進行處理。
	- 否，只會將資料子集複製到 Azure。
6. **您慣用的 Azure 雲端目的地為何？** 例如：
	- 將資料移至 Azure 儲存體 Blob。
	- 將資料儲存在可掛接的 Azure 虛擬硬碟中。
	- 將資料載入 Azure 虛擬機器上的 SQL Server 資料庫。
	- 將資料對應至 Azure HDInsight Hive 資料表。

## 您的案例是什麼？
一旦回答上一區段中的問題之後，您便已準備好決定哪個案例最符合您的情況。案例範例已於 [Azure 機器學習中的進階分析案例](../machine-learning-data-science-plan-sample-scenarios.md)中概述。

## Azure 中的進階分析資源
視您的情況而定，您可能需要部分下列工具和資源。

1.  Azure 工具： 
	* 	[Azure PowerShell SDK](../install-configure-powershell.md) 
	* 	[Azure 儲存體總管](http://azurestorageexplorer.codeplex.com/)
	* 	[AzCopy](../storage-use-azcopy.md)
2.  執行 SQL Server 的 Azure 虛擬機器
3.  Azure HDInsight (Hadoop)
4.  用以內部部署至 Azure 檔案共用的 Azure 虛擬網路
5.  適用於已排定之資料移動的 Azure Data Factory






 

<!---HONumber=Oct15_HO4-->