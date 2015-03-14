<properties 
	pageTitle="發佈和使用 Azure Marketplace 中機器學習應用程式的常見問題集 | Azure" 
	description="常見問題集" 
	services="machine-learning" 
	documentationCenter="" 
	authors="luisca" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/10/2015" 
	ms.author="luisca"/>

#發佈和使用 Azure Marketplace 中機器學習應用程式的常見問題集

##從 Marketplace 取用


###問題 1：在輸入 Web 服務的輸入後，我看到下列錯誤。為什麼我會收到這則錯誤訊息？
要求產生後端逾時或後端錯誤。小組正在調查這個問題。很抱歉造成您的不便。 (500)
答：您的輸入參數可能不符合特定 Web 服務所需的格式。請參閱對應的文件連結，來尋找輸入參數的正確格式以及此 Web 服務的限制。

###問題 2：如果我複製在 [探索此資料集] 中所看到的 Web 服務 API 連結，並將它貼到另一個瀏覽器視窗或索引標籤上，我應該使用哪些認證來存取結果，以及它們會如何顯示？
答：您應該使用您的 Marketplace 帳戶作為使用者名稱，並使用主要帳戶金鑰作為密碼。您可以在 Web 服務描述下的 [探索此資料集] 頁面上找到主要帳戶金鑰 (按一下 [顯示] 按鈕)。視您所使用的瀏覽器而定，結果可能會顯示在瀏覽器中或可供下載。

###問題 3：在 [探索此資料集] 中輸入 Web 服務的輸入後，我看到下列錯誤。為什麼我會收到這則錯誤訊息？
處理您的要求時發生未預期的錯誤。請再試一次。
答：在 Marketplace [探索此資料集] 頁面上使用 Web 服務時，該服務的一或多個輸入參數可能超過長度限制。使用 HTTP POST 方法可能可以呼叫較長輸入長度的服務。範例程式碼已公佈在此處所述的範例服務下：http://azure.microsoft.com/documentation/articles/machine-learning-r-csharp-web-service-examples/。

###

##從 Marketplace 上的 Azure ML 發佈

###問題 1：為何 Web 服務不會重新整理我的標誌/影像/交易數？ 
答：發佈入口網站中會有標誌/影像的快取，新的標誌/影像可能需要多達 10 天的時間才會在入口網站上更新。

###問題 2：為什麼在 Marketplace 產品上，Web 服務的 [詳細資料] 索引標籤會顯示錯誤？
答：在連接到 Azure ML 以取得服務詳細資料時，會發生一個已知的 Marketplace 問題。小組正著手解決這個問題。

###問題 3：為什麼在取用 Marketplace 中的 Web 服務時，無法使用 Azure ML Web 服務中的 R 範例程式碼？
答：將直接連接到 Azure ML Web 服務與透過 Marketplace 連接到這些 Web 服務進行比較時，兩者有不同的驗證系統。Marketplace 中的服務是 OData 服務，且可以使用 GET 或 POST 方法進行呼叫。 

###問題 4：為何我的 Web 服務產品的支援連結無法正確更新我的部分產品？
答：支援連結是依照發佈者而非依照產品的全域連結。 

###問題 5：如何使用 Marketplace 中的批次輸入模式來發佈 Web 服務？
答：Marketplace Web 服務目前不支援批次輸入模式。

###問題 6：如果我有關於成為資料發佈者的問題，或在發佈過程中發生問題時，我應該與誰連絡以取得協助？
答：請連絡 Marketplace 小組 (datamarketbd@microsoft.com) 以取得詳細資訊。






<!--HONumber=46--> 
