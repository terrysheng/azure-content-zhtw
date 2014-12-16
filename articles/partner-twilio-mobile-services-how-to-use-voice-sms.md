<properties pageTitle="透過 Twilio 使用語音和簡訊功能 | 行動開發人員中心 " metaKeywords="" description="Learn how to perform common tasks using the Twilio API with Azure Mobile Services." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="How to use Twilio for voice and SMS capabilities from Mobile Services" authors="MicrosoftHelp@twilio.com" solutions="" manager="twilio" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="10/03/2014" ms.author="MicrosoftHelp@twilio.com" />


<h1>如何從行動服務透過 Twilio 使用語音和簡訊功能</h1>

本主題將說明如何使用 Twilio API 和 Azure 行動服務執行一般工作。在本教學課程中，您將進一步了解如何建立自訂 API 指令碼，以使用 Twilio API 起始電話及傳送簡訊 (SMS)。 

<h2><a id="WhatIs"></a>什麼是 Twilio？</h2>
Twilio 正在形塑商業環境的未來，可讓開發人員將語音、VoIP 和訊息傳送內嵌到應用程式中。它們將雲端、全球化環境中所需的整個基礎結構虛擬化，透過 Twilio 通訊 API 平台來揭露基礎結構。輕鬆就可建立和擴充應用程式。享受隨收隨付定價的彈性和雲端可靠性的好處。

**Twilio 語音**可讓應用程式撥打和接聽電話。**Twilio 簡訊**可讓應用程式收發簡訊。**Twilio 用戶端**可讓您從任何電話、平板電腦或瀏覽器撥打 VoIP 電話，且支援 WebRTC。

<h2><a id="Pricing"></a>Twilio 定價和特別優惠</h2>
Azure 客戶享有 [特別優惠][special_offer]：升級 Twilio 帳戶即贈送價值 $10 的 Twilio 點數。此 Twilio 點數可用來折抵任何 Twilio 使用量 ($10 點數相當於最多傳送 1,000 則簡訊，或最多接收 1000 分鐘的撥入語音，視電話號碼所在地點或通話目的地而定)。請至 [ahoy.twilio.com/azure][special_offer] 兌換 Twilio 點數來開始使用。

Twilio 是隨收隨付的服務。不需要設定費，隨時都可結清帳戶。如需詳細資訊，請參閱＜[Twilio 定價][twilio_pricing]＞(英文)。  

<h2><a id="Concepts"></a>概念</h2>
Twilio API 是一套為應用程式提供語音和簡訊功能的 RESTful API。用戶端程式庫有多種語言版本，相關清單請參閱＜[Twilio API 程式庫] [twilio_libraries]＞(英文)。其他教學課程會說明如何使用 Twilio 和以 [.NET][azure_twilio_howto_dotnet]、[node.js][azure_twilio_howto_node]、[Java][azure_twilio_howto_java]、[PHP][azure_twilio_howto_php]、[Python][azure_twilio_howto_python] 或 [Ruby][azure_twilio_howto_ruby] 撰寫的 Azure 應用程式。

Twilio API 的兩大重點是 Twilio 動詞和 Twilio 標記語言 (TwiML)。

<h3><a id="Verbs"></a>Twilio 動詞</h3>
API 採用 Twilio 動詞。例如，**<Say>** 動詞指示 Twilio 在通話中用語音傳遞訊息。 

