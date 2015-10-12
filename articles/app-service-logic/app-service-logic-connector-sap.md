<properties
   pageTitle="在 Logic Apps 中使用 SAP 連接器 | Microsoft Azure App Service"
   description="如何建立並設定 SAP 連接器或 API 應用程式，並在 Azure App Service 的邏輯應用程式中使用它"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="harishkragarwal"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="08/23/2015"
   ms.author="sameerch"/>


# 開始使用 SAP 連接器並將它加入您的邏輯應用程式
連線至內部部署 SAP 以呼叫 RFC 或取得中繼資料。在某些案例中，您可能需要使用在內部部署安裝並位於防火牆後的 SAP。透過在流程中運用 SAP 連接器，您可以達到各種案例的目的。幾個範例：

1.	透過 web 或行動使用者前端公開位於 SAP 上的部分資料。
2.	處理到期後將資料發佈到 SAP
3.	從 SAP 擷取資料，以供商務程序使用

在邏輯應用程式中，連接器可以在執行流程時用來擷取、處理或發送資料。您可以將 SAP 連接器加入您的商務工作流程，就能在邏輯應用程式的該工作流程中處理資料。


在這些案例中，請務必完成下列需求：

1. 建立 SAP 連接器 API 應用程式的執行個體
2. 建置 API 應用程式可與內部部署 SAP 進行通訊的混合式連線
3. 在邏輯應用程式中使用建立的 API 應用程式，以達成所需商務程序的目的


## 建立 SAP 連接器 API 應用程式的執行個體 ##

連接器可以在邏輯應用程式內建立，或直接從 Azure Marketplace 建立。從 Marketplace 建立連接器：

1. 在 Azure 開始面板中，選取 [Marketplace]。
2. 搜尋「SAP 連接器」，將其選取，然後選取 [建立]。
3. 進行下列設定：
	1. 在第一個分頁中提供一般詳細資訊，例如名稱、應用程式服務方案等等

	2. 封裝設定過程中必須提供 SAP 認證。並提供 Azure 服務匯流排連接字串。這可用來建置與內部部署 SAP 的混合式連線。

	3. RFC、TRFC、BAPI 和 IDOC 必須根據案例需求進行設定。如果需要多個值，則以逗號分隔的方式提供

![][1]

## 設定剛建立的 SAP 連接器 API 應用程式 ##

透過 [瀏覽] -> [API 應用程式] -> [<Name of the API App just created>] 瀏覽到剛建立的 API 應用程式，您將會看到下列行為。因為尚未建立混合式連線，所以安裝未完成：![][2]

SAP 連接器需要混合式連線才能連接到*任何* SAP 端點。若要建置混合式連線，請執行下列作業：

1. 複製主要連接字串
2. 按一下 [下載及設定] 連結
3. 依照剛起始的安裝程序執行，並在要求時提供主要連接字串
4. 完成安裝程序後，便會顯示如下所示的對話方塊：![][3]

現在當您重新瀏覽至建立的 API 應用程式時，您會看到混合式連線狀態為 [已連線]：![][4]

注意：如果您要切換到次要連線字串，您只需重做混合式設定，並提供次要連接字串來取代主要連接字串即可

## 邏輯應用程式中的使用方式 ##

在邏輯應用程式中，SAP 連接器只可以做為動作/步驟使用。

建立/編輯邏輯應用程式時，請選擇上面剛建立的 SAP 連接器 API 應用程式。這會一次列出可供選擇的所有允許動作：![][5]

選取動作時，會同時列出該動作的輸入參數。請提供適當的值，並按一下 [勾號] 圖示：![][6]

現在，步驟/動作在邏輯應用程式中顯示為已設定。作業的輸出會隨即顯示，而可在後續步驟中用做輸入值：![][7]

完成邏輯應用程式以定義商務程序，然後執行它來達到所需的目的。

## 進一步運用您的連接器
現在已建立連接器，您可以將它加入到使用邏輯應用程式的商務工作流程。請參閱[什麼是邏輯應用程式？](app-service-logic-what-are-logic-apps.md)。

>[AZURE.NOTE]如果您想在註冊 Azure 帳戶前開始使用 Azure Logic Apps，請移至[試用 Logic App](https://tryappservice.azure.com/?appservice=logic)，即可在 App Service 中立即建立短期入門邏輯應用程式。不需要信用卡，無需承諾。

檢視位於[連接器和 API Apps 參考](http://go.microsoft.com/fwlink/p/?LinkId=529766)的 Swagger REST API 參考。

您也可以檢閱連接器的效能統計資料及控制安全性。請參閱[管理和監視內建 API Apps 和連接器](app-service-logic-monitor-your-connectors.md)。

<!--Image references-->
[1]: ./media/app-service-logic-connector-sap/Create.jpg
[2]: ./media/app-service-logic-connector-sap/BrowseSetupIncomplete.jpg
[3]: ./media/app-service-logic-connector-sap/HybridSetup.jpg
[4]: ./media/app-service-logic-connector-sap/BrowseSetupComplete.jpg
[5]: ./media/app-service-logic-connector-sap/LogicApp1.jpg
[6]: ./media/app-service-logic-connector-sap/LogicApp2.jpg
[7]: ./media/app-service-logic-connector-sap/LogicApp3.jpg

<!---HONumber=Oct15_HO1-->