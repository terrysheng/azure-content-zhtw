<properties
 pageTitle="遠端監視預先設定解決方案逐步解說 | Microsoft Azure"
 description="說明 Azure IoT 預先設定解決方案遠端監視及其架構。"
 services=""
 suite="iot-suite"
 documentationCenter=""
 authors="stevehob"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-suite"
 ms.devlang="na"
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="03/02/2016"
 ms.author="stevehob"/>

# 遠端監視預先設定解決方案逐步解說

## 簡介

IoT 套件遠端監視預先設定解決方案是商務案例基本的端對端監視解決方案，可以在遠端位置操作多部機器。解決方案結合主要的 Azure IoT Suite 服務，以提供商務案例的一般實作，同時也是想要規劃實作此類型的 IoT 解決方案以符合各自特定商務需求之客戶的起點。

## 邏輯架構

下圖概述預先設定解決方案的邏輯元件：

![](media/iot-suite-remote-monitoring-sample-walkthrough/remote-monitoring-architecture.png)


### 模擬的裝置

在預先設定解決方案中，模擬的裝置會表示冷卻裝置 (例如建築物空調或設備空氣處理單位)。每個模擬的裝置會將下列遙測訊息傳送至 IoT 中樞：


| 訊息 | 說明 |
|----------|-------------|
| 啟動 | 當裝置啟動時，它會傳送**裝置資訊**訊息，其中包含自身的相關資訊，例如裝置識別碼、裝置中繼資料、裝置支援的命令清單，以及裝置的目前組態。 |


模擬的裝置會傳送下列裝置屬性做為中繼資料：

| 屬性 | 目的 |
|------------------------|--------- |
| 裝置識別碼 | 在解決方案中建立裝置時所提供或指派的識別碼。 |
| 製造商 | 裝置製造商 |
| 型號 | 裝置的型號 |
| 序號 | 裝置的序號 |
| 韌體 | 裝置的目前韌體版本 |
| 平台 | 裝置的平台架構 |
| 處理器 | 執行裝置的處理器 |
| 已安裝的 RAM | 在裝置上安裝的 RAM 數量 |
| 中樞已啟用狀態 | 裝置的 IoT 中樞狀態屬性 |
| 建立時間 | 在解決方案中建立裝置的時間 |
| 更新時間 | 裝置上次更新屬性的時間 |
| 緯度 | 裝置的緯度位置 |
| 經度 | 裝置的經度位置 |

模擬器會以範例值在模擬裝置中植入這些屬性。模擬器每次初始化模擬的裝置時，裝置會將預先定義的中繼資料發佈至 IoT 中樞。請注意，這會覆寫裝置入口網站中所做的任何中繼資料更新。


模擬的裝置可以處理從 IoT 中樞傳送的下列命令：

| 命令 | 說明 |
|------------------------|-----------------------------------------------------|
| PingDevice | 傳送 _ping_ 給裝置以檢查是否運作 |
| StartTelemetry | 傳送遙測以啟動裝置 |
| StopTelemetry | 傳送遙測以停止裝置 |
| ChangeSetPointTemp | 變更設定點值，在其周圍會產生隨機資料 |
| DiagnosticTelemetry | 觸發裝置模擬器以傳送其他遙測值 (externalTemp) |
| ChangeDeviceState | 變更裝置的擴充狀態屬性，並從裝置傳送裝置資訊訊息 |


裝置命令通知是透過 IoT 中樞提供。


### Azure 串流分析工作


**作業 1：裝置資訊**會篩選來自傳入訊息串流的裝置資訊訊息，並將它們傳送到事件中樞端點。裝置會在啟動時傳送裝置資訊將訊息，並且回應 **SendDeviceInfo** 命令。此作業會使用下列查詢定義：

```
SELECT * FROM DeviceDataStream Partition By PartitionId WHERE  ObjectType = 'DeviceInfo'
```

**作業 2：規則**會針對每一裝置臨界值評估傳入氣溫和溼度遙測值。臨界值是在解決方案中所包含的規則編輯器中設定。每個裝置/值組是依據時間戳記儲存在 blob 中，會讀取到串流分析做為**參考資料**。工作會針對裝置的設定臨界值比較任何非空白值。如果超過 '>' 條件，工作將會輸出**警示**事件，表示已超過臨界值，並且提供裝置、值和時間戳記值。此作業會使用下列查詢定義：

