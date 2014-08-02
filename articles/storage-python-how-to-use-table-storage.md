<properties linkid="develop-python-table-service" urlDisplayName="Table Service" pageTitle="How to use table storage (Python) | Microsoft Azure" metaKeywords="Azure table Python, creating table Azure, deleting table Azure, inserting table Azure, querying table Azure" description="Learn how to use the Table service from Python to create and delete a table, and insert, delete, and query the table." metaCanonical="" services="storage" documentationCenter="Python" title="How to Use the Table Storage Service from Python" authors="" solutions="" manager="" editor="" />

如何從 Python 使用資料表儲存體服務
==================================

本指南說明如何使用 Windows Azure 資料表儲存體服務執行一般案例。相關範例是以 Python API 撰寫。所涵蓋的案例包括**「建立和刪除資料表」**、**「在資料表中插入及查詢實體」**。如需資料表的詳細資訊，請參閱[後續步驟](#next-steps)一節。

目錄
----

[什麼是資料表服務？](#what-is)
 [概念](#concepts)
 [建立 Azure 儲存體帳戶](#create-account)
 [作法：建立資料表](#create-table)
 [作法：將實體加入至資料表](#add-entity)
 [作法：更新實體](#update-entity)
 [作法：變更一組實體](#change-entities)
 [作法：查詢實體](#query-for-entity)
 [作法：查詢實體集合](#query-set-entities)
 [作法：查詢實體屬性的子集](#query-entity-properties)
 [作法：刪除實體](#delete-entity)
 [作法：刪除資料表](#delete-table)
 [後續步驟](#next-steps)

[WACOM.INCLUDE [howto-table-storage](../includes/howto-table-storage.md)]

建立 Azure 儲存體帳戶
---------------------

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

**注意：** 如果您需要安裝 Python 或用戶端程式庫，請參閱 [Python 安裝指南](../python-how-to-install/)。

如何建立資料表
--------------

**TableService** 物件可讓您操作資料表服務。下列程式碼將建立 **TableService** 物件。將下列內容新增至您想要在其中以程式設計方式存取 Azure 儲存體之任何 Python 檔案內的頂端附近：

    from azure.storage import *

下列程式碼會使用儲存體帳戶名稱和帳戶金鑰來建立 **TableService** 物件：將 'myaccount' 和 'mykey' 取代為真實的帳戶和金鑰。

    table_service = TableService(account_name='myaccount', account_key='mykey')

    table_service.create_table('tasktable')

如何將實體新增至資料表
----------------------

若要新增實體，請先建立定義實體屬性名稱和值的字典。請注意，對每個實體都必須指定 **PartitionKey** 及 **RowKey**。這些是實體的唯一識別碼，且其值的查詢速度比其他屬性快上許多。系統使用 **PartitionKey** 自動將資料表的實體分散在許多儲存體節點上。具有相同 **PartitionKey** 的實體會儲存在相同節點上。**RowKey** 是實體在其所屬資料分割內的唯一識別碼。

若要將實體新增至資料表，請將字典物件傳給 **insert\_entity** 方法。

    task = {'PartitionKey':'tasksSeattle', 'RowKey':'1', 'description' :'Take out the trash', 'priority' : 200}
    table_service.insert_entity('tasktable', task)

您也可以將 **Entity** 類別的執行個體傳給 **insert\_entity** 方法。

    task = Entity()
    task.PartitionKey = 'tasksSeattle'
    task.RowKey = '2'
    task.description = 'Wash the car'
    task.priority = 100
    table_service.insert_entity('tasktable', task)

如何更新實體
------------

此程式碼說明如何以更新版本取代舊版本的現有實體。

    task = {'description' :'Take out the garbage', 'priority' : 250}
    table_service.update_entity('tasktable', 'tasksSeattle', '1', task)

如果正在更新的實體不存在，則更新操作便會失敗。如果您想要儲存實體 (不論它是否已存在)，請使用 **insert\_or\_replace\_entity**。在下列範例中，第一個呼叫將取代現有實體。第二個呼叫將插入新實體，因為資料表中沒有具有指定 **PartitionKey** 和 **RowKey** 的實體存在。

    task = {'description' :'Take out the garbage again', 'priority' : 250}
    table_service.insert_or_replace_entity('tasktable', 'tasksSeattle', '1', task)

    task = {'description' :'Buy detergent', 'priority' : 300}
    table_service.insert_or_replace_entity('tasktable', 'tasksSeattle', '3', task)

如何變更一組實體
----------------

有時候批次提交多個操作是有意義的，可以確保伺服器會進行不可部分完成的處理。要達到此點，請在 **TableService** 上使用 **begin\_batch** 方法，然後如常呼叫操作系列。當您真的想提交批次時，請呼叫 **commit\_batch**。請注意，所有實體都必須位於相同的資料分割中，才能以批次方式進行變更。以下範例在一個批次中同時新增兩個實體。

    task10 = {'PartitionKey':'tasksSeattle', 'RowKey':'10', 'description' :'Go grocery shopping', 'priority' : 400}
    task11 = {'PartitionKey':'tasksSeattle', 'RowKey':'11', 'description' :'Clean the bathroom', 'priority' : 100}
    table_service.begin_batch()
    table_service.insert_entity('tasktable', task10)
    table_service.insert_entity('tasktable', task11)
    table_service.commit_batch()

如何查詢實體
------------

若要查詢資料表中的實體，請使用 **get\_entity** 方法，傳遞 **PartitionKey** 和 **RowKey**。

    task = table_service.get_entity('tasktable', 'tasksSeattle', '1')
    print(task.description)
    print(task.priority)

如何查詢一組實體
----------------

此範例會根據 **PartitionKey** 找到在西雅圖的所有工作。

    tasks = table_service.query_entities('tasktable', "PartitionKey eq 'tasksSeattle'")
    for task in tasks:
        print(task.description)
        print(task.priority)

如何查詢實體屬性的子集
----------------------

一項資料表查詢可以只擷取實體的少數屬性。這項稱為「投射」的技術可減少頻寬並提高查詢效能 (尤其是對大型實體而言)。使用       **select**  參數並傳遞您要帶到用戶端的屬性名稱。

下列程式碼中的查詢只會傳回資料表中各實體的**說明**。

*請注意，下列片段只適用於雲端儲存體服務，儲存體模擬器並不支援下列程式碼。*

    tasks = table_service.query_entities('tasktable', "PartitionKey eq 'tasksSeattle'", 'description')
    for task in tasks:
        print(task.description)

如何刪除實體
------------

您可以使用實體的資料分割和資料列索引鍵來刪除實體。

    table_service.delete_entity('tasktable', 'tasksSeattle', '1')

如何刪除資料表
--------------

下列程式碼會從儲存體帳戶刪除資料表。

    table_service.delete_table('tasktable')

後續步驟
--------

了解資料表儲存體的基礎概念之後，請參考下列連結以了解如何執行更複雜的儲存工作。

-   請參閱 MSDN 參考：[在 Azure 中儲存和存取資料](http://msdn.microsoft.com/en-us/library/windowsazure/gg433040.aspx)
-   [請造訪 Azure 儲存體團隊部落格](http://blogs.msdn.com/b/windowsazurestorage/)

