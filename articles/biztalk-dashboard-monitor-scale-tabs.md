<properties linkid="manage-services-biztalk-services-dashboard-monitor-scale-tabs" urlDisplayName="Dashboard, Monitor and Scale tabs" pageTitle="Dashboard, Monitor, and Scale in Biztalk Services | Azure" metaKeywords="BizTalk Services, Azure, dashboard, monitor, scale" description="Learn about the controls on the Management Portal tabs for BizTalk Services: Dashboard, Monitor, and Scale." metaCanonical="" services="biztalk-services" documentationCenter="" title=" Monitor and Scale tabs" authors="mandia" solutions="" manager="paulettm" editor="cgronlun" />

BizTalk 服務：儀表板、監視器和調整索引標籤
==========================================

第一次開啟 Azure 管理入口網站時會自動進入 **[所有項目]** 索引標籤。**[所有項目]** 索引標籤中的資料行可排序。若要檢視 BizTalk 服務，請在 **[所有項目]** 索引標籤中選取 BizTalk 服務，或按一下 **[BIZTALK 服務]** 索引標籤，然後按一下您的 BizTalk 服務名稱。

這樣會開啟新視窗並顯示下列選項：

-   [快速入門](#QuickStart)

-   [儀表板](#Dashboard)

-   [監視](#Monitor)

-   [調整](#Scale)

本主題說明這些索引標籤。

快速入門
--------

在 [快速入門] 索引標籤中，您可以執行下列動作：

<table border="1">
<tr bgcolor="FAF9F9">
        <td><strong>選項</strong></td>
        <td><strong>說明</strong></td>
</tr>
    <tr>
        <td>取得工具</td>

        <td>下載 BizTalk 服務 SDK，將 Visual Studio 專案範本安裝到內部部署開發電腦上。這些範本會建立 <strong>BizTalk 服務</strong> (橋接)，以及可部署至 BizTalk 服務的 <strong>BizTalk 服務成品</strong> (轉換) Visual Studio 專案。

        <br/><br/>
		＜<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302335">如何開始使用 Azure BizTalk 服務 SDK＞</a>和＜<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=241589">安裝 Azure BizTalk 服務 SDK</a>＞列出開始進行的步驟。
        </td>
    </tr>

    <tr>
        <td>建立夥伴協議</td>

        <td>開啟裝載於 Azure 的 Azure BizTalk 服務入口網站，以加入夥伴並建立 X12 和 AS2 EDI 協議。 

        <br/><br/>

        ＜<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=303653">在 BizTalk 服務入口網站上設定 EDI 訊息的元件</a>＞列出開始進行的步驟。 
        </td>
    </tr>

<tr>
        <td>深入了解 BizTalk 服務</td>

        <td>前往學習中心以深入了解 Azure BizTalk 服務。</td>
</tr>

</table>

在底部的工作列，您可以**「管理」** BizTalk 服務、檢視存取控制命名空間**「連接資訊」**、對儲存體帳戶執行**「同步金鑰」**，或**「刪除」** BizTalk 服務：

<table border="1">
<tr bgcolor="FAF9F9">
        <td><strong>選項</strong></td>
        <td><strong>說明</strong></td>
</tr>
<tr>
<td>管理</td>
<td>按一下 [管理] 會開啟 Azure BizTalk 服務入口網站。BizTalk 服務入口網站是 EDI 組態的入口，包括加入夥伴及建立 AS2 和 X12 協議。
<br/><br/>
這與 <strong>[快速入門]</strong> 索引標籤上的 <strong>[Create partner agreements]</strong> 相同。
<br/><br/>
＜<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=303653">在 BizTalk 服務入口網站上設定 EDI 訊息的元件</a>＞提供 BizTalk 服務入口網站的詳細資訊。</td>
</tr>

<tr>
<td>連接資訊</td>
<td>按一下 [連接資訊] 時會顯示 [Access Control Namespace]、[預設簽發者] 和 [預設金鑰]。您可以複製這些值。
<br/><br/>
您也可以開啟存取控制管理入口網站。此存取控制管理入口網站就與使用左導覽窗格中的 Active Directory 選項一樣。
<br/><br/>
＜<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=285670">管理 ACS 命名空間</a>＞提供存取控制管理入口網站的詳細資訊。</td>
</tr>

<tr>
<td>同步金鑰</td>
<td>當建立儲存體帳戶時，會自動建立主要金鑰和次要金鑰。這些金鑰可控制儲存體帳戶的存取。BizTalk 服務會自動使用主要金鑰。<strong>同步金鑰</strong> 可讓使用者在主要金鑰和次要金鑰之間切換，而不會中斷 BizTalk 服務。
<br/><br/>
例如，您希望 BizTalk 服務對儲存體帳戶使用新的主要金鑰。作法： 
<br/><br/>
<ol>
<li>按一下 BizTalk 服務，再按一下 <strong>[Sync Keys]</strong> 。選取次要金鑰。這時，BizTalk 服務就會開始使用次要金鑰。</li>
<li>在 Azure 管理入口網站中，按一下儲存體帳戶並 [重新產生] 主要金鑰。記住，BizTalk 服務現在使用次要金鑰。</li>
<li>按一下 BizTalk 服務，再按一下 <strong>[Sync Keys]</strong> 。現在，選取主要金鑰。這是您重新產生的新主要金鑰。</li>
<li>在 Azure 管理入口網站中，按一下儲存體帳戶並 [重新產生] 次要金鑰。</li>
</ol>
<br/>
此程序稱為「換用金鑰」。目的是讓使用者在主要金鑰和次要金鑰之間切換，而不會中斷 BizTalk 服務。
</tr>

<tr>
<td>刪除</td>
<td>按一下 [刪除] 會移除 BizTalk 服務及已部署到它的所有項目。</td>
</tr>
</table>

儀表板
------

按一下 BizTalk 服務名稱時會顯示 [儀表板] 索引標籤。[儀表板] 會顯示下列資訊：

#### 度量圖

此圖表顯示固定的效能度量清單。這些度量提供 BizTalk 服務健全狀況的即時值。度量包括：

-   CPU 使用率
-   來源處失敗
-   處理時失敗
-   已處理的訊息
-   已接收的訊息
-   處理延遲

如需這些效能度量的說明，請移至本主題的＜[可用的度量](#Metrics)＞。

##### 相對或絕對

此圖表會顯示趨勢，但只顯示每個度量的目前值，也就是 **[相對]** 選項。若要顯示 Y 軸來查看絕對值，請選取 **[絕對]**。

##### 間隔

修改圖表上顯示的度量時間範圍。選項包括：

-   1 小時
-   1 天
-   7 天

#### 快速概覽

列出 BizTalk 服務屬性，包括：

<table border="1">
<tr bgcolor="FAF9F9">
        <td><strong>選項</strong></td>
        <td><strong>說明</strong></td>
</tr>
<tr>
<td>更新追蹤資料庫認證</td>
<td>變更用於登入追蹤資料庫的使用者名稱和密碼。<br/><br/>
佈建 BizTalk 服務時，您需要輸入使用者名稱和密碼，以登入追蹤資料庫。此選項可讓您修改 BizTalk 服務，以使用不同的使用者名稱和密碼來登入追蹤資料庫。<br/><br/>
＜<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302280">BizTalk 服務：使用 Azure 管理入口網站進行佈建</a>＞ 列出佈建 BizTalk 服務的步驟。</td>
</tr>
<tr>
<td>更新 SSL 憑證</td>
<td>可以輸入不同的 SSL 憑證。<br/><br/>
佈建 BizTalk 服務時會自動建立自我簽署 SSL 憑證。此選項可讓您修改 BizTalk 服務來使用不同的 SSL 憑證。<br/><br/>
＜<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302280">BizTalk 服務：使用 Azure 管理入口網站進行佈建</a>＞ 列出佈建 BizTalk 服務的步驟。</td>
</tr>
<tr>
<td>下載憑證</td>
<td>此選項可讓您下載 BizTalk 服務所使用的 SSL 憑證。<br/><br/>
＜<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302280">BizTalk 服務：使用 Azure 管理入口網站進行佈建</a>＞ 列出佈建 BizTalk 服務的步驟。</td>
</tr>
<tr>
<td>Status</td>
<td>顯示 BizTalk 服務的目前狀態。</td>
</tr>
<tr>
<td>服務 URL</td>
<td>此 URL 可前往您的 BizTalk 服務。這與佈建您的 BizTalk 服務時所輸入的<strong>網域 URL</strong> 相同。 </td>
</tr>
<tr>
<td>公用虛擬 IP (VIP) 位址</td>
<td>此 IP 位址指派給您的 BizTalk 服務。它用於所有輸入端點，也是輸出流量的來源位址。此 IP 位址屬於您已佈建的 BizTalk 服務。如果刪除 BizTalk 服務，則此 IP 位址會指派給其他服務部署。</td>
</tr>
<tr>
<td>ACS 命名空間</td>
<td>向 BizTalk 服務驗證。這與佈建 BizTalk 服務時所輸入的 <strong>ACS 命名空間相同</strong>。</td>
</tr>
<tr>
<td>版本</td>
<td>列出版本。選項包括開發人員、基本、標準和高級。這與佈建 BizTalk 服務時所輸入的版本相同。<br/><br/>
＜<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302281">BizTalk 服務：開發人員、基本、標準和高級版本圖表</a>＞ lists the edition differences, including costs.</td>
</tr>
<tr>
<td>位置</td>
<td>顯示裝載 BizTalk 服務的地理區域。這與佈建 BizTalk 服務時所輸入的<strong>區域</strong>相同。</td>
</tr>
<tr>
<td>建立時間</td>
<td>顯示佈建 BizTalk 服務的日期和時間。</td>
</tr>
<tr>
<td>追蹤資料庫</td>
<td>儲存 BizTalk 服務所使用之追蹤資料表的 Azure SQL Database 名稱。這與佈建 BizTalk 服務時所輸入的<strong>追蹤資料庫相同</strong>。</td>
</tr>
<tr>
<td>監視/封存儲存體</td>
<td>儲存 BizTalk 服務之監視輸出的 Azure 儲存體帳戶名稱。這與佈建 BizTalk 服務時所輸入的<strong>監視/封存儲存體帳戶相同</strong>。</td>
</tr>
<tr>
<td>訂閱名稱</td>
<td>訂閱掌管對 Azure 管理入口網站的存取權。這與佈建 BizTalk 服務時所選取的<strong>訂閱</strong>名稱相同。</td>
</tr>
<tr>
<td>訂閱識別碼</td>
<td>訂閱掌管對 Azure 管理入口網站的存取權。建立訂閱時會自動產生訂閱識別碼。使用 REST API 時，您可能需要輸入訂閱識別碼。</td>
</tr>
</table>

＜[BizTalk 服務：使用 Azure 管理入口網站進行佈建](http://go.microsoft.com/fwlink/p/?LinkID=302280)＞列出佈建 BizTalk 服務的步驟。

#### 管理、連接資訊、同步金鑰和刪除

在底部的工作列，您可以**「管理」** BizTalk 服務、檢視存取控制命名空間**「連接資訊」**、對儲存體帳戶執行**「同步金鑰」**，或**「刪除」** BizTalk 服務：

<table border="1">
<tr bgcolor="FAF9F9">
        <td><strong>選項</strong></td>
        <td><strong>說明</strong></td>
</tr>
<tr>
<td>管理</td>
<td>按一下 [管理] 會開啟 Azure BizTalk 服務入口網站。BizTalk 服務入口網站是 EDI 組態的入口，包括加入夥伴及建立 AS2 和 X12 協議。
<br/><br/>
這與 <strong>[快速入門]</strong> 索引標籤上的 <strong>[Create partner agreements]</strong> 相同。
<br/><br/>
＜<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=303653">在 BizTalk 服務入口網站上設定 EDI 訊息的元件</a>＞ 提供 BizTalk 服務入口網站的詳細資訊。</td>
</tr>
<tr>
<td>連接資訊</td>
<td>按一下 [連接資訊] 時會顯示 [Access Control Namespace]、[預設簽發者] 和 [預設金鑰]。您可以複製這些值。
<br/><br/>
您也可以開啟存取控制管理入口網站。此存取控制管理入口網站就與使用左導覽窗格中的 Active Directory 選項一樣。
<br/><br/>
＜<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=285670">管理 ACS 命名空間</a>＞ 提供存取控制管理入口網站的詳細資訊。</td>
</tr>
<tr>
<td>同步金鑰</td>
<td>當建立儲存體帳戶時，會自動建立主要金鑰和次要金鑰。這些金鑰可控制儲存體帳戶的存取。BizTalk 服務會自動使用主要金鑰。<strong>同步金鑰</strong>可讓使用者在主要金鑰和次要金鑰之間切換，而不會中斷 BizTalk 服務。
<br/><br/>
例如，您希望 BizTalk 服務對儲存體帳戶使用新的主要金鑰。作法： 
<br/><br/>
<ol>
<li>按一下 BizTalk 服務，再按一下 <strong>[Sync Keys]</strong>。選取次要金鑰。這時，BizTalk 服務就會開始使用次要金鑰。</li>
<li>在 Azure 管理入口網站中，按一下儲存體帳戶並 [重新產生] 主要金鑰。記住，BizTalk 服務現在使用次要金鑰。</li>
<li>按一下 BizTalk 服務，再按一下 <strong>[Sync Keys]</strong>。現在，選取主要金鑰。這是您重新產生的新主要金鑰。</li>
<li>在 Azure 管理入口網站中，按一下儲存體帳戶並 [重新產生] 次要金鑰。</li>
</ol>
<br/>
此程序稱為「換用金鑰」。目的是讓使用者在主要金鑰和次要金鑰之間切換，而不會中斷 BizTalk 服務。</td>
</tr>

<tr>
<td>刪除</td>
<td>按一下 [刪除] 會移除 BizTalk 服務及已部署到它的所有項目。</td>
</tr>
</table>

監視
----

[監視] 索引標籤會顯示下列資訊：

#### 度量圖

此圖表顯示已選取的效能度量。這些度量提供 BizTalk 服務健全狀況的即時值。請選擇要顯示哪些效能度量。一次最多同時顯示六個效能度量。

##### 相對或絕對

此圖表會顯示趨勢，但只顯示每個度量的目前值，也就是 **[相對]** 選項。若要顯示 Y 軸來查看絕對值，請選取 **[絕對]**。

##### 間隔

修改圖表上顯示的度量時間範圍。選項包括：

-   1 小時
-   1 天
-   7 天

#### 在圖表中移除或顯示度量

> 1.  按一下 **[監視]** 索引標籤。
> 2.  按一下工作列的 **[Add Metrics]**：<br/>
>     ![Click Add Metrics](./media/biztalk-dashboard-monitor-scale-tabs/WABS_AddMetrics.png)
> 3.  勾選您要顯示在 **[監視]** 索引標籤上的效能度量。
> 4.  按一下勾選記號以回到 **[監視]** 索引標籤。
> 5.  按一下度量旁邊的圓圈，將該度量的值顯示在圖表中。
> 
>     例如，**[CPU 使用率]** 度量呈現灰色，其輸出不會出現在圖表中：
>     
>     ![CPU Usage metric is grayed out](./media/biztalk-dashboard-monitor-scale-tabs/WABS_GrayedMetric.png)
><br/>
>     按一下灰色圓圈可將 **[CPU 使用率]** 度量的輸出顯示在圖表中：
>     
>     ![CPU Usage metric is enabled](./media/biztalk-dashboard-monitor-scale-tabs/WABS_EnabledMetric.png)

> 1.  若要從顯示圖表和清單中移除度量，請按一下工作列的 **[刪除度量]**。按一下 **[刪除度量]** 會從 [監視] 索引標籤中移除度量。若要將度量加回到清單中，請按一下工作列的 **[Add Metrics]**，勾選度量，然後按一下勾選記號以回到 **[監視]** 索引標籤。按一下灰色圓圈讓度量出現在圖表中。

可用的度量
----------

以下是可用的效能計數器/度量：

<table border="1">
<tr bgcolor="FAF9F9">
<td><strong>度量</strong></td>
<td><strong>說明</strong></td>
</tr>
<tr>
<td>往返延遲</td>
<td>此效能度量顯示從收到訊息起，到 BizTalk 服務跨所有橋接器完全處理完為止，處理訊息所花費的平均時間，以毫秒為單位 (ms)。只會計算成功處理的訊息。<br/><br/>
發生下列事件時會建立時間戳記： 
<ul>
<li>訊息進入閘道</li>
<li>訊息已路由傳送至目的地</li>
<li>收到目的地回應</li>
<li>目的地認可回應已傳送至閘道</li>
</ul>
<br/>
此度量顯示下列計算的結果：
<br/><br/>
 [目的地認可回應已傳送至閘道] - [訊息進入閘道]</td>
</tr>
<tr>
<td>來源處失敗</td>
<td>此效能度量顯示從來源端點提取訊息時，BizTalk 服務無法處理的訊息總數。</td>
</tr>
<tr>
<td>CPU 使用率</td>
<td>列出所有角色執行個體的平均 % 處理器時間。</td>
</tr>
<tr>
<td>處理延遲</td>
<td>此效能度量顯示 BizTalk 服務跨所有橋接器來處理某個訊息所花費的平均時間，不包括在目的地所花費的時間，以毫秒為單位 (ms)。只會計算成功處理的訊息。<br/><br/>
發生下列每一個事件時會建立時間戳記： 

<ul>
<li>訊息進入閘道</li>
<li>訊息已路由傳送至目的地</li>
<li>收到目的地回應</li>
<li>目的地認可回應已傳送至閘道</li>
</ul>
<br/>此度量顯示下列計算的結果：<br/><br/>
 [目的地認可回應已傳送至閘道] - [訊息進入閘道] - [收到目的地回應] + [訊息已路由傳送至目的地]</td>
</tr>
<tr>
<td>處理時失敗</td>
<td>此效能度量顯示在一段時間內，BizTalk 服務跨所有橋接器處理訊息時失敗的訊息總數。</td>
</tr>
<tr>
<td>已傳送的訊息</td>
<td>此效能度量顯示在一段時間內，BizTalk 服務跨所有橋接器傳送的訊息總數。當管線傳送的訊息抵達路由目的地時，此度量會遞增。此度量不表示已成功處理訊息。<br/><br/>
在要求回覆案例中，當路由目的地將收到認可傳回至管線時，此度量會遞增。</td>
</tr>
<tr>
<td>已接收的訊息</td>
<td>此效能度量顯示在一段時間內，BizTalk 服務跨所有橋接器收到的訊息總數。當管線收到新的訊息時，此度量會遞增。</td>
</tr>
<tr>
<td>處理中的訊息</td>
<td>此效能度量顯示 BizTalk 服務在一段時間內正在處理的訊息總數。</td>
</tr>
<tr>
<td>已處理的訊息</td>
<td>此效能度量顯示在一段時間內，BizTalk 服務跨所有橋接器已成功處理的訊息總數。當訊息由管線成功接收且成功地路由傳送至目的地時，此度量會遞增。</td>
</tr>
</table>

調整
----

在 [調整] 索引標籤中，您可以增加或減少 BizTalk 服務使用的單位數。依預設已設定一個單位。您可以將更多單位加入至 BizTalk 服務。增加規模時，也會增量輸送量。資源數量也會增加，包括部署的橋接器、協議、LOB 連接和處理能力。例如，將規模從 1 單位增加為 2 單位。在此情況下，您可以部署兩倍的橋接器、兩倍的協議、兩倍的 LOB 連接，以及兩倍的處理能力。

有些 BizTalk 版本不提供調整選項。在此情況下，只允許一個單位。若要判斷您的版本可調整的單位數，請參閱＜[BizTalk 服務：開發人員、基本、標準和高級版本圖表](http://go.microsoft.com/fwlink/p/?LinkID=302279)＞。

增加單位數可能會影響定價。如果增加單位，按一下 **[儲存]** 會出現訊息，告知您計費是否受影響。您可以選擇繼續。增加單位數時，BizTalk 服務狀態會從 [作用中] 變更為 [正在更新]。在更新狀態下，BizTalk 服務會繼續執行。

下一步
------

現在，您已熟悉不同的索引標籤，您可以接著深入了解 Azure BizTalk 服務的功能：

-   [BizTalk 服務：節流](http://go.microsoft.com/fwlink/p/?LinkID=302282)
     (英文)
-   [BizTalk 服務：簽發者名稱和簽發者金鑰](http://go.microsoft.com/fwlink/p/?LinkID=303941) (英文)
-   [BizTalk 服務：備份與還原](http://go.microsoft.com/fwlink/p/?LinkID=329873)
     (英文)

另請參閱
--------

-   [BizTalk 服務：開發人員、基本、標準和高級版本圖表](http://go.microsoft.com/fwlink/p/?LinkID=302279)
     (英文)
-   [BizTalk 服務：使用 Azure 管理入口網站進行佈建](http://go.microsoft.com/fwlink/p/?LinkID=302280)
     (英文)
-   [BizTalk 服務：BizTalk 服務狀態圖](http://go.microsoft.com/fwlink/p/?LinkID=329870)
     (英文)
-   [如何開始使用 Azure BizTalk 服務 SDK](http://go.microsoft.com/fwlink/p/?LinkID=302335)

