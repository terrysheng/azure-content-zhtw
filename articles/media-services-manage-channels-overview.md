<properties 
	pageTitle="使用內部部署編碼器，將多位元速率即時資料流傳送至通道" 
	description="本主題描述如何讓通道接收內部部署編碼器的多位元速率即時資料流。資料流可以透過一或多個串流端點並利用以下其中一種調適性串流通協定，再傳遞給用戶端播放應用程式：HLS、Smooth Stream、MPEG DASH、HDS。" 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="ne" 
	ms.topic="article" 
	ms.date="04/06/2015" 
	ms.author="juliako"/>

# 使用內部部署編碼器，將多位元速率即時資料流傳送至通道

## 概觀

在 Azure 媒體服務 (AMS) 中，**通道**代表一個管線，負責處理即時資料流內容。**程式**可讓您控制即時資料流中區段的發佈和儲存。通道會管理程式。通道和程式的關聯性非常類似於傳統媒體，此處的通道有常數內容資料流，而程式的範圍是該通道上的某些計時事件。 

使用即時資料流時，工作流程涉及到的元件之一就是即時視訊編碼器，它會將相機中的訊號轉換成資料流，然後再傳送至即時資料流服務。內部部署即時編碼器負責輸出多位元速率 RTMP 或 Fragmented MP4 (Smooth Streaming) 資料流，而**通道**就是從這個內部部署即時編碼器接收即時輸入資料流。您可以使用下列可以輸出 Smooth Streaming 的即時編碼器：Elemental、Envivio、Cisco。下列即時編碼器可以輸出 RTMP：Adobe Flash Live、Telestream Wirecast 和 Tricaster 轉碼器。內嵌的資料流可以通過**通道**，無需進一步的處理。即時編碼器也可以內嵌單一位元速率資料流，但是因為資料流未經處理，因此用戶端應用程式也會收到單一位元速率資料流 (不建議這個選項)。當收到的內容發行之後，就可以透過一或多個**串流端點**傳送至用戶端播放應用程式。下列調適性串流處理通訊協定可以用來播放串流：HLS、MPEG DASH、Smooth Stream、HDS。 

