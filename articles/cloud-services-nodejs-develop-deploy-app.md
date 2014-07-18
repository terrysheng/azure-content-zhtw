<properties  linkid="dev-nodejs-getting-started" urlDisplayName="Cloud Service" pageTitle="Node.js Getting Started Guide - Azure Tutorial" metaKeywords="Azure node.js getting started, Azure Node.js tutorial, Azure Node.js tutorial" description="An end-to-end tutorial that helps you develop a simple Node.js web application and deploy it to Azure." metaCanonical="" services="cloud-services" documentationCenter="Node.js" title="Build and deploy a Node.js application to an Azure Cloud Service" authors="larryfr" solutions="" manager="" editor="" />

# 建立 Node.js 應用程式並部署到 Azure 雲端服務

完成此指南後，您將有簡單的 Node.js 應用程式在 Azure 雲端服務中執行。雲端服務是 Azure 中可擴充雲端應用程式的建置組塊。可以個別獨立管理和向外延展應用程式的前端和後端元件。雲端服務提供穩定代管各個角色的強固專用虛擬機器。

如需雲端服務及其相較於 Azure 網站和虛擬機器的詳細資訊，請參閱 [Azure 網站、雲端服務和 VM：使用時機][1]
<div class="dev-callout"><strong>尋求建立簡單的網站？</strong>
<p>如果您只需要簡單的網站前端，請考慮<a  href="../create-a-website-(mac)">使用輕量型 Azure 網站</a>。隨著網站擴大，以及需求改變，您可以很輕易地升級到雲端服務。</p>
</div>

 

按照本教學課程進行，您將建立在 Web 角色內代管的簡單 Web 應用程式。您將使用計算模擬器在本機測試您的應用程式，然後使用
PowerShell 命令列工具部署應用程式。

以下螢幕擷取畫面顯示完成的應用程式：

