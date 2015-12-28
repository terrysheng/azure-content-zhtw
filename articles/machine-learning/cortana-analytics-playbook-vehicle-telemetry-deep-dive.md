<properties 
	pageTitle="車輛遙測分析方案腳本：深入探索方案 | Microsoft Azure" 
	description="使用 Cortana Analytics 具備的強大功能，取得關於車輛健全狀態與駕駛習慣的即時預測情資。" 
	services="machine-learning" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/02/2015" 
	ms.author="bradsev" />


# 車輛遙測分析方案腳本：深入探索方案

此**功能表**連結至此腳本的章節：

[AZURE.INCLUDE [cap-vehicle-telemetry-playbook-selector](../../includes/cap-vehicle-telemetry-playbook-selector.md)]

本節深入「方案架構」所述每個階段的細節，並提供自訂的指示和方針。

## 資料來源

此方案使用兩種不同的資料來源：

- **模擬車輛訊號和診斷資料集**和 
- **車輛類別目錄**

此方案包含車輛遠程資訊服務模擬器。它會在指定時間點發出對應於車輛狀態與駕駛模式的診斷資訊和訊號。按一下[車輛遠程資訊服務模擬器](http://go.microsoft.com/fwlink/?LinkId=717075)下載**車輛遠程資訊服務模擬器 Visual Studio 方案**，以根據您的需求來自訂。車輛類別目錄包含一個含有 VIN 至車型對應的參考資料集。

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig2-vehicle-telematics-simulator.png)

*圖 2 – 車輛遠程資訊服務模擬器*

這是 JSON 格式的資料集，包含以下結構描述。

欄 | 說明 | 值   
 ------- | ----------- | ---------  
VIN | 隨機產生的車輛識別號碼 | 這取自於一份含有 10,000 個隨機產生車輛識別號碼的主要清單
Outside temperature | 車輛行駛時的外部溫度 | 從 0-100 隨機產生的數字
Engine temperature | 車輛的引擎溫度 | 從 0-500 隨機產生的數字
速度 | 車輛行駛時的引擎速度 | 從 0-100 隨機產生的數字
Fuel | 車輛的燃油量 | 從 0-100 隨機產生的數字 (表示燃油量百分比)
EngineOil | 車輛的機油量 | 從 0-100 隨機產生的數字 (表示機油量百分比)
Tirepressure | 車輛的胎壓 | 從 0-50 隨機產生的數字 (表示胎壓位準百分比)
Odometer | 車輛的里程表讀數 | 從 0-200000 隨機產生的數字
Accelerator\_pedal\_position | 車輛的油門踏板位置 | 從 0-100 隨機產生的數字 (表示油門位準百分比)
Parking\_brake\_status | 指出車輛是否已停車 | True 或 False
Headlamp\_status | 指出大燈是否打開 | True 或 False
Brake\_pedal\_status | 指出是否踩下剎車踏板 | True 或 False
Transmission\_gear\_position | 車輛的傳動齒輪位置 | 狀態：first、second、third、fourth、fifth、sixth、seventh、eighth
Ignition\_status | 指出車輛在行駛中或已停車 | True 或 False
Windshield\_wiper\_status | 指出擋風玻璃雨刷是否啟動 | True 或 False
ABS | 指出 ABS 是否發揮作用 | True 或 False
Timestamp | 建立資料點時的時間戳記 | 日期
City | 車輛的位置 | 此方案中有 4 個城市：Bellevue、Redmond、Sammamish、Seattle


車型參考資料集包含 VIN 至車型的對應。

