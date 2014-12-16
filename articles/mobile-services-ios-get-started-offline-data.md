<properties urlDisplayName="Using Offline Data" pageTitle="在行動服務中使用離線資料同步 (iOS) | 行動開發人員中心" metaKeywords="" description="Learn how to use Azure Mobile Services to cache and sync offline data in your iOS application" metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Using Offline data sync in Mobile Services" authors="krisragh" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="10/10/2014" ms.author="krisragh" />

# 開始在行動服務中使用離線資料同步


[WACOM.INCLUDE [mobile-services-selector-offline](../includes/mobile-services-selector-offline.md)]


本教學課程將說明 iOS 上的行動服務的離線同步功能，此功能可讓開發人員撰寫即使在使用者沒有網路存取的情況下仍可使用的應用程式。

離線同步具有幾種潛在用途：

* 在裝置上本機快取伺服器資料，以改善應用程式回應性
* 讓應用程式能夠在網路連線中斷後恢復
* 讓使用者即使在沒有網路存取的情況下仍能建立及修改資料，而支援連線微弱或無連線的情況
* 同步多個裝置之間的資料，並在兩個裝置修改相同的記錄時偵測衝突

本教學課程將在[開始使用行動服務]教學課程中說明如何更新應用程式，以支援 Azure 行動服務的離線功能。接著，您會在中斷連線的離線狀態下新增資料、將這些項目同步處理至線上資料庫，然後登入 Azure 管理入口網站，以檢視執行應用程式時對資料所做的變更。

>[WACOM.NOTE] 若要完成此教學課程，您需要 Azure 帳戶。如果您沒有帳戶，可以註冊 Azure 試用版並取得多達 10 個免費的行動服務，即使在試用期結束之後仍可繼續使用這些服務。如需詳細資訊，請參閱 <a href="http://www.windowsazure.com/zh-tw/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">Azure 免費試用</a>。

本教學課程旨在協助您深入了解如何透過行動服務，以使用 Azure 儲存並擷取 Windows 市集應用程式中的資料。因此，本主題將逐步說明已在行動服務快速入門中完成的許多步驟。如果這是您第一次接觸行動服務，請考慮首先完成教學課程[開始使用行動服務]。

>[WACOM.NOTE] 您可以略過這幾節，直接下載一個已具有離線支援和本主題所述一切功能的使用者入門專案。  若要下載具有離線支援的專案，請參閱[開始使用離線 iOS 範例]。


本教學課程將逐步引導您完成下列基本步驟：

1. [取得範例快速入門應用程式]
2. [下載預覽 SDK 和更新架構]
3. [設定 Core Data]
4. [定義 Core Data 模型]
5. [初始化及使用同步資料表和同步內容]
6. [測試應用程式]

## <a name="get-app"></a>取得範例快速入門應用程式

依照[開始使用行動服務]的指示，下載快速入門專案。

## <a name="update-app"></a>下載預覽 SDK 和更新架構

1. 為了將離線支援加入至我們的應用程式，讓我們取得一個支援離線同步的行動服務 iOS SDK 版本。因為我們是以預覽功能來啟動它，它尚未納入正式可下載的 SDK 中。[請在這裡下載預覽 SDK]。

2. 然後，在 Xcode 中，從專案移除現有的 **WindowsAzureMobileServices.framework** 參考，請選取它，按一下 [**編輯**] 功能表，然後選取 [移至垃圾筒]，以確實刪除檔案。

      ![][update-framework-1]

