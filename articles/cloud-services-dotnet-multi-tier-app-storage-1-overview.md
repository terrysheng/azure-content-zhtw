<properties linkid="develop-net-tutorials-multi-tier-web-site-1-overview" pageTitle="Azure Cloud Service Tutorial: ASP.NET MVC Web Role, Worker Role, Azure Storage Tables, Queues, and Blobs" metaKeywords="Azure tutorial, Azure storage tutorial, Azure multi-tier tutorial, MVC Web Role tutorial, Azure worker role tutorial, Azure blobs tutorial, Azure tables tutorial, Azure queues tutorial" description="Learn how to create a multi-tier app using ASP.NET MVC and Azure. The app runs in a cloud service, with web role and worker roles, and uses Azure storage tables, queues, and blobs." metaCanonical="" services="cloud-services,storage" documentationCenter=".NET" title="Azure Cloud Service Tutorial: ASP.NET MVC Web Role, Worker Role, Azure Storage Tables, Queues, and Blobs" authors="tdykstra,riande" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="cloud-services" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="tdykstra,riande"></tags>

# Azure 雲端服務教學課程：ASP.NET MVC Web 角色、背景工作角色以及 Azure 儲存體資料表、佇列和 Blob - 1/5

本教學課程系列說明如何建立及部署在 Azure 雲端服務中執行，並使用 Azure 儲存體資料表、佇列和 Blob 的多層式 ASP.NET MVC Web 應用程式。您可以從 MSDN Code Gallery 下載[完整的應用程式][]，或從 TechNet E-Book Gallery 下載舊版的[電子書][]。

以下圖表顯示應用程式的這些部分的互動情況：

![電子郵件訊息處理][]

本教學課程系列分成 5 部分。如果您需要雲端服務、佇列和 Blob 的更快且更簡單的介紹，請參閱[開始使用 Azure 雲端服務和 ASP.NET][] (英文)。或者，您也可以在「網站」和 WebJobs 中執行多層式應用程式；如需詳細資訊，請參閱[開始使用 Azure WebJobs SDK][] (英文)。

在本教學課程系列中，您將了解：

-   如何安裝 Azure SDK 好讓電腦適合用於進行 Azure 開發。
-   如何建立含一個 ASP.NET MVC Web 角色與兩個背景工作角色的 Visual Studio 雲端專案。
-   如何將雲端專案發佈至 Azure 雲端服務。
-   如何使用 Azure 佇列儲存體服務進行各層之間或各背景工作角色之間的通訊。
-   如何使用 Azure 資料表儲存體服務做為結構化、非關聯式資料的高度可調整資料存放區。
-   如何使用 Azure Blob 服務將檔案儲存在雲端中。
-   如何使用 Visual Studio 伺服器總管來檢視及編輯 Azure 資料表、佇列和 Blob。
-   如何使用 SendGrid 傳送電子郵件。
-   如何設定追蹤和檢視追蹤資料。
-   如何增加背景工作角色執行個體數目來調整應用程式。

## <a name="toc"></a>本系列中的教學課程

本系列有五個教學課程：

1.  **Azure 電子郵件服務應用程式簡介** (本教學課程)。深入了解應用程式和其架構。如果您只想查看如何部署，或想查看程式碼，您可以略過這個教學課程，稍後再回頭進一步了解架構。
2.  [設定及部署 Azure 電子郵件服務應用程式][]。如何下載範例應用程式，並加以設定、在本機測試、部署，以及在雲端測試。
3.  [建置 Azure 電子郵件服務應用程式的 Web 角色][]。如何建置應用程式的 MVC 元件，並在本機加以測試。
4.  [建置 Azure 電子郵件服務應用程式的背景工作角色 A (電子郵件排程器)][]。如何建置後端元件 (來建立傳送電子郵件時所需的佇列工作項目)，並在本機加以測試。
5.  [建置 Azure 電子郵件服務應用程式的背景工作角色 B (電子郵件寄件者)][]。如何建置後端元件 (來處理傳送電子郵件時所需的佇列工作項目)，並在本機加以測試。

## 本教學課程章節

-   [必要條件][]
-   [前端概觀][]
-   [後端概觀][]
-   [Azure 資料表][]
-   [Azure 佇列][]
-   [資料圖][]
-   [Azure Blob][]
-   [Azure 雲端服務與 Azure 網站的比較][]
-   [成本][]
-   [驗證和授權][]
-   [後續步驟][]

## 必要條件

這些教學課程中的指示適用於下列產品：

-   Visual Studio 2013 Update 2
-   Visual Studio 2013 Express for Web Update 2

您也需要 Azure 訂用帳戶。您可以建立[免費試用帳戶][]或[啟用 MSDN 訂戶權益][]。

## <a name="frontend"></a>前端概觀

這個應用程式是電子郵件清單服務。其前端包含服務的系統管理員用來管理電子郵件清單的網頁。

(螢幕擷取畫面顯示的是 Visual Studio 2012 範本樣式；其內容與 Visual Studio 2013 相同，但樣式不同。)

![Mailing List Index Page][]

![Subscriber Index Page][]

還有一組頁面可讓系統管理員用來建立要傳送給電子郵件清單的訊息。

![訊息索引頁面][]

![訊息建立頁面][]

這項服務的用戶端是公司，這些公司提供機會讓自己的客戶在用戶端網站上申請成為郵寄清單的一份子。例如，系統管理員為 Contoso 大學歷史系系辦公告設定了清單。當學生按一下 Contoso 大學網站上的連結表示想要收到歷史系系辦公告時，Contoso 大學便會對 Azure 電子郵件服務應用程式進行 Web 服務呼叫。此服務方法會造成傳送電子郵件給客戶。該電子郵件包含一個超連結，如果收件者按一下連結，就會顯示一個歡迎客戶使用歷史系系辦公告清單的頁面。

