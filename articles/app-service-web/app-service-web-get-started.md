<properties 
	pageTitle="在 Azure App Service 中開始使用 Web 應用程式" 
	description="了解如何在 App Service 中輕鬆地即時執行 Web 應用程式。在 5 分鐘內開始進行真正的開發，並立即查看結果。" 
	services="app-service\web"
	documentationCenter=""
	authors="cephalin" 
	manager="wpickett" 
	editor="" 
/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="hero-article"
	ms.date="03/17/2016" 
	ms.author="cephalin"
/>
	
# 在 Azure App Service 中開始使用 Web 應用程式

本教學課程可幫助您快速地開始將 Web 應用程式部署至 [Azure App Service](../app-service/app-service-value-prop-what-is.md)。您只需採取一些動作，就會︰

- 部署範例 Web 應用程式 (在 ASP.NET、PHP、Node.js、Java 或 Python 之間選擇)。
- 在短短幾秒內看見您的應用程式即時執行。
- 以您推送 [Git](http://www.git-scm.com/) 認可的相同方式，更新您的 Web 應用程式。

您將第一次目睹 [Azure 入口網站](https://portal.azure.com)並調查可用的功能。

## 必要條件

若要完成本教學課程，您需要：

- Git。您可以在[這裡](http://www.git-scm.com/downloads)下載安裝二進位檔。您應該能夠從您選擇的命令列終端機執行 `git --version`。 
- Git 基本知識。
- Azure CLI。安裝指示在[這裡](../xplat-cli-install.md)。您應該能夠從您選擇的命令列終端機執行 `azure --version`。
- Microsoft Azure 帳戶。如果您沒有這類帳戶，可以[申請免費試用](/pricing/free-trial/?WT.mc_id=A261C142F)，或是[啟用自己的 Visual Studio 訂閱者權益](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)。

>[AZURE.NOTE] 若要在註冊 Azure 帳戶前查看運作中的 Azure App Service，請移至[試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751)。您可以於該處，在 App Service 中立即建立短期的入門應用程式 — 不需信用卡，不需任何承諾。

## 部署 Web 應用程式

將 Web 應用程式部署至 Azure App Service。

1. 開啟新的 Windows 命令提示字元、Linux Shell 或 OS X 終端機，並 `CD` 到工作目錄和複製範例應用程式，如下所示︰

        git clone <github_sample_url>

    對於 &lt;github\_sample\_url>，使用下列其中一個 URL (視您要的架構而定)：

    - ASP.NET：[https://github.com/Azure-Samples/app-service-web-dotnet-get-started.git](https://github.com/Azure-Samples/app-service-web-dotnet-get-started.git)
    - PHP (CodeIgniter)：[https://github.com/Azure-Samples/app-service-web-php-get-started.git](https://github.com/Azure-Samples/app-service-web-php-get-started.git)
    - Node.js (Express)：[https://github.com/Azure-Samples/app-service-web-nodejs-get-started.git](https://github.com/Azure-Samples/app-service-web-nodejs-get-started.git) 
    - Java：[https://github.com/Azure-Samples/app-service-web-java-get-started.git](https://github.com/Azure-Samples/app-service-web-java-get-started.git)
    - Python (Django)：[https://github.com/Azure-Samples/app-service-web-python-get-started.git](https://github.com/Azure-Samples/app-service-web-python-get-started.git)

2. `CD` 為您的範例應用程式的根目錄。例如，

        cd app-service-web-dotnet-get-started

3. 如下所示，登入 Azure：

        azure login
    
    依照提示，在瀏覽器中繼續使用具有 Azure 訂用帳戶的 Microsoft 帳戶進行登入。

4. 在 Azure 中以下一個命令建立具有唯一應用程式名稱的 App Service 應用程式資源。您的 Web 應用程式的 URL 會是 http://&lt;app_name>.azurewebsites.net。

        azure site create --git <app_name> 
      
    >[AZURE.NOTE] 如果您從未設定 Azure 訂用帳戶的部署認證，系統將會提示您加以建立。App Service 只會將這些認證 (而不是您的 Azure 帳戶認證) 使用於 Git 部署與 FTP 登入。
    
    不僅您的應用程式已建立於 Azure 中，而您目前的目錄也已進行 Git 初始化並連接到新的 App Service 應用程式而成為 Git 遠端。您可以瀏覽至您應用程式的 URL 來查看美麗的預設 HTML 網頁，但讓我們立即實際取得您自己的程式碼。

4. 現在，將範例程式碼部署至新的 App Service 應用程式，如同使用 Git 推送任何程式碼一樣︰

        git push azure master 
    
    >[AZURE.NOTE] 系統將會詢問您的部署密碼。如果您是 App Service 新手，請提供您剛建立的部署密碼，即可開始執行作業。
    
    `git push` 不只將程式碼放在 Azure 中，也會在部署引擎中觸發部署工作。如果您的專案 (儲存機制) 根目錄中有任何 package.json (Node.js) 或 requirements.txt (Python)，或您的 ASP.NET 專案中有 packages.config，則部署指令碼會為您還原必要的封裝。您也可以[啟用編輯器延伸模組](web-sites-php-mysql-deploy-use-git.md#composer)，以在 PHP 應用程式中自動處理 composer.json 檔案。

恭喜，您的應用程式已部署至 Azure App Service。

## 看見您的應用程式即時執行

若要看見您的應用程式在 Azure 中即時執行，請執行這個命令 ︰

    azure site browse <app_name>

如果您看到錯誤訊息︰`Site <app_name> does not exist or has no hostnames`，請在幾秒後重試命令。有些應用程式 (如 Java 應用程式) 總結部署的時間比較長。

## 更新您的應用程式

您現在可以使用 Git 隨時從您的專案 (儲存機制) 根目錄進行推送，以更新即時網站。您可以用第一次將應用程式部署至 Azure 時的相同方式來執行這項作業。例如，每次您想要推送已在本機測試的新變更時，只需從專案 (儲存機制) 根目錄執行下列命令︰
    
    git add .
    git commit -m "<your_message>"
    git push azure master

## 其他部署方式

有各種方式可以部署 Web 應用程式，而從本機儲存機制進行 Git 部署只是其中一種方式。您可以直接從 Visual Studio 部署、從 GitHub 連續部署、從 DropBox 或 OneDrive 同步處理、透過 FTP 檔案上傳等。如需部署選項的詳細資訊，請參閱[將應用程式部署至 Azure App Service](../app-service-web/web-sites-deploy.md)。

## 在 Azure 入口網站上查看您的應用程式

現在，讓我們前往 Azure 入口網站，查看您所建立的項目︰

1. 使用具有您的 Azure 訂用帳戶的 Microsoft 帳戶，登入 [Azure 入口網站](https://portal.azure.com)。

2. 在左列中，按一下 [應用程式服務]。

3. 按一下您剛建立的 App Service 應用程式，以在入口網站中開啟其刀鋒視窗中。您會看到 [設定] 刀鋒視窗也已依預設開啟，方便您使用。

    ![Azure App Service 中第一個應用程式的入口網站檢視](./media/app-service-web-get-started/portal-view.png)

您的 App Service 應用程式的入口網站刀鋒視窗會呈現一組豐富的設定和工具，讓您設定、監視、保護及疑難排解您的應用程式。請花一點時間執行一些簡單的工作，讓自己熟悉此介面︰

- 停止應用程式
- 重新啟動應用程式
- 按一下 [資源群組] 連結，以查看資源群組中部署的所有資源
- 按一下 [設定] > [屬性]，以查看有關您的應用程式的其他資訊
- 按一下 [工具] 以存取可供監視和疑難排解的實用工具  

## 後續步驟

將您部署的應用程式提升到更高的層次。使用驗證保護其安全。根據需求加以調整。設定一些效能警示。全部只要點選幾下滑鼠即可完成。請參閱[開始使用 Azure App Service -第 2 部分](app-service-web-get-started-2.md)。

或者，進一步探索如何使用特定語言架構建立 App Service 的 Web 應用程式︰

- [在 Azure App Service 中建立 ASP.NET Web 應用程式](web-sites-dotnet-get-started.md)
- [在 Azure App Service 中建立 PHP Web 應用程式](web-sites-php-mysql-deploy-use-git.md)
- [在 Azure App Service 中建立 Node.js Web 應用程式](web-sites-nodejs-develop-deploy-mac.md)
- [在 Azure App Service 中建立 Java Web 應用程式](web-sites-java-get-started.md)
- [在 Azure App Service 中建立 Python Web 應用程式](web-sites-python-ptvs-django-mysql.md)

或者，尋找更多您可在 Azure App Service 上建置之應用程式的相關內容，包括 Web 應用程式、行動應用程式後端和 API 應用程式。

- [建立 Web 應用程式](/documentation/learning-paths/appservice-webapps/)
- [建立行動應用程式](/documentation/learning-paths/appservice-mobileapps/)
- [建立 API 應用程式](../app-service-api/app-service-api-apps-why-best-platform.md)

<!---HONumber=AcomDC_0323_2016-->