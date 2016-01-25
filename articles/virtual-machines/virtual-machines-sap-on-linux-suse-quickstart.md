<properties
   pageTitle="測試 Microsoft Azure SUSE Linux VM 上的 SAP NetWeaver | Microsoft Azure"
   description="在 Microsoft Azure SUSE Linux VM 上測試 SAP NetWeaver"
   services="virtual-machines,virtual-network,storage"
   documentationCenter="saponazure"
   authors="hermanndms"
   manager="juergent"
   editor=""
   tags="azure-resource-manager"
   keywords=""/>
<tags
   ms.service="virtual-machines"
   ms.devlang="NA"
   ms.topic="campaign-page"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="na"
   ms.date="11/26/2015"
   ms.author="hermannd"/>

# 在 Microsoft Azure SUSE Linux VM 上測試 SAP NetWeaver


以下是在 Microsoft Azure SUSE Linux VM 上測試 SAP NetWeaver 時要考量的項目清單。目前尚無關於 SAP-Linux-Azure 的官方 SAP 支援的說明。不過，客戶可以執行不需要官方 SAP 支援的一些測試、示範或原型設計。

下列資訊應該協助您避免一些潛在的問題。



## 在 Microsoft Azure 上測試 SAP 的 SUSE 映像

對於 Azure 上的 SAP 測試，僅使用 SUSE Linux Enterprise Server (SLES) 11 SP4 和 SLES 12。您可以在 Azure Marketplace 找到特殊的 SUSE 映像 ("SLES 11 SP3 for SAP CAL")，但這不適用一般用途。針對 [SAP 雲端應用裝置程式庫](https://cal.sap.com/) 解決方案保留。沒有任何選項可以隱藏此映像。所以，就不使用它。

您應該對 Azure 上的所有新測試使用 Azure 資源管理員。若要尋找 SUSE SLES 映像和使用 Azure Powershell 或 Azure 命令列介面 (CLI) 的版本，請使用下列命令。然後可以使用輸出，例如，在 json 範本中定義作業系統映像，以部署新的 SUSE Linux VM。下列 PowerShell 命令適用於 Azure Powershell 版本 1.0.1 或更新版本。

* 尋找現有的發佈者，包括 SUSE：

   ```
   PS  : Get-AzureRmVMImagePublisher -Location "West Europe"  | where-object { $_.publishername -like "*US*"  }
   CLI : azure vm image list-publishers westeurope | grep "US"
   ```

* 從 SUSE 尋找現有的供應項目：

   ```
   PS  : Get-AzureRmVMImageOffer -Location "West Europe" -Publisher "SUSE"
   CLI : azure vm image list-offers westeurope SUSE
   ```

* 尋找 SUSE SLES 供應項目：

   ```
   PS  : Get-AzureRmVMImageSku -Location "West Europe" -Publisher "SUSE" -Offer "SLES"
   CLI : azure vm image list-skus westeurope SUSE SLES
   ```

* 尋找 SLES SKU 的特定版本：

   ```
   PS  : Get-AzureRmVMImage -Location "West Europe" -Publisher "SUSE" -Offer "SLES" -skus "12"
   CLI : azure vm image list westeurope SUSE SLES 12
   ```

## 在 SUSE VM 中安裝 WALinuxAgent

稱為 WALinuxAgent 的代理程式是 Azure Marketplace 中的 SLES 映像的一部分。您可以在下列位置找到手動安裝它 (例如從內部部署上傳 SLES OS VHD) 的相關資訊：

- [OpenSUSE](http://software.opensuse.org/package/WALinuxAgent)

- [Azure](virtual-machines-linux-endorsed-distributions.md)

- [SUSE](https://www.suse.com/communities/blog/suse-linux-enterprise-server-configuration-for-windows-azure/)

## 將 Azure 資料磁碟連結至 Azure Linux VM

請永遠不要透過裝置識別碼將 Azure 資料磁碟掛接到 Azure Linux VM。請改用 UUID。使用工具 (例如圖形工具) 來掛接 Azure 資料磁碟時要小心。請仔細檢查 /et/fstab 中的項目。

裝置識別碼的問題在於它可能會變更，然後 Azure VM 可能會在開機程序停止回應。您可能會在 /etc/fstab 新增 nofail 參數來解決這個問題。但請留意，nofail 應用程式可能會以先前的方式使用掛接點，而且萬一外部 Azure 資料磁碟未在開機期間掛接，可能會寫入根目錄檔案系統。

## 從內部部署將 SUSE VM 上傳至 Azure

[這篇文章](virtual-machines-linux-create-upload-vhd-suse.md)描述從內部部署將 SUSE VM 上傳至 Azure 的步驟。

如果您想要上傳 VM，但是希望結束時沒有取消佈建步驟以保留像是現有的 SAP 安裝及主機名稱等，請檢查下列項目：

* 確定作業系統磁碟是透過 UUID 掛接，而不是透過裝置識別碼。只在 /etc/fstab 變更 UUID 對作業系統磁碟而言並不夠。同時，不要忘了調整開機載入器，透過 Yast 或編輯 /boot/grub/menu.lst
* 如果您針對 SUSE 作業系統磁碟使用 VHDX 格式，為了上傳至 Azure 而將它轉換為 VHD，很有可能網路裝置已從 eth0 變更為 eth1。為避免稍後在 Azure 上開機時發生問題，您應改回 eth0，如[這篇文章](https://dartron.wordpress.com/2013/09/27/fixing-eth1-in-cloned-sles-11-vmware/)所述。

除了文章中說明的以外，建議您一併移除下列項目：

   # sudo mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/

只要沒有多個 NIC，安裝 Azure Linux 代理程式 (waagent) 應該也可協助您避免任何潛在問題。

## 在 Azure 上部署 SUSE VM

新的 SUSE VM 應該透過新的 Azure 資源管理員模型中的 JSON 範本檔案建立。一旦建立 JSON 範本檔案，就可以使用下列 CLI 命令做為 Powershell 的替代來部署 VM：

   ``` azure 群組部署建立 "<deployment name>" -g "<resource group name>" --template-file "<../../filename.json>"

   ``` 您可以在[這篇文章](resource-group-authoring-templates.md)和[此網頁](https://azure.microsoft.com/documentation/templates/)找到 JSON 範本檔案的更多詳細資料。

您可以在[這篇文章](xplat-cli-azure-resource-manager.md)中找到 CLI 與 Azure 資源管理員的更多詳細資料。

## SAP 授權與硬體金鑰

針對官方的 SAP-Windows-Azure 憑證，已經有新的機制可以計算 SAP 授權使用的 SAP 硬體金鑰。要使用此機制，必須調整 SAP 核心。Linux 目前的 SAP 核心版本不包括此程式碼變更。因此，在某些情況下 (例如 Azure VM 調整大小)，SAP 硬體金鑰可能會變更，而且 SAP 授權不再有效

## SUSE sapconf 封裝

SUSE 提供稱為 "sapconf" 的封裝，這組封裝負責一組 SAP 特定設定。如需更多封裝的詳細資訊，用途以及安裝和使用方式，可在這裡找到：[此 SUSE 部落格項目](https://www.suse.com/communities/blog/using-sapconf-to-prepare-suse-linux-enterprise-server-to-run-sap-systems/)和[此 SAP 部落格項目](http://scn.sap.com/community/linux/blog/2014/03/31/what-is-sapconf-or-how-to-prepare-a-suse-linux-enterprise-server-for-running-sap-systems)

## 分散式 SAP 安裝中的 NFS 共用

如果在分散式安裝中，您想要在個別的 VM 安裝如資料庫和 SAP 應用程式伺服器等，可能會透過網路檔案系統 (NFS) 共用 /sapmnt 目錄。如果為 /sapmnt 建立 NFS 共用之後安裝步驟發生問題，請檢查是否已為共用設定 "no\_root\_squash"。這是內部測試案例中的解決方案。


## 邏輯磁碟區

邏輯磁碟區管理員 (LVM) 在 Azure 上並未經過徹底的驗證。如果您需要跨多個 Azure 資料磁碟的大型邏輯磁碟區 (例如 SAP 資料庫)，應使用 mdadm。[這篇文章](virtual-machines-linux-configure-raid.md)說明如何使用 mdadm 在 Azure 上設定 Linux RAID：


## Azure SUSE 儲存機制

如果存取標準 Azure SUSE 儲存機制發生問題，有一個簡單的命令可以重設它。在 Azure 區域建立私用的作業系統映像，然後將該映像複製到不同的區域時，若您想依據這個私用的作業系統映像部署新的 VM 時，就可能會發生此問題。只要在 VM 中執行下列命令：

   ```
   service guestregister restart
   ```

## Gnome 桌面

如果您想要使用 Gnome 桌面，在單一 VM (SAP GUI、瀏覽器及 SAP 管理主控台等) 內安裝完整的 SAP 示範系統，這裡提供在 Azure SLES 映像上安裝它的一點提示：

   SLES 11

   ```
   zypper in -t pattern gnome
   ```

   SLES 12

   ```
   zypper in -t pattern gnome-basic
   ```

## 雲端中的 Linux 上的 SAP-Oracle 支援

在虛擬環境中，Oracle 對 Linux 的支援有所限制。這是一般主題，而非 Azure 特定主題。不過，卻有了解的必要。SAP 將不支援 SUSE 上的 Oracle 或類似 Azure 的公用雲端中的 Red Hat。客戶應直接連絡 Oracle 來討論這個主題。

<!---HONumber=AcomDC_0114_2016-->