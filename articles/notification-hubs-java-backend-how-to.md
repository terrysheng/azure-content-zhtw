<properties linkid="notification-hubs-java-back-end-how-to" urlDisplayName="How to use Notification Hubs with Java" pageTitle="How to use Notification Hubs with Java" metaKeywords="" description="Learn how to use Azure Notification Hubs from a Java back-end." metaCanonical="" services="mobile-services,notification-hubs,push,java" documentationCenter="" title="How to use Notification Hubs with Java" authors="elioda" solutions="" manager="" editor="" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="java" ms.topic="article" ms.date="01/01/1900" ms.author="elioda" />

# 如何使用 Java/PHP 的通知中心

<div class="dev-center-tutorial-selector sublanding"> 
<a href="/zh-tw/documentation/articles/notification-hubs-java-backend-how-to/" title="Java" class="current">Java</a><a href="/zh-tw/documentation/articles/notification-hubs-php-backend-how-to/" title="PHP">PHP</a>
</div>

您可以使用通知中心 REST 介面，存取 Java/PHP/Ruby 後端的所有通知中心功能，如 MSDN 主題[通知中心 REST API][通知中心 REST API] 中所述。

在本主題中，我們將說明如何：

-   在 Java 中建置通知中心功能的 REST 用戶端；
-   依照 [開始使用教學課程][開始使用教學課程]，針對您所選的行動平台，在 Java 中實作後端部分。

## <a name="client-interface"></a>用戶端介面

主要用戶端介面可提供與 [.NET 通知中心 SDK][.NET 通知中心 SDK] 提供的相同方法，這可讓您直接轉譯本網站上目前所提供以及網際網路上社群所貢獻的所有教學課程和範例。

您可在 [Java REST 包裝函式範例][Java REST 包裝函式範例]中找到所有可用的程式碼。

例如，若要建立用戶端：

    new NotificationHub("connection string", "hubname");    

若要建立 iOS 註冊 (與 Windows、Android、Windows Phone 和 Kindle Fire 類似)：

    String id = hub.createRegistrationId();
    AppleRegistration reg = new AppleRegistration(id, DEVICETOKEN);
    reg.getTags().add("myTag");
    reg.getTags().add("myOtherTag");
    hub.upsertRegistration(reg);

若要傳送 iOS 原生通知：

    Notification n = Notification.createAppleNotifiation("APNS body");
    hub.sendNotification(n);

## <a name="implementation"></a>實作

請依照[開始使用教學課程][開始使用教學課程]進行到最後一節的實作後端 (如果您尚未進行此作業)。
另外，如果您要的話，您可以使用 [Java REST 包裝函式範例][Java REST 包裝函式範例]中的程式碼，直接進入[完成教學課程][完成教學課程]一節。

您可以在 [MSDN][MSDN] 上找到所有實作完整 REST 包裝函式的詳細資料。在本節中，我們將針對存取通知中心 REST 端點所需主要步驟的 Java 實作進行說明：

1.  解析連接字串
2.  產生授權權杖
3.  執行 HTTP 呼叫

在下列程式碼片段中，我們將使用下列元件：

-   [Apache HttpComponents][Apache HttpComponents]
-   [Apache Commons-Codec][Apache Commons-Codec]
-   [Apache Commons-Io][Apache Commons-Io]

### 解析連接字串

以下是實作其建構函式可解析連接字串之用戶端的主要類別：

    public class NotificationHub {

        private static final String APIVERSION = "?api-version=2013-10";
        private static final String CONTENT_LOCATION_HEADER = "Location";
        private String endpoint;
        private String hubPath;
        private String SasKeyName;
        private String SasKeyValue;

        private HttpClient httpClient;

        public NotificationHub(String connectionString, String hubPath) {
            this.httpClient = HttpClients.createDefault();
            this.hubPath = hubPath;

            String[] parts = connectionString.split(";");
            if (parts.length != 3)
                throw new RuntimeException("Error parsing connection string: "
                        + connectionString);

            for (int i = 0; i < parts.length; i++) {
                if (parts[i].startsWith("Endpoint")) {
                    this.endpoint = "https" + parts[i].substring(11);
                } else if (parts[i].startsWith("SharedAccessKeyName")) {
                    this.SasKeyName = parts[i].substring(20);
                } else if (parts[i].startsWith("SharedAccessKey")) {
                    this.SasKeyValue = parts[i].substring(16);
                }
            }
        }
    }

### 建立安全性權杖

