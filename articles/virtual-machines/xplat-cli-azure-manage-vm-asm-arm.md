<properties
	pageTitle="適用於 VM 工作的對等 Azure CLI 命令 |Microsoft Azure"
	description="對等的 Azure CLI 命令，在 Azure 資源管理員和 Azure 服務管理模式中建立和管理 Azure VM"
	services="virtual-machines"
	documentationCenter=""
	authors="dlepow"
	manager="timlt"
	editor=""
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="command-line-interface"
	ms.workload="infrastructure-services"
	ms.date="08/28/2015"
	ms.author="danlep"/>


# 在使用適用於 Mac、Linux 及 Windows 的 Azure CLI 的 VM 工作中的對等資源管理員和服務管理命令
本文將說明在 Azure 服務管理和 Azure 資源管理員中建立和管理 Azure VM 的對等 Microsoft Azure 命令列介面 (Azure CLI) 命令。將本文當成便利指南，來將指令碼從某一個命令模式移轉至另一個命令模式。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]



* 如果您尚未安裝 Azure CLI 及連線至您的訂用帳戶，請參閱[安裝 Azure CLI](../xplat-cli-install.md) 和[從 Azure CLI 連線至 Azure 訂用帳戶](../xplat-cli-connect.md)。當您想要使用資源管理員模式命令時，請務必使用登入方法連線。

* 若要開始在 Azure CLI 中使用資源管理員模式以及切換命令模式，請參閱[搭配使用 Azure 命令列介面與資源管理員](xplat-cli-azure-resource-manager.md)。根據預設，CLI 會在服務管理模式下啟動。若要變更為資源管理員模式，請執行 `azure config mode arm`。若要回到服務管理模式，請執行 `azure config mode asm`。

* 如需線上命令說明和選項，請輸入 `azure <command> <subcommand> --help` 或 `azure help <command> <subcommand>`。

## VM 工作
下表比較常見的 VM 工作，您可以在服務管理和資源管理員中使用 Azure CLI 命令來執行這類工作。使用許多資源管理員命令時，您需要傳遞現有的資源群組名稱。

> [AZURE.NOTE]這些範例不包括資源管理員中以範本為基礎的操作。如需詳細資訊，請參閱[搭配使用 Azure 命令列介面與資源管理員](xplat-cli-azure-resource-manager.md)。

工作 | 服務管理 | 資源管理員
-------------- | ----------- | -------------------------
建立最基本的 VM | `azure vm create [options] <dns-name> <image> [userName] [password]` | `azure vm quick-create [options] <resource-group> <name> <location> <os-type> <image-urn> <admin-username> <admin-password>`<br/><br/>(從 `azure vm image list` 命令取得 `image-urn`。)
建立 Linux VM | `azure vm create [options] <dns-name> <image> [userName] [password]` | `azure  vm create [options] <resource-group> <name> <location> -y "Linux"`
建立 Windows VM | `azure vm create [options] <dns-name> <image> [userName] [password]` | `azure  vm create [options] <resource-group> <name> <location> -y "Windows"`
列出 VM | `azure  vm list [options]` | `azure  vm list [options] <resource_group>`
取得 VM 的相關資訊 | `azure  vm show [options] <vm_name>` | `azure  vm show [options] <resource_group> <name>`
啟動 VM | `azure vm start [options] <name>` | `azure vm start [options] <resource_group> <name>`
停止 VM | `azure vm shutdown [options] <name>` | `azure vm stop [options] <resource_group> <name>`
解除配置 VM | 尚未提供 | `azure vm deallocate [options] <resource-group> <name>`
重新啟動 VM | `azure vm restart [options] <vname>` | `azure vm restart [options] <resource_group> <name>`
刪除 VM | `azure vm delete [options] <name>` | `azure vm delete [options] <resource_group> <name>`
擷取 VM | `azure vm capture [options] <name>` | `azure vm capture [options] <resource_group> <name>`
從使用者映像建立 VM | `azure  vm create [options] <dns-name> <image> [userName] [password]` | `azure  vm create [options] –q <image-name> <resource-group> <name> <location> <os-type>`
從特殊化磁碟建立 VM | `azure  vm create [options]-d <custom-data-file> <dns-name> [userName] [password]` | `azue  vm create [options] –d <os-disk-vhd> <resource-group> <name> <location> <os-type>`
將資料磁碟新增至 VM | `azure  vm disk attach [options] <vm-name> <disk-image-name>` -或- <br/> `vm disk attach-new [options] <vm-name> <size-in-gb> [blob-url]` | `azure  vm disk attach-new [options] <resource-group> <vm-name> <size-in-gb> [vhd-name]`
從 VM 移除資料磁碟 | `azure  vm disk detach [options] <vm-name> <lun>` | `azure  vm disk detach [options] <resource-group> <vm-name> <lun>`
將泛型延伸模組新增至 VM | `azure  vm extension set [options] <vm-name> <extension-name> <publisher-name> <version>` | `azure  vm extension set [options] <resource-group> <vm-name> <name> <publisher-name> <version>`
將 VM 存取延伸模組新增至 VM | 尚未提供 | `azure vm reset-access [options] <resource-group> <name>`
將 Docker 延伸模組新增至 VM | `azure  vm docker create [options] <dns-name> <image> <user-name> [password]` | `azure  vm docker create [options] <resource-group> <name> <location> <os-type>`
將 Chef 延伸模組新增至 VM | `azure  vm extension get-chef [options] <vm-name>` | 尚未提供
停用 VM 延伸模組 | `azure  vm extension set [options] –b <vm-name> <extension-name> <publisher-name> <version>` | 尚未提供
移除 VM 延伸模組 | `azure  vm extension set [options] –u <vm-name> <extension-name> <publisher-name> <version>` | `azure  vm extension set [options] –u <resource-group> <vm-name> <name> <publisher-name> <version>`
列出 VM 延伸模組 | `azure vm extension list [options]` | `azure  vm extension get [options] <resource-group> <vm-name>`
列出 VM 映像 | `azure vm image list [options]` | `azure vm image list [options] <location> <publisher> [offer] [sku]` -或- <br/> `azure vm image list-publishers [options] <location>` -或- <br/> `azure vm image list-offers [options] <location>` -或- <br/> `azure vm image list-skus [options] <location>`
顯示 VM 映像 | `azure vm image show [options]` | 尚未提供
取得 VM 資源的使用量 | 尚未提供 | `azure vm list-usage [options] <location>`
取得所有可用的 VM 大小 | 尚未提供 | `azure vm sizes [options]`


## 後續步驟

* 如需如何以 Azure CLI 來使用資源管理員資源的詳細資訊，請參閱[搭配使用 Azure 命令列介面與資源管理員](xplat-cli-azure-resource-manager.md)和[使用 Azure 命令列介面管理角色型存取控制](../role-based-access-control-xplat-cli-install.md)。
* 如需 CLI 命令的其他範例，請參閱[搭配使用 Azure 命令列介面與 Azure 服務管理](../virtual-machines-command-line-tools.md)和[搭配使用 Azure CLI 與 Azure 資源管理員](azure-cli-arm-commands.md)。

<!---HONumber=Oct15_HO3-->