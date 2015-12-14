<properties 
	pageTitle="車輛遙測分析方案範本 PowerBI 儀表板安裝指示 | Microsoft Azure" 
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


# 車輛遙測分析方案範本 PowerBI 儀表板安裝指示

此**功能表**連結至此腳本的章節。

[AZURE.INCLUDE [cap-vehicle-telemetry-playbook-selector](../../includes/cap-vehicle-telemetry-playbook-selector.md)]


「車輛遙測分析」方案示範汽車經銷商、汽車製造商和保險公司如何運用 Cortana Analytics 功能，取得車輛健全狀態與駕駛習慣的即時預測情資，以改善客戶體驗、R&D 和行銷活動等方面。本文件包含在您的訂用帳戶中部署方案之後，如何設定 PowerBI 報告和儀表板的逐步指示。


## 必要條件
1.	瀏覽至 [https://gallery.cortanaanalytics.com/SolutionTemplate/Vehicle-Telemetry-Analytics-3](https://gallery.cortanaanalytics.com/SolutionTemplate/Vehicle-Telemetry-Analytics-3) 部署「車輛遙測分析」方案  
2.	[安裝 Microsoft Power BI Desktop](http://www.microsoft.com/download/details.aspx?id=45331)
3.	[Azure 訂用帳戶](https://azure.microsoft.com/pricing/free-trial/)。如果您沒有 Azure 訂用帳戶，請立即取得免費的 Azure 訂用帳戶
4.	Microsoft PowerBI 帳戶
	

## Cortana Analytics Suite 元件
「車輛遙測分析」方案範本會在您的訂用帳戶中部署下列 Cortana Analytics 服務。

- **事件中樞**可將數百萬計的車輛遙測事件擷取至 Azure。
- **串流分析**可取得關於車輛健全狀態的即時情資，同時將資料保存到長期儲存體，供進行更豐富的批次分析。
- **機器學習服務**可即時進行異常偵測與批次處理以取得預測情資。
- **HDInsight** 用於大規模的資料轉換
- **Data Factory** 可執行批次處理管線的協調、排程、資源管理和監控工作。

**Power BI** 給此方案一個豐富的儀表板來提供即時資料和預測性分析視覺效果。

此方案使用兩種不同的資料來源：**模擬車輛訊號和診斷資料集**和**車輛類別目錄**。

此方案包含車輛遠程資訊服務模擬器。它會在指定時間點發出對應於車輛狀態與駕駛模式的診斷資訊和訊號。

「車輛類別目錄」是包含 VIN 至車型對應的參考資料集


## PowerBI 儀表板的準備工作

### 部署

完成部署後，您應該會看到下圖，其中以「綠色」標示所有這些元件。

- 按一下綠色節點右上方的箭號，瀏覽至對應的服務，以驗證是否已成功部署所有這些元件。
- 按一下 [車輛遠程資訊服務模擬器] 節點右上方的箭號，下載資料模擬器封裝。將檔案儲存在本機電腦上並解壓縮。 

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/1-deployed-components.png)

現在，您可以使用豐富的視覺效果來設定 PowerBI 儀表板，以取得車輛健全狀況和駕駛習慣的即時預測性情資。建立所有報告和設定儀表板大約需要 45 分鐘至一小時。


### 設定 Power BI 即時儀表板

**產生模擬資料**

1. 在本機電腦上，移至您解壓縮「車輛遠程資訊服務模擬器」封裝的資料夾 

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/2-vehicle-telematics-simulator-folder.png)

2.	執行應用程式 ***CarEventGenerator.exe***。
3.	它會在指定時間點發出對應於車輛狀態與駕駛模式的診斷資訊和訊號。這會發佈至您在部署時所設定的 Azure 事件中樞執行個體。

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/3-vehicle-telematics-diagnostics.png)
	 
