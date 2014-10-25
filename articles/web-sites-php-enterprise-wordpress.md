<properties title="Enterprise class WordPress on Azure Websites" pageTitle="Enterprise class WordPress on Azure Websites" description="Learn how to host an enterprise class WordPress site on Azure Websites" metaKeywords="wordpress azure, wordpress website, wordpress azure website" services="web-sites" solutions="web" documentationCenter="" authors="larryfr" videoId="" scriptId="" />

## Azure 網站上的企業級 WordPress

Azure 網站針對關鍵的大規模 [WordPress][] 網站，提供可擴充、安全又容易使用的環境。Microsoft 本身執行企業級網站，例如 [Office][] 和 [Bing][] 部落格。本文件說明如何使用 Azure 網站，來建立與維護可應付大量訪客的企業級雲端架構 WordPress 網站。

## 本文內容

-   [架構與規劃][] - 請了解架構、需求和效能考量之後，再建立網站

-   [作法][] - 建立、部署和設定網站

-   [其他資源][] - 額外資源和資訊

## <span id="plan"></span></a>架構與規劃

基本 WordPress 安裝僅有兩項需求。

-   **MySQL 資料庫** - 可透過 [Azure 市集中的 ClearDB][] 取得，或您可在 Azure 虛擬機器上，使用 [Windows][] 或 [Linux][] 來管理自己的 MySQL 安裝。

    > [WACOM.NOTE] ClearDB 提供數個 MySQL 組態，每個組態具有不同的效能特性。請參閱 [Azure 市集][Azure 市集中的 ClearDB]，以取得透過 Azure 市集所提供的產品相關資訊，請參閱 [ClearDB 定價][]，以取得 ClearDB 直接提供的產品。

-   **PHP 5.2.4 或更高版本** - Azure 網站目前提供 [PHP 5.3、5.4 和 5.5 版][]。

    > [WACOM.NOTE] 建議您務必在最新版的 PHP 上執行，以確保您擁有最新的安全性問題修正。

### 基本部署

只需使用基本需求，您便可在 Azure 區域內建立基本的解決方案。

![an Azure Website and MySQL Database hosted in a single Azure region][]

這可讓您透過建立網站的多個執行個體來橫向擴充應用程式，並同時在特定地理區域的資料中心內託管所有項目。此區域以外的訪客可能會在使用此網站時察覺到緩慢的回應時間，而且如果此區域內的資料中心發生故障，您的應用程式也就無法運作。

### 多重區域部署

使用 Azure [流量管理員][]，您可以在多個地理區域中調整 WordPress 網站，但只提供一個 URL 給訪客。所有透過流量管理員進入的訪客，會根據負載平衡組態接著被路由傳送至某個區域。

![an Azure Website, hosted in multiple regions, using CDBR High Availability router to route to MySQL across regions][]

在每個區域內，您仍然可以在多個網站執行個體中調整 WordPress 網站，但此調整會是區域特定；高流量區域和低流量區域的調整可能會有所不同。

您可以使用 ClearDB 的 [CDBR 高可用性路由器][] (如左側所示) 或 [MySQL 叢集 CGE][]，進行複寫和路由傳送至多個 MySQL 資料庫。

### 包含媒體儲存體和快取的多重區域部署

如果網站接受上傳或主機媒體檔案，請使用 Azure Blob 儲存體。如果您需要快取，請考慮 [Redis cache][]、[Memcache Cloud][]、[MemCachier][] 或 [Azure 市集][]中的其中一個快取產品。

![an Azure Website, hosted in multiple regions, using CDBR High Availability router for MySQL, with Managed Cache, Blob storage, and CDN][]

依預設，Blob 儲存體會在不同區域中異地發佈，因此您無需擔心在所有網站中複寫檔案。您也可以為 Blob 儲存體啟用 Azure [內容發佈網路 (Content Distribution Network，CDN)][]，這會將檔案發佈至靠近訪客的端點。

### 規劃

