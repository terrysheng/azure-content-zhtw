<properties title="How to Create a DocumentDB Account" pageTitle="建立資料庫帳戶 | Azure" description="了解如何在 Azure Preview 入口網站中建立 DocumentDB NoSQL 文件資料庫帳戶及選擇帳戶設定。"  metaKeywords="NoSQL, DocumentDB, database, document-orientated database, JSON, account" services="documentdb"  solutions="data-management" documentationCenter=""  authors="mimig" manager="jhubbard" editor="monicar" videoId="" scriptId="" />

<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/09/2014" ms.author="mimig" />

#建立資料庫帳戶
若要使用 Microsoft Azure DocumentDB，您必須建立 DocumentDB 帳戶。本主題說明如何在 Azure Preview 管理入口網站中建立 DocumentDB 帳戶。  


1.	登入 [Azure 管理入口網站](https://portal.azure.com/#gallery/Microsoft.DocumentDB)。
2.	按一下 [新增] -> [DocumentDB 帳戶]。  
	![][1]  

	或者，您也可以從「開始面板」瀏覽 Azure Marketplace，選取 [資料 + 分析] 類別，選擇 [DocumentDB]****，然後按一下 [建立]****。  

	![][2]   

3. 在 [新增 DocumentDB (預覽)]**** 刀鋒視窗中，指定想要的 DocumentDB 帳戶組態。 
 
	![][3] 


	- 在 [識別碼]**** 方塊中，輸入用來識別 DocumentDB 帳戶的名稱。這個值會變成 URI 中的主機名稱。此識別碼只能包含小寫字母、數字及 '-' 字元，且長度必須為 3 到 50 個字元。請注意，documents.azure.com 會附加至您選擇的端點名稱後面，產生的結果將成為您的 DocumentDB 帳戶端點。

	- [定價層]**** 鏡頭已被鎖定，因為 DocumentDB 預覽版僅支援單一標準定價層。如需詳細資訊，請參閱 [DocumentDB 定價](http://go.microsoft.com/fwlink/p/?LinkID=402317&clcid=0x409)。

	- [選擇性組態]**** 透鏡可用來指定配置給 DocumentDB 帳戶的初始容量。DocumentDB 利用容量單位讓您調整 DocumentDB 帳戶，每個容量單位包含保留的資料庫儲存體和輸送量。依預設會佈建 1 個容量單位。您可以隨時透過 [Preview 管理入口網站](https://portal.azure.com/#gallery/Microsoft.DocumentDB)來調整 DocumentDB 帳戶可用的容量單位數。如需有關 DocumentDB 帳戶容量和輸送量的詳細資料，請參閱[管理 DocumentDB 的容量和效能][documentdb-manage]一文。

	- 在 [資源群組]**** 中，為 DocumentDB 帳戶選取或建立資源群組。依預設會建立新的資源群組。不過，您可以選擇現有的資源群組，以便在其中加入您的 DocumentDB 帳戶。如需詳細資訊，請參閱[使用資源群組管理您的 Azure 資源](http://azure.microsoft.com/zh-tw/documentation/articles/azure-preview-portal-using-resource-groups/)。

	- 針對 [訂用帳戶]****，選取要用於 DocumentDB 訂用帳戶的 Azure 訂用帳戶。如果您的帳戶只有一個訂用帳戶，將會自動選取該帳戶。*
 
	- 使用 [位置]**** 來指定將裝載您 DocumentDB 帳戶的地理位置。   

3.	設定新的 DocumentDB 帳戶選項之後，按一下 [建立]****。建立 DocumentDB 帳戶可能需要數分鐘的時間。若要檢查狀態，您可以在「開始面板」上監視進度。  
	![][4]  
  
	或者，您也可以從 [通知] 中樞監視進度。  

	![][5]  

	![][6]

4.	建立好的 DocumentDB 帳戶可立即以預設值來使用。

	請注意，DocumentDB 帳戶的預設一致性會設定為 [階段工作]。您可以透過 [Preview 管理入口網站](https://portal.azure.com/#gallery/Microsoft.DocumentDB)調整預設一致性設定。  
	![][7]  

5.	您也可以從 [瀏覽]**** 刀鋒視窗存取現有的 DocumentDB 帳戶。  
	![][8]

##<a id="NextSteps"></a>後續步驟
若要開始使用 Azure DocumentDB，請探索以下資源：

-	[DocumentDB 資源模型和概念](/documentation/articles/documentdb-resources/)
-	[與 DocumentDB 資源互動](/documentation/articles/documentdb-interactions-with-resources/)
-	[如何建立 DocumentDB 帳戶](/documentation/articles/documentdb-create-account/)
-	[開始使用 DocumentDB 帳戶](/documentation/articles/documentdb-get-started/)

若要深入了解 DocumentDB，請參閱 [azure.com](http://go.microsoft.com/fwlink/p/?LinkID=402319) 上的 Azure DocumentDB 文件。

[做法：建立 DocumentDB 帳戶]: #Howto
[後續步驟]: #NextSteps
[documentdb-manage]:../documentdb-manage/

<!--Image references-->
[1]: ./media/documentdb-create-account/ca1.png
[2]: ./media/documentdb-create-account/ca2.png
[3]: ./media/documentdb-create-account/ca3.png
[4]: ./media/documentdb-create-account/ca4.png
[5]: ./media/documentdb-create-account/ca5.png
[6]: ./media/documentdb-create-account/ca6.png
[7]: ./media/documentdb-create-account/ca7.png
[8]: ./media/documentdb-create-account/ca8.png

<!--HONumber=35.2-->
