<properties 
    pageTitle="使用 DocumentDB 建置具有 Python 和 Flask 的 Web 應用程式 |Azure" 
    description="了解如何使用 DocumentDB 來儲存和存取主控於 Azure 上之 Python 和 Flask (MVC) Web 應用程式的資料。" 
    services="documentdb" 
    documentationCenter="" 
    authors="crwilcox" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-management" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="02/12/2015" 
    ms.author="crwilcox"/>

# 使用 DocumentDB 建置具有 Python 和 Flask (MVC) 的 Web 應用程式
------------------------------------------------------------------------

為了特別說明客戶要如何有效率地利用 Azure DocumentDB 來儲存和查詢 JSON 文件，本文件提供如何使用 Azure Document DB 建置投票 Web 應用程式的完整逐步解說。

本逐步解說說明如何使用 Azure 所提供的 DocumentDB 服務，來儲存和存取 Azure 上所託管 Python Web 應用程式的資料，並假設您先前已有使用 Python 和 Azure 網站的經驗。

本逐步解說涵蓋下列項目：

1. 建立和佈建 DocumentDB 帳戶

2. 建立 Python MVC 應用程式

3. 從 Web 應用程式連線到 Azure DocumentDB 和使用 Azure DocumentDB

4. 將 Web 應用程式部署至 Azure 網站

依照本逐步解說進行，您將建置簡單的投票
應用程式，可讓您針對民調進行投票。

![Alt text](./media/documentdb-python-application/image1.png)


## 必要條件

在依照本文中的指示進行之前，您應確定已安裝下列項目：

- Visual Studio 2013 (或免費版本的 [Visual Studio Express][])

- Python Tools for Visual Studio [下載位置][]

- Azure SDK for Visual Studio 2013 版或更高版本[下載位置][1]

- Python 2.7 [下載位置][2]

- Microsoft Visual C++ Compiler for Python 2.7 [下載位置][3]

## 步驟 1：建立 DocumentDB 資料庫帳戶

若要在 Azure 中佈建 DocumentDB 資料庫帳戶，請開啟 [Azure 管理入口網站][]，並按一下首頁上的 [Azure Gallery] 磚，或按一下畫面左下角的 [+]。

![Alt text](./media/documentdb-python-application/image2.png)


這會開啟 [Azure Marketplace]，您可以在其中從多個可用 Azure 服務進行選取。在 Marketplace 中，從類別清單中選取 [資料 + 分析]。

![Alt text](./media/documentdb-python-application/image3.png)

您可以在此搜尋 documentdb，並選取 DocumentDB 選項

![Alt text](./media/documentdb-python-application/image4.png)

然後選取畫面底部的 [建立]

![Alt text](./media/documentdb-python-application/image5.png)

這會開啟 [新增 DocumentDB] 分頁，您可以在其中指定新帳戶的名稱、區域、規模、資源群組和其他設定。

![Alt text](./media/documentdb-python-application/image6.png)

為帳戶提供完值之後，按一下 [建立]，佈建程序便會開始建立您的資料庫帳戶。
佈建程序完成之後，您應該會看到入口網站的 [通知] 區域中顯示一則通知，而您 [開始] 畫面上的磚 (如果您已選取要建立一個磚) 將會變更以顯示已完成的動作。

![Alt text](./media/documentdb-python-application/image7.png)

佈建完成之後，按一下開始畫面中的 [DocumentDB] 磚，這個新建立 DocumentDB 帳戶的主要刀鋒就會出現。

![Alt text](./media/documentdb-python-application/image8.png)

使用 [金鑰] 按鈕，存取端點 URL 和主要金鑰，將這些項目複製到剪貼簿並將它們保存在隨手可得的地方，因為我們將在接下來要建立的 Web 應用程式中使用這些值。


## 步驟 2：建立新的 Python Flask Web 應用程式

開啟 Visual Studio，並依序按一下 [檔案] -> [新增專案] -> [Python]，然後按一下名為 **tutorial** 的 [Flask Web 專案]。 

