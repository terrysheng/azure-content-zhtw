<properties linkid="develop-python-web-site-with-django" urlDisplayName="Websites with Django" pageTitle="Python Websites with Django - Azure tutorial" metaKeywords="Azure django, django website" description="A tutorial that introduces you to running a Python website on Azure." metaCanonical="" services="web-sites" documentationCenter="Python" title="Creating Websites with Django" authors="huvalo" solutions="" manager="" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="01/01/1900" ms.author="huvalo"></tags>

# 使用 Django 建立網站

本教學課程說明如何開始在 Azure 網站上執行 Python。Azure 網站提供有限的免費主控和快速的部署，而現在您可以使用 Python！隨著應用程式規模增加，您可以切換為付費主控，也可以與其他所有 Azure 服務整合。

本教學課程示範如何部署以 Django Web 架構組建的應用程式。本教學課程會引導您逐步完成應用程式和所需之程式庫的部署，包括 Django。您會將上述內容全部放入 Git 儲存機制，以便快速而簡便地將更新推送到網站。最終，您會透過 Azure 設定新建立的網站，使其執行您的 Python 應用程式。

[WACOM.INCLUDE [create-account-and-websites-note][]]

本教學課程使用 Python 2.7 和 Django 1.4。您可以自行取得，或使用 [][]<http://www.windowsazure.com/zh-tw/develop/python/></a> (英文) 的 Windows Installer 連結迅速而便利地進行安裝。

**注意**：Azure 網站現在隨附預先安裝的 Python (您可以選擇 2.7.3 或 3.4.0) 和 wfastcgi 處理常式。不過 Django 等 Web 架構則未包含在內。如果您喜歡的話，也可以使用不同的 Python 解譯器。只要將它納入 Git 儲存機制，再將網站設定為使用該解譯器 (而非已安裝的 Python 2.7 解譯器) 即可。

> [WACOM.NOTE] 您現在可以藉由開啟網站的 [設定] 索引標籤，並變更 [Python 版本] 設定，來選取要在 Azure 網站入口網站中使用的 Python 版本。

您還需要安裝將網站推送到 Azure 的部署選項。可用的部署工具很多，不過本教學課程使用 Git。我們推薦使用 [msysgit][] (英文)。

**注意**：Python 專案目前不支援 TFS 發佈。

安裝好 Python、Django 及 Git 後，您便已備妥開始這項工作所需的所有元件。

## 在入口網站上建立網站

建立應用程式的第一步是透過 Azure 管理入口網站建立網站。若要建立網站，您需要登入入口網站並按一下左下角的 [新增] 按鈕。隨即會出現一個視窗。按一下 [快速建立]、輸入 URL，然後選取 [建立網站]。

![][]

系統隨即會迅速地設定網站。接下來，您需要新增透過 Git 發佈的支援功能。您可以藉由選擇 [設定從原始檔控制進行部署] 來完成操作。

![][1]

在 [Set up deployment] 對話方塊中，向下捲動並選取 [Local Git] 選項。按一下向右箭頭以繼續操作。

![][2]

設定好 Git 發佈後，您會短暫地看到一個頁面，通知您系統正在建立儲存機制。當儲存機制準備妥當後，系統會帶領您前往部署索引標籤。部署索引標籤含有如何連接的指示。

![][3]

## 網站開發

您已在 Azure 中建立 Git 儲存機制，現在可以開始將網站從本機電腦填入 Git 儲存機制。第一個步驟是使用提供的 URL 複製現有的空白網站：

![][4]

現階段您已經做好設定網站登錄的準備了。您需要執行幾項操作：

1.  納入 Django 程式庫和其他用來執行網站的程式庫。
2.  納入 Django 應用程式的程式碼。

首先，請納入 Django 程式庫。若要納入 Django 程式庫，請先建立名為 site-packages 的新目錄，然後使用以下命令將安裝的 Django 版本複製到該目錄：

    mkdir site-packages
    cd site-packages
    xcopy /s C:\Python27\lib\site-packages\* .

這些命令會複製 site-packages 內的所有程式庫，包括 Django。如果有網站未使用的程式庫，您可以放心地將其移除。

![][5]

接下來，建立初始的 Django 應用程式。您可以從命令列建立應用程式，如同建立其他任何 Django 應用程式一般，也可以使用 [Python Tools for Visual Studio][] (英文) 來建立專案。以下內容展示這兩種選項。

**選項 1：**
若要從命令列建立新專案，請執行以下命令。該命令會在 DjangoApplication 資料夾內建立 Django 應用程式：

     C:\Python27\python.exe -m django.bin.django-admin startproject DjangoApplication

![][6]

**選項 2：**
您也可以使用 Python Tools for Visual Studio 來建立新網站。在已安裝 Python Tools for Visual Studio 的情況下啟動 Visual Studio，然後選取 [檔案] -\> [新增專案]。在 [Other Languages] 下，向下鑽研 Python 專案並選取 [Django Application]。輸入 **DjangoApplication** 作為專案名稱並確認 [為方案建立目錄] 未勾選，以便使目錄結構與從命令列建立 Django 應用程式時相同。此選項會為您設定 Visual Studio 方案和專案檔案，帶給您包括範本偵錯和 IntelliSense 的絕佳本機部署經驗。

![][7]

現在您只需要新增剛才新增的所有檔案，然後將網站推送到 Git。若要這樣做，請執行以下命令：

    git add DjangoApplication site-packages
    git commit -m "Initial site"
    git remote add azure https://dinov@pythonwebsite.scm.azurewebsites.net/PythonWebSite.git
    git push azure master

