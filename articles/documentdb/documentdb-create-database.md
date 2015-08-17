<properties 
	pageTitle="建立 NoSQL DocumentDB 資料庫 | Microsoft Azure" 
	description="了解如何使用 Azure DocumentDB 的線上服務入口網站、JSON 的 NoSQL 文件資料庫，來建立受管理的資料庫。立即取得免費試用版。" 
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
	ms.topic="article" 
	ms.date="06/26/2015" 
	ms.author="mimig"/>

# 使用 Azure Preview 入口網站建立 DocumentDB 資料庫

若要使用 Microsoft Azure DocumentDB，您必須擁有 [DocumentDB 帳戶](documentdb-create-account.md)、資料庫、集合和文件。本主題說明如何在 Microsoft Azure Preview 入口網站中建立 DocumentDB 資料庫。

資料庫不一定要使用預覽入口網站建立，您也可以使用 [DocumentDB SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx) 來建立。如需 C# 程式碼範例，示範如何使用 DocumentDB .NET SDK 建立資料庫，請參閱 DatabaseManagement 專案中的 [Program.cs](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/DatabaseManagement/Program.cs) 檔案，可於 [GitHub.com](https://github.com) 上的 [azure-documentdb-net](https://github.com/Azure/azure-documentdb-net) 儲存機制中取得。

1.  在 [Azure Preview 入口網站](https://portal.azure.com/)中，按一下 [**全部瀏覽**]。

2.  在 [**瀏覽**] 刀鋒視窗中，按一下 [**DocumentDB 帳戶**]。

3.  在 [**DocumentDB 帳戶**] 刀鋒視窗中，選取要在其中新增 DocumentDB 資料庫的帳戶。如果您沒有任何列出的帳戶，您需要[建立 DocumentDB 帳戶](documentdb-create-account.md)。
    
    ![反白顯示 [瀏覽] 按鈕、[瀏覽] 刀鋒視窗上的 DocumentDB 帳戶，及 [DocumentDB 帳戶] 刀鋒視窗上的 DocumentDB 帳戶等螢幕擷取畫面](./media/documentdb-create-database/docdb-database-creation-1-3.png)

4. 在 [**DocumentDB 帳戶**] 刀鋒視窗中，按一下 [**新增資料庫**]。

5. 在 [**新增資料庫**] 刀鋒視窗中，輸入您的新資料庫識別碼。驗證名稱時，[識別碼] 方塊中會出現綠色的核取記號。

6. 按一下螢幕底部的 [**確定**]，以建立新的資料庫。

	![反白顯示 [DocumentDB 帳戶] 刀鋒視窗上的 [新增資料庫] 按鈕、[新增資料庫] 刀鋒視窗上的 [識別碼] 方塊，及 [確定] 按鈕等螢幕擷取畫面](./media/documentdb-create-database/docdb-database-creation-4-6.png)

8. 新的資料庫現在便會出現在 [**DocumentDB 帳戶**] 刀鋒視窗上的 [**資料庫**] 鏡頭中。
 
	![[DocumentDB 帳戶] 刀鋒視窗中的新資料庫螢幕擷取畫面](./media/documentdb-create-database/docdb-database-creation-7.png)

## 後續步驟

既然您已有了 DocumentDB 資料庫，下一步是[建立集合](documentdb-create-collection.md)。

建立集合之後，您可以使用預覽入口網站中的 Document Explorer 來[新增文件](../documentdb-view-json-document-explorer.md)、使用 DocumentDB 資料移轉工具來[匯入文件](documentdb-import-data.md)至集合，或使用其中一個 [DocumentDB SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx) 來執行 CRUD 作業。DocumentDB 有.NET、Java、Python、Node.js 和 JavaScript API SDK。如需 .NET 程式碼範例，示範如何建立、移除、更新及刪除文件，請參閱 GitHub.com上 azure-documentdb-net 儲存機制中 DocumentManagement 專案中的 [Program.cs](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/DocumentManagement/Program.cs)。

在集合中有了文件之後，您可以使用預覽入口網站中的[查詢總管](documentdb-query-collections-query-explorer.md)、[REST API](https://msdn.microsoft.com/library/azure/dn781481.aspx) 或其中一個 [SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx)，針對文件使用 [DocumentDB SQL](documentdb-sql-query.md) 來[執行查詢](documentdb-sql-query.md#executing-queries)。

<!---HONumber=August15_HO6-->