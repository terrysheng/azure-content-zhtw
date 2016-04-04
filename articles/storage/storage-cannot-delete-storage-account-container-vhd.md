<properties
	pageTitle="針對刪除 Azure 儲存體帳戶、容器或 VHD| Microsoft Azure 進行疑難排解"
	description="針對刪除 Azure 儲存體帳戶、容器或 VHD 進行疑難排解"
	services="storage"
	documentationCenter=""
	authors="genlin"
	manager="felixwu"
	editor=""
	tags="storage"/>

<tags
	ms.service="storage"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/20/2016"
	ms.author="genli"/>

# 針對刪除 Azure 儲存體帳戶、容器或 VHD 進行疑難排解

## 摘要
當您嘗試刪除 [Azure 入口網站](https://portal.azure.com/)或 [Azure 傳統入口網站](https://manage.windowsazure.com/)中的 Azure 儲存體帳戶、容器或 VHD 時，可能會收到錯誤。下列原因可能導致這些問題︰

-	當您刪除 VM 時，磁碟和 VHD 不會自動刪除，這可能就是儲存體帳戶刪除失敗的原因。我們不會刪除磁碟，所以您可以使用磁碟來裝載另一個 VM。

-	磁碟上仍有租用或磁碟上有相關聯的 blob。

若本文中未提及您的 Azure 問題，請前往 [MSDN 及 Stack Overflow 上的 Azure 論壇](https://azure.microsoft.com/support/forums/)。您可以在這些論壇上，或前往 Twitter 的 @AzureSupport 上張貼您的問題。此外，您也可以選取 [Azure 支援](https://azure.microsoft.com/support/options/)網站上的 [取得支援]，來提出 Azure 支援要求。

## 解決方案
若要解決最常見的問題，請嘗試下列方法︰

1. 切換至 [Azure 傳統入口網站](https://manage.windowsazure.com/)。
2. 選取 [虛擬機器] > [磁碟]。

	![disk.png](./media/storage-cannot-delete-storage-account-container-vhd/VMUI.png)

3. 找出與您想要刪除的儲存體帳戶、容器或 VHD 相關聯的磁碟。檢查磁碟的位置，您會發現相關聯的儲存體帳戶、容器和 VHD。

	![location](./media/storage-cannot-delete-storage-account-container-vhd/DiskLocation.png)

4. 確認磁碟上的 [連接到] 欄位上沒有列出 VM，然後再刪除磁碟。

 	> [AZURE.NOTE] 如果磁碟連接到 VM，您將無法刪除它。磁碟會以非同步方式從已刪除的 VM 卸離，在刪除 VM 之後，可能需要數分鐘才能清除欄位。

5. 選取 [虛擬機器] > [映像]，然後刪除與儲存體帳戶、容器或 VHD 相關聯的映像。

接下來，嘗試再次刪除儲存體帳戶、容器或 VHD。

> [AZURE.WARNING] 請務必先備份您想要儲存的任何資料，再刪除帳戶。您無法還原已刪除的儲存體帳戶，也無法擷取刪除之前所包含的任何內容。這也適用於帳戶中的任何資源 - 一旦刪除 VHD、Blob、資料表、佇列或檔案，就是永久刪除。請確定資源並未使用。

## 徵狀

下列章節列出您在嘗試刪除 Azure 儲存體帳戶、容器或 VHD 時可能會收到的常見錯誤︰

### 案例 1 無法刪除儲存體帳戶

當您嘗試藉由瀏覽至 [Azure 入口網站](https://portal.azure.com/)或 [Azure 傳統入口網站](https://manage.windowsazure.com/)中的儲存體帳戶，以刪除您不再使用的儲存體帳戶，然後選取 [刪除] 時，您可能會看到下列錯誤訊息︰

**在 Azure 入口網站中**：

無法刪除儲存體帳戶 <vm-storage-account-name>。無法刪除儲存體帳戶 <vm-storage-account-name>：「儲存體帳戶 <vm-storage-account-name> 有某些作用中的映像及/或磁碟。確認這些映像及/或磁碟已移除之後再刪除此儲存體帳戶。」

**在 Azure 傳統入口網站中**：

儲存體帳戶 <vm-storage-account-name> 有某些作用中的映像及/或磁碟，例如 xxxxxxxxx- xxxxxxxxx-O-209490240936090599。確認這些映像及/或磁碟已移除之後再刪除此儲存體帳戶。

您也可能會看到此錯誤︰

**在 Azure 入口網站中**：

儲存體帳戶 <vm-storage-account-name> 已有 1 個具備作用中映像及/或磁碟成品的容器。請確定這些成品已從映像儲存機制移除之後再刪除此儲存體帳戶。

**在 Azure 傳統入口網站中**：

提交失敗的儲存體帳戶 <vm-storage-account-name> 已有 1 個具備作用中映像及/或磁碟成品的容器。請確定這些成品已從映像儲存機制移除之後再刪除此儲存體帳戶。當您嘗試刪除儲存體帳戶，而且具有與其相關聯的磁碟仍在作用中時，您會看到一則訊息，告訴您有需要刪除的作用中磁碟。

### 案例 2 無法刪除容器

當您嘗試刪除儲存體容器時，您可能會看到下列錯誤︰

無法刪除儲存體容器 <container name>。錯誤：「目前容器上沒有租用，且要求中沒有指定任何租用識別碼。

### 案例 3 無法刪除 VHD

刪除 VM 之後，您會嘗試刪除相關聯 VHD 的 blob 並收到下列訊息︰

無法刪除 blob 'path/XXXXXX-XXXXXX-os-1447379084699.vhd'。錯誤：「目前 blob 上沒有租用，且要求中沒有指定任何租用識別碼。

## 詳細資訊

已保留的 V1 VM 會在 [Azure 入口網站](https://portal.azure.com/)或 [Azure 傳統入口網站](https://manage.windowsazure.com/)中顯示為處於已停止 (已取消配置) 狀態。

**Azure 傳統入口網站**︰

![screenshot1](./media/storage-cannot-delete-storage-account-container-vhd/moreinfo1.png)

**Azure 入口網站**︰

![screenshot2](./media/storage-cannot-delete-storage-account-container-vhd/moreinfo2.png)

「已停止 (已取消配置)」的狀態會釋放計算資源，例如 CPU、記憶體和網路，但磁碟仍然會保留給使用者，使其能在必要時快速地重新建立 VM。這些磁碟都建立在由 Azure 儲存體提供的 VHD 上。儲存體帳戶具有這些 VHD 而磁碟有這些 VHD 上的租用。

## 參考

- [刪除儲存體帳戶](storage-create-storage-account.md#delete-a-storage-account)
- [如何在 Microsoft Azure (PowerShell) 中止 blob 儲存體的鎖定租用](https://gallery.technet.microsoft.com/scriptcenter/How-to-break-the-locked-c2cd6492)

<!---HONumber=AcomDC_0323_2016-->