![確認電子郵件][]

![歡迎使用清單頁面][]

所傳送的每封電子郵件 (但訂閱確認除外) 都包含一個取消訂閱的超連結。如果收件者按一下連結，就會顯示一個詢問是否確認要取消訂閱的網頁。

![確認取消訂閱頁面][]

如果收件者按一下 [確認] 按鈕，就會顯示一個表示已從清單中移除該人員的頁面。

![Unsubscribe confirmed page][]

## <a name="backend"></a>後端概觀

前端會將電子郵件清單和要傳送給這些電子郵件清單的訊息儲存在 Azure 資料表中。每當系統管理員排定一則要傳送的訊息時，`message` 資料表中就會加入一列來包含已排定的日期和其他資料 (例如主旨列)。背景工作角色會定期掃描 `message` 資料表，看看是否有需要傳送的訊息 (此背景工作角色將稱為背景工作角色 A)。

背景工作角色 A 在找到需要傳送的訊息時，會執行下列工作：

-   取得目的地電子郵件清單中的所有電子郵件地址。
-   將傳送每封電子郵件所需的資訊放置到 `message` 資料表中。
-   為每封需要傳送的電子郵件各建立一個佇列工作項目。

第二個背景工作角色 (稱為背景工作角色 B) 會輪詢佇列，看看是否有工作項目。背景工作角色 B 在找到工作項目時，會傳送電子郵件來處理項目，然後從佇列中刪除工作項目。下圖顯示這些關係。

![電子郵件訊息處理][]

如果背景工作角色 B 當機而必須重新啟動，並不會造成任何電子郵件漏傳，因為要等到傳送了電子郵件後，電子郵件的佇列工作項目才會刪除。這個應用程式也可防止萬一背景工作角色 A 當機而必須重新啟動時，造成有電子郵件多傳。

![防止多傳電子郵件][]

背景工作角色 B 會輪詢訂閱佇列，看看 Web API 服務方法是否在該處放置了任何代表新訂閱的工作項目。一旦找到這類項目，就會傳送確認電子郵件。

![訂閱佇列訊息處理][]

## <a name="tables"></a>Azure 資料表

Azure 電子郵件服務應用程式會將資料儲存在 Azure 儲存體資料表中。Azure 資料表是一種 NoSQL 資料存放區，而不是 [Azure SQL Database][] 之類的關聯式資料庫。當效率和延展性比資料正規化與關係完整性更重要時，Azure 資料表是不錯的選擇。例如，在此應用程式中，一個背景工作角色會在每次有佇列工作項目建立時就建立一列，而另一個背景工作角色會在每次有電子郵件傳出時就擷取並更新一列，這時如果使用關聯式資料庫，就可能會出現效能瓶頸。此外，Azure 資料表也比 Azure SQL 便宜。如需 Azure 資料表的詳細資訊，請參閱[本系列的最後一個教學課程][建置 Azure 電子郵件服務應用程式的背景工作角色 B (電子郵件寄件者)]。

下列各節將說明此 Azure 電子郵件服務應用程式所用之各項 Azure 資料表的內容。如需各資料表和其彼此關係的圖解，請參閱本頁稍後的 [Azure 電子郵件服務資料圖][資料圖]。

### mailinglist 資料表

`mailinglist` 資料表中儲存關於郵寄清單和郵寄清單有哪些訂閱者的資訊。(Azure 資料表命名慣例最佳做法為全都使用小寫字母)。

在 Azure 資料表中，不同的列可以有不同的結構描述，這項彈性使得一個資料表可以儲存在關聯式資料庫中要有多個資料表才能儲存的資料。例如，若要在 SQL Database 中儲存郵寄清單資料，您可能得使用三個資料表：`mailinglist` 資料表 (用來儲存清單的相關資訊)、`subscriber` 資料表 (用來儲存訂閱者的相關資訊)，以及 `mailinglistsubscriber` 資料表 (用來儲存郵寄清單與訂閱者的關聯)。在這個應用程式的 NoSQL 資料表中，所有這些功能全都整合到一個名為 `mailinglist` 的資料表中。

在 Azure 資料表中，每列都有一個「資料分割索引鍵」及一個「列索引鍵」來唯一識別該列。資料分割索引鍵的用處是以邏輯方式將資料表分成多個資料分割。列索引鍵的用處則是在資料分割內唯一識別列。沒有次要索引；因此，若要確定應用程式可受調整，請務必將資料表設計成，您永遠可以使用資料分割索引鍵和列索引鍵進行查詢。

`mailinglist` 資料表的資料分割索引鍵是郵寄清單的名稱。

`mailinglist` 資料表的列索引鍵可以是下列兩項之一：常數 "mailinglist" 或是訂閱者的電子郵件地址。列索引鍵為 "mailinglist" 的列將包含郵寄清單的相關資訊。列索引鍵為電子郵件地址的列將包含清單訂閱者的相關資訊。

換言之，列索引鍵為 "mailinglist" 的列相當於關聯式資料庫中的 `mailinglist` 資料表。列索引鍵 = 電子郵件地址的列相當於關聯式資料庫中的 `subscriber` 資料表與 `mailinglistsubscriber` 關聯資料表。

如此用一個資料表來滿足多種用途，將能達到更好的效能。若是在關聯式資料庫中，將必須讀取三張資料表，而且必須整理並交相比對三組列，而這需要花時間來完成。在這裡，只需讀取一張資料表，就會自動依資料分割索引鍵與列索引鍵順序來傳回其中的列。

