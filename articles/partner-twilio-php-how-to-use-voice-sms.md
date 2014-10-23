<properties title="How to Use Twilio for Voice and SMS (PHP) - Azure" pageTitle="How to Use Twilio for Voice and SMS (PHP) - Azure" metaKeywords="Azure PHP Twilio, Azure phone calls, Azure phone calls, Azure twilio, Azure SMS, Azure SMS, Azure voice calls, azure voice calls, Azure text messages, Azure text messages" description="Learn how to make a phone call and send a SMS message with the Twilio API service on Azure. Code samples written in PHP." documentationCenter="PHP" services="" authors="MicrosoftHelp@twilio.com; robmcm" manager="wpickett" editor="mollybos" videoId="" scriptId="" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="01/01/1900" ms.author="MicrosoftHelp@twilio.com; robmcm"></tags>

# 如何在 PHP 中透過 Twilio 使用語音和簡訊功能

本指南示範如何在 Azure 上透過 Twilio API 服務執行常見的程式設計工作。涵蓋的案例包括打電話和傳送簡訊 (SMS)。如需有關如何在應用程式中使用 Twilio 語音和 SMS 的詳細資訊，請參閱[後續步驟][]一節。

## 目錄

-   [什麼是 Twilio？][]
-   [Twilio 定價][]
-   [概念][]
-   [建立 Twilio 帳戶][]
-   [驗證電話號碼][]
-   [建立 PHP 應用程式][]
-   [設定應用程式以使用 Twilio 程式庫][]
-   [作法：撥出電話][]
-   [作法：傳送簡訊][]
-   [作法：從您自己的網站提供 TwiML 回應][]

## <span id="WhatIs"></span></a>什麼是 Twilio？

Twilio 正在形塑商業環境的未來，可讓開發人員將語音、VoIP 和訊息傳送內嵌到應用程式中。它們將雲端、全球化環境中所需的整個基礎結構虛擬化，透過 Twilio 通訊 API 平台來揭露基礎結構。輕鬆就可建立和擴充應用程式。享受隨收隨付定價的彈性和雲端可靠性的好處。

**Twilio 語音**可讓應用程式撥打和接聽電話。**Twilio 簡訊**可讓應用程式收發簡訊。**Twilio 用戶端**可讓您從任何電話、平板電腦或瀏覽器撥打 VoIP 電話，且支援 WebRTC。

## <span id="Pricing"></span></a>Twilio 定價和特別優惠

