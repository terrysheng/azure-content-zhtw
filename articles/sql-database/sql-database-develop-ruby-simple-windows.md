<properties
	pageTitle="使用 Ruby 和 Windows 上的 TinyTDS 連接到 SQL Database"
	description="提供您可在 Windows 上執行以連接到 Azure SQL Database 的 Ruby 程式碼範例。"
	services="sql-database"
	documentationCenter=""
	authors="meet-bhagdev"
	manager="jeffreyg"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="sql-database"
	ms.tgt_pltfrm="na"
	ms.devlang="ruby"
	ms.topic="article"
	ms.date="03/14/2016"
	ms.author="meetb"/>


# 在 Windows 上使用 Ruby 連接到 SQL Database

[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]

本主題提供可在執行 Windows 8.1 的 Windows 電腦上執行，以連接到 Azure SQL Database 的 Ruby 程式碼範例。


## 步驟 1︰設定開發環境

[使用 TinyTDS Ruby Driver for SQL Server 的必要條件](https://msdn.microsoft.com/library/mt711041.aspx#Windows)

## 步驟 2：建立 SQL Database

請參閱[快速入門頁面](sql-database-get-started.md)，以了解如何建立範例資料庫。請務必遵循該指南以建立 **AdventureWorks 資料庫範本**。以下所示的範例僅適用於 **AdventureWorks 結構描述**。


## 步驟 3：取得連接詳細資料

[AZURE.INCLUDE [sql-database-include-connection-string-details-20-portalshots](../../includes/sql-database-include-connection-string-details-20-portalshots.md)]

## 步驟 4︰連接

[TinyTDS::Client](https://github.com/rails-sqlserver/tiny_tds) 函式可用來連接到 SQL Database。

    require 'tiny_tds'
    client = TinyTds::Client.new username: 'yourusername@yourserver', password: 'yourpassword',
    host: 'yourserver.database.windows.net', port: 1433,
    database: 'AdventureWorks', azure:true

## 步驟 5：執行查詢

複製以下程式碼並貼到空白檔案中。稱它為 test.rb。然後執行它，方法是從命令提示字元輸入下列命令：

	ruby test.rb

在程式碼範例中，[TinyTds::Result](https://github.com/rails-sqlserver/tiny_tds) 函數可用來從針對 SQL Database 的查詢擷取結果集。此函數會接受查詢，並傳回結果集。結果集可使用 [result.each do |row|](https://github.com/rails-sqlserver/tiny_tds) 重複列舉。

    require 'tiny_tds'  
    print 'test'     
    client = TinyTds::Client.new username: 'yourusername@yourserver', password: 'yourpassword',
    host: 'yourserver.database.windows.net', port: 1433,
    database: 'AdventureWorks', azure:true
    results = client.execute("SELECT c.CustomerID, c.CompanyName,COUNT(soh.SalesOrderID) AS OrderCount FROM SalesLT.Customer AS c LEFT OUTER JOIN SalesLT.SalesOrderHeader AS soh ON c.CustomerID = soh.CustomerID GROUP BY c.CustomerID, c.CompanyName ORDER BY OrderCount DESC")
    results.each do |row|
    puts row
    end

## 步驟 6：插入資料列

在這個範例中，您將了解如何安全地執行 [INSERT](https://msdn.microsoft.com/library/ms174335.aspx) 陳述式、傳遞透過 [SQL 插入](https://technet.microsoft.com/library/ms161953(v=sql.105).aspx) 弱點保護您應用程式的參數，以及擷取自動產生的[主索引鍵](https://msdn.microsoft.com/library/ms179610.aspx)值。

若要搭配使用 TinyTDS 和 Azure，建議您執行多個 `SET` 陳述式來變更目前工作階段處理特定資訊的方式。建議使用程式碼範例中所提供的 `SET` 陳述式。例如，即使未明確陳述資料行的可為 null 狀態，`SET ANSI_NULL_DFLT_ON` 也可讓新建立的資料行允許 null 值。

若要配合 Microsoft SQL Server [datetime](http://msdn.microsoft.com/library/ms187819.aspx) 格式，請使用 [strftime](http://ruby-doc.org/core-2.2.0/Time.html#method-i-strftime) 函數轉換成相對應的日期時間格式。

    require 'tiny_tds'
    client = TinyTds::Client.new username: 'yourusername@yourserver', password: 'yourpassword',
    host: 'yourserver.database.windows.net', port: 1433,
    database: 'AdventureWorks', azure:true
    results = client.execute("SET ANSI_NULLS ON")
    results = client.execute("SET CURSOR_CLOSE_ON_COMMIT OFF")
    results = client.execute("SET ANSI_NULL_DFLT_ON ON")
    results = client.execute("SET IMPLICIT_TRANSACTIONS OFF")
    results = client.execute("SET ANSI_PADDING ON")
    results = client.execute("SET QUOTED_IDENTIFIER ON")
    results = client.execute("SET ANSI_WARNINGS ON")
    results = client.execute("SET CONCAT_NULL_YIELDS_NULL ON")
    require 'date'
    t = Time.now
    curr_date = t.strftime("%Y-%m-%d %H:%M:%S.%L")
    results = client.execute("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate)
    OUTPUT INSERTED.ProductID VALUES ('SQL Server Express New', 'SQLEXPRESS New', 0, 0, '#{curr_date}' )")
    results.each do |row|
    puts row
    end

<!---HONumber=AcomDC_0330_2016-->