<properties
   pageTitle="執行虛擬機器 (Windows) |藍圖 | Microsoft Azure"
   description="如何在 Azure 上執行單一 VM，並注意延展性、恢復能力、管理性和安全性。"
   services=""
   documentationCenter="na"
   authors="mikewasson"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/17/2016"
   ms.author="mikewasson"/>

# 在 Azure 上執行單一 Windows VM

本文概述一組已經實證的做法，以便在 Azure 上執行單一 VM，並注意延展性、恢復能力、管理性和安全性。

> [AZURE.WARNING] Azure 上的單一 VM 沒有正常運作 SLA。請使用此組態進行開發和測試，而不是做為生產部署。

Azure 有兩個不同的部署模型：[資源管理員][resource-manager-overview]和傳統。本文使用 Microsoft 建議用於新部署的資源管理員。有幾種方式可使用資源管理員，包括 [Azure 入口網站][azure-portal]、[Azure PowerShell][azure-powershell]、[Azure CLI][azure-cli] 命令，或[資源管理員範本][arm-templates]。本文章包含使用 Azure CLI 的範例。

![IaaS：單一 VM](media/guidance-compute-single-vm.png)

在 Azure 中佈建單一 VM 牽涉到比核心 VM 本身更多的移動組件。有計算、網路及儲存體元素。

- **資源群組。** 建立[資源群組][resource-manager-overview]以保存此 VM 的資源。_資源群組_是保存相關資源的容器。

- **VM**。您可以從已發佈的映像清單，或從您上傳至 Azure Blob 儲存體的 VHD 檔案佈建 VM。

- **作業系統磁碟。** 作業系統磁碟是儲存在 [Azure 儲存體][azure-storage]中的 VHD。這表示即使主機電腦關閉，它仍持續存在。

- **暫存磁碟。** 使用暫存磁碟 (Windows 上的 `D:` 磁碟機) 建立 VM。此磁碟會儲存在主機電腦的實體磁碟機上。不會儲存在 Azure 儲存體中，而且可能在重新開機期間和其他 VM 生命週期事件中消失。僅將此磁碟使用於暫存資料，例如分頁檔或交換檔。

- **資料磁碟。** [資料磁碟][data-disk]是用於應用程式資料的持續性 VHD。資料磁碟會儲存在 Azure 儲存體中，例如作業系統磁碟。

- **虛擬網路 (VNet) 和子網路。** 在 Azure 中的每個 VM 都會部署到虛擬網路 (VNet) 中，而虛擬網路會盡一步分成數個子網路。

- **公用 IP 位址。** 必須要有公用 IP 位址才能與 VM 進行通訊，例如透過遠端桌面 (RDP)。

- **網路安全性群組 (NSG)**。[NSG][nsg] 用來允許/拒絕 VM 的網路流量。預設的 NSG 規則不允許所有傳入的網際網路流量。

- **網路介面卡 (NIC)**。NIC 可讓 VM 與虛擬網路通訊。

- **診斷。** 診斷記錄對於管理和針對 VM 進行疑難排解十分重要。

## VM 建議

- 將現有的工作負載移至 Azure 時，選擇最符合您的內部部署伺服器的 [VM 大小][virtual-machine-sizes]。我們建議 DS 和 GS 系列，其可使用進階儲存體來處理 I/O 密集的工作負載。

    - 如果您的工作負載不需要高效能、低延遲的磁碟存取，請考慮其他標準層 VM 大小，例如 A 系列或 D 系列。

- 當您佈建 VM 和其他資源時，您必須指定一個位置。一般而言，選擇最接近您的內部使用者或客戶的位置。不過，並非所有的 VM SKU 均可用於所有位置。如需詳細資訊，請參閱[依區域提供的服務][services-by-region]。

- 如需選擇已發佈 VM 映像的相關資訊，請參閱[瀏覽並選取 Azure 虛擬機器映像][select-vm-image]。

## 磁碟和儲存體建議

我們建議使用[進階儲存體][premium-storage]，以獲得最佳的磁碟 I/O 效能。不過，請注意進階儲存體需要 DS 或 GS 系列 VM。

- 進階儲存體的成本是以佈建的磁碟大小為基礎。IOPS 和輸送量 (亦即，資料傳輸速率) 也取決於磁碟大小，因此當您佈建磁碟時，請考慮以下三個因素 (容量、IOPS 和輸送量)。

- 標準儲存體的成本是以寫入磁碟的資料量為基礎。因此，佈建最大大小 (1023 GB) 是相當好的做法。不過，在格式化磁碟時，請務必使用快速格式。完整磁碟格式會將零寫入磁碟，以致使用到實際的儲存體。請參閱 [Azure 儲存體價格][storage-price]。

