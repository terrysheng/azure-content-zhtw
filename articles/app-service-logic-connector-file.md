<properties 
	pageTitle="檔案連接器"
	description="開始使用檔案連接器"
	authors="anuragdalmia" 
	manager="dwrede" 
	editor="" 
	services="app-service\logic" 
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/09/2015"
	ms.author="andalmia"/>

# 在邏輯應用程式中使用檔案連接器 #

邏輯應用程式可以根據各種資料來源觸發，並提供連接器以取得及處理屬於流程一部分的資料。 

檔案連接器可讓您從主機電腦的檔案系統上傳\下載\刪除檔案。

## 建立邏輯應用程式的檔案連接器 ##
若要使用檔案連接器，您必須先建立檔案連接器 API 應用程式的執行個體。以下步驟可以達到此目的：

1.	使用 Azure 入口網站左下方的 [+新增] 選項開啟 Azure Marketplace。
2.	瀏覽至 [Web 與行動] > [API 應用程式]，並搜尋「檔案連接器」。
3.	設定檔案連接器，如下所示：
 
	![][1]
 
	- **名稱** - 提供檔案連接器的名稱
	- **套件設定**
		- **根資料夾** - 指定主機電腦上的根資料夾路徑。例如D:\FileConnectorTest
		- **服務匯流排連線字串** - 提供服務匯流排連線字串。請確認服務匯流排命名空間屬於「標準」而非「基本」
	- **應用程式服務計劃** - 選取或建立應用程式服務計劃
	- **定價層** - 選擇連接器的定價層
	- **資源群組** - 選取或建立連接器所在的資源群組
	- **訂用** - 選擇您要建立此連接器的訂用
	- **位置** - 選擇您要部署連接器的地理位置

4. 按一下 [建立]。將建立新的檔案連接器。
5. API 應用程式執行個體建立之後，瀏覽至其儀表板。依序按一下 [瀏覽]->[API 應用程式]->[選取您的檔案連接器] 即可完成
6. 您可以在 [檔案連接器] 儀表板看到 [混合式連線] 透鏡顯示 [內部部署設定完成]。若要完成「混合式設定」，請從要連線到的檔案系統之主機電腦開啟此儀表板。然後按一下「 混合式連線 」透鏡。在開啟的 [混合式連線] 刀鋒視窗中按一下 [下載並設定] 連結來安裝 [內部部署混合式連線管理員]。

	![][2]

7. 這會開啟安裝程式並需要您輸入 [轉送接聽連接字串]從 [混合式連線] 刀鋒視窗複製 [主要組態字串]。按一下 [安裝]。

	![][3]

8. 重新開啟 [檔案連接器] 儀表板，以驗證混合式安裝已成功。您可以看到混合式連線狀態為 [已連線]。

	![][4]

## 在邏輯應用程式中使用檔案連接器 ##
建立 API 應用程式之後，您現在可以使用檔案連接器做為邏輯應用程式的動作。若要這樣做，您需要：

1.	建立新的邏輯應用程式，並選擇具有檔案連接器的相同資源群組。依照下列指示[建立新的邏輯應用程式]。  	
	
2.	在建立的邏輯應用程式中開啟 [觸發程序和動作] 以開啟邏輯應用程式設計工具，並設定您的流程。  	
	
3.	檔案連接器就會出現在右側程式庫中的 [此資源群組中的 API 應用程式] 區段。
 	
4.	您可以在 [檔案連接器] 上按一下來將檔案連接器 API 應用程式置入編輯器。檔案連接器公開 1 個觸發程序和 4 個動作。
 
	![][5]

6.	其中每個都公開特定的屬性。下圖列出觸發程序和「取得檔案」動作的屬性
 
	![][6]

7. 設定好這些之後，就可以在流程中使用觸發程序和動作。同樣地，您也可以設定其他動作。

8. 若要在邏輯應用程式外使用連接器，可以利用連接器公開的 REST API。您可以使用 [瀏覽]->[API 應用程式]->[檔案連接器] 檢視這個「API 定義」。在 [摘要] 區段下的 [API 定義] 透鏡上按一下，來檢視此連接器公開的 API。

	![][7]

9. API 的詳細資訊可在 [檔案連接器 API 定義]中找到。

<!-- Image reference -->
[1]: ./media/app-service-logic-connector-file/img1.PNG
[2]: ./media/app-service-logic-connector-file/img2.PNG
[3]: ./media/app-service-logic-connector-file/img3.PNG
[4]: ./media/app-service-logic-connector-file/img4.PNG
[5]: ./media/app-service-logic-connector-file/img5.PNG
[6]: ./media/app-service-logic-connector-file/img6.PNG
[7]: ./media/app-service-logic-connector-file/img7.PNG

<!-- Links -->
[建立新的邏輯應用程式]: app-service-logic-create-a-logic-app.md
[檔案連接器 API 定義]: https://msdn.microsoft.com/en-US/library/dn936296.aspx


<!--HONumber=52-->