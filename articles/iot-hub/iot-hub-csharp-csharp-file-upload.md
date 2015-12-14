<properties
	pageTitle="使用 IoT 中心從裝置上傳檔案 | Microsoft Azure"
	description="請遵循本教學課程以了解如何從使用 Azure IoT 中心搭配 C# 的裝置上傳檔案。"
	services="iot-hub"
	documentationCenter=".net"
	authors="fsautomata"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="dotnet"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/29/2015"
     ms.author="elioda"/>

# 教學課程：如何使用 IoT 中樞將檔案從裝置上傳到雲端

## 簡介

Azure IoT 中心是一項完全受管理的服務，可讓數百萬個 IoT 裝置和一個應用程式後端進行可靠且安全的雙向通訊。先前的教學課程 ([IoT 中心入門]和[使用 IoT 中心傳送雲端到裝置訊息]) 說明了 IoT 中心的裝置到雲端和雲端到裝置的基本傳訊功能，以及如何從裝置和雲端元件存取它們的方式。[處理裝置到雲端訊息]說明能在 Azure Blob 儲存體中可靠地儲存裝置到雲端訊息的方法。然而，有些情況下來自裝置的資料不會很容易地對應到相對較小的裝置到雲端訊息。某些範例是包含圖片、影片、高頻率震動資料範例，或是包含部分前置處理資料形式的大型檔案。這些檔案通常會使用工具 (例如 [Azure Data Factory] 或 [Hadoop] 堆疊) 以批次方式處理。當偏好從裝置上傳檔案來傳送事件時，仍可能使用 IoT 中心安全性與可靠性功能。

本教學課程以[使用 IoT 中樞傳送雲端到裝置訊息]中的程式碼建置，以示範如何使用雲端到裝置訊息來安全地提供用來上傳檔案之 Azure Blob URI 給裝置，以及如何使用 IoT 中樞傳遞通知來觸發來自您應用程式後端的檔案處理。此方法的優點是重複使用 IoT 中樞裝置身分識別以及雲端到裝置訊息的傳遞通知，來通知應用程式後端檔案已經順利上傳。

> [AZURE.NOTE]這裡使用的相同方法可用來讓裝置安全地由雲端下載檔案。

您可以在 [IoT 中心開發人員指南]中找到有關雲端到裝置訊息和 IoT 中心安全性的詳細資訊。

在本教學課程結尾處，您將會執行兩個 Windows 主控台應用程式：

* **SimulatedDevice**，[使用 IoT 中心傳送雲端到裝置訊息]中建立之 App 的修改版本，可連接到您的 IoT 中心、接收包含 Azure Blob URI 的雲端到裝置訊息。針對每個接收到的雲端到裝置訊息，它會觸發將檔案上傳至指定的 Blob URI。
* **SendCloudToDevice**，這會建置 Azure Blob URI (如[使用 Blob 服務建立與使用 SAS](../storage/storage-dotnet-shared-access-signature-part-2.md) 中所說明)，在雲端到裝置訊息中透過 IoT 中心將 Azure Blob URI 傳送到模擬裝置，然後接收其傳遞通知。

> [AZURE.NOTE]IoT 中心透過 Azure IoT 裝置 SDK 提供許多裝置平台和語言的 SDK 支援 (包括 C、Java 和 Javascript)。如需如何將您的裝置與本教學課程中之程式碼連接 (通常是連接到 Azure IoT 中樞) 的逐步指示，請參閱 [Azure IoT 開發人員中心]。適用於 Java 和 Node 的 Azure IoT 服務 SDK 即將推出。

若要完成本教學課程，您需要下列項目：

+ Microsoft Visual Studio 2015，

+ 使用中的 Azure 帳戶。<br/>如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 [Azure 免費試用](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fzh-TW%2Fdevelop%2Fiot%2Ftutorials%2Ffile-upload%2F target="\_blank")。


[AZURE.INCLUDE [iot-hub-file-upload-cloud-csharp](../../includes/iot-hub-file-upload-cloud-csharp.md)]


[AZURE.INCLUDE [iot-hub-file-upload-device-csharp](../../includes/iot-hub-file-upload-device-csharp.md)]

## 執行應用程式

現在您已經準備好執行應用程式。

1.  在 Visual Studio 中，以滑鼠右鍵按一下您的解決方案並選取 [設定啟始專案...]。選取 [多個啟始專案]，然後同時針對 **SimulatedDevice** 和 **SendCloudToDevice** App 選取 [啟動] 動作。

2.  按下 **F5**，您應該會看到所有的應用程式啟動。選取 **SendCloudToDevice** 視窗並按下索引鍵。當模擬裝置已上傳檔案時，您會看到模擬裝置輸出訊息，且 **SendCloudToDevice** App 會顯示成功的意見反應回條。您可以使用 [Azure 入口網站]或 Visual Studio 伺服器總管來檢查您儲存體帳戶中的檔案是否存在。

  ![][50]


## 後續步驟

在本教學課程中，您已學到如何運用雲端到裝置訊息來簡化從裝置上傳檔案。您可以利用下列教學課程繼續探索 IoT 中心功能和案例：

- [處理裝置到雲端訊息]，示範如何可靠地處理來自裝置的遙測和互動式訊息。

有關 IoT 中心的其他資訊：

* [IoT 中心概觀]
* [IoT 中心開發人員指南]
* [IoT 中心指引]
* [支援的裝置平台和語言][Supported devices]
* [Azure IoT 開發人員中心]

<!-- Images. -->

[50]: ./media/iot-hub-csharp-csharp-file-upload/run-apps1.png

<!-- Links -->

[Send Cloud-to-Device messages with IoT Hub]: iot-hub-csharp-csharp-c2d.md

[Azure 入口網站]: https://portal.azure.com/

[Azure Data Factory]: https://azure.microsoft.com/zh-TW/documentation/services/data-factory/
[Hadoop]: https://azure.microsoft.com/zh-TW/documentation/services/hdinsight/

[Get started with IoT Hub]: iot-hub-csharp-csharp-getstarted.md
[使用 IoT 中心傳送雲端到裝置訊息]: iot-hub-csharp-csharp-c2d.md
[使用 IoT 中樞傳送雲端到裝置訊息]: iot-hub-csharp-csharp-c2d.md
[處理裝置到雲端訊息]: iot-hub-csharp-csharp-process-d2c.md
[Uploading files from devices]: iot-hub-csharp-csharp-file-upload.md

[IoT 中心概觀]: iot-hub-what-is-iot-hub.md
[IoT 中心指引]: iot-hub-guidance.md
[IoT 中心開發人員指南]: iot-hub-devguide.md
[IoT Hub Supported Devices]: iot-hub-supported-devices.md
[IoT 中心入門]: iot-hub-csharp-csharp-getstarted.md
[Supported devices]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/tested_configurations.md
[Azure IoT 開發人員中心]: http://www.azure.com/develop/iot

<!---HONumber=AcomDC_1203_2015-->