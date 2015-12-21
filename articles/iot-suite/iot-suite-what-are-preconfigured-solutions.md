<properties
 pageTitle="Azure IoT 預先設定解決方案 | Microsoft Azure"
 description="說明 Azure IoT 預先設定解決方案及其架構，且包含其他資源的連結。"
 services=""
 documentationCenter=""
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="na"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="11/30/2015"
 ms.author="dobett"/>

# 什麼是 Azure IoT 套件預先設定的解決方案？

Azure IoT 套件預先設定的解決方案是常見 IoT 解決方案模式的實作，您可以使用您的 Azure 訂用帳戶將這些模式部署到 Microsoft Azure。您可以使用預先設定的解決方案：

- 做為您的 IoT 解決方案的起點。
- 深入了解 IoT 解決方案設計與開發中的常見模式。

每個預先設定解決方案都會使用模擬的裝置實作一個常見 IoT 案例，且為完整的端對端實作，以產生遙測。

除了部署並執行 Azure 中的預先設定解決方案之外，您也可以下載完整的原始程式碼，然後自訂和擴充解決方案，以符合特定 IoT 需求。

可用的預先設定解決方案包括：

- [遠端監視][lnk-remote-monitoring]
- [預測性維護][lnk-predictive-maintenance]

下表顯示這些預先設定解決方案如何對應至特定 IoT 功能：

| 方案 | 資料擷取 | 裝置身分識別 | 命令和控制 | 規則和動作 | 預測性分析 |
|------------------------|-----|-----|-----|-----|-----|
| 遠端監視 | 是 | 是 | 是 | 是 | - | | 預測性維護 | 是 | 是 | 是 | 是 | 是 |

## 遠端監視預先設定解決方案概觀

本節說明遠端監視預先設定解決方案的一些重要元素。遠端監視是最簡單的預先設定解決方案，並且說明其他預先設定解決方案共用的一般設計元素。

下圖說明遠端監視解決方案的主要元素。下列各節提供關於這些元素的詳細資訊。

![遠端監視預先設定解決方案架構][img-remote-monitoring-arch]

## 裝置

當您部署遠端監視預先設定解決方案時，部署包括軟體裝置模擬器的執行個體，可模擬實體冷卻器裝置。模擬的裝置會將溫度和溼度遙測傳送至 IoT 中樞端點。模擬的裝置也會回應透過 IoT 中樞從解決方案入口網站傳送的下列命令：

- 偵測裝置
- 啟動遙測
- 停止遙測
- 變更設定點溫度
- 診斷遙測
- 變更裝置狀態

## IoT 中心

IoT 中樞會從單一端點接收來自冷卻裝置的遙測資料。IoT 中樞也會維護裝置可從中抓取命令 (如 **PingDevice** 命令) 的裝置特定端點。

IoT 中樞會透過取用者群組端點讓收到的遙測可供使用。

在此預先設定解決方案中，IoT 中樞執行個體會對應至典型 [IoT 解決方案架構][lnk-what-is-azure-iot]中的「雲端閘道器」。

## Azure 串流分析

預先設定解決方案使用三個 [Azure 串流分析][lnk-asa] (ASA) 作業來篩選來自冷卻器裝置的遙測串流：

- 作業 #1 會將冷儲存體的所有遙測傳送至 Azure Blob 儲存體
- 作業 #2 會篩選遙測串流以識別來自裝置的命令回應訊息和裝置狀態更新訊息，並將這些特定訊息傳送至 Azure 事件中樞端點。
- 作業 #3 會篩選觸發警示的值的遙測串流。當值觸發警示時，解決方案就會在解決方案入口網站的儀表板檢視的警示歷程記錄資料表中顯示通知。

在此預先設定解決方案中，ASA 作業會形成典型 [IoT 解決方案架構][lnk-what-is-azure-iot]中的「IoT 解決方案後端」的一部分。

## 事件處理器

[EventPocessorHost][lnk-event-processor] 執行個體 (在 [WebJob][lnk-web-job] 中執行) 會處理 ASA 作業 #2 識別的命令回應和裝置狀態訊息，然後將此資訊儲存在 [Azure DocumentDB][lnk-document-db] 資料庫中。

在此預先設定解決方案中，事件處理器會形成典型 [IoT 解決方案架構][lnk-what-is-azure-iot]中的「IoT 解決方案後端」的一部分。

## 解決方案入口網站

解決方案入口網站是 Web 型 UI，部署至雲端做為預先設定解決方案的一部分。可以讓您：

- 檢視儀表板中的遙測和警示歷程記錄。
- 佈建新裝置。
- 管理和監視裝置。
- 將命令傳送至特定裝置。
- 管理規則和動作。

> [AZURE.NOTE]解決方案入口網站會將 IoT 中樞[裝置身分識別登錄][lnk-identity-registry]與解決方案的 DocumentDB 資料庫中的豐富裝置狀態資訊維持同步。

在此預先設定解決方案中，解決方案入口網站會形成典型 [IoT 解決方案架構][lnk-what-is-azure-iot]中的「IoT 解決方案後端」和「處理和業務連線」的一部分。

## 後續步驟

請探索下列資源以深入了解 IoT 預先設定解決方案：

- [Azure IoT 預先設定解決方案概觀][lnk-suite-overview]
- [IoT 預先設定解決方案入門][lnk-preconf-get-started]

[img-remote-monitoring-arch]: ./media/iot-suite-what-are-preconfigured-solutions/remote-monitoring-arch1.png
[lnk-remote-monitoring]: iot-suite-remote-monitoring-sample-walkthrough.md
[lnk-what-is-azure-iot]: iot-suite-what-is-azure-iot.md
[lnk-asa]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-event-processor]: event-hubs-programming-guide.md#event-processor-host
[lnk-web-job]: web-sites-create-web-jobs.md
[lnk-document-db]: https://azure.microsoft.com/documentation/services/documentdb/
[lnk-identity-registry]: iot-hub-devguide.md#device-identity-registry
[lnk-suite-overview]: iot-suite-overview.md
[lnk-preconf-get-started]: iot-suite-getstarted-preconfigured-solutions.md
[lnk-predictive-maintenance]: iot-suite-predictive-overview.md

<!---HONumber=AcomDC_1210_2015-->