您可以在[這裡][這裡]找到建立安全性權杖的詳細資料。
您必須將下列方法加入 **NotificationHub** 類別，才能根據目前要求的 URI 及擷取自連接字串的認證來建立權杖。

    private String generateSasToken(URI uri) {
        String targetUri;
        try {
            targetUri = URLEncoder
                    .encode(uri.toString().toLowerCase(), "UTF-8")
                    .toLowerCase();

            long expiresOnDate = System.currentTimeMillis();
            int expiresInMins = 60; // 1 hour
            expiresOnDate += expiresInMins * 60 * 1000;
            long expires = expiresOnDate / 1000;
            String toSign = targetUri + "\n" + expires;

            // Get an hmac_sha1 key from the raw key bytes
            byte[] keyBytes = SasKeyValue.getBytes("UTF-8");
            SecretKeySpec signingKey = new SecretKeySpec(keyBytes, "HmacSHA256");

            // Get an hmac_sha1 Mac instance and initialize with the signing key
            Mac mac = Mac.getInstance("HmacSHA256");
            mac.init(signingKey);

            // Compute the hmac on input data bytes
            byte[] rawHmac = mac.doFinal(toSign.getBytes("UTF-8"));

            // Convert raw bytes to Hex
            String signature = URLEncoder.encode(
                    Base64.encodeBase64String(rawHmac), "UTF-8");

            // construct authorization string
            String token = "SharedAccessSignature sr=" + targetUri + "&sig="
                    + signature + "&se=" + expires + "&skn=" + SasKeyName;
            return token;
        } catch (Exception e) {
            throw new RuntimeException(e);
        }
    }

### 傳送通知

首先，讓我們先定義呈現通知的類別。

    import java.util.HashMap;
    import java.util.Iterator;
    import java.util.Map;
    import org.apache.http.entity.ContentType;

    public class Notification {
        private Map<String, String> headers = new HashMap<String, String>();
        private String body;
        private ContentType contentType;

        public static Notification createWindowsNotification(String body) {
            Notification n = new Notification();
            n.body = body;
            n.headers.put("ServiceBusNotification-Format", "windows");

            if (body.contains("<toast>"))
                n.headers.put("X-WNS-Type", "wns/toast");
            if (body.contains("<tile>"))
                n.headers.put("X-WNS-Type", "wns/tile");
            if (body.contains("<badge>"))
                n.headers.put("X-WNS-Type", "wns/badge");
            if (body.startsWith("<")) {
                n.contentType = ContentType.APPLICATION_XML;
            }
            return n;
        }

        public static Notification createAppleNotifiation(String body) {
            Notification n = new Notification();
            n.body = body;
            n.contentType = ContentType.APPLICATION_JSON;
            n.headers.put("ServiceBusNotification-Format", "apple");
            return n;
        }

        public static Notification createGcmNotifiation(String body) {
            Notification n = new Notification();
            n.body = body;
            n.contentType = ContentType.APPLICATION_JSON;
            n.headers.put("ServiceBusNotification-Format", "gcm");
            return n;
        }

        public static Notification createAdmNotifiation(String body) {
            Notification n = new Notification();
            n.body = body;
            n.contentType = ContentType.APPLICATION_JSON;
            n.headers.put("ServiceBusNotification-Format", "adm");
            return n;
        }

        public static Notification createMpnsNotifiation(String body) {
            Notification n = new Notification();
            n.body = body;
            n.headers.put("ServiceBusNotification-Format", "windowsphone");

            if (body.contains("<wp:Toast>")) {
                n.headers.put("X-WindowsPhone-Target", "toast");
                n.headers.put("X-NotificationClass", "2");
            }
            if (body.contains("<wp:Tile>")) {
                n.headers.put("X-WindowsPhone-Target", "tile");
                n.headers.put("X-NotificationClass", "1");
            }
            if (body.startsWith("<")) {
                n.contentType = ContentType.APPLICATION_XML;
            }
            return n;
        }

        public static Notification createTemplateNotification(
                Map<String, String> properties) {
            Notification n = new Notification();
            StringBuffer buf = new StringBuffer();
            buf.append("{");
            for (Iterator<String> iterator = properties.keySet().iterator(); iterator
                    .hasNext();) {
                String key = iterator.next();
                buf.append("\"" + key + "\":\"" + properties.get(key) + "\"");
                if (iterator.hasNext())
                    buf.append(",");
            }
            buf.append("}");
            n.body = buf.toString();
            n.contentType = ContentType.APPLICATION_JSON;
            n.headers.put("ServiceBusNotification-Format", "template");
            return n;
        }

        public Map<String, String> getHeaders() { return headers; }

        public void setHeaders(Map<String, String> headers) { this.headers = headers; }

        public String getBody() { return body; }

        public void setBody(String body) { this.body = body; }

        public ContentType getContentType() { return contentType; }

        public void setContentType(ContentType contentType) { this.contentType = contentType; }
    }

此類別是原生通知主體的容器，或是一組範本通知案例的屬性，及一組包含格式 (原生平台或範本) 的標頭，以及平台特定屬性 (如 Apple 到期屬性和 WNS 標頭)。我們也會定義一些便利建構函式，以產生常用的通知類型。

請參閱[通知中心 REST API 文件][通知中心 REST API 文件]及特定通知平台的格式，以取得所有可用選項。

