<properties 
   pageTitle="管理 Azure 自動化資料 | Microsoft Azure"
   description="本文章包含用於管理 Azure 自動化環境的多個主題。目前將資料保留和備份 Azure 自動化災害復原併入 Azure 自動化中。"
   services="automation"
   documentationCenter=""
   authors="SnehaGunda"
   manager="stevenka"
   editor="tysonn" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/03/2016"
   ms.author="bwren;sngun" />

# 管理 Azure 自動化資料

本文章包含用於管理 Azure 自動化環境的多個主題。

## 資料保留

當您刪除 Azure 自動化中的資源時，會將它保留 90 天作為稽核用途，之後才永久移除。在這段期間您無法看到或使用該資源。此原則也適用於屬於已刪除的自動化帳戶的資源。

Azure 自動化會自動刪除並永久移除超過 90 天的工作。

下表摘要說明不同的資源的保留原則。

|資料|原則|
|:---|:---|
|帳戶|刪除使用者帳戶 90 天後永久移除。|
|Assets|使用者刪除資產後 90 天，或使用者刪除持有資產的帳戶 90 天後永久移除。|
|模組|使用者刪除模組後 90 天，或使用者刪除持有模組的帳戶 90 天後永久移除。|
|Runbook|使用者刪除資源後 90 天，或使用者刪除持有資源的帳戶 90 天後永久移除。|
|作業|在上次修改日期的 90 天後刪除並永久移除。這可以是在工作完成、停止或暫止之後。|
|節點組態/MOF 檔案| 舊的節點組態會在新的節點組態產生之後的 90 天永久移除。|
|DSC 節點| 使用 Azure 入口網站或在 Windows PowerShell Cmdlet 中使用 [Unregister-AzureRMAutomationDscNode](https://msdn.microsoft.com/library/mt603500.aspx) 在節點從自動化帳戶取消註冊之後的 90 天永久移除。節點也會在擁有節點的帳戶被使用者刪除之後的 90 天永久移除。 |
|節點報告| 該節點產生新的報告之後的 90 天永久移除|

保留原則適用於所有使用者，而且目前無法自訂。

## 備份 Azure 自動化

在 Microsoft Azure 中刪除自動化帳戶時，會刪除帳戶中的所有物件，包括 Runbook、模組、組態、設定、工作和資產。刪除帳戶之後，就無法復原物件。您可以使用下列資訊，在刪除之前備份您的自動化帳戶的內容。

### Runbook

您可以使用 Azure 管理入口網站或 Windows PowerShell 中的 [Get-AzureAutomationRunbookDefinition](https://msdn.microsoft.com/library/dn690269.aspx) Cmdlet，將您的 Runbook 匯出為指令碼檔案。可以將這些指令碼檔案匯入到另一個自動化帳戶，如[建立或匯入 Runbook](https://msdn.microsoft.com/library/dn643637.aspx) 中所述。


### 整合模組

您無法從 Azure 自動化匯出整合模組。您必須確定它們可供在自動化帳戶外部使用。

### Assets

您無法從 Azure 自動化匯出 [資產](https://msdn.microsoft.com/library/dn939988.aspx)。使用 Azure 管理入口網站，您必須記下變數、認證、憑證、連接和排程的詳細資料。然後必須手動建立您匯入到另一個自動化的 Runbook 所使用的任何資產。

您可以使用 [Azure Cmdlet](https://msdn.microsoft.com/library/dn690262.aspx) 來擷取未加密的資產的詳細資料並加以儲存供日後參考，或在另一個自動化帳戶中建立對等的資產。

您無法使用 Cmdlet 擷取加密的變數的值或認證的密碼欄位。如果您不知道這些值，則可以從 Runbook 使用 [Get-AutomationVariable](https://msdn.microsoft.com/library/dn940012.aspx) 和 [Get-AutomationPSCredential](https://msdn.microsoft.com/library/dn940015.aspx) 活動來擷取它們。

您無法從 Azure 自動化匯出憑證。您必須確定 Azure 外部有任何憑證可供使用。

### DSC 組態

您可以使用 Azure 管理入口網站或 Windows PowerShell 中的 [Export-AzureRmAutomationDscConfiguration](https://msdn.microsoft.com/library/mt603485.aspx) Cmdlet，將您的組態匯出為指令碼檔案。這些組態可以匯入並用於另一個自動化帳戶中。


##Azure 自動化中的異地複寫

異地複寫是 Azure 自動化帳戶中的標準功能，可將帳戶資料備份到其他地理區域做為備援。您可以在設定帳戶時選擇主要區域，然後就會自動指派次要區域給帳戶。從主要區域複製到次要區域的資料會持續更新，以防止資料遺失。

下表顯示可用的主要和次要區域配對：

|主要 |次要
| ---------------   |----------------
|美國中南部 |美國中北部
|美國東部 2 |美國中部
|西歐 |北歐
|東南亞 |東亞
|日本東部 |日本西部

萬一主區域資料遺失，Microsoft 會嘗試將它復原。如果主要資料無法復原，則會執行異地容錯移轉，而且將透過受影響客戶的訂用帳戶將此情況通知他們。

<!---HONumber=AcomDC_0204_2016-->