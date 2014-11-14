<properties linkid="manage-windows-howto-logon" urlDisplayName="Log on to a VM" pageTitle="登入執行 Windows Server 的虛擬機器" metaKeywords="Azure logging on vm, vm portal" description="了解如何使用 Azure 管理入口網站來登入執行 Windows Server 2008 R2 的虛擬機器。" metaCanonical="" services="virtual-machines" documentationCenter="" title="如何登入執行 Windows Server 的虛擬機器" authors="kathydav" solutions="" manager="dongill" editor="tysonn" />

> [WACOM.NOTE] 如需需求和疑難排解秘訣，請參閱[透過 RDP 或 SSH 連線至 Azure 虛擬機器][透過 RDP 或 SSH 連線至 Azure 虛擬機器]。

1.  如果您未曾執行過這項操作，請登入 [Azure 管理入口網站][Azure 管理入口網站]。

2.  按一下 [虛擬機器]，然後選取適當的虛擬機器。

3.  按一下命令列上的 [連接]。

    ![登入虛擬機器][登入虛擬機器]

4.  按一下 [開啟]，並使用系統自動為虛擬機器建立的遠端桌面通訊協定檔案。

5.  按一下 [連接] 以繼續進行連線程序。

    ![繼續連接][繼續連接]

6.  在虛擬機器上輸入系統管理帳戶的使用者名稱和密碼，然後按一下 [確定]。

7.  按一下 [是] 以驗證虛擬機器的身分識別。

    ![驗證機器的身分識別][驗證機器的身分識別]

    您現在可以開始使用虛擬機器，就如同操作任何其他伺服器一樣。

  [透過 RDP 或 SSH 連線至 Azure 虛擬機器]: http://go.microsoft.com/fwlink/p/?LinkId=398294
  [Azure 管理入口網站]: http://manage.windowsazure.com
  [登入虛擬機器]: ./media/virtual-machines-log-on-win-server/connectwindows.png
  [繼續連接]: ./media/virtual-machines-log-on-win-server/connectpublisher.png
  [驗證機器的身分識別]: ./media/virtual-machines-log-on-win-server/connectverify.png
