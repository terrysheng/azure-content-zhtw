<properties linkid="manage-linux-common-task-upload-vhd" urlDisplayName="Upload a VHD" pageTitle="Create and upload a Linux VHD in Azure" metaKeywords="Azure VHD, uploading Linux VHD" description="Learn to create and upload an Azure virtual hard disk (VHD) that has the Linux operating system." metaCanonical="" services="virtual-machines" documentationCenter="" title="Creating and Uploading a Virtual Hard Disk that Contains the Linux Operating System" authors="kathydav" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="06/05/2014" ms.author="kathydav, szarkos"></tags>

# 建立及上傳包含 Linux 作業系統的虛擬硬碟

本文說明如何建立及上傳虛擬硬碟 (VHD)，以便用它做為您自己的映像，在 Azure 中建立虛擬機器。您將了解如何準備作業系統，以便用它根據該映像建立多台虛擬機器。

> [WACOM.NOTE] 您不需要有任何 Azure VM 的使用經驗，也能完成本文的步驟。但您必須要有 Azure 帳戶。只需要幾分鐘的時間，您就可以建立免費試用帳戶。如需詳細資料，請參閱＜[建立 Azure 帳戶][]＞。

Azure 中的虛擬機器會執行根據您建立虛擬機器時所選擇映像的作業系統。您的映像會以 VHD 格式的 .vhd 檔案儲存在儲存體帳戶中。如需 Azure 中的磁碟和映像的詳細資訊，請參閱[管理磁碟和映像][]。

在建立虛擬機器時，您可以自訂部分作業系統設定，使用它們適合您要執行的應用程式。如需指示，請參閱[如何建立自訂虛擬機器][]。

**重要事項**：只有在其中一個背書散發套件使用[本文][]中指定的組態詳細資料時，才會對執行 Linux OS 的虛擬機器套用 Azure 平台 SLA。Azure 映像庫中提供的所有 Linux 散發套件，皆為使用必要組態的背書散發套件。

## 必要條件

本文假設您具有下列項目：

-   **管理憑證** - 您已針對要上傳 VHD 的訂閱建立管理憑證，並將此憑證匯出至 .cer 檔案。如需建立憑證的詳細資訊，請參閱[建立 Azure 的管理憑證][]。

-   **以 .vhd 檔案安裝的 Linux 作業系統。** - 您已將支援的 Linux 作業系統安裝至虛擬硬碟。有多項工具可用來建立 .vhd 檔案。您可以使用虛擬化解決方案 (例如 Hyper-V) 建立 .vhd 檔案，並安裝作業系統。如需指示，請參閱[安裝 Hyper-V 角色及設定虛擬機器][]。

    **重要事項**：Azure 不支援較新的 VHDX 格式。您可以使用 Hyper-V 管理員或 convert-vhd Cmdlet，將磁碟轉換為 VHD 格式。

    如需背書散發套件清單，請參閱 [Linux on Azure 背書散發套件][]。或者，請參閱本文最後一節中的[非背書散發套件的資訊][]。

-   **Linux Azure 命令列工具。**如果您要使用 Linux 作業系統建立映像，您可以使用此工具來上傳 VHD 檔案。若要下載此工具，請參閱[適用於 Mac 和 Linux 的 Azure 命令列工具][]。

-   **Add-AzureVhd Cmdlet**，這是 Azure PowerShell 模組的一部分。若要下載此模組，請參閱 [Azure 下載][]。如需參照資訊，請參閱 [Add-AzureVhd][]。

**對於所有的散發套件，請注意下列事項：**

-   安裝 Linux 系統時，我們建議您使用標準磁碟分割而不是 LVM (常是許多安裝的預設設定)。這可避免 LVM 與複製之虛擬機器的名稱衝突，特別是為了疑難排解而需要將作業系統磁碟連接至其他虛擬機器時。

-   Azure Linux 代理程式 (waagent) 與 NetworkManager 不相容。網路組態應可透過 ifup/ifdown 指令碼控制。散發套件提供的大部分 RPM/Deb 套件會將 NetworkManager 設定為 waagent 套件的衝突，因此會在您安裝 Linux 代理程式套件時解除安裝 NetworkManager。Azure Linux 代理程式也需要安裝 python-pyasn1 套件。