下表顯示含郵寄清單資訊的列 (列索引鍵 = "mailinglist") 會有哪些列屬性。

| 屬性             | 資料類型 | 說明                                                                                                                                               |
|------------------|----------|----------------------------------------------------------------------------------------------------------------------------------------------------|
| PartitionKey     | String   | ListName：郵寄清單的唯一識別碼，例如：contoso1。此資料表的一般用處是擷取特定郵寄清單的所有資訊，因此使用清單名稱是將此資料表進行分割的高效率方式。 |
| RowKey           | String   | 常數 "mailinglist"。                                                                                                                               |
| 說明             | String   | 郵寄清單的說明，例如："Contoso University History Department announcements"。                                                                      |
| FromEmailAddress | String   | 對此清單傳送之電子郵件中的 [收件者] 電子郵件地址，例如：donotreply@contoso.edu。                                                                   |

下表顯示含清單訂閱者相關資訊的列 (列索引鍵 = 電子郵件地址) 會有哪些列屬性。

| 屬性           | 資料類型 | 說明                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
|----------------|----------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| PartitionKey   | String   | ListName：郵寄清單的名稱 (唯一識別碼)，例如：contoso1。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| RowKey         | String   | EmailAddress：訂閱者電子郵件地址，例如：student1@contoso.edu。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| SubscriberGUID | String   | 在電子郵件地址新增至清單時產生。用於訂閱與取消訂閱連結中，以降低將別人的電子郵件地址訂閱或取消訂閱的可能性。                                                                                                                                                                                                                                                                                                                                                                                                                                                                       
                              「訂閱」和「取消訂閱」網頁的某些查詢只會指定 PartitionKey 和這個屬性。不使用 RowKey 就查詢資料分割，將使應用程式的延展性受限，因為當郵寄清單大小增加時，查詢需要的時間將更長。有個提升延展性的選項，就是新增查閱列並使其 RowKey 屬性含有 SubscriberGUID。例如，就每個電子郵件地址而言，一個列的 RowKey 可以是 "email:student1@domain.com"，而相同訂閱者另一列的 RowKey 可以是 "guid:6f32b03b-90ed-41a9-b8ac-c1310c67b66a"。要實現這點很簡單，因為資料分割內列上的不可部分完成批次交易很容易撰寫。如需詳細資訊，請參閱[真實世界：設計 Azure 資料表儲存體的可調整分割策略][] (英文)  |
| Verified       | Boolean  | 一開始為新的訂閱者建立列時，此值為 false。只有在新的訂閱者按一下歡迎電子郵件中的確認超連結，或系統管理員將它設為 true 之後，才會變更為 true。如果傳送訊息給清單時，訊息其中一個訂閱者的 Verified 值為 false，則不會傳送電子郵件給該訂閱者。                                                                                                                                                                                                                                                                                                                                        |

下列清單示範資料表中的資料可能長什麼樣子。

<table border="1">
<tr>
<th width="200" align="right" bgcolor="lightgray">
資料分割索引鍵

</th>
<td>
contoso1

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
列索引鍵

</th>
<td>
mailinglist

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
說明

</th>
<td>
Contoso University History Department announcements

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
FromEmailAddress

</th>
<td>
donotreply@contoso.edu

</td>
</tr>
</table>

------------------------------------------------------------------------

<table border="1">
<tr>
<th width="200" align="right" bgcolor="lightgray">
資料分割索引鍵

</th>
<td>
contoso1

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
列索引鍵

</th>
<td>
student1@domain.com

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
SubscriberGUID

</th>
<td>
6f32b03b-90ed-41a9-b8ac-c1310c67b66a

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
Verified

</th>
<td>
true

</td>
</tr>
</table>

------------------------------------------------------------------------

<table border="1">
<tr>
<th width="200" align="right" bgcolor="lightgray">
資料分割索引鍵

</th>
<td>
contoso1

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
列索引鍵

</th>
<td>
student2@domain.com

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
SubscriberGUID

</th>
<td>
01234567-90ed-41a9-b8ac-c1310c67b66a

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
Verified

</th>
<td>
false

</td>
</tr>
</table>

------------------------------------------------------------------------

<table border="1">
<tr>
<th width="200" align="right" bgcolor="lightgray">
資料分割索引鍵

</th>
<td>
fabrikam1

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
列索引鍵

</th>
<td>
mailinglist

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
說明

</th>
<td>
Fabrikam Engineering job postings

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
FromEmailAddress

</th>
<td>
donotreply@fabrikam.com

</td>
</tr>
</table>

------------------------------------------------------------------------

<table border="1">
<tr>
<th width="200" align="right" bgcolor="lightgray">
資料分割索引鍵

</th>
<td>
fabrikam1

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
列索引鍵

</th>
<td>
applicant1@domain.com

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
SubscriberGUID

</th>
<td>
76543210-90ed-41a9-b8ac-c1310c67b66a

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
Verified

</th>
<td>
true

</td>
</tr>
</table>
### message 資料表

`message` 資料表中儲存關於已排定要對郵寄清單傳送之訊息的資訊。系統管理員會使用網頁在此資料表中建立及編輯列，而背景工作角色會使用此資料表，將每封電子郵件的相關資訊從背景工作角色 A 傳遞至背景工作角色 B。