- 如果您選取標準儲存體，我們建議使用異地備援儲存體 (GRS)，因為即使發生主要區域因全區中斷或嚴重損壞而無法復原的情況，您的資料仍會是永久性。

- 新增一或多個資料磁碟。當您建立新的 VHD 時，它仍未格式化。登入 VM 來格式化磁碟。

- 如果您有大量的資料磁碟，請注意儲存體帳戶的總 I/O 限制。如需詳細資訊，請參閱[虛擬機器磁碟限制][vm-disk-limits]。

- 為了達到最佳效能，請建立個別的儲存體帳戶來保存診斷記錄。標準本地備援儲存體 (LRS) 帳戶已足以保存診斷記錄。

## 網路建議

- 對於單一 VM，建立含有一個子網路的一個 VNet。並且建立 NSG 和公用 IP 位址。

- 此公用 IP 位址可以是動態或靜態。預設值為動態。

    - 如果您需要不會變更的固定 IP 位址，請保留[靜態 IP 位址][static-ip] &mdash; 例如，如果您需要在 DNS 中建立 A 記錄，或需要將 IP 位址列入白名單。

    - 根據預設，IP 位址沒有完整網域名稱 (FQDN)。如需詳細資訊，請參閱[在 Azure 入口網站中建立完整網域名稱][fqdn]。

- 配置 NIC 並將它與 IP 位址、子網路和 NSG 產生關聯。

- 預設的 NSG 規則不允許 RDP。若要啟用 RDP，請新增一個規則，以允許 TCP 連接埠 3389 的輸入流量。

## 延展性

您可以藉由變更 VM 大小來相應增加或相應減少 VM。下列 Azure CLI 命令調整 VM 大小：

```text
azure vm set -g <<resource-group>> --vm-size <<new-vm-size>
    --boot-diagnostics-storage-uri <<storage-account-uri>> <<vm-name>>
```

調整 VM 大小將會致使系統重新啟動，並在重新啟動之後，重新對應您現有的作業系統和資料磁碟。暫存磁碟的所有項目將會遺失。`--boot-diagnostics-storage-uri` 選項可讓[開機診斷][boot-diagnostics] 記錄與啟動相關的任何錯誤。

您可能無法從一個 SKU 系列擴充至另一個系列 (例如，從 A 系列擴充至 G 系列)。使用下列 CLI 命令來取得現有 VM 的可用大小清單：

```text
azure vm sizes -g <<resource-group>> --vm-name <<vm-name>>
```

若要調整為未列出的大小，您必須刪除 VM 執行個體並建立一個新的執行個體。刪除 VM 時不會刪除 VHD。

## Availability

- 單一 VM 沒有可用性 SLA。若要達到 SLA 可用性，您必須將多個 VM 部署到可用性設定組中。

- 您的 VM 可能會受到[計劃性維護][planned-maintenance]或[非計劃性維護][manage-vm-availability]影響。您可以使用 [VM 重新啟動記錄檔][reboot-logs]來判斷 VM 重新開機是否由計劃性維護所造成。

- 請勿將單一 VM 放入可用性設定組中。將 VM 放入可用性設定組時，會告知 Azure 將 VM 視為多執行個體集合的一部分，而且您不會收到任何有關計劃性維護重新開機的預先警告或通知。

- VHD 是由 [Azure 儲存體][azure-storage]支援，而 Azure 儲存體會為了維持持久性和可用性而進行複寫。

- 若要防止在正常作業期間意外遺失資料 (例如，由於使用者錯誤)，您也應該使用 [Blob 快照][blob-snapshot]或其他工具實作時間點備份。

## 可管理性

- 執行下列 CLI 命令來啟用 VM 診斷：
    
    ```text
    azure vm enable-diag <<resource-group>> <<vm-name>>
    ```
    
    此命令可啟用基本健康情況度量、診斷基礎結構記錄檔及開機診斷。如需詳細資訊，請參閱[啟用監視和診斷][enable-monitoring]。

- 使用 [Azure 記錄檔收集][log-collector]延伸模組來收集記錄檔並上傳至 Azure 儲存體。

- Azure 會區分「已停止」和「已取消配置」狀態。當 VM 狀態為「已停止」時，您需要付費。VM 解除配置後，則不需付費(請參閱 [Azure VM 常見問題集][vm-faq])。

    使用下列 CLI 命令來解除配置 VM：
    
    ```text
    azure vm deallocate <<resource-group>> <<vm-name>>
    ```
    
    注意：Azure 入口網站中的 [停止] 按鈕也會取消配置 VM。不過，如果您從 Windows 內部關閉 (透過 RDP)，VM 會停止但不會取消配置，因此您仍需付費。

