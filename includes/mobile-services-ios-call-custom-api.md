## <a name="update-app"></a><span class="short-header">更新應用程式 </span>更新應用程式以呼叫自訂 API

1.  建立按鈕，如此即可按一下按鈕呼叫自訂 API。從 [公用程式] 窗格底部的 [物件庫] 中拖曳 [Round Rect Button]，並將它放在文字欄位底下或旁邊。連按兩下以新增文字 **"All"**。

    這會新增 ["All"] 按鈕。

2.  開啟 **QSTodoService.m** 程式碼檔案，尋找 `refreshDataOnSuccess` 方法並確定其包含下列程式碼：

        - (void)refreshDataOnSuccess:(QSCompletionBlock)completion
        {          
            // Create a predicate that finds items where complete is false
            NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];

            // Query the TodoItem table and update the items property with the results from the service
            [self.table readWithPredicate:predicate completion:^(NSArray *results, NSInteger totalCount, NSError *error)
            {
                [self logErrorIfNotNil:error];

                items = [results mutableCopy];

                // Let the caller know that we finished
                completion();
            }];                                 
        }

    這會篩選項目，如此一來，查詢就不會傳回已完成的項目。

3.  現在請連線此物件以檢視控制器原始程式碼。**Control-按一下**新的 ["All"] 按鈕，並將滑鼠拖曳到 **QSTodoListViewController.h** 的 `@end` 行前方。將物件連線到 **QSTodoListViewController** 中名為 `onCompleteAll` 的新**動作**。Xcode 會自動在 `@end` 行前方插入下行：

           - (IBAction)onCompleteAll:(id)sender;

4.  此 `onCompleteAll` 方法的目標是處理新按鈕的 Click 事件。它會叫用我們將在自訂類別中加入的新 `completeAll` 方法，接著將 POST 要求傳送至新的自訂 API。如有任何錯誤，自訂 API 傳回的結果會顯示在訊息對話方塊中。編輯 **QSTodoListViewController.m**，將下列實作新增至 `@end` 行前面：

           - (IBAction)onCompleteAll:(id)sender {
            [self.todoService completeAll:^(id result, NSHTTPURLResponse* response, NSError* error)
             {
                 if (error)
                 {
                     NSString* errorMessage = @"There was a problem! ";
                     errorMessage = [errorMessage stringByAppendingString:[error localizedDescription]];
                     UIAlertView* myAlert = [[UIAlertView alloc]
                                             initWithTitle:@"Error!"
                                             message:errorMessage
                                             delegate:nil
                                             cancelButtonTitle:@"Okay"
                                             otherButtonTitles:nil];
                     [myAlert show];
                     [self refresh];
                 } else {
                     NSString* successMessage = [NSString stringWithFormat:@"%d items marked as complete", [[result objectForKey:@"count"] integerValue]];                   
                     UIAlertView* myAlert = [[UIAlertView alloc]
                                             initWithTitle:@"Success!"
                                             message:successMessage
                                             delegate:nil
                                             cancelButtonTitle:@"Okay"
                                             otherButtonTitles:nil];
                     [myAlert show];
                     [self refresh];
                 }
             }];
           }

5.  請注意，以上程式碼參照到尚未在 **QSTodoService** 中定義的新方法 `completeAll`。編輯 **QSTodoService.h**，並將下行新增至 `@end` 行前面：

        - (void) completeAll:(MSAPIBlock)completion;

6.  將 **QSTodoService.m** 中 `completeAll` 的對應實作新增至 `@end` 行前方。iOS 類似於 JavaScript，但它不支援任意類型的 JSON 序列化。因此它也具備可叫用自訂 API 的極簡單 API (由 `invokeAPI` 方法組成)。

        - (void) completeAll:(MSAPIBlock)completion
        {
            [self.client
             invokeAPI:@"completeall"
             body:nil
             HTTPMethod:@"POST"
             parameters:nil
             headers:nil
             completion:completion ];
        }

## <a name="test-app"></a>測試應用程式

1.  在 Xcode 中，選取要部署到 iPhone 或 iPad 上的模擬器，按 [執行] 按鈕 (或 **Command+R** 鍵)，以重建專案並啟動應用程式。這將執行您的 Windows Azure 行動服務用戶端，其使用 iOS SDK 建置，可從您的行動服務查詢項目。

2.  在文字方塊中鍵入文字，然後按一下 **+** 按鈕。這會傳送新項目以插入至行動服務。

3.  重複前一個步驟，直到將數個項目新增至清單為止。

4.  點選 [全部] 按鈕。出現警示方塊，指出標示為完成的項目數，並重新執行篩選查詢，以便清除清單的所有項目。

    ![][0]

  [0]: ./media/mobile-services-ios-call-custom-api/mobile-custom-api-ios-completed.png
