<properties
 pageTitle="適用於 IT 專業人員的 Azure IoT 中樞資訊 | Microsoft Azure"
 description="有助於 IT 專業人員使用 Azure IoT 中樞的資訊，例如連接埠需求和安全性背景。"
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
 ms.date="09/28/2015"
 ms.author="dobett"/>

# 設定和管理 IoT 中樞的存取權

本文章提供的資訊可協助 IT 專業人員設定環境，讓 IoT 裝置可透過網路基礎結構與 IoT 中樞通訊。

## 網路連線

裝置使用 AMQP 或 HTTPS 通訊協定與 Azure 中的 IoT 中樞通訊。一般而言，選擇的通訊協定是根據方案的特定需求而定。下表列出必須要為裝置開啟的輸出連接埠，以使用特定的通訊協定：

| 通訊協定 | 連接埠 |
| -------- | ------- |
| HTTPS | 443 |
| AMQP | 5671 |

當您在 Azure 區域中建立 IoT 中樞後，中樞將在該中樞的存留期間，保留相同的 IP 位址。不過，在嚴重損壞修復案例中，如果 Microsoft 將 IoT 中樞移至不同的縮放單位，則它會獲派新的 IP 位址。

## IoT 中樞和安全性

只有向 IoT 中樞註冊的裝置可與該 IoT 中樞進行通訊。已註冊的裝置必須獲得 *DeviceConnect* 權限。裝置會藉由包含權杖 (其會封裝其建立之每個要求中的裝置唯一識別碼) 來識別其本身，且中樞會檢查權杖的有效性，以及裝置是否未列入黑名單 (*DeviceConnect* 權限已撤銷)。

IoT 中樞內對其他管理端點的存取權，也是透過一組權限來控制：*iothubowner*、*service*、*registryRead* 和 *registryReadWrite*。連接到 IoT 中樞的所有用戶端管理應用程式，都必須包含具有適當權限的權杖。

## 後續步驟

這篇文章包含特定資訊，供設定開發和測試環境的 IT 專業人員和開發人員閱讀。請遵循下列連結以深入了解 Azure IoT 中樞服務：

- [何謂 Azure IoT 中樞？][lnk-iothub]
- [Azure IoT 中樞開發人員指南中的「安全性」][lnk-devguide]一節提供 IoT 中樞內權杖和權限系統的其他資訊。
- [透過 Azure Preview 入口網站管理 IoT 中樞][lnk-manage-portal]說明如何使用 Azure Preview 入口網站來管理您 IoT 的中樞。

[lnk-iothub]: iot-hub-what-is-iot-hub.md
[lnk-devguide]: iot-hub-devguide.md#security
[lnk-manage-portal]: iot-hub-manage-through-portal.md

<!---HONumber=Nov15_HO3-->