以下是 Twilio 動詞清單。如需了解其他動詞和功能，請參閱 [Twilio 標記語言文件](http://www.twilio.com/docs/api/twiml) (英文)。

* **<Dial>**：使撥號者接通另一支電話。
* **<Gather>**：收集電話按鍵上輸入的號碼。
* **<Hangup>**：結束通話。
* **<Play>**：播放音訊檔案。
* **<Pause>**：靜候一段指定的秒數。
* **<Record>**：錄製來電者的語音，並傳回含有錄音的檔案 URL。
* **<Redirect>**：將通話或簡訊的控制權移轉至不同 URL 的 TwiML。
* **<Reject>**：拒絕 Twilio 號碼的來電而不計費
* **<Say>**：將來電的文字轉換成語音。
* **<Sms>**：傳送簡訊。

<h3> <a id="TwiML"></a>TwiML</h3>
TwiML 是以 Twilio 動詞為基礎的一組 XML 指令，可指示 Twilio 如何處理來電或簡訊。

例如，下列 TwiML 會將 **Hello World** 文字轉換成語音。

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

當應用程式呼叫 Twilio API 時，其中一個 API 參數是傳回 TwiML 回應的 URL。在開發用途上，您可以使用 Twilio 提供的 URL 來提供應用程式所使用的 TwiML 回應。您也可以裝載您自己的 URL 來產生 TwiML 回應，另一種選擇是使用 **TwiMLResponse** 物件。

如需 Twilio 動詞、屬性和 TwiML 的詳細資訊，請參閱 [TwiML][twiml]。如需 Twilio API 的詳細資訊，請參閱 [Twilio API][twilio_api]。

<h2><a id="CreateAccount"></a>建立 Twilio 帳戶</h2>
準備取得 Twilio 帳戶時，請至 [試用 Twilio][try_twilio] 註冊。您可以先使用免費帳戶，稍後再升級帳戶。

註冊 Twilio 帳戶時，您會收到帳戶識別碼和驗證權杖。兩者皆為呼叫 Twilio API 所需。為了防止未經授權存取您的帳戶，您妥善保管驗證權杖。在 [Twilio 帳戶頁面][twilio_account] (英文) 的 **ACCOUNT SID** 和 **AUTH TOKEN** 欄位中，分別可檢視您的帳戶識別碼和驗證權杖。

<h2><a id="VerifyPhoneNumbers"></a>驗證電話號碼</h2>
各種電話號碼都必須經過您在 Twilio 的帳戶來驗證。例如，如果想要向外撥打電話，則必須向 Twilio 驗證此電話號碼為外撥來電者識別碼。同樣地，如果想要以某個電話號碼來接收簡訊，則必須向 Twilio 驗證此受話電話號碼。如需有關如何驗證電話號碼的詳細資訊，請參閱＜[管理電話號碼] [verify_phone]＞(英文)。以下某些程式碼依賴需要向 Twilio 驗證的電話號碼。

除了在應用程式中使用現有的電話號碼，您也可以購買 Twilio 電話號碼。如需有關購買 Twilio 電話號碼的詳細資訊，請參閱 [Twilio 電話號碼說明](https://www.twilio.com/help/faq/phone-numbers) (英文)。

<h2><a id="create_app"></a>建立行動服務</h2>
代管已啟用 Twilio 功能之應用程式的行動服務，與其他行動服務並無不同。您只是在服務中新增 Twilio node.js 程式庫，以便從您的行動服務自訂 API 指令碼加以參考而已。如需建立初始行動服務的相關資訊，請參閱 [開始使用行動服務](/zh-tw/documentation/articles/mobile-services-ios-get-started/)。

<h2><a id="ConfigureMobileService"></a>設定行動服務以使用 Twilio Node.js 程式庫</h2>
Twilio 所提供的 Node.js 程式庫封裝了 Twilio 的各種組件，讓您方便而輕鬆地與 Twilio REST API 和 Twilio 用戶端互動，以產生 TwiML 回應。

若要在行動服務中使用 Twilio node.js 程式庫，您必須利用行動服務 npm 模組支援 (只要將您的指令碼儲存在原始檔控制中，即可支援)。 

1. 完成教學課程 [在原始檔控制中儲存指令碼](/zh-tw/documentation/articles/mobile-services-store-scripts-source-control/)。這會逐步引導您設定行動服務的原始檔控制，並在 Git 儲存機制中儲存伺服器指令碼。

2. 在設定行動服務的原始檔控制之後，請開啟位於本機電腦上的儲存機制，並瀏覽至 `\services` 子資料夾，在文字編輯器中開啟 package.json 檔案，然後在**相依性**物件中新增下列欄位：

		"twilio": "~1.7.0"
 
3. 在新增**相依性**物件的 Twilio 封裝參考之後，package.json 檔案看起來應該如下所示：

		{
		  "name": "todolist",
		  "version": "1.0.0",
		  "description": "todolist - hosted on Windows Azure Mobile Services",
		  "main": "server.js",
		  "engines": {
		    "node": ">= 0.8.19"
		  },
		  "dependencies": {
			"twilio": "~1.7.0" 
		  },
		  "devDependencies": {},
		  "scripts": {},
		  "author": "unknown",
		  "licenses": [],
		  "keywords":[]
		}

	>[WACOM.NOTE]應將 Twilio 的相依性新增為 `"twilio":"~1.7.0"`，使用 (~)。不支援使用插入號 (^) 的參考。 

4. 認可此檔案更新，並將更新推回行動服務。

	此項 package.json 檔案更新會重新啟動您的行動服務。
	
行動服務現在會安裝並載入 Twilio 封裝，以便您可以在自訂的 API 和資料表指令碼中參考及使用 Twilio 程式庫。

<h2><a id="howto_make_call"></a>作法：撥出電話</h2>
下列指令碼將說明如何使用 **makeCall** 函數從您的行動服務起始對話通話。此程式碼也使用 Twilio 提供的網站來傳回 Twilio 標記語言 (TwiML) 回應。請將 **From** 和 **To** 電話號碼換成您的值，在執行程式碼之前，請記得先驗證 Twilio 帳戶的 **From** 電話號碼。

    var twilio = require('twilio');

    exports.post = function(request, response) {

        var client = new twilio.RestClient('[ACCOUNT_SID]', 'AUTH_TOKEN');

        client.makeCall({
            to:'+16515556677', 
            from: '+14506667788',
            url: 'http://www.example.com/twiml.php' 

        }, function(err, responseData) {
            console.log(responseData.from); 
            response.send(200, '');
        });
    };

如需 **client.makeCall** 函數中傳遞參數的詳細資訊，請參閱 [http://www.twilio.com/docs/api/rest/making-calls][twilio_rest_making_calls] (英文)。

如前所述，此程式碼使用 Twilio 提供的網站來傳回 TwiML 回應。您可以改用您自己的網站來提供 TwiML 回應。如需詳細資訊，請參閱 [如何：從您自己的網站提供 TwiML 回應](#howto_provide_twiml_responses)。

<h2><a id="howto_send_sms"></a>作法：傳送簡訊</h2>
下列程式碼將說明如何使用 **sendSms** 函數來傳送簡訊。**From** 號碼由 Twilio 提供給試用帳戶來傳送簡訊。執行程式碼之前，必須驗證 Twilio 帳戶的 **To** 號碼。

    var twilio = require('twilio');

    exports.post = function(request, response) {

        var client = new twilio.RestClient('[ACCOUNT_SID]', 'AUTH_TOKEN');
 
        client.sendSms({
            to:'[]',
            from:'[]',
            body:'ahoy hoy! Testing Twilio and node.js'
        }, function(error, message) {
    
            // The "error" variable will contain error information, if any.
            // If the request was successful, this value will be "false"
            if (!error) {
                console.log('Success! The SID for this SMS message is: ' + message.sid);
                console.log('Message sent on: ' + message.dateCreated);
            }
            else {
                console.log('Oops! There was an error.');
            }
            response.send(200, { error : error } );
        });
    };


<h2><a id="howto_provide_twiml_responses"></a>作法：從您自己的網站提供 TwiML 回應</h2>

當應用程式開始呼叫 Twilio API 時 (例如，透過 client.InitiateOutboundCall 方法)，Twilio 會傳送要求至 URL，然後應該會傳回 TwiML 回應。作法：撥出電話中的範例使用 Twilio 提供的 URL http://twimlets.com/message 來傳回回應。

<div class="dev-callout">
<b>注意</b>
<p>雖然 TwiML 是專供 Web 服務使用，但您也可以在瀏覽器中檢視 TwiML。例如，您可以按一下 <a href="http://twimlets.com/message">twimlet_message_url</a> 以檢視空的 <Response> 元素，又或者按一下 <a href="http://twimlets.com/message?Message%5B0%5D=Hello%20World">twimlet_message_url_hello_world</a> 以檢視包含 <Say> 元素的 <Response> 元素。</p>
</div>

除了依賴 Twilio 提供的 URL，您也可以建立自己的 URL 網站來傳回 HTTP 回應。您可以使用任何可傳回 HTTP 回應的語言來建立網站。本主題假設您從 ASP.NET 通用處理常式來裝載 URL。

下列指令碼會產生一個在來電中顯示 Hello World 的 TwiML 回應。

    var twilio = require('twilio');

    exports.post = function(request, response) {
        var resp = new twilio.TwimlResponse();
        resp.say({voice:'woman'}, 'ahoy hoy! Testing Twilio and node.js');
        response.set('Content-Type', 'text/xml');
        response.send(200, resp.toString());
    };

如需 TwiML 的詳細資訊，請參閱 [https://www.twilio.com/docs/api/twiml](https://www.twilio.com/docs/api/twiml)。

設定好如何提供 TwiML 回應之後，您可以將 URL 傳入 **client.makeCall** 方法中，如下列程式碼範例所示：
    
    var twilio = require('twilio');

    exports.post = function(request, response) {

        var client = new twilio.RestClient('[ACCOUNT_SID]', 'AUTH_TOKEN');

        client.makeCall({
            to:'+16515556677', 
            from: '+14506667788',
            url: 'http://<your_mobile_service>.azure-mobile.net/api/makeCall' 

        }, function(err, responseData) {

            console.log(responseData.from);
            response.send(200, '');
        });
    };

[WACOM.INCLUDE [twilio_additional_services_and_next_steps](../includes/twilio_additional_services_and_next_steps.md)]


[twilio_rest_making_calls]: http://www.twilio.com/docs/api/rest/making-calls

[twilio_pricing]: http://www.twilio.com/pricing
[special_offer]: http://ahoy.twilio.com/azure
[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api]: http://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_account]:  https://www.twilio.com/user/account
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#


[azure_twilio_howto_dotnet]: /zh-tw/develop/net/how-to-guides/twilio-voice-and-sms-service/
[azure_twilio_howto_java]: /zh-tw/develop/java/how-to-guides/twilio-voice-and-sms-service/
[azure_twilio_howto_node]: /zh-tw/develop/nodejs/how-to-guides/twilio-voice-and-sms-service/
[azure_twilio_howto_ruby]: /zh-tw/develop/ruby/how-to-guides/twilio-voice-and-sms-service/
[azure_twilio_howto_python]: /zh-tw/develop/python/how-to-guides/twilio-voice-and-sms-service/
[azure_twilio_howto_php]: /zh-tw/develop/php/how-to-guides/twilio-voice-and-sms-service/
