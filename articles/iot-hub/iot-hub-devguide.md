<properties
 pageTitle="Azure IoT 中心開發人員指南 | Microsoft Azure"
 description="Azure IoT 中心開發人員指南涵蓋 IoT 中心端點、安全性、裝置身分識別登錄和傳訊"
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
 ms.date="09/08/2015"
 ms.author="elioda"/>

# Azure IoT 中心開發人員指南

Azure IoT 中心是一項完全受管理的服務，可讓數百萬個 IoT 裝置和一個應用程式後端進行可靠且安全的雙向通訊。

Azure IoT 中心能夠︰

* 使用每一裝置的安全性認證和存取控制來保護通訊的安全，
* 提供可靠的裝置到雲端和雲端到裝置的超大規模傳訊，以及
* 透過最受歡迎的語言和平台的裝置程式庫，進行簡單的裝置連線。

本文件涵蓋下列領域：

- [端點](#endpoints)。本節說明每個 IoT 中心針對執行階段和管理作業所公開的各種端點。
- [裝置身分識別登錄](#identityregistry)。本節說明哪些資訊會儲存在每個 IoT 中心的裝置身分識別登錄，以及如何加以存取和修改。
- [安全性](#security)。本節說明用來授與 IoT 中心功能存取權之裝置和雲端元件的安全性模型。
- [傳訊](#messaging)。本節說明 IoT 中心所公開的傳訊功能 (裝置到雲端和雲端到裝置)。
- [配額和節流](#throttling)。本節摘要說明適用於您的 IoT 中心的使用配額。

## 端點 <a id="endpoints"></a>

Azure IoT 中心是一項多租用戶服務，會公開各種動作項目的功能。下圖顯示 Azure IoT 中心所公開的各種端點。

![IoT 中心端點][img-endpoints]

以下是各端點的說明︰

* **資源提供者** -IoT 中心資源提供者會公開 [Azure 資源管理員][lnk-arm]介面，可讓 Azure 訂用帳戶擁有者建立 IoT 中心、更新 IoT 中心屬性及刪除 IoT 中心。IoT 中心屬性用來管理中心層級的安全性原則 (相對於裝置層級的存取控制，請參閱[存取控制](#accesscontrol)) 和雲端到裝置和裝置到雲端傳訊的功能選項。資源提供者也可讓您[匯出裝置識別](#importexport)。
* **裝置身分識別管理** - 每個 IoT 中心會公開一組用來管理裝置身分識別的 HTTP REST 端點 (也就是建立、擷取、更新和刪除)。裝置識別用於裝置驗證和存取控制。如需詳細資訊，請參閱[裝置身分識別登錄](#identityregistry)。
* **裝置端點** -對於裝置身分識別登錄中佈建的每個裝置，IoT 中心會公開一組用來與該裝置進行通訊的端點。這些端點目前公開於 HTTP 和 [AMQP][lnk-amqp] 中︰
    - *傳送裝置到雲端的訊息*。此端點用來傳送裝置到雲端的訊息。如需詳細資訊，請參閱[裝置到雲端傳訊](#d2c)。
    - *接收雲端到裝置的訊息*。裝置會使用此端點來接收以它為目標的雲端到裝置訊息。如需詳細資訊，請參閱[雲端到裝置傳訊](#c2d)。
* **服務端點** - 每個 IoT 中心也會公開一組由您的應用程式後端 (*服務*) 所使用的端點，以便與您的裝置進行通訊。目前僅使用 [AMQP][lnk-amqp] 通訊協定公開這些端點︰
    - *接收裝置到雲端的訊息*。此端點與 [Azure 事件中樞][lnk-event-hubs]相容，可用來讀取您的裝置所傳送的所有裝置到雲端訊息。如需詳細資訊，請參閱[裝置到雲端傳訊](#d2c)。
    - *傳送雲端到裝置的訊息及接收傳遞通知*。這些端點可讓您的應用程式後端傳送定雲端到裝置的可靠訊息，以及接收對應的傳遞或到期通知。如需詳細資訊，請參閱[雲端到裝置傳訊](#c2d)。

[IoT 中心 API 和 SDK][lnk-apis-sdks]一文說明您可用來存取這些端點的各種方式。

最後請務必注意，所有 IoT 中心端點都是透過 [TLS][lnk-tls] 公開，而且不曾在未加密/不安全的通道上公開任何端點。

### 如何從事件中樞相容的端點讀取。<a id="eventhubcompatible"></a>
使用[適用於 .NET 的 Azure 服務匯流排 SDK](https://www.nuget.org/packages/WindowsAzure.ServiceBus) 或[事件中樞 - 事件處理器主機]時，您可以使用任何 IoT 中心連接字串搭配正確的權限，然後使用 `messages/events` 做為事件中樞名稱。

使用未察覺 IoT 中心的 SDK (或產品整合) 時，您必須從 [Azure Preview 入口網站]的 IoT 中心設定擷取事件中樞相容的端點和事件中樞名稱︰

1. 在 IoT 中心刀鋒視窗中，按一下 [設定]，然後按一下 [傳訊]。
2. 在 [裝置到雲端的設定] 區段中，您會發現 [事件中樞相容端點]、[事件中樞相容名稱] 和 [資料分割] 方塊。

    ![][img-eventhubcompatible]

> [AZURE.NOTE]SDK 有時會需要 [主機名稱] 或 [命名空間]，在此情況下，請從 [事件中樞相容端點] 移除配置。比方說，如果事件中樞相容端點是 `sb://iothub-ns-myiothub-1234.servicebus.windows.net/`，則 [Hostname] 為 `iothub-ns-myiothub-1234.servicebus.windows.net`，而 [命名空間] 會是 `iothub-ns-myiothub-1234`。

您可以接著使用具有 **ServiceConnect** 權限的任何共用存取安全性原則來連接至上述的事件中樞。

萬一您必須使用上述資訊建立事件中樞連接字串，您可以使用下列模式：

        Endpoint={Event Hub-compatible endpoint};SharedAccessKeyName={iot hub policy name};SharedAccessKey={iot hub policy key}


以下是 SDK 清單以及可搭配 IoT 中心使用的整合︰

* [Java 事件中樞用戶端](https://github.com/hdinsight/eventhubs-client)
* [Apache Storm Spout](../hdinsight/hdinsight-storm-develop-csharp-event-hub-topology.md)，您可以[在此](https://github.com/apache/storm/tree/master/external/storm-eventhubs)找到 Spout 來源的連結
* [Apache Spark 整合](../hdinsight/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming.md)

## 裝置身分識別登錄 <a id="identityregistry"></a>
每個 IoT 中心都有一個裝置身分識別登錄，可用來在服務中建立各裝置的資源 (例如含有傳送中雲端到裝置訊息的佇列)，以及允許存取如[存取控制](#accesscontrol)一節所述的裝置面向端點。

總括來說，裝置身分識別登錄是符合 REST 限制的裝置身分識別資源集合。下列各節將詳細說明裝置身分識別資源屬性以及登錄允許對身分識別執行的作業。

> [AZURE.NOTE]如需 HTTP 通訊協定的詳細資訊以及可用來與裝置身分識別登錄互動的 SDK，您可以參考 [IoT 中心 API 和 SDK][lnk-apis-sdks]。

### 裝置身分識別屬性 <a id="deviceproperties"></a>
裝置識別會以具有下列屬性的 JSON 文件表示。

| 屬性 | 選項 | 說明 |
| -------- | ------- | ----------- |
| deviceId | 必要，只能讀取更新 | ASCII 7 位元英數字元 + `{'-', ':', '.', '+', '&percnt;', '_', '&num;', '&ast;', '?', '!', '(', ')', ',', '=', '&commat;', ';', '&dollar;', '''}` 的區分大小寫字串 (最長為 128 個字元)。 |
| generationId | 必要，唯讀 | 中樞產生的區分大小寫字串最長為 128 個字元。這在刪除並重建裝置時，用來區分具有相同 **deviceId** 的裝置。 |
| etag | 必要，唯讀 | 依據 [RFC7232][lnk-rfc7232]，代表裝置身分識別之弱式 etag 的字串。|
| auth | 選用 | 包含驗證資訊和安全性資料的複合物件。 |
| auth.symkey | 選用 | 包含主要和次要金鑰 (以 base64 格式儲存) 的複合物件。 |
| status | 必要 | 可以是 [啟用] 或 [停用]。如果為 [啟用]，則允許連接裝置。如果為 [停用]，此裝置無法存取任何裝置面向的端點。 |
| statusReason | 選用 | 128 個字元長的字串，用來儲存裝置身分識別狀態的原因。允許所有 UTF-8 字元。 |
| statusUpdateTime | 唯讀 | 上次狀態更新的日期和時間。 |
| connectionState | 唯讀 | [已連線] 或 [已中斷連線]，代表裝置連線狀態的 IoT 中心檢視。 |
| connectionStateUpdatedTime | 唯讀 | 上次更新連線狀態的日期和時間。 |
| lastActivityTime | 唯讀 | 上次連接裝置、接收或傳送訊息的日期和時間。 |

> [AZURE.NOTE]連線狀態只能代表連線狀態的 IoT 中心檢視。此狀態的顯示與否取決於網路狀況和設定。

### 裝置身分識別作業
Azure IoT 中心裝置身分識別登錄會公開下列作業︰

* 建立裝置身分識別
* 更新裝置身分識別
* 依照 ID 擷取裝置身分識別別
* 刪除裝置身分識別
* 列出多達 1000 個識別

上述所有作業都允許如 [RFC7232][lnk-rfc7232] 中指定的使用開放式並行存取。

> [AZURE.IMPORTANT]要擷取中心的身分識別登錄中所有身分識別的唯一方法是使用[匯出](#importexport)功能。

中心的裝置身分識別登錄不包含任何應用程式中繼資料，可如同字典使用 **deviceId** 做為金鑰存取，而且不支援易懂的查詢。任何 IoT 解決方案都必須有解決方案特定存放區，其中包含應用程式特定中繼資料，例如在智慧型建築解決方案中已部署溫度感應器的空間。

### 停用裝置
您可藉由更新登錄中身分識別的 [狀態] 屬性來停用裝置。通常這使用於兩個案例中：

1. 在佈建協調程序期間。如需詳細資訊，請參閱 [Azure IoT 中心指引 - 佈建][lnk-guidance-provisioning]。
2. 如有任何原因而讓您考慮使用遭到入侵或暫時未經授權的裝置。

### 匯出裝置識別 <a id="importexport"></a>
匯出是長時間執行的工作，其使用客戶提供的 Blob 容器來讀取和寫入裝置身分識別資料。

您可以從 IoT 中心的身分識別登錄大量匯出裝置身分識別。這可透過 Azure IoT 中心資源提供者端點 ([端點](#endpoints)) 上的非同步作業來完成。

以下是可能對匯出工作執行的作業︰

* 建立匯出工作
* 擷取執行中工作的狀態
* 取消執行中的工作

> [AZURE.NOTE]在任何指定的時間，每個中心只可以有一項執行中的工作。

如須匯入和匯出 API 的詳細資訊，請參閱 [Azure IoT 中心 - 資源提供者 API][lnk-resource-provider-apis]。

#### 作業
所有匯出工作都具有下列屬性︰

| 屬性 | 選項 | 說明 |
| -------- | ------- | ----------- |
| jobId | 由系統產生，建立時略過 | |
| creationTime | 由系統產生，建立時略過 | |
| endOfProcessingTime | 由系統產生，建立時略過 | |
| 類型 | 唯讀 | **匯出** |
| status | 由系統產生，建立時略過 | [已加入佇列]、[已啟動]、[已完成]、[失敗] |
| progress | 由系統產生，建立時略過 | 完成百分比的整數值。 |
| outputBlobContainerURI | 所有工作都需要 | 具有 Blob 容器寫入權限的 Blob 共用存取簽章 URI (請參閱 [透過 Blob 服務建立與使用 SAS][lnk-createuse-sas]，以及[在 Java 中建立共用存取簽章][lnk-sas-java])。這用來輸出工作的狀態和結果。 |
| includeKeysInExport | 選用 | 若為 **true**，則金鑰會包含在匯出輸出中；否則將金鑰匯出為 null。預設值︰**false**。 |
| failureReason | 由系統產生，建立時略過 | 如果狀態為 [失敗]，則為包含原因的字串。 |

#### 匯出工作
匯出工作會以 Blob 共用存取簽章 URI 做為參數，以授與 Blob 容器的寫入存取權。這用來輸出作業的結果。

輸出結果會在名為 `job_{job_id}_devices.txt` 的檔案中寫入指定的 Blob 容器中。這個檔案將包含已序列化為[裝置身分識別屬性](#deviceproperties)中所指定 JSON 的裝置身分識別。萬一 **includeKeysInExport** 設定為 **false**，安全性資料將會設定為 **null**。

**範例**：

    {"deviceId":"devA","auth":{"symKey":{"primaryKey":"123"}},"status":"enabled"}
    {"deviceId":"devB","auth":{"symKey":{"primaryKey":"234"}},"status":"enabled"}
    {"deviceId":"devC","auth":{"symKey":{"primaryKey":"345"}},"status":"enabled"}
    {"deviceId":"devD","auth":{"symKey":{"primaryKey":"456"}},"status":"enabled"}


## 安全性 <a id="security"></a>

本節說明用來保護 Azure IoT 中心的選項。

### 存取控制 <a id="accesscontrol"></a>
Azure IoT 中心會使用下列權限來授與每個中心端點的存取權。權限可根據功能限制 IoT 中心的存取權。

* **RegistryRead**。此權限可授與裝置身分識別登錄的讀取權限。如需詳細資訊，請參閱[裝置身分識別登錄](#identityregistry)。
* **RegistryReadWrite**。此權限可授與裝置身分識別登錄的讀取和寫入權限。如需詳細資訊，請參閱[裝置身分識別登錄](#identityregistry)。
* **ServiceConnect**。此權限可授與雲端服務面向通訊和監視端點的存取權。例如，它會授與雲端服務的權限，以接收裝置到雲端的訊息、傳送雲端到裝置的訊息以及擷取對應的傳遞通知。
* **DeviceConnect**。此權限可授與裝置面向通訊端點的存取權。例如，它會授與傳送裝置到雲端的訊息和接收雲端到裝置的訊息的權限。此權限最常由裝置使用。

授與權限的方式如下：

* **中心層級的共用存取原則**。共用存取原則可以授與上面所列權限的任意組合。您可以在 [Azure 管理入口網站][lnk-management-portal]中定義原則或以程式設計方式使用 [Azure IoT 中心資源提供者 API][lnk-resource-provider-apis]。新建立的 IoT 中心有下列預設原則︰
    - *iothubowner*︰具備所有權限的原則，
    - *service*︰具備 **ServiceConnect** 權限的原則，
    - *device*︰具備 **DeviceConnect** 權限的原則，
    - *registryRead*︰具備 **RegistryRead** 權限的原則，
    - *registryReadWrite*︰具備 **RegistryRead** 和 **RegistryWrite** 權限的原則，
* **各裝置的安全性認證**。每個 IoT 中心都包含[裝置身分識別登錄](#identityregistry)。對於此登錄中的每個裝置，您可以設定安全性認證，以授與範圍設為對應裝置端點的 **DeviceConnect** 權限。

**範例**。在 IoT 解決方案，通常有使用 *registryReadWrite* 原則的裝置管理元件，以及事件處理器元件和使用*service* 原則的執行階段裝置商務邏輯元件。個別裝置會使用中心身分識別登錄內儲存的認證進行連接。

如需 IoT 中心安全性主題的指引，請參閱 [Azure IoT 中心指引][lnk-guidance-security]的安全性章節。

### 驗證
Azure IoT 中心可根據共用存取原則和裝置身分識別登錄安全性認證驗證權杖，以授與端點的存取權。安全性認證 (例如對稱金鑰) 決不會在網路上傳送。

> [AZURE.NOTE]如同 [Azure 資源管理員][lnk-azure-resource-manager]中的所有提供者，Azure IoT 中心資源提供者是透過您的 Azure 訂用帳戶進行保護。

#### 安全性權杖格式 <a id="tokenformat"></a>
安全性權杖具有下列格式：

        SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}

以下是預期的值：

| 值 | 說明 |
| ----- | ----------- |
| {signature} | 字串的 HMAC-SHA256 簽章：`{URL-encoded-resourceURI} + "\n" + expiry` |
| {resourceURI} | 可使用此權杖存取之端點的 URI 前置詞 (由區段)。例如，`/events` |
| {expiry} | 從新紀元時間 (Epoch) 1970 年 1 月 1日 00:00:00 UTC 時間至今秒數的 UTF8 字串。 |
| {URL-encoded-resourceURI} | URL 編碼的資源 URI (小寫) |
| {policyName} | 此權杖所參考的共用存取原則名稱。在權杖參考裝置登錄認證的情況下不存在。 |

**前置詞的注意事項**︰URI 前置詞是依區段 (而不是依字元) 計算。例如 `/a/b` 是 `/a/b/c` 的前置詞，但不是`/a/bc` 的前置詞。

#### 通訊協定詳細規格
每個支援的通訊協定 (例如 AMQP 和 HTTP) 會以不同的方式傳輸權杖。

在 [授權] 要求標頭中包含有效的權杖，即可實作 HTTP 驗證。稱為 [授權] 的查詢參數也可以傳輸權杖。

使用 [AMQP][lnk-amqp] 時，Azure IoT 中心會支援 [SASL PLAIN][lnk-sasl-plain] 和[以 AMQP 宣告為基礎的安全性][lnk-cbs]。

在以 AMQP 宣告為基礎的安全性中，標準會指定如何傳輸上面所列的權杖。

對於 SASL PLAIN，使用者名稱可以是︰在中心層級權杖的情況中為 * `{policyName}&commat;sas.root.{iothubName}`。在裝置範圍權杖的情況中為 * `{deviceId}`。

在這兩種情況下，密碼欄位都包含[權杖格式](#tokenformat)一節中所述的權杖。

> [AZURE.NOTE] [Azure IoT 中心 SDK][lnk-apis-sdks] 會在連接至服務時自動產生權杖。在某些情況下，SDK 在其所支援的通訊協定或可用的驗證方法中會受限。如需詳細資訊，請參閱 [Azure IoT 中心 SDK][lnk-apis-sdks] 文件。

#### 相較於 CBS 的 SASL PLAIN
使用 SASL PLAIN 時，連接至 IoT 中心的用戶端可為每個 TCP 連線使用單一權杖。當權杖到期時，TCP 連線會中斷與服務的連線，並觸發重新連線。此行為雖不會對應用程式後端元件造成問題，但是對裝置端應用程式極為不利，其原因如下︰

*  閘道器通常會代表許多裝置連線。使用 SASL PLAIN 時，它們必須針對連接至 IoT 中心的每個裝置不同的建立 TCP 連線。這會大幅提高電力與網路資源的耗用量並增加每個裝置連線的延遲。
* 在每個權杖到期後，增加使用要重新連接的資源通常會影響資源受限的裝置。

### 設定中心層級認證的範圍
使用受限制的資源 URI 建立權杖，可以設定中心層級安全性原則的範圍。例如，要從裝置傳送裝置到雲端訊息的端點是 `/devices/{deviceId}/events`。您也可以使用中心層級的共用存取原則搭配 **DeviceConnect** 權限來簽署其 resourceURI 為 `/devices/{deviceId}` 的權杖，並建立只能代表裝置 **deviceId** 傳送訊息的權杖。

這個機制類似於[事件中樞發佈者原則][lnk-event-hubs-publisher-policy]，能夠如 [Azure IoT 中心指引][lnk-guidance-security]的安全性一節所述實作自訂驗證方法。

## 訊息
Azure IoT 中心提供傳訊基本類型，以進行從應用程式後端 (服務或雲端) 至裝置的通訊，反之亦然。我們將這些功能稱為[裝置到雲端](#d2c)和[雲端到裝置](#c2d)。

IoT 中心傳訊功能的核心屬性是訊息的可靠性和持久性。這可在裝置端上恢復間歇性連線，以及在雲端恢復事件處理的負載尖峰。IoT 中心會對 D2C 和 C2D 傳訊實作至少一次傳遞保證。

IoT 中心支援多個裝置面向通訊協定 (例如 AMQP 和 HTTP/1)。為了支援完美的跨通訊協定互通性，IoT 中心定義了所有裝置面向通訊協定均可支援的通用訊息格式。

### 訊息格式 <a id="messageformat"></a>
IoT 中心訊息包含︰

* 一組系統屬性。這些是由 IoT 中心所解譯或設定的屬性。這個集合是預先決定的。
* 一組應用程式屬性。這是應用程式可以定義的字串屬性字典，而不需將本文還原序列化即可加以存取。IoT 中心永遠不會修改這些屬性。
* 不透明的二進位主體。

如需有關如何在不同的通訊協定中將訊息編碼，請參閱 [IoT 中心 API 和 SDK][lnk-apis-sdks]。

這是 IoT 中心訊息中的系統屬性集合。

| 屬性 | 說明 |
| -------- | ----------- |
| MessageId | 使用者為訊息設定的識別碼，通常用於要求-回覆模式。格式︰ASCII 7 位元英數字元 + `{'-', ':',’.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}` 的區分大小寫字串 (最長為 128 個字元)。 |
| 序號 | IoT 中心指派給每則 C2D 訊息的數字 (每一裝置-佇列特有)。 |
| 收件人 | 在[雲端到裝置](#c2d)中使用於訊息的目的地。|
| ExpiryTimeUtc | 訊息到期的日期和時間。 |
| EnqueuedTime | IoT 中心收到訊息的時間。 |
| CorrelationId | 通常包含要求-回覆模式中要求的訊息識別碼的字串屬性。 |
| UserId | 用來指定訊息的來源。當 IoT 中心產生訊息時，它會設定為 `{iot hub name}`。 |
| Ack | 使用於 C2D 訊息，要求 IoT 中心因為裝置取用訊息而產生意見反應訊息。可能的值︰**無** (預設值)︰不會產生任何意見反應訊息，**正數**︰如果訊息已完成，則會收到意見反應訊息，**負數**︰如果訊息未由裝置完成就到期 (或已達到最大傳遞計數) 則會收到意見反應訊息，**完整**︰正數和負數。若需詳細資訊，請參閱[訊息意見反應](#feedback)。 |
| ConnectionDeviceId | 由 IoT 中心設定於 D2C 訊息上。它包含傳送訊息之裝置的 deviceId。 |
| ConnectionDeviceGenerationId | 由 IoT 中心設定於 D2C 訊息上。它包含傳送訊息之裝置的 generationId (依據[裝置身分識別屬性](#deviceproperties))。 |
| ConnectionAuthMethod | 由 IoT 中心設定於 D2C 訊息上。用來驗證傳送訊息之裝置的驗證方法的相關資訊。如需詳細資訊，請參閱 [D2C 反詐騙](#antispoofing)。|

### 選擇您的通訊協定 <a id="amqpvshttp"></a>
Iot 中心同時支援 [AMQP][lnk-amqp] 和 HTTP/1 通訊協定進行裝置端通訊。以下是有關其用途的考量清單。

* **雲端到裝置的模式**。HTTP/1 沒有有效的方式可實作伺服器發送。因此，使用 HTTP/1 時，裝置會向 Azure IoT 中心輪詢雲端到裝置的訊息。這對於裝置和 IoT 中心而言都非常沒有效率。在目前的指導方針中，使用 HTTP/1 時，每個裝置的輪詢間隔會設定為小於每 25 分鐘一次。另一方面，AMQP 在接收雲端到裝置的訊息時支援伺服器發送，而且能立即將訊息從 IoT 中心發送至裝置。如果傳遞延遲是一大考量，最好使用 AMQP 通訊協定。另一方面，HTTP/1 也適用於幾乎不連接的裝置。
* **現場閘道器**。假設伺服器發送有相關的 HTTP/1 限制，則不適合用於[現場閘道器案例][lnk-azure-gateway-guidance]。
* **低資源裝置**。HTTP/1 程式庫比 AMQP 程式庫小得多。因此，裝置擁有的資源很少 (例如，小於 1 Mb RAM)，HTTP/1 可能是唯一可用的通訊協定實作。
* **網路周遊**。AMQP 標準會在連接埠 5672 上接聽。這可能會導致對非 HTTP 通訊協定關閉的網路發生問題。請注意，這項限制是暫時的，因為 IoT 中心將透過 WebSockets 實作 AMQP。
* **承載大小**。AMQP 是明顯比 HTTP/1 更為精簡的二進位通訊協定。

總括來說，我們建議盡可能使用 AMQP，而如果裝置資源或網路設定不允許 AMQP，則使用 HTTP/1。此外，使用 HTTP/1 時，每個裝置的輪詢頻率應設定為小於每 25 分鐘一次。很顯然在開發期間，可接受更頻繁的輪詢頻率。

最後請務必參考 [Azure IoT 通訊協定閘道器][lnk-azure-protocol-gateway]，該文可讓您部署直接與 IoT 中心接合的高效能 MQTT 閘道器。MQTT 支援伺服器發送 (因此能夠將 C2D 訊息立即傳遞到裝置)，而且適用於資源非常少的裝置。這個方法的主要缺點是需要自我裝載和管理通訊協定閘道器。

### 裝置到雲端 <a id="d2c"></a>
如[端點](#endpoints)一節所詳述，裝置到雲端的訊息會透過裝置面向端點 (特別是 `/devices/{deviceId}/messages/events`) 傳送，以及透過與[事件中樞][lnk-event-hubs]相容的服務面向端點 (`/messages/events`) 接收。因此，您可以使用標準事件中樞整合和 SDK 來接收訊息。

Azure IoT 中心實作裝置到雲端傳訊的方式非常類似於[事件中樞][lnk-event-hubs]，而比起[服務匯流排][lnk-servicebus]訊息，IoT 中心的裝置到雲端訊息更類似於事件中樞的事件。

這有下列含意：

* 與事件中樞事件類似，裝置到雲端訊息都會長期保留 IoT 中心中最多 7 天 (請參閱 [裝置到雲端的設定選項](#d2cconfiguration))。
* 裝置到雲端的訊息會分割於建立時所設定的一組固定資料分割中 (請參閱[裝置到雲端的設定選項](#d2cconfiguration))。
* 與事件中樞類似，讀取裝置到雲端訊息的用戶端必須處理資料分割和檢查點，請參閱[事件中樞 - 取用事件][lnk-event-hubs-consuming-events]。
* 如同事件中樞的事件，裝置到雲端的訊息最大可能為 256 Kb，而且可分成數個批次以最佳化傳送。批次大小最大可能為 256 Kb，最多包含 500 則訊息。

不過，IoT 中心裝置到雲端與事件中樞之間還有一些重要差異：

* 如[安全性](#security)一節所述，IoT 中心允許每一裝置的驗證和存取控制。
* IoT 中心允許同時連接數百萬個裝置 (請參閱[配額和節流](#throttling))，而事件中樞則受限於每個命名空間 5000 個 AMQP 連線。
* IoT 中心不允許使用 **PartitionKey** 任意進行資料分割。裝置到雲端的訊息會根據其原始的 **deviceId** 進行分割。
* 調整 IoT 中心與事件中樞稍微不同，如需詳細資訊，請參閱 Azure IoT 中心指引的[調整][lnk-guidance-scale]一節。

請注意，以上說明並不表示 IoT 中心可以替代所有案例中的事件中樞。例如，在某些事件處理運算中，有可能需要在分析資料串流之前，重新分割有關不同屬性或欄位的事件。在此情況下，事件中樞可用來解離串流處理管線的兩個部分。

如需如何使用裝置到雲端訊息的詳細資訊，請參閱 [IoT 中心 API 和 SDK][lnk-apis-sdks]。

#### 非遙測流量
在許多情況下，裝置不只會將遙測資料點傳送到應用程式後端，也會傳送需要從應用程式商務邏輯層執行和處理的互動式訊息和要求。具代表性的範例包括必須在後端觸發特定動作的重要警示，或命令的裝置回覆。

如需有關處理此種訊息的最佳方式詳細資訊，請參閱 IoT 中心指引的[裝置到雲端的處理][lnk-guidance-d2c-processing]一節。

#### 裝置到雲端的設定選項 <a id="d2cconfiguration"></a>
IoT 中心會公開下列屬性以控制 D2C 傳訊。

* **資料分割計數**。此屬性可在建立時設定，以定義 D2C 事件擷取的資料分割數目。
* **保留時間**。此屬性指定裝置到雲端訊息的保留時間。其預設值為 1 天，但可提高至 7。

而且，與事件中樞類似，IoT 中心允許管理裝置到雲端的接收端點上的取用者群組。

上述所有屬性均可透過 [Azure 管理入口網站][lnk-management-portal]或以程式設計方式透過 [Azure IoT 中心 - 資源提供者 API][lnk-resource-provider-apis] 修改。

#### 防詐騙屬性 <a id="antispoofing"></a>
為了避免裝置到雲端的訊息中出現裝置詐騙，IoT 中心使用下列屬性在所有訊息上加上戳記：

* **ConnectionDeviceId**
* **ConnectionDeviceGenerationId**
* **ConnectionAuthMethod**

前兩個屬性包含來源裝置的 **deviceId** 和 **generationId** (依據[裝置身分識別屬性](#deviceproperties))。**ConnectionAuthMethod** 屬性包含具有下列屬性的 JSON 序列化物件︰

    {
        "scope": "{ hub | device}",
        "type": "{ symkey | sas}",
        "issuer": "iothub"
    }

### 雲端到裝置 <a id="c2d"></a>
如[端點](#endpoints)一節所詳述，雲端到裝置的訊息會透過服務面向端點 (`/messages/devicebound`) 傳送，以及透過一系列裝置面向端點 (`/devices/{deviceId}/messages/devicebound`) 接收。

每個雲端到裝置的訊息都是以單一裝置為目標，並將 [to] 屬性設定為 `/devices/{deviceId}/messages/devicebound`。

**重要事項**︰每個裝置佇列最多可以保留 50 個雲端到裝置的訊息。嘗試將更多訊息傳送至相同的裝置會導致錯誤。

#### 訊息生命週期 <a id="message lifecycle"></a>
為了實作至少一次傳遞保證，雲端到裝置的訊息會保存在每一裝置的佇列中，而裝置必須明確地認可完成，IoT 中心才能夠從佇列中移除訊息。這保證連線失敗和裝置故障能夠恢復。

下圖詳細說明雲端到裝置訊息的生命週期狀態圖。

![雲端到裝置的訊息生命週期][img-lifecycle]

當服務傳送訊息時，該訊息會被視為 [已加入佇列]。當裝置想要接收訊息時，IoT 中心會鎖定此訊息 ([不可見]狀態)，以便讓相同裝置上的其他執行緒開始接收其他訊息。當裝置執行緒完成裝置的處理時，它會藉由完成訊息來通知 IoT 中心。它也可以決定拒絕此訊息，將訊息傳送至 [寄不出去] 狀態，或決定放棄，將訊息放回佇列中 ([已加入佇列] 狀態)。

因為執行緒可能無法處理訊息，而且不會通知 IoT 中心，所以在 [可見性 (或鎖定) 逾時] (預設值︰1 分鐘) 之後，訊息將會自動從 [不可見] 轉換回 [已加入佇列]。訊息可以在 [已加入佇列] 與 [不可見] 狀態之間轉換的次數，以在 IoT 中心上的 [最大傳遞計數] 屬性中指定的次數為限。在該轉換次數之後，訊息將會自動停止傳送。同樣地，訊息會在其到期時間之後自動停止傳送 (請參閱[存留時間](#ttl))。

請參閱[開始使用 Azure IoT 中心雲端到裝置的訊息][lnk-getstarted-c2d-tutorial]以取得雲端到裝置訊息的教學課程，以及參閱 [IoT 中心 API 和 SDK][lnk-apis-sdks] 以取得不同的 API 和 SDK 如何公開雲端到裝置功能的參考資訊。

> [AZURE.NOTE]通常只要遺失訊息不會影響應用程式邏輯，就應該完成雲端到裝置的訊息。這種情形可能會發生於許多不同的案例中，例如：訊息內容已成功保存在本機儲存體，或已成功執行作業，或訊息帶有遺失時不會影響應用程式功能的暫時性資訊。有時候，對於長時間執行的工作，通常會在本機儲存體上保存工作描述之後完成雲端到裝置的訊息，然後在工作進度的各個階段以一或多則裝置到雲端的訊息通知應用程式後端。

#### 存留時間 <a id="ttl"></a>
每個雲端到裝置的訊息都有到期時間。這可以由服務 (在 **ExpiryTimeUtc** 屬性中) 明確設定，或由 IoT 中心使用指定為 IoT 中心屬性的預設存留時間來設定，請參閱[雲端到裝置的設定選項](#c2dconfiguration)。

#### 訊息意見反應 <a id="feedback"></a>
傳送雲端到裝置的訊息時，服務可以要求傳遞每則訊息的意見反應 (關於該訊息的最終狀態)。將 **Ack** 屬性設定為 [正數] 時，只有在雲端到裝置的訊息達到 [已完成] 狀態，IoT 中心才會產生意見反應訊息。將 **Ack** 屬性設定為 [負數] 時，只有在雲端到裝置的訊息達到 [寄不出去] 狀態，IoT 中心才會產生意見反應訊息。將 **Ack** 屬性設定為 [完整]，IoT 中心即可在任一情況下產生意見反應訊息。

如[端點](#endpoints)所詳述，意見反應會透過服務面向的端點 (`/messages/servicebound/feedback`) 做為訊息傳遞。對於具有相同 [訊息生命週期](#訊息生命週期) 的雲端到裝置的訊息，意見反應的接收語意相同。可能的話，訊息意見反應會放入單一訊息中，其格式如下。

從意見反應端點擷取的每則訊息具有下列屬性。

| 屬性 | 說明 |
| -------- | ----------- |
| EnqueuedTime | 建立批次時的時間戳記。 |
| UserId | `{iot hub name}` |
| ContentType | `application/vnd.microsoft.iothub.feedback.json` |

主體是記錄的 JSON 序列化陣列，而每筆記錄都具有下列屬性︰

| 屬性 | 說明 |
| -------- | ----------- |
| EnqueuedTime | 訊息的出現結果時的時間戳記。例如，完成已裝置或訊息已到期。 |
| CorrelationId | 與此意見反應資訊相關的雲端到裝置訊息的 **MessageId**。 |
| StatusCode | 如果成功則為 **0**，如果訊息到期則為 **1**，如果超過最大傳遞計數則為 **2**，如果訊息遭到拒絕則為 **3**。 |
| 說明 | 上述結果的字串值。 |
| DeviceId | 與此意見反應相關之雲端到裝置訊息的目標裝置的 **DeviceId**。 |
| DeviceGenerationId | 與此意見反應相關之雲端到裝置訊息的目標裝置的 **DeviceGenerationId**。 |

**重要事項**。服務必須指定雲端到裝置訊息的 **MessageId**，才能使其意見反應與原始訊息相互關聯。

**範例**。以下是意見反應訊息的主體範例。

    [
        {
            "CorrelationId": "0987654321",
            "EnqueuedTime": "2015-07-28T16:24:48.789Z",
            "StatusCode": "0",
            "Description": "Success",
            "DeviceId": "123",
            "DeviceGenerationId": "abcdefghijklmnopqrstuvwxyz"
        },
        {
            ...
        },
        ...
    ]

#### 雲端到裝置的設定選項 <a id="c2dconfiguration"></a>
每個 IoT 中心都會針對雲端到裝置傳訊公開下列設定選項。

| 屬性 | 說明 | 範圍和預設值 |
| -------- | ----------- | ----------------- |
| defaultTtlAsIso8601 | 雲端到裝置訊息的預設 TTL。 | ISO\_8601 間隔高達 2D (最小為 1 分鐘)。預設值︰1 小時。 |
| maxDeliveryCount | 每個裝置佇列的雲端到裝置最大傳遞計數。 | 1 到 100。預設值：10 |
| feedback.ttlAsIso8601 | 保留服務繫結的意見反應訊息。 | ISO\_8601 間隔高達 2D (最小為 1 分鐘)。預設值︰1 小時。 |
| feedback.maxDeliveryCount | 意見反應佇列的最大傳遞計數。 | 1 到 100。預設值 = 100。 |


## 配額和節流 <a id="throttling"></a>
每個 Azure 訂用帳戶最多可以有 10 個 IoT 中心。

每個 IoT 中心都會以特定 SKU 中特定數目的單位進行佈建 (如需詳細資訊，請參閱 [Azure IoT 中心價格][lnk-pricing])。SKU 和單位數目可決定可以傳送的每日訊息配額上限以及身分識別登錄中的裝置身分識別數目上限。同時連接的裝置數目受限於登錄中的識別數目。

它們也可以決定 IoT 中心將對作業強制執行的節流限制。

### 裝置身分識別登錄配額
IoT 中心允許每天每個單位 (不管 SKU) 最多有 1100 個裝置更新 (建立、更新、刪除)。

### 作業節流
作業節流是在分鐘範圍內套用的速率限制，主要是為了避免不當使用。IoT 中心會試著儘可能避免傳回錯誤，但如果違反節流太久，就會開始傳回例外狀況。以下是強制執行的節流清單。個別中心的值如下。

| 節流 | 每個中心的值 |
| -------- | ------------- |
| 身分識別登錄作業 (建立、擷取、列出、更新、刪除)，個別或大量匯入/匯出 | 100/分鐘/單位，最高 5000/分鐘 |
| 裝置連線 | 100/秒/單位 |
| D2C 傳送 | 2000/分鐘/單位 (適用於 S2)，60/分鐘/單位 (適用於 S1)。最小值為 100/秒。 |
| C2D 作業 (傳送、接收、意見反應) | 100/分鐘/單位 |

**注意**。在任何指定的時間，都可能藉由增加 IoT 中心佈建的單位來增加配額或節流限制。

## 後續步驟

您現已了解開發 IoT 中心的概觀，請遵循下列連結深入了解：

- [開始使用 IoT 中心 (教學課程)][lnk-get-started]
- [作業系統平台與硬體相容性][lnk-compatibility]
- [規劃 IoT 實作][lnk-guidance]

[事件中樞 - 事件處理器主機]: http://blogs.msdn.com/b/servicebus/archive/2015/01/16/event-processor-host-best-practices-part-1.aspx

[Azure Preview 入口網站]: https://ms.portal.azure.com

[img-summary]: media/iot-hub-devguide/summary.png
[img-endpoints]: media/iot-hub-devguide/endpoints.png
[img-lifecycle]: media/iot-hub-devguide/lifecycle.png
[img-eventhubcompatible]: media/iot-hub-devguide/eventhubcompatible.png

[lnk-compatibility]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/tested_configurations.md
[lnk-apis-sdks]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-azure-hub-sdks]: https://msdn.microsoft.com/library/mt488521.aspx
[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[lnk-resource-provider-apis]: https://msdn.microsoft.com/library/mt548492.aspx
[lnk-reference-architecture]: iot-hub-what-is-azure-iot.md

[lnk-azure-gateway-guidance]: iot-hub-guidance.md#fieldgateways
[lnk-guidance-provisioning]: iot-hub-guidance.md#provisioning
[lnk-guidance-scale]: iot-hub-guidance.md#scale
[lnk-guidance-security]: iot-hub-guidance.md#customauth

[lnk-azure-protocol-gateway]: iot-hub-protocol-gateway.md
[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-guidance]: iot-hub-guidance.md
[lnk-getstarted-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md

[lnk-amqp]: https://www.amqp.org/
[lnk-arm]: https://azure.microsoft.com/zh-TW/documentation/articles/resource-group-overview/
[lnk-azure-resource-manager]: https://azure.microsoft.com/zh-TW/documentation/articles/resource-group-overview/
[lnk-cbs]: https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc
[lnk-createuse-sas]: https://azure.microsoft.com/zh-TW/documentation/articles/storage-dotnet-shared-access-signature-part-2/
[lnk-event-hubs-publisher-policy]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-99ce67ab
[lnk-event-hubs]: http://azure.microsoft.com/services/event-hubs/
[lnk-event-hubs-consuming-events]: https://azure.microsoft.com/zh-TW/documentation/articles/event-hubs-programming-guide/#consume-events-event-consumers
[lnk-guidance-d2c-processing]: iot-hub-csharp-csharp-process-d2c.md
[lnk-management-portal]: https://portal.azure.com
[lnk-rfc7232]: https://tools.ietf.org/html/rfc7232
[lnk-sas-java]: https://msdn.microsoft.com/zh-TW/library/azure/Hh875756.aspx
[lnk-sasl-plain]: http://tools.ietf.org/html/rfc4616
[lnk-servicebus]: http://azure.microsoft.com/services/service-bus/
[lnk-tls]: https://tools.ietf.org/html/rfc5246

<!---HONumber=Oct15_HO1-->