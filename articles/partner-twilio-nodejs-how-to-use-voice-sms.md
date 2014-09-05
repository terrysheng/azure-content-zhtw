<properties linkid="dev-nodejs-how-to-twilio-voice-sms-service" urlDisplayName="Twilio Voice and SMS Service" pageTitle="Using Twilio for Voice, VoIP, and SMS Messaging in Azure" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="Node.js" title=" VoIP" authors="" solutions="" manager="" editor="" />

在 Azure 中透過 Twilio 使用語音、VoIP 和簡訊功能
================================================

本指南將說明如何建置可在 Azure 上與 Twilio 和 node.js 通訊的應用程式。

目錄
----

-   [什麼是 Twilio？](#whatis)
-   [註冊 Twilio (Microsoft 折扣)](#signup)
-   [建立及部署 node.js Azure 網站](#azuresite)
-   [設定 Twilio 模組](#twiliomodule)
-   [向外撥打電話](#makecall)
-   [傳送簡訊](#sendmessage)
-   [後續步驟](#nextsteps)

## 什麼是 Twilio？

Twilio 是一個 API 平台，可協助開發人員撥接電話、收發簡訊，以及在瀏覽器應用程式和原生行動應用程式中內嵌 VoIP 通話。在深入說明之前，我們先概略了解其運作方式。

### 接收來電和簡訊

Twilio 可讓開發人員[購買可程式化的電話號碼](https://www.twilio.com/user/account/phone-numbers/available/local)，用以收發電話和簡訊。當 Twilio 號碼接收到內送的電話或簡訊時，Twilio 會對您的 Web 應用程式傳送 HTTP POST 或 GET 要求，向您請示如何處理來電或簡訊。您的伺服器會以 [TwiML](https://www.twilio.com/docs/api/twiml) 回應 Twilio 的 HTTP 要求；這是一組簡單的 XML 標籤，其中包含如何處理來電或簡訊的指示。稍後我們會檢視 TwiML 的範例。

### 撥打電話和傳送簡訊

開發人員可對 Twilio Web 服務 API 發出 HTTP 要求，以傳送簡訊或向外撥打電話。對於外撥電話，開發人員還必須指定一個 URL 以傳回相關指示，說明在接通後應如何處理外撥電話。

### 在 UI 程式碼中內嵌 VoIP 功能 (JavaScript、iOS 或 Android)

Twilio 提供了一個可將任何桌面 Web 瀏覽器、iOS 應用程式或 Android 應用程式轉換為 VoIP 電話的用戶端 SDK。在本文中，我們僅討論如何在瀏覽器中使用 VoIP 通話。除了在瀏覽器中執行 Twilio JavaScript SDK 以外，同時也須使用伺服器端應用程式 (node.js 應用程式)，將「功能權杖」發出至 JavaScript 用戶端。如需透過 node.js 使用 VoIP 的詳細資訊，請參閱 [Twilio 開發人員部落格](http://www.twilio.com/blog/2013/04/introduction-to-twilio-client-with-node-js.html)。

## 註冊 Twilio (Microsoft 折扣)

在使用 Twilio 服務之前，您必須先[註冊帳戶](http://ahoy.twilio.com/azure)。Microsoft Azure 客戶享有特別折扣 - [請務必在此註冊](http://ahoy.twilio.com/azure)！

## 建立及部署 node.js Azure 網站

接著，您必須建立在 Azure 上執行的 node.js 網站。[此作業的正式指引文件位於此處](http://www.windowsazure.com/zh-tw/develop/nodejs/tutorials/create-a-website-(mac)/)。概括而言，您將執行下列作業：

-   註冊 Azure 帳戶 (若您尚無此帳戶)
-   使用 Azure 管理主控台建立新網站
-   新增原始檔控制支援 (我們假設您使用 git)
-   使用簡單的 node.js Web 應用程式建立 `server.js` 檔案
-   將這個簡單的應用程式部署至 Azure

## 設定 Twilio 模組

接著，我們將開始撰寫採用 Twilio API 的簡易 node.js 應用程式。開始之前，我們必須設定 Twilio 帳戶認證。

### 在系統環境變數中設定 Twilio 認證

若要對 Twilio 後端發出已驗證的要求，我們必須要有自己的帳戶 SID 和驗證權杖，以作為 Twilio 帳戶的使用者名稱和密碼集。最安全的方式是透過系統環境變數，在 Azure 中設定這些與節點模組搭配使用的項目；您可以直接在 Azure 管理主控台中設定。

選取您的 node.js 網站，然後按一下 "CONFIGURE" 連結。稍微向下捲動，您會看見一個可讓您為應用程式設定組態屬性的區域。請依照以下說明輸入您的 Twilio 帳戶認證 ([位於 Twilio 儀表板上](https://www.twilio.com/user/account)) - 請確實分別將其命名為 "TWILIO\_ACCOUNT\_SID" 和 "TWILIO\_AUTH\_TOKEN"：

![Azure admin console](./media/partner-twilio-nodejs-how-to-use-voice-sms/twilio_1.png)

這些變數設定完成後，請在 Azure 主控台中重新啟動您的應用程式。

### 在 package.json 中宣告 Twilio 模組

接下來，我們必須建立一個 package.json，以透過 [npm](http://npmjs.org) 管理節點模組相依性。在您於 Azure/node.js 教學課程中建立的 "server.js" 檔案所屬的相同層級上，建立名為 "package.json" 的檔案。在此檔案中放入下列項目：

	  {
	    "name": "application-name",
	    "version": "0.0.1",
	    "private": true,
	    "scripts": {
	      "start": "node server"
	    },
	    "dependencies": {
	      "express": "3.1.0",
	      "ejs": "*",
	      "twilio":"*"
	    }
	  }

這會將 twilio 模組宣告為相依性，以及常用的 [Express Web 架構](http://expressjs.com)和 EJS 範本引擎。一切都已就緒，可以開始撰寫程式碼了。

## 向外撥打電話

我們可以建立簡易表單，以撥打我們選擇的號碼。請開啟 server.js，然後輸入下列程式碼。請留意顯示 "CHANGE\_ME" 的部分 - 請在該處放入您 Azure 網站的名稱：

	  // Module dependencies
	  var express = require('express'), 
	      path = require('path'), 
	      http = require('http'), 
	      twilio = require('twilio');
	
	  // Create Express web application
	  var app = express();
	
	  // Express configuration
	  app.configure(function(){
	      app.set('port', process.env.PORT || 3000);
	      app.set('views', __dirname + '/views');
	      app.set('view engine', 'ejs');
	      app.use(express.favicon());
	      app.use(express.logger('dev'));
	      app.use(express.bodyParser());
	      app.use(express.methodOverride());
	      app.use(app.router);
	      app.use(express.static(path.join(__dirname, 'public')));
	  });
	  app.configure('development', function(){
	      app.use(express.errorHandler());
	  });
	
	  // Render an HTML user interface for the application's home page
	  app.get('/', function(request, response) {
	      response.render('index');
	  });
	
	  // Handle the form POST to place a call
	  app.post('/call', function(request, response) {
	      var client = twilio();
	      client.makeCall({
	          // make a call to this number
	          to:request.body.number,
	
	          // Change to a Twilio number you bought - see:
	          // https://www.twilio.com/user/account/phone-numbers/incoming
	          from:'+15558675309',
	
	          // A URL in our app which generates TwiML
	          // Change "CHANGE_ME" to your app's name
	          url:'https://CHANGE_ME.azurewebsites.net/outbound_call'
	      }, function(error, data) {
	          // Go back to the home page
	          response.redirect('/');
	      });
	  });
	
	  // Generate TwiML to handle an outbound call
	  app.post('/outbound_call', function(request, response) {
	      var twiml = new twilio.TwimlResponse();
	
	      // Say a message to the call's receiver 
	      twiml.say('hello - thanks for checking out Twilio and Azure', {
	          voice:'woman'
	      });
	
	      response.set('Content-Type', 'text/xml');
	      response.send(twiml.toString());
	  });
	
	  // Start server
	  http.createServer(app).listen(app.get('port'), function(){
	    console.log("Express server listening on port " + app.get('port'));
	  });

接著，請建立名為 "views" 的目錄 - 在此目錄中，請以下列內容建立名為 "index.ejs" 的檔案：

	  &lt;!DOCTYPE html&gt;
	  &lt;html&gt;
	  &lt;head&gt;
	      &lt;title&gt;Twilio Test&lt;/title&gt;
	      &lt;style&gt;
	      input { height:20px; width:300px; font-size:18px; margin:5px; padding:5px; }
	      &lt;/style&gt;
	  &lt;/head&gt;
	  &lt;body&gt;
	      &lt;h1&gt;Twilio Test&lt;/h1&gt;
	      &lt;form action="/call" method="POST"&gt;
	          &lt;input placeholder="Enter a phone number" name="number"/&gt;
	          &lt;br/&gt;
	          &lt;input type="submit" value="Call the number above"/&gt;
	      &lt;/form&gt;
	  &lt;/body&gt;
	  &lt;/html&gt;

現在，將您的網站部署至 Azure，然後開啟您的首頁。您應可在文字欄位中輸入電話號碼，並接聽您 Twilio 號碼的來電。

## 傳送簡訊

現在，我們要設定用以傳送簡訊的使用者介面和表單處理邏輯。請開啟 "server.js"，並在 "app.post" 的最後一個通話之後新增下列程式碼：

	  app.post('/sms', function(request, response) {
	      var client = twilio();
	      client.sendSms({
	          // send a text to this number
	          to:request.body.number,
	
	          // A Twilio number you bought - see:
	          // https://www.twilio.com/user/account/phone-numbers/incoming
	          from:'+15558675309',
	
	          // The body of the text message
	          body: request.body.message
	          
	      }, function(error, data) {
	          // Go back to the home page
	          response.redirect('/');
	      });
	  	});

In "views/index.ejs", add another form under the first one to submit a number and a text message:

	  &lt;form action="/sms" method="POST"&gt;
	      &lt;input placeholder="Enter a phone number" name="number"/&gt;
	      &lt;br/&gt;
	      &lt;input placeholder="Enter a message to send" name="message"/&gt;
	      &lt;br/&gt;
	      &lt;input type="submit" value="Send text to the number above"/&gt;
	  &lt;/form&gt;

將您的應用程式重新部署至 Azure，此時您應可提交該表單，並且將簡訊傳送給自己 (或是您所有的好友)。

## 後續步驟

現在，您已了解使用 node.js 和 Twilio 建置通訊應用程式的基本概念。但前述範例僅只勾勒出 Twilio 和 node.js 功能的大致輪廓。如需使用 Twilio 和 node.js 的詳細資訊，請參考下列資源：

-   [正式模組文件](http://twilio.github.io/twilio-node/)
-   [透過 node.js 應用程式使用 VoIP 的教學課程](http://www.twilio.com/blog/2013/04/introduction-to-twilio-client-with-node-js.html)
-   [Votr - 使用 node.js 和 CouchDB 的即時簡訊投票應用程式 (三個單元)](http://www.twilio.com/blog/2012/09/building-a-real-time-sms-voting-app-part-1-node-js-couchdb.html)
-   [在瀏覽器中使用 node.js 的配對程式設計](http://www.twilio.com/blog/2013/06/pair-programming-in-the-browser-with-twilio.html)

祝您在 Azure 上使用 node.js 和 Twilio 時能夠得心應手！

