<properties  linkid="develop-python-tutorials-django-with-python-tools-for-visual-studio" urlDisplayName="Django with Python Tools for Visual Studio 2.0" pageTitle="Creating Django applications with Python Tools for Visual Studio 2.0" metaKeywords="" description="Learn how to use the Python Tools for Visual Studio to create a Django application that stores data in a SQL Database or MySQL database instance and can be deployed to a web site or cloud service." metaCanonical="" services="web-sites,cloud-services" documentationCenter="Python" title="Creating Django applications with Python Tools 2.0 for Visual Studio" authors="" solutions="" manager="" editor="" />

# 使用 Python Tools 2.0 for Visual Studio 建立 Django 應用程式

<div  class="dev-onpage-video-clear clearfix">
<div  class="dev-onpage-left-content" markdown="1">
在本教學課程中，我們將使用 Python Tools 2.0 for Visual Studio 來建立簡單的 Django
應用程式。此應用程式可讓使用者進行民調投票。我們會先使用本機 sqlite3 資料庫，接著改用 Azure 上的 SQL Server 或
MySQL 資料庫。我們將說明如何啟用 Django 管理介面，並利用它將民調加入至資料庫。我們也會使用 Visual Studio 中整合的 Django Shell。最後，我們會將應用程式部署至 Azure 網站和 Azure 雲端服務。


<p>如果想要看影片，右邊片段播放的步驟與本教學課程相同。</p>

</div>
<div class="dev-onpage-video-wrapper"><a href="http://www.youtube.com/watch?v=wkqjafvvU5w" target="_blank" class="label">觀看教學課程</a> <a style="background-image: url('/media/devcenter/python/videos/django-tutorial-180x120.png') !important;" href="http://www.youtube.com/watch?v=wkqjafvvU5w" target="_blank" class="dev-onpage-video"><span class="icon">播放影片</span></a> <span class="time">33:08</span></div>
</div>


