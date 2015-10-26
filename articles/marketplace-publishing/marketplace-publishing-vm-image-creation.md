<properties
   pageTitle="建立 Azure Marketplace 的虛擬機器映像 | Microsoft Azure"
   description="如何建立 Azure Marketplace 的虛擬機器映像以讓其他人購買的詳細指示。"
   services="Azure Marketplace"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

<tags
   ms.service="marketplace-publishing"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="Azure"
   ms.workload="na"
   ms.date="10/09/2015"
   ms.author="hascipio; v-divte"/>

# 建立 Azure Marketplace 的虛擬機器映像的指南

本文的**步驟 2** 將逐步引導準備您的 VHD，這是 SKU 的基礎，而 SKU 則為您部署到 Azure Marketplace 的項目。這個程序將因為您是否提供以 Linux 或 Windows 為基礎的 SKU 而有所不同。本節涵蓋這兩種案例。這個程序可與[帳戶建立和註冊][link-acct-creation]同步執行。

## 1\.定義供應項目和 SKU

在本節中，您將定義供應項目和其下的 SKU。

供應項目是其所有 SKU 的「上層」。您可以擁有多個供應項目。您可以隨意決定供應項目的建構方式。當供應項目進入預備環境，它的所有 SKU 也會一起進入。細心考量您的 SKU 識別碼，因為這些識別碼會在 URL 中出現。

- Azure.com – http://azure.microsoft.com/marketplace/partners/{PartnerNamespace}/{OfferIdentifier}-{SKUidentifier}

- Azure Preview 入口網站 - https://portal.azure.com/#gallery/{PublisherNamespace}.{OfferIdentifier}{SKUIDdentifier}

SKU 是 VM 映像的商務名稱。VM 映像包含一個 OS 磁碟和零或多個資料磁碟。它在本質上是虛擬機器的完整儲存體設定檔。每個磁碟都需要一個 VHD；即使空的資料磁碟也需要建立一個 VHD。

無論您使用哪一種作業系統，您只能加入 SKU 所需的最少數目資料磁碟。使用者無法在部署時移除屬於映像一部分的磁碟，但是可隨時在必要時於部署期間或之後加入磁碟。

### 1\.1 加入供應項目

1. 使用您的賣方帳戶登入[發佈入口網站][link-pubportal]。
2. 輸入發佈入口網站的 [虛擬機器] 索引標籤。在提示的輸入欄位中輸入您的供應項目名稱並且建立。
3. 供應項目名稱通常是您計劃在 Azure Marketplace 中銷售的產品/服務的名稱。

### 1\.2 定義 SKU
一旦您加入供應項目之後，您必須定義/識別您的 SKU。您可以有多個供應項目，每個供應項目在其下可以有多個 SKU。當供應項目進入預備環境，它的所有 SKU 也會一起進入。

1. **加入 SKU。** 此步驟必須具備將用於 URL 中的識別碼。它必須是發行設定檔中的唯一識別碼，但是若與其他發行者發生識別碼衝突，並不會有任何風險。

> [AZURE.NOTE]供應項目和 SKU 識別項會顯示在 Marketplace 中的供應項目 URL。

2. **為 SKU 加入摘要描述。** 人們會在 UX 中閱讀此描述，所以建議使用淺顯的內容。此資訊在「進入預備環境」之前都不需要鎖定。在那之前，您都可以任意編輯。
3. 如果您正在使用以 Windows 為基礎的 SKU，請遵循建議的連結，要求使用核准的 Windows Server 版本。

## 2\.建立與 Azure 相容的 VHD (以 Linux 為基礎)
下節的焦點是為 Microsoft Azure Marketplace 建立以 Linux 為基礎的 VM 映像所採行的最佳作法。若需逐步解說，請參考下列文件：[建立與上傳包含 Linux 作業系統的虛擬硬碟][link-azure-vm-1]

> [AZURE.TIP]已針對 Microsoft Azure 映像庫中可用的 Linux 映像執行下列許多步驟 (例如，代理程式安裝、核心開機參數)。因此，以這些映像其中之一做為基底開始，可以代表節省時間，與設定非 Azure 感知 Linux 映像做對比。

### 2\.1 選擇正確的 VHD 大小
已發行的 SKU (VM 映像) 應設計為可與所有支援 SKU 磁碟數目的 VM 大小搭配運作。您可以針對建議的大小提供指導，但是這些大小僅為建議使用，而非強迫使用。

1. Linux OS VHD：VM 映像中的 Linux OS VHD 應建立為 30GB - 50GB 的固定格式 VHD。其大小不得少於 30GB。如果實體大小小於 VHD 大小，此 VHD 應為疏鬆 VHD。大於 50GB 的 Linux VHD 應以個別案例的基礎進行考量。如果您有不同格式的 VHD，您可以使用 [Convert-VHD PowerShell Cmdlet 變更格式][link-technet-1]。
2. 資料磁碟 VHD：資料磁碟的大小可高達 1TB。資料磁碟 VHD 應建立為固定格式 VHD，但也可以是疏鬆 VHD。決定磁碟大小時，請記得使用者無法在映像中調整 VHD 大小。

### 2\.2 確認已安裝最新的 Azure Linux 代理程式
準備 OS VHD 時，請確認已安裝最新的 [Azure Linux 代理程式][link-azure-vm-2]。使用 RPM 或 Deb 封裝。封裝通常名為 walinuxagent 或 WALinuxAgent，但是請檢查您的散發以確定。此代理程式可提供在 Azure 中進行 Linux IaaS 部署的重要功能，例如 VM 佈建和網路能力。

雖然代理程式可以不同方式進行設定，建議您使用一般代理程式設定以最大化相容性。雖然可以手動安裝代理程式，如果可用，強烈建議您使用您的散發的預先設定封裝。

