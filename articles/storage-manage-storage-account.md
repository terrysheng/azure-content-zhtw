<properties urlDisplayName="How to manage" pageTitle="如何管理儲存體帳戶 | Microsoft Azure" metaKeywords="Azure manage storage accounts, storage account management portal, storage account geo-replication, Azure geo-replication, Azure access keys" description="了解如何使用管理入口網站來管理 Azure 中的儲存體帳戶。" metaCanonical="" services="storage" documentationCenter="" title="如何管理儲存體帳戶" authors="tamram" solutions="" manager="adinah" editor="cgronlun" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="tamram" />

# <span id="managestorageaccounts"></span></a>如何管理儲存體帳戶

## 目錄

-   [作法：管理儲存體帳戶複寫][作法：管理儲存體帳戶複寫]
-   [作法：檢視、複製和重新產生儲存體存取金鑰][作法：檢視、複製和重新產生儲存體存取金鑰]
-   [作法：刪除儲存體帳戶][作法：刪除儲存體帳戶]

## <span id="georeplication"></span></a>作法：複製儲存體帳戶資料以實現持久性和高可用性

[WACOM.INCLUDE [storage-replication-options](../includes/storage-replication-options.md)]

### 指定儲存體帳戶的複寫設定

1.  在 [Azure 管理入口網站][Azure 管理入口網站]中，按一下 [儲存體]，然後按一下您的儲存體帳戶名稱以顯示儀表板。

2.  按一下 [設定]。

3.  在 [複寫] 欄位中，選取您要用於儲存體帳戶的複寫類型。

4.  按一下 [儲存]，並在出現提示時確認您的選擇。

## <span id="regeneratestoragekeys"></span></a>作法：檢視、複製和重新產生儲存體存取金鑰

當您建立儲存體帳戶時，Azure 會產生兩個 512 位元的儲存體存取金鑰，作為存取儲存體帳戶時的驗證憑藉。透過提供這兩個儲存體存取金鑰，Azure 讓您可重新產生金鑰，同時又不需中斷儲存體服務或對該服務的存取。

在[管理入口網站][管理入口網站]中，於儀表板或 [儲存體] 頁面上使用 [管理金鑰]，來檢視、複製和重新產生用於存取 Blob、資料表與佇列服務的儲存體存取金鑰。

### 複製儲存體存取金鑰

您可以使用 [管理金鑰] 來複製要在連接字串使用中的儲存體存取金鑰。連接字串需有要在驗證中使用的儲存體帳戶名稱和金鑰。如需關於設定連接字串以存取 Azure 儲存體服務的詳細資訊，請參閱[設定連接字串][設定連接字串]。

1.  在[管理入口網站][管理入口網站]中，按一下 [儲存體]，然後按一下儲存體帳戶名稱即可開啟儀表板。

2.  按一下 [管理金鑰]。

    [管理存取金鑰] 隨即開啟。

    ![Managekeys][Managekeys]

3.  若要複製儲存體存取金鑰，請選取金鑰文字。然後按一下滑鼠右鍵並按一下 [複製]。

### 重新產生儲存體存取金鑰

您應定期變更儲存體帳戶的存取金鑰，讓儲存體連線更加安全。指派的兩個存取金鑰可讓您在重新產生一個存取金鑰的同時，使用另一個存取金鑰維持儲存體帳戶連線。

<div class="dev-callout"> 
<b>警告</b> 
<p>重新產生存取金鑰會影響虛擬機器、媒體服務，以及任何相依於儲存體帳戶的應用程式。所有使用存取金鑰來存取儲存體帳戶的用戶端，都必須更新為使用新的金鑰。
    </p> 
    </div>

**虛擬機器** - 如果您的儲存體帳戶中有任何虛擬機器在執行中，您必須在重新產生存取金鑰之後，重新部署所有的虛擬機器。若要避免重新部署，請在重新產生存取金鑰之前關閉虛擬機器。

**媒體服務** - 如果您有媒體服務相依於儲存體帳戶，您必須在重新產生金鑰之後，將存取金鑰與媒體服務重新同步。

**應用程式** - 如果您有 Web 應用程式或雲端服務正在使用儲存體帳戶，除非您變換金鑰，否則會在重新產生金鑰後失去連線。程序如下：

1.  更新應用程式程式碼中的連接字串，以參考儲存體帳戶的次要存取金鑰。

2.  重新產生儲存體帳戶的主要存取金鑰。在[管理入口網站][管理入口網站]中，從儀表板或 [設定] 頁面按一下 [管理金鑰]。按一下主要存取金鑰下的 [重新產生]，然後按一下 [是] 確認要產生新的金鑰。

3.  更新程式碼中的連接字串，以參考新的主要存取金鑰。

4.  重新產生次要存取金鑰。

## <span id="deletestorageaccount"></span></a>作法：刪除儲存體帳戶

若要移除不再使用的儲存體帳戶，請在儀表板或 [設定] 頁面上使用 [刪除]。[刪除] 會刪除整個儲存體帳戶，包括帳戶中的所有 Blob、資料表和佇列。

<div class="dev-callout">
<b>警告</b>
<p>您將無法還原已刪除之儲存體帳戶中的內容。請確定 
已確份您想要儲存的任何資料，再刪除帳戶。
    </p>
    <p>
如果儲存體帳戶包含 Azure 虛擬機器的任何 VHD 檔案或磁碟， 
則必須先刪除任何使用這些 VHD 檔案的映像和磁碟， 
才能刪除儲存體帳戶。首先，停止執行中的虛擬機器，然後予以刪除。若要刪除磁碟，請瀏覽至 [磁碟] 索引標籤並刪除儲存體帳戶中包含的所有磁碟。若要刪除映像，請瀏覽至 [映像] 索引標籤並刪除帳戶中儲存的所有映像。
    </p>
</div>

1.  在[管理入口網站][管理入口網站]中，按一下 [儲存體]。

2.  在儲存體帳戶項目中名稱以外的任何位置，然後按一下 [刪除]。

    -或-

    按一下儲存體帳戶名稱以開啟儀表板，然後按一下 [刪除]。

3.  按一下 [是] 確認要刪除儲存體帳戶。

  [作法：管理儲存體帳戶複寫]: #georeplication
  [作法：檢視、複製和重新產生儲存體存取金鑰]: #regeneratestoragekeys
  [作法：刪除儲存體帳戶]: #deletestorageaccount
  [Azure 管理入口網站]: https://manage.windowsazure.com
  [管理入口網站]: http://manage.windowsazure.com
  [設定連接字串]: http://msdn.microsoft.com/zh-tw/library/ee758697.aspx
  [Managekeys]: ./media/storage-manage-storage-account/Storage_ManageKeys.png
