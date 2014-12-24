<properties title="Build a web application with Python and Flask (MVC) using DocumentDB" pageTitle="使用 DocumentDB 建置具有 Python 和 Flask 的 Web 應用程式 |Azure" description="Learn how to use DocumentDB to store and access data from an Python and Flask (MVC) web application hosted on Azure."  metaKeywords="NoSQL, DocumentDB,  database, document-orientated database, JSON, account" services="documentdb"  solutions="data-management" documentationCenter=""  authors="hawong" manager="jhubbard" editor="cgronlun" videoId="" scriptId="" />


<tags ms.service="documentdb" ms.workload="data-management" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/20/2014" ms.author="hawong" />

<a name="_Toc395888515"></a><a name="_Toc395809324">使用 DocumentDB 建置具有 Python 和 Flask (MVC) 的 Web 應用程式</a>
===========================================================================================================================================

<a name="_Toc395809324">

<a name="_Toc395888516"></a><a name="_Toc395809325"></a><a name="_Toc389865467"></a><a name="_Toc389828008">概觀</a>
========================================================================================================================

<a name="_Toc395888517"></a><a name="_Toc395809326">案例</a>
----------------------------------------------------------------

若要反白顯示客戶可以如何有效率地利用 Azure DocumentDB 來
儲存與查詢的 JSON 文件，本文提供
有關使用 Azure 文件建置投票 Web 應用程式的端對端逐步解說
DB。

本逐步解說說明如何使用 Azure 所提供的 DocumentDB 服務，
從 Azure 上所託管的 Python Web 應用程式來儲存和存取資料，
並假設您先前已有使用 Python 和 Azure 網站的經驗
。

您將了解：

1\. 建立和佈建 DocumentDB 帳戶

2\. 建立 Python MVC 應用程式

3\. 從 Web 應用程式連線到 Azure DocumentDB 和使用 Azure DocumentDB

4\. 將 Web 應用程式部署至 Azure 網站

依照本逐步解說進行，您將建置簡單的投票
應用程式，可讓您針對民調進行投票。

![Alt text](./media/documentdb-python-application/image1.png)

<a name="_Toc395888520"></a><a name="_Toc395809329">必要條件</a>


在依照本文指示進行之前，您應該確定
已安裝下列項目：

Visual Studio 2013 (或 [Visual Studio Express][] 免費
版本)

Python Tools for Visual Studio [下載位置][]

Azure SDK for Visual Studio 2013 2.4 版或更高版本，
[下載位置][1]

Azure 跨平台命令列工具，可透過下列產品取得：[Microsoft
Web Platform Installer][]

<a name="_Toc395888519"></a><a name="_Toc395809328">建立 DocumentDB 資料庫帳戶</a>
============================================================================================

若要在 Azure 中佈建 DocumentDB 資料庫帳戶，請開啟 Azure
管理入口網站，並按一下首頁上的 [Azure 組件庫] 磚，
或按一下畫面左下角的加號 "+" 。

![Alt text](./media/documentdb-python-application/image2.png)


這會開啟 [Azure 組件庫]，您可以從此處的許多
可用 Azure 服務中進行選取。在 [組件庫] 的類別清單中，選取 [資料、儲存體和
備份]。

![Alt text](./media/documentdb-python-application/image3.png)

從這裡，選取 Azure DocumentDB 的選項

![Alt text](./media/documentdb-python-application/image4.png)

然後選取畫面底部的 [建立]

![Alt text](./media/documentdb-python-application/image5.png)

這將會開啟 [新增 DocumentDB] 分頁，您可以在此指定新帳戶的
名稱、區域、規模、資源群組和其他設定
。

![Alt text](./media/documentdb-python-application/image6.png)

完成提供帳戶的值之後，請按一下 [建立]，
佈建程序便會開始建立您的資料庫帳戶。
佈建程序完成之後，您應該會看到一則通知
出現在入口網站的通知區域上，且 
[開始] 畫面上的磚 (如果您選取要建立一個) 會變更以顯示
已完成的動作。