如果您確實是選擇從 [GitHub 存放庫][link-github-waagent]手動安裝代理程式，請先將 'waagent' 檔案複製到 /usr/sbin 並執行下列命令做為根命令：

    # chmod 755 /usr/sbin/waagent
    # /usr/sbin/waagent -install

代理程式設定檔將會放在 /etc/waagent.conf。

### 2\.3 驗證是否已包含必要的程式庫
除了 Azure Linux 代理程式之外，也要包含下列程式庫：

1. 您的核心中必須啟用 [Linux Integration Services][link-intsvc] 3.0 版或更新版本請參閱 [Linux Kernel 需求](../virtual-machines/virtual-machines-linux-create-upload-vhd-generic/#linux-kernel-requirements)
2. Azure I/O 穩定性的[核心修補程式](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/scsi/storvsc_drv.c?id=5c1b10ab7f93d24f29b5630286e323d1c5802d5c) (最新的核心可能不需要，但必須經過驗證)
3. [Python][link-python] 2.6 或更新版本
4. Python] pyasn1 封裝 (如果尚未安裝)
5. [OpenSSL][link-openssl] (建議使用 v1.0 或更高版本)

### 2\.4 設定磁碟分割
**不**建議使用邏輯磁碟區管理員。為 OS 磁碟建立單一根分割。不可在 OS 或資料磁碟上使用交換分割。建議移除交換分割，即使它沒有裝載於 /etc/fstab 中也要移除。必要時，Linux 代理程式可在本機資源磁碟 (/dev/sdb) 上建立交換分割。

### 2\.5 加入必要的核心開機指令列參數
下列參數也必須加入核心開機指令列中：

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300

這樣可以確保 Azure 支援會在必要時提供序列主控台輸出給客戶。它也可以提供適當的逾時給從雲端儲存體裝載的 OS 磁碟。即使您的 SKU 封鎖一般客戶直接 SSH 進入虛擬機器，還是必須啟用序列主控台輸出。

### 2\.6 預設包含 SSH 伺服器
強烈建議為使用者啟用 SSH。如果已啟用 SSH 伺服器，請利用下列選項將保持運作的 SSH 加入 sshd 設定：ClientAliveInterval 180。建議使用 180 時，可接受範圍為 30 到 235。並非所有應用程式都同意允許引導 SSH 至使用者的虛擬機器。如果 SSH 遭到明確的封鎖，就不需要設定 ClientAliveInterval 。

### 2\.7 符合網路需求
以下是與 Azure 相容之 Linux VM 映像的網路需求。

- 在許多情況下，最好停用 NetworkManager。其中一個例外是使用 CentOS 7.x 基礎系統 (及衍生項目) 時，其中應該保留啟用 NetworkManager。
- 網路組態應可透過 ifup/ifdown 指令碼控制。Linux 代理程式可能會使用這些命令，在佈建期間重新啟動網路。
- 不得出現自訂網路設定。應該刪除 resolv.conf 檔案做為最後一個步驟。這通常是做為取消佈建的一部分來完成 (請參閱《[Azure Linux 代理程式使用者指南](../virtual-machines/virtual-machines-linux-agent-user-guide/)》)。您也可以利用下列命令手動執行這個步驟：

        rm /etc/resolv.conf

- 網路裝置必須在開機時啟動並使用 DHCP。
- Azure 上不支援 IPv6。如果啟用這個屬性，就無法運作。

### 2\.8 確認已備妥安全性最佳作法
遵循與安全性相關的最佳作法對於 Azure Marketplace 中的 SKU 非常重要。其中包括：

- 為您的散發內容安裝所有的安全性補充程式。
- 遵循散發安全性指南。
- 避免跨佈建執行個體建立預設帳戶，這些帳戶會維持不變。
- 清除非正式記錄項目。
- 包含 iptables (防火牆) 軟體，但不啟用任何規則。如此將能夠為客戶提供順暢的預設體驗。想要使用 VM 防火牆進行其他設定的客戶可設定 iptables 規則以滿足他們的特定需求。

### 2\.9 一般化映像
Azure Marketplace 中的所有映像通常都必須能夠重複使用，因此必須移除其中的某些設定內容。如果要在 Linux 中完成此動作，必須取消 OS VHD 的佈建。

Linux 的取消佈建命令如下：

        # waagent -deprovision

這個命令會自動執行下列動作：

- 移除 /etc/resolv.conf 中的名稱伺服器設定
- 移除快取的 DHCP 用戶端租用
- 將主機名稱重設為 localhost.localdomain

建議將設定檔 (/etc/waagent.conf) 進行設定，以確認完成下列動作：

