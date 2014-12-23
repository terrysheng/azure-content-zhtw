
您的行動服務已準備就緒，現在可以更新應用程式以便在行動服務 (而非本機收集) 中儲存項目。

1. 如果您尚未安裝 [Mobile Services iOS SDK](https://go.microsoft.com/fwLink/p/?LinkID=266533)，請立即安裝。安裝之後，複製 WindowsAzureMobileServices.framework 目錄，並覆寫已下載的專案中包含的 WindowsAzureMobileServices.framework 目錄。如此一來，您會使用最新的 Azure Mobile Services SDK。

2. 在 TodoService.h 檔案中，尋找下列已加上註解的程式碼行：

        // TODO - create an MSClient proeprty

   	在此註解之後，新增下列程式碼行：

        @property (nonatomic, strong)   MSClient *client;

   	這會建立一個屬性，表示連線至此服務的 MSClient

3. 在 TodoService.m 檔案中，尋找下列已加上註解的程式碼行：

        // TODO - create an MSTable property for your items

   	After this comment, add the following line of code inside the @interface declaration:

        @property (nonatomic, strong)   MSTable *table;

   	這將建立行動服務資料表的代表屬性。

4. 在管理入口網站中，按一下 [**行動服務**]，然後按一下您剛剛建立的行動服務。

5. 按一下 [**儀表板**] 索引標籤並記下 [**網站 URL**]，然後按一下 [**管理金鑰**] 並記下 [**應用程式索引金鑰**]。

   	![](./media/mobile-services-ios-enable-mobile-service-access/mobile-dashboard-tab.png)

  	從應用程式程式碼存取行動服務時，您將會用到這些值。

6. 回到 Xcode，開啟 TodoService.m 並尋找下列已加上註解的程式碼行：

        // Initialize the Mobile Service client with your URL and key.

    在此註解之後，新增下列程式碼行：

        self.client = [MSClient clientWithApplicationURLString:@"APPURL" applicationKey:@"APPKEY"];

    這會建立行動服務用戶端的執行個體。

7. 使用您在步驟 6 中從行動服務取得的 URL 和應用程式金鑰，來取代此程式碼中的 **APPURL** 和 **APPKEY** 值。

8. 尋找下列已加上註解的程式碼行：

        // Create an MSTable instance to allow us to work with the TodoItem table.

    在此註解之後，新增下列程式碼行：

        self.table = [self.client tableWithName:@"TodoItem"];

    這會建立 TodoItem 資料表執行個體。

9. 尋找下列已加上註解的程式碼行：

 	    // Create a predicate that finds items where complete is false

    在此註解之後，新增下列程式碼行：

        NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];

    This creates a query to return all tasks that have not yet been completed.

10. 尋找下列已加上註解的程式碼行：

        // Query the TodoItem table and update the items property with the results from the service

   	使用下列程式碼來取代註解和後續 **completion** 區塊引動：

        [self.table readWhere:predicate completion:^(NSArray *results, NSInteger totalCount, NSError *error)
		{
		   self.items = [results mutableCopy];
           completion();
        }];

11. 尋找 **addItem** 方法，並使用下列程式碼來取代方法的主體：

        // Insert the item into the TodoItem table and add to the items array on completion
        [self.table insert:item completion:^(NSDictionary *result, NSError *error) {
            NSUInteger index = [items count];
            [(NSMutableArray *)items insertObject:item atIndex:index];

            // Let the caller know that we finished
            completion(index);
        }];

    此程式碼會傳送插入要求給行動服務。

12. 尋找 **completeItem** 方法，並找出下列加上註解的程式碼行：

        // Update the item in the TodoItem table and remove from the items array on completion

    以下列程式碼取代方法的主體 (從該點至方法的結尾)：

        // Update the item in the TodoItem table and remove from the items array on completion
        [self.table update:mutable completion:^(NSDictionary *item, NSError *error) {

            // Get a fresh index in case the list has changed
            NSUInteger index = [items indexOfObjectIdenticalTo:mutable];

            [mutableItems removeObjectAtIndex:index];

            // Let the caller know that we have finished
            completion(index);
	    }];

   	此程式碼會在 TodoItems 被標示為已完成時將它移除。

13. 在 TodoListController.m 中，尋找 **onAdd** 方法並以下列程式碼進行覆寫：

      - (IBAction)onAdd:(id)sender
      {
          if (itemText.text.length  == 0)
          {
              return;
          }

          NSDictionary *item = @{ @"text" : itemText.text, @"complete" : @NO };
          UITableView *view = self.tableView;
          [self.todoService addItem:item completion:^(NSUInteger index)
          {
              NSIndexPath *indexPath = [NSIndexPath indexPathForRow:index inSection:0];
              [view insertRowsAtIndexPaths:@[ indexPath ]
                          withRowAnimation:UITableViewRowAnimationTop];
          }];

          itemText.text = @"";
      }


應用程式現已更新為使用行動服務進行後端儲存，我們可以開始在行動服務中測試應用程式。
