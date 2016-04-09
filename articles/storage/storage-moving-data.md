<properties
	pageTitle="從 Azure 儲存體來回移動資料 | Microsoft Azure"
	description="這篇文章提供從 Azure 儲存體來回移動資料之不同方法的概觀。"
	services="storage"
	documentationCenter=""
	authors="micurd"
	manager="jahogg"
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/18/2016"
	ms.author="micurd"/>

# 從 Azure 儲存體來回移動資料

如果您想要將內部部署資料移至 Azure 儲存體 (或相反)，有各種不同的方法可以辦到。最適合的方法將取決於您的案例。本文將提供不同案例的快速概觀和每個案例的適當供應項目。

## 建置應用程式

如果您要建置應用程式，針對 REST API 或許多用戶端程式庫進行開發是從 Azure 儲存體來回移動資料的好方法。

Azure 儲存體提供 .NET、iOS、Java、Android、通用 Windows 平台 (UWP)、Xamarin、c++、Node.JS、PHP、Ruby 和 Python 等豐富的用戶端程式庫。這些用戶端程式庫提供多種進階功能，例如大重試邏輯、記錄與並行上傳等等。您也可以直接透過 REST API 開發，它可以透過提出 HTTP/HTTPS 要求的任何語言進行呼叫。

若要深入了解，請參閱[開始使用 Azure Blob 儲存體](storage-dotnet-how-to-use-blobs.md)。

## 快速檢視/和資料互動

如果您想要輕鬆地檢視 Azure 儲存體資料，同時有上傳和下載資料的能力，請考慮使用 Azure 儲存體總管。

若要深入了解，請參閱我們的 [Azure 儲存體總管](storage-explorers.md)清單。

## 系統管理

如果您需要或比較熟悉命令列公用程式 (例如系統管理員)，以下是您應考慮的幾個選項︰

### AzCopy

AzCopy 為 Windows 命令列公用程式，可以極高效能將資料複製到 Azure 儲存體，以及從 Azure 儲存體複製資料。您也可以複製儲存體帳戶內或不同儲存體帳戶之間的資料。

若要深入了解，請參閱[使用 AzCopy 命令列公用程式傳輸資料](storage-use-azcopy.md)。

### Azure PowerShell

Azure PowerShell 模組提供 Cmdlet 讓您用於管理 Azure 上的服務。此模組為工作型的命令列 Shell 與指令碼語言，專為系統管理所設計。

若要深入了解，請參閱[搭配使用 Azure PowerShell 與 Azure 儲存體](storage-powershell-guide-full.md)

### Azure CLI

Azure CLI 提供您一組開放原始碼的跨平台命令，供您使用 Azure 服務。Azure CLI 可在 Windows、OSX 和 Linux 上使用。

若要深入了解，請閱讀[使用 Azure CLI 搭配 Azure 儲存體](storage-azure-cli.md)。

## 以較慢的網路移動大量資料

與移動大量資料相關聯的最大挑戰之一是傳輸時間。如果您想要從 Azure 儲存體來回取得資料，而不需擔心網路成本或撰寫程式碼，Azure 匯入/匯出會是適當的解決方案。

若要深入了解，請參閱 [Azure 匯入/匯出](storage-import-export-service.md)。

## 備份您的資料

如果您只需要將資料備份到 Azure 儲存體，Azure 備份是最好的選擇。對於備份內部部署資料和 Azure VM，這是功能強大的解決方案。

若要深入了解，請參閱 [Azure 備份](../backup/backup-introduction-to-azure-backup.md)。

## 存取內部部署資料和雲端的資料

如果您需要解決方案來存取內部部署資料和雲端的資料，您應該考慮使用 Azure 的混合式雲端儲存體解決方案，StorSimple。此解決方案包含實體 StorSimple 裝置，以智慧方式將經常使用的資料儲存在 SSD 上，將偶爾使用的資料儲存在 HDD 上，並且將非作用中/備份/封存資料儲存在 Azure 儲存體上。

若要深入了解，請參閱 [StorSimple](../storsimple/storsimple-overview.md)。

## 復原您的資料

當您擁有內部部署工作負載和應用程式，您需要可讓您在發生災害事件時繼續執行業務的解決方案。Azure Site Recovery 可處理虛擬機器和實體伺服器的複寫、容錯移轉及復原作業。複寫的資料會儲存在 Azure 儲存體，讓您不需要次要本地資料中心。

若要深入了解，請參閱 [Azure Site Recovery](../site-recovery/site-recovery-overview.md)。

<!---HONumber=AcomDC_0323_2016-->