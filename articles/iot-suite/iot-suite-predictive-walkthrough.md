<properties
 pageTitle="預測性維護逐步解說 |Microsoft Azure"
 description="預先設定之 Azure IoT 預測性維護解決方案的逐步解說。"
 services=""
 suite="iot-suite"
 documentationCenter=""
 authors="aguilaaj"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-suite"
 ms.devlang="na"
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="12/02/2015"
 ms.author="araguila"/>

# 預先設定的預測性維護解決方案逐步解說

## 簡介

IoT Suite 預先設定的預測性維護解決方案是一個端對端解決方案，適用於可預測可能發生失敗時間點的商務案例。您可以主動對最佳化維護等活動運用此預先設定的解決方案。此解決方案結合了主要 Azure IoT Suite 服務，包括帶有實驗的 [Azure Machine Learning][lnk_machine_learning] 工作區，以便根據公用範例資料集供預測飛機引擎的剩餘使用年限 (RUL)。此解決方案提供完整的商務案例實作做為您規劃和實作此類型 IoT 解決方案的起點，以滿足特定商務需求。

## 邏輯架構

下圖概述預先設定解決方案的邏輯元件：

![][img-architecture]

藍色項目是您在佈建預先設定的解決方案時選取的位置中佈建的 Azure 服務。您可以在美國東部、北歐或東亞區域中佈建預先設定的解決方案。

某些資源不適用於您佈建預先設定之解決方案的區域。圖表中的橘色項目代表在最接近的可用區域 (中南美洲、西歐或東南亞) 中佈建的 Azure 服務。

綠色項目是表示飛機引擎的模擬裝置。您可以在下面進一步了解這些模擬裝置。

灰色項目代表實作裝置管理功能的元件。目前預先設定的預測性維護解決方案版本不會佈建這些資源。若要深入了解裝置管理，請參閱[遠端監視預先設定的解決方案][lnk-remote-monitoring]。

## 模擬的裝置

在預先設定的解決方案中，模擬裝置代表飛機引擎。此解決方案已佈建 2 個對應至單一飛機的引擎。每個引擎會發出 4 種遙測：感應器 9、感應器 11、感應器 14 和感應器 15，以提供機器學習服務模型計算該引擎的剩餘使用年限 (RUL) 所需的資料。每個模擬的裝置會將下列遙測訊息傳送至 IoT 中樞：

*週期計數*。一個週期表示 2 至 10 小時不等的已完成飛行，而在飛行時間內會每半小時擷取一次遙測資料。

*遙測*。有 4 個代表引擎屬性的感應器。這些感應器會一般會標示為感應器 9、感應器 11、感應器 14 和感應器 15。這 4 個感應器代表足以從 RUL 的機器學習服務模型取得有用結果的遙測。此模型是根據包含實際引擎感應器資料的公用資料集建立而來。如需有關如何從原始資料集建立模型的詳細資訊，請參閱 [Cortana 分析資源庫預測性維護範本][lnk-cortana-analytics]。

模擬的裝置可以處理從 IoT 中樞傳送的下列命令：

| 命令 | 說明 |
|---------|-------------|
| StartTelemetry | 控制模擬的狀態。<br/>啟動裝置傳送遙測 |
| StopTelemetry | 控制模擬的狀態。<br/>停止裝置傳送遙測 |

IoT 中樞會提供裝置命令通知。

## Azure 串流分析作業

**工作：遙測**會使用兩個陳述式操作傳入裝置遙測串流。第一個陳述式會從裝置選取所有遙測，然後將此資料從在 Web 應用程式中視覺化的位置傳送至 Blob 儲存體。第二個陳述式會透過兩分鐘的滑動視窗計算平均感應器值，然後透過事件中樞將此資料傳送至**事件處理器**。

## 事件處理器

**事件處理器**會採用已完成週期的平均感應器值，並將這些值傳遞至可公開機器學習服務定型模型的 API，以便計算引擎的 RUL。

## Azure Machine Learning

如需有關如何從原始資料集建立模型的詳細資訊，請參閱 [Cortana 分析資源庫預測性維護範本][lnk-cortana-analytics]。

## 讓我們開始

本章節將逐步解說解決方案的元件、說明預定的使用情況，並提供範例。

### 預測性維護儀表板

Web 應用程式中的此頁面會使用 PowerBI JavaScript 控制項 (請參閱 [PowerBI 視覺效果儲存機制][lnk-powerbi]) 以視覺化方式呈現：

- Blob 儲存體中串流分析作業的輸出資料。
- 每個飛機引擎的 RUL 和週期計數。

### 觀察雲端解決方案的行為

您可以檢視已佈建的資源，方法是瀏覽至 Azure 入口網站，然後瀏覽至具有您所選之解決方案名稱的資源群組。

![][img-resource-group]

當您佈建預先設定的解決方案，您會收到一封電子郵件，其中包含機器學習服務工作區的連結。如果此機器學習服務工作區不是處於 [就緒] 狀態，您也可以從已佈建解決方案的 [azureiotsuite.com][lnk-azureiotsuite] 頁面瀏覽至此工作區。

![][img-machine-learning]

在解決方案入口網站中，您可以看到範例具備四個模擬裝置，表示各有 2 個引擎的 2 架飛機，而每個引擎有 4 感應器。當您第一次瀏覽至解決方案入口網站時，模擬便會停止。

![][img-simulation-stopped]

按一下 [開始模擬] 即可開始模擬，而您將看到感應器歷程記錄、RUL、週期和 RUL 模擬記錄填入儀表板。

![][img-simulation-running]

當 RUL 小於 160 時 (為了示範目的而選擇的任意臨界值)，解決方案入口網站會在 RUL 旁邊顯示警告符號，並將圖片中的飛機引擎標成黃色。您會注意到 RUL 值有整體向下的趨勢，但傾向於上下波動。這是因為各種週期長度和模型精確度的緣故。

![][img-simulation-warning]

完整模擬需要約 35 分鐘的時間才能完成 148 個週期。160 RUL 臨界值第一次在大約 5 分鐘的時候達到，而這兩個引擎在大約 8 分鐘的時候同時達到臨界值。

模擬會徹底執行 148 個週期的完整資料集並確定最終的 RUL 和週期值。

您可以隨時停止模擬，但按一下 [開始模擬] 會從資料集的開頭重新執行模擬。

## 後續步驟

您現在已執行預先設定的預測性維護解決方案，您可加以修改，請參閱[自訂預先設定的解決方案指引][lnk-customize]。

  
[img-architecture]: media/iot-suite-predictive-walkthrough/architecture.png
[img-resource-group]: media/iot-suite-predictive-walkthrough/resource-group.png
[img-machine-learning]: media/iot-suite-predictive-walkthrough/machine-learning.png
[img-simulation-stopped]: media/iot-suite-predictive-walkthrough/simulation-stopped.png
[img-simulation-running]: media/iot-suite-predictive-walkthrough/simulation-running.png
[img-simulation-warning]: media/iot-suite-predictive-walkthrough/simulation-warning.png

[lnk-powerbi]: https://www.github.com/Microsoft/PowerBI-visuals
[lnk_machine_learning]: https://azure.microsoft.com/services/machine-learning/
[lnk-remote-monitoring]: iot-suite-remote-monitoring-sample-walkthrough.md
[lnk-cortana-analytics]: http://gallery.cortanaanalytics.com/Collection/Predictive-Maintenance-Template-3
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md

<!---HONumber=AcomDC_0218_2016-->