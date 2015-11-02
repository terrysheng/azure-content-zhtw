<properties 
	pageTitle="在 Cortana 分析程序中進行資料取樣" 
	description="如何在不同的儲存體環境中瀏覽資料。" 
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
	ms.date="10/20/2015" 
	ms.author="fashah;garye;bradsev" />

#<a name="heading"></a>在 Cortana 分析程序中進行資料取樣

此**功能表**連結說明如何從各種不同儲存體環境進行資料取樣的連結。此工作是 Cortana 分析程序 (CAP) 中的一個步驟。

[AZURE.INCLUDE [cap-sample-data-selector](../../includes/cap-explore-data-selector.md)]

## 簡介

本文件說明為儲存於 Azure blob 儲存體中的資料進行取樣，您可以利用程式設計方式下載它，然後使用 Python 程式碼範例來對它進行取樣。執行此動作的步驟如下：

本文件涵蓋如何對在 Cortana 分析程序中通常使用三個位置所儲存的資料進行取樣：

- 透過程式設計方式下載 **Azure blob 儲存體資料**，然後使用 Python 程式碼範例來進行取樣，這是對 Azure blob 儲存體資料進行取樣的方式。
- 使用 SQL 和 Python 程式設計語言來對 **SQL Server 資料** 進行取樣。 
- 使用 Hive 查詢來對 **Hive 資料表**進行取樣。

<!---HONumber=Oct15_HO4-->