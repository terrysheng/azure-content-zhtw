<properties 
	pageTitle="如何在 Azure API 管理中建立和使用群組來管理開發人員帳戶" 
	description="了解如何在 Azure API 管理中使用群組來管理管理開發人員帳戶。"
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

# 如何在 Azure API 管理中建立和使用群組來管理開發人員帳戶

在 API 管理 (預覽) 中，群組的作用是管理產品對於開發人員的可見度。產品是先設為可讓群組看見，然後群組中的開發人員就能檢視和訂閱與群組相關聯的產品。

API 管理具有以下內建群組。

-   **管理員** - 管理員可管理 API 管理服務執行個體、建立開發人員所使用的 API、作業和產品。
-   **開發人員** - 開發人員是使用您的 API 建置應用程式的客戶。開發人員獲授與開發人員入口網站的存取權，並建置呼叫 API 作業的應用程式。
-   **來賓** - 造訪 API 管理開發人員入口網站、未經驗證的使用者 (例如潛在客戶) 的執行個體會歸類到這個群組。他們可獲得特定唯讀存取權限，例如他們可檢視 API 但無法進行呼叫。

除了這些內建的群組以外，管理員還可建立自訂群組。自訂群組與內建的開發人員群組有相同的權限，並且可用來管理開發人員的多個群組。例如，您可以建立一個開發人員的自訂群組，將使用某個產品的 API，而另一個開發人員群組則使用不同產品的 API。

本指南說明 API 管理執行個體的管理員如何加入新的群組，並將這些群組與產品和開發人員建立關聯。

## 本主題內容

-   [建立群組][建立群組]
-   [將群組與產品建立關聯][將群組與產品建立關聯]
-   [將群組與開發人員建立關聯][將群組與開發人員建立關聯]
-   [後續步驟][後續步驟]

## <a name="create-group"> </a>建立群組

若要建立新的群組，請在 API 管理服務的 Azure 入口網站中按一下 [管理主控台]。這會帶您前往 API 管理的管理入口網站。

> 如果您尚未建立 API 管理服務執行個體，請參閱[開始使用 Azure API 管理][開始使用 Azure API 管理]教學課程中的[建立 API 管理服務執行個體][建立 API 管理服務執行個體]。

![API Management console][API Management console]

從左邊的 [API 管理] 功能表中按一下 [群組]，然後按一下 [加入群組]。

![Add new group][Add new group]

輸入群組的唯一名稱和選用的描述，然後按一下 [儲存]。

![Add new group][1]

新群組會顯示在 [群組] 索引標籤中。若要編輯群組的 [名稱] 或 [描述]，請在清單中按一下群組名稱。若要刪除群組，請按一下 [刪除]。

![Group added][Group added]

現在已建立群組，它可以與產品和開發人員建立關聯。

## <a name="associate-group-product"> </a>將群組與產品建立關聯

若要將群組與產品建立關聯，請從左邊的 [API 管理] 功能表中按一下 [產品]，然後按一下所需產品的名稱。

![Set visibility][Set visibility]

選取 [可見度] 索引標籤來加入和移除群組，以及檢視產品的目前群組。若要加入或移除群組，請核取或取消核取所需群組的核取方塊，然後按一下 [儲存]。

![Set visibility][2]

> 若要從產品的 [可見度] 索引標籤中設定群組，請按一下 [管理群組]。

當產品與群組建立關聯之後，該群組中的開發人員就可以檢視和訂閱產品。

## <a name="associate-group-developer"> </a>將群組與開發人員建立關聯

若要將群組與開發人員建立關聯，請從左邊的 [API 管理] 功能表中按一下 [開發人員]，然後核取要與群組建立關聯的開發人員旁邊的核取方塊。

![Add developer to group][Add developer to group]

核取想要的開發人員之後，在 [加入群組] 下拉式清單中按一下所需的群組。使用 [Remove from Group] 下拉式清單，可從群組中移除開發人員。

![開發人員][開發人員]

在開發人員與群組之間加入關聯之後，您可以在 [開發人員] 索引標籤中檢視此關聯。

## <a name="next-steps"> </a>後續步驟

當開發人員加入至群組之後，他們就可以檢視和訂閱與該群組相關聯的產品。如需詳細資訊，請參閱[如何在 Azure API 管理中建立和發行產品][如何在 Azure API 管理中建立和發行產品]。

  [建立群組]: #create-group
  [將群組與產品建立關聯]: #associate-group-product
  [將群組與開發人員建立關聯]: #associate-group-developer
  [後續步驟]: #next-steps
  [開始使用 Azure API 管理]: ../api-management-get-started
  [建立 API 管理服務執行個體]: ../api-management-get-started/#create-service-instance
  [API Management console]: ./media/api-management-howto-create-groups/api-management-management-console.png
  [Add new group]: ./media/api-management-howto-create-groups/api-management-add-group.png
  [1]: ./media/api-management-howto-create-groups/api-management-add-group-window.png
  [Group added]: ./media/api-management-howto-create-groups/api-management-new-group.png
  [Set visibility]: ./media/api-management-howto-create-groups/api-management-add-group-to-product.png
  [2]: ./media/api-management-howto-create-groups/api-management-add-group-to-product-visibility.png
  [Add developer to group]: ./media/api-management-howto-create-groups/api-management-add-group-to-developer.png
  [開發人員]: ./media/api-management-howto-create-groups/api-management-add-group-to-developer-saved.png
  [如何在 Azure API 管理中建立和發行產品]: ../api-management-howto-add-products

<!--HONumber=46--> 