3. 將新的預覽 SDK 的內容解壓縮，並拖放至新的 **WindowsAzureMobileServices.framework** SDK 上取代舊的 SDK。請確定已選取 [Copy items into destination group's folder (if needed)]。

      ![][update-framework-2]


## <a name="setup-core-data"></a>設定 Core Data

1. iOS 行動服務 SDK 可讓您使用任何符合 **MSSyncContextDataSource** 通訊協定的持續性存放區。SDK 中包含一個根據 [Core Data] 實作此通訊協定的資料來源。

2. 由於應用程式使用 Core Data，請瀏覽至 [**目標**] --> [**Build Phases**]，在 [**Link Binary with Libraries**] 下方，加入 [**CoreData.framework**]。

      ![][core-data-1]

      ![][core-data-2]

3. 我們要將 Core Data 加入至 Xcode 中尚未支援 Core Data 的現有專案。因此，我們需要將額外的未定案程式碼加入至專案的各個部分。首先，在 **QSAppDelegate.h** 中加入下列程式碼：

        #import <UIKit/UIKit.h>  
        #import <CoreData/CoreData.h>  

        @interface QSAppDelegate : UIResponder <UIApplicationDelegate>  

        @property (strong, nonatomic) UIWindow *window;  

        @property (readonly, strong, nonatomic) NSManagedObjectContext *managedObjectContext;  
        @property (readonly, strong, nonatomic) NSManagedObjectModel *managedObjectModel;  
        @property (readonly, strong, nonatomic) NSPersistentStoreCoordinator *persistentStoreCoordinator;  

        - (void)saveContext;  
        - (NSURL *)applicationDocumentsDirectory;  

        @end

4. 接下來，使用下列程式碼取代 **QSAppDelegate.m** 的內容。當您在 Xcode 中建立新的應用程式，並選取 [Use Core Data] 核取方塊時，所產生的程式碼與此幾乎相同，差別在於初始化 **_managedObjectContext** 時改以使用私用佇列並行類型。透過這項變更，您幾乎可立即使用 Core Data，只是還沒有開始使用它。

        #import "QSAppDelegate.h"

        @implementation QSAppDelegate

        @synthesize managedObjectContext = _managedObjectContext;
        @synthesize managedObjectModel = _managedObjectModel;
        @synthesize persistentStoreCoordinator = _persistentStoreCoordinator;

        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
        {
            return YES;
        }

        - (void)saveContext
        {
            NSError *error = nil;
            NSManagedObjectContext *managedObjectContext = self.managedObjectContext;
            if (managedObjectContext != nil) {
                if ([managedObjectContext hasChanges] && ![managedObjectContext save:&error]) {
                    // Replace this implementation with code to handle the error appropriately.
                    // abort() causes the application to generate a crash log and terminate. You should not use this function in a shipping application, although it may be useful during development.
                    NSLog(@"Unresolved error %@, %@", error, [error userInfo]);
                    abort();
                }
            }
        }

        #pragma mark - Core Data stack

        // Returns the managed object context for the application.
        // If the context doesn't already exist, it is created and bound to the persistent store coordinator for the application.
        - (NSManagedObjectContext *)managedObjectContext
        {
            if (_managedObjectContext != nil) {
                return _managedObjectContext;
            }

            NSPersistentStoreCoordinator *coordinator = [self persistentStoreCoordinator];
            if (coordinator != nil) {
                _managedObjectContext = [[NSManagedObjectContext alloc] initWithConcurrencyType:NSPrivateQueueConcurrencyType];
                [_managedObjectContext setPersistentStoreCoordinator:coordinator];
            }
            return _managedObjectContext;
        }

        // Returns the managed object model for the application.
        // If the model doesn't already exist, it is created from the application's model.
        - (NSManagedObjectModel *)managedObjectModel
        {
            if (_managedObjectModel != nil) {
                return _managedObjectModel;
            }
            NSURL *modelURL = [[NSBundle mainBundle] URLForResource:@"QSTodoDataModel" withExtension:@"momd"];
            _managedObjectModel = [[NSManagedObjectModel alloc] initWithContentsOfURL:modelURL];
            return _managedObjectModel;
        }

        // Returns the persistent store coordinator for the application.
        // If the coordinator doesn't already exist, it is created and the application's store added to it.
        - (NSPersistentStoreCoordinator *)persistentStoreCoordinator
        {
            if (_persistentStoreCoordinator != nil) {
                return _persistentStoreCoordinator;
            }

            NSURL *storeURL = [[self applicationDocumentsDirectory] URLByAppendingPathComponent:@"qstodoitem.sqlite"];

            NSError *error = nil;
            _persistentStoreCoordinator = [[NSPersistentStoreCoordinator alloc] initWithManagedObjectModel:[self managedObjectModel]];
            if (![_persistentStoreCoordinator addPersistentStoreWithType:NSSQLiteStoreType configuration:nil URL:storeURL options:nil error:&error]) {
                /*
                 Replace this implementation with code to handle the error appropriately.

                 abort() causes the application to generate a crash log and terminate. You should not use this function in a shipping application, although it may be useful during development.

                 Typical reasons for an error here include:
                 * The persistent store is not accessible;
                 * The schema for the persistent store is incompatible with current managed object model.
                 Check the error message to determine what the actual problem was.

                 If the persistent store is not accessible, there is typically something wrong with the file path. Often, a file URL is pointing into the application's resources directory instead of a writeable directory.

                 If you encounter schema incompatibility errors during development, you can reduce their frequency by:
                 * Simply deleting the existing store:
                 [[NSFileManager defaultManager] removeItemAtURL:storeURL error:nil]

                 * Performing automatic lightweight migration by passing the following dictionary as the options parameter:
                 @{NSMigratePersistentStoresAutomaticallyOption:@YES, NSInferMappingModelAutomaticallyOption:@YES}

                 Lightweight migration will only work for a limited set of schema changes; consult "Core Data Model Versioning and Data Migration Programming Guide" for details.

                 */

                NSLog(@"Unresolved error %@, %@", error, [error userInfo]);
                abort();
            }

            return _persistentStoreCoordinator;
        }

        #pragma mark - Application's Documents directory

        // Returns the URL to the application's Documents directory.
        - (NSURL *)applicationDocumentsDirectory
        {
            return [[[NSFileManager defaultManager] URLsForDirectory:NSDocumentDirectory inDomains:NSUserDomainMask] lastObject];
        }

        @end

## <a name="defining-core-data"></a>定義 Core Data 模型

1. 現在讓我們定義資料模型，繼續以 Core Data 來設定應用程式。我們還不會開始此資料模型。首先，讓我們先定義 Core Data 模型或結構描述。若要開始，請按一下 [**檔案**] -> [**新增檔案**]，在 [**Core Data**] 區段中選取 [**資料模型**]。當提示您輸入檔案名稱，請使用 **QSTodoDataModel.xcdatamodeld**。

      ![][defining-core-data-main-screen]

2. 接下來，定義我們需要的實際實體 (資料表)。我們將使用 Core Data 模型編輯器來建立三個資料表 (實體)。若要深入了解，請參閱 [Core Data 模型編輯器說明]。。

  * TodoItem：用於儲存項目本身
  * MS_TableOperations：追蹤需要與伺服器同步的項目 (為了讓離線功能運作)
  * MS_TableOperationErrors：追蹤離線同步處理期間發生的任何錯誤 (為了讓離線功能運作)

      ![][defining-core-data-model-editor]

3. 定義三個實體，如下所示。儲存模型，並建置專案，以確定一切都沒問題。現在我們已設定好應用程式來使用 Core Data，但應用程式尚未使用它。

      ![][defining-core-data-todoitem-entity]

      ![][defining-core-data-tableoperations-entity]

      ![][defining-core-data-tableoperationerrors-entity]


    **TodoItem**

    | 屬性  |  類型   |
    |----------- |  ------ |
    | id         | 字串  |
    | complete   | 布林 |
    | text       | 字串  |
    | ms_version | 字串  |

    **MS_TableOperations**

    | 屬性  |    類型     |
    |----------- |   ------    |
    | id         | 整數 64  |
    | properties | 二進位資料 |
    | itemId     | 字串      |
    | table      | 字串      |

    **MS_TableOperationErrors**

    | 屬性  |    類型     |
    |----------- |   ------    |
    | id         | 字串      |
    | properties | 二進位資料 |

## <a name="setup-sync"></a> 初始化及使用同步資料表和同步內容

1. 為了開始快取離線資料，讓我們將 **MSTable** 改成使用 **MSSyncTable** 來存取行動服務。不同於一般 **MSTable**，同步資料表是本機資料表，能夠將本機所做的變更推播至遠端資料表，並在本機提取這些變更。在 **QSTodoService.h** 中，移除 **table** 屬性的定義：

        @property (nonatomic, strong)   MSTable *table;

    Add a new line to define the **syncTable** property:

        @property (nonatomic, strong)   MSTable *syncTable;

2. Add the following import statement at the top of **QSTodoService.m**:

        #import "QSAppDelegate.h"

3. 在 **QSTodoService.m** 中，從 **init** 中移除下列兩行：

        // Create an MSTable instance to allow us to work with the TodoItem table
        self.table = [_client tableWithName:@"TodoItem"];

    Instead, add these two new lines in its place:

        // Create an MSSyncTable instance to allow us to work with the TodoItem table
        self.syncTable = [self.client syncTableWithName:@"TodoItem"];

4. 接下來，同樣在 **QSTodoService.m** 中，讓我們以上述的 Core Data 型資料存放區實作來初始化 **MSClient** 中的同步處理內容。此內容負責追蹤哪些項目已在本機變更，並在推播作業開始時將這些變更推播至伺服器。為了初始化內容，我們需要資料來源 (**MSCoreDataStore** 的通訊協定實作) 和選用的 **MSSyncContextDelegate** 實作。將這幾行插入您剛插入的那兩行上方。

        QSAppDelegate *delegate = (QSAppDelegate *)[[UIApplication sharedApplication] delegate];
        NSManagedObjectContext *context = delegate.managedObjectContext;
        MSCoreDataStore *store = [[MSCoreDataStore alloc] initWithManagedObjectContext:context];

        self.client.syncContext = [[MSSyncContext alloc] initWithDelegate:nil dataSource:store callback:nil];

5. 接下來，讓我們更新 **QSTodoService.m** 中的作業來使用同步資料表，而不是一般資料表。首先，使用下列實作來取代 **refreshDataOnSuccess**。這樣會從服務擷取資料，讓我們將它更新為使用同步資料表，要求同步資料表只提取符合準則的項目，然後開始將本機同步資料表中的資料載入至服務的 **items** 屬性中。透過此程式碼，  **refreshDataOnSuccess** 會將遠端資料表中的資料提取到本機 (同步) 資料表。我們通常只會提取資料表的子集，所以不會將不必要的資料載入到用戶端。

    在此作業和更往下的其餘作業中，我們將 completion 區塊的呼叫包裝在主執行緒的 **dispatch_async** 呼叫中。當我們初始化同步內容時，我們不傳遞回呼參數，以便架構建立預設的序列佇列，將所有 syncTable 作業的結果分派至背景執行緒。在修改 UI 元件時，我們需要將程式碼分派回到 UI 執行緒。

          -(void) refreshDataOnSuccess:(QSCompletionBlock)completion
          {
              NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];
              MSQuery *query = [self.syncTable queryWithPredicate:predicate];

              [query orderByAscending:@"text"];
              [query readWithCompletion:^(MSQueryResult *result, NSError *error) {
                  [self logErrorIfNotNil:error];

                  self.items = [result.items mutableCopy];

                  // Let the caller know that we finished
                  dispatch_async(dispatch_get_main_queue(), ^{
                      completion();
                  });
              }];
          }

