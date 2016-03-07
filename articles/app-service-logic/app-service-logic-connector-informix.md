<properties
   pageTitle="在Microsoft Azure App Service 中使用 Informix 連接器 | Microsoft Azure"
   description="如何使用 Informix 連接器搭配邏輯應用程式觸發程序和動作"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="gplarsen"
   manager="erikre"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="02/10/2016"
   ms.author="plarsen"/>

# Informix 連接器
>[AZURE.NOTE] 這一版文章適用於邏輯應用程式 2014-12-01-preview 結構描述版本。

Microsoft Connector for Informix 是一個 API 應用程式，可透過 Azure App Service 將應用程式連接至儲存在 IBM Informix 資料庫中的資源。連接器含有一個 Microsoft 用戶端，可透過 TCP/IP 網路連線 (包括使用 Azure 服務匯流排轉送對內部部署 Informix 伺服器進行的 Azure 混合式連線) 連接至遠端 Informix 伺服器電腦。連接器支援下列資料庫作業：

- 使用 SELECT 讀取資料列
- 使用後接 SELECT 的 SELECT COUNT 輪詢以讀取資料列
- 使用 INSERT 加入一個資料列或多個 (大量) 資料列
- 使用 UPDATE 變更一個資料列或多個 (大量) 資料列
- 使用 DELETE 移除一個資料列或多個 (大量) 資料列
- 使用後接 UPDATE WHERE CURRENT OF CURSOR 的 SELECT CURSOR 讀取以變更資料列
- 使用後接 UPDATE WHERE CURRENT OF CURSOR 的 SELECT CURSOR 讀取以移除資料列
- 使用 CALL 執行具有輸入和輸出參數、傳回值、結果集的程序
- 使用 SELECT、INSERT、UPDATE、DELETE 的自訂命令和複合作業

## 觸發程序和動作
連接器支援下列邏輯應用程式觸發程序和動作：

觸發程序 | 動作
--- | ---
<ul><li>輪詢資料</li></ul> | <ul><li>Bulk Insert</li><li>Insert</li><li>Bulk Update</li><li>Update</li><li>Call</li><li>Bulk Delete</li><li>Delete</li><li>Select</li><li>Conditional update</li><li>Post to EntitySet</li><li>Conditional delete</li><li>選取單一實體</li><li>Delete</li><li>Upsert to EntitySet</li><li>自訂命令</li><li>復合作業</li></ul>


## 建立 Informix 連接器
您可以在邏輯應用程式中或從 Azure Marketplace 定義連接器，如下列範例所示：

1. 在 Azure 開始面板中，選取 [**Marketplace**]。
2. 在 [所有項目] 刀鋒視窗的 [搜尋所有項目] 方塊中輸入 **informix**，然後按一下 Enter 鍵。
3. 在 [搜尋所有項目的結果] 窗格中，選取 [Informix 連接器]。
4. 在 [Informix 連接器描述] 刀鋒視窗中，選取 [建立]。
5. 在 [Informix 連接器封裝] 刀鋒視窗中，輸入 [名稱] (例如："InformixConnectorNewOrders")、[App Service 方案] 和其他屬性。
6. 選取 [封裝設定]，然後輸入下列封裝設定。

	名稱 | 必要 | 說明
--- | --- | ---
ConnectionString | 是 | Informix 用戶端連接字串 (例如，"Network Address=servername;Network Port=9089;User ID=username;Password=password;Initial Catalog=nwind;Default Schema=informix")。
資料表 | 是 | OData 作業所需以及產生含範例之 Swagger 文件所需的資料表、檢視和別名清單 (以逗號分隔) (例如"NEWORDERS")。
程序 | 是 | 以逗號分隔的程序和函式名稱清單 (例如"SPORDERID")。
OnPremise | 否 | 使用 Azure 服務匯流排轉送部署內部部署應用程式
ServiceBusConnectionString | 否 | Azure 服務匯流排轉送連接字串。
PollToCheckData | 否 | 要搭配邏輯應用程式觸發程序使用的 SELECT COUNT 陳述式 (例如"SELECT COUNT(*) FROM NEWORDERS WHERE SHIPDATE IS NULL")。
PollToReadData | 否 | 要搭配邏輯應用程式觸發程序使用的 SELECT 陳述式 (例如"SELECT * FROM NEWORDERS WHERE SHIPDATE IS NULL FOR UPDATE")。
PollToAlterData | 否 | 要搭配邏輯應用程式觸發程序使用的 UPDATE 或 DELETE 陳述式 (例如"UPDATE NEWORDERS SET SHIPDATE = CURRENT DATE WHERE CURRENT OF &lt;CURSOR&gt;")。

