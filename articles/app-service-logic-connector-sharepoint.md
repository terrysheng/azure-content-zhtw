<properties 
   pageTitle="在邏輯應用程式中使用 SharePoint 連接器" 
   description="在邏輯應用程式中使用 SharePoint 連接器" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="anuragdalmia" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="03/20/2015"
   ms.author="vagarw"/>

# 在邏輯應用程式中使用 SharePoint 連接器

邏輯應用程式可以根據各種資料來源和提供項目連接器觸發，以在流程中取得和處理資料。Microsoft SharePoint 連接器可讓您與 Microsoft SharePoint Server/SharePoint Online 連線，並管理文件和清單項目。您可以執行各種動作，例如建立、更新、取得和刪除文件和清單項目。如果是內部部署 SharePoint Server，您可以提供服務匯流排連接字串做為連接器組態的一部分，並安裝內部部署接聽程式代理程式以連線到伺服器。

SharePoint Online 連接器和 SharePoint Server 連接器組件庫應用程式可提供觸發程序和動作做為與 SharePoint 互動的機制。

## 建立邏輯應用程式的 SharePoint Online 連接器

若要使用 SharePoint Online 連接器，您必須先建立 SharePoint Online 連接器 API 應用程式的執行個體。如下所示，完成此作業：

1. 使用 Azure 入口網站右下方的 [+新增] 選項開啟 Azure Marketplace。

2. 瀏覽至 [Web 與行動] > [API 應用程式]，並搜尋「SharePoint Online 連接器」。

3. 設定 SharePoint Online 連接器，並按一下 [建立]。下列是建立連接器所需提供的參數：

	<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>必要</b></td>
    <td><b>說明</b></td>
  </tr>
  <tr>
    <td>網站 URL</td>
    <td>是</td>
    <td>指定 SharePoint 網站的完整 URL。範例：https://microsoft.sharepoint.com/teams/wabstest </td>
  </tr>
  <tr>
    <td>文件庫 / 清單相對 URL</td>
    <td>是</td>
    <td>指定相對於 SharePoint 網站 URL、且可由連接器修改的文件庫/清單 URL。範例：清單/工作、共用文件。</td>
  </tr>
</table>![][1]


4. 完成後，您現在可以在相同的資源群組中建立邏輯應用程式，以便使用 SharePoint Online 連接器。

## 建立邏輯應用程式的 SharePoint Server 連接器

若要使用 SharePoint Server 連接器，您必須先建立 SharePoint Server 連接器 API 應用程式的執行個體。如下所示，完成此作業：

1. 使用 Azure 入口網站右下方的 [+新增] 選項開啟 Azure Marketplace。

2. 瀏覽至 [Web 與行動] > [API 應用程式]，並搜尋「SharePoint Server 連接器」。

3. 設定 SharePoint Server 連接器，並按一下 [建立]。下列是建立連接器所需提供的參數：

	<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>必要</b></td>
    <td><b>說明</b></td>
  </tr>
  <tr>
    <td>網站 URL</td>
    <td>是</td>
    <td>指定 SharePoint 網站的完整 URL。範例：https://microsoft.sharepoint.com/teams/wabstest </td>
  </tr>
  <tr>
    <td>驗證模式</td>
    <td>是</td>
    <td>指定要連線到 SharePoint 網站的驗證模式。允許的值包括：<br><br>
		預設<br>			
		WindowsAuthentication<br>
		FormBasedAuthentication。<br><br>

