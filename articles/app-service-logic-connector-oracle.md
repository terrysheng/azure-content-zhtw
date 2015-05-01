<properties 
   pageTitle="Oracle 連接器" 
   description="如何使用 Oracle 連接器" 
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
   ms.author="sutalasi"/>


# Oracle 資料庫連接器 #

在邏輯應用程式中，連接器可以在執行流程時用來擷取、處理或發送資料。透過在流程中運用 Oracle 連接器，您可以達到各種案例的目的。幾個範例：  

1.	透過 web 或行動使用者前端公開位於 Oracle 上的部分資料。
2.	將資料插入 Oracle 資料庫資料表以儲存 (例如：Employee Records、Sales Orders 等等)
3.	從 Oracle 擷取資料，以供商務程序使用

在這些案例中，請務必完成下列需求： 

1. 建立 Oracle 連接器 API 應用程式的執行個體
2. 建置 API 應用程式可與內部部署 Oracle 進行通訊的混合式連線
3. 在邏輯應用程式中使用建立的 API 應用程式，以達成所需商務程序的目的

	### 基本觸發程序和動作
		
    - 輪詢資料 (觸發程序) 
    - 插入到資料表
    - 更新資料表
    - 從資料表選取
    - 從資料表刪除
    - 呼叫預存程序

## 建立 Oracle 資料庫連接器 API 應用程式的執行個體 ##

若要使用 Oracle 連接器，您必須建立 Oracle 連接器 API 應用程式的執行個體。以下步驟可以達到此目的：

1. 使用 Azure 入口網站左下方的 '+新增' 選項開啟 Azure Marketplace。
2. 瀏覽至 [Web 與行動] > [API 應用程式]，並搜尋「Oracle 連接器」。
3. 在第一個分頁中提供一般詳細資訊，例如名稱、應用程式服務方案等等
4. 提供下表中所述的套件設定。

<style type="text/css">
	table.tableizer-table {
	border: 1px solid #CCC; font-family: Arial, Helvetica, sans-serif;
	font-size: 12px;
} 
.tableizer-table td {
	padding: 4px;
	margin: 3px;
	border: 1px solid #ccc;
}
.tableizer-table th {
	background-color: #525B64; 
	color: #FFF;
	font-weight: bold;
}
</style><table class="tableizer-table">
<tr class="tableizer-firstrow"><th>名稱</th><th>必要</th><th>說明</th></tr>
 <tr><td>資料來源</td><td>是</td><td>安裝 Oracle 用戶端的電腦上的 tnsnames.ora 檔案中所指定的資料來源 (網路服務) 名稱。如需資料來源名稱和 tnsnames.ora 的詳細資訊，請參閱[設定 Oracle 用戶端]</td></tr>
 <tr><td>使用者名稱</td><td>是</td><td>指定連線到 Oracle 伺服器的有效使用者名稱。</td></tr>
 <tr><td>密碼</td><td>是</td><td>指定連線到 Oracle 伺服器的有效密碼。</td></tr>
 <tr><td>服務匯流排連接字串</td><td>是</td><td>選用。如果 Oracle 伺服器是在內部部署，請指定此參數。這應該會是有效的服務匯流排命名空間連接字串。您必須在能夠存取 Oracle Server 的伺服器上安裝接聽程式代理程式。您可以移至您的 API 應用程式摘要頁面，然後按一下 '混合式連線' 來安裝代理程式。</td></tr>
 <tr><td>資料表</td><td>否</td><td>選用。指定資料庫中允許連接器修改的資料表。例如：OrdersTable、EmployeeTable</td></tr>
 <tr><td>預存程序</td><td>否</td><td>選用。指定資料庫中連接器可以呼叫的預存程序。例如：IsEmployeeEligible、CalculateOrderDiscount</td></tr>
 <tr><td>函式</td><td>否</td><td>選用。指定資料庫中連接器可以呼叫的函式。例如：IsEmployeeEligible、CalculateOrderDiscount</td></tr>
 <tr><td>套件實體</td><td>否</td><td>選用。指定資料庫中連接器可以呼叫的套件實體。例如：PackageOrderProcessing.CompleteOrder、PackageOrderProcessing.GenerateBill</td></tr>
 <tr><td>資料提供陳述式</td><td>否</td><td>選用。指定陳述式，該陳述式決定是否有任何資料可供輪詢。範例：從 table_name 選取 *</td></tr>
 <tr><td>輪詢類型</td><td>否</td><td>選用。指定輪詢型別。允許的值為「Select」、「Procedure」、「Function」、「Package」。</td></tr>
 <tr><td>輪詢陳述式</td><td>否</td><td>選用。指定輪詢 Oracle Server 資料庫的陳述式。範例： SELECT * from table_name</td></tr>
 <tr><td>後輪詢陳述式</td><td>否</td><td>選用。指定輪詢後要執行的陳述式。範例： DELETE * from table_name.</td></tr>
