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
 ms.date="02/03/2016"
 ms.author="dobett"/>

[AZURE.INCLUDE [iot-azure-and-iot](../../includes/iot-azure-and-iot.md)]

## 後續步驟

Azure IoT 中樞是一項 Azure 服務，可讓應用程式後端與數百萬個裝置進行安全可靠的雙向通訊。它可讓應用程式後端接收裝置的大規模遙測資料，將該資料路由傳送至串流事件處理器，而且還能對特定裝置傳送雲端到裝置的命令。您可以使用 IoT 中樞來實作自己的方案後端。此外，IoT 中樞還包含裝置身分識別登錄，可供用來佈建裝置、其安全性認證和其連線到中樞的權限。若要深入了解，請參閱：

- [何謂 IoT 中樞？][lnk-iot-hub]
- [開始使用 IoT 中樞][lnk-getstarted]

您可以使用 IoT 裝置 SDK 來實作用戶端應用程式，以便在各式各樣的裝置硬體平台和作業系統上執行。IoT 裝置 SDK 包含程式庫，可協助將遙測傳送至 IoT 中樞，並接收雲端到裝置的命令。當您使用 SDK 時，您可從數個網路通訊協定中挑選，以和 IoT 中樞通訊。若要深入了解，請參閱[裝置 SDK 的相關資訊][lnk-device-sdks]。

您也可能會對 [Azure IoT 套件][lnk-iot-suite]有興趣，這是一套預先設定的方案。IoT 套件可讓您快速入門和調整 IoT 專案，以因應常見的 IoT 案例，例如遠端監視、資產管理和預測性維護。

[lnk-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-iot-hub]: iot-hub-what-is-iot-hub.md
[lnk-iot-suite]: http://azure.microsoft.com/solutions/iot/
[lnk-iotdev]: https://azure.microsoft.com/develop/iot/

<!---HONumber=AcomDC_0204_2016-->