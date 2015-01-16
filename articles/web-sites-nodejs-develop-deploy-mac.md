<properties urlDisplayName="Website" pageTitle="在 Mac 上建立 Node.js 網站 - Azure 教學課程" metaKeywords ="Azure 建立網站 Node, Azure 部署網站 Node, Node 網站" description="了解如何在 Azure 中建立和部署 Node.js 網站。程式碼範例以 Java 撰寫。" metaCanonical="" services="web-sites" documentationCenter="nodejs" title="Build and deploy a Node.js website to Azure" authors="larryfr" solutions="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr" />






# 建置並部署 Node.js 網站至 Azure

本教學課程說明如何建立 [Node] [nodejs.org] 應用程式，並使用 [Git] 將其部署至 Azure 網站。本教學課程中的指示可運用在任何足以執行 Node 應用程式的作業系統上。

如果您偏好觀看影片，右側的短片會執行與本教學課程相同的步驟。
 
完成之應用程式的螢幕擷取畫面如下：

![A browser displaying the 'Hello World' message.][helloworld-completed]

##建立 Azure 網站並啟用 Git 發行

請遵循以下步驟來建立 Azure 網站，然後為該網站啟用 Git 發行功能。

<div class="dev-callout"><strong>注意</strong>
<p> 若要完成此教學課程，您需要 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 <a href="http://www.windowsazure.com/zh-tw/pricing/free-trial/?WT.mc_id=A7171371E" target="_blank">Azure 免費試用</a>。</p>
</div>

1. 登入 [Azure 管理入口網站]。

2. 按一下入口網站左下方的 [**+新增**]

    ![The Azure Portal with the +NEW link highlighted.][portal-new-website]

3. 按一下 [**網站**]，然後按一下 [**快速建立**]。輸入 **URL** 值，然後在 [**區域**] 下拉式清單中為您的網站選取資料中心。按一下對話方塊底部的核取記號。

    ![The Quick Create dialog][portal-quick-create]

4. 一旦網站狀態變更為 [**執行中**]，按一下網站名稱可存取 [**儀表板**]

	![Open web site dashboard][go-to-dashboard]

6. 在 [快速啟動] 頁面右下方，選取 [**Set up a deployment from source control**]。

	![Set up Git publishing][setup-git-publishing]

6. 當系統詢問您「您的來源程式碼在哪裡？」時，選取 [**Local Git repository**]，然後按一下該箭頭。

	![where is your source code][where-is-code]

7. 若要啟用 Git 發佈，您必須提供使用者名稱和密碼。如果您先前已經為 Azure 網站啟用了發行功能，則系統不會提示您輸入使用者名稱或密碼。而是會使用您先前指定的使用者名稱與密碼來建立 Git 儲存機制。請記下使用者名稱與密碼，因為您將為您所建立的所有 Azure 網站使用該資訊進行 Git 發行。

	![The dialog prompting for user name and password.][portal-git-username-password]

8. 一旦 Git 儲存機制就緒，系統會顯示 Git 命令的使用指示，以便設定本機儲存機制並將檔案發送至 Azure。

	![Git deployment instructions returned after creating a repository for the website.][git-instructions]

##在本機建置與測試您的應用程式

在本節中，您將建立 **server.js** 檔案，其中內含來自 [nodejs.org] (英文) 的 'hello world' 範例。此範例已自原始範例加以修改，將 process.env.PORT 加入為於 Azure 網站執行時要接聽的連接埠。

1. 使用文字編輯器，在 **helloworld** 目錄中建立名為 **server.js** 的新檔案。如果 **helloworld** 目錄不存在，請建立一個。
2. 新增下列作為 **server.js** 檔案的內容，然後加以儲存：

        var http = require('http')
        var port = process.env.PORT || 1337;
        http.createServer(function(req, res) {
          res.writeHead(200, { 'Content-Type': 'text/plain' });
          res.end('Hello World\n');
        }).listen(port);

3. 開啟命令列，然後使用以下命令在本機啟動網頁：

        node server.js

4. 開啟您的網頁瀏覽器並瀏覽至 http://localhost:1337。將顯示內含「Hello World」字樣的網頁，如以下螢幕擷取畫面所示：

    ![A browser displaying the 'Hello World' message.][helloworld-localhost]

##發行您的應用程式

1. 從命令列中，將目錄變更至 **helloworld** 目錄，然後輸入以下命令以初始化本機 Git 儲存機制。 

		git init

	<div class="dev-callout"><strong>Git 命令無法使用？</strong>
	<p><a href="http://git-scm.com/" target="_blank">Git</a> 是一個您可用來部署 Azure 網站的分散式版本控制系統。如需您的平台適用的安裝指示，請參閱 <a href="http://git-scm.com/download" target="_blank">Git 下載頁面</a>。</p>
	</div>