#### 其他需求

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">作法...</th>
<th align="left">目的...</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><strong>上傳或儲存大型檔案</strong></td>
<td align="left"><a href="https://wordpress.org/plugins/windows-azure-storage/">使用 Blob 儲存體的 WordPress 外掛程式</a></td>
</tr>
<tr class="even">
<td align="left"><strong>傳送電子郵件</strong></td>
<td align="left"><a href="http://azure.microsoft.com/en-us/gallery/store/sendgrid/sendgrid-azure/">SendGrid</a> 和<a href="http://wordpress.org/plugins/sendgrid-email-delivery-simplified/">使用 SendGrid 的 WordPress 外掛程式</a></td>
</tr>
<tr class="odd">
<td align="left"><strong>自訂網域名稱</strong></td>
<td align="left"><a href="http://azure.microsoft.com/zh-tw/documentation/articles/web-sites-custom-domain-name/">Azure 網站中的自訂網域名稱</a></td>
</tr>
<tr class="even">
<td align="left"><strong>HTTPS</strong></td>
<td align="left"><a href="http://azure.microsoft.com/zh-tw/documentation/articles/web-sites-configure-ssl-certificate/">Azure 網站中的 HTTPS 支援</a></td>
</tr>
<tr class="odd">
<td align="left"><strong>生產前驗證</strong></td>
<td align="left"><a href="http://azure.microsoft.com/zh-tw/documentation/articles/web-sites-staged-publishing/">Azure 網站的預備發佈支援</a>
<p>請注意，從預備網站切換成生產網站時也會跟著移動 WordPress 組態。您應確定所有設定會更新到生產網站的需求，才能將預備網站切換成生產網站。</p></td>
</tr>
<tr class="even">
<td align="left"><strong>監視與疑難排解</strong></td>
<td align="left"><a href="http://azure.microsoft.com/zh-tw/documentation/articles/web-sites-enable-diagnostic-log/">在 Azure 網站中的診斷記錄</a>和<a href="http://azure.microsoft.com/zh-tw/documentation/articles/web-sites-monitor/">監視 Azure 網站</a></td>
</tr>
<tr class="odd">
<td align="left"><strong>部署網站</strong></td>
<td align="left"><a href="http://azure.microsoft.com/zh-tw/documentation/articles/web-sites-deploy/">部署 Azure 網站</a></td>
</tr>
</tbody>
</table>

#### 可用性和災難復原

| 作法...                            | 目的...                                         |
|------------------------------------|-------------------------------------------------|
| **負載平衡網站**或**異地發佈網站** | [使用 Azure 流量管理員路由傳送流量][流量管理員] |
| **備份與還原**                     | [備份 Azure 網站][]和[還原 Azure 網站][]        |

#### 效能

雲端中的效能主要會透過快取和橫向擴充實現；然而也應將記憶體、頻寬和網站主控的其他屬性納入考量。

| 作法...                  | 目的...                                                                                                                                                               |
|--------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **了解網站執行個體功能** | [定價詳細資料，包括網站大小和模式的功能][]                                                                                                                            |
| **快取資源**             | [Redis cache][]、[Memcache Cloud][]、[MemCachier][] 或 [Azure 市集][]中的其中一個快取產品。                                                                           |
| **調整應用程式**         | [調整 Azure 網站][]和 [ClearDB 高可用性路由處理][CDBR 高可用性路由器] (英文)。如果您選擇主控與管理自己的 MySQL 安裝，您應考量可橫向擴充的 [MySQL 叢集 CGE][] (英文)。 |

#### 移轉

將現有的 WordPress 網站移轉成 Azure 網站的方法有兩種。

-   **[WordPress 匯出][]** - 這將匯出您的部落格內容，並接著使用 [WordPress 匯入工具外掛程式][]將其匯入 Azure 上的新 WordPress 網站。

    > [WACOM.NOTE] 此程序可讓您移轉內容，但它無法移轉任何外掛程式、主題或其他自訂。您必須手動重新安裝這些項目。

-   **手動移轉** - [備份您的網站][]和[資料庫][] (英文)，然後手動將它還原成 Azure 網站和相關聯 MySQL 資料庫，以移轉高度自訂的網站並避免冗長的手動外掛程式、主題或其他自訂安裝。

## 作法

### <span id="create"></span></a>建立新的 WordPress 網站

1.  使用 [Azure 市集][Azure 市集中的 ClearDB]，在您將主控網站的區域中建立 MySQL 資料庫，其大小為您在[架構與規劃][]區段中所指定的大小。

2.  依照[從 Azure 中的映像庫建立 WordPress 網站][]中的步驟來建立新的 WordPress 網站。建立網站時，請選取 [Use an existing MySQL Database]，然後選取在步驟 1 中所建立的資料庫。