7. 選取 [確定]，然後選取 [建立]。
8. 完成時，[封裝設定] 看起來如下：![][1]


## 以 Informix 連接器動作新增資料的邏輯應用程式 ##
您可以定義邏輯應用程式動作，以使用 API Insert 或 Post to Entity OData 作業將資料加入至 Informix 資料表。例如，您可對以身分識別資料行定義的資料表處理 SQL INSERT 陳述式，將身分識別值或受影響的資料列傳回至邏輯應用程式，進而插入一筆新的客戶訂單記錄 (SELECT ORDID FROM FINAL TABLE (INSERT INTO NEWORDERS (CUSTID,SHIPNAME,SHIPADDR,SHIPCITY,SHIPREG,SHIPZIP) VALUES (?,?,?,?,?,?)))。

> [AZURE.TIP] Informix Connection "*Post to EntitySet*" 會傳回身分識別資料行值，而 "*API Insert*" 會傳回受影響的資料列

1. 在 Azure 開始面板中，依序選取 **+** (加號)、[Web + 行動] 和 [邏輯應用程式]。
2. 輸入名稱 (例如"NewOrdersInformix")、App Service 方案、其他屬性，然後選取 [建立]。
3. 在 Azure 開始面板中，依序選取您剛建立的邏輯應用程式、[設定] 和 [觸發程序和動作]。
4. 在 [觸發程序和動作] 刀鋒視窗中，選取 [邏輯應用程式範本] 中的 [從頭建立]。
5. 在 API Apps 面板中，選取 [週期]，設定頻率和間隔，然後選取**核取記號**。
6. 在 API Apps 面板中，選取 [Informix 連接器]，展開作業清單以選取 [插入 NEWORDER]。
7. 展開參數清單以輸入下列值：  

	名稱 | 值
--- | --- 
CUSTID | 10042
SHIPID | 10000
SHIPNAME | Lazy K Kountry Store 
SHIPADDR | 12 Orchestra Terrace
SHIPCITY | Walla Walla 
SHIPREG | WA
SHIPZIP | 99362 

8. 選取**核取記號**以儲存動作設定，然後選取 [儲存]。
9. 設定應如下所示：![][3]  
10. 在 [作業] 之下的 [所有執行] 清單中，選取第一個列出的項目 (最近一次執行)。 
11. 在 [邏輯應用程式執行] 刀鋒視窗中，選取 [動作] 項目 **informixconnectorneworders**。
12. 在 [邏輯應用程式動作] 刀鋒視窗中，選取 [輸入連結]。Informix 連接器會使用輸入來處理參數化的 INSERT 陳述式。
13. 在 [邏輯應用程式動作] 刀鋒視窗中，選取 [輸出連結]。輸入應如下所示：![][4]

#### 您所需了解的事情