![A browser window displaying the hello world page.The URL indicates the page is hosted on Azure.](https://wacomdpsstablestorage.blob.core.windows.net/articlesmedia/demo-ppe.windowsazure.com/en-us/documentation/articles/cloud-services-nodejs-develop-deploy-app/20140107035927/node21.png)

## 建立新節點應用程式

執行下列工作，建立新的 Azure 雲端服務專案以及基本的 Node.js 樣板：

1.  從 **開始** 功能表或 **開始** 畫面中，搜尋 **Azure PowerShell**。最後，以滑鼠右鍵按一下 **Azure PowerShell**，然後選取
    **以系統管理員身分執行**。
    
    ![Azure PowerShell
    圖示](./media/cloud-services-nodejs-develop-deploy-app/azure-powershell-start.png)
    
    [WACOM.INCLUDE
    [install-dev-tools](../includes/install-dev-tools.md)]

2.  在 C 磁碟機上建立新的 **node** 目錄，並切換到 c:\\node 目錄：
    
    ![顯示命令「mkdir c:\\node」及「cd
    node」的命令提示字元。](./media/cloud-services-nodejs-develop-deploy-app/getting-started-6.png)

3.  輸入下列 Cmdlet 建立新的解決方案：
    
        PS C:\node> New-AzureServiceProject helloworld
        
        您將看見下列回應：
    
    ![New-AzureService helloworld
    命令的結果](./media/cloud-services-nodejs-develop-deploy-app/node9.png)
    
    **New-AzureServiceProject** Cmdlet 可對於建立將發佈到雲端服務的新 Azure Node 應用程式產生基本結構。其中包含發佈到 Azure 所需的組態檔。該 Cmdlet 也會將您的工作目錄變更為服務的目錄。
    
    **New-AzureServiceProject** Cmdlet 建立的檔案是：
    
    * **ServiceConfiguration.Cloud.cscfg**、**ServiceConfiguration.Local.cscfg** 和 **ServiceDefinition.csdef** 是發佈您的應用程式所需的 Azure 特定檔案。
    
    如需這些檔案的詳細資訊，請參閱[雲端服務][2]。
    
    * **deploymentSettings.json** 儲存 Azure PowerShell 部署 Cmdlet 使用的本機設定。

4.  輸入下列命令，使用 **Add-AzureNodeWebRole Cmdlet** 新增 Web 角色。
    
        PS C:\node\helloworld> Add-AzureNodeWebRole
    
    您將看見下列回應：
    
    ![Add-AzureNodeWebRole
    命令的輸出。](./media/cloud-services-nodejs-develop-deploy-app/node11.png)
    
    **Add-AzureNodeWebRole** Cmdlet 會建立應用程式的新目錄，並產生基本 Node.js 應用程式的樣板。它也會修改上一個步驟中建立的
    **ServiceConfiguration.Cloud.csfg**、**ServiceConfiguration.Local.csfg** 和 **ServiceDefinition.csdef** 檔案，新增新角色的組態項目。
	<div class="dev-callout">

	<b>注意</b>
	<p>依預設，如果您不提供角色名稱，將為您建立一個名稱。您可以提供名稱作為 <b>Add-AzureNodeWebRole</b> 的第一個參數。例如，`Add-AzureNodeWebRole MyRole`</p>
	</div>


5.  使用下列命令瀏覽到 **WebRole1** 目錄，然後在記事本中開啟 **server.js** 檔案。
    
    PS C:\\node\\helloworld> cd WebRole1 PS
    C:\\node\\helloworld\\WebRole1> notepad server.js
    
    **server.js** 檔案是由 **Add-AzureNodeWebRole** Cmdlet 建立的，其中包含下列啟動程式碼。這段程式碼類似於 [nodejs.org][3] 網站的「Hello World」範例，不過：

 - 連接埠經過變更，允許應用程式尋找         雲端環境指派給它的正確連接埠。

 - 已移除控制台記錄。     
	![Notepad displaying the contents of server.js](./media/cloud-services-nodejs-develop-deploy-app/node13.png)

## 在模擬器本機執行您的應用程式

Azure SDK 安裝的其中一個工具是 Azure 計算模擬器，可以讓您在本機測試您的應用程式。計算模擬器可模擬您的應用程式部署到雲端執行時的環境。執行下列步驟在模擬器中測試應用程式。

1.  關閉記事本，並切換回 Windows PowerShell 視窗。輸入下列 Cmdlet 在模擬器中執行您的服務：
    
        PS C:\node\helloworld\WebRole1> Start-AzureEmulator -Launch
    
    **-Launch**
    參數可指定工具應該自動開啟瀏覽器視窗，並且在應用程式在模擬器中執行時立即顯示應用程式。瀏覽器將開啟，並顯示「Hello World」，如以下螢幕擷取畫面所示。這表示服務在計算模擬器中執行，並且正常運作。
    
    ![顯示 Hello World
    網頁的網頁瀏覽器](./media/cloud-services-nodejs-develop-deploy-app/node14.png)

2.  若要停止計算模擬器，請使用 **Stop-AzureEmulator** 命令：
    
    PS C:\\node\\helloworld\\WebRole1> Stop-AzureEmulator

## 將應用程式部署到 Azure

    [WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]

### <a  id="download_publishing_settings" ></a>下載 Azure 發佈設定

為了將應用程式部署到 Azure，您必須先下載您 Azure 訂閱的發佈設定。下列步驟將引導您進行這個程序：

1.  從 Windows PowerShell 視窗中，執行下列 Cmdlet 啟動下載頁面：
    
        PS C:\node\helloworld\WebRole1> Get-AzurePublishSettingsFile
    
    這將使用瀏覽器瀏覽到發佈設定下載頁面。可能提示您使用 Microsoft 帳戶登入。若是如此，請使用與您的 Azure 訂閱相關聯的帳戶。
    
    將下載的設定檔儲存到您可以輕鬆存取的檔案位置。

2.  在 Azure PowerShell 視窗中使用下列 Cmdlet，設定 Node.js Cmdlet 的 Windows PowerShell 使用您下載的 Azure 發佈設定檔：
    
        PS C:\node\helloworld\WebRole1> Import-AzurePublishSettingsFile [檔案的路徑]
	<div class="dev-callout">

	<b>注意</b>
	<p>在匯入發佈設定之後，請考慮刪除下載的 .publishSettings 檔案，因為它包含了他人可用來存取您帳戶的資訊。</p>
	</div>

### 發佈應用程式

1.  使用 **Publish-AzureServiceProject** Cmdlet 發佈應用程式，如下所示。
    
        PS C:\node\helloworld\WebRole1> Publish-AzureServiceProject -ServiceName NodeHelloWorld -Location "East US" -Launch
    
    * **servicename** 參數會指定這個部署使用的名稱。這必須是唯一名稱，否則發佈程序將失敗。
    
    * **location** 參數會指定將代管應用程式的資料中心。若要查看可用資料中心的清單，請使用 **Get-AzureLocation** Cmdlet。
    
    * 部署完成後，**launch** 參數將啟動瀏覽器，並瀏覽到代管的服務。
    
    發佈成功後，您將看見如下的回應：
    
    ![Publish-AzureService
    命令的輸出](./media/cloud-services-nodejs-develop-deploy-app/node19.png)
    
    **Publish-AzureServiceProject** Cmdlet 將執行下列步驟：

2.  建立將部署到 Azure 的封裝。該封裝包含 node.js 應用程式資料夾中所有的檔案。

3.  建立新的**儲存體帳戶** (如果不存在)。Azure 儲存體帳戶將用來在部署期間儲存應用程式封裝。部署完成後，即可安全刪除儲存體帳戶。

4.  建立新的**雲端服務** (如果不存在)。**雲端服務**是應用程式部署到 Azure 時代管應用程式的容器。如需詳細資訊，請參閱[雲端服務][2]。

5.  將部署封裝發佈到 Azure。
    
    > [WACOM.NOTE] 應用程式部署並且在第一次發佈時可供使用，需要 5 到 7 分鐘的時間。
    
    部署完成後，瀏覽器視窗將開啟，並瀏覽到雲端服務。
    
    ![A browser window displaying the hello world page.The URL indicates the page is hosted on Azure.](./media/cloud-services-nodejs-develop-deploy-app/node21.png)
    
    您的應用程式現在就在 Azure 上執行！

## 停止並刪除您的應用程式

部署您的應用程式後，您可能會想要將它停用，以便避免額外的成本。Azure 會對於 Web 角色執行個體的伺服器使用時間時數計費。部署應用程式之後，即使執行個體未執行而在停止狀態中，也會使用伺服器時間。

1.  在 Windows PowerShell 視窗中，使用下列 Cmdlet 停止上一個小節中建立的服務部署：
    
        PS C:\node\helloworld\WebRole1> Stop-AzureService
    
    停止服務可能需要幾分鐘的時間。服務停止時，您將收到表示停止的訊息。
    
    ![Stop-AzureService
    命令的狀態](./media/cloud-services-nodejs-develop-deploy-app/node48.png)

2.  若要刪除服務，請呼叫下列 Cmdlet：
    
        PS C:\node\helloworld\WebRole1> Remove-AzureService
    
    出現提示時，輸入 **Y** 以刪除服務。
    
    刪除服務可能需要幾分鐘的時間。刪除服務後，您將收到表示已刪除服務的訊息。
    
    ![Remove-AzureService
    命令的狀態](./media/cloud-services-nodejs-develop-deploy-app/node49.png)
	<div class="dev-callout">

	<b>注意</b>
	<p>刪除服務不會刪除初次發佈服務時建立的儲存體帳戶，而且將持續對使用的儲存體計費。如需刪除儲存體帳戶的詳細資訊，請參閱<a href="http://msdn.microsoft.com/en-us/library/windowsazure/hh531562.aspx">如何從 Azure 訂用帳戶刪除儲存體帳戶</a>.。</p>
	</div>
	</div>



[1]: http://msdn.microsoft.com/en-us/library/windowsazure/jj218759.aspx
[2]: http://msdn.microsoft.com/en-us/library/windowsazure/jj155995.aspx
[3]: http://nodejs.org/
[4]: http://msdn.microsoft.com/en-us/library/windowsazure/hh531562.aspx