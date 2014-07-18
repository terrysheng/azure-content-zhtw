<properties  linkid="manage-windows-howto-logon" urlDisplayName="Log on to a VM" pageTitle="Log on to a virtual machine running Windows Server" metaKeywords="Azure logging on vm, vm portal" description="Learn how to log on to a virtual machine running Windows Server 2008 R2 by using the Azure Management Portal." metaCanonical="" services="virtual-machines" documentationCenter="" title="How to Log on to a Virtual Machine Running Windows Server" authors="kathydav" solutions="" manager="jeffreyg" editor="tysonn" />

# 如何登入執行 Windows Server 的虛擬機器

對於執行 Windows Server 作業系統的虛擬機器，您可以在管理入口網站中使用 [連接] 按鈕來啟動遠端桌面連線。

1.  如果您未曾執行過這項操作，請登入 [Azure 管理入口網站][1]。

2.  按一下 **虛擬機器**，然後選取適當的虛擬機器。

3.  按一下命令列上的 **連接**。
    
    ![登入虛擬機器](./media/virtual-machines-log-on-windows-server/connectwindows.png)

4.  按一下 **開啟** 以使用系統自動為虛擬機器建立的遠端桌面通訊協定檔案。

5.  按一下 **連接** 以繼續進行連線程序。
    
    ![繼續連接](./media/virtual-machines-log-on-windows-server/connectpublisher.png)

6.  在虛擬機器上輸入系統管理帳戶的使用者名稱和密碼，然後按一下 **確定**。

7.  按一下 **是** 以驗證虛擬機器的身分識別。
    
    ![驗證機器的身分識別](./media/virtual-machines-log-on-windows-server/connectverify.png)
    
    您現在可以開始使用虛擬機器，就如同操作任何其他伺服器一樣。



[1]: http://manage.windowsazure.com