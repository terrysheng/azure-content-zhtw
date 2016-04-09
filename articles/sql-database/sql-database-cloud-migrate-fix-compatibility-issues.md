<properties
   pageTitle="在移轉至 SQL 資料庫之前修正 SQL Server 資料庫相容性問題"
   description="Microsoft Azure SQL Database, 資料庫移轉, 資料庫相容性, SQL Azure 移轉精靈"
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
   ms.date="03/14/2016"
   ms.author="carlrab"/>

# 在移轉至 SQL 資料庫之前修正 SQL Server 資料庫相容性問題

如果您判斷來源 SQL Server 資料庫不相容，您有許多選項可修正已識別的資料庫相容性問題。

> [AZURE.SELECTOR]
- 使用 [SQL Azure 移轉精靈](sql-database-cloud-migrate-fix-compatibility-issues.md)
- 使用 [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
- 使用 [SSMS](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md)

## 使用 SQL Azure 移轉精靈

您可以使用 [SQL Azure 移轉精靈](http://sqlazuremw.codeplex.com/) CodePlex 工具從不相容的來源資料庫產生 T-SQL 指令碼，精靈會轉換該資料庫使其與 SQL Database 相容，然後連接至 Azure SQL Database 以執行此指令碼。這項工具也會分析追蹤檔案以判斷相容性問題。只能使用結構描述產生指令碼，或者指令碼可以 BCP 格式包含資料。CodePlex 的 [SQL Azure 移轉精靈](http://sqlazuremw.codeplex.com/)提供其他文件，包括逐步指引。

 ![SAMW 移轉圖表](./media/sql-database-cloud-migrate/02SAMWDiagram.png)

  > [AZURE.NOTE] 請注意，並非精靈可偵測到之所有不相容的結構描述都可由內建轉換加以修正。無法處理的不相容指令碼將會報告為錯誤，並在產生的指令碼中插入註解。如果偵測到許多錯誤，請使用 Visual Studio 或 SQL Server Management Studio 來逐步執行和修正無法使用 SQL Server 移轉精靈修正的每個錯誤。

## 下一步：選取移轉方法，並執行移轉

[選取移轉方法](sql-database-cloud-migrate.md#migrate-a-compatible-sql-server-database-to-sql-database)。

<!---HONumber=AcomDC_0316_2016-->