### 將 SharePoint 2010 升級至 SharePoint 2013 然後再安裝 StorSomple Adapter for SharePoint

>[AZURE.IMPORTANT]先前透過 RBS 移到外部儲存體的任何檔案，必須等到升級完成並重新啟用 RBS 功能之後才能使用。為了限制使用者受影響的程度，請在規劃的維護期間執行任何升級或重新安裝。

[AZURE.INCLUDE [storsimple-upgrade-sharepoint-adapter](../../includes/storsimple-upgrade-sharepoint-adapter.md)]
 
#### 將 SharePoint 2010 升級為 SharePoint 2013，然後安裝配接器

1. 在 SharePoint 2010 伺服陣列中，記下外部化 BLOB 的 BLOB 存放區路徑和在其中啟用 RBS 的內容資料庫。 

2. 安裝和設定新的 SharePoint 2013 伺服器陣列。

3. 將資料庫、應用程式和網站集合從 SharePoint 2010 伺服器陣列移至新的 SharePoint 2013 伺服器陣列。如需指示，請移至 SharePoint 2013 升級程序的概觀。

4. 在新的伺服器陣列上安裝 StorSimple Adapter for SharePoint移至[安裝 StorSimple Adapter for SharePoint](#install-the-storsimple-adapter-for-sharepoint) 以了解程序。

5. 使用您在步驟 1 記下的資訊，為同一組內容資料庫啟用 RBS 並提供 BLOB 存放區路徑，需與 SharePoint 2010 安裝中所使用的路徑相同。移至[設定 RBS](#configure-rbs) 以了解程序。完成這個步驟之後，先前已外部化的檔案必須能夠從新的伺服器陣列存取。

### 升級 StorSimple Adapter for SharePoint

>[AZURE.IMPORTANT]基於下列原因，您應該將此升級排程於規劃的維護視窗期間：
>
>- 在重新安裝配接器之前，先前已外部化的內容將無法使用。
>
>- 在您解除安裝舊版 StorSimple Adapter for SharePoint 之後，安裝新版之前，任何上傳至網站的內容都將儲存在內容資料庫中。安裝新的配接器之後，您必須將該內容移至 StorSimple 裝置。


#### 升級 StorSimple Adapter for SharePoint 

1. 解除安裝舊版 StorSimple Adapter for SharePoint。

    >[AZURE.NOTE]這會自動停用內容資料庫上的 RBS。不過，現有的 BLOB 會保留在 StorSimple 裝置上。因為已停用 RBS 且 BLOB 尚未移轉回內容資料庫，對這些 BLOB 的任何要求都會失敗。
 
2. 安裝新的 StorSimple Adapter for SharePoint。新的配接器會自動辨識先前已為 RBS 啟用或停用的內容資料庫，並使用先前的設定。

<!---HONumber=July15_HO5-->