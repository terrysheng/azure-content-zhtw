<properties 
	pageTitle="如何建立 App Service 環境" 
	description="App Service 的建立流程說明" 
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
	ms.date="01/14/2016" 
	ms.author="ccompy"/>

# 如何建立 App Service 環境 #

App Service 環境 (ASE) 是 Azure App Service 的進階服務選項，可提供多租用戶戳記中不提供的增強式設定功能。ASE 功能基本上會將 Azure App Service 部署到客戶的虛擬網路中。若要更深入了解 App Service 環境所提供的功能，請閱讀[什麼是 App Service 環境][WhatisASE]文件。

### 概觀 ###

要建立 ASE，客戶必須提供下列幾項資訊：

- ASE 的名稱
- 要用於 ASE 的訂用帳戶  
- 資源群組
- Azure 虛擬網路 (VNET) 選取項目和子網路
- ASE 資源集區定義

這些項目各有其重要的詳細資料。 - ASE 的名稱會用在該 ASE 中所建立的任何應用程式的子網域中 - 所有在 ASE 中建立的應用程式都屬於與 ASE 本身相同的訂用帳戶下 - 如果您無法存取用來建立 ASE 的訂用帳戶，則無法使用 ASE 建立應用程式 - 用來裝載 ASE 的 VNET 必須是區域的傳統 "v1" VNET - 用來裝載 ASE 的子網路不可包含任何其他計算資源 - 一個子網路中只能有一個 ASE 存在

每個 ASE 部署都是 Azure 管理和維護的託管服務。雖然客戶會管理執行個體的數量和其大小，但不能存取裝載 ASE 系統角色的計算資源。

存取 ASE 建立 UI 的方法有兩種。在 Azure Marketplace 中搜尋 ***App Service 環境***或經由 [新增] -> [Web + 行動]，即可找到。

如果您想讓 VNET 有不同於 ASE 的資源群組，您必須先個別建立 VNET，然後在 ASE 建立期間加以選取。此外，如果您想要於 ASE 建立期間在現有的 VNET 中建立子網路，則 ASE 必須屬於與 VNET 相同的資源群組。

### 快速建立 ###
在建立 ASE 時，可以利用一組預設值來進行快速建立。您只要輸入部署的名稱，即可快速建立 ASE。接著，將會透過下列項目在最接近您的區域中建立 ASE：

- 具有 512 個位址的 VNET 
- 具有 256 個位址的子網路
- 具有 2 個 P2 計算資源的前端集區
- 具有 2 個 P1 計算資源的背景工作集區
- 要用於 IP SSL 的單一 IP 位址

前端集區需要 P2 或更大的大小。請小心選取您想讓 ASE 屬於哪個訂用帳戶。唯一可使用 ASE 來裝載內容的帳戶必須屬於用來建立帳戶的訂用帳戶。

![][1]

針對 ASE 指定的名稱將用於在 ASE 中建立的應用程式。如果 ASE 的名稱是 appsvcenvdemo，則網域名稱會是 .*appsvcenvdemo.p.azurewebsites.net*。如果您因此建立名為 *mytestapp* 的應用程式，則可定址於 *mytestapp.appsvcenvdemo.p.azurewebsites.net*。您無法在 ASE 的名稱中使用空白字元。如果您在名稱中使用大寫字元，則網域名稱會是該名稱的全小寫版本。

在某些情況下使用預設值會有很好的效果，但通常您都必須進行調整。後續幾節將一一說明 ASE 的相關組態區段。

### 虛擬網路 ###
雖然快速建立功能可自動建立新的 VNET，但這項功能還支援選取現有的 VNET 和手動建立 VNET。如果現有的 VNET 夠大，您可加以選取 (在這時候，只有傳統 "v1" 虛擬網路受支援)，以支援 App Service 環境部署。VNET 必須有 8 個或更多位址。

如果您選取預先存在的 VNET，您也必須指定要使用的子網路或建立新的子網路。子網路必須有 8 個或更多位址，且不可有任何其他資源已包含於其中。如果您嘗試使用已配置 VM 的子網路，ASE 建立將會失敗。

如果要經由 VNET 建立 UI，您必須提供：

- VNET 名稱
- CIDR 表示法中的 VNET 位址範圍
- 位置

VNET 的位置就是 ASE 的位置，因為 ASE 部署至該 VNET 中。

在您指定或選取 VNET 後，您必須建立或選取適當的子網路。您必須在此處提供的詳細資料包括：- 子網路名稱 - CIDR 表示法中的子網路範圍

如果您不熟悉 CIDR (無類別網域間路由) 表示法，請留意它會採用以正斜線與 CIDR 值分隔的 IP 位址格式。其顯示如下：*10.0.0.0/22*。CIDR 值表示對顯示的 IP 位址進行遮罩處理的前導位元數。如果要以更簡單的方式表達此概念，我們可以說 CIDR 值提供了 IP 範圍。在此範例中，10.0.0.0/22 表示 1024 個位址，或從 10.0.0.0 到 10.0.3.255 的範圍。/23 表示 512 個位址等等。

