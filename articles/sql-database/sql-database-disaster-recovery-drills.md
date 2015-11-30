<properties 
   pageTitle="SQL Database 嚴重損壞修復演練" 
   description="了解使用 Azure SQL Database 執行嚴重損壞修復演練的指引和最佳作法，以協助確保您的關鍵性商務應用程式在失敗和中斷時可迅速復原。" 
   services="sql-database" 
   documentationCenter="" 
   authors="mihaelablendea" 
   manager="jeffreyg" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management" 
   ms.date="11/16/2015"
   ms.author="mihaelab"/>

#執行嚴重損壞修復演練

建議您定期驗證應用程式的復原工作流程整備。最佳設計作法是，驗證容錯移轉所涉及之資料遺失和 (或) 中斷的應用程式行為和影響。這也是大多數業界標準在業務續航力認證中的規定。

嚴重損壞修復演練內容包括：

- 模擬資料層中斷情況
- 復原 
- 驗證復原後的應用程式完整性

執行演練的工作流程會因您[設計業務續航力之應用程式](sql-database-business-continuity.md)的方式而異。以下將說明在 Azure SQL Database 的內容中進行嚴重損壞修復演練的最佳作法。

##異地還原

為了避免在進行嚴重損壞修復演練時可能遺失資料，建議您使用測試環境來執行演練，方法是建立生產環境的複本，然後使用這個複本來驗證應用程式的容錯移轉工作流程。
 
####中斷模擬

若要模擬中斷，您可以刪除或重新命名來源資料庫。這會導致應用程式連線失敗。

####復原

- 如[此處](sql-database-disaster-recovery.md)所述，將資料庫異地還原至其他伺服器。 
- 將應用程式組態變更為連接到復原資料庫，並遵循[在復原後設定資料庫](sql-database-disaster-recovery.md)指南以完成復原。

####驗證

- 驗證復原後的應用程式完整性 (例如連接字串、登入、基本功能測試或標準應用程式登出程序的其他驗證部分)，完成演練。

##異地複寫

針對使用異地複寫保護的資料庫，本演練內容涵蓋規劃容錯移轉至次要資料庫。規劃的容錯移轉可在角色切換時，確保主要和次要資料庫保持同步。與未計畫的容錯移轉不同的是，這項作業不會導致資料遺失，所以可以在生產環境中執行這項演練。

####中斷模擬

若要模擬中斷，您可以停用連接到資料庫的 Web 應用程式或虛擬機器。這會導致 Web 用戶端的連線失敗。

####復原

- 請確定 DR 區域中的應用程式組態會指向先前的次要資料庫，該資料庫將變成可完全存取的新主要資料庫。 
- 執行[規劃的容錯移轉](sql-database-geo-replication-powershell.md#initiate-a-planned-failover)將次要資料庫變成新的主要資料庫
- 請遵循[在復原後設定資料庫](sql-database-disaster-recovery.md)指南以完成復原。

####驗證

- 驗證復原後的應用程式完整性 (例如連接字串、登入、基本功能測試或標準應用程式登出程序的其他驗證部分)，完成演練。

<!---HONumber=Nov15_HO4-->