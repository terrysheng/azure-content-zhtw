<properties
	pageTitle="準備 Debian Linux VHD | Microsoft Azure"
	description="了解如何建立 Debian 7 和 8 VHD，以供部署於 Azure 中。"
	services="virtual-machines-linux"
	documentationCenter=""
	authors="SuperScottz"
	manager="timlt"
	editor=""
    tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/01/2015"
	ms.author="mingzhan"/>




# 準備適用於 Azure 的 Debian VHD

## 先決條件
本節假設您已使用從 [Debian 網站](https://www.debian.org/distrib/)下載的 .iso 檔案將 Debian Linux 作業系統安裝至虛擬硬碟。可建立 .vhd 檔案的工具有多種，Hyper-V 只是其中一個範例。如需 Hyper-V 的使用指示，請參閱[安裝 Hyper-V 角色及設定虛擬機器](https://technet.microsoft.com/library/hh846766.aspx)。


## 安裝注意事項

- Azure 不支援較新的 VHDX 格式。您可以使用 Hyper-V 管理員或 **convert-vhd** Cmdlet，將磁碟轉換為 VHD 格式。
- 安裝 Linux 系統時，建議您使用標準磁碟分割而不是 LVM (常是許多安裝的預設設定)。這可避免 LVM 與複製之虛擬機器的名稱衝突，特別是為了疑難排解而需要將作業系統磁碟連接至其他虛擬機器時。如果願意，您可以在資料磁碟上使用 LVM 或 RAID。
- 請勿在作業系統磁碟上設定交換磁碟分割。您可以設定 Azure Linux 代理程式，以在暫存資源磁碟上建立交換檔。您可以在以下步驟中找到與此有關的詳細資訊。
- 所有 VHD 的大小都必須是 1 MB 的倍數。


## Debian 7.x 和 8.x

1. 在 Hyper-V 管理員中，選取虛擬機器。

2. 按一下 [連接]，以開啟虛擬機器的主控台視窗。

3. 如果您針對 ISO 檔案來設定 VM，請將 `/etc/apt/source.list` 中的 **deb cdrom** 一行註解化。

4. 依照以下方法編輯 `/etc/default/grub` 檔案及修改 **GRUB\_CMDLINE\_LINUX** 參數，以納入用於 Azure 的其他核心參數。

        GRUB_CMDLINE_LINUX="console=ttyS0 earlyprintk=ttyS0 rootdelay=300"

5. 重建 grub，然後執行：

        # sudo update-grub

6. 安裝 Azure Linux 代理程式的相依性封裝：

        # apt-get install -y git parted

7.	使用[指引](virtual-machines-linux-update-agent.md)從 GitHub 安裝 Azure Linux 代理程式，並選擇 2.0.14 版：

			# wget https://raw.githubusercontent.com/Azure/WALinuxAgent/WALinuxAgent-2.0.14/waagent
			# chmod +x waagent
			# cp waagent /usr/sbin
			# /usr/sbin/waagent -install -verbose

8. 取消佈建虛擬機器並準備將其佈建於 Azure 上，然後執行：

        # sudo waagent –force -deprovision
        # export HISTSIZE=0
        # logout

9. 在 Hyper-V 管理員中，依序按一下 [動作] -> [關閉]。您現在可以將 Linux VHD 上傳至 Azure。

## 使用 Credativ 指令碼來建立 Debian VHD

Credativ 網站具有協助您自動建立 Debian VHD 的指令碼。您可以從[這裡](https://gitlab.credativ.com/de/azure-manage)下載，再將它安裝在 Linux VM 中。若要建立 Debian VHD (如 Debian 7)，請執行：

        # azure_build_image --option release=wheezy --option image_prefix=lilidebian7 --option image_size_gb=30 SECTION

如果您在使用該指令碼時遭遇任何問題，只要透過[這裡](https://gitlab.credativ.com/groups/de/issues)向 Credativ 提出問題即可。

## 後續步驟

您現在可以開始使用您的 Debian 虛擬硬碟在 Azure 建立新的虛擬機器。如果這是您第一次將該 .vhd 檔案上傳到 Azure，請參閱[建立及上傳包含 Linux 作業系統的虛擬硬碟](virtual-machines-linux-classic-create-upload-vhd.md)中的步驟 2 和 3。

<!---HONumber=AcomDC_0323_2016-->