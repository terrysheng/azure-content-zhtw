<properties title="How to Scale Websites" pageTitle="How to Scale Websites" description="required" metaKeywords="scaling Azure websites" services="web-sites" solutions="web" documentationCenter="" authors="cephalin" manager="wpickett" editor="mollybos" videoId="" scriptId="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="cephalin" />

# 如何調整網站

為了提升 Microsoft Azure 中網站的效能和輸送量，您可以使用 Azure 管理入口網站將「免費」的 Web 主控方案模式調整為「共用」、「基本」或「標準」。

擴大 Azure 網站涉及兩件相關的事務：將 Web 主控方案模式變更為較高的服務等級，和在切換為較高的服務等級後配置幾項設定。本文章涵蓋以上兩個主題。較高的服務層級 (如「標準」模式) 能讓您以更健全、更彈性的方式決定 Azure 中的資源使用情況。

模式的變更和設定可在管理入口網站的 [調整] 索引標籤中輕易完成。您可以視需要擴大或縮小。這些變更只需要幾秒鐘的時間便能完成套用，且影響範圍遍及 Web 主控方案內的所有網站。您不需要變更程式碼或重新部署應用程式。

如需 Web 主控方案的相關資訊，請參閱[什麼是 Web 主控方案？][什麼是 Web 主控方案？]和 [Azure 網站 Web 主控方案深入概觀][Azure 網站 Web 主控方案深入概觀]。如需各 Web 主控方案之定價和功能的相關資訊，請參閱[網站定價詳細資料][網站定價詳細資料]。

> [WACOM.NOTE] 將網站從 [免費] Web 主控方案模式切換到 [基本] 或 [標準] Web 主控方案模式之前，您必須先移除網站訂閱的支出上限。若要檢視或變更 Microsoft Azure 網站訂閱的選項，請參閱 [Microsoft Azure 訂閱][Microsoft Azure 訂閱]。

本文內容：

-   [調整為共用或基本方案模式][調整為共用或基本方案模式]
-   [調整為標準方案模式][調整為標準方案模式]
-   [調整與網站連接的 SQL Server Database][調整與網站連接的 SQL Server Database]
-   [開發人員功能][開發人員功能]
-   [其他功能][其他功能]

<a name="scalingsharedorbasic"></a>
<!-- ===================================== -->

## 調整為共用或基本方案模式

<!-- ===================================== -->

1.  在瀏覽器中開啟[管理入口網站][管理入口網站]。

2.  在 [網站] 索引標籤中，選取您的網站。

    ![選取網站][選取網站]

3.  按一下 [調整] 索引標籤。

    ![調整索引標籤][調整索引標籤]

4.  在 [Web Hosting Plan Mode] 區段中選擇 [共用] 或 [基本]。影像中的範例選擇 [基本]。

    ![選擇 Web 主控方案][選擇 Web 主控方案]

    [Web Hosting Plan Sites] 區段會顯示採用目前方案的簡短網站清單。所有採用目前方案的網站都將變更為您選取的 Web 主控方案模式。

5.  在 [容量] 區段中，選擇 [執行個體大小]可用的選項包括 [小型]、[中型] 或 [大型]。共用模式未提供執行個體大小選項。如需這些執行個體大小的詳細資訊，請參閱 [Windows Azure 的虛擬機器和雲端服務大小][Windows Azure 的虛擬機器和雲端服務大小]。

    ![基本模式的執行個體大小][基本模式的執行個體大小]

6.  使用滑桿來選擇所需的 [執行個體計數]。

    ![基本模式的執行個體計數][基本模式的執行個體計數]

7.  在命令列中選擇 [儲存]。

    ![Save button][Save button]

    > [WACOM.NOTE] 如果需要的話，您可以個別設定和儲存 [Web Hosting Plan]、[執行個體大小] 及 [執行個體計數] 設定。

8.  確認訊息會提醒您採用之 Web 主控方案與目前網站相同的網站也會變更為新模式。選擇 [是] 以完成變更。

    在範例中，方案模式已變更為 [基本]。

    ![方案變更完成][方案變更完成]

<a name="scalingstandard"></a>
<!-- ================================= -->

## 調整為標準方案模式

<!-- ================================= -->

> [WACOM.NOTE] 在將 Web 主控方案切換為 [標準] 模式之前，您應該先移除 Microsoft Azure 網站訂閱的支出上限，以免帳單期間還未結束，網站就因為已達支出上限而變得無法使用。若要檢視或變更 Microsoft Azure 網站訂閱的選項，請參閱 [Microsoft Azure 訂閱][Microsoft Azure 訂閱]。