-   因為 Linux Kernel 2.6.37 以下版本的一個錯誤，較大的虛擬機器不支援 NUMA。手動安裝 waagent 將針對 Linux Kernel 自動停用 GRUB 組態中的 NUMA 。這個問題主要會影響使用上游 Red Hat 2.6.32 kernel 的散發套件。

-   建議您不要在安裝期間建立 SWAP 磁碟分割。您可以使用 Azure Linux 代理程式來設定 SWAP 空間。此外，我們也建議您不要對 Azure 虛擬機器使用未在 [Microsoft 網站][] 上提供修補程式的主流 Linux 核心 (許多目前的散發套件/核心可能以包含此修正程式)。

-   所有 VHD 的大小都必須是 1 MB 的倍數。

此工作包含下列步驟：

-   [步驟 1：準備要上傳的映像][]
-   [步驟 2：在 Azure 中建立儲存體帳戶][]
-   [步驟 3：準備 Azure 的連線][]
-   [步驟 4：將映像上傳至 Azure][]

## <span id="prepimage"></span> </a>步驟 1：準備要上傳的映像

### 準備 CentOS 6.2+

請務必在作業系統中進行這些組態步驟，以便虛擬機器可以用來做為映像

1.  在 Hyper-V 管理員的中央窗格中，選取虛擬機器。

2.  按一下 **[連接]**，以開啟虛擬機器的視窗。

3.  執行下列命令以解除安裝 NetworkManager：

        rpm -e --nodeps NetworkManager

    **注意：**如果尚未安裝封裝，此命令將會失敗，並出現錯誤訊息。這是預期行為。

4.  在 `/etc/sysconfig/` 目錄中，建立名為 **network** 且包含下列文字的檔案：

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

5.  在 `/etc/sysconfig/network-scripts/` 目錄中，建立名為 **ifcfg-eth0** 且包含下列文字的檔案：

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

8.  **僅限 CentOS 6.2 或 6.3**：安裝 Linux Integration Services 的驅動程式

    **注意：**此步驟僅適用於 CentOS 6.2 和 6.3。在 CentOS 6.4+ 中，Linux Integration Services 已是核心中的可用項目。

    a) 從[下載中心][]取得包含 Linux Integration Services 之驅動程式的 .iso 檔案。

    b) 在 Hyper-V 管理員的 **[動作]** 窗格中，按一下 **[設定]**。

    ![開啟 Hyper-V 設定][]

    c) 在 **[硬體]** 窗格中，按一下 **[IDE 控制器 1]**。

    ![使用安裝媒體新增 DVD 光碟機][]

    d) 在 **[IDE 控制器]** 方塊中，按一下 **[DVD 光碟機]**，再按 **[新增]**。

    e) 選取 **[映像檔]**，瀏覽至 **Linux IC v3.2.iso**，然後按一下 **[開啟]**。

    f) 在 **[設定]** 頁面中，按一下 **[確定]**。

    g) 按一下 **[連接]**，以開啟虛擬機器的視窗。

    h) 在 [命令提示字元] 視窗中，輸入下列命令：

        mount /dev/cdrom /media
        /media/install.sh
        reboot

9.  執行下列命令以安裝 python-pyasn1：

        # sudo yum install python-pyasn1

10. 將其 /etc/yum.repos.d/CentOS-Base.repo 檔案取代為下列文字

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

12. 編輯檔案 "/etc/yum/pluginconf.d/fastestmirror.conf" 以停用 yum 模組 "fastestmirror"，然後在 [main] 下方輸入下列內容

        set enabled=0

13. 執行下列命令，以清除目前的 yum 中繼資料：

        yum clean all

14. 針對 CentOS 6.2 和 6.3，執行下列命令以更新執行中 VM 的核心

    針對 CentOS 6.2，執行下列命令：

        sudo yum remove kernel-firmware
        sudo yum --disableexcludes=all install kernel

    針對 CentOS 6.3+ 輸入下列內容：

        sudo yum --disableexcludes=all install kernel

15. 修改 grub 組態中的核心開機那一行，使其額外包含用於 Azure 的核心參數。作法是，在文字編輯器中開啟 /boot/grub/menu.lst，確定預設核心包含以下參數：

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300 numa=off

    這也將確保所有主控台訊息都會傳送給第一個序列埠，有助於 Azure 支援團隊進行問題偵錯程序。除此之外，由於 CentOS 6 使用之核心版本的錯誤，這也會停用 NUMA。

16. 在 /etc/sudoers 中註解化以下一行 (如果存在)：

        Defaults targetpw

