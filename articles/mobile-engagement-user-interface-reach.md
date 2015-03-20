<properties 
   pageTitle="Azure Mobile Engagement 使用者介面 - 觸達" 
   description="Azure Mobile Engagement [觸達] 區段的使用者介面概觀" 
   services="mobile-engagement" 
   documentationCenter="mobile" 
   authors="v-micada" 
   manager="mattgre" 
   editor=""/>

<tags
   ms.service="mobile-engagement"
   ms.devlang="Java"
   ms.topic="article"
   ms.tgt_pltfrm="mobile"
   ms.workload="required" 
   ms.date="02/17/2015"
   ms.author="v-micada"/>

# Azure Mobile Engagement - 使用者介面

<div class="dev-center-tutorial-selector sublanding">
<a href="../mobile-engagement-user-interface" title="Introduction">簡介</a>
<a href="../mobile-engagement-user-interface-navigation" title="Navigation">瀏覽</a>
<a href="../mobile-engagement-user-interface-home/" title="Home">首頁</a>
<a href="../mobile-engagement-user-interface-my-account" title="My Account">我的帳戶</a>
<a href="../mobile-engagement-user-interface-analytics" title="Analytics">分析</a>
<a href="../mobile-engagement-user-interface-monitor" title="Monitor">監視</a>
<a href="../mobile-engagement-user-interface-reach" title="Reach">觸達</a>
<a href="../mobile-engagement-user-interface-segments" title="Segments">使用者分佈</a>
<a href="../mobile-engagement-user-interface-dashboard" title="Dashboard">儀表板</a>
<a href="../mobile-engagement-user-interface-settings" title="Settings">設定</a>
</div>

# <a name="Reach">觸達</a>
 
UI 的 [觸達] 區段是推送活動管理工具，您可以在此建立/編輯/啟動/完成/監視推送通知活動，並取得推送通知活動的統計資料，您也可以透過「觸達 API」(和低階推送 API 的部份元素) 存取這些功能。請記住，無論您使用 API 或 UI，您都必須先使用 SDK 針對每個平台將 Azure Mobile Engagement 和觸達整合到您的應用程式，才能使用觸達活動。

**另請參閱：** 

-  [API 文件 - 觸達 API][Link 4][API 文件 - 推送 API][Link 4]、[疑難排解指南 - 推送/觸達][Link 2]
-  <a href="#ReachCampaign" title="Reach Campaign">觸達活動</a>、<a href="#ReachCriterion" title="Reach Criterion">觸達準則</a>、<a href="#ReachContent" title="Reach Content">觸達內容</a>
 
## 推送通知的四種類型：
1.    通知 - 可讓您傳送廣告訊息給使用者，將他們重新導向至您應用程式內的另一個位置，或者將他們傳送至您應用程式外的網頁或市集。 
2.    投票 - 可讓您透過詢問使用者問題，從使用者收集資訊。
3.    資料推送 - 可讓您傳送二進位或 base64 資料檔案。資料推送中所包含的資訊會傳送至您的應用程式，以便調整您的使用者在應用程式中目前的體驗。您的應用程式必須能夠處理資料推送中的資料。
4.    磚 (僅限 Windows Phone) - 可讓您使用 Microsoft Push Notification Service (MPNS) 傳送包含 XML 資料的原生 Windows Push。(從 SDK 版本 0.9.0 開始支援。磚的最後一個裝載不能超過 32 KB)。

**另請參閱：** 

-  [概念 - 詞彙][Link 6]

## 每個活動會顯示三種即時統計資料： 
1.    已推送 - 根據活動中指定的準則傳送的推送數目。 
2.    已回覆 - 從應用程式外部開啟通知或在應用程式中關閉通知來回應通知的使用者數目。 
3.    已採取動作 - 在通知中按下連結而被重新導向至應用程式新位置、市集或網頁瀏覽器的使用者數目。 

> 注意：您可以透過「觸達 API Stats」取得更詳細的活動統計資料。

**另請參閱：** 

-  [概念 - 詞彙][Link 6]、[API 文件 - 觸達 API - Stats][Link 4]


