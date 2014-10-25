<properties linkid="dev-nodejs-cloud9" urlDisplayName="Deploying with Cloud9" pageTitle="Node.js deploying with Cloud9 - Azure tutorial" metaKeywords="Cloud9 IDE Azure, Azure node.js, Azure node apps" description="Learn how to use Cloud9 IDE to develop, build, and deploy a Node.js application to Azure." metaCanonical="" services="cloud-services" documentationCenter="nodejs" title="Deploying an Azure App from Cloud9" authors="larryfr" solutions="" manager="" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr"></tags>

# 從 Cloud9 部署 Azure 應用程式

本教學課程描述如何使用 Cloud9 IDE 開發和建置 Node.js 應用程式，並將它部署至 Azure。

在本教學課程中，您將了解如何：

-   建立 Cloud9 IDE 專案
-   將專案部署至 Azure
-   更新現有的 Azure 部署
-   在預備與生產部署之間移動專案

[Cloud9 IDE][] 提供跨平台的瀏覽器型開發環境。Cloud9 針對 Node.js 專案支援的其中一個功能，是您可以從 IDE 直接部署至 Azure。Cloud9 也會與 GitHub 和 BitBucket 儲存機制服務整合，因此可以輕鬆地與其他人共用您的專案。

使用 Cloud9，您可以從許多現代瀏覽器和作業系統開發應用程式並將它部署至 Azure，而不需要在本機安裝其他開發工具或 SDK。下列步驟示範如何在 Mac 上使用 Google Chrome。

## 註冊

若要使用 Cloud9，您需要先瀏覽其網站並[註冊訂閱][Cloud9 IDE]。您可以使用現有 GitHub 或 BitBucket 帳戶登入，或建立 Cloud9 帳戶。您可以取得免費訂閱方案，以及提供其他功能的付費方案。如需詳細資訊，請參閱 [Cloud9 IDE][] (英文)。

## 建立 Node.js 專案

1.  登入 Cloud9、按一下 [我的專案] 旁邊的 [+] 符號，
    然後選取 [建立新專案]。

    ![create new Cloud9 project][]

2.  在 [建立新專案] 對話方塊中，輸入專案名稱、存取權和專案類型。
    按一下 [建立] 建立專案。

    ![create new project dialog Cloud9][]

    <div class="dev-callout">
<strong>注意</strong>
<p>某些選項需要付費型 Cloud9 計畫。</p>
</div>

    <div class="dev-callout">
<strong>注意</strong>
<p>部署至 Azure 時，不會使用 Cloud9 專案的專案名稱。</p>
</div>

3.  建立專案之後，請按一下 [Start Editing]。如果這是您第一次使用 Cloud9 IDE，則可以選擇進行服務的導覽。如果您想要跳過此導覽，稍後再進行檢視，請選取 [Just the editor,please]。

    ![start editing the Cloud9 project][]

4.  若要建立新的 Node 應用程式，請選取 [檔案]，然後選取 [新檔案]。

    ![create new file in the Cloud9 project][]

5.  將會顯示標題為 **Untitled1** 的新索引標籤。請在 [Untitled1] 索引標籤上輸入下列程式碼，
    以建立 Node 應用程式：

        var http = require('http');
        var port = process.env.PORT;
        http.createServer(function(req,res) {
            res.writeHead(200, { 'Content-Type': 'text/plain' });
            res.end('hello azure\n');
        }).listen(port);

    <div class="dev-callout">
<strong>注意</strong>
<p>如果使用 process.env.PORT，則不論在 Cloud9 偵錯工具中執行或部署至 Azure 時，都可確保應用程式挑選到正確的連接埠。</p>
</div>

6.  若要儲存程式碼，請選取 [檔案]，然後選取 [另存新檔]。在 [另存新檔] 對話方塊中，
    輸入 **server.js** 作為檔案名稱，然後按一下 [儲存]。

    <div class="dev-callout">