17. 確定您已安裝 SSH 伺服器，並已設定為在開機時啟動。

18. 執行以下命令來安裝 Azure Linux 代理程式：

        yum install WALinuxAgent

    請注意，如果 NetworkManager 和 NetworkManager-gnome 套件沒有如步驟 2 所述遭到移除，則在安裝 WALinuxAgent 套件時會將這兩個套件移除。

19. 請勿在作業系統磁碟上建立交換空間。

    Azure Linux 代理程式可在 VM 佈建於 Azure 後，使用附加至 VM 的本機資源磁碟自動設定交換空間。請注意，資源磁碟是*暫存*磁碟，可能會在 VM 取消佈建時清空。安裝 Azure Linux 代理程式 (請參閱上一個步驟) 後，請在 /etc/waagent.conf 中適當修改下列參數：

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

20. 執行下列命令，以取消佈建虛擬機器，並準備將其佈建於 Azure 上：

        waagent -force -deprovision
        export HISTSIZE=0
        logout

21. 在 Hyper-V 管理員中按一下 **[關閉]**。

### 準備 Ubuntu 12.04+

1.  在 Hyper-V 管理員的中央窗格中，選取虛擬機器。

2.  按一下 **[連接]**，以開啟虛擬機器的視窗。

3.  取代您的映像中目前的儲存機制，以使用具有核心的 azure 儲存機制，以及您升級 VM 時所需的代理程式封裝。此步驟會隨著 Ubuntu 版本而略有不同。

    在編輯 /etc/apt/sources.list 前，建議先備份
     sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak

    Ubuntu 12.04：

        sudo sed -i "s/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g" /etc/apt/sources.list
        sudo apt-add-repository 'http://archive.canonical.com/ubuntu precise-backports main'
        sudo apt-get update

    Ubuntu 12.10：

        sudo sed -i "s/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g" /etc/apt/sources.list
        sudo apt-add-repository 'http://archive.canonical.com/ubuntu quantal-backports main'
        sudo apt-get update

    Ubuntu 13.04+：

        sudo sed -i "s/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g" /etc/apt/sources.list
        sudo apt-get update

4.  執行下列命令，將作業系統更新為最新的核心：

    Ubuntu 12.04：

        sudo apt-get update
        sudo apt-get install hv-kvp-daemon-init linux-backports-modules-hv-precise-virtual
        (recommended) sudo apt-get dist-upgrade
        sudo reboot

    Ubuntu 12.10：

        sudo apt-get update
        sudo apt-get install hv-kvp-daemon-init linux-backports-modules-hv-quantal-virtual
        (recommended) sudo apt-get dist-upgrade
        sudo reboot

    Ubuntu 13.04、13.10、14.04：

        sudo apt-get update
        sudo apt-get install hv-kvp-daemon-init
        (recommended) sudo apt-get dist-upgrade
        sudo reboot

5.  在系統當機後，Ubuntu 會在出現使用者輸入的 Grub 提示時等候。若要防止此情況發生，請完成下列步驟：

    a) 開啟 /etc/grub.d/00\_header 檔案。

    b) 在函數 **make\_timeout()** 中，搜尋 **if ["${recordfail}" = 1 ]; then**

    c) 將此行下的陳述式變更為 **set timeout=5**。

    d) 執行 'sudo update-grub'。

6.  修改 Grub 的核心開機程式行，使其額外包含用於 Azure 的核心參數。作法是，在文字編輯器中開啟 /etc/default/grub，找到變數 "GRUB\_CMDLINE\_LINUX\_DEFAULT" (或視需要自行加入)，加以編輯使其包含以下參數：

        GRUB_CMDLINE_LINUX_DEFAULT="console=ttyS0 earlyprintk=ttyS0 rootdelay=300"

    儲存並關閉此檔案，然後執行 'sudo update-grub'。這將確保所有主控台訊息都會傳送給第一個序列埠，有助於 Azure 技術支援團隊進行問題偵錯程序。

7.  在 /etc/sudoers 中註解化以下一行 (如果存在)：

        Defaults targetpw

8.  確定您已安裝 SSH 伺服器，並已設定為在開機時啟動。

9.  使用 sudo 執行下列命令，以安裝代理程式：

        apt-get update
        apt-get install walinuxagent

    請注意，若已安裝 NetworkManager 和 NetworkManager-gnome 套件，則在安裝 walinuxagent 套件時會將它們移除。