## 活動詳細資料：
您可以停留在活動名稱上或按一下以開啟活動，來編輯、複製、刪除活動或啟動尚未啟動的活動。您可以停留在活動名稱上或按一下以開啟活動，來複製已經啟動的活動。不過，活動一旦啟動，您就無法變更。
 
![Reach1][18]

## 觸達意見反應：
您可以從詳細資料切換至已啟動之開啟活動的統計檢視，並且將統計資料從簡單檢視切換到進階檢視，以檢視更詳細的資訊 (視您的權限而定)。您也可以使用來自先前活動的觸達意見反應資訊，做為新活動的目標設定準則。「觸達意見反應」統計資料也可以透過「觸達 API」的 "Stats" 收集。您也可以根據先前的活動，自訂推送活動的對象。


**另請參閱：** 

-  <a href="#ReachCampaign">UI 文件 - 觸達 - 新增推送活動</a>、[API 文件 - 觸達 API - 統計資料][Link 4]
![Reach2][19]

## <a name="ReachCampaign">觸達 - 新增推送活動：</a>
 
您可以使用 UI 的 [觸達] 區段，提供傳送推送通知所需的所有資訊，建立具有複雜公式的新推送活動。推送活動的選項根據下列四種活動類型會稍微有所不同：通知、投票、資料推送以及磚 (僅限 Windows Phone)。

**選項適用範圍：**

- 語言：    全部 (通知、投票、資料推送、磚)
- 活動：    全部 (通知、投票、資料推送、磚)
- 通知：     通知、投票
- 內容：    每種活動類型都不同
- 對象：     全部 (通知、投票、資料推送、磚)
- 時間範圍：     通知、投票、磚
- 測試：    全部 (通知、投票、資料推送、磚)
 
![Reach-Campaign1][20]

## 語言：
您可以使用 [語言] 下拉式功能表，將不同版本的推送傳送至設定使用不同語言的裝置。無論裝置設定使用的語言為何，所有裝置預設都會收到相同的推送。將裝置設定使用不同語言的使用者會收到預設語言版本的推送。許多推送活動選項都可讓您針對每個選取的其他語言，指定不同的內容。 
 
![Reach-Campaign2][21]

**語言差異適用範圍：**

- 語言：    除了預設語言之外，還可以選取唯一的語言
- 活動：    所有語言都相同
- 通知：    不只預設語言，每個語言都可以不同
- 內容：    不只預設語言，每個語言都可以不同
- 對象：     可依不同的語言準則篩選
- 時間範圍：     所有語言都相同
- 測試：    可以一次傳送給一種語言
 
**支援的語言：**

- 阿拉伯文 (ar) 
- 保加利亞文 (bg) 
- 卡達隆尼亞文 (ca) 
- 中文 (zh) 
- 克羅埃西亞文 (hr) 
- 捷克文 (cs) 
- 丹麥文 (da) 
- 荷蘭文 (nl) 
- 英文 (en) 
- 芬蘭文 (fi) 
- 法文 (fr) 
- 德文 (de) 
- 希臘文 (el) 
- 希伯來文 (he) 
- 印度文 (hi) 
- 匈牙利文 (hu) 
- 印尼文 (id) 
- 義大利文 (it) 
- 日文 (ja) 
- 韓文 (ko) 
- 拉脫維亞文 (lv) 
- 立陶宛文 (lt) 
- 馬來文 (macrolanguage) (ms) 
- 挪威文 (巴克摩) (nb) 
- 波蘭文 (pl) 
- 葡萄牙文 (pt) 
- 羅馬尼亞文 (ro) 
- 俄文 (ru) 
- 塞爾維亞文 (sr) 
- 斯洛伐克文 (sk) 
- 斯洛維尼亞文 (sl) 
- 西班牙文 (es) 
- 瑞典文 (sv) 
- 塔加拉族文 (tl) 
- 泰文 (th) 
- 土耳其文 (tr) 
- 烏克蘭文 (uk) 
- 越南文 (vi) 
 
## 活動：
您可以使用 [活動] 區段設定活動的名稱和類別，如果您計劃忽略推送活動的對象區段，並改為透過「觸達 API」(以及使用低階推送 API 的部份元素) 傳送此活動，也可以這麼做。類別可以搭配自訂的通知範本一起使用，以便根據預先定義的設定控制應用程式內通知。您可以透過「觸達 API」取得一份您現有的「類別」清單。

