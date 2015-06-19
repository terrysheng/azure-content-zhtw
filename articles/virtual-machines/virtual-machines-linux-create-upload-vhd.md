<properties 
	pageTitle="在 Azure 中建立及上傳 Linux VHD" 
	description="了解如何建立及上傳包含 Linux 作業系統的 Azure 虛擬硬碟 (VHD)。" 
	services="virtual-machines" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/13/2015" 
	ms.author="kathydav, szarkos"/>

# 建立及上傳包含 Linux 作業系統的虛擬硬碟

本文說明如何建立及上傳虛擬硬碟 (VHD)，以便用它做為您自己的映像，在 Azure 中建立虛擬機器。您將了解如何準備作業系統，以便用它根據該映像建立多台虛擬機器。  

> [AZURE.NOTE] 您不需要有任何 Azure VM 的使用經驗，也能完成本文的步驟。但您必須要有 Azure 帳戶。只需要幾分鐘的時間，您就可以建立免費試用帳戶。如需詳細資訊，請參閱[建立 Azure 帳戶](http://azure.microsoft.com/develop/php/tutorials/create-a-windows-azure-account/)。 

Azure 中的虛擬機器會執行根據您建立虛擬機器時所選擇映像的作業系統。您的映像會以 VHD 格式的 .vhd 檔案儲存在儲存體帳戶中。如需 Azure 中的磁碟和映像的詳細資訊，請參閱[管理磁碟和映像](http://msdn.microsoft.com/library/windowsazure/jj672979.aspx)。

在建立虛擬機器時，您可以自訂部分作業系統設定，使用它們適合您要執行的應用程式。如需指示，請參閱[如何建立自訂虛擬機器](/zh-tw/manage/windows/how-to-guides/custom-create-a-vm/)。

**重要事項**：只有在其中一個背書散發套件使用[本文](http://support.microsoft.com/kb/2805216)中指定的組態詳細資料時，才會對執行 Linux OS 的虛擬機器套用 Azure 平台 SLA。Azure 映像庫中提供的所有 Linux 散發套件，皆為使用必要組態的背書散發套件。


##必要條件##
本文假設您具有下列項目：

- **管理憑證** - 您已針對要上傳 VHD 的訂閱建立管理憑證，並將此憑證匯出至 .cer 檔案。如需建立憑證的詳細資訊，請參閱[建立 Azure 的管理憑證](http://msdn.microsoft.com/library/windowsazure/gg551722.aspx)。 

- **以 .vhd 檔案安裝的 Linux 作業系統**  - 您已將支援的 Linux 作業系統安裝至虛擬硬碟。有多項工具可用來建立 .vhd 檔案，例如，您可以使用虛擬化解決方案 (例如 Hyper-V) 來建立 .vhd 檔案並安裝作業系統。如需指示，請參閱[安裝 Hyper-V 角色及設定虛擬機器](http://technet.microsoft.com/library/hh846766.aspx)。 

	**重要事項**：Azure 不支援較新的 VHDX 格式。您可以使用 Hyper-V 管理員或 convert-vhd Cmdlet，將磁碟轉換為 VHD 格式。

	如需背書散發套件清單，請參閱 [Linux on Azure 背書散發套件](../linux-endorsed-distributions.md)。或者，請參閱本文最後一節中的[非背書散發套件的資訊](virtual-machines-linux-create-upload-vhd-generic.md)。

- **Linux Azure 命令列工具** - 如果您打算使用 Linux 作業系統來建立映像，則您可以使用 [適用於 Linux 和 Mac 的 Azure 命令列工具](http://go.microsoft.com/fwlink/?LinkID=253691&clcid=0x409)來上傳 VHD。

- **Azure Powershell 工具** -  `Add-AzureVhd` Cmdlet 也可用來上傳 VHD。若要下載 Azure Powershell Cmdlet，請造訪 [Azure 下載](http://azure.microsoft.com/downloads/)。如需參考資訊，請參閱 [Add-AzureVhd](http://msdn.microsoft.com/library/windowsazure/dn495173.aspx)。


此工作包含下列步驟：

- [步驟 1：準備要上傳的映像] []
- [步驟 2：在 Azure 中建立儲存體帳戶] []
- [步驟 3：準備 Azure 的連線] []
- [步驟 4：將映像上傳至 Azure] []

## <a id="prepimage"> </a>步驟 1：準備要上傳的映像 ##

Microsoft Azure 支援各種 Linux 散發套件 (請參閱[背書散發套件](../linux-endorsed-distributions.md))。下列文章會逐步引導您如何準備各種 Azure 支援的 Linux 散發套件：

- **[CentOS 型散發套件](virtual-machines-linux-create-upload-vhd-centos.md)**
- **[Oracle Linux](virtual-machines-linux-create-upload-vhd-oracle.md)**
- **[SLES 和 openSUSE](virtual-machines-linux-create-upload-vhd-suse.md)**
- **[Ubuntu](virtual-machines-linux-create-upload-vhd-ubuntu.md)**
- **[其他 - 非背書散發套件](virtual-machines-linux-create-upload-vhd-generic.md)**

另請參閱 **[Linux 安裝注意事項](../virtual-machines-linux-create-upload-vhd-generic/#linuxinstall)**，以取得準備 Azure 之 Linux 映像的其他秘訣。

在完成上述指南中的步驟後，您應會有一個已準備好上傳到 Azure 的 VHD 檔案。


## <a id="createstorage"> </a>步驟 2：在 Azure 中建立儲存體帳戶 ##

儲存體帳戶代表用以存取儲存服務、並且與您的 Azure 訂閱相關聯的最高層級命名空間。必須要有 Azure 中的儲存體帳戶，才能將可用來建立虛擬機器的 .vhd 檔案上傳至 Azure。儲存體帳戶可使用 Azure 管理入口網站來建立。

1. 登入 Azure 管理入口網站。

2. 在命令列上，按一下 **[新增]**。

	![Create storage account](./media/virtual-machines-linux-create-upload-vhd/create.png)

3. 按一下 **[儲存體帳戶]**，然後按一下 **[快速建立]**。

	![Quick create a storage account](./media/virtual-machines-linux-create-upload-vhd/storage-quick-create.png)

4. 依照下列方式填入欄位：

	![Enter storage account details](./media/virtual-machines-linux-create-upload-vhd/storage-create-account.png)

- 在 **[URL]** 下，為儲存體帳戶輸入要在 URL 中使用的子網域名稱。此項目可以包含 3 至 24 個小寫字母與數字。此名稱會成為 URL 內用來為訂閱的 Blob、「佇列」或「資料表」資源定址的主機名稱。
	
- 選擇儲存體帳戶的位置或同質群組。藉由指定同質群組，您可以將雲端服務並置在與儲存體相同的資料中心。
 
- 決定儲存體帳戶是否要使用地理區域複寫。依預設會開啟地理區域複寫。此選項可讓您免費將資料複寫至次要位置，使您在遇到無法在主要位置中處理的重大錯誤時，可將儲存體容錯移轉至次要位置。次要位置會自動指派，且無法變更。如果法律規定或組織原則要求您必須更嚴密地掌控雲端儲存體的位置，您可以關閉地理區域複寫。但請注意，如果您後續又開啟地理區域複寫，在您將現有的資料複寫至次要位置時，將會產生一次性的資料傳輸費用。不含地理區域複寫的儲存服務會有相對的折扣。

5. 按一下 **[建立儲存體帳戶]**。

	帳戶此時會列在 [**儲存體帳戶**] 下。

	![Storage account successfully created](./media/virtual-machines-linux-create-upload-vhd/Storagenewaccount.png)


## <a id="connect"> </a>步驟 3：準備 Azure 的連線 ##

您必須先在電腦與 Azure 中的訂閱之間建立安全連線，才能上傳 .vhd 檔案。 

1. 開啟 Azure PowerShell 視窗。

2. 輸入： 

	`Get-AzurePublishSettingsFile`

	此命令會開啟瀏覽器視窗，並自動下載含有您的 Azure 訂閱所需之資訊和憑證的 .publishsettings 檔案。 

3. 儲存 .publishsettings 檔案。 

4. 輸入：

	`Import-AzurePublishSettingsFile <PathToFile>`

	其中 `<PathToFile>` 是 .publishsettings 檔案的完整路徑。 

	如需詳細資訊，請參閱[開始使用 Azure Cmdlet](http://msdn.microsoft.com/library/windowsazure/jj554332.aspx) 


## <a id="upload"> </a>步驟 4：將映像上傳至 Azure ##

在上傳 .vhd 檔案時，您可以將 .vhd 檔案放在 Blob 儲存體中的任一處。在下列命令範例中，**BlobStorageURL** 是您在步驟 2 中建立之儲存體帳戶的 URL，**YourImagesFolder** 則是您要用來儲存映像之 Blob 儲存體中的容器。**VHDName** 是管理入口網站中用來識別虛擬硬碟的顯示標籤。**PathToVHDFile** 是 .vhd 檔案的完整路徑和名稱。 

執行下列其中一項：

- 從您在上一個步驟使用的 Azure PowerShell 視窗中，輸入：

		`Add-AzureVhd -Destination <BlobStorageURL>/<YourImagesFolder>/<VHDName> -LocalFilePath <PathToVHDFile>`

	如需詳細資訊，請參閱 [Add-AzureVhd](http://msdn.microsoft.com/library/windowsazure/dn205185.aspx)。

- 使用 Linux 命令列工具上傳映像。您可以使用下列命令來上傳映像：

		# azure vm image create <image-name> --location <location-of-the-data-center> --os Linux <source-path-to the vhd>



[步驟 1：準備要上傳的映像]: #prepimage
[步驟 2：在 Azure 中建立儲存體帳戶]: #createstorage
[步驟 3：準備 Azure 的連線]: #connect
[步驟 4：將映像上傳至 Azure]: #upload


<!--HONumber=45--> 
 