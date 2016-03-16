<properties
	pageTitle="在 Azure API 管理中自訂快取"
	description="了解如何在 Azure API 管理中依索引鍵快取項目"
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
	ms.date="02/19/2016"
	ms.author="v-darmi"/>

# 在 Azure API 管理中自訂快取
Azure API 管理服務以資源 URL 做為索引鍵，內建對 [HTTP 回應的快取](api-management-howto-cache.md)的支援。可以在要求標頭中使用 `vary-by` 屬性修改索引鍵。這很適合用於快取整個 HTTP 回應 (也稱為｢表示法｣)，但是有時候也很適合用於只快取部分表示法。新的 [cache-lookup-value](https://msdn.microsoft.com/library/azure/dn894086.aspx#GetFromCacheByKey) 和 [cache-store-value](https://msdn.microsoft.com/library/azure/dn894086.aspx#StoreToCacheByKey) 原則提供了可儲存及擷取原則定義中任意資料的能力。這個能力也讓先前推出的 [send-request](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendRequest) 原則更有價值，因為您現在可以快取外部服務的回應。

## 架構  
API 管理服務使用共用的個別租用戶資料快取，所以，當您相應增加為多個單位時，您仍可以存取相同的快取資料。不過，使用多區域部署時，在每個區域內有獨立的快取。由於這個原因，切勿將快取視為資料存放區，資料存放區是部分資訊片段的唯一來源。如果您這麼做，之後又決定要採用多區域部署，擁有旅遊使用者的客戶可能會失去該快取資料的存取權。

## 片段快取
在某些案例中，傳回的回應包含的某些資料不但判斷代價昂貴，而且還會保留一段合理的長時間。以航空公司建立的服務為例，服務提供航班訂位、航班狀態等相關資訊。如果使用者是航空公司集點方案的會員，他們也會擁有與其目前狀態和累積里程的相關資訊。這些使用者相關資訊可能儲存在不同的系統中，但有可能需要包含在航班狀態和訂位相關的傳回回應中。這可以用稱為｢片段快取｣的程序做到。可從原始伺服器傳回主要的表示法，並使用某種權杖來指示要將使用者相關資訊插入何處。

考慮下列來自後端 API 的 JSON 回應。


    {
      "airline" : "Air Canada",
      "flightno" : "871",
      "status" : "ontime",
      "gate" : "B40",
      "terminal" : "2A",
      "userprofile" : "$userprofile$"
    }  

在 `/userprofile/{userid}` 的次要資源看起來像，

     { "username" : "Bob Smith", "Status" : "Gold" }

為了決定要包含的適當使用者資訊，我們需要找出使用者是誰。這個機制取決於實作。例如，我使用 `JWT` 權杖的 `Subject` 宣告。

    <set-variable
      name="enduserid"
      value="@(context.Request.Headers.GetValueOrDefault("Authorization","").Split(' ')[1].AsJwt()?.Subject)" />

我們會將這個 `enduserid` 值儲存在內容變數中供稍後使用。下一個步驟是判斷先前的要求是否已擷取使用者資訊並將其儲存在快取中。我們的做法是使用 `cache-lookup-value` 原則。

	  <cache-lookup-value
      key="@("userprofile-" + context.Variables["enduserid"])"
      variable-name="userprofile" />

如果回應索引鍵值的快取中沒有任何項目，則不會建立 `userprofile` 內容變數。我們使用 `choose` 控制流程原則來檢查查詢是否成功。

    <choose>
        <when condition="@(!context.Variables.ContainsKey("userprofile"))">
            <!— If the userprofile context variable doesn’t exist, make an HTTP request to retrieve it.  -->
        </when>
    </choose>


如果 `userprofile` 內容變數不存在，我們就要發出 HTTP 要求來擷取它。

	<send-request
      mode="new"
      response-variable-name="userprofileresponse"
      timeout="10"
      ignore-error="true">

      <!-- Build a URL that points to the profile for the current end-user -->
      <set-url>@(new Uri(new Uri("https://apimairlineapi.azurewebsites.net/UserProfile/"),
          (string)context.Variables["enduserid"]).AbsoluteUri)
      </set-url>
      <set-method>GET</set-method>
	</send-request>

我們使用 `enduserid` 來建構使用者設定檔資源的 URL。一旦得到回應，我們可以提取回應的本文文字，並將它存回內容變數。

    <set-variable
        name="userprofile"
        value="@(((IResponse)context.Variables["userprofileresponse"]).Body.As<string>())" />

為了避免我們需要再次發出這個 HTTP 要求，當相同使用者進行另一個要求時，我們可以將使用者設定檔儲存在快取中。

	<cache-store-value
        key="@("userprofile-" + context.Variables["enduserid"])"
        value="@((string)context.Variables["userprofile"])" duration="100000" />

我們使用我們原先嘗試擷取值時完全相同的索引鍵，將值儲存在快取中。我們選擇的值儲存持續時間，應該根據資訊變更的經常性以及使用者對過期資訊的容忍度。

重要的是要了解，從快取擷取仍然是程序外的網路要求，並可能使要求的時間增加數十毫秒。當判斷使用者設定檔資訊要花很長的時間，遠超過需要執行資料庫查詢或從多個後端彙總資訊的時間時，其優點就會顯現出來。

此程序的最後一個步驟是以我們的使用者設定檔資訊更新傳回的回應。

    <!—Update response body with user profile-->
    <find-and-replace
        from='"$userprofile$"'
        to="@((string)context.Variables["userprofile"])" />

我選擇在權杖中使用引號，這樣一來，即使取代沒有發生，回應仍是有效的 JSON。這麼做主要是讓偵錯更容易。

將這些步驟全部結合在一起後，最終結果是看起來像以下這個的原則。

     <policies>
    	<inbound>
    		<!-- How you determine user identity is application dependent -->
    		<set-variable
              name="enduserid"
              value="@(context.Request.Headers.GetValueOrDefault("Authorization","").Split(' ')[1].AsJwt()?.Subject)" />

    		<!--Look for userprofile for this user in the cache -->
    		<cache-lookup-value
              key="@("userprofile-" + context.Variables["enduserid"])"
              variable-name="userprofile" />

    		<!-- If we don’t find it in the cache, make a request for it and store it -->
    		<choose>
    			<when condition="@(!context.Variables.ContainsKey("userprofile"))">
    				<!—Make HTTP request to get user profile -->
    				<send-request
                      mode="new"
                      response-variable-name="userprofileresponse"
                      timeout="10"
                      ignore-error="true">

                       <!-- Build a URL that points to the profile for the current end-user -->
    					<set-url>@(new Uri(new Uri("https://apimairlineapi.azurewebsites.net/UserProfile/"),(string)context.Variables["enduserid"]).AbsoluteUri)</set-url>
    					<set-method>GET</set-method>
    				</send-request>

    				<!—Store response body in context variable -->
    				<set-variable
                      name="userprofile"
                      value="@(((IResponse)context.Variables["userprofileresponse"]).Body.As<string>())" />

    				<!—Store result in cache -->
    				<cache-store-value
                      key="@("userprofile-" + context.Variables["enduserid"])"
                      value="@((string)context.Variables["userprofile"])"
                      duration="100000" />
    			</when>
    		</choose>
    		<base />
    	</inbound>
    	<outbound>
    		<!—Update response body with user profile-->
    		<find-and-replace
                  from='"$userprofile$"'
                  to="@((string)context.Variables["userprofile"])" />
    		<base />
    	</outbound>
     </policies>

此快取方法主要用於 HTML 是在伺服器端組成的網站，讓 HTML 可以轉譯成單一頁面。不過，它也適用於用戶端無法執行用戶端 HTTP 快取的 API，或是不想將此責任放在用戶端的情況。

此種片段快取也可以在使用 Redis 快取伺服器的後端 Web 伺服器上進行，不過，當快取的片段來自不同後端而非主要回應時，使用 API 管理服務來執行這項工作便很實用。

## 透明的版本設定
隨時支援多個不同實作版本的 API 是常見的做法。這或許是為了支援不同的環境，像是開發、測試、生產等，或為了支援舊版的 API 讓 API 取用者有時間移轉到較新版本。

其中一種做法是將取用者目前想使用的 API 版本儲存在取用者的設定檔資料中，並呼叫適當的後端 URL，而不需要用戶端開發人員將 URL 從 `/v1/customers` 變更為 `/v2/customers`。為了判斷特定用戶端要呼叫的正確後端 URL，必須查詢某些組態資料。透過快取這項組態資料，我們可以減少執行此查詢的效能負面影響。

第一個步驟是判斷用來設定所需版本的識別碼。在此範例中，我選擇將版本關聯至產品訂用帳戶金鑰。

		<set-variable name="clientid" value="@(context.Subscription.Key)" />

然後，我們執行快取查閱，以查看我們是否已經擷取所需的用戶端版本。

		<cache-lookup-value
        key="@("clientversion-" + context.Variables["clientid"])"
        variable-name="clientversion" />

接著，我們找找看在快取中有沒有它。

	<choose>
		<when condition="@(!context.Variables.ContainsKey("clientversion"))">

如果沒有，就去擷取它。

	<send-request
        mode="new"
        response-variable-name="clientconfiguresponse"
        timeout="10"
        ignore-error="true">
        		<set-url>@(new Uri(new Uri(context.Api.ServiceUrl.ToString() + "api/ClientConfig/"),(string)context.Variables["clientid"]).AbsoluteUri)</set-url>
        		<set-method>GET</set-method>
	</send-request>

從回應中擷取回應本文文字。

    <set-variable
          name="clientversion"
          value="@(((IResponse)context.Variables["clientconfiguresponse"]).Body.As<string>())" />

將它存回快取中供日後使用。

    <cache-store-value
          key="@("clientversion-" + context.Variables["clientid"])"
          value="@((string)context.Variables["clientversion"])"
          duration="100000" />

最後，更新後端 URL 以選取用戶端所需的服務版本。

	<set-backend-service
          base-url="@(context.Api.ServiceUrl.ToString() + "api/" + (string)context.Variables["clientversion"] + "/")" />

完整的原則看起來如下。

	 <inbound>
		<base />
		<set-variable name="clientid" value="@(context.Subscription.Key)" />
		<cache-lookup-value key="@("clientversion-" + context.Variables["clientid"])" variable-name="clientversion" />

		<!-- If we don’t find it in the cache, make a request for it and store it -->
		<choose>
			<when condition="@(!context.Variables.ContainsKey("clientversion"))">
				<send-request mode="new" response-variable-name="clientconfiguresponse" timeout="10" ignore-error="true">
					<set-url>@(new Uri(new Uri(context.Api.ServiceUrl.ToString() + "api/ClientConfig/"),(string)context.Variables["clientid"]).AbsoluteUri)</set-url>
					<set-method>GET</set-method>
				</send-request>
				<!-- Store response body in context variable -->
				<set-variable name="clientversion" value="@(((IResponse)context.Variables["clientconfiguresponse"]).Body.As<string>())" />
				<!-- Store result in cache -->
				<cache-store-value key="@("clientversion-" + context.Variables["clientid"])" value="@((string)context.Variables["clientversion"])" duration="100000" />
			</when>
		</choose>
		<set-backend-service base-url="@(context.Api.ServiceUrl.ToString() + "api/" + (string)context.Variables["clientversion"] + "/")" />
	</inbound>


讓 API 取用者能夠直接控制用戶端不需要更新和重新部署用戶端就可以存取哪一個後端版本，是一種簡潔的解決辦法，能夠解決許多 API 版本設定的問題。

## 租用戶隔離

在大型、多租用戶的部署中，有些公司會在後端硬體的不同部署上建立不同的租用戶群組。這樣可以降低後端硬體問題所影響的客戶數目。也可以將新的軟體版本分階段推動。在理想情況下，對 API 取用者來說這個後端架構應該是透明的。其做法類似於透明的版本設定，因為它根據相同的後端 URL 操作技巧，使用每個 API 金鑰的組態狀態。

您將會傳回將租用戶與指派之硬體群組建立關聯的識別碼，而不是傳回每個訂用帳戶金鑰的 API 慣用的版本。這個識別碼可以用來建構適當的後端 URL。

## 摘要
能夠自由地使用 Azure API 管理快取來儲存任何種類的資料，可讓您有效率地存取組態資料，而這些資料可能會影響輸入要求的處理方式。上述快取也可以用來儲存資料片段，此種片段可以增加從後端 API 傳回的回應。

## 後續步驟
如果這些原則為您達成其他案例，或是有案例您想要達成但認為目前無法執行，敬請不吝賜教在 Disqus 的本主題系列中提供意見。

<!---HONumber=AcomDC_0302_2016-------->