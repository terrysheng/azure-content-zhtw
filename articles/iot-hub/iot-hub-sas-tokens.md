<properties
 pageTitle="如何產生 IoT 中樞安全性權杖 | Microsoft Azure"
 description="說明 IoT 中樞使用的各類安全性權杖，以及如何產生它們。"
 services="iot-hub"
 documentationCenter=".net"
 authors="fsautomata"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="tbd"
 ms.date="03/02/2016"
 ms.author="elioda"/>

# 使用 IoT 中樞安全性權杖

IoT 中樞使用安全性權杖來驗證裝置和服務，以避免透過線路傳送金鑰。此外，安全性權杖有時效性和範圍的限制。[Azure IoT 中樞 SDK][lnk-apis-sdks] 能在不需要任何特殊組態的情況下自動產生權杖。不過在某些案例中，使用者必須直接產生及使用安全性權杖。這些案例包括直接使用 AMQP、MQTT 或 HTTP 介面或實作權杖服務模式，如 [IoT 中心指引][lnk-guidance-security]所述。

本文章說明：

* 安全性權杖的格式，以及如何產生。
* 使用安全性權杖來驗證裝置和後端服務的主要使用案例。

## 安全性權杖結構
透過安全性權杖，您可以將 IoT 中樞內特定功能的限時存取權限授與裝置和服務。為了確保只有取得授權的裝置和服務可以連線，安全性權杖必須經過共用存取原則金鑰簽署，或經過與裝置身分識別一同儲存在身分識別登錄中的對稱金鑰簽署。

經過共用存取原則金鑰簽署的權限，能授與所有與共用存取原則權限相關聯之功能的存取權限。請參閱 [IoT 中樞開發人員指南的安全性章節][lnk-devguide-security]。另一方面，經過裝置身分識別對稱金鑰簽署的權杖，只能授與相關聯之裝置身分識別的 **DeviceConnect** 權限。

安全性權杖具有下列格式：

    SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}

以下是預期的值：

| 值 | 說明 |
| ----- | ----------- |
| {signature} | HMAC-SHA256 簽章字串，格式為：`{URL-encoded-resourceURI} + "\n" + expiry`。**重要事項**：金鑰是從 base64 解碼而來，並且會做為用來執行 HMAC-SHA256 計算的金鑰。 |
| {resourceURI} | 可使用此權杖存取之端點的 URI 前置詞 (依區段)，其開頭為 IoT 中樞的主機名稱 (無通訊協定)。例如，`myHub.azure-devices.net/devices/device1` |
| {expiry} | 從新紀元時間 (Epoch) 1970 年 1 月 1日 00:00:00 UTC 時間至今秒數的 UTF8 字串。 |
| {URL-encoded-resourceURI} | 小寫資源 URI 的小寫 URL 編碼 |
| {policyName} | 此權杖所參考的共用存取原則名稱。在權杖參考裝置登錄認證的情況下不存在。 |

**前置詞的注意事項**︰URI 前置詞是依區段 (而不是依字元) 計算。例如，`/a/b` 是 `/a/b/c` 的前置詞，而不是 `/a/bc` 的前置詞。

這是從輸入 `resourceUri, signingKey, policyName, expiresInMins` 計算權杖的 Node 函式。下一節將詳細說明如何初始化不同權杖使用案例的不同輸入。

    var crypto = require('crypto');

    var generateSasToken = function(resourceUri, signingKey, policyName, expiresInMins) {
        resourceUri = encodeURIComponent(resourceUri.toLowerCase()).toLowerCase();

        // Set expiration in seconds
        var expires = (Date.now() / 1000) + expiresInMins * 60;
        expires = Math.ceil(expires);
        var toSign = resourceUri + '\n' + expires;

        // using crypto
        var decodedPassword = new Buffer(signingKey, 'base64').toString('binary');
        const hmac = crypto.createHmac('sha256', decodedPassword);
        hmac.update(toSign);
        var base64signature = hmac.digest('base64');
        var base64UriEncoded = encodeURIComponent(base64signature);

        // construct autorization string
        var token = "SharedAccessSignature sr=" + resourceUri + "&sig="
        * base64UriEncoded + "&se=" + expires;
        if (policyName) token += "&skn="+policyName;
        // console.log("signature:" + token);
        return token;
    };

> [AZURE.NOTE] 由於權杖的時效性會在 IoT 中樞機器上驗證，因此請務必將產生權杖之機器上的時鐘飄移降低最低。

## 將 SAS 權杖當做裝置

利用安全性權杖取得 IoT 中樞之 **DeviceConnect** 權限的方法有兩種：使用裝置身分識別金鑰或共用存取原則金鑰。

此外，請注意，所有可從裝置存取的功能都會因為前置詞為 `/devices/{deviceId}` 之端點的設計而公開。

