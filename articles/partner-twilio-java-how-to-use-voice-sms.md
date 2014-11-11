<properties linkid="develop-java-how-to-twilio-sms-service" urlDisplayName="Twilio Voice/SMS Service" pageTitle="How to Use Twilio for Voice and SMS (Java) - Azure" metaKeywords="Twilio, Twilio API, phone calls, SMS message, TwiML responses, Azure Twilio Java" description="Learn how to make a phone call and send a SMS message with the Twilio API service on Azure. Code samples written in Java." metaCanonical="" services="" videoId="" scriptId="" documentationCenter="Java" title="How to Use Twilio for Voice and SMS Capabilities in Java" authors="MicrosoftHelp@twilio.com; robmcm" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="MicrosoftHelp@twilio.com; robmcm" />

# 如何在 Java 中透過 Twilio 使用語音和簡訊功能

本指南示範如何在 Azure 上透過 Twilio API 服務執行常見的程式設計工作。涵蓋的案例包括打電話和傳送簡訊 (SMS)。如需有關如何在應用程式中使用 Twilio 語音和 SMS 的詳細資訊，請參閱[後續步驟][後續步驟]一節。

## 目錄

-   [什麼是 Twilio？][什麼是 Twilio？]
-   [Twilio 定價][Twilio 定價]
-   [概念][概念]
-   [建立 Twilio 帳戶][建立 Twilio 帳戶]
-   [驗證電話號碼][驗證電話號碼]
-   [建立 Java 應用程式][建立 Java 應用程式]
-   [設定應用程式以使用 Twilio 程式庫][設定應用程式以使用 Twilio 程式庫]
-   [作法：撥出電話][作法：撥出電話]
-   [作法：傳送簡訊][作法：傳送簡訊]
-   [作法：從您自己的網站提供 TwiML 回應][作法：從您自己的網站提供 TwiML 回應]
-   [作法：使用其他 Twilio 服務][作法：使用其他 Twilio 服務]
-   [後續步驟][後續步驟]

## <span id="WhatIs"></span></a>什麼是 Twilio？

Twilio 是一種電話語音 Web 服務 API，能夠讓您使用現有的 Web 語言和技術建立語音和 SMS 應用程式。Twilio 算是協力廠商服務 (並非 Azure 功能，也並非 Microsoft 產品)。

**Twilio 語音**可讓應用程式撥打和接聽電話。**Twilio SMS** 可以讓您的應用程式撰寫和接收 SMS 訊息。**Twilio Client** 可以讓您的應用程式在現有網際網路連線 (包括行動連線) 中啟用語音通訊。

## <span id="Pricing"></span></a>Twilio 定價和特別優惠

[Twilio 定價][1] (英文) 提供 Twilio 的定價資訊。Azure 客戶享有[特別優惠][特別優惠]：免費 1000 則文字簡訊或接聽 1000 分鐘電話。若要註冊獲得這項優惠或取得詳細資訊，請造訪 [][特別優惠]<http://ahoy.twilio.com/azure></a> (英文)。

## <span id="Concepts"></span></a> 概念

Twilio API 是一套為應用程式提供語音和簡訊功能的 RESTful API。用戶端程式庫有多種語言版本，相關清單請參閱＜[Twilio API 程式庫][Twilio API 程式庫]＞(英文)。

Twilio API 的兩大重點是 Twilio 動詞和 Twilio 標記語言 (TwiML)。

### <span id="Verbs"></span></a>Twilio 動詞

API 採用 Twilio 動詞。例如，**\<Say\>** 動詞指示 Twilio 在通話中用語音傳遞訊息。

以下是 Twilio 動詞清單。

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

如需 Twilio 動詞、屬性和 TwiML 的詳細資訊，請參閱 [TwiML][TwiML]。如需 Twilio API 的詳細資訊，請參閱 [Twilio API][Twilio API]。

## <span id="CreateAccount"></span></a>建立 Twilio 帳戶

準備取得 Twilio 帳戶時，請至[試用 Twilio][試用 Twilio] 註冊。您可以先使用免費帳戶，稍後再升級帳戶。

註冊 Twilio 帳戶時，您會收到帳戶識別碼和驗證權杖。兩者皆為呼叫 Twilio API 所需。為了防止未經授權存取您的帳戶，您妥善保管驗證權杖。在 [Twilio 帳戶頁面][Twilio 帳戶頁面] (英文) 的 **ACCOUNT SID** 和 **AUTH TOKEN** 欄位中，分別可檢視您的帳戶識別碼和驗證權杖。

## <span id="VerifyPhoneNumbers"></span></a>驗證電話號碼

各種電話號碼都必須經過您在 Twilio 的帳戶來驗證。例如，如果想要向外撥打電話，則必須向 Twilio 驗證此電話號碼為外撥來電者識別碼。同樣地，如果想要以某個電話號碼來接收簡訊，則必須向 Twilio 驗證此受話電話號碼。如需如何驗證電話號碼的詳細資訊，請參閱[管理電話號碼][管理電話號碼]。以下某些程式碼依賴需要向 Twilio 驗證的電話號碼。

