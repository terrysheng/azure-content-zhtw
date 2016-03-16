<properties 
   pageTitle="通知使用者從感應器或其他系統接收到的資料 | Microsoft Azure"
   description="說明如何使用事件中樞通知使用者有感應器資料。"
   services="event-hubs"
   documentationCenter="na"
   authors="spyrossak"
   manager="timlt"
   editor="" />
<tags 
   ms.service="event-hubs"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/16/2015"
   ms.author="spyros;sethm" />

# 通知使用者從感應器或其他系統接收到的資料

假設您有應用程式會即時監視資料，或依排程產生報告。當您查看顯示這些即時圖表或報告的網站時，您可能會看到需要採取因應動作的項目。如果您需要這些狀況的相關警示來提醒您，而非依賴記性來查看網站，將會如何？ 假設您在溫室裡設置了植物生長燈，而您需要在燈光熄滅時立即得知。要這麼做，方法之一是在溫室裡使用光感應器，並設定成在燈光熄滅時傳送電子郵件。

![][1]

在另一個案例中，假設您經營寵物店，而必須在庫存供應水準偏低時收到警示。例如，您可以設定在狗食的庫存嚴重偏低時，透過您的 ERP 系統接收簡訊。

![][2]

問題在於，如何在符合特定條件時獲得重要資訊，而不是抽出時間查看靜態報告。如果您使用 [Azure 事件中樞][]或 [IoT 中樞][]，從裝置或企業應用程式 (例如 [Dynamics AX][]) 接收資料，您可以透過數種方式來處理這些資料。您可以在網站上檢視資料、您可以分析資料、您可以儲存資料，且您可以使用資料觸發命令，以執行某些動作。若要這麼做，您可以使用功能強大的工具，例如 [Azure 網站][]、[SQL Azure][]、[HDInsight][]、[Cortana 分析套件][]、[IoT 套件][]、[邏輯應用程式][]或 [Azure 通知中樞][]。但有時您只是想以最低的額外負荷將該資料傳送給其他人。為了說明如何以些許程式碼進行此作業，我們提供了新的範例 [AppToNotifyUsers][]。包含的選項為電子郵件 (SMTP)、SMS 和電話。

## 應用程式結構

應用程式以 C# 撰寫，範例中的 Readme 檔案則包含您要修改、建置及發佈應用程式所需的所有資訊。下列各節提供應用程式之用途的高階概觀。

首先，我們假設您有重大事件要推送至 Azure 事件中樞或 IoT 中樞。任何中樞都可以使用，只要您有其存取權，並且知道連接字串即可。

