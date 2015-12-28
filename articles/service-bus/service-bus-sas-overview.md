<properties
   pageTitle="共用存取簽章概觀 | Microsoft Azure"
   description="共用存取簽章為何、其如何運作，以及如何從 Node、PHP 和 C# 中使用它們。"
   services="service-bus,event-hubs"
   documentationCenter="na"
   authors="djrosanova"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-bus"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/09/2015"
   ms.author="darosa"/>

# 共用存取簽章

*共用存取簽章* (SAS) 是服務匯流排的主要安全性機制，括事件中樞、代理傳訊 (佇列和主題) 和轉送傳訊。這篇文章討論共用存取簽章、其運作方式以及如何以平台無關的方式使用它們。

## SAS 的概觀

共用存取簽章是以 SHA-256 安全雜湊或 URI 為基礎的驗證機制。SAS 是所有服務匯流排服務使用的非常強大的機制。在實際使用中，SAS 有兩個元件：*共用存取原則*和*共用存取簽章* (通常稱為*權杖*)。

您可以在[使用服務匯流排的共用存取簽章驗證](service-bus-shared-access-signature-authentication.md)中，找到共用存取簽章與服務匯流排的更詳細資訊。

## 共用的存取原則

對 SAS 應了解的一項重點是，一切都從原則開始。針對每個原則，您會決定三段資訊：**名稱**、**範圍**和**權限**。**名稱**只是該範圍內的唯一名稱。範圍也很簡單：它是發生問題之資源的 URI。針對服務匯流排命名空間，範圍是完整的網域名稱 (FQDN)，例如 **`https://<yournamespace>.servicebus.windows.net/`**。

原則的可用權限大部分都易於理解：

  + 傳送
  + 接聽
  + 管理

建立原則之後，它會獲指派*主索引鍵*和*次要索引鍵*。這些是密碼編譯增強式金鑰。不會遺失或洩漏它們 - 它們永遠都可在 [Azure 傳統入口網站][]取得。您可以使用其中一個產生的金鑰，以及您可以隨時重新產生它們。不過，如果您重新產生或變更原則中的主索引鍵，從其建立的任何共用存取簽章將會失效。

建立服務匯流排命名空間時，將會自動為整個命名空間建立稱為 **RootManageSharedAccessKey** 的原則，而且此原則會具備所有權限。您未以 **root** 登入，所以除非有適合的理由，請勿使用此原則。您可以在入口網站命名空間的 [設定] 索引標籤中建立額外的原則。請務必注意服務匯流排中單一樹狀目錄的層級 (命名空間、佇列、事件中樞等) 只能有最多 12 個附加至它的原則。

## 共用存取簽章 (權杖)

原則本身不是服務匯流排的存取權杖。它是來自使用主要或次要索引鍵產生的存取權杖的物件。權杖是以下列格式仔細編寫字串而產生：

```
SharedAccessSignature sig=<signature-string>&se=<expiry>&skn=<keyName>&sr=<URL-encoded-resourceURI>
```

其中，`signature-string` 是權杖範圍的 SHA-256 雜湊 (如前一節中所述的**範圍**) 並附加 CRLF 與到期時間 (自紀元起以秒為單位：1970 年 1 月 1 日 `00:00:00 UTC`)。

雜湊看起來類似下列虛擬程式碼，並傳回 32 個位元組。

```
SHA-256('https://<yournamespace>.servicebus.windows.net/'+'\n'+ 1438205742)
```

非雜湊值位在 **SharedAccessSignature** 字串中，如此收件者可以使用相同的參數計算雜湊，以確保它會傳回相同的結果。URI 會指定範圍，而金鑰名稱會識別要用來計算雜湊的原則。從安全性的觀點而言，這很重要。如果簽章與收件者 (服務匯流排) 所計算的不符，則會拒絕存取。此時我們可以確定寄件者可存取金鑰，並且應該被授與原則中指定的權限。

## 從原則產生簽章

您在程式碼中如何實際進行此動作？ 讓我們來看一下其中的一些。

### NodeJS

```
function createSharedAccessToken(uri, saName, saKey) { 
    if (!uri || !saName || !saKey) { 
            throw "Missing required parameter"; 
        } 
    var encoded = encodeURIComponent(uri); 
    var now = new Date(); 
    var week = 60*60*24*7;
    var ttl = Math.round(now.getTime() / 1000) + week;
    var signature = encoded + '\n' + ttl; 
    var signatureUTF8 = utf8.encode(signature); 
    var hash = crypto.createHmac('sha256', saKey).update(signatureUTF8).digest('base64'); 
    return 'SharedAccessSignature sr=' + encoded + '&sig=' +  
        encodeURIComponent(hash) + '&se=' + ttl + '&skn=' + saName; 
}
``` 

### Java

