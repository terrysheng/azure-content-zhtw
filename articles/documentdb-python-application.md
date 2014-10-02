<properties title="Build a web application with Python and Flask (MVC) using DocumentDB" pageTitle="Build a web app with Python and Flask using DocumentDB | Azure" description="Learn how to use DocumentDB to store and access data from an Python and Flask (MVC) web application hosted on Azure."  metaKeywords="NoSQL, DocumentDB,  database, document-orientated database, JSON, account" services="documentdb"  solutions="data-management" documentationCenter=""  authors="hawong" manager="jhubbard" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="documentdb" ms.workload="data-management" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/20/2014" ms.author="hawong"></tags>

# <a name="_Toc395888515"></a><a name="_Toc395809324">使用 DocumentDB 建置具有 Python 和 Flask (MVC) 的 Web 應用程式</a>

<a name="_Toc395809324">

# <a name="_Toc395888516"></a><a name="_Toc395809325"></a><a name="_Toc389865467"></a><a name="_Toc389828008">概觀</a>

## <a name="_Toc395888517"></a><a name="_Toc395809326">案例</a>

為了特別說明客戶要如何有效率地利用 Azure DocumentDB 來
儲存和查詢 JSON 文件，本文件提供如何
使用 Azure DocumentDB 建置投票 Web 應用程式的
完整逐步解說。

本逐步解說說明如何使用 Azure 所提供的 DocumentDB 服務，
來儲存和存取 Azure 上所託管 Python Web 應用程式
的資料，並假設您先前已有使用 Python
和 Azure 網站的經驗。

您將了解：

1\. 建立和佈建 DocumentDB 帳戶

2\. 建立 Python MVC 應用程式

3\. 從 Web 應用程式連線到 Azure DocumentDB 和使用 Azure DocumentDB

4\. 將 Web 應用程式部署至 Azure 網站

按照本逐步解說進行後，您將建置可讓您舉行投票活動的簡單投票
應用程式。

![替代文字][]

<a name="_Toc395888520"></a><a name="_Toc395809329">必要條件</a>

在依照本文中的指示進行之前，您應該確定
已安裝下列項目：

Visual Studio 2013 (或免費版本的 [Visual Studio Express][] (英文))

[這裡][]的 Python Tools for Visual Studio

[這裡][1]提供的 Azure SDK for Visual Studio 2013 2.4 版或更高版本

Azure 跨平台命令列工具，可透過 [Microsoft Web Platform Installer][] (英文) 取得

# <a name="_Toc395888519"></a><a name="_Toc395809328">建立 DocumentDB 資料庫帳戶</a>

若要在 Azure 中佈建 DocumentDB 資料庫帳戶，請開啟 Azure
管理入口網站，並按一下首頁上的 [Azure Gallery] 磚，
或按一下畫面左下角的 “+”。

![替代文字][2]

這會開啟 [Azure Gallery]，您可以在其中從多個可用 Azure 服務
進行選取。在 Gallery 中，從類別清單中選取 [Data, storage and backup]。

![替代文字][3]

從這裡，選取 Azure DocumentDB 的選項

![替代文字][4]

然後選取畫面底部的 [建立]

![替代文字][5]

這會開啟 [New DocumentDB] 刀鋒，您可以在其中指定
新帳戶的名稱、區域、規模、資源群組
和其他設定。

![替代文字][6]

提供好帳戶值之後，按一下 [建立]，佈建程序
即會開始建立資料庫帳戶。佈建程序完成時，
您會在入口網站的通知區域中看到通知，而且
開始畫面上的磚 (如果您已選擇建立磚) 將會變更成
顯示已完成的動作。

![替代文字][7]

佈建完成之後，按一下開始畫面中的 [DocumentDB] 磚
，這個新建立 DocumentDB 帳戶的
主要刀鋒就會出現。

![替代文字][8]
![替代文字][9]

使用 [金鑰] 按鈕，存取端點 URL 和主要金鑰，
將這些項目複製到剪貼簿並將它們保存在隨手可得的地方，
因為我們將在接下來要建立的 Web 應用程式中使用這些值。

</a>

# <a name="_Toc395888520"></a><a name="_Toc395809329">建立新的 Python Flask Web 應用程式</a>

開啟 Visual Studio，並按一下 [檔案] -\> [新增專案] -\> [Python]，然後選擇 [Flask Web
專案] (名稱為 **tutorial**)。系統會詢問您是否要
安裝外部套件。按一下 [安裝] 安裝至虛擬環境，
然後按一下 [建立]。這會設定專案所需的 Python
虛擬環境。

對於 Flask 的初學者而言，這個網路架構可協助我們在 Python 中更快速地建置
Web 應用程式。[按一下這裡可存取 Flask 教學課程][] (英文)。

