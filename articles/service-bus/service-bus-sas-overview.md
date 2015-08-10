<properties
   pageTitle="共用存取簽章概觀"
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
   ms.workload="tbd"
   ms.date="07/24/2015"
   ms.author="darosa"/>

# 共用存取簽章

*共用存取簽章* (SAS) 是服務匯流排的主要安全性機制，括事件中樞、代理傳訊 (佇列和主題) 和轉送傳訊。這篇文章討論共用存取簽章、其運作方式以及如何以平台無關的方式使用它們。

## SAS 的概觀

共用存取簽章是以 SHA-256 安全雜湊或 URI 為基礎的驗證機制。SAS 是所有服務匯流排服務使用的非常強大的機制。在實際使用中，SAS 有兩個元件：*共用存取原則*和*共用存取簽章* (通常稱為*權杖*)。

您可以在[使用服務匯流排的共用存取簽章驗證](https://msdn.microsoft.com/library/azure/dn170477.aspx)中找到共用存取簽章與服務匯流排的更詳細資訊。

## 共用的存取原則

對 SAS 應了解的一項重點是，一切都從原則開始。針對每個原則，您會決定三段資訊：**名稱**、**範圍**和**權限**。**名稱**只是該範圍內的唯一名稱。範圍也很簡單：它是發生問題之資源的 URI。針對服務匯流排命名空間，範圍是完整的網域名稱 (FQDN)，例如 **`https://<yournamespace>.servicebus.windows.net/`**。

原則的可用權限大部分都易於理解：

  + 傳送
  + 接聽
  + 管理

建立原則之後，它會獲指派*主索引鍵*和*次要索引鍵*。這些是密碼編譯增強式金鑰。不會遺失或洩漏它們 - 它們永遠都可在入口網站取得。您可以使用其中一個產生的金鑰，以及您可以隨時重新產生它們。不過，如果您重新產生或變更原則中的主索引鍵，從其建立的任何共用存取簽章將會失效。

建立服務匯流排命名空間時，將會自動為整個命名空間建立稱為 **RootManageSharedAccessKey** 的原則，而且此原則會具備所有權限。您未以 **root** 登入，所以除非有適合的理由，請勿使用此原則。您可以在 Azure 管理入口網站命名空間的 [**設定**] 索引標籤中建立額外的原則。請務必注意服務匯流排中單一樹狀目錄的層級 (命名空間、佇列、事件中樞等) 只能有最多 12 個附加至它的原則。

## 共用存取簽章 (權杖)

原則本身不是服務匯流排的存取權杖。它是來自使用主要或次要索引鍵產生的存取權杖的物件。權杖是以下列格式仔細編寫字串而產生：

```
SharedAccessSignature sig=<signature-string>&se=<expiry>&skn=<keyName>&sr=<URL-encoded-resourceURI>
```

其中，`signature-string` 是權杖範圍的 SHA-256 雜湊 (如前一節中所述的\*\*範圍\*\*) 並附加 CRLF 與到期時間 (自紀元起以秒為單位：1970 年 1 月 1 日 `00:00:00 UTC`)。

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

## 使用共用存取簽章
 
既然知道如何為服務匯流排中的任何實體建立共用存取簽章，您可準備執行 HTTP POST：

```
POST https://<yournamespace>.servicebus.windows.net/<yourentity>/messages
Content-Type: application/json
Authorization: SharedAccessSignature sr=https%3A%2F%2F<yournamespace>.servicebus.windows.net%2F<yourentity>&sig=<yoursignature from code above>&se=1438205742&skn=KeyName
ContentType: application/atom+xml;type=entry;charset=utf-8
``` 
	
請記住，這適用於所有項目。您可以為佇列、主題、訂用帳戶、事件中樞或轉送建立 SAS。如果您對事件中樞使用每一發行者身分識別，您只需附加 `/publishers/< publisherid>`。

如果您提供寄件者或用戶端 SAS 權杖，他們不會直接有金鑰，並且他們無法回復雜湊來取得它。因此，您可以控制他們可以存取的項目，以及時間長度。要記住的重點是，如果您變更原則中的主索引鍵，從其建立的任何共用存取簽章將會失效。

## 後續步驟

如需有關如何使用這些 SAS 權杖的詳細資訊，請參閱[服務匯流排 REST API 參考](https://msdn.microsoft.com/library/azure/hh780717.aspx)。

如需有關 SAS 的詳細資訊，請參閱 MSDN 上的[服務匯流排驗證](https://msdn.microsoft.com/library/azure/dn155925.aspx)節點。

<!---HONumber=July15_HO5-->