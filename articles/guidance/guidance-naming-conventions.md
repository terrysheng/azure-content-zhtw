<properties
   pageTitle="Azure 資源的建議命名慣例 | 指導方針 | Microsoft Azure"
   description="Azure 資源的建議命名慣例。如何為虛擬機器、儲存體帳戶、網路、虛擬網路、子網路和其他 Azure 實體命名"
   services=""
   documentationCenter="na"
   authors="masimms"
   manager="marksou"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/01/2016"
   ms.author="masimms"/>
   
# Azure 資源的建議命名慣例

請務必慎選 Microsoft Azure 資源的名稱，原因如下：

- 難以在稍後變更名稱。
- 名稱必須符合其特定資源類型的需求。

同樣地，一致的命名慣例可讓您更加容易找到資源。此外，也有助於了解資源在方案中的角色。

本文將摘要說明 Azure 資源的命名規則和限制，並提供一組基本的命名慣例建議。您可以使用這些建議來開始建立符合個人需求的特有慣例。

命名慣例的成功關鍵在於必須跨應用程式和組織加以建立及遵循，並隨著在 Azure 平台部署的應用程式和服務增加予以進行調整。

## 命名訂用帳戶

在命名 Azure 訂用帳戶時，詳細的名稱可讓您清楚了解每個訂用帳戶的內容和目的。在可能包含許多訂用帳戶的環境中工作時，遵循共用的命名慣例可以大幅提升使用者對訂用帳戶的了解。

訂用帳戶的建議命名模式如下：

`<Company> <Department (optional)> <Product Line (optional)> <Environment>`

- 在大部分情況下，每個訂用帳戶的 Company (公司) 都相同。不過，有些公司的組織結構內可能有子公司。這些公司可能是由中央 IT 團隊負責管理，在此情況下，我們可以讓其名稱包含母公司名稱 (*Contoso*) 和子公司名稱 (*North Wind*) 來加以區別。

- Department (部門) 是組織內的一群人工作所在單位的名稱。此為命名空間內的選用項目。這是因為有些公司可能因為規模太小而不需要如此向下細分。

- Product Line (產品線) 是產品或部門所行使之職責的特定名稱。雖然對於內部服務和應用程式來說，此項目通常是選用的，但如果是需要能夠輕易區別和辨識 (例如為了清楚區別帳單記錄) 的公開服務，則強烈建議使用此項目。

- Environment (環境) 是描述應用程式或服務之部署生命週期的名稱，例如開發、品管或生產。

| 公司 | 部門 | 產品線或服務 | Environment | 完整名稱 |
----------| ---------- | ----------------------- | ----------- | ---------- |
| Contoso | SocialGaming | AwesomeService | Production | Contoso SocialGaming AwesomeService Production |
| Contoso | SocialGaming | AwesomeService | Dev | Contoso SocialGaming AwesomeService Dev |
| Contoso | IT | InternalApps | Production | Contoso IT InternalApps Production |
| Contoso | IT | InternalApps | Dev | Contoso IT InternalApps Dev |

<!-- TODO; include more information about organizing subscriptions for application deployment, pods, etc -->

## 使用詞綴以避免名稱不夠明確

在 Azure 中命名資源時，建議使用通用的首碼或尾碼來識別資源的類型和內容。雖然關於類型、中繼資料和內容的資訊皆能透過程式設計方式取得，但利用通用詞綴可讓您更容易地進行識別。在將詞綴併入命名慣例時，請務必明確指定詞綴的所在位置是名稱的開頭 (首碼) 或結尾 (尾碼)。

例如，以下是兩個適用於裝載計算引擎之服務的可能名稱：

- SvcCalculationEngine (首碼)
- CalculationEngineSvc (尾碼)

詞綴指的是可說明特定資源的各個層面。下列表格顯示一些常用範例。

