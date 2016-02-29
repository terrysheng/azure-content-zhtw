<properties
    pageTitle="在 Azure App Service Mobile Apps 上建立 Cordova 應用程式 | Microsoft Azure"
    description="請依照此教學課程中的說明，開始使用 Azure 行動應用程式後端來進行 Apache Cordova 開發"
    services="app-service\mobile"
    documentationCenter="javascript"
    authors="adrianhall"
    manager="ggailey777"
    editor=""
    tags=""
    keywords="cordova, javascript, mobile, 用戶端" />

<tags
    ms.service="app-service-mobile"
    ms.workload="na"
    ms.tgt_pltfrm="mobile-html"
    ms.devlang="javascript"
    ms.topic="article"
    ms.date="02/09/2016"
    ms.author="adrianha"/>

#建立 Apache Cordova 應用程式

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## 概觀

本教學課程示範如何使用 Azure 行動應用程式後端，將雲端式後端服務新增到 Apache Cordova 行動應用程式。您將建立一個新的行動應用程式後端，以及一個將應用程式資料儲存在 Azure 中的簡易「待辦事項清單」Apache Cordova 應用程式。

所有其他 Apache Cordova 教學課程只要是與使用 Azure App Service 中的 Mobile Apps 功能相關，都必須先完成本教學課程。

## 先決條件

若要完成此教學課程，您需要下列項目：

* 具有 [Visual Studio Community 2015] 或更新版本的電腦。
* [Visual Studio Tools for Apache Cordova]。
* 有效的 [Azure 帳戶](https://azure.microsoft.com/pricing/free-trial/)。

您也可以略過 Visual Studio 而直接使用 Apache Cordova 命令列。在 Mac 電腦上完成本教學課程時，這會相當有用。本教學課程未涵蓋使用命令列來編譯 Apache Cordova 用戶端應用程式。

## 建立新的 Azure 行動應用程式後端

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## 設定伺服器專案

[AZURE.INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## 下載並執行 Apache Cordova 應用程式

[AZURE.INCLUDE [app-service-mobile-cordova-run-app](../../includes/app-service-mobile-cordova-run-app.md)]

## 後續步驟

您現在已經完成本快速入門教學課程，請繼續進行下列其中一個教學課程：

* [新增驗證]至您的 Apache Cordova 應用程式。
* [新增推播通知]至您的 Apache Cordova 應用程式。

深入了解使用 Azure App Service 的相關重要概念。

* [驗證]
* [推播通知]

了解如何使用 SDK。

* [ASP.NET Server SDK]
* [Node.js Server SDK]

<!-- Images. -->

<!-- URLs -->
[Azure portal]: https://portal.azure.com/
[Visual Studio Community 2015]: http://www.visualstudio.com/
[Visual Studio Tools for Apache Cordova]: https://www.visualstudio.com/zh-TW/features/cordova-vs.aspx
[新增驗證]: app-service-mobile-cordova-get-started-users.md
[新增推播通知]: app-service-mobile-cordova-get-started-push.md
[驗證]: app-service-mobile-auth.md
[推播通知]: ../notification-hubs/notification-hubs-overview.md
[ASP.NET Server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Node.js Server SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md

<!---HONumber=AcomDC_0218_2016-->