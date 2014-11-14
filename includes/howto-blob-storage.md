## <a name="what-is"> </a>什麼是 Blob 儲存體

Azure Blob 儲存體是一項儲存大量非結構化資料的服務 (例如文字或二進位資料)，
全球任何地方都可透過 HTTP 或 HTTPS
來存取這些資料。您可以使用 Blob 儲存體向全球公開資料，
或私下儲存應用程式資料。

Blob 儲存體的一般用途包括：

-   直接提供映像或文件給瀏覽器
-   儲存檔案供分散式存取
-   串流傳輸視訊和音訊
-   執行安全備份和災害復原
-   儲存資料供內部部署或 Azure 託管服務進行分析

## <a name="concepts"> </a> 概念

Blob 服務包含下列元件：

![Blob 1][Blob 1]

-   **儲存體帳戶：** 一律透過儲存體帳戶來存取 Azure 儲存體。
    如需關於儲存體帳戶容量的詳細資訊，請參閱＜[Azure 儲存體延展性和效能目標][Azure 儲存體延展性和效能目標]＞(英文)。

-   **容器：** 容器提供一組 Blob 的群組方式。
    所有 Blob 都必須放在容器中。一個帳戶可以包含的
    容器不限數量。容器可以儲存
    無限制的 Blob。

-   **Blob：**任何類型和大小的檔案。Azure 儲存中
    可以儲存兩種 Blob：區塊和分頁 Blob。
    大部分檔案都是區塊 Blob。單一區塊 Blob 的大小上限為
    200GB。本教學課程使用區塊 Blob。分頁 Blob (另一種 Blob 類型)
    的大小上限為 1TB，當檔案中的位元組範圍經常修改時，
    分頁 Blob 的效率較高。如需關於 Blob 的詳細資訊，
    請參閱 [了解區塊 Blob 和分頁 Blob]。

-   **URL 格式：** 可利用下列 URL 格式來
    定址 Blob：
    http://`<storage account>`.blob.core.windows.net/`<container>`/`<blob>`

    下列範例 URL 可用來定址上圖的
    其中一個 Blob：
    `http://sally.blob.core.windows.net/movies/MOV1.AVI`

  [Blob 1]: ./media/howto-blob-storage/blob1.jpg
  [Azure 儲存體延展性和效能目標]: http://msdn.microsoft.com/zh-tw/library/dn249410.aspx
