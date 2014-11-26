<properties title="Search Service: workflow for developers" pageTitle="Search Service: workflow for developers" description="Search Service: workflow for developers" metaKeywords="" services="" solutions="" documentationCenter="" authors="heidist" videoId="" scriptId="" />

# Azure 搜尋：開發工作流程

[WACOM.INCLUDE [本文使用 Azure 預覽入口網站](../includes/preview-portal-note.md)]

本文提供藍圖以及一些最佳方法，供您在建立與維護搜尋服務和其索引時使用。

我們假設您已佈建此服務。如果您尚未進行佈建，請參閱[在 Azure 預覽入口網站中設定搜尋][在 Azure 預覽入口網站中設定搜尋]以開始作業。

-   [步驟 1：建立索引][步驟 1：建立索引]
-   [步驟 2：新增文件][步驟 2：新增文件]
-   [步驟 3：查詢索引][步驟 3：查詢索引]
-   [步驟 4：上傳或刪除索引及文件][步驟 4：上傳或刪除索引及文件]
-   [儲存體設計考量][儲存體設計考量]

## 步驟 1：建立索引

針對包含搜尋資料和屬性的搜尋索引進行查詢 (至少是非系統查詢)。在此步驟中，您會以 JSON 格式定義索引的結構描述，並執行 HTTPS PUT 要求在服務中建立索引。

一般來說，索引會以程式碼的形式存在您的本機開發環境中。索引定義中也沒有內建工具或編輯器。如需建立索引的詳細資訊，請參閱 MSDN 上的[建立索引 (Azure 搜尋 API)][建立索引 (Azure 搜尋 API)]。

## 步驟 2：新增文件

建立搜尋索引後，您可以 POST 文件為 JSON 格式並加入索引中。每個文件必須具有唯一索引鍵，以及包含可搜尋和不可搜尋資料的欄位集合。文件資料代表一組索引鍵值組。

我們建議您批次新增文件以提高輸送量。假設文件平均大小為 1-2 KB，您可以批次處理最多 10,000 份文件。

針對 POST 要求，會有一個整體狀態碼。如果有組合成功和失敗的文件，則狀態碼為 HTTP 200 (成功) 或 HTTP 207 (多狀態)。除了針對 POST 要求的的狀態碼，Azure 搜尋會維護每個文件的狀態欄位。針對批次上傳，您需要一個方法來取得指出每個文件插入成功或失敗的文件專屬狀態。狀態欄會提供這些資訊。如果文件載入失敗，將設定為「false」。

負載量大時，出現一些上傳錯誤是正常的。如果出現此現象，整體狀態碼為 207，代表部分成功，而建立索引失敗之文件的「狀態」屬性會設定為「false」。

> [WACOM.NOTE] 當服務接收文件時，文件會排入佇列以等待建立索引，而且可能不會立即納入搜尋結果中。如果不是在負載量大的情況下，一般來說，文件會在幾秒鐘內完成索引建立。

## 步驟 3：查詢索引

文件建立索引後，您即可執行搜尋查詢。您可以使用 OData 或簡單的查詢語法，一次查詢一個索引：

-   [適用於 Azure 搜尋的 OData 運算式語法][適用於 Azure 搜尋的 OData 運算式語法]
-   [Azure 搜尋中的簡單查詢語法][Azure 搜尋中的簡單查詢語法]

## 步驟 4：上傳或刪除索引及文件

您可以選擇將結構描述變更為搜尋索引、從索引內部更新或刪除文件，以及刪除索引。

更新索引時，您可以將多個動作 (插入、合併、刪除) 結合在同個批次中，以排除反覆存取的時間。Azure 搜尋目前不支援部份更新 (HTTP PATCH)，因此如果要更新索引，您必須重新傳送索引定義。

## 儲存體設計考量

許多搜尋應用程式會根據不同應用程式的需求而使用多種儲存格式。Azure 搜尋具備的內部儲存體必需用來儲存索引和文件。文字分析的執行取決於是否具有隨手可得的所有可搜尋欄位和相關聯屬性。

文件中的欄位並非都是可搜尋的。例如，如果您的應用程式是音樂和影片的線上目錄，我們建議您將二進位檔儲存在 BLOB 或一些其他儲存體中。二進位檔本身並不可搜尋，因此並不需要將其存在 Azure 搜尋儲存體中。您應該將影像、影片、聲音檔存在其他服務或位置中，而將連結到檔案位置的 URL 存在文件中的欄位裡。

如需建立索引或文件的詳細資訊，請參閱 [Azure 搜尋 Rest API][Azure 搜尋 Rest API]。

<!--Anchors-->
<!--Image references-->
<!--Link references-->

  [在 Azure 預覽入口網站中設定搜尋]: ../search-configure/
  [步驟 1：建立索引]: #sub-1
  [步驟 2：新增文件]: #sub-2
  [步驟 3：查詢索引]: #sub-3
  [步驟 4：上傳或刪除索引及文件]: #sub-4
  [儲存體設計考量]: #sub-5
  [建立索引 (Azure 搜尋 API)]: http://msdn.microsoft.com/zh-tw/library/dn798941.aspx
  [適用於 Azure 搜尋的 OData 運算式語法]: http://msdn.microsoft.com/zh-tw/library/dn798921.aspx
  [Azure 搜尋中的簡單查詢語法]: http://msdn.microsoft.com/zh-tw/library/dn798920.aspx
  [Azure 搜尋 Rest API]: http://msdn.microsoft.com/zh-tw/library/dn798935.aspx