啟動即時儀表板應用程式。方案包含一個可在 PowerBI 中產生即時儀表板的應用程式。此應用程式會接聽由串流分析以連續模式發佈事件的事件中樞執行個體。針對此應用程式收到的每一個事件，它會使用機器學習服務要求-回應評分端點來處理資料，而產生的資料集會發佈至 PowerBI 推送 API 來呈現視覺效果。若要下載應用程式：

1.	按一下圖表檢視上的 PowerBI 節點，然後按一下屬性窗格上的 [下載即時儀表板應用程式] 連結。

2.	擷取並儲存在本機

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/4-real-time-dashboard-application.png)

3.	執行應用程式 ‘RealtimeDashboardApp.exe’
4.	提供有效的 Power BI 認證、登入，然後按一下 [接受]
	
![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/5-sign-into-powerbi.png)
	
![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/6-powerbi-dashboard-permissions.png)
 
## 建立即時 Power BI 報告 

### 設定 PowerBI 報告

Power BI 中將會產生新的資料集。按一下 **ConnectedCarsRealtime** 資料集。

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/7-select-connected-cars-realtime-dataset.png)

使用 **Ctrl + s** 儲存空白報告。

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/8-save-blank-report.png)

提供報告名稱「車輛遙測分析即時 - 報告」。

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/9-provide-report-name.png)


### 建立即時報告：*行駛中的車輛*
  
按兩下 [第 1 頁]，重新命名為「行駛中的車輛」![Connected Cars - 行駛中的車輛](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4a.png)

從 [欄位] 中選取 **vin** 欄位，然後選擇「卡片」做為視覺效果類型。

建立的卡片視覺效果如下圖所示。![Connected Cars - 選取 vin](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4b.png)

按一下空白區域以加入新的視覺效果。

從欄位中選取 **City** 和 **vin**。將視覺效果變更為「地圖」。將 **vin** 拖曳到值區域。從欄位中將 **city** 拖曳到 [圖例] 區域。![Connected Cars - 卡片視覺效果](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4c.png)
  
從 [視覺效果] 中選取 [格式] 區段，按一下 [標題]，將文字變更為「行駛中的車輛 (依城市)」。![Connected Cars - 行駛中的車輛 (依城市)](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4d.png)

最終的視覺效果如下圖所示。![Connected Cars - 最終視覺效果](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4e.png)

按一下空白區域以加入新的視覺效果。

選取 **City** 和 **vin**，將視覺效果類型變更為「群組直條圖」。請確定 **City** 欄位在 [軸] 區域，**vin** 在 [值] 區域

依 [vin 的計數] 排序圖表 ![Connected Cars - vin 的計數](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4f.png)

將圖表 [標題] 變更為「行駛中的車輛 (依城市)」

按一下 [格式] 區段，然後選取 [資料色彩]，按一下 [全部顯示] 的 [開啟] ![Connected Cars - 顯示所有資料色彩](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4g.png)

按一下色彩圖示變更個別城市的色彩。![Connected Cars - 變更色彩](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4h.png)

按一下空白區域以加入新的視覺效果。

從 [視覺效果] 中選取「群組直條圖」視覺效果，將 **city** 欄位拖曳到 [軸] 區域、將 **Model** 拖曳到 [圖例] 區域、將 **vin** 拖曳到 [值] 區域。![Connected Cars - 群組直條圖](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4i.png) ![Connected Cars -轉譯](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4j.png)
  
重新排列此頁面上的所有視覺效果，如下圖所示。![Connected Cars -視覺效果](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4k.png)

### 加入新的報告：*需要維修的車輛*
  
按一下 ![新增](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4add.png) 加入新的報告，重新命名為「需要維修的車輛」

![Connected Cars - 需要維修的車輛](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4l.png)

選取 **vin** 欄位，將視覺效果類型變更為「卡片」。![Connected Cars - Vin 卡片視覺效果](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4m.png)

