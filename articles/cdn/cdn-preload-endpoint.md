<properties 
	pageTitle="在 Azure CDN 端點上預先載入資產" 
	description="了解如何預先載入 CDN 端點上的快取內容。" 
	services="cdn" 
	documentationCenter=".NET" 
	authors="camsoper" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cdn" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/20/2016" 
	ms.author="casoper"/>
	
# 在 Azure CDN 端點上預先載入資產

本教學課程將逐步引導您在 Azure CDN 邊緣節點上預先載入快取的所有內容。

## 逐步介紹

1. 在 [Azure 入口網站](http://portal.azure.com) 中，瀏覽到包含您希望預載之端點的 CDN 設定檔。設定檔刀鋒視窗隨即開啟。

2. 按一下清單中的端點。端點刀鋒視窗隨即開啟。

3. 從 CDN 端點刀鋒視窗，按一下 [載入] 按鈕。
	
	![CDN 端點刀鋒視窗](./media/cdn-preload-endpoint/cdn-endpoint-blade.png)
	
	[載入] 刀鋒視窗隨即開啟。
	
	![CDN 載入刀鋒視窗](./media/cdn-preload-endpoint/cdn-load-blade.png)
	
4. 在 [路徑] 文字方塊中輸入每個您希望載入之資產的完整路徑 (例如，/pictures/kitten.png)。

	> [AZURE.TIP]輸入之後會出現更多 [路徑] 文字方塊，讓您建立多個資產的清單。按一下刪節號 (...) 按鈕可以將資產從清單刪除。
	>
	> 路徑必須為相對 URL。星號 (*) 可做為萬用字元。
	
    ![載入按鈕](./media/cdn-preload-endpoint/cdn-load-paths.png)
    
5. 按一下 [載入] 按鈕。

	![載入按鈕](./media/cdn-preload-endpoint/cdn-load-button.png)
	

## 另請參閱
- [清除 Azure CDN 端點](cdn-purge-endpoint.md)
- [Azure CDN REST API 參考資料 - 清除或預先載入端點](https://msdn.microsoft.com/library/mt634451.aspx)

<!---HONumber=AcomDC_0121_2016-->