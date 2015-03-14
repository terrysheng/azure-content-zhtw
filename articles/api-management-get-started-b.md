<properties 
	pageTitle="開始使用 Azure API 管理" 
	description="了解如何建立 API、操作及開始使用 API 管理。" 
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
	ms.date="2/11/2015" 
	ms.author="sdanie"/>

# 開始使用 Azure API 管理

本指南示範如何快速開始使用 API 管理及進行您的第一個 API 呼叫。

> 若要完成此教學課程，您需要 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 [Azure 免費試用][]。

使用 API 管理的第一個步驟是建立服務執行個體。登入「管理入口網站」[][]，然後依序按一下 [新增]****、[應用程式服務]****、[API 管理]****、[建立]****。

![API Management new instance][api-management-create-instance-menu]

針對 **URL**，指定要用於服務 URL 的唯一子網域名稱。

針對您的服務執行個體，選擇想要的 [訂用帳戶]**** 和 [地區]****。進行您的選擇之後，請按下一步按鈕。

![New API Management service][api-management-create-instance-step1]

輸入 **Contoso Ltd.** 做為 [組織名稱]****，然後在系統管理員電子郵件欄位中輸入您的電子郵件地址。

>此電子郵件地址將用於自 API 管理系統傳送通知。如需詳細資訊，請參閱[設定通知][]。

![New API Management service][api-management-create-instance-step2]

「API 管理」服務執行個體有兩個層次：「開發人員」和「標準」。根據預設，會使用「開發人員」層次建立新的「API 管理」服務執行個體。若要選取 [標準] 層次，請檢查 [進階設定]**** 核取方塊，然後在接下來的畫面中選取 [標準] 層次。

>Microsoft Azure 提供兩個層次供您執行「API 管理」服務：「開發人員」和「標準」。「開發人員」層次可用來針對不注重高可用性的 API 程式進行開發、測試及試驗。在「標準」層次中，您可以調整保留的單位計數以處理更多流量。「標準」層次可為您的「API 管理」服務提供最高的處理能力和效能。您可以使用任一層次來完成本教學課程。如需有關「API 管理」層次的詳細資訊，請參閱 [API 管理定價][]。

按一下核取方塊來建立您的服務執行個體。

![New API Management service][api-management-instance-created]

建立服務執行個體之後，下一步是建立 API。

## <a name="create-api"> </a>建立 API

API 包含可自用戶端應用程式叫用的一組作業。API 作業會代理到現有的 Web 服務。

每個 API 管理服務執行個體都預先設定一個範例 Echo API，可讓您叫用任何 HTTP 指令動詞，而傳回值會等於您所傳送的標頭和本文。本教學課程使用 Echo API 的後端 Web 服務，在「API 管理」中建立一個稱為 **My Echo Service** 的新 API。

API 是透過您經由 Azure 管理入口網站存取的「API 管理」主控台來建立和設定。若要連線至「API 管理」主控台，請在「Azure 入口網站」中按一下您「API 管理」服務的 [管理主控台]****。

![New API Management console][api-management-management-console]

若要建立 **My Echo API**，請從左側的 [API 管理]**** 功能表按一下 [API]****，然後按一下 [加入 API]****。

![Create API][api-management-create-api]

![Add new API][api-management-add-new-api]

以下三個欄位可用來設定新 API。

-	在 [Web API 標題]**** 文字方塊中，輸入 **My Echo API******[Web API 標題] 提供 API 的獨特描述性名稱。此標題會顯示在開發人員和管理入口網站中。
-	在 [Web 服務 URL]**** 中，輸入 **http://echoapi.cloudapp.net/api**。****[Web 服務 URL] 會參考實作 API 的 HTTP 服務。API 管理則將要求轉送至此位址。
-	在 [Web API URL 尾碼]**** 中，輸入 **myecho**。****[Web API URL 尾碼] 會附加至 API 管理服務的基礎 URL。您的 API 將共用共同的基礎 URL，而且可由基礎之後附加的唯一尾碼來加以識別。
-	[Web API 的 URL 配置]**** 會決定可使用哪些通訊協定來存取 API。預設會指定 HTTPs。

