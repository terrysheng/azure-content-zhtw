<properties title="How to create a hybrid deployment of RemoteApp" pageTitle="如何建立 RemoteApp 的混合式部署" description="了解如何建立連線到內部網路的 RemoteApp 部署。" metaKeywords="" services="" solutions="" documentationCenter="" authors="elizapo" manager="kathyw" />

<tags ms.service="remoteapp" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/12/2014" ms.author="elizapo" ms.manager="kathyw" />

#如何建立 RemoteApp 的混合式部署

RemoteApp 部署分成兩種： 

- 雲端：完全在 Azure 之中，可使用 Azure 管理入口網站中的 [**快速建立**] 選項建立。  
- 混合式：包含內部部署存取所需的虛擬網路，需使用管理入口網站中的 [**Create with VPN**] 選項建立。

本教學課程將逐步引導您完成建立混合式部署的程序。共有七個步驟： 

1.	建立 [RemoteApp 的自訂範本映像](http://azure.microsoft.com/zh-tw/documentation/articles/remoteapp-create-custom-image/)。
2.	建立 RemoteApp 服務。
2.	連結至虛擬網路。
3.	連結範本映像。
4.	設定目錄同步處理。RemoteApp 要求用下列方式與 Azure Active Directory 整合：1) 設定具有 [密碼同步] 選項的 DirSync，或 2) 設定不具 [密碼同步] 選項的 DirSync，但使用同盟至 AD FS 的網域。
5.	發佈 RemoteApp 程式。
6.	設定使用者存取。

**開始之前**

在建立服務之前，您必須執行下列作業：

