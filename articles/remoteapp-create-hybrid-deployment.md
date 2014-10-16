<properties title="How to create a hybrid deployment of RemoteApp" pageTitle="How to create a hybrid deployment of RemoteApp" description="Learn how to create a deployment of RemoteApp that connects to your internal network." metaKeywords="" services="" solutions="" documentationCenter="" authors="elizapo"  />

<tags ms.service="remoteapp" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="elizapo"></tags>

# 如何建立 RemoteApp 的混合式部署

RemoteApp 部署分成兩種：

-   雲端：完全在 Azure 之中，可使用 Azure 管理入口網站中的 [快速建立] 選項建立。
-   混合式：包含內部部署存取所需的虛擬網路，需使用管理入口網站中的 [Create with VPN] 選項建立。

本教學課程將逐步引導您完成建立混合式部署的程序。共有七個步驟：

1.  建立 RemoteApp 範本映像。
2.  建立 RemoteApp 服務。
3.  連結至虛擬網路。
4.  連結範本映像。
5.  設定目錄同步處理。必須執行此步驟，RemoteApp 才能將使用者、群組、連絡人和密碼從您的內部部署 Active Directory 同步處理至 Azure Active Directory 租用戶。
6.  發佈 RemoteApp 程式。
7.  設定使用者存取。

**開始之前**

在建立服務之前，您必須執行下列作業：

-   安裝[必要更新][]以改善 Azure RemoteApp 的效能。
-   註冊 [RemoteApp 預覽版][]。
-   在 Active Directory 中建立使用者帳戶，以做為 RemoteApp 服務帳戶。限制此帳戶的權限，使其只能將機器加入網域中。
-   收集內部部署網路的相關資訊：IP 位址資訊和 VPN 裝置詳細資料。
-   安裝 [Azure PowerShell][] 模組。
-   收集您要為其授與存取權之使用者和群組的相關資訊。這可以是使用者或群組的 Microsoft 帳戶資訊或 Active Directory 組織帳戶資訊。

## **步驟 1：建立範本映像**

Azure RemoteApp 會使用 Windows Server 2012 R2 範本映像來主控您要與使用者共用的所有程式。若要建立自訂 RemoteApp 範本映像，您可以從現有的映像建立，或是建立新映像。可上傳用於 Azure RemoteApp 的映像有下列需求：

-   必須在 VHD 檔案上 (VHDX 檔案目前不受支援)。
-   VHD 可以固定大小或動態擴充。建議採用動態擴充 VHD 的做法，因為這會比固定大小 VHD 檔案更快速地上傳至 Azure。
-   磁碟必須使用主開機記錄 (MBR) 分割樣式進行初始化。GUID 磁碟分割資料表 (GPT) 磁碟分割樣式不受支援。
-   VHD 必須包含單一 Windows Server 2012 R2 安裝。它可包含多個磁碟區，但只有其中一個包含 Windows 安裝。
-   必須安裝「遠端桌面工作階段主機 (RDSH)」角色和「桌面體驗」功能。
-   必須停用「加密檔案系統 (EFS)」。
-   映像必須使用參數 **/oobe /generalize /shutdown** 進行 SYSPREP 處理 (請不要使用 **/mode:vm** 參數)。

若要從頭建立新的範本映像：

1.  找出 Windows Server 2012 R2 DVD 或 ISO 映像。
2.  建立 VHD 檔案。
3.  安裝 Windows Server 2012 R2。
4.  安裝「遠端桌面工作階段主機 (RDSH)」角色和「桌面體驗」功能。
5.  安裝您的應用程式所需的其他功能。
6.  安裝及設定您的應用程式。
7.  執行您的應用程式所需的任何其他 Windows 設定。
8.  停用「加密檔案系統 (EFS)」。
9.  進行映像的 SYSPREP 處理。

建立新映像的詳細步驟為：

1.  找出 Windows Server 2012 R2 DVD 或 ISO 映像。
2.  使用「磁碟管理」建立 VHD 檔案。

    1.  啟動「磁碟管理」(diskmgmt.msc)。
    2.  建立會動態擴充、大小 40 GB 或更大的 VHD。(請估計 Windows、您的應用程式和自訂所需的空間量。已安裝 RDSH 角色和「桌面體驗」功能的 Windows Server 將需要約 10 GB 的空間)。

        1.  按一下 [動作 \> 建立 VHD]。
        2.  指定位置、大小和 VHD 格式。選取 [動態擴充]，然後按一下 [確定]。

            此作業會執行數秒。VHD 建立完成後，您會在 [磁碟管理] 主控台中看見不具任何磁碟機代號、狀態為「未初始化」的新磁碟。

        -   以滑鼠右鍵按一下磁碟 (不是未配置的空間)，然後按一下 [初始化磁碟]。選取 \[MBR] (主開機記錄) 做為磁碟分割樣式，然後按一下 [確定]。
        -   建立新的磁碟區：以滑鼠右鍵按一下未配置的空間，然後按一下 [新增簡單磁碟區]。您可以接受精靈中的預設值，但請務必指派磁碟機代號，以避免在上傳範本映像時發生問題。
        -   以滑鼠右鍵按一下磁碟，然後按一下 [Detach VHD]。