按一下 [儲存]**** 以建立 API。建立新 API 之後，API 的摘要頁面隨即會顯示在管理入口網站中。

![API summary][api-management-new-api-summary]


>範例 Echo API 未使用驗證，但如需有關設定驗證的詳細資訊，請參閱[設定 API 設定][]。


## <a name="add-operation"> </a>加入作業

按一下 [作業]**** 以顯示 API 的作業窗格。作業定義可用來驗證連入要求，以及自動產生文件。由於我們尚未加入任何作業，因此不會顯示任何作業。

![Operations][api-management-myecho-operations]

按一下 [加入作業]**** 以加入新的作業。將會顯示 [新增作業]**** 視窗，並預設選取 [簽章]**** 索引標籤。

![Operation signature][api-management-operation-signature]

在此範例中，我們會對 echo 服務指定 GET 作業。在 [簽章]**** 索引標籤上的欄位中輸入下列值。

-	在 [HTTP 指令動詞]**** 文字方塊中，輸入 **GET**。在您開始輸入時，即可以從顯示的 HTTP 指令動詞清單中選取 [GET]****。
-	在 [URL 範本]**** 文字方塊中，輸入 **/resource**。
-	在 [顯示名稱]**** 文字方塊中，輸入 **GET resource**。
-	將 **A demonstration of a GET call on a sample resource.It is handled by an "echo" backend which returns a response equal to the request (the supplied headers and body are being returned as received).** 輸入至 [描述]**** 文字方塊中。當開發人員使用此 API 時，此描述會用來產生此作業的文件。

按一下 [參數]**** 來設定此作業的查詢字串參數。若要加入查詢參數，請按一下 [加入查詢參數]****，然後指定下列值。

-	在 [名稱]**** 文字方塊中，輸入 **param1**。
-	在 [描述]**** 文字方塊中，輸入 **A sample parameter that is required.**。
-	按一下 [類型]**** 欄位，然後從清單中選擇 [string]****。支援的類型為：**string**、**number**、**boolean** 及 **dateTime**。
-	按一下 [值]**** 欄位，在文字方塊中輸入 **sample**，然後按一下加號以將預設值文字加入至參數。加入預設文字之後，按一下 [值]**** 欄位外的任意位置來關閉 [加入值] 視窗。
-	核取 [必要項]**** 核取方塊。

按一下 [儲存]**** 以將新設定的作業加入至 API。


## <a name="add-api-to-product"> </a>將新的 API 加入產品

開發人員必須先訂閱產品，才能進行 API 呼叫。產品可提供一或多個 API 的存取，並可能包含存取限制 (像是使用量配額和費率限制)。在教學課程的這個步驟中，您會將 My Echo API 加入至現有產品。

從左側的 [API 管理]**** 功能表中按一下 [產品]****，以檢視和設定此 API 執行個體中可用的產品。

![Products][api-management-list-products]

依預設，每個 API 管理執行個體會隨附兩個範例產品：

-	**入門**
-	**無限制**

在本教學課程中，我們將使用「入門」****產品。按一下 [入門]**** 來檢視設定，包括與該產品關聯的 API。

![Add API][api-management-add-api-to-product]

按一下 [將 API 加入至產品]****。

![Add API][api-management-add-myechoapi-to-product]

核取 [My Echo API]**** 的核取方塊，然後按一下 [儲存]****。

![API added][api-management-api-added-to-product]

既然 [My Echo API]**** 已經與某個產品關聯，開發人員便可以訂閱它並開始使用 API。

>本教學課程步驟使用「入門」****產品，這是經過預先設定而立即可用的產品。如需有關建立和發行新產品的逐步指南，請參閱[如何建立和發行產品][]。

系統管理員使用者會自動訂閱所有產品，且可存取他們提供存取權的 API。因此，不需要手動訂閱剛建立的產品就可進行呼叫。

## <a name="call-operation"> </a>透過開發人員入口網站呼叫作業

您可以從開發人員入口網站直接呼叫作業，以便檢視和測試 API 的操作。在本教學課程步驟中，您將會呼叫已加入至 [My Echo API]**** 的 Get 方法。從「管理入口網站」右上方的功能表中，按一下 [開發人員入口網站]****。

