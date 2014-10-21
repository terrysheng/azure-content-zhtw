<properties title="Monitor a DocumentDB Account" pageTitle="Manage a DocumentDB account | Azure" description="Learn how to manage your DocumentDB account." metaKeywords="NoSQL, DocumentDB,  database, document-orientated database, JSON, accounts" services="documentdb" solutions="data-management" documentationCenter=""  authors="hawong" manager="jhubbard" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/03/2014" ms.author="hawong"></tags>

# 如何管理 DocumentDB 帳戶

## 目錄

-   [作法：檢視、複製和重新產生 DocumentDB 存取金鑰][作法：檢視、複製和重新產生 DocumentDB 存取金鑰]
-   [作法：管理 DocumentDB 一致性設定][作法：管理 DocumentDB 一致性設定]
-   [作法：管理 DocumentDB 容量設定][作法：管理 DocumentDB 容量設定]
-   [作法：刪除 DocumentDB 帳戶][作法：刪除 DocumentDB 帳戶]
-   [後續步驟][後續步驟]

## <span id="keys"></span></a>作法：檢視、複製和重新產生存取金鑰

當您建立 DocumentDB 帳戶時，系統會產生兩個主要存取金鑰，作為存取 DocumentDB 帳戶時的驗證憑藉。透過提供這兩個存取金鑰，DocumentDB 讓您可以重新產生金鑰，同時又不需中斷 DocumentDB 帳戶。

在 [Azure 管理預覽入口網站][Azure 管理預覽入口網站][][]中，從 [DocumentDB 帳戶] 分頁存取 [金鑰] 部分，即可檢視、複製及重新產生存取 DocumentDB 帳戶所用的存取金鑰。

![][]

### 檢視及複製存取金鑰

1.      在 [Azure 管理預覽入口網站][Azure 管理預覽入口網站]中存取 DocumentDB 帳戶。

2.      在 [摘要] 透鏡中按一下 [金鑰]。

3.      在 [金鑰] 分頁中，按一下要複製之金鑰旁的 [複製] 按鈕。

4.      按 Ctrl+C 以複製金鑰。

  ![][1]

### 重新產生存取金鑰

您應定期變更 DocumentDB 帳戶的存取金鑰，讓連線更加安全。指派的兩個存取金鑰可讓您在重新產生一個存取金鑰的同時，使用另一個存取金鑰維持 DocumentDB 帳戶連線。

**警告**

重新產生存取金鑰會影響任何相依於目前金鑰的應用程式。所有使用存取金鑰來存取 DocumentDB 帳戶的用戶端，都必須更新為使用新的金鑰。

如果您有應用程式或雲端服務正在使用 DocumentDB 帳戶，除非您變換金鑰，否則會在重新產生金鑰後失去連線。以下是供您遵循的程序：

1.      更新應用程式程式碼中的存取金鑰，以參考 DocumentDB 帳戶的次要存取金鑰。

2.      重新產生 DocumentDB 帳戶的主要存取金鑰。在 [Azure 管理預覽入口網站][Azure 管理預覽入口網站]中存取 DocumentDB 帳戶。

3.      在 [摘要] 透鏡中按一下 [金鑰]。

4.      在 [金鑰] 分頁中按一下 [Regenerate Primary] 命令，然後按一下[確定] 以確認要產生新的金鑰。

5.      一旦確認新的金鑰可供使用後 (重新產生後約 5 分鐘)，更新應用程式程式碼中的存取金鑰，以參考新的主要存取金鑰。

6.      重新產生次要存取金鑰。

*請注意，新產生的金鑰可能需要經過幾分鐘之後才能用來存取 DocumentDB 帳戶。*

## <span id="consistency"></span></a>作法：管理 DocumentDB 一致性設定

DocumentDB 支援四個定義完善的使用者可設定資料一致性層級，使開發人員得以在預測範圍內針對一致性、可用性和延遲做出適當取捨。

·         **增強式**一致性可保證讀取作業一律傳回上次寫入的值。

·         **界限-陳舊**一致性可保證讀取內容並未過時太久。它尤其能保證讀取內容不比上次寫入的版本舊 K 個版本。

·         **工作階段**一致性不僅可保證單純讀取 (先讀取舊資料，接著讀取新資料，然後再讀取舊資料的情況絕對不會發生) 和單純寫入 (依序寫入)，還能保證所讀取的寫入內容不論從哪個用戶端的角度來看都是最新的。

