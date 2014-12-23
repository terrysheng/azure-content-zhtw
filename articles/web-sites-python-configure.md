<properties urlDisplayName="Configuring Python with Azure Websites" pageTitle="在 Azure 網站上設定 Python" metaKeywords="" description="This tutorial describes options for authoring and configuring a basic Web server Gateway Interface (WSGI) compliant Python application on Azure Websites." metaCanonical="" services="web-sites" documentationCenter="Python" title="Configuring Python with Azure Websites" authors="huvalo" solutions="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="01/01/1900" ms.author="huvalo" />




# 在 Azure 網站上設定 Python #

本教學課程描述在 Azure 網站上編寫與設定基本的 Web 伺服器閘道介面 (WSGI) 相容之 Python 應用程式的選項。開始使用 Azure 網站很簡單，而您的 Python 應用程式將有空間可擴充至其他 Azure 服務。Azure 網站平台內含 Python (2.7.3 或 3.4.0，可自選) 以及 Python 專屬的泛用型 wfastcgi.py FastCGI 處理常式。您只需將網站設定為使用 Python 處理常式即可。 

> [WACOM.NOTE] 您可以開啟網站的 [設定] 索引標籤，然後變更 [**Python 版本**] 設定，藉此選取要在 Azure 網站入口網站中使用的 Python 版本。

