<properties 
	pageTitle="設定和使用 Machine Learning Recommendations API 的常見問題集 |Azure" 
	description="以 Azure Machine Learning 建置之 Microsoft RECOMMENDATIONS API 的常見問題集" 
	services="machine-learning" 
	documentationCenter="" 
	authors="jaymathe" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/30/2014" 
	ms.author="jaymathe"/> 

# 設定和使用 Machine Learning Recommendations API 的常見問題集
1.0 版

目錄

* 什麼是 RECOMMENDATIONS？	
* 我可以用 RECOMMENDATIONS 來做什麼？	
* 在推出期間是否有任何限制？	
* 購買和計費	
	* 在推出期間，RECOMMENDATIONS 的價格為何？	
	* 讓 RECOMMENDATIONS 為我追蹤和/或儲存使用者活動是否有任何相關聯的成本？	
	* RECOMMENDATIONS 是否有免費試用版？	
	* 推出期間何時會結束？	
	* 公開上市 (GA) 後的價格為何？	
	* RECOMMENDATIONS 何時會計費？	
	* 如何升級至較高層的服務？	
	* 我的 RECOMMENDATIONS 訂閱何時會結束？	
	* 如何取消我的 RECOMMENDATIONS 訂閱？	
* 開始使用 RECOMMENDATIONS	
	* RECOMMENDATIONS 是給我的嗎？	
	* 設定 RECOMMENDATIONS 的必要條件為何？	
	* 我如何在第一次設定 RECOMMENDATIONS？	
	* 哪裡可以找到 API 文件？	
	* 有哪些選項可將目錄和使用資料上傳至 RECOMMENDATIONS？	
* 維護與支援	
	* 我可以有多大的資料集？	
	* 哪裡可以取得 RECOMMENDATIONS 的技術支援？	
	* 哪裡可以找到使用條款？	
* 法律	


## 什麼是 RECOMMENDATIONS？
對於依賴建議對客戶進行交叉銷售和向上銷售的組織和企業，Microsoft Azure Machine Learning 的 RECOMMENDATIONS 是透過 Azure 的自助建議引擎。它是「協同過濾」的實作，其使用「矩陣分解」作為核心演算法。應用程式開發人員可透過 RESTful HTTP 介面以 API 形式進行存取。RECOMMENDATIONS 目前為公開預覽版，即將成為公開上市的服務。

## 我可以用 RECOMMENDATIONS 來做什麼？
RECOMMENDATIONS 將一個項目或一組項目作為輸入，並傳回相關建議清單。例如：線上零售商的客戶按一下產品。線上零售商會將該產品當作輸入傳送至 RECOMMENDATIONS，取得回報的產品清單，並決定要向客戶顯示哪些產品。您可以使用 RECOMMENDATIONS 來最佳化您的線上商店，或甚至通知您的內部銷售部門或客服中心。


## 在推出期間是否有任何限制？

* 每一訂閱的模型數上限： 10
* 一個目錄可保留的項目數上限： 100,000
* 可以利用 POST 傳送 (例如：匯入目錄資料、匯入使用資料) 的資料大小上限為 200 MB
* 不在作用中的建議模型組建的每秒交易數是 ~2TPS；只有作用中的建議模型組建可以保留高達 20TPS

## 購買和計費 
在這裡可以找到有關了解訂閱的詳細資訊。

###在推出期間，RECOMMENDATIONS 的價格為何？
RECOMMENDATIONS 是一項以訂閱為基礎的服務。收費是根據每個月的交易量來進行。在推出期間，此服務是免費的，每個月限制為 100000 筆交易。

###讓 RECOMMENDATIONS 為我追蹤和/或儲存使用者活動是否有任何相關聯的成本？
目前沒有。

###RECOMMENDATIONS 是否有免費試用版？
在推出期間，此服務是免費的，每個月限制為 100000 筆交易。

###推出期間何時會結束？
推出期間與其價格應該會持續幾個月。我們將會通知訂閱的使用者有關狀態和價格的後續變更。

###公開上市 (GA) 後的價格為何？
尚未決定 RECOMMENDATIONS 公開上市後的價格。價格變更時，將會通知訂閱者。

###RECOMMENDATIONS 何時會計費？
付費的訂閱就是任何按月計價的訂閱。當您購買付費的訂閱時，我們會立即向您收取第一個月的使用費用。您需支付訂閱頁面上提供項目旁邊顯示的金額 (加上適用稅額)。這筆每月費用會每個月在與您原先購買相同的行事曆日期收取，直到您取消訂閱為止。如需有關了解訂閱的詳細資訊，請按一下這裡。

###如何升級至較高層的服務？
在推出期間，此服務限制為每個月 100000 筆交易，不可升級至較高的層次。

當 RECOMMENDATIONS 公開上時時 - 如果發現您的訂閱未提供給您足夠的交易，您可以升級至具有較高交易限制的訂閱。

當您升級訂閱時：

* 舊訂閱剩餘的交易不會加至新的訂閱。 
* 即使您的舊訂閱有未使用的交易，仍需支付新訂閱的完整價格。

升級訂閱的程序

