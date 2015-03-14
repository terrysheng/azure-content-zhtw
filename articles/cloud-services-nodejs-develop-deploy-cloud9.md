<properties 
	pageTitle="使用 Cloud9 來部署 Node.js - Azure 教學課程" 
	description="了解如何使用 Cloud9 IDE 開發和建置 Node.js 應用程式，並將它部署至 Azure。" 
	services="cloud-services" 
	documentationCenter="nodejs" 
	authors="" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="09/17/2014" 
	ms.author="wpickett"/>






# 從 Cloud9 部署 Azure 應用程式

本教學課程說明如何使用 Cloud9 IDE 開發、建置及
部署 Node.js 應用程式至 Azure。

在本教學課程中，您將了解如何：

-   建立 Cloud9 IDE 專案
-   將專案部署至 Azure
-   更新現有的 Azure 部署
-   在預備與生產部署之間移動專案

[Cloud9 IDE][] 提供以瀏覽器為基礎的跨平台開發
環境。Cloud9 針對 Node.js 專案所支援的其中一項功能是
您可以直接從 IDE 內部署到 Azure 。
Cloud9 也會整合 GitHub 和 BitBucket 儲存機制
服務，讓您可以輕易地與他人共用專案。

使用 Cloud9，您可以從許多新式瀏覽器和作業系統開發應用程式並部署至 Azure
，而不需要
在本機上安裝其他開發工具或 SDK。下列步驟
是在 Mac 上使用 Google Chrome 進行示範。

## 註冊

若要使用 Cloud9，您必須先瀏覽其網站並[註冊
訂閱][Cloud9 IDE]。您可以使用現有 
GitHub 或 BitBucket 帳戶進行登入，或建立 Cloud9 帳戶。您可以取得免費
訂閱方案，以及能提供更多功能的付費方案
。如需詳細資訊，請參閱 [Cloud9 IDE][]。

## 建立 Node.js 專案

1.  登入 Cloud9，按一下 [**我的專案**] 旁的 **+** 符號，
    然後選取 [**建立新的專案**]。

	![create new Cloud9 project](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_create_project.png)

2.  在 [**建立新的專案**] 對話方塊中，輸入專案名稱，
    存取和專案類型。按一下 [**建立**] 來建立專案。

	![create new project dialog Cloud9](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_new_project.png)

	> [AZURE.NOTE] 某些選項需要付費型 Cloud9 計畫。
	   
	> [AZURE.NOTE] 部署至 Azure 時，不會使用 Cloud9 專案的專案名稱。

3.  在建立好專案後，按一下 [**開始編輯**]。如果這是您第一次使用 Cloud9 IDE，則可以選擇進行服務的導覽。如果您想要先跳過此導覽，日後再檢視，請選取 [**請直接進入編輯器**]。

	![start editing the Cloud9 project](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_startediting.png)

4.  若要建立新的 Node 應用程式，請依序選取 [**檔案**] 和 [**新增
    檔案**]。

	![create new file in the Cloud9 project](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_filenew.png)

5.  隨即會顯示新的索引標籤，其標題為 [**Untitled1**]。在 
    [**Untitled1**] 索引標籤上輸入下列程式碼來建立 Node 
    應用程式：

        var http = require('http');
        var port = process.env.PORT;
        http.createServer(function(req,res) {
            res.writeHead(200, { 'Content-Type': 'text/plain' });
            res.end('hello azure\n');
        }).listen(port);
	
	> [AZURE.NOTE] 如果使用 process.env.PORT，則不論在 Cloud9 偵錯工具中執行或部署至 Azure 時，都可確保應用程式挑選到正確的連接埠。

6.  若要儲存程式碼，請依序選取 [**檔案**] 和 [**另存新檔**]。在 
    [**另存新檔**] 對話方塊中，輸入 **server.js** 做為檔案名稱，然後
    按一下 [**儲存**]。


	> [AZURE.NOTE] 您可能會注意到指出未使用 req 變數的警告符號。您可以放心地略過此警告。

	![save the server.js file](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_saveas.png)

## 執行應用程式

> [AZURE.NOTE] 雖然本節中提供的步驟對 Hello World 應用程式就已足夠，但如果是使用外部模組的應用程式，則可能需要針對偵錯環境選取特定版本的 Node.js。若要這樣做，請從偵錯下拉式清單中選取 [**設定...**]，然後選取特定版本的 Node.js。例如，如果您沒有選取 Node.js 0.6.x，在使用 'azure' 模組時，可能會收到驗證錯誤。


1.  在 Cloud9 偵錯工具中按一下 [**偵錯**] 來執行應用程式。
	
	![run in the debugger](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_debug.png)

2.  將會顯示輸出視窗。按一下列出的 URL 以
    透過瀏覽器視窗存取您的應用程式。

	![output window](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_output.png)

	產生的應用程式外觀如下：

	![application running in browser](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_debug_browser.png)

3.  若要停止偵錯應用程式，請按一下 [**停止**]。

## 建立 Azure 帳戶

