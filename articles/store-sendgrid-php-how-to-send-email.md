<properties title="How to use the SendGrid email service (PHP) - Azure" pageTitle="如何使用 SendGrid 電子郵件服務 (PHP) - Azure" metaKeywords="Azure SendGrid, Azure 電子郵件服務, Azure SendGrid PHP, Azure 電子郵件 PHP" description="了解如何在 Azure 上使用 SendGrid 電子郵件服務傳送電子郵件。以 PHP 撰寫的程式碼範例。" documentationCenter="PHP" services="" manager="wpickett" editor="mollybos" authors="robmcm" scriptId="" videoId="" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="10/30/2014" ms.author="elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork; matt.bernier@sendgrid.com" />

# 如何透過 PHP 使用 SendGrid 電子郵件服務

本指南示範如何在 Azure 上透過 SendGrid 電子郵件服務執行常見程式設計工作。相關範例是以 PHP 撰寫的。
涵蓋的案例包括「建構電子郵件」****、「傳送電子郵件」****和「新增附件」****。如需 SendGrid 與傳送電子郵件的詳細資訊，請參閱[後續步驟][]一節。

## 目錄

-   [什麼是 SendGrid 電子郵件服務][]
-   [建立 SendGrid 帳戶][]
-   [透過 PHP 應用程式使用 SendGrid][]
-   [做法：傳送電子郵件][]
-   [做法：新增附件][]
-   [做法：使用篩選器來啟用頁尾、追蹤和分析][]
-   [後續步驟][]

## <a name="bkmk_WhatIsSendGrid"> </a>什麼是 SendGrid 電子郵件服務？

SendGrid 是[雲端式電子郵件服務]，能提供可靠的[交易式電子郵件傳遞]、延展性和即時分析，以及有彈性的 API 來輕鬆進行自訂整合。常見的 SendGrid 使用案例包括：

-   自動傳送回條給客戶
-   管理通訊群組清單，以便將每月電子傳單和特別優惠傳送給客戶
-   收集封鎖的電子郵件、客戶的回應情形等項目的即時計量
-   產生報表，協助找出趨勢
-   轉寄客戶查詢
- 透過電子郵件從您的應用程式傳送通知

