<properties 
	pageTitle="如何使用 CDN |Microsoft Azure" 
	description="了解如何使用 Azure 內容傳遞網路 (CDN) 來快取 Blob 和靜態內容，以傳遞高頻寬內容。" 
	services="cdn" 
	documentationCenter=".net" 
	authors="camsoper" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cdn" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/02/2015" 
	ms.author="casoper"/>


# 整合儲存體帳戶與 CDN

可以啟用 CDN，以從您的 Azure 儲存體快取內容。它透過將計算執行個體 Blob 與靜態內容快取到位於美國、歐洲、亞洲、澳洲與南美洲的實體節點中，為開發人員提供一套傳遞高頻寬內容的全球解決方案。


## 步驟 1：建立儲存體帳戶

使用下列程序，為 Azure 訂用帳戶建立新的儲存體帳戶。有了儲存體帳戶，才能存取 Azure 儲存體服務。儲存體帳戶代表最高層級的命名空間，用於存取每個 Azure 儲存體服務元件：Blob 服務、佇列服務和資料表服務。如需詳細資訊，請參閱 [Microsoft Azure 儲存體簡介](../storage/storage-introduction.md)。

若要建立儲存體帳戶，您必須是服務管理員或是相關聯訂用帳戶的共同管理員。

> [AZURE.NOTE] 您可使用數種方法來建立儲存體帳戶，包括 Azure 入口網站和 Powershell。針對本教學課程，我們將使用 Azure 入口網站。

**為 Azure 訂用帳戶建立儲存體帳戶**