> 警告：如果您在觸達活動的 [活動] 區段中使用 [略過對象，推送將透過 API 傳送給使用者] 選項，活動將不會自動傳送，您必須以手動方式透過「觸達 API」傳送活動。
 
![Reach-Campaign3][22]
 
**選項適用範圍：**

- 名稱：    全部
- Category:    通知、投票
- 略過對象，推送將透過 API 傳送給使用者：    全部
 
## 通知：
您可以使用 [通知] 區段設定推送的基本設定，包括：推送的標題、訊息、應用程式內影像，或者它是否可關閉。許多通知設定都和您使用的裝置平台有關。您可以選擇要在「應用程式內」或「應用程式外」或兩者傳送您的推送。(請記住，使用者可以在裝置的作業系統層級「選擇加入」或「選擇退出」「應用程式外」的推送，Azure Mobile Engagement 無法覆寫這項設定。也請記住，「觸達 API」會處理「應用程式內」與「應用程式外」的推送。「推送 API」也可用來處理「應用程式外」的推送)。推送可使用圖片或 HTML 內容加以自訂，包括自訂連結到您的應用程式之外或連結到您的應用程式內其他位置的深層連結 (需要 Android SDK 2.1.0 或更新版本的類別)。您可以變更圖示或 iOS 徽章，並傳送文字或 Web 內容 (有 html 內容和 URL 連結的快顯，可連結到應用程式內部或外部的其他位置)。也可以讓 Android 裝置在收到推送時響鈴或震動。(請記住，您在 Android 資訊清單檔案中需要正確的 SDK 權限，才能讓裝置響鈴或震動)。Android 的「大型圖片」尺寸目前沒有任何業界標準，因為每個裝置使用不同的螢幕尺寸，不過 400x100 的圖片幾乎可在所有的螢幕尺寸上運作。

## 傳遞類型：
-    僅限應用程式外：當使用者不使用應用程式時，就會傳遞通知。
- 僅限應用程式外的通知需要來自 Apple 或 Google 的憑證 (APN 或 GCM 憑證)。
- 僅限應用程式內：通知只會在應用程式執行時顯示。
- 通知使用 Capptain 傳遞系統觸達使用者。您可以完全自訂推送的版面配置/顯示方式。
- 任何時間：無論應用程式是否執行，此選項可確保您傳送通知。

 
![Reach-Campaign4][23]

**選項適用範圍：**

- 通知：     通知、投票
 
## 內容：
您可以使用 [內容] 區段修改 [通知]、[投票]、[資料推送] 和 [磚] (僅限 Windows Phone) 的內容。推送活動的 [內容] 設定與活動類型有關。 

**另請參閱：**

- <a href="#ReachContent">UI 文件 - 觸達 - 推送內容</a>
 
![Reach-Campaign5][24]

## 對象：
您可以使用 [對象] 區段定義標準的項目清單，或者根據自訂的準則，對活動設定限制。限制對象的標準選項設定，可讓您只發送給新的或舊的使用者，或者只給原生的推送使用者。您也可以設定配額，限制接收推送的使用者數目。您可以手動編輯運算式，如何篩選您的活動以包含一或多個選取使用者的準則。您可以手動輸入對象運算式。這類運算式必須明確定義準則之間的關係。描述準則的識別項必須以大寫字母開頭，而且不能包含空格。準則之間的關係可以使用  'and'、 'or'、 'not' 等運算子，以及 '('、')' 描述。範例："Criterion1 或 (Criterion1 and not Criterion2)"。

> 注意：如果活動的對象很多，尤其是當您嘗試同時啟動多個活動時，伺服器端的目標掃描可能會變慢。

- 可能的話，一次只啟動一個活動。
- 一次最多只啟動四個活動。
- 只推送給作用中使用者 (核取 [只吸引可使用原生推送觸達的使用者] 和 [只吸引作用中使用者] 方塊)，就只會掃描仍然安裝應用程式且使用它的使用者。
一旦定義您的對象之後，就可以使用模擬按鈕找出會收到此推送的使用者數目。這會計算此對象可能選出的已知使用者數目 (根據使用者的隨機取樣估計)。請注意，已解除安裝應用程式的使用者也屬於這類對象，但無法觸達。

