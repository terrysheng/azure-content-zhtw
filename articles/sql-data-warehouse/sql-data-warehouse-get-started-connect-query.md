<properties
   pageTitle="入門：連接到 Azure SQL 資料倉儲 | Microsoft Azure"
   description="開始連線到 SQL 資料倉儲並執行一些查詢。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="twounder"
   manager=""
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="01/04/2016"
   ms.author="twounder;barbkess"/>

# 使用 Visual Studio 連接及查詢

> [AZURE.SELECTOR]
- [Visual Studio](sql-data-warehouse-get-started-connect.md)
- [SQLCMD](sql-data-warehouse-get-started-connect-sqlcmd.md)

本逐步解說將示範使用 Visual Studio 在短時間內連接和查詢 Azure SQL 資料倉儲資料庫的方式。在本逐步解說中，您將：

+ 安裝必要軟體
+ 連接到包含 AdventureWorksDW 範例資料庫的資料庫
+ 針對範例資料庫執行查詢  

## 必要條件

+ Visual Studio 2013/2015 - 若要下載並安裝 Visual Studio 2015 及/或 SSDT，請參閱[安裝 Visual Studio 與 SSDT](sql-data-warehouse-install-visual-studio.md)

## 取得您的完整 Azure SQL 伺服器名稱

若要連接至您的資料庫，需要包含您想連接之資料庫的伺服器完整名稱 (***servername**.database.windows.net*)。

1. 移至 [Azure 入口網站](https://portal.azure.com)。
2. 瀏覽至您想連接的資料庫。
3. 找出完整的伺服器名稱 (我們將在下列步驟中使用此名稱)：

![][1]

## 連接到您的 SQL Database

1. 開啟 Visual Studio。
2. 從 [檢視] 功能表開啟 **SQL Server 物件總管**
 
![][2]

3. 按一下 [新增 SQL Server] 按鈕

![][3]

4. 輸入我們在上方擷取的*伺服器名稱*
5. 在 [驗證] 清單中，選取 [SQL Server 驗證]。
6. 輸入您建立 SQL Database 伺服器時指定的「登入」和「密碼」，然後按一下 [連接]。

## 執行範例查詢

我們現已註冊我們的伺服器，接著繼續撰寫查詢。

1. 按一下 SSDT 中的使用者資料庫。

2. 按一下 [新增查詢] 按鈕。新的視窗隨即開啟。

![][4]

3. 將下列內程式碼輸入查詢視窗中：

	```
	SELECT COUNT(*) FROM dbo.FactInternetSales;
	```

4. 執行查詢。

	若要執行查詢，請按一下綠色箭頭，或使用下列快速鍵：`CTRL`+`SHIFT`+`E`。

## 後續步驟

您現在可以連接並查詢，請嘗試[使用 PowerBI 連接][]。

[使用 PowerBI 連接]: ./sql-data-warehouse-integrate-power-bi.md


<!--Image references-->

[1]: ./media/sql-data-warehouse-get-started-connect-query/get-server-name.png
[2]: ./media/sql-data-warehouse-get-started-connect-query/open-ssdt.png
[3]: ./media/sql-data-warehouse-get-started-connect-query/connection-dialog.png
[4]: ./media/sql-data-warehouse-get-started-connect-query/new-query.png

<!---HONumber=AcomDC_0107_2016-->