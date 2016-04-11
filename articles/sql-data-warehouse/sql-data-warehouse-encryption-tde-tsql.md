<properties
   pageTitle="開始使用 SQL 資料倉儲透明資料加密 (TDE) TSQL| Microsoft Azure"
   description="開始使用 SQL 資料倉儲透明資料加密 (TDE) TSQL"
   services="sql-data-warehouse"
   documentationCenter=""
   authors="twounder"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.workload="data-management"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="03/23/2016"
   ms.author="mausher;barbkess;sonyama"/>

# 開始使用透明資料加密 (TDE)
> [AZURE.SELECTOR]
- [Azure 傳統入口網站](sql-data-warehouse-encryption-tde.md)
- [TSQL](sql-data-warehouse-encryption-tde-tsql.md)

Azure SQL 資料倉儲透明資料加密 (TDE) 可在不需變更應用程式的情況下，對靜止的資料庫、相關聯的備份和交易記錄檔執行即時加密和解密，協助防止惡意活動的威脅。

TDE 會使用稱為資料庫加密金鑰的對稱金鑰來加密整個資料庫的儲存體。在 SQL Database 中，資料庫加密金鑰是由內建伺服器憑證保護。內建伺服器憑證對每個 SQL Database 伺服器都是唯一的。Microsoft 至少每 90 天會自動替換這些憑證。如需 TDE 的一般描述，請參閱[透明資料加密 (TDE)]。

##啟用加密

若要啟用 SQL 資料倉儲的 TDE，請遵循下列步驟：

1. 使用在 master 資料庫中做為系統管理員或 **dbmanager** 角色成員的登入，連接到裝載資料庫之伺服器上的 *master* 資料庫
2. 執行下列陳述式來加密資料庫。

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION ON;
```

##停用加密

若要停用 SQL 資料倉儲的 TDE，請遵循下列步驟：

1. 使用在 master 資料庫中做為系統管理員或 **dbmanager** 角色成員的登入，連接到 *master* 資料庫
2. 執行下列陳述式來加密資料庫。

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION OFF;
```

##確認加密

若要確認 SQL 資料倉儲的加密狀態，請遵循下列步驟：

1. 使用在 master 資料庫中做為系統管理員或 **dbmanager** 角色成員的登入，連接到 *master* 或執行個體資料庫
2. 執行下列陳述式來加密資料庫。

```sql
SELECT
	[name],
	[is_encrypted]
FROM
	sys.databases;
```

```1``` 的結果表示加密的資料庫，```0``` 表示未加密的資料庫。


<!--Anchors-->
[透明資料加密 (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx


<!--Image references-->

<!--Link references-->

<!---HONumber=AcomDC_0330_2016-->