**另請參閱：**

- <a href="#ReachCriterion">UI 文件 - 觸達 - 新增推送準則</a>

![Reach-Campaign6][25]

## 編輯運算式
 
![Reach-Campaign7][26]
 
**限制對象選項適用範圍：**

- 只吸引部份使用者：    全部 (通知、投票、資料推送、磚)
- 只吸引舊使用者：    全部 (通知、投票、資料推送、磚)
- 只吸引新使用者：    全部 (通知、投票、資料推送、磚)
- 只吸引閒置使用者：    通知、投票、磚
- 只吸引作用中使用者：    全部 (通知、投票、資料推送、磚)
- 只吸引可使用原生推送觸達的使用者：     通知、投票
 
## 時間範圍：
您可以使用 [時間範圍] 區段設定推送傳送的時間，或者可以將時間範圍留空以便立即啟動活動。請記住，使用使用者的時區來啟動活動，對於亞洲的使用者可能會比您預期的時間還早一天，因此請在世界上所有的時區都符合活動的時間範圍時，才一次傳送小型批次的推送。使用使用者的時區也可能會造成活動延遲，因為必須先從使用者的電話取得時間才能啟動推送。

> 注意：沒有結束日期的活動可能會在本機快取推送，甚至在您手動完成活動之後，仍然繼續顯示推送。若要避免發生這個狀況，請為活動設定確切的結束時間。

**另請參閱：**

- [作法 - 排程][Link 3] 
 
![Reach-Campaign8][27]

**設定適用範圍：**

- 時間範圍：     通知、投票、磚
 
## 測試：
儲存活動之前，您可以使用 [測試] 區段將此推送傳送到您自己的測試裝置。如果您為此活動設定任何自訂語言，可以使用每個語言測試推送。您可以從 [我的帳戶] 設定測試裝置。
> 注意：當您使用按鈕來「測試」推送時，不會記錄任何伺服器端的資料，只會記錄實際推送活動的資料。

**另請參閱：**

- [UI 文件 - 我的帳戶][Link 14]
 
![Reach-Campaign9][28]

## <a name="ReachCriterion">觸達 - 新增推送準則 (用來選取目標對象)</a>

在 Azure Mobile Engagement 中使用 [新增準則] 按鈕透過特定準則來找出目標對象，是一個非常有力的概念，這樣可協助您傳送相關的推送通知給會回應的客戶，而不是濫發垃圾訊息給每個人。您可以根據標準的準則限制對象，並且模擬推送來判斷有多少人會收到通知。

**另請參閱：**

- <a href="#ReachCampaign">UI 文件 - 觸達 - 新推送活動</a>

## 對象準則可以包括：

- **技術：**您可以根據 [分析] 和 [監視] 區段中也可看見的相同技術資訊來選取目標。**另請參閱：**[UI 文件 - 分析][Link 15]、[UI 文件 - 監視][Link 16]
- **位置：**使用 "Real time location reporting" 的應用程式搭配「地理圍欄」(Geo-Fening) 可以使用地理位置做為條件，從 GPS 位置找出目標對象。"Lazy Area Location Reporting" 呼叫也可用來從手機位置找出目標對象 (必須從 SDK 啟用 "Real time location reporting" 和 "Lazy Area Location Reporting")。**另請參閱：**[SDK 文件 - iOS - 整合][Link 5]、[SDK 文件 - Android - 整合][Link 5]
- **觸達意見反應：**您可以透過 [通知]、[投票] 和 [資料推送] 的觸達意見反應，根據目標對象對於先前觸達通知的意見反應找出目標對象。進行兩、三個觸達活動之後，您就能夠比第一次找出更準確的目標對象。您也可以設定一個活動，「不要」傳送至已接收到上一個特定活動的使用者，篩選出已收到類似通知內容的使用者。您甚至可以排除從接收到新推送之後，在特定活動中仍然作用中的使用者。**另請參閱：**<a href="#ReachContent">UI 文件 - 觸達 - 推送內容</a>
- **安裝追蹤：**您可以根據使用者安裝應用程式的位置追蹤資訊。**另請參閱：**[UI 文件 - 設定][Link 20]
- **使用者設定檔：**您可以根據標準的使用者資訊，或是根據您所建立的自訂應用程式資訊找出目標。這包括目前已登入的使用者，以及已經回答您在應用程式中設定的特定問題 (而不是只是回應先前活動) 的使用者。針對應用程式定義的所有應用程式資訊都會顯示在此清單上。
- 使用者分佈：您也可以根據所建立的使用者分佈 (包含多個準則的特定使用者行為) 找出目標。針對應用程式定義的所有使用者分佈都會顯示在此清單上。**另請參閱：**[UI 文件 - 使用者分佈][Link 18]
- **應用程式資訊：**您可以從 [設定] 建立自訂應用程式資訊標記，來追蹤使用者行為。**另請參閱：**[UI 文件 - 設定][Link 20]
## 範例： 
如果您只想要推送通知給已執行應用程式內購買動作的部份使用者使用者，

