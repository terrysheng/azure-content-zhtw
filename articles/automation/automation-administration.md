<properties 
   pageTitle="Azure 自動化管理"
   description="這篇文章包含用於管理 Azure 自動化環境的多個主題。目前包含資料保留和備份 Azure 自動化。"
   services="automation"
   documentationCenter=""
   authors="bwren"
   manager="stevenka"
   editor="tysonn" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/06/2015"
   ms.author="bwren" />

# Azure 自動化管理

這篇文章包含用於管理 Azure 自動化環境的多個主題。

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

保留原則適用於所有使用者，而且目前無法自訂。

## 備份 Azure 自動化

在 Microsoft Azure 中刪除自動化帳戶時，會刪除帳戶中的所有物件，包括 Runbook、模組、設定、工作和資產。刪除帳戶之後，就無法復原物件。您可以使用下列資訊，在刪除之前備份您的自動化帳戶的內容。

### Runbook

您可以使用 Azure 管理入口網站或 Windows PowerShell 中的 [Get-AzureAutomationRunbookDefinition](https://msdn.microsoft.com/library/dn690269.aspx) Cmdlet，將您的 Runbook 匯出為指令碼檔案。可以將這些指令碼檔案匯入到另一個自動化帳戶，如[建立或匯入 Runbook](https://msdn.microsoft.com/library/dn643637.aspx) 中所述。


### 整合模組

您無法從 Azure 自動化匯出整合模組。您必須確定它們可供在自動化帳戶外部使用。

### Assets

您無法從 Azure 自動化匯出 [資產](https://msdn.microsoft.com/library/dn939988.aspx)。使用 Azure 管理入口網站，您必須記下變數、認證、憑證、連接和排程的詳細資料。然後必須手動建立您匯入到另一個自動化的 Runbook 所使用的任何資產。

您可以使用 [Azure Cmdlet](https://msdn.microsoft.com/library/dn690262.aspx) 來擷取未加密的資產的詳細資料並加以儲存供日後參考，或在另一個自動化帳戶中建立對等的資產。

您無法使用 Cmdlet 擷取加密的變數的值或認證的密碼欄位。如果您不知道這些值，則可以從 Runbook 使用 [Get-AutomationVariable](https://msdn.microsoft.com/library/dn940012.aspx) 和 [Get-AutomationPSCredential](https://msdn.microsoft.com/library/dn940015.aspx) 活動來擷取它們。

您無法從 Azure 自動化匯出憑證。您必須確定 Azure 外部有任何憑證可供使用。

<!---HONumber=July15_HO4-->