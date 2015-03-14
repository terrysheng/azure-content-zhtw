<properties 
	pageTitle="API 管理主要概念" 
	description="了解 API、產品、角色、群組及其他 API 管理的主要概念。"
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
	ms.date="11/18/2014" 
	ms.author="sdanie"/>

# 如何在 Azure API 管理中連同操作一起匯入 API 的定義

在 API 管理 (預覽) 中，可手動建立新的 API 並加入操作，或在一個步驟中連同操作一起匯入 API。

您可以使用下列格式來匯入 API 及其操作。

-   WADL
-   Swagger

本指南示範如何在一個步驟中建立新的 API 並匯入其操作。

> 如需有關手動建立 API 和加入操作的資訊，請參閱[如何建立 API][如何建立 API] 和[如何將操作加入至 API][如何將操作加入至 API]。

## 本主題內容

-   [匯入 API][匯入 API]
-   [匯出 API][匯出 API]
-   [後續步驟][後續步驟]

## <a name="import-api"> </a>匯入 API

若要建立和設定 API，請在 API 管理服務的 Azure 入口網站中按一下 [管理主控台]。這會帶您前往 API 管理的管理入口網站。

> 如果您尚未建立 API 管理服務執行個體，請參閱[開始使用 Azure API 管理][開始使用 Azure API 管理]教學課程中的[建立 API 管理服務執行個體][建立 API 管理服務執行個體]。

![管理主控台][管理主控台]

從左邊的 [API 管理] 功能表中按一下 [API]，然後按一下 [匯入 API]。

![Import API][Import API]

[匯入 API] 視窗有三個索引標籤，分別對應至三種提供 API 規格的方式。

-   [From clipboard] 可讓您將 API 規格貼到指定的文字方塊中。
-   [從檔案] 可讓您瀏覽並選取含有 API 規格的檔案。
-   [From URL] 可讓您提供指向 API 規格的 URL。

![Import API format][Import API format]

提供 API 規格之後，請使用右邊的選項按鈕來指出規格格式。支援下列格式。

-   WADL
-   Swagger

接下來，輸入 [Web API URL 尾碼]。這會附加至 API 管理服務的基礎 URL 後面。基礎 URL 是 API 管理服務的每一個執行個體上主控的所有 API 所共有。API 管理依尾碼來區分 API，因此，特定 API 管理服務執行個體中的每一個 API 必須有唯一的尾碼。

輸入所有的值之後，按一下 [儲存]，以建立 API 和相關聯的操作。

## <a name="export-api"> </a> 匯出 API

除了匯入新的 API，您也可以從管理主控台匯出 API 的定義。若要這樣做，請從 **API** 的 [摘要] 索引標籤中按一下 [匯出 API]。

![Export API][Export API]

您可以使用 WADL 或 Swagger 來匯出 API。選取所需的格式，按一下 [儲存]，並選擇用來儲存檔案的位置。

![Export API format][Export API format]

## <a name="next-steps"> </a>後續步驟

建立 API 並匯入操作之後，您就可以檢閱和設定其他任何設定、將 API 加入至產品，以及發行它供開發人員使用。如需詳細資訊，請參閱下列指南。

-   [如何設定 API 設定][如何設定 API 設定]
-   [如何建立和發行產品][如何建立和發行產品]

  [如何建立 API]: ../api-management-howto-create-apis
  [如何將操作加入至 API]: ../api-management-howto-add-operations
  [匯入 API]: #import-api
  [匯出 API]: #export-api
  [後續步驟]: #next-steps
  [開始使用 Azure API 管理]: ../api-management-get-started
  [建立 API 管理服務執行個體]: ../api-management-get-started/#create-service-instance
  [管理主控台]: ./media/api-management-howto-import-api/api-management-management-console.png
  [Import API]: ./media/api-management-howto-import-api/api-management-api-import-apis.png
  [Import API format]: ./media/api-management-howto-import-api/api-management-import-api-wizard.png
  [Export API]: ./media/api-management-howto-import-api/api-management-export-api.png
  [Export API format]: ./media/api-management-howto-import-api/api-management-export-api-format.png
  [如何設定 API 設定]: ../api-management-howto-create-apis/#configure-api-settings
  [如何建立和發行產品]: ../api-management-howto-add-products

<!--HONumber=46--> 