VIN | 模型 |
--------------|------------------
FHL3O1SA4IEHB4WU1 | 轎車 |
8J0U8XCPRGW4Z3NQE | 混合式 |
WORG68Z2PLTNZDBI7 | 家庭房車 |
JTHMYHQTEPP4WBMRN | 轎車 |
W9FTHG27LZN1YWO0Y | 混合式 |
MHTP9N792PHK08WJM | 家庭房車 |
EI4QXI2AXVQQING4I | 轎車 |
5KKR2VB4WHQH97PF8 | 混合式 |
W9NSZ423XZHAONYXB | 家庭房車 |
26WJSGHX4MA5ROHNL | 敞篷車 |
GHLUB6ONKMOSI7E77 | 旅行車 |
9C2RHVRVLMEJDBXLP | 小型車 |
BRNHVMZOUJ6EOCP32 | 小型 SUV |
VCYVW0WUZNBTM594J | 跑車 |
HNVCE6YFZSA5M82NY | 中型 SUV |
4R30FOR7NUOBL05GJ | 旅行車 |
WYNIIY42VKV6OQS1J | 大型 SUV |
8Y5QKG27QET1RBK7I | 大型 SUV |
DF6OX2WSRA6511BVG | 兩人座轎車 |
Z2EOZWZBXAEW3E60T | 轎車 |
M4TV6IEALD5QDS3IR | 混合式 |
VHRA1Y2TGTA84F00H | 家庭房車 |
R0JAUHT1L1R3BIKI0 | 轎車 |
9230C202Z60XX84AU | 混合式 |
T8DNDN5UDCWL7M72H | 家庭房車 |
4WPYRUZII5YV7YA42 | 轎車 |
D1ZVY26UV2BFGHZNO | 混合式 |
XUF99EW9OIQOMV7Q7 | 家庭房車
8OMCL3LGI7XNCC21U | 敞篷車 |
……. | |


### 產生模擬資料
1.	按一下 [車輛遠程資訊服務模擬器] 節點右上方的箭號，下載資料模擬器封裝。將檔案儲存在本機電腦上並解壓縮。![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig3-vehicle-telemetry-blueprint.png) *圖 3 – 車輛遙測分析方案藍圖*

2.	在本機電腦上，移至您解壓縮「車輛遠程資訊服務模擬器」封裝的資料夾。![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig4-vehicle-telematics-simulator-folder.png) *圖 4 – 車輛遠程資訊服務模擬器資料夾*

3.	執行應用程式 **CarEventGenerator.exe**。

### 參考
[車輛遠程資訊服務模擬器 Visual Studio 方案](http://go.microsoft.com/fwlink/?LinkId=717075) [Azure 事件中樞](http://azure.microsoft.com/services/event-hubs/) [Azure Data Factory](https://azure.microsoft.com/documentation/learning-paths/data-factory/)


## 擷取
利用 Azure 事件中樞、串流分析和 Data Factory 的組合，以擷取車輛訊號和診斷事件及即時和批次分析。方案部署時會建立和設定所有這些元件。

### 即時分析
使用事件中樞 SDK 將「車輛遠程資訊服務模擬器」所產生的事件發佈至事件中樞。串流分析作業會從事件中樞擷取這些事件，並即時處理資料，以分析車輛健全狀況。

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig5-vehicle-telematics-event-hub-dashboard.png)

*圖 5 - 事件中樞儀表板*

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig6-vehicle-telematics-stream-analytics-job-processing-data.png)

*圖 6 - 處理資料的串流分析作業*

串流分析作業會從事件中樞擷取資料、結合參考資料將車輛 VIN 對應至相符的車型，也會將資料保存到 Azure Blob 儲存體進行充分的批次分析。下列串流分析查詢用來將資料保存到 Azure Blob 儲存體。

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig7-vehicle-telematics-stream-analytics-job-query-for-data-ingestion.png)

*圖 7 - 用於擷取資料的串流分析作業查詢*

### 批次分析
我們也會產生另一批模擬車輛訊號和診斷資料集，以進行更多樣的批次分析。這是為了確保有良好的代表性資料量可供批次處理。基於此目的，我們在 Azure Data Factory 工作流程中使用名為 'PrepareSampleDataPipeline' 的管線，以產生一年份的模擬車輛訊號和診斷資料集。按一下 [Data Factory 自訂活動](http://go.microsoft.com/fwlink/?LinkId=717077)下載 Data Factory 自訂 DotNet 活動 Visual Studio 方案，以根據您的需求來自訂。

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig8-vehicle-telematics-prepare-sample-data-for-batch-processing.png)

*圖 8 - 準備批次處理工作流程的範例資料*

管線包含自訂的 ADF.Net 活動，顯示如下：

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig9-vehicle-telematics-prepare-sample-data-pipeline.png)

