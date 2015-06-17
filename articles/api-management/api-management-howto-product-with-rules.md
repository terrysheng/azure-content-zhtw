<properties 
	pageTitle="如何在 Azure API 管理中建立和設定進階產品設定" 
	description="了解如何使用配額和速率限制等原則設定產品。" 
	services="api-management" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/11/2015" 
	ms.author="sdanie"/>

# 如何在 Azure API 管理中建立和設定進階產品設定

在 Azure API 管理 (預覽) 中，產品的可設定度相當高，而可滿足 API 發行者的需求。本主題示範如何設定一些進階產品設定，包括費率限制和配額原則。

在本教學課程中，您會建立免費試用的產品 (每分鐘最多允許 10 個呼叫，而每週允許 200 個呼叫)、發行產品，並測試費率限制原則。

## 本主題內容

-   [建立產品][建立產品]
-   [將 API 加入至產品][將 API 加入至產品]
-   [設定呼叫費率限制和配額原則][設定呼叫費率限制和配額原則]
-   [發行產品][發行產品]
-   [為開發人員帳戶訂閱產品][為開發人員帳戶訂閱產品]
-   [呼叫作業並測試費率限制][呼叫作業並測試費率限制]
-   [後續步驟][後續步驟]

## <a name="create-product"> </a>建立產品

在本步驟中，您將建立不需核准訂閱的免費試用產品。

若要開始，請在 API 管理服務的 Azure 入口網站中按一下 [管理主控台]。這會帶您前往 API 管理的管理入口網站。

> 如果您尚未建立 API 管理服務執行個體，請參閱[開始使用 Azure API 管理][開始使用 Azure API 管理]教學課程中的[建立 API 管理服務執行個體][建立 API 管理服務執行個體]。

![API Management console][API Management console]

從左側的 [API 管理] 功能表按一下 [產品]，以顯示 [產品] 頁面。

![Add product][Add product]

按一下 [加入產品] 以顯示 [加入新的產品] 快顯視窗。

![Add new product][Add new product]

在 [標題] 文字方塊中輸入 **Free Trial**。

在 [說明] 文字方塊中輸入 **Subscribers will be able to run 10 calls/minute up to a maximum of 200 calls/week after which access is denied.**。

如果您希望管理員檢閱並接受或拒絕對此產品的訂閱嘗試，請核取 [Require subscription approval]。如果未核取方塊，將會自動核准訂閱嘗試。在此範例中會自動核准訂閱，因此請勿核取方塊。

輸入所有值之後，按一下 [儲存] 來建立產品。

![Product added][Product added]

依預設，**Administrator** 群組中的使用者可看見新產品。我們即將加入 **Developers** 群組。按一下 [免費試用]，並選取 [可見度] 索引標籤。

> 在 API 管理中，群組的作用是管理產品對於開發人員的可見度。產品會將可見度授與群組，而開發人員可檢視並訂閱其所屬群組可見的產品。如需詳細資訊，請參閱[如何在 Azure API 管理中建立和使用群組][如何在 Azure API 管理中建立和使用群組]。

![Add developers group][Add developers group]

核取 [開發人員] 群組，然後按一下 [儲存]。

## <a name="add-api"> </a>將 API 加入至產品

在教學課程的這個步驟中，我們會將 Echo API 加入至新的「免費試用」產品。

> 每個 API 管理服務執行個體隨附預先設定的範例 Echo API，可供您試驗與了解 API 管理。如需詳細資訊，請參閱[開始使用 Azure API 管理][開始使用 Azure API 管理]。

從左側的 [API 管理] 功能表按一下 [產品]，並按 [免費試用] 來設定產品。

![Configure product][Configure product]

按一下 [加入 API 至產品]。

![Add API to product][Add API to product]

核取 [Echo API] 旁的核取方塊，然後按一下 [儲存]。

![Add Echo API][Add Echo API]

## <a name="policies"> </a>設定呼叫費率限制和配額原則

費率限制和配額是在原則編輯器中設定。從左側的 [API 管理] 功能表按一下 [原則]，並從 [免費試用] 選取 [Policy Scope Product] 下拉式清單來設定產品。

![Product policy][Product policy]

按一下 [加入原則] 來匯入原則範本，並開始建立費率限制和配額原則。

![Add policy][Add policy]

若要插入原則，請將游標放在原則範本的 [輸入] 或 [輸出] 區段上。費率限制和配額原則為輸入原則，因此將游標放置在輸入元素中。

