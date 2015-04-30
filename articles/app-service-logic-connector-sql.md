<properties 
   pageTitle="SQL 連接器" 
   description="如何使用 SQL 連接器" 
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


# Microsoft SQL 連接器 #

在邏輯應用程式中，連接器可以在執行流程時用來擷取、處理或發送資料。某些案例中您可能需要處理 Azure SQL 上的 SQL 資料庫，或 SQL Server (安裝於內部部署且位於防火牆後)。透過在流程中運用 SQL 連接器，您可以達到各種案例的目的。幾個範例：  

1.	透過 web 或行動使用者前端公開位於 SQL 上的部分資料。
2.	將資料插入 SQL 資料庫資料表以儲存 (例如：Employee Records、Sales Orders 等等)
3.	從 SQL 擷取資料，以供商務程序使用

在這些案例中，請務必完成下列需求： 

1. 建立 SQL 連接器 API 應用程式的執行個體
2. 建置 API 應用程式可與內部部署 SQL 進行通訊的混合式連線這個步驟是選用的，且指只有內部部署的 SQL Server 需要，SQL Azure 則不需要。
3. 在邏輯應用程式中使用建立的 API 應用程式，以達成所需商務程序的目的

	### 基本觸發程序和動作
		
    - 輪詢資料 (觸發程序) 
    - 插入到資料表
    - 更新資料表
    - 從資料表選取
    - 從資料表刪除
    - 呼叫預存程序

## 建立 SQL 連接器 API 應用程式的執行個體 ##

若要使用 SQL 連接器，您必須建立 SQL 連接器 API 應用程式的執行個體。以下步驟可以達到此目的：

1. 使用 Azure 入口網站左下方的 [+新增] 選項開啟 Azure Marketplace。
2. 瀏覽至 [Web 與行動] > [API 應用程式]，並搜尋「SQL 連接器」。
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
<tr class="tableizer-firstrow"><th>名稱</th><th>必要</th><th>預設值</th><th>說明</th></tr>
 <tr><td>伺服器名稱</td><td>是</td><td>&nbsp;</td><td>指定 SQL Server 名稱。範例："SQLserver"、"SQLserver/sqlexpress" 或 "SQLserver.mydomain.com"。</td></tr>
 <tr><td>連接埠</td><td>否</td><td> 1433</td><td>選用。建立連線的連接埠號碼。若沒有指定值，連接器會透過預設的連接埠連線。</td></tr>
 <tr><td>使用者名稱</td><td>是</td><td>&nbsp;</td><td>指定連線到 SQL 伺服器的有效使用者名稱。</td></tr>
 <tr><td>密碼</td><td>是</td><td>&nbsp;</td><td>指定連線到 SQL 伺服器的有效密碼。</td></tr>
 <tr><td>資料庫名稱</td><td>是</td><td>&nbsp;</td><td>指定 SQL Server 中資料庫的名稱。範例："orders" 或 "dbo/orders" 或者 "myaccount/employees"。</td></tr>
 <tr><td>內部部署</td><td>是</td><td>FALSE</td><td>指定 SQL Server 是否內部部署在防火牆後面。如果設定為 TRUE，則您必須在能夠存取 SQL 伺服器的伺服器上安裝接聽程式代理程式。您可以移至您的 API 應用程式摘要頁面，然後按一下 '混合式連線' 來安裝代理程式。</td></tr>
 <tr><td>服務匯流排連接字串</td><td>否</td><td>&nbsp;</td><td>選用。如果 SQL 伺服器是在內部部署，請指定此參數。這應該會是有效的服務匯流排命名空間連接字串。請確認您使用 '標準' 版本的 Azure 服務會流排，而不是 '基本'。</td></tr>
 <tr><td>夥伴伺服器名稱</td><td>否</td><td>&nbsp;</td><td>選用。指定當主要伺服器關閉時，要連線到的夥伴伺服器。</td></tr>
 <tr><td>資料表</td><td>否</td><td>&nbsp;</td><td>選用。指定資料庫中允許連接器修改的資料表。例如：OrdersTable、EmployeeTable</td></tr>
 <tr><td>預存程序</td><td>否</td><td>&nbsp;</td><td>選用。指定資料庫中連接器可以呼叫的預存程序。例如：IsEmployeeEligible、CalculateOrderDiscount</td></tr>
 <tr><td>資料可用查詢</td><td>否</td><td>&nbsp;</td><td>選用。指定SQL 陳述式，該陳述式決定是否有任何資料可供輪詢 SQL Server 資料庫資料表 。範例： SELECT COUNT(*) from table_name.</td></tr>
 <tr><td>輪詢資料查詢</td><td>否</td><td>&nbsp;</td><td>選用。指定輪詢 SQL Server 資料庫資料表的 SQL 陳述式。您可以指定任意數目的 SQL 陳述式並以分號隔開。範例：SELECT * from table_name; DELETE from table_name. 注意：您必須提供不會造成無線迴圈的輪詢陳述式。例如，選取之後應跟隨刪除，且根據旗標選取後應跟隨更新旗標。</td></tr>
