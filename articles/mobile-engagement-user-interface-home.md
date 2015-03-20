<properties 
   pageTitle="Azure Mobile Engagement 使用者介面 - 首頁" 
   description="Azure Mobile Engagement [首頁] 區段的使用者介面概觀" 
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

# 首頁
 
UI 的 [首頁] 區段包含 [我的應用程式] 中所有的應用程式清單，並且可以授與其他權限給您的應用程式。任何人都可以建立帳戶存取 UI 的首頁，但是您必須授與其他使用者權限，他們才能在 [我的專案] 中存取您自訂的應用程式。

**另請參閱：** 

-  [疑難排解指南 - 服務][Link 2]
 
## 我的應用程式：
 
![Home1][2]

這裡會大略介紹您的應用程式，讓您選取想要開啟的應用程式，以檢視詳細的功能區選項。您可以按一下應用程式的名稱，回到應用程式中最近瀏覽的功能區位置，或者按一下齒輪圖示，直接移至應用程式的 [設定] 頁面。您可以搜尋、篩選或排序應用程式表格上所顯示的資訊。您也可以拖放欄標頭來變更順序。 
 
應用程式的概觀包括：

- 使用者總計 (使用者總數)
- 新使用者趨勢 (新使用者過去兩個星期的發展)
- 作用中使用者 (過去 30 天的作用中使用者數目)
- 作用中使用者趨勢 (作用中使用者過去兩個星期的發展)
 
您也可以查看比較您的應用程式的圖表。

- 顯示比較圖表 (可以圖表形式顯示應用程式資料)
- 核取方塊 (新增此應用程式至比較圖表或從中移除)
 
![Home2][3]

## 我的專案：
 
![Home5][6]

您可以使用專案來將您的應用程式分組，並且授與權限給您的應用程式。使用 [新增專案] 按鈕，只要輸入新專案的「名稱」和「說明」，就可以建立新專案。專案建立之後，您可以按一下專案名稱來編輯產品的名稱和描述，以及選取您想要在此專案中查看的所有應用程式。您也可以刪除此專案，不會破壞它所參考的應用程式。不過這樣一來，您將無法存取不屬於您的所有應用程式，也無法從另一個專案存取應用程式。因此，請務必小心！ 
您也可以根據電子郵件地址邀請使用者到您的專案。這些使用者必須先在 Azure Mobile Engagement 中建立帳戶，您才能授與他們權限。 

**角色包括：** 

- 檢視者：檢視者是只能檢視與專案相關聯之應用程式的使用者。檢視者可以存取分析和監視資料，並查看觸達結果。檢視者無法變更任何資訊，也無法管理應用程式或使用者。檢視者無法建立或變更觸達活動狀態。
- 開發者：開發者是除了具備檢視者的權限之外，還可管理應用程式的使用者。開發者可以啟用和停用應用程式、變更應用程式的資訊 (例如封裝和簽章)，以及建立觸達活動。開發者無法管理使用者。 
- 管理者：管理者是除了具備開發者的權限之外，還可管理使用者的使用者。管理者可以邀請使用者加入專案、變更使用者角色，還可以變更專案的資訊。在 [設定] 中也可以設定應用程式層級權限。
 
**另請參閱：** 

-  [UI 文件 - 設定][Link 20]

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