1. 請移至您的應用程式設定頁面，選取 [應用程式資訊] 功能表，然後選取 [新增應用程式資訊]
2. 註冊一個稱為 "inAppPurchase" 的新布林值應用程式資訊
3. 讓您的應用程式將此應用程式資訊在使用者成功執行應用程式內購買 (使用 sendAppInfo("inAppPurchase", ...) 函式) 時設定為 "true"
4. 如果您不想從您的應用程式執行這項操作，可以從您的後端使用裝置 API 執行
5. 然後，您只需要建立您的通知，以及將您的對象限制為已設定 "inAppPurchase" 為 "true" 的使用者的準則
 
> 注意：如果目標選取是根據準則而不是應用程式資訊標記，Azure Mobile Engagement 必須先從使用者的裝置收集資訊，才能傳送推送，如此一來可能會導致延遲。複雜的推送設定選項 (例如更新徽章) 可能也會延遲推送。在 Azure Mobile Engagement 中，從「推送 API」使用「一次性」的活動，絕對是最快速的推送方法。對於觸達活動只使用應用程式資訊標記做為推送準則 (從「觸達 API」或 UI) 是第二種最快的方法，因為應用程式資訊標記儲存在伺服器端。對於推送活動使用其他目標選取準則，是最具彈性、但也是最慢的推送方法，因為 Azure Mobile Engagement 必須先查詢裝置才能傳送活動。
 
![Reach-Criterion1][29] 

**準則選項適用範圍：**

- **技術**     
- 韌體名稱：    韌體名稱
- 韌體版本：    韌體版本
- 裝置型號：    裝置型號
- 裝置製造商：    裝置製造商
- 應用程式版本：    應用程式版本
- 電信業者名稱：    電信業者名稱、未定義
- 電信業者國家 (地區)：    電信業者國家 (地區)、未定義
- 網路類型：    網路類型
- 地區設定：    地區設定
- 螢幕大小：    螢幕大小
- **位置**      
- 上一個已知區域：    國家/地區、區域、位置
- 即時地理圍欄：    POI 清單 (名稱、動作)、圓形 POI (名稱、緯度、經度、以公尺為單位的半徑)
- **觸達意見反應**     
- 通知意見反應：    通知、意見反應
- 投票意見反應：    投票、意見反應
- 投票答案意見反應：    投票答案意見反應、問題、選項
- 資料推送意見反應：    資料推送、意見反應
- **安裝追蹤**     
- 市集：    市集、未定義
- 來源:    來源、未定義
- **使用者設定檔**     
- 性別：    男性或女性、未定義
- 生日：    運算子、日期、未定義
- 選擇加入：    true 或 false、未定義
- **應用程式資訊**      
- 字串：    字串、未定義
- 日期：    運算子、日期、未定義
- 整數：    運算子、數字、未定義
- 布林值：    true 或 false、未定義
- **使用者分佈**
- 使用者分佈的名稱 (從下拉式清單)、排除 (不屬於此使用者分佈的目標使用者)。

## <a name="ReachContent">觸達 - 推送內容 (針對每種活動類型)</a>
 
您可以使用新觸達活動的 [內容] 區段修改 [通知]、[投票]、[資料推送] 和 [磚] (僅限 Windows Phone) 的內容。推送活動的內容設定與活動類型有關。 
 
