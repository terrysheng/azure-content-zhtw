<properties 
   pageTitle="Microsoft Azure 資料湖分析概觀 | Azure" 
   description="資料湖分析是一個 Azure 巨量資料計算服務，可讓您使用從雲端資料所獲得的深入見解，使用資料刺激您的業務，不論其所在位置和大小。資料湖分析可以用最簡單、最可調整及最經濟的方式，達成這個目的。" 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="mumian" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="10/28/2015"
   ms.author="jgao"/>

# Microsoft Azure 資料湖分析概觀

## 什麼是 Azure 資料湖分析？

Azure 資料湖分析是一個全新的服務，其目的是為了讓巨量資料分析更加容易。此服務可讓您專注於撰寫、執行和管理工作，而非操作分散式基礎結構。無需部署、設定及調整硬體，您只需寫入查詢便可轉換資料並擷取寶貴的深入見解。您只需針對所需的功能設定級別，此項分析服務便可立即處理任何規模的工作。只有在工作進行時您才需要支付費用，十分符合成本效益。分析服務支援 Azure Active Directory，讓您只需管理存取權及角色，且其與您的內部部署身分識別系統整合。分析服務支援還包括 U-SQL，該語言可將 SQL 的優勢與使用者程式碼的運算能力結合在一起。U-SQL 的可調整分散式執行階段可讓您有效地分析資料，範圍遍及存放區以及 Azure、Azure SQL Database 以及 Azure SQL 資料倉儲中的 SQL Server。


## 主要功能

- **動態結構描述** 

    資料湖分析專為雲端規模與效能全新打造。資料湖分析服務會動態部署資源，讓您可以分析數 TB 乃至於數 EB 的資料。當工作完成時，其會自動逐步釋放這些資源，因此您只需要支付所用處理功能的費用。當您所儲存的資料量或所用的運算量有所增減時，也無需重新編寫程式碼。如此您便可將心力全數投注在商業邏輯，而無需分神在處理與儲存大型資料集的方式上。

- **利用熟悉的工具縮短開發時間，並讓偵錯及最佳化工作更加敏捷**

    資料湖分析與 Visual Studio 的深度整合，讓您可以使用熟悉的工具執行、偵錯及調整您的程式碼。視覺化您的 U-SQL 工作可讓您了解程式碼在大規模執行時的狀況，從而輕鬆找出效能瓶頸與並達到最佳成本效益。

- **U-SQL：簡單、熟悉、強大且易於擴充**

    資料湖分析包括 U-SQL，此查詢語言透過 C# 的運算能力，將大家所熟悉 SQL 的簡單、宣告的性質加以擴充。U-SQL 語言是建立在提供 Microsoft 巨量資料系統資源的相同分散式執行階段之上。數百萬名 SQL 和 .NET 開發人員現在已經可以利用他們本來具備的技術處理及分析所有的資料。

- **與您的 IT 投資無縫整合**

    資料湖分析會運用您現有的 IT 投資，進行身分識別、管理、安全性及資料倉儲等工作。不僅資料管理得以簡化，擴充現有的資料應用程式也變得更為容易。資料湖分析與 Active Directory 整合以進行使用者管理與授予使用者權限，並隨附內建的監視與稽核功能。

- **經濟實惠並符合成本效益**

    資料湖分析可用於執行巨量資料工作負載，是十分符合成本效益的解決方案。所處理的資料將會以每件工作來計費。您無需簽訂任何硬體、授權或服務相關的支援合約。系統會自動在工作開始及完成時相應增加或減少，讓您的每一分錢都花在刀口上。

- **使用所有的 Azure 資料**

    資料湖分析使用大量的 Azure 資料來源：Azure Storage Blob、Azure SQL DB、Azure SQL DB，資料湖分析當然已特別針對與 Azure 資料湖存放區搭配使用最佳化，可提供最高等級的效能、輸送量，並會平行處理您的巨量資料工作負載。

## 另請參閱

- 開始使用
    - [使用 Azure Preview 入口網站開始使用資料湖分析](data-lake-analytics-get-started-portal.md)
    - [使用 Azure PowerShell 開始使用資料湖分析](data-lake-analytics-get-started-powershell.md)
    - [使用 Azure .NET SDK 開始使用資料湖分析](data-lake-analytics-get-started-net-sdk.md)
    - [使用適用於 Visual Studio 的資料湖工具開發 U-SQL 指令碼](data-lake-analytics-data-lake-tools-get-started.md)
    - [開始使用 Azure 資料湖分析 U-SQL 語言](data-lake-analytics-u-sql-get-started.md)
    
- U-SQL 和開發
    - [開始使用 Azure 資料湖分析 U-SQL 語言](data-lake-analytics-u-sql-get-started.md)
    - [針對 Azure 資料湖分析工作使用 U-SQL 視窗函式](data-lake-analytics-use-window-functions.md)
    - [針對資料湖分析工作開發 U-SQL 使用者定義運算子](data-lake-analtyics-u-sql-develop-user-defined-operators.md)
    
- 管理
    - [使用 Azure Preview 入口網站管理 Azure 資料湖分析](data-lake-analytics-manage-use-portal.md)
    - [使用 Azure PowerShell 管理 Azure 資料湖分析](data-lake-analytics-manage-use-powershell.md)
    - [使用 Azure Preview 入口網站監視和疑難排解 Azure 資料湖分析工作](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

- 端對端教學課程
    - [使用 Azure 資料湖分析互動式教學課程](data-lake-analytics-use-interactive-tutorials.md)
    - [使用 Azure 資料湖分析來分析網站記錄](data-lake-analytics-analyze-weblogs.md)

- 讓我們知道您的想法
    - [我們文件待處理項目的註解](data-lake-analytics-documentation-backlog.md)
    - [提交要求功能](http://aka.ms/adlafeedback)
    - [在論壇上取得協助](http://aka.ms/adlaforums)

<!---HONumber=Nov15_HO1-->