**串流端點**代表可以直接將內容傳遞給用戶端播放程式應用程式，或傳遞給內容傳遞網路 (CDN) 進行進一步發佈的串流服務。從串流端點服務的輸出串流可以是即時資料流，或媒體服務帳戶中的隨選視訊資產。此外，您可以藉由調整串流保放留單位，控制串流端點服務如何應付不斷增加的頻寬需求。您應該為生產環境中的應用程式，至少配置一個保留單元。如需詳細資訊，請參閱[如何調整媒體服務](media-services-manage-origins.md#scale_streaming_endpoints)。

下圖顯示一個即時資料流工作流程，這個流程利用即時編碼器輸出多位元速率 RTMP 或 Fragmented MP4 (Smooth Streaming) 資料流。 

![即時工作流程][live-overview]

本主題簡介如何讓通道接收內部部署編碼器的多位元速率即時資料流。本主題也會討論與通道有關的元件。

## <a id="scenarios"></a>常見的即時資料流案例

下列步驟描述當我們建立一般即時資料流應用程式時，會涉及到的各種工作。

1. 將攝影機連接到電腦。啟動並設定內部部署即時編碼器，讓它輸出多位元速率 RTMP 或 Fragmented MP4 (Smooth Streaming) 資料流。如需詳細資訊，請參閱 [Azure 媒體服務 RTMP 支援和即時編碼器](http://go.microsoft.com/fwlink/?LinkId=532824)。
	
	當您建立通道之後，也可以執行這個步驟。

1. 建立以及啟動通道。
1. 擷取通道內嵌 URL。 

	即時編碼器會利用這個嵌入 URL，然後將串流傳送至通道。
1. 擷取通道預覽 URL。 

	使用此 URL 來確認您的通道可以正確接收即時資料流。

2. 建立資產。
3. 如果您想要在播放期間動態加密資產，請執行下列動作： 	
	
	1.	建立內容金鑰。 
	1.	設定內容金鑰的授權原則。
1. 設定資產傳遞原則 (使用動態封裝和動態加密)。
3. 建立程式並指定要使用您所建立的資產。
1. 建立 OnDemand 定位器，然後發行與程式有關的資產。  

	確定負責傳送內容的串流端點上，至少有一個串流保留單位。
1. 當您準備好開始串流和封存時，啟動程式。
1. 當您想停止串流和封存事件時，停止程式。
1. 刪除程式 (以及選擇性刪除資產)。   

[即時資料流工作](media-services-manage-channels-overview.md#tasks) 區段會連結至主題，而這些主題會示範如何完成上述工作。


## <a id="channel_input"></a>頻道輸入 (內嵌) 組態

### <a id="ingest_protocols"></a>內嵌資料流通訊協定

媒體服務支援各種採用下列資料流通訊協定的內嵌即時訊息來源： 

- **多位元速率 Fragmented MP4**
 
- **多位元速率 RTMP** 

	選取 **RTMP** 內嵌資料流通訊協定時，會為通道建立兩個 ingest(input) 端點： 
	
	**主要URL**：指定通道主要 RTMP 內嵌端點的完整 URL。

	**次要 URL**(選擇性)：指定通道次要 RTMP 內嵌端點的完整 URL。 

	使用次要 URL 來改善內嵌資料流的持久性、容錯以及編碼器容錯移轉和容錯。 
	
	- 若要改善內嵌持久性和容錯：
		
		使用一個編碼器，將相同的資料傳送至主要和次要內嵌 URL。 
大部分的 RTMP 編碼器 (例如，Flash Media Encoder 或 Wirecast) 都可以使用主要和次要 URL。

	- 若要處理編碼器容錯移轉和容錯：
		
		使用多個編碼器來產生相同的資料，然後傳送至主要和次要內嵌 URL。這個方法能夠同時改善內嵌持久性和編碼器高可用性。注意：編碼器需要支援高可用性案例，而且也必須內部同步處理時間 (如需詳細資訊，請參閱您的編碼器手冊)。
	
 	使用次要內嵌 URL 時會佔用額外的頻寬。 

請注意：您可以內嵌單一位元速率至您的通道，不過由於通道尚未處理資料流，所以用戶端應用程式也會收到單一位元速率資料流 (不建議這個選項)。

如需 RTMP 即時編碼器的相關資訊，請參閱 [Azure 媒骿服務 RTMP 支援和即時編碼器](http://go.microsoft.com/fwlink/?LinkId=532824)。

您必須考量下列事項：

- 確定您的網際網路速度夠快，足以將資料傳送至內嵌點。 
- 內送的多位元速率資料流最多可以有 10 個視訊品質等級 (也稱為圖層)，以及最多 5 個音軌。
- 任何視訊品質等級或圖層的最高平均位元速率，應低於 10 Mbps。
- 所有視訊和音訊串流的平均位元速率彙總，應低於 25 Mbps。
- 當通道或其梘關程式執行時，您無法變更輸入通訊協定。如果您需要不同的通訊協定，請為每一個輸入通訊協定建立個別的通道。 


### 內嵌 URL (端點) 

通道提供一個輸入端點 (內嵌 URL)，您可以在即時編碼器中指定這個端點，這樣編碼器就可以將資料流推播到您的通道。   

當您建立通道時，您可以取得內嵌 URL。通道不需處於啟動狀態也可以取得這些 URL。當您準備好要開始推播資料給通道時，就必須先啟動通道。一旦通道開始內嵌資料後，您可以透過預覽 URL 來預覽資料流。

您可以選擇透過 SSL 連線來內嵌 Fragmented MP4 (Smooth Streaming) 即時資料流。若要透過 SSL 擷取，請務必將擷取 URL 更新為 HTTPS。目前，您無法內嵌 RTMP over SSL。 

### <a id="keyframe_interval"></a>主要畫面格間隔

當使用內部部署即時編碼器來產生多位元速率資料流時，主要畫面格間隔會指定 GOP 持續期間 (供該外部編碼器使用)。一旦通道收到此內送資料流時，然後您可以再將即時資料流傳遞至下列任一形式的用戶端播放應用程式：Smooth Streaming、DASH 和 HLS。在執行即時資料流時，會一律動態封裝 HLS。依預設，媒體服務會根據從即時編碼器收到的主要畫面格間隔 (也稱為圖片群組 - GOP)，自動計算 HLS 區段封裝比例 (每一個區段的片段)。 

下表顯示如何計算區段持續時間：

<table border="1">
<tr><th>主要畫面格間隔</th><th>HLS 區段封裝比例 (FragmentsPerSegment)</th><th>範例</th></tr>
<tr><td>小於或等於 3 秒</td><td>3:1</td><td>如果 KeyFrameInterval (或 GOP) 為 2 秒鐘，則預設 HLS 區段封裝比例就會是 3 比 1，這將會建立 6 秒鐘的 HLS 區段。</td></tr>
<tr><td>3 到 5  秒</td><td>2:1</td><td>如果 KeyFrameInterval (或 GOP) 為 4 秒鐘，則預設 HLS 區段封裝比例就會是 2 比 1，這將會建立 8 秒鐘的 HLS 區段。</td></tr>
<tr><td>大於 5 秒</td><td>1:1</td><td>如果 KeyFrameInterval (或 GOP) 為 6 秒鐘，則預設 HLS 區段封裝比例就會是 1 比 1，這將會建立 6 秒鐘的 HLS 區段。</td></tr>
</table>

您可以設定通道的輸出並在 ChannelOutputHls 上設定 FragmentsPerSegment，即可變更每個區段比例的片段。 

您也可以在 ChanneInput 上設定 KeyFrameInterval 屬性，即可變更主要畫面格間隔值。 

如果您明確設定 KeyFrameInterval，就會使用上述的規則計算 FragmentsPerSegment HLS 區段封裝比率。  

如果您明確設定 KeyFrameInterval 和 FragmentsPerSegment，媒體服務會使用您所設定的值。 


### 允許的 IP 位址

您可以定義一些被允許將視訊發佈到這個通道的 IP 位址。允許的 IP 位址可以指定為單一 IP 位址 (例如'10.0.0.1')、使用 IP 位址的 IP 範圍以及 CIDR 子網路遮罩 (例如'10.0.0.1/22')，或使用 IP 位址和小數點十進位子網路遮罩的 IP 範圍 (例如'10.0.0.1(255.255.252.0)')。 

如果未指定 IP 位址而且也未定義規則，則任何 IP 位址都不允許。若要允許任何 IP 位址，請建立規則並設定 0.0.0.0/0。

## 通道預覽 

### 預覽 URL

通道會提供預覽端點 (預覽 URL)，您可在進一步處理和傳遞之前先用來預覽及驗證您的資料流。

當您建立通道時，您可以取得預覽 URL。通道不需處於啟動狀態也可以取得 URL。 

通道開始內嵌資料後，您就可以預覽您的資料流。

請注意，無論指定的輸入類型為何，預覽資料流目前只能利用 Fragmented MP4 (Smooth Streaming) 格式傳遞。您可以使用 [http://smf.cloudapp.net/healthmonitor](http://smf.cloudapp.net/healthmonitor) 播放程式測試 Smooth Stream。您也可以使用 Azure 管理入口網站中架設的播放程式來查看您的資料流。


### 允許的 IP 位址

您可以定義一些被允許連線至預覽端點的 IP 位址。如果未指定 IP 位址，則任何 IP 位址皆不被允許。允許的 IP 位址可以指定為單一 IP 位址 (例如'10.0.0.1')、使用 IP 位址的 IP 範圍以及 CIDR 子網路遮罩 (例如'10.0.0.1/22')，或使用 IP 位址和小數點十進位子網路遮罩的 IP 範圍 (例如'10.0.0.1(255.255.252.0)')。

## 通道輸出

如需詳細資訊，請參閱[設定主要畫面格間隔](#keyframe_interval) 一節。


## 通道的程式

通道會有關聯的程式，讓您可以控制即時資料流片段的發佈和儲存。通道會管理程式。通道和程式的關聯性非常類似於傳統媒體，此處的通道有常數內容資料流，而程式的範圍是該通道上的某些計時事件。

您可以設定 **Archive Window** 屬性，指定記錄的程式內容應保留多久時間。此值最小可以設定為 5 分鐘，最大可以設定為 25 個小時。Archive Window 長度也會指定用戶端可從目前即時位置往回搜尋的最大時間量。程式可以執行超過指定的時間量，但是超過此時間長度的內容會被持續捨棄。此屬性的值也決定用戶端資訊清單可以增長的時間長度。

每個程式都與一個資產相關聯。若要發佈程式，您必須為相關資產建立定位器。擁有此定位器，可讓您建立可以提供給用戶端的串流 URL。

通道最多支援三個同時執行的程式，因此您可以為同一個內送資料流建立多個封存。這可讓您視需要發佈和封存事件的不同部分。比方說，您的業務需求是封存 6 小時的程式，但只廣播最後 10 分鐘。若要達成此目的，您需要建立兩個同時執行的程式。一個程式設定為封存 6 小時的事件，但不發佈該程式。另一個程式設定為封存 10 分鐘，並發佈此程式。

現有的程式請勿重複應用到新的事件。您反而應該按照〈設計即時資料流應用程式〉一節中所述，為每一個事件建立並啟動新的程式。

當您準備好開始串流和封存時，啟動程式。當您想停止串流和封存事件時，停止程式。 

若要刪除封存的內容，請停止並刪除程式，然後再刪除相關的資產。如果某一個程式正在使用資產，請先刪除該程式。 

即使您停止並刪除程式，使用者還是能夠將封存的內容當作是點播視頻傳送出去，而且只要您不刪除資產，時間多久都沒有問題。

如果真的想保留封存的內容，但不想傳送出去，刪除資料流定位器即可。

## 通道狀態

通道的目前狀態。可能的值包括：

- 已停止。這是通道建立後的初始狀態。在此狀態下，通道屬性可以更新，但是不允許傳送資料流。
- 啟動中。通道正在啟動中。在此狀態下，不允許任何更新或傳送資料流。如果發生錯誤，通道會恢復「已停止」狀態。
- 執行中。通道可以處理即時資料流。
- 停止中。正在停止通道中。在此狀態下，不允許任何更新或傳送資料流。
- 刪除中。正在刪除中通道。在此狀態下，不允許任何更新或傳送資料流。 

## 隱藏式字幕和廣告插入 

下表示範支援的隱藏式字幕和廣告插入標準。

<table border="1">
<tr><th>標準</th><th>注意</th></tr>
<tr><td>CEA-708 和 EIA-608 (708/608)</td><td>CEA-708 和 EIA-608 是美國和加拿大的隱藏式字幕標準。<br/>目前只有編碼的輸入資料流附帶字幕時，才能播放字幕。您使用的即時媒體編碼器，必須可以將 608 或 708 字幕插入至已傳送至媒體服務的已編碼資料流。媒體服務會將內含字幕的內容傳遞給您的檢視器。</td></tr>
<tr><td>.ismt 裡面附帶字幕 (Smooth Streaming 文字播放軌)</td><td>媒體服務動態封裝功能可讓您的用戶端傳送以下任何格式的內容：MPEG DASH、HLS 或 Smooth Streaming。不過，如果您內嵌 Fragmented MP4 (Smooth Streaming) 而且在 .ismt 裡面附帶字幕 (Smooth Streaming 文字播放軌)，您就只能將資料流傳遞至 Smooth Streaming 用戶端。</td></tr>
<tr><td>SCTE-35</td><td>數位訊號系統，用來提示廣告插入。下游接收端會使用信號並根據分配的時間，將廣告切割成資料流。SCTE 35 必須以鬆散播放軌的形式傳送至輸入資料流中。<br/>請注意，目前唯一支援附帶廣告訊號的輸入資料流格式是 Fragmented MP4 (Smooth Streaming)。唯一支援的輸出格式也是 Smooth Streaming。</td></tr>
</table>

## 注意事項

使用內部部署即時編碼器並將多位元速率資料流傳送到通道時，請注意以下限制：

- 確定您的網際網路速度夠快，足以將資料傳送至內嵌點。 
- 內送的多位元速率資料流最多可以有 10 個視訊品質等級 (10 個圖層)，以及最多 5 個音軌。
- 任何視訊品質等級或圖層的最高平均位元速率，應低於 10 Mbps。
- 所有視訊和音訊串流的平均位元速率彙總，應低於 25 Mbps。
- 當通道或其梘關程式執行時，您無法變更輸入通訊協定。如果您需要不同的通訊協定，請為每一個輸入通訊協定建立個別的通道。 


以下是其他與通道和相關元件應用有關的注意事項：


- 只有通道處於執行中狀態時，才會向您收取費用。
- 每次重新設定即時編碼器時，請在通道上呼叫 **Reset** 方法。重設通道之前，您必須停止程式。重設通道之後，重新啟動程式。 
- 只有通道處於執行中狀態，而且通道上的所有程式皆已停止，如此才有可能停止通道。
- 依預設，您最多可以在媒體服務帳戶中新增 5 個通道。如需詳細資訊，請參閱[配額和限制](media-services-quotas-and-limitations.md)。
- 當通道或其梘關程式執行時，您無法變更輸入通訊協定。如果您需要不同的通訊協定，請為每一個輸入通訊協定建立個別的通道。 

## <a id="tasks"></a>Live 即時資料流的相關工作

### 設定您的電腦

如需瞭解如何設定您的電腦，請參閱[設定您的電腦](media-services-set-up-computer.md)。

### 設定串流端點

如需有關串流端點以及有管理方法簡介，請參閱[如何管理媒體服務帳戶中的串流處理端點](media-services-manage-origins.md)

### 使用內部部署即時編碼器，將多位元速率資料流輸出至通道

如需詳細資訊，請參閱[搭配使用協力廠商即時編碼器與 Azure 媒體服務](https://msdn.microsoft.com/library/azure/dn783464.aspx)。

### 管理通道、程式、資產
如需詳細的概觀，請參閱[管理通道和程式概觀](media-services-manage-channels-overview.md)。

依序選擇 [入口網站]、[.NET]、[REST API]，即可查看範例。

[AZURE.INCLUDE [media-services-selector-manage-channels](../includes/media-services-selector-manage-channels.md)]

### 設定資產傳遞原則

使用 **.NET** 或 **REST API** 設定資產傳遞原則。

[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../includes/media-services-selector-asset-delivery-policy.md)]

### 建立內容金鑰

建立當您利用 **.NET** 或 **REST API** 加密資產時會用到的內容金鑰。

[AZURE.INCLUDE [media-services-selector-create-contentkey](../includes/media-services-selector-create-contentkey.md)]

### 設定內容金鑰授權原則 

使用 **.NET** 或 **REST API** 設定內容保護和金鑰授權原則。

[AZURE.INCLUDE [media-services-selector-content-key-auth-policy](../includes/media-services-selector-content-key-auth-policy.md)]


### 發行資產

使用 **Azure 管理入口網站**或 **.NET** (藉由建立定位器) 發行資產。

[AZURE.INCLUDE [media-services-selector-publish](../includes/media-services-selector-publish.md)]


### 啟用 Azure CDN

媒體服務支援與 CDN 的整合。如需瞭解如何啟用 Azure CDN，請參閱[如何管理媒體服務帳戶中的串流端點](media-services-manage-origins.md#enable_cdn)。

### 調整媒體服務帳戶

您可以藉由指定您要佈建給帳戶的**串流保留單元**數目來調整 **媒體服務**。 

如需調整串流單元的相關資訊，請參閱：[如何調整串流單位](media-services-manage-origins.md#scale_streaming_endpoints.md).


[live-overview]: ./media/media-services-overview/media-services-live-streaming-current.png


<!--HONumber=52-->