| 層面 | 範例 | 注意事項 |
| ------ | ------- | ----- |
| Environment | 開發、生產、品管 | 識別資源的環境 |
| 位置 | uw (美國西部)、ue (美國東部) | 識別資源所要部署到的區域 |
| 執行個體 | 01、02 | 適用於有多個具名執行個體 (Web 伺服器等) 的資源。 |
| 產品或服務 | 服務進行分析 | 識別資源可支援的產品、應用程式或服務 |
| 角色 | sql、Web、傳訊 | 識別相關聯資源的角色 |

在為公司或專案開發特定命名慣例時，請務必選擇一組通用的詞綴及其位置 (尾碼或首碼)。

## 命名規則和限制

Azure 中的每個資源或服務皆會強制實施一組命名限制和範圍。所有命名慣例或模式都必須遵守必要的命名規則和範圍。例如，雖然 VM 的名稱對應至 DNS 名稱 (因此在所有 Azure 中都必須是唯一的名稱)，但 VNET 名稱的範圍則必須侷限在其建立所在的資源群組內。

一般情況下，請避免使用特殊字元 (`-` 或 `_`) 做為名稱的第一個或最後一個字元，否則將會違反大多數的驗證規則。

| 類別 | 服務或實體 | Scope | 長度 | 大小寫 | 有效字元 | 建議模式 | 範例 |
| ------------- | ----------------- | ----- | ------ | ------ | ---------------- | ----------------- | ------- |
| 資源群組 | 資源群組 | 全域 | 1-64 | 不區分大小寫 | 英數字元、底線和連字號 | `<service short name>-<environment>-rg` | `profx-prod-rg` |
| 資源群組 | 可用性設定組 | 資源群組 | 1-80 | 不區分大小寫 | 英數字元、底線和連字號 | `<service-short-name>-<context>-as` | `profx-sql-as` |
| 一般 | Tag | 相關聯的實體 | 512 (名稱)、256 (值) | 不區分大小寫 | 英數字元 | `"key" : "value"` | `"department" : "Central IT"` |
| 計算 | 虛擬機器 | 資源群組 | 1-15 | 不區分大小寫 | 英數字元、底線和連字號 | `<name>-<role>-<instance>` | `profx-sql-001` |
| 儲存體 | 儲存體帳戶名稱 (資料) | 全域 | 3-24 | 小寫 | 英數字元 | `<service short name><type><number>` | `profxdata001` |
| 儲存體 | 儲存體帳戶名稱 (磁碟) | 全域 | 3-24 | 小寫 | 英數字元 | `<vm name without dashes>st<number>` | `profxsql001st0` |
| 儲存體 | 容器名稱 | 儲存體帳戶 | 3-63 |	小寫 | 英數字元和破折號 | `<context>` | `logs` |
| 儲存體 | Blob 名稱 | 容器 | 1-1024 | 區分大小寫 | 任何 URL 字元 | `<variable based on blob usage>` | `<variable based on blob usage>` |
| 儲存體 | Queue name | 儲存體帳戶 | 3-63 | 小寫 | 英數字元和破折號 | `<service short name>-<context>-<num>` | `awesomeservice-messages-001` |
| 儲存體 | 資料表名稱 | 儲存體帳戶 | 3-63 |不區分大小寫 | 英數字元 | `<service short name>-<context>` | `awesomeservice-logs` |
| 儲存體 | 檔案名稱 | 儲存體帳戶 | 3-63 | 小寫 | 英數字元 | `<variable based on blob usage>` | `<variable based on blob usage>` |
| 網路 | 虛擬網路 (VNet) | 資源群組 | 2-80 | 不區分大小寫 | 英數字元、破折號、底線和句點 | `<service short name>-[section]-vnet` | `profx-vnet` |
| 網路 | 子網路 | 父 VNet | 2-80 | 不區分大小寫 | 英數字元、底線、破折號和句點 | `<role>-subnet` | `gateway-subnet` |
| 網路 | 網路介面 | 資源群組 | 1-80 | 不區分大小寫 | 英數字元、破折號、底線和句點 | `<vmname>-<num>nic` | `profx-sql1-1nic` |
| 網路 | 網路安全性群組 | 資源群組 | 1-80 | 不區分大小寫 | 英數字元、破折號、底線和句點 | `<service short name>-<context>-nsg` | `profx-app-nsg` |
| 網路 | 網路安全性群組規則 | 資源群組 | 1-80 | 不區分大小寫 | 英數字元、破折號、底線和句點 | `<descriptive context>` | `sql-allow` |
| 網路 | 公用 IP 位址 | 資源群組 | 1-80 | 不區分大小寫 | 英數字元、破折號、底線和句點 | `<vm or service name>-pip` | `profx-sql1-pip` |
| 網路 | 負載平衡器 | 資源群組 | 1-80 | 不區分大小寫 | 英數字元、破折號、底線和句點 | `<service or role>-lb` | `profx-lb` |
| 網路 | 負載平衡的規則組態 | 負載平衡器 | 1-80 | 不區分大小寫 | 英數字元、破折號、底線和句點 | `descriptive context` | `http` |

