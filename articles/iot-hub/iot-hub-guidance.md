<properties
 pageTitle="IoT 中樞解決方案指引 | Microsoft Azure"
 description="使用 Azure IoT 中樞來開發 IoT 解決方案的閘道器、裝置佈建及驗證的相關指引主題。"
 services="iot-hub"
 documentationCenter=""
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="02/03/2016"
 ms.author="dobett"/>

# 設計您的解決方案

本文提供如何在物聯網 (IoT) 解決方案中設計下列功能的指引：

- 裝置佈建
- 現場閘道器
- 裝置驗證

## 裝置佈建

IoT 解決方案會儲存個別裝置的相關資料，例如：

- 裝置身分識別和驗證金鑰
- 裝置硬體類型和版本
- 裝置狀態
- 軟體版本和功能
- 裝置命令歷程記錄

給定的 IoT 解決方案儲存的裝置資料取決於該解決方案的特定需求。但是解決方案至少必須儲存裝置身分識別和驗證金鑰。Azure IoT 中樞包含[身分識別登錄][lnk-devguide-identityregistry]，其可以儲存每個裝置的值，例如識別碼、驗證金鑰和狀態碼。解決方案可以使用其他 Azure 服務 (例如資料表、blob 或 Azure DocumentDB) 來儲存任何其他裝置資料。

*裝置佈建*是將初始裝置資料加入至解決方案中存放區的程序。若要讓新裝置可連接到您的中樞，必須將新裝置識別碼和金鑰加入至 [IoT 中樞身分識別登錄][lnk-devguide-identityregistry]。做為佈建程序的一部分，您可能需要初始化其他解決方案存放區中的裝置特定資料。

[IoT 中樞裝置管理指引][lnk-device-management]文章描述裝置佈建的一些常見策略。[IoT 中樞身分識別登錄 API][lnk-devguide-identityregistry] 可讓您將 IoT 中樞整合到您的佈建程序。

## 現場閘道器

在 IoT 解決方案中，*現場閘道器*位於您的裝置和 IoT 中樞之間。它通常位於接近您的裝置的位置。您的裝置會使用裝置所支援的通訊協定，直接與現場閘道器通訊。現場閘道器會使用 IoT 中樞所支援的通訊協定來與 IoT 中樞通訊。現場閘道器可以是專用的獨立裝置，或是在現有硬體部分上執行的軟體。

現場閘道器與簡單的流量路由裝置 (例如網路位址轉譯 (NAT) 裝置或防火牆) 不同，因為它通常會在解決方案內管理存取和資訊流程中扮演主動的角色。例如，現場閘道器可以：

- 管理本機裝置。例如，現場閘道器可以執行事件規則處理，並將命令傳送至裝置以回應特定遙測資料。
- 篩選或彙總遙測資料，之後再將資料轉送到 IoT 中樞。這可以減少傳送到 IoT 中樞的資料量，還可能降低您的解決方案中的成本。
- 有助於佈建裝置。
- 轉換遙測資料，以協助您解決方案後端的處理。
- 執行通訊協定轉譯可讓裝置與 IoT 中樞進行通訊，即使它們未使用 IoT 中樞所支援的傳輸通訊協定亦然。

> [AZURE.NOTE] 雖然您通常會將現場閘道器在本機部署到您的裝置，在某些情況下，您可能會在雲端中部署[通訊協定閘道][lnk-gateway]。

### 現場閘道器的類型

現場閘道器可以是 *transparent* 或 *opaque*：

| &nbsp; | 透明的閘道器 | 不透明的閘道器|
|--------|-------------|--------|
| 儲存在 IoT 中樞身分識別登錄中的身分識別 | 所有連接裝置的身分識別 | 僅現場閘道器的身分識別 |
| IoT 中樞可以提供[裝置身分識別反詐騙][lnk-devguide-antispoofing] | 是 | 否 |
| [節流和配額][lnk-throttles-quotas] | 套用至每個裝置 | 套用至現場閘道器 |

> [AZURE.IMPORTANT]  使用不透明的閘道器模式時，所有透過該閘道器連線的裝置會共用相同的雲端到裝置的佇列，此佇列中最多可包含 50 則訊息。因此，不透明的閘道器模式的使用時機，應該為只有很少的裝置是透過每個現場閘道器連線，且其雲端到裝置的流量很低時。

### 其他考量

您可以使用 [Azure IoT 裝置 SDK][lnk-device-sdks]來實作現場閘道器。某些裝置 SDK 提供的特定功能可協助您實作現場閘道器，例如能夠將來自多個裝置到通往 IoT 中樞的相同連線上之通訊多工處理的能力。如同 [IoT 中樞開發人員指南 - 選擇通訊協定][lnk-devguide-protocol]中所述，您應該避免使用 HTTP/1 做為現場閘道器的傳輸通訊協定。

## 自訂裝置驗證

您可以使用 IoT 中樞[裝置身分識別登錄][lnk-devguide-identityregistry]來設定每一裝置的安全性認證和存取控制。如果 IoT 解決方案已經大幅投資自訂裝置身分識別登錄及/或驗證配置，您可以藉由建立*權杖服務*，將這個現有基礎結構與 IoT 中樞整合。如此一來，您可以在解決方案中使用其他 IoT 功能。

權杖服務是自訂雲端服務。建立具備 **DeviceConnect** 權限的 IoT 中樞共用存取原則，以建立「裝置範圍」權杖。這些權杖可讓裝置連接到 IoT 中樞。

  ![權杖服務模式的步驟][img-tokenservice]

