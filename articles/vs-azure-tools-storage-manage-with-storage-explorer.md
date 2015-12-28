<properties
	pageTitle="使用儲存體總管管理 Azure 儲存體資源 (預覽) | Microsoft Azure"
	description="描述如何使用 Microsoft Azure 儲存體總管 (預覽) 來建立和管理 Azure 儲存體資源。"
	services="visual-studio-online"
	documentationCenter="na"
	authors="kempb"
	manager="douge"
	editor="tlee" />

 <tags
	ms.service="visual-studio-online"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="na"
	ms.date="11/17/2015"
	ms.author="kempb" />

# 使用儲存體總管管理 Azure 儲存體資源 (預覽)

Microsoft Azure 儲存體總管 (預覽) 是獨立的工具，可協助您輕鬆地管理 Azure 儲存體帳戶。它在您想要快速地管理 Azure 入口網站外部的儲存體的情況下，相當有用，例如當您正在 Visual Studio 中開發應用程式時。此預覽版本可讓您輕鬆地使用 Blob 儲存體。您可以建立和刪除容器、上傳、下載及刪除 blob，並搜尋所有容器和 blob。進階的功能可讓開發人員和操作員使用 SAS 金鑰和原則。Windows 開發人員也可以使用 Azure 儲存體模擬器，利用本機開發儲存體帳戶來測試程式碼。

