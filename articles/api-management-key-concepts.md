<properties pageTitle="API Management key concepts" metaKeywords="" description="Learn about APIs, products, roles, groups, and other API Management key concepts." metaCanonical="" services="" documentationCenter="API Management" title="API Management key concepts" authors="sdanie" solutions="" manager="" editor="" />

<tags ms.service="api-management" ms.workload="mobile" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="sdanie"></tags>

# API 管理主要概念

「API 管理」(預覽) 可協助組織發行 API 給外部合作夥伴和內部開發人員，以發掘其資料與服務的潛力。各地的公司都想要將其作業延伸為數位平台、建立新的管道、尋找新客戶並對現有客戶促進更深入的參與。「API 管理」提供的核心專長認證，透過開發人員參與、商務洞察力、分析、安全性和保護，可確保 API 程式獲致成功。

為了使用 API 管理，管理員會建立 API。每個 API 都包含一或多個作業，而每個 API 則可加入至一或多個產品。為了使用 API，開發人員會訂閱包含該 API 的產品，而且他們之後可呼叫 API 的作業，但需受限於可能生效的任何使用原則。

本主題說明 API 管理的主要概念。

## 本主題內容

-   [API 和作業][]
-   [產品][]
-   [群組][]
-   [開發人員][]
-   [原則][]
-   [開發人員入口網站][]

## <a name="apis"> </a>API 和作業

API 是 API 管理服務執行個體的基礎。每個 API 都代表可供開發人員使用的一組作業。每個 API 都包含會實作 API 之後端服務的參考，而其作業會與後端服務實作的作業相對應。API 管理中的作業可設定度相當高，並可控制 URL 對應、查詢和路徑參數、要求和回應內容，以及作業回應快取。費率限制、配額和 IP 限制原則亦可在 API 或個別作業層級實作。

如需詳細資訊，請參閱[如何建立 API][] 和[如何將作業加入至 API][]。

## <a name="products"> </a>產品

產品是將 API 呈現給開發人員的方式。在 API 管理中的產品包含一或多個 API，並且設定了標題、說明與使用規定。當產品可供開發人員使用時，即可將產品發行。發行產品之後，開發人員即可檢視並訂閱產品。訂閱核准是在產品層級設定，可能需要管理員核准，或是可自動核准。

群組的作用是管理產品對於開發人員的可見度。產品會將可見度授與群組，而開發人員可檢視並訂閱其所屬群組可見的產品。

如需詳細資訊，請參閱[如何建立和發行產品][]。

## <a name="groups"> </a>群組

群組的作用是管理產品對於開發人員的可見度。API 管理具有以下內建群組。

-   **管理員** - 管理員可管理 API 管理服務執行個體、建立開發人員所使用的 API、作業和產品。
-   **開發人員** - 開發人員是使用您的 API 建置應用程式的客戶。開發人員獲授與[開發人員入口網站][]的存取權，並建置呼叫 API 作業的應用程式。
-   **來賓** - 造訪 API 管理開發人員入口網站、未經驗證的使用者 (例如潛在客戶) 的執行個體會歸類到這個群組。他們可獲得特定唯讀存取權限，例如他們可檢視 API 但無法進行呼叫。

除了這些內建的群組以外，管理員還可建立自訂群組。自訂群組與內建的開發人員群組有相同的權限，並且可用來管理開發人員的多個群組。例如，您可以建立一個開發人員的自訂群組，將使用某個產品的 API，而另一個開發人員群組則使用不同產品的 API。

如需詳細資訊，請參閱[如何建立和使用群組][]。

## <a name="developers"> </a>開發人員

開發人員代表 API 管理服務執行個體中的使用者帳戶。開發人員可由管理員建立或邀請加入，也可以透過[開發人員入口網站][]註冊。每個開發人員都是一或多個群組的成員，而且可訂閱對那些群組授與可見度的產品。

當開發人員訂閱產品時，將可獲得產品的主要和次要金鑰。在對產品的 API 進行呼叫時會使用該金鑰。

如需詳細資訊，請參閱[如何建立或邀請開發人員][]和[如何將群組與開發人員建立關聯][]。

## <a name="policies"> </a>原則

原則是 API 管理的強大功能，可讓發行者透過組態變更 API 的行為。原則是陳述式的集合，會因 API 的要求或回應循序執行。常見陳述式包括從 XML 對 JSON 的格式轉換，以及可限制來自開發人員的傳入呼叫數量的呼叫費率限制，而且還有許多原則可供使用。

如需 API 管理原則的完整清單，請參閱[原則參考文件][]。如需使用和設定原則的詳細資訊，請參閱 [API 管理原則][]。如需建立產品並加上費率限制和配額原則的教學課程，請參閱[如何建立和設定進階產品設定][]。

## <a name="developer-portal"> </a>開發人員入口網站

開發人員入口網站是開發人員可了解您的 API、檢視和呼叫作業，以及訂閱產品的地方。潛在客戶可以造訪開發人員入口網站、檢視 API 和作業，以及註冊。開發人員入口網站的 URL 位於 API 管理服務執行個體之 Azure 入口網站的儀表板上。

透過加入自訂內容、自訂樣式和加入自己的品牌，即可自訂開發人員入口網站的外觀及操作。

  [API 和作業]: #apis
  [產品]: #products
  [群組]: #groups
  [開發人員]: #developers
  [原則]: #policies
  [開發人員入口網站]: #developer-portal
  [如何建立 API]: ../api-management-howto-create-apis
  [如何將作業加入至 API]: ../api-management-howto-add-operations
  [如何建立和發行產品]: ../api-management-howto-add-products
  [如何建立和使用群組]: ../api-management-howto-create-groups
  [如何建立或邀請開發人員]: ../api-management-howto-create-or-invite-developers
  [如何將群組與開發人員建立關聯]: ../api-management-howto-create-groups/#associate-group-developer
  [原則參考文件]: ../api-management-policy-reference
  [API 管理原則]: ../api-management-howto-policies
  [如何建立和設定進階產品設定]: ../api-management-howto-product-with-rules
