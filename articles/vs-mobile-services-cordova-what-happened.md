<properties title="開始使用行動服務" pageTitle="" metaKeywords="Azure, Getting Started, Mobile Services" description="" services="mobile-services" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="mobile-services" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb"></tags>

> [AZURE.SELECTOR]
>
> -   [開始使用][開始使用]
> -   [發生什麼情形][發生什麼情形]

### <span id="whathappened">我的專案發生什麼情形？</span>

##### 加入參考

所有多重裝置混合應用程式隨附的 Azure 行動服務用戶端外掛程式已啟用。

##### 行動服務的連接字串值

在 `services\mobileServices\settings` 下，已產生一個含有 **MobileServiceClient** 的新的 JavaScript (.js) 檔案，內含所選行動服務的應用程式 URL 和應用程式金鑰。此檔案包含行動服務用戶端物件的初始化，類似於下列程式碼。

    var mobileServiceClient;
    document.addEventListener("deviceready", function() {
            mobileServiceClient = new WindowsAzure.MobileServiceClient(
            "<your mobile service name>.azure-mobile.net",
            "<insert your key>"
        );

[深入了解行動服務][深入了解行動服務]

  [開始使用]: /documentation/articles/vs-mobile-services-cordova-getting-started/
  [發生什麼情形]: /documentation/articles/vs-mobile-services-cordova-what-happened/
  [深入了解行動服務]: http://azure.microsoft.com/documentation/services/mobile-services/