第一個命令會新增未追蹤的檔案，以便進行追蹤。第二個命令會認可剛才新增至儲存機制的檔案。第三個命令會為儲存機制新增名為 *azure* 的遠端。最後一個命令會接受變更並推送到遠端儲存機制。最後一個命令同時也會啟動部署作業。完成操作後，您應該可以看見與下面相似的結果：

![][8]

完成推送後，您會看見 Azure 入口網站重新整理及顯示作用中的部署作業：

![][9]

## 網站組態

現在您需要設定網站，使其得知 Django 專案並使用 wfastcgi 處理常式。若要這樣做，您可以按一下畫面頂端的 [設定] 索引標籤，然後向下捲動到頁面的下半部 (含有 [應用程式設定] 和 [處理常式對應] 等區段)。

於此處配置的所有設定會在實際的要求期間轉變成環境變數。這表示您可以利用這點來設定 DJANGO\_SETTINGS\_MODULE 環境變數以及 PYTHONPATH 和 WSGI\_HANDLER。如果應用程式有其他組態值，您可以在此處予以指派並從環境中挑選。有時候您會想要設定網站中檔案的路徑。例如，您可以為 PYTHONPATH 設定此項目。當網站以 Azure 網站形式運作時，它的位置會在 \*\*D:\\home\\site\\wwwroot\*\* 之中，因此您可以將這項資訊用於需要磁碟中檔案之完整路徑的場合。

若要設定 Django 應用程式，您需要建立三個環境變數。第一個是 DJANGO\_SETTINGS\_MODULE，它能提供用於組態的 Django 應用程式模組名稱。第二個是 PYTHONPATH 環境變數，它能指定設定模組所在的套件。第三個是 WSGI\_HANDLER。此變數是模組/套件名稱，再加上要使用之模組中的屬性 (如 mypackage.mymodule.handler)。請加上括弧以指出應呼叫該屬性。綜觀以上資訊，請將這些變數設定為：

    DJANGO_SETTINGS_MODULE    DjangoApplication.settings
    PYTHONPATH                D:\home\site\wwwroot\DjangoApplication;D:\home\site\wwwroot\site-packages
    WSGI_HANDLER              django.core.handlers.wsgi.WSGIHandler()

![][10]

現在您需要設定處理常式對應。請使用 Python 解譯器的路徑和 wfastcgi.py 指令碼的路徑來登錄所有副檔名的處理常式：

    EXTENSION                 *
    SCRIPT PROCESSOR PATH     D:\python27\python.exe
    ADDITIONAL ARGUMENTS      D:\python27\scripts\wfastcgi.py

![][11]

此時，您可以按一下底部的 [儲存] 按鈕。

最後，請返回 [儀表板]。前往左下角的 [網站 URL]。按一下連結並開啟新的 Django 網站，其外觀如下：

![][12]

## 後續步驟

現階段，您可以利用目前使用中的工具來繼續開發 Django 應用程式。如果您使用 [Python Tools for Visual Studio][] (英文) 來進行部署，也許會想要安裝 [VisualGit][] (英文) 以從 Visual Studio 內部取得原始檔控制整合。

您的應用程式可能會擁有 Python 和 Django 之外的相依性。如果您使用 [][]<http://www.windowsazure.com/zh-tw/develop/python/></a> (英文) 的安裝程式來安裝 Python，便已安裝了可用來快速新增相依性的 PIP。例如，若要安裝自然語言工具組和該工具組的所有相依性，請輸入：

    pip install nltk

接著，您需要將 C:\\Python27\\Lib\\site-packages 中的檔案複製到本機 site-packages 目錄，藉此更新 site-packages 目錄。

複製檔案後，請執行命令 **git status** 來查看新增的檔案，接著再依序執行 **git add** 和 **git commit** 來認可儲存機制的變更。最後，您可以執行 **git push** 將更新的網站部署到 Azure。

現在，您可以前往 DjangoApplication 目錄，再依照平常的用法使用 manage.py 來開始將新應用程式加入 Django 專案。

  [create-account-and-websites-note]: ../includes/create-account-and-websites-note.md
  []: http://www.windowsazure.com/zh-tw/develop/python/
  [msysgit]: http://code.google.com/p/msysgit/
  []: ./media/web-sites-python-create-deploy-django-app/django-ws-003.png
  [1]: ./media/web-sites-python-create-deploy-django-app/django-ws-004.png
  [2]: ./media/web-sites-python-create-deploy-django-app/django-ws-005.png
  [3]: ./media/web-sites-python-create-deploy-django-app/django-ws-006.png
  [4]: ./media/web-sites-python-create-deploy-django-app/django-ws-007.png
  [5]: ./media/web-sites-python-create-deploy-django-app/django-ws-008.png
  [Python Tools for Visual Studio]: http://pytools.codeplex.com/
  [6]: ./media/web-sites-python-create-deploy-django-app/django-ws-010.png
  [7]: ./media/web-sites-python-create-deploy-django-app/django-ws-011.png
  [8]: ./media/web-sites-python-create-deploy-django-app/django-ws-013.png
  [9]: ./media/web-sites-python-create-deploy-django-app/django-ws-014.png
  [10]: ./media/web-sites-python-create-deploy-django-app/django-ws-015.png
  [11]: ./media/web-sites-python-create-deploy-django-app/django-ws-016.png
  [12]: ./media/web-sites-python-create-deploy-django-app/django-ws-017.png
  [VisualGit]: http://code.google.com/p/visualgit/
