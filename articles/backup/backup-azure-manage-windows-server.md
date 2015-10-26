<properties
	pageTitle="管理 Azure 備份保存庫與伺服器 | Microsoft Azure"
	description="使用本教學課程了解如何管理 Azure 備份保存庫與伺服器。"
	services="backup"
	documentationCenter=""
	authors="aashishr"
	manager="jwhit"
	editor="tysonn"/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/05/2015"
	ms.author="jimpark; aashishr; giridham"/>


# 管理 Azure 備份保存庫與伺服器
在本文中，您將了解透過管理入口網站提供之備份管理工作的概觀。

1. 登入[管理入口網站](https://manage.windowsazure.com)。
2. 按一下 [復原服務]，然後按一下備份保存庫的名稱以檢視 [快速啟動] 頁面。

    藉由選取 [快速啟動] 頁面頂端的選項，您可以看到可用的管理工作。

    ![受保護項目](./media/backup-azure-manage-windows-server/RS_tabs.png)

## 儀表板
選取 [儀表板] 以查看伺服器的使用量概觀。您可以在儀表板下方執行下列工作：

- **管理憑證**。如果已使用憑證註冊伺服器，這個選項可用來更新憑證。如果使用保存庫認證，則請勿使用 [管理憑證]。
- **刪除**。刪除目前的備份保存庫。如果您有不再使用的備份保存庫，可將之刪除來釋出儲存空間。只有在從保存庫中刪除了所有已註冊的伺服器之後，[刪除] 才會啟用。
- **保存庫認證**。這個 [快速概覽] 功能表項目可用來設定您的保存庫認證。

## 受保護項目
選取 [受保護項目]，以檢視已從伺服器備份的項目。此清單僅用來提供資訊。

![受保護項目](./media/backup-azure-manage-windows-server/RS_protecteditems.png)

## 已註冊的項目
選取 [已註冊的項目]，以檢視已註冊至此保存庫的伺服器名稱。

![已刪除的伺服器](./media/backup-azure-manage-windows-server/RS_deletedserver.png)

您可以在其中執行下列工作：

- **允許重新註冊** - 為伺服器選取此選項時，您可以使用代理程式中的「註冊精靈」，再次向備份保存庫註冊伺服器。如果憑證中有錯誤，或必須重建伺服器，您可能需要重新註冊。
- **刪除** - 從備份保存庫中刪除伺服器。所有與該伺服器相關聯的已儲存資料都將立即刪除。

## 後續步驟
- [從 Azure 還原 Windows Server 或 Windows 用戶端](backup-azure-restore-windows-server.md)
- 若要深入了解 Azure 備份，請參閱 [Azure 備份概觀](backup-introduction-to-azure-backup.md)
- 瀏覽 [Azure 備份論壇](http://go.microsoft.com/fwlink/p/?LinkId=290933)

<!---HONumber=Oct15_HO3-->