<properties 
   pageTitle="Azure Mobile Engagement 使用者介面 - 設定" 
   description="了解如何使用 Azure Mobile Engagement 管理應用程式的全域設定" 
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

# 如何管理應用程式的全域設定

視應用程式平台和您所擁有的該應用程式權限而定，應用程式中所提供的 [**設定**] 功能表選項將有所不同。設定中包含：[詳細資料]、[專案]、[原生推送]、[推送速度]、 [標記 (應用程式資訊)] 和 [商業壓力]。[設定] 區段中的 [標記 (應用程式資訊)] 功能表選項可透過您的應用程式 (使用 SDK) 或您的後端 (使用裝置 API) 管理。


>[AZURE.NOTE]許多 **Mobile Engagement** 入口網站 UI 的區段含有 [**顯示說明**] 按鈕。按該按鈕，可獲得關於區段的詳細內容資訊。

## 詳細資料

可讓您變更您應用程式的名稱與說明。檢視您應用程式的擁有者與您的角色權限。

分析組態可讓您檢視或變更每星期的第一天以及保留時間 (以天為單位)。
 
  ![settings1][46]
 
## 專案

可讓您選取您要應用程式在其中出現的所有專案。

您也可以搜尋專案及檢視您的應用程式所屬之任何專案的名稱、描述、擁有者及您的角色權限。

如需詳細資訊，請參閱 [UI 文件 – 首頁][Link 13]
 
  ![settings3][48]

## 原生推送

可讓您註冊新憑證來與原生推送搭配使用，或刪除現有憑證。原生推送可讓 Azure Mobile Engagement 在任何時間 (甚至在它沒有執行時) 推送至您的應用程式。

在為至少一項原生推送服務提供認證或憑證之後，您可以在建立觸達活動時選擇 [任何時間]，也可以使用 PUSH API 中的 "notifier" 參數。



### Apple Push Notification Service (APNS)

若要讓原生推送使用 Apple Push Notification Service，您必須註冊您的憑證。您將需要指定憑證類型為開發 (DEV) 或生產 (PROD)。然後您需要上傳您的憑證與密碼。

如需詳細資訊，請參閱：[SDK 文件 - iOS - 如何準備您的應用程式以使用 Apple 推播通知][Link 5]
 
![settings4][49]
 
### Windows 推播通知服務 (WPNS)

若要讓原生推送使用 Windows 推播通知，您必須提供應用程式的憑證。您將需要您的封裝安全性識別碼 (SID) 與您的秘密金鑰。
 
![settings5][50]
 
### Google Cloud Messaging for Android (GCM)

若要讓原生推送使用 GCM，您必須依照 Google 的指示進行。然後，您必須貼上在沒有 IP 限制下設定的伺服器簡易 API 金鑰。需要與 SDK for Android 1.12.0 版以上整合。

如需詳細資訊，請參閱：

- [SDK 文件 Android 如何整合 GCM][Link 5]
- [Google 開發人員 GCM 指南](http://developer.android.com/guide/google/gcm/gs.html)
 
### Android 的 Amazon 裝置傳訊 (ADM)

若要讓原生推送使用 ADM，您必須提供由用戶端識別碼和用戶端密碼組成的 <OAuth credentials> (需要與 Android 2.1.0 版以上的 SDK 整合)。

如需詳細資訊，請參閱：

- [SDK 文件 Android 如何整合 ADM][Link 5]
- [Amazon 開發人員 ADM 文件](https://developer.amazon.com/sdk/adm/credentials.html#Getting)
 
![settings6][51]

## 推送速度

顯示您的應用程式目前的推送速度，而且可讓您定義應用程式的推送速度。
 
  ![settings7][52]

## 標記 (應用程式資訊)

![settings11][56]
  
## 商業壓力


![settings12][57]


## 另請參閱

- [概念][Link 6]
- [疑難排解指南服務][Link 24]

 

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