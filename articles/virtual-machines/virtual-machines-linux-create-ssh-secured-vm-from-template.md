<properties
	pageTitle="使用 Azure 範本建立安全的 Linux VM | Microsoft Azure"
	description="使用 Azure Resource Manager 範本在 Azure 上建立安全的 Linux VM。"
	services="virtual-machines-linux"
	documentationCenter=""
	authors="vlivech"
	manager="timlt"
	editor=""
	tags="azure-service-management,azure-resource-manager" />

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="03/08/2016"
	ms.author="vlivech"/>

# 使用 Azure 範本建立受保護的 Linux VM

範本可讓您在 Azure 上使用要在啟動期間自訂的設定 (如使用者名稱和主機名稱) 建立 VM。本文著重在使用 Azure 範本啟動 Ubuntu VM，此 VM 會建立只開啟一個連接埠 (22，以用於 SSH) 的網路安全性群組 (NSG)，且需要 SSH 金鑰才能登入。

Azure Resource Manager 範本是 JSON 檔案，可用於簡單的一次性工作 (例如本文所進行的啟動 Ubuntu VM)，或用來建構涵蓋整個環境的複雜 Azure 組態 (例如從網路、作業系統到應用程式堆疊部署的測試、開發或生產部署)。

## 目標

- 在 Azure 中使用 Azure 範本建立受 SSH 保護的 Linux VM

## 必要條件

- Azure 帳戶 ([取得免費試用版！](https://azure.microsoft.com/pricing/free-trial/))、[SSH 公開金鑰檔案](virtual-machines-linux-mac-create-ssh-keys.md)、[Azure CLI](../xplat-cli-install.md)，然後輸入 `azure config mode arm` 讓 CLI 進入資源模式。然後在 CLI 中輸入 `azure login` 以登入 Azure。

## 快速命令摘要

此部署只需要一個命令，並選取指出 Azure Resource Manager 範本所在位置的選項。本主題會直接以 Azure 快速入門範本 Github 儲存機制做為範例來使用範本。以下的各種選項只會指出範本和任何參數檔案的所在位置。

1. `azure group create -n <exampleRGname> -l <exampleAzureRegion> [--template-uri <URL> | --template-file <path> | <template-file> -e <parameters.json file>]`

## 建立 Linux VM

下列程式碼範例示範如何呼叫 `azure group create`，以使用[此 Azure Resource Manager 範本](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json)同時建立資源群組和部署受 SSH 保護的 Linux VM。請記住，在您的範例中您必須使用環境中唯一的名稱。此範例則以 `quicksecuretemplate` 做為資源群組名稱、以 `securelinux` 做為 VM 名稱，並以 `quicksecurelinux` 做為子網域名稱。

```bash
azure group create -n quicksecuretemplate -l eastus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
info:    Executing command group create
+ Getting resource group quicksecuretemplate
+ Creating resource group quicksecuretemplate
info:    Created resource group quicksecuretemplate
info:    Supply values for the following parameters
adminUserName: ops
sshKeyData: ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDDRZ/XB8p8uXMqgI8EoN3dWQw... user@contoso.com
dnsLabelPrefix: quicksecurelinux
vmName: securelinux
+ Initializing template configurations and parameters
+ Creating a deployment
info:    Created template deployment "azuredeploy"
data:    Id:                  /subscriptions/<guid>/resourceGroups/quicksecuretemplate
data:    Name:                quicksecuretemplate
data:    Location:            eastus
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK
```

## 詳細的逐步解說

您可以使用 `--template-uri` 參數建立新的資源群組和部署 VM，或者您可以使用 `--template-file` 參數並以範本檔案路徑做為引數在本機下載或建立範本並傳遞範本。Azure CLI 會提示您輸入範本所需的參數。

## 後續步驟

使用範本建立 Linux VM 後，您會想要看看還有哪些應用程式架構可與範本搭配使用。請搜尋[範本庫](https://azure.microsoft.com/documentation/templates/)以探索接下來要部署哪些應用程式架構。

<!---HONumber=AcomDC_0406_2016-->