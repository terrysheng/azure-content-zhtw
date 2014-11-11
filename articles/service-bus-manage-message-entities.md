<properties linkid="service-bus-manage-messaging-entitites" urlDisplayName="Traffic Manager" pageTitle="Manage Service Bus Messaging Entities - Azure" metaKeywords="" description="Learn how to create and manage your Service Bus entities using the Azure Management Portal." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="service-bus" documentationCenter="" title="How to Manage Service Bus Messaging Entities" authors="sethm" solutions="" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="sethm" />

# 如何管理服務匯流排訊息實體

本主題將說明如何使用 [Azure 管理入口網站][Azure 管理入口網站]來建立及管理您的服務匯流排實體。您可以使用此入口網站建立新的服務命名空間或訊息實體 (查詢、主題或訂閱)。您也可以刪除實體，或變更實體的狀態。

## 目錄

-   [作法：建立服務匯流排實體][作法：建立服務匯流排實體]
-   [作法：刪除服務匯流排實體][作法：刪除服務匯流排實體]
-   [作法：停用或啟用服務匯流排實體][作法：停用或啟用服務匯流排實體]
-   [其他資源][其他資源]

## <span id="create"></span></a>作法：建立服務匯流排實體

Azure 管理入口網站支援兩種建立服務匯流排實體的方式：*快速建立*或*自訂建立*。

### Quick Create

「快速建立」可讓您以一個簡單的步驟建立服務匯流排佇列、主題或轉送服務命名空間。依照下列步驟即可建立服務匯流排實體。

1.  登入 [Azure 管理入口網站][Azure 管理入口網站]。
2.  按一下管理入口網站左下方的 [新增] 圖示。
3.  按一下 [應用程式服務] 圖示，然後按一下 \[服務匯流排佇列] (主題或轉送)。按一下 [快速建立]，然後輸入佇列名稱、區域和 Azure 訂閱 ID。

    a. 如果這是您在所選區域中的第一個命名空間，入口網站會建議命名空間佇列 ; [your entity name]-ns。您可以變更此值。

    b. 如果您在此區域中至少已有一個服務命名空間，則會自動選取命名空間。您可以變更這個選取的命名空間。

4.  按一下 \[建立新的佇列] (或主題) 旁的核取記號。
5.  建立佇列或主題之後，您將會看見「佇列 ‘[Queue Name]’ 建立完成」的訊息。

    a. 如果您在此區域或此 Azure 訂閱中沒有任何命名空間，系統會為您自動建立新的命名空間。在此情況下，您會收到兩則成功訊息：一則是建立命名空間的訊息，一則是建立實體的訊息。

    ![][0]

按一下左側導覽列上的 [服務匯流排] 圖示，以取得命名空間清單。您會看見您剛剛建立的新命名空間。在清單中按一下該命名空間。您會看見您剛剛在該命名空間下建立的實體。

**注意** 該命名空間可能不會立即列出。建立服務命名空間並更新入口網站介面，需要幾秒鐘的時間。

**注意** 使用「快速建立」進行轉送，並不會建立新的轉送端點。它只會建立可讓您以程式設計方式建立轉送端點的命名空間。如需詳細資訊，請參閱[服務匯流排文件][服務匯流排文件]。

### 自訂建立

「自訂建立」是一種較詳盡的方式，可讓您變更所建立之實體 (佇列或主題) 的預設屬性值。若要使用「自訂建立」來建立主題或實體，請執行下列步驟：

1.  登入 [Azure 管理入口網站][Azure 管理入口網站]。
2.  按一下管理入口網站左下方的 [新增]。
3.  按一下 [應用程式服務] 圖示，然後按一下 \[服務匯流排佇列] (主題或轉送)。接著，按一下 [自訂建立]。
4.  在第一個對話畫面中輸入佇列名稱、區域和 Azure 訂閱 ID。

    a. 如果這是您在所選區域中的第一個服務命名空間，入口網站會建議命名空間佇列 ; [your entity name]-ns。您可以變更此值。

    b. 如果您在此區域中至少已有一個命名空間，則會自動選取命名空間。您可以變更這個選取的命名空間。

5.  按 [下一步] 以插入其餘屬性。

    ![][1]

6.  按一下核取記號以建立佇列。

    ![][2]

按一下左側導覽列上的 [服務匯流排] 圖示，以取得服務命名空間清單。您會看見您剛剛建立的新命名空間。在清單中按一下該命名空間。您會看見您剛剛在該命名空間下建立的實體。

## <span id="delete"></span></a>作法：刪除服務匯流排實體

使用此入口網站，可讓您刪除服務匯流排訊息實體。這適用於佇列、主題和主題訂閱。若要刪除佇列或主題，請執行下列動作：

1.  導覽至服務命名空間清單檢視，然後按一下您用來建立實體 (佇列或主題) 的命名空間。
2.  按一下頁面底部的 [刪除] 圖示，然後確認刪除作業。

    ![][3]

**注意** 實體刪除是無法復原的。實體一旦刪除後，即無法復原。但您可以用相同的名稱建立另一個實體。

若要刪除主題訂閱，請執行下列動作：

1.  導覽至命名空間清單檢視，然後按一下您用來建立主題的命名空間。
2.  按一下您用來建立訂閱的主題。
3.  按一下 [訂閱] 索引標籤，然後選取您要刪除的訂閱。
4.  按一下頁面底部的 [刪除] 圖示，然後確認刪除作業。

## <span id="disableenable"></span></a>作法：停用或啟用服務匯流排實體

您可以使用此入口網站變更服務匯流排實體的狀態。這適用於佇列和主題。若要停用或啟用佇列或主題，請執行下列動作：

1.  導覽至服務命名空間清單檢視，然後按一下您用來建立實體 (佇列或主題) 的命名空間。
2.  按一下頁面底部的 \[停用] (或 [啟用])。

    ![][4]

## <span id="seealso"></span></a>其他資源

[Azure 服務匯流排][Azure 服務匯流排]

Azure 網站上的 [.NET 開發人員中心][.NET 開發人員中心]

[建立使用服務匯流排主題和訂閱的應用程式][建立使用服務匯流排主題和訂閱的應用程式]

[佇列、主題和訂閱][佇列、主題和訂閱]

  [Azure 管理入口網站]: http://manage.windowsazure.com
  [作法：建立服務匯流排實體]: #create
  [作法：刪除服務匯流排實體]: #delete
  [作法：停用或啟用服務匯流排實體]: #disableenable
  [其他資源]: #seealso
  [0]: ./media/service-bus-manage-message-entities/QueueQuickCreate.png
  [服務匯流排文件]: http://www.windowsazure.com/zh-TW/develop/net/how-to-guides/service-bus-relay/
  [1]: ./media/service-bus-manage-message-entities/AddQueue1.png
  [2]: ./media/service-bus-manage-message-entities/ConfigureQueue.png
  [3]: ./media/service-bus-manage-message-entities/DeleteEntity.png
  [4]: ./media/service-bus-manage-message-entities/DisableEnable.png
  [Azure 服務匯流排]: http://go.microsoft.com/fwlink/?LinkId=266834
  [.NET 開發人員中心]: http://go.microsoft.com/fwlink/?LinkID=262187
  [建立使用服務匯流排主題和訂閱的應用程式]: http://go.microsoft.com/fwlink/?LinkId=264293
  [佇列、主題和訂閱]: http://go.microsoft.com/fwlink/?LinkId=264291
