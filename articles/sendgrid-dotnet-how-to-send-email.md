<properties linkid="dev-net-how-to-sendgrid-email-service" urlDisplayName="SendGrid Email Service" pageTitle="How to use the SendGrid email service (.NET) - Azure" metaKeywords="Azure SendGrid, Azure email service, Azure SendGrid .NET, Azure email .NET, Azure SendGrid C#, Azure email C#" description="Learn how send email with the SendGrid email service on Azure. Code samples written in C# and use the .NET API." metaCanonical="" services="" documentationCenter=".NET" title="How to Send Email Using SendGrid with Azure" authors="" solutions="" manager="" editor="" />

如何在 Azure 上使用 SendGrid 傳送電子郵件
=========================================

本指南示範如何在 Azure 上透過 SendGrid 電子郵件服務執行常見程式設計工作。相關範例是以 C\# 撰寫並使用 .NET API。涵蓋的案例包括**建構電子郵件**、**傳送電子郵件**、**新增附件**及**使用篩選器**。如需 SendGrid 及傳送電子郵件的詳細資訊，請參閱[後續步驟](#nextsteps)一節。

目錄
----

[什麼是 SendGrid 電子郵件服務？](#whatis)
 [建立 SendGrid 帳戶](#createaccount)
 [參考 SendGrid .NET 類別庫](#reference)
 [作法：建立電子郵件](#createemail)
 [作法：傳送電子郵件](#sendemail)
 [作法：新增附件](#addattachment)
 [作法：使用篩選器來啟用頁尾、追蹤和分析](#usefilters)
 [作法：使用其他 SendGrid 服務](#useservices)
 [後續步驟](#nextsteps)

什麼是 SendGrid 電子郵件服務？什麼是 SendGrid 電子郵件服務？
------------------------------------------------------------

SendGrid 是[雲端架構電子郵件服務](http://sendgrid.com/solutions) (英文)，能提供可靠的[交易式電子郵件傳遞](http://sendgrid.com/transactional-email) (英文)，擴充性和即時分析，以及有彈性的 API 來輕鬆進行自訂整合。常見的 SendGrid 使用案例包括：

-   自動傳送回條給客戶。
-   管理通訊群組清單，以便將每月電子傳單和特別優惠傳送給客戶。
-   收集即時事物 (如封鎖的電子郵件) 的度量以及客戶回應。
-   產生報表，協助找出趨勢。
-   轉寄客戶查詢。

如需詳細資訊，請參閱 <http://sendgrid.com>。

建立 SendGrid 帳戶建立 SendGrid 帳戶
------------------------------------

[WACOM.INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

參考 SendGrid .NET 類別庫參考 SendGrid .NET 類別庫
--------------------------------------------------

SendGrid NuGet 封裝是取得 SendGrid API 及透過所有相依性設定應用程式的最簡單方式。NuGet 是 Microsoft Visual Studio 2012 隨附的 Visual Studio 延伸模組，能輕鬆地安裝及更新程式庫和工具。

**注意**

如果您是執行 Visual Studio 2012 之前的 Visual Studio 版本，若要安裝 NuGet，請造訪 <http://www.nuget.org> (英文)，然後按一下 **[安裝 NuGet]** 按鈕。

若要在應用程式中安裝 SendGrid NuGet 封裝，請執行下列動作：

1.  在 **[方案總管]** 中，以滑鼠右鍵按一下 **[參考]**，然後按一下 **[Manage NuGet Packages]**。

2.  在 **[Manage NuGet Packages]** 對話方塊的左窗格中，按一下 **[線上]**。

3.  搜尋 **SendGrid**，然後選取結果清單中的 **[SendGrid]** 項目。

    ![SendGrid NuGet 封裝](./media/sendgrid-dotnet-how-to-send-email/sendgrid01.png)

4.  按一下 **[安裝]** 完成安裝，然後關閉此對話方塊。

SendGrid 的 .NET 類別庫稱為 **SendGridMail**。其中包含下列命名空間：

-   **SendGridMail** 可供建立和處理電子郵件項目。
-   **SendGridMail.Transport** 可供使用 **SMTP** 通訊協定或 HTTP 1.1 通訊協定 (透過 **Web/REST**) 傳送電子郵件。

將下列程式碼命名空間宣告，新增至您想要在其中以程式設計方式存取 SendGrid 電子郵件服務之任何 C\# 檔案內的頂端。**System.Net** 和 **System.Net.Mail** 均為 .NET Framework 命名空間，其納入原因是因為包含您通常搭配 SendGrid API 使用的類型。

    using System.Net;
    using System.Net.Mail;
    using SendGridMail;
    using SendGridMail.Transport;

作法：建立電子郵件作法：建立電子郵件
------------------------------------

使用靜態 **SendGrid.GetInstance** 方法，建立 **SendGrid** 類型的電子郵件。建立電子郵件後，即可使用 **SendGrid** 屬性和方法設定相關值，包括電子郵件寄件者、電子郵件收件者以及電子郵件的主旨和本文。

下列範例示範如何建立完全填入的電子郵件物件：

    // Setup the email properties.
    var from = new MailAddress("john@contoso.com");
    var to   = new MailAddress[] { new MailAddress("jeff@contoso.com") };
    var cc   = new MailAddress[] { new MailAddress("anna@contoso.com") };
    var bcc  = new MailAddress[] { new MailAddress("peter@contoso.com") };
    var subject = "Testing the SendGrid Library";
    var html    = "<p>Hello World!</p>";
    var text = "Hello World plain text!";
    var transport = SendGridMail.TransportType.SMTP;

    // Create an email, passing in the the eight properties as arguments.
    SendGrid myMessage = SendGrid.GetInstance(from, to, cc, bcc, subject, html, text, transport);

下列範例示範如何建立空的電子郵件物件：

    // Create the email object first, then add the properties.
    SendGrid myMessage = SendGrid.GetInstance();
     
    // Add the message properties.
    MailAddress sender = new MailAddress(@"John Smith <john@contoso.com>");
    myMessage.From = sender;
     
    // Add multiple addresses to the To field.
    List<String> recipients = new List<String>
    {
        @"Jeff Smith <jeff@contoso.com>",
        @"Anna Lidman <anna@contoso.com>",
        @"Peter Saddow <peter@contoso.com>"
    };
     
    foreach (string recipient in recipients)
    {
        myMessage.AddTo(recipient);
    }
     
    // Add a message body in HTML format.
    myMessage.Html = "<p>Hello World!</p>";

    // Add the subject.
    myMessage.Subject = "Testing the SendGrid Library";

如需 **SendGrid** 類型支援的所有屬性和方法的詳細資訊，請參閱 GitHub 上的 [sendgrid-csharp](https://github.com/sendgrid/sendgrid-csharp) (英文)。

作法：傳送電子郵件作法：傳送電子郵件
------------------------------------

建立電子郵件之後，您可以使用 SendGrid 提供的 SMTP 或 Web API 進行傳送。如需各 API 優缺點的詳細資訊，請參閱 SendGrid 文件中的 [SMTP 與 Web API 的比較](http://docs.sendgrid.com/documentation/get-started/integrate/examples/smtp-vs-rest/)。

您必須提供 SendGrid 帳戶認證 (使用者名稱和密碼)，才能透過任一通訊協定傳送電子郵件。下列程式碼示範如何在 **NetworkCredential** 物件中包裝認證：

    // Create network credentials to access your SendGrid account.
    var username = "your_sendgrid_username";
    var pswd = "your_sendgrid_password";

    var credentials = new NetworkCredential(username, pswd);

若要傳送電子郵件，請在 **SMTP** 類別 (使用 SMTP 通訊協定) 或 **REST** 傳輸類別 (呼叫 SendGrid Web API) 上使用 **[傳遞]** 方法。下列範例顯示如何使用 SMTP 與 Web API 傳送郵件。

### SMTP

    // Create the email object first, then add the properties.
    SendGrid myMessage = SendGrid.GetInstance();
    myMessage.AddTo("anna@contoso.com");
    myMessage.From = new MailAddress("john@contoso.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    // Create credentials, specifying your user name and password.
    var credentials = new NetworkCredential("username", "password");

    // Create an SMTP transport for sending email.
    var transportSMTP = SMTP.GetInstance(credentials);

    // Send the email.
    transportSMTP.Deliver(myMessage);

### Web API

    // Create the email object first, then add the properties.
    SendGrid myMessage = SendGrid.GetInstance();
    myMessage.AddTo("anna@contoso.com");
    myMessage.From = new MailAddress("john@contoso.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    // Create credentials, specifying your user name and password.
    var credentials = new NetworkCredential("username", "password");

    // Create a REST transport for sending email.
    var transportREST = Web.GetInstance(credentials);

    // Send the email.
    transportREST.Deliver(myMessage);

作法：新增附件作法：新增附件
----------------------------

呼叫 **AddAttachment** 方法及指定您要附加的檔案名稱和路徑，即可將附件新增至郵件。您可以對想要附加的每個檔案呼叫一次此方法，即可包含多個附件。下列範例示範如何將附件新增至郵件：

    SendGrid myMessage = SendGrid.GetInstance();
    myMessage.AddTo("anna@contoso.com");
    myMessage.From = new MailAddress("john@contoso.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    myMessage.AddAttachment(@"C:\file1.txt");

作法：使用篩選器來啟用頁尾、追蹤和分析作法：使用篩選器來啟用頁尾、追蹤和分析
----------------------------------------------------------------------------

SendGrid 提供了運用篩選器的其他電子郵件功能。這些設定可新增到電子郵件以啟用特定功能，例如點選追蹤、Google 分析、訂閱追蹤等。如需完整的篩選器清單，請參閱[篩選器設定](http://docs.sendgrid.com/documentation/api/smtp-api/filter-settings/)。

使用作為 **SendGrid** 類別的一部分實作的方法，即可將篩選器套用到 **SendGrid** 電子郵件。您必須先藉由呼叫 **InitalizeFilters** 方法來初始化可用的篩選器清單，才能在電子郵件上啟用篩選器。

下列範例示範頁尾和點選追蹤篩選器：

### 頁尾

    // Create the email object first, then add the properties.
    SendGrid myMessage = SendGrid.GetInstance();
    myMessage.AddTo("anna@contoso.com");
    myMessage.From = new MailAddress("john@contoso.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    myMessage.InitializeFilters();
    // Add a footer to the message.
    myMessage.EnableFooter("PLAIN TEXT FOOTER", "<p><em>HTML FOOTER</em></p>");

### 點選追蹤

    // Create the email object first, then add the properties.
    SendGrid myMessage = SendGrid.GetInstance();
    myMessage.AddTo("anna@contoso.com");
    myMessage.From = new MailAddress("john@contoso.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Html = "<p><a href=\"http://www.windowsazure.com\">Hello World Link!</a></p>";
    myMessage.Text = "Hello World!";

    myMessage.InitializeFilters();
    // true indicates that links in plain text portions of the email 
    // should also be overwritten for link tracking purposes. 
    myMessage.EnableClickTracking(true);

作法：使用其他 SendGrid 服務作法：使用其他 SendGrid 服務
--------------------------------------------------------

SendGrid 提供的網頁式 API 可供從 Azure 應用程式運用其他 SendGrid 功能。如需完整詳細資料，請參閱 [SendGrid API 文件](http://docs.sendgrid.com/documentation/api/)。

後續步驟後續步驟
----------------

了解 SendGrid 電子郵件服務的基本概念後，請參考下列連結以取得更多資訊。

-   SendGrid C\# 程式庫儲存機制：[sendgrid-csharp](https://github.com/sendgrid/sendgrid-csharp)
-   SendGrid API 文件：<http://docs.sendgrid.com/documentation/api/>
-   Azure 客戶的 SendGrid 特別優惠：<http://sendgrid.com>

