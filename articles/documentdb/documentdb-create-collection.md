<properties 
	pageTitle="建立 DocumentDB 資料庫集合 | Microsoft Azure" 
	description="了解如何使用 Azure DocumentDB 的線上服務入口網站、JSON 的受管理 NoSQL 文件資料庫，來建立集合。立即取得免費試用版。" 
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
	ms.date="07/08/2015" 
	ms.author="mimig"/>

# 使用 Azure Preview 入口網站建立 DocumentDB 集合

若要使用 Microsoft Azure DocumentDB，您必須擁有 [DocumentDB 帳戶](documentdb-create-account.md)、[資料庫](documentdb-create-database.md)、集合和文件。本主題說明如何在 Azure Preview 入口網站中建立 DocumentDB 集合。

集合不一定要從預覽入口網站建立，您也可以使用 [DocumentDB SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx) 來建立集合。如需 C# 程式碼範例，示範如何使用 DocumentDB .NET SDK 建立集合，請參閱 CollectionManagement 專案中的 [Program.cs](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/CollectionManagement/Program.cs) 檔案，可於 [GitHub.com](https://github.com) 上的 [azure-documentdb-net](https://github.com/Azure/azure-documentdb-net) 儲存機制中取得。

1.  在 [Azure Preview 入口網站](https://portal.azure.com/)中，按一下 [**全部瀏覽**]。

2.  在 [**瀏覽**] 刀鋒視窗中，按一下 [**DocumentDB 帳戶**]。

3.  在 [**DocumentDB 帳戶**] 刀鋒視窗中，選取包含要在其中新增集合之資料庫的帳戶。如果您沒有任何列出的帳戶，您需要[建立 DocumentDB 帳戶](documentdb-create-account.md)。
    
    ![反白顯示 [瀏覽] 按鈕、[瀏覽] 刀鋒視窗上的 DocumentDB 帳戶，及 [DocumentDB 帳戶] 刀鋒視窗上的 DocumentDB 帳戶等螢幕擷取畫面](./media/documentdb-create-collection/docdb-database-creation-1-3.png)

4. 在 [**資料庫**] 刀鋒視窗中，按一下 [**新增集合**]。

5. 在 [**新增集合**] 刀鋒視窗中，輸入您的新集合的識別碼。驗證名稱時，[識別碼] 方塊中會出現綠色的核取記號。

6. 選取新集合的定價層。您所建立的每個集合會是可計費的實體。如需可用效能層級的詳細資訊，請參閱 [DocumentDB 中的效能層級](documentdb-performance-levels.md)。

7. 選取下列其中一種**索引編製原則**。

	- **預設值**。當您使用 ORDER BY、範圍和數字的相等查詢針對字串進行相等查詢時，這是最佳的原則。這個原則的索引儲存空間負擔比**範圍**低。
	- **雜湊**。當您針對數字和字串執行相等查詢時，這是最佳的原則。這個原則的索引儲存空間負擔最低。
	- **範圍**。當您使用 ORDER BY、範圍以及數字和字串的相等查詢時，這是最佳的原則。這個原則的索引儲存空間負擔比**預設**或**雜湊**高。

	如需索引編製原則的詳細資訊，請參閱 [DocumentDB 索引編製原則](documentdb-indexing-policies.md)。

8. 按一下螢幕底部的 [**確定**]，以建立新的集合。

	![反白顯示 [資料庫] 刀鋒視窗上的 [新增集合] 按鈕、[新增集合] 刀鋒視窗上的 [識別碼] 方塊，及 [確定] 按鈕等螢幕擷取畫面](./media/documentdb-create-collection/docdb-collection-creation-4-7.png)

9. 新的集合現在便會出現在 [**資料庫**] 刀鋒視窗上的 [**集合**] 鏡頭中。
 
	![[DocumentDB 帳戶] 刀鋒視窗中的新資料庫螢幕擷取畫面](./media/documentdb-create-collection/docdb-collection-creation-8.png)

## 後續步驟

既然您已有了集合，下一個步驟是將文件加入或滙入集合。談到將文件加入集合時，您會有幾個選擇：

- 您可以使用預覽入口網站中的 [Document Explorer] 來[新增文件](../documentdb-view-json-document-explorer.md)。
- 您可以使用 DocumentDB 資料移轉工具來[匯入文件和資料](documentdb-import-data.md)，此工具可讓您匯入 JSON 和 CSV 檔案，以及來自 SQL Server、MongoDB、Azure 資料表儲存體及其他 DocumentDB 集合的資料。 
- 或者，您可以透過使用其中一個 [DocumentDB SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx) 來加入文件。DocumentDB 有.NET、Java、Python、Node.js 和 JavaScript API SDK。DocumentManagement 專案中的 [Program.cs](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/DocumentManagement/Program.cs) 檔案 (可於 [GitHub.com](https://github.com) 上的 [azure-documentdb-net](https://github.com/Azure/azure-documentdb-net) 儲存機制中取得)，會示範使用 DocumentDB .NET SDK 在文件上的 CRUD 作業。

在集合中有了文件之後，您可以使用預覽入口網站中的[查詢總管](documentdb-query-collections-query-explorer.md)、[REST API](https://msdn.microsoft.com/library/azure/dn781481.aspx) 或其中一個 [SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx)，針對文件使用 [DocumentDB SQL](documentdb-sql-query.md) 來[執行查詢](documentdb-sql-query.md#executing-queries)。

<!---HONumber=July15_HO4-->