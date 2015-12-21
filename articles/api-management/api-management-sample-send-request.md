<properties
   pageTitle="使用 API 管理服務產生 HTTP 要求"
   description="了解如何使用 API 管理中的要求和回應原則，從您的 API 呼叫外部服務"
   services="api-management"
   documentationCenter=""
   authors="darrelmiller"
   manager=""
   editor=""/>

<tags
   ms.service="api-management"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/03/2015"
   ms.author="v-darmi"/>


# 使用來自 Azure API 管理服務的外部服務

Azure API 管理服務中可用的原則可純粹根據連入要求、傳出回應及基本組態資訊來進行各式各樣的有用工作。不過，能夠與來自 API 管理原則的外部服務進行互動，可開啟更多的機會。

我們先前曾討論過如何與[適用於記錄、監視及分析的 Azure 事件中樞服務](api-management-sample-logtoeventhub.md)互動的方式。在本文中，我們將示範可讓您與任何以 HTTP 為基礎之外部服務進行互動的原則。這些原則可用來觸發遠端事件，或用來擷取將以某種方式用於操作原始要求和回應的資訊。

## 傳送單向要求
或許對要求來說，最簡單的外部互動是射後不理的樣式，讓外部服務能夠獲得某些種類之重要事件的通知。我們可以使用控制流程原則 `choose` 來偵測任何一種我們感興趣的狀況，接著，如果條件成立，我們就可以使用 [send-one-way-request](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendOneWayRequest) 原則提出外部的 HTTP 要求。這可能是對傳訊系統 (例如 Hipchat 或 Slack) 的要求，也可能是對郵件 API (例如 SendGrid 或 MailChimp) 的要求，或者是針對某些像是 PagerDuty 的重大支援事件的要求。所有的這些傳訊系統都具有簡單的 HTTP API，可讓我們輕鬆叫用。

### 使用 Slack 提供警示
下列範例示範如果 HTTP 回應狀態碼大於或等於 500，如何將訊息傳送至 Slack 聊天室。500 範圍錯誤表示我們的後端 API發生問題，而我們 API 的用戶端無法解決這類問題。通常我們需要進行某種形式的介入。

    <choose>
        <when condition="@(context.Response.StatusCode >= 500)">
          <send-one-way-request mode="new">
            <set-url>https://hooks.slack.com/services/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</set-url>
            <set-method>POST</set-method>
            <set-body>@{
                    return new JObject(
                            new JProperty("username","APIM Alert"),
                            new JProperty("icon_emoji", ":ghost:"),
                            new JProperty("text", String.Format("{0} {1}\nHost: {2}\n{3} {4}\n User: {5}",
                                                    context.Request.Method,
                                                    context.Request.Url.Path + context.Request.Url.QueryString,
                                                    context.Request.Url.Host,
                                                    context.Response.StatusCode,
                                                    context.Response.StatusReason,
                                                    context.User.Email
                                                    ))
                            ).ToString();
                }</set-body>
          </send-one-way-request>
        </when>
    </choose>

Slack 具有傳入 Web 攔截的概念。在設定傳入的 Web 攔截時，Slack 會產生特殊的 URL，讓您能夠執行簡單的 POST 要求，並將訊息傳遞至 Slack 通道。我們建立的 JSON 主體是以 Slack 所定義的格式為根據。

![Slack 的 Web 攔截](./media/api-management-sample-send-request/api-management-slack-webhook.png)

### 「射後不理」 夠好嗎？
使用要求的射後不理樣式有一些特定的權衡取捨。如果基於某些原因而導致要求失敗，則不會報告失敗。在此特殊情況下，無法保證具有次要失敗報告系統的複雜度，以及等待回應所需的其他效能成本。如果檢查回應很重要，則 [send-request](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendRequest) 原則是較好的選項。

## 傳送要求
`send-request` 原則能夠使用外部服務來執行複雜的處理函式，並將資料傳回 API 管理服務，此服務可用來進一步處理原則。

