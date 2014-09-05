<properties linkid="develop-mobile-tutorials-get-started-with-data-ios" urlDisplayName="Get Started with Data" pageTitle="Get started with data (iOS) | Mobile Dev Center" metaKeywords="Azure iOS data, Azure mobile services data, " description="Learn how to get started using Mobile Services to leverage data in your iOS app." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="glenga" solutions="" manager="" editor="" />

開始使用行動服務中的資料
========================

[Windows 市集 C#](/en-us/develop/mobile/tutorials/get-started-with-data-dotnet "Windows 市集 C#")[Windows 市集 JavaScript](/en-us/develop/mobile/tutorials/get-started-with-data-js "Windows 市集 JavaScript")[Windows Phone](/en-us/develop/mobile/tutorials/get-started-with-data-wp8 "Windows Phone")[iOS](/en-us/develop/mobile/tutorials/get-started-with-data-ios "iOS")[Android](/en-us/develop/mobile/tutorials/get-started-with-data-android "Android")[HTML](/en-us/develop/mobile/tutorials/get-started-with-data-html "HTML")[Xamarin.iOS](/en-us/develop/mobile/tutorials/get-started-with-data-xamarin-ios "Xamarin.iOS")[Xamarin.Android](/en-us/develop/mobile/tutorials/get-started-with-data-xamarin-android "Xamarin.Android")

本主題將示範如何使用 Azure 行動服務，進而運用 iOS 應用程式中的資料。在本教學課程中，您將下載應用程式，並在記憶體中儲存資料、建立新的行動服務、將行動服務與該應用程式整合，然後登入 Azure 管理入口網站查看執行應用程式時所做的資料變更。

**注意**

本教學課程旨在協助您深入了解如何透過行動服務，來使用 Azure 儲存並擷取 iOS 應用程式中的資料。因此，本主題將逐步說明已在行動服務快速入門中完成的許多步驟。如果這是您第一次接觸行動服務，請考慮先完成教學課程[開始使用行動服務](/en-us/develop/mobile/tutorials/get-started-ios)。

本教學課程將逐步引導您完成下列基本步驟：

1.  [下載 iOS 應用程式專案](#download-app)
2.  [建立行動服務](#create-service)
3.  [新增用於儲存的資料表](#add-table)
4.  [更新應用程式以使用行動服務](#update-app)
5.  [針對行動服務進行應用程式測試](#test-app)

本教學課程需要 [Mobile Services iOS SDK](https://go.microsoft.com/fwLink/p/?LinkID=266533) 和 [XCode 4.5](https://go.microsoft.com/fwLink/p/?LinkID=266532) 及 iOS 5.0 或更新版本。

**注意**

若要完成此教學課程，您需要 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 [Azure 免費試用](http://www.windowsazure.com/zh-tw/pricing/free-trial/?WT.mc_id=A756A2826&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-ios%2F)。

下載專案下載 GetStartedWithData 專案
------------------------------------

本教學課程以 [GetStartedWithData 應用程式](http://go.microsoft.com/fwlink/p/?LinkId=268622) (iOS 應用程式) 為基礎而建立。除了會將新增項目儲存在本機記憶體中之外，由此應用程式與行動服務 iOS 快速入門所產生的應用程式兩者的 UI 完全相同。

1.  下載 GetStartedWithData [範例應用程式](http://go.microsoft.com/fwlink/p/?LinkId=268622)。

2.  在 Xcode 中，開啟此下載專案並檢查 QSTodoService.m 檔案。

   	Notice that there are eight **// TODO** comments that specify the steps you must take to make this app work with your mobile service.

3.  按 **[執行]** 按鈕 (或 Command+R 鍵)，以重建專案並啟動應用程式。

4.  在應用程式的文字方塊中鍵入一些文字，然後按一下 **+** 按鈕。

   	![](./media/mobile-services-ios-get-started-data/mobile-quickstart-startup-ios.png)

   	Notice that the saved text is displayed in the list below.

建立行動服務在管理入口網站中建立新的行動服務
--------------------------------------------

[WACOM.INCLUDE [mobile-services-create-new-service-data](../includes/mobile-services-create-new-service-data.md)]

新增資料表將資料表新增至行動服務
--------------------------------

[WACOM.INCLUDE [mobile-services-create-new-service-data-2](../includes/mobile-services-create-new-service-data-2.md)]

更新應用程式更新應用程式以使用行動服務進行資料存取
--------------------------------------------------

您的行動服務已準備就緒，現在可以更新應用程式以便在行動服務 (而非本機收集) 中儲存項目。

1.  如果您尚未安裝 [Mobile Services iOS SDK](https://go.microsoft.com/fwLink/p/?LinkID=266533)，請立即安裝。

2.  在 Xcode 的 [專案導覽] 中，開啟位於 [快速入門] 資料夾中的 TodoService.m 和 TodoService.h 檔案，並新增下列 import 陳述式：

         #import <WindowsAzureMobileServices/WindowsAzureMobileServices.h>  

3.  在 ToDoService.h 檔案中，尋找下列已加上註解的程式碼行：

        // Create an MSClient property comment in the #interface declaration for the TodoService. 

   	After this comment, add the following line of code:

        @property (nonatomic, strong)   MSClient *client;

   	This creates a property that represents the MSClient that connects to the service

4.  在 TodoService.m 檔案中，尋找下列已加上註解的程式碼行：

        // Create an MSTable property for your items. 

   	After this comment, add the following line of code inside the @interface declaration:

        @property (nonatomic, strong)   MSTable *table;

   	This creates a property representation for your mobile services table.

5.  在管理入口網站中，按一下 **[行動服務]**，然後按一下您剛剛建立的行動服務。

6.  按一下 **[儀表板]** 索引標籤並記下 **[網站 URL]**，然後按一下 **[管理金鑰]** 並記下 **[應用程式金鑰]**。

   	![](./media/mobile-services-ios-get-started-data/mobile-dashboard-tab.png)

  	從應用程式程式碼存取行動服務時，您將會用到這些值。

1.  回到 Xcode，開啟 TodoService.m 並尋找下列已加上註解的程式碼行：

         // Initialize the Mobile Service client with your URL and key.

    在此註解之後，新增下列程式碼行：

         self.client = [MSClient clientWithApplicationURLString:@"APPURL" applicationKey:@"APPKEY"];

    這會建立行動服務用戶端的執行個體。

2.  使用您在步驟 6 中從行動服務取得的 URL 和應用程式金鑰，來取代此程式碼中的 **APPURL** 和 **APPKEY** 值。

3.  尋找下列已加上註解的程式碼行：

         // Create an MSTable instance to allow us to work with the TodoItem table.

    在此註解之後，新增下列程式碼行：

         self.table = [self.client tableWithName:@"TodoItem"];

    這會建立 TodoItem 資料表執行個體。

4.  尋找下列已加上註解的程式碼行：

 	    // 建立述詞，在 refreshDataOnSuccess 方法中尋找包含完成為 False 註解的項目。

    After this comment, add the following line of code:

        NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];

    This creates a query to return all tasks that have not yet been completed.

1.  尋找下列已加上註解的程式碼行：

        // Query the TodoItem table and update the items property with the results from the service.

	使用下列程式碼來取代註解和後續 **completion** 區塊引動：

        // Query the TodoItem table and update the items property with the results from the service
        [self.table readWithPredicate:predicate completion:^(NSArray *results, NSInteger totalCount, NSError *error) 
        {
           self.items = [results mutableCopy];
           completion();
        }]; 

1.  尋找 **addItem** 方法，並使用下列程式碼來取代方法的主體：

        // Insert the item into the TodoItem table and add to the items array on completion
        [self.table insert:item completion:^(NSDictionary *result, NSError *error) {
        NSUInteger index = [items count];
        [(NSMutableArray *)items insertObject:item atIndex:index];
         
        // Let the caller know that we finished
        completion(index);
        }];

    此程式碼會傳送插入要求給行動服務。

2.  尋找 **completeItem** 方法，並使用下列程式碼來取代方法的主體：

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

測試應用程式在新的行動服務上測試應用程式
----------------------------------------

1.  在 Xcode 中，選取要部署到 iPhone 或 iPad 上的模擬器，按 **[執行]** 按鈕 (或 Command+R 鍵)，以重建專案並啟動應用程式。

   	This executes your Azure Mobile Services client, built with the iOS SDK, that queries items from your mobile service.

2.  和之前一樣，在文字方塊中鍵入文字，然後按一下 **+** 按鈕。

   	This sends a new item as an insert to the mobile service.

3.  在[管理入口網站](https://manage.windowsazure.com/)中按一下 **[行動服務]**，然後按一下您的行動服務。

4.  按一下 **[資料]** 索引標籤，然後按一下 **[瀏覽]**。

   	![](./media/mobile-services-ios-get-started-data/mobile-todoitem-data-browse.png)
  
   	Notice that the **TodoItem** table now contains data, with id values generated by Mobile Services, and that columns have been automatically added to the table to match the TodoItem class in the app.

這將結束 iOS 的**開始使用資料**教學課程。

後續步驟
--------

本教學課程將示範啟用 iOS 應用程式來使用行動服務資料的基本概念。

接下來，可考慮進入下列其中一個教學課程，這些教學課程都是以您在本教學課程中建立的 GetStartedWithData 應用程式為基礎：

-   [使用指令碼來驗證和修改資料](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet)
    深入了解在行動服務中，使用伺服器指令碼來驗證和變更從應用程式傳送的資料。

-   [使用分頁縮小查詢範圍](/en-us/develop/mobile/tutorials/add-paging-to-data-ios)
    了解如何在查詢中使用分頁，來控制單一要求中所處理的資料量。

完成資料數列之後，請嘗試下列其他 iOS 教學課程：

-   [開始使用驗證](/en-us/develop/mobile/tutorials/get-started-with-users-ios)
    了解如何驗證應用程式的使用者。

-   [開始使用推播通知](/en-us/develop/mobile/tutorials/get-started-with-push-ios)
    了解如何透過行動服務將非常基本的推播通知傳送至應用程式。