*圖 9 - PrepareSampleDataPipeline*

一旦成功執行管線，而且 'RawCarEventsTable' 資料集標示為「就緒」，就會產生一年份的模擬車輛訊號和診斷資料。您在儲存體帳戶的 'connectedcar' 容器下會看到已建立下列資料夾和檔案

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig10-vehicle-telematics-prepare-sample-data-pipeline-output.png)

*圖 10 - PrepareSampleDataPipeline 輸出*

### 參考
[用於擷取串流的 Azure 事件中樞 SDK](event-hubs-csharp-ephcs-getstarted.md) [Azure Data Factory 資料移動功能](data-factory-data-movement-activities.md) [Azure Data Factory DotNet 活動](data-factory-use-custom-activities.md) [用於準備範例資料的 Azure Data Factory DotNet 活動 Visual Studio 方案](http://go.microsoft.com/fwlink/?LinkId=717077)


## 準備
>[AZURE.ALERT]方案中的此步驟僅適用於批次處理。

在資料準備步驟中，原始的半結構化車輛訊號和診斷資料集分割成 YEAR/MONTH 格式，以利於提高查詢效率和使用可擴充的長期儲存體 (也就是說，能夠在第一個 blob 帳戶填滿時切換到下一個 blob 帳戶)。輸出資料 (標示為 *PartitionedCarEventsTable*) 應該在客戶的「資料湖」中以基本/「最原始」的資料形式長期保存。通常會捨棄此管線的輸入資料，因為輸出資料會完整無缺地呈現輸入 - 只是方便後續使用而儲存 (分割)。

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig11-vehicle-telematics-partition-car-events-workflow.png)

*圖 11 – 分割汽車事件工作流程*

'PartitionCarEventsPipeline' 中使用 Hive HDInsight 活動分割原始資料。步驟 1 所產生的範例資料依 YEAR/MONTH 分割，以產生對應於一年中每個月的車輛訊號和診斷資料分割區 (總共 12 個分割區)。

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig12-vehicle-telematics-partition-car-events-pipeline.png)

*圖 12 - PartitionCarEventsPipeline*

如下所示名為 ‘partitioncarevents.hql’ 的 Hive 指令碼用於分割，位於已下載的 zip 的 ‘\\demo\\src\\connectedcar\\scripts’ 資料夾中。

	SET hive.exec.dynamic.partition=true;
	SET hive.exec.dynamic.partition.mode = nonstrict;
	set hive.cli.print.header=true;

	DROP TABLE IF EXISTS RawCarEvents; 
	CREATE EXTERNAL TABLE RawCarEvents 
	(
            	vin								string,
				model							string,
				timestamp						string,
				outsidetemperature				string,
				enginetemperature				string,
				speed							string,
				fuel							string,
				engineoil						string,
				tirepressure					string,
				odometer						string,
				city							string,
				accelerator_pedal_position		string,
				parking_brake_status			string,
				headlamp_status					string,
				brake_pedal_status				string,
				transmission_gear_position		string,
				ignition_status					string,
				windshield_wiper_status			string,
				abs  							string,
				gendate							string
                
	) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:RAWINPUT}'; 

	DROP TABLE IF EXISTS PartitionedCarEvents; 
	CREATE EXTERNAL TABLE PartitionedCarEvents 
	(
            	vin								string,
				model							string,
				timestamp						string,
				outsidetemperature				string,
				enginetemperature				string,
				speed							string,
				fuel							string,
				engineoil						string,
				tirepressure					string,
				odometer						string,
				city							string,
				accelerator_pedal_position		string,
				parking_brake_status			string,
				headlamp_status					string,
				brake_pedal_status				string,
				transmission_gear_position		string,
				ignition_status					string,
				windshield_wiper_status			string,
				abs  							string,
				gendate							string
	) partitioned by (YearNo int, MonthNo int) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:PARTITIONEDOUTPUT}';

	DROP TABLE IF EXISTS Stage_RawCarEvents; 
	CREATE TABLE IF NOT EXISTS Stage_RawCarEvents 
	(
            	vin								string,
				model							string,
				timestamp						string,
				outsidetemperature				string,
				enginetemperature				string,
				speed							string,
				fuel							string,
				engineoil						string,
				tirepressure					string,
				odometer						string,
				city							string,
				accelerator_pedal_position		string,
				parking_brake_status			string,
				headlamp_status					string,
				brake_pedal_status				string,
				transmission_gear_position		string,
				ignition_status					string,
				windshield_wiper_status			string,
				abs  							string,
				gendate							string,
				YearNo 							int,
				MonthNo 						int) 
	ROW FORMAT delimited fields terminated by ',' LINES TERMINATED BY '10';

	INSERT OVERWRITE TABLE Stage_RawCarEvents
	SELECT
		vin,			
		model,
		timestamp,
		outsidetemperature,
		enginetemperature,
		speed,
		fuel,
		engineoil,
		tirepressure,
		odometer,
		city,
		accelerator_pedal_position,
		parking_brake_status,
		headlamp_status,
		brake_pedal_status,
		transmission_gear_position,
		ignition_status,
		windshield_wiper_status,
		abs,
		gendate,
		Year(gendate),
		Month(gendate)

	FROM RawCarEvents WHERE Year(gendate) = ${hiveconf:Year} AND Month(gendate) = ${hiveconf:Month}; 

	INSERT OVERWRITE TABLE PartitionedCarEvents PARTITION(YearNo, MonthNo) 
	SELECT
		vin,			
		model,
		timestamp,
		outsidetemperature,
		enginetemperature,
		speed,
		fuel,
		engineoil,
		tirepressure,
		odometer,
		city,
		accelerator_pedal_position,
		parking_brake_status,
		headlamp_status,
		brake_pedal_status,
		transmission_gear_position,
		ignition_status,
		windshield_wiper_status,
		abs,
		gendate,
		YearNo,
		MonthNo
	FROM Stage_RawCarEvents WHERE YearNo = ${hiveconf:Year} AND MonthNo = ${hiveconf:Month};

