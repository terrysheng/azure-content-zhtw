<properties
   pageTitle="將 Windows Server 或 Windows 用戶端檔案與資料夾備份至 Azure | Microsoft Azure"
   description="使用簡單程序將 Windows Server 或 Windows 用戶端備份至 Azure。您可以透過幾個簡單步驟，將 Windows 檔案和資料夾備份至雲端。"
   services="backup"
   documentationCenter=""
   authors="Jim-Parker"
   manager="jwhit"
   editor=""
   keywords="Windows 伺服器備份；備份 Windows 伺服器"/>

<tags
   ms.service="backup"
   ms.workload="storage-backup-recovery"
	 ms.tgt_pltfrm="na"
	 ms.devlang="na"
	 ms.topic="article"
	 ms.date="01/22/2016"
	 ms.author="jimpark;"/>

# 將 Windows Server 或 Windows 用戶端檔案和資料夾備份至 Azure
使用這個簡單程序即可將 Windows 檔案與資料夾備份至 Azure。如果您尚未這樣做，請先完成[必要條件](backup-configure-vault.md#before-you-start)為您的環境進行 Windows 電腦備份工作，再繼續執行。

## 備份檔案和資料夾
1. 電腦已註冊後，請開啟 Microsoft Azure 備份 MMC 嵌入式管理單元。

    ![搜尋結果](./media/backup-azure-backup-windows-server/result.png)

2. 按一下 [**排程備份**]

    ![Windows Server 備份排程](./media/backup-azure-backup-windows-server/schedulebackup.png)

3. 選取您想要備份的項目。Windows Server/Windows 用戶端 (例如不包含 System Center Data Protection Manager) 上的 Azure 備份可讓您保護檔案和資料夾。

    ![Windows Server 備份項目](./media/backup-azure-backup-windows-server/items.png)

4. 下列[文章](backup-azure-backup-cloud-as-tape.md)詳細說明指定備份排程和保留原則。

5. 選擇傳送初始備份的方法。您選擇的完成初始植入效率取決於您想要備份的資料量，以及網際網路的上傳連結速度。如果您打算在高延遲、低頻寬連接的情況下備份高達 GB/TB 的資料，建議您將磁碟送至最接近的 Azure 資料中心以完成初始備份。這稱為「離線備份」，在此[文章](backup-azure-backup-import-export.md)中涵蓋詳細說明。如果您有足夠的頻寬連接，建議您透過網路完成初始備份。

    ![Windows Server 初始備份](./media/backup-azure-backup-windows-server/initialbackup.png)

6. 排程備份程序完成後，請返回 MMC 嵌入式管理單元，然後按一下 [**立即備份**] 以透過網路完成初始植入。

    ![Windows Server 立即備份](./media/backup-azure-backup-windows-server/backupnow.png)

7. 完成初始植入後，Azure 備份主控台中的 [**工作**] 檢視會表示狀態。

    ![IR 已完成](./media/backup-azure-backup-windows-server/ircomplete.png)

## 後續步驟
- [管理 Windows Server 或 Windows 用戶端](backup-azure-manage-windows-server.md)
- [從 Azure 還原 Windows Server 或 Windows 用戶端](backup-azure-restore-windows-server.md)
- [Azure 備份常見問題集](backup-azure-backup-faq.md)

<!---HONumber=AcomDC_0128_2016-->