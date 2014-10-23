<properties title="How to debug with events" pageTitle="How to debug with events" description="Learn how to see events in Azure." authors="hanikn"  />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="hanikn"></tags>

# 監視對您的 Azure 資源或資源群組造成影響的事件

1.  登入 [Azure 入口網站預覽][]。
2.  按一下左側導覽列 (也稱為**導向列**) 上的 [瀏覽] 按鈕。
    
	![瀏覽中樞][]
3.  然後選擇任何資源 (視您關注的事件而定)。為方便解說，本文中的螢幕擷取畫面將包含資源群組的事件。
4.  在 [資源群組] 分頁上，按一下資源群組的名稱。這會將您導向至資源群組分頁。
    
	![資源群組][]
5.  資源群組分頁包含一個名為 [上週事件] 的組件。該組件上的每個長條，分別代表上週的每一天發生的事件數。每個長條可以有兩種不同的顏色：藍色和粉紅色。粉紅色代表當天的**失敗**事件，藍色代表所有其他事件。
    
	![資源群組][1]
6.  現在，按一下 [上週事件] 組件。您會看見名為 [事件] 分頁的新分頁，其中包含上週所有對您的資源群組造成影響的事件。
    
	![資源群組][2]
7.  按一下其中一個事件。
    
	![資源群組][3]
    此時會開啟新分頁，其中包含該事件的多項詳細資料。對於**失敗**事件，此頁面通常會顯示 [子狀態] 和 [屬性] 區段，其中包含可用於偵錯的詳細資料。

  [Azure 入口網站預覽]: https://portal.azure.com/
  [瀏覽中樞]: ./media/insights-debugging-with-events/Insights_Browse.png
  [資源群組]: ./media/insights-debugging-with-events/Insights_SelectRG.png
  [1]: ./media/insights-debugging-with-events/Insights_RGBlade.png
  [2]: ./media/insights-debugging-with-events/Insights_AllEvents.png
  [3]: ./media/insights-debugging-with-events/Insights_EventDetails.png
