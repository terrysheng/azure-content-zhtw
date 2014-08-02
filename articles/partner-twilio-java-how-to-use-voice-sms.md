<properties linkid="develop-java-how-to-twilio-sms-service" urlDisplayName="Twilio Voice/SMS Service" pageTitle="How to Use Twilio for Voice and SMS (Java) - Azure" metaKeywords="Twilio, Twilio API, phone calls, SMS message, TwiML responses, Azure Twilio Java" description="Learn how to make a phone call and send a SMS message with the Twilio API service on Azure. Code samples written in Java." metaCanonical="" services="" videoId="" scriptId="" documentationCenter="Java" title="How to Use Twilio for Voice and SMS Capabilities in Java" authors="waltpo" solutions="" manager="bjsmith" editor="mollybos" />

如何在 Java 中使用 Twilio 加入語音和 SMS 功能
=============================================

本指南示範如何在 Azure 上透過 Twilio API 服務執行常見程式設計工作。所涵蓋的案例包括撥打電話和傳送簡訊 (SMS)。如需關於 Twilio 及在應用程式中使用語音和 SMS 的詳細資訊，請參閱[後續步驟](#NextSteps)一節。

目錄
----

-   [什麼是 Twilio？](#WhatIs)
-   [Twilio 定價](#Pricing)
-   [概念](#Concepts)
-   [建立 Twilio 帳戶](#CreateAccount)
-   [驗證電話號碼](#VerifyPhoneNumbers)
-   [建立 Java 應用程式](#create_app)
-   [設定您的應用程式以使用 Twilio 程式庫](#configure_app)
-   [作法：撥出電話](#howto_make_call)
-   [作法：傳送 SMS 訊息](#howto_send_sms)
-   [作法：從自己的網站提供 TwiML 回應](#howto_provide_twiml_responses)
-   [作法：使用其他 Twilio 服務](#AdditionalServices)
-   [後續步驟](#NextSteps)

什麼是 Twilio？
---------------

Twilio 是一種電話語音 Web 服務 API，能夠讓您使用現有的 Web 語言和技術建立語音和 SMS 應用程式。Twilio 算是協力廠商服務 (並非 Azure 功能，也並非 Microsoft 產品)。

**Twilio Voice** 可以讓您的應用程式撥打電話和接聽來電。**Twilio SMS** 可以讓您的應用程式撰寫和接收 SMS 訊息。**Twilio Client** 可以讓您的應用程式在現有網際網路連線 (包括行動連線) 中啟用語音通訊。

Twilio 定價和特別優惠
---------------------

[Twilio 定價](http://www.twilio.com/pricing) (英文) 提供 Twilio 的定價資訊。Azure 客戶可獲得[特別優惠](http://ahoy.twilio.com/azure) (英文)：免費 1000 則文字簡訊或接聽 1000 分鐘電話。若要註冊獲得這項優惠或取得詳細資訊，請造訪 <http://ahoy.twilio.com/azure> (英文)。

概念
----

Twilio API 是一種可為應用程式提供語音和 SMS 功能且符合 REST 限制的 API。用戶端程式碼有多種語言版本；如需清單，請參閱 [Twilio API 程式庫](https://www.twilio.com/docs/libraries) (英文)。

Twilio API 的主要方面是 Twilio 動詞和 Twilio 標記語言 (TwiML)。

### Twilio 動詞

API 會使用 Twilio 動詞；例如 **&lt;Say\>** 動詞會指示 Twilio 在收到通話時傳送語音訊息。

以下是 Twilio 動詞的清單。

-   **&lt;Dial\>**：將來電者連接到其他台電話。
-   **&lt;Gather\>**：收集以電話鍵台輸入的數字。
-   **&lt;Hangup\>**：結束通話。
-   **&lt;Play\>**：播放音訊檔案。
-   **&lt;Pause\>**：靜待指定的秒數。
-   **&lt;Record\>**：錄下來電者的語音，並傳回該錄音檔案的 URL。
-   **&lt;Redirect\>**：將電話或 SMS 的控制權轉給位於不同 URL 的 TwiML。
-   **&lt;Reject\>**：拒接撥打到您 Twilio 號碼的來電，而不向您收費
-   **&lt;Say\>**：將文字轉換為在通話中發出的語音。
-   **&lt;Sms\>**：傳送 SMS 訊息。

### TwiML

TwiML 是一組以 Twilio 動詞為基礎的 XML 型指示，會通知 Twilio 如何處理通話或 SMS。

例如，下列 TwiML 會將 **Hello World** 文字轉換為語音。

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

當您的應用程式呼叫 Twilio API 時，其中一個 API 參數是會傳回 TwiML 回應的 URL。進行開發時，可使用 Twilio 提供的 URL 來提供您的應用程式所使用的 TwiML 回應。您也可以裝載自己的 URL 來產生 TwiML 回應，而另一個選擇是使用 **TwiMLResponse** 物件。

如需 Twilio 動詞、其屬性及 TwiML 的詳細資訊，請參閱 [TwiML](http://www.twilio.com/docs/api/twiml) (英文)。如需 Twilio API 的詳細資訊，請參閱 [Twilio API](http://www.twilio.com/api) (英文)。

建立 Twilio 帳戶
----------------

準備好要取得 Twilio 帳戶時，請在[試用 Twilio](https://www.twilio.com/try-twilio) (英文) 註冊。您可以一開始先使用免費帳戶，日後再升級帳戶。

當您註冊 Twilio 帳戶時，您將收到帳戶 ID 和驗證權杖。需要這兩者才能進行 Twilio API 通話。為了避免您的帳戶遭受未經授權的存取，請妥善保管您的驗證權杖。在 [Twilio 帳戶頁面](https://www.twilio.com/user/account) (英文) 的 **[ACCOUNT SID]** 和 **[AUTH TOKEN]** 欄位中，可以分別檢視帳戶 ID 和驗證權杖。

驗證電話號碼
------------

您的帳戶需要通過 Twilio 的各種電話號碼驗證。例如，如果要撥出電話，必須向 Twilio 驗證該電話號碼作為撥出通話者 ID。同樣地，如果要讓電話號碼接收 SMS 訊息，必須向 Twilio 驗證進行接收的電話號碼。如需關於如何驗證電話號碼的詳細資訊，請參閱[管理號碼](https://www.twilio.com/user/account/phone-numbers/verified#) (英文)。下列部分程式碼要有您需要向 Twilio 驗證的電話號碼。

除了讓您的應用程式使用現有的號碼之外，您也可以購買 Twilio 電話號碼。如需關於購買 Twilio 電話號碼的詳細資訊，請參閱 [Twilio 電話號碼說明](https://www.twilio.com/help/faq/phone-numbers) (英文)。

建立 Java 應用程式
------------------

1.  取得 Twilio JAR 並將它加到您的 Java 組建路徑和 WAR 部署組件。在 <https://github.com/twilio/twilio-java> (英文)，您可以下載 GitHub 原始檔並建立自己的 JAR，也可以下載預先建置的 JAR (含或不含相依性)。
2.  確定 JDK 的 **cacerts** 金鑰存放區包含 MD5 指紋為 67:CB:9D:C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 (序號為 35:DE:F4:CF 且 SHA1 指紋為 D2:32:09:AD:23:D3:14:23:21:74:E4:0D:7F:9D:62:13:97:86:63:3A) 的 Equifax 安全憑證授權單位憑證。這是 <https://api.twilio.com> (英文) 服務的憑證授權單位 (CA) 憑證，您使用 Twilio API 時便會呼叫。如需關於確定 JDK 的 **cacerts** 金鑰存放區包含正確 CA 憑證的詳細資訊，請參閱[新增憑證至 Java CA 憑證存放區](../java-add-certificate-ca-store)。

[如何從 Azure 中的 Java 應用程式使用 Twilio 撥打電話](../partner-twilio-java-phone-call-example)中提供使用 Java 版 Twilio 用戶端程式庫的詳細指示。

設定您的應用程式以使用 Twilio 程式庫
------------------------------------

在您的程式碼中，您可以針對於要在應用程式中使用的 Twilio 封裝或類別，在其原始程式檔的頂端加入 **import** 陳述式。

對於 Java 原始程式檔：

    import com.twilio.*;
    import com.twilio.sdk.*;
    import com.twilio.sdk.resource.factory.*;
    import com.twilio.sdk.resource.instance.*;

對於 Java 伺服器頁面 (JSP) 原始程式檔：

    import="com.twilio.*"
    import="com.twilio.sdk.*"
    import="com.twilio.sdk.resource.factory.*"
    import="com.twilio.sdk.resource.instance.*"

端視要使用的 Twilio 封裝或類別而定，您的 **import** 陳述式可能不同。

作法：撥出電話
--------------

以下顯示如何使用 **CallFactory** 類別撥出電話。這段程式碼也使用 Twilio 提供的網站傳回 Twilio 標記語言 (TwiML) 回應。將 **From** 和 **To** 電話號碼取代為您的值，並且先確定您已驗證 Twilio 帳戶的 **From** 電話號碼再執行程式碼。

    // Use your account SID and authentication token instead
    // of the placeholders shown here.
    String accountSID = "your_twilio_account";
    String authToken = "your_twilio_authentication_token";

    // Create an instance of the Twilio client.
    TwilioRestClient client;
    client = new TwilioRestClient(accountSID, authToken);

    // Retrieve the account, used later to create an instance of the CallFactory.
    Account account = client.getAccount();

    // Use the Twilio-provided site for the TwiML response.
    String Url="http://twimlets.com/message";
    Url = Url + "
    Message%5B0%5D=Hello%20World";

    // Place the call From, To and URL values into a hash map. 
    HashMap<String, String> params = new HashMap<String, String>();
    params.put("From", "NNNNNNNNNN"); // Use your own value for the second parameter.
    params.put("To", "NNNNNNNNNN");   // Use your own value for the second parameter.
    params.put("Url", Url);

    // Create an instance of the CallFactory class.
    CallFactory callFactory = account.getCallFactory();

    // Make the call.
    Call call = callFactory.create(params);

如需 **CallFactory.create** 方法中傳遞之參數的詳細資訊，請參閱 <http://www.twilio.com/docs/api/rest/making-calls> (英文)。

如上所述，這段程式碼使用 Twilio 提供的網站傳回 TwiML 回應。您可以改用自己的網站提供 TwiML 回應；如需詳細資訊，請參閱[如何在 Azure 上的 Java 應用程式中提供 TwiML 回應](#howto_provide_twiml_responses)。

作法：傳送 SMS 訊息
-------------------

以下顯示如何使用 **SmsFactory** 類別傳送 SMS 訊息。**From** 號碼 **4155992671** 是 Twilio 提供來傳送 SMS 訊息的試用帳戶。執行程式碼之前，您 Twilio 帳戶的 **To** 號碼必須先通過驗證。

    // Use your account SID and authentication token instead
    // of the placeholders shown here.
    String accountSID = "your_twilio_account";
    String authToken = "your_twilio_authentication_token";

    // Create an instance of the Twilio client.
    TwilioRestClient client;
    client = new TwilioRestClient(accountSID, authToken);

    // Retrieve the account, used later to create an instance of the SmsFactory.
    Account account = client.getAccount();

    // Send an SMS message.
    // Place the call From, To and Body values into a hash map. 
    HashMap<String, String> smsParams = new HashMap<String, String>();
    smsParams.put("From", "4155992671"); // The second parameter is a phone number provided
    // by Twilio for trial accounts.
    smsParams.put("To", "NNNNNNNNNN");   // Use your own value for the second parameter.
    smsParams.put("Body", "This is my SMS message.");

    // Create an instance of the SmsFactory class.
    SmsFactory smsFactory = account.getSmsFactory();

    // Send the message.
    Sms sms = smsFactory.create(smsParams);

如需 **SmsFactory.create** 方法中傳遞之參數的詳細資訊，請參閱 <http://www.twilio.com/docs/api/rest/sending-sms> (英文)。

作法：從自己的網站提供 TwiML 回應
---------------------------------

當您的應用程式呼叫 Twilio API 時 (例如透過 **CallFactory.create** 方法)，Twilio 將傳送您的要求到應該傳送 TwiML 回應的 URL。以上範例使用 Twilio 提供的 URL <http://twimlets.com/message>。(雖然 TwiML 是針對供 Web 服務使用而設計，但您可以在瀏覽器中檢視 TwiML。例如，按一下 <http://twimlets.com/message> 可查看空白 **&lt;Response\>** 元素，又例如，按一下 <http://twimlets.com/message?Message%5B0%5D=Hello%20World> 可查看包含 **&lt;Say\>** 元素的 **&lt;Response\>** 元素。)

除了使用 Twilio 提供的 URL 之外，您也可以自行建立會傳回 HTTP 回應的網站 URL。您可以使用任何語言建立會傳回 HTTP 回應的網站；本主題假設您將該 URL 裝載在 JSP 頁面中。

下列 JSP 頁面將引起 TwiML 在通話中說出 **Hello World** 作為回應。

    <%@ page contentType="text/xml" %>
    <Response> 
        <Say>Hello World</Say>
    </Response>

下列 JSP 頁面將引起 TwiML 有以下回應：說出一些文字、停頓數次，然後說出 Twilio API 版本和 Azure 角色名稱資訊。

    <%@ page contentType="text/xml" %>
    <Response> 
        <Say>Hello from Azure</Say>
        <Pause></Pause>
        <Say>The Twilio API version is <%= request.getParameter("ApiVersion") %>.</Say>
        <Say>The Azure role name is <%= System.getenv("RoleName") %>.</Say>
        <Pause></Pause>
        <Say>Good bye.</Say>
    </Response>

**ApiVersion** 參數會出現在 Twilio 語音要求 (而非 SMS 要求) 中。若要查看 Twilio 語音和 SMS 要求的可用要求參數，請分別參閱 &lt;https://www.twilio.com/docs/api/twiml/twilio\_request\> (英文) 及 &lt;https://www.twilio.com/docs/api/twiml/sms/twilio\_request\> (英文)。**RoleName** 環境參數會隨附在 Azure 部署中。(如果您要新增自訂環境參數，以便可以從 **System.getenv** 選擇這些參數，請參閱[其他角色組態設定](http://msdn.microsoft.com/en-us/library/windowsazure/hh690945.aspx)的環境變數一節。)

設定 JSP 頁面來提供 TwiML 回應之後，請使用 JSP 頁面的 URL 作為傳遞到 **CallFactory.create** 方法的 URL。例如，如果將名稱為 MyTwiML 的 Web 應用程式部署到 Azure 代管的服務，而且 JSP 頁面的名稱是 mytwiml.jsp，則可以將 URL 傳遞到 **CallFactory.create**，如下所示：

    // Place the call From, To and URL values into a hash map. 
    HashMap<String, String> params = new HashMap<String, String>();
    params.put("From", "NNNNNNNNNN");
    params.put("To", "NNNNNNNNNN");
    params.put("Url", "http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.jsp");

    CallFactory callFactory = account.getCallFactory();
    Call call = callFactory.create(params);

另一個選項是透過 **com.twilio.sdk.verbs** 封裝中的 **TwiMLResponse** 類別進行 TwiML 回應。

如需關於透過 Java 在 Azure 中使用 Twilio 的詳細資訊，請參閱[如何從 Azure 中的 Java 應用程式使用 Twilio 撥打電話](../partner-twilio-java-phone-call-example)。

作法：使用其他 Twilio 服務
--------------------------

除了此處所示的範例以外，Twilio 還提供網頁式 API，方便您從 Azure 應用程式中充份利用其他 Twilio 功能。如需完整詳細資料，請參閱[Twilio API 文件](http://www.twilio.com/api)。

後續步驟
--------

了解基本的 Twilio 服務之後，請參考下列連結以取得更多資訊：

-   [Twilio 安全性方針](http://www.twilio.com/docs/security)
-   [Twilio 作法與範例程式碼](http://www.twilio.com/docs/howto)
-   [Twilio 快速入門教學課程](http://www.twilio.com/docs/quickstart)
-   [GitHub 上的 Twilio](https://github.com/twilio)
-   [洽詢 Twilio 支援](http://www.twilio.com/help/contact)

