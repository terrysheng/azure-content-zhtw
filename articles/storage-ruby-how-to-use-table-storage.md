<properties 
	pageTitle="如何使用資料表儲存體 (Ruby) | Microsoft Azure" 
	description="如何在 Azure 中使用資料表儲存體服務。程式碼範例以 Ruby API 撰寫。" 
	services="storage" 
	documentationCenter="ruby" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="ruby" 
	ms.topic="article" 
	ms.date="11/24/2014" 
	ms.author="tomfitz"/>





# 如何使用 Ruby 的資料表服務

本指南將示範如何使用 Azure 資料表服務執行一般案例。這些範例使用 Ruby API 撰寫。所涵蓋的案例包括**「建立和刪除資料表」、「在資料表中插入及查詢實體」**。如需資料表的詳細資訊，請參閱[後續步驟](#next-steps) 一節。

## 目錄

* [什麼是資料表服務？](#what-is)
* [概念](#concepts)
* [建立 Azure 儲存體帳戶](#create-a-windows-azure-storage-account)
* [建立 Ruby 應用程式](#create-a-ruby-application)
* [設定您的應用程式以存取儲存體](#configure-your-application-to-access-storage)
* [設定 Azure 儲存體連線](#setup-a-windows-azure-storage-connection)
* [做法：建立資料表](#how-to-create-a-table)
* [做法：將實體加入至資料表](#how-to-add-an-entity-to-a-table)
* [做法：更新實體](#how-to-update-an-entity)
* [做法：使用實體群組](#how-to-work-with-groups-of-entities)
* [做法：查詢實體](#how-to-query-for-an-entity)
* [做法：查詢實體集合](#how-to-query-a-set-of-entities)
* [做法：查詢實體屬性的子集](#how-to-query-a-subset-of-entity-properties)
* [做法：刪除實體](#how-to-delete-an-entity)
* [做法：刪除資料表](#how-to-delete-a-table)
* [後續步驟](#next-steps)

[AZURE.INCLUDE [howto-table-storage](../includes/howto-table-storage.md)]

## <a id="create-a-windows-azure-storage-account"></a>建立 Azure 儲存體帳戶

[AZURE.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## <a id="create-a-ruby-application"></a>建立 Ruby 應用程式

建立 Ruby 應用程式。如需指示， 
請參閱[在 Azure 上建立 Ruby 應用程式](/zh-tw/develop/ruby/tutorials/web-app-with-linux-vm/)。

## <a id="configure-your-application-to-access-storage"></a>設定您的應用程式以存取儲存體

若要使用 Azure 儲存體，您需要下載及使用 Ruby azure 套件， 
其含有一組能與儲存體 REST 服務通訊的便利程式庫。

### 使用 RubyGems 來取得套件

1. 使用命令列介面，例如 **PowerShell** (Windows)、**Terminal** (Mac) 或 **Bash** (Unix)。

2. 在命令視窗中鍵入 **gem install azure** 以安裝 Gem 和相依性。

### 匯入封裝

使用您偏好的文字編輯器，將以下內容新增至您打算使用儲存體的 Ruby 檔案頂端：

	require "azure"

## <a id="setup-a-windows-azure-storage-connection"></a>設定 Azure 儲存體連線

Azure 模組會讀取環境變數 **AZURE\_STORAGE\_ACCOUNT** and **AZURE\_STORAGE\_ACCESS\_KEY**，以取得連接 Azure 儲存體帳戶所需的資訊。如果尚未設定這些環境變數，您必須使用下列程式碼，在使用 **Azure::TableService** 之前指定帳戶資訊：

	Azure.config.storage_account_name = "<your azure storage account>"
	Azure.config.storage_access_key = "<your azure storage access key>"

若要取得這些值，請執行下列動作：

1. 登入 [Azure 管理入口網站](https://manage.windowsazure.com/)。

2. 瀏覽到您要使用的儲存體帳戶。

3. 按一下導覽窗格底部的 **[管理金鑰]**。

4. 在快顯對話方塊中，您將會看到儲存體帳戶名稱、主要存取金鑰和次要存取金鑰。如需存取金鑰，您可使用主要存取金鑰或次要存取金鑰。

## <a id="how-to-create-a-table"></a>如何建立資料表

**Azure::TableService** 物件可讓您操作資料表及實體。若要建立容器，請使用 **create\_table()** 方法。下列範例將建立資料表或列印錯誤訊息 (若有的話)。

	azure_table_service = Azure::TableService.new
	begin
	  azure_table_service.create_table("testtable")
	rescue
	  puts $!
	end

## <a id="how-to-add-an-entity-to-a-table"></a>如何將實體新增至資料表

若要新增實體，請先建立定義實體屬性的雜湊物件。請注意，您必須對每個實體指定 **PartitionKey** 和 **RowKey**。這些是實體的唯一識別碼，且其值的查詢速度比其他屬性快上許多。Azure 儲存體服務使用 **PartitionKey** 自動將資料表的實體分散在許多儲存體節點上。具有相同 **PartitionKey** 的實體會儲存在相同節點上。**RowKey** 是實體在其所屬資料分割內的唯一識別碼。 

	entity = { "content" => "test entity", 
	  :PartitionKey => "test-partition-key", :RowKey => "1" }
	azure_table_service.insert_entity("testtable", entity)

## <a id="how-to-update-an-entity"></a>做法：更新實體

有多種方法可以用來更新現有的實體：

* **update\_entity():** 藉由取代來更新現有實體。
* **merge\_entity():** 藉由將新的屬性值合併到現有實體來更新現有實體。
* **insert\_or\_merge\_entity():** 藉由取代來更新現有實體。如果實體不存在，將會插入新的實體：
* **insert\_or\_replace\_entity():** 藉由將新的屬性值合併到現有實體來更新現有實體。如果實體不存在，將會插入新的實體。

下列範例示範使用 **update\_entity()**: 來更新實體：

	entity = { "content" => "test entity with updated content", 
	  :PartitionKey => "test-partition-key", :RowKey => "1" }
	azure_table_service.update_entity("testtable", entity)

使用 **update\_entity()** 和 **merge\_entity()** 時，如果要更新的實體不存在，更新作業將會失敗。因此，如果無論實體是否存在，您都希望儲存實體，您應改為使用 **insert\_or\_replace\_entity()** 或 **insert\_or\_merge\_entity()**。

## <a id="how-to-work-with-groups-of-entities"></a>做法：使用實體群組

有時候批次提交多個操作是有意義的，可以確保伺服器會進行不可部分完成的處理。若要達到此目的，您首先必須建立 **Batch** 物件，然後在 **TableService** 上使用 **execute\_batch()** 方法。下列範例示範在一個批次中，提交具備 RowKey 2 和 3 的兩個實體。請注意，它僅適用於具備相同 PartitionKey 的實體。

	azure_table_service = Azure::TableService.new
	batch = Azure::Storage::Table::Batch.new("testtable", 
	  "test-partition-key") do
	  insert "2", { "content" => "new content 2" }
	  insert "3", { "content" => "new content 3" }
	end
	results = azure_table_service.execute_batch(batch)

## <a id="how-to-query-for-an-entity"></a>做法：查詢實體

若要查詢資料表中的實體，請使用 **get\_entity()** 方法，並傳遞資料表名稱、**PartitionKey** 及 **RowKey**。

	result = azure_table_service.get_entity("testtable", "test-partition-key", 
	  "1")

## <a id="how-to-query-a-set-of-entities"></a>做法：查詢實體集合

若要查詢資料表中的實體集合，請建立查詢雜湊物件並使用 **query\_entities()** 方法。下列範例示範取得具備相同 **PartitionKey** 的所有實體：

	query = { :filter => "PartitionKey eq 'test-partition-key'" }
	result, token = azure_table_service.query_entities("testtable", query)

**請注意**，如果單一查詢的結果集因為太大而無法傳回，則會傳回接續權杖，方便您用來擷取後續頁面。

## <a id="how-to-query-a-subset-of-entity-properties"></a>做法：查詢實體屬性的子集

一項資料表查詢可以只擷取實體的少數屬性。這項稱為「投射」的技術可減少頻寬並提高查詢效能 (尤其是對大型實體而言)。使用 select 子句並傳遞您要帶到用戶端的屬性名稱。

	query = { :filter => "PartitionKey eq 'test-partition-key'", 
	  :select => ["content"] }
	result, token = azure_table_service.query_entities("testtable", query)

## <a id="how-to-delete-an-entity"></a>做法：刪除實體

若要刪除實體，請使用 **delete\_entity()** 方法。您必須傳入資料表名稱，其中包含實體、實體的 PartitionKey 和 RowKey。

		azure_table_service.delete_entity("testtable", "test-partition-key", "1")

## <a id="how-to-delete-a-table"></a>做法：刪除資料表

若要刪除資料表，請使用 **delete\_table()** 方法並傳入要刪除之資料表的名稱。

		azure_table_service.delete_table("testtable")

## <a id="next-steps"></a>後續步驟

了解資料表儲存體的基礎概念之後，請參考下列連結以了解如何執行更複雜的儲存工作。

* 請參閱 MSDN 參考：[在 Azure 中儲存及存取資料](http://msdn.microsoft.com/zh-tw/library/windowsazure/gg433040.aspx)
* 請造訪 [Azure 儲存體團隊部落格](http://blogs.msdn.com/b/windowsazurestorage/)
* 請造訪 GitHub 上的 [Azure SDK for Ruby](http://github.com/WindowsAzure/azure-sdk-for-ruby) 儲存機制 (英文)。
<!--HONumber=42-->