對於 Flask 的初學者而言，這個網路架構可協助我們在 Python 中更快速地建置 Web 應用程式。[按一下這裡可存取 Flask 教學課程][] (英文)。

![Alt text](./media/documentdb-python-application/image9.png)

系統會詢問您是否要安裝外部套件。按一下 [**安裝到虛擬環境**]。因為 PyDocumentDB 目前不支援 Python 3.x，請務必使用 Python 2.7 做為基底環境。這會設定專案所需的 Python 虛擬環境。

![Alt text](./media/documentdb-python-application/image10.png)


## 步驟 3：修改 Python Flask Web 應用程式


### 將 Flask 封裝新增至專案

專案設定好之後，您需要新增專案所需的特定 Flask 封裝，包括 pydocumentdb (DocumentDB 的 python 封裝)。開啟名為 **requirements.txt** 的檔案，並以下列內容取代：

    flask==0.9
    flask-mail==0.7.6
    sqlalchemy==0.7.9
    flask-sqlalchemy==0.16
    sqlalchemy-migrate==0.7.2
    flask-whooshalchemy==0.55a
    flask-wtf==0.8.4
    pytz==2013b
    flask-babel==0.8
    flup
    pydocumentdb>=0.9.4-preview

以滑鼠右鍵按一下 [**env**]，並按一下 [**從 requirements.txt 安裝**]

![Alt text](./media/documentdb-python-application/image11.png)

**注意：**在少數情況下，您可能會在 [輸出] 視窗中看到失敗。如果發生這種情況，請檢查錯誤是否與清除有關。有時候在清理失敗的情況下，安裝仍會成功 (在 [輸出] 視窗中向上捲動來確認這點 )。
<a name="verify-the-virtual-environment"></a>如果發生這種情況，您可以放心繼續進行。


### 驗證虛擬環境

讓我們來確定一切都已正確安裝。按一下 [**F5**] 鍵啟動網站。這將會啟動 Flask 開發伺服器和您的網頁瀏覽器。應該會出現下列網頁：

![Alt text](./media/documentdb-python-application/image12.png)

### 建立資料庫、集合和文件定義

在 [方案總管] 中，以滑鼠右鍵按一下名為 **tutorial** 的資料夾來新增 Python 檔案。將檔案命名為 **forms.py**。我們打算建立投票應用程式。

    from flask.ext.wtf import Form
    from wtforms import RadioField

    class VoteForm(Form):
        deploy_preference  = RadioField('Deployment Preference', choices=[
            ('Web Site', 'Web Site'),
            ('Cloud Service', 'Cloud Service'),
            ('Virtual Machine', 'Virtual Machine')], default='Web Site')

### 將必要匯入新增至 views.py

在 **views.py** 頂端新增下列匯入陳述式。這些將匯入 DocumentDB 的 PythonSDK 和 Flask 封裝。


    from forms import VoteForm
    import config
    import pydocumentdb.document_client as document_client
    

### 建立資料庫、集合和文件

將下列程式碼新增至 **views.py**。此程式碼可建立表單所使用的資料庫。請勿刪除 **views.py** 中的任何現有程式碼。只需將它附加至結尾。

    @app.route('/create')
    def create():
        """Renders the contact page."""
        client = document_client.DocumentClient(config.DOCUMENTDB_HOST, {'masterKey': config.DOCUMENTDB_KEY})
    
        # Attempt to delete the database.  This allows this to be used to recreate as well as create
        try:
            db = next((data for data in client.ReadDatabases() if data['id'] == config.DOCUMENTDB_DATABASE))
            client.DeleteDatabase(db['_self'])
        except:
            pass
    
        # Create database
        db = client.CreateDatabase({ 'id': config.DOCUMENTDB_DATABASE })
        # Create collection
        collection = client.CreateCollection(db['_self'],{ 'id': config.DOCUMENTDB_COLLECTION })
        # Create document
        document = client.CreateDocument(collection['_self'],
            { 'id': config.DOCUMENTDB_DOCUMENT,
            'Web Site': 0,
            'Cloud Service': 0,
            'Virtual Machine': 0,
            'name': config.DOCUMENTDB_DOCUMENT })
    
        return render_template(
            'create.html', 
            title='Create Page', 
            year=datetime.now().year,
            message='You just created a new database, collection, and document.  Your old votes have been deleted')


