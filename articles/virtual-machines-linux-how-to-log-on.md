<properties urlDisplayName="Log on to a VM" pageTitle="登入在 Azure 中執行 Linux 的虛擬機器" metaKeywords="Azure Linux vm, Linux SSH" description="Learn how to log on to an Azure virtual machine running Linux by using a Secure Shell (SSH) client." metaCanonical="" services="virtual-machines" documentationCenter="" title="How to Log on to a Virtual Machine Running Linux" authors="kathydav" solutions="" manager="timlt" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="11/12/2014" ms.author="kathydav" />




#如何登入執行 Linux 的虛擬機器 #

對於執行 Linux 作業系統的虛擬機器，您可以使用安全殼層 (SSH) 用戶端來登入。

您必須在要用來登入虛擬機器的電腦上安裝 SSH 用戶端。有許多 SSH 用戶端程式可供選擇。下面是一些可能的選項：

- 在執行 Windows 作業系統的電腦上，您可能會想要使用 PuTTY 之類的 SSH 用戶端。如需詳細資訊，請參閱 [PuTTY 下載頁面](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)。
- 在執行 Linux 作業系統的電腦上，您可能會想要使用 OpenSSH 之類的 SSH 用戶端。如需詳細資訊，請參閱 [OpenSSH](http://www.openssh.org/)。

>[WACOM.NOTE] 如需詳細的需求和疑難排解提示，請參閱[透過 RDP 或 SSH 連接至 Azure 虛擬機器](http://go.microsoft.com/fwlink/p/?LinkId=398294)。 

此程序說明如何使用 PuTTY 程式來存取虛擬機器。

1. 從

	![Obtain SSH details](./media/virtual-machines-linux-how-to-log-on/sshdetails.png)

2. Open the PuTTY program.

3. Enter the Host Name and the Port information that you collected from the dashboard, and then click **Open**.

	![Open PuTTY](./media/virtual-machines-linux-how-to-log-on/putty.png)

4. Log on to the virtual machine using the account that you specified when the machine was created. The user name is azureuser by default.

	![Log on to the virtual machine](./media/virtual-machines-linux-how-to-log-on/sshlogin.png)

>[WACOM.NOTE] The VMAccess extension can help you reset the SSH key or password if you've forgotten it. If you've forgotten the user name, you can use the extension to create a new one with sudo authority. For instructions, see the [How to Reset a Password or SSH for Linux Virtual Machines]. 
	
You can now work with the virtual machine just as you would with any other server.

<!-- LINKS -->
[How to Reset a Password or SSH for Linux Virtual Machines]: http://go.microsoft.com/fwlink/p/?LinkId=512138

<!--HONumber=35_1-->
