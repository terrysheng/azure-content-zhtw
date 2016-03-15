<properties
   pageTitle="在 Azure App Service 中使用邏輯應用程式內的 VETR 建立 EAI Logic Apps | Microsoft Azure"
   description="BizTalk XML 服務的驗證、編碼和轉換功能"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="rajeshramabathiran"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/18/2016"
   ms.author="rajram"/>


# 使用 VETR 建立 EAI 邏輯應用程式

>[AZURE.NOTE] 這一版的文章適用於邏輯應用程式 2014-12-01-preview 結構描述版本。

大部分的企業應用程式整合 (EAI) 案例可在來源與目的地之間傳達資料。此種案例通常會有一組常見的需求：

- 確定來自不同系統的資料格式正確。
- 對內送資料執行「查閱」動作以便制定決策。
- 將資料從一種格式轉換成另一種格式。例如，將資料從 CRM 系統的資料格式轉換成 ERP 系統的資料格式。
- 將資料路由到所需的應用程式或系統。

本文將說明常見的整合模式：「單向訊息中繼」，即 VETR (驗證、擴充、轉換、路由)。VETR 模式會在來源實體與目的地實體之間傳達資料。通常來源和目的地為資料來源。

想像一個接受訂單的網站。使用者會使用 HTTP 將訂單張貼至系統。系統會在幕後驗證內送資料的正確性、將其正規化，並將它保存在服務匯流排佇列中以進行後續處理。系統會將訂單從佇列中移除，預期它會採用特定的格式。因此：端對端流程會是：

**HTTP** → **驗證** → **轉換** → **服務匯流排**

![基本 VETR 流程][1]

下列的 BizTalk API Apps 可協助您建立這種模式：

* **HTTP 觸發程序** - 觸發訊息事件的來源
* **驗證** - 驗證內送資料的正確性
* **轉換** - 將資料從內送格式轉換成下游系統所需的格式
* **服務匯流排連接器** - 傳送資料的目的地實體


## 建構基本的 VETR 模式
### 基本概念

在 Azure 入口網站中，依序選取 [+ 新增]、[Web + 行動] 和 [邏輯應用程式]。選擇名稱、位置、訂閱、資源群組以及運作位置。資源群組可做為應用程式的容器使用，且應用程式的所有資源都屬於相同的資源群組。

接下來，讓我們新增觸發程序和動作。


## 新增 HTTP 觸發程序
1. 在 [邏輯應用程式範本] 中，選取 [從頭建立]。
1. 從組件庫中選取 [**HTTP 接聽程式**] 以建立新的接聽程式。稱它為 **HTTP1**。
2. 將 [是否自動傳送回應？] 設定設為 false。透過將 [HTTP 方法]設定為 [POST]，並將 [相對 URL] 設定為 _/OneWayPipeline_，來設定觸發程序動作：![HTTP 觸發程序][2]
3. 選取綠色打勾記號來完成觸發程序。

## 加入驗證動作

現在，讓我們輸入每當引發觸發程序時 (亦即，每當 HTTP 端點上收到呼叫時) 所要執行的動作。

1. 從組件庫中新增 **BizTalk XML 驗證器**，並將它命名為 _(Validate1)_ 以建立執行個體。
2. 設定 XSD 結構描述來驗證內送的 XML 訊息。選取 _Validate_ 動作，然後選取 _triggers(‘httplistener’).outputs.Content_ 做為 _inputXml_ 參數的值。

目前，驗證動作是 HTTP 接聽程式之後的第一個動作：

![BizTalk XML 驗證器][3]

讓我們以類似的方式加入其餘動作。

## 新增轉換動作
我們將設定可標準化內送資料的轉換。

1. 從資源庫中新增 [BizTalk 轉換服務]。
2. 若要設定轉換以轉換內送 XML 訊息，請選取 [轉換] 動作做為呼叫這個 API 時要執行的動作。選取 ```triggers(‘httplistener’).outputs.Content``` 做為 [inputXml] 的值。因為內送資料符合所有已設定的轉換，因此*對應*會是選擇性參數，而且僅適用於符合結構描述的對應。
3. 最後，只有當驗證成功時才會執行轉換。若要設定此條件，請選取右上方的齒輪圖示，然後選取 [新增要符合的條件]。將條件設為 ```equals(actions('xmlvalidator').status,'Succeeded')```：  

![BizTalk 轉換][4]


## 新增服務匯流排連接器
接下來，我們將新增可寫入資料的目的地 (服務匯流排佇列)。

1. 從組件庫新增 [**服務匯流排連接器**]。將 **Name** 設為 _Servicebus1_、將 **Connection String** 設為服務匯流排執行個體的連接字串、將 **Entity Name** 設為 _Queue_，然後略過 **Subscription name**。
2. 選取 [傳送訊息] 動作，並將動作的 [內容] 欄位設為 _actions('transformservice').outputs.OutputXml_。
3. 將 [內容類型] 欄位設為 [應用程式/xml]：  

![服務匯流排][5]


## 傳送 HTTP 回應
完成管線處理後，以下列步驟傳回成功和失敗的 HTTP 回應：

1. 從組件庫新增 [**HTTP 接聽程式**]，然後選取 [**傳送 HTTP 回應**] 動作。
2. 設定用以傳送*訊息*的 [回應識別碼]。
2. 將 [回應內容] 設為 [已完成管線處理]。
3. [回應狀態碼] 為 *200* 表示 HTTP 200 沒問題。
4. 選取右上方的下拉式功能表，然後選取 [新增要符合的條件]。將條件設為下列運算式：```@equals(actions('azureservicebusconnector').status,'Succeeded')``` <br/>
5. 您也可以重複上述步驟來傳送失敗的 HTTP 回應。變更 [條件] 為下列運算式：```@not(equals(actions('azureservicebusconnector').status,'Succeeded'))``` <br/>
6. 依序選取 [確定] 和 [建立]。



## 完成
每次有人傳送訊息至 HTTP 端點時，它會觸發應用程式並執行您剛建立的動作。若要管理任何您所建立的此類邏輯應用程式，請在 Azure 入口網站中依序選取 [瀏覽] 和 [Logic Apps]。選取您的 App，以查看詳細資訊。

一些有用的主題：

[管理和監視 API Apps 和連接器](app-service-logic-monitor-your-connectors.md) <br/> [監視 Logic Apps](app-service-logic-monitor-your-logic-apps.md)

<!--image references -->
[1]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/BasicVETR.PNG
[2]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/HTTPListener.PNG
[3]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/BizTalkXMLValidator.PNG
[4]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/BizTalkTransforms.PNG
[5]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/AzureServiceBus.PNG

<!---HONumber=AcomDC_0302_2016-------->