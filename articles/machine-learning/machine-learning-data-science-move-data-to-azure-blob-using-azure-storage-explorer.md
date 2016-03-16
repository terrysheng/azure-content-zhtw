<properties 
	pageTitle="使用 Azure 儲存體總管從 Azure Blob 儲存體來回移動資料 | Microsoft Azure" 
	description="使用 Azure 儲存體總管從 Azure Blob 儲存體來回移動資料" 
	services="machine-learning,storage" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/08/2016" 
	ms.author="bradsev" />

# 使用 Azure 儲存體總管從 Azure Blob 儲存體來回移動資料

## 簡介 

Azure 儲存體總管是一個免費的 Windows 型工具，可用以檢查及更改 Azure 儲存體帳戶中的資料。本主題說明如何使用它來於 Azure Blob 儲存體中上傳及下載資料。您可以從 [Azure Storage Explorer (Azure 儲存體總管)](http://azurestorageexplorer.codeplex.com/) 下載該工具。

以下是有關從 Azure Blob 儲存體來回移動資料所使用之技術的指引連結：

[AZURE.INCLUDE [blob-storage-tool-selector](../../includes/machine-learning-blob-storage-tool-selector.md)]


> [AZURE.NOTE] 如果您是使用以 [Azure 中的資料科學虛擬機器](machine-learning-data-science-virtual-machines.md)提供的指令碼所設定的 VM，則 Azure 儲存體總管已經安裝在 VM 上。

> [AZURE.NOTE] 如需 Azure Blob 儲存體的完整介紹，請參閱 [Azure Blob 基本概念](../storage-dotnet-how-to-use-blobs.md)和 [Azure Blob 服務](https://msdn.microsoft.com/library/azure/dd179376.aspx)。

## 必要條件

本文件假設您擁有 Azure 訂用帳戶、儲存體帳戶和該帳戶的對應儲存體金鑰。上傳/下載資料之前，您必須知道 Azure 儲存體帳戶名稱和帳戶金鑰。

- 若要設定 Azure 訂用帳戶，請參閱[免費試用一個月](https://azure.microsoft.com/pricing/free-trial/)。
- 如需建立儲存體帳戶的指示，以及取得帳戶和金鑰的資訊，請參閱[關於 Azure 儲存體帳戶](../storage-create-storage-account.md)。


<a id="explorer"></a>
## 使用 Azure 儲存體總管 

下列步驟說明如何使用 Azure 儲存體總管上傳/下載資料。

1.  啟動 Azure 儲存體總管 
2.  如果您想要存取的儲存體帳戶尚未新增至 Azure 儲存體總管，按一下 [新增帳戶] 按鈕來新增帳戶。如果已經新增，可從 [--選取儲存體帳戶--] 下拉式清單中選取帳戶。![建立工作區][1] <br>
3. 輸入儲存體帳戶名稱和儲存體帳戶金鑰，然後按一下 [新增儲存體帳戶]。您可以新增多個儲存體帳戶，各個帳戶將會顯示於個別的索引標籤上。此儲存體帳戶下方的容器會顯示於左面板中。選取容器，以在右面板查看容器中的 Blob。![建立工作區][2] <br> ![建立工作區][3] <br>
4. 按一下 [上傳] 按鈕來上傳資料。從檔案系統中選取一或多個要上傳的檔案，然後按一下 [開啟]，開始上傳檔案。
5. 選取對應容器中的 Blob，然後按一下 [下載] 按鈕來下載資料。

<!-- Images -->

[1]: ./media/machine-learning-data-science-move-azure-blob/data-science-process-uploading-data-to-blob-storage-img1.png
[2]: ./media/machine-learning-data-science-move-azure-blob/data-science-process-uploading-data-to-blob-storage-img2.png
[3]: ./media/machine-learning-data-science-move-azure-blob/data-science-process-uploading-data-to-blob-storage-img3.png

<!---HONumber=AcomDC_0211_2016-->