<properties linkid="manage-services-biztalk-services-throttling" urlDisplayName="Throttling" pageTitle="Throttling thresholds in BizTalk Services | Azure" metaKeywords="BizTalk Services, throttling, Azure" description="Learn about throttling thresholds and resulting runtime behaviors for BizTalk Services. Throttling is based on memory usage and number of simultaneous messages." metaCanonical="" services="biztalk-services" documentationCenter="" title="BizTalk Services: Throttling" authors="mandia" solutions="" manager="dwrede" editor="cgronlun" />

<tags ms.service="biztalk-services" ms.workload="integration" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/10/2014" ms.author="mandia"></tags>

# BizTalk 服務：節流

Azure BizTalk 服務根據兩個條件來實作服務節流：記憶體使用量和同時訊息處理數目。本主題列出節流閾值，並說明發生節流狀況時的執行階段行為。

## 節流閾值

下表列出節流來源和閾值：

<table border="1">
<tr bgcolor="FAF9F9">
<th>
</th>
<td>
<b>說明</b>

</td>
<td>
<b>低閾值</b>

</td>
<td>
<b>高閾值</b>

</td>
</tr>
<tr>
<td>
記憶體

</td>
<td>
可用系統記憶體總計的 %/PageFileBytes。<br/><br/>
可用的 PageFileBytes 總計大約是系統 RAM 的 2 倍。
</td>
<td>
60%

</td>
<td>
70%

</td>
</tr>
<tr>
<td>
訊息處理

</td>
<td>
同時處理的訊息數目

</td>
<td>
40 * 核心數目

</td>
<td>
100 * 核心數目

</td>
</tr>
</table>
達到高閾值時，Azure BizTalk 服務就會開始節流。達到低閾值時會停止節流。例如，服務使用 65% 系統記憶體。在此情況下，服務不會節流。服務開始使用 70% 系統記憶體。在此情況下，服務會節流，並持續節流直到服務使用 60% (低閾值) 系統記憶體為止。

Azure BizTalk 服務會追蹤節流狀態 (正常狀態與節流狀態) 和節流持續時間。

## 執行階段行為

Azure BizTalk 服務進入節流狀態時會發生下列情況：

-   依每一角色執行個體來節流。例如：
    RoleInstanceA 節流。RoleInstanceB 未節流。在此情況下，RoleInstanceB 中的訊息如預期般地處理。RoleInstanceA 中的訊息會捨棄且失敗，並傳回下列錯誤：<br/><br/>
    伺服器忙碌中。請再試一次。<br/><br/>
-   所有提取來源不會輪詢或下載訊息。例如：
    管線從外部 FTP 來源提取訊息。進行提取的角色執行個體會進入節流狀態。在此情況下，在角色執行個體停止節流之前，管線會停止下載其他訊息。
-   回應會傳送給用戶端，讓用戶端可以重新提交訊息。
-   您必須等待節流情況解決為止。尤其，您必須等待到達到低閾值為止。

## 重要事項

-   無法停用節流。
-   無法修改節流閾值。
-   節流的實作以全系統為範圍。
-   Azure SQL Database Server 也有內建的節流。

## 其他 Azure BizTalk 服務主題

-   [安裝 Azure BizTalk 服務 SDK][安裝 Azure BizTalk 服務 SDK]
-   [教學課程：Azure BizTalk 服務][教學課程：Azure BizTalk 服務]
-   [如何開始使用 Azure BizTalk 服務 SDK][如何開始使用 Azure BizTalk 服務 SDK]
-   [Azure BizTalk 服務][Azure BizTalk 服務]

## 另請參閱

-   [BizTalk 服務：開發人員、基本、標準和高級版本圖表][BizTalk 服務：開發人員、基本、標準和高級版本圖表]
     (英文)
-   [BizTalk 服務：使用 Azure 管理入口網站進行佈建][BizTalk 服務：使用 Azure 管理入口網站進行佈建]
     (英文)
-   [BizTalk 服務：佈建狀態圖][BizTalk 服務：佈建狀態圖]
     (英文)
-   [BizTalk 服務：儀表板、監視器和調整索引標籤][BizTalk 服務：儀表板、監視器和調整索引標籤]
     (英文)
-   [BizTalk 服務：備份與還原][BizTalk 服務：備份與還原]
     (英文)
-   [BizTalk 服務：簽發者名稱和簽發者金鑰][BizTalk 服務：簽發者名稱和簽發者金鑰]
     (英文)

  [安裝 Azure BizTalk 服務 SDK]: http://go.microsoft.com/fwlink/p/?LinkID=241589
  [教學課程：Azure BizTalk 服務]: http://go.microsoft.com/fwlink/p/?LinkID=236944
  [如何開始使用 Azure BizTalk 服務 SDK]: http://go.microsoft.com/fwlink/p/?LinkID=302335
  [Azure BizTalk 服務]: http://go.microsoft.com/fwlink/p/?LinkID=303664
  [BizTalk 服務：開發人員、基本、標準和高級版本圖表]: http://go.microsoft.com/fwlink/p/?LinkID=302279
  [BizTalk 服務：使用 Azure 管理入口網站進行佈建]: http://go.microsoft.com/fwlink/p/?LinkID=302280
  [BizTalk 服務：佈建狀態圖]: http://go.microsoft.com/fwlink/p/?LinkID=329870
  [BizTalk 服務：儀表板、監視器和調整索引標籤]: http://go.microsoft.com/fwlink/p/?LinkID=302281
  [BizTalk 服務：備份與還原]: http://go.microsoft.com/fwlink/p/?LinkID=329873
  [BizTalk 服務：簽發者名稱和簽發者金鑰]: http://go.microsoft.com/fwlink/p/?LinkID=303941
