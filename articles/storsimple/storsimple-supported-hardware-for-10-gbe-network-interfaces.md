<properties 
   pageTitle="10 GbE 網路介面在 StorSimple 裝置上支援的硬體"
   description="描述 StorSimple 裝置上的 10 GbE 網路介面支援的收發器、纜線及交換器。"
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carolz"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="07/27/2015"
   ms.author="alkohli" />

# 10 GbE 網路介面在 StorSimple 裝置上支援的硬體

## 概觀

本文章提供補充使用 Microsoft Azure StorSimple 裝置的補充硬體之相關資訊。

## 受支援裝置清單

Microsoft 已經測試下列小型尺寸隨插即用 (SFP) 收發器、纜線以及交換器以確保其對裝置以最佳方式運作。(下表將會在測試新硬體時更新。)

### SFP+ 收發器

|請確定|模型|
|---|---|
|Cisco|SFP-10G-SR|

### 纜線

|序號 |請確定|模型|
|---|---|---|
| 1\.|Cisco|SFP-H10GB-CU1M|
| 2\.|Cisco|SFP-H10GB-CU2M|
| 3\.|Cisco|SFP-H10GB-CU3M|
| 4\.|Tripp-Lite|N820-05M (OM3)|

### 交換器

|序號|請確定|模型|
|---|---|---|
| 1\. |Cisco|N3K-C3172PQ-10GE|
| 2\. |Cisco|N3K-C3048-ZM-F|
| 3\. |Cisco|N5K-C5596UP-FA|

## Mellanox 所支援之裝置的清單  

Mellanox 已經測試下列小型尺寸隨插即用 (SFP) 收發器、纜線以及交換器以確保它們能夠對 Mellanox 網路介面以最佳方式運作，例如 StorSimple 裝置上的 10 GbE 網路介面。

### Mellanox 所支援的纜線及模組 

下表列出 Mellanox 所支援的纜線及模組。這些項目尚未經過 Microsoft 測試，但可能使用您的 StorSimple 裝置。