我們在資料集中有一個名為 **"MaintenanceLabel"** 的欄位。此欄位的值為 **"0"** 和 **"1"**。"1" 表示車輛需要維修

我們加入 [頁面層級] 篩選以顯示需要維修的車輛資料。

1. 將 **"MaintenanceLabel"** 欄位拖曳到 [頁面層級篩選]。![Connected Cars - 頁面層級篩選](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n1.png)  

2. 按一下 MaintenanceLabel 頁面層級篩選底部出現的 [基本篩選] 功能表。![Connected Cars - 基本篩選](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n2.png)

3.  將篩選值設為 **“1”** ![Connected Cars - 篩選值](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n3.png)
  
>[AZURE.NOTE]我們可以在圖表層級設定篩選，稱為「視覺化層級篩選」。它們僅適用於特定的視覺效果。頁面層級篩選適用於報告上的所有視覺效果。

按一下空白區域以加入新的視覺效果。

從 [視覺效果] 中選取「群組直條圖」 ![Connected Cars - Vind 卡片視覺效果](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4o.png) ![Connected Cars - 群組直條圖](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4p.png)

將 **Model** 欄位拖曳到 [軸] 區域，將 **Vin** 拖曳到 [值] 區域。然後，依 [vin 的計數] 排序視覺效果。將圖表 [標題] 變更為「需要維修的車輛 (依車型)」

將 **vin** 欄位拖曳到 [視覺效果] 索引標籤的 [欄位] ![欄位](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4field.png) 區段上出現的 [色彩飽和度] ![Connected Cars - 色彩飽和度](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4q.png)

在 [視覺效果] 中從 [格式] 區段變更 [資料色彩] 將 [最小值色彩] 變更為：**F2C812** 將 [最大值色彩] 變更為：**FF6300** ![Connected Cars - 色彩變更](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4r.png) ![Connected Cars - 新的視覺效果色彩](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4s.png)

按一下空白區域以加入新的視覺效果。

從 [視覺效果] 中選取「群組直條圖」，將 **vin** 欄位拖曳到 [值] 區域，將 **City** 欄位拖曳到 [軸] 區域。依 [vin 的計數] 排序圖表。將圖表 [標題] 變更為「需要維修的車輛 (依城市)」 ![Connected Cars - 需要維修的車輛 (依城市)](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4t.png)

按一下空白區域以加入新的視覺效果。

從 [視覺效果] 中選取「多列卡片」視覺效果，將 **Model** 和 **vin** 拖曳到 [欄位] 區域。![Connected Cars - 多列卡片](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4u.png)

重新排列所有視覺效果。最終的報告顯示如下 ![Connected Cars - 多列卡片](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4v.png)

### 加入新的報告：*車輛健全狀況統計資料*
  
按一下 ![新增](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4add.png) 加入新的報告，重新命名為「車輛健全狀況統計資料」

從 [視覺效果] 中選取「量測計」視覺效果，然後將 **Speed** 欄位拖曳到 [值]、[最小值]。[最大值] 區域。![Connected Cars - 多列卡片](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4w.png)

將 [值] 區域中的 **speed** 預設彙總變更為 [平均值]

將 [最小值] 區域中的 **speed** 預設彙總變更為 [最小值]

將 [最大值] 區域中的 **speed** 預設彙總變更為 [最大值]

![Connected Cars - 多列卡片](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4x.png)

將 [量測計標題] 重新命名為「平均速度」
 
![Connected Cars - 量測計](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4y.png)

按一下空白區域以加入新的視覺效果。

同樣地加入「平均機油」、「平均油量」和「平均引擎溫度」的**量測計**。

依照上述「平均速度」量測計中的步驟，變更每個量測計中各欄位的預設彙總。

![Connected Cars - 量測計](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4z.png)

按一下空白區域以加入新的視覺效果。

從 [視覺效果] 中選取「條線和群組直條圖」，然後將 [City] 欄位拖曳到 [共用軸]，將 **speed**、**tirepressure 和 engineoil 欄位**拖曳到 [資料行值] 區域，將其彙總類型變更為 [平均值]。

