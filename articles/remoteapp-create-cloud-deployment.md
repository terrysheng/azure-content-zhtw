<properties title="如何建立 RemoteApp 的雲端部署" pageTitle="如何建立 RemoteApp 的雲端部署" description="了解如何建立將資料儲存在 Azure 雲端中的 RemoteApp 部署。" metaKeywords="" services="" solutions="" documentationCenter="" authors="elizapo" manager="kathyw" />

<tags ms.service="remoteapp" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/05/2014" ms.author="elizapo" ms.manager="kathyw" />

# 如何建立 RemoteApp 的雲端部署

RemoteApp 部署分成兩種：

-   雲端：完全在 Azure 之中，可使用 Azure 管理入口網站中的 [快速建立] 選項建立。
-   混合式：包含內部部署存取所需的虛擬網路，需使用管理入口網站中的 [Create with VPN] 選項建立。

本教學課程將逐步引導您完成建立雲端部署的程序。共有四個步驟：

1.  建立 RemoteApp 服務。
2.  選擇性地設定目錄同步處理。必須執行此步驟，RemoteApp 才能將使用者、群組、連絡人和密碼從您的內部部署 Active Directory 同步處理至 Azure Active Directory 租用戶。
3.  發佈 RemoteApp 程式。
4.  設定使用者存取。

**開始之前**

在建立服務之前，您必須執行下列作業：

-   註冊 RemoteApp 預覽版。您可以在 <http://azure.microsoft.com/zh-tw/services/remoteapp/> 執行此作業。
-   收集您要為其授與存取權之使用者和群組的相關資訊。這可以是使用者或群組的 Microsoft 帳戶資訊或 Active Directory 工作帳戶資訊。
-   此程序假設您將使用您的訂閱隨附的範本映像，或是您已上傳所要使用的範本映像。如果您需要上傳不同的範本映像，您可以從 [範本映像] 頁面執行此作業。只要按一下 [上傳範本映像]，然後遵循精靈中的步驟即可。
-   想提供自訂應用程式或 LOB 程式？請建立新的[自訂範本映像][自訂範本映像]，並在您的雲端部署中加以使用。

## **步驟 1：建立 RemoteApp 服務**

1.  在 [Windows Azure 管理入口網站][Windows Azure 管理入口網站]中，移至 RemoteApp 頁面。
2.  按一下 [新增] \> [快速建立]。

3.  輸入服務的名稱，然後選取區域。
4.  選擇要用來建立此服務的訂閱。
5.  選擇要用於此服務的範本。

    **秘訣：**您的 RemoteApp 訂閱會隨附於包含 Office 2013 程式的範本映像，其中有些程式已發佈 (例如 Word)，有些即將發佈。您也可以建立新的[自訂範本映像][自訂範本映像]，並在您的雲端部署中加以使用。

6.  按一下 [建立 RemoteApp 服務]。

    **重要事項：**佈建服務最多可能需要 30 分鐘。

在您的 RemoteApp 服務建立後，請移至 RemoteApp [快速入門] 頁面，繼續進行安裝步驟。

## **步驟 2：設定 Active Directory 目錄同步處理 (選用)**

如果您要使用 Active Directory，則必須在 Azure Active Directory 與您的內部部署 Active Directory 之間進行目錄同步處理，RemoteApp 才能將使用者、群組、連絡人和密碼同步處理至您的 Azure Active Directory 租用戶。如需規劃資訊和詳細步驟，請參閱[目錄同步處理藍圖][目錄同步處理藍圖]。

## **步驟 3：發佈 RemoteApp 程式**

RemoteApp 程式是您提供給使用者的應用程式或程式。此程式位於您為服務上傳的範本映像中。當使用者存取 RemoteApp 程式時，此程式看似會在其本機環境中執行，但實際上是執行於 Azure 中。

您必須先將 RemoteApp 程式發佈至使用者摘要，您的使用者才能存取這些程式；使用者摘要是您的使用者可透過 Azure 入口網站存取的程式清單。

您可以將多個程式發佈至 RemoteApp 服務。在 RemoteApp 程式頁面中按一下 [發佈]，可新增程式。您可以從範本映像的 [開始] 功能表發佈，或藉由為程式指定範本映像的路徑來發佈。如果您選擇從 [開始] 功能表新增，請選擇要發佈的程式。如果您選擇提供程式的路徑，請提供程式的名稱，以及程式在範本映像上的安裝路徑。

## **步驟 4：設定使用者存取**

在您建立 RemoteApp 服務之後，現在您必須新增要能夠使用遠端資源的使用者和群組。如果您使用 Active Directory，則您要為其提供存取權的使用者或群組，必須存在於與您用來建立此 RemoteApp 服務的訂閱相關聯的 Active Directory 租用戶中。

1.  在 [快速入門] 頁面上，按一下 [Configure user access]。
2.  輸入工作帳戶或群組名稱 (來自於 Active Directory)，或是您要為其授與存取權的 Microsoft 帳戶。

    對於使用者，請確實使用 “user@domain.com” 格式。對於群組，請輸入群組名稱。

3.  在驗證使用者或群組後，請按一下 [儲存]。

## 後續步驟

至此，您已成功建立並部署 RemoteApp 雲端部署。下一個步驟是要讓使用者下載並安裝遠端桌面用戶端。您可以在 RemoteApp 的 [快速入門] 頁面上找到用戶端的 URL。接著，請讓使用者登入用戶端，並存取您所發佈的 RemoteApp 程式。

  [自訂範本映像]: http://azure.microsoft.com/zh-tw/documentation/articles/remoteapp-create-custom-image/
  [Windows Azure 管理入口網站]: http://manage.windowsazure.com
  [目錄同步處理藍圖]: http://msdn.microsoft.com/zh-tw/library/azure/hh967642.aspx