message 資料表的資料分割索引鍵是排定要傳送電子郵件的日期，格式為 yyyy-mm-dd。如此可使資料表在面對最常對這個資料表執行的查詢 (也就是會選取 `ScheduledDate` 為今天或更早日期之列的查詢) 時，展現最佳效能。不過，這也會引起潛在的效能瓶頸，因為 Azure 儲存體資料表對於一個資料分割，設有每秒 500 個實體的輸送量上限。每遇到一封要傳送的電子郵件，應用程式就會在 `message` 資料表中寫入一列、讀取一列再刪除一列。因此，當一天就有 1,000,000 封電子郵件被排定要傳送時，最短可能處理時間就有兩小時，不管新增了多少個背景工作角色來處理增加的負載都一樣。

`message` 資料表的列索引鍵可以是下列兩項之一：常數 "message" 加上訊息的唯一索引鍵 (稱為 `MessageRef`)，或 `MessageRef` 值加上訂閱者的電子郵件地址。列索引鍵以 "message" 開頭的列會包含訊息的相關資訊，例如要傳送給的郵寄清單，以及應該傳送的時間。列索引鍵為 `MessageRef` 與電子郵件地址的列，則會包含傳送電子郵件給該電子郵件地址時所需的一切資訊。

在關聯式資料庫術語中，列索引鍵以 "message" 開頭的列相當於 `message` 資料表。列索引鍵 = `MessageRef` 加上電子郵件地址的列，則相當於含 `mailinglist`、`message` 和 `subscriber` 資訊的聯結查詢檢視。

下表顯示 `message` 資料表中含訊息本身相關資訊的列會有哪些列屬性。

<table>
<colgroup>
<col width="33%" />
<col width="33%" />
<col width="33%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">屬性</th>
<th align="left">資料類型</th>
<th align="left">說明</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">PartitionKey</td>
<td align="left">String</td>
<td align="left">排定要傳送訊息的日期，格式為 yyyy-mm-dd。</td>
</tr>
<tr class="even">
<td align="left">RowKey</td>
<td align="left">String</td>
<td align="left">常數 &quot;message&quot; 與 <code data-inline="1">MessageRef</code> 值串連。<code data-inline="1">MessageRef</code> 是透過在建立列時，從 <code data-inline="1">DateTime.Now</code> 取得 <code data-inline="1">Ticks</code> 值所建立的唯一值。<br /><br />注意：應該準備好大量多執行緒、多執行個體的應用程式，以處理使用 Ticks 時出現的重複 RowKey 例外狀況。Ticks 不保證具唯一性。</td>
</tr>
<tr class="odd">
<td align="left">ScheduledDate</td>
<td align="left">Date</td>
<td align="left">排定要傳送訊息的日期。(與 <code data-inline="1">PartitionKey</code> 相同，但採用 Date 格式。)</td>
</tr>
<tr class="even">
<td align="left">SubjectLine</td>
<td align="left">String</td>
<td align="left">電子郵件的主旨列。</td>
</tr>
<tr class="odd">
<td align="left">ListName</td>
<td align="left">String</td>
<td align="left">此訊息要傳送到的清單。</td>
</tr>
<tr class="even">
<td align="left">Status</td>
<td align="left">String</td>
<td align="left"><ul>
<li>&quot;Pending&quot; -- 背景工作角色 A 尚未開始建立佇列訊息來排定電子郵件。</li>
<li>&quot;Queuing&quot; -- 背景工作角色 A 已開始建立佇列訊息來排定電子郵件。</li>
<li>&quot;Processing&quot; -- 背景工作角色 A 已為清單中的所有電子郵件建立佇列工作項目，但是尚未傳送所有電子郵件。</li>
<li>&quot;Completed&quot; -- 背景工作角色 B 已完成處理所有佇列工作項目 (已傳送所有電子郵件)。已完成的列會封存在 <code data-inline="1">messagearchive</code> 資料表中，稍後會有說明。我們希望在下一版中使這個屬性採用 <code data-inline="1">enum</code> 格式。</li>
</ul></td>
</tr>
</tbody>
</table>

背景工作角色 A 在為待傳送給清單的電子郵件建立佇列訊息時，會在 `message` 資料表中建立電子郵件列。背景工作角色 B 在傳送電子郵件時，則會將電子郵件列移至 `messagearchive` 資料表，並將 `EmailSent` 屬性更新為 `true`。當處於 Processing 狀態的訊息其所有電子郵件列都已被封存時，背景工作角色 A 會將狀態設為 Completed，並將 `message` 列移至 `messagearchive` 資料表。

下表顯示 `message` 資料表中的電子郵件列會有哪些列屬性。

| 屬性              | 資料類型 | 說明                                                                                             |
|-------------------|----------|--------------------------------------------------------------------------------------------------|
| PartitionKey      | String   | 排定要傳送訊息的日期，格式為 yyyy-mm-dd。                                                        |
| RowKey            | String   | `MessageRef` 值以及目的地電子郵件地址 (來自 `mailinglist` 資料表的 `subscriber` 列)。            |
| MessageRef        | Long     | 與 `RowKey` 的 `MessageRef` 元件相同。                                                           |
| ScheduledDate     | Date     | 已排定的日期，來自 `message` 資料表的 `message` 列。(與 `PartitionKey` 相同，但採用 Date 格式。) |
| SubjectLine       | String   | 電子郵件主旨列，來自 `message` 資料表的 `message` 列。                                           |
| ListName          | String   | 郵寄清單名稱，來自 `mailinglist` 資料表。                                                        |
| From EmailAddress | String   | [寄件者] 電子郵件地址，來自 `mailinglist` 資料表的 `mailinglist` 列。                            |
| EmailAddress      | String   | 電子郵件地址，來自 `mailinglist` 資料表的 `subscriber` 列。                                      |
| SubscriberGUID    | String   | 訂閱者 GUID，來自 `mailinglist` 資料表的 `subscriber` 列。                                       |
| EmailSent         | Boolean  | False 表示尚未傳送電子郵件；true 表示已傳送電子郵件。                                            |

