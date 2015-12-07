<properties
	pageTitle="在 Azure 入口網站中建立執行 Linux 的 Azure 虛擬機器 | Microsoft Azure"
	description="使用 Azure 入口網站建立執行 Linux 的 Azure 虛擬機器 (VM) 搭配 Azure 資源群組。"
	services="virtual-machines"
	documentationCenter=""
	authors="squillace"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/21/2015"
	ms.author="rasquill"/>

# 使用 Azure Preview 入口網站建立執行 Linux 的虛擬機器

> [AZURE.SELECTOR]
- [Azure CLI](virtual-machines-linux-tutorial.md)
- [Azure preview portal](virtual-machines-linux-tutorial-portal-rm.md)

<br>[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]傳統部署模型。

建立執行 Linux 的 Azure 虛擬機器 (VM) 很簡單。本教學課程示範如何使用 Azure Preview 入口網站來快速建立一個虛擬機器，並使用`~/.ssh/id_rsa.pub`公開金鑰檔來確保您可以安全地將 **SSH** 連接至 VM。您也可以使用[自己的映像作為範本](virtual-machines-linux-create-upload-vhd.md)來建立 Linux VM。

> [AZURE.NOTE]本教學課程會建立受 Azure 資源群組 API 管理的 Azure 虛擬機器。如需詳細資訊，請參閱《[Azure 資源群組概觀](resource-group-overview.md)》。

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

## 選取映像

移至 Preview 入口網站中的 Azure Marketplace，以尋找您想要的 Windows Server VM 映像。

