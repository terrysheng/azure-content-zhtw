<properties urlDisplayName="Log on to a VM" pageTitle="f登入在 Azure 中執行 Linux 的虛擬機器" metaKeywords="Azure Linux vm, Linux SSH" description="Learn how to log on to an Azure virtual machine running Linux by using a Secure Shell (SSH) client." metaCanonical="" services="virtual-machines" documentationCenter="" title="How to Log on to a Virtual Machine Running Linux" authors="kathydav" solutions="" manager="timlt" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="11/12/2014" ms.author="kathydav" />




#如何登入執行 Linux 的虛擬機器 #

對於執行 Linux 作業系統的虛擬機器，您可以使用安全殼層 (SSH) 用戶端來登入。

您必須在要用來登入虛擬機器的電腦上安裝 SSH 用戶端。有許多 SSH 用戶端程式可供選擇。下面是一些可能的選項：

- 在執行 Windows 作業系統的電腦上，您可能會想要使用 PuTTY 之類的 SSH 用戶端。如需詳細資訊，請參閱 [PuTTY 下載頁面](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)。
- 在執行 Linux 作業系統的電腦上，您可能會想要使用 OpenSSH 之類的 SSH 用戶端。如需詳細資訊，請參閱 [OpenSSH](http://www.openssh.org/)。

>[WACOM.NOTE] 如需詳細的需求和疑難排解提示，請參閱[透過 RDP 或 SSH 連接至 Azure 虛擬機器](http://go.microsoft.com/fwlink/p/?LinkId=398294)。 

此程序說明如何使用 PuTTY 程式來存取虛擬機器。

1. 從**管理入口網站** 尋找 [**主機名稱**] 和 [[連接埠資訊]](http://manage.windowsazure.com)。您可以從虛擬機器的儀表板中找到您需要的資訊。請按一下虛擬機器名稱，然後在儀表板的 [**快速概覽**] 區段中尋找 [**SSH 詳細資料**]。

	![Obtain SSH details](./media/virtual-machines-linux-how-to-log-on/sshdetails.png)

2. 開啟 PuTTY 程式。

3. 輸入您從儀表板收集的主機名稱和連接埠資訊，然後按一下 [**開啟**]。

	![Open PuTTY](./media/virtual-machines-linux-how-to-log-on/putty.png)

4. 使用您在建立機器時指定的帳戶登入虛擬機器。使用者名稱預設為 azureuser。

	![Log on to the virtual machine](./media/virtual-machines-linux-how-to-log-on/sshlogin.png)

>[WACOM.NOTE] VMAccess 擴充功能可以在您忘記 SSH 金鑰或密碼時幫助您重設。如果忘記使用者名稱，您可以使用擴充功能，以 sudo 權限建立新的使用者名稱。如需指示，請參閱[如何為 Linux 虛擬機器重設密碼或 SSH]。 
	
您現在可以開始使用虛擬機器，就如同操作任何其他伺服器一樣。

<!-- LINKS -->
[如何為 Linux 虛擬機器重設密碼或 SSH]: http://go.microsoft.com/fwlink/p/?LinkId=512138


<!--HONumber=35.1-->
