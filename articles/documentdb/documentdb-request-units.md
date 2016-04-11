<properties 
	pageTitle="DocumentDB 中的要求單位 | Microsoft Azure" 
	description="了解如何在 DocumentDB 中了解、指定及估計要求單位需求。" 
	services="documentdb" 
	authors="stephbaron" 
	manager="jhubbard" 
	editor="mimig" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/30/2016" 
	ms.author="stbaro"/>

#DocumentDB 中的要求單位

本文提供 [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) 中要求單位的概觀。

閱讀本文後，您將能夠回答下列問題：

-	什麼是要求單位和要求費用？
-	如何指定集合的要求單位容量？
-	如何估計應用程式的要求單位需求？
-	如果超過集合的要求單位容量，會發生什麼事？


##要求單位和要求費用
DocumentDB 藉由「保留」資源以滿足應用程式的輸送量需求，來提供快速且可預測的效能。因為應用程式會隨著時間載入和存取模式變化，所以 DocumentDB 可讓您輕鬆地增加或減少應用程式可用的保留輸送量。

有了 DocumentDB，保留的輸送量是根據每秒處理的要求單位來指定。您可以將要求單位想像為輸送量貨幣，因此您每秒可「保留」保證可供應用程式使用的要求單位數量。DocumentDB 中的每個作業 (寫入文件、執行查詢、更新文件) 都會耗用 CPU、記憶體和 IOPS。也就是說，每個作業都會產生「要求費用」，這是以「要求單位」來表示。了解影響要求單位費用的因素，以及您應用程式的輸送量需求，讓您能夠以最經濟實惠的方式來執行應用程式。

##指定要求單位容量
建立 DocumentDB 集合時，您可以指定想要保留給集合的每秒要求單位數 (RU)。建立集合之後，指定 RU 的完整配置會保留以供集合使用。保證每個集合都會有專用和隔離的輸送量特性。

