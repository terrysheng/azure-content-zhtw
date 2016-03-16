<properties
    pageTitle="在 Azure RemoteApp 中使用 Outlook | Microsoft Azure" 
    description="了解如何在 Azure RemoteApp 中設定和使用 Outlook | Microsoft Azure"
    services="remoteapp"
    documentationCenter=""
    authors="pavithir"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="02/25/2016"
    ms.author="elizapo" />

# 在 Azure RemoteApp 中使用 Microsoft Outlook

Azure RemoteApp 支援 Microsoft Outlook O365。深入了解 [Office 在 Azure RemoteApp 中運作](remoteapp-officesubscription.md)的方式。在 Azure RemoteApp 中使用 Outlook 時，有幾個建議的設定。

## 快取模式
在 Azure RemoteApp 中使用 Outlook 時，快取模式是建議的組態。當您設定 Outlook 2013 帳戶以使用「快取 Exchange 模式」時，Outlook 2013 會從使用者的 Microsoft Exchange 信箱的本機副本運作，它與離線通訊錄 (OAB) 一起儲存在使用者電腦上的離線資料檔案 (OST 檔案)。快取信箱和 OAB 會定期從 O365 服務更新。深入了解[快取和線上模式的差異](https://technet.microsoft.com/library/jj683103.aspx)。

使用者可以在帳戶設定期間選取 [快取 Exchange 模式] 或 [線上模式]，或變更帳戶設定。您也可以使用 Office 自訂工具 (OCT) 或群組原則來部署一個模式或其他模式。

閱讀[啟用快取模式的逐步指示](https://technet.microsoft.com/library/c6f4cad9-c918-420e-bab3-8b49e1885034#proc)。

## 搜尋
在 Azure RemoteApp 中，使用 Outlook 中的搜尋有限制。Azure RemoteApp 會使用集區的 VM，以配合使用者工作階段。搜尋索引取決於電腦識別碼，不同 VM 的識別碼有差異。每次使用者登入 Azure RemoteApp 時，有可能被導向至新的 VM。這表示，如果我們啟用本機搜尋，每次電腦識別碼變更 (當使用者在不同的 VM 上) 時，將會執行索引子。視 .OST 檔案的大小而定，索引子可能需要很長的時間完成，並且用盡其他應用程式所需的資源。搜尋不只很慢，而且可能不會產生結果。解決此問題的方式是預設啟用線上搜尋。不幸的是，在 Outlook 2013 中無法停用已編製索引/本機搜尋，且預設無法啟用線上搜尋。

Outlook 2016 有解決方案可以解決這個問題，方法是為 Exchange 2016 上裝載 (或裝載在 Office 365) 上的信箱提供新的線上搜尋體驗。這會對本機快取 (OST) 使用伺服器搜尋結果。在某些案例中，Outlook 可能會改回使用搜尋索引子，但是大部分的搜尋會使用線上模式。如果郵件搜尋是非常重要的案例，則 Azure RemoteApp 的建議是使用 Outlook 2016。

<!---HONumber=AcomDC_0302_2016-->