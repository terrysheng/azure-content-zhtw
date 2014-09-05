<properties linkid="manage-services-import-export" urlDisplayName="Azure Import/Export Service" pageTitle="Using import/export to transfer data to Blob Storage | Microsoft Azure" metaKeywords="" description="Learn how to create import and export jobs in the Azure Management Portal to transfer data to blob storage." metaCanonical="" disqusComments="1" umbracoNaviHide="0" title="Using the Azure Import/Export Service to Transfer Data to Blob Storage" authors="tamram" />

使用 Azure 匯入/匯出服務將資料移轉至 Blob 儲存體
================================================

透過網路進行上傳所費不貲或不可行時，您可以使用 Azure 匯入/匯出服務將大量檔案資料移轉至 Azure Blob 儲存體。您也可以使用匯入/匯出服務，以具成本效益的方式適時將位於 Blob 儲存體的大量資料移轉至內部部署安裝。

若要將大量檔案資料移轉至 Blob 儲存體，您可以將含有該資料的一或多個硬碟送至 Azure 資料中心，而您的資料將會在此上傳至儲存體帳戶。同樣地，若要從 Blob 儲存體匯出資料，您可以將空的硬碟送至 Azure 資料中心，而您儲存體帳戶中的 Blob 資料將會在此複製到硬碟。然後再送回給您。在傳送含有資料的磁碟機之前，您要加密該磁碟機上的資料；當匯入/匯出服務匯出您的資料以便傳送給您時，此資料也會在送出前進行加密。

您可以用下列兩種方法其中之一來建立及管理匯入和匯出工作：

-   使用 Azure 管理入口網站。
-   使用服務的 REST 介面。

