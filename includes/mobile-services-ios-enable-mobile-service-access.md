您的行動服務已準備就緒，現在可以更新應用程式以便在行動服務 (而非本機收集) 中儲存項目。

1.  如果您尚未安裝 [Mobile Services iOS SDK][Mobile Services iOS SDK]，請立即安裝。

2.  在 Xcode 的 [專案導覽] 中，開啟位於 [快速入門] 資料夾中的 TodoService.m 和 TodoService.h 檔案，並新增下列 import 陳述式：

        #import <WindowsAzureMobileServices/WindowsAzureMobileServices.h>  

3.  在 ToDoService.h 檔案中，尋找下列已加上註解的程式碼行：

        // Create an MSClient property comment in the #interface declaration for the TodoService. 

    在此註解之後，新增下列程式碼行：

        @property (nonatomic, strong)   MSClient *client;

    這會建立一個屬性，表示連線至此服務的 MSClient

4.  在 TodoService.m 檔案中，尋找下列已加上註解的程式碼行：

        // Create an MSTable property for your items. 

    After this comment, add the following line of code inside the @interface declaration:

        @property (nonatomic, strong)   MSTable *table;

    這會為您的行動服務資料表建立一個屬性方法。

5.  在管理入口網站中，按一下 [行動服務]，然後按一下您剛剛建立的行動服務。

6.  按一下 [儀表板] 索引標籤並記下 [網站 URL]，然後按一下 [管理金鑰] 並記下 [應用程式金鑰]。

    ![][0]

    從應用程式程式碼存取行動服務時，您將會用到這些值。

7.  回到 Xcode，開啟 TodoService.m 並尋找下列已加上註解的程式碼行：

        // Initialize the Mobile Service client with your URL and key.

    在此註解之後，新增下列程式碼行：

        self.client = [MSClient clientWithApplicationURLString:@"APPURL" withApplicationKey:@"APPKEY"];

    這會建立行動服務用戶端的執行個體。

8.  使用您在步驟 6 中從行動服務取得的 URL 和應用程式金鑰，來取代此程式碼中的 **APPURL** 和 **APPKEY** 值。

9.  尋找下列已加上註解的程式碼行：

        // Create an MSTable instance to allow us to work with the TodoItem table.

    在此註解之後，新增下列程式碼行：

        self.table = [self.client getTable:@"TodoItem"];

    這會建立 TodoItem 資料表執行個體。

10. 尋找下列已加上註解的程式碼行：

        // Create a predicate that finds items where complete is false comment in the refreshDataOnSuccess method. 

    在此註解之後，新增下列程式碼行：

        NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];

    This creates a query to return all tasks that have not yet been completed.

11. 尋找下列已加上註解的程式碼行：

        // Query the TodoItem table and update the items property with the results from the service.

    使用下列程式碼來取代註解和後續 **completion** 區塊引動：

        // Query the TodoItem table and update the items property with the results from the service
        [self.table readWhere:predicate completion:^(NSArray *items, NSInteger totalCount, NSError *error)
        {
           self.items = [items mutableCopy];
           completion();
        }]; 

12. 尋找 **addItem** 方法，並將下列程式碼新增至方法的主體：

        // Insert the item into the TodoItem table and add to the items array on completion
        [self.table insert:item completion:^(NSDictionary *result, NSError *error) {
            NSUInteger index = [items count];
            [(NSMutableArray *)items insertObject:item atIndex:index];

            // Let the caller know that we finished
            completion(index);
        }];

    此程式碼會傳送插入要求給行動服務。

13. 尋找 **completeItem** 方法，並將下列程式碼新增至方法的主體：

        // Update the item in the TodoItem table and remove from the items array on completion
        [self.table update:mutable completion:^(NSDictionary *item, NSError *error) {

            // TODO
            // Get a fresh index in case the list has changed
            NSUInteger index = [items indexOfObjectIdenticalTo:mutable];

            [mutableItems removeObjectAtIndex:index];

            // Let the caller know that we have finished
            completion(index);
        }]; 

    此程式碼會在 TodoItems 被標示為已完成時將它移除。

應用程式現已更新為使用行動服務進行後端儲存，我們可以開始在行動服務中測試應用程式。

  [Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
  [0]: ./media/mobile-services-ios-enable-mobile-service-access/mobile-dashboard-tab.png