## 內容類型
- 通知
- 投票
- 資料推送
- 磚 (僅限 Windows Phone)
 
## 通知的內容
 
 ![Reach-Content1][30] 

## 選擇您的通知類型：
-    只有通知：這是簡單的標準通知。表示如果使用者按一下通知，不會出現任何其他檢視，但是會發生與它關聯的動作。
-    文字通知：這是一種吸引使用者查看文字檢視的通知。
-    Web 通知：這是一種吸引使用者查看 Web 檢視的通知。

**另請參閱：**

- [作法 - 通知][Link 3] 

**關於 Web 檢視通知：**

您在此處提供的每個 "{deviceid}" 字樣 (在 HTML 程式碼或 JavaScript 程式碼中) 將會自動取代為顯示通知的裝置識別碼。這個簡單的方法可以擷取後端辦公室裝載的外部 Web 服務中的 Azure Mobile Engagement 裝置識別碼。
如果您想要建立全螢幕的 Web 檢視 (不含我們提供的預設 [動作] 和 [離開] 按鈕)，您可以從 Web 檢視通知的 JavaScript 程式碼中使用下列函式： 

-    執行通知動作：ReachContent.actionContent()
-    從通知離開：ReachContent.exitContent()
 
## 選擇您的動作：

**關於動作 URL：**

可由目標裝置的作業系統解譯的任何 URL 都可以用來做為動作 URL。
應用程式可能支援的任何專用 URL (例如讓使用者跳至特定畫面) 也可用來做為動作 URL。
每個 {deviceid} 字樣的項目都會自動取代為執行動作的裝置識別碼。透過後端辦公室裝載的外部 Web 服務，這可用來輕鬆擷取 Azure Mobile Engagement 裝置識別碼。

- **Android + iOS 動作**
    - 開啟網頁
    - http://[web-site-domain] 
    - 範例:http://www.azure.com
    - 傳送電子郵件
    - mailto:[e-mail-recipient]?subject=[subject]&body=[message] 
    - 範例：mailto:foo@example.com?subject=Greetings%20from%20Azure%20Mobile%20Engagement!&body=Good%20stuff!
    - 傳送簡訊
    - sms:[phone-number] 
    - 範例：sms:2125551212
    - 撥電話號碼
    - tel:[phone-number] 
    - 範例：tel:2125551212
- **僅限 Android 的動作**
    - 下載 Play 商店上的應用程式
    - market://details?id=[app package] 
    - 範例：market://details?id=com.microsoft.office.word
    - 開始當地化的搜尋
    - geo:0,0?q=[search query] 
    - 範例：geo:0,0?q=starbucks,paris
- **僅限 iOS 的動作**
    - 下載 App Store 上的應用程式
    - http://itunes.apple.com/[country]/app/[app name]/id[app id]?mt=8 
    - 範例:http://itunes.apple.com/fr/app/briquet-virtuel/id430154748?mt=8
    - Windows 動作
    - 開啟網頁
    - http://[web-site-domain] 
    - 範例:http://www.azure.com
    - 傳送電子郵件
    - mailto:[e-mail-recipient]?subject=[subject]&body=[message] 
    - 範例：mailto:foo@example.com?subject=Greetings%20from%20Azure%20Mobile%20Engagement!&body=Good%20stuff!
    - 傳送簡訊 (需要 Skype 市集應用程式)
    - sms:[phone-number] 
    - 範例：sms:2125551212
    - 撥電話號碼 (需要 Skype 市集應用程式)
    - tel:[phone-number] 
    - 範例：tel:2125551212
    - 下載 Play 商店上的應用程式
    - ms-windows-store:PDP?PFN=[app package ID] 
    - 範例：ms-windows-store:PDP?PFN=4d91298a-07cb-40fb-aecc-4cb5615d53c1
    - 開始 bingmaps 搜尋
    - bingmaps:?q=[search query] 
    - 範例：bingmaps:?q=starbucks,paris
    - 使用自訂配置
    - [custom scheme]://[custom scheme params] 
    - 範例：myCustomProtocol://myCustomParams
    - 使用封裝資料 (需要延伸讀取的市集應用程式)
    - [folder][data].[extension] 
    - 範例：myfolderdata.txt
 