![Alt text](./media/documentdb-python-application/image7.png)

佈建完成後，按一下開始畫面的 [DocumentDB] 磚
將會開啟這個新建立 DocumentDB 帳戶的主要分頁
。

![Alt text](./media/documentdb-python-application/image8.png)
![Alt text](./media/documentdb-python-application/image9.png)



使用 [金鑰] 按鈕，來存取您的端點 URL 和主要金鑰，
將它們複製到剪貼簿，並放在容易取得的地方，因為我們會在後續建立的 Web 應用程式中用到這些
值。

</a>

<a name="_Toc395888520"></a><a name="_Toc395809329">建立新的 Python Flask Web 應用程式</a>
=================================================================================================

開啟 Visual Studio，依序選取 [檔案] -\> [新增專案] -\> [Python] -\> [Flask Web
專案，名稱為 **tutorial**]。系統會詢問您是否要
安裝外部封裝。按一下 [安裝到虛擬環境]，
然後按一下 [建立]。這會為您的專案安裝必要的 Python 虛擬
環境。

若您是剛接觸 Flask 的新手，Flask 是一種 Web 架構，可協助我們在 Python 中更快速地建置 Web 
應用程式。[按一下這裡可存取 Flask 教學課程][] (英文)。

![Alt text](./media/documentdb-python-application/image10.png)

<a name="_Toc395888520"></a><a name="_Toc395809329">將 Flask 封裝新增至專案</a>
==================================

在專案設定後，您必須加入專案所需的特定 flask 封裝
 - 例如表單。以滑鼠右鍵按一下 [**env**]
和 [**安裝下列 Python 封裝 (請務必依照這個順序
)**]：

    flask==0.9
    flask-login
    flask-openid
    flask-mail==0.7.6
    sqlalchemy==0.7.9
    flask-sqlalchemy==0.16
    sqlalchemy-migrate==0.7.2
    flask-whooshalchemy==0.55a
    flask-wtf==0.8.4
    pytz==2013b
    flask-babel==0.8
    flup

![Alt text](./media/documentdb-python-application/image11.png)

**注意：**在罕見情況下，輸出視窗中可能出現失敗。如果
發生此情形，請檢查錯誤是否與清除有關。有時是
清理失敗，但安裝卻成功 (在輸出視窗中向上捲動
來驗證這一點)。
<a name="verify-the-virtual-environment"></a> 如果發生這種情況，您可以放心繼續進行。

</h1>
<a name="_Toc395888522"></a><a name="_Toc395809331">驗證虛擬環境</a>
======================================================================================


讓我們來確定一切都已正確安裝。啟動網站
，方法是按一下 **F5**，這會啟動 flask 開發伺服器
並啟動網頁瀏覽器。應該會出現下列網頁：

![Alt text](./media/documentdb-python-application/image12.png)

<a name="_Toc395888523"></a><a name="_Toc395809332">將 DocumentDB 新增至專案</a>
=========================================================================================

DocumentDB Python SDK 會託管於 PyPi 中，而且可以使用 pip 進行安裝
。

展開 [方案總管] 中的 [Python 環境] 節點，以滑鼠右鍵按一下
您的環境，然後選取 [安裝 Python 封裝...]

![Alt text](./media/documentdb-python-application/image13.png)

輸入 "--pre pydocumentdb"，其為 PyPi 封裝的名稱。如果
您想要控制要安裝的版本，您可以選擇性地提供已知版本
將版本留空可確保安裝的是最新
穩定版本。請注意，您必須輸入
完整名稱 "--pre pydocumentdb"。

![Alt text](./media/documentdb-python-application/image14.png)

這會下載 pydocumentdb 封裝，並將它安裝到您的
環境中，完成後您應該會看到 pydocumentdb 已列為
您環境下的一個模組。

