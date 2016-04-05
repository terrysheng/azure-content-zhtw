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
	ms.date="03/30/2016" 
	ms.author="mimig"/>

# 如何使用 Azure 入口網站建立 DocumentDB 集合

若要使用 Microsoft Azure DocumentDB，您必須擁有 [DocumentDB 帳戶](documentdb-create-account.md)、[資料庫](documentdb-create-database.md)、集合和文件。本主題說明如何在 Azure 入口網站中建立 DocumentDB 集合。

不確定集合是什麼嗎？ 請參閱＜[什麼是 DocumentDB 集合？](#what-is-a-documentdb-collection)＞一節。

1.  在 [Azure 入口網站](https://portal.azure.com/)的動態工具列中，按一下 [DocumentDB 帳戶]。如果看不到 [DocumentDB 帳戶]，請按一下 [瀏覽]，然後按一下 [DocumentDB 帳戶]。

    ![螢幕擷取畫面，其中在 [資料庫] 透鏡裡反白顯示動態工具列的 [DocumentDB 帳戶]、[DocumentDB 帳戶] 刀鋒視窗中的帳戶和 [DocumentDB 帳戶] 刀鋒視窗中的資料庫](./media/documentdb-create-collection/docdb-database-creation-1-2.png)

2.  在 [DocumentDB 帳戶] 刀鋒視窗中，選取要新增集合的帳戶。如果您沒有任何列出的帳戶，您需要[建立 DocumentDB 帳戶](documentdb-create-account.md)。

3. 在所選取帳戶的 [DocumentDB 帳戶] 刀鋒視窗中，向下捲動至 [資料庫] 透鏡，然後選取要新增集合的資料庫。

    ![螢幕擷取畫面，其中在 [資料庫] 透鏡裡反白顯示動態工具列的 [DocumentDB 帳戶]、[DocumentDB 帳戶] 刀鋒視窗中的帳戶和 [DocumentDB 帳戶] 刀鋒視窗中的資料庫](./media/documentdb-create-collection/docdb-database-creation-3.png)

4. 在 [資料庫] 刀鋒視窗中，按一下 [新增集合]。

	![螢幕擷取畫面，其中強調 [資料庫] 刀鋒視窗上的 [新增集合] 按鈕、[新增集合] 刀鋒視窗上的設定，以及 [確定] 按鈕 - 適用於 DocumentDB 的 Azure 入口網站 - NoSQL JSON 資料庫的雲端式資料庫建立者](./media/documentdb-create-collection/docdb-collection-creation-4.png)

5. 在 [新增集合] 刀鋒視窗的 [識別碼] 方塊中，輸入您新集合的識別碼。集合名稱的長度必須介於 1 到 255 個字元，且不能包含 `/ \ # ?` 或尾端空格。驗證名稱時，[識別碼] 方塊中會出現綠色的核取記號。

	![螢幕擷取畫面，其中強調 [資料庫] 刀鋒視窗上的 [新增集合] 按鈕、[新增集合] 刀鋒視窗上的設定，以及 [確定] 按鈕 - 適用於 DocumentDB 的 Azure 入口網站 - NoSQL JSON 資料庫的雲端式資料庫建立者](./media/documentdb-create-collection/docdb-collection-creation-5-8.png)

6. 如果您要建立具有單一資料分割的集合，請按一下箭頭以選取定價層，接著按一下 [檢視全部]，然後選取集合的效能層級。如需可用效能層級的詳細資訊，請參閱 [DocumentDB 中的效能層級](documentdb-performance-levels.md)。您所建立的每個集合會是可計費的實體。

	如果您要建立分割的集合，請跳至步驟 7，因為您不需要選取定價層。

7. 選取集合的 [資料分割模式]，亦即 [單一資料分割] 或 [已分割]。單一資料分割保留的儲存體容量為 10 GB，且輸送量層級可以從每秒 400 個到 10,000 個要求單位。已分割的集合可以進行調整以處理多個資料分割總共 250 GB 的儲存體，且輸送量層級可以從每秒 10,100 個到 250,000 個要求單位。

8. 選取已分割之集合的 [輸送量]。一個要求單位 (RU) 會對應至讀取 1 KB 文件的輸送量。如需要求單位的詳細資訊，請參閱[要求單位](documentdb-request-units.md)。

9. 如果您要建立已分割的集合，請選取集合的 [資料分割索引鍵]。選取正確的資料分割索引鍵對於建立高效能集合來說很重要。如需選取資料分割索引鍵的詳細資訊，請參閱[在 DocumentDB 中分割資料](documentdb-partition-data.md)。

10. 按一下螢幕底部的 [確定]，以建立新的集合。

11. 新的集合現在便會出現在 [資料庫] 刀鋒視窗上的 [集合] 鏡頭中。
 
	![[資料庫] 刀鋒視窗中新集合的螢幕擷取畫面- 適用於 DocumentDB 的 Azure 入口網站 - NoSQL JSON 資料庫的雲端式資料庫建立者](./media/documentdb-create-collection/docdb-collection-creation-9.png)

## 什麼是 DocumentDB 集合？ 

集合是 JSON 文件和相關聯 JavaScript 應用程式邏輯的容器。集合是計費實體，其[成本](documentdb-performance-levels.md)是由集合所佈建的輸送量所決定。集合可以跨越一或多個資料分割/伺服器，也可以進行調整以處理幾乎無限量的儲存體或輸送量。

集合會由 DocumentDB 自動分割成一或多個實體伺服器。當您建立集合時，您可以指定以每秒的要求單位和資料分割索引鍵屬性佈建的輸送量。DocumentDB 將會使用此屬性的值將文件散佈到資料分割並路由要求 (例如查詢)。資料分割索引鍵值也可做為預存程序和觸發程序的交易界限。每個集合都有該集合特定的保留輸送量，且不會與相同帳戶中的其他集合共用。因此，您可以在儲存體和輸送量方面相應放大您的應用程式。

集合與關聯式資料庫中的資料表不同。集合不會強制使用結構描述；事實上，DocumentDB 不會強制使用任何結構描述，它是無結構描述的資料庫。因此，您可以在相同集合中儲存具有各種不同結構描述之不同類型的文件。您可以選擇使用集合來儲存單一類型的物件，正如同您會對資料表所做的一樣。最佳模型僅取決於資料一起出現在查詢和交易中的方式。

## 建立 DocumentDB 集合的其他方法

集合不一定要使用入口網站來建立，您也可以使用 [DocumentDB SDK](documentdb-sdk-dotnet.md) 和 REST API 來建立集合。

- 如需 C# 程式碼範例，請參閱 [C# 集合樣本](documentdb-dotnet-samples.md#collection-examples)。 
- 如需 Node.js 程式碼範例，請參閱 [Node.js 集合樣本](documentdb-nodejs-samples.md#collection-examples)。
- 如需 Python 程式碼範例，請參閱 [Python 集合樣本](documentdb-python-samples.md#collection-examples)。
- 如需 REST API 範例，請參閱[建立集合](https://msdn.microsoft.com/library/azure/mt489078.aspx)。

## 疑難排解

如果 Azure 入口網站的 [新增集合] 遭到停用，這代表您的帳戶目前已遭停用，這種情況通常會在當月的所有權益信用額度都用完時發生。

## 後續步驟

既然您已有了集合，下一個步驟是將文件加入或滙入集合。談到將文件加入集合時，您會有幾個選擇：

- 您可以使用入口網站中的文件總管來[新增文件](documentdb-view-json-document-explorer.md)。
- 您可以使用 DocumentDB 資料移轉工具來[匯入文件和資料](documentdb-import-data.md)，此工具可讓您匯入 JSON 和 CSV 檔案，以及來自 SQL Server、MongoDB、Azure 資料表儲存體及其他 DocumentDB 集合的資料。 
- 或者，您可以利用其中一個 [DocumentDB SDK](documentdb-sdk-dotnet.md) 來新增文件。DocumentDB 有.NET、Java、Python、Node.js 和 JavaScript API SDK。如需示範如何使用 DocumentDB .NET SDK 處理文件的 C# 程式碼範例，請參閱 [C# 文件範例](documentdb-dotnet-samples.md#document-examples)。如需示範如何使用 DocumentDB Node.js SDK 處理文件的 Node.js 程式碼範例，請參閱 [Node.js 文件範例](documentdb-nodejs-samples.md#document-examples)。

當集合中有文件之後，您就可以利用入口網站中的[查詢總管](documentdb-query-collections-query-explorer.md)、[REST API](https://msdn.microsoft.com/library/azure/dn781481.aspx)，或其中一個 [SDK](documentdb-sdk-dotnet.md)，來針對文件使用 [DocumentDB SQL](documentdb-sql-query.md) 來[執行查詢](documentdb-sql-query.md#executing-queries)。

<!---HONumber=AcomDC_0330_2016-->