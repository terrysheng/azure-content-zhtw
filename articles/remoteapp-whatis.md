<properties title="What is RemoteApp?" pageTitle="What is RemoteApp?" description="Learn about RemoteApp." metaKeywords="" services="" solutions="" documentationCenter="" authors="elizapo"  />

<tags ms.service="remoteapp" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="elizapo"></tags>


# 什麼是 Azure RemoteApp？

RemoteApp 可讓您透過 Azure 從遠端存取的程式，看起來像是在使用者的本機電腦上執行一般。這些程式稱為 RemoteApp 程式。RemoteApp 程式不會在遠端桌面工作階段主機 (RD 工作階段主機) 伺服器的桌面中對使用者顯示，而是與用戶端的桌面整合。RemoteApp 程式執行於本身可調整大小的視窗中，並可在多個監視器之間拖曳，且在工作列中具有其本身的項目。如果使用者在相同的 RD 工作階段主機伺服器上執行多個 RemoteApp 程式，RemoteApp 程式將會共用相同的遠端桌面服務工作階段。

在許多情況下，RemoteApp 都可降低複雜性和管理負荷，其中包括：

-   分公司，其本機 IT 支援和網路頻寬可能都有限。
-   使用者需要從遠端存取程式的情況。
-   企業營運系統 (LOB) 程式的部署，尤其是自訂 LOB 程式。
-   使用者未被指派電腦的環境，例如「公用辦公桌」或「旅館式辦公」工作區。
-   多個程式版本的部署，尤其是在本機上安裝多個版本即會導致衝突時。

不同於傳統的遠端桌面服務，Azure RemoteApp 執行於 Azure 管理入口網站中。您的使用者可透過入口網站存取程式，而您可以透過管理入口網站執行所有的程式和使用者管理工作。

RemoteApp 部署分成兩種：

-   雲端部署會由 Azure 雲端代管，並將所有程式資料儲存在此雲端。
-   混合式部署由 Azure 雲端代管，但會讓使用者存取您的本機網路上儲存的資料。

無論您選擇哪種部署類型，在建立服務後，您即會將要與使用者共用的程式發佈至使用者摘要。這是您的使用者可透過 Azure 入口網站存取的程式清單。請注意，摘要會顯示與您的訂閱相關聯之所有服務中的所有程式。