<!-- TODO fill in the rest of these resources
| Networking | Azure Application Gateway | Resource Group | 1-80 | Case-insensitive | Alphanumeric, dash, underscore and period | `<service or role>-aag` | `profx-aag`
| Networking | Azure Application Gateway Connection | Azure Application Gateway | 1-80 | Case-insensitive | Alphanumeric, dash, underscore and period | `` | TODO
| Networking | Traffic Manager Profile | Resource Group | 1-80 | Case-insensitive | Alphanumeric, dash, underscore and period | TODO | TODO
-->

## 使用標記組織資源

Azure Resource Manager 支援使用任意文字字串來標記實體，以識別內容並簡化自動化作業。例如，`"sqlVersion: "sql2014ee"` 之類的標記可識別執行 SQL Server 2014 Enterprise Edition 之部署中的 VM，以便對 VM 執行自動化指令碼。標記應該用來與所選擇的命名慣例一起擴充及增強內容。

> [AZURE.TIP] 標記的另一項優點是標記可跨越資源群組，因此可讓您連結不同部署間的實體並予以相互關聯。

每個資源或資源群組最多可以有 **15** 個標記。標記名稱上限為 512 個字元，且標記值上限為 256 字元。

如需資源標記的詳細資訊，請參閱[使用標記來組織您的 Azure 資源](../resource-group-using-tags.md)。

部分常見的標記使用案例包括：

- **計費**：將資源分組，並讓其與計費或退費代碼建立關聯。
- **服務內容識別**：識別所有資源群組的各組資源，以進行常見作業和分組
- **存取控制和安全性內容**：根據產品組合、系統、服務、應用程式、執行個體等項目識別系統管理角色 

> [AZURE.TIP] 請盡早且經常地標記。最好是備有基準標記計畫並隨時調整，而不要事後才來修改。

部分常見的標記方法範例如下：

| 標記名稱 | 金鑰 | 範例 | 註解 |
| -------- | --- | ------- | ------- |
| 帳單收件者/內部退款識別碼 | billTo | `IT-Chargeback-1234` | 內部的 I/O 或計費代碼 |
| 操作員或直接負責人 (DRI) | managedBy | `joe@contoso.com` | 別名或電子郵件地址 |
| 專案名稱 | project-name | `myproject` | 專案或產品線的名稱 |
| 專案版本 | project-version | `3.4` | 專案或產品線的版本 |
| Environment | 環境 | `<Production, Staging, QA >` | 環境識別碼 | 
| 層 | tier | `Front End, Back End, Data` | 層或角色/內容識別 |
| 資料設定檔 | dataProfile | `Public, Confidential, Restricted, Internal` | 資源中所儲存資料的敏感性 |
 
## 秘訣與技巧