1.  若要調整為標準，請遵循與調整為共用或基本模式相同的初始步驟，然後將 [Web Hosting Plan Mode] 選為 [標準]。

    ![選擇標準方案][選擇標準方案]

    如同先前所述，[Web Hosting Plan Sites] 區段會顯示採用目前方案的簡短網站清單。在這種情況下，所有採用目前方案的網站均會變更為標準模式。

2.  選取 [標準]，展開 [容量] 區段以顯示 [執行個體大小] 和 [執行個體計數] 選項 (基本模式亦提供這些選項)。唯有標準模式提供 [Edit Scale Settings for Schedule] 和 [Scale by Metric] 選項。

    ![標準模式的容量區段][標準模式的容量區段]

3.  設定 [執行個體大小]。可用的選項包括 [小型]、[中型] 或 [大型]。

    ![選擇執行個體大小][選擇執行個體大小]

    如需這些執行個體大小的詳細資訊，請參閱 [Windows Azure 的虛擬機器和雲端服務大小][Windows Azure 的虛擬機器和雲端服務大小]。

4.  如果您想要根據日間和夜間、工作日和週末及/或特定日期和時間來自動調整資源，請在 [Edit Scale Settings for Schedule] 選項中選擇 [Set up schedule times]。

    ![設定排程時間][設定排程時間]

5.  [Set up schedule times] 對話方塊提供許多有用的組態選擇。

    ![SetUpScheduleTimesDialog][SetUpScheduleTimesDialog]

6.  在 [Recurring Schedules] 下方選取 [Differing scale between Day and Night] 和/或 [Differing Scale between Weekday and Weekend]，可根據日間和夜間排程及/或個別的工作日和週末排程來調整資源。

    > [WACOM.NOTE] 為因應此項功能的目的，週末的開始時間為星期五結束時 (預設為下午 8:00)，結束時間為星期一開始時 (預設為上午 8:00)。週末設定檔使用的一天開始時間和結束時間與您在 [時間] 設定中的定義相同。

7.  在 [時間] 下方，選擇一天的開始和結束時間 (以半小時為單位) 及時區。依預設，一天的開始時間為上午 8:00，結束時間為下午 8:00。日光節約時間取決於您選取的時區。

8.  在 [Specific Dates] 下方，您可以建立一或多個具名的時間範圍來調整資源。例如，對於可能會產生大量 Web 流量的銷售或發表活動，您也許會想要在活動進行期間提供額外的資源。

9.  選擇完畢之後，請按一下 [確定] 來關閉 [排程時間] 對話方塊。

10. [Edit Scale Settings for Schedule] 方塊現在會根據您的變更顯示可設定的排程或事件。您可以選取一或多個週期性排程或特定的日期來予以設定。

    ![編輯排程的擴充設定][編輯排程的擴充設定]

    您現在可以使用 [依照度量調整] 和 [執行個體計數] 功能來針對每個選擇的排程微調資源調整。

11. 若要在網站負載變更時動態地調整網站使用的執行個體數目，請選擇 [CPU] 來啟用 [依照度量調整] 選項。

    ![依照度量調整][依照度量調整]

    圖表顯示在過去一週內使用的執行個體數目。您可以使用圖表來監視調整活動。

12. [Scale by Metric] 會修改 [執行個體計數] 功能，因此您可以設定要用於自動調整的虛擬機器數目下限和上限。Azure 一律會避免超出或低於您設定的限制。

    ![執行個體計數][執行個體計數]

13. [Scale by Metric] 也會啟用 [目標 CPU] 選項，以便您指定 CPU 使用量的目標範圍。此範圍代表網站的平均 CPU 使用率。Windows Azure 會增加或移除標準執行個體，以將網站維持在此範圍之內。

    ![目標 CPU][目標 CPU]

    **注意**：啟用 [依照度量調整] 時，Microsoft Azure 會每隔五分鐘檢查網站的 CPU 一次，然後在該時間點內視需要增加執行個體。如果 CPU 使用率較低，Microsoft Azure 會每隔兩小時移除執行個體一次，確保網站維持一定效能。一般說來，將執行個體計數下限設定為 1 是合適的值。然而，如果網站突然湧入大量的使用量，請確保您的執行個體數目下限足以處理負載。例如，如果網站在 Microsoft Azure 檢查 CPU 使用率之前的 5 分鐘間隔內突然湧入大量流量，網站在該段時間內可能會處於沒有回應的狀態。如果您已預知會突然湧入大量流量，請設定較高的執行個體計數下限來因應突發的流量。

