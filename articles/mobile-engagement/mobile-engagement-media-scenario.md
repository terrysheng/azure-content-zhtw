<properties 
	pageTitle="對媒體應用程式實作 Azure Mobile Engagement"
	description="對媒體應用程式實作 Azure Mobile Engagement 的案例" 
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
	ms.date="11/23/2015"
	ms.author="piyushjo"/>

#對媒體應用程式實作 Mobile Engagement

## 概觀

John 是一家大型媒體公司的行動裝置專案經理。他最近推出了一款新的應用程式，並獲得很高的下載次數。他已達成預定的下載次數目標，但從每位使用者身上所獲得的投資回報 (ROI) 卻未達要求。

John 已找到 ROI 太低的原因。使用者經常在短短 2 週後就停止使用他的應用程式，而且大多不會再回頭使用。他想要提升應用程式的保留期。

他在做過初步測試後發現，當他利用推播通知吸引使用者注意時，他們往往會繼續使用這款應用程式。而且原本已經沒在使用應用程式的使用者，經常也會因為某些傳送給他們的通知，而回頭繼續使用。John 決定為他的應用程式投資某種形式的業務開發計劃，以便透過推播通知來使用進階的目標鎖定功能。

John 最近看了 [Azure Mobile Engagement - 入門指南與最佳作法](mobile-engagement-getting-started-best-practices.md)一文，因此決定實作指南中所提出的建議。

##目標和 KPI

John 所推出的應用程式的重要關係人聚在一起。當使用者使用他的媒體時，其中的廣告便會為他們帶來生意。所以只要增加每位使用者使用的內容，John 就能增加營收。所有人都同意一個主要目標，那就是將廣告所帶來的銷售量增加 25%。他們建立起業務關鍵效能指標 (KPI)，以便測量和推動這個目標。

* 每位使用者點按的廣告數目
* (每位使用者/每個工作階段/每週/每月...) 瀏覽過多少文章頁面
* 最愛的類別有哪些

John 根據他與重要關係人的會面結果，定義了他的業務 KPI。他按照 [Azure Mobile Engagement - 入門指南與最佳作法](mobile-engagement-getting-started-best-practices.md)的第一部分進行。

接下來，他建立下列 Engagement KPI 來確定有達成目標：

* 監視下列時間間隔的保留期：每日、每週、每兩週和每月。
* 活躍使用者人數
* 應用程式在應用程式市集內的評分

根據 IT 團隊所提的建議，他新增了下列技術 KPI 來回答下列問題：

* 我的使用者路徑為何 (所瀏覽的頁面、使用者在其中所花的時間長短)
* 每個工作階段發生當機或錯誤的次數？
* 我的使用者執行哪些作業系統版本？
* 我的使用者所用螢幕的平均大小為何？
* 我的使用者擁有哪種網際網路連線？

他針對每個 KPI 分類出所需的資料，並將其記錄在腳本的適當位置。

## 業務開發計劃和整合

現在 John 已定義好 KPI，於是他開始進行業務開發策略階段，他定義 4 個業務開發計劃和計劃目標：![][1]

然後 John 更進一步，詳細說明每個計劃的推播通知。推播通知是由五個項目所定義而成：

1. 目標：通知目標為何
2. 如何達成目標
3. 目標：誰會收到通知？
4. 內容：通知的措辭和格式 (應用程式內/應用程式外)
5. 何時：此推播通知的最佳傳送時機為何

	![][2]

如需詳細資訊，請參閱[腳本](https://github.com/Azure/azure-mobile-engagement-samples/tree/master/Playbooks)。

根據白皮書的第二部分，John 會使用目標區段來定義他必須收集的資料，並與 IT 小組共同撰寫標記計劃來實作解決方案。經過一星期的實作和使用者接受度測試後，John 終於可以啟動他的計劃。

##計劃結果

四個月之後，John 檢閱計劃的成效。歡迎計劃和每週計劃有達成他的目標。只有一個工作階段的使用者數目減少、受到使用的應用程式功能變多，而且每週的連線數目翻倍。

**無活動計劃**協助 John 了解了使用者的喜好傾向。看來有 15% 的無活動使用者回來使用應用程式。不過，這些人大多不會持續使用超過 1 個月。John 預計在透過其他通知並擴大其內容的選擇性後，應該可以讓這個順序變成最好的情況。

**探索計劃**的效果不好。交叉銷售量是增加了，但不足以達到其目標。John 發現他沒有足夠資料能夠鎖定相關目標並提出適當內容。於是他停止這個計劃，並將重心放在使用 Azure Mobile Engagement 傳送「評論推播通知」。他手下的記者已經有 CMS 解決方案可用來傳送推播通知，所以不想做改變。

John 決定使用觸達 API，這種 HTTP REST API 可讓他們管理觸達活動，但又不需要使用 AZME Web 介面。透過這種方式，John 就可以收集所需資料，並讓他的撰稿人能夠繼續使用 CMS 解決方案。

為了確保該功能正常運作，John 要求 IT 小組注意下列事項：

1. **作業系統**：他們各有自己的推播通知管理規則，因此 John 決定列出所有情形，並看看 API 是否能夠處理得來。例如：Android 推播系統允許大型圖片，但 iOS 則不行。

2. **時間範圍**：John 所想要的 API 要能夠設定時間範圍，並設定活動的結束時間。他不希望使用者因為收到接二連三的通知而感覺受到打擾。

3. **類別**：行銷小組準備了每種警示類型的範本。John 要求 IT 小組在 API 內設定類別。

在做過一些測試之後，John 感到非常滿意。多虧有這個 API，記者們仍然可以使用他們的 CMS 傳送推播通知，而 Azure Mobile Engagement 會幫他們收集所有使用行為資料。

在經過最初四個月後，所得到的結果顯示整體成效良好，這讓 John 和他的董事會成員深具信心，每個使用者的 ROI 增加 15%，行動銷售佔總銷售量 17.5%，僅僅四個月就增加了 7.5%。

<!--Image references-->
[1]: ./media/mobile-engagement-media-scenario/engagement-strategy.png
[2]: ./media/mobile-engagement-media-scenario/push-scenarios.png

<!--Link references-->
[Media Playbook link]: https://github.com/Azure/azure-mobile-engagement-samples/tree/master/Playbooks

<!---HONumber=AcomDC_1217_2015-->