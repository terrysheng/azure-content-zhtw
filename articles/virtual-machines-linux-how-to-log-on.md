<properties linkid="manage-linux-howto-logon-linux-vm" urlDisplayName="Log on to a VM" pageTitle="Log on to a virtual machine running Linux in Azure" metaKeywords="Azure Linux vm, Linux SSH" description="Learn how to log on to an Azure virtual machine running Linux by using a Secure Shell (SSH) client." metaCanonical="" services="virtual-machines" documentationCenter="" title="How to Log on to a Virtual Machine Running Linux" authors="" solutions="" manager="" editor="" />

如何登入執行 Linux 的虛擬機器
=============================

對於執行 Linux 作業系統的虛擬機器，您可以使用安全殼層 (SSH) 用戶端來登入。

您必須在要用來登入虛擬機器的電腦上安裝 SSH 用戶端。有許多 SSH 用戶端程式可供選擇。下面是一些可能的選項：

-   如果您使用的是執行 Windows 作業系統的電腦，可能會想要使用 PuTTY 之類的 SSH 用戶端。如需詳細資訊，請參閱 [PuTTY 下載頁面](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) (英文)。
-   如果您使用的是執行 Linux 作業系統的電腦，可能會想要使用 OpenSSH 之類的 SSH 用戶端。如需詳細資訊，請參閱 [OpenSSH](http://www.openssh.org/)。

此程序說明如何使用 PuTTY 程式來存取虛擬機器。

1.  在[管理入口網站](http://manage.windowsazure.com)中尋找 **[主機名稱]** 和 **[連接埠資訊]**。您可以從虛擬機器的儀表板中找到您需要的資訊。請按一下虛擬機器名稱，然後在儀表板的 **[Quick Glance]** 區段中尋找 **[SSH Details]**。

    ![取得 SSH 詳細資料](./media/virtual-machines-linux-how-to-log-on/sshdetails.png)

2.  開啟 PuTTY 程式。

3.  輸入您從儀表板收集的主機名稱和連接埠資訊，然後按一下 **[開啟]**。

    ![開啟 PuTTY](./media/virtual-machines-linux-how-to-log-on/putty.png)

4.  使用您在建立機器時指定的帳戶登入虛擬機器。

    ![登入虛擬機器](./media/virtual-machines-linux-how-to-log-on/sshlogin.png)

    您現在可以開始使用虛擬機器，就如同操作任何其他伺服器一樣。