*圖 13 - PartitionConnectedCarEvents Hive 指令碼*

一旦成功執行管線，您在儲存體帳戶的 'connectedcar' 容器下會看到已產生下列分割區。

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig14-vehicle-telematics-partitioned-output.png)

*圖 14 - 分割的輸出*

資料現在已最佳化、更容易管理，可供進一步處理以更深入了解批次。

## 資料分析

本節中，您會看到我們如何使用 Azure 串流分析、Azure Machine Learning、Azure Data Factory 和 Azure HDInsight 的組合，對車輛健全狀況和駕駛習慣進行多樣化的進階分析。本節有 3 個小節：

1.	**機器學習服務**：此小節包含異常偵測實驗的相關資訊，我們在此方案中利用此實驗來預測需要維修的車輛和由於安全性問題而需要召回的車輛
2.	**即時分析**：此小節包含使用串流分析查詢語言進行即時分析，以及使用自訂應用程式即時進行機器學習服務實驗的相關資訊
3.	**批次分析**：此小節包含使用 Azure Data Factory 所進行的 Azure HDInsight 和 Azure Machine Learning 來轉換和處理批次資料的相關資訊

### 機器學習服務

這裡的目標是根據特定的健全狀況統計資料，預測需要維修或召回的車輛。我們有下列假設

- 如果下列三個條件之一成立，則車輛需要**維修**：
	- 胎壓偏低
	- 機油量偏低
	- 引擎溫度偏高

- 如果下列其中一個條件成立，則車輛可能有**安全性問題**而需要**召回**：
	- 引擎溫度偏高，但外部溫度偏低
	- 引擎溫度偏低，但外部溫度偏高

根據上述需求，我們建立兩個不同的模型來偵測異常，其中一個用於車輛維修偵測，另一個用於車輛召回偵測。在這兩個模型中，使用內建的主成分分析 (PCA) 演算法來偵測異常。

**維修偵測模型** 在維修偵測模型中，如果三個指標的其中一個符合其各自的條件，此模型會報告異常 - 胎壓、機油或引擎溫度。因此，我們在建置模型時只需要考慮這三個變數。在 Azure Machine Learning 中的實驗上，我們先使用**專案資料行**模組來擷取這三個變數。接下來，我們使用以 PCA 為基礎的異常偵測模組來建立異常偵測模型。

