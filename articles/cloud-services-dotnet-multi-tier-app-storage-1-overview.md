<properties  linkid="develop-net-tutorials-multi-tier-web-site-1-overview" urlDisplayName="Step 1: Overview" pageTitle="ASP.NET Multi-tier Web Application with Azure - Step 1: Overview" metaKeywords="Azure tutorial, email list service app, email service architecture, Azure tutorial overview, Azure multi-tier, Azure storage, Azure blobs, Azure tables, Azure queues" description="Learn about the five-part multi-tier Azure web application tutorial." metaCanonical="" services="cloud-services,storage" documentationCenter=".NET" title="Multi-tier ASP.NET MVC Web Site Tutorial - Step 1: Overview" authors="tdykstra,riande" solutions="" manager="wpickett" editor="mollybos" />

# 使用 Azure 儲存體資料表、佇列與 Blob 的 ASP.NET 多層式應用程式 - 1/5

本教學課程系列說明如何建立使用了 Azure 儲存體資料表、佇列和 Blob 的多層式 ASP.NET MVC Web 應用程式，並且說明如何將應用程式部署到 Azure 雲端服務。這些教學課程假設您先前沒有使用 Azure 的經驗。完成本系列後，您將知道如何建置靈活、可調整的資料驅動型 Web 應用程式，並將之部署到雲端。

這些內容已以免費電子書形式放在 [TechNet 電子書庫][1] (英文) 上任人取用。

<h2><a name="whatyoulllearn"></a><span  class="short-header">您將學到什麼</span>您將學到什麼</h2>


在本教學課程系列中，您將學到：

* 如何安裝 Azure SDK，讓電腦具備 Azure 開發的能力。
* 如何建立含一個 ASP.NET MVC Web 角色與兩個背景工作角色的 Visual Studio 雲端專案。
* 如何將雲端專案發佈至 Azure 雲端服務。
* 如何將 MVC 專案發佈至 Azure 網站 (如果想要的話)，而且仍然在雲端服務中使用背景工作角色。
* 如何使用 Azure 佇列儲存體服務進行各層之間或各背景工作角色之間的通訊。
* 如何使用 Azure 資料表儲存體服務作為結構化、非關聯式資料的高度可調整資料存放區。
* 如何使用 Azure Blob 服務將檔案儲存在雲端中。
* 如何使用 Visual Studio 或 Azure 儲存體總管來檢視及編輯 Azure 資料表、佇列與 Blob。
* 如何使用 SendGrid 傳送電子郵件。
* 如何設定追蹤及檢視追蹤資料。
* 如何增加背景工作角色執行個體數目來調整應用程式。

<h2><a name="frontendoverview"></a><span  class="short-header">前端概觀</span>前端概觀</h2>


您將建置的應用程式是電子郵件清單服務。此多層式應用程式的前端包括讓該服務的系統管理員用來管理電子郵件清單的網頁。

![郵寄清單索引頁面](./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-mailing-list-index-page.png)

![訂閱者索引頁面](./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-subscribers-index-page.png)

還有一組頁面可讓系統管理員用來建立要傳送給電子郵件清單的訊息。

![訊息索引頁面](./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-message-index-page.png)

![訊息建立頁面](./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-message-create-page.png)

此服務的用戶端是公司，這些公司提供機會讓自己的客戶在用戶端網站上申請成為郵寄清單的一份子。例如，系統管理員為 Contoso
大學歷史系系辦公告設定了清單。當學生按一下 Contoso 大學網站上的連結表示想要收到歷史系系辦公告時，Contoso 大學便會對 Azure
電子郵件服務應用程式進行 Web 服務呼叫。此服務方法會造成傳送電子郵件給客戶。該電子郵件包含一個超連結，如果收件者按一下連結，就會顯示一個歡迎客戶使用歷史系系辦公告清單的頁面。

![確認電子郵件](./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-subscribe-email.png)

![歡迎使用清單頁面](./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-subscribe-confirmation-page.png)

服務所傳送的每封電子郵件 (但訂閱確認除外) 都包含一個取消訂閱的超連結。如果收件者按一下連結，就會顯示一個詢問是否確認要取消訂閱的網頁。

![確認取消訂閱頁面](./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-unsubscribe-query-page.png)

如果收件者按一下 **確認** 按鈕，就會顯示一個表示已從清單中移除該人員的頁面。

![取消訂閱已確認頁面](./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-unsubscribe-confirmation-page.png)

<h2><a name="whyanemaillistapp"></a><span  class="short-header">教學課程</span>本系列中的教學課程</h2>


以下清單列出各教學課程和其內容摘要：

1.  **Azure 電子郵件服務應用程式簡介** (本教學課程)。應用程式和其架構的概觀。
2.  [設定及部署 Azure 電子郵件服務應用程式](/en-us/develop/net/tutorials/multi-tier-web-site/2-download-and-run/)。如何下載範例應用程式，並加以設定、在本機測試、部署，以及在雲端測試。
3.  [建置 Azure 電子郵件服務應用程式的 Web 角色](/en-us/develop/net/tutorials/multi-tier-web-site/3-web-role/)。如何建置應用程式的 MVC 4 元件，並在本機加以測試。
4.  [建置 Azure 電子郵件服務應用程式的背景工作角色 A (電子郵件排程器)](/en-us/develop/net/tutorials/multi-tier-web-site/4-worker-role-a/)。如何建置後端元件
    (來建立傳送電子郵件時所需的佇列工作項目)，並在本機加以測試。
5.  [建置 Azure 電子郵件服務應用程式的背景工作角色 B
    (電子郵件寄件者)](/en-us/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/)。如何建置後端元件
    (來處理傳送電子郵件時所需的佇列工作項目)，並在本機加以測試。

若只需要下載並試用此應用程式，則您只需要完成前兩個教學課程。若需要了解從頭建置應用程式 (如這個應用程式) 的所有步驟，請在完成前兩個教學課程後，完成後三個教學課程。