### 讀取資料庫、集合和文件並送出表單

將下列程式碼新增至 **views.py**。此程式碼可設定表單並讀取資料庫、集合和文件。請勿刪除 **views.py** 中的任何現有程式碼。只需將它附加至結尾。
    
    @app.route('/vote', methods=['GET', 'POST'])
    def vote(): 
        form = VoteForm()
        replaced_document ={}
        if form.validate_on_submit(): # is user submitted vote  
            client = document_client.DocumentClient(config.DOCUMENTDB_HOST, {'masterKey': config.DOCUMENTDB_KEY})
            
            # Read databases and take first since id should not be duplicated.
            db = next((data for data in client.ReadDatabases() if data['id'] == config.DOCUMENTDB_DATABASE))
            
            # Read collections and take first since id should not be duplicated.
            coll = next((coll for coll in client.ReadCollections(db['_self']) if coll['id'] == config.DOCUMENTDB_COLLECTION))
    
            # Read documents and take first since id should not be duplicated.
            doc = next((doc for doc in client.ReadDocuments(coll['_self']) if doc['id'] == config.DOCUMENTDB_DOCUMENT))
    
            # Take the data from the deploy_preference and increment our database
            doc[form.deploy_preference.data] = doc[form.deploy_preference.data] + 1
            replaced_document = client.ReplaceDocument(doc['_self'], doc)
    
            # Create a model to pass to results.html
            class VoteObject:
                choices = dict()
                total_votes = 0
    
            vote_object = VoteObject()
            vote_object.choices = {
                "Web Site" : doc['Web Site'],
                "Cloud Service" : doc['Cloud Service'],
                "Virtual Machine" : doc['Virtual Machine']
            }
            vote_object.total_votes = sum(vote_object.choices.values())
    
            return render_template(
                'results.html', 
                year=datetime.now().year, 
                vote_object = vote_object)
    
        else :
            return render_template(
                'vote.html', 
                title = 'Vote',
                year=datetime.now().year,
                form = form)


### 建立 html 檔案

在 [範本] 資料夾下，新增下列 html 檔案：create.html、results.html、vote.html

將下列程式碼新增至 **create.html**。此程式碼可顯示訊息指出我們已建立新的資料庫、集合和文件。

    {% extends "layout.html" %}
    {% block content %}
    <h2>{{ title }}.</h2>
    <h3>{{ message }}</h3>
    <p><a href="{{ url_for('vote') }}" class="btn btn-primary btn-large">Vote &raquo;</a></p>
    {% endblock %}

將下列程式碼新增至 **results.html**。此程式碼可顯示投票結果。

    {% extends "layout.html" %}
    {% block content %}
    <h2>Results of the vote</h2>
    <br />

    {% for choice in vote_object.choices %}
    <div class="row">
        <div class="col-sm-5">{{choice}}</div>
        <div class="col-sm-5">
            <div class="progress">
                <div class="progress-bar" role="progressbar" aria-valuenow="{{vote_object.choices[choice]}}" aria-valuemin="0" 
                     aria-valuemax="{{vote_object.total_votes}}" style="width: {{(vote_object.choices[choice]/vote_object.total_votes)*100}}%;">
                    {{vote_object.choices[choice]}}
                </div>
            </div>
        </div>
    </div>
    {% endfor %}

    <br />
    <a class="btn btn-primary" href="{{ url_for('vote') }}">Vote again?</a>
    {% endblock %}

將下列程式碼新增至 **vote.html**。此程式碼可顯示投票以及接受投票。註冊投票時，控制權會傳遞給 views.py，我們將在其中辨識投票以及相應地附加文件。

    {% extends "layout.html" %}
    {% block content %}
    <h2>What is your favorite way to host an application on Azure?</h2>
    <form action="" method="post" name="vote">
        {{form.hidden_tag()}}
        {{form.deploy_preference}}
        <button class="btn btn-primary" type="submit">Vote</button>
    </form>
    {% endblock %}

