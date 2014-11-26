<properties pageTitle="Get started with Azure API Management" metaKeywords="" description="Learn how to create APIs, operations, and get started with API Management." metaCanonical="" services="" documentationCenter="API Management" title="Get started with Azure API Management" authors="sdanie" solutions="" manager="" editor="" />

<tags ms.service="api-management" ms.workload="mobile" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="sdanie" />

# 開始使用 Azure API 管理

本指南示範如何快速開始使用 API 管理及進行您的第一個 API 呼叫。

## 本主題內容

-   [建立 API 管理執行個體][建立 API 管理執行個體]
-   [建立 API][建立 API]
-   [加入作業][加入作業]
-   [將新的 API 加入產品][將新的 API 加入產品]
-   [訂閱包含 API 的產品][訂閱包含 API 的產品]
-   [透過開發人員入口網站呼叫作業][透過開發人員入口網站呼叫作業]
-   [檢視分析][檢視分析]
-   [後續步驟][後續步驟]

## <a name="create-service-instance"> </a>建立 API 管理執行個體

> 若要完成此教學課程，您需要 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 [Azure 免費試用][Azure 免費試用]。

使用 API 管理的第一個步驟是建立服務執行個體。登入[管理入口網站][管理入口網站]，並依序按一下 [新增]、[應用程式服務]、[API 管理] 及 [建立]。

![API Management new instance][API Management new instance]

針對 [URL]，請指定要用於服務 URL 的唯一子網域名稱。

選擇您的服務執行個體需要的 [價格層]、[訂閱] 和 [區域]。所有價格層均可用於此教學課程。進行您的選擇之後，請按下一步按鈕。

![New API Management service][New API Management service]

輸入 **Contoso Ltd.** 作為 [組織名稱]，然後在管理員電子郵件欄位中輸入您的電子郵件地址。

> 此電子郵件地址將用於自 API 管理系統傳送通知。如需詳細資訊，請參閱[設定通知][設定通知]。

按一下核取方塊來建立您的服務執行個體。

![New API Management service][1]

![New API Management service][2]

建立服務執行個體之後，下一步是建立 API。

## <a name="create-api"> </a>建立 API

API 包含可自用戶端應用程式叫用的一組作業。API 作業會代理到現有的 Web 服務。

每個 API 管理服務執行個體隨附預先設定的範例 Echo API，該 API 會將傳送至它的輸入傳回。若要使用該 API，您可以叫用任何 HTTP 動詞，而傳回值會等於您所傳送的標頭和本文。

此教學課程使用 <http://echoapi.cloudapp.net/api> Web 服務，在 API 管理中建立名為 **My Echo Service** 的新 API。

API 是透過您經由 Azure 管理入口網站存取的「API 管理」主控台來建立和設定。若要連接「API 管理」主控台，請在 API 管理服務的 Azure 入口網站中按一下 [管理主控台]。

![New API Management console][New API Management console]

若要建立 **My Echo API**，請從左側的 [API 管理] 功能表按一下 [API]，然後按 [加入 API]。

![Create API][Create API]

![Add new API][Add new API]

以下三個欄位可用來設定新 API。

-   在 [Web API Title] 文字方塊中輸入 **My Echo API**。[Web API Title] 提供 API 唯一且描述性的名稱。會顯示在開發人員和管理入口網站中。
-   在 [Web 服務 URL] 中輸入 **<http://echoapi.cloudapp.net/api>**。[Web 服務 URL] 會參考實作 API 的 HTTP 服務。API 管理則將要求轉送至此位址。
-   在 [Web API URL 尾碼] 中輸入 **myecho**。[Web API URL 尾碼] 會附加到 API 管理服務的基礎 URL。您的 API 將共用共同的基礎 URL，而且可由基礎之後附加的唯一尾碼來加以識別。

按一下 [儲存] 建立 API。建立新 API 之後，API 的摘要頁面隨即會顯示在管理入口網站中。

![API summary][API summary]