</table>


 ![][1]  

## 混合式組態 (選用) ##

注意：只有當您使用內部部署在防火牆後面的 SQL Server 時才需要此步驟。

透過 [瀏覽] -> [API 應用程式] -> [<剛建立的 API 應用程式名稱>] 瀏覽到剛建立的 API 應用程式，您將會看到下列行為。因為尚未建立混合式連線，所以安裝未完成。

![][2] 

若要建置混合式連線，請執行下列作業：

1. 複製主要連接字串
2. 按一下  '下載及設定' 連結
3. 依照剛起始的安裝程序執行，並在要求時提供主要連接字串
4. 完成安裝程序後，便會顯示如下所示的對話方塊

![][3] 

現在當您重新瀏覽至建立的 API 應用程式時，您會看到混合式連線狀態為 [已連線]。 

![][4] 

注意：如果您要切換到次要連線字串，您只需重做混合式設定，並提供次要連接字串來取代主要連接字串即可  

## 邏輯應用程式中的使用方式 ##

SQL 連接器可以在邏輯應用程式中做為觸發程序/動作使用。觸發程序和所有動作支援 JSON 和 XML 兩種資料格式。每個提供為套件設定一部份的資料表，都有一組 JSON 動作和一組 XML 動作。如果您使用 XML 觸發程序/動作，您可以使用「轉換 API 應用程式」將資料轉換成令一種 XML 資料格式。 

讓我們以一個簡單的邏輯應用程式為例，它會輪詢來自 SQL 資料表的資料，新增其他資料表內的資料並更新資料。



-  建立/編輯邏輯應用程式時，請選擇 SQL 連接器 API 應用程式。這樣會列出可以使用的觸發程序 - 「輪詢資料 (JSON)」和「輪詢資料 (XML)」。

 ![][5] 


- 選取觸發程序 - [輪詢資料 (JSON)]，然後指定 [頻率] 並按一下 ✓。

![][6] 



- 該觸發程序在邏輯應用程式中現在會顯示為已設定。觸發程序輸出的結果將會顯示，並可用於輸入後續動作。 

![][7] 


- 從元件庫選取相同的 SQL 連接器做為動作。選取其中一個「插入」動作 - [插入到 TempEmployeeDetails (JSON)]。

![][8] 



- 提供要插入的記錄，然後按一下 ✓。 

![][9] 



- 從元件庫選取相同的 SQL 連接器做為動作。選取相同資料表上的「更新」動作 (例如：更新 EmployeeDetails)

![][11] 



- 提供更新動作的輸入並按一下 ✓。 

![][12] 

您可以在所輪詢的資料表中新增記錄，來測試邏輯應用程式。

<!--Image references-->
[1]: ./media/app-service-logic-connector-sql/Create.jpg
[2]: ./media/app-service-logic-connector-sql/BrowseSetupIncomplete.jpg
[3]: ./media/app-service-logic-connector-sql/HybridSetup.jpg
[4]: ./media/app-service-logic-connector-sql/BrowseSetupComplete.jpg
[5]: ./media/app-service-logic-connector-sql/LogicApp1.jpg
[6]: ./media/app-service-logic-connector-sql/LogicApp2.jpg
[7]: ./media/app-service-logic-connector-sql/LogicApp3.jpg
[8]: ./media/app-service-logic-connector-sql/LogicApp4.jpg
[9]: ./media/app-service-logic-connector-sql/LogicApp5.jpg
[10]: ./media/app-service-logic-connector-sql/LogicApp6.jpg
[11]: ./media/app-service-logic-connector-sql/LogicApp7.jpg
[12]: ./media/app-service-logic-connector-sql/LogicApp8.jpg



<!--HONumber=52-->