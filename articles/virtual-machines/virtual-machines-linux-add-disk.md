<properties
	pageTitle="在 Linux VM 中新增磁碟 | Microsoft Azure"
	description="了解如何在 Linux VM 中新增永續性磁碟"
	keywords="linux 虛擬機器,新增資源磁碟"
	services="virtual-machines-linux"
	documentationCenter=""
	authors="rickstercdn"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager" />

<tags
	ms.service="virtual-machines-linux"
	ms.topic="get-started-article"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.date="03/01/2016"
	ms.author="rclaus"/>

# 在 Linux VM 中新增磁碟

本文說明如何將持續性磁碟連接到您的 VM，以便您保留資料 - 即使您的 VM 會由於維護或調整大小而重新佈建。若要新增磁碟，您需要處於資源管理員模式的 [Azure CLI](../xplat-cli-install.md) (`azure config mode arm`)。

## 快速命令

```
# In the following command examples, replace the values between &lt; and &gt; with the values from your own environment.

rick@ubuntu$ azure vm disk attach-new <myuniquegroupname> <myuniquevmname> <size-in-GB>
```

## 連接磁碟

連接新磁碟很快。只要輸入 `azure vm disk attach-new <myuniquegroupname> <myuniquevmname> <size-in-GB>`，就能為 VM 建立並連接新的 GB 磁碟。您應該會看到類似下面的畫面：

	azure vm disk attach-new myuniquegroupname myuniquevmname 5
	info:    Executing command vm disk attach-new
	+ Looking up the VM "myuniquevmname"
	info:    New data disk location: https://cliexxx.blob.core.windows.net/vhds/myuniquevmname-20150526-0xxxxxxx43.vhd
	+ Updating VM "myuniquevmname"
	info:    vm disk attach-new command OK


## 連接到 Linux VM 以掛接新磁碟

> [AZURE.NOTE] 本主題會利用使用者名稱和密碼連線到 VM；若要使用公開和私密金鑰組來與您的 VM 通訊，請參閱[如何搭配使用 SSH 與 Azure 上的 Linux](virtual-machines-linux-ssh-from-linux.md)。您可以修改利用 `azure vm quick-create` 命令建立之 VM 的 **SSH** 連線能力，方法為使用 `azure vm reset-access` 命令來完全地重設 **SSH** 存取、新增或移除使用者，或新增公開金鑰檔案來保護存取安全。