6. 接下來，取代 **QSTodoService.m** 中的 **addItem**，如下所示。透過這項變更，您可以將作業排入佇列中，以便將變更推播至遠端服務，並讓每個人都可以看到：

        -(void)addItem:(NSDictionary *)item completion:(QSCompletionWithIndexBlock)completion
        {
            // Insert the item into the TodoItem table and add to the items array on completion
            [self.syncTable insert:item completion:^(NSDictionary *result, NSError *error)
             {
                 [self logErrorIfNotNil:error];

                 NSUInteger index = [items count];
                 [(NSMutableArray *)items insertObject:result atIndex:index];

                 // Let the caller know that we finished
                 dispatch_async(dispatch_get_main_queue(), ^{
                     completion(index);
                 });
             }];
        }

7. 更新 **QSTodoService.m** 中的 **completeItem**，如下所示。不同於在 **MSTable** 中，**MSSyncTable** 的 **update** 作業的 completion 區塊沒有更新的項目。使用 **MSTable** 時，伺服器會修改正在更新的項目，而所做的修改會反映在用戶端。使用 **MSSyncTable** 時，更新的項目不會修改，且 completion 區塊沒有參數。

        -(void) completeItem:(NSDictionary *)item completion:(QSCompletionWithIndexBlock)completion
        {
            // Cast the public items property to the mutable type (it was created as mutable)
            NSMutableArray *mutableItems = (NSMutableArray *) items;

            // Set the item to be complete (we need a mutable copy)
            NSMutableDictionary *mutable = [item mutableCopy];
            [mutable setObject:@YES forKey:@"complete"];

            // Replace the original in the items array
            NSUInteger index = [items indexOfObjectIdenticalTo:item];
            [mutableItems replaceObjectAtIndex:index withObject:item];

            // Update the item in the TodoItem table and remove from the items array on completion
            [self.syncTable update:mutable completion:^(NSError *error) {

                [self logErrorIfNotNil:error];

                NSUInteger index = [items indexOfObjectIdenticalTo:mutable];
                if (index != NSNotFound)
                {
                    [mutableItems removeObjectAtIndex:index];
                }

                // Let the caller know that we have finished
                dispatch_async(dispatch_get_main_queue(), ^{
                    completion(index);
                });

            }];
        }