如果您打算移轉現有的 WordPress 網站，在建立新網站後，請參閱[移轉現有的 WordPress 網站][]。

### <span id="migrate"></span></a>將現有的 WordPress 網站移轉至 Azure

如[架構與規劃][]區段中所提到的，移轉 WordPress 網站的方法有兩種。

-   **匯出與匯入** - 適用於沒有許多自訂的網站，或只想要移動內容的網站。

-   **備份與還原** - 適用於具有許多自訂，並且要移動所有項目的網站。

使用下列其中一個區段來移轉網站。

#### 匯出與匯入方法

1.  使用 [WordPress 匯出][]來匯出您的現有網站。

2.  使用[建立新的 WordPress 網站][]區段中的步驟建立新網站。

3.  登入您在 Azure 網站上的 WordPress 網站，然後依序按一下 [外掛程式] -\> [新增]。搜尋與安裝 [WordPress Importer] 外掛程式。

4.  在安裝匯入工具外掛程式後，依序按一下 [工具] -\> [匯入]，然後選取 [WordPress] 以使用 WordPress 匯入工具外掛程式。

5.  在 [Import WordPress] 頁面上，按一下 [選擇檔案]。瀏覽至從現有 WordPress 網站匯出的 WXR 檔案，然後選擇 [Upload file and import]。

6.  按一下 [提交]。系統將提示您已成功匯入。

7.  在完成所有這些步驟後，請在 [Azure 管理入口網站][]的網站儀表板中重新啟動網站。

在匯入網站後，您可能需要執行下列步驟才能啟用不包含在匯入檔案中的設定。

| 如果使用...       | 執行此動作...                                                                                             |
|-------------------|-----------------------------------------------------------------------------------------------------------|
| **Permalinks**    | 從新網站的 WordPress 儀表板中，依序按一下 [設定] -\> [Permalinks]，然後更新 Permalinks 結構               |
| **映像/媒體連結** | 若要更新新位置的連結，請使用 [Velvet Blues Update URLs 外掛程式][] (搜尋和取代工具)，或手動在資料庫中更新 |
| **主題**          | 移至 [外觀] -\> [主題]，然後視需要更新網站主題                                                            |
| **功能表**        | 如果您的主題支援功能表，前往首頁的連結可能仍然內嵌舊的子目錄。移至 [外觀] -\> [功能表]，然後進行更新      |

#### 備份與還原方法

1.  使用 [WordPress 備份][備份您的網站]上的資訊，來備份現有的 WordPress 網站。

2.  使用[備份資料庫][資料庫]上的資訊，來備份現有的資料庫。

3.  建立新的資料庫並還原備份。

    1.  從 [Azure 市集][Azure 市集中的 ClearDB]中購買新的資料庫，或在 [Windows][] 或 [Linux][] VM 上設定 MySQL 資料庫。

    2.  使用 MySQL 用戶端 (如 [MySQL Workbench][]) 連線到新的資料庫，並匯入您的 WordPress 資料庫。

    3.  更新資料庫，將網域項目變更成新的 Azure 網站網域。例如，mywordpress.azurewebsites.net。使用[搜尋與取代 WordPress 資料庫指令碼][] 以安全的方式變更所有執行個體。

4.  建立新網站，並發佈 WordPress 備份。

    1.  在 [Azure 管理入口網站][]中，使用 [新增] -\> [網站] -\> [自訂建立]，來建立新網站和資料庫。這將會建立一個空白網站

    2.  在您的 WordPress 備份中，尋找 **wp-config.php** 檔案，並在編輯器中開啟該檔案。使用新 MySQL 資料庫的資訊來取代下列項目。

        -   **DB\_NAME** - 資料庫的使用者名稱

        -   **DB\_USER** - 用來存取資料庫的使用者名稱

        -   **DB\_PASSWORD** - 使用者密碼

        變更這些項目後，請儲存與關閉 **wp-config.php** 檔案。

    3.  使用[如何部署 Azure 網站][部署 Azure 網站]資訊來啟用您要使用的部署方法，然後將您的 WordPress 備份部署至 Azure 網站。

5.  在部署 WordPress 網站後，您應能夠使用網站的 \*.azurewebsite.net URL 來存取新網站。

### 設定網站

