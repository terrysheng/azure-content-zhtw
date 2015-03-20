<properties 
   pageTitle="Azure Mobile Engagement 疑難排解指南" 
   description="Azure Mobile Engagement 的疑難排解指南" 
   services="mobile-engagement" 
   documentationCenter="mobile" 
   authors="v-micada" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="mobile-engagement"
   ms.devlang="Java"
   ms.topic="article"
   ms.tgt_pltfrm="mobile"
   ms.workload="mobile" 
   ms.date="02/17/2015"
   ms.author="v-micada"/>

# Azure Mobile Engagement - 疑難排解指南

## 簡介

下列疑難排解指南涵蓋 Azure Mobile Engagement 最常見的問題，但不包含[帳單][Link 11] 問題和協助註冊[預覽][Link 7]。如果這些疑難排解指南無法解決您的問題，您可以開啟 Azure 服務要求 (若您具有 [Azure 支援合約](http://azure.microsoft.com/support/options/)) 或是於 [MSDN 論壇]要求協助[Link 8]。當您開啟服務要求時，請務必包含 <a href="#SR" title="SR">SR 資訊</a>中所有的疑難排解資訊，以協助技術支援人員調查問題。

## <a name="#ANALYTICS">分析、監視、分割，以及儀表板</a>

Azure Mobile Engagement 如何收集您的應用程式、裝置和使用者相關資訊的問題。

**徵兆清單：**

1. <a href="#ANALYTICS1">遺漏/延遲資訊</a> 
2. <a href="#ANALYTICS2">在 UI 中找不到項目</a>
3. <a href="#ANALYTICS3">當機疑難排解</a>
 
<a name="#ANALYTICS1">**徵兆：**
1.    遺漏/延遲資訊：</a>

- 資訊在分析、分割或儀表板中會延遲出現。
- 監視中遺漏資訊。
- 分析、分割或儀表板中遺漏資訊。
- 達到分割限制。

**原因：**

- 您可以使用分析 API、監視 API 與區段 API，查看是否能透過 API 看到 UI 中遺漏的任何資料。
- 如果 Azure Mobile Engagement SDK 未正確地整合到您的應用程式中，您就無法查看分析、分割、監視或儀表板中的資訊。
- 區段建立之後就無法變更，只能「再製」(複製) 或「終結」(刪除) 區段。
區段只能包含 10 個準則。
- 測試監視中遺漏資訊的最佳方式 (安裝測試裝置、解除安裝及/或重新安裝測試裝置上的應用程式)。
- 每隔 24 小時會重新整理分析、分割或儀表板的資訊。
- 在新區段建立 24 小時之後，新區段中的資訊才會顯示 (即使該區段是以先前的資訊為根據)。
- 篩選 UI 中的分析資料會顯示此類型的所有範例，無論應用程式版本為何 (例如，依據名稱篩選「當機」會顯示版本 1 和版本 2 的應用程式)。
- 分析的時間週期是以使用者裝置設定的日期為依據，因此使用者的手機日期設定如果不正確，可能會顯示錯誤的時間週期。
- 當您使用按鈕來「測試」推送時，不會記錄任何伺服器端的資料，只會記錄實際推送活動的資料。

**另請參閱：** 

- [疑難排解指南 - SDK][Link 2]、[API 文件][Link 4]、[UI 文件 - 區段][Link 1]

<a name="#ANALYTICS2">**徵兆：**
2. 在 UI 中找不到項目：</a>

- 無法建立以特定內建或自訂應用程式資訊標記準則為依據的區段。
- 分析、監視或儀表板中找不到特定內建或自訂應用程式資訊標記準則。
- 無法解譯分析、監視、分割或儀表板中的資料。

**原因：**

- 部分內建項目與應用程式資訊標記只能做為推送準則使用，無法新增至區段，或是顯示於分析、監視或儀表板中。 
- 對於無法新增至區段的內建項目與應用程式資訊標記，您必須在每個活動中設定目標準則的清單，來執行以區段為依據之目標的相同函式。
- 請參閱 Azure Mobile Engagement UI 的分析、監視、分割及儀表板區段中的操作功能表，以取得更多協助與使用方式資訊。

**另請參閱：**

- [UI 文件 - Reach 的新推送準則 (適用於目標對象)][Link 1]
 
<a name="#ANALYTICS3">**徵兆：**
3. 當機疑難排解：</a>

- 分析、監視或儀表板中發生應用程式當機。

**原因：**

- 如果要疑難排解分析、監視或儀表板中發生的應用程式當機，請務必查看版本資訊，了解舊版 SDK 的已知問題。
- 若要進一步疑難排解應用程式當機，請在已安裝您應用程式的測試裝置上執行事件，並在 Azure Mobile Engagement UI 的 [監視 - 事件] 區段中查詢您的裝置識別碼。然後執行造成應用程式當機的事件，並查詢 Azure Mobile Engagement UI 的 [監視 - 當機] 區段中的其他資訊。 

**另請參閱：**

- [概念 - 常見問題集][Link 6]、[概念 - 詞彙][Link 6]、[UI 文件][Link 1]、[SDK 文件 - 版本資訊][Link 5]、[SDK 文件 - 升級指南][Link 5]、[使用方式 - 裝置識別碼註冊][Link 3]

## <a name="#APIS">API</a>

系統管理員如何透過 API 與 Azure Mobile Engagement 互動的問題。

**徵兆清單：**

1. <a href="#APIS1">語法問題</a>
2. <a href="#APIS2">無法使用 API 執行 Azure Mobile Engagement UI 中可用的相同動作</a>
3. <a href="#APIS3">錯誤訊息</a>
4. <a href="#APIS4">無訊息失敗</a>
 
<a name="#APIS1">**徵兆：**
1. 語法問題：</a>

- 使用 API 的語法錯誤 (或非預期的行為)。

**原因：**

- 語法問題：
    - 請務必檢查您正在使用之特定 API 的語法，以確認該選項可以使用。
    - API 使用方式的一個常見問題是將觸達 API 與推送 API 混淆 (大部分工作應該是使用觸達 API 來執行，而不是使用推送 API 來執行)。 
    - SDK 整合與 API 使用方式的另一個常見問題是將 SDK 金鑰和 API 金鑰混淆。
    - 連接到 API 的指令碼必須至少每 10 分鐘傳送一次資料，否則連線會逾時 (這在接聽資料的監視 API 指令碼中特別常見)。若要避免逾時，請讓您的指令碼每 10 分鐘傳送一個 XMPP ping，以保持伺服器工作階段運作。

**另請參閱：**
 
- [概念 - 詞彙][Link 6]、[API 文件][Link 4]、[XMPP 通訊協定資訊]( http://xmpp.org/extensions/xep-0199.html)
 
<a name="#APIS2">**徵兆：**
2. 無法使用 API 執行 Azure Mobile Engagement UI 中可用的相同動作：</a>

- 可從 Azure Mobile Engagement UI 運作的動作，無法從相關的 Azure Mobile Engagement API 運作。

**原因：**

- 確認您可以從 Azure Mobile Engagement UI 執行相同的動作之後，即表示您已正確地將 Azure Mobile Engagement 的這項功能與 SDK 整合。

**另請參閱：**
 
- [UI 文件][Link 1]
 
<a name="#APIS3">**徵兆：**
3. 錯誤訊息：</a>

- 使用 API 時在執行階段或記錄檔中顯示的錯誤碼。

**原因：**

- 以下是供參考和初步疑難排解使用之一般 API 狀態碼號碼的複合清單：

        200        Success.
        200        Account updated: device registered, associated, updated, or removed from the current account.
        200        Returns a list of projects as a JSON object or an authentication token generated and returned in the response's body.
        201        Account created.
        400        Invalid parameter or validation exception (check payload for details). The parameters provided to the API or service are invalid. In this case, the HTTP response will embed more details. Make sure to test for the MIME type of the response as the payload can either be plain text or a JSON object.
        401        Authentication error. No user is currently authenticated or connected (check the AppID and SDK key).
        402        Billing lock. The application is either off its quotas or is currently in a bad billing state.
        403        The application is not enabled or the specific API is disabled for this application.
        403        Unauthorized access to the project or application, invalid access key (the key must match the one provided when created).
        403        Campaign specific errors: campaign must be finished (or has already been activated), the suspend action can only be performed on an scheduled campaign, cannot finish a campaign that is not currently "in progress", campaign must be "in progress" and the campaign's property named, manual Push must be set to true.
        403        The email address is already associated to another account (a super user for instance). No authentication token will be generated.
        404        Application, device, campaign, or project identifier not found.
        404        Query parameter is invalid JSON or has a field with an unexpected value.
        404        The email address is not associated with an account. Please create or update the account first.
        405        Invalid HTTP method (GET, POST, etc.) or trying to edit a read only segment (i.e. add or update or delete a criterion). A segment becomes read only after it has been computed for the first time.
        409        Name already associated to a different device ID or campaign.
        413        Too many device identifiers (current limit is 1,000), POST URL encoded entity is over 2MB, or the period is too large to be displayed (the server didn't retrieve the analytics because the user request is for a period that is too large).
        503        Analytics not available yet (the requested information is not computed yet for an application).
        504         The server was not able to handle your request in a reasonable time (if you make multiple calls to an API very quickly, try to make one call at a time and spread the calls out over time).

**另請參閱：**

- [API 文件 - 適用於每個特定 API 的詳細錯誤][Link 4]
 
<a name="#APIS4">**徵兆：**
4. 無訊息失敗：</a>

- API 動作失敗，但執行階段或記錄檔中沒有顯示任何錯誤訊息。

**原因：**

- 如果項目整合不正確，Azure Mobile Engagement UI 中的許多項目將會停用，但會從 API 以無訊息方式發生失敗，因此請記得從 UI 測試相同功能，以查看功能是否正常運作。
- Azure Mobile Engagement 以及許多您嘗試使用的 Azure Mobile Engagement 進階功能，都需要使用 SDK 以獨立步驟方式個別整合到您的應用程式中，您才能使用它們。

**另請參閱：**

- [疑難排解指南 - SDK][Link 2]、[SDK 文件][Link 5]
 
## <a name="#PUSH">推送/觸達</a>

Azure Mobile Engagement 如何將資訊傳送給使用者的問題。
 
**徵兆清單：**

1. <a href="#PUSH1">推送失敗</a>
2. <a href="#PUSH2">推送測試問題</a>
3. <a href="#PUSH3">推送自訂問題</a>
4. <a href="#PUSH4">推送目標問題</a>
5. <a href="#PUSH5">推送排程</a>
 
<a name="#PUSH1">**徵兆：**
1. 推送失敗：</a>

- 推送沒有運作 (在應用程式中、在應用程式外，或兩者)。

**原因：**

- 推送失敗：
    - 許多時候，推送失敗表示 Azure Mobile Engagement、Reach 或 Azure Mobile Engagement 的其他進階功能未正確整合，或是需要升級 SDK 以修正新的作業系統或裝置平台的已知問題。
    - 測試應用程式內推送和應用程式外推送，以判斷這是應用程式內或應用程式外的問題。
    - 同時從 UI 與 API 測試以做為疑難排解步驟，藉以查看這兩者中有哪些其他的錯誤資訊。
    - 除非 SDK 中整合了 Azure Mobile Engagement 與 Reach，否則應用程式外推送不會運作。
    - 如果憑證無效，或正在正確使用 PROD 與 DEV (僅限 iOS)，應用程式外推送不會運作。
    - 應用程式外推送計數在不同的平台上有不同的處理方式 (如果裝置停用原生推送，iOS 顯示的資訊會比 Android 少，API 可以提供比 UI 更多的推送狀態相關資訊)。
    - 應用程式外推送可以由客戶在作業系統層級 (iOS 與 Android) 封鎖。
    - 如果應用程式外推送未正確整合，在 Azure Mobile Engagement UI 中會顯示為停用，但可能會從 API 以無訊息方式發生失敗。
    - 除非 SDK 中同時整合了 Azure Mobile Engagement 與 Reach，否則應用程式內推送不會運作。
    - 除非 SDK 中整合了 Azure Mobile Engagement 與特定伺服器，否則 GCM 與 ADM 推送不會運作 (僅限 Android)。
    - 應用程式內推送和應用程式外推送應個別測試，以判斷是「推送」或「觸達」問題。
    - 應用程式內推送要求應用程式開放被接收功能。
    - 應用程式內推送通常設定為依據加入 (opt-in) 或退出 (opt-out) 應用程式資訊標記來篩選。
    - 如果您在 Reach 中使用自訂類別來顯示應用程式內通知，您必須遵循通知的正確生命週期，否則當使用者關閉通知時可能不會清除通知。
    - 如果您開始了一個沒有結束日期的活動，且裝置接收到應用程式內通知但尚未顯示，那麼即使您手動結束活動，使用者在下一次登入應用程式時仍然會收到通知。
    - 對於推送 API 的問題，請確認您真的希望使用推送 API 而不是觸達 API (因為觸達 API 更常使用)，且您並沒有混淆 "payload" 和 "notifier" 參數。
    - 使用透過 WIFI 與 3G 連線的裝置測試您的推播活動，來消除可能為問題來源的網路連線。

**另請參閱：**

- [疑難排解指南 - SDK][Link 2]、[疑難排解指南 - 推送][Link 2]、[SDK 文件 - iOS - 如何準備您的應用程式以使用 Apple 推送通知][Link 5]
 
<a name="#PUSH2">**徵兆：**
2. 推送測試問題：</a>

- 推送可以依據裝置識別碼傳送給特定的裝置。

**原因：**

- 測試裝置在各平台上有不同的設定，但是在您測試裝置上的應用程式中引發事件，以及在入口網站尋找您的裝置識別碼的功能應該運作，以尋找您的裝置在所有平台上的裝置識別碼。
- 測試裝置與 IDFA 與 IDFV 搭配使用時運作方式不同 (僅限 iOS)。

**另請參閱：**

- [UI 文件 - Reach][Link 1]
 
<a name="#PUSH3">**徵兆：**
3. 推送自訂問題：</a>

- 進階推送內容項目無法運作 (徽章、響鈴、震動、圖片等)。
- 推送中的連結沒有作用 (應用程式外、應用程式內、網站連結、應用程式中的位置連結)。
- 推送統計資料顯示推送並沒有傳送給預期的人數 (太多或不足)。
- 重複推送，並且收到兩次。
- 無法為 Azure Mobile Engagement 推送註冊測試裝置 (使用您自己的 Prod 或 DEV 應用程式)。

**原因：**

- 要連結到應用程式內的特定位置時需要「類別」(僅限 Android)。
- 外部影像伺服器必須能夠使用 HTTP "GET" 與 "HEAD"，大型圖片推送才能運作 (僅限 Android)。
- 在您的程式碼中，您可以在鍵盤開啟時停用 Azure Mobile Engagement 代理程式，並讓程式碼在鍵盤關閉時重新啟用 Azure Mobile Engagement 代理程式，這樣一來鍵盤就不會影響您的通知的外觀 (僅限 iOS)。
- 部分項目在測試模擬中沒有作用，只有在實際活動 (徽章、響鈴、震動、圖片等) 中才會有作用。
- 當您使用按鈕「測試」推送時，不會記錄任何伺服器端的資料。只會記錄實際推送活動的資料。
- 為了協助您找出問題，請以測試、模擬及實際活動來執行疑難排解，因為它們各自的運作方式稍有不同。

**另請參閱：**

- [使用方式 - 推送][Link 3]、[疑難排解指南 - 推送][Link 2]、[HTTP 通訊協定資訊]( http://www.w3.org/Protocols/rfc2616/rfc2616-sec5.html)
 
<a name="#PUSH4">**徵兆：**
4. 推送目標問題：</a>

- 內建目標未如預期般運作。
- 應用程式資訊標記目標未如預期般運作。
- 地理位置目標未如預期般運作。
- 語言選項未如預期般運作。

**原因：**

- 推送目標：
    - 請確定您已經透過 Azure Mobile Engagement UI 或 API 上傳應用程式資訊標記。
    - 於應用程式層級控制推送速度或推送配額，或於活動層級限制對象，可以防止使用者接收特定的推送，即使它們符合您其他的目標準則。 
    - 設定「語言」和依據國家或地區設定目標不同，這也和依據地理位置、手機位置或 GPS 定位位置設定目標不同。
    - 系統會傳送使用「預設語言」的訊息給未將其裝置設定為您所指定之替代語言之一的任何客戶。

**另請參閱：**

- [UI 文件 - Reach][Link 1]、[UI 文件 - 設定][Link 1]、[API 文件 - Reach][Link 4]、[API 文件 - 推送][Link 4]、[API 文件 - 裝置][Link 4]
 
<a name="#PUSH5">**徵兆：**
5. 推送排程：</a>

- 推送排程未如預期般運作 (過早或延遲傳送)。

**原因：**

- 時區可能導致排程問題，特別是在使用使用者的時區時。
- 進階推送功能可能會延遲推送。
- 根據手機設定 (而不是根據應用程式資訊標記) 設定的目標可能會延遲推送，因為 Azure Mobile Engagement 可能必須在傳送推送之前，即時要求手機資料。
- 建立時未設定結束日期的活動會將推送儲存在本機裝置上，並在下一次應用程式開啟時顯示，即使活動已手動結束。
- 同時啟動多個活動可能需要較長時間來掃描您的使用者基礎 (請嘗試一次只啟動一個活動 (上限為四個)，目標也只限定於您在作用中的使用者，如此就不需要掃描舊的使用者)。
- 如果您在觸達活動的 [活動] 區段中使用 [略過對象，推送將透過 API 傳送給使用者] 選項，活動將不會自動傳送，您必須以手動方式透過「觸達 API」傳送活動。
- 如果您在 Reach 中使用自訂類別來顯示應用程式內通知，您必須遵循通知的正確生命週期，否則當使用者關閉通知時可能不會清除通知。

**另請參閱：**

- [使用方式 - 排程][Link 3]、[UI 文件 - Reach 的新推送活動][Link 1]
 
## <a name="#SERVICE">服務</a>

Azure Mobile Engagement 執行方式的問題。

**徵兆清單：**

1. <a href="#SERVICE1">服務中斷</a>
2. <a href="#SERVICE2">連線和資訊不正確的問題</a>
3. <a href="#SERVICE3">功能要求</a>
 
<a name="#SERVICE1">**徵兆：**
1. 服務中斷：</a>

**原因：**

- 似乎是因為 Azure Mobile Engagement 服務中斷所造成的問題，可能由數種不同原因所造成：
    - 原本顯示為 Azure Mobile Engagement 系統問題的隔離問題
    - 伺服器關閉所造成的已知問題 (不一定會顯示在伺服器狀態中)：
排程延遲、目標錯誤、徽章更新問題、統計資料停止收集、推播停止運作、API 停止運作、無法建立新的應用程式或使用者、DNS 錯誤，以及 UI、API 或裝置上應用程式中的逾時錯誤。
1.    雲端相依性中斷
<Azure 服務狀態><Amazon Web Services (AWS) 狀態>
2.    推送通知服務 (PNS) 相依性中斷
<Google - 服務><Apple - 服務><Android - Google GCM><Android - Amazon ADM><Apple - APNS><Windows Phone - WNS><Windows Phone - MPNS><Windows - WNS>
3.    應用程式商店中斷
<GooglePlay><iTunes><Windows Phone 市集><Windows 市集>

    - 雲端相依性中斷
[Azure 服務狀態]( http://azure.microsoft.com/status/)、[Amazon Web Services (AWS) 狀態]( http://status.aws.amazon.com/) 
    - 推送通知服務 (PNS) 相依性中斷
[Google - 服務](http://www.google.com/appsstatus#hl=en&v=status)，[Apple - 服務]( http://www.apple.com/support/systemstatus/)、[Android - Google GCM]( http://developer.android.com/google/gcm/index.html)、[Android - Amazon ADM]( https://developer.amazon.com/appsandservices/apis/engage/device-messaging)、[Apple - APNS]( https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html)、[Windows Phone - WNS](http://msdn.microsoft.com/library/windows/apps/hh465407.aspx)、[Windows Phone - MPNS](http://msdn.microsoft.com/library/windows/apps/ff402558(v=vs.105).aspx)、[Windows - WNS](https://developer.windows.com/)
    - 應用程式商店中斷
[GooglePlay](https://play.google.com/)、[iTunes](http://www.apple.com/itunes/charts/)、[Windows Phone 市集](http://www.windowsphone.com/)、[Windows 市集](http://windows.microsoft.com/)

*若要測試查看是否為系統性問題，您可以從下列不同的位置測試相同的函式：*

    - Azure Mobile Engagement integrated application
    - Test device
    - Test device OS version
    - Campaign
    - Administrator user account
    - Browser (IE, Firefox, Chrome, etc.)
    - Computer
*若要測試問題是否只影響 UI 或 API：*

    - Test the same function from both the Azure Mobile Engagement UI and the Azure Mobile Engagement API's.

[API 文件][Link 4]、[UI 文件][Link 1]
	
*若要測試是否是您的行動電話網路的問題：*

    - Test while connected to the Internet via WIFI and while connected via your 3G cellular phone network.
    - Confirm that your firewall is not blocking any of the Azure Mobile Engagement IP Addresses or Ports.

*若要測試是否是您的裝置的問題：*

    - Test if your Device is able to connect to Azure Mobile Engagement with another Azure Mobile Engagement integrated app.
    - Test that you can generate Events, Jobs, and Crashes from your phone that can be seen in the Azure Mobile Engagement UI. 
    - Test if you can send push notifications from the Azure Mobile Engagement UI to your device based on its Device ID. 
[UI 文件 - 設定][Link 1]

*若要測試是否是您的應用程式的問題：*

    - Install and test your application from an emulator instead of from a physical device:
        - Download and use Android SDK (includes an Android Emulator)
        - Download and use Apple Xcode (includes an iOS Simulator)
        - Download and use Windows Phone SDK (includes a Windows Phone 7, 8 and 8.1 Emulators)

若要測試是否是使用者裝置的作業系統升級的問題 (需要升級 SDK 才能解決)：

    - Test your application on different devices with different versions of the OS.
    - Confirm that you are using the most recent version of the SDK.
[疑難排解指南 - SDK][Link 2]
 
<a name="#SERVICE2">**徵兆：**
2. 連線和資訊不正確的問題：</a>

- 登入 Azure Mobile Engagement UI 時發生問題。
- 使用 Azure Mobile Engagement API 時發生連線錯誤。
- 透過裝置 API 上傳應用程式資訊標記時發生問題。
- 從 Azure Mobile Engagement 下載記錄檔或匯出資料時發生問題。
- Azure Mobile Engagement UI 中顯示的資訊不正確。
- Azure Mobile Engagement 記錄檔中顯示的資訊不正確。

**原因：**

- Azure Mobile Engagement 連線問題：
    - 確認您的使用者帳戶具備執行該作業所需的權限。
    - 確認問題不是因為一部電腦或是因為您的區域網路而產生。
    - 確認 Azure Mobile Engagement 服務沒有報告中斷。
    - 確認您的應用程式資訊標記遵循以下所有規則：
        - 僅使用 UTF8 字元集 (不支援 ANSI 字元集)。
        - 使用逗號 "," 做為分隔字元 (您可以開啟服務要求，來要求將 .csv 分隔字元從逗號變更為另一個字元，例如分號 ";")。
        - 使用全部大寫的布林值 "TRUE" 和 "FALSE"。
        - 使用小於 35MB 檔案大小上限的檔案。

**另請參閱：**

[API 文件][Link 4]、[UI 文件 - 首頁][Link 1]
 
<a name="#SERVICE3">**徵兆：**
3. 功能要求：</a>

- 您想要使用的功能似乎尚未存在於 Azure Mobile Engagement。

**原因：**

建議 Azure Mobile Engagement 中尚未存在的新功能：
    - 開啟 Azure Mobile Engagement 服務要求，盡可能提供您想要在 Azure Mobile Engagement 中看見之新功能的相關詳細資料。
 
## <a name="#SDK">SDK</a>

Azure Mobile Engagement 如何與您的應用程式整合的問題。

**徵兆清單：**

1. <a href="#SDK1">因為您應用程式的另一個區域中發生失敗而發現的 Azure Mobile Engagement SDK 的問題</a>
2. <a href="#SDK2">進階編碼問題</a>
3. <a href="#SDK3">應用程式當機問題</a>
4. <a href="#SDK4">應用程式商店上傳失敗</a>
 
<a name="#SDK1">**徵兆：**
1. 因為您應用程式的另一個區域中發生失敗而發現的 Azure Mobile Engagement SDK 的問題</a>

- UI 資料收集失敗 (在分析、監視、分割或儀表板中)。
- 推送失敗 (推送在應用程式內或應用程式外無法運作，或在兩個情況下都無法運作)。
- 進階功能失敗 (追蹤、地理位置或平台特定的推送無法運作)。
- API 失敗 (API 經常以無訊息模式失敗，沒有錯誤訊息)。
- 服務失敗 (所有 Azure Mobile Engagement 都無法在您的應用程式上運作)。

**原因：**

- 大部分需要解決的 Azure Mobile Engagement SDK 問題，都會因為您應用程式中發生失敗而發現 (例如 UI 資料收集失敗、推送失敗、進階功能失敗、API 失敗、應用程式當機或明顯的服務中斷)。  
- 如果 Azure Mobile Engagement 的某項特定功能從未在您的應用程式中運作過，表示您必須完成整合。 
- 如果 Azure Mobile Engagement 的某項特定功能曾運作並停止，表示您可能需要升級到 Azure Mobile Engagement SDK 的最新版本。請記住，Azure Mobile Engagement 支援的每個平台 (Android、iOS、Web、Windows 及 Windows Phone) 皆有不同的 Azure Mobile Engagement SDK 適用版本。

SDK 整合：** 

- Azure Mobile Engagement 未正確整合在 SDK 中 (分析)。
- Reach 未正確整合在 SDK 中 (應用程式內推送和應用程式外推送)。
- 憑證已過期或 PROD 與 DEV 不正確 (僅限 iOS)。
- GCM 或 ADM 未正確整合在 SDK 中 (僅限 Android - 服務特定推送)。
- 追蹤未正確整合在 SDK 中 (安裝存放區追蹤)。
- 延遲位置或 GPS 位置未正確整合在 SDK 中 (依地理位置設定目標)。
[SDK 文件 - 整合指南][Link 5]、[疑難排解指南 - 推送][Link 2]

*SDK 升級：*

- 必須升級 SDK 來解決較舊版本 SDK 的問題 (經常與裝置作業系統的較新版本有關)。
- 解除安裝您裝置上所有的舊版應用程式，並重新安裝最新版本的應用程式，然後從 Azure Mobile Engagement UI 重新註冊您的裝置識別碼，以確認您的裝置是使用最新版本的應用程式。
[SDK 文件 - 版本資訊](http://go.microsoft.com/fwlink/?LinkId= 525554)、[SDK 文件 - 升級指南](http://go.microsoft.com/fwlink/?LinkId= 525554)、[UI 文件 - 設定][Link 1]

*SDK 其他問題：*

- 與 Azure Mobile Engagement 相關之程式碼區段中的錯誤可能會導致 Azure Mobile Engagement 無法運作。
- 應用程式資訊清單 "AndroidManifest.xml" 中的錯誤可能會導致 Azure Mobile Engagement 無法運作 (僅限 Android)。
- SDK 整合與 
- API 使用方式的一個常見問題是將 SDK 金鑰和 API 金鑰混淆。
[概念 - 詞彙][Link 6]
 
<a name="#SDK2">**徵兆：**
2. 進階編碼問題：</a>

-  與 Azure Mobile Engagement 沒有直接相關的平台特定程式碼可能會造成在 iOS、Android 及 Windows Phone 上發生問題。

**原因：**

- 許多 Azure Mobile Engagement 的進階編碼問題，都是因為沒有正確撰寫，且與 Azure Mobile Engagement 沒有直接相關的平台特定程式碼而產生。除了Azure Mobile Engagement 文件 (Android、iOS、Web、Windows 及 Windows Phone) 之外，您也必須參考您正在開發之平台的特定文件。
- 未正確設定「類別」會導致無法從通知連結到應用程式內或應用程式外的另一個位置 (僅限 Android )。 
- 在您的 iOS 程式碼中未將 "UIKit.framework" 設定為 "optional"，會在較舊的 iOS 裝置上顯示「錯誤：找不到符號」及/或當機 (僅限 iOS)。
- 過期的憑證或未正確使用憑證的 DEV 或 Prod 版本，會造成推送問題 (僅限 iOS)。
- 平台中有一些固有限制是 Azure Mobile Engagement 無法控制的 (例如系統中心針對 Android 和 iOS 中的應用程式外推送所採用的運作方式)。
- Azure Mobile Engagement 發佈了 Azure Mobile Engagement 針對 iOS 與 Android 所使用之內部套件的完整清單以供參考。請記住，Azure Mobile Engagement 的部分功能為平台特定功能 (Android、iOS、Web、Windows 及 Windows Phone)。
- 適用於每個平台的 SDK 會以下列程式設計語言來撰寫：
    -     Android SDK 是以 Java 撰寫
    -     iOS SDK 是以 Objective C 撰寫
    -     Web SDK 是以 JavaScript 撰寫
    -     Windows SDK 是以 C# 與 JavaScript 撰寫
    -     Windows Phone SDK 是以 C# 與 JavaScript 撰寫

**另請參閱：**

 - [疑難排解指南 - 推送][Link 2]、[SDK 文件 - 版本資訊][Link 5]、[SDK 文件 - 升級指南][Link 5]、[SDK 文件 - Android - Azure Mobile Engagement 技術文件概觀][Link 5]、[SDK 文件 - iOS - Azure Mobile Engagement 技術文件概觀][Link 5]、[SDK 文件 - iOS - 如何準備您的應用程式以使用 Apple 推送通知][Link 5]、[Android 開發人員](https://developer.android.com/)、[iOS 開發人員](https://developer.apple.com/)、[Windows 開發人員](https://developer.windows.com/) 
 
<a name="#SDK3">**徵兆：**
3.    應用程式當機問題</a>

- 您的應用程式在使用者的裝置上當機。

**原因：**

- 當機資訊可以在分析 UI 或分析 API 中檢視。
- 您可以尋找測試裝置的裝置識別碼，並採取造成使用者使用應用程式時當機的相同動作，以協助識別您應用程式的當機原因。
- 升級為最新版本的 SDK 有時候會解決造成應用當機的 Azure Mobile Engagement SDK 已知問題，因此在調查當機原因時，請務必查閱平台的相關版本資訊。

**另請參閱：**

- [概念 - 常見問題集][Link 6]、[概念 - 詞彙][Link 6]、[API 文件 - 分析 API - 當機][Link 4]、[UI 文件 - 分析 - 當機][Link 1]、[UI 文件 - 設定][Link 1]、[SDK 文件 - 版本資訊][Link 5]、[SDK 文件 - 升級指南][Link 5]

<a name="#SDK4">**徵兆：**
4. 應用程式商店上傳失敗</a>

- 將您最新版本的應用程式上傳到 iTunes、GooglePlay、Windows 或 Windows Phone 市集的相關錯誤。

**原因：**

- 應用程式商店有時候會封鎖已啟用特定功能的應用程式 (iTunes Store 會防止商店中的應用程式使用 IDFV，GooglePlay 商店則是會防止應用程式之間共享應用程式資訊)。 
- 如果您無法將應用程式上傳至商店，請務必查閱平台與 SDK 目前版本的相關版本資訊。

**另請參閱：**

- [SDK 文件 - 版本資訊][Link 5]、[SDK 文件 - 升級指南][Link 5] 

## <a name="#SR">SR 疑難排解資訊</a>

當您開啟任何 Azure Mobile Engagement 服務要求時，請提供下列資訊：
 
**識別碼：請提供任何與您的問題有關的適當識別碼：**

    - App ID
    - Campaign ID
    - Device ID
    - User ID
    - Username
    - App Info Tag
 
**錯誤：請提供任何與您的問題有關的適當錯誤資訊：**

    - The name of the API or UI section where the issue occurs
    - The text of any error message you receive
    - The results of any tests you have performed from the troubleshooting guides
 
-    [疑難排解指南](http://go.microsoft.com/fwlink/?LinkId=524382)


**程式碼：請提供任何與您的問題有關的適當編碼資訊：**

    - The SDK version and platform of your app (Android SDK 2.4.1, iOS 1.16.2, etc.)
    - The download location of your production app (or the APK/TGZ files of your development app)
    - The "AndroidManifest.xml" and/or any code snippet from your app related to Azure Mobile Engagement (for advanced troubleshooting)

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
