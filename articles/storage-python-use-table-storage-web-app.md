<properties linkid="develop-python-web-app-with-blob-storage" urlDisplayName="Web App with Blob Storage" pageTitle="Python web app with table storage | Microsoft Azure" metaKeywords="Azure table storage Python, Azure Python application, Azure Python tutorial, Azure Python example" description="A tutorial that teaches you how to create a Python web application using the Azure Client Libraries. Django is used as the web framework." metaCanonical="" services="storage" documentationCenter="Python" title="Python Web Application using Table Storage" authors="" solutions="" videoId="" scriptId="" manager="" editor="mollybos" />

使用資料表儲存體的 Python Web 應用程式
======================================

在此教學課程中，您將了解如何建立使用資料表儲存體搭配 Azure Client Libraries for Python 的應用程式。如果這是您的第一個 Python Azure 應用程式，您可能會想先參閱 [Django Hello World Web 應用程式](http://windowsazure.com/en-us/documentation/articles/virtual-machines-python-django-web-app-windows-server) (英文)。

在此指南中，您將建立一個可部署到 Azure 的 Web 架構工作清單應用程式。使用者可透過工作清單來擷取工作、新增工作及將工作標示為已完成。我們將使用 Django 作為 Web 架構。

工作項目會儲存於 Azure 儲存體中。Azure 儲存體提供可容錯且高度可用的非結構化資料儲存體。Azure 儲存體包括數種可儲存和存取資料的資料結構，並且您可以從 Azure SDK for Python 中所隨附的 API 或透過 REST API 來運用儲存體服務。如需詳細資訊，請參閱[在 Azure 中儲存和存取資料](http://msdn.microsoft.com/zh-tw/library/windowsazure/gg433040.aspx) (英文)。

您將了解：

-   如何使用 Azure 資料表儲存體服務

完成的應用程式螢幕擷取畫面將與下圖類似 (新增的工作項目會有所差異)：

![](./media/storage-python-use-table-storage-web-app/web-app-with-storage-Finaloutput-mac.png)

[WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]

設定開發環境
------------

**注意：** 如果您需要安裝 Python 或用戶端程式庫，請參閱 [Python 安裝指南](http://windowsazure.com/en-us/documentation/articles/python-how-to-install)。

*Windows 注意事項*：如果使用 Windows WebPI 安裝程式，則您已安裝 Django 和用戶端程式庫。

在 Azure 中建立儲存體帳戶
-------------------------

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

建立 Django 專案
----------------

以下是建立應用程式的步驟：

-   建立名為 'TableserviceSample' 的預設 Django 專案
-   在命令列中，切換至您要儲存程式碼的目錄，然後執行下列命令：

        django-admin.py startproject TableserviceSample

-   新增 Python 檔案 **views.py** 至專案
-   新增下列程式碼至 **views.py**，以匯入必要的 Django 支援：

         from django.http import HttpResponse
        from django.template.loader import render_to_string
        from django.template import Context

-   在 **TableserviceSample/TableserviceSample** 資料夾下，建立名為 **templates** 的新資料夾。
-   編輯應用程式設定，方便找到您的範本。開啟 **settings.py**，並將下列項目新增至 INSTALLED\_APPS：

        'TableserviceSample',

-   新增 Django 範本檔案 **mytasks.html** 至 **templates** 資料夾，並對其新增下列程式碼：

``` {}
    <html>
    <head><title></title></head>
    <body>
    <h2>My Tasks</h2> <br>
    <table border="1"> 
    <tr>
    <td>Name</td><td>Category</td><td>Date</td><td>Complete</td><td>Action</td></tr>
    {% for entity in entities %}
    <form action="update_task" method="GET">
    <tr><td>{{entity.name}} <input type="hidden" name='name' value="{{entity.name}}"></td>
    <td>{{entity.category}} <input type="hidden" name='category' value="{{entity.category}}"></td>
    <td>{{entity.date}} <input type="hidden" name='date' value="{{entity.date}}"></td>
    <td>{{entity.complete}} <input type="hidden" name='complete' value="{{entity.complete}}"></td>

    <td><input type="submit" value="Complete"></td>
    </tr>
    </form>
    {% endfor %}
    </table>
    <br>
    <hr>
    <table border="1">
    <form action="add_task" method="GET">
    <tr><td>Name:</td><td><input type="text" name="name"></input></td></tr>
    <tr><td>Category:</td><td><input type="text" name="category"></input></td></tr>
    <tr><td>Item Date:</td><td><input type="text" name="date"></input></td></tr>
    <tr><td><input type="submit" value="add task"></input></td></tr>
    </form>
    </table>
    </body>
    </html>    
```

匯入 windowsazure 儲存體模組
----------------------------

緊接 Django 匯入之後，在 **views.py** 的頂端上新增下列程式碼

    from azure.storage import TableService

取得儲存體帳戶名稱和帳戶金鑰
----------------------------

緊接  windowsazure 匯入之後，將下列程式碼新增至 **views.py**，並以您真正的帳戶名稱和金鑰取代 'youraccount' 和 'yourkey'。您可以從 Azure 管理入口網站取得帳戶名稱和金鑰。

    account_name = 'youraccount'
    account_key = 'yourkey'

建立 TableService
-----------------

在 **account\_name** 後面新增下列程式碼：

    table_service = TableService(account_name=account_name, account_key=account_key)
    table_service.create_table('mytasks')

列出工作
--------

將函數 list\_tasks 新增至 **views.py**：

    def list_tasks(request): 
        entities = table_service.query_entities('mytasks', '', 'name,category,date,complete')    
        html = render_to_string('mytasks.html', Context({'entities':entities}))
        return HttpResponse(html)

新增工作
--------

將函數 add\_task 新增至 **views.py**：

    def add_task(request):
        name = request.GET['name']
        category = request.GET['category']
        date = request.GET['date']
        table_service.insert_entity('mytasks', {'PartitionKey':name+category, 'RowKey':date, 'name':name, 'category':category, 'date':date, 'complete':'No'}) 
        entities = table_service.query_entities('mytasks', '', 'name,category,date,complete')
        html = render_to_string('mytasks.html', Context({'entities':entities}))
        return HttpResponse(html)

更新工作狀態
------------

將函數 update\_task 新增至 **views.py**：

    def update_task(request):
        name = request.GET['name']
        category = request.GET['category']
        date = request.GET['date']
        partition_key = name + category
        row_key = date
        table_service.update_entity('mytasks', partition_key, row_key, {'PartitionKey':partition_key, 'RowKey':row_key, 'name':name, 'category':category, 'date':date, 'complete':'Yes'})
        entities = table_service.query_entities('mytasks', '', 'name,category,date,complete')    
        html = render_to_string('mytasks.html', Context({'entities':entities}))
        return HttpResponse(html)

對應 URL
--------

現在，您必須在 Django 應用程式中對應 URL。開啟 **urls.py**，並將下列對應新增至 urlpatterns：

    url(r'^$', 'TableserviceSample.views.list_tasks'),
    url(r'^list_tasks$', 'TableserviceSample.views.list_tasks'),
    url(r'^add_task$', 'TableserviceSample.views.add_task'),
    url(r'^update_task$', 'TableserviceSample.views.update_task'),

執行應用程式
------------

-   切換至 **TableserviceSample** 目錄 (如果您尚未這麼做)，並執行命令：

    python manage.py runserver

-   在瀏覽器中移至：`http://127.0.0.1:8000/`.以真正的連接埠編號取代 8000。

您現在可以按一下 **[新增工作]** 以建立工作，然後按一下 **[完成]** 按鈕，以更新工作並將其狀態設定為 [是]。

在計算模擬器中執行應用程式、發行並停止/刪除您的應用程式
-------------------------------------------------------

現在您已在內建 Django 伺服器上順利執行應用程式，您可以進一步試用該應用程式，方法是將它部署到 Azure 模擬器 (僅限 Windows) 然後發行至 Azure。如需如何執行此動作的一般指示，請參閱文章 [Django Hello World Web 應用程式](http://windowsazure.com/en-us/documentation/articles/virtual-machines-python-django-web-app-windows-server) (英文)，文中將詳細討論這些步驟。

後續步驟
--------

了解 Azure 資料表儲存體服務的基礎概念之後，請參考下列連結以了解如何執行更複雜的儲存工作。

-   請參閱 MSDN 參考：[在 Azure 中儲存和存取資料](http://msdn.microsoft.com/zh-tw/library/windowsazure/gg433040.aspx)
-   請造訪 Azure 儲存體團隊部落格：&lt;http://blogs.msdn.com/b/windowsazurestorage/\&gt;