主成分分析 (PCA) 是已建立的機器學習服務技術，可運用於特徵選取、分類及異常偵測。PCA 會將一組包含可能相關變數的案例，轉換成一組稱為主成分的值。以 PCA 為基礎模型化的主要概念是將專案資料投射至較低維度的空間，以更輕鬆識別特徵和異常。
 
在異常偵測的案例中，針對每個新的輸入，異常偵測器會先計算它在特徵向量上的投影，然後計算正規化重構錯誤。這個正規化錯誤就是異常分數。錯誤越高，表示實例越異常。

在維修偵測問題中，每一筆記錄可視為由胎壓、機油和引擎溫度座標所定義的三維空間中的一個點。為了擷取這些異常，我們可以使用 PCA 將三維空間中的原始資料投射到二維空間。因此，我們將 PCA 中使用的「成分數目」參數設為 2。在套用以 PCA 為基礎的異常偵測時，這個參數扮演重要的角色。使用 PCA 投射資料之後，我們可以更輕鬆識別這些異常。

**召回異常偵測模型** 在召回異常偵測模型中，我們以類似方式使用「專案資料行」和以 PCA 為基礎的異常偵測模組。具體來說，我們先使用**專案資料行**模組擷取三個變數 - 引擎溫度、外部溫度和速度。因為引擎溫度通常與速度相互關聯，所以我們也包含速度變數。接下來，我們使用以 PCA 為基礎的異常偵測模組，將三維空間的資料投射到二維空間。當引擎溫度和外部溫度呈現高度負相關時，就滿足召回準則，因此需要召回車輛。由於使用以 PCA 為基礎的異常偵測演算法，我們可以在執行 PCA 之後擷取異常。

請注意，在訓練其中一種模型時，我們必須使用不需要維修或召回的正常資料做為輸入資料，以訓練以 PCA 為基礎的異常偵測模型。在評分實驗中，我們使用已訓練的異常偵測模型，偵測車輛是否需要維修或召回。


### 即時分析

下列串流分析 SQL 查詢用來取得所有重要車輛參數的平均值，例如車輛速度、燃油量、引擎溫度、里程表讀數、胎壓、機油量等等，以此來偵測異常、發出警示、判斷在特定區域行駛的車輛的整體健全狀況，並將它與人口統計資料相互關聯。

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig15-vehicle-telematics-stream-analytics-query-for-real-time-processing.png)

圖 15 – 用於即時處理的串流分析查詢

所有平均值都以 3 秒的 TumblingWindow 來計算。因為我們需要非重疊和連續的時間間隔，所以在此案例中使用 TubmlingWindow。

