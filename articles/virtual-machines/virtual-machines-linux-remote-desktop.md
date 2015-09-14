<properties
	pageTitle="使用遠端桌面透過 xrdp 來連接 Microsoft Azure Linux VM。"
	description="了解如何在 Microsoft Azure Linux VM 上安裝和設定遠端桌面。"
	services="virtual-machines"
	documentationCenter=""
	authors="SuperScottz"
	manager="timlt"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/31/2015"
	ms.author="mingzhan"/>


#使用遠端桌面透過 xrdp 來連接 Microsoft Azure Linux VM

##概觀

RDP (遠端桌面通訊協定) 是用於 Windows 的專屬通訊協定，但我們如何使用 RDP 遠端連接 Linux VM？

本指南會為您提供答案！ 它會協助您在 Microsoft Azure Linux VM (虛擬機器) 上安裝及設定 xrdp，而您能夠從 Windows 電腦使用遠端桌面來連接它。

Xrdp 是開放原始碼 RDP 伺服器，可讓您從 Windows 電腦使用遠端桌面連接 Linux 伺服器。其執行效果比 VNC (虛擬網路運算) 好多了。VNC 具有 "JPEG"品質和緩慢行為，而 RDP 既快速又清楚。
 

> [AZURE.NOTE]您必須已經有執行 Linux 的 Microsoft Azure VM。若要建立並設定 Linux VM，請參閱 [Azure Linux VM 教學課程](virtual-machines-linux-tutorial.md)。


##建立遠端桌面的端點
我們將對本文件中的遠端桌面使用預設端點 3389。所以將 3389 端點設定為 Linux VM 的遠端桌面，如下所示：


![image](./media/virtual-machines-linux-remote-desktop/no1.png)


如果您不知道如何設定您的 VM 的端點，請參閱[指引](virtual-machines-set-up-endpoints.md)。


##安裝 Gnome 桌面

透過 putty 連接到 Linux VM，然後安裝 `Gnome Desktop`。

針對 Red Hat 系列 Linux，使用︰

	#sudo yum install gnome* "xorg*" -y

針對 Debian 和 Ubuntu，使用︰

	#sudo apt-get update
	#sudo apt-get install ubuntu-desktop


針對 OpenSUSE，使用︰

	#sudo zypper -y install gnome-session


##安裝 xrdp

對於 Red Hat 系列 Linux，您需要先在 Linux VM 中新增 EPEL 儲存機制，才能透過 `yum` 安裝 xrdp 封裝，請使用：

	#sudo rpm -ivh http://dl.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm
	#sudo yum -y install xrdp tigervnc-server tigervnc-server-module xterm

針對 Debian 和 Ubuntu Linux，使用︰

	#sudo apt-get install xrdp


針對 OpenSUSE，使用︰

> [AZURE.NOTE]在以下命令中使用您正在使用的版本更新 OpenSUSE 版本，以下是 `OpenSUSE 13.2` 的範例命令。

	#sudo zypper in http://download.opensuse.org/repositories/X11:/RemoteDesktop/openSUSE_13.2/x86_64/xrdp-0.9.0git.1401423964-2.1.x86_64.rpm
    #sudo zypper install tigervnc xorg-x11-Xvnc xterm remmina-plugin-vnc


##在開機時啟動 xrdp 並設定 xdrp 服務

針對 Red Hat 系列 Linux，使用︰

	#sudo service xrdp start
	#sudo chkconfig xrdp on


針對 OpenSUSE，使用︰

	#sudo systemctl start xrdp
	#sudo systemctl enable xrdp
 

##如果您使用 Red Hat 系列 Linux 則停用 iptables 

使用︰

	#sudo service iptables stop


##如果您使用晚於 Ubuntu 12.04LTS 的 Ubuntu 版本則使用 xfce

因為目前 xrop 無法支援來自晚於 Ubuntu 12.04LTS 之 Ubuntu 版本的 Gnome 桌面，我們將改用 `xfce` 桌面。

安裝 `xfce`，請使用：

    #sudo apt-get install xubuntu-desktop

然後啟用 `xfce`，請使用：
    
    #echo xfce4-session >~/.xsession

編輯組態檔 `/etc/xrdp/startwm.sh`，請使用：

    #sudo vi /etc/xrdp/startwm.sh   

在 `/etc/X11/Xsession` 一行前面加入 `xfce4-session` 一行。

重新啟動 xrdp 服務，請使用：

    #sudo service xrdp restart


##從 Windows 電腦連接 Linux VM
在 Windows 電腦上，啟動遠端桌面用戶端、輸入 Linux VM DNS 名稱或移至 Azure 入口網站中您的 VM 的 `Dashboard` 並按一下 `Connect`，您將會看到以下登入視窗：

![image](./media/virtual-machines-linux-remote-desktop/no2.png)

使用您的 Linux VM 的 `user` 和 `password` 進行登入，並立即享有來自 Microsoft Azure Linux VM 的遠端桌面！


##下一步
如需使用 xrdp 的詳細資訊，您可以參考[這裡](http://www.xrdp.org/)。





 

<!---HONumber=September15_HO1-->