```
private static String GetSASToken(String resourceUri, String keyName, String key)
  {
      long epoch = System.currentTimeMillis()/1000L;
      int week = 60*60*24*7;
      String expiry = Long.toString(epoch + week);

      String sasToken = null;
      try {
          String stringToSign = URLEncoder.encode(resourceUri, "UTF-8") + "\n" + expiry;
          String signature = getHMAC256(key, stringToSign);
          sasToken = "SharedAccessSignature sr=" + URLEncoder.encode(resourceUri, "UTF-8") +"&sig=" +
                  URLEncoder.encode(signature, "UTF-8") + "&se=" + expiry + "&skn=" + keyName;
      } catch (UnsupportedEncodingException e) {

          e.printStackTrace();
      }

      return sasToken;
  }


public static String getHMAC256(String key, String input) {
    Mac sha256_HMAC = null;
    String hash = null;
    try {
        sha256_HMAC = Mac.getInstance("HmacSHA256");
        SecretKeySpec secret_key = new SecretKeySpec(key.getBytes(), "HmacSHA256");
        sha256_HMAC.init(secret_key);
        Encoder encoder = Base64.getEncoder();

        hash = new String(encoder.encode(sha256_HMAC.doFinal(input.getBytes("UTF-8"))));

    } catch (InvalidKeyException e) {
        e.printStackTrace();
    } catch (NoSuchAlgorithmException e) {
        e.printStackTrace();
   } catch (IllegalStateException e) {
        e.printStackTrace();
    } catch (UnsupportedEncodingException e) {
        e.printStackTrace();
    }

    return hash;
}
```

### PHP

```
function generateSasToken($uri, $sasKeyName, $sasKeyValue) 
{ 
$targetUri = strtolower(rawurlencode(strtolower($uri))); 
$expires = time(); 	
$expiresInMins = 60; 
$week = 60*60*24*7;
$expires = $expires + $week; 
$toSign = $targetUri . "\n" . $expires; 
$signature = rawurlencode(base64_encode(hash_hmac('sha256', 			
 $toSign, $sasKeyValue, TRUE))); 

$token = "SharedAccessSignature sr=" . $targetUri . "&sig=" . $signature . "&se=" . $expires . 		"&skn=" . $sasKeyName; 
return $token; 
}
```
 
### C&#35;

```
private static string createToken(string resourceUri, string keyName, string key)
{
    TimeSpan sinceEpoch = DateTime.UtcNow - new DateTime(1970, 1, 1);
    var week = 60 * 60 * 24 * 7;
    var expiry = Convert.ToString((int)sinceEpoch.TotalSeconds + week);
    string stringToSign = HttpUtility.UrlEncode(resourceUri) + "\n" + expiry;
    HMACSHA256 hmac = new HMACSHA256(Encoding.UTF8.GetBytes(key));
    var signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(stringToSign)));
    var sasToken = String.Format(CultureInfo.InvariantCulture, "SharedAccessSignature sr={0}&sig={1}&se={2}&skn={3}", HttpUtility.UrlEncode(resourceUri), HttpUtility.UrlEncode(signature), expiry, keyName);
    return sasToken;
}
```

## 使用共用存取簽章 (於 HTTP 層級)
 
既然知道如何為服務匯流排中的任何實體建立共用存取簽章，您可準備執行 HTTP POST：

```
POST https://<yournamespace>.servicebus.windows.net/<yourentity>/messages
Content-Type: application/json
Authorization: SharedAccessSignature sr=https%3A%2F%2F<yournamespace>.servicebus.windows.net%2F<yourentity>&sig=<yoursignature from code above>&se=1438205742&skn=KeyName
ContentType: application/atom+xml;type=entry;charset=utf-8
``` 
	
請記住，這適用於所有項目。您可以為佇列、主題、訂用帳戶、事件中樞或轉送建立 SAS。如果您對事件中樞使用每一發行者身分識別，您只需附加 `/publishers/< publisherid>`。

如果您提供寄件者或用戶端 SAS 權杖，他們不會直接有金鑰，並且他們無法回復雜湊來取得它。因此，您可以控制他們可以存取的項目，以及時間長度。要記住的重點是，如果您變更原則中的主索引鍵，從其建立的任何共用存取簽章將會失效。

## 使用共用存取簽章 (於 AMQP 層級)

在前一節中，說明了如何使用 SAS 權杖搭配 HTTP POST 要求傳送資料到服務匯流排。如您所了解，您可以使用 AMQP (進階訊息佇列通訊協定) 通訊協定來存取服務匯流排，AMQP 通訊協定在許多的案例中，都是基於效能考量而做為主要及慣用的通訊協定。使用 SAS 權杖搭配 AMQP 的用法在下列文章 [AMQP 宣告型安全性 1.0 版](https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc)中說明，這是自 2013 年開始的工作草稿，不過現在 Azure 已經提供良好的支援。

開始將資料傳送到服務匯流排之前，發行者需要在 AMQP 訊息內部將 SAS 權杖傳送至正確定義且名為 **"$cbs"** 的 AMQP 節點 (您可以將它視為像是一個由服務使用的「特別」佇列，用來取得並驗證所有的 SAS 權杖)。發行者必須在 AMQP 訊息中指定 **"ReplyTo"** 欄位；這是服務將以權杖驗證結果 (發行者與服務之間的簡單要求/回覆模式) 回覆發行者的節點所在。此回覆節點是「動態」建立，如 AMQP 1.0 規格中所述的「動態建立遠端節點」。檢查 SAS 權杖有效之後，發行者可以繼續並開始將資料傳送至服務。