如果您還沒有事件中樞或 IoT 中樞，您可以依照[逐步操作](https://github.com/Azure/connectthedots)專案中的指示，透過 Arduino Shield 與 Raspberry Pi 輕鬆設定測試平台。Arduino Shield 上的光感應器會透過 Pi 將光度數據傳送至 [Azure 事件中樞][] (**ehdevices**)，如果接收到的光度數據低於特定水準，[Azure 串流分析](https://azure.microsoft.com/services/stream-analytics/)作業即會將警示傳送至第二個事件中樞 (**ehalerts**)。

**AppToNotify** 在啟動時將會讀取組態檔 (App.config)，以取得接收警示之事件中心的 URL 和認證。接著，它會衍生出另一個程序，以持續監視該事件中樞是否有任何訊息傳入 – 只要您可以存取事件中樞或 IoT 中樞的 URL，並具備有效的認證，此事件中樞讀取器程式碼即會持續讀取傳入的訊息。在啟動期間，應用程式也會讀取您想要使用的訊息服務 (電子郵件、SMS、電話) 的 URL 和認證，以及寄件者的名稱/位址和收件者清單。

事件中樞監視器一旦偵測到訊息，即會觸發一個程序，以使用組態檔中指定的方法傳送該訊息。請注意，它不會傳送本身偵測到的每個訊息。如果您將監視器設定為指向每秒會接收十個訊息的事件中樞，則寄件者每秒將會傳送十個訊息 – 每秒十則電子郵件、每秒十個 SMS 訊息、每秒十通電話。因此，請確定您所監視的事件中樞只會接收需要傳送出去的警示，而不是會從您的感應器或應用程式接收所有原始資料的事件中樞。

## 適用性

此範例中的程式碼只會說明如何監視事件中樞，以及在您想要將這項功能新增至應用程式時如何呼叫外部訊息服務。請注意，此解決方案是需要獨立完成、側重於開發人員需求的範例。它無法因應企業需求，例如在失敗時進行備援、容錯移轉、重新啟動等。如需較全面的生產解決方案，請參閱下列各項：

- 透過 [Azure Logic Apps](../app-service-logic/app-service-logic-connectors-list.md) 服務，使用連接器或推播通知。
- 依照部落格[使用 Azure 通知中樞將推播通知廣播給數百萬個行動裝置](http://weblogs.asp.net/scottgu/broadcast-push-notifications-to-millions-of-mobile-devices-using-windows-azure-notification-hubs)中的說明，使用 [Azure 通知中樞](https://msdn.microsoft.com/library/azure/jj927170.aspx)。 

## 後續步驟

建立簡單的通知服務，將電子郵件或簡訊傳送給收件者 (或打電話給他們)，以轉送事件中樞或 IoT 中樞接收到的資料，是很單純的作業。若要部署解決方案，以根據這些中樞所接收的資料來通知使用者，請瀏覽 [AppToNotifyUsers][]。

如需關於些中樞的詳細資訊，請參閱下列文章：

- [Azure 事件中樞]
- [Azure IoT 中心]
- 開始使用[事件中樞教學課程]。
- [使用事件中樞的完整範例應用程式]。
- 使用服務匯流排佇列的[佇列訊息解決方案]。

若要部署解決方案，以根據這些中樞所接收的資料來通知使用者，請瀏覽：

- [AppToNotifyUsers][]

[事件中樞教學課程]: event-hubs-csharp-ephcs-getstarted.md
[Azure IoT 中心]: https://azure.microsoft.com/services/iot-hub/
[IoT 中樞]: https://azure.microsoft.com/services/iot-hub/
[Azure 事件中樞]: https://azure.microsoft.com/services/event-hubs/
[Azure 事件中樞]: https://azure.microsoft.com/services/event-hubs/
[使用事件中樞的完整範例應用程式]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Event-Hub-286fd097
[佇列訊息解決方案]: ../service-bus-dotnet-multi-tier-app-using-service-bus-queues.md
[AppToNotifyUsers]: https://github.com/Azure-Samples/event-hubs-dotnet-user-notifications
[Dynamics AX]: http://www.microsoft.com/en-us/dynamics/erp-ax-overview.aspx
[Azure 網站]: https://azure.microsoft.com/services/app-service/web/
[SQL Azure]: https://azure.microsoft.com/services/sql-database/
[HDInsight]: https://azure.microsoft.com/services/hdinsight/
[Cortana 分析套件]: http://www.microsoft.com/server-cloud/cortana-analytics-suite/Overview.aspx?WT.srch=1&WT.mc_ID=SEM_lLFwOJm3&bknode=BlueKai
[IoT 套件]: https://azure.microsoft.com/solutions/iot-suite/
[邏輯應用程式]: https://azure.microsoft.com/services/app-service/logic/
[Azure 通知中樞]: https://azure.microsoft.com/services/notification-hubs/
[Azure Stream Analytics]: https://azure.microsoft.com/services/stream-analytics/
 
[1]: ./media/event-hubs-sensors-notify-users/event-hubs-sensor-alert.png
[2]: ./media/event-hubs-sensors-notify-users/event-hubs-erp-alert.png

<!---HONumber=AcomDC_1223_2015-->