<properties
		pageTitle="對 Azure 上的 Linux VM 加入使用者 | Microsoft Azure"
		description="將使用者加入 Azure 上的 Linux VM。"
		services="virtual-machines-linux"
		documentationCenter=""
		authors="vlivech"
		manager="timlt"
		editor=""
		tags="azure-resource-manager"
/>

<tags
		ms.service="virtual-machines-linux"
		ms.workload="infrastructure-services"
		ms.tgt_pltfrm="vm-linux"
		ms.devlang="na"
		ms.topic="article"
		ms.date="03/04/2016"
		ms.author="v-livech"
/>

# 將使用者加入 Azure VM 中

任何新啟動的 Linux VM 首要工作之一就是建立新的使用者。在本文中，我們會逐步建立 sudo 使用者帳戶、設定密碼、加入 SSH 公開金鑰，最後使用 `visudo` 允許 sudo 不用密碼。

必要條件︰[Azure 帳戶](https://azure.microsoft.com/pricing/free-trial/)、[SSH 公開金鑰與私密金鑰](virtual-machines-linux-mac-create-ssh-keys.md)、Azure 資源群組，且已安裝 Azure CLI 同時使用 `azure config mode arm` 切換至 Azure Resource Manager 模式。

## 快速命令

```bash
# Add a new user on RedHat family distros
bill@slackware$ sudo useradd -G wheel exampleUser

# Add a new user on Debian family distros
bill@slackware$ sudo useradd -G sudo exampleUser

# Set a password
bill@slackware$ sudo passwd exampleUser
Enter new UNIX password:
Retype new UNIX password:
passwd: password updated successfully

# Copy the SSH Public Key to the new user
bill@slackware$ ssh-copy-id -i ~/.ssh/id_rsa exampleuser@exampleserver

# Change sudoers to allow no password
# Execute visudo as root to edit the /etc/sudoers file
bill@slackware$ visudo

# On RedHat family distros uncomment this line:
## Same thing without a password
# %wheel        ALL=(ALL)       NOPASSWD: ALL

# to this
## Same thing without a password
%wheel        ALL=(ALL)       NOPASSWD: ALL

# On Debian family distros change this line:
# Allow members of group sudo to execute any command
%sudo   ALL=(ALL:ALL) ALL

# to this
%sudo   ALL=(ALL) NOPASSWD:ALL

# Verify everything
# Verify the SSH keys & User account
bill@slackware$ ssh -i ~/.ssh/id_rsa exampleuser@exampleserver

# Verify sudo access
exampleuser@exampleserver$ sudo top
```

## 詳細的逐步解說

### 簡介

新伺服器最常見的首要工作之一，就是加入使用者帳戶。根登入應該一律停用，根帳戶本身甚至不應與您的 Linux 伺服器一起使用，只有 sudo 可以。建立新的使用者，再使用 sudo 提升新使用者的根權限，才是管理及使用 Linux 的正確方法。

我們要使用的命令是 `useradd`，其會修改 `/etc/passwd`、`/etc/shadow`、`/etc/group` 與 `/etc/gshadow`以建立新的 Linux 使用者。我們會在 `useradd` 命令中加入命令列旗標 ，將新的使用者也加入正確的 Linux sudo 群組。雖然 `useradd` 會在 `/etc/passwd` 中建立項目，但卻不會為新的使用者帳戶提供密碼。我們會使用非常簡單的 `passwd` 命令，為新的使用者建立初始密碼。最後一個步驟是修改 sudo 規則，允許我們的使用者以 sudo 權限執行命令，而不必每個命令都要輸入密碼。因為使用者已使用成對的公開金鑰與私密金鑰登入，所以我們假設該使用者帳戶非意圖不良的執行者，因此允許他以 sudo 存取而不需要用密碼。

### 將單一 sudo 使用者加入 Azure VM 中

使用 SSH 金鑰登入 Azure VM。如果您尚未設定 SSH 公開金鑰存取權，請先閱畢本文：[對 Azure 使用公開金鑰驗證](http://link.to/article)。

`useradd` 命令會完成下列工作：

- 建立新的使用者帳戶
- 以相同的名稱建立新的使用者群組
- 在 `/etc/passwd` 中加入空白項目
- 在 `/etc/gpasswd` 中加入空白項目

`-G` 命令列旗標會將新的使用者帳戶加入正確的 Linux 群組，為新的使用者帳戶提供根提升權限。

#### 加入使用者

```bash
# On RedHat family distros
bill@slackware$ sudo useradd -G wheel exampleUser

# On Debian family distros
bill@slackware$ sudo useradd -G sudo exampleUser
```

#### 設定密碼

`useradd` 命令會建立新的使用者，並將項目加入 `/etc/passwd` 和 `/etc/gpasswd` 中，但不會實際設定密碼。我們現在要使用 `passwd` 命令執行這個作業。

```bash
bill@slackware$ sudo passwd exampleUser
Enter new UNIX password:
Retype new UNIX password:
passwd: password updated successfully
```

我們在伺服器上已有具備 sudo 權限的使用者。

### 請將您的 SSH 公開金鑰加入新的使用者帳戶中

用剛才設定的新密碼從電腦使用 `ssh-copy-id` 命令。

```bash
bill@slackware$ ssh-copy-id -i ~/.ssh/id_rsa exampleuser@exampleserver
```

### 使用 visudo 允許在不需要密碼的情況下使用 sudo

使用 `visudo` 編輯 `/etc/sudoers` 檔案，提供更多層的保護，避免不正確地修改了這個很重要的檔案。執行 `visudo` 時會鎖住 `/etc/sudoers` 檔案，以確保沒有任何其他人能在檔案編輯時對檔案進行變更。當您嘗試儲存或結束時，`visudo` 也會透過 `/etc/sudoers` 檢查檔案是否有錯誤。如此可確保您不會在系統上留下不完整的 sudoers 檔案。

我們在正確的預設群組中，已有使用者可進行 sudo 存取。現在我們要讓這些群組可以在沒有密碼的情況下使用 sudo。

```bash
# Execute visudo as root to edit the /etc/sudoers file
bill@slackware$ visudo

# On RedHat family distros uncomment this line:
## Same thing without a password
# %wheel        ALL=(ALL)       NOPASSWD: ALL

# to this
## Same thing without a password
%wheel        ALL=(ALL)       NOPASSWD: ALL

# On Debian family distros change this line:
# Allow members of group sudo to execute any command
%sudo   ALL=(ALL:ALL) ALL

# to this
%sudo   ALL=(ALL) NOPASSWD:ALL
```

### 驗證使用者、ssh 金鑰與 sudo

```bash
# Verify the SSH keys & User account
bill@slackware$ ssh -i ~/.ssh/id_rsa exampleuser@exampleserver

# Verify sudo access
exampleuser@exampleserver$ sudo top
```

<!---HONumber=AcomDC_0330_2016-->