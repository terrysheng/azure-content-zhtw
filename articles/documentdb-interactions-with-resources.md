<properties title="Interact with DocumentDB resources" pageTitle="與 DocumentDB 資源互動 | Azure" description="DocumentDB 提供 .NET、Python、Node.js 和 JavaScript 的用戶端 SDK，而這些 SDK 全是透過基礎 REST API 的簡單包裝函式。" metaKeywords="" services="documentdb" solutions="data-management"  authors="bradsev" manager="jhubbard" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/20/2014" ms.author="spelluru" />

#與 DocumentDB 資源互動 
DocumentDB 提供透過 HTTP 的簡單且開放 RESTful 程式設計模型。在其預覽版本中，DocumentDB 提供 .NET、Python、Node.js 和 JavaScript 的用戶端 SDK，而這些 SDK 全是透過基礎 REST API 的簡單包裝函式。在未來的版本中，還會提供 C++ 和 Java SDK。我們已公開我們的 SDK，希望您也為自己特有的程式設計環境撰寫專屬 SDK，並將它分享給社群使用。 

>[AZURE.NOTE] 此外，它也提供高效率的 TCP 通訊協定，此 TCP 通訊協定在通訊模型中也符合 REST 限制，並且可以透過 .NET 用戶端 SDK 取得。  

##資源
DocumentDB 所管理的實體稱為「**資源**」，可透過其邏輯 URI 來唯一識別。開發人員可以使用標準 HTTP 動詞命令、要求/回應標頭和狀態碼來與資源互動。如下圖所示，DocumentDB 的「**資源模型**」是由資料庫帳戶下轄的一組資源所組成，而每個資源都可以透過邏輯和穩定 URI 來定址。在本文件中，一組資源稱為「**摘要**」。  

![][1]  

##資料庫帳戶的階層式資源模型 ##

做為使用 DocumentDB 的客戶，您一開始要先使用 Azure 訂用帳戶佈建 DocumentDB **資料庫帳戶**。資料庫帳戶是由一組「**資料庫**」所組成，每個資料庫都包含多個「**集合**」，而每個集合又依序包括**預存程序、觸發程序、UDF、文件**及相關**附件**。資料庫也有相關聯的**使用者**，每位使用者都有一組可存取其他各種集合、預存程序、觸發程序、UDF、文件或附件的**權限**。資料庫、使用者、權限和集合是具有已知結構描述的系統定義資源，而文件和附件則包含使用者定義的任意 JSON 內容。  

|資源 	|描述
|-----------|-----------
|資料庫帳戶	|資料庫帳戶會與一或多個代表所佈建文件儲存體和輸送量的容量單位、一組資料庫和 Blob 儲存體相關聯。您可以使用 Azure 訂用帳戶建立一或多個資料庫帳戶。
|資料庫	|資料庫是分割給多個集合之文件儲存體的邏輯容器。同時也是使用者容器。
|使用者	|用於權限範圍界定/分割的邏輯命名空間。 
|權限	|使用者的相關聯授權權杖，可讓使用者獲得授權以存取特定資源。
|集合	|集合是 JSON 文件和相關聯 JavaScript 應用程式邏輯的容器。
|預存程序	|以 JavaScript 撰寫的應用程式邏輯，可向集合註冊並透過交易方式在資料庫引擎內執行。
|觸發程式	|以 JavaScript 模型副作用撰寫的應用程式邏輯，其與插入、取代或刪除作業相關聯。
|UDF	|以 JavaScript 撰寫的無副作用應用程式邏輯。UDF 可讓您建立自訂查詢運算子的模型，進而擴充核心 DocumentDB 查詢語言。
|文件	|使用者定義的 (任意) JSON 內容。依照預設，不是不需要定義任何結構描述，就是需要提供所有新增至集合之文件的次要索引。
|附件	|附件是含有外部 Blob/媒體之參考和相關聯中繼資料的特殊文件。開發人員可以選擇讓 DocumentDB 管理 Blob，或使用外部 Blob 服務提供者 (例如 OneDrive、Dropbox 等) 儲存 Blob。 

