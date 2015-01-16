<properties title="How to Configure PHP in Azure Websites" pageTitle="如何在 Azure 網站中設定 PHP" metaKeywords="Azure, Azure Web Sites, configuration, PHP" description="了解如何設定預設 PHP 安裝或在 Azure 網站中新增自訂 PHP 安裝。" services="Web Sites" documentationCenter="PHP" authors="tomfitz" manager="wpickett" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="11/18/2014" ms.author="tomfitz" />

#如何在 Azure 網站中設定 PHP

本指南將說明您如何在 Azure 網站中設定內建 PHP 執行階段、提供自訂 PHP 執行階段，並且在 Azure 網站中啟用擴充功能。若要使用 Azure 網站，請註冊[免費試用]。若要充分利用本指南，您應該先在 Azure 網站中建立 PHP 網站 (請參閱 [PHP 開發人員中心教學課程])。如需在 Azure 網站中設定網站的一般資訊，請參閱[如何設定網站]。

##目錄

* [什麼是 Azure 網站？](#WhatIs)
* [作法：變更預設 PHP 組態](#ChangeBuiltInPHP)
* [作法：在內建 PHP 執行階段中啟用擴充](#EnableExtDefaultPHP)
* [作法：使用自訂 PHP 執行階段](#UseCustomPHP)
* [後續步驟](#NextSteps)

<h2><a name="WhatIs"></a>什麼是 Azure 網站？</h2>
Azure 網站可以讓您在 Azure 上建立高擴充性網站。您很容易就能夠將網站快速部署至高擴充性雲端環境，讓您在一開始時先建立小規模的網站，往後隨著流量增加時擴充。Azure 網站使用您選擇的語言和開放原始碼應用程式，並支援使用 Git、FTP 和 TFS 進行部署。您很容易就能夠整合其他服務，例如 MySQL、SQL Database、快取、CDN 和儲存。

<h2><a name="ChangeBuiltInPHP"></a>作法：變更內建 PHP 組態</h2>
您建立 Azure 網站時，預設會安裝 PHP 5.4，而且可立即供您使用。若要查看可用的修訂版、其預設組態及啟用的擴充，最好的方法是部署呼叫 [phpinfo()] 函數的指令碼。

PHP 5.5 也可供使用，不過並未預設啟用。若要將它啟用，請依照下列步驟執行：

1. 在 Azure 入口網站中瀏覽至您網站的儀表板，並按一下 [**設定**]。

	![Configure tab on Web Sites dashboard][configure]

1. 按一下 [PHP 5.5]。

	![Select PHP version][select-php-version]

1. 按一下頁面底部的 [**儲存**]。

	![Save configuration settings][save-button]

對於任一個內建 PHP 執行階段，您可以按照下列步驟，變更並非僅系統層級指示詞的任何組態選項。(如需僅系統層級指示詞的資訊，請參閱 [php.ini 指示詞的清單] (英文)。)

1. 將 [.user.ini] f檔案新增至根目錄。
1. 使用在 `php.ini` 檔案中使用的相同語法，將組態設定新增至 `.user.ini` 檔案。例如，如果您要啟動 `display_errors` 設定，並將 `upload_max_filesize` 設定設為 10M，則 `.user.ini` 檔案將包含下列文字：

		; Example Settings
		display_errors=On
		upload_max_filesize=10M

1. 部署應用程式。
1. 重新啟動您的網站。(必須重新啟動，因為 PHP 讀取 `.user.ini` 檔案的頻率是由 `user_ini.cache_ttl` 設定所控制，這是系統層級設定，預設為 300 秒 (5 分鐘)。重新啟動網站將使得 PHP 讀取 `.user.ini` 檔案中的新設定。)

除了使用 `.user.ini` 檔案之外，您也可以在指令碼中使用 [ini_set()] 函數，設定非系統層級指示詞的組態選項。

<h2><a name="EnableExtDefaultPHP"></a>作法：在預設 PHP 執行階段中啟用擴充</h2>
如同上一個小節所述，若要查看預設 PHP 版本、其預設組態及啟用的擴充，最好的方法是部署呼叫 [phpinfo()] 函數的指令碼。若要啟用擴充，請依照下列步驟執行。

1. 將 `bin` 目錄新增至根目錄。
1. 將 `.dll` 擴充檔放入 bin 目錄 (例如，`php_mongo.dll`)。確定擴充與 PHP 預設版本 (截至撰寫時為止，該版本為 PHP 5.4) 相容，而且與 VC9 及非安全執行緒 (nts) 相容。
1. 部署應用程式。
1. 在 Azure 入口網站中瀏覽至您網站的儀表板，並按一下 [**設定**]。

	![Configure tab on Web Sites dashboard][configure]

1. 在 [**應用程式設定**] 區段中，建立 **PHP_EXTENSIONS** 索引鍵和 **bin\your-ext-file** 值。若要啟用多個擴充，請加入逗號區隔的 `.dll` 檔案清單。

	![Enable extension in app settings][app-settings]

1. 按一下頁面底部的 [**儲存**]。

	![Save configuration settings][save-button]

<h2><a name="UseCustomPHP"></a>作法：使用自訂 PHP 執行階段</h2>
除了預設 PHP 執行階段之外，Azure 網站也可以使用您提供的 PHP 執行階段來執行 PHP 指令碼。您指定的執行階段可以由也是您提供的 `php.ini` 檔案加以設定。若要在 Azure 網站中使用自訂 PHP 執行階段，請依照下列步驟執行。

1. 取得 PHP for Windows 的非安全執行緒 VC9 相容版本。可以在下列網址找到最新版 PHP for Windows：[http://windows.php.net/download/]。在以下的封存可以找到舊版：[http://windows.php.net/downloads/releases/archives/]。
1. 為您的執行階段修改 `php.ini` 檔案。請注意，Azure 網站將忽略僅系統層級指示詞的任何組態設定。(如需僅系統層級指示詞的資訊，請參閱 [php.ini 指示詞的清單] (英文)。)
1. 或者，將擴充新增至 PHP 執行階段，並且在 `php.ini` 檔案中啟用這些擴充。
1. 將 `bin` 目錄新增至根目錄，並且在其中放入包含 PHP 執行階段的目錄 (例如，`bin\php`)。
1. 部署應用程式。
1. 在 Azure 入口網站中瀏覽至您網站的儀表板，並按一下 [**設定**]。

	![Configure tab on Web Sites dashboard][configure]

1. 在 [**處理常式對應**] 區段中，將 `*.php` 新增至 EXTENSION，並且新增 `php-cgi.exe` 可執行檔的路徑。如果您將 PHP 執行階段放入應用程式的 bin 根目錄，該路徑將是 'D:\home\site\wwwroot\bin\php\php-cgi.exe'。

	![Specify handler in hander mappings][handler-mappings]

1. 按一下頁面底部的 [**儲存**]。

	![Save configuration settings][save-button]

<h2><a name="NextSteps"></a>後續步驟</h2>
了解如何在 Azure 網站中設定 PHP 之後，請參考下列連結了解如何進行後續動作。

- [在 Azure 中設定、監視和調整網站]
- [下載 Azure SDK for PHP]


[免費試用]: https://www.windowsazure.com/zh-tw/pricing/free-trial/
[PHP 開發人員中心教學課程]: https://www.windowsazure.com/zh-tw/develop/php/tutorials/
[如何設定網站]: https://www.windowsazure.com/zh-tw/manage/services/web-sites/how-to-configure-websites/
[phpinfo()]: http://php.net/manual/en/function.phpinfo.php
[select-php-version]: ./media/web-sites-php-configure/select-php-version.png
[php.ini 指示詞的清單]: http://www.php.net/manual/en/ini.list.php
[.user.ini]: http://www.php.net/manual/en/configuration.file.per-user.php
[ini_set()]: http://www.php.net/manual/en/function.ini-set.php
[configure]: ./media/web-sites-php-configure/configure.png
[app-settings]: ./media/web-sites-php-configure/app-settings.png
[save-button]: ./media/web-sites-php-configure/save-button.png
[http://windows.php.net/download/]: http://windows.php.net/download/
[http://windows.php.net/downloads/releases/archives/]: http://windows.php.net/downloads/releases/archives/
[handler-mappings]: ./media/web-sites-php-configure/handler-mappings.png
[在 Azure 中設定、監視和調整網站]: http://www.windowsazure.com/zh-tw/manage/services/web-sites/
[下載 Azure SDK for PHP]: http://www.windowsazure.com/zh-tw/develop/php/common-tasks/download-php-sdk/

<!--HONumber=35.1-->
