<properties
   pageTitle="做為可呼叫端點的邏輯應用程式"
   description="如何建立並設定 HTTP 接聽程式，並在 Azure App Service 的邏輯應用程式中使用它"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="anuragdalmia"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="02/17/2016"
   ms.author="stepsic"/>


# 做為可呼叫端點的邏輯應用程式

邏輯應用程式的上一個結構描述版本 (*2014-12-01-preview*) 需要使用名為 **HTTP 接聽程式**的 API 應用程式，來公開可能同步呼叫的 HTTP 端點。透過最新的結構描述 (*2015-08-01-preview*)，邏輯應用程式原本就能公開同步的 HTTP 端點。

## 將觸發程序加入您的定義
第一個步驟是將觸發程序加入您的邏輯應用程式定義，如此您就能收到連入要求。有 3 個類型的觸發程序可以收到要求：* 手動 * apiConnectionWebhook * httpWebhook

針對本文章的其餘部分，我們將使用**手動**做為範例，但所有的原則同樣適用於其他 2 個類型的觸發程序。將此觸發程序加入您的工作流程定義，會使其看起如下：

```
{
    "$schema": "http://schema.management.azure.com/providers/Microsoft.Logic/schemas/2015-08-01-preview/workflowdefinition.json#",
    "triggers" : {
        "myendpointtrigger" : {
            "type" : "manual"
        }
    }
}
```

這將會建立您可在如下的 URL 上呼叫的端點：
 
```
https://prod-03.brazilsouth.logic.azure.com:443/workflows/080cb66c52ea4e9cabe0abf4e197deff/triggers/myendpointtrigger?...
```

您可以在使用者介面取得此端點，或藉由呼叫下列項目來取得：

```
POST https://management.azure.com/{resourceID of your logic app}/triggers/myendpointtrigger/listCallbackURL?api-version=2015-08-01-preview
```

## 呼叫邏輯應用程式觸發程序的端點
擁有觸發程序的端點之後，您可以在後端系統中儲存它，並透過對完整 URL 的 `POST` 來呼叫它。您可以在主體中包含額外的查詢參數、標頭，以及任何內容。

如果 content-type 是 `application/json`，則您將能夠從要求內部參考屬性。否則，它將會被視為單一的二進位單位，其可傳遞至其他 API，但無法在內部參考。

## 參考連入要求的內容
`@triggerOutputs()` 函式將輸出連入要求的內容。例如，它應該看起來如下：

```
{
    "headers" : {
        "content-type" : "application/json"
    },
    "body" : {
        "myprop" : "a value"
    }
}
```

您可以使用 `@triggerBody()` 捷徑，特別存取 `body` 屬性。

這與 *2014-12-01-preview* 版本有著些微差異，您可在其中透過如下的函式存取 HTTP 接聽程式的主體：`@triggerOutputs().body.Content`。

## 回應要求
對於某些啟動邏輯應用程式的要求，您可能想要使用某些內容來回應呼叫者。有一個名為 **response** 的新動作類型，可用來建構回應的狀態碼、主體及標頭。請注意，如果沒有出現**回應**圖形，邏輯應用程式端點將「立即」使用 [202 已接受] 來回應 (這相當於 HTTP 接聽程式中的「自動傳送回應」)。

```
"myresponse" : {
    "type" : "response",
    "inputs" : {
        "statusCode" : 200,
        "body" : {
            "contentFieldOne" : "value100",
            "anotherField" : 10.001
        },
        "headers" : {
            "x-ms-date" : "@utcnow()",
            "Content-type" : "application/json"
        }
    },
    "conditions" : []
}
```

回應會包含下列內容：

| 屬性 | 說明 |
| -------- | ----------- |
| StatusCode | 要回應連入要求的 HTTP 狀態碼。它可以是任何以 2xx、4xx 或 5xx 開頭的有效狀態碼。不允許 3xx 狀態碼。 | 
| body | 主體物件可以是字串、JSON 物件，甚至是上一個步驟中所參考的二進位內容。 | 
| headers | 您可以定義要包含於回應中的標頭，且數目不限 | 

邏輯應用程式中針對回應所需的所有步驟都必須在原始要求收到要求的「60 秒」內完成。如果在 60 秒內未達成任何回應動作，則連入要求將會逾時，並收到 [408 用戶端逾時] HTTP 回應。

## 進階的端點組態
邏輯應用程式內建直接存取端點的支援，並一律使用 `POST` 方法來啟動執行。**HTTP 接聽程式** API 應用程式之前也支援變更 URL 區段和 HTTP 方法。您甚至可以藉由將其加入 API 應用程式主機 (已裝載 API 應用程式的 Web 應用程式)，來設定額外的安全性或自訂網域。

此功能是透過 **API 管理**取得：* [變更要求的方法](https://msdn.microsoft.com/library/azure/dn894085.aspx#SetRequestMethod) * [變更要求的 URL 區段](https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#RewriteURL) * 在傳統的 Azure 入口網站中的 [設定] 索引標籤上設定 API 管理網域 * 設定原則來檢查基本驗證 (**需要連結**)

## 從 2014-12-01-preview 開始的移轉摘要

| 2014-12-01-preview | 2015-08-01-preview |
|---------------------|--------------------|
| 按一下 [HTTP 接聽程式] API 應用程式 | 按一下 [手動觸發程序] \(不需要 API 應用程式) |
| HTTP 接聽程式設定 [自動傳送回應] | 可能包含**回應**動作或不在工作流程定義中 |
| 設定基本或 OAuth 驗證 | 透過 API 管理 |
| 設定 HTTP 方法 | 透過 API 管理 |
| 設定相對路徑 | 透過 API 管理 |
| 透過 `@triggerOutputs().body.Content` 參考連入主體 | 透過 `@triggerOutputs().body` 參考 |
| HTTP 接聽程式上的**傳送 HTTP 回應**動作 | 按一下 [回應 HTTP 要求] (不需要 API 應用程式)

<!----HONumber=AcomDC_0224_2016-->