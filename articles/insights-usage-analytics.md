<properties title="How to use end user analytics" pageTitle="How to use end user analytics" description="Learn about end user analytics in Azure." authors="awills" manager="kamrani" />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="2014-09-29" ms.author="awills"></tags>

# 網站分析

想知道有多少使用者造訪過您的網站嗎？想知道頁面的平均載入時間，或是使用中的瀏覽器有哪些？只要在網頁的程式碼中插入幾行，您即可收集客戶如何使用您的網站的相關資料。

![End User Analytics][End User Analytics]

## 如何設定使用者分析

1.  在 [網站] 分頁上，按一下顯示為「使用者分析」的組件。
2.  在 [組態] 分頁上選取整個檢測指令碼，並加以複製。
    
	![組態][組態]
3.  在您的每個網頁中，將此指令碼貼到
    標籤中) 取代。建議您將此指令碼插入您所有的網頁中。如果您使用 ASP.NET，您可以將指令碼插入應用程式的主要頁面中。
4.  部署並使用您的 Web 應用程式。使用分析資訊大約會在 5-10 分鐘後出現。

## 探索資料

瀏覽器工作階段組件可讓您深入檢視不同的瀏覽器，以及瀏覽器版本。
	![Browsers][Browsers]

[分析] 組件會顯示：

-   不同裝置類型的明細，包括 [桌面] 和 [行動]。
-   您上週最常使用的 5 個頁面，以及頁面載入時間的圖形。此外也會提供工作階段和檢視的數目。
    
	![最常使用的頁面][最常使用的頁面]
-   上週速度最緩慢的頁面；您可以進行改善，以符合您的商業需求和目標。

  [End User Analytics]: ./media/insights-usage-analytics/Insights_ConfiguredExperience.png
  [組態]: ./media/insights-usage-analytics/Insights_CopyCode.png
  [Browsers]: ./media/insights-usage-analytics/Insights_Browsers.png
  [最常使用的頁面]: ./media/insights-usage-analytics/Insights_TopPages.png
