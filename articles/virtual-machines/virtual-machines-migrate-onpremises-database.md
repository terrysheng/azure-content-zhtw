<properties
	pageTitle="將內部部署資料庫移轉到 Azure 虛擬機器中的 SQL Server"
	description="深入了解如何將內部部署使用者資料庫移轉到 Azure 虛擬機器中的 SQL Server。"
	services="virtual-machines"
	documentationCenter=""
	authors="carlrabeler"
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/16/2015"
	ms.author="carlrab"/>


# 將資料庫移轉至 Azure VM 上的 SQL Server

有多個方法可將內部部署 SQL Server 使用者資料庫移轉至 Azure VM 中 SQL Server。這篇文章將簡短討論各種方法、建議各種案例的最佳方法，並包含的教學課程引導您使用「將 SQL Server Database部署到 Microsoft Azure VM」精靈。

## 主要的移轉方法有哪些？

主要的移轉方法如下：

- 使用「將 SQL Server Database 部署到 Microsoft Azure VM」精靈
- 使用壓縮執行在內部部署備份，並手動將備份檔案複製到 Azure 虛擬機器
- 執行備份至 URL 並從 URL 還原到 Azure 虛擬機器
- 中斷連結並將資料和記錄檔複製到 Azure blob 儲存體，然後從 URL 連結至 Azure VM 中的 SQL Server
- 將內部部署實體機器轉換為 Hyper-V VHD，接著上傳至 Azure Blob 儲存體，然後使用上傳的 VHD 部署為新的 VM
- 使用 Windows 匯入/匯出服務寄送硬碟機

