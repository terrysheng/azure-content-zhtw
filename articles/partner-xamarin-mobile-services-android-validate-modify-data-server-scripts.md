<properties 
	pageTitle="使用伺服器指令碼驗證與修改資料 (Xamarin Android) | 行動開發人員中心" 
	description="了解如何驗證和修改從 Xamarin.Android 應用程式使用伺服器指令碼傳送的資料。" 
	documentationCenter="xamarin" 
	services="mobile-services" 
	authors="lindydonna" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin-android" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/26/2014" 
	ms.author="donnam"/>

# 使用伺服器指令碼在行動服務中驗證與修改資料

[AZURE.INCLUDE [mobile-services-selector-validate-modify-data](../includes/mobile-services-selector-validate-modify-data.md)]

本主題將示範如何在 Azure 行動服務中運用伺服器指令碼。您可在行動服務中註冊伺服器指令碼，並使用該指令碼來針對插入和更新資料執行各種操作，包括驗證與資料修改。在本教學課程中，您將定義並註冊可驗證與修改資料的伺服器指令碼。由於伺服器端指令碼的行為常會影響用戶端，您也可以更新您的 Android 應用程式，以充分利用這些新行為。完成的程式碼可從 [ValidateModifyData 應用程式][GitHub]範例中取得。

本教學課程將逐步引導您完成下列基本步驟：

1. [新增字串長度驗證]
2. [更新用戶端以支援驗證]
3. [插入時新增時間戳記]
4. [更新用戶端以顯示時間戳記]

本教學課程會以上一堂教學課程[開始使用資料]中的步驟和範例應用程式為基礎。在開始本教學課程之前，您必須首先完成[開始使用資料]。  

## <a name="string-length-validation"></a>新增驗證

驗證使用者提交的資料長度一向是最佳做法。首先，註冊可驗證傳送至行動服務之字串資料長度的指令碼，並拒絕太長的字串，在此案例中為不得超過 10 個字元。

1. 登入「Azure 管理入口網站」[]，按一下 **[行動服務]**，然後按一下您的應用程式。 

	![][0]

2. 按一下 **[資料]** 索引標籤，然後按一下 **[TodoItem]** 資料表。

	![][1]

3. 按一下 **[指令碼]**，然後選取 **[插入]** 作業。

	![][2]

4. 使用下列函式取代現有的指令碼，然後按一下 **[儲存]**。

        function insert(item, user, request) {
            if (item.text.length > 10) {
                request.respond(statusCodes.BAD_REQUEST, 'Text length must be 10 characters or less.');
            } else {
                request.execute();
            }
        }

    此指令碼會檢查 **text** 屬性長度，並在長度超過 10 個字元時傳送錯誤回應。否則，系統會呼叫 **execute** 方法以完成插入。

    > [AZURE.TIP] 您可以將位於 [指令碼]**** 索引標籤上的已註冊指令碼移除，方法是依序按一下 [清除]****、[儲存]****。

## <a name="update-client-validation"></a>更新用戶端

現在，行動服務正在驗證資料並傳送錯誤回應，您必須確認應用程式能夠正確處理驗證的錯誤回應。

1. 在 Xamarin Studio 中，開啟您完成[開始使用資料]教學課程時所建立的專案。

2. 在 TodoActivity.cs 檔案中找出 **AddItem** 方法，並以下列程式碼取代 CreateAndShowDialog 方法的呼叫：

    	var exDetail = ex.InnerException.InnerException as 	
			MobileServiceInvalidOperationException;
    	CreateAndShowDialog(exDetail.Message, "Error");

	這會顯示行動服務所傳回的錯誤訊息。 

3. 按一下 **[執行]** 以啟動應用程式，在文字方塊中輸入超過 10 個字元的文字，然後按一下 **[新增]** 按鈕。

  	請留意到，此時會處理錯誤，並對使用者顯示錯誤訊息。

## <a name="add-timestamp"></a>新增時間戳記

上一個工作驗證了插入操作，並已接受或拒絕此操作。現在，您將更新插入資料，方法是在插入物件之前，使用可將時間戳記屬性新增至物件的伺服器指令碼。

1. 在[管理入口網站]的 **[指令碼]** 索引標籤中，使用下列函數來取代目前的 **Insert** 指令碼，然後按一下 **[儲存]**。

        function insert(item, user, request) {
            if (item.text.length > 10) {
                request.respond(statusCodes.BAD_REQUEST, 'Text length must be under 10');
            } else {
                item.createdAt = new Date();
                request.execute();
            }
        }

    此函數可強化上一個 insert 指令碼，方法是在呼叫 **request**.**execute** 來插入物件之前，先將新的 **createdAt** 時間戳記屬性新增至物件。 

    > [AZURE.IMPORTANT] 首次執行插入指令碼時，必須啟用動態結構描述。在啟用動態結構描述的情況下，行動服務會在首次執行時自動將 **createdAt** 欄新增至 **TodoItem** 資料表。依預設會啟用新行動服務的動態結構描述，您應先加以停用，再發佈應用程式。