8. 將下列的 **syncData** 作業宣告加入至 **QSTodoService.h**：

        - (void)syncData:(QSCompletionBlock)completion;

     Add the corresponding implementation of **syncData** to **QSTodoService.m**. We're adding this operation to update the sync table with remote changes.

          -(void)syncData:(QSCompletionBlock)completion
           {
              // Create a predicate that finds items where complete is false
              NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];

              MSQuery *query = [self.syncTable queryWithPredicate:predicate];

              // Pulls data from the remote server into the local table. We're only
              // pulling the items which we want to display (complete == NO).
              [self.syncTable pullWithQuery:query completion:^(NSError *error) {
                  [self logErrorIfNotNil:error];
                  [self refreshDataOnSuccess:completion];
           }];
          }

9. Back in **QSTodoListViewController.m**, change the implementation of **refresh** to call **syncData** instead of **refreshDataOnSuccess**:

        -(void) refresh
        {
            [self.refreshControl beginRefreshing];
            [self.todoService syncData:^
             {
                  [self.refreshControl endRefreshing];
                  [self.tableView reloadData];
             }];
        }

10. 同樣在 **QSTodoListViewController.m** 中，使用下列程式碼取代 **viewDidLoad** 作業結尾的 **[self refresh]** 呼叫：

        // load the local data, but don't pull from server
        [self.todoService refreshDataOnSuccess:^
         {
             [self.refreshControl endRefreshing];
             [self.tableView reloadData];
         }];

