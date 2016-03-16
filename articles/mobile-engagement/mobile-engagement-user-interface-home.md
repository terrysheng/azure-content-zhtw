<properties 
   pageTitle="Azure Mobile Engagement 使用者介面 - 首頁" 
   description="了解如何使用 Azure Mobile Engagement 管理現有的應用程式和專案" 
   services="mobile-engagement" 
   documentationCenter="" 
   authors="piyushjo" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="mobile-engagement"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="mobile-multiple"
   ms.workload="mobile" 
   ms.date="11/29/2015"
   ms.author="piyushjo"/>

# 如何管理現有的應用程式和專案


本文說明 **Mobile Engagement** 入口網站的**首頁**。使用 **Mobile Engagement** 入口網站可監視與管理您的行動應用程式。請注意，若要開始使用入口網站，您必須先建立 **Azure Mobile Engagement** 帳戶。如需詳細資訊，請參閱[建立 Azure Mobile Engagement 帳戶](mobile-engagement-create-account.md)。
 
若要前往首頁，請按一下頁面左上角的**首頁**。若您的應用程式屬於所選集合，則會全部列於此處。此頁面上只會顯示您應用程式的快速概觀。
   
可包含您帳戶中任何應用程式的專案也會顯示在首頁。請注意，任何人都可以建立帳戶以存取 UI 的首頁，但是您必須將權限授與其他使用者，他們才能在**我的專案**中存取您自訂的應用程式。

您也可以檢視所選應用程式的比較圖表。或者，您也可以選擇檢視專案中所選應用程式的比較圖表。

![Home1][0]


## 我的應用程式

這裡會大略介紹您的應用程式，讓您選取想要開啟的應用程式，以檢視詳細的功能區選項。您可以按一下應用程式的名稱，回到應用程式中最近瀏覽的功能區位置，或者按一下齒輪圖示，直接移至應用程式的 [設定] 頁面。您可以搜尋、篩選或排序應用程式表格上所顯示的資訊。您也可以拖放欄標題來變更順序。
 
此外，應用程式的概觀包括：

- **新使用者趨勢**：新使用者過去兩個星期的發展。
- **作用中使用者**：過去 30 天的作用中使用者數目。
- **作用中使用者趨勢**：作用中使用者過去兩個星期的發展。
- **工作階段**：工作階段是由使用者執行的一次應用程式使用，從使用者開始使用的時間，直到使用者停止的時間為止。 
- **工作階段趨勢**：工作階段過去兩個星期的發展。

按一下應用程式後，您就可以開始透過使用者介面監視和管理您的應用程式。例如：

- [監視應用程式的即時資料](mobile-engagement-user-interface-monitor.md)
- [分析應用程式的歷史資料](mobile-engagement-user-interface-analytics.md)
- [建立和管理使用者的使用者分佈以識別使用模式](mobile-engagement-user-interface-segments.md)
- [透過推播通知觸及應用程式的使用者](mobile-engagement-user-interface-reach.md)
 
## 我的專案

您可以使用專案將您的應用程式分組，並將權限授與其他使用者以便存取您的應用程式。您可以透過提供電子郵件地址將權限授與其他使用者。使用 [**新增專案**] 按鈕，只要輸入新專案的「名稱」和「說明」，就可以建立新專案。專案建立之後，可以按一下專案名稱來編輯產品的名稱和說明，以及選取您想要在此專案中查看的所有應用程式。


![Home6][60]

角色包括：

- **檢視者**：只能檢視與專案相關聯之應用程式的使用者。檢視者可以存取分析和監視資料，並查看觸達結果。檢視者無法變更任何資訊，也無法管理應用程式或使用者。檢視者無法建立或變更觸達活動狀態。
- **開發人員**：除了具備檢視者的權限之外，還可管理應用程式的使用者。開發人員可以啟用和停用應用程式、變更應用程式的資訊 (例如封裝和簽章)，以及建立觸達活動。開發人員無法管理使用者。 
- **管理員**：除了具備開發人員的權限之外，還可管理使用者的使用者。管理員可以邀請使用者加入專案、變更使用者角色，還可以變更專案的資訊。在 [設定] 中也可以設定應用程式層級權限。


按一下專案即可檢視所有屬於此專案的應用程式。下圖顯示所選應用程式的比較圖表。

![Home2][3]


## 另請參閱

- [概念][Link 6]
- [疑難排解指南服務][Link 24]

<!--Image references-->
[0]: ./media/mobile-engagement-user-interface-home/home0.png
[1]: ./media/mobile-engagement-user-interface-navigation/navigation1.png
[2]: ./media/mobile-engagement-user-interface-home/home1.png
[3]: ./media/mobile-engagement-user-interface-home/home2.png
[4]: ./media/mobile-engagement-user-interface-home/home3.png
[5]: ./media/mobile-engagement-user-interface-home/home4.png
[6]: ./media/mobile-engagement-user-interface-home/home5.png
[60]: ./media/mobile-engagement-user-interface-home/home6.png
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
[20]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign1.png
[21]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign2.png
[22]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign3.png
[23]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign4.png
[24]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign5.png
[25]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign6.png
[26]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign7.png
[27]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign8.png
[28]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign9.png
[29]: ./media/mobile-engagement-user-interface-reach-criterion/Reach-Criterion1.png
[30]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content1.png
[31]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content2.png
[32]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content3.png
[33]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content4.png
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
[Link 1]: mobile-engagement-user-interface.md
[Link 2]: mobile-engagement-troubleshooting-guide.md
[Link 3]: mobile-engagement-how-tos.md
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/home?forum=azuremobileengagement
[Link 9]: http://azure.microsoft.com/services/mobile-engagement/
[Link 10]: http://azure.microsoft.com/documentation/services/mobile-engagement/
[Link 11]: http://azure.microsoft.com/pricing/details/mobile-engagement/
[Link 12]: mobile-engagement-user-interface-navigation.md
[Link 13]: mobile-engagement-user-interface-home.md
[Link 14]: mobile-engagement-user-interface-my-account.md
[Link 15]: mobile-engagement-user-interface-analytics.md
[Link 16]: mobile-engagement-user-interface-monitor.md
[Link 17]: mobile-engagement-user-interface-reach.md
[Link 18]: mobile-engagement-user-interface-segments.md
[Link 19]: mobile-engagement-user-interface-dashboard.md
[Link 20]: mobile-engagement-user-interface-settings.md
[Link 21]: mobile-engagement-troubleshooting-guide-analytics.md
[Link 22]: mobile-engagement-troubleshooting-guide-apis.md
[Link 23]: mobile-engagement-troubleshooting-guide-push-reach.md
[Link 24]: mobile-engagement-troubleshooting-guide-service.md
[Link 25]: mobile-engagement-troubleshooting-guide-sdk.md
[Link 26]: mobile-engagement-troubleshooting-guide-sr-info.md
[Link 27]: ../mobile-engagement-how-tos-first-push.md
[Link 28]: ../mobile-engagement-how-tos-test-campaign.md
[Link 29]: ../mobile-engagement-how-tos-personalize-push.md
[Link 30]: ../mobile-engagement-how-tos-differentiate-push.md
[Link 31]: ../mobile-engagement-how-tos-schedule-campaign.md
[Link 32]: ../mobile-engagement-how-tos-text-view.md
[Link 33]: ../mobile-engagement-how-tos-web-view.md


 
 

<!---HONumber=AcomDC_1203_2015-->