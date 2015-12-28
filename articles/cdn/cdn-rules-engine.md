<properties 
	pageTitle="CDN - 使用規則引擎覆寫預設的 HTTP 行為" 
	description="規則引擎可讓您自訂 HTTP 要求的處理方式，例如封鎖傳遞特定類型的內容、定義快取原則及修改 HTTP 標頭。" 
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

# 使用規則引擎覆寫預設的 HTTP 行為

## 概觀

規則引擎可讓您自訂 HTTP 要求的處理方式，例如封鎖傳遞特定類型的內容、定義快取原則及修改 HTTP 標頭。本教學課程將示範如何建立用以變更 CDN 資產之快取行為的規則。

> [AZURE.NOTE]規則引擎是高階 CDN 層的一項功能。如需標準和高階 CDN 功能的比較，請參閱 [Azure CDN 概觀](cdn-overview.md)。

## 教學課程

1. 在 [CDN 設定檔] 刀鋒視窗中，按一下 [管理] 按鈕。

	![[CDN 設定檔] 刀鋒視窗的 [管理] 按鈕](./media/cdn-rules-engine/cdn-rules-manage-btn.png)
	
	隨即開啟 CDN 管理入口網站。
	
2. 依序按一下 [HTTP 大型] 索引標籤和 [規則引擎]。
	
	隨即顯示新規則的選項。
	
	![CDN 新規則選項](./media/cdn-rules-engine/cdn-new-rule.png)

3. 在 [名稱/描述] 文字方塊中輸入名稱。

4. 識別將套用此規則的要求類型。預設會選取 [永遠] 相符條件。本教學課程將使用 [永遠]，因此請維持選取。

	![CDN 相符條件](./media/cdn-rules-engine/cdn-request-type.png)
	
	>[AZURE.TIP]下拉式清單中提供許多類型的相符條件。按一下相符條件左側的藍色資訊圖示，即會詳細說明目前選取的條件。
	>
	>如需完整相符條件清單的詳細資訊，請參閱[規則引擎相符條件和功能詳細資訊](cdn-rules-engine-details.md#match-conditions)。
	
5.  按一下 [功能] 旁的 **+** 按鈕，以新增功能。在左側下拉式清單中，選取 [強制內部最大壽命]。在出現的文字方塊中，輸入 **300**。保留其餘預設值。

	![CDN 功能](./media/cdn-rules-engine/cdn-new-feature.png)

	>[AZURE.NOTE]如同相符條件，按一下新功能左側的藍色資訊圖示會顯示這項功能的詳細資訊。在 [強制內部最大壽命] 中，我們將覆寫資產的 **Cache-Control** 和 **Expires** 標頭，以控制 CDN 邊緣節點何時要從原始來源重新整理資產。我們的範例為 300 秒，表示 CDN 邊緣節點會快取資產 5 分鐘，再從其原始來源重新整理資產。
	>
	>如需完整功能清單的詳細資訊，請參閱[規則引擎相符條件和功能詳細資訊](cdn-rules-engine-details.md#features)。
	
6.  按一下 [加入] 按鈕，以儲存新規則。新規則現在正在等待核准。核准後，狀態會從 [暫止 XML] 變更為 [使用中 XML]。

## 考量

- 多項規則的列出順序會影響規則的處理方式。後一項規則可能會覆寫前一項規則所指定的動作。

## 另請參閱
* [規則引擎相符條件和功能詳細資料](cdn-rules-engine-details.md)
* [Azure CDN 概觀](cdn-overview.md)
* [Microsoft Azure CDN 中的即時統計資料](cdn-real-time-stats.md)
* [進階 HTTP 報告](cdn-advanced-http-reports.md)
* [分析邊緣效能](cdn-edge-performance.md)


	

<!---HONumber=AcomDC_1217_2015-->