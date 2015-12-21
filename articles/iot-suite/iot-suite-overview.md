<properties
	pageTitle="Microsoft Azure IoT Suite 概觀 | Microsoft Azure"
	description="這提供 Azure IoT 套件概觀，包括封裝和預先設定解決方案。"
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
     ms.date="11/27/2015"
     ms.author="dobett"/>

# Azure IoT 套件的概觀

Azure IoT 服務提供廣泛的功能。這些企業等級的服務可讓您：

- 從裝置收集資料
- 分析移動中的資料串流
- 儲存和查詢大型資料集
- 視覺化即時和歷程記錄資料
- 與後端辦公室系統整合

Azure IoT 套件封裝通常會一起運用 Azure 服務與自訂延伸模組做為預先設定的解決方案。這些預先設定的解決方案是常見的 IoT 解決方案模式的基本實作，可幫助您減少傳遞 IoT 解決方案所花費的時間。使用 [IoT 軟體開發套件][lnk-sdks]，您可以輕易地自訂這些預先設定的解決方案以符合您自己的需求，或在開發新的解決方案時運用它們做為範例。

下列影片提供 Azure IoT 套件的簡介：

> [AZURE.VIDEO azurecon-2015-introducing-the-microsoft-azure-iot-suite]

## Azure IoT 套件中的 Azure IoT 服務

Azure IoT 套件的核心是 [Azure IoT 中樞][lnk-iot-hub]服務。這項服務提供裝置到雲端和雲端到裝置的傳訊功能，並做為雲端到其他重要的 IoT 套件服務的閘道器。

[Azure 串流分析][lnk-asa]提供移動中的資料分析。IoT 套件會運用這項服務來處理內送遙測、執行彙總以及偵測事件。預先設定的解決方案也會使用串流分析來處理資訊訊息，它包含像是中繼資料或是從裝置回應的命令的資料。

[Azure 儲存體][lnk-azure-storage]和 [Azure DocumentDB][lnk-document-db] 提供資料儲存體功能。預先設定的解決方案使用 Blob 儲存體來儲存遙測，並且讓它可用於分析。預先設定的解決方案使用 DocumentDB 的半結構化資料功能上的已編制索引的儲存體，以管理裝置中繼資料。這可讓解決方案管理具有不同內容儲存體需求的異質裝置。

[Azure Web Apps][lnk-web-apps] 和 [Microsoft Power BI][lnk-power-bi] 提供資料視覺化功能。Power BI 的彈性可讓您快速建置自己的互動式儀表板 (使用 IoT 套件資料)。

如需典型的 IoT 解決方案架構的概觀，請參閱 [Microsoft Azure 和物聯網 (IoT)][iot-suite-what-is-azure-iot]。

## 預先設定的解決方案

IoT 套件包括預先設定的解決方案，可讓您快速地開始使用，並瀏覽 Azure IoT 套件使其可行的常見 IoT 案例。您可以將預先設定的解決方案部署到您的 Azure 訂用帳戶，然後再執行完整的端對端 IoT 解決方案。

## 後續步驟

若要深入了解 IoT 套件中的預先設定的解決方案，請參閱[什麼是 Azure IoT 預先設定的解決方案？][lnk-what-are-preconfig]

若要開始使用其中一個預先設定的解決方案，請參閱[開始使用 IoT 預先設定的解決方案][lnk-preconfig-start]。

若要深入了解 Azure IoT 中樞服務，請參閱 [IoT 中樞文件][lnk-iot-hub]。


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

<!-------HONumber=AcomDC_1210_2015--->