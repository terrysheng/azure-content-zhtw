<properties
 pageTitle="IoT 中樞的 MQTT 支援 | Microsoft Azure"
 description="IoT 中樞層級的 MQTT 支援說明"
 services="iot-hub"
 documentationCenter=".net"
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="02/03/2016"
 ms.author="dobett"/>

# IoT 中樞的 MQTT 支援

IoT 中樞可讓裝置在連接埠 8883 使用 [MQTT v3.1.1][lnk-mqtt-org] 通訊協定來與 IoT 中樞裝置端點進行通訊。IoT 中樞要求使用 TLS/SSL 保護所有裝置通訊的安全。

## 連接到 IoT 中樞

裝置可以使用 MQTT 通訊協定連接到 IoT 中樞，方法是使用 [Microsoft Azure IoT SDK][lnk-device-sdks] 中的程式庫或直接使用 MQTT 通訊協定。

## 使用裝置用戶端 SDK

支援 MQTT 通訊協定的[裝置用戶端 SDK][lnk-device-sdks] 有提供 Java、Node.js、C 和 C# 等版本。裝置用戶端 SDK 會使用標準的 IoT 中樞連接字串來連接到 IoT 中樞。若要使用 MQTT 通訊協定，用戶端通訊協定參數必須設定為 **MQTT**。根據預設，裝置用戶端 SDK 會連接到 **CleanSession** 旗標設為 **0** 的 IoT 中樞，並使用 **QoS 1** 來與 IoT 中樞交換訊息。

當裝置連接到 IoT 中樞時，裝置用戶端 SDK 會提供方法讓裝置在 IoT 中樞傳送和接收訊息。

下表包含每一種支援語言的程式碼範例連結，並指出要用來以 MQTT 通訊協定連接到 IoT 中樞的參數。

| 語言 | 通訊協定參數 |
| -------------------------- | ------------------------- |
| [Node.js][lnk-sample-node] | azure-iot-device-mqtt |
| [Java][lnk-sample-java] | IotHubClientProtocol.MQTT |
| [C][lnk-sample-c] | MQTT\_Protocol |
| [C#][lnk-sample-csharp] | TransportType.Mqtt |

## 直接使用 MQTT 通訊協定

如果裝置無法使用裝置用戶端 SDK，它仍可使用 MQTT 通訊協定連接到公用裝置端點。在 **CONNECT** 封包中，裝置應使用下列值：

- 做為 **ClientId** 的 **deviceId**
- [使用者名稱] 欄位中的 `{iothubhostname}/{device_id}`，其中 {iothubhostname} 是 IoT 中樞的完整 CName (例如，contoso.azure-devices.net)。
- [密碼] 欄位中的 SAS 權杖。[SAS 權杖的格式][lnk-iothub-security]與針對 HTTP 和 AMQP 通訊協定所述的格式相同：<br/>`SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`。

對於 MQTT 的連接和中斷連接封包，IoT 中樞會對**作業監視**通道發出事件。

### 將訊息傳送至 IoT 中樞

成功連接之後，裝置可以使用 `devices/{did}/messages/events/` 或 `devices/{did}/messages/events/{property_bag}` 做為**主題名稱**來將訊息傳送至 IoT 中樞。`{property_bag}` 項目可讓裝置以 url 編碼格式傳送具有其他屬性的訊息。例如：

```
RFC 2396-encoded(<PropertyName1>)=RFC 2396-encoded(<PropertyValue1>)&RFC 2396-encoded(<PropertyName2>)=RFC 2396-encoded(<PropertyValue2>)…
```
 
> [AZURE.NOTE] 這是與用於 HTTP 通訊協定中的查詢字串相同的編碼。

裝置用戶端應用程式也可以使用 `devices/{did}/messages/events/{property_bag}` 做為 **Will 主題名稱**，來定義要以遙測訊息形式轉送的 *Will 訊息*。

### 接收訊息

若要從 IoT 中樞接收訊息，裝置應該使用 `devices/{did}/messages/devicebound/#”` 做為**主題篩選**來進行訂閱。如果有任何訊息屬性，IoT 中樞會傳遞具有**主題名稱** `devices/{did}/messages/devicebound/` 或 `devices/{did}/messages/devicebound/{property_bag}` 的訊息。`{property_bag}` 包含以 url 編碼的訊息屬性索引鍵/值組。屬性包中只會包含應用程式屬性和使用者可設定的系統屬性 (例如 **messageId** 或 **correlationId**)。系統屬性名稱具有前置詞 **$**，但應用程式屬性則會使用沒有前置詞的原始屬性名稱。

## 後續步驟

若要深入了解如何使用裝置用戶端 SDK 來與 IoT 中樞通訊，請參閱[開始使用 Azure IoT 中樞][lnk-iot-get-stated]。

若要深入了解 MQTT 通訊協定，請參閱 [MQTT 文件][lnk-mqtt-docs]。

[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-mqtt-org]: http://mqtt.org/
[lnk-iot-get-stated]: iot-hub-csharp-csharp-getstarted.md
[lnk-mqtt-docs]: http://mqtt.org/documentation
[lnk-iothub-security]: iot-hub-devguide.md#security
[lnk-sample-node]: https://github.com/Azure/azure-iot-sdks/blob/develop/node/device/samples/simple_sample_device.js
[lnk-sample-java]: https://github.com/Azure/azure-iot-sdks/blob/develop/java/device/samples/send-receive-sample/src/main/java/samples/com/microsoft/azure/iothub/SendReceive.java
[lnk-sample-c]: https://github.com/Azure/azure-iot-sdks/tree/master/c/iothub_client/samples/iothub_client_sample_mqtt
[lnk-sample-csharp]: https://github.com/Azure/azure-iot-sdks/tree/master/csharp/device/samples

<!---HONumber=AcomDC_0218_2016-->