![Policy editor][Policy editor]

我們要在本教學課程中加入的兩個原則為**限制呼叫費率**和**設定使用量配額**原則。

![Policy statements][Policy statements]

游標放置在 [輸入] 原則元素中之後，按一下 [限制呼叫費率] 旁的箭頭來插入其原則範本。

    <rate-limit calls="number" renewal-period="seconds">
    <api name="name" calls="number">
    <operation name="name" calls="number" />
    </api>
    </rate-limit>

**限制呼叫費率**可用在產品層級，也可以用在 API 和個別作業名稱層級。在本教學課程中只會使用產品層級原則，因此請從 **rate-limit** 元素刪除 **api** 和 **operation** 元素，如以下範例所示。

    <rate-limit calls="number" renewal-period="seconds">
    </rate-limit>

在**免費試用**產品中，允許的呼叫費率為每分鐘 10 個呼叫，因此請輸入 **10** 做為呼叫屬性的值，以及 **60** 做為 **renewal-period** 屬性。

    <rate-limit calls="10" renewal-period="60">
    </rate-limit>

若要設定**設定使用量配額**原則，請將游標放置在 **inbound** 元素內緊接著新建立的 **rate-limit** 元素下，然後按一下 [設定使用量配額] 左側的箭頭。

    <quota calls="number" bandwidth="kilobytes" renewal-period="seconds">
    <api name="name" calls="number" bandwidth="kilobytes">
    <operation name="name" calls="number" bandwidth="kilobytes" />
    </api>
    </quota>

因為此原則也意欲用於產品層級，請刪除 **api** 和 **operation** 名稱元素，如以下範例所示。

    <quota calls="number" bandwidth="kilobytes" renewal-period="seconds">
    </quota>

配額可能是基於每一間隔的呼叫數、頻寬，或兩者。在本教學課程中，我們不會基於頻寬進行節流，因此請刪除 **bandwidth** 屬性。

    <quota calls="number" renewal-period="seconds">
    </quota>

在**免費試用**產品中，配額為每週 200 個呼叫。指定 **200** 作為呼叫屬性的值，並指定 **604800** 作為 renewal-period 的值。

    <quota calls="200" bandwidth="kilobytes" renewal-period="604800">
    </quota>

> 原則間隔是依秒來指定。若要計算一週的間隔，您可以將天數 (7) x 一天的小時數 (24) x 每小時的分鐘數 (60) x 每分鐘的秒數 (60)。 7 * 24 * 60 * 60 = 604800.

完成設定原則之後，應該符合以下範例。

    <policies>
        <inbound>
            <rate-limit calls="10" renewal-period="60">
            </rate-limit>
            <quota calls="200" renewal-period="604800">
            </quota>
            <base />
        
    </inbound>
    <outbound>
        
        <base />
        
        </outbound>
    </policies>

設定需要的原則之後，請按一下 [儲存]。

![Save policy][Save policy]

## <a name="publish-product"> </a>發行產品

現在已加入 API，也已設定原則，產品即已備妥可供開發人員使用。在產品可供開發人員使用之前，必須先發行產品。從左側的 [API 管理] 功能表按一下 [產品]，並按 [免費試用] 來設定產品。

![Configure product][Configure product]

按一下 [發行]，然後按 [是，發行] 確認。

![Publish product][Publish product]

## <a name="subscribe-account"> </a>為開發人員帳戶訂閱產品

現在已發行產品，產品即可供開發人員訂閱和使用。

> API 管理的管理員執行個體會自動訂閱每個產品。在這個教學課程步驟中，我們會將其中一個非管理員開發人員帳戶訂閱「免費試用」產品。如果您的開發人員帳戶隸屬於管理員角色，即使您已訂閱，也可以遵循此步驟。

在左側的 [API 管理] 功能表上按一下 [開發人員]，然後按您的開發人員帳戶的名稱。在此範例中，我們要使用 **Clayton Gragg** 開發人員帳戶。

![Configure developer][Configure developer]

按一下 [加入訂閱]。

![Add subscription][Add subscription]

核取 [免費試用] 旁的核取方塊，然後按一下 [訂閱]。

![Add subscription][1]

## <a name="test-rate-limit"> </a>呼叫作業並測試費率限制

現在免費試用產品已設定和發行，我們可以呼叫一些作業並測試費率限制原則。在右上角的功能表中按一下 [開發人員入口網站]，切換到開發人員入口網站。

