<properties
	pageTitle="在 Azure App Service Web Apps 中設定 PHP"
	description="了解如何設定預設的 PHP 安裝，或是在 Azure App Service 中新增適用於 Web Apps 的自訂 PHP 安裝。"
	services="app-service\web"
	documentationCenter="php"
	authors="tfitzmac"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="PHP"
	ms.topic="article"
	ms.date="03/24/2015"
	ms.author="tomfitz"/>

#在 Azure App Service Web Apps 中設定 PHP 

## 簡介

本指南將示範如何在 [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) 的 Web Apps 中設定內建的 PHP 執行階段、提供自訂的 PHP 執行階段，以及啟用擴充功能。若要使用 App Service，請註冊[免費試用]。若要充分利用本指南，您應該先在 App Service 中建立 PHP Web 應用程式。

## 作法：變更內建 PHP 組態
當您建立 App Service Web 應用程式時，預設會安裝 PHP 5.4，而且可立即使用。若要查看可用的修訂版、其預設設定，以及啟用的擴充功能，最好的方法是部署呼叫 [phpinfo()] 函數的指令碼。

PHP 5.5 和 PHP 5.6 版本同樣可供使用，但預設並未啟用。若要更新 PHP 版本，請遵循下列步驟：

1. 在 [Azure 入口網站](http://go.microsoft.com/fwlink/?LinkId=529715)中瀏覽至您的 Web 應用程式，然後按一下 [設定] 按鈕。

	![Web 應用程式設定][settings-button]

2. 從 [設定] 刀鋒視窗中，選取 [應用程式設定] 並選擇新的 PHP 版本。

    ![應用程式設定][application-settings]

3. 按一下 [Web 應用程式設定] 刀鋒視窗頂端的 [儲存] 按鈕。

	![儲存組態設定][save-button]

對於任一個內建的 PHP 執行階段，您可以遵循下列步驟，來變更並非僅系統層級指示詞的任何設定選項。(如需僅系統層級指示詞的資訊，請參閱 [php.ini 指示詞的清單]。)

1. 將 [.user.ini] 檔案新增至您的根目錄。
2. 使用在 `.user.ini` 檔案中使用的相同語法，將組態設定新增至 `php.ini` 檔案。例如，如果您要開啟 `display_errors` 設定，並將 `upload_max_filesize` 設定設為 10M，則 `.user.ini` 檔案將包含下列文字：

		; Example Settings
		display_errors=On
		upload_max_filesize=10M

3. 部署 Web 應用程式。
4. 重新啟動 Web 應用程式。(必須重新啟動，因為 PHP 讀取 `.user.ini` 檔案的頻率是由 `user_ini.cache_ttl` 設定所控制，這是系統層級設定，預設為 300 秒 (5 分鐘)。重新啟動 Web 應用程式將強制 PHP 讀取 `.user.ini` 檔案中的新設定。)

除了使用 `.user.ini` 檔案之外，您也可以在指令碼中使用 [ini_set()] 函數，來設定非系統層級指示詞的設定選項。

## 作法：在預設 PHP 執行階段中啟用擴充
如同上一節所述，若要查看預設的 PHP 版本、其預設設定及啟用的擴充，最好的方法是部署呼叫 [phpinfo()] 函數的指令碼。若要啟用擴充，請依照下列步驟執行。

1. 將 `bin` 目錄新增至根目錄。
2. 將 `.dll` 擴充檔放入  `bin` 錄中 (例如，  `php_mongo.dll`)。確定擴充與 PHP 預設版本 (截至撰寫時為止，該版本為 PHP 5.4) 相容，而且與 VC9 及非安全執行緒 (nts) 相容。
3. 部署 Web 應用程式。
4. 在 Azure 入口網站中瀏覽至您的 Web 應用程式，然後按一下 [設定] 按鈕。

	![Web App Settings][settings-button]

5. 從 [設定] 刀鋒視窗中選取 [應用程式設定]，然後捲動至 [應用程式設定] 區段。
6. 在 [應用程式設定] 區段中，建立 **PHP_EXTENSIONS** 索引鍵。此索引鍵的值是相對於網站根目錄的路徑：**bin\your-ext-file**。

	![Enable extension in app settings][php-extensions]

7. 按一下 [Web 應用程式設定] 刀鋒視窗頂端的 [儲存] 按鈕。

	![Save configuration settings][save-button]

Zend 擴充功能也支援使用 **PHP_ZENDEXTENSIONS** 索引鍵。若要啟用多個擴充功能，請針對應用程式設定值包含以逗號分隔的 `.dll` 檔案清單。

## 作法：使用自訂 PHP 執行階段
除了預設的 PHP 執行階段之外，App Service Web Apps 也可以使用您提供的 PHP 執行階段來執行 PHP 指令碼。您提供的執行階段可以由也是您提供的 `php.ini` 檔案加以設定。若要使用自訂 PHP 執行階段搭配 Web Apps，請依照下列步驟執行。

1. 取得 PHP for Windows 的非安全執行緒 VC9 相容版本。可以在下列網址找到最新版 PHP for Windows：[http://windows.php.net/download/]。在以下的封存可以找到舊版：[http://windows.php.net/downloads/releases/archives/]。
2. 修改您執行階段的 `php.ini` 檔案。請注意， Web Apps 將忽略僅系統層級指示詞的任何組態設定(如需僅系統層級指示詞的資訊，請參閱 [php.ini 指示詞的清單])。
3. 或者，將擴充功能新增至 PHP 執行階段，並且在 `php.ini` 檔案中啟用這些擴充功能。
4. 將 `bin` 目錄新增至根目錄，並在其中放入包含 PHP 執行階段的目錄 (例如， `bin\php`)。
5. 部署 Web 應用程式。
4. 在 Azure 入口網站中瀏覽至您的 Web 應用程式，然後按一下 [設定] 按鈕。

	![Web App Settings][settings-button]

7. 從 [設定] 刀鋒視窗中選取 [應用程式設定]，然後捲動至 [處理常式對應] 區段。將 '*.php' 新增至 [副檔名] 欄位，然後將路徑加入 `php-cgi.exe` 可執行檔。如果您將 PHP 執行階段放入應用程式根目錄內的  `bin` 目錄中，該路徑將是 `D:\home\site\wwwroot\bin\php\php-cgi.exe`。

	![Specify handler in hander mappings][handler-mappings]

8. 按一下 [Web 應用程式設定] 刀鋒視窗頂端的 [儲存] 按鈕。

	![Save configuration settings][save-button]

>[AZURE.NOTE] 如果您想要在註冊 Azure 帳戶之前開始使用 Azure App Service，請移至[試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751)，讓您能夠在 App Service 中立即建立短期的入門 Web 應用程式。不需要信用卡，無需承諾。

## 相關變更
* 如需從網站變更為 App Service 的指南，請參閱：[Azure App Service 及其對現有 Azure 服務的影響](http://go.microsoft.com/fwlink/?LinkId=529714)
* 如需從舊版入口網站變更為新版入口網站的指南，請參閱：[瀏覽預覽入口網站的參考](http://go.microsoft.com/fwlink/?LinkId=529715)

[免費試用]: https://www.windowsazure.com/pricing/free-trial/
[phpinfo()]: http://php.net/manual/en/function.phpinfo.php
[select-php-version]: ./media/web-sites-php-configure/select-php-version.png
[php.ini 指示詞的清單]: http://www.php.net/manual/en/ini.list.php
[.user.ini]: http://www.php.net/manual/en/configuration.file.per-user.php
[ini_set()]: http://www.php.net/manual/en/function.ini-set.php
[application-settings]: ./media/web-sites-php-configure/application-settings.png
[settings-button]: ./media/web-sites-php-configure/settings-button.png
[save-button]: ./media/web-sites-php-configure/save-button.png
[php-extensions]: ./media/web-sites-php-configure/php-extensions.png
[handler-mappings]: ./media/web-sites-php-configure/handler-mappings.png
[http://windows.php.net/download/]: http://windows.php.net/download/
[http://windows.php.net/downloads/releases/archives/]: http://windows.php.net/downloads/releases/archives/

<!--HONumber=49-->