- 如果您刪除 VM，並不會刪除 VHD。這表示您可以放心地刪除 VM，而不會遺失任何資料。不過，您仍需支付儲存體費用。若要刪除 VHD，請從 [Blob 儲存體][blog-storage]刪除檔案。

- 若要調整 OS 磁碟大小，請下載 .vhd 檔案並使 [RESIZE-VHD][Resize-VHD] 用之類的工具調整 VHD 大小。將已調整大小的 VHD 上傳至 Blob 儲存體，然後刪除 VM 執行個體並佈建一個使用已調整大小的 VHD 的新執行個體。


## 安全性

- 使用 [Azure 資訊安全中心][security-center]，可集中檢閱 Azure 資源的安全性狀態。資訊安全中心會監視潛在的安全性問題，例如系統更新、反惡意程式碼和端點 ACL，並提供全面性的部署安全性健康狀態。**附註：**在撰寫本文時，資訊安全中心仍處於預覽狀態。

- 使用[角色型存取控制][rbac] (RBAC) 來定義哪些 DevOps 團隊成員可以管理您部署的 Azure 資源 (VM、網路等等)。

- 請考慮安裝[安全性延伸模組][security-extensions]。

- 使用 [Azure 磁碟加密][disk-encryption]來加密作業系統和資料磁碟。**附註：**在撰寫本文時，Azure 磁碟加密仍處於預覽狀態。

## 疑難排解

- 若要重設本機系統管理員密碼，請執行 `vm reset-access` Azure CLI 命令。
    
    ```text
    azure vm reset-access -u <<user>> -p <<new-password>> <<resource-group>> <<vm-name>>
    ```
    
- 如果您的 VM 進入無法開機的狀態，請使用[開機診斷][boot-diagnostics]來診斷開機失敗。

- 查看[稽核記錄檔][audit-logs]中的佈建動作和其他 VM 事件。

## Azure CLI 命令 (範例)

下列 Windows 批次指令碼會執行 [Azure CLI][azure-cli] 命令，以部署單一 VM 執行個體和相關的網路和儲存體資源 (如前一個圖所示)。

```bat
ECHO OFF
SETLOCAL

IF "%~1"=="" (
    ECHO Usage: %0 subscription-id
    EXIT /B
    )

:: Set up variables to build out the naming conventions for deploying
:: the cluster

SET LOCATION=eastus2
SET APP_NAME=app1
SET ENVIRONMENT=dev
SET USERNAME=testuser
SET PASSWORD=AweS0me@PW

:: Explicitly set the subscription to avoid confusion as to which subscription
:: is active/default
SET SUBSCRIPTION=%1

:: Set up the names of things using recommended conventions
SET RESOURCE_GROUP=%APP_NAME%-%ENVIRONMENT%-rg
SET VM_NAME=%APP_NAME%-vm0

SET IP_NAME=%APP_NAME%-pip
SET NIC_NAME=%VM_NAME%-0nic
SET NSG_NAME=%APP_NAME%-nsg
SET SUBNET_NAME=%APP_NAME%-subnet
SET VNET_NAME=%APP_NAME%-vnet
SET VHD_STORAGE=%VM_NAME:-=%st0
SET DIAGNOSTICS_STORAGE=%VM_NAME:-=%diag

:: For Windows, use the following command to get the list of URNs:
:: azure vm image list %LOCATION% MicrosoftWindowsServer WindowsServer 2012-R2-Datacenter
SET WINDOWS_BASE_IMAGE=MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20160126

:: For a list of VM sizes see...
SET VM_SIZE=Standard_DS1

:: Set up the postfix variables attached to most CLI commands
SET POSTFIX=--resource-group %RESOURCE_GROUP% --location %LOCATION% ^
  --subscription %SUBSCRIPTION%

CALL azure config mode arm

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
:: Create resources

:: Create the enclosing resource group
CALL azure group create --name %RESOURCE_GROUP% --location %LOCATION%

:: Create the VNet
CALL azure network vnet create --address-prefixes 172.17.0.0/16 ^
  --name %VNET_NAME% %POSTFIX%

:: Create the subnet
CALL azure network vnet subnet create --vnet-name %VNET_NAME% --address-prefix ^
  172.17.0.0/24 --name %SUBNET_NAME% --resource-group %RESOURCE_GROUP% ^
  --subscription %SUBSCRIPTION%

:: Create the public IP address (dynamic)
CALL azure network public-ip create --name %IP_NAME% %POSTFIX%

:: Create the network security group
CALL azure network nsg create --name %NSG_NAME% %POSTFIX%

:: Create the NIC
CALL azure network nic create --network-security-group-name %NSG_NAME% ^
  --public-ip-name %IP_NAME% --subnet-name %SUBNET_NAME% --subnet-vnet-name ^
  %VNET_NAME%  --name %NIC_NAME% %POSTFIX%

:: Create the storage account for the OS VHD
CALL azure storage account create --type PLRS %POSTFIX% %VHD_STORAGE%

:: Create the storage account for diagnostics logs
CALL azure storage account create --type LRS %POSTFIX% %DIAGNOSTICS_STORAGE%

:: Create the VM
CALL azure vm create --name %VM_NAME% --os-type Windows --image-urn ^
  %WINDOWS_BASE_IMAGE% --vm-size %VM_SIZE%   --vnet-subnet-name %SUBNET_NAME% ^
  --nic-name %NIC_NAME% --vnet-name %VNET_NAME% --storage-account-name ^
  %VHD_STORAGE% --os-disk-vhd "%VM_NAME%-osdisk.vhd" --admin-username ^
  "%USERNAME%" --admin-password "%PASSWORD%" --boot-diagnostics-storage-uri ^
  "https://%DIAGNOSTICS_STORAGE%.blob.core.windows.net/" %POSTFIX%

:: Attach a data disk
CALL azure vm disk attach-new -g %RESOURCE_GROUP% --vm-name %VM_NAME% ^
  --size-in-gb 128 --vhd-name data1.vhd --storage-account-name %VHD_STORAGE%

:: Allow RDP
CALL azure network nsg rule create -g %RESOURCE_GROUP% --nsg-name %NSG_NAME% ^
  --direction Inbound --protocol Tcp --destination-port-range 3389 ^
  --source-port-range * --priority 100 --access Allow RDPAllow
```

