<properties
	pageTitle="在 Azure App Service 中建置和部署 Node.js API 應用程式"
	description="了解如何建立 Node.js API 應用程式封裝並將其部署至 Azure App Service。"
	services="app-service\api"
	documentationCenter="node"
	authors="bradygaster"
	manager="mohisri" 
	editor="tdykstra "/>

<tags
	ms.service="app-service-api"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="node"
	ms.topic="get-started-article"
	ms.date="11/27/2015"
	ms.author="bradygaster"/>

# 在 Azure App Service 中建置和部署 Node.js API 應用程式

[AZURE.INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

## 必要條件
1. 在開發電腦上執行的 [Node.js](nodejs.org) (此範例假設已安裝 Node.js 版本 4.2.2)
1. [GitHub](https://github.com/) 帳戶
1. Microsoft Azure [免費試用帳戶](https://azure.microsoft.com/pricing/free-trial/)
1. 安裝在本機開發工作站上的 Git

## 安裝指示
您應該使用 Node.js 命令列執行下列命令。使用 Swaggerize Yo 產生器，即可建構您為 Swagger JSON 檔案中定義的 HTTP 要求提供服務時，所需的基準 Node.js 程式碼。
 
1. 全面安裝 **yo** 和 **generator swaggerize** NPM 模組。

        npm install -g yo
	    npm install -g generator-swaggerize
		
1. 複製[包含範例程式碼的 GitHub 儲存機制](https://github.com/Azure-Samples/app-service-api-node-contact-list)。

		git clone https://github.com/Azure-Samples/app-service-api-node-contact-list.git
				
1. 執行此命令，建構以原始程式碼隨附的 **api.json** 檔案為基礎的 API。**api.json** 檔案是 Swagger 檔案，代表您將在下一個步驟中使用 "yo swaggerize" 命令建構的實際 API。

        yo swaggerize
        
    **附註：**API.json 與 API Apps 預覽時間範圍內的 *apiapp.json* 檔案有所不同。

1. Swaggerize 會建構處理常式，並設定 **api.json** 內含的 Swagger 中繼資料。在建構處理期間，系統會詢問您各種問題，如 GitHub 使用者名稱和電子郵件地址。此資訊用於在您的應用程式的資料夾中產生 **package.json** 檔案。在架構處理期間詢問的所有問題之中，最重要的是必須在系統詢問時選取 **express**；因為此範例會使用 express 檢視引擎，在 Azure 中執行 API 應用程式時產生 Swagger 說明頁面。

	![Swaggerize 命令列](media/app-service-api-nodejs-api-app/swaggerize-command-line.png)
    
1. 移入包含所建構程式碼的資料夾 (在此例中為 *ContactList* 子資料夾)。然後，安裝 **jsonpath** NPM 模組。

        npm install --save jsonpath
        
    您會在命令列體驗中看到安裝的結果。

    ![Jsonpath 安裝](media/app-service-api-nodejs-api-app/jsonpath-install.png)

1. 安裝 **swaggerize-ui** NPM 模組。

        npm install --save swaggerize-ui
        
    您會在命令列體驗中看到安裝的結果。

    ![Swaggerize Ui 安裝](media/app-service-api-nodejs-api-app/swaggerize-ui-install.png)

1. 從 **start** 資料夾將 **lib** 資料夾複製到建構者所建立的 **ContactList** 資料夾。

1. 以下列程式碼取代 **handlers/contacts.js** 檔案中的程式碼。此程式碼會使用 **lib/contactRepository.js** 所提供的 **lib/contacts.json** 檔案中儲存的 JSON 資料。以下的新 contats.js 程式碼會回應 HTTP 要求，以取得使用此程式碼的所有連絡人。

        'use strict';
        
        var repository = require('../lib/contactRepository');
        
        module.exports = {
            get: function contacts_get(req, res) {
                res.json(repository.all())
            }
        };

1. 以下列程式碼取代 **handlers/contacts/{id}.js** 檔案中的程式碼，其使用 **lib/contactRepository.js** 來取得 HTTP 要求所要求的連絡人並將它當作 JSON 承載傳回。

        'use strict';

        var repository = require('../../lib/contactRepository');
        
        module.exports = {
            get: function contacts_get(req, res) {
                res.json(repository.get(req.params['id']))
            }    
        };

1. 以下列程式碼取代 **server.js** 中的程式碼。請注意，會使用註解醒目提示對 server.js 檔案所做的變更，因此您可以看到所做的變更。

        'use strict';

        var port = process.env.PORT || 8000; // first change

        var http = require('http');
        var express = require('express');
        var bodyParser = require('body-parser');
        var swaggerize = require('swaggerize-express');
        var swaggerUi = require('swaggerize-ui'); // second change
        var path = require('path');

        var app = express();

        var server = http.createServer(app);

        app.use(bodyParser.json());

        app.use(swaggerize({
            api: path.resolve('./config/api.json'), // third change
            handlers: path.resolve('./handlers'),
            docspath: '/swagger' // fourth change
        }));

        // change four
        app.use('/docs', swaggerUi({
          docs: '/swagger'  
        }));

        server.listen(port, function () { // fifth change
            app.setHost(undefined); // sixth and final change
        });

1. 使用 Node.js 命令列可執行檔來啟動伺服器。

        node server.js

    執行此命令將會啟動 Node.js HTTP 伺服器並開始為您的 API 提供服務。

1. 當您瀏覽至 ****http://localhost:8000/contacts** 時，您會看見連絡人清單的 JSON 輸出 (或者系統會提示您下載它，視您的瀏覽器而定)。

    ![所有連絡人 Api 呼叫](media/app-service-api-nodejs-api-app/all-contacts-api-call.png)

1. 當您瀏覽至 ****http://localhost:8000/contacts/2** 時，您會看見該識別碼值所表示的連絡人。

    ![特定連絡人 Api 呼叫](media/app-service-api-nodejs-api-app/specific-contact-api-call.png)

1. Swagger JSON 資料是透過 **/swagger** 端點提供：

    ![連絡人 Swagger Json](media/app-service-api-nodejs-api-app/contacts-swagger-json.png)

1. Swagger UI 是透過 **/docs** 端點。在 Swagger UI 中，您可以使用豐富的 HTML 用戶端功能來測試您的 API。

    ![Swagger Ui](media/app-service-api-nodejs-api-app/swagger-ui.png)

## 在 Azure 入口網站中建立新的 API 應用程式
本節將逐步說明在 Azure 中建立新的空白 API 應用程式的程序。然後，您會將應用程式連到 Git 儲存機制，以便連續傳遞您的程式碼變更。

您從中複製原始程式碼的 GitHub 儲存機制，並不是您要推送程式碼以供部署的同一個儲存機制。範例 GitHub 儲存機制包含了程式碼的「開始」狀態，而現在您已建構程式碼的「結束」狀態，您必須將該程式碼只推送至與您的 API 應用程式相關聯的 Git 儲存機制。第一個步驟是使用 Azure 入口網站建立 API 應用程式，然後您將

1. 瀏覽至 [Azure 入口網站](http://portal.azure.com)。 

1. 建立新的 API 應用程式。

    ![新 Api 應用程式入口網站](media/app-service-api-nodejs-api-app/new-api-app-portal.png)

1. 您可以將新的 API 應用程式加入至現有的資源群組和/或 App Service 方案，或者建立新的資源群組和 App Service 方案，如以下螢幕擷取畫面所示範。

    ![Api 應用程式建立刀鋒視窗](media/app-service-api-nodejs-api-app/api-app-creation-blade.png)

1. 在入口網站中建立 API 應用程式後，瀏覽至包含 API 應用程式設定的刀鋒視窗，如下所示。

    ![入口網站瀏覽至設定](media/app-service-api-nodejs-api-app/portal-nav-to-settings.png)

1. 按一下 [設定] 功能表中的 [部署認證] 瀏覽項目。刀鋒視窗一旦開啟，新增用來將 Node.js 程式碼發佈至 API 應用程式的使用者名稱和密碼。然後，按一下 [設定部署認證] 刀鋒視窗上的 [儲存] 按鈕。

    ![部署認證](media/app-service-api-nodejs-api-app/deployment-credentials.png)

1. 一旦設定部署認證，即可建立與 App Service 相關聯的 Git 儲存機制。每次將程式碼推送至這個儲存機制時，Azure App Service 將收取您的變更，並將它們直接部署到您的 API 應用程式執行個體。若要建立要與您的網站產生關聯的 Git 儲存機制，請按一下 [設定] 功能表刀鋒視窗中的 [連續部署] 功能表項目，如下所示。然後，選取 [選擇來源] 刀鋒視窗中的 [本機 Git 儲存機制] 選項。然後，按一下 [確定] 按鈕來建立 Git 儲存機制。

    ![建立 Git 儲存機制](media/app-service-api-nodejs-api-app/create-git-repo.png)

1. 一旦建立 Git 儲存機制，此刀鋒視窗便會變更並顯示作用中的部署。由於這是新的儲存機制，所以清單中應該沒有作用中的部署。

    ![沒有作用中的部署](media/app-service-api-nodejs-api-app/no-active-deployments.png)

1. 最後一個步驟是從入口網站複製 Git 儲存機制 URL。若要這麼做，請瀏覽至新 API 應用程式的刀鋒視窗並查看刀鋒視窗的 [基本功能] 區段。您應該會在 [基本功能] 區段中看到 [Git 複製 URL]。旁邊會有一個圖示，可將 URL 複製到剪貼簿。按一下此圖示來複製 URL (此按鈕會在您將滑鼠移至 URL 上時顯示)，或選取整個 URL 並將它複製到剪貼簿。

    ![從入口網站取得 Git Url](media/app-service-api-nodejs-api-app/get-the-git-url-from-the-portal.png)

    **注意**：您在下一個步驟中需要使用 [Git 複製 URL]，才能確保暫時將它儲存在某個地方。

現在，您有新的 API 應用程式並以 Git 儲存機制進行備份，您可以將程式碼推送至儲存機制並利用 Azure 的連續部署功能來自動部署變更。

## 將 API 應用程式程式碼部署至 Azure
使用 Azure App Service 所提供的內建連續傳遞功能，您只要將程式碼認可至與 App Service 相關聯的 Git 儲存機制，Azure 就會挑選您的程式碼並將它部署到 API 應用程式。

1. 將 swaggerize 建構者所建立的 **src/end/ContactList** 資料夾複製到您的桌面或其他資料夾，因為您將會為應存留在您從包含入門程式碼的 GitHub 複製的主要儲存機制外部的程式碼，建立新的本機 Git 儲存機制。 

1. 使用 Node.js 命令列體驗來瀏覽至新的資料夾。抵達之後，執行下列命令來建立新的本機 Git 儲存機制。

        git init

    此命令會建立本機 Git 儲存機制，而您將看見已初始化新的儲存機制的確認訊息。

    ![新本機 Git 儲存機制](media/app-service-api-nodejs-api-app/new-local-git-repo.png)

1. 使用 Node.js 命令列體驗來執行下列命令，即可將 Git 遠端儲存機制新增到本機儲存機制。遠端儲存機制會是您剛建立的儲存機制，並與在 Azure 中執行的 API 應用程式相關聯。

        git remote add azure YOUR_GIT_CLONE_URL_HERE

    **注意**：您會想要以稍早複製的 Git 複製 URL 取代上述 "YOUR\_GIT\_CLONE\_URL\_HERE" 字串。

1. 接著，從 Node.js 命令列體驗執行以下兩個命令。

        git add .
        git commit -m "initial revision"

    完成這兩個命令後，您應會在命令列視窗中會看到類似以下的螢幕擷取畫面。

    ![Git 認可輸出](media/app-service-api-nodejs-api-app/git-commit-output.png)

1. 若要將程式碼推送至 Azure，以觸發對您的 API 應用程式部署，請在 Node.js 命令列中執行下列命令。當系統提示您輸入密碼時，請使用先前在 Azure 入口網站中建立部署認證時所使用的密碼。

        git push azure master

1. 如果您瀏覽回到 API 應用程式的 [連續部署] 刀鋒視窗，您會看到正在進行部署。

    ![發生的部署](media/app-service-api-nodejs-api-app/deployment-happening.png)

    同時，Node.js 命令列將會反映您目前的部署狀態。

    ![發生的節點 Js 部署](media/app-service-api-nodejs-api-app/node-js-deployment-happening.png)

1. 一旦部署完成，[連續部署] 刀鋒視窗會將部署成功的程式碼變更反映至您的 API 應用程式。複製 [API 應用程式] 刀鋒視窗的 [基本功能] 區段中的 [URL]。

    ![部署已完成](media/app-service-api-nodejs-api-app/deployment-completed.png)

1. 使用 Postman 或 Fiddler 等 REST API 用戶端 (或您的 Web 瀏覽器)，提供您的連絡人API 呼叫的 URL，這應該是 API 應用程式的 **/contacts** 端點。

    **注意：**URL 會類似於 http://myapiapp.azurewebsites.net/contacts

    當您對此端點發出 GET 要求時，您應會看到您的 API 應用程式的 JSON 輸出。

    ![Postman Hitting Api](media/app-service-api-nodejs-api-app/postman-hitting-api.png)

## 後續步驟

此時您已使用 Node.js 成功建立並部署第一個 API 應用程式。API Apps 入門系列中的下一個教學課程示範如何[使用 CORS 從 JavaScript 用戶端取用 API 應用程式](app-service-api-cors-consume-javascript.md)。

為了根據此範例進行建置，您可以將程式碼加入至處理常式，以便在資料庫中或在 API 應用程式執行個體的磁碟上儲存資料。既然您已連接 [連續部署]，變更 API 應用程式功能並加以擴充，就如同變更您的程式碼並推送至 Git 儲存機制一樣容易。

<!---HONumber=AcomDC_1210_2015-->