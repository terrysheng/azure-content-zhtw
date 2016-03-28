<properties 
	pageTitle="監視和管理 Azure Data Factory 管線" 
	description="了解如何使用監視及管理應用程式來監視及管理 Azure 的資料處理站及管線" 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/14/2016" 
	ms.author="spelluru"/>

# 使用新的監視及管理應用程式，來監視及管理 Azure Data Factory 管線
> [AZURE.SELECTOR]
- [使用 Azure 入口網站/Azure PowerShell](data-factory-monitor-manage-pipelines.md)
- [使用監視及管理應用程式](data-factory-monitor-manage-app.md)

本文說明如何使用監視及管理應用程式，來監視、管理管線，以及為管線偵錯；同時也提供如何使用應用程式來建立警示，以及取得失敗通知的資訊。
      
## 啟動監視及管理應用程式 
如要啟動監視及管理應用程式，請按一下您資料處理站的 [DATA FACTORY] 刀鋒視窗上的 [監視和管理] 圖格。

![Data Factory 首頁上的監視磚](./media/data-factory-monitor-manage-app/MonitoringAppTile.png)

您應該會看到監視及管理應用程式在另一個索引標籤/視窗中啟動。

![監視及管理應用程式](./media/data-factory-monitor-manage-app/AppLaunched.png)

> [AZURE.NOTE] 如果未在底部的清單中看見活動時段，可按一下工具列上的 [重新整理] 按鈕，重新整理清單。此外，為 [開始時間] 和 [結束時間] 篩選器設定正確的值。

## 了解啟動監視及管理應用程式
左側會有三個索引標籤 ([資源總管]、[監視檢視] 及 [警示])，而系統預設會選取第一個索引標籤 (資源總管)。

### 資源總管
您會在左窗格看到 [資源總管] 的**樹狀檢視**，在頂端看到 [圖表檢視]，在中間窗格的底部看到 [活動時段] 清單，以及在右窗格看到 [屬性]/[活動時段總管] 索引標籤。

您可以在樹狀檢視的資料處理站中看到所有資源 (管線、資料集、已連結的服務)。當您選取 [資源總管] 中某個物件時，會看到下列情況：

- 在 [圖表檢視] 中，相關聯的資料處理站實體會反白顯示。
- 在底部的 [活動時段] 清單中，相關聯的活動時段 (請按一下[這裡](data-factory-scheduling-and-execution.md)來了解活動時段) 會反白顯示。  
- 右窗格的 [屬性] 視窗中有已選取物件的屬性。 
- 所選取物件的 JSON 定義 (如果適用)。例如︰連結服務或資料集或管線。 

![資源總管](./media/data-factory-monitor-manage-app/ResourceExplorer.png)

請參閱[排程和執行](data-factory-scheduling-and-execution.md)文章，來了解活動時段的詳細概念性資訊。

### 圖表檢視
Data Factory 的圖表檢視提供單一窗格，可用來監視和管理 Data Factory 及其資產。當您選取圖表檢視中某個資料處理站實體 (資料集/管線) 時，您會看到下列情況：
 
- 在樹狀檢視中，資料處理站實體已選取。
- 在 [活動時段] 清單中，相關聯的活動時段已反白顯示。
- 在 [屬性] 視窗中，有已選取物件的屬性。

當管線已啟用時 (也就是不在暫停狀態)，它會有綠色線條，如下所示。

![管線執行中](./media/data-factory-monitor-manage-app/PipelineRunning.png)

您會注意到在圖表檢視中，每個管線有 3 個命令按鈕。第 2 個按鈕可以讓您暫停管線，但它不會終止正在執行的活動，還會讓活動繼續完成。第 3 個按鈕會暫停管線，並終止管線正在執行的活動。第 1 個按鈕會讓管線繼續執行，也就是取消暫停管線。當管線暫停時，您會注意到管線磚的色彩變化，如下所示。

![磚上的暫停/繼續按鈕](./media/data-factory-monitor-manage-app/SuspendResumeOnTile.png)

您可以多重選取兩個以上的管線 (使用 CTRL 鍵)，然後使用命令列按鈕來同時暫停/繼續多個管線。

![命令列上的暫停/繼續按鈕](./media/data-factory-monitor-manage-app/SuspendResumeOnCommandBar.png)

您可以查看管線中的所有活動，方法是用滑鼠右鍵按一下管線圖格，然後按一下 [開啟管線]。