> [AZURE.NOTE]如果您有內部部署的 AlwaysOn，您也可以考慮使用 [[加入 Azure 複本精靈](https://msdn.microsoft.com/library/dn463980.aspx)] 在 Azure 中建立複本，然後進行容錯移轉作為移轉的方法。

## 選擇您的移轉方法

為了達到最佳的資料傳輸效能，將資料庫檔案移轉至 Azure VM 時使用壓縮的備份檔案通常是最好的方法。這就是[「將 SQL Server Database 部署到 Microsoft Azure VM」精靈](#azure-vm-deployment-wizard-tutorial)所使用的方法。當壓縮的資料庫備份檔時小於 1 TB 時，建議的方法是使用此精靈將執行 SQL Server 2005 或更新版本的內部部署使用者資料庫移轉到 SQL Server 2014 或更新版本。

如果因為資料庫備份檔案太大，或您的目的地 SQL Server 執行個體不是 SQL Server 2014 或更新版本而無法使用精靈，則移轉程序會成為手動程序，通常會從資料庫備份開始，接著將資料庫備份複製到 Azure，然後完成資料庫還原。您也可以將資料庫檔案本身複製到 Azure。若要將資料庫移轉到 Azure VM，有數種方法可以完成此手動程序。

> [AZURE.NOTE]當您從舊版 SQL Server 升級至 SQL Server 2014 或 SQL Server 2016 時，您應該考慮是否需要變更。我們建議您先解決所有新版本 SQL Server 不支援的功能相依性作為移轉專案的一部分。如需有關支援版本和案例的詳細資訊，請參閱《[升級至 SQL Server](https://msdn.microsoft.com/library/bb677622.aspx)》。

下表會列出每個主要的移轉方法，並討論何時使用每個方法是最合適的。

| 方法 | 來源資料庫版本 | 目的地資料庫版本 | 來源資料庫備份的大小條件約束 | 注意事項 |
|---|---|---|---|---|
| [使用「將 SQL Server 資料庫部署到 Microsoft Azure VM」精靈](#azure-vm-deployment-wizard-tutorial) | SQL Server 2005 或更新版本 | SQL Server 2014 或更新版本 | > 1 TB | 盡可能使用最快速且最簡單的方法來移轉到 Azure 虛擬機器中新的或現有 SQL Server 執行個體 |
| [使用壓縮執行在內部部署備份，並手動將備份檔案複製到 Azure 虛擬機器](#backup-to-file-and-copy-to-vm-and-restore) | SQL Server 2005 或更新版本 | SQL Server 2005 或更新版本 | [Azure VM 儲存體限制](https://azure.microsoft.com/zh-tw/documentation/articles/azure-subscription-service-limits/) | 使用時機僅限於當您無法使用此精靈時，例如當目的地資料庫版本早於 SQL Server 2012 SP1 CU2，或資料庫備份大小大於 1 TB (使用 SQL Server 2016 時則為 12.8 TB) |
| [執行備份至 URL 並從 URL 還原到 Azure 虛擬機器](#backup-to-url-and-restore) | SQL Server 2012 SP1 CU2 或更新版本 | SQL Server 2012 SP1 CU2 或更新版本 | > 1 TB (對於 SQL Server 2016，則為 < 12.8 TB) | 通常使用 [[備份至 URL](https://msdn.microsoft.com/library/dn435916.aspx)] 在效能上相當於使用精靈，但較不容易 |
| [中斷連結並將資料和記錄檔複製到 Azure blob 儲存體，然後從 URL 連結至 Azure 虛擬機器中的 SQL Server](#detach-and-copy-to-url-and-attach-from-url) | SQL Server 2005 或更新版本 | SQL Server 2014 或更新版本 | [Azure VM 儲存體限制](https://azure.microsoft.com/zh-tw/documentation/articles/azure-subscription-service-limits/) | 使用時機包含將資料庫檔案連結至 Azure VM 中 SQL Server 時[使用 Azure Blob 儲存體服務儲存這些檔案](https://msdn.microsoft.com/library/dn385720.aspx)，尤其是針對極大的資料庫 |
| [將內部部署機器轉換為 Hyper-V VHD，接著上傳至 Azure Blob 儲存體，然後使用上傳的 VHD 部署新的虛擬機器](#convert-to-vm-and-upload-to-url-and-deploy-as-new-vm) | SQL Server 2005 或更新版本 | SQL Server 2005 或更新版本 | [Azure VM 儲存體限制](https://azure.microsoft.com/zh-tw/documentation/articles/azure-subscription-service-limits/) | 使用時機包含[使用您自己的 SQL Server 授權時](../data-management-azure-sql-database-and-sql-server-iaas/)、移轉將在舊版 SQL Server 上執行的資料庫時，或同時移轉系統和使用者資料庫作為其他使用者資料庫和/或系統資料庫的一部分資料庫相依性時。 |
| [使用 Windows 匯入/匯出服務寄送硬碟機](#ship-hard-drive) | SQL Server 2005 或更新版本 | SQL Server 2005 或更新版本 | [Azure VM 儲存體限制](https://azure.microsoft.com/zh-tw/documentation/articles/azure-subscription-service-limits/) | 當手動複製方法太慢，例如包含極大資料庫時，請使用 [Windows 匯入/匯出服務](../storage-import-export-service/) |

## Azure VM 部署精靈教學課程

使用 Microsoft SQL Server Management Studio 中的「將 SQL Server Database 部署到 Microsoft Azure VM」精靈，將 SQL Server 2005、SQL Server 2008、SQL Server 2008 R2、SQL Server 2012、SQL Server 2014 或 SQL Server 2016 內部部署使用者資料庫 (最多 1 TB) 移轉到 Azure 虛擬機器中的 SQL Server 2014 或 SQL Server 2016。使用此精靈將使用者資料庫移轉到現有的 Azure 虛擬機器，或使用移轉程序期間由精靈所建立 SQL Server 的 Azure VM。當您將資料庫移轉到更新版本的 SQL Server 時，資料庫會自動在程序期間進行升級。

### 取得「將 SQL Server Database 部署到 Microsoft Azure VM」精靈的最新版本

使用最新版本的 Microsoft SQL Server Management Studio for SQL Server，以確定您已擁有「將 SQL Server Database 部署到 Microsoft Azure VM」精靈的最新版本。此精靈的最新版本會併入 Azure 入口網站的最新更新，且支援資源庫中最新的 Azure VM 映像 (較舊版本的精靈可能無法運作)。若要取得最新版本的 Microsoft SQL Server Management Studio for SQL Server，請進行[下載](http://go.microsoft.com/fwlink/?LinkId=616025)並將其安裝在可連線到您規劃移轉的資料庫和網際網路的用戶端電腦。

### 設定現有的 Azure 虛擬機器和 SQL Server 執行個體 (如果適用)

如果您要移轉到現有的 Azure VM，則必須遵循下列設定步驟：

- 透過遵循《[在 Azure 上佈建 SQL Server 虛擬機器](../virtual-machines-provision-sql-server/#SSMS)》中〈從其他電腦上的 SSMS 連線至 SQL Server VM 執行個體〉一節的步驟，設定 Azure VM 和 SQL Server 執行個體來啟用從另一部電腦的連線功能。如果您使用精靈進行移轉，則僅支援資源庫中的 SQL Server 2014 和 SQL Server 2016 映像。
- 使用私人通訊埠 11435 在 Microsoft Azure 閘道器上設定 SQL Server 雲端配接器服務的開啟端點。此連接埠是作為 Microsoft Azure VM 上 SQL Server 2014 或 SQL Server 2016 佈建的一部分而建立。雲端配接器也會建立 Windows 防火牆規則，以允許其在預設通訊埠 11435 的傳入 TCP 連接。此端點可讓精靈利用雲端配接器服務，將備份檔案從內部部署執行個體複製到 Azure VM。如需詳細資訊，請參閱《[適用 SQL Server 的雲端配接器](https://msdn.microsoft.com/library/dn169301.aspx)》。

	![建立雲端配接器端點](./media/virtual-machines-migrate-onpremises-database/cloud-adapter-endpoint.png)

### 執行使用「將 SQL Server Database 部署到 Microsoft Azure VM」精靈

1. 開啟 Microsoft SQL Server Management Studio for Microsoft SQL Server 2016 並連線至包含將移轉到 Azure VM 之使用者資料庫的 SQL Server 執行個體。
2. 在您要移轉的資料庫上按右鍵，指向 [工作]，然後按一下 [部署到 Microsoft Azure VM]。

	![啟動精靈](./media/virtual-machines-migrate-onpremises-database/start-wizard.png)

3. 在 [簡介] 頁面上，按一下 [下一步]。
4. 在 [來源設定] 頁面上，連線至包含將移轉到 Azure VM 之資料庫的 SQL Server 執行個體。
5. 指定備份檔案的暫存位置。如果連線至遠端伺服器，您必須指定網路磁碟機。

	![來源設定](./media/virtual-machines-migrate-onpremises-database/source-settings.png)

6. 按 [下一步]。
7. 在 [Microsoft Azure 登入]頁面上，按一下 [登入] 並登入至您的 Azure 帳戶。
8. 選取您要使用的訂用帳戶，然後按一下 [下一步]。

	![Azure 登入](./media/virtual-machines-migrate-onpremises-database/azure-signin.png)

9. 在 [部署設定] 頁面上，您可以指定新的或現有的雲端服務名稱和虛擬機器名稱：

 - 指定新的雲端服務名稱和虛擬機器名稱，並使用 SQL Server 2014 或 SQL Server 2016 資源庫映像來建立新的雲端服務以搭配新的 Azure 虛擬機器。

     - 如果您指定新的雲端服務名稱，請指定您將使用的儲存體帳戶。

     - 如果您指定現有的雲端服務名稱，則系統將擷取儲存體帳戶並為您輸入。

 - 指定現有的雲端服務名稱和新的虛擬機器名稱，以在現有的雲端服務中建立新的 Azure 虛擬機器。僅指定 SQL Server 2014 或 SQL Server 2016 資源庫映像。
 - 指定現有的雲端服務名稱和虛擬機器名稱來使用現有的 Azure 虛擬機器。這必須是使用 SQL Server 2014 或 SQL Server 2016 資源庫映像所建置的映像。

		![Deploymnent Settings](./media/virtual-machines-migrate-onpremises-database/deployment-settings.png)

10. 按一下 [設定]
  - 如果您已指定現有的雲端服務名稱和虛擬機器名稱，系統會提示您提供使用者名稱和密碼。

		![Azure machine settings](./media/virtual-machines-migrate-onpremises-database/azure-machine-settings.png)

	- 如果您指定新的虛擬機器名稱，系統會提示您從資源庫映像清單中選取映像，並提供下列資訊：
	  - 映像 – 僅選取 SQL Server 2014 或 SQL Server 2016
		- 使用者名稱
		- 新密碼
		- 確認密碼
		- 位置
		- 大小。
 	- 此外，按一下以接受自我產生的憑證以便用於這部新的 Microsoft Azure 虛擬機器，然後按一下 [確定]。

	![Azure 新機器設定](./media/virtual-machines-migrate-onpremises-database/azure-new-machine-settings.png)

11. 如果與來源資料庫名稱不同，請指定目標資料庫名稱。如果目標資料庫已經存在，則系統會自動遞增資料庫名稱，而不會覆寫現有的資料庫。
12. 然後依序按一下 [下一步] 和 [完成]。

	![結果](./media/virtual-machines-migrate-onpremises-database/results.png)

13. 當精靈完成時，會連線至虛擬機器，並驗證您的資料庫是否已移轉。
14. 如果您已建立新的虛擬機器，請透過遵循《[在 Azure 上佈建 SQL Server 虛擬機器](../virtual-machines-provision-sql-server/#SSMS)》中〈從其他電腦上的 SSMS 連線至 SQL Server 虛擬機器執行個體〉一節的步驟，設定 Azure 虛擬機器和 SQL Server 執行個體。

## 備份至檔案並複製到 VM 和還原

當您因為要移轉至 SQL Server 2014 之前的 SQL Server 版本，或備份檔案大於 1 TB 而無法使用「將 SQL Server Database 部署到 Microsoft Azure VM」精靈時，請使用這個方法。如果您的備份檔案大於 1 TB，您必須等量磁碟區，因為 VM 磁碟的大小上限為 1 TB。您可以使用下列一般步驟，使用此手動方法來移轉使用者資料庫：

1.	執行完整資料庫備份至內部部署位置。
2.	使用所需的 SQL 伺服器版本建立或上傳虛擬機器。
3.	使用《[在 Azure 上佈建 SQL Server 虛擬機器](../virtual-machines-provision-sql-server/#SSMS)》中的步驟來佈建虛擬機器。
4.	使用遠端桌面、Windows 檔案總管或命令提示字元的 copy 命令，將您的備份檔案複製到您的 VM。

## 備份至 URL 和還原

當您因為備份檔案大於 1 TB，且移轉來源或目標為 SQL Server 2016 而無法使用「將 SQL Server Database 部署到 Microsoft Azure VM」精靈時，請使用《[備份至 URL](https://msdn.microsoft.com/library/dn435916.aspx)》方法。對於小於 1 TB，或執行 SQL Server 2016 之前 SQL Server 版本的資料庫，我們建議使用此精靈。當您想要提升效能，且需要超過每個 blob 的大小限制時，建議使用 SQL Server 2016 以支援等量備份組。對於大型資料庫，建議使用 [Windows 匯入/匯出服務](../storage-import-export-service/)。

## 中斷連結並複製至 URL 以及從 URL 連結

當您打算[使用 Azure Blob 儲存體服務](https://msdn.microsoft.com/library/dn385720.aspx)來存放這些檔案，並將其連結至在 Azure VM 中執行的 SQL Server，尤其是針對極大資料庫時，請使用這個方法。您可以使用下列一般步驟，使用此手動方法來移轉使用者資料庫：

1.	從內部部署資料庫執行個體中斷連結資料庫檔案。
2.	使用 [AZCopy 命令列公用程式](../storage-use-azcopy/)，將中斷連結的資料庫檔案複製到 Azure blob 儲存體。
3.	將資料庫檔案從 Azure URL 連結至 Azure VM 中的 SQL Server 執行個體。

## 轉換為 VM 並上傳至 URL 以及部署為新的 VM

您可以使用這個方法，將內部部署 SQL Server 執行個體中的所有系統和使用者資料庫移轉至 Azure 虛擬機器。您可以使用下列一般步驟，使用此手動方法來移轉整個 SQL Server 執行個體：

1.	使用[Microsoft 虛擬機器轉換器](http://technet.microsoft.com/library/dn873998.aspx)，將實體或虛擬機器轉換為 Hyper-V VHD。
2.	使用 [Add-azurevhd cmdlet](https://msdn.microsoft.com/library/windowsazure/dn495173.aspx)，將 VHD 檔案上傳至 Azure 儲存體。
3.	使用上傳的 VHD 來部署新的虛擬機器。

> [AZURE.NOTE]若要移轉整個應用程式，請考慮使用 [Azure Site Recovery](../services/site-recovery/)。

## 寄送硬碟機

透過網路進行上傳所費不貲或不可行時，請使用 [Windows 匯入/匯出服務方法](../storage-import-export-service/)，將大量檔案資料移轉至 Azure Blob 儲存體。您可以使用此服務，將包含該資料的一個或多個硬碟送至 Azure 資料中心，而您的資料將會在此上傳至儲存體帳戶。

<!---HONumber=July15_HO3-->