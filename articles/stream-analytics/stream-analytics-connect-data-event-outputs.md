<properties 
	pageTitle="建立串流分析輸出 |Microsoft Azure" 
	description="了解如何連線以及設定輸出目標，以制定串流分析解決方案。" 
	documentationCenter="" 
	services="stream-analytics"
	authors="jeffstokes72" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="10/05/2015" 
	ms.author="jeffstok"/>

# 建立串流分析輸出

## 了解串流分析輸出 ##
---
在建立串流分析工作時，該工作輸出的取用方式也須加以考量。資料轉換的取用者會如何檢視串流分析工作的結果？ 他們將使用哪些工具來分析輸出？ 需要資料保留或倉儲嗎？

Azure 的串流分析提供儲存和檢視工作輸出的七種不同方法。SQL Database、Blob 儲存體、事件中樞、服務匯流排佇列、服務匯流排主題、Power BI 和資料表儲存體。針對資料倉儲和其他用途，這可提供易於檢視工作輸出的方式，也提供取用和儲存工作輸出的彈性。

## 使用 SQL Database 做為輸出。 ##
---
如果資料的本質上具備關聯性，或者應用程式所需的內容需由關聯式資料庫提供時，就可使用 SQL Database 做為資料輸出。如需 Azure SQL 資料庫的詳細資訊，請參閱 [Azure SQL Database](http://azure.microsoft.com/services/sql-database/)。

### 參數 ###

若要開始使用 SQL Database，將需要下列 SQL Database 的相關資訊：

1. 伺服器名稱
2. 資料庫名稱
3. 有效的使用者名稱/密碼組合
4. 輸出資料表名稱。此資料表必須已經存在，因為此作業不會建立該資料表。

### 加入 SQL Database 做為輸出 ###

![graphic1][graphic1]

移至工作的輸出索引標籤，然後依序按一下 [加入輸出] 命令和 [下一步]。

![graphic2][graphic2]

選擇 [SQL Database] 做為輸出。

![graphic3][graphic3]

在下一個頁面上輸入資料庫資訊。輸出別名是易記名稱，用於在查詢中將查詢輸出指向這個資料庫。如果只有一個輸出，預設別名就是 "output"。

![graphic4][graphic4]

如果該資料庫存在於與此串流工作相同的訂用帳戶中，則可使用這個選項來選取 [從目前的訂用帳戶使用 SQL Database]。然後從下拉式清單選取該資料庫。

![graphic5][graphic5]

要新增此輸出，請按 [下一步]。應該會啟動兩項作業，第一項是加入輸出。

![graphic6][graphic6]

第二項作業是測試連線。Azure 串流分析會嘗試連接至該 SQL Database，然後檢查輸入的認證是否正確，以及檢查是否可以存取資料表。

## 使用 Blob 儲存體做為輸出 ##
---
如需 Azure Blob 儲存體和其使用方式的簡介，請參閱[如何使用 Blob](./articles/storage/storage-dotnet-how-to-use-blobs.md) 中的文件。

### 參數 ###

若要開始使用 Blob 儲存體輸出，將需要以下資訊：

1. 如果儲存體帳戶位於串流工作以外的不同訂用帳戶，則會出現用於輸入儲存體帳戶名稱和儲存體帳戶金鑰的欄位。
2. 容器名稱。
3. 檔案名稱前置詞。
4. 該資料使用的序列化格式 (Avro、CSV、JSON)。

### 加入 Blob 儲存體做為輸出 ###

選擇 Blob 儲存體的輸出

![graphic20][graphic20]

然後提供詳細資料，如下所示：

![graphic21][graphic21]

## 使用事件中樞做為輸出 ##
---
### 概觀 ###
 
事件中樞可以擷取大量的事件，是最常見的 Stream Analytics 資料入口。強固的大量事件處理也使它們適合工作輸出。事件中樞當做輸出的一個用途，就是用於串流分析工作的輸出將成為另一個串流工作的輸入時。如需事件中樞的詳細資訊，請造訪[事件中樞](https://azure.microsoft.com/services/event-hubs/ "事件中心")入口網站。
 
### 參數 ###

設定事件中樞資料流時，還需要設定幾個參數。

1. 服務匯流排命名空間：事件中樞的服務匯流排命名空間。服務匯流排命名空間是一個容器，包含一組訊息實體。建立新的事件中樞時，也會建立服務匯流排命名空間。 
2. 事件中樞名稱：事件中樞的名稱。此為建立新事件中樞時所指定的名稱。 
3. 事件中樞原則名稱：共用存取原則 (用於存取事件中樞) 的名稱。您可以在 [設定] 索引標籤上，設定事件中樞的共用存取原則。每一個共用存取原則都會產生名稱、權限和存取金鑰。
4. 事件中樞原則索引鍵：共用存取原則 (用於存取事件中樞) 的主要或次要索引鍵。  
5. 資料分割索引鍵資料行： 事件中樞的輸出參數 (選擇性)。這個資料行包含事件中樞輸出的資料分割索引鍵。

### 加入事件中樞做為輸出 ###

1. 按一下頁面頂端的 [輸出]，然後按一下 [新增輸出]。選取將事件中樞做為輸出的選項，並按一下視窗底部右邊的按鈕。

    ![graphic38][graphic38]

2. 在輸出的欄位中提供相關資訊，完成時請按一下視窗底部右邊的按鈕以繼續。

    ![graphic36][graphic36]

3. 驗證事件序列化格式編碼類型，且格式已設定為適當的值，然後按一下核取方塊以完成動作。

    ![graphic37][graphic37]

## 使用 Power BI 做為輸出 ##
---
### 概觀 ###
可以使用 Power BI 做為串流分析工作的輸出，藉此提供串流分析使用者豐富的視覺體驗。這項功能可以用於可作業的儀表板、報告的產生及度量驅動的報告。如需 Power BI 的詳細資訊，請造訪 [Power BI](https://powerbi.microsoft.com/) 網站。

### 參數 ###

設定 Power BI 輸出時，還需要設定幾個參數。

1. 輸出別名 - 任何容易記住的輸出別名。如果決定讓工作擁有多個輸出，填寫輸出別名會非常有幫助。在此情況下，此別名將會在查詢中受到參考。例如，使用輸出別名值 = "OutPbi"。
2. 資料集名稱 - 提供 Power BI 輸出需要使用的資料集名稱。例如，使用 "pbidemo"。
2. 資料表名稱 - 提供 Power BI 輸出資料集的資料表名稱。例如，使用 "pbidemo"。**目前，串流分析工作的 Power BI 輸出中，一個資料集只能有一個資料表。**

### 加入 Power BI 做為輸出 ###

1.  按一下頁面頂端的 [輸出]，然後按一下 [新增輸出]。選取 Power BI 做為輸出選項。

    ![graphic22][graphic22]

2.  會顯示類似下列的畫面：

    ![graphic23][graphic23]

3.  在此步驟中，請提供工作或學校帳戶以授權 Power BI 輸出。如果您尚未註冊 Power BI，請選擇 [立即註冊]。
4.  接下來會顯示如下所示的畫面：

    ![graphic24][graphic24]


>	[AZURE.NOTE] One should not explicitly create the dataset and table in the Power BI dashboard. The dataset and table will be automatically populated when the job is started and the job starts pumping output into Power BI. Note that if the job query doesn’t return any results, the dataset and table will not be created. Also be aware that if Power BI already had a dataset and table with the same name as the one provided in this Stream Analytics job, the existing data will be overwritten.

*	依序按一下 [確定]、[測試連線]，即完成輸出組態。


## 使用 Azure 資料表儲存體做為輸出 ##
---
資料表儲存體提供高可用性且可大幅擴充的儲存體，方便應用程式自動擴充以滿足使用者需求。資料表儲存體是 Microsoft 的 NoSQL 索引鍵/屬性存放區，其中可以使用結構化資料，但結構描述的限制較少。使用 Azure 資料表儲存資料時，資料可長期儲存而且調閱方便。如需 Azure 資料表儲存體的詳細資訊，請造訪 [Azure 資料表儲存體](./articles/storage/storage-introduction.md)。

### 參數 ###

若要開始使用 Azure 資料表儲存體，需要以下資訊：

1. 儲存體帳戶名稱 (如果這個儲存體與串流工作中的訂用帳戶不同)。
2. 儲存體帳戶金鑰 (如果這個儲存體與串流工作中的訂用帳戶不同)。
3. 輸出資料表名稱 (如果沒有，我們會建立這個名稱)。
4. 資料分割索引鍵 (必填)。
5. 列索引鍵

為了設計更好的資料分割索引鍵和資料列索引鍵，請參閱下列文章[為 Azure Table Storage 設計可擴充的資料分割策略](https://msdn.microsoft.com/library/azure/hh508997.aspx)。

### 加入 Azure 資料表儲存體做為輸出 ###

![graphic11][graphic11]

移至工作的輸出索引標籤，然後依序按一下 [加入輸出] 命令和 [下一步]。

![graphic12][graphic12]

選擇 [資料表儲存體] 做為輸出。

![graphic13][graphic13]

在下一個頁面上輸入 Azure 資料表資訊。輸出別名為可在查詢中用來將查詢輸出指向這個資料表的名稱。

![graphic14][graphic14]

![graphic15][graphic15]

批次大小為批次作業的記錄數目。預設值通常足以應付大部分的工作，如需修改此設定的詳細資訊，請參閱[資料表批次作業規格](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablebatchoperation.aspx)。

如果 Azure 儲存體存在於用來建立該工作的相同訂用帳戶中，請選取 [使用目前訂用帳戶的儲存體帳戶]，然後從下拉式清單中選取儲存體帳戶。

要新增此輸出，請按 [下一步]。應該會啟動兩項作業，第一項是加入輸出。

![graphic16][graphic16]

第二個操作是測試連線。Azure 串流分析會嘗試連線至儲存體帳戶，然後檢查輸入的認證是否正確。

## 服務匯流排佇列 ##
---
### 服務匯流排佇列概念簡介 ###
如果有一或多個競爭取用者，服務匯流排佇列會採取先進先出 (FIFO) 訊息傳遞機制。通常會預期由接收者依訊息加入佇列的時間順序來接收和處理訊息，而且每則訊息只能由一個訊息取用者接收和處理。

如需服務匯流排佇列的詳細資訊，請參閱[服務匯流排佇列、主題和訂閱](https://msdn.microsoft.com/library/azure/hh367516.aspx "服務匯流排佇列、主題和訂閱")和[服務匯流排佇列簡介](http://blogs.msdn.com/b/appfabric/archive/2011/05/17/an-introduction-to-service-bus-queues.aspx "服務匯流排佇列簡介")。

### 參數 ###

若要開始使用服務匯流排佇列輸出，將需要以下資訊：

1. 輸出別名 - 任何容易記住的輸出別名。如果決定讓工作擁有多個輸出，填寫輸出別名會非常有幫助。在此情況下，此別名將會在工作查詢中受到參考。
2. 命名空間和服務匯流排的名稱。
3. 佇列名稱 - 佇列為訊息實體，類似於事件中樞和主題。它們可以收集各種裝置和服務的事件資料流。建立佇列時也會給予其特定名稱。
4. 該資料使用的序列化格式 (Avro、CSV、JSON)。

### 加入服務匯流排佇列做為輸出 ###

![graphic31][graphic31]

然後提供如下所示的詳細資料，並選取 [下一步]。

![graphic32][graphic32]

確認資料格式和序列化正確無誤。

![graphic33][graphic33]

## 服務匯流排主題 ##
---
### 服務匯流排主題概念簡介 ###
相較於服務匯流排佇列會提供從傳送者到接收者的一對一通訊方法，服務匯流排主題則是提供一對多的通訊形式。

如需服務匯流排主題的詳細資訊，請參閱[服務匯流排佇列、主題和訂閱](https://msdn.microsoft.com/library/azure/hh367516.aspx "服務匯流排佇列、主題和訂閱")和[服務匯流排主題簡介](http://blogs.msdn.com/b/appfabric/archive/2011/05/25/an-introduction-to-service-bus-topics.aspx "服務匯流排主題簡介")。

### 參數 ###

若要開始使用服務匯流排主題輸出，將需要以下資訊：

1. 輸出別名 - 任何容易記住的輸出別名。如果決定讓工作擁有多個輸出，填寫輸出別名會非常有幫助。在此情況下，此別名將會在查詢中受到參考。
2. 命名空間和服務匯流排的名稱。
3. 主題名稱 - 主題為訊息實體，類似於事件中樞和佇列。它們可以收集各種裝置和服務的事件資料流。建立主題時也會給予其特定名稱。除非已建立訂用帳戶，否則傳送至主題的訊息將無法使用，所以請確保該主題內有一或多個訂用帳戶。
4. 該資料使用的序列化格式 (Avro、CSV、JSON)。

### 加入服務匯流排主題做為輸出 ###

選取服務匯流排主題的輸出。

![graphic34][graphic34]

然後提供如下所示的詳細資料，並選取 [下一步]。

![graphic35][graphic35]

確認資料格式和序列化正確無誤。

![graphic33][graphic33]


## 取得說明
如需進一步的協助，請參閱我們的 [Azure Stream Analytics 論壇](https://social.msdn.microsoft.com/Forums/zh-TW/home?forum=AzureStreamAnalytics)

## 後續步驟

- [Azure Stream Analytics 介紹](stream-analytics-introduction.md)
- [開始使用 Azure Stream Analytics](stream-analytics-get-started.md)
- [調整 Azure Stream Analytics 工作](stream-analytics-scale-jobs.md)
- [Azure Stream Analytics 查詢語言參考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure 串流分析管理 REST API 參考](https://msdn.microsoft.com/library/azure/dn835031.aspx)




[graphic1]: ./media/stream-analytics-connect-data-event-outputs/1-stream-analytics-connect-data-event-input-output.png
[graphic2]: ./media/stream-analytics-connect-data-event-outputs/2-stream-analytics-connect-data-event-input-output.png
[graphic3]: ./media/stream-analytics-connect-data-event-outputs/3-stream-analytics-connect-data-event-input-output.png
[graphic4]: ./media/stream-analytics-connect-data-event-outputs/4-stream-analytics-connect-data-event-input-output.png
[graphic5]: ./media/stream-analytics-connect-data-event-outputs/5-stream-analytics-connect-data-event-input-output.png
[graphic6]: ./media/stream-analytics-connect-data-event-outputs/6-stream-analytics-connect-data-event-input-output.png
[graphic7]: ./media/stream-analytics-connect-data-event-outputs/7-stream-analytics-connect-data-event-input-output.png
[graphic8]: ./media/stream-analytics-connect-data-event-outputs/8-stream-analytics-connect-data-event-input-output.png
[graphic9]: ./media/stream-analytics-connect-data-event-outputs/9-stream-analytics-connect-data-event-input-output.png
[graphic10]: ./media/stream-analytics-connect-data-event-outputs/10-stream-analytics-connect-data-event-input-output.png
[graphic11]: ./media/stream-analytics-connect-data-event-outputs/11-stream-analytics-connect-data-event-input-output.png
[graphic12]: ./media/stream-analytics-connect-data-event-outputs/12-stream-analytics-connect-data-event-input-output.png
[graphic13]: ./media/stream-analytics-connect-data-event-outputs/13-stream-analytics-connect-data-event-input-output.png
[graphic14]: ./media/stream-analytics-connect-data-event-outputs/14-stream-analytics-connect-data-event-input-output.png
[graphic15]: ./media/stream-analytics-connect-data-event-outputs/15-stream-analytics-connect-data-event-input-output.png
[graphic16]: ./media/stream-analytics-connect-data-event-outputs/16-stream-analytics-connect-data-event-input-output.png
[graphic17]: ./media/stream-analytics-connect-data-event-outputs/17-stream-analytics-connect-data-event-input-output.png
[graphic18]: ./media/stream-analytics-connect-data-event-outputs/18-stream-analytics-connect-data-event-input-output.png
[graphic19]: ./media/stream-analytics-connect-data-event-outputs/19-stream-analytics-connect-data-event-input-output.png
[graphic20]: ./media/stream-analytics-connect-data-event-outputs/20-stream-analytics-connect-data-event-input-output.png
[graphic21]: ./media/stream-analytics-connect-data-event-outputs/21-stream-analytics-connect-data-event-input-output.png
[graphic22]: ./media/stream-analytics-connect-data-event-outputs/22-stream-analytics-connect-data-event-input-output.png
[graphic23]: ./media/stream-analytics-connect-data-event-outputs/23-stream-analytics-connect-data-event-input-output.png
[graphic24]: ./media/stream-analytics-connect-data-event-outputs/24-stream-analytics-connect-data-event-input-output.png
[graphic25]: ./media/stream-analytics-connect-data-event-outputs/25-stream-analytics-connect-data-event-input-output.png
[graphic26]: ./media/stream-analytics-connect-data-event-outputs/26-stream-analytics-connect-data-event-input-output.png
[graphic27]: ./media/stream-analytics-connect-data-event-outputs/27-stream-analytics-connect-data-event-input-output.png
[graphic28]: ./media/stream-analytics-connect-data-event-outputs/28-stream-analytics-connect-data-event-input-output.png
[graphic29]: ./media/stream-analytics-connect-data-event-outputs/29-stream-analytics-connect-data-event-input-output.png
[graphic30]: ./media/stream-analytics-connect-data-event-outputs/30-stream-analytics-connect-data-event-input-output.png
[graphic31]: ./media/stream-analytics-connect-data-event-outputs/31-stream-analytics-connect-data-event-input-output.png
[graphic32]: ./media/stream-analytics-connect-data-event-outputs/32-stream-analytics-connect-data-event-input-output.png
[graphic33]: ./media/stream-analytics-connect-data-event-outputs/33-stream-analytics-connect-data-event-input-output.png
[graphic34]: ./media/stream-analytics-connect-data-event-outputs/34-stream-analytics-connect-data-event-input-output.png
[graphic35]: ./media/stream-analytics-connect-data-event-outputs/35-stream-analytics-connect-data-event-input-output.png
[graphic36]: ./media/stream-analytics-connect-data-event-outputs/36-stream-analytics-connect-data-event-input-output.png
[graphic37]: ./media/stream-analytics-connect-data-event-outputs/37-stream-analytics-connect-data-event-input-output.png
[graphic38]: ./media/stream-analytics-connect-data-event-outputs/38-stream-analytics-connect-data-event-input-output.png

<!---HONumber=Oct15_HO3-->