將 **engineTemperature** 欄位拖曳到 [條線值] 區域，將彙總類型變更為 [平均值]。

![Connected Cars -視覺效果欄位](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4aa.png)

將圖表 [標題] 變更為「平均速度、胎壓、機油和引擎溫度」

![Connected Cars -視覺效果欄位](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4bb.png)

按一下空白區域以加入新的視覺效果。

從 [視覺效果] 中選取「樹狀圖」視覺效果，將 **Model** 欄位拖曳到 [群組] 區域，將 **MaintenanceProbability** 欄位拖曳到 [值] 區域。

將圖表 [標題] 變更為「需要維修的車型」

![Connected Cars - 變更圖表標題](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4cc.png)

按一下空白區域以加入新的視覺效果。

從 [視覺效果] 中選取「100% 堆疊橫條圖」視覺效果，將 **city** 欄位拖曳到 [軸] 區域，將 **MaintenanceProbability**、**RecallProbability** 欄位拖曳到 [值] 區域。

![Connected Cars - 加入新的視覺效果](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4dd.png)

按一下 [格式]，選取 [資料色彩]，將 **MaintenanceProbability** 色彩變更為值 **"F2C80F"**

將圖表的 [標題] 變更為「車輛維條和召回的機率 (依城市)」

![Connected Cars - 加入新的視覺效果](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ee.png)

按一下空白區域以加入新的視覺效果。

從 [視覺效果] 中選取「區域圖」視覺效果，將 **Model** 欄位拖曳到 [軸] 區域，將 **engineOil、tirepressure、speed 和 MaintenanceProbability** 欄位拖曳到 [值] 區域。將其彙總類型變更為 [平均值]。

![Connected Cars - 變更彙總類型](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ff.png)

將圖表的 [標題] 變更為「平均機油、胎壓、速度和維修機率 (依車型)」

![Connected Cars - 變更圖表標題](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4gg.png)

按一下空白區域以加入新的視覺效果。

1. 從 [視覺效果] 中選取「散佈圖」視覺效果
2. 將 **Model** 欄位拖曳到 [詳細資料] 和 [圖例] 區域 
3. 將 **fuel** 欄位拖曳到 [X 軸] 區域，將彙總變更為 [平均值]
4. 將 **engineTemparature** 欄位拖曳到 [Y 軸] 區域，將彙總變更為 [平均值]
5. 將 **vin** 欄位拖曳到 [大小] 區域

![Connected Cars - 加入新的視覺效果](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4hh.png)

將圖表 [標題] 變更為「燃料、引擎溫度的平均值 (依車型)」

![Connected Cars - 變更圖表標題](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ii.png)

最終的報告顯示如下。

![Connected Cars - 最終報告](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4jj.png)

### 將報告中的視覺效果釘選到即時儀表板
  
將以上報告中的視覺效果釘選到我們在第 2 節 - **安裝 Power BI 儀表板**中建立的即時儀表板。

儀表板應該如下所示。

![Connected Cars - 儀表板](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-4.0.png)


##  設定 Power BI 批次處理儀表板

>[AZURE.ALERT]端對端批次處理管線需要大約 2 小時才能完成執行。請稍候再進行後續步驟。

**下載 PowerBI 設計工具檔案** • PowerBI 設計工具檔案包含在部署中 • 按一下圖表檢視上的 PowerBI 節點，然後在屬性窗格上按一下 [下載 PowerBI 設計工具檔案] 連結 • 儲存在本機

**設定 PowerBI 報告** • 使用 PowerBI Desktop 開啟設計工具檔案 ‘VehicleTelemetryAnalytics - Desktop Report.pbix’。如果您還沒有安裝 PowerBI Desktop，請從 - http://www.microsoft.com/zh-TW/download/details.aspx?id=45331 安裝 • 按一下 [編輯查詢]。

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/10-edit-powerbi-query.png)

