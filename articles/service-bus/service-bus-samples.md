<properties 
   pageTitle="服務匯流排範例概觀 | Microsoft Azure"
   description="分類及描述服務匯流排範例與每個範例的連結。"
   services="service-bus"
   documentationCenter="na"
   authors="sethmanheim"
   manager="timlt"
   editor="tysonn" />
<tags 
   ms.service="service-bus"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/08/2015"
   ms.author="sethm" />

# 服務匯流排範例

服務匯流排範例會示範服務匯流排 (雲端服務) 和 Service Bus for Windows Server 中的重要功能。本主題分類及描述可用的範例與每個範例的連結。

>[AZURE.NOTE]服務匯流排範例不會與 SDK 一起安裝。若要取得範例，請造訪 [Azure SDK 範例頁面](https://code.msdn.microsoft.com)。

## 服務匯流排代理傳訊

下列範例會說明如何撰寫使用服務匯流排的應用程式。

請注意，代理傳訊範例需要連接字串以存取服務匯流排服務命名空間。

### 取得 Azure 服務匯流排的連接字串。

1. 登入 [Azure 傳統入口網站](http://manage.windowsazure.com)。

1. 在左側欄中，按一下 [服務匯流排]。

1. 按一下清單中的服務命名空間名稱。

1. 按一下 [連線資訊]。在 [存取連接資訊] 對話方塊中，將連接字串複製到剪貼簿。

1. 將連接字串貼到範例的 App.config 檔案。

### 取得 Service Bus for Windows Server 的連接字串

1. 執行下列 PowerShell cmdlet：

	```
	get-sbClientConfiguration
	```

2. 將連接字串貼到範例的 App.config 檔案。

### 開始使用範例

這些範例說明基本的傳訊和轉送功能。

|範例名稱|說明|最低需求 SDK 版本|Availability|
|---|---|---|---|
|[開始使用：使用佇列傳訊](http://code.msdn.microsoft.com/Getting-Started-Brokered-aa7a0ac3)|示範如何使用 Microsoft Azure 服務匯流排傳送和接收來自佇列的訊息。|1\.8|Microsoft Azure 服務匯流排；Service Bus for Windows Server|
|[開始使用：使用主題傳訊](http://code.msdn.microsoft.com/Getting-Started-Brokered-614d42e5)|示範如何使用 Microsoft Azure 服務匯流排傳送和接收來自包含多個訂用帳戶之主題的訊息。|1\.8|Microsoft Azure 服務匯流排；Service Bus for Windows Server|
|[開始使用事件中樞](http://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097)|示範事件中樞的基本功能，例如建立事件中樞、傳送事件到事件中樞、使用事件處理器取用事件。|2\.4|Microsoft Azure 服務匯流排|

### 探索功能

下列範例會示範服務匯流排的各種功能。

|範例名稱|說明|最低需求 SDK 版本|Availability|
|---|---|---|---|
|[HTTP 權杖提供者](http://code.msdn.microsoft.com/Service-Bus-HTTP-Token-38f2cfc5)|示範利用服務匯流排驗證 HTTP/REST 用戶端的不同方式。|2\.1|Microsoft Azure 服務匯流排；Service Bus for Windows Server|
|[服務匯流排 HTTP 用戶端](http://code.msdn.microsoft.com/Service-Bus-HTTP-client-fe7da74a)|示範如何透過 HTTP/REST 與服務匯流排傳之間送及及接收訊息。|2\.3|Microsoft Azure 服務匯流排；Service Bus for Windows Server|
|[服務匯流排自動轉送](http://code.msdn.microsoft.com/Service-Bus-Autoforwarding-b9df470b)|示範如何將訊息從佇列、訂用帳戶或無效信件佇列自動轉送到另一個佇列或主題。它也會示範如何透過傳輸佇列將訊息傳送到佇列或主題。|2\.3|Microsoft Azure 服務匯流排；Service Bus for Windows Server|
|[代理傳訊：WCF 通道工作階段範例](http://code.msdn.microsoft.com/Brokered-Messaging-WCF-0a526451)|示範如何透過 Windows Communication Foundation (WCF) 通道使用 Microsoft Azure 服務匯流排。此範例會顯示如何使用 WCF 通道來透過服務匯流排佇列傳送和接收訊息。此範例會顯示透過服務匯流排的工作階段和非工作階段通訊。|1\.8|Microsoft Azure 服務匯流排；Service Bus for Windows Server|
|[代理傳訊：交易](http://code.msdn.microsoft.com/Brokered-Messaging-8cd41d1e)|示範如何使用交易範圍內的 Microsoft Azure 服務匯流排傳訊功能，確保批次傳訊作業以不可部分完成的方式認可。|1\.8|Microsoft Azure 服務匯流排；Service Bus for Windows Server|
|[代理傳訊：使用 REST 的管理作業](http://code.msdn.microsoft.com/Brokered-Messaging-569cff88)|示範如何使用 REST 在服務匯流排上執行管理作業。|1\.8|Microsoft Azure 服務匯流排；Service Bus for Windows Server|
|[資源提供者 REST API](http://code.msdn.microsoft.com/Service-Bus-Resource-5d887203)|示範如何使用新的服務匯流排 RDFE REST API 來管理命名空間和訊息實體。|1\.8|Microsoft Azure 服務匯流排；Service Bus for Windows Server|
|[代理傳訊：WCF 服務工作階段範例](http://code.msdn.microsoft.com/Brokered-Messaging-WCF-db4262c2)|示範如何透過 WCF 服務模型使用 Microsoft Azure 服務匯流排。此範例示範如何透過服務匯流排佇列使用 WCF 服務模型來完成工作階段為基礎的通訊。|1\.8|Microsoft Azure 服務匯流排；Service Bus for Windows Server|
|[代理傳訊：要求回應](http://code.msdn.microsoft.com/Brokered-Messaging-Request-2b4ff5d8)|示範如何使用 Microsoft Azure 服務匯流排和要求/回應功能。此範例會示範透過服務匯流排佇列進行簡單的用戶端和伺服器通訊。|1\.8|Microsoft Azure 服務匯流排；Service Bus for Windows Server|
|[代理傳訊：無效信件佇列](http://code.msdn.microsoft.com/Brokered-Messaging-Dead-22536dd8)|示範如何使用 Microsoft Azure 服務匯流排和傳訊「無效信件佇列」功能。此範例會示範透過服務匯流排佇列進行簡單的傳送者和接收者通訊。|1\.8|Microsoft Azure 服務匯流排；Service Bus for Windows Server|
|[代理傳訊：延遲的訊息](http://code.msdn.microsoft.com/Brokered-Messaging-ccc4f879)|示範如何使用 Microsoft Azure 服務匯流排的訊息延遲功能。此範例會示範透過服務匯流排佇列進行簡單的傳送者和接收者通訊。|1\.8|Microsoft Azure 服務匯流排；Service Bus for Windows Server|
|[代理傳訊：工作階段訊息](http://code.msdn.microsoft.com/Brokered-Messaging-Session-41c43fb4)|示範如何使用 Microsoft Azure 服務匯流排和傳訊工作階段功能。此範例會示範透過服務匯流排佇列進行簡單的傳送者和接收者通訊。|1\.8|Microsoft Azure 服務匯流排；Service Bus for Windows Server|
|[代理傳訊：要求回應主題](http://code.msdn.microsoft.com/Brokered-Messaging-Request-6759a36e)|示範如何使用 Microsoft Azure 服務匯流排主題和訂用帳戶實作要求/回應模式。此範例會示範透過服務匯流排主題進行簡單的用戶端和伺服器通訊。|1\.8|Microsoft Azure 服務匯流排；Service Bus for Windows Server|
|[代理傳訊：要求回應佇列](http://code.msdn.microsoft.com/Brokered-Messaging-Request-0ce8fcaf)|示範如何使用 Microsoft Azure 服務匯流排和要求/回應功能。此範例會示範透過兩個服務匯流排佇列進行簡單的用戶端和伺服器通訊。|1\.8|Microsoft Azure 服務匯流排；Service Bus for Windows Server|
|[代理傳訊：重複偵測](http://code.msdn.microsoft.com/Brokered-Messaging-c0acea25)|示範如何搭配佇列使用 Microsoft Azure 服務匯流排重複訊息偵測。它會建立兩個佇列，一個啟用重複偵測而另一個沒有重複偵測。|1\.8|Microsoft Azure 服務匯流排；Service Bus for Windows Server|
|[代理傳訊：非同步傳訊](http://code.msdn.microsoft.com/Brokered-Messaging-Async-211c1e74)|示範如何使用 Microsoft Azure 服務匯流排以非同步方式傳送和接收來自佇列的訊息。佇列提供傳送者與任意數目接收者之間的低耦合、非同步通訊 (此處為單一接收者)。|1\.8|Microsoft Azure 服務匯流排；Service Bus for Windows Server|
|[代理傳訊︰進階篩選器](http://code.msdn.microsoft.com/Brokered-Messaging-6b0d2749)|示範如何使用 Microsoft Azure 服務匯流排發佈/訂閱進階篩選器。它會利用不同的篩選定義建立一個主題和 3 個訂用帳戶、將訊息傳送至主題，並從訂用帳戶接收所有訊息。|1\.8|Microsoft Azure 服務匯流排；Service Bus for Windows Server|
|[代理傳訊：訊息預先擷取](http://code.msdn.microsoft.com/Brokered-Messaging-be2dac1d)|示範如何使用 Microsoft Azure 服務匯流排的訊息預先擷取功能。它會示範如何在接收時使用訊息預先擷取功能。|1\.8|Microsoft Azure 服務匯流排；Service Bus for Windows Server|

## 服務匯流排轉送

示範服務匯流排轉送的範例。

### 開始使用

|範例名稱|說明|最低需求 SDK 版本|Availability|
|---|---|---|---|
|[轉送傳訊：Azure](http://code.msdn.microsoft.com/Relayed-Messaging-Windows-0d2cede3)|示範如何在 Azure 上執行服務匯流排用戶端和服務。此範例會以程式設計方式設定服務匯流排。只有環境和安全性資訊會儲存在組態檔中。|1\.8|Microsoft Azure 服務匯流排|
|[轉送傳訊驗證：共用的密碼](http://code.msdn.microsoft.com/Relayed-Messaging-92b04c02)|示範如何使用簽發者名稱和簽發者密碼來驗證服務匯流排。|1\.8|Microsoft Azure 服務匯流排|
|[轉送傳訊驗證：WebNoAuth](http://code.msdn.microsoft.com/Relayed-Messaging-a4f0b831)|示範如何公開不需要用戶端使用者驗證的 HTTP 服務。|1\.8|Microsoft Azure 服務匯流排|
|[轉送傳訊繫結：WebHttp](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-a6477ba0)|示範如何使用 **WebHttpRelayBinding** 繫結傳回使用 Web 程式設計模型的二進位資料。|1\.8|Microsoft Azure 服務匯流排|
|[轉送傳訊繫結：NetTcp Relayed](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-2dec7692)|示範如何使用 **NetTcpRelayBinding** 繫結。|1\.8|Microsoft Azure 服務匯流排|

### 探索功能

示範各種服務匯流排轉送功能的範例。

|範例名稱|說明|最低需求 SDK 版本|Availability|
|---|---|---|---|
|[轉送傳訊驗證：簡單的 WebToken](http://code.msdn.microsoft.com/Relayed-Messaging-32c74392)|示範如何使用簡式 web 權杖認證來驗證服務匯流排。這個範例和 Echo 範例類似，只有一些變更。具體而言，此範例會將行為加入 ServiceHost (服務) 和 ChannelFactory (用戶端) 應用程式中。|1\.8|Microsoft Azure 服務匯流排|
|[轉送傳訊：負載平衡](http://code.msdn.microsoft.com/Relayed-Messaging-Load-bd76a9f8)|示範如何使用 Microsoft Azure 服務匯流排將訊息路由傳送至多個接收者。它會顯示一個簡單服務的多個執行個體，透過 **NetTcpRelayBinding** 繫結與用戶端進行通訊|1\.8|Microsoft Azure 服務匯流排|
|[轉送傳訊繫結：網路事件](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-c0176977)|示範如何使用 Microsoft Azure 服務匯流排上的 **NetEventRelayBinding**繫結。|1\.8|Microsoft Azure 服務匯流排|
|[轉送傳訊繫結：WS2007Http 工作階段](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-ef1f1fcb)|示範如何使用已啟用可靠工作階段的 **WS2007HttpRelayBinding** 繫結。它也會示範如何在組態檔中 (而不是以程式設計方式) 指定服務匯流排認證。|1\.8|Microsoft Azure 服務匯流排|
|[轉送傳訊繫結：WS2007Http MsgSecCertificate](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-f29c9da5)|示範如何使用 **WS2007HttpRelayBinding** 繫結搭配訊息安全性來保護端對端訊息，同時仍要求用戶端驗證服務匯流排。|1\.8|Microsoft Azure 服務匯流排|
|[轉送傳訊：中繼資料交換](http://code.msdn.microsoft.com/Relayed-Messaging-Metadata-f122312e)|示範如何公開使用轉送繫結的中繼資料端點。下列轉送繫結中支援 **MetadataExchange**：**NetTcpRelayBinding**、**NetOnewayRelayBinding**、**BasicHttpRelayBinding** 和 **WS2007HttpRelayBinding**。|1\.8|Microsoft Azure 服務匯流排|
|[轉送傳訊繫結：NetTcp Direct](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-ca039161)|示範如何設定 **NetTcpRelayBinding** 繫結來支援 **混合式** 連接模式，該模式會先建立轉送的連接，並盡可能自動切換至用戶端和服務之間的直接連接。|1\.8|Microsoft Azure 服務匯流排|
|[轉送傳訊繫結：NetTcp MsgSec UserName](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-30542392)|示範如何使用 **NetTcpRelayBinding** 繫結搭配訊息安全性。|1\.8|Microsoft Azure 服務匯流排|
|[轉送傳訊繫結：Net Oneway](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-bb5b813a)|示範如何公開及取用使用 **NetOnewayRelayBinding** 繫結的服務端點。|1\.8|Microsoft Azure 服務匯流排|
|[轉送傳訊繫結：WS2007Http Simple](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-aa4b793a)|示範如何使用 **WS2007HttpRelayBinding** 繫結。它會示範一個簡單的服務，不使用任何安全性選項且不要求用戶端進行驗證。|1\.8|Microsoft Azure 服務匯流排|

## 服務匯流排參考工具

下列範例會示範服務的各種其他功能。

|範例名稱|說明|最低需求 SDK 版本|Availability|
|---|---|---|---|
|[服務匯流排總管](http://code.msdn.microsoft.com/Service-Bus-Explorer-f2abca5a)|服務匯流排總管可讓使用者連接到服務匯流排服務命名空間並以簡便的方式管理訊息實體。此工具提供進階的功能，例如匯入/匯出功能和測試訊息實體及轉送服務的能力。|1\.8|Microsoft Azure 服務匯流排；Service Bus for Windows Server|
|[授權：SBAzTool](http://code.msdn.microsoft.com/Authorization-SBAzTool-6fd76d93)|這個範例會示範如何在 Microsoft Azure Active Directory 存取控制中建立和管理服務身分識別 (也稱為存取控制服務或 ACS) 以搭配服務匯流排使用。|N/A|Microsoft Azure 服務匯流排|

## 後續步驟

請參閱下列主題以取得服務匯流排的其他概觀。

- [服務匯流排訊息概觀](service-bus-messaging-overview.md)
- [服務匯流排架構](service-bus-architecture.md)
- [服務匯流排基本概念](service-bus-fundamentals-hybrid-solutions.md)

<!---HONumber=AcomDC_1203_2015-->