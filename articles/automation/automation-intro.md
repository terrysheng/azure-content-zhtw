<properties
	pageTitle="什麼是 Azure 自動化"
	description="了解 Azure Automation 提供的價值，並取得常見問題的解答，以便您可以開始建立及使用 Runbook。"
	services="automation"
	documentationCenter=""
	authors="bwren"
	manager="stevenka"
	editor=""/>

<tags
	ms.service="automation"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article" 
	ms.date="09/03/2015"
	ms.author="bwren"/>

# 什麼是 Azure 自動化？

Microsoft Azure 自動化可讓使用者將執行於雲端和企業環境中的手動、長時間執行、易發生錯誤且重複性高的工作自動化。本文章提供 Azure 自動化常見問題的簡短解答。您可以參考此文件庫中的其他文件，以取得不同主題的詳細資訊。

## 「Azure 自動化」提供什麼價值？

Azure 自動化可節省時間，並增加您在雲端和企業環境中執行的一般管理工作的可靠性。您可以以 Runbook 的形式實作這些程序，它可執行多個工作而不需人為介入，並且甚至可排程它們以定期間隔自動執行。

## 什麼是 Runbook？

Runbook 是可在 Azure 自動化中執行一些自動程序的一組工作。它可能是簡單的程序 (例如啟動虛擬機器或建立記錄項目)，或您可能會有一個結合其他較小的 Runbook 的複雜 Runbook，用來對多個資源或甚至是多個雲端執行複雜的程序。

例如，您可能有用於佈建新虛擬機器的現有手動程序，其中包含多個步驟，例如：建立虛擬機器、將它連線到網路、為它指派 IP 位址，以及通知使用者機器已備妥。不需手動執行每個步驟，您可以建立會以單一程序的形式執行這所有工作的 Runbook。您會啟動 Runbook、提供所需的資訊，例如虛擬機器名稱、IP 位址和收件者電子郵件，然後在程序自行完成時在一旁休息。


## Runbook 可以自動化什麼？

Azure 自動化中的 Runbook 是基於 PowerShell 工作流程，因此它們會執行 PowerShell 能做的一切功能。如果應用程式或服務具有 API，則 Runbook 處理它。如果您有它適用的 PowerShell 模組，可以將該模組載入到 Azure 自動化，並且在 Runbook 中包含這些 Cmdlet。Azure 自動化 Runbook 會在 Azure 雲端執行，使得它可以存取雲端中的任何資源或可以從雲端存取的外部資源。使用[混合式 Runbook 背景工作](automation-hybrid-runbook-worker.md)，Runbook 可以在您的本機資料中心中執行以管理本機資源。


## 我可以在哪裡取得 Runbook？

[Runbook 資源庫](http://msdn.microsoft.com/library/azure/dn781422.aspx)包含來自 Microsoft 和社群的 Runbook，您可以在您的環境中原樣使用或根據您的用途加以自訂。它們也可做為參考以了解如何建立您自己的 Runbook。您甚至可以將您認為其他使用者可能覺得很有用的自己的 Runbook 貢獻到資源庫中。


## 如何建立我自己的 Runbook？

您可以從頭[建立您自己的 Runbook](http://msdn.microsoft.com/library/azure/dn643637.aspx) 或根據您的需求從 [Runbook 資源庫](http://msdn.microsoft.com/library/azure/dn781422.aspx)修改 Runbook。如果您想要直接使用 PowerShell 程式碼，您可以在 Azure 入口網站中[使用文字編輯器編輯 Runbook](http://msdn.microsoft.com/library/azure/dn879137.aspx) 或離線編輯。如果您想要編輯 Runbook 而不要看到基礎程式碼，則可以在 Azure 預覽入口網站中使用[圖形化編輯器](automation-graphical-authoring-intro.md)。


## Azure 自動化與其他自動化工具如何產生關聯？

[服務管理自動化 (SMA)](http://technet.microsoft.com/library/dn469260.aspx) 旨在自動化私人雲端中的管理工作。它會在您的本機資料中心安裝為 [Windows Azure Pack](http://www.microsoft.com/server-cloud/products/windows-azure-pack/default.aspx) 的元件。SMA 和 Azure 自動化使用基於 Windows PowerShell 工作流程的相同 Runbook 格式，但 SMA 不支援[圖形化 Runbook](automation-graphical-authoring-intro.md)。

[System Center 2012 Orchestrator](http://technet.microsoft.com/library/hh237242.aspx) 旨在內部部署資源的自動化。它會使用與 Azure 自動化和服務管理自動化不同的 Runbook 格式，並具有圖形化介面，用來建立 Runbook，而不需要編寫任何指令碼。其 Runbook 是由來自專門為 Orchestrator 編寫的整合套件的活動組成。

## 我可以在哪裡取得詳細資訊？

您可利用各種資源，深入了解 Azure 自動化，並建立自己的 Runbook。

- **Azure 自動化程式庫**是您目前的所在位置。本文件庫中的文章提供完整的文件，說明如何設定和管理 Azure 自動化，以及撰寫自己的 Runbook。
- [Azure PowerShell Cmdlet](http://msdn.microsoft.com/library/jj156055.aspx) 提供使用 Windows PowerShell 自動執行 Azure 作業的資訊。Runbook 會使用這些 Cmdlet 來處理 Azure 資源。
- [管理部落格](http://azure.microsoft.com/blog/topics/management) 提供來自 Microsoft 的 Azure 自動化最新資訊和其他管理技術。您應該訂閱此部落格，隨時掌握 Azure 自動化團隊的最新消息。
- [自動化論壇](http://go.microsoft.com/fwlink/p/?LinkId=390561)可讓您張貼有關要由 Microsoft 和自動化社群解決的 Azure 自動化問題。

## 可以提供意見嗎？

**請不吝提供意見！** 如果您要尋找 Azure 自動化 Runbook 解決方案或整合模組，請在指令碼中心提出指令碼要求。如果您有關於 Azure 自動化的任何意見或功能要求，請張貼在 [User Voice](http://feedback.windowsazure.com/forums/34192--general-feedback) 上。感謝您！

<!---HONumber=Sept15_HO2-->