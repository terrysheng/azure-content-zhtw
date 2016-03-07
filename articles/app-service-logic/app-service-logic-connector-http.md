<properties
   pageTitle="在 Logic Apps 中使用 HTTP 接聽程式和連接器 | Microsoft Azure App Service"
   description="如何建立並設定 HTTP 接聽程式和 HTTP 動作連接器或 API 應用程式，並在 Azure App Service 的邏輯應用程式中使用它"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="anuragdalmia"
   manager="erikre"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="02/11/2016"
   ms.author="prkumar"/>


# 開始使用 HTTP 接聽程式和 HTTP 動作，並將它加入您的邏輯應用程式
>[AZURE.NOTE] 這一版文章適用於邏輯應用程式 2014-12-01-preview 結構描述版本。

直接連線到 HTTP 資源以接聽 HTTP 要求和設定 HTTP Web 要求。在某些情況下，您可能需要使用直接 HTTP 連線，包括：

1.	若要開發支援 Web 或行動使用者互動前端的邏輯應用程式。
2.	若要取得並處理沒有現成連接器之 Web 服務的資料。
3.	若要執行已公開為 web 服務，但無法當做 API 應用程式使用的動作。

對於這些案例，有兩個選項：

1. **HTTP 接聽程式**：這個連接器可做為觸發程序，並接聽設定的端點上的 HTTP 要求。設定的端點上收到呼叫時，它會觸發流程的新執行個體，並將要求中所收到的資料傳遞到處理的流程中。它也可以設定為在啟動流程時自動回應內送要求，或可讓您根據流程執行建構回應並傳送回應給呼叫者。
2. **HTTP 動作**：這可讓您將 Web 要求設定到網際網路上任何可用的端點、取得回應，並提供給流程中的其他動作取用。

邏輯應用程式可以根據各種資料來源觸發，並提供連接器以取得及處理屬於流程一部分的資料。您可以將 HTTP 連接器加入您的商務工作流程，就能在邏輯應用程式的該工作流程中處理資料。

## 建立邏輯應用程式的 HTTP 接聽程式
連接器可以在邏輯應用程式內建立，或直接從 Azure Marketplace 建立。從 Marketplace 建立連接器：

1. 在 Azure 開始面板中，選取 [Marketplace]。
2. 搜尋 "HTTP"，選取 HTTP 接聽程式，然後選取 [建立]。
3.	設定 HTTP 接聽程式，如下所示：![][1]

4.	進行封裝設定時，您會看到下列有關接聽程式是否應該自動回應或要求您傳送明確回應的選項。將此選項設為 **False** 以傳送自己的回應︰![][2]

5.	按一下 [確定] 以建立。
6.	建立 API 應用程式執行個體後，請開啟設定以設定安全性。HTTP 接聽程式目前支援基本驗證。開啟 HTTP 接聽程式時，您可以使用 [安全性] 選項進行設定︰![][3]
  
	**已知問題** *安全性設定會顯示 [無] 做為預設值，但是它還未定義。您必須將設定變更為 [基本]，然後在儲存它之前變回 [無]，以確定正確地設定 HTTP 接聽程式。*

7. 最後，將 API 應用程式的安全性設定設為 [公用 (匿名)]，以允許外部用戶端存取端點。在 HTTP 接聽程式 API 應用程式的 [所有設定] > [應用程式設定] 下可取得此設定︰![][10]

一旦完成，您立即可建立邏輯應用程式來使用 HTTP 接聽程式。

## 在邏輯應用程式中使用 HTTP 接聽程式
建立 API 應用程式之後，您現在可以使用 HTTP 接聽程式做為邏輯應用程式的觸發程序。若要這樣做，您需要：

4.	建立新的邏輯應用程式。
5.	開啟 [觸發程序和動作] 以開啟 Logic Apps 設計工具，並設定您的流程。HTTP 接聽程式會列在資源庫中。請選取它。
6.	您現在可以設定 HTTP 方法以及需要接聽程式在其上觸發流程的相對 URL︰![][4] ![][5]

7.	若要取得完整的 URI，請按兩下 [HTTP 接聽程式] 以檢視其組態設定，並複製 API 應用程式的「主機」URL︰![][6]
8.	您現在可以在流程的其他動作中使用 HTTP 要求中所接收的資料，如下所示：![][7] ![][8]
9.	最後，若要傳送回應，請新增另一個 HTTP 接聽程式，並選取 [傳送 HTTP 回應] 動作。將要求識別碼設定為從 HTTP 接聽程式取得的 RequestID，並填入您想要傳回的回應本文和 HTTP 狀態：![][9]

## 使用 HTTP 動作
Logic Apps 原生支援 HTTP 動作，而且無需建立 API 應用程式即可使用。您可以在應用程式邏輯的任何位置插入 HTTP 動作，並選擇呼叫的 URI、標頭和主體。HTTP 動作支援多個用戶端安全性選項。請參閱[用戶端的安全性選項](../scheduler/scheduler-outbound-authentication.md)。

HTTP 動作的輸出會是標頭和主體，可進一步在流程的下游中使用，且與取用其他動作和連接器的輸出方式類似。

## 進一步運用您的連接器
現在已建立連接器，您可以將它加入到使用邏輯應用程式的商務工作流程。請參閱[什麼是邏輯應用程式？](app-service-logic-what-are-logic-apps.md)。

檢視位於[連接器和 API Apps 參考](http://go.microsoft.com/fwlink/p/?LinkId=529766)的 Swagger REST API 參考。

您也可以檢閱連接器的效能統計資料及控制安全性。請參閱〈[管理和監視內建 API Apps 和連接器](app-service-logic-monitor-your-connectors.md)〉。

> [AZURE.NOTE] 如果您想要在註冊 Azure 帳戶之前先開始使用 Azure Logic Apps，請移至[試用 Logic Apps](https://tryappservice.azure.com/?appservice=logic)。您可以在 App Service 中立即建立短期的入門邏輯應用程式。不需要信用卡；無需承諾。

<!--Image references-->
[1]: ./media/app-service-logic-connector-http/1.png
[2]: ./media/app-service-logic-connector-http/2.png
[3]: ./media/app-service-logic-connector-http/3.png
[4]: ./media/app-service-logic-connector-http/4.png
[5]: ./media/app-service-logic-connector-http/5.png
[6]: ./media/app-service-logic-connector-http/6.png
[7]: ./media/app-service-logic-connector-http/7.png
[8]: ./media/app-service-logic-connector-http/8.png
[9]: ./media/app-service-logic-connector-http/9.png
[10]: ./media/app-service-logic-connector-http/10.png

<!---HONumber=AcomDC_0224_2016-->