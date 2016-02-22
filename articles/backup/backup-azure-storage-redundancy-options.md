<properties
	pageTitle="決定 Azure 備份儲存體備援選項 | Microsoft Azure"
	description="了解異地備援儲存體與本地備援儲存體之間的差異，以決定您的「Azure 備份」儲存體備援選項。"
	services="backup"
	documentationCenter=""
	authors="Jim-Parker"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/05/2016"
	ms.author="jimpark; markgal"/>


# Azure 備份的儲存體備援選項

您的業務需求會決定「Azure 備份」儲存體的適當儲存體備援選項。如果您使用 Azure 做為主要的備份儲存體端點 (例如您要從 Windows Server 備份至 Azure)，您應該考慮挑選異地備援儲存體選項 (預設)。如果您使用 Azure 做為第三備份儲存體端點 (例如您使用 SCDPM 在內部部署環境中建立本機備份複本，並使用 Azure 來滿足長期保留需求)，則應該考慮選擇本地備援儲存體。

## 異地備援儲存體 (GRS)

異地備援儲存體最多可保有六個資料複本。有了 GRS，您的資料會在主要區域內複寫三次，並在與主要區域相距甚遠的次要區域內複寫三次，提供最高等級的持久性。將資料儲存在 GRS 時，若主要區域發生故障，則 Azure 備份可確保您的資料持續儲存在兩個不同區域中。

## 本機備援儲存體 (LRS)

本地備援儲存體可維護三個資料複本。LRS 會在單一區域的單一設備內複寫三次。LRS 可保護您的資料免於一般硬體故障，但無法避免整體 Azure 設備故障。這將減少在 Azure 中儲存資料的成本，同時針對您可能會接受第三個複本的資料提供較低層級的持久性。

您可以從備份保存庫的 [設定] 選項選取符合您需求的適當選項。

## 後續步驟

- 確定您的環境已[準備好備份 Windows 伺服器或用戶端電腦](backup-configure-vault.md)
- 如果您仍有未獲得解答的問題，請查看 [Azure 備份常見問題集](backup-azure-backup-faq.md)。
- 瀏覽 [Azure 備份論壇](http://go.microsoft.com/fwlink/p/?LinkId=290933)

<!---HONumber=AcomDC_0211_2016-->