根據應用程式類型而定，某些類型的資源的命名和慣例可能需要特別注意。關於這些方面的其他詳細資料和內容列示如下。

### 虛擬機器

特別是在較大型的拓撲中，小心命名虛擬機器將可大幅簡化每個機器之角色和用途的識別，以及製作出更容易預測的指令碼。

> [AZURE.WARNING] 請注意，Azure 中的每個虛擬機器都擁有 Azure 資源名稱和作業系統主機名稱。如果資源名稱和主機名稱不同，這些 VM 可能會不好管理 (例如，如果透過 .vhd 建立虛擬機器，但該 .vhd 已經設定了作業系統與主機名稱)，因此應予以避免。

- [Windows Server VM 的命名慣例](https://support.microsoft.com/zh-TW/kb/188997)

<!-- TODO - recommendations on naming VMs. -->

###	儲存體帳戶和儲存體實體

儲存體帳戶有兩個主要的使用案例，分別是備份 VM 的磁碟以及將資料儲存在 Blob、佇列和資料表中。用於 VM 磁碟的儲存體帳戶應遵循將其與父 VM 名稱建立關聯的命名慣例 (如果可能需要多個儲存體帳戶來用於高階 VM SKU，則還需要運用數字尾碼)。

> [AZURE.TIP] 無論是用於資料還是磁碟的儲存體帳戶，都應該遵循能夠利用多個儲存體帳戶的命名慣例 (也就是一律使用數字尾碼)。

您可以設定自訂網域名稱，以供存取 Azure 儲存體帳戶中的 Blob 資料。Blob 服務的預設端點是 `https://mystorage.blob.core.windows.net`。

但如果您將自訂網域 (如 www.contoso.com) 對應至儲存體帳戶的 Blob 端點，也能使用該網域來存取儲存體帳戶中的 Blob 資料。例如，若使用自訂網域名稱，就能將 `http://mystorage.blob.core.windows.net/mycontainer/myblob` 當成 `http://www.contoso.com/mycontainer/myblob` 來存取。

如需有關設定此功能的詳細資訊，請參閱[針對 Blob 儲存體端點設定自訂網域名稱](../storage/storage-custom-domain-name.md)。

如需有關命名 Blob、容器和資料表的詳細資訊：

- [命名和參考容器、Blob 及中繼資料](https://msdn.microsoft.com/library/dd135715.aspx)
- [命名佇列和中繼資料](https://msdn.microsoft.com/library/dd179349.aspx)
- [命名資料表](https://msdn.microsoft.com/library/azure/dd179338.aspx)

Blob 名稱可以包含任何字元組合，但是必須正確逸出保留的 URL 字元。避免使用結尾是句號 (.)、正斜線 (/) 或一連串的兩者或兩者之組合的 Blob 名稱。依照慣例，正斜線是**虛擬**目錄分隔符號。請勿在 Blob 名稱中使用反斜線 (\\)。雖然用戶端 API 可能允許，但接下來卻會無法正常雜湊且簽章不會相符。

儲存體帳戶或容器的名稱在建立後就無法修改。如果想要使用新名稱，必須先刪除舊名稱再建立新名稱。

> [AZURE.TIP] 建議您先對所有儲存體帳戶和類型建立命名慣例，再開始開發新的服務或應用程式。

## 範例 - 部署多層式架構服務

在此範例中，我們會定義多層式架構的服務組態，其包含前端 IIS 伺服器 (裝載於 Windows Server VM)、SQL Server (裝載於兩個 Windows Server VM)、ElasticSearch 叢集 (裝載於 6 個 Linux VM) 和相關聯的儲存體帳戶、虛擬網路、資源群組和負載平衡器。

我們一開始會先定義此應用程式的內容慣例：

| 實體 | 慣例 | 說明 |
| ------ | ---------- | ------------ |  
| 服務名稱 | `profx` | 所部署之應用程式或服務的簡短名稱 |
| Environment | `prod` | 這適用於生產環境部署 (相對於品管、測試等) |

依據該基準，我們便可以制定出每個資源類型的慣例：

| 資源類型 | 慣例基底 | 範例 |
| ------------- | --------------- | ------- |
| 訂閱 | `<Company> <Department (optional)> <Product Line (optional)> <Environment>` | `Contoso IT InternalApps Profx Production` |
| 資源群組 | `servicename-rg` | `profx-rg` |
| 虛擬網路 | `servicename-vnet` | `profx-vnet` |
| 子網路 | `role-subnet` | `sql-vnet` |
| 負載平衡器 | `servicename-lb` | `profx-lb` |
| 虛擬機器 | `servicename-role[number]` | `profx-sql0` |
| 儲存體帳戶 | `<vmnamenodashes>st<num>` | `profxsql0st0` |

如下圖所示：

![應用程式拓撲圖](media/guidance-naming-conventions/guidance-naming-convention-example.png "範例應用程式拓撲")

## 範例 - 用於部署上面範例的 Azure CLI 指令碼

```bash
#!/bin/sh

#####################################################################
# Sample script using the Azure CLI to build out an application 
# demonstrating naming conventions.  
#
# Note; this script is not intended for production deployment, as it does 
# not create availability sets, configure network security rules, etc.
#####################################################################

# Set up variables to build out the naming conventions for deploying
# the cluster  
LOCATION=eastus2
APP_NAME=profx
ENVIRONMENT=prod
USERNAME=testuser
PASSWORD="testpass"

# Set up the tags to associate with items in the application
TAG_BILLTO="InternalApp-ProFX-12345"
TAGS="billTo=${TAG_BILLTO}"

# Explicitly set the subscription to avoid confusion as to which subscription
# is active/default
SUBSCRIPTION=3e9c25fc-55b3-4837-9bba-02b6eb204331

# Set up the names of things using recommended conventions
RESOURCE_GROUP="${APP_NAME}-${ENVIRONMENT}-rg"
VNET_NAME="${APP_NAME}-vnet"

# Set up the postfix variables attached to most CLI commands
POSTFIX="--resource-group ${RESOURCE_GROUP} --location ${LOCATION} --subscription ${SUBSCRIPTION}"

##########################################################################################
# Set up the VM conventions for Linux and Windows images

# For Windows, get the list of URN's via 
# azure vm image list ${LOCATION} MicrosoftWindowsServer WindowsServer 2012-R2-Datacenter
WINDOWS_BASE_IMAGE=MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20160126

# For Linux, get the list or URN's via 
# azure vm image list ${LOCATION} canonical ubuntuserver
LINUX_BASE_IMAGE=canonical:ubuntuserver:16.04.0-DAILY-LTS:16.04.201602130

#########################################################################################
## Define functions 
create_vm ()
{
    vm_name=$1
    vnet_name=$2
    subnet_name=$3
    os_type=$4
    vhd_path=$5
    vm_size=$6
    diagnostics_storage=$7

	# Create the network interface card for this VM
	azure network nic create --name "${vm_name}-0nic" --subnet-name ${subnet_name} --subnet-vnet-name ${vnet_name} \
        --tags="${TAGS}" ${POSTFIX}

	# Create the storage account for this vm's disks (premium locally redundant storage -> PLRS)
    # Note the ${var//-/} syntax to remove dashes from the vm name
    storage_account_name=${vm_name//-/}st01
	azure storage account create --type=PLRS --tags "${TAGS}" ${POSTFIX} "${storage_account_name}"

    # Map the name of the diagnostics storage account to a blob URI for boot diagnostics
    # This is (currently) required when deploying with a named premium storage account 
    diag_blob="https://${diagnostics_storage}.blob.core.windows.net/"

    # Create the VM
    azure vm create --name ${vm_name} --nic-name "${vm_name}-0nic" --os-type ${os_type} \
        --image-urn ${vhd_path} --vm-size ${vm_size} --vnet-name ${vnet_name} --vnet-subnet-name ${subnet_name} \
        --storage-account-name "${storage_account_name}" --storage-account-container-name vhds --os-disk-vhd "${vm_name}-osdisk.vhd" \
        --admin-username "${USERNAME}" --admin-password "${PASSWORD}" \
		--boot-diagnostics-storage-uri "${diag_blob}" \
        --tags="${TAGS}" ${POSTFIX} 
}

###################################################################################################
# Create resources

# Step 1 - create the enclosing resource group
azure group create --name "${RESOURCE_GROUP}" --location "${LOCATION}" --tags "${TAGS}" --subscription "${SUBSCRIPTION}"

# Step 2 - create the network security groups

# Step 3 - create the networks (VNet and subnets)
azure network vnet create --name "${VNET_NAME}" --address-prefixes="10.0.0.0/8" --tags "${TAGS}" ${POSTFIX}
# TODO - does subnet support tagging?
azure network vnet subnet create --name gateway-subnet --vnet-name "${VNET_NAME}" --address-prefix="10.0.1.0/24" --resource-group "${RESOURCE_GROUP}" --subscription ${SUBSCRIPTION}
azure network vnet subnet create --name es-master-subnet --vnet-name "${VNET_NAME}" --address-prefix="10.0.2.0/24" --resource-group "${RESOURCE_GROUP}" --subscription ${SUBSCRIPTION}
azure network vnet subnet create --name es-data-subnet --vnet-name "${VNET_NAME}" --address-prefix="10.0.3.0/24" --resource-group "${RESOURCE_GROUP}" --subscription ${SUBSCRIPTION}
azure network vnet subnet create --name sql-subnet --vnet-name "${VNET_NAME}" --address-prefix="10.0.4.0/24" --resource-group "${RESOURCE_GROUP}" --subscription ${SUBSCRIPTION}

# Step 4 - define the load balancer and network security rules
azure network lb create --name "${APP_NAME}-lb" ${POSTFIX}
# In a production deployment script, we'd create load balancer rules and 
# network security groups here

# Step 5 - create a diagnostics storage account
diagnostics_storage_account=${APP_NAME//-/}diag
azure storage account create --type=LRS --tags "${TAGS}" ${POSTFIX} "${diagnostics_storage_account}"

# Step 6.1 - Create the gateway VMs
for i in `seq 1 4`;
do
	create_vm "${APP_NAME}-gw${i}" "${APP_NAME}-vnet" "gateway-subnet" "Windows" "${WINDOWS_BASE_IMAGE}" "Standard_DS1" "${diagnostics_storage_account}" 
done    

# Step 6.2 - Create the ElasticSearch master and data VMs
for i in `seq 1 3`;
do
    create_vm "${APP_NAME}-es-master${i}" "${APP_NAME}-vnet" "es-master-subnet" "Linux" "${LINUX_BASE_IMAGE}" "Standard_DS1" "${diagnostics_storage_account}"
done
for i in `seq 1 3`;
do
    create_vm "${APP_NAME}-es-data${i}" "${APP_NAME}-vnet" "es-data-subnet" "Linux" "${LINUX_BASE_IMAGE}" "Standard_DS1" "${diagnostics_storage_account}"
done

# Step 6.3 - Create the SQL VMs
create_vm "${APP_NAME}-sql0" "${APP_NAME}-vnet" "sql-subnet" "Windows" "${WINDOWS_BASE_IMAGE}" "Standard_DS1" "${diagnostics_storage_account}"
create_vm "${APP_NAME}-sql1" "${APP_NAME}-vnet" "sql-subnet" "Windows" "${WINDOWS_BASE_IMAGE}" "Standard_DS1" "${diagnostics_storage_account}"
```

<!---HONumber=AcomDC_0302_2016-------->