<strong>注意</strong>
<p>您可能會注意到指出未使用 req 變數的警告符號。您可以放心地略過此警告。</p>
</div>

    ![save the server.js file][]

## 執行應用程式

<div class="dev-callout">
<strong>注意</strong>
<p>雖然本節中提供的步驟對 Hello World 應用程式就已足夠，但如果是使用外部模組的應用程式，則可能需要針對偵錯環境選取特定版本的 Node.js。若要這樣做，請從偵錯下拉式清單中選取 [設定...]，然後選取特定版本的 Node.js。例如，如果您未選取 Node.js 0.6.x，則可能會在使用「azure」模組時接收到驗證錯誤。</p>
</div>

1.  在 Cloud9 偵錯工具中，按一下 [偵錯] 執行應用程式。

    ![run in the debugger][]

2.  將會顯示輸出視窗。按一下列出的 URL，透過瀏覽器視窗來存取應用程式。

    ![output window][]

    產生的應用程式外觀如下：

    ![application running in browser][]

3.  若要停止偵錯應用程式，請按一下 [停止]。

## 建立 Azure 帳戶

若要將應用程式部署至 Azure，您需要有帳戶。如果您還沒有 Azure 帳戶，
則可遵循下列步驟來註冊免費試用版：

[WACOM.INCLUDE [create-azure-account][]]

## 建立部署

1.  若要建立新的部署，請選取 [部署]，然後按一下 [+] 建立部署伺服器。

    [建立新部署][]

2.  在 [Add a deploy target] 對話方塊中輸入部署名稱，然後選取 [選擇類型] 清單中的 [Azure]。您指定的部署名稱將會用來識別 Cloud9 內的部署；它將不會對應到 Azure 內的部署名稱。

3.  如果這是您第一次建立使用 Azure 的 Cloud9 部署，則必須設定 Azure 發佈設定。請執行下列步驟來下載這些設定，並將其安裝至 Cloud9：

    1.  按一下 [Download Azure Settings]。

        ![download publish settings][]

        這會開啟 Azure 管理入口網站，並提示您下載 Azure 發佈設定。您需要先登入 Azure 帳戶，才能開始進行。

    2.  將發佈設定檔案儲存至本機磁碟機。

    3.  在 [新增部署目標] 對話方塊中，選取 [選擇檔案]，然後選取上一個步驟中下載的檔案。

    4.  選取檔案之後，按一下 [上傳]。

4.  按一下 [+ 新建] 建立新的託管服務。「託管服務」是應用程式部署到 Azure 時託管應用程式的容器。如需詳細資訊，請參閱[雲端服務][]。

    ![create a new deployment][]

5.  系統將會提示您輸入新託管服務的名稱，以及執行個體數目、主機 OS 和資料中心這類組態選項。指定的部署名稱將會用作 Azure 中的託管服務名稱。在 Azure 系統內，此名稱必須是唯一的。

    ![create a new hosted service][]

    <div class="dev-callout">
<strong>注意</strong>
<p>在 [新增部署目標] 對話方塊的 [選擇現有的部署] 區段下，會列出所有現有的 Azure 託管服務；如果選取現有的託管服務，即會將此專案部署至該服務。</p>
</div>

    <div class="dev-callout">
<strong>注意</strong>
<p>如果選取 [啟用 RDP] 並提供使用者名稱和密碼，則會啟用您部署的遠端桌面。</p>
</div>

## 部署至 Azure 生產環境

1.  選取您在先前步驟中建立的部署。此時會顯示對話方塊，來提供此部署的相關資訊，以及將在部署至 Windows Azure 之後使用的生產 URL。

    ![select a deployment][]

2.  選取 [部署到生產環境]。

3.  按一下 [部署] 開始部署。

4.  如果這是您第一次將此專案部署至 Azure，則會接收到 [No web.config found] 錯誤。請選取 [是] 建立檔案。這會將 'Web.cloud.config' 檔案新增至專案。

    ![no web.config file found message][]

