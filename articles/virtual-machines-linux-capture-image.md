<properties linkid="manage-linux-howto-capture-an-image" urlDisplayName="Capture an image" pageTitle="Capture an image of a virtual machine running Linux" metaKeywords="Azure Linux vm, Linux vm" description="Learn how to capture an image of an Azure virtual machine (VM) running Linux. " metaCanonical="" services="virtual-machines" documentationCenter="" title="How to Capture an Image of a Virtual Machine Running Linux" authors="kathydav" solutions="" manager="jeffreyg" editor="tysonn" />

如何對執行 Linux 的虛擬機器擷取映像
===================================

您可以使用映像庫中的映像輕鬆建立虛擬機器，也可以擷取並使用自己的映像來建立自訂的虛擬機器。映像是一個虛擬硬碟檔 (.vhd)，當成建立虛擬機器的範本來使用。映像只是個範本，因為其並不像設定好的虛擬機器一樣有特定設定，例如主機名稱和使用者帳戶設定。如果您想建立以相同方式設定的多個虛擬機器，可以對已設定好的虛擬機器擷取映像，然後以該映像作為範本。

1.  使用[如何登入執行 Linux 的虛擬機器](../virtual-machines-linux-how-to-log-on)中列出的步驟，連線至虛擬機器。

2.  在 SSH 視窗中，輸入下列命令，然後輸入您在虛擬機器上建立之帳戶的密碼。請注意，不同版本的 `waagent` 公用程式可能會有稍微不同的輸出：

    `sudo waagent -deprovision`

    ![Deprovision the virtual machine](./media/virtual-machines-linux-capture-image/LinuxDeprovision.png)

3.  輸入 **y** 繼續。

    ![Deprovision of virtual machine successful](./media/virtual-machines-linux-capture-image/LinuxDeprovision2.png)

4.  輸入 **Exit** 關閉 SSH 用戶端。

5.  在[管理入口網站](http://manage.windowsazure.com)中，選取虛擬機器，然後按一下 **[關閉]**。

    ![Shutdown the virtual machine](./media/virtual-machines-linux-capture-image/ShutdownVM.png)

6.  按一下 **[是]**，表示您知道當虛擬機器不在執行時，您將繼續被收取虛擬機器的費用。

7.  當虛擬機器停止時，按一下命令列上的 **[擷取]**。

    ![Capture an image of the virtual machine](./media/virtual-machines-linux-capture-image/CaptureVM.png)

    **[Capture Virtual Machine]** 對話方塊隨即出現。

    ![Enter the details of the capture](./media/virtual-machines-linux-capture-image/CaptureLinux.png)

8.  在 **[映像名稱]** 中，輸入新映像的名稱。

9.  所有 Linux 映像都必須已<em>「取消佈建」</em>，方法是執行加上 `-deprovision` 選項的 `waagent` 命令。按一下 **[I have run the de-provision command on the Virtual Machine]**，表示作業系統已準備好要成為映像。

10. 按一下打勾記號以擷取映像。

    新映像現在會出現在 **[映像]** 下。擷取此映像後，虛擬機器便會遭刪除。

    ![Image capture successful](./media/virtual-machines-linux-capture-image/CaptureSuccess.png)

    當您使用 **[從組件庫]** 方法建立虛擬機器時，按一下 **[Select the virtual machine operating system]** 頁面上的 **[My Images]**，即可使用您擷取的映像。