有了此類別之後，我們現在可以在 **NotificationHub** 類別內寫入傳送通知方法。

    public void sendNotification(Notification notification) {
        sendNotification(notification, "");
    }

    public void sendNotification(Notification notification, Set<String> tags) {
        if (tags.isEmpty())
            throw new IllegalArgumentException(
                    "tags has to contain at least an element");

        StringBuffer exp = new StringBuffer();
        for (Iterator<String> iterator = tags.iterator(); iterator.hasNext();) {
            exp.append(iterator.next());
            if (iterator.hasNext())
                exp.append(" || ");
        }

        sendNotification(notification, exp.toString());
    }

    public void sendNotification(Notification notification, String tagExpression) {
        HttpPost post = null;
        try {
            URI uri = new URI(endpoint + hubPath + "/messages" + APIVERSION);
            post = new HttpPost(uri);
            post.setHeader("Authorization", generateSasToken(uri));

            if (tagExpression != null && !"".equals(tagExpression)) {
                post.setHeader("ServiceBusNotification-Tags", tagExpression);
            }

            for (String header : notification.getHeaders().keySet()) {
                post.setHeader(header, notification.getHeaders().get(header));
            }

            post.setEntity(new StringEntity(notification.getBody()));
            HttpResponse response = httpClient.execute(post);

            if (response.getStatusLine().getStatusCode() != 201) {
                String msg = "";
                if (response.getEntity() != null
                        && response.getEntity().getContent() != null) {
                    msg = IOUtils.toString(response.getEntity().getContent());
                }
                throw new RuntimeException("Error: " + response.getStatusLine()
                        + " body: " + msg);
            }

        } catch (Exception e) {
            throw new RuntimeException(e);
        } finally {
            if (post != null)
                post.releaseConnection();
        }
    }

上述方法會傳送 HTTP POST 要求至通知中心的 /messages 端點，並使用正確的主體和標頭傳送通知。

## <a name="complete-tutorial"></a>完成教學課程

現在您可以透過從 Java 後端傳送通知，來完成開始使用教學課程。

初始化您的通知中心用戶端 (請依[開始使用教學課程][開始使用教學課程]中的指示替換連接字串和中心名稱)：
NotificationHub hub = new NotificationHub("{connection string}", "{hubname}");

然後根據您的目標行動平台新增傳送程式碼。

### Windows 市集和 Windows Phone 8.1 (非 Silverlight)

    String toast = "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">Hello from Java!</text></binding></visual></toast>";
    Notification n = Notification.createWindowsNotification(toast);
    hub.sendNotification(n);

### iOS

    String alert = "{\"aps\":{\"alert\":\"Hello from Java!\"}}";
    Notification n = Notification.createAppleNotification(alert);
    hub.sendNotification(n);

### Android

    String message = "{\"data\":{\"msg\":\"Hello from Java!\"}}";
    Notification n = Notification.createGcmNotification(message);
    hub.sendNotification(n);

### Windows Phone 8.0 和 8.1 Silverlight

    String toast = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                "<wp:Notification xmlns:wp=\"WPNotification\">" +
                   "<wp:Toast>" +
                        "<wp:Text1>Hello from Java!</wp:Text1>" +
                   "</wp:Toast> " +
                "</wp:Notification>";
    Notification n = Notification.createMpnsNotification(toast);
    hub.sendNotification(n);

### Kindle Fire

    String message = "{\"data\":{\"msg\":\"Hello from Java!\"}}";
    Notification n = Notification.createAdmNotification(message);
    hub.sendNotification(n);

執行 Java 程式碼現在應會產生一則顯示於目標裝置的通知。

## <a name="next-steps"></a>後續步驟

在本主題中，我們會說明如何為通知中心建立簡單的 Java REST 用戶端。您可以在這裡執行下列動作：

-   下載完整的 [Java REST 包裝函式範例][Java REST 包裝函式範例]，其中包含上述所有程式碼，以及註冊管理。
-   繼續了解 [即時新聞教學課程] 中的通知中心標記功能
-   了解 [通知使用者教學課程] 中的推播通知給個人使用者

  [通知中心 REST API]: http://msdn.microsoft.com/zh-tw/library/dn223264.aspx
  [開始使用教學課程]: http://azure.microsoft.com/zh-tw/documentation/articles/notification-hubs-ios-get-started/
  [.NET 通知中心 SDK]: http://msdn.microsoft.com/zh-tw/library/jj933431.aspx
  [Java REST 包裝函式範例]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/notificationhubs-rest-java
  [完成教學課程]: #complete-tutorial
  [MSDN]: http://msdn.microsoft.com/zh-tw/library/dn530746.aspx
  [Apache HttpComponents]: http://hc.apache.org/httpcomponents-client-ga/
  [Apache Commons-Codec]: http://commons.apache.org/proper/commons-codec/
  [Apache Commons-Io]: http://commons.apache.org/proper/commons-io/
  [這裡]: http://msdn.microsoft.com/zh-tw/library/dn495627.aspx
  [通知中心 REST API 文件]: http://msdn.microsoft.com/zh-tw/library/dn495827.aspx
