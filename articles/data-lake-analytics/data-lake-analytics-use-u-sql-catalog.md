<properties
   pageTitle="介紹 Azure 資料湖分析 U-SQL 目錄 | Azure"
   description="介紹 Azure 資料湖分析 U-SQL 目錄"
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
   ms.date="10/26/2015"
   ms.author="jgao"/>

# 使用 U-SQL 目錄

U-SQL 目錄是用來建構資料和程式碼，讓 U-SQL 指令碼可以共用它們。目錄可以讓 Azure 資料湖中的資料具有可能的最高效能。

每個 Azure 資料湖分析帳戶都有一個與其相關聯的 U-SQL 目錄。您無法刪除 U-SQL 目錄。目前無法在資料湖存放區帳戶之間共用 U-SQL 目錄。

每個 U-SQL 目錄包含名為 **Master** 的資料庫。Master 資料庫無法刪除。每個 U-SQL 目錄可以包含多個額外資料庫。

U-SQL 資料庫包含：

- 組件 - 在 U-SQL 指令碼之間共用 .NET 程式碼。
- 資料表-值函式 - 在 U-SQL 指令碼之間共用 U-SQL 程式碼。
- 資料表 - 在 U-SQL 指令碼之間共用資料。
- 結構描述 - 在 U-SQL 指令碼之間共用資料表結構描述。

## 管理目錄
每個 Azure 資料湖分析帳戶有與其相關聯的預設 Azure 資料湖存放區帳戶。這個資料湖存放區帳戶稱為預設資料湖存放區帳戶。U-SQL 目錄會儲存在 /catalog 資料夾底下的預設資料湖存放區帳戶。不要刪除 /catalog 資料夾中的任何檔案。

### 使用 Azure 入口網站

請參閱[使用入口網站管理資料湖分析](data-lake-analytics-use-portal.md#view-u-sql-catalog)


### 使用適用於 Visual Studio 的資料湖工具：

您可以使用適用於 Visual Studio 的資料湖工具來管理目錄。如需工具的詳細資訊，請參閱[使用適用於 Visual Studio 的資料湖工具](data-lake-analytics-data-lake-tools-get-started.md)。

**管理目錄**

1. 開啟 Visual Studio 並連接至 Azure。如需指示，請參閱[連接至 Azure](data-lake-analytics-data-lake-tools-get-started.md#connect-to-azure)。
1. 按 **CTRL+ALT+S**，開啟 [伺服器總管]。
2. 從 [伺服器總管] 依序展開 [Azure]、[資料湖分析]、您的資料湖分析帳戶、[資料庫]，然後展開 [master]。



    - 若要新增新的資料庫，以滑鼠右鍵按一下 [資料庫]，然後按一下 [建立資料庫]。
    - 若要新增新的組件，以滑鼠右鍵按一下 [組件]，然後按一下 [註冊組件]。
    - 若要新增新的結構描述，以滑鼠右鍵按一下 [結構描述]，然後按一下 [建立結構描述]**。
    - 若要新增新的資料表，以滑鼠右鍵按一下 [資料表]，然後按一下 [建立資料表]**。
    - 若要新增新的資料表值函式，請參閱[針對資料湖分析工作開發 U-SQL 使用者定義運算子](data-lake-analytics-u-sql-develop-user-defined-operators.md)。


![瀏覽 U-SQL Visual Studio 目錄](./media/data-lake-analytics-use-u-sql-catalog/data-lake-analytics-browse-catalogs.png)


## 另請參閱

- 開始使用
    - [使用 Azure 入口網站開始使用資料湖分析](data-lake-analytics-get-started-portal.md)
    - [使用 Azure PowerShell 開始使用資料湖分析](data-lake-analytics-get-started-powershell.md)
    - [使用 Azure .NET SDK 開始使用資料湖分析](data-lake-analytics-get-started-net-sdk.md)
    - [使用適用於 Visual Studio 的資料湖工具開發 U-SQL 指令碼](data-lake-analytics-data-lake-tools-get-started.md)
    - [開始使用 Azure 資料湖分析 U-SQL 語言](data-lake-analytics-u-sql-get-started.md)

- U-SQL 和開發
    - [開始使用 Azure 資料湖分析 U-SQL 語言](data-lake-analytics-u-sql-get-started.md)
    - [針對 Azure 資料湖分析工作使用 U-SQL 視窗函式](data-lake-analytics-use-window-functions.md)
    - [針對資料湖分析工作開發 U-SQL 使用者定義運算子](data-lake-analtyics-u-sql-user-defined-operators.md)

- 管理
    - [使用 Azure 入口網站管理 Azure 資料湖分析](data-lake-analytics-use-portal.md)
    - [使用 Azure PowerShell 管理 Azure 資料湖分析](data-lake-analytics-use-powershell.md)
    - [使用 Azure 入口網站監視和疑難排解 Azure 資料湖分析工作](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

- 端對端教學課程
    - [使用 Azure 資料湖分析互動式教學課程](data-lake-analytics-use-interactive-tutorials.md)
    - [使用 Azure 資料湖分析來分析網站記錄](data-lake-analytics-analyze-weblogs.md)

<!---HONumber=AcomDC_1203_2015-->