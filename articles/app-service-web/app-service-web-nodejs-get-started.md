<properties
	pageTitle="在 Azure App Service 中開始使用 Node.js Web 應用程式"
	description="了解如何將 Node.js 應用程式部署到 Azure App Service 中的 Web 應用程式。"
	services="app-service\web"
	documentationCenter="nodejs"
	authors="rmcmurray"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="nodejs"
	ms.topic="get-started-article"
	ms.date="03/31/2016"
	ms.author="cephalin;robmcm"/>

# 在 Azure App Service 中開始使用 Node.js Web 應用程式

> [AZURE.SELECTOR]
- [.Net](web-sites-dotnet-get-started.md)
- [Node.js](app-service-web-nodejs-get-started.md)
- [Java](web-sites-java-get-started.md)
- [PHP - Git](web-sites-php-mysql-deploy-use-git.md)
- [PHP - FTP](web-sites-php-mysql-deploy-use-ftp.md)
- [Python](web-sites-python-ptvs-django-mysql.md)

本教學課程顯示如何建立簡單的 [Node.js](http://nodejs.org) 應用程式，並透過 cmd.exe 或 bash 之類的命令列將其部署至 [Azure App Service](../app-service/app-service-value-prop-what-is.md) 中的 [Web 應用程式](app-service-web-overview.md)。本教學課程中的指示可運用在任何足以執行 Node.js 應用程式的作業系統上。

[Azure App Service Web Apps](/services/app-service/web/)

<a name="prereq"/>
## 必要條件

- Node.js。安裝二進位檔可從[這裡](https://nodejs.org/)取得。
- Yoeman。安裝指示在[這裡](http://yeoman.io/)。
- Git。安裝二進位檔可從[這裡](http://www.git-scm.com/downloads)取得。
- Azure CLI。安裝指示在[這裡](../xplat-cli-install.md)。
- Microsoft Azure 帳戶。如果您沒有這類帳戶，可以[申請免費試用](/pricing/free-trial/?WT.mc_id=A261C142F)，或是[啟用自己的 Visual Studio 訂閱者權益](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)。

## 建立和部署簡單的 Node.js Web 應用程式

1. 開啟您選擇的命令列終端機來安裝[適用於 Yoeman 的 Express 產生器](https://github.com/petecoop/generator-express)。

        npm install -g generator-express

2. `CD` 至工作目錄，並產生如下的快速應用程式︰

        yo express
        
    在系統提示時選擇下列選項︰

    `? Would you like to create a new directory for your project?` **Yes** `? Enter directory name` **&lt;appname>** `? Select a version to install:` **MVC** `? Select a view engine to use:` **Jade** `? Select a css preprocessor to use (Sass Requires Ruby):` **None** `? Select a database to use:` **None** `? Select a build tool to use:` **Grunt**

3. `CD` 至新應用程式的根目錄，並加以啟動以確保它會在您的開發環境中執行︰

        npm start

    在瀏覽器中瀏覽至 [http://localhost:3000/](http://localhost:3000) 以確定您可以看到 Express 首頁。一旦您確認應用程式正確執行，請使用 `Ctrl-C` 來停止它。
    
1. 如下所示登入 Azure (為此您需要 [Azure CLI](#prereq))：

        azure login

    依照提示，在瀏覽器中繼續使用具有 Azure 訂用帳戶的 Microsoft 帳戶進行登入。

2. 確定您仍在應用程式的根目錄中。在 Azure 中以下一個命令建立具有唯一應用程式名稱的 App Service 應用程式資源。您的 Web 應用程式的 URL 會是 http://&lt;appname>.azurewebsites.net。

        azure site create --git <appname>

    依照提示來選取要部署的目標 Azure 區域。如果您從未針對 Azure 訂用帳戶設定 Git/FTP 部署認證，則系統也會提示您加以建立。

3. 開啟 config/config.js 並將生產連接埠變更為 `process.env.port`。您的生產 JSON 物件看起來應如下所示︰

        production: {
            root: rootPath,
            app: {
                name: 'express1'
            },
            port: process.env.port,
        }

    這可讓您的 Node.js 應用程式在 iisnode 所接聽的預設連接埠上回應 Web 要求。
    
4. 儲存變更，然後使用 git 將您的應用程式部署至 Azure：

        git add .
        git commit -m "<your commit message>"
        git push azure master

    Express 產生器已提供 .gitignore 檔案，因此您的 `git push` 不會使用頻寬來嘗試上傳 node\_modules/ 目錄。

5. 最後，在瀏覽器中只要啟動即時 Azure 應用程式︰

        azure site browse

    您現在應該會看到 Node.js Web 應用程式在 Azure App Service 中即時執行。
    
    ![](./media/app-service-web-nodejs-get-started/deployed-express-app.png)

## 更新您的 Node.js Web 應用程式

若要更新在 App Service 中執行的 Node.js Web 應用程式，只需和您第一次部署它時一樣執行 `git add`、`git commit` 和 `git push`。
     
## App Service 如何部署您的 Node.js 應用程式

Azure App Service 使用 [iisnode](https://github.com/tjanczuk/iisnode/wiki) 來執行 Node.js 應用程式。Azure CLI 和 Kudu 引擎 (Git 部署) 會一同合作，讓您在透過命令列開發和部署 Node.js 應用程式時獲得流暢的體驗。

- `azure site create --git` 會辨識 server.js 或 app.js 的常見 Node.js 模式，並在根目錄中建立 iisnode.yml。您可以使用這個檔案來自訂 iisnode。
- 在 `git push azure master` 中，Kudu 會自動執行下列部署工作︰

    - 如果 package.json 位於儲存機制根目錄中，請執行 `npm install --production`。
    - 在 package.json 中為指向啟動指令碼的 iisnode 產生 Web.config (例如 server.js 或 app.js)。
    - 自訂 Web.config 以讓應用程式準備好使用節點偵測器進行偵錯。
    
## 使用 Node.js 架構

如果您使用熱門的 Node.js 架構 (例如 [Sails.js](http://sailsjs.org/) 或 [MEAN.js](http://meanjs.org/)) 來開發應用程式，您可以將這些應用程式部署到 App Service。熱門的 Node.js 架構有其特定的行為模式，而且其封裝相依性會不斷更新。不過，App Service 可提供 stdout 和 stderr 記錄給您，讓您確實了解應用程式發生了什麼事並據以變更。如需詳細資訊，請參閱[從 iisnode 取得 stdout 和 stderr 記錄](#iisnodelog)。

請參閱為您示範如何在 App Service 中使用特定架構的教學課程

- [將 Sails.js Web 應用程式部署至 Azure App Service](app-service-web-nodejs-sails.md)
- [在 Azure App Service 中使用 Socket.IO 建立 Node.js 聊天應用程式](web-sites-nodejs-chat-app-socketio.md)
- [如何搭配使用 io.js 和 Azure App Service Web Apps](web-sites-nodejs-iojs.md)

## 使用特定的 Node.js 引擎

和平常在 package.json 中的方式一樣，您可以在一般工作流程中告知 App Service 使用特定的 Node.js 引擎。例如：

    "engines": {
        "node": "5.5.0"
    }, 

Kudu 部署引擎會依下列順序決定要使用哪個 Node.js 引擎︰

- 首先，查看 iisnode.yml 以確認是否已指定 `nodeProcessCommandLine`。如果是，則使用它。
- 接下來，查看 package.json 以確認是否已在 `engines` 物件中指定 `"node": "..."`。如果是，則使用它。
- 依預設選擇預設的 Node.js 版本。

<a name="iisnodelog" />
## 從 iisnode 取得 stdout 和 stderr 記錄

若要讀取 iisnode 記錄，請遵循下列步驟︰

1. 開啟 Azure CLI 給您的 iisnode.yml 檔案

2. 設定下列兩個參數︰

        loggingEnabled: true
        logDirectory: iisnode
    
    它們會一起告訴 App Service 中的 iisnode 將其 stdout 和 stderror 輸出放在 D:\\home\\site\\wwwroot**iisnode** 目錄中。

3. 儲存變更，然後使用下列 Git 命令將變更推送至 Azure：

        git add .
        git commit -m "<your commit message>"
        git push azure master
   
   iisnode 現在已設定好。接下來的步驟會示範如何存取這些記錄。
     
4. 在瀏覽器中存取應用程式的 Kudu 偵錯主控台，其位於︰

        https://<appname>.scm.azurewebsites.net/DebugConsole 

5. 瀏覽至 D:\\home\\site\\wwwroot\\iisnode

    ![](./media/app-service-web-nodejs-get-started/iislog-kudu-console-navigate.png)

6. 按一下您想讀取之記錄的 [編輯] 圖示。如果您想要的話，也可以按一下 [下載] 或 [刪除]。

    ![](./media/app-service-web-nodejs-get-started/iislog-kudu-console-open.png)

    現在您可以查看記錄以協助您偵錯 App Service 部署。
    
    ![](./media/app-service-web-nodejs-get-started/iislog-kudu-console-read.png)


## 使用節點偵測器偵錯應用程式

如果您使用節點偵測器來偵錯 Node.js 應用程式，您可以將它用於您的即時 App Service 應用程式。節點偵測器會預先安裝在 App Service 的 iisnode 安裝中。如果您透過 Git 部署，則從 Kudu 自動產生的 Web.config 已包含啟用節點偵測器所需的所有組態。

若要啟用節點偵測器，請遵循下列步驟︰

1. 開啟位於儲存機制根目錄中的 iisnode.yml，並指定下列參數︰ 

        debuggingEnabled: true
        debuggerExtensionDll: iisnode-inspector.dll

3. 儲存變更，然後使用下列 Git 命令將變更推送至 Azure：

        git add .
        git commit -m "<your commit message>"
        git push azure master
   
4. 現在，只需要在 URL 中加上 /debug 以瀏覽至 package.json 中的啟動指令碼所指定的應用程式啟動檔案。例如，

        http://<appname>.azurewebsites.net/server.js/debug
    
    或者，
    
        http://<appname>.azurewebsites.net/app.js/debug

## 其他資源

- [在 Azure 應用程式中指定 Node.js 版本](../nodejs-specify-node-version-azure-apps.md)
- [如何在 Azure App Service 中偵錯 Node.js Web 應用程式](web-sites-nodejs-debug.md)
- [使用 Node.js 模組與 Azure 應用程式搭配](../nodejs-use-node-modules-azure-apps.md)
- [Azure App Service Web Apps：Node.js](http://blogs.msdn.com/b/silverlining/archive/2012/06/14/windows-azure-websites-node-js.aspx)
- [Node.js 開發人員中心](/develop/nodejs/)
- [在 Azure App Service 中開始使用 Web 應用程式](app-service-web-get-started.md)

<!---HONumber=AcomDC_0406_2016-->