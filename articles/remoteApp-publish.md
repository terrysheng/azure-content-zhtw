<properties title="Publish an app in RemoteApp" pageTitle="在 RemoteApp 中發佈應用程式" description="了解如何在 RemoteApp 中發佈應用程式和資源" metaKeywords="" services="" solutions="" documentationCenter="" authors="elizapo"  />

<tags ms.service="remoteapp" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/11/2014" ms.author="elizapo" />

#如何在 RemoteApp 中發佈應用程式

建立雲端或混合式收藏之後，您必須發佈想供使用者使用的應用程式或資源。隨您的訂閱提供的範本映像預設只會發佈幾個應用程式，若要共用其他應用程式，您必須發佈它們。
 
在入口網站的 [發佈]**** 索引標籤中，按一下 [發佈]****。您可以從您範本映像的 [開始] 功能表新增應用程式，或是在範本映像提供安裝程式的路徑。如果您選擇從 [開始] 功能表新增，請從清單中選擇要發佈的應用程式。如果您選擇提供應用程式的路徑，請輸入應用程式的名稱和應用程式路徑。請在路徑中使用變數，例如 "%systemdrive%" 而非 "c:\"。

**注意：**如果您想從 [開始] 功能表新增您的應用程式，您必須「先將該應用程式新增到範本映像的 [開始] 功能表」，RemoteApp 只會看到 [開始] 功能表上的現有項目，因而您會感到混淆。如果您忘記將應用程式新增到 [開始] 功能表，當您建立範本時，請選擇新增應用程式的路徑 (或重新建立您的範本映像，但這項工作較為繁複)。

<!--HONumber=35.2-->
