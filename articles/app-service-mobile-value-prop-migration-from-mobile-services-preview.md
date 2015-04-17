<properties
	pageTitle="我使用行動服務，應用程式服務對我有何幫助？"
	description="了解應用程式服務可為您現有的行動服務專案帶來哪些優勢。"
	services="app-service\mobile"
	documentationCenter="ios"
	authors="kirillg"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-multiple"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="02/20/2015"
	ms.author="kirillg"/>

# <a name="getting-started"> </a>我使用行動服務，應用程式服務對我有何幫助？

## 概觀
您現有的行動服務是安全的，且將繼續受到支援。但是， *Azure App Service* 平台可為您的行動應用程式提供目前的行動服務所沒有的許多優點：  

- 為同時包含 Web 和行動用戶端的應用程式，提供更簡單、更易用且更符合成本效益的服務
- 新的主機功能包括 Web 工作、自訂 Cname 和更理想的監視功能
- 與 Office 365、Dynamics CRM、Salesforce 和其他重要的 SaaS API 密切整合。
- 除了 Node.js 和 .NET，還支援 Java 與 PHP 後端程式碼 
- 與流量管理員密切整合
- 除了混合式連線以外，還可使用 VNet 連接到您的內部部署資源和 VPN
- 可使用 NewRelic 或 AppInsights 和警示來監視您的應用程式並進行疑難排解
- 更豐富的基礎計算資源，例如 VM 大小 
- 內建自動調整、負載平衡和效能監視等功能。
- 內建預備、備份、復原和生產環境測試功能 

## 新的主控功能
在  *Azure App Service* 中， *Mobile App* 後端程式碼會在與 Web 應用程式和 API 應用程式相同的容器中執行。因此，您可以利用此容器中的所有功能，包括行動服務中目前沒有的某些功能： 

- 透過 Web 工作新增持續執行中的後端邏輯 
- 確定您的後端程式碼一律執行中
- 使用自訂 CName 為您的行動後端端點提供易記而穩定的名稱
- 使用流量管理員為應用程式進行地理調整


## 將您的 *Mobile App*連接到 SaaS API
*Azure 應用程式服務* 可讓您輕鬆地將行動應用程式連接到 SaaS API (包括 Office 365、Dynamics、Salesforce、SAP) 以及更多項目。 *Azure App Service*提供代表使用者的周全驗證，並可讓您跨所有 SaaS API 執行真正的單一登入；方法是將個別 SaaS API 的權杖與您的主要身分識別產生關聯。

## 使用 VNet 存取內部部署資料
透過現今的行動服務，您已可使用混合式連線來存取內部部署資源。但在某些情況下，VPN 解決方案會更為適用。透過  *Azure App Service*，您可以將 Azure VNet 用於行動應用程式後端程式碼。

## 使用您偏好的後端語言
*Azure 應用程式服務* 提供更廣泛和豐富的開發人員平台支援；除了行動服務中支援的 .NET 和 Node.js，還包括 Java、PHP 和 Python 。

## 設定自動調整
透過行動服務，後端程式碼的所有執行個體皆執行於小型 VM 上。 *Azure App Service* 可讓您從更豐富的選項集選取 VM 的大小。您也可以根據各種效能度量快速地向上或向外擴充，以處理內送的客戶負載。 

## 全盤掌控
透過監視及警示讓您和團隊成員自動獲得通知，以即時因應各種問題。整合 New Relic 和 AppInsights 中的進階應用程式分析和監視功能，以更深入地了解行動應用程式的執行情況。透過  *Azure App Service*，現在您可以用程式設計方式和入口網站，根據各種不同的效能度量設定警示

## 確保資產的安全性
自動備份您的後端和資料庫。您的程式碼和資料可抵禦嚴重損壞並輕易還原，讓您可安心執行您的業務。

## 準備、就緒，執行！
透過  *Azure App Service*，現在您可以為行動應用程式建立多個私人測試和預備環境。您可以在部署前使用這些環境執行測試。順暢移轉至生產環境，無須停止運作。Web 應用程式會預先載入，確保最理想的客戶使用經驗。



在  *Azure App Service Mobile App* 功能正式上市前，如果您選擇移轉，我們將讓您以順暢的使用性從現有的行動服務移轉至應用程式服務。同時，您可以開始探索  *App Service*，並將  *App Service* 運用在您現有的行動服務上，方法如下 [tutorial](app-service-mobile-dotnet-backend-migrating-from-mobile-services-preview.md).



<!--HONumber=49-->