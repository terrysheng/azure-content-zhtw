<properties 
	pageTitle="搜尋服務：開發人員的工作流程" 
	description="搜尋服務：開發人員的工作流程" 
	services="search" 
	documentationCenter="" 
	authors="HeidiSteen" 
	manager="mblythe" 
	editor=""/>

<tags 
	ms.service="search" 
	ms.devlang="rest-api" 
	ms.workload="search" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.date="01/16/2015" 
	ms.author="heidist"/>

# Azure 搜尋：開發工作流程

[WACOM.INCLUDE [This article uses the Azure Preview portal](../includes/preview-portal-note.md)]

本文提供藍圖以及一些最佳方法，供您在建立與維護搜尋服務和其索引時使用。 

我們假設您已佈建此服務。如果尚未執行該作業，請參閱[開始使用 Azure Search](../search-get-started/)，以取得進一步指示。

+ [步驟 1：建立索引](#sub-1)
+ [步驟 2：新增文件](#sub-2)
+ [步驟 3：查詢索引](#sub-3)
+ [步驟 4：上傳或刪除索引及文件](#sub-4)
+ [儲存體設計考量](#sub-5)


<h2 id="sub-1">步驟 1：建立索引</h2>

查詢會以包含搜尋資料和屬性的搜尋索引為目標。此情況下，您佈建服務後的第一個步驟就是以 JSON 格式定義索引結構描述，並執行 HTTPS PUT 要求以在服務中建立索引。 

應用程式程式碼會建構索引。沒有內建工具或編輯器可協助您在使用者介面中定義索引。示範各種建構索引方式的範例包括[使用 Azure Search 建立第一個搜尋解決方案](../search-create-first-solution/) (其中結構描述已在 Program.cs 檔案中指定) 及[開始使用 Azure Search 中的評分設定檔](../search-get-started-scoring-profiles) (在獨立 JSON 結構描述檔案中提供索引)。如需深入了解如何建立索引，請參閱 MSDN 上的[建立索引 (Azure Search API)](http://msdn.microsoft.com/library/dn798941.aspx)。

<h2 id="sub-2">步驟 2：新增文件</h2>

建立搜尋索引後，您可以 POST 文件為 JSON 格式並加入索引中。每個文件必須具有唯一索引鍵，以及包含可搜尋和不可搜尋資料的欄位集合。文件資料代表一組索引鍵值組。

我們建議您批次新增文件以提高輸送量。假設文件平均大小為 1-2 KB，您可以批次處理最多 1,000 份文件。

針對 POST 要求，會有一個整體狀態碼。如果有組合成功和失敗的文件，則狀態碼為 HTTP 200 (成功) 或 HTTP 207 (多狀態)。除了針對 POST 要求的的狀態碼，Azure 搜尋會維護每個文件的狀態欄位。針對批次上傳，您需要一個方法來取得指出每個文件插入成功或失敗的文件專屬狀態。狀態欄會提供這些資訊。如果文件載入失敗，將設定為 false。

負載量大時，出現一些上傳錯誤是正常的。如果出現此現象，整體狀態碼為 207，代表部分成功，而建立索引失敗之文件的「狀態」屬性會設定為 false。

> [WACOM.NOTE] 當服務接收文件時，文件會排入佇列以等待建立索引，而且可能不會立即納入搜尋結果中。如果不是在負載量大的情況下，一般來說，文件會在幾秒鐘內完成索引建立。


<h2 id="sub-3">步驟 3：查詢索引</h2>

文件建立索引後，您即可執行搜尋查詢。您可以使用 OData 或簡單的查詢語法，一次查詢一個索引：

+	[適用於 Azure Search 的 OData 運算式語法](http://msdn.microsoft.com/library/dn798921.aspx)
+	[Azure Search 中的簡單查詢語法](http://msdn.microsoft.com/library/dn798920.aspx)

<h2 id="sub-4">步驟 4：上傳或刪除索引及文件</h2>

您可以選擇將結構描述變更為搜尋索引、從索引內部更新或刪除文件，以及刪除索引。

更新索引時，您可以將多個動作 (插入、合併、刪除) 結合在同個批次中，以排除反覆存取的時間。Azure 搜尋目前不支援部份更新 (HTTP PATCH)，因此如果要更新索引，您必須重新傳送索引定義。

<h2 id="sub-5">儲存體設計考量</h2>

Azure Search 會將內部儲存體使用於搜尋作業中所用的索引和文件。文字分析和索引剖析的執行取決於是否具有隨手可得的所有可搜尋欄位和相關聯屬性。

文件中的欄位並非都是可搜尋的。例如，如果您的應用程式是音樂和影片的線上目錄，我們建議您將二進位檔儲存在 Azure BLOB 服務或一些其他儲存體格式中。二進位檔本身並不可搜尋，因此並不需要將其存在 Azure 搜尋儲存體中。雖然您應該將影像、視訊和音訊檔案存放在其他服務或位置，但您應該包含可參考檔案位置 URL 的欄位。如此一來，您即可傳回外部資料作為搜尋結果的一部分。 

如需建立索引或文件的詳細資訊，請參閱 [Azure Search Rest API](http://msdn.microsoft.com/library/dn798935.aspx)。


<!--Anchors-->
[步驟 1：建立索引]: #sub-1
[步驟 2：新增文件]: #sub-2
[步驟 3：查詢索引]: #sub-3
[步驟 4：上傳或刪除索引及文件]: #sub-4
[選擇文件存放區]: #sub-5


<!--Image references-->

<!--Link references-->
[開始使用 Azure Search]: ../search-get-started/
[在 Microsoft Azure 上管理搜尋服務]: ../search-manage/
[使用 Azure 搜尋建立您的第一個搜尋解決方案]: ../search-create-first-solution/


<!--HONumber=46--> 
