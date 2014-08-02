<properties linkid="develop-python-web-app-with-django" urlDisplayName="Web with Django (Windows)" pageTitle="Python web app with Django - Azure tutorial" metaKeywords="Azure Django web app, Azure Django virtual machine" description="A tutorial that teaches you how to host a Django-based website on Azure using a Windows Server 2008 R2 virtual machine." metaCanonical="" services="virtual-machines" documentationCenter="Python" title="Django Hello World Web Application" authors="" solutions="" manager="" editor="" />

Django Hello World Web 應用程式
===============================

[Windows](/en-us/develop/python/tutorials/web-app-with-django/ "Windows")[Mac/Linux](/en-us/develop/python/tutorials/django-hello-world-(maclinux)/ "MacLinux")

本教學課程說明如何使用 Windows Server 虛擬機器在 Azure 上裝載 Django 型網站。本教學課程假設您先前沒有使用 Azure 的經驗。完成本指南之後，您將在雲端啟動並執行 Django 型應用程式。

您將了解如何：

-   設定 Azure 虛擬機器以裝載 Django。雖然本教學課程說明如何在 **Windows Server** 下完成這項程序，不過使用 Azure 中裝載的 Linux VM 也可以完成相同的程序。
-   從 Windows 建立新的 Django 應用程式。

依照本教學課程的步驟，您將建置一個簡單的 Hello World Web 應用程式。該應用程式將裝載於 Azure 虛擬機器中。

完成之應用程式的螢幕擷取畫面如下：

![A browser window displaying the hello world page on Azure](./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-browser-azure.png)

[WACOM.INCLUDE [create-account-and-vms-note](../includes/create-account-and-vms-note.md)]

建立及設定 Azure 虛擬機器以裝載 Django
--------------------------------------

1.  按照[此處](/en-us/manage/windows/tutorials/virtual-machine-from-gallery/) (英文) 提供的指示，建立 *Windows Server 2012 Datacenter* 散發套件的 Azure 虛擬機器。

2.  指示 Azure 將連接埠 **80** 的流量從 Web 導向虛擬機器上的連接埠 **80**：

    -   在 Azure 入口網站中瀏覽至新建立的虛擬機器，並按一下 [端點]** 索引標籤。
    -   按一下畫面底部的 [新增端點]** 按鈕。
    ![add endpoint](./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-addendpoint.png)

    -   開啟 *[TCP]* 通訊協定的 *[公用連接埠 80]*，比照 *[私人連接埠 80]*。 
    ![](./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-port80.png)

1.  使用 Windows *Remote Desktop* 從遠端登入新建立的 Azure 虛擬機器。

**重要事項：**以下所有指示皆假設您已正確登入虛擬機器，並且是在該處發出命令，而不是在本機電腦上發出命令！

設定開發環境
------------

若要設定您的 Python 和 Django 環境，請參閱[安裝指南](../python-how-to-install/) (英文) 以了解詳細資訊。

**備註 1：** 您*「只」*需要從 Windows WebPI 安裝程式將 **Django** 產品安裝在 Azure 虛擬機器上，即可讓*「這個」*特定的教學課程運作。

**備註 2：** 為了下載 WebPI 安裝程式，您可能必須設定 IE ESC 設定 ([開始]/[系統管理工具]/[伺服器管理員]，然後按一下 **[設定 IE ESC]**，將其設為 [關閉])。

設定含 FastCGI 的 IIS
---------------------

1.  安裝含 FastCGI 支援的 IIS

         start /wait %windir%\System32\PkgMgr.exe /iu:IIS-WebServerRole;IIS-WebServer;IIS-CommonHttpFeatures;IIS-StaticContent;IIS-DefaultDocument;IIS-DirectoryBrowsing;IIS-HttpErrors;IIS-HealthAndDiagnostics;IIS-HttpLogging;IIS-LoggingLibraries;IIS-RequestMonitor;IIS-Security;IIS-RequestFiltering;IIS-HttpCompressionStatic;IIS-WebServerManagementTools;IIS-ManagementConsole;WAS-WindowsActivationService;WAS-ProcessModel;WAS-NetFxEnvironment;WAS-ConfigurationAPI;IIS-CGI

