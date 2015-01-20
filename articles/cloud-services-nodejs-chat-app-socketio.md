<properties urlDisplayName="App Using Socket.IO" pageTitle="使用 Socket.io 的 Node.js 應用程式 - Azure 教學課程" metaKeywords="Azure Node.js socket.io 教學課程, Azure Node.js socket.io, Azure Node.js 教學課程" description="示範在裝載於 Azure 的 node.js 應用程式中使用 socket.io 的教學課程。" metaCanonical="" services="cloud-services" documentationCenter="nodejs" title="Build a Node.js Chat Application with Socket.IO on an Azure Cloud Service" authors="larryfr" solutions="" manager="wpickett" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="wpickett" />





# 在 Azure 雲端服務上使用 Socket.IO 建立 Node.js 交談應用程式

Socket.IO 提供 node.js 伺服器和用戶端之間的即時通訊。本教學課程引導您將 socket.IO 型交談應用程式裝載於 Azure 上。如需 Socket.IO 的詳細資訊，請參閱 <a href="http://socket.io/">http://socket.io/</a>。

完成之應用程式的螢幕擷取畫面如下：
	
![A browser window displaying the service hosted on Azure][completed-app]  

## 建立雲端服務專案

下列步驟會建立雲端服務專案來裝載 Socket.IO 應用程式。

1. 從 [開始] 功能表****或 [開始] 畫面****，搜尋 **Azure PowerShell**。最後，在 [Azure PowerShell]**** 上按一下滑鼠右鍵，然後選取 [以系統管理員身分執行]****。

	![Azure PowerShell icon][powershell-menu]

	[WACOM.INCLUDE [install-dev-tools](../includes/install-dev-tools.md)]



2. 切換至 **c:\\node** 目錄，然後輸入下列命令來建立名為 **chatapp** 的新方案和名為 **WorkerRole1** 的背景工作角色：

		PS C:\node> New-AzureServiceProject chatapp
		PS C:\Node> Add-AzureNodeWorkerRole

	您將看見下列回應：

	![The output of the new-azureservice and add-azurenodeworkerrolecmdlets](./media/cloud-services-nodejs-chat-app-socketio/socketio-1.png)

## 下載交談範例

在此專案中，我們將使用來自 [Socket.IO GitHub 存放庫]的交談範例。請執行下列步驟來下載範例，並將它加入至您先前建立的專案。