</h1>
<a name="_Toc395888524"></a><a name="_Toc395809333">建立資料庫、集合和文件定義</a>
============================================================================================================

新增下列 python 檔案 - **forms.py** 並將它新增至 [方案總管] 中
名為 tutorial 的資料夾。將下列程式碼新增至
forms.py。我們打算建立投票應用程式。這可透過
建立三個可根據使用者輸入進行切換的布林欄位來完成。

</h1>

    #forms.py
    from flask.ext.wtf import Form
    from wtforms import TextField, BooleanField
    from wtforms.validators import Required
    import pydocumentdb.documents as documents
    import pydocumentdb.document_client as document_client
    import pydocumentdb.errors as errors
    class LoginForm(Form):
        openid = TextField('openid')
        remember_me = BooleanField('remember_me', default = False)
        remember_me1 = BooleanField('remember_me1', default = False)
        remember_me2 = BooleanField('remember_me2', default = False)

<a name="_Toc395888520"></a>
============================

### <a name="_Toc395809338">建立資料庫、集合和文件</a>

\#注意：將您的驗證認證保存
在組態檔而非您的應用程式中會被視為較安全的方法。為了簡單起見，我們
將它放置在原始碼中。在 views.py 中建立新的函數，並將它命名為
create。將下列程式碼附加至 **views.py**。請勿刪除任何
現有程式碼。

</h1>

    @app.route('/create')
    def create():
        """Renders the contact page."""
        host = 'https://meetdemo.documents.azure.com:443/'
        masterKey = '7xPDjHxJSuAUPI2BEWXF2VNVO6c3MN4q+941NAQwPphIqOGW/gE+pB1CQNp4K2F9/4T1bTl040t6JDeyCBmj3A=='
        client = document_client.DocumentClient(host, {'masterKey': masterKey})
        databases = client.ReadDatabases().ToArray()
        #delete any existing databases for simplicity
        for database in databases:
            client.DeleteDatabase(database['_self'])
        #create database
        db = client.CreateDatabase({ 'id': 'sample database' })
        # create collection
        collection = client.CreateCollection(db['_self'],{ 'id': 'sample collection' })
        # create document
        document = client.CreateDocument(collection['_self'],
        { 'id': 'sample document',
        'Web Site': '0 votes',
        'Cloud Service': '0 votes',
        'Virtual Machine': '0 votes',
        'name': 'sample document' })
        return render_template('create.html',title='Create Page',year=datetime.now().year,
        message='You just created a new database - sample database a collection - sample collection and a document - sample document that has your votes. Your old votes have been deleted')

<a name="_Toc395888529"></a><a name="_Toc395809338">接受使用者投票和更新文件</a>
=================================================================================================

### <a name="_Toc395809338">新增必要的匯入

<a name="_Toc395888529"></a>
============================

在 [**views.py**] 頂端新增下列匯入陳述式。這些陳述式
將匯入 DocumentDB 的 PythonSDK 和 Flask 封裝。


    from wtforms import Form, BooleanField, TextField, PasswordField, validators
    from forms import LoginForm
    import pydocumentdb.documents as documents
    import pydocumentdb.document_client as document_client
    import pydocumentdb.errors as errors

### <a name="_Toc395809338">讀取資料庫、集合和文件</a>

