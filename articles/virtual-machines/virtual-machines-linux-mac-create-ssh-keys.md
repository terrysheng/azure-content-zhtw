<properties
	pageTitle="在 Linux 和 Mac 上建立 SSH 金鑰 |Microsoft Azure"
	description="在 Linux 和 Mac 上產生和使用 SSH 金鑰，搭配 Azure 上資源管理員和傳統部署模型。"
	services="virtual-machines-linux"
	documentationCenter=""
	authors="vlivech"
	manager="timlt"
	editor=""
	tags="" />

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="04/04/2016"
	ms.author="v-livech"/>

# 在 Linux 和 Mac 上為 Azure 中的 Linux VM 建立 SSH 金鑰

若要建立受密碼保護的 SSH 公開和私密金鑰，您需要處於資源管理員模式的 [Azure CLI](../xplat-cli-install.md) (`azure config mode arm`)。

## 快速命令清單

在下列命令範例中，請將 &lt; 和 &gt; 之間的值取代為您自己環境中的值。

```bash
[chrisL@fedora ~]$ ssh-keygen -t rsa -b 2048 -C "<your_user@yourdomain.com>"

#Enter the name of the file that will be saved in the `~/.ssh/` directory.
azure_fedora_id_rsa

#Enter (twice) a [secure](https://www.xkcd.com/936/) password for the SSH key.

#Enter passphrase for github_id_rsa:
correct horse battery staple

#Add the newly created key to `ssh-agent` on Linux and Mac (also added to OSX Keychain).
[chrisL@fedora ~]$ eval "$(ssh-agent -s)"
[chrisL@fedora ~]$ ssh-add ~/.ssh/azure_fedora_id_rsa

#Copy the SSH public key to your Linux Server.
[chrisL@fedora ~]$ ssh-copy-id -i ~/.ssh/azure_fedora_id_rsa.pub <youruser@yourserver.com>

#Test the login using keys instead of a password.
[chrisL@fedora ~]$ ssh -i ~/.ssh/azure_fedora_id_rsa <youruser@yourserver.com>

Last login: Tue Dec 29 07:07:09 2015 from 66.215.21.201
[chrisL@fedora ~]$

```

## 簡介

