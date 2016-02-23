<properties 
	pageTitle="建立 DocumentDB 資料庫集合 | Microsoft Azure" 
	description="了解如何使用 Azure DocumentDB 的線上服務入口網站 (也就是雲端式 NoSQL 文件資料庫)，來建立 JSON 文件集合。立即取得免費試用版。" 
	services="documentdb" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="monicar" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="12/18/2015" 
	ms.author="mimig"/>

# 如何使用 Azure 入口網站建立 DocumentDB 集合

若要使用 Microsoft Azure DocumentDB，您必須擁有 [DocumentDB 帳戶](documentdb-create-account.md)、[資料庫](documentdb-create-database.md)、集合和文件。本主題說明如何在 Azure 入口網站中建立 DocumentDB 集合。

不確定集合是什麼嗎？ 請參閱＜[什麼是 DocumentDB 集合？](#what-is-a-documentdb-collection)＞一節。

![螢幕擷取畫面，其中在 [資料庫] 透鏡裡反白顯示動態工具列的 [DocumentDB 帳戶]、[DocumentDB 帳戶] 刀鋒視窗中的帳戶和 [DocumentDB 帳戶] 刀鋒視窗中的資料庫](./media/documentdb-create-collection/docdb-database-creation-1-3.png)

1.  在 [Azure 入口網站](https://portal.azure.com/)的動態工具列中，按一下 [DocumentDB 帳戶]。 

2.  在 [DocumentDB 帳戶] 刀鋒視窗中，選取要新增集合的帳戶。如果您沒有任何列出的帳戶，您需要[建立 DocumentDB 帳戶](documentdb-create-account.md)。

3. 在所選取帳戶的 [DocumentDB 帳戶] 刀鋒視窗中，向下捲動至 [資料庫] 透鏡，然後選取要新增集合的資料庫。
    
4. 在 [**資料庫**] 刀鋒視窗中，按一下 [**新增集合**]。

	![螢幕擷取畫面，其中強調 [資料庫] 刀鋒視窗上的 [新增集合] 按鈕、[新增集合] 刀鋒視窗上的設定，以及 [確定] 按鈕 - 適用於 DocumentDB 的 Azure 入口網站 - NoSQL JSON 資料庫的雲端式資料庫建立者](./media/documentdb-create-collection/docdb-collection-creation-4-7.png)

5. 在 [新增集合] 刀鋒視窗中，輸入您新集合的識別碼。集合名稱的長度必須介於 1 到 255 個字元，且不能包含 `/ \ # ?` 或尾端空格。驗證名稱時，[識別碼] 方塊中會出現綠色的核取記號。

6. 選取新集合的定價層。您所建立的每個集合會是可計費的實體。如需可用效能層級的詳細資訊，請參閱 [DocumentDB 中的效能層級](documentdb-performance-levels.md)。

7. 選取下列其中一種**索引編製原則**。

	- **預設值**。此原則會為字串使用雜湊索引，以及為數字使用範圍索引。它最適合針對字串、ORDER BY 和範圍的相等查詢，以及最適合針對數字的相等查詢。此原則的索引儲存空間負擔較低，且包括地理空間索引。
	- **範圍**。當您使用 ORDER BY、範圍以及數字和字串的相等查詢時，這是最佳的原則。此原則的索引儲存空間負擔比**預設值**高，且包括地理空間索引。

	如需索引編製原則的詳細資訊，請參閱 [DocumentDB 索引編製原則](documentdb-indexing-policies.md)。

8. 按一下螢幕底部的 [**確定**]，以建立新的集合。


9. 新的集合現在便會出現在 [**資料庫**] 刀鋒視窗上的 [**集合**] 鏡頭中。
 
	![[資料庫] 刀鋒視窗中新集合的螢幕擷取畫面- 適用於 DocumentDB 的 Azure 入口網站 - NoSQL JSON 資料庫的雲端式資料庫建立者](./media/documentdb-create-collection/docdb-collection-creation-8.png)

## 什麼是 DocumentDB 集合？ 

集合是 JSON 文件和相關聯 JavaScript 應用程式邏輯的容器。集合是計費實體，其中的[成本](documentdb-performance-levels.md)是由與集合相關聯的效能層級所決定。

集合是預存程序和觸發程序的交易界限，以及查詢和 CRUD 作業的進入點。每個集合都有該集合特定的保留輸送量，且不會與相同帳戶中的其他集合共用。因此，您可以藉由新增更多集合，然後在它們之間散佈您的文件，在儲存體和輸送量方面相應放大您的應用程式。

集合與關聯式資料庫中的資料表不同。集合不會強制使用結構描述；事實上，DocumentDB 不會強制使用任何結構描述，它是無結構描述的資料庫。因此，您可以在相同集合中儲存具有各種不同結構描述之不同類型的文件。您可以選擇使用集合來儲存單一類型的物件，正如同您會對資料表所做的一樣。最佳模型僅取決於資料一起出現在查詢和交易中的方式。

## 建立 DocumentDB 集合的其他方法

集合不一定要使用入口網站來建立，您也可以使用 [DocumentDB SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx) 來建立集合。如需示範如何使用 DocumentDB .NET SDK 來建立集合的 C# 程式碼範例，請參閱 CollectionManagement 專案中的 [Program.cs](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/CollectionManagement/Program.cs) 檔案 (位於 [GitHub.com](https://github.com) 的 [azure-documentdb-net](https://github.com/Azure/azure-documentdb-net) 儲存機制中)。

## 疑難排解

如果 Azure 入口網站的 [新增集合] 遭到停用，這代表您的帳戶目前已遭停用，這種情況通常會在當月的所有權益信用額度都用完時發生。

## 後續步驟

既然您已有了集合，下一個步驟是將文件加入或滙入集合。談到將文件加入集合時，您會有幾個選擇：

- 您可以使用入口網站中的文件總管來[新增文件](documentdb-view-json-document-explorer.md)。
- 您可以使用 DocumentDB 資料移轉工具來[匯入文件和資料](documentdb-import-data.md)，此工具可讓您匯入 JSON 和 CSV 檔案，以及來自 SQL Server、MongoDB、Azure 資料表儲存體及其他 DocumentDB 集合的資料。 
- 或者，您可以利用其中一個 [DocumentDB SDK](documentdb-sdk-dotnet.md) 來新增文件。DocumentDB 有.NET、Java、Python、Node.js 和 JavaScript API SDK。DocumentManagement 專案中的 [Program.cs](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/DocumentManagement/Program.cs) 檔案 (位於 [GitHub.com](https://github.com) 上的 [azure-documentdb-net](https://github.com/Azure/azure-documentdb-net) 儲存機制中) 會利用 DocumentDB .NET SDK 來示範 CRUD 對文件的作業。

當集合中有文件之後，您就可以利用入口網站中的[查詢總管](documentdb-query-collections-query-explorer.md)、[REST API](https://msdn.microsoft.com/library/azure/dn781481.aspx)，或其中一個 [SDK](documentdb-sdk-dotnet.md)，來針對文件使用 [DocumentDB SQL](documentdb-sql-query.md) 來[執行查詢](documentdb-sql-query.md#executing-queries)。

<!---HONumber=AcomDC_0218_2016-->