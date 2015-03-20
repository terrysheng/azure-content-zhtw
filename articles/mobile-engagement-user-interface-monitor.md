<properties 
   pageTitle="Azure Mobile Engagement 使用者介面 - 監視" 
   description="Azure Mobile Engagement [監視] 區段的使用者介面概觀" 
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

# 監視
 
UI 的 [監視] 區段提供即時的分析資訊，而且可讓您設定警示，當 UI 的 [分析] 區段中過去可取得的大部分相同資訊達到臨界值時觸發警示。＜概念＞中的「詞彙」提供 [分析] 和 [監視] 中的術語和縮寫定義，例如：作用中使用者、新使用者、保留使用者、工作階段、使用者路徑圖表、使用者地圖、追蹤 URL、趨勢、活動、事件、作業、錯誤、額外資訊、當機以及應用程式資訊。

**另請參閱：** 

-  [概念 - 詞彙][Link 6]、[疑難排解指南 - 分析][Link 2]

## 監視 - 工作階段、作業、事件、錯誤和當機：
您可以查看有多少使用者目前正在工作階段和特定畫面，或執行特定動作。您可以檢視每個工作階段、作業、事件、錯誤和當機的使用者活動。您可以查看目前的資訊，並顯示過去一小時、過去一天或過去一週的資訊。您可以查看每個類別中的所有資訊，或者依特定工作階段、作業、事件、錯誤和當機排序資訊。在如推送活動等事件期間，若要查看傳送推送通知後是否立即發生上報動作時，即時監視就會很好用。 
 
![Monitor1][14]  

## [監視] - [事件] - [詳細資料] 的疑難排解：
若要尋找測試裝置的「裝置識別碼」，並且確認您的應用程式已整合 Azure Mobile Engagement 的 [分析]、[監視] 和 [區段] 運作，其中一個最簡單的方式就是在測試裝置的應用程式中產生事件，並且在 [監視] - [事件] - [詳細資料] 中尋找。找到測試裝置的「裝置識別碼」之後，就可以在 [我的帳戶] - [裝置] 中將它加入您的測試裝置。如果您無法產生事件，請確定已使用 SDK 將 Azure Mobile Engagement 正確整合到您的 Android/iOS/Web/Windows/Windows Phone 應用程式中。

**另請參閱：** 

-  [SDK 文件][Link 5]

![Monitor2][15]  

## [監視] - [當機] - [詳細資料] 的疑難排解：
您可以從 [監視] - [當機] - [詳細資料] 檢閱有關您應用程式的當機資訊，協助判斷應用程式當機的原因。您也應該針對 Android/iOS/Web/Windows/Windows Phone 的每個 SDK 版本，查閱版本資訊中每個 SDK 版本的已知問題。 

**另請參閱：** 

-  [SDK 文件 - 版本資訊][Link 5]

![Monitor3][16] 

## [監視] - [警示]：
您也可以指定警示的條件，當達到條件時，警示會透過電子郵件或立即訊息自動傳送給您。(支援任何與 XMPP 相容的服務，例如 Google 的 GTalk 或 Apple 的 iChat)。警示是根據預先定義的偵測臨界值，當偵測到大於 (>) 或小於 (<) 每秒鐘、每分鐘或每小時的特定工作階段數、作業數、事件數、錯誤數或當機數時會觸發警示。警示可以監視指定類型的所有活動，或者只監視特定的作業、事件或錯誤活動。您也可以指定「最小偵測率」，這是對於相同警示的兩個通知之間間隔的最小時間量，確保當您的警示被觸發時，您在每 X 分鐘之內只會收到 1 個通知。
 
![Monitor4][17]

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
