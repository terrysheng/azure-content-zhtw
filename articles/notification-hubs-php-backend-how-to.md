<properties linkid="notification-hubs-php-back-end-how-to" urlDisplayName="How to use Notification Hubs with PHP" pageTitle="How to use Notification Hubs with PHP" metaKeywords="" description="Learn how to use Azure Notification Hubs from a PHP back-end." metaCanonical="" services="mobile-services,notification-hubs,push,php" documentationCenter="" title="How to use Notification Hubs with PHP" authors="elioda" solutions="" manager="" editor="" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="php" ms.topic="article" ms.date="01/01/1900" ms.author="elioda"></tags>

# 如何使用 Java/PHP 的通知中心

<div class="dev-center-tutorial-selector sublanding"> 
<a href="/zh-tw/documentation/articles/notification-hubs-java-backend-how-to/" title="Java">Java</a><a href="/zh-tw/documentation/articles/notification-hubs-php-backend-how-to/" title="PHP" class="current">PHP</a>
</div>

您可以使用通知中心 REST 介面，存取 Java/PHP/Ruby 後端的所有通知中心功能，如 MSDN 主題[通知中心 REST API][通知中心 REST API] 中所述。

在本主題中，我們將說明如何：

-   在 PHP 中建置通知中心功能的 REST 用戶端；
-   依照 [開始使用教學課程][開始使用教學課程]，針對您所選的行動平台，在 PHP 中實作後端部分。

## 用戶端介面

主要用戶端介面可提供與 [.NET 通知中心 SDK][.NET 通知中心 SDK] 提供的相同方法，這可讓您直接轉譯本網站上目前所提供以及網際網路上社群所貢獻的所有教學課程和範例。

您可在 [PHP REST 包裝函式範例][PHP REST 包裝函式範例]中找到所有可用的程式碼。

例如，若要建立用戶端：

    $hub = new NotificationHub("connection string", "hubname"); 

若要傳送 iOS 原生通知：

    $notification = new Notification("apple", '{"aps":{"alert": "Hello!"}}');
    $hub->sendNotification($notification);

## 實作

請依照[開始使用教學課程][開始使用教學課程]進行到最後一節的實作後端 (如果您尚未進行此作業)。
另外，如果您要的話，您可以使用 [PHP REST 包裝函式範例][PHP REST 包裝函式範例]中的程式碼，直接進入[完成教學課程][完成教學課程]一節。

您可以在 [MSDN][MSDN] 上找到所有實作完整 REST 包裝函式的詳細資料。在本節中，我們將針對存取通知中心 REST 端點所需主要步驟的 PHP 實作進行說明：

1.  解析連接字串
2.  產生授權權杖
3.  執行 HTTP 呼叫

### 解析連接字串

以下是實作其建構函式可解析連接字串之用戶端的主要類別：

    class NotificationHub {
        const API_VERSION = "?api-version=2013-10";

        private $endpoint;
        private $hubPath;
        private $sasKeyName;
        private $sasKeyValue;

        function __construct($connectionString, $hubPath) {
            $this->hubPath = $hubPath;

            $this->parseConnectionString($connectionString);
        }

        private function parseConnectionString($connectionString) {
            $parts = explode(";", $connectionString);
            if (sizeof($parts) != 3) {
                throw new Exception("Error parsing connection string: " . $connectionString);
            }

            foreach ($parts as $part) {
                if (strpos($part, "Endpoint") === 0) {
                    $this->endpoint = "https" . substr($part, 11);
                } else if (strpos($part, "SharedAccessKeyName") === 0) {
                    $this->sasKeyName = substr($part, 20);
                } else if (strpos($part, "SharedAccessKey") === 0) {
                    $this->sasKeyValue = substr($part, 16);
                }
            }
        }
    }

### 建立安全性權杖

您可以在[這裡][這裡]找到建立安全性權杖的詳細資料。
您必須將下列方法加入 **NotificationHub** 類別，才能根據目前要求的 URI 及擷取自連接字串的認證來建立權杖。

    private function generateSasToken($uri) {
        $targetUri = strtolower(rawurlencode(strtolower($uri)));

        $expires = time();
        $expiresInMins = 60;
        $expires = $expires + $expiresInMins * 60;
        $toSign = $targetUri . "\n" . $expires;

        $signature = rawurlencode(base64_encode(hash_hmac('sha256', $toSign, $this->sasKeyValue, TRUE)));

        $token = "SharedAccessSignature sr=" . $targetUri . "&sig="
                    . $signature . "&se=" . $expires . "&skn=" . $this->sasKeyName;

        return $token;
    }

### 傳送通知

首先，讓我們先定義呈現通知的類別。

    class Notification {
        public $format;
        public $payload;

        # array with keynames for headers
        # Note: Some headers are mandatory: Windows: X-WNS-Type, WindowsPhone: X-NotificationType
        # Note: For Apple you can set Expiry with header: ServiceBusNotification-ApnsExpiry in W3C DTF, YYYY-MM-DDThh:mmTZD (for example, 1997-07-16T19:20+01:00).
        public $headers;

        function __construct($format, $payload) {
            if (!in_array($format, ["template", "apple", "windows", "gcm", "windowsphone"])) {
                throw new Exception('Invalid format: ' . $format);
            }

            $this->format = $format;
            $this->payload = $payload;
        }
    }

此類別是原生通知主體的容器，或是一組範本通知案例的屬性，及一組包含格式 (原生平台或範本) 的標頭，以及平台特定屬性 (如 Apple 到期屬性和 WNS 標頭)。

