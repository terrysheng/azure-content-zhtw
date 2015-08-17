<properties
	pageTitle="將自訂資料插入 Azure 虛擬機器"
	description="本主題說明如何在建立執行個體時，將自訂資料插入 Azure 虛擬機器，以及如何在 Windows 或 Linux 上尋找自訂資料。"
	services="virtual-machines"
	documentationCenter=""
	authors="squillace"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management" />

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/14/2015"
	ms.author="rasquill"/>


#將自訂資料插入 Azure 虛擬機器

不論作業系統是 Windows 或 Linux 散發套件，將指令碼或其他資料插入正在佈建的 Azure 虛擬機器是很常見的案例。本主題將說明如何：

- 將資料插入正在佈建的 Azure 虛擬機器

- 針對 Windows 和 Linux 進行擷取。

- 使用某些系統提供的特殊工具來自動偵測與處理自訂資料。

> [AZURE.NOTE]本文將說明如何使用建立的 VM 插入自訂資料以搭配 Azure 服務管理 API。若要了解如何使用 Azure 資源管理 API，請參閱[範例範本](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-customdata)。

## 將自訂資料插入 Azure 虛擬機器

目前僅在 [Microsoft Azure 命令列介面](https://github.com/Azure/azure-xplat-cli)中支援此功能。雖然您可能會在 `azure vm create` 命令中使用任何選項，但是下列將示範一個非常基本的方法。

```
    PASSWORD='AcceptablePassword -- more than 8 chars, a cap, a num, a special'
    VMNAME=mycustomdataubuntu
    USERNAME=username
    VMIMAGE= An image chosen from among those listed by azure vm image list
    azure vm create $VMNAME $VMIMAGE $USERNAME $PASSWORD --location "West US" --json -d ./custom-data.txt -e 22
```


## 在虛擬機器中使用自訂資料

+ 如果 Azure 的虛擬機器是以 Windows 為基礎的虛擬機器，自訂資料檔案則會儲存至 `%SYSTEMDRIVE%\AzureData\CustomData.bin`。雖然從本機電腦傳送到新虛擬機器的資料是 base64 編碼，但是系統會自動將它解碼並立即開啟或使用。

   >[AZURE.NOTE]如果檔案已存在，則會被覆寫。目錄上的安全性會設為 [**System:Full Control**] 和 [**Administrators:Full Control**]。

+ 如果您的 Azure 虛擬機器是以 Linux 為基礎的虛擬機器，自訂資料檔案則會位於下列兩個位置中。資料將會以 base64 編碼，因此您必須先解碼資料。

    + 於 `/var/lib/waagent/ovf-env.xml`
    + 於 `/var/lib/waagent/CustomData`



## Azure 上的 Cloud-Init

如果您的 Azure 虛擬機器是來自 Ubuntu 或 CoreOS 映像，則您可以使用 CustomData 將 cloud-config 傳送到 cloud-init。或者，如果您的自訂資料檔案是指令碼，則 cloud-init 只能執行它。

### Ubuntu 雲端映像

在大部分的 Azure Linux 映像中．您可以編輯 "/etc/waagent.conf" ，以設定暫存資源磁碟和交換檔。如需詳細資訊，請參閱 [Azure Linux 代理程式使用者指南](virtual-machines-linux-agent-user-guide.md)。

不過，在 Ubuntu 雲端映像上，您必須使用 cloud-init 設定資源磁碟 (也就是「暫時」磁碟) 和交換資料分割。如需詳細資訊，請參閱 Ubuntu wiki 上的下列網頁：[AzureSwapPartitions](https://wiki.ubuntu.com/AzureSwapPartitions)。



<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 後續步驟：使用 cloud-init

如需進一步資訊，請參閱 [Ubuntu 的 cloud-init 文件](https://help.ubuntu.com/community/CloudInit) (英文)。

<!--Link references-->
[加入角色服務管理 REST API 參考](http://msdn.microsoft.com/library/azure/jj157186.aspx)

[Azure 命令列介面](https://github.com/Azure/azure-sdk-tools-xplat)

<!---HONumber=August15_HO6-->