·         **最終**一致性可保證讀取作業一律讀取寫入內容的有效子集，且最終會趨於一致。

*請注意，系統預設會為 DocumentDB 帳戶佈建「工作階段」層級一致性。如需 DocumentDB 一致性設定的詳細資訊，請參閱[一致性層級][一致性層級] (英文) 一節。*

### 指定 DocumentDB 帳戶的預設一致性

1.      在 [Azure 管理預覽入口網站][Azure 管理預覽入口網站]中存取 DocumentDB 帳戶。

2.      在 [組態] 透鏡中按一下 [一致性]。

3.      在 [預設一致性] 分頁中，選取 DocumentDB 帳戶的預設一致性
層級。

4.      按一下 [儲存]。

5.      您可以透過 Azure 管理預覽入口網站的通知中心監視作業進度。

![][2]

![][3]

*請注意，整個 DocumentDB 帳戶的預設一致性設定變更可能需要
幾分鐘的時間才會生效。*

## <span id="capacity"></span></a>作法：管理 DocumentDB 容量設定

Microsoft Azure DocumentDB 可讓您在應用程式的生命週期內，隨著應用程式需求的變更彈性地調整。DocumentDB的調整，可藉由透過 Azure 管理預覽入口網站增加 DocumentDB 資料庫帳戶的容量來實現。

當您建立資料庫帳戶時，系統會為其佈建資料庫儲存體和預留的輸送量。您可以隨時在 Azure 管理預覽入口網站中新增或移除容量單位，藉此變更對帳戶佈建的資料庫儲存體和輸送量。

### 新增或移除容量單位

1.      在 [Azure 管理預覽入口網站][Azure 管理預覽入口網站]中存取 DocumentDB 帳戶。

2.      在 [流量] 透鏡中按一下 [調整]。

3.      在 [調整] 分頁中，指定 DocumentDB 帳戶的容量單位數目。

4.      按一下 [儲存] (請注意，調整作業可能需要幾分鐘的時間才能完成，您可以透過 Azure 管理預覽入口網站的通知中心監視作業進度)。

請注意，對於每個 DocumentDB 帳戶，DocumentDB Preview 最多支援 5 個容量單位。


![][4]

 

## <span id="delete"></span></a>作法：刪除 DocumentDB 帳戶

若要移除不再使用的 DocumentDB 帳戶，請使用 [DocumentDB 帳戶] 分頁的 [刪除] 命令。

**警告**

在預覽版本中，您無法還原已刪除之 DocumentDB 帳戶的內容。刪除 DocumentDB 帳戶將會刪除帳戶的所有資源，包括資料庫、集合、文件及附件。


![][5]

1.      在 [Azure 管理預覽入口網站][Azure 管理預覽入口網站]中存取要刪除的 DocumentDB 帳戶。

2.      在帳戶分頁中按一下 [刪除] 命令。

3.      在後續的確認分頁中輸入 DocumentDB 帳戶名稱，確認您想要刪除該帳戶。

4.      在確認分頁中按一下 [刪除] 按鈕。

## <span id="next"></span></a>後續步驟

-   了解如何[開始使用 DocumentDB 帳戶][開始使用 DocumentDB 帳戶]。
-   若要深入了解 DocumentDB，請參閱 [azure.com][azure.com] 上的
    Azure DocumentDB 文件。

 

  [作法：檢視、複製和重新產生 DocumentDB 存取金鑰]: #keys
  [作法：管理 DocumentDB 一致性設定]: #consistency
  [作法：管理 DocumentDB 容量設定]: #capacity
  [作法：刪除 DocumentDB 帳戶]: #delete
  [後續步驟]: #next
  [Azure 管理預覽入口網站]: https://portal.azure.com/
  []: http://manage.windowsazure.com
  []: ./media/documentdb-manage-account/image002.jpg
  [1]: ./media/documentdb-manage-account/image004.jpg
  [一致性層級]: http://go.microsoft.com/fwlink/p/?LinkId=402365
  [2]: ./media/documentdb-manage-account/image005.png
  [3]: ./media/documentdb-manage-account/image006.png
  [4]: ./media/documentdb-manage-account/image007.png
  [5]: ./media/documentdb-manage-account/image009.png
  [開始使用 DocumentDB 帳戶]: http://go.microsoft.com/fwlink/p/?LinkId=402364
  [azure.com]: http://go.microsoft.com/fwlink/?LinkID=402319&clcid=0x409