在建立或移轉 WordPress 網站後，請使用下列資訊以提升效能或啟用其他功能。

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">作法...</th>
<th align="left">目的...</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><strong>設定網站模式、大小及啟用調整</strong></td>
<td align="left"><a href="http://azure.microsoft.com/zh-tw/documentation/articles/web-sites-scale/">如何調整網站</a></td>
</tr>
<tr class="even">
<td align="left"><strong>啟用持續資料庫連線</strong>
<p>依預設，WordPress 不會使用持續資料庫連線，因為在多個連線後，此選項會造成資料庫連線進入流速控制狀態。</p></td>
<td align="left"><ol>
<li><p>編輯 <strong>wp-includes/wp-db.php</strong> 檔案。</p></li>
<li><p>尋找下列程式碼行。</p>
<p><code data-inline="1">$this-&gt;dbh = mysql_connect( $this-&gt;dbhost, $this-&gt;dbuser, $this-&gt;dbpassword, $new_link, $client_flags );</code></p></li>
<li><p>使用下列程式碼來取代先前的程式碼行。</p>
<p><code>$this-&gt;dbh = mysql_pconnect( $this-&gt;dbhost, $this-&gt;dbuser, $this-&gt;dbpassword, $client_flags ); if ( false !== $error_reporting ) { /br/&gt;  error_reporting( $error_reporting ); } </code></p></li>
<li><p>尋找下列程式碼行。</p>
<p><code data-inline="1">$this-&gt;dbh = @mysql_connect( $this-&gt;dbhost, $this-&gt;dbuser, $this-&gt;dbpassword, $new_link, $client_flags ); </code></p></li>
<li><p>使用下列程式碼來取代上面的程式碼行。</p>
<p><code data-inline="1">$this-&gt;dbh = @mysql_pconnect( $this-&gt;dbhost, $this-&gt;dbuser, $this-&gt;dbpassword, $client_flags ); </code></p></li>
<li><p>儲存 <strong>wp-includes/wp-db.php</strong> 檔案，然後重新部署網站。</p></li>
</ol>
<div class="wa-note"><span class="wa-icon-bulb"></span><h5><a name="note"></a>注意：</h5><p>更新 WordPress 時有可能會覆寫這些變更。</p><p>WordPress 預設會自動更新，您可透過編輯 <strong>wp-config.php</strong> 檔案，並新增 <code data-inline="1">define ( 'WP_AUTO_UPDATE_CORE', false );</code> 來停用自動更新。</p><p>處理更新的另一個方法是使用 WebJob 來監視 <strong>wp-db.php</strong> 檔案，並在每次更新檔案時執行上述修改。請參閱 <a href="http://www.hanselman.com/blog/IntroducingWindowsAzureWebJobs.aspx">WebJobs 簡介</a>，以取得更多資訊。</p></div></td>
</tr>
<tr class="odd">
<td align="left"><strong>提升效能</strong></td>
<td align="left"><ul>
<li><p><a href="http://blogs.msdn.com/b/windowsazure/archive/2013/11/18/disabling-arr-s-instance-affinity-in-windows-azure-web-sites.aspx">停用 ARR Cookie</a> - 在多個網站執行個體上執行 WordPress 時可提升效能。</p></li>
<li><p>啟用快取。<a href="http://msdn.microsoft.com/en-us/library/azure/dn690470.aspx">Redis cache</a> (預覽版) 可與 <a href="https://wordpress.org/plugins/redis-object-cache/">Redis 物件快取 WordPress 外掛程式</a> 搭配使用，或使用 <a href="http://azure.microsoft.com/en-us/gallery/store/">Azure 市集</a>的其中一個快取產品。</p></li>
<li><p><a href="http://ruslany.net/2010/03/make-wordpress-faster-on-iis-with-wincache-1-1/">如何透過 Wincache 來加快 WordPress 的速度</a> (英文) - 依預設，網站會啟用 Wincache。</p></li>
<li><p><a href="http://azure.microsoft.com/zh-tw/documentation/articles/web-sites-scale/">調整您的 Azure 網站</a>，並使用 <a href="http://www.cleardb.com/developers/cdbr/introduction">ClearDB 高可用性路由處理</a>或 <a href="http://www.mysql.com/products/cluster/">MySQL 叢集 CGE</a></p></li>
</ul></td>
</tr>
<tr class="even">
<td align="left"><strong>使用 Blob 進行儲存</strong></td>
<td align="left"><ol>
<li><p><a href="http://azure.microsoft.com/zh-tw/documentation/articles/storage-create-storage-account/">建立 Azure 儲存體帳戶</a></p></li>
<li><p>了解如何<a href="http://azure.microsoft.com/zh-tw/documentation/articles/cdn-how-to-use/">使用內容發佈網路 (CDN)</a> 來異地發佈儲存在 Blob 中的資料。</p></li>
<li><p>安裝和設定 <a href="https://wordpress.org/plugins/windows-azure-storage/">WordPress 外掛程式的 Azure 儲存體</a>。</p>
<p>如需此外掛程式的詳細安裝和設定資訊，請參閱<a href="http://plugins.svn.wordpress.org/windows-azure-storage/trunk/UserGuide.docx">使用者指南</a>。</p></li>
</ol></td>
</tr>
<tr class="odd">
<td align="left"><strong>啟用電子郵件</strong></td>
<td align="left"><ol>
<li><p><a href="http://azure.microsoft.com/en-us/gallery/store/sendgrid/sendgrid-azure/">使用 Azure 市集啟用 SendGrid</a></p></li>
<li><p><a href="http://wordpress.org/plugins/sendgrid-email-delivery-simplified/">安裝 WordPress 的 SendGrid 外掛程式</a></p></li>
</ol></td>
</tr>
<tr class="even">
<td align="left"><strong>設定自訂網域名稱</strong></td>
<td align="left"><a href="http://azure.microsoft.com/zh-tw/documentation/articles/web-sites-custom-domain-name/">在 Azure 網站中使用自訂網域名稱</a></td>
</tr>
<tr class="odd">
<td align="left"><strong>啟用自訂網域名稱的 HTTPS</strong></td>
<td align="left"><a href="http://azure.microsoft.com/zh-tw/documentation/articles/web-sites-configure-ssl-certificate/">在 Azure 網站中使用 HTTPS</a></td>
</tr>
<tr class="even">
<td align="left"><strong>負載平衡或異地發佈您的網站</strong></td>
<td align="left"><a href="http://azure.microsoft.com/blog/2014/03/27/azure-traffic-manager-can-now-integrate-with-azure-web-sites/">使用 Azure 流量管理員路由傳送流量</a>。如果您打算使用自訂網域，請參閱<a href="http://azure.microsoft.com/zh-tw/documentation/articles/web-sites-custom-domain-name/">在 Azure 網站中使用自訂網域名稱</a>，以取得搭配自訂網域名稱使用流量管理員的相關資訊</td>
</tr>
<tr class="odd">
<td align="left"><strong>啟用自動化網站備份</strong></td>
<td align="left"><a href="http://azure.microsoft.com/zh-tw/documentation/articles/web-sites-backup/">備份 Azure 網站</a></td>
</tr>
<tr class="even">
<td align="left"><strong>啟用診斷記錄</strong></td>
<td align="left"><a href="http://azure.microsoft.com/zh-tw/documentation/articles/web-sites-enable-diagnostic-log/">啟用網站的診斷記錄</a></td>
</tr>
</tbody>
</table>

