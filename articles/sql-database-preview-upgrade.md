<properties 
	pageTitle="升級至最新的 SQL Database Update V12 (預覽)" 
	description="升級至最新的 SQL Database Update V12 (預覽)" 
	services="sql-database" 
	documentationCenter="" 
	authors="sonalmm" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-management" 
	ms.date="12/11/2014" 
	ms.author="sonalm"/>



# 升級至最新的 SQL Database Update V12 (預覽)


[註冊](https://portal.azure.com) 最新的 SQL Database Update V12，以在 Microsoft Azure 上充分利用新一代的   SQL Database。首先，您需要 Microsoft Azure 訂用帳戶。註冊 [Azure 免費試用](http://azure.microsoft.com/pricing/free-trial) 並檢閱 [定價](http://azure.microsoft.com/pricing/details/sql-database) 資訊。 

## 如何使用最新的 SQL Database Update 就地升級伺服器 ##

| 升級  | 螢幕擷取畫面 |
| :--- | :--- |
| 1.登入 [http://portal.azure.com/](http://portal.azure.com/)。 | ![New Azure Portal][1] |
| 2.按一下 [瀏覽]****。 | ![Browse Services][2] |
| 3.	按一下 [SQL Server]****。隨即顯示 SQL Server 名稱清單。 | ![Select SQL Server service][3] |
| 4.選取您想複製到啟用 SQL Database Update 之新伺服器的伺服器。 | ![Shows a list of SQL Servers][4] |
| 5.按一下 [**最新的 SQL Database Update V12**]。 | ![Latest preview feature][5] |
| 6.按一下 [升級此伺服器]****。 | ![Upgrades the SQL Server to the preview][6] |

> [AZURE.NOTE] **重要事項**：一旦您選取升級選項，您的伺服器和該伺服器內的資料庫將會啟用 SQL Database Update V12 功能，並且無法回復。若要將伺服器升級至 SQL Database Update V12，您需要「基本」、「標準」或「高階」服務層。如需服務層的詳細資訊，請參閱 [將 SQL Database Web/Business 資料庫升級至新的服務層"](http://azure.microsoft.com/documentation/articles/sql-database-upgrade-new-service-tiers/)。

> **重要事項**：SQL Database Update V12 (預覽) 不支援異地複寫。如需詳細資訊，請參閱 [規劃和準備升級至 Azure SQL Database V12 預覽](http://azure.microsoft.com/documentation/articles/sql-database-preview-plan-prepare-upgrade)。


按一下 [升級此伺服器]**** 選項後，會開啟顯示有關驗證程序之訊息的分頁。 

- 驗證程序會檢查您資料庫的服務層，並檢查是否已啟用異地複寫。驗證完成後，分頁會顯示結果。 
- 驗證程序完成後，您會看到一份需要您採取動作以符合升級至 SQL Database Update V12 之需求的資料庫名稱清單。**您必須個別為那些資料庫完成動作，才能升級至 SQL Database Update V12**。
- 當您按一下每個資料庫名稱時，新的分頁會根據您目前的使用量提供服務定價層建議。您也可以瀏覽各種定價層，選取最符合您環境的選項。所有已設定異地複寫的資料庫都必須重新設定才能停止複寫。 
- 請注意，若找不到足夠資料，將不會顯示定價層建議。 

| 動作 | 螢幕擷取畫面 |
| :--- | :--- |
| 7.在您完成伺服器升級的準備動作後，請輸入要升級的伺服器名稱，然後按一下 [確定]****。 | ![Confirm the server name to upgrade][7] |
| 8.升級程序即會啟動。升級最久可能需要 24 小時。此伺服器上的所有資料庫在這段期間將維持線上狀態，但伺服器和資料庫管理動作將受到限制。一旦程序完成，伺服器分頁上會顯示 [已啟用]**** 狀態。 | ![Confirms preview features are enabled][8] |
 

# 相關連結  #

-  [SQL Database Update V12 新功能 (預覽)](http://azure.microsoft.com/documentation/articles/sql-database-preview-whats-new/)
- [規劃和準備升級至最新的 SQL Database Update V12 (預覽)](http://azure.microsoft.com/documentation/articles/sql-database-preview-plan-prepare-upgrade/)


<!--Image references-->
[1]: ./media/sql-database-preview-upgrade/firstscreenportal.png
[2]: ./media/sql-database-preview-upgrade/browse.png
[3]: ./media/sql-database-preview-upgrade/sqlserver.png
[4]: ./media/sql-database-preview-upgrade/sqlserverlist.png
[5]: ./media/sql-database-preview-upgrade/latestprview.png
[6]: ./media/sql-database-preview-upgrade/upgrade.png
[7]: ./media/sql-database-preview-upgrade/typeservername.png
[8]: ./media/sql-database-preview-upgrade/enabled.png
<!--HONumber=47-->