提醒您，如果您想要在現有的 VNET 中建立子網路，ASE 將會屬於與 VNET 相同的資源群組。若要讓您的 ASE 與 VNET 分屬於不同的資源群組，只要在建立 ASE 之前個別建立 VNET 和子網路即可。

![][2]


### 計算資源集區 ###

在 ASE 建立期間，您可以設定每個資源集區的資源數量及其大小。您可以在 ASE 建立時設定資源集區的大小，也可以後續再使用手動調整選項或自動調整選項加以調整。

如前所述，ASE 是由前端伺服器和背景工作所組成。前端伺服器可處理應用程式連線負載和執行應用程式程式碼的背景工作。前端伺服器是在專用的計算資源集區中進行管理。而背景工作則是在以下 3 個不同的計算資源集區中進行管理：

- 背景工作集區 1
- 背景工作集區 2
- 背景工作集區 3

如果您有針對簡單 Web 應用程式的大量要求，您便可能相應增加前端，而有較少的背景工作。如果您有需要大量 CPU 或記憶體但流量不高的 Web 應用程式，則您不需要許多前端，但可能需要更多或更大的背景工作。

![][3]

不論計算資源的大小為何，最少的使用量會具有 2 個前端伺服器和 2 個背景工作。一個 ASE 可設定為最多總計使用 55 個計算資源。在這 55 個計算資源中，只有 50 個可用來裝載工作負載。其原因有兩個。前端計算資源的下限為 2 個。上限可高達 53 個，用以支援背景工作集區配置。為了提供容錯功能，您需要根據下列規則配置額外的計算資源：

- 每個背景工作集區至少需要一個無法指派工作負載的額外計算資源
- 當集區中的計算資源數量超出特定值時，則需要另一個計算資源

在任何單一背景工作集區中，容錯需求就是指派給背景工作集區的 X 個資源的指定值：

- 如果 X 介於 2 到 20，您可用於工作負載的可用計算資源數量為 X-1
- 如果 X 介於 21 到 40，您可用於工作負載的可用計算資源數量為 X-2
- 如果 X 介於 41 到 53，您可用於工作負載的可用計算資源數量為 X-3

除了能夠管理您可指派給指定集區的計算資源數量以外，您也可以控制大小。在 App Service 環境中，您可以選擇 4 個標示為 P1 至 P4 的不同大小。如需大小及其價格的詳細資訊，請參閱 [App Service 價格][AppServicePricing]。P1 至 P3 計算資源大小與多租用戶環境中可用的大小相同。P4 計算資源提供 14 GB RAM 的八核心， 而且只適用於 App Service 環境。

App Service 環境的定價是根據指派的計算資源。無論是否裝載工作負載，您都需支付配置給您的 App Service 環境的計算資源。

根據預設，ASE 隨附 1 個可供 IP SSL 使用的 IP 位址。如果您知道您將需要更多數量，您可以在此進行指定或在建立後加以管理。
  
### 在 App Service 環境建立之後 ###

建立 ASE 之後，您可以調整：

- 前端的數量 (最小值：2)
- 背景工作的數量 (最小值：2)
- IP SSL 可用的 IP 位址數目
- 前端或背景工作所使用的計算資源大小 (前端大小下限為 P2)

您無法變更：

- 位置
- 訂閱
- 資源群組
- 使用的 VNET
- 使用的子網路

以下有更多關於手動調整、管理及監視 App Service 環境的詳細資料：[如何設定 App Service 環境][ASEConfig]

如需自動調整的相關資訊，請參閱：[如何設定 App Service 環境的自動調整][ASEAutoscale]

有其他無法自訂的相依性，例如資料庫和儲存體。這些都是由 Azure 處理並由系統隨附。系統儲存體對於整個 App Service 環境最多可支援 500 GB，且 Azure 會根據系統規模的需要來調整資料庫。


## 開始使用

若要開始使用 App Service 環境，請參閱 [App Service 環境簡介][WhatisASE]

如需有關 Azure App Service 平台的詳細資訊，請參閱 [Azure App Service][AzureAppService]。

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]
 

<!--Image references-->
[1]: ./media/app-service-web-how-to-create-an-app-service-environment/asecreate-basecreateblade.png
[2]: ./media/app-service-web-how-to-create-an-app-service-environment/asecreate-vnetcreation.png
[3]: ./media/app-service-web-how-to-create-an-app-service-environment/asecreate-resources.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[ASEConfig]: http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[ASEAutoscale]: http://azure.microsoft.com/documentation/articles/app-service-environment-auto-scale/

<!---HONumber=AcomDC_0128_2016-->