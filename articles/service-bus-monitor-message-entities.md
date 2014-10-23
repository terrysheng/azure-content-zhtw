<properties linkid="service-bus-monitor-messaging-entitites" urlDisplayName="Traffic Manager" pageTitle="Monitor Service Bus Messaging Entities - Azure" metaKeywords="" description="Learn how to monitor your Service Bus entities using the Azure Management Portal." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="service-bus" documentationCenter="" title="How to Monitor Service Bus Messaging Entities" authors="sethm" solutions="" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="sethm"></tags>

# 如何監視服務匯流排訊息實體

本主題將說明如何使用 [Azure 管理入口網站][]來管理及監視您的服務匯流排實體。此入口網站可讓您詳盡檢視佇列和主題的狀態。您也可以監視其使用情形。

## 如何監視服務匯流排佇列上的活動

若要監視服務匯流排佇列，請執行下列動作：

1.  登入 [Azure 管理入口網站][]。
2.  按一下左側導覽列上的 [服務匯流排] 圖示，以取得服務命名空間清單。
3.  按一下您要監視的佇列所在的命名空間。
4.  在頁面頂端的樞紐列中，按一下 [佇列]。
5.  按一下您要監視之佇列的名稱。佇列儀表板會隨即出現。
6.  您可以在您建立的佇列上檢視活動。您可以檢視多個時間範圍的佇列活動。預設值為 1 小時，但您可以按一下時間旁的下拉式清單，以選擇不同的時間範圍：過去 24 小時、過去 7 天，或過去 30 天。在一小時的時間範圍中，您可以精準度達到每 5 分鐘的測量點精確檢視資料，24 小時的時間範圍則為 1 小時的間隔，7 天和 30 天的時間範圍最小間隔為 1 天。

對於任何佇列，您都可以檢視下列圖表：

-   **傳入訊息**：在此時間間隔內排入佇列的訊息數。
-   **外送訊息**：在此時間間隔內移出佇列的訊息數。
-   **長度**：在此時間間隔結束時位於實體中的訊息數。
-   **大小**：此實體在此時間間隔結束時所使用的儲存空間 (以 MB 為單位)。

### 快速概覽

儀表板上的 [速覽] 會以 [佇列長度] 的形式反映佇列的現行大小。它也會顯示佇列或主題的其他屬性。這項資訊每 10 秒會重新整理一次。

![][]

## 如何監視服務匯流排主題上的活動

若要監視服務匯流排主題，請執行下列動作：

1.  登入 [Azure 管理入口網站][]。
2.  按一下左側導覽列上的 [服務匯流排] 圖示，以取得服務命名空間清單。
3.  按一下您要監視的主題所在的命名空間。
4.  在頁面頂端的樞紐列中，按一下 [主題]。
5.  按一下您要監視之主題的名稱。主題儀表板會隨即出現。

主題儀表板與佇列儀表板相仿，差別在於使用量度量。外送訊息數和長度不會顯示在主題儀表板中，因為這項資訊會隨著主題的每個訂閱而不同。[監視] 索引標籤可讓您依據個別的主題訂閱新增使用量度量 (外送訊息數和長度)。若要新增這些度量，請按一下 [監視] 索引標籤。接著，按一下頁面底部的 [新增度量]，然後選擇主題下的訂閱。

![][1]

  [Azure 管理入口網站]: http://manage.windowsazure.com
  []: ./media/service-bus-monitor-message-entities/QueueDashboard.png
  [1]: ./media/service-bus-monitor-message-entities/AddMetrics.png
