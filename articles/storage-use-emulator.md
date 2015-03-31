<properties 
	pageTitle="使用 Azure 儲存體模擬器進行開發和測試" 
	description="了解如何使用 Azure 儲存體模擬器進行開發和測試。" 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="adinah" 
	editor=""/>
<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/20/2015" 
	ms.author="tamram"/>

# 使用 Azure 儲存體模擬器進行開發和測試

## 概觀
Microsoft Azure 儲存體模擬器提供了模擬 Azure Blob、佇列和資料表服務的本機環境，以供進行開發。使用儲存體模擬器，您可以針對儲存體服務在本機測試您的應用程式，而不會產生任何費用。

> [AZURE.NOTE] 儲存體模擬器可當成 Microsoft Azure SDK 一部分來使用。您也可以將儲存體模擬器安裝為獨立的封裝。
若要設定儲存體模擬器，您必須在電腦上具有系統管理權限。 
> 請注意，在某個儲存體模擬器版本中建立的資料不保證可在使用不同版本時加以存取。如果您需要長期保存資料，建議您將資料儲存於 Azure 儲存體帳戶中 (而不是儲存體模擬器中)。
 
儲存體模擬器與 Azure 儲存體服務之間存在一些差異。如需這些差異的詳細資訊，請參閱[儲存體模擬器和 Azure 儲存體服務之間的差異](https://msdn.microsoft.com/library/azure/gg433135.aspx)。

儲存體模擬器會使用 Microsoft (r) SQL Server(tm) 執行個體以及本機檔案系統來模擬 Azure 儲存體服務。根據預設，已針對 Microsoft (r) SQL Server (tm) 2012 Express LocalDB 中的資料庫設定儲存體模擬器。您可以安裝 SQL Server Management Studio Express 來管理您的 LocalDB 安裝。儲存體模擬器會使用 Windows 驗證，連接到 SQL Server 或 LocalDB。您可以選擇使用儲存體模擬器命令列工具參考來設定儲存體模擬器，以存取 SQL Server 的本機執行個體，而不是 LocalDB。

## 驗證要求

儲存體模擬器僅支援單一固定帳戶及已知的驗證金鑰。此帳戶和金鑰都是唯一允許搭配儲存體模擬器使用的認證，如下：

    Account name: devstoreaccount1
    Account key: Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtrKBHBeksoGMGw==
    
> [AZURE.NOTE] 儲存體模擬器所支援的驗證金鑰僅可用來測試用戶端驗證碼的功能。它不提供任何安全性用途。您無法將生產儲存體帳戶和金鑰與儲存體模擬器搭配使用。也請注意，您不應該將開發帳戶與生產資料搭配使用。
 

## 啟動及初始化儲存體模擬器
若要啟動 Azure 儲存體模擬器，請選取 [開始] 按鈕或按下 Windows 鍵。開始輸入 Azure 儲存體模擬器，並從應用程式清單中選取 [Azure 儲存體模擬器]。 

或者，如果 Azure 計算模擬器已經在執行中，您可以使用滑鼠右鍵按一下系統匣圖示並選取 [啟動儲存體模擬器] 來啟動儲存體模擬器。如需執行計算模擬器的詳細資訊，請參閱[在計算模擬器中執行 Azure 應用程式](https://msdn.microsoft.com/library/azure/hh403990.aspx)。

當儲存體模擬器啟動時，即會出現命令列。您可以使用此命令列來啟動和停止儲存體模擬器，以及清除資料、取得目前的狀態及初始化模擬器。如需詳細資訊，請參閱[儲存體模擬器命令列工具參考](https://msdn.microsoft.com/library/azure/gg433005.aspx)。

關閉命令列視窗時，儲存體模擬器會繼續執行。若要再次顯示命令列，請遵循上述步驟，如同啟動儲存體模擬器一樣。


當您第一次執行儲存體模擬器時，系統會為您初始化本機儲存體環境。您可以使用儲存體模擬器命令列工具來指向不同的資料庫執行個體，或者重新初始化現有的資料庫。初始化處理程序會在 LocalDB 中建立資料庫，並為每個本機儲存體服務保留 HTTP 連接埠。此步驟需要系統管理權限。如需詳細資訊，請參閱[儲存體模擬器命令列工具參考](https://msdn.microsoft.com/library/azure/gg433005.aspx)。

## 關於儲存體服務 URI

您為 Azure 儲存體服務中資源定址的方式，會根據該資源是位於 Azure 或是在儲存體模擬器服務中而有所不同。有一個 URI 配置是用來為 Azure 中的儲存體資源定址，而另一個 URI 配置是用來為儲存體模擬器中的儲存體資源定址。差異在於本機電腦不會執行網域名稱解析。這兩個 URI 配置一律會包含帳戶名稱以及所要求資源的位址。

## 在雲端中為 Azure 儲存體資源定址

在用來為 Azure 中儲存體資源定址的 URI 配置中，帳戶名稱是 URI 主機名稱的一部分，而要定址的資源是 URI 路徑的一部分。下列基本定址配置可用來存取儲存體資源：

    <http|https>://<account-name>.<service-name>.core.windows.net/<resource-path>


`<account-name>` 是儲存體帳戶的名稱。`<service-name>` 是所存取服務的名稱，而 `<resource-path>` 是所要求資源的路徑。下列清單顯示每個儲存體服務的 URI 配置：

	Blob Service: <http|https>://<account-name>.blob.core.windows.net/<resource-path>
	Queue Service: <http|https>://<account-name>.queue.core.windows.net/<resource-path>
	Table Service: <http|https>://<account-name>.table.core.windows.net/<resource-path>

例如，下列位址可能會用於存取雲端中的 Blob：
    
    http://myaccount.blob.core.windows.net/mycontainer/myblob.txt

> [AZURE.NOTE] 您也可以將自訂網域名稱與雲端中的 Blob 儲存體端點產生關聯，並使用該自訂網域名稱來為 Blob 和容器定址。請參閱 
 
## 在儲存體模擬器中為本機 Azure 儲存體資源定址

在儲存體模擬器中，因為本機電腦不會執行網域名稱解析，所以帳戶名稱是 URI 路徑的一部分。在儲存體模擬器中執行之資源的 URI 配置會遵循下列格式：

    http://<local-machine-address>:<port>/<account-name>/<resource-path>

您可以使用下列格式，來為在儲存體模擬器中執行的資源定址：

	Blob Service: http://127.0.0.1:10000/<account-name>/<resource-path>
	Queue Service: http://127.0.0.1:10001/<account-name>/<resource-path>
	Table Service: http://127.0.0.1:10002/<account-name>/<resource-path>

例如，下列位址可能會用於存取儲存體模擬器中的 Blob：

    http://127.0.0.1:10000/myaccount/mycontainer/myblob.txt

> [AZURE.NOTE] HTTPS 不是允許用來為本機儲存體資源定址的通訊協定。
 
## 使用 RA-GRS 為帳戶次要位址定址
從 3.1 版開始，儲存體模擬器帳戶就支援讀取存取地理備援複寫 (RA-GRS)。針對同時位於雲端和本機模擬器中的儲存體資源，您可以藉由將 -secondary 附加到帳戶名稱來存取次要位置。例如，下列位址可能會用於在儲存體模擬器中使用唯讀的次要位置來存取 Blob：

    http://127.0.0.1:10000/myaccount-secondary/mycontainer/myblob.txt 

> [AZURE.NOTE] 如需以程式設計方式使用儲存體模擬器來存取次要位置，請使用 Storage Client Library for.NET 3.2 版或更新版本。如需詳細資訊，請參閱＜儲存體用戶端程式庫參考＞。
 
## 使用命令列工具初始化儲存體模擬器
您可以使用儲存體模擬器命令列工具來初始化儲存體模擬器，以指向預設值以外的資料庫執行個體。如果您想要使用預設的 LocalDB 資料庫執行個體，就不需要執行個別的初始化步驟。

此工具預設會安裝至 C:\Program Files (x86) \Microsoft SDKs\Azure\Storage Emulator\ 目錄。當您第一次啟動模擬器時，初始化會自動執行。

> [AZURE.NOTE] 您必須擁有系統管理權限，才能執行 `init` 命令，如下所述。

1. 按一下 [開始]**** 按鈕或按下 [Windows]**** 鍵。開始輸入 `Azure 儲存體模擬器`，並在它出現時加以選取。命令提示字元視窗隨即出現。


2. 在命令提示字元視窗中，輸入下列命令，其中 `<SQLServerInstance>` 是 SQL Server 執行個體的名稱。若要使用 LocalDb，請指定 `(localdb)\v11.0` 做為 SQL Server 執行個體。

    WAStorageEmulator init /sqlInstance <SQLServerInstance> 
    
您也可以使用下列命令，引導模擬器使用預設的 SQL Server 執行個體：

    WAStorageEmulator init /server .\\ 

或者，您可以使用下列命令，重新初始化資料庫：

    WAStorageEmulator init /forceCreate 

## 儲存體模擬器命令列工具參考

從 3.0 版開始，當您啟動儲存體模擬器，您會看到命令提示字元隨即出現。使用命令提示字元來啟動和停止模擬器，以及查詢狀態和執行其他作業。

> [AZURE.NOTE] 如果您已安裝計算模擬器，當您啟動儲存體模擬器時，就會出現系統匣圖示。使用滑鼠右鍵按一下圖示即可顯示功能表，提供圖形化方式來啟動和停止儲存體模擬器。

### 命令列語法

	WAStorageEmulator [/start] [/stop] [/status] [/clear] [/init] [/help]

### 選項
若要檢視選項清單，請在命令提示字元中輸入 `/help`。

## 後續步驟
- [儲存體模擬器命令列工具參考](https://msdn.microsoft.com/library/azure/gg433005.aspx)
- [儲存體模擬器和 Azure 儲存體服務之間的差異](https://msdn.microsoft.com/library/azure/gg433135.aspx)
- [儲存體模擬器版本資訊](https://msdn.microsoft.com/library/azure/dn683879.aspx)

<!--HONumber=47-->
