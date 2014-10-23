<properties linkid="develop-python-django-with-visual-studio" urlDisplayName="Django with Visual Studio" pageTitle="Django with Visual Studio (Python) - Azure tutorial" metaKeywords="Azure Django web app, Azure Django virtual machine" description="A tutorial that teaches you how to build a Django web application hosted in an Azure virtual machine." metaCanonical="" services="cloud-services" documentationCenter="Python" title="Creating Django applications with Python Tools for Visual Studio 1.5" authors="huvalo" solutions="" manager="" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="01/01/1900" ms.author="huvalo"></tags>

# 使用 Python Tools for Visual Studio 1.5 建立 Django 應用程式

**注意：**本教學課程也已製作成 [Youtube 影片][]。

**注意：**目前已針對 PTVS 2.0 Beta 設計了[更新、內容更豐富的教學課程][]。

透過可用工具，即可輕鬆開發 Azure。本教學課程假設您先前沒有使用 Azure 的經驗。完成本指南之後，您將在雲端啟動並執行 Django 應用程式。

您將了解：

-   如何建立基本 Django 應用程式
-   如何使用 Django 測試伺服器在本機執行您的 Django 應用程式，並為其偵錯
-   如何在計算模擬器中本機執行您的 Django 應用程式
-   如何將您的應用程式發佈及重新發佈至 Azure。

依照本教學課程的步驟，您將建置一個簡單的 Hello World Web 應用程式。應用程式將託管於 Web 角色的執行個體中，而此執行個體在 Azure 中執行時，本身將託管於專用的虛擬機器 (VM) 中。

完成之應用程式的螢幕擷取畫面如下：

![][]

[WACOM.INCLUDE [create-account-note][]]

## <span id="setup"></span> </a>設定開發環境

在開始開發 Azure 應用程式之前，您需要先取得工具，並設定開發環境。如需取得及安裝 Azure SDK for Python 的詳細資訊，請參閱[如何安裝 Python][]。

**注意：**本教學課程需要 Python 2.7 和 Django 1.4。目前的 Azure SDK for Python 附有這些版本。

**注意：**對計算模擬器和 (或) Azure 進行部署時，必須使用完整版本的 Visual Studio (不支援整合式 Shell)。

## 建立新的 Django 應用程式

若要建立新的 Django 應用程式，請先啟動 Visual Studio，然後使用 [檔案] \> [新增專案] 建立新專案。找出 [Python] 索引標籤 (可從頂層或 [其他語言] 區域中存取)，然後選取 [Django 應用程式] 範本：

![New Python Project Templates][]

按一下 [確定]，即會建立您的第一個 Django 應用程式。

![Visual Studio opened to your first Django project][]

接著，我們將開始開發第一個 Django 應用程式。您可以用滑鼠右鍵按一下模組節點，然後選取 [Add New Django app]，在您的專案中設定新的應用程式：

![Add New App menu item][]

接著，您可以為應用程式輸入新的名稱：

![Add New App name prompt][]

輸入應用程式的名稱後，請按一下 [確定]，新的應用程式即會新增至您的專案：

![Solution Explorer with new app added][]

接著請更新 **settings.py**，以註冊您的應用程式。這會使 Django 自動探索已新增至應用程式 Templates 目錄的範本檔案。請將應用程式名稱新增至 INSTALLED\_APPS 區段：

    'DjangoApplication.MyFirstApp',

![Add app to settings.py in INSTALLED\_APPS][]

接著，我們要將適當的程式碼新增至應用程式的 **views.py**，以便傳回簡易範本檔案：

    from django.http import HttpResponse
    from django.template.loader import render_to_string
    def home(request):
        return HttpResponse(render_to_string(
                                            'index.html',
                                            {'content': 'Hello World'}
                                            ))

![Add app to settings.py in INSTALLED\_APPS][1]

然後，我們要新增在您瀏覽此檢視時所將呈現的簡易範本檔案。若要執行此動作，請以滑鼠右鍵按一下 Templates 資料夾，然後選取 [Add new Item]：

![Add new item to Templates foder][]

現在，您可以從範本清單中選取 [Django HTML 範本]，然後輸入 **index.html** 作為檔案名稱：

![Add new item to Templates foder][2]

範本會隨即新增至專案並開啟。在此，您會看見某些針對範本標籤而強調顯示的語法開頭：

![Template Added to Solution Explorer][]

此時您可以開始更新範本以變更轉譯的 HTML，而在執行此動作時，您會有很好的 Intellisense：

![Template Intellisense for Django Filters][]

開頭大寫要不要保留都可以，這對此教學課程的結果不會有太大的影響。最後，您只需以 url 形式在 **urls.py** 中註冊您的檢視即可。請將其新增至 **urlpatterns**：

    url(r'^$', 'DjangoApplication.MyFirstApp.views.home', name='home'),

![Register URL][]

## 在測試伺服器中本機執行您的應用程式

現在，您已建立您的第一個 Django 應用程式。接著您只要**按 F5**，即可在本機加以執行。

![Django Hello World in a Browser and the Test Server][]

這會使您執行 Django **manage.py** 的 Python 解譯器開始執行測試伺服器。成功啟動測試伺服器之後，也將啟動網頁瀏覽器來檢視網站。由於您是以 F5 啟動的，應用程式會在偵錯程式下啟動，因此我們也可以在任何 Python 程式碼 (例如檢視程式碼) 或範本檔案本身中設定中斷點：

![Debugger stopped at a template breakpoint][]

