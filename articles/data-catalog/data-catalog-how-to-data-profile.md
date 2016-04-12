<properties
   pageTitle="如何對資料來源進行資料分析"
   description="專門說明如何在 Azure 資料目錄中註冊資料來源時包含資料表和資料行層級的資料設定檔以及如何使用資料設定檔來了解資料來源的操作說明文章。"
   services="data-catalog"
   documentationCenter=""
   authors="dvana"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="03/31/2016"
   ms.author="derrickv"/>

# 對資料來源進行資料分析

## 簡介

[Microsoft Azure 資料目錄] 是全面管理的雲端服務，可作為企業資料來源的註冊系統和探索系統。換句話說，**Azure 資料目錄**的重點在於協助人們探索、了解，以及使用資料來源，並可協助組織從現有的資料獲得更多價值。當資料來源向 **Azure 資料目錄**註冊之後，該服務會複製其中繼資料並建立索引，但不僅止於此。

**Azure 資料目錄**會檢查目錄所支援資料來源中的資料，並收集關於該資料的統計資料和資訊。這稱為**資料分析**。想要包含資料資產的設定檔很容易。當您註冊資料資產時，請選擇資料來源註冊工具中的 [包含資料設定檔]。

## 什麼是資料分析

資料分析會檢查所註冊資料來源中的資料，並收集關於該資料的統計資料和資訊。在探索資料來源期間，這些統計資料可以協助使用者判斷資料是否適合用來解決其商務問題。

<!-- In [How to discover data sources](data-catalog-how-to-discover.md), you learn about **Azure Data Catalog's** extensive search capabilities including searching for data assets that have a profile. See [How to include a data profile when registering a data source](#howto). -->

下列資料來源都支援資料分析︰

- SQL Server (包括 Azure SQL DB 和 Azure SQL 資料倉儲) 資料表和檢視
- Oracle 資料表和檢視
- Teradata 資料表和檢視
- Hive 資料表

在註冊資料資產時包含資料設定檔可幫助使用者回答資料來源的相關問題，包括︰

-	是否可用來解決商務問題？
-	資料是否符合特定標準或模式？
-	資料來源有哪些異常之處？
-	將此資料整合到應用程式時可能面臨哪些挑戰？

> [AZURE.NOTE] 您也可以對資產新增說明文件來描述如何將資料整合到應用程式。請參閱[如何記載資料來源](data-catalog-how-to-documentation.md)。


<a name="howto"/>
## 如何在註冊資料來源時包含資料設定檔

想要包含資料來源的設定檔很容易。當您在註冊資料來源時，請在資料來源註冊工具的 [要註冊的物件] 面板中選擇 [包含資料設定檔]。

![](media\data-catalog-data-profile\data-catalog-register-profile.png)

若要深入了解如何註冊資料來源，請參閱[如何註冊資料來源](data-catalog-how-to-register.md)和[開始使用 Azure 資料目錄](data-catalog-get-started.md)。


## 篩選包含資料設定檔的資料資產
若要探索包含資料設定檔的資料資產，您可以包含 **has:tableDataProfiles** 或 **has:columnsDataProfiles** 以做為搜尋字詞之一。

> [AZURE.NOTE] 在資料來源註冊工具中選取 [包含資料設定檔] 將會包含資料表和資料行層級的設定檔資訊，但是資料目錄 API 只允許在註冊資料資產時包含一組設定檔資訊。

## 檢視資料設定檔資訊

一旦您找到含有設定檔的合適資料來源，您可以檢視資料設定檔的詳細資料。若要檢視資料設定檔，請在資料目錄入口網站視窗中選取資料資產並選擇 [資料設定檔]。

![](media\data-catalog-data-profile\data-catalog-view.png)

[Azure 資料目錄] 中的資料設定檔會顯示資料表和資料行設定檔資訊，包括︰

### 物件資料設定檔

-	資料列數目
-	資料表大小
-	物件的上次更新時間

### 資料行資料設定檔

- 資料行資料類型
- 相異值數目
- 具有 NULL 值的資料列數目
- 資料行的最小值、最大值、平均值和標準差值

## 摘要
資料分析可提供關於註冊資料資產的統計資料和資訊，以協助使用者判斷資料是否適合用來解決商務問題。加上註解和記載資料來源後，資料設定檔可以讓使用者更深入了解資料。


## 另請參閱
-	[如何註冊資料來源](data-catalog-how-to-register.md)
-	[開始使用 Azure 資料目錄](data-catalog-get-started.md)

<!---HONumber=AcomDC_0406_2016-->