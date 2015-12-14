<properties
   pageTitle="測試 Azure Web 應用程式的效能 |Microsoft Azure"
   description="執行 Azure Web 應用程式效能測試，檢查您的應用程式如何處理使用者負載。測量回應時間及尋找可能表示問題的失敗狀況。"
   services="app-service\web"
   documentationCenter=""
   authors="ecfan"
   manager="douge"
   editor="jimbe"/>

<tags
   ms.service="app-service-web"
   ms.workload="web"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="09/11/2015"
   ms.author="estfan; manasma"/>

# 測試 Azure Web 應用程式在低負載下的效能

在您啟動 Web 應用程式或將更新部署至生產環境之前，請先檢查該應用程式的效能。如此一來，您即可更妥善地評估您的應用程式是否已準備好發行。對您的應用程式更具信心，其可在尖峰使用期間或在您下一波推式行銷時處理流量。

在公開預覽期間，您可以在 Azure 入口網站中免費測試您的應用程式的效能。這些測試會模擬您的應用程式在特定期間內的使用者負載並測量您的應用程式的回應。例如，您的測試結果會顯示您的應用程式對指定數量的使用者的回應速度。也會顯示多少要求失敗，這可能表示您的應用程式有問題。

![尋找您的 Web 應用程式中的效能問題][TestOverview]

## 開始之前

*	您將需要 [Azure 訂用帳戶][AzureSubscription] (如果您還沒有的話)。了解如何[免費申請 Azure 帳戶][AzureFreeTrial]。

*	您需要 [Visual Studio Team Services (VSTS)][WhatIsVSTS] 帳戶才能保留您的效能測試記錄。請在設定效能測試時建立新帳戶，如果您是帳戶擁有者，請使用現有的帳戶。[Visual Studio Team Service 帳戶還有什麼用途？](#VSTSAccount)

*	部署您的應用程式以便在非生產環境中進行測試。讓您的應用程式使用生產環境中所用方案以外的 App Service 方案。這樣一來，您不會影響任何現有的客戶或讓您的應用程式在生產環境中變慢。

## 設定和執行效能測試

0.	登入 [Azure 入口網站][AzurePortal]。若要使用您所擁有的 Visual Studio Team Services 帳戶，請以帳戶擁有者的身分登入。

0.	移至您的 Web 應用程式。

	![請移至 [全部瀏覽]、[Web Apps]、您的 Web 應用程式][WebApp]

0.	移至 [效能測試]。

	![移至 [工具]、[效能測試]][ExpandedTools]
 
0.	現在您需要 [Visual Studio Team Services (VSTS)][WhatIsVSTS] 帳戶才能保留您的效能測試記錄。

	如果您已經有 VSTS 帳戶，請選取該帳戶。如果沒有，請建立新帳戶。

	![選取現有的 VSTS 帳戶，或建立新的帳戶][ExistingNewVSTSAccount]

0.	建立您的效能測試。設定詳細資料並執行測試。您可以在測試執行時即時觀看結果。

	例如，假設我們有在去年節日特賣時發放優待券的應用程式。這個活動持續了 15 分鐘，尖峰負載為 100 位並行客戶。我們希望今年的客戶數變成兩倍。我們還想讓頁面載入時間從 5 秒降低為 2 秒，以改善客戶滿意度。因此，我們將以 250 名使用者測試更新後應用程式的效能達 15 分鐘之久。

	我們會產生同時造訪我們的網站的虛擬使用者 (客戶)，藉此模擬應用程式的負載。這會顯示有多少個要求失敗或回應速度較慢。

	![設定、設定和執行效能測試][NewTest]

	 *	系統會自動加入您的 Web 應用程式的預設 URL。您可以變更此 URL 以測試其他頁面 (僅限 HTTP GET 要求)。

	 *	若要模擬本機情況並降低延遲，請選取最靠近使用者的位置來產生負載。

	以下是進行中的測試。在第一分鐘內，我們的頁面載入速度低於預期。

	![使用即時資料的進行中效能測試][TestRunning]

	測試完成後，我們了解頁面載入速度在第一分鐘後變快許多。這有助於找出我們要開始排解疑難問題的位置。

	![完成的效能測試會顯示結果，包括失敗的要求][TestDone]
	
歡迎您提供的意見反應。如有疑問或問題，請與我們連絡︰<vsoloadtest@microsoft.com>

##	問答集

#### 問：我可持續執行測試的時間是否有所限制？ 

答：是，您最多可以在 Azure 入口網站中執行一小時的測試。

#### 問：我可執行效能測試的時間有多少？ 

答：公開預覽後，透過您的 Visual Studio Team Services 帳戶，您每個月可免費取得 20,000 虛擬使用者分鐘數 (VUM)。VUM 是虛擬使用者數目乘以您測試中的分鐘數。如果您的需求超過免費限制，您可以購買更多時間，而且僅支付您所用的時間。

#### 問：哪裡可以檢查到目前為止我已使用多少 VUM？

答：您可以在 Azure 入口網站中檢查此數量。

![移至 VSTS 帳戶][VSTSAccount]

![檢查已使用的 VUM][CheckTestTime]

<a name="VSOAccount"></a>
#### 問：Visual Studio Team Services 帳戶還有什麼用途？

答：若要尋找您的新帳戶，請移至 ```https://{accountname}.visualstudio.com```。使用任何工具或語言來共用您的程式碼、建置、測試、追蹤工作及軟體出貨 – 一切盡在雲端。深入了解 [Visual Studio Team Services][WhatIsVSTS] 功能及服務如何協助您的小組更輕鬆地共同作業及持續進行部署。

<!--Image references-->
[WebApp]: ./media/app-service-web-app-performance-test/azure-np-web-apps.png
[TestOverview]: ./media/app-service-web-app-performance-test/azure-np-perf-test-overview.png
[ExpandedTools]: ./media/app-service-web-app-performance-test/azure-np-web-app-details-tools-expanded.png
[ExistingNewVSTSAccount]: ./media/app-service-web-app-performance-test/azure-np-no-vso-account.png
[NewTest]: ./media/app-service-web-app-performance-test/azure-np-new-performance-test.png
[TestRunning]: ./media/app-service-web-app-performance-test/azure-np-running-perf-test.png
[TestDone]: ./media/app-service-web-app-performance-test/azure-np-perf-test-done.png
[VSTSAccount]: ./media/app-service-web-app-performance-test/azure-np-vso-accounts.png
[CheckTestTime]: ./media/app-service-web-app-performance-test/azure-np-vso-accounts-vum-summary.png

<!--Reference links -->
[AzurePortal]: https://portal.azure.com
[AzureSubscription]: https://account.windowsazure.com/subscriptions
[AzureFreeTrial]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[WhatIsVSTS]: https://www.visualstudio.com/products/what-is-visual-studio-online-vs

<!---HONumber=AcomDC_1203_2015-->