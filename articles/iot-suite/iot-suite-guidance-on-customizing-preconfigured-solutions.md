<properties
	pageTitle="自訂預先設定解決方案 | Microsoft Azure"
	description="提供如何自訂 Azure IoT 套件預先設定解決方案的指引。"
	services=""
    suite="iot-suite"
	documentationCenter=".net"
	authors="stevehob"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-suite"
     ms.devlang="dotnet"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="03/02/2016"
     ms.author="stevehob"/>

# 自訂預先設定的解決方案

Azure IoT Suite 提供的預先設定解決方案能夠示範套件中共同運作的服務可提供端對端解決方案。從這個起點開始，有好幾個地方可以擴充並針對特定案例自訂解決方案。下列各節說明這些常見的自訂點。

## 尋找原始程式碼

預先設定解決方案的原始程式碼可在以下 GitHub 的儲存機制取得：

- 遠端監視：[https://www.github.com/Azure/azure-iot-remote-monitoring](https://github.com/Azure/azure-iot-remote-monitoring)
- 預測性維護︰[https://github.com/Azure/azure-iot-predictive-maintenance](https://github.com/Azure/azure-iot-predictive-maintenance)

提供預先設定解決方案原始程式碼的目的，在於示範實作使用 Azure IoT 套件之 IoT 解決方案的端對端功能時，所採用的模式和作法。每個預先設定解決方案的 GitHub Wiki 備有從命令列建置和部署的指引︰

- [遠端監視 Wiki](https://github.com/Azure/azure-iot-remote-monitoring/wiki)
- [預測性維護 Wiki](https://github.com/Azure/azure-iot-predictive-maintenance/wiki)

## 管理預先設定解決方案中的權限
每個預先設定解決方案的解決方案入口網站會建立為新的 Azure Active Directory 應用程式。您可以管理解決方案入口網站 (AAD 應用程式) 的權限，如下所示︰

1. 開啟 [Azure 傳統入口網站](https://manage.windowsazure.com)。
2. 藉由選取 [我公司所擁有的應用程式] 瀏覽至 AAD 應用程式，然後按一下核取記號。
3. 瀏覽至 [使用者]，接著將 Azure Active Directory 租用戶中的成員指派給某個角色。 

根據預設，應用程式會佈建管理員、唯讀及隱含唯讀等角色。隱含唯讀可授與隸屬於 Azure Active Directory 租用戶，但尚未獲得角色指派的使用者。在分接 GitHub 儲存機制並重新部署解決方案後，您可以修改 [RolePermissions.cs](https://github.com/Azure/azure-iot-remote-monitoring/blob/master/DeviceAdministration/Web/Security/RolePermissions.cs)。

## 變更預先設定規則

遠端監視解決方案包含三個 [Azure 串流分析](https://azure.microsoft.com/services/stream-analytics/)工作，這些工作可實作針對解決方案顯示的裝置資訊、遙測及規則邏輯。

[遠端監視預先設定解決方案逐步解說](iot-suite-remote-monitoring-sample-walkthrough.md)提供這三個串流分析工作和其語法的深入描述。

您可以直接編輯這些工作以改變邏輯，或新增案例特有的邏輯。您可以尋找串流分析工作，如下所示︰
 
1. 移至 [Azure 入口網站](https://portal.azure.com)。
2. 瀏覽至名稱與 IoT 解決方案相同的資源群組。 
3. 選取要修改的 Azure 串流分析作業。 
4. 在命令集中選取 [停止] 以停止作業。 
5. 編輯輸入、查詢及輸出。

    簡單修改的目的在於變更規則作業的查詢，以便使用 "<" 而不是 ">"。在編輯規則時，解決方案入口網站仍會顯示 **">"**，不過因為基礎作業中的變更，您可以發現行為已翻轉。

6. 啟動工作

> [AZURE.NOTE] 遠端監視儀表板依賴特定資料，因此變更工作可能會造成儀表板失敗。

## 新增自己的規則

除了變更預先設定的 Azure 串流分析工作，您也可以使用 Azure 入口網站新增工作或新增現有工作的查詢。

## 自訂裝置

最常見的擴充功能活動之一是使用案例特定的裝置。使用裝置的方法有數種。這些方法包括變更模擬裝置以符合您的案例，或使用 [IoT Device SDK][] 將實體裝置連接到解決方案。

如需將裝置加入遠端監視預先設定解決方案的竹步說明，請參閱 [Iot Suite 連接裝置](iot-suite-connecting-devices.md)。

### 建立自己的模擬裝置

之前提及的遠端監視解決方案原始程式碼中包含 .NET 模擬器。此模擬器是解決方案中佈建的模擬器，並且可以變更以傳送不同的中繼資料、遙測或回應給不同的命令。

遠端監視預先設定解決方案中的預先設定模擬器是發出溫度和濕度遙測的冷卻裝置，分接 GitHub 儲存機制後，您可以在 [Simulator.WebJob](https://github.com/Azure/azure-iot-remote-monitoring/tree/master/Simulator/Simulator.WebJob) 專案中修改模擬器。

此外，Azure IoT 提供針對和遠端監視預先設定解決方案搭配使用所設計的 [C SDK 範例](https://github.com/Azure/azure-iot-sdks/c/serializer/samples/remote_monitoring)。

### 建置並使用自己的 (實體) 裝置

[Azure IoT SDK](https://github.com/Azure/azure-iot-sdks) 提供用來將各種裝置類型 (語言和作業系統) 連接至 IoT 解決方案中的程式庫。

## 後續步驟

本文件是否涵蓋您感興趣的自訂內容？ 請在[使用者心聲](https://feedback.azure.com/forums/321918-azure-iot)中加入功能建議、在文章下方留言或傳送郵件給我們 (iotsolhelp@microsoft.com)。

如需 IoT 裝置的詳細資訊，請參閱 [Azure IoT 開發人員網站](https://azure.microsoft.com/develop/iot/)來尋找連結和文件。

[IoT Device SDK]: https://azure.microsoft.com/documentation/articles/iot-hub-sdks-summary/

<!---HONumber=AcomDC_0309_2016-->