下列步驟將說明如果您無法在 C&#35; 中使用官方的服務匯流排 SDK (例如在 WinRT、.Net Compact Framework、.Net Micro Framework 和 Mono 中) 進行開發，應如何有效地使用 [AMQP.Net Lite](http://amqpnetlite.codeplex.com) 程式庫搭配 AMQP 通訊協定傳送 SAS 權杖。當然，此函式庫對於了解宣告型安全性如何在 AMQP 層級運作而言非常有用，如同您了解他如何在 HTTP 層級運作一樣 (使用 HTTP POST 要求以及在標頭 "Authorization" 內部傳送的 SAS 權杖)。不過，不用擔心！ 如果您不需要深入了解 AMQP，您可以搭配 .Net Framework 應用程式使用正式服務匯流排 SDK，這也會為您和所有其他平台的 [Azure SB Lite](http://azuresblite.codeplex.com) 程式庫 (請參閱前述) 做到這點。

### C&#35;

```
/// <summary>
/// Send Claim Based Security (CBS) token
/// </summary>
/// <param name="shareAccessSignature">Shared access signature (token) to send</param>
private bool PutCbsToken(Connection connection, string sasToken)
{
    bool result = true;
    Session session = new Session(connection);

    string cbsClientAddress = "cbs-client-reply-to";
    var cbsSender = new SenderLink(session, "cbs-sender", "$cbs");
    var cbsReceiver = new ReceiverLink(session, cbsClientAddress, "$cbs");

    // construct the put-token message
    var request = new Message(sasToken);
    request.Properties = new Properties();
    request.Properties.MessageId = Guid.NewGuid().ToString();
    request.Properties.ReplyTo = cbsClientAddress;
    request.ApplicationProperties = new ApplicationProperties();
    request.ApplicationProperties["operation"] = "put-token";
    request.ApplicationProperties["type"] = "servicebus.windows.net:sastoken";
    request.ApplicationProperties["name"] = Fx.Format("amqp://{0}/{1}", sbNamespace, entity);
    cbsSender.Send(request);

    // receive the response
    var response = cbsReceiver.Receive();
    if (response == null || response.Properties == null || response.ApplicationProperties == null)
    {
        result = false;
    }
    else
    {
        int statusCode = (int)response.ApplicationProperties["status-code"];
        if (statusCode != (int)HttpStatusCode.Accepted && statusCode != (int)HttpStatusCode.OK)
        {
            result = false;
        }
    }

    // the sender/receiver may be kept open for refreshing tokens
    cbsSender.Close();
    cbsReceiver.Close();
    session.Close();

    return result;
}
```

上述 *PutCbsToken()* 方法會接收代表服務之 TCP 連線的*連線* (AMQP .Net Lite 程式庫所提供的 AMQP Connection 類別執行個體) 以及要做為 SAS 權杖傳送的 *sasToken* 參數。注意：請務必以**設為 EXTERNAL 的 SASL 驗證機制** (而非當您不需要傳送 SAS 權杖時所使用之包含使用者名稱與密碼的預設 PLAIN) 建立連線。

接下來，發行者會建立兩個 AMQP 連結來傳送 SAS 權杖並接受來自服務的回覆 (權杖驗證結果)。

AMQP 訊息因為具有眾多屬性而有點複雜，且含有比簡單訊息更多的資訊。SAS 權杖會放在訊息內文中 (使用其建構函式)。**"ReplyTo"** 屬性設定為在接收者連結上接收驗證結果的節點名稱 (您可以將它變更為您想要的名稱，服務將會動態建立它)。最後三個 application/custom 屬性是由服務使用，以了解它必須執行什麼類型的作業。如 CBS 草稿規格所述，它們必須是**作業名稱** (像是 "put-token")、放置的**語彙基元類型** (像是 "servicebus.windows.net:sastoken")，最後則是套用權杖之**對象的 "name"** (完整實體)。

在寄件者連結上傳送 SAS 權杖之後，發行者需要在接收者連結上讀取回覆。回覆是包含名稱為 **"status-code"** 之應用程式屬性的簡單 AMQP 訊息，可用來包含做為 HTTP 狀態碼的相同值

## 後續步驟

如需有關如何使用這些 SAS 權杖的詳細資訊，請參閱[服務匯流排 REST API 參考](https://msdn.microsoft.com/library/azure/hh780717.aspx)。

如需有關服務匯流排驗證的詳細資訊，請參閱[服務匯流排驗證與授權](service-bus-authentication-and-authorization.md)。

[本部落格文章](http://developers.de/blogs/damir_dobric/archive/2013/10/17/how-to-create-shared-access-signature-for-service-bus.aspx)中有更多 C# 和 Java Script 的 SAS 範例。

[Azure 傳統入口網站]: http://manage.windowsazure.com

<!---HONumber=AcomDC_1217_2015-->