<properties pageTitle="How create and publish a product in Azure API Management" metaKeywords="" description="Learn how to create and publish products in Azure API Management." metaCanonical="" services="" documentationCenter="API Management" title="How create and publish a product in Azure API Management" authors="sdanie" solutions="" manager="" editor="" />

<tags ms.service="api-management" ms.workload="mobile" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="sdanie" />

# 如何在 Azure API 管理中建立和發行產品

在 Azure API 管理 (預覽)中，產品包含一或多個 API 以及使用量配額與使用規定。發行產品之後，開發人員便可訂閱產品，並開始使用產品的 API。本主題提供建立產品、加入 API 和發行供開發人員使用的指引。

## 本主題內容

-   [建立產品][建立產品]
-   [將 API 加入至產品][將 API 加入至產品]
-   [將描述性資訊加入至產品][將描述性資訊加入至產品]
-   [發行產品][發行產品]
-   [讓開發人員看見產品][讓開發人員看見產品]
-   [檢視產品的訂閱者][檢視產品的訂閱者]
-   [後續步驟][後續步驟]

## <a name="create-product"> </a>建立產品

請在管理主控台加入和設定 API 的操作。若要存取管理主控台，請在 API 管理服務的 Azure 入口網站中按一下 [管理主控台]。

> 如果您尚未建立 API 管理服務執行個體，請參閱[開始使用 Azure API 管理][開始使用 Azure API 管理]教學課程中的[建立 API 管理服務執行個體][建立 API 管理服務執行個體]。

![API Management console][API Management console]

按一下左邊功能表中的 [產品]，以顯示 [產品] 頁面，然後按一下 [加入產品]。

![產品][產品]

![New product][New product]

在 [名稱] 欄位中輸入產品的描述性名稱，在 [描述] 欄位中輸入產品的描述。

如果您希望管理員檢閱並接受或拒絕對此產品的訂閱嘗試，請核取 [Require subscription approval]。如果未核取方塊，將會自動核准訂閱嘗試。如需訂閱的詳細資訊，請參閱[檢視產品的訂閱者][檢視產品的訂閱者]。

![產品][1]

> 依預設不會發行新產品，且只有 [Administrators] 群組才能看見。

若要設定產品，請在 [產品] 索引標籤中按一下產品名稱。

## <a name="add-apis"> </a>將 API 加入至產品

[產品] 頁面包含四個組態連結：[摘要]、[設定]、[可見度] 和 [開發人員]。[摘要] 索引標籤可讓您加入 API 及發行或取消發行產品。

![摘要][摘要]

發行產品之前，您必須加入一或多個 API。若要這樣做，請安一下 [加入 API 至產品]。

![Add APIs][Add APIs]

選取想要的 API，按一下 [儲存]。

## <a name="add-description"> </a>將描述性資訊加入至產品

[設定] 索引標籤可讓您提供產品的詳細資訊，例如用途、它提供存取的 API 及其他有用的資訊。內容以將會呼叫 API 的開發人員為對象，可使用純文字或 HTML 標記來撰寫。

![Product settings][Product settings]

如果您要手動核准所有產品訂閱要求，請選取 [Require subscription approval]。依預設會自動同意所有產品訂閱。

選擇性填寫 [使用規定] 欄位，描述訂閱者必須接受才可使用產品的產品使用規定。

## <a name="publish-product"> </a>發行產品

產品必須發行，才能呼叫產品中的 API。在產品的 [摘要] 索引標籤上，按一下 [發行]，然後按一下 [是，發行] 表示確認。若要將先前發行的產品設為私人，請按一下 [取消發行]。

![Publish product][Publish product]

## <a name="make-visible"> </a>讓開發人員看見產品

[可見度] 索引標籤可讓選擇哪些角色可在開發人員入口網站上看見產品及訂閱產品。

![Product visibility][Product visibility]

若要允許或不允許群組中的開發人員看見產品，請核取或取消核取群組旁邊的核取方塊，然後按一下 [儲存]。

> 如需詳細資訊，請參閱[如何在 Azure API 管理中建立和使用群組來管理開發人員帳戶][如何在 Azure API 管理中建立和使用群組來管理開發人員帳戶]。

## <a name="view-subscribers"> </a>檢視產品的訂閱者

[開發人員] 索引標籤列出已訂閱產品的開發人員。按一下開發人員的名稱，即可檢視開發人員的詳細資料和設定。在此範例中，還沒有開發人員訂閱產品。

![開發人員][開發人員]

## <a name="next-steps"> </a>後續步驟

加入想要的 API 並發行產品之後，開發人員就可以訂閱產品並開始呼叫 API。如需有關這些項目和進階產品組態的示範教學課程，請參閱[如何在 Azure API 管理中建立和設定進階產品設定][如何在 Azure API 管理中建立和設定進階產品設定]。

  [建立產品]: #create-product
  [將 API 加入至產品]: #add-apis
  [將描述性資訊加入至產品]: #add-description
  [發行產品]: #publish-product
  [讓開發人員看見產品]: #make-visible
  [檢視產品的訂閱者]: #view-subscribers
  [後續步驟]: #next-steps
  [開始使用 Azure API 管理]: ../api-management-get-started
  [建立 API 管理服務執行個體]: ../api-management-get-started/#create-service-instance
  [API Management console]: ./media/api-management-howto-add-products/api-management-management-console.png
  [產品]: ./media/api-management-howto-add-products/api-management-products.png
  [New product]: ./media/api-management-howto-add-products/api-management-add-new-product.png
  [1]: ./media/api-management-howto-add-products/api-management-products-page.png
  [摘要]: ./media/api-management-howto-add-products/api-management-new-product-summary.png
  [Add APIs]: ./media/api-management-howto-add-products/api-management-add-apis-to-product.png
  [Product settings]: ./media/api-management-howto-add-products/api-management-product-settings.png
  [Publish product]: ./media/api-management-howto-add-products/api-management-publish-product.png
  [Product visibility]: ./media/api-management-howto-add-products/api-management-product-visibility.png
  [如何在 Azure API 管理中建立和使用群組來管理開發人員帳戶]: ../api-management-howto-create-groups
  [開發人員]: ./media/api-management-howto-add-products/api-management-developer-list.png
  [如何在 Azure API 管理中建立和設定進階產品設定]: ../api-management-howto-product-with-rules
