<properties pageTitle="How to configure notifications and email templates in Azure API Management" metaKeywords="" description="Learn how to configure notifications and email templates in Azure API Management." metaCanonical="" services="" documentationCenter="API Management" title="How to configure notifications and email templates in Azure API Management" authors="sdanie" solutions="" manager="" editor="" />

<tags ms.service="api-management" ms.workload="mobile" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="sdanie" />

# 如何在 Azure API 管理中設定通知和電子郵件範本

API 管理 (預覽) 可讓您設定特定事件的通知，以及設定用來與 API 管理執行個體的管理員和開發人員通訊的電子郵件範本。本主題說明如何為可用的事件設定通知，並提供設定這些事件所使用之電子郵件範本的概觀。

## 本主題內容

-   [設定發行者通知][設定發行者通知]
-   [設定電子郵件範本][設定電子郵件範本]

## <a name="publisher-notifications"> </a>設定發行者通知

若要設定通知，請在 API 管理服務的 Azure 入口網站中按一下 [管理主控台]。這會帶您前往 API 管理的管理入口網站。

> 如果您尚未建立 API 管理服務執行個體，請參閱[開始使用 Azure API 管理][開始使用 Azure API 管理]教學課程中的[建立 API 管理服務執行個體][建立 API 管理服務執行個體]。

![API Management console][API Management console]

從左邊的 [API 管理] 功能表中按一下 [通知]，以檢視可用的通知。

![Publisher notifications][Publisher notifications]

下列事件清單可設定通知。

-   **訂閱要求 (需要核准)** - 關於需要核准的 API 產品訂閱要求，指定的電子郵件收件者和使用者會收到電子郵件通知。
-   **新的訂閱** - 關於新的 API 產品訂閱，指定的電子郵件收件者和使用者會收到電子郵件通知。
-   **應用程式庫要求** - 當新的應用程式提交至應用程式庫時，指定的電子郵件收件者和使用者會收到電子郵件通知。
-   **BCC** - 指定的電子郵件收件者和使用者會收到所有寄給開發人員的電子郵件的密件副本。
-   **新的問題或意見** - 當開發人員入口網站上提出新的問題或意見時，指定的電子郵件收件者和使用者會收到電子郵件通知。
-   **關閉帳戶訊息** - 有帳戶關閉時，指定的電子郵件收件者和使用者會收到電子郵件通知。
-   **接近訂閱配額限制** - 當訂閱使用量接近使用量配額時，下列電子郵件收件者和使用者會收到電子郵件通知。

針對每一個事件，您可以使用電子郵件地址文字方塊來指定電子郵件地址，或從清單中選取使用者。

若要指定要通知的電子郵件地址，請在電子郵件地址文字方塊中輸入。如果您有多個電子郵件地址，請以逗號分隔。

![Notification recipients][Notification recipients]

若要指定要通知的使用者，請按一下 [add recipient]，選取要通知之使用者旁邊的核取方塊，然後按一下 [確定]。

> 請注意，清單中只顯示系統管理員。

設定通知收件者之後，按一下 [儲存]，套用已更新的通知收件者。

> 如果您離開 [Publisher Notifications] 索引標籤，API 管理入口網站會警告有未儲存的變更。

## <a name="email-templates"> </a>設定電子郵件範本

對於管理和使用服務期間傳送的電子郵件訊息，API 管理提供電子郵件範本。提供的電子郵件範本如下。

-   已核准應用程式庫提交
-   開發人員離職信
-   開發人員接近配額限制通知
-   邀請使用者
-   問題中加入新的意見
-   收到新的問題
-   已啟用新的訂閱
-   確認訂閱已更新
-   拒絕訂閱要求
-   收到訂閱要求

可依需要修改這些範本。

若要檢視和設定 API 管理執行個體的電子郵件範本，請從左邊的 [API 管理] 功能表中按一下 [通知]，然後選取 [電子郵件範本] 索引標籤。

![Email templates][Email templates]

若要檢視或修改特定的範本，請從 [範本] 下拉式清單中選取它。

![Email templates list][Email templates list]

每一個電子郵件範本都有純文字的主旨，以及 HTML 格式的本文定義。可依需要自訂每一個項目。

![Email template editor][Email template editor]

[參數] 清單包含一份參數清單，當插入至主旨或本文時，則會在傳送電子郵件時取代為指定的值。若要插入參數，請將游標移至您要放置參數的地方，然後按一下參數名稱左邊的箭頭。

按一下 [預覽] 或 [Send a test]，以預覽電子郵件的內容或傳送測試電子郵件。

> 請注意，預覽或傳送測試時，不會以實際值來取代參數。
>
> 若要儲存電子郵件範本的變更，請按一下 [儲存]，若要取消變更，請按一下 [取消]。

  [設定發行者通知]: #publisher-notifications
  [設定電子郵件範本]: #email-templates
  [開始使用 Azure API 管理]: ../api-management-get-started
  [建立 API 管理服務執行個體]: ../api-management-get-started/#create-service-instance
  [API Management console]: ./media/api-management-howto-configure-notifications/api-management-management-console.png
  [Publisher notifications]: ./media/api-management-howto-configure-notifications/api-management-publisher-notifications.png
  [Notification recipients]: ./media/api-management-howto-configure-notifications/api-management-email-addresses.png
  [Email templates]: ./media/api-management-howto-configure-notifications/api-management-email-templates.png
  [Email templates list]: ./media/api-management-howto-configure-notifications/api-management-email-templates-list.png
  [Email template editor]: ./media/api-management-howto-configure-notifications/api-management-email-template.png