![替代文字][10]

# <a name="_Toc395888520"></a><a name="_Toc395809329">將 Flask 封裝新增至專案</a>

專案設定好之後，您需要新增專案所需的特定 Flask 封裝
(例如表單)。在 [env] 上按一下滑鼠右鍵，
並**安裝下列 Python 封裝 (務必要按照此順序)**：

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

![替代文字][11]

**注意：**在罕見情況下，輸出視窗中可能出現失敗。如果發生此情形，請檢查錯誤是否與清除有關。
有時是清理失敗，但安裝卻成功 (在輸出視窗中向上捲動來驗證這一點)。
<a name="verify-the-virtual-environment"></a>如果發生這種情況，
您可以放心繼續進行。

</h1>
# <a name="_Toc395888522"></a><a name="_Toc395809331">驗證虛擬環境</a>

讓我們來確定一切都安裝正確。按一下
 **F5** 鍵啟動網站。這將會啟動 Flask 開發伺服器和您的網頁瀏覽器。
應該會出現下列網頁：

![替代文字][12]

# <a name="_Toc395888523"></a><a name="_Toc395809332">將 DocumentDB 新增至專案</a>

DocumentDB Python SDK 託管於 PyPi 中，而且可以使用 pip 進行安裝。

在 [方案總管] 中展開 [Python 環境] 節點，並於
您的環境上按一下滑鼠右鍵，然後選取 [Install Python Package...]。

![替代文字][13]

輸入 "--pre pydocumentdb"，其為 PyPi 封裝的名稱。如果您
想要控制要安裝的版本，則可以選擇提供已知版本，
將版本省略可確保安裝的是最新的
穩定版本。請記住，您需要輸入
完整名稱 "--pre pydocumentdb"。

![替代文字][14]

這會將 pydocumentdb 封裝下載和安裝至
環境，完成之後，您應該會看到 pydocumentdb
列為您環境下的模組。

</h1>
# <a name="_Toc395888524"></a><a name="_Toc395809333">建立資料庫、集合和文件定義</a>

新增下列 python 檔案 – **forms.py**，並將它新增至 [方案總管] 中
稱為 tutorial 的資料夾。將下列程式碼新增至
forms.py。我們將會建立投票應用程式。做法是
建立三個布林值欄位，根據使用者輸入的資料進行切換。

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

# <a name="_Toc395888520"></a>

### <a name="_Toc395809338">建立資料庫、集合和文件</a>

\#附註：將驗證認證保存在組態檔中，會比在應用程式中
安全許多。但為求簡化，我們將它放在
原始程式碼中。在 views.py 中建立新函數，並將它稱為 create。
將下列內容附加至 **views.py**。請不要刪除任何
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

# <a name="_Toc395888529"></a><a name="_Toc395809338">接受使用者投票和更新文件</a>

### <a name="_Toc395809338">新增必要匯入

# <a name="_Toc395888529"></a>

在 **views.py** 頂端新增下列匯入陳述式。這些陳述式
將匯入 DocumentDB 的 PythonSDK 和 Flask 封裝。

    from wtforms import Form, BooleanField, TextField, PasswordField, validators
    from forms import LoginForm
    import pydocumentdb.documents as documents
    import pydocumentdb.document_client as document_client
    import pydocumentdb.errors as errors

### <a name="_Toc395809338">讀取資料庫、集合和文件</a>

將下列程式碼新增至 **views.py**。此程式碼可設定
表單以及讀取資料庫、集合和文件。請不要刪除
views.py 中的任何現有程式碼。只要將此程式碼附加至結尾即可。

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

### <a name="_Toc395809338">註冊投票和修改文件</a>

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

在範本資料夾下，新增下列 html 檔案：
create.html、results.html、vote.html

將下列程式碼新增至 **create.html**。此程式碼可顯示
訊息指出我們已建立新的資料庫、集合和文件。

    {% extends "layout.html" %}
    {% block content %}
    <h2>{{ title }}.</h2>
    <h3>{{ message }}</h3>
    <p><a href="{{ url_for('vote') }}" class="btn btn-primary btn-large">Click here to Vote &raquo;</a></p>
    {% endblock %}

將下列程式碼新增至 **results.html**。此程式碼可顯示
投票結果。

    {% extends "layout.html" %}
    {% block content %}
    <h3>Results of the vote</h3>
    <pre>{{ title }}</pre>
    <p><a href="{{ url_for('vote') }}" class="btn btn-primary btn-large">Vote Again &raquo;</a></p>
    {% endblock %}

將下列程式碼新增至 **vote.html**。此程式碼可顯示
投票以及接受投票。註冊投票時，控制權
會傳遞給 views.py，我們將在其中辨識投票以及
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

