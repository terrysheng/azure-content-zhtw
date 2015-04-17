<properties 
	pageTitle="Microsoft Azure API 應用程式資料連接器 | API 應用程式微服務" 
	description="了解如何建立 Microsoft Azure 資料連接器 API 應用程式，並將 API 應用程式新增至您的邏輯應用程式；微服務" 
	services="app-service-logic" 
	documentationCenter="" 
	authors="MSFTMan" 
	manager="dwrede" 
	editor=""/>

<tags
	ms.service="app-service-logic" 
	ms.workload="data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="deonhe"/>


# Microsoft Azure 應用程式服務中的資料連接器


## 什麼是新增資料 + 服務 API 應用程式連接器
應用程式和資料服務連接器是「API 應用程式」，可連接至各種資料服務應用程式，並可代表已驗證使用者執行數個 *Actions*。大部分的這些連接器也可以使用 *Trigger*進行設定。觸發程序是可在部分連接器上設定，以初始化工作流程的事件 (與 .NET Framework 中的事件類似)。例如，Dropbox 連接器的執行個體可以使用 *new file*觸發程序進行設定，每當新檔案加入受監視的 Dropbox 帳戶時，觸發程序便會執行。接著可以設定此觸發程序以初始化 *Action*，活動將 *Get*檔案並將它上傳至內部部署 SharePoint 清單。


以下是每個 Azure 組件庫上可用應用程式和資料服務連接器的簡短概觀。 

<table>
<tr>
<th> 名稱</th>
<th> 說明</th>
<th> 觸發程序</th>
<th>動作</th>

<tr>
<td>Azure 媒體服務
<td>Azure 媒體服務連接器可讓您建立端對端媒體工作流程，包括彈性、可調整的編碼、封裝及發佈。您也可以將傳送至各種電視、PC 和行動裝置端點的隨選和即時資料流，以安全的方式上傳、儲存、編碼及串流處理視訊或音訊內容。
<td>無
<td>無 
</tr>

<tr>
<td>Azure 行動服務
<td>Azure 行動服務連接器可讓您建立和修改資料，並呼叫自訂 API。
<td>無
<td><li>查詢
<li>插入
<li>刪除
<li>更新
<li>自訂 API 呼叫

</tr>

<tr>
<td>Azure 服務匯流排
<td>Azure 服務匯流排連接器可讓您從服務匯流排實體 (如佇列和主題) 傳送訊息，並接收來自服務匯流排實體 (例如佇列和訂閱) 的訊息。
<td>新增訊息
<td>傳送訊息
</tr>

<tr>
<td>Azure 儲存體 Blob
<td>Azure 儲存體 Blob 可讓您連接至 Blob 儲存體，並執行各種動作。
<td>已加入新檔案
<td><li>上傳 <li>取得 <li>刪除 <li>列出 <li>複製 <li>快照
</tr>

<tr>
<td>Azure 儲存體資料表
<td>Azure 儲存體資料表連接器可讓您連接至 Azure 儲存體資料表，並執行各種動作，例如取得實體、查詢實體、插入實體、更新實體、刪除實體和可擷取資料的觸發程序。
<td>新增實體
<td><li>取得實體
	<li>插入實體
	<li>刪除實體
	<li>更新實體
	<li>查詢

</tr>

<tr>
<td>Box
<td>Box 連接器可讓您連接至 Box，並在檔案上執行各種動作。 
<td>已加入新檔案
<td><li>上傳檔案
<li>取得檔案
<li>刪除檔案
<li>列出檔案
</tr>

<tr>
<td>Dropbox
<td>Dropbox 連接器可讓您連接至 Dropbox 並執行各種動作，例如上傳檔案、取得檔案、刪除檔案、列出檔案和可擷取資料的觸發程序。
<td>已加入新檔案
<td><li>上傳檔案
<li>取得檔案
<li>刪除檔案
<li>列出檔案
</tr>

<tr>
<td>HDInsight
<td>HDInsight 連接器可讓您在 Azure 上建立 Hadoop 叢集，並提交各種 Hadoop 工作，例如 Hive、Pig、MapReduce 和 Streaming MapReduce。使用此連接器，您可以微調叢集、提交工作，以及等待工作完成。
<td>N/A
<td><li>建立叢集
		<li>等候建立叢集
		<li>提交 Pig 工作
		<li>提交 Hive 工作
		<li>提交 MapReduce 工作
		<li>等候工作完成
		<li>刪除叢集
