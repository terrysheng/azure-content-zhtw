<properties urlDisplayName="Log on to a VM" pageTitle="登入執行 Windows Server 的虛擬機器" metaKeywords="Azure logging on vm, vm portal" description="了解如何使用 Azure 管理入口網站來登入執行 Windows Server 的虛擬機器。" metaCanonical="" services="virtual-machines" documentationCenter="" title="如何登入執行 Windows Server 的虛擬機器" authors="kathydav" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="08/05/2014" ms.author="kathydav" />

# 如何登入執行 Windows Server 的虛擬機器

對於執行 Windows Server 作業系統的虛擬機器，您可以在管理入口網站中使用 [連接] 按鈕來啟動遠端桌面連接。

> [WACOM.NOTE] 若您必須重設使用者名稱或密碼，或啟用虛擬機器的 RDP，可以使用 [VMAccess][VMAccess] 擴充功能。如需要求和疑難排解提示，請參閱[透過 RDP 或 SSH 連接至 Azure 虛擬機器][透過 RDP 或 SSH 連接至 Azure 虛擬機器]。

1.  如果您未曾執行過這項操作，請登入 [Azure 管理入口網站][Azure 管理入口網站]。

2.  按一下 [虛擬機器]，然後選取適當的虛擬機器。

3.  按一下命令列上的 [連接]。

    ![登入虛擬機器][登入虛擬機器]

4.  按一下 [開啟]，並使用系統自動為虛擬機器建立的遠端桌面通訊協定檔案。

5.  按一下 [連接] 以繼續。

    ![繼續連接][繼續連接]

6.  在虛擬機器上輸入系統管理帳戶的使用者名稱和密碼，然後按一下 [確定]。這是您在建立虛擬機器時所指定的使用者名稱和密碼，但如果虛擬機器現在是網域控制站，則請輸入該網域之網域系統管理員的使用者名稱和密碼。

7.  按一下 [是] 以驗證虛擬機器的身分識別。

    ![驗證機器的身分識別][驗證機器的身分識別]

    您現在可以開始使用虛擬機器，就如同操作任何其他伺服器一樣。

  [VMAccess]: http://go.microsoft.com/fwlink/p/?LinkId=396856
  [透過 RDP 或 SSH 連接至 Azure 虛擬機器]: http://go.microsoft.com/fwlink/p/?LinkId=398294
  [Azure 管理入口網站]: http://manage.windowsazure.com
  [登入虛擬機器]: ./media/virtual-machines-log-on-windows-server/connectwindows.png
  [繼續連接]: ./media/virtual-machines-log-on-windows-server/connectpublisher.png
  [驗證機器的身分識別]: ./media/virtual-machines-log-on-windows-server/connectverify.png
