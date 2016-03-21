<properties
	pageTitle="Azure SQL Database 的主動式異地複寫"
	description="本主題說明 SQL Database 的主動式異地複寫與其用途。"
	services="sql-database"
	documentationCenter="na"
	authors="rothja"
	manager="jeffreyg"
	editor="monicar" />


<tags
	ms.service="sql-database"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="data-management"
	ms.date="03/07/2016"
	ms.author="jroth" />

# Azure SQL Database 的主動式異地複寫

## 概觀
主動式異地複寫功能會實作可在相同 Microsoft Azure 區域或不同區域 (異地備援) 內提供資料庫備援的機制。主動式異地複寫以非同步方式從資料庫將已認可的交易複寫至不同伺服器上最多四個資料庫複本。當設定作用中異地複寫時，會在指定的伺服器上建立次要資料庫。原始的資料庫會變成主要資料庫。主要資料庫會以非同步方式將已認可的交易複寫到每一個次要資料庫。雖然次要資料可能會在任何指定時間點稍微落後主要資料庫，但是次要資料庫保證一定會和主要資料庫認可的變更保持交易一致性。

作用中異地複寫的主要優點之一，是它提供了具有極短復原時間的資料庫層級災害復原解決方案。當您將次要資料庫放在不同區域中的伺服器上，就能夠為應用程式中加入最大的恢復能力。跨區域備援可讓應用程式從天然災害、災難性人為錯誤或惡意行為所造成的全部或部分資料中心永久遺失復原。下圖顯示在進階資料庫上設定的作用中異地複寫的範例，在美國西部區域有主要資料庫，在美國東部區域有次要資料庫。

![異地複寫關聯性](./media/sql-database-active-geo-replication/geo-replication-relationship.png)

另一個主要優點是次要資料庫可讀取，而且可用來卸載唯讀工作負載，例如報告作業。如果您只想要使用的次要資料庫進行負載平衡，您可以在與主要資料庫相同的區域中建立它。不過，這不會增加應用程式對於災難性失敗的恢復能力。

可以使用主動式異地覆寫的其他情況包括：

- 資料庫移轉：您可以使用主動式異地覆寫，以最少的停機時間將資料庫從一部伺服器移轉到另一個線上伺服器。
- 應用程式升級：您可以在應用程式升級期間建立額外的次要資料庫做為容錯回復複本。

若要達到真正的業務持續性，新增資料中心之間的資料庫備援只是解決方案的一部分。在災難性失敗後要端對端復原應用程式 (服務) 需要復原構成服務的所有元件和任何相依的服務。這些元件的範例包括用戶端軟體 (例如自訂 JavaScript 的瀏覽器)、web 前端、儲存體和 DNS。所有元件都必須對相同的失敗具有恢復功能，並且在應用程式的復原時間目標 (RTO) 內可供使用。因此，您需要識別所有相依服務並了解其提供的保證與功能。然後，您必須採取適當步驟以確保服務功能在它所依賴的服務容錯移轉期間都正常。如需有關設計災害復原解決方案的詳細資訊，請參閱[使用主動式異地複寫設計災害復原的雲端解決方案](sql-database-designing-cloud-solutions-for-disaster-recovery.md)。

## 主動式異地複寫功能
主動式異地覆寫功能提供下列基本功能：

- 自動非同步複寫︰您只能藉由加入至現有資料庫來建立次要資料庫。次要資料庫只能建立在不同的 Azure SQL Database 伺服器。一旦建立之後，次要資料庫就要填入從主要資料庫複製的資料。這個程序稱為植入。建立並植入次要資料庫之後，主要資料庫的更新會以非同步方式自動複製到次要資料庫。這表示交易會先在主要資料庫上受到認可，才會複製到次要資料庫。SQL Database 保證在植入完成之後，次要資料庫會一直維持交易一致性。如需有關建立次要資料庫的詳細資訊，請參閱[使用 Transact-SQL 為 Azure SQL Database 進行異地複寫](sql-database-geo-replication-transact-sql.md)和[使用 PowerShell 為 Azure SQL Database 進行異地複寫](sql-database-geo-replication-powershell.md)。如需使用 Azure 入口網站建立次要資料庫的詳細資訊，請參閱[使用 Azure 入口網站為 Azure SQL Database 設定異地複寫](sql-database-geo-replication-portal.md)。

