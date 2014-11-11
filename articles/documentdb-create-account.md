<properties title="How to Create a DocumentDB Account" pageTitle="How to create a DocumentDB account | Azure" description="Find out how to create a DocumentDB account and choose account settings in the Azure Preview portal."  metaKeywords="NoSQL, DocumentDB,  database, document-orientated database, JSON, account" services="documentdb"  solutions="data-management" documentationCenter=""  authors="bradsev" manager="jhubbard" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/20/2014" ms.author="bradsev" />

# 如何建立 DocumentDB 帳戶

若要使用 DocumentDB，您必須在可用的地理區域中建立 DocumentDB 帳戶。本主題說明如何在 Azure Preview 管理入口網站中建立 DocumentDB 帳戶。

## 目錄

-   [作法：建立 DocumentDB 帳戶][作法：建立 DocumentDB 帳戶]
-   [後續步驟][後續步驟]

## <span id="HowTo"></span></a>作法：建立 DocumentDB 帳戶

1.  登入 [Azure 管理預覽入口網站][Azure 管理預覽入口網站]
2.  按一下 [新增] -\> [DocumentDB 帳戶]。
    ![][0]

    或者，您也可以瀏覽 Azure 組件庫，選取 [Data, storage, + backup] 類別，選擇 [DocumentDB]，然後按一下 [建立]。

    ![][1]

    在 [New DocumentDB (Preview)] 分頁中，指定想要的 DocumentDB 帳戶組態。

    ![][2]

    在 [名稱] 中，輸入要在 DocumentDB 帳戶的 URI 中使用的名稱。此值會變成用來定址 DocumentDB 帳戶的 URI 內的主機名稱。名稱項目只能包含小寫字母、數字和 '-' 字元，且必須有 3 到 50 個字元。

    *請注意，documents.azure.com 會附加至您選擇的端點名稱後面，形成的結果就成為您的 DocumentDB 帳戶端點。*

    [定價層] 透鏡已鎖定，因為 DocumentDB 預覽僅支援單一標準定價層。

    *如需詳細資訊，請參閱 [DocumentDB 定價詳細資料][DocumentDB 定價詳細資料]*

    [Optional configuration] 透鏡用來指定配置給 DocumentDB 帳戶的初始容量。DocumentDB 利用容量單位讓您調整 DocumentDB 帳戶，每個容量單位包含保留的資料庫儲存體和輸送量。依預設會佈建 1 個容量單位。您隨時都可以透過 [Azure 管理預覽入口網站][Azure 管理預覽入口網站]來調整 DocumentDB 帳戶可用的容量單位數。

    *如需 DocumentDB 帳戶容量和輸送量的詳細資訊，請參閱[管理 DocumentDB 容量和效能][管理 DocumentDB 容量和效能]一文。*

    在 [資源群組] 中，選取或建立 DocumentDB 帳戶的資源群組。依預設會建立新的資源群組。不過，您可以選擇現有的資源群組，以便在其中加入您的 DocumentDB 帳戶。

    *如需詳細資訊，請參閱[使用資源群組管理您的 Azure 資源][使用資源群組管理您的 Azure 資源]。*

    在 [訂用帳戶] 中，選取您要用於 DocumentDB 帳戶的 Azure 訂用帳戶。

    *如果您的帳戶只有一個訂閱，則會自動選取。*

    使用 [位置] 指定將代管您的 DocumentDB 帳戶的地理位置。

3.  設定新的 DocumentDB 帳戶選項之後，按一下 [建立]。建立 DocumentDB 帳戶可能需要數分鐘的時間。若要檢查狀態，您可以監視開始面板上的進度。
    ![][3]

    或是從 [通知中心]。

    ![][4]

    ![][5]

4.  建立好的 DocumentDB 帳戶可立即以預設值來使用。

    *請注意，DocumentDB 帳戶的預設一致性會設為 [階段工作]。您可以透過 [Azure 管理預覽入口網站][Azure 管理預覽入口網站]來調整預設一致性設定。*
    ![][6]

5.  您也可以從 [瀏覽] 分頁存取現有的 DocumentDB 帳戶。
    ![][7]

## <span id="NextSteps"></span></a>後續步驟

-   若要深入了解 DocumentDB，請參閱 [azure.com][azure.com] 上的 Azure DocumentDB 文件

<!--Image references-->

  [作法：建立 DocumentDB 帳戶]: #Howto
  [後續步驟]: #NextSteps
  [Azure 管理預覽入口網站]: https://portal.azure.com/
  [0]: ./media/documentdb-create-account/ca1.png
  [1]: ./media/documentdb-create-account/ca2.png
  [2]: ./media/documentdb-create-account/ca3.png
  [DocumentDB 定價詳細資料]: http://go.microsoft.com/fwlink/p/?LinkID=402317&clcid=0x409
  [管理 DocumentDB 容量和效能]: ../documentdb-manage/
  [使用資源群組管理您的 Azure 資源]: http://azure.microsoft.com/zh-tw/documentation/articles/azure-preview-portal-using-resource-groups/
  [3]: ./media/documentdb-create-account/ca4.png
  [4]: ./media/documentdb-create-account/ca5.png
  [5]: ./media/documentdb-create-account/ca6.png
  [6]: ./media/documentdb-create-account/ca7.png
  [7]: ./media/documentdb-create-account/ca8.png
  [azure.com]: http://go.microsoft.com/fwlink/p/?LinkID=402319