## [][]其他資源

-   [WordPress 最佳化][]

-   [將 WordPress 網站轉換成多網站][]

-   [適用於 Azure 的 ClearDB 升級精靈][]

-   [在 Azure 網站的子資料夾中主控 WordPress][]

-   [逐步說明：使用 Azure 建立 WordPress 網站][]

-   [在 Azure 上主控現有的 WordPress 部落格][]

-   [在 WordPress 中啟用 Pretty Permalinks][]

-   [如何在 Azure 網站上移轉與執行 WordPress 部落格][]

-   [如何在 Azure 網站上免費執行 WordPress][]

-   [在 2 分鐘內完成在 Azure 上安裝與執行 WordPress][]

-   [將 WordPress 部落格移至 Azure - 第 1 部分：在 Azure 上建立 WordPress 部落格][]

-   [將 WordPress 部落格移至 Azure - 第 2 部分：傳輸內容][]

-   [將 WordPress 部落格移至 Azure - 第 3 部分：設定自訂網域][]

-   [將 WordPress 部落格移至 Azure - 第 4 部分：Pretty Permalinks 和 URL Rewrite 規則][]

-   [將 WordPress 部落格移至 Azure - 第 5 部分：從子資料夾移至根目錄][]

-   [如何在 Azure 帳戶中設定 WordPress 網站][]

-   [在 Azure 上維持 WordPress][]