如果您要選擇預設認證，系統會使用執行 SharePoint Microservice 所用的預設認證，且不需要使用者名稱/密碼。其他驗證類型需要使用者名稱和密碼欄位。<br><br>請注意：不支援匿名驗證。</td>
  </tr>
  <tr>
    <td>使用者名稱</td>
    <td>否</td>
    <td>如果 [驗證] 模式不是 [預設]，請指定一個可連線到 SharePoint 網站的有效使用者名稱。</td>
  </tr>
  <tr>
    <td>密碼</td>
    <td>否</td>
    <td>如果 [驗證] 模式不是 [預設]，請指定一個可連線到 SharePoint 網站的有效密碼。</td>
  </tr>
  <tr>
    <td>文件庫 / 清單相對 URL</td>
    <td>是</td>
    <td>指定相對於 SharePoint 網站 URL、且可由連接器修改的文件庫/清單 URL。範例：清單/工作、共用文件。</td>
  </tr>
  <tr>
    <td>服務匯流排連接字串</td>
    <td>否</td>
    <td>這應該會是有效的服務匯流排命名空間連接字串。<br><br>

您必須在能夠存取 SharePoint Server 的伺服器上安裝接聽程式代理程式。<br>您可以移至您的 API 應用程式摘要頁面，然後按一下 [混合式連線] 來安裝代理程式。</td>
  </tr>
</table>![][2]

4. 完成後，您現在可以在相同的資源群組中建立邏輯應用程式，以便使用 SharePoint Server 連接器。
5. 您必須在能夠存取 SharePoint Server 的伺服器上安裝接聽程式代理程式。您可以移至您的 API 應用程式摘要頁面，然後按一下 [混合式連線] 來安裝代理程式

## 在邏輯應用程式中使用 SharePoint 連接器

建立 API 應用程式之後，您現在可以使用 SharePoint 連接器做為邏輯應用程式的觸發程序或動作。若要這麼做，您需要：

1. 建立新的邏輯應用程式，並選擇具有 SharePoint 連接器的相同資源群組。

2. 開啟 [觸發程序和動作] 以開啟邏輯應用程式設計工具，並設定您的流程。SharePoint 連接器就會出現在右側組件庫中的 [最近使用的] 區段。選取該項目。

3. 如果在啟動邏輯應用程式時就選取 SharePoint 連接器，它就會像觸發程序般運作，您可以使用連接器在 SharePoint 帳戶上採取其他動作。

4. 如果使用 SharePoint Online 連接器，則必須驗證和授權邏輯應用程式以代表您執行作業。若要啟動授權，請按一下 SharePoint 連接器上的 [授權]。

	![][3]

5. 按一下 [授權] 便會開啟 SharePoint 的 [驗證] 對話方塊。提供您要在其上執行作業的 SharePoint 帳戶登入詳細資料。

	![][4]
6. 對您的帳戶授與邏輯應用程式存取權限，以便代表您執行作業。 

	![][5]

7. 如果將 SharePoint 連接器設定為觸發程序，則會顯示觸發程序和其他動作清單，然後您可以選擇想要執行的適當作業。

	![][6]

	<b>已設定文件庫的相對 URL</b><br><br>

	![][7]

	<b>已設定文件清單的相對 URL</b>

	<b>注意：</b>對於下列觸發程序，系統會假設使用者應在連接器封裝設定中指定「共用文件、清單/工作」，其中「共用文件」是文件庫，而「清單/工作」則是清單。

##  觸發程序
如果您要起始邏輯應用程式，請使用觸發程序

**注意**：觸發程序將在讀取檔案後刪除它們。若要保留這些檔案，請提供封存位置的值。

### 1.共用文件中的新文件 (JSON)
當「共用文件」中有可用的新文件時，便會啟動此觸發程序。

**輸入：**

<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>必要</b></td>
    <td><b>說明</b></td>
  </tr>
  <tr>
    <td>檢視名稱</td>
    <td>否</td>
    <td>指定要挑選用來篩選文件的有效檢視。範例：「核准的訂單」。若要處理所有的現有文件，請將此欄位留空。</td>
  </tr>
  <tr>
    <td>封存位置</td>
    <td>否</td>
    <td>請指定相對於 SharePoint 網站的有效資料夾 URL，也就是封存已處理文件的位置。</td>
  </tr>
  <tr>
    <td>在封存中覆寫</td>
    <td>否</td>
    <td>勾選此選項即可覆寫封存路徑中的檔案 (如果已存在)。</td>
  </tr>
  <tr>
    <td>CAML 查詢</td>
    <td>否，進階</td>
    <td>指定要篩選文件的有效 CAML 查詢。範例：<Where><Geq><FieldRef Name='ID'/><Value Type='Number'>10</Value></Geq></Where></td>
  </tr>
