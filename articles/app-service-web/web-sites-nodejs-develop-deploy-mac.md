<properties
	pageTitle="在 Azure App Service 中建立 Node.js Web App | Microsoft Azure"
	description="了解如何將 Node.js 應用程式部署到 Azure App Service 中的 Web 應用程式。"
	services="app-service\web"
	documentationCenter="nodejs"
	authors="MikeWasson"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="nodejs"
	ms.topic="hero-article"
	ms.date="10/13/2015"
	ms.author="mwasson"/>

# 在 Azure App Service 中建立 Node.js Web 應用程式

> [AZURE.SELECTOR]
- [.Net](web-sites-dotnet-get-started.md)
- [Node.js](web-sites-nodejs-develop-deploy-mac.md)
- [Java](web-sites-java-get-started.md)
- [PHP - Git](web-sites-php-mysql-deploy-use-git.md)
- [PHP - FTP](web-sites-php-mysql-deploy-use-ftp.md)
- [Python](web-sites-python-ptvs-django-mysql.md)

本教學課程顯示如何建立簡單的 [Node.js](http://nodejs.org) 應用程式，並使用 [Git](http://git-scm.com) 將其部署至 [Azure App Service](../app-service/app-service-value-prop-what-is.md) 中的 [Web 應用程式](app-service-web-overview.md)。本教學課程中的指示可運用在任何足以執行 Node.js 應用程式的作業系統上。

您將了解：

* 如何使用 Azure Preview 入口網站在 Azure App Service 中建立 Web 應用程式
* 如何藉由推送至 Web 應用程式的 Git 儲存機制，將 Node.js 應用程式部署到 Web 應用程式。

已完成的應用程式會將簡短的「hello world」字串寫入至瀏覽器。

![顯示「Hello World」訊息的瀏覽器。][helloworld-completed]

如需教學課程，以及具有更複雜的 Node.js 應用程式的範例程式碼，或如需有關如何在 Azure 中使用 Node.js 的其他主題，請參閱 [Node.js 開發人員中心](/develop/nodejs/)。

> [AZURE.NOTE]若要完成此教學課程，您需要 Microsoft Azure 帳戶。如果您沒有這類帳戶，可以[啟用自己的 MSDN 訂戶權益](/zh-TW/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)或是[申請免費試用](/zh-TW/pricing/free-trial/?WT.mc_id=A261C142F)。
>
> 如果您想要在註冊 Azure 帳戶之前先開始使用 Azure App Service，請移至[試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751)。您可以於該處，在 App Service 中立即建立短期的入門 Web app - 不需信用卡，不需任何承諾。

## 建立 Web 應用程式並啟用 Git 發佈

請遵循下列步驟，在 Azure App Service 中建立 Web 應用程式，並啟用 Git 發佈。

[Git](http://git-scm.com/%20target="_blank) 是一個您可用來部署 Azure 網站的分散式版本控制系統。您將會在本機 Git 儲存機制中儲存您為 Web 應用程式撰寫的程式碼，並藉由發送至遠端儲存機制，將您的程式碼部署至 Azure。此部署方法是 App Service Web Apps 的功能。

1. 登入 [AAzure Preview 入口網站](https://portal.azure.com)。

2. 按一下入口網站左上方的 [+新增] 圖示。

3. 按一下 [Web + 行動]，然後按一下 [Web 應用程式]。

    ![][portal-quick-create]

4. 在 [Web 應用程式] 方塊中，輸入 Web 應用程式的名稱。

	此名稱在 azurewebsites.net 網域中必須是唯一的，因為 Web 應用程式的 URL 將是 {name}.azurewebsites.net。如果您輸入的名稱不是唯一的，紅色驚嘆號會出現在文字方塊中。

5. 選取 [資源群組] 或建立新的資源群組。

	如需資源群組的詳細資訊，請參閱[使用 Azure Preview 入口網站管理您的 Azure 資源](../resource-group-portal.md)。

5. 選取 [App Service 方案/位置]，或建立新的 App Service 方案/位置。

	如需 App Service 方案的詳細資訊，請參閱 [Azure App Service 方案概觀](../azure-web-sites-web-hosting-plans-in-depth-overview.md)

6. 按一下 [建立]。
   
	![][portal-quick-create2]

	Azure 很快 (通常不到一分鐘) 就會完成建立新的 Web 應用程式。

7. 按一下 [Web 應用程式] > [{新的 Web 應用程式}]。

	![](./media/web-sites-nodejs-develop-deploy-mac/gotowebapp.png)

8. 在 [Web 應用程式] 刀鋒視窗中，按一下 [部署] 組件。

	![][deployment-part]

9. 在 [持續部署] 刀鋒視窗，按一下 [選擇來源]

14. 按一下 [本機 Git 儲存機制]，然後按一下 [確定]。

	![][setup-git-publishing]

11. 如果您還沒有設定部署認證，請設定之。

	a.在 [Web 應用程式] 刀鋒視窗中，按一下 [設定] > [部署認證]。

	![][deployment-credentials]
 
	b.建立使用者名稱和密碼。
	
	![](./media/web-sites-nodejs-develop-deploy-mac/setdeploycreds.png)

11. 在 [Web 應用程式] 刀鋒視窗中，按一下 [設定]，然後按一下 [內容]。
 
	若要發佈，您將推送至遠端 Git 儲存機制。儲存機制的 URL 列示在 **GIT URL** 下方。您稍後將在教學課程中使用此 URL。

	![][git-url]

## 在本機建置與測試您的應用程式

在本節中，您將建立 **server.js** 檔案，其中包含略為修改的「Hello World」範例版本 (來自 [nodejs.org])。程式碼會新增 process.env.PORT，做為在 Azure Web 應用程式中執行時要接聽的連接埠。

1. 建立名為 *helloworld* 的目錄。

2. 使用文字編輯器，在 **helloworld** 目錄中建立名為 *server.js* 的新檔案。

2. 將下列程式碼複製至 **server.js** 檔案，然後儲存該檔案：

        var http = require('http')
        var port = process.env.PORT || 1337;
        http.createServer(function(req, res) {
          res.writeHead(200, { 'Content-Type': 'text/plain' });
          res.end('Hello World\n');
        }).listen(port);

3. 開啟命令列，然後使用下列命令，在本機啟動 Web app。

        node server.js

4. 開啟您的 Web 瀏覽器並瀏覽至 http://localhost:1337。

	顯示「Hello World」的網頁隨即出現，如下列螢幕擷取畫面所示：

    ![顯示「Hello World」訊息的瀏覽器。][helloworld-localhost]

## 發行您的應用程式

1. 如果尚未安裝l Git，請安裝。

	如需平台適用的安裝指示，請參閱 [Git 下載頁面](http://git-scm.com/download)。

1. 從命令列將目錄位置變更至 **helloworld** 目錄，然後輸入以下命令來初始化本機 Git 儲存機制。

		git init


2. 使用下列命令將檔案新增至儲存機制：

		git add .
		git commit -m "initial commit"

3. 使用下列命令來新增 Git remote，將更新推送至您先前建立的 Web app：

		git remote add azure [URL for remote repository]

4. 使用下列命令，將您的變更推送至 Azure：

		git push azure master

	系統會提示您輸入先前建立的密碼。輸出類似於下列範例：

		Counting objects: 3, done.
		Delta compression using up to 8 threads.
		Compressing objects: 100% (2/2), done.
		Writing objects: 100% (3/3), 374 bytes, done.
		Total 3 (delta 0), reused 0 (delta 0)
		remote: New deployment received.
		remote: Updating branch 'master'.
		remote: Preparing deployment for commit id '5ebbe250c9'.
		remote: Preparing files for deployment.
		remote: Deploying Web.config to enable Node.js activation.
		remote: Deployment successful.
		To https://user@testsite.scm.azurewebsites.net/testsite.git
		 * [new branch]      master -> master

5. 若要檢視您的應用程式，請按一下 Azure 入口網站中 [Web 應用程式] 組件上的 [瀏覽] 按鈕。

	![瀏覽按鈕](./media/web-sites-nodejs-develop-deploy-mac/browsebutton.png)

	![Azure 中的 Hello World](./media/web-sites-nodejs-develop-deploy-mac/helloworldazure.png)

## 將變更發行至您的應用程式

1. 在文字編輯器中開啟 **server.js** 檔案，然後將「Hello World\\n」變更為「Hello Azure\\n」。 

2. 儲存檔案。

2. 從命令列將目錄位置變更至 **helloworld** 目錄，然後執行下列命令：

		git add .
		git commit -m "changing to hello azure"
		git push azure master

	系統會重新提示您輸入密碼。

3. 重新整理您已瀏覽至 Web 應用程式之 URL 的瀏覽器視窗。

	![顯示「Hello Azure」的網頁][helloworld-completed]

## 回復部署

從 [Web 應用程式] 刀鋒視窗中，您可以按一下 [設定] > [持續部署]，以查看 [部署] 刀鋒視窗中的部署歷程記錄。如果您需要回復到先前的部署，您可以選取它，然後按一下 [部署詳細資料] 刀鋒視窗中的 [重新部署]。

## 後續步驟

您已將 Node.js 應用程式部署到 Azure App Service 中的 Web 應用程式。若要深入了解 App Service Web Apps 如何執行 Node.js 應用程式，請參閱 [Azure App Service Web Apps：Node.js](http://blogs.msdn.com/b/silverlining/archive/2012/06/14/windows-azure-websites-node-js.aspx) 和[指定 Azure 應用程式中的 Node.js 版本](../nodejs-specify-node-version-azure-apps.md)。

Node.js 提供您豐富的模組生態系統，可供您的應用程式使用。若要了解 Web Apps 如何與模組搭配使用，請參閱[搭配 Azure 應用程式使用 Node.js 模組](../nodejs-use-node-modules-azure-apps.md)。

如果您在將應用程式部署到 Azure 後遇到問題，請參閱[如何在 Azure App Service 中偵錯 Node.js 應用程式](web-sites-nodejs-debug.md)，以獲得診斷問題的詳細資訊。

這篇文章會使用 Azure 入口網站來建立 Web 應用程式。您也可以使用 [Azure 命令列介面](../xplat-cli-install.md) 或 [Azure PowerShell](../install-configure-powershell.md) 執行相同作業。

如需如何在 Azure 上開發 Node.js 應用程式的詳細資訊，請參閱 [Node.js 開發人員中心](/develop/nodejs/)。

[helloworld-completed]: ./media/web-sites-nodejs-develop-deploy-mac/helloazure.png
[helloworld-localhost]: ./media/web-sites-nodejs-develop-deploy-mac/helloworldlocal.png
[portal-quick-create]: ./media/web-sites-nodejs-develop-deploy-mac/create-quick-website.png
[portal-quick-create2]: ./media/web-sites-nodejs-develop-deploy-mac/create-quick-website2.png
[setup-git-publishing]: ./media/web-sites-nodejs-develop-deploy-mac/setup_git_publishing.png
[go-to-dashboard]: ./media/web-sites-nodejs-develop-deploy-mac/go_to_dashboard.png
[deployment-part]: ./media/web-sites-nodejs-develop-deploy-mac/deployment-part.png
[deployment-credentials]: ./media/web-sites-nodejs-develop-deploy-mac/deployment-credentials.png
[git-url]: ./media/web-sites-nodejs-develop-deploy-mac/git-url.png

<!---HONumber=Oct15_HO3-->