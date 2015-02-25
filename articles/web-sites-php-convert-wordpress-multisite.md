<properties 
	pageTitle="將 WordPress 網站轉換成多網站" 
	description="了解如何將透過 Azure 的組件庫所建立的現有 WordPress 網站轉換成 WordPress 多網站。" 
	services="web-sites" 
	documentationCenter="php" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="10/28/2014" 
	ms.author="tomfitz"/>





# 將 WordPress 網站轉換成多網站

*作者：[Ben Lobaugh][ben-lobaugh]，[Microsoft Open Technologies Inc.][ms-open-tech]*

本教學課程中，您將了解如何將透過 Azure 的組件庫所建立的現有 WordPress 網站，轉換成 WordPress 多網站安裝。此外，您也將了解如何將自訂網域指派給安裝內的每一個子網站。

本文假設您目前已安裝 WordPress。如果沒有，請依照[從 Azure 中的組件庫建立 WordPress 網站][website-from-gallery]所提供的指導方針進行。

將現有的 WordPress 單一網站安裝轉換成多網站通常相當簡單，以下許多初始步驟都直接取材自 [WordPress Codex](http://codex.wordpress.org) 的[建立網站][wordpress-codex-create-a-network]頁面。

讓我們開始吧！

## 允許多網站

您需要先在 `wp-config.php` 檔案中使用 **WP\_ALLOW\_MULTISITE** 常數來啟用多網站。有兩種方法可以編輯您的網站檔案：第一種是透過 FTP，第二種是透過 Git。如果不熟悉如何設定這些方法，請參閱下列教學課程：

* [PHP 網站與 MySQL 和 FTP][website-w-mysql-and-ftp-ftp-setup]

* [PHP 網站與 MySQL 和 Git][website-w-mysql-and-git-git-setup]

使用您選擇的編輯器開啟 `wp-config.php` 檔案，然後在 `/* That's all, stop editing! Happy blogging. */` 那一行上方新增下列程式碼。

	/* Multisite */

	define( 'WP_ALLOW_MULTISITE', true );

記得儲存檔並重新傳送至伺服器！

## 網路設定

登入網站的 *wp-admin* 區域，在 [Tools] **** (工具) 功能表下應該會出現名為 [Network Setup]**** (網路設定) 的新項目。按一下 [Network Setup]**** (網路設定)，並填寫網路的詳細資料。

![Network Setup Screen][wordpress-network-setup]

本教學課程採用 *Sub-directories*網站結構描述，因為這總是沒有問題，我們將於教學課程稍後設定每一個子網站的自訂網域。不過，只要透過入口網站來對應網域，並適當地設定萬用字元 DNS，就能夠設定子網域安裝。

如需子網域和子目錄設定兩者的詳細資訊，請參閱 WordPress Codex 上的[多網站網路的類型][wordpress-codex-types-of-networks]文章。

## 啟用網路

資料庫中現在已設定網路，還差一步就能啟用網路功能。請完成 `wp-config.php` 設定，並確定 `web.config` 可適當地路由傳送每一個網站。


在 [ *Network Setup*] (網路設定) 頁面按一下 [Install]**** (安裝) 按鈕後，WordPress 將嘗試更新 `wp-config.php` 和 `web.config` 檔案。不過，一定要檢查檔案，以確定更新成功。如果失敗，此畫面會顯示必要的更新。編輯並儲存檔案。


完成這些更新之後，您必須登出再重新登入 wp-admin 儀表板。

管理列上現在應該又會多一個標示為 [My Sites]**** (我的網站) 的功能表。此功能表可讓您透過 [Network Admin]**** (網路管理) 儀表板來控制新的網路。

# 新增自訂網域

[WordPress MU Domain Mapping][wordpress-plugin-wordpress-mu-domain-mapping] 外掛程式可讓您輕鬆地將自訂網域加入至網路中的任何網站。為了讓外掛程式正常運作，您需要在入口網站上執行其他一些設定，也需要在網域註冊機構上這樣做。

## 允許將網域對應至網站

預設的免費網站模式不支援將自訂網域加入至 Azure 網站。您需要切換至共用或標準模式。作法：

* 登入 Azure 入口網站，找出您的網站。 
* 按一下主內容區域的 [Scale]**** (調整模規) 索引標籤
* 在 [General]**** (一般) 下，選取 [ *SHARED*] (共用) 或 [ *STANDARD*] (標準)。
* 按一下 [Save]**** (儲存)。

可能會出現訊息要求您確認變更並認可網站現在會引發成本，視使用量和您設定的其他組態而定。

需要一些時間來處理新的設定，這段時間剛好可拿來開始設定您的網域。

## 驗證網域

必須先確認您有權限來對應網域，Azure 網站才允許您將網域對應至網站。若要這樣做，您必須將新的 CNAME 記錄加入至 DNS 項目。

* 登入網域的 DNS 管理員
* 建立新的 CNAME *awverify*
* 將 *awverify* 指向 *awverify.YOUR_DOMAIN.azurewebsites.net*

DNS 變更需要一些時間才會完全生效，如果無法立即執行下列步驟，請休息一下，稍後再回來重試。

## 將網域加入至網站

透過 Azure 入口網站回到您的網站，這次請按一下 [CONFIGURE]**** (設定) 索引標籤。應該會可以使用 [MANAGE DOMAINS]**** (管理網域) 按鈕。按一下此按鈕。

將顯示 [ *Manage custom domains*] (管理自訂網域) 對話方塊。請在這裡輸入想要指派給網站的所有網域。如果其中未列出某個網域，就表示不論網域 DNS 如何設定，都無法在 WordPress 內對應此網域。

![管理自訂網域對話方塊][wordpress-manage-domains]

在文字方塊中輸入網域之後，Azure 會驗證您先前建立的 *awverify* CNAME 記錄。如果 DNS 未完整傳播，則會出現紅色指標。如果成功，則會出現綠色核取記號。 

請記下對話方塊底部列出的 IP 位址。設定網域的 A 記錄時需要此資訊。

## 設定網域 A 記錄

如果其他步驟成功，則現在可以透過 DNS A 記錄將網域指派給 Azure 網站。 

在此必須注意，Azure 網站接受 CNAME 與 A 記錄，但您 *must*使用 A 記錄才能適當地對應網域。CNAME 無法轉送至另一個 CNAME，即 Azure 以 YOUR_DOMAIN.azurewebsites.net 為您建立的記錄。

回到 DNS 管理員，設定 A 記錄來指向您在上一步記下的 IP 位址。


## 安裝和設定外掛程式

WordPress 多網站目前沒有內建的方法可對應自訂網域。不過，有一個稱為 [WordPress MU Domain Mapping][wordpress-plugin-wordpress-mu-domain-mapping] 的外掛程式可為您增加此功能。請登入網站的 [Network Admin] (網路管理) 部分，並安裝 **WordPress MU Domain Mapping** 外掛程式。

安裝並啟動此外掛程式之後，請移至 [Settings]**** (設定) > [Domain Mapping]**** (網域對應) 來設定外掛程式。在第一個文字方塊 [ *Server IP Address*] (伺服器 IP 位址) 中，輸入您用來設定網域 A 記錄的 IP 位址。設定您要的任何 [ *Domain Options*] (網域選項) (預設值通常就很適合)，然後按一下 [Save]**** (儲存)。

## 對應網域

移至要讓網域對應到的網站的 [Dashboard]**** (儀表板)。按一下 [Tools]**** (工具) > [Domain Mapping]**** (網域對應)，在文字方塊中輸入新的網域，然後按一下 [Add]**** (新增)。

依預設，新的網域會改寫為自動產生的網站網域。如果要讓所有流量都傳送至新網域，請在儲存之前選取 [ *Primary domain for this blog*] (此部落格的主網域) 方塊。可加入至網站的網域數量不限，但只能有一個主網域。

## 再做一次

Azure 網站可讓您將不限數量的網域加入至網站。若要加入其他網域，您需要對每一個網域執行＜驗證網域＞****和＜設定網域 A 記錄＞****兩節。	

[ben-lobaugh]: http://ben.lobaugh.net
[ms-open-tech]: http://msopentech.com
[website-from-gallery]: https://www.windowsazure.com/zh-tw/develop/php/tutorials/website-from-gallery/
[wordpress-codex-create-a-network]: http://codex.wordpress.org/Create_A_Network
[website-w-mysql-and-ftp-ftp-setup]: https://www.windowsazure.com/zh-tw/develop/php/tutorials/website-w-mysql-and-ftp/#header-0
[website-w-mysql-and-git-git-setup]: https://www.windowsazure.com/zh-tw/develop/php/tutorials/website-w-mysql-and-git/#header-1
[wordpress-network-setup]: ./media/web-sites-php-convert-wordpress-multisite/wordpress-network-setup.png
[wordpress-codex-types-of-networks]: http://codex.wordpress.org/Before_You_Create_A_Network#Types_of_multisite_network
[wordpress-plugin-wordpress-mu-domain-mapping]: http://wordpress.org/extend/plugins/wordpress-mu-domain-mapping/

[wordpress-manage-domains]: ./media/web-sites-php-convert-wordpress-multisite/wordpress-manage-domains.png



<!--HONumber=42-->
