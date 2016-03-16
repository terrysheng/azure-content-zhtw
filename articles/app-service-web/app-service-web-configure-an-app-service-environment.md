<properties 
	pageTitle="如何設定 App Service 環境" 
	description="設定、管理和監視 App Service環境" 
	services="app-service" 
	documentationCenter="" 
	authors="ccompy" 
	manager="stefsch" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/04/2016" 
	ms.author="ccompy"/>


# 設定 App Service 環境 #

## 概觀 ##

App Service 環境是 Azure App Service 中的高階層功能，可提供新的延展性和網路存取功能。這項新的調整功能可讓您將 Azure App Service 的執行個體放入 VNET 中。這項功能可以裝載 Web Apps、Web App 和 API Apps。Logic Apps 尚未在 ASE 中執行。

如果您不熟悉 App Service 環境 (ASE) 功能，請閱讀文件：[什麼是 App Service 環境](app-service-app-service-environment-intro.md)。如需有關如何建立 ASE 的資訊，請讀取文件：[如何建立 App Service 環境](app-service-web-how-to-create-an-app-service-environment.md)。

App Service 環境是由數個主要元件所組成：

- 在 Azure App 環境託管服務中執行的計算資源
- 儲存體
- 資料庫
- 傳統 "v1" 虛擬網路具有至少一個子網路
- 在其中執行 Azure App 環境託管服務的子網路

計算資源會用於您的 4 個資源集區。每個 App Service 環境都有一組前端和 3 個背景工作集區。您不需要 3 個背景工作角色集區都使用，如果您想要，可以只使用其中一個。前端是您放置在 ASE 中的應用程式的 HTTP 端點。背景工作是您的應用程式實際執行之處。何時需要新增更多前端或更多背景工作，牽涉到您放在 ASE 中的應用程式以何種方式執行。例如，假設您在 ASE 上只有一個應用程式，而且是會有大量要求的 hello world 應用程式。在此情況下，您將需要相應增加前端來處理 HTTP 負載，但相對地並不需要相應增加背景工作。以手動方式處理這一切是相當令人怯步的，特別是在考量到每個 ASE 很可能有效能準則互異的不同應用程式執行於其中時。幸而，我們已在 App Service 環境中新增了自動調整功能，這會讓我們的工作輕鬆許多。如需 App Service 環境的調整和自動調整的詳細資訊，請使用以下連結：[如何在 App Service 環境中設定自動調整][ASEAutoscale]

每個 ASE 各設有 500 GB 的儲存體。此空間會用於 ASE 中的所有應用程式。此儲存空間屬於 ASE 的一部分，且目前無法切換為使用客戶的儲存空間。

資料庫會保存定義環境及詳細說明其中執行之應用程式的資訊。這同樣也屬於 Azure 訂用帳戶的一部分，且客戶無法直接加以操作。

與 ASE 搭配使用的虛擬網路，可以是您在建立 ASE 時所設定的虛擬網路，或是您既有的虛擬網路。如果您想要讓 VNET 所屬的資源群組不同於用在 ASE 的資源群組，您必須在 ASE 建立流程以外個別設定 VNET。同時建立您想要使用的子網路是個不錯的主意，因為在 ASE 建立期間建立子網路，會強制 ASE 進入與 VNET 相同的資源群組。目前僅支援 V1「傳統」VNET。

用來管理和監視 App Service 環境的 UI，可從 Azure 入口網站使用。如果您有 ASE，則可能會在資訊看板上看到 App Service 符號。這個符號用來代表 Azure 入口網站中的 App Service 環境。

![][1]

您可以使用圖示，或選取資訊看板底部的＞形箭號 (大於符號)，然後選取 App Service 環境。兩種方式都可開啟 UI，列出您所有的 App Service 環境。選取其中一個列出的 ASE，會開啟用來監視及管理 ASE 的 UI。

![][2]

這是第一個刀鋒視窗，顯示 ASE 的某些屬性和每個資源集區的計量圖表。某些顯示於 [基本功能] 區塊中的屬性同時也是超連結，將會開啟與其相關聯的刀鋒視窗。例如，您可以選取 [VNET 名稱]，以開啟與您的 ASE 執行所在的 VNET 相關聯的 UI。App Service 方案和應用程式分別會開啟不同的刀鋒視窗，列出位於您 ASE 中的項目。

