<properties 
	pageTitle="Node.js 入門指南 - Azure 教學課程" 
	description="了解如何建立簡單的 Node.js Web 應用程式，並將它部署至 Azure 雲端服務。" 
	services="cloud-services" 
	documentationCenter="nodejs" 
	authors="MikeWasson" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="hero-article" 
	ms.date="02/24/2015" 
	ms.author="mwasson"/>


# 建立 Node.js 應用程式並部署到 Azure 雲端服務

> [AZURE.SELECTOR]
- [Node.js](cloud-services-nodejs-develop-deploy-app.md)
- [.NET](cloud-services-dotnet-get-started.md)

本教學課程示範如何建立在 Azure 雲端服務中執行的簡單 Node.js 應用程式。雲端服務是 Azure 中可擴充雲端應用程式的建置組塊。可以個別獨立管理和向外延展應用程式的前端和後端元件。雲端服務提供穩定代管各個角色的強固專用虛擬機器。

如需雲端服務的詳細資訊，及其與 Azure 網站和虛擬機器的比較資訊，請參閱 [Azure 網站、雲端服務和虛擬機器的比較](http://azure.microsoft.com/documentation/articles/choose-web-site-cloud-service-vm/) (英文)。

>[AZURE.TIP] 尋求建置簡單的網站？如果您只需要簡單的網站前端，請考慮使用<a href="/documentation/articles/web-sites-nodejs-develop-deploy-mac/">輕量型 Azure 網站</a>。隨著網站擴大，以及需求改變，您可以很輕易地升級到雲端服務。


按照本教學課程進行，您將建立在 Web 角色內代管的簡單 Web 應用程式。您將使用計算模擬器在本機測試您的應用程式，然後使用 PowerShell 命令列工具部署應用程式。

應用程式是簡單的 "hello world" 應用程式：

<p><img src="https://wacomdpsstablestorage.blob.core.windows.net/articlesmedia/demo-ppe.windowsazure.com/documentation/articles/cloud-services-nodejs-develop-deploy-app/20140107035927/node21.png" alt="顯示 Hello World 頁面的瀏覽器視窗。URL 指出此頁面是裝載在 Azure 上。">
</p>

## 必要條件

> [AZURE.NOTE] 本教學課程使用 Azure PowerShell (需要 Windows)。

- 安裝 Azure SDK for Node.js：<a href="http://go.microsoft.com/fwlink/?LinkId=254279">Windows 安裝程式</a> 

- 安裝並設定 [Azure Powershell](install-configure-powershell.md)。


## 建立 Azure 雲端服務專案

執行下列工作，建立新的 Azure 雲端服務專案以及基本的 Node.js 樣板：


1. 以系統管理員的身分執行 **Azure PowerShell**。(從 [開始] 功能表或 [開始] 畫面，搜尋 **Azure PowerShell**。)

2.  輸入下列 PowerShell Cmdlet 來建立專案：

        New-AzureServiceProject helloworld

	![The result of the New-AzureService helloworld command](./media/cloud-services-nodejs-develop-deploy-app/node9.png)

	**New-azureserviceproject** 指令程式會產生發佈 Node.js 應用程式至雲端服務的基本結構。其中包含發佈到 Azure 所需的組態檔。該 Cmdlet 也會將您的工作目錄變更為服務的目錄。

	Cmdlet 會建立下列檔案：

	-   **ServiceConfiguration.Cloud.cscfg**、 **ServiceConfiguration.Local.cscfg** 和 **ServiceDefinition.csdef**：發佈您的應用程式所需的 Azure 特定檔案。如需詳細資訊，請參閱[為 Azure 建立託管服務概觀][]。

	-   **deploymentSettings.json**: 儲存 Azure PowerShell 部署 Cmdlet 使用的本機設定。

4.  輸入下列命令以新增 Web 角色：

        Add-AzureNodeWebRole
	
	![The output of the Add-AzureNodeWebRole command.](./media/cloud-services-nodejs-develop-deploy-app/node11.png)

	**Add-azurenodewebrole** Cmdlet 會建立基本的 Node.js 應用程式。它也會修改 **.csfg** 和 **.csdef** 檔案以加入新角色的組態項目。

	> [AZURE.NOTE] 如果您未指定角色名稱，系統會使用預設名稱。您可以提供名稱做為第一個 Cmdlet 參數：`Add-AzureNodeWebRole MyRole`


Node.js app 定義於 **server.js** 檔案，位在 Web 角色(預設為 **WebRole1**) 的目錄中。程式碼如下：

	var http = require('http');
	var port = process.env.port || 1337;
	http.createServer(function (req, res) {
	    res.writeHead(200, { 'Content-Type': 'text/plain' });
	    res.end('Hello World\n');
	}).listen(port);

此程式碼基本上和 [nodejs.org][] 網站上的 "Hello World" 範例一樣，但它使用由雲端環境指派的連接埠號碼。


## 在本機模擬器執行應用程式

Azure SDK 安裝的其中一項工具是 Azure 計算模擬器，可讓您測試本機的應用程式。這個計算模擬器會模擬將應用程式部署到雲端時，執行該應用程式的環境。 

1.  輸入下列 Azure PowerShell Cmdlet 以在模擬器中執行您的服務：

        Start-AzureEmulator -Launch

	使用 **-Launch** 參數，當 Web 角色在模擬器中執行時，便會自動開啟瀏覽器視窗。應會咼起瀏覽器視窗，並顯示「Hello World」，如以下螢幕擷取畫面所示。 

	![A web browser displaying the Hello World web page](./media/cloud-services-nodejs-develop-deploy-app/node14.png)

2.  若要停止計算模擬器，請使用 **Stop-AzureEmulator** Cmdlet ：
	
		Stop-AzureEmulator

## 將應用程式部署至 Azure

	[AZURE.INCLUDE [create-account-note](../includes/create-account-note.md)]


### 下載 Azure 發佈設定

若要將應用程式部署到 Azure，您必須先下載您 Azure 訂閱的發佈設定。 

1.  執行下列 Azure PowerShell Cmdlet：

        Get-AzurePublishSettingsFile

	這將使用瀏覽器瀏覽到發佈設定下載頁面。可能提示您使用 Microsoft 帳戶登入。若是如此，請使用與您的 Azure 訂閱相關聯的帳戶。

	將下載的設定檔儲存到您可以輕鬆存取的檔案位置。

2.  執行下列 Cmdlet 來匯入您所下載的發佈設定檔：

        Import-AzurePublishSettingsFile [path to file]


	> [AZURE.NOTE] 在匯入發佈設定之後，請考慮刪除下載的 .publishSettings 檔案，因為它包含了可能讓他人存取您帳戶的資訊。
    

### 發行應用程式

若要發佈，請執行 **Publish-azureserviceproject** Cmdlet ，如下所示：

    Publish-AzureServiceProject -ServiceName NodeHelloWorld -Location "East US" -Launch

- **-ServiceName** 指定部署的名稱。這必須是唯一名稱，否則發佈程序將失敗。

- **-Location** 會指定將裝載應用程式的資料中心。若要查看可用資料中心的清單，請使用 **Get-AzureLocation** Cmdlet。

- **-Launch** 會在部署完成之後開啟瀏覽器視窗，並瀏覽至託管服務。

發佈成功後，您將看見如下的回應：

![The output of the Publish-AzureService command](./media/cloud-services-nodejs-develop-deploy-app/node19.png)

> [AZURE.NOTE]
> 第一次發佈應用程式時，從部署到可供使用需要 5 到 7 分鐘的時間。

部署完成後，瀏覽器視窗將開啟，並瀏覽到雲端服務。


![A browser window displaying the hello world page. The URL indicates the page is hosted on Azure.](./media/cloud-services-nodejs-develop-deploy-app/node21.png)

您的應用程式現在就在 Azure 上執行！

**Publish-AzureServiceProject** Cmdlet 會執行下列步驟：

1.  建立部署的封裝。該封裝包含 node.js 應用程式資料夾中所有的檔案。

2.  建立新的「儲存體帳戶」(如果不存在)。Azure 儲存體帳戶將用來在部署期間儲存應用程式封裝。部署完成後，即可安全刪除儲存體帳戶。

3.  建立新的「雲端服務」(如果不存在)。「雲端服務」是應用程式部署到 Azure 時，裝載應用程式的容器。如需詳細資訊，請參閱[為 Azure 建立託管服務概觀][]。

4.  將部署封裝發佈到 Azure。



## 停止並刪除您的應用程式

部署您的應用程式後，您可能會想要將它停用，以便避免額外的成本。Azure 會就每小時伺服器時間所使用的 Web 角色執行個體數進行收費。一旦部署應用程式，即會耗用伺服器時間，即使執行個體不在執行中，並處於停止狀態也一樣。

1.  在 Windows PowerShell 視窗中，使用下列 Cmdlet 停止上一個小節中建立的服務部署：

        Stop-AzureService

	停止服務可能需要幾分鐘的時間。服務停止時，您將收到表示停止的訊息。

	![The status of the Stop-AzureService command](./media/cloud-services-nodejs-develop-deploy-app/node48.png)

2.  若要刪除服務，請呼叫下列 Cmdlet：

        Remove-AzureService

	出現提示時，輸入 **Y** 以刪除服務。

	刪除服務可能需要幾分鐘的時間。刪除服務後，您將收到表示已刪除服務的訊息。

	![The status of the Remove-AzureService command](./media/cloud-services-nodejs-develop-deploy-app/node49.png)

	> [AZURE.NOTE] 刪除服務不會刪除初次發佈服務時建立的儲存體帳戶，而且將持續對使用的儲存體計費。如需刪除儲存體帳戶的詳細資訊，請參閱[如何從 Azure 訂用帳戶刪除儲存體帳戶](http://msdn.microsoft.com/library/windowsazure/hh531562.aspx)。


[mkdir]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-6.png
[nodejs.org]: http://nodejs.org/
[為 Azure 建立託管服務概觀]: http://msdn.microsoft.com/library/windowsazure/jj155995.aspx
[從工作列在 Azure 模擬器上按一下滑鼠右鍵時顯示的功能表。]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-11.png
[顯示 LiveID 登入頁面的瀏覽器視窗]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-13.png

[Publish-AzureService 命令的完整狀態]: ./media/cloud-services-nodejs-develop-deploy-app/node20.png
[如何從 Azure 訂用帳戶刪除儲存體帳戶]: https://www.windowsazure.com/manage/services/storage/how-to-manage-a-storage-account/
[powershell-menu]: ./media/cloud-services-nodejs-develop-deploy-app/azure-powershell-start.png


<!--HONumber=52-->