若要深入了解 Azure 串流分析中的所有「時間範圍」功能，請按一下[時間範圍 (Azure 串流分析)](https://msdn.microsoft.com/library/azure/dn835019.aspx)。

**即時預測**

此方案包含一個應用程式來即時操作機器學習模型。方案部署時會建立和設定這個稱為 “RealTimeDashboardApp” 的應用程式。此應用程式會執行下列作業：

1.	接聽由串流分析以連續模式發佈事件的事件中樞執行個體。![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig16-vehicle-telematics-stream-analytics-query-for-publishing.png)*圖 16 – 將資料發佈至輸出事件中樞執行個體的串流分析查詢* 

2.	對於此應用程式收到的每個事件：

	- 使用機器學習服務要求回應評分 (RRS) 端點來處理資料。部署時會自動發佈 RRS 端點。
	- RRS 輸出會透過推送 API 發佈至 PowerBI 資料集。

如果您想要整合企業營運應用程式與警示、通知、傳訊等案例的即時分析流程，此模式也適用。

按一下 [RealtimeDashboardApp 下載](http://go.microsoft.com/fwlink/?LinkId=717078)下載 RealtimeDashboardApp Visual Studio 方案來自訂。

****執行即時儀表板應用程式 **

1.	按一下圖表檢視上的 PowerBI 節點，然後按一下屬性窗格上的 [下載即時儀表板應用程式] 連結。![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig17-vehicle-telematics-powerbi-dashboard-setup.png) *圖 17 – PowerBI 儀表板安裝指示*
2.	擷取並儲存在本機 ![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig18-vehicle-telematics-realtimedashboardapp-folder.png) *圖 18 – RealtimeDashboardApp 資料夾*
3.	執行應用程式 RealtimeDashboardApp.exe
4.	提供有效的 Power BI 認證、登入，然後按一下 [接受] ![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig19a-vehicle-telematics-realtimedashboardapp-sign-in-to-powerbi.png) ![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig19b-vehicle-telematics-realtimedashboardapp-sign-in-to-powerbi.png) 

*圖 19 – RealtimeDashboardApp：登入 PowerBI*

>[AZURE.NOTE]附註：如果您想要排清 PowerBI 資料集，請指定 ‘flushdata’ 來執行 RealtimeDashboardApp：

	RealtimeDashboardApp.exe -flushdata

### 批次分析

這裡的目標是展示 Contoso Motors 如何利用 Azure 計算能力來處理巨量資料，以深入了解駕駛模式、使用行為和車輛健全狀況，進而達成下列目標：

- 改善客戶體驗，更輕鬆了解駕駛習慣和省油的駕駛行為
- 主動了解客戶及其駕駛模式，以制定商務決策並提供最佳的產品與服務

在此方案中，我們以達成下列度量為目標：

1.	**激進駕駛行為** 識別車型、位置、駕駛狀況和一年中的時間等趨勢，以深入了解激進駕駛模式，供 Contoso Motors 用於行銷活動、推出新的人性化功能，以及基於使用方式的保險。
2.	**省油駕駛行為** 識別車型、位置、駕駛狀況和行駛時間的趨勢，以深入了解省油駕駛模式，供 Contoso Motors 用於行銷活動、推出新的功能和主動回報駕駛人，進而養成節省成本和符合環保的駕駛習慣。 
3.	**召回車型** 進行異常偵測機器學習服務實驗，以識別需要召回的車型

讓我們看一看上述每個度量的詳細資料，


**激進駕駛模式**

在名為 'AggresiveDrivingPatternPipeline' 的管線中，使用 Hive 處理分割的車輛訊號和診斷資料，以判斷呈現激進駕駛模式的車型、位置、車輛和駕駛狀況等。

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig20-vehicle-telematics-aggressive-driving-pattern.png) *圖 20 – 激進駕駛模式工作流程*

名為 ‘aggresivedriving.hql’ 用於分析激進駕駛狀況模式的 Hive 指令碼位於已下載的 zip 的 ‘\\demo\\src\\connectedcar\\scripts’ 資料夾中。

	DROP TABLE IF EXISTS PartitionedCarEvents; 
	CREATE EXTERNAL TABLE PartitionedCarEvents
	(
            	vin								string,
				model							string,
				timestamp						string,
				outsidetemperature				string,
				enginetemperature				string,
				speed							string,
				fuel							string,
				engineoil						string,
				tirepressure					string,
				odometer						string,
				city							string,
				accelerator_pedal_position		string,
				parking_brake_status			string,
				headlamp_status					string,
				brake_pedal_status				string,
				transmission_gear_position		string,
				ignition_status					string,
				windshield_wiper_status			string,
				abs  							string,
				gendate							string
                                
	) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:PARTITIONEDINPUT}';

	DROP TABLE IF EXISTS CarEventsAggresive; 
	CREATE EXTERNAL TABLE CarEventsAggresive
	(
               	vin 						string, 
				model						string,
                timestamp					string,
				city						string,
				speed 			 			string,
				transmission_gear_position	string,
				brake_pedal_status			string,
				Year						string,
				Month						string
                                
	) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:AGGRESIVEOUTPUT}';



	INSERT OVERWRITE TABLE CarEventsAggresive
	select
	vin,
	model,
	timestamp,
	city,
	speed,
	transmission_gear_position,
	brake_pedal_status,
	"${hiveconf:Year}" as Year,
	"${hiveconf:Month}" as Month
	from PartitionedCarEvents
	where transmission_gear_position IN ('fourth', 'fifth', 'sixth', 'seventh', 'eight') AND brake_pedal_status = '1' AND speed >= '50'

