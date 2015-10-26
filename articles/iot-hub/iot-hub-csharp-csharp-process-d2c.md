<properties
	pageTitle="處理 IoT 中心裝置到雲端訊息 | Microsoft Azure"
	description="請依照此教學課程來學習處理 IoT 中心裝置到雲端訊息的有用模式。"
	services="iot-hub"
	documentationCenter=".net"
	authors="fsautomata"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="csharp"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/29/2015"
     ms.author="elioda"/>

# 教學課程：如何處理 IoT 中樞裝置到雲端訊息

## 簡介

Azure IoT 中心是一項完全受管理的服務，可讓數百萬個 IoT 裝置和一個應用程式後端進行可靠且安全的雙向通訊。先前的教學課程 ([IoT 中心入門]和[使用 IoT 中心傳送雲端到裝置訊息]) 說明了 IoT 中心的裝置到雲端和雲端到裝置的基本傳訊功能，以及如何從裝置和雲端元件存取它們的方式。

本教學課程以 [IoT 中心入門]中的程式碼建置，以呈現兩種處理裝置到雲端訊息的模式。

第一個模式是 [Azure Blob] 中裝置到雲端訊息的可靠儲存體。當實作*冷路徑*分析時，其中儲存於 Blob 中的資料用來做為工具 (例如 [Azure Data Factory] 或 [Hadoop] 堆疊) 所驅動之分析的輸入時，這種情況很常見。

第二個模式是*互動式*裝置到雲端訊息的可靠處理。當它們因為應用程式後端中的一組動作而立即觸發 (相對於送入分析引擎的*資料點*訊息) 時，裝置到雲端訊息稱為*互動式*。例如，由必須觸發在 CRM 系統中插入票證之裝置所發出的警示是*互動式*裝置到雲端訊息，相對於包含溫度範例，屬於*資料點*訊息的遙測訊息。

因為 IoT 中心會公開事件中樞　相容端點來接收裝置到雲端訊息，本教學課程使用了 [EventProcessorHost] 來裝載事件處理器類別，這會：

* 在 Azure Blob 中可靠地儲存*資料點*，且
* 將*互動式*裝置到雲端訊息轉寄到[服務匯流排佇列]，以進行立即處理。

[服務匯流排][Service Bus Queue]是用來確保能可靠地處理互動式訊息的絕佳方式，因為它提供了各訊息的檢查點，以及以時間範圍為基礎的重複資料刪除。

在本教學課程結尾處，您將會執行三個 Windows 主控台應用程式：

* **SimulatedDevice**，這是 [IoT 中心入門]中建立之 App 的已修改版本，它會每秒傳送*資料點* 裝置到雲端訊息，每 10 秒傳送*互動式*裝置到雲端訊息。
* **ProcessDeviceToCloudMessages**，會使用 [EventProcessorHost] 將*資料點*訊息可靠地儲存在 Azure Blob 中並將*互動式*訊息轉寄到服務匯流排佇列，以及
* **ProcessD2cInteractiveMessages**，可將訊息從佇列清除。

> [AZURE.NOTE]IoT 中心透過 Azure IoT 裝置 SDK 提供許多裝置平台和語言的 SDK 支援 (包括 C、Java 和 Javascript)。如需如何將您的裝置與本教學課程中之程式碼連接 (通常是連接到 Azure IoT 中樞) 的逐步指示，請參閱 [Azure IoT 開發人員中心]。

> [AZURE.NOTE]本教學課程的內容可直接適用於使用事件中樞相容訊息的其他方式，例如像是 Storm 的 [Hadoop] 專案。如需詳細資訊，請參閱 [IoT 中心指引 - 事件中樞相容性]。

若要完成本教學課程，您需要下列項目：

+ Microsoft Visual Studio 2015，

+ 使用中的 Azure 帳戶。<br/>如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 [Azure 免費試用](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fzh-TW%2Fdevelop%2Fiot%2Ftutorials%2Fprocess-d2c%2F target="\_blank")。

