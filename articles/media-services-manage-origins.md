<properties 
	pageTitle="如何在媒體服務帳戶中管理串流端點" 
	description="本主題說明如何使用 Azure 管理入口網站管理串流端點。" 
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
	ms.date="02/15/2015" 
	ms.author="juliako"/>


# <a id="managemediaservicesorigins"></a>如何在媒體服務帳戶中管理串流端點

這篇文章是[媒體服務點播視訊工作流程](../media-services-video-on-demand-workflow)和[媒體服務即時串流工作流程](../media-services-live-streaming-workflow)系列的一部分。  


在媒體服務中，串流端點代表可以直接將內容傳遞給用戶端播放程式應用程式，或傳遞給內容傳遞網路 (CDN) 進行進一步發佈的串流服務。目前 Microsoft Azure 媒體服務不提供順暢的 CDN 整合，但您可以利用市場上的 CDN 提供者 (Azure CDN 或 Akamai)。從串流端點服務的輸出串流可以是即時串流或媒體服務帳戶中的隨選視訊資產。 

此外，您可以藉由調整縮放單位 (也稱為串流單位)，控制串流端點服務處理成長頻寬需求的產能。建議您為生產環境中的應用程式配置一個或多個縮放單位。縮放單位提供您可以以 200 Mbps 為增量購買的專用流出容量和目前包含動態封裝的其他功能。 

本主題說明如何使用 Azure 管理入口網站管理串流端點。


## 新增及刪除串流端點 

1. 在 [[管理入口網站](https://manage.windowsazure.com/)]，按一下 [**媒體服務**]。接著，按一下媒體服務的名稱。
2. 選取 [**串流端點**] 頁面。 
3. 按一下頁面底部的 [新增] 或 [刪除] 按鈕。請注意，預設串流端點不可刪除。 
4. 按一下 [啟動] 按鈕以啟動串流端點。 
5. 按一下串流端點的名稱加以設定。   

	![Origin page][origin-page]

## <a id="scale_streaming_endpoints"></a>調整串流端點

串流單位提供您可以以 200 Mbps 為增量購買的專用流出容量和目前包含[動態封裝功能](http://go.microsoft.com/fwlink/?LinkId=276874)的其他功能。依預設，串流是以共用執行個體模型來設定，可與其他所有使用者共用伺服器資源 (例如，運算、流出容量等)。若要改善串流的輸送量，建議購買串流單位。 

若要變更串流單位數目，請執行下列動作：

1. 若要指定串流單位數目，請選取 [調整] 索引標籤，然後移動 [**保留容量**] 滑桿。

	![Scale page](./media/media-services-how-to-scale/media-services-origin-scale.png)

4. 按 [儲存] 以儲存您的變更。

	任何新的串流單位配置需要大約 20 分鐘的時間才能完成。 

	 
	>[AZURE.NOTE] 目前，串流單位從任何正數值到無，都可能停用隨選串流長達一小時。

	>[AZURE.NOTE] 計算成本時會使用 24 小時內指定的最大單位數。如需定價詳細資料，請參閱[媒體服務定價詳細資料](http://go.microsoft.com/fwlink/?LinkId=275107)。
	
## 設定串流端點

[設定] 索引標籤可讓您執行組態，此圖所示。欄位的說明如下。

>[AZURE.NOTE] 此頁面上的組態只會套用到至少有一個保留單元的串流端點。保留隨選串流保留單元。

![Configure origin][configure-origin]
  

1. 設定會在 HTTP 回應的 cache control 標頭中指定的快取期間上限。此值將不會覆寫在 Blob 內容上明確設定的快取值上限。

2. 指定能夠連接到已發佈之串流端點的 IP 位址。若未指定 IP 位址，則任何 IP 位址都可連接。

3. 為 Akamai 簽章標頭驗證指定組態。

4. 您可以指定 Adobe Flash 用戶端的跨網域存取原則 (如需詳細資訊，請參閱[跨網域原則檔案規格](http://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html)。以及 Microsoft Silverlight 用戶端的用戶端存取原則 (如需詳細資訊，請參閱[讓服務可跨網域界限使用](https://msdn.microsoft.com/zh-tw/library/cc197955(v=vs.95).aspx)。  

5. 您也可以按一下 [**設定**] 按鈕，設定自訂主機名稱。如需詳細資訊，請參閱**StreamingEndpont** 主題中的 [CustomHostNames](https://msdn.microsoft.com/zh-tw/library/dn783468.aspx) 屬性。  



[origin-page]: ./media/media-services-manage-origins/media-services-origins-page.png
[configure-origin]: ./media/media-services-manage-origins/media-services-origins-configure.png
[configure-origin-configure-custom-host-names]: ./media/media-services-manage-origins/media-services-configure-custom-host-names.png

<!--HONumber=45--> 