10. 請勿在作業系統磁碟上建立交換空間。

    Azure Linux 代理程式可在 VM 佈建於 Azure 後，使用附加至 VM 的本機資源磁碟自動設定交換空間。請注意，資源磁碟是*暫存*磁碟，可能會在 VM 取消佈建時清空。安裝 Azure Linux 代理程式 (請參閱上一個步驟) 後，請在 /etc/waagent.conf 中適當修改下列參數：

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

11. 執行下列命令，以取消佈建虛擬機器，並準備將其佈建於 Azure 上：

        waagent -force -deprovision
        export HISTSIZE=0
        logout

12. 在 Hyper-V 管理員中按一下 **[關閉]**。

### 準備 Oracle Linux 6.4+

您必須在作業系統中完成特定組態步驟，虛擬機器才能在 Azure 中執行。

1.  在 Hyper-V 管理員的中央窗格中，選取虛擬機器。

2.  按一下 **[連接]**，以開啟虛擬機器的視窗。

3.  執行下列命令以解除安裝 NetworkManager：

        rpm -e --nodeps NetworkManager

    **注意：**如果尚未安裝封裝，此命令將會失敗，並出現錯誤訊息。這是預期行為。

4.  在 `/etc/sysconfig/` 目錄中，建立名為 **network** 且包含下列文字的檔案：

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

5.  在 `/etc/sysconfig/network-scripts/` 目錄中，建立名為 **ifcfg-eth0** 且包含下列文字的檔案：

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

8.  執行下列命令以安裝 python-pyasn1：

        # sudo yum install python-pyasn1

9.  修改 grub 組態中的核心開機那一行，使其額外包含用於 Azure 的核心參數。作法是，在文字編輯器中開啟 /boot/grub/menu.lst，確定預設核心包含以下參數：

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300 numa=off

    這也將確保所有主控台訊息都會傳送給第一個序列埠，有助於 Azure 支援團隊進行問題偵錯程序。除此之外，由於 CentOS 6 使用之核心版本的錯誤，這也會停用 NUMA。

10. 在 /etc/sudoers 中註解化以下一行 (如果存在)：

        Defaults targetpw

11. 確定您已安裝 SSH 伺服器，並已設定為在開機時啟動。

12. 執行以下命令來安裝 Azure Linux 代理程式：

        yum install WALinuxAgent

    請注意，若已安裝 NetworkManager 和 NetworkManager-gnome 套件，則在安裝 WALinuxAgent 套件時會將它們移除。

13. 請勿在作業系統磁碟上建立交換空間。

    Azure Linux 代理程式可在 VM 佈建於 Azure 後，使用附加至 VM 的本機資源磁碟自動設定交換空間。安裝 Azure Linux 代理程式 (請參閱上一個步驟) 後，請在 /etc/waagent.conf 中適當修改下列參數：

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

14. 執行下列命令，以取消佈建虛擬機器，並準備將其佈建於 Azure 上：

        waagent -force -deprovision
        export HISTSIZE=0
        logout

15. 在 Hyper-V 管理員中按一下 **[關閉]**。

### 準備 SUSE Linux Enterprise Server 11 SP2 和 SP3

**注意：** [SUSE Studio][] (英文) 可輕鬆建立及管理您用於 Azure 和 Hyper-V 的 SLES/opeSUSE 映像。此外，您可以將下列 SUSE Studio 程式庫中的官方映像，下載或複製到您自己的 SUSE Studio 帳戶來輕鬆進行自訂 -- [SUSE Studio Gallery 上的 SLES 11 SP3 for Azure][] (英文)。

1.  在 Hyper-V 管理員的中央窗格中，選取虛擬機器。

2.  按一下 **[連接]**，以開啟虛擬機器的視窗。