- 形成邏輯應用程式的動作名稱時，連接器會截斷 Informix 資料表名稱。例如，[插入 NEWORDERS] 作業會被截斷成 [插入 NEWORDER]。
- 儲存邏輯應用程式的 [觸發程序和動作] 之後，邏輯應用程式會處理此作業。在邏輯應用程式處理此作業前，可能會有數秒的延遲 (例如 3-5 秒)。或者，您可以按一下 [立即執行] 來處理此作業。
- Informix 連接器會以屬性定義 EntitySet 成員，包括成員是否對應至具有預設值的 Informix 資料行或產生的資料行 (例如身分識別)。邏輯應用程式會在 EntitySet 成員識別碼名稱旁邊顯示一個紅色星號，表示需有值的 Informix 資料行。您不應輸入 ORDID 成員的值，其對應至 Informix 身分識別資料行。您可以輸入其他選擇性成員 (ITEMS、ORDDATE、REQDATE、SHIPID、FREIGHT、SHIPCTRY) 的值，其對應至具有預設值的 Informix 資料行。 
- Informix 連接器會將 Post to EntitySet 回應 (包含身分識別資料行的值) 傳回至邏輯應用程式，而該回應衍生自已備妥 SQL INSERT 陳述式上的 DRDA SQLDARD (SQL 資料區域回覆資料)。Informix 伺服器不會對具有預設值的資料行傳回插入的值。  


## 以 Informix 連接器動作新增大量資料的邏輯應用程式 ##
您可以定義邏輯應用程式動作，以使用 API Bulk Insert 作業將資料加入至 Informix 資料表。例如，您可使用資料列值的陣列對以身分識別資料行定義的資料表處理 SQL INSERT 陳述式，將受影響的資料列傳回至邏輯應用程式，進而插入兩筆新的客戶訂單記錄 (SELECT ORDID FROM FINAL TABLE (INSERT INTO NEWORDERS (CUSTID,SHIPNAME,SHIPADDR,SHIPCITY,SHIPREG,SHIPZIP) VALUES (?,?,?,?,?,?)))。

1. 在 Azure 開始面板中，依序選取 **+** (加號)、[Web + 行動] 和 [邏輯應用程式]。
2. 輸入名稱 (例如"NewOrdersBulkInformix")、App Service 方案、其他屬性，然後選取 [建立]。
3. 在 Azure 開始面板中，依序選取您剛建立的邏輯應用程式、[設定] 和 [觸發程序和動作]。
4. 在 [觸發程序和動作] 刀鋒視窗中，選取 [邏輯應用程式範本] 中的 [從頭建立]。
5. 在 API Apps 面板中，選取 [週期]，設定頻率和間隔，然後選取**核取記號**。
6. 在 API Apps 面板中，選取 [Informix 連接器]，展開作業清單以選取 [大量插入 NEW]。
7. 輸入**資料列**值做為陣列。例如，複製並貼上下列程式碼：  

	```
    [{"custid":10081,"shipid":10000,"shipname":"Trail's Head Gourmet Provisioners","shipaddr":"722 DaVinci Blvd.","shipcity":"Kirkland","shipreg":"WA","shipzip":"98034"},{"custid":10088,"shipid":10000,"shipname":"White Clover Markets","shipaddr":"305 14th Ave. S. Suite 3B","shipcity":"Seattle","shipreg":"WA","shipzip":"98128","shipctry":"USA"}]
	```
        
8. 選取**核取記號**以儲存動作設定，然後選取 [儲存]。設定應如下所示：![][6]

9. 在 [作業] 之下的 [所有執行] 清單中，按一下第一個列出的項目 (最近一次執行)。
10. 在 [邏輯應用程式執行] 刀鋒視窗中，按一下 [動作] 項目。
11. 在 [邏輯應用程式動作] 刀鋒視窗中，按一下 [輸入連結]。輸出應如下所示：[][7]
12. 在 [邏輯應用程式動作] 刀鋒視窗中，按一下 [輸出連結]。輸出應如下所示：![][8]

#### 您所需了解的事情

- 形成邏輯應用程式的動作名稱時，連接器會截斷 Informix 資料表名稱。例如，[大量插入 NEWORDERS] 作業會被截斷成 [大量插入 NEW]。
- Informix 資料庫的資料表和資料行名稱可能會區分大小寫。例如，Bulk Insert 作業陣列資料行名稱可能需要以小寫 ("custid") 而不是大寫 ("CUSTID") 指定。
- 略過身分識別欄位 (例如 ORDID)、可為 null 的資料行 (例如 SHIPDATE) 和具有預設值的資料行 (例如 ORDDATE、REQDATE、SHIPID、FREIGHT、SHIPCTRY)，Informix 資料庫即可產生值。
- 指定「今天」和「明天」，Informix 連接器即可產生「目前日期」和「目前日期 + 1 天」函式 (例如 REQDATE)。 


