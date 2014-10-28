<properties pageTitle="How to create APIs in Azure API Management" metaKeywords="" description="Learn how to create and configure APIs in Azure API Management." metaCanonical="" services="" documentationCenter="API Management" title="How to create APIs in Azure API Management" authors="sdanie" solutions="" manager="" editor="" />

<tags ms.service="api-management" ms.workload="mobile" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="sdanie"></tags>

# 如何在 Azure API 管理中建立 API

API 管理 (預覽) 中的 API 代表可供用戶端應用程式叫用的一組操作。新的 API 是在管理主控台中建立，然後加入所需的操作。加入操作之後，API 就可加入至產品，接著就可發行。API 發行之後，就可供開發人員訂閱和使用。

本指南示範過程中的第一個步驟：如何在 API 管理中建立和設定新 API。如需有關加入操作和發行產品的詳細資訊，請參閱[如何將操作加入至 API][如何將操作加入至 API] 和[如何建立和發行產品][如何建立和發行產品]。

## 本主題內容

-   [建立新的 API][建立新的 API]
-   [設定 API 的設定][設定 API 的設定]
-   [後續步驟][後續步驟]

## <a name="create-new-api"> </a>建立新的 API

若要建立和設定 API，請在 API 管理服務執行個體的 Azure 入口網站中按一下 [管理主控台]。這會帶您前往 API 管理的管理入口網站。

> 如果您尚未建立 API 管理服務執行個體，請參閱[開始使用 Azure API 管理][開始使用 Azure API 管理]教學課程中的[建立 API 管理服務執行個體][建立 API 管理服務執行個體]。

![管理主控台][管理主控台]

從左邊的 [API 管理] 功能表中按一下 [API]，然後按一下 [加入 API]。

![Create API][Create API]

使用 [Add new API] 視窗來設定新的 API。

![Add new API][Add new API]

以下三個欄位可用來設定新 API。

-   [Web API title] 提供 API 的獨特描述性名稱。會顯示在開發人員和管理入口網站中。
-   [Web 服務 URL] 會參考實作 API 的 HTTP 服務。API 管理則將要求轉送至此位址。
-   [Web API URL 尾碼] 會附加到 API 管理服務的基礎 URL。基礎 URL 是 API 管理服務主控的所有 API 所共有。API 管理依尾碼來區分 API，因此，特定發行者的每一個 API 必須有唯一的尾碼。

設定這三個值之後，按一下 [儲存]。建立新 API 之後，API 的摘要頁面隨即會顯示在管理入口網站中。

![API summary][API summary]

## <a name="configure-api-settings"> </a>設定 API 的設定

您可以使用 [設定] 索引標籤來驗證和編輯 API 的組態。[Web API title]、[Web 服務 URL] 和 [Web API URL 尾碼] 最初是在建立 API 時設定，可在這裡修改。[描述] 提供選用的描述，[With credentials] 可讓您設定基本 HTTP 驗證。

![API settings][API settings]

若要為實作 API 的 Web 服務設定 HTTP 基本驗證，請從 [With credentials] 下拉式清單中選取 [基本]，並輸入所需的認證。

![Basic authentication settings][Basic authentication settings]

按一下 [儲存]，儲存您對 API 設定所做的任何變更。

## <a name="next-steps"> </a>後續步驟

建立 API 並完成設定之後，接下來是將操作加入至 API、將 API 加入至產品，以及發行它供開發人員使用。如需詳細資訊，請參閱下列兩個指南。

-   [如何將作業加入至 API][如何將操作加入至 API]
-   [如何建立和發行產品][如何建立和發行產品]

  [如何將操作加入至 API]: ../api-management-howto-add-operations
  [如何建立和發行產品]: ../api-management-howto-add-products
  [建立新的 API]: #create-new-api
  [設定 API 的設定]: #configure-api-settings
  [後續步驟]: #next-steps
  [開始使用 Azure API 管理]: ../api-management-get-started
  [建立 API 管理服務執行個體]: ../api-management-get-started/#create-service-instance
  [管理主控台]: ./media/api-management-howto-create-apis/api-management-management-console.png
  [Create API]: ./media/api-management-howto-create-apis/api-management-create-api.png
  [Add new API]: ./media/api-management-howto-create-apis/api-management-add-new-api.png
  [API summary]: ./media/api-management-howto-create-apis/api-management-api-summary.png
  [API settings]: ./media/api-management-howto-create-apis/api-management-api-settings.png
  [Basic authentication settings]: ./media/api-management-howto-create-apis/api-management-api-settings-credentials.png