2. 使用下列命令將檔案新增至儲存機制：

		git add .
		git commit -m "initial commit"

3. 使用下列命令，加入 Git remote 以將更新發送至您先前建立的 Azure 網站：

		git remote add azure [URL for remote repository]

    ![Git deployment instructions returned after creating a repository for the web site.][git-instructions]
 
4. 使用下列命令將您的變更發送至 Azure：

		git push azure master

	系統會提示您輸入先前建立的密碼，您會看到下列輸出：

		Password for 'testsite.scm.azurewebsites.net':
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
    
	如果您瀏覽至管理入口網站內的 Azure 網站部署索引標籤，會在部署歷程記錄中看到您的第一次部署：

	![Git deployment status on the portal][git-deployments-first] 

5. 在管理入口網站內，使用 Azure 網站頁面上的 [**瀏覽**] 按鈕瀏覽至您的網站。

##將變更發行至您的應用程式

1. 在文字編輯器中開啟 **server.js** 檔案，然後將 'Hello World\n' 變更為 'Hello Azure\n'。儲存檔案。
2. 從命令列中，將目錄位置變更至 **helloworld** 目錄，然後執行下列命令：

		git add .
		git commit -m "changing to hello azure"
		git push azure master

	系統會提示您輸入先前建立的密碼。如果您瀏覽至管理入口網站內的 Azure 網站部署索引標籤，會看到更新的部署歷程記錄：
	
	![Git deployment status updated on the portal][git-deployments-second]

3. 使用 [**瀏覽**] 按鈕瀏覽至您的網站，並注意更新已經套用。

	![A web page displaying 'Hello Azure'][helloworld-completed]

4. 您可以在管理入口網站內，選取 Azure 網站的 [部署] 索引標籤，然後使用 [**重新部署**] 按鈕回復至先前的部署狀態。

##後續步驟

雖然本文內的步驟使用 Azure 入口網站來建立網站，但是您也可以使用[適用於 Mac 與 Linux 的 Azure 命令列工具]來執行相同的操作。

Node.js 提供您豐富的模組生態系統，可供您的應用程式使用。若要了解 Azure 網站如何與模組搭配使用，請參閱[使用 Node.js 模組與 Azure 應用程式搭配](/zh-tw/documentation/articles/nodejs-use-node-modules-azure-apps/)。

若要深入了解 Azure 隨附的 Node.js 版本，以及如何指定要與您的應用程式搭配使用的版本，請參閱[在 Azure 應用程式中指定 Node.js 版本](/zh-tw/documentation/articles/nodejs-specify-node-version-azure-apps/)。

如果您在將應用程式部署到 Azure 後遇到問題，請參閱[如何在 Azure 網站中對 Node.js 應用程式進行偵錯](/zh-tw/documentation/articles/web-sites-nodejs-debug/) (英文) 以獲得診斷問題的詳細資訊。


##其他資源

* [Azure PowerShell]
* [適用於 Mac 和 Linux 的 Azure 命令列工具]

[Azure PowerShell]: /zh-tw/documentation/articles/install-configure-powershell/

[nodejs.org]: http://nodejs.org
[Git]: http://git-scm.com

[Azure 管理入口網站]: http://manage.windowsazure.com
[適用於 Mac 和 Linux 的 Azure 命令列工具]: /zh-tw/documentation/articles/xplat-cli/

[helloworld-completed]: ./media/web-sites-nodejs-develop-deploy-mac/helloazure.png
[helloworld-localhost]: ./media/web-sites-nodejs-develop-deploy-mac/helloworldlocal.png
[portal-new-website]: ./media/web-sites-nodejs-develop-deploy-mac/plus-new.png
[portal-quick-create]: ./media/web-sites-nodejs-develop-deploy-mac/create-quick-website.png

[portal-git-username-password]: ./media/web-sites-nodejs-develop-deploy-mac/git-deployment-credentials.png
[git-instructions]: ./media/web-sites-nodejs-develop-deploy-mac/git-instructions.png

[git-deployments-first]: ./media/web-sites-nodejs-develop-deploy-mac/git_deployments_first.png
[git-deployments-second]: ./media/web-sites-nodejs-develop-deploy-mac/git_deployments_second.png

[setup-git-publishing]: ./media/web-sites-nodejs-develop-deploy-mac/setup_git_publishing.png
[go-to-dashboard]: ./media/web-sites-nodejs-develop-deploy-mac/go_to_dashboard.png
[where-is-code]: ./media/web-sites-nodejs-develop-deploy-mac/where_is_code.png