</table>



 ![][1]  

## 混合式組態 ##

透過 [瀏覽] -> [API 應用程式] -> [<剛建立的 API 應用程式名稱>] 瀏覽到剛建立的 API 應用程式，您將會看到下列行為。因為尚未建立混合式連線，所以安裝未完成。

![][2] 

若要建置混合式連線，請執行下列作業：

1. 複製主要連接字串
2. 按一下 '下載及設定' 連結
3. 依照剛起始的安裝程序執行，並在要求時提供主要連接字串
4. 完成安裝程序後，便會顯示如下所示的對話方塊

![][3] 

現在當您重新瀏覽至建立的 API 應用程式時，您會看到混合式連線狀態為 [已連線]。 

![][4] 

注意：如果您要切換到次要連線字串，您只需重做混合式設定，並提供次要連接字串來取代主要連接字串即可  

## 邏輯應用程式中的使用方式 ##

Oracle 連接器可以在邏輯應用程式中做為觸發程序/動作使用。觸發程序和所有動作支援 JSON 和 XML 兩種資料格式。每個提供為套件設定一部份的資料表，都有一組 JSON 動作和一組 XML 動作。如果您使用 XML 觸發程序/動作，您可以使用「轉換 API 應用程式」將資料轉換成令一種 XML 資料格式。 

讓我們以一個簡單的邏輯應用程式為例，它會輪詢來自 Oracle 資料表的資料，新增其他資料表內的資料並更新資料。



-  建立/編輯邏輯應用程式時，請選擇 Oracle 連接器 API 應用程式。這樣會列出可以使用的觸發程序 - 「輪詢資料 (JSON)」和「輪詢資料 (XML)」。

 ![][5] 


- 選取觸發程序 - [輪詢資料 (JSON)]，然後指定 [頻率] 並按一下 ✓。

![][6] 



- 該觸發程序在邏輯應用程式中現在會顯示為已設定。觸發程序輸出的結果將會顯示，並可用於輸入後續動作。 

![][7] 


- 從元件庫選取相同的 Oracle 連接器做為動作。選取其中一個「插入」動作 - [插入到 TempEmployeeDetails (JSON)]。

![][8] 



- 提供要插入的記錄，然後按一下 ✓。 

![][9] 



- 從元件庫選取相同的 Oracle 連接器做為動作。選取相同資料表上的「更新」動作 (例如：更新 EmployeeDetails)

![][11] 



- 提供更新動作的輸入並按一下 ✓。 

![][12] 

您可以在所輪詢的資料表中新增記錄，來測試邏輯應用程式。

<!--Image references-->
[1]: ./media/app-service-logic-connector-oracle/Create.jpg
[2]: ./media/app-service-logic-connector-oracle/BrowseSetupIncomplete.jpg
[3]: ./media/app-service-logic-connector-oracle/HybridSetup.jpg
[4]: ./media/app-service-logic-connector-oracle/BrowseSetupComplete.jpg
[5]: ./media/app-service-logic-connector-oracle/LogicApp1.jpg
[6]: ./media/app-service-logic-connector-oracle/LogicApp2.jpg
[7]: ./media/app-service-logic-connector-oracle/LogicApp3.jpg
[8]: ./media/app-service-logic-connector-oracle/LogicApp4.jpg
[9]: ./media/app-service-logic-connector-oracle/LogicApp5.jpg
[10]: ./media/app-service-logic-connector-oracle/LogicApp6.jpg
[11]: ./media/app-service-logic-connector-oracle/LogicApp7.jpg
[12]: ./media/app-service-logic-connector-oracle/LogicApp8.jpg

<!--Links-->
[設定 Oracle 用戶端]: https://msdn.microsoft.com/zh-tw/library/dd787872.aspx



<!--HONumber=52-->