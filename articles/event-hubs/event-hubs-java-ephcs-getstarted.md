<properties
	pageTitle="開始使用以 Java 撰寫的事件中樞 | Microsoft Azure"
	description="遵循此教學課程，以開始使用 Azure 事件中樞；透過 Java 傳送事件並使用 EventProcessorHost 以 C# 接收事件。"
	services="event-hubs"
	documentationCenter=""
	authors="fsautomata"
	manager="timlt"
	editor=""/>

<tags
	ms.service="event-hubs"
	ms.workload="core"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/08/2016"
	ms.author="sethm"/>

# 開始使用事件中心

[AZURE.INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## 簡介

事件中心是可高度擴充的擷取系統，每秒可以吸收數以百萬計的事件，讓應用程式能處理和分析已連線裝置和應用程式所產生的大量資料。收集到事件中樞後，您可以使用任何即時分析提供者或儲存體叢集轉換和儲存資料。

如需詳細資訊，請參閱[事件中樞概觀][]。

在本教學課程中，您將學習如何使用以 Java 撰寫的主控台應用程式將訊息擷取到事件中樞，以及使用 C# [事件處理器主機][]程式庫平行擷取它們。

若要完成本教學課程，您需要下列項目：

+ Java 開發環境。在本教學課程中，我們將採用 [Eclipse](https://www.eclipse.org/)。

+ Microsoft Visual Studio Express for Windows

+ 使用中的 Azure 帳戶。<br/>如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費帳戶。如需詳細資訊，請參閱 <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fzh-TW%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Azure 免費試用</a>。

## 建立事件中心

1. 登入 [Azure 傳統入口網站][]，並按一下畫面底部的 [新增]。

2. 按一下 [**應用程式服務**]、[**服務匯流排**]、[**事件中樞**]、[**快速建立**]。

	![][1]

3. 為您的事件中樞輸入名稱、選取所需的區域，然後按一下 [**建立新的事件中樞**]。

	![][2]

4. 按一下您剛才建立的命名空間 (通常是 ***event hub name*-ns**)。

	![][3]

5. 按一下頁面頂端的 [事件中心] 索引標籤，然後按一下您剛建立的事件中心。

	![][4]

6. 按一下頁面頂端的 [**設定**] 索引標籤，新增名為 **SendRule** 且具有*傳送*權限的規則，以及另一個名為 **ReceiveRule** 且具有*管理、傳送、接聽*權限的規則，然後按一下 [**儲存**]。

	![][5]

7. 在相同頁面上，記下為 **SendRule** 產生的金鑰。

	![][7]

8. 按一下頁面頂端的 [**儀表板**] 索引標籤，然後按一下 [**連接資訊**]。記下這兩個連接字串。

	![][6]

現已建立事件中心，並具有傳送與接收事件所需的連接字串。

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-java](../../includes/service-bus-event-hubs-get-started-send-java.md)]


[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-ephcs](../../includes/service-bus-event-hubs-get-started-receive-ephcs.md)]

## 執行應用程式

現在您已經準備好執行應用程式。

1.	從 Visual Studio 執行 **Receiver** 專案，然後等它啟動所有分割區的接收器。

	![][21]

2.	執行 **Sender** 專案，按下主控台視窗中的 **Enter** 鍵，並查看出現在接收器視窗中的事件。

	![][22]

## 後續步驟

您已經建置工作應用程式，可建立「事件中樞」和傳送及接收資料，接下來可進行下列案例：

- [使用事件中樞的完整範例應用程式][]。
- [使用「事件中樞」相應放大事件處理][]範例。
- 使用服務匯流排佇列的[佇列訊息解決方案][]。

如需詳細資訊，請參閱 [Java 開發人員中心](/develop/java/)。

<!-- Images. -->
[1]: ./media/event-hubs-java-ephcs-getstarted/create-event-hub1.png
[2]: ./media/event-hubs-java-ephcs-getstarted/create-event-hub2.png
[3]: ./media/event-hubs-java-ephcs-getstarted/create-event-hub3.png
[4]: ./media/event-hubs-java-ephcs-getstarted/create-event-hub4.png
[5]: ./media/event-hubs-java-ephcs-getstarted/create-event-hub5.png
[6]: ./media/event-hubs-java-ephcs-getstarted/create-event-hub6.png
[7]: ./media/event-hubs-java-ephcs-getstarted/create-event-hub6b.png


[21]: ./media/event-hubs-java-ephcs-getstarted/run-csharp-ephcs1.png
[22]: ./media/event-hubs-java-ephcs-getstarted/run-csharp-ephcs2.png

<!-- Links -->
[Azure 傳統入口網站]: https://manage.windowsazure.com/
[事件處理器主機]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[事件中樞概觀]: event-hubs-overview.md
[使用事件中樞的完整範例應用程式]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[使用「事件中樞」相應放大事件處理]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
[佇列訊息解決方案]: ../service-bus/service-bus-dotnet-multi-tier-app-using-service-bus-queues.md
 

<!---HONumber=AcomDC_0323_2016-->