11. 現在，讓我們真正地離線測試應用程式。將一些項目加入至應用程式中，然後瀏覽 Azure 管理入口網站，查看您應用程式的 [**資料**] 索引標籤。您會看到尚未加入任何項目。

12. 接下來，從頂端拖曳，對應用程式執行重新整理動作。然後再次瀏覽 Azure 管理入口網站，並查看 [**資料**] 索引標籤。您會看到現在儲存在雲端中的資料。您也可以在加入項目之後關閉應用程式 (或在編輯項目之後，如果應用程式已啟用編輯項目的功能)。當應用程式重新啟動時，它會與伺服器同步處理，並儲存變更。

13. 當用戶端在本機對項目執行某些變更時，這些變更會儲存在要傳送到伺服器的同步內容中。*push* 作業將追蹤的變更傳送到遠端伺服器，但是在這裡，我們沒有對伺服器執行任何推播呼叫。不過，*在執行提取之前，任何暫止的作業通常會推播到伺服器*，因此仍會自動進行推播，以避免發生衝突。這就是為什麼此應用程式中沒有明確呼叫 *push* 的原因。

## <a name="test-app"></a>測試應用程式

最後，我們來離線測試應用程式。在應用程式中加入一些項目。然後移至入口網站並瀏覽資料 (或使用 PostMan 或 Fiddler 等網路工具直接查詢資料表)。

