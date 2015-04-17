<properties 
	pageTitle="什麼是 API 應用程式？" 
	description="了解 Azure 應用程式服務為何是開發、發佈及裝載符合 REST 限制之 API 的最佳平台。" 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/06/2015" 
	ms.author="tdykstra"/>

# 什麼是 API 應用程式？

## 概觀

Azure 應用程式服務是完全受管理的運算平台，可為專業開發人員帶來一組豐富的 Web、 行動和整合案例功能。API 應用程式是應用程式服務套件的一部分，可讓任何技術使用者或開發人員在現代化、功能豐富、可擴充且全球通用的雲端平台上探索、託管、管理及銷售 API 和 SaaS 連接器。

## 為何採用 API 應用程式？

Azure 應用程式服務中的 API 應用程式可供輕鬆開發、發佈、管理及銷售 API。

- **以現況攜帶 API** - 將 ASP.NET、Java、PHP、Node.js 或 Python 使用於您的 API。您的 API 可以利用 Azure 應用程式服務的各項功能，而不需做任何變更。

- **將您的應用程式連接至熱門的 SaaS 平台** - Azure 應用程式服務可讓您輕鬆地連線至熱門的 SaaS 平台，包括 Salesforce、Office 365、Twitter、Facebook、Dropbox 等等。

- **簡單的存取控制** - API 可以僅對 Azure 應用程式服務中的其他應用程式公開，或完全公開，而且您可以加入 Azure Active Directory 或協力廠商服務的驗證，而不必變更您的程式碼。

- **API 的公開與組織組件庫** - 使用您自己的 API 私人組織組件庫，輕鬆地與您組織中的其他小組共用 API。API 也可以公開共用，以供協力廠商開發人員使用。

- **自動 SDK 產生** - Azure 應用程式服務可以自動建置適用於各種語言 (包括 C#、Java 和 Javascript) 的 SDK，讓您的 API 可供許多平台使用。

- **使用市面上最佳的 IDE** - [Visual Studio](/campaigns/visual-studio-2013/) 整合簡化 API 建立、偵錯、封裝和發佈，並可進行完整生命週期應用程式管理。

<!--依照只是移除，以縮減此區段的大小
- **沒有 ops** - 在具有自動修補的高可用性環境中執行您的 API 應用程式。與 Azure 應用程式服務一起部署的 API 應用程式會被隔離並裝載在您的應用程式專用的 VM 中 - 確保可預測的效能和安全性隔離。

- **自動調整** - Azure 應用程式服務可讓您快速地向上或向外延展，以處理任何內送的客戶載入。手動選取 VM 的數目和大小，或設定
[自動調整](/documentation/videos/auto-scaling-azure-web-sites/)
以根據負載或排程調整您的伺服器。
-->

- **存取內部部署資料** - API 應用程式可使用[混合式連線](integration-hybrid-connection-overview.md)和 [VNET](web-sites-integrate-with-vnet.md)，從您自己的資料中心取用資料。

- **友善的部署** - 使用 Visual Studio Online、GitHub、TeamCity、Hudson 或 BitBucket 設定連續整合和部署工作流程 - 讓您在每次成功的程式碼簽入或整合測試中自動建置、測試及部署 API 應用程式。

## API 應用程式概念 ##

- **API 應用程式組件庫** - 從不斷成長的現有 API 應用程式範本清單中選取。
- **連接器** - 一種可讓您輕鬆地連接到 SaaS 平台 (例如 Salesforce 和 Office 365) 的 API 應用程式 。
- **閘道** - 一個可處理 API 管理功能 (例如驗證資源群組中的所有 API 應用程式) 的 Web 應用程式。 
- **自動調整** - API 應用程式可以自動向上或向快延展，以處理任何內送的客戶載入。手動選取 VM 的數目和大小，或設定自動調整以根據負載或排程來調整您的伺服器。
- **連續整合** - 使用 VSO、GitHub、TeamCity、Hudson 或 BitBucket 設定連續整合和部署工作流程 - 讓您在每次成功的程式碼簽入或整合測試中自動建置、測試及部署 Web 應用程式。

## 開始使用

若要開始使用 API 應用程式，請遵循[建立 API 應用程式教學課程](app-service-dotnet-create-api-app.md)。

如需有關 Azure 應用程式服務平台的詳細資訊，請參閱 [Azure 應用程式服務](app-service-value-prop-what-is.md)。

<!--HONumber=49-->