</table>

**輸出：**<table> <tr> <td><b>名稱</b></td> <td><b>說明</b></td> </tr> <tr> <td>名稱</td> <td>文件的名稱。</td> </tr> <tr> <td>內容</td> <td>文件的內容。</td> </tr> <tr> <td>ContentTransferEncoding</td> <td>訊息的內容轉移編碼 ([無]|[base64])</ </tr> </table>


注意：[進階] 輸出屬性中會顯示文件項目的所有資料行。


###2.工作中的新項目 (JSON)
在「工作」清單中新增項目時，即會啟動此觸發程序。

**輸入：**<table> <tr> <td><b>名稱</b></td> <td><b>必要</b></td> <td><b>說明</b></td> </tr> <tr> <td>檢視名稱</td> <td>否</td> <td>指定用來篩選清單中項目的有效檢視。範例：「核准的訂單」。若要處理新的項目，請將此欄位留空。</td> </tr> <tr> <td>封存位置</td> <td>否</td> <td>指定相對於 SharePoint 網站的有效資料夾 URL，也就是封存已處理清單項目的位置。</td> </tr> <tr> <td>Caml 查詢</td> <td>否，進階</td> <td>指定要篩選清單項目的有效 CAML 查詢。範例：<Where><Geq><FieldRef Name='ID'/><Value Type='Number'>10</Value></Geq></Where></td> </tr> </table>


**輸出：**

<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>說明</b></td>
  </tr>
  <tr>
    <td>系統會以動態方式填入清單中的資料行，並顯示在輸出參數中。</td>
    <td>&#160;</td>
  </tr>

</table>


###3.共用文件中的新文件 (XML)

當「共用文件」中有可用的新文件時，便會啟動此觸發程序。新文件會以 XML 訊息的方式傳回。

**輸入：**

<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>必要</b></td>
    <td><b>說明</b></td>
  </tr>
  <tr>
    <td>檢視名稱</td>
    <td>否</td>
    <td>指定要挑選用來篩選文件的有效檢視。範例：「核准的訂單」。若要處理所有的現有文件，請將此欄位留空。</td>
  </tr>
  <tr>
    <td>封存位置</td>
    <td>否</td>
    <td>請指定相對於 SharePoint 網站的有效資料夾 URL，也就是封存已處理文件的位置。</td>
  </tr>
  <tr>
    <td>在封存中覆寫</td>
    <td>否</td>
    <td>勾選此選項即可覆寫封存路徑中的檔案 (如果已存在)。</td>
  </tr>
  <tr>
    <td>CAML 查詢</td>
    <td>否，進階</td>
    <td>指定要篩選文件的有效 CAML 查詢。範例：<Where><Geq><FieldRef Name='ID'/><Value Type='Number'>10</Value></Geq></Where></td>
  </tr>
</table>

**輸出：**

<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>說明</b></td>
  </tr>
  <tr>
    <td>內容</td>
    <td>文件的內容。</td>
  </tr>
  <tr>
    <td>ContentTransferEncoding</td>
    <td>訊息的內容傳送編碼。([無]|[base64])</td>
  </tr>
</table>


###4.工作中的新項目 (XML)

在「工作」清單中新增項目時，即會啟動此觸發程序。新的清單項目會以 XML 訊息的方式傳回。

**輸入：**

