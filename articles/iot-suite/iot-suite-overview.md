<properties
	pageTitle="Microsoft Azure IoT Suite 概觀 | Microsoft Azure"
	description="這提供 Azure IoT 套件概觀，包括封裝和預先設定解決方案。"
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

# Azure IoT 套件的概觀

Azure IoT 服務提供廣泛的功能。這些企業等級的服務可讓您：

- 從裝置收集資料
- 分析移動中的資料串流
- 儲存和查詢大型資料集
- 視覺化即時和歷程記錄資料
- 與後端辦公室系統整合

為了提供這些功能，Azure IoT 套件將多個 Azure 服務與自訂延伸模組封裝在一起做為「預先設定的解決方案」。這些預先設定的解決方案是常見 IoT 解決方案模式的基礎實作，可幫助您減少實行 IoT 解決方案所花費的時間。透過使用 [IoT 軟體開發套件][lnk-sdks]，您將可自訂和擴充這些解決方案來滿足您自己的需求。您也可以使用這些解決方案做為開發新 IoT 解決方案時的範例或範本。

下列影片提供 Azure IoT 套件的簡介：

> [AZURE.VIDEO azurecon-2015-introducing-the-microsoft-azure-iot-suite]

## Azure IoT 套件中的 Azure IoT 服務

預先設定的解決方案通常使用下列服務：

- Azure IoT 套件的核心是 [Azure IoT 中樞][lnk-iot-hub]服務。這項服務提供裝置到雲端和雲端到裝置的傳訊功能，並做為雲端到其他重要的 IoT 套件服務的閘道器。此服務可讓您從您的裝置大量接收訊息，並將命令傳送給您的裝置。

- [Azure 串流分析][lnk-asa]提供移動中的資料分析。IoT 套件會運用這項服務來處理內送遙測、執行彙總以及偵測事件。預先設定的解決方案也會使用串流分析來處理資訊訊息，它包含像是中繼資料或是從裝置回應的命令的資料。這些解決方案使用「串流分析」來處理來自您裝置的訊息，並將這些訊息傳送給其他服務。

- [Azure 儲存體][lnk-azure-storage]和 [Azure DocumentDB][lnk-document-db] 提供資料儲存體功能。預先設定的解決方案使用 Blob 儲存體來儲存遙測，並且讓它可用於分析。這些解決方案使用 DocumentDB 來儲存裝置中繼資料，以及啟用解決方案的裝置管理功能。

- [Azure Web Apps][lnk-web-apps] 和 [Microsoft Power BI][lnk-power-bi] 提供資料視覺化功能。Power BI 的彈性可讓您快速建置自己的互動式儀表板 (使用 IoT 套件資料)。

如需典型的 IoT 解決方案架構的概觀，請參閱 [Microsoft Azure 和物聯網 (IoT)][iot-suite-what-is-azure-iot]。

## 預先設定的解決方案

IoT 套件包括預先設定的解決方案，可讓您快速地開始使用，並瀏覽 Azure IoT 套件使其可行的常見 IoT 案例，例如「遠端監視」和「預測性維護」。您可以將這些解決方案部署到您的 Azure 訂用帳戶，然後執行完整的端對端 IoT 案例。

## 後續步驟

既然您已概略了解 IoT 套件的功能及其主要元件，您可以：

- 深入了解 IoT 套件中預先設定的解決方案，請參閱[什麼是 Azure IoT 預先設定的解決方案？][lnk-what-are-preconfig]

- 開始使用其中一個預先設定的解決方案，請參閱[開始使用 IoT 預先設定的解決方案][lnk-preconfig-start]。

- 深入了解 Azure IoT 中樞服務，請參閱 [IoT 中樞文件][lnk-iot-hub]。


[lnk-sdks]: https://azure.microsoft.com/documentation/articles/iot-hub-sdks-summary/
[lnk-iot-hub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-asa]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-azure-storage]: https://azure.microsoft.com/documentation/services/storage/
[lnk-document-db]: https://azure.microsoft.com/documentation/services/documentdb/
[lnk-power-bi]: https://powerbi.microsoft.com/
[lnk-web-apps]: https://azure.microsoft.com/documentation/services/app-service/web/
[iot-suite-what-is-azure-iot]: iot-suite-what-is-azure-iot.md
[lnk-what-are-preconfig]: iot-suite-what-are-preconfigured-solutions.md
[lnk-preconfig-start]: iot-suite-getstarted-preconfigured-solutions/
[lnk-iot-hub]: https://azure.microsoft.com/documentation/services/iot-hub/

<!---HONumber=AcomDC_0224_2016-->