-   [在 Azure 上的 WordPress 秘訣][]

  [WordPress]: http://www.microsoft.com/web/wordpress
  [Office]: http://blogs.office.com/
  [Bing]: http://blogs.bing.com/
  [架構與規劃]: #planning
  [作法]: #howto
  [其他資源]: #resources
  [Azure 市集中的 ClearDB]: http://www.cleardb.com/store/azure
  [Windows]: http://azure.microsoft.com/zh-tw/documentation/articles/virtual-machines-mysql-windows-server-2008r2/
  [Linux]: http://azure.microsoft.com/zh-tw/documentation/articles/virtual-machines-linux-mysql-use-opensuse/
  [ClearDB 定價]: http://www.cleardb.com/pricing.view
  [PHP 5.3、5.4 和 5.5 版]: http://azure.microsoft.com/zh-tw/documentation/articles/web-sites-php-configure/
  [an Azure Website and MySQL Database hosted in a single Azure region]: ./media/web-sites-enterprise-wordpress/basic-diagram.png
  [流量管理員]: http://azure.microsoft.com/blog/2014/03/27/azure-traffic-manager-can-now-integrate-with-azure-web-sites/
  [an Azure Website, hosted in multiple regions, using CDBR High Availability router to route to MySQL across regions]: ./media/web-sites-enterprise-wordpress/multi-region-diagram.png
  [CDBR 高可用性路由器]: http://www.cleardb.com/developers/cdbr/introduction
  [MySQL 叢集 CGE]: http://www.mysql.com/products/cluster/
  [Redis cache]: http://msdn.microsoft.com/en-us/library/azure/dn690470.aspx
  [Memcache Cloud]: http://azure.microsoft.com/en-us/gallery/store/garantiadata/memcached/
  [MemCachier]: http://azure.microsoft.com/en-us/gallery/store/memcachier/memcachier/
  [Azure 市集]: http://azure.microsoft.com/en-us/gallery/store/
  [an Azure Website, hosted in multiple regions, using CDBR High Availability router for MySQL, with Managed Cache, Blob storage, and CDN]: ./media/web-sites-enterprise-wordpress/performance-diagram.png
  [內容發佈網路 (Content Distribution Network，CDN)]: http://azure.microsoft.com/zh-tw/documentation/articles/cdn-how-to-use/
  [使用 Blob 儲存體的 WordPress 外掛程式]: https://wordpress.org/plugins/windows-azure-storage/
  [SendGrid]: http://azure.microsoft.com/en-us/gallery/store/sendgrid/sendgrid-azure/
  [使用 SendGrid 的 WordPress 外掛程式]: http://wordpress.org/plugins/sendgrid-email-delivery-simplified/
  [Azure 網站中的自訂網域名稱]: http://azure.microsoft.com/zh-tw/documentation/articles/web-sites-custom-domain-name/
  [Azure 網站中的 HTTPS 支援]: http://azure.microsoft.com/zh-tw/documentation/articles/web-sites-configure-ssl-certificate/
  [Azure 網站的預備發佈支援]: http://azure.microsoft.com/zh-tw/documentation/articles/web-sites-staged-publishing/
  [在 Azure 網站中的診斷記錄]: http://azure.microsoft.com/zh-tw/documentation/articles/web-sites-enable-diagnostic-log/
  [監視 Azure 網站]: http://azure.microsoft.com/zh-tw/documentation/articles/web-sites-monitor/
  [部署 Azure 網站]: http://azure.microsoft.com/zh-tw/documentation/articles/web-sites-deploy/
  [備份 Azure 網站]: http://azure.microsoft.com/zh-tw/documentation/articles/web-sites-backup/
  [還原 Azure 網站]: http://azure.microsoft.com/zh-tw/documentation/articles/web-sites-restore/
  [定價詳細資料，包括網站大小和模式的功能]: https://azure.microsoft.com/en-us/pricing/details/web-sites/
  [調整 Azure 網站]: http://azure.microsoft.com/zh-tw/documentation/articles/web-sites-scale/
  [WordPress 匯出]: http://en.support.wordpress.com/export/
  [WordPress 匯入工具外掛程式]: http://wordpress.org/plugins/wordpress-importer/
  [備份您的網站]: http://codex.wordpress.org/WordPress_Backups
  [資料庫]: http://codex.wordpress.org/Backing_Up_Your_Database
  [從 Azure 中的映像庫建立 WordPress 網站]:  "http://azure.microsoft.com/zh-tw/documentation/articles/web-sites-php-web-site-gallery/"
  [移轉現有的 WordPress 網站]: #migrate
  [建立新的 WordPress 網站]: #create
  [Azure 管理入口網站]: https://manage.windowsazure.com/
  [Velvet Blues Update URLs 外掛程式]: https://wordpress.org/plugins/velvet-blues-update-urls/
  [MySQL Workbench]: http://www.mysql.com/products/workbench/
  [搜尋與取代 WordPress 資料庫指令碼]: http://interconnectit.com/124/search-and-replace-for-wordpress-databases/
  [WebJobs 簡介]: http://www.hanselman.com/blog/IntroducingWindowsAzureWebJobs.aspx
  [停用 ARR Cookie]: http://blogs.msdn.com/b/windowsazure/archive/2013/11/18/disabling-arr-s-instance-affinity-in-windows-azure-web-sites.aspx
  [Redis 物件快取 WordPress 外掛程式]: https://wordpress.org/plugins/redis-object-cache/
  [如何透過 Wincache 來加快 WordPress 的速度]: http://ruslany.net/2010/03/make-wordpress-faster-on-iis-with-wincache-1-1/
  [建立 Azure 儲存體帳戶]: http://azure.microsoft.com/zh-tw/documentation/articles/storage-create-storage-account/
  [使用者指南]: http://plugins.svn.wordpress.org/windows-azure-storage/trunk/UserGuide.docx
  []: resources
  [WordPress 最佳化]: http://codex.wordpress.org/WordPress_Optimization
  [將 WordPress 網站轉換成多網站]: http://azure.microsoft.com/zh-tw/documentation/articles/web-sites-php-convert-wordpress-multisite/
  [適用於 Azure 的 ClearDB 升級精靈]: http://www.cleardb.com/store/azure/upgrade
  [在 Azure 網站的子資料夾中主控 WordPress]: http://blogs.msdn.com/b/webapps/archive/2013/02/13/hosting-wordpress-in-a-subfolder-of-your-windows-azure-web-site.aspx
  [逐步說明：使用 Azure 建立 WordPress 網站]: http://blogs.technet.com/b/blainbar/archive/2013/08/07/article-create-a-wordpress-site-using-windows-azure-read-on.aspx
  [在 Azure 上主控現有的 WordPress 部落格]: http://blogs.msdn.com/b/msgulfcommunity/archive/2013/08/26/migrating-a-self-hosted-wordpress-blog-to-windows-azure.aspx
  [在 WordPress 中啟用 Pretty Permalinks]: http://www.iis.net/learn/extensions/url-rewrite-module/enabling-pretty-permalinks-in-wordpress
  [如何在 Azure 網站上移轉與執行 WordPress 部落格]: http://www.kefalidis.me/2012/06/how-to-migrate-and-run-your-wordpress-blog-on-windows-azure-websites/
  [如何在 Azure 網站上免費執行 WordPress]: http://architects.dzone.com/articles/how-run-wordpress-azure
  [在 2 分鐘內完成在 Azure 上安裝與執行 WordPress]: http://www.sitepoint.com/wordpress-windows-azure-2-minutes-less/
  [將 WordPress 部落格移至 Azure - 第 1 部分：在 Azure 上建立 WordPress 部落格]: http://www.davebost.com/2013/07/10/moving-a-wordpress-blog-to-windows-azure-part-1
  [將 WordPress 部落格移至 Azure - 第 2 部分：傳輸內容]: http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-transferring-your-content
  [將 WordPress 部落格移至 Azure - 第 3 部分：設定自訂網域]: http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-3-setting-up-your-custom-domain
  [將 WordPress 部落格移至 Azure - 第 4 部分：Pretty Permalinks 和 URL Rewrite 規則]: http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-4-pretty-permalinks-and-url-rewrite-rules
  [將 WordPress 部落格移至 Azure - 第 5 部分：從子資料夾移至根目錄]: http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-5-moving-from-a-subfolder-to-the-root
  [如何在 Azure 帳戶中設定 WordPress 網站]: http://www.itexperience.net/2014/01/20/how-to-set-up-a-wordpress-website-in-your-windows-azure-account/
  [在 Azure 上維持 WordPress]: http://www.johnpapa.net/wordpress-on-azure/
  [在 Azure 上的 WordPress 秘訣]: http://www.johnpapa.net/azurecleardbmysql/
