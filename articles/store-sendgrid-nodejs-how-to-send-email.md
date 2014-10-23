<properties linkid="dev-nodejs-how-to-sendgrid-email-service" urlDisplayName="SendGrid Email Service" pageTitle="How to use the SendGrid email service (Node.js) - Azure" metaKeywords="Azure SendGrid, Azure email service, Azure SendGrid Node.js, Azure email Node.js" description="Learn how send email with the SendGrid email service on Azure. Code samples written using the Node.js API." metaCanonical="" services="" documentationCenter="nodejs" title="How to Send Email Using SendGrid from Node.js" authors="elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork" solutions="" manager="wpickett" editor="" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="01/01/1900" ms.author="elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork"></tags>

# 如何使用 SendGrid 透過 Node.js 傳送電子郵件

本指南示範如何在 Azure 上透過 SendGrid 電子郵件服務執行常見程式設計工作。這些範例使用 Node.js API 撰寫。涵蓋的案例包括**建構電子郵件**、**傳送電子郵件**、**新增附件**、**使用篩選器**及**更新屬性**。如需有關 SendGrid 及傳送電子郵件的詳細資訊，請參閱[後續步驟][]一節。

## 目錄

-   [什麼是 SendGrid 電子郵件服務？][]
-   [建立 SendGrid 帳戶][]
-   [參考 SendGrid Node.js 模組][]
-   [作法：建立電子郵件][]
-   [作法：傳送電子郵件][]
-   [作法：新增附件][]
-   [作法：使用篩選器來啟用頁尾、追蹤和分析][]
-   [作法：更新電子郵件屬性][]
-   [作法：使用其他 SendGrid 服務][]
-   [後續步驟][1]

## <a name="whatis"> </a>什麼是 SendGrid 電子郵件服務？

SendGrid 是[雲端架構電子郵件服務][] (英文)，能提供可靠的[交易式電子郵件傳遞][] (英文)、擴充性和即時分析，以及有彈性的 API來輕鬆進行自訂整合。常見的 SendGrid 使用案例包括：

-   自動傳送回條給客戶
-   管理通訊群組清單，以便將每月
    電子傳單和特別優惠傳送給客戶
-   收集即時事物 (如封鎖的電子郵件) 的度量以及
    客戶回應
-   產生報表，協助找出趨勢
-   轉寄客戶查詢
-   透過電子郵件從您的應用程式傳送通知

如需詳細資訊，請參閱 [][]<http://sendgrid.com></a>。

## <a name="createaccount"> </a>建立 SendGrid 帳戶

[WACOM.INCLUDE [sendgrid-sign-up][]]

## <a name="reference"> </a>參考 SendGrid Node.js 模組

您可以使用下列命令，透過節點封裝管理員 (npm) 安裝適用於 Node.js 的 SendGrid 模組：

    npm install sendgrid

安裝之後，您可以使用下列程式碼要求您應用程式中的模組：

    var SendGrid = require('sendgrid')

SendGrid module 會匯出 **SendGrid** 和 **Email** 函數。**SendGrid** 負責透過 SMTP 或 WebAPI 傳送電子郵件，而 **Email** 則封裝電子郵件訊息。

## <a name="createemail"> </a>作法：建立電子郵件

使用 SendGrid 模組建立電子郵件訊息涉及先使用 Email 函數建立電子郵件訊息，再使用 SendGrid 函數傳送該電子郵件訊息。以下是使用 Email 函數建立新訊息的範例：

    var mail = new SendGrid.Email({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text: 'This is a sample email message.'
    });

您也可以設定 html 屬性，為支援 HTML 訊息的用戶端指定一個 HTML 訊息。例如：

    html: This is a sample <b>HTML<b> email message.

同時設定 text 和 html 屬性可以為無法支援 HTML 訊息的用戶端提供正常的文字內容遞補。

如需有關 Email 函數所支援之所有屬性的詳細資訊，請參閱 [sendgrid-nodejs][]。

## <a name="sendemail"> </a>作法：傳送電子郵件

使用 Email 函數建立電子郵件訊息之後，您可以使用 SendGrid 所提供的 SMTP 或 Web API 進行傳送。如需有關各個 API 優點和差異的詳細資料，請參閱 SendGrid 文件中的 [SMTP 與 Web API 的比較][]。

不論是使用 SMTP API 或 Web API，您都必須先使用您 SendGrid 帳戶的使用者和金鑰來初始化 SendGrid 函數，如下：

    var sender = new SendGrid('user','key');

現在可以使用 SMTP 或 Web API 來傳送訊息。呼叫實際上相同，都會傳送電子郵件訊息和選用的回呼函數；回呼是用來判斷作業是否成功。下列範例顯示如何使用 SMTP 與 Web API 傳送郵件。

### SMTP

    sender.smtp(mail, function(success, err){
        if(success) console.log('Email sent');
        else console.log(err);
    });

### Web API

    sender.send(mail, function(success, err){
        if(success) console.log('Email sent');
        else console.log(err);
    });