- 註冊 RemoteApp 預覽版。您可以在 [http://azure.microsoft.com/zh-tw/services/remoteapp/](http://azure.microsoft.com/zh-tw/services/remoteapp/) 執行此作業。
- 在 Active Directory 中建立使用者帳戶，以做為 RemoteApp 服務帳戶。限制此帳戶的權限，使其只能將機器加入網域中。
- 收集內部部署網路的相關資訊：IP 位址資訊和 VPN 裝置詳細資料。
- 安裝 [Azure PowerShell](http://azure.microsoft.com/zh-tw/documentation/articles/install-configure-powershell/) 模組。
- 收集您要為其授與存取權之使用者和群組的相關資訊。這可以是使用者或群組的 Microsoft 帳戶資訊或 Active Directory 工作帳戶資訊。
- 建立範本映像。RemoteApp 範本映像包含您要為使用者發行的應用程式與程式。請參閱[如何建立 RemoteApp 的自訂範本映像](http://azure.microsoft.com/zh-tw/documentation/articles/remoteapp-create-custom-image/)，以取得詳細步驟。 






## **步驟 1：建立 RemoteApp 服務** ##



1. 在 [Azure 管理入口網站](http://manage.windowsazure.com)中，移至 RemoteApp 頁面。
2. 按一下 [**新增 > Create with VPN**]。
3. 輸入服務的名稱，然後按一下 [**建立 RemoteApp 服務**]。

在您的 RemoteApp 服務建立後，請移至 RemoteApp [**快速入門**] 頁面，繼續進行安裝步驟。

## **步驟 2：連結至虛擬網路** ##

虛擬網路可讓您的使用者透過 RemoteApp 遠端資源存取您本機網路上的資料。虛擬網路連結的設定分成四個步驟：

1. 在 [快速入門] 頁面上，按一下 [**連結 remoteapp 虛擬網路**]。 
2. 選擇要建立新的虛擬網路還是使用現有網路。在本教學課程中，我們將建立新網路。
3. 提供新網路的下列資訊：  
      - 名稱
      - 虛擬網路位址空間
      - 本機位址空間
      - DNS 伺服器 IP 位址
      - VPN IP 位址

	如需詳細資訊，請參閱[使用管理入口網站設定站對站 VPN](http://msdn.microsoft.com/library/azure/dn133795.aspx)。

4. 接著，回到 [快速入門] 頁面，按一下 [**取得指令碼**] 以下載指令碼，將您的 VPN 裝置設定成連接到您剛剛建立的虛擬網路。您將需要下列關於 VPN 裝置的資訊： 
	- 廠商
	- 平台
	- 作業系統

	儲存指令碼，並在 VPN 裝置上加以執行。 

	**注意：**在您執行此指令碼後，虛擬網路將會進入 [Ready] 狀態 - 這需要 5-7 分鐘的時間。在網路就緒之前，您無法加以使用。

5. 最後，同樣在 [快速入門] 頁面上，按一下 [**加入本機網域**]。將 RemoteApp 服務帳戶新增至您的本機 Active Directory 網域。您將需要網域名稱、組織單位、服務帳戶的使用者名稱和密碼。


## **步驟 3：連結至 RemoteApp 範本映像** ##

RemoteApp 範本映像包含您要與使用者共用的程式。您可以上傳您建立的新範本映像 (從[如何建立 RemoteApp 的自訂範本映像](http://azure.microsoft.com/zh-tw/documentation/articles/remoteapp-create-custom-image/)中的指示) 或連結至現有的映像 (已上傳至 Azure 的映像)。

如果您要上傳新映像，您必須輸入名稱，並選擇映像的位置。在精靈的下一頁，您會看見一組 PowerShell Cmdlet - 從提高權限的 Windows PowerShell 提示複製並執行這些 Cmdlet，可上傳指定的映像。

如果您要連結至現有的範本映像，請直接指定映像名稱、位置和相關聯的 Azure 訂閱。



## **步驟 4：設定 Active Directory 目錄同步處理** ##

RemoteApp 要求用下列方式與 Azure Active Directory 整合：1) 設定具有 [密碼同步] 選項的 DirSync，或 2) 設定不具 [密碼同步] 選項的 DirSync，但使用同盟至 AD FS 的網域。如需規劃資訊和詳細步驟，請參閱[目錄同步處理藍圖](http://msdn.microsoft.com//library/azure/hh967642.aspx)。

## **步驟 5：發佈 RemoteApp 程式** ##

RemoteApp 程式是您提供給使用者的應用程式或程式。此程式位於您為服務上傳的範本映像中。當使用者存取 RemoteApp 程式時，此程式看似會在其本機環境中執行，但實際上是執行於 Azure 中。 

您必須先將 RemoteApp 程式發佈至使用者摘要，您的使用者才能存取這些程式；使用者摘要是您的使用者可透過 Azure 入口網站存取的程式清單。
 
您可以將多個程式發佈至 RemoteApp 服務。在 RemoteApp 程式頁面中按一下 [**發佈**]，可新增程式。您可以從範本映像的 [開始] 功能表發佈，或藉由為程式指定範本映像的路徑來發佈。如果您選擇從 [開始] 功能表新增，請選擇要發佈的程式。如果您選擇提供程式的路徑，請提供程式的名稱，以及程式在範本映像上的安裝路徑。

## **步驟 6：設定使用者存取** ##

在您建立 RemoteApp 服務之後，現在您必須新增要能夠使用遠端資源的使用者和群組。您要為其提供存取權的使用者或群組，必須存在於與您用來建立此 RemoteApp 服務的訂閱相關聯的 Active Directory 租用戶中。

1.	在 [快速入門] 頁面上，按一下 [**設定使用者存取**]。 
2.	輸入工作帳戶或群組名稱 (來自於 Active Directory)，或是您要為其授與存取權的 Microsoft 帳戶。

	對於使用者，請確實使用 "user@domain.com" 格式。對於群組，請輸入群組名稱。

3.	在驗證使用者或群組後，請按一下 [**儲存**]。


## 後續步驟 ##
至此，您已成功建立並部署 RemoteApp 混合式部署。下一個步驟是要讓使用者下載並安裝遠端桌面用戶端。您可以在 RemoteApp 的 [快速入門] 頁面上找到用戶端的 URL。接著，請讓使用者登入用戶端，並存取您所發佈的 RemoteApp 程式。