API 區段有四個索引標籤。\[摘要] 索引標籤顯示基本度量和 API 的相關資訊。[設定] 索引標籤可用來檢視和編輯 API 的組態，包括後端服務的驗證認證。[作業] 索引標籤可用來管理 API 的作業，並用於教學課程中的下列步驟，而 [問題] 索引標籤則可用來檢視使用您的 API 之開發人員回報的問題。

> 範例 echo API 未使用驗證，但如需設定驗證的詳細資訊，請參閱[設定 API 設定][設定 API 設定]。

建立 API 且進行設定之後，下一個步驟是將作業加入至 API。作業定義可用來驗證連入要求，以及自動產生文件。

## <a name="add-operation"> </a>加入作業

按一下 [作業]，以顯示 API 的作業窗格。由於我們尚未加入任何作業，因此不會顯示任何作業。

![Operations][Operations]

按一下 \[加入作業\] 以加入新的作業。將顯示 [新增作業] 視窗，並且預設會選取 [簽章] 索引標籤。

![Operation signature][Operation signature]

在此範例中，我們會對 echo 服務指定 GET 作業。在 [簽章] 索引標籤上的欄位輸入下列值。

-   在 [HTTP 指令動詞] 文字方塊中輸入 **GET**。在您開始輸入時，您可以從顯示的 HTTP 指令動詞清單中選取 **GET**。
-   在 [URL 範本] 文字方塊中輸入 **/resource**。
-   在 [顯示名稱] 文字方塊中輸入 **GET resource**。
-   輸入 **A demonstration of a GET call on a sample resource.It is handled by an "echo" backend which returns a response equal to the request (the supplied headers and body are being returned as received).** 到 [說明] 文字方塊。當開發人員使用此 API 時，會使用此說明來產生此作業的文件。

按一下 [參數] 來設定此作業的查詢字串參數。在此範例中，有兩個查詢字串參數。若要加入查詢參數，請按一下 [加入查詢參數]，並指定下列值。

針對第一個查詢參數，設定下列值。

-   在 [名稱] 文字方塊中輸入 **param1**。
-   在 [說明] 文字方塊中輸入 **A sample parameter that is required.**。
-   按一下 [類型] 欄位，然後從清單選擇 [字串]。支援的類型為：**string**、**number**、**boolean** 和 **dateTime**。
-   按一下 [值] 欄位，在文字方塊中輸入 **sample**，然後按一下加號，將預設值文字加入到參數。加入預設文字之後，按一下 [值] 欄位外的任意位置來關閉加入值視窗。
-   核取 [必要項] 核取方塊。

針對第二個查詢參數，輸入下列值。

-   **名稱**：**param2**
-   **說明**：**Another sample parameter, set to not required.**
-   **類型**：**number**

為作業可能產生的所有狀態碼提供回應範例是良好作法。每個狀態碼可能有不只一個回應本文範例，每個支援的內容類型則會有一個範例。在本教學課程中，我們要加入 **200 OK** 回應碼。

在 [回應] 區段中按一下 [加入]，開始在文字方塊中輸入 **200**，然後從下拉式清單選取 [200 OK]。

![Add response][Add response]

選取 [200 OK] 之後，便會將新回應碼加入至作業，且會顯示回應視窗。在 [說明] 文字方塊中輸入 **Returned in all cases.**。

![Add response][3]

> **Add Representation** 可用來設定多個表示式中的回應。如需詳細資訊，請參閱[回應][回應]。

按一下 [儲存] 將新設定的作業加入至 API。

## <a name="add-api-to-product"> </a>將新的 API 加入產品

開發人員必須先訂閱產品，才能進行 API 呼叫。產品可提供一或多個 API 的存取，並可能包含存取限制 (像是使用量配額和費率限制)。在教學課程的這個步驟中，您會將 My Echo API 加入至現有產品。

從左側的 [API 管理] 功能表按一下 [產品]，以檢視和設定此 API 執行個體中可用的產品。

![Products][Products]

依預設，每個 API 管理執行個體會隨附兩個範例產品：

-   **入門**
-   **無限**

