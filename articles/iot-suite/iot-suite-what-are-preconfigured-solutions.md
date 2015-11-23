<properties
 pageTitle="Azure IoT 預先設定解決方案 | Microsoft Azure"
 description="說明 Azure IoT 預先設定解決方案及其架構，且包含其他資源的連結。"
 services=""
 documentationCenter=""
 authors="aguilaaj"
 manager="timlt"
 editor=""/>

<tags
 ms.service="na"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/29/2015"
 ms.author="araguila"/>

# 什麼是 Azure IoT 預先設定解決方案？

您可以使用 Azure 訂用帳戶將實作常見物聯網 (IoT) 案例的預先設定解決方案部署至 Microsoft Azure。您可以使用預先設定解決方案：

- 做為您的 IoT 解決方案的起點。
- 深入了解 IoT 方案設計與開發最常見的模式。

每個預先設定解決方案都會實作一個常見 IoT 案例，且為完整的端對端實作。

除了部署並執行 Azure 中的預先設定解決方案之外，您也可以下載完整的原始程式碼來自訂和擴充解決方案，以符合特定需求。

可用的預先設定解決方案包括：

- 遠端監視
- 預測性維護

下表顯示這些預先設定解決方案如何對應至特定 IoT 功能：

| 方案 | 資料擷取 | 裝置身分識別 | 命令和控制 | 規則和動作 | 預測性分析 |
|------------------------|----------------|-----------------|---------------------|-------------------|----------------------|
| 遠端監視 | 是 | 是 | 是 | 是 | - | | 預測性維護 | 是 | 是 | 是 | 是 | 是 |

## 遠端監視範例概觀

遠端監視是最簡易且具備完整功能的預先設定解決方案。本節以簡介整組預先設定解決方案的方式說明遠端監視預先設定解決方案的一些重要功能。

下圖和下列各節說明解決方案的重要功能。下列各節提供更多圖中所示元素的資訊。

![遠端監視預先設定解決方案架構][img-remote-monitoring-arch]

### 裝置

遠端監視預先設定解決方案中已預先佈建的裝置是一部軟體模擬的冷卻器，它會傳送溫度和濕度遙測資料。該裝置也可以回應一組透過 IoT 中心從解決方案入口網站傳送的命令。已在模擬器中實作的命令為：Ping Device、Start Telemetry、Stop Telemetry、Change Set Point Temp、Diagnostic Telemetry 和 Change Device State。

此預先設定解決方案中的冷卻器與典型 IoT 解決方案架構中的「裝置和資料來源」相對應。

### IoT 中心

IoT 中心會從單一端點接收來自冷卻器的遙測資料，並維護裝置可從中抓取命令 (如 PingDevice 命令) 的裝置特定端點。

IoT 中心會透過取用者群組端點公開收到的遙測資料。

此預先設定解決方案中的 IoT 中樞執行個體與典型 IoT 解決方案架構中的「雲端閘道器」相對應。

### Azure 串流分析

預先設定解決方案使用 [Azure 串流分析][]工作來篩選來自冷卻器的事件串流。其中一項工作會將所有的遙測資料傳送到冷儲存體的 Azure 儲存體 Blob。第二項工作會篩選命令回應訊息和裝置狀態更新訊息的事件串流，並傳送這些特定訊息至 Azure 事件中樞端點。第三項工作會篩選已觸發的警示，並在解決方案入口網站之儀表板檢視中的警示歷程記錄資料表內顯示這些警示。


### 事件處理器

事件處理器執行個體 (在 Web 工作中執行) 會處理命令回應和裝置狀態資料，並將此資訊儲存在 Azure DocumentDB 資料庫中。

### 解決方案入口網站

解決方案入口網站是 Web 式 UI，可讓您：

- 檢視儀表板中的遙測和警示歷程記錄。
- 佈建新裝置。
- 管理和監視裝置。
- 將命令傳送至特定裝置。
- 管理規則和動作。

> [AZURE.NOTE]解決方案入口網站的裝置檢視會將 IoT 中樞裝置身分識別登錄與 DocumentDB 資料庫中的豐富裝置狀態資訊維持同步。

## 後續步驟

請探索下列資源以深入了解 IoT 預先設定解決方案：

- [Azure IoT 預先設定解決方案概觀](iot-suite-overview.md)
- [IoT 預先設定解決方案入門](iot-suite-getstarted-preconfigured-solutions.md)

[img-remote-monitoring-arch]: ./media/iot-suite-what-are-preconfigured-solutions/remote-monitoring-arch1.png
[Azure 串流分析]: https://azure.microsoft.com/services/stream-analytics/

<!---HONumber=Nov15_HO3-->