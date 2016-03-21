<properties
 pageTitle="IoT 中樞開發人員指南主題 | Microsoft Azure"
 description="Azure IoT 中樞開發人員指南包含 IoT 中樞端點、安全性、裝置身分識別登錄和傳訊"
 services="iot-hub"
 documentationCenter=".net"
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="02/03/2016"
 ms.author="dobett"/>

# Azure IoT 中樞開發人員指南

Azure IoT 中心是一項完全受管理的服務，可讓數百萬個 IoT 裝置和一個應用程式後端進行可靠且安全的雙向通訊。

Azure IoT 中樞能夠︰

* 使用每一裝置的安全性認證和存取控制來保護通訊的安全。
* 提供可靠的裝置到雲端和雲端到裝置的超大規模傳訊。
* 透過最受歡迎的語言和平台的裝置程式庫，進行簡單的裝置連線。

本文章涵蓋下列主題：

- [端點](#endpoints)。本節說明每個 IoT 中樞針對執行階段和管理作業所公開的各種端點。
- [裝置身分識別登錄](#device-identity-registry)。本節說明各 IoT 中樞的裝置身分識別登錄所儲存的資訊，以及您可以存取和修改資訊的方式。
- [安全性](#security)。本節說明用來將存取權授與裝置和雲端元件之 IoT 中樞功能的安全性模型。
- [傳訊](#messaging)。本節說明 IoT 中樞所公開的傳訊功能 (裝置到雲端和雲端到裝置)。
- [配額和節流](#throttling)。本節摘要說明適用於您的 IoT 中樞的使用配額。

## 端點 <a id="endpoints"></a>

Azure IoT 中樞是多租用戶服務，且公開功能給各種執行者。下圖顯示 IoT 中樞公開的各種端點。

![IoT 中樞端點][img-endpoints]

以下是端點的說明︰

* **資源提供者**：IoT 中樞資源提供者會公開 [Azure Resource Manager][lnk-arm] 介面，可讓 Azure 訂用帳戶擁有者建立 IoT 中樞、更新 IoT 中樞屬性及刪除 IoT 中樞。IoT 中樞屬性可管理中心層級的安全性原則，相對於裝置層級的存取控制 (請參閱下面的[存取控制](#accesscontrol)) 和雲端到裝置和裝置到雲端傳訊的功能選項。資源提供者也可讓您[匯出裝置身分識別](#importexport)。
* 裝置身分識別管理：每個 IoT 中樞會公開一組用來管理裝置身分識別的 HTTP REST 端點 (建立、擷取、更新和刪除)。裝置識別用於裝置驗證和存取控制。如需詳細資訊，請參閱[裝置身分識別登錄](#device-identity-registry)。
* 裝置端點：針對裝置身分識別登錄中所佈建的各個裝置，IoT 中樞會公開裝置可用來傳送並接收訊息的一組端點：
    - 傳送裝置到雲端的訊息。使用這個端點傳送裝置到雲端的訊息。如需詳細資訊，請參閱[裝置到雲端傳訊](#d2c)。
    - 接收雲端到裝置的訊息。使用此端點來接收目標雲端到裝置訊息的裝置。如需詳細資訊，請參閱[雲端到裝置傳訊](#c2d)。

    這些端點會使用 HTTP、[MQTT][lnk-mqtt] 和 [AMQP][lnk-amqp] 通訊協定加以公開。請注意，AMQP 也可透過連接埠 443 上的 [WebSockets][lnk-websockets] 來取得。
* 服務端點：各 IoT 中樞會公開您的應用程式後端可用來與您的裝置通訊的一組端點。目前僅使用 [AMQP][lnk-amqp] 通訊協定公開這些端點。
    - 接收裝置到雲端的訊息。此端點與 [Azure 事件中樞][lnk-event-hubs]相容，且後端服務可用它來讀取由您的裝置所傳送的所有裝置到雲端訊息。如需詳細資訊，請參閱[裝置到雲端傳訊](#d2c)。
    - 傳送雲端到裝置的訊息及接收傳遞通知。這些端點可讓您的應用程式後端傳送可靠的雲端到裝置訊息，以及接收相對應的傳遞或到期通知。如需詳細資訊，請參閱[雲端到裝置傳訊](#c2d)。

[IoT 中樞 API 和 SDK][lnk-apis-sdks]一文說明您可用來存取這些端點的各種方式。

最後請務必注意，所有的 IoT 中樞端點都使用 [TLS][lnk-tls] 通訊協定，且絕不會在未加密/不安全的通道上公開任何端點。

### 如何讀取事件中樞相容端點。<a id="eventhubcompatible"></a>

使用[適用於 .NET 的 Azure 服務匯流排 SDK](https://www.nuget.org/packages/WindowsAzure.ServiceBus) 或[事件中樞 - 事件處理器主機][]時，您可以使用任何 IoT 中樞連接字串搭配正確的權限，然後使用訊息/事件做為事件中樞名稱。

使用未能察覺 IoT 中樞的 SDK (或產品整合) 時，您必須從 [Azure 入口網站][]的 IoT 中樞設定中，擷取事件中樞相容端點和事件中樞名稱︰

1. 在 IoT 中樞刀鋒視窗中，按一下 [設定]，然後按一下 [傳訊]。
2. 在 [裝置到雲端的設定] 區段中，您會發現 [事件中樞相容端點]、[事件中樞相容名稱] 和 [資料分割] 值。

    ![][img-eventhubcompatible]

> [AZURE.NOTE] SDK 有時需要 [主機名稱] 或 [命名空間] 值。在此情況下，請從 [事件中樞相容端點] 移除配置。比方說，如果您的事件中樞相容端點為 ****sb://iothub-ns-myiothub-1234.servicebus.windows.net/**，主機名稱會是 iothub-ns-myiothub-1234.servicebus.windows.net，而命名空間會是 iothub-ns-myiothub-1234。

然後，您可以使用具有 ServiceConnect 權限的任何共用存取安全性原則，連接至指定的事件中樞。

如果您需要建置使用先前資訊的事件中樞連接字串，請使用下列模式：

```
Endpoint={Event Hub-compatible endpoint};SharedAccessKeyName={iot hub policy name};SharedAccessKey={iot hub policy key}
```

以下是您可以搭配 IoT 中樞公開之事件中樞相容端點使用之 SDK 和整合項目的清單：

* [Java 事件中樞用戶端](https://github.com/hdinsight/eventhubs-client)
* [Apache Storm Spout](../hdinsight/hdinsight-storm-develop-csharp-event-hub-topology.md)。您可以檢視 GitHub 上的 [Spout 原始檔](https://github.com/apache/storm/tree/master/external/storm-eventhubs)。
* [Apache Spark 整合](../hdinsight/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming.md)

## 裝置身分識別登錄

每個 IoT 中樞都有一個裝置身分識別登錄，您可用來在服務中建立各裝置的資源 (例如含有傳送中雲端到裝置訊息的佇列)，以及允許存取如[存取控制](#accesscontrol)一節所述的裝置面向端點。

總括來說，裝置身分識別登錄是支援 REST 的裝置身分識別資源集合。下列各節將詳細說明裝置身分識別資源屬性，以及登錄對身分識別啟用的作業。

> [AZURE.NOTE] 如需您可用來與裝置身分識別登錄互動的 HTTP 通訊協定和 SDK 的詳細資訊，請參閱 [IoT 中樞 API 和 SDK][lnk-apis-sdks]。

### 裝置身分識別屬性 <a id="deviceproperties"></a>

裝置識別會以具有下列屬性的 JSON 文件表示。

| 屬性 | 選項 | 說明 |
| -------- | ------- | ----------- |
| deviceId | 必要，只能讀取更新 | ASCII 7 位元英數字元 + `{'-', ':', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}` 的區分大小寫字串 (最長為 128 個字元)。 |
| generationId | 必要，唯讀 | 中樞產生的區分大小寫字串最長為 128 個字元。這在刪除並重建裝置時，用來區分具有相同 deviceId 的裝置。 |
| etag | 必要，唯讀 | 依據 [RFC7232][lnk-rfc7232]，此字串代表裝置身分識別的弱式 etag。|
| auth | 選用 | 包含驗證資訊和安全性資料的複合物件。 |
| auth.symkey | 選用 | 包含主要和次要金鑰 (以 base64 格式儲存) 的複合物件。 |
| status | 必要 | 可以是 [已啟用] 或 [已停用]。如果為 [已啟用]，則允許連接裝置。如果為 [已停用]，此裝置無法存取任何裝置面向的端點。 |
| statusReason | 選用 | 128 個字元的字串，用來儲存裝置身分識別狀態的原因。允許所有 UTF-8 字元。 |
| statusUpdateTime | 唯讀 | 上次狀態更新的日期和時間。 |
| connectionState | 唯讀 | [已連線] 或 [已中斷連線]，代表裝置連線狀態的 IoT 中樞檢視。**重要事項**：此欄位只應用於開發/偵錯用途。只有使用 AMQP 或 MQTT 的裝置會更新連線狀態。此外，它是以通訊協定層級的偵測 (MQTT 偵測或 AMQP 偵測) 為基礎，並且會有最多 5 分鐘的延遲。基於這些理由，其中可能會有誤判情形，例如將裝置回報為已連線，但實際上已中斷連線。 |
| connectionStateUpdatedTime | 唯讀 | 上次更新連線狀態的日期和時間。 |
| lastActivityTime | 唯讀 | 裝置上次連接、接收或傳送訊息的日期和時間。 |

> [AZURE.NOTE] 連線狀態只能代表連線狀態的 IoT 中樞檢視。根據網路狀況和組態而定，可能會延遲此狀態的更新。

### 裝置身分識別作業

IoT 中樞裝置身分識別登錄會公開下列作業︰

* 建立裝置身分識別
* 更新裝置身分識別
* 依照 ID 擷取裝置身分識別別
* 刪除裝置身分識別
* 列出多達 1000 個識別
* 將所有身分識別匯出至 Blob 儲存體
* 從 Blob 儲存體匯入身分識別

上述所有作業允許使用 [RFC7232][lnk-rfc7232] 中指定的開放式並行存取。

> [AZURE.IMPORTANT] 如果要擷取中心的身分識別登錄中的所有身分識別，唯一方法是使用[匯出](#importexport)功能。

IoT 中樞裝置身分識別登錄：

- 不包含任何應用程式中繼資料。
- 可以將 deviceId 做為索引鍵來進行存取，就像字典一樣。
- 不支援表達式查詢。

IoT 方案通常具有不同的方案專屬存放區，其中包含應用程式特定的中繼資料。例如，智慧建置方案中的解決方案專用存放區會記錄部署溫度感應器的空間。

> [AZURE.IMPORTANT] 請只將裝置身分識別登錄用於裝置管理和佈建作業。執行階段的高輸送量作業不應該仰賴在裝置身分識別登錄中執行作業。例如，在傳送命令前先檢查裝置的連線狀態就不是支援的模式。請務必檢查裝置身分識別登錄的[節流速率](#throttling)以及[裝置活動訊號][lnk-guidance-heartbeat]模式。

### 停用裝置

您可以更新登錄中身分識別的 [狀態] 屬性來停用裝置。您通常會在兩種情況下使用此功能：

- 在佈建協調程序期間。如需詳細資訊，請參閱[設計您的解決方案 - 裝置佈建][lnk-guidance-provisioning]。
- 如果因為任何原因，您認為裝置遭到入侵，或變成未經授權。

### 匯出裝置身分識別 <a id="importexport"></a>

匯出是長時間執行的作業，其使用客戶提供的 Blob 容器來儲存讀取自身分識別登錄的裝置身分識別資料。

您可以使用 [IoT 中樞資源提供者端點](#endpoints)上的非同步作業，從 IoT 中樞的身分識別登錄大量匯出裝置身分識別。

以下是可能對匯出工作執行的作業︰

* 建立匯出工作
* 擷取執行中工作的狀態
* 取消執行中的工作

> [AZURE.NOTE] 在任何指定的時間，每個中心只可以有一項執行中的工作。

如須匯入和匯出 API 的詳細資訊，請參閱 [Azure IoT 中樞 - 資源提供者 API][lnk-resource-provider-apis]。

若要深入了解如何執行匯入和匯出作業，請參閱[大量管理 IoT 中樞的裝置身分識別][lnk-bulk-identity]

### 匯出工作

所有匯出工作都具有下列屬性︰

| 屬性 | 選項 | 說明 |
| -------- | ------- | ----------- |
| jobId | 由系統產生，建立時略過 | |
| creationTime | 由系統產生，建立時略過 | |
| endOfProcessingTime | 由系統產生，建立時略過 | |
| 類型 | 唯讀 | ExportDevices |
| status | 由系統產生，建立時略過 | [已加入佇列]、[已啟動]、[已完成]、[失敗] |
| progress | 由系統產生，建立時略過 | 完成百分比的整數值。 |
| outputBlobContainerURI | 所有工作都需要 | 具有 Blob 容器寫入權限的 Blob 共用存取簽章 URI (請參閱[搭配 Blob 服務建立與使用 SAS][lnk-createuse-sas])。這用來輸出工作的狀態和結果。 |
| excludeKeysInExport | 選用 | 若為 false，則金鑰會包含在匯出輸出中；否則將金鑰匯出為 null。預設值為 false。 |
| failureReason | 由系統產生，建立時略過 | 如果狀態為 [失敗]，則為包含原因的字串。 |

匯出工作接受 Blob 共用存取簽章 URI 做為參數。這會授與 Blob 容器的寫入權限，讓工作輸出其結果。

作業會將輸出結果寫入名為 devices.txt 之檔案中指定的 Blob 容器。這個檔案包含依[裝置身分識別屬性](#deviceproperties)中所指定而序列化為 JSON 的裝置身分識別。如果 excludeKeysInExport 參數設為 true，devices.txt 檔案中的每個裝置的驗證值就會設定為 null。

範例：

```
{"id":"devA","eTag":"MQ==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"123","secondaryKey":"123"}}}
{"id":"devB","eTag":"MQ==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"123","secondaryKey":"123"}}}
{"id":"devC","eTag":"MQ==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"123","secondaryKey":"123"}}}
```

### 匯入裝置身分識別

匯入是長時間執行的作業，其使用客戶提供的 Blob 容器中的資料，將裝置身分識別資料寫入至裝置身分識別登錄。

您可以使用 [IoT 中樞資源提供者端點](#endpoints)上的非同步作業，將裝置身分識別大量匯入到 IoT 中樞的身分識別登錄。

以下是可能對匯入作業執行的作業︰

* 建立匯入作業
* 擷取執行中工作的狀態
* 取消執行中的工作

> [AZURE.NOTE] 在任何指定的時間，每個中心只可以有一項執行中的工作。

如須匯入和匯出 API 的詳細資訊，請參閱 [Azure IoT 中樞 - 資源提供者 API][lnk-resource-provider-apis]。

若要深入了解如何執行匯入和匯出作業，請參閱[大量管理 IoT 中樞的裝置身分識別][lnk-bulk-identity]

### 匯入作業

所有匯入作業都具有下列屬性︰

| 屬性 | 選項 | 說明 |
| -------- | ------- | ----------- |
| jobId | 由系統產生，建立時略過 | |
| creationTime | 由系統產生，建立時略過 | |
| endOfProcessingTime | 由系統產生，建立時略過 | |
| 類型 | 唯讀 | ImportDevices |
| status | 由系統產生，建立時略過 | [已加入佇列]、[已啟動]、[已完成]、[失敗] |
| progress | 由系統產生，建立時略過 | 完成百分比的整數值。 |
| outputBlobContainerURI | 所有工作都需要 | 具有 Blob 容器寫入權限的 Blob 共用存取簽章 URI (請參閱[搭配 Blob 服務建立與使用 SAS][lnk-createuse-sas])。這用來輸出作業的狀態。 |
| inputBlobContainerURI | 必要 | 具有 Blob 容器讀取權限的 Blob 共用存取簽章 URI (請參閱[搭配 Blob 服務建立與使用 SAS][lnk-createuse-sas])。作業會讀取裝置資訊以從此 Blob 匯入。 |
| failureReason | 由系統產生，建立時略過 | 如果狀態為 [失敗]，則為包含原因的字串。 |

匯入作業接受兩個 Blob 共用存取簽章 URI 做為參數。其中一個會授與 Blob 容器的寫入權限以讓作業輸出其狀態，另一個則會授與 Blob 容器的讀取權限以讓作業讀取其輸入資料。

作業會從名為 devices.txt 之檔案中指定的 Blob 容器讀取輸入資料。這個檔案包含依[裝置身分識別屬性](#deviceproperties)中所指定而序列化為 JSON 的裝置身分識別。您可以藉由新增 importMode 屬性覆寫每個裝置的預設匯入行為。此屬性可以接受下列其中一個值：

| importMode | 說明 |
| -------- | ----------- |
| createOrUpdate | 如果不存在具有指定識別碼的裝置，則表示是新註冊的裝置。<br/>如果裝置已存在，則會以所提供的輸入資料覆寫現有資訊，而不管 ETag 值為何。 |
| create | 如果不存在具有指定識別碼的裝置，則表示是新註冊的裝置。<br/>如果裝置已存在，則會在記錄檔中寫入錯誤。 |
| update | 如果已存在具有指定識別碼的裝置，則會以所提供的輸入資料覆寫現有資訊，而不管 ETag 值為何。<br/>如果裝置不存在，則會在記錄檔中寫入錯誤。 |
| pdateIfMatchETagu | 如果已存在具有指定識別碼的裝置，則當 ETag 相符時，才會以所提供的輸入資料覆寫現有資訊。<br/>如果裝置不存在，則會在記錄檔中寫入錯誤。<br/>如果 ETag 不相符，則會在記錄檔中寫入錯誤。 |
| createOrUpdateIfMatchETag | 如果不存在具有指定**識別碼**的裝置，則表示是新註冊的裝置。<br/>如果裝置已存在，則當 ETag 相符時，才會以所提供的輸入資料覆寫現有資訊。<br/>如果 ETag 不相符，則會在記錄檔中寫入錯誤。 |
| delete | 如果已存在具有指定識別碼的裝置，則會遭到刪除，而不管 ETag 值為何。<br/>如果裝置不存在，則會在記錄檔中寫入錯誤。 |
| deleteIfMatchETag | 如果已存在具有指定識別碼的裝置，則只會在 ETag 相符時予以刪除。如果裝置不存在，則會在記錄檔中寫入錯誤。<br/>如果 ETag 不相符，則會在記錄檔中寫入錯誤。 |

範例：

```
{"id":"devA","eTag":"MQ==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"123","secondaryKey":"123"}}, "importMode":"delete"}
{"id":"devB","eTag":"MQ==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"123","secondaryKey":"123"}}, "importMode":"createOrUpdate"}
{"id":"devC","eTag":"MQ==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"123","secondaryKey":"123"}}, "importMode":"create"}
```

## 安全性 <a id="security"></a>

本節說明用來保護 Azure IoT 中樞的選項。

### 存取控制 <a id="accesscontrol"></a>

IoT 中樞使用下列權限組，授與每個 IoT 中樞端點的存取權。權限可根據功能限制 IoT 中樞的存取權。

* RegistryRead。授與裝置身分識別登錄的讀取權限。如需詳細資訊，請參閱[裝置身分識別登錄](#device-identity-registry)。
* RegistryReadWrite。授與裝置身分識別登錄的讀取和寫入權限。如需詳細資訊，請參閱[裝置身分識別登錄](#device-identity-registry)。
* ServiceConnect。授與雲端服務面向通訊和監視端點的存取權。例如，它授權後端雲端服務接收裝置到雲端的訊息、傳送雲端到裝置的訊息，以及擷取相對應的傳遞通知。
* DeviceConnect。授與裝置面向通訊端點的存取權。例如，它會授與傳送裝置到雲端的訊息和接收雲端到裝置的訊息的權限。裝置會使用此權限。

您可以透過下列方式授與權限：

* 中心層級的共用存取原則。共用存取原則可以授與上一節所列權限的任意組合。您可以在 [Azure 入口網站][lnk-management-portal]中定義原則，或使用 [Azure IoT 中樞資源提供者 API][lnk-resource-provider-apis] 以程式設計方式定義原則。新建立的 IoT 中樞有下列預設原則︰

    - iothubowner︰具備所有權限的原則
    - service︰具備 ServiceConnect 權限的原則
    - device︰具備 DeviceConnect 權限的原則
    - registryRead︰具備 RegistryRead 權限的原則
    - registryReadWrite︰具備 RegistryRead 和 RegistryWrite 權限的原則

* 各裝置的安全性認證。每個 IoT 中樞都包含[裝置身分識別登錄](#device-identity-registry)。對於此登錄中的每個裝置，您可以設定安全性認證，以對應的裝置端點為範圍來授與 DeviceConnect 權限。

範例。在典型的 IoT 解決方案中︰
- 裝置管理元件使用 registryReadWrite 原則。
- 事件處理器元件使用 service 原則。
- 執行階段裝置商務邏輯元件使用 service 原則。
- 個別裝置會使用 IoT 中樞身分識別登錄內儲存的認證進行連接。

如需 IoT 中樞安全性主題的指引，請參閱[設計您的方案][lnk-guidance-security]中的安全性區段。

### 驗證

Azure IoT 中樞可根據共用存取原則和裝置身分識別登錄安全性認證驗證權杖，以授與端點的存取權。

安全性認證 (例如對稱金鑰) 決不會在網路上傳送。

> [AZURE.NOTE] 如同 [Azure Resource Manager][lnk-azure-resource-manager] 中的所有提供者一樣，Azure IoT 中樞資源提供者也是透過您的 Azure 訂用帳戶而受保護。

#### 安全性權杖格式 <a id="tokenformat"></a>

安全性權杖具有下列格式：

	SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}

以下是預期的值：

| 值 | 說明 |
| ----- | ----------- |
| {signature} | HMAC-SHA256 簽章字串，格式為：`{URL-encoded-resourceURI} + "\n" + expiry`。重要事項：金鑰是從 base64 解碼而來，並且會做為用來執行 HMAC-SHA256 計算的金鑰。 |
| {resourceURI} | 可使用此權杖存取之端點的 URI 前置詞 (由區段)。例如，`/events` |
| {expiry} | 從新紀元時間 (Epoch) 1970 年 1 月 1日 00:00:00 UTC 時間至今秒數的 UTF8 字串。 |
| {URL-encoded-resourceURI} | 小寫資源 URI 的小寫 URL 編碼 |
| {policyName} | 此權杖所參考的共用存取原則名稱。在權杖參考裝置登錄認證的情況下不存在。 |

前置詞的注意事項︰URI 前置詞是依區段 (而不是依字元) 計算。例如，`/a/b` 是 `/a/b/c` 的前置詞，而不是 `/a/bc` 的前置詞。

您可以在 IoT 裝置和服務 SDK 中找到簽章演算法的實作：

* [適用於 Java 的 IoT 服務 SDK](https://github.com/Azure/azure-iot-sdks/tree/master/java/service/iothub-service-sdk/src/main/java/com/microsoft/azure/iot/service/auth)
* [適用於 Java 的 IoT 裝置 SDK](https://github.com/Azure/azure-iot-sdks/tree/master/java/device/iothub-java-client/src/main/java/com/microsoft/azure/iothub/auth)
* [適用於 Node.js 的 IoT 裝置和服務 SDK](https://github.com/Azure/azure-iot-sdks/blob/master/node/common/core/lib/shared_access_signature.js)

#### 通訊協定詳細規格

每個支援的通訊協定 (例如 AMQP、MQTT 和 HTTP) 會以不同的方式傳輸權杖。

HTTP 會透過在 Authorization 要求標頭中包含有效的權杖來實作驗證。稱為 [授權] 的查詢參數也可以傳輸權杖。

使用 [AMQP][lnk-amqp] 時，IoT 中樞支援 [SASL PLAIN][lnk-sasl-plain] 和 [AMQP 宣告式安全性][lnk-cbs]。

在 AMQP 宣告式安全性中，標準指定如何傳輸這些權杖。

在 SASL PLAIN 中，username 可以是：

* 以中樞層級權杖而言，`{policyName}@sas.root.{iothubName}`。
* 以裝置範圍權杖而言，`{deviceId}`。

在這兩種情況下，密碼欄位都包含[權杖格式](#tokenformat)一節所述的權杖。

使用 MQTT 時，CONNECT 封包具有做為 ClientId 的 deviceId，在 [使用者名稱] 欄位中具有 {iothubhostname}/{deviceId}，並且在 [密碼] 欄位中具有 SAS 權杖。{iothubhostname} 應該是 IoT 中樞的完整 CName (例如，contoso.azure-devices.net)。

##### 範例： #####

使用者名稱 (DeviceId 區分大小寫)︰`iothubname.azure-devices.net/DeviceId`

密碼 (使用裝置總管產生 SAS)︰`SharedAccessSignature sr=iothubname.azure-devices.net%2fdevices%2fDeviceId&sig=kPszxZZZZZZZZZZZZZZZZZAhLT%2bV7o%3d&se=1487709501`

> [AZURE.NOTE] [Azure IoT 中樞 SDK][lnk-apis-sdks] 會在連接至服務時自動產生權杖。在某些情況下，SDK 不支援所有的通訊協定或所有驗證方法。

#### 相較於 CBS 的 SASL PLAIN

使用 SASL PLAIN 時，連接至 IoT 中樞的用戶端可為每個 TCP 連線使用單一權杖。當權杖過期時，TCP 連線會中斷服務連線，並觸發重新連線。此行為雖不會對應用程式後端元件造成問題，但是對裝置端應用程式極為不利，其原因如下︰

*  閘道器通常會代表許多裝置連線。使用 SASL PLAIN 時，它們必須針對連接至 IoT 中樞的每個裝置不同的建立 TCP 連線。這會大幅提高電力與網路資源的耗用量並增加每個裝置連線的延遲。
* 在每個權杖到期後，增加使用要重新連接的資源通常會對資源受限的裝置有不良影響。

### 設定中心層級認證的範圍

使用受限制的資源 URI 建立權杖，可以設定中心層級安全性原則的範圍。例如，要從裝置傳送裝置對雲端訊息的端點是 /devices/{deviceId}/events。您也可以使用中心層級的共用存取原則搭配 DeviceConnect 權限，簽署 resourceURI 為 /devices/{deviceId} 的權杖，建立只能用來代表裝置 deviceId 傳送訊息的權杖。

這項機制類似於[事件中樞發行者原則][lnk-event-hubs-publisher-policy]，並讓您可實作自訂驗證方法，如[設計您的解決方案][lnk-guidance-security]的安全性區段中所述。

## 訊息

IoT 中樞提供通訊的傳訊基礎︰
- [雲端到裝置](#c2d)︰從應用程式後端 (服務或雲端)。
- [裝置到雲端](#d2c)︰從裝置到應用程式後端。

IoT 中樞傳訊功能的核心屬性是訊息的可靠性和持久性。這可在裝置端上恢復間歇性連線，以及在雲端恢復事件處理的負載尖峰。IoT 中樞會針對裝置到雲端和雲端到裝置實作至少一次傳遞保證。

IoT 中樞支援多個裝置面向通訊協定 (例如 AMQP 和 HTTP/1)。為了支援完美的跨通訊協定互通性，IoT 中樞定義了所有裝置面向通訊協定均可支援的通用訊息格式。

### 訊息格式 <a id="messageformat"></a>

IoT 中樞訊息包含︰

* 一組系統屬性。這些是 IoT 中樞解譯或設定的屬性。這個集合是預先決定的。
* 一組應用程式屬性。這是應用程式可以定義的字串屬性字典，而不需將訊息本文還原序列化即可加以存取。IoT 中樞不會修改這些屬性。
* 不透明的二進位主體。

如需不同通訊協定中如何將訊息編碼的詳細資訊，請參閱 [IoT 中樞 API 和 SDK][lnk-apis-sdks]。

這是 IoT 中樞訊息中的系統屬性集合。

| 屬性 | 說明 |
| -------- | ----------- |
| MessageId | 使用者可設定的訊息識別碼，通常用於要求-回覆模式。格式︰ASCII 7 位元英數字元 + `{'-', ':',’.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}` 的區分大小寫字串 (最長為 128 個字元)。 |
| 序號 | IoT 中樞指派給每則雲端到裝置訊息的數字 (對每個裝置佇列而言都是唯一的)。 |
| 收件人 | 在[雲端到裝置](#c2d)訊息中用來指定目的地。 |
| ExpiryTimeUtc | 訊息到期的日期和時間。 |
| EnqueuedTime | IoT 中樞收到訊息的日期和時間。 |
| CorrelationId | 回應訊息中的字串屬性，通常包含採用「要求-回覆」模式之要求的 MessageId。 |
| UserId | 用來指定訊息的來源。當 IoT 中樞產生訊息時，它會設定為 `{iot hub name}`。 |
| Ack | 使用於雲端到裝置訊息，要求 IoT 中樞因為裝置取用訊息而產生意見反應訊息。可能的值︰無 (預設值)︰不會產生任何意見反應訊息，正數︰如果訊息已完成，則會收到意見反應訊息，負數︰如果訊息未由裝置完成就到期 (或已達到最大傳遞計數) 則會收到意見反應訊息，完整︰正數和負數。若需詳細資訊，請參閱[訊息意見反應](#feedback)。 |
| ConnectionDeviceId | 由 IoT 中樞在裝置到雲端訊息上設定。它包含傳送訊息之裝置的 deviceId。 |
| ConnectionDeviceGenerationId | 由 IoT 中樞在裝置到雲端訊息上設定。它包含傳送訊息之裝置的 generationId (依據[裝置身分識別屬性](#deviceproperties))。 |
| ConnectionAuthMethod | 由 IoT 中樞在裝置到雲端訊息上設定。用來驗證傳送訊息之裝置的驗證方法的相關資訊。如需詳細資訊，請參閱[裝置到雲端反詐騙](#antispoofing)。|

### 選擇您的通訊協定 <a id="amqpvshttp"></a>

在裝置端通訊方面，Iot 中樞支援 [AMQP][lnk-amqp]、透過 WebSockets 的 AMQP、MQTT 和 HTTP/1 通訊協定。以下是有關其用途的考量清單。

* 雲端到裝置的模式。HTTP/1 沒有有效的方式可實作伺服器發送。因此，使用 HTTP/1 時，裝置會向 IoT 中樞輪詢雲端到裝置的訊息。這對於裝置和 IoT 中樞而言都非常沒有效率。使用 HTTP/1 時，目前的指導方針是讓每個裝置每隔 25 分鐘或以上輪詢一次。另一方面，AMQP 和 MQTT 在接收雲端到裝置的訊息時支援伺服器推送，而且能立即將訊息從 IoT 中樞推送至裝置。如果傳遞延遲是一大考量，最好使用 AMQP 或 MQTT 通訊協定。另一方面，HTTP/1 也適用於幾乎不連接的裝置。
* 現場閘道器。使用 HTTP/1 和 MQTT 時，您無法使用相同的 TLS 連線來連線到多個裝置 (各有自己的每一裝置認證)。可見在實作[現場閘道器案例][lnk-azure-gateway-guidance]時，這些並不是最理想的通訊協定，因為對於每個連線至現場閘道器的裝置，這些通訊協定需要一個現場閘道器和 IoT 中樞之間的 TLS 連線。
* 低資源裝置。MQTT 和 HTTP/1 程式庫的使用量比 AMQP 程式庫更小。因此，如果裝置擁有的資源很少 (例如，小於 1 Mb RAM)，這些通訊協定可能是唯一可用的通訊協定實作。
* 網路周遊。MQTT 標準會在連接埠 8883 上接聽。這可能會導致對非 HTTP 通訊協定關閉的網路發生問題。HTTP 和 AMQP (透過 WebSockets) 皆可用在此案例中。
* 承載大小。AMQP 和 MQTT 是二進位通訊協定，因此明顯比 HTTP/1 更加精簡。

概括而言，您應該盡可能使用 AMQP (或透過 WebSockets 的 AMQP)，而且只在資源的條件約束會禁止使用 AMQP 時才使用 MQTT。只有當網路周遊和網路設定會阻止使用 MQTT 和 AMQP 時，才應該使用 HTTP/1。此外，在使用 HTTP/1 時，每個裝置應該每隔 25 分鐘或以上輪詢一次雲端到裝置的訊息。

> [AZURE.NOTE] 很顯然地，在開發期間以比每隔 25 分鐘一次還頻繁地進行輪詢是可接受的情況。

#### MQTT 支援的注意事項
IoT 中樞會實作 MQTT v3.1.1 通訊協定，但其具有下列限制和特定行為：

  * QoS 2 不受支援：若裝置用戶端使用 QoS 2 發佈訊息，IoT 中樞會關閉網路連接。當裝置用戶端訂閱具有 QoS 2 的主題時，IoT 中樞會在 SUBACK 封包中授與最大 QoS 層級 1。
  * 保留：如果裝置用戶端發佈 RETAIN 旗標設為 1 的訊息，IoT 中樞會在訊息中新增 x-opt-retain 應用程式屬性。這表示 IoT 中樞不會持續保留訊息，而是改為將它傳遞至後端應用程式。

最後請檢閱 [Azure IoT 通訊協定閘道器][lnk-azure-protocol-gateway]，它可讓您部署直接與 IoT 中樞互動的高效能自訂通訊協定閘道器。Azure IoT 通訊協定閘道器可讓您自訂裝置通訊協定，以順應要重建的 MQTT 部署或其他自訂通訊協定。這個方法的代價是需要自我裝載和運作自訂的通訊協定閘道器。

### 裝置到雲端 <a id="d2c"></a>

如[端點](#endpoints)區段中所詳述，裝置到雲端訊息是透過裝置面向端點 (/devices/{deviceId}/messages/events) 傳送，並透過與[事件中樞][lnk-event-hubs]相容的服務面向端點 (/messages/events) 接收。因此，您可以使用標準事件中樞整合和 SDK 來接收裝置到雲端的訊息。

IoT 中樞實作裝置到雲端傳訊的方式類似於[事件中樞][lnk-event-hubs]，而比起[服務匯流排][lnk-servicebus]訊息，IoT 中樞的裝置到雲端訊息更像事件中樞的事件。

此實作具有下列含意：

* 與事件中樞事件類似，裝置到雲端訊息會長期保留在 IoT 中樞最多 7 天 (請參閱[裝置到雲端的設定選項](#d2cconfiguration))。
* 裝置到雲端的訊息會分割存放到建立時所設定的一組固定資料分割中 (請參閱[裝置到雲端的設定選項](#d2cconfiguration))。
* 與事件中樞類似，讀取裝置到雲端訊息的用戶端必須處理資料分割和檢查點，請參閱[事件中樞 - 取用事件][lnk-event-hubs-consuming-events]。
* 如同事件中樞的事件，裝置到雲端的訊息最大可能為 256 Kb，而且可分成數個批次以最佳化傳送。批次最大可能為 256 Kb，最多包含 500 則訊息。

不過，IoT 中樞裝置到雲端與事件中樞之間還有一些重要差異：

* 如[安全性](#security)一節所述，IoT 中樞允許每一裝置的驗證和存取控制。
* IoT 中樞允許同時連接數百萬個裝置 (請參閱[配額和節流](#throttling))，而事件中樞則受限於每個命名空間 5000 個 AMQP 連線。
* IoT 中樞不允許使用 PartitionKey 進行任意資料分割。裝置對雲端訊息是根據其來源的 deviceId 分割。
* IoT 中樞的調整方式稍微不同於事件中樞。如需詳細資訊，請參閱[調整 IoT 中樞][lnk-guidance-scale]。

請注意，這不表示您在所有情況下都能替代事件中樞的 IoT 中樞。例如，在某些事件處理運算中，有可能需要在分析資料串流之前，根據不同屬性或欄位而重新分割事件。在此案例中，您可以使用事件中樞來減少串流處理管線的兩個部分。如需詳細資訊，請參閱 [Azure 事件中樞概觀][lnk-eventhub-partitions]中的分割數。

如需如何使用裝置到雲端傳訊的詳細資訊，請參閱 [IoT 中樞 API 和 SDK][lnk-apis-sdks]。

> [AZURE.NOTE] 使用 HTTP 來傳送裝置到雲端的訊息時，下列字串只能包含 ASCII 字元：系統屬性值，以及應用程式屬性名稱和值。

#### 非遙測流量

在許多情況下，除了遙測資料點之外，裝置也會傳送訊息，以及需要執行與處理應用程式商務邏輯層要求的要求。例如，必須在後端觸發特定動作的重大警示，或對由後端傳送之命令的裝置回應。

如需有關這些類型之訊息的最佳處理方式，請參閱[裝置到雲端處理][lnk-guidance-d2c-processing]。

#### 裝置到雲端的設定選項 <a id="d2cconfiguration"></a>

IoT 中樞會公開下列屬性，讓您控制裝置到雲端傳訊。

* 分割計數。在建立時設定此屬性，以定義裝置對雲端事件擷取的資料分割數目。
* 保留時間。此屬性指定裝置到雲端訊息的保留時間。預設值是一天，但它可以增加到七天。

此外，類似於事件中樞，IoT 中樞也可讓您管理裝置對雲端接收端點上的「取用者群組」。

您可以使用 [Azure 入口網站][lnk-management-portal]修改以上所有屬性，或透過 [Azure IoT 中樞 - 資源提供者 API][lnk-resource-provider-apis] 以程式設計方式來修改。

#### 防詐騙屬性 <a id="antispoofing"></a>

為了避免裝置到雲端的訊息中出現裝置詐騙，IoT 中樞使用下列屬性在所有訊息上加上戳記：

* ConnectionDeviceId
* ConnectionDeviceGenerationId
* ConnectionAuthMethod

前兩個屬性包含來源裝置的 deviceId 和 generationId (依據[裝置身分識別屬性](#deviceproperties))。

ConnectionAuthMethod 屬性包含具有下列屬性的 JSON 序列化物件︰

```
{
  "scope": "{ hub | device}",
  "type": "{ symkey | sas}",
  "issuer": "iothub"
}
```

### 雲端到裝置 <a id="c2d"></a>

如[端點](#endpoints)一節中所詳述，您可以透過服務對應端點 (/messages/devicebound) 傳送雲端到裝置訊息，而裝置則可透過裝置特定端點 (/devices/{deviceId}/messages/devicebound) 接收它們。

每個雲端到裝置訊息都以單一裝置為目標，其中會將 to 屬性設定為 /devices/{deviceId}/messages/devicebound。

重要事項︰每個裝置佇列最多可以保留 50 則雲端到裝置的訊息。嘗試將更多訊息傳送至相同的裝置會導致錯誤。

> [AZURE.NOTE] 在傳送雲端到裝置的訊息時，下列字串只能包含 ASCII 字元：系統屬性值，以及應用程式屬性名稱和值。

#### 訊息生命週期 <a id="message lifecycle"></a>

為了實作至少一次傳遞保證，雲端到裝置的訊息會保存在每一裝置的佇列中，而裝置必須明確地認可完成，IoT 中樞才會從佇列中移除訊息。這保證連線失敗和裝置故障能夠恢復。

下圖顯示雲端到裝置訊息的生命週期狀態圖。

![雲端到裝置的訊息生命週期][img-lifecycle]

當服務傳送訊息時，它會被視為「已加入佇列」。當裝置想要「接收」訊息時，IoT 中樞會「鎖定」該訊息 (將狀態設為 [不可見])，以便讓相同裝置上的其他執行緒開始接收其他訊息。當裝置執行緒完成處理時，它會藉由「完成」訊息來通知 IoT 中樞。

裝置也可以︰
- 「拒絕」訊息，這會導致 IoT 中樞將其設定為 [停止傳送] 狀態。
- 「放棄」訊息，這會導致 IoT 中樞將訊息放回佇列，並將狀態設定為 [已加入佇列]。

執行緒可能無法處理訊息，且不會通知 IoT 中樞。在此情況下，訊息會在過了「可見性 (或鎖定) 逾時」的一分鐘預設值之後，自動從 [不可見]狀態轉換回 [已加入佇列] 狀態。訊息可以在 [已加入佇列] 與 [不可見] 狀態之間轉換的次數，以 IoT 中樞上 [最大傳遞計數] 屬性中指定的次數為限。在該轉換次數之後，IoT 中樞就會將訊息的狀態設定為 [寄不出]。同樣地，IoT 中樞也會在訊息的到期時間過後 (請參閱[存留時間](#ttl))，將訊息的狀態設定為 [寄不出]。

如需雲端到裝置訊息的教學課程，請參閱[開始使用 Azure IoT 中樞雲端到裝置訊息][lnk-getstarted-c2d-tutorial]。如需有關不同 API 和 SDK 如何公開雲端到裝置功能的參考主題，請參閱 [IoT 中樞 API 和 SDK][lnk-apis-sdks]。

> [AZURE.NOTE] 通常只要遺失訊息不會影響應用程式邏輯，就應該完成雲端到裝置的訊息。這可能會在許多不同的情況下發生。例如：訊息內容已成功保存在本機儲存體，或已成功執行作業，或訊息帶有遺失時不會影響應用程式功能的暫時性資訊。有時候，對於長時間執行的工作，在本機儲存體上保存工作描述之後，您可以完成雲端到裝置的訊息，然後在工作進度的各個階段，以一或多則裝置到雲端的訊息通知應用程式後端。

#### 存留時間 <a id="ttl"></a>

每個雲端到裝置的訊息都有到期時間。這可以由服務 (在 ExpiryTimeUtc 屬性中) 明確設定，或由 IoT 中樞使用指定為 IoT 中樞屬性的預設存留時間來設定。請參閱[雲端到裝置的設定選項](#c2dconfiguration)。

#### 訊息意見反應 <a id="feedback"></a>

當您傳送雲端到裝置的訊息時，服務可以要求傳遞每則訊息的意見反應 (關於該訊息的最終狀態)。

- 如果您將 Ack 屬性設定為 [正數]，只有在雲端到裝置的訊息達到 [已完成] 狀態時，IoT 中樞才會產生回饋訊息。
- 如果您將 Ack 屬性設定為 [負數]，只有在雲端到裝置的訊息達到 [寄不出] 狀態時，IoT 中樞才會產生回饋訊息。
- 如果您將 Ack 屬性設定為 [完整]，則 IoT 中樞在上述任一情況下都會產生回饋訊息。

> [AZURE.NOTE] 當 Ack 為 [完整] 時，如果未收到任何回饋訊息，即表示回饋訊息已過期，服務無法得知原始訊息發生什麼狀況。實際上，服務應該確保它可以在回饋到期之前對其進行處理。最長到期時間是兩天，因此當發生失敗時，應該有相當充裕的時間可以讓服務啟動並正常執行。

如[端點](#endpoints)中所述，IoT 中樞會透過服務對應端點 (/messages/servicebound/feedback) 以訊息方式傳遞回饋。回饋的接收語意與雲端到裝置訊息的接收語意相同，並且具有相同的 [訊息生命週期](#訊息生命週期)。可能的話，訊息意見反應會放入單一訊息中，其格式如下。

裝置從回饋端點擷取的每則訊息具有下列屬性：

| 屬性 | 說明 |
| -------- | ----------- |
| EnqueuedTime | 指出訊息建立時間的時間戳記。 |
| UserId | `{iot hub name}` |
| ContentType | `application/vnd.microsoft.iothub.feedback.json` |

主體是記錄的 JSON 序列化陣列，而每筆記錄都具有下列屬性︰

| 屬性 | 說明 |
| -------- | ----------- |
| EnqueuedTimeUtc | 指出訊息的結果出現時的時間戳記。例如，完成已裝置或訊息已到期。 |
| OriginalMessageId | 與此意見反應資訊相關的雲端到裝置訊息的 MessageId。 |
| StatusCode | 必須是整數。用於 IoT 中樞所產生的回饋訊息中。<br/> 0 = 成功 <br/> 1 = 訊息已過期 <br/> 2 = 超出最大傳遞計數 <br/> 3 = 訊息被拒 |
| 說明 | StatusCode 的字串值。 |
| DeviceId | 與此意見反應相關之雲端到裝置訊息的目標裝置 DeviceId。 |
| DeviceGenerationId | 與此意見反應相關之雲端到裝置訊息的目標裝置 DeviceGenerationId。 |


重要事項。服務必須指定雲端到裝置訊息的 MessageId，才能使其意見反應與原始訊息相互關聯。

範例。以下是意見反應訊息的主體範例。

```
[
  {
    "OriginalMessageId": "0987654321",
    "EnqueuedTimeUtc": "2015-07-28T16:24:48.789Z",
    "StatusCode": 0
    "Description": "Success",
    "DeviceId": "123",
    "DeviceGenerationId": "abcdefghijklmnopqrstuvwxyz"
  },
  {
    ...
  },
  ...
]
```

#### 雲端到裝置的設定選項 <a id="c2dconfiguration"></a>

每個 IoT 中樞都會針對雲端到裝置傳訊公開下列設定選項：

| 屬性 | 說明 | 範圍和預設值 |
| -------- | ----------- | ----------------- |
| defaultTtlAsIso8601 | 雲端到裝置訊息的預設 TTL。 | ISO\_8601 間隔高達 2D (最小為 1 分鐘)。預設值︰1 小時。 |
| maxDeliveryCount | 每個裝置佇列的雲端到裝置最大傳遞計數。 | 1 到 100。預設值：10 |
| feedback.ttlAsIso8601 | 保留服務繫結的意見反應訊息。 | ISO\_8601 間隔高達 2D (最小為 1 分鐘)。預設值︰1 小時。 |
| feedback.maxDeliveryCount | 意見反應佇列的最大傳遞計數。 | 1 到 100。預設值 = 100。 |

如需進一步資訊，請參閱[管理 IoT 中樞][lnk-manage]。

## 配額和節流 <a id="throttling"></a>

每個 Azure 訂用帳戶最多可以有 10 個 IoT 中樞。

每個 IoT 中樞都會以特定 SKU 佈建特定數目的單位 (如需詳細資訊，請參閱 [Azure IoT 中樞定價][lnk-pricing])。SKU 和單位數目會決定可以傳送之訊息的每日配額上限。

SKU 也會決定 IoT 中樞在所有作業上強制執行的節流限制。

### 作業節流

作業節流是在分鐘範圍內套用的速率限制，主要是為了避免不當使用。IoT 中樞會試著儘可能避免傳回錯誤，但如果違反節流太久，就會開始傳回例外狀況。

以下是強制執行的節流清單。個別中心的值如下。

| 節流 | 每個中心的值 |
| -------- | ------------- |
| 身分識別登錄作業 (建立、擷取、列出、更新、刪除) | 100/分鐘/單位，最高 5000/分鐘 |
| 裝置連線 | 120/秒/單位 (適用於 S2), 12/秒/單位 (適用於 S1)。<br/>最小值為 100/秒。<br/>例如，兩個 S1 單位是 2*12 = 24/秒，但是您在全部單位將至少擁有 100/秒。如果有九個 S1 單位，您的全部單位就會擁有 108/秒 (9*12)。 |
| 裝置到雲端傳送 | 120/秒/單位 (適用於 S2), 12/秒/單位 (適用於 S1)。<br/>最小值為 100/秒。<br/>例如，兩個 S1 單位是 2*12 = 24/秒，但是您在全部單位將至少擁有 100/秒。如果有九個 S1 單位，您的全部單位就會擁有 108/秒 (9*12)。 |
| 雲端到裝置的傳送 | 100/分鐘/單位 |
| 雲端到裝置的接收 | 1000/分鐘/單位 |

注意。不論何時，都可以藉由增加 IoT 中樞佈建的單位來提高配額或節流限制。

重要事項：身分識別登錄作業是用於裝置管理與佈建案例中的執行階段用途。透過[匯入/匯出作業](#importexport)即可支援讀取或更新大量的裝置身分識別。

## 後續步驟

既然您已了解開發 IoT 中樞的概觀，請遵循下列連結深入了解：

- [開始使用 IoT 中樞 (教學課程)][lnk-get-started]
- [作業系統平台與硬體相容性][lnk-compatibility]
- [Azure IoT 開發人員中心][lnk-iotdev]
- [設計您的解決方案][lnk-guidance]

[事件中樞 - 事件處理器主機]: http://blogs.msdn.com/b/servicebus/archive/2015/01/16/event-processor-host-best-practices-part-1.aspx

[Azure 入口網站]: https://portal.azure.com

[img-endpoints]: ./media/iot-hub-devguide/endpoints.png
[img-lifecycle]: ./media/iot-hub-devguide/lifecycle.png
[img-eventhubcompatible]: ./media/iot-hub-devguide/eventhubcompatible.png

[lnk-compatibility]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/tested_configurations.md
[lnk-apis-sdks]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[lnk-resource-provider-apis]: https://msdn.microsoft.com/library/mt548492.aspx

[lnk-azure-gateway-guidance]: iot-hub-guidance.md#field-gateways
[lnk-guidance-provisioning]: iot-hub-guidance.md#provisioning
[lnk-guidance-scale]: iot-hub-scaling.md
[lnk-guidance-security]: iot-hub-guidance.md#customauth
[lnk-guidance-heartbeat]: iot-hub-guidance.md#heartbeat

[lnk-azure-protocol-gateway]: iot-hub-protocol-gateway.md
[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-guidance]: iot-hub-guidance.md
[lnk-getstarted-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md

[lnk-amqp]: https://www.amqp.org/
[lnk-mqtt]: http://mqtt.org/
[lnk-websockets]: https://tools.ietf.org/html/rfc6455
[lnk-arm]: ../resource-group-overview.md
[lnk-azure-resource-manager]: https://azure.microsoft.com/documentation/articles/resource-group-overview/
[lnk-cbs]: https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc
[lnk-createuse-sas]: ../storage-dotnet-shared-access-signature-part-2/
[lnk-event-hubs-publisher-policy]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-99ce67ab
[lnk-event-hubs]: http://azure.microsoft.com/documentation/services/event-hubs/
[lnk-event-hubs-consuming-events]: ../event-hubs/event-hubs-programming-guide.md#event-consumers
[lnk-guidance-d2c-processing]: iot-hub-csharp-csharp-process-d2c.md
[lnk-management-portal]: https://portal.azure.com
[lnk-rfc7232]: https://tools.ietf.org/html/rfc7232
[lnk-sasl-plain]: http://tools.ietf.org/html/rfc4616
[lnk-servicebus]: http://azure.microsoft.com/documentation/services/service-bus/
[lnk-tls]: https://tools.ietf.org/html/rfc5246
[lnk-iotdev]: https://azure.microsoft.com/develop/iot/
[lnk-bulk-identity]: iot-hub-bulk-identity-mgmt.md
[lnk-eventhub-partitions]: ../event-hubs/event-hubs-overview.md#partitions
[lnk-manage]: iot-hub-manage-through-portal.md

<!---HONumber=AcomDC_0309_2016-->