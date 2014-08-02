<properties  linkid="dev-nodejs-basic-web-app-with-express" urlDisplayName="Web App with Express" pageTitle="Web App with Express (Node.js) - Azure Tutorial" metaKeywords="Azure Node.js hello world tutorial, Azure Node.js hello world, Azure Node.js Getting Started tutorial, Azure Node.js tutorial, Azure Node.js Express tutorial" description="An tutorial that builds on the cloud service tutorial, and demonstrates how to use the Express module." metaCanonical="" services="cloud-services" documentationCenter="Node.js" title="Build a Node.js web application using Express on an Azure Cloud Service" authors="" solutions="" manager="" editor="" />

# 在 Azure 雲端服務上使用 Express 建立 Node.js Web 應用程式

Node.js 包含核心執行時期的一組最低功能。開發人員在開發 Node.js
應用程式時，通常會使用協力廠商模組來提供更多功能。在本教學課程中，您將使用 [Express][1] 模組來建立新的應用程式，此模組提供建立
Node.js Web 應用程式的 MVC 架構。

完成之應用程式的螢幕擷取畫面如下：

![A web browser displaying Welcome to Express in
Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node36.png)

## 建立雲端服務專案

請執行下列步驟來建立名為 'expressapp' 的新雲端服務專案：

1.  從 **開始] 功能表**** 或 [開始畫面** 中，搜尋 **Azure PowerShell**。最後，以滑鼠右鍵按一下
    **Azure PowerShell**，然後選取 **以系統管理員身分執行**。
    
    ![Azure PowerShell
    icon](./media/cloud-services-nodejs-develop-deploy-express-app/azure-powershell-start.png)
    
    [WACOM.INCLUDE [install-dev-tools](../includes/install-dev-tools.md)]

2.  切換至 **c:\node** 目錄，然後輸入下列命令來建立名為 **expressapp** 的新方案和名為 **WebRole1**
    的 Web 角色：
    
         PS C:\node> New-AzureServiceProject expressapp
        PS C:\Node> Add-AzureNodeWebRole

## 安裝 Express

1.  發出下列命令來安裝 Express 模組：
    
         PS C:\node\expressapp> npm install express -g
    
    npm 命令的輸出類似下列結果。
    
    ![Windows PowerShell displaying the output of the npm install
    express
    command.](./media/cloud-services-nodejs-develop-deploy-express-app/express-g.png)

2.  切換至 **WebRole1** 目錄，然後使用 express 命令來產生新的應用程式：
    
         PS C:\node\expressapp\WebRole1> express
    
    系統會提示您覆寫先前的應用程式。輸入 **y** 或 **yes** 繼續進行。Express 會產生 app.js
    檔案和資料夾結構來準備建立應用程式。
    
    ![The output of the express
    command](./media/cloud-services-nodejs-develop-deploy-express-app/node23.png)

3.  刪除 **server.js** 檔案，然後將產生的 **app.js** 檔案重新命名為 **server.js**。
    
        PS C:\node\expressapp\WebRole1> del server.js
        PS C:\node\expressapp\WebRole1> ren app.js server.js

4.  若要安裝 package.json 檔案中定義的其他相依性，請輸入下列命令：
    
        PS C:\node\expressapp\WebRole1> npm install
    
    ![The output of the npm install
    command](./media/cloud-services-nodejs-develop-deploy-express-app/node26.png)

5.  使用下列命令，在 Azure 模擬器中執行應用程式：
    
        PS C:\node\expressapp\WebRole1> Start-AzureEmulator -launch
    
    ![A web page containing welcome to
    express.](./media/cloud-services-nodejs-develop-deploy-express-app/node28.png)

## 修改檢視

現在，修改檢視來顯示「歡迎在 Azure 中使用 Express」訊息。

1.  輸入下列命令來開啟 index.jade 檔案：
    
        PS C:\node\expressapp\WebRole1> notepad views/index.jade
    
    ![The contents of the index.jade
    file.](./media/cloud-services-nodejs-develop-deploy-express-app/getting-started-19.png)
    
    Jade 是 Express 應用程式使用的預設檢視引擎。如需 Jade 檢視引擎的詳細資訊，請參閱
    [http://jade-lang.com][2]。

2.  修改最後一行文字，加上 **in Azure**。
    
    ![The index.jade file, the last line reads:p Welcome to \#{title} in
    Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node31.png)

3.  儲存檔案並結束 [記事本]。

4.  重新整理瀏覽器，您將會看到變更。
    
    ![A browser window, the page contains Welcome to Express in
    Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node32.png)

測試應用程式之後，請使用 **Stop-AzureEmulator** Cmdlet 來停止模擬器。

## 將應用程式發行至 Azure

在 Azure PowerShell 視窗中，請使用 **Publish-AzureServiceProject** Cmdlet
將應用程式部署至雲端服務

    PS C:\node\expressapp\WebRole1> Publish-AzureServiceProject -ServiceName myexpressapp -Location "East US" -Launch

部署作業完成時，瀏覽器會開啟並顯示網頁。

![A web browser displaying the Express page.The URL indicates it is now
hosted on Azure.](./media/cloud-services-nodejs-develop-deploy-express-app/node36.png)



[1]: http://expressjs.com/
[2]: http://jade-lang.com