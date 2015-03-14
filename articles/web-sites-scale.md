<properties 
	pageTitle="如何調整網站規模" 
	description="必要" 
	services="web-sites" 
	documentationCenter="" 
	authors="cephalin" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/24/2014" 
	ms.author="cephalin"/>

# 如何調整網站規模 #

為了提升 Microsoft Azure 中網站的效能和輸送量，您可以使用 Azure 管理入口網站將「免費」的虛擬主機方案模式調整為「共用」、「基本」或「標準」。 

調升 Azure 網站規模牽涉到兩個相關動作：將虛擬主機方案模式變更為較高的服務等級，和在切換為較高的服務等級後設定特定設定。本文章涵蓋以上兩個主題。較高的服務層級 (如「標準」模式) 能讓您以更健全、更彈性的方式決定 Azure 中的資源使用情況。

模式的變更和設定可在管理入口網站的 [調整] 索引標籤中輕易完成。您可以視需要擴大或縮小。這些變更只需要幾秒鐘的時間便能完成套用，且影響範圍遍及虛擬主機方案內的所有網站。您不需要變更程式碼或重新部署應用程式。

如需虛擬主機方案的相關資訊，請參閱[什麼是虛擬主機方案？](http://azure.microsoft.com/documentation/articles/web-sites-web-hosting-plan-overview/)與[Azure 網站虛擬主機方案深入概觀](http://www.azure.microsoft.com/zh-tw/Documentation/Articles/azure-web-sites-web-hosting-plans-in-depth-overview/)。
如需個別虛擬主機方案之定價與功能的相關資訊，請參閱[網站定價詳細資料](http://azure.microsoft.com/pricing/details/web-sites/)。  

> [AZURE.NOTE] 將網站從「免費」****虛擬主機方案模式切換到「基本」****或「標準」****虛擬主機方案模式之前，您必須先 Azure 網站訂用帳戶的支出上限。若要檢視或變更 Microsoft Azure 網站訂用帳戶的選項，請參閱 [Microsoft Azure 訂用帳戶][azuresubscriptions]。

本文內容：

- [調整為共用或基本方案模式](#scalingsharedorbasic)
- [調整為標準方案模式](#scalingstandard)
- [調整與網站連接的 SQL Server Database](#ScalingSQLServer)
- [開發人員功能](#devfeatures)
- [其他功能](#OtherFeatures)

<a name="scalingsharedorbasic"></a>
<!-- ===================================== -->
## 調整為共用或基本方案模式
<!-- ===================================== -->

1. 在瀏覽器中開啟[管理入口網站][入口網站]。
	
2. 在 [網站]**** 索引標籤中，選取您的網站。
	
	![Selecting a website][SelectWebsite]
	
3. 按一下 [調整]**** 索引標籤。
	
	![The scale tab][SelectScaleTab]
	
4. 在 [虛擬主機方案模式]**** 區段中，選擇 [共用]**** 或 [基本]****。影像中的範例選擇 [基本]。
	
	![Choose Web Hosting Plan][ChooseWHP]
	
	[虛擬主機方案網站]**** 區段顯示目前方案中網站的簡短清單。所有採用目前方案的網站都將變更為您選取虛擬主機方案模式。
	
5. 在 [容量]**** 區段中，選擇 [執行個體大小]****。可用選項包括 [小型]****、[中型] **** 或 [大型]****。共用模式未提供執行個體大小選項。如需這些執行個體大小的詳細資訊，請參閱 [Microsoft Azure 的虛擬機器和雲端服務大小][vmsizes]。
	
	![Instance size for Basic mode][ChooseBasicInstanceSize]
	
6. 使用滑桿來選擇所需的 [執行個體計數]****。
	
	![Instance count for Basic mode][ChooseBasicInstanceCount]
	
7. 在命令列中選擇 [儲存]****。 
	
	![Save button][SaveButton]
 	
	> [AZURE.NOTE] 如果需要的話，您可以個別設定和儲存 [虛擬主機方案]****、[執行個體大小]**** 與 [執行個體計數]**** 設定。
	
8. 確認訊息會提醒您採用之虛擬主機方案與目前網站相同的網站也會變更為新模式。選擇 [是]**** 以完成變更。 
	
	在範例中，方案模式已變更為 [基本]****：
	
	![Plan change complete][BasicComplete]
	
<a name="scalingstandard"></a>
<!-- ================================= -->
## 調整為標準方案模式
<!-- ================================= -->

> [AZURE.NOTE] 在將虛擬主機方案切換為 [標準] 模式之前，您應該先移除 Microsoft Azure 網站訂用帳戶的支出上限。以免帳單期間還未結束，網站就因為已達支出上限而變得無法使用。若要檢視或變更 Microsoft Azure 網站訂用帳戶的選項，請參閱 [Microsoft Azure 訂用帳戶][azuresubscriptions]。

1. 若要調整為標準，請依照與調整為「共用」或「基本」模式相同的初始步驟，然後將 [虛擬主機方案模式]**** 選擇為 [標準]****。 
	
	![Choose Standard Plan][ChooseStandard]
	
	如同先前所述，[虛擬主機方案網站]**** 區段會顯示採用目前方案的簡短網站清單。在這種情況下，所有採用目前方案的網站均會變更為標準模式。
	
2. 選取 [標準]**** 會展開 [容量]**** 區段以顯示 [執行個體大小]**** 與 [執行個體計數]**** 選項 (「基本」模式亦提供這些選項)。唯有「標準」模式提供 [編輯排程的調整規模設定]**** 與 [依度量調整規模]**** 選項。
	
	![Capacity section in Standard][CapacitySectionStandard]
	
3. 設定 [執行個體大小]****。可用選項包括 [小型]****、[中型] **** 或 [大型]****。
	
	![Choose instance size][ChooseInstanceSize]
	
	如需這些執行個體大小的詳細資訊，請參閱 [Microsoft Azure 的虛擬機器和雲端服務大小][vmsizes]。
	
4. 如果您想要根據日間和夜間、工作日和週末和/或特定日期和時間來自動調整資源，請在 [編輯排程的調整規模設定]**** 選項中選擇 [設定排程時間]****。
	
	![Set up schedule times][SetUpScheduleTimesButton]
	
5. [設定排程時間]**** 對話方塊提供許多有用的組態選擇。
	
	![SetUpScheduleTimesDialog][SetUpScheduleTimesDialog]
	
6. 在 [週期性排程]**** 下，選取 [白天與夜晚的不同調整規模設定]**** 和/或 [工作日和週末的不同調整規模設定]****，可根據日間和夜間排程及/或個別的工作日和週末排程來調整資源。
	
	> [AZURE.NOTE] 為因應此項功能的目的，週末的開始時間為星期五結束時 (預設為下午 8:00)，結束時間為星期一開始時 (預設為上午 8:00)。週末設定檔使用的一天開始時間和結束時間與您在 [時間]**** 設定中的定義相同。
	
7. 在 [時間]**** 下方，選擇一天的開始和結束時間 (以半小時為單位) 及時區。依預設，一天的開始時間為上午 8:00，結束時間為下午 8:00。日光節約時間取決於您選取的時區。 
	
8. 在 [特定日期]**** 下方，您可以建立一或多個具名的時間範圍來調整資源。例如，對於可能會產生大量 Web 流量的銷售或發表活動，您也許會想要在活動進行期間提供額外的資源。
	
9. 選擇完畢之後，請按一下 [確定]**** 來關閉 [排程時間]**** 對話方塊。
	
10.   [編輯排程的調整規模設定]**** 方塊現在會根據您的變更顯示可設定的排程或事件。您可以選取一或多個週期性排程或特定的日期來予以設定。 
	
	![Edit scale settings for schedule][EditScaleSettingsForSchedule]
	
	您現在可以使用 [依度量調整規模]**** 與 [執行個體計數]**** 功能來針對每個選擇的排程微調資源調整。 
	
11.  若要在網站負載變更時動態地調整網站使用的執行個體數目，請選擇 [CPU]**** 來啟用 [依度量調整規模]**** 選項。
	
	![Scale By Metric][ScaleByMetric]
	
	圖表顯示在過去一週內使用的執行個體數目。您可以使用圖表來監視調整活動。
	
12. [依度量調整規模]**** 戶會修改 [執行個體計數]**** 功能，因此您可以設定要用於自動調整的虛擬機器數目下限和上限。Azure 一律會避免超出或低於您設定的限制。
	
	![Instance count][InstanceCount]
	
13. [依度量調整規模]**** 也會啟用 [目標 CPU]**** 選項，以便您指定 CPU 使用量的目標範圍。此範圍代表網站的平均 CPU 使用率。Windows Azure 會增加或移除標準執行個體，以將網站維持在此範圍之內。
	
€
	
	**注意**：啟用 [依度量調整規模]**** 時，Microsoft Azure 會每隔五分鐘檢查網站的 CPU 一次，然後在該時間點視需要增加執行個體。如果 CPU 使用率較低，Microsoft Azure 會每隔兩小時移除執行個體一次，確保網站維持一定效能。一般說來，將執行個體計數下限設定為 1 是合適的值。然而，如果網站突然湧入大量的使用量，請確保您的執行個體數目下限足以處理負載。例如，如果網站在 Microsoft Azure 檢查 CPU 使用率之前的 5 分鐘間隔內突然湧入大量流量，網站在該段時間內可能會處於沒有回應的狀態。如果您已預知會突然湧入大量流量，請設定較高的執行個體計數下限來因應突發的流量。 
	
14. 完成 [編輯排程的調整規模設定]**** 清單中的項目變更後，請按一下頁面底部之命令列中的 [儲存]**** 圖示來一次儲存所有排程設定 (您不需要分別儲存每個排程)。

> [AZURE.NOTE] 在 [Azure Preview 入口網站](https://portal.azure.com/)中，您不僅可以調整 CPU 百分比，還能調整其他 [記憶體百分比]、[磁碟佇列長度]、[HTTP 佇列長度]、[資料輸入] 及 [資料輸出] 的度量。您也可以建立一或多個調升規模和調降規模規則，以便讓您可以調整更多自訂控制。如需詳細資訊，請參閱 Azure 預覽入口網站文件中的[如何調整網站規模](http://azure.microsoft.com/documentation/articles/insights-how-to-scale/)。

<a name="ScalingSQLServer"></a>
##調整與網站連接的 SQL Server Database	
如果您有一或多個與網站連接的 SQL Server 資料庫 (不論虛擬主機方案模式為何)，這些資料庫會列示於 [調整規模] 頁面底部的 [已連結的資源]**** 區段中。

1. 若要調整資料庫的規模，請在 [已連結的資源]**** 區段中，按一下資料庫名稱旁的 [管理這個資料庫的調整規模]**** 連結。
	
	![Linked database][LinkedResources]
	
2. 此連結會帶您前往 Azure 管理入口網站的 [SQL Server] 索引標籤，供您設定資料庫的 [版本]**** 與 [大小上限]****：
	
	![Scale your SQL Server database][ScaleDatabase]
	
	對於 [版本]****，請根據所需的儲存體容量選擇 [基本]****、[標準]**** 或 [高階]****。對於 **Web** 與「商業」****版本的後續情況，請參閱[Web 與商業版本終止支援常見問題集](http://msdn.microsoft.com/library/azure/dn741330.aspx)。
	
	您選擇的 [大小上限]**** 值能指定資料庫的上限。我們收取的資料庫費用乃是以實際儲存的資料量為準，因此變更 [大小上限]**** 屬性本身並不會影響資料庫費用。如需詳細資訊，請參閱 [Microsoft Azure SQL Database 中的帳戶和計費][SQLaccountsbilling]。

<a name="devfeatures"></a>
## 開發人員功能
視虛擬主機方案模式而定，可以使用下列以開發人員為導向的功能：

**位元**

- 基本和標準方案模式支援 64 和 32 位元的應用程式。
- 免費和共用方案模式僅支援 32 位元的應用程式。

**偵錯工具支援**

- 對於免費、共用及基本虛擬主機方案模式提供的偵錯工具支援，每個應用程式僅限 1 個同時連線。
- 對於標準虛擬主機方案模式提供的偵錯工具支援，每個應用程式可擁有 5 個同時連線。

<a name="OtherFeatures"></a>
## 其他功能

**Web 端點監視**

- 基本和標準虛擬主機方案模式提供 Web 端點監視功能。如需 Web 端點監視的詳細資訊，請參閱[如何監視網站](http://azure.microsoft.com/documentation/articles/web-sites-monitor/)。

- 如需虛擬主機方案其他所有功能的詳細資訊，包括所有使用者 (包括開發人員) 關心的定價和功能，請參閱[網站定價詳細資料](http://azure.microsoft.com/pricing/details/web-sites/)。

<a name="Next Steps"></a>	
## 後續步驟

- 若要開始使用 Azure，請參閱 [Microsoft Azure 免費試用](http://azure.microsoft.com/pricing/free-trial/)。

- 如需定價、支援及 SLA 的相關資訊，請參閱以下連結：
	
	[資料傳輸定價詳細資料](http://azure.microsoft.com/pricing/details/data-transfers/)
	
	[Microsoft Azure 支援方案](http://azure.microsoft.com/support/plans/)
	
	[服務等級協定](http://azure.microsoft.com/support/legal/sla/)
	
	[SQL Database 定價詳細資料](http://azure.microsoft.com/pricing/details/sql-database/)
	
	[Microsoft Azure 的虛擬機器和雲端服務大小][vmsizes]
	
	[網站定價詳細資料](http://azure.microsoft.com/pricing/details/web-sites/)
	
	[網站定價詳細資料 - SSL 連線](http://azure.microsoft.com/pricing/details/web-sites/#ssl-connections)

- 如需 Azure 網站最佳做法 (包括建置可調整且具彈性的架構) 的相關資訊，請參閱[最佳做法：Windows Azure 網站 (WAWS)](http://blogs.msdn.com/b/windowsazure/archive/2014/02/10/best-practices-windows-azure-websites-waws.aspx)。

- 調整 Azure 網站規模的影片：
	
	[何時該調整 Azure 網站規模，與 Stefan Schackow](http://azure.microsoft.com/documentation/videos/azure-web-sites-free-vs-standard-scaling/)
	
	[自動調整 Azure 網站規模、CPU 或排程，與 Stefan Schackow](http://azure.microsoft.com/documentation/videos/auto-scaling-azure-web-sites/)

	[如何調整 Azure 網站規模，與 Stefan Schackow](http://azure.microsoft.com/documentation/videos/how-azure-web-sites-scale/)



<!-- LINKS -->
[vmsizes]:http://go.microsoft.com/fwlink/?LinkId=309169
[SQLaccountsbilling]:http://go.microsoft.com/fwlink/?LinkId=234930
[azuresubscriptions]:http://go.microsoft.com/fwlink/?LinkID=235288
[portal]: https://manage.windowsazure.com/

<!-- IMAGES -->
[SelectWebsite]: ./media/web-sites-scale/01SelectWebSite.png
[SelectScaleTab]: ./media/web-sites-scale/02SelectScaleTab.png

[ChooseWHP]: ./media/web-sites-scale/03aChooseWHP.png
[ChooseBasicInstanceSize]: ./media/web-sites-scale/03bChooseBasicInstanceSize.png
[ChooseBasicInstanceCount]: ./media/web-sites-scale/04ChooseBasicInstanceCount.png
[SaveButton]: ./media/web-sites-scale/05SaveButton.png
[BasicComplete]: ./media/web-sites-scale/06BasicComplete.png
[ChooseStandard]: ./media/web-sites-scale/07ChooseStandard.png
[CapacitySectionStandard]: ./media/web-sites-scale/08CapacitySectionStandard.png
[ChooseInstanceSize]: ./media/web-sites-scale/09ChooseInstanceSize.png
[SetUpScheduleTimesButton]: ./media/web-sites-scale/10SetUpScheduleTimesButton.png
[SetUpScheduleTimesDialog]: ./media/web-sites-scale/11SetUpScheduleTimesDialog.png
[EditScaleSettingsForSchedule]: ./media/web-sites-scale/12EditScaleSettingsForSchedule.png
[ScaleByMetric]: ./media/web-sites-scale/13ScaleByMetric.png
[InstanceCount]: ./media/web-sites-scale/14InstanceCount.png
[TargetCPU]: ./media/web-sites-scale/15TargetCPU.png
[LinkedResources]: ./media/web-sites-scale/16LinkedResources.png
[ScaleDatabase]: ./media/web-sites-scale/17ScaleDatabase.png


<!--HONumber=42-->
