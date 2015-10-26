<properties
	pageTitle="建立和匯出 Azure SQL Database 的 BACPAC"
	description="建立和匯出 Azure SQL Database 的 BACPAC"
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="10/13/2015"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# 建立和匯出 Azure SQL Database 的 BACPAC

**單一資料庫**

> [AZURE.SELECTOR]
- [Azure Preview Portal](sql-database-export.md)
- [PowerShell](sql-database-export-powershell.md)

本文提供使用 [Azure 預覽入口網站](https://portal.azure.com)匯出您 Azure SQL Database 的 BACPAC 的說明。

BACPAC 是一種包含資料庫結構描述和資料的 .bacpac 檔案。如需詳細資訊，請參閱[資料層應用程式](https://msdn.microsoft.com/library/ee210546.aspx)中的備份封裝 (.bacpac)。

> [AZURE.NOTE]Azure SQL Database 會自動為每個使用者資料庫建立備份。如需詳細資訊，請參閱[商務持續性概觀](sql-database-business-continuity.md)。


BACPAC 會匯出至 Azure 儲存體 Blob 容器，以供您在作業順利完成之後下載。

若要完成本文，您需要下列項目：

- Azure 訂用帳戶。如果需要 Azure 訂用帳戶，可以先按一下此頁面頂端的 [免費試用]，然後再回來完成這篇文章。
- Azure SQL Database。如果沒有 SQL Database，請遵循本文中以下的步驟：[建立您的第一個 Azure SQL Database](sql-database-get-started.md)。
- 用來儲存資料庫備份的 [Azure 儲存體帳戶](storage-create-storage-account.md)與 Blob 容器。目前，儲存體帳戶必須使用傳統的部署模型，因此建立儲存體帳戶時，請選擇**傳統**。 


## 匯出您的資料庫

開啟您想要匯出 .bacpac 檔案當做資料庫的 SQL Database 刀鋒視窗：

1.	移至 [Azure Preview 入口網站](https://portal.azure.com)。
2.	按一下 [全部瀏覽]。
3.	按一下 [SQL 資料庫]。
2.	按一下您想要匯出為 BACPAC 的資料庫。
3.	在 SQL Database 刀鋒視窗中，按一下 [匯出] 以開啟 [匯出資料庫] 刀鋒視窗：

    ![匯出按鈕][1]

1.  按一下 [儲存體]，並選取您的儲存體帳戶以及要儲存 BACPAC 的 Blob 容器：

    ![匯出資料庫][2]

1.  針對包含您要備份之資料庫的 Azure SQL Server，輸入 **Server 系統管理員登入**和**密碼**。
1.  按一下 [建立] 匯出資料庫。

按一下 [建立] 時，會建立匯出資料庫要求並將它提交至服務。視資料庫大小而定，匯出作業可能需要一些時間才能完成。

## 監視匯出作業的進度

2.	按一下 [全部瀏覽]。
3.	按一下 [SQL Server]。
2.	按一下包含您剛才匯出的原始 (來源) 資料庫的伺服器。
3.	在 SQL Server 刀鋒視窗中，按一下 [匯入/匯出記錄]：

    ![匯入匯出記錄][3] ![匯入匯出記錄][4]

## 確認 BACPAC 位於儲存體容器中

2.	按一下 [全部瀏覽]。
3.	按一下 [儲存體帳戶 (傳統)]。
2.	按一下您用來儲存 BACPAC 的儲存體帳戶。
3.	按一下 [容器]，並選取存放匯出資料庫的容器，以取得備份的詳細資料 (您也可以從這裡下載並儲存 BACPAC)。

    ![.bacpac 檔案詳細資料][5]


## 後續步驟

- [匯入 Azure SQL Database](sql-database-import.md)



## 其他資源

- [業務續航力概觀](sql-database-business-continuity.md)
- [災害復原詳細資訊](sql-database-disaster-recovery-drills.md)
- [SQL Database 文件](https://azure.microsoft.com/documentation/services/sql-database/)


<!--Image references-->
[1]: ./media/sql-database-export/export.png
[2]: ./media/sql-database-export/export-blade.png
[3]: ./media/sql-database-export/export-history.png
[4]: ./media/sql-database-export/export-status.png
[5]: ./media/sql-database-export/bacpac-details.png

<!---HONumber=Oct15_HO3-->