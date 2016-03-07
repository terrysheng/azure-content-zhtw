<properties
   pageTitle="在移轉至 SQL 資料庫之前修正 SQL Server 資料庫相容性問題"
   description="Microsoft Azure SQL Database, 資料庫移轉, 資料庫相容性, SQL Azure 移轉精靈, SSDT"
   services="sql-database"
   documentationCenter=""
   authors="carlrabeler"
   manager="jeffreyg"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="02/22/2016"
   ms.author="carlrab"/>

# 在移轉至 SQL Database 之前，透過 SSDT 修正 SQL Server 資料庫相容性問題

如果您判斷來源 SQL Server 資料庫不相容，您有許多選項可修正已識別的資料庫相容性問題。

> [AZURE.SELECTOR]
- Use [SQL Azure Migration Wizard](sql-database-cloud-migrate-fix-compatibility-issues.md)
- Use [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
- Use [SSMS](sql-database-cloud-migrate-fix-compatibility-issues-SSMS.md)

## 使用 Visual Studio 的 SQL Server Data Tools

您可以使用 Visual Studio 的 SQL Server Data Tools ("SSDT") 將資料庫結構描述匯入 Visual Studio 資料庫專案以進行分析。若要進行分析，請將專案的目標平台指定為 SQL Database V12，然後再建置專案。如果建置成功，則資料庫為相容。如果建置失敗，您可以在 SSDT (或本主題中討論的其他工具之一) 中解析錯誤。一旦專案成功建置，您可以將該專案發佈回來做為來源資料庫的複本，然後使用 SSDT 中的資料比較功能，將資料從來源資料庫複製到 Azure SQL V12 相容資料庫。然後，您可以移轉此更新的資料庫。若要使用此選項，請下載[最新版本的 SSDT](https://msdn.microsoft.com/library/mt204009.aspx)。

  ![VSSSDT 移轉圖表](./media/sql-database-cloud-migrate/03VSSSDTDiagram.png)

  > [AZURE.NOTE] 如果您需要的是僅限結構描述的移轉，則可以直接從 Visual Studio 將結構描述發佈至 Azure SQL Database。當資料庫結構描述需要的變更超過移轉精靈單獨可處理的數量時，使用這個方法。

## 下一步：選取移轉方法，並執行移轉

[選取移轉方法](sql-database-cloud-migrate.md#migrate-a-compatible-sql-server-database-to-sql-database)。

<!---HONumber=AcomDC_0224_2016-->