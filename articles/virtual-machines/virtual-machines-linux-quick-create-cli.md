<properties
   pageTitle="使用 CLI 在 Azure 上建立 Linux VM | Microsoft Azure"
   description="使用 CLI 在 Azure 上建立 Linux VM。"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="vlivech"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="04/04/2016"
   ms.author="v-livech"/>


# 使用 CLI 在 Azure 上建立 Linux VM

本文說明如何使用 Azure CLI 的 `azure vm quick-create` 命令在 Azure 中快速建立 Linux VM，該命令會建立具有基本基礎結構的 VM，可讓您非常快速地建立原型或測試概念。本文需要 Azure 帳戶 ([取得免費試用](https://azure.microsoft.com/pricing/free-trial/)) 和處於資源管理員模式的 [Azure CLI](../xplat-cli-install.md) (`azure config mode arm`)。

## 快速命令摘要

```
# One command to quickly the VM that prompts for arguments
chrisL@fedora$ azure vm quick-create
```

## 詳細的逐步解說

### 建立 Linux VM

在下列命令中，可以使用所需的任何映像，但此範例使用 `canonical:ubuntuserver:14.04.2-LTS:latest`，快速建立 VM。(若要在 Marketplace 中尋找映像，請[搜尋映像](virtual-machines-linux-cli-ps-findimage.md)，或者您可以[上傳您自己的自訂映像](virtual-machines-linux-create-upload-generic.md))。 您會看到類似下列畫面。

在下列命令範例中，請將 &lt; 和 &gt; 之間的值取代為您自己環境中的值。

```bash
# Create the Linux VM using prompts
username@macbook$ azure vm quick-create
info:    Executing command vm quick-create
Resource group name: exampleResourceGroup
Virtual machine name: exampleVMname
Location name: westus
Operating system Type [Windows, Linux]: linux
ImageURN (in the format of "publisherName:offer:skus:version") or a VHD link to the user image: Canonical:UbuntuServer:14.04.4-LTS:latest
User name: ops
Password: *********
Confirm password: *********
+ Looking up the VM "exampleVMname"
info:    Using the VM Size "Standard_D1"
info:    The [OS, Data] Disk or image configuration requires storage account
+ Looking up the storage account cli133501687
info:    Could not find the storage account "cli1301687", trying to create new one
+ Creating storage account "cli133501687" in "westus"
+ Looking up the storage account cli133501687
+ Looking up the NIC "quick-westu-1363648838-nic"
info:    An nic with given name "quick-westu-1363648838-nic" not found, creating a new one
+ Looking up the virtual network "quick-westu-1363648838-vnet"
info:    Preparing to create new virtual network and subnet
\ Creating a new virtual network "quick-westu-1363648838-vnet" [address prefix: "10.0.0.0/16"] with subnet "quick-westu-13636488+8-snet" [address prefix: "10.0.1.0/24"]
+ Looking up the virtual network "quick-westu-1363648838-vnet"
+ Looking up the subnet "quick-westu-1363648838-snet" under the virtual network "quick-westu-1363648838-vnet"
info:    Found public ip parameters, trying to setup PublicIP profile
+ Looking up the public ip "quick-westu-1363648838-pip"
info:    PublicIP with given name "quick-westu-1363648838-pip" not found, creating a new one
+ Creating public ip "quick-westu-1363648838-pip"
+ Looking up the public ip "quick-westu-1363648838-pip"
+ Creating NIC "quick-westu-1363648838-nic"
+ Looking up the NIC "quick-westu-1363648838-nic"
+ Creating VM "quickcreate"
+ Looking up the VM "exampleVMname"
+ Looking up the NIC "quick-westu-1363648838-nic"
+ Looking up the public ip "quick-westu-1363648838-pip"
data:    Id                              :/subscriptions/<guid>/resourceGroups/exampleResourceGroup/providers/Microsoft.Compute/virtualMachines/exampleVMname
data:    ProvisioningState               :Succeeded
data:    Name                            :exampleVMname
data:    Location                        :westus
data:    Type                            :Microsoft.Compute/virtualMachines
data:
data:    Hardware Profile:
data:      Size                          :Standard_D1
data:
data:    Storage Profile:
data:      Image reference:
data:        Publisher                   :canonical
data:        Offer                       :ubuntuserver
data:        Sku                         :14.04.2-LTS
data:        Version                     :latest
data:
data:      OS Disk:
data:        OSType                      :Linux
data:        Name                        :cli350d386daac1f01c-os-1457063387485
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :https://cli1361687.blob.core.windows.net/vhds/cli350d386daac1f01c-os-1457063387485.vhd
data:
data:    OS Profile:
data:      Computer Name                 :exampleVMname
data:      User Name                     :ops
data:      Linux Configuration:
data:        Disable Password Auth       :false
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-32-E9-66
data:          Provisioning State        :Succeeded
data:          Name                      :quick-westu-1363648838-nic
data:          Location                  :westus
data:            Public IP address       :137.135.33.58
data:            FQDN                    :quick-westu-1363648838-pip.westus.cloudapp.azure.com
data:
data:    Diagnostics Profile:
data:      BootDiagnostics Enabled       :true
data:      BootDiagnostics StorageUri    :https://cli13601687.blob.core.windows.net/
data:
data:      Diagnostics Instance View:
info:    vm quick-create command OK
```

您現在可以在預設的 SSH 連接埠 22 上，透過 SSH 登入您的 VM。

`azure vm quick-create` 可快速建立 VM，因此您可以登入並開始工作。但它並沒有複雜的環境，因此，如果您想要自訂您的環境，可以[使用 Azure Resource Manager 範本快速建立特定的部署](virtual-machines-linux-cli-deploy-templates.md)，或者您可以[使用 Azure CLI 命令，直接為 Linux VM 建立自己的自訂環境](virtual-machines-linux-cli-deploy-templates.md)。

上述範例會建立：

- Azure 資源群組，將 VM 部署至
- 一個 Azure 儲存體帳戶，用於存放 VM 映像的 .vhd 檔案
- 一個 Azure 虛擬網路和子網路，用於連線至 VM
- 一個虛擬網路介面卡 (NIC)，用於建立 VM 與網路的關聯
- 一個公用 IP 位址和子網域首碼，用於提供網際網路位址供外部使用，然後在該環境內建立 Linux VM。

此 VM 會直接公開到網際網路，而且只受到使用者名稱和密碼的保護。

## 後續步驟

現在您已經快速建立一個用於測試或示範用途的 Linux VM。您可以在 Azure 中使用 Linux VM，建立一個更安全的執行環境，方法是：

- [在 Azure 中使用 Azure 範本建立 Linux VM](virtual-machines-linux-cli-deploy-templates.md)
- [在 Azure 中使用 Azure 範本建立受 SSH 保護的 Linux VM](virtual-machines-linux-create-ssh-secured-vm-from-template.md)
- [使用 Azure CLI 在 Azure 中建立 Linux VM 與自訂基礎結構](virtual-machines-linux-create-cli-complete.md)

以及多種專屬和開放原始碼基礎結構部署、組態和協調流程工具。

<!---HONumber=AcomDC_0406_2016-->