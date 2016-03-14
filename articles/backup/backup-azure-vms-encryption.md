<properties
   pageTitle="Azure 備份 - 利用加密磁碟備份 Azure IaaS VM |Microsoft Azure"
   description="了解 Azure 備份如何處理在 IaaS VM 備份期間使用 BitLocker 或 dmcrypt 加密的資料。本文介紹備份與還原作業在處理加密磁碟時的差異。"
   services="backup"
   documentationCenter=""
   authors="markgalioto"
   manager="jwhit"
   editor=""/>
<tags
   ms.service="backup"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="storage-backup-recovery"
   ms.date="11/27/2015"
   ms.author="markgal; jimpark"/>

# 處理 VM 備份期間加密的磁碟

如果企業想要在 Azure 中加密其 VM 資料，可以使用 Bitlocker (Windows 電腦上) 或 dmcrypt (Linux 電腦上) 解決方案。這兩種解決方案都是磁碟區層級加密解決方案。本文詳細說明如何為這類 Azure VM 設定備份，以及加密的資料對於還原工作流程的影響。

## 備份的運作方式

整體解決方案包含兩個層級：VM 層和儲存層。

1. VM 層處理客體作業系統和虛擬機器中執行的應用程式所看到的資料。它也是執行加密軟體 (Bitlocker 或 dmcrypt)，並以透明方式加密磁碟區上的資料再寫入磁碟的層級。
2. 儲存層處理連接到 VM 的分頁 Blob 和磁碟。它不知道正在寫入磁碟的資料，也不知道資料是否已加密。這是 VM 備份功能運作的層級。

![Bitlocker 加密與 Azure VM 備份如何共存](./media/backup-azure-vms-encryption/how-it-works.png)

整個資料加密會以透明方式並順暢地在 VM 層中進行。因此，寫入連接到 VM 之分頁 Blob 的資料會是加密的資料。當 [Azure 備份擷取 VM 的磁碟快照並傳輸資料](backup-azure-vms-introduction.md#how-does-azure-back-up-virtual-machines)時，它會複製分頁 Blob 上的加密資料。

## 解決方案元件

若要讓這個解決方案正常運作，您必須正確地設定及管理其所包含的許多組件：

| 函數 | 使用的軟體 | 其他注意事項 |
| -------- | ------------- | ------- |
| 加密 | Bitlocker 或 dmcrypt | 由於相較於 Azure 備份，此加密會在「不同」層級中進行，因此可以使用任何加密軟體。但只有 Bitlocker 和 dmcrypt 經過驗證。<br><br> 若要加密資料，需要有金鑰。您必須將金鑰存放在安全的位置，以確保資料的存取經過授權。 |
| 金鑰管理 | CloudLink SecureVM<br>或 Azure KeyVault | 金鑰是加密或解密資料的必要項目。如果沒有正確的金鑰，就無法擷取資料。這對下列作業「非常」重要：<br><li>金鑰變換<li>長期保留<br><br>例如，用來備份 7 年前資料的金鑰可能與今日所使用的金鑰不同。如果沒有 7 年前的金鑰，就無法使用從該時間還原的資料。|
| 資料備份 | Azure 備份 | 使用 Azure 備份並搭配 [Azure 管理入口網站](http://manage.windowsazure.com)或 PowerShell 來備份您的 Azure IaaS VM |
| 資料還原 | Azure 備份 | 使用 Azure 備份從復原點還原磁碟或整個 VM。資料不是由 Azure 備份當做還原作業的一部分來解密。|
| 解密 | Bitlocker 或 dmcrypt | 為了從還原的資料磁碟或還原的 VM 讀取資料，軟體需要有來自金鑰管理軟體的金鑰。如果沒有正確的金鑰，就無法解密資料。 |

> [AZURE.IMPORTANT]  金鑰管理 (包括金鑰變換) 不是 Azure 備份的一部分。這方面需要分開管理，但對整體備份/還原作業很重要。

## CloudLink SecureVM

[CloudLink SecureVM](http://www.cloudlinktech.com/choose-your-cloud/microsoft-azure/) 是 VM 加密解決方案，可以用來保護您的 Azure IaaS VM 資料。CloudLink SecureVM 可搭配 Azure 備份使用。

### 支援資訊

- CloudLink SecureVM 4.0 版 (組建 21536.121 或更新版本)
- Azure PowerShell 0.9.8 版或更新版本。

### 金鑰管理

當您需要變換或變更已有備份之 VM 的金鑰時，您需要確保備份時所用的金鑰可供使用。建議的方法之一是建立金鑰存放區或整個 SecureVM 系統的備份。

### 文件和資源

- [部署指南 - PDF](http://www.cloudlinktech.com/Azure/CL_SecureVM_4_0_DG_EMC_Azure_R2.pdf)
- [部署及使用 SecureVM - 影片](https://www.youtube.com/watch?v=8AIRe92UDNg)

<!---HONumber=AcomDC_0302_2016-->