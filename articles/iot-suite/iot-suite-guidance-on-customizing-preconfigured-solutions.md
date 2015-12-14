<properties
	pageTitle="自訂預先設定解決方案 | Microsoft Azure"
	description="提供如何自訂 Azure IoT 套件預先設定解決方案的指引。"
	services=""
	documentationCenter=".net"
	authors="stevehob"
	manager="timlt"
	editor=""/>

<tags
     ms.service="na"
     ms.devlang="dotnet"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/29/2015"
     ms.author="stevehob"/>

# 自訂預先設定的解決方案

Azure IoT Suite 提供的預先設定解決方案能夠示範套件中共同運作的服務可提供端對端解決方案。從這個起點開始，有好幾個地方可以擴充並針對特定案例自訂解決方案。下列各節說明這些常見的自訂點。

## 尋找原始程式碼

預先設定解決方案的原始程式碼可在以下 GitHub 的儲存機制取得：

- 遠端監視：[https://www.github.com/Azure/azure-iot-remote-monitoring](https://github.com/Azure/azure-iot-remote-monitoring)

此來源是用來示範使用 Azure IoT Suite 實作遠端監視的核心功能。

## 變更預先設定規則

遠端監視解決方案包含二個 [Azure 串流分析](http://azure.microsoft.com/services/stream-analytics)工作，以實作顯示在儀表板上遙測和警示邏輯。

第一個工作會選取所有來自遙測傳入資料流的資料，並建立二個不同的輸出。工作的名稱會是 **[solution name]-Telemetry**。

- 第一個輸出會使用 `SELECT *` 取得的所有資料，並將它輸出到 blob 儲存體。此 Blob 儲存體是儀表板讀取原始值以建立圖表的來源。
- 第二個輸出會在 5 分鐘的滑動視窗執行 `AVG()`、`MIN()` 和 `MAX()` 計算。此資料會顯示在儀表板上的轉盤中。

使用串流分析使用者介面，您可以直接編輯這些工作以改變邏輯或新增案例特定的邏輯。

第二個工作的作業是針對解決方案 [規則] 頁面中建立的「裝置對臨界值」值。此工作會使用參考資料 (針對各裝置設定的臨界值)。它會比較臨界值以查看是否大於 (`>`) 實際值。您可以修改此工作，例如變更比較運算子。

> [AZURE.NOTE]遠端監視儀表板依賴特定資料，因此變更工作可能會造成儀表板失敗。

## 新增自己的規則

除了變更預先設定的 Azure 串流分析工作，您也可以使用 Azure 入口網站新增工作或新增現有工作的查詢。

## 自訂裝置

最常見的擴充功能活動之一是使用案例特定的裝置。使用裝置的方法有數種。這些方法包括變更模擬裝置以符合您的案例，或使用 [IoT Device SDK][] 將實體裝置連接到解決方案。

如需將裝置加入遠端監視預先設定解決方案的竹步說明，請參閱 [Iot Suite 連接裝置](iot-suite-connecting-devices.md)。

### 建立自己的模擬裝置

之前提及的遠端監視解決方案原始程式碼中包含 .NET 模擬器。此模擬器是解決方案中佈建的模擬器，並且可以變更以傳送不同的中繼資料、遙測或回應給不同的命令。

此外，Azure IoT 提供針對和遠端監視預先設定解決方案搭配使用所設計的 [C SDK 範例](https://github.com/Azure/azure-iot-sdks/c/serializer/samples/remote_monitoring)。

### 建置並使用自己的 (實體) 裝置

[Azure IoT SDK](https://github.com/Azure/azure-iot-sdks) 提供用來將各種裝置類型 (語言和作業系統) 連接至 IoT 解決方案中的程式庫。

## 後續步驟

如需 IoT 裝置的詳細資訊，請參閱 [Azure IoT 開發人員網站](http://azure.microsoft.com/develop/iot)來尋找連結和文件。

[IoT Device SDK]: https://azure.microsoft.com/documentation/articles/iot-hub-sdks-summary/

<!---HONumber=AcomDC_1203_2015-->