3.  新增包含最新核心和 Azure Linux 代理程式的儲存機制。輸入命令 `zypper lr`。以 SLES 11 SP3 為例，輸出應會類似於下列內容：

        # | Alias                        | Name               | Enabled | Refresh
        --+------------------------------+--------------------+---------+--------
        1 | susecloud:SLES11-SP1-Pool    | SLES11-SP1-Pool    | No      | Yes
        2 | susecloud:SLES11-SP1-Updates | SLES11-SP1-Updates | No      | Yes
        3 | susecloud:SLES11-SP2-Core    | SLES11-SP2-Core    | No      | Yes
        4 | susecloud:SLES11-SP2-Updates | SLES11-SP2-Updates | No      | Yes
        5 | susecloud:SLES11-SP3-Pool    | SLES11-SP3-Pool    | Yes     | Yes
        6 | susecloud:SLES11-SP3-Updates | SLES11-SP3-Updates | Yes     | Yes

    如果命令傳回如下的錯誤訊息：

        "No repositories defined. Use the 'zypper addrepo' command to add one or more repositories."

    then the repositories may need to be re-enabled or the system registered.This can be done via the suse\_register utility.如需詳細資訊，請參閱 [SLES 文件][] (英文)。

    如果有其中一個相關的更新儲存機制未啟用，請使用下列命令加以啟用：

        zypper mr -e [REPOSITORY NUMBER]

    在前述案例中，適當的命令會是

        zypper mr -e 1 2 3 4

1.  將核心更新為最新的可用版本：

        zypper up kernel-default

2.  安裝 Azure Linux 代理程式：

        zypper up WALinuxAgent

    您可能會看到如下的訊息：

        "There is an update candidate for 'WALinuxAgent', but it is from different vendor.
        Use 'zypper install WALinuxAgent-1.2-1.1.noarch' to install this candidate."

    當封裝的供應商已從 "Microsoft Corporation" 變更為 "SUSE LINUX Products GmbH, Nuernberg, Germany" 時，您就必須依照訊息的指示明確安裝封裝。

    注意：WALinuxAgent 封裝的版本可能略有不同。

3.  修改 grub 組態中的核心開機那一行，使其額外包含用於 Azure 的核心參數。作法是，在文字編輯器中開啟 /boot/grub/menu.lst，確定預設核心包含以下參數：

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300

    這將確保所有主控台訊息都會傳送給第一個序列埠，有助於 Azure 支援團隊進行問題偵錯程序。

4.  建議您將 /etc/sysconfig/network/dhcp 或對等項目從 DHCLIENT\_SET\_HOSTNAME="yes" 改設為 DHCLIENT\_SET\_HOSTNAME="no"

5.  在 /etc/sudoers 中註解化以下一行 (如果存在)：

        Defaults targetpw

6.  確定 SSH 伺服器已安裝，並設定成在開機時啟動

7.  請不要在 OS 磁碟上建立交換空間

    Azure Linux 代理程式可在 VM 佈建於 Azure 後，使用附加至 VM 的本機資源磁碟自動設定交換空間。請注意，資源磁碟是*暫存*磁碟，可能會在 VM 取消佈建時清空。安裝 Azure Linux 代理程式 (請參閱上一個步驟) 後，請在 /etc/waagent.conf 中適當修改下列參數：

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

8.  執行下列命令，以取消佈建虛擬機器，並準備將其佈建於 Azure 上：

        waagent -force -deprovision
        export HISTSIZE=0
        logout

9.  在 Hyper-V 管理員中按一下 **[關閉]**。

### 準備 openSUSE 12.3+

**注意：** [SUSE Studio][] (英文) 可輕鬆建立及管理您用於 Azure 和 Hyper-V 的 SLES/opeSUSE 映像。此外，您可以將下列 SUSE Studio 程式庫中的官方映像，下載或複製到您自己的 SUSE Studio 帳戶來輕鬆進行自訂：

> -   [SUSE Studio Gallery 上的 openSUSE 13.1 for Azure][]

1.  在 Hyper-V 管理員的中央窗格中，選取虛擬機器。

2.  按一下 **[連接]**，以開啟虛擬機器的視窗。

3.  將作業系統更新為最新的可用核心和修補程式

4.  在 Shell 上執行命令 '`zypper lr`'。若此命令傳回類似以下的輸出 (請注意，版本號碼可能會不同)：

        # | Alias                     | Name                      | Enabled | Refresh
        --+---------------------------+---------------------------+---------+--------
        1 | Cloud:Tools_openSUSE_12.3 | Cloud:Tools_openSUSE_12.3 | Yes     | Yes
        2 | openSUSE_12.3_OSS         | openSUSE_12.3_OSS         | Yes     | Yes
        3 | openSUSE_12.3_Updates     | openSUSE_12.3_Updates     | Yes     | Yes

    則儲存機制的設定符合預期，不需要任何調整。

    如果此命令傳回 "No repositories defined.Use the 'zypper addrepo' command to add one or more repositories."，則必須重新啟用儲存機制：

        zypper ar -f http://download.opensuse.org/distribution/12.3/repo/oss openSUSE_12.3_OSS
        zypper ar -f http://download.opensuse.org/update/12.3 openSUSE_12.3_Updates

    重新呼叫 'zypper lr'，以驗證您的儲存機制已新增。

    如果有其中一個相關的更新儲存機制未啟用，請使用下列命令加以啟用：

        zypper mr -e [NUMBER OF REPOSITORY]