5.  如果這是您第一次將此專案部署至 Azure，則會接收到 [No 'csdef' file present] 錯誤。請選取 [是] 建立 .csdef 檔案。這會將 'ServiceDefinition.csdef' 檔案新增至專案。ServiceDefinition.csdef 是發佈應用程式所需的 Azure 特定檔案。如需詳細資訊，請參閱[雲端服務][]。

6.  系統將會提示您選取此應用程式的執行個體大小。選取 [小型]，然後按一下 [建立]。如需 Azure VM 大小的詳細資料，請參閱[設定雲端服務的大小][]。

    ![specify csdef file values][]

7.  部署項目將會顯示部署程序的狀態。完成之後，部署將會顯示為 [使用中]。

    ![deployment status][]

    <div class="dev-callout">
<strong>注意</strong>
<p>會將 GUID 指派給透過 Cloud 9 IDE 所部署的專案，以做為 Azure 中的部署名稱。</p>
</div>

8.  部署對話方塊包括生產 URL 的連結。部署完成時，請按一下 URL，以瀏覽至您在 Azure 中執行的應用程式。

    ![Azure production URL link][]

## 更新應用程式

變更應用程式時，可以使用 Cloud9，將更新過的應用程式部署至相同的 Azure 託管服務。

1.  在 server.js 檔案中，更新程式碼，以將 "hello azure v2" 列印至螢幕。您可以將現有程式碼取代為下列更新過的程式碼：

        var http = require('http');
        var port = process.env.PORT;
        http.createServer(function(req,res) {
            res.writeHead(200, { 'Content-Type': 'text/plain' });
            res.end('hello azure v2\n');
        }).listen(port);

2.  若要儲存程式碼，請依序選取 [檔案] 及 [儲存]。

## 將更新部署至 Azure 預備環境

1.  選取 [Deploy to Staging]。

2.  按一下 [部署] 開始部署。

    每個 Azure 託管服務都支援兩種環境：預備和生產。預備環境與生產環境完全相同，差別在於您只能使用 Azure 所產生的模糊 GUID 型 URL 來存取預備應用程式。您可以使用預備環境來測試應用程式，而且在驗證變更之後，可以執行虛擬 IP (VIP) 交換來將預備版本移動至生產環境 (如本教學課程後面所述)。

3.  將應用程式部署至預備環境時，將會在 [主控台] 輸出中顯示 GUID 型預備 URL (如下面的螢幕擷取畫面所示)。按一下 URL，即可在瀏覽器中開啟預備應用程式。

    ![console output showing staging URL][]

## 使用 VIP 交換，將更新移動至生產環境

將服務部署至生產或預備環境時，會將虛擬 IP 位址 (VIP) 指派給該環境中的服務。當您想要將服務從預備環境移至生產環境時，只需執行 VIP 交換就能達成，而不需重新部署，而 VIP 交換會交換預備和生產部署。VIP 交換會將測試過的預備應用程式放到生產環境中，而生產環境中不會產生任何停機時間。如需更多詳細資料，請參閱[在 Azure 中管理部署][]。

1.  在部署對話方塊中，按一下 [開啟入口網站] 連結，以開啟 Azure 管理入口網站。

    [從部署對話方塊到 Azure 管理入口網站的連結][從部署對話方塊到 Azure 管理入口網站的連結]

2.  使用您的認證來登入入口網站。

3.  在網頁左側，選取 [託管服務、儲存體帳戶和 CDN]，
    然後按一下 [託管服務]。

    [Azure 管理入口網站][Azure 管理入口網站]

    結果窗格會顯示具有您在 Cloud9 中所指定名稱的託管服務，以及兩個部署：一個具有 [環境] 值 [預備]，第二個則具有 [生產]。

4.  若要執行 VIP 交換，請選取託管服務，然後按一下功能區中的 [交換 VIP]。

    ![VIP SWAP][]

