<properties 
	pageTitle="使用 Azure 入口網站升級至 Azure SQL Database V12 | Microsoft Azure" 
	description="說明如何使用 Azure 入口網站升級至 Azure SQL Database V12，包括如何升級 Web 和商務資料庫，以及如何將 V11 伺服器的資料庫直接移轉至彈性資料庫集區來升級 V11 伺服器。" 
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
	ms.date="12/01/2015" 
	ms.author="sstein"/>


# 使用 Azure 入口網站升級至 Azure SQL Database V12


> [AZURE.SELECTOR]
- [Azure portal](sql-database-upgrade-server-portal.md)
- [PowerShell](sql-database-upgrade-server-powershell.md)


SQL Database V12 是最新的版本，因此建議升級至 SQL Database V12。SQL Database V12 具有[舊版所欠缺的許多優點](sql-database-v12-whats-new.md)，包括：

- 提升與 SQL Server 的相容性。
- 提供改進的高階效能和新的效能等級。
- [彈性資料庫集區](sql-database-elastic-pool.md)。

本文提供將現有的 SQL Database V11 伺服器和資料庫升級至 SQL Database V12 的說明。

在升級至 V12 的過程中，您將會把所有 Web 和商務資料庫都升級至新的服務層級，因此本文也包含了升級 Web 和商務資料庫的說明。

此外，與升級至單一資料庫的個別效能等級 (定價層) 相比，移轉至[彈性資料庫集區](sql-database-elastic-pool.md)更符合成本效益。集區也可以簡化資料庫管理，因為您只需要管理集區的效能設定，而不需分開管理個別資料庫的效能等級。如果您的資料庫位於多部伺服器上，請考慮將它們移到相同的伺服器，並利用將它們放入集區所帶來的優點。您可以輕鬆地[使用 PowerShell 自動將資料庫從 V11 伺服器直接移轉至彈性資料庫集區](sql-database-upgrade-server.md)。您也可以使用入口網站將 V11 資料庫移轉至集區，但在入口網站中，您必須已經備妥 V12 伺服器來建立集區。如果您擁有[可從集區受益的資料庫](sql-database-elastic-pool-guidance.md)，本文稍後有提供在升級伺服器之後建立集區的說明。

請注意，您的資料庫會維持在線上，並且在整個升級作業中都會繼續保持運作。在實際轉換到新的效能等級時，資料庫連線可能會暫時中斷一段非常短的時間，通常約 90 秒，但最長可達 5 分鐘。如果您的應用程式[對於連線終止有暫時性的錯誤處理方式](sql-database-connect-central-recommendations.md)，就足以在升級結束時防止連線中斷。

升級至 SQL Database V12 後即無法復原。在升級之後，即無法將伺服器還原至 V11。

