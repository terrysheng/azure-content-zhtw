<properties
   pageTitle="安裝適用於 SQL 資料倉儲的 Visual Studio 及/或 SSDT | Microsoft Azure"
   description="安裝適用於 Azure SQL 資料倉儲的 Visual Studio 及/或 SSDT 開發工具"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="twounder"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="10/21/2015"
   ms.author="twounder;barbkess"/>

# 安裝適用於 SQL 資料倉儲的 Visual Studio 2015 及/或 SSDT

若要開發 SQL 資料倉儲的應用程式，建議使用 Visual Studio 2013 或更新版本，並搭配最新版本的 SQL Server Data Tools (SSDT)。

若要從 Visual Studio 整合式開發環境 (IDE) 執行查詢，您只需要安裝 SSDT。這將安裝 Visual Studio IDE 以及 SSDT，讓您可以使用 [SQL Server 物件總管] 連接到 Azure SQL Server。然後，您將能夠針對 SQL 資料倉儲資料庫檢視並執行查詢。


## 步驟 1：下載並安裝 Visual Studio。

如果選擇安裝 Visual Studio，您可以使用 Visual Studio 2013 或 Visual Studio 2015 與 SQL 資料倉儲搭配。如果已安裝 Visual Studio 2013 或 2015，請跳至步驟 2 安裝 SSDT。

若要安裝 Visual Studio 2015：

1. 從 Visual Studio Online [下載 Visual Studio 2015](https://www.visualstudio.com/downloads)。 
2. 依照 MSDN 上的[安裝 Visual Studio](https://msdn.microsoft.com/library/e2h7fzkw.aspx) 指南進行安裝，並選擇預設組態。

## 步驟 2：下載並安裝最新的 SQL Server Data Tools (SSDT) 

不論是否已安裝 Visual Studio，您仍然需要最新版本的 SQL Server 資料工具 (SSDT)，才能支援 SQL 資料倉儲。

若要安裝最新版本的 SSDT：

1. [下載 SQL Server Data Tools Preview](https://msdn.microsoft.com/library/mt204009.aspx) (適用於 Visual Studio 2013 或 2015)。
2. 依照下載網站上的指示進行安裝。

## 後續步驟

既然有了最新版本的 SSDT，您可以開始[連接](./sql-data-warehouse-get-started-connect.md)到資料庫。

<!--Anchors-->

<!--Image references-->

<!---HONumber=Oct15_HO4-->