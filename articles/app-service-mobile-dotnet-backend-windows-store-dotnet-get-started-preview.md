<properties
	pageTitle="開始使用 Windows 市集應用程式的行動應用程式後端 | 行動開發人員中心"
	description="遵循此教學課程，可開始使用 Azure 行動應用程式後端進行 C#、VB 或 JavaScript 的 Windows 市集開發。"
	services="app-service\mobile"
	documentationCenter="windows"
	authors="christopheranderson"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="02/24/2015"
	ms.author="chrande"/>


# <a name="getting-started"> </a>建立 Windows 應用程式

[AZURE.INCLUDE [app-service-mobile-selector-get-started-preview](../includes/app-service-mobile-selector-get-started-preview.md)]

本教學課程顯示如何使用 Azure 行動應用程式後端，將雲端型後端服務新增到通用 Windows 應用程式。通用 Windows 應用程式解決方案包括 Windows 市集 8.1 和 Windows Phone 市集 8.1 應用程式的專案，以及一般共用專案。

[AZURE.INCLUDE [app-service-mobile-windows-universal-get-started-preview](../includes/app-service-mobile-windows-universal-get-started-preview.md)]

若要完成此教學課程，您需要下列項目：

* 使用中的 Azure 帳戶。如果您沒有帳戶，可以註冊 Azure 試用版並取得多達 10 個免費的行動應用程式，即使在試用期結束之後仍可繼續使用這些應用程式。如需詳細資訊，請參閱 [Azure 免費試用](http://azure.microsoft.com/pricing/free-trial/)。
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a>.

>[AZURE.NOTE] 如果您在註冊 Azure 帳戶之前想要開始使用 Azure 應用程式服務，請移至 [試用應用程式服務](http://go.microsoft.com/fwlink/?LinkId=523751&appServiceName=mobile)，在此您將可立即在應用程式服務中建立存留期較短的入門行動應用程式。不需要信用卡，無需承諾。

## <a name="create-new-service"> </a>建立新的行動應用程式後端

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service-preview](../includes/app-service-mobile-dotnet-backend-create-new-service-preview.md)]

## 建立新的通用 Windows 應用程式

在建立您的行動應用程式後端之後，您可以依照 [Azure 入口網站] 中的簡易快速入門，來建立新的應用程式或修改現有的應用程式，以連接到您的行動應用程式後端。

在本節中，您將建立與行動應用程式後端連接的新通用 Windows 應用程式。

1. 在 Azure 入口網站中，按一下 [**行動應用程式**]，然後按一下您剛剛建立的行動應用程式。

2. 在分頁頂端，按一下 [**新增用戶端**]，然後展開 **Windows (C#)**。

   ![Mobile App quickstart steps](./media/app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-preview/windows-quickstart.png)

   這將顯示三個簡單步驟，可用來建立連接到行動應用程式後端的 Windows 市集應用程式。

3. 在您的本機電腦或虛擬機器下載並安裝 <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a> (如果您尚未這麼做)。

4. 在 [**在本機下載及執行您的應用程式和服務**] 下，選取 Windows 市集應用程式的語言，然後按一下 [**下載**]。

   這會下載一個方案，其中包含行動應用程式後端的專案，以及與行動應用程式後端連接的範例_待辦事項清單_應用程式的專案。將此壓縮專案檔案儲存到您的本機電腦，並記錄儲存位置。

## 測試行動應用程式

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-test-local-service-preview](../includes/app-service-mobile-dotnet-backend-test-local-service-preview.md)]

## 發佈您的行動應用程式後端

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]

## 執行 Windows 應用程式

現在行動應用程式後端已發佈，且用戶端已連接至 Azure 代管的遠端行動應用程式後端，我們就可以使用 Azure 來執行應用程式，以儲存項目。

[AZURE.INCLUDE [app-service-mobile-windows-universal-test-app-preview](../includes/app-service-mobile-windows-universal-test-app-preview.md)]

<!-- Anchors. -->

[開始使用行動應用程式後端]:#getting-started
[建立新的行動應用程式後端]:#create-new-service
[定義行動應用程式後端執行個體]:#define-mobile-app-backend-instance
[後續步驟]:#next-steps

<!-- Images. -->



<!-- URLs. -->
[開始使用驗證]: app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-users-preview.md
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[行動應用程式 SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Azure 入口網站]: https://portal.azure.com/

<!--HONumber=49-->