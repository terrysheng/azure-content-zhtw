<properties linkid="develop-mobile-tutorials-get-started-offline-data-ios" urlDisplayName="Getting Started with Offline Data" pageTitle="Get started with offline data in Mobile Services (iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to cache and sync offline data in your iOS application" metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Get started with offline data sync in Mobile Services" authors="wesmc" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="wesmc"></tags>

# 開始在行動服務中使用離線資料同步

<div class="dev-center-tutorial-selector sublanding">
<a href="/zh-tw/documentation/articles/mobile-services-windows-store-dotnet-get-started-offline-data" title="Windows 市集 C#">Windows 市集 C#</a>
<a href="/zh-tw/documentation/articles/mobile-services-windows-phone-get-started-offline-data" title="Windows Phone">Windows Phone</a>
<a href="/zh-tw/documentation/articles/mobile-services-ios-get-started-offline-data" title="iOS" class="current">iOS</a>
<a href="/zh-tw/documentation/articles/mobile-services-xamarin-ios-get-started-offline-data" title="Xamarin.iOS">Xamarin.iOS</a>
<a href="/zh-tw/documentation/articles/mobile-services-xamarin-android-get-started-offline-data" title="Xamarin.Android">Xamarin.Android</a>
</div>

本教學課程將說明行動服務的離線同步功能，此功能可讓開發人員撰寫即使在使用者沒有網路存取的情況下仍可使用的應用程式。

離線同步具有幾種潛在用途：

-   在裝置上本機快取伺服器資料，以改善應用程式回應性
-   讓應用程式能夠在網路連線中斷後恢復
-   讓使用者即使在沒有網路存取的情況下仍能建立及修改資料，而支援連線微弱或無連線的情況
-   同步多個裝置之間的資料，並在兩個裝置修改相同的記錄時偵測衝突

本教學課程將說明如何更新[開始使用行動服務][開始使用行動服務]或[開始使用資料][開始使用資料]教學課程中的應用程式，以支援 Azure 行動服務的離線功能。接著，您會在中斷連線的離線狀態下新增資料、將這些項目同步處理至線上資料庫，然後登入 Azure 管理入口網站，以檢視執行應用程式時對資料所做的變更。

> [WACOM.NOTE] 若要完成此教學課程，您需要 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 [Azure 免費試用][Azure 免費試用]。

## 取得範例應用程式

**完整教學課程即將推出。在這段期間，請參閱範例 [iOS 離線待辦事項清單][iOS 離線待辦事項清單]和[同好部落格貼文][同好部落格貼文]。**

<!-- ## Next steps

* [How to use the Mobile Services client library for iOS]
   -->

<!-- URLs. -->
[Windows 市集 C#]: /zh-tw/documentation/articles/mobile-services-windows-store-dotnet-get-started-offline-data "Windows 市集 C#"
[Windows Phone]: /zh-tw/documentation/articles/mobile-services-windows-phone-get-started-offline-data "Windows Phone"
[iOS]: /zh-tw/documentation/articles/mobile-services-ios-get-started-offline-data "iOS"
[Xamarin.iOS]: /zh-tw/documentation/articles/mobile-services-xamarin-ios-get-started-offline-data "Xamarin.iOS"
[Xamarin.Android]: /zh-tw/documentation/articles/mobile-services-xamarin-android-get-started-offline-data "Xamarin.Android"
[開始使用行動服務]: /zh-tw/documentation/articles/mobile-services-ios-get-started/
[開始使用資料]: /zh-tw/documentation/articles/mobile-services-ios-get-started-data/
[Azure 免費試用]: http://www.windowsazure.com/zh-tw/pricing/free-trial/?WT.mc_id=AE564AB28
[iOS 離線待辦事項清單]: https://github.com/Azure/mobile-services-samples/tree/master/TodoOffline/iOS/blog20140611
[同好部落格貼文]: http://aka.ms/iosoffline