###系統與使用者定義資源的比較
資料庫帳戶、資料庫、集合、使用者、權限、預存程序、觸發程序和 UDF 等資源的結構描述都是固定不變的，因此稱為「*系統資源*」。相反地，文件和附件等資源的結構描述沒有任何限制，並且是*使用者定義的資源*的範例。在 DocumentDB 中，系統定義的資源與使用者定義的資源都會以符合標準 JSON 的形式來表示和管理。所有資源 (不論是系統定義的還是使用者定義的) 都具有下列共同屬性。

>[AZURE.NOTE] 請注意，系統產生的資源屬性在以 JSON 形式表示時，前面都會加上底線 (_)。  


<table width="500"> 
<tbody>
<tr>
<td valign="top" ><p><b>屬性 </b></p></td>
<td valign="top" ><p><b>可由使用者設定或由系統產生？</b></p></td>
<td valign="top" ><p><b>目的</b></p></td>
</tr>

<tr>
<td valign="top" ><p>_rid</p></td>
<td valign="top" ><p>由系統產生</p></td>
<td valign="top" ><p>系統產生的唯一階層式資源識別碼</p></td>
</tr>

<tr>
<td valign="top" ><p>_etag</p></td>
<td valign="top" ><p>由系統產生</p></td>
<td valign="top" ><p>要控制開放式並行存取所需之資源的 etag</p></td>
</tr>

<tr>
<td valign="top" ><p>_ts</p></td>
<td valign="top" ><p>由系統產生</p></td>
<td valign="top" ><p>資源上次更新之時間的時間戳記</p></td>
</tr>

<tr>
<td valign="top" ><p>_self</p></td>
<td valign="top" ><p>由系統產生</p></td>
<td valign="top" ><p>資源的唯一可定址 URI </p></td>
</tr>

<tr>
<td valign="top" ><p>id</p></td>
<td valign="top" ><p>可由使用者設定</p></td>
<td valign="top" ><p>資源的使用者定義唯一名稱 </p></td>
</tr>

</tbody>
</table>


###以線路表示資源
DocumentDB 不會要求您提供用於 JSON 標準或特殊編碼的專屬延伸模組；DocumentDB 本身就能處理符合 JSON 標準的文件。  
 
###資源定址
所有資源都能以 URI 定址。資源的 **_self** 屬性值代表資源的相對 URI。URI 的格式是由 /<feed>/{_rid} 路徑片段所組成：  

|Value of the _self	|Description
|-------------------|-----------
|/dbs	|feed of databases under a database account
|/dbs/{_rid-db}	|Database with the unique id property with the value {_rid-db}
|/dbs/{_rid-db}/colls/	|feed of collections under a database 
|/dbs/{_rid-db}/colls/{_rid-coll}	|Collection with the unique id property with the value {_rid-coll}
|/dbs/{_rid-db}/users/	|feed of users under a database 
|/dbs/{_rid-db}/users/{_rid-user}	|User with the unique id property with the value {_rid-user}
|/dbs/{_rid-db}/users/{_rid-user}/permissions	|feed of permissions under a database 
|/dbs/{_rid-db}/users/{_rid-user}/permissions/{_rid-permission}	|Permission with the unique id property with the value {_rid-permission}  
  

資源也具有使用者定義的唯一名稱，並透過資源的 id 屬性公開。id 是由使用者定義且長度最多 256 個字元的字串，且在特定父系資源的內容中是唯一的。例如，給定集合內所有文件的 id 屬性值都是唯一的，但是在不同集合中則不一定如此。同樣地，給定使用者之所有權限的 id 屬性值是唯一的，但是在所有使用者中則不一定如此。_rid 屬性可用來建構資源的可定址 _self 連結。   

每個資源還會有系統產生的階層式資源識別碼 (也稱為 RID)，其可透過 _rid 屬性取得。RID 會對給定資源的整個階層進行編碼，此內部表示法十分方便，可透過分散方式強制執行參考完整性。RID 在資料庫帳戶內是唯一的，DocumentDB 會在內部使用，以進行有效率的路由，而不需要跨資料分割進行查閱。 

_self 和  _rid 屬性值都是用來表示資源的標準方法，並可互相替代。  
 