若要將應用程式部署至 Azure，您需要有帳戶。如果您
還沒有 Azure 帳戶，您可以註冊免費的
試用版，步驟如下：

[AZURE.INCLUDE [create-azure-account](../includes/create-azure-account.md)]


## 建立部署

1.  若要建立新的部署，請選取 [**部署**]，然後按一下 **+** 來建立部署伺服器。

    ![create a new deployment][create a new deployment]

2.  在 [**新增部署目標**] 對話方塊中，輸入部署名稱，然後在 [**選擇類型**] 清單中選取 [**Azure**]。您指定的部署名稱將會用來識別 Cloud9 內的部署；它將不會對應到 Azure 內的部署名稱。

3.  如果這是您第一次建立使用 Azure 的 Cloud9 部署，則必須設定 Azure 發佈設定。請執行下列步驟來下載這些設定，並將其安裝至 Cloud9：

    1.  按一下 [**下載 Azure 設定**]。

        ![download publish settings](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_choosetypeandcert.png)

        這會開啟 Azure 管理入口網站，並提示您下載 Azure 發佈設定。您需要先登入 Azure 帳戶，才能開始進行。

    2.  將發佈設定檔案儲存至本機磁碟機。

    3.  在 [**新增部署目標**] 對話方塊中，選取 [**選擇檔案**]，
        然後選取在上一個步驟中下載的檔案。

    4.  選取檔案之後，按一下 [**上傳**]。

4.  按一下 [**+ 新建**] 來建立新的託管服務。 *hosted service*是應用程式在部署至 Azure 時的裝載容器。如需詳細資訊，請參閱[為 Azure 建立託管服務概觀][]。

	![create a new deployment](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_createdeployment.png)

5.  系統將會提示您輸入新託管服務的名稱，以及執行個體數目、主機 OS 和資料中心這類組態選項。指定的部署名稱將會用作 Azure 中的託管服務名稱。在 Azure 系統內，此名稱必須是唯一的。
	
	![create a new hosted service](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_new_hosted_service_settings.png)

	> [AZURE.NOTE] 在 [**新增部署目標**] 對話方塊中，任何現有的 Azure 託管服務都會列示於 [**選擇現有的部署**] 區段中；選取現有的託管服務，將會導致此專案部署至該服務。

	> [AZURE.NOTE] 選取 [**啟用 RDP**] 並提供使用者名稱和密碼就會為您的部署啟用遠端桌面。


## 部署至 Azure 生產環境

1.  選取您在先前步驟中建立的部署。隨即會出現對話方塊，
    其中提供此部署的相關資訊，以及
    在部署至 Windows Azure 之後所要使用的生產 URL
    。

	![select a deployment](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_select_deployment.png)

2.  選取 [**部署到生產環境**]。

3.  按一下 [**部署**] 來開始部署。

4.  如果這是您第一次將此專案部署至 Azure，您會收到「**找不到 web.config**」的錯誤。請選取 [**是**] 來建立此檔案。這會新增  'Web.cloud.config' 檔案至您的專案。
	
	![no web.config file found message](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_no_web_config.png)

5.  如果這是您第一次將此專案部署至 Azure，您會收到「** 'csdef' 檔案不存在**」的錯誤。選取 [**是**] 建立 .csdef 檔案。這會將 'ServiceDefinition.csdef' 檔案新增至專案。    ServiceDefinition.csdef 是發佈應用程式所需的 Azure 特定檔案。如需詳細資訊，請參閱[為 Azure 建立託管服務概觀][]。

6.  系統將會提示您選取此應用程式的執行個體大小。選取 [**小型**]，然後按一下 [**建立**]。如需有關 Azure VM 大小的詳細資訊，請參閱[設定雲端服務的大小][]。

	![specify csdef file values](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_createcsdef.png)

7.  部署項目將會顯示部署程序的狀態。部署完成後，將會顯示為 [**使用中**]。

	![deployment status](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_deployment_status.png)

	> [AZURE.NOTE] 會將 GUID 指派給透過 Cloud 9 IDE 所部署的專案，以做為 Azure 中的部署名稱。

8.  部署對話方塊包括生產 URL 的連結。部署完成時，請按一下 URL，以瀏覽至您在 Azure 中執行的應用程式。

	![Azure production URL link](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_production_url.png)

## 更新應用程式

變更應用程式時，可以使用 Cloud9，將更新過的應用程式部署至相同的 Azure 託管服務。

1.  在 server.js 檔案中，更新程式碼，以將 "hello azure v2" 列印至螢幕。您可以將現有程式碼取代為下列更新過的程式碼：

        var http = require('http');
        var port = process.env.PORT;
        http.createServer(function(req,res) {
            res.writeHead(200, { 'Content-Type': 'text/plain' });
            res.end('hello azure v2\n');
        }).listen(port);

2.  若要儲存程式碼，請依序選取 [**檔案**] 和 [**儲存**]。

## 將更新部署至 Azure 預備環境

1.  選取 [**部署到預備環境**]。

