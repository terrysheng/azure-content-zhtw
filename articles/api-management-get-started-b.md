<properties pageTitle="Get started with Azure API Management" metaKeywords="" description="Learn how to create APIs, operations, and get started with API Management." metaCanonical="" services="" documentationCenter="API Management" title="Get started with Azure API Management" authors="sdanie" solutions="" manager="" editor="" />

# 開始使用 Azure API 管理

本指南示範如何快速開始使用 API 管理及進行您的第一個 API 呼叫。

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

每個 API 管理服務執行個體都預先設定一個範例 Echo API，可讓您叫用任何 HTTP 指令動詞，而傳回值會等於您所傳送的標頭和本文。本教學課程使用 Echo API 的後端 Web 服務，在 API 管理中建立一個稱為 **My Echo Service** 的新 API。

API 是透過您經由 Azure 管理入口網站存取的「API 管理」主控台來建立和設定。若要連接「API 管理」主控台，請在 API 管理服務的 Azure 入口網站中按一下 [管理主控台]。

![New API Management console][New API Management console]

若要建立 **My Echo API**，請從左側的 [API 管理] 功能表按一下 [API]，然後按 [加入 API]。

![Create API][Create API]

![Add new API][Add new API]

以下三個欄位可用來設定新 API。

-   在 [Web API Title] 文字方塊中輸入 **My Echo API**。[Web API Title] 提供 API 的獨特描述性名稱。會顯示在開發人員和管理入口網站中。
-   在 [Web 服務 URL] 中輸入 **<http://echoapi.cloudapp.net/api>**。[Web 服務 URL] 會參考實作 API 的 HTTP 服務。API 管理則將要求轉送至此位址。
-   在 [Web API URL 尾碼] 中輸入 **myecho**。[Web API URL 尾碼] 會附加到 API 管理服務的基礎 URL。您的 API 將共用共同的基礎 URL，而且可由基礎之後附加的唯一尾碼來加以識別。

按一下 [儲存] 建立 API。建立新 API 之後，API 的摘要頁面隨即會顯示在管理入口網站中。

![API summary][API summary]

> 範例 echo API 未使用驗證，但如需設定驗證的詳細資訊，請參閱[設定 API 設定][設定 API 設定]。

## <a name="add-operation"> </a>加入作業

按一下 [作業]，以顯示 API 的作業窗格。作業定義可用來驗證連入要求，以及自動產生文件。由於我們尚未加入任何作業，因此不會顯示任何作業。

![Operations][Operations]

按一下 [加入作業] 以加入新的作業。將顯示 [新增作業] 視窗，並且預設會選取 [簽章] 索引標籤。

![Operation signature][Operation signature]

在此範例中，我們會對 echo 服務指定 GET 作業。在 [簽章] 索引標籤上的欄位輸入下列值。

-   在 [HTTP 指令動詞] 文字方塊中輸入 **GET**。在您開始輸入時，您可以從顯示的 HTTP 指令動詞清單中選取 **GET**。
-   在 [URL 範本] 文字方塊中輸入 **/resource**。
-   在 [顯示名稱] 文字方塊中輸入 **GET resource**。
-   輸入 **A demonstration of a GET call on a sample resource.It is handled by an "echo" backend which returns a response equal to the request (the supplied headers and body are being returned as received).** 到 [說明] 文字方塊。當開發人員使用此 API 時，會使用此說明來產生此作業的文件。

按一下 [參數] 來設定此作業的查詢字串參數。若要加入查詢參數，請按一下 [加入查詢參數]，並指定下列值。

-   在 [名稱] 文字方塊中輸入 **param1**。
-   在 [說明] 文字方塊中輸入 **A sample parameter that is required.**。
-   按一下 [類型] 欄位，然後從清單選擇 [字串]。支援的類型為：**string**、**number**、**boolean** 和 **dateTime**。
-   按一下 [值] 欄位，在文字方塊中輸入 **sample**，然後按一下加號，將預設值文字加入到參數。加入預設文字之後，按一下 [值] 欄位外的任意位置來關閉加入值視窗。
-   核取 [必要項] 核取方塊。

按一下 [儲存] 將新設定的作業加入至 API。

## <a name="add-api-to-product"> </a>將新的 API 加入產品

開發人員必須先訂閱產品，才能進行 API 呼叫。產品可提供一或多個 API 的存取，並可能包含存取限制 (像是使用量配額和費率限制)。在教學課程的這個步驟中，您會將 My Echo API 加入至現有產品。

從左側的 [API 管理] 功能表按一下 [產品]，以檢視和設定此 API 執行個體中可用的產品。

![產品][產品]

依預設，每個 API 管理執行個體會隨附兩個範例產品：

-   **入門**
-   **無限**

在本教學課程中，我們將使用**入門**產品。按一下 [入門] 來檢視設定，包括與該產品關聯的 API。

![Add API][Add API]

按一下 [加入 API 至產品]。

![Add API][3]

核取 [My Echo API] 的核取方塊，然後按一下 [儲存]。

![API added][API added]

請注意，[My Echo API] 與某個產品相關聯，開發人員可以訂閱產品並開始使用 API。

> 本教學課程步驟使用「入門」產品，這是經過預先設定而立即可用的產品。如需建立和發行新產品的逐步指南，請參閱[如何建立和發行產品][如何建立和發行產品]。

系統管理員使用者會自動訂閱所有產品，且可存取他們提供存取權的 API。因此，不需要手動訂閱剛建立的產品就可進行呼叫。

## <a name="call-operation"> </a>透過開發人員入口網站呼叫作業

您可以從開發人員入口網站直接呼叫作業，以便檢視和測試 API 的操作。在本教學課程步驟中，您將會呼叫已加入至 **My Echo API** 的 Get 方法。從管理入口網站右上方的功能表按一下 [開發人員入口網站]。

![開發人員入口網站][開發人員入口網站]

從上方功能表一下 [API]，然後按 [My Echo API] 來查看可用的作業。

![開發人員入口網站][4]

請注意，將會顯示您建立作業時所加入的說明和參數，如此可為要使用此作業之開發人員提供文件。

按一下 [GET Resource]，然後按 [開啟主控台]。

![Operation console][Operation console]

輸入參數的一些值，並指定您的開發人員金鑰，然後按一下 [HTTP Get]。

![HTTP Get][HTTP Get]

叫用作業之後，開發人員入口網站會顯示來自後端服務 [要求的 URL]、[回應狀態]、[回應標頭]，以及任何的 [回應內容]。

![Response][Response]

## <a name="next-steps"> </a>後續步驟

-   設定原則
-   自訂開發人員入口網站
-   使用 API 偵測器來追蹤呼叫

  [Azure 免費試用]: http://www.windowsazure.com/zh-tw/pricing/free-trial/
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
  [產品]: ./media/api-management-get-started/api-management-list-products.png
  [Add API]: ./media/api-management-get-started/api-management-add-api-to-product.png
  [3]: ./media/api-management-get-started/api-management-add-myechoapi-to-product.png
  [API added]: ./media/api-management-get-started/api-management-api-added-to-product.png
  [如何建立和發行產品]: ../api-management-howto-add-products
  [開發人員入口網站]: ./media/api-management-get-started/api-management-developer-portal-menu.png
  [4]: ./media/api-management-get-started/api-management-developer-portal-myecho-api.png
  [Operation console]: ./media/api-management-get-started/api-management-developer-portal-myecho-api-console.png
  [HTTP Get]: ./media/api-management-get-started/api-management-invoke-get.png
  [Response]: ./media/api-management-get-started/api-management-invoke-get-response.png