請參閱[通知中心 REST API 文件][通知中心 REST API 文件]及特定通知平台的格式，以取得所有可用選項。

有了此類別之後，我們現在可以在 **NotificationHub** 類別內寫入傳送通知方法。

    public function sendNotification($notification) {
        $this->sendNotification($notification, "");
    }

    public function sendNotification($notification, $tagsOrTagExpression) {
        if (is_array($tagsOrTagExpression)) {
            $tagExpression = implode(" || ", $tagsOrTagExpression);
        } else {
            $tagExpression = $tagsOrTagExpression;
        }

        # build uri
        $uri = $this->endpoint . $this->hubPath . "/messages" . NotificationHub::API_VERSION;
        $ch = curl_init($uri);

        if (in_array($notification->format, ["template", "apple", "gcm"])) {
            $contentType = "application/json";
        } else {
            $contentType = "application/xml";
        }

        $token = $this->generateSasToken($uri);

        $headers = [
            'Authorization: '.$token,
            'Content-Type: '.$contentType,
            'ServiceBusNotification-Format: '.$notification->format
        ];

        if ("" !== $tagExpression) {
            $headers[] = 'ServiceBusNotification-Tags: '.$tagExpression;
        }

        # add headers for other platforms
        if (is_array($notification->headers)) {
            $headers = array_merge($headers, $notification->headers);
        }
        
        curl_setopt_array($ch, array(
            CURLOPT_POST => TRUE,
            CURLOPT_RETURNTRANSFER => TRUE,
            CURLOPT_SSL_VERIFYPEER => FALSE,
            CURLOPT_HTTPHEADER => $headers,
            CURLOPT_POSTFIELDS => $notification->payload
        ));

        // Send the request
        $response = curl_exec($ch);

        // Check for errors
        if($response === FALSE){
            throw new Exception(curl_error($ch));
        }

        $info = curl_getinfo($ch);

        if ($info['http_code'] <> 201) {
            throw new Exception('Error sending notificaiton: '. $info['http_code'] . ' msg: ' . $response);
        }
    } 

上述方法會傳送 HTTP POST 要求至通知中心的 /messages 端點，並使用正確的主體和標頭傳送通知。

## <a name="complete-tutorial"></a>完成教學課程

現在您可以透過從 PHP 後端傳送通知，來完成開始使用教學課程。

初始化您的通知中心用戶端 (請依[開始使用教學課程][開始使用教學課程]中的指示替換連接字串和中心名稱)：
$hub = new NotificationHub("connection string", "hubname");

然後根據您的目標行動平台新增傳送程式碼。

### Windows 市集和 Windows Phone 8.1 (非 Silverlight)

    $toast = '<toast><visual><binding template="ToastText01"><text id="1">Hello from PHP!</text></binding></visual></toast>';
    $notification = new Notification("windows", $toast);
    $notification->headers[] = 'X-WNS-Type: wns/toast';
    $hub->sendNotification($notification);

### iOS

    $alert = '{"aps":{"alert":"Hello from PHP!"}}';
    $notification = new Notification("apple", $alert);
    $hub->sendNotification($notification);

### Android

    $message = '{"data":{"msg":"Hello from PHP!"}}';
    $notification = new Notification("gcm", $message);
    $hub->sendNotification($notification);

### Windows Phone 8.0 和 8.1 Silverlight

    $toast = '<?xml version="1.0" encoding="utf-8"?>' .
                '<wp:Notification xmlns:wp="WPNotification">' .
                   '<wp:Toast>' .
                        '<wp:Text1>Hello from PHP!</wp:Text1>' .
                   '</wp:Toast> ' .
                '</wp:Notification>';
    $notification = new Notification("mpns", $toast);
    $notification->headers[] = 'X-WindowsPhone-Target : toast';
    $notification->headers[] = 'X-NotificationClass : 2';
    $hub->sendNotification($notification);

### Kindle Fire

    $message = '{"data":{"msg":"Hello from PHP!"}}';
    $notification = new Notification("adm", $message);
    $hub->sendNotification($notification);

執行 PHP 程式碼現在應會產生一則顯示於目標裝置的通知。

## 後續步驟

在本主題中，我們會說明如何為通知中心建立簡單的 Java REST 用戶端。您可以在這裡執行下列動作：

-   下載完整的 [PHP REST 包裝函式範例][PHP REST 包裝函式範例]，其中包含上述所有程式碼。
-   繼續了解 [即時新聞教學課程] 中的通知中心標記功能
-   了解 [通知使用者教學課程] 中的推播通知給個人使用者

  [Java]: /zh-tw/documentation/articles/notification-hubs-java-backend-how-to/ "Java"
  [PHP]: /zh-tw/documentation/articles/notification-hubs-php-backend-how-to/ "PHP"
  [通知中心 REST API]: http://msdn.microsoft.com/zh-tw/library/dn223264.aspx
  [開始使用教學課程]: http://azure.microsoft.com/zh-tw/documentation/articles/notification-hubs-ios-get-started/
  [.NET 通知中心 SDK]: http://msdn.microsoft.com/zh-tw/library/jj933431.aspx
  [PHP REST 包裝函式範例]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/notificationhubs-rest-php
  [完成教學課程]: #complete-tutorial
  [MSDN]: http://msdn.microsoft.com/zh-tw/library/dn530746.aspx
  [這裡]: http://msdn.microsoft.com/zh-tw/library/dn495627.aspx
  [通知中心 REST API 文件]: http://msdn.microsoft.com/zh-tw/library/dn495827.aspx
