<properties 
   pageTitle="Azure Mobile Engagement 使用者介面 - 設定" 
   description="Azure Mobile Engagement 設定區段的使用者介面概觀" 
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

# 設定
 
可用的 [設定] 功能表選項會視應用程式不同而改變，需視應用程式平台和針對應用程式授與您的權限而定。[設定] 包括以下項目：詳細資料、專案、原生推送、推送速度、SDK、追蹤、應用程式資訊、商業壓力及權限。UI 之 [設定] 區段的選項中，只有 [應用程式資訊] 功能表選項包含可以使用裝置 API 之「標記」功能管理的元素。「概念」中的「詞彙」包括術語和縮寫的定義，例如下列項目：APNS、GCM、IDFA、API、SDK、API Key、SDK Key、應用程式識別碼 (App ID)、AppStore ID、標記計畫、使用者識別碼、裝置識別碼、應用程式委派、堆疊追蹤，以及深層連結。

**另請參閱：** 

- [API 文件 - 裝置 API][Link 4]、[概念 - 詞彙][Link 6]、[移難排解指南 - 服務][Link 2]
 
  ![settings1][46]

## 詳細資料：
可讓您變更應用程式的名稱與描述 
檢視您應用程式的擁有者與您的角色權限。 
分析設定：可讓您檢視或變更開始週次與天次，以及保留時間 (天)
 
  ![settings2][47]
 
## 專案：
可讓您選取您要應用程式在其中出現的所有專案。 
您也可以搜尋專案及檢視您的應用程式所屬之任何專案的名稱、描述、擁有者及您的角色權限。

**另請參閱：**

-    [UI 文件 - 首頁][Link 13]
 
  ![settings3][48]

## 原生推送：
可讓您註冊新憑證來與原生推送搭配使用，或刪除現有憑證。 
原生推送可讓 Azure Mobile Engagement 在任何時間 (甚至在它沒有執行時) 推送至您的應用程式。 
在為至少一項原生推送服務提供認證或憑證之後，您可以在建立觸達活動時選擇 "Any time"，也可以使用 PUSH API 中的 "notifier" 參數。

**另請參閱：**

- [API 文件 - 觸達 API] [Link 4]、[API 文件 - 推送 API] [Link 4]、[UI 文件 - 觸達 - 新推送活動][Link 17]
 
**Apple Push Notification Service (APNS)**

若要讓原生推送使用 Apple Push Notification Service，您必須註冊您的憑證。您將需要指定憑證類型為開發 (DEV) 或生產 (PROD)。然後您需要上傳您的憑證與密碼。

**另請參閱：**

- [SDK 文件 - iOS - 如何準備您的應用程式以使用 Apple 推送通知][Link 5]
 
![settings4][49]
 
**Windows 推送通知服務 (WPNS)**

若要讓原生推送使用 Windows 通知服務，您必須提供應用程式的憑證。 
您將需要您的封裝安全性識別碼 (SID) 與您的秘密金鑰。
 
![settings5][50]
 
**使用 GCM 或 ADM 的 Android 原生推送**

**適用於 Android 的 Google 雲端通訊 (GCM)**

若要讓原生推送使用 GCM，您需要依照 http://developer.android.com/guide/google/gcm/gs.html 中的指示進行。 
然後，您必須貼上在沒有 IP 限制下設定的伺服器簡易 API 金鑰。 
需要與 Android v1.12.0+ 的 SDK 整合。

**另請參閱：**

- [SDK 文件 - Android - 如何整合 GCM][Link 5]
 
**適用於 Android 的 Amazon Device Messaging (ADM)**

若要讓原生推送使用 ADM，您必須提供由用戶端識別碼和用戶端密碼 (需要與 Android v2.1.0+ 的 SDK 整合) 組成的 <OAuth 認證>。

**另請參閱：**

- [SDK 文件 - Android - 如何整合 ADM][Link 5]

![settings6][51]

## 推送速度：
顯示您的應用程式目前的推送速度，而且可讓您定義應用程式的推送速度。推送速度會定義觸達模組將執行之每秒推送數目的上限。當您擁有的伺服器在活動啟動後因為每秒要求 (/s) 數目太多而過載時，這會很有幫助。
 
  ![settings7][52]

## 追蹤：
追蹤功能可讓您追蹤您的 iOS 與 Android 應用程式的安裝原始來源。它可以讓您知道使用者從哪裡下載您的應用程式 (例如，從哪一個應用程式商商店)，以及那個來源讓他們知道您的應用程式 (例如，廣告活動、部落格、網站、電子郵件、簡訊等等)。Azure Mobile Engagement 的追蹤功能必須透過個別步驟從 SDK 整合到您的應用程式。 

**另請參閱：**

- [SDK 文件 - Android - 如何整合][Link 5]、[SDK 文件 - iOS - 如何整合][Link 5]
 
**商店**

可讓您依據商店的名稱與相關聯的下載 URL 來註冊可下載您的應用程式的所有商店。這樣做可讓您建立位置以待管追蹤 URL。可以從這個頁面建立或刪除商店。使用圖示來建立新的追蹤 URL 會將您帶到下述追蹤 URL 頁面。有幾種方式可以追蹤您的使用者從哪裡下載您的應用程式：

