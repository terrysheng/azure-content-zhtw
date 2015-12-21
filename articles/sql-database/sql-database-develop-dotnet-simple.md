<properties
	pageTitle="使用 .NET (C#) 連接到 SQL Database"
	description="使用這個快速入門中的範例程式碼建置現代應用程式，這個應用程式使用 C#，並受到具有 Azure SQL Database 的雲端中之強大關聯式資料庫的支援。"
	services="sql-database"
	documentationCenter=""
	authors="tobbox"
	manager="jeffreyg"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="sql-database"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="12/08/2015"
	ms.author="tobiast"/>


# 從 .NET (C#) 使用 SQL Database


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


## 先決條件

### .NET Framework

.NET Framework 會隨 Windows 預先安裝。若是 Linux 和 Mac OS X，您可以從 [Mono Project](http://www.mono-project.com/) 下載 .NET Framework。

### SQL Database

請參閱[快速入門頁面](sql-database-get-started.md)，以了解如何建立範例資料庫。請務必遵循該指南以建立 **AdventureWorks 資料庫範本**。以下所示的範例僅適用於 **AdventureWorks 結構描述**。

## 步驟 1：取得連接字串

[AZURE.INCLUDE [sql-database-include-connection-string-dotnet-20-portalshots](../../includes/sql-database-include-connection-string-dotnet-20-portalshots.md)]

## 步驟 2：連接

[System.Data.SqlClient.SqlConnection 類別](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx)可用來連接到 SQL Database。


```
using System.Data.SqlClient;

class Sample
{
  static void Main()
  {
	  using(var conn = new SqlConnection("Server=tcp:yourserver.database.windows.net,1433;Database=yourdatabase;User ID=yourlogin@yourserver;Password={yourpassword};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"))
	  {
		  conn.Open();
	  }
  }
}
```

## 步驟 3：執行查詢

[System.Data.SqlClient.SqlCommand](https://msdn.microsoft.com/library/system.data.sqlclient.sqlcommand.aspx) 和 [SqlDataReader](https://msdn.microsoft.com/library/system.data.sqlclient.sqldatareader.aspx) 類別可用來擷取對 SQL Database 查詢的結果集。請注意，System.Data.SqlClient 也支援將資料擷取到離線 [System.Data.DataSet](https://msdn.microsoft.com/library/system.data.dataset.aspx) 中。

```
using System;
using System.Data.SqlClient;

class Sample
{
	static void Main()
	{
	  using(var conn = new SqlConnection("Server=tcp:yourserver.database.windows.net,1433;Database=yourdatabase;User ID=yourlogin@yourserver;Password={yourpassword};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"))
		{
			var cmd = conn.CreateCommand();
			cmd.CommandText = @"
					SELECT
						c.CustomerID
						,c.CompanyName
						,COUNT(soh.SalesOrderID) AS OrderCount
					FROM SalesLT.Customer AS c
					LEFT OUTER JOIN SalesLT.SalesOrderHeader AS soh ON c.CustomerID = soh.CustomerID
					GROUP BY c.CustomerID, c.CompanyName
					ORDER BY OrderCount DESC;";

			conn.Open();

			using(var reader = cmd.ExecuteReader())
			{
				while(reader.Read())
				{
					Console.WriteLine("ID: {0} Name: {1} Order Count: {2}", reader.GetInt32(0), reader.GetString(1), reader.GetInt32(2));
				}
			}					
		}
	}
}

```  

## 步驟 4：插入資料列

在這個範例中，您將了解如何安全地執行 [INSERT](https://msdn.microsoft.com/library/ms174335.aspx) 陳述式、傳遞透過 [SQL 插入](https://technet.microsoft.com/library/ms161953(v=sql.105).aspx) 弱點保護您應用程式的參數，以及擷取自動產生的[主索引鍵](https://msdn.microsoft.com/library/ms179610.aspx)值。

```
using System;
using System.Data.SqlClient;

class Sample
{
    static void Main()
    {
		using(var conn = new SqlConnection("Server=tcp:yourserver.database.windows.net,1433;Database=yourdatabase;User ID=yourlogin@yourserver;Password={yourpassword};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"))
        {
            var cmd = conn.CreateCommand();
            cmd.CommandText = @"
                INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate)
                OUTPUT INSERTED.ProductID
                VALUES (@Name, @Number, @Cost, @Price, CURRENT_TIMESTAMP)";

            cmd.Parameters.AddWithValue("@Name", "SQL Server Express");
            cmd.Parameters.AddWithValue("@Number", "SQLEXPRESS1");
            cmd.Parameters.AddWithValue("@Cost", 0);
            cmd.Parameters.AddWithValue("@Price", 0);

            conn.Open();

            int insertedProductId = (int)cmd.ExecuteScalar();

            Console.WriteLine("Product ID {0} inserted.", insertedProductId);
        }
    }
}
```

<!---HONumber=AcomDC_1210_2015-->