##基本互動模型
資源可支援下列 HTTP 動詞命令  及其標準解釋：

>[AZURE.NOTE] 日後，我們打算透過 PATCH 新增對於選擇性更新的支援  

1.	POST 方法會建立新的項目資源 
2.	GET 方法會讀取現有項目或摘要資源 
3.	PUT 方法會取代現有項目資源 
4.	DELETE 方法會刪除現有項目資源  
5.	HEAD 方法會進行無回應裝載 (例如只有標頭) 的取得 (GET)。

在未來的版本中，項目資源還將支援 PATCH 動詞命令。  

如下圖所示，POST 只能對「摘要」資源發出；PUT、DELETE 只能對「項目」資源發出；GET 和 HEAD 能對「摘要」或「項目」資源發出。 

![][2]  

**使用標準 HTTP 動詞命令的互動模型**

為了更加了解互動模型，我們來看一下建立新資源 (也稱為 INSERT) 的情況。為了建立新資源，您必須發出 HTTP POST 要求，而且此要求的本文內要包含以資源所屬容器摘要的 URI 表示資源的程式碼。此要求唯一的必要屬性是資源識別碼。  

舉例來說，為了建立新資料庫，您需要對 /dbs 發佈 (POST) 資料庫資源 (透過設定具有唯一名稱的 id 屬性)，同樣地，為了建立新集合，您需要對 /dbs/_rid/colls/ 發佈 (POST) 集合資源，以此類推。回應將會包含具有系統所產生屬性 (包括可供導覽到其他資源的 _self 資源連結) 的完全認可資源。以下是簡單的 HTTP 互動模型範例，用戶端可以發出 HTTP 要求，以在帳戶內建立新資料庫：  

	POST http://fabrikam.documents.azure.net/dbs
	{
	      "id":"MyDb"
	}
DocumentDB 的回應是產生成功回應和狀態碼，指出已成功建立資料庫。  

	[201 Created]
	{
	      "id": "MyDb",
	      "_rid": "UoEi5w==",
	      "_self": "dbs/MyDb/",
	      "_ts": 1407370063,
	      "_etag": "\"00002100-0000-0000-0000-50e71fda0000\"",
	      "_colls": "colls/",
	      "_users": "users/"
	}

DocumentDB 服務的回應是產生成功回應和狀態碼，指出已成功建立資料庫。  

再來看另一個建立和執行資源的範例，下列預存程序完全是以 JavaScript 撰寫。   

	function sproc(docToCreate, addedPropertyName, addedPropertyValue) {
	    var collectionManager = getContext().getCollection();
	    collectionManager.createDocument(collectionManager.getSelfLink(), docToCreate,   
	    function(err, docCreated) {
	        if(err) throw new Error('Error while creating document: ' + err.message);
	        
	        docCreated.addedPropertyName = addedPropertyValue;
	        getContext().getResponse().setBody(docCreated);
	    });
	}

對 /dbs/_rid-db/colls/_rid-coll/sprocs 發出 POST，即可向 MyDb 下轄的集合註冊預存程序。 

	POST /dbs/MyDb/colls/MyColl/sprocs HTTP/1.1
	
	{
	  "id": "sproc1",
	  "body": "function (docToCreate, addedPropertyName, addedPropertyValue) {
	                var collectionManager = getContext().getCollection();
	                collectionManager.createDocument(collectionManager.getSelfLink(), 
	                            docToCreate, function(err, docCreated) {
	                    if(err) throw new Error('Error while creating document: ' + 
	                                                     err.message);
	                    
	                    docCreated.addedPropertyName = addedPropertyValue;
	                    getContext().getResponse().setBody(docCreated);
	                });
	            }"
	}
DocumentDB 服務的回應是產生成功回應和狀態碼，指出已成功註冊預存程序。  

	[201 Created]
	{
	      "id": "sproc1",
	      "_rid": "EoEi5w==",
	      "_self": "dbs/MyDb/colls/MyColl/sprocs/sproc1",
	      "_etag": "\"00002100-0000-0000-0000-50f71fda0000\"",
	       ...
	}

