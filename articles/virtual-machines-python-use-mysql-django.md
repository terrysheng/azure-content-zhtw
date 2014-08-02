<properties linkid="develop-python-web-app-with-django-and-mysql" urlDisplayName="Web with Django + MySQL" pageTitle="Python web app with Django and MySQL - Azure tutorial" metaKeywords="Azure django web app, Azure Django MySQL, Azure django Python" description="A tutorial that teaches you how to use MySQL in with Django on an Azure virtual machine. Code samples written in Python." metaCanonical="" services="virtual-machines" documentationCenter="Python" title="Django Hello World - MySQL Windows Edition" authors="" solutions="" manager="" editor="" />

Django Hello World - MySQL Windows 版本
=======================================

本教學課程說明如何在單一 Azure 虛擬機器上搭配 Django 使用 MySQL。本指南假設您過去有使用 Azure 及 Django 的一些經驗。關於 Azure 及 Django 的簡介，請參閱[現代化商務的雲端](http://windowsazure.com/en-us/documentation/articles/virtual-machines-python-django-web-app-windows-server)。本指南也假設您擁有一些關於 MySQL 的知識。如需 MySQL 的概觀，請參閱 [MySQL 網站](http://dev.mysql.com/doc/) (英文)。

在本教學課程中，您將了解如何：

-   設定 Azure 虛擬機器代管 MySQL 及 Django。雖然本教學課程說明如何在 Windows Server 2008 R2 下完成這個程序，不過相同的程序也可以對於 Azure 中代管的 Linux VM 完成。
-   安裝 Python 版 [MySQL 驅動程式](http://pypi.python.org/pypi/MySQL-python/1.2.3)。
-   設定現有 Django 應用程式使用 MySQL 資料庫。
-   直接從 Python 使用 MySQL。
-   代管並執行 MySQL Django 應用程式。

您將使用 Azure VM 中代管的 MySQL 找到 *World* 的替代，擴展 [Django Hello World](http://windowsazure.com/en-us/documentation/articles/virtual-machines-python-django-web-app-windows-server) 範例。接著透過 MySQL 支援的 Django *counter* 應用程式決定替代。與 Hello World 範例一樣，Azure 虛擬機器將再次代管這個 Django 應用程式。

這個教學課程的專案檔案將儲存於 **C:\\django\\helloworld** 中，完成的應用程式將如下所示：

![](./media/virtual-machines-python-use-mysql-django/mysql_tutorial01.png)

[WACOM.INCLUDE [create-account-and-vms-note](../includes/create-account-and-vms-note.md)]

設定虛擬機器代管 MySQL 及 Django。
----------------------------------

1.  按照[此處](/en-us/manage/windows/tutorials/virtual-machine-from-gallery/)提供的指示建立 *Windows Server 2008 R2* 散發套件的 Azure 虛擬機器。

2.  開啟虛擬機器上用於 MySQL 交易的 TCP 連接埠：
    -   在 Azure 入口網站中瀏覽至新建立的虛擬機器，並按一下 [端點]** 索引標籤。
    -   按一下畫面底部的 [新增端點]** 按鈕。
    -   新增 NAME = **mysql**、PROTOCOL = **TCP**、PUBLIC PORT = **3306**、PRIVATE PORT = **3306** 的端點。

3.  新增 NAME = **web**、PROTOCOL = **TCP**、PUBLIC PORT = **80**、PRIVATE PORT = **80** 的另一個端點。這會將外部網際網路要求重新導向至 Django 執行的連接埠 *80*。

4.  使用 Windows *Remote Desktop* 從遠端登入新建立的 Azure 虛擬機器。

5.  開啟虛擬機器上的 TCP 連接埠 **80**：
    -   在 **[開始]** 功能表中，選取 [管理員工具] **** 以及 **[具備進階安全性的 Windows 防火牆]**。
    -   在左側窗格中，選取 **[內送規則]**。在右側的 **[動作]** 窗格中，選取 **[新增規則...]**。
    -   在 **[新內送規則精靈]** 中，選取 **[連接埠]** 並按一下 **[下一步]**。
    -   選取 **[TCP]** 以及 **[指定本機連接埠]**。指定「80」(Django 接聽的連接埠) 並按 **[下一步]**。
    -   選取 **[允許連線]** 並按一下 **[下一步]**。
    -   再按一下 **[下一步]**。
    -   指定規則名稱，例如「DjangoPort」，並按一下 [結束]。

6.  在虛擬機器上安裝最新版 [MySQL Community Server](http://dev.mysql.com/downloads/mysql/) for Windows：

    **注意**：建議在作業系統以外的不同資料磁碟分割安裝資料庫。

    -   在[這裡](http://dev.mysql.com/downloads/mysql/)執行 *Windows (x86、64 位元) MSI 安裝程式*連結，並且從最鄰近的下載鏡像下載適當的 MSI 安裝程式。實用提示如下：
        -   選取*完成* 設定類型。
        -   選取組態精靈中的 [Detailed Configuration]**。
        -   **務必啟用連接埠號碼 3306 的 TCP/IP 網路，並新增連接埠的防火牆例外。**
        -   設定跟密碼，並且啟用遠端機器的根存取。
    -   安裝範例[「world」資料庫](http://dev.mysql.com/doc/index-other.html) (MyISAM 版本)：
        -   在 Azure 虛擬機器上下載[這個](http://downloads.mysql.com/docs/world.sql.zip) zip 檔。
        -   **將它解壓縮至 *C:\\Users\\Administrator\\Desktop\\world.sql*。**

1.  安裝 MySQL 後，按一下 Windows [開始]** 功能表，並執行新安裝的 *MySQL 5.5 命令列用戶端*。發出以下命令：

		CREATE DATABASE world;
		USE world;
		SOURCE C:\Users\Administrator\Desktop\world.sql
		CREATE USER 'testazureuser'@'%' IDENTIFIED BY 'testazure';
		CREATE DATABASE djangoazure;
		GRANT ALL ON djangoazure.* TO 'testazureuser'@'%';
		GRANT ALL ON world.* TO 'testazureuser'@'%';
		SELECT name from country LIMIT 1;


    您此時應該會看到如下所示的回應：

    ![](./media/virtual-machines-python-use-mysql-django/mysql_tutorial01-2.png)

1.  您開始開發 Django 應用程式前，當然需要在虛擬機器上安裝 Python+Django。您可以使用 [Web Platform Installer](http://www.microsoft.com/web/downloads/platform.aspx) 這麼做。安裝 Web PI 之後，用它來搜尋「Django」並安裝 Django (Python) 產品。

    **注意：**您只需要從 WebPI 安裝 *Django* 產品，即可進行本教學課程。您**不**需要安裝 *Python Tools for Visual Studio* 或甚至 Azure Python SDK，也可以進行。

1.  安裝 MySQL Python 用戶端套件。您可以直接[從此連結](http://code.google.com/p/soemin/downloads/detail?name=MySQL-python-1.2.3.win32-py2.7.exe&can=2&q=)安裝它。一旦完成，即可執行下列命令確認您的安裝：

    ![](./media/virtual-machines-python-use-mysql-django/mysql_tutorial01-1.png)

擴展 Django Hello World 應用程式
--------------------------------

1.  按照 [Django Hello World](http://windowsazure.com/en-us/documentation/articles/virtual-machines-python-django-web-app-windows-server) 教學課程中提供的指示，在 Django 中建立一個不起眼的「Hello World」Web 應用程式。

2.  在您慣用的文字編輯器中開啟 **C:\\django\\helloworld\\helloworld\\settings.py**。修改 **DATABASES** 全域字典讀取：

         DATABASES = {
        'default': {
        'ENGINE':'django.db.backends.mysql',
        'NAME':'djangoazure',               
        'USER':'testazureuser',  
        'PASSWORD':'testazure',
        'HOST': '127.0.0.1', 
        'PORT': '3306',
                 },
        'world': {
        'ENGINE':'django.db.backends.mysql',
        'NAME':'world',               
        'USER':'testazureuser',  
        'PASSWORD':'testazure',
        'HOST': '127.0.0.1', 
        'PORT': '3306',
                 }
             }

   如您所見，剛才提供從哪裡找出我們 MySQL 資料庫的 Django 指示。

   **注意：**您**必須** 將 *HOST* 索引鍵變更為 Azure (MySQL) VM 的**永久** IP 位址。此時，應該將*HOST* 設定為 *ipconfig* Windows 命令回報的任何內容。
   
   將 *HOST* 修改為 MySQL VM 的 IP 位址後，請儲存此檔案並予以關閉。

1.  由於我們已經參照 *djangoazure* 資料庫，因此讓我們進行一些有用的處理動作！為了這個目的，我們將對於一個不起眼的 *counter* 應用程式建立模型。若要指示 Django 建立模型，請執行下列命令：

        cd C:\django\helloworld
        C:\Python27\python.exe manage.py startapp counter

   如果 Django 並未回報上述最後命令的輸出，表示成功執行。

1.  將下列文字附加到 **C:\\django\\helloworld\\counter\\models.py** 之後：

        class Counter(models.Model):
           count = models.IntegerField()
           def __unicode__(self):
              return u'%s' % (self.count)

   我們在這裡完成的所有動作將被定義為 Django 的 *Model* 類別之中的子類別，名稱為 *Counter*，其中只有一個整數欄位 *count*。這個不起眼的 counter 模型將記錄對於 Django 應用程式的點擊次數。

1.  接著，我們設定 Django 注意 *Counter* 的存在：

    -   再次編輯 **C:\\django\\helloworld\\helloworld\\settings.py**。將 *'counter'* 新增至 *INSTALLED\_APPS* Tuple。
    -   從命令提示字元下，執行：

                cd C:\django\helloworld
                C:\Python27\python manage.py sql counter
                C:\Python27\python manage.py syncdb

          這些命令會將 *Counter* 模型儲存於存留的 Django 資料庫中，並顯現如下所示的輸出：

        		C:\django\helloworld> C:\Python27\python manage.py sql counter
        		BEGIN;
        		CREATE TABLE `counter_counter` (

    			`id` integer AUTO\_INCREMENT NOT NULL PRIMARY KEY,

                `count` integer NOT NULL
      		    )
      	        ;
        		COMMIT;
            
        		C:\django\helloworld> C:\Python27\python manage.py syncdb
        		Creating tables ...
     		    Creating table auth_permission
         		Creating table auth_group_permissions
                Creating table auth_group
       	 	    Creating table auth_user_user_permissions
        	    Creating table auth_user_groups
     	        Creating table auth_user
    	        Creating table django_content_type
      	        Creating table django_session
      	        Creating table django_site
       	        Creating table counter_counter
            
      		    You just installed Django's auth system, which means you don't have any superusers defined.
     		    Would you like to create one now
                (yes/no):no
      		    Installing custom SQL ...
     		    Installing indexes ...
   		        Installed 0 object(s) from 0 fixture(s)

6 .  取代 **C:\\django\\helloworld\\helloworld\\views.py** 的內容。以下新實作的 *hello* 函數搭配先前安裝的個別範例資料庫 *world* 使用 *Counter* 模型，產生 "*World*" 字串的適當取代：

        from django.http import HttpResponse
        import django.db
        from counter.models import Counter
            
        def getCountry(intId):
        #Connect to the MySQL sample database 'world'
        cur = django.db.connections['world'].cursor()
        #Execute a trivial SQL query which returns the name of 
        #all countries contained in 'world'
        cur.execute("SELECT name from country")
        tmp = cur.fetchall()
        #Clean-up after ourselves
        cur.close()
        if intId >= len(tmp):
        return "countries exhausted"
        return tmp[intId][0]
            
        def hello(request):
        if len(Counter.objects.all())==0:
        #when the database corresponding to 'helloworld.counter' is 
        #initially empty...
        c = Counter(count=0)
        else:
        c = Counter.objects.all()[0]
        c.count += 1
        c.save()       
        world = getCountry(int(c.count))
        return HttpResponse("<html><body>Hello <em>" + world + "</em></body></html>")

部署並執行 Django 網站
----------------------

注意：下列顯示如何在測試環境中執行 Django。若要在生產環境中執行它，請按照「Django Hello World 教學課程」的「使用 FastCGI 設定 IIS」小節進行。FastCGI 不需要使用 Windows 防火牆用戶端，對於 Windows Server 2K8 R2 虛擬機器上的網際網路流量開啟連接埠 80。

1.  切換回 Windows PowerShell 視窗，並輸入下列命令對外公開部署 Django 網站：

        PS C:\django\helloworld> $ipPort = 
        PS C:\django\helloworld> $ipPort = [string]$ipPort.AddressList[1]
        PS C:\django\helloworld> $ipPort += ":80"
        PS C:\django\helloworld> C:\Python27\python.exe .\manage.py runserver $ipPort

    **runserver** 參數將指示 Django 在 TCP 連接埠 *80* 上執行 *helloworld* 網站。此命令的結果應該如下所示：

        PS C:\django\helloworld> C:\Python27\python.exe .\manage.py runserver $ipPort
        Validating models...
            
        0 errors found
        Django version 1.4, using settings 'helloworld.settings'
        Development server is running at http://123.34.56.78:80
        Quit the server with CTRL-BREAK.

2.  從本機網頁瀏覽器中，開啟 **http://*yourVmName*.cloudapp.net** (其中 *yourVmName* 是您在虛擬機器建立步驟中使用的名稱)。您應該會看見「Hello ... !」，如以下螢幕擷取畫面所示。這表示 Django 在虛擬機器中執行，並且正常運作。

    ![](./media/virtual-machines-python-use-mysql-django/mysql_tutorial01.png)

    重新整理網頁瀏覽器數次，您應該會看見訊息從 *"Hello **&lt;country abc\>**"* 變更為 *"Hello **&lt;some other country\>**"*。

1.  若要停止 Django 代管網站，只要切換回 PowerShell 視窗並按下 **CTRL-C** 即可。

關閉 Azure 虛擬機器
-------------------

完成本教學課程時，請關閉並/或移除新建立的 Azure 虛擬機器釋出資源供其他教學課程使用，並避免產生 Azure 使用的費用。

