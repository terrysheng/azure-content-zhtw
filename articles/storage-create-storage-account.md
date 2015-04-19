<properties 
	pageTitle="如何建立、管理或刪除儲存體帳戶 | Azure" 
	description="了解如何在 Azure 管理入口網站中建立、管理或刪除儲存體帳戶。" 
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
	ms.date="11/17/2014" 
	ms.author="tamram"/>


# 關於 Azure 儲存體帳戶

Azure 儲存體帳戶是一個安全的帳戶，可讓您存取「Azure 儲存體」中的服務。您的儲存體帳戶可為您的資料提供唯一的命名空間，且根據預設，只有您 (帳戶擁有者) 可以使用。 

儲存體帳戶分為兩種類型：

- Standard 儲存體帳戶包括 Blob、資料表和佇列儲存體。若要使用檔案儲存體，只需透過 [Azure Preview 頁面](/zh-tw/services/preview/)提出要求即可。
- Premium 儲存體帳戶目前僅支援 Azure 虛擬機器磁碟。若要使用「Azure Premium 儲存體」，只需透過 [Azure Preview 頁面](/zh-tw/services/preview/)提出要求即可。請參閱 [Premium 儲存體：適用於 Azure 虛擬機器工作負載的高效能儲存體](http://go.microsoft.com/fwlink/?LinkId=521898)，以取得「Premium 儲存體」的深入概觀。

您將按照儲存體帳戶的「Azure 儲存體」使用量付費。儲存體成本以四項因素為基礎：儲存體容量、複寫配置、儲存體交易數與資料外送流量。 

- 儲存體容量是指您用於儲存資料的儲存體帳戶配額。若只是儲存資料，則成本是由您所儲存的資料量和複寫資料的方式來決定。 
- 複寫可決定要在什麼位置一次維持多少份資料。 
- 交易是指對 Azure 儲存體進行的所有讀取和寫入作業。 
- 資料外送流量是指傳出 Azure 地區的資料。當您儲存體帳戶中的資料受不同地區中執行的應用程式存取時，不論該應用程式是雲端服務還是其他某類應用程式，您都要負擔資料外送流量的費用。(若為 Azure 服務，您可以採取步驟，將資料和服務群組在相同的資料中心，以減少或消除資料外送流量費用。)  

[儲存體定價詳細資料](http://azure.microsoft.com/pricing/details/#storage)頁面提供儲存體容量、複寫和交易的詳細定價資訊。[資料傳輸定價詳細資料](http://azure.microsoft.com/pricing/details/data-transfers/)則提供資料外送流量的詳細定價資訊。

如需有關儲存體帳戶容量和效能目標的詳細資訊，請參閱 [Azure 儲存體延展性和效能目標](http://msdn.microsoft.com/library/windowsazure/dn249410.aspx)。

> [AZURE.NOTE] 當您建立 Azure 虛擬機器時，如果您在部署位置中沒有儲存體帳戶，則會在該位置自動建立儲存體帳戶。因此，您無須依照下方的步驟為虛擬機器磁碟建立儲存體帳戶。儲存體帳戶名稱將以虛擬機器名稱為基礎。如需詳細資訊，請參閱 [Azure 虛擬機器文件](/zh-tw/documentation/services/virtual-machines/)。 <br />

## 目錄 ##

本文說明如何建立標準儲存體帳戶，以及建立帳戶時所需考量的一些決定。此外也說明如何管理您的儲存體帳戶存取金鑰，以及如何刪除儲存體帳戶。


- [做法：建立儲存體帳戶](#create)
- [做法：檢視、複製及重新產生儲存體存取金鑰](#regeneratestoragekeys)
- [做法：刪除儲存體帳戶](#deletestorageaccount)
* [後續步驟](#next)

## <a id="create"></a>做法：建立儲存體帳戶 ##

1. 登入[管理入口網站](https://manage.windowsazure.com)。

2. 依序按一下 [建立新項目]、[儲存體] 和 [快速建立]。

	![NewStorageAccount](./media/storage-create-storage-account/storage_NewStorageAccount.png)

3. 在 [URL] 中，輸入儲存體帳戶的名稱。請參閱下方的[儲存體帳戶端點](#account-endpoints) ，以深入了解此名稱如何用來解析您在 Azure 儲存體中儲存的物件。

4. 在 [地點/同質群組] 中，選取靠近您或客戶的儲存體帳戶位置。如果您將會從其他 Azure 服務 (例如 Azure 虛擬機器或雲端服務) 存取儲存體帳戶，您可以從清單中選取同質群組，以將儲存體帳戶與您使用的其他 Azure 服務劃分到相同的資料中心，以提升效能並降低成本。 

	> [AZURE.NOTE] 請注意，您必須在儲存體帳戶建立時選取同質群組；您無法將現有的帳戶移至同質群組。

	如需有關同質群組的詳細資料，請參閱下方的[同質群組的服務共置](#affinity-group)。

	
5. 如果您有一個以上的 Azure 訂用帳戶，就會顯示 [訂用帳戶] 欄位。在 [訂用帳戶] 中，輸入您要使用儲存體帳戶的 Azure 訂用帳戶。您可以針對一個訂用帳戶建立多達 5 個儲存體帳戶。

6. 在 [複寫] 中，為您的儲存體帳戶選取適當的複寫層級。建議的複寫選項是 [異地備援] 複寫，這可為您的資料提供最大的耐用性。如需有關「Azure 儲存體」複寫選項的詳細資訊，請參閱下方的[儲存體帳戶複寫選項](#replication-options)。

6. 按一下 [建立儲存體帳戶]。

	建立儲存體帳戶可能需要花費數分鐘的時間。若要檢查狀態，可以監視位於入口網站底部的通知。建立儲存體帳戶之後，新的儲存體帳戶會處於 [線上] 狀態並已可供使用。 

![StoragePage](./media/storage-create-storage-account/Storage_StoragePage.png)


### <a id="account-endpoints"></a>儲存體帳戶端點 

您儲存在「Azure 儲存體」中的每個物件都有唯一的 URL 位址；儲存體帳戶名稱會形成該位址的子網域。各個服務特定的子網域與網域名稱會一起形成儲存體帳戶的「端點」 **。 

例如，如果您儲存體帳戶的名稱為  *mystorageaccount*，則您儲存體帳戶的預設端點是： 

- Blob 服務: http://*mystorageaccount*.blob.core.windows.net

- 資料表服務: http://*mystorageaccount*.table.core.windows.net

- 佇列服務: http://*mystorageaccount*.queue.core.windows.net

- 檔案服務: http://*mystorageaccount*.file.core.windows.net

建立帳戶後，您可以在「Azure 管理入口網站」的「儲存體儀表板」上看見您儲存體帳戶的端點。

用以存取儲存體帳戶中某物件的 URL，可藉由在端點後附加該物件在儲存體帳戶中的位置來建置。例如，Blob 位址的格式可能如下： http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob*.

您也可以設定與儲存體帳戶搭配使用的自訂網域名稱。如需詳細資料，請參閱[針對儲存體帳戶中的 Blob 資料設定自訂網域名稱](storage-custom-domain-name.md)。

### <a id="affinity-group"></a>同質群組的服務共置 

「同質群組」**是將您的 Azure 服務和 VM 與 Azure 儲存體帳戶依地理位置形成的群組。同質群組會將電腦工作負載置於相同的資料中心內或目標使用者對象附近，因而能改善服務效能。此外，當您儲存體帳戶中的資料是由同一同質群組中的服務存取時，資料外送流量並不會產生任何費用。

> [AZURE.NOTE]  若要建立同質群組，請開啟「管理入口網站」的 [設定]<b></b> 區域，按一下 [同質群組]<b></b>，然後按一下 [加入同質群組]<b></b> 或 [新增]<b></b> 按鈕。您也可以使用 Azure 服務管理 API 建立及管理同質群組。如需詳細資訊，請參閱<a href="http://msdn.microsoft.com/library/windowsazure/ee460798.aspx">同質群組的相關作業</a>。


### <a id="replication-options"></a>儲存體帳戶複寫選項

[AZURE.INCLUDE [storage-replication-options](../includes/storage-replication-options.md)]


## <a id="regeneratestoragekeys"></a>做法：檢視、複製及重新產生儲存體存取金鑰

當您建立儲存體帳戶時，Azure 會產生兩個 512 位元的儲存體存取金鑰，做為存取儲存體帳戶時的驗證憑藉。透過提供這兩個儲存體存取金鑰，Azure 讓您可重新產生金鑰，同時又不需中斷儲存體服務或對該服務的存取。

> [AZURE.NOTE] 建議您避免將儲存體帳戶存取金鑰透露給其他任何人。若要允許在不出示存取金鑰的情況下存取儲存體資源，您可以使用「共用存取簽章」**。共用存取簽章可在您定義的間隔期間內，使用您所指定的權限，來存取帳戶中的資源。如需詳細資訊，請參閱[共用存取簽章教學課程](storage-dotnet-shared-access-signature-part-1.md)。

在[管理入口網站](http://manage.windowsazure.com)中，使用儀表板或 [儲存體] 頁面上的 [管理金鑰]，來檢視、複製和重新產生用於存取 Blob、「資料表」及「佇列」服務的儲存體存取金鑰。 

### 複製儲存體存取金鑰 ###

您可以使用 [管理金鑰] 來複製要在連接字串中使用的儲存體存取金鑰。連接字串需有要在驗證中使用的儲存體帳戶名稱和金鑰。如需有關設定連接字串以存取 Azure 儲存體服務的資訊，請參閱[設定連接字串](http://msdn.microsoft.com/library/ee758697.aspx).

1. 在[管理入口網站](http://manage.windowsazure.com)中，按一下 [儲存體]，然後按一下儲存體帳戶的名稱以開啟儀表板。

2. 按一下 [管理金鑰]。

 	[管理存取金鑰] 隨即開啟。

	![Managekeys](./media/storage-create-storage-account/Storage_ManageKeys.png)

 
3. 若要複製儲存體存取金鑰，請選取金鑰文字。然後按一下滑鼠右鍵，再按一下 [複製]。

### 重新產生儲存體存取金鑰 ###
您應定期變更儲存體帳戶的存取金鑰，讓儲存體連線更加安全。指派的兩個存取金鑰可讓您在重新產生一個存取金鑰的同時，使用另一個存取金鑰維持儲存體帳戶連線。 

> [AZURE.WARNING] 重新產生存取金鑰會影響虛擬機器、媒體服務，以及任何相依於儲存體帳戶的應用程式。所有使用存取金鑰來存取儲存體帳戶的用戶端，都必須更新為使用新的金鑰。

**虛擬機器** - 如果您的儲存體帳戶含有任何執行中的虛擬機器，在重新產生存取金鑰之後，您將必須重新部署所有虛擬機器。若要避免重新部署，請在重新產生存取金鑰之前關閉虛擬機器。
 
**媒體服務** - 如果您有媒體服務與儲存體帳戶相依，在重新產生金鑰之後，您必須將存取金鑰與媒體服務重新同步。
 
**應用程式** - 如果您有 Web 應用程式或雲端服務正在使用儲存體帳戶，在重新產生金鑰之後將會失去連線，除非您更換金鑰。程序如下：

1. 更新應用程式程式碼中的連接字串，以參考儲存體帳戶的次要存取金鑰。 

2. 重新產生儲存體帳戶的主要存取金鑰。在[管理入口網站](http://manage.windowsazure.com)中，從儀表板或 [設定] 頁面按一下 [管理金鑰]。按一下主要存取金鑰下的 [重新產生]，然後按一下 [是] 以確認要產生新的金鑰。

3. 更新程式碼中的連接字串，以參考新的主要存取金鑰。

4. 重新產生次要存取金鑰。

## <a id="deletestorageaccount"></a>做法：刪除儲存體帳戶

若要移除不再使用的儲存體帳戶，請使用儀表板或 [設定] 頁面上的 [刪除]。[刪除] 會刪除整個儲存體帳戶，包括帳戶中的所有 Blob、資料表和佇列。 

> [AZURE.WARNING] 您將無法還原已刪除之儲存體帳戶中的內容。在刪除帳戶之前，請務必先備份所有要保留的資料。 <br />
	如果儲存體帳戶包含 Azure 虛擬機器的任何 VHD 檔案或磁碟，則必須先刪除使用了這些 VHD 檔案的映像和磁碟，才能刪除儲存體帳戶。首先，停止執行中的虛擬機器，然後將它刪除。若要刪除磁碟，請瀏覽至 [磁碟] 索引標籤並刪除儲存體帳戶中包含的所有磁碟。若要刪除映像，請瀏覽至 [映像] 索引標籤並刪除帳戶中儲存的所有映像。


1. 在[管理入口網站](http://manage.windowsazure.com)中，按一下 [儲存體]。

2. 按一下儲存體帳戶項目中名稱以外的任何位置，然後按一下 [刪除]。

	 -或-

	按一下儲存體帳戶名稱以開啟儀表板，然後按一下 [刪除]。

3. 按一下 [是] 以確認您要刪除儲存體帳戶。

## <a id="next"></a>後續步驟

- 若要深入了解「Azure 儲存體」，請參閱 [azure.com](http://azure.microsoft.com/documentation/services/storage/) 和 [MSDN](http://msdn.microsoft.com/library/gg433040.aspx) 上的「Azure 儲存體」文件。 

- 請造訪 [Azure 儲存體團隊部落格](http://blogs.msdn.com/b/windowsazurestorage/)。



<!--HONumber=42-->
