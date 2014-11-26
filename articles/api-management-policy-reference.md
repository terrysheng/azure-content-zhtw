<properties pageTitle="Azure API Management Policy Reference" metaKeywords="" description="Learn about the policies available to configure API Management." metaCanonical="" services="" documentationCenter="API Management" title="Azure API Management Policy Reference" authors="sdanie" solutions="" manager="" editor="" />

<tags ms.service="api-management" ms.workload="mobile" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="sdanie" />

# Azure API 管理原則參考文件

本主題提供下列 API 管理 (預覽) 原則的參考。如需有關新增和設定原則的資訊，請參閱 [API 管理中的原則][API 管理中的原則]。

-   [存取限制原則][存取限制原則]

    -   [使用量配額][使用量配額] - 可讓您強制執行可更新或存留期呼叫量及/或頻寬配額。
    -   [速率限制][速率限制] - 限制呼叫數及/或頻寬耗用率以防止 API 使用量突增。
    -   [呼叫端 IP 限制][呼叫端 IP 限制] - 篩選 (允許/拒絕) 來自特定 IP 位址及/或位址範圍的呼叫。
-   [內容轉換原則][內容轉換原則]

    -   [設定 HTTP 標頭][設定 HTTP 標頭] - 指派值給現有的回應及/或要求標頭，或加入新的回應及/或要求標頭。
    -   [將 XML 轉換為 JSON][將 XML 轉換為 JSON] - 將要求或回應本文從 XML 轉換為 "JSON" 或 JSON 的「XML 忠實」形式。
    -   [取代本文中的字串][取代本文中的字串] - 尋找要求或回應子字串並取代為不同的子字串。
    -   [設定查詢字串參數][設定查詢字串參數] - 新增、取代值或刪除要求查詢字串參數。
-   [快取原則][快取原則]

    -   [儲存至快取][儲存至快取] - 根據指定的快取組態來快取回應。
    -   [從快取中取得][從快取中取得] - 執行快取查閱並傳回有效的快取回應 (如果有的話)。
-   [其他原則][其他原則]

    -   [重寫 URI][重寫 URI] - 將要求 URL 從公用格式轉換成 Web 服務所需的格式。
    -   [遮罩內容中的 URL][遮罩內容中的 URL] - 重寫 (遮罩) 回應本文和位置標頭中的連結，使它們經由 Proxy 指向同等的連結。
    -   [允許跨網域呼叫][允許跨網域呼叫] - 將 API 設為可供 Adobe Flash 和 Microsoft Silverlight 瀏覽器型用戶端存取。
    -   [JSONP][JSONP] - 將 JSON 與補充的 (JSONP) 支援加入至操作或 API，以允許來自 JavaScript 瀏覽器型用戶端的跨網域呼叫。
    -   [CORS][CORS] - 將跨原始來源資源分享 (CORS) 支援加入至操作或 API，以允許來自瀏覽器型用戶端的跨網域呼叫。

## <a name="access-restriction-policies"> </a> 存取限制原則

-   [使用量配額][使用量配額] - 可讓您強制執行可更新或存留期呼叫量及/或頻寬配額。
-   [速率限制][速率限制] - 限制呼叫數及/或頻寬耗用率以防止 API 使用量突增。
-   [呼叫端 IP 限制][呼叫端 IP 限制] - 篩選 (允許/拒絕) 來自特定 IP 位址及/或位址範圍的呼叫。

### <a name="usage-quota"> </a> 使用量配額

**描述：**
強制執行可更新或存留期呼叫量及/或頻寬配額。

**原則陳述式：**

    <quota calls="number" bandwidth="kilobytes" renewal-period="seconds">
        <api name="name" calls="number" bandwidth="kilobytes">
            <operation name="name" calls="number" bandwidth="kilobytes" />
        </api>
    </quota>

**範例：**

    <policies>
        <inbound>
            <base />
            <quota calls="10000" bandwidth="40000" renewal-period="3600" />
        </inbound>
        <outbound>
            <base />
        </outbound>
    </policies>

**可套用的位置：**
在產品範圍的輸入區段中使用。

**可套用的時機：**
應該限制產品的操作及/或 API 使用量時。