您必須使用 SSH 登入 Azure VM 來分割、格式化和掛接新磁碟以供 Linux VM 使用。如果您不熟悉使用 **ssh** 進行連接，此命令會採用 `ssh <username>@<FQDNofAzureVM> -p <the ssh port>` 形式，如下所示：

	ssh ops@myuni-westu-1432328437727-pip.westus.cloudapp.azure.com -p 22
	The authenticity of host 'myuni-westu-1432328437727-pip.westus.cloudapp.azure.com (191.239.51.1)' can't be established.
	ECDSA key fingerprint is bx:xx:xx:xx:xx:xx:xx:xx:xx:x:x:x:x:x:x:xx.
	Are you sure you want to continue connecting (yes/no)? yes
	Warning: Permanently added 'myuni-westu-1432328437727-pip.westus.cloudapp.azure.com,191.239.51.1' (ECDSA) to the list of known hosts.
	ops@myuni-westu-1432328437727-pip.westus.cloudapp.azure.com's password:
	Welcome to Ubuntu 14.04.2 LTS (GNU/Linux 3.16.0-37-generic x86_64)

	 * Documentation:  https://help.ubuntu.com/

	  System information as of Fri May 22 21:02:32 UTC 2015

	  System load: 0.37              Memory usage: 2%   Processes:       207
	  Usage of /:  41.4% of 1.94GB   Swap usage:   0%   Users logged in: 0

	  Graph this data and manage this system at:
	    https://landscape.canonical.com/

	  Get cloud support with Ubuntu Advantage Cloud Guest:
	    http://www.ubuntu.com/business/services/cloud

	0 packages can be updated.
	0 updates are security updates.

	The programs included with the Ubuntu system are free software;
	the exact distribution terms for each program are described in the
	individual files in /usr/share/doc/*/copyright.

	Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
	applicable law.

	ops@myuniquevmname:~$

現在已連線到 VM，您已準備好連接磁碟。請先使用 `dmesg | grep SCSI` (您用來探索新磁碟的方法可能有所不同) 尋找該磁碟。在此案例中，它看起來如下：

	dmesg | grep SCSI
	[    0.294784] SCSI subsystem initialized
	[    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
	[    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
	[    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
	[ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk

而且，在本主題的案例中，`sdc` 磁碟是我們想要的磁碟。現在使用 `sudo fdisk /dev/sdc` 來分割磁碟 -- 假設在您的案例中，磁碟為 `sdc`，使它成為磁碟分割 1 上的主要磁碟，並接受其他預設值。

	sudo fdisk /dev/sdc
	Device contains neither a valid DOS partition table, nor Sun, SGI or OSF disklabel
	Building a new DOS disklabel with disk identifier 0x2a59b123.
	Changes will remain in memory only, until you decide to write them.
	After that, of course, the previous content won't be recoverable.

	Warning: invalid flag 0x0000 of partition table 4 will be corrected by w(rite)

	Command (m for help): n
	Partition type:
	   p   primary (0 primary, 0 extended, 4 free)
	   e   extended
	Select (default p): p
	Partition number (1-4, default 1): 1
	First sector (2048-10485759, default 2048):
	Using default value 2048
	Last sector, +sectors or +size{K,M,G} (2048-10485759, default 10485759):
	Using default value 10485759

在命令提示字元中，輸入 `p` 來建立磁碟分割：

	Command (m for help): p

	Disk /dev/sdc: 5368 MB, 5368709120 bytes
	255 heads, 63 sectors/track, 652 cylinders, total 10485760 sectors
	Units = sectors of 1 * 512 = 512 bytes
	Sector size (logical/physical): 512 bytes / 512 bytes
	I/O size (minimum/optimal): 512 bytes / 512 bytes
	Disk identifier: 0x2a59b123

	   Device Boot      Start         End      Blocks   Id  System
	/dev/sdc1            2048    10485759     5241856   83  Linux

	Command (m for help): w
	The partition table has been altered!

	Calling ioctl() to re-read partition table.
	Syncing disks.

同時，使用 **mkfs** 命令來指定檔案系統類型和裝置名稱，將檔案系統寫入磁碟分割。在本主題中，我們將使用先前內容中的 `ext4` 和 `/dev/sdc1`：

	sudo mkfs -t ext4 /dev/sdc1
	mke2fs 1.42.9 (4-Feb-2014)
	Discarding device blocks: done
	Filesystem label=
	OS type: Linux
	Block size=4096 (log=2)
	Fragment size=4096 (log=2)
	Stride=0 blocks, Stripe width=0 blocks
	327680 inodes, 1310464 blocks
	65523 blocks (5.00%) reserved for the super user
	First data block=0
	Maximum filesystem blocks=1342177280
	40 block groups
	32768 blocks per group, 32768 fragments per group
	8192 inodes per group
	Superblock backups stored on blocks:
		32768, 98304, 163840, 229376, 294912, 819200, 884736
	Allocating group tables: done
	Writing inode tables: done
	Creating journal (32768 blocks): done
	Writing superblocks and filesystem accounting information: done

現在我們會使用 `mkdir`，建立目錄來掛接檔案系統：

	sudo mkdir /datadrive

而您可以使用 `mount` 來掛接目錄：

	sudo mount /dev/sdc1 /datadrive

資料磁碟現在可以當做 `/datadrive` 來使用。

	ls
	bin   datadrive  etc   initrd.img  lib64       media  opt   root  sbin  sys  usr  vmlinuz
	boot  dev        home  lib         lost+found  mnt    proc  run   srv   tmp  var

> [AZURE.NOTE] 您也可以使用 SSH 金鑰進行識別，連接到 Linux 虛擬機器。如需詳細資訊，請參閱[如何搭配使用 SSH 與 Azure 上的 Linux](virtual-machines-linux-ssh-from-linux.md)。

## 後續步驟

- 請記住，如果將新磁碟重新開機，除非您將該資訊寫入 [/etc/fstab](http://en.wikipedia.org/wiki/Fstab) 檔案，否則該磁碟通常無法供 VM 使用。
- 檢閱[最佳化您的 Linux 機器效能](virtual-machines-linux-optimization.md)建議，確保您的 Linux VM 已正確設定。
- 新增其他磁碟以擴充儲存體容量，並[設定 RAID](virtual-machines-linux-configure-raid.md) 以提升效能。

<!---HONumber=AcomDC_0406_2016-->