現在，您可以**按一下停止按鈕**，轉而在 Azure 計算模擬器中執行。

## 在模擬器本機執行您的應用程式

若要在計算模擬器中執行，您只需將 Azure 部署專案新增至您在方案中的 Django 專案即可。

**注意：**對計算模擬器和 (或) Azure 進行部署時，必須使用完整版本的 Visual Studio (不支援整合式 Shell)。

請以滑鼠右鍵按一下 [方案總管] 中的 Django 專案節點，然後選取 [新增 Azure 雲端服務專案] 即可：

![Add Deployment Project][]

執行此命令後，您會在方案總管中看見新增的專案：

![After Add Deployment Project][]

這個新專案此時也會標示為方案中的起始專案。此時，您必須**以系統管理員的身分重新啟動 Visual Studio**，才能在計算模擬器中執行，但在此之後，我們只需**按 F5**，應用程式即會在計算模擬器中執行及部署：

![After Add Deployment Project][3]

此時您會發現，雖然我們檢視的是相同的網頁，但 URL 卻略有不同。您也會發現此時並沒有執行 Django 測試伺服器的 python.exe。我們會透過 IIS，使用自動加入、且會在藉由 Visual Studio 而運作時進行設定的 FastCGI 閘道，來執行 Django。

在計算模擬器中執行時，您可以快速地逐一檢測您的應用程式 - 只要切換回 Visual Studio、更新您的檔案，再重新整理網頁瀏覽器即可。結果會立即顯示。

## 將應用程式部署到 Azure

現在，您已可將專案部署至 Azure。若要執行此作業，您只需以滑鼠右鍵按一下 [方案總管] 中的 Azure 部署專案，再選取 [發佈] 即可：

![Package App Menu][]

選取 [發佈] 後，將會出現登入 Azure 的提示。您可以在此處匯入現有的認證，或設定新認證：

![Package Subscription][]

選取認證後，您會看見 [Azure Publish Settings] 畫面。您可以選取不同的選項來決定部署的執行方式，也可以直接按 [發佈]：

![Package Settings][]

現在，您必須等待應用程式完成設定和部署。

![Package Deployment][]

完成所有設定之後，您可以按一下 [DNS 名稱] 下方的連結，來檢視您在雲端中執行的網站：

![Your Django app in the cloud][]

  [Youtube 影片]: http://www.youtube.com/watch?v=UsLti4KlgAY
  [更新、內容更豐富的教學課程]: ../web-sites-python-create-deploy-django-app/
  []: ./media/python-django-app-tools-visual-studio/ptvs-dj-FirstAppInCloud.png
  [create-account-note]: ../includes/create-account-note.md
  [如何安裝 Python]: ../python-how-to-install/
  [New Python Project Templates]: ./media/python-django-app-tools-visual-studio/ptvs-dj-NewProject.png
  [Visual Studio opened to your first Django project]: ./media/python-django-app-tools-visual-studio/ptvs-dj-FirstProject.png
  [Add New App menu item]: ./media/python-django-app-tools-visual-studio/ptvs-dj-AddNewApp.png
  [Add New App name prompt]: ./media/python-django-app-tools-visual-studio/ptvs-dj-AddNewAppPrompt.png
  [Solution Explorer with new app added]: ./media/python-django-app-tools-visual-studio/ptvs-dj-MyFirstApp.png
  [Add app to settings.py in INSTALLED\_APPS]: ./media/python-django-app-tools-visual-studio/ptvs-dj-InstallApp.png
  [1]: ./media/python-django-app-tools-visual-studio/ptvs-dj-FirstView.png
  [Add new item to Templates foder]: ./media/python-django-app-tools-visual-studio/ptvs-dj-AddFirstTemplate.png
  [2]: ./media/python-django-app-tools-visual-studio/ptvs-dj-NewDjangoTemplate.png
  [Template Added to Solution Explorer]: ./media/python-django-app-tools-visual-studio/ptvs-dj-TemplateAdded.png
  [Template Intellisense for Django Filters]: ./media/python-django-app-tools-visual-studio/ptvs-dj-TemplateIntellisense.png
  [Register URL]: ./media/python-django-app-tools-visual-studio/ptvs-dj-RegisterUrl.png
  [Django Hello World in a Browser and the Test Server]: ./media/python-django-app-tools-visual-studio/ptvs-dj-DjangoHelloWorldTestServer.png
  [Debugger stopped at a template breakpoint]: ./media/python-django-app-tools-visual-studio/ptvs-dj-TemplateBreakpoint.png
  [Add Deployment Project]: ./media/python-django-app-tools-visual-studio/ptvs-dj-AddDeploymentProject.png
  [After Add Deployment Project]: ./media/python-django-app-tools-visual-studio/ptvs-dj-AfterDeployProjAdded.png
  [3]: ./media/python-django-app-tools-visual-studio/ptvs-dj-ComputeEmulator.png
  [Package App Menu]: ./media/python-django-app-tools-visual-studio/ptvs-dj-publish1.png
  [Package Subscription]: ./media/python-django-app-tools-visual-studio/ptvs-dj-publish2.png
  [Package Settings]: ./media/python-django-app-tools-visual-studio/ptvs-dj-publish3.png
  [Package Deployment]: ./media/python-django-app-tools-visual-studio/ptvs-dj-publish4.png
  [Your Django app in the cloud]: ./media/python-django-app-tools-visual-studio/ptvs-dj-FirstAppInCloud.png