## 以 Informix 連接器觸發程序讀取、變更或刪除資料的邏輯應用程式 ##
您可定義邏輯應用程式觸發程序，使用 API 輪詢資料複合作業來輪詢和讀取 Informix 資料表中的資料。例如，您可以讀取一或多筆新的客戶訂單記錄，並將記錄傳回至邏輯應用程式。Informix Connection 封裝/應用程式設定應如下所示：

	App Setting | Value
--- | --- | ---
PollToCheckData | SELECT COUNT(*) FROM NEWORDERS WHERE SHIPDATE IS NULL
PollToReadData | SELECT * FROM NEWORDERS WHERE SHIPDATE IS NULL FOR UPDATE
PollToAlterData | <no value specified>


此外，您可定義邏輯應用程式觸發程序，使用 API 輪詢資料複合作業來輪詢、讀取及變更 Informix 資料表中的資料。例如，您可以讀取一或多筆新的客戶訂單記錄、更新資料列值，並將選取 (更新前) 的記錄傳回至邏輯應用程式。Informix Connection 封裝/應用程式設定應如下所示：

	App Setting | Value
--- | --- | ---
PollToCheckData | SELECT COUNT(*) FROM NEWORDERS WHERE SHIPDATE IS NULL
PollToReadData | SELECT * FROM NEWORDERS WHERE SHIPDATE IS NULL FOR UPDATE
PollToAlterData | UPDATE NEWORDERS SET SHIPDATE = CURRENT DATE WHERE CURRENT OF &lt;CURSOR&gt;


此外，您可定義邏輯應用程式觸發程序，使用 API 輪詢資料複合作業來輪詢、讀取及移除 Informix 資料表中的資料。例如，您可以讀取一或多筆新的客戶訂單記錄、刪除資料列，並將選取 (刪除前) 的記錄傳回至邏輯應用程式。Informix Connection 封裝/應用程式設定應如下所示：

	App Setting | Value
--- | --- | ---
PollToCheckData | SELECT COUNT(*) FROM NEWORDERS WHERE SHIPDATE IS NULL
PollToReadData | SELECT * FROM NEWORDERS WHERE SHIPDATE IS NULL FOR UPDATE
PollToAlterData | DELETE NEWORDERS WHERE CURRENT OF &lt;CURSOR&gt;

在此範例中，邏輯應用程式會輪詢、讀取、更新，而後重新讀取 Informix 資料表中的資料。

1. 在 Azure 開始面板中，依序選取 **+** (加號)、[Web + 行動] 和 [邏輯應用程式]。
2. 輸入名稱 (例如"ShipOrdersInformix")、App Service 方案、其他屬性，然後選取 [建立]。
3. 在 Azure 開始面板中，依序選取您剛建立的邏輯應用程式、[設定] 和 [觸發程序和動作]。
4. 在 [觸發程序和動作] 刀鋒視窗中，選取 [邏輯應用程式範本] 中的 [從頭建立]。
5. 在 API Apps 面板中，選取 [Informix 連接器]，設定頻率和間隔，然後選取**核取記號**。
6. 在 API Apps 面板中，選取 [Informix 連接器]，展開作業清單以選取 [從 NEWORDERS 選取]。
7. 選取**核取記號**以儲存動作設定，然後選取 [儲存]。設定應如下所示：![][10]
8. 按一下以關閉 [觸發程序和動作] 刀鋒視窗，然後按一下以關閉 [設定] 刀鋒視窗。
9. 在 [作業] 之下的 [所有執行] 清單中，按一下第一個列出的項目 (最近一次執行)。
10. 在 [邏輯應用程式執行] 刀鋒視窗中，按一下 [動作] 項目。
11. 在 [邏輯應用程式動作] 刀鋒視窗中，按一下 [輸出連結]。輸出應如下所示：![][11]


