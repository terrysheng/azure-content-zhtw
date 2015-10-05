<properties 
	pageTitle="選取 Linux 的使用者名稱 | Microsoft Azure" 
	description="了解如何在 Azure 中選取適用於 Linux 虛擬機器的使用者名稱。" 
	services="virtual-machines" 
	documentationCenter="" 
	authors="szarkos" 
	manager="timlt" 
	editor=""
	tags="azure-service-management,azure-resource-manager" />

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/29/2015" 
	ms.author="szark"/>



#在 Azure 上選取適用於 Linux 的使用者名稱#

在 Azure 上佈建 Linux 虛擬機器時，您必須指定非根使用者的名稱供稍後使用，以使用登入 VM。您可以選擇新使用者的名稱；如果是透過管理入口網站佈建，可以使用預設名稱「azureuser」。

在多數情況中，這位新使用者不會存在於基本映像中，而會在佈建程序中建立。若此使用者存在於基本 VM 映像，則 Azure Linux 代理程式只會在建立 VM 時，根據您在建立 VM 時所提供的資訊，為該使用者設定密碼 (及/或 SSH 金鑰)。

**不過**，Linux 會定義一組不應該使用的使用者名稱。如果您嘗試使用現有的系統使用者 (定義為具有 UID 0-99 的使用者) 佈建 Linux VM，佈建程序將**失敗**。常見的範例是 `root` 使用者，其 UID 為 0。

 - 另請參閱：[Linux 標準基礎 - 使用者 ID 範圍](http://refspecs.linuxfoundation.org/LSB_4.1.0/LSB-Core-generic/LSB-Core-generic/uidrange.html)

以下是在佈建 Linux 虛擬機器時應避免使用的使用者名稱。建議您**不要使用這些使用者名稱**，因為這些名稱可能會導致 VM 佈建失敗。


## openSUSE
- abrt
- adm
- audio
- bin
- bin
- cdrom
- cgred
- daemon
- dbus
- dialout
- dip
- disk
- floppy
- ftp
- games
- gopher
- haldaemon
- halt
- kmem
- lock
- lp
- mail
- man
- mem
- nfsnobody
- nobody
- ntp
- operator
- oprofile
- postdrop
- postfix
- qpidd
- root
- rpc
- rpcuser
- saslauth
- shutdown
- slocate
- sshd
- stapdev
- stapusr
- sync
- sys
- tape
- test
- tcpdump
- tty
- users
- utempter
- utmp
- uucp
- vcsa
- video
- wheel


## SLES
- audio
- bin
- cdrom
- console
- daemon
- dialout
- disk
- floppy
- ftp
- ftp
- games
- haldaemon
- kmem
- lp
- lp
- mail
- maildrop
- man
- messagebus
- modem
- news
- news
- nobody
- nogroup
- polkituser
- postfix
- public
- root
- shadow
- sshd
- sys
- test
- trusted
- tty
- users
- utmp
- uucp
- uuidd
- video
- wheel
- www
- wwwrun
- xok

 
## CentOS
- abrt
- adm
- audio
- bin
- cdrom
- cgred
- daemon
- dbus
- dialout
- dip
- disk
- floppy
- ftp
- ftp
- games
- gopher
- haldaemon
- halt
- kmem
- lock
- lp
- mail
- man
- mem
- nfsnobody
- nobody
- ntp
- operator
- oprofile
- postdrop
- postfix
- qpidd
- root
- rpc
- rpcuser
- saslauth
- shutdown
- slocate
- sshd
- stapdev
- stapusr
- sync
- sys
- tape
- test
- tcpdump
- tty
- users
- utempter
- utmp
- uucp
- vcsa
- video
- wheel


## Ubuntu
- adm
- admin
- audio
- backup
- bin
- cdrom
- crontab
- daemon
- dialout
- dip
- disk
- fax
- floppy
- fuse
- games
- gnats
- irc
- kmem
- landscape
- libuuid
- list
- lp
- mail
- man
- messagebus
- mlocate
- netdev
- news
- nobody
- nogroup
- operator
- plugdev
- proxy
- root
- sasl
- shadow
- src
- ssh
- sshd
- staff
- sudo
- sync
- sys
- syslog
- tape
- tty
- users
- utmp
- uucp
- video
- voice
- whoopsie
- www-data

 

<!---HONumber=Sept15_HO4-->