若要檢視或管理儲存體總管中的儲存體資源，您必須能夠以您的訂用帳戶或外部儲存體帳戶存取 Azure 儲存體帳戶。如果沒有儲存體帳戶，只需要幾分鐘的時間就可以建立帳戶。如果您有 MSDN 訂用帳戶，請參閱 [Visual Studio 訂閱者的每月 Azure 點數](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)。否則，請參閱[免費試用一個月](https://azure.microsoft.com/pricing/free-trial/)。

## 管理 Azure 帳戶和訂用帳戶

若要在 [儲存體總管] 中查看您的 Azure 儲存體資源，您需要登入具有一或多個作用中訂用帳戶的 Azure 帳戶。如果您有一個以上的 Azure 帳戶，您可以將它們加入 [儲存體總管] 中，然後選擇您想要包含在 [儲存體總管] 資源檢視中的訂用帳戶。如果您未曾使用過 Azure，或尚未將必要的帳戶加入至 Visual Studio，將會提示您登入 Azure 帳戶。

### 將 Azure 帳戶新增至儲存體總管

1.	在 [儲存體總管] 工具列上選擇 [設定] (齒輪) 圖示。
1.	選擇 [新增帳戶] 連結。登入您想要瀏覽儲存體資源的 Azure 帳戶。帳戶選擇器下拉式清單中應該已選取您剛才加入的帳戶。該帳戶的所有訂用帳戶會出現在帳戶項目下。

	![][0]

1.	選取您想要瀏覽之訂用帳戶的核取方塊，然後選擇 [套用] 按鈕。

	![][1]

	選取的訂用帳戶的 Azure 儲存體資源會出現在 [儲存體總管] 中。

### 附加外部儲存體

1. 取得您想要附加的儲存體帳戶的帳戶名稱和金鑰。
	1.	在 Azure Preview 入口網站中，選擇想要附加的儲存體帳戶。
	1.	在 Azure Preview 入口網站中的 [設定] 窗格的 [管理] 區段，選擇 [金鑰] 按鈕。
	1.	複製 [儲存體帳戶名稱] 和 [主要存取金鑰] 值。

		![][2]

1.	在 [儲存體總管] 的 [儲存體帳戶] 節點的捷徑功能表上，選擇 [附加外部儲存體] 命令。

	![][3]

1. 在 [帳戶名稱] 方塊中輸入儲存體帳戶名稱，在 [帳戶金鑰] 方塊中輸入主要存取金鑰。選擇 [確定] 按鈕以繼續。

	![][4]

	外部儲存體會出現在儲存體總管中。

	![][5]

1. 若要移除外部儲存體，在外部儲存體的捷徑功能表上選擇 [卸離] 命令。

	![][6]

## 檢視和瀏覽儲存體資源

若要在 [儲存體總管] 中瀏覽至 Azure 儲存體資源並檢視其資訊，請展開儲存體類型，然後選擇資源。所選取資源的相關資訊會出現在 [儲存體總管] 底部的 [動作] 和 [屬性] 索引標籤上。

![][7]

-	[動作] 索引標籤會顯示您在 [儲存體總管] 中可針對選取的儲存體資源採取的動作，例如開啟、複製或刪除。[動作] 也會顯示在資源的捷徑功能表上。

-	[屬性] 索引標籤會顯示儲存體資源的屬性，例如其類型、地區設定、相關聯的資源群組和 URL。

所有儲存體帳戶都有 [在入口網站中開啟] 這個動作。當您選擇此動作時，[儲存體總管] 會在 Azure Preview 入口網站中顯示選取的儲存體帳戶。

根據選取的資源而定，也會出現其他動作和屬性值。例如，Blob 容器、佇列和資料表節點都有 [建立] 動作。個別項目 (例如 blob 容器) 具有下列動作：[開啟]、[刪除] 和 [取得共用存取簽章]。當您選擇儲存體帳戶 blob 時，將會出現開啟 blob 編輯器的動作。

## 搜尋儲存體帳戶和 blob 容器

若要在 Azure 訂用帳戶中尋找具有特定名稱的儲存體帳戶和 blob 容器，請在 [儲存體總管] 的 [搜尋] 方塊中輸入名稱。

![][8]

當您在 [搜尋] 方塊中輸入字元時，只有符合這些字元的儲存體帳戶和 blob 容器才會出現在資源樹狀目錄中。若要清除搜尋，請在 [搜尋] 方塊中選擇 [x] 按鈕。

## 編輯儲存體帳戶

若要新增或變更儲存體帳戶的內容，請針對該儲存體類型選擇 [開啟編輯器] 命令。您可以選擇所選取項目的捷徑功能表上的動作，或者選擇 [儲存體總管] 底部的 [動作] 索引標籤上的動作。

![][9]

您可以建立或刪除 Blob 容器、佇列和資料表。您也可以選擇 [開啟 Blob 容器編輯器] 動作，編輯 [儲存體總管] 中的 blob。

### 編輯 Blob 容器

1.	選擇 [開啟 Blob 容器編輯器] 動作。[Blob 容器編輯器] 會出現在右窗格中。

	![][10]

1.	選擇 [上傳] 按鈕，然後選擇 [上傳檔案] 命令。

	![][11]

	如果您想要上傳的檔案是在單一資料夾中，您可以改為選擇 [上傳資料夾] 命令。

1. 在 [上傳檔案] 對話方塊中，選擇 [檔案] 方塊右側的省略符號 (**...**) 按鈕，以選取您想要上傳的檔案。然後，選擇您想要上傳的 blob 類型 (區塊、頁面或附加)。如果您想要的話，可以選擇將檔案上傳至 blob 容器中的資料夾。在 [上傳到資料夾 (選擇性)] 方塊中輸入資料夾的名稱。如果資料夾不存在，系統就會加以建立。

	![][12]

	在下列螢幕擷取畫面中，三個映像檔案已上傳到 [映像] blob 容器中名為 [我的新檔案] 的新資料夾。

	![][13]

	Blob 編輯器工具列上的按鈕可讓您選取、下載、開啟、複製和刪除檔案等等。對話方塊底部的 [活動] 窗格會顯示您的作業是否成功，並可讓您從檢視中僅移除成功的活動，或完全清除窗格。選擇已上傳檔案旁邊的 [+] 圖示，以檢視已上傳檔案的詳細清單。

## 建立共用存取簽章 (SAS)

對於某些作業而言，您可能需要 SAS 才能存取儲存體資源。您可以使用 [儲存體總管] 建立。

1.	選取您要為其建立 SAS 的項目，然後在 [動作] 窗格或項目的捷徑功能表上，選擇 [取得共用存取簽章] 命令。

	![][14]

1.	在 [共用存取簽章] 對話方塊中，選擇原則、開始和到期日期及時區。此外，選取您想要的資源的存取層級核取方塊，例如唯讀、讀寫等等。完成時，選擇 [建立] 按鈕以建立 SAS。

	![][15]

1.	[共用存取簽章] 對話方塊會列出您可以用來存取儲存體資源的容器及 URL 和 QueryStrings。選擇 [複製] 按鈕以複製字串。

	![][16]

## 管理 SAS 和權限

若要控制對 blob 容器的存取權，您可以選擇 [管理存取控制清單] 和 [設定公用存取層級] 命令。

-	[管理存取控制清單] 可讓您對選取的 blob 容器新增、編輯和移除原則 (使用者是否可以讀取、寫入等等)。
-	[設定公用存取層級] 可讓您決定多少存取公用使用者可以取得資源。  

-

1.	選擇 blob 容器，然後在捷徑功能表上或 [動作] 窗格中選擇 [管理存取控制清單] 命令。

	![][17]

1.	在 [存取控制清單] 對話方塊中，選擇 [新增] 按鈕以新增存取原則。選擇存取原則，然後為它選取權限。完成時，請選擇 [儲存] 按鈕。

	![][18]

1.	若要設定 blob 容器的存取層級，在 [儲存體總管] 中選擇它，然後在捷徑功能表上或 [動作] 窗格中選擇 [設定公用存取層級] 命令。

	![][19]

1.	在 [設定容器公用存取層級] 對話方塊中，選取您想要提供給公用使用者的存取層級的選項按鈕，然後選擇 [套用] 按鈕。

	![][20]

## 後續步驟
深入了解 Azure 儲存體服務中的功能，方法是閱讀 [Microsoft Azure 儲存體簡介](storage-introduction.md)中的文章。

[0]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AddAccount1c.png
[1]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AddAccount2c.png
[2]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/External1c.png
[3]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/External2c.png
[4]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/External3c.png
[5]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/External4c.png
[6]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/External5c.png
[7]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Navigatec.png
[8]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Searchc.png
[9]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Edit1c.png
[10]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Edit2c.png
[11]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Edit3c.png
[12]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Edit4c.png
[13]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Edit5c.png
[14]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/SAS1c.png
[15]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/SAS2c.png
[16]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/SAS3c.png
[17]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ManageSAS1c.png
[18]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ManageSAS2c.png
[19]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ManageSAS3c.png
[20]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ManageSAS4c.png

<!---HONumber=AcomDC_1217_2015-->