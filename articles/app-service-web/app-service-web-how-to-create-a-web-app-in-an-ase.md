<properties
	pageTitle="如何在 App Service 環境中建立 Web 應用程式"
	description="針對 App Service 環境檢查之 Web 應用程式和 App Service 方案的建立流程"
	services="app-service"
	documentationCenter=""
	authors="ccompy"
	manager="stefsch"
	editor=""/>

<tags
	ms.service="app-service"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article" 
	ms.date="09/15/2015"
	ms.author="ccompy"/>

# 如何在 App Service 環境中建立 Web 應用程式 #

在 App Service 環境 (ASE) 中建立 Web 應用程式幾乎與往常一樣。如果您不熟悉 App Service 環境功能，請閱讀文件：[什麼是 App Service 環境](app-service-app-service-environment-intro.md)。

若要在 ASE 中建立 Web 應用程式，您必須先從擁有 ASE 開始。如需有關建立 ASE 的詳細資訊，請閱讀此處的文件：[如何建立 App Service 環境](app-service-web-how-to-create-an-app-service-environment.md)。

建立 Web 應用程式的第一個步驟就是選取您的訂用帳戶。如果您有多個訂用帳戶，請注意，若要在您的 ASE 中建立應用程式，則需要使用您在建立 ASE 時使用的同一個訂用帳戶。下一個步驟包括選取或建立資源群組。如果您不熟悉資源群組，這裡有詳細資訊：[管理您的 Azure 資源][ResourceGroups]。除了協助管理您的資源外，為您的應用程式建立 RBAC 規則時，資源群組也是非常重要。

選取了您的訂用帳戶和資源群組之後，您需要建立或選取 App Service 方案 (ASP)。如果需要在 ASE 中建立新的 ASP，則您必須提供 ASP 的名稱、從 [位置] 中選取您想要的 ASE，然後選取您想要 ASP 位於的背景工作角色集區。下面有更詳細的描述。如果您在 ASE 中選取 ASP，則在正常情況下，應用程式建立流程與建立應用程式相同。這將開始使用 [新增]-> [Web + 行動]-> [Web 應用程式]，來執行 Web 應用程式建立流程。

![][1]


如果您是使用已在 App Service 環境中建立的 App Service 方案，則只需選取該方案、輸入 Web 應用程式的名稱，然後選取 [建立]。此流程與您往常建立 Web 應用程式時一樣。您可以藉由查看 ASP 名稱下加註的位置，識別 ASE 中的 ASP。

![][5]

當您建立應用程式時，它將位於：

[*站台名稱*].[*App Service 環境的名稱*].p.azurewebsites.net

而不是

[*站台名稱*].azurewebsites.net

現在，您的 Web 應用程式名稱必須是整個 Azure App Service 中的唯一名稱。這表示如果您要建立名為 "thisismywebapp" 的 Web 應用程式，則目前在 Azure App Service 中不能有任何其他名為 "thisismywebapp" 的 Web 應用程式。

### App Service 方案 ###

App Service 方案是一組受管理的 Web 應用程式。當您選取價格時，支付的價格會套用到 App Service 方案，而非個別的應用程式。若要相應增加 Web 應用程式的執行個體數目，您可相應增加 ASP 的執行個體，這會影響該方案中的所有 Web 應用程式。方案中的某些功能 (例如網站位置或 VNET 整合) 也有數量限制。您可以進一步從以下文件了解 App Service 方案： [了解 Azure App Service 方案](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)

如果您正在制定新的 Azure App Service 方案，這與在 Azure App Service 環境中建立 ASP 有一些差異。此外，您的背景工作角色選擇不同，因為 ASE 中沒有共用的背景工作角色作。您必須使用的背景工作角色也就是由系統管理員配置給 ASE 的背景工作角色。這表示，若要建立新的 ASP，配置給 ASE 背景工作角色集區的背景工作角色數目必須超過已在該背景工作角色中之所有 ASP 的執行個體總數。如果您的 ASE 背景工作角色集區中沒有足夠的背景工作角色來建立 ASP，則需要與 ASE 系統管理員合作，一起新增背景工作角色。

