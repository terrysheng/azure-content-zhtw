<properties title="How to create a custom template image for RemoteApp" pageTitle="How to create a custom template nimage for RemoteApp" description="Learn how to create a custom template image for RemoteApp. You can use this template with either a hybrid or cloud deployment." metaKeywords="" services="" solutions="" documentationCenter="" authors="elizapo" manager="kathyw" />

<tags ms.service="remoteapp" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/12/2014" ms.author="elizapo" ms.manager="kathyw" />

# 如何為 RemoteApp 建立自訂範本映像

Azure RemoteApp 會使用 Windows Server 2012 R2 範本映像來主控您要與使用者共用的所有程式。若要建立自訂 RemoteApp 範本映像，您可以從現有的映像建立，或是建立新映像。可上傳用於 Azure RemoteApp 的映像有下列需求：

-   映像大小應為 MB 的倍數。如果您嘗試上傳的映像大小不是正確的倍數，上傳作業會失敗。
-   映像大小必須為 127 GB 或更小。
-   必須在 VHD 檔案上 (VHDX 檔案目前不受支援)。
-   VHD 可以固定大小或動態擴充。建議採用動態擴充 VHD 的做法，因為這會比固定大小 VHD 檔案更快速地上傳至 Azure。
-   磁碟必須使用主開機記錄 (MBR) 分割樣式進行初始化。GUID 磁碟分割資料表 (GPT) 磁碟分割樣式不受支援。
-   VHD 必須包含單一 Windows Server 2012 R2 安裝。它可包含多個磁碟區，但只有其中一個包含 Windows 安裝。
-   必須安裝「遠端桌面工作階段主機 (RDSH)」角色和「桌面體驗」功能。
-   必須停用「加密檔案系統 (EFS)」。
-   映像必須使用參數 **/oobe /generalize /shutdown** 進行 SYSPREP 處理 (請不要使用 **/mode:vm** 參數)。

**開始之前**

在建立服務之前，您必須執行下列作業：

-   註冊 RemoteApp 預覽版。您可以在 [][]<http://azure.microsoft.com/zh-tw/services/remoteapp/></a> 執行此作業。
-   在 Active Directory 中建立使用者帳戶，以做為 RemoteApp 服務帳戶。限制此帳戶的權限，使其只能將機器加入網域中。
-   收集內部部署網路的相關資訊：IP 位址資訊和 VPN 裝置詳細資料。
-   安裝 [Azure PowerShell][Azure PowerShell] 模組。
-   收集您要為其授與存取權之使用者和群組的相關資訊。這可以是使用者或群組的 Microsoft 帳戶資訊或 Active Directory 組織帳戶資訊。

## **建立範本映像**

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

        -   以滑鼠右鍵按一下磁碟 (不是未配置的空間)，然後按一下 [初始化磁碟]。選取 [MBR] (主開機記錄) 做為磁碟分割樣式，然後按一下 [確定]。
        -   建立新的磁碟區：以滑鼠右鍵按一下未配置的空間，然後按一下 [新增簡單磁碟區]。您可以接受精靈中的預設值，但請務必指派磁碟機代號，以避免在上傳範本映像時發生問題。
        -   以滑鼠右鍵按一下磁碟，然後按一下 [Detach VHD]。

3.  安裝 Windows Server 2012 R2：

    1.  建立新的虛擬機器。在 [Hyper-V 管理員] 或 [用戶端 Hyper-V] 中使用 [新增虛擬機器精靈]。
        1.  在 [Specify Generation] 頁面上，選擇 [Generation 1]。
        2.  在 [連接虛擬硬碟] 頁面上選取 [使用現有的虛擬硬碟]，然後瀏覽至您在上一個步驟中建立的 VHD。
        3.  在 [安裝選項] 頁面上，選取 [從開機 CD/DVD\_ROM 安裝作業系統]，然後選取您的 Windows Server 2012 R2 安裝媒體的位置。
        4.  在精靈中選擇其他安裝 Windows 和應用程式所需的選項。完成精靈。
    2.  精靈完成後，請編輯虛擬機器設定並進行安裝 Windows 和程式所需的任何其他變更 (例如虛擬處理器數目)，然後按一下 [確定]。
    3.  連接到虛擬機器，並安裝 Windows Server 2012 R2。
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

    <strong>重要事項：</strong>Microsoft 建議您在安裝應用程式之前先安裝 RDSH 角色，以確保可在映像上傳至 RemoteApp 之前發現任何關於應用程式相容性的問題。

7.  執行您的應用程式所需的任何其他 Windows 設定。
8.  停用「加密檔案系統 (EFS)」。在提高權限的命令視窗上執行下列命令：

        Fsutil behavior set disableencryption 1

    或者，您可以在登錄中設定或新增下列 DWORD 值：

        HKLM\System\CurrentControlSet\Control\FileSystem\NtfsDisableEncryption = 1

9.  如果您是在 Azure 虛擬機器內建立映像，請重新命名 **\\%windir%\\Panther\\Unattend.xml** 檔案，以免上傳指令碼稍後無法正常運作。將此檔案名稱變更為 Unattend.old，以便當您需要回復部署時，仍舊保有這個檔案可用。
10. 進行映像的 SYSPREP 處理。在提高權限的命令提示字元上執行下列命令：

    **C:\\Windows\\System32\\sysprep\\sysprep.exe /generalize /oobe /shutdown**

    **注意：**請不要使用 SYSPREP 命令的 **/mode:vm** 參數，即使這是虛擬機器亦然。

## 後續步驟

現在您的自訂範本映像已經就緒，您需要將該映像上傳至您的 RemoteApp 部署。使用下列文章裡的資訊，建立您的部署：

-   [如何建立 RemoteApp 的混合式部署][如何建立 RemoteApp 的混合式部署]
-   [如何建立 RemoteApp 的雲端部署][如何建立 RemoteApp 的雲端部署]

  []: http://azure.microsoft.com/zh-tw/services/remoteapp/
  [Azure PowerShell]: http://azure.microsoft.com/zh-tw/documentation/articles/install-configure-powershell/
  [如何建立 RemoteApp 的混合式部署]: http://azure.microsoft.com/zh-tw/documentation/articles/remoteapp-create-hybrid-deployment/
  [如何建立 RemoteApp 的雲端部署]: http://azure.microsoft.com/zh-tw/documentation/articles/remoteapp-create-cloud-deployment/
