<properties
   pageTitle="建立 Marketplace 供應項目所需的各種入口網站概觀 | Microsoft Azure"
   description="建立 Marketplace 供應項目所需的各種入口網站概觀"
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/05/2015"
   ms.author="hascipio" />


# 您需要的入口網站
在您開始處理程序之前，讓我們簡介發佈供應項目所需的各種入口網站。以下是有關入口網站的簡短摘要 - **賣方儀表板、發佈入口網站**和**Azure Preview 入口網站** (依您將與之互動的順序顯示)。
## 賣方儀表板
[https://sellerdashboard.microsoft.com](https://sellerdashboard.microsoft.com)
### 說明
[賣方儀表板] 帳戶只需建立一次。嘗試建立賣方儀表板帳戶之前，合作夥伴應該檢查公司是否確實未曾擁有賣方儀表板帳戶。在這個程序期間，我們會收集銀行帳戶資訊、稅務資訊和公司地址資訊。

> [AZURE.NOTE]如果您只要發佈免費的供應項目 (或自備授權)，我們不需要稅捐和銀行資訊。

### 使用的身分識別/帳戶
理想的情況下是通訊群組清單或安全性群組 (例如 azurepublishing@partnercompany.com)。此通訊群組清單或安全性群組**必須**已註冊為 Microsoft 帳戶。

> [AZURE.TIP]建議使用通訊群組清單或安全性群組，因為它會移除任何個別帳戶的相依性；雖然也會使用個別帳戶。

## 發佈入口網站
[https://publish.windowsazure.com](https://publish.windowsazure.com)

### 說明
可供合作夥伴處理其供應項目並加以發佈 (行銷、價格、發佈憑證等) 的入口網站。

### 使用的身分識別/帳戶
上述 [通訊群組清單] 或 [安全性群組] 第一次必須用於登入發佈入口網站，之後其他使用者才可以新增為共同管理員。這就是對應至賣方儀表板註冊資料的方式。

## Azure Preview 入口網站
[https://ms.portal.azure.com](https://ms.portal.azure.com)
### 說明
在此入口網站中，合作夥伴可以在 Marketplace 中檢視其預備與發佈的供應項目 (適用於 VM、解決方案範本和 ARM 架構開發人員服務)
### 使用的身分識別/帳戶
從發佈入口網站預備供應項目時，必須將訂用帳戶識別碼列入允許清單。登入此入口網站來測試預備的供應項目時，必須使用相同的訂用帳戶 [沒有相關聯的使用者名稱和密碼]。

## 另請參閱
- [使用者入門：如何將供應項目發佈至 Azure Marketplace](marketplace-publishing-getting-started.md)

<!---HONumber=Nov15_HO3-->