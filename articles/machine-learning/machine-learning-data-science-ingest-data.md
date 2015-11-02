<properties 
	pageTitle="將資料載入至儲存體環境以便進行分析 | Microsoft Azure" 
	description="從 Azure Blob 儲存體來回移動資料" 
	services="machine-learning,storage" 
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
	ms.date="10/19/2015" 
	ms.author="bradsev" />

# 將資料載入至儲存體環境以便進行分析

## 簡介

Cortana 分析程序 (CAP) 要求將資料擷取或載入至各種不同的儲存體環境，以便在程序的每個階段中以最適當的方式處理或分析。CAP 通常用於處理的資料目的地包括 Azure Blob 儲存體、SQL Azure 資料庫、Azure VM 上的 SQL Server、HDInsight (Hadoop) 和 Azure 機器學習。

此**功能表**所連結的主題說明如何將資料擷取至可儲存和處理資料的目標環境。

[AZURE.INCLUDE [cap-ingest-data-selector](../../includes/cap-ingest-data-selector.md)]

技術和商務需求以及資料的初始位置、格式和大小，會決定需要擷取資料到其中的目標環境以達成您的分析目標。以下案例並不常見：要求在數個環境之間移動資料來達成建構預測模型所需的各種工作。比方說，這一系列工作可以包含資料瀏覽、前置處理、清除、向下取樣和模型定型。

<!---HONumber=Oct15_HO4-->