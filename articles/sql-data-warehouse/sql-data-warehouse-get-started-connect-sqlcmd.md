<properties
   pageTitle="入門：連接到 Azure SQL 資料倉儲 | Microsoft Azure"
   description="開始連線到 SQL 資料倉儲並執行一些查詢。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyama"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="03/28/2016"
   ms.author="mausher;barbkess;sonyama"/>

# 使用 SQLCMD 連接及查詢

> [AZURE.SELECTOR]
- [Visual Studio](sql-data-warehouse-get-started-connect.md)
- [SQLCMD](sql-data-warehouse-get-started-connect-sqlcmd.md)

本逐步解說將示範使用 sqlcmd.exe 公用程式在短時間內連接和查詢 Azure SQL 資料倉儲資料庫的方式。在本逐步解說中，您將：

+ 安裝必要軟體
+ 連接到包含 AdventureWorksDW 範例資料庫的資料庫
+ 針對範例資料庫執行查詢  

## 必要條件

+ [sqlcmd.exe](https://msdn.microsoft.com/library/azure/ms162773.aspx) - 若要下載 sqlcmd.exe，請參閱 [適用於 SQL Server 的 Microsoft 命令列公用程式 11](http://go.microsoft.com/fwlink/?LinkId=321501)。

## 取得您的完整 Azure SQL 伺服器名稱

若要連接至您的資料庫，需要包含您想連接之資料庫的伺服器完整名稱 (****servername**.database.windows.net*)。

1. 移至 [Azure 入口網站](https://portal.azure.com)。
2. 瀏覽至您想連接的資料庫。
3. 找出完整的伺服器名稱 (我們將在下列步驟中使用此名稱)：

![][1]


## 使用 sqlcmd 連接到 SQL 資料倉儲

若要在使用 sqlcmd 時連接到 SQL 資料倉儲的特定執行個體，您必須開啟命令提示字元並輸入 **sqlcmd**，後面接著 SQL 資料倉儲資料庫的連接字串。連接字串需要下列必要參數：

+ **伺服器 (-S)：** 採用 `<`Server Name`>`.database.windows.net 格式的伺服器
+ **資料庫 (-d)：**資料庫名稱。
+ **使用者 (-U)：**`<`使用者`>`表單中的伺服器使用者
+ **密碼 (-P)：**與使用者相關聯的密碼。
+ **啟用引號識別碼 (-I)：**必須啟用引號識別碼，才能連接到 SQL 資料倉儲執行個體。

因此，若要連接到 SQL 資料倉儲執行個體，您需輸入下列資訊：

```sql
C:\>sqlcmd -S <Server Name>.database.windows.net -d <Database> -U <User> -P <Password> -I
```

## 執行範例查詢

連線之後，您可以對執行個體發出任何支援的 Transact-SQL 陳述式。

```sql
C:\>sqlcmd -S <Server Name>.database.windows.net -d <Database> -U <User> -P <Password> -I
1> SELECT name FROM sys.tables;
2> GO
3> QUIT
```

如需 sqlcmd 的其他資訊，請參閱 [sqlcmd 文件](https://msdn.microsoft.com/library/azure/ms162773.aspx)。


## 後續步驟

您現在可以連接並查詢，請嘗試[使用 PowerBI 連接][]。

[使用 PowerBI 連接]: ./sql-data-warehouse-integrate-power-bi.md


<!--Image references-->
[1]: ./media/sql-data-warehouse-get-started-connect/get-server-name.png

<!---HONumber=AcomDC_0330_2016-->