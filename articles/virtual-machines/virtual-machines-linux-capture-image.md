<properties
	pageTitle="對執行 Linux 的虛擬機器擷取映像"
	description="了解如何對執行 Linux 的 Azure 虛擬機器 (VM) 擷取映像。"
	services="virtual-machines"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/16/2015"
	ms.author="dkshir"/>


# 如何擷取 Linux 虛擬機器作為範本使用

本文說明如何擷取執行 Linux 的 Azure 虛擬機器，以便用它做為範本建立其他虛擬機器。此範本包括 OS 磁碟和連結虛擬機器的資料磁碟。它不包含網路組態，因此您將需要在建立使用該範本的其他虛擬機器時加以設定。

Azure 會將此範本視為映像，並將其儲存在 [**映像**] 下。這也是您已上傳的任何映像儲存所在之處。如需有關映像的詳細資訊，請參閱[關於 Azure 中的虛擬機器映像][]。

## 開始之前

這些步驟假設您已建立 Azure 虛擬機器且設定好作業系統，包括連接任何資料磁碟。如果您還沒這麼做，請參閱下列指示：

- [如何建立執行 Linux 的虛擬機器][]


## 擷取虛擬機器

1. 連線到使用您所選擇 SSH 用戶端的虛擬機器。如需詳細資訊，請參閱[如何登入執行 Linux 的虛擬機器][]。

2. 在 SSH 視窗中，輸入下列命令。請注意，不同版本的 `waagent` 公用程式可能會有稍微不同的輸出：

	`sudo waagent -deprovision`

	此命令會嘗試清除系統，使之適合重新佈建。這項作業會執行下列工作：

	- 移除 SSH 主機金鑰 (如果組態檔中的 Provisioning.RegenerateSshHostKeyPair 是 'y')
	- 清除 /etc/resolv.conf 中的名稱伺服器設定
	- 移除 /etc/shadow 中的 `root` 使用者密碼 (如果組態檔中的 Provisioning.DeleteRootPassword 是 'y')
	- 移除快取的 DHCP 用戶端租用
	- 將主機名稱重設為 localhost.localdomain
	- 刪除最後佈建的使用者帳戶 (取自於 /var/lib/waagent) **和相關聯的資料**。

	>[AZURE.NOTE]解除佈建會刪除檔案與資料，以將映象「一般化」。請只在您意圖擷取做為新的映像範本的虛擬機器上執行這個命令。這不能保證映像檔中的所有機密資訊都會清除完畢或適合轉散發給第三方。


3. 輸入 **y** 繼續。您可以加入 `-force` 參數以避免此確認步驟。

4. 輸入 **Exit** 關閉 SSH 用戶端。


	>[AZURE.NOTE]接下來的步驟假設您已經在用戶端電腦上[安裝 Azure CLI](../xplat-cli-install.md)。您也可以在[管理入口網站][]完成下方所有步驟。

5. 從用戶端電腦，開啟 Azure CLI 並登入您的 Azure 訂用帳戶。如需詳細資料，請閱讀＜[從 Azure CLI 連接到 Azure 訂用帳戶](../xplat-cli-connect.md)＞。

6. 請確定您是處於服務管理模式中：

	`azure config mode asm`

7. 使用下列程式碼，將已在上述步驟中取消佈建的虛擬機器關機：

	`azure vm shutdown <your-virtual-machine-name>`

	>[AZURE.NOTE]您可以使用 `azure vm list`，找出訂用帳戶中建立的所有虛擬機器

8. 當虛擬機器停止時，使用下列命令來擷取映像：

	`azure vm capture -t <your-virtual-machine-name> <new-image-name>`

	輸入您想要的映像名稱以取代 _new-image-name_。此命令會建立通用的 OS 映像。`-t` 子命令會刪除原始的虛擬機器。

9.	新的映像現在可從映像清單中取得，且可用來設定任何新的虛擬機器。您可以使用下列命令進行檢視：

	`azure vm image list`

	在[管理入口網站][]上，它會出現在 [**映像**] 清單。

	![Image capture successful](./media/virtual-machines-linux-capture-image/VMCapturedImageAvailable.png)


## 後續步驟
映像已可用來做為範本，建立虛擬機器。您可以使用 Azure CLI 命令 `azure vm create` 並提供您剛才建立的映像名稱。如需命令的詳細資料，請參閱[使用 Azure CLI 搭配服務管理](virtual-machines-command-line-tools.md)。或者，您可以利用 [**從資源庫**] 方法，並選取您剛才建立的映像，以便使用[管理入口網站][]來建立自訂虛擬機器。如需詳細資料，請參閱[如何建立自訂虛擬機器][]。

**另請參閱：** [Azure Linux 代理程式使用者指南](virtual-machines-linux-agent-user-guide.md)

[管理入口網站]: http://manage.windowsazure.com
[如何登入執行 Linux 的虛擬機器]: virtual-machines-linux-how-to-log-on.md
[關於 Azure 中的虛擬機器映像]: http://msdn.microsoft.com/library/azure/dn790290.aspx
[如何建立自訂虛擬機器]: virtual-machines-create-custom.md
[How to Attach a Data Disk to a Virtual Machine]: storage-windows-attach-disk.md
[如何建立執行 Linux 的虛擬機器]: virtual-machines-linux-tutorial.md

<!---HONumber=August15_HO6-->