### 授權參考權杖
API 管理的主要功能是保護後端資源。如果您的 API 所使用的授權伺服器會建立 [JWT 權杖](http://jwt.io/) 做為其 OAuth2 流程的一部分，當 [Azure Active Directory](../active-directory/active-directory-aadconnect.md) 這樣做時，則您可以使用 `validate-jwt` 原則來驗證權杖的有效性。不過，某些授權伺服器會建立所謂的[參考權杖](http://leastprivilege.com/2015/11/25/reference-tokens-and-introspection/)，其無法在不對授權伺服器進行回呼的情況下進行驗證。

### 將自我檢查標準化
過去一直沒有標準化的方式可使用授權伺服器來驗證參考權杖。不過，IETF 最近發佈的提議標準 [RFC 7662](https://tools.ietf.org/html/rfc7662) 定義了資源伺服器如何驗證權杖的有效性。

### 擷取權杖
第一個步驟是從授權標頭擷取權杖。標頭值應該使用 `Bearer` 授權配置、單一空格和授權權杖，按照每個 [RFC 6750](http://tools.ietf.org/html/rfc6750#section-2.1) 進行格式化。不過，有一些情況需要省略授權配置。為了在剖析時說明這一點，我們會使用空格來分割標頭值，並從字串的傳回陣列中選取最後一個字串。這樣可為格式錯誤的授權標頭提供因應措施。

    <set-variable name="token" value="@(context.Request.Headers.GetValueOrDefault("Authorization","scheme param").Split(' ').Last())" />

### 提出驗證要求
一旦擁有授權權杖之後，就可以提出要求來驗證權杖。RFC 7662 會呼叫此程序進行自我檢查，並要求您將 HTML 表單 `POST` 到自我檢查資源。HTML 表單至少必須包含具有索引鍵 `token` 的索引鍵/值組。這項對授權伺服器的要求也必須經過驗證，以確保惡意用戶端無法撈取有效的權杖。

    <send-request mode="new" response-variable-name="tokenstate" timeout="20" ignore-error="true">
      <set-url>https://microsoft-apiappec990ad4c76641c6aea22f566efc5a4e.azurewebsites.net/introspection</set-url>
      <set-method>POST</set-method>
      <set-header name="Authorization" exists-action="override">
        <value>basic dXNlcm5hbWU6cGFzc3dvcmQ=</value>
      </set-header>
      <set-header name="Content-Type" exists-action="override">
        <value>application/x-www-form-urlencoded</value>
      </set-header>
      <set-body>@($"token={(string)context.Variables["token"]}")</set-body>
    </send-request>

### 檢查回應
`response-variable-name` 屬性可用來提供所傳回回應的存取權。這個屬性中定義的名稱可以用來做為 `context.Variables` 字典的索引鍵來存取 `IResponse` 物件。

從回應物件中，我們可以擷取主體，而 RFC 7622 告訴我們，回應必須是 JSON 物件，而且必須包含至少一個稱為 `active` 的屬性 (此為布林值)。當 `active` 為 true，則權杖會被視為有效。

### 報告失敗
我們使用 `<choose>` 原則來偵測權杖是否無效，如果無效，則會傳回 401 回應。

    <choose>
      <when condition="@((bool)((IResponse)context.Variables["tokenstate"]).Body.As<JObject>()["active"] == false)">
        <return-response response-variable-name="existing response variable">
          <set-status code="401" reason="Unauthorized" />
          <set-header name="WWW-Authenticate" exists-action="override">
            <value>Bearer error="invalid_token"</value>
          </set-header>
        </return-response>
      </when>
    </choose>

根據每個說明應如何使用 `bearer` 權杖的 [RFC 6750](https://tools.ietf.org/html/rfc6750#section-3)，我們也會傳回 `WWW-Authenticate` 標頭以及 401 回應。WWW 驗證的目的是指示用戶端如何建構適當授權的要求。由於有各式各樣可能具備 OAuth2 架構的處理方法，因此很難傳達所有必要的資訊。幸好我們仍持續努力來協助[用戶端探索如何適當地將要求授權給資源伺服器](http://tools.ietf.org/html/draft-jones-oauth-discovery-00)。

### 最終解決方案
將所有項目放在一起，就能得到下列原則：

    <inbound>
      <!-- Extract Token from Authorization header parameter -->
      <set-variable name="token" value="@(context.Request.Headers.GetValueOrDefault("Authorization","scheme param").Split(' ').Last())" />

      <!-- Send request to Token Server to validate token (see RFC 7662) -->
      <send-request mode="new" response-variable-name="tokenstate" timeout="20" ignore-error="true">
        <set-url>https://microsoft-apiappec990ad4c76641c6aea22f566efc5a4e.azurewebsites.net/introspection</set-url>
        <set-method>POST</set-method>
        <set-header name="Authorization" exists-action="override">
          <value>basic dXNlcm5hbWU6cGFzc3dvcmQ=</value>
        </set-header>
        <set-header name="Content-Type" exists-action="override">
          <value>application/x-www-form-urlencoded</value>
        </set-header>
        <set-body>@($"token={(string)context.Variables["token"]}")</set-body>
      </send-request>

      <choose>
  			<!-- Check active property in response -->
  			<when condition="@((bool)((IResponse)context.Variables["tokenstate"]).Body.As<JObject>()["active"] == false)">
  				<!-- Return 401 Unauthorized with http-problem payload -->
  				<return-response response-variable-name="existing response variable">
  					<set-status code="401" reason="Unauthorized" />
  					<set-header name="WWW-Authenticate" exists-action="override">
  						<value>Bearer error="invalid_token"</value>
  					</set-header>
  				</return-response>
  			</when>
  		</choose>
      <base />
    </inbound>

這是眾多範例中唯一一個說明如何使用 `send-request` 原則來將有用的外部服務整合至要求和回應的程序，此程序的流程會通過 API 管理服務。

## 回應組合
`send-request` 原則可用來增強對後端系統的主要要求 (如同我們在上述範例中所見)，或者它可用來完全取代後端呼叫。使用這項技術，我們可以輕鬆地建立複合資源，這些資源彙總自多個不同的系統。

### 建置儀表板   
有時您想要能夠公開存在於多個後端系統中的資訊，例如，驅動儀表板。KPI 來自所有不同的後端，但是您習慣不提供它們的直接存取權，而且如果所有資訊都是擷取自單一要求，這就非常有用。或許有一些後端資訊需要進行某些切割與細分，需要先稍微處理一下！ 當您知道使用者習慣按 F5 鍵來查看其效能不佳的指標是否可能變更時，若要降低後端負載，能夠快取該複合資源就非常實用。

### 假造資源
建置儀表板資源的第一個步驟是在 API 管理發行者入口網站中設定新的作業。這是用來設定我們的撰寫原則以建置動態資源的預留位置作業。

![儀表板作業](./media/api-management-sample-send-request/api-management-dashboard-operation.png)

### 提出要求
一旦建立 `dashboard` 作業之後，我們就能特別針對該作業來設定原則。

![儀表板作業](./media/api-management-sample-send-request/api-management-dashboard-policy.png)

第一個步驟是擷取來自傳入要求的任何查詢參數，讓我們可以將其轉送到後端。在此範例中，我們的儀表板會根據一段時間來顯示資訊，因此具有 `fromDate` 和 `toDate` 參數。我們可以使用 `set-variable` 原則來擷取要求 URL 中的資訊。

    <set-variable name="fromDate" value="@(context.Request.Url.Query["fromDate"].Last())">
    <set-variable name="toDate" value="@(context.Request.Url.Query["toDate"].Last())">

一旦擁有這項資訊之後，就可以對所有後端系統提出要求。每個要求都會使用參數資訊來建構新的 URL，並呼叫各自的伺服器，將回應儲存於內容變數中。

    <send-request mode="new" response-variable-name="revenuedata" timeout="20" ignore-error="true">
      <set-url>@($"https://accounting.acme.com/salesdata?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

    <send-request mode="new" response-variable-name="materialdata" timeout="20" ignore-error="true">
      <set-url>@($"https://inventory.acme.com/materiallevels?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

    <send-request mode="new" response-variable-name="throughputdata" timeout="20" ignore-error="true">
    <set-url>@($"https://production.acme.com/throughput?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

    <send-request mode="new" response-variable-name="accidentdata" timeout="20" ignore-error="true">
    <set-url>@($"https://production.acme.com/throughput?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

這些要求將會依序執行，但這並不理想。在即將推出的版本中，我們將導入稱為 `wait` 的新原則，讓所有的這些要求都能以平行方式執行。

### 回應

若要建構複合回應，我們可以使用 [return-response](https://msdn.microsoft.com/library/azure/dn894085.aspx#ReturnResponse) 原則。`set-body` 元素可以使用運算式，來建構新的 `JObject` 以及內嵌為屬性的所有元件表示法。

    <return-response response-variable-name="existing response variable">
      <set-status code="200" reason="OK" />
      <set-header name="Content-Type" exists-action="override">
        <value>application/json</value>
      </set-header>
      <set-body>
        @(new JObject(new JProperty("revenuedata",((IResponse)context.Variables["revenuedata"]).Body.As<JObject>()),
                      new JProperty("materialdata",((IResponse)context.Variables["materialdata"]).Body.As<JObject>()),
                      new JProperty("throughputdata",((IResponse)context.Variables["throughputdata"]).Body.As<JObject>()),
                      new JProperty("accidentdata",((IResponse)context.Variables["accidentdata"]).Body.As<JObject>())
                      ).ToString())
      </set-body>
    </return-response>

完整的原則看起來如下：

    <policies>
    	<inbound>

      <set-variable name="fromDate" value="@(context.Request.Url.Query["fromDate"].Last())">
      <set-variable name="toDate" value="@(context.Request.Url.Query["toDate"].Last())">

        <send-request mode="new" response-variable-name="revenuedata" timeout="20" ignore-error="true">
          <set-url>@($"https://accounting.acme.com/salesdata?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
          <set-method>GET</set-method>
        </send-request>

        <send-request mode="new" response-variable-name="materialdata" timeout="20" ignore-error="true">
          <set-url>@($"https://inventory.acme.com/materiallevels?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
          <set-method>GET</set-method>
        </send-request>

        <send-request mode="new" response-variable-name="throughputdata" timeout="20" ignore-error="true">
        <set-url>@($"https://production.acme.com/throughput?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
          <set-method>GET</set-method>
        </send-request>

        <send-request mode="new" response-variable-name="accidentdata" timeout="20" ignore-error="true">
        <set-url>@($"https://production.acme.com/throughput?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
          <set-method>GET</set-method>
        </send-request>

        <return-response response-variable-name="existing response variable">
          <set-status code="200" reason="OK" />
          <set-header name="Content-Type" exists-action="override">
            <value>application/json</value>
          </set-header>
          <set-body>
            @(new JObject(new JProperty("revenuedata",((IResponse)context.Variables["revenuedata"]).Body.As<JObject>()),
                          new JProperty("materialdata",((IResponse)context.Variables["materialdata"]).Body.As<JObject>()),
                          new JProperty("throughputdata",((IResponse)context.Variables["throughputdata"]).Body.As<JObject>()),
                          new JProperty("accidentdata",((IResponse)context.Variables["accidentdata"]).Body.As<JObject>())
                          ).ToString())
          </set-body>
        </return-response>
    	</inbound>
    	<backend>
    		<base />
    	</backend>
    	<outbound>
    		<base />
    	</outbound>
    </policies>

在預留位置作業的組態中，我們可以將儀表板資源設定為至少快取一個小時，因為我們了解資料的本質意味著即使它在一個小時之後就會過期，仍然可以充分有效傳達重要資訊給使用者。

## 摘要
Azure API 管理服務提供彈性的原則，可以選擇性地套用到 HTTP 流量，並且能夠組合後端服務。不論您是否想要使用警示功能、確認、驗證功能或根據多個後端服務建立新的複合資源來增強您的 API 閘道器，`send-request` 及相關原則都會開啟各種可能性。

<!---HONumber=AcomDC_1210_2015-->