## 建置追蹤 URL：
-    請參閱 UI 文件的＜設定＞一節，了解建置可讓使用者下載其他應用程式之追蹤 URL 的相關指示。
 
## 定義您的通知文字：
填寫通知的標題、內容和按鈕文字。 
您可以根據使用者如何回應此活動的觸達意見反應，找出未來的活動對象。選取目標對象可以根據此活動是否已推送、回覆、採取動作或離開的意見反應。

**另請參閱：**
- <a href="#ReachCriterion">UI 文件 - 觸達 - 新增推送準則</a>

## 投票的內容
 
![Reach-Content2][31] 
填寫通知的標題、描述和按鈕文字。 
然後，新增問題和問題的答案選項。
您可以根據使用者如何回應此活動的觸達意見反應，找出未來的活動對象。選取目標對象時可以根據此活動是否已推送、回覆、採取動作或離開。選取目標對象時也可以根據投票答案的意見反應，將其中的問題和答案選項做為準則。

**另請參閱：**

- <a href="#ReachCriterion">UI 文件 - 觸達 - 新增推送準則</a>
 
## 資料推送的內容
 
![Reach-Content3][32] 

## 選擇您的資料類型
- 文字
- 二進位資料
- Base64 資料

## 定義資料的內容
- 如果您選取推送文字資料，請複製文字並貼到 [內容] 方塊。
- 如果您選取推送二進位或 base64 資料，請使用 [上傳您的檔案] 按鈕上傳您的檔案。
- 您可以根據使用者如何回應此活動的觸達意見反應，找出未來的活動對象。選取目標對象時可以根據此活動是否已推送、回覆、採取動作或離開。

**另請參閱：**

- <a href="#ReachCriterion">UI 文件 - 觸達 - 新增推送準則</a>

## 磚的內容 (僅限 Windows Phone)

![Reach-Content4][33]

## 定義磚的內容
磚裝載是在 Windows Phone 裝置上應用程式磚中顯示的文字。
磚推送是適用於 Windows Phone 之原生推送的 Microsoft Push Notification Service (MPNS) 版本。磚推送類型是唯一沒有回應的推送類型，因此無法依據磚推送活動的結果建置未來活動的對象。 

**另請參閱：**

- [API 文件 - 觸達率 API - 原生推送][Link 4]

