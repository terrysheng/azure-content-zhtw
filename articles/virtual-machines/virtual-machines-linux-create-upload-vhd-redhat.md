<properties 
	pageTitle="建立及上傳 Red Hat Enterprise Linux VHD 以在 Azure 中使用" 
	description="了解如何建立及上傳包含 Red Hat Linux 作業系統的 Azure 虛擬硬碟 (VHD)。" 
	services="virtual-machines" 
	documentationCenter="" 
	authors="SuperScottz" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/23/2015" 
	ms.author="mingzhan"/>


# 準備執行 Azure 的 Red Hat 型虛擬機器
在本文中，您將學習如何準備 Red Hat Enterprise Linux (RHEL) 虛擬機器以在 Azure 中使用。本文中涵蓋的 RHEL 版本為 6.7 和 7.1，而本文中涵蓋的預備 Hypervisor 為 Hyper-V、KVM 和 VMWare。如需參加 Red Hat 雲端存取方案之資格需求的詳細資訊，請參閱 [Red Hat 雲端存取網站](http://www.redhat.com/en/technologies/cloud-computing/cloud-access)。




##從 Hyper-V 管理員準備映像 
###必要條件
本節假設您已從取自 RedHats 網站的 ISO 檔案將 RHEL 映像安裝至虛擬硬碟 (VHD)。如需有關如何使用 Hyper-V 管理員來安裝作業系統映像的詳細資訊，請參閱[安裝 Hyper-V 角色和設定虛擬機器](http://technet.microsoft.com/library/hh846766.aspx)。

**RHEL 安裝注意事項**

- Azure 不支援較新的 VHDX 格式。您可以使用 Hyper-V 管理員或 convert-vhd Powershell Cmdlet，將磁碟轉換為 VHD 格式。

- 安裝 Linux 系統時，建議您使用標準磁碟分割而不是 LVM (常是許多安裝的預設設定)。這可避免 LVM 與複製之虛擬機器的名稱衝突，特別是為了疑難排解而需要將作業系統磁碟連接至其他虛擬機器時。如果願意，您可以在資料磁碟上使用 LVM 或 RAID。

- 請勿在作業系統磁碟上設定交換磁碟分割。您可以設定 Linux 代理程式在暫存資源磁碟上建立交換檔。您可以在以下步驟中找到與此有關的詳細資訊。

- 所有 VHD 的大小都必須是 1 MB 的倍數。

- 使用 qemu-img 將磁碟映像轉換成 VHD 格式時，請注意 qemu-img >=2.2.1 的版本中已知有 Bug 會導致 VHD 的格式不正確。這個問題將於即將推出的 qemu-img 版本中獲得修正。目前建議使用 qemu-img 2.2.0 版或更低版本。


###RHEL 6.7

1.	在 Hyper-V 管理員中，選取虛擬機器。

2.	按一下 [連接]，以開啟虛擬機器的主控台視窗。

3.	執行下列命令以解除安裝 NetworkManager：

        # sudo rpm -e --nodeps NetworkManager

    **注意：**如果尚未安裝封裝，此命令將會失敗，並出現錯誤訊息。這是預期行為。

4.	在 `/etc/sysconfig/` 目錄中，建立名為 **network** 且包含下列文字的檔案：

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

5.	在 `/etc/sysconfig/network-scripts/` 目錄中，建立名為 **ifcfg-eth0** 且包含下列文字的檔案：

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

6.	移動 (或移除) udev 角色可防止產生乙太網路介面的靜態規則。在 Microsoft Azure 或 Hyper-V 中複製虛擬機器時，這些規則會造成問題：
            
        # sudo mkdir -m 0700 /var/lib/waagent
        # sudo mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/
        # sudo mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/

7.	要確保開機時會啟動網路服務，可執行以下命令：

        # sudo chkconfig network on

8.	透過執行以下命令來註冊 Red Hat 訂用帳戶，以便從 RHEL 儲存機制安裝封裝：

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

9.	WALinuxAgent 封裝 `WALinuxAgent-<version>` 已推送至 Fedora EPEL 6 儲存機制。執行下列命令以啟用 EPEL 儲存機制：

        # wget http://download.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm
        # rpm -ivh epel-release-6-8.noarch.rpm

10.	修改 grub 組態中的核心開機那一行，使其額外包含用於 Azure 的核心參數。作法是，在文字編輯器中開啟 `/boot/grub/menu.lst`，並確定預設核心包含以下參數：

        console=ttyS0 
        earlyprintk=ttyS0 
        rootdelay=300 
        numa=off

    這也將確保所有主控台訊息都會傳送給第一個序列埠，有助於 Azure 支援團隊進行問題偵錯程序。因為 RHEL 6 所使用核心版本的一個錯誤，這將會停用 NUMA。

    除了上述以外，我們還建議您移除下列參數：

        rhgb quiet crashkernel=auto

    在雲端環境中，我們會將所有記錄傳送到序列埠，因此不適合使用圖形化和無訊息啟動。

    如有需要，您可以保留 crashkernel 選項的設定，但請注意，此參數將會減少 VM 中約 128MB 或以上的可用記憶體數量，這在較小的 VM 中可能會是個問題。

11.	確定您已安裝 SSH 伺服器，並已設定為在開機時啟動。這通常是預設值。修改 /etc/ssh/sshd\_config 以包含下面一行：

        ClientAliveInterval 180

12.	執行以下命令來安裝 Azure Linux 代理程式：

        # sudo yum install WALinuxAgent
        # sudo chkconfig waagent on

    **注意**：如果 NetworkManager 和 NetworkManager-gnome 封裝沒有如步驟 2 所述遭到移除，則在安裝 WALinuxAgent 封裝時會移除這兩個封裝。

13.	請不要在 OS 磁碟上建立交換空間。Azure Linux 代理程式可在 VM 佈建於 Azure 後，使用附加至 VM 的本機資源磁碟自動設定交換空間。請注意，資源磁碟是暫存磁碟，可能會在 VM 取消佈建時清空。安裝 Azure Linux 代理程式 (請參閱上一個步驟) 後，請在 /etc/waagent.conf 中適當修改下列參數：

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

14.	執行下列命令以取消註冊訂用帳戶 (如有必要)：

        # sudo subscription-manager unregister

15.	執行下列命令，以取消佈建虛擬機器，並準備將其佈建於 Azure 上：

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

16.	在 Hyper-V 管理員中，依序按一下 [動作] -> [關閉]。您現在可以將 Linux VHD 上傳至 Azure。

###RHEL 7.1

1. 在 Hyper-V 管理員中，選取虛擬機器。

2.	按一下 [連接] 以開啟虛擬機器的主控台視窗。

3.	在 `/etc/sysconfig/` 目錄中，建立名為 **network** 且包含下列文字的檔案：

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

4.	在 `/etc/sysconfig/network-scripts/` 目錄中，建立名為 **ifcfg-eth0** 且包含下列文字的檔案：

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

5.	要確保開機時會啟動網路服務，可執行以下命令：

        # sudo chkconfig network on

6.	透過執行以下命令來註冊 Red Hat 訂用帳戶，以便從 RHEL 儲存機制安裝封裝：

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

7.	修改 grub 組態中的核心開機那一行，使其額外包含用於 Azure 的核心參數。作法是，在文字編輯器中開啟 `/etc/default/grub`，並編輯 **GRUB\_CMDLINE\_LINUX** 參數，例如：

        GRUB_CMDLINE_LINUX="rootdelay=300 
        console=ttyS0 
        earlyprintk=ttyS0"

    這也將確保所有主控台訊息都會傳送給第一個序列埠，有助於 Azure 支援團隊進行問題偵錯程序。除了上述以外，我們還建議您移除下列參數：

        rhgb quiet crashkernel=auto
    在雲端環境中，我們會將所有記錄傳送到序列埠，因此不適合使用圖形化和無訊息啟動。如有需要，您可以保留 crashkernel 選項的設定，但請注意，此參數將會減少 VM 中約 128MB 或以上的可用記憶體數量，這在較小的 VM 中可能會是個問題。

8.	在您參照上述完成編輯 `/etc/default/grub` 之後，請執行下列命令以重建 grub 組態：

        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

9.	確定您已安裝 SSH 伺服器，並已設定為在開機時啟動。這通常是預設值。修改 `/etc/ssh/sshd_config` 以包含下面一行：

        ClientAliveInterval 180

10.	WALinuxAgent 封裝 `WALinuxAgent-<version>` 已推送至 Fedora EPEL 6 儲存機制。執行下列命令以啟用 EPEL 儲存機制：

        # wget http://dl.fedoraproject.org/pub/epel/7/x86_64/e/epel-release-7-5.noarch.rpm
        # rpm -ivh epel-release-7-5.noarch.rpm

11.	執行以下命令來安裝 Azure Linux 代理程式：

        # sudo yum install WALinuxAgent
        # sudo systemctl enable waagent.service 

12.	請勿在作業系統磁碟上建立交換空間。Azure Linux 代理程式可在 VM 佈建於 Azure 後，使用附加至 VM 的本機資源磁碟自動設定交換空間。請注意，資源磁碟是暫存磁碟，可能會在 VM 取消佈建時清空。安裝 Azure Linux 代理程式 (請參閱上一個步驟) 後，請在 `/etc/waagent.conf` 中適當修改下列參數：

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

13.	如果您要取消註冊訂用帳戶，請執行下列命令：

        # sudo subscription-manager unregister

14.	執行下列命令，以取消佈建虛擬機器，並準備將其佈建於 Azure 上：
        
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

15.	在 Hyper-V 管理員中，依序按一下 [動作] -> [關閉]。您現在可以將 Linux VHD 上傳至 Azure。


##從 KVM 準備映像 
###RHEL 6.7

1.	從 Red Hat 網站下載 RHEL 6.7 的 KVM 映像。

2.	設定根密碼

    產生加密的密碼，並複製命令的輸出：

        # openssl passwd -1 changeme
    使用 guestfish 設定根密碼：
       
        # guestfish --rw -a <image-name>
        ><fs> run
        ><fs> list-filesystems
        ><fs> mount /dev/sda1 /
        ><fs> vi /etc/shadow
        ><fs> exit
    將根使用者的第二個欄位從 "!!" 變更為加密的密碼。

3.	在 KVM 中從 qcow2 映像建立虛擬機器、將磁碟類型設定為 **qcow2**，將虛擬網路介面裝置模型設定為 **virtio**。然後啟動虛擬機器並以 root 的身分登入。

4.	在 `/etc/sysconfig/` 目錄中，建立名為 **network** 且包含下列文字的檔案：

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

5.	在 `/etc/sysconfig/network-scripts/` 目錄中，建立名為 **ifcfg-eth0** 且包含下列文字的檔案：

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

6.	移動 (或移除) udev 角色可防止產生乙太網路介面的靜態規則。在 Microsoft Azure 或 Hyper-V 中複製虛擬機器時，這些規則會造成問題：

        # mkdir -m 0700 /var/lib/waagent
        # mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/
        # mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/

7.	要確保開機時會啟動網路服務，可執行以下命令：

        # chkconfig network on

8.	透過執行以下命令來註冊 Red Hat 訂用帳戶，以便從 RHEL 儲存機制安裝封裝：

        # subscription-manager register –auto-attach --username=XXX --password=XXX

9.	修改 grub 組態中的核心開機那一行，使其額外包含用於 Azure 的核心參數。作法是，在文字編輯器中開啟 `/boot/grub/menu.lst`，並確定預設核心包含以下參數：

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300 numa=off

    這也將確保所有主控台訊息都會傳送給第一個序列埠，有助於 Azure 支援團隊進行問題偵錯程序。因為 RHEL 6 所使用核心版本的一個錯誤，這將會停用 NUMA。

    除了上述以外，我們還建議您移除下列參數：

        rhgb quiet crashkernel=auto

    在雲端環境中，我們會將所有記錄傳送到序列埠，因此不適合使用圖形化和無訊息啟動。如有需要，您可以保留 crashkernel 選項的設定，但請注意，此參數將會減少 VM 中約 128MB 或以上的可用記憶體數量，這在較小的 VM 中可能會是個問題。

10.	解除安裝 cloud-init：

        # yum remove cloud-init

11.	確定您已安裝 SSH 伺服器，並已設定為在開機時啟動：
 
        # chkconfig sshd on

    修改 /etc/ssh/sshd\_config 以包含下面幾行：

        PasswordAuthentication yes
        ClientAliveInterval 180

    重新啟動 sshd：

		# service sshd restart

12.	WALinuxAgent 封裝 `WALinuxAgent-<version>` 已推送至 Fedora EPEL 6 儲存機制。執行下列命令以啟用 EPEL 儲存機制：

        # wget http://download.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm
        # rpm -ivh epel-release-6-8.noarch.rpm

13.	執行以下命令來安裝 Azure Linux 代理程式：

        # yum install WALinuxAgent
        # chkconfig waagent on

14.	Azure Linux 代理程式可在 VM 佈建於 Azure 後，使用附加至 VM 的本機資源磁碟自動設定交換空間。請注意，資源磁碟是暫存磁碟，可能會在 VM 取消佈建時清空。安裝 Azure Linux 代理程式 (請參閱上一個步驟) 後，請在 **/etc/waagent.conf** 中適當修改下列參數：

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

15.	執行下列命令以取消註冊訂用帳戶 (如有必要)：
        
        # subscription-manager unregister

16.	執行下列命令，以取消佈建虛擬機器，並準備將其佈建於 Azure 上：

        # waagent -force -deprovision
        # export HISTSIZE=0
        # logout

17.	在 KVM 中關閉 VM。

18.	將 qcow2 映像轉換成 vhd 格式：先將映像轉換成原始格式：
         
         # qemu-img convert -f qcow2 –O raw rhel-6.7.qcow2 rhel-6.7.raw
    確定原始映像的大小符合 1 MB；若非如此，則總大小必須符合 1MB: # MB=$((1024*1024)) # size=$(qemu-img info -f raw --output json "rhel-6.7.raw" | \\ gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}') # rounded\_size=$((($size/$MB + 1)*$MB))

         # qemu-img resize rhel-6.7.raw $rounded_size

    將原始磁碟轉換成固定大小的 vhd：

         # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-6.7.raw rhel-6.7.vhd


###RHEL 7.1

1.	從 Red Hat 網站下載 RHEL 7.1 的 KVM 映像。

2.	設定根密碼

    產生加密的密碼，並複製命令的輸出

        # openssl passwd -1 changeme

    使用 guestfish 設定根密碼

        # guestfish --rw -a <image-name>
        ><fs> run
        ><fs> list-filesystems
        ><fs> mount /dev/sda1 /
        ><fs> vi /etc/shadow
        ><fs> exit

    將根使用者的第二個欄位從 "!!" 變更為加密的密碼

3.	在 KVM 中從 qcow2 映像建立虛擬機器、將磁碟類型設定為 **qcow2**，將虛擬網路介面裝置模型設定為 **virtio**。然後啟動虛擬機器並以 root 的身分登入。

4.	在 `/etc/sysconfig/` 目錄中，建立名為 **network** 且包含下列文字的檔案：

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

5.	在 `/etc/sysconfig/network-scripts/` 目錄中，建立名為 **ifcfg-eth0** 且包含下列文字的檔案：

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

6.	要確保開機時會啟動網路服務，可執行以下命令：

        # chkconfig network on

7.	透過執行以下命令來註冊 Red Hat 訂用帳戶，以便從 RHEL 儲存機制安裝封裝：

        # subscription-manager register –auto-attach --username=XXX --password=XXX

8.	修改 grub 組態中的核心開機那一行，使其額外包含用於 Azure 的核心參數。作法是，在文字編輯器中開啟 `/etc/default/grub`，並編輯 **GRUB\_CMDLINE\_LINUX** 參數，例如：

        GRUB_CMDLINE_LINUX="rootdelay=300 
        console=ttyS0 
        earlyprintk=ttyS0"

    這也將確保所有主控台訊息都會傳送給第一個序列埠，有助於 Azure 支援團隊進行問題偵錯程序。除了上述以外，我們還建議您移除下列參數：

        rhgb quiet crashkernel=auto

    在雲端環境中，我們會將所有記錄傳送到序列埠，因此不適合使用圖形化和無訊息啟動。如有需要，您可以保留 crashkernel 選項的設定，但請注意，此參數將會減少 VM 中約 128MB 或以上的可用記憶體數量，這在較小的 VM 中可能會是個問題。

9.	在您參照上述完成編輯 `/etc/default/grub` 之後，請執行下列命令以重建 grub 組態：

        # grub2-mkconfig -o /boot/grub2/grub.cfg

10.	解除安裝 cloud-init：

        # yum remove cloud-init

11.	確定您已安裝 SSH 伺服器，並已設定為在開機時啟動：

        # systemctl enable sshd

    修改 /etc/ssh/sshd\_config 以包含下面幾行：

        PasswordAuthentication yes
        ClientAliveInterval 180

    重新啟動 sshd：

        systemctl restart sshd	

12.	WALinuxAgent 封裝 `WALinuxAgent-<version>` 已推送至 Fedora EPEL 6 儲存機制。執行下列命令以啟用 EPEL 儲存機制：

        # wget http://dl.fedoraproject.org/pub/epel/7/x86_64/e/epel-release-7-5.noarch.rpm
        # rpm -ivh epel-release-7-5.noarch.rpm

13.	執行以下命令來安裝 Azure Linux 代理程式：

        # yum install WALinuxAgent

    啟用 waagent 服務：

        # systemctl enable waagent.service

14.	請勿在作業系統磁碟上建立交換空間。Azure Linux 代理程式可在 VM 佈建於 Azure 後，使用附加至 VM 的本機資源磁碟自動設定交換空間。請注意，資源磁碟是暫存磁碟，可能會在 VM 取消佈建時清空。安裝 Azure Linux 代理程式 (請參閱上一個步驟) 後，請在 `/etc/waagent.conf` 中適當修改下列參數：

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

15.	執行下列命令以取消註冊訂用帳戶 (如有必要)：

        # subscription-manager unregister

16.	執行下列命令，以取消佈建虛擬機器，並準備將其佈建於 Azure 上：

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

17.	在 KVM 中關閉虛擬機器。

18.	將 qcow2 映像轉換成 vhd 格式：

    先將映像轉換成原始格式：

         # qemu-img convert -f qcow2 –O raw rhel-7.1.qcow2 rhel-7.1.raw

    確定原始映像的大小 為 1 MB，否則將大小四捨五入為 1 MB：

         # MB=$((1024*1024))
         # size=$(qemu-img info -f raw --output json "rhel-7.1.raw" | \
                  gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')
         # rounded_size=$((($size/$MB + 1)*$MB))

         # qemu-img resize rhel-7.1.raw $rounded_size

    將原始磁碟轉換成固定大小的 vhd：

         # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.1.raw rhel-7.1.vhd


##從 VMWare 準備映像
###必要條件
本節假設您已在 VMWare 中安裝 RHEL 虛擬機器。如需有關如何在 VMWare 中安裝作業系統的詳細資訊，請參閱 [VMWare 客體作業系統安裝指南](http://partnerweb.vmware.com/GOSIG/home.html)。
 
- 安裝 Linux 系統時，建議您使用標準磁碟分割而不是 LVM (常是許多安裝的預設設定)。這可避免 LVM 與複製之虛擬機器的名稱衝突，特別是為了疑難排解而需要將作業系統磁碟連接至其他虛擬機器時。如果願意，您可以在資料磁碟上使用 LVM 或 RAID。

- 請勿在作業系統磁碟上設定交換磁碟分割。您可以設定 Linux 代理程式在暫存資源磁碟上建立交換檔。您可以在以下步驟中找到與此有關的詳細資訊。

- 建立虛擬硬碟時，請選取 [將虛擬磁碟儲存為單一檔案]。

###RHEL 6.7
1.	執行下列命令以解除安裝 NetworkManager：

         # sudo rpm -e --nodeps NetworkManager

    **注意：**如果尚未安裝封裝，此命令將會失敗，並出現錯誤訊息。這是預期行為。

2.	在 /etc/sysconfig/ 目錄中，建立名為 **network** 且包含下列文字的檔案：

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

3.	在 /etc/sysconfig/network-scripts/ 目錄中，建立名為 **ifcfg-eth0** 且包含下列文字的檔案：

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

4.	移動 (或移除) udev 角色可防止產生乙太網路介面的靜態規則。在 Microsoft Azure 或 Hyper-V 中複製虛擬機器時，這些規則會造成問題：

        # sudo mkdir -m 0700 /var/lib/waagent
        # sudo mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/
        # sudo mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/

5.	要確保開機時會啟動網路服務，可執行以下命令：

        # sudo chkconfig network on

6.	透過執行以下命令來註冊 Red Hat 訂用帳戶，以便從 RHEL 儲存機制安裝封裝：

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

7.	WALinuxAgent 封裝 `WALinuxAgent-<version>` 已推送至 Fedora EPEL 6 儲存機制。執行下列命令以啟用 EPEL 儲存機制：

        # wget http://download.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm
        # rpm -ivh epel-release-6-8.noarch.rpm

8.	修改 grub 組態中的核心開機那一行，使其額外包含用於 Azure 的核心參數。作法是，在文字編輯器中開啟 "/boot/grub/menu.lst"，並確定預設核心包含以下參數：

        console=ttyS0 
        earlyprintk=ttyS0 
        rootdelay=300 
        numa=off

    這也將確保所有主控台訊息都會傳送給第一個序列埠，有助於 Azure 支援團隊進行問題偵錯程序。因為 RHEL 6 所使用核心版本的一個錯誤，這將會停用 NUMA。除了上述以外，我們還建議您移除下列參數：

        rhgb quiet crashkernel=auto

    在雲端環境中，我們會將所有記錄傳送到序列埠，因此不適合使用圖形化和無訊息啟動。如有需要，您可以保留 crashkernel 選項的設定，但請注意，此參數將會減少 VM 中約 128MB 或以上的可用記憶體數量，這在較小的 VM 中可能會是個問題。

9.	確定您已安裝 SSH 伺服器，並已設定為在開機時啟動。這通常是預設值。修改 `/etc/ssh/sshd_config` 以包含下面一行：

        ClientAliveInterval 180

10.	執行以下命令來安裝 Azure Linux 代理程式：

        # sudo yum install WALinuxAgent
        # sudo chkconfig waagent on

11.	請不要在 OS 磁碟上建立交換空間：
    
    Azure Linux 代理程式可在 VM 佈建於 Azure 後，使用附加至 VM 的本機資源磁碟自動設定交換空間。請注意，資源磁碟是暫存磁碟，可能會在 VM 取消佈建時清空。安裝 Azure Linux 代理程式 (請參閱上一個步驟) 後，請在 `/etc/waagent.conf` 中適當修改下列參數：

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

12.	執行下列命令以取消註冊訂用帳戶 (如有必要)：

        # sudo subscription-manager unregister

13.	執行下列命令，以取消佈建虛擬機器，並準備將其佈建於 Azure 上：

        # sudo waagent -force -deprovision 
        # export HISTSIZE=0
        # logout

14.	關閉 VM，並將 VMDK 檔案轉換成 VHD 檔案。

    先將映像轉換成原始格式：

        # qemu-img convert -f vmdk –O raw rhel-6.7.vmdk rhel-6.7.raw

    確定原始映像的大小 為 1 MB，否則將大小四捨五入為 1 MB：

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-6.7.raw" | \
                gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')
        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-6.7.raw $rounded_size

    將原始磁碟轉換成固定大小的 vhd：

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-6.7.raw rhel-6.7.vhd

###RHEL 7.1

1.	在 /etc/sysconfig/ 目錄中，建立名為 **network** 且包含下列文字的檔案：

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

2.	在 /etc/sysconfig/network-scripts/ 目錄中，建立名為 **ifcfg-eth0** 且包含下列文字的檔案：

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

3.	要確保開機時會啟動網路服務，可執行以下命令：

        # sudo chkconfig network on

4.	透過執行以下命令來註冊 Red Hat 訂用帳戶，以便從 RHEL 儲存機制安裝封裝：

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

5.	修改 grub 組態中的核心開機那一行，使其額外包含用於 Azure 的核心參數。作法是，在文字編輯器中開啟 `/etc/default/grub`，並編輯 **GRUB\_CMDLINE\_LINUX** 參數，例如：

        GRUB_CMDLINE_LINUX="rootdelay=300 
        console=ttyS0 
        earlyprintk=ttyS0"

    這也將確保所有主控台訊息都會傳送給第一個序列埠，有助於 Azure 支援團隊進行問題偵錯程序。除了上述以外，我們還建議您移除下列參數：

        rhgb quiet crashkernel=auto

    在雲端環境中，我們會將所有記錄傳送到序列埠，因此不適合使用圖形化和無訊息啟動。如有需要，您可以保留 crashkernel 選項的設定，但請注意，此參數將會減少 VM 中約 128MB 或以上的可用記憶體數量，這在較小的 VM 中可能會是個問題。

6.	在您參照上述完成編輯 `/etc/default/grub` 之後，請執行下列命令以重建 grub 組態：

         # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

7.	將 Hyper-V 模組新增至 initramfs：

    編輯 `/etc/dracut.conf`，新增內容：

        add_drivers+=”hv_vmbus hv_netvsc hv_storvsc”

    重建 initramfs：

        # dracut –f -v

8.	確定您已安裝 SSH 伺服器，並已設定為在開機時啟動。這通常是預設值。修改 `/etc/ssh/sshd_config` 以包含下面一行：

        ClientAliveInterval 180

9.	WALinuxAgent 封裝 `WALinuxAgent-<version>` 已推送至 Fedora EPEL 6 儲存機制。執行下列命令以啟用 EPEL 儲存機制：


        # wget http://dl.fedoraproject.org/pub/epel/7/x86_64/e/epel-release-7-5.noarch.rpm
        # rpm -ivh epel-release-7-5.noarch.rpm

10.	執行以下命令來安裝 Azure Linux 代理程式：

        # sudo yum install WALinuxAgent
        # sudo systemctl enable waagent.service

11.	請勿在作業系統磁碟上建立交換空間。Azure Linux 代理程式可在 VM 佈建於 Azure 後，使用附加至 VM 的本機資源磁碟自動設定交換空間。請注意，資源磁碟是暫存磁碟，可能會在 VM 取消佈建時清空。安裝 Azure Linux 代理程式 (請參閱上一個步驟) 後，請在 `/etc/waagent.conf` 中適當修改下列參數：

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

12.	如果您要取消註冊訂用帳戶，請執行下列命令：

        # sudo subscription-manager unregister

13.	執行下列命令，以取消佈建虛擬機器，並準備將其佈建於 Azure 上：

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

14.	關閉 VM，並將 VMDK 檔案轉換成 VHD 格式。

    先將映像轉換成原始格式：

        # qemu-img convert -f vmdk –O raw rhel-7.1.vmdk rhel-7.1.raw

    確定原始映像的大小 為 1 MB，否則將大小四捨五入為 1 MB：

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-7.1.raw" | \
                 gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')
        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-7.1.raw $rounded_size

    將原始磁碟轉換成固定大小的 vhd：

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.1.raw rhel-7.1.vhd


##自動從使用 kickstart 檔案的 ISO 準備
###RHEL 7.1

1.	使用以下內容建立 kickstart 檔案，並儲存此檔案。如需有關 kickstart 安裝的詳細資訊，請參閱 [Kickstart 安裝指南](https://access.redhat.com/documentation/zh-TW/Red_Hat_Enterprise_Linux/7/html/Installation_Guide/chap-kickstart-installations.html)。


        # Kickstart for provisioning a RHEL 7 Azure VM

        # System authorization information
        auth --enableshadow --passalgo=sha512

        # Use graphical install
        text

        # Do not run the Setup Agent on first boot
        firstboot --disable

        # Keyboard layouts
        keyboard --vckeymap=us --xlayouts='us'

        # System language
        lang en_US.UTF-8

        # Network information
        network  --bootproto=dhcp

        # Root password
        rootpw --plaintext "to_be_disabled"

        # System services
        services --enabled="sshd,waagent,NetworkManager"

        # System timezone
        timezone Etc/UTC --isUtc --ntpservers 0.rhel.pool.ntp.org,1.rhel.pool.ntp.org,2.rhel.pool.ntp.org,3.rhel.pool.ntp.org

        # Partition clearing information
        clearpart --all --initlabel

        # Clear the MBR
        zerombr

        # Disk partitioning information
        part /boot --fstype="xfs" --size=500
        part / --fstyp="xfs" --size=1 --grow --asprimary

        # System bootloader configuration
        bootloader --location=mbr

        # Firewall configuration
        firewall --disabled

        # Enable SELinux
        selinux --enforcing

        # Don't configure X
        skipx

        # Power down the machine after install
        poweroff

        # Primary Fedora repo
        repo --name="epel7" --baseurl="http://dl.fedoraproject.org/pub/epel/7/x86_64/"

        %packages
        @base
        @console-internet
        chrony
        sudo
        python-pyasn1
        parted
        ntfsprogs
        WALinuxAgent
        -dracut-config-rescue

        %end

        %post --log=/var/log/anaconda/post-install.log

        #!/bin/bash

        # Disable the root account
        usermod root -p '!!'

        # Configure swap in WALinuxAgent
        sed -i 's/^(ResourceDisk\.EnableSwap)=[Nn]$/\1=y/g' /etc/waagent.conf
        sed -i 's/^(ResourceDisk\.SwapSizeMB)=[0-9]*$/\1=2048/g' /etc/waagent.conf

        # Set the cmdline
        sed -i 's/^(GRUB_CMDLINE_LINUX)=".*"$/\1="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"/g' /etc/default/grub

        # Enable SSH keepalive
        sed -i 's/^#(ClientAliveInterval).*$/\1 180/g' /etc/ssh/sshd_config

        # Build the grub cfg
        grub2-mkconfig -o /boot/grub2/grub.cfg

        # Configure network
        cat << EOF > /etc/sysconfig/network-scripts/ifcfg-eth0
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
        NM_CONTROLLED=yes
        EOF

        # Deprovision and prepare for Azure
        waagent -force -deprovision

        %end

 

2.	將 kickstart 檔案放在可從安裝系統存取的位置。
 
3.	在 Hyper-V 管理員中建立新的 VM。在 [連線虛擬硬碟] 頁面上，選取 [稍後連接虛擬硬碟]，並完成 [新增虛擬機器精靈]。

4.	開啟 VM 設定：

    a.將新的虛擬硬碟連接到 VM，務必選取 [VHD 格式] 和 [固定大小]。
    
    b.將安裝 ISO 連接到 DVD 光碟機。

    c.將 BIOS 設定成從 CD 開機。

5.	啟動 VM，當安裝指南出現時，請按 **Tab** 鍵以設定開機選項。

6.	在開機選項結尾輸入 `inst.ks=<the location of the Kickstart file>`，然後按 **Enter** 鍵。

7.	等待安裝完成，當它完成時，VM 將會關閉自動。您現在可以將 Linux VHD 上傳至 Azure。

##已知問題
當您在 Hyper-V 和 Azure 中使用 RHEL 7.1 時，存在已知的問題。

###問題：磁碟 I/O 凍結 

這個問題可能發生在 Hyper-V 和 Azure 中 RHEL 7.1 的頻繁儲存磁碟 I/O 活動期間。

附買回利率：

此問題會間歇發生，不過在 Hyper-V 和 Azure 中比較常發於頻繁儲存磁碟 I/O 作業期間。

    
[AZURE.NOTE]Red Hat 已經解決這個已知的問題。若要安裝相關聯的修正程式，請執行下列命令：

    # sudo yum update

<!---HONumber=AcomDC_1203_2015-->