## 監控 ##

圖表可讓您檢視每個資源集區中各種不同的效能計量。對於前端集區，監視平均 CPU 和記憶體是很合理的。前端具有分散式負載，您只要查看平均值，就可以清楚了解一般效能。但背景工作角色集區則不同。多個 App Service 方案可能會使用同一個背景工作角色集區中的背景工作。如果是這樣，CPU 和記憶體使用量將無法提供什麼實用資訊。更重要的是要追蹤您已使用和可用的背景工作角色數目，特別是在您管理此系統以供他人使用時。

所有可在圖表中追蹤的計量，也都可以用來設定警示。設定警示的效果與在 App Service 中的其他位置設定相同。您可以從警示 UI 部分設定警示，或深入探索任何計量 UI 並按 [新增警示] 來設定。
 
![][3]

剛剛所討論的計量是 App Service 環境計量。另外也有可在 App Service 方案層級上使用的計量。在其中監視 CPU 和記憶體是很合理的作法。在 ASE 中，所有的 ASP 都是專用 ASP。這表示，會在配置給該 ASP 的主機上執行的應用程式，僅限於該 ASP 中的應用程式。若要檢視 ASP 的詳細資料，請直接從 ASE UI 中的任何清單顯示您的 ASP，或藉由瀏覽 App Service 方案全部加以列出。

您可能已熟悉 ASE 以外可在 ASP 中使用的自動調整功能，及其如何運用可用於資源的計量。此外您也熟悉 App Service 環境的自動調整。您不僅仍可根據 ASE 中的計量自動調整 ASP，也可以設定 ASE 本身的自動調整規則。如需關於設定自動調整的詳細資訊，請參閱此處的詳盡指南：[App Service 環境中的自動調整][ASEAutoscale]


## 屬性 ##

[設定] 刀鋒視窗會在您顯示 ASE 刀鋒視窗時自動開啟。最上方是 [屬性]。在這裡會看到許多與 [基本功能] 重複的項目，但 [VIP 位址] 和 [輸出 IP 位址] 是非常好用的項目。目前兩者可視為同一項目，但未來很可能會區隔輸出 IP 位址與 VIP 位址，因此在這裡我們兩者併陳。因此，如果您不計入在 ASE 中設定 SSL 並為單一應用程式新增 IP 位址，在 DNS 中針對您在 ASE 中建立的應用程式所設定的 IP，將會是您在此處的 [屬性] 中所看到的 VIP 位址。

![][4]

## IP 位址 ##

您可以在此處將更多 IP 位址新增至 ASE，供您的應用程式使用。如果您在 ASE 中建立想要以 IP SSL 設定的應用程式，您需要保留一個 IP 位址僅供該應用程式使用。若要那麼做，您的 ASE 必須有一些本身擁有而可配置的 IP 位址。ASE 建立時，會有 1 個位址用於此目的。如果您需要更多，請在這裡新增。在您拉到最大配額之前，請注意如果您需要更多，額外的 IP 位址將有其費用。關於增加費用的詳細資料列載於價格頁面上：[App Service 價格][AppServicePricing]，請直接向下捲動至關於 SSL 連線的一節。額外的價格是 IP SSL 價格。

**附註：**如果您新增更多 IP 位址，請留意這是調整作業。一次只能進行一個調整作業。共有三種級別作業：

- 變更 ASE 中可供 IP SSL 使用的 IP 位址數目
- 變更用於資源集區中的計算資源大小
- 以手動方式或透過自動調整變更用於資源集區中的計算資源數目

## 資源集區 ##

您可以從 [設定] 進入前端集區或背景工作集區 UI。這些資源集區刀鋒視窗可讓您檢視該資源集區的資訊，以及提供充分調整該資源集區所需的控制。

每個資源集區的基準刀鋒視窗分別會提供一個圖表，內附該資源集區的計量。就像 ASE 刀鋒視窗中的圖表，您可以進入圖表，並設定所需的警示。從 ASE 刀鋒視窗為特定的資源集區設定警示，效果等同於從資源集區進行設定。在背景工作集區的 [設定] 刀鋒視窗中，您有權列出在此背景工作集區中執行的所有應用程式或 App Service 方案。

![][5]

