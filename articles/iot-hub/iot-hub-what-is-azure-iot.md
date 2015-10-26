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
 ms.date="09/29/2015"
 ms.author="dobett"/>

[AZURE.INCLUDE [iot-azure-and-iot](../../includes/iot-azure-and-iot.md)]

## 本文件的範圍

這些 Azure 和 IoT 文章著重於兩個資源集合，可協助您根據 Microsoft IoT 平台，實作自己的 IoT 解決方案。

- Azure IoT 中心
- Azure IoT 裝置 SDK

您也可能會對 [Azure IoT 套件][lnk-iot-suite]有興趣，這是一套預先設定的解決方案，可讓您快速入門和調整 IoT 專案，以因應常見的 IoT 案例，例如遠端監視、資產管理和預測性維護。

### Azure IoT 中心

IoT 中樞是一項 Azure 服務，可從您的裝置大規模接收裝置到雲端的資料，並將該資料路由至資料流事件處理器。IoT 中心也可以使用裝置的特定佇列，將雲端到裝置的命令傳送到特定裝置。

此外，IoT 中樞服務包含裝置識別登錄，您可用來佈建裝置，以及管理哪些裝置可連接到 IoT 中樞。

### Azure IoT 裝置 SDK

Microsoft 提供 IoT 裝置 SDK，可讓您用來實作用戶端應用程式，以便在各式各樣的裝置硬體平台和作業系統上執行。IoT 裝置 SDK 包含程式庫，可協助將裝置到雲端的遙測資料傳送至 IoT 中心，並從 IoT 中心接收雲端到裝置的命令。當您使用 SDK 時，您可讓從數個不同的網路通訊協定中挑選，以和 Azure IoT 中樞通訊。

## 後續步驟

若要開始在 Azure 上使用 IoT，請探索以下資源：

- [開始使用 IoT 中心][lnk-getstarted]
- [Azure IoT 開發人員中心][lnk-iotdev]
- [Azure IoT 中心][lnk-iot-hub]
- [Azure IoT 套件][lnk-iot-suite]  


[lnk-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-iot-hub]: iot-hub-what-is-iot-hub.md
[lnk-iot-suite]: http://azure.microsoft.com/solutions/iot/
[lnk-iotdev]: https://azure.microsoft.com/develop/iot/

<!---HONumber=Oct15_HO3-->