請務必注意，DocumentDB 會在保留模型上運作；也就是您需要針對為集合所「保留」的輸送量支付費用，而不論該輸送量中有多少數量是主動「使用」的。不過請記住，當您應用程式的負載、資料及使用方式模式變更時，您可以透過 DocumentDB SDK 或使用 [Azure 入口網站](https://portal.azure.com)，輕鬆地相應增加和減少保留 RU 的數量。如需相應增加和減少輸送量的詳細資訊，請參閱 [DocumentDB 中的效能等級](documentdb-performance-levels.md)。

##要求單位的考量
在估計要保留給 DocumentDB 集合的要求單位數量時，請務必考量下列變數：

- **文件大小**。文件大小增加時，也會增加讀取或寫入資料所耗用的單位。
- **文件屬性計數**。假設預設會編製所有屬性的索引，撰寫文件所耗用的單位將會隨著屬性計數增加而增加。
- **資料一致性**。當使用強式或繫結失效的資料一致性層級時，將會耗用額外單位來讀取文件。
- **已編製索引的屬性**。每個集合上的索引原則會判定預設要編製哪些索引的屬性。您可以限制已編製索引的屬性數目或讓索引延遲編製，以減少要求單位的耗用量。
- **編製文件索引**。依預設會自動編製每份文件的索引，如果您選擇不要編製一些文件的索引，則會耗用較少的要求單位。
- **查詢模式**。查詢的複雜性會影響針對作業所耗用的要求單位數量。述詞數目、述詞性質、預測、UDF 數目，以及來源資料集的大小，全都會影響查詢作業的成本。
- **指令碼使用方式**。查詢、預存程序和觸發程序會根據所執行作業的複雜度來耗用要求單位。開發應用程式時，請檢查要求費用標頭，進一步了解每項作業如何耗用要求單位容量。

##估計輸送量需求
要求單位是要求處理成本的標準化量值。單一要求單位代表讀取 (透過自我連結或識別碼) 由 10 個唯一屬性值 (不包括系統屬性) 所組成的單一 1KB JSON 文件所需的處理容量。建立 (插入)、取代或刪除相同文件的要求將會耗用服務的更多處理，因此會耗用更多的要求單位。

> [AZURE.NOTE] 1KB 文件 1 個要求單位的基準，會對應至文件自我連結或識別碼的簡單 GET。

DocumentDB 服務的每個回應都會包括自訂標頭 (x-ms-request-charge)，其中包含要求所耗用的要求單位。此標頭也可以透過 DocumentDB SDK 進行存取。在 .NET SDK 中，RequestCharge 是 ResourceResponse 物件的屬性。針對查詢， Azure 入口網站中的 DocumentDB 查詢總管會提供已執行查詢的要求費用資訊。

![在查詢總管中檢查 RU 費用][1]

將此銘記於心，有一個估計應用程式所需之保留輸送量的方法是，記錄與根據應用程式所使用之代表性文件執行一般作業相關聯的要求單位費用，然後估計您預期每秒執行的作業數目。此外，請務必測量並包含一般查詢和 DocumentDB 指令碼使用方式。

>[AZURE.NOTE]如果您的文件類型與已編製索引之屬性的大小與數目截然不同，則請記錄與每個標準文件「類型」相關聯的適用作業要求單位費用。

例如：

1. 記錄建立 (插入) 標準文件的要求單位費用。 
2. 記錄讀取標準文件的要求單位費用。
3. 記錄更新標準文件的要求單位費用。
3. 記錄標準且常見文件查詢的要求單位費用。
4. 記錄應用程式所使用的任何自訂指令碼 (預存程序、觸發程序、使用者定義的函式) 的要求單位費用
5. 根據您預期每秒執行的作業估計數目，來計算必要的要求單位數。

##要求單位估計範例
請考量下列 ~1KB 文件：

	{
	 "id": "08259",
  	"description": "Cereals ready-to-eat, KELLOGG, KELLOGG'S CRISPIX",
  	"tags": [
    	{
      	"name": "cereals ready-to-eat"
    	},
    	{
      	"name": "kellogg"
    	},
    	{
      	"name": "kellogg's crispix"
    	}
	],
  	"version": 1,
  	"commonName": "Includes USDA Commodity B855",
  	"manufacturerName": "Kellogg, Co.",
  	"isFromSurvey": false,
  	"foodGroup": "Breakfast Cereals",
  	"nutrients": [
    	{
      	"id": "262",
      	"description": "Caffeine",
      	"nutritionValue": 0,
      	"units": "mg"
    	},
    	{
      	"id": "307",
      	"description": "Sodium, Na",
      	"nutritionValue": 611,
      	"units": "mg"
    	},
    	{
      	"id": "309",
      	"description": "Zinc, Zn",
      	"nutritionValue": 5.2,
      	"units": "mg"
    	}
  	],
  	"servings": [
    	{
      	"amount": 1,
      	"description": "cup (1 NLEA serving)",
      	"weightInGrams": 29
    	}
  	]
	}

>[AZURE.NOTE]文件在 DocumentDB 中會變小，因此系統針對上述文件計算出的大小會稍微少於 1KB。


下表顯示本文件中標準操作的概略要求單位費用 (概略的要求單位費用假設帳戶一致性層級會設定為「工作階段」且所有文件都已自動編制索引)：

作業|要求單位費用 
---|---
建立文件|~15 RU 
讀取文件|~1 RU
依識別碼查詢文件|~2.5 RU

此外，下表顯示應用程式中所使用之標準查詢的概略要求單位費用︰

查詢|要求單位費用|傳回的文件數目
---|---|--- 
依識別碼選取食物|~2.5 RU|1 
依製造商選取食物|~7 RU|7
依食物群組選取並依重量排序|~70 RU|100
選取食物群組中的前 10 個食物|~10 RU|10

>[AZURE.NOTE]RU 費用會根據傳回的文件數目而有所不同。

利用此資訊，我們可以根據預期每秒的作業和查詢數目，來估計此應用程式的 RU 需求︰

作業/查詢|每秒估計的數目|必要的 RU 數 
---|---|--- 
建立文件|10|150 
讀取文件|100|100 
依製造商選取食物|25|175 
依食物群組選取|10|700 
選取前 10 個|15|總共 150 個|155|1275

在此情況下，我們預期平均輸送量需求為 1,275 RU/秒。四捨五入至最接近 100 的數目，我們會針對此應用程式的集合佈建 1,300 RU/秒。

##超過保留的輸送量限制
您應該記得，要求單位耗用量是以每秒的速率來評估。對於超過集合上佈建的要求單位速率的應用程式，對於該集合的要求會受到節流控制，直到該速率降到預留層級以下。當節流發生時，伺服器將預先使用 RequestRateTooLarge (HTTP 狀態碼 429) 來結束要求，並傳回 x-ms-retry-after-ms 標頭，以指出使用者重試要求之前必須等候的時間量 (毫秒)。

	HTTP Status 429
	Status Line: RequestRateTooLarge
	x-ms-retry-after-ms :100

如果您使用 .NET 用戶端 SDK 和 LINQ 查詢，則大部分時間您都不再需要處理這個例外狀況，因為目前版本的 .NET 用戶端 SDK 會隱含地攔截這個回應、遵守伺服器指定的 retry-after 標頭，然後重試要求。除非有多個用戶端同時存取您的帳戶，否則下次重試將會成功。

如果您有多個用戶端會以高於要求速率的方式累積運作，則預設的重試行為可能會不敷使用，而用戶端將擲回 DocumentClientException (狀態碼 429) 到應用程式。在這類情況下，您可以考慮處理應用程式錯誤處理常式中的重試行為和邏輯，或為集合提高保留的輸送量。

##後續步驟

若要深入了解透過 Azure DocumentDB 保留輸送量的方式，請探索下列資源：
 
- [DocumentDB 價格](https://azure.microsoft.com/pricing/details/documentdb/)
- [管理 DocumentDB 容量](documentdb-manage.md) 
- [在 DocumentDB 中模型化資料](documentdb-modeling-data.md)
- [DocumentDB 效能等級](documentdb-partition-data.md)

若要深入了解 DocumentDB，請參閱 Azure DocumentDB [文件](https://azure.microsoft.com/documentation/services/documentdb/)。

[1]: ./media/documentdb-request-units/queryexplorer.png

<!---HONumber=AcomDC_0330_2016-->