3.  安裝 Windows Server 2012 R2：

    1.  建立新的虛擬機器。在 [Hyper-V 管理員] 或 [用戶端 Hyper-V] 中使用 [新增虛擬機器精靈]。

        1.  在 [連接虛擬硬碟] 頁面上選取 [使用現有的虛擬硬碟]，然後瀏覽至您在上一個步驟中建立的 VHD。
        2.  在 [安裝選項] 頁面上，選取 [從開機 CD/DVD\_ROM 安裝作業系統]，然後選取您的 Windows Server 2012 R2 安裝媒體的位置。
        3.  在精靈中選擇其他安裝 Windows 和應用程式所需的選項。完成精靈。

    2.  精靈完成後，請編輯 VM 的設定並進行安裝 Windows 和程式所需的任何其他變更 (例如虛擬處理器數目)，然後按一下 [確定]。
    3.  連接到 VM 並安裝 Windows Server 2012 R2。

4.  安裝「遠端桌面工作階段主機 (RDSH)」角色和「桌面體驗」功能：

    1.  啟動伺服器管理員。
    2.  在 [歡迎使用] 畫面或 [管理] 功能表上，按一下 [新增角色和功能]。
    3.  在 [開始之前] 頁面上，按 [下一步]。
    4.  選取 [角色型或功能型安裝]，然後按 [下一步]。
    5.  從清單中選取本機機器，然後按 [下一步]。
    6.  選取 [遠端桌面服務]，然後按 [下一步]。
    7.  展開 [使用者介面與基礎結構]，然後選取 [桌面體驗]。
    8.  按一下 [新增功能]，然後按 [下一步]。
    9.  在 [遠端桌面服務] 頁面上，按 [下一步]。
    10. 按一下 [遠端桌面工作階段主機]。
    11. 按一下 [新增功能]，然後按 [下一步]。
    12. 在 [確認安裝選項] 頁面上，選取 [必要時自動重新啟動目的地伺服器]，然後在出現重新啟動警告時按一下 [是]。
    13. 按一下 [Install]。電腦會重新啟動。

5.  安裝您的應用程式所需的其他功能，例如 .NET Framework 3.5。若要安裝這些功能，請執行 [新增角色和功能] 精靈。
6.  安裝並設定您要透過 RemoteApp 發佈的程式和應用程式。

    **重要事項**：Microsoft 建議您在安裝應用程式之前先安裝 RDSH 角色，以確保可在映像上傳至 RemoteApp 之前發現任何關於應用程式相容性的問題。

7.  執行您的應用程式所需的任何其他 Windows 設定。
8.  停用「加密檔案系統 (EFS)」。在提高權限的命令視窗上執行下列命令：

        Fsutil behavior set disableencryption 1

    或者，您可以在登錄中設定或新增下列 DWORD 值：

        HKLM\System\CurrentControlSet\Control\FileSystem\NtfsDisableEncryption = 1

9.  如果您要在 [Azure 虛擬機器] 內建置映像，您必須刪除或重新命名 **\\Windows\\Panther\\Unattend.xml** 檔案，因為此檔案會使後續使用的上傳指令碼無法運作。
10. 進行映像的 SYSPREP 處理。在提高權限的命令提示字元上執行下列命令：

    **C:\\Windows\\System32\\sysprep\\sysprep.exe /generalize /oobe /shutdown**

    **注意：**請不要使用 SYSPREP 命令的 **/mode:vm** 參數，即使這是虛擬機器亦然。

## **步驟 2：建立 RemoteApp 服務**

1.  在 [Azure 管理入口網站][]中，移至 RemoteApp 頁面。
2.  按一下 [新增] \> [Create with VPN]。
3.  輸入服務的名稱，然後按一下 [建立 RemoteApp 服務]。

在您的 RemoteApp 服務建立後，請移至 RemoteApp [快速入門] 頁面，繼續進行安裝步驟。

## **步驟 3：連結至虛擬網路**

虛擬網路可讓您的使用者透過 RemoteApp 遠端資源存取您本機網路上的資料。虛擬網路連結的設定分成四個步驟：

