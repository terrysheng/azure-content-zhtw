<properties urlDisplayName="Validate Data" pageTitle="使用伺服器指令碼驗證與修改資料 (iOS) | 行動開發人員中心" metaKeywords="" description="Learn how to validate and modify data sent using server scripts from your iOS app." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Validate and modify data in Mobile Services by using server scripts" authors="krisragh" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="10/10/2014" ms.author="krisragh" />

# 使用伺服器指令碼在行動服務中驗證與修改資料

[WACOM.INCLUDE [mobile-services-selector-validate-modify-data](../includes/mobile-services-selector-validate-modify-data.md)]

本主題將示範如何在 Azure 行動服務中運用伺服器指令碼。您可在行動服務中註冊伺服器指令碼，並使用該指令碼來針對插入和更新資料執行各種操作，包括驗證與資料修改。在本教學課程中，您將定義並註冊可驗證與修改資料的伺服器指令碼。由於伺服器端指令碼的行為常會影響用戶端，您也可以更新您的 iOS 應用程式，以充分利用這些新行為。

本教學課程將逐步引導您完成下列基本步驟：

1. [新增字串長度驗證]
2. [更新用戶端以支援驗證]


本教學課程會以上一個教學課程[開始使用資料]中的步驟和範例應用程式為基礎。在開始本教學課程之前，您必須首先完成[開始使用資料]。  

## <a name="string-length-validation"></a>新增驗證

驗證使用者提交的資料長度一向是最佳做法。首先，註冊可驗證傳送至行動服務之字串資料長度的指令碼，並拒絕太長的字串，在此案例中為不得超過 10 個字元。

1. 登入 [Azure 管理入口網站]，按一下 [**行動服務**]。

   	![][0]

2. 按一下 [**資料**] 索引標籤，然後按一下 [**TodoItem**] 資料表。

   	![][1]

3. 按一下 [**指令碼**]，然後選取 [**插入**] 作業。

   	![][2]

4. 以下列函數取代現有的指令碼，然後按一下 [**儲存**]。

        function insert(item, user, request) {
            if (item.text.length > 10) {
                request.respond(statusCodes.BAD_REQUEST, 'Text length must be 10 characters or less.');
            } else {
                request.execute();
            }
        }

    本指令碼會檢查 [**text**] 屬性的長度，並在長度超過 10 個字元時傳送錯誤回應。否則，系統會呼叫 **execute** 方法以完成插入。

    <div class="dev-callout">
	<b>注意</b>
	<p>在 [<strong>指令碼</strong>] 索引標籤上按一下 [<strong>清除</strong>]，然後按一下 [<strong>儲存</strong>]，即可移除註冊的指令碼。</p></div>

## <a name="update-client-validation"></a>更新用戶端

現在，行動服務正在驗證資料並傳送錯誤回應，您必須更新您的應用程式以便處理驗證的錯誤回應。

1. 在 Xcode 中，開啟您完成教學課程 [開始使用資料] 時所修改的專案。

2. 按 [**執行**] 按鈕 (Command + R) 以建置專案並啟動應用程式，在文字方塊中輸入超過 10 個字元的文字，然後按一下加號 (**+**) 圖示。

   	請注意應用程式會指出一個由行動服務所傳回的未處理錯誤，顯示為 400 response (Bad Request)。

3. 在 QSTodoService.m 檔案中，找出位於 **addItem** 方法中的下列程式碼行：

        [self logErrorIfNotNil:error];

   	在此行程式碼之後，將完成區塊的提醒以下列程式碼取代：

        BOOL goodRequest = !((error) && (error.code == MSErrorMessageErrorCode));

        // detect text validation error from service.
        if (goodRequest) // The service responded appropriately
        {
            NSUInteger index = [items count];
            [(NSMutableArray *)items insertObject:result atIndex:index];

            // Let the caller know that we finished
            completion(index);
        }
        else{

            // if there's an error that came from the service
            // log it, and popup up the returned string.
            if (error && error.code == MSErrorMessageErrorCode) {
                NSLog(@"ERROR %@", error);
                UIAlertView *av =
                [[UIAlertView alloc]
                 initWithTitle:@"Request Failed"
                 message:error.localizedDescription
                 delegate:nil
                 cancelButtonTitle:@"OK"
                 otherButtonTitles:nil
                 ];
                [av show];
            }
        }

   	這樣會在輸入視窗中記錄錯誤，並顯示給使用者參考。

4. 重新建置並啟動應用程式。

   	![][4]

  	請留意，此時會處理錯誤，並對使用者顯示錯誤訊息。

## <a name="add-timestamp"></a>新增時間戳記

上一個工作驗證了插入操作，並已接受或拒絕此操作。現在，您將更新插入資料，方法是在插入物件之前，使用可將時間戳記屬性新增至物件的伺服器指令碼。

1. 在 [管理入口網站] 的 [**指令碼**] 索引標籤中，使用下列函數來取代目前的 **Insert** 指令碼，然後按一下 [**儲存**]。

        function insert(item, user, request) {
            if (item.text.length > 10) {
                request.respond(statusCodes.BAD_REQUEST, 'Text length must be under 10');
            } else {
                item.createdAt = new Date();
                request.execute();
            }
        }

    此函數可強化上一個 insert 指令碼，方法是先將新的 **createdAt** 時間戳記屬性新增至物件，再呼叫 **request**.**execute**。

    <div class="dev-callout"><b>注意</b>
	<p>首次執行插入指令碼時，必須啟用動態結構描述。在啟用動態結構描述的情況下，行動服務會在首次執行時自動將 <strong>createdAt</strong> 資料行新增至 <strong>TodoItem</strong> 資料表。依預設會啟用新行動服務的動態結構描述，您應先加以停用，再發佈應用程式。</p>
    </div>