# <a name="_Toc395888529"></a>

刪除 **layout.html** 中的程式碼，並將它取代為下列程式碼。此程式碼
可設定 navbar 以及個別的路由 url。

刪除 index.html 中的程式碼，並將它取代為下列程式碼。此程式碼
可做為您應用程式的登陸頁面。

</h1>
## <a name="_Toc395888532"></a><a name="_Toc395809341">新增組態檔並變更 \_\_init\_\_.py</a>

在專案名稱教學課程上按一下滑鼠右鍵，並新增檔案 – **config.py**。
Flask 中的表單需要此組態。您也可以用它來提供
秘密金鑰。將下列程式碼新增至 config.py。

    CSRF_ENABLED = True
    SECRET_KEY = 'you-will-never-guess'
    OPENID_PROVIDERS = [
       ]

同樣地，附加 **\_\_init\_\_.py 檔案**。在稱為 tutorial 的
資料夾下找到它。將原始程式碼取代為下列程式碼。此程式碼
可連接檢視與 config.py 檔案。

    from flask import Flask
    app = Flask(__name__)
    app.config.from_object('config')
    import tutorial.views

按照上面所說的步驟進行之後，方案總管的外觀應該會變成下面這樣：

![替代文字][15]

# <a name="_Toc395888534"></a><a name="_Toc395809343"></a><a name="_Toc395637774">在本機執行您的應用程式</a>

在 Visual Studio 中按 F5 鍵或執行按鈕，您應該就會在畫面上看到
下列內容。

![替代文字][16]

按一下 [vote]，並選取您的選項。

![替代文字][17]

對於您投的每張票，都會讓適當的計數器遞增。下次投票之後，
您就可以看到結果。

![替代文字][18]

</h1>
# <a name="_Toc395888534"></a><a name="_Toc395809343"></a><a name="_Toc395637774">將應用程式部署至 Azure 網站</a>

您已經擁有可在
DocumentDB 正常運作的完整應用程式，我們現在要將此應用程式部署至 Azure 網站。在 [方案總管] 的 [專案] 上按一下滑鼠右鍵
 (請確定您已沒有在本機上執行此應用程式)，
然後選取 [發佈]。

![替代文字][19]

提供認證，並建立新的網站或重複使用舊的網站，
以設定 Azure 網站。

![替代文字][20]

幾秒後，Visual Studio 就會發佈好 Web
應用程式並啟動瀏覽器，您可以在其中看到您方便好用的應用程式
已在 Azure 中執行！

</h1>
# <a name="_Toc395809338">結論</a>

恭喜！您剛剛已經使用 Azure DocumentDB 建置您的第一個 Python 應用程式，
並將它發佈至 Azure 網站。

如果您想要完整的方案，請[按一下這裡][] (附註：您還是需要
如上所述新增虛擬環境並安裝 python 工具和封裝)。

</h1>

  [替代文字]: ./media/documentdb-python-application/image1.png
  [Visual Studio Express]: http://www.visualstudio.com/en-us/products/visual-studio-express-vs.aspx
  [這裡]: https://pytools.codeplex.com/releases/view/123624
  [1]: http://go.microsoft.com/fwlink/?linkid=254281&clcid=0x409
  [Microsoft Web Platform Installer]: http://www.microsoft.com/web/downloads/platform.aspx
  [2]: ./media/documentdb-python-application/image2.png
  [3]: ./media/documentdb-python-application/image3.png
  [4]: ./media/documentdb-python-application/image4.png
  [5]: ./media/documentdb-python-application/image5.png
  [6]: ./media/documentdb-python-application/image6.png
  [7]: ./media/documentdb-python-application/image7.png
  [8]: ./media/documentdb-python-application/image8.png
  [9]: ./media/documentdb-python-application/image9.png
  [按一下這裡可存取 Flask 教學課程]: http://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-i-hello-world
  [10]: ./media/documentdb-python-application/image10.png
  [11]: ./media/documentdb-python-application/image11.png
  [12]: ./media/documentdb-python-application/image12.png
  [13]: ./media/documentdb-python-application/image13.png
  [14]: ./media/documentdb-python-application/image14.png
  [15]: ./media/documentdb-python-application/image15.png
  [16]: ./media/documentdb-python-application/image16.png
  [17]: ./media/documentdb-python-application/image17.png
  [18]: ./media/documentdb-python-application/image18.png
  [19]: ./media/documentdb-python-application/image19.png
  [20]: ./media/documentdb-python-application/image20.png
  [按一下這裡]: http://go.microsoft.com/fwlink/?LinkID=509840&clcid=0x409
