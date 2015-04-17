<properties 
	pageTitle="如何在 Azure 應用程式服務中調整 Web 應用程式" 
	description="了解如何在 Azure 應用程式服務中向上及向外擴充 Web 應用程式，包括自動調整。" 
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
	ms.date="03/23/2015" 
	ms.author="cephalin"/>

# 如何在 Azure 應用程式服務中調整 Web 應用程式 #

若要在 Microsoft Azure 上提高 Web 應用程式的效能和輸送量，以啟用更多的功能而符合您的業務需求，您可以使用 Azure 管理入口網站，將您的應用程式服務計劃從 [免費] 模式調整為 [共用]、[基本]、[標準] 和 [高階 (預覽)] 模式。 

調升 Azure Web 應用程式的規模牽涉到兩個相關動作：將應用程式服務計劃模式變更為較高的服務等級，和在切換為較高的服務等級後設定特定設定。本文章涵蓋以上兩個主題。較高的服務層級 (如「標準」模式) 能讓您以更健全、更彈性的方式決定 Azure 中的資源使用情況。

模式的變更和設定可在管理入口網站的 [調整] 索引標籤中輕易完成。您可以視需要擴大或縮小。這些變更只需要幾秒鐘的時間便能完成套用，且影響範圍遍及服務計劃中的所有 Web 應用程式。您不需要變更程式碼或重新部署應用程式。

