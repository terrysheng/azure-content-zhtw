<properties
    pageTitle="在 Azure App Service Mobile Apps 上建立 Cordova 應用程式 | Microsoft Azure"
    description="請依照此教學課程中的說明，開始使用 Azure 行動應用程式後端來進行 Apache Cordova 開發"
    services="app-service\mobile"
    documentationCenter="javascript"
    authors="adrianhall"
    manager="glenga"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="na"
    ms.tgt_pltfrm="mobile-javascript"
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


<!-- Images. -->

<!-- URLs -->
[Azure portal]: https://portal.azure.com/
[Visual Studio Community 2015]: http://www.visualstudio.com/
[Visual Studio Tools for Apache Cordova]: https://www.visualstudio.com/zh-TW/features/cordova-vs.aspx

<!---HONumber=AcomDC_0211_2016-->