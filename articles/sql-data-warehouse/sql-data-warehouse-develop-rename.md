<properties
   pageTitle="在 SQL 資料倉儲中重新命名 | Microsoft Azure"
   description="在「Azure SQL 資料倉儲」中重新命名資料表以開發解決方案的秘訣。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="twounder"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="03/03/2016"
   ms.author="mausher;jrj;barbkess;sonyama"/>

# 在 SQL 資料倉儲中重新命名
SQL Server 是透過預存程序 ```sp_renamedb``` 支援資料庫重新命名，而「SQL 資料倉儲」則是使用 DDL 語法來達到相同的目的。DDL 命令是 ```RENAME OBJECT```。

## 重新命名資料表

目前只有資料表可以重新命名。重新命名資料表的語法是：

```
RENAME OBJECT Customer TO NewCustomer;
```

重新命名資料表時，會將與資料表關聯的所有物件和屬性都更新成參考新的資料表名稱。例如，會更新資料表定義、索引、條件約束及權限。不會更新檢視。

## 重新命名外部資料表

重新命名外部資料表會變更 SQL Server PDW 內的資料表名稱。它不會影響該資料表的外部資料位置。

## 變更資料表結構描述
如果目的是要變更物件所屬的結構描述，可以透過 ALTER SCHEMA 達成：

```
ALTER SCHEMA dbo TRANSFER OBJECT::product.item;
```

## 資料表重新命名需要獨佔的資料表鎖定

請務必記得，您無法在資料表處於使用中時將它重新命名。重新命名資料表時，需要該資料表的獨佔鎖定。如果資料表處於使用中，您可能需要終止使用該資料表的工作階段。若要終止工作階段，您必須使用 [KILL](https://msdn.microsoft.com/library/ms173730.aspx) 命令。使用 ```KILL``` 時請小心，因為終止工作階段時，會回復所有未認可的工作。「SQL 資料倉儲」中的工作階段會以 'SID' 做為首碼。叫用 KILL 命令時，將需要包含此首碼和工作階段號碼。例如：```KILL 'SID1234'```


## 後續步驟
如需更多開發祕訣，請參閱[開發概觀][]。

<!--Image references-->

<!--Article references-->
[開發概觀]: sql-data-warehouse-overview-develop.md

<!---HONumber=AcomDC_0309_2016-->