除了在應用程式中使用現有的電話號碼，您也可以購買 Twilio 電話號碼。如需有關購買 Twilio 電話號碼的詳細資訊，請參閱＜[Twilio 電話號碼說明][Twilio 電話號碼說明]＞(英文)。

## <span id="create_app"></span></a>建立 Java 應用程式

1.  取得 Twilio JAR 並將它加到您的 Java 組建路徑和 WAR 部署組件。在 [][]<https://github.com/twilio/twilio-java></a> 上，您可以下載 GitHub 來源及建立自己的 JAR，或下載預先建置的 JAR (可能有相依性)。
2.  確定 JDK 的 **cacerts** 金鑰存放區包含 MD5 指紋為 67:CB:9D:C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 (序號為 35:DE:F4:CF 且 SHA1 指紋為 D2:32:09:AD:23:D3:14:23:21:74:E4:0D:7F:9D:62:13:97:86:63:3A) 的 Equifax 安全憑證授權單位憑證。這是 [][2]<https://api.twilio.com></a> 服務的憑證授權單位 (CA) 憑證，會在您使用 Twilio API 時受到呼叫。如需關於確定 JDK 的 **cacerts** 金鑰存放區包含正確 CA 憑證的詳細資訊，請參閱[新增憑證至 Java CA 憑證存放區][新增憑證至 Java CA 憑證存放區]。

[如何從 Azure 中的 Java 應用程式使用 Twilio 撥打電話][如何從 Azure 中的 Java 應用程式使用 Twilio 撥打電話]中提供使用 Java 版 Twilio 用戶端程式庫的詳細指示。

## <span id="configure_app"></span></a>設定應用程式以使用 Twilio 程式庫

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

## <span id="howto_make_call"></span></a>作法：撥出電話

以下顯示如何使用 **CallFactory** 類別撥出電話。此程式碼也使用 Twilio 提供的網站來傳回 Twilio 標記語言 (TwiML) 回應。請將 **From** 和 **To** 電話號碼換成您的值，在執行程式碼之前，請記得先驗證 Twilio 帳戶的 **From** 電話號碼。

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
    Url = Url + "?Message%5B0%5D=Hello%20World";

    // Place the call From, To and URL values into a hash map. 
    HashMap<String, String> params = new HashMap<String, String>();
    params.put("From", "NNNNNNNNNN"); // Use your own value for the second parameter.
    params.put("To", "NNNNNNNNNN");   // Use your own value for the second parameter.
    params.put("Url", Url);

    // Create an instance of the CallFactory class.
    CallFactory callFactory = account.getCallFactory();

    // Make the call.
    Call call = callFactory.create(params);

如需 **CallFactory.create** 方法中傳遞之參數的詳細資訊，請參閱 [][3]<http://www.twilio.com/docs/api/rest/making-calls></a> (英文)。

如前所述，此程式碼使用 Twilio 提供的網站來傳回 TwiML 回應。您可以改用自己的網站提供 TwiML 回應；如需詳細資訊，請參閱[如何在 Azure 上的 Java 應用程式中提供 TwiML 回應][作法：從您自己的網站提供 TwiML 回應]。

## <span id="howto_send_sms"></span></a>作法：傳送簡訊

以下顯示如何使用 **SmsFactory** 類別傳送 SMS 訊息。**From** 號碼 **4155992671** 是 Twilio 提供來傳送 SMS 訊息的試用帳戶。執行程式碼之前，必須驗證您 Twilio 帳戶的 **To** 號碼。

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

如需 **SmsFactory.create** 方法中傳遞之參數的詳細資訊，請參閱 [][4]<http://www.twilio.com/docs/api/rest/sending-sms></a> (英文)。

## <span id="howto_provide_twiml_responses"></span></a>作法：從您自己的網站提供 TwiML 回應

當您的應用程式呼叫 Twilio API 時 (例如透過 **CallFactory.create** 方法)，Twilio 將傳送您的要求到應該傳送 TwiML 回應的 URL。前述範例使用 Twilio 提供的 URL [][5]<http://twimlets.com/message></a>。(雖然 TwiML 是針對供 Web 服務使用而設計，但您可以在瀏覽器中檢視 TwiML。例如，按一下 [][5]<http://twimlets.com/message></a> 可查看空白 **\<Response\>** 元素，又例如，按一下 [][6]<http://twimlets.com/message?Message%5B0%5D=Hello%20World></a> 可查看包含 **\<Say\>** 元素的 **\<Response\>** 元素。)

除了依賴 Twilio 提供的 URL，您也可以建立自己的 URL 網站來傳回 HTTP 回應。您可以使用任何語言建立會傳回 HTTP 回應的網站；本主題假設您將該 URL 裝載在 JSP 頁面中。

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