將下列程式碼新增至 **views.py**。這會負責設定
表單、讀取資料庫、集合和文件。請勿刪除 
views.py 中任何現有程式碼。只需將此程式碼附加至結尾即可。

    @app.route('/vote', methods=['GET', 'POST'])
    def vote(): 
        form = LoginForm()
        replaced_document ={}
        if form.validate_on_submit(): # is user submitted vote  
            host = 'https://meetdemo.documents.azure.com:443/'
            masterKey = '7xPDjHxJSuAUPI2BEWXF2VNVO6c3MN4q+941NAQwPphIqOGW/gE+pB1CQNp4K2F9/4T1bTl040t6JDeyCBmj3A=='
            client = document_client.DocumentClient(host, {'masterKey': masterKey})
            databases = client.ReadDatabases().ToArray()   #Read database
            db =databases[0] #For simplicity we are assuming there is only one database
            collections = client.ReadCollections(db['_self']).ToArray() #Read collection
            coll =collections[0] #For simplicity we are assuming there is only one collection
            documents = client.ReadDocuments(coll['_self']).ToArray() #Read document
            doc = documents[0]   #For simplicity we are assuming there is only document
    
### <a name="_Toc395809338">Registering the vote and modifying the document</a>

            replaced_document = doc
            if form.remember_me.data:
                print 'choice 1'             
                setvar = doc['Web Site']
                finalvar = setvar.split();
                votes = int(finalvar[0])
                votes = votes+1
                doc['Web Site'] = str(votes)+' '+finalvar[1]
                replaced_document = client.ReplaceDocument(doc['_self'],
                                                       doc)
                print replaced_document
            elif form.remember_me1.data:
                print 'choice 2'             
                setvar = doc['Cloud Service']
                finalvar = setvar.split();
                votes = int(finalvar[0])
                votes = votes+1
                doc['Cloud Service'] = str(votes)+' '+finalvar[1]
                replaced_document = client.ReplaceDocument(doc['_self'],
                                                       doc)
            else:
                print 'choice 2'             
                setvar = doc['Virtual Machine']
                finalvar = setvar.split();
                votes = int(finalvar[0])
                votes = votes+1
                doc['Virtual Machine'] = str(votes)+' '+finalvar[1]
                replaced_document = client.ReplaceDocument(doc['_self'],
                                                       doc)

### <a name="_Toc395809338">顯示結果</a>

    return render_template('results.html', 
            title = 'Website = ' + replaced_document['Web Site'] + '\n' +'Cloud Service = ' + replaced_document['Cloud Service'] + '\n' + 'Virtual Machine = ' + replaced_document['Virtual Machine'])
        else :        
            return render_template('vote.html', 
            title = 'Vote',
            form = form,
            providers = app.config['OPENID_PROVIDERS'])

    @app.route('/results')
    def results():
        """Renders the results page."""
        return render_template(
            'results.html',
            title='Results',
            year=datetime.now().year,
            message='Your application description page.')


### <a name="_Toc395809338">建立 html 檔案</a>

在 [範本] 資料夾下加入下列 html 檔案。
create.html、results.html、vote.html

將下列程式碼新增至 **create.html**。它會負責顯示
我們已建立新資料庫、集合和文件的訊息。

    {% extends "layout.html" %}
    {% block content %}
    <h2>{{ title }}.</h2>
    <h3>{{ message }}</h3>
    <p><a href="{{ url_for('vote') }}" class="btn btn-primary btn-large">Click here to Vote &raquo;</a></p>
    {% endblock %}

將下列程式碼新增至 **results.html**。它會負責顯示
民調的結果。
    
    {% extends "layout.html" %}
    {% block content %}
    <h3>Results of the vote</h3>
    <pre>{{ title }}</pre>
    <p><a href="{{ url_for('vote') }}" class="btn btn-primary btn-large">Vote Again &raquo;</a></p>
    {% endblock %}

將下列程式碼新增至 **vote.html**。它會負責顯示
民調和接受投票。註冊投票時，控制項會
傳遞到 views.py，在這裡我們將認可投票並
相應地附加文件。

    {% extends "layout.html" %}
    {% block content %}
    <h1>What is your favorite way to host an application on Azure?</h1>
    <form action="" method="post" name="login">
    {{form.hidden_tag()}}
    <p> </p>
        {{form.remember_me}} Website
        {{form.remember_me1}} Cloud
        {{form.remember_me2}} Virtual Machine
        <p><input type="submit" value="Cast your vote"></p>
    </form>
    {% endblock %}
    
