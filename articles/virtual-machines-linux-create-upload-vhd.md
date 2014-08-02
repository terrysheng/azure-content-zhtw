<properties linkid="manage-linux-common-task-upload-vhd" urlDisplayName="Upload a VHD" pageTitle="Create and upload a Linux VHD in Azure" metaKeywords="Azure VHD, uploading Linux VHD" description="Learn to create and upload an Azure virtual hard disk (VHD) that has the Linux operating system." metaCanonical="" services="virtual-machines" documentationCenter="" title="Creating and Uploading a Virtual Hard Disk that Contains the Linux Operating System" authors="kathydav" solutions="" manager="jeffreyg" editor="tysonn" />

Linux 운영 체제가 포함된 가상 하드 디스크 만들기 및 업로드
==========================================================

Azure의 가상 컴퓨터는 가상 컴퓨터를 만들 때 선택한 운영 체제를 실행합니다. Azure는 가상 컴퓨터의 운영 체제를 가상 하드 디스크에 VHD 형식(.vhd 파일)으로 저장합니다. 복제를 위해 준비된 운영 체제의 VHD를 이미지라고 합니다. 이 문서에서는 설치하여 범용화한 운영 체제와 함께 .vhd 파일을 업로드하여 고유의 이미지를 만드는 방법을 보여 줍니다. Azure의 디스크 및 이미지에 대한 자세한 내용은 [디스크 및 이미지 관리](http://msdn.microsoft.com/en-us/library/windowsazure/jj672979.aspx)를 참조하십시오.

**참고**: 가상 컴퓨터를 만드는 경우 운영 체제 설정을 사용자 지정하여 응용 프로그램 실행을 원활하게 할 수 있습니다. 설정한 구성은 해당 가상 컴퓨터의 디스크에 저장됩니다. 지침은 [사용자 지정 가상 컴퓨터를 만드는 방법](/en-us/manage/windows/how-to-guides/custom-create-a-vm/)(영문)을 참조하십시오.

**중요**: [이 문서](http://support.microsoft.com/kb/2805216)에 지정된 대로 보증된 분산 중 하나가 구성 세부 정보와 함께 사용되는 경우에만 Linux OS를 실행하는 가상 컴퓨터에 Azure 플랫폼 SLA가 적용됩니다. Azure 이미지 갤러리에 제공된 모든 Linux 분산은 필요한 구성이 포함된 보증된 분산입니다.

필수 조건
---------

이 문서에서는 사용자에게 다음 항목이 있다고 가정합니다.

-   **관리 인증서** - VHD를 업로드할 구독에 필요한 관리 인증서를 만들어 .cer 파일로 내보냈습니다. 인증서 만들기에 대한 자세한 내용은 [Azure용 관리 인증서 만들기](http://msdn.microsoft.com/library/windowsazure/gg551722.aspx)를 참조하십시오.

-   **.vhd 파일에 설치된 Linux 운영 체제.** - 지원되는 Linux 운영 체제가 가상 하드 디스크에 설치되어 있습니다. .vhd 파일을 만드는 도구는 여러 가지가 있습니다. Hyper-V와 같은 가상화 솔루션을 사용하여 .vhd 파일을 만들고 운영 체제를 설치할 수 있습니다. 지침은 [Hyper-V 역할 설치 및 가상 시스템 구성](http://technet.microsoft.com/library/hh846766.aspx)을 참조하십시오.

    **중요**: 새 VHDX 형식은 Azure에서 지원되지 않습니다. Hyper-V 관리자 또는 convert-vhd cmdlet을 사용하여 디스크를 VHD 형식으로 변환할 수 있습니다.

    보증된 분산 목록은 [Azure의 Linux-보증된 분산](../linux-endorsed-distributions)(영문)을 참조하십시오. 또한 [보증되지 않는 분산에 대한 정보](#nonendorsed)는 이 문서의 끝에 있는 섹션을 참조하십시오.

-   **Linux Azure 명령줄 도구.** Linux 운영 체제를 사용하여 이미지를 만드는 경우 이 도구를 사용하여 VHD 파일을 업로드합니다. 도구를 다운로드하려면 [Linux 및 Mac용 Azure 명령줄 도구](http://go.microsoft.com/fwlink/?LinkID=253691&clcid=0x409)를 참조하십시오.

-   **Add-AzureVhd cmdlet**은, Azure PowerShell 모듈의 일부입니다. 모듈을 다운로드하려면 [Azure 다운로드](/en-us/develop/downloads/)를 참조하십시오. 참조 정보는 [Add-AzureVhd](http://msdn.microsoft.com/library/windowsazure/dn495173.aspx)(영문)를 참조하십시오.

**모든 분산에서 다음 내용에 유의하십시오.**

-   Linux 시스템 설치 시 LVM(설치 기본값인 경우가 많음)이 아닌 표준 파티션을 사용하는 것이 좋습니다. 이렇게 하면 특히 문제 해결을 위해 OS 디스크를 다른 VM에 연결해야 하는 경우 복제된 VM과 LVM 이름이 충돌하지 않도록 방지합니다.

-   Azure Linux 에이전트(waagent)는 NetworkManager와 호환되지 않습니다. 네트워킹 구성은 ifup/ifdown 스크립트를 통해 제어할 수 있어야 합니다. 분산을 통해 제공되는 RPM/Deb 패키지는 대부분 NetworkManager를 waagent 패키지에 대한 충돌로 구성하므로 Linux 에이전트 패키지 설치 시 NetworkManager를 제거합니다. Azure Linux 에이전트에서는 python-pyasn1 패키지도 설치되어야 합니다.

-   크기가 큰 VM의 경우 Linux 커널 버전 2.6.37 이전의 버그로 인해 NUMA가 지원되지 않습니다. waagent를 수동으로 설치하면 자동으로 Linux 커널 GRUB 구성에서 NUMA를 사용하지 않도록 설정합니다. 이 문제는 주로 업스트림 Red Hat 2.6.32 커널을 사용하는 분산에 영향을 줍니다.

-   설치 시 스왑 파티션을 만들지 않는 것이 좋습니다. Azure Linux 에이전트를 사용하여 스왑 공간을 구성할 수 있습니다. 또한 [Microsoft 웹 사이트](http://go.microsoft.com/fwlink/?LinkID=253692&clcid=0x409)에 사용 가능한 패치가 없는 주요 Linux 커널은 Azure 가상 컴퓨터와 함께 사용하지 않는 것이 좋습니다(많은 수의 현재 분산/커널에는 이 픽스가 이미 포함되어 있을 수 있음).

-   모든 VHD 크기는 1MB의 배수여야 합니다.

이 작업에는 다음 단계가 포함됩니다.

-   [1단계: 업로드할 이미지 준비](#prepimage)
-   [2단계: Azure에서 저장소 계정 만들기](#createstorage)
-   [3단계: Azure 연결 준비](#connect)
-   [4단계: Azure에 이미지 업로드](#upload)

1단계: 업로드할 이미지 준비
---------------------------

### CentOS 6.2+ 준비

가상 컴퓨터를 Azure에서 실행하려면 운영 체제에서 특정 구성 단계를 완료해야 합니다.

1.  Hyper-V 관리자의 가운데 창에서 가상 컴퓨터를 선택합니다.

2.  **연결**을 클릭하여 가상 컴퓨터 창을 엽니다.

3.  다음 명령을 실행하여 NetworkManager를 제거합니다.

         rpm -e --nodeps NetworkManager

    **참고:** 패키지가 아직 설치되어 있지 않은 경우 이 명령이 실패하고 오류 메시지가 표시됩니다. 예상된 동작입니다.

4.  다음 텍스트가 포함된 **network** 파일을 `/etc/sysconfig/` 디렉터리에 만듭니다.

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

5.  다음 텍스트가 포함된 **ifcfg-eth0** 파일을 `/etc/sysconfig/network-scripts/` 디렉터리에 만듭니다.

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

6.  이더넷 인터페이스에 대한 정적 규칙을 생성하지 않도록 방지하는 udev 규칙을 이동(또는 제거)합니다. 이러한 규칙은 Azure 또는 Hyper-V에서 가상 컴퓨터를 복제하는 경우 문제를 발생시킵니다.

        # sudo mkdir -m 0700 /var/lib/waagent
        # sudo mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/
        # sudo mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/

7.  다음 명령을 실행하여 부팅 시 네트워크 서비스가 시작되도록 합니다.

         # chkconfig network on

8.  **CentOS 6.2 또는 6.3만**: Linux 통합 서비스 드라이버 설치

    **참고:** 이 단계는 CentOS 6.2 및 6.3에만 유효합니다. CentOS 6.4+에서는 Linux 통합 서비스를 커널에서 이미 사용할 수 있습니다.

    a) [다운로드 센터](http://www.microsoft.com/en-us/download/details.aspx?id=34603)에서 Linux 통합 서비스 드라이버가 포함된 .iso 파일을 엽니다.

    b) Hyper-V 관리자의 **작업** 창에서 **설정**을 클릭합니다.

    ![Hyper-V 설정 열기](./media/virtual-machines-linux-create-upload-vhd/settings.png)

    c) **하드웨어** 창에서 **IDE 컨트롤러 1**을 클릭합니다.

    ![DVD 드라이브와 설치 미디어 추가](./media/virtual-machines-linux-create-upload-vhd/installiso.png)

    d) **IDE 컨트롤러** 상자에서 **DVD 드라이브**를 클릭하고 **추가**를 클릭합니다.

    e) **이미지 파일**을 선택하고 **Linux IC v3.2.iso**를 찾은 다음 **열기**를 클릭합니다.

    f) **설정** 페이지에서 **확인**을 클릭합니다.

    g) **연결**을 클릭하여 가상 컴퓨터 창을 엽니다.

    h) 명령 프롬프트 창에서 다음 명령을 입력합니다.

        mount /dev/cdrom /media
        /media/install.sh
        reboot

9.  다음 명령을 실행하여 python-pyasn1을 설치합니다.

         # sudo yum install python-pyasn1

10. /etc/yum.repos.d/CentOS-Base.repo 파일을 다음 텍스트로 바꿉니다.

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

11. /etc/yum.conf에 다음 줄을 추가합니다.

        http_caching=packages

    다음 줄은 CentOS 6.2 & 6.3에서만 추가합니다.

        exclude=kernel*

12. "/etc/yum/pluginconf.d/fastestmirror.conf" 파일을 편집하여 yum 모듈 "fastestmirror"를 사용하지 않도록 설정하고 [main] 아래에 다음을 입력합니다.

        set enabled=0

13. 다음 명령을 실행하여 현재 yum 메타데이터를 지웁니다.

        yum clean all

14. CentOS 6.2 및 6.3의 경우 다음 명령을 실행하여 실행 중인 VM 커널을 업데이트합니다.

    CentOS 6.2에서는 다음 명령을 실행합니다.

        sudo yum remove kernel-firmware
        sudo yum --disableexcludes=all install kernel

    CentOS 6.3+에서는 다음 명령을 입력합니다.

        sudo yum --disableexcludes=all install kernel

15. Azure용 커널 매개 변수를 추가로 포함하려면 grub 구성에서 커널 부팅 줄을 수정합니다. 이 작업을 수행하려면 /boot/grub/menu.lst를 텍스트 편집기에서 열고 다음 매개 변수가 기본 커널에 포함되어 있는지 확인합니다.

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300 numa=off

    이렇게 하면 모든 콘솔 메시지가 첫 번째 직렬 포트로 전송되므로 Azure 지원에서 문제를 디버깅하는 데에도 도움이 될 수 있습니다. 또한 CentOS 6에서 사용하는 커널 버전의 버그로 인해 NUMA를 사용하지 않도록 설정합니다.

16. /etc/sudoers가 있는 경우 다음 줄을 주석으로 처리합니다.

        Defaults targetpw

17. SSH 서버가 설치되어 부팅 시 시작되도록 구성되어 있는지 확인합니다.

18. 다음 명령을 실행하여 Azure Linux 에이전트를 설치합니다.

        yum install WALinuxAgent

    WALinuxAgent 패키지를 설치하면 NetworkManager 및 NetworkManager-gnome 패키지가 2단계에서 설명된 대로 아직 제거되지 않은 경우 이러한 패키지를 제거합니다.

19. OS 디스크에 스왑 공간을 만들지 마십시오.

    Azure Linux 에이전트는 Azure에서 프로비전한 후 VM에 연결된 로컬 리소스 디스크를 사용하여 자동으로 스왑 공간을 구성할 수 있습니다. Azure Linux 에이전트를 설치한 후(이전 단계 참조) /etc/waagent.conf에서 다음 매개 변수를 적절하게 수정합니다.

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

20. 다음 명령을 실행하여 가상 컴퓨터의 프로비전을 해제하고 Azure에서 프로비전할 준비를 합니다.

        waagent -force -deprovision
        export HISTSIZE=0
        logout

21. Hyper-V 관리자에서 **종료**를 클릭합니다.

### Ubuntu 12.04+ 준비

1.  Hyper-V 관리자의 가운데 창에서 가상 컴퓨터를 선택합니다.

2.  **연결**을 클릭하여 가상 컴퓨터 창을 엽니다.

3.  이미지의 현재 리포지토리를 바꾸어 VM을 업그레이드하는 데 필요한 커널 및 에이전트 패키지가 있는 azure 리포지토리를 사용합니다. 단계는 Ubuntu 버전에 따라 약간씩 다릅니다.

    /etc/apt/sources.list를 편집하기 전에 백업 sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak을 만드는 것이 좋습니다.

    Ubuntu 12.04:

        sudo sed -i "s/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g" /etc/apt/sources.list
        sudo apt-add-repository 'http://archive.canonical.com/ubuntu precise-backports main'
        sudo apt-get update

    Ubuntu 12.10:

        sudo sed -i "s/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g" /etc/apt/sources.list
        sudo apt-add-repository 'http://archive.canonical.com/ubuntu quantal-backports main'
        sudo apt-get update

    Ubuntu 13.04+:

        sudo sed -i "s/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g" /etc/apt/sources.list
        sudo apt-get update

4.  다음 명령을 실행하여 최신 커널로 운영 체제를 업데이트합니다.

    Ubuntu 12.04:

         sudo apt-get update
         sudo apt-get install hv-kvp-daemon-init linux-backports-modules-hv-precise-virtual
         (recommended) sudo apt-get dist-upgrade
         sudo reboot

    Ubuntu 12.10:

         sudo apt-get update
         sudo apt-get install hv-kvp-daemon-init linux-backports-modules-hv-quantal-virtual
         (recommended) sudo apt-get dist-upgrade
         sudo reboot

    Ubuntu 13.04, 13.10 및 14.04:

         sudo apt-get update
         sudo apt-get install hv-kvp-daemon-init
         (recommended) sudo apt-get dist-upgrade
         sudo reboot

5.  Ubuntu는 시스템 작동 중지 후 사용자 입력을 위한 Grub 프롬프트를 기다립니다. 이를 예방하려면 다음 단계를 완료하십시오.

    a) /etc/grub.d/00\_header 파일을 엽니다.

    b) 함수 **make\_timeout()**에서 **if ["\${recordfail}" = 1 ]; then**을 검색합니다.

    c) 해당 줄 아래 문을 **set timeout=5**로 변경합니다.

    d) 'sudo update-grub'을 실행합니다.

6.  Azure용 커널 매개 변수를 추가로 포함하려면 Grub의 커널 부팅 줄을 수정합니다. 이 작업을 수행하려면 /etc/default/grub을 텍스트 편집기에서 열고 변수 "GRUB\_CMDLINE\_LINUX\_DEFAULT"를 찾아서(또는 필요한 경우 추가함) 다음 매개 변수가 포함되도록 편집합니다.

         GRUB_CMDLINE_LINUX_DEFAULT="console=ttyS0 earlyprintk=ttyS0 rootdelay=300"

    이 파일을 저장하고 닫은 다음 'sudo update-grub'을 실행합니다. 이렇게 하면 모든 콘솔 메시지가 첫 번째 직렬 포트로 전송되므로 Azure 기술 지원에서 문제를 디버깅하는 데 도움이 될 수 있습니다.

7.  /etc/sudoers가 있는 경우 다음 줄을 주석으로 처리합니다.

        Defaults targetpw

8.  SSH 서버가 설치되어 부팅 시 시작되도록 구성되어 있는지 확인합니다.

9.  다음 명령을 sudo로 실행하여 에이전트를 설치합니다.

        apt-get update
        apt-get install walinuxagent

    walinuxagent 패키지를 설치하면 NetworkManager 및 NetworkManager-gnome 패키지가 설치되어 있는 경우 이러한 패키지를 제거합니다.

10. OS 디스크에 스왑 공간을 만들지 마십시오.

    Azure Linux 에이전트는 Azure에서 프로비전한 후 VM에 연결된 로컬 리소스 디스크를 사용하여 자동으로 스왑 공간을 구성할 수 있습니다. Azure Linux 에이전트를 설치한 후(이전 단계 참조) /etc/waagent.conf에서 다음 매개 변수를 적절하게 수정합니다.

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

11. 다음 명령을 실행하여 가상 컴퓨터의 프로비전을 해제하고 Azure에서 프로비전할 준비를 합니다.

        waagent -force -deprovision
        export HISTSIZE=0
        logout

12. Hyper-V 관리자에서 **종료**를 클릭합니다.

### Oracle Linux 6.4+ 준비

가상 컴퓨터를 Azure에서 실행하려면 운영 체제에서 특정 구성 단계를 완료해야 합니다.

1.  Hyper-V 관리자의 가운데 창에서 가상 컴퓨터를 선택합니다.

2.  **연결**을 클릭하여 가상 컴퓨터 창을 엽니다.

3.  다음 명령을 실행하여 NetworkManager를 제거합니다.

         rpm -e --nodeps NetworkManager

    **참고:** 패키지가 아직 설치되어 있지 않은 경우 이 명령이 실패하고 오류 메시지가 표시됩니다. 예상된 동작입니다.

4.  다음 텍스트가 포함된 **network** 파일을 `/etc/sysconfig/` 디렉터리에 만듭니다.

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

5.  다음 텍스트가 포함된 **ifcfg-eth0** 파일을 `/etc/sysconfig/network-scripts/` 디렉터리에 만듭니다.

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

6.  이더넷 인터페이스에 대한 정적 규칙을 생성하지 않도록 방지하는 udev 규칙을 이동(또는 제거)합니다. 이러한 규칙은 Azure 또는 Hyper-V에서 가상 컴퓨터를 복제하는 경우 문제를 발생시킵니다.

        # sudo mkdir -m 0700 /var/lib/waagent
        # sudo mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/
        # sudo mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/

7.  다음 명령을 실행하여 부팅 시 네트워크 서비스가 시작되도록 합니다.

         # chkconfig network on

8.  다음 명령을 실행하여 python-pyasn1을 설치합니다.

         # sudo yum install python-pyasn1

9.  Azure용 커널 매개 변수를 추가로 포함하려면 grub 구성에서 커널 부팅 줄을 수정합니다. 이 작업을 수행하려면 /boot/grub/menu.lst를 텍스트 편집기에서 열고 다음 매개 변수가 기본 커널에 포함되어 있는지 확인합니다.

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300 numa=off

    이렇게 하면 모든 콘솔 메시지가 첫 번째 직렬 포트로 전송되므로 Azure 지원에서 문제를 디버깅하는 데에도 도움이 될 수 있습니다. 또한 CentOS 6에서 사용하는 커널 버전의 버그로 인해 NUMA를 사용하지 않도록 설정합니다.

10. /etc/sudoers가 있는 경우 다음 줄을 주석으로 처리합니다.

        Defaults targetpw

11. SSH 서버가 설치되어 부팅 시 시작되도록 구성되어 있는지 확인합니다.

12. 다음 명령을 실행하여 Azure Linux 에이전트를 설치합니다.

        yum install WALinuxAgent

    WALinuxAgent 패키지를 설치하면 NetworkManager 및 NetworkManager-gnome 패키지가 설치되어 있는 경우 이러한 패키지를 제거합니다.

13. OS 디스크에 스왑 공간을 만들지 마십시오.

    Azure Linux 에이전트는 Azure에서 프로비전한 후 VM에 연결된 로컬 리소스 디스크를 사용하여 자동으로 스왑 공간을 구성할 수 있습니다. Azure Linux 에이전트를 설치한 후(이전 단계 참조) /etc/waagent.conf에서 다음 매개 변수를 적절하게 수정합니다.

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

14. 다음 명령을 실행하여 가상 컴퓨터의 프로비전을 해제하고 Azure에서 프로비전할 준비를 합니다.

        waagent -force -deprovision
        export HISTSIZE=0
        logout

15. Hyper-V 관리자에서 **종료**를 클릭합니다.

### SUSE Linux Enterprise Server 11 SP2 및 SP3 준비

**참고:** [SUSE Studio](http://www.susestudio.com)에서는 Azure 및 Hyper-V용 SLES/opeSUSE 이미지를 쉽게 만들고 관리할 수 있습니다. 또한 간편한 사용자 지정을 위해 SUSE Studio Gallery에 있는 다음 공식 이미지를 자체 SUSE Studio 계정으로 다운로드하거나 복제할 수 있습니다.

> -   [SUSE Studio Gallery의 Azure용 SLES 11 SP3](http://susestudio.com/a/02kbT4/sles-11-sp3-for-windows-azure)

1.  Hyper-V 관리자의 가운데 창에서 가상 컴퓨터를 선택합니다.

2.  **연결**을 클릭하여 가상 컴퓨터 창을 엽니다.

3.  최신 커널 및 Azure Linux 에이전트가 포함된 리포지토리를 추가합니다. `zypper lr` 명령을 실행합니다. 예를 들어 SLES 11 SP3의 출력은 다음과 유사합니다.

         # | Alias                        | Name               | Enabled | Refresh
         --+------------------------------+--------------------+---------+--------
         1 | susecloud:SLES11-SP1-Pool    | SLES11-SP1-Pool    | No      | Yes
         2 | susecloud:SLES11-SP1-Updates | SLES11-SP1-Updates | No      | Yes
         3 | susecloud:SLES11-SP2-Core    | SLES11-SP2-Core    | No      | Yes
         4 | susecloud:SLES11-SP2-Updates | SLES11-SP2-Updates | No      | Yes
         5 | susecloud:SLES11-SP3-Pool    | SLES11-SP3-Pool    | Yes     | Yes
         6 | susecloud:SLES11-SP3-Updates | SLES11-SP3-Updates | Yes     | Yes

 	이 명령에서 다음과 같은 오류 메시지를 반환하는 경우가 있습니다.

		"No repositories defined. Use the 'zypper addrepo' command to add one or more repositories."

	then the repositories may need to be re-enabled or the system registered.  This can be done via the suse_register utility.  For more information, please see the [SLES documentation](https://www.suse.com/documentation/sles11/).

   관련된 업데이트 리포지토리 중 하나가 사용하도록 설정되어 있지 않은 경우 다음 명령을 사용하여 사용하도록 설정합니다.

		zypper mr -e [REPOSITORY NUMBER]

   위와 같은 경우 올바른 명령은 다음과 같습니다.

		zypper mr -e 1 2 3 4

1.  커널을 사용 가능한 최신 버전으로 업데이트합니다.

         zypper up kernel-default

2.  Azure Linux 에이전트를 설치합니다.

         zypper up WALinuxAgent

    다음과 유사한 메시지가 표시됩니다.

         "There is an update candidate for 'WALinuxAgent', but it is from different vendor.
         Use 'zypper install WALinuxAgent-1.2-1.1.noarch' to install this candidate."

    패키지 공급업체가 "Microsoft Corporation"에서 "SUSE LINUX Products GmbH, Nuernberg, Germany"로 변경되었기 때문에 메시지에 언급된 대로 패키지를 명시적으로 설치해야 합니다.

    참고: WALinuxAgent 패키지 버전은 약간 다를 수 있습니다.

3.  Azure용 커널 매개 변수를 추가로 포함하려면 grub 구성에서 커널 부팅 줄을 수정합니다. 이 작업을 수행하려면 /boot/grub/menu.lst를 텍스트 편집기에서 열고 다음 매개 변수가 기본 커널에 포함되어 있는지 확인합니다.

         console=ttyS0 earlyprintk=ttyS0 rootdelay=300

    이렇게 하면 모든 콘솔 메시지가 첫 번째 직렬 포트로 전송되므로 Azure 지원에서 문제를 디버깅하는 데 도움이 될 수 있습니다.

4.  /etc/sysconfig/network/dhcp 또는 이와 동등한 항목을 DHCLIENT\_SET\_HOSTNAME="yes"에서 DHCLIENT\_SET\_HOSTNAME="no"로 설정하는 것이 좋습니다.

5.  /etc/sudoers가 있는 경우 다음 줄을 주석으로 처리합니다.

        Defaults targetpw

6.  SSH 서버가 설치되어 부팅 시 시작되도록 구성되어 있는지 확인합니다.

7.  OS 디스크에 스왑 공간을 만들지 마십시오.

    Azure Linux 에이전트는 Azure에서 프로비전한 후 VM에 연결된 로컬 리소스 디스크를 사용하여 자동으로 스왑 공간을 구성할 수 있습니다. Azure Linux 에이전트를 설치한 후(이전 단계 참조) /etc/waagent.conf에서 다음 매개 변수를 적절하게 수정합니다.

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

8.  다음 명령을 실행하여 가상 컴퓨터의 프로비전을 해제하고 Azure에서 프로비전할 준비를 합니다.

        waagent -force -deprovision
        export HISTSIZE=0
        logout

9.  Hyper-V 관리자에서 **종료**를 클릭합니다.

### openSUSE 12.3+ 준비

**참고:** [SUSE Studio](http://www.susestudio.com)에서는 Azure 및 Hyper-V용 SLES/opeSUSE 이미지를 쉽게 만들고 관리할 수 있습니다. 또한 간편한 사용자 지정을 위해 SUSE Studio Gallery에 있는 다음 공식 이미지를 자체 SUSE Studio 계정으로 다운로드하거나 복제할 수 있습니다.

> -   [SUSE Studio Gallery의 Azure용 openSUSE 13.1](https://susestudio.com/a/02kbT4/opensuse-13-1-for-windows-azure)

1.  Hyper-V 관리자의 가운데 창에서 가상 컴퓨터를 선택합니다.

2.  **연결**을 클릭하여 가상 컴퓨터 창을 엽니다.

3.  운영 체제를 사용 가능한 최신 커널 및 패치로 업데이트합니다.

4.  셸에서 '`zypper lr`' 명령을 실행합니다. 이 명령에서 다음과 유사한 출력을 반환하는 경우가 있습니다(버전 번호는 다를 수 있음).

         # | Alias                     | Name                      | Enabled | Refresh
         --+---------------------------+---------------------------+---------+--------
         1 | Cloud:Tools_openSUSE_12.3 | Cloud:Tools_openSUSE_12.3 | Yes     | Yes
         2 | openSUSE_12.3_OSS         | openSUSE_12.3_OSS         | Yes     | Yes
         3 | openSUSE_12.3_Updates     | openSUSE_12.3_Updates     | Yes     | Yes

    이 경우 리포지토리는 예상대로 구성되며 조정이 필요하지 않습니다.

    이 경우 명령에서 "No repositories defined. Use the 'zypper addrepo' command to add one or more repositories"를 반환합니다. 그러면 리포지토리를 다시 사용하도록 설정해야 합니다.

         zypper ar -f http://download.opensuse.org/distribution/12.3/repo/oss openSUSE_12.3_OSS
         zypper ar -f http://download.opensuse.org/update/12.3 openSUSE_12.3_Updates

    'zypper lr'을 다시 호출하여 리포지토리가 추가되었는지 확인합니다.

    관련된 업데이트 리포지토리 중 하나가 사용하도록 설정되어 있지 않은 경우 다음 명령을 사용하여 사용하도록 설정합니다.

         zypper mr -e [NUMBER OF REPOSITORY]

5.  자동 DVD ROM 검색을 사용하지 않도록 설정합니다.

6.  Azure Linux 에이전트를 설치합니다.

    먼저 새 WALinuxAgent가 포함된 리포지토리를 추가합니다.

        zypper ar -f -r http://download.opensuse.org/repositories/Cloud:/Tools/openSUSE_12.3/Cloud:Tools.repo

    다음 명령을 실행합니다.

        zypper up WALinuxAgent

    이 명령을 실행하면 다음과 유사한 메시지가 표시됩니다.

        "There is an update candidate for 'WALinuxAgent', but it is from different vendor. 
        Use 'zypper install WALinuxAgent' to install this candidate."

    예상된 메시지입니다. 패키지 공급업체가 "Microsoft Corporation"에서 "obs://build.opensuse.org/Cloud"로 변경되었기 때문에 메시지에 언급된 대로 패키지를 명시적으로 설치해야 합니다.

7.  Azure용 커널 매개 변수를 추가로 포함하려면 grub 구성에서 커널 부팅 줄을 수정합니다. 이 작업을 수행하려면 /boot/grub/menu.lst를 텍스트 편집기에서 열고 다음 매개 변수가 기본 커널에 포함되어 있는지 확인합니다.

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300

    이렇게 하면 모든 콘솔 메시지가 첫 번째 직렬 포트로 전송되므로 Azure 지원에서 문제를 디버깅하는 데 도움이 될 수 있습니다. 또한 커널 부팅 줄에 다음 매개 변수가 있는 경우 해당 매개 변수를 제거합니다.

        libata.atapi_enabled=0 reserve=0x1f0,0x8

8.  /etc/sysconfig/network/dhcp 또는 이와 동등한 항목을 DHCLIENT\_SET\_HOSTNAME="yes"에서 DHCLIENT\_SET\_HOSTNAME="no"로 설정하는 것이 좋습니다.

9.  /etc/sudoers가 있는 경우 다음 줄을 주석으로 처리합니다.

        Defaults targetpw

10. SSH 서버가 설치되어 부팅 시 시작되도록 구성되어 있는지 확인합니다.

11. OS 디스크에 스왑 공간을 만들지 마십시오.

    Azure Linux 에이전트는 Azure에서 프로비전한 후 VM에 연결된 로컬 리소스 디스크를 사용하여 자동으로 스왑 공간을 구성할 수 있습니다. Azure Linux 에이전트를 설치한 후(이전 단계 참조) /etc/waagent.conf에서 다음 매개 변수를 적절하게 수정합니다.

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

12. 다음 명령을 실행하여 가상 컴퓨터의 프로비전을 해제하고 Azure에서 프로비전할 준비를 합니다.

        waagent -force -deprovision
        export HISTSIZE=0
        logout

13. 시작할 때 Azure Linux 에이전트가 실행되는지 확인합니다.

        systemctl enable waagent.service

14. Hyper-V 관리자에서 **종료**를 클릭합니다.

2단계: Azure에서 저장소 계정 만들기
-----------------------------------

저장소 계정은 저장소 서비스에 액세스하는 데 필요한 가장 높은 수준의 네임스페이스를 나타내며 Azure 구독과 관련이 있습니다. 가상 컴퓨터를 만드는 데 사용할 수 있는 .vhd 파일을 Azure에 업로드하려면 Azure에 저장소 계정이 있어야 합니다. Azure 관리 포털을 사용하여 저장소 계정을 만들 수 있습니다.

1.  Azure 관리 포털에 로그인합니다.

2.  명령 모음에서 **새로 만들기**를 클릭합니다.

    ![저장소 계정 만들기](./media/virtual-machines-linux-create-upload-vhd/create.png)

3.  **저장소 계정**을 클릭한 후 **빠른 생성**을 클릭합니다.

    ![저장소 계정 빠른 생성](./media/virtual-machines-linux-create-upload-vhd/storage-quick-create.png)

4.  다음과 같이 필드를 채웁니다.

    ![저장소 계정 세부 정보 입력](./media/virtual-machines-linux-create-upload-vhd/storage-create-account.png)

-   **URL**에서 저장소 계정의 URL에 사용할 하위 도메인 이름을 입력합니다. 이 입력에는 3-24자의 소문자와 숫자를 사용할 수 있습니다. 이 이름은 구독에 대한 Blob, 큐 또는 테이블 리소스의 주소를 지정하는 데 사용되는 URL 내의 호스트 이름이 됩니다.

-   저장소 계정의 위치 또는 선호도 그룹을 선택합니다. 선호도 그룹을 지정하여 저장소와 동일한 데이터 센터에 클라우드 서비스를 배치할 수 있습니다.

-   저장소 계정에 지역에서 복제를 사용할지 여부를 결정합니다. 지역에서 복제는 기본적으로 설정되어 있습니다. 이 옵션을 사용하면 추가 비용 없이 보조 위치로 데이터를 복제하므로 기본 위치에서 처리할 수 없는 심각한 장애가 발생하는 경우 저장소에서 보조 위치로 장애 조치(Failover)합니다. 보조 위치는 자동으로 할당되며 변경될 수 없습니다. 법적 필요 또는 조직 정책에 따라 클라우드 기반 저장소의 위치를 더 엄격하게 제어해야 하는 경우 지역에서 복제를 해제할 수 있습니다. 그러나 나중에 지역에서 복제를 설정하는 경우 보조 위치로 기존 데이터를 복제하는 데 일회성 데이터 전송 요금이 부과됩니다. 지역에서 복제를 사용하지 않는 저장소 서비스는 할인하여 제공됩니다.

1.  **저장소 계정 만들기**를 클릭합니다.

    이제 계정이 **저장소 계정**에 나열되어 있습니다.

    ![저장소 계정 만들기 성공](./media/virtual-machines-linux-create-upload-vhd/Storagenewaccount.png)

3단계: Azure 연결 준비
----------------------

.vhd 파일을 업로드하려면 컴퓨터와 Azure의 구독 사이에 보안 연결을 설정해야 합니다.

1.  Azure PowerShell 창을 엽니다.

2.  다음을 입력합니다.

    `Get-AzurePublishSettingsFile`

    이 명령은 브라우저 창을 열고 Azure 구독에 대한 정보와 인증서가 포함된 .publishsettings 파일을 자동으로 다운로드합니다.

3.  .publishsettings 파일을 저장합니다.

4.  다음을 입력합니다.

    `Import-AzurePublishSettingsFile <PathToFile>`

    여기서 `<PathToFile>`는 .publishsettings 파일의 전체 경로입니다.

    자세한 내용은 [Azure Cmdlets 시작](http://msdn.microsoft.com/en-us/library/windowsazure/jj554332.aspx)을 참조하십시오.

4단계: Azure에 이미지 업로드
----------------------------

.vhd 파일을 업로드하는 경우 Blob 저장소 내 임의의 위치에 .vhd 파일을 배치할 수 있습니다. 다음 명령 예제에서 **BlobStorageURL**은 2단계에서 만든 저장소 계정의 URL이고, **YourImagesFolder**는 이미지를 저장할 Blob 저장소 내 컨테이너입니다. **VHDName**은 가상 하드 디스크를 식별하기 위해 관리 포털에 표시되는 레이블입니다. **PathToVHDFile**은 .vhd 파일의 전체 경로 및 이름입니다.

다음 중 하나를 수행합니다.

-   이전 단계에서 사용한 Azure PowerShell 창에서 다음을 입력합니다.

    `Add-AzureVhd -Destination <BlobStorageURL>/<YourImagesFolder>/<VHDName> -LocalFilePath <PathToVHDFile>`

    자세한 내용은 [Add-AzureVhd](http://msdn.microsoft.com/en-us/library/windowsazure/dn205185.aspx)(영문)를 참조하십시오.

-   Linux 명령줄 도구를 사용하여 이미지를 업로드합니다. 다음 명령을 사용하여 이미지를 업로드할 수 있습니다.

          Azure vm image create <image name> --location <Location of the data center> --OS Linux <Sourcepath to the vhd>

보증되지 않는 분산에 대한 정보
------------------------------

기본적으로 Azure에서 실행되는 모든 분산이 플랫폼에서 올바르게 실행되려면 다음 필수 조건이 충족되어야 합니다.

모든 분산은 서로 다르므로 이 목록은 포괄적이지 않습니다. 아래 기준이 모두 충족되어도 플랫폼에서 올바르게 실행되도록 하려면 여전히 이미지를 상당히 조정해야 할 수 있습니다.

이러한 이유로 Microsoft [파트너 보증 이미지](https://www.windowsazure.com/en-us/manage/linux/other-resources/endorsed-distributions/)(영문) 중 하나로 시작하는 것을 권장합니다.

아래 목록은 고유 VHD를 만드는 프로세스의 1단계를 바꿉니다.

1.  Hyper V의 최신 LIS 드라이버를 통합하거나 성공적으로 컴파일한 커널을 실행하고 있는지 확인해야 합니다(드라이버는 오픈 소싱됨). 드라이버는 [이 위치](http://go.microsoft.com/fwlink/p/?LinkID=254263&clcid=0x409)에 있습니다.

2.  커널은 또한 이미지를 프로비전하는 데 사용되며 커밋 cd006086fa5d91414d8ff9ff2b78fbb593878e3c Date: Fri May 4 22:15:11 2012 +0100 ata\_piix로 커널에 커밋된 픽스가 있는 최신 버전의 ATA PiiX 드라이버를 포함해야 합니다. 기본적으로 Hyper-V 드라이버로 디스크를 연기합니다.

3.  압축 intird는 40MB 미만이어야 합니다(\* 이 숫자를 증가시키기 위해 계속 노력하고 있으므로 현재는 이미 만료된 사항일 수 있음).

4.  다음 매개 변수를 포함하려면 Grub 또는 Grub2의 커널 부팅 줄을 수정합니다. 이렇게 하면 모든 콘솔 메시지가 첫 번째 직렬 포트로 전송되므로 Azure 지원에서 문제를 디버깅하는 데에도 도움이 될 수 있습니다.

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300

5.  /etc/sysconfig/network/dhcp 또는 이와 동등한 항목을 DHCLIENT\_SET\_HOSTNAME="yes"에서 DHCLIENT\_SET\_HOSTNAME="no"로 설정하는 것이 좋습니다.

6.  커널에 탑재된 모든 SCSI 장치에 300초 이상의 I/O 시간 제한이 포함되었는지 확인해야 합니다.

7.  [Linux 에이전트 가이드](https://www.windowsazure.com/en-us/manage/linux/how-to-guides/linux-agent-guide/)(영문)의 단계에 따라 Azure Linux 에이전트를 설치해야 합니다. 에이전트는 Apache 2 라이선스에 따라 릴리스되며 최신 비트는 [에이전트 GitHub 위치](http://go.microsoft.com/fwlink/p/?LinkID=250998&clcid=0x409)(영문)에서 가져올 수 있습니다.

8.  /etc/sudoers가 있는 경우 다음 줄을 주석으로 처리합니다.

        Defaults targetpw

9.  SSH 서버가 설치되어 부팅 시 시작되도록 구성되어 있는지 확인합니다.

10. OS 디스크에 스왑 공간을 만들지 마십시오.

    Azure Linux 에이전트는 Azure에서 프로비전한 후 VM에 연결된 로컬 리소스 디스크를 사용하여 자동으로 스왑 공간을 구성할 수 있습니다. Azure Linux 에이전트를 설치한 후(이전 단계 참조) /etc/waagent.conf에서 다음 매개 변수를 적절하게 수정합니다.

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

11. 다음 명령을 실행하여 가상 컴퓨터의 프로비전을 해제해야 합니다.

        waagent -force -deprovision
        export HISTSIZE=0
        logout

12. 그런 다음 가상 컴퓨터를 종료하고 업로드를 계속합니다.