<h2><a name="whyanemaillistapp"></a><span  class="short-header">為何是此應用程式</span>為何是電子郵件清單服務應用程式</h2>


我們選擇電子郵件清單服務作為此範例應用程式，是因為這種應用程式必須兼具恢復力強和可調整的兩項特質，這使得 Azure 特別適合它。

### 恢復力強

如果伺服器在傳送電子郵件給大型清單時故障，您需要能夠輕鬆快速地讓新的伺服器運轉上線，而且需要能夠讓應用程式從中斷之處繼續，而不會造成任何電子郵件漏傳或多傳。Azure 雲端服務 Web 或背景工作角色執行個體 (虛擬機器) 如果故障，就會自動受到取代。而 Azure 儲存體佇列與資料表則可實現就算遇到故障，也可繼續正常進行的伺服器間通訊。

### 可調整

電子郵件服務還必須能夠應付突然暴增的工作負載，因為電子郵件有時候是要傳送給很小的清單，有時候則是要傳送給很大的清單。在許多主控環境中，您必須購買並維護足夠的硬體來因應突然暴增的工作負載，因此您是 100% 的時間在為該容量付出金錢，但可能只有 5% 的時間使用它。有了 Azure，您只需要為真正需要的運算能力量和時間付出金錢。若要向上調整以進行大規模郵寄，您只需要變更組態設定來提高可用來處理工作負載的伺服器數目，而此動作可以透過程式設計方式完成。例如，您可以設定應用程式，使得當佇列中等待的工作項目數目超出特定數目時，就自動使 Azure 讓更多背景工作角色執行個體運轉上線來處理那些工作項目。

<h2><a name="backendoverview"></a><span  class="short-header">後端概觀</span>後端概觀</h2>


前端會將電子郵件清單和要傳送給這些電子郵件清單的訊息儲存在 Azure 資料表中。每當系統管理員排定一則要傳送的訊息時，`message` 資料表中就會新增一列來包含已排定的日期和其他資料 (例如主旨列) 。背景工作角色會定期掃描 `message` 資料表，看看是否有需要傳送的訊息
(此背景工作角色將稱為背景工作角色 A)。

背景工作角色 A 在找到需要傳送的訊息時，會執行下列工作：

* 取得目的地電子郵件清單中的所有電子郵件地址。
* 將傳送每封電子郵件所需的資訊放置到 `message` 資料表中。
* 為每封需要傳送的電子郵件各建立一個佇列工作項目。

第二個背景工作角色 (稱為背景工作角色 B) 會輪詢佇列，看看是否有工作項目。背景工作角色 B 在找到工作項目時，會傳送電子郵件來處理項目，然後從佇列中刪除工作項目。下圖顯示這些關係。

![電子郵件訊息處理](./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-worker-roles-a-and-b.png)

如果背景工作角色 B 當機而必須重新啟動，並不會造成任何電子郵件漏傳，因為要等到傳送了電子郵件後，電子郵件的佇列工作項目才會刪除。後端也實作了資料表處理機制，以防止萬一背景工作角色 A 當機而必須重新啟動時，造成有電子郵件多傳。萬一背景工作角色 A 真的當機而重新啟動，則替某目的地電子郵件地址產生的佇列工作項目可能會有多個。但針對每個目的地電子郵件地址，`message` 中都會有一列來追蹤是否已傳送電子郵件。背景工作 A 會使用此列來避免建立第二個佇列工作項目，或背景工作 B 會使用此列來避免傳送第二封電子郵件 (視重新啟動與電子郵件處理的時間而定)。

![防止多傳電子郵件](./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-message-processing.png)

背景工作角色 B 也會輪詢訂閱佇列，看看 Web API 服務方法是否在該處放置了任何代表新訂閱的工作項目。一旦找到這類項目，就會傳送確認電子郵件。

![訂閱佇列訊息處理](./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-subscribe-diagram.png)

<h2><a name="tables"></a><span  class="short-header">資料表</span>Azure 資料表</h2>


Azure 電子郵件服務應用程式會將資料儲存在 Azure 儲存體資料表中。Azure 資料表是一種 NoSQL 資料存放區，而不是 [Azure SQL Database][2] 之類的關聯式資料庫。因此，當效率和延展性比資料正規化與關係完整性更重要時，這將是不錯的選擇。例如，在此應用程式中，一個背景工作角色會在每次有佇列工作項目建立時就建立一列，而另一個背景工作角色會在每次有電子郵件傳出時就擷取並更新一列，這時如果使用關聯式資料庫，就可能會出現效能瓶頸。此外，Azure 資料表也比 Azure SQL 便宜。如需 Azure 資料表的詳細資訊，請參閱[本系列的最後一個教學課程](/en-us/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/)最後列出的資源。

