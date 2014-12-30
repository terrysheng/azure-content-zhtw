<properties urlDisplayName="index" pageTitle="如何管理媒體服務帳戶中的原始來源" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="How to Manage Streaming Endpoints in a Media Services Account" authors="juliako"  solutions="" writer="juliako" manager="dwrede" editor=""  />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/30/2014" ms.author="juliako" />


<h1><a id="managemediaservicesorigins"></a>如何管理媒體服務帳戶中的串流端點</h1>

媒體服務可讓您在帳戶中新增多個串流端點，以及設定串流端點。每個媒體服務帳戶都至少會與一個名為**預設**的串流端點相關聯。

>[WACOM.NOTE] 串流端點先前稱為「原始來源」。 


<h2>新增及刪除串流端點</h2> 

1. 在[管理入口網站](https://manage.windowsazure.com/)中，按一下 [**媒體服務**]。接著，按一下媒體服務的名稱。
2. 選取 [串流端點] 頁面。 
3. 按一下頁面底部的 [新增] 或 [刪除] 按鈕。請注意，預設串流端點不可刪除。 
4. 按一下 [啟動] 按鈕以啟動串流端點。 
5. 按一下串流端點的名稱加以設定。   

	![Origin page][origin-page]

<h2>設定串流端點</h2>

[設定] 索引標籤可讓您執行下列設定：

1. 設定會在 HTTP 回應的 cache control 標頭中指定的快取期間上限。此值將不會覆寫在 Blob 內容上明確設定的快取值上限。

2. 指定能夠連接到已發佈之串流端點的 IP 位址。若未指定 IP 位址，則任何 IP 位址都可連接。

3. 為 Akamai 簽章標頭驗證指定組態。



	![Configure origin][configure-origin]

	此頁面上的組態只會套用到至少有一個保留單元的串流端點。若要保留隨選串流保留單元，請移至 [調整] 索引標籤。如需保留單元的詳細資訊，請參閱[調整媒體服務] (英文)(../media-services-how-to-scale/)。


[origin-page]: ./media/media-services-manage-origins/media-services-origins-page.png
[configure-origin]: ./media/media-services-manage-origins/media-services-origins-configure.png

<!--HONumber=35_1-->
