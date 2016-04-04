<properties
   pageTitle="使用 Azure CLI 從頭開始建立 Linux VM | Microsoft Azure"
   description="使用 Azure CLI 從頭開始建立 Linux VM、儲存體、虛擬網路和子網路、NIC、公用 IP、網路安全性群組。"
   services="virtual-machines-linux"
   documentationCenter="virtual-machines"
   authors="vlivech"
   manager="squillace"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="02/10/2016"
   ms.author="v-livech"/>

# 使用 Azure CLI 從頭開始建立 Linux VM


## 目標

- 部署資源群組
- 部署儲存體帳戶
- 部署虛擬網路與子網路
- 設定網路安全性群組和輸入規則
- 指派公用 IP 給 NIC
- 指派 NSG 給 NIC
- 部署 Ubuntu 14.04 LTS VM

## 必要條件

- Azure 帳戶
  - [取得免費試用版。](https://azure.microsoft.com/pricing/free-trial/) 
  - [Azure 入口網站](https://portal.azure.com)
- JSON 剖析工具︰這個範例使用 [jq](https://stedolan.github.io/jq/)


## 簡介

本文利用 VNetwork 子網路內的 Linux VM 建置類似雲端服務部署的部署。它以命令方式引導整個基本部署，依序引導逐一命令，直到您具備有效的安全 Linux VM，可透過網際網路從任何地方連線。

在過程中，您將了解資源管理員部署模型提供給您的相依性階層，以及它提供多少功能。一旦您看到系統如何建置，您可以使用更直接的 Azure CLI 命令更快速地重建系統 (請參閱[這裡](insertlinktonewdoc)以了解使用 `azure vm quick-create` 命令且大致相同的部署)，或者您可以繼續了解如何設計和自動化整個網路和應用程式部署，並使用 [Azure Resource Manager 範本](../resource-group-authoring-templates.md)更新它們。一旦您看到部署的組件如何彼此搭配運作，就可以更輕鬆地建立範本來將它們自動化。

讓我們利用對部署和簡單計算很實用的 VM 來建置簡單的網路，我們會在過程中加以解釋。然後您就可以繼續進行更複雜的網路和部署。

## 快速命令

在這個快速命令一節中的命名有幾個範例，您會想要取代為您自己的設定，視需要編輯。

```bash
# Create the Resource Group
azure group create TestRG westeurope

# Create the Storage Account
azure storage account create \  
--location westeurope \
--resource-group TestRG \
--type GRS \
computeteststore

# Verify the RG using the JSON parser
azure group show testrg --json | jq '.'

# Create the Virtual Network
azure network vnet create -g TestRG -n TestVNet -a 192.168.0.0/16 -l westeurope

# Verify the RG
azure group show testrg --json | jq '.'

# Create the Subnet
azure network vnet subnet create -g TestRG -e TestVNet -n FrontEnd -a 192.168.1.0/24

# Verify the VNet and Subnet
azure network vnet show testrg testvnet --json | jq '.'

# Create the NIC
azure network nic create -g TestRG -n TestNIC -l westeurope -a 192.168.1.101 -m TestVNet -k FrontEnd

# Verify the NIC
azure network nic show testrg testnic --json | jq '.'

# Create the NSG
azure network nsg create testrg testnsg westeurope

# Add an inbound rule for the NSG
azure network nsg rule create --protocol tcp --direction inbound --priority 1000  --destination-port-range 22 --access allow testrg testnsg testnsgrule

# Creat the Public facing NIC
azure network public-ip create -d testsubdomain testrg testpip westeurope

# Verify the NIC
azure network public-ip show testrg testpip --json | jq '.'

# Associate the Public IP to the NIC
azure network nic set --public-ip-name test pip

# Bind the NSG to the NIC
azure network nic set --network-security-group-name testnsg testrg testnic

# Create the Linux VM
azure vm create \            
    --resource-group testrg \
    --name testvm \
    --location westeurope \
    --os-type linux \
    --nic-name testnic \
    --vnet-name testvnet \
    --vnet-subnet-name FrontEnd \
    --storage-account-name computeteststore \
    --image-urn canonical:UbuntuServer:14.04.3-LTS:latest \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --admin-username ops
    
# Verify everything built
azure vm show testrg testvm 

```


## 詳細的逐步解說

### 建立資源群組，並選擇部署位置 

Azure 資源群組是邏輯部署實體，包含啟用資源部署之邏輯管理的組態及其他中繼資料。

    azure group create TestRG westeurope                        
    info:    Executing command group create
    + Getting resource group TestRG
    + Creating resource group TestRG
    info:    Created resource group TestRG
    data:    Id:                  /subscriptions/<yoursub>/resourceGroups/TestRG
    data:    Name:                TestRG
    data:    Location:            westeurope
    data:    Provisioning State:  Succeeded
    data:    Tags: null
    data:
    info:    group create command OK

### 建立儲存體帳戶 

在其他案例中，你將需要儲存體帳戶，用於您的 VM 磁碟和任何您想要新增的其他額外資料磁碟。簡單地說，您一定要在建立資源群組之後立即建立儲存體帳戶。

我們在此使用 `azure storage account create` 命令，傳遞帳戶的位置、將控制它的資源群組，以及您想要的儲存體支援類型。

    azure storage account create \  
    --location westeurope \
    --resource-group TestRG \
    --type GRS \
    computeteststore
    info:    Executing command storage account create
    + Creating storage account
    info:    storage account create command OK
    rasquill•~/workspace/keygen» azure group show testrg 
    info:    Executing command group show
    + Listing resource groups
    + Listing resources for the group
    data:    Id:                  /subscriptions/<guid>/resourceGroups/TestRG
    data:    Name:                TestRG
    data:    Location:            westeurope
    data:    Provisioning State:  Succeeded
    data:    Tags: null
    data:    Resources:
    data:
    data:      Id      : /subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Storage/storageAccounts/computeteststore
    data:      Name    : computeteststore
    data:      Type    : storageAccounts
    data:      Location: westeurope
    data:      Tags    :
    data:
    data:    Permissions:
    data:      Actions: *
    data:      NotActions:
    data:
    info:    group show command OK
    
讓我們使用 [jq](https://stedolan.github.io/jq/) 工具 (您可以使用 **jsawk** 或任何您想要剖析 JSON 的語言程式庫) 連同 `--json` Azure CLI 選項來檢查使用 `azure group show` 命令的資源群組。

    azure group show testrg --json | jq '.'                                                                                        
    {
      "tags": {},
      "id": "/subscriptions/<guid>/resourceGroups/TestRG",
      "name": "TestRG",
      "provisioningState": "Succeeded",
      "location": "westeurope",
      "properties": {
        "provisioningState": "Succeeded"
      },
      "resources": [
        {
          "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Storage/storageAccounts/computeteststore",
          "name": "computeteststore",
          "type": "storageAccounts",
          "location": "westeurope",
          "tags": null
        }
      ],
      "permissions": [
        {
          "actions": [
            "*"
          ],
          "notActions": []
        }
      ]
    }

若要調查使用 CLI 的儲存體帳戶，您必須先使用下列命令的變化，將本文的儲存體帳戶名稱取代為您自己的，以設定帳戶名稱和金鑰。

        AZURE_STORAGE_CONNECTION_STRING="$(azure storage account connectionstring show computeteststore --resource-group testrg --json | jq -r '.string')"

然後您就能夠輕鬆地檢視您的儲存體資訊︰

        azure storage container list 
        info:    Executing command storage container list
        + Getting storage containers
        data:    Name  Public-Access  Last-Modified
        data:    ----  -------------  -----------------------------
        data:    vhds  Off            Sun, 27 Sep 2015 19:03:54 GMT
        info:    storage container list command OK

### 建立您的虛擬網路和子網路 

你將需要建立您可以在其中安裝 VM 的 Azure 虛擬網路和子網路。

    azure network vnet create -g TestRG -n TestVNet -a 192.168.0.0/16 -l westeurope
    info:    Executing command network vnet create
    + Looking up virtual network "TestVNet"
    + Creating virtual network "TestVNet"
    + Loading virtual network state
    data:    Id                              : /subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
    data:    Name                            : TestVNet
    data:    Type                            : Microsoft.Network/virtualNetworks
    data:    Location                        : westeurope
    data:    ProvisioningState               : Succeeded
    data:    Address prefixes:
    data:      192.168.0.0/16
    info:    network vnet create command OK

同樣地，讓我們看看如何使用 --`azure group show` 和 **jq** 的 json 選項建置資源。我們現在有 `storageAccounts` 資源和 `virtualNetworks` 資源。

    azure group show testrg --json | jq '.'
    {
      "tags": {},
      "id": "/subscriptions/<guid>/resourceGroups/TestRG",
      "name": "TestRG",
      "provisioningState": "Succeeded",
      "location": "westeurope",
      "properties": {
        "provisioningState": "Succeeded"
      },
      "resources": [
        {
          "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet",
          "name": "TestVNet",
          "type": "virtualNetworks",
          "location": "westeurope",
          "tags": null
        },
        {
          "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Storage/storageAccounts/computeteststore",
          "name": "computeteststore",
          "type": "storageAccounts",
          "location": "westeurope",
          "tags": null
        }
      ],
      "permissions": [
        {
          "actions": [
            "*"
          ],
          "notActions": []
        }
      ]
    }

現在讓我們在要於其中部署 VM 的 `TestVnet` 虛擬網路中建立子網路。我們使用 `azure network vnet subnet create` 命令搭配我們已經建立的資源︰`TestRG` 資源群組、`TestVNet` 虛擬網路，而且我們會新增子網路名稱 `FrontEnd` 和子網路位址首碼 `192.168.1.0/24`，如下所示。

    azure network vnet subnet create -g TestRG -e TestVNet -n FrontEnd -a 192.168.1.0/24
    info:    Executing command network vnet subnet create
    + Looking up the subnet "FrontEnd"
    + Creating subnet "FrontEnd"
    + Looking up the subnet "FrontEnd"
    data:    Id                              : /subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
    data:    Type                            : Microsoft.Network/virtualNetworks/subnets
    data:    ProvisioningState               : Succeeded
    data:    Name                            : FrontEnd
    data:    Address prefix                  : 192.168.1.0/24
    data:
    info:    network vnet subnet create command OK
  
因為子網路是以邏輯方式位於虛擬網路內，所以我們將利用稍有不同的命令尋找子網路資訊 -- `azure network vnet show`，但仍需使用 **jq** 檢查 JSON 輸出。

    azure network vnet show testrg testvnet --json | jq '.'
    {
      "subnets": [
        {
          "ipConfigurations": [],
          "addressPrefix": "192.168.1.0/24",
          "provisioningState": "Succeeded",
          "name": "FrontEnd",
          "etag": "W/"974f3e2c-028e-4b35-832b-a4b16ad25eb6"",
          "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd"
        }
      ],
      "tags": {},
      "addressSpace": {
        "addressPrefixes": [
          "192.168.0.0/16"
        ]
      },
      "dhcpOptions": {
        "dnsServers": []
      },
      "provisioningState": "Succeeded",
      "etag": "W/"974f3e2c-028e-4b35-832b-a4b16ad25eb6"",
      "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet",
      "name": "TestVNet",
      "location": "westeurope"
    }

### 建立 NIC 與 Linux VM 搭配使用

因為您可能會將規則套用至 NIC 的使用上，並有多個規則，即使 NIC 也可以程式設計方式使用。

        azure network nic create -g TestRG -n TestNIC -l westeurope -a 192.168.1.101 -m TestVNet -k FrontEnd
        info:    Executing command network nic create
        + Looking up the network interface "TestNIC"
        + Looking up the subnet "FrontEnd"
        + Creating network interface "TestNIC"
        + Looking up the network interface "TestNIC"
        data:    Id                              : /subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC
        data:    Name                            : TestNIC
        data:    Type                            : Microsoft.Network/networkInterfaces
        data:    Location                        : westeurope
        data:    Provisioning state              : Succeeded
        data:    Enable IP forwarding            : false
        data:    IP configurations:
        data:      Name                          : NIC-config
        data:      Provisioning state            : Succeeded
        data:      Private IP address            : 192.168.1.101
        data:      Private IP Allocation Method  : Static
        data:      Subnet                        : /subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
        data:
        info:    network nic create command OK

由於 NIC 資源與 VM 和網路安全性群組相關聯，所以您在檢查 `TestRG` 資源群組時可以將其視為最上層資源︰

        azure group show testrg --json | jq '.'
        {
        "tags": {},
        "id": "/subscriptions/guid/resourceGroups/TestRG",
        "name": "TestRG",
        "provisioningState": "Succeeded",
        "location": "westeurope",
        "properties": {
            "provisioningState": "Succeeded"
        },
        "resources": [
            {
            "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC",
            "name": "TestNIC",
            "type": "networkInterfaces",
            "location": "westeurope",
            "tags": null
            },
            {
            "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet",
            "name": "TestVNet",
            "type": "virtualNetworks",
            "location": "westeurope",
            "tags": null
            },
            {
            "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Storage/storageAccounts/computeteststore",
            "name": "computeteststore",
            "type": "storageAccounts",
            "location": "westeurope",
            "tags": null
            }
        ],
        "permissions": [
            {
            "actions": [
                "*"
            ],
            "notActions": []
            }
        ]
        }

您可以藉由直接檢查資源與使用 `azure network nic show` 命令來查看詳細資料。

        azure network nic show testrg testnic --json | jq '.'
        {
        "ipConfigurations": [
            {
            "loadBalancerBackendAddressPools": [],
            "loadBalancerInboundNatRules": [],
            "privateIpAddress": "192.168.1.101",
            "privateIpAllocationMethod": "Static",
            "subnet": {
                "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd"
            },
            "provisioningState": "Succeeded",
            "name": "NIC-config",
            "etag": "W/"4d29b1ca-0207-458c-b258-f298e6fc450f"",
            "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC/ipConfigurations/NIC-config"
            }
        ],
        "tags": {},
        "dnsSettings": {
            "appliedDnsServers": [],
            "dnsServers": []
        },
        "enableIPForwarding": false,
        "provisioningState": "Succeeded",
        "etag": "W/"4d29b1ca-0207-458c-b258-f298e6fc450f"",
        "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC",
        "name": "TestNIC",
        "location": "westeurope"
        }

### 建立您的網路安全性群組和規則

我們現在會建立網路安全性群組 (NSG) 和管理 NIC 存取權的輸入規則。

        azure network nsg create testrg testnsg westeurope

讓我們新增 NSG 的輸入規則以允許連接埠 22 上的輸入連線 (以支援 SSH)：

        azure network nsg rule create --protocol tcp --direction inbound --priority 1000  --destination-port-range 22 --access allow testrg testnsg testnsgrule

> [AZURE.NOTE] 輸入規則是輸入網路連線的篩選器。在此範例中，我們會將 NSG 繫結至 VM 虛擬網路介面卡 (nic)，這表示任何對連接埠 22 的要求都會在 VM 上傳遞到 nic。因為這是關於網路連線開啟連接埠的規則--並非傳統部署中的端點，所以您必須保留 `--source-port-range` 設為 '*' (預設值) 才能接受來自**任何**要求連接埠的輸入要求，這些要求通常是動態的。

### 建立您的公用 IP 位址 (PIP)

現在讓我們建立可讓您從網際網路使用 `azure network public-ip create` 命令連線到 VM 的公用 IP 位址 (PIP)。由於預設值是動態位址，所以我們會使用 `-d testsubdomain` 選項在 **.cloudapp.azure.com** 網域中建立具名的 DNS 項目。

        azure network public-ip create -d testsubdomain testrg testpip westeurope
        info:    Executing command network public-ip create
        + Looking up the public ip "testpip"
        + Creating public ip address "testpip"
        + Looking up the public ip "testpip"
        data:    Id                              : /subscriptions/guid/resourceGroups/testrg/providers/Microsoft.Network/publicIPAddresses/testpip
        data:    Name                            : testpip
        data:    Type                            : Microsoft.Network/publicIPAddresses
        data:    Location                        : westeurope
        data:    Provisioning state              : Succeeded
        data:    Allocation method               : Dynamic
        data:    Idle timeout                    : 4
        data:    Domain name label               : testsubdomain
        data:    FQDN                            : testsubdomain.westeurope.cloudapp.azure.com
        info:    network public-ip create command OK

這也是最上層資源，所以您可以利用 `azure group show` 查看它。

        azure group show testrg --json | jq '.'
        {
        "tags": {},
        "id": "/subscriptions/guid/resourceGroups/TestRG",
        "name": "TestRG",
        "provisioningState": "Succeeded",
        "location": "westeurope",
        "properties": {
            "provisioningState": "Succeeded"
        },
        "resources": [
            {
            "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC",
            "name": "TestNIC",
            "type": "networkInterfaces",
            "location": "westeurope",
            "tags": null
            },
            {
            "id": "/subscriptions/guid/resourceGroups/testrg/providers/Microsoft.Network/publicIPAddresses/testpip",
            "name": "testpip",
            "type": "publicIPAddresses",
            "location": "westeurope",
            "tags": null
            },
            {
            "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet",
            "name": "TestVNet",
            "type": "virtualNetworks",
            "location": "westeurope",
            "tags": null
            },
            {
            "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Storage/storageAccounts/computeteststore",
            "name": "computeteststore",
            "type": "storageAccounts",
            "location": "westeurope",
            "tags": null
            }
        ],
        "permissions": [
            {
            "actions": [
                "*"
            ],
            "notActions": []
            }
        ]
        }

一如往常，您可以調查更多資源的詳細資料，包括使用更完整 `azure network public-ip show` 命令的子網域完整網域名稱 (FQDN)。請注意，公用 IP 位址資源以邏輯方式配置，但尚未指派特定位址。為此，您需要我們尚未建立的 VM。

        azure network public-ip show testrg testpip --json | jq '.'
        {
        "tags": {},
        "publicIpAllocationMethod": "Dynamic",
        "dnsSettings": {
            "domainNameLabel": "testsubdomain",
            "fqdn": "testsubdomain.westeurope.cloudapp.azure.com"
        },
        "idleTimeoutInMinutes": 4,
        "provisioningState": "Succeeded",
        "etag": "W/"c63154b3-1130-49b9-a887-877d74d5ebc5"",
        "id": "/subscriptions/guid/resourceGroups/testrg/providers/Microsoft.Network/publicIPAddresses/testpip",
        "name": "testpip",
        "location": "westeurope"
        }

### 建立公用 IP 和網路安全性群組與 NIC 的關聯

        azure network nic set --public-ip-name test pip

將 NSG 繫結至 NIC：

        azure network nic set --network-security-group-name testnsg testrg testnic

### 建立您的 Linux VM

您已建立儲存體和網路資源以支援可存取網際網路的 VM。現在讓我們建立該 VM，並利用沒有密碼的 ssh 金鑰保障其安全。在此情況下，我們要根據最新的 LTS 建立 Ubuntu VM。我們會使用 `azure vm image list` 找出映像資訊，如[尋找 Azure VM 映像](virtual-machines-linux-cli-ps-findimage.md)所述。我們過往使用命令 `azure vm image list westeurope canonical | grep LTS` 選取映像，而在此情況下，我們將使用 `canonical:UbuntuServer:14.04.3-LTS:14.04.201509080`，但是我們將在最後一個欄位傳遞 `latest`，以便未來可隨時取得最新的組建 (我們使用的字串將會是 `canonical:UbuntuServer:14.04.3-LTS:latest`)。

> [AZURE.NOTE] 已使用 **ssh-keygen -t rsa -b 2048** 在 Linux 或 Mac 上建立 ssh rsa 公用和私人金鑰組的任何人都熟悉下一個步驟。如果您的 `~/.ssh` 目錄中沒有任何憑證金鑰組，您可以建立憑證金鑰組︰<br /> 1. 使用 `azure vm create --generate-ssh-keys` 選項自動進行 2. 使用[自行建立指示](virtual-machines-linux-ssh-from-linux.md)手動進行 <br /> 或者，您可以使用 `azure vm create --admin-username --admin-password` 選項，以使用通常較不安全的使用者名稱和密碼方法，在建立 VM 之後驗證您的 ssh 連線。

我們會藉由利用 `azure vm create` 命令結合所有資源和資訊，以建立 VM。

        azure vm create \            
        --resource-group testrg \
        --name testvm \
        --location westeurope \
        --os-type linux \
        --nic-name testnic \
        --vnet-name testvnet \
        --vnet-subnet-name FrontEnd \
        --storage-account-name computeteststore \
        --image-urn canonical:UbuntuServer:14.04.3-LTS:latest \
        --ssh-publickey-file ~/.ssh/id_rsa.pub \
        --admin-username ops
        info:    Executing command vm create
        + Looking up the VM "testvm"
        info:    Verifying the public key SSH file: /Users/user/.ssh/id_rsa.pub
        info:    Using the VM Size "Standard_A1"
        info:    The [OS, Data] Disk or image configuration requires storage account
        + Looking up the storage account computeteststore
        + Looking up the NIC "testnic"
        info:    Found an existing NIC "testnic"
        info:    Found an IP configuration with virtual network subnet id "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd" in the NIC "testnic"
        info:    This NIC IP configuration has a public ip already configured "/subscriptions/guid/resourcegroups/testrg/providers/microsoft.network/publicipaddresses/testpip", any public ip parameters if provided, will be ignored.
        + Creating VM "testvm"
        info:    vm create command OK

您可以立即使用預設 ssh 金鑰連接至 VM。

        ssh ops@testsubdomain.westeurope.cloudapp.azure.com           
        The authenticity of host 'testsubdomain.westeurope.cloudapp.azure.com (XX.XXX.XX.XXX)' can't be established.
        RSA key fingerprint is b6:a4:7g:4b:cb:cd:76:87:63:2d:84:83:ac:12:2d:cd.
        Are you sure you want to continue connecting (yes/no)? yes
        Warning: Permanently added 'testsubdomain.westeurope.cloudapp.azure.com,XX.XXX.XX.XXX' (RSA) to the list of known hosts.
        Welcome to Ubuntu 14.04.3 LTS (GNU/Linux 3.19.0-28-generic x86_64)
        
        * Documentation:  https://help.ubuntu.com/
        
        System information as of Mon Sep 28 18:45:02 UTC 2015
        
        System load: 0.64              Memory usage: 5%   Processes:       81
        Usage of /:  45.3% of 1.94GB   Swap usage:   0%   Users logged in: 0
        
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
        
        ops@testvm:~$

而且您可以立即使用 `azure vm show testrg testvm` 命令來檢查您建立的內容。此時，您在 Azure 中具有正在執行的 Ubuntu VM，您只能利用您擁有的 ssh 金鑰組登入；密碼會停用。

        azure vm show testrg testvm 
        info:    Executing command vm show
        + Looking up the VM "testvm"
        + Looking up the NIC "testnic"
        + Looking up the public ip "testpip"
        data:    Id                              :/subscriptions/guid/resourceGroups/testrg/providers/Microsoft.Compute/virtualMachines/testvm
        data:    ProvisioningState               :Succeeded
        data:    Name                            :testvm
        data:    Location                        :westeurope
        data:    FQDN                            :testsubdomain.westeurope.cloudapp.azure.com
        data:    Type                            :Microsoft.Compute/virtualMachines
        data:
        data:    Hardware Profile:
        data:      Size                          :Standard_A1
        data:
        data:    Storage Profile:
        data:      Image reference:
        data:        Publisher                   :canonical
        data:        Offer                       :UbuntuServer
        data:        Sku                         :14.04.3-LTS
        data:        Version                     :latest
        data:
        data:      OS Disk:
        data:        OSType                      :Linux
        data:        Name                        :cli4eecdddc349d6015-os-1443465824206
        data:        Caching                     :ReadWrite
        data:        CreateOption                :FromImage
        data:        Vhd:
        data:          Uri                       :https://computeteststore.blob.core.windows.net/vhds/cli4eecdddc349d6015-os-1443465824206.vhd
        data:
        data:    OS Profile:
        data:      Computer Name                 :testvm
        data:      User Name                     :ops
        data:      Linux Configuration:
        data:        Disable Password Auth       :true
        data:        SSH Public Keys:
        data:          Public Key #1:
        data:            Path                    :/home/ops/.ssh/authorized_keys
        data:            Key                     :MIIBrTCCAZigAwIBAgIBATALBgkqhkiG9w0BAQUwADAiGA8yMDE1MDkyODE4MzM0
        <snip>
        data:
        data:    Network Profile:
        data:      Network Interfaces:
        data:        Network Interface #1:
        data:          Id                        :/subscriptions/guid/resourceGroups/testrg/providers/Microsoft.Network/networkInterfaces/testnic
        data:          Primary                   :true
        data:          MAC Address               :00-0D-3A-21-8E-AE
        data:          Provisioning State        :Succeeded
        data:          Name                      :testnic
        data:          Location                  :westeurope
        data:            Private IP alloc-method :Dynamic
        data:            Private IP address      :192.168.1.101
        data:            Public IP address       :40.115.48.189
        data:            FQDN                    :testsubdomain.westeurope.cloudapp.azure.com
        data:
        data:    Diagnostics Instance View:
        info:    vm show command OK

### 後續步驟

現在您已準備好開始處理多個網路元件和 VM。

<!---HONumber=AcomDC_0323_2016-->