*圖 21 – 激進駕駛模式 Hive 查詢*

它使用車輛的傳輸齒輪位置、剎車踏板狀態和速度的組合，根據高速狀態下的剎車模式來偵測魯莽/激進駕駛行為。

一旦成功執行管線，您在儲存體帳戶的 'connectedcar' 容器下會看到已產生下列分割區。

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig22-vehicle-telematics-aggressive-driving-pattern-output.png)

*圖 22 – AggressiveDrivingPatternPipeline 輸出*


**省油駕駛模式**

在名為 'FuelEfficientDrivingPatternPipeline' 的管線中，使用 Hive 處理分割的車輛訊號和診斷資料，以判斷呈現省油駕駛模式的車型、位置、車輛和駕駛狀況等。

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig23-vehicle-telematics-fuel-efficient-driving-pattern.png)

*圖 23 – 省油駕駛模式工作流程*

名為 ‘fuelefficientdriving.hql’ 用於分析激進駕駛狀況模式的 Hive 指令碼位於已下載的 zip 的 ‘\\demo\\src\\connectedcar\\scripts’ 資料夾中。

	DROP TABLE IF EXISTS PartitionedCarEvents; 
	CREATE EXTERNAL TABLE PartitionedCarEvents
	(
            	vin								string,
				model							string,
				timestamp						string,
				outsidetemperature				string,
				enginetemperature				string,
				speed							string,
				fuel							string,
				engineoil						string,
				tirepressure					string,
				odometer						string,
				city							string,
				accelerator_pedal_position		string,
				parking_brake_status			string,
				headlamp_status					string,
				brake_pedal_status				string,
				transmission_gear_position		string,
				ignition_status					string,
				windshield_wiper_status			string,
				abs  							string,
				gendate							string
                                
	) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:PARTITIONEDINPUT}';

	DROP TABLE IF EXISTS FuelEfficientDriving; 
	CREATE EXTERNAL TABLE FuelEfficientDriving
	(
               	vin 						string, 
				model						string,
               	city						string,
				speed 			 			string,
				transmission_gear_position	string,                
				brake_pedal_status			string,            
				accelerator_pedal_position	string,                             
				Year						string,
				Month						string
                                
	) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:FUELEFFICIENTOUTPUT}';



	INSERT OVERWRITE TABLE FuelEfficientDriving
	select
	vin,
	model,
	city,
	speed,
	transmission_gear_position,
	brake_pedal_status,
	accelerator_pedal_position,
	"${hiveconf:Year}" as Year,
	"${hiveconf:Month}" as Month
	from PartitionedCarEvents
	where transmission_gear_position IN ('fourth', 'fifth', 'sixth', 'seventh', 'eight') AND parking_brake_status = '0' AND brake_pedal_status = '0' AND speed <= '60' AND accelerator_pedal_position >= '50'


*圖 24 – 省油駕駛模式 Hive 查詢*

它使用車輛的傳輸齒輪位置、剎車踏板狀態、速度和油門踏板位置的組合，根據油門、剎車和速度模式來偵測省油駕駛行為。

一旦成功執行管線，您在儲存體帳戶的 'connectedcar' 容器下會看到已產生下列分割區。

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig25-vehicle-telematics-fuel-efficient-driving-pattern-output.png)

*圖 25 – FuelEfficientDrivingPatternPipeline 輸出*


**召回預測**

方案部署時會將機器學習服務實驗佈建並發佈為 Web 服務。此工作流程中利用的批次評分端點會註冊為 Data Factory 連結服務，並使用 Data Factory 批次評分活動來運作。

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig26-vehicle-telematics-machine-learning-endpoint.png)

*圖 26 – 在 Data Factory 中註冊為連結服務的機器學習服務端點*

DetectAnomalyPipeline 中使用已註冊的連結服務，透過異常偵測模型給資料評分。

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig27-vehicle-telematics-aml-batch-scoring.png)

*圖 27 – Data Factory 中的 Azure Machine Learning 批次評分活動*