它也提供了部分 [Azure 儲存體]和 [Azure 服務匯流排]的資訊。


[AZURE.INCLUDE [iot-hub-process-d2c-device-csharp](../../includes/iot-hub-process-d2c-device-csharp.md)]


[AZURE.INCLUDE [iot-hub-process-d2c-cloud-csharp](../../includes/iot-hub-process-d2c-cloud-csharp.md)]

## 執行應用程式

現在您已經準備好執行應用程式。

1.	在 Visual Studio 中，以滑鼠右鍵按一下您的解決方案並選取 [設定啟始專案...]。選取 [多個啟始專案]，然後針對**ProcessDeviceToCloudMessages**、**SimulatedDevice** 和 **ProcessD2cInteractiveMessages** App 選取 [啟動] 動作。

2.	按下 **F5**，您應該會看到所有的應用程式啟動，且模擬裝置所傳送的每一個互動式訊息都應由互動式訊息處理器進行處理。

  ![][50]

> [AZURE.NOTE]若要查看您正在更新的 Blob 檔案，您可能必須將 `StoreEventProcessor` 中的 `MAX_BLOCK_SIZE` 常數降低 (例如 `1024`)。這是因為達到模擬裝置所傳送之資料的區塊大小限制需要一些時間。編輯之後，您應該能在您的儲存體容器中看見要建立及更新的 Blob。

## 後續步驟

在本教學課程中，您學到如何使用 [EventProcessorHost] 可靠地處理*資料點*與*互動式*裝置到雲端訊息。可以用來實作的類比訊息處理邏輯：

- [從裝置上傳檔案]說明使用雲端到裝置訊息來幫助從裝置上傳檔案的模式。

有關 IoT 中心的其他資訊：

* [IoT 中心概觀]
* [IoT 中心開發人員指南]
* [IoT 中心指引]
* [支援的裝置平台和語言][Supported devices]
* [Azure IoT 開發人員中心]

<!-- Images. -->
[50]: ./media/iot-hub-csharp-csharp-process-d2c/run1.png


<!-- Links -->

[Azure Blob]: https://azure.microsoft.com/zh-TW/documentation/articles/storage-dotnet-how-to-use-blobs/
[Azure Data Factory]: https://azure.microsoft.com/zh-TW/documentation/services/data-factory/
[Hadoop]: https://azure.microsoft.com/zh-TW/documentation/services/hdinsight/
[Service Bus Queue]: https://azure.microsoft.com/zh-TW/documentation/articles/service-bus-dotnet-how-to-use-queues/
[服務匯流排佇列]: https://azure.microsoft.com/zh-TW/documentation/articles/service-bus-dotnet-how-to-use-queues/
[EventProcessorHost]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost(v=azure.95).aspx

[Transient Fault Handling]: https://msdn.microsoft.com/zh-TW/library/hh680901(v=pandp.50).aspx

[IoT 中心指引 - 事件中樞相容性]: iot-hub-guidance.md#eventhubcompatible

[Azure 儲存體]: https://azure.microsoft.com/zh-TW/documentation/services/storage/
[Azure 服務匯流排]: https://azure.microsoft.com/zh-TW/documentation/services/service-bus/

[Azure Preview Portal]: https://portal.azure.com/

[使用 IoT 中心傳送雲端到裝置訊息]: iot-hub-csharp-csharp-c2d.md
[Process Device-to-Cloud messages]: iot-hub-csharp-csharp-process-d2c.md
[從裝置上傳檔案]: iot-hub-csharp-csharp-file-upload.md

[IoT 中心概觀]: iot-hub-what-is-iot-hub.md
[IoT 中心指引]: iot-hub-guidance.md
[IoT 中心開發人員指南]: iot-hub-devguide.md
[IoT Hub Supported Devices]: iot-hub-supported-devices.md
[IoT 中心入門]: iot-hub-csharp-csharp-getstarted.md
[Supported devices]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/tested_configurations.md
[Azure IoT 開發人員中心]: http://www.azure.com/develop/iot

<!---HONumber=Oct15_HO3-->