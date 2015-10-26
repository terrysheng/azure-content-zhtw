<properties 
	pageTitle="SQL Database 1433 以外的連接埠 | Microsoft Azure"
	description="從 ADO.NET 至 Azure SQL Database V12 的用戶端連線有時會略過 Proxy 並直接與資料庫互動。1433 以外的連接埠變得重要。"
	services="sql-database"
	documentationCenter=""
	authors="MightyPen"
	manager="jeffreyg"
	editor="" />


<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/15/2015" 
	ms.author="genemi"/>


# 針對 ADO.NET 4.5 及 SQL Database V12 的 1433 以外的連接埠


本主題說明 Azure SQL Database V12 對於使用 ADO.NET 4.5 或更新版本的用戶端的連接行為所帶來的變更。


## SQL Database V11：連接埠 1433


當用戶端程式使用 ADO.NET 4.5 來連接並查詢 SQL Database V11 時，內部順序如下：


1. ADO.NET 嘗試連線到 SQL Database。

2. ADO.NET 使用連接埠 1433 來呼叫中介軟體模組，中介軟體會連線至 SQL Database。

3. SQL Database 會將其回應傳回給中介軟體，中介軟體將回應轉送給 ADO.NET 連接埠 1433。


**術語：**我們使用 *proxy 路由*來說明 ADO.NET 與 SQL Database 互動的上述順序。如果沒有牽涉到中介軟體，我們會說使用的是*直接路由*。


## SQL Database V12：內部與外部


對於連線到 V12，我們必須詢問您的用戶端程式是在 Azure 雲端界限*外部*或*內部*執行。這些小節將討論兩種常見案例。


#### *外部：*在桌上型電腦上執行的用戶端


連接埠 1433 是裝載您的 SQL Database 用戶端應用程式的桌上型電腦上唯一必須開啟的連接埠。


#### *內部：*在 Azure 上執行的用戶端


當您的用戶端是在 Azure 雲端界限內部執行時，它會使用我們可以稱為*直接路由*的路由與 SQL Database 伺服器互動。建立連線之後，用戶端和資料庫之間的進一步互動未牽涉到任何中介軟體 proxy。


順序如下：


1. ADO.NET 4.5 (或更新版本) 會起始與 Azure 雲端的簡短互動，並且接收動態已識別的連接埠號碼。
 - 動態識別的連接埠號碼範圍為 11000-11999 或 14000-14999。

2. 然後 ADO.NET 會直接連線到 SQL Database 伺服器，中間沒有中介軟體。

3. 查詢會直接傳送到資料庫，結果會直接傳回至用戶端。


請確定 Azure 用戶端電腦上 11000-11999 及 14000-14999 的連接埠範圍已保留可供 ADO.NET 4.5 用戶端與 SQL Database V12 互動。

- 特別是範圍中的連接埠必須沒有其他任何輸出封鎖器。

- 在您的 Azure VM 上，**具有進階安全性的 Windows 防火牆**會控制此連接埠設定。
 - 您可以使用[防火牆的使用者介面](http://msdn.microsoft.com/library/cc646023.aspx)來新增規則，其中您可使用如 **11000-11999** 的語法指定 **TCP** 通訊協定和連接埠範圍。


## 版本說明


本章節將釐清參考產品版本的 Moniker。它也會列出產品之間的一些版本配對。


#### ADO.NET


- ADO.NET 4.0 支援 TDS 7.3 通訊協定，但不支援 7.4。
- ADO.NET 4.5 和更新版本支援 TDS 7.4 通訊協定。


#### SQL Database V11 和 V12


SQL Database V11 和 V12 之間的用戶端連線差異是本主題中的重點。


*附註：*Transact-SQL 陳述式 `SELECT @@version;` 會傳回值，從例如 '11.' 或 '12.' 的數字開頭，以及符合我們的 SQL Database V11 和 V12 版本名稱。


## 相關連結


- ADO.NET 4.6 於 2015 年 7 月 20 日發行。您可以在[這裡](http://blogs.msdn.com/b/dotnet/archive/2015/07/20/announcing-net-framework-4-6.aspx)查看 .NET 小組的部落格公告。


- ADO.NET 4.5 於 2012 年 8 月 15 日發行。您可以在[這裡](http://blogs.msdn.com/b/dotnet/archive/2012/08/15/announcing-the-release-of-net-framework-4-5-rtm-product-and-source-code.aspx)查看 .NET 小組的部落格公告。
 - 您可以在[這裡](http://blogs.msdn.com/b/dotnet/archive/2013/06/26/announcing-the-net-framework-4-5-1-preview.aspx)查看有關 ADO.NET 4.5.1 的部落格文章。


- [TDS 通訊協定版本清單](http://www.freetds.org/userguide/tdshistory.htm)


- [連接到 SQL Database：連結、最佳作法和設計方針](sql-database-connect-central-recommendations.md)


- [Azure SQL Database 防火牆](sql-database-firewall-configure.md)


- [如何：在 SQL Database 上進行防火牆設定](sql-database-configure-firewall-settings.md)

<!---HONumber=Oct15_HO3-->