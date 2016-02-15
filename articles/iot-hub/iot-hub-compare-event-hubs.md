<properties
 pageTitle="比較 Azure IoT 中樞與 Azure 事件中樞 |Microsoft Azure"
 description="透過反白顯示功能差異和使用案例來比較 Azure IoT 中樞和 Azure 事件中樞服務。"
 services="iot-hub"
 documentationCenter=""
 authors="fsautomata"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="01/20/2016"
 ms.author="elioda"/>

# IoT 中樞和事件中樞的比較

Azure IoT 中心的主要使用案例之一是從裝置收集遙測。因此，經常會比較 IoT 中樞與 [Azure 事件中樞][]。類似於 IoT 中樞，事件中樞是事件處理服務，它能提供大規模進入雲端的事件和遙測入口，並具備低延遲和高可靠性等特性。

不過，這些服務有許多差異，將在下表中詳述。

| 領域 | IoT 中心 | 事件中樞 |
| ---- | ------- | ---------- |
| 通訊模式 | 啟用裝置到雲端及雲端到裝置的傳訊功能。 | 僅提供事件輸入 (通常視為裝置到雲端案例)。 |
| 裝置通訊協定支援 | 支援 AMQP、透過 WebSockets 的 AMQP、MQTT 和 HTTP/1。此外，IoT 中樞還可搭配 [Azure IoT 通訊協定閘道][lnk-azure-protocol-gateway]來使用，後者是支援自訂通訊協定的可自訂通訊協定閘道實作。 | 支援 AMQP、透過 WebSockets 的 AMQP 和 HTTP/1。 |
| 安全性 | 提供每個裝置的身分識別與可撤銷的存取控制。請參閱 [IoT 中樞開發人員指南的安全性章節]。 | 提供全事件中樞的[共用存取原則][Event Hub - security]，以及透過[發行者原則][Event Hub publisher policies]的有限撤銷支援。IoT 解決方案通常需要實作自訂解決方案來支援每個裝置的認證以及防詐騙措施。 |
| 作業監視 | 可讓 IoT 解決方案訂閱一組豐富的裝置身分識別管理和連線事件，例如個別裝置驗證錯誤、節流和格式錯誤的例外狀況。這些事件可讓您快速識別個別裝置層級的連線問題。 | 僅公開彙總的度量。 |
| 調整 | 已最佳化以支援數百萬同時連接的裝置。 | 可支援更多同時連接的限制數目：每個 [Azure 服務匯流排配額][]高達 5,000 個 AMQP 連接。另一方面，事件中樞可讓您指定每個傳送訊息的分割。 |
| 裝置 SDK | 提供[裝置 SDK][Azure IoT Hub SDKs] 給各種平台和語言。 | 在 .NET 和 C 上都支援，且事件中樞提供 AMQP 和 HTTP 傳送介面。 |

總而言之，即使唯一的使用案例是裝置到雲端遙測輸入，IoT 中樞仍會提供專為 IoT 裝置連線所設計的服務。它會持續擴充具備 IoT 功能之案例的價值主張。事件中樞的設計對象是資料中心之間及資料中心內部案例的大規模事件輸入。

在相同解決方案中同時使用 IoT 中樞和事件中樞的情況並不常見，因為 IoT 中樞是處理裝置到雲端通訊，而事件中樞處理後期事件輸入至即時處理引擎。

## 後續步驟

遵循下列連結以深入了解 Azure IoT 中樞：

- [開始使用 Azure IoT 中樞 (教學課程)][lnk-get-started]
- [何謂 Azure IoT 中心？][]

[Azure 事件中樞]: ../event-hubs/event-hubs-what-is-event-hubs.md
[IoT 中樞開發人員指南的安全性章節]: iot-hub-devguide.md#security
[Event Hub - security]: ../event-hubs/event-hubs-authentication-and-security-model-overview.md
[Event Hub publisher policies]: ../event-hubs/event-hubs-overview.md#common-publisher-tasks
[Azure 服務匯流排配額]: ../service-bus/service-bus-quotas.md
[Azure IoT Hub SDKs]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[何謂 Azure IoT 中心？]: iot-hub-what-is-iot-hub.md
[lnk-azure-protocol-gateway]: iot-hub-protocol-gateway.md

<!---HONumber=AcomDC_0204_2016-->