5.  在顯示的 [交換 VIP] 對話方塊中，按一下 [確定]。

6.  瀏覽至生產應用程式。您將會看到先前部署至預備環境的應用程式版本現在為生產版本。

    ![Production application running on Azure][]

## 使用遠端桌面

如果您已在建立部署時啟用 RDP 並指定使用者名稱和密碼，則可選取特定執行個體，然後選取功能區上的 [連線]，以使用 [遠端桌面] 連線到您的託管服務。

![Connect to an instance][]

當您按一下 [連線] 時，系統會提示您開啟或下載 .RDP 檔案。此檔案包含連線到遠端桌面工作階段所需的資訊。如果在 Windows 系統上執行此檔案，則系統會提示您提供在建立部署時所輸入的使用者名稱和密碼，接著將您連線到所選取執行個體的桌面。

<div class="dev-callout">
<strong>注意</strong>
<p>用來連線到應用程式託管執行個體的 .RDP 檔案只適用於 WIndows 上的 [遠端桌面] 
應用程式。</p>
</div>

## 停止和刪除應用程式

Azure 會根據每小時的耗用伺服器時間向角色執行個體請款，而且部署應用程式之後即會耗用伺服器時間，即使執行個體未執行並處於已停止狀態也是一樣。此外，生產和預備部署都會耗用伺服器時間。

Cloud9 著重在提供 IDE，並未提供將應用程式部署至 Azure 之後，停止或刪除應用程式的直接方法。若要刪除 Azure 中所託管的應用程式，請執行下列步驟：

1.  在部署對話方塊中，按一下 [Open portal] 連結，以開啟 Azure 管理入口網站。

    ![Link from deploy dialog to Azure Management Portal][]

2.  使用您的認證來登入入口網站。

3.  在網頁左側，選取 [託管服務、儲存體帳戶和 CDN]，然後按一下 [託管服務]。

4.  選取預備部署 (以 [環境] 值表示)。按一下功能區中的 [刪除] 來刪除應用程式。

    ![delete the deployment][]

5.  選取生產部署，然後按一下 [刪除] 一併刪除該應用程式。

## 其他資源

-   [Cloud9 文件][]

  [Cloud9 IDE]: http://cloud9ide.com/
  [create new Cloud9 project]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_create_project.png
  [create new project dialog Cloud9]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_new_project.png
  [start editing the Cloud9 project]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_startediting.png
  [create new file in the Cloud9 project]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_filenew.png
  [save the server.js file]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_saveas.png
  [run in the debugger]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_debug.png
  [output window]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_output.png
  [application running in browser]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_debug_browser.png
  [create-azure-account]: ../includes/create-azure-account.md
  [download publish settings]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_choosetypeandcert.png
  [雲端服務]: http://msdn.microsoft.com/zh-TW/library/windowsazure/jj155995.aspx
  [建立新部署]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_createdeployment.png
  [create a new hosted service]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_new_hosted_service_settings.png
  [select a deployment]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_select_deployment.png
  [no web.config file found message]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_no_web_config.png
  [設定雲端服務的大小]: http://msdn.microsoft.com/zh-TW/library/windowsazure/ee814754.aspx
  [specify csdef file values]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_createcsdef.png
  [deployment status]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_deployment_status.png
  [Azure production URL link]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_production_url.png
  [console output showing staging URL]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_staging_console_output.png
  [在 Azure 中管理部署]: http://msdn.microsoft.com/zh-TW/library/windowsazure/gg433027.aspx
  [VIP SWAP]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_portal_vipswap.png
  [Production application running on Azure]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_production_on_azure.png
  [Connect to an instance]: ./media/cloud-services-nodejs-develop-deploy-cloud9/connect.png
  [Link from deploy dialog to Azure Management Portal]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_portal_link.png
  [delete the deployment]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_deletedeployment.png
  [Cloud9 文件]: http://go.microsoft.com/fwlink/?LinkId=241421&clcid=0x409
