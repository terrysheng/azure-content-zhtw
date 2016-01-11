<properties
 pageTitle="Azure IoT 中樞調整 | Microsoft Azure"
 description="描述如何調整 Azure IoT 中樞。"
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
 ms.date="10/02/2015"
 ms.author="elioda"/>

# 調整 IoT 中樞

藉由將 IoT 中樞 S1 層級單位或 S2 層級單位的數目增加至 2,000，Azure IoT 中樞最多可以同時支援一百萬個連接的裝置。如需詳細資訊，請參閱 [IoT 中樞價格][lnk-pricing]。

每個 IoT 中樞單位允許登錄許某特定數目的裝置，且這些裝置都可以同時連接。每個單位也允許一些每日訊息。

為了適當調整您的解決方案，您必須考慮 IoT 中樞的特定使用方式。尤其要考慮以下類別的作業所需的尖峰輸送量：

* 裝置到雲端的訊息
* 雲端到裝置的訊息
* 身分識別登錄作業

除了此輸送量資訊之外，請參考 [IoT 中樞配額與節流][]並據以設計您的方案。

## 裝置到雲端及雲端到裝置訊息輸送量

若要評估每個裝置的流量，最佳方式為調整 IoT 中心解決方案的大小。

裝置到雲端訊息會遵循這些持續的輸送量指導方針。

| 層 | 持續的輸送量 | 持續的傳送速率 |
| ---- | -------------------- | ------------------- |
| S1 | 每個裝置最多 8 KB/小時 | 每個裝置平均 4 個訊息/小時 |
| S2 | 每個裝置最多 4 KB/分鐘 | 每個裝置平均 2 個訊息/分鐘 |

接收裝置到雲端訊息時，應用程式後端可以預期下列最大輸送量 (在所有的讀取器中)。

| 層 | 持續的輸送量 |
| ---- | -------------------- |
| S1 | 每個單位最多 120 KB/分鐘，最少 2 MB/秒。 |
| S2 | 每個單位最多 4 KB/分鐘，最少 2 MB/秒 |

雲端到裝置訊息的效能會隨每個裝置調整，每個裝置最多每分鐘接收 5 個訊息。

## 身分識別登錄作業輸送量

因為大部分的 IoT 中心識別登錄作業都與裝置佈建相關，所以不支援這些作業做為執行階段作業。

如需特定高載效能數據，請參閱 [IoT 中樞配額與節流][]。

## 分區化

雖然單一 IoT 中樞可以擴充到數百萬個裝置，但有時候您的解決方案需要單一 IoT 中樞無法保證的特定效能特性。在此情況下，建議您將裝置分割成多個 IoT 中樞。多個 IoT 中心可減緩資料傳輸量暴增，並取得所需的輸送量或作業速率。

## 後續步驟

遵循下列連結以深入了解 Azure IoT 中樞：

- [開始使用 IoT 中樞 (教學課程)][lnk-get-started]
- [何謂 Azure IoT 中心？][]

[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[IoT 中樞配額與節流]: iot-hub-devguide.md#throttling

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[何謂 Azure IoT 中心？]: iot-hub-what-is-iot-hub.md

<!---HONumber=AcomDC_1223_2015-->