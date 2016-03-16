<properties 
   pageTitle="StorSimple Virtual Array 版本資訊| Microsoft Azure"
   description="描述 StorSimple Virtual Array 的重要議題和解決方式。"
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="01/13/2016"
   ms.author="alkohli" />

# StorSimple Virtual Array 版本資訊 (預覽版) 

## 概觀

下列版本資訊識別 2015 年 12 月公開預覽版的 Microsoft Azure StorSimple Virtual Array (也稱為 StorSimple 內部部署虛擬裝置或 StorSimple 虛擬裝置) 的重要議題。

版本資訊會持續更新，並在發現需要提出因應措施的重大問題時有所增補。部署 StorSimple 虛擬裝置之前，請仔細檢閱版本資訊中所含的資訊。

>[AZURE.IMPORTANT]
>
>- StorSimple Virtual Array 目前是預覽版，僅供評估及部署規劃之用。不支援在生產環境中安裝此預覽版。 
>
>- 如果您遇到任何 StorSimple Virtual Array 的問題，請在 [StorSimple MSDN 論壇](https://social.msdn.microsoft.com/Forums/home?forum=StorSimple)提出問題。

下表提供此版本的已知問題摘要。

| 編號| 功能 | 問題 | 因應措施/註解 |
|----|---------|-------|---------------------|                                                                                                                                                                                                                                                      
| **1.** | 更新 | 預覽版中所建立的虛擬裝置無法更新為支援的正式運作版本。| 必須針對正式運作版本使用災害復原 (DR) 工作流程容錯移轉這些虛擬裝置。|
| **2.** | 階層式磁碟區或共用 | 不支援使用 StorSimple 階層式磁碟區之應用程式的位元組範圍鎖定。如果啟用位元組範圍鎖定，則 StorSimple 分層無法運作。 | 建議的方法包括：<ul><li>關閉應用程式邏輯中的位元組範圍鎖定。</li><li> 選擇將這個應用程式的資料放在固定在本機的磁碟區中，而非階層式磁碟區。</li>*警告*：如果使用固定在本機的磁碟區，並啟用位元組範圍鎖定，請注意固定在本機的磁碟區可以上線，即使在還原完成之前也是一樣。在這種情況下，如果正在還原，則必須等待還原完成。|
| **3.** | 階層式共用 | 使用大型檔案可能會導致緩慢分層輸出。 | 使用大型檔案時，請確定最大檔案小於共用大小的 3%。|
| **4.** | 固定在本機的磁碟區或共用 | 固定在本機的共用或磁碟區的可用容量是已佈建容量的 85-90%。10-15% 範圍保留供中繼資料使用。 | 您需要佈建較大的磁碟區大小，以取得所需的可用容量。例如，1 TB 的可用容量需要佈建 1.15 TB 的磁碟區。|
| **5.** | 本機 Web UI | 如果啟用 Internet Explorer (IE ESC) 中的增強式安全性功能，則部分本機 Web UI 頁面 (例如 [疑難排解] 或 [維護]) 可能無法正常運作。這些頁面上的按鈕也可能無法運作。 | 關閉 Internet Explorer 中的增強式安全性功能。|
| **6.** | 本機 Web UI | 這個版本不支援本機 Web UI 的當地語系化。 | 新版本將會實作這個功能。|
| **7.** | 本機 Web UI | 在 Hyper-V 虛擬機器中，Web UI 中的網路介面會顯示為 10 Gbps 介面。 | 這個行為是 Hyper-V 的反射。Hyper-V 一律會將虛擬網路介面卡顯示為 10 Gbps。|                                                                                                 
| **8.** | 災害復原 | 您只能對與來源裝置網域相同的網域執行檔案伺服器的災害復原。這個版本不支援另一個網域中目標裝置的災害復原。 | 新版本將會實作這個功能。|
| **9.**| 共用的已使用容量| 您可能會在共用上沒有任何資料時看到共用耗用。原因是共用的已使用容量包括中繼資料。 | |                                                                                                                                                                                                                                                         
| **10.** | 時區設定| 如果變更已註冊裝置的時區，則無法將變更反映在透過本機 Web UI 執行的診斷測試中。 | 新版本將會解決此問題。|
| **11.** | 網路設定 | 透過 Web UI 設定網路介面的靜態 IP 位址時，IP 會變更。不過，DNS 伺服器 IP 也會修改成 IPv6 站台本機位址。 | 新版本將會解決此問題。|
| **12.** | Azure PowerShell | 在這版本中，無法透過 Azure PowerShell 管理 StorSimple 虛擬裝置。 | 所有虛擬裝置的管理應該透過 Azure 傳統入口網站和本機 Web UI 來完成。|
| **13.** | 作業 | 工作進度不精確。工作進度可能會從 0 直接跳到 100%。 | 新版本將會解決此問題。|
| **14.** | 佈建的資料磁碟 | 佈建特定指定大小的資料磁碟並建立對應的 StorSimple 虛擬裝置之後，不得展開或壓縮資料磁碟。嘗試這麼做可能會導致遺失裝置本機層中的所有資料。 | |
| **15.** | 説明內容 | 説明內容主題將無法使用。| 所有文件都是透過 Microsoft 下載中心和 Azure 文件網站取得。 |   

<!---HONumber=AcomDC_0121_2016-->