<div class="dev-callout">
<strong>注意</strong>
<p>雖然上述範例顯示傳入 email 物件和
callback 函數，但您也可以直接叫用 send 和 smtp
函數，方法是直接指定電子郵件屬性。例如：</p>
<pre class="prettyprint">sender.send({
to:'john@contoso.com',
from:'anna@contoso.com',
subject:'test mail',
text:'This is a sample email message.'
});
</pre>
</div>

## <a name="addattachment"> </a>作法：新增附件

您可以透過在**files** 屬性中指定檔案名稱和路徑，將附件新增至訊息中。下列範例示範如何傳送附件：

    sender.send({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text: 'This is a sample email message.',
        files: {
            'file1.txt': __dirname + '/file1.txt',
            'image.jpg': __dirname + '/image.jpg'
        }
    });

<div class="dev-callout">
<strong>注意</strong>
<p>使用 <strong>files</strong> 屬性時，必須要能夠透過 
<a href="http://nodejs.org/docs/v0.6.7/api/fs.html#fs.readFile">fs.readFile</a> 存取。如果您想要附加的檔案裝載於 Azure 儲存體中 (例如 Blob 容器中)，您就必須先將該檔案複製到本機儲存體或 Azure 磁碟機，才能使用 <strong>files</strong> 屬性以附件形式傳送它。</p>
</div>

## <a name="usefilters"> </a>作法：使用篩選器來啟用頁尾、追蹤和分析

SendGrid 提供了運用篩選器的其他電子郵件功能。這些設定可新增到電子郵件以啟用特定功能，例如啟用點擊追蹤、Google分析、訂閱追蹤等。如需完整的篩選器清單，請參閱[篩選器設定][]。

您可以使用 **filters** 屬性將篩選器套用到訊息。每個篩選器都是由包含篩選器特定設定的雜湊來指定。下列範例示範頁尾、點選追蹤及 Twitter 篩選器：

### 頁尾

    sender.send({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text: 'This is a sample email message.',
        filters: {
            'footer': {
                'settings': {
                    'enable': 1,
                    'text/plain': 'This is a text footer.'
                }
            }
        }
    });

### 點選追蹤

    sender.send({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text: 'This is a sample email message.',
        filters: {
            'clicktrack': {
                'settings': {
                    'enable': 1
                }
            }
        }
    });

### Twitter

    sender.send({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text: 'This is a sample email message.',
        filters: {
            'twitter': {
                'settings': {
                    'enable': 1,
                    'username': 'twitter_username',
                    'password': 'twitter_password'
                }
            }
        }
    });

## <a name="updateproperties"> </a>作法：更新電子郵件屬性

某些電子郵件屬性可使用 **set*Property*** 進行覆寫，或使用**add*Property*** 進行附加。例如，您可以使用下列方式新增其他收件者：

    email.addTo('jeff@contoso.com');

或使用下列方式設定篩選器：

    email.setFilterSetting({
      'footer': {
        'setting': {
          'enable': 1,
          'text/plain': 'This is a footer.'
        }
      }
    });

如需詳細資訊，請參閱 [sendgrid-nodejs][] (英文)。

## <a name="useservices"> </a>作法：使用其他 SendGrid 服務

SendGrid 提供的網頁式 API 可供從 Azure 應用程式運用其他 SendGrid 功能。如需完整詳細資料，請參閱 [SendGrid API 文件][] (英文)。

## <a name="nextsteps"> </a>後續步驟

了解 SendGrid 電子郵件服務的基本概念後，請參考下列連結以取得更多資訊。

-   SendGrid Node.js 模組儲存機制：[sendgrid-nodejs][]
-   SendGrid API 文件：
    <http://docs.sendgrid.com/documentation/api/>
-   Azure 客戶的 SendGrid 特別優惠：
    [][2]<http://sendgrid.com/azure.html></a>

  [後續步驟]: http://www.windowsazure.com/en-us/develop/nodejs/how-to-guides/blob-storage/#next-steps
  [什麼是 SendGrid 電子郵件服務？]: #whatis
  [建立 SendGrid 帳戶]: #createaccount
  [參考 SendGrid Node.js 模組]: #reference
  [作法：建立電子郵件]: #createemail
  [作法：傳送電子郵件]: #sendemail
  [作法：新增附件]: #addattachment
  [作法：使用篩選器來啟用頁尾、追蹤和分析]: #usefilters
  [作法：更新電子郵件屬性]: #updateproperties
  [作法：使用其他 SendGrid 服務]: #useservices
  [1]: #nextsteps
  [雲端架構電子郵件服務]: http://sendgrid.com/solutions
  [交易式電子郵件傳遞]: http://sendgrid.com/transactional-email
  []: http://sendgrid.com
  [sendgrid-sign-up]: ../includes/sendgrid-sign-up.md
  [sendgrid-nodejs]: https://github.com/sendgrid/sendgrid-nodejs
  [SMTP 與 Web API 的比較]: http://docs.sendgrid.com/documentation/get-started/integrate/examples/smtp-vs-rest/
  [fs.readFile]: http://nodejs.org/docs/v0.6.7/api/fs.html#fs.readFile
  [篩選器設定]: http://docs.sendgrid.com/documentation/api/smtp-api/filter-settings/
  [SendGrid API 文件]: http://docs.sendgrid.com/documentation/api/
  [2]: http://sendgrid.com/azure.html