1.  停用自動 DVD ROM 探查。

2.  安裝 Azure Linux 代理程式：

    首先，請新增包含新 WALinuxAgent 的儲存機制：

        zypper ar -f -r http://download.opensuse.org/repositories/Cloud:/Tools/openSUSE_12.3/Cloud:Tools.repo

    然後，執行下列命令：

        zypper up WALinuxAgent

    執行此命令後，可能會出現如下的訊息：

        "There is an update candidate for 'WALinuxAgent', but it is from different vendor. 
        Use 'zypper install WALinuxAgent' to install this candidate."

    這是預期的訊息。當封裝的供應商已從 "Microsoft Corporation" 變更為 "Microsoft Corporation" to "obs://build.opensuse.org/Cloud" 時，您就必須依照訊息的指示明確安裝封裝。

3.  修改 grub 組態中的核心開機那一行，使其額外包含用於 Azure 的核心參數。作法是，在文字編輯器中開啟 /boot/grub/menu.lst，確定預設核心包含以下參數：

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300

    這將確保所有主控台訊息都會傳送給第一個序列埠，有助於 Azure 支援團隊進行問題偵錯程序。此外，請從核心開機程式行中移除以下參數 (如果有)：

        libata.atapi_enabled=0 reserve=0x1f0,0x8

4.  建議您將 /etc/sysconfig/network/dhcp 或對等項目從 DHCLIENT\_SET\_HOSTNAME="yes" 改設為 DHCLIENT\_SET\_HOSTNAME="no"

5.  在 /etc/sudoers 中註解化以下一行 (如果存在)：

        Defaults targetpw

6.  確定 SSH 伺服器已安裝，並設定成在開機時啟動

7.  請不要在 OS 磁碟上建立交換空間

    Azure Linux 代理程式可在 VM 佈建於 Azure 後，使用附加至 VM 的本機資源磁碟自動設定交換空間。請注意，資源磁碟是*暫存*磁碟，可能會在 VM 取消佈建時清空。安裝 Azure Linux 代理程式 (請參閱上一個步驟) 後，請在 /etc/waagent.conf 中適當修改下列參數：

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

8.  執行下列命令，以取消佈建虛擬機器，並準備將其佈建於 Azure 上：

        waagent -force -deprovision
        export HISTSIZE=0
        logout

9.  確定 Azure Linux 代理程式會在啟動時執行：

        systemctl enable waagent.service

10. 在 Hyper-V 管理員中按一下 **[關閉]**。

## <span id="createstorage"></span> </a>步驟 2：在 Azure 中建立儲存體帳戶

儲存體帳戶代表用以存取儲存服務、並且與您的 Azure 訂閱相關聯的最高層級命名空間。必須要有 Azure 中的儲存體帳戶，才能將可用來建立虛擬機器的 .vhd 檔案上傳至 Azure。儲存體帳戶可使用 Azure 管理入口網站來建立。

1.  登入 Azure 管理入口網站。

2.  按一下命令列上的 [新增]。

    ![建立儲存體帳戶][]

3.  按一下 [儲存體帳戶]，然後按一下 [快速建立]。

    ![快速建立儲存體帳戶][]

4.  依照下列方式填入欄位：

    ![輸入儲存體帳戶詳細資料][]

 -   在 **URL** 下，為儲存體帳戶輸入要在 URL 中使用的子網域名稱。此項目可以包含 3 至 24 個小寫字母與數字。此名稱會成為 URL 內用來為訂閱的 Blob、「佇列」或「資料表」資源定址的主機名稱。

 -   選擇儲存體帳戶的位置或同質群組。藉由指定同質群組，您可以將雲端服務並置在與儲存體相同的資料中心。

 -   決定儲存體帳戶是否要使用地理區域複寫。依預設會開啟地理區域複寫。此選項可讓您免費將資料複寫至次要位置，使您在遇到無法在主要位置中處理的重大錯誤時，可將儲存體容錯移轉至次要位置。次要位置會自動指派，且無法變更。如果法律規定或組織原則要求您必須更嚴密地掌控雲端儲存體的位置，您可以關閉地理區域複寫。但請注意，如果您後續又開啟地理區域複寫，在您將現有的資料複寫至次要位置時，將會產生一次性的資料傳輸費用。不含地理區域複寫的儲存服務會有相對的折扣。

