<properties title="How to make a phone call from Twilio (PHP) - Azure" pageTitle="How to make a phone call from Twilio (PHP) - Azure" metaKeywords="Azure PHP Twilio, Azure Twilio, Azure phone calls, Azure twilio, Azure SMS, Azure SMS, Azure voice calls, azure voice calls, Azure text messages, Azure text messages, PHP twilio Azure" description="Learn how to make a phone call and send a SMS message with the Twilio API service on Azure. Samples are for PHP application." documentationCenter="PHP" services="" solutions="" videoId="" scriptId="" authors="MicrosoftHelp@twilio.com; robmcm" manager="wpickett" editor="mollybos" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="01/01/1900" ms.author="MicrosoftHelp@twilio.com; robmcm"></tags>

# 如何在 Azure 上的 PHP 應用程式中使用 Twilio 撥打電話

下列範例將說明如何從 Azure 代管的 PHP 網頁上使用 Twilio 撥打電話。產生的應用程式會提示使用者提供電話值，如下列螢幕擷取畫面所示。

![Azure Call Form Using Twilio and PHP][]

您必須執行下列動作才能使用本主題中的程式碼：

1.  取得 Twilio 帳戶和驗證權杖。若要開始使用 Twilio，請在 [][]<http://www.twilio.com/pricing></a> 上評估價格。您可以在 [][1]<https://www.twilio.com/try-twilio></a> 上註冊試用帳戶。如需 Twilio 所提供之 API 的相關資訊，請參閱 [][2]<http://www.twilio.com/api></a>。
2.  向 Twilio 驗證您的電話號碼是外撥來電者 ID。如需如何驗證電話號碼的相關資訊，請參閱 [][3]<https://www.twilio.com/user/account/phone-numbers/verified>\#</a>。除了使用現有的電話號碼，您也可以購買 Twilio 電話號碼。
    在此範例中，請使用已驗證的電話號碼作為 **callform.php** 的 **From** 值 (後續將有說明)。