本文提供匯入/匯出服務的概觀，並說明如何透過管理入口網站使用匯入/匯出服務。如需 REST API 的相關資訊，請參閱 [Azure 匯入/匯出服務 REST API 參考](http://go.microsoft.com/fwlink/?LinkID=329099)。

匯入/匯出服務的概觀
-------------------

若要開始進行 Blob 儲存體的匯入或匯出程序，請先建立<em>「工作」</em>。此工作可以是<em>「匯入工作」</em>或<em>「匯出工作」</em>：

-   當您要將內部部署的資料移轉至 Azure 儲存體帳戶中的 Blob 時，請建立匯入工作。
-   當您要將目前儲存為儲存體帳戶中 Blob 的資料移轉至要運送給您的硬碟時，請建立匯出工作。

當您建立工作時，您會通知匯入/匯出服務您將運送一或多個硬碟至 Azure 資料中心。若為匯入工作，您將運送含有檔案資料的硬碟。若為匯出工作，您將運送空的硬碟。

為了準備要針對匯入工作運送的硬碟，您將會執行 **WAImportExport** 工具，該工具能將您的資料複製到磁碟機、利用 BitLocker 加密磁碟機上的資料，以及產生下面討論的磁碟機日誌檔案。

**注意**

必須使用「BitLocker 磁碟機加密」將磁碟機上的資料加密。此功能可保護傳輸中的資料。若為匯出工作，匯入/匯出服務會在將磁碟機送回給您之前，加密您的資料。

當您建立匯入或匯出工作時，您還需要<em>「磁碟機 ID」</em>，這是磁碟機製造商指派給特定硬碟的序號。磁碟機 ID 會顯示在磁碟機的外部。

### 需求和範圍

1.  **訂閱和儲存體帳戶：**您必須有現有的 Azure 訂閱以及一或多個儲存體帳戶，才能使用匯入/匯出服務。每項工作都只能從僅只一個儲存體帳戶收送資料。換句話說，一項工作不能跨越多個儲存體帳戶。如需建立新儲存體帳戶的詳細資訊，請參閱[如何建立儲存體帳戶](http://www.windowsazure.com/zh-tw/manage/services/storage/how-to-create-a-storage-account/) (英文)。
2.  **硬碟：**只有 3.5 英吋的 SATA II 硬碟能夠用於匯入/匯出服務。預覽版本不支援 4TB 以上的硬碟。若為匯入工作，將只會處理磁碟機上的第一個資料磁碟區。此資料磁碟區必須以 NTFS 格式化。您可以將 SATA II 磁碟附加至大多數使用 SATA II USB 介面卡的外部電腦。
3.  **BitLocker 加密：**必須使用 BitLocker 和以數值密碼保護的加密金鑰來加密硬碟上儲存的所有資料。
4.  **Blob 儲存體目標：**從區塊 Blob 和頁面 Blob 可以上傳或下載資料。
5.  **工作數目：**一個客戶對於每一訂閱可以有多達 20 項作用中工作。
6.  **工作的大小上限：**工作的大小是由使用的硬碟容量以及儲存體帳戶可儲存的資料數量上限所決定。每項工作可包含的硬碟不超過 10 個。

在管理入口網站中建立匯入工作
----------------------------

建立匯入工作以通知匯入/匯出服務，您會將含有資料的一或多個磁碟機運送至資料中心，以便將資料匯入您的儲存體帳戶中。

### 準備磁碟機

建立匯入工作之前，請透過 [WAImportExport 工具](http://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409)備妥磁碟機。如需使用 WAImportExport 工具的詳細資訊，請參閱 [WAImportExport 工具參考](http://go.microsoft.com/fwlink/?LinkId=329032)。

若要準備磁碟機，請遵循以下三個步驟：

1.  決定要匯入的資料，以及您需要的磁碟機數目。
2.  在 Azure Blob 服務中識別資料的目的地 Blob。
3.  使用 WAImportExport 工具將資料複製到一或多個硬碟。

WAImportExport 工具會針對每個備妥的磁碟機產生一個<em>「磁碟機日誌」</em>檔案。磁碟機日誌檔案會儲存在本機電腦上，而非磁碟機本身。您會在建立匯入工作時上傳該日誌檔案。磁碟機日誌檔案包含磁碟機 ID 和 BitLocker 金鑰，以及有關磁碟機的其他資訊。

### 建立匯入工作

1.  備妥磁碟機後，請在管理入口網站中瀏覽至您的儲存體帳戶，然後檢視 [儀表板]。在 **[Quick Glance]** 之下，按一下 **[Create an Import Job]**。

2.  在精靈的步驟 1，指出您已備妥磁碟機並有可用的磁碟機日誌檔案。

3.  在步驟 2，提供負責處理此匯入工作的人員連絡資訊。如果您要儲存 匯入工作的詳細資訊記錄檔，請核取 **[Save the verbose log in my 'waimportexport' blob container]**。

4.  在步驟 3，上傳在磁碟機準備步驟中取得的磁碟機日誌檔案。您必須 針對已備妥的每個磁碟機上傳一個檔案。

    ![建立匯入工作 - 步驟 3](./media/storage-import-export-service/import-job-03.png)

5.  在步驟 4，輸入匯入工作的描述性名稱。請注意，您輸入的名稱只能包含 小寫字母、數字、連字號和底線，必須以字母開頭，且不得包含空格。 當工作正在進行中和一旦完成後，您將使用您所選的名稱來進行追蹤。

    資料中心區域將會指出您的包裹必須送達的資料中心。如需詳細資訊，請參閱底下的常見問題集。

    如果已有 FedEx 追蹤號碼，請選取 **[I have my tracking number and want to enter it now]**，然後瀏覽至下一個步驟。如果沒有追蹤號碼，請選擇 **[I will provide my shipping information for this import job once I have shipped my package]**，然後完成匯入程序。

6.  如果您已有追蹤號碼，則在步驟 5 輸入此追蹤號碼並加以確認。

在管理入口網站中建立匯出工作
----------------------------

建立匯出工作以通知匯入/匯出服務，您會將一或多個空磁碟機送到資料中心，以便將資料從儲存體帳戶匯出至磁碟機，然後再將這些磁碟機運送給您。

1.  若要建立匯出工作，請在管理入口網站中瀏覽至儲存體帳戶，然後檢視 [儀表板]。 在 **[Quick Glance]** 之下，按一下 **[Create an Export Job]**，然後繼續執行 精靈。

2.  在步驟 2，提供負責處理此匯出工作的人員連絡資訊。如果您要儲存 匯出工作的詳細資訊記錄檔，請核取 **[Save the verbose log in my 'waimportexport' blob container]**。

3.  在步驟 3，指定您要從儲存體帳戶匯出至空白磁碟機的 Blob 資料。您可以選擇匯出儲存體帳戶中所有的 Blob 資料，也可以指定要匯出哪些 Blob 或 Blob 組合。

    ![建立匯出工作 - 步驟 3](./media/storage-import-export-service/export-job-03.png)

    -   若要指定要匯出的 Blob，請使用 **[等於]** 選取器，指定 Blob 的相對路徑 (以容器名稱開頭)。使用 *\$root* 指定根容器。
    -   若要指定以某個首碼開頭的所有 Blob，請使用 **[開頭為]** 選取器，指定首碼 (以正斜線 '/' 開頭)。此首碼可以是容器名稱的首碼、完整容器名稱，或是後面接著 Blob 名稱首碼的完整容器名稱。

    下表顯示有效 Blob 路徑範例：

	<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
		<tbody>
			<tr>
				<td><strong>選取器</strong></td>
				<td><strong>Blob 路徑</strong></td>
				<td><strong>說明</strong></td>
			</tr>
			<tr>
				<td>開頭為</td>
				<td>/</td>
				<td>匯出儲存體帳戶中的所有 Blob</td>
			</tr>
			<tr>
				<td>開頭為</td>
				<td>/$root/</td>
				<td>匯出根容器中的所有 Blob</td>
			</tr>
			<tr>
				<td>開頭為</td>
				<td>/book</td>
				<td>匯出任何容器中以首碼 <strong>book</strong> 開頭的所有 Blob</td>
			</tr>
			<tr>
				<td>開頭為</td>
				<td>/music/</td>
				<td>匯出容器 <strong>music</strong> 中的所有 Blob</td>
			</tr>
			<tr>
				<td>開頭為</td>
				<td>/music/love</td>
				<td>匯出容器 <strong>music</strong> 中以首碼 <strong>love</strong> 開頭的所有 Blob</td>
			</tr>
			<tr>
				<td>等於</td>
				<td>$root/logo.bmp</td>
				<td>匯出根容器中的 Blob <strong>logo.bmp</strong></td>
			</tr>
			<tr>
				<td>等於</td>
				<td>videos/story.mp4</td>
				<td>匯出容器 <strong>videos</strong> 中的 Blob <strong>story.mp4</strong></td>
			</tr>
		</tbody>
	</table>


4.  在步驟 4，輸入匯出工作的描述性名稱。您輸入的名稱只能包含小寫 字母、數字、連字號和底線，必須以字母開頭，且不得包含空格。

    資料中心區域將會指出您的包裹必須送達的資料中心。如需詳細資訊，請參閱底下的常見問題集。

    如果已有 FedEx 追蹤號碼，請選取 **[I have my tracking number and want to enter it now]**，然後瀏覽至下一個步驟。如果沒有追蹤號碼，請選擇 **[I will provide my shipping information for this export job once I have shipped my package]**，然後完成匯入程序。

5.  在步驟 5 輸入此追蹤號碼並加以確認。

在管理入口網站中追蹤工作狀態
----------------------------

您可以在管理入口網站中追蹤匯入或匯出工作的狀態。在管理入口網站中瀏覽至您的儲存體帳戶，然後按一下 **[匯入/匯出]** 索引標籤。頁面上將會顯示您的工作清單。您可以依照工作狀態、工作名稱、工作類型或追蹤號碼篩選此清單。

下表說明每項工作狀態的意義：

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
	<tbody>
		<tr>
			<td><strong>工作狀態</strong></td>
			<td><strong>說明</strong></td>
		</tr>
		<tr>
			<td>建立中</td>
			<td>工作已建立，但您尚未提供運送資訊。</td>
		</tr>
		<tr>
			<td>運送中</td>
			<td>工作已建立，且您已提供運送資訊。</td>
		</tr>
		<tr>
			<td>移轉中</td>
			<td>您的資料正從硬碟移轉 (適用於匯入工作) 或移轉至硬碟 (適用於匯出工作)。</td>
		</tr>
		<tr>
			<td>包裝中</td>
			<td>資料移轉已完成，而您的硬碟正準備送回給您。</td>
		</tr>
		<tr>
			<td>完成</td>
			<td>您的硬碟已送回給您。</td>
		</tr>
	</tbody>
</table>

檢視匯出工作的 BitLocker 金鑰
-----------------------------

若為匯出工作，您可以檢視和複製服務為磁碟機產生的 BitLocker 金鑰，以便在您從 Azure 資料中心收到磁碟機時，將匯出的資料解密。在管理入口網站中瀏覽至您的儲存體帳戶，然後按一下 **[匯入/匯出]** 索引標籤。從清單中選取您的匯出工作，然後按一下 **[檢視金鑰]** 按鈕。BitLocker 金鑰如下所示：

![檢視匯出工作的 BitLocker 金鑰](./media/storage-import-export-service/export-job-bitlocker-keys.png)

常見問題集
----------

<h3>一般</h3>

**匯入/匯出服務的定價為何？**

-   如需定價資訊，請參閱[定價頁面](http://go.microsoft.com/fwlink/?LinkId=329033)。

**匯入或匯出資料需要多久時間？**

-   這需要時間運送磁碟，加上每 TB 的資料需要數小時的時間複製資料。

**支援的介面類型有哪些？**

-   匯入/匯出服務支援 3.5 英吋的 SATA II 硬碟機 (HDD)。您可以在運送之前，使用下列轉換器將 USB 裝置中的資料移轉至 SATA：
    -   Anker 68UPSATAA-02BU
    -   Anker 68UPSHHDS-BU
    -   Startech SATADOCK22UE

**如果我想匯入或匯出的磁碟機超過 10 個，我該怎麼做？**

-   在匯入/匯出服務的預覽版本中，一項匯入或匯出工作只能參考單一工作中的 10 個磁碟機。如果想要運送的磁碟機超過 10 個，可以建立多項工作。

**如果我不小心傳送不符支援需求的 HDD，則會發生什麼情況？**

-   Azure 資料中心會將不符支援需求的磁碟機退回給您。如果包裹中只有部分磁碟機符合支援需求，將會處理這些磁碟機，而不符需求的磁碟機則會退回給您。

<h3>匯入/匯出工作管理</h3>

**如果我刪除 Azure 儲存體帳戶，則匯入和匯出工作會發生什麼情況？**

-   當您刪除儲存體帳戶時，所有 Azure 匯入/匯出工作都會隨著帳戶一起刪除。

**我可以取消工作嗎？**

-   您可以在工作狀態為 [建立中] 或 [運送中] 時取消工作。

**我可以在管理入口網站中檢視已完成工作的狀態多久？**

-   您可以檢視已完成工作的狀態長達 90 天。所有已完成的工作都會在 90 天後封存。如果您需要在 90 天後擷取已完成的工作狀態，請連絡客戶支援。

<h3>運送中</h3>

**支援的快遞服務有哪些？**

-   預覽版本僅支援聯邦快遞 (FedEx)。
-   匯入工作的包裹可以透過 FedEx Express 或 FedEx Ground 運送。
-   所有包裹都會透過 FedEx Ground 退回。

    **重要事項**

    您必須將追蹤號碼提供給 Azure 匯入/匯出服務；否則無法處理您的工作。

**退件是否有任何相關的成本？**

-   預覽版本期間免費退件。

**我可以從哪裡收送資料？**

-   匯入/匯出服務只能接受**源自**美國各地的運送，而且只能將包裹退回至美國地址。此服務支援下列區域中儲存體帳戶的資料匯入和匯出：
    -   美國東部
    -   美國西部
    -   美國中北部
    -   美國中南部
    -   北歐
    -   西歐
    -   東亞
    -   東南亞
-   如果您的儲存體帳戶位於美國資料中心，您可能必須將磁碟機運送至不同區域的資料中心，因為並非所有資料中心目前都支援匯入/匯出服務。如果您的工作是在儲存體帳戶所在區域以外的區域處理，則可能產生出口流量費用。

-   如果您的儲存體帳戶位於歐洲或亞洲資料中心，則必須將磁碟機運送至美國的其中一個支援區域，而且必須從美國境內運送。匯入/匯出服務會接著將資料複製自或複製到您在歐洲或亞洲的儲存體帳戶。

    -   若為匯入工作，複製作業不會有入口流量費用。
    -   若為匯出工作，在 Azure 資料中心之間複製資料並沒有資料傳送費用。例如，如果您的儲存體帳戶位於西歐，而您將磁碟機運送至美國東部資料中心，則將資料從西歐移至美國東部以便匯出，將會產生出口流量費用。

    **重要事項**

    Azure 資料中心無法接收從美國境外運送的磁碟機，並將拒絕傳遞這些包裹。

**我可以為了匯入/匯出工作向 Microsoft 購買磁碟機嗎？**

-   不可以。您必須針對匯入和匯出工作運送自己的磁碟機。

<h3>安全性</h3>

**Bitlocker 加密是否為必要需求？**

-   是。所有磁碟機都必須使用 BitLocker 金鑰加密。

**你們會在退回磁碟機前進行格式化嗎？**

-   不會。所有磁碟機都必須針對 BitLocker 預作準備。

