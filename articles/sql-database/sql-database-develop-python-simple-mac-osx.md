<properties
	pageTitle="在 Mac OS 上使用 Python 連接到 SQL Database"
	description="提供可用來從 Mac 連接到 Azure SQL Database 的 Python 程式碼範例。這個範例使用 pymssql 驅動程式。"
	services="sql-database"
	documentationCenter=""
	authors="meet-bhagdev"
	manager="jeffreyg"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="python"
	ms.topic="article"
	ms.date="12/08/2015"
	ms.author="meetb"/>


# 在 Mac OS 上使用 Python 連接到 SQL Database


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


本主題提供以 Python 撰寫的程式碼範例。這個範例會在 Mac 電腦上執行。這個範例使用 **pymssql** 驅動程式連接到 Azure SQL Database。此外，請使用[開始在 Mac 上使用 Python](https://www.youtube.com/watch?v=OMpugPTwnTI) 視訊以補充本文件。


## 先決條件


- [Python 2.7.6](https://www.python.org/download/releases/2.7.6/)。
- [FreeTDS](https://github.com/brianb/FreeTDS)
- [Pymssql](https://github.com/pymssql/pymssql)

### 安裝必要的模組


開啟您的終端機並安裝

**1) Homebrew**：從您的終端機執行下列命令：這會在您的電腦上下載 Homebrew 封裝管理員。

    ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"

**2) FreeTDS：**從您的終端機執行下列命令。這會在您的電腦上下載 FreeTDS。pymmsql 需要 FreeTDS 才能工作。

    brew install FreeTDS

**3) Pymmsql**：從您的終端機執行下列命令。這將會在您的電腦上安裝 pymmsql。

    sudo -H pip install pymssql

### SQL Database

請參閱[快速入門頁面](sql-database-get-started.md)，以了解如何建立範例資料庫。請務必遵循該指南以建立 **AdventureWorks 資料庫範本**。以下所示的範例僅適用於 **AdventureWorks 結構描述**。

## 步驟 1：取得連線詳細資料

[AZURE.INCLUDE [sql-database-include-connection-string-details-20-portalshots](../../includes/sql-database-include-connection-string-details-20-portalshots.md)]

## 步驟 2：連接

[pymssql.connect](http://pymssql.org/en/latest/ref/pymssql.html) 函式可用來連接到 SQL Database。

	import pymssql
	conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')


## 步驟 3：執行查詢

[cursor.execute](http://pymssql.org/en/latest/ref/pymssql.html#pymssql.Cursor.execute) 函式可用來擷取對 SQL Database 查詢的結果集。這個函式基本上會接受任何查詢並傳回結果集，您可以使用 [cursor.fetchone()](http://pymssql.org/en/latest/ref/pymssql.html#pymssql.Cursor.fetchone) 反覆查詢結果集。


	import pymssql
	conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')
	cursor = conn.cursor()
	cursor.execute('SELECT c.CustomerID, c.CompanyName,COUNT(soh.SalesOrderID) AS OrderCount FROM SalesLT.Customer AS c LEFT OUTER JOIN SalesLT.SalesOrderHeader AS soh ON c.CustomerID = soh.CustomerID GROUP BY c.CustomerID, c.CompanyName ORDER BY OrderCount DESC;')
	row = cursor.fetchone()
	while row:
	    print str(row[0]) + " " + str(row[1]) + " " + str(row[2]) 	
	    row = cursor.fetchone()


## 步驟 4：插入資料列

在這個範例中，您將了解如何安全地執行 [INSERT](https://msdn.microsoft.com/library/ms174335.aspx) 陳述式、傳遞透過 [SQL 插入](https://technet.microsoft.com/library/ms161953(v=sql.105).aspx) 弱點保護您應用程式的參數，以及擷取自動產生的[主索引鍵](https://msdn.microsoft.com/library/ms179610.aspx)值。



	import pymssql
	conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')
	cursor = conn.cursor()
	cursor.execute("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('SQL Server Express', 'SQLEXPRESS', 0, 0, CURRENT_TIMESTAMP)")
	row = cursor.fetchone()
	while row:
	    print "Inserted Product ID : " +str(row[0])
	    row = cursor.fetchone()


## 步驟 5：回復交易


這個程式碼範例示範如何使用交易，您將：


-開始交易

-插入一列資料

-回復您的交易以復原插入


	import pymssql
	conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')
	cursor = conn.cursor()
	cursor.execute("BEGIN TRANSACTION")
	cursor.execute("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('SQL Server Express New', 'SQLEXPRESS New', 0, 0, CURRENT_TIMESTAMP)")
	cnxn.rollback()


## 後續步驟

如需詳細資訊，請參閱 [Python 開發人員中心](/develop/python/)。

<!---HONumber=AcomDC_1210_2015-->