![[開啟管線] 功能表](./media/data-factory-monitor-manage-app/OpenPipelineMenu.png)

在開啟的管線檢視中，您會看到管線裡的所有活動。在這個範例中只有一個活動：複製活動。如要回到上一個檢視，請按一下頂端階層連結功能表中的資料處理站名稱。

![已開啟的管線](./media/data-factory-monitor-manage-app/OpenedPipeline.png)

無論是在關閉或開啟的管線檢視中，當您按一下某個輸出資料集時，當您把滑鼠游標移動到輸出資料集上時，您會看到該資料集的 [活動時段] 快顯視窗。

![活動時段快顯視窗](./media/data-factory-monitor-manage-app/ActivityWindowsPopup.png)

只要按一下某個活動時段，即可在右窗格的 [屬性] 視窗中看到該活動時段的詳細資料。

![活動時段屬性](./media/data-factory-monitor-manage-app/ActivityWindowProperties.png)

在右窗格中，切換到 [活動時段總管] 索引標籤，即可看到更多詳細資料。

![活動時段總管](./media/data-factory-monitor-manage-app/ActivityWindowExplorer.png)

您也會看到在 [嘗試次數] 區段中每個活動執行嘗試的**解析變數**。

![解析的變數](./media/data-factory-monitor-manage-app/ResolvedVariables.PNG)

切換至 [指令碼] 索引標籤，以查看所選物件的 JSON 指令碼定義。

![[指令碼] 索引標籤](./media/data-factory-monitor-manage-app/ScriptTab.png)

您可以在三個地方看到活動時段：

- 圖表檢視 (中間窗格) 中的 [活動時段] 快顯視窗。
- 右窗格中的 [活動時段總管]。
- 底部窗格中的 [活動時段清單]。

在 [活動時段] 快顯視窗及 [活動時段總管] 中，您可以使用向左及向右箭號捲動到上一週及下一週。

![活動時段總管的向左/向右箭號](./media/data-factory-monitor-manage-app/ActivityWindowExplorerLeftRightArrows.png)

在 [圖表檢視] 的底部，您會看到 [放大]、[縮小]、[縮放至適當比例]、[顯示比例 100%]、[鎖定配置] (防止您在圖表檢視中不小心移動了資料表和管線) 按鈕。[鎖定配置] 按鈕預設為開啟，但您可以關閉該設定，以便移動圖表中的實體。當您關閉該設定，您可以使用上一個按鈕來自動把資料表和管線移動到適當的地方。您也可以使用滑鼠滾輪來放大/縮小。

![圖表檢視的縮放命令](./media/data-factory-monitor-manage-app/DiagramViewZoomCommands.png)


### 活動時段清單
中間窗格底端的 [活動時段清單] 會顯示您在 [資源總管] 或圖表檢視中所選取資料集的所有活動時段。根據預設，清單是以遞減的順序排列，這代表清單頂端會是最新的活動時段。

![活動時段清單](./media/data-factory-monitor-manage-app/ActivityWindowsList.png)

這份清單不會自動重新整理，因此請使用工具列上 [重新整理] 按鈕來手動重新整理。


下列為活動時段的狀態：

<table>
<tr>
	<th align="left">狀態</th><th align="left">子狀態</th><th align="left">說明</th>
</tr>
<tr>
	<td rowspan="8">等候</td><td>ScheduleTime</td><td>活動時段執行的時間還沒到。</td>
</tr>
<tr>
<td>DatasetDependencies</td><td>上游相依項目尚未就緒。</td>
</tr>
<tr>
<td>ComputeResources</td><td>計算資源無法使用。</td>
</tr>
<tr>
<td>ConcurrencyLimit</td> <td>所有活動執行個體都在執行其他的活動時段。</td>
</tr>
<tr>
<td>ActivityResume</td><td>活動已暫停；此時活動必須要繼續，才能再執行活動時段。</td>
</tr>
<tr>
<td>Retry</td><td>將重試活動執行。</td>
</tr>
<tr>
<td>驗證</td><td>驗證尚未啟動。</td>
</tr>
<tr>
<td>ValidationRetry</td><td>正在等待重試驗證。</td>
</tr>
<tr>
&lt;tr
<td rowspan="2">InProgress</td><td>Validating</td><td>驗證進行中。</td>
</tr>
<td></td>
<td>系統正在處理該活動時段。</td>
</tr>
<tr>
<td rowspan="4">Failed</td><td>TimedOut</td><td>執行花費的時間超過活動所允許的時間。</td>
</tr>
<tr>
<td>Canceled</td><td>被使用者動作取消。</td>
</tr>
<tr>
<td>驗證</td><td>驗證失敗。</td>
</tr>
<tr>
<td></td><td>無法產生和/或驗證活動時段。</td>
</tr>
<td>Ready</td><td></td><td>活動時段已可供使用。</td>
</tr>
<tr>
<td>Skipped</td><td></td><td>系統沒有在處理該活動時段。</td>
</tr>
<tr>
<td>None</td><td></td><td>曾經以不同的狀態存在，但目前已重設的活動時段。</td>
</tr>
</table>


