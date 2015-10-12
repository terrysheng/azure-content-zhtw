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
	ms.date="08/04/2015" 
	ms.author="mebha"/>


# 在 Windows 上使用 Ruby 連接到 SQL Database

[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]

本主題提供可在執行 Windows 8.1 的 Windows 電腦上執行，以連接到 Azure SQL Database 資料庫的 Ruby 程式碼範例。

## 安裝必要的模組

開啟您的終端機，並安裝下列：

**1) Ruby：**如果您的電腦沒有 Ruby，請安裝。若是新的 Ruby 使用者，我們建議您使用 Ruby 2.1.X 安裝程式。這些安裝程式提供相容且更新的穩定語言和廣泛封裝清單 (gem)。[移至 Ruby 下載頁面](http://rubyinstaller.org/downloads/)並且下載適當的 2.1.x 安裝程式。舉例來說，如果您是使用 64 位元電腦，請下載 **Ruby 2.1.6 (x64)** 安裝程式。<br/><br/>一旦下載安裝程式之後，請執行下列動作：


- 按兩下檔案以啟動安裝程式。

- 選取您的語言，並同意接受這些條款。

- 在安裝設定畫面上，選取 [將 Ruby 可執行檔新增至您的路徑] 和 [讓 .rb 和 .rbw 檔案與此 Ruby 安裝產生關聯] 旁的核取方塊。


**2) DevKit：**從 [RubyInstaller 頁面](http://rubyinstaller.org/downloads/)下載 DevKit

下載完成之後，執行下列作業：


- 按兩下檔案。系統會詢問您解壓縮檔案的位置。

- 按一下 "..." 按鈕，然後選取 "C:\\DevKit"。您可能必須藉由按一下 [新增資料夾]，先建立此資料夾。

- 按一下 [確定]，然後按一下 [解壓縮]，以解壓縮檔案。


現在開啟命令列提示字元並輸入下列命令：

	> chdir C:\DevKit
	> ruby dk.rb init
	> ruby dk.rb install

您現在有功能完整的 Ruby 和 RubyGems！


**3) TinyTDS：**瀏覽至 C:\\DevKit 並且從您的終端機執行下列命令：這會在您的電腦上安裝 TinyTDS。

	gem inst tiny_tds --pre

## 建立資料庫，擷取您的連接字串

Ruby 範例依賴 `AdventureWorks` 範例資料庫。如果您還沒有 `AdventureWorks`，您可以造訪[建立您的第一個 Azure SQL Database](sql-database-get-started.md)，以了解如何建立。

本主題也說明如何擷取資料庫連接字串。

## 連接到您的 SQL Database

[TinyTDS::Client](https://github.com/rails-sqlserver/tiny_tds) 函數可用來連接到 SQL Database。

    require 'tiny_tds' 
    client = TinyTds::Client.new username: 'yourusername@yourserver', password: 'yourpassword', 
    host: 'yourserver.database.windows.net', port: 1433, 
    database: 'AdventureWorks', azure:true 

## 執行 SELECT 陳述式並擷取結果集

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

## 插入資料列、傳遞參數及擷取產生的主索引鍵值

程式碼範例：

- 將參數傳遞給要插入資料列的值。
- 插入資料列。
- 擷取為主要索引鍵產生的值。

在 SQL Database 中，[IDENTITY](http://msdn.microsoft.com/library/ms186775.aspx) 屬性和 [SEQUENECE](http://msdn.microsoft.com/library/ff878058.aspx) 物件可用來自動產生[主索引鍵](http://msdn.microsoft.com/library/ms179610.aspx)值。

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

<!---HONumber=Oct15_HO1-->