<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>必要</b></td>
    <td><b>說明</b></td>
  </tr>
  <tr>
    <td>檢視名稱</td>
    <td>否</td>
    <td>指定要用來在清單中篩選項目的有效檢視。範例：「核准的訂單」。若要處理新的項目，請將此欄位留空。</td>
  </tr>
  <tr>
    <td>封存位置</td>
    <td>否</td>
    <td>請指定相對於 SharePoint 網站的有效資料夾 URL，也就是封存已處理清單項目的位置。</td>
  </tr>
  <tr>
    <td>CAML 查詢</td>
    <td>否，進階</td>
    <td>指定要篩選清單項目的有效 CAML 查詢。範例：<Where><Geq><FieldRef Name='ID'/><Value Type='Number'>10</Value></Geq></Where></td>
  </tr>
</table>


**輸出：**

<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>說明</b></td>
  </tr>
  <tr>
    <td>內容</td>
    <td>文件的內容。</td>
  </tr>
  <tr>
    <td>ContentTransferEncoding</td>
    <td>訊息的內容傳送編碼。([無]|[base64])</td>
  </tr>
</table>


##  動作
對於下列動作，系統會假設使用者應在連接器封裝設定中指定「共用文件、清單/工作」，其中「共用文件」是文件庫，而「清單/工作」則是清單。

###1.上傳到共用文件 (JSON)

這個動作會將新文件上載到「共用文件」。輸入為包含文件庫中所有資料行欄位的強型別 JSON 物件。

**輸入：**

<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>必要</b></td>
    <td><b>說明</b></td>
 </tr>
  <tr>
    <td>名稱</td>
    <td>是</td>
    <td>文件的名稱。</td>
  </tr>
  <tr>
    <td>內容</td>
    <td>是</td>
    <td>文件的內容。</td>
  </tr>
  <tr>
    <td>ContentTransferEncoding</td>
    <td>是</td>
    <td>訊息的內容傳送編碼。([無]|[base64])</td>
  </tr>
  <tr>
    <td>強制覆寫</td>
    <td>是</td>
    <td>如果設定為 TRUE，且指定名稱的文件存在的情況下，文件將會被覆寫。</td>
  </tr>
  <tr>
    <td>ReqParam1*</td>
    <td>是</td>
    <td>這是要在文件庫中加入文件的其中一個必要參數。</td>
  </tr>
  <tr>
    <td>ReqParam2*</td>
    <td>是</td>
    <td>這是要在文件庫中加入文件的其中一個必要參數。</td>
  </tr>
  <tr>
    <td>OptionalParam1*</td>
    <td>否。進階</td>
    <td>這是要在文件庫中加入文件的其中一個選擇性參數。</td>
  </tr>
  <tr>
    <td>OptionalParam2*</td>
    <td>否。進階</td>
    <td>這是要在文件庫中加入文件的其中一個選擇性參數。</td>
  </tr>
</table>

<b>注意：</b>系統會以動態方式填入文件庫中的所有參數。可以看見必要參數，而選擇性參數則在進階區段中。


**輸出：**

<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>說明</b></td>
  </tr>
  <tr>
    <td>ItemId</td>
    <td>加入文件庫的文件項目識別碼。</td>
  </tr>
  <tr>
    <td>狀態</td>
    <td>成功上傳文件會傳回狀態碼 200 (確定)。</td>
  </tr>
</table>


 

###2.從共用文件取得 (JSON)
這個動作會從指定文件相對 URL (資料夾結構) 的文件庫中取得文件。


**輸入：**

<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>必要</b></td>
    <td><b>說明</b></td>
  </tr>
  <tr>
    <td>文件相對 URI</td>
    <td>否</td>
    <td>指定相對於「共用文件」的文件 URL。範例：myspec1、myfolder/orders</td>
  </tr>
</table>


**輸出：**

<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>說明</b></td>
  </tr>
  <tr>
    <td>內容</td>
    <td>文件內容</td>
  </tr>
  <tr>
    <td>ContentTransferEncoding</td>
    <td>訊息的內容傳送編碼。([無]|[base64])</td>
  </tr>
  <tr>
    <td>狀態</td>
    <td>成功執行動作會傳回狀態碼 200 (確定)。</td>
  </tr>
  <tr>
    <td>Param1*</td>
    <td>這是位於文件庫中文件的其中一個參數。</td>
  </tr>
  <tr>
    <td>Param2*</td>
    <td>這是位於文件庫中文件的其中一個參數。</td>
  </tr>