**ApiVersion** 參數會出現在 Twilio 語音要求 (而非 SMS 要求) 中。若要查看 Twilio 語音和 SMS 要求的可用要求參數，請分別參閱 <https://www.twilio.com/docs/api/twiml/twilio_request> (英文) 及 <https://www.twilio.com/docs/api/twiml/sms/twilio_request> (英文)。**RoleName** 環境參數會隨附在 Azure 部署中。(如果您要新增自訂環境參數，以便可以從 **System.getenv** 選擇這些參數，請參閱[其他角色組態設定][其他角色組態設定]的環境變數一節。)

設定 JSP 頁面來提供 TwiML 回應之後，請使用 JSP 頁面的 URL 作為傳遞到 **CallFactory.create** 方法的 URL。例如，如果將名稱為 MyTwiML 的 Web 應用程式部署到 Azure 代管的服務，而且 JSP 頁面的名稱是 mytwiml.jsp，則可以將 URL 傳遞到 **CallFactory.create**，如下所示：

    // Place the call From, To and URL values into a hash map. 
    HashMap<String, String> params = new HashMap<String, String>();
    params.put("From", "NNNNNNNNNN");
    params.put("To", "NNNNNNNNNN");
    params.put("Url", "http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.jsp");

    CallFactory callFactory = account.getCallFactory();
    Call call = callFactory.create(params);

另一個選項是透過 **com.twilio.sdk.verbs** 封裝中的 **TwiMLResponse** 類別進行 TwiML 回應。

如需關於透過 Java 在 Azure 中使用 Twilio 的詳細資訊，請參閱[如何從 Azure 中的 Java 應用程式使用 Twilio 撥打電話][如何從 Azure 中的 Java 應用程式使用 Twilio 撥打電話]。

## <span id="AdditionalServices"></span></a>作法：使用其他 Twilio 服務

除了此處所示的範例以外，Twilio 還提供網頁式 API，方便您從 Azure 應用程式中充份利用其他 Twilio 功能。如需完整詳細資料，請參閱[Twilio API 文件][Twilio API]。

## <span id="NextSteps"></span></a>後續步驟

了解基本的 Twilio 服務之後，請參考下列連結以取得更多資訊：

-   [Twilio 安全性方針][Twilio 安全性方針]
-   [Twilio 作法與範例程式碼][Twilio 作法與範例程式碼]
-   [Twilio 快速入門教學課程][Twilio 快速入門教學課程]
-   [GitHub 上的 Twilio][GitHub 上的 Twilio]
-   [洽詢 Twilio 支援][洽詢 Twilio 支援]

  [後續步驟]: #NextSteps
  [什麼是 Twilio？]: #WhatIs
  [Twilio 定價]: #Pricing
  [概念]: #Concepts
  [建立 Twilio 帳戶]: #CreateAccount
  [驗證電話號碼]: #VerifyPhoneNumbers
  [建立 Java 應用程式]: #create_app
  [設定應用程式以使用 Twilio 程式庫]: #configure_app
  [作法：撥出電話]: #howto_make_call
  [作法：傳送簡訊]: #howto_send_sms
  [作法：從您自己的網站提供 TwiML 回應]: #howto_provide_twiml_responses
  [作法：使用其他 Twilio 服務]: #AdditionalServices
  [1]: http://www.twilio.com/pricing
  [特別優惠]: http://ahoy.twilio.com/azure
  [Twilio API 程式庫]: https://www.twilio.com/docs/libraries
  [TwiML]: http://www.twilio.com/docs/api/twiml
  [Twilio API]: http://www.twilio.com/api
  [試用 Twilio]: https://www.twilio.com/try-twilio
  [Twilio 帳戶頁面]: https://www.twilio.com/user/account
  [管理電話號碼]: https://www.twilio.com/user/account/phone-numbers/verified#
  [Twilio 電話號碼說明]: https://www.twilio.com/help/faq/phone-numbers
  []: https://github.com/twilio/twilio-java
  [2]: https://api.twilio.com
  [新增憑證至 Java CA 憑證存放區]: ../java-add-certificate-ca-store
  [如何從 Azure 中的 Java 應用程式使用 Twilio 撥打電話]: ../partner-twilio-java-phone-call-example
  [3]: http://www.twilio.com/docs/api/rest/making-calls
  [4]: http://www.twilio.com/docs/api/rest/sending-sms
  [5]: http://twimlets.com/message
  [6]: http://twimlets.com/message?Message%5B0%5D=Hello%20World
  [其他角色組態設定]: http://msdn.microsoft.com/zh-TW/library/windowsazure/hh690945.aspx
  [Twilio 安全性方針]: http://www.twilio.com/docs/security
  [Twilio 作法與範例程式碼]: http://www.twilio.com/docs/howto
  [Twilio 快速入門教學課程]: http://www.twilio.com/docs/quickstart
  [GitHub 上的 Twilio]: https://github.com/twilio
  [洽詢 Twilio 支援]: http://www.twilio.com/help/contact
