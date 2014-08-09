<properties linkid="manage-linux-common-task-upload-vhd" urlDisplayName="Upload a VHD" pageTitle="Create and upload a Linux VHD in Azure" metaKeywords="Azure VHD, uploading Linux VHD" description="Learn to create and upload an Azure virtual hard disk (VHD) that has the Linux operating system." metaCanonical="" services="virtual-machines" documentationCenter="" title="Creating and Uploading a Virtual Hard Disk that Contains the Linux Operating System" authors="kathydav" solutions="" manager="jeffreyg" editor="tysonn" />

建立及上傳包含 Linux 作業系統的虛擬硬碟
=======================================

Azure 中的虛擬機器會執行您建立虛擬機器時選擇的作業系統。Azure 將虛擬機器的作業系統以 VHD 格式 (.vhd 檔案) 存放在虛擬硬碟中。為了複製而準備的作業系統 VHD 叫做「映像」。本文說明如何上傳包含您已安裝及一般化之作業系統的 .vhd 檔案，以建立您自己的映像。如需 Azure 中磁碟和映像的詳細資訊，請參閱[管理磁碟及映像](http://msdn.microsoft.com/zh-tw/library/windowsazure/jj672979.aspx)。

**注意**：建立虛擬機器時，您可以自訂作業系統設定以利您的應用程式執行。您設定的組態會存放在該虛擬機器的磁碟上。如需說明，請參閱[如何建立自訂虛擬機器](/en-us/manage/windows/how-to-guides/custom-create-a-vm/)。

**重要事項**：當有背書的散發套件之一搭配[這篇文章](http://support.microsoft.com/kb/2805216)中指定的組態詳細資料使用時，Azure 平台的 SLA 僅適用於執行 Linux 作業系統的虛擬機器。Azure 映像庫中提供的所有 Linux 散發套件都是搭配所需組態的背書散發套件。

必要條件
--------

本文假設您已有以下項目：

-   **管理憑證** - 您已經為您想要上傳 VHD 的訂閱建立管理憑證，並已將憑證匯出到 .cer 檔案。如需建立憑證的詳細資訊，請參閱[建立及上傳 Windows Azure 的管理憑證](http://msdn.microsoft.com/library/windowsazure/gg551722.aspx)。

-   **已於 .vhd 檔案安裝 Linux 作業系統。** - 您已將支援的 Linux 作業系統安裝到虛擬硬碟上。有多個工具可供建立 .vhd 檔案。您可以使用虛擬化解決方案 (如 Hyper-V) 建立 .vhd 檔案及安裝作業系統。如需說明，請參閱[安裝 Hyper-V 角色和設定虛擬機器](http://technet.microsoft.com/library/hh846766.aspx)。

    **重要事項**：Azur 不支援較新的 VHDX 格式。您可以使用 Hyper-V 管理員或 convert-vhd Cmdlet 將磁碟轉換為 VHD 格式。

    如需背書散發套件的清單，請參閱 [Azure 背書之散發套件上的 Linux](../linux-endorsed-distributions)。或者，請看本文最後的[非背書散發套件的資訊](#nonendorsed)小節。

-   **Linux Azure 命令列工具。**若您使用 Linux 作業系統建立映像，請使用此工具上傳 VHD 檔案。若要下載工具，請參閱[適用於 Linux 和 Mac 的 Azure 命令列工具](http://go.microsoft.com/fwlink/?LinkID=253691&clcid=0x409)。

-   **Add-AzureVhd Cmdlet**，這是 Azure PowerShell 模組的一部分。若要下載此模組，請參閱 [Azure 下載](/en-us/develop/downloads/)。如需參考資訊，請參閱 [Add-AzureVhd](http://msdn.microsoft.com/library/windowsazure/dn495173.aspx) (英文)。

**所有散發套件皆請注意下列事項：**

-   安裝 Linux 系統時，建議使用標準磁碟分割而不是 LVM (常是許多安裝的預設設定)。這可避免 LVM 與複製之虛擬機器的名稱衝突，特別是為了疑難排解而需要將作業系統磁碟連接至其他虛擬機器時。

-   Azure Linux 代理程式 (waagent) 與 NetworkManager 不相容。網路組態應可透過 ifup/ifdown 指令碼控制。散發套件提供的大部分 RPM/Deb 套件會將 NetworkManager 設定為 waagent 套件的衝突，因此會在您安裝 Linux 代理程式套件時解除安裝 NetworkManager。Azure Linux 代理程式也需要安裝 python-pyasn1 套件。

-   因為 Linux Kernel 2.6.37 以下版本的一個錯誤，較大的虛擬機器不支援 NUMA。手動安裝 waagent 將針對 Linux Kernel 自動停用 GRUB 組態中的 NUMA 。這個問題主要會影響使用上游 Red Hat 2.6.32 kernel 的散發套件。

-   建議您不要在安裝期間建立 SWAP 磁碟分割。您可使用 Azure Linux 代理程式設定 SWAP 空間。也建議您，在沒有使用 [Microsoft 網站](http://go.microsoft.com/fwlink/?LinkID=253692&clcid=0x409) (英文) 上所提供之修補程式的情況下，不要使用主流 Linux Kernel 搭配 Azure 虛擬機器 (目前許多散發套件/核心可能已包含此修正)。

-   所有 VHD 的大小都必須是 1 MB 的倍數。

此工作包含下列步驟：

-   [步驟 1：準備要上傳的映像](#prepimage)
-   [步驟 2：建立 Azure 中的儲存體帳戶](#createstorage)
-   [步驟 3：準備與 Azure 連接](#connect)
-   [步驟 4：上傳映像至 Azure](#upload)

步驟 1：準備要上傳的映像
------------------------

### 準備 CentOS 6.2+

您必須在作業系統中完成特定組態步驟，才能在 Azure 中執行虛擬機器。

1.  在 Hyper-V 管理員的中間窗格中，選取虛擬機器。

2.  按一下 **[連接]** 以開啟虛擬機器的視窗。

3.  執行以下命令解除安裝 NetworkManager：

        rpm -e --nodeps NetworkManager

    **注意：**若未安裝該套件，此命令將失敗，並發出錯誤訊息。這是預期的反應。

4.  在 `/etc/sysconfig/` 目錄中建立 **network** 檔案，檔案中包含以下文字：

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

5.  在 `/etc/sysconfig/network-scripts/` 目錄中建立 **ifcfg-eth0** 檔案，檔案中包含以下文字：

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

6.  移動 (或移除) udev 角色可防止產生乙太網路介面的靜態規則。在 Azure 或 Hyper-V 中複製虛擬機器時，這些規則會造成問題：

        # sudo mkdir -m 0700 /var/lib/waagent
        # sudo mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/
        # sudo mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/

7.  要確保開機時會啟動網路服務，可執行以下命令：

        # chkconfig network on

8.  **僅限 CentOS 6.2 或 6.3**：安裝 Linux 整合服務的驅動程式

    **注意：**此步驟僅適用於 CentOS 6.2 和 6.3。在 CentOS 6.4 及更高的版本中，核心已包含 Linux 整合服務。

    a) 從[下載中心](http://www.microsoft.com/en-us/download/details.aspx?id=34603) (英文) 取得包含 Linux 整合服務驅動程式的 .iso 檔案。

    b) 在 Hyper-V 管理員的 **[動作]** 窗格中，按一下 **[設定]**。

    ![開啟 Hyper-V 設定](./media/virtual-machines-linux-create-upload-vhd/settings.png)

    c) 在 **[硬體]** 窗格中，按一下 **[IDE 控制器 1]**。

    ![新增含有安裝媒體的 DVD 光碟機](./media/virtual-machines-linux-create-upload-vhd/installiso.png)

    d) 在 **[IDE 控制器]** 方塊中，按一下 **[DVD 光碟機]**，然後按一下 **[新增]**。

    e) 選取 **[映像檔]**，瀏覽至 **[Linux IC v3.2.iso]**，然後按一下 **[開啟]**。

    f) 在 **[設定]** 窗格中按一下 **[確定]**。

    g) 按一下 **[連接]** 以開啟虛擬機器的視窗。

    h) 在 [命令提示字元] 視窗中，輸入以下命令：

        mount /dev/cdrom /media
        /media/install.sh
        reboot

9.  執行以下命令來安裝 python-pyasn1：

        # sudo yum install python-pyasn1

10. 以下列文字取代它們的 /etc/yum.repos.d/CentOS-Base.repo 檔案

        [openlogic]
        name=CentOS-$releasever - openlogic packages for $basearch
        baseurl=http://olcentgbl.trafficmanager.net/openlogic/$releasever/openlogic/$basearch/
        enabled=1
        gpgcheck=0
            
        [base]
        name=CentOS-$releasever - Base
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/os/$basearch/
        gpgcheck=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6
            
        #released updates
        [updates]
        name=CentOS-$releasever - Updates
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/updates/$basearch/
        gpgcheck=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6
            
        #additional packages that may be useful
        [extras]
        name=CentOS-$releasever - Extras
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/extras/$basearch/
        gpgcheck=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6
            
        #additional packages that extend functionality of existing packages
        [centosplus]
        name=CentOS-$releasever - Plus
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/centosplus/$basearch/
        gpgcheck=1
        enabled=0
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6
            
        #contrib - packages by Centos Users
        [contrib]
        name=CentOS-$releasever - Contrib
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/contrib/$basearch/
        gpgcheck=1
        enabled=0
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

11. 在 /etc/yum.conf 中加入這一行：

        http_caching=packages

    若是 CentOS 6.2 和 6.3，則只加入這一行：

        exclude=kernel*

12. 停用 yum 模組 "fastestmirror"，作法是編輯 "/etc/yum/pluginconf.d/fastestmirror.conf" 檔案，在 [main] 底下輸入：

        set enabled=0

13. 執行以下命令清除目前的 yum 中繼資料：

        yum clean all

14. 針對 CentOS 6.2 和 6.3，執行以下命令更新執行中的虛擬機器核心：

    針對 CentOS 6.2，執行以下命令：

        sudo yum remove kernel-firmware
        sudo yum --disableexcludes=all install kernel

    針對 CentOS 6.3 及更高版本，輸入：

        sudo yum --disableexcludes=all install kernel

15. 修改 grub 組態中的核心開機那一行，使其額外包含用於 Azure 的核心參數。作法是，在文字編輯器中開啟 /boot/grub/menu.lst，確定預設核心包含以下參數：

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300 numa=off

    這也將確保所有主控台訊息都會傳送給第一個序列埠，有助於 Azure 支援團隊進行問題偵錯程序。除此之外，由於 CentOS 6 使用之核心版本的錯誤，這也會停用 NUMA。

16. 在 /etc/sudoers 中，將以下這一行 (如果有) 標成註解：

        Defaults targetpw

17. 確定您已安裝 SSH 伺服器，並已設定為在開機時啟動。

18. 執行以下命令來安裝 Azure Linux 代理程式：

        yum install WALinuxAgent

    請注意，如果 NetworkManager 和 NetworkManager-gnome 套件沒有如步驟 2 所述遭到移除，則在安裝 WALinuxAgent 套件時會將這兩個套件移除。

19. 請勿在作業系統磁碟上建立交換空間。

    Azure Linux 代理程式可使用會在佈建於 Azure 後連接至虛擬機器的本機資源磁碟，自動設定交換空間。安裝 Azure Linux 代理程式後 (見上一個步驟)，適當修改 /etc/waagent.conf 中的以下參數：

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## 注意：請依您的需要設定此項目。

20. 執行以下命令取消佈建虛擬機器，準備將其佈建在 Azure 上：

        waagent -force -deprovision
        export HISTSIZE=0
        logout

21. 按一下 Hyper-V 管理員中的 **[關機]**。

### 準備 Ubuntu 12.04+

1.  在 Hyper-V 管理員的中間窗格中，選取虛擬機器。

2.  按一下 **[連接]** 以開啟虛擬機器的視窗。

3.  取代您的映像中目前的儲存機制，改為使用帶有升級虛擬機器時所需核心和代理程式套件的 Azure 儲存機制。此步驟會因為 Ubuntu 的版本而略有不同。

    編輯 /etc/apt/sources.list 之前，建議您先進行備份 sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak

    Ubuntu 12.04：

        sudo sed -i "s/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g" /etc/apt/sources.list
        sudo apt-add-repository 'http://archive.canonical.com/ubuntu precise-backports main'
        sudo apt-get update

    Ubuntu 12.10：

        sudo sed -i "s/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g" /etc/apt/sources.list
        sudo apt-add-repository 'http://archive.canonical.com/ubuntu quantal-backports main'
        sudo apt-get update

    Ubuntu 13.04 及更高版本：

        sudo sed -i "s/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g" /etc/apt/sources.list
        sudo apt-get update

4.  執行以下命令將作業系統更新為最新核心：

    Ubuntu 12.04：

        sudo apt-get update
        sudo apt-get install hv-kvp-daemon-init linux-backports-modules-hv-precise-virtual
        (建議) sudo apt-get dist-upgrade
        sudo reboot

    Ubuntu 12.10：

        sudo apt-get update
        sudo apt-get install hv-kvp-daemon-init linux-backports-modules-hv-quantal-virtual
        (建議) sudo apt-get dist-upgrade
        sudo reboot

    Ubuntu 13.04、13.10、14.04：

        sudo apt-get update
        sudo apt-get install hv-kvp-daemon-init
        (建議) sudo apt-get dist-upgrade
        sudo reboot

5.  Ubuntu 會在系統當機後，於 Grub 提示字元等候使用者輸入資料。為避免這個情況，請完成以下步驟：

    a) 開啟 /etc/grub.d/00\_header 檔案。

    b) 在 **make\_timeout()** 函數中搜尋 **if ["\${recordfail}" = 1 ]; then**

    c) 將這一行底下的陳述式變更為 **set timeout=5**。

    d) 執行 'sudo update-grub'。

6.  修改 Grub 的核心開機程式行，使其額外包含用於 Azure 的核心參數。作法是，在文字編輯器中開啟 /etc/default/grub，找到變數 "GRUB\_CMDLINE\_LINUX\_DEFAULT" (或視需要自行加入)，加以編輯使其包含以下參數：

        GRUB_CMDLINE_LINUX_DEFAULT="console=ttyS0 earlyprintk=ttyS0 rootdelay=300"

    儲存並關閉此檔案，然後執行 'sudo update-grub'。這將確保所有主控台訊息都會傳送給第一個序列埠，有助於 Azure 技術支援團隊進行問題偵錯程序。

7.  在 /etc/sudoers 中，將以下這一行 (如果有) 標成註解：

        Defaults targetpw

8.  確定您已安裝 SSH 伺服器，並已設定為在開機時啟動。

9.  以 sudo 執行以下命令來安裝代理程式：

        apt-get update
        apt-get install walinuxagent

    請注意，若已安裝 NetworkManager 和 NetworkManager-gnome 套件，則在安裝 walinuxagent 套件時會將它們移除。

10. 請勿在作業系統磁碟上建立交換空間。

    Azure Linux 代理程式可使用會在佈建於 Azure 後連接至虛擬機器的本機資源磁碟，自動設定交換空間。安裝 Azure Linux 代理程式後 (見上一個步驟)，適當修改 /etc/waagent.conf 中的以下參數：

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## 注意：請依您的需要設定此項目。

11. 執行以下命令取消佈建虛擬機器，準備將其佈建在 Azure 上：

        waagent -force -deprovision
        export HISTSIZE=0
        logout

12. 按一下 Hyper-V 管理員中的 **[關機]**。

### 準備 Oracle Linux 6.4+

您必須在作業系統中完成特定組態步驟，才能在 Azure 中執行虛擬機器。

1.  在 Hyper-V 管理員的中間窗格中，選取虛擬機器。

2.  按一下 **[連接]** 以開啟虛擬機器的視窗。

3.  執行以下命令解除安裝 NetworkManager：

        rpm -e --nodeps NetworkManager

    **注意：**若未安裝該套件，此命令將失敗，並發出錯誤訊息。這是預期的反應。

4.  在 `/etc/sysconfig/` 目錄中建立 **network** 檔案，檔案中包含以下文字：

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

5.  在 `/etc/sysconfig/network-scripts/` 目錄中建立 **ifcfg-eth0** 檔案，檔案中包含以下文字：

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

6.  移動 (或移除) udev 角色可防止產生乙太網路介面的靜態規則。在 Azure 或 Hyper-V 中複製虛擬機器時，這些規則會造成問題：

        # sudo mkdir -m 0700 /var/lib/waagent
        # sudo mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/
        # sudo mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/

7.  要確保開機時會啟動網路服務，可執行以下命令：

        # chkconfig network on

8.  執行以下命令來安裝 python-pyasn1：

        # sudo yum install python-pyasn1

9.  修改 grub 組態中的核心開機那一行，使其額外包含用於 Azure 的核心參數。作法是，在文字編輯器中開啟 /boot/grub/menu.lst，確定預設核心包含以下參數：

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300 numa=off

    這也將確保所有主控台訊息都會傳送給第一個序列埠，有助於 Azure 支援團隊進行問題偵錯程序。除此之外，由於 CentOS 6 使用之核心版本的錯誤，這也會停用 NUMA。

10. 在 /etc/sudoers 中，將以下這一行 (如果有) 標成註解：

        Defaults targetpw

11. 確定您已安裝 SSH 伺服器，並已設定為在開機時啟動。

12. 執行以下命令來安裝 Azure Linux 代理程式：

        yum install WALinuxAgent

    請注意，若已安裝 NetworkManager 和 NetworkManager-gnome 套件，則在安裝 WALinuxAgent 套件時會將它們移除。

13. 請勿在作業系統磁碟上建立交換空間。

    Azure Linux 代理程式可使用會在佈建於 Azure 後連接至虛擬機器的本機資源磁碟，自動設定交換空間。安裝 Azure Linux 代理程式後 (見上一個步驟)，適當修改 /etc/waagent.conf 中的以下參數：

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## 注意：請依您的需要設定此項目。

14. 執行以下命令取消佈建虛擬機器，準備將其佈建在 Azure 上：

        waagent -force -deprovision
        export HISTSIZE=0
        logout

15. 按一下 Hyper-V 管理員中的 **[關機]**。

### 準備 SUSE Linux Enterprise Server 11 SP2 和 SP3

**注意：** [SUSE Studio](http://www.susestudio.com) (英文) 可輕鬆建立及管理您用於 Azure 和 Hyper-V 的 SLES/opeSUSE 映像。此外，您可以將下列 SUSE Studio 程式庫中的官方映像，下載或複製到您自己的 SUSE Studio 帳戶來輕鬆進行自訂：

> -   [SUSE Studio 程式庫上的 SLES 11 SP3 for Azure (英文)](http://susestudio.com/a/02kbT4/sles-11-sp3-for-windows-azure)

1.  在 Hyper-V 管理員的中間窗格中，選取虛擬機器。

2.  按一下 **[連接]** 以開啟虛擬機器的視窗。

3.  加入包含最新核心和 Azure Linux 代理程式的儲存機制。執行 `zypper lr` 命令。例如，使用 SLES 11 SP3 時，輸出應類似這樣：

        # | Alias                        | Name               | Enabled | Refresh
        --+------------------------------+--------------------+---------+--------
        1 | susecloud:SLES11-SP1-Pool    | SLES11-SP1-Pool    | No      | Yes
        2 | susecloud:SLES11-SP1-Updates | SLES11-SP1-Updates | No      | Yes
        3 | susecloud:SLES11-SP2-Core    | SLES11-SP2-Core    | No      | Yes
        4 | susecloud:SLES11-SP2-Updates | SLES11-SP2-Updates | No      | Yes
        5 | susecloud:SLES11-SP3-Pool    | SLES11-SP3-Pool    | Yes     | Yes
        6 | susecloud:SLES11-SP3-Updates | SLES11-SP3-Updates | Yes     | Yes

	萬一命令傳回錯誤訊息，像是：

	    "No repositories defined.Use the 'zypper addrepo' command to add one or more repositories."

	則儲存機制可能需要重新啟用或登錄到系統。使用 suse_register 公用程式即可做到。如需詳細資訊，請參閱 [SLES 文件](https://www.suse.com/documentation/sles11/)。

	如果其中一個相關的更新儲存機制未啟用，請透過下列命令加以啟用：

	    zypper mr -e [REPOSITORY NUMBER]

	在上述情況中，適合的命令是

	    zypper mr -e 1 2 3 4

4. 將核心更新至最新的可用版本：

	    zypper up kernel-default

5. 安裝 Azure Linux 代理程式：

	    zypper up WALinuxAgent

	您會看到如下所示的訊息：

	    "There is an update candidate for 'WALinuxAgent', but it is from different vendor.
	    Use 'zypper install WALinuxAgent-1.2-1.1.noarch' to install this candidate."

	當套件的廠商從 "Microsoft Corporation" 變為 "SUSE LINUX Products GmbH, Nuernberg, Germany"，則必須如訊息中所述明確安裝套件。

	注意：WALinuxAgent 套件的版本可能略有不同。

6. 修改 grub 組態中的核心開機那一行，使其額外包含用於 Azure 的核心參數。作法是，在文字編輯器中開啟 /boot/grub/menu.lst，確定預設核心包含以下參數：

	    console=ttyS0 earlyprintk=ttyS0 rootdelay=300

	這將確保所有主控台訊息都會傳送給第一個序列埠，有助於 Azure 支援團隊進行問題偵錯程序。

7. 建議您將 /etc/sysconfig/network/dhcp 或其等同項目從 DHCLIENT\_SET\_HOSTNAME="yes" 設為 DHCLIENT\_SET\_HOSTNAME="no"

8. 在 /etc/sudoers 中，將以下這一行 (如果有) 標成註解：

	    Defaults targetpw

9. 確定您已安裝 SSH 伺服器，並已設定為在開機時啟動。

10. 請勿在作業系統磁碟上建立交換空間。

	Azure Linux 代理程式可使用會在佈建於 Azure 後連接至虛擬機器的本機資源磁碟，自動設定交換空間。安裝 Azure Linux 代理程式後 (見上一個步驟)，適當修改 /etc/waagent.conf 中的以下參數：

	    ResourceDisk.Format=y
	    ResourceDisk.Filesystem=ext4
	    ResourceDisk.MountPoint=/mnt/resource
	    ResourceDisk.EnableSwap=y
	    ResourceDisk.SwapSizeMB=2048    ## 注意：請依您的需要設定此項目。

11. 執行以下命令取消佈建虛擬機器，準備將其佈建在 Azure 上：

	    waagent -force -deprovision
	    export HISTSIZE=0
	    logout

12. 按一下 Hyper-V 管理員中的 **[關機]**。

### 準備 openSUSE 12.3+

**注意：** [SUSE Studio](http://www.susestudio.com) (英文) 可輕鬆建立及管理您用於 Azure 和 Hyper-V 的 SLES/opeSUSE 映像。此外，您可以將下列 SUSE Studio 程式庫中的官方映像，下載或複製到您自己的 SUSE Studio 帳戶來輕鬆進行自訂：

> -   [SUSE Studio 程式庫上的 openSUSE 13.1 for Azure (英文)](https://susestudio.com/a/02kbT4/opensuse-13-1-for-windows-azure)

1.  在 Hyper-V 管理員的中間窗格中，選取虛擬機器。

2.  按一下 **[連接]** 以開啟虛擬機器的視窗。

3.  將作業系統更新至最新的可用核心和修補程式

4.  在 shell 上執行 '`zypper lr`' 命令。若此命令傳回類似以下的輸出 (請注意，版本號碼可能會不同)：

        # | Alias                     | Name                      | Enabled | Refresh
        --+---------------------------+---------------------------+---------+--------
        1 | Cloud:Tools_openSUSE_12.3 | Cloud:Tools_openSUSE_12.3 | Yes     | Yes
        2 | openSUSE_12.3_OSS         | openSUSE_12.3_OSS         | Yes     | Yes
        3 | openSUSE_12.3_Updates     | openSUSE_12.3_Updates     | Yes     | Yes

    則表示儲存機制的設定如預期，不需要進行調整。

    如果命令傳回 "No repositories defined.Use the 'zypper addrepo' command to add one or more repositories."，則需要重新啟用儲存機制：

        zypper ar -f http://download.opensuse.org/distribution/12.3/repo/oss openSUSE_12.3_OSS
        zypper ar -f http://download.opensuse.org/update/12.3 openSUSE_12.3_Updates

    再次呼叫 'zypper lr' 以確認您的儲存機制已新增。

    如果其中一個相關的更新儲存機制未啟用，請透過下列命令加以啟用：

        zypper mr -e [NUMBER OF REPOSITORY]

5.  停用 DVD ROM 自動探查。

6.  安裝 Azure Linux 代理程式：

    首先，新增包含新 WALinuxAgent 的儲存機制：

        zypper ar -f -r http://download.opensuse.org/repositories/Cloud:/Tools/openSUSE_12.3/Cloud:Tools.repo

    然後，執行下列命令：

        zypper up WALinuxAgent

    執行此命令後，您會看到類似以下的訊息：

        "There is an update candidate for 'WALinuxAgent', but it is from different vendor. 
        Use 'zypper install WALinuxAgent' to install this candidate."

    這是預期會出現的訊息。當套件的廠商從 "Microsoft Corporation" 變為 "obs://build.opensuse.org/Cloud"，則必須如訊息中所述明確安裝套件。

7.  修改 grub 組態中的核心開機那一行，使其額外包含用於 Azure 的核心參數。作法是，在文字編輯器中開啟 /boot/grub/menu.lst，確定預設核心包含以下參數：

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300

    這將確保所有主控台訊息都會傳送給第一個序列埠，有助於 Azure 支援團隊進行問題偵錯程序。此外，請從核心開機程式行中移除以下參數 (如果有)：

        libata.atapi_enabled=0 reserve=0x1f0,0x8

8.  建議您將 /etc/sysconfig/network/dhcp 或其等同項目從 DHCLIENT\_SET\_HOSTNAME="yes" 設為 DHCLIENT\_SET\_HOSTNAME="no"

9.  在 /etc/sudoers 中，將以下這一行 (如果有) 標成註解：

        Defaults targetpw

10. 確定您已安裝 SSH 伺服器，並已設定為在開機時啟動。

11. 請勿在作業系統磁碟上建立交換空間。

    Azure Linux 代理程式可使用會在佈建於 Azure 後連接至虛擬機器的本機資源磁碟，自動設定交換空間。安裝 Azure Linux 代理程式後 (見上一個步驟)，適當修改 /etc/waagent.conf 中的以下參數：

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## 注意：請依您的需要設定此項目。

12. 執行以下命令取消佈建虛擬機器，準備將其佈建在 Azure 上：

        waagent -force -deprovision
        export HISTSIZE=0
        logout

13. 確定系統啟動時會執行 Azure Linux 代理程式：

        systemctl enable waagent.service

14. 按一下 Hyper-V 管理員中的 **[關機]**。

步驟 2：建立 Azure 中的儲存體帳戶
---------------------------------

儲存體帳戶代表用於存取儲存服務的最高層級命名空間，並且會與您的 Azure 訂閱相關聯。您必須有 Azure 儲存體帳戶，才能將可用於建立虛擬機器的 .vhd 檔案上傳至 Azure。您可使用 Azure 管理入口網站建立儲存體帳戶。

1.  登入 Azure 管理入口網站。

2.  按一下命令列上的 **[新增]**。

    ![建立儲存體帳戶](./media/virtual-machines-linux-create-upload-vhd/create.png)

3.  按一下 **[儲存體帳戶]**，然後按一下 **[快速建立]**。

    ![快速建立儲存體帳戶](./media/virtual-machines-linux-create-upload-vhd/storage-quick-create.png)

4.  如下所示填寫欄位：

    ![輸入儲存體帳戶詳細資料](./media/virtual-machines-linux-create-upload-vhd/storage-create-account.png)

-   在 **[URL]** 下，輸入要用於 URL 中的儲存體帳戶子網域名稱。此項目可以包含 3 至 24 個小寫字母與數字。此名稱會成為 URL 內用來將訂閱的 Blob、「佇列」或「表格」資源定址的主機名稱。

-   選擇儲存體帳戶的位置或同質群組。藉由指定同質群組，您可以將雲端服務與儲存體共同放置在同一個資料中心內。

-   決定儲存體是否要使用地理區域複寫。預設會開啟地理區域複寫。此選項會將您的資料複寫至次要位置 (無須任何成本)，以在主要位置發生無法處理的重大錯誤時，讓您的儲存體容錯移轉至次要位置。次要位置會自動指派且無法變更。若因法律規定或組織政策而需要對雲端儲存體的位置採取更嚴謹的控制，則可關閉地理區域複寫。不過請注意，如果您之後再開啟地理區域複寫，會因為要將您的現有資料複寫至次要位置，而向您收取一次性的資料轉移費用。沒有地理區域複寫功能的儲存服務會有折扣。

1.  按一下 **[建立儲存體帳戶]**。

    現在帳戶已列在 **[儲存體帳戶]** 之下。

    ![儲存體帳戶已成功建立](./media/virtual-machines-linux-create-upload-vhd/Storagenewaccount.png)

步驟 3：準備與 Azure 連接
-------------------------

您必須先在電腦與 Azure 訂閱之間建立安全連線，才能上傳 .vhd 檔案。

1.  開啟 [Azure PowerShell] 視窗。

2.  輸入：

    `Get-AzurePublishSettingsFile`

    此命令會開啟瀏覽器視窗，並自動下載包含 Azure 訂閱相關資訊和憑證的 .publishsettings 檔案。

3.  儲存 .publishsettings 檔案。

4.  輸入：

    `Import-AzurePublishSettingsFile <PathToFile>`

    其中 `<PathToFile>` 是 .publishsettings 檔案的完整路徑。

    如需詳細資訊，請參閱 [Windows Azure Cmdlet 使用者入門](http://msdn.microsoft.com/zh-tw/library/windowsazure/jj554332.aspx)。

步驟 4：上傳映像至 Azure
------------------------

上傳 .vhd 檔案時，您可以將 .vhd 檔案放在 Blob 儲存體中的任何地方。在底下的命令範例中，**BlobStorageURL** 是您在步驟 2 中建立之儲存體帳戶的 URL，**YourImagesFolder** 是您要存放映像的 Blob 儲存體內的容器。**VHDName** 是出現在管理入口網站中用以識別虛擬硬碟的標示文字。**PathToVHDFile** 是 .vhd 檔案的完整路徑和名稱。

執行下列其中一項動作：

-   在前一個步驟使用的 [Azure PowerShell] 視窗中，輸入：

    `Add-AzureVhd -Destination <BlobStorageURL>/<YourImagesFolder>/<VHDName> -LocalFilePath <PathToVHDFile>`

    如需詳細資訊，請參閱 [Add-AzureVhd](http://msdn.microsoft.com/zh-tw/library/windowsazure/dn205185.aspx) (英文)。

-   使用 Linux 命令列工具上傳映像。您可以使用以下命令上傳映像：

	    Azure vm image create <image name> --location <Location of the data center> --OS Linux <Sourcepath to the vhd>

非背書散發套件的資訊
--------------------

基本上，所有在 Azure 上執行的散發套件皆必須符合以下必要條件，才有可能在此平台中正常執行。

此必要條件清單並不完整，因為每個散發套件都不一樣，而且即使您符合以下所有條件，您仍有可能需要大幅調整映像，才能確保它可在平台上正常執行。

基於這個原因，建議您從我們其中一個[合作夥伴背書的映像](https://www.windowsazure.com/en-us/manage/linux/other-resources/endorsed-distributions/) (英文) 開始這項作業。

以下清單取代了用以建立自己的 VHD 之程序的第一個步驟：

1.  您必須確定所執行的核心包含 Hyper V 最新的 LIS 驅動程式或是已成功編譯核心 (它們的原始碼早已開放使用)。您可以在[這裡](http://go.microsoft.com/fwlink/p/?LinkID=254263&clcid=0x409) (英文) 找到驅動程式。

2.  您的核心也應該包含用於佈建映像的最新版 ATA PiiX 驅動程式，此驅動程式的核心已認可修正檔 (所含認可為 cd006086fa5d91414d8ff9ff2b78fbb593878e3c Date:Fri May 4 22:15:11 2012 +0100 ata\_piix:預設會遞延磁碟至 Hyper-V 驅動程式)

3.  您壓縮後的 intird 應該小於 40 MB (\* 我們一直在設法加大這個數字，因此現在應該不只如此了)

4.  修改 Grub 或 Grub2 中的核心開機程式行，使其包含以下參數。這也將確保所有主控台訊息都會傳送給第一個序列埠，有助於 Azure 支援團隊進行問題偵錯程序：

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300

5.  建議您將 /etc/sysconfig/network/dhcp 或其等同項目從 DHCLIENT\_SET\_HOSTNAME="yes" 設為 DHCLIENT\_SET\_HOSTNAME="no"

6.  您應該要確定掛接在核心上的所有 SCSI 裝置皆有 300 秒以上的 I/O 逾時時間。

7.  您必須依照 [Linux 代理程式指南](https://www.windowsazure.com/en-us/manage/linux/how-to-guides/linux-agent-guide/) (英文) 中的步驟安裝 Azure Linux 代理程式。此代理程式已在 Apache 2 的授權下發行，您可以在[代理程式的 GitHub 位置](http://go.microsoft.com/fwlink/p/?LinkID=250998&clcid=0x409) (英文) 取得最新版本。

8.  在 /etc/sudoers 中，將以下這一行 (如果有) 標成註解：

        Defaults targetpw

9.  確定您已安裝 SSH 伺服器，並已設定為在開機時啟動。

10. 請勿在作業系統磁碟上建立交換空間。

    Azure Linux 代理程式可使用會在佈建於 Azure 後連接至虛擬機器的本機資源磁碟，自動設定交換空間。安裝 Azure Linux 代理程式後 (見上一個步驟)，適當修改 /etc/waagent.conf 中的以下參數：

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## 注意：請依您的需要設定此項目。

11. 您必須執行以下命令來取消佈建虛擬機器：

        waagent -force -deprovision
        export HISTSIZE=0
        logout

12. 接著您必須將虛擬機器關機，然後繼續進行上傳作業。