如需在 Azure 網站上設定 Django 架構的更複雜範例，請參閱下列教學課程： 
[http://www.windowsazure.com/zh-tw/develop/python/tutorials/web-sites-with-django](http://www.windowsazure.com/zh-tw/develop/python/tutorials/web-sites-with-django) (英文)。  

## WSGI 支援

WSGI 是由 [PEP 3333](http://www.python.org/dev/peps/pep-3333/) 描述的一項 Python 標準，此標準定義了 Web 伺服器與 Python 之間的介面。此標準為您提供標準化介面，方便您使用 Python 撰寫各種 Web 應用程式與架構。今日熱門的 Python Web 架構都採用 WSGI。Azure 網站針對此類任何架構提供支援，而進階使用者甚至可以撰寫自己的架構，但前提是自訂處理常式必須遵守 WSGI 規範指示。

## 網站建立

本教學課程假定現有一個 Azure 訂閱，而且能夠存取 Azure 管理入口網站。以下網站提供您建立網站的詳細說明：[http://www.windowsazure.com/zh-tw/manage/services/web-sites/how-to-create-websites](http://www.windowsazure.com/zh-tw/manage/services/web-sites/how-to-create-websites)。
 
簡單地說，如果您還沒有網站，則可以從 Azure 管理入口網站建立一個網站。選取 [網站] 功能並使用 [快速建立] 選項，然後為您的網站指定一個 URL。

![](./media/web-sites-python-configure/configure-python-create-website.png)

## Git 發行

在您新建立的網站上使用 [快速啟動] 或 [儀表板] 索引標籤以設定 Git 發行。本教學課程將使用 Git 來建立、管理並將您的 Python 網站發行至 Azure 網站。 

![](./media/web-sites-python-configure/configure-python-git.png)

Git 發行設定完畢後，會建立一個 Git 儲存機制並與您的網站產生關聯。該儲存機制的 URL 會加以顯示，方便您將資料從本機開發環境推送到雲端。若要透過 Git 發行應用程式，請確保同時安裝了 Git 用戶端，並遵守提供的指示將您的網站內容推送到 Azure 網站。

## 網站內容

我們在範例中使用基本 Python 應用程式搭配基本 WSGI 處理常式，以說明要在 Azure 網站中充分利用 Python 支援所需投入的最少工作。因此，您可使用此一骨架式 Python 應用程式開始編寫各式各樣的解決方案，包括如以下範例所示的複雜程度，一路到功能完整的 Web 架構。  

以下為基本 WSGI 處理常式的程式碼。它類似 [PEP 3333](http://www.python.org/dev/peps/pep-3333/) 規範所建議的程式碼，可作為 WSGI 相容應用程式的起點。我們將此內容儲存在名為 ConfigurePython.py (建立於網站根目錄下方 ConfigurePython 資料夾中) 的檔案：

	def application(environ, start_response):
	    status = '200 OK'
	    response_headers = [('Content-type', 'text/plain')]
	    start_response(status, response_headers)
	    yield 'Hello from Azure Websites\n'

*application* 可由 Python 呼叫，以作為 WSGI 相容伺服器所呼叫的進入點。此一可呼叫物件接受 2 個位置引數： 

* *environ*：內含各種環境變數的目錄
* *start_response*：由 Web 伺服器提供，以便傳輸 HTTP 狀態及回應標題的可呼叫物件

此處理常式會針對所接收的每一項要求傳回這段 "Hello from Azure Websites" 純文字。

## 組態選項

在 Azure 網站上設定 Python 應用程式可使用 2 種不同的選項。

<h3 id="option1">選項 1：入口網站</h3>

1.1. 透過入口網站中的 [設定] 索引標籤來註冊 FastCGI 處理常式。
在此範例中，我們使用 Azure 網站內含的 Python 專用 FastCGI 處理常式。若要執行相同程序，請針對您的指令碼處理器與 FastCGI 處理常式引數使用下列路徑：

* Python 指令碼處理器路徑：D:\python27\python.exe
* Python FastCGI 處理常式路徑：D:\python27\scripts\wfastcgi.py

![](./media/web-sites-python-configure/configure-python-handler-mapping.png)

1.2. 透過入口網站中相同的 [設定] 索引標籤來進行應用程式設定。
這些應用程式設定會轉換為環境變數。您可以在組態值上使用這項機制來滿足您的 Python 應用程式需求。為方便說明此基本範例應用程式，我們進行以下設定：

* PYTHONPATH 會通知 Python 關於搜尋模組的目錄。Azure 網站提供 D:\home\site\wwwroot，讓語法糖衣 (syntactic sugar) 指向您的網站根目錄。 
* WSGI\_HANDLER 會記錄模組或套件名稱，以及要使用的屬性。

![](./media/web-sites-python-configure/configure-python-app-settings.png)

<h3 id="option2">選項 2：web.config</h3>
替代的組態則是使用網站根目錄下的 web.config 檔案來執行下述動作。使用 web.config 選項可為 Web 應用程式提供更優異的可攜性潛力。您可以透過 2 種方式，將要求引導至 Web 應用程式：第一種是設定處理 * 路徑的處理常式，以便指示 IIS 將每個內送的要求引導至 Python，第二種則是設定 Python 會處理的特定路徑，進而使用 URL 重新撰寫將各種 URL 重新導向至我們選取的路徑。事實上，我們建議使用第二種方法，亦即在網站根目錄下使用空的處理常式檔案作為要求目標 (範例中使用 handler.fcgi) 以獲得更優異的效能。在先前的案例中，包括適用靜態內容的所有要求 (例如，圖片檔與樣式表) 都必須通過 Python，進而顛覆 Web 伺服器所提供的最佳化功能以便存取靜態檔案。使用第二種方法可有效率地處理靜態內容，並在必要時才叫用 Python。

指定 PYTHONPATH 變數。 
> 此舉會通知 Python 該往何處尋找應用程式程式碼。在此也會使用 D:\home\site\wwwroot 作為網站的絕對路徑。

2.2. 設定 WSGI\_HANDLER 變數。
> Azure 網站使用此值來引導 Python 呼叫我們的 WSGI 處理常式。此變數值是一項 Python 運算式，一旦執行會傳回代表 WSGI 處理常式的可呼叫物件。 

2.3. 為 Python 新增處理常式。
> 此舉會通知 Azure 網站 Python 必須處理對路徑 handler.fcgi 所做的要求。重要的是，處理常式語法必須與以下範例中 &lt;handlers&gt; 標籤內的語法完全一樣，否則您必須帶來自己的 FastCGI 處理常式或是 Python 開發堆疊。

2.4. 重新將 URL 寫入 handler.fcgi。
> 隨時要求 handler.fcgi 可能不是最好的辦法。為了選取由 Python 處理常式所處理的檔案路徑，我們使用 URL 重新寫入方法，以便所有 URL 都能由我們的 Python 處理常式加以處理。

	<configuration>
  		<appSettings>
    		<add key="pythonpath" value="D:\home\site\wwwroot\ConfigurePython" />
    		<add key="WSGI_HANDLER" value="ConfigurePython.application" />
  		</appSettings>
  		<system.webServer>
    		<handlers>
      			<add name="PythonHandler" 
           		verb="*" path="handler.fcgi" 
           		modules="FastCgiModule" 
           		scriptProcessor="D:\Python27\Python.exe|D:\Python27\Scripts\wfastcgi.py" 
           		resourceType="Either" />
   			</handlers>
			<rewrite>
	    		<rules>
					<rule name="Configure Python" stopProcessing="true">
		    			<match url="(.*)" ignoreCase="false" />
		    			<conditions>
							<add input="{REQUEST_FILENAME}" matchType="IsFile" negate="true" />
		    			</conditions>
		    			<action type="Rewrite" url="handler.fcgi/{R:1}" appendQueryString="true" />
					</rule>
	    		</rules>
			</rewrite>
  		</system.webServer>
	</configuration> 

以下是網站根目錄下方範例的資料夾結構 (Python 資料夾與檔案名稱的大小寫非常重要，並反映在 web.config 中)：

* ConfigurePython\ConfigurePython.py
* web.config
* handler.fcgi

由於我們正將所有 URL 重新寫入 handler.fcgi 並透過 FastCGI 將該路徑導向 Python，我們需要建立一個同名的預留位置檔案，以免 IIS 傳回 HTTP 404 錯誤。這是因為 IIS FastCGI 模組的內部行為所致，該行為會強制所要求的檔案必須存在，才能傳遞至指定的指令碼處理器應用程式。

瀏覽至您的網站以測試正確的組態。在此範例中，存取時會顯示 "Hello from Azure Websites" 訊息。

![](./media/web-sites-python-configure/configure-python-result.png)

