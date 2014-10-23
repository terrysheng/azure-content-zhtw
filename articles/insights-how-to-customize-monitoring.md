<properties title="How to customize monitoring" pageTitle="How to customize monitoring" description="Learn how to customize monitoring charts in Azure." authors="stepsic"  />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="stepsic"></tags>

# 自訂監視

在「Azure 入口網站預覽」中，您現在已可利用比以往更多的方式來檢視監視資料，包括自訂時間範圍和選擇更多度量。

1.  在 [Azure 入口網站預覽][]中，按一下 [瀏覽]，然後按一下您有興趣監視的資源。
2.  [監視] 透鏡中含有每個 Azure 資源最重要的度量。例如，網站就有「要求」、「錯誤」、「[Web 測試][]」和「[分析][]」。按一下 [今天的要求和錯誤] 組件，會顯示 [度量] 分頁。
    
	![監視透鏡][]
3.  [度量] 分頁會顯示您所選度量的詳細資料。分頁頂端會有一個圖形，圖形下會有一個資料表顯示這些度量的彙總，例如平均值、最小值和最大值。該資料表下會列出您所定義的警示 (依照分頁上顯示的度量進行篩選)。如此，即使您有許多警示，在此您也只會看見關聯性較高者。您仍可檢視網站的所有警示，只要按一下 [網站] 分頁上的 [警示規則] 組件即可。
    
	![度量分頁][]
4.  若要自訂要顯示的度量，請以滑鼠右鍵按一下圖表，然後選取 [編輯查詢]：
    
	![編輯查詢][]
5.  在 [編輯查詢] 分頁上，您可以執行兩個動作：變更時間範圍和選擇不同的度量。
    
	![編輯查詢][1]
6.  變更時間範圍十分容易，只要選取不同的範圍 (例如 [Past Hour])，再按一下分頁底部的 [儲存] 即可。您也可以選擇 [自訂]，這是「入口網站預覽」的新功能：
    
	![自訂時間範圍][]
7.  「自訂」可讓您選擇過去 2 週的任何一段期間，例如，您可以完整檢視這兩週，或僅檢視昨天的某 1 小時。請在文字方塊中輸入不同的小時。
8.  在時間範圍下，您可以選擇任何要顯示在圖表上的度量數。您可以檢視某些新度量：[記憶體工作集] 和 [平均記憶體工作集]。

9.  如果您按一下 [儲存]，您的變更將會保留到您退出網站分頁為止。當您後續返回頁面時，您將會看見原有的度量和時間範圍。

## 監視新資源

監視各種新資源的效能度量是 Azure 入口網站預覽的新功能，包括：

-   Web 主控方案
-   Redis 快取
-   DocumentDB 帳戶

Web 主控方案較其他資源來得複雜些，因為他們代表您**網站**執行的執行個體效能。若要存取 Web 主控方案，請按一下網站「摘要」透鏡中的 Web 主控方案。

![Web hosting plan][]

在此，您可以檢視 [監視] 透鏡中的圖表，其運作方式與網站分頁中的圖表相同，但在此處您可以檢視新度量：

-   CPU 百分比
-   記憶體百分比
-   HTTP 佇列深度
-   磁碟佇列深度

## 建立並排圖表

透過「Azure 入口網站預覽」中強大的使用者自訂功能，您可以建立並排圖表，以供自訂之用。

1.  首先，請以滑鼠右鍵按一下最先要處理的圖表，然後選取 [自訂]。
    
	![自訂圖表][]
2.  然後，按一下 [...] 功能表上的 [複製]，以複製組件。
    
	![複製組件][]
3.  最後，在畫面頂端的工具列上按一下 [完成]。現在，您可以將此組件視為一般度量組件。如果您以滑鼠右鍵按一下顯示的度量並加以變更，您將可看見兩個不同的度量同時並排顯示：
    
	![兩個度量並排顯示][]

 請注意，當您退出入口網站時，圖表的時間範圍和選擇的度量將會重設。

  [Azure 入口網站預覽]: https://portal.azure.com/
  [Web 測試]: http://go.microsoft.com/fwlink/?LinkID=394528&clcid=0x409
  [分析]: http://go.microsoft.com/fwlink/?LinkID=394529&clcid=0x409
  [監視透鏡]: ./media/insights-how-to-customize-monitoring/Insights_MonitoringChart.png
  [度量分頁]: ./media/insights-how-to-customize-monitoring/Insights_MetricBlade.png
  [編輯查詢]: ./media/insights-how-to-customize-monitoring/Insights_MetricMenu.png
  [1]: ./media/insights-how-to-customize-monitoring/Insights_EditQuery.png
  [自訂時間範圍]: ./media/insights-how-to-customize-monitoring/Insights_CustomTime.png
  [Web hosting plan]: ./media/insights-how-to-customize-monitoring/Insights_WHPSelect.png
  [自訂圖表]: ./media/insights-how-to-customize-monitoring/Insights_Customize.png
  [複製組件]: ./media/insights-how-to-customize-monitoring/Insights_ClonePart.png
  [兩個度量並排顯示]: ./media/insights-how-to-customize-monitoring/Insights_SideBySide.png
