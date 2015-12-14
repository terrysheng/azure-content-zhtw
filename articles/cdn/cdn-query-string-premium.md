<properties 
	pageTitle="CDN - 使用查詢字串控制 CDN 要求的快取行為 - 高階" 
	description="CDN 查詢字串快取可控制檔案內含查詢字串時的檔案快取方式。" 
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
	ms.date="12/02/2015" 
	ms.author="casoper"/>

#使用查詢字串控制 CDN 要求的快取行為 - 高階

> [AZURE.SELECTOR]
- [Standard](cdn-query-string.md)
- [Premium](cdn-query-string-premium.md)

##概觀

查詢字串快取可控制檔案內含查詢字串時的檔案快取方式。

> [AZURE.NOTE]標準與高階 CDN 層提供相同的查詢字串快取功能，但兩者的使用者介面不同。本文件說明**高階**層使用者介面。針對標準層，請參閱[使用查詢字串控制 CDN 要求的快取行為](cdn-query-string.md)。

提供三種可用模式：

- **標準快取**：此為預設模式。CDN 邊緣節點會將要求者發出的查詢字串，傳遞至第一個要求的來源並快取資產。快取的資產到期之前，所有從邊緣節點提供且針對該資產的後續查詢皆會忽略查詢字串。 
- **無快取**：在此模式中，系統不會於 CDN 邊緣節點快取使查詢字串的要求。邊緣節點會直接從來源擷取資產，然後透過每個要求將其傳遞給要求者。 
- **唯一快取**：此模式會將包含查詢字串的每個要求，視為具有專屬快取的唯一資產。例如，系統會於邊緣節點快取 *foo.ashx?q=bar* 要求的來源回應，並使用相同的查詢字串針對後續快取傳回。針對 *foo.ashx?q=somethingelse* 要求，系統會將其快取為具專屬存留時間的個別資產。
	
	>[AZURE.WARNING]若查詢字串包含會隨每個要求變更的參數 (例如工作階段識別碼或使用者名稱)，則不應使用此模式，因為這會導致快取點擊率極低。

##變更查詢字串快取設定

1. 在 CDN 設定檔刀鋒視窗中，按一下 [**管理**] 按鈕。

	![CDN 設定檔刀鋒視窗管理按鈕](./media/cdn-query-string-premium/cdn-manage-btn.png)
	
	此時即會開啟 CDN 管理入口網站。
	
2. 將滑鼠移至 [**HTTP 大型**] 索引標籤上，然後將滑鼠移至 [**快取設定**] 飛出視窗上。按一下 [**查詢字串快取**]。
	
	查詢字串快取選項隨即顯示。
	
	![CDN 查詢字串快取選項](./media/cdn-query-string-premium/cdn-query-string.png)
	
3. 進行選擇後，按一下 [**更新**] 按鈕。




	

<!---HONumber=AcomDC_1203_2015-->