<properties
	pageTitle="清除 Azure CDN 端點"
	description="了解如何清除 CDN 端點的所有快取內容"
	services="cdn"
	documentationCenter=".NET"
	authors="camsoper"
	manager="erikre"
	editor=""/>

<tags
	ms.service="cdn"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/25/2016" 
	ms.author="casoper"/>

# 清除 Azure CDN 端點

## 概觀

Azure CDN 邊緣節點會快取資產，直到資產的存留時間 (TTL) 到期。資產的 TTL 到期之後，當用戶端從邊緣節點要求資產時，邊緣節點會建立資產新的更新的複本以服務用戶端的要求並儲存重新整理快取。

有時您可能想要清除所有邊緣節點的快取內容，並強制它們全部擷取新的更新的資產。可能是因為您的 Web 應用程式更新，或快速更新包含不正確資訊的資產。

本教學會逐步引導您清除端點的所有邊緣節點的資產。

## 逐步介紹

1. 在 [Azure 入口網站](https://portal.azure.com) 中，瀏覽到包含您希望清除之端點的 CDN 設定檔。

2. 在 [CDN 設定檔] 刀鋒視窗中，按一下 [清除] 按鈕。

	![CDN 設定檔刀鋒視窗](./media/cdn-purge-endpoint/cdn-profile-blade.png)

	會開啟 [清除] 刀鋒視窗。

	![CDN 清除刀鋒視窗](./media/cdn-purge-endpoint/cdn-purge-blade.png)

3. 在 [清除] 刀鋒視窗中，從 [URL] 下拉式清單中選取希望清除的服務位址。

	![清除表單](./media/cdn-purge-endpoint/cdn-purge-form.png)

	> [AZURE.NOTE] 您也可以按一下 [CDN 端點] 刀鋒視窗中的 [清除] 按鈕來開啟 [清除] 刀鋒視窗。在此情況下，[URL] 欄位會預先填入該特定端點的服務位址。

4. 選取您希望從邊緣節點清除的資產。如果您希望清除所有資產，請按一下 [清除] 核取方塊。或者，在 [路徑] 文字方塊中輸入每個您希望清除之資產的完整路徑以清除 (例如，*/pictures/kitten.png*)。

	> [AZURE.TIP] 輸入之後會出現更多 [路徑] 文字方塊，讓您建立多個資產的清單。按一下刪節號 (...) 按鈕可以將資產從清單刪除。
	>
	> 路徑必須為相對 URL。星號 (*) 可做為萬用字元。

5. 按一下 [清除] 按鈕。

	![清除按鈕](./media/cdn-purge-endpoint/cdn-purge-button.png)


## 另請參閱
- [在 Azure CDN 端點上預先載入資產](cdn-preload-endpoint.md)
- [Azure CDN REST API 參考資料 - 清除或預先載入端點](https://msdn.microsoft.com/library/mt634451.aspx)

<!---HONumber=AcomDC_0302_2016-->