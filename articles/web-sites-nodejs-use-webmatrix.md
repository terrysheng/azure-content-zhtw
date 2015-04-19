<properties 
	pageTitle="使用 WebMatrix 的 Node.js 網站 - Azure 教學課程" 
	description="指導如何使用 WebMatrix 開發 Node.js 應用程式並部署至 Azure 網站的教學課程。" 
	services="web-sites" 
	documentationCenter="nodejs" 
	authors="blackmist" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="09/17/2014" 
	ms.author="larryfr"/>


# 使用 WebMatrix 來建立 Node.js 網站並部署至 Azure

本教學課程說明如何使用 WebMatrix 來開發 Node.js 應用程式並部署至 Azure 網站。WebMatrix 是 Microsoft 提供的免費 Web 開發工具，包含網站開發所需的一切。WebMatrix 包含一些功能讓您輕鬆地使用 Node.js，包括程式碼自動完成、預先建立的範本，以及 Jade、LESS 和 CoffeeScript 的編輯器支援。深入了解 [WebMatrix for Azure](http://go.microsoft.com/fwlink/?LinkID=253622&clcid=0x409)。

完成本指南後，您將會有一個在 Azure 中執行的 Node.js 網站。
 
完成之應用程式的螢幕擷取畫面如下：

![Azure node Web site][webmatrix-node-completed]

[AZURE.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

## 登入 Azure

遵循下列步驟以建立 Azure 網站。

> [AZURE.NOTE] 若要完成此教學課程，您需要已啟用 Azure 網站功能的 Azure 帳戶。<br />如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 [Azure 免費試用](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A7171371E"%20target="_blank")。
<br />

1. 啟動 WebMatrix
2. 如果這是您第一次使用 WebMatrix，則會提示您登入 Azure。否則，您可以按一下 [登入]**** 按鈕，然後選擇 [新增帳戶]****。選擇使用您的 Microsoft 帳戶來 [登入]****。

	![Add Account][addaccount]

3. 如果您已註冊 Azure 帳戶，則可使用您的 Microsoft 帳戶登入：

	![Sign into Azure][signin]	


## 使用 Azure 內建的範本建立網站

1. 在開始畫面上，按一下 [新增]**** 按鈕，然後選擇 [範本庫]****，從範本庫建立新的網站：

	![New site from Template Gallery][sitefromtemplate]

2. 在 [依據範本的站台]**** 對話方塊中，選取 [節點]****，再選取 [Express 網站]****。最後，按一下 [下一步]****。如果您遺漏 [Express 網站]**** 範本的任何先決條件，則會提示您安裝。

	![select express template][webmatrix-templates]

3. 如果您已登入 Azure，則現在可選擇建立 Azure 網站作為您的本機網站。請選擇唯一名稱，並選取您想在其中建立網站的資料中心。 

	![Create site on Azure][nodesitefromtemplateazure]
	
4. 當 WebMatrix 完成建立網站之後，就會顯示 WebMatrix IDE。

	![webmatrix ide][webmatrix-ide]

##將應用程式發行至 Azure

1. 在 WebMatrix 中，從 [首頁]**** 功能區按一下 [發行]****，以顯示網站的 [發行預覽]**** 對話方塊。

	![publish preview][webmatrix-node-publishpreview]

2. 按一下 [繼續]****。發行完成時，網站在 Azure 上的 URL 會顯示在 WebMatrix IDE 底部

	![publish complete][webmatrix-publish-complete]

3. 按一下連結以在瀏覽器中開啟網站。

	![Express web site][webmatrix-node-express-site]

##修改和重新發行應用程式

您可以輕鬆地修改和重新發行應用程式。以下，您將在 **index.jade** 檔案中簡單地變更標題，然後重新發行應用程式。

1. 在 WebMatrix 中，選取 [檔案]****，然後展開 [views]**** 資料夾。按兩下以開啟 **index.jade** 檔案。

	![webmatrix viewing index.jade][webmatrix-modify-index]

2. 將第二行變成：

		p Welcome to #{title} with WebMatrix on Azure!

3. 儲存您所做的變更，然後按一下發行圖示。最後，在 [發行預覽]**** 對話方塊中按一下 [繼續]****，等待發行更新。

	![publish preview][webmatrix-republish]

4. 發行完成時，使用發行程序完成時所傳回的連結，來查看更新後的網站。

	![Azure node Web site][webmatrix-node-completed]

##後續步驟

若要深入了解 Azure 隨附之 Node.js 的版本相關資訊，以及如何指定要與您的應用程式搭配使用的版本，請參閱[在 Azure 應用程式中指定 Node.js 版本](nodejs-specify-node-version-azure-apps.md).

如果您在將應用程式部署到 Azure 後遇到問題，請參閱[如何在 Azure 網站中對 Node.js 應用程式進行偵錯](http://azure.microsoft.com/develop/nodejs/how-to-guides/Debug-Website/)以取得診斷問題的詳細資訊。


[Azure 管理入口網站]: http://manage.windowsazure.com
[WebMatrix 網站]: http://www.microsoft.com/click/services/Redirect2.ashx?CR_CC=200106398
[WebMatrix for Azure]: http://go.microsoft.com/fwlink/?LinkID=253622&clcid=0x409

[使用 Git 發佈 Azure 網站]: /zh-tw/develop/nodejs/common-tasks/publishing-with-git/
[免費]: /zh-tw/pricing/free-trial
[webmatrix-node-completed]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-node-complete.png



[webmatrix-templates]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-templates.png







[webmatrix-node-publishpreview]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-publishpreview.png



[webmatrix-ide]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-ide.png
[webmatrix-publish-complete]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-publish-complete.png
[webmatrix-node-express-site]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-express-webiste.png
[webmatrix-modify-index]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-node-edit.png
[webmatrix-republish]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-republish.png
[addaccount]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-add-account.png
[signin]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-sign-in.png
[sitefromtemplate]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-site-from-template.png
[nodesitefromtemplateazure]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-node-site-azure.png


<!--HONumber=42-->