* 登入 Marketplace。
* 按一下 [我的資料] 索引標籤。
* 按一下您要升級的訂閱後面的 [使用]。 
* 右窗格中會列出所有可用的訂閱，但您的訂閱呈現灰色。按一下您要升級至的訂閱的選項按鈕。
* 如果您想要升級，請按一下對話方塊上的 [確定]。如果您不想要升級，請按一下 [取消]。

重要事項：升級前，請先仔細閱讀對話方塊，因為其中有計費和使用暗示。

如需有關了解訂閱的詳細資訊，請按一下[這裡](http://msdn.microsoft.com/library/gg312164.aspx)。

###我的 RECOMMENDATIONS 訂閱何時會結束？
您的訂閱會在您取消時結束。如果您想要取消您的訂閱，請參閱下列指示。

###如何取消我的 RECOMMENDATIONS 訂閱？
若要取消您的訂閱，請遵循下列步驟。如果您目前的訂閱是付費的訂閱，則您的訂閱會持續有效，直到目前的計費期間結束為止。如果您需要取消立即生效，請[在此](https://support.microsoft.com/oas/default.aspx?gprid=17024&st=1&wfxredirect=1&sd=gn)與我們連絡。

如果您在計費期間結束前取消，或對於計費期間內未使用的交易，均不會提供退款。

* 登入 Marketplace。
* 按一下 [我的資料] 索引標籤。
* 找出您想要取消的訂閱。
* 按一下資料集名稱和狀態右邊的 [取消]。您可以使用此訂閱，直到目前的計費期間結束或達到您的交易限制為止 - 以較早發生者為準。

如果您想要立即取消訂閱，以便購買新的訂閱，請移至[這裡](https://support.microsoft.com/oas/default.aspx?gprid=17024&st=1&wfxredirect=1&sd=gn)向我們的客戶支援提出票證。


## 開始使用 RECOMMENDATIONS

###RECOMMENDATIONS 是給我的嗎？ 
Microsoft Azure Machine Learning 的 RECOMMENDATIONS 適用於依賴建議向客戶進行交叉銷售和向上銷售的組織和企業。如果您有面向客戶的網站、銷售團隊、內部銷售團隊或客服中心，而且如果您提供的目錄有好幾十項產品或服務 - 您的盈餘即可受惠於取用 RECOMMENDATIONS。實驗 RECOMMENDATIONS 的設計相當簡單。目前的 API 版本可執行必要的基本程式設計技能。如需協助，請連絡您網站的開發廠商。如果您有內部 IT 部門或內部的開發人員 - 他們應可為您取得 RECOMMENDATIONS。 

###設定 RECOMMENDATIONS 的必要條件為何？
RECOMMENDATIONS 要求您有與您的目錄相關的使用者選擇記錄檔。如果您沒有這類記錄檔，而且有面向客戶的網站，則 RECOMMENDATIONS 可為您收集使用者活動。RECOMMENDATIONS 也需要您的產品或服務目錄。如果您沒有目錄，RECOMMENDATIONS 可以使用實際的客戶使用資料並摘錄成目錄。「隱含」目錄不包含未「回報」成為使用者交易一部分的項目。

###我如何在第一次設定 RECOMMENDATIONS？
訂閱 RECOMMENDATIONS 之後，您應使用  [這裡](https://onedrive.live.com/view.aspx?cid=8536718B52F71725&resid=8536718B52F71725!118&app=Word&authkey=!AOLb7aR6D0cVqMQ)的 API 文件來設定服務。

###哪裡可以找到 API 文件？ 
API 文件在[這裡](https://onedrive.live.com/view.aspx?cid=8536718B52F71725&resid=8536718B52F71725!118&app=Word&authkey=!AOLb7aR6D0cVqMQ)。

###有哪些選項可將目錄和使用資料上傳至 RECOMMENDATIONS？
用於上傳目錄和使用資料的選項有兩個：從 CRM 系統或其他記錄檔匯出這些資料並上傳至 RECOMMENDATIONS，或者您可將標記加入至您的網站以便追蹤使用者活動。若為後者，資料會儲存在 Azure 上。

##維護與支援

###我可以有多大的資料集？
每個資料集可包含多達 100000 個目錄項目以及高達 2048 MB 的使用資料。
此外，一個訂閱最多可包含 10 個資料集 (模型)。

###哪裡可以取得 RECOMMENDATIONS 的技術支援？
技術支援可在[這裡](https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=MachineLearning)取得。

###哪裡可以找到使用條款？

使用條款可在[這裡](https://datamarket.azure.com/dataset/amla/recommendations#terms)取得。

#法律
這份文件係依 「現狀」提供。本文件中說明的資訊與畫面 (包括 URL 及其他網際網路網站參考資料) 如有變更， 恕不另行通知。 
此處描述的一些範例僅供說明之用，純屬虛構。並未影射或關聯任何真實人事物。 
本文件未提供給您任何 Microsoft 產品中任何智慧財產的任何法定權利。您可以複製並使用這份文件，供內部參考之用。 
(c) 2014 Microsoft.著作權所有，並保留一切權利。 



<!--HONumber=46--> 
