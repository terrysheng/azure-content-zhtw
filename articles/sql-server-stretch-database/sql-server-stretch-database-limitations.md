<properties
	pageTitle="Stretch Database 的介面區域限制和封鎖問題 | Microsoft Azure"
	description="了解在啟用 Stretch Database 前必須解決的封鎖問題。"
	services="sql-server-stretch-database"
	documentationCenter=""
	authors="douglaslMS"
	manager=""
	editor=""/>

<tags
	ms.service="sql-server-stretch-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/26/2016"
	ms.author="douglasl"/>

# Stretch Database 的介面區域限制和封鎖問題

了解在啟用 Stretch Database 前必須解決的封鎖問題。

## <a name="Limitations"></a>封鎖問題
在目前的 SQL Server 2016 預覽版本中，下列項目會讓資料表不適用於 Stretch。

**資料表屬性**
-   資料行超過 1,023 個

-   索引超過 998 個

-   包含 FILESTREAM 資料的資料表

-   Filetable

-   複寫的資料表

-   目前正在使用變更追蹤或異動資料擷取的資料表

-   記憶體最佳化資料表

**資料類型和資料行屬性**
-   timestamp

-   sql\_variant

-   XML

-   geometry

-   geography

-   hierarchyid

-   CLR 使用者定義的類型 (UDT)

**資料行類型**
-   COLUMN\_SET

-   計算資料行

**條件約束**
-   檢查條件約束

-   預設條件約束

-   參考資料表的外部索引鍵條件約束

    您無法在其上啟用 Stretch Database 的資料表，是外部索引鍵條件約束所參考的資料表。在父子式關聯性 (例如，訂單和訂單詳細資料) 中，這是父資料表 (訂單)。

**索引數**
-   全文檢索索引

-   XML 索引

-   空間索引

-   參考資料表的索引檢視表

## <a name="Caveats"></a>Stretch 資料表的限制和注意事項
在目前的 SQL Server 2016 預覽版本中，Stretch 資料表有下列限制或注意事項。

-   在 Stretch 表格中，「唯一」條件約束和「主要金鑰」條件約束並無強制唯一性。

-   您無法在 Stretch 資料表上執行「更新」或「刪除」作業。

-   您無法從遠端插入到連結伺服器上啟用 Stretch 的資料表。

-   您無法搭配啟用 Stretch 的資料表使用複寫功能。

-   您無法建立其中包含 Stretch 資料表的檢視表索引。

-   您無法更新或刪除其中包含 Stretch 資料表的檢視表。不過，您可以插入到其中包含 Stretch 資料表的檢視表。

-   索引篩選不會傳播至遠端資料表。

## 另請參閱

[透過執行 Stretch Database Advisor 為資料庫和資料表識別 Stretch Database](sql-server-stretch-database-identify-databases.md)

[為資料庫啟用 Stretch Database](sql-server-stretch-database-enable-database.md)

[為資料表啟用 Stretch Database](sql-server-stretch-database-enable-table.md)

<!---HONumber=AcomDC_0323_2016-->