### 計算資源數量級別 ###

若要更清楚地顯示在 ASE 中調整應用程式的情形，請參閱以下指南：[在 App Service 環境中調整應用程式](app-service-web-scale-a-web-app-in-an-app-service-environment.md)。如果您想要進一步了解如何設定 ASE 資源集區的自動調整，請先參閱：[App Service 環境中的自動調整][ASEAutoscale]。其中詳細說明資源集區的手動調整作業。

租用戶無法直接存取資源集區、前端和背景工作。也就是說，您無法為他們 RDP、變更其佈建，或作為其系統管理員。他們會由 Azure 進行操作與維護。因此，計算資源的數量和大小是由使用者來決定。

實際上有三種方式可控制您資源集區中擁有的伺服器數目 - 最上方主要 ASE 刀鋒視窗中的調整作業 - 個別資源集區的 [調整] 刀鋒視窗中的手動調整作業 (位於 [設定] 下) - 可從個別資源集區的 [調整] 刀鋒視窗設定的自動調整

若要使用 ASE 刀鋒視窗上的調整作業，請直接按一下它，然後將滑桿拖曳至所需的數量並儲存即可。此 UI 也支援變更大小作業。

![][6]

若要使用特定資源集區中的手動或自動調整功能，請先進入 ASE 刀鋒視窗，並移至 [設定]。在 [設定] 上依需求開啟前端集區或或背景工作集區，然後開啟您想要變更的集區。在 [設定] 下會有 [調整] ＞ 形箭號。它所開啟的刀鋒視窗可以用於手動調整或自動調整。

![][7]

App Service 環境可設定為最多總計使用 55 個計算資源。在這 55 個計算資源中，只有 50 個可用來裝載工作負載。其原因有兩個。前端計算資源的下限為 2 個。上限可高達 53 個，用以支援背景工作集區配置。為了提供容錯功能，您需要根據下列規則配置額外的計算資源：

- 每個背景工作角色集區至少需要一個無法被指派工作負載的額外計算資源
- 當背景工作角色集區中的計算資源數量超出特定值時，則需要另一個計算資源以進行容錯。在前端集區中並非如此。

在任何單一背景工作集區中，容錯需求就是指派給背景工作集區的 X 個資源的指定值：

- 如果 X 介於 2 到 20，您可用於工作負載的可用計算資源數量為 X-1
- 如果 X 介於 21 到 40，您可用於工作負載的可用計算資源數量為 X-2
- 如果 X 介於 41 到 53，您可用於工作負載的可用計算資源數量為 X-3

切記，最少的使用量具有 2 個前端伺服器和 2 個背景工作角色。以上陳述可用幾個範例加以釐清。

- 如果您在單一集區中有 30 個背景工作，則其中有 28 個可用來裝載工作負載。 
- 如果您在單一集區中有 2 個背景工作角色，則有 1 個可用來裝載工作負載。
- 如果您在單一集區中有 20 個背景工作角色，則有 19 個可用來裝載工作負載。  
- 如果您在單一集區中有 21 個背景工作角色，則同樣只有 19 個可用來裝載工作負載。  

容錯是很重要的環節，但您在調整超過特定臨界值時必須加以留意。如果您想要從 20 個執行個體新增更多容量，請調整至 22 或更高，因為 21 並不會額外新增任何容量。調整為 40 以上時也是一樣，下一個會新增容量的數目為 42。

### 計算資源大小級別 ###

除了能夠管理您可指派給指定集區的計算資源數量以外，您也可以控制大小。在 App Service 環境中，您可以選擇 4 個標示為 P1 至 P4 的不同大小。如需這些大小及其定價的詳細資訊，請參閱 [App Service 訂價](../app-service/app-service-value-prop-what-is.md)。P1 至 P3 計算資源大小就是通常可用的大小。P4 計算資源提供 14 GB RAM 的八核心， 而且只適用於 App Service 環境。

如果您想要變更集區中所使用的計算資源大小，可透過兩種方式來執行。有調整命令可從 ASE刀鋒視窗使用，也可從 [定價層] 刀鋒視窗使用 (移至個別資源集區中的 [設定] 即可進入)。

![][8]

不過在進行任何變更之前，請務必注意下列事項：