[AZURE.NOTE]Azure SQL Database 的**名稱/密碼**是您在部署安裝過程中提供的值。

- 按兩下 [來源]。

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/11-set-powerbi-source.png)

- 更新伺服器連接字串
- 將 [資料庫] 設為 *connectedcar*。

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/12-set-powerbi-database.png)

- 按一下 [確定]。
- 依預設會選取 [Windows 認證] 索引標籤，請按一下右邊的 [資料庫] 索引標籤，將它變更為 [資料庫認證]。
- 提供 [使用者名稱]和 [密碼]

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/13-provide-database-credentials.png)

- 按一下 [連接]。
- 針對右窗格中出現的其餘 3 個查詢，重複上述步驟，並更新資料來源連接詳細資料。
- 按一下 [關閉並載入]。Power BI Desktop 檔案資料集會連接到 SQL Azure 資料庫資料表。
- **關閉** Power BI Desktop 檔案。

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/14-close-powerbi-desktop.png)

- 按一下 [儲存] 按鈕以儲存變更。 
 
**上傳至 *powerbi.com*** 瀏覽至 powerbi.com 並登入。 ![車輛遙測 PowerBI.com](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/15-vehicle-telemetry-powerbi-site.png)

1.	按一下 [取得資料]  
2.	上傳 Power BI Desktop 檔案。  
3.	若要上傳，請按一下 [取得資料] -> [檔案取得]-> [本機檔案]  
4.	瀏覽至 **“Connected Cars – Desktop Report.pbix”**  
5.	一旦上傳檔案，將會瀏覽回到您的 Power BI 工作區。  

	將會為您建立資料集、報告和空白儀表板。

	>**附註：**您可能需要按一下 [儀表板] 旁邊的加號圖示，以建立空白儀表板。

**將圖表釘選到儀表板** 在 Power BI 中，按一下上面建立的空白儀表板，然後瀏覽至剛才上傳的報告上的 [報告] 區段。

![車輛遙測 PowerBI.com](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard1.png)


**注意報告有六頁：**第 1 頁：車輛密度 第 2 頁：即時車輛健全狀況 第 3 頁：激進駕駛的車輛 第 4 頁：召回的車輛 第 5 頁：省油駕駛的車輛 第 6 頁：Contoso 標誌

![Connected Cars PowerBI.com](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard2.png)
 

**從第 3 頁**，釘選下列項目 1.VIN 的計數 ![Connected Cars PowerBI.com](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard3.png) 2.激進駕駛的車輛 (依車型) – 瀑布圖 ![車輛遙測 - 釘選圖表 4](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard4.png)

**從第 5 頁**，釘選下列項目 1.vin 的計數 ![車輛遙測 - 釘選圖表 5](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard5.png) 2.省油的車輛 (依車型)：群組直條圖 ![車輛遙測 - 釘選圖表 6](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard6.png)

**從第 4 頁**，釘選下列項目

1.	vin 的計數 ![車輛遙測 - 釘選圖表 7](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard7.png) 

2.	召回的車輛 (依城市、車型)：樹狀圖 ![車輛遙測 - 釘選圖表 8](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard8.png)

**從第 6 頁**，釘選下列項目

1.	Contoso Motors 標誌 ![車輛遙測 - 釘選圖表 9](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard9.png)

**組織儀表板**

1.	瀏覽至儀表板 ![車輛遙測 - 組織儀表板 1](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-organize-dashboard1.png)

2.	將滑鼠暫留在每個圖表，根據下列完整儀表板影像中提供的名稱來重新命名。另外也將圖表排列成如下列儀表板所示。![車輛遙測 - 組織儀表板 2](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-organize-dashboard2.png)

![車輛遙測 PowerBI.com](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard.png)

<!---HONumber=AcomDC_1203_2015-->