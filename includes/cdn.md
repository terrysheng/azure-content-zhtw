# 使用 Azure 的 CDN

Azure 內容傳遞網路 (CDN) 透過將運算執行個體 Blob 與靜態內容快取到位於美國、歐洲、亞洲、澳洲與南美洲的實體節點中，為開發人員提供一套傳遞高頻寬內容的全球解決方案。如需目前的 CDN 節點位置清單，請參閱 [Azure CDN 節點位置]。

此工作包含下列步驟：

* [步驟 1：建立儲存體帳戶](#Step1)
* [步驟 2：為儲存體帳戶建立新的 CDN 端點](#Step2)
* [步驟 3：存取 CDN 內容](#Step3)
* [步驟 4：移除 CDN 內容](#Step4)

使用 CDN 來快取 Azure 資料的優點包括：

-   讓離內容來源很遙遠、且所使用的應用程式需要在網際網路上歷經長途跋涉才能載入內容的使用者，享有更好的效能和使用者經驗
-   大型的分散式規模可更妥善處理瞬間大量負載 (例如產品上市等活動的開頭)

現有 CDN 客戶現在可以在 [Azure 管理入口網站]中使用 Azure CDN。CDN 是一項可用於您訂閱的附加元件，且有自己的[計費方案]。

<a id="Step1"> </a>
<h2>步驟 1：建立儲存體帳戶</h2>

使用下列程序，為 Azure 訂閱建立新的儲存體帳戶。有了儲存體帳戶，才能存取 Azure 儲存體服務。儲存體帳戶代表最高層級的命名空間，用於存取每個 Azure 儲存服務元件，也就是：Blob 服務、佇列服務和表格服務。如需 Azure 儲存服務的詳細資訊，請參閱[使用 Azure 儲存服務](http://msdn.microsoft.com/zh-tw/library/azure/gg433040.aspx)。

若要建立儲存體帳戶，您必須是服務管理員或是相關聯訂閱的共同管理員。

<div class="dev-callout">
<strong>注意</strong>
<p>如需關於使用 Azure 服務管理 API 來執行此作業的詳細資訊，請參閱 <a href="http://msdn.microsoft.com/zh-tw/library/windowsazure/hh264518.aspx">建立儲存體帳戶</a> 參考主題。</p>
</div>

**為 Azure 訂閱建立儲存體帳戶**

1.  登入 [Azure 管理入口網站]。
2.  按一下左下方的 [**新增**]。在 [**新增**] 對話方塊中，選取 [**資料服務**]，然後依序按一下 [**儲存體**] 和 [**快速建立**]。

    [**建立儲存體帳戶**] 對話方塊隨即出現。

    ![Create Storage Account][create-new-storage-account]

4. 在 [**URL**] 欄位中，輸入子網域名稱。此項目可以包含 3 至 24 個小寫字母與數字。

    此值會成為 URI 內用來將訂閱的 Blob、「佇列」或「表格」資源定址的主機名稱。若要將 Blob 服務中的容器資源定址，您需要使用下列格式的 URI，其中 *&lt;StorageAccountLabel&gt;* 是指在 [**輸入 URL**] 中輸入的值：

    http://*<StorageAcountLabel>*.blob.core.windows.net/*<mycontainer>*

    **重要事項：**URL 標籤會形成儲存體帳戶 URI 的子網域，而且必須在 Azure 中的所有代管服務間是唯一的。

	此值也用作這個儲存體帳戶在入口網站中的名稱，或用於透過程式設計方式存取此帳戶時。

5.  從 [**區域/同質群組**] 下拉式清單中，選取儲存體帳戶的區域或同質群組。如果您希望儲存體服務位於與您目前使用的其他 Windows Azure 服務相同的資料中心，請選取同質群組而非區域。這麼做可改善效能，而且出口流量不會產生任何費用。  

    **注意：**若要建立同質群組，請開啟管理入口網站的 [**設定**] 區域、按一下 [**同質群組**]，然後按一下 [**加入同質群組**] 或 [**新增**]。您也可以使用 Windows Azure 服務管理 API 建立和管理同質群組。如需詳細資訊，請參閱[同質群組的相關作業]。

6. 從 [**訂閱**] 下拉式清單中，選取將與儲存體帳戶搭配使用的訂閱。
7.  按一下 [**建立儲存體帳戶**]。建立儲存體帳戶的程序可能需要幾分鐘才能完成。
8.  若要確認已順利建立儲存體帳戶，請確認帳戶出現在 [**儲存體**] 所列出的項目中，且狀態為 [**線上**]。

<a id="Step2"> </a>
<h2>步驟 2：為儲存體帳戶建立新的 CDN 端點</h2>

啟用儲存體帳戶或裝載服務的 CDN 存取之後，所有公開可用的物件皆適用於 CDN 邊緣快取。如果您修改的物件目前是 CDN 中的快取物件，在快取內容的有效存留期已滿，且 CDN 重新整理內容之前，都無法透過 CDN 取得新的內容。

**為儲存體帳戶建立新的 CDN 端點**

1. 在 [[Azure 管理入口網站]] 的瀏覽窗格中，按一下 [**CDN**]。

2. 在功能區中，按一下 [**新增**]。在 [**新增**] 對話方塊中，選取 [**應用程式服務**]，然後依序按一下 [**CDN**] 和 [**快速建立**]。

3. 在 [**原始網域**] 下拉式清單中，從可用儲存體帳戶清單中選取您在上一節建立的儲存體帳戶。 

4. 按一下 [**建立**] 按鈕，建立新的端點。

5. 端點建立完畢之後，即會出現在訂閱的端點清單中。此清單檢視會顯示用來存取所快取內容的 URL 以及原始網域。 

	原始網域是指 CDN 從其快取內容過來的位置。原始網域可以是儲存體帳戶或雲端服務；就此範例的目的而言，我們使用的是儲存體帳戶。根據您指定的 cache-control 設定或是根據快取網路的預設啟發學習法，儲存體內容會被快取至 Edge Server。請參閱[如何管理 Blob 內容過期]，(http://msdn.microsoft.com/zh-tw/library/gg680306.aspx) 以獲得詳細資訊。 


    <div class="dev-callout">
    <strong>Note</strong>
    <p>The configuration created for the endpoint will not
    immediately be available; it can take up to 60 minutes for the
    registration to propagate through the CDN network. Users who try to
    use the CDN domain name immediately may receive status code 400
    (Bad Request) until the content is available via the CDN.</p>
    </div>

<a id="Step3"> </a>
<h2>步驟 3：存取 CDN 內容</h2> 

若要存取 CDN 上快取的內容，請使用入口網站中提供的 CDN URL。所快取 Blob 的位址將類似如下：

http://<*CDNNamespace*\>.vo.msecnd.net/<*myPublicContainer*\>/<*BlobName*\>

<a id="Step4"> </a>
<h2>步驟 4：從 CDN 移除內容</h2>

如果您不想再快取 Azure 內容
傳遞網路 (CDN) 中的物件，可以採取下列其中一個步驟：

-   對於 Azure Blob，您可以從公用容器中刪除 Blob。 -   您可以將容器設為私人而非公用。請參閱[限制對 Blob 和容器的存取](http://msdn.microsoft.com/zh-tw/library/dd179354.aspx) 以獲得詳細資訊。
-   您可以使用管理入口網站來停用或刪除 CDN 端點。
-   您可以修改託管服務，使其不再回應物件的要求。

已在 CDN 中快取的物件會保持快取狀態，直到物件的有效存留期已滿。有效存留期間已滿時，CDN 將查看 CDN 端點是否仍然有效，以及物件是否仍可匿名存取。如果不是的話，將不再快取物件。

Azure 管理入口網站目前不支援立即清除內容的功能。請聯絡 [Azure 支援]：(http://azure.microsoft.com/zh-tw/support/options/)  如果您需要立即清除內容。 

## 其他資源

-   [如何在 Azure 中建立同質群組]
-   [作法：管理 Azure 訂閱的儲存體帳戶]
-   [關於服務管理 API]
-   [如何將 CDN 對應至自訂網域]

  [建立儲存體帳戶]: http://msdn.microsoft.com/zh-tw/library/windowsazure/hh264518.aspx
  [Azure CDN 節點位置]: http://msdn.microsoft.com/zh-tw/library/windowsazure/gg680302.aspx
  [Azure 管理入口網站]: https://manage.windowsazure.com/
  [計費方案]: /zh-tw/pricing/calculator/?scenario=full
  [如何註冊自訂子網域名稱以供在 Azure 中存取 Blob]: http://msdn.microsoft.com/zh-tw/library/windowsazure/ee795179.aspx
  [如何在 Azure 中建立同質群組]: http://msdn.microsoft.com/library/azure/ee460798.aspx
  [Azure CDN 概觀]: http://msdn.microsoft.com/zh-tw/library/windowsazure/ff919703.aspx
  [作法：管理 Azure 訂閱的儲存體帳戶]: http://msdn.microsoft.com/zh-tw/library/windowsazure/hh531567.aspx
  [關於服務管理 API]: http://msdn.microsoft.com/zh-tw/library/windowsazure/ee460807.aspx
  [如何將 CDN 對應至自訂網域]: http://msdn.microsoft.com/zh-tw/library/windowsazure/gg680307.aspx


[create-new-storage-account]: ./media/cdn/CDN_CreateNewStorageAcct.png
[先前的管理入口網站]: ../../Shared/Media/previous-portal.png

<!--HONumber=35_1-->
