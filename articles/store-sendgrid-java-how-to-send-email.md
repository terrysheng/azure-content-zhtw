<properties linkid="dev-java-how-to-access-control" urlDisplayName="SendGrid Email Service" pageTitle="How to use the SendGrid email service (Java) - Azure" metaKeywords="Azure SendGrid, Azure email service, Azure SendGrid Java, Azure email Java" description="Learn how send email with the SendGrid email service on Azure. Code samples written in Java." metaCanonical="" services="" documentationCenter="Java" title="How to Send Email Using SendGrid from Java" authors="waltpo" solutions="" manager="" editor="mollybos" />

如何使用 SendGrid 透過 Java 傳送電子郵件
========================================

本指南示範如何在 Azure 上透過 SendGrid 電子郵件服務執行常見程式設計工作。相關範例是以 Java 撰寫的。涵蓋的案例包括**建構電子郵件**、**傳送電子郵件**、**新增附件**、**使用篩選器**及**更新屬性**。如需 SendGrid 及傳送電子郵件的詳細資訊，請參閱[後續步驟](#bkmk_NextSteps)一節。

目錄
----

-   [什麼是 SendGrid 電子郵件服務？](#bkmk_WhatIsSendGrid)
-   [建立 SendGrid 帳戶](#bkmk_CreateSendGridAcct)
-   [作法：使用 javax.mail 程式庫](#bkmk_HowToUseJavax)
-   [作法：建立電子郵件](#bkmk_HowToCreateEmail)
-   [作法：傳送電子郵件](#bkmk_HowToSendEmail)
-   [作法：新增附件](#bkmk_HowToAddAttachment)
-   [作法：使用篩選器來啟用頁尾、追蹤和分析](#bkmk_HowToUseFilters)
-   [作法：更新電子郵件屬性](#bkmk_HowToUpdateEmail)
-   [作法：使用其他 SendGrid 服務](#bkmk_HowToUseAdditionalSvcs)
-   [後續步驟](#bkmk_NextSteps)

什麼是 SendGrid 電子郵件服務？
------------------------------

SendGrid 是[雲端架構電子郵件服務](http://sendgrid.com/solutions) (英文)，能提供可靠的[交易式電子郵件傳遞](http://sendgrid.com/transactional-email) (英文)，擴充性和即時分析，以及有彈性的 API 來輕鬆進行自訂整合。常見的 SendGrid 使用案例包括：

-   自動傳送回條給客戶
-   管理通訊群組清單，以便將每月電子傳單和特別優惠傳送給客戶
-   收集封鎖的電子郵件、客戶的回應情形等項目的即時度量
-   產生報表，協助找出趨勢
-   轉寄客戶查詢
-   透過電子郵件從您的應用程式傳送通知

如需詳細資訊，請參閱 &lt;http://sendgrid.com\&gt;。

建立 SendGrid 帳戶
------------------

[WACOM.INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

作法：使用 javax.mail 程式庫
----------------------------

取得 javax.mail 程式庫，例如從 &lt;http://www.oracle.com/technetwork/java/javamail\&gt; 並將其匯入您的程式碼中。使用 javax.mail 程式庫來傳送採用 SMTP 之電子郵件的高層級程序就是執行下列動作：

1.  指定 SMTP 值 (包括 SMTP 伺服器)，對 SendGrid 而言是 smtp.sendgrid.net。

        public class MyEmailer {
        private static final String SMTP_HOST_NAME = "smtp.sendgrid.net";
        private static final String SMTP_AUTH_USER = "your_sendgrid_username";
        private static final String SMTP_AUTH_PWD = "your_sendgrid_password";
            
        public static void main(String[] args) throws Exception{
              new MyEmailer().SendMail();
           }
            
        public void SendMail() throws Exception
           {
        Properties properties = new Properties();
              properties.put("mail.transport.protocol", "smtp");
              properties.put("mail.smtp.host", SMTP_HOST_NAME);
              properties.put("mail.smtp.port", 587);
              properties.put("mail.smtp.auth", "true");
              // ”¦

2.  擴充 javax.mail.Authenticator 類別，以及在 getPasswordAuthentication 方法的實作中，傳回 SendGrid 使用者名稱和密碼。

        private class SMTPAuthenticator extends javax.mail.Authenticator {
        public PasswordAuthentication getPasswordAuthentication() {
        String username = SMTP_AUTH_USER;
        String password = SMTP_AUTH_PWD;
        return new PasswordAuthentication(username, password);
        }

3.  透過 javax.mail.Session 物件建立經過驗證的電子郵件工作階段。

        Authenticator auth = new SMTPAuthenticator();
        Session mailSession = Session.getDefaultInstance(properties, auth);

4.  建立郵件並指派 **[收件者]**、**[寄件者]**、**[主旨]** 和內容值。如[作法：建立電子郵件](#bkmk_HowToCreateEmail)一節所示。
5.  透過 javax.mail.Transport 物件傳送郵件。如[作法：傳送電子郵件](#bkmk_HowToSendEmail)一節所示。

作法：建立電子郵件
------------------

下列程式碼顯示如何指定電子郵件的值。

    MimeMessage message = new MimeMessage(mailSession);
    Multipart multipart = new MimeMultipart("alternative");
    BodyPart part1 = new MimeBodyPart();
    part1.setText("Hello, Your Contoso order has shipped.Thank you, John");
    BodyPart part2 = new MimeBodyPart();
    part2.setContent(
        "<p>Hello,</p>
        <p>Your Contoso order has <b>shipped</b>.</p>
        <p>Thank you,<br>John</br></p>",
        "text/html");
    multipart.addBodyPart(part1);
    multipart.addBodyPart(part2);
    message.setFrom(new InternetAddress("john@contoso.com"));
    message.addRecipient(Message.RecipientType.TO,
    new InternetAddress("someone@example.com"));
    message.setSubject("Your recent order");
    message.setContent(multipart);

作法：傳送電子郵件
------------------

下列程式碼顯示如何傳送電子郵件。

    Transport transport = mailSession.getTransport();
    // Connect the transport object.
    transport.connect();
    // Send the message.
    transport.sendMessage(message, message.getRecipients(Message.RecipientType.TO));
    // Close the connection.
    transport.close();

作法：新增附件
--------------

下列程式碼顯示如何新增附件。

    // Local file name and path.
    String attachmentName = "myfile.zip";
    String attachmentPath = "c:\myfiles\"; 
    MimeBodyPart attachmentPart = new MimeBodyPart();
    // Specify the local file to attach.
    DataSource source = new FileDataSource(attachmentPath + attachmentName);
    attachmentPart.setDataHandler(new DataHandler(source));
    // This example uses the local file name as the attachment name.
    // They could be different if you prefer.
    attachmentPart.setFileName(attachmentName);
    multipart.addBodyPart(attachmentPart);

作法：使用篩選器來啟用頁尾、追蹤和分析
--------------------------------------

SendGrid 運用*篩選器*提供其他電子郵件功能。這些設定可新增到電子郵件以啟用特定功能，例如啟用點擊追蹤、Google 分析、訂閱追蹤等。如需完整的篩選器清單，請參閱[篩選器設定](http://sendgrid.com/docs/API_Reference/Web_API/filter_settings.html) (英文)。

-   下列程式碼顯示如何插入頁尾篩選器，以使 HTML 文字出現在傳送之電子郵件的底部。

        message.addHeader("X-SMTPAPI", 
        "{message.addHeader("X-SMTPAPI", 
        "{\"filters\": 
        {\"footer\": 
        {\"settings\": 
            {\"enable\":1,\"text/html\": 
        \"<html><b>Thank you</b> for your business.</html>\"}}}}");
        quot;filtersmessage.addHeader("X-SMTPAPI", 
        "{\"filters\": 
        {\"footer\": 
        {\"settings\": 
            {\"enable\":1,\"text/html\": 
        \"<html><b>Thank you</b> for your business.</html>\"}}}}");
        quot;: 
        {message.addHeader("X-SMTPAPI", 
        "{\"filters\": 
        {\"footer\": 
        {\"settings\": 
            {\"enable\":1,\"text/html\": 
        \"<html><b>Thank you</b> for your business.</html>\"}}}}");
        quot;footermessage.addHeader("X-SMTPAPI", 
        "{\"filters\": 
        {\"footer\": 
        {\"settings\": 
            {\"enable\":1,\"text/html\": 
        \"<html><b>Thank you</b> for your business.</html>\"}}}}");
        quot;: 
        {message.addHeader("X-SMTPAPI", 
        "{\"filters\": 
        {\"footer\": 
        {\"settings\": 
            {\"enable\":1,\"text/html\": 
        \"<html><b>Thank you</b> for your business.</html>\"}}}}");
        quot;settingsmessage.addHeader("X-SMTPAPI", 
        "{\"filters\": 
        {\"footer\": 
        {\"settings\": 
            {\"enable\":1,\"text/html\": 
        \"<html><b>Thank you</b> for your business.</html>\"}}}}");
        quot;: 
            {message.addHeader("X-SMTPAPI", 
        "{\"filters\": 
        {\"footer\": 
        {\"settings\": 
            {\"enable\":1,\"text/html\": 
        \"<html><b>Thank you</b> for your business.</html>\"}}}}");
        quot;enablemessage.addHeader("X-SMTPAPI", 
        "{\"filters\": 
        {\"footer\": 
        {\"settings\": 
            {\"enable\":1,\"text/html\": 
        \"<html><b>Thank you</b> for your business.</html>\"}}}}");
        quot;:1,message.addHeader("X-SMTPAPI", 
        "{\"filters\": 
        {\"footer\": 
        {\"settings\": 
            {\"enable\":1,\"text/html\": 
        \"<html><b>Thank you</b> for your business.</html>\"}}}}");
        quot;text/htmlmessage.addHeader("X-SMTPAPI", 
        "{\"filters\": 
        {\"footer\": 
        {\"settings\": 
            {\"enable\":1,\"text/html\": 
        \"<html><b>Thank you</b> for your business.</html>\"}}}}");
        quot;: 
        message.addHeader("X-SMTPAPI", 
        "{\"filters\": 
        {\"footer\": 
        {\"settings\": 
            {\"enable\":1,\"text/html\": 
        \"<html><b>Thank you</b> for your business.</html>\"}}}}");
        quot;<html><b>Thank you</b> for your business.</html>message.addHeader("X-SMTPAPI", 
        "{\"filters\": 
        {\"footer\": 
        {\"settings\": 
            {\"enable\":1,\"text/html\": 
        \"<html><b>Thank you</b> for your business.</html>\"}}}}");
        quot;}}}}");

-   另一個篩選器範例就是點擊追蹤。假設您的電子郵件文字包含超連結 (如下所示)，而您想要追蹤點擊率：

        messagePart.setContent(
        "Hello,
        <p>This is the body of the message.Visit 
        <a href='http://www.contoso.com'>http://www.contoso.com</a>.</p>
        Thank you.", 
            "text/html");

-   若要啟用點擊追蹤，請使用下列程式碼：

        message.addHeader("X-SMTPAPI", 
        "{message.addHeader("X-SMTPAPI", 
        "{\"filters\": 
        {\"clicktrack\": 
        {\"settings\": 
            {\"enable\":1}}}}");
        quot;filtersmessage.addHeader("X-SMTPAPI", 
        "{\"filters\": 
        {\"clicktrack\": 
        {\"settings\": 
            {\"enable\":1}}}}");
        quot;: 
        {message.addHeader("X-SMTPAPI", 
        "{\"filters\": 
        {\"clicktrack\": 
        {\"settings\": 
            {\"enable\":1}}}}");
        quot;clicktrackmessage.addHeader("X-SMTPAPI", 
        "{\"filters\": 
        {\"clicktrack\": 
        {\"settings\": 
            {\"enable\":1}}}}");
        quot;: 
        {message.addHeader("X-SMTPAPI", 
        "{\"filters\": 
        {\"clicktrack\": 
        {\"settings\": 
            {\"enable\":1}}}}");
        quot;settingsmessage.addHeader("X-SMTPAPI", 
        "{\"filters\": 
        {\"clicktrack\": 
        {\"settings\": 
            {\"enable\":1}}}}");
        quot;: 
            {message.addHeader("X-SMTPAPI", 
        "{\"filters\": 
        {\"clicktrack\": 
        {\"settings\": 
            {\"enable\":1}}}}");
        quot;enablemessage.addHeader("X-SMTPAPI", 
        "{\"filters\": 
        {\"clicktrack\": 
        {\"settings\": 
            {\"enable\":1}}}}");
        quot;:1}}}}");

作法：更新電子郵件屬性
----------------------

某些電子郵件屬性可使用 **set*Property*** 進行覆寫，或使用 **add*Property*** 進行附加。

例如，若要指定 **ReplyTo** 地址，請使用下列程式碼：

    InternetAddress addresses[] = 
        { new InternetAddress("john@contoso.com"),
    new InternetAddress("wendy@contoso.com") };

    message.setReplyTo(addresses);

若要增加 **副本** 收件者，請使用下列程式碼：

    message.addRecipient(Message.RecipientType.CC, new 
    InternetAddress("john@contoso.com"));

作法：使用其他 SendGrid 服務
----------------------------

SendGrid 提供的網頁式 API 可供從 Azure 應用程式運用其他 SendGrid 功能。如需完整詳細資料，請參閱 [SendGrid API 文件](http://sendgrid.com/docs/API_Reference/index.html) (英文)。

後續步驟
--------

了解 SendGrid 電子郵件服務的基本概念後，請參考下列連結以取得更多資訊。

-   示範在 Azure 部署中使用 SendGrid 的範例：[如何在 Azure 部署中使用 SendGrid 透過 Java 傳送電子郵件](/en-us/develop/java/how-to-guides/sendgrid-sample/)
-   SendGrid Java 資訊：&lt;http://sendgrid.com/docs/Code\_Examples/java.html\&gt;
-   SendGrid API 文件：&lt;http://sendgrid.com/docs/API\_Reference/index.html\&gt;
-   Azure 客戶的 SendGrid 特別優惠：&lt;http://sendgrid.com/azure.html\&gt;


