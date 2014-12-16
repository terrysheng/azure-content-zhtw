<properties urlDisplayName="Validate Data" pageTitle="使用伺服器指令碼驗證與修改資料 (iOS) | 行動開發人員中心" metaKeywords="" description="Learn how to validate and modify data sent using server scripts from your iOS app." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Validate and modify data in Mobile Services by using server scripts" authors="krisragh" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="10/10/2014" ms.author="krisragh" />

# 使用伺服器指令碼在行動服務中驗證與修改資料

[WACOM.INCLUDE [mobile-services-selector-validate-modify-data](../includes/mobile-services-selector-validate-modify-data.md)]

本主題將示範如何在 Azure 行動服務中運用伺服器指令碼。您可在行動服務中註冊伺服器指令碼，並使用該指令碼來針對插入和更新資料執行各種操作，包括驗證與資料修改。在本教學課程中，您將定義並註冊可驗證與修改資料的伺服器指令碼。由於伺服器端指令碼的行為常會影響用戶端，您也可以更新您的 iOS 應用程式，以充分利用這些新行為。

本教學課程將逐步引導您完成下列基本步驟：

1. [新增字串長度驗證]
2. [更新用戶端以支援驗證]


本教學課程會以先前的教學課程[開始使用資料]中的步驟和範例應用程式為基礎。在開始本教學課程之前，您必須首先完成[開始使用資料]。  

## <a name="string-length-validation"></a>Add validation

It is always a good practice to validate the length of data that is submitted by users. First, you register a script that validates the length of string data sent to the mobile service and rejects strings that are too long, in this case longer than 10 characters.

1. Log into the [Azure Management Portal], click **Mobile Services**, and then click your app.

   	![][0]

2. Click the **Data** tab, then click the **TodoItem** table.

   	![][1]

3. Click **Script**, then select the **Insert** operation.

   	![][2]

4. Replace the existing script with the following function, and then click **Save**.

        function insert(item, user, request) {
            if (item.text.length > 10) {
                request.respond(statusCodes.BAD_REQUEST, 'Text length must be 10 characters or less.');
            } else {
                request.execute();
            }
        }

    This script checks the length of the **text** property and sends an error response when the length exceeds 10 characters. Otherwise, the **execute** method is called to complete the insert.

    <div class="dev-callout">
	<b>Note</b>
	<p>You can remove a registered script on the <strong>Script</strong> tab by clicking <strong>Clear</strong> and then <strong>Save</strong>.</p></div>

## <a name="update-client-validation"></a>Update the client

Now that the mobile service is validating data and sending error responses, you need to update your app to be able to handle error responses from validation.

1. In Xcode, open the project that you modified when you completed the tutorial [Get started with data].

2. Press the **Run** button (Command + R) to build the project and start the app, then type text longer than 10 characters in the textbox and click the  plus (**+**) icon.

   	Notice that the app raises an unhandled error as a result of the 400 response (Bad Request) returned by the mobile service.

3. In the QSTodoService.m file, locate the following line of code in the **addItem** method:

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

4. Rebuild and start the app.

   	![][4]

  	Notice that error is handled and the error messaged is displayed to the user.

<!--## <a name="add-timestamp"></a>Add a timestamp

The previous tasks validated an insert and either accepted or rejected it. Now, you will update inserted data by using a server script that adds a timestamp property to the object before it gets inserted.

1. In the **Scripts** tab in the [Management Portal], replace the current **Insert** script with the following function, and then click **Save**.

        function insert(item, user, request) {
            if (item.text.length > 10) {
                request.respond(statusCodes.BAD_REQUEST, 'Text length must be under 10');
            } else {
                item.createdAt = new Date();
                request.execute();
            }
        }

    This function augments the previous insert script by adding a new **createdAt** timestamp property to the object before it gets inserted by the call to **request**.**execute**.

    <div class="dev-callout"><b>Note</b>
	<p>Dynamic schema must be enabled the first time that this insert script runs. With dynamic schema enabled, Mobile Services automatically adds the <strong>createdAt</strong> column to the <strong>TodoItem</strong> table on the first execution. Dynamic schema is enabled by default for a new mobile service, and it should be disabled before the app is published.</p>
    </div>

2. In Visual Studio, press the **F5** key to run the app, then type text (shorter than 10 characters) in **Insert a TodoItem** and click **Save**.

   	Notice that the new timestamp does not appear in the app UI.

3. Back in the Management Portal, click the **Browse** tab in the **todoitem** table.

   	Notice that there is now a **createdAt** column, and the new inserted item has a timestamp value.

Next, you need to update the iOS app to display this new column.

## <a name="update-client-timestamp"></a>Update the client again

The Mobile Service client will ignore any data in a response that it cannot serialize into properties on the defined type. The final step is to update the client to display this new data.

1. In Visual Studio, open the file MainPage.xaml.cs, then replace the existing **TodoItem** class with the following definition:

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

    This new class definition includes the new timestamp property, as a nullable DateTime type.

    <div class="dev-callout"><b>Note</b>
	<p>The <strong>DataMemberAttribute</strong> tells the client to map the new <strong>CreatedAt</strong> property in the app to the <strong>createdAt</strong> column defined in the TodoItem table, which has a different casing. By using this attribute, your app can have property names on objects that differ from column names in the SQL Database. Without this attribute, an error would occur because of the casing differences.</p>
    </div>

5. Add the following XAML element just below the **CheckBoxComplete** element in the MainPage.xaml file:

        <TextBlock Name="WhenCreated" Text="{Binding CreatedAt}" VerticalAlignment="Center"/>

   	如此會在文字方塊中顯示新的 **CreatedAt** 屬性。

6. 按 **F5** 鍵以執行應用程式。

   請留意到，只有在您更新 insert 指令碼後插入的項目，才會顯示時間戳記。

7. 使用下列程式碼取代現有的 **RefreshTodoItems** 方法：

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

   	This method updates the query to also filter out items that do not have a timestamp value.

8. 按 **F5** 鍵以執行應用程式。

   	請留意到，所有已建立但不含時間戳記值的項目，都會從 UI 中消失。

您已完成此使用資料教學課程。-->

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