在本教學課程中，我們將使用**入門**產品。按一下 [入門] 來檢視設定，包括與該產品關聯的 API。

![Add API][Add API]

按一下 [加入 API 至產品]。

![Add API][4]

核取 [My Echo API] 的核取方塊，然後按一下 [儲存]。

![API added][API added]

請注意，[My Echo API] 與某個產品相關聯，開發人員可以訂閱產品並開始使用 API。

> 本教學課程步驟會使用「入門」產品，這是經過預先設定而立即可用的產品。如需建立和發行新產品的逐步指南，請參閱[如何建立和發行產品][如何建立和發行產品]。

## <a name="subscribe"> </a>訂閱包含 API 的產品

為了對 API 進行呼叫，開發人員必須先訂閱能夠提供存取權的產品。開發人員可以在開發人員入口網站中訂閱產品，或是，管理員也可以在管理主控台中為開發人員訂閱產品。由於您在教學課程先前的步驟中建立了 API 管理執行個體，因此您預設會是管理員，而會為某個帳戶訂閱**入門**產品。

從左側的 [API 管理] 功能表按一下 [開發人員]，以檢視和設定此服務執行個體的開發人員。

![Developers][Developers]

按一下開發人員的名稱來設定該使用者的設定，包括訂閱。

> 在此範例中，我們要為名為 Clayton Gragg 的開發人員進行訂閱。如果您尚未建立任何開發人員帳戶，則可為管理員帳戶進行訂閱。如需建立開發人員帳戶的詳細資訊，請參閱[如何在 Azure API 管理中管理開發人員帳戶][如何在 Azure API 管理中管理開發人員帳戶]。

![Add subscription][Add subscription]

按一下 [加入訂閱]。

![Add subscription][5]

核取 [入門] 的核取方塊，然後按一下 [訂閱]。

![Subscription added][Subscription added]

為開發人員帳戶進行訂閱之後，您可以呼叫該產品的 API。

## <a name="call-operation"> </a>透過開發人員入口網站呼叫作業

您可以從開發人員入口網站直接呼叫作業，該網站提供檢視及測試 API 作業的便利方式。在本教學課程步驟中，您將會呼叫已加入至 **My Echo API** 的 Get 方法。從管理入口網站右上方的功能表按一下 [開發人員入口網站]。

![Developer portal][Developer portal]

從上方功能表一下 [API]，然後按 [My Echo API] 來查看可用的作業。

![Developer portal][6]

請注意，將會顯示您建立作業時所加入的說明和參數，如此可為要使用此作業之開發人員提供文件。

按一下 [GET Resource]，然後按 [開啟主控台]。

![Operation console][Operation console]

輸入參數的一些值，並指定您的開發人員金鑰，然後按一下 \[HTTP Get\]。

![HTTP Get][HTTP Get]

叫用作業之後，開發人員入口網站會顯示來自後端服務 [要求的 URL]、[回應狀態]、[回應標頭]，以及任何的 [回應內容]。

![Response][Response]

## <a name="view-analytics"> </a>檢視分析

若要檢視 **My Echo API** 的分析，請從開發人員入口網站右上角的使用者功能表選取 \[管理\]，以切換回管理入口網站。

![管理][管理]

管理入口網站的預設檢視為儀表板，其提供 API 管理執行個體的概觀。

![儀表板][儀表板]

將滑鼠移過 My Echo API 圖表上，以查看 API 在指定時段特定度量的使用量。

> 如果您在圖表上看不見任何線條，請切換回開發人員入口網站，並對 API 進行一些呼叫，等候片刻，然後返回儀表板。

![Analytics][Analytics]

按一下 [檢視詳細資料] 來檢視 API 的摘要頁面，包括已顯示度量的較大版本。

![摘要][摘要]

如需詳細度量和報告，請從左側的 [API 管理] 功能表按一下 [分析]。

![概觀][概觀]

[分析] 區段有以下四個索引標籤。