![Developer portal][api-management-developer-portal-menu]

從上方功能表中按一下 [API]****，然後按一下 [My Echo API]**** 來查看可用的作業。

![Developer portal][api-management-developer-portal-myecho-api]

請注意，將會顯示您建立作業時所加入的說明和參數，如此可為要使用此作業之開發人員提供文件。

按一下 [GET 資源]****，然後按一下 [開啟主控台]****。 

![Operation console][api-management-developer-portal-myecho-api-console]

輸入參數的一些值，並指定您的開發人員金鑰，然後按一下 [HTTP Get]****。

€

叫用作業之後，開發人員入口網站便會顯示來自後端服務的 [要求的 URL]****、[回應狀態]****、[回應標頭]****，以及任何 [回應內容]****。 

![Response][api-management-invoke-get-response]



## <a name="next-steps"> </a>後續步驟

-   設定原則
-   自訂開發人員入口網站
-   使用 API 偵測器來追蹤呼叫

[Azure 免費試用]: http://azure.microsoft.com/pricing/free-trial/

[建立 API 管理執行個體]: #create-service-instance
[建立 API]: #create-api
[加入作業]: #add-operation
[將新的 API 加入產品]: #add-api-to-product
[訂閱包含 API 的產品]: #subscribe
[透過開發人員入口網站呼叫作業]: #call-operation
[檢視分析]: #view-analytics
[後續步驟]: #next-steps

[設定 API 的設定]: ../api-management-howto-create-apis/#configure-api-settings
[設定通知]: ../api-management-howto-configure-notifications
[回應]: ../api-management-howto-add-operations/#responses
[如何建立和發行產品]: ../api-management-howto-add-products
[開始使用進階 API 組態]: ../api-management-get-started-advanced
[API 管理定價]: http://azure.microsoft.com/pricing/details/api-management/
[管理入口網站]: https://manage.windowsazure.com/

[設定原則]: ../api-management-howto-policies
[自訂開發人員入口網站]: ../api-management-customize-portal
[使用 API 偵測器來追蹤呼叫]: ../api-management-howto-api-inspector

[api-management-management-console]: ./media/api-management-get-started-b/api-management-management-console.png
[api-management-create-instance-menu]: ./media/api-management-get-started-b/api-management-create-instance-menu.png
[api-management-create-instance-step1]: ./media/api-management-get-started-b/api-management-create-instance-step1.png
[api-management-create-instance-step2]: ./media/api-management-get-started-b/api-management-create-instance-step2.png
[api-management-instance-created]: ./media/api-management-get-started-b/api-management-instance-created.png
[api-management-create-api]: ./media/api-management-get-started-b/api-management-create-api.png
[api-management-add-new-api]: ./media/api-management-get-started-b/api-management-add-new-api.png
[api-management-new-api-summary]: ./media/api-management-get-started-b/api-management-new-api-summary.png
[api-management-myecho-operations]: ./media/api-management-get-started-b/api-management-myecho-operations.png
[api-management-operation-signature]: ./media/api-management-get-started-b/api-management-operation-signature.png
[api-management-list-products]: ./media/api-management-get-started-b/api-management-list-products.png
[api-management-add-api-to-product]: ./media/api-management-get-started-b/api-management-add-api-to-product.png
[api-management-add-myechoapi-to-product]: ./media/api-management-get-started-b/api-management-add-myechoapi-to-product.png
[api-management-api-added-to-product]: ./media/api-management-get-started-b/api-management-api-added-to-product.png



[api-management-developer-portal-menu]: ./media/api-management-get-started-b/api-management-developer-portal-menu.png
[api-management-developer-portal-myecho-api]: ./media/api-management-get-started-b/api-management-developer-portal-myecho-api.png
[api-management-developer-portal-myecho-api-console]: ./media/api-management-get-started-b/api-management-developer-portal-myecho-api-console.png
[api-management-invoke-get]: ./media/api-management-get-started-b/api-management-invoke-get.png
[api-management-invoke-get-response]: ./media/api-management-get-started-b/api-management-invoke-get-response.png

<!--HONumber=35.2-->

<!--HONumber=46--> 
