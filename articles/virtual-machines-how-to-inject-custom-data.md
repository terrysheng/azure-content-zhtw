<properties title="virtual-machines-how-to-inject-custom-data" pageTitle="將自訂資料插入 Azure 虛擬機器" description="本主題說明如何在建立執行個體時，將自訂資料插入 Azure 虛擬機器，以及如何在 Windows 或 Linux 上尋找自訂資料。" metaKeywords="Azure linux vm, linux vm, userdata vm, user data vm, custom data vm, windows custom data" services="virtual-machines" solutions="" documentationCenter="" authors="rasquill" manager="timlt" editor="tysonn" videoId="" scriptId="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="10/1/2014" ms.author="rasquill" />



#將自訂資料插入 Azure 虛擬機器 

不論作業系統是 Microsoft Windows 或 Linux 散發套件，將指令碼或其他資料插入正在佈建的 Azure 虛擬機器是很常見的案例。本主題將說明如何：

- 將資料插入正在佈建的 Azure 虛擬機器

- 針對 Windows 和 Linux 進行擷取，以及 

- 使用某些系統提供的特殊工具來自動偵測與處理自訂資料。

> [WACOM.NOTE] 本主題以關於這項功能的[這篇 Azure 部落格文章]為基礎，(http://azure.microsoft.com/blog/2014/04/21/custom-data-and-cloud-init-on-windows-azure/) 且將隨著更多功能的推出而及時更新。

<!--Table of contents for topic, the words in brackets must match the heading wording exactly-->
本主題內容：

+ [將自訂資料插入 Azure 虛擬機器](#injectingCustomData)

+ [在虛擬機器中使用自訂資料](#usingCustomData)

+ [後續步驟](#nextsteps)


## <a id="injectingCustomData"></a>將自訂資料插入 Azure 虛擬機器

此功能目前僅支援 [Microsoft Azure 跨平台命令列介面](https://github.com/Azure/azure-sdk-tools-xplat)。雖然您可能會在 `azure vm create` 命令中使用任何選項，下列將說明一個非常基本的方法。 

```
    PASSWORD='AcceptablePassword -- more than 8 chars, a cap, a num, a special'
    VMNAME=mycustomdataubuntu
    USERNAME=username
    VMIMAGE= An image chosen from among those listed by azure vm image list
    azure vm create $VMNAME $VMIMAGE $USERNAME $PASSWORD --location "West US" --json -d ./custom-data.txt -e 22
```


## <a id="usingCustomData"></a>在虛擬機器中使用自訂資料
 
+ 如果您的 Azure 虛擬機器是 Windows 虛擬機器，則自訂資料檔案會被儲存到 `%SYSTEMDRIVE%\AzureData\CustomData.bin`，而且雖然從本機電腦傳送到新虛擬機器的資料是 base64 編碼，系統會自動將它解碼並立即開啟使用。 

   > [WACOM.NOTE] 如果檔案已存在，則會被覆寫。目錄上的安全性會設為 [**System:Full Control**] 和 [**Administrators:Full Control**]。

+ 如果您的 Azure 虛擬機器是 Linux 虛擬機器，則自訂資料檔案會位於下列兩個地方，但因為資料會是 base64 編碼，您必須先將資料解碼。

    + 在 `/var/lib/waagent/ovf-env.xml` 上
    + 在 `/var/lib/waagent/CustomData` 上 

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a id="nextsteps"></a>後續步驟：使用 cloud-init

如果您的 Azure 虛擬機器是 Ubuntu 映像，則您可以使用 cloud-init 來啟動指令碼以自動使用自訂資料 (或者，如果您的自訂資料檔案便是指令碼，則執行此檔案)。如需進一步資訊，請參閱 [Ubuntu 的 cloud-init 文件](https://help.ubuntu.com/community/CloudInit)。

<!--Link references-->
[加入角色服務管理 REST API 參考](http://msdn.microsoft.com/library/azure/jj157186.aspx)

[Microsoft Azure 跨平台命令列介面](https://github.com/Azure/azure-sdk-tools-xplat)


<!--HONumber=35_1-->
