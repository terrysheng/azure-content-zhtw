<properties title="How to scale a website" pageTitle="How to scale a website" description="Learn how to scale your hosting plan in Azure." authors="stepsic"  />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="stepsic"></tags>

# 如何調整網站

在「Azure 入口網站預覽」中，您可以手動設定 Web 主控方案的執行個體計數，或是藉由參數設定使其自訂調整。在進行 Web 主控方案的調整設定之前，您應考量執行個體大小對調整的影響。大小愈大，核心和記憶體就愈多，因此將可對相同的執行個體數目帶來較高的效能。

調整會對整個 Web 主控方案產生影響。當您建立網站時，您可以選擇建立新的 Web 主控方案，或使用現有的 Web 主控方案。具備 Web 主控方案後，您所有的網站將共用相同的執行個體，因此會全部一起進行調整。

## 調整 Web 主控方案

1.  在 [Azure 入口網站預覽][]中，依序按一下 [瀏覽]、[網站] 和網站的名稱，以開啟分頁。
2.  在網站分頁上，[作業] 透鏡上的 [調整] 組件可為您指出 Web 主控方案的狀態：當您手動調整時，會顯示 [關閉]、依一或多個效能度量調整時會顯示 [效能]，啟用多個自動調整設定檔時則會顯示 [排程]。
    
	![調整組件][]
3.  按一下組件，將會進入 [調整] 分頁。在調整分頁頂端，您可以檢視 Web 主控方案之自動調整動作的歷程記錄。

    ![Scale blade][]

4.  您可以使用 [執行個體] 滑動軸，手動調整執行 Web 主控方案的虛擬機器數。
5.  如果您要讓執行個體數目依據負載自動調整，請選取 [自動調整模式] 下的 [效能]。此時，您將無法在「Azure 入口網站預覽」中選取 [排程]。
    
	![依據 CPU 百分比調整分頁][]
6.  選取 [效能] 後，會出現兩項變更：

    -   [執行個體範圍] 此時會讓您選擇最大和最小執行個體計數。無論負載多寡，自動調整一律會將您限定在此範圍內。
    -   您可以在 [目標度量] 區段中定義效能度量。

7.  [CPU 百分比] 區段可讓您為 Web 主控方案中的所有執行個體設定平均 CPU 的目標。平均 CPU 超過您所定義的最大值時，即會擴大。
啟用自動調整時，您會在網站分頁上的組件中看見 [效能]，並且會在圖表中看見您的調整歷程記錄：

	![Scale blade with CPU Percentage][]

請注意，在「Azure 入口網站預覽」中，您無法變更共用 Web 主控方案的執行個體數目。

## 進階調整

您可以根據 [CPU 百分比] 以外的度量進行調整，且甚至可設定一組複雜的擴大和縮小規則，這是「Azure 入口網站預覽」的新功能。

### 根據其他效能度量進行調整

除了 CPU，您也可以使用下列調整依據：

-   平均記憶體 - 如果執行個體上使用的平均記憶體百分比高於或低於指定的臨界值，則會新增或移除執行個體。
-   HTTP 佇列深度或磁碟佇列深度 - 如果 HTTP 要求或磁碟之佇列中的訊息數高於或低於指定的臨界值，則會新增或移除執行個體。

有兩種方式可讓您依據其他度量進行調整。如果您要依單一度量進行調整，請選取 [CPU 百分比] 滑動軸旁的＞形箭號。這會開啟 [度量詳細資料] 分頁：

![Entry point to scale metrics][]

若要逐一依據一個度量進行調整，您可以按一下命令列中的 [新增度量]：

![Add metrics][]

[度量詳細資料] 分頁包含您設定最佳調整設定檔所需的所有控制項。請在頂端選擇要做為調整依據的新度量。

### 使用多個步驟進行調整

度量圖形下有兩個區段：[Scale up rules] 和 [Scale down rules]。如果符合**任何**擴大規則，您的服務即會擴大。反之，如果符合**所有**縮小規則，您的服務即會縮小。

對於每項規則，您可以選擇：

-   條件 - 大於或小於
-   臨界值 - 此度量要觸發動作所需達到的數目
-   過量期間 - 此度量高於平均值的分鐘數
-   擴大或縮小依據 - 調整動作的大小
-   等待期間 - 在前次調整動作之後，此規則要再次執行調整前所應等待的時間

![Multiple scale rules][]

透過多項調整規則，您將可更靈活地隨著效能變更進行擴大 (或縮小)。例如，您可以定義兩項調整規則：

1.  如果 CPU 百分比高於 60%，則擴大 1 個執行個體
2.  如果 CPU 百分比高於 85%，則擴大 3 個執行個體

透過這項附加規則，如果您的負載在調整動作之前超過 85%，您將會增加兩個執行個體，而不是一個。

  [Azure 入口網站預覽]: https://portal.azure.com/
  [調整組件]: ./media/insights-how-to-scale/Insights_ScalePartOff.png
  [Scale blade]: ./media/insights-how-to-scale/Insights_ScaleBladeDayZero.png
  [依據 CPU 百分比調整分頁]: ./media/insights-how-to-scale/Insights_ScaleBladeCPU.png
  [Scale blade with CPU Percentage]: ./media/insights-how-to-scale/Insights_ScalePartBladeOn.png
  [Entry point to scale metrics]: ./media/insights-how-to-scale/Insights_ScaleMetricChevron.png
  [Add metrics]: ./media/insights-how-to-scale/Insights_AddMetric.png
  [Multiple scale rules]: ./media/insights-how-to-scale/Insights_MultipleScaleRules.png