2. 在 Visual Studio 中，按 **F5** 鍵以執行應用程式，然後在 [**Insert a TodoItem**] 中鍵入文字 (少於 10 個字元)，並按一下 [**Save**]。

   	請注意新的時間戳記不會出現在應用程式 UI 中。

3. 回到 [管理入口網站] 中，按一下 [**todoitem**] 資料表中的 [**Browse**] 索引標籤。

   	請注意現在會有一個 **createdAt** 資料欄，且新插入的項目也具有時間戳記值。

接下來，您必須更新 iOS 應用程式以顯示此新資料欄。

## <a name="update-client-timestamp"></a>重新更新用戶端

針對無法序列化成已定義類型屬性的回應，行動服務用戶端將忽略該回應中的任何資料。最後步驟是更新用戶端以顯示此新資料。

1. 在 Visual Studio 中開啟檔案 MainPage.xaml.cs，然後使用下列定義來取代現有的 **TodoItem** 類別：

	    public class TodoItem
	    {
	        public int Id { get; set; }

            [DataMember(Name="text")]
	        public string Text { get; set; }

            [DataMember(Name="complete")]
	        public bool Complete { get; set; }

            [DataMember(Name="createdAt")]
	        public DateTime? CreatedAt { get; set; }
	    }

    此新類別定義將包括如可為 Null 之 DateTime 類型的新時間戳記屬性。

    <div class="dev-callout"><b>注意</b>
	<p><strong>DataMemberAttribute</strong> 會告訴用戶端，將應用程式中的新 <strong>CreatedAt</strong> 屬性對應到 TodoItem 資料表中所定義、且使用不同大小寫的 <strong>createdAt</strong> 資料欄。使用此屬性，您的應用程式便可擁有物件上的屬性名稱，且該名稱會與 SQL Database 中的資料欄名稱不同。若是沒有這個屬性，則會因大小寫差異而發生錯誤。</p>
    </div>

2. 在 MainPage.xaml 檔案中的 **CheckBoxComplete** 元素正下方新增下列 XAML 元素：

        <TextBlock Name="WhenCreated" Text="{Binding CreatedAt}" VerticalAlignment="Center"/>

   	如此會在文字方快中顯示近的 **CreatedAt** 屬性。

3. 按 **F5** 鍵以執行應用程式。

   請留意到，只有在您更新 insert 指令碼後插入的項目，才會顯示時間戳記。

4. 以下列程式碼取代 **RefreshTodoItems** 方法：

        private void RefreshTodoItems()
        {
            // This query filters out completed TodoItems and
            // items without a timestamp.
            items = todoTable
               .Where(todoItem => todoItem.Complete == false
                   && todoItem.CreatedAt != null)
               .ToCollectionView();

            ListItems.ItemsSource = items;
        }

   	此方法會更新查詢，篩選出沒有時間戳記的項目。

5. 按 **F5** 鍵以執行應用程式。

   	請留意到，所有已建立但不含時間戳記值的項目，都會從 UI 中消失。

您已完成此使用資料教學課程。

## <a name="next-steps"> </a>後續步驟

現在，您已完成本教學課程，請考慮繼續進行資料數列中最後的教學課程：[使用分頁縮小查詢範圍]。

您也可以在授權使用者及傳送推播通知時使用伺服器指令碼。如需詳細資訊，請參閱下列教學課程：

* [使用指令碼授權使用者]
  <br/>了解如何根據通過驗證的使用者識別碼篩選資料。

* [開始使用推播通知]
  <br/>了解如何將非常基本的推播通知傳送至您的應用程式。

* [行動服務伺服器指令碼參考]
  <br/>進一步了解註冊和使用伺服器指令碼。

<!-- Anchors. -->
[新增字串長度驗證]: #string-length-validation
[更新用戶端以支援驗證]: #update-client-validation
[插入時新增時間戳記]: #add-timestamp
[更新用戶端以顯示時間戳記]: #update-client-timestamp
[後續步驟]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-ios-validate-modify-data-server-scripts/mobile-services-selection.png
[1]: ./media/mobile-services-ios-validate-modify-data-server-scripts/mobile-portal-data-tables.png
[2]: ./media/mobile-services-ios-validate-modify-data-server-scripts/mobile-insert-script-users.png

[4]: ./media/mobile-services-ios-validate-modify-data-server-scripts/mobile-quickstart-data-error-ios.png

<!-- URLs. -->
[行動服務伺服器指令碼參考]: http://go.microsoft.com/fwlink/?LinkId=262293
[開始使用行動服務]: /zh-tw/develop/mobile/tutorials/get-started-ios
[使用指令碼授權使用者]: /zh-tw/develop/mobile/tutorials/authorize-users-in-scripts-ios
[使用分頁縮小查詢範圍]: /zh-tw/develop/mobile/tutorials/add-paging-to-data-ios
[開始使用資料]: /zh-tw/develop/mobile/tutorials/get-started-with-data-ios
[開始使用驗證]: /zh-tw/develop/mobile/tutorials/get-started-with-users-ios
[開始使用推播通知]: /zh-tw/develop/mobile/tutorials/get-started-with-push-ios

[管理入口網站]: https://manage.windowsazure.com/
[Azure 管理入口網站]: https://manage.windowsazure.com/
