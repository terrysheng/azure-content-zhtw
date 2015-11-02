<properties
 pageTitle="使用 Azure IoT 中樞指引主題 | Microsoft Azure"
 description="使用 Azure IoT 中樞開發解決方案的指引主題集合。"
 services="iot-hub"
 documentationCenter=""
 authors="fsautomata"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/29/2015"
 ms.author="elioda"/>

# Azure IoT 中樞指引

## 裝置佈建

IoT 解決方案可在許多不同系統和儲存體中維護裝置資訊。在維護應用程式特定裝置資訊的其他儲存區中，其中包括 [IoT 中樞的身分識別登錄][IoT Hub Developer Guide - identity registry]。我們將在所有系統中建立必要裝置資訊的程序稱為*佈建*。

裝置佈建有許多需求和策略 (如需詳細資訊，請參閱 [IoT 中樞裝置管理指引])。[IoT 中樞身分識別登錄][IoT Hub Developer Guide - identity registry]提供您在佈建程序中整合 IoT 中樞所需的 API。

## 現場閘道器

欄位閘道器是特殊化的裝置-應用裝置，或做為通訊啟用器的一般用途軟體，甚至可能是本機裝置控制系統和裝置資料處理中心。現場閘道器可以執行本機處理並控制針對裝置的函式。另一方面，它可以篩選或彙總裝置遙測，並藉此減少傳輸至後端的資料量。

現場閘道器的範圍包括現場閘道器本身以及連接到它的所有裝置。正如其名，閘道欄位器可做為外部專用的資料處理設備，且通常會和位置繫結。欄位閘道器和純粹流量路由器的不同之處在於，它在管理存取及資訊流量方面扮演主動的角色。這表示它是由應用程式處理的實體和網路連接或工作階段終點 (例如，在此內容中的閘道器可以協助裝置佈建、資料轉換、通訊協定轉譯和事件規則處理)。相對地，NAT 裝置或防火牆不會限定為欄位閘道器，因為它們並不是明確的連接或工作階段終點，而是路由 (或拒絕) 連接或工作階段經過之處。

### 透明 vs 不透明欄位閘道器

關於裝置身分識別，如果現場閘道器範圍中的其他裝置在 IoT 中樞的身分識別登錄中具有裝置身分識別時體，則該現場閘道器可稱為*透明*。如果 IoT 中樞的身分識別登錄中唯一的身分識別就是現場閘道器的身分識別，則可稱為*不透明*。

請務必注意，使用*不透明*閘道器可防止 IoT 中樞提供[裝置身分識別反詐騙][IoT Hub Developer Guide - Anti-spoofing]。此外，因為現場閘道器範圍中的所有裝置都會表示為 IoT 中樞內的單一裝置，它們將受限於單一裝置的節流與配額。

### 其他考量

實作現場閘道器時，您可以使用 [Azure IoT 裝置 SDK][]。某些 SDK 提供欄位閘道器特定功能，例如相同 IoT 中心連接上多工的多裝置通訊功能。如同 [IoT 中樞開發人員指南 - 選擇通訊協定][]中所述，您應該避免使用 HTTP/1 做為現場閘道器的傳輸通訊協定。

## 使用自訂裝置驗證配置/服務

IoT 中樞可讓您透過使用[裝置身分識別登錄][IoT Hub Developer Guide - identity registry]設定每個裝置的安全性認證和存取控制。

如果 IoT 解決方案已經大幅投資自訂裝置身分識別登錄及/或驗證配置，它仍然可以藉由建立 IoT 中樞的*權杖服務*以利用其他 IoT 中樞的功能。

權杖服務是自我部署的雲端服務，它會使用 IoT 中樞*共用存取原則*與 **DeviceConnect** 權限來建立*裝置範圍*權杖。

  ![][img-tokenservice]

以下是權杖服務模式的主要步驟。

1. 建立 IoT 中樞的 [IoT 中樞共用存取原則][IoT Hub Developer Guide - Security] 與 **DeviceConnect** 權限。權杖服務將使用此原則簽署權杖。
2. 當裝置想要存取 IoT 中心時，它會向您的權杖服務要求已簽署的權杖。裝置可以使用您的自訂裝置身分識別登錄/驗證配置。
3. 權杖服務會傳回為每個 [IoT 中樞開發人員指南 - 安全性][]建立的權杖，使用 `/devices/{deviceId}` 做為 `resourceURI`，`deviceId` 做為進行驗證的裝置。
4. 裝置直接透過 IoT 中心使用權杖。

權杖服務可以視需要設定權杖到期日。到期時，IoT 中樞將處理連接，裝置必須向權杖服務要求新權杖。很明顯地，過短的到期時間會增加裝置與權杖服務的負載。

值得一提的是，為了讓裝置能夠連接，仍需要在 IoT 中樞中建立裝置身分識別。這也表示即使裝置利用權杖驗證，每個裝置的存取控制仍會有功能 (藉由停用裝置身分識別做為每個 [IoT 中樞開發人員指南 - 身分識別登錄][])。如此可減少長期存在的權杖。

### 和自訂閘道器的比較

權杖服務模式為使用 IoT 中心實作自訂身分識別登錄/驗證配置的建議方式，因為它可讓 IoT 中心處理大部份的解決方案流量。不過在一些情況下，自訂驗證配置和通訊協定 (例如 [TLS-PSK][]) 過度交織，因此需要可處理所有流量 (*自訂閘道器*) 的服務。如需詳細資訊，請參閱[通訊協定閘道][]主題。

## 後續步驟

遵循下列連結以深入了解 Azure IoT 中樞：

- [開始使用 IoT 中樞 (教學課程)][lnk-get-started]
- [何謂 Azure IoT 中心？][]

[img-tokenservice]: ./media/iot-hub-guidance/tokenservice.png

[IoT Hub Developer Guide - identity registry]: iot-hub-devguide.md#identityregistry
[IoT 中樞開發人員指南 - 身分識別登錄]: iot-hub-devguide.md#identityregistry
[IoT 中樞裝置管理指引]: iot-hub-device-management.md
[IoT Hub Developer Guide - Anti-spoofing]: iot-hub-devguide.md#antispoofing
[Azure IoT 裝置 SDK]: iot-hub-sdks-summary.md
[IoT 中樞開發人員指南 - 選擇通訊協定]: iot-hub-devguide.md#amqpvshttp
[IoT Hub Developer Guide - Security]: iot-hub-devguide.md#security
[IoT 中樞開發人員指南 - 安全性]: iot-hub-devguide.md#security
[TLS-PSK]: https://tools.ietf.org/html/rfc4279
[通訊協定閘道]: iot-hub-protocol-gateway.md

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[何謂 Azure IoT 中心？]: iot-hub-what-is-iot-hub.md

<!---HONumber=Oct15_HO4-->