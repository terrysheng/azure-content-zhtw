## 什麼是 Blob 儲存體

Azure Blob 儲存體是一項儲存大量非結構化資料的服務 (例如文字或二進位資料)，全球任何地方都可透過 HTTP 或 HTTPS 來存取這些資料。您可以使用 Blob 儲存體向全球公開資料，或私下儲存應用程式資料。

Blob 儲存體的一般用途包括：

-   直接提供映像或文件給瀏覽器
-   儲存檔案供分散式存取
-   串流傳輸視訊和音訊
-   執行安全備份和災害復原
-   儲存資料供內部部署或 Azure 託管服務進行分析

## Blob 服務概念

Blob 服務包含下列元件：

![Blob 1][Blob1]

-   **儲存體帳戶：**一律透過儲存體帳戶來存取 Azure 儲存體。如需關於儲存體帳戶容量的詳細資訊，請參閱＜[Azure 儲存體延展性和效能目標](storage-scalability-targets.md)＞(英文)。

-   **容器：**容器提供一組 Blob 的群組。所有 Blob 都必須放在容器中。一個帳戶可以包含的容器不限數量。容器可以儲存無限制的 Blob。

-   **Blob：**任何類型和大小的檔案。Azure 儲存體提供三種類型的 Blob：區塊 Blob、分頁 Blob 及附加 Blob。
    
	*區塊 Blob* 很適合儲存文字或二進位檔案，例如文件和媒體檔案。*附加 Blob* 和區塊 Blob 相似，但已針對附加作業最佳化，因此適合用於記錄的情況。單一區塊 blob 或附加 blob 可包含高達 50,000 個區塊，每個區塊最大為 4 MB，大小總計稍高於 195 GB (4 MB X 50,000)。
    
	*分頁 Blob* 大小可達 1 TB，且在頻繁進行讀寫操作時更有效率。Azure 虛擬機器會使用分頁 Blob 作為作業系統和資料磁碟。

	如需關於 Blob 的詳細資訊，請參閱[了解區塊 Blob、分頁 Blob 及附加 Blob](https://msdn.microsoft.com/library/azure/ee691964.aspx)。

## 命名與參考容器和 Blob

您可以使用下列 URL 格式，在您的儲存體帳戶中定址 Blob：
   
    http://<storage-account-name>.blob.core.windows.net/<container-name>/<blob-name>  
      
例如，以下是上圖中定址其中一個 Blob 的 URL：

    http://sally.blob.core.windows.net/movies/MOV1.AVI

### 容器命名規則

容器名稱必須是有效的 DNS 名稱，且符合下列規則：

- 容器名稱必須是全小寫。
- 容器名稱必須以字母或數字開頭，並且只能包含字母、數字和虛線 (-) 字元。
- 每個虛線 (-) 字元前後必須立即接著字母或數字；容器名稱中不允許連續虛線。
- 容器名稱必須介於 3 至 63 個字元長。

### Blob 命名規則

Blob 名稱必須符合下列規則：

- Blob 名稱可包含任何字元的組合。
- Blob 名稱必須至少一個字元長，而且不能超過 1,024 個字元。
- Blob 名稱會區分大小寫。
- 保留的 URL 字元必須正確逸出。
- Blob 名稱中所包含的路徑線段數目不能超過 254 個。路徑線段是與虛擬目錄名稱對應的連續分隔符號字元 (*例如*，正斜線 '/') 之間的字串。

Blob 服務是以一般儲存體配置為根據。您可以透過在 Blob 名稱內指定字元或字串分隔符號建立虛擬階層。例如，下列清單顯示一些有效且唯一的 Blob 名稱：

	/a
	/a.txt
	/a/b
	/a/b.txt

您可以使用分隔符號字元，以階層方式列出 Blob。


[Blob1]: ./media/storage-blob-concepts-include/blob1.jpg

<!---HONumber=August15_HO6-->