1.  在 [快速入門] 頁面上，按一下 [連結 remoteapp 虛擬網路]。
2.  選擇要建立新的虛擬網路還是使用現有網路。在本教學課程中，我們將建立新網路。
3.  提供新網路的下列資訊：
    -   名稱
    -   虛擬網路位址空間
    -   本機位址空間
    -   DNS 伺服器 IP 位址
    -   VPN IP 位址

    如需詳細資訊，請參閱[使用管理入口網站設定站對站 VPN][]。

4.  接著，回到 [快速入門] 頁面，按一下 [get script] 以下載指令碼，將您的 VPN 裝置設定成連接到您剛剛建立的虛擬網路。您將需要下列關於 VPN 裝置的資訊：
    -   廠商
    -   平台
    -   作業系統

    儲存指令碼，並在 VPN 裝置上加以執行。

    **注意：**在您執行此指令碼後，虛擬網路將會進入 [Ready] 狀態 - 這需要 5-7 分鐘的時間。在網路就緒之前，您無法加以使用。

5.  最後，同樣在 [快速入門] 頁面上，按一下 [加入本機網域]。將 RemoteApp 服務帳戶新增至您的本機 Active Directory 網域。您將需要網域名稱、組織單位、服務帳戶的使用者名稱和密碼。

## **步驟 4：連結至 RemoteApp 範本映像**

RemoteApp 範本映像包含您要與使用者共用的程式。您可以上傳您在步驟 1 中建立的新範例映像，或連結至現有映像 (已上傳至 Azure 的映像)。

如果您要上傳新映像，您必須輸入名稱，並選擇映像的位置。在精靈的下一頁，您會看見一組 PowerShell Cmdlet - 從提高權限的 Azure PowerShell 提示複製並執行這些 Cmdlet，可上傳指定的映像。

如果您要連結至現有的範本映像，請直接指定映像名稱、位置和相關聯的 Azure 訂閱。

## **步驟 5：設定 Active Directory 目錄同步處理**

必須在 Azure Active Directory 與您的內部部署 Active Directory 之間進行目錄同步處理，RemoteApp 才能將使用者、群組、連絡人和密碼同步處理至您的 Azure Active Directory 租用戶。如需規劃資訊和詳細步驟，請參閱[目錄同步處理藍圖][]。

## **步驟 6：發佈 RemoteApp 程式**

RemoteApp 程式是您提供給使用者的應用程式或程式。此程式位於您為服務上傳的範本映像中。當使用者存取 RemoteApp 程式時，此程式看似會在其本機環境中執行，但實際上是執行於 Azure 中。

您必須先將 RemoteApp 程式發佈至使用者摘要，您的使用者才能存取這些程式；使用者摘要是您的使用者可透過 Azure 入口網站存取的程式清單。

您可以將多個程式發佈至 RemoteApp 服務。在 RemoteApp 程式頁面中按一下 [發佈]，可新增程式。您可以從範本映像的 [開始] 功能表發佈，或藉由為程式指定範本映像的路徑來發佈。如果您選擇從 [開始] 功能表新增，請選擇要發佈的程式。如果您選擇提供程式的路徑，請提供程式的名稱，以及程式在範本映像上的安裝路徑。

## **步驟 7：設定使用者存取**

在您建立 RemoteApp 服務之後，現在您必須新增要能夠使用遠端資源的使用者和群組。您要為其提供存取權的使用者或群組，必須存在於與您用來建立此 RemoteApp 服務的訂閱相關聯的 Active Directory 租用戶中。

1.  在 [快速入門] 頁面上，按一下 [Configure user access]。
2.  輸入組織帳戶或群組名稱 (來自於 Active Directory)，或是您要為其授與存取權的 Microsoft 帳戶。

    對於使用者，請確實使用 “<user@domain.com>”格式。對於群組，請輸入群組名稱。

3.  在驗證使用者或群組後，請按一下 [儲存]。

## 後續步驟

至此，您已成功建立並部署 RemoteApp 混合式部署。下一個步驟是要讓使用者下載並安裝遠端桌面用戶端。您可以在 RemoteApp 的 [快速入門] 頁面上找到用戶端的 URL。接著，請讓使用者登入 Azure，並存取您所發佈的 RemoteApp 程式。

  [必要更新]: http://support.microsoft.com/kb/2977219
  [RemoteApp 預覽版]: http://azure.microsoft.com/en-us/services/remoteapp/
  [Azure PowerShell]: http://azure.microsoft.com/zh-tw/documentation/articles/install-configure-powershell/
  [Azure 管理入口網站]: http://manage.windowsazure.com
  [使用管理入口網站設定站對站 VPN]: http://msdn.microsoft.com/library/azure/dn133795.aspx
  [目錄同步處理藍圖]: http://msdn.microsoft.com//library/azure/hh967642.aspx