14. 完成 [Edit Scale Settings for Schedule] 清單中的項目變更後，請按一下頁面底部之命令列中的 [儲存] 圖示來一次儲存所有排程設定 (您不需要分別儲存每個排程)。

> [WACOM.NOTE] 在 [Azure 預覽入口網站][Azure 預覽入口網站] 中，您不僅可以調整 CPU 百分比，還能調整其他 [記憶體百分比]、[磁碟佇列長度]、[HTTP 佇列長度]、[資料輸入] 及 [資料輸出] 的度量。您也可以建立一或多個擴大和縮小規則，以便讓您可以調整更多自訂控制。如需詳細資訊，請參閱 Azure 預覽入口網站文件中的[如何調整網站][如何調整網站]。

<a name="ScalingSQLServer"></a>

## 調整與網站連接的 SQL Server Database

如果您有一或多個與網站連接的 SQL Server 資料庫 (不論 Web 主控方案模式為何)，這些資料庫會列示於 [調整] 頁面底部的 [連結的資源] 區段中。

1.  若要調整某一個資料庫，請在 [Linked Resources] 區段中按一下資料庫名稱旁的 [Manage scale for this database] 連結。

    ![連結的資料庫][連結的資料庫]

2.  此連結會帶您前往 Azure 管理入口網站的 [SQL Server] 索引標籤，供您設定資料庫的 [版本] 和 [最大大小]。

    ![調整 SQL Server Database][調整 SQL Server Database]

    對於 [版本]，請根據所需的儲存體容量選擇 [Web] 或 [Business]。[Web] 版本提供一系列較小的容量，而 [Business] 版本則提供一系列較大的容量。

    您選擇的 [最大大小] 值能指定資料庫的上限。我們收取的資料庫費用乃是以實際儲存的資料量為準，因此變更 [最大大小] 屬性本身並不會影響資料庫費用。如需詳細資訊，請參閱 [Windows Azure SQL Database 中的帳戶和計費][Windows Azure SQL Database 中的帳戶和計費]。

<a name="devfeatures"></a>

## 開發人員功能

視 Web 主控方案模式而定，可以使用下列以開發人員為導向的功能：

**位元**

-   基本和標準等方案模式支援 64 和 32 位元的應用程式。
-   免費和共用等方案模式僅支援 32 位元的應用程式。

**偵錯工具支援**

-   對於免費、共用及基本等 Web 主控方案模式提供的偵錯工具支援，每個應用程式僅限 1 個同時連線。
-   對於標準 Web 主控方案模式提供的偵錯工具支援，每個應用程式可擁有 5 個同時連線。

<a name="OtherFeatures"></a>

## 其他功能

**Web 端點監視**

-   基本和標準等 Web 主控方案模式提供 Web 端點監視功能。如需 Web 端點監視的詳細資訊，請參閱[如何監視網站][如何監視網站]。

-   如需 Web 主控方案其他所有功能的詳細資訊，包括所有使用者 (包括開發人員) 關心的定價和功能，請參閱[網站定價詳細資料][網站定價詳細資料]。

<a name="Next Steps"></a>

## 後續步驟

-   若要開始使用 Azure，請參閱 [Microsoft Azure 免費試用][Microsoft Azure 免費試用]。

-   如需定價、支援及 SLA 的相關資訊，請參閱以下連結：

    [資料傳輸定價詳細資料][資料傳輸定價詳細資料]

    [Azure 支援方案][Azure 支援方案]

    [服務等級協定][服務等級協定]

    [SQL Database 定價詳細資料][SQL Database 定價詳細資料]

    [Windows Azure 的虛擬機器和雲端服務大小][Windows Azure 的虛擬機器和雲端服務大小]

    [網站定價詳細資料][網站定價詳細資料]

    [網站定價詳細資料 - SSL 連線][網站定價詳細資料 - SSL 連線]

-   如需 Azure 網站最佳作法 (包括組建可調整且彈性的架構) 的相關資訊，請參閱[最佳作法：Azure 網站 (WAWS)][最佳作法：Azure 網站 (WAWS)] (英文)。

-   調整 Azure 網站的影片：

    [何時該調整 Azure 網站，與 Stefan Schackow][何時該調整 Azure 網站，與 Stefan Schackow]

    [自動調整 Azure 網站、CPU 或排程，與 Stefan Schackow][自動調整 Azure 網站、CPU 或排程，與 Stefan Schackow]

    [如何調整 Azure 網站，與 Stefan Schackow][如何調整 Azure 網站，與 Stefan Schackow]

