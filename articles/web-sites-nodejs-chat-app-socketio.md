<properties linkid="dev-nodejs-website-with-socketio" urlDisplayName="Website Using Socket.IO" pageTitle="Node.js Website using Socket.io - Azure tutorial" metaKeywords="Azure Node.js socket.io tutorial, Azure Node.js socket.io, Azure Node.js tutorial" description="A tutorial that demonstrates using socket.io in a node.js website hosted on Azure." metaCanonical="" services="web-sites" documentationCenter="Node.js" title="Build a Node.js Chat Application with Socket.IO on an Azure Web Site" authors="larryfr" solutions="" videoId="" scriptId="" manager="paulettm" editor="mollybos" />

在 Azure 網站上使用 Socket.IO 建立 Node.js 聊天應用程式
=======================================================

Socket.IO 提供 node.js 伺服器與用戶端之間的即時通訊。本教學課程逐步引導您以 Azure 網站來託管 Socket.IO 架構的聊天應用程式。如需 Socket.IO 的詳細資訊，請參閱 <http://socket.io/>。

**注意**

此工作中的程序適用於 Azure 網站。若是使用雲端服務，請參閱＜[在 Azure 雲端服務上使用 Socket.IO 建立 Node.js 聊天應用程式](http://www.windowsazure.com/zh-tw/develop/nodejs/tutorials/app-using-socketio/)＞。

以下螢幕擷取畫面顯示完成的應用程式：

![A browser displaying the chat application](./media/web-sites-nodejs-chat-app-socketio/websitesocketcomplete.png)

下載聊天範例
------------

在此專案中，我們將使用 [Socket.IO GitHub 儲存機制](https://github.com/LearnBoost/socket.io/tree/0.9.14)中的聊天範例。請執行下列步驟來下載範例，並將它加入至您先前建立的專案。

1.  使用 **[Clone]** 按鈕來建立儲存機制的本機複本。您也可以使用 **[ZIP]** 按鈕來下載專案。

    ![A browser window viewing https://github.com/LearnBoost/socket.io/tree/master/examples/chat, with the ZIP download icon highlighted](./media/web-sites-nodejs-chat-app-socketio/socketio-2.png)

2.  瀏覽本機儲存機制的目錄結構，直到找到 **examples\\chat** 目錄為止。將此目錄的內容複製到另一個目錄，例如 **\\node\\chat**。

修改 App.js 和安裝模組
----------------------

將應用程式部署至 Azure 之前，必須稍做一些修改。請對 app.js 檔案中執行下列步驟：

1.  在記事本或其他文字編輯器中開啟 app.js 檔案。

2.  在 app.js 開頭找出 **Module dependencies** 區段，將含有 **sio = require('..//..//lib//socket.io')** 的那一行變更為 **sio = require('socket.io')**，如下所示：

		var express = require('express')
  		, stylus = require('stylus')
  		, nib = require('nib')
		//, sio = require('..//..//lib//socket.io'); //Original
  		, sio = require('socket.io');                //Updated

3.  為了確保應用程式在正確的通訊埠上接聽，請在記事本或您常用的編輯器中開啟 app.js，然後變更下列這一行，將 **3000** 改為 **process.env.PORT**，如下所示：

        //app.listen(3000, function () {            //Original
		app.listen(process.env.PORT, function () {  //Updated
		  var addr = app.address();
		  console.log('   app listening on http://' + addr.address + ':' + addr.port);
		});

	將變更儲存至 app.js 之後，使用下列步驟來安裝必要的模組：

1.  從命令列中，切換至 **\\node\\chat** 目錄，使用下列命令來安裝此應用程式所需的模組：

         npm install

    這樣會安裝 package.json 檔案中列出的模組。命令完成之後，您應該會看到類似這樣的輸出：

    ![The output of the npm install command](./media/web-sites-nodejs-chat-app-socketio/socketio-7.png)

2.  由於此範例原本為 Socket.IO GitHub 儲存機制的一部分，依相對路徑來直接參考 Socket.IO 程式庫，且 package.json 檔案中並沒有參考 Socket.IO，所以我們必須使用下列指令來安裝它：

        npm install socket.io -save

建立 Azure 網站並啟用 Git 發行
------------------------------

請遵循以下步驟來建立 Azure 網站，然後為該網站啟用 Git 發行功能。

**注意**

若要完成此教學課程，您需要 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 [Azure 免費試用](http://www.windowsazure.com/zh-tw/pricing/free-trial/?WT.mc_id=A7171371E)。

1.  從命令列中，切換至 **\\node\\chat** 目錄，使用下列命令來建立新的 Azure 網站，並啟用網站和本機目錄的 Git 儲存機制。這也會建立名為 'azure' 的 Git 遠端。

         azure site create mysitename --git

    您必須將 'mysitename' 改成您的網站的唯一名稱。

2.  使用下列命令，將現有的檔案認可到本機儲存機制：

         git add .
        git commit -m "Initial commit"

3.  使用下列命令，將檔案推送至 Azure 網站儲存機制：

        git push azure master

    在伺服器上匯入模組時會顯示狀態訊息。此程序完成之後，應用程式將在 Azure 網站上託管。



    <b>Note</b>
    <p>During module installation, you may notice errors that 'The imported project ... was not found'.These can safely be ignored.</p>
    </div>

1.  Socket.IO 使用 WebSockets (在 Azure 上依預設不會啟用)。若要啟用 Web Sockets，請使用下列命令：

         azure site set -w

    如果出現提示，請輸入網站的名稱。

    > [WACOM.NOTE] 'azure site set -w' 命令僅適用於 Azure 跨平台命令列介面 0.7.4 版或更新的版本。您也可以使用 Azure 管理入口網站來啟用 WebSocket 支援。
    >
    > 若要使用 [Azure 管理入口網站](https://manage.windowsazure.com)來啟用 WebSockets，請選取網站的 [設定] 頁面，在 Web Socket 項目上選取 [開啟]，然後按一下 [儲存]。
    >
    > ![websockets](./media/web-sites-nodejs-chat-app-socketio/websockets.png)

2.  若要在 Azure 上檢視網站，請使用下列命令來啟動網頁瀏覽器，並瀏覽至託管的網站：

         azure site browse

應用程式現在已在 Azure 上執行，且可以使用 Socket.IO 在不同用戶端之間轉送聊天訊息。

**注意**

為了簡化起見，此範例只能讓連線至相同執行個體的使用者聊天。這表示如果雲端服務建立兩個背景工作角色執行個體，則使用者只能夠與連線至相同背景工作角色執行個體的其他使用者交談。若要擴充應用程式來處理多個角色執行個體，您可以使用服務匯流排之類的技術，以便跨執行個體來共用 Socket.IO 儲存狀態。如需相關範例，請參閱＜[Azure SDK for Node.js GitHub 儲存機制](https://github.com/WindowsAzure/azure-sdk-for-node)＞(英文) 中的服務匯流排佇列和主題使用範例。

後續步驟
--------

在本教學課程中，您學到如何建立於 Azure 網站中託管的聊天應用程式。您也可以將此應用程式交由 Azure 雲端服務託管。如需相關作法的步驟，請參閱＜[在 Azure 雲端服務上使用 Socket.IO 建立 Node.js 聊天應用程式](/en-us/develop/nodejs/tutorials/app-using-socketio/)＞。

