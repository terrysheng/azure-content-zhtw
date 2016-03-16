<properties 
	pageTitle="在 Azure Blob 容器、SQL Server 和 Hive 資料表中進行資料取樣 | Microsoft Azure" 
	description="如何探索儲存在各種 Azure 環境中的資料。" 
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
	ms.date="02/07/2016" 
	ms.author="fashah;garye;bradsev" />

#<a name="heading"></a>在 Azure Blob 容器、SQL Server 和 Hive 資料表中進行資料取樣

## 簡介

本文件涵蓋如何在 Cortana Analytics 程序中分析和模型化資料時，對儲存在常使用的三個 Azure 位置的資料進行取樣：

- 透過程式設計方式下載 **Azure blob 儲存體資料**，然後使用 Python 程式碼範例來進行取樣，這是對 Azure blob 儲存體資料進行取樣的方式。
- 使用 SQL 和 Python 程式設計語言來對 **SQL Server 資料** 進行取樣。 
- 使用 Hive 查詢來對 **Hive 資料表**進行取樣。

以下**功能表**會連結至說明如何從這些 Azure 儲存體環境進行資料取樣的主題。

[AZURE.INCLUDE [cap-sample-data-selector](../../includes/cap-sample-data-selector.md)]

此取樣工作是 [Cortana Analytics Process (CAP) (Cortana Analytics 程序 (CAP))](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/) 中的一個步驟。

## 為何要對資料進行取樣？

如果您規劃分析的資料集很龐大，通常最好是對資料進行向下取樣，將資料縮減為更小但具代表性且更容易管理的大小。這有助於資料了解、探索和功能工程。它在 Cortana 分析程序中扮演的角色是能夠快速建立資料處理函式與機器學習服務模型的原型。

<!---HONumber=AcomDC_0211_2016-->