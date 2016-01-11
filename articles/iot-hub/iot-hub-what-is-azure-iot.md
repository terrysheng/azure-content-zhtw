<properties
 pageTitle="適用於物聯網的 Azure 解決方案 | Microsoft Azure"
 description="Azure 上的 IoT 概觀，包括架構解決方案範例，以及它如何與 Azure IoT 中樞、裝置 SDK 以及預先設定的解決方案相關聯"
 services="iot-hub"
 documentationCenter=""
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="11/05/2015"
 ms.author="dobett"/>

[AZURE.INCLUDE [iot-azure-and-iot](../../includes/iot-azure-and-iot.md)]

## 後續步驟

Azure IoT 中樞是一項 Azure 服務，可從您的裝置大規模接收遙測資料，並將該資料路由至串流事件處理器。您可以使用 IoT 中樞來實作自己的方案後端。IoT 中樞也可以將雲端到裝置的命令傳送到特定裝置。此外，IoT 中樞包含裝置識別登錄，您可用來佈建裝置，以及管理哪些裝置可連接到中樞。若要深入了解，請參閱：

- [何謂 IoT 中樞？][lnk-iot-hub]
- [開始使用 IoT 中樞][lnk-getstarted]

您可以使用 IoT 裝置 SDK 來實作用戶端應用程式，以便在各式各樣的裝置硬體平台和作業系統上執行。IoT 裝置 SDK 包含程式庫，可協助將遙測傳送至 IoT 中樞，並接收雲端到裝置的命令。當您使用 SDK 時，您可從數個網路通訊協定中挑選，以和 IoT 中樞通訊。若要深入了解，請參閱[裝置 SDK 的相關資訊][lnk-device-sdks]。

您也可能會對 [Azure IoT 套件][lnk-iot-suite]有興趣，這是一套預先設定的方案。IoT 套件可讓您快速入門和調整 IoT 專案，以因應常見的 IoT 案例，例如遠端監視、資產管理和預測性維護。

[lnk-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-iot-hub]: iot-hub-what-is-iot-hub.md
[lnk-iot-suite]: http://azure.microsoft.com/solutions/iot/
[lnk-iotdev]: https://azure.microsoft.com/develop/iot/

<!---HONumber=AcomDC_1223_2015-->