![Developer portal][Developer portal]

在上方功能表中按一下 [API]，然後選取 [Echo API]。

![Developer portal][2]

> 如果您的帳戶只設定或只看見一個 API，按一下 API 將帶您直接前往該 API 的作業。

選取 [GET Resource] 作業，然後按一下 [開啟主控台]。

![Open console][Open console]

保留預設的參數值，並選取您的**免費試用**產品的訂閱金鑰。

![Subscription key][Subscription key]

> 如果您有多個訂閱，請務必選取**免費試用**的金鑰，否則在先前步驟中設定的原則將不會生效。

按一下 [HTTP Get]，然後檢視回應。記下 [200 OK] 的 [回應狀態]。

![Operation results][Operation results]

按一下 [HTTP Get]，將費率設為大於每分鐘 10 個呼叫的費率限制原則。超出費率限制原則時，會傳回 [429 Too many Requests] 回應狀態。

![Operation results][3]

[回應標頭] 和 [回應內容] 指出重試會成功的剩餘間隔時間。

每分鐘 10 個呼叫的費率限制原則生效時，在超出費率限制之後，後續的呼叫會在對產品的 10 個成功呼叫的第一個經過 60 秒後失敗。在此範例中，剩餘的間隔時間為 43 秒。

## <a name="next-steps"> </a>後續步驟

-   在[開始使用進階 API 組態][開始使用進階 API 組態]教學課程中查看其他主題。

  [建立產品]: #create-product
  [將 API 加入至產品]: #add-api
  [設定呼叫費率限制和配額原則]: #policies
  [發行產品]: #publish-product
  [為開發人員帳戶訂閱產品]: #subscribe-account
  [呼叫作業並測試費率限制]: #test-rate-limit
  [後續步驟]: #next-steps
  [開始使用 Azure API 管理]: ../api-management-get-started
  [建立 API 管理服務執行個體]: ../api-management-get-started/#create-service-instance
  [API Management console]: ./media/api-management-howto-product-with-rules/api-management-management-console.png
  [Add product]: ./media/api-management-howto-product-with-rules/api-management-add-product.png
  [Add new product]: ./media/api-management-howto-product-with-rules/api-management-new-product-window.png
  [Product added]: ./media/api-management-howto-product-with-rules/api-management-product-added.png
  [如何在 Azure API 管理中建立和使用群組]: ../api-management-howto-create-groups
  [Add developers group]: ./media/api-management-howto-product-with-rules/api-management-add-developers-group.png
  [Configure product]: ./media/api-management-howto-product-with-rules/api-management-configure-product.png
  [Add API to product]: ./media/api-management-howto-product-with-rules/api-management-add-api.png
  [Add Echo API]: ./media/api-management-howto-product-with-rules/api-management-add-echo-api.png
  [Product policy]: ./media/api-management-howto-product-with-rules/api-management-product-policy.png
  [Add policy]: ./media/api-management-howto-product-with-rules/api-management-add-policy.png
  [Policy editor]: ./media/api-management-howto-product-with-rules/api-management-policy-editor-inbound.png
  [Policy statements]: ./media/api-management-howto-product-with-rules/api-management-limit-policies.png
  [Save policy]: ./media/api-management-howto-product-with-rules/api-management-policy-save.png
  [Publish product]: ./media/api-management-howto-product-with-rules/api-management-publish-product.png
  [Configure developer]: ./media/api-management-howto-product-with-rules/api-management-configure-developer.png
  [Add subscription]: ./media/api-management-howto-product-with-rules/api-management-add-subscription-menu.png
  [1]: ./media/api-management-howto-product-with-rules/api-management-add-subscription.png
  [Developer portal]: ./media/api-management-howto-product-with-rules/api-management-developer-portal-menu.png
  [2]: ./media/api-management-howto-product-with-rules/api-management-developer-portal-api-menu.png
  [Open console]: ./media/api-management-howto-product-with-rules/api-management-open-console.png
  [Subscription key]: ./media/api-management-howto-product-with-rules/api-management-select-key.png
  [Operation results]: ./media/api-management-howto-product-with-rules/api-management-http-get-results.png
  [3]: ./media/api-management-howto-product-with-rules/api-management-http-get-429.png
  [開始使用進階 API 組態]: ../api-management-get-started-advanced

<!--HONumber=46--> 
 