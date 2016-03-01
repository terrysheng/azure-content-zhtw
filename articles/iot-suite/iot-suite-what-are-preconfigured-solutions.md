<properties
 pageTitle="Azure IoT 預先設定解決方案 | Microsoft Azure"
 description="說明 Azure IoT 預先設定解決方案及其架構，且包含其他資源的連結。"
 services=""
 suite="iot-suite"
 documentationCenter=""
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-suite"
 ms.devlang="na"
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="02/19/2016"
 ms.author="dobett"/>

# 什麼是 Azure IoT 套件預先設定的解決方案？

Azure IoT 套件預先設定的解決方案是常見 IoT 解決方案模式的實作，您可以使用您的 Azure 訂用帳戶將這些模式部署到 Microsoft Azure。您可以使用預先設定的解決方案：

- 做為您的 IoT 解決方案的起點。
- 深入了解 IoT 解決方案設計與開發中的常見模式。

每個預先設定解決方案都會使用模擬的裝置實作一個常見 IoT 案例，且為完整的端對端實作，以產生遙測。

除了部署並執行 Azure 中的解決方案之外，您也可以下載完整的原始程式碼，然後自訂和擴充解決方案以符合您的特定 IoT 需求。

> [AZURE.NOTE] [開始使用 IoT 預先設定的解決方案][lnk-preconf-get-started]一文說明如何部署並執行其中的一個解決方案。

下表顯示這些解決方案與特定的 IoT 功能如何對應：

| 方案 | 資料擷取 | 裝置身分識別 | 命令和控制 | 規則和動作 | 預測性分析 |
|------------------------|-----|-----|-----|-----|-----|
| [遠端監視][lnk-remote-monitoring] | 是 | 是 | 是 | 是 | - | 
| [預測性維護][lnk-predictive-maintenance] | 是 | 是 | 是 | 是 | 是 |

## 遠端監視預先設定解決方案概觀

在本文中，我們選擇討論遠端監視預先設定解決方案，因為它是解決方案中最簡單的一個，而且說明了其他解決方案所共用的常見設計元素。

下圖說明遠端監視解決方案的主要元素。下列各節提供關於這些元素的詳細資訊。

![遠端監視預先設定解決方案架構][img-remote-monitoring-arch]

## 裝置

當您部署遠端監視預先設定的解決方案時，有四個模擬裝置會預先佈建於解決方案中，以便模擬冷卻裝置。這些模擬裝置具有可發出遙測資訊的內建溫度和溼度模型。

當裝置第一次連線到遠端監視預先設定的解決方案中的 IoT 中樞時，傳送到 IoT 中樞的裝置資訊訊息會列舉裝置可以回應的命令清單。在遠端監視預先設定的解決方案中，命令如下：

- *Ping 裝置*。此裝置會以通知來回應此命令。這適合用於檢查裝置是否仍在作用中和接聽。
- *啟動遙測*。指示裝置開始傳送遙測資訊。
- *停止遙測*。指示裝置停止傳送遙測資訊。
- *變更設定點溫度*。控制裝置傳送的模擬溫度遙測值。這適合用於測試。
- *診斷遙測*。控制裝置是否應傳送外部溫度做為遙測資訊。
- *變更裝置狀態*。設定裝置所報告的裝置狀態中繼資料屬性。這適合用於測試。

您可以將其他的模擬裝置新增至解決方案，以便發出相同的遙測資訊並回應相同的命令。

## IoT 中心

IoT 中樞會從單一端點接收來自冷卻裝置的遙測資料。IoT 中樞也會維護裝置可從中擷取命令的裝置特定端點。

IoT 中樞會透過取用者群組端點讓收到的遙測可供使用。

在此預先設定解決方案中，IoT 中樞執行個體會對應至典型 [IoT 解決方案架構][lnk-what-is-azure-iot]中的「雲端閘道器」。

## Azure 串流分析

預先設定解決方案使用三個 [Azure 串流分析][lnk-asa] (ASA) 作業來篩選來自冷卻器裝置的遙測串流：


- *DeviceInfo 作業* - 將裝置註冊特定訊息傳送至解決方案裝置登錄 (DocumentDB 資料庫)。
- *遙測作業* - 將所有的原始遙測資訊傳送到 Azure Blob 儲存體進行冷儲存，並計算會顯示在解決方案儀表板中的遙測彙總。
- *規則作業* - 篩選遙測串流中超出任何規則臨界值的值。當規則引發時，解決方案入口網站儀表板檢視會將此事件顯示為警示歷程記錄資料表中的新資料列，並根據在解決方案入口網站中的 [規則和動作] 檢視上定義的設定來觸發動作。

在此預先設定解決方案中，ASA 作業會形成典型 [IoT 解決方案架構][lnk-what-is-azure-iot]中「IoT 解決方案後端」的一部分。

## 事件處理器

在 [WebJob][lnk-web-job] 中執行的 [EventPocessorHost][lnk-event-processor] 執行個體會處理 DeviceInfo 與規則作業的輸出。

在此預先設定解決方案中，事件處理器會形成典型 [IoT 解決方案架構][lnk-what-is-azure-iot]中「IoT 解決方案後端」的一部分。

## 解決方案入口網站

![解決方案儀表板][img-dashboard]

解決方案入口網站是 Web 型 UI，部署至雲端做為預先設定解決方案的一部分。可以讓您：

- 檢視儀表板中的遙測和警示歷程記錄。
- 佈建新裝置。
- 管理和監視裝置。
- 將命令傳送至特定裝置。
- 管理規則和動作。

> [AZURE.NOTE] 預先設定的解決方案會讓 IoT 中樞[裝置身分識別登錄][lnk-identity-registry]與解決方案的裝置登錄 (DocumentDB 資料庫) 保持同步，以儲存更豐富的裝置中繼資料。

在此預先設定解決方案中，解決方案入口網站會形成典型 [IoT 解決方案架構][lnk-what-is-azure-iot]中「IoT 解決方案後端」及「處理和業務連線」的一部分。

## 後續步驟

請探索下列資源以深入了解 IoT 預先設定解決方案：

- [IoT 預先設定解決方案入門][lnk-preconf-get-started]
- [預先設定的預防性維護解決方案概觀][lnk-predictive-maintenance]

[img-remote-monitoring-arch]: ./media/iot-suite-what-are-preconfigured-solutions/remote-monitoring-arch1.png
[img-dashboard]: ./media/iot-suite-what-are-preconfigured-solutions/dashboard.png
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

<!---HONumber=AcomDC_0224_2016-->