1.  按一下 [建立儲存體帳戶]。

    帳戶此時會列在 **[儲存體帳戶]** 下。

    ![已成功建立儲存體帳戶][]

## <span id="#connect"></span> </a>步驟 3：準備 Azure 的連線

您必須先在電腦與 Azure 中的訂閱之間建立安全連線，才能上傳 .vhd 檔案。

1.  開啟 Azure PowerShell 視窗。

2.  輸入：

    `Get-AzurePublishSettingsFile`

    此命令會開啟瀏覽器視窗，並自動下載含有您的 Azure 訂閱所需之資訊和憑證的 .publishsettings 檔案。

3.  儲存 .publishsettings 檔案。

4.  輸入：

    `Import-AzurePublishSettingsFile <PathToFile>`

    其中，`<PathToFile>` 是 .publishsettings 檔案的完整路徑。

    如需詳細資訊，請參閱[開始使用 Azure Cmdlet][]

## <span id="upload"></span> </a>步驟 4：將映像上傳至 Azure

在上傳 .vhd 檔案時，您可以將 .vhd 檔案放在 Blob 儲存體中的任一處。在下列命令範例中，**BlobStorageURL** 是您在步驟 2 中建立之儲存體帳戶的 URL，**YourImagesFolder** 則是您要用來儲存映像之 Blob 儲存體中的容器。**VHDName** 是管理入口網站中用來識別虛擬硬碟的顯示標籤。**PathToVHDFile** 是 .vhd 檔案的完整路徑和名稱。

執行下列其中一項：

-   從您在上一個步驟使用的 Azure PowerShell 視窗中，輸入：

    `Add-AzureVhd -Destination <BlobStorageURL>/<YourImagesFolder>/<VHDName> -LocalFilePath <PathToVHDFile>`

    如需詳細資訊，請參閱 [Add-AzureVhd][1]。

-   使用 Linux 命令列工具上傳映像。您可以使用下列命令來上傳映像：

        Azure vm image create <image name> --location <Location of the data center> --OS Linux <Sourcepath to the vhd>

## <span id="nonendorsed"></span> </a>非背書散發套件的資訊

基本上，所有執行於 Azure 的散發套件都必須符合下列必要條件，才能在平台上正確執行。

此處所列並非完整清單，因為每個散發套件都不同；且即使您符合下列所有條件，仍很可能需要詳加審視您的映像，以確保它可在平台上正確執行。

正因如此，建議您從我們的其中一個[合作夥伴背書映像][]開始著手進行。

下列清單取代了讓您自行建立 VHD 之程序的步驟 1：

1.  您必須確定所執行的核心包含 Hyper V 最新的 LIS 驅動程式或是已成功編譯核心 (它們的原始碼早已開放使用)。驅動程式位於[此位置上][]

2.  您的核心也應包含用來佈建映像的最新版 ATA PiiX 驅動程式，且驅動程式後續應具有適用於核心的修正程式：commit cd006086fa5d91414d8ff9ff2b78fbb593878e3c Date:Fri May 4 22:15:11 2012 +0100 ata\_piix:defer disks to the Hyper-V drivers by default

3.  您的壓縮 intird 應小於 40 MB (\* 我們持續設法提高此數目，因此這個數據現在可能已不合用)

4.  在 Grub 或 Grub2 中修改核心開機行，以加入下列參數。這樣也可確保主控台訊息能傳送至第一個序列埠，以協助 Azure 支援偵錯問題：

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300

5.  建議您將 /etc/sysconfig/network/dhcp 或對等項目從 DHCLIENT\_SET\_HOSTNAME="yes" 改設為 DHCLIENT\_SET\_HOSTNAME="no"

6.  您應確定所有掛接在核心中的 SCSI 裝置都具有 300 秒或更長的 I/O 逾時。

7.  您必須依照 [Linux 代理程式指南][]中的步驟安裝 Azure Linux 代理程式。此代理程式已在 Apache 2 授權下發行，您可以在[代理程式 GitHub 位置][]上取得最新版本