-   **At a glance** 提供整體的使用量和健康情況度量，以及名列前茅的開發人員、產品、API 和作業等相關資訊。
-   **Usage** 提供 API 呼叫和頻寬的深入檢視，包括地理區域的呈現。
-   **Health** 著重在狀態碼、快取成功率、回應時間，以及 API 與服務回應時間。
-   **Activity** 提供的報告依開發人員、產品、API 和作業向下鑽研特定活動。

## <a name="next-steps"> </a>後續步驟

-   在[開始使用進階 API 組態][開始使用進階 API 組態]教學課程中查看其他主題。

  [建立 API 管理執行個體]: #create-service-instance
  [建立 API]: #create-api
  [加入作業]: #add-operation
  [將新的 API 加入產品]: #add-api-to-product
  [訂閱包含 API 的產品]: #subscribe
  [透過開發人員入口網站呼叫作業]: #call-operation
  [檢視分析]: #view-analytics
  [後續步驟]: #next-steps
  [Azure 免費試用]: http://azure.microsoft.com/zh-tw/pricing/free-trial/?WT.mc_id=api_management_hero_a
  [管理入口網站]: https://manage.windowsazure.com/
  [API Management new instance]: ./media/api-management-get-started/api-management-create-instance-menu.png
  [New API Management service]: ./media/api-management-get-started/api-management-create-instance-step1.png
  [設定通知]: ../api-management-howto-configure-notifications
  [1]: ./media/api-management-get-started/api-management-create-instance-step2.png
  [2]: ./media/api-management-get-started/api-management-instance-created.png
  [New API Management console]: ./media/api-management-get-started/api-management-management-console.png
  [Create API]: ./media/api-management-get-started/api-management-create-api.png
  [Add new API]: ./media/api-management-get-started/api-management-add-new-api.png
  [API summary]: ./media/api-management-get-started/api-management-new-api-summary.png
  [設定 API 設定]: ../api-management-howto-create-apis/#configure-api-settings
  [Operations]: ./media/api-management-get-started/api-management-myecho-operations.png
  [Operation signature]: ./media/api-management-get-started/api-management-operation-signature.png
  [Add response]: ./media/api-management-get-started/api-management-add-response.png
  [3]: ./media/api-management-get-started/api-management-add-response-window.png
  [回應]: ../api-management-howto-add-operations/#responses
  [Products]: ./media/api-management-get-started/api-management-list-products.png
  [Add API]: ./media/api-management-get-started/api-management-add-api-to-product.png
  [4]: ./media/api-management-get-started/api-management-add-myechoapi-to-product.png
  [API added]: ./media/api-management-get-started/api-management-api-added-to-product.png
  [如何建立和發行產品]: ../api-management-howto-add-products
  [Developers]: ./media/api-management-get-started/api-management-developers.png
  [如何在 Azure API 管理中管理開發人員帳戶]: ../api-management-howto-create-or-invite-developers/
  [Add subscription]: ./media/api-management-get-started/api-management-add-subscription.png
  [5]: ./media/api-management-get-started/api-management-add-subscription-window.png
  [Subscription added]: ./media/api-management-get-started/api-management-subscription-added.png
  [Developer portal]: ./media/api-management-get-started/api-management-developer-portal-menu.png
  [6]: ./media/api-management-get-started/api-management-developer-portal-myecho-api.png
  [Operation console]: ./media/api-management-get-started/api-management-developer-portal-myecho-api-console.png
  [HTTP Get]: ./media/api-management-get-started/api-management-invoke-get.png
  [Response]: ./media/api-management-get-started/api-management-invoke-get-response.png
  [管理]: ./media/api-management-get-started/api-management-manage-menu.png
  [儀表板]: ./media/api-management-get-started/api-management-dashboard.png
  [Analytics]: ./media/api-management-get-started/api-management-mouse-over.png
  [摘要]: ./media/api-management-get-started/api-management-api-summary-metrics.png
  [概觀]: ./media/api-management-get-started/api-management-analytics-overview.png
  [開始使用進階 API 組態]: ../api-management-get-started-advanced
