<properties 
	pageTitle="升級至 SQL Database V12" 
	description="說明如何從較早版本的 Azure SQL Database 升級為 Azure SQL Database V12。" 
	services="sql-database" 
	documentationCenter="" 
	authors="stevestein" 
	manager="jeffreyg"
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-management" 
	ms.date="10/08/2015" 
	ms.author="sstein"/>


# 升級至 SQL Database V12


> [AZURE.SELECTOR]
- [Azure Preview Portal](sql-database-v12-upgrade.md)
- [PowerShell](sql-database-upgrade-server.md)


SQL Database V12 是 SQL Database 的最新版本，具有[舊版 V2 所欠缺的許多優點](sql-database-v12-whats-new.md)。本文說明如何使用 Azure 預覽入口網站，將 V2 伺服器升級為 V12 版本。

在升級至 SQL Database V12 的過程中，您也必須[將所有 Web 和 Business 資料庫一併更新至新的服務層](sql-database-upgrade-new-service-tiers.md)。下列指示包括更新 Web 和 Business 資料庫的步驟，並根據您資料庫的使用歷史記錄，為您提供定價層建議。



1. 在 [Azure 預覽入口網站](http://portal.azure.com/)中，瀏覽至您想要升級的伺服器，方法是選取 [瀏覽全部] > [SQL Server]，並選取所需的伺服器。
2. 選取 [最新的 SQL Database 更新]，然後選取 [升級此伺服器]。

      ![升級伺服器][1]

## 升級 Web 和 Business 資料庫

2. 升級所有 Web 和 Business 資料庫。如果您的伺服器內有任何 Web 或 Business 資料庫，就必須將這些資料庫升級。為了協助您完成升級，SQL Database 服務建議為每個資料庫選擇適當的服務層和效能層級 (定價層)。SQL Database 服務會透過分析每個資料庫的使用歷史記錄，建議最適合用於執行您現有資料庫工作負載的層。 
    
    逐一選取各資料庫，檢閱建議升級的定價層並加以選取。您隨時都可以瀏覽各種可用的定價層，並選取最符合您環境的選項。

     ![資料庫][2]



7. 按一下建議的層之後，您會看到 [選擇定價層] 刀鋒視窗，您可以在此按一下任一層，然後按一下 [選取] 按鈕變更為該層。為每個 Web 或 Business 資料庫選取新的層

    ![Mahout][6]


伺服器的所有資料庫都符合資格之後，您就可以準備開始升級

## 開始升級

3. 伺服器的所有資料庫都符合升級條件後，您必須**輸入伺服器名稱**，以確認您要執行升級，然後按一下 [確定]。 

    ![確認升級][3]


4. 升級隨即開始，過程中也會顯示進度通知。升級程序即會啟動。視特定資料庫的詳細資料而定，升級至 V12 可能需要一些時間。在這段期間，伺服器上的所有資料庫將維持線上狀態，但伺服器和資料庫的管理動作將受到限制。

    ![升級進行中][4]

    在實際轉換到新的效能層級時，資料庫連線可能會暫時中斷一小段時間 (通常以秒計算)。若應用程式對於連線終止出現暫時性的錯誤處理 (重試邏輯)，在升級結束時連線將不會中斷。

5. 升級作業完成後，會隨即啟用 SQL Database V12 伺服器功能。

    ![已啟用 V12][5]


## 相關連結

- [SQL Database V12 新功能](sql-database-v12-whats-new.md)
- [規劃和準備升級至 SQL Database V12](sql-database-v12-plan-prepare-upgrade.md)


<!--Image references-->
[1]: ./media/sql-database-v12-upgrade/latest-sql-database-update.png
[2]: ./media/sql-database-v12-upgrade/upgrade-server2.png
[3]: ./media/sql-database-v12-upgrade/upgrade-server3.png
[4]: ./media/sql-database-v12-upgrade/online-during-upgrade.png
[5]: ./media/sql-database-v12-upgrade/enabled.png
[6]: ./media/sql-database-v12-upgrade/recommendations.png

<!---HONumber=Oct15_HO3-->