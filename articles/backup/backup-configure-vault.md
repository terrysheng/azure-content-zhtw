<properties
	pageTitle="設定 Azure 備份服務以準備備份 Windows Server | Microsoft Azure"
	description="本教學課程讓您了解如何使用 Microsoft Azure 雲端產品中的備份服務，將 Windows Server 備份至雲端。"
	services="backup"
	documentationCenter=""
	authors="Jim-Parker"
	manager="jwhit"
	editor=""/>

<tags ms.service="backup" ms.workload="storage-backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/21/2015" ms.author="jimpark"; "aashishr"/>

# 設定 Azure 備份以準備備份 Windows Server

本教學課程將引導您啟用 Azure 備份功能。若要將 Windows Server 或 Windows 用戶端備份至 Azure，您需要 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 [Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。
>[AZURE.NOTE]您之前需要建立或取得 X.509 第 3 版憑證，才能註冊備份伺服器。這些憑證仍受到支援，但現在為了簡化向伺服器註冊 Azure 保存庫的作業，您可以直接從 [快速啟動] 頁面產生保存庫認證。

## 開始之前
若要將檔案和資料從 Windows Server 備份至 Azure，您必須先：

- **建立備份保存庫** — 在 Azure 備份主控台中建立保存庫。
- **下載保存庫認證** — 在 Azure 備份中，將您建立的管理憑證上傳到保存庫。
- **安裝 Azure 備份代理程式並註冊伺服器** — 從 Azure 備份，安裝代理程式並在備份保存庫中註冊伺服器。

[AZURE.INCLUDE [backup-create-vault-wgif](../../includes/backup-create-vault-wgif.md)]

[AZURE.INCLUDE [backup-download-credentials](../../includes/backup-download-credentials.md)]

[AZURE.INCLUDE [backup-install-agent](../../includes/backup-install-agent.md)]

## 後續步驟
- [備份 Windows Server 或 Windows 用戶端](backup-azure-backup-windows-server.md)
- [管理 Windows Server 或 Windows 用戶端](backup-azure-manage-windows-server.md)
- [從 Azure 還原 Windows Server 或 Windows 用戶端](backup-azure-restore-windows-server.md)
- [Azure 備份常見問題集](backup-azure-backup-faq.md)
- [Azure 備份論壇](http://go.microsoft.com/fwlink/p/?LinkId=290933)

<!---HONumber=August15_HO9-->