<!-- LINKS --> 
<!-- IMAGES -->

  [什麼是 Web 主控方案？]: http://azure.microsoft.com/zh-TW/documentation/articles/web-sites-web-hosting-plan-overview/
  [Azure 網站 Web 主控方案深入概觀]: http://www.azure.microsoft.com/zh-TW/Documentation/Articles/azure-web-sites-web-hosting-plans-in-depth-overview/
  [網站定價詳細資料]: http://www.windowsazure.com/zh-TW/pricing/details/web-sites/
  [Microsoft Azure 訂閱]: http://go.microsoft.com/fwlink/?LinkID=235288
  [調整為共用或基本方案模式]: #scalingsharedorbasic
  [調整為標準方案模式]: #scalingstandard
  [調整與網站連接的 SQL Server Database]: #ScalingSQLServer
  [開發人員功能]: #devfeatures
  [其他功能]: #OtherFeatures
  [管理入口網站]: https://manage.windowsazure.com/
  [選取網站]: ./media/web-sites-scale/01SelectWebSite.png
  [調整索引標籤]: ./media/web-sites-scale/02SelectScaleTab.png
  [選擇 Web 主控方案]: ./media/web-sites-scale/03aChooseWHP.png
  [Windows Azure 的虛擬機器和雲端服務大小]: http://go.microsoft.com/fwlink/?LinkId=309169
  [基本模式的執行個體大小]: ./media/web-sites-scale/03bChooseBasicInstanceSize.png
  [基本模式的執行個體計數]: ./media/web-sites-scale/04ChooseBasicInstanceCount.png
  [Save button]: ./media/web-sites-scale/05SaveButton.png
  [方案變更完成]: ./media/web-sites-scale/06BasicComplete.png
  [選擇標準方案]: ./media/web-sites-scale/07ChooseStandard.png
  [標準模式的容量區段]: ./media/web-sites-scale/08CapacitySectionStandard.png
  [選擇執行個體大小]: ./media/web-sites-scale/09ChooseInstanceSize.png
  [設定排程時間]: ./media/web-sites-scale/10SetUpScheduleTimesButton.png
  [SetUpScheduleTimesDialog]: ./media/web-sites-scale/11SetUpScheduleTimesDialog.png
  [編輯排程的擴充設定]: ./media/web-sites-scale/12EditScaleSettingsForSchedule.png
  [依照度量調整]: ./media/web-sites-scale/13ScaleByMetric.png
  [執行個體計數]: ./media/web-sites-scale/14InstanceCount.png
  [目標 CPU]: ./media/web-sites-scale/15TargetCPU.png
  [Azure 預覽入口網站]: https://portal.azure.com/
  [如何調整網站]: http://azure.microsoft.com/zh-TW/documentation/articles/insights-how-to-scale/
  [連結的資料庫]: ./media/web-sites-scale/16LinkedResources.png
  [調整 SQL Server Database]: ./media/web-sites-scale/17ScaleDatabase.png
  [Windows Azure SQL Database 中的帳戶和計費]: http://go.microsoft.com/fwlink/?LinkId=234930
  [如何監視網站]: http://www.windowsazure.com/zh-TW/documentation/articles/web-sites-monitor/
  [Microsoft Azure 免費試用]: http://azure.microsoft.com/zh-TW/pricing/free-trial/
  [資料傳輸定價詳細資料]: http://www.windowsazure.com/zh-TW/pricing/details/data-transfers/
  [Azure 支援方案]: http://www.windowsazure.com/zh-TW/support/plans/
  [服務等級協定]: http://www.windowsazure.com/zh-TW/support/legal/sla/
  [SQL Database 定價詳細資料]: http://www.windowsazure.com/zh-TW/pricing/details/sql-database/
  [網站定價詳細資料 - SSL 連線]: http://www.windowsazure.com/zh-TW/pricing/details/web-sites/#ssl-connections
  [最佳作法：Azure 網站 (WAWS)]: http://blogs.msdn.com/b/windowsazure/archive/2014/02/10/best-practices-windows-azure-websites-waws.aspx
  [何時該調整 Azure 網站，與 Stefan Schackow]: http://www.windowsazure.com/zh-TW/documentation/videos/azure-web-sites-free-vs-standard-scaling/
  [自動調整 Azure 網站、CPU 或排程，與 Stefan Schackow]: http://www.windowsazure.com/zh-TW/documentation/videos/auto-scaling-azure-web-sites/
  [如何調整 Azure 網站，與 Stefan Schackow]: http://www.windowsazure.com/zh-TW/documentation/videos/how-azure-web-sites-scale/