以下是權杖服務模式的主要步驟：

1. 為您的 IoT 中樞建立具備 **DeviceConnect** 權限的 [IoT 中樞共用存取原則][lnk-devguide-security]。您可以在 [Azure 入口網站][lnk-portal]中或以程式設計方式建立此原則。權杖服務會使用此原則簽署它所建立的權杖。
2. 當裝置需要存取 IoT 中樞時，它會向您的權杖服務要求已簽署的權杖。裝置可以使用您的自訂裝置身分識別登錄/驗證配置來進行驗證，以判斷權杖服務用來建立權杖的裝置身分識別。
3. 權杖服務會傳回權杖。使用 `/devices/{deviceId}` 做為 `resourceURI` (其中 `deviceId` 是要驗證的裝置)，並根據 [IoT 中樞開發人員指南的安全性一節][lnk-devguide-security]建立權杖。權杖服務會使用共用存取原則來建構權杖。
4. 裝置直接透過 IoT 中心使用權杖。

> [AZURE.NOTE] 您可以使用 .NET 類別 [SharedAccessSignatureBuilder][lnk-dotnet-sas] 或 Java 類別 [IotHubServiceSasToken][lnk-java-sas] 在權杖服務中建立權杖。

權杖服務可以視需要設定權杖到期日。權杖到期時，IoT 中樞會切斷裝置連線。然後，裝置必須向權杖服務要求新權杖。如果您使用過短的到期時間，這會增加裝置與權杖服務上的負載。

為了讓裝置連線至中樞，您仍必須將它加入 IoT 中樞裝置身分識別登錄，即使裝置使用權杖而不是裝置金鑰來連線。因此，您可以在裝置使用權杖驗證時，利用在 [IoT 中樞識別登錄][lnk-devguide-identityregistry]中啟用或停用裝置身分識別，繼續使用每一裝置存取控制。如此可減輕使用較長到期時間權杖的風險。

### 和自訂閘道器的比較

權杖服務模式為使用 IoT 中樞實作自訂身分識別登錄/驗證配置的建議方式。這麼建議是因為 IoT 中樞會繼續處理大部份的解決方案流量。不過，在一些情況下，自訂驗證配置和通訊協定過度交織，因此需要可處理所有流量 (*自訂閘道器*) 的服務。[傳輸層安全性 (TLS) 和預先共用金鑰 (PSK)][lnk-tls-psk] 是服務範例之一。如需詳細資訊，請參閱[通訊協定閘道器][lnk-gateway]主題。

## 裝置活動訊號 <a id="heartbeat"></a>

[IoT 中樞身分識別登錄][lnk-devguide-identityregistry]包含名為 [connectionState] 的欄位。您只應該在開發和偵錯期間使用 [connectionState] 欄位，IoT 解決方案不應該在執行階段查詢該欄位 (例如，為了檢查裝置是否已連線，以便決定是否要傳送雲端到裝置的訊息或 SMS)。如果 IoT 解決方案需要知道裝置是否已連線 (在執行階段，或比 **connectionState** 屬性所提供的更精確時)，解決方案應該實作*活動訊號模式*。

在活動訊號模式中，裝置每隔固定時間就會至少傳送一次裝置到雲端的訊息 (例如，每小時至少一次)。這表示即使裝置沒有任何要傳送的資料，它仍會傳送空的裝置到雲端的訊息 (通常具有可供識別其屬於活動訊號的屬性)。在服務端，解決方案會維護一份地圖，裡面有每個裝置所收到的最後一次活動訊號，並假設如果裝置沒有在預期時間內收到活動訊號訊息，即表示裝置有問題。

更複雜的實作可以包含來自[作業監視][lnk-devguide-opmon]的資訊，以便識別嘗試連線或通訊但失敗的裝置。當您實作活動訊號模式時，請務必檢查 [IoT 中樞配額與節流][]。

## 後續步驟

遵循下列連結以深入了解 Azure IoT 中樞：

- [開始使用 IoT 中樞 (教學課程)][lnk-get-started]
- [何謂 Azure IoT 中心？][lnk-what-is-hub]

[img-tokenservice]: ./media/iot-hub-guidance/tokenservice.png

[lnk-devguide-identityregistry]: iot-hub-devguide.md#identityregistry
[lnk-device-management]: iot-hub-device-management.md
[lnk-devguide-opmon]: iot-hub-operations-monitoring.md

[lnk-device-sdks]: iot-hub-sdks-summary.md
[lnk-devguide-security]: iot-hub-devguide.md#security
[lnk-tls-psk]: https://tools.ietf.org/html/rfc4279
[lnk-gateway]: iot-hub-protocol-gateway.md

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-what-is-hub]: iot-hub-what-is-iot-hub.md
[lnk-portal]: https://portal.azure.com
[lnk-throttles-quotas]: ../azure-subscription-service-limits.md/#iot-hub-limits
[lnk-devguide-antispoofing]: iot-hub-devguide.md#antispoofing
[lnk-devguide-protocol]: iot-hub-devguide.md#amqpvshttp
[lnk-dotnet-sas]: https://msdn.microsoft.com/library/microsoft.azure.devices.common.security.sharedaccesssignaturebuilder.aspx
[lnk-java-sas]: http://azure.github.io/azure-iot-sdks/java/service/api_reference/com/microsoft/azure/iot/service/auth/IotHubServiceSasToken.html
[IoT 中樞配額與節流]: iot-hub-devguide.md#throttling

<!---HONumber=AcomDC_0204_2016-->