<properties
	pageTitle="使用匯入/匯出將資料移轉至 Blob 儲存體 | Microsoft Azure"
	description="了解如何在「Azure 傳統入口網站」中建立匯入和匯出工作，以將資料移轉至 Blob 儲存體。"
	authors="robinsh"
	manager="carmonm"
	editor="tysonn"
	services="storage"
	documentationCenter=""/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/22/2015"
	ms.author="renash"/>


# 使用 Microsoft Azure 匯入/匯出服務將資料移轉至 Blob 儲存體

## 概觀

透過網路進行上傳所費不貲或不可行時，您可以使用 Microsoft Azure 匯入/匯出服務將大量檔案資料移轉至 Azure Blob 儲存體。您也可以使用匯入/匯出服務，以具成本效益的方式適時將位於 Blob 儲存體的大量資料移轉至內部部署安裝。

若要將大量檔案資料移轉至 Blob 儲存體，您可以將含有該資料的一或多個硬碟送至 Azure 資料中心，而您的資料將會在此上傳至儲存體帳戶。同樣地，若要從 Blob 儲存體匯出資料，您可以將空的硬碟送至 Azure 資料中心，而您儲存體帳戶中的 Blob 資料將會在此複製到硬碟。然後再送回給您。在傳送含有資料的磁碟機之前，您要加密該磁碟機上的資料；當匯入/匯出服務匯出您的資料以便傳送給您時，此資料也會在送出前進行加密。

您可以用下列兩種方法其中之一來建立及管理匯入和匯出工作：

- 使用 Azure 傳統入口網站。
- 使用服務的 REST 介面。