當您按一下清單中的某個活動時段時，您會在右側的 [活動時段總管] 或 [屬性] 視窗中看到該活動時段的詳細資料。

![活動時段總管](./media/data-factory-monitor-manage-app/ActivityWindowExplorer-2.png)

### 重新整理活動時段  
詳細資料並不會自動重新整理，因此您必須使用命令列上的 [重新整理] 按鈕 (第二個按鈕)，手動重新整理活動時段清單。
 

### [屬性] 視窗
[屬性] 視窗在監視及管理應用程式最右側的窗格中。

![[屬性] 視窗](./media/data-factory-monitor-manage-app/PropertiesWindow.png)

它會顯示您在 [資源總管] (樹狀檢視) (或) 圖表檢視 (或) [活動時段清單] 中所選取項目的屬性。

### 活動時段總管

[活動時段總管] 視窗位於 [監視及管理] 應用程式最右側的窗格中。它會顯示您在 [活動時段] 快顯視窗或 [活動時段清單] 中所選取活動時段的詳細資料。

![活動時段總管](./media/data-factory-monitor-manage-app/ActivityWindowExplorer-3.png)

您可以切換至不同的活動時段，方法是按一下頂端行事曆檢視中的其他活動時段。而頂端的向左箭號/向右箭號按鈕可讓您查看上一週/下一週的活動時段。

底部窗格的工具列按鈕可讓您**重新執行**活動時段，或是**重新整理**窗格中的詳細資料。

### 指令碼 
您可以使用 [指令碼] 索引標籤，來檢視所選取 Data Factory 實體 (連結服務、資料集和管線) 的 JSON 定義。

![[指令碼] 索引標籤](./media/data-factory-monitor-manage-app/ScriptTab.png)

## 使用系統檢視
[監視及管理] 應用程式包含預先建置的系統檢視 ([最近的活動時段]、[失敗的活動時段]、[進行中的活動時段])，可讓您查看資料處理站最近/失敗/進行中的活動時段。

只要按一下左側的 [監視檢視] 索引標籤，就能切換到 [監視檢視]。

![[監視檢視] 索引標籤](./media/data-factory-monitor-manage-app/MonitoringViewsTab.png)

目前我們支援三種系統檢視。請選取某個選項，以便在 [活動時段清單] (位於中間窗格底部) 中查看最近的活動時段 (或) 失敗的活動時段 (或) 進行中的活動時段。

當您選取 [最近的活動時段] 時，您會看到以 [上次嘗試時間] 的遞減順序排列之所有最近的活動時段。

您可以使用 [失敗的活動時段] 檢視，來查看清單中所有失敗的活動時段。只要選取清單中某個失敗的活動時段，就能在 [屬性] 視窗 (或) [活動時段總管] 中看到該活動時段的詳細資料。您也可以下載失敗的活動時段的任何記錄檔。


## 排序及篩選活動時段
您可以在命令列中變更**開始時間**及**結束時間**設定，來篩選活動時段。當您變更開始時間及結束時間之後，請按一下結束時間旁邊的按鈕來重新整理活動時段清單。

![開始及結束時間](./media/data-factory-monitor-manage-app/StartAndEndTimes.png)

> [AZURE.NOTE] 目前，監視及管理應用程式中所有時間都是以 UTC 格式來顯示

在 [活動時段清單] 中，按一下某個資料行的名稱 (例如：狀態)。

![活動時段清單的資料行功能表](./media/data-factory-monitor-manage-app/ActivityWindowsListColumnMenu.png)

您可以執行下列動作：

