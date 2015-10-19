<properties
	pageTitle="Microsoft Azure IoT Suite 概觀 | Microsoft Azure"
	description="這將提供 Azure IoT 套件的概觀。"
	services=""
	documentationCenter=".net"
	authors="aguilaaj"
	manager="timlt"
	editor=""/>

<tags
     ms.service="na"
     ms.devlang="na"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="tbd"
     ms.date="10/06/2015"
     ms.author="araguila"/>

# Azure IoT 套件的概觀

Microsoft 使用 Azure IoT 服務提供範圍廣泛的功能。這些服務提供企業等級能力，可從裝置收集資料、分析移動中的資料串流、儲存和查詢大型資料集、視覺化即時和歷程記錄資料，並與重要的後端辦公室系統整合。Azure IoT 套件將一組常用服務和擴充的功能封裝在一起，為客戶縮短創造價值的時程。這些擴充功能包括預先設定的解決方案，提供最常見解決方案模式的基本實作。與 IoT 軟體開發套件 (SDK) 結合後，客戶就能夠輕鬆自訂預先設定的解決方案，或利用這些功能做為開發新解決方案的範例。

## Azure IoT 套件中的 Azure IoT 服務

Azure IoT 套件的核心是 IoT 中樞服務。此服務會提供裝置到雲端與雲端到服務的訊息。它可做為進入雲端和其他重要 IoT Suite 服務的閘道。

移動中的資料分析是由 Azure 串流分析提供。IoT 套件會運用這項服務來處理內送遙測訊息、執行彙總以及偵測事件。它也用來處理可用於裝置中繼資料或命令回應的參考訊息。

資料儲存體功能是透過 Azure 儲存體和 Azure DocumentDB 的組合啟用。Azure 儲存體可啟用 blob 儲存體的遙測，用於保留和未來分析。DocumentDB 對半結構化資料的索引儲存體功能可用來管理裝置中繼資料。透過允許不同裝置有不同的內容，這可實現異質性裝置的管理。

Azure 網站和 Microsoft Power BI 的結合可提供視覺化的資料。Power BI 的彈性可讓客戶從 IoT 套件資料快速建置自己的互動式儀表板。

有關架構以及如何使用這些服務的詳細資料可在 [Microsoft Azure 和物聯網 (IoT)](iot-suite-what-is-azure-iot.md) 中找到。

## 預先設定的解決方案

Azure IoT 套件中包含的預先設定解決方案可讓客戶能夠快速地開始使用及探索 Azure IoT 套件實現的案例。

可用的第一個預先設定解決方案是[遠端監視](iot-suite-what-are-preconfigured-solutions.md)。

<!---HONumber=Oct15_HO2-->