<properties 
   pageTitle="Azure Mobile Engagement 疑難排解指南 - 服務" 
   description="Azure Mobile Engagement 疑難排解" 
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
   ms.date="02/17/2015"
   ms.author="piyushjo"/>


# 服務問題的疑難排解指南

以下是您可能會遇到，有關 Azure Mobile Engagement 執行的問題。

## 服務中斷

### 問題
- 似乎是因為 Azure Mobile Engagement 服務中斷所造成的問題。

### 原因
- 似乎是因為 Azure Mobile Engagement 服務中斷所造成的問題，可能由數種不同原因所造成：
    - 原本顯示為 Azure Mobile Engagement 系統問題的隔離問題
    - 伺服器關閉所造成的已知問題 (不一定會顯示在伺服器狀態中)：
	- 排程延遲、目標錯誤、徽章更新問題、統計資料停止收集、推播停止運作、API 停止運作、無法建立新的應用程式或使用者、DNS 錯誤，以及 UI、API 或裝置上應用程式中的逾時錯誤。
    - 雲端相依性中斷 [Azure 服務狀態](http://status.azure.com/), [Amazon Web Services (AWS) 狀態](http://status.aws.amazon.com/) 
    - 推送通知服務 (PNS) 相依性中斷 [Google - 服務](http://www.google.com/appsstatus#hl=en&v=status), [Apple - 服務](http://www.apple.com/support/systemstatus/), [Android - Google GCM](http://developer.android.com/google/gcm/index.html), [Android - Amazon ADM](https://developer.amazon.com/appsandservices/apis/engage/device-messaging), [Apple - APNS](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html), [Windows Phone - WNS](http://msdn.microsoft.com/library/windows/apps/hh465407.aspx), [Windows Phone - MPNS][LinkMPNS], [Windows - WNS](https://developer.windows.com/)
    - 應用程式商店中斷 [GooglePlay](https://play.google.com/), [iTunes](http://www.apple.com/itunes/charts/), [Windows Phone 市集](http://www.windowsphone.com/), [Windows 市集](http://windows.microsoft.com/)

若要測試查看是否為系統性問題，您可以從下列不同的位置測試相同的函式：**

    - Azure Mobile Engagement integrated application
    - Test device
    - Test device OS version
    - Campaign
    - Administrator user account
    - Browser (IE, Firefox, Chrome, etc.)
    - Computer
若要測試問題是否只影響 UI 或 API：**

    - Test the same function from both the Azure Mobile Engagement UI and the Azure Mobile Engagement API's.

[API 文件][Link 4], [UI 文件][Link 1]

若要測試是否是您的行動電話網路的問題：**

    - Test while connected to the Internet via WIFI and while connected via your 3G cellular phone network.
    - Confirm that your firewall is not blocking any of the Azure Mobile Engagement IP Addresses or Ports.

若要測試是否是您的裝置的問題：**

    - Test if your Device is able to connect to Azure Mobile Engagement with another Azure Mobile Engagement integrated app.
    - Test that you can generate Events, Jobs, and Crashes from your phone that can be seen in the Azure Mobile Engagement UI. 
    - Test if you can send push notifications from the Azure Mobile Engagement UI to your device based on its Device ID. 
[UI 文件 - 設定][Link 1]

若要測試是否是您的應用程式的問題：**

    - Install and test your application from an emulator instead of from a physical device:
        - Download and use Android SDK (includes an Android Emulator)
        - Download and use Apple Xcode (includes an iOS Simulator)
        - Download and use Windows Phone SDK (includes a Windows Phone 7, 8 and 8.1 Emulators)

若要測試是否是使用者裝置的作業系統升級的問題 (需要升級 SDK 才能解決)：

    - Test your application on different devices with different versions of the OS.
    - Confirm that you are using the most recent version of the SDK.
[疑難排解指南 - SDK][Link 2]
 
## 連線和資訊不正確的問題

### 問題
- 登入 Azure Mobile Engagement UI 時發生問題。
- 使用 Azure Mobile Engagement API 時發生連線錯誤。
- 透過裝置 API 上傳應用程式資訊標記時發生問題。
- 從 Azure Mobile Engagement 下載記錄檔或匯出資料時發生問題。
- Azure Mobile Engagement UI 中顯示的資訊不正確。
- Azure Mobile Engagement 記錄檔中顯示的資訊不正確。

### 原因
- Azure Mobile Engagement 連線問題：
    - 確認您的使用者帳戶具備執行該作業所需的權限。
    - 確認問題不是因為一部電腦或是因為您的區域網路而產生。
    - 確認 Azure Mobile Engagement 服務沒有報告中斷。
    - 確認您的應用程式資訊標記遵循以下所有規則：
        - 僅使用 UTF8 字元集 (不支援 ANSI 字元集)。
        - 使用逗號 "," 做為分隔字元 (您可以開啟服務要求，來要求將 .csv 分隔字元從逗號變更為另一個字元，例如分號 ";")。
        - 使用全部小寫的布林值 "true" 和 "false"。
        - 使用小於 35MB 檔案大小上限的檔案。

### 另請參閱

[API 文件][Link 4], [UI 文件 - 首頁][Link 1]
 
## 功能要求

### 問題
- 您想要使用的功能似乎尚未存在於 Azure Mobile Engagement。

### 原因

若要提供 Azure Mobile Engagement 尚未存在的新功能建議：- 開啟 Azure Mobile Engagement 服務要求，盡可能提供您想要在 Azure Mobile Engagement 中看見之新功能的相關詳細資料。

### 另請參閱

[Mobile Engagement 意見反應/要求增加功能](http://feedback.azure.com/forums/285737-mobile-engagement)

<!--Link references-->
[Link 1]: mobile-engagement-user-interface.md
[Link 2]: mobile-engagement-troubleshooting-guide.md
[Link 3]: mobile-engagement-how-tos.md
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=azuremobileengagement
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
[Link 27]: mobile-engagement-user-interface-reach-campaign.md
[Link 28]: mobile-engagement-user-interface-reach-criterion.md
[Link 29]: mobile-engagement-user-interface-reach-content.md
[LinkMPNS]: http://msdn.microsoft.com/library/windows/apps/ff402558(v=vs.105).aspx

<!--HONumber=54--> 