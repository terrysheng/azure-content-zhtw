<properties linkid="develop-mobile-tutorials-validate-modify-and-augment-data-ios" urlDisplayName="Validate Data" pageTitle="Use server scripts to validate and modify data (iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to validate and modify data sent using server scripts from your iOS app." metaCanonical="" services="" documentationCenter="Mobile" title="Validate and modify data in Mobile Services by using server scripts" authors="" solutions="" manager="" editor="" />

使用伺服器指令碼在行動服務中驗證與修改資料
==========================================

[Windows 市集 C\#](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet "Windows 市集 C#")[Windows 市集 JavaScript](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-js "Windows 市集 JavaScript")[Windows Phone](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-wp8 "Windows Phone")[iOS](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-ios "iOS")[Android](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-android "Android")[HTML](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-html "HTML")[Xamarin.iOS](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios "Xamarin.iOS")[Xamarin.Android](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-android "Xamarin.Android")

本主題將示範如何在 Azure 行動服務中運用伺服器指令碼。您可在行動服務中註冊伺服器指令碼，並使用該指令碼來針對插入和更新資料執行各種操作，包括驗證與資料修改。在本教學課程中，您將定義並註冊可驗證與修改資料的伺服器指令碼。由於伺服器端指令碼的行為常會影響用戶端，您也可以更新您的 iOS 應用程式，以充分利用這些新行為。

本教學課程將逐步引導您完成下列基本步驟：

1.  [新增字串長度驗證](#string-length-validation)
2.  [更新用戶端以支援驗證](#update-client-validation)
3.  [插入時新增時間戳記](#add-timestamp)
3.  [更新用戶端以顯示時間戳記](#update-client-timestamp)

本教學課程會以上一堂教學課程[開始使用資料](/en-us/develop/mobile/tutorials/get-started-with-data-ios)中的步驟和範例應用程式為基礎。在開始本教學課程之前，您必須首先完成[開始使用資料](/en-us/develop/mobile/tutorials/get-started-with-data-ios)。

新增驗證
--------

驗證使用者提交的資料長度一向是最佳做法。首先，註冊可驗證傳送至行動服務之字串資料長度的指令碼，並拒絕太長的字串，在此案例中為不得超過 10 個字元。

1.  登入 [Azure 管理入口網站](https://manage.windowsazure.com/)，按一下 **[行動服務]**，然後按一下您的應用程式。

	![](./media/mobile-services-ios-validate-modify-data-server-scripts/mobile-services-selection.png)

2.  按一下 **[資料]** 索引標籤，然後按一下 **[TodoItem]** 資料表。

	![](./media/mobile-services-ios-validate-modify-data-server-scripts/mobile-portal-data-tables.png)

3.  按一下 **[指令碼]**，然後選取 **[插入]** 作業。

	![](./media/mobile-services-ios-validate-modify-data-server-scripts/mobile-insert-script-users.png)

4.  以下列函數取代現有的指令碼，然後按一下 **[儲存]**。

        function insert(item, user, request) {
            if (item.text.length > 10) {
                request.respond(statusCodes.BAD_REQUEST, 'Text length must be 10 characters or less.');
            } else {
                request.execute();
            }
        }

    本指令碼會檢查 **text** 屬性的長度，並在長度超過 10 個字元時傳送錯誤回應。否則，系統會呼叫 **execute** 方法以完成插入。

    **注意**

    您可以將位於 **[指令碼]** 索引標籤上的已註冊指令碼移除，方法是依序按一下 **[清除]**、**[儲存]**。

更新用戶端
----------

現在，行動服務正在驗證資料並傳送錯誤回應，您必須更新您的應用程式以便處理驗證的錯誤回應。

1.  在 Xcode 中，開啟您完成教學課程[開始使用資料](/en-us/develop/mobile/tutorials/get-started-with-data-ios)時所修改的專案。

2.  按 **[執行]** 按鈕 (Command + R) 以建置專案並啟動應用程式，在文字方塊中輸入超過 10 個字元的文字，然後按一下加號 (**+**) 圖示。

	Notice that the app raises an unhandled error as a result of the 400 response (Bad Request) returned by the mobile service.  

3.  在 QSTodoService.m 檔案中，找出位於 **addItem** 方法中的下列程式碼行：

        [self logErrorIfNotNil:error]; 

	After this line of code, replace the remainder of the completion block with the following code:

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

	This logs the error to the output window and displays it to the user. 

4.  重新建置並啟動應用程式。

	![](./media/mobile-services-ios-validate-modify-data-server-scripts/mobile-quickstart-data-error-ios.png)

	請留意到，此時會處理錯誤，並對使用者顯示錯誤訊息。

後續步驟
--------

現在，您已完成本教學課程，請考慮繼續進行資料數列中最後的教學課程：[使用分頁縮小查詢範圍](/en-us/develop/mobile/tutorials/add-paging-to-data-ios)。

您也可以在授權使用者及傳送推播通知時使用伺服器指令碼。如需詳細資訊，請參閱下列教學課程：

-   [使用指令碼授權使用者](/en-us/develop/mobile/tutorials/authorize-users-in-scripts-ios)
  <br/>了解如何根據通過驗證使用者的 ID 來篩選資料。

-   [開始使用推播通知](/en-us/develop/mobile/tutorials/get-started-with-push-ios)
  <br/>了解如何將極為基本的推播通知傳送到應用程式。

-   [行動服務伺服器指令碼參考](http://go.microsoft.com/fwlink/?LinkId=262293)
  <br/>深入了解如何註冊和使用伺服器指令碼。