- 多個次要資料庫：兩個或以上的次要資料庫可增加主要資料庫和應用程式的備援和保護層級。如果有多個次要資料庫存在，即使其中一個次要資料庫失敗，應用程式仍會受到保護。如果只有一個次要資料庫卻失敗了，應用程式會暴露在更高的風險中，直到建立新的次要資料庫。

- 可讀取的次要資料庫：應用程式可以存取次要資料庫，使用用於存取主要資料庫的相同安全性主體或不同的安全性主體進行唯讀作業。在快照集隔離模式中執行次要資料庫，以確保主要資料庫更新的複寫 (記錄重播) 不會被次要資料庫上執行的查詢延遲。

>[AZURE.NOTE] 如果有從主要資料庫接收的結構描述更新，則記錄重播將會延遲，因為它需要次要資料庫上的結構描述鎖定。

- 彈性集區資料庫的作用中異地複寫：作用中異地複寫可以針對進階彈性資料庫集區中的任何資料庫進行設定。次要資料庫可以在其他進階彈性資料庫集區中。對於一般資料庫，只要服務層相同，次要資料庫可以是彈性資料庫集區，反之亦然。如需有關如何針對彈性集區資料庫進行異地複寫的詳細資訊，請參閱[使用 Transact-SQL 為 Azure SQL Database 進行異地複寫](sql-database-geo-replication-transact-sql.md)和[使用 PowerShell 為 Azure SQL Database 進行異地複寫](sql-database-geo-replication-powershell.md)。  

