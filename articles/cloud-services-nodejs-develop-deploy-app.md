<properties 
	pageTitle="Node.js 入門指南 - Azure 教學課程" 
	description="協助您開發簡單 Node.js Web 應用程式並部署至 Azure 的端對端教學課程。" 
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






# 建立 Node.js 應用程式並部署到 Azure 雲端服務

完成本指南後，您在 Azure 雲端服務中會執行簡單的 Node.js 應用程式。 
雲端服務是 Azure 中可擴充之雲端應用程式的建置區塊。 
它們可個別和獨立
管理和向外延展應用程式的前端與後端元件。  雲端服務 
提供強固的專屬虛擬機器來穩定代管各個角色。

如需雲端服務的詳細資訊，及其與 Azure 網站和虛擬機器的比較資訊，請參閱 [Azure 網站、雲端服務和虛擬機器的比較](http://azure.microsoft.com/documentation/articles/choose-web-site-cloud-service-vm/) (英文)。

>[AZURE.TIP] 您正在尋求建立簡單的網站嗎？如果您的案例只涉及簡單的網站前端，請考慮 <a href="/zh-tw/documentation/articles/web-sites-nodejs-develop-deploy-mac/">使用輕量型「Azure 網站」</a>。 隨著網站擴大，以及需求改變，您可以很輕易地升級到雲端服務。


按照本教學課程進行，您將建立在 Web 角色內代管的簡單 Web 應用程式。您
將使用計算模擬器在本機測試您的應用程式，然後
使用 PowerShell 命令列工具部署它。

完成之應用程式的螢幕擷取畫面如下：

<p><img src="https://wacomdpsstablestorage.blob.core.windows.net/articlesmedia/demo-ppe.windowsazure.com/zh-tw/documentation/articles/cloud-services-nodejs-develop-deploy-app/20140107035927/node21.png" alt="A browser window displaying the hello world page. The URL indicates the page is hosted on Azure.">
</p>



## 建立新節點應用程式

執行下列工作，建立新的 Azure 雲端服務專案以及基本的 Node.js 樣板：

1. 從 **[開始]** 功能表或 **[開始]** 畫面，搜尋 **Azure PowerShell**。最後，在 [**Azure PowerShell**] 上按一下滑鼠右鍵，然後選取 [**以系統管理員身分執行**]。

	![Azure PowerShell icon][powershell-menu]

	[AZURE.INCLUDE [install-dev-tools](../includes/install-dev-tools.md)]


2.  在 C 磁碟機上建立新的 **node** 目錄，並切換到 c:\node 目錄：
	
	![A command prompt displaying the commands 'mkdir c:\\node' and 'cd node'.][mkdir]

3.  輸入下列 Cmdlet 建立新的解決方案：

        PS C:\node> New-AzureServiceProject helloworld

    您將看見下列回應：

	![The result of the New-AzureService helloworld command](./media/cloud-services-nodejs-develop-deploy-app/node9.png)

	**New-AzureServiceProject** Cmdlet 會產生基本結構，建立將發行至雲端服務的新 Azure 節點應用程式。其中包含發佈到 Azure 所需的組態檔。該 Cmdlet 也會將您的工作目錄變更為服務的目錄。

	**New-AzureServiceProject** Cmdlet 建立的檔案為：

	-   **ServiceConfiguration.Cloud.cscfg**、
        **ServiceConfiguration.Local.cscfg** 和 **ServiceDefinition.csdef** 是
        發行應用程式所需的 Azure 特定檔案
        。
		
	如需這些檔案的詳細資訊，請參閱
        [建立 Azure 託管服務概觀][]。

	-   **deploymentSettings.json** 儲存
        Azure PowerShell 部署 Cmdlet 所使用的本機設定。

4.  輸入下列命令，使用
    **Add-AzureNodeWebRole Cmdlet** 加入新的 Web 角色：

        PS C:\node\helloworld> Add-AzureNodeWebRole

	您將看見下列回應：

	![The output of the Add-AzureNodeWebRole command.](./media/cloud-services-nodejs-develop-deploy-app/node11.png)

	**Add-AzureNodeWebRole** Cmdlet 會為應用程式建立新目錄，並為基本 Node.js 應用程式產生 Scaffolding。它也會修改在上一個步驟中建立的 **ServiceConfiguration.Cloud.csfg**、**ServiceConfiguration.Local.csfg** 及 **ServiceDefinition.csdef** 檔案，來為新角色新增組態項目。

	> [AZURE.NOTE] 依預設，如果您不提供角色名稱，將為您建立一個名稱。您可以提供名稱做為 **Add-AzureNodeWebRole** 的第一個參數。例如，`Add-AzureNodeWebRole MyRole`

5.  使用下列命令來瀏覽至 **WebRole1** 目錄，然後在記事本中開啟 **server.js** 檔案。 

	PS C:\\node\\helloworld> cd WebRole1
        PS C:\node\helloworld\WebRole1> notepad server.js

	**server.js** 檔案是由 **Add-AzureNodeWebRole** Cmdlet 所建立，並且包含下列起始程式碼。這段程式碼類似於 [nodejs.org][] 網站的 "Hello World" 範例，不過：

   	-   已變更此連接埠，允許應用程式尋找 
        雲端環境指派給它的正確連接埠。
   	-   已移除主控台記錄。

	![Notepad displaying the contents of server.js](./media/cloud-services-nodejs-develop-deploy-app/node13.png)

## 在模擬器本機執行您的應用程式

Azure SDK 安裝的其中一項工具是 Azure
計算模擬器，可讓您測試本機的應用程式。此
計算模擬器會模擬將應用程式部署到雲端時，執行該應用程式的環境。
執行下列步驟在模擬器中測試應用程式。

1.  關閉記事本，並切換回 Windows PowerShell 視窗。
  	輸入下列 Cmdlet 在模擬器中執行您的服務：

        PS C:\node\helloworld\WebRole1> Start-AzureEmulator -Launch

	**-Launch** 參數可指定工具應該在應用程式於模擬器中執行時，便立即自動開啟瀏覽器視窗並顯示應用程式。瀏覽器將開啟，並顯示「Hello World」，如以下螢幕擷取畫面所示。這表示服務在計算模擬器中執行，並且正常運作。

	![A web browser displaying the Hello World web page](./media/cloud-services-nodejs-develop-deploy-app/node14.png)

2.  若要停止計算模擬器，請使用 **Stop-AzureEmulator** 命令：
	
	PS C:\node\helloworld\WebRole1> Stop-AzureEmulator

## 將應用程式部署到 Azure

	[AZURE.INCLUDE [create-account-note](../includes/create-account-note.md)]



### <a id="download_publishing_settings"></a>下載 Azure 發佈設定

為了將應用程式部署到 Azure，您必須先下載您 Azure 訂閱的發佈設定。下列步驟將引導您進行這個程序：

1.  從 Windows PowerShell 視窗中，執行下列 Cmdlet 啟動下載頁面：

        PS C:\node\helloworld\WebRole1> Get-AzurePublishSettingsFile

	這將使用瀏覽器瀏覽到發佈設定下載頁面。可能提示您使用 Microsoft 帳戶登入。若是如此，請使用與您的 Azure 訂閱相關聯的帳戶。

	將下載的設定檔儲存到您可以輕鬆存取的檔案位置。

2.  在 Azure PowerShell 視窗中使用下列 Cmdlet，設定 Node.js Cmdlet 的 Windows PowerShell 使用您下載的 Azure 發佈設定檔：

        PS C:\node\helloworld\WebRole1> Import-AzurePublishSettingsFile [path to file]


	> [AZURE.NOTE] 在匯入發佈設定之後，請考慮刪除下載的 .publishSettings 檔案，因為它包含了他人可用來存取您帳戶的資訊。
    

### 發佈應用程式

1.  使用 **Publish-AzureServiceProject** Cmdlet 發行應用程式，
    如下所示。

        PS C:\node\helloworld\WebRole1> Publish-AzureServiceProject -ServiceName NodeHelloWorld -Location "East US" -Launch

	- **servicename** 參數可指定這個部署所使用的名稱。這必須是唯一名稱，否則發佈程序將失敗。

	- **location** 參數會指定將裝載應用程式的資料中心。若要查看可用資料中心的清單，請使用 **Get-AzureLocation** Cmdlet。

	- 部署完成之後，**launch** 參數便會啟動瀏覽器，並瀏覽到託管服務。

	發佈成功後，您將看見如下的回應：

	![The output of the Publish-AzureService command](./media/cloud-services-nodejs-develop-deploy-app/node19.png)

	**Publish-AzureServiceProject** Cmdlet 會執行下列步驟：

1.  建立將部署到 Azure 的封裝。該封裝包含 node.js 應用程式資料夾中所有的檔案。

2.  建立新的**「儲存體帳戶」**(如果不存在)。Azure 儲存體帳戶將用來在部署期間儲存應用程式封裝。部署完成後，即可安全刪除儲存體帳戶。

3.  建立新的**「雲端服務」**(如果不存在)。**「雲端服務」**是應用程式部署到 Azure 時，裝載應用程式的容器。如需詳細資訊，請參閱[建立 Azure 託管服務概觀][]。

4.  將部署封裝發佈到 Azure。


	> [AZURE.NOTE]
	> 第一次發佈應用程式時，從部署到可供使用需要 5 到 7 分鐘的時間。

	部署完成後，瀏覽器視窗將開啟，並瀏覽到雲端服務。


	![A browser window displaying the hello world page. The URL indicates the page is hosted on Azure.](./media/cloud-services-nodejs-develop-deploy-app/node21.png)

	您的應用程式現在就在 Azure 上執行！


## 停止並刪除您的應用程式

部署您的應用程式後，您可能會想要將它停用，以便避免額外的成本。Azure 會就每小時伺服器時間所使用的 Web 角色執行個體數進行收費。一旦部署應用程式，即會耗用伺服器時間，即使執行個體不在執行中，並處於停止狀態也一樣。

1.  在 Windows PowerShell 視窗中，使用下列 Cmdlet 停止上一個小節中建立的服務部署：

        PS C:\node\helloworld\WebRole1> Stop-AzureService

	停止服務可能需要幾分鐘的時間。服務停止時，您將收到表示停止的訊息。

	![The status of the Stop-AzureService command](./media/cloud-services-nodejs-develop-deploy-app/node48.png)

2.  若要刪除服務，請呼叫下列 Cmdlet：

        PS C:\node\helloworld\WebRole1> Remove-AzureService

	出現提示時，輸入 **Y** 以刪除服務。

	刪除服務可能需要幾分鐘的時間。刪除服務後，您將收到表示已刪除服務的訊息。

	![The status of the Remove-AzureService command](./media/cloud-services-nodejs-develop-deploy-app/node49.png)

	> [AZURE.NOTE] 刪除服務不會刪除初次發佈服務時建立的儲存體帳戶，而且將持續對使用的儲存體計費。如需刪除儲存體帳戶的詳細資訊，請參閱[如何從 Azure 訂用帳戶刪除儲存體帳戶](http://msdn.microsoft.com/library/windowsazure/hh531562.aspx)。


[The Windows Start menu with the Azure SDK Node.js entry expanded]: ./media/cloud-services-nodejs-develop-deploy-app/azure-powershell-menu.png
[mkdir]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-6.png
[nodejs.org]: http://nodejs.org/
[A directory listing of the helloworld folder.]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-7.png
[建立 Azure 託管服務概觀]: http://msdn.microsoft.com/library/windowsazure/jj155995.aspx
[A directory listing of the WebRole1 folder]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-8.png
[The menu displayed when right-clicking the Azure emulator from the task bar.]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-11.png
[A browser window displaying http://www.windowsazure.com/ with the Free Trial link highlighted]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-12.png
[A browser window displaying the liveID sign in page]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-13.png
[Internet Explorer displaying the save as dialog for the publishSettings file.]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-14.png

[The full status output of the Publish-AzureService command]: ./media/cloud-services-nodejs-develop-deploy-app/node20.png
[powershell-menu]: ./media/cloud-services-nodejs-develop-deploy-app/azure-powershell-start.png

<!--HONumber=45--> 