```
WITH AlarmsData AS 
(
SELECT
     Stream.DeviceID,
     'Temperature' as ReadingType,
     Stream.Temperature as Reading,
     Ref.Temperature as Threshold,
     Ref.TemperatureRuleOutput as RuleOutput,
     Stream.EventEnqueuedUtcTime AS [Time]
FROM IoTTelemetryStream Stream
JOIN DeviceRulesBlob Ref ON Stream.DeviceID = Ref.DeviceID
WHERE
     Ref.Temperature IS NOT null AND Stream.Temperature > Ref.Temperature

UNION ALL

SELECT
     Stream.DeviceID,
     'Humidity' as ReadingType,
     Stream.Humidity as Reading,
     Ref.Humidity as Threshold,
     Ref.HumidityRuleOutput as RuleOutput,
     Stream.EventEnqueuedUtcTime AS [Time]
FROM IoTTelemetryStream Stream
JOIN DeviceRulesBlob Ref ON Stream.DeviceID = Ref.DeviceID
WHERE
     Ref.Humidity IS NOT null AND Stream.Humidity > Ref.Humidity
)

SELECT *
INTO DeviceRulesMonitoring
FROM AlarmsData

SELECT *
INTO DeviceRulesHub
FROM AlarmsData
```

** 作業 3：遙測**會以兩種方法來操作傳入裝置遙測串流。第一種方法會將裝置的所有遙測訊息都傳送至持續性 blob 儲存體。第二種方法會透過五分鐘滑動視窗計算平均、最小和最大溼度值。這項資料也會傳送到 blob 儲存體。此作業會使用下列查詢定義：

```
WITH 
    [StreamData]
AS (
    SELECT
        *
    FROM 
      [IoTHubStream] 
    WHERE
        [ObjectType] IS NULL -- Filter out device info and command responses
) 

SELECT
    *
INTO
    [Telemetry]
FROM
    [StreamData]

SELECT
    DeviceId,
    AVG (Humidity) AS [AverageHumidity], 
    MIN(Humidity) AS [MinimumHumidity], 
    MAX(Humidity) AS [MaxHumidity], 
    5.0 AS TimeframeMinutes 
INTO
    [TelemetrySummary]
FROM
    [StreamData]
WHERE
    [Humidity] IS NOT NULL
GROUP BY
    DeviceId, 
    SlidingWindow (mi, 5)
```

### 事件處理器

**事件處理器**會處理裝置資訊訊息和命令回應。它會使用：

- 具有目前裝置資訊的裝置資訊訊息以更新裝置登錄 (儲存於 DocumentDB 資料庫)。
- 命令回應訊息以更新裝置命令歷程記錄 (儲存於 DocumentDB 資料庫)。

## 讓我們開始

本章節將逐步解說解決方案的元件、說明預定的使用情況，並提供範例。

### 遠端監視儀表板
Web 應用程式中的此頁面會使用 PowerBI javascript 控制項 (請參閱 [PowerBI 視覺效果儲存機制](https://www.github.com/Microsoft/PowerBI-visuals)) 以視覺化方式呈現 blob 儲存體中串流分析工作的輸出資料。


### 裝置系統管理入口網站

此 Web 應用程式可讓您：

- 佈建新的裝置，這樣會設定唯一裝置識別碼並產生驗證金鑰。
- 管理裝置屬性，其中包括檢視現有屬性和使用新的屬性更新。
- 將命令傳送至裝置。
- 檢視裝置的命令歷程記錄。

### 觀察雲端解決方案的行為
您可以檢視已佈建的資源，方法是移至 [Azure 入口網站](https://portal.azure.com)並瀏覽至具有您指定之解決方案名稱的資源群組。

![](media/iot-suite-remote-monitoring-sample-walkthrough/azureportal_01.png)

當您第一次執行範例時，有四個預先設定、模擬的裝置：

![](media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_01.png)

您可以使用「裝置系統管理入口網站」加入新的模擬的裝置：

![](media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_02.png)

一開始在裝置系統管理入口網站中新裝置的狀態是 [暫止]：

![](media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_03.png)

應用程式已完成部署模擬的裝置時，您會看到裝置的狀態在裝置系統管理入口網站中變更為 [執行中]，如下列螢幕擷取畫面所示。**DeviceInfo** 串流分析工作會將裝置狀態資訊從裝置傳送至裝置系統管理入口網站。

![](media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_04.png)

使用解決方案入口網站可以傳送如 **ChangeSetPointTemp** 的命令給裝置：

![](media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_05.png)

當裝置報告它已成功執行命令時，狀態會變更為 [成功]：

![](media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_06.png)

您可以使用解決方案入口網站搜尋具有特定特性的裝置，例如型號：

![](media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_07.png)

您可以停用裝置，並且在已停用之後移除：

![](media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_08.png)

<!---HONumber=AcomDC_0309_2016-->