最後，若要在上述範例中執行預存程序，則一個必須對預存程序資源的 URI 發出 POST(/dbs/_rid-db/colls/_rid-coll/sprocs/sproc1)。其程式碼如下所示：  

	POST /dbs/MyDb/colls/MyColl/sprocs/sproc1 HTTP/1.1
	 [ { "id": "TestDocument", "book": "Autumn of the Patriarch"}, "Price", 200 ]
 

DocumentDB 服務將做出下列回應：  

	HTTP/1.1 200 OK
	 { 
	  "id": "TestDocument",  
	  "_rid": "ZTlcANiwqwIBAAAAAAAAAA==",
	  "_ts": 1407370063,
	  "_self": "dbs/ZTlcAA==/colls/ZTlcANiwqwI=/docs/ZTlcANiwqwIBAAAAAAAAAA==/",
	  "_etag": "00000900-0000-0000-0000-53e2c34f0000",
	  "_attachments": "attachments/",
	  "book": "Autumn of the Patriarch", 
	  "price": 200
	}

請注意，POST 動詞命令可用於建立新資源、執行預存程序或發出 SQL 查詢。為了說明 SQL 查詢如何執行，請看下列程式碼：  

	POST /dbs/MyDb/colls/MyColl/docs HTTP/1.1
	...
	x-ms-documentdb-isquery: True
	Content-Type: application/sql
	
	SELECT * FROM root.children

服務會回應 SQL 查詢的結果。   

	HTTP/1.1 200 Ok
	x-ms-activity-id: 83f9992c-abae-4eb1-b8f0-9f2420c520d2
	x-ms-item-count: 2
	x-ms-session-token: 81
	x-ms-request-charge: 1.43
	Content-Length: 287
	
	{"_rid":"sehcAIE2Qy4=","Documents":[[{"firstName":"Henriette Thaulow","gender":"female","grade":5,"pets":[{"givenName":"Fluffy"}]}],[{"familyName":"Merriam","givenName":"Jesse","gender":"female","grade":1},{"familyName":"Miller","givenName":"Lisa","gender":"female","grade":8}]],"count":2}


取代或讀取資源分別等同於對資源的 _self 連結發出 PUT (具有有效要求本文) 和 GET 動詞命令。同樣地，刪除資源等同於對資源的 _self 連結發出 DELETE 動詞命令。必須指出的一點是，要想在 DocumentDB 的資源模型中以階層方式組織資源，就必須支援串聯刪除功能，以便只要刪除擁有者的資源就能刪除相依資源。相依資源可能會散佈在擁有者資源以外的其他節點，因此，刪除作業可能進展緩慢。不論記憶體回收的機制為何，資源一經刪除，就會立即釋出配額供您使用。請注意，系統會保留參考完整性。例如，您不可以將集合插入至已刪除的資料庫，或者取代或查詢不再存在之集合的文件。  
 
對資源摘要發出 GET 或是查詢集合都可能會導致產生數百萬個項目，因此這兩部伺服器根本不可能有辦法顯示這些項目，用戶端也不可能在一次往返/要求和回應交換內就完全取用這些項目。為了解決此問題，DocumentDB 允許用戶端以一次一頁的方式對大型摘要進行分頁處理。用戶端可以使用 [x-ms-continuationToken] 回應標頭做為資料指標，以導覽到下一頁。   

##開放式並行存取控制
大部分的 Web 應用程式都依賴以實體標記為基礎的開放式並行存取控制，以避免令人厭煩的「遺失更新」和「遺失刪除」問題。實體標記是支援 HTTP 且與資源相關聯的邏輯時間戳記。DocumentDB 透過確定每個 HTTP 回應都 (持久) 包含與特定資源相關聯的版本，原生支援開放式並行存取控制。在下列情況時，可正確偵測到並行存取控制衝突：  

1.	如果兩個用戶端同時以最新版本的資源 (透過 [if-match] 要求標頭來指定) 對資源發出變更要求 (透過 PUT/DELETE 動詞命令)，則 DocumentDB 資料庫引擎會讓它們遵循交易式並行存取控制。
2.	如果用戶端提出舊版資源 (透過 [if-match] 要求標頭來指定)，則會拒絕其要求。  