<a name="_Toc395888529"></a>
============================

刪除 **layout.html** 中的程式碼，並將它取代為下列程式碼。它
會負責設定 navbar 以及個別的路由 url。

刪除 index.html 中的程式碼，並將它取代為下列程式碼。此程式碼
可做為您應用程式的登陸頁面。

</h1>
<a name="_Toc395888532"></a><a name="_Toc395809341">新增組態檔並變更 \_\_init\_\_.py</a>。
----------------------------------------------------------------------------------------------------------------

以滑鼠右鍵按一下專案名稱 tutorial，並新增檔案 - **config.py**。
Flask 中的表單需要使用此組態。您也可能會用它來提供
秘密金鑰。將下列程式碼新增至 config.py
    
    CSRF_ENABLED = True
    SECRET_KEY = 'you-will-never-guess'
    OPENID_PROVIDERS = [
       ]

同樣地附加 **\_\_init\_\_.py 檔案**。在
名為 tutorial 的資料夾下找到它。將原始程式碼取代為下列程式碼。此程式碼
會負責檢視與 config.py 檔案之間的連線。

    from flask import Flask
    app = Flask(__name__)
    app.config.from_object('config')
    import tutorial.views

在依照上述步驟進行之後，您的方案
總管應該看起來應如下所示

![Alt text](./media/documentdb-python-application/image15.png)

<a name="_Toc395888534"></a><a name="_Toc395809343"></a><a name="_Toc395637774">在本機執行您的應用程式</a>
============================

在 Visual Studio 中按 F5 鍵或執行按鈕，您應該會
在畫面上看到下列內容。

![Alt text](./media/documentdb-python-application/image16.png)

按一下 [vote]，並選取您的選項。

![Alt text](./media/documentdb-python-application/image17.png)

對於您投的每張票，都會讓適當的計數器遞增。在
下次投票之後，您就可以看到結果。

![Alt text](./media/documentdb-python-application/image18.png)

</h1>
<a name="_Toc395888534"></a><a name="_Toc395809343"></a><a name="_Toc395637774">將應用程式部署至 Azure 網站</a>
========================================================================================================================

您已經擁有可在DocumentDB 正常運作的完整應用程式，
我們現在要將此應用程式部署至 Azure 網站。以滑鼠右鍵按一下 
[方案總管] 中的專案 (請確定您已經沒有在本機上執行此應用程式
)，然後選取 [發佈]。

![Alt text](./media/documentdb-python-application/image19.png)


建立您的 Azure 網站，方法是提供您的認證並建立
新網站/重複使用舊的網站。

![Alt text](./media/documentdb-python-application/image20.png)


幾秒後，Visual Studio 便會完成發佈 Web 
應用程式並啟動瀏覽器，您可以在瀏覽器中看到您方便好用的應用程式
已在 Azure 中執行！

</h1>
<a name="_Toc395809338">結論</a>
==========

恭喜！您剛剛已使用 Azure DocumentDB 建立您的第一個 Python 應用程式，
並已將它發佈至 Azure 網站。

如果您想要完整的方案，請 [按一下這裡][]。(注意：您將
仍然必須加入虛擬環境，安裝 python 工具和
封裝，如上所述)

</h1>

  [按一下這裡]: http://go.microsoft.com/fwlink/?LinkID=509840&clcid=0x409
  [按一下這裡可存取 Flask 教學課程 (英文)]: http://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-i-hello-world

  [Visual Studio Express]: http://www.visualstudio.com/en-us/products/visual-studio-express-vs.aspx
  [開始]: https://pytools.codeplex.com/releases/view/123624
  [1]: http://go.microsoft.com/fwlink/?linkid=254281&clcid=0x409
  [Microsoft Web Platform Installer]: http://www.microsoft.com/web/downloads/platform.aspx