- 以遞增順序來排序。
- 以遞減順序來排序。
- 根據一或多個值 (Ready、Waiting 等) 來篩選。

當您在某個資料行上指定篩選條件時，該資料行的篩選器按鈕就會啟用，以指出該資料行中的值為已經過篩選的值。

![活動時段清單中資料行的篩選器](./media/data-factory-monitor-manage-app/ActivityWindowsListFilterInColumn.png)

您可以使用相同的快顯視窗來清除篩選條件。如要清除活動時段清單的所有篩選條件，請按一下命令列上的 [清除篩選條件] 按鈕。

![清除活動時段清單中的所有篩選條件](./media/data-factory-monitor-manage-app/ClearAllFiltersActivityWindowsList.png)


## 執行批次動作

### 重新執行已選取的活動時段
選取某個活動時段、按一下第一個命令列按鈕旁的向下箭號，然後選取 [重新執行]/[搭配管線上游來重新執行]。當您選取 [搭配管線上游來重新執行] 時，系統也會傳回所有上游的活動時段。![重新執行某個活動時段](./media/data-factory-monitor-manage-app/ReRunSlice.png)

您也可以選取清單中的數個活動時段，然後同時重新執行這些活動時段。您可能會想要根據狀態 (例如：**失敗**) 來篩選活動時段，然後在修正導致活動時段執行失敗的問題之後，重新執行失敗的活動時段。請參閱下一節來取得如何篩選清單中活動時段的詳細資料。

### 暫停/繼續多個管線
您可以多重選取兩個以上的管線 (使用 CTRL 鍵)，然後使用命令列按鈕 (下圖紅色矩形中的按鈕) 來同時暫停/繼續多個管線。

![命令列上的暫止/繼續按鈕](./media/data-factory-monitor-manage-app/SuspendResumeOnCommandBar.png)

## 建立警示 
[警示] 頁面可讓您建立新警示，以及檢視/編輯/刪除現有的警示。您也可以停用/啟用警示。按一下 [警示] 索引標籤即可查看該頁面。

![[警示] 索引標籤](./media/data-factory-monitor-manage-app/AlertsTab.png)

### 如何建立警示

1. 按一下 [加入警示] 來加入警示。此時您會看到 [詳細資料] 頁面。 

	![建立警示：[詳細資料] 頁面](./media/data-factory-monitor-manage-app/CreateAlertDetailsPage.png)
1. 指定警示的**名稱**及**說明**，然後按 [下一步]。此時應該會出現 [篩選器] 頁面。

	![建立警示：[篩選器] 頁面](./media/data-factory-monitor-manage-app/CreateAlertFiltersPage.png)

	您也可以**彙總**警示事件，如下圖所示：

	![彙總警示](./media/data-factory-monitor-manage-app/AggregateAlerts.png)
2. 選取您想要 Data Factory 服務提醒您的**事件**、**狀態**及**子狀態** (選擇性)，然後按 [下一步]。您應該會看到 [收件者] 頁面。

	![建立警示：[收件者] 頁面](./media/data-factory-monitor-manage-app/CreateAlertRecipientsPage.png) 
3. 選取 [電子郵件訂用帳戶管理員] 並/或輸入**其他管理員的電子郵件**，然後按一下 [完成]。此時您應該會看到警示清單。 
	
	![警示清單](./media/data-factory-monitor-manage-app/AlertsList.png)

請在警示清單中，使用與警示相關聯的按鈕來編輯/刪除/停用/啟用警示。

### 事件/狀態/子狀態
下列資料表提供可用事件和狀態 (及子狀態) 的清單。

事件名稱 | 狀態 | 子狀態
-------------- | ------ | ----------
活動執行已開始 | 已啟動 | 啟動中
活動執行已結束 | Succeeded | Succeeded 
活動執行已結束 | Failed| 資源配置失敗<br/><br/>執行失敗<br/><br/>逾時<br/><br/>驗證失敗<br/><br/>已放棄
已開始建立隨選 HDI 叢集 | 已啟動 | &nbsp; |
已成功建立隨選 HDI 叢集 | Succeeded | &nbsp; |
已刪除隨選 HDI 叢集 | Succeeded | &nbsp; |
### 如何編輯/刪除/停用警示


![警示按鈕](./media/data-factory-monitor-manage-app/AlertButtons.png)



    
 

<!---HONumber=AcomDC_0316_2016-->