- 將設定檔中的 Provisioning.RegenerateSshHostKeyPair 設為 'y' 以移除所有的 SSH 主機金鑰。
- 將設定檔中的 Provisioning.DeleteRootPassword 設為 'y' 以從 /etc/shadow 移除根密碼。關於組態檔內容的文件，請參閱代理程式 Github 儲存機制頁面上讀我檔案的 [組態] 區段 ([https://github.com/Azure/WALinuxAgent](https://github.com/Azure/WALinuxAgent) 並且向下捲動)。  

此時您已完成 Linux VM 的一般化。從 Azure 入口網站、命令列或從 VM 中關閉 VM。關機完成時，繼續步驟 3.4。

## 3\.建立與 Azure 相容的 VHD (以 Windows 為基礎)
下節的焦點是為 Microsoft Azure Marketplace 建立以 Windows Server 為基礎的 SKU 所採行的步驟。

### 3\.1 確認您使用的是正確的基底 VHD
VM 映像的 OS VHD 必須以獲得 Microsoft Azure 核准的基底映像為基礎，包含 Windows Server 或 SQL Server。

若要開始，請從下列其中一個映像建立 VM，位於 [Microsoft Azure 入口網站][link-azure-portal]：

- Windows Server ([2012 R2 Datacenter][link-datactr-2012-r2]、[2012 Datacenter][link-datactr-2012]、[2008 R2 SP1][link-datactr-2008-r2])
- SQL Server 2014 ([Enterprise][link-sql-2014-ent]、[Standard][link-sql-2014-std]、[Web][link-sql-2014-web])
- SQL Server 2012 SP2 ([Enterprise][link-sql-2012-ent]、[Standard][link-sql-2012-std]、[Web][link-sql-2012-web])

這些連結也可以在 SKU 頁面下的發佈入口網站中找到。

> [AZURE.TIP]如果您使用的是最新的 Azure 管理入口網站或 PowerShell，2014 年 9 月 8 日之後發行的 Windows Server 映像都會獲得核准。


### 3\.2 建立您的 Windows VM
從 Microsoft Azure 入口網站，您可以利用幾個簡單的步驟，根據核准的基底映像建立您的 VM。下列內容是程序概觀。

1. 從基底映像頁面，選取 [建立 VM] 以導向新的 [Microsoft Azure 入口網站][link-azure-portal]。

    ![繪圖][img-acom-1]

2. 利用您希望使用的 Azure 訂用帳戶之 Microsoft 帳戶 (MSA) 和密碼登入入口網站。
3. 遵循提示使用您已選取的基底映像建立 VM。至少，您必須提供主機名稱 (電腦的名稱)、使用者名稱 (註冊的系統管理員使用者) 和 VM 的密碼。

    ![繪圖][img-portal-vm-create]

4. 選取要部署之 VM 的大小。

    a.如果您打算開發 VHD 內部部署，大小不會造成影響。請考慮使用其中一個較小的 VM。

    b.如果您打算在 Azure 中開發映像，請考慮使用其中一個建議的 VM 大小做為選取的映像。

    c.關於價格資訊，請參考入口網站中顯示的建議定價層選取器。它將提供三個由發行者提供的建議大小。(在這個案例中，發行者為 Microsoft。)

    ![繪圖][img-portal-vm-size]

5. 設定屬性

    a.若要快速部署，您可以保留 [選用設定] 與 [資源群組] 下的預設值。

    b.您也可以根據需求在 [儲存體帳戶] 下選取要儲存 OS VHD 的儲存體帳戶。

    c.您也可以根據需求在 [資源群組] 下選取要放置 VM 的邏輯群組。
6. 選取要部署的**位置**。

    a.如果您打算開發 VHD 內部部署，位置不會造成影響，因為您稍後會將映像上傳至 Azure。

    b.如果您打算在 Azure 中開發映像，請考慮從一開始就使用其中一個位於美國的 Microsoft Azure 區域。這樣可以在您提交映像以進行認證時，加速 Microsoft 代表您執行的 VHD 複製程序。

    ![繪圖][img-portal-vm-location]

7. 按一下 [建立]。VM 即將開始部署。在數分鐘內，您將成功完成部署，並且可以開始為您的 SKU 建立映像。

### 3\.3 在雲端開發您的 VHD
強烈建議您在雲端使用遠端桌面通訊協定 (RDP) 開發您的 VHD。您將利用在佈建期間指定的使用者名稱和密碼連接到 RDP。

> [AZURE.IMPORTANT]：如果您正在開發 VHD 內部部署 (不建議)，請參閱[建立虛擬機器映像內部部署](marketplace-publishing-vm-image-creation-on-premise.md)。**如果您在雲端中開發，就不必下載您的 VHD**。


**使用 [Microsoft Azure 入口網站][link-azure-portal]，透過 RDP 連接**

1. 選取 [瀏覽]，然後選取 [VM]。
2. VM 刀鋒視窗會隨即開啟。確認您想要連接的 VM 正在運作，並從已部署的 VM 清單中選取該 VM。
3. 描述選取之 VM 的刀鋒視窗隨即開啟。在頂端，按一下 [連接]。
4. 您將收到提示，輸入在佈建時指定的使用者名稱和密碼。

**使用 PowerShell，透過 RDP 連接**

若要將遠端桌面檔案下載至本機電腦，請使用 [Get-AzureRemoteDesktopFile Cmdlet][link-technet-2]。為了使用這個 Cmdlet，您必須知道服務名稱和 VM 的名稱。如果您已從 [Microsoft Azure 入口網站][link-azure-portal]建立 VM，您可以在 VM 屬性下找到這個資訊。

1. 在 Microsoft Azure 入口網站中，選取 [瀏覽]，然後選取 [VM]。
2. [虛擬機器] 刀鋒視窗會隨即開啟。選取您從 VM 清單部署的 VM。
3. 描述選取之 VM 的刀鋒視窗隨即開啟。
4. 按一下 [屬性]
5. 網域名稱的第一個部份是服務名稱。主機名稱是 VM 名稱。

    ![繪圖][img-portal-vm-rdp]

6. 要將已建立之 VM 的 RDP 檔案下載至系統管理員的本機桌面之 Cmdlet 如下所示：

        Get‐AzureRemoteDesktopFile ‐ServiceName “baseimagevm‐6820cq00” ‐Name “BaseImageVM” –LocalPath “C:\Users\Administrator\Desktop\BaseImageVM.rdp”

RDP 的相關詳細資訊可在 MSDN 的文章＜[透過 RDP 或 SSH 連接至 Azure VM](http://msdn.microsoft.com/library/azure/dn535788.aspx)＞中找到。

**設定 VM 並建立您的 SKU**

一旦下載了 OS VHD 之後，請使用 Hyper-V 並將 VM 設定為開始建立您的 SKU。詳細步驟可在下列 TechNet 連結中找到：[安裝 Hyper-V 和設定 VM](http://technet.microsoft.com/library/hh846766.aspx)。

### 3\.4 選擇正確的 VHD 大小
在您 VM 映像中的 Windows OS VHD 應建立為 128 GB 固定格式 VHD。

如果實體大小小於 128 GB，此 VHD 應為疏鬆 VHD。所提供的基底 Windows 和 SQL Server 映像皆符合這些需求；請勿變更所取得之 VHD 的格式或大小。

資料磁碟的大小可高達 1TB。決定磁碟大小時，請記得使用者無法於部署時在映像中調整 VHD 大小。資料磁碟 VHD 應建立為固定格式 VHD，但也可以是疏鬆 VHD。資料磁碟可以空白或包含資料。


### 3\.5 安裝最新的 Windows 補充程式
基底映像包含至其發佈日期為止的最新補充程式。在您發行您已建立的 OS VHD 之前，請確認 Windows Update 已執行，且已安裝最新的「關鍵」和「重要」安全性更新。

### 3\.6 必要時執行其他設定並排程工作
如果需要進行其他設定，請在部署 VM 後，考慮使用在啟動時執行的已排程工作，進行最終的 VM 變更。

- 讓工作在成功執行時將自己刪除是最佳作法。
- 所有設定都應該依靠 C:\\ 或 D:\\，因為它們是唯二保證隨時都存在的硬碟。C:\\ 是 OS 磁碟，而 D:\\ 是暫存本機磁碟。

### 3\.7 一般化映像
Azure Marketplace 中的所有映像通常都必須能夠重複使用。也就是說，OS VHD 必須一般化。

- 對 Windows 而言，此映像應該是 "sysprepped"，而且所有設定都應該在支援 sysprep 命令的狀態下進行。
- 您可以從目錄 %windir%\\System32\\Sysprep 執行以下命令。

        sysprep.exe /generalize /oobe /sshutdown

  下列 MSDN 文章的「步驟」提供如何 sysprep 作業系統的指導 - ＜[建立 Windows Server VHD 並將其上傳至 Azure](../virtual-machines/virtual-machines-create-upload-vhd-windows-server/)＞。

## 4\.從您的 VHD 部署 VM
一旦您的 VHD (已產生的 OS VHD 和零或更多個資料磁碟 VHD) 都以上傳至 Azure 儲存體帳戶，您就可以將它們註冊為要進行測試的使用者 VM 映像。請注意，一旦產生您的 OS VHD 之後，您就無法藉由提供 VHD URL 來直接部署 VM。

若要深入了解 VM 映像，請檢閱下列部落格文章：

- [VM 映像](https://azure.microsoft.com/blog/vm-image-blog-post/)
- [VM 映像 PowerShell 如何](https://azure.microsoft.com/blog/vm-image-powershell-how-to-blog-post/)
- [關於 Azure 中的 VM 映像](https://msdn.microsoft.com/library/azure/dn790290.aspx)

### 4\.1 建立使用者 VM 映像
若要從您的 SKU 建立使用者 VM 映像以開始部署多個 VM，您必須使用[建立 VM 映像 Rest API](http://msdn.microsoft.com/library/azure/dn775054.aspx)，將 VHD 註冊為 VM 映像。

您可以使用 Invoke-WebRequest Cmdlet 從 PowerShell 建立 VM 映像。下方的 PowerShell 指令碼可說明如何利用一個 OS 磁碟和一個資料磁碟建立 VM 映像。請注意，PowerShell 區段應該已完成設定並成為一個訂用帳戶集。

        # Image Parameters to Specify
        $ImageName=’ENTER-YOUR-OWN-IMAGE-NAME-HERE’
        $Label='ENTER-YOUR-LABEL-HERE'
        $Description='DESCRIBE YOUR IMAGE HERE’
        $osCaching='ReadWrite' $os = 'Windows'
        $state = 'Generalized'
        $osMediaLink = 'https://mystorageaccount.blob.core.windows.net/vhds/myosvhd.vhd'
        $dataCaching='None'
        $lun='1'
        $dataMediaLink='http://mystorageaccount.blob.core.windows.net/vhds/mydatavhd.vhd'
        # Subscription Related Properties
        $SrvMngtEndPoint='https://management.core.windows.net'
        $subscription = Get-AzureSubscription -Current -ExtendedDetails
        $certificate = $subscription.Certificate
        $SubId = $subscription.SubscriptionId
        $body =  
        "<VMImage xmlns=`"http://schemas.microsoft.com/windowsazure`" xmlns:i=`"http://www.w3.org/2001/XMLSchema-instance`">" + Name>" + $ImageName + "</Name>" +
        "<Label>" + $Label + "</Label>" +
        "<Description>" + $Description + "</Description>" + "<OSDiskConfiguration>" +
        "<HostCaching>" + $osCaching + "</HostCaching>" +
        "<OSState>" + $state + "</OSState>" +
        "<OS>" + $os + "</OS>" +
        "<MediaLink>" + $osMediaLink + "</MediaLink>" +
        "</OSDiskConfiguration>" +
        "<DataDiskConfigurations>" +
        "<DataDiskConfiguration>" +
        "<HostCaching>" + $dataCaching + "</HostCaching>" +
        "<Lun>" + $lun + "</Lun>" +
        "<MediaLink>" + $dataMediaLink + "</MediaLink>" +
        "</DataDiskConfiguration>" +
        "</DataDiskConfigurations>" +
        "</VMImage>"
        $uri = $SrvMngtEndPoint + "/" + $SubId + "/" + "services/vmimages" $headers = @{"x-ms-version"="2014-06-01"}
        $response = Invoke-WebRequest -Uri $uri -ContentType "application/xml" -Body
        $body -Certificate $certificate -Headers $headers -Method POST
        if ($response.StatusCode -ge 200 -and $response.StatusCode -lt 300)
        {
        echo "Accepted"
        } else {
        echo "Not Accepted" }
        $opId = $response.Headers.'x-ms-request-id'
        $uri2 = $SrvMngtEndPoint + "/" + $SubId + "/" + "operations" + "/" + $opId $response2 = Invoke-WebRequest -Uri $uri2 -ContentType "application/xml" -
        Certificate $certificate -Headers $headers -Method GET
        $response2.RawContent


藉由執行這個指令碼，您可利用您提供給 ImageName 參數的名稱 myVMImage 建立使用者 VM 映像。它包含一個 OS 磁碟和一個資料磁碟。

這個 API 是個非同步作業，並且會用已接受的 202 回應。為了查看 VM 映像是否已建立，您必須查詢作業狀態。傳回回應中的 x-ms-request-id 是作業識別碼。這個識別碼應該在下方的 $opId 中設定。

        $opId = #Fill In With Operation ID
        $uri2 = $SrvMngtEndPoint + "/" + $SubId + "/" + "operations" + "/" + "opId"
        $response2 = Invoke‐WebRequest ‐Uri $uri2 ‐ContentType "application/xml" ‐Certificate $certificate ‐Headers $headers ‐Method GET

若要利用「建立 VM 映像 API」從 OS VHD 和其他空的資料磁碟 (您沒有這個已建立磁碟的 VHD) 建立 VM 映像，請使用下列指令碼：

        # Image Parameters to Specify
        $ImageName=’myVMImage’
        $Label='IMAGE_LABEL'
        $Description='My VM Image to Test’
        $osCaching='ReadWrite'
        $os = 'Windows'
        $state = 'Generalized'
        $osMediaLink = 'http://mystorageaccount.blob.core.windows.net/containername/myOSvhd.vhd'
        $dataCaching='None'
        $lun='1'
        $emptyDiskSize= 32
        # Subscription Related Properties
        $SrvMngtEndPoint='https://management.core.windows.net'
        $subscription = Get‐AzureSubscription –Current ‐ExtendedDetails
        $certificate = $subscription.Certificate
        $SubId = $subscription.SubscriptionId
        $body =
        "<VMImage xmlns="http://schemas.microsoft.com/windowsazure" xmlns:i="http://www.w3.org/2001/XMLSchema‐instance">" +
        "<Name>" + $ImageName + "</Name>" +
        "<Label>" + $Label + "</Label>" +
        "<Description>" + $Description + "</Description>" +
        "<OSDiskConfiguration>" +
        "<HostCaching>" + $osCaching + "</HostCaching>" +
        "<OSState>" + $state + "</OSState>" +
        "<OS>" + $os + "</OS>" +
        "<MediaLink>" + $osMediaLink + "</MediaLink>" +
        "</OSDiskConfiguration>" +
        "<DataDiskConfigurations>" +
        "<DataDiskConfiguration>" +
        "<HostCaching>" + $dataCaching + "</HostCaching>" +
        "<Lun>" + $lun + "</Lun>" +
        "<MediaLink>" + $dataMediaLink + "</MediaLink>" +
        "<LogicalDiskSizeInGB>" + $emptyDiskSize + "</LogicalDiskSizeInGB>" +
        "</DataDiskConfiguration>" +
        "</DataDiskConfigurations>" +
        "</VMImage>"
        $uri = $SrvMngtEndPoint + "/" + $SubId + "/" + "services/vmimages"
        $headers = @{"x‐ms‐version"="2014‐06‐01"}
        $response = Invoke‐WebRequest ‐Uri $uri ‐ContentType "application/xml" ‐Body $body ‐Certificate $certificate ‐Headers $headers ‐Method POST
        if ($response.StatusCode ‐ge 200 ‐and $response.StatusCode ‐lt 300)
        {
        echo "Accepted"
        }
        else
        {
        echo "Not Accepted"
        }

藉由執行這個指令碼，您可利用您提供給 ImageName 參數的名稱 myVMImage 建立使用者 VM 映像。它包含一個 OS 磁碟和一個資料磁碟。

這個 API 是個非同步作業，並且會用已接受的 202 回應。為了查看 VM 映像是否已建立，您必須查詢作業狀態。傳回回應中的 x-ms-request-id 是作業識別碼。這個識別碼應該在下方的 $opId 中設定。

        $opId = #Fill In With Operation ID
        $uri2 = $SrvMngtEndPoint + "/" + $SubId + "/" + "operations" + "/" + "$opId"
        $response2 = Invoke-WebRequest -Uri $uri2 -ContentType "application/xml" Certificate $certificate -Headers $headers -Method GET

若要利用「建立 VM 映像 API」從 OS VHD 和其他空的資料磁碟 (您沒有這個已建立磁碟的 VHD) 建立 VM 映像，請使用下列指令碼：

        # Image Parameters to Specify
        $ImageName=’myVMImage’
        $Label='IMAGE_LABEL'
        $Description='My VM Image to Test’
        $osCaching='ReadWrite' $os = 'Windows'
        $state = 'Generalized'
        $osMediaLink =
        'http://mystorageaccount.blob.core.windows.net/containername/myOSvhd.vhd'
        $dataCaching='None'
        $lun='1'
        $emptyDiskSize= 32
        # Subscription Related Properties
        $SrvMngtEndPoint='https://management.core.windows.net'
        $subscription = Get-AzureSubscription –Current -ExtendedDetails
        $certificate = $subscription.Certificate
        $SubId = $subscription.SubscriptionId
        $body =  
        "<VMImage xmlns=`"http://schemas.microsoft.com/windowsazure`" xmlns:i=`"http://www.w3.org/2001/XMLSchema-instance`">" +
        "<Name>" + $ImageName + "</Name>" +
        "<Label>" + $Label + "</Label>" +
        "<Description>" + $Description + "</Description>" + "<OSDiskConfiguration>" + "<HostCaching>" + $osCaching + "</HostCaching>" +
        "<OSState>" + $state + "</OSState>" +
        "<OS>" + $os + "</OS>" +
        "<MediaLink>" + $osMediaLink + "</MediaLink>" +
        "</OSDiskConfiguration>" +
        "<DataDiskConfigurations>" +
        "<DataDiskConfiguration>" +
        "<HostCaching>" + $dataCaching + "</HostCaching>" +
        "<Lun>" + $lun + "</Lun>" +
        "<MediaLink>" + $dataMediaLink + "</MediaLink>" +
        "<LogicalDiskSizeInGB>" + $emptyDiskSize + "</LogicalDiskSizeInGB>" + "</DataDiskConfiguration>" +
        "</DataDiskConfigurations>" +
        "</VMImage>"
        $uri = $SrvMngtEndPoint + "/" + $SubId + "/" + "services/vmimages"
        $headers = @{"x-ms-version"="2014-06-01"}
        $response = Invoke-WebRequest -Uri $uri -ContentType "application/xml" -Body $body Certificate $certificate -Headers $headers -Method POST
        if ($response.StatusCode -ge 200 -and $response.StatusCode -lt 300)
        { echo "Accepted"
        } else
        { echo "Not Accepted"
        }

藉由執行這個指令碼，您可利用您提供給 ImageName 參數的名稱 myVMImage 建立使用者 VM 映像。它包含一個 OS 磁碟 (以您傳遞的 VHD 為基礎) 和一個空的 32 GB 資料磁碟。

### 4\.2 從使用者 VM 映像部署 VM
若要從使用者 VM 映像部署 VM，您可以使用最新的 [Azure 管理入口網站](https://manage.windowsazure.com)或 PowerShell。

**從最新的 Azure 管理入口網站部署 VM**

1. 移至 [新增] > [運算] > [VM] > [從資源庫]。

    ![繪圖][img-manage-vm-new]

2. 移至 [我的映像]，並選取要從其中部署 VM 的 VM 映像。
  1. 密切注意您選取的映像，因為 [我的映像] 會同時檢視 OS 映像和 VM 映像清單。
  2. 查看磁碟數目有助於判斷您正在部署的映像類型，因為大部分的 VM 映像都具有一個以上的磁碟。然而，VM 映像還是可能只具有一個 OS 磁碟，稍後會將「數個磁碟」設定為一個。

    ![繪圖][img-manage-vm-select]

3. 遵循 VM 建立精靈，指定 VM 名稱、VM 大小、位置、使用者名稱和密碼。

**從 PowerShell 部署 VM**

若要從方才建立的已產生 VM 映像部署一個大型 VM，可使用下列 Cmdlet。

    $img = Get‐AzureVMImage ‐ImageName "myVMImage"
    $user = "user123"
    $pass = "adminPassword123"
    $myVM = New‐AzureVMConfig ‐Name "VMImageVM" ‐InstanceSize "Large" ‐ImageName $img.ImageName | Add‐AzureProvisioningConfig ‐Windows ‐AdminUsername $user ‐Password $pass
    New‐AzureVM ‐ServiceName "VMImageCloudService" ‐VMs $myVM ‐Location "West US" ‐WaitForBoot

## 5\.取得 VM 映像的認證
為您的 VM 映像做好進入 Azure 市集之準備的下一個步驟是為它取得認證。

這個程序包含執行特殊的認證工具、將認證結果上傳至您的 VHD 所在的 Azure 容器、加入供應項目、定義您的 SKU、以及提交您的 VM 映像以取得認證。

### 5\.1 下載並執行 Microsoft Azure 認證工具
Microsoft Azure 認證工具的執行對象是運作中的 VM (從您的使用者 VM 映像佈建)，可確認 VM 映像與 Microsoft Azure 相容。它將驗證是否已符合為 VHD 做準備的指導和需求。工具的輸出是相容性報告，應該在要求憑證時於發佈入口網站上傳。

認證工具可適用於 Windows 和 Linux VM。它會透過 PowerShell 連接至 Windows VM，並透過 SSH.Net 連接至 Linux VM。

1. 首先，請在 [Microsoft 下載網站][link-msft-download]下載認證工具
2. 開啟認證工具並按一下 [啟動新測試] 按鈕。
3. 從 [測試資訊] 畫面，輸入執行測試的名稱。
4. 選擇您的 VM 位於 Linux 或 Windows。根據您的選擇，選取後續選項。

### **連接認證工具和 Linux VM 映像**

1. 選取 SSH 驗證模式：密碼或金鑰檔案。
2. 如果使用以密碼為基礎的驗證，請輸入 DNS 名稱、使用者名稱和密碼。
3. 如果使用金鑰檔案驗證，請輸入 DNS、使用者名稱和私密金鑰位置。

  ![Linux VM 映像的密碼驗證][img-cert-vm-pswd-lnx]

  ![Linux VM 映像的金鑰檔案驗證][img-cert-vm-key-lnx]

### **連接認證工具和 Windows VM 映像**

1. 輸入完整 VM 網域名稱系統 (DNS)，例如 MyVMName.Cloudapp.net。
2. 輸入使用者名稱和密碼。

  ![Windows VM 映像的密碼驗證][img-cert-vm-pswd-win]

一旦您選取了 Linux 或 Windows VM 映像的正確選項，請按下 [測試連接] 以確認 SSH.Net 或 PowerShell 是否具有有效的連接以供測試用途。一旦建立連接之後，請按 [下一步] 啟動測試。

測試完成時，您將收到每個測試項目的結果 (通過/失敗/警告)。

![Linux VM 映像的測試案例][img-cert-vm-test-lnx]

![Windows VM 映像的測試案例][img-cert-vm-test-win]

如果任何測試失敗，您的映像就不會獲得認證。請檢閱需求並進行任何必要的變更。

遵循自動化測試，系統將會要求您透過問卷螢幕提供您的 VM 映像的額外輸入。完成問題，然後按 [下一步]。

![認證工具問卷][img-cert-vm-questionnaire]

![認證工具問卷][img-cert-vm-questionnaire-2]

一旦您完成問卷調查，您可以提供其他資訊，例如 Linux VM 映像的 SSH 存取資訊，和失敗評估的說明。您可以下載已執行測試案例的測試結果和記錄檔，以及問卷調查的答案。將結果儲存在與您的 VHD 相同的容器中。

![儲存認證測試結果][img-cert-vm-results]

### 5\..2 取得 VM 映像的共用存取簽章統一資源識別項 (URI)

在發行期間，您將指定 URI，這些 URI 會指向您為 SKU 所建立的每個 VHD。Microsoft 需要在認證程序期間存取這些 VHD。因此，您必須建立每個 VHD 的共用存取簽章 (SAS) 統一資源識別項 (URI)。此為要在發佈入口網站的 [映像] 標籤中輸入的 URI。

建立的 SAS URI 應符合下列需求：

- 為 VHD 產生 SAS URI 時，必須有足夠的列出和唯讀權限。不提供寫入或刪除存取權。
- 存取期間應為建立 SAS URI 起至少 7 個工作天。
- 為了避免時鐘誤差所造成的即時錯誤，請指定目前時間的前 15 分鐘。

若要建立 SAS URI，您可以遵循[共用存取簽章，第 1 部分：了解 SAS 模型][link-azure-1]或[共用存取簽章，第 2 部分：透過 Blob 服務來建立與使用 SAS][link-azure-2]中提供的指示。

而不是使用程式碼產生共用的存取金鑰，您也可以使用儲存體工具，例如 [Azure 儲存體總管][link-azure-codeplex]。

**使用 Azure 儲存體總管來產生共用存取金鑰**

1. 從 CodePlex 下載 [Azure 儲存體總管][link-azure-codeplex] 6 或更新版本。
2. 一旦安裝之後，請開啟應用程式。
3. 按一下 [加入帳戶]。

    ![繪圖][img-azstg-add]

4. 指定儲存體帳戶名稱、儲存體帳戶金鑰和儲存體端點網域。**不**選取 [使用 Https]

    ![繪圖][img-azstg-setup-1]

5. 使用上述步驟，Azure 儲存體總管現在會連接到您的特定儲存體帳戶。就會開始顯示儲存體帳戶內的所有容器。選取您已複製 OS 磁碟 VHD 檔案 (如果適用於您的案例，同時也複製資料磁碟) 所在的容器。

    ![繪圖][img-azstg-setup-2]

6. 選取 Blob 容器之後，Azure 儲存體總管應用程式會開始顯示容器內的檔案。選取需要提交的影像檔案 (.vhd)。

    ![繪圖][img-azstg-setup-3]

7. 選取容器中的 (.vhd) 檔案之後，按一下下方反白顯示的 [安全性] 索引標籤。

    ![繪圖][img-azstg-setup-4]

8. 按一下 [安全性] 索引標籤，下列對話方塊隨即出現，保留 [存取層級] 索引標籤上的預設值，並且按一下 [共用存取簽章] 索引標籤

    ![繪圖][img-azstg-setup-5]

9. 在此索引標籤上依照下列步驟來產生 .vhd 映像的 SAS URL

    ![繪圖][img-azstg-setup-6]

    a.允許存取開始時間 -> 為了確保使用 UTC 時間，請從目前日期之前的日期中選取。例如，如果目前日期為 10/6/2014，請在這裡選取 10/5/2014。

    b.允許存取 -> 給予至少 7 至 8 天的時間。

    c.允許動作-> 確定同時提供列出和讀取權限

    d.如果您已正確選取 .vhd 檔案，您會在 Blob 名稱下方看到具有副檔名 .vhd 的檔案

    e.按一下 [產生簽章]。

    f.在產生的共用存取簽章 URI 中，在下列項目中檢查上方反白顯示的項目

    - i.檢查 url 不是以 https 開頭
    - ii.您的映像檔案名稱 .vhd 在 URI 中
    - iii.在簽章的結尾，確認您有 =rl (顯示讀取和列出存取權已成功提供) g。若要確認產生的 SAS URI 有效，請按一下瀏覽器中的 [測試]。它應該開始下載程序。
10. 複製 SAS URI。此為要貼入發佈入口網站的 URI。
11. 為 SKU 中的每個 VHD 重複這些步驟。

### 5\.3 在發佈入口網站中提供 VM 映像和要求認證的相關資訊。
一旦您已建立供應項目和 SKU，您應該輸入和該 SKU 相關聯的映像詳細資料。

1. 移至[發佈入口網站][link-pubportal]並以您的賣方帳戶登入。
2. 選取 [VM 映像] 索引標籤
3. 列在頁面頂端的識別碼其實是供應項目識別碼，而不是 SKU 識別碼。
4. 在 SKU 區段中填入屬性。

    ![繪圖][img-pubportal-vm-skus]

5. 在 [作業系統系列] 下，選取與 OS VHD 相關聯的作業系統類型。
6. 在 [作業系統] 下，描述作業系統。請考慮使用作業系統系列、類型、版本和更新等格式。其中一個範例為 Windows Server Datacenter 2014 R2。
7. 選取 3 個建議的虛擬機器大小。當使用者決定購買與部署您的映像，這些大小是在 Azure 管理入口網站的定價層刀鋒視窗中對其顯示的建議大小。

  >[AZURE.NOTE]這些只是建議大小。使用者可以選取任何可容納您映像中指定之磁碟的 VM 大小。

8. 輸入版本。[版本] 欄位會封裝語意版本來識別產品和它的更新。
  -	版本格式應該是 X.Y.Z，其中 X、Y 和 Z 是整數。
  -	不同 SKU 中的映像可以有不同的主要和次要版本。
  -	SKU 中的版本應該是累加變更，增加修補程式版本 (X.Y.Z 中的 Z)。
9. 在 **OS VHD URL** 底下，輸入為 OS VHD 建立的 SAS URI。
10. 如果有和這個 SKU 相關聯的資料磁碟，請選取您希望在部署時於其中裝載這個資料磁碟的邏輯單元編號 (LUN)。
11. 在 LUN X VHD URL 下，輸入為第一個資料 VHD 建立的 SAS URI。
12.	從認證工具上傳您的驗證/測試結果。
13.	按一下 [要求認證]。
14.	對其他每個資料磁碟 VHD 重複步驟 11、12 和 13。

    ![繪圖][img-pubportal-vm-skus-2]

## 下一步
一旦您提交您的虛擬機器映像 SKU 進行認證，您可以前進至 [Azure Marketplace 行銷內容指南][link-pushstaging]。在發佈程序的這個步驟中，您將在**步驟 3.在預備環境中測試您的 VM 供應項目**之前提供行銷內容、定價和其他必要資訊，您將在該步驟中測試各種使用案例，然後再將供應項目部署到 Azure Marketplace 以供公開查看和購買。

## 另請參閱
- [使用者入門：如何將供應項目發佈至 Azure Marketplace](marketplace-publishing-getting-started.md)

[img-acom-1]: media/marketplace-publishing-vm-image-creation/vm-image-acom-datacenter.png
[img-portal-vm-size]: media/marketplace-publishing-vm-image-creation/vm-image-portal-size.png
[img-portal-vm-create]: media/marketplace-publishing-vm-image-creation/vm-image-portal-create-vm.png
[img-portal-vm-location]: media/marketplace-publishing-vm-image-creation/vm-image-portal-location.png
[img-portal-vm-rdp]: media/marketplace-publishing-vm-image-creation/vm-image-portal-rdp.png
[img-azstg-add]: media/marketplace-publishing-vm-image-creation/vm-image-storage-add.png
[img-azstg-setup-1]: media/marketplace-publishing-vm-image-creation/vm-image-storage-setup.png
[img-azstg-setup-2]: media/marketplace-publishing-vm-image-creation/vm-image-storage-setup-2.png
[img-azstg-setup-3]: media/marketplace-publishing-vm-image-creation/vm-image-storage-setup-3.png
[img-azstg-setup-4]: media/marketplace-publishing-vm-image-creation/vm-image-storage-setup-4.png
[img-azstg-setup-5]: media/marketplace-publishing-vm-image-creation/vm-image-storage-setup-5.png
[img-azstg-setup-6]: media/marketplace-publishing-vm-image-creation/vm-image-storage-setup-6.png
[img-manage-vm-new]: media/marketplace-publishing-vm-image-creation/vm-image-manage-new.png
[img-manage-vm-select]: media/marketplace-publishing-vm-image-creation/vm-image-manage-select.png
[img-cert-vm-key-lnx]: media/marketplace-publishing-vm-image-creation/vm-image-certification-keyfile-linux.png
[img-cert-vm-pswd-lnx]: media/marketplace-publishing-vm-image-creation/vm-image-certification-password-linux.png
[img-cert-vm-pswd-win]: media/marketplace-publishing-vm-image-creation/vm-image-certification-password-win.png
[img-cert-vm-test-lnx]: media/marketplace-publishing-vm-image-creation/vm-image-certification-test-sample-linux.png
[img-cert-vm-test-win]: media/marketplace-publishing-vm-image-creation/vm-image-certification-test-sample-win.png
[img-cert-vm-results]: media/marketplace-publishing-vm-image-creation/vm-image-certification-results.png
[img-cert-vm-questionnaire]: media/marketplace-publishing-vm-image-creation/vm-image-certification-questionnaire.png
[img-cert-vm-questionnaire-2]: media/marketplace-publishing-vm-image-creation/vm-image-certification-questionnaire-2.png
[img-pubportal-vm-skus]: media/marketplace-publishing-vm-image-creation/vm-image-pubportal-skus.png
[img-pubportal-vm-skus-2]: media/marketplace-publishing-vm-image-creation/vm-image-pubportal-skus-2.png

[link-pushstaging]: marketplace-publishing-push-to-staging.md
[link-github-waagent]: https://github.com/Azure/WALinuxAgent
[link-azure-codeplex]: https://azurestorageexplorer.codeplex.com/
[link-azure-2]: ../storage/storage-dotnet-shared-access-signature-part-2/
[link-azure-1]: ../storage/storage-dotnet-shared-access-signature-part-1/
[link-msft-download]: http://www.microsoft.com/download/details.aspx?id=44299
[link-technet-3]: https://technet.microsoft.com/library/hh846766.aspx
[link-technet-2]: https://msdn.microsoft.com/library/dn495261.aspx
[link-azure-portal]: https://portal.azure.com
[link-pubportal]: https://publish.windowsazure.com
[link-sql-2014-ent]: http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2014enterprisewindowsserver2012r2/
[link-sql-2014-std]: http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2014standardwindowsserver2012r2/
[link-sql-2014-web]: http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2014webwindowsserver2012r2/
[link-sql-2012-ent]: http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2012sp2enterprisewindowsserver2012/
[link-sql-2012-std]: http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2012sp2standardwindowsserver2012/
[link-sql-2012-web]: http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2012sp2webwindowsserver2012/
[link-datactr-2012-r2]: http://azure.microsoft.com/marketplace/partners/microsoft/windowsserver2012r2datacenter/
[link-datactr-2012]: http://azure.microsoft.com/marketplace/partners/microsoft/windowsserver2012datacenter/
[link-datactr-2008-r2]: http://azure.microsoft.com/marketplace/partners/microsoft/windowsserver2008r2sp1/
[link-acct-creation]: marketplace-publishing-accounts-creation-registration.md
[link-azure-vm-1]: ../virtual-machines/virtual-machines-linux-create-upload-vhd/
[link-technet-1]: https://technet.microsoft.com/library/hh848454.aspx
[link-azure-vm-2]: ../virtual-machines/virtual-machines-linux-agent-user-guide/
[link-openssl]: https://www.openssl.org/
[link-intsvc]: http://www.microsoft.com/download/details.aspx?id=41554
[link-python]: https://www.python.org/

<!---HONumber=Oct15_HO3-->