</table>

<b>注意：</b>系統會以動態方式填入文件庫中的所有參數。此外，它們位於進階設定中。

 

###3.從共用文件中刪除

這個動作會從指定文件相對 URL (資料夾結構) 的文件庫中刪除文件。

**輸入：**

<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>必要</b></td>
    <td><b>說明</b></td>
  </tr>
  <tr>
    <td>文件相對 URI</td>
    <td>否</td>
    <td>指定相對於「共用文件」的文件 URL。範例：myspec1、myfolder/orders</td>
  </tr>
</table>


**輸出：**

<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>說明</b></td>
  </tr>
  <tr>
    <td>狀態</td>
    <td>成功執行動作會傳回狀態碼 200 (確定)。</td>
  </tr>
</table>


###4.在工作中插入 (JSON)

這個動作會在項目清單中加入項目。

**輸入：**

<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>必要</b></td>
    <td><b>說明</b></td>
  </tr>
  <tr>
    <td>ReqParam1*</td>
    <td>是</td>
    <td>這是要在清單中加入項目的其中一個必要參數。</td>
  </tr>
  <tr>
    <td>ReqParam2*</td>
    <td>是</td>
    <td>這是要在清單中加入項目的其中一個必要參數。</td>
  </tr>
  <tr>
    <td>OptionalParam1*</td>
    <td>否。進階</td>
    <td>這是要在清單中加入項目的其中一個必要參數。</td>
  </tr>
  <tr>
    <td>OptionalParam2*</td>
    <td>否。進階</td>
    <td>這是要在清單中加入項目的其中一個必要參數。</td>
  </tr>
</table>


<b>注意：</b>系統會以動態方式填入「清單」的所有參數。可以看見必要參數，而選擇性參數則在進階區段中。

**輸出：**

<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>說明</b></td>
  </tr>
  <tr>
    <td>ItemId</td>
    <td>加入清單項目的項目識別碼。</td>
  </tr>
  <tr>
    <td>狀態</td>
    <td>成功插入清單項目會傳回狀態碼 200 (確定)。</td>
  </tr>
</table>


###5.更新工作 (JSON)

這個動作會在項目清單中更新項目。

**輸入：**

<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>必要</b></td>
    <td><b>說明</b></td>
  </tr>
  <tr>
    <td>ItemId</td>
    <td>是</td>
    <td>清單項目的項目識別碼。</td>
  </tr>
  <tr>
    <td>ReqParam1*</td>
    <td>否</td>
    <td>這是要在清單中加入項目的其中一個必要參數。</td>
  </tr>
  <tr>
    <td>ReqParam2*</td>
    <td>否</td>
    <td>這是要在清單中加入項目的其中一個必要參數。</td>
  </tr>
  <tr>
    <td>OptionalParam1*</td>
    <td>否。進階</td>
    <td>這是要在清單中加入項目的其中一個必要參數。</td>
  </tr>
  <tr>
    <td>OptionalParam2*</td>
    <td>否。進階</td>
    <td>這是要在清單中加入項目的其中一個必要參數。</td>
  </tr>
</table>

<b>注意：</b>系統會以動態方式填入「清單」的所有參數。可以看見必要參數，而選擇性參數則在進階區段中。


**輸出：**

<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>說明</b></td>
  </tr>
  <tr>
    <td>狀態  </td>
    <td>成功更新清單項目會傳回狀態碼 200 (確定)。</td>
  </tr>
</table>


###6.從工作取得項目 (JSON)

這個動作會從項目清單中取得項目。


**輸入：**

<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>必要</b></td>
    <td><b>說明</b></td>
  </tr>
  <tr>
    <td>ItemId</td>
    <td>是</td>
    <td>清單項目的項目識別碼。</td>
  </tr>