<li>提交 MapReduce 工作
<li>提交 Streaming MapReduce 工作

</tr>

<tr>
<td>Microsoft SQL
<td>Microsoft SQL 連接器可讓您建立和修改 Microsoft SQL Server 和 Azure SQL Database 資料表中的項目。
<td>N/A
<td><li>訊息：傳送、列出、取得和搜尋
	<li>電話號碼：購買、列出和取得當地和免付費電話號碼
</tr>

<tr>
<td>MongoDB
<td>MongoDB 連接器可讓您建立、更新、刪除和取得 MongoDB 集合中的文件。
<td>新增文件
<td>	<li>新增文件
		<li>更新文件
		<li>取得文件
		<li>更新插入文件
		<li>刪除文件
</tr>

<tr>
<td>Office 365
<td>Office 365 連接器可讓您傳送和接收電子郵件，並管理您的行事曆和連絡人。
<td>新增訊息
<td>	<li>傳送郵件
		<li>回覆郵件
		<li>傳送事件
		<li>新增連絡人
</tr>

<tr>
<td>OneDrive
<td>OneDrive 連接器可讓您連接至您個人的 Microsoft OneDrive 儲存體帳戶，並執行各種動作，例如上傳、取得、刪除、列出檔案。
<td>新增檔案
<td><li>檔案：上傳、刪除、列出、下載
</tr>

<tr>
<td>Oracle
<td>Oracle 資料庫連接器可讓您建立和修改 Oracle 資料庫資料表中的項目。
<td>新資料 (根據查詢區分)
<td><li>資料表：插入目標、 更新、選取來源、刪除來源
<li>呼叫預存程序
</tr>

<tr>
<td>SharePoint Online
<td>Microsoft SharePoint 連接器可讓您建立和修改 SharePoint Server 或 Microsoft SharePoint Online 中的文件和清單項目。
<td><li>新增文件
<li>新增清單項目
<td><li>文件庫：上傳、刪除和取得
<li>清單：插入項目

</tr>

<tr>
<td>SharePoint Server
<td>SharePoint Server 連接器可讓您管理 SharePoint Server 中的文件和清單項目。支援預設的認證、Windows 驗證和表單型驗證。您必須提供服務匯流排連線字串，並安裝內部部署接聽程式代理程式之後才能連線到伺服器。
<td><li>新增文件
<li>新增清單項目
<td><li>文件庫：上傳、刪除和取得
<li>清單：插入項目
</tr>
</table>


## 為何要使用連接器？

連接器可加速應用程式開發，甚至還可讓非開發人員建立功能完整的企業級應用程式，而不需要學習程式設計語言或撰寫任何程式碼。

現在您已了解應用程式 + 資料服務連接器可以執行的功能，我們來看看這些連接器的一些簡單使用案例。 

### 監視您的 Dropbox 並更新 SharePoint ###
想像您的公司為接收包含藍圖等大型檔案的建設公司。這些檔案通常太大而無法透過電子郵件處理，因此貴公司會設定 Dropbox 帳戶，並要求其客戶將藍圖放入 Dropbox。接著您可以要求每位員工不斷地檢查 Dropbox 資料夾是否有新藍圖，然後將它們上傳至您的 SharePoint Server，不過，您相信一定有更好的方法才對！幸運的是，您發現 Microsoft 最近發行了 Dropbox、Sharepoint 和其他資料服務的應用程式 + 資料服務連接器。您可以輕鬆地建立 Dropbox 和 SharePoint 的連接器、將它們新增至邏輯應用程式，以及設定它們將每個新檔案  從您的 Dropbox 帳戶上傳至您的 SharePoint 清單。由於 Dropbox 連接器具有 *new message*觸發程序，您可以用它來通知邏輯應用程式有可用的新檔案。Dropbox 連接器即可下載檔案。您便可設定 Sharepoint 連接器，使用 SharePoint *upload*動作將檔案上傳至 SharePoint 清單。您可以在無需撰寫任何程式碼的情況下執行這所有動作。  

讓我們開始吧。 

## 建立連接器

建立連接器的方式有兩種：

- 透過使用 Microsoft Azure 入口網站 (無需撰寫程式碼！)
- 透過使用 REST API。 

### 在 Microsoft Azure 入口網站中建立 SharePoint 連接器