2.  設定 Python 快速 CGI 處理常式

         %windir%\system32\inetsrv\appcmd set config /section:system.webServer/fastCGI "/+[fullPath='c:\Python27\python.exe', arguments='C:\Python27\Scripts\wfastcgi.py']"

3.  登錄此網站的處理常式

         %windir%\system32\inetsrv\appcmd set config /section:system.webServer/handlers "/+[name='Python_via_FastCGI',path='*',verb='*',modules='FastCgiModule',scriptProcessor='c:\Python27\python.exe|C:\Python27\Scripts\wfastcgi.py',resourceType='Unspecified']"

4.  設定處理常式以執行您的 Django 應用程式

         %windir%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='C:\Python27\python.exe', arguments='C:\Python27\Scripts\wfastcgi.py'].environmentVariables.[name='DJANGO_SETTINGS_MODULE',value='DjangoApplication.settings']" /commit:apphost

5.  設定 PYTHONPATH，讓 Python 解譯器能夠找到您的 Django 應用程式

         %windir%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='C:\Python27\python.exe', arguments='C:\Python27\Scripts\wfastcgi.py'].environmentVariables.[name='PYTHONPATH',value='C:\inetpub\wwwroot\DjangoApplication']" /commit:apphost

    您應該會看見下列內容：

    ![IIS config1](./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-iis1.png)

6.  告訴 FastCGI 到 WSGI 的閘道要使用哪個 WSGI 處理常式：

         %windir%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='C:\Python27\python.exe', arguments='C:\Python27\Scripts\wfastcgi.py'].environmentVariables.[name='WSGI_HANDLER',value='django.core.handlers.wsgi.WSGIHandler()']" /commit:apphost

7.  從 [codeplex](http://go.microsoft.com/fwlink/?LinkID=316392&clcid=0x409) (英文) 下載 wfastcgi.py 並將它儲存至 C:\\Python27\\Scripts。這是先前的命令用來登錄 FastCGI 處理常式的位置。或者，您也可以使用 Web Platform Installer 來進行安裝。請搜尋 'WFastCGI'。

建立新的 Django 應用程式
------------------------

1.  啟動 cmd.exe

2.  將目錄切換至 C:\\inetpub\\wwwroot

3.  輸入下列命令以建立新的 Django 專案：

    C:\\Python27\\python.exe -m django.bin.django-admin startproject DjangoApplication

    ![The result of the New-AzureService command](./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-cmd-new-azure-service.png)

	**django-admin.py** 指令碼會為 Django 型網站產生一個基本結構：

-   **manage.py** 可協助您開始裝載及停止裝載 Django 型網站。
-   **DjangoApplication\\settings.py** 包含您應用程式的 Django 設定。
-   **DjangoApplication\\urls.py** 包含每個 URL 與其檢視之間的對應碼。

1.  在 *C:\\inetpub\\wwwroot\\DjangoApplication* 的 *DjangoApplication* 子目錄中建立一個名為 **views.py** 的新檔案做為 **urls.py** 的同層級項目。這將包含轉譯 "hello world" 頁面的檢視。啟動您的編輯器並輸入下列程式碼：

        from django.http import HttpResponse
        def hello(request):
            html = "<html><body>Hello World!</body></html>"
            return HttpResponse(html)

2.  現在，以下列程式碼取代 **urls.py** 檔案的內容：

        from django.conf.urls.defaults import patterns, include, url
        from DjangoApplication.views import hello
        urlpatterns = patterns('',
        (r'^$',hello),
        )

3.  最後，在您的瀏覽器中載入網頁。

![A browser window displaying the hello world page on Azure](./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-browser-azure.png)

關閉 Azure 虛擬機器
-------------------

完成本教學課程時，請關閉並/或移除新建立的 Azure 虛擬機器釋出資源供其他教學課程使用，並避免產生 Azure 使用的費用。