8.  在 /etc/sudoers 中註解化以下一行 (如果存在)：

        Defaults targetpw

9.  確定 SSH 伺服器已安裝，並設定成在開機時啟動

10. 請不要在 OS 磁碟上建立交換空間

    Azure Linux 代理程式可在 VM 佈建於 Azure 後，使用附加至 VM 的本機資源磁碟自動設定交換空間。請注意，資源磁碟是*暫存*磁碟，可能會在 VM 取消佈建時清空。安裝 Azure Linux 代理程式 (請參閱上一個步驟) 後，請在 /etc/waagent.conf 中適當修改下列參數：

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

11. 您必須執行下列命令，以取消佈建虛擬機器：

        waagent -force -deprovision
        export HISTSIZE=0
        logout

12. 接著，您必須關閉虛擬機器，並繼續執行上傳。

  [建立 Azure 帳戶]: http://www.windowsazure.com/zh-tw/develop/php/tutorials/create-a-windows-azure-account/
  [管理磁碟和映像]: http://msdn.microsoft.com/zh-tw/library/windowsazure/jj672979.aspx
  [如何建立自訂虛擬機器]: /zh-tw/manage/windows/how-to-guides/custom-create-a-vm/
  [本文]: http://support.microsoft.com/kb/2805216
  [建立 Azure 的管理憑證]: http://msdn.microsoft.com/library/windowsazure/gg551722.aspx
  [安裝 Hyper-V 角色及設定虛擬機器]: http://technet.microsoft.com/library/hh846766.aspx
  [Linux on Azure 背書散發套件]: ../linux-endorsed-distributions
  [非背書散發套件的資訊]: #nonendorsed
  [適用於 Mac 和 Linux 的 Azure 命令列工具]: http://go.microsoft.com/fwlink/?LinkID=253691&clcid=0x409
  [Azure 下載]: /zh-tw/develop/downloads/
  [Add-AzureVhd]: http://msdn.microsoft.com/library/windowsazure/dn495173.aspx
  [Microsoft 網站]: http://go.microsoft.com/fwlink/?LinkID=253692&clcid=0x409
  [步驟 1：準備要上傳的映像]: #prepimage
  [步驟 2：在 Azure 中建立儲存體帳戶]: #createstorage
  [步驟 3：準備 Azure 的連線]: #connect
  [步驟 4：將映像上傳至 Azure]: #upload
  [下載中心]: http://www.microsoft.com/zh-tw/download/details.aspx?id=34603
  [開啟 Hyper-V 設定]: ./media/virtual-machines-linux-create-upload-vhd/settings.png
  [使用安裝媒體新增 DVD 光碟機]: ./media/virtual-machines-linux-create-upload-vhd/installiso.png
  [SUSE Studio]: http://www.susestudio.com
  [SUSE Studio Gallery 上的 SLES 11 SP3 for Azure]: http://susestudio.com/a/02kbT4/sles-11-sp3-for-windows-azure
  [SLES 文件]: https://www.suse.com/documentation/sles11/
  [SUSE Studio Gallery 上的 openSUSE 13.1 for Azure]: https://susestudio.com/a/02kbT4/opensuse-13-1-for-windows-azure
  [建立儲存體帳戶]: ./media/virtual-machines-linux-create-upload-vhd/create.png
  [快速建立儲存體帳戶]: ./media/virtual-machines-linux-create-upload-vhd/storage-quick-create.png
  [輸入儲存體帳戶詳細資料]: ./media/virtual-machines-linux-create-upload-vhd/storage-create-account.png
  [已成功建立儲存體帳戶]: ./media/virtual-machines-linux-create-upload-vhd/Storagenewaccount.png
  [開始使用 Azure Cmdlet]: http://msdn.microsoft.com/zh-tw/library/windowsazure/jj554332.aspx
  [1]: http://msdn.microsoft.com/zh-tw/library/windowsazure/dn205185.aspx
  [合作夥伴背書映像]: https://www.windowsazure.com/zh-tw/manage/linux/other-resources/endorsed-distributions/
  [此位置上]: http://go.microsoft.com/fwlink/p/?LinkID=254263&clcid=0x409
  [Linux 代理程式指南]: https://www.windowsazure.com/zh-tw/manage/linux/how-to-guides/linux-agent-guide/
  [代理程式 GitHub 位置]: http://go.microsoft.com/fwlink/p/?LinkID=250998&clcid=0x409
