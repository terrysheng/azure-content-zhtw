<properties linkid="dev-nodejs-basic-web-app-with-express" urlDisplayName="Web App with Express" pageTitle="Web App with Express (Node.js) - Azure Tutorial" metaKeywords="Azure Node.js hello world tutorial, Azure Node.js hello world, Azure Node.js Getting Started tutorial, Azure Node.js tutorial, Azure Node.js Express tutorial" description="An tutorial that builds on the cloud service tutorial, and demonstrates how to use the Express module." metaCanonical="" services="cloud-services" documentationCenter="nodejs" title="Build a Node.js web application using Express on an Azure Cloud Service" authors="larryfr" solutions="" manager="" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr" />

# 在 Azure 雲端服務上使用 Express 建立 Node.js Web 應用程式

Node.js 包含核心執行階段的一組基本功能。開發人員在開發 Node.js 應用程式時，通常會使用協力廠商模組來提供更多功能。在本教學課程中，您將使用 [Express][Express] 模組來建立新的應用程式，此模組提供建立 Node.js Web 應用程式的 MVC 架構。

完成之應用程式的螢幕擷取畫面如下：

![A web browser displaying Welcome to Express in Azure][A web browser displaying Welcome to Express in Azure]

## 建立雲端服務專案

請執行下列步驟來建立名為 'expressapp' 的新雲端服務專案：

1.  從 [開始] 功能表 或 [開始畫面] 中，搜尋 **Azure PowerShell**。最後，以滑鼠右鍵按一下 [Azure PowerShell]，然後選取 [以系統管理員身分執行]。

    ![Azure PowerShell icon][Azure PowerShell icon]

    [WACOM.INCLUDE [install-dev-tools](../includes/install-dev-tools.md)]

2.  切換至 **c:\\node** 目錄，然後輸入下列命令來建立名為 **expressapp** 的新方案和名為 **WebRole1** 的 Web 角色：

        PS C:\node> New-AzureServiceProject expressapp
        PS C:\Node\expressapp> Add-AzureNodeWebRole
        PS C:\Node\expressapp> Set-AzureServiceProjectRole WebRole1 node 0.10.21

    > [WACOM.NOTE] 根據預設，**Add-AzureNodeWebRole** 會使用舊版的 Node.js。上述的 **Set-AzureServiceProjectRole** 陳述式會指示 Azure 使用 0.10.21 版的 Node。

## 安裝 Express

1.  發出下列命令來安裝 Express 產生器：

        PS C:\node\expressapp> npm install express-generator -g

    npm 命令的輸出類似下列結果。

    ![Windows PowerShell displaying the output of the npm install express command.][Windows PowerShell displaying the output of the npm install express command.]

2.  切換至 **WebRole1** 目錄，然後使用 express 命令來產生新的應用程式：

        PS C:\node\expressapp\WebRole1> express

    系統會提示您覆寫先前的應用程式。輸入 **y** 或 **yes** 繼續進行。Express 會產生 app.js 檔案和資料夾結構來準備建立應用程式。

    ![The output of the express command][The output of the express command]

3.  若要安裝 package.json 檔案中定義的其他相依性，請輸入下列命令：

        PS C:\node\expressapp\WebRole1> npm install

    ![The output of the npm install command][The output of the npm install command]

4.  使用下列命令，將 **bin/www** 檔案複製到 **server.js**。正因如此，雲端服務便能找到此應用程式的進入點。

        PS C:\node\expressapp\WebRole1> copy bin/www server.js

    此命令完成之後，您應該會在 WebRole1 目錄中擁有 **server.js** 檔案。

5.  修改 **server.js**，從下一行程式碼中移除其中一個 '.' 字元。

        var app = require('../app');

    進行這個修改之後，程式碼行應會以如下方式出現。

        var app = require('./app');

    由於我們已將檔案 (先前為 **bin/www**) 移至與應用程式檔案所需的相同目錄，因此，這是必要變更。完成此變更之後，儲存 **server.js** 檔案。

6.  使用下列命令，在 Windows Azure 模擬器中執行應用程式：

        PS C:\node\expressapp\WebRole1> Start-AzureEmulator -launch

    ![A web page containing welcome to express.][A web page containing welcome to express.]

## 修改檢視

現在，修改檢視來顯示「歡迎在 Azure 中使用 Express」訊息。

1.  輸入下列命令來開啟 index.jade 檔案：

        PS C:\node\expressapp\WebRole1> notepad views/index.jade

    ![The contents of the index.jade file.][The contents of the index.jade file.]

    Jade 是 Express 應用程式使用的預設檢視引擎。如需 Jade 檢視引擎的詳細資訊，
    請參閱 [][]<http://jade-lang.com></a>。

2.  修改最後一行文字，加上 **in Azure**。

    ![The index.jade file, the last line reads:p Welcome to \#{title} in Azure][The index.jade file, the last line reads:p Welcome to \#{title} in Azure]

3.  儲存檔案並結束 [記事本]。

4.  重新整理瀏覽器，您將會看到變更。

    ![A browser window, the page contains Welcome to Express in Azure][A browser window, the page contains Welcome to Express in Azure]

測試應用程式之後，請使用 **Stop-AzureEmulator** Cmdlet 來停止模擬器。

## 將應用程式發行至 Azure

在 Azure PowerShell 視窗中，請使用 **Publish-AzureServiceProject** Cmdlet 將應用程式部署至雲端服務

    PS C:\node\expressapp\WebRole1> Publish-AzureServiceProject -ServiceName myexpressapp -Location "East US" -Launch

部署作業完成時，瀏覽器會開啟並顯示網頁。

![A web browser displaying the Express page.The URL indicates it is now hosted on Azure.][A web browser displaying Welcome to Express in Azure]

  [Express]: http://expressjs.com/
  [A web browser displaying Welcome to Express in Azure]: ./media/cloud-services-nodejs-develop-deploy-express-app/node36.png
  [Azure PowerShell icon]: ./media/cloud-services-nodejs-develop-deploy-express-app/azure-powershell-start.png
  [Windows PowerShell displaying the output of the npm install express command.]: ./media/cloud-services-nodejs-develop-deploy-express-app/express-g.png
  [The output of the express command]: ./media/cloud-services-nodejs-develop-deploy-express-app/node23.png
  [The output of the npm install command]: ./media/cloud-services-nodejs-develop-deploy-express-app/node26.png
  [A web page containing welcome to express.]: ./media/cloud-services-nodejs-develop-deploy-express-app/node28.png
  [The contents of the index.jade file.]: ./media/cloud-services-nodejs-develop-deploy-express-app/getting-started-19.png
  []: http://jade-lang.com
  [A browser window, the page contains Welcome to Express in Azure]: ./media/cloud-services-nodejs-develop-deploy-express-app/node32.png