您會看到項目尚未加入至服務。現在，請從頂端拖曳，以在應用程式中執行重新整理動作。您會看到資料現在已儲存在雲端中。您甚至可以在加入一些項目之後關閉應用程式。當您再次啟動應用程式時，它會與伺服器同步處理，並儲存您所做的變更。

## 後續步驟

* [處理行動服務的離線支援衝突]

<!-- URLs. -->

[取得範例快速入門應用程式]: #get-app
[下載預覽 SDK 和更新架構]: #update-app
[設定 Core Data]: #setup-core-data
[定義 Core Data 模型]: #defining-core-data
[初始化及使用同步資料表和同步內容]: #setup-sync
[測試應用程式]: #test-app

[core-data-1]: ./media/mobile-services-ios-get-started-offline-data/core-data-1.png
[core-data-2]: ./media/mobile-services-ios-get-started-offline-data/core-data-2.png
[core-data-3]: ./media/mobile-services-ios-get-started-offline-data/core-data-3.png
[defining-core-data-main-screen]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-main-screen.png
[defining-core-data-model-editor]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-model-editor.png
[defining-core-data-tableoperationerrors-entity]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-tableoperationerrors-entity.png
[defining-core-data-tableoperations-entity]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-tableoperations-entity.png
[defining-core-data-todoitem-entity]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-todoitem-entity.png
[update-framework-1]: ./media/mobile-services-ios-get-started-offline-data/update-framework-1.png
[update-framework-2]: ./media/mobile-services-ios-get-started-offline-data/update-framework-2.png




[Core Data 模型編輯器說明]: https://developer.apple.com/library/mac/recipes/xcode_help-core_data_modeling_tool/Articles/about_cd_modeling_tool.html
[建立輸出連線]: https://developer.apple.com/library/mac/recipes/xcode_help-interface_builder/articles-connections_bindings/CreatingOutlet.html
[建置使用者介面]: https://developer.apple.com/library/mac/documentation/ToolsLanguages/Conceptual/Xcode_Overview/Edit_User_Interfaces/edit_user_interface.html
[在分鏡腳本的場景之間加入轉場]: https://developer.apple.com/library/ios/recipes/xcode_help-IB_storyboard/chapters/StoryboardSegue.html#//apple_ref/doc/uid/TP40014225-CH25-SW1
[將場景加入至分鏡腳本]: https://developer.apple.com/library/ios/recipes/xcode_help-IB_storyboard/chapters/StoryboardScene.html

[Core Data]: https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/CoreData/cdProgrammingGuide.html
[在這裡下載預覽 SDK]: http://aka.ms/Gc6fex
[如何使用行動服務 iOS 用戶端程式庫]: /zh-tw/documentation/articles/mobile-services-ios-how-to-use-client-library/
[開始使用離線 iOS 範例]: https://github.com/Azure/mobile-services-samples/tree/master/TodoOffline/iOS/blog20140611


[開始使用行動服務]: /zh-tw/documentation/articles/mobile-services-ios-get-started/
[開始使用資料]: /zh-tw/documentation/articles/mobile-services-ios-get-started-data/
[處理行動服務的離線支援衝突]: /zh-tw/documentation/articles/mobile-services-ios-handling-conflicts-offline-data/
