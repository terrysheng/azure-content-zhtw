<properties
	pageTitle="如何搭配 Linux 使用 Azure 檔案 | Microsoft Azure"
        description="使用此逐步解說教學課程，在雲端中建立 Azure 檔案共用。管理您的檔案共用內容，並從執行 Linux 的 Azure 虛擬機器 (VM) 或支援 SMB 3.0 的內部部署應用程式掛接檔案共用。"
        services="storage"
        documentationCenter="na"
        authors="mine-msft"
        manager="aungoo"
        editor="tysonn" />

<tags ms.service="storage"
      ms.workload="storage"
      ms.tgt_pltfrm="na"
      ms.devlang="na"
      ms.topic="article"
      ms.date="02/29/2016"
      ms.author="minet" />


# 如何搭配使用 Azure 檔案儲存體與 Linux

## 概觀

Azure 檔案儲存體可在雲端中使用標準的 SMB 通訊協定提供檔案共用。使用 Azure 檔案，您可以將依賴檔案伺服器的企業應用程式移轉至 Azure。在 Azure 中執行的應用程式可以從執行 Linux 的 Azure 虛擬機器輕鬆地掛接檔案共用。有了最新版本的檔案儲存體後，您也可以從支援 SMB 3.0 的內部部署應用程式掛接檔案共用。

