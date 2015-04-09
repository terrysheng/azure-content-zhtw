## 什麼是 Blob 儲存體

Azure Blob 儲存體是一種服務，用於儲存大量
非結構化的資料，例如文字或二進位資料，這些資料可以從世界各地，透過
HTTP 或 HTTPS 存取。您可以使用 Blob 儲存體公開地向世界公開資料，或
私下儲存應用程式資料。

Blob 儲存體的一般用途包括：

-   直接提供影像或文件給瀏覽器
-   儲存檔案供分散式存取
-   串流傳輸視訊和音訊
-   執行安全備份和災害復原
-   儲存資料供內部部署或 Azure 託管服務
    服務進行分析

## Blob 服務概念

Blob 服務包含下列元件：

![Blob1][Blob1]

-   **儲存體帳戶：**已透過儲存體帳戶，完成對 Azure 儲存體的所有存取
    。如需關於儲存體帳戶容量的詳細資訊，請參閱 [Azure 儲存體延展性和效能目標](http://msdn.microsoft.com/library/azure/dn249410.aspx)。

-   **容器：**容器提供一組 Blob 的群組。
    所有 Blob 都必須放在容器中。一個帳戶可以包含
    無限個容器。一個容器可以儲存
    無限個 Blob。

-   **Blob：**任何類型和大小的檔案。Blob 有兩種類型
    可以儲存在 Azure 儲存體中：區塊 Blob 和頁面 Blob。
    大部分檔案都是區塊 Blob。單一區塊 Blob 的大小上限為 200 GB
    。本教學課程使用區塊 Blob。頁面 Blob 是另一種 Blob
    類型，其大小上限可達 1 TB，經常修改檔案中的位元組範圍時，
    將更有效率。如需 Blob 的詳細資訊，
    請參閱[了解區塊 Blob 和頁面 Blob][]。

-   **URL 格式：**Blob 可以使用下列 URL 定址
    檔案：   
    http://`<storage
    account>`.blob.core.windows.net/`<container>`/`<blob>`  
      
    下列範例 URL 可用來定址上圖的其中一個
    Blob：  
    `http://sally.blob.core.windows.net/movies/MOV1.AVI`


[Blob1]: ./media/storage-blob-concepts-include/blob1.jpg


<!--HONumber=49-->