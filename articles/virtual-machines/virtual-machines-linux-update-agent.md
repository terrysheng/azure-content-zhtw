<properties
	pageTitle="從 GitHub 更新 Azure Linux 代理程式 | Microsoft Azure"
	description="了解如何從 Github 更新 Azure 中的 Linux VM 之 Azure Linux 代理程式為最新版本"
	services="virtual-machines"
	documentationCenter=""
	authors="SuperScottz"
	manager="timlt"
	editor=""
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/14/2015"
	ms.author="mingzhan"/>


# 如何在 VM 上從 Github 更新至最新版的 Azure Linux 代理程式

若要更新 Azure 中 Linux VM 的 [Azure Linux 代理程式](https://github.com/Azure/WALinuxAgent)，您必須準備：

1. 在 Azure 中執行的 Linux VM。
2. 對該 Linux VM 的 SSH 連線。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


> [AZURE.NOTE] 如果您會從 Windows 電腦執行這項工作，可以使用 PuTTY 來 SSH 到 Linux 機器。如需詳細資訊，請參閱[如何登入執行 Linux 的虛擬機器](virtual-machines-linux-how-to-log-on.md)。

支援 Azure 的 Linux 散發版本有將 Azure Linux 代理程式套件放在其儲存機制，因此請先從這個 Distro 儲存機制檢查並安裝最新版本。

對於 Ubuntu，只要輸入：

    #sudo apt-get install walinuxagent

在 CentOS 上請輸入：

    #sudo yum install waagent

如為 Oracle Linux，請確定已啟用 `Addons` 儲存機制。視您的情況，編輯檔案 `/etc/yum.repo.d/public-yum-ol6.repo`(Oracle Linux 6) 或 `/etc/yum.repo.d/public-yum-ol7.repo`(Oracle Linux)，將此檔案中 **[ol6\_addons]** 或 **[ol7\_addons]** 底下的 `enabled=0` 一行變更為 `enabled=1`。

接下來，若要安裝最新版的 Azure Linux 代理程式，請輸入：

    #sudo yum install WALinuxAgent

通常您要做就是這幾個步驟了，但如果因為其他原因造成您必須直接從 https://github.com 安裝，請執行以下步驟。


## 安裝 wget

使用 SSH 登入 VM。

在命令列輸入 `#sudo yum install wget` 來安裝 wget (有一些散發版本基本上是不會安裝的，例如 Redhat、CentOS、Oracle Linux 6.4 和 6.5 版)。


## 下載最新版本

在網頁中開啟 [Github 中的 Azure Linux 代理程式版本](https://github.com/Azure/WALinuxAgent/releases)，然後查明最新的版本號碼。(您可以輸入 `#waagent --version`，即可找到目前的版本 )。

### 針對 2.0.x 版本，請輸入：

    #wget https://raw.githubusercontent.com/Azure/WALinuxAgent/WALinuxAgent-[version]/waagent  

   下列一行使用 2.0.14 版做為範例：

    #wget https://raw.githubusercontent.com/Azure/WALinuxAgent/WALinuxAgent-2.0.14/waagent  

### 針對 2.1.x 版本或較新版本，請輸入：

    #wget https://github.com/Azure/WALinuxAgent/archive/WALinuxAgent-[version].zip
    #unzip WALinuxAgent-[version].zip
    #cd WALinuxAgent-[version]

   下面這一行會使用 2.1.0 版本做為範例：

    #wget https://github.com/Azure/WALinuxAgent/archive/WALinuxAgent-2.1.0.zip
    #unzip WALinuxAgent-2.1.0.zip  
    #cd WALinuxAgent-2.1.0

## 安裝 Azure Linux 代理程式

### 針對 2.0.x 版本，請使用：

 讓 waagent 變成可執行檔：

    #chmod +x waagent

 將新的可執行檔複製到 /usr/sbin/。

  針對大部分的 Linux，請使用：

    #sudo cp waagent /usr/sbin

  針對 CoreOS，請使用：

    #sudo cp waagent /usr/share/oem/bin/

  如果是新安裝 Azure Linux 代理程式，請執行：

    #sudo /usr/sbin/waagent -install -verbose

### 針對 2.1.x 版本，請使用：

您可能需要先安裝套件 `setuptools`，請參閱[此處](https://pypi.python.org/pypi/setuptools)。然後，執行：

    #sudo python setup.py install

## 重新啟動 waagent 服務

針對大多數的 Linux 散發版本：

    #sudo service waagent restart

針對 Ubuntu，使用：

    #sudo service walinuxagent restart

針對 CoreOS，請使用：

    #sudo systemctl restart waagent

## 確認 Azure Linux 代理程式版本

    #waagent -version

針對 CoreOS，上述命令可能無效。

您會看到 Azure Linux 代理程式版本已更新為新的版本。

如需有關 Azure Linux 代理程式的詳細資訊，請參閱 [Azure Linux 代理程式讀我檔案](https://github.com/Azure/WALinuxAgent)。

<!---HONumber=AcomDC_0211_2016-->