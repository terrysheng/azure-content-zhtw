<properties
	pageTitle="在 Windows 上搭配 JDBC 使用 Java 連接到 SQL Database"
	description="提供可用來連接到 Azure SQL Database 的 Java 程式碼範例。這個範例使用在 Windows 用戶端電腦上執行的 JDBC。"
	services="sql-database"
	documentationCenter=""
	authors="LuisBosquez"
	manager="jeffreyg"
	editor="genemi"/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="java"
	ms.topic="article"
	ms.date="12/17/2015"
	ms.author="lbosq"/>


# 在 Windows 上搭配 JDBC 使用 Java 連接到 SQL Database


> [AZURE.SELECTOR]
- [C#](sql-database-develop-dotnet-simple.md)
- [PHP](sql-database-develop-php-simple-windows.md)
- [Python](sql-database-develop-python-simple-windows.md)
- [Ruby](sql-database-develop-ruby-simple-windows.md)
- [Java](sql-database-develop-java-simple-windows.md)
- [Node.js](sql-database-develop-nodejs-simple-windows.md)


本主題提供可用來連接到 Azure SQL Database 的 Java 程式碼範例。Java 範例需要 Java Development Kit (JDK) 1.8 版。這個範例使用 JDBC 驅動程式連接到 Azure SQL Database。


## 必要條件

### 驅動程式和程式庫

- [適用於 SQL Server 的 Microsoft JDBC 驅動程式 - SQL JDBC 4](http://www.microsoft.com/download/details.aspx?displaylang=en&id=11774)。
- 執行 [Java Development Kit 1.8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) 的任何作業系統平台。

### SQL Database

請參閱[快速入門頁面](sql-database-get-started.md)，以了解如何建立資料庫。

### SQL 資料表

本主題中的 Java 程式碼範例假設您的 Azure SQL Database 資料庫中已經有下列測試資料表。

<!--
Could this instead be a #tempPerson table, so that the Java code sample could be fully self-sufficient and be runnable (with automatic cleanup)?
-->


	CREATE TABLE Person
	(
		id         INT    PRIMARY KEY    IDENTITY(1,1),
		firstName  VARCHAR(32),
		lastName   VARCHAR(32),
		age        INT
	);


## 步驟 1：取得連接字串

[AZURE.INCLUDE [sql-database-include-connection-string-jdbc-20-portalshots](../../includes/sql-database-include-connection-string-jdbc-20-portalshots.md)]

> [AZURE.NOTE]如果您使用 JTDS JDBC 驅動程式，則您必須在連接字串的 URL 加入 "ssl=require"，然後您需要設定 JVM 的下列選項 "-Djsse.enableCBCProtection=false"。此 JVM 選項會停用安全性漏洞修正程式，因此請確定您了解會涉及到哪些風險，才能設定此選項。


## 步驟 2：編譯 Java 程式碼範例


本節包含大量的 Java 程式碼範例。其中的註解指出您將複製並貼上後續章節所提供之一小部分 Java 程式碼片段的位置。即使未複製並貼到註解附近，本節中的範例仍可編譯及執行，但該範例只會連接，然後就結束。您會找到下列註解：


1. `// INSERT two rows into the table.`
2. `// TRANSACTION and commit for an UPDATE.`
3. `// SELECT rows from the table.`


接下來還有大量的 Java 程式碼範例。這個範例包含 `SQLDatabaseTest` 類別的 `main` 函式。


	import java.sql.*;
	import com.microsoft.sqlserver.jdbc.*;

	public class SQLDatabaseTest {

		public static void main(String[] args) {
			String connectionString =
				"jdbc:sqlserver://your_server.database.windows.net:1433;"
				+ "database=your_database;"
				+ "user=your_user@your_server;"
				+ "password=your_password;"
				+ "encrypt=true;"
				+ "trustServerCertificate=false;"
				+ "hostNameInCertificate=*.database.windows.net;"
				+ "loginTimeout=30;";

			// Declare the JDBC objects.
			Connection connection = null;
			Statement statement = null;
			ResultSet resultSet = null;
			PreparedStatement prepsInsertPerson = null;
			PreparedStatement prepsUpdateAge = null;

			try {
				connection = DriverManager.getConnection(connectionString);

				// INSERT two rows into the table.
				// ...

				// TRANSACTION and commit for an UPDATE.
				// ...

				// SELECT rows from the table.
				// ...
			}
			catch (Exception e) {
				e.printStackTrace();
			}
			finally {
				// Close the connections after the data has been handled.
				if (prepsInsertPerson != null) try { prepsInsertPerson.close(); } catch(Exception e) {}
				if (prepsUpdateAge != null) try { prepsUpdateAge.close(); } catch(Exception e) {}
				if (resultSet != null) try { resultSet.close(); } catch(Exception e) {}
				if (statement != null) try { statement.close(); } catch(Exception e) {}
				if (connection != null) try { connection.close(); } catch(Exception e) {}
			}
		}
	}


當然，若要實際執行上述 Java 程式碼範例，您必須將實際值放入連接字串以取代下列預留位置：


- your\_server
- your\_database
- your\_user
- your\_password


## 步驟 3：插入資料列


這個 Java 程式碼片段會發出 Transact-SQL INSERT 陳述式，以將兩個資料列插入 Person 資料表。一般順序如下：


1. 使用 `Connection` 物件產生 `PreparedStatement` 物件。
 - 我們包含參數 `Statement.RETURN_GENERATED_KEYS`，以便稍後可以取得為 **id** 索引鍵值自動產生的值。
2. 在 `PreparedStatement` 物件上呼叫 `execute` 方法。
3. 使用 `PreparedStatement` 物件取得為主索引鍵自動產生的數值。
 - 這與 Person 資料表中的 **id** 資料行上的 AUTO\_INCREMENT 規格相關。


將這個簡短的 Java 程式碼片段，複製並貼到主要程式碼範例中出現註解 `// INSERT two rows into the table.` 的位置。


	// Create and execute an INSERT SQL prepared statement.
	String insertSql = "INSERT INTO Person (firstName, lastName, age) VALUES "
		+ "('Bill', 'Gates', 59), "
		+ "('Steve', 'Ballmer', 59);";

	prepsInsertPerson = connection.prepareStatement(
		insertSql,
		Statement.RETURN_GENERATED_KEYS);
	prepsInsertPerson.execute();
	// Retrieve the generated key from the insert.
	resultSet = prepsInsertPerson.getGeneratedKeys();
	// Iterate through the set of generated keys.
	while (resultSet.next()) {
		System.out.println("Generated: " + resultSet.getString(1));
	}


## 步驟 4：認可交易

下列 Java 程式碼片段會發出 Transact-SQL UPDATE 陳述式，以提高 Person 資料表中每一個資料列的 `age` 值。一般順序如下：


1. 呼叫 `setAutoCommit` 方法，以避免自動認可資料庫中的更新。
2. 呼叫 `executeUpdate` 方法，以在交易內容中執行更新。
3. 呼叫 `commit` 方法，以明確地認可交易。


將這個簡短的 Java 程式碼片段，複製並貼到主要程式碼範例中出現註解 `// TRANSACTION and commit for an UPDATE.` 的位置。


	// Set AutoCommit value to false to execute a single transaction at a time.
	connection.setAutoCommit(false);

	// Write the SQL Update instruction and get the PreparedStatement object.
	String transactionSql = "UPDATE Person SET Person.age = Person.age + 1;";
	prepsUpdateAge = connection.prepareStatement(transactionSql);

	// Execute the statement.
	prepsUpdateAge.executeUpdate();

	//Commit the transaction.
	connection.commit();

	// Return the AutoCommit value to true.
	connection.setAutoCommit(true);


## 步驟 4：執行查詢


這個 Java 程式碼片段會執行 Transact-SQL SELECT 陳述式，以查看 Person 資料表中所有已更新的資料列。一般順序如下：


1. 使用 `Connection` 物件產生 `Statement` 物件。
2. 使用 `Statement` 物件產生 `ResultSet` 物件。
3. 重複呼叫 `resultSet.next`，以逐一查看所有傳回的資料列。


將這個簡短的 Java 程式碼片段，複製並貼到主要程式碼範例中出現註解 `// SELECT rows from a table.` 的位置。


	// Create and execute a SELECT SQL statement.
	String selectSql = "SELECT firstName, lastName, age FROM dbo.Person";
	statement = connection.createStatement();
	resultSet = statement.executeQuery(selectSql);

	// Iterate through the result set and print the attributes.
	while (resultSet.next()) {
		System.out.println(resultSet.getString(2) + " "
			+ resultSet.getString(3));
	}

## 後續步驟

如需詳細資訊，請參閱 [Java 開發人員中心](/develop/java/)。

<!---HONumber=AcomDC_1223_2015-->