-    使用協力廠商 AD 伺服器 (Azure Mobile Engagement 目前支援 [SmartAd](http://smartadserver.fr/) 與 [Surikate](http://www.surikate.com/))。
-    使用協力廠商屬性服務 (Azure Mobile Engagement 目前支援 [Mobile App Tracking]，( http://www.mobileapptracking.com/) 且不久之後也可能會支援 [Trademob](http://www.trademob.com/))。
-    使用協力廠商安裝推薦者 (Azure Mobile Engagement 目前支援 [Google Play Install Referrer](https://developers.google.com/app-conversion-tracking/docs/third-party-trackers/) - 僅限 Android)。
-    使用手動報告。
 
  ![settings8][53]
 
**廣告活動**
可讓您建立由 AD 伺服器名稱、活動識別碼及可下載您應用程式之來源所組成的「新廣告活動」。 
 
  ![settings9][54]
 
**追蹤 URL**
可讓您建立追蹤 URL 以在您的來源 (廣告活動、部落格、網站、電子郵件、簡訊等等) 中作為目標 URL 使用，來將使用者重導至他們可以下載您的應用程式的商店。也可以讓您顯示您已經建立的所有追蹤 URL。追蹤 URL 可用來作為廣告活動或觸達宣告的動作 URL，以邀請您的使用者從另一個應用程式下載您其中一個應用程式。追蹤 URL 會重導至與所選商店相關聯的下載 URL，同時可讓我們的追蹤系統記錄應用程式在安裝時是從哪一個商店下載。如果已提供來源，我們的追蹤系統也會記錄該來源，讓您可以在您為應用程式建立的各種廣告活動之間區分。

**另請參閱：**

- [UI 文件 - 觸達 - 新推送活動][Link 17] 
 
建立新追蹤 URL 時，您必須指定商店與 [無] 或 [客戶] 來源，或新增伺服器。 

-    指定 [無] 來源時會建立一個預設追蹤 URL。
-    指定 [客戶] 來源時，可讓您指定外部伺服器的 URL 以下載您的應用程式。
-    廣告伺服器的來源會在預設指定的廣告伺服器中建立一個預設追蹤 URL。
 
**建立追蹤 URL**
您也可以建立一個讓使用者能夠從新觸達活動的內容區段內部下載您其中一個應用程式的追蹤 URL。

**另請參閱：**

- [UI 文件 - 觸達 - 推送內容][Link 17]
 
![settings10][55]

## 應用程式資訊：
可讓您註冊與您應用程式的使用者相關聯的其他資訊。此資訊可以由您的應用程式 (使用 SDK) 或由您的後端 (使用裝置 API) 插入。 
**另請參閱：**

- [API 文件 - 裝置 API][Link 4]

註冊應用程式資訊標記可讓您透過依據觸達對象建立特定觸達對象條件，來區隔您的觸達活動。您可以檢視現有應用程式資訊標記的名稱與類型，或依據名稱與字串、日期、整數或布林值的類型新增新的應用程式資訊。

**另請參閱：**

- [UI 文件 - 觸達 - 新推送活動][Link 17]
 
![settings11][56]
 
## 商業壓力： 
推送配額可讓您定義在一段時間內可向裝置推送的次數上限。只有已經啟用 [套用推送配額] 的觸達活動才會使用推送配額。推送配額可依據區段或預設值來管理。配額可以設定為僅在最後一個小時、最後一天、最後一週或最後一個月期間傳送達到次數上限的推送次數。

**另請參閱：**

- [UI 文件 - 觸達 - 新推送活動][Link 17]、[UI 文件 - 區段][Link 18]
 
**配額**

- 依預設值區分配額：此配額會套用至與下面 '依區段區分配額'區段中的任何區段不符的使用者。預設並未設定任何配額。
- 依區段區分配額：因為配額必須套用至一組使用者，所以您必須建立區段來識別要套用配額的使用者。這可能是依據使用者所建立的工作階段數目，或依據您認為有趣而想要用來定義使用者群組的任何條件，來定義的 '重度使用者'區段。如果裝置與數個已定義配額的區段相符，則只會套用每小時推送次數最高的配額。

![settings12][57]
 
## Permissions:
可讓您搜尋及檢視您應用程式之使用者的電子郵件、名稱、組織及權限層級。權限概念是專案角色的一個新增概念。它可以讓您將一組權限和擁有您應用程式存取權的特定使用者關聯。

**目前可用的權限等級如下：**

-    "Reach Campaign Creator" (可建立觸達活動的使用者) 
-    "Reach Campaign Manager" (可建立觸達活動及啟用、暫停、完成、終結觸達活動的使用者)

> 注意：當使用者同時擁有專案角色與指定應用程式的一組權限時，就會使用較寬鬆的概念。因此，我們建議您在使用權限時，將您使用者的專案角色設定為 "Viewer" (最低權限角色)，並在應用程式層級新增較寬鬆的權限。

**另請參閱：**

- [UI 文件 - 首頁][Link 13]  
 
![settings13][58]

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
