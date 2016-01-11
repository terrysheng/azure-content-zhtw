<properties
    pageTitle="使用 DocumentDB 進行 Python Flask Web 應用程式開發 | Microsoft Azure"
    description="檢閱有關如何使用 DocumentDB 來儲存和存取 Azure 上主控之 Python Flask Web 應用程式資料的資訊。尋找應用程式開發解決方案。" 
	keywords="應用程式開發, 資料庫教學課程, python flask, python Web 應用程式, python Web 開發, documentdb, azure, Microsoft azure"
    services="documentdb"
    documentationCenter="python"
    authors="ryancrawcour"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="documentdb"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="python"
    ms.topic="hero-article"
    ms.date="12/23/2015"
    ms.author="ryancraw"/>

# 使用 DocumentDB 進行 Python Flask Web 應用程式開發

> [AZURE.SELECTOR]
- [.NET](documentdb-dotnet-application.md)
- [Node.js](documentdb-nodejs-application.md)
- [Java](documentdb-java-application.md)
- [Python](documentdb-python-application.md)

本教學課程說明如何使用 Azure DocumentDB，來儲存和存取 Azure 上所託管的 Python Web 應用程式資料，並假設您先前已有使用 Python 和 Azure 網站的經驗。

此資料庫教學課程涵蓋：

1. 建立和佈建 DocumentDB 帳戶。
2. 建立 Python MVC 應用程式。
3. 從 Web 應用程式連線到 Azure DocumentDB 和使用 Azure DocumentDB。
4. 將 Web 應用程式部署至 Azure 網站。

按照本教學課程進行後，您將建置可讓您舉行投票活動的簡單投票應用程式。

![本資料庫教學課程所建立的待辦事項清單 Web 應用程式的螢幕擷取畫面](./media/documentdb-python-application/image1.png)


## 資料庫教學課程必要條件

在依照本文中的指示進行之前，您應確定已安裝下列項目：