1.  使用 [複製]**** 按鈕來建立存放庫的本機複本。您也可以使用 [ZIP]**** 按鈕來下載專案。

    ![A browser window viewing https://github.com/LearnBoost/socket.io/tree/master/examples/chat, with the ZIP download icon highlighted][chat-example-view]

3.  瀏覽本機存放庫的目錄結構，直到達到 **examples\\chat**   目錄為止。將此目錄的內容複製到稍早前建立的 **C:\\node\\chatapp\\WorkerRole1** 目錄。

    ![Explorer, displaying the contents of the examples\\chat directory extracted from the archive][chat-contents]

    上方螢幕擷取畫面中醒目提示的項目是從 **examples\\chat** 目錄複製的檔案

4.  在 **C:\\node\\chatapp\\WorkerRole1** 目錄中，刪除 **server.js** 檔案，然後將 **app.js** 檔案重新命名為 **server.js**。這樣會移除先前由 **Add-AzureNodeWorkerRole** Cmdlet 建立的預設 **server.js** 檔案，並以交談範例中的應用程式檔案取代它。

### 修改 Server.js 和安裝模組

在 Azure 模擬器中測試應用程式之前，我們必須稍做一些修改。請對 server.js 檔案執行下列步驟：

1.  在 [記事本] 或其他文字編輯器中開啟 server.js 檔案。

2.  在 server.js 開頭找出 **Module dependencies** 區段，將含有 **sio = require('..//..//lib//socket.io')** 的那一行變更為 **sio = require('socket.io')**，如下所示：

		var express = require('express')
  		, stylus = require('stylus')
  		, nib = require('nib')
		//, sio = require('..//..//lib//socket.io'); //Original
  		, sio = require('socket.io');                //Updated

3.  為了確保應用程式會在正確的連接埠上接聽，請在「記事本」或您喜歡的編輯器中開啟 server.js，然後以 **process.env.port** 取代 **3000** 來變更下列程式碼行，如下所示：

        //app.listen(3000, function () {            //Original
		app.listen(process.env.port, function () {  //Updated
		  var addr = app.address();
		  console.log('   app listening on http://' + addr.address + ':' + addr.port);
		});

儲存 server.js 的變更之後，請使用下列步驟來安裝必要的模組，然後在 Azure 模擬器中測試應用程式：

1.  使用 **Azure PowerShell**，切換至 **C:\\node\\chatapp\\WorkerRole1** 目錄，再使用下列命令來安裝此應用程式所需的模組：

        PS C:\node\chatapp\WorkerRole1> npm install

    這將會安裝 package.json 檔案中列出的模組。命令完成之後，您應該會看到類似下列的輸出： 
    ![The output of the npm install command][The-output-of-the-npm-install-command]

4.  因為此範例原本是 Socket.IO GitHub 儲存機制的一部分，且以相對路徑直接參考 Socket.IO 程式庫，package.json 檔案中並未參考 Socket.IO，因此，我們必須發出下列命令來安裝它：

        PS C:\node\chatapp\WorkerRole1> npm install socket.io -save

### 測試和部署

1.  發出下列命令來啟動模擬器：

        PS C:\node\chatapp\WorkerRole1> Start-AzureEmulator -Launch

2.  當瀏覽器視窗開啟時，請輸入暱稱，然後按 Enter 鍵。這樣可讓您以特定的暱稱來張貼訊息。若要測試多使用者功能，請使用相同 URL 開啟其他瀏覽器視窗，並輸入不同的暱稱。

    ![Two browser windows displaying chat messages from User1 and User2](./media/cloud-services-nodejs-chat-app-socketio/socketio-8.png)

3.  測試應用程式之後，發出下列命令來停止模擬器：

        PS C:\node\chatapp\WorkerRole1> Stop-AzureEmulator

4.  若要將應用程式部署至 Azure，請使用 **Publish-AzureServiceProject** Cmdlet。例如：

        PS C:\node\chatapp\WorkerRole1> Publish-AzureServiceProject -ServiceName mychatapp -Location "East US" -Launch

	<div class="dev-callout">
	<strong>注意</strong>
	<p>務必使用唯一的名稱，否則發行程序會失敗。部署完成之後，瀏覽器會開啟並瀏覽至已部署的服務。</p>
	<p>如果出現錯誤指出匯入的發行設定檔中沒有您所提供的訂閱名稱，則在部署至 Azure 之前，您必須下載並匯入訂閱的發行設定檔。請參閱＜<a href="https://www.windowsazure.com/zh-tw/develop/nodejs/tutorials/getting-started/">建立 Node.js 應用程式並部署至 Azure 雲端服務</a>＞的＜<b>將應用程式部署至 Azure</b>＞一節</p>
	</div>

    ![A browser window displaying the service hosted on Azure][completed-app]

	<div class="dev-callout">
	<strong>注意</strong>
	<p>如果出現錯誤指出匯入的發行設定檔中沒有您所提供的訂閱名稱，則在部署至 Azure 之前，您必須下載並匯入訂閱的發行設定檔。請參閱＜<a href="https://www.windowsazure.com/zh-tw/develop/nodejs/tutorials/getting-started/">建立 Node.js 應用程式並部署至 Azure 雲端服務</a>＞的＜<b>將應用程式部署至 Azure</b>＞一節</p>
	</div>

您的應用程式現在已在 Azure 上執行，並且可以使用
Socket.IO 在不同的用戶端之間轉送訊息。

<div class="dev-callout">
<strong>注意</strong>
<p>為了簡化起見，此範例只限於連線至相同執行個體的使用者之間的交談。這表示如果雲端服務建立兩個背景工作角色執行個體，則使用者只能夠與連線至相同背景工作角色執行個體的其他使用者交談。若要擴充應用程式來處理多個角色執行個體，您可以使用服務匯流排之類的技術，以便跨執行個體來共用 Socket.IO 儲存狀態。相關範例請參閱 <a href="https://github.com/WindowsAzure/azure-sdk-for-node">Azure SDK for Node.js GitHub 存放庫</a>中的服務匯流排佇列和主題使用範例。</p>
</div>

##後續步驟

在本教學課程中，您學到如何建立裝載於 Azure 雲端服務的基本交談應用程式。若要了解如何在「Azure 網站」中裝載此應用程式，請參閱[在 Azure 網站上使用 Socket.IO 建立 Node.js 交談應用程式][chatwebsite]。

  [chatwebsite]: /zh-tw/develop/nodejs/tutorials/website-using-socketio/

  [Azure SLA]: http://www.windowsazure.com/zh-tw/support/sla/
  [Azure SDK for Node.js GitHub 存放庫]: https://github.com/WindowsAzure/azure-sdk-for-node
  [completed-app]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-10.png
  [Azure SDK for Node.js]: https://www.windowsazure.com/zh-tw/develop/nodejs/
  [Node.js Web 應用程式]: https://www.windowsazure.com/zh-tw/develop/nodejs/tutorials/getting-started/
  [Socket.IO GitHub 存放庫]: https://github.com/LearnBoost/socket.io/tree/0.9.14
  [Azure 考量]: #windowsazureconsiderations
  [將交談範例裝載在背景工作角色中]: #hostingthechatexampleinawebrole
  [摘要和後續步驟]: #summary
  [powershell-menu]: ./media/cloud-services-nodejs-chat-app-socketio/azure-powershell-start.png

  [交談範例]: https://github.com/LearnBoost/socket.io/tree/master/examples/chat
  [chat-example-view]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-22.png
  
  
  [chat-contents]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-5.png
  [The-output-of-the-npm-install-command]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-7.png
  [The output of the Publish-AzureService command]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-9.png
  

<!--HONumber=35.2-->