1.  登入 [Azure 入口網站](https://portal.azure.com)。
2.  在左下角，選取 [**新增**]。在 [**新增**] 對話方塊中，選取 [**資料 + 儲存體**]，然後按一下 [**儲存體帳戶**]。選取 [**傳統**] 做為部署模型，然後按一下 [**建立**]。

    此時會顯示 [**儲存體帳戶**] 刀鋒視窗。

    ![建立儲存體帳戶][create-new-storage-account]

4. 在 [**儲存體**] 欄位中，輸入子網域名稱。此項目可以包含 3 至 24 個小寫字母與數字。

    此值會成為 URI 內用來將訂用帳戶的 Blob、「佇列」或「資料表」資源定址的主機名稱。若要將 Blob 服務中的容器資源定址，您需要使用下列格式的 URI，其中 *&lt;StorageAccountLabel&gt;* 是指在 [**輸入 URL**] 中輸入的值：

    http://*&lt;StorageAcountLabel&gt;*.blob.core.windows.net/*&lt;mycontainer&gt;*

    **重要事項：**URL 標籤會形成儲存體帳戶 URI 的子網域，而且必須在 Azure 中的所有代管服務間是唯一的。

	此值也用作這個儲存體帳戶在入口網站中的名稱，或用於透過程式設計方式存取此帳戶時。

5.  選取 [**定價層**] 或使用預設值。如需定價層的詳細資訊，請參閱 [Azure 儲存體價格](../../pricing/details/storage)。

6.  選取或建立 [**資源群組**]。如需資源群組的資訊，請參閱 [Azure 資源管理員概觀](resource-group-overview/#resource-groups)。

7. 選取將與儲存體帳戶搭配使用的 [**訂用帳戶**]。

8.  按一下 [建立]。建立儲存體帳戶的程序可能需要幾分鐘才能完成。

9.  若要確認已順利建立儲存體帳戶，請確認帳戶出現在 [儲存體] 所列出的項目中，且狀態為 [線上]。


## 步驟 2：建立新的 CDN 設定檔

CDN 設定檔為 CDN 端點的集合。每個設定檔皆包含一或多個 CDN 端點。您可能會想要使用多個設定檔，依網際網路網域、Web 應用程式或其他準則來組織您的 CDN 端點。

> [AZURE.TIP] 若您已有您想要用於本教學課程的 CDN 設定檔，請繼續進行[步驟 3](#step-3-create-a-new-cdn-endpoint)。

**建立新的 CDN 設定檔**

1. 在 [Azure 管理入口網站](https://portal.azure.com)的左上角，按一下 [新增]。在 [新增] 刀鋒視窗中，依序選取 [媒體 + CDN] 和 [CDN]。

    此時會顯示新的 [CDN 設定檔] 刀鋒視窗。
    
    ![新增 CDN 設定檔][new-cdn-profile]

2. 輸入 CDN 設定檔的名稱。

3. 選取 [定價層] 或使用預設值。

4. 選取或建立 [**資源群組**]。此資源群組並不必然是與您儲存體帳戶相同的資源群組。

5. 針對此 CDN 設定檔選取 [**訂用帳戶**]。基於本教學課程用途，此訂用帳戶必須為與儲存體帳戶相同的訂用帳戶。

6. 選取 [**位置**]。此為儲存您 CDN 設定檔資訊的所在 Azure 位置。其不會影響 CDN 端點位置。此位置不一定是與儲存體帳戶相同的位置。

7. 按一下 [**建立**] 按鈕，以建立新設定檔。

## 步驟 3：建立新的 CDN 端點

**為儲存體帳戶建立新的 CDN 端點**

1. 在 [Azure 管理入口網站](https://portal.azure.com)中，巡覽至您的 CDN 設定檔。您可能已在先前步驟中將其釘選至儀表板。若否，則您可依序按一下 [瀏覽]、[CDN 設定檔] 尋找該設定檔，然後再按一下您要在其中加入端點的設定檔。

    此時會顯示 [CDN 設定檔] 刀鋒視窗。
    
    ![CDN 設定檔][cdn-profile-settings]
    
2. 按一下 [新增端點] 按鈕。

    ![[加入端點] 按鈕][cdn-new-endpoint-button]

    此時會顯示 [加入端點] 刀鋒視窗。
    
    ![新增端點刀鋒視窗][cdn-add-endpoint]

3. 輸入這個 CDN 端點的 [名稱]。此名稱會用於存取位於網域 `<EndpointName>.azureedge.net` 的快取資源。

4. 在 [**原始類型**] 下拉式清單中，選取 [*儲存體*]。

5. 在 [**原始主機名稱**] 下拉式清單中，選取您的儲存體帳戶。

6. 針對以下項目保留預設值：[**原始路徑**]、[**原始主機標頭**] 和 [**通訊協定/原始連接埠**]。您至少必須指定一個通訊協定 (HTTP 或 HTTPS)。

    > [AZURE.NOTE] 此組態可讓您在 CDN 快取儲存體帳戶中所有公開可見的容器。若您想要將範圍限制為單一容器，請使用 [**原始路徑**]。請注意，容器可見度必須設為公開。

7. 按一下 [**新增**] 按鈕，以建立新的端點。

8. 端點建立完畢之後，即會出現在設定檔的端點清單中。此清單檢視會顯示用來存取所快取內容的 URL 以及原始網域。

    ![CDN 端點][cdn-endpoint-success]

    > [AZURE.NOTE] 端點將無法立即可用。註冊可能需要 90 分鐘的處理時間，以透過 CDN 網路傳播。若使用者嘗試立即使用 CDN 網域名稱，則可能會顯示狀態碼 404，直到可透過 CDN 使用內容為止。


## 步驟 4：存取 CDN 內容

若要存取 CDN 上快取的內容，請使用入口網站中提供的 CDN URL。所快取 Blob 的位址將類似如下：

http://<*EndpointName*>.azureedge.net/<*myPublicContainer*>/<*BlobName*>

> [AZURE.NOTE] 啟用儲存體帳戶或裝載服務的 CDN 存取之後，所有公開可用的物件皆適用於 CDN 邊緣快取。如果您修改的物件目前是 CDN 中的快取物件，在快取內容的有效存留期已滿，且 CDN 重新整理內容之前，都無法透過 CDN 取得新的內容。

## 步驟 5：從 CDN 移除內容

如果不想要將內容快取到 Azure 內容傳遞網路 (CDN) 中，您可以採取下列其中一個步驟：

-   您可以將容器設為私人而非公用。如需詳細資訊，請參閱[管理對容器和 Blob 的匿名讀取權限](../storage-manage-access-to-resources/)。
-   您可以使用管理入口網站來停用或刪除 CDN 端點。
-   您可以修改託管服務，使其不再回應物件的要求。

已在 CDN 中快取的物件會保持快取狀態，直到物件的有效存留期已過或端點已清除為止。有效存留期間已滿時，CDN 將查看 CDN 端點是否仍然有效，以及物件是否仍可匿名存取。如果不是的話，將不再快取物件。


## 其他資源

-   [如何將 CDN 內容對應至自訂網域](cdn-map-content-to-custom-domain.md)

[create-new-storage-account]: ./media/cdn-create-a-storage-account-with-cdn/CDN_CreateNewStorageAcct.png

[new-cdn-profile]: ./media/cdn-create-a-storage-account-with-cdn/cdn-new-profile.png
[cdn-profile-settings]: ./media/cdn-create-a-storage-account-with-cdn/cdn-profile-settings.png
[cdn-new-endpoint-button]: ./media/cdn-create-a-storage-account-with-cdn/cdn-new-endpoint-button.png
[cdn-add-endpoint]: ./media/cdn-create-a-storage-account-with-cdn/cdn-add-endpoint.png
[cdn-endpoint-success]: ./media/cdn-create-a-storage-account-with-cdn/cdn-endpoint-success.png

<!---HONumber=AcomDC_0204_2016-->