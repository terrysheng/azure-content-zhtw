<properties urlDisplayName="How to create" pageTitle="如何建立網站 - Azure 服務管理" metaKeywords="Azure creating website, Azure deleting website" description="了解如何使用 Azure Management Portal 建立網站。" metaCanonical="" services="web-sites" documentationCenter="" title="How to Create and Deploy a Website" authors="cephalin" solutions="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/24/2014" ms.author="cephalin" />

#如何建立網站

本主題說明如何從組件庫或使用管理入口網站來建立網站。

如需關於如何將內容部署到已建立之 Azure 網站的詳細資訊，請參閱 **[Azure 網站**] 中的 [[部署]] 一節(/zh-tw/documentation/services/web-sites/)。

## 目錄 ##

- [作法：使用管理入口網站來建立網站](#createawebsiteportal)
- [作法：從藝廊建立網站](#howtocreatefromgallery)
- [作法：刪除網站](#deleteawebsite)
- [後續步驟](#nextsteps)

##<a name="createawebsiteportal"></a>作法：使用管理入口網站來建立網站

執行下列步驟在 Azure 中建立網站。
	
1. 登入 [Azure 管理入口網站](http://manage.windowsazure.com/)。

2. 按一下 [管理入口網站] 左下角的 [**新建**] 圖示。

3. 依序按一下 [**網站**] 圖示和 [**快速建立**] 圖示，輸入 URL 的值，然後按一下頁面右下角的 [**建立網站**] 旁的勾選記號。

4. 建立網站後，您會看到「**建立網站 <*website name*> 成功**」字樣。按一下入口網站頁面底部的 [**瀏覽**] 即可瀏覽到該網站。

5. 在入口網站中，按一下網站清單中顯示的網站名稱，以開啟網站的 [**快速入門**] 管理頁面。

6. [**快速入門**] 頁面上提供各種選項，讓您取得網站開發工具、設定網站的發行，或從 TFS 或 Git 之類的原始檔控制提供者設定部署。依預設會為網站設定 FTP 發行，並在 [**儀表板**] 頁面之 [**快速概覽**] 區段的 [**FTP 主機名稱**] 下顯示 FTP 主機名稱。使用 FTP 或 Git 進行發行前，請選擇 [**儀表板**] 頁面的 [**重設部署認證**] 選項，以便能在將內容部署到網站時，根據 FTP 主機或 Git 儲存機制進行驗證。

7. [**設定**] 管理頁面會公開您的網站設定，例如：

	- Web 應用程式的 .NET Framework 或 PHP 版本
	- SSL 繫結
	- 自訂網域名稱
	- 記錄選項
	- Azure 環境的應用程式設定 (例如，在您開發環境的 Web.config 中覆寫 <appSettings>)
	- 連接字串 (例如，在您開發環境的 Web.config 中覆寫 <connectionStrings>)
	- 特定副檔名的指令碼處理器，例如 *.php

##<a name="howtocreatefromgallery"></a>作法：從組件庫建立網站

[WACOM.INCLUDE [website-from-gallery](../includes/website-from-gallery.md)]

##<a name="deleteawebsite"></a>作法：刪除網站
您可以使用 Azure 管理入口網站中的 [**刪除**] 圖示來刪除網站。在 Azure 入口網站中，當您按一下 [**網站**] 列出所有網站時會看見 [**刪除**] 圖示，但此圖示也會顯示在每個網站管理頁面的底部。

##<a name="nextsteps"></a>後續步驟

如需詳細資訊，請參閱 [Azure 網站](/zh-tw/documentation/services/web-sites/)。


<!--HONumber=35.1-->