2.  按一下 [**部署**] 來開始部署。

	每個 Azure 託管服務都支援兩種環境：預備和生產。預備環境與生產環境完全相同，差別在於您只能使用 Azure 所產生的模糊 GUID 型 URL 來存取預備應用程式。您可以使用預備環境來測試應用程式，而且在驗證變更之後，可以執行虛擬 IP (VIP) 交換來將預備版本移動至生產環境 (如本教學課程後面所述)。

3.  將應用程式部署至預備環境時，將會在 [主控台] 輸出中顯示 GUID 型預備 URL (如下面的螢幕擷取畫面所示)。按一下 URL，即可在瀏覽器中開啟預備應用程式。

	![console output showing staging URL](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_staging_console_output.png)

## 使用 VIP 交換，將更新移動至生產環境

當您將服務部署至生產或預備
環境時，服務在該環境中會收到指派的虛擬 IP 位址 (VIP)
。當您想要將服務從預備環境移動到
生產環境時，您可以直接這樣做，而不必
執行 VIP 交換來進行重新部署，以交換預備和生產
部署。VIP 交換可將測試過的預備應用程式放到
生產環境，卻不必讓生產環境停止運作。如需
詳細資訊，請參閱[Azure 部署管理概觀][]。

1.  在 [部署] 對話方塊中，按一下 [**開啟入口網站**] 連結以開啟 
    Azure 管理入口網站。

	![Link from deploy dialog to Azure Management Portal][Link from deploy dialog to Azure Management Portal]

2.  使用您的認證來登入入口網站。

3.  在網頁左側，選取 [*託管服務*]、 儲存體
    帳戶和 [**CDN**]，然後按一下 [**託管服務**]。

	![Azure Management Portal][Azure Management Portal]

	[結果] 窗格會顯示您在 Cloud9 中所指定名稱的託管服務，以及兩個部署，第一個的     [**環境**] 值是 [**預備**]，第二個的值則是 [**生產**]。

4.  若要執行 VIP 交換，請選取託管服務，然後按一下功能區的 [**交換 VIP**]。

	![VIP SWAP](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_portal_vipswap.png)

5.  在出現的 [交換 VIP] 對話方塊中按一下 [**確定**] 。

6.  瀏覽至生產應用程式。您將會看到先前部署至預備環境的應用程式版本現在為生產版本。

	![Production application running on Azure](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_production_on_azure.png)

## 使用遠端桌面

如果您在建立部署時啟用了 RDP 並已指定使用者名稱和密碼，您可以使用遠端桌面連線到您的託管服務，方法是選取特定執行個體，然後選取功能區的 [連線]
。

![Connect to an instance](./media/cloud-services-nodejs-develop-deploy-cloud9/connect.png)

當您按一下 [連線] 時，系統會提示您開啟或下載 .RDP 檔案。此檔案包含連線到遠端桌面工作階段所需的資訊。在 Windows 系統上執行這個檔案，系統就會提示您輸入建立部署時所輸入的使用者名稱和密碼，然後將您連線到所選執行個體的桌面
。

> [AZURE.NOTE] 用來連線到應用程式託管執行個體的 .RDP 檔案只適用於 WIndows 上的 [遠端桌面]
應用程式。

## 停止和刪除應用程式

Azure 會根據每小時的耗用伺服器時間向角色執行個體請款，而且部署應用程式之後即會耗用伺服器時間，即使執行個體未執行並處於已停止狀態也是一樣。此外，
生產和預備部署都會耗用伺服器時間。

Cloud9 著重在提供 IDE，並未提供將應用程式部署至 Azure 之後，停止或刪除應用程式的直接方法。若要刪除 Azure 中所託管的應用程式，請執行下列步驟：

1.  在 [部署] 對話方塊中，按一下 [**開啟入口網站**] 連結來開啟 Azure 管理入口網站。

	![Link from deploy dialog to Azure Management Portal](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_portal_link.png)

2.  使用您的認證來登入入口網站。

3.  在網頁左側，選取 [**託管服務、儲存體帳戶和 CDN**]，然後按一下 [**託管服務**]。

4.  選取預備部署 (以 [**環境**] 值表示)。按一下功能區的 [**刪除**] 來刪除應用程式。

	![delete the deployment](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_deletedeployment.png)

5.  選取生產部署，然後按一下 [**刪除**] 來一併刪除該應用程式。

## 其他資源

-   [Cloud9 文件][]


  [Cloud9 IDE]: http://cloud9ide.com/ 
  [為 Azure 建立託管服務概觀]: http://msdn.microsoft.com/library/windowsazure/jj155995.aspx
  [如何設定虛擬機器大小]: http://msdn.microsoft.com/library/windowsazure/ee814754.aspx
  [Azure 部署管理概觀。]: http://msdn.microsoft.com/library/windowsazure/gg433027.aspx
  [Cloud9 文件]: http://go.microsoft.com/fwlink/?LinkId=241421&clcid=0x409

<!--HONumber=45--> 