##連線選項
在 DocumentDB 所公開的邏輯定址模型中，每個資源都具有可透過其 _self 連結來加以識別的邏輯和穩定 URI。做為遍布多個區域的分散式儲存體系統，DocumentDB 中的各種資料庫帳戶所下轄的資源會分割給多部電腦，並且會複寫每個資料分割以提供高可用性。管理給定資料分割資源的複本會註冊實體位址。雖然實體位址在一段時間後就會因為失敗而有所變更，但是其邏輯位址仍會維持不變。同時以資源形式在內部提供使用的路由表，會保存將邏輯位址轉譯為實體位址的方法。DocumentDB 公開兩種連線模式：  

1.	**閘道模式：**用戶端不會接觸到邏輯位址與實體位址的轉換過程或是路由細節；他們只會處理邏輯 URI，並以符合 REST 限制的方式導覽資源模型。用戶端會使用邏輯 URI 發出要求，而 Edge 機器會將此邏輯 URI 轉譯成可管理資源並轉送要求的複本實體位址。 有了 Edge 機器快取 (和定期重新整理) 路由表，路由將會很有效率。 
2.	**直接連線模式：**用戶端直接在其程序空間中管理路由表，並定期進行重新整理。用戶端可以略過 Edge 電腦，直接與複本連線。   



<table width="300">
    <tbody>
        <tr>
            <td width="120" valign="top">
                <p>
                    <strong>連線模式</strong>
                </p>
            </td>
            <td width="66" valign="top">
                <p>
                    <strong>通訊協定</strong>
                </p>
            </td>
            <td width="264" valign="top">
                <p>
                    <strong>詳細資料</strong>
                </p>
            </td>
            <td width="150" valign="top">
                <p>
                    <strong>DocumentDB SDK</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="120" valign="top">
                <p>
                    閘道
                </p>
            </td>
            <td width="66" valign="top">
                <p>
                    HTTPS
                </p>
            </td>
            <td width="264" valign="top">
                <p>
                    應用程式使用邏輯 URI 直接與 Edge 節點連線。這會發生額外的網路躍點。
                </p>
            </td>
            <td width="150" valign="top">
                <p>
                    REST API
                </p>
                <p>
                    .NET、JavaScript、Node.js、Python
                </p>
            </td>
        </tr>
        <tr>
            <td width="120" valign="top">
                <p>
                    直接連線
                </p>
            </td>
            <td width="66" valign="top">
                <p>
                    HTTPS 和
                </p>
                <p>
                    TCP
                </p>
            </td>
            <td width="264" valign="top">
                <p>
                    應用程式可以直接存取路由表，並執行用戶端路由直接與複本連線。
                </p>
            </td>
            <td width="150" valign="top">
                <p>
                    .NET
                </p>
            </td>
        </tr>
    </tbody>
</table>

##參考
-	REST (英文) [http://en.wikipedia.org/wiki/Representational_state_transfer](http://en.wikipedia.org/wiki/Representational_state_transfer)
-	JSON 規格 (英文)  [http://-www.ietf.org/rfc/rfc4627.txt](http://-www.ietf.org/rfc/rfc4627.txt)
-	HTTP 規格 (英文) [http://www.w3.org/Protocols/rfc2616/rfc2616.html](http://www.w3.org/Protocols/rfc2616/rfc2616.html)
-	實體標記 (英文) [http://en.wikipedia.org/wiki/HTTP_ETag](http://en.wikipedia.org/wiki/HTTP_ETag)
-	[查詢 DocumentDB](/documentation/articles/documentdb-sql-query/)
-	[DocumentDB SQL 參考](http://go.microsoft.com/fwlink/p/?LinkID=510612)
-	[DocumentDB 程式設計：預存程序、觸發程序和 UDF](/documentation/articles/documentdb-programming/)
-	[DocumentDB 參考文件](http://go.microsoft.com/fwlink/p/?LinkID=402384)


[1]: ./media/documentdb-interactions-with-resources/interactions-with-resources1.png
[2]: ./media/documentdb-interactions-with-resources/interactions-with-resources2.png
