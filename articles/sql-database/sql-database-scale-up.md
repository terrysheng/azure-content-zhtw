<properties
	pageTitle="變更 Azure SQL Database 的服務層級和效能等級"
	description="變更 Azure SQL Database 的服務層級和效能等級說明如何相應增加或減少您的 SQL Database。變更 Azure SQL Database 的定價層。"
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="12/01/2015"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# 變更 SQL Database 的服務層級和效能等級 (定價層)

**單一資料庫**

> [AZURE.SELECTOR]
- [Azure Portal](sql-database-scale-up.md)
- [PowerShell](sql-database-scale-up-powershell.md)

本文將說明如何使用 [Azure 入口網站](https://portal.azure.com)變更您的 SQL Database 的服務層級和效能等級。

使用[將 SQL Database Web/Business 資料庫升級至新的服務層](sql-database-upgrade-new-service-tiers.md)和 [Azure SQL Database 服務層和效能等級](sql-database-service-tiers.md)中的資訊，來判斷適合您 Azure SQL Database 的服務層和效能等級。

> [AZURE.IMPORTANT]變更 SQL Database 的服務層級和效能等級是線上作業。這表示您的資料庫在整個作業過程中，將維持在線上可供使用的狀態而不需停機。

- 若要將資料庫降級，資料庫應該小於目標服務層允許的大小上限。 
- 升級資料庫時若將[標準地理複寫](https://msdn.microsoft.com/library/azure/dn758204.aspx)或[作用中地理複寫](https://msdn.microsoft.com/library/azure/dn741339.aspx)啟用，您必須先將其次要資料庫升級為所需的效能層次，然後再升級主要資料庫。
- 從高階服務層降級時，您必須先終止所有的「異地複寫」關聯性。您可以遵循[終止連續複製關聯性](https://msdn.microsoft.com/library/azure/dn741323.aspx)主題所述的步驟，停止主要資料庫與作用中次要資料庫之間的複寫程序。
- 還原服務會針對各種服務層提供不同的選項。降級後您可能會無法還原至某個時間點，或具有較短的備份保留期限。如需詳細資訊，請參閱 [Azure SQL Database 備份和還原](https://msdn.microsoft.com/library/azure/jj650016.aspx)。
- 您可以在 24 小時期間內，變更最多四個個別的資料庫 (服務層或效能等級)。
- 完成變更之前，不會將新屬性套用至資料庫。


**若要完成本文，您需要下列項目：**

- Azure 訂用帳戶。如果需要 Azure 訂用帳戶，可以先按一下此頁面頂端的 [免費試用]，然後再回來完成這篇文章。
- Azure SQL Database。如果您沒有 SQL Database，請遵循此文章中的步驟來建立：[建立您的第一個 Azure SQL Database](sql-database-get-started.md)。


## 變更您資料庫的服務層級和效能等級


開啟您想要相應增加或減少之資料庫的 [SQL Database] 刀鋒視窗：

1.	移至 [Azure 入口網站](https://portal.azure.com)。
2.	按一下 [全部瀏覽]。
3.	按一下 [SQL Database]。
2.	按一下您想要變更的資料庫。
3.	在 [SQL Database] 刀鋒視窗中，按一下 [定價層] 磚：

    ![定價磚][1]

1.  選取新的階層，然後按一下 [選取]：

    按一下 [選取] 提交調整要求以變更資料庫層。視資料庫大小而定，調整作業需要一些時間才能完成。按一下 [通知] 以取得調整作業的詳細資料和狀態。

    ![選取定價層][2]

3.	按一下左邊功能區的 [通知]：

    ![通知][3]

## 確認資料庫位在所選取的定價層

   調整作業完成之後，檢查及確認資料庫位在想要的階層：

2.	按一下 [全部瀏覽]。
3.	按一下 [SQL Database]。
2.	按一下您更新的資料庫。
3.	檢查 [定價層] 磚並確認它設定在正確的階層。

    ![新的價格][4]


## 後續步驟

- [相應放大和縮小](sql-database-elastic-scale-get-started.md)
- [使用 SSMS 連接和查詢 SQL Database](sql-database-connect-query-ssms.md)
- [匯出 Azure SQL Database](sql-database-export.md)

## 其他資源

- [業務續航力概觀](sql-database-business-continuity.md)
- [SQL Database 文件](https://azure.microsoft.com/documentation/services/sql-database/)


<!--Image references-->
[1]: ./media/sql-database-scale-up/pricing-tile.png
[2]: ./media/sql-database-scale-up/choose-tier.png
[3]: ./media/sql-database-scale-up/scale-notification.png
[4]: ./media/sql-database-scale-up/new-tier.png

<!---HONumber=AcomDC_1203_2015-->