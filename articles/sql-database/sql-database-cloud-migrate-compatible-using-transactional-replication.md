<properties
   pageTitle="使用異動複寫移轉到 SQL Database"
   description="Microsoft Azure SQL Database, 資料庫移轉, 匯入資料庫, 異動複寫"
   services="sql-database"
   documentationCenter=""
   authors="carlrabeler"
   manager="jeffreyg"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="12/17/2015"
   ms.author="carlrab"/>

# 使用異動複寫將 SQL Server 資料庫移轉到 SQL Database

當您在移轉發生時無法負擔從實際執行中移除 SQL Server 資料庫，可以使用 SQL Server 交易複寫做為移轉解決方案。使用交易複寫，開始移轉和完成移轉之間發生的資料或結構描述的所有變更都會出現在您的 Azure SQL Database。移轉完成之後，您只需要變更您的應用程式的連接字串，將其指向您的 Azure SQL Database，而不是指向您的內部部署資料庫。一旦交易複寫清空留在內部部署資料庫的任何變更，且所有應用程式指向 Azure DB，您現在可以安全地解除安裝複寫，讓您的 Azure SQL Database 做為實際執行系統。

 ![SeedCloudTR 圖表](./media/sql-database-cloud-migrate/SeedCloudTR.png)


交易複寫是內建技術，與 SQL Server 6.5 之後的 SQL Server 整合。它是大部分 DBA 知道他們會體驗到的非常成熟且經過實證的技術。現在，您只要使用 [SQL Server 2016 預覽](http://www.microsoft.com/server-cloud/products/sql-server-2016/)，即可將 Azure SQL Database 設為內部部署發佈的[交易複寫訂閱者](https://msdn.microsoft.com/library/mt589530.aspx)。您從 Management Studio 設定它的經驗，與在內部部署伺服器上設定交易複寫訂閱者完全相同。當發行者和散發者至少其中之一為下列 SQL Server 版本時，即支援對此案例的支援：

 - SQL Server 2016 CTP3 (預覽版) 和更新版本 
 - SQL Server 2014 SP1 CU3 和更新版本
 - SQL Server 2014 RTM CU10 和更新版本
 - SQL Server 2012 SP2 CU8 和更新版本
 - SQL Server 2013 SP3 (當它發行時)

您也可以使用交易複寫以移轉內部部署資料庫的子集。您複寫至 Azure SQL Database 的發佈可以限制為複寫的資料庫中資料表的子集。此外，對於複寫的每一個資料表，您可以將資料限制為資料列的子集和 (或) 資料行的子集。

<!---HONumber=AcomDC_1223_2015-->