- 次要資料庫的可設定效能層級︰可以使用比主要資料庫低的效能層級建立次要資料庫。主要和次要資料庫必須有相同的服務層。對於具有高資料庫寫入活動的應用程式不建議使用這個選項，因為它可能會導致增加的複寫延遲，進而造成容錯移轉之後高度資料遺失的風險。此外，在容錯移轉之後應用程式的效能將會受到影響，直到新的主要資料庫升級至較高的效能層級。在 Azure 入口網站上的記錄 IO 百分比圖表，提供預估次要資料庫承受複寫負載所需的最少效能層級的好方法。例如，如果您的主要資料庫是 P6 (1000 DTU) 和其記錄 IO 百分比為 50%，則次要資料庫必須至少是 P4 (500 DTU)。您也可以使用 [sys.resource\_stats](https://msdn.microsoft.com/library/dn269979.aspx) 或 [sys.dm\_db\_resource\_stats](https://msdn.microsoft.com/library/dn800981.aspx) 資料庫檢視，擷取記錄 IO 資料。如需 SQL Database 效能層級的詳細資訊，請參閱 [SQL Database 選項和效能](sql-database-service-tiers.md)。如需有關如何設定次要資料庫效能層級範例的詳細資訊，請參閱[使用 Transact-SQL 為 Azure SQL Database 進行異地複寫](sql-database-geo-replication-transact-sql.md)和[使用 PowerShell 為 Azure SQL Database 進行異地複寫](sql-database-geo-replication-powershell.md)。

- 使用者控制容錯移轉和容錯回復：次要資料庫可以隨時透過應用程式或使用者的明確動作，切換到主要角色。在實際的中斷期間，應該使用「非計劃性」選項，這樣會立即將次要升級為主要。當失敗的主要資料庫復原，並且再次可以使用，系統會自動將它標示為次要資料庫，並讓它與新的主要資料庫保持更新。由於複寫的非同步本質，如果主要資料庫在將最新的變更複寫至次要資料庫之前失敗，則非計劃的容錯移轉期間會有少量的資料遺失。當具有多個次要資料庫的主要資料庫容錯移轉時，系統會自動重新設定複寫關聯性，並且將剩餘的次要資料庫連結至新升級的主要資料庫，而不需要任何使用者介入。解決造成容錯移轉的中斷之後，可能想要讓應用程式返回主要區域。若要這麼做，應該使用「計劃」選項叫用容錯移轉命令。如需如何使用 T-SQL 啟用容錯移轉的詳細資訊，請參閱[使用 Transact-SQL 為 Azure SQL Database 進行異地複寫](sql-database-geo-replication-transact-sql.md)。如需如何使用 PowerShell 啟用容錯移轉的詳細資訊，請參閱[使用 PowerShell 為 Azure SQL Database 進行異地複寫](sql-database-geo-replication-powershell.md)。

- 保持認證和防火牆規則同步︰我們建議針對異地複寫資料庫使用[資料庫防火牆規則](sql-database-firewall-configure.md)，這樣這些規則可以與資料庫複寫，以確保所有次要資料庫具有與主要資料庫相同的防火牆組態。這樣就不需要客戶手動設定及維護同時裝載主要和次要資料庫之伺服器上的防火牆規則。同樣地，針對資料存取使用[自主資料庫使用者](sql-database-manage-logins.md)，確保主要與次要資料庫永遠具有相同的使用者認證，在容錯移轉時不會因為登入和密碼不相符而有任何中斷。使用額外的 [Azure Active Directory](../active-directory/active-directory-whatis.md)，客戶可以管理主要和次要資料庫的使用者存取，並且排除在資料庫中管理認證的需求。

- Azure Resource Manager API 和以角色為基礎的安全性︰作用中異地複寫包含一組 [Azure Resource Manager (ARM) API](https://msdn.microsoft.com/library/azure/mt163571.aspx) 以進行管理，包括[以 ARM 為基礎的 PowerShell Cmdlet](sql-database-geo-replication-powershell.md)。這些 API 需要使用資源群組，並支援以角色為基礎的安全性 (RBAC)。如需如何實作存取角色的詳細資訊，請參閱 [Azure 角色型存取控制](../active-directory/role-based-access-control-configure.md)。

>[AZURE.NOTE] 現有 [Azure SQL 服務管理 (傳統) REST API](https://msdn.microsoft.com/library/azure/dn505719.aspx) 和 [Azure SQL Database (傳統) Cmdlet](https://msdn.microsoft.com/library/azure/dn546723.aspx) 支援回溯相容性。不過，許多作用中異地複寫的新功能只在以 ARM 為基礎的 [Azure SQL REST API](https://msdn.microsoft.com/library/azure/mt163571.aspx) 和以 ARM 為基礎的 [Azure SQL Database PowerShell Cmdlet](https://msdn.microsoft.com/library/azure/mt574084.aspx) 中受到支援。

## 防止重要資料遺失
由於廣域網路的高度延遲，連續複製採用非同步複寫機制。這使得發生失敗時部分資料遺失是無法避免的。不過，有些應用程式可能會要求資料不能遺失。若要保護這些重大更新，應用程式開發人員可以在認可交易後立即呼叫 [sp\_wait\_for\_database\_copy\_sync](https://msdn.microsoft.com/library/dn467644.aspx) 系統程序。呼叫 sp\_wait\_for\_database\_copy\_sync 會封鎖呼叫執行緒，直到最後認可的交易複寫到次要資料庫。此程序會等候直到次要資料庫認可所有加入佇列的交易。sp\_wait\_for\_database\_copy\_sync 以特定的連續複製連結為範圍。任何具備主要資料庫連接權限的使用者都可以呼叫此程序。

>[AZURE.NOTE] sp\_wait\_for\_database\_copy\_sync 程序呼叫所造成的延遲可能會相當可觀。延遲會取決於當時交易記錄長度的大小，而且不會傳回，直到複寫整個記錄。請避免呼叫此程序，除非絕對必要。

## 後續步驟
如需 SQL Database 業務連續性功能的詳細資訊，請參閱[業務連續性概觀](sql-database-business-continuity.md)。

<!---HONumber=AcomDC_0309_2016-->