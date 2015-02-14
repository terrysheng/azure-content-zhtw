<properties 
	pageTitle="如何管理媒體服務帳戶中的原始來源" 
	description="" 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	writer="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/29/2015" 
	ms.author="juliako"/>


# <a id="managemediaservicesorigins"></a>如何管理媒體服務帳戶中的串流端點

媒體服務可讓您在帳戶中新增多個串流端點，以及設定串流端點。每個媒體服務帳戶都至少會與一個名為**預設**的串流端點相關聯。

>[AZURE.NOTE] 串流端點先前稱為「原始來源」。 


## 新增及刪除串流端點 

1. 在[管理入口網站](https://manage.windowsazure.com/)中，按一下 [**媒體服務**]。接著，按一下媒體服務的名稱。
2. 選取 [串流端點] 頁面。 
3. 按一下頁面底部的 [新增] 或 [刪除] 按鈕。請注意，預設串流端點不可刪除。 
4. 按一下 [啟動] 按鈕以啟動串流端點。 
5. 按一下串流端點的名稱加以設定。   

	![Origin page][origin-page]

## 調整串流端點

串流單元為您提供專用的流出容量 (以 200 Mbps 為增量來購買)  和其他功能 (目前包括[動態封裝功能](http://go.microsoft.com/fwlink/?LinkId=276874))。依預設，隨選串流是以共用執行個體模型來設定，可與其他所有使用者共用伺服器資源 (例如，運算、流出容量等)。若要改進隨選串流輸送量，建議購買串流單元。 

若要變更串流單元的數目，請執行下列動作：

1. 若要指定串流單元數目，請選取 [調整] 索引標籤，然後移動 [**reserved capacity**] 滑動軸。

	![Scale page](./media/media-services-how-to-scale/media-services-origin-scale.png)

4. 按 [儲存] 以儲存您的變更。

	任何新的串流單元大約都需要 20 分鐘才能配置完成。 

	 
	>[AZURE.NOTE] 目前，從串流單元的任何正值回到無，可能會使隨選串流停用長達一小時。

	>[AZURE.NOTE] 計算成本時會使用 24 小時內指定的最大單元數。如需定價詳細資料的相關資訊，請參閱[媒體服務定價詳細資料](http://go.microsoft.com/fwlink/?LinkId=275107)。
	
## 設定串流端點

[設定] 索引標籤可讓您執行組態，如此影像中所示。隨後是欄位的說明。

>[AZURE.NOTE] 此頁面上的組態只會套用到至少有一個保留單元的串流端點。若要保留隨選串流保留單元：

![Configure origin][configure-origin]
  

1. 設定會在 HTTP 回應的 cache control 標頭中指定的快取期間上限。此值將不會覆寫在 Blob 內容上明確設定的快取值上限。

2. 指定能夠連接到已發佈之串流端點的 IP 位址。若未指定 IP 位址，則任何 IP 位址都可連接。

3. 為 Akamai 簽章標頭驗證指定組態。

4. 您可以指定 Adobe Flash 用戶端的跨網域存取原則 (如需詳細資訊，請參閱[跨網域原則檔案規格](http://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html))，以及 Microsoft Silverlight 用戶端的用戶端存取原則 (如需詳細資訊，請參閱[讓服務可跨網域界限使用](https://msdn.microsoft.com/zh-tw/library/cc197955(v=vs.95).aspx)。  

5. 您也可以按一下 [**設定**] 按鈕，以設定自訂主機名稱。如需詳細資訊，請參閱 [StreamingEndpont](https://msdn.microsoft.com/zh-tw/library/dn783468.aspx) 主題中的 **CustomHostNames** 屬性。  




[origin-page]: ./media/media-services-manage-origins/media-services-origins-page.png
[configure-origin]: ./media/media-services-manage-origins/media-services-origins-configure.png
[configure-origin-configure-custom-host-names]: ./media/media-services-manage-origins/media-services-configure-custom-host-names.png
<!--HONumber=42-->