使用下列程式碼取代 **index.html** 的內容。此程式碼可做為您應用程式的登陸頁面。

    {% extends "layout.html" %}
    {% block content %}
    <h2>Python + DocumentDB Voting Application.</h2>
    <h3>This is a sample DocumentDB voting application using PyDocumentDB</h3>
    <p><a href="{{ url_for('create') }}" class="btn btn-primary btn-large">Create/Clear the Voting Database &raquo;</a></p>
    <p><a href="{{ url_for('vote') }}" class="btn btn-primary btn-large">Vote &raquo;</a></p>
    {% endblock %}


### 新增組態檔並變更 \_\_init\_\_.py

以滑鼠右鍵按一下專案名稱 tutorial，並新增檔案 - **config.py**。
Flask 中的表單需要使用此組態。您也可以用它來提供秘密金鑰。但本教學課程不需要用到此資訊。將下列程式碼新增至 config.py。   變更 **DOCUMENTDB\_HOST** 和 **DOCUMENTDB\_KEY** 的值。

    CSRF_ENABLED = True
    SECRET_KEY = 'you-will-never-guess'
    
    DOCUMENTDB_HOST = 'https://YOUR_DOCUMENTDB_NAME.documents.azure.com:443/'
    DOCUMENTDB_KEY = 'YOUR_SECRET_KEY_ENDING_IN_=='
    
    DOCUMENTDB_DATABASE = 'voting database'
    DOCUMENTDB_COLLECTION = 'voting collection'
    DOCUMENTDB_DOCUMENT = 'voting document'


同樣地，使用下列資訊取代 **\_\_init\_\_.py** 的內容。

    from flask import Flask
    app = Flask(__name__)
    app.config.from_object('config')
    import tutorial.views

在依照上述步驟進行之後，您的方案
總管應該看起來應如下所示

![Alt text](./media/documentdb-python-application/image15.png)


## 步驟 4：在本機執行您的應用程式

在 Visual Studio 中按 F5 鍵或 [執行] 按鈕，您應該就會在畫面上看到下列內容。

![Alt text](./media/documentdb-python-application/image16.png)

按一下 [建立/清除投票資料庫] 來產生資料庫。

![Alt text](./media/documentdb-python-application/image17.png)

然後，按一下 [投票]，並選取您的選項。

![Alt text](./media/documentdb-python-application/image18.png)

對於您投的每張票，都會讓適當的計數器遞增。

![Alt text](./media/documentdb-python-application/image19.png)


## 步驟 5：將應用程式部署至 Azure 網站

您已經擁有可在DocumentDB 正常運作的完整應用程式，我們現在要將此應用程式部署至 Azure 網站。在 [方案總管] 的 [專案] 上按一下滑鼠右鍵 (請確定您已沒有在本機上執行此應用程式)，然後選取 [發佈]。接著，選取 [Microsoft Azure 網站]。

![Alt text](./media/documentdb-python-application/image20.png)


設定您的 Azure 網站，方法是提供您的認證並按一下 [發佈]。

![Alt text](./media/documentdb-python-application/image21.png)

幾秒後，Visual Studio 便會發佈 Web 應用程式並啟動瀏覽器，您可以在瀏覽器中看到您方便好用的應用程式已在 Azure 中執行！


## 後續步驟

恭喜！您剛剛已經使用 Azure DocumentDB 建置您的第一個 Python 應用程式，並將它發佈至 Azure 網站。


  [按一下這裡可存取 Flask 教學課程] (英文): http://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-i-hello-world

  [Visual Studio Express]: http://www.visualstudio.com/products/visual-studio-express-vs.aspx
  [這裡]: http://aka.ms/ptvs
  [1]: http://go.microsoft.com/fwlink/?linkid=254281&clcid=0x409
  [2]: https://www.python.org/downloads/windows/
  [3]: http://aka.ms/vcpython27 
  [Microsoft Web Platform Installer]: http://www.microsoft.com/web/downloads/platform.aspx
  [Azure 管理入口網站]: http://portal.azure.com
<!--HONumber=47-->
