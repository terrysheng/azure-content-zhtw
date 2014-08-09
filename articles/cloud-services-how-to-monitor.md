<properties  linkid="manage-services-how-to-monitor-a-cloud-service" urlDisplayName="How to monitor" pageTitle="How to monitor a cloud service - Azure" metaKeywords="Azure monitoring cloud services, Azure Management Portal cloud services" description="Learn how to monitor cloud services by using the Azure Management Portal." metaCanonical="" services="cloud-services" documentationCenter="" title="How to Monitor Cloud Services" authors="ryanwi" solutions="" manager="" editor="" />

# 如何監視雲端服務

[WACOM.INCLUDE [disclaimer](../includes/disclaimer.md)]

您可以監視您的雲端服務在 Azure 管理入口網站中的效能度量。您可以對於各個服務角色設定從最小到詳細的監視層級，並且可以自訂監視顯示。詳細資訊監視資料儲存在儲存體帳戶中，您可以從入口網站外部存取。

管理入口網站的監視顯示本身的可設定度相當高。您可以在 **監視** 頁面上的度量清單中選擇要監視的度量，而且可以在 **監視** 頁面和儀表板上的度量圖表選擇要繪製的度量。

## 目錄

* [概念](#concepts)
* [作法：設定雲端服務的監視](#verbose)
* [作法：接收雲端服務度量的警示](#receivealerts)
* [作法：將度量新增至度量表](#addmetrics)
* [作法：自訂度量圖表](#customizechart)
* [作法：從管理入口網站外部存取詳細資訊監視資料](#accessverbose)

<h2><a  id="concepts" ></a> 概念</h2>


依預設，對於使用從角色執行個體 (虛擬機器) 的主機作業系統收集的效能計數器進行的新雲端服務，將提供最小監視。最小度量僅限於 [CPU 百分比]、[資料輸入]、[資料輸出]、[磁碟讀取輸送量] 和 [磁碟寫入輸送量]。設定詳細資訊監視之後，即可收到以虛擬機器 (角色執行個體) 內的效能資料為基礎的其他度量。詳細度量能夠進一步分析應用程式作業期間發生的問題。

> [WACOM.NOTE] 如果您使用詳細資訊監視，可以在角色執行個體啟動時、透過診斷組態檔或使用 Azure 診斷 API
> 從遠端新增更多效能計數器。若要能夠在管理入口網站中監視這些度量，您必須先新增效能計數器，才能設定詳細資訊監視。如需詳細資訊，請參閱[使用
> Azure 診斷收集記錄資料][1]和[在 Azure
> 應用程式中建立及使用效能計數器][2]。

依預設，角色執行個體的效能計數器資料會每隔 3 分鐘取樣一次，並且從執行個體傳輸。您啟用詳細資訊監視時，將每隔 5 分鐘、1 小時和 12 小時對於各個角色執行個體和各個角色的角色執行個體彙總原始效能計數器資料。經過 10 天後將清除彙總的資料。

您啟用詳細資訊監視後，彙總的監視資料將儲存於您儲存體帳戶中的資料表。若要啟用角色的詳細資訊監視，您必須設定連結到儲存體帳戶的診斷連線字串。您可以對於不同的角色使用不同的儲存體帳戶。

請注意，啟用詳細資訊監視將增加資料儲存、資料傳輸和儲存交易相關的儲存成本。最小監視不需要儲存體帳戶。即使將監視層級設定為詳細資訊，在最小監視層級顯現的度量資料也不會儲存在您的儲存體帳戶中。

<h2><a  id="verbose" ></a>作法：設定雲端服務的監視</h2>


使用下列程序，在管理入口網站中設定詳細資訊或最小監視。啟用 Azure 診斷，並設定診斷連線字串啟用 Azure 診斷存取儲存體帳戶來儲存詳細資訊監視資料前，您無法啟動詳細資訊監視。

### 開始之前

* 建立儲存體帳戶儲存監視資料。您可以對於不同的角色使用不同的儲存體帳戶。如需詳細資訊，請參閱**儲存體帳戶**的說明，或參閱[如何建立儲存體帳戶](/en-us/manage/services/storage/how-to-create-a-storage-account/)。

* 對於雲端服務角色啟用 Azure 診斷。  
    
  您必須更新雲端服務定義檔 (.csdef) 和雲端服務組態檔 (.cscfg)。如需詳細資訊，請參閱[設定 Azure 診斷功能][3]。

在管理入口網站中，您可以新增或修改診斷連線字串，Azure 診斷使用該字串來存取儲存詳細資訊監視資料的儲存體帳戶，您也可以將監視層級設定為詳細資訊或最小。由於詳細資訊監視將資料儲存在儲存體帳戶中，因此您必須先設定診斷連線字串，才能將監視層級設定為詳細資訊。

### 設定詳細資訊監視的診斷連線字串

1.  對於將用來儲存詳細資訊監視資料的儲存體帳戶複製儲存體存取金鑰。在 [Azure 管理入口網站][4]中，您可以使用 **儲存體帳戶** 頁面上的 **管理金鑰**。如需詳細資訊，請參閱[如何管理雲端服務][5] (英文) 或參閱 **儲存體帳戶** 頁面的說明。

2.  開啟 **雲端服務**。然後，按一下要設定的雲端服務名稱，將儀表板開啟。

3.  按一下 **生產** 或 **預備** 顯示要設定的部署。

4.  按一下 **設定**。
    
    您將編輯 **設定** 頁面頂端的 **監視** 設定，如下所示。如果您尚未啟用雲端服務的 Azure 診斷，則無法使用 **層級** 選項。您無法變更資料保留原則。雲端服務的詳細資訊監視資料會儲存 10 天。
    
    ![監視選項](./media/cloud-services-how-to-monitor/CloudServices_MonitoringOptions.png)

5.  在 **Diagnostics Connection Strings** 中，對於要進行詳細資訊監視的各個角色填入診斷連線字串。
    
    連線字串的格式如下。(這是使用預設端點的雲端服務範例。)若要更新連線字串，請輸入要使用之儲存體帳戶的有效儲存體帳戶名稱和儲存體存取金鑰。

 	DefaultEndpointsProtocol=https;AccountName=Storag	eAccountName;AccountKey=StorageAccountKey

1. 按一下 **儲存**。

如果您要啟動詳細資訊監視，請先執行下一個程序，再設定服務角色的診斷連線字串。

### 將監視層級變更為詳細資訊或最小

1.  在**管理入口網站][4]中，開啟雲端服務部署的 [設定** 頁面。

2.  在 **層級** 中，按一下 **詳細資訊** 或 **最小**。

3.  按一下 **儲存**。

啟動詳細資訊監視之後，您應該在一小時內開始查看管理入口網站中的監視資料。

原始效能計數器資料和彙總的監視資料是儲存在角色的部署 ID 所保留的表格中出現的儲存體帳戶內。

<h2><a  id="receivealerts" ></a>作法：接收雲端服務度量的警示</h2>


您可以按照雲端服務監視度量接收警示。在 Azure 管理入口網站的 **Management Services** 頁面上，您可以建立當您選擇的度量達到指定值時觸發警示的規則。您也可以選擇在觸發警示時傳送電子郵件。如需詳細資訊，請參閱[如何：在 Azure 中接收警示通知及管理警示規則][6]。

<h2><a  id="addmetrics" ></a>作法：將度量新增至度量表</h2>


1.  在**管理入口網站][7]中，開啟雲端服務的 [監視** 頁面。
    
    依預設，度量表會顯示一部份的可用度量。該圖顯示雲端服務的預設詳細資訊度量，這僅限於在角色層集彙總資料的記憶體\\可用 MB 效能計數器。使用 **Add Metrics** 選取要在管理入口網站中監視的其他彙總和角色層級度量。
    
    ![詳細資訊顯示](./media/cloud-services-how-to-monitor/CloudServices_DefaultVerboseDisplay.png)

2.  若要將度量新增至度量表：
    
    a. 按一下 **Add Metrics** 開啟 **Choose Metrics**，如下所示。第一個可用的度量將展開，顯示可用的選項。對於各個度量，頂端選項將顯示所有角色的彙總監視資料。此外，您可以選擇要顯示資料的個別角色。
    
    ![新增度量](./media/cloud-services-how-to-monitor/CloudServices_AddMetrics.png)
    
    b. 若要選取要顯示的度量：
    
    * 按一下度量旁邊的向下箭頭，展開監視選項。
    * 選取要顯示的各個監視選項的核取方塊。
    
    在度量表中，最多可以顯示 50 個度量。
<div class="dev-callout">

<b>提示</b>
<p>在詳細資訊監視中，度量清單可包含許多度量。若要顯示捲軸，請移動游標停留在對話方塊的右邊。若要篩選清單，請按一下搜尋圖示，並且在搜尋方塊中輸入文字，如下所示。</p>
</div>
</div>

   ![Add metrics search](./media/cloud-services-how-to-monitor/CloudServices_AddMetrics_Search.png)

1.  完成選取度量後，按一下 [確定] (核取記號)。
    
    選取的度量隨即新增到度量表，如下所示。
    
    ![監視度量](./media/cloud-services-how-to-monitor/CloudServices_Monitor_UpdatedMetrics.png)

2. 若要從度量表刪除度量，請按一下度量加以選取，然後按一下 **Delete Metric**。(只有在您選取度量時，才會看見 **Delete Metric**。)

<h2><a  id="customizechart" ></a>作法：自訂度量圖表</h2>


1.  在度量表中，最多可選取 6 個度量在度量圖表上繪製。若要選取度量，請按一下左邊的核取方塊。若要移除度量圖表中的度量，請清除度量表中的核取方塊。
    
    您選取度量表中的度量時，度量將新增到度量表。在縮小顯示上，**n more** 下拉式清單包含無法完全顯示的度量標頭。

2.  若要切換顯示相對值 (各個度量的最終值) 和絕對值 (顯示的 Y 軸)，請選取圖表頂端的 [相對] 或 [絕對]。
    
    ![相對或絕對](./media/cloud-services-how-to-monitor/CloudServices_Monitor_RelativeAbsolute.png)

3.  若要變更度量圖表顯示的時間範圍，請選取圖表頂端的 [1 hour]、[24 小時] 或 [7 days]。
    
    ![監視顯示期間](./media/cloud-services-how-to-monitor/CloudServices_Monitor_DisplayPeriod.png)
    
    在儀表板度量圖表上，繪製圖表的方法並不相同。其中有一組標準的度量可供使用，而且選取度量標頭即可新增或移除度量。

### 自訂儀表板上的度量圖表

1.  開啟雲端服務的儀表板。

2.  在圖表中新增或移除度量：
    
    * 若要繪製新的度量，請在圖表標頭中選取度量的核取方塊。在縮小顯示上，按一下 ***n* metrics** 旁邊的向下箭頭，繪製圖表標頭區域無法顯示的度量。
    
    * 若要刪除圖表上繪製的度量，請清除標頭旁邊的核取方塊。

3.  切換**相對**和**絕對**顯示。

4.  選擇要顯示的 1 小時、24 小時或 7 天資料。

<h2><a  id="accessverbose" ></a>作法：從管理入口網站外部存取詳細資訊監視資料</h2>


詳細資訊監視資料儲存在您對於各個角色指定的儲存體帳戶內的表格中。對於各個雲端服務部署，將為角色建立 6 個表格。個別 (5 分鐘、1 小時和 12 小時) 建立 2 個表格。其中一個表格儲存角色層級彙總，另一個表格儲存角色執行個體的彙總。

表格名稱的格式如下：

    WAD*deploymentID*PT*aggregation_interval*[R|RI]Table

其中：

* *deploymentID* 是指派給雲端服務部署的 GUID

* *aggregation\_interval* = 5M、1H 或 12H

* 角色層級彙總 = R

* 角色執行個體彙總 = RI

例如，下表將儲存 1 小時間隔彙總的詳細資訊監視資料：

    WAD8b7c4233802442b494d0cc9eb9d8dd9fPT1HRTable (角色的每小時彙總)
    
    WAD8b7c4233802442b494d0cc9eb9d8dd9fPT1HRITable (角色執行個體的每小時彙總)



[1]: http://msdn.microsoft.com/zh-tw/library/gg433048.aspx
[2]: http://msdn.microsoft.com/zh-tw/library/hh411542.aspx
[3]: http://msdn.microsoft.com/zh-tw/library/windowsazure/dn186185.aspx
[4]: https://manage.windowsazure.com/
[5]: http://www.windowsazure.com/en-us/manage/services/cloud-services/how-to-manage-a-cloud-service/
[6]: http://go.microsoft.com/fwlink/?LinkId=309356
[7]: http://manage.windowsazure.com/