1. 登入 [Preview 入口網站](https://portal.azure.com)。

2. 在 [中樞] 功能表上，按一下 [**新增**] > [**計算**] > [**Ubuntu Server 14.04 LTS**]。

	![選擇 VM 映像](media/virtual-machines-linux-tutorial-portal-rm/chooseubuntuvm.png)

	> [AZURE.TIP]若要尋找其他映像，請按一下 [Marketplace]，然後搜尋或篩選可用的項目。

3. 在 [Ubuntu Server 14.04 LTS] 頁面底部，選取 [使用資源管理員堆疊] 在 [Azure 資源管理員] 中建立 VM。請注意，對於多數新的工作負載，我們建議使用 [資源管理員] 堆疊。如需考量事項，請參閱《[Azure 資源管理員下的 Azure 計算、網路和儲存體提供者](virtual-machines-azurerm-versus-azuresm.md)》。

4. 接下來，按一下 ![建立按鈕](media/virtual-machines-linux-tutorial-portal-rm/createbutton.png)。

	![變更資源管理員計算堆疊](media/virtual-machines-linux-tutorial-portal-rm/changetoresourcestack.png)

## 建立虛擬機器

選取映像之後，您可以針對多數組態使用 Azure 的預設設定，並能快速建立 VM。

1. 在 [建立虛擬機器] 刀鋒視窗中，按一下 [基本概念]。輸入您想要的 VM [名稱]，以及公開金鑰檔案 (為 **ssh rsa** 格式，在此情況下來自於 `~/.ssh/id_rsa.pub` 檔案)。如果您有一個以上的訂用帳戶，請針對新的 VM，以及新的或現有的**資源群組**和 Azure 資料中心**位置**指定一個訂用帳戶。

	![](media/virtual-machines-linux-tutorial-portal-rm/step-1-thebasics.png)

	> [AZURE.NOTE]如果您不想要使用公用和私人金鑰交換來保護您的 **ssh** 工作階段，您也可在此處選擇使用者名稱/密碼驗證並輸入該資訊。

2. 按一下 [大小]，然後選取適當的 VM 大小以符合您的需求。每個大小會指定計算核心、記憶體和其他功能的數目，例如支援將會影響價格的進階儲存體。Azure 會自動根據您選擇的映像來建議特定大小。完成時，按一下 ![選取按鈕](media/virtual-machines-linux-tutorial-portal-rm/selectbutton-size.png)。

	>[AZURE.NOTE]進階儲存體可供某些區域的 DS 系列虛擬機器使用。進階儲存體對於如資料庫這類資料密集的工作負載是最佳的儲存體選項。如需詳細資訊，請參閱[高階儲存體：Azure 虛擬機器工作負載適用的高效能儲存體](storage-premium-storage-preview-portal.md)。

3. 按一下 [設定] 以查看新 VM 的儲存體和網路設定。對於第一個 VM，通常您可以接受預設的設定。如果您選取支援的 VM 大小，您可以藉由選取 [磁碟類型] 下的 [進階 (SSD)] 嘗試進階儲存體。完成時，按一下 ![[確定] 按鈕](media/virtual-machines-linux-tutorial-portal-rm/okbutton.png)。

	![](media/virtual-machines-linux-tutorial-portal-rm/step-3-settings.png)

6. 按一下 [摘要] 以檢閱您的組態選擇。當您完成檢閱或更新設定時，請按一下 ![[確定] 按鈕](media/virtual-machines-linux-tutorial-portal-rm/createbutton.png)。

	![建立摘要](media/virtual-machines-linux-tutorial-portal-rm/summarybeforecreation.png)

8. 當 Azure 建立 VM 時，您可以在 [中樞] 功能表的 [通知] 中追蹤進度。Azure 建立 VM 之後，除非您在 [建立虛擬機器] 刀鋒視窗中清除 [釘選到「開始面板」]，否則您將會在「開始面板」上看到 VM。

	> [AZURE.NOTE]請注意，摘要不包含使用服務管理計算堆疊在雲端服務內建立 VM 時的公開 DNS 名稱。

## 使用 **ssh** 連線到 Azure Linux VM

現在您可以透過標準方式使用 **ssh** 連線到 Ubuntu VM。不過，你需要針對 VM 及其資源開啟磚，以便探索配置給 Azure VM 的 IP 位址。您可以按一下 [瀏覽]，然後選取 [最近] 並尋找建立的 VM，或按一下「開始面板」上為您建立的磚。在任一情況下，找出並複製 [公用 IP 位址] 值，如下圖所顯示。

![成功建立的摘要](media/virtual-machines-linux-tutorial-portal-rm/successresultwithip.png)

現在您可以使用 **ssh** 連線到 Azure VM，而您已準備好繼續。

	ssh ops@23.96.106.1 -p 22
	The authenticity of host '23.96.106.1 (23.96.106.1)' can't be established.
	ECDSA key fingerprint is bc:ee:50:2b:ca:67:b0:1a:24:2f:8a:cb:42:00:42:55.
	Are you sure you want to continue connecting (yes/no)? yes
	Warning: Permanently added '23.96.106.1' (ECDSA) to the list of known hosts.
	Welcome to Ubuntu 14.04.2 LTS (GNU/Linux 3.16.0-43-generic x86_64)

	 * Documentation:  https://help.ubuntu.com/

	  System information as of Mon Jul 13 21:36:28 UTC 2015

	  System load: 0.31              Memory usage: 5%   Processes:       208
	  Usage of /:  42.1% of 1.94GB   Swap usage:   0%   Users logged in: 0

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

	ops@ubuntuvm:~$


> [AZURE.NOTE]您也可以在入口網站中設定虛擬機器的完整網域名稱 (FQDN)。在[這裡](virtual-machines-create-fqdn-on-portal.md)進一步了解 FQDN。

## 後續步驟

若要深入了解 Azure 上的 Linux，請參閱：

- [Azure 上的 Linux 和開放原始碼運算](virtual-machines-linux-opensource.md)

- [如何使用適用於 Mac 和 Linux 的 Azure 命令列工具](virtual-machines-command-line-tools.md)

- [使用適用於 Linux 的 Azure CustomScript 延伸模組部署 LAMP 應用程式](virtual-machines-linux-script-lamp.md)

- [Azure 上 Linux 的 Docker 虛擬機器擴充程式](virtual-machines-docker-vm-extension.md)

<!---HONumber=AcomDC_1125_2015-->