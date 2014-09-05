<properties linkid="manage-windows-howto-capture-an-image" urlDisplayName="Capture an image" pageTitle="Capture an image of a virtual machine running Windows Server" metaKeywords="Azure capture image vm, capturing vm" description="Learn how to capture an image of an Azure virtual machine (VM) running Windows Server 2008 R2. " metaCanonical="" services="virtual-machines" documentationCenter="" title="How to Capture an Image of a Virtual Machine Running Windows Server" authors="kathydav" solutions="" manager="jeffreyg" editor="tysonn" />

如何對執行 Windows Server 的虛擬機器擷取映像
============================================

您可以使用映像庫中的映像輕鬆建立虛擬機器，或是可以擷取並使用自己的映像來建立自訂的虛擬機器。映像是虛擬硬碟 (.vhd) 檔案，用來作為建立虛擬機器的範本。映像只是範本，並沒有像設定的虛擬機器一樣有特定設定，例如電腦名稱及使用者帳戶設定。如果您想要建立多部以相同方式設定的虛擬機器，可以對已設定的虛擬機器擷取映像，並使用該映像作為範本。

1.  使用[如何登入執行 Windows Server 的虛擬機器](http://www.windowsazure.com/zh-tw/manage/windows/how-to-guides/log-on-a-windows-vm/) (英文) 中列出的步驟，連線至虛擬機器。

2.  以系統管理員身分開啟 [命令提示字元] 視窗。

3.  切換至 `%windir%\system32\sysprep` 目錄，然後執行 sysprep.exe。

4.  **[系統準備工具]** 對話方塊隨即出現。

    在 **[系統清理動作]** 中選取 **[進入系統全新體驗 (OOBE)]**，並確定已核取 **[一般化]**。如需使用 Sysprep 的詳細資訊，請參閱[如何使用 Sysprep：簡介](http://technet.microsoft.com/en-us/library/bb457073.aspx) (英文)。

5.  在 **[關機選項]** 中選取 **[關機]**。

6.  按一下 **[確定]**。

7.  Sysprep 會將虛擬機器關機，這會在[管理入口網站](http://manage.windowsazure.com) 中，將機器的狀態變更為 **[已停止]**。

8.  按一下 **[虛擬機器]**，然後選取要擷取的虛擬機器。

9.  按一下命令列上的 **[擷取]**。

    **[Capture an Image from a Virtual Machine]** 對話方塊隨即出現。

10. 在 **[映像名稱]** 中輸入新映像的名稱。

11. 將 Windows Server 映像新增到自訂映像組合之前，必須先如前述步驗所指示，執行 Sysprep 將它一般化。按一下 **[I have run Sysprep on the virtual machine]**，表示您已這麼做。

12. 按一下核取記號以擷取映像。當您擷取虛擬機器的映像時，會刪除該機器。

    新映像現在提供於 **[映像]** 底下。

    當您使用 **[從組件庫]** 方法建立虛擬機器時，可以使用您擷取的映像，請按一下 **[選擇映像]** 頁面上的 **[My Images]**。