如需詳細資訊，請參閱 [https://sendgrid.com][]。

## <a name="bkmk_CreateSendGrid"> </a>建立 SendGrid 帳戶

[WACOM.INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="bkmk_UsingSendGridfromPHP"> </a>透過 PHP 應用程式使用 SendGrid

在 Azure PHP 應用程式中使用 SendGrid 並不需要特別的
設定或編碼。SendGrid 是一項服務，因此可
透過雲端應用程式存取，就像從內部部署
的應用程式存取一樣。

## <a name="bkmk_HowToSendEmail"> </a>作法：傳送電子郵件

您可以使用 SendGrid 提供的 SMTP 或 Web API 傳送電子郵件
。

### SMTP API

若要使用 SendGrid SMTP API 傳送電子郵件，請使用 *Swift Mailer*，它是元件型資料庫，可透過 PHP 應用程式傳送電子郵件。您可以從 [http://swiftmailer.org/download][] v5.3.0 ( 使用[編輯器]安裝 Swift Mailer) 下載 *Swift Mailer* 程式庫。使用程式庫傳送電子郵件牽涉到建立 <span class="auto-style2">Swift\_SmtpTransport</span>、 <span class="auto-style2">Swift\_Mailer</span>和 <span class="auto-style2">Swift\_Message</span> 類別的執行個體、設定適當的屬性和呼叫 <span class="auto-style2">Swift\_Mailer::send</span> 方法的定義。

    <?php
     include_once "vendor/autoload.php";
     /*
      * Create the body of the message (a plain-text and an HTML version).
      * $text is your plain-text email
      * $html is your html version of the email
      * If the reciever is able to view html emails then only the html
      * email will be displayed
      */ 
     $text = "Hi!\nHow are you?\n";
     $html = "<html>
           <head></head>
           <body>
               <p>Hi!<br>
                   How are you?<br>
               </p>
           </body>
           </html>";
     // This is your From email address
     $from = array('someone@example.com' => 'Name To Appear');
     // Email recipients
     $to = array(
           'john@contoso.com'=>'Destination 1 Name',
           'anna@contoso.com'=>'Destination 2 Name'
     );
     // Email subject
     $subject = 'Example PHP Email';

     // Login credentials
     $username = 'yoursendgridusername';
     $password = 'yourpassword';
     
     // Setup Swift mailer parameters
     $transport = Swift_SmtpTransport::newInstance('smtp.sendgrid.net', 587);
     $transport->setUsername($username);
     $transport->setPassword($password);
     $swift = Swift_Mailer::newInstance($transport);

     // Create a message (subject)
     $message = new Swift_Message($subject);

     // attach the body of the email
     $message->setFrom($from);
     $message->setBody($html, 'text/html');
     $message->setTo($to);
     $message->addPart($text, 'text/plain');
     
     // send message 
     if ($recipients = $swift->send($message, $failures))
     {
         // This will let us know how many users received this message
         echo 'Message sent out to '.$recipients.' users';
     }
     // something went wrong =(
     else
     {
         echo "Something went wrong - ";
         print_r($failures);
     }

### Web API

透過 PHP 的 [curl 函式][]使用 SendGrid Web API 傳送電子郵件。

    <?php

     $url = 'https://api.sendgrid.com/';
     $user = 'USERNAME';
     $pass = 'PASSWORD'; 

     $params = array(
          'api_user' => $user,
          'api_key' => $pass,
          'to' => 'john@contoso.com',
          'subject' => 'testing from curl',
          'html' => 'testing body',
          'text' => 'testing body',
          'from' => 'anna@contoso.com',
       );
       
     $request = $url.'api/mail.send.json';
     
     // Generate curl request
     $session = curl_init($request);
     
     // Tell curl to use HTTP POST
     curl_setopt ($session, CURLOPT_POST, true);
     
     // Tell curl that this is the body of the POST
     curl_setopt ($session, CURLOPT_POSTFIELDS, $params);
     
     // Tell curl not to return headers, but do return the response
     curl_setopt($session, CURLOPT_HEADER, false);
     curl_setopt($session, CURLOPT_RETURNTRANSFER, true);
     
     // obtain response
     $response = curl_exec($session);
     curl_close($session);
     
     // print everything out
     print_r($response);

SendGrid 的 Web API 與 REST API 十分類似，但並非真的是 REST 型 API，因為在大部分的呼叫中，GET 和 POST 動詞可以交換使用。

## <a name="bkmk_HowToAddAttachment"> </a>作法：新增附件

### SMTP API

要使用 SMTP API 傳送附件，您必須在使用 Swift Mailer 傳送電子郵件的範例指令碼中額外撰寫一行程式碼。

    <?php
     include_once "vendor/autoload.php";
     /*
      * Create the body of the message (a plain-text and an HTML version).
      * $text is your plain-text email
      * $html is your html version of the email
      * If the reciever is able to view html emails then only the html
      * email will be displayed
      */
     $text = "Hi!\nHow are you?\n";
      $html = "<html>
          <head></head>
          <body>
             <p>Hi!<br>
                How are you?<br>
             </p>
          </body>
          </html>";

     // This is your From email address
     $from = array('someone@example.com' => 'Name To Appear');
     
     // Email recipients
     $to = array(
          'john@contoso.com'=>'Destination 1 Name',
          'anna@contoso.com'=>'Destination 2 Name'
     );
     // Email subject
     $subject = 'Example PHP Email';
     
     // Login credentials
     $username = 'yoursendgridusername';
     $password = 'yourpassword';
     
     // Setup Swift mailer parameters
     $transport = Swift_SmtpTransport::newInstance('smtp.sendgrid.net', 587);
     $transport->setUsername($username);
     $transport->setPassword($password);
     $swift = Swift_Mailer::newInstance($transport);
     
     // Create a message (subject)
     $message = new Swift_Message($subject);
     
     // attach the body of the email
     $message->setFrom($from);
     $message->setBody($html, 'text/html');
     $message->setTo($to);
     $message->addPart($text, 'text/plain');
     $message->attach(Swift_Attachment::fromPath("path\to\file")->setFileName("file_name"));
     
     // send message 
     if ($recipients = $swift->send($message, $failures))
     {
          // This will let us know how many users received this message
          echo 'Message sent out to '.$recipients.' users';
     }
     // something went wrong =(
     else
     {
          echo "Something went wrong - ";
          print_r($failures);
     }

額外的一行程式碼如下：

     $message->attach(Swift_Attachment::fromPath("path\to\file")->setFileName('file_name'));

這行程式碼會在 <span class="auto-style2">Swift\_Message</span> 物件上呼叫 attach 方法，並在 Swift\_Attachment 類別上使用靜態方法 <span class="auto-style2">fromPath</span> 以 <span class="auto-style2">取得</span> 並附加檔案至訊息。

### Web API

使用 Web API 傳送附件與使用 Web API 傳送電子郵件十分類似。但請注意，在接下來的範例中，參數陣列必須包含此元素：

    'files['.$fileName.']' => '@'.$filePath.'/'.$fileName

範例：

    <?php

     $url = 'https://api.sendgrid.com/';
     $user = 'USERNAME';
     $pass = 'PASSWORD';
     
     $fileName = 'myfile';
     $filePath = dirname(__FILE__);

     $params = array(
         'api_user' => $user,
         'api_key' => $pass,
         'to' =>'john@contoso.com',
         'subject' => 'test of file sends',
         'html' => '<p> the HTML </p>',
         'text' => 'the plain text',
         'from' => 'anna@contoso.com',
         'files['.$fileName.']' => '@'.$filePath.'/'.$fileName
     );
     
     print_r($params);
     
     $request = $url.'api/mail.send.json';
     
     // Generate curl request
     $session = curl_init($request);
     
     // Tell curl to use HTTP POST
     curl_setopt ($session, CURLOPT_POST, true);
     
     // Tell curl that this is the body of the POST
     curl_setopt ($session, CURLOPT_POSTFIELDS, $params);
     
     // Tell curl not to return headers, but do return the response
     curl_setopt($session, CURLOPT_HEADER, false);
     curl_setopt($session, CURLOPT_RETURNTRANSFER, true);
     
     // obtain response
     $response = curl_exec($session);
     curl_close($session);
     
     // print everything out
     print_r($response);

## <a name="bkmk_HowToUseFilters"> </a>作法：使用篩選器來啟用頁尾、追蹤和分析

SendGrid 提供了運用 'filters' 的其他電子郵件功能。這些設定可新增到電子郵件以啟用特定功能，例如啟用點擊追蹤、Google 分析、訂閱追蹤等。

您可以使用篩選器屬性對訊息套用篩選器。每個篩選器都是由包含篩選器特定設定的雜湊來指定。以下範例會啟用頁尾篩選器，並指定文字訊息來附加到電子郵件訊息的下方。在此範例中，我們將使用 [sendgrid-php 程式庫]。使用[編輯器]安裝程式庫：
    
    php composer.phar require sendgrid/sendgrid 2.1.1

範例：    

    <?php
     /*
      * This example is used for sendgrid-php V2.1.1 (https://github.com/sendgrid/sendgrid-php/tree/v2.1.1)
      */
     include "vendor/autoload.php";

     $email = new SendGrid\Email();
     // The list of addresses this message will be sent to
     // [This list is used for sending multiple emails using just ONE request to SendGrid]
     $toList = array('john@contoso.com', 'anna@contoso.com');

     // Specify the names of the recipients
     $nameList = array('Name 1', 'Name 2');

     // Used as an example of variable substitution
     $timeList = array('4 PM', '5 PM');

     // Set all of the above variables
     $email->setTos($toList);
     $email->addSubstitution('-name-', $nameList);
     $email->addSubstitution('-time-', $timeList);

     // Specify that this is an initial contact message
     $email->addCategory("initial");

     // You can optionally setup individual filters here, in this example, we have 
     // enabled the footer filter
     $email->addFilter('footer', 'enable', 1);
     $email->addFilter('footer', "text/plain", "Thank you for your business");
     $email->addFilter('footer', "text/html", "Thank you for your business");

     // The subject of your email
     $subject = 'Example SendGrid Email';

     // Where is this message coming from. For example, this message can be from 
     // support@yourcompany.com, info@yourcompany.com
     $from = 'someone@example.com';

     // If you do not specify a sender list above, you can specifiy the user here. If 
     // a sender list IS specified above, this email address becomes irrelevant.
     $to = 'john@contoso.com';

     # 建立訊息內文 (純文字版本和 HTML 版本) 。 
     # 文字是純文字電子郵件 
     # HTML 是您的電子郵件的 HTML 版本
     # 如果收件者可以檢視 HTML 電子郵件，則只會顯示
     # HTML 電子郵件

     /*
      * Note the variable substitution here =)
      */
     $text = "
     Hello -name-,
     Thank you for your interest in our products. We have set up an appointment to call you at -time- EST to discuss your needs in more detail.
     Regards,
     Fred";

     $html = "
     <html> 
     <head></head>
     <body>
     <p>Hello -name-,<br>
     Thank you for your interest in our products. We have set up an appointment
     to call you at -time- EST to discuss your needs in more detail.

     Regards,

     Fred<br>
     </p>
     </body>
     </html>";

     // set subject
     $email->setSubject($subject);

     // attach the body of the email
     $email->setFrom($from);
     $email->setHtml($html);
     $email->addTo($to);
     $email->setText($text);

     // Your SendGrid account credentials
     $username = 'sendgridusername@yourdomain.com';
     $password = 'example';

     // Create SendGrid object
     $sendgrid = new SendGrid($username, $password);

     // send message
     $response = $sendgrid->send($email);

     print_r($response);

## <a name="bkmk_NextSteps"> </a>後續步驟

了解 SendGrid 電子郵件服務的基本概念後，請參考下列
連結以取得更多資訊。

-   SendGrid 文件：<https://sendgrid.com/docs>
-   SendGrid PHP 程式庫：<https://github.com/sendgrid/sendgrid-php>
-   Azure 客戶的 SendGrid 特別優惠：<https://sendgrid.com/windowsazure.html>

  [後續步驟]: #bkmk_NextSteps
  [什麼是 SendGrid 電子郵件服務]: #bkmk_WhatIsSendGrid
  [建立 SendGrid 帳戶]: #bkmk_CreateSendGrid
  [透過 PHP 應用程式使用 SendGrid]: #bkmk_UsingSendGridfromPHP
  [做法：傳送電子郵件]: #bkmk_HowToSendEmail
  [做法：新增附件]: #bkmk_HowToAddAttachment
  [做法：使用篩選器來啟用頁尾、追蹤和分析]: #bkmk_HowToUseFilters
  [做法：使用其他 SendGrid 服務]: #bkmk_HowToUseAdditionalSvcs
  [https://sendgrid.com]: https://sendgrid.com
  [https://sendgrid.com/transactional-email/pricing]: https://sendgrid.com/transactional-email/pricing
  [特殊優惠]: https://www.sendgrid.com/windowsazure.html
  [封裝和部署 Azure 的 PHP 應用程式]: http://msdn.microsoft.com/library/windowsazure/hh674499(v=VS.103).aspx
  [http://swiftmailer.org/download]: http://swiftmailer.org/download
  [curl 函式]: http://php.net/curl
  [雲端式電子郵件服務]: https://sendgrid.com/email-solutions
  [交易式電子郵件傳遞]: https://sendgrid.com/transactional-email
  [sendgrid-php 程式庫]: https://github.com/sendgrid/sendgrid-php/tree/v2.1.1
  [編輯器]: https://getcomposer.org/download/

<!--HONumber=35.2-->
