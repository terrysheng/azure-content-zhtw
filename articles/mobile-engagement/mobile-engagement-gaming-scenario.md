<properties 
	pageTitle="對遊戲應用程式實作 Azure Mobile Engagement"
	description="對遊戲應用程式實作 Azure Mobile Engagement 的案例" 
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="piyushjo"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-engagement"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="mobile-multiple"
	ms.workload="mobile" 
	ms.date="02/29/2016"
	ms.author="piyushjo"/>

#對遊戲應用程式實作 Mobile Engagement

## 概觀

某家新創遊戲公司推出了一款新的釣魚角色扮演/策略遊戲應用程式。該遊戲已上線運作 6 個月之久，所創造的成績非常優異，下載人數已達數百萬，且相較於其他新創公司所推出的遊戲應用程式，這款遊戲的保留期相當高。在每季檢討會議中，重要關係人一致認為他們需要增加每位使用者的平均營收 (ARPU)。他們在遊戲中提供高階套件做為特殊優惠。這些遊戲套件可讓使用者升級他們在遊戲中所用釣線和誘餌或釣具的外觀和效果。不過，這些套件的銷售成績非常差。因此他們決定先使用分析工具分析客戶在玩遊戲時的體驗，然後發展業務開發計劃，透過進一步劃分玩家來增加銷售額。

他們根據 [Azure Mobile Engagement - 入門指南與最佳作法](mobile-engagement-getting-started-best-practices.md)建構了業務開發策略。

##目標和 KPI

該遊戲的重要關係人齊聚一堂。所有人都同意一個主要目標，那就是將高階套件的銷售量增加 15%。他們建立起業務關鍵效能指標 (KPI)，以便測量和推動這個目標。

* 使用者會在到達哪個遊戲等級時購買這些套件？
* 每位使用者、每個工作階段、每週和每月的營收為何？
* 最愛的購買類型為何？

[入門指南](mobile-engagement-getting-started-best-practices.md)的第 1 部分說明如何定義目標和 KPI。

現在已定義好業務 KPI，行動裝置產品經理接著會建立業務開發 KPI，以確定新的使用者趨勢和保留期。

* 監視下列時間間隔的保留期和使用情形：每天、每 2 天、每週、每月和每 3 個月
* 活躍使用者人數
* 應用程式在市集內的評分

根據 IT 團隊所提的建議，他新增了下列技術 KPI 來回答下列問題：

* 我的使用者路徑為何 (所瀏覽的頁面、使用者在其中所花的時間長短)
* 每個工作階段發生當機或錯誤的次數
* 我的使用者執行哪些作業系統版本？
* 我的使用者所用螢幕的平均大小為何？
* 我的使用者擁有哪種網際網路連線？

行動裝置產品經理針對每個 KPI 指定她需要的資料，以及其在腳本中的位置。

## 業務開發計劃和整合

在建置進階業務開發計劃之前，負責該項專案的行動裝置專案主任應該先深入了解使用者使用產品的方式和時間。

3 個月過後，行動裝置專案主任已收集足夠的資料，可以加強他的應用程式內推播通知銷售額。他發現到：

* 第一次購買通常發生在 14 級，其中有 90% 會購買價格 3 美元的新傳說級武器，
* 而購買過的使用者中有 80% 會繼續使用產品並購買其他裝備。
* 超過 20 級的使用者會開始每週花費超過 10 美元。
* 使用者往往會在 16 級、24 級和 32 級時購買高階套件。

多虧了這項分析，行動裝置專案主任決定建立特定的推播通知順序，以增加應用程式內銷售額。他建立了三個他稱為「歡迎計劃」、「銷售計劃」和「無活動計劃」的推播順序。如需詳細資訊，請參閱[腳本](https://github.com/Azure/azure-mobile-engagement-samples/tree/master/Playbooks) ![][1]。

<!--Image references-->

[1]: ./media/mobile-engagement-game-scenario/notification-scenario.png

<!--Link references-->

<!---HONumber=AcomDC_0302_2016-------->