<!-- links -->

[arm-templates]: ../virtual-machines/virtual-machines-deploy-rmtemplates-azure-cli.md
[audit-logs]: https://azure.microsoft.com/zh-TW/blog/analyze-azure-audit-logs-in-powerbi-more/
[azure-cli]: ../virtual-machines/virtual-machines-command-line-tools.md
[azure-portal]: ../azure-portal/resource-group-portal.md
[azure-powershell]: ../powershell-azure-resource-manager.md
[azure-storage]: ../storage/storage-introduction.md
[blob-snapshot]: ../storage/storage-blob-snapshots.md
[blog-storage]: ../storage/storage-introduction.md
[boot-diagnostics]: https://azure.microsoft.com/zh-TW/blog/boot-diagnostics-for-virtual-machines-v2/
[data-disk]: ../virtual-machines/virtual-machines-disks-vhds.md
[disk-encryption]: ../azure-security-disk-encryption.md
[enable-monitoring]: ../azure-portal/insights-how-to-use-diagnostics.md
[fqdn]: ../virtual-machines/virtual-machines-create-fqdn-on-portal.md
[log-collector]: https://azure.microsoft.com/zh-TW/blog/simplifying-virtual-machine-troubleshooting-using-azure-log-collector/
[manage-vm-availability]: ../virtual-machines/virtual-machines-manage-availability.md
[nsg]: ../virtual-network/virtual-networks-nsg.md
[password-reset]: ../virtual-machines/virtual-machines-windows-reset-password.md
[planned-maintenance]: ../virtual-machines/virtual-machines-planned-maintenance.md
[premium-storage]: ../storage/storage-premium-storage-preview-portal.md
[rbac]: ../active-directory/role-based-access-control-configure.md
[reboot-logs]: https://azure.microsoft.com/zh-TW/blog/viewing-vm-reboot-logs/
[Resize-VHD]: https://technet.microsoft.com/zh-TW/library/hh848535.aspx
[resource-manager-overview]: ../resource-group-overview.md
[security-center]: https://azure.microsoft.com/zh-TW/services/security-center/
[security-extensions]: ../virtual-machines/virtual-machines-extensions-features.md#security-and-protection
[select-vm-image]: ../virtual-machines/resource-groups-vm-searching.md
[services-by-region]: https://azure.microsoft.com/zh-TW/regions/#services
[static-ip]: ../virtual-network/virtual-networks-reserved-public-ip.md
[storage-price]: https://azure.microsoft.com/pricing/details/storage/
[virtual-machine-sizes]: ../virtual-machines/virtual-machines-size-specs.md
[vm-disk-limits]: ../azure-subscription-service-limits.md#virtual-machine-disk-limits
[vm-faq]: ../virtual-machines/virtual-machines-questions.md

<!---HONumber=AcomDC_0302_2016-------->