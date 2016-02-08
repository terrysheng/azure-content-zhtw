<properties 
	pageTitle="如何在 App Service 環境中調整應用程式" 
	description="在 App Service 環境中調整應用程式" 
	services="app-service" 
	documentationCenter="" 
	authors="ccompy" 
	manager="stefsch" 
	editor="jimbe"/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/14/2016" 
	ms.author="ccompy"/>

# 在 App Service 環境中調整應用程式 #

基本上，App Service 環境是 Azure App Service 在 VNET 中的個人部署，僅可由您的訂用帳戶管理。這類環境會提供新的網路功能，因為它們位於您的 VNET 中，而且還可以調整超過 Azure App Service 環境中通常可用的數量。如果您需要更多有關何謂 App Service 環境 (ASE) 的資訊，請查看[什麼是 App Service 環境][WhatisASE]。如需有關建立 App Service 環境或在 App Service 環境中建立 Web 應用程式的詳細資訊，請參閱[如何建立 App Service 環境][HowtoCreateASE]和[如何在 App Service 環境中建立 Web 應用程式][CreateWebappinASE]

提醒您，若要在 Azure App Service 中正常變更 Web、行動或 API 應用程式的調整屬性時，需在 App Service 方案 (ASP) 層級進行變更。如需調整 App Service 方案的詳細資訊，或只需 App Service 環境外部 App Service 方案的詳細資訊，請參閱[在 Azure App Service 中調整 Web 應用程式][ScaleWebapp]和 [App Service 方案深度概觀][Appserviceplans]。

在 App Service 環境中調整應用程式與一般調整應用程式的方式非常類似。在 Azure App Service 中，您通常有三件事可以調整：

- 定價方案
- 背景工作大小 (適用於專用的執行個體)
- 執行個體數目。

在 ASE 中不需要選取或變更定價方案。就功能而言，它已經是 Premium 定價功能層級。在 App Service 環境中也沒有任何共用的背景工作。全部都是專用的背景工作。

關於背景工作角色大小，ASE 系統管理員可以指派要用於每個背景工作角色集區的計算資源大小。這表示您可以有具 P4 計算資源的背景工作集區 1，以及具 P1 計算資源的背景工作集區 2 (如有需要的話)。它們並沒有大小順序。如需大小及其定價的詳細資訊，請參閱 [Azure App Service 定價][AppServicePricing]文件。以下是 App Service 環境中的 Web 應用程式和 App Service 方案的調整選項：

- 背景工作集區選取
- 執行個體數目

如需變更任一項目，都可以透過針對 ASE 託管之 App Service 方案所顯示的適用 UI 完成。您無法將您的 ASP 相應增加超過 ASP 所在背景工作角色集區中可用的計算資源數量。如果您需要更多數量，則必須讓您的 ASE 系統管理員新增更多運算資源到您指定的背景工作集區。如需重新設定 ASE 的相關資訊，請閱讀以下資訊：[如何設定 App Service 環境][HowtoConfigureASE]。您也可能需利用 ASE 自動調整功能，以根據排程或計量增加容量。若要取得設定 ASE 環境本身自動調整的相關詳細資訊，請參閱[如何設定 App Service 環境的自動調整][ASEAutoscale]。

![][1]

### 調整執行個體數目 ###

當您在 App Service 環境中首次建立 Web 應用程式時，您應該相應增加為至少 2 個執行個體，才能提供容錯功能。

如果 ASE 有足夠的容量，這就很簡單。您可移至具有您要相應增加之站台的 App Service 方案，然後選取 [調整]。這會開啟 UI，您可以在當中為 ASP 手動設定調整或設定自動調整規則。若要手動調整應用程式，只需將 [***調整依據***] 設為 [***手動輸入的執行個體計數***]。從這裡拖曳滑桿至所需的數量，或將所需數量輸入滑桿旁邊的方塊。

![][2]

在 ASE 中的 ASP 自動調整規則與一般運作方式相同。您可以選取 [***調整依據***] 下方的 [***CPU 百分比***]，根據 CPU 百分比建立 ASP 的自動調整規則，或使用 [***排程和效能規則***] 建立更複雜的規則。若要查看有關設定自動調整更完整的詳細資訊，請參閱本指南的[在 Azure App Service 中調整 Web 應用程式規模][AppScale]。


### 背景工作集區選取 ###

如前所述，背景工作角色集區選項需透過 ASP UI 存取。開啟您想要調整的 ASP 刀鋒視窗，選取 [背景工作集區]。您會看到您已在 App Service 環境中設定的所有背景工作集區。如果您只有一個背景工作集區，您只會看到一個集區列出。若要變更 ASP 所在的背景工作角色集區，您只需選取 App Service 方案所要移入的背景工作角色集區。

![][3]

將 ASP 從一個背景工作角色集區移到另一個集區之前，請務必確定您有足夠的容量可容納該 ASP。在背景工作集區清單中，不只會列出背景工作集區名稱，您也可以查看該背景工作集區中有多少可用的背景工作。請確定有足夠的執行個體可包含您的 App Service 方案。如果您需要在背景工作集區中移入更多計算資雲，則必須讓您的 ASE 系統管理員加以新增。

> [AZURE.NOTE] 從一個背景工作角色集區移出 ASP，會導致該 ASP 中的應用程式重新啟動。這會造成應用程式短暫停機 (實際時間長短視重新啟動應用程式所花費的時間而定)。

## 開始使用

若要開始使用 App Service 環境，請參閱[如何建立 App Service 環境][HowtoCreateASE]

如需有關 Azure App Service 平台的詳細資訊，請參閱 [Azure App Service][AzureAppService]。

<!--Image references-->
[1]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-aspblade.png
[2]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-manualscale.png
[3]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-sizescale.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[ScaleWebapp]: http://azure.microsoft.com/documentation/articles/web-sites-scale/
[HowtoCreateASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[HowtoConfigureASE]: http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment/
[CreateWebappinASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-a-web-app-in-an-ase/
[Appserviceplans]: http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[ASEAutoscale]: http://azure.microsoft.com/documentation/articles/app-service-environment-auto-scale/
[AppScale]: http://azure.microsoft.com/documentation/articles/web-sites-scale/

<!---HONumber=AcomDC_0128_2016-->