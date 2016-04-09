<properties
	pageTitle="比較 Azure Mobile Engagement 與其他類似的 Azure 服務"
	description="比較 Azure Mobile Engagement 與其他類似的 Azure 服務- HockeyApp、AppInsights、通知中樞"
	services="mobile-engagement"
	documentationCenter="mobile" 
	authors="piyushjo" 
	manager="erikre" 
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/03/2016"
	ms.author="piyushjo" />

# 比較 Azure Mobile Engagement 與其他類似的 Azure 服務

Microsoft Azure 所提供之軟體的清單會不斷擴大，且您有時可能會想要知道 Azure Mobile Engagement 和您剛剛閱讀或聽過的其他服務有什麼不同。此文章會嘗試釐清您的疑惑，並在 Azure Mobile Engagement 是最適合您使用的服務時，指示您選擇 Azure Mobile Engagement。
 
Azure Mobile Engagement 是針對**數位行銷人員/CMO** 特別提供的服務，但任何想要提高行動應用程式使用量、忠誠度及營收的**行動應用程式擁有者或發行者**也都可以使用。

它是軟體即服務 (SaaS) 的使用者參與平台，可針對應用程式使用量和即時使用者區隔提供資料導向的深入解析，以及啟用內容感知的推播通知及應用程式內的傳訊功能。

除了此定義之外，它還具有以下重要特性，展現其獨特的價值主張：

1.	**可感知上下文的推播通知和應用程式內傳訊**
		
	這是產品的其中一項核心功能 - 執行目標性和個人化的推播通知。而且為了達到此目標，我們收集了豐富的行為分析資料。

2.	**資料導向深入解析應用程式使用狀況**

	我們提供跨平台 SDK 來收集與應用程式使用者有關的行為分析。請注意「行為分析」(針對效能分析) 一詞，因為我們是著重在應用程式使用者如何使用應用程式。我們確實會收集與錯誤、當機等等有關的基本效能分析資料，但那不是本產品的核心重點。

3.	**即時使用者區隔**

	在您收集應用程式使用者的行為分析資料之後，我們可以讓您依據各種參數與收集的資料區隔您的對象，讓您能夠執行目標性的推播活動。

4.	**軟體即服務 (SaaS)：**

	我們有一個和 Azure 管理入口網站不同的入口網站，該入口網站已經針對和應用程式使用者相關的豐富行為分析互動及加以檢視，以及針對執行行銷推播活動最佳化。產品已經準備完成，隨時可供您使用！
 
了解這些差異之後，以下會說明我們如何與其他看起來類似的 Azure 產品做比較 - 請注意，本文並不做功能層級比較，但會透過以案例為基礎的方式定義哪一個產品最適用：
 
1.	[HockeyApp](https://azure.microsoft.com/services/hockeyapp/) 是 Microsoft 的行動 DevOps 方案。利用該解決方案，您可以將組建散發給 Beta 版測試人員、收集損毀資料，以及取得使用者意見反應。它也會與 Visual Studio Team Services 整合，讓您能夠輕鬆建置部署和工作項目整合。 
	
	這裡的重點在 DevOps 及收集與行動應用程式有關的效能分析資料。您最後可能會在您的應用程式中整合 HockeyApps 與 Mobile Engagement，而且那將會是很正常的情況，因為即使收集的資料中有一些重疊，但這兩項產品的核心重點是不一樣的，因此它們可以協助您達到不同的目標。

2.	[Application Insights](../application-insights/app-insights-overview.md) 如果您的行動應用程式有伺服器端，您可以使用 Application Insights 監視應用程式的 Web 伺服器端，但對於用戶端行動應用程式，您應該使用 HockeyApp。

3.	[通知中樞](https://azure.microsoft.com/services/notification-hubs/)可在您不需要於行動應用程式中整合 SDK 的情況下提供輕量服務，並且您可以完全控制您的行動應用程式，而且它允許傳送具有基本標記功能的推播通知。對於不在乎目標，比較在乎立即傳送/傳達資訊給應用程式使用者 (向大量使用者廣播) 的任何應用程式擁有者來說，這是很棒的功能。

	請注意，這裡的重點在於傳送具有基本區隔能力的超快速通知。

我們來看一些案例：

1.	Tim 是 Adventure Works 商店中負責為使用者發佈行動應用程式之數位行銷團隊的一員。Tim 的目標是確保下載其行動應用程式的使用者會繼續並頻繁使用應用程式。這不只是要提高品牌與應用程式使用者的黏著度，也要在應用程式使用者使用行動應用程式購買商品時提高獲利。因此，Tim 需要對應用程式使用者傳送會讓他們覺得很有用的目標性通知，以鼓勵使用者開啟應用程式，以及經常回到應用程式。這會是 Tim 將發現 Mobile Engagement 很有用的地方。 

2.	Joann 是 Adventure Works 中開發團隊的一員，而且正在尋找能夠記錄當機例外狀況相關詳細資料，以及能夠從應用程式取得效能遙測資料的產品。這是 Joann 將發現 HockeyApp 很有用的地方。Joann 可以在相同的應用程式中為其開發人員的重點案例整合 HockeyApp，以及為 Tim 整合 Azure Mobile Engagement，來同時提供兩者最好用的功能。

3.	Robin 是 Contoso News 的行動應用程式網路開發團隊的一員，她最想要的功能是在有新聞警示被觸發時，在沒有大幅度區隔使用者的情況下，盡快向所有使用者傳送重大新聞警示。這是 Robin 將發現「通知中樞」很有用的地方。但是在此案例中，隨著應用程式漸趨成熟，還是有可能需要執行更多樣化的區隔，並取得與應用程式使用者行為有關的詳細資料。此時，Robin 將必須評估 Azure Mobile Engagement。
 
總而言之，Mobile Engagement 的用途不止是用來收集分析 - 它「還不是 Microsoft 的另一項分析產品」。這與傳送目標性推播通知，以及我們為此目標性通知收集行為分析資料有關，但是重點還是在於向應用程式使用者傳送最有意義的推播通知，以避免通知變成垃圾通知。

如需詳細資料，請參考這段有關 Mobile Engagement 簡略說明的[快速上手影片](mobile-engagement-overview.md)。

<!---HONumber=AcomDC_0309_2016-->