3.  取得適用於 PHP 的 Twilio 程式庫。您可以從 Github 下載此程式庫 ([][4]<https://github.com/twilio/twilio-php></a>)，或以 PEAR 封裝的形式安裝。如需詳細資訊，請參閱 [][5]<https://github.com/twilio/twilio-php/blob/master/README.md></a>。
4.  安裝 Azure SDK for PHP。如需 SDK 的概觀及其安裝指示，請參閱[設定 Azure SDK for PHP][]。

## 建立用以撥打電話的 Web 表單

下列 HTML 程式碼將說明如何建置網頁 (**callform.html**)，以擷取撥打電話所需的使用者資料：

    <html>
    <head>
        <title>Automated call form</title>
    </head>
    <body>
    <h1>Automated Call Form</h1>
    <p>Fill in all fields and click <b>Make this call</b>.</p>
    <form action="makecall.php" method="post">
    <table>
        <tr>
            <td>To:</td>
            <td><input type="text" size=50 name="callTo" value=""></td>
        </tr>
        <tr>
            <td>From:</td>
            <td><input type="text" size=50 name="callFrom" value=""></td>
        </tr>
        <tr>
            <td>Call message:</td>
            <td><input type="text" size=100 name="callText" value="Hello. This is the call text. Good bye." /></td>
        </tr>
        <tr>
            <td colspan=2><input type="submit" value="Make this call"></td>
        </tr>
    </table>
    </form>
    <br/>
    </body>
    </html>

## 建立用以撥打電話的程式碼

下列程式碼將說明如何建置會在使用者提交 **callform.html** 所顯示的表單時受到呼叫的網頁 (**makecall.php**)。下方顯示的程式碼會建立通話訊息及產生通話。(在下方的程式碼中，請使用您的 Twilio 帳戶和驗證權杖，而不要使用指派給 **$sid** 和 **$token** 的預留位置值。)

    <html>
    <head><title>Making call...</title></head>
    <body>
    <p>Your call is being made.</p>

    <?php
    require_once 'Services/Twilio.php';

    $sid = "your_account_sid";
    $token = "your_authentication_token";

    $from_number = $_POST['callFrom']; // Calls must be made from a registered Twilio number.
    $to_number = $_POST['callTo'];
    $message = $_POST['callText'];

    $client = new Services_Twilio($sid, $token, "2010-04-01");

    $call = $client->account->calls->create(
        $from_number, 
        $to_number,
        'http://twimlets.com/message?Message='.urlencode($message)
    );

    echo "Call status: ".$call->status."<br />";
    echo "URI resource: ".$call->uri."<br />";
    ?>
    </body>
    </html>

除了撥打電話以外，**makecall.php** 也會顯示某些通話中繼資料 (範例顯示於下方的螢幕擷取畫面中)。如需通話中繼資料的詳細資訊，請參閱 [][6]<https://www.twilio.com/docs/api/rest/call#instance-properties></a>。

![Azure Call Response Using Twilio and PHP][]

## 執行應用程式

下一個步驟是將您的應用程式部署至 Azure 網站。下列文章包含使用 Git、FTP 或 WebMatrix 來建立網站及部署程式碼的相關資訊 (並非每篇文章中的所有資訊都是相關的)：

-   [建立 PHP-MySQL Azure 網站並使用 Git 部署][]
-   [建立 PHP-MySQL Azure 網站並使用 FTP 部署][]
-   [使用 WebMatrix 建立並部署 PHP-MySQL Azure 網站][]

## 後續步驟

此程式可說明在 Azure 上的 PHP 中使用 Twilio 的基本功能。在部署至生產環境中的 Azure 之前，您可以新增更多錯誤處理或其他功能。例如：

-   除了使用 Web 表單以外，您也可以使用 Azure 儲存體 Blob 或 SQL Database 來儲存電話號碼和通話文字。如需在 PHP 中使用 Azure 儲存體 Blob 的相關資訊，請參閱[搭配使用 Azure 儲存體與 PHP 應用程式][]。如需在 PHP 中使用 SQL Database 的相關資訊，請參閱[搭配使用 SQL Database 與 PHP][]。
-   **makecall.php** 程式碼會使用 Twilio 提供的 URL ([][7]<http://twimlets.com/message></a>) 來提供 Twilio 標記語言 (TwiML) 回應，以告知 Twilio 應如何執行通話。例如，傳回的 TwiML 可能包含 `<Say>` 動詞，可產生要傳達給受話方的文字。除了使用 Twilio 提供的 URL 以外，您也可以建置自己的服務來回應 Twilio 的要求；如需詳細資訊，請參閱[如何在 PHP 中透過 Twilio 使用語音和簡訊功能][]。如需 TwiML 的詳細資訊，請參閱 [][8]<http://www.twilio.com/docs/api/twiml></a>；如需 `<Say>` 和其他 Twilio 動詞的詳細資訊，請參閱 [][9]<http://www.twilio.com/docs/api/twiml/say></a>。
-   閱讀 [][10]<https://www.twilio.com/docs/security></a> 上的 Twilio 安全性指引。

如需 Twilio 的其他資訊，請參閱 [][11]<https://www.twilio.com/docs></a>。

## 另請參閱

-   [如何在 PHP 中透過 Twilio 使用語音和簡訊功能][]

  [Azure Call Form Using Twilio and PHP]: ./media/partner-twilio-php-make-phone-call/WA_TwilioPHPCallForm.jpg
  []: http://www.twilio.com/pricing
  [1]: http://www.twilio.com/try-twilio
  [2]: http://www.twilio.com/api
  [3]: https://www.twilio.com/user/account/phone-numbers/verified#
  [4]: https://github.com/twilio/twilio-php
  [5]: https://github.com/twilio/twilio-php/blob/master/README.md
  [設定 Azure SDK for PHP]: http://azurephp.interoperabilitybridges.com/articles/setup-the-windows-azure-sdk-for-php
  [6]: https://www.twilio.com/docs/api/rest/call#instance-properties
  [Azure Call Response Using Twilio and PHP]: ./media/partner-twilio-php-make-phone-call/WA_TwilioPHPMakeCall.jpg
  [建立 PHP-MySQL Azure 網站並使用 Git 部署]: https://www.windowsazure.com/en-us/develop/php/tutorials/website-w-mysql-and-git/
  [建立 PHP-MySQL Azure 網站並使用 FTP 部署]: https://www.windowsazure.com/en-us/develop/php/tutorials/website-w-mysql-and-ftp/
  [使用 WebMatrix 建立並部署 PHP-MySQL Azure 網站]: https://www.windowsazure.com/en-us/develop/php/tutorials/website-w-mysql-and-webmatrix/
  [搭配使用 Azure 儲存體與 PHP 應用程式]: http://msdn.microsoft.com/en-us/library/windowsazure/hh674502(v=vs.103).aspx
  [搭配使用 SQL Database 與 PHP]: http://msdn.microsoft.com/en-us/library/windowsazure/hh674500(v=vs.103).aspx
  [7]: http://twimlets.com/message
  [如何在 PHP 中透過 Twilio 使用語音和簡訊功能]: ../partner-twilio-php-how-to-use-voice-sms
  [8]: http://www.twilio.com/docs/api/twiml
  [9]: http://www.twilio.com/docs/api/twiml/say
  [10]: http://www.twilio.com/docs/security
  [11]: http://www.twilio.com/docs