</table>


**輸出：**

<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>說明</b></td>
  </tr>
  <tr>
    <td>Column1*</td>
    <td>這是清單中的其中一個參數。</td>
  </tr>
  <tr>
    <td>Column2*</td>
    <td>這是清單中的其中一個參數。</td>
  </tr>
  <tr>
    <td>狀態</td>
    <td>成功執行動作會傳回狀態碼 200 (確定)。</td>
  </tr>
</table>

<b>注意：</b>系統會以動態方式填入清單中的資料行，並將它們顯示在輸出參數中。


###7.從工作中刪除項目

這個動作會從項目清單中刪除項目。

**輸入：**

<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>必要</b></td>
    <td><b>說明</b></td>
  </tr>
  <tr>
    <td>ItemId</td>
    <td>是</td>
    <td>清單項目的項目識別碼。</td>
  </tr>
</table>


**輸出：**

<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>說明</b></td>
  </tr>
  <tr>
    <td>狀態  </td>
    <td>成功刪除清單項目會傳回狀態碼 200 (確定)。</td>
  </tr>
</table>


###8.列出共用文件 (JSON)。

這個動作會列出文件庫中的所有文件。您可以使用 [檢視] 或 CAML 查詢來篩選文件。

**輸入：**

<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>必要</b></td>
    <td><b>說明</b></td>
  </tr>
  <tr>
    <td>檢視名稱</td>
    <td>否</td>
    <td>指定要挑選用來篩選文件的有效檢視。範例：「核准的訂單」。若要處理所有的現有文件，請將此欄位留空。</td>
  </tr>
  <tr>
    <td>CAML 查詢</td>
    <td>否</td>
    <td>指定要篩選文件的有效 CAML 查詢。範例：<Where><Geq><FieldRef Name='ID'/><Value Type='Number'>10</Value></Geq></Where></td>
  </tr>
</table>


**輸出：**

<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>說明</b></td>
  </tr>
  <tr>
    <td>文件</td>
    <td>所有文件的陣列。每份文件都會有下列欄位。<br><br>
	文件 []<br>
	名稱<br>
	項目識別碼<br>
	項目完整 URL<br>
	進階<br>
	項目編輯 URL<br>
	清單名稱<br>
	清單完整 URL<br>
	</td>
  </tr>
  <tr>
    <td>狀態  </td>
    <td>成功插入清單項目會傳回狀態碼 200 (確定)。</td>
  </tr>
</table>


###9.上傳到共用文件 (XML)

這個動作會將新文件上載到「共用文件」。輸入文件應該是 XML 裝載。動作的回應會是 XML 裝載。

**輸入：**

<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>必要</b></td>
    <td><b>說明</b></td>
  </tr>
  <tr>
    <td>名稱</td>
    <td>是</td>
    <td>文件的名稱。</td>
  </tr>
  <tr>
    <td>內容</td>
    <td>是</td>
    <td>文件的內容。</td>
  </tr>
  <tr>
    <td>ContentTransferEncoding</td>
    <td>是</td>
    <td>訊息的內容傳送編碼。([無]|[base64])</td>
  </tr>
  <tr>
    <td>強制覆寫</td>
    <td>是</td>
    <td>如果設定為 TRUE，且指定名稱的文件存在的情況下，文件將會被覆寫。</td>
  </tr>
</table>
 

**輸出：**

<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>說明</b></td>
  </tr>
  <tr>
    <td>輸出 XML</td>
    <td>使用 XML 格式的上傳動作回應。</td>
  </tr>
  <tr>
    <td>狀態  </td>
    <td>成功上傳文件會傳回狀態碼 200 (確定)。</td>
  </tr>
</table>

###10.從共用文件取得 (XML)

這個動作會從指定文件相對 URL (資料夾結構) 的文件庫中取得文件。

**輸入：**