想要登入 Linux 伺服器，最安全**且**最簡單的方式是使用 SSH 公開和私密金鑰，但比起使用密碼來登入 Azure 中的 Linux 或 BSD VM，使用[公開金鑰密碼編譯](https://en.wikipedia.org/wiki/Public-key_cryptography)會安全得多，因為密碼非常容易遭到暴力破解。公開金鑰可以與任何人共用；但只有您 (或您的本機安全性基礎結構) 會擁有私密金鑰。

本文會建立 ssh-rsa 格式的金鑰檔案，因為它們是 Resource Manager 上的部署所建議使用的檔案，而且也是在[入口網站](https://portal.azure.com)上進行傳統部署和資源管理員部署時所必須使用的檔案。


## 建立 SSH 金鑰

Azure 需要至少 2048 位元的 ssh-rsa 格式公開和私密金鑰。若要建立金鑰組，請使用 `ssh-keygen`，在詢問一系列問題後，它便會編寫私密金鑰和對應的公開金鑰。您在建立 Azure VM 時會傳遞公開金鑰內容，此內容會複製到 Linux VM，而且當您登入時，此內容會與安全儲存在本機的私密金鑰搭配使用來對您進行驗證。

### 使用 `ssh-keygen`

此命令會使用 2048 位元 RSA 建立 SSH 金鑰組，並為其加上註解以供輕鬆識別。

```
chrisL@fedora$ ssh-keygen -t rsa -b 2048 -C "username@fedoraVMAzure"
```

##### 命令的說明

`ssh-keygen` = 用來建立金鑰的程式

`-t rsa` = 要建立的金鑰類型，也就是 [RSA 格式](https://en.wikipedia.org/wiki/RSA_(cryptosystem))

`-b 2048` = 金鑰的位元

`-C "username@fedoraVMAzure"` = 金鑰的註解，以便輕鬆識別金鑰。註解會附加至公開金鑰檔案的結尾。常用的註解是電子郵件地址，但在本文中，我們要能夠使用多個 SSH 金鑰，所以建議使用泛型註解。

#### `ssh-keygen` 的逐步解說

```bash
chrisL@fedora$ ssh-keygen -t rsa -b 2048 -C "username@fedoraVMAzure"
Generating public/private rsa key pair.
Enter file in which to save the key (/Users/steve/.ssh/id_rsa): azure_fedora_id_rsa
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in azure_fedora_id_rsa.
Your public key has been saved in azure_fedora_id_rsa.pub.
The key fingerprint is:
14:a3:cb:3e:79:ad:25:cc:65:e9:0c:07:e5:d1:a9:08 username@fedoraVMAzure
The key's randomart image is:
+--[ RSA 4096]----+
|        o o. .   |
|      E. = .o    |
|      ..o...     |
|     . o....     |
|      o S =      |
|     . + O       |
|      + = =      |
|       o +       |
|        .        |
+-----------------+

chrisL@fedora$ ls -al ~/.ssh
-rw------- 1 username staff  1675 Aug 25 18:04 azure_fedora_id_rsa
-rw-r--r-- 1 username staff   410 Aug 25 18:04 azure_fedora_id_rsa.pub
```

`Enter file in which to save the key (/Users/steve/.ssh/id_rsa): azure_fedora_id_rsa` 本文中的金鑰組名稱。預設會有名為 **id\_rsa** 的金鑰組，而且有些工具可能會預期私密金鑰的檔案名稱為 **id\_rsa**，所以最好有此金鑰組 (`~/.ssh/` 是所有 SSH 金鑰組和 SSH 組態檔的典型預設位置)。

`Enter passphrase (empty for no passphrase):` 強烈建議為金鑰組加上密碼 (`ssh-keygen` 將此密碼稱為「複雜密碼」)。若沒有使用密碼來保護金鑰組，任何人只要擁有私密金鑰檔案，就可以用它登入擁有相對應公開金鑰的任何伺服器 (您的伺服器)。新增密碼進而提升防護能力以防有人能夠取得您的私密金鑰檔案，可讓您有時間變更用來對您進行驗證的金鑰。

`chrisL@fedora$ ls -al ~/.ssh` 這會顯示新的金鑰組和其權限。`ssh-keygen` 會建立 `~/.ssh` (如果不存在)，並設定正確的擁有權和檔案模式。

## 建立和設定 SSH 組態檔

雖然對於讓 Linux VM 啟動並執行來說不一定需要，但最好還是建立和設定 `~/.ssh/config` 檔案，以避免不小心使用密碼來登入 VM、自動為不同的 Azure VM 使用不同的金鑰組，以及也設定其他程式 (例如 **git**) 來鎖定多部伺服器。

下列範例將示範標準組態。

### 建立檔案

```bash
chrisL@fedora$ touch ~/.ssh/config
```

### 編輯檔案以加入新的 SSH 組態

```bash
chrisL@fedora$ vim ~/.ssh/config

#Azure Keys
Host fedora22
  Hostname 102.160.203.241
  User username
  PubkeyAuthentication yes
  IdentityFile /Users/steve/.ssh/azure_fedora_id_rsa
# ./Azure Keys
# GitHub keys
Host github.com
  Hostname github.com
  User git
  PubKeyAuthentication yes
  IdentityFile /Users/steve/.ssh/github_id_rsa
Host github.private
  Hostname github.com
  User git
  PubKeyAuthentication yes
  IdentityFile /Users/steve/.ssh/private_repo_github_id_rsa
# ./Github Keys
# Default Settings
Host *
  PubkeyAuthentication=no
  IdentitiesOnly=yes
  ServerAliveInterval=60
  ServerAliveCountMax=30
  ControlMaster auto
  ControlPath /Users/steve/.ssh/Connections/ssh-%r@%h:%p
  ControlPersist 4h
  StrictHostKeyChecking=no
  IdentityFile /Users/steve/.ssh/id_rsa
  UseRoaming=no
```

此 SSH 組態可讓您將各個服務分段，讓它們各自擁有專用的金鑰組。預設設定則適用於您所登入、但不符合上述任何群組的任何主機。SSH 組態也可讓您擁有兩個不同的 [GitHub](https://github.com) 登入，一個用於公開的工作，一個則只用於您工作時的一般私密儲存機制。


##### 組態檔的說明

`Host` = 在終端機上所呼叫的主機名稱。`ssh fedora22` 會指示 `SSH` 使用設定區塊中標示為 `Host fedora22` 的值。注意︰這可以是符合您用途的任何標籤，並不代表任何伺服器的實際主機名稱。

`Hostname 102.160.203.241` = 所登入伺服器的 IP 位址或 DNS 名稱。這會用來路由傳送至伺服器，而且可以是與內部 IP 對應的外部 IP。

`User git` = 登入 Azure VM 時要使用的遠端使用者帳戶。

`PubKeyAuthentication yes` = 這會向 SSH 指出您想要使用 SSH 金鑰來登入。

`IdentityFile /Users/steve/.ssh/azure_fedora_id_rsa` = 這會向 SSH 指出要向伺服器出示以供驗證登入的金鑰組。


## 使用 SSH 登入 Linux VM 而不提供密碼

現在您已擁有 SSH 金鑰組和設定好的 SSH 組態檔，因此您可以快速且安全地登入 Linux VM。第一次使用 SSH 金鑰登入伺服器時，命令會提示您輸入該金鑰檔案的複雜密碼。

`chrisL@fedora$ ssh fedora22`

##### 命令的說明

在執行 `chrisL@fedora$ ssh fedora22` 後，SSH 會先從 `Host fedora22` 區塊中找出所有設定並加以載入，再從最後一個區塊 `Host *` 中載入所有其餘設定。

## 後續步驟

現在您可以開始著手使用金鑰檔案以[使用範本在 Azure 中建立安全的 Linux VM](virtual-machines-linux-create-ssh-secured-vm-from-template.md)。

<!---HONumber=AcomDC_0406_2016-->