Azure 客戶享有 [特別優惠][<http://www.twilio.com/azure>]：升級 Twilio 帳戶即贈送價值 $10 的 Twilio 點數。此 Twilio 點數可用來折抵任何 Twilio 使用量 ($10 點數相當於最多傳送 1,000 則簡訊，或最多接收 1000 分鐘的撥入語音，視電話號碼所在地點或通話目的地而定)。請至下列位置兌換此 Twilio 點數，並開始使用：[][]<http://ahoy.twilio.com/azure></a>。

Twilio 是隨收隨付的服務。不需要設定費，隨時都可結清帳戶。如需詳細資訊，請參閱＜[Twilio 定價][1]＞(英文)。

## <span id="Concepts"></span></a> 概念

Twilio API 是一套為應用程式提供語音和簡訊功能的 RESTful API。用戶端程式庫有多種語言版本，相關清單請參閱＜[Twilio API 程式庫][]＞(英文)。

Twilio API 的兩大重點是 Twilio 動詞和 Twilio 標記語言 (TwiML)。

### <span id="Verbs"></span></a>Twilio 動詞

API 採用 Twilio 動詞。例如，**\<Say\>** 動詞指示 Twilio 在通話中用語音傳遞訊息。

以下是 Twilio 動詞清單。請透過 [Twilio 標記語言文件][<http://www.twilio.com/docs/api/twiml>] 了解其他的動詞和功能。

-   **\<Dial\>**：使撥號者接通另一支電話。
-   **\<Gather\>**：收集電話按鍵上輸入的號碼。
-   **\<Hangup\>**：結束通話。
-   **\<Play\>**：播放音訊檔案。
-   **\<Pause\>**：靜候一段指定的秒數。
-   **\<Record\>**：錄製來電者的語音並傳回含有錄音的檔案 URL。
-   **\<Redirect\>**：將通話或簡訊的控制權移轉至不同 URL 的 TwiML。
-   **\<Reject\>**：拒絕 Twilio 號碼的來電而不計費
-   **\<Say\>**：將來電的文字轉換成語音。
-   **\<Sms\>**：傳送簡訊。

### <span id="TwiML"></span></a>TwiML

TwiML 是以 Twilio 動詞為基礎的一組 XML 指令，可指示 Twilio 如何處理來電或簡訊。

例如，下列 TwiML 會將 **Hello World** 文字轉換成語音。

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

當應用程式呼叫 Twilio API 時，其中一個 API 參數是傳回 TwiML 回應的 URL。在開發用途上，您可以使用 Twilio 提供的 URL 來提供應用程式所使用的 TwiML 回應。您也可以裝載您自己的 URL 來產生 TwiML 回應，另一種選擇是使用 **TwiMLResponse** 物件。

如需 Twilio 動詞、屬性和 TwiML 的詳細資訊，請參閱 [TwiML][]。如需 Twilio API 的詳細資訊，請參閱 [Twilio API][]。

## <span id="CreateAccount"></span></a>建立 Twilio 帳戶

準備取得 Twilio 帳戶時，請至[試用 Twilio][] 註冊。您可以先使用免費帳戶，稍後再升級帳戶。

註冊 Twilio 帳戶時，您會收到帳戶識別碼和驗證權杖。兩者皆為呼叫 Twilio API 所需。為了防止未經授權存取您的帳戶，您妥善保管驗證權杖。在 [Twilio 帳戶頁面][] (英文) 的 **ACCOUNT SID** 和 **AUTH TOKEN** 欄位中，分別可檢視您的帳戶識別碼和驗證權杖。

## <span id="VerifyPhoneNumbers"></span></a>驗證電話號碼

各種電話號碼都必須經過您在 Twilio 的帳戶來驗證。例如，如果想要使用來電者 ID 現有的電話號碼向外撥打電話，則必須向 Twilio 驗證此電話號碼。同樣地，在您升級之前，如果想要傳送簡訊給某個電話號碼，也必須向 Twilio 驗證此號碼。在升級之後，您無須驗證電話號碼即可傳送簡訊給任何號碼。如需如何驗證電話號碼的詳細資訊，請參閱[管理電話號碼][]。以下某些程式碼依賴需要向 Twilio 驗證的電話號碼。

除了在應用程式中使用現有的電話號碼，您也可以購買 Twilio 電話號碼。如需有關購買 Twilio 電話號碼的詳細資訊，請參閱＜[Twilio 電話號碼說明][]＞(英文)。

## <span id="create_app"></span></a>建立 PHP 應用程式

使用 Twilio 服務且執行於 Azure 的 PHP 應用程式，與其他使用 Twilio 服務的 PHP 應用程式並無不同。雖然 Twilio 服務是以 REST 為基礎，並且可透過數種方式從 PHP 撥打，但本文的重點是要說明如何搭配使用 Twilio 服務與 [適用於 PHP 的 Twiliofor 程式庫 (由 Github 提供)][]。若想進一步了解如何使用適用於 PHP 的 Twilio 程式庫，請參閱 [][2]<http://readthedocs.org/docs/twilio-php/en/latest/index.html></a>。

如需建置 Twilio/PHP 應用程式以及將其部署至 Azure 的詳細指示，請參閱[如何在 Azure 上的 PHP 應用程式中使用 Twilio 撥打電話][]。

## <span id="configure_app"></span></a>設定應用程式以使用 Twilio 程式庫

您可以透過兩種方式設定應用程式，以使用適用於 PHP 的 Twilio 程式庫：

1.  從 Github 下載適用於 PHP 的 Twilio 程式庫 ([][適用於 PHP 的 Twiliofor 程式庫 (由 Github 提供)]<https://github.com/twilio/twilio-php></a>)，然後將 **Services** 目錄新增至您的應用程式。

    -或-

2.  以 PEAR 封裝的形式，安裝適用於 PHP 的 Twilio 程式庫。您可以使用下列命令進行此安裝：

        $ pear channel-discover twilio.github.com/pear
        $ pear install twilio/Services_Twilio

在適用於 PHP 的 Twilio 程式庫安裝完成後，您可以在 PHP 檔案頂端新增 **require\_once** 陳述式，以參考該程式庫：

        require_once 'Services/Twilio.php';

如需詳細資訊，請參閱 [][3]<https://github.com/twilio/twilio-php/blob/master/README.md></a>。

## <span id="howto_make_call"></span></a>作法：撥出電話

以下說明如何使用 **Services\_Twilio** 類別來撥出電話。此程式碼也使用 Twilio 提供的網站來傳回 Twilio 標記語言 (TwiML) 回應。請將 **From** 和 **To** 電話號碼換成您的值，在執行程式碼之前，請記得先驗證 Twilio 帳戶的 **From** 電話號碼。

    // Include the Twilio PHP library.
    require_once 'Services/Twilio.php';

    // Library version.
    $version = "2010-04-01";

    // Set your account ID and authentication token.
    $sid = "your_twilio_account_sid";
    $token = "your_twilio_authentication_token";

    // The number of the phone initiating the the call.
    // (Must be previously validated with Twilio.)
    $from_number = "NNNNNNNNNNN";

    // The number of the phone receiving call.
    $to_number = "NNNNNNNNNNN";

    // Use the Twilio-provided site for the TwiML response.
    $url = "http://twimlets.com/message";

    // The phone message text.
    $message = "Hello world.";

    // Create the call client.
    $client = new Services_Twilio($sid, $token, $version);

    //Make the call.
    try
    {
        $call = $client->account->calls->create(
            $from_number, 
            $to_number,
            $url.'?Message='.urlencode($message)
        );
    }
    catch (Exception $e) 
    {
        echo 'Error: ' . $e->getMessage();
    }

如前所述，此程式碼使用 Twilio 提供的網站來傳回 TwiML 回應。您可以改用自己的網站來提供 TwiML 回應；如需詳細資訊，請參閱[如何從您自己的網站提供 TwiML 回應][作法：從您自己的網站提供 TwiML 回應]。

-   **注意**：若要對 SSL 憑證驗證錯誤進行疑難排解，請參閱 [][4]<http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html></a>。

## <span id="howto_send_sms"></span></a>作法：傳送簡訊

以下說明如何使用 **Services\_Twilio** 類別來傳送簡訊。**From** 號碼由 Twilio 提供給試用帳戶來傳送簡訊。執行程式碼之前，必須驗證您 Twilio 帳戶的 **To** 號碼。

    // Include the Twilio PHP library.
    require_once 'Services/Twilio.php';

    // Library version.
    $version = "2010-04-01";

    // Set your account ID and authentication token.
    $sid = "your_twilio_account_sid";
    $token = "your_twilio_authentication_token";


    $from_number = "NNNNNNNNNNN"; // With trial account, texts can only be sent from your Twilio number.
    $to_number = "NNNNNNNNNNN";
    $message = "Hello world.";

    // Create the call client.
    $client = new Services_Twilio($sid, $token, $version);

    // Send the SMS message.
    try
    {
        $client->account->sms_messages->create($from_number, $to_number, $message);
    }
    catch (Exception $e) 
    {
        echo 'Error: ' . $e->getMessage();
    }

## <span id="howto_provide_twiml_responses"></span></a>作法：從您自己的網站提供 TwiML 回應

當您的應用程式開始呼叫 Twilio API 時，Twilio 會將要求傳送至 URL，然後應該會傳回 TwiML 回應。前述範例使用 Twilio 提供的 URL [][5]<http://twimlets.com/message></a>。(雖然 TwiML 是設計給 Twilio 使用的，但您也可以在瀏覽器中檢視 TwiML。例如，按一下 [][5]<http://twimlets.com/message></a> 可查看空白的 `<Response>` 元素，又例如，按一下 [][6]<http://twimlets.com/message?Message%5B0%5D=Hello%20World></a> 可查看包含 `<Say>` 元素的 `<Response>` 元素。)

除了使用 Twilio 提供的 URL 以外，您也可以建立自己的網站來傳回 HTTP 回應。您可以使用任何語言建立會傳回 XML 回應的網站；本主題假設您將使用 PHP 建立 TwiML。

下列 PHP 頁面會產生一個在來電中顯示 **Hello World** 的 TwiML 回應。

    <?php    
        header("content-type: text/xml");    
        echo "<?xml version=\"1.0\" encoding=\"UTF-8\"?>\n";
    ?>
    <Response>    
        <Say>Hello world.</Say>
    </Response>

從以上範例中可知，TwiML 回應只是一份 XML 文件。適用於 PHP 的 Twilio 程式庫包含可為您產生 TwiML 的類別。下列範例會產生同上的回應，但改用適用於 PHP 的 Twilio 程式庫中的 **Services\_Twilio\_Twiml** 類別：

    require_once('Services/Twilio.php');

    $response = new Services_Twilio_Twiml();
    $response->say("Hello world.");
    print $response;

如需 TwiML 的詳細資訊，請參閱 [][7]<https://www.twilio.com/docs/api/twiml></a>。

設定 PHP 頁面來提供 TwiML 回應之後，請使用 PHP 頁面的 URL 作為傳遞到 `Services_Twilio->account->calls->create` 方法的 URL。例如，如果您已將名為 **MyTwiML** 的 Web 應用程式部署至 Azure 代管的服務，而 PHP 頁面的名稱為 **mytwiml.php**，則可將其 URL 傳至 **Services\_Twilio-\>account-\>calls-\>create**，如下列範例所示：

    require_once 'Services/Twilio.php';

    $sid = "your_twilio_account_sid";
    $token = "your_twilio_authentication_token";
    $from_number = "NNNNNNNNNNN";
    $to_number = "NNNNNNNNNNN";
    $url = "http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.php";

    // The phone message text.
    $message = "Hello world.";

    $client = new Services_Twilio($sid, $token, "2010-04-01");

    try
    {
        $call = $client->account->calls->create(
            $from_number, 
            $to_number,
            $url.'?Message='.urlencode($message)
        );
    }
    catch (Exception $e) 
    {
        echo 'Error: ' . $e->getMessage();
    }

如需在具有 PHP 的 Azure 中使用 Twilio 的相關資訊，請參閱[如何在 Azure 上的 PHP 應用程式中使用 Twilio 撥打電話][]。

## <span id="AdditionalServices"></span></a>作法：使用其他 Twilio 服務

除了此處所示的範例以外，Twilio 還提供網頁式 API，方便您從 Azure 應用程式中充份利用其他 Twilio 功能。如需完整詳細資料，請參閱[Twilio API 文件][Twilio API]。

## <span id="NextSteps"></span></a>後續步驟

了解基本的 Twilio 服務之後，請參考下列連結以取得更多資訊：

-   [Twilio 安全性方針][]
-   [Twilio 作法與範例程式碼][]
-   [Twilio 快速入門教學課程][]
-   [GitHub 上的 Twilio][]
-   [洽詢 Twilio 支援][]

  [後續步驟]: #NextSteps
  [什麼是 Twilio？]: #WhatIs
  [Twilio 定價]: #Pricing
  [概念]: #Concepts
  [建立 Twilio 帳戶]: #CreateAccount
  [驗證電話號碼]: #VerifyPhoneNumbers
  [建立 PHP 應用程式]: #create_app
  [設定應用程式以使用 Twilio 程式庫]: #configure_app
  [作法：撥出電話]: #howto_make_call
  [作法：傳送簡訊]: #howto_send_sms
  [作法：從您自己的網站提供 TwiML 回應]: #howto_provide_twiml_responses
  []: http://ahoy.twilio.com/azure
  [1]: http://www.twilio.com/pricing
  [Twilio API 程式庫]: https://www.twilio.com/docs/libraries
  [TwiML]: http://www.twilio.com/docs/api/twiml
  [Twilio API]: http://www.twilio.com/api
  [試用 Twilio]: https://www.twilio.com/try-twilio
  [Twilio 帳戶頁面]: https://www.twilio.com/user/account
  [管理電話號碼]: https://www.twilio.com/user/account/phone-numbers/verified#
  [Twilio 電話號碼說明]: https://www.twilio.com/help/faq/phone-numbers
  [適用於 PHP 的 Twiliofor 程式庫 (由 Github 提供)]: https://github.com/twilio/twilio-php
  [2]: http://readthedocs.org/docs/twilio-php/en/latest/index.html
  [如何在 Azure 上的 PHP 應用程式中使用 Twilio 撥打電話]: ../partner-twilio-php-make-phone-call
  [3]: https://github.com/twilio/twilio-php/blob/master/README.md
  [4]: http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html
  [5]: http://twimlets.com/message
  [6]: http://twimlets.com/message?Message%5B0%5D=Hello%20World
  [7]: https://www.twilio.com/docs/api/twiml
  [Twilio 安全性方針]: http://www.twilio.com/docs/security
  [Twilio 作法與範例程式碼]: http://www.twilio.com/docs/howto
  [Twilio 快速入門教學課程]: http://www.twilio.com/docs/quickstart
  [GitHub 上的 Twilio]: https://github.com/twilio
  [洽詢 Twilio 支援]: http://www.twilio.com/help/contact