1. 在 Azure 入口網站中，依序選取 [**新增**] > [**Web + 行動**] > [**Azure Marketplace**]
2. **搜尋**連接器，或從清單中選取連接器。選取之後，便會隨即開啟新分頁或視窗。選取 [**建立**]。 
3. 請輸入下列連接器的屬性： 
	<table>
	    <tr><th>屬性</th> <th>說明</th> </tr>
	    <tr><td>名稱</td> <td>為您的 API 應用程式輸入任何名稱。例如，您可以將它命名為 RulesDiscountTaxCode 或 APIAppValidateXML</td> </tr>
	    <tr><td>應用程式服務方案</td> <td>列出您的付款計劃。如果您需要更多或更少的資源，您可以加以變更</th> </td>
	    <tr><td>定價層</td> <td>可列出您 Azure 訂閱內定價類別的唯讀屬性。</td> </tr>
	    <tr><td>資源群組</td> <td>建立一個新的或使用現有的群組。使用資源群組說明這個屬性</td> </tr>
	    <tr><td>訂閱</td> <td>可列出您目前訂閱的唯讀屬性</td> </tr>
	    <tr><td>位置</td> <td>裝載您 Azure 服務的地理位置 </td></tr>
        <tr><td>新增至開始面板</td> <td>選取此選項將 API 應用程式新增至您的開始面板 (首頁)</td></tr>
	</table> 
4. 選取 [**建立**]。將會建立您的連接器。它可能需要一些時間才能完成，且在連接器的建立過程中將會顯示主畫面。使用左側的通知功能表項目來監視您的連接器狀態。

既然您已經建立您的第一個連接器，請考慮建立使用該連接器的 Web、行動或邏輯應用程式。 


### 使用 REST API 建立連接器

[使用 REST API 建立連接器](http://go.microsoft.com/fwlink/p/?LinkId=529766)

## 將連接器新增至應用程式 
Microsoft Azure 應用程式服務會公開可以使用這些連接器的不同應用程式類型。例如，您可以建立 *Logic*應用程式，方法是在單一應用程式中 *logically*結合一或多個連接器。

若要在您的 *Logic*應用程式中使用連接器，您可以從清單中選取每個設定的連接器、將它加入您的設計工作流程、進行所需的組態變更，這樣您便可以開始使用連接器。 

若要依照這些步驟執行，您需要 Web 應用程式、行動應用程式或邏輯應用程式。如需特定步驟，請參閱 <>。應用程式可以使用後，請新增您的連接器。以下是執行方式：

若要將連接器新增至邏輯應用程式，請使用下列步驟： 

1. 在 Azure 入口網站開始面板 (首頁) 中，請移至 [**Marketplace**]，並搜尋您的  邏輯、行動或 Web 應用程式。 

	如果您打算建立新的應用程式，請搜尋邏輯應用程式、行動應用程式或 Web 應用程式。選取應用程式，並在新的分頁中選取 [**建立**]。[建立邏輯應用程式](app-service-logic-create-a-logic-app.md)  會列出步驟。 

2. 開啟您的應用程式，並選取 [**觸發程序和動作**]。 
3. 從 [**組件庫**] 中選取連接器。它便會新增至您的應用程式。
4. 設定連接器：
5. 每個連接器都會有其所連接服務和環境特有的屬性。輸入屬性的詳細資料。請注意，有些是選擇性的屬性。
6. 選取 [**確定**] 以儲存變更。


## 安全性
連接器會使用 OAuth 或使用者名稱和密碼。 


## 請參閱邏輯應用程式和 Web 應用程式
[什麼是邏輯應用程式？](app-service-logic-what-are-logic-apps.md) |
[ Azure 應用程式服務中的網站和 Web 應用程式](app-service-web-app-azure-portal.md) |



## 其他連接器

[BizTalk 整合連接器](app-service-logic-integration-connectors.md) |
[企業連接器](app-service-logic-enterprise-connectors.md) |
[企業對企業連接器](app-service-logic-b2b-connectors.md) |
[社交連接器](app-service-logic-social-connectors.md) |
[通訊協定連接器](app-service-logic-protocol-connectors.md) |
[應用程式和資料服務連接器](app-service-logic-data-connectors.md) |
[連接器和 API 應用程式清單](app-service-logic-connectors-list.md)

<!--HONumber=49-->