- 所做的變更可能需要數小時才能完成，視變更的大小而定
- 已經有 App Service 環境組態變更正在進行時，您就無法開始另一項變更
- 如果您變更背景工作角色集區中使用的計算資源大小，您可能會導致在該背景工作角色集區中執行的應用程式中斷

將其他執行個體新增至背景工作角色集區是一項良性作業，不會導致任何應用程式因為該集區中的資源而中斷。不過，變更用於背景工作集區中的計算資源大小則是另一回事。若要避免在背景工作角色集區的大小變更期間發生停機，最好是：

- 使用未使用的背景工作集區，以提出所需大小的必要執行個體
- 將 App Service 方案調整為新的背景工作集區。  
 
相較於變更具有執行中工作負載的計算資源大小，這麼做比較不會干擾執行中的應用程式。如需在 App Service 環境中調整應用程式的詳細資訊，請移至[在 App Service 環境中調整應用程式](app-service-web-scale-a-web-app-in-an-app-service-environment.md)

## 虛擬網路 ##

不同於包含 ASE 的託管服務，[虛擬網路][virtualnetwork]和子網路全都在使用者的控制之下。App Service 環境的確有一些網路需求，但其餘部分是由使用者控制。這些 ASE 需求包括：

- 傳統 "v1" VNET 
- 至少有 8 個位址的子網路 
- VNET 必須是區域 VNET  
 
透過虛擬網路 UI 或 Powershell 即可管理您的 VNET。

因為這項功能會將 Azure App Service 放入 VNET，這表示裝載於 ASE 的應用程式現在可以存取直接透過 ExpressRoute 或站台對站台 VPN 提供的資源。您的 APP Service 環境中的應用程式不需要額外的網路功能，即可存取裝載 App Service 環境的 VNET 可用的資源。這表示您不需要使用 VNET 整合或混合式連線，即可將資源移入或連接到您的 VNET。但您仍然可以使用這兩個功能，存取網路中未連接到 VNET 的資源。比方說，您可以使用 VNET 整合，與您的訂用帳戶中未連接到您的 ASE 所在之 VNET 的 VNET 相整合。您仍可使用混合式連線來存取其他網路中的資源，一如既往。

如果您的 VNET 設定了 ExpressRoute VPN，則應留意 ASE 的某些路由需求。某些使用者定義的路由 (UDR) 組態與 ASE 不相容。如需關於在具有 ExpressRoute 的 VNET 中執行 ASE 的詳細資訊，請參閱以下文件：[在具有 ExpressRoute 的 VNET 中執行 App Service 環境][ExpressRoute]

您現在也可以使用網路安全性群組來控制應用程式的存取。這項功能可讓您鎖住您的 App Service 環境，只您想要限制它的 IP 位址。如需相關詳細資訊，請參閱以下文件：[如何在 App Service 環境中控制輸入流量](app-service-app-service-environment-control-inbound-traffic.md)。

## 刪除 App Service 環境 ##

如果您要刪除 App Service 環境，只需使用 App Service 環境刀鋒視窗頂端的 [刪除] 動作。當您這麼做時，系統將提示您輸入 App Service 環境的名稱，以確認您真正想要執行這項操作。附註：當您刪除 App Service 環境時，將同時刪除其中包含的所有內容。

![][9]

## 開始使用

若要開始使用 App Service 環境，請參閱[如何建立 App Service 環境](app-service-web-how-to-create-an-app-service-environment.md)

如需有關 Azure App Service 平台的詳細資訊，請參閱 [Azure App Service](../app-service/app-service-value-prop-what-is.md)。

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-web-configure-an-app-service-environment/ase-icon.png
[2]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-aseblade.png
[3]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolchart.png
[4]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-properties.png
[5]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolblade.png
[6]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-scalecommand.png
[7]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolscale.png
[8]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-pricingtiers.png
[9]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-deletease.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[Appserviceplans]: http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[HowtoCreateASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[HowtoScale]: http://azure.microsoft.com/documentation/articles/app-service-web-scale-a-web-app-in-an-app-service-environment/
[ControlInbound]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[ASEAutoscale]: http://azure.microsoft.com/documentation/articles/app-service-environment-auto-scale/
[ExpressRoute]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-network-configuration-expressroute/

<!---HONumber=AcomDC_0128_2016-->