與 App Service 環境託管 APS 的另一個差異就是缺少價格選取項目。當您有 App Service 環境時，您會支付系統所使用的計算資源費用，但該環境中的 ASP 不會有附加的費用。 當您建立 ASP 時，您通常會選取用以決定費率的價格方案。。App Service 環境基本上是您可以在其中建立內容的私人位置。您可支付環境費用，而非裝載您的內容。

### 建立 App Service 環境的 ASP ###

若要在 ASE 中建立 ASP，作法為在 ASP 選取 UI 中選取 [新建]，並提供 ASP 的名稱，正如同您在 ASE 外所做的一般。下一個步驟是從位置選擇器中選取您想要使用的 ASE。因為 App Service 環境基本上是專用部署位置，所以它會顯示在 [位置] 下。

![][2]

在位置選擇器中選取 ASE 之後，ASP 建立 UI 將會更新。位置現在將顯示 ASE 系統的位置及其所在區域，而價格方案選擇器會取代為背景工作角色集區選擇器。

![][3]

### 選取您的背景工作集區 ###

在 Azure App Service 之中和 App Service 環境之外，選取專用價格方案通常可提供 3 個運算大小。同樣地，擁有 ASE 的客戶最多可以定義 3 個背景工作角色集區，並指定用於該背景工作角色集區的運算大小。對租用戶來說，這表示不是選取具有 ASP 運算大小的價格方案，而是選取所謂的背景工作角色集區。

背景工作角色集區選取 UI 會在名稱下方顯示該背景工作集區角色使用的運算大小。可用數量是指有多少運算執行個體可使用於該集區。總計集區實際上可能有超過這個數字的執行個體，但這個值只是指未使用的數量。如果您需要調整 App Service 環境以加入更多計算資源，請參閱以下的文件：[設定 App Service 環境](app-service-web-configure-an-app-service-environment.md)。

![][4]

在此範例中，您只可以看到兩個可用的背景工作集區。這是因為 ASE 系統管理員只會將主機配置到這兩個背景工作角色集區中。第三個集區會在有 VM 配置到其中時出現。

### 建立 Web 應用程式之後 ###

在 ASE 中執行 Web 應用程式及管理 ASP 時，有幾件事需要列入考量。

如先前所述，ASE 的擁有者負責系統大小，因此他們也會負責確保有足夠的容量來裝載所需的 ASP。如果沒有可用的背景工作，您將無法建立您的 ASP。也就無法相應增加您的 Web 應用程式。如果您需要更多執行個體，您必須讓您的 App Service 環境的系統管理員新增更多的背景工作。

建立 Web 應用程式和 ASP 之後，最好能相應增加其數量。 在 ASE 中，一律需要至少 2 個 ASP 執行個體，才能為您的應用程式提供容錯功能。在 ASE 中調整 ASP 同樣是透過 ASP UI 進行。如需調整的詳細資訊，請參閱文件：[如何在 App Service 環境中調整 Web 應用程式](app-service-web-scale-a-web-app-in-an-app-service-environment.md)


[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspnewwebapp.png
[2]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createasplocation.png
[3]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspselected.png
[4]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspworkerpool.png
[5]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/selectaspinase.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[Appserviceplans]: http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[HowtoCreateASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[HowtoScale]: http://azure.microsoft.com/documentation/articles/app-service-web-scale-a-web-app-in-an-app-service-environment
[HowtoConfigureASE]: http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment
[ResourceGroups]: http://azure.microsoft.com/documentation/articles/resource-group-portal/
[AzurePowershell]: http://azure.microsoft.com/documentation/articles/powershell-install-configure/

<!---HONumber=Oct15_HO4-->