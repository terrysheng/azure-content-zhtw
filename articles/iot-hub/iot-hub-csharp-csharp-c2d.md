<properties
	pageTitle="透過 IoT 中心傳送雲端到裝置的訊息 | Microsoft Azure"
	description="請遵循本教學課程，以了解如何搭配 C# 使用 Azure IoT 中心傳送雲端到裝置的訊息。"
	services="iot-hub"
	documentationCenter=".net"
	authors="fsautomata"
	manager="kevinmil"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="csharp"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="tbd"
     ms.date="09/29/2015"
     ms.author="elioda"/>

# 透過 IoT 中心傳送雲端到裝置的訊息

## 簡介

Azure IoT 中心是一項完全受管理的服務，可讓數百萬個 IoT 裝置和一個應用程式後端進行可靠且安全的雙向通訊。[IoT 中心入門]教學課程會示範如何建立 IoT 中心、在其中佈建裝置識別，以及編寫模擬的裝置，以傳送裝置到雲端的訊息。

本教學課程是以 [IoT 中心入門]為基礎，並示範如何將雲端到裝置的訊息傳送至單一裝置、如何要求 IoT 中心的傳遞通知 (*feedback*)，以及從應用程式雲端的後端接收它。

您可以在 [IoT 中心開發人員指南][IoT Hub Developer Guide - C2D]中，找到有關雲端到裝置訊息的詳細資訊。

在本教學課程結尾處，您將會執行兩個 Windows 主控台應用程式：

* **SimulatedDevice**，[IoT 中心入門]中建立之應用程式的修改版本，可連接到您的 IoT 中心，並接收雲端到裝置的訊息。 
* **SendCloudToDevice**，將雲端到裝置的訊息透過 IoT 中心傳送到模擬的裝置，然後收到其傳遞通知。

> [AZURE.NOTE]IoT 中心透過 Azure IoT 裝置 SDK 提供許多裝置平台和語言的 SDK 支援 (包括 C、Java 和 Javascript)。如需如何將您的裝置與本教學課程中之程式碼連接 (通常是連接到 Azure IoT 中心) 的逐步指示，請參閱 [Azure IoT 開發人員中心]。適用於 Java 和 Node 的 Azure IoT 服務 SDK 即將推出。

若要完成本教學課程，您需要下列項目：

+ Microsoft Visual Studio 2015，

+ 使用中的 Azure 帳戶。<br/>如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 [Azure 免費試用](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fzh-TW%2Fdevelop%2Fiot%2Ftutorials%2Fc2d%2F target="\_blank")。

[AZURE.INCLUDE [iot-hub-c2d-device-csharp](../../includes/iot-hub-c2d-device-csharp.md)]


[AZURE.INCLUDE [iot-hub-c2d-cloud-csharp](../../includes/iot-hub-c2d-cloud-csharp.md)]

## 後續步驟

在本教學課程中，您已了解如何傳送和接收雲端到裝置的訊息。您可以利用下列教學課程，繼續探索 IoT 中心功能和案例：

- [處理裝置到雲端訊息]，示範如何可靠地處理來自裝置的遙測和互動式訊息。
- [從裝置上傳檔案]，說明使用雲端到裝置訊息來幫助從裝置上傳檔案的模式。

有關 IoT 中心的其他資訊：

* [IoT 中心概觀]
* [IoT 中心開發人員指南]
* [IoT 中心指引]
* [支援的裝置平台和語言][Supported devices]
* [Azure IoT 開發人員中心]

<!-- Images. -->

<!-- Links -->

[Get started with IoT Hub]: iot-hub-csharp-csharp-getstarted.md

[IoT Hub Developer Guide - C2D]: iot-hub-devguide.md#c2d

[Azure Preview Portal]: https://portal.azure.com/

[Send Cloud-to-Device messages with IoT Hub]: iot-hub-csharp-csharp-c2d.md
[處理裝置到雲端訊息]: iot-hub-csharp-csharp-process-d2c.md
[從裝置上傳檔案]: iot-hub-csharp-csharp-file-upload.md

[IoT 中心概觀]: iot-hub-what-is-iot-hub.md
[IoT 中心指引]: iot-hub-guidance.md
[IoT 中心開發人員指南]: iot-hub-devguide.md
[IoT Hub Supported Devices]: iot-hub-supported-devices.md
[IoT 中心入門]: iot-hub-csharp-csharp-getstarted.md
[Supported devices]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/tested_configurations.md
[Azure IoT 開發人員中心]: http://www.azure.com/iotdev



 

<!---HONumber=Oct15_HO1-->