<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>必要</b></td>
    <td><b>說明</b></td>
  </tr>
  <tr>
    <td>文件相對 URI</td>
    <td>否</td>
    <td>指定相對於「共用文件」的文件 URL。範例：myspec1、myfolder/orders</td>
  </tr>
  <tr>
    <td>檔案類型</td>
    <td>是</td>
    <td>指定檔案是二進位檔案或文字檔案。</td>
  </tr>
</table>


**輸出：**

<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>說明</b></td>
  </tr>
  <tr>
    <td>輸出 XML</td>
    <td>文件內容</td>
  </tr>
  <tr>
    <td>ContentTransferEncoding</td>
    <td>訊息的內容傳送編碼。([無]|[base64])</td>
  </tr>
  <tr>
    <td>狀態</td>
    <td>成功執行動作會傳回狀態碼 200 (確定)。</td>
  </tr>
</table>

###11.在工作中插入 (XML)

這個動作會在項目清單中加入項目。輸入應是 XML 裝載。

**輸入：**

<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>必要</b></td>
    <td><b>說明</b></td>
  </tr>
  <tr>
    <td>輸入 XML</td>
    <td>是</td>
    <td>XML 訊息，其中包含要插入之清單項目的欄位值。您可以使用轉換 API 應用程式來產生 XML 訊息。</td>
  </tr>
</table>
<b>注意：</b>系統會以動態方式填入「清單」的所有參數。可以看見必要參數，而選擇性參數則在進階區段中。

**輸出：**

<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>說明</b></td>
  </tr>
  <tr>
    <td>ItemId</td>
    <td>加入清單項目的項目識別碼。</td>
  </tr>
  <tr>
    <td>狀態  </td>
    <td>成功插入清單項目會傳回狀態碼 200 (確定)。</td>
  </tr>
</table>


###12.更新工作 (XML)

這個動作會在項目清單中更新項目。輸入應是 XML 裝載。


**輸入：**

<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>必要</b></td>
    <td><b>說明</b></td>
  </tr>
  <tr>
    <td>ItemId</td>
    <td>是</td>
    <td>清單項目的項目識別碼。</td>
  </tr>
  <tr>
    <td>輸入 XML</td>
    <td>是</td>
    <td>XML 訊息，其中包含要插入之清單項目的欄位值。您可以使用轉換 API 應用程式來產生 XML 訊息。</td>
  </tr>
</table>

<b>注意：</b>系統會以動態方式填入「清單」的所有參數。可以看見必要參數，而選擇性參數則在進階區段中。

**輸出：**

<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>說明</b></td>
  </tr>
  <tr>
    <td>狀態  </td>
    <td>成功更新清單項目會傳回狀態碼 200 (確定)。</td>
  </tr>
</table>


###13.從工作取得項目 (XML)

這個動作會從項目清單中取得項目。


**輸入：**

<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>必要</b></td>
    <td><b>說明</b></td>
  </tr>
  <tr>
    <td>ItemId</td>
    <td>是</td>
    <td>清單項目的項目識別碼。</td>
  </tr>
</table>

**輸出：**

<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>說明</b></td>
  </tr>
  <tr>
    <td>輸出 XML</td>
    <td>XML 訊息，其中包含選取清單項目的欄位值。</td>
  </tr>
  <tr>
    <td>狀態  </td>
    <td>成功執行動作會傳回狀態碼 200 (確定)。</td>
  </tr>
</table>


<!--Image references-->
[1]: ./media/app-service-logic-connector-sharepoint/image_0.png
[2]: ./media/app-service-logic-connector-sharepoint/image_1.png
[3]: ./media/app-service-logic-connector-sharepoint/image_2.png
[4]: ./media/app-service-logic-connector-sharepoint/image_3.png
[5]: ./media/app-service-logic-connector-sharepoint/image_4.jpg
[6]: ./media/app-service-logic-connector-sharepoint/image_5.png
[7]: ./media/app-service-logic-connector-sharepoint/image_6.png

<!--HONumber=54-->