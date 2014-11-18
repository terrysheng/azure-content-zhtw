<properties urlDisplayName="How to create" pageTitle="如何建立儲存體帳戶 | Azure" metaKeywords="" description="Learn how to create a storage account in the Azure management portal." metaCanonical="" services="storage" documentationCenter="" title="How To Create a Storage Account" solutions="" authors="tamram" manager="adinah" editor="cgronlun" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="tamram" />


<h1><a id="createstorageaccount"></a>如何建立儲存體帳戶</h1>

若要在 Azure 的 Blob、資料表、佇列和檔案服務中儲存檔案和資料，您必須在想要儲存資料的地理區域中建立儲存體帳戶。本主題說明如何在 Azure 管理入口網站中建立儲存體帳戶。

如需儲存體帳戶容量和輸送量的詳細資訊，請參閱 [Azure 儲存體延展性和效能目標](http://msdn.microsoft.com/zh-tw/library/dn249410.aspx) (英文)。

> [WACOM.NOTE] 對於 Azure 虛擬機器，如果您在部署位置中沒有儲存體帳戶，則會在該位置自動建立儲存體帳戶。儲存體帳戶名稱將以虛擬機器名稱為基礎。

##目錄##

* [作法：建立儲存體帳戶](#create)
* [後續步驟](#next)

<h2><a id="create"></a>作法：建立儲存體帳戶</h2>

1. 登入[管理入口網站](https://manage.windowsazure.com)。

2. 依序按一下 [**建立新的**]、[**儲存體**] 和 [**快速建立**]。

	![NewStorageAccount](./media/storage-create-storage-account/storage_NewStorageAccount.png)

3. 在 [**URL**] 中，輸入要用於儲存體帳戶 URL 的子網域名稱。若要存取儲存體中的物件，請將物件的位置附加至端點。例如，可供存取 BLOB 的 URL 可能是 http://*myaccount*.blob.core.windows.net/*mycontainer*/*myblob*。

4. 在 [**區域/同質群組**] 中，選取儲存體的區域或同質群組。如果您希望儲存體服務位於與您目前使用的其他 Azure 服務相同的資料中心，請選取同質群組而非區域。這麼做可改善效能，而且出口流量不會產生任何費用。

	> [WACOM.NOTE]  若要建立同質群組，請開啟管理入口網站的 [<b>設定</b>] 區域、按一下 [<b>同質群組</b>]，然後按一下 [<b>加入同質群組</b>] 或 [<b>新增</b>] 按鈕。您也可以使用 Azure 服務管理 API 建立和管理同質群組。如需詳細資訊，請參閱<a href="http://msdn.microsoft.com/zh-tw/library/windowsazure/ee460798.aspx">同質群組的相關作業</a>。

5. 如果您有一個以上的 Azure 訂用帳戶，則會顯示 [**訂用帳戶**] 欄位。在 [**訂用帳戶**] 中，輸入您要使用儲存體帳戶的 Azure 訂用帳戶。您可以針對一項訂用帳戶建立多達 5 個儲存體帳戶。

6.  在 [**複寫**] 中，選取您的儲存體帳戶所需的複寫層級。

	[WACOM.INCLUDE [storage-replication-options](../includes/storage-replication-options.md)]

6. 按一下 [**建立儲存體帳戶**]。

	建立儲存體帳戶可能需要花費數分鐘的時間。若要檢查狀態，可以監控位於入口網站底部的通知。建立儲存體帳戶之後，新的儲存體帳戶會處於 [**線上**] 狀態並可提供使用。 

	![StoragePage](./media/storage-create-storage-account/Storage_StoragePage.png)

<h2><a id="next"></a>後續步驟</h2>

- 若要深入了解 Azure 儲存體，請參閱 [azure.com](http://azure.microsoft.com/zh-tw/documentation/services/storage/) (英文) 和 [MSDN](http://msdn.microsoft.com/zh-tw/library/gg433040.aspx) (英文) 上的 Azure 儲存體文件。 

- 造訪 [Azure 儲存體團隊部落格](http://blogs.msdn.com/b/windowsazurestorage/) (英文)。