下列各節將說明此 Azure 電子郵件服務應用程式所用之各項 Azure 資料表的內容。如需各資料表和其彼此關係的圖解，請參閱本頁稍後的
[Azure 電子郵件服務資料圖](#datadiagram)。

### mailinglist 資料表

`mailinglist` 資料表中儲存關於郵寄清單和郵寄清單有哪些訂閱者的資訊。(Azure 資料表命名慣例最佳做法為全都使用小寫字母。)
系統管理員可以使用網頁來建立及編輯郵寄清單，而用戶端和訂閱者可以使用一組網頁和一個服務方法來進行訂閱和取消訂閱。

在 NoSQL 資料表中，不同的列可以有不同的結構描述，這項彈性使得一個資料表可以儲存在關聯式資料庫中要有多個資料表才能儲存的資料。例如，若要在 SQL Database 中儲存郵寄清單資料，您可能得使用三個資料表：`mailinglist` 資料表 (用來儲存清單的相關資訊)、`subscriber` 資料表 (用來儲存訂閱者的相關資訊)，以及 `mailinglistsubscriber` 資料表 (用來儲存郵寄清單與訂閱者的關聯)。在此應用程式的 NoSQL 資料表中，所有這些功能全都整合到一個名為 `mailinglist`的資料表中。

在 Azure
資料表中，每列都有一個「資料分割索引鍵」**及一個「列索引鍵」**來唯一識別該列。資料分割索引鍵的用處是以邏輯方式將資料表分成多個資料分割。列索引鍵的用處則是在資料分割內唯一識別列。沒有次要索引；因此，若要確定應用程式可受調整，請務必將資料表設計成，您永遠可以利用查詢的 Where 子句來指定資料分割索引鍵與列索引鍵值。

`mailinglist` 資料表的資料分割索引鍵是郵寄清單的名稱。

`mailinglist` 資料表的列索引鍵可以是下列兩項之一：常數 "mailinglist" 或是訂閱者的電子郵件地址。列索引鍵為 "mailinglist" 的列將包含郵寄清單的相關資訊。列索引鍵為電子郵件地址的列將包含清單訂閱者的相關資訊。

換言之，列索引鍵為 "mailinglist" 的列相當於關聯式資料庫中的 `mailinglist` 資料表。列索引鍵 = 電子郵件地址的列相當於關聯式資料庫中的 `subscriber` 資料表與 `mailinglistsubscriber` 關聯資料表。

如此用一個資料表來滿足多種用途，將能達到更好的效能。若是在關聯式資料庫中，將必須讀取三張資料表，而且必須整理並交相比對三組列，而這需要花時間來完成。在這裡，只需讀取一張資料表，就會自動依資料分割索引鍵與列索引鍵順序來傳回其中的列。

下表顯示含郵寄清單資訊的列 (列索引鍵 = "MailingList") 會有哪些列屬性。

<table  border="1">

<tr  bgcolor="lightgray">
<th>屬性</th>

<th>資料類型</th>

<th>說明</th>

</tr>


<tr>
<td>PartitionKey</td>

<td>String</td>

<td>ListName：郵寄清單的唯一識別碼，例如：contoso1。此資料表的一般用處是擷取特定郵寄清單的所有資訊，因此使用清單名稱是將此資料表進行分割的高效率方式。</td>

</tr>


<tr>
<td>RowKey</td>

<td>String</td>

<td>常數 "mailinglist"。</td>

</tr>


<tr>
<td>Description</td>

<td>String</td>

<td>郵寄清單的說明，例如："Contoso University History Department announcements"。</td>

</tr>


<tr>
<td>FromEmailAddress</td>

<td>String</td>

<td>對此清單傳送之電子郵件中的 [收件者] 電子郵件地址，例如：donotreply@contoso.edu。</td>

</tr>


</table>

下表顯示含清單訂閱者相關資訊的列 (列索引鍵 = 電子郵件地址) 會有哪些列屬性。

<table  border="1">

<tr  bgcolor="lightgray">
<th>屬性</th>

<th>資料類型</th>

<th>說明</th>

</tr>


<tr>
<td>PartitionKey</td>

<td>String</td>

<td>ListName：郵寄清單的名稱 (唯一識別碼)，例如：contoso1。</td>

</tr>


<tr>
<td>RowKey</td>

<td>String</td>

<td>EmailAddress：訂閱者電子郵件地址，例如：student1@contoso.edu。</td>

</tr>


<tr>
<td>SubscriberGUID</td>

<td>String</td>

<td>在電子郵件地址新增至清單時產生。用於訂閱與取消訂閱連結中，以降低將別人的電子郵件地址訂閱或取消訂閱的可能性。<br  />
<br  />
 「訂閱」網頁與「取消」訂閱的某些查詢只會指定 PartitionKey 和此屬性。不使用 RowKey 就查詢資料分割，將使應用程式的延展性受限，因為當郵寄清單大小增加時，查詢需要的時間將更長。有個提升延展性的選項，就是新增查閱列並使其 RowKey 屬性含有 SubscriberGUID。例如，就每個電子郵件地址而言，一個列的 RowKey 可以是 "email:student1@domain.com"，而相同訂閱者另一列的 RowKey 可以是 "guid:6f32b03b-90ed-41a9-b8ac-c1310c67b66a"。要實現這點很簡單，因為資料分割內列上的不可部分完成批次交易很容易撰寫。我們希望在下個版本的範例應用程式中實作這點。如需詳細資訊，請參閱<a  href="http://msdn.microsoft.com/zh-tw/library/windowsazure/hh508997.aspx">真實世界：設計 Azure 資料表儲存體的可調整分割策略</a>
 (英文)
</td>

</tr>


<tr>
<td>Verified</td>

<td>Boolean</td>

<td>一開始為新的訂閱者建立列時，此值為 false。只有在新的訂閱者按一下歡迎電子郵件中的確認超連結，或系統管理員將它設為 true 之後，才會變更為 true。如果傳送訊息給清單時，訊息其中一個訂閱者的 Verified 值為 false，則不會傳送電子郵件給該訂閱者。</td>

</tr>


</table>

下列清單示範資料表中的資料可能長什麼樣子。

<table  border="1">
<tr>
<th  width="200" align="right" bgcolor="lightgray">資料分割索引鍵</th>

<td>contoso1</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">列索引鍵</th>

<td>mailinglist</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">說明</th>

<td>Contoso University History Department announcements</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">FromEmailAddress</th>

<td>donotreply@contoso.edu</td>

</tr>


</table>

* * *


<table  border="1">

<tr>
<th  width="200" align="right" bgcolor="lightgray">資料分割索引鍵</th>

<td>contoso1</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">列索引鍵</th>

<td>student1@domain.com</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">SubscriberGUID</th>

<td>6f32b03b-90ed-41a9-b8ac-c1310c67b66a</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">Verified</th>

<td>true</td>

</tr>


</table>

* * *


<table  border="1">

<tr>
<th  width="200" align="right" bgcolor="lightgray">資料分割索引鍵</th>

<td>contoso1</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">列索引鍵</th>

<td>student2@domain.com</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">SubscriberGUID</th>

<td>01234567-90ed-41a9-b8ac-c1310c67b66a</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">Verified</th>

<td>false</td>

</tr>


</table>

* * *


<table  border="1">

<tr>
<th  width="200" align="right" bgcolor="lightgray">資料分割索引鍵</th>

<td>fabrikam1</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">列索引鍵</th>

<td>mailinglist</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">說明</th>

<td>Fabrikam Engineering job postings</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">FromEmailAddress</th>

<td>donotreply@fabrikam.com</td>

</tr>


</table>

* * *


<table  border="1">

<tr>
<th  width="200" align="right" bgcolor="lightgray">資料分割索引鍵</th>

<td>fabrikam1</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">列索引鍵</th>

<td>applicant1@domain.com</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">SubscriberGUID</th>

<td>76543210-90ed-41a9-b8ac-c1310c67b66a</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">Verified</th>

<td>true</td>

</tr>


</table>

### message 資料表

`message`
資料表中儲存關於已排定要對郵寄清單傳送之訊息的資訊。系統管理員會使用網頁在此資料表中建立及編輯列，而背景工作角色會使用此資料表，將每封電子郵件的相關資訊從背景工作角色 A 傳遞至背景工作角色 B。

message 資料表的資料分割索引鍵是排定要傳送電子郵件的日期，格式為 yyyy-mm-dd。如此可使資料表在面對最常對此資料表執行的查詢 (也就是會選取 `ScheduledDate` 為今天或更早日期的列的查詢) 時，展現最佳效能。不過，這也會引起潛在的效能瓶頸，因為 Azure 儲存體資料表對於一個資料分割，設有每秒 500 個實體的輸送量上限。每遇到一封要傳送的電子郵件，應用程式就會在 `message` 資料表中寫入一列、讀取一列再刪除一列。因此，當一天就有 1,000,000 封電子郵件被排定要傳送時，最短可能處理時間就有兩小時，不管新增了多少個背景工作角色來處理增加的負載都一樣。

`message` 資料表的列索引鍵可以是下列兩項之一：常數 "message" 加上訊息的唯一索引鍵 (稱為 `MessageRef`)，或 `MessageRef` 值加上訂閱者的電子郵件地址。列索引鍵以 "message" 開頭的列會包含訊息的相關資訊，例如要傳送給的郵寄清單，以及應該傳送的時間。列索引鍵為 `MessageRef` 與電子郵件地址的列，則會包含傳送電子郵件給該電子郵件地址時所需的一切資訊。

在關聯式資料庫術語中，列索引鍵以 "message" 開頭的列相當於 `message` 資料表。列索引鍵 = `MessageRef` 加電子郵件地址的列，則相當於含 `mailinglist`、`message` 和 `subscriber` 資訊的聯結查詢檢視。

下表顯示 `message` 資料表中含訊息本身相關資訊的列會有哪些列屬性。

<table  border="1">

<tr  bgcolor="lightgray">
<th>屬性</th>

<th>資料類型</th>

<th>說明</th>

</tr>


<tr>
<td>PartitionKey</td>

<td>String</td>

<td>排定要傳送訊息的日期，格式為 yyyy-mm-dd。</td>

</tr>


<tr>
<td>RowKey</td>

<td>String</td>

<td>常數 "message" 加 <code>MessageRef</code>
 值。<code>MessageRef</code>
 是建立列時，從 <code>DateTime.Now</code>
 取得 <code>Ticks</code>
 值而建立的唯一值。<br  />
<br  />
注意：應該準備好大量多執行緒、多執行個體的應用程式，以處理使用 Ticks 時出現的重複 RowKey 例外狀況。Ticks 不保證具唯一性。</td>

</tr>


<tr>
<td>ScheduledDate</td>

<td>Date</td>

<td>排定要傳送訊息的日期。(與 <code>PartitionKey</code>
 相同，但採用 Date 格式。)</td>

</tr>


<tr>
<td>SubjectLine</td>

<td>String</td>

<td>電子郵件的主旨列。</td>

</tr>


<tr>
<td>ListName</td>

<td>String</td>

<td>此訊息要傳送到的清單。</td>

</tr>


<tr>
<td>Status</td>

<td>String</td>

<td><ul>
<li>"Pending" -- 背景工作角色 A 尚未開始建立佇列訊息來排定電子郵件。</li>

<li>"Queuing" -- 背景工作角色 A 已開始建立佇列訊息來排定電子郵件。</li>

<li>"Processing" -- 背景工作角色 A 已為清單中的所有電子郵件建立佇列工作項目，但是尚未傳送所有電子郵件。</li>

<li>"Completed" -- 背景工作角色 B 已完成處理所有佇列工作項目 (已傳送所有電子郵件)。已完成的列會封存在 <code>messagearchive</code>
 資料表中，稍後會有說明。我們希望在下一版中使此屬性採用 <code>enum</code>
 格式。</li>
</ul>
</td>

</tr>


</table>

背景工作角色 A 在為待傳送給清單的電子郵件建立佇列訊息時，會在 `message` 資料表中建立電子郵件列。背景工作角色 B 在傳送電子郵件時，則會將電子郵件列移至 `messagearchive` 資料表，並將 `EmailSent` 屬性更新為 `true`。當處於 Processing 狀態的訊息其所有電子郵件列都已被封存時，背景工作角色 A 會將狀態設為 Completed，並將 `message` 列移至 `messagearchive` 資料表。

下表顯示 `message` 資料表中的電子郵件列會有哪些列屬性。

<table  border="1">

<tr  bgcolor="lightgray">
<th>屬性</th>

<th>資料類型</th>

<th>說明</th>

</tr>


<tr>
<td>PartitionKey</td>

<td>String</td>

<td>排定要傳送訊息的日期，格式為 yyyy-mm-dd。</td>

</tr>


<tr>
<td>RowKey</td>

<td>String</td>

<td><code>MessageRef</code>
 值以及目的地電子郵件地址 (來自 <code>mailinglist</code>
 資料表 <code>subscriber</code>
)。
</td>

</tr>


<tr>
<td>MessageRef</td>

<td>Long</td>

<td>與 <code>RowKey</code>
 的 <code>MessageRef</code>
 元件相同。</td>

</tr>


<tr>
<td>ScheduledDate</td>

<td>Date</td>

<td>已排定的日期，來自 <code>message</code>
 資料表的 <code>message</code>
 列。(與 <code>PartitionKey</code>
 相同，但採用 Date 格式。)</td>

</tr>


<tr>
<td>SubjectLine</td>

<td>String</td>

<td>電子郵件主旨列，來自 <code>message</code>
 資料表的 <code>message</code>
列。</td>

</tr>


<tr>
<td>ListName</td>

<td>String</td>

<td>郵寄清單名稱，來自 <code>mailinglist</code>
 資料表。</td>

</tr>


<tr>
<td>From EmailAddress</td>

<td>String</td>

<td>[寄件者] 電子郵件地址，來自 <code>mailinglist</code>
 資料表的 <code>mailinglist</code>
 列。</td>

</tr>


<tr>
<td>EmailAddress</td>

<td>String</td>

<td>電子郵件地址，來自 <code>mailinglist</code>
 資料表的 <code>subscriber</code>
 列。</td>

</tr>


<tr>
<td>SubscriberGUID</td>

<td>String</td>

<td>訂閱者 GUID，來自 <code>mailinglist</code>
 資料表的 <code>subscriber</code>
 列。</td>

</tr>


<tr>
<td>EmailSent</td>

<td>Boolean</td>

<td>False 表示尚未傳送電子郵件；true 表示已傳送電子郵件。</td>

</tr>


</table>

這些列中有重複的資料 (在關聯式資料庫中，這通常您是會避免的情況)。但是在此情況下，您是以資料重複的一些缺點換得更大的處理效率與延展性。因為電子郵件所需的所有資料都呈現在其中一列，所以背景工作角色 B 只需讀取一列，即可在從佇列中取出工作項目的同時就傳送電子郵件。

您可能在想，那麼電子郵件的本文又從何而來。這些列並沒有電子郵件本文所在檔案的 Blob 參考，因為該值是衍生自 `MessageRef` 值。例如，如果 `MessageRef` 為 634852858215726983，則 Blob 會命名為 634852858215726983.htm 和 634852858215726983.txt。

下列清單示範資料表中的資料可能長什麼樣子。

<table  border="1">

<tr>
<th  width="200" align="right" bgcolor="lightgray">資料分割索引鍵</th>

<td>2012-10-15</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">列索引鍵</th>

<td>message634852858215726983</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">MessageRef</th>

<td>634852858215726983</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">ScheduledDate</th>

<td>2012-10-15</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">SubjectLine</th>

<td>New lecture series</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">ListName</th>

<td>contoso1</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">Status</th>

<td>Processing</td>

</tr>


</table>

* * *


<table  border="1">

<tr>
<th  width="200" align="right" bgcolor="lightgray">資料分割索引鍵</th>

<td>2012-10-15</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">列索引鍵</th>

<td>634852858215726983student1@contoso.edu</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">MessageRef</th>

<td>634852858215726983</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">ScheduledDate</th>

<td>2012-10-15</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">SubjectLine</th>

<td>New lecture series</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">ListName</th>

<td>contoso1</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">FromEmailAddress</th>

<td>donotreply@contoso.edu</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">EmailAddress</th>

<td>student1@contoso.edu</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">SubscriberGUID</th>

<td>76543210-90ed-41a9-b8ac-c1310c67b66a</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">EmailSent</th>

<td>true</td>

</tr>


</table>

* * *


<table  border="1">

<tr>
<th  width="200" align="right" bgcolor="lightgray">資料分割索引鍵</th>

<td>2012-10-15</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">列索引鍵</th>

<td>634852858215726983student2@contoso.edu</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">MessageRef</th>

<td>634852858215726983</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">ScheduledDate</th>

<td>2012-10-15</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">SubjectLine</th>

<td>New lecture series</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">ListName</th>

<td>contoso1</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">FromEmailAddress</th>

<td>donotreply@contoso.edu</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">EmailAddress</th>

<td>student2@contoso.edu</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">SubscriberGUID</th>

<td>12345678-90ed-41a9-b8ac-c1310c679876</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">EmailSent</th>

<td>true</td>

</tr>


</table>

* * *


<table  border="1">

<tr>
<th  width="200" align="right" bgcolor="lightgray">資料分割索引鍵</th>

<td>2012-11-15</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">列索引鍵</th>

<td>message124852858215726999</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">MessageRef</th>

<td>124852858215726999</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">ScheduledDate</th>

<td>2012-11-15</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">SubjectLine</th>

<td>New job postings</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">ListName</th>

<td>fabrikam</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">Status</th>

<td>Pending</td>

</tr>


</table>

### messagearchive 資料表

有個策略可確保查詢會有效率地執行 (尤其是當您必須在 `PartitionKey` 和 `RowKey` 以外的其他欄位上搜尋時)，那就是限制資料表的大小。背景工作角色 A 中有個查詢會查看是否已傳送某個訊息的所有電子郵件，而該查詢需要在 `message` 資料表中尋找 `EmailSent` = false 的電子郵件列。`EmailSent` 值並不在 PartitionKey 或 RowKey 中，所以當訊息具有大量電子郵件列時，這不會是有效率的查詢。 因此，傳送電子郵件時，應用程式會將電子郵件列移至 `messagearchive` 資料表。如此一來，該查詢在檢查是否已傳送某個訊息的所有電子郵件時，就只須查詢 message 資料表的 `PartitionKey` 與 `RowKey`，原因是只要它發現某個訊息有任何電子郵件列，就表示有未傳送的訊息，而該訊息就不能標示為 `Complete`。

`messagearchive` 資料表中列的結構描述與 `message` 資料表中列的結構描述相同。視您想要如何用此封存資料做什麼而定，您可以減少針對每列儲存的屬性數，並刪除超過特定年齡的列，來限制此封存資料的大小與成本。

<h2><a name="queues"></a><span  class="short-header">佇列</span>Azure 佇列</h2>


Azure 佇列可促進這個多層式應用程式的各層之間，以及後端階層中各背景工作角色之間的通訊。佇列會用於背景工作角色 A 與背景工作角色 B 之間的通訊，讓應用程式變得可調整。背景工作角色 A 可能在 Message 資料表中為每封電子郵件各建立一列，而背景工作角色 B 則可能會掃描資料表，找出代表尚未傳送之電子郵件的列，但您將無法新增更多背景工作角色 B 執行個體。使用資料表列來協調背景工作角色 A 與背景工作角色 B 之間的工作有個問題，就是您沒辦法確保任一資料表列都只會被一個背景工作角色執行個體挑中處理。佇列則可提供該保證。當背景工作角色執行個體從佇列中取出工作項目時，佇列服務會確定沒有其他背景工作角色執行個體可以提取該工作項目。Azure 的這項獨家釋出功能有助於將工作負載分攤給背景工作角色的多個執行個體。

Azure 也提供服務匯流排佇列服務。如需 Azure 儲存體佇列與服務匯流排佇列的詳細資訊，請參閱[本系列的最後一個教學課程](/en-us/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/)最後列出的資源。

Azure 電子郵件服務應用程式使用兩個佇列，分別名為 `AzureMailQueue` 和 `AzureMailSubscribeQueue`。

### AzureMailQueue

`AzureMailQueue` 佇列會協調傳送電子郵件到電子郵件清單的順序。背景工作角色 A 會針對每封要傳送的電子郵件各放置一個工作項目到佇列中，而背景工作角色 B 則會從佇列中提出工作項目並傳送電子郵件。

佇列工作項目包含一個逗點分隔字串，字串中的每個分隔項目依序代表訊息 (`message` 資料表的資料分割索引鍵) 的已排定日期、`MessageRef` 與 `EmailAddress` 值 (`message` 資料表的列索引鍵) 以及一個旗標 (指出項目是否是在背景工作角色當機而重新啟動之後建立)，例如：

      2012-10-15,634852858215726983,student1@contoso.edu,0

背景工作角色 B 會使用這些值來查閱 `message` 資料表中含傳送電子郵件時所需之一切資訊的列。如果重新啟動旗標指出有重新啟動的情況，則背景工作 B 會先確定尚未傳送電子郵件，然後才傳送電子郵件。

當流量突然暴增時，雲端服務可以經過重新設定，以便具現化多個背景工作角色 B 執行個體，並讓這每個執行個體都可以獨立從佇列中提出工作項目。

### AzureMailSubscribeQueue

`AzureMailSubscribeQueue`
佇列會協調傳送訂閱確認電子郵件的順序。服務方法在收到服務方法呼叫時，會在佇列上放置一個工作項目。背景工作角色 B 會從佇列中提出工作項目，並傳送訂閱確認電子郵件。

佇列工作項目包含訂閱者 GUID。此值會唯一識別電子郵件地址和要讓它訂閱的清單，這些就是背景工作角色 B 傳送確認電子郵件時所需的一切資訊。如稍早所述，要做這個動作，就需要查詢不在 `PartitionKey` 或 `RowKey` 中的欄位，而這樣做很沒有效率。若要使應用程式更可調整，則必須重新建構 `mailinglist` 資料表，使 `RowKey` 中包含訂閱者
GUID。

<h2><a name="datadiagram"></a><span  class="short-header">資料圖</span>Azure 電子郵件服務資料圖</h2>


下圖顯示各資料表與佇列之間的關係。

![Azure 電子郵件服務應用程式的資料圖](./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-datadiagram.png)

<h2><a name="blobs"></a><span  class="short-header">Blob</span>Azure Blob</h2>


Blob 是指「二進位大型物件」。Azure Blob 服務提供一種在雲端上傳和儲存檔案的方法。如需 Azure Blob 的詳細資訊，請參閱[本系列的最後一個教學課程](/en-us/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/)最後列出的資源。

Azure 郵件服務系統管理員會將電子郵件本文以 HTML 形式放置於 *.htm* 檔案中，或以純文字形式放置於 *.txt* 檔案中。要排定電子郵件時，他們會在 **建立訊息** 網頁中上傳這些檔案，而該頁面的 ASP.NET MVC 控制器就會將上傳的檔案儲存在 Azure Blob 中。

Blob 是儲存在 Blob 容器中，就像檔案是儲存在資料夾中一樣。Azure 郵件服務應用程式是使用單一 Blob 容器，名為 **azuremailblobcontainer**。容器中各 Blob 的名稱是串連 MessageRef 值與副檔名所洐生出來的，例如：634852858215726983.htm 和 634852858215726983.txt。

由於 HTML 和純文字訊息基本上都是字串，因此我們原本也可將應用程式設計為將電子郵件訊息本文儲存在 `Message` 資料表中，而非 Blob 中。不過，資料表列中的屬性有 64K 的大小限制，因此使用 Blob 可避開該電子郵件本文大小限制。(64K 只是屬性的總大小上限；如果排除掉編碼額外負荷，則可在屬性中儲存的字串大小上限實際上比較接近 48k。)

<h2><a name="wawsvswacs"></a><span  class="short-header">雲端服務與網站</span>Azure 雲端服務與 Azure 網站</h2>


當您下載 Azure 電子郵件服務時，其已設定成讓前端和後端全都在單一 Azure 雲端服務中執行。

![應用程式架構概觀](./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-architecture-overview.png)

替代的架構是在 Azure 網站中執行前端。

![替代的應用程式架構](./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-alternative-architecture.png)

將所有元件保留在雲端服務中，可讓設定與部署工作變簡單。如果您建立的應用程式會讓 ASP.NET MVC 前端位於 Azure 網站中，則您將有兩個部署，一個位於 Azure 網站，而另一個則位於 Azure 雲端服務。此外，Azure 雲端服務 Web 角色還提供 Azure 網站所無法提供的下列功能：

* 支援自訂與萬用字元憑證。
* 完整控制 IIS 設定方式。許多 IIS 功能在 Azure 網站上無法啟用。有了 Azure Web   角色，您可以定義一個啟動命令，並讓該命令執行 [AppCmd][3] 程式來修改無法透過 *Web.config* 檔案來設定的 IIS
  設定。如需詳細資訊，請參閱[如何在 Azure 中設定 IIS 元件][4]和[如何封鎖特定 IP 位址不讓它們存取 Web 角色][5]。
* 支援自動調整 Web 應用程式
  (說明請見[自動調整應用程式區塊](/en-us/develop/net/how-to-guides/autoscaling/))。
* 能夠執行權限提高的指令碼來安裝應用程式、修改登錄設定、安裝效能計數器等等。
* 與 [Azure Connect][6] 和 [Azure 虛擬網路][7]搭配使用的網路隔離。
* 進行遠端桌面存取來做偵錯與進階診斷。
* 透過 [虛擬 IP 交換][8]進行輪流升級。此功能會交換預備部署與生產部署的內容。

替代的架構可能具有一些成本效益，因為就相等容量而言，Azure 網站可能比雲端服務中執行的 Web 角色更便宜。本系列稍後的教學課程將說明這兩個架構之間相異的實作詳細規格。

如需關於如何在 Azure 網站與 Azure 雲端服務之間做出選擇的詳細資訊，請參閱 [Azure 執行模型][9] (英文)。

<h2><a name="cost"></a><span  class="short-header">成本</span>成本</h2>


本節概略介紹在 Azure 執行範例應用程式所需的成本，其中的費率乃是 2012 年 12 月發表本教學課程時的有效費率。在根據成本做出任何商業決策之前，請務必到下列頁面查看最新費率：

* [Azure 定價計算機][10]
* [SendGrid Azure (英文)][11]

成本會受您決定要維護的 Web 與背景工作角色執行個體數目所影響。為了達到 [Azure 雲端服務 99.95% 的服務等級協定 (SLA)][12]，您必須針對每個角色各部署兩個以上的執行個體。至少必須執行兩個角色執行個體的原因之一，是因為執行您應用程式的虛擬機器每個月會因為要進行作業系統升級，而重新啟動兩次左右。(如需作業系統升級的詳細資訊，請參閱[角色執行個體由於作業系統升級而重新啟動][13]
(英文)。)

在此範例中，兩個背景工作角色所執行的工作並無時間上的急迫性，不需要達到 99.5% 的 SLA。因此，只要工作負載用一個執行個體就能應付，只執行每個背景工作角色的單一執行個體並無不妥。Web 角色執行個體則有時間上的急迫性，也就是說，使用者預期網站不會有任何停機的情況發生，因此生產應用程式應該至少有兩個 Web 角色執行個體。

下表顯示 Azure 電子郵件服務範例應用程式預設架構的成本 (假設工作負載僅達最低水準)。所示的成本是根據使用一個額外小型 (共用) 虛擬機器大小而來。您建立 Visual Studio 雲端專案時的預設虛擬機器大小很小，比這額外小型大小大約貴了六倍。

<table  border="1">

<tr  bgcolor="lightgray">
<th>元件或服務</th>

<th>費率</th>

<th>每月成本</th>

</tr>


<tr>
<td>Web 角色</td>

<td>2 個執行個體，以額外小型執行個體而言，費率為 $.02 美元/小時</td>

<td>$29.00 美元</td>

</tr>


<tr>
<td>背景工作角色 A (排定要傳送的電子郵件)</td>

<td>1 個執行個體，以額外小型執行個體而言，費率為 $.02 美元/小時</td>

<td>$14.50 美元
</td>

</tr>


<tr>
<td>背景工作角色 B (傳送電子郵件)</td>

<td>1 個執行個體，以額外小型執行個體而言，費率為 $.02 美元/小時</td>

<td>$14.50 美元</td>

</tr>


<tr>
<td>Azure 儲存體交易</td>

<td>每個月 1 百萬筆交易，費率為 $0.10 美元/百萬筆 (每個查詢都算是一筆交易；背景工作角色 A 會持續查詢資料表，看看是否有需要傳送的訊息。應用程式也是設定成將診斷資料寫入 Azure 儲存體，而且每次這樣做，即為一筆交易。)</td>

<td>$0.10 美元</td>

</tr>


<tr>
<td>Azure 本機備援儲存體</td>

<td>25 GB $2.33 美元 (包括存放應用程式資料表與診斷資料的儲存體。)</td>

<td>$2.33 美元</td>

</tr>


<tr>
<td>頻寬</td>

<td>5 GB 免費出口流量</td>

<td>免費</td>

</tr>


<tr>
<td>SendGrid</td>

<td>Azure 客戶每個月可以免費傳送 25,000 封電子郵件</td>

<td>免費</td>

</tr>


<tr>
<td  colspan="2">總計</td>

<td>$60.43 美元</td>

</tr>


</table>

您可以看見，角色執行個體為整體成本中的主要部分。角色執行個體即使停止也會產生成本，因此您必須刪除角色執行個體，才能不產生任何費用。節省成本的方法之一為將背景工作角色 A 與背景工作角色 B 中的所有程式碼都移到一個背景工作角色中。在這些教學課程中，我們刻意選擇實作兩個背景工作執行個體，以便簡化向外延展的過程。背景工作角色 B 執行的工作是由 Azure 佇列服務協調，這表示您可以僅增加背景工作角色 B 的執行個體數目，向外延展該角色。(背景工作角色 B 為高負載條件的限制因素。) 背景工作角色 A 執行的工作不是由佇列協調，因此您無法執行背景工作角色 A 的多個執行個體。如果這兩個背景工作角色合併，而您想要啟用向外延展，則您會需要實作一個機制，確保背景工作角色 A 工作只會在一個執行個體中執行。([CloudFx][14] (英文)就提供了這樣一個機制。請參閱 [WorkerRole.cs 範例][15] (英文)。)

也可以將這兩個背景工作角色中的所有程式碼都移到 Web 角色中，讓一切都在 Web 角色中執行。不過，在 ASP.NET 執行背景工作並不受支援，也不被視為健全，而且此架構將使延展性變複雜。如需詳細資訊，請參閱[在 ASP.NET 實作週期性背景工作的危險][16] (英文)。另請參閱[如何在 Azure 結合背景工作與 Web 角色][17] (英文) 和[將多個 Azure 背景工作角色結合成一個 Azure Web 角色][18] (英文)。

另一個使成本降低的替代架構是使用[自動調整應用程式區塊](/en-us/develop/net/how-to-guides/autoscaling/)，以僅在已排定的期間自動部署背景工作角色，並在工作完成時加以刪除。如需自動調整的詳細資訊，請參閱[本系列的最後一個教學課程](/en-us/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/)最後的連結。

未來的 Azure 可能會針對排定的重新開機提供通知機制，讓您能夠在重新開機時間範圍僅讓一個額外的 Web 角色執行個體運轉上線。您將達不到 99.95 SLA，但可以減少幾乎一半的成本，並確保您的 Web 應用程式在重新開機期間仍然可用。

<h2><a name="auth"></a><span  class="short-header">驗證與授權</span>驗證與授權</h2>


在生產應用程式中，您會實作驗證與授權機制，如 ASP.NET MVC Web 前端的 ASP.NET 成員資格系統，包括 ASP.NET Web API 服務方法。也有其他選項 (例如使用共用密鑰) 可保護 Web API 服務方法的安全。範例應用程式中省略了驗證與授權功能，以方便設定和部署。(本系列的第二個教學課程將說明如何實作 IP 限制，讓未獲授權的人員無法使用您部署至雲端的應用程式。)

如需關於如何在 ASP.NET MVC Web 專案中實作驗證與授權的詳細資訊，請參閱下列資源：

* [ASP.NET Web API 中的驗證與授權][19]
* [音樂市集第 7 部分：成員資格與授權][20]

**注意**：我們原本計劃納入一個機制來使用共用密鑰保護 Web API 服務方法，但未及時在初次發佈時完成。因此，第三個教學課程不會說明如何建置訂閱程序的 Web API 控制器。我們希望在本教學課程的下一版本中納入實作安全訂閱程序的指示。在那之前，您可以使用系統管理員網頁讓電子郵件地址訂閱清單，藉以測試應用程式。

<h2><a name="nextsteps"></a><span  class="short-header">後續步驟</span>後續步驟</h2>


在[下一個教學課程](/en-us/develop/net/tutorials/multi-tier-web-site/2-download-and-run/)中，您將下載範例專案、設定開發環境、設定適用於環境的專案，然後在本機與雲端測試專案。在下列教學課程中，您將看到如何從頭建置專案。

如需關於使用 Azure 儲存體資料表、佇列與 Blob 的其他資源連結，請參閱[本系列的最後一個教學課程](/en-us/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/)的最後。

 
<div><a  href="/en-us/develop/net/tutorials/multi-tier-web-site/2-download-and-run/" class="site-arrowboxcta download-cta">教學課程 2</a></div>

 

[1]: http://social.technet.microsoft.com/wiki/contents/articles/11608.e-book-gallery-for-microsoft-technologies.aspx#ASPNETMultiTierWindowsAzureApplicationUsingStorageTablesQueuesandBlobs
[2]: http://msdn.microsoft.com/zh-tw/library/windowsazure/ee336279.aspx
[3]: http://www.iis.net/learn/get-started/getting-started-with-iis/getting-started-with-appcmdexe "appCmd"
[4]: http://msdn.microsoft.com/zh-tw/library/windowsazure/gg433059.aspx
[5]: http://msdn.microsoft.com/zh-tw/library/windowsazure/jj154098.aspx
[6]: http://msdn.microsoft.com/zh-tw/library/windowsazure/gg433122.aspx
[7]: http://msdn.microsoft.com/zh-tw/library/windowsazure/jj156007.aspx
[8]: http://msdn.microsoft.com/zh-tw/library/windowsazure/ee517253.aspx "VIP 交換"
[9]: http://www.windowsazure.com/zh-tw/manage/windows/fundamentals/compute/
[10]: http://www.windowsazure.com/zh-tw/pricing/calculator/
[11]: http://sendgrid.com/windowsazure.html
[12]: https://www.windowsazure.com/en-us/support/legal/sla/ "SLA"
[13]: http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx
[14]: http://nuget.org/packages/Microsoft.Experience.CloudFx "CloudFX"
[15]: http://code.msdn.microsoft.com/windowsazure/CloudFx-Samples-60c3a852/sourcecode?fileId=57087&pathId=528472169
[16]: http://haacked.com/archive/2011/10/16/the-dangers-of-implementing-recurring-background-tasks-in-asp-net.aspx
[17]: http://www.31a2ba2a-b718-11dc-8314-0800200c9a66.com/2010/12/how-to-combine-worker-and-web-role-in.html
[18]: http://www.31a2ba2a-b718-11dc-8314-0800200c9a66.com/2012/02/combining-multiple-azure-worker-roles.html
[19]: http://www.asp.net/web-api/overview/security/authentication-and-authorization/authentication-and-authorization-in-aspnet-web-api
[20]: http://www.asp.net/mvc/tutorials/mvc-music-store/mvc-music-store-part-7