這些列中有重複的資料 (在關聯式資料庫中，這通常您是會避免的情況)。但是在此情況下，您是以資料重複的一些缺點換得更大的處理效率與延展性。因為電子郵件所需的所有資料都呈現在其中一列，所以背景工作角色 B 只需讀取一列，即可在從佇列中取出工作項目的同時就傳送電子郵件。

您可能在想，那麼電子郵件的本文又從何而來。這些列並沒有電子郵件本文所在檔案的 Blob 參考，因為該值是衍生自 `MessageRef` 值。例如，如果 `MessageRef` 為 634852858215726983，則 Blob 會命名為 634852858215726983.htm 和 634852858215726983.txt。

下列清單示範資料表中的資料可能長什麼樣子。

<table border="1">
<tr>
<th width="200" align="right" bgcolor="lightgray">
資料分割索引鍵

</th>
<td>
2012-10-15

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
列索引鍵

</th>
<td>
message634852858215726983

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
MessageRef

</th>
<td>
634852858215726983

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
ScheduledDate

</th>
<td>
2012-10-15

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
SubjectLine

</th>
<td>
New lecture series

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
ListName

</th>
<td>
contoso1

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
Status

</th>
<td>
Processing

</td>
</tr>
</table>

------------------------------------------------------------------------

<table border="1">
<tr>
<th width="200" align="right" bgcolor="lightgray">
資料分割索引鍵

</th>
<td>
2012-10-15

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
列索引鍵

</th>
<td>
634852858215726983student1@contoso.edu

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
MessageRef

</th>
<td>
634852858215726983

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
ScheduledDate

</th>
<td>
2012-10-15

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
SubjectLine

</th>
<td>
New lecture series

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
ListName

</th>
<td>
contoso1

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
FromEmailAddress

</th>
<td>
donotreply@contoso.edu

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
EmailAddress

</th>
<td>
student1@contoso.edu

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
SubscriberGUID

</th>
<td>
76543210-90ed-41a9-b8ac-c1310c67b66a

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
EmailSent

</th>
<td>
true

</td>
</tr>
</table>

------------------------------------------------------------------------

<table border="1">
<tr>
<th width="200" align="right" bgcolor="lightgray">
資料分割索引鍵

</th>
<td>
2012-10-15

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
列索引鍵

</th>
<td>
634852858215726983student2@contoso.edu

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
MessageRef

</th>
<td>
634852858215726983

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
ScheduledDate

</th>
<td>
2012-10-15

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
SubjectLine

</th>
<td>
New lecture series

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
ListName

</th>
<td>
contoso1

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
FromEmailAddress

</th>
<td>
donotreply@contoso.edu

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
EmailAddress

</th>
<td>
student2@contoso.edu

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
SubscriberGUID

</th>
<td>
12345678-90ed-41a9-b8ac-c1310c679876

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
EmailSent

</th>
<td>
true

</td>
</tr>
</table>

------------------------------------------------------------------------

<table border="1">
<tr>
<th width="200" align="right" bgcolor="lightgray">
資料分割索引鍵

</th>
<td>
2012-11-15

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
列索引鍵

</th>
<td>
message124852858215726999

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
MessageRef

</th>
<td>
124852858215726999

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
ScheduledDate

</th>
<td>
2012-11-15

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
SubjectLine

</th>
<td>
New job postings

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
ListName

</th>
<td>
fabrikam

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
Status

</th>
<td>
Pending

</td>
</tr>
</table>

### messagearchive 資料表

有個策略可確保查詢會有效率地執行 (尤其是當您必須在 `PartitionKey` 和 `RowKey` 以外的其他欄位上搜尋時)，那就是限制資料表的大小。背景工作角色 A 中有個查詢會查看是否已傳送某個訊息的所有電子郵件，而該查詢需要在 `message` 資料表中尋找 `EmailSent` = false 的電子郵件列。`EmailSent` 值並不在 PartitionKey 或 RowKey 中，所以當訊息具有大量電子郵件列時，這不會是有效率的查詢。因此，傳送電子郵件時，應用程式會將電子郵件列移至 `messagearchive` 資料表。如此一來，該查詢在檢查是否已傳送某個訊息的所有電子郵件時，就只須查詢 message 資料表的 `PartitionKey` 和 `RowKey`，原因是只要它發現某個訊息有任何電子郵件列，就表示有未傳送的訊息，而該訊息就不能標示為 `Complete`。

`messagearchive` 資料表中列的結構描述與 `message` 資料表中列的結構描述相同。視您想要如何用此封存資料做什麼而定，您可以減少針對每列儲存的屬性數，並刪除超過特定年齡的列，來限制此封存資料的大小與成本。

## <a name="queues"></a>Azure 佇列

Azure 佇列可促進這個多層式應用程式的各層之間，以及後端階層中各背景工作角色之間的通訊。
佇列會用於背景工作角色 A 與背景工作角色 B 之間的通訊，讓應用程式變得可調整。背景工作角色 A 可能在 Message 資料表中為每封電子郵件各建立一列，而背景工作角色 B 則可能會掃描資料表，找出代表尚未傳送之電子郵件的列，但您將無法新增更多背景工作角色 B 執行個體。使用資料表列來協調背景工作角色 A 與背景工作角色 B 之間的工作有個問題，就是您沒辦法確保任一資料表列都只會被一個背景工作角色執行個體挑中處理。佇列則可提供該保證。當背景工作角色執行個體從佇列中取出工作項目時，佇列服務會確定沒有其他背景工作角色執行個體可以提取該工作項目。Azure 的這項獨家釋出功能有助於將工作負載分攤給背景工作角色的多個執行個體。