> [AZURE.IMPORTANT] IoT 中樞驗證特定裝置的唯一方法是使用裝置身分識別對稱金鑰。在使用共用存取原則來存取裝置功能的案例中，方案必須將發出安全性權杖之元件視為信任的子元件。

面向裝置的端點是 (不論通訊協定為何)︰

| 端點 | 功能 |
| ----- | ----------- |
| `{iot hub host name}/devices/{deviceId}/messages/events` | 傳送裝置到雲端的訊息。 |
| `{iot hub host name}/devices/{deviceId}/devicebound` | 接收雲端到裝置的訊息。 |

### 使用身分識別登錄中的對稱金鑰

使用裝置身分識別的對稱金鑰來產生權杖時，會省略權杖的 policyName (`skn`) 元素。

例如，為存取所有裝置功能而建立的權杖應具有下列參數︰

* 資源 URI：`{IoT hub name}.azure-devices.net/devices/{device id}`、
* 簽署金鑰︰`{device id}` 身分識別的任何對稱金鑰、
* 無原則名稱、
* 任何到期時間。

使用前述 Node 函式的範例為︰

    var endpoint ="myhub.azure-devices.net/devices/device1";
    var deviceKey ="...";

    var token = generateSasToken(endpoint, deviceKey, null, 60);

結果 (將所有功能的存取權限授與 device1) 為︰

    SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697

> [AZURE.NOTE] 您可以使用 .NET 工具[裝置總管][lnk-device-explorer]來產生安全金鑰。

### 使用共用存取原則

在從共用存取原則建立權杖時，您必須將原則名稱欄位 `skn` 設定為使用之原則的名稱。原則也必須授與 **DeviceConnect** 權限。

使用共用存取原則來存取裝置功能的兩個主要案例包括︰

* [雲端通訊協定閘道][lnk-azure-protocol-gateway]、
* 用來實作自訂驗證配置的[權杖服務][lnk-devguide-security]。

由於共用存取原則可能可以授與以任何裝置身分連線的存取權限，因此在建立安全性權杖時，請務必使用正確的資源 URI。這點對權杖服務來說特別重要，因為它們必須使用資源 URI 來設定權杖的範圍，以便納入特定裝置。這點與通訊協定閘道的關聯性比較薄弱，因為它們已經在為所有裝置調節流量。

例如，使用稱為 **device** 之預先建立共用存取原則的權杖服務，會建立具有以下參數的權杖︰

* 資源 URI：`{IoT hub name}.azure-devices.net/devices/{device id}`、
* 簽署金鑰︰`device` 原則的其中一個金鑰、
* 原則名稱：`device`、
* 任何到期時間。

使用前述 Node 函式的範例為︰

    var endpoint ="myhub.azure-devices.net/devices/device1";
    var policyName = 'device';
    var policyKey = '...';

    var token = generateSasToken(endpoint, policyKey, policyName, 60);

結果 (將所有功能的存取權限授與 device1) 為︰

    SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697&skn=device

通訊協定閘道可以將相同的權杖用於所有裝置上，只要將資源 URI 設定為 `myhub.azure-devices.net/devices` 即可。

## 使用來自服務元件的安全性權杖

服務元件只能使用授與適當權限的共用存取原則來產生安全性權杖，如 [IoT 中樞開發人員指南的安全性一節][lnk-devguide-security]所述。

以下是在端點上公開的服務功能︰

| 端點 | 功能 |
| ----- | ----------- |
| `{iot hub host name}/devices` | 建立、更新、擷取及刪除裝置身分識別。 |
| `{iot hub host name}/messages/events` | 接收裝置到雲端的訊息。 |
| `{iot hub host name}/servicebound/feedback` | 接收雲端到裝置之訊息的意見反應。 |
| `{iot hub host name}/devicebound` | 傳送雲端到裝置的訊息。 |

例如，使用稱為 **registryRead** 之預先建立共用存取原則產生的服務，會建立具有以下參數的權杖︰

* 資源 URI：`{IoT hub name}.azure-devices.net/devices`、
* 簽署金鑰︰`registryRead` 原則的其中一個金鑰、
* 原則名稱：`registryRead`、
* 任何到期時間。

    var endpoint ="myhub.azure-devices.net/devices"; var policyName = 'device'; var policyKey = '...';

    var token = generateSasToken(endpoint, policyKey, policyName, 60);

結果 (授與所有裝置身分識別的讀取權限) 會是︰

    SharedAccessSignature sr=myhub.azure-devices.net%2fdevices&sig=JdyscqTpXdEJs49elIUCcohw2DlFDR3zfH5KqGJo4r4%3D&se=1456973447&skn=registryRead


[lnk-apis-sdks]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-guidance-security]: iot-hub-guidance.md#customauth
[lnk-devguide-security]: iot-hub-devguide.md#security
[lnk-azure-protocol-gateway]: iot-hub-protocol-gateway.md
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/doc/how_to_use_device_explorer.md

<!---HONumber=AcomDC_0316_2016-->