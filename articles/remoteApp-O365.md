<properties title="Using Office365 with Azure RemoteApp" pageTitle="透過 Azure RemoteApp 使用 Office 365" description="了解 Office 365 和 Azure RemoteApp 如何搭配使用" metaKeywords="" services="" solutions="" documentationCenter="" authors="elizapo"  />

<tags ms.service="remoteapp" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/11/2014" ms.author="elizapo" />

#透過 Azure RemoteApp 使用 Office 365

在 RemoteApp 中裝載 Office 應用程式有兩個選項：Office 365 ProPlus 或 Office 2013 Professional Plus 試用版。

##Office 365 ProPlus 
您可以使用 Office 365 ProPlus 範本映像建立 RemoteApp 收藏。此選項可讓您將 Office 365 服務延伸至 RemoteApp。您必須有現有的訂閱方案，而且您的使用者必須有 Office 365 ProPlus 服務的授權 (獨立授權或透過 Office 365 服務方案授權)。Office 應用程式的 RemoteApp 執行個體將計算為每個使用者允許的五個安裝執行個體之一，而且將完全啟用。您可以在 [Office 365 管理入口網站](https://portal.office365.com/)中管理使用者的 Office 365 授權。閱讀更多關於 [Office 365 服務方案](http://technet.microsoft.com/library/office-365-plan-options.aspx)的資訊。  

Office 365 ProPlus 選項在 30 天的免費試用期間和實際執行模式中均有提供 ，但在試用版過期後，後者是唯一支援的選項。  

請注意您也可以建立包含 Office 365 ProPlus 的自訂範本映像。若要建構這類範本映像，請遵循 RDS 上關於 Office 365 ProPlus 的[部署步驟](http://technet.microsoft.com/zh-tw/library/dn782858(v=office.15).aspx)。

##Office 2013 Professional Plus 試用版 
在 RemoteApp 30 天的試用版期間，您可以使用 Office 2013 Professional Plus (試用版) 範本映像來建立 RemoteApp 收藏。您可以透過使用者的 Azure Active Directory 工作帳戶或 Microsoft 帳戶，將他們指派至這個試用版收藏。不需再另外訂閱。

這是能親自在 RemoteApp 中體驗和感受 Office 的絕佳選項。不過，此選項僅適用於評估和測試。使用 Office 2013 Professional Plus (試用版) 範本映像建立的 RemoteApp 收藏無法轉換成實際執行模式，在試用期結束後就會停用。

##從試用版切換到實際執行
開始 30 天的免費試用後，入口網站中 RemoteApp 區段的備註會告知您距離轉換為付費帳戶前所剩餘的試用天數。您可以使用這個備註中的連結來啟用您的帳戶並切換成實際執行模式。 

啟用帳戶後，將會影響您帳戶中的所有 RemoteApp 收藏。 

- 透過 Windows Server 2012 R2 或 Office 365 ProPlus 範本映像執行的收藏將順利地轉換成實際執行。所有使用者資料與設定 (包括進行中的工作階段) 都將維持不變。
- 如果您已上傳自訂範本映像，使用這些映像的收藏也將順利轉換。
- Office 2013 Professional Plus (試用版) 範本映像僅適用於評估。透過此範本映像執行的收藏無法轉換成實際執行。它們會成為「停用」狀態。


如果您在試用期過後還未轉換成實際執行模式，將會停用您的 RemoteApp 收藏。別擔心，您的設定和使用者資料會持續儲存 90 天，因此，您仍可以啟用您的服務和切換到實際執行模式，不會遺失任何資料。

<!--HONumber=35.2-->
