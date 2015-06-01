# 使用 Azure 的 CDN

Azure 內容傳遞網路 (CDN) 為開發人員提供
一個全球解決方案，透過在美國、歐洲、亞洲、澳洲和南美洲的實體節點
快取運算執行個體的 Blob 和靜態內容，
傳遞高頻寬的內容。如需最新的
CDN 節點位置清單，請參閱 [Azure CDN 節點位置]。

此工作包含下列步驟：

* [步驟 1：建立儲存體帳戶](#Step1)
* [步驟 2：為儲存體帳戶建立新的 CDN 端點](#Step2)
* [步驟 3：存取 CDN 內容](#Step3)
* [步驟 4：移除 CDN 內容](#Step4)

使用 CDN 來快取 Azure 資料的優點包括：

-   讓離內容來源很遙遠、且所使用的應用程式需要在 'internet trips'網際網路上歷經長途跋涉才能載入內容的使用者，享有更好的效能和使用者經驗
-   大型的分散式規模可更妥善處理瞬間大量負載 (例如產品上市等活動的開頭)

現有 CDN 客戶現在可以在 [Azure 管理入口網站]中使用 Azure CDN。CDN 是一項可用於您訂閱的附加元件功能，且有自己的[計費方案]。

<a id="Step1"> </a>
<h2>步驟 1：建立儲存體帳戶</h2>

使用下列程序，為 Azure 訂閱建立新的儲存體帳戶
。儲存體帳戶可存取 
Azure 儲存體服務。儲存體帳戶代表命名空間的最高層級，
可存取每個 Azure 儲存體服務
元件：Blob 服務、佇列服務和表格服務。如需
有關 Azure 儲存體服務的詳細資訊，請參閱[使用
Azure 儲存體服務](http://msdn.microsoft.com/library/azure/gg433040.aspx)。

若要建立儲存體帳戶，您必須是服務
系統管理員或相關聯訂閱的共同管理員。

> [AZURE.NOTE] 如需關於使用 Azure 服務管理 API
來執行此作業的詳細資訊，請參閱[建立儲存體帳戶](http://msdn.microsoft.com/library/windowsazure/hh264518.aspx)參考主題。

**為 Azure 訂閱建立儲存體帳戶**

1.  登入 [Azure 管理入口網站]。
2.  按一下左下角的 [新增]。在 [新增] 對話方塊中，選取 [資料服務]，然後依序按一下 [儲存體] 和 [快速建立]。

    [建立儲存體帳戶] 對話方塊隨即出現。

    ![建立儲存體帳戶][create-new-storage-account]

4. 在 **URL** 欄位中，輸入子網域名稱。此項目可以包含 3 至 24 個小寫字母與數字。

    此值會變成 URI 內，用來
    定址訂閱的 Blob、佇列或對應表資源的主機名稱。如果
    定址 Blob 服務中的容器資源，您要使用
    下列格式的 URI，其中 *&lt;StorageAccountLabel&gt;* 指的是
    您在 [輸入 URL] 中輸入的值：

    http://*&lt;StorageAcountLabel&gt;*.blob.core.windows.net/*&lt;mycontainer&gt;*

    **重要事項：**URL 標籤會形成儲存體帳戶 URI 的子網域，
    而且在 Azure 的所有託管服務中 
    必須是唯一的。

	此值也用作這個儲存體帳戶在入口網站中的名稱，或用於透過程式設計方式存取此帳戶時。

5.  從 [區域/同質群組] 下拉式清單中，選取儲存體帳戶的區域或同質群組。如果您希望儲存體服務位於與您目前使用的其他 Windows Azure 服務相同的資料中心，請選取同質群組而非區域。這麼做可改善效能，而且出口流量不會產生任何費用。  

    **注意：**若要建立同質群組，請開啟管理入口網站的 [設定] 區域，按一下 [同質群組]，然後按一下 [新增同質群組] 或 [新增]。您也可以使用 Windows Azure 服務管理 API 建立和管理同質群組。如需詳細資訊，請參閱[同質群組的相關作業]。

6. 從 [訂閱] 下拉式清單中，選取將與儲存體帳戶搭配使用的訂閱。
7.  按一下 [建立儲存體帳戶]。建立儲存體帳戶的程序可能需要幾分鐘才能完成。
8.  若要確認已順利建立儲存體帳戶，請確認帳戶出現在 [儲存體] 所列出的項目中，且狀態為 [線上]。

<a id="Step2"> </a>
<h2>步驟 2：為儲存體帳戶建立新的 CDN 端點</h2>

一旦您啟用 CDN 對儲存體帳戶或託管服務的存取之後，所有
公開可用的物件皆適用於 CDN 邊緣快取。如果您
修改目前在 CDN 中快取的物件，將無法
透過 CDN 使用新內容，直到快取的內容
存留時間期限到期時，CDN 重新整理其內容為止。

**為儲存體帳戶建立新的 CDN 端點**

1. 在 [Azure 管理入口網站]的瀏覽窗格中，按一下 [CDN]。

2. 在功能區上，按一下 [新增]。在 [新增] 對話方塊中，依序選取 [應用程式服務]、[CDN]、[快速建立]。

3. 在 [原始網域] 下拉式清單中，從可用儲存體帳戶清單中選取您在上一節建立的儲存體帳戶。 

4. 按一下 [建立] 按鈕，建立新的端點。

5. 端點建立完畢之後，即會出現在訂閱的端點清單中。此清單檢視會顯示用來存取所快取內容的 URL 以及原始網域。 

	原始網域是指 CDN 從其快取內容過來的位置
    。原始網域可以是儲存體帳戶或雲端服務；就此範例的目的而言，我們使用的是儲存體帳戶。根據您指定的 cache-control 設定或是根據快取網路的預設啟發學習法，儲存體內容會被快取至 Edge Server。如需詳細資訊，請參閱[如何管理 Blob 內容過期](http://msdn.microsoft.com/library/gg680306.aspx)。 


    > [AZURE.NOTE] 為端點建立的組態
    無法立即使用；最多需要 60 分鐘
    進行註冊，才能透過 CDN 網路傳播。嘗試立即
    使用 CDN 網域名稱的使用者可能會收到狀態碼 400
    (不正確的要求)，直到可透過 CDN 使用內容為止。

<a id="Step3"> </a>
<h2>步驟 3：存取 CDN 內容</h2> 

若要存取 CDN 上快取的內容，請使用入口網站中提供的 CDN URL。所快取 Blob 的位址將類似如下：

http://<*CDNNamespace*>.vo.msecnd.net/<*myPublicContainer*>/<*BlobName*>

<a id="Step4"> </a>
<h2>步驟 4：從 CDN 移除內容</h2>

如果您不想再快取 Azure 內容
傳遞網路 (CDN) 中的物件，可以採取下列其中一個步驟：

-   對於 Azure Blob，您可以從公用容器中刪除 Blob
    。
-   您可以將容器設為私人而非公用。如需詳細資訊，請參閱[限制對容器和 Blob 的存取](http://msdn.microsoft.com/library/dd179354.aspx)。
-   您可以使用管理入口網站來停用或刪除 CDN 端點
    。
-   您可以修改託管服務，使其不再回應物件的要求
    。

已在 CDN 中快取的物件會保持快取狀態，直到
物件的存留時間期限到期為止。當存留時間期限
到期時，CDN 將會檢查以查看 CDN 端點是否仍然
有效，且物件是否仍然可以匿名存取。若非如此，則
將無法再存取物件。

Azure 管理入口網站目前不支援立即清除內容的功能。如果您需要立即清除內容，請連絡 [Azure 支援](http://azure.microsoft.com/support/options/)。 

## 其他資源

-   [如何在 Azure 中建立同質群組]
-   [作法：管理 Azure 訂閱的儲存體帳戶]
-   [關於服務管理 API]
-   [如何將 CDN 內容對應至自訂網域]

  [建立儲存體帳戶]: http://msdn.microsoft.com/library/windowsazure/hh264518.aspx
  [Azure CDN 節點位置]: http://msdn.microsoft.com/library/windowsazure/gg680302.aspx
  [Azure 管理入口網站]: https://manage.windowsazure.com/
  [計費方案]: /pricing/calculator/?scenario=full
  [如何註冊自訂子網域名稱以供在 Azure 中存取 Blob]: http://msdn.microsoft.com/library/windowsazure/ee795179.aspx
  [如何在 Azure 中建立同質群組]: http://msdn.microsoft.com/library/azure/ee460798.aspx
  [Azure CDN 概觀]: http://msdn.microsoft.com/library/windowsazure/ff919703.aspx
  [作法：管理 Azure 訂閱的儲存體帳戶]: http://msdn.microsoft.com/library/windowsazure/hh531567.aspx
  [關於服務管理 API]: http://msdn.microsoft.com/library/windowsazure/ee460807.aspx
  [如何將 CDN 內容對應至自訂網域]: http://msdn.microsoft.com/library/windowsazure/gg680307.aspx


[create-new-storage-account]: ./media/cdn/CDN_CreateNewStorageAcct.png
[先前的管理入口網站]: ../../Shared/Media/previous-portal.png

<!--HONumber=49-->