<properties pageTitle="開始使用事件中心" metaKeywords="Azure 服務匯流排, 事件中心, 開始使用事件中心" description="Follow this tutorial to get started using Azure Event Hubs sending events with Java and receiving in C# using EventProcessorHost" metaCanonical="" services="" documentationCenter="" title="Get Started with Event Hubs" authors="elioda" solutions="" manager="timlt" editor="" />

<tags ms.service="service-bus" ms.workload="core" ms.tgt_pltfrm="csharp" ms.devlang="csharp" ms.topic="hero-article" ms.date="10/27/2014" ms.author="elioda" />

# <a name="getting-started"> </a>開始使用事件中心

[WACOM.INCLUDE [service-bus-selector-get-started](../includes/service-bus-selector-get-started.md)]

事件中心是可高度擴充的擷取系統，每秒可以吸收數以百萬計的事件，讓應用程式能處理和分析已連線裝置和應用程式所產生的大量資料。一旦收集到事件中心，您即可使用任何即時分析提供者或儲存體叢集來轉換和儲存資料。

	如需詳細資訊，請參閱[事件中心概觀].

在本教學課程中，您將學習如何使用以 Java 撰寫的主控台應用程式將訊息擷取到事件中心，以及使用 C# [事件處理器主機]程式庫平行擷取它們。

若要完成本教學課程，您需要下列項目：

+ Java 開發環境。在本教學課程中，我們將假設 [Eclipse](https://www.eclipse.org/)。

+ Microsoft Visual Studio Express 2013 for Windows

+ 使用中的 Azure 帳戶。<br/>如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 <a href="http://www.windowsazure.com/zh-tw/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fzh-tw%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Azure 免費試用</a>。

## 建立事件中心

1. 登入 [Azure 管理入口網站]，並按一下畫面底部的 [**新增**]。

2. 按一下 [**應用程式服務**]、[**服務匯流排**]、[**事件中心**]、[**快速建立**]。

   	![][1]

3. 為您的事件中心輸入名稱、選取所需的區域，然後按一下 [**Create a new Event Hub**]。

   	![][2]

4. 按一下您剛才建立的命名空間 (通常是 ***event hub name*-ns**)。

   	![][3]

5. 按一下頁面頂端的 [**事件中心**] 索引標籤，然後按一下您剛才建立的事件中心。

   	![][4]

6. 按一下頁面頂端的 [**設定**] 索引標籤，新增名為 **SendRule** 且具有*傳送* 權限的規則，以及另一個名為 **ReceiveRule** 且具有*管理、傳送、接聽* 權限的規則，然後按一下 [**儲存**]。

   	![][5]

7. 在相同頁面上，記下為 **SendRule** 產生的金鑰。

   	![][6b]

8. 按一下頁面頂端的 [**儀表板**] 索引標籤，然後按一下 [**連接資訊**]。記下這兩個連接字串。

   	![][6]

現已建立事件中心，並具有傳送與接收事件所需的連接字串。

[WACOM.INCLUDE [service-bus-event-hubs-get-started-send-java](../includes/service-bus-event-hubs-get-started-send-java.md)]


[WACOM.INCLUDE [service-bus-event-hubs-get-started-receive-ephcs](../includes/service-bus-event-hubs-get-started-receive-ephcs.md)]

## 執行應用程式

現在您已經準備好執行應用程式。

1.	從 Visual Studio 執行 **Receiver** 專案，然後等它啟動所有資料分割的接收器。

   	![][21]

2.	執行 **Sender** 專案，按主控台視窗中的 **Enter** 鍵，並查看出現在接收器視窗中的事件。

   	![][22]

<!-- Images. -->
[1]: ./media/service-bus-event-hubs-getstarted/create-event-hub1.png
[2]: ./media/service-bus-event-hubs-getstarted/create-event-hub2.png
[3]: ./media/service-bus-event-hubs-getstarted/create-event-hub3.png
[4]: ./media/service-bus-event-hubs-getstarted/create-event-hub4.png
[5]: ./media/service-bus-event-hubs-getstarted/create-event-hub5.png
[6]: ./media/service-bus-event-hubs-getstarted/create-event-hub6.png
[6b]: ./media/service-bus-event-hubs-getstarted/create-event-hub6b.png


[21]: ./media/service-bus-event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs1.png
[22]: ./media/service-bus-event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs2.png

<!-- Links -->
[Azure 管理入口網站]: https://manage.windowsazure.com/
[事件處理器主機]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[事件中心概觀]: http://msdn.microsoft.com/zh-tw/library/azure/dn836025.aspx