此管線中執行幾個步驟來準備資料，以利用於使用批次評分 Web 服務來操作。

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig28-vehicle-telematics-pipeline-predicting-recalls.png)

*圖 28 – 用於預測需要召回車輛的 DetectAnomalyPipeline*

一旦完成評分，就使用 HDInsight 活動來處理及彙總資料，並將機率分數為 0.60 或更高的車型分類為異常。

	DROP TABLE IF EXISTS CarEventsAnomaly; 
	CREATE EXTERNAL TABLE CarEventsAnomaly 
	(
            	vin							string,
				model						string,
				gendate						string,
				outsidetemperature			string,
				enginetemperature			string,
				speed						string,
				fuel						string,
				engineoil					string,
				tirepressure				string,
				odometer					string,
				city						string,
				accelerator_pedal_position	string,
				parking_brake_status		string,
				headlamp_status				string,
				brake_pedal_status			string,
				transmission_gear_position	string,
				ignition_status				string,
				windshield_wiper_status		string,
				abs  						string,
				maintenanceLabel			string,
				maintenanceProbability		string,
				RecallLabel					string,
				RecallProbability			string
                                
	) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:ANOMALYOUTPUT}';

	DROP TABLE IF EXISTS RecallModel; 
	CREATE EXTERNAL TABLE RecallModel 
	(

				vin							string,
				model						string,
				city						string,
				outsidetemperature			string,
				enginetemperature			string,
				speed						string,
            	Year						string,
				Month						string				
                                
	) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:RECALLMODELOUTPUT}';

	INSERT OVERWRITE TABLE RecallModel
	select
	vin,
	model,
	city,
	outsidetemperature,
	enginetemperature,
	speed,
	"${hiveconf:Year}" as Year,
	"${hiveconf:Month}" as Month
	from CarEventsAnomaly
	where RecallLabel = '1' AND RecallProbability >= '0.60'

*圖 29 – 召回彙總 Hive 查詢*

一旦成功執行管線，您在儲存體帳戶的 'connectedcar' 容器下會看到已產生下列分割區。

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig30-vehicle-telematics-detect-anamoly-pipeline-output.png)

*圖 30 – DetectAnomalyPipeline 輸出*


## 發佈

### 即時分析

串流分析作業的其中一個查詢會將事件發佈至輸出事件中樞執行個體。

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig31-vehicle-telematics-stream-analytics-job-publishes-output-event-hub.png)

*圖 31 – 串流分析作業發佈至輸出事件中樞執行個體*

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig32-vehicle-telematics-stream-analytics-query-publish-output-event-hub.png)

*圖 32 – 用於發佈至輸出事件中樞執行個體的串流分析查詢*

此方案包含的 RealTimeDashboardApp 會取用此事件串流。此應用程式利用機器學習服務的要求-回應 Web 服務進行即時評分，並將產生的資料發佈給 PowerBI 資料集取用。

### 批次分析

批次和即時處理的結果會發佈給 Azure SQL Database 資料表取用。安裝指令碼會自動建立 Azure SQL Server、Database 和資料表。

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig33-vehicle-telematics-batch-processing-results-copy-to-data-mart.png)

*圖 33 – 批次處理結果複製到資料超市工作流程*

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig34-vehicle-telematics-stream-analytics-job-publishes-to-data-mart.png)

*圖 34 – 串流分析作業發佈至資料超市*

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig35-vehicle-telematics-data-mart-setting-in-stream-analytics-job.png)

*圖 35 – 串流分析作業中的資料超市設定*


## 取用

Power BI 給此方案一個豐富的儀表板來提供即時資料和預測性分析視覺效果。

如需有關設定 PowerBI 報告和儀表板的詳細指示，請按一下這裡。最終的儀表板顯示如下：

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig36-vehicle-telematics-powerbi-dashboard.png)

*圖 36 - PowerBI 儀表板*

## 摘要

本文件包含車輛遙測資料分析方案的詳細探討。這以預測和動作示範即時和批次分析的 Lambda 架構模式。此模式適用於需要最忙碌路徑 (即時) 和不忙碌路徑 (批次) 分析的各種使用案例。

<!---HONumber=AcomDC_1217_2015-->