升級至 V12 之後，將不會立即提供[服務層級建議](sql-database-service-tier-advisor.md)和[彈性集區建議](sql-database-elastic-pool-portal.md#step-2-choose-a-pricing-tier)，必須等到服務有時間評估您在新伺服器上的工作負載之後，才會提供。V11 伺服器建議記錄不適用於 V12 伺服器，因此不會保留。


## 準備升級

- **升級所有 Web 和商務資料庫**：請參閱下面的[升級所有 Web 和商務資料庫](sql-database-upgrade-server-portal.md#upgrade-all-web-and-business-databases)一節，或使用 [PowerShell 來升級資料庫和伺服器](sql-database-upgrade-server-powershell.md)。
- **檢閱和暫停異地複寫**：如果您的 Azure SQL Database 已針對異地複寫做設定，您應該記錄其目前的設定並[停止異地複寫](sql-database-geo-replication-portal.md#remove-secondary-database)。在升級完成之後，請重新設定資料庫的異地複寫。
- **如果您的用戶端位於 Azure VM 上，請開啟這些連接埠**：如果您的用戶端在 Azure 虛擬機器 (VM) 上執行，而用戶端程式連線到 SQL Database V12，您就必須開啟此 VM 上 11000-11999 和 14000-14999 範圍的連接埠。如需詳細資訊，請參閱 [SQL Database V12 的連接埠](sql-database-develop-direct-route-ports-adonet-v12.md)。



## 開始升級

1. 在 [Azure 入口網站](http://portal.azure.com/)中，瀏覽至您想要升級的伺服器，方法是選取 [瀏覽全部] > [SQL Server]，然後選取所需的伺服器。
2. 選取 [**最新的 SQL Database 更新**]，然後選取 [**升級此伺服器**]。

      ![升級伺服器][1]

## 升級所有 Web 和商務資料庫

如果您的伺服器內有任何 Web 或 Business 資料庫，就必須將這些資料庫升級。在升級至 SQL Database V12 的過程中，您將會把所有 Web 和商務資料庫更新至新的服務層級。

為了協助您完成升級，SQL Database 服務建議為每個資料庫選擇適當的服務層和效能層級 (定價層)。服務會透過分析您資料庫過去的使用情況，建議最適合用於執行您現有資料庫工作負載的層。
    
3. 在 [**升級此伺服器**] 刀鋒視窗中，選取每個要檢閱的資料庫，然後選取建議的定價層來進行升級。您隨時都可以瀏覽各種可用的定價層，並選取最符合您環境的選項。


     ![資料庫][2]


7. 按一下建議的層之後，您會看到 [**選擇定價層**] 刀鋒視窗，您可以在此視窗選取一個層，然後按一下 [**選取**] 按鈕來變更為該層。為每個 Web 或商務資料庫選取新的層。

    請特別注意，SQL Database 不會鎖定至任何特定的服務層級或效能等級，因此當您的資料庫需求變更時，您可以在可用的服務層級和效能等級之間輕鬆進行變更。事實上，基本、標準和高階 SQL Database 是以小時計費，您可以在 24 小時內向上或向下調整每個資料庫 4 次。

    ![Mahout][6]


伺服器的所有資料庫都符合資格之後，您就可以準備開始升級

## 確認升級

3. 當伺服器上的所有資料庫都符合升級條件之後，您必須 [輸入伺服器名稱] 以確認您想要執行升級，然後按一下 [確定]。 

    ![確認升級][3]


4. 升級隨即開始，過程中也會顯示進度通知。升級程序即會啟動。視特定資料庫的詳細資料而定，升級至 V12 可能需要一些時間。在這段期間，伺服器上的所有資料庫將維持線上狀態，但伺服器和資料庫的管理動作將受到限制。

    ![升級進行中][4]

    在實際轉換到新的效能層級時，資料庫連線可能會暫時中斷一小段時間 (通常以秒計算)。若應用程式對於連線終止出現暫時性的錯誤處理 (重試邏輯)，在升級結束時連線將不會中斷。

5. 升級作業完成之後，[**最新更新**] 刀鋒視窗將會顯示 [**已啟用**]。

    ![已啟用 V12][5]

## 將資料庫移至彈性資料庫集區

在 [Azure 入口網站](https://portal.azure.com/)中，瀏覽至 V12 伺服器並按一下 [新增集區]。

-或-

如果您看到 [按一下這裡可檢視針對此伺服器所建議的彈性資料庫集區] 訊息，請按一下該處，即可輕鬆地建立已針對您伺服器的資料庫進行最佳化的集區。如需詳細資訊，請參閱[建議的彈性資料庫集區](sql-database-elastic-pool-portal.md#recommended-elastic-database-pools)。

![將集區加入伺服器][7]
   
請依照[建立彈性資料庫集區](sql-database-elastic-pool.md)一文中的說明來完成集區建立。

## 在升級至 SQL Database V12 後監視資料庫


升級之後，建議您主動監視資料庫，以確保應用程式達到所需的執行效能，並視需要將使用情況調整到最佳狀態。

除了監視個別的資料庫之外，您也可以[使用入口網站](sql-database-elastic-pool-portal.md#monitor-and-manage-an-elastic-database-pool)或藉由 [PowerShell](sql-database-elastic-pool-powershell.md#monitoring-elastic-databases-and-elastic-database-pools) 監視彈性資料庫集區。


**資源耗用量資料：**「基本」、「標準」及「高階」資料庫的資源耗用量資料會透過使用者資料庫中的 [sys.dm\_ db\_ resource\_stats](http://msdn.microsoft.com/library/azure/dn800981.aspx) DMV 提供。此 DMV 以 15 秒的間隔提供幾乎即時的前一小時作業資源耗用量資訊。某一間隔的 DTU 百分比耗用量會以 CPU、IO 及記錄檔方面的最大百分比耗用量來計算。下列是計算前一小時之平均 DTU 百分比耗用量的查詢：

    SELECT end_time
    	 , (SELECT Max(v)
             FROM (VALUES (avg_cpu_percent)
                         , (avg_data_io_percent)
                         , (avg_log_write_percent)
    	   ) AS value(v)) AS [avg_DTU_percent]
    FROM sys.dm_db_resource_stats
    ORDER BY end_time DESC;

其他監視資訊：

- [單一資料庫的 Azure SQL Database 效能指引](http://msdn.microsoft.com/library/azure/dn369873.aspx)。
- [彈性資料庫集區的價格和效能考量](sql-database=elastic-pool-guidance.md)。
- [使用動態管理檢視監視 Azure SQL Database](sql-database-monitoring-with-dmvs.md)




**警示：**在 Azure 入口網站中設定「警示」，即可在已升級之資料庫的 DTU 耗用量接近特定的高層級時通知您。您可以在 Azure 入口網站中為各種效能計量 (例如 DTU、CPU、IO 及記錄檔) 設定資料庫警示。請瀏覽至您的資料庫，然後在 [**設定**] 刀鋒視窗中，選取 [**警示規則**]。

例如，您可以設定若過去 5 分鐘的平均 DTU 百分比值超出 75% 則發出「 DTU 百分比 」電子郵件警示。若要深入了解如何設定警示通知，請參閱[接收警示通知](insights-receive-alert-notifications.md)。





## 後續步驟

- [查看彈性資料庫集區建議](sql-database-elastic-pool-portal.md#recommended-elastic-database-pools)。
- [建立彈性資料庫集區](sql-database-elastic-pool-portal.md)，並將您的部分或全部資料庫新增至集區。
- [變更您資料庫的服務層級和效能等級](sql-database-scale-up.md)。



## 相關連結

- [SQL Database V12 新功能](sql-database-v12-whats-new.md)
- [規劃和準備升級至 SQL Database V12](sql-database-v12-plan-prepare-upgrade.md)


<!--Image references-->
[1]: ./media/sql-database-upgrade-server-portal/latest-sql-database-update.png
[2]: ./media/sql-database-upgrade-server-portal/upgrade-server2.png
[3]: ./media/sql-database-upgrade-server-portal/upgrade-server3.png
[4]: ./media/sql-database-upgrade-server-portal/online-during-upgrade.png
[5]: ./media/sql-database-upgrade-server-portal/enabled.png
[6]: ./media/sql-database-upgrade-server-portal/recommendations.png
[7]: ./media/sql-database-upgrade-server-portal/new-elastic-pool.png

<!---HONumber=AcomDC_1203_2015-->