您可以使用 [Azure 入口網站](https://portal.azure.com)、Azure 儲存體 PowerShell Cmdlet、Azure 儲存體用戶端程式庫或 Azure 儲存體 REST API 來建立 Azure 檔案共用。此外，由於檔案共用為 SMB 共用，因此您可以透過標準檔案系統 API 存取它們。

檔案儲存體是使用與 Blob、資料表和佇列儲存體相同的技術建置，因此檔案儲存體能夠提供可用性、持續性、延展性和建置於 Azure 儲存體平台內的異地備援。如需有關檔案儲存體效能目標和限制的詳細資訊，請參閱 [Azure 儲存體延展性和效能目標](storage-scalability-targets.md)。

檔案儲存體現已公開推出，並同時支援 SMB 2.1 和 SMB 3.0。如需有關檔案儲存體的詳細資訊，請參閱[檔案服務 REST API](https://msdn.microsoft.com/library/azure/dn167006.aspx)。

>[AZURE.NOTE] Linux SMB 用戶端尚未支援加密，因此若要從 Linux 掛接檔案共用，用戶端仍必須與檔案共用位於相同的 Azure 區域。不過，適用於 Linux 的加密支援已列入開發藍圖中，負責 SMB 功能的 Linux 開發人員將著手開發。未來，支援加密功能的 Linux 散發套件也能從任何位置掛接 Azure 檔案共用。

## 影片：搭配 Linux 使用 Azure 檔案儲存體

以下影片示範如何在 Linux 上建立和使用 Azure 檔案共用。

> [AZURE.VIDEO azure-file-storage-with-linux]

## 選擇要使用的 Linux 散發套件 ##

在 Azure 中建立 Linux 虛擬機器時，您可以從 Azure 映像庫指定支援 SMB 2.1 或更高版本的 Linux 映像。以下是建議的 Linux 映像清單：

- Ubuntu Server 14.04
- Ubuntu Server 15.04
- CentOS 7.1
- Open SUSE 13.2
- SUSE Linux Enterprise Server 12
- SUSE Linux Enterprise Server 12 (Premium 映像)

## 掛接檔案共用 ##

若要從執行 Linux 的虛擬機器掛接檔案共用，如果您使用的散發套件沒有內建用戶端的話，您可能需要安裝 SMB/CIFS 用戶端。這是 Ubuntu 的命令，可用來安裝一個選擇的 cifs-utils：

    sudo apt-get install cifs-utils

接下來，您必須建立掛接點 (mkdir mymountpoint)，然後發出如下所示的掛接命令：

     sudo mount -t cifs //myaccountname.file.core.windows.net/mysharename ./mymountpoint -o vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777

您也可以在 /etc/fstab 中加入設定，以便掛接共用。

請注意，這裡的 0777 代表目錄/檔案權限程式碼，可賦與所有使用者執行/讀取/寫入權限。您可以遵循 Linux 檔案權限文件的指示，以其他檔案權限程式碼加以取代。

此外，若要在重新開機後繼續掛接檔案共用，您可以在 /etc/fstab 中加入類似下方的設定：

    //myaccountname.file.core.windows.net/mysharename /mymountpoint cifs vers=3.0,username= myaccountname,password= StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777

例如，如果您使用 Linux 映像 Ubuntu Server 15.04 (可從 Azure 映像庫取得) 建立 Azure VM，則可以如下所示掛接檔案：

    azureuser@azureconubuntu:~$ sudo apt-get install cifs-utils
    azureuser@azureconubuntu:~$ sudo mkdir /mnt/mountpoint
    azureuser@azureconubuntu:~$ sudo mount -t cifs //myaccountname.file.core.windows.net/mysharename /mnt/mountpoint -o vers=3.0,user=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
    azureuser@azureconubuntu:~$ df -h /mnt/mountpoint
    Filesystem  Size  Used Avail Use% Mounted on
    //myaccountname.file.core.windows.net/mysharename  5.0T   64K  5.0T   1% /mnt/mountpoint

如果您使用 CentOS 7.1，可以如下所示掛接檔案：

    [azureuser@AzureconCent ~]$ sudo yum install samba-client samba-common cifs-utils
    [azureuser@AzureconCent ~]$ sudo mount -t cifs //myaccountname.file.core.windows.net/mysharename /mnt/mountpoint -o vers=3.0,user=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
    [azureuser@AzureconCent ~]$ df -h /mnt/mountpoint
    Filesystem  Size  Used Avail Use% Mounted on
    //myaccountname.file.core.windows.net/mysharename  5.0T   64K  5.0T   1% /mnt/mountpoint

如果您使用 Open SUSE 13.2，可以如下所示掛接檔案：

    azureuser@AzureconSuse:~> sudo zypper install samba*  
    azureuser@AzureconSuse:~> sudo mkdir /mnt/mountpoint
    azureuser@AzureconSuse:~> sudo mount -t cifs //myaccountname.file.core.windows.net/mysharename /mnt/mountpoint -o vers=3.0,user=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
    azureuser@AzureconSuse:~> df -h /mnt/mountpoint
    Filesystem  Size  Used Avail Use% Mounted on
    //myaccountname.file.core.windows.net/mysharename  5.0T   64K  5.0T   1% /mnt/mountpoint

## 管理檔案共用 ##

[Azure 入口網站](https://portal.azure.com)提供可管理 Azure 檔案儲存體的使用者介面。您可以從網路瀏覽器執行下列動作：

- 上傳檔案至檔案共用以及從檔案共用下載檔案。
- 監視每個檔案共用的實際使用狀況。
- 調整檔案室共用大小配額。
- 複製並使用 `net use` 命令以從 Windows 用戶端掛接檔案共用。

您也可以使用 Linux 的 Azure 跨平台命令列介面 (Azure CLI) 管理檔案共用。Azure CLI 提供您一組開放原始碼的跨平台命令集合，供您運用在 Azure 儲存體上，包括檔案儲存體。它提供許多與 Azure 入口網站相同的功能，以及豐富的資料存取功能。如需範例，請參閱[使用 Azure CLI 搭配 Azure 儲存體](storage-azure-cli.md)。

## 使用檔案儲存體開發 ##

身為開發人員，您可以使用 [Azure Storage Client Library for Java](https://github.com/azure/azure-storage-java) 建立具有檔案儲存體的應用程式。如需程式碼範例，請參閱[如何使用 Java 的檔案儲存體](storage-java-how-to-use-file-storage.md)。

您也可以使用 [Azure Storage Client Library for Node.js](https://github.com/Azure/azure-storage-node) 針對檔案儲存體進行開發。

## 意見反應和詳細資訊 ##

Linux 使用者，歡迎您提供相關資訊！

Linux 使用者群組的 Azure 檔案儲存體提供論壇，讓您在 Linux 上評估並採用檔案儲存體的同時分享意見。請寄電子郵件至 [Azure 檔案儲存體 Linux 使用者](mailto:azurefileslinuxusers@microsoft.com)以加入使用者的群組。

## 後續步驟

請參閱這些連結以取得 Azure 檔案儲存體的相關詳細資訊。

### 概念性文章和影片

- [Azure 檔案儲存體：適用於 Windows 和 Linux 的無摩擦雲端 SMB 檔案系統](https://azure.microsoft.com/documentation/videos/azurecon-2015-azure-files-storage-a-frictionless-cloud-smb-file-system-for-windows-and-linux/)
- [在 Windows 上開始使用 Azure 檔案儲存體](storage-dotnet-how-to-use-files.md)

### 檔案儲存體的工具支援

- [使用 AzCopy 命令列公用程式傳輸資料](storage-use-azcopy.md)
- 使用 Azure CLI [建立和管理檔案共用](storage-azure-cli.md#create-and-manage-file-shares)

### 參考

- [檔案服務 REST API 參考](http://msdn.microsoft.com/library/azure/dn167006.aspx)

### 部落格文章

- [Azure 檔案儲存體現已公開推出](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/)
- [Azure 檔案儲存體內部](https://azure.microsoft.com/blog/inside-azure-file-storage/)
- [Microsoft Azure 檔案服務簡介](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)
- [保留與 Microsoft Azure 檔案的連線](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx)

<!---HONumber=AcomDC_0302_2016-------->