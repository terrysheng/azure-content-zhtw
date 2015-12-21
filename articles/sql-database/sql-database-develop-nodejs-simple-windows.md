<properties
	pageTitle="在 Windows 上使用 Node.js 連接到 SQL Database"
	description="提供可用來連接到 Azure SQL Database 的 Node.js 程式碼範例。這個範例會在 Windows 用戶端電腦上執行。"
	services="sql-database"
	documentationCenter=""
	authors="meet-bhagdev"
	manager="jeffreyg"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="nodejs"
	ms.topic="article"
	ms.date="12/08/2015"
	ms.author="meetb"/>


# 在 Windows 上使用 Node.js 連接到 SQL Database


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


本主題提供可用來連接到 Azure SQL Database 的 Node.js 程式碼範例。Node.js 程式會在 Windows 用戶端電腦上執行。若要管理連接，請使用 msnodesql 驅動程式。


## 必要條件


用戶端開發電腦上必須有下列軟體項目。


-  Node.js - [0\.8.9 版 (32 位元版本)](http://blog.nodejs.org/2012/09/11/node-v0-8-9-stable/)。捲動並按一下適用於 32 位元 x86 的 Window Installer 下載連結，而不是 Windows x64 Installer 64 位元。
- [Python 2.7.6](https://www.python.org/download/releases/2.7.6/)，適用於 x86 或 x64 的安裝程式。
- [Visual C++ 2010](https://app.vssps.visualstudio.com/profile/review?download=true&family=VisualStudioCExpress&release=VisualStudio2010&type=web&slcid=0x409&context=eyJwZSI6MSwicGMiOjEsImljIjoxLCJhbyI6MCwiYW0iOjEsIm9wIjpudWxsLCJhZCI6bnVsbCwiZmEiOjAsImF1IjpudWxsLCJjdiI6OTY4OTg2MzU1LCJmcyI6MCwic3UiOjAsImVyIjoxfQ2) - Microsoft 免費提供的 Express 版。
- SQL Server Native Client 11.0 - 以 [SQL Server 2012 功能套件](http://www.microsoft.com/download/details.aspx?id=29065)中的 Microsoft SQL Server 2012 Native Client 提供。


### 安裝必要的模組

在您達成需求後，請確定您是在 Node.js 版本 0.8.9 上。您可以從您的命令列終端機，使用下列命令：node -v.。<br>在 **cmd.exe** 命令列視窗中，瀏覽至您的專案目錄，例如：C:\\NodeJSSQLProject。請依照顯示的順序輸入下列命令。

	npm init
	npm install msnodesql

接著瀏覽至 node\_modules\\msnodesql 資料夾，並執行 **msnodesql 0.2.1-v0.8 ia32** 可執行檔。請遵循安裝精靈的步驟，並在完成時叫按一下 [完成]。此時您應已安裝 Node.js SQL Server 驅動程式。請依照後續步驟以取得連接字串，然後您應能從 Node.js 應用程式連接到 Azure SQL DB。


### SQL Database

請參閱[快速入門頁面](sql-database-get-started.md)，以了解如何建立範例資料庫。請務必遵循該指南以建立 **AdventureWorks 資料庫範本**。以下所示的範例僅適用於 **AdventureWorks 結構描述**。


## 步驟 1：取得連線詳細資料

[AZURE.INCLUDE [sql-database-include-connection-string-details-20-portalshots](../../includes/sql-database-include-connection-string-details-20-portalshots.md)]

## 步驟 2：連接


- 將下列程式碼複製到您的專案目錄中的 .js 檔案。


		var http = require('http');
		var sql = require('msnodesql');
		var http = require('http');
		var fs = require('fs');
		var useTrustedConnection = false;
		var conn_str = "Driver={SQL Server Native Client 11.0};Server=tcp:yourserver.database.windows.net;" +
		(useTrustedConnection == true ? "Trusted_Connection={Yes};" : "UID=yourusername;PWD=yourpassword;") +
		"Database={AdventureWorks};"
		sql.open(conn_str, function (err, conn) {
		    if (err) {
		        console.log("Error opening the connection!");
		        return;
		    }
		    else
		        console.log("Successfuly connected");
		});


- 現在發出下列命令來執行您的 .js 檔案。


		node index.js


## 步驟 3：執行查詢


	var http = require('http');
	var sql = require('msnodesql');
	var http = require('http');
	var fs = require('fs');
	var useTrustedConnection = false;
	var conn_str = "Driver={SQL Server Native Client 11.0};Server=tcp:yourserver.database.windows.net;" +
	(useTrustedConnection == true ? "Trusted_Connection={Yes};" : "UID=yourusername;PWD=yourpassword;") +
	"Database={AdventureWorks};"
	sql.open(conn_str, function (err, conn) {
	    if (err) {
	        console.log("Error opening the connection!");
	        return;
	    }
	    else
	        console.log("Successfuly connected");


	    conn.queryRaw("SELECT c.CustomerID, c.CompanyName,COUNT(soh.SalesOrderID) AS OrderCount FROM SalesLT.Customer AS c LEFT OUTER JOIN SalesLT.SalesOrderHeader AS soh ON c.CustomerID = soh.CustomerID GROUP BY c.CustomerID, c.CompanyName ORDER BY OrderCount DESC;", function (err, results) {
	        if (err) {
	            console.log("Error running query1!");
	            return;
	        }
	        for (var i = 0; i < results.rows.length; i++) {
	            console.log(results.rows[i]);
	        }
	    });
	});


## 步驟 4：插入資料列


	var http = require('http');
	var sql = require('msnodesql');
	var http = require('http');
	var fs = require('fs');
	var useTrustedConnection = false;
	var conn_str = "Driver={SQL Server Native Client 11.0};Server=tcp:yourserver.database.windows.net;" +
	(useTrustedConnection == true ? "Trusted_Connection={Yes};" : "UID=yourusername;PWD=yourpassword;") +
	"Database={AdventureWorks};"
	sql.open(conn_str, function (err, conn) {
	    if (err) {
	        console.log("Error opening the connection!");
	        return;
	    }
	    else
	        console.log("Successfuly connected");


	    conn.queryRaw("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('SQL Server Express', 'SQLEXPRESS', 0, 0, CURRENT_TIMESTAMP)", function (err, results) {
	        if (err) {
	            console.log("Error running query!");
	            return;
	        }
	        for (var i = 0; i < results.rows.length; i++) {
	            console.log("Product ID Inserted : "+results.rows[i]);
	        }
	    });
	});


## 步驟 5：回復交易


**conn.beginTransactions** 方法無法在 Azure SQL Database 中運作。相反地，請遵循下列程式碼範例，以在 SQL Database 中執行交易。


	var http = require('http');
	var sql = require('msnodesql');
	var http = require('http');
	var fs = require('fs');
	var useTrustedConnection = false;
	var conn_str = "Driver={SQL Server Native Client 11.0};Server=tcp:yourserver.database.windows.net;" +
	(useTrustedConnection == true ? "Trusted_Connection={Yes};" : "UID=yourusername;PWD=yourpassword;") +
	"Database={AdventureWorks};"
	sql.open(conn_str, function (err, conn) {
	    if (err) {
	        console.log("Error opening the connection!");
	        return;
	    }
	    else
	        console.log("Successfuly connected");


	    conn.queryRaw("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('SQL Server Express New ', 'SQLEXPRESS New', 1, 1, CURRENT_TIMESTAMP)", function (err, results) {
	        if (err) {
	            console.log("Error running query!");
	            return;
	        }
	        for (var i = 0; i < results.rows.length; i++) {
	            console.log("Product ID Inserted : "+results.rows[i]);
	        }
	    });

	    conn.queryRaw("ROLLBACK TRANSACTION; ", function (err, results) {
            	if (err) {
        		console.log("Rollback failed");
        		return;
        	}
    	    });
	});


## 步驟 6：預存程序

您必須先擁有或建立不輸入任何參數的預存程序，這個程式碼範例才能運作。您可以使用 SQL Server Management Studio (SSMS.exe) 等工具建立預存程序。


	var http = require('http');
	var sql = require('msnodesql');
	var http = require('http');
	var fs = require('fs');
	var useTrustedConnection = false;
	var conn_str = "Driver={SQL Server Native Client 11.0};Server=tcp:yourserver.database.windows.net;" +
	(useTrustedConnection == true ? "Trusted_Connection={Yes};" : "UID=yourusername;PWD=yourpassword;") +
	"Database={AdventureWorks};"
	sql.open(conn_str, function (err, conn) {
	    if (err) {
	        console.log("Error opening the connection!");
	        return;
	    }
	    else
	        console.log("Successfuly connected");

	    conn.query("exec NameOfStoredProcedure", function (err, results) {
	    	if (err) {
			console.log("Error running query8!");
			return;
		}
	    });
	});


## 後續步驟

如需詳細資訊，請參閱 [Node.js 開發人員中心](/develop/nodejs/)。

<!---HONumber=AcomDC_1210_2015-->