- [Visual Studio 2013](http://www.visualstudio.com/) 或更高版本，或免費版本的 [Visual Studio Express]()。本教學課程中的指示專為 Visual Studio 2015 所編寫。 
- [GitHub](http://microsoft.github.io/PTVS/) 的 Python Tools for Visual Studio。本教學課程使用 Python Tools for VS 2015。 
- [azure.com](https://azure.microsoft.com/downloads/) 提供 Azure Python SDK for Visual Studio 2.4 版或更高版本。我們使用 Microsoft Azure SDK for Python 2.7。
- 來自 [python.org][2] 的 Python 2.7。我們使用 Python 2.7.11。 

> [AZURE.IMPORTANT]如果您是第一次安裝 Python 2.7，請確定在自訂 Python 2.7.11 畫面中，選取 [將 python.exe 加入路徑]。
> 
>    ![自訂 Python 2.7.11 的螢幕擷取畫面，您需要選取 [將 python.exe 加入路徑]](./media/documentdb-python-application/image2.png)

- 來自 [Microsoft 下載中心][3]的 Microsoft Visual C++ Compiler for Python 2.7。

## 步驟 1：建立 DocumentDB 資料庫帳戶

讓我們從建立 DocumentDB 帳戶開始。如果您已經擁有帳戶，可以跳到[步驟 2：建立新的 Python Flask Web 應用程式](#Step-2:-Create-a-new-Python-Flask-Web-Application)。

[AZURE.INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

<br/>
我們現在將從頭開始逐步解說如何建立新的 Python Flask Web 應用程式。

## 步驟 2：建立新的 Python Flask Web 應用程式

1. 在 Visual Studio 的 [**檔案**] 功能表中，指向 [**新增**]，然後按一下 [**專案**]。

    [**新增專案** ] 對話方塊隨即出現。

2. 在左窗格中，依序展開 [範本]、[Python]，再按一下 [Web]。

3. 在中央窗格中選取 [Flask Web 專案]，並在 [名稱] 方塊中輸入 [教學課程]，再按一下 [確定]。請記住，Python 封裝名稱應該全小寫，如 [Python 程式碼文件編寫指南](https://www.python.org/dev/peps/pep-0008/#package-and-module-names)中所述。

	對於 Python Flask 的初學者而言，此 Web 應用程式開發架構可協助您在 Python 中更快速地建置 Web 應用程式。

	![Visual Studio 中左側為反白顯示的 Python、中間為選取的「Python Flask Web 專案」，以及 [名稱] 方塊中名稱為 tutorial 的 [新增專案] 視窗螢幕擷取畫面](./media/documentdb-python-application/image9.png)

4. 在 [Python Tools for Visual Studio] 視窗中，按一下 [虛擬環境安裝]。

	![資料庫教學課程 - Python Tools for Visual Studio 視窗的螢幕擷取畫面](./media/documentdb-python-application/image10.png)

5. 在 [加入虛擬環境] 視窗中，接受預設值並使用 Python 2.7 當做基本環境，因為 PyDocumentDB 目前不支援 Python 3.x；接著按一下 [建立]。這會設定專案所需的 Python 虛擬環境。

	![資料庫教學課程 - Python Tools for Visual Studio 視窗的螢幕擷取畫面](./media/documentdb-python-application/image10_A.png)

    當成功安裝環境之後，輸出視窗會顯示 `Successfully installed Flask-0.10.1 Jinja2-2.8 MarkupSafe-0.23 Werkzeug-0.11.3 itsdangerous-0.24 'requirements.txt' was installed successfully.`。

## 步驟 3：修改 Python Flask Web 應用程式

### 將 Python Flask 封裝加入專案

專案設定好之後，您需要加入專案所需的特定 Flask 封裝，包括 pydocumentdb (DocumentDB 的 Python 封裝)。

1. 在「方案總管」中開啟名為 **requirements.txt** 的檔案，並以下列內容取代，接著儲存檔案：

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
    	pydocumentdb>=1.0.0

2. 在「方案總管」中以滑鼠右鍵按一下 [env]，然後按一下 [從 requirements.txt 安裝]。

	![螢幕擷取畫面，顯示從清單中反白顯示的 requirements.txt 安裝時選取的 env (Python 2.7)。](./media/documentdb-python-application/image11.png)

    成功安裝之後，輸出視窗會顯示下列資訊：

        Successfully installed Babel-2.1.1 Tempita-0.5.2 WTForms-2.1 Whoosh-2.7.0 blinker-1.4 decorator-4.0.6 flask-0.9 flask-babel-0.8 flask-mail-0.7.6 flask-sqlalchemy-0.16 flask-whooshalchemy-0.55a0 flask-wtf-0.8.4 flup-1.0.2 pydocumentdb-1.4.2 pytz-2013b0 speaklater-1.3 sqlalchemy-0.7.9 sqlalchemy-migrate-0.7.2

    > [AZURE.NOTE]在罕見情況下，輸出視窗中可能會出現失敗。如果發生此情形，請檢查錯誤是否與清除有關。有時是清理失敗，但安裝卻成功 (在輸出視窗中向上捲動來驗證這一點)。您可以[驗證虛擬環境](#verify-the-virtual-environment)來檢查安裝。 如果安裝失敗，但驗證成功，則可以繼續。


### 驗證虛擬環境

讓我們來確定一切都安裝正確。

1. 按下 CTRL+SHIFT+B 建置解決方案。
2. 建置成功後，按下 F5 啟動網站。這會啟動 Flask 開發伺服器和您的網頁瀏覽器。應該會出現下列網頁。

	![在瀏覽器中會顯示空白的 Python Flask Web 開發專案](./media/documentdb-python-application/image12.png)

3. 按下Shift+F5 停止對網站進行偵錯。

### 建立資料庫、集合和文件定義

現在請加入新檔案並更新其他檔案，以建立您的投票應用程式。

1. 在「方案總管」中，以滑鼠右鍵按一下 [教學課程] 專案，然後按一下 [加入]，再按一下 [新增項目]。選取 [空白 Python 檔案]，並將檔案命名為 **forms.py**。  
2. 將下列程式碼加入 forms.py 檔案，然後儲存該檔案。

```python
from flask.ext.wtf import Form
from wtforms import RadioField

class VoteForm(Form):
	deploy_preference  = RadioField('Deployment Preference', choices=[
        ('Web Site', 'Web Site'),
        ('Cloud Service', 'Cloud Service'),
        ('Virtual Machine', 'Virtual Machine')], default='Web Site')
```


### 將必要匯入新增至 views.py

1. 在「方案總管」中，展開 [教學課程] 資料夾，然後開啟 **views.py** 檔案。 
2. 在 **views.py** 檔案頂端新增下列 import 陳述式，然後儲存檔案。這些陳述式會匯入 DocumentDB 的 PythonSDK 和 Flask 封裝。

	```python
	from forms import VoteForm
	import config
	import pydocumentdb.document_client as document_client
	```


### 建立資料庫、集合和文件

- 在 **views.py** 檔案結尾處加入以下程式碼。此程式碼可建立表單所使用的資料庫。請勿刪除 **views.py** 中任何現有的程式碼。只需將它附加至結尾。

```python
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
    collection = client.CreateCollection(db['_self'],{ 'id': config.DOCUMENTDB_COLLECTION }, { 'offerType': 'S1' })

    # Create document
    document = client.CreateDocument(collection['_self'],
        { 'id': config.DOCUMENTDB_DOCUMENT,
          'Web Site': 0,
          'Cloud Service': 0,
          'Virtual Machine': 0,
          'name': config.DOCUMENTDB_DOCUMENT 
        })

    return render_template(
       'create.html',
        title='Create Page',
        year=datetime.now().year,
        message='You just created a new database, collection, and document.  Your old votes have been deleted')
```

> [AZURE.TIP]**CreateCollection** 方法會採用選擇性的 **RequestOptions** 做為第三個參數。這可以用來指定集合的優惠類型。如果未提供 offerType 值，則將會使用預設的優惠類型來建立集合。如需 DocumentDB 優惠類型的詳細資訊，請參閱 [DocumentDB 中的效能層級](documentdb-performance-levels.md)。


### 讀取資料庫、集合、文件並送出表單

- 在 **views.py** 檔案結尾處加入以下程式碼。此程式碼可設定表單並讀取資料庫、集合和文件。請勿刪除 **views.py** 中任何現有的程式碼。只需將它附加至結尾。

```python
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
```


### 建立 HTML 檔案

1. 在「方案總管」中，以滑鼠右鍵按一下 [教學課程] 資料夾中的 [範本] 資料夾，，然後按一下 [加入]，再按一下 [新增項目]。 
2. 選取 [HTML 頁面]，在名稱方塊中輸入 **create.html**。 
3. 重複步驟 1 和 2 來建立其他兩個 HTML 檔案，分別是 results.html 和 vote.html。
4. 將下列程式碼新增至 <body> 元素中的 **create.html**。此程式碼可顯示訊息，指出我們已建立新的資料庫、集合和文件。

	```html
	{% extends "layout.html" %}
	{% block content %}
	<h2>{{ title }}.</h2>
	<h3>{{ message }}</h3>
	<p><a href="{{ url_for('vote') }}" class="btn btn-primary btn-large">Vote &raquo;</a></p>
	{% endblock %}
	```

5. 將下列程式碼新增至 <body> 元素中的 **results.html**。此程式碼會顯示投票結果。

	```html
	{% extends "layout.html" %}
	{% block content %}
	<h2>Results of the vote</h2>
		<br />
		
	{% for choice in vote_object.choices %}
	<div class="row">
		<div class="col-sm-5">{{choice}}</div>
	        <div class="col-sm-5">
	        	<div class="progress">
	        		<div class="progress-bar" role="progressbar" aria-valuenow="{{vote_object.choices[choice]}}" aria-valuemin="0" aria-valuemax="{{vote_object.total_votes}}" style="width: {{(vote_object.choices[choice]/vote_object.total_votes)*100}}%;">
	                    		{{vote_object.choices[choice]}}
				</div>
			</div>
	        </div>
	</div>
	{% endfor %}
	
	<br />
	<a class="btn btn-primary" href="{{ url_for('vote') }}">Vote again?</a>
	{% endblock %}
	```

6. 將下列程式碼新增至 <body> 元素中的 **vote.html**。此程式碼會顯示並接受投票。註冊投票時，控制權會傳遞給 views.py，我們將在其中辨識投票以及相應地附加文件。

	```html
	{% extends "layout.html" %}
	{% block content %}
	<h2>What is your favorite way to host an application on Azure?</h2>
	<form action="" method="post" name="vote">
		{{form.hidden_tag()}}
	        {{form.deploy_preference}}
	        <button class="btn btn-primary" type="submit">Vote</button>
	</form>
	{% endblock %}
	```

7. 在 [範本] 資料夾中，使用下列程式碼取代 **index.html** 的內容。此程式碼可做為您應用程式的登陸頁面。
	
	```html
	{% extends "layout.html" %}
	{% block content %}
	<h2>Python + DocumentDB Voting Application.</h2>
	<h3>This is a sample DocumentDB voting application using PyDocumentDB</h3>
	<p><a href="{{ url_for('create') }}" class="btn btn-primary btn-large">Create/Clear the Voting Database &raquo;</a></p>
	<p><a href="{{ url_for('vote') }}" class="btn btn-primary btn-large">Vote &raquo;</a></p>
	{% endblock %}
	```

### 新增組態檔並變更 \_\_init\_\_.py

1. 在「方案總管」中，以滑鼠右鍵按一下 [教學課程] 專案，再依序按一下 [加入]、[新增項目]，接著選取 [空白 Python 檔案]，並為 **config.py** 檔案命名。Flask 中的表單需要使用此組態檔案。您也可以用它來提供秘密金鑰。但本教學課程不需要用到此金鑰。

2. 要在 config.py 中新增下列程式碼，您需要在下個步驟中變更 **DOCUMENTDB\_HOST** 和 **DOCUMENTDB\_KEY** 的值。

	```python
	CSRF_ENABLED = True
	SECRET_KEY = 'you-will-never-guess'
	
	DOCUMENTDB_HOST = 'https://YOUR_DOCUMENTDB_NAME.documents.azure.com:443/'
	DOCUMENTDB_KEY = 'YOUR_SECRET_KEY_ENDING_IN_=='
	
	DOCUMENTDB_DATABASE = 'voting database'
	DOCUMENTDB_COLLECTION = 'voting collection'
	DOCUMENTDB_DOCUMENT = 'voting document'
	```

3. 在 [Azure 入口網站](https://portal.azure.com/)中，按一下 [瀏覽]、[DocumentDB 帳戶]，按兩下要使用的帳戶名稱，再按一下 [金鑰] 瀏覽到 [金鑰] 刀鋒視窗。在 [金鑰] 刀鋒視窗中複製 **URI** 值並貼到 **config.py** 檔案中，做為 **DOCUMENTDB\_HOST** 屬性的值。
4. 回到 Azure 入口網站，在 [金鑰] 刀鋒視窗中複製**主要金鑰**或**次要金鑰**值，貼到 **config.py** 檔案中，做為 **DOCUMENTDB\_KEY** 屬性的值。
5. 在 **\_\_init\_\_.py** 檔案中，加入以下這一行。 

        app.config.from_object('config')

    因此，檔案的內容應該如下：

	```python
	from flask import Flask
	app = Flask(__name__)
	app.config.from_object('config')
	import tutorial.views
	```

6. 加入所有檔案之後，方案總管看起來應該像這樣：

	![Visual Studio [方案總管] 視窗的螢幕擷取畫面](./media/documentdb-python-application/image15.png)


## 步驟 4：在本機執行您的 Web 應用程式

1. 按下 CTRL+SHIFT+B 建置解決方案。
2. 建置成功後，按下 F5 啟動網站。您應該會在畫面上看到下列內容。

	![網頁瀏覽器中顯示的 Python + DocumentDB 投票應用程式螢幕擷取畫面](./media/documentdb-python-application/image16.png)

3. 按一下 [建立/清除投票資料庫] 來產生資料庫。

	![Web 應用程式建立頁面 - 開發詳細資訊的螢幕擷取畫面](./media/documentdb-python-application/image17.png)

4. 然後，按一下 [投票]，並選取您的選項。

	![具有投票問題的 Web 應用程式的螢幕擷取畫面](./media/documentdb-python-application/image18.png)

5. 對於您投的每張票，都會讓適當的計數器數字遞增。

	![顯示投票頁面結果的螢幕擷取畫面](./media/documentdb-python-application/image19.png)

6. 按下 Shift+F5 停止對專案進行偵錯。

## 步驟 5：將 Web 應用程式部署至 Azure 網站

您已經擁有可在DocumentDB 正常運作的完整應用程式，我們現在要將此應用程式部署至 Azure 網站。

1. 以滑鼠右鍵按一下「方案總管」中的專案 (請確定您已沒有在本機上執行該案)，然後選取 [發佈]。  

 	![[方案總管] 中選取之教學課程 (具有反白顯示的 [發佈] 選項) 的螢幕擷取畫面](./media/documentdb-python-application/image20.png)

2. 在 [發佈 Web] 視窗中，選取 [Microsoft Azure 網站]。

	![[發佈 Web] 視窗的螢幕擷取畫面](./media/documentdb-python-application/image21.png)

3. 在 [選取現有網站] 視窗中，按一下 [新增]。

	![[選取現有網站] 視窗的螢幕擷取畫面](./media/documentdb-python-application/select-existing-website.png)

4. 在 [在 Microsoft Azure 上建立網站] 視窗中，輸入 [網站名稱]，選取 [區域]，再按一下 [建立]。

	![[在 Microsoft Azure 上建立網站] 視窗的螢幕擷取畫面](./media/documentdb-python-application/create-site-on-microsoft-azure.png)

5. 在 [發佈 Web] 視窗中，按一下 [發佈]。

	![[在 Microsoft Azure 上建立網站] 視窗的螢幕擷取畫面](./media/documentdb-python-application/publish-web.png)

3. 幾秒後，Visual Studio 便會發佈 Web 應用程式並啟動瀏覽器，您可以在瀏覽器中看到您方便好用的應用程式已在 Azure 中執行！

## 疑難排解

如果這是您在電腦上執行的第一個 Python 應用程式，請確定 PATH 變數中包括下列資料夾 (或同等的安裝位置：

    C:\Python27\site-packages;C:\Python27\;C:\Python27\Scripts;

如果您在投票頁面上收到錯誤，且您的專案不是命名為**教學課程**，請確定 **\_\_init\_\_.py** 在下列程式碼行中參照了正確的專案名稱：`import tutorial.view`。

## 後續步驟

恭喜！ 您剛剛已使用 Azure DocumentDB 建置您的第一個 Python Web 應用程式，並將其發佈至 Azure 網站。

我們會根據您的意見反應，經常更新並改善此主題。當您完成教學課程後，請使用位於此頁面頂端及底部的投票按鈕來投票，並務必對您想看到的改善內容提供您的意見反應。如果想要我們直接與您連絡，歡迎在留言中留下電子郵件地址。

要在 Web 應用程式中加入其他功能，請參閱 [DocumentDB Python SDK](documentdb-sdk-python.md) 中可用的 API。

如需 Azure、Visual Studio 及 Python 的詳細資訊，請參閱 [Python 開發人員中心](/develop/python/)。

如需其他的 Python Flask 教學課程，請參閱 [Flask 教學課程庫，第一部分：Hello, World!](http://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-i-hello-world)。

  [Visual Studio Express]: http://www.visualstudio.com/products/visual-studio-express-vs.aspx
  [2]: https://www.python.org/downloads/windows/
  [3]: https://www.microsoft.com/download/details.aspx?id=44266
  [Microsoft Web Platform Installer]: http://www.microsoft.com/web/downloads/platform.aspx
  [Azure portal]: http://portal.azure.com

<!---HONumber=AcomDC_1223_2015-->