本教學課程以 Python Tools for Visual Studio 和 Azure 為重點。如需 Django
和本教學課程中建立的民調應用程式的詳細資訊，請參閱 [https://www.djangoproject.com/][2]。

[WACOM.INCLUDE[create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

## 需求

若要完成本教學課程，您需要

* [Python Tools 2.0 for Visual Studio][3]
* [Python 2.7 (32 位元)][4]
* Visual Studio 和 Azure SDK：
  * VS 2010 Pro 或以上版本，搭配 Azure SDK 2.1
  * VS 2012 Pro 或以上版本，搭配 Azure SDK 2.1、2.2 或以上版本
  * VS 2013 Pro 或以上版本，搭配 Azure SDK 2.2 或以上版本
  * 免費的 VS 2013 Integrated Shell。**Azure SDK 不支援 Integrated
    Shell**。您可以在本機使用免費的 Integrated Shell 來開發、偵錯和執行 Django 應用程式，但無法利用
    Visual Studio 來發佈至 Azure 網站或雲端服務。

請使用 Web Platform Installer 來安裝 Azure SDK。這將會安裝 SDK、模擬器和 Azure Tools for
Visual Studio。在 Web Platform Installer 中，請搜尋 **Azure SDK for
.NET**，然後選取您的 Visual Studio 版本支援的其中一個 SDK 版本。

**注意：**為了順利使用 Azure 網站或雲端服務來執行此應用程式，您需要使用 [python.org][4] 的正式 CPython
2.7 散發套件。可使用其他散發套件，但沒有正式支援。

## 下載現有的專案

如果想要跳過教學課程中的步驟，您可以[下載此專案的原始程式碼][5]。

其中已使用下列超級使用者認證建立 sqlite3 資料庫：

`Username:tutorial 
 Password:azure`

下載檔「不含」虛擬環境。請依照[建立虛擬環境](#creating-a-virtual-environment)一節的步驟建立虛擬環境。完成之後，就可依照[偵錯](#debugging)一節來繼續處理專案。

## 建立專案

Python Tools for Visual Studio 支援 Python 虛擬環境。我們將建立 Django
專案並利用虛擬環境來安裝相依性。建議以此來設定可發佈至 Azure 網站或雲端服務的專案。

1.  開啟 Visual Studio、按一下 [檔案/新增專案]、選擇 [Django 應程式]，並命名為 **tutorial**。
    
    ![New
    Project](./media/cloud-services-python-create-deploy-django-app/django-tutorial-001-new-project.png)

**注意：**在方案總管的 [參考] 下，將出現 Django 1.4 的節點。這是供 Azure 雲端服務部署將 Python 和
Django 安裝到目標電腦。請勿從參考附註中刪除 Django 1.4 的參考。因為我們使用虛擬環境並在其中安裝我們自己的 Django
封裝，所以將會使用虛擬環境中安裝的 Django 封裝。

## <a name="creating-a-virtual-environment"></a>建立虛擬環境

我們將使用虛擬環境來安裝相依性。這對任何 Python 應用程式而言都是很好的做法，也是發佈至 Azure 時的必要做法。

1.  建立新的虛擬環境。在方案總管中，以滑鼠右鍵按一下 **Python 環境**，然後選取 **加入虛擬環境**。
    
    ![Add Virtual
    Environment](./media/cloud-services-python-create-deploy-django-app/django-tutorial-002-add-virtual-env.png)

2.  選取 Python 2.7 做為基本的 Python 解譯器，並接受預設名稱 **env**。PTVS 會安裝及/或
    virtualenv (若尚未安裝)。

3.  以滑鼠右鍵按一下 **env** 和 **安裝 Python 封裝**：**django**
    
    ![Install
    Django](./media/cloud-services-python-create-deploy-django-app/django-tutorial-003-install-django.png)

4.  Django有許多檔案，需要花一些時間來安裝。您可以在輸出視窗中查看進度。
    
    ![Install Django
    Output](./media/cloud-services-python-create-deploy-django-app/django-tutorial-004-install-django-output.png)
    
    **注意：**在罕見情況下，輸出視窗中可能出現失敗。如果發生此情形，請檢查錯誤是否與清除有關。有時是清理失敗，但安裝卻成功
    (在輸出視窗中向上捲動來驗證這一點)。這是因為 PTVS 鎖定新建立的暫存檔案/資料夾，導致 pip 清除步驟無法刪除它們。

5.  以滑鼠右鍵按一下 **env** 和 **安裝 Python 封裝**：**pytz** (選擇性，但建議使用，供 django
    用來支援時區)

## 驗證虛擬環境

1.  讓我們來確定一切都安裝正確。請按 **F5** 或 **CTRL+F5** 啟動網站。這將會啟動 django
    開發伺服器和您的網頁瀏覽器。應該會出現下列網頁：
    
    ![Django Web
    Browser](./media/cloud-services-python-create-deploy-django-app/django-tutorial-004b-itworked.png)

## 建立民調應用程式

本節中，我們將加入應用程式來處理民調投票。

一個 Django 專案可能有多個應用程式。在本教學課程中，專案名稱是 'tutorial'，且對應於 Visual Studio
專案。我們加入的應用程式名稱是 **polls**，也是專案節點下的一個資料夾。

1.  選取 **專案節點**、**加入**->**Django 應程式**，並命名為
    **polls**。這將會建立應用程式的資料夾，以及常用應用程式檔案的未定案程式碼。
    
    ![Add Django
    App](./media/cloud-services-python-create-deploy-django-app/django-tutorial-005-add-django-app.png)

2.  在 **tutorial/settings.py** 中，將下列程式碼加入至 **INSTALLED_APPS**：
    
         'polls'，

3.  從 **INSTALLED_APPS** 中取消註解下列程式碼：
    
         'django.contrib.admin'，

4.  以下列程式碼取代 **tutorial/urls.py**：
    
        from django.conf.urls import patterns, include, url
        
        from django.contrib import admin
        admin.autodiscover()
        
        urlpatterns = patterns('',
            url(r'^', include('polls.urls', namespace="polls")),
            url(r'^admin/', include(admin.site.urls)),
         )

5.  以下列程式碼取代 **polls/models.py**：
    
        from django.db import models
        	
        class Poll(models.Model):
            question = models.CharField(max_length=200)
            pub_date = models.DateTimeField('date published')
        		
        def __unicode__(self):
            return self.question
        	
        class Choice(models.Model):
            poll = models.ForeignKey(Poll)
            choice_text = models.CharField(max_length=200)
            votes = models.IntegerField(default=0)
        		
        def __unicode__(self):
            return self.choice_text

6.  以下列程式碼取代 **polls/views.py**：
    
        from django.shortcuts import get_object_or_404, render
        from django.http import HttpResponseRedirect
        from django.core.urlresolvers import reverse
        from polls.models import Choice, Poll
        	
        def vote(request, poll_id):
            p = get_object_or_404(Poll, pk=poll_id)
            try:
                selected_choice = p.choice_set.get(pk=request.POST['choice'])
            except (KeyError, Choice.DoesNotExist):
            # Redisplay the poll voting form.
            return render(request, 'polls/detail.html', {
               'poll':p,
               'error_message':"You didn't select a choice.",
            })
        else:
             selected_choice.votes += 1
             selected_choice.save()
             # Always return an HttpResponseRedirect after successfully dealing
             # with POST data.This prevents data from being posted twice if a
             # user hits the Back button.
             return HttpResponseRedirect(reverse('polls:results', args=(p.id,)))

7.  加入新的 Python 檔案 **polls/urls.py** 並填入程式碼：
    
        from django.conf.urls import patterns, url
        from django.views.generic import DetailView, ListView
        from polls.models import Poll
        
        urlpatterns = patterns('',
           url(r'^$',
            ListView.as_view(
        		queryset=Poll.objects.order_by('-pub_date')[:5],
        		context_object_name='latest_poll_list',
        		template_name='polls/index.html'),
        	name='index'),
        url(r'^(?P<pk>\d+)/$',
        	DetailView.as_view(
        		model=Poll,
        		template_name='polls/detail.html'),
        	name='detail'),
        url(r'^(?P<pk>\d+)/results/$',
        	DetailView.as_view(
        		model=Poll,
        		template_name='polls/results.html'),
        	name='results'),
        url(r'^(?P<poll data-morhtml="true"_id>\d+)/vote/$', 'polls.views.vote', name='vote'),
         )

8.  建立新的項目 **polls/admin.py** 並填入下列程式碼：
    
        from django.contrib import admin
        from polls.models import Choice, Poll
        	
        class ChoiceInline(admin.TabularInline):
        	model = Choice
        	extra = 3
        	
        class PollAdmin(admin.ModelAdmin):
        	fieldsets = [
        		(None,               {'fields':['question']}),
        		('Date information', {'fields':['pub_date'], 'classes':['collapse']}),
             ]
        	inlines = [ChoiceInline]
        	list_display = ('question', 'pub_date')
        	list_filter = ['pub_date']
        	search_fields = ['question']
        	date_hierarchy = 'pub_date'
        	
        admin.site.register(Poll, PollAdmin)

9.  在 **polls/templates** 資料夾下，建立新的資料夾並命名為 **polls**。

10. 透過拖放或剪貼作業，將檔案 **polls/templates/index.html** 移至
    **polls/templates/polls** 資料夾。

11. 以下列標記取代 **polls/templates/polls/index.html**：
    
        <html>
        <head></head>
        <body>
        {% if latest_poll_list %}
        	<ul>
        	{% for poll in latest_poll_list %}
        		<li><a href="{% url 'polls:detail' poll.id %}">{{ poll.question }}</a></li>
        	{% endfor %}
        	</ul>
        {% else %}
        	<p>No polls are available.</p>
        {% endif %}
        </body>
        </html>

12. 建立新的 Django HTML 範本 **polls/templates/polls/detail.html** 並填入下列標記：
    
         <html>
        <head></head>
        <body>
        <h1>{{ poll.question }}</h1>
        {% if error_message %}<p><strong>{{ error_message }}</strong></p>{% endif %}
        <form  action="{% url 'polls:vote' poll.id %}" method="post">
        {% csrf_token %}
        {% for choice in poll.choice_set.all %}
        	<input  type="radio" name="choice" id="choice{{ forloop.counter }}" value="{{ choice.id }}" />
        	<label  for="choice{{ forloop.counter }}">{{ choice.choice_text }}</label><br  />
        {% endfor %}
        <input  type="submit" value="Vote" />
        </form>
        </body>
        </html>

13. 建立新的 Django HTML 範本 **polls/templates/polls/results.html** 並填入下列標記：
    
         <html>
        <head></head>
        <body>
        <h1>{{ poll.question }}</h1>
        <ul>
        {% for choice in poll.choice_set.all %}
        	<li>{{ choice.choice_text }} -- {{ choice.votes }} vote{{ choice.votes|pluralize }}</li>
        {% endfor %}
        </ul>
        <a href="{% url 'polls:detail' poll.id %}">Vote again?</a>
        </body>
        </html>

14. 專案現在應該有下列檔案：
    
    ![方案總管](./media/cloud-services-python-create-deploy-django-app/django-tutorial-006-solution-explorer.png)

## 在本機建立 sqlite3 資料庫

我們的 Web 應用程式差不多可以使用，但需要先設定資料庫。為了在本機測試網站，我們將建立 sqlite3
資料庫。這是非常輕量型的資料庫，完全不需要另外安裝。資料庫檔案會建立在專案資料夾下。

1.  在 **tutorial/settings.py** 中，將下列 import 加入至檔案頂端：
    
         from os import path

2.  在靠近檔案的頂端，將下列定義加入至 import 後面：
    
         PROJECT_ROOT = path.dirname(path.abspath(path.dirname(__file__)))

3.  將 DATABASES 區段改成下列程式碼：
    
        DATABASES = {
        	'default': {
        		'ENGINE':'django.db.backends.sqlite3',
        		'NAME':path.join(PROJECT_ROOT, 'db.sqlite3'),
        		'USER': '',
        		'PASSWORD': '',
        		'HOST': '',
        		'PORT': '',
             }
         }

4.  以滑鼠右鍵按一下專案節點，然後選取 **Django**->**Django Sync DB**。將出現 Django
    管理互動式視窗。因為資料庫尚不存在，將會提示您建立管理員認證。請輸入使用者名稱和密碼。電子郵件是選擇性的。
    
    ![Django Sync
    DB](./media/cloud-services-python-create-deploy-django-app/django-tutorial-007-sqlite3.png)

5.  按 F5 或 CTRL-F5 啟動網站。這將會啟動 django 開發伺服器和您的網頁瀏覽器。網站的根 url
    會顯示民調的索引，但資料庫中尚無民調。
    
    ![Web
    Browser](./media/cloud-services-python-create-deploy-django-app/django-tutorial-008-dev-server.png)

6.  瀏覽至
    **http://localhost:{port}/admin**。您可以從開發伺服器主控台視窗中取得連接埠號碼。以您在先前步驟中建立的認證登入。
    
    ![Add
    Poll](./media/cloud-services-python-create-deploy-django-app/django-tutorial-009-admin-login.png)

7.  使用管理介面加入幾個民調。請勿浪費太多時間將民調加入至本機資料庫。稍後我們會改用雲端資料庫，到時會重新填入資料庫。
    
    ![Poll
    Index](./media/cloud-services-python-create-deploy-django-app/django-tutorial-009-admin-add-poll.png)

8.  瀏覽至 **http://localhost:{port}/**。您會看到已加入的民調的索引。
    
    ![](./media/cloud-services-python-create-deploy-django-app/django-tutorial-010-index.png)

9.  按一下其中一個民調前往投票頁面。
    
    ![Poll
    Detail](./media/cloud-services-python-create-deploy-django-app/django-tutorial-011-detail.png)

10. 投下選票，將會重新導向至結果頁面，您會看到票數增加。
    
    ![Poll
    Results](./media/cloud-services-python-create-deploy-django-app/django-tutorial-012-results.png)

## 使用樣式表和其他靜態檔案

本節中，我們將使用樣式表來更新網站外觀。樣式表之類的靜態檔案有不同的處理方式，必須儲存在正確位置。

1.  在 **tutorial/settings.py**，找出指派的 **STATIC_ROOT** 並變更為：
    
         STATIC_ROOT = path.join(PROJECT_ROOT, 'static').replace('\\','/')

2.  在 **polls** 資料夾下，建立的資料夾並命名為 **static**。

3.  在 **polls/static** 資料夾下，建立新的資料夾並命名為 **polls**。

4.  在 **polls/static/polls** 資料夾下，建立新的資料夾並命名為 **images**。

5.  加入新的樣式表檔案 **polls/static/polls/style.css** 並填入下列程式碼：
    
        body {
        	color:darkblue;
        	background:white url("images/background.jpg");
         }

6.  將現有影像加入至 **polls/static/polls/images** 資料夾，並命名為 **background.jpg**。

7.  專案現在應該有下列檔案：
    
    ![方案總管](./media/cloud-services-python-create-deploy-django-app/django-tutorial-013-solution-explorer.png)

8.  使用下列標記編輯所有範本的標頭來參考樣式表：
    
        <head>
        {% load staticfiles %}
        <link  rel="stylesheet" type="text/css" href="{% static 'polls/style.css' %}" />
        </head>

9.  重新執行網站。索引、民調和結果頁面會使用我們建立的樣式表，變成深藍色文字並加上背景影像。
    
    ![Poll
    Detail](./media/cloud-services-python-create-deploy-django-app/django-tutorial-014-detail-styled.png)

## <a name="debugging"></a>偵錯

Python Tools for Visual Studio 提供特殊支援來偵錯 Django 範本。

1.  開啟 **polls/templates/polls/index.html**，在這一行按 **F9** 來設定中斷點：
    
         {% if latest_poll_list %}

2.  按 **F5** 開始偵錯。Visual Studio 會中斷並進入範本檔案。

3.  從 **偵錯->視窗->區域變數** 中開啟 **區域變數視窗**，您會看見
    **latest_poll_list** 變數和值。

4.  您可以按 **F10** 逐步執行，如同在一般 Python 程式碼中一樣。在 for 迴圈內，您可以檢查 **poll** 的值：
    
    ![Debugging](./media/cloud-services-python-create-deploy-django-app/django-tutorial-015-debugging.png)

## 在 Azure 上建立資料庫

我們現在已確認民調應用程式可在本機上運作，讓我們改用 Azure 上代管的資料庫。

接下來兩節，我們說明如何使用 SQL 資料庫和 MySQL 資料庫。兩者都是代管服務。

另一種選擇是建立虛擬機器並安裝資料庫伺服器。關於在 Azure Linux VM 上設定 MySQL 的指示，請參閱[這裡][6]。

**注意：**在 Azure 上可使用 sqlite3 資料庫 (僅做為開發用途，不建議在實際執行環境中使用)。您需要將
**db.sqlite3** 檔案加入至專案，以便隨著 django 應用程式一起部署資料庫。

### SQL Database

在本節，我們將在 Azure 上建立 SQL 資料庫，將必要的封裝加入至虛擬環境，以及變更設定來使用新的資料庫。

1.  在 WindowsAzure 管理入口網站中，選取 **SQL 資料庫**。

2.  先建立伺服器來代管資料庫。選取 **伺服器** 和 **加入**。

3.  在新建立的伺服器的 **設定** 索引標籤中，將出現您目前的用戶端 IP 位址。在該索引標籤旁邊，按一下 **加入至允許的 IP 位址**。
    
    **注意：** Azure 有時無法正確偵測到用戶端 IP 位址。如果同步處理資料庫時發生錯誤，請剪貼錯誤訊息中的 IP
    位址並加入至允許的 IP 位址。

4.  接下來要建立資料庫。在 **資料庫** 索引標籤中，按一下下方列的 **加入**。

5.  在 Visual Studio 中，我們會將從 Django 存取 SQL Server 資料庫所需的封裝安裝至虛擬環境。

6.  以滑鼠右鍵按一下 **env** 和 **安裝 Python 封裝**：**pyodbc** 使用
    **easy_install**。

7.  以滑鼠右鍵按一下 **env** 和 **安裝 Python 封裝**：**django-pyodbc-azure** 使用
    **pip**。

8.  編輯 **tutorial/settings.py** 並將 **DATABASES** 定義變更如下，將
    **NAME**、**USER**、**PASSWORD** 和 **HOST** 換成 ClearDB 控制台列出的值：
    
        DATABASES = {
        	'default': {
        		'ENGINE':'sql_server.pyodbc',
        		'NAME':'<database  name>',
        		'USER':'<user  name>@<server  name>',
        		'PASSWORD':'<user  password>',
        		'HOST':'<server  name>.database.windows.net',
        		'PORT': '',
        'OPTIONS': {
        'driver':'SQL Server Native Client 11.0',
        'MARS_Connection':True,
                 },
             }
         }
    
    必須確定使用您已安裝在電腦上的驅動程式。從開始功能表/畫面開啟 **系統管理工具**，選取 **ODBC 資料來源 (32 位元)**。驅動程式會列在 **驅動程式** 索引標籤下。
    
    在 Azure 網站上執行時，**SQL Server Native Client 10.0** 和 **SQL Server
    Native Client 11.0** 都能運作。
    
    在 Azure 雲端服務上執行時，只有 **SQL Server Native Client 11.0** 可運作。

9.  同步處理資料庫並建立管理員認證，就像先前設定本機 sqlite3 資料庫一樣。

### MySQL 資料庫

在本節，我們將在 Azure 上建立 MySQL 資料庫，將必要的封裝加入至虛擬環境，以及變更設定來使用新的資料庫。

在 Azure Store 中，您可以將各種服務加入至帳戶，包括 MySQL
資料庫。我們可以建立免費的試用帳戶，還有固定大小的免費小型資料庫可用。

1.  在 Azure 管理入口網站中，選取
    **新增**->**存放區**->**應用程式服務**->**ClearDB MySQL 資料庫**。使用免費方案建立資料庫。

2.  接下來，我們會將從 Django 存取 MySQL 資料庫所需的封裝安裝至虛擬環境。以滑鼠右鍵按一下 **env** 和 **安裝 Python 封裝**：**mysql-python** 使用 **easy_install**。

3.  編輯 **tutorial/settings.py** 並將 **DATABASES** 定義變更如下，將
    **NAME**、**USER**、**PASSWORD** 和 **HOST** 換成 ClearDB 控制台列出的值：
    
        DATABASES = {
        	'default': {
        		'ENGINE':'django.db.backends.mysql',
        		'NAME':'<database  name>',
        		'USER':'<user  name>',
        		'PASSWORD':'<user  password>',
        		'HOST':'<host  url>',
        		'PORT': '',
             }
         }

4.  同步處理資料庫並建立管理員認證，就像先前設定本機 sqlite3 資料庫一樣。

## 使用 Django Shell

1.  以滑鼠右鍵按一下專案節點，然後選取 **Django**->**開啟 Django Shell**。

2.  在此互動式視窗中，您可以使用我們的模型來存取資料庫。請輸入下列程式碼將民調加入至資料庫：
    
        from polls.models import Poll, Choice
        from django.utils import timezone
        
        p = Poll(question="Favorite way to host Django on Azure?", pub_date=timezone.now())
        p.save()
        
        p.choice_set.create(choice_text='Web Site', votes=0)
        p.choice_set.create(choice_text='Cloud Service', votes=0)
        p.choice_set.create(choice_text='Virtual Machine', votes=0)
    
    ![Django Shell Add
    Poll](./media/cloud-services-python-create-deploy-django-app/django-tutorial-018-shell-add-poll.png)

3.  模型的靜態分析中只能看到完整 API 的一部分。在互動式視窗中，您可取得即時物件的 IntelliSense，這是探索 API
    的好方法。以下是可在互動式視窗中嘗試的一些動作：

        # all poll objects
        Poll.objects.all()

        # primary key for poll
        p.id

        # all choice objects for the poll
        p.choice_set.all()

        # get object by primary key
        Poll.objects.get(pk=1)
    
    ![Django Shell Query
    Poll](./media/cloud-services-python-create-deploy-django-app/django-tutorial-019-shell-query.png)

4.  啟動網站。您可以看到我們使用 Django Shell 加入的民調。

## 發佈至 Azure

現在資料庫已經在 Azure 上，接下來是將網站本身交由 Azure 代管。

Azure 有一些選項可代管 Django 應用程式：

* [網站][7]
* [服務雲端][8]
* [虛擬機器][9]

Python Tools for Visual Studio 支援發佈至 Azure
網站和雲端服務。接下來兩節提供這些說明，您可以選擇閱讀其中一節，或兩節都看。

在這兩種情況下，PTVS 都會替您設定 IIS，也會為您產生 web.config 檔案 (若專案中無此檔案)。只要您在 settings.py
中設定 STATIC_ROOT，就會自動收集靜態檔案 (manage.py collectstatic)。

在虛擬機器中代管 Django 已超出本教學課程的範圍。其中涉及依照您的作業系統 (Windows 或 Linux) 建立 VM、安裝
Python 和手動部署 Django 應用程式。

### Azure 網站

1.  首先需要建立網站。請使用 Azure 管理入口網站，按一下
    **新增**->**計算**->**網站**->**快速建立**。挑選任何可用的名稱。

2.  建立之後，下載網站的發行設定檔。
    
    ![Web Site Download
    Profile](./media/cloud-services-python-create-deploy-django-app/django-tutorial-020-website-download-profile.png)

3.  在 Visual Studio 中，以滑鼠右鍵按一下專案節點，然後選取 **發行**。
    
    ![Web Site
    Publish](./media/cloud-services-python-create-deploy-django-app/django-tutorial-020-website-publish.png)

4.  匯入先前下載的網站發行設定檔。

5.  接受預設值，按一下 **發行** 開始發行。

6.  發行完成時，網頁瀏覽器會開啟到已發佈的網站。
    
    ![Web Site
    Browser](./media/cloud-services-python-create-deploy-django-app/django-tutorial-020-website.png)

### Azure 雲端服務

1.  以滑鼠右鍵按一下專案節點，然後選取 **加入 Azure 雲端服務專案** 或 [轉換 -> 轉換成 Azure
    雲端服務專案]**** (出現何者視您的 Visual Studio 版本而定)。這會將新專案加入至方案中，字尾是
    .Azure。此新專案會標示為方案的啟動專案。
    
    **注意：**如果專案內容功能中沒有出現用來建立 Azure 雲端服務專案的命令，則您需要安裝 Azure Tools for
    Visual Studio。這些工具隨 Azure SDK for .NET 一起安裝。請參閱本教學課程開頭的需求一節。
    
    ![方案總管](./media/cloud-services-python-create-deploy-django-app/django-tutorial-021-cloudservice-solution-explorer.png)

#### 在 Azure 模擬器中執行

1.  您需要**以系統管理員身分重新啟動 Visual Studio**，才能在計算模擬器中執行。

2.  按 **F5** 開始偵錯，應用程式將在計算模擬器中執行和部署。確認管理介面可運作，且您可以進行民調投票。
    
    ![Compute
    Emulator](./media/cloud-services-python-create-deploy-django-app/django-tutorial-021-cloudservice-emulator.png)

3.  如果不想繼續以系統管理員身分執行，您現在可以重新啟動 Visual Studio。

#### 發佈至 Azure 雲端服務

1.  接下來要將雲端服務發佈至 Azure。請以滑鼠右鍵按一下 **tutorial.Azure** 雲端服務專案，然後選取
    **發行**。
    
    **注意：**必須在雲端服務專案上選取 **發行**。這樣會開啟 **發行 Azure 應用程式** 對話方塊，準備發行至
    Azure 雲端服務。如果您在 Django 專案上選取 **發行**，則會開啟用來發行至 Azure 網站的 [發行
    Web]**** 對話方塊。
    
    ![Cloud Service
    Publish](./media/cloud-services-python-create-deploy-django-app/django-tutorial-021-cloudservice-publish.png)

2.  您需要匯入 Azure 訂閱檔案。請按一下[登入以下載認證][10]連結，從 Azure 入口網站下載檔案。

3.  在 **設定] 頁面中，選取 [雲端服務] 下拉式方塊中的 [建立新的**，建立新的雲端服務。您可以使用任何可用的名稱。
    
    ![Cloud Service
    Settings](./media/cloud-services-python-create-deploy-django-app/django-tutorial-021-cloudservice-settings.png)

4.  接受預設值並按一下 **發行**。因為需要佈建雲端服務的 VM，需要的時間比發佈至網站更久。您可以在 [Azure 活動記錄檔]
    視窗中檢視進度：
    
    ![Cloud Service
    Publish](./media/cloud-services-python-create-deploy-django-app/django-tutorial-021-cloudservice-publish-progress.png)

5.  作業完成時，按一下 [Azure 活動記錄檔] 視窗中的網站 URL，以開啟網頁瀏覽器。
    
    ![Cloud Service
    Browser](./media/cloud-services-python-create-deploy-django-app/django-tutorial-021-cloudservice.png)

## 結論

在本教學課程中，我們使用 [Python Tools for Visual Studio][3] 開發 Django 應用程式。我們共使用 3
個不同的資料庫：sqlite3、SQL Server 和 MySQL 資料庫。最後，我們將應用程式發佈至 Azure 網站和雲端服務。



[1]: http://www.youtube.com/watch?v=wkqjafvvU5w
[2]: https://www.djangoproject.com/
[3]: http://pytools.codeplex.com
[4]: http://www.python.org/download/
[5]: http://download-codeplex.sec.s-msft.com/Download?ProjectName=pytools&DownloadId=783376
[6]: http://www.windowsazure.com/en-us/manage/linux/common-tasks/mysql-on-a-linux-vm/
[7]: http://www.windowsazure.com/en-us/services/web-sites/
[8]: http://www.windowsazure.com/en-us/services/cloud-services/
[9]: http://www.windowsazure.com/en-us/services/virtual-machines/
[10]: https://manage.windowsazure.com/publishsettings/index?client=vs&schemaversion=2.0