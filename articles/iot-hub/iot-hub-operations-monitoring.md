<properties
 pageTitle="IoT 中樞作業監視"
 description="Azure IoT 中樞的作業監視概觀，可讓使用者即時監視其 IoT 中樞上的作業狀態。"
 services="iot-hub"
 documentationCenter=""
 authors="nberdy"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="02/03/2016"
 ms.author="nberdy"/>

# 作業監視簡介

IoT 中樞的作業監視可讓使用者即時監視其 IoT 中樞上的作業狀態。IoT 中樞會追蹤數個作業類別的事件，而且使用者可以選擇讓一個或多個類別的事件傳送至其 IoT 中樞的端點進行處理。使用者可以監視資料中是否有錯誤，或根據資料模式設定更複雜的處理行為。

IoT 中樞會監視四個類別的事件：

- 裝置身分識別作業
- 裝置遙測
- 雲端到裝置的命令
- 連線

## 如何啟用作業監視

1. 建立 IoT 中樞。您可以在[開始使用][lnk-get-started]指南中找到如何建立 IoT 中樞的指示。

2. 開啟 IoT 中樞的刀鋒視窗。在該處按一下 [所有設定]，然後按一下 [作業監視]。

    ![][1]

3. 選取您要監視的監視類別，然後按一下 [儲存]。您可以從 [監視設定] 中所列出的事件中樞相容端點讀取事件。

    ![][2]

## 事件類別和其使用方式

每個作業監視類別會各自追蹤與 IoT 中樞所進行的不同類型的互動，而每一個監視類別都有結構描述來定義該類別的事件要如何構成。

### 裝置身分識別作業

裝置身分識別作業類別會追蹤使用者嘗試在其 IoT 中樞的身分識別登錄中建立、更新或刪除項目時所發生的錯誤。佈建案例就很適合追蹤此類別。

    {
        "time": "UTC timestamp",
         "operationName": "create",
         "category": "DeviceIdentityOperations",
         "level": "Error",
         "statusCode": 4XX,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID",
         "durationMs": 1234,
         "userAgent": “userAgent”,
         "sharedAccessPolicy": "accessPolicy"
    }

### 裝置遙測

裝置遙測類別會追蹤在 IoT 中樞發生，且與遙測管線相關的錯誤。這包括在傳送遙測事件 (例如節流) 和接收遙測事件 (例如未經授權的讀取器) 時所發生的錯誤。請注意，這個類別無法捕捉裝置本身所執行之程式碼所造成的錯誤。

    {
         "messageSizeInBytes": 1234,
         "batching": 0,
         "protocol": "Amqp",
         "authType": "{"scope":"device","type":"sas","issuer":"iothub"}",
         "time": "UTC timestamp",
         "operationName": "ingress",
         "category": "DeviceTelemetry",
         "level": "Error",
         "statusCode": 4XX,
         "statusType": 4XX001,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID",
         "EventProcessedUtcTime": "UTC timestamp",
         "PartitionId": 1,
         "EventEnqueuedUtcTime": "UTC timestamp"
    }

### 雲端到裝置的命令

雲端到裝置的命令類別會追蹤在 IoT 中樞發生，且與裝置命令管線相關的錯誤。這包括在傳送命令 (例如未經授權的寄件者)、接收命令 (例如超過傳遞計數) 和接收命令回饋 (例如回饋已過期) 時所發生的錯誤。此類別不會捕捉未正確處理命令但卻將其成功傳遞之裝置所發生的錯誤。

    {
         "messageSizeInBytes": 1234,
         "authType": "{"scope":"hub","type":"sas","issuer":"iothub"}",
         "deliveryAcknowledgement": 0,
         "protocol": "Amqp",
         "time": " UTC timestamp",
         "operationName": "ingress",
         "category": "C2DCommands",
         "level": "Error",
         "statusCode": 4XX,
         "statusType": 4XX001,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID",
         "EventProcessedUtcTime": "UTC timestamp",
         "PartitionId": 1,
         "EventEnqueuedUtcTime": “UTC timestamp"
    }

### 連線

連線類別會追蹤與 IoT 中樞連線或中斷連線之裝置所造成的事件。若想識別未經授權的連線嘗試，以及在連線品質不佳之區域內的裝置中斷連線時進行追蹤，就很適合追蹤此類別。

    {
         "durationMs": 1234,
         "authType": "{"scope":"hub","type":"sas","issuer":"iothub"}",
         "protocol": "Amqp",
         "time": " UTC timestamp",
         "operationName": "deviceConnect",
         "category": "Connections",
         "level": "Error",
         "statusCode": 4XX,
         "statusType": 4XX001,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID"
    }

## 後續步驟

您現已了解作業監視的概觀，接下來請遵循下列連結來深入了解：

- [IoT 中樞的診斷度量][lnk-diagnostic-metrics]
- [調整 IoT 中樞][lnk-scaling]
- [IoT 中樞高可用性和災害復原][lnk-dr]

<!-- Links and images -->
[1]: media/iot-hub-operations-monitoring/enable-OM-1.png
[2]: media/iot-hub-operations-monitoring/enable-OM-2.png

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-diagnostic-metrics]: iot-hub-metrics.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-dr]: iot-hub-ha-dr.md

<!---HONumber=AcomDC_0204_2016-->