<!--Image references-->
[1]: ./media/mobile-engagement-user-interface-navigation/navigation1.png
[2]: ./media/mobile-engagement-user-interface-home/home1.png
[3]: ./media/mobile-engagement-user-interface-home/home2.png
[4]: ./media/mobile-engagement-user-interface-home/home3.png
[5]: ./media/mobile-engagement-user-interface-home/home4.png
[6]: ./media/mobile-engagement-user-interface-home/home5.png
[7]: ./media/mobile-engagement-user-interface-my-account/myaccount1.png
[8]: ./media/mobile-engagement-user-interface-my-account/myaccount2.png
[9]: ./media/mobile-engagement-user-interface-my-account/myaccount3.png
[10]: ./media/mobile-engagement-user-interface-analytics/analytics1.png
[11]: ./media/mobile-engagement-user-interface-analytics/analytics2.png
[12]: ./media/mobile-engagement-user-interface-analytics/analytics3.png
[13]: ./media/mobile-engagement-user-interface-analytics/analytics4.png
[14]: ./media/mobile-engagement-user-interface-monitor/monitor1.png
[15]: ./media/mobile-engagement-user-interface-monitor/monitor2.png
[16]: ./media/mobile-engagement-user-interface-monitor/monitor3.png
[17]: ./media/mobile-engagement-user-interface-monitor/monitor4.png
[18]: ./media/mobile-engagement-user-interface-reach/reach1.png
[19]: ./media/mobile-engagement-user-interface-reach/reach2.png
[20]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign1.png
[21]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign2.png
[22]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign3.png
[23]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign4.png
[24]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign5.png
[25]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign6.png
[26]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign7.png
[27]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign8.png
[28]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign9.png
[29]: ./media/mobile-engagement-user-interface-reach/Reach-Criterion1.png
[30]: ./media/mobile-engagement-user-interface-reach/Reach-Content1.png
[31]: ./media/mobile-engagement-user-interface-reach/Reach-Content2.png
[32]: ./media/mobile-engagement-user-interface-reach/Reach-Content3.png
[33]: ./media/mobile-engagement-user-interface-reach/Reach-Content4.png
[34]: ./media/mobile-engagement-user-interface-dashboard/dashboard1.png
[35]: ./media/mobile-engagement-user-interface-segments/segments1.png
[36]: ./media/mobile-engagement-user-interface-segments/segments2.png
[37]: ./media/mobile-engagement-user-interface-segments/segments3.png
[38]: ./media/mobile-engagement-user-interface-segments/segments4.png
[39]: ./media/mobile-engagement-user-interface-segments/segments5.png
[40]: ./media/mobile-engagement-user-interface-segments/segments6.png
[41]: ./media/mobile-engagement-user-interface-segments/segments7.png
[42]: ./media/mobile-engagement-user-interface-segments/segments8.png
[43]: ./media/mobile-engagement-user-interface-segments/segments9.png
[44]: ./media/mobile-engagement-user-interface-segments/segments10.png
[45]: ./media/mobile-engagement-user-interface-segments/segments11.png
[46]: ./media/mobile-engagement-user-interface-settings/settings1.png
[47]: ./media/mobile-engagement-user-interface-settings/settings2.png
[48]: ./media/mobile-engagement-user-interface-settings/settings3.png
[49]: ./media/mobile-engagement-user-interface-settings/settings4.png
[50]: ./media/mobile-engagement-user-interface-settings/settings5.png
[51]: ./media/mobile-engagement-user-interface-settings/settings6.png
[52]: ./media/mobile-engagement-user-interface-settings/settings7.png
[53]: ./media/mobile-engagement-user-interface-settings/settings8.png
[54]: ./media/mobile-engagement-user-interface-settings/settings9.png
[55]: ./media/mobile-engagement-user-interface-settings/settings10.png
[56]: ./media/mobile-engagement-user-interface-settings/settings11.png
[57]: ./media/mobile-engagement-user-interface-settings/settings12.png
[58]: ./media/mobile-engagement-user-interface-settings/settings13.png

<!--Link references-->
[Link 1]: ../mobile-engagement-user-interface/
[Link 2]: ../mobile-engagement-troubleshooting-guide/
[Link 3]: ../mobile-engagement-how-tos/
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/home?forum=azuremobileengagement
[Link 9]: http://azure.microsoft.com/services/mobile-engagement/
[Link 10]: http://azure.microsoft.com/documentation/services/mobile-engagement/
[Link 11]: http://azure.microsoft.com/pricing/details/mobile-engagement/
[Link 12]: ../mobile-engagement-user-interface-navigation/
[Link 13]: ../mobile-engagement-user-interface-home/
[Link 14]: ../mobile-engagement-user-interface-my-account/
[Link 15]: ../mobile-engagement-user-interface-analytics/
[Link 16]: ../mobile-engagement-user-interface-monitor/
[Link 17]: ../mobile-engagement-user-interface-reach/
[Link 18]: ../mobile-engagement-user-interface-segments/
[Link 19]: ../mobile-engagement-user-interface-dashboard/
[Link 20]: ../mobile-engagement-user-interface-settings/
[Link 21]: ../mobile-engagement-troubleshooting-guide-analytics/
[Link 22]: ../mobile-engagement-troubleshooting-guide-apis/
[Link 23]: ../mobile-engagement-troubleshooting-guide-push-reach/
[Link 24]: ../mobile-engagement-troubleshooting-guide-service/
[Link 25]: ../mobile-engagement-troubleshooting-guide-sdk/
[Link 26]: ../mobile-engagement-troubleshooting-guide-sr-info/
[Link 27]: ../mobile-engagement-how-tos-first-push/
[Link 28]: ../mobile-engagement-how-tos-test-campaign/
[Link 29]: ../mobile-engagement-how-tos-personalize-push/
[Link 30]: ../mobile-engagement-how-tos-differentiate-push/
[Link 31]: ../mobile-engagement-how-tos-schedule-campaign/
[Link 32]: ../mobile-engagement-how-tos-text-view/
[Link 33]: ../mobile-engagement-how-tos-web-view/

<!--HONumber=47-->