Azure 也提供服務匯流排佇列服務。如需 Azure 儲存體佇列與服務匯流排佇列的詳細資訊，請參閱[本系列的最後一個教學課程][建置 Azure 電子郵件服務應用程式的背景工作角色 B (電子郵件寄件者)]。

Azure 電子郵件服務應用程式使用兩個佇列，分別名為 `AzureMailQueue` 和 `AzureMailSubscribeQueue`。

### AzureMailQueue

`AzureMailQueue` 佇列會協調傳送電子郵件到電子郵件清單的順序。背景工作角色 A 會針對每封要傳送的電子郵件各放置一個工作項目到佇列中，而背景工作角色 B 則會從佇列中提出工作項目並傳送電子郵件。

佇列工作項目包含一個逗號分隔字串，字串中的每個分隔項目依序代表訊息 (`message` 資料表的資料分割索引鍵) 的已排定日期、`MessageRef` 和 `EmailAddress` 值 (`message` 資料表的列索引鍵)，以及一個旗標 (指出項目是否是在背景工作角色當機而重新啟動之後建立)，例如：

      2012-10-15,634852858215726983,student1@contoso.edu,0

背景工作角色 B 會使用這些值來查閱 `message` 資料表中含傳送電子郵件時所需之一切資訊的列。如果重新啟動旗標指出有重新啟動的情況，則背景工作 B 會先確定尚未傳送電子郵件，然後才傳送電子郵件。

當流量突然暴增時，雲端服務可以經過重新設定，以便具現化多個背景工作角色 B 執行個體，並讓這每個執行個體都可以獨立從佇列中提出工作項目。

### AzureMailSubscribeQueue

`AzureMailSubscribeQueue` 佇列會協調傳送訂閱確認電子郵件的順序。服務方法在收到服務方法呼叫時，會在佇列上放置一個工作項目。背景工作角色 B 會從佇列中提出工作項目，並傳送訂閱確認電子郵件。

佇列工作項目包含訂閱者 GUID。此值會唯一識別電子郵件地址和要讓它訂閱的清單，這些就是背景工作角色 B 傳送確認電子郵件時所需的一切資訊。如稍早所述，要做這個動作，就需要查詢不在 `PartitionKey` 或 `RowKey` 中的欄位，而這樣做很沒有效率。若要使應用程式更可調整，則必須重組 `mailinglist` 資料表的結構，使 `RowKey` 中包含訂閱者 GUID。

## <a name="datadiagram"></a><span class="short-header">資料圖</span>Azure 電子郵件服務資料圖

下圖顯示各資料表與佇列之間的關係。

![Azure 電子郵件服務應用程式的資料圖][]

## <a name="blobs"></a>Azure Blob

Blob 是指「二進位大型物件」。Azure Blob 服務提供一種在雲端上傳和儲存檔案的方法。如需 Azure Blob 的詳細資訊，請參閱[本系列的最後一個教學課程][建置 Azure 電子郵件服務應用程式的背景工作角色 B (電子郵件寄件者)]。

Azure 郵件服務系統管理員會將電子郵件本文以 HTML 形式放置於 *.htm* 檔案中，或以純文字形式放置於 *.txt* 檔案中。要排定電子郵件時，他們會在 [建立訊息] 網頁中上傳這些檔案，而該頁面的 ASP.NET MVC 控制器就會將上傳的檔案儲存在 Azure Blob 中。

Blob 是儲存在 Blob 容器中，就像檔案是儲存在資料夾中一樣。Azure 郵件服務應用程式是使用單一 Blob 容器，名為 **azuremailblobcontainer**。容器中各 Blob 的名稱是串連 MessageRef 值與副檔名所洐生出來的，例如：634852858215726983.htm 和 634852858215726983.txt。

由於 HTML 和純文字訊息基本上都是字串，因此我們原本也可將應用程式設計為將電子郵件訊息本文儲存在 `Message` 資料表中，而非 Blob 中。不過，資料表列中的屬性有 64K 的大小限制，因此使用 Blob 可避開該電子郵件本文大小限制。(64K 只是屬性的總大小上限；如果排除掉編碼額外負荷，則可在屬性中儲存的字串大小上限實際上比較接近 48k。)

## <a name="wawsvswacs"></a>Azure 雲端服務與 Azure 網站的比較

Azure 電子郵件服務已設定成讓前端和後端全都在 Azure 雲端服務中執行。

![應用程式架構概觀][]

替代的架構是在 Azure 網站中執行前端。

![替代的應用程式架構][]

另一個替代方法是在 Azure 網站中執行前端，並且使用 WebJob 功能在執行前端的相同伺服器上執行後端。如需詳細資訊，請參閱[開始使用 Azure WebJobs SDK][] (英文)。

## <a name="cost"></a>成本

本節概略介紹在 Azure 執行範例應用程式所需的成本，其中的費率乃是原本在 2012 年 12 月發佈本教學課程時的有效費率。在根據成本做出任何商業決策之前，請務必到下列網頁查看最新費率：

-   [Azure 定價計算機][]
-   [SendGrid Azure (英文)][]