2. 從 **[執行]** 功能表按一下 **[執行]** 以啟動應用程式，然後在文字方塊中輸入少於 10 個字元的文字，再按一下 **[新增]**。

   	請注意新的時間戳記不會出現在應用程式 UI 中。

3. 回到 [管理入口網站] 中，按一下 **[todoitem]** 資料表中的 **[瀏覽]** 索引標籤。
   
   	請注意，現在會有一個 **createdAt** 資料欄，且新插入的項目也具有時間戳記值。
  
接下來，您必須更新 Android 應用程式以顯示此新資料欄。

## <a name="update-client-timestamp"></a>重新更新用戶端

針對無法序列化成已定義類型屬性的回應，行動服務用戶端將忽略該回應中的任何資料。最後步驟是更新用戶端以顯示此新資料。

1. 在 **TodoItem** 類別的私人欄位定義中新增下列程式碼：

        [DataMember(Name = "createdAt")]
        public DateTime? CreatedAt { get; set; }
  
    > [AZURE.NOTE]  `DataMember's Name` 註解會指示用戶端，將應用程式中的新  `CreatedAt` 屬性對應到 TodoItem 資料表中所定義且具有不同名稱的  `createdAt` 欄。使用此註解，您的應用程式便可擁有物件上的屬性名稱，且該名稱會與 SQL Database 中的資料欄名稱不同。若是沒有此註解，則會因大小寫差異而發生錯誤。

2. 在 GetView 方法中，將下列程式碼新增至將 <code>checkBox.Text</code> 設為 <code>currentItem.Text</code> 的現行程式碼上方：

       	string displayDate = "missing";
       	if (currentItem.CreatedAt.HasValue)
       		displayDate = currentItem.CreatedAt.Value.ToShortTimeString();

   	具有時間戳記值時，這會建立已格式化的日期字串。 

3. 再次找出程式碼  `checkBox.Text = currentItem.Text`，並將此程式碼行取代為下列內容：

		checkBox.Text = string.Format("{0} - {1}", currentItem.Text, displayDate);

	這會將時間戳記日期附加至顯示的項目。
	
4. 在 **[執行]** 功能表中，按一下 **[執行]** 以啟動應用程式。 

	請留意到，只有在您更新 insert 指令碼後插入的項目，才會顯示時間戳記。

5. 在 **TodoActivity.cs** 中，以下列查詢取代 **RefreshItemsFromTableAsync** 中的現有查詢：

		var list = await todoTable.Where(item => item.Complete == false && 
										 item.CreatedAt != null)
								  .ToListAsync();

	此方法會更新查詢，篩選出沒有時間戳記的項目。
	
6. 在 **[執行]** 功能表中，按一下 **[執行]** 以啟動應用程式。

  	請留意到，所有已建立但不含時間戳記值的項目，都會從 UI 中消失。

您已完成此使用資料教學課程。

## <a name="next-steps"> </a>後續步驟

現在，您已完成本教學課程，請考慮繼續進行資料數列中最後的教學課程：[使用分頁縮小查詢範圍]。

您也可以在授權使用者及傳送推播通知時使用伺服器指令碼。如需詳細資訊，請參閱下列教學課程：

* [使用指令碼授權使用者]
  <br/>了解如何根據通過驗證的使用者識別碼篩選資料。

* [開始使用推播通知] 
  <br/>了解如何將極為基本的推播通知傳送到應用程式。

* [行動服務伺服器指令碼參考]
  <br/>深入了解如何註冊和使用伺服器指令碼。

<!-- Anchors. -->
[新增字串長度驗證]: #string-length-validation
[更新用戶端以支援驗證]: #update-client-validation
[插入時新增時間戳記]: #add-timestamp
[更新用戶端以顯示時間戳記]: #update-client-timestamp
[後續步驟]: #next-steps

<!-- Images. -->
[0]: ./media/partner-xamarin-mobile-services-android-validate-modify-data-server-scripts/mobile-services-selection.png
[1]: ./media/partner-xamarin-mobile-services-android-validate-modify-data-server-scripts/mobile-portal-data-tables.png
[2]: ./media/partner-xamarin-mobile-services-android-validate-modify-data-server-scripts/mobile-insert-script-users.png



<!-- URLs. -->
[行動服務伺服器指令碼參考]: http://go.microsoft.com/fwlink/?LinkId=262293
[開始使用行動服務]: /zh-tw/develop/mobile/tutorials/get-started-xamarin-android
[使用指令碼授權使用者]: /zh-tw/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-android
[使用分頁縮小查詢範圍]: /zh-tw/develop/mobile/tutorials/add-paging-to-data-xamarin-android
[開始使用資料]: /zh-tw/develop/mobile/tutorials/get-started-with-data-xamarin-android
[開始使用驗證]: /zh-tw/develop/mobile/tutorials/get-started-with-users-xamarin-android
[開始使用推播通知]: /zh-tw/develop/mobile/tutorials/get-started-with-push-xamarin-android

[管理入口網站]: https://manage.windowsazure.com/
[Azure 管理入口網站]: https://manage.windowsazure.com/
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=331330


<!--HONumber=42-->
