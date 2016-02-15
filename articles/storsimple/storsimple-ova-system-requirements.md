<properties
   pageTitle="StorSimple Virtual Array 系統需求"
   description="了解 StorSimple Virtual Array 的軟體和網路功能需求"
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="01/07/2016"
   ms.author="alkohli"/>

# StorSimple Virtual Array 系統需求 (預覽版)

## 概觀

本文說明 Microsoft Azure StorSimple Virtual Array (也稱為 StorSimple 內部部署虛擬裝置或 StorSimple 虛擬裝置) 和存取陣列的儲存體用戶端重要的系統需求。建議您先仔細檢閱資訊，再部署 StorSimple 系統，然後在部署和後續作業期間，必要時回顧參考。

系統需求包括：

-   **儲存體用戶端的軟體需求** - 描述支援的虛擬化平台、網頁瀏覽器、iSCSI 啟動器、SMB 用戶端、基本虛擬裝置需求和那些作業系統的任何額外需求。

-   **StorSimple 裝置的網路需求** - 提供需要在防火牆中開啟以允許 iSCSI、雲端或管理流量的連接埠相關資訊。

這篇文章中的 StorSimple 系統需求資訊，僅適用於 StorSimple Virtual Array。

- 針對 8000 系列裝置，請移至 [StorSimple 8000 系列裝置的系統需求](storsimple-system-requirements.md)。
 
- 針對 7000 系列裝置，請移至 [System requirements for your StorSimple 5000-7000 series device (StorSimple 5000-7000 系列裝置的系統需求)](http://onlinehelp.storsimple.com/1_StorSimple_System_Requirements)。

> [AZURE.IMPORTANT]
> 
> - 此公開預覽版僅供評估之用。不支援在生產環境中安裝此預覽版。
> - 如果您遇到任何 StorSimple Virtual Array 的問題，請在 [StorSimple MSDN 論壇](https://social.msdn.microsoft.com/Forums/zh-TW/home?forum=StorSimple)提出問題。  

## 軟體需求

軟體需求包括支援的網頁瀏覽器、SMB 版本、虛擬化平台和最低虛擬裝置需求的資訊。

### 支援的虛擬化平台


| **Hypervisor** | **版本** |
|----------------|--------------------------------------|
| Hyper-V | Windows Server 2008 R2 SP1 和更新版本 |
| VMware ESXi | 5\.5 和更新版本 |

### 虛擬裝置需求

| **元件** | **需求** |
|----------------------------------------------|----------------------------|
| 虛擬處理器 (核心) 的最小數目 | 4 |
| 最小記憶體 (RAM) | 8 GB |
| 磁碟空間<sup>1</sup> | OS 磁碟 - 80 GB <br></br>資料磁碟 - 500 GB 到 8 TB|
| 最小網路介面數目 | 1 |
| 基本網際網路頻寬<sup>2</sup> | 5 Mbps |

<sup>1</sup> - 精簡佈建

<sup>2</sup> - 網路需求可能會依據每日資料變更率而有所不同。例如，如果裝置一天需要備份 10 GB 或更多的變更，則透過 5 Mbps 連線的每日備份最多可能需要 4.25 小時 (如果資料無法壓縮或刪除重複資料)。

### 支援的網頁瀏覽器

| **元件** | **版本** | **其他需求/注意事項** |
|-------------------|-----------------|-----------------------------------|
| Microsoft Edge | 最新版本 | |
| Internet Explorer | 最新版本 | 通過 Internet Explorer 11 測試 |
| Google Chrome | 最新版本 | 通過 Chrome 46 測試 |

### 支援的 SMB 版本

| **版本** |
|-------------|
| SMB 2.x |
| SMB 3.0 |
| SMB 3.02 |

## 網路需求 

下表列出必須在您的防火牆中開啟的連接埠，以允許 iSCSI、SMB、雲端或管理流量。在這個資料表中，*in* 或 *inbound* 指的是輸入用戶端要求存取裝置的方向。*Out* 或 *outbound* 指的是 StorSimple 裝置於外部傳送資料至部署之上的方向：例如，輸出到網際網路。

| **連接埠號碼<sup>1</sup>** | **內或外** | **連接埠範圍** | **必要** | **注意事項** |
|--------------------------|---------------|----------------|---------------------------|----------------------------------------------------------------------------------------------------------------------|
| TCP 80 (HTTP) | 外 | WAN | 否 | 輸出連接埠用於網際網路存取以擷取更新。<br></br>輸出 Web Proxy 可由使用者設定。 |
| TCP 443 (HTTPS) | 外 | WAN | 是 | 輸出連接埠用來存取雲端中的資料。<br></br>輸出 Web Proxy 可由使用者設定。 |
| UDP 53 (DNS) | 外 | WAN | 在某些情況下，請參閱附註。 | 只有當您使用網際網路 DNS 伺服器時，才需要此連接埠。<br></br> **注意**：如果部署的是檔案伺服器，建議使用本機 DNS 伺服器。|
| UDP 123 (NTP) | 外 | WAN | 在某些情況下，請參閱附註。 | 只有當您使用網際網路 NTP 伺服器時，才需要此連接埠。<br></br> **附註：**如果部署的是檔案伺服器，建議您和 Active Directory 網域控制站同步時間。 |
|TCP 9354 | 外 | WAN | 是 | StorSimple 裝置使用輸出連接埠與 StorSimple Manager 服務通訊。|
| TCP 80 (HTTP) | 在 | LAN | 是 | 這是 StorSimple 裝置上用於本機管理的本機 UI 的輸入連接埠。<br></br> **注意**：透過 HTTP 存取本機 UI 會自動重新導向至 HTTPS。|
| TCP 443 (HTTPS) | 在 | LAN | 是 | 這是 StorSimple 裝置上用於本機管理的本機 UI 的輸入連接埠。|
| TCP 3260 (iSCSI) | 在 | LAN | 否 | 此連接埠用來透過 iSCSI 存取資料。|

<sup>1</sup> 公用網際網路上沒有必須開啟的輸入連接埠。

## 後續步驟

-   [Prepare the portal to deploy your StorSimple Virtual Array (準備入口網站以部署 StorSimple Virtual Array)](storsimple-ova-deploy1-portal-prep.md)

<!---HONumber=AcomDC_0204_2016-->