成本會受您決定要維護的 Web 與背景工作角色執行個體數目所影響。為了達到 [Azure 雲端服務 99.95% 的服務等級協定 (SLA)][]，您必須針對每個角色各部署兩個以上的執行個體。至少必須執行兩個角色執行個體的原因之一，是因為執行您應用程式的虛擬機器每個月會因為要進行作業系統升級，而重新啟動兩次左右。(如需作業系統升級的詳細資訊，請參閱[角色執行個體由於作業系統升級而重新啟動][] (英文)。)

在此範例中，兩個背景工作角色所執行的工作並無時間上的急迫性，不需要達到 99.5% 的 SLA。因此，只要工作負載用一個執行個體就能應付，只執行每個背景工作角色的單一執行個體並無不妥。Web 角色執行個體則有時間上的急迫性，也就是說，使用者預期網站不會有任何停機的情況發生，因此生產應用程式應該至少有兩個 Web 角色執行個體。

下表顯示 Azure 電子郵件服務範例應用程式預設架構的成本 (假設工作負載僅達最低水準)。所示的成本是根據使用一個額外小型 (共用) 虛擬機器大小而來。您建立 Visual Studio 雲端專案時的預設虛擬機器大小很小，比這額外小型大小大約貴了六倍。

<table border="1">
<tr bgcolor="lightgray">
<th>
元件或服務

</th>
<th>
費率

</th>
<th>
每月成本

</th>
</tr>
<tr>
<td>
Web 角色

</td>
<td>
2 個執行個體，以額外小型執行個體而言，費率為 $.02 美元/小時

</td>
<td>
$29.00

</td>
</tr>
<tr>
<td>
背景工作角色 A (排定要傳送的電子郵件)

</td>
<td>
1 個執行個體，以額外小型執行個體而言，費率為 $.02 美元/小時

</td>
<td>
$14.50

</td>
</tr>
<tr>
<td>
背景工作角色 B (傳送電子郵件)

</td>
<td>
1 個執行個體，以額外小型執行個體而言，費率為 $.02 美元/小時

</td>
<td>
$14.50

</td>
</tr>
<tr>
<td>
Azure 儲存體交易

</td>
<td>
每個月 1 百萬筆交易，費率為 $0.10 美元/百萬筆 (每個查詢都算是一筆交易；背景工作角色 A 會持續查詢資料表，看看是否有需要傳送的訊息。應用程式也是設定成將診斷資料寫入 Azure 儲存體，而且每次這樣做，即為一筆交易。)

</td>
<td>
$0.10

</td>
</tr>
<tr>
<td>
Azure 本機備援儲存體

</td>
<td>
25 GB $2.33 美元 (包括存放應用程式資料表與診斷資料的儲存體。)

</td>
<td>
$2.33

</td>
</tr>
<tr>
<td>
頻寬

</td>
<td>
5 GB 免費出口流量

</td>
<td>
免費

</td>
</tr>
<tr>
<td>
SendGrid

</td>
<td>
Azure 客戶每個月可以免費傳送 25,000 封電子郵件

</td>
<td>
免費

</td>
</tr>
<tr>
<td colspan="2">
總計

</td>
<td>
$60.43

</td>
</tr>
</table>
您可以看見，角色執行個體為整體成本中的主要部分。角色執行個體即使停止也會產生成本，因此您必須刪除角色執行個體，才能不產生任何費用。節省成本的方法之一為將背景工作角色 A 與背景工作角色 B 中的所有程式碼都移到一個背景工作角色中。在這些教學課程中，我們刻意選擇實作兩個背景工作執行個體，以便簡化向外延展的過程。背景工作角色 B 執行的工作是由 Azure 佇列服務協調，這表示您可以僅增加背景工作角色 B 的執行個體數目，向外延展該角色。(背景工作角色 B 為高負載條件的限制因素。) 背景工作角色 A 執行的工作不是由佇列協調，因此您無法執行背景工作角色 A 的多個執行個體。如果這兩個背景工作角色合併，而您想要啟用向外延展，則您會需要實作一個機制，確保背景工作角色 A 工作只會在一個執行個體中執行。([CloudFx][] (英文)就提供了這樣一個機制。請參閱 [WorkerRole.cs 範例][] (英文)。)

也可以將這兩個背景工作角色中的所有程式碼都移到 Web 角色中，讓一切都在 Web 角色中執行。不過，在 ASP.NET 執行背景工作並不受支援，也不被視為健全，而且此架構將使延展性變複雜。如需詳細資訊，請參閱[在 ASP.NET 實作週期性背景工作的危險][] (英文)。另請參閱[如何在 Azure 結合背景工作與 Web 角色][] (英文) 和[將多個 Azure 背景工作角色結合成一個 Azure Web 角色][] (英文)。如果您想要朝這個方向進行，[在 Azure 網站中執行並使用 WebJobs 功能執行後端工作][]會是更佳的解決方案。

另一個使成本降低的替代架構是使用[自動調整應用程式區塊][]，以僅在已排定的期間自動部署背景工作角色，並在工作完成時加以刪除。如需自動調整的詳細資訊，請參閱[本系列的最後一個教學課程][建置 Azure 電子郵件服務應用程式的背景工作角色 B (電子郵件寄件者)]。

未來的 Azure 可能會針對排定的重新開機提供通知機制，讓您能夠在重新開機時間範圍僅讓一個額外的 Web 角色執行個體運轉上線。您將達不到 99.95 SLA，但可以減少幾乎一半的成本，並確保您的 Web 應用程式在重新開機期間仍然可用。

## <a name="auth"></a>驗證和授權

