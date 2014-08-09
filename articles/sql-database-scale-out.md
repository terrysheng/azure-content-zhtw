<properties linkid="manage-services-how-to-scale-a-sqldb" urlDisplayName="How to scale" pageTitle="How to scale a SQL Database - Azure" metaKeywords="" description="Learn about options for scaling your SQL Database in Azure." metaCanonical="" services="sql-database" documentationCenter="" title="How to Scale a SQL Database Solution" authors="" solutions="" manager="" editor="" />

如何調整 SQL Database 方案
==========================

在 Azure 上，資料庫延展性和向外延展是同義詞，意指將工作負載重新分配在資料中心內的多部商業伺服器上。向外延伸是解決資料容量或效能的策略。資料成長幅度高的超大型資料庫終究會需要向外延伸策略，不論存取的使用者人數多寡，皆是如此。

同盟是在 Azure 上達成向外延伸目標的最佳方法。SQL Database 同盟是以水平分區化為基礎，意指按照資料列來分割一或多個資料表，然後再分配到多個同盟成員中。

同盟不是所有延伸性問題的解答。有時候，資料特性或應用程式需求會指向更簡單的方法。以下清單是按照複雜性排列的潛在方案。

增加資料庫的大小
----------------

建立的資料庫有其固定大小，其受限於每個版本施行的上限。對於 Web Edition，您可以將資料庫增加為 5 GB 的上限。對於 Business Edition，資料庫大小上限為 150 GB。增加資料容量最明顯的方法是變更版本和大小上限：

     ALTER DATABASE school MODIFY (EDITION = 'Business', MAXSIZE=10GB);

使用多個資料庫及分配使用者
--------------------------

在有限的情況下，您可以建立資料庫複本，再將登入和使用者分配到每個資料庫。在考慮同盟選項之前，這是重新分配工作負載常用的方法。這個方法適用於短期使用、後續會併入內部部署之主要資料庫的資料庫，以及適用於提供唯讀資料的方案。

使用同盟
--------

SQL Database 中的同盟可用來實現成效較高的延伸性和效能。其意指按照資料列來分割一或多個資料表，然後再分配到多部資料庫 (同盟成員) 中。這種類型的水平分割通常稱為「分區化」。凡是需要實現延伸性、效能或需要管理容量的情況都可以說是適用於這個方法的主要案例。

Business Edition 支援同盟。如需詳細資訊，請參閱 [SQL 資料庫中的同盟](http://msdn.microsoft.com/zh-tw/library/windowsazure/hh597452.aspx) (英文) 和 [SQL Database 同盟教學課程 - DBA](http://msdn.microsoft.com/zh-tw/library/windowsazure/hh778416.aspx) (英文)。

考慮其他形式的儲存體
--------------------

請記住，Azure 支援多種形式的資料儲存體，包括資料表儲存體和 Blob 儲存體。如果您不需要關聯性功能，資料表或 Blob 儲存體是比較經濟實惠的選擇。

