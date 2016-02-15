<properties
   pageTitle="Azure IoT 通訊協定閘道 | Microsoft Azure"
   description="描述如何使用 Azure IoT 通訊協定閘道來擴充 Azure IoT 中樞的功能和通訊協定支援。"
   services="iot-hub"
   documentationCenter=""
   authors="kdotchkoff"
   manager="timlt"
   editor=""/>

<tags
   ms.service="iot-hub"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/03/2016"
   ms.author="kdotchko"/>

# 支援 IoT 中心的其他通訊協定

Azure IoT 中樞會透過 AMQP、MQTT 和 HTTP/1 通訊協定，以原生方式支援通訊。在某些情況下，裝置或現場閘道可能無法使用這些標準通訊協定的其中一個，且需要通訊協定調適。在這種情況下，您可以使用自訂閘道。自訂閘道可以橋接在 IoT 中樞進出的流量，藉此為 IoT 中樞端點啟用通訊協定調適。您可以使用 [Azure IoT 通訊協定閘道](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md)做為自訂閘道，來為 IoT 中樞啟用通訊協定調適。

## Azure IoT 通訊協定閘道

Azure IoT 通訊協定閘道是通訊協定調適的架構，專門用來與 IoT 中樞的高延展性雙向裝置通訊。通訊協定閘道是一種傳遞元件，會透過特定通訊協定接受裝置的連接。透過 AMQP 1.0 橋接 IoT 中樞的流量。IoT 通訊協定閘道是以開放原始碼軟體 (OSS) 專案的方式提供使用，可在為各種通訊協定和通訊協定版本增加支援時提供支援彈性。

您可以使用 Azure 雲端服務背景工作角色，以具高延展性的方式，在 Azure 中部署通訊協定閘道。此外，通訊協定閘道可以部署在內部部署環境中，例如現場閘道。

Azure IoT 通訊協定閘道包含可讓您視需要自訂 MQTT 通訊協定行為的 MQTT 通訊協定配接器。由於 IoT 中樞內建支援 MQTT v3.1.1 通訊協定，因此請在需要自訂通訊協定或特定需要其他功能時，再考慮使用 MQTT 通訊協定配接器。

MQTT 配接器也會示範程式設計模型，此模型可為其他通訊協定建置通訊協定配接器。此外，IoT 通訊協定閘道的程式設計模型，可讓您對特製化處理插入自訂元件，例如自訂驗證、訊息轉換、壓縮/解壓縮，或加密/解密裝置與 IoT 中樞之間的流量。

為了讓您有彈性空間，通訊協定閘道和 MQTT 實作是以開放原始碼軟體專案的方式提供。這可讓您視需要自訂實作。

## 後續步驟

若要深入了解 Azure IoT 通訊協定閘道，以及如何使用並將其部署為 IoT 方案的一部分，請參閱：

* [GitHub 上的 Azure IoT 通訊協定閘道儲存機制 (英文)](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md)
* [Azure IoT 通訊協定閘道開發人員指南 (英文)](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/docs/DeveloperGuide.md)

<!---HONumber=AcomDC_0204_2016-->