## 以 Informix 連接器動作移除資料的邏輯應用程式 ##
您可以定義邏輯應用程式動作，以使用 API Delete 或 Post to Entity OData 作業來移除 Informix 資料表中的資料。例如，您可對以身分識別資料行定義的資料表處理 SQL INSERT 陳述式，將身分識別值或受影響的資料列傳回至邏輯應用程式，進而插入一筆新的客戶訂單記錄 (SELECT ORDID FROM FINAL TABLE (INSERT INTO NEWORDERS (CUSTID,SHIPNAME,SHIPADDR,SHIPCITY,SHIPREG,SHIPZIP) VALUES (?,?,?,?,?,?)))。

## 建立使用 Informix 連接器來移除資料的邏輯應用程式 ##
您可以從 Azure Marketplace 建立新的邏輯應用程式，然後使用 Informix 連接器動作來移除客戶訂單。例如，您可以使用 Informix 連接器條件式刪除作業來處理 SQL DELETE 陳述式 (DELETE FROM NEWORDERS WHERE ORDID >= 10000)。

1. 在 Azure **開始面板**的中樞功能表中，依序按一下 **+** (加號)、[Web + 行動] 和 [邏輯應用程式]。 
2. 在 [建立邏輯應用程式] 刀鋒視窗中，輸入 [名稱]，例如 **RemoveOrdersInformix**。
3. 選取或定義其他設定 (例如服務計劃、資源群組) 的值。
4. 設定應如下所示。按一下 [建立]：![][12]
5. 在 [設定] 刀鋒視窗中，按一下 [觸發程序和動作]。
6. 在 [觸發程序和動作] 刀鋒視窗的 [邏輯應用程式範本] 清單中，按一下 [從頭建立]。
7. 在 [觸發程序和動作] 刀鋒視窗的 [API Apps] 面板中，按一下資源群組內的 [週期]。
8. 在邏輯應用程式設計介面上，按一下 [週期] 項目、設定 [頻率] 和 [間隔]，例如 [天] 和 [1]，然後按一下**核取記號**以儲存週期項目設定。
9. 在 [觸發程序和動作] 刀鋒視窗的 [API Apps] 面板中，按一下資源群組內的 [Informix 連接器]。
10. 在邏輯應用程式設計介面上，按一下 [Informix 連接器] 動作項目，按一下省略符號 (**...**) 以展開作業清單中，然後按一下 [條件式刪除自 N]。
11. 在 Informix 連接器動作項目上，針對 [識別項目子集的運算式] 輸入 **ordid ge 10000**。
12. 按一下**核取記號**以儲存動作設定，然後按一下 [儲存]。設定應如下所示：![][13]
13. 按一下以關閉 [觸發程序和動作] 刀鋒視窗，然後按一下以關閉 [設定] 刀鋒視窗。
14. 在 [作業] 之下的 [所有執行] 清單中，按一下第一個列出的項目 (最近一次執行)。
15. 在 [邏輯應用程式執行] 刀鋒視窗中，按一下 [動作] 項目。
16. 在 [邏輯應用程式動作] 刀鋒視窗中，按一下 [輸出連結]。輸出應如下所示：![][14]

**注意：**邏輯應用程式設計工具會截斷資料表名稱。例如，[條件式刪除自 NEWORDERS] 作業會被截斷成 [條件式刪除自 N]。


> [AZURE.TIP] 使用下列 SQL 陳述式來建立範例資料表與預存程序。

您可以使用下列 Informix SQL DDL 陳述式建立範例 NEWORDERS 資料表：
 
    create table neworders (  
 		ordid serial(10000) unique ,  
 		custid int not null ,  
 		empid int not null default 10000 ,  
 		orddate date not null default today ,  
 		reqdate date default today ,  
 		shipdate date ,  
 		shipid int not null default 10000 ,  
 		freight decimal (9,2) not null default 0.00 ,  
 		shipname char (40) not null ,  
 		shipaddr char (60) not null ,  
 		shipcity char (20) not null ,  
 		shipreg char (15) not null ,  
 		shipzip char (10) not null ,  
 		shipctry char (15) not null default ''USA'' 
 		)


