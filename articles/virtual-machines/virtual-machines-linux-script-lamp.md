<properties
	pageTitle="在 Linux VM 上使用 CustomScript 擴充功能 | Microsoft Azure"
	description="了解如何使用 CustomScript 擴充功能，在 Azure 中以傳統部署模型所建立的 Linux 虛擬機器上部署應用程式。"
	editor="tysonn"
	manager="timlt"
	documentationCenter=""
	services="virtual-machines"
	authors="gbowerman"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="multiple"
	ms.tgt_pltfrm="linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/23/2015"
	ms.author="guybo"/>

#使用適用於 Linux 的 Azure CustomScript 延伸模組部署 LAMP 應用程式#

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]資源管理員模型。


適用於 Linux 的 Microsoft Azure CustomScript 延伸模組提供一種方式，讓您可以執行使用該虛擬機器 (VM) 所支援的任何指令碼語言 (例如 Python 和 Bash) 所撰寫的任意程式碼來自訂 VM。這提供極具彈性的方式，自動將應用程式部署到多部電腦。

您可以使用 Azure 傳統入口網站、Windows PowerShell 或 Azure 命令列介面 (Azure CLI)，來部署 CustomScript 延伸模組。

在本文中我們將使用 Azure CLI，將簡單的 LAMP 應用程式部署至以傳統部署模型建立的 Ubuntu VM。

## 必要條件

在此範例中，會先建立兩個執行 Ubuntu 14.04 或更新版本的 Azure VM。VM 的名稱為 *script-vm* 和 *lamp-vm* 。建立 VM 時請使用唯一名稱。其中一個用來執行 CLI 命令，而另一個用來部署 LAMP 應用程式。

您也需要 Azure 儲存體帳戶和金鑰才能存取它 (您可以從 Azure 傳統入口網站取得此資訊)。

如果您需要在 Azure 上建立 Linux VM 的說明，請參閱[建立執行 Linux 的虛擬機器](virtual-machines-linux-tutorial.md)。

安裝命令假設的是 Ubuntu，但是您可以對任何支援的 Linux distro 採用此安裝。

script-vm VM 需要安裝 Azure CLI，並且與 Azure 之間具有正常運作的連線。如需此動作的說明，請參閱[安裝與設定 Azure 命令列介面](../xplat-cli-install.md)。

## 上傳指令碼

我們會使用 CustomScript 擴充功能在遠端 VM 上執行指令碼，以安裝 LAMP 堆疊並建立 PHP 頁面。為了可從任何地方存取指令碼，我們會以 Azure Blob 形式上傳該指令碼。

### 指令碼概觀

此指令碼範例會將 LAMP 堆疊安裝到 Ubuntu (包括設定 MySQL 的無訊息安裝)、寫入簡單的 PHP 檔案，並啟動 Apache。

	#!/bin/bash
	# set up a silent install of MySQL
	dbpass="mySQLPassw0rd"

	export DEBIAN_FRONTEND=noninteractive
	echo mysql-server-5.6 mysql-server/root_password password $dbpass | debconf-set-selections
	echo mysql-server-5.6 mysql-server/root_password_again password $dbpass | debconf-set-selections

	# install the LAMP stack
	apt-get -y install apache2 mysql-server php5 php5-mysql  

	# write some PHP
	echo <center><h1>My Demo App</h1><br/></center> > /var/www/html/phpinfo.php
	echo <\?php phpinfo()\; \?> >> /var/www/html/phpinfo.php

	# restart Apache
	apachectl restart

### 上傳指令碼

將指令碼儲存為文字檔 (例如 *lamp\_install.sh*)，然後將它上傳到 Azure 儲存體。您可以使用 Azure CLI，輕鬆執行這個動作。下列範例會將檔案上傳到名為 "scripts" 的儲存體容器中。如果此容器不存在，您必須先建立它。

    azure storage blob upload -a <yourStorageAccountName> -k <yourStorageKey> --container scripts ./install_lamp.sh

此外，還會建立 JSON 檔案，此檔案會描述如何從 Azure 儲存體下載指令碼。將此檔案儲存為 *public\_config.json* (使用您的儲存體帳戶名稱來取代 "mystorage")：

    {"fileUris":["https://mystorage.blob.core.windows.net/scripts/install_lamp.sh"], "commandToExecute":"sh install_lamp.sh" }


## 部署延伸模組

現在您可以使用下一個命令，透過 Azure CLI 將 Linux CustomScript 延伸模組部署到遠端 VM。

    azure vm extension set -c "./public_config.json" lamp-vm CustomScriptForLinux Microsoft.OSTCExtensions 1.*

前一個命令會在名為 *lamp-vm* 的 VM 上下載並執行 *lamp\_install.sh* 指令碼。

因為該應用程式包含 Web 伺服器，所以請記得使用下列命令，在遠端 VM 上開啟 HTTP 接聽連接埠。

    azure vm endpoint create -n Apache -o tcp lamp-vm 80 80

## 監視與疑難排解

您可以藉由查看遠端 VM 上的記錄檔來檢查自訂指令碼執行的情況。SSH 連線到 *lamp-vm* ，並使用下一個命令顯示記錄檔的結尾。

    cd /var/log/azure/Microsoft.OSTCExtensions.CustomScriptForLinux/1.3.0.0/
    tail -f extension.log

執行 CustomScript 延伸模組之後，您可以瀏覽至您建立的 PHP 網頁以取得資訊。這篇文章中的範例 PHP 頁面是 *http://lamp-vm.cloudapp.net/phpinfo.php* 。

## 其他資源

您可以使用相同的基本步驟來部署更複雜的應用程式。此範例將安裝指令碼儲存為 Azure 儲存體中的公用 Blob。比較安全的選項是使用[安全存取簽章](https://msdn.microsoft.com/library/azure/ee395415.aspx) (SAS)，以安全 Blob 形式儲存安裝指令碼。

以下列出 Azure CLI、Linux 與 CustomScript 延伸模組的其他資源。

[使用 CustomScript 延伸模組以將 Linux VM 自訂工作自動化](http://azure.microsoft.com/blog/2014/08/20/automate-linux-vm-customization-tasks-using-customscript-extension/)

[Azure Linux 延伸模組 (GitHub)](https://github.com/Azure/azure-linux-extensions)

[Azure 上的 Linux 和開放原始碼運算](virtual-machines-linux-opensource.md)

<!---HONumber=AcomDC_1203_2015-->