在生產應用程式中，您會實作驗證與授權機制，如 ASP.NET MVC Web 前端的 [ASP.NET 身分識別][]，包括 ASP.NET Web API 服務方法。也有其他選項 (例如使用共用密鑰) 可保護 Web API 服務方法的安全。範例應用程式中省略了驗證與授權功能，以方便設定和部署。

## <a name="nextsteps"></a>後續步驟

在[下一個教學課程][設定及部署 Azure 電子郵件服務應用程式]中，您將下載範例專案、設定開發環境、設定適用於環境的專案，然後在本機與雲端測試專案。在教學課程 3 到 5 中，您將看到如何從頭建置專案。

如需使用 Azure 儲存體資料表、佇列和 Blob 的其他資源連結，請參閱[本系列的最後一個教學課程][]。

<div><a href="/en-us/develop/net/tutorials/multi-tier-web-site/2-download-and-run/" class="site-arrowboxcta download-cta">教學課程 2</a></div>

  [完整的應用程式]: http://code.msdn.microsoft.com/Windows-Azure-Multi-Tier-eadceb36
  [電子書]: http://social.technet.microsoft.com/wiki/contents/articles/11608.e-book-gallery-for-microsoft-technologies.aspx#ASPNETMultiTierWindowsAzureApplicationUsingStorageTablesQueuesandBlobs
  [電子郵件訊息處理]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-worker-roles-a-and-b.png
  [開始使用 Azure 雲端服務和 ASP.NET]: /zh-tw/documentation/articles/cloud-services-dotnet-get-started/
  [開始使用 Azure WebJobs SDK]: /zh-tw/documentation/articles/websites-dotnet-webjobs-sdk-get-started/
  [設定及部署 Azure 電子郵件服務應用程式]: /en-us/develop/net/tutorials/multi-tier-web-site/2-download-and-run/
  [建置 Azure 電子郵件服務應用程式的 Web 角色]: /en-us/develop/net/tutorials/multi-tier-web-site/3-web-role/
  [建置 Azure 電子郵件服務應用程式的背景工作角色 A (電子郵件排程器)]: /en-us/develop/net/tutorials/multi-tier-web-site/4-worker-role-a/
  [建置 Azure 電子郵件服務應用程式的背景工作角色 B (電子郵件寄件者)]: /en-us/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/
  [必要條件]: #prerequisites
  [前端概觀]: #frontend
  [後端概觀]: #backend
  [Azure 資料表]: #tables
  [Azure 佇列]: #queues
  [資料圖]: #datadiagram
  [Azure Blob]: #blobs
  [Azure 雲端服務與 Azure 網站的比較]: #wawsvswacs
  [成本]: #cost
  [驗證和授權]: #auth
  [後續步驟]: #nextsteps
  [免費試用帳戶]: /en-us/pricing/free-trial/
  [啟用 MSDN 訂戶權益]: /en-us/pricing/member-offers/msdn-benefits/
  [Mailing List Index Page]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-mailing-list-index-page.png
  [Subscriber Index Page]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-subscribers-index-page.png
  [訊息索引頁面]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-message-index-page.png
  [訊息建立頁面]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-message-create-page.png
  [確認電子郵件]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-subscribe-email.png
  [歡迎使用清單頁面]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-subscribe-confirmation-page.png
  [確認取消訂閱頁面]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-unsubscribe-query-page.png
  [Unsubscribe confirmed page]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-unsubscribe-confirmation-page.png
  [防止多傳電子郵件]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-message-processing.png
  [訂閱佇列訊息處理]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-subscribe-diagram.png
  [Azure SQL Database]: http://msdn.microsoft.com/en-us/library/windowsazure/ee336279.aspx
  [真實世界：設計 Azure 資料表儲存體的可調整分割策略]: http://msdn.microsoft.com/en-us/library/windowsazure/hh508997.aspx
  [Azure 電子郵件服務應用程式的資料圖]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-datadiagram.png
  [應用程式架構概觀]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-architecture-overview.png
  [替代的應用程式架構]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-alternative-architecture.png
  [Azure 定價計算機]: http://www.windowsazure.com/en-us/pricing/calculator/
  [SendGrid Azure (英文)]: http://sendgrid.com/windowsazure.html
  [Azure 雲端服務 99.95% 的服務等級協定 (SLA)]: https://www.windowsazure.com/en-us/support/legal/sla/ "SLA"
  [角色執行個體由於作業系統升級而重新啟動]: http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx
  [CloudFx]: http://nuget.org/packages/Microsoft.Experience.CloudFx "CloudFX"
  [WorkerRole.cs 範例]: http://code.msdn.microsoft.com/windowsazure/CloudFx-Samples-60c3a852/sourcecode?fileId=57087&pathId=528472169
  [在 ASP.NET 實作週期性背景工作的危險]: http://haacked.com/archive/2011/10/16/the-dangers-of-implementing-recurring-background-tasks-in-asp-net.aspx
  [如何在 Azure 結合背景工作與 Web 角色]: http://www.31a2ba2a-b718-11dc-8314-0800200c9a66.com/2010/12/how-to-combine-worker-and-web-role-in.html
  [將多個 Azure 背景工作角色結合成一個 Azure Web 角色]: http://www.31a2ba2a-b718-11dc-8314-0800200c9a66.com/2012/02/combining-multiple-azure-worker-roles.html
  [在 Azure 網站中執行並使用 WebJobs 功能執行後端工作]: http://go.microsoft.com/fwlink/?LinkId=390226
  [自動調整應用程式區塊]: /en-us/develop/net/how-to-guides/autoscaling/
  [ASP.NET 身分識別]: http://asp.net/identity
  [本系列的最後一個教學課程]: /en-us/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/#nextsteps
