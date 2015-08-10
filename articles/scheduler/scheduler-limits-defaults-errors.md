<properties
 pageTitle="排程器限制、預設值和錯誤碼"
 description=""
 services="scheduler"
 documentationCenter=".NET"
 authors="krisragh"
 manager="dwrede"
 editor=""/>
<tags
 ms.service="scheduler"
 ms.workload="infrastructure-services"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="article"
 ms.date="07/28/2015"
 ms.author="krisragh"/>

# 排程器限制、預設值和錯誤碼

## 排程器配額、限制、預設值和節流

[AZURE.INCLUDE [scheduler-limits-table](../../includes/scheduler-limits-table.md)]

## x-ms-request-id 標頭

每一個對排程器服務提出的要求都會傳回名為 \*\*x-ms-request-id\*\* 的回應標頭。此標頭包含專門識別要求的不透明值。

如果要求一直失敗，而且您已確認要求表達正確，則可以使用此值將錯誤回報給 Microsoft。在您的報告中，包括值 ofx-ms-request-id、提出要求的大約時間、訂用帳戶的識別碼、雲端服務、工作集合及/或工作，以及要求所嘗試的作業類型。

## 排程器狀態和錯誤碼

除了標準 HTTP 狀態碼外，Azure 排程器 REST API 還會傳回延伸的錯誤碼和錯誤訊息。延伸的錯誤碼不會取代標準 HTTP 狀態碼，但會提供其他可採取動作的資訊，可以搭配標準 HTTP 狀態碼使用。

例如，發生 HTTP 404 錯誤可能有許多原因，因此延伸訊息中具有其他資訊可以協助解決問題。如需 REST API 所傳回之標準 HTTP 狀態碼的詳細資訊，請參閱[服務管理狀態和錯誤碼](https://msdn.microsoft.com/library/windowsazure/ee460801.aspx)。服務管理 API 的 REST API 作業會傳回標準 HTTP 狀態碼，其定義在 [HTTP/1.1 狀態碼定義](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)中。下表描述可能由服務傳回的常見錯誤。

|錯誤碼|HTTP 狀態碼|使用者訊息|
|----|----|----|
|MissingOrIncorrectVersionHeader|要求錯誤 (400)|未指定或未正確指定版本控制標頭。|
|InvalidXmlRequest|要求錯誤 (400)|要求內文的 XML 無效或未正確指定。|
|MissingOrInvalidRequiredQueryParameter|要求錯誤 (400)|未對此要求指定必要的查詢參數，或未正確指定。|
|InvalidHttpVerb|要求錯誤 (400)|伺服器無法辨識指定的 HTTP 動詞或不是這個資源的有效動詞。|
|AuthenticationFailed|禁止 (403)|伺服器無法驗證要求。請確認憑證有效，而且與此訂用帳戶相關聯。|
|ResourceNotFound|找不到 (404)|指定的資源不存在。|
|InternalError|內部伺服器錯誤 (500)|伺服器發生內部錯誤。請重試要求。|
|OperationTimedOut|內部伺服器錯誤 (500)|無法在允許的時間內完成作業。|
|ServerBusy|服務無法使用 (503)|伺服器 (或內部元件) 目前無法用來接收要求。請重試您的要求。|
|SubscriptionDisabled|禁止 (403)|訂用帳戶處於已停用狀態。|
|BadRequest|要求錯誤 (400)|參數不正確。|
|ConflictError|衝突 (409)|發生衝突，致使作業無法完成。|
|TemporaryRedirect|暫時重新導向 (307)|無法使用要求的物件。可從回應中的 [位置] 欄位取得物件的新位置的暫時 URI。可在新的 URI 上重複原始要求。|

API 作業也可能傳回管理服務所定義的其他錯誤資訊。此其他錯誤資訊是以回應本文傳回。錯誤回應的本文遵循底下顯示的基本格式。

		<?xml version="1.0" encoding="utf-8"?>  
		<Error>  
			<Code>string-code</Code>  
			<Message>detailed-error-message</Message>  
		</Error>  

## 另請參閱

 [排程器概念、術語及實體階層](scheduler-concepts-terms.md)

 [在管理入口網站中開始使用排程器](scheduler-get-started-portal.md)

 [Azure 排程器的計劃和計費](scheduler-plans-billing.md)

 [如何使用 Azure 排程器建立複雜的排程和進階週期](scheduler-advanced-complexity.md)

 [排程器 REST API 參考](https://msdn.microsoft.com/library/dn528946)

 [排程器高可用性和可靠性](scheduler-high-availability-reliability.md)

 [排程器輸出驗證](scheduler-outbound-authentication.md)

<!---HONumber=July15_HO5-->