如需應用程式服務計劃 (前身為虛擬主機方案) 的相關資訊，請參閱[什麼是應用程式服務主控計劃？](web-sites-web-hosting-plan-overview.md)與 [Azure 應用程式服務主控計劃深入概觀](azure-web-sites-web-hosting-plans-in-depth-overview.md)。若想了解個別應用程式服務計劃的定價資訊及功能，請參閱[應用程式服務定價詳細資料](http://http://azure.microsoft.com/pricing/details/web-sites/)。  

> [AZURE.NOTE] 將 Web 應用程式從 [**免費**] 模式切換為 [**基本**] 或 [**標準**] 模式之前，您必須先移除 Azure 應用程式服務訂閱中已有的支出上限。若要檢視或變更 Microsoft Azure 應用程式服務訂閱的選項，請參閱 [Microsoft Azure 訂閱][azuresubscriptions]。

<a name="scalingsharedorbasic"></a>
<!-- ===================================== -->
## 調整為共用或基本應用程式服務計劃模式
<!-- ===================================== -->

1. 在瀏覽器中，開啟 [Azure 管理入口網站][portal]。
	
2. 在您的 Web 應用程式分頁中，依序按一下 [**所有設定**]、[**調整**]、[**從免費計劃升級以新增執行個體並提升效能**]。
	
	![Choose Hosting Plan][ChooseWHP]
	
4. 在 [**選擇定價層**] 分頁中，選擇 [**共用**] 或 [**基本**] 模式，然後按一下 [**選取**]。

	當作業完成時，[**通知**] 索引標籤會有綠色的**成功**字樣閃爍顯示。 
	
5. 將 [**執行個體**] 列從左滑動到右以增加執行個體的數量，然後按一下命令列中的 [**儲存**]。[**共用**] 模式未提供執行個體大小選項。如需這些執行個體大小的詳細資訊，請參閱 [Windows Azure 的虛擬機器和雲端服務大小][vmsizes]。
	
	![Instance size for Basic mode][ChooseBasicInstances]
	
	當作業完成時，[**通知**] 索引標籤會有綠色的**成功**字樣閃爍顯示。 
	
<a name="scalingstandard"></a>
<!-- ================================= -->
## 調整為標準應用程式服務計劃模式
<!-- ================================= -->

> [AZURE.NOTE] 在將應用程式服務計劃從 [**免費**] 切換為 [**標準**] 模式之前，您應先移除 Microsoft Azure 應用程式服務訂閱中已有的支出上限。以免帳單期間還未結束，網站就因為已達支出上限而變得無法使用。若要檢視或變更 Microsoft Azure 應用程式服務訂閱的選項，請參閱 [Microsoft Azure 訂閱][azuresubscriptions]。

1. 若要調整為 [**標準**] 模式，請遵循調整為 [**共用**] 或 [**基本**] 時的相同起始步驟，然後在 [**選擇定價層**] 中選擇 [**標準**] 模式，再按一下 [**選取**]。 
	
	當作業完成時，[**通知**] 索引標籤會有綠色的**成功**字樣閃爍顯示，並且會啟用 [**自動調整模式**]。

	![Scale in Standard Mode][ScaleStandard]

	您仍可滑動 [**執行個體**] 列以手動調整為更多執行個體，就像在前述的 [**基本**] 模式中一樣。但在此您將了解到如何使用 [**自動調整模式**]。 

2. 在 [**自動調整模式**] 中選取　[**效能**]，可根據效能度量自動調整。
	
	![Autoscale Mode set to Performance][Autoscale]
	
3. 在 [**執行個體範圍**] 中移動兩個滑桿，可定義要為應用程式服務計劃自動調整的執行個體數量下限和上限。在本教學課程中，請將最大滑桿移至 **6** 個執行個體。

4. 按一下命令列中的 [**儲存**]。

4. 在 [**目標度量**] 下，按一下 **>** 以設定預設度量的自動調整規則。  
	
	![Set Target Metrics][SetTargetMetrics]
	
	您可以設定不同效能度量的自動調整規則，包括 CPU、記憶體、磁碟佇列、HTTP 佇列和資料流程。在此，您將為處於下列狀況的 CPU 百分比設定自動調整：

	- 如果 CPU 使用率在過去 10 分鐘內高於 70%，則向上調整 1 個執行個體
	- 如果 CPU 使用率在過去 5 分鐘內高於 90%，則向上調整 3 個執行個體
	- 如果 CPU 使用率在過去 30 分鐘內低於 50%，則向下調整 1 個執行個體 


4. 將 [**度量**] 下拉式清單保留為 [**CPU 百分比**]。
	
5. 在 [**向上調整規則**] 中設定第一項規則，方法是將 [**條件**] 設為 [**大於**]、[**臨界值**] 設為 [**70**\] (%)、[**經歷時間**] 設為 [**10**\] (分鐘)、[**向上調整值**] 設為 [**1**\] (執行個體)，並將 [**等待期間**] 設為 [**10**\] (分鐘)。 
	
	![Set First Autoscale Rule][SetFirstRule]

	>[AZURE.NOTE] [**等待期間**] 設定會指定在前次調整動作之後，此規則要再次執行調整前所應等待的時間。
	
6. 按一下 [**新增向上調整規則**]，然後設定第二項規則，方法是將 [**條件**] 設為 [**大於**]、[**臨界值**] 設為 [**90**\] (%)、[**經歷時間**] 設為 [**1**\] (分鐘)、[**向上調整值**] 設為 [**3**\] (執行個體)，並將 [**等待期間**] 設為 [**1**\] (分鐘)。
	
	![Set Second Autoscale Rule][SetSecondRule]
	
5. 在 [**向下調整規則**] 中設定第三項規則，方法是將 [**條件**] 設為 [**小於**]、[**臨界值**] 設為 [**50**\] (%)、[**經歷時間**] 設為 [**30**\] (分鐘)、[**向下調整值**] 設為 [**1**\] (執行個體)，並將 [**等待期間**] 設為 [**60**\] (分鐘)。 
	
	![Set Third Autoscale Rule][SetThirdRule]
	
7. 按一下命令列中的 [**儲存**]。自動調整規則現在應該會反映在 [**調整**] 分頁中。 
	
	![Set Autoscale Rule Result][SetRulesFinal]
	
<a name="ScalingSQLServer"></a>
## 調整與您的 Web 應用程式連接的 SQL Server 資料庫
如果您有一或多個連結至 Web 應用程式的 SQL Server 資料庫 (無論應用程式服務計劃模式為何)，您都可以根據本身的需求快速加以調整。

1. 若要調整其中一個連結的資料庫，請在 [Azure 入口網站][portal]中開啟您的 Web 應用程式分頁。在 **Essentials** 可摺疊的下拉式清單中，按一下 [**資源群組**] 連結。然後，在資源群組分頁的 [**摘要**] 部件中，按一下其中一個連結的資料庫。
	
	![Linked database][ResourceGroup]
	
2. 在您連結的 SQL 資料庫分頁中，按一下 [**定價層**] 部件，根據您的效能需求選取其中一層，然後按一下 [**選取**]。 
	
	![Scale your SQL Database][ScaleDatabase]
	
3. 您也可以設定地理複寫，以增加 SQL 資料庫的高可用性和災害復原功能。若要這樣做，請按一下 [**地理複寫**] 部件。

	![Set up geo-replication for SQL Database][GeoReplication]

<a name="devfeatures"></a>
## 開發人員功能
視 Web 應用程式的服務計劃模式而定，可以使用下列以開發人員為導向的功能：

### 位元 ###

- 基本和標準方案模式支援 64 和 32 位元的應用程式。
- 免費和共用方案模式僅支援 32 位元的應用程式。

### 偵錯工具支援 ###

- 對於免費、共用及基本應用程式服務計劃模式提供的偵錯工具支援，每個應用程式僅限 1 個同時連線。
- 對於標準應用程式服務計劃模式提供的偵錯工具支援，每個應用程式可擁有 5 個同時連線。

<a name="OtherFeatures"></a>
## 其他功能

### Web 端點監視 ###

- 基本和標準應用程式服務計劃模式提供 Web 端點監視功能。如需 Web 端點監視的詳細資訊，請參閱[如何監視 Web 應用程式](web-sites-monitor.md)。

- 如需應用程式服務計劃其他所有功能的詳細資訊，包括所有使用者 (包括開發人員) 關心的定價和功能，請參閱[應用程式服務定價詳細資料](http://azure.microsoft.com/pricing/details/web-sites/)。

<a name="Next Steps"></a>	
## 後續步驟

- 若要開始使用 Azure，請參閱 [Microsoft Azure 免費試用](http://azure.microsoft.com/pricing/free-trial/)。

- 如需定價、支援及 SLA 的相關資訊，請參閱以下連結：
	
	[資料傳輸定價詳細資料](http://azure.microsoft.com/pricing/details/data-transfers/)
	
	[Microsoft Azure 支援方案](http://azure.microsoft.com/support/plans/)
	
	[服務等級協定](http://azure.microsoft.com/support/legal/sla/)
	
	[SQL Database 定價詳細資料](http://azure.microsoft.com/pricing/details/sql-database/)
	
	[Microsoft Azure 的虛擬機器和雲端服務大小][vmsizes]
	
	[應用程式服務定價詳細資料](http://azure.microsoft.com/pricing/details/web-sites/)
	
	[應用程式服務定價詳細資料 - SSL 連線](http://azure.microsoft.com/pricing/details/web-sites/#ssl-connections)

- 如需 Azure 應用程式服務最佳作法 (包括建置可調整且具彈性的架構) 的相關資訊，請參閱[最佳作法：Microsoft Azure 網站 (WAWS)](http://blogs.msdn.com/b/windowsazure/archive/2014/02/10/best-practices-windows-azure-websites-waws.aspx)。

- 調整 Azure 網站規模的影片：
	
	[何時該調整 Azure 網站規模，與 Stefan Schackow](http://azure.microsoft.com/documentation/videos/azure-web-sites-free-vs-standard-scaling/)
	
	[自動調整 Azure 網站規模、CPU 或排程，與 Stefan Schackow](http://azure.microsoft.com/documentation/videos/auto-scaling-azure-web-sites/)

	[如何調整 Azure 網站規模，與 Stefan Schackow](http://azure.microsoft.com/documentation/videos/how-azure-web-sites-scale/)



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