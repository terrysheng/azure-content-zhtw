<properties 
	pageTitle="DocumentDB 和 Power BI 視覺效果教學課程 |Microsoft Azure" 
	description="了解如何使用 Power BI Desktop，透過 DocumentDB (完善管理的 NoSQL 文件資料庫) 中的 JSON 資料建立具深入資訊的報告。" 
	services="documentdb" 
	authors="h0n" 
	manager="jhubbard" 
	editor="mimig" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/22/2015" 
	ms.author="hawong"/>

# 使用 Power BI 視覺化 DocumentDB 資料
[PowerBI.com](https://powerbi.microsoft.com/) 是一項線上服務，您可以在其中建立及共用具有您和組織的重要資料的儀表板和報告。Power BI Desktop 是一項專用的報告撰寫工具，可讓您從各種資料來源擷取資料、合併和轉換資料、建立功能強大的報告和視覺效果，以及將報告發佈至 Power BI。透過最新版的 Power BI Desktop，您現在可以透過 Power BI 的 DocumentDB 連接器連接到您的 DocumentDB 帳戶。

在本教學課程中，我們將逐步解說在 Power BI Desktop 中連接到 DocumentDB 帳戶、瀏覽到我們要使用「瀏覽器」擷取資料的集合、使用 Power BI Desktop 查詢編輯器將 JSON 資料轉換成表格式格式，和建置報告並將其發佈至 PowerBI.com 等作業的步驟。

閱讀本文後，您將能夠回答下列問題：

-	如何使用 Power BI Desktop 以 DocumentDB 中的資料建置報告？ 
-	如何在 Power BI Desktop 中連接到 DocumentDB 帳戶？
-	如何在 Power BI Desktop 中從集合擷取資料？
-	如何在 Power BI Desktop 中轉換巢狀 JSON 資料？
-	如何在 PowerBI.com 中發佈及共用我的報告？ 

## 必要條件

在依照本文中的指示進行之前，請確定已具備下列項目：

- [最新版的 Power BI 桌面](https://powerbi.microsoft.com/desktop)
- 我們的示範帳戶或您 Azure DocumentDB 帳戶中之資料的存取權。 
	- 示範帳戶會填入此教學課程中所顯示的火山資料。此示範帳戶未受限於任何 SLA，而是僅供示範之用。我們保留對此示範帳戶隨時進行修改的權利，包括 (但不限於) 終止帳戶、變更金鑰、限制存取權、變更和刪除資料，不事先通知或告知原因。 
		- URL：https://analytics.documents.azure.com
		- 唯讀金鑰：MSr6kt7Gn0YRQbjd6RbTnTt7VHc5ohaAFu7osF0HdyQmfR+YhwCH2D2jcczVIR1LNK3nMPNBD31losN7lQ/fkw==
	- 或者，若要建立您自己的帳戶，請參閱[使用 Azure Preview 入口網站建立 DocumentDB 資料庫帳戶](https://azure.microsoft.com/documentation/articles/documentdb-create-account/)。然後，若要取得類似於本教學課程所使用的範例火山資料 (但不包含 GeoJSON 區塊)，請參閱 [NOAA 網站](https://www.ngdc.noaa.gov/nndc/struts/form?t=102557&s=5&d=5)，然後使用 [DocumentDB 資料移轉工具](https://azure.microsoft.com/documentation/articles/documentdb-import-data/)匯入資料。


若要在 PowerBI.com 上共用您的報告，您必須有 PowerBI.com 中的帳戶。若要深入了解 Power BI for Free 和 Power BI Pro，請造訪 [https://powerbi.microsoft.com/pricing](https://powerbi.microsoft.com/pricing)。

## 現在就開始吧
在本教學課程中，我們假設您是研究世界各地火山的地質學家。火山資料儲存在 DocumentDB 帳戶中，JSON 文件看起來如下。

	{
    	"Volcano Name": "Rainier",
   		"Country": "United States",
  		"Region": "US-Washington",
  		"Location": {
    		"type": "Point",
    		"coordinates": [
      		-121.758,
      		46.87
    		]
  		},
  		"Elevation": 4392,
  		"Type": "Stratovolcano",
  		"Status": "Dendrochronology",
  		"Last Known Eruption": "Last known eruption from 1800-1899, inclusive"
	}	

您想要從 DocumentDB 帳戶擷取火山資料，並產生如下的互動式 Power BI 報告。

![Power BI Desktop 火山報告](./media/documentdb-powerbi-visualize/pbireportfinal.png)

準備好要試試看了嗎？ 現在就開始吧。

## 連接到 DocumentDB 帳戶

1. 在您的工作站上執行 Power BI Desktop。
2. Power BI Desktop 啟動時，會顯示 [歡迎使用] 畫面。

	![Power BI Desktop 歡迎使用畫面](./media/documentdb-powerbi-visualize/welcomescreen.png)

3. 您可以 [取得資料]、檢視 [最近的來源]，或直接從 [歡迎使用] 畫面 [開啟其他報告]。按一下右上角的 X，以關閉畫面。Power BI Desktop 的 [報告] 檢視隨即顯示。

	![Power BI Desktop 報告檢視](./media/documentdb-powerbi-visualize/pbireportview.png)

4. 選取 [首頁] 功能區，然後按一下 [取得資料]。此時應會出現 [取得資料] 視窗。

5. 按一下 [Azure]、選取 [Microsoft Azure DocumentDB (Beta)]，然後按一下 [連接]。此時應會出現 [Microsoft Azure DocumentDB 連接] 視窗。

	![Power BI Desktop 取得資料](./media/documentdb-powerbi-visualize/pbigetdata.png)

6. 依照下列方式指定您要從中擷取資料的 DocumentDB 帳戶端點 URL，然後按一下 [確定]。您可以從 Azure Preview 入口網站之 [金鑰] 刀鋒視窗的 [URI] 方塊中擷取 URL，或使用上面提供的示範帳戶資訊。如需詳細資訊，請參閱[金鑰](documentdb-manage-account.md#keys)。


	*注意。在本教學課程中，我們不會指定資料庫名稱、集合名稱或 SQL 陳述式，因為這些欄位是選用的。我們將使用「瀏覽器」來選取資料庫和集合，以識別資料來自何處。*

    ![Power BI Desktop 連接視窗](./media/documentdb-powerbi-visualize/pbiconnectwindow.png)

7. 如果您是第一次連接到此端點，系統會提示您提供帳戶金鑰。輸入帳戶金鑰，然後按一下 [連接]。
	
	*注意。建議您在建置報告時使用唯讀金鑰。這樣可避免非必要地將主要金鑰暴露於潛在的安全性風險下。唯讀金鑰可從 Azure Preview 入口網站的 [唯讀金鑰] 刀鋒視窗取得，或使用上面提供的示範帳戶資訊取得。*

    ![Power BI Desktop 帳戶金鑰](./media/documentdb-powerbi-visualize/pbidocumentdbkey.png)

8. 順利連接帳戶後，會出現 [瀏覽器]。[瀏覽器] 會顯示帳戶下的資料庫清單。
9. 按一下並展開作為報告資料來源的資料庫。此時會在資料庫下顯示集合清單。  

10. 現在，請選取您要從中擷取資料的集合，例如 volcano1。

	*注意。[預覽] 窗格會顯示 [記錄] 項目的清單。一份文件會顯示為 Power BI 中的 [記錄] 類型。同樣地，文件內的巢狀 JSON 區塊也是 [記錄]。*

    ![Power BI Desktop 瀏覽器](./media/documentdb-powerbi-visualize/pbinavigator.png)

11. 按一下 [編輯] 以啟動 [查詢編輯器]，以便轉換資料。

## 簡維化和轉換 JSON 文件
1. 在 Power BI 查詢編輯器中，您應該會在中央窗格內看到 [文件] 資料行。![Power BI Desktop 查詢編輯器](./media/documentdb-powerbi-visualize/pbiqueryeditor.png)

2. 按一下位於 [文件] 資料行標頭右側的展開器。此時會出現含有欄位清單的內容功能表。選取您的報告所需的欄位，例如火山名稱、國家、區域、位置、高度、類型、狀態和已知的上次爆發時間，然後按一下 [確定]。
    
	![Power BI Desktop 展開文件](./media/documentdb-powerbi-visualize/pbiqueryeditorexpander.png)

3. 中央窗格會顯示所選欄位的結果預覽。

	![Power BI Desktop 簡維化結果](./media/documentdb-powerbi-visualize/pbiresultflatten.png)

4. 在我們的範例中，[位置] 屬性是文件中的 GeoJSON 區塊。如您所見，[位置] 顯示為 Power BI Desktop 中的 [記錄] 類型。
5. 按一下位於 [位置] 資料行標頭右側的展開器。此時會出現含有類型和座標欄位的內容功能表。我們選取座標欄位，然後按一下 [確定]。

    ![Power BI Desktop 位置記錄](./media/documentdb-powerbi-visualize/pbilocationrecord.png)

6. 中央窗格現在會顯示 [清單] 類型的座標資料行。如本教學課程一開始所說明，本教學課程中的 GeoJSON 資料屬於 Point 類型，具有座標陣列中所記錄的緯度和經度值。

	*注意：coordinates[0] 元素代表經度，coordinates[1] 則代表緯度。* ![Power BI Desktop 座標清單](./media/documentdb-powerbi-visualize/pbiresultflattenlist.png)

7. 為了將座標陣列簡維化，我們將建立名為 LatLong 的 [自訂資料行]。選取 [新增資料行] 功能區，然後按一下 [新增自訂資料行]。此時應會出現 [新增自訂資料行] 視窗。

8. 提供新資料行的名稱，例如 LatLong。

9. 接下來，指定新資料行的自訂公式。在我們的範例中，我們將依照下列方式使用以下公式，串連以逗號分隔的緯度和經度值：Text.From([coordinates]{1})&","&Text.From([coordinates]{0})。按一下 [確定]。
	
    *注意。如需資料分析運算式 (DAX) (包括 DAX 函數) 的詳細資訊，請瀏覽 [Power BI Desktop 中的 DAX 基礎](https://support.powerbi.com/knowledgebase/articles/554619-dax-basics-in-power-bi-desktop)。*

    ![Power BI Desktop 自訂資料行](./media/documentdb-powerbi-visualize/pbicustomlatlong.png)

10. 現在，中央窗格會顯示新的 LatLong 資料行，並且已填入以逗號分隔的緯度和經度值。

	![Power BI Desktop 自訂 LatLong](./media/documentdb-powerbi-visualize/pbicolumnlatlong.png)

11. 現在我們已將資料簡維化成表格式格式。您可以利用查詢編輯器中所有可用的功能，將 DocumentDB 中的資料圖形化及進行轉換。例如，您可以在 [首頁] 功能區上變更 [資料類型]，以將 [高度] 的資料類型變更為 [十進位數字]。

    ![Power BI Desktop 變更類型](./media/documentdb-powerbi-visualize/pbichangetype.png)

12. 按一下 [關閉並套用] 以儲存資料模型。
    
    ![Power BI Desktop 關閉套用](./media/documentdb-powerbi-visualize/pbicloseapply.png)

## 建置報告
[Power BI Desktop 報告] 檢視可作為您開始建立報告的起始點。您可以將欄位拖放到 [報告] 畫布上，以建立報告。

![Power BI Desktop 報告檢視](./media/documentdb-powerbi-visualize/pbireportview2.png)
 
在 [報告] 檢視中，您應該會看到：

 1. [欄位] 窗格，您會在這裡看到資料模型清單，以及您可以用於報告的欄位。

 2. [視覺效果] 窗格。一份報告可以包含一或多個視覺效果。請從 [視覺效果] 窗格中選擇適合本身需求的視覺化類型。

 3. [報告] 畫布，您可以在此處建置報告的視覺效果。

 4. [報告] 頁面。您可以在 Power BI Desktop 中心增多個報告頁面。

以下說明建立簡單的互動式地圖檢視報告的基本步驟。

1. 在此處的範例中，我們將建立會顯示每個火山所在位置的地圖檢視。在 [視覺效果] 窗格中，按一下地圖視覺類型，如上方的螢幕擷取畫面所強調顯示。您應該會看到地圖視覺化類型繪製於 [報告] 畫布上。[視覺效果] 窗格也應該會顯示一組與地圖視覺化類型相關的屬性。

2. 現在，將 LatLong 欄位從 [欄位] 窗格拖放到 [視覺效果] 窗格中的 [位置] 屬性上。
3. 接下來，將 [火山名稱] 欄位拖放到 [圖例] 屬性上。  

4. 然後，將 [高度] 欄位拖放到 [值] 屬性上。

5. 您現在應該會看到地圖視覺化顯示一組表示每個火山所在位置的泡泡，且泡泡的大小會與火山的高度相關聯。

6. 現在您已建立基本報告。您可以新增更多視覺效果，以進一步自訂報告。在本例中，我們新增 [火山類型] 交叉分析篩選器，讓報告更具互動性。

    ![Power BI Desktop 報告結果](./media/documentdb-powerbi-visualize/pbireportfinal.png)

## 發佈和共用您的報告
若要共用您的報告，您必須有 PowerBI.com 中的帳戶。

1. 在 Power BI Desktop 中，按一下 [首頁] 功能區。
2. 按一下 [發佈]。系統會提示您輸入 PowerBI.com 帳戶的使用者名稱和密碼。
3. 認證經過驗證後，報告即會發佈至您的 PowerBI.com 帳戶。
4. 然後，您就可以在 PowerBI.com 中共用您的報告。

## 後續步驟
- 若要深入了解 Power BI，請按一下 [這裡][](https://support.powerbi.com/knowledgebase)。
- 若要深入了解 DocumentDB，請按一下[這裡](https://azure.microsoft.com/documentation/services/documentdb/)。

<!---HONumber=Oct15_HO4-->