| 序號 | 速度 | 模型 | 說明 | 請確定 |
|--------|-------|-----------------------|--------------------------------------------------------|-----------------------|
| 1\. | 10 GbE| CAB-SFP-SFP-1M | 被動銅纜線 SFP + 10 Gb/s 1m | Arista |
| 2\. | 10 GbE| CAB-SFP-SFP-2M | 被動銅纜線 SFP + 10 Gb/s 2m | Arista |
| 3\. | 10 GbE| CAB-SFP-SFP-3M | 被動銅纜線 SFP + 10 Gb/s 3m | Arista |
| 4\. | 10 GbE| CAB-SFP-SFP-5M | 被動銅纜線 SFP + 10 Gb/s 5m | Arista |
| 5\. | 10 GbE| Cisco SFP-H10GBCU1M | Cisco SFP + 纜線 | Cisco |
| 6\. | 10 GbE| Cisco SFP-H10GBCU3M | Cisco SFP + 纜線 | Cisco |
| 7\. |10 GbE | Cisco SFP-H10GBCU5M | Cisco SFP + 纜線 | Cisco |
| 8\. | 10 GbE| J9281B HP X242 10G | SFP+ to SFP+ 1m 直接附加銅纜線 | HP |
| 9\. | 10 GbE| 455883-B21 HP BLc | 10 Gb SR SFP + 選擇 | HP |
| 10\. | 10 GbE| 455886-B21 HP BLc | 10Gb LR SFP+ 選擇 | HP |
| 11\. |10 GbE | 487649-B21 HP BLc | SFP+ 0.5m 10GbE 銅纜線 | HP |
| 12\. |10 GbE | 487652-B21 HP BLc | SFP+ 1m 10GbE 銅纜線 | HP |
| 13\. |10 GbE | 487655-B21 HP BLc | SFP+ 3m 10GbE 銅纜線 | HP |
| 14\. |10 GbE | 487658-B21 HP BLc | SFP+ 7m 10GbE 銅纜線 | HP |
| 15\. |10 GbE | 537963-B21 HP BLc | SFP+ 5m 10GbE 銅纜線 | HP |
| 16\. |10 GbE | AP784A HP | 3m C 系列被動銅 SFP + 纜線 | HP |
| 17\. |10 GbE | AP785A HP | 5m C 系列被動銅 SFP + 纜線 | HP |
| 18\. |10 GbE | AP818A HP | 1m C 系列主動銅 SFP + 纜線 | HP |
| 19\. |10 GbE | AP819A HP | 3m B 系列主動銅 SFP + 纜線 | HP |
| 20\. |10 GbE | J9150A HP | X132 10G SFP+ LC SR 收發器 | HP |
| 21\. |10 GbE | J9151A HP | X132 10G SFP+ LC LR 收發器 | HP |
| 22\. |10 GbE | J9283B HP | X242 10G SFP+ SFP+ 3m DAC 纜線 | HP |
| 23\. |10 GbE | J9285B HP | X242 10G SFP+ SFP+ 7m DAC 纜線 | HP |
| 24\. | 10 GbE| JD095B HP | X240 10G SFP+ SFP+ 0.65m DAC 纜線 | HP |
| 25\. | 10 GbE| JD096B HP | X240 10G SFP+ SFP+ 1.2m DAC 纜線 | HP |
| 26\. | 10 GbE| JD097B HP | X240 10G SFP+ SFP+ 3m DAD 纜線 | HP |
| 27\. | 10 GbE| MAM1Q00A-QSA Mellanox | QSFP To SFP+ 配接器 | Mellanox 技術 |
| 28\. | 10 GbE| MC2309124-006 Mt | 被動銅纜線 1x SFP+ To QSFP 10Gb/s 24awg 7m | Mellanox 技術 |
| 29\. | 10 GbE| MC2309124-007 Mt | 被動銅纜線 1x SFP+ To QSFP 10Gb/s 24awg 7m | Mellanox 技術 |
| 30\. | 10 GbE| MC2309130-003 Mt | 被動銅纜線 1x SFP+ To QSFP 10Gb/s 30awg 3m | Mellanox 技術 |
| 31\. | 10 GbE| MC2309130-00A Mt | 被動銅纜線 1x SFP+ To QSFP 10Gb/s 30awg 0.5m | Mellanox 技術 |
| 32\. | 10 GbE| MC3309124-005 Mt | 被動銅纜線 1x SFP+ 10Gb/s 24awg 5m | Mellanox 技術 |
| 33\. | 10 GbE| MC3309124-007 Mt | 被動銅纜線 1x SFP+ 10Gb/s 24awg 7m | Mellanox 技術 |
| 34\. | 10 GbE| MC3309130-003 Mt | 被動銅纜線 1x SFP+ 10Gb/s 30awg 3m | Mellanox 技術 |
| 35\. | 10 GbE| MC3309130-00A Mt | 被動銅纜線 1x SFP+ 10Gb/s 30awg 0.5m | Mellanox 技術 |

### Mellanox 所支援的切換器 

下表列出 Mellanox 所支援的切換器。這些項目尚未經過 Microsoft 測試，但可能使用您的 StorSimple 裝置。

| 序號 | 速度 | 模型 | 說明 | 請確定 |
|--------|-------|-------------|---------------------------------------------------------------------|-------------|
| 1\. | 10GbE | 516733-B21 | HP ProCurve 6120XG 10GbE 乙太網路刀鋒視窗切換器 | HP |
| 2\. | 10GbE | 538113-B21 | HP 10GbE 傳遞模組 (PTM) | HP |
| 3\. | 10GbE | EN4093 | IBM PureFlex 系統網狀架構 EN4093 10 GB 可擴充交換器模組 | IBM |
| 4\. | 1GbE | 3020 | Cisco Catalyst 3020 1GbE 交換器刀鋒視窗 | Cisco |
| 5\. | 1GbE | 3020X | Cisco Catalyst 3020X 1GbE 交換器刀鋒視窗 | Cisco |
| 6\. | 1GbE | 438030-B21 | HP 1GbE 切換器模組 - GbE2c Layer 2/3 乙太網路刀鋒視窗切換器 | HP |
| 7\. | 1GbE | 6120G | HP ProCurve 6120G/XG 1GbE 交換器刀鋒視窗 | HP |

## 後續步驟

[深入了解 StorSimple 硬體元件](https://msdn.microsoft.com/library/azure/dn757755.aspx)

<!---HONumber=July15_HO5-->