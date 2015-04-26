<properties title="Azure RemoteApp licensing" pageTitle="Azure RemoteApp 授權" description="了解 Azure RemoteApp 授權" metaKeywords="" services="" solutions="" documentationCenter="" authors="elizapo"  />

<tags ms.service="remoteapp" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/12/2014" ms.author="elizapo" />

#Azure RemoteApp 中的授權如何運作？


您已設定好 Azure RemoteApp 服務和建立範本，準備向使用者發佈應用程式。但還有最後一件事必須解決：授權。要如何處理您透過 RemoteApp 共用之應用程式的授權？

您可提供您擁有授權的任何應用程式或程式。 

身為 Azure RemoteApp 訂用帳戶，您不需購買 Windows 授權和/或遠端桌面 CAL，這些都包含在訂閱成本中。

如果您想使用 Azure RemoteApp 隨附的 Office 365 範本映像，您必須有「現有的」Office 365 ProPlus 方案。而您使用自訂範本發佈的任何 Office 365 應用程式也是如此。您必須以自己的訂用帳戶啟用應用程式。無論試用版與付費訂閱都是如此。如果您想在試用期間使用 Office 365 範本映像，「但您還沒有訂閱」，請移至 Office 365 頁面以[註冊](https://go.microsoft.com/fwlink/p/?LinkID=403802)試用訂閱。如需詳細資訊，請參閱 [RemoteApp 與 Office 如何共同運作？](http://azure.microsoft.com/zh-tw/documentation/articles/remoteapp-o365/)。

如果試用期間內您不想取得 Office 365 試用訂閱，請使用 RemoteApp 隨附的 Office 2013 Professional Plus 範本映像。此範本映像只能使用 30 天，而且無法轉換成付費收藏。在試用期結束後，如果您想繼續使用 Office 2013，您必須刪除收藏並建立一個新收藏。  

這很合理，對吧？您可以發佈您有權共用的任何應用程式。您必須確認您確實有權共用您的程式。

請注意，您不能在雲端收藏中使用 CAL 或大量授權合約。您「可以」在混合式收藏中使用大量授權合約來啟用應用程式 (Office 除外)。您只需從大量授權媒體將它們安裝在範本映像上。請依照應用程式廠商提供的資訊，在遠端桌面環境中安裝授權。

<!--HONumber=35.2-->
