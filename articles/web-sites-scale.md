<properties 
	pageTitle="在 Azure App Service 中調整 Web 應用程式規模" 
	description="了解如何在 Azure App Service 中調升和調降 Web 應用程式規模，包括自動調整。" 
	services="app-service\web" 
	documentationCenter="" 
	authors="cephalin" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="cephalin"/>

# 在 Azure App Service 中調整 Web 應用程式規模 #

若要在 Microsoft Azure 上提高 Web 應用程式的效能和輸送量，可以使用 [Azure 入口網站](http://go.microsoft.com/fwlink/?LinkId=529715)，將您的 [App Service](http://go.microsoft.com/fwlink/?LinkId=529714) 方案從 [免費] 模式調整為 [共用]、[基本]、[標準] 或 [高階] 模式。 

調升 Azure Web 應用程式規模牽涉到兩個相關動作：將 App Service 方案模式變更為較高的服務層級，以及在切換為較高的服務層級後設定特定設定。本文章涵蓋以上兩個主題。較高的服務層級 (如 [標準] 和 [高階] 模式) 能讓您以更健全、更彈性的方式決定 Azure 中的資源使用情況。

模式的變更和設定可在管理入口網站的 [調整] 索引標籤中輕易完成。您可以視需要擴大或縮小。這些變更只需幾秒鐘便能完成套用，且影響範圍遍及 App Service 方案內的所有 Web 應用程式。您不需要變更程式碼或重新部署應用程式。

如需 App Service 方案的相關資訊，請參閱[什麼是 App Service 方案？](web-sites-web-hosting-plan-overview.md) 和 [Azure App Service 方案的深入概觀](azure-web-sites-web-hosting-plans-in-depth-overview.md). 如需個別 App Service 方案的定價和功能相關資訊，請參閱 [App Service 定價詳細資料](/pricing/details/web-sites/).  

> [AZURE.NOTE] 將 Web 應用程式從 [免費] 模式切換為 [基本]、[標準] 或 [高階] 模式之前，您必須先適當地移除 Azure App Service 訂用帳戶的支出費用上限。若要檢視或變更 Microsoft Azure App Service 訂用帳戶的選項，請參閱 [Microsoft Azure 訂用帳戶][azuresubscriptions]。

<a name="scalingsharedorbasic"></a>
<!-- ===================================== -->
## 調整為共用或基本模式
<!-- ===================================== -->

1. 在瀏覽器中，開啟 [Azure 入口網站][portal]。
	
2. 在 Web 應用程式刀鋒視窗中，依序按一下 [所有設定]、[調整規模] 及 [從免費方案升級以加入執行個體並取得更佳效能]。
	
	![選擇方案][ChooseWHP]
	
4. 在 [選擇定價層] 刀鋒視窗中，選擇 [共用] 或 [基本] 模式，然後按一下 [選取]。
	
	一旦作業完成之後，[通知] 索引標籤就會閃爍綠色以代表 [成功]。 
	
5. 將 [執行個體] 滑桿從左邊滑動到右邊以增加執行個體數目，然後按一下命令列中的 [儲存]。[共用] 模式未提供執行個體大小選項。如需這些執行個體大小的詳細資訊，請參閱 [Microsoft Azure 的虛擬機器和雲端服務大小][vmsizes]。
	
	![基本模式的執行個體大小][ChooseBasicInstances]
	
	一旦作業完成之後，[通知] 索引標籤就會閃爍綠色以代表 [成功]。 
	
<a name="scalingstandard"></a>
<!-- ================================= -->
## 調整為標準或高階模式
<!-- ================================= -->

> [AZURE.NOTE] 在將 App Service 方案切換為 [標準] 或 [高階] 模式之前，應該針對您的 Microsoft Azure App Service 訂用帳戶適當地移除支出費用上限，以免在帳單期間還未結束之前，Web 應用程式就因為已達支出上限而變得無法使用。若要檢視或變更 Microsoft Azure App Service 訂用帳戶的選項，請參閱 [Microsoft Azure 訂用帳戶][azuresubscriptions]。

1. 若要調整為 [標準] 或 [高階] 模式，請依照與調整為 [共用] 或 [基本] 時相同的初始步驟執行，接著在 [選擇定價層] 中選擇 [標準] 或 [高階] 模式，然後按一下 [選取]。 
	
	一旦作業完成之後，[通知] 索引標籤就會閃爍綠色以表示 [成功]，而且將啟用 [自動調整模式]。
	
	![在標準或高階模式中調整規模][ScaleStandard]
	
	您仍然可以滑動 [執行個體] 滑桿，手動調整為更多執行個體，就像在 [基本] 模式中所做的一樣 (如上所述)。但是，您將在此處了解如何使用 [自動調整模式]。 
	
2. 在 [自動調整模式] 中，選取 [效能]，以根據效能度量進行自動調整。
	
	![將自動調整模式設為效能][Autoscale]
	
3. 在 [執行個體範圍] 中，移動這兩個滑桿來定義執行個體數目的下限與上限，以針對 App Service 方案進行自動調整。在此教學課程中，請將上限滑桿移至 **6** 個執行個體。
	
4. 按一下命令列中的 [儲存]。
	
4. 在 [目標度量] 下方，按一下 [>]，為預設度量設定自動調整規則。  
	
	![設定目標度量][SetTargetMetrics]
	
	您可以為不同的效能度量 (包括 CPU、記憶體、磁碟佇列、HTTP 佇列和資料流程) 設定自動調整規則。您會在此處將 CPU 百分比設為自動調整，以執行下列作業：
	
	- 如果 CPU 在最後 10 分鐘內超過 70%，即會調升 1 個執行個體
	- 如果 CPU 在最後 5 分鐘內超過 90%，即會調升 3 個執行個體
	- 如果 CPU 在最後 30 分鐘內低於 50%，即會調降 1 個執行個體 
	
	
4. 將 [度量] 下拉式清單保留為 [CPU 百分比]。
	
5. 在 [調升規則] 中，藉由將 [條件] 設為 [大於]、將 [閾值] 設為 [70] (%)、將 [經過的時間] 設為 [10] (分鐘)、將 [調升依據] 設為 [1] (執行個體)，以及將 [緩和時間] 設為 [10] (分鐘)，來設定第一個規則。 
	
	![設定第一個自動調整規則][SetFirstRule]
	
	>[AZURE.NOTE] [緩和時間] 設定會指定在前次調整動作之後，此規則再次執行調整前所應等待的時間。
	
6. 按一下 [新增調升規則]，然後藉由將 [條件] 設為 [大於]、將 [閾值] 設為 [90] (%)、將 [經過的時間] 設為 [1] (分鐘)、將 [調升依據] 設為 [3] (執行個體)，以及將 [緩和時間] 設為 [1] (分鐘)，來設定第二個規則。
	
	![設定第二個自動調整規則][SetSecondRule]
	
5. 在 [調降規模規則] 中，藉由將 [條件] 設為 [小於]、將 [閾值] 設為 [50] (%)、將 [經過的時間] 設為 [30] (分鐘)、將 [調降規模依據] 設為 [1] (執行個體)，以及將 [緩和時間] 設為 [60] (分鐘)，來設定第三個規則。 
	
	![設定第三個自動調整規則][SetThirdRule]
	
7. 按一下命令列中的 [儲存]。自動調整規則現在應該會反映在 [調整規模] 刀鋒視窗中。 
	
	![設定自動調整規則結果][SetRulesFinal]

<a name="ScalingSQLServer"></a>
##調整與 Web 應用程式連接的 SQL Server 資料庫規模
如果您有一或多個 SQL Server 資料庫連結至 Web 應用程式 (不論 App Service 方案模式為何)，您都可以根據需求快速調整它們的規模。

1. 若要調整其中一個連結的資料庫規模，可在 [Azure 入口網站][portal]中開啟 Web 應用程式的刀鋒視窗。在 [基本功能] 摺疊下拉式清單中，按一下 [資源群組] 連結。接著，在資源群組刀鋒視窗的 [摘要] 組件中，按一下其中一個連結的資料庫。

	![連結的資料庫][ResourceGroup]
	
2. 在連結的 SQL Database 刀鋒視窗中，按一下 [定價層] 組件、根據您的效能需求選取其中一個層級，然後按一下 [選取]。 
	
	![調整您的 SQL Database 規模][ScaleDatabase]
	
3. 您也可以設定異地複寫，以提高 SQL Database 的高可用性和災害復原功能。若要執行這個動作，可按一下 [異地複寫] 組件。
	
	![針對 SQL Database 設定異地複寫][GeoReplication]

<a name="devfeatures"></a>
## 開發人員功能
視 Web 應用程式的模式而定，可使用下列以開發人員為導向的功能：

### 位元 ###

- [基本]、[標準] 及 [高階] 模式支援 64 位元和 32 位元的應用程式。
- [免費] 和 [共用] 方案模式僅支援 32 位元的應用程式。

### 偵錯工具支援 ###

- 在每個 App Service 方案 1 個同時連線的情況下，偵錯工具支援適用於 [免費]、[共用] 及 [基本] 模式。
- 在每個 App Service 方案 5 個同時連線的情況下，偵錯工具支援適用於 [標準] 和 [高階] 模式。

<a name="OtherFeatures"></a>
## 其他功能

### Web 端點監視 ###

- Web 端點監視適用於 [基本]、[標準] 及 [高階] 模式。如需 Web 端點監視的詳細資訊，請參閱[如何監視 Web 應用程式](web-sites-monitor.md).。

- 如需 App Service 方案中所有剩餘功能的詳細資訊，包括所有使用者 (包含開發人員在內) 關心的定價和功能，請參閱 [App Service 定價詳細資料](/pricing/details/web-sites/).。

>[AZURE.NOTE] 如果您想要在註冊 Azure 帳戶之前開始使用 Azure App Service，請移至[試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751)，讓您能夠在 App Service 中立即建立短期的入門 Web 應用程式。不需要信用卡，無需承諾。

<a name="Next Steps"></a>	
## 後續步驟

- 若要開始使用 Azure，請參閱 [Microsoft Azure 免費試用](/pricing/free-trial/).。
- 如需定價、支援及 SLA 的相關資訊，請參閱以下連結：
	
	[資料傳輸定價詳細資料](/pricing/details/data-transfers/)
	
	[Microsoft Azure 支援方案](/support/plans/)
	
	[服務等級協定](/support/legal/sla/)
	
	[SQL Database 定價詳細資料](/pricing/details/sql-database/)
	
	[Microsoft Azure 的虛擬機器和雲端服務大小][vmsizes]
	
	[App Service 定價詳細資料](/pricing/details/web-sites/)
	
	[App Service 定價詳細資料 - SSL 連線](/pricing/details/web-sites/#ssl-connections)

- 如需 Azur App Service 最佳做法 (包括建置可調整且具彈性的架構) 的相關資訊，請參閱[最佳做法：Azure App Service Web Apps](http://blogs.msdn.com/b/windowsazure/archive/2014/02/10/best-practices-windows-azure-websites-waws.aspx)。

- 調整 Web Apps 規模的相關影片：
	
	- [何時該調整 Azure 網站規模 - 和 Stefan Schackow 一起](/documentation/videos/azure-web-sites-free-vs-standard-scaling/)
	- [自動調整 Azure 網站規模、CPU 或排程 - 和 Stefan Schackow 一起](/documentation/videos/auto-scaling-azure-web-sites/)
	- [如何調整 Azure 網站規模 - 和 Stefan Schackow 一起](/documentation/videos/how-azure-web-sites-scale/)

## 相關變更
* 如需從網站變更為 App Service 的指南，請參閱：[Azure App Service 及其對現有 Azure 服務的影響](http://go.microsoft.com/fwlink/?LinkId=529714)
* 如需從舊版入口網站變更為新版入口網站的指南，請參閱：[瀏覽預覽入口網站的參考](http://go.microsoft.com/fwlink/?LinkId=529715)

<!-- LINKS -->
[vmsizes]:http://go.microsoft.com/fwlink/?LinkId=309169
[SQLaccountsbilling]:http://go.microsoft.com/fwlink/?LinkId=234930
[azuresubscriptions]:http://go.microsoft.com/fwlink/?LinkID=235288
[portal]: https://portal.azure.com/

<!-- IMAGES -->
[ChooseWHP]: ./media/web-sites-scale/scale1ChooseWHP.png
[ChooseBasicInstances]: ./media/web-sites-scale/scale2InstancesBasic.png
[SaveButton]: ./media/web-sites-scale/05SaveButton.png
[BasicComplete]: ./media/web-sites-scale/06BasicComplete.png
[ScaleStandard]: ./media/web-sites-scale/scale3InstancesStandard.png
[Autoscale]: ./media/web-sites-scale/scale4AutoScale.png
[SetTargetMetrics]: ./media/web-sites-scale/scale5AutoScaleTargetMetrics.png
[SetFirstRule]: ./media/web-sites-scale/scale6AutoScaleFirstRule.png
[SetSecondRule]: ./media/web-sites-scale/scale7AutoScaleSecondRule.png
[SetThirdRule]: ./media/web-sites-scale/scale8AutoScaleThirdRule.png
[SetRulesFinal]: ./media/web-sites-scale/scale9AutoScaleFinal.png
[ResourceGroup]: ./media/web-sites-scale/scale10ResourceGroup.png
[ScaleDatabase]: ./media/web-sites-scale/scale11SQLScale.png
[GeoReplication]: ./media/web-sites-scale/scale12SQLGeoReplication.png

<!--HONumber=49-->