您可以使用下列 Informix DDL 陳述式建立範例 SPORDERID 預存程序：
 
    create procedure sporderid ( ord_id int)  
 		returning int, int, int, date, date, date, int, decimal (9,2), char (40), char (60), char (20), char (15), char (10), char (15)  
 		define xordid, xcustid, xempid, xshipid int;  
 		define xorddate, xreqdate, xshipdate date;  
 		define xfreight decimal (9,2);  
 		define xshipname char (40);  
 		define xshipaddr char (60);  
 		define xshipcity char (20);  
 		define xshipreg, xshipctry char (15);  
 		define xshipzip char (10);  
 		select ordid, custid, empid, orddate, reqdate, shipdate, shipid, freight, shipname, shipaddr, shipcity, shipreg, shipzip, shipctry  
 			into xordid, xcustid, xempid, xorddate, xreqdate, xshipdate, xshipid, xfreight, xshipname, xshipaddr, xshipcity, xshipreg, xshipzip, xshipctry  
 			from neworders where ordid = ord_id;  
 		return xordid, xcustid, xempid, xorddate, xreqdate, xshipdate, xshipid, xfreight, xshipname, xshipaddr, xshipcity, xshipreg, xshipzip, xshipctry;  
    end procedure; 


## 混合式組態 (選用)

> [AZURE.NOTE] 只有當您在防火牆後方使用 DB2 連接器內部部署時，才需要此步驟。

App Service 使用混合式組態管理員來安全地連線到內部部署系統。如果連接器使用內部部署 IBM DB2 Server for Windows，則需要混合式連線管理員。

請參閱[使用混合式連線管理員](app-service-logic-hybrid-connection-manager.md)。


## 進一步運用您的連接器
現在已建立連接器，您可以將它加入到使用邏輯應用程式的商務工作流程。請參閱[什麼是邏輯應用程式？](app-service-logic-what-are-logic-apps.md)。

使用 REST API 建立 API Apps。請參閱[連接器和 API Apps 參考](http://go.microsoft.com/fwlink/p/?LinkId=529766)。

您也可以檢閱連接器的效能統計資料及控制安全性。請參閱[管理和監視內建 API Apps 和連接器](app-service-logic-monitor-your-connectors.md)。


<!--Image references-->
[1]: ./media/app-service-logic-connector-informix/ApiApp_InformixConnector_Create.png
[2]: ./media/app-service-logic-connector-informix/LogicApp_NewOrdersInformix_Create.png
[3]: ./media/app-service-logic-connector-informix/LogicApp_NewOrdersInformix_TriggersActions.png
[4]: ./media/app-service-logic-connector-informix/LogicApp_NewOrdersInformix_Outputs.png
[5]: ./media/app-service-logic-connector-informix/LogicApp_NewOrdersBulkInformix_Create.png
[6]: ./media/app-service-logic-connector-informix/LogicApp_NewOrdersBulkInformix_TriggersActions.png
[7]: ./media/app-service-logic-connector-informix/LogicApp_NewOrdersBulkInformix_Inputs.png
[8]: ./media/app-service-logic-connector-informix/LogicApp_NewOrdersBulkInformix_Outputs.png
[9]: ./media/app-service-logic-connector-informix/LogicApp_UpdateOrdersInformix_Create.png
[10]: ./media/app-service-logic-connector-informix/LogicApp_UpdateOrdersInformix_TriggersActions.png
[11]: ./media/app-service-logic-connector-informix/LogicApp_UpdateOrdersInformix_Outputs.png
[12]: ./media/app-service-logic-connector-informix/LogicApp_RemoveOrdersInformix_Create.png
[13]: ./media/app-service-logic-connector-informix/LogicApp_RemoveOrdersInformix_TriggersActions.png
[14]: ./media/app-service-logic-connector-informix/LogicApp_RemoveOrdersInformix_Outputs.png

<!---HONumber=AcomDC_0224_2016-->