本文提供匯入/匯出服務的概觀，並說明如何搭配「傳統入口網站」使用匯入/匯出服務。如需 REST API 的相關資訊，請參閱 [Azure 匯入/匯出服務 REST API 參考](http://go.microsoft.com/fwlink/?LinkID=329099)。

## 匯入/匯出服務的簡介 ##

若要開始進行 Blob 儲存體的匯入或匯出程序，請先建立「工作」。此工作可以是「匯入工作」或「匯出工作」：

- 當您要將內部部署的資料移轉至 Azure 儲存體帳戶中的 Blob 時，請建立匯入工作。
- 當您要將目前儲存為儲存體帳戶中 Blob 的資料移轉至要運送給您的硬碟時，請建立匯出工作。

當您建立工作時，您會通知匯入/匯出服務您將運送一或多個硬碟至 Azure 資料中心。若為匯入工作，您將運送含有檔案資料的硬碟。若為匯出工作，您將運送空的硬碟。

為了準備要針對匯入工作運送的硬碟，您將會執行 [Microsoft Azure 匯入/匯出工具] 工具，該工具能將您的資料複製到磁碟機、利用 BitLocker 加密磁碟機上的資料，以及產生下面討論的磁碟機日誌檔案。

> [AZURE.NOTE] 必須使用「BitLocker 磁碟機加密」將磁碟機上的資料加密。此功能可保護傳輸中的資料。若為匯出工作，匯入/匯出服務會在將磁碟機送回給您之前，加密您的資料。

當您建立匯入或匯出工作時，您還需要「磁碟機 ID」，這是磁碟機製造商指派給特定硬碟的序號。磁碟機 ID 會顯示在磁碟機的外部。

### 需求和範圍

1.	**訂用帳戶和儲存體帳戶：**您必須有現有的 Azure 訂用帳戶以及一或多個儲存體帳戶，才能使用匯入/匯出服務。每項工作都只能從僅只一個儲存體帳戶收送資料。換句話說，一項工作不能跨越多個儲存體帳戶。如需建立新儲存體帳戶的詳細資訊，請參閱[如何建立儲存體帳戶](storage-create-storage-account.md) (英文)。
2.	**硬碟：**只有 3.5 英吋的 SATA II/III 內部硬碟能夠用於匯入/匯出服務。可支援高達 6 TB 的硬碟。若為匯入工作，將只會處理磁碟機上的第一個資料磁碟區。此資料磁碟區必須以 NTFS 格式化。您可以將 SATA II/III 磁碟連接至大多數使用外部 SATA II/III USB 介面卡的外部電腦。
3.	**BitLocker 加密**：須使用 BitLocker 和以數值密碼保護的加密金鑰來加密硬碟上儲存的所有資料。
4.	**Blob 儲存體目標：**從區塊 Blob 和頁面 Blob 可以上傳或下載資料。
5.	**工作數目：**一個客戶對於每一儲存體帳戶可以有多達 20 項作用中工作。
6.	**工作的大小上限：**工作的大小是由使用的硬碟容量以及儲存體帳戶可儲存的資料數量上限所決定。每項工作可包含的硬碟不超過 10 個。

  >[AZURE.IMPORTANT] 此服務不支援隨附內建 USB 介面卡的外接式硬碟。請勿準備外部 HDD。您也無法使用外接盒內部的磁碟來匯入資料。請使用 3.5 英吋的 SATA II/III「內部」硬碟。如果您無法將 SATA 磁碟直接連接到您的電腦，請使用外部 SATA 到 USB 介面卡。請參閱＜常見問題集＞一節中建議的介面卡清單。

## 在傳統入口網站中建立匯入工作##

建立匯入工作以通知匯入/匯出服務，您會將含有資料的一或多個磁碟機運送至資料中心，以便將資料匯入您的儲存體帳戶中。

### 準備磁碟機

建立匯入工作之前，請透過 Microsoft Azure 匯入/匯出工具備妥磁碟機。如需有關使用 Microsoft Azure 匯入/匯出工具的詳細資訊，請參閱 [Microsoft Azure 匯入/匯出工具參考](http://go.microsoft.com/fwlink/?LinkId=329032)。您可以下載作為獨立封裝的 [Microsoft Azure 匯入/匯出工具](http://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409)。

若要準備磁碟機，請遵循以下三個步驟：

1.	決定要匯入的資料，以及您需要的磁碟機數目。
2.	在 Blob 儲存體中識別資料的目的地 Blob。
3.	使用 Microsoft Azure 匯入/匯出工具將資料複製到一或多個硬碟。

Microsoft Azure 匯入/匯出工具會針對每個備妥的磁碟機產生一個「磁碟機日誌」檔案。磁碟機日誌檔案會儲存在本機電腦上，而非磁碟機本身。您會在建立匯入工作時上傳該日誌檔案。磁碟機日誌檔案包含磁碟機 ID 和 BitLocker 金鑰，以及有關磁碟機的其他資訊。

### 建立匯入工作

1.	備妥磁碟機後，請在「傳統入口網站」中瀏覽至您的儲存體帳戶，然後檢視 [儀表板]。在 [快速概覽] 之下，按一下 [建立匯入工作]。

2.	在精靈的步驟 1，指出您已備妥磁碟機並有可用的磁碟機日誌。

3.	在步驟 2，提供負責處理此匯入工作的人員連絡資訊。若想要儲存匯入工作的詳細資訊記錄資料，請核取 [將詳細資訊記錄儲存於我的 'waimportexport' Blob 容器中] 選項。

4.	在步驟 3，上傳在磁碟機準備步驟中取得的磁碟機日誌檔案。您需要針對已備妥的每個磁碟機上傳一個檔案。

	![建立匯入工作 - 步驟 3][import-job-03]

5.	在步驟 4 中，輸入匯入工作的描述性名稱。請注意，您輸入的名稱只能包含小寫字母、數字、連字號和底線，必須以字母開頭，且不得包含空格。當工作正在進行中和一旦完成後，您將使用您所選的名稱來進行追蹤。

	接著，從清單中選取資料中心區域。資料中心區域將會指出您的包裹必須送達的資料中心和地址。如需詳細資訊，請參閱底下的常見問題集。

6. 	在步驟 5 中，從清單中選取您的寄回貨運公司，並輸入貨運公司客戶編號。當匯入工作完成時，Microsoft 會透過此廠商將磁碟機寄還給您。

	若您有追蹤號碼，請從清單中選取您的貨運公司，並輸入追蹤號碼。

	如果沒有追蹤號碼，請選擇 [一旦傳送套件，就會提供此匯入工作的傳送資訊]，然後完成匯入程序。

7. 若要在寄出包裹之後輸入追蹤號碼，請在「傳統入口網站」中回到您儲存體帳戶的 [匯入/匯出] 頁面，從清單中選取工作，然後選擇 [出貨資訊]。逐步執行精靈，在步驟 2 中輸入追蹤號碼。

	如果在建立作業的 2 個星期內沒有更新追蹤號碼，該工作會過期。

	若工作處於「建立中」、「傳送中」或「傳輸中」狀態，則您也可以在精靈的步驟 2 中更新貨運公司客戶編號。一旦工作進入「包裝中」狀態，您就無法更新該工作的貨運公司客戶編號。

## 在傳統入口網站中建立匯出工作##

建立匯出工作以通知匯入/匯出服務，您會將一或多個空磁碟機送到資料中心，以便將資料從儲存體帳戶匯出至磁碟機，然後再將這些磁碟機運送給您。

1. 	若要建立匯出工作，請在「傳統入口網站」中瀏覽至您的儲存體帳戶，然後檢視 [儀表板]。在 [快速概覽] 之下，按一下 [建立匯出工作]，然後繼續執行精靈。

2. 	在步驟 2，提供負責處理此匯出工作的人員連絡資訊。若想要儲存匯出工作的詳細資訊記錄資料，請核取 [將詳細資訊記錄儲存於我的 'waimportexport' Blob 容器中] 選項。

3.	在步驟 3，指定您要從儲存體帳戶匯出至空白磁碟機的 Blob您可以選擇匯出儲存體帳戶中所有的 Blob 資料，也可以指定要匯出哪幾個或哪幾組 Blob。

	![建立匯出工作 - 步驟 3][export-job-03]

	- 若要指定要匯出的 Blob，請使用 [等於] 選取器，指定 Blob 的相對路徑 (以容器名稱開頭)。使用 *$root* 指定根容器。
	- 若要指定以某個首碼開頭的所有 Blob，請使用 [開頭為] 選取器，指定首碼 (以正斜線 '/' 開頭)。此首碼可以是容器名稱的首碼、完整容器名稱，或是後面接著 Blob 名稱首碼的完整容器名稱。

	下表顯示有效 Blob 路徑範例：

	選取器|Blob 路徑|說明
	---|---|---
	開頭為|/|匯出儲存體帳戶中的所有 Blob
	開頭為|/$root/|匯出根容器中的所有 Blob
	開頭為|/book|匯出任何容器中以首碼 **book** 開頭的所有 Blob
	開頭為|/music/|匯出容器 **music** 中的所有 Blob
	開頭為|/music/love|匯出容器 **music** 中以首碼 **love** 開頭的所有 Blob
	等於|$root/logo.bmp|匯出根容器中的 Blob **logo.bmp**
	等於|videos/story.mp4|匯出容器 **videos** 中的 Blob **story.mp4**


4.	在步驟 4，輸入匯出工作的描述性名稱。您輸入的名稱只能包含小寫字母、數字、連字號和底線，必須以字母開頭，且不得包含空格。

	資料中心區域將會指出您的包裹必須送達的資料中心。如需詳細資訊，請參閱底下的常見問題集。

5. 	在步驟 5 中，從清單中選取您的寄回貨運公司，並輸入貨運公司客戶編號。當匯出工作完成時，Microsoft 會使用此帳戶將磁碟機寄還給您。

	若您有追蹤號碼，請從清單中選取您的貨運公司，並輸入追蹤號碼。

	如果沒有追蹤號碼，請選擇 [I will provide my shipping information for this export job once I have shipped my package]，然後完成匯出程序。

6. 若要在寄出包裹之後輸入追蹤號碼，請在「傳統入口網站」中回到您儲存體帳戶的 [匯入/匯出] 頁面，從清單中選取工作，然後選擇 [出貨資訊]。逐步執行精靈，在步驟 2 中輸入追蹤號碼。

	如果在建立作業的 2 個星期內沒有更新追蹤號碼，該工作會過期。

	若工作處於「建立中」、「傳送中」或「傳輸中」狀態，則您也可以在精靈的步驟 2 中更新貨運公司客戶編號。一旦工作進入「包裝中」狀態，您就無法更新該工作的貨運公司客戶編號。

> [AZURE.NOTE] 如果要匯出的 Blob 在複製到硬碟機時為使用中，則 Azure 匯入/匯出服務會擷取 Blob 的快照集，並複製此快照集。

## 在傳統入口網站中追蹤工作狀態##

您可以在「傳統入口網站」中追蹤匯入或匯出工作的狀態。請在「傳統入口網站」中瀏覽至您的儲存體帳戶，然後按一下 [匯入/匯出] 索引標籤。頁面上將會顯示您的工作清單。您可以依照工作狀態、工作名稱、工作類型或追蹤號碼篩選此清單。

下表說明每項工作狀態的意義：

工作狀態|說明
---|---
建立中|工作已建立，但您尚未提供運送資訊。
運送中|工作已建立，且您已提供運送資訊。
移轉中|您的資料正從硬碟移轉 (適用於匯入工作) 或移轉至硬碟 (適用於匯出工作)。
包裝中|資料移轉已完成，而您的硬碟正準備送回給您。
完成|您的硬碟已送回給您。


## 檢視匯出工作的 BitLocker 金鑰 ##

若為匯出工作，您可以檢視和複製服務為磁碟機產生的 BitLocker 金鑰，以便在您從 Azure 資料中心收到磁碟機時，將匯出的資料解密。請在「傳統入口網站」中瀏覽至您的儲存體帳戶，然後按一下 [匯入/匯出] 索引標籤。從清單中選取您的匯出工作，然後按一下 [檢視金鑰] 按鈕。BitLocker 金鑰如下所示：

![檢視匯出工作的 BitLocker 金鑰][export-job-bitlocker-keys]

## 常見問題集 ##

### 一般

**匯入/匯出服務的定價為何？**

- 如需定價資訊，請參閱[定價頁面](http://go.microsoft.com/fwlink/?LinkId=329033)。

**匯入或匯出資料需要多久時間？**

- 這需要時間運送磁碟，加上每 TB 的資料需要數小時的時間複製資料。

**支援的介面類型有哪些？**

- 匯入/匯出服務支援 3.5 英吋的 SATA II/III 內部硬碟機 (HDD)。您可以在運送之前，使用下列轉換器將 USB 裝置中的資料移轉至 SATA：
	- Anker 68UPSATAA-02BU
	- Anker 68UPSHHDS-BU
	- Startech SATADOCK22UE

> [AZURE.NOTE] 若您有以上未列出的轉換器，您可以嘗試使用您的轉換器執行 [Microsoft Azure 匯入/匯出工具] 來準備磁碟機，看看是否有用，再決定購買支援的轉換器。

- 不支援含有內建 USB 介面卡的外部 HDD。

**如果我想匯入或匯出的磁碟機超過 10 個，我該怎麼做？**

- 在匯入/匯出服務中，一項匯入或匯出工作只能參考單一工作中的 10 個磁碟機。如果想要運送的磁碟機超過 10 個，可以建立多項工作。

**如果我不小心傳送不符支援需求的 HDD，則會發生什麼情況？**

- Azure 資料中心會將不符支援需求的磁碟機退回給您。如果包裹中只有部分磁碟機符合支援需求，將會處理這些磁碟機，而不符需求的磁碟機則會退回給您。

### 匯入/匯出工作管理

**如果我刪除 Azure 儲存體帳戶，則匯入和匯出工作會發生什麼情況？**

- 當您刪除儲存體帳戶時，所有 Azure 匯入/匯出工作都會隨著帳戶一起刪除。  

**我可以取消工作嗎？**

- 您可以在工作狀態為 [建立中] 或 [運送中] 時取消工作。

**我可以在「傳統入口網站」中檢視多久期間內已完成工作的狀態？**

- 您最多可以檢視之前 90 天內已完成工作的狀態。所有已完成的工作都會在 90 天後刪除。

**Bitlocker 加密是否為必要需求？**

- 是。所有磁碟機都必須使用 BitLocker 金鑰加密。

**你們會在退回磁碟機前進行格式化嗎？**

- 不會。所有磁碟機都必須針對 BitLocker 預作準備。

**我需要在建立匯出作業時執行任何磁碟準備工作嗎？**
- 不用，但建議先執行某些前置檢查。請使用 Azure 匯入/匯出工具的 [PreviewExport](https://msdn.microsoft.com/library/azure/dn722414.aspx) 命令檢查所需的磁碟號碼。它可根據您要使用的磁碟機大小，協助您預覽所選取之 Blob 的磁碟機使用情況。也請檢查您可以對為了匯出工作而運送的硬碟讀取/寫入。

### 運送中

**支援的快遞服務有哪些？**

- 在美國和歐洲地區僅支援 [聯邦快遞](http://www.fedex.com/us/oadr/) (FedEx)。所有包裹都會透過 FedEx Ground 或 FedEx International Economy 寄回。

- 在亞洲地區僅支援 [DHL](http://www.dhl-welcome.com/Tutorial/)。所有包裹都會透過 DHL Express Worldwide 寄回。

	> [AZURE.IMPORTANT] 您必須將追蹤號碼提供給 Azure 匯入/匯出服務；否則無法處理您的工作。

**退件是否有任何相關的成本？**

- Microsoft 會使您在建立工作時提供的貨運公司客戶編號，將磁碟機從資料中心寄到您的寄回地址。請務必提供資料中心區域所支援之貨運公司的貨運公司客戶編號。您可以建立 [FedEx](http://www.fedex.com/us/oadr/) (適用於美國和歐洲) 或 [DHL](http://www.dhl-welcome.com/Tutorial/) (亞洲) 貨運公司客戶 (若沒有的話)。

- 寄回費用由您的運貨公司客戶收取，由運貨公司決定。

**我可以從哪裡收送資料？**

- 在下列區域，匯入/匯出服務支援從儲存體帳戶中匯入資料和匯出資料：
	- 美國東部
	- 美國西部
	- 美國中北部
	- 美國中南部
	- 北歐
	- 西歐
	- 東亞
	- 東南亞

- 將會提供您的儲存體帳戶所在區域中的寄送地址給您。例如，假設您住在美國，而儲存體帳戶位於西歐資料中心，則會提供歐洲的寄送地址讓您寄出磁碟機。

	> [AZURE.IMPORTANT] 請注意，您寄送的實體媒體可能需要跨國界。您必須確定實體媒體和資料的匯入和/或匯出符合相關管轄法律。在寄出實體媒體之前，請洽詢顧問來確認您的媒體和資料可以合法地寄到所識別的資料中心。這有助於確保及時送達 Microsoft。

- 在寄送包裹時，您必須遵守 [Microsoft Azure 服務條款](https://azure.microsoft.com/support/legal/services-terms/)中的條款。

**我可以為了匯入/匯出工作向 Microsoft 購買磁碟機嗎？**

- 	不可以。您必須針對匯入和匯出工作運送自己的磁碟機。

**我的包裹中應該包含什麼物品？**

- 僅限寄送硬碟機。請勿加入電源線或 USB 纜線等項目。

## 另請參閱

[使用 AzCopy 命令列公用程式傳輸資料](storage-use-azcopy)


[import-job-03]: ./media/storage-import-export-service-classic-portal/import-job-03.png
[export-job-03]: ./media/storage-import-export-service-classic-portal/export-job-03.png
[export-job-bitlocker-keys]: ./media/storage-import-export-service-classic-portal/export-job-bitlocker-keys.png

<!---HONumber=AcomDC_0128_2016-->