**套用和不套用的理由：**
根據時間和頻寬來定義產品的使用量限制。當 API 達到已定義的配額限制時，則會拒絕後續的呼叫並傳回錯誤訊息。配額通常定義為在一段預先定義的時間間隔內和頻寬限制下允許的要求訊息數。

| 屬性                  | 說明                                                                               |
|-----------------------|------------------------------------------------------------------------------------|
| quota calls="數字"    | 在 renewal-period 期間允許的訂閱呼叫數上限 (例如， 10000)                          |
| bandwidth="KB"        | 在指定的 renewal-period 期間允許此產品、API 或操作耗用的 KB 數上限 (例如， 40000). |
| renewal-period="秒"   | 套用配額的時間週期 (以秒為單位) (例如，3600)。                                     |
| api name="名稱"       | 套用配額的 API 呼叫的名稱。                                                        |
| calls="數字"          | 在指定的 renewal-period 內可呼叫 API 或操作的次數上限 (例如， 5000).               |
| operation name="名稱" | 套用配額的操作名稱。                                                               |

### <a name="rate-limit"> </a> 速率限制

**描述：**
限制流向 API 和 (選擇性地) 特定 API 操作的要求速率，以防止 API 使用量突增。觸發此原則時，取用者會收到 `429 Too Many Requests` 回應狀態碼。

**原則陳述式：**

    <rate-limit calls="number" renewal-period="seconds">
        <api name="name" calls="number" renewal-period="seconds">
            <operation name="name" calls="number" renewal-period="seconds" />
        </api>
    </rate-limit>

**範例：**

    <policies>
        <inbound>
            <base />
            <rate-limit calls="20" renewal-period="90" />
        </inbound>
        <outbound>
            <base />        
        </outbound>
    </policies>

**可套用的位置：**
在產品範圍的輸入區段中使用。

| 元素/屬性             | 說明                                   |
|-----------------------|----------------------------------------|
| calls="數字"          | 每一更新間隔允許的呼叫數               |
| renewal-period="秒"   | 速率限制呼叫配額更新之前經過的時間週期 |
| api name="名稱"       | 套用速率限制的 API 名稱                |
| operation name="名稱" | 套用速率限制的操作名稱                 |

### <a name="caller-ip-restriction"> </a> 呼叫端 IP 限制

**描述：**
篩選 (允許/拒絕) 來自特定 IP 位址及/或位址範圍的呼叫。

**原則陳述式：**

    <ip-filter action="allow | forbid">
        <address>address</address>
        <address-range from="address" to="address" />
    </ip-filter>

**範例：**

    <ip-filter action="allow | forbid">
        <address>address</address>
        <address-range from="address" to="address" />
    </ip-filter>

**可套用的位置：**
在任何範圍的輸入區段中使用。

**應該套用的時機：**
需要明確控制誰可存取您的服務時，請使用此原則。

**套用和不套用的理由：**
只有當個別主機或一群主機需要嚴格控制存取時，才需要使用此原則 (例如，安全性需求很高的服務)。

| 屬性                                | 說明                                 |
|-------------------------------------|--------------------------------------|
| ip-filter action="allow | forbid"   | 指定允許或不允許指定的位址進行呼叫。 |
| address="位址"                      | 允許或拒絕存取的一或多個 IP 位址。   |
| address-range from="位址" to="位址" | 允許或拒絕存取的 IP 位址範圍。       |

## <a name="content-transformation-policies"> </a> 內容轉換原則

-   [設定 HTTP 標頭][設定 HTTP 標頭] - 指派值給現有的回應及/或要求標頭，或加入新的回應及/或要求標頭。
-   [將 XML 轉換為 JSON][將 XML 轉換為 JSON] - 將要求或回應本文從 XML 轉換為 "JSON" 或 JSON 的「XML 忠實」形式。
-   [取代本文中的字串][取代本文中的字串] - 尋找要求或回應子字串並取代為不同的子字串。
-   [設定查詢字串參數][設定查詢字串參數] - 新增、取代值或刪除要求查詢字串參數。

### <a name="set-http-header"> </a> 設定 HTTP 標頭

**描述：**
指派值給現有的回應及/或要求標頭，或加入新的回應及/或要求標頭。

將 HTTP 標頭清單插入至 HTTP 訊息中。放在輸入管線中時，此原則會為傳遞至目標服務的要求設定 HTTP 標頭。放在輸出管線中時，此原則會為傳送至 Proxy 用戶端的回應設定 HTTP 標頭。

**原則陳述式：**

    <set-header name="header name" exists-action="override | skip | append | delete">
        <value>value</value> <!--for multiple headers with the same name add additional value elements-->
    </set-header>

**範例：**

    <set-header exists-action="override">
        <header name="some value to set" exists-action="override">
        <value>20</value> 
        </header>
    </set-header>

**可套用的位置：**
在「發行者」以外的任何範圍的輸入和輸出區段中使用。

**應該套用的時機：**
用於指定 HTTP 交易的操作參數及/或傳回值。

**套用和不套用的理由：**
大部分 HTTP 要求和許多回應都需要標頭來指定輸入/回應參數。所以，此原則很可能適用於幾乎所有的服務。

| 屬性                                     | 說明                                                                                                                                                    |
|------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------|
| reconcile-action="override|skip|append"  | 指定當已指定標頭時要採取的動作。注意：設為 "override" 時，編列多個相同名稱的項目會導致根據所有項目來設定標頭 (列出多次)；只有列出的值才會設定在結果中。 |
| header name="標頭名稱"                   | 指定要設定之標頭的名稱。必要。                                                                                                                          |
| exists-action="override | skip | append" | 指定當已指定標頭時要採取的動作                                                                                                                          |
| value="值"                               | 指定要設定之標頭的值。                                                                                                                                  |

### <a name="convert-xml-to-json"> </a> 將 XML 轉換為 JSON

**描述：**
將要求或回應本文從 XML 轉換為 JSON。

**原則陳述式：**

    <xml-to-json kind="javascript-friendly | direct" apply="always | content-type-xml" consider-accept-header="true | false"/>

**範例：**

    <policies>
        <inbound>
            <base />
        </inbound>
        <outbound>
            <base />
            <xml-to-json kind="direct" apply="always" consider-accept-header="false" />
        </outbound>
    </policies>

**可套用的位置：**
在 API 或操作範圍的輸入或輸出區段中使用。

**套用和不套用的理由：**
根據 XML 專用後端 Web 服務將 API 現代化。

| 屬性                                  | 說明                                                                                     |
|---------------------------------------|------------------------------------------------------------------------------------------|
| kind="javascript-friendly | direct    | 轉換過的 JSON 有 JavaScript 開發人員熟悉的格式 | 轉換過的 JSON 可反映原始 XML 文件的結構 |
| apply= always | content-type-xml      | 永遠轉換 | 只有當 `Content-Type` 標頭指出 XML 存在時才轉換                               |
| consider-accept-header="true | false" | 根據 `Accept` 標頭的值套用轉換 | 永遠轉換                                                |

### <a name="replace-string-in-body"> </a> 取代本文中的字串

**描述：**
在要求或回應中尋找子字串，並取代為不同的字串。

**原則陳述式：**

    <find-and-replace from="what to replace" to="replacement" />

**範例：**

    <find-and-replace from="notebook" to="laptop" />

**可套用的位置：**
在任何範圍的輸入和輸出區段中使用。

| 屬性                | 說明                             |
|---------------------|----------------------------------|
| from="要取代的字串" | 要搜尋的字串。                   |
| to="替換字串"       | 用來取代以上所找到之字串的字串。 |

### <a name="set-query-string-parameter"> </a> 設定查詢字串參數

**描述：**
新增、取代值或刪除要求查詢字串參數。

**原則陳述式：**

    <set-query-parameter name="param name" exists-action="override | skip | append | delete">
        <value>value</value> <!--for multiple parameters with the same name add additional value elements-->
    </set-query-parameter>

**範例：**

    <policies>
        <inbound>
            <base />
            <set-query-parameter>
                <parameter name="api-key" exists-action="skip">
                    <value>12345678901</value>
                </parameter>
                <!-- for multiple parameters with the same name add additional value elements -->
            </set-query-parameter>
        </inbound>
        <outbound>
            <base />
        </outbound>
    </policies>

**可套用的位置：**
可在任何範圍的輸入區段中使用。

**套用和不套用的理由：**
用來傳遞後端服務所需的查詢參數，為選擇性或從未存在於要求中。

| 元素/屬性                | 說明                                   |
|--------------------------|----------------------------------------|
| name="名稱"              | 用來命名參數的字串                     |
| exists-action="override" | 取代參數的值 (如果出現在要求中)        |
| exists-action="skip"     | 如果參數出現在要求中，則不執行任何動作 |
| exists-action="append"   | 將值附加至現有的要求參數               |
| exists-action="delete"   | 從要求中移除參數\*                     |
| value="值"               | 在括住的元素中設定參數的值             |

## <a name="caching-policies"> </a> 快取原則

-   [儲存至快取][儲存至快取] - 根據指定的快取組態來快取回應。
-   [從快取中取得][從快取中取得] - 執行快取查閱並傳回有效的快取回應 (如果有的話)。

### <a name="store-to-cache"> </a> 儲存至快取

**描述：**
根據指定的快取設定來快取回應。必須有對應的[從快取中取得][從快取中取得]原則。

**原則陳述式：**

    cache-store duration="seconds" caching-mode="cache-on | do-not-cache" />

**範例：**

    <policies>
        <inbound>
            <base />
              <cache-lookup vary-by-developer=*"true | false"* vary-by-developer-groups=*"true | false"* downstream-caching-type=*"none | private | public"*>
                    <vary-by-header>Accept</vary-by-header> <!-- should be present in most cases -->
                    <vary-by-header>Accept-Charset</vary-by-header> <!-- should be present in most cases -->
                    <vary-by-header>header name</vary-by-header> <!-- optional, can repeated several times -->
                    <vary-by-query-parameter>parameter name</vary-by-query-parameter> <!-- optional, can repeated several times -->
            </cache-lookup>
        </inbound>
        <outbound>
            <base />
                <cache-store duration="3600" caching-mode="cache-on" />
        </outbound>
    </policies>

**可套用的位置：**
可出現在 API 和操作範圍其中一個或兩者的輸出區段中。

**套用的時機：**
當回應內容在一段期間維持靜態時應該套用。

**套用和不套用的理由：**
回應快取可降低加諸於後端 Web 伺服器的頻寬和處理需求，並縮短 API 取用者所感受的延遲時間。

| 元素/屬性               | 說明                                                    |
|-------------------------|---------------------------------------------------------|
| seconds                 | 快取項目的存留時間 (以秒為單位，預設值=3600)            |
| cache-on | do-not-cache | 快取回應 | 不快取回應，且快取的相關標頭設為禁止下游快取 |

### <a name="get-from-cache"> </a> 從快取中取得

**描述：**
執行快取查閱並傳回有效的快取回應 (如果有的話)。適當地回應 API 取用者的快取驗證要求。必須有對應的[儲存至快取][儲存至快取]原則。

**原則陳述式：**

    <cache-lookup vary-by-developer=*"true | false"* vary-by-developer-groups=*"true | false"* downstream-caching-type=*"none | private | public"*>
        <vary-by-header>Accept</vary-by-header> <!-- should be present in most cases -->
        <vary-by-header>Accept-Charset</vary-by-header> <!-- should be present in most cases -->
        <vary-by-header>header name</vary-by-header> <!-- optional, can repeated several times -->
        <vary-by-query-parameter>parameter name</vary-by-query-parameter> <!-- optional, can repeated several times -->
    </cache-lookup>

**範例：**

    <policies>
        <inbound>
            <base />
            <cache-lookup vary-by-developer="false" vary-by-developer-groups="false" downstream-caching-type="none">
                <vary-by-query-parameter>version</vary-by-query-parameter>
                <vary-by-header>Accept</vary-by-header>
                <vary-by-header>Accept-Charset</vary-by-header>
            </cache-lookup>         
        </inbound>
        <outbound>
            <cache-store duration="seconds" caching-mode="cache-on | do-not-cache" />
            <base />        
        </outbound>
    </policies>

**可套用的位置：**
可出現在 API 和操作範圍其中一個或兩者的輸入區段中。

**套用的時機：**
當回應內容在一段期間維持靜態時應該套用。

**套用和不套用的理由：**
回應快取可降低加諸於後端 Web 伺服器的頻寬和處理需求，並縮短 API 取用者所感受的延遲時間。

| 元素/屬性                                         | 說明                                                                                                                                               |
|---------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------|
| vary-by-developer="true | false"                  | 設為 \*true\* 可根據開發人員金鑰開始快取回應；預設會設為 \*false\*                                                                                 |
| vary-by-developer-groups="true | false"           | 設為 \*true\* 可根據使用者角色開始快取回應；預設會設為 \*false\*                                                                                   |
| downstream-caching-type="none | private | public" | \*none\* - 不允許下游快取；預設值 | \*private\* - 允許下游私人快取 | \*public\* - 允許私人和共用下游快取。                                         |
| vary-by-header:"Accept"                           | 根據 `Accept` 標頭的值開始快取回應                                                                                                                 |
| vary-by-header:Accept-Charset"                    | 根據 `Accept-Charset` 標頭的值開始快取回應                                                                                                         |
| vary-by-header:"標頭名稱"                         | 根據指定標頭的值開始快取回應，例如 `Accept | Accept-Charset | Accept-Encoding | Accept-Language | Authorization | Expect | From | Host | If-Match` |
| vary-by-query-parameter:"參數名稱"                | 根據指定查詢參數的值開始快取回應。輸入單一或多個參數。使用分號作為分隔符號。如果未指定，則會使用所有查詢參數。                                     |

## <a name="other-policies"> </a> 其他原則

-   [重寫 URI][重寫 URI] - 將要求 URL 從公用格式轉換成 Web 服務所需的格式。
-   [遮罩內容中的 URL][遮罩內容中的 URL] - 重寫 (遮罩) 回應本文和位置標頭中的連結，使它們經由 Proxy 指向同等的連結。
-   [允許跨網域呼叫][允許跨網域呼叫] - 將 API 設為可供 Adobe Flash 和 Microsoft Silverlight 瀏覽器型用戶端存取。
-   [JSONP][JSONP] - 將 JSON 與補充的 (JSONP) 支援加入至操作或 API，以允許來自 JavaScript 瀏覽器型用戶端的跨網域呼叫。
-   [CORS][CORS] - 將跨原始來源資源分享 (CORS) 支援加入至操作或 API，以允許來自瀏覽器型用戶端的跨網域呼叫。

### <a name="rewrite-uri"> </a> 重寫 URI

**描述：**
將要求 URL 從公用格式轉換成 Web 服務所需的格式。

**公用 URL：** <http://api.example.com/storenumber/ordernumber>

**要求 URL：** <http://api.example.com/v2/US/hardware/storenumber&ordernumber?City&State>。

請勿在重寫 URL 中加入額外的範本路徑參數。您只能加入查詢字串參數。

**原則陳述式：**

    <rewrite-uri template="uri template" />

**範例：**

    <policies>
        <inbound>
            <base />
            <rewrite-uri template="/v2/US/hardware/{storenumber}&{ordernumber}?City=city&State=state" />
        </inbound>
        <outbound>
            <base />
        </outbound>
    </policies>

**可套用的位置：**
只在操作範圍的輸入區段中。

**套用的時機：**
應該將一般人及/或瀏覽器可理解的 URL 轉換成 Web 服務所需的 URL 格式時使用。

**套用和不套用的理由：**
只有在公開替代 URL 格式時才需要套用。簡潔 URL、RESTful URL、使用者易記 URL 或符合 SEO 的 URL 是單純的結構式 URL，不含查詢字串，只包含資源的路徑 (在配置和授權後面)。這樣做通常是基於美觀、實用性或搜尋引擎最佳化 (SEO) 目的。

| 屬性                | 說明                                    |
|---------------------|-----------------------------------------|
| template="uri 範本" | 含有任何查詢字串參數的實際 Web 服務 URL |

### <a name="mask-urls-in-content"> </a> 遮罩內容中的 URL

**描述：**
重寫 (遮罩) 回應本文和位置標頭中的連結，使它們經由 Proxy 指向同等的連結。

**原則陳述式：**

    <redirect-body-urls />

**範例：**

    <redirect-body-urls />

**可套用的位置：**
套用在 *API* 和「操作」範圍。可在輸入和輸出區段中套用。

### <a name="allow-cross-domain-calls"> </a> 允許跨網域呼叫

**描述：**
將 API 設為可供 Adobe Flash 和 Microsoft Silverlight 瀏覽器型用戶端存取。

**原則陳述式：**

    <cross-domain />

**範例：**

    <cross-domain />

**可套用的位置：**
在「發行者」範圍的輸入區段中使用。

### <a name="jsonp"> </a> JSONP

**描述：**
將 JSON 與補充的 (JSONP) 支援加入至操作或 API，以允許來自 JavaScript 瀏覽器型用戶端的跨網域呼叫。JSONP 是 JavaScript 程式中使用的方法，可從位於不同網域的伺服器要求資料。JSONP 會略過大多數網頁瀏覽器中規定必須在相同網域內才能存取網頁的限制。

**原則陳述式：**

    <jsonp callback-parameter-name="callback function name" />

**範例：**

    <jsonp callback-parameter-name="cb" />

如果呼叫方法未指定回呼參數 `?cb=XXX`，則會傳回一般 JSON (沒有函數呼叫包裝函式)。

如果加上回呼參數 `?cb=XXX`，則會傳回 JSONP 結果，在回呼函數旁邊包裝原始的 JSON 結果，例如 `XXX('<json result goes here>')`;

**可套用的位置：**
只在輸出區段中使用。

**套用的時機：**
從位於不同網域的伺服器要求資料。

| 屬性                    | 說明                                                         |
|-------------------------|--------------------------------------------------------------|
| callback-parameter-name | 跨網域 JavaScript 函數呼叫，開頭加上函數所在的完整網域名稱。 |

### <a name="cors"> </a> CORS

**描述：**
將跨原始來源資源分享 (CORS) 支援加入至操作或 API，以允許來自瀏覽器型用戶端的跨網域呼叫。

CORS 可讓瀏覽器和伺服器互動，以決定是否允許特定的跨原始來源要求 (例如，從網頁上的 JavaScript 對其他網域提出的 XMLHttpRequests 呼叫)。這比只允許相同原始來源的要求更有彈性，也比允許所有跨原始來源的要求更安全。

**原則陳述式：**

     <cors>
        <allowed-origins>
            <origin>*</origin> <!-- allow any -->
            <!-- OR a list of one or more specific URIs (case-sensitive) -->
            <origin>URI</origin> <!-- URI must include scheme, host, and port. If port is omitted, 80 is assumed for http and 443 is assumed for https. -->
        </allowed-origins>
     </cors>

**範例：**

    <cors>
        <allowed-origins>
            <origin>*</origin> <!-- allow any -->
            <!-- OR a list of one or more specific URIs (case-sensitive) -->
            <origin>http://contoso.com:81</origin> <!-- URI must include scheme, host, and port. If port is omitted, 80 is assumed for http and 443 is assumed for https. -->
        </allowed-origins>
    </cors>

**可套用的位置：**
在輸入區段中且只在 *API* 和「操作」範圍內使用。

| 屬性                 | 說明                                                                                           |
|----------------------|------------------------------------------------------------------------------------------------|
| <origin>\*</origin>  | 允許任何「或」一或多個特定的 URI                                                               |
| <origin>URI</origin> | URI 必須包含配置、主機和連接埠。如果省略連接埠，則 http 會採用連接埠 80，而 https 會採用 443。 |

  [API 管理中的原則]: ../api-management-howto-policies
  [存取限制原則]: #access-restriction-policies
  [使用量配額]: #usage-quota
  [速率限制]: #rate-limit
  [呼叫端 IP 限制]: #caller-ip-restriction
  [內容轉換原則]: #content-transformation-policies
  [設定 HTTP 標頭]: #set-http-header
  [將 XML 轉換為 JSON]: #convert-xml-to-json
  [取代本文中的字串]: #replace-string-in-body
  [設定查詢字串參數]: #set-query-string-parameter
  [快取原則]: #caching-policies
  [儲存至快取]: #store-to-cache
  [從快取中取得]: #get-from-cache
  [其他原則]: #other-policies
  [重寫 URI]: #rewrite-uri
  [遮罩內容中的 URL]: #mask-urls-in-content
  [允許跨網域呼叫]: #allow-cross-domain-calls
  [JSONP]: #jsonp
  [CORS]: #cors
