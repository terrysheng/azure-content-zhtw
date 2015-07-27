<properties 
	pageTitle="使用 Ruby 和 Ubuntu 上的 TinyTDS 連接到 SQL Database" 
	description="提供 Ruby 您可在 Ubuntu Linux 上執行做為用戶端的程式碼範例，已連接至 Azure SQL Database。"
	services="sql-database" 
	documentationCenter="" 
	authors="ajlam" 
	manager="jeffreyg" 
	editor=""/>


<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="ruby" 
	ms.topic="article" 
	ms.date="07/13/2015" 
	ms.author="andrela"/>


# 在 Ubuntu Linux 上使用 Python 連接到 SQL Database

[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]

本主題提供可在 Ubuntu Linux 用戶端電腦上執行，以連接到 Azure SQL Database 資料庫的 Ruby 程式碼範例。

## 安裝必要的模組

開啟您的終端機，如果您的電腦上沒有 FreeTDS，請加以安裝。
	
    sudo apt-get --assume-yes update 
    sudo apt-get --assume-yes install freetds-dev freetds-bin


如果您的電腦上還沒有 Ruby，請在利用 FreeTDS 設定您的電腦之後安裝 Ruby。
    
    sudo apt-get install libgdbm-dev libncurses5-dev automake libtool bison libffi-dev 
    curl -L https://get.rvm.io | bash -s stable

如果您有簽章的任何問題，請執行下列命令。

    command curl -sSL https://rvm.io/mpapis.asc | gph --import - 

如果沒有簽章的任何問題，請執行下列命令。

    source ~/.rvm/scripts/rvm 
    rvm install 2.1.2 
    rvm use 2.1.2 --default 
    ruby -v 

請確定您執行的是 2.1.2 版或 Ruby VM。

接著，請安裝 TinyTDS。

    gem install tiny_tds

## 建立資料庫，擷取您的連接字串

Ruby 範例依賴 AdventureWorks 範例資料庫。如果您還沒有 AdventureWorks，您就可以在下列主題了解如何建立：[建立您的第一個 Azure SQL Database](sql-database-get-started.md)

本主題也說明如何擷取資料庫連接字串。

## 連接到您的 SQL Database

[TinyTDS::Client](https://github.com/rails-sqlserver/tiny_tds) 函數可用來連接到 SQL Database。

    require 'tiny_tds' 
    client = TinyTds::Client.new username: 'yourusername@yourserver', password: 'yourpassword', host: 'yourserver.database.windows.net', port: 1433, database: 'AdventureWorks', azure:true 

## 執行 SELECT 陳述式並擷取結果集

[TinyTds::Result](https://github.com/rails-sqlserver/tiny_tds) 函數可用來從針對 SQL Database 的查詢擷取結果集。此函數會接受查詢，並傳回結果集。結果集可使用 [result.each do |row|](https://github.com/rails-sqlserver/tiny_tds) 重複列舉。

    require 'tiny_tds'  
    print 'test'     
    client = TinyTds::Client.new username: 'yourusername@yourserver', password: 'yourpassword', host: 'yourserver.database.windows.net', port: 1433, database: 'AdventureWorks', azure:true 
    results = client.execute("select * from SalesLT.Product") 
    results.each do |row| 
    puts row 
    end 

## 插入資料列、傳遞參數及擷取產生的主索引鍵值

程式碼範例：

- 將參數傳遞給要插入資料列的值。
- 插入資料列。
- 擷取為主要索引鍵產生的值。

在 SQL Database 中，[IDENTITY](http://msdn.microsoft.com/library/ms186775.aspx) 屬性和 [SEQUENECE](http://msdn.microsoft.com/library/ff878058.aspx) 物件可用來自動產生[主索引鍵值](http://msdn.microsoft.com/library/ms179610.aspx)。

若要搭配使用 TinyTDS 和 Azure，建議您執行多個 `SET` 陳述式來變更目前工作階段處理特定資訊的方式。建議使用程式碼範例中所提供的 `SET` 陳述式。例如，即使未明確陳述資料行的可為 null 狀態，`SET ANSI_NULL_DFLT_ON` 也可讓新建立的資料行允許 null 值。

若要配合 Microsoft SQL Server [datetime](http://msdn.microsoft.com/library/ms187819.aspx) 格式，請使用 [strftime](http://ruby-doc.org/core-2.2.0/Time.html#method-i-strftime) 函數轉換成相對應的日期時間格式。

    require 'tiny_tds' 
    require 'date'
    t = Time.now
    curr_date = t.strftime("%Y-%m-%d %H:%M:%S.%L") 
    client = TinyTds::Client.new username: 'yourusername@yourserver', password: 'yourpassword', host: 'yourserver.database.windows.net', port: 1433, database: 'AdventureWorks', azure:true 
    results = client.execute("SET ANSI_NULLS ON")
    results = client.execute("SET CURSOR_CLOSE_ON_COMMIT OFF")
    results = client.execute("SET ANSI_NULL_DFLT_ON ON")
    results = client.execute("SET IMPLICIT_TRANSACTIONS OFF")
    results = client.execute("SET ANSI_PADDING ON")
    results = client.execute("SET QUOTED_IDENTIFIER ON")
    results = client.execute("SET ANSI_WARNINGS ON")
    results = client.execute("SET CONCAT_NULL_YIELDS_NULL ON")
    results = client.execute("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('SQL Server Express New', 'SQLEXPRESS New', 0, 0, '#{curr_date}' )")
    results.each do |row| 
    puts row
    end 

<!---HONumber=July15_HO3-->