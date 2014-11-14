<properties urlDisplayName="Handle Conflicts with Offline Data" pageTitle="處理行動服務 (iOS) 中的離線資料衝突 | 行動開發人員中心" metaKeywords="" description="了解您在 iOS 應用程式中同步離線資料時應如何使用 Azure 行動服務處理衝突" metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="處理行動服務中的離線資料衝突" authors="krisragh" manager="dwrede"/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="dotnet" ms.topic="article" ms.date="10/10/2014" ms.author="krisragh" />

# 處理行動服務中的離線資料同步衝突

<div class="dev-center-tutorial-selector sublanding">
<a href="/zh-tw/documentation/articles/mobile-services-windows-store-dotnet-handling-conflicts-offline-data" title="Windows 市集 C#">Windows 市集 C#</a>
<a href="/zh-tw/documentation/articles/mobile-services-windows-phone-handling-conflicts-offline-data" title="Windows Phone">Windows Phone</a>
<a href="/zh-tw/documentation/articles/mobile-services-ios-handling-conflicts-offline-data" title="iOS" class="current">iOS</a>
</div>

本主題將說明在使用 Azure 行動服務的離線功能時，應如何同步處理資料及處理衝突。本教學課程會以先前的教學課程[開始使用離線資料][開始使用離線資料]中的步驟和範例應用程式為基礎。在開始本教學課程之前，您必須先完成[開始使用離線資料][開始使用離線資料]。

> [WACOM.NOTE] 若要完成此教學課程，您需要 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 [Azure 免費試用][Azure 免費試用]。

## 完成開始使用離線資料教學課程

遵循[開始使用離線資料][開始使用離線資料]教學課程中的指示，完成該專案。我們將使用在該教學課程中完成的專案，作為本教學課程的起點。

## 更新應用程式專案以允許編輯

我們將會更新在[開始使用離線資料][開始使用離線資料]中完成的專案，以允許項目的編輯。目前，如果您在兩支電話上執行這個應用程式，請在這兩支電話的本機上變更相同項目，然後將變更送回伺服器，如此將會引發失敗，並產生衝突。

SDK 中的離線同步功能可讓您透過程式碼處理此類衝突，並可讓您動態決定如何處理衝突的項目。變更快速入門專案，可讓我們實驗這項功能。

### 更新待辦事項清單檢視控制器

1.  我們將更新 iPhone 腳本。如果您使用 iPad，也請遵循相同的步驟來更新 iPad 腳本。

2.  在 Xcode 的 [專案導覽] 中選取 [MainStoryboard\_iPhone.storyboard]，然後選取 [待辦事項清單檢視控制器]。在頂端功能表中，按一下 [編輯器] -\> [Embed In] -\> [Navigation Controller]

    ![][0]

3.  接著，在 [待辦事項清單檢視控制器] 中選取資料表檢視儲存格，並將其 [配件] 模式設為 [Disclosure indicator]。公開指標會為使用者指出如果他們點選了相關聯的資料表檢視控制器，就會顯示新的檢視。公開指標不會產生事件。

    ![][1]

4.  在 **TodoListViewController.m** 中，一併移除下列作業及其內容。我們不需要這些項目：

        -(NSString *)tableView:(UITableView *)tableView titleForDeleteConfirmationButtonForRowAtIndexPath:(NSIndexPath *)indexPath

        -(UITableViewCellEditingStyle)tableView:(UITableView *)tableView editingStyleForRowAtIndexPath:(NSIndexPath *)indexPath

        -(void)tableView:(UITableView *)tableView commitEditingStyle:(UITableViewCellEditingStyle)editingStyle
         forRowAtIndexPath:(NSIndexPath *)indexPath

### 新增待辦事項檢視控制器

1.  將名為 **QSTodoItemViewController** 的新 Objective-C 類別 (衍生自 **UIViewController**) 新增至您的專案：

    ![][2]

    ![][3]

2.  在 **QSTodoItemViewController.h** 中新增屬性，以存放要修改的項目：

        @property (nonatomic, weak) NSMutableDictionary *item;

3.  在 **QSTodoItemViewController.m** 中，為我們要編輯之待辦事項的兩個欄位新增兩個私人屬性 -- 完成狀態和待辦事項本身的文字：

        @interface QSTodoItemViewController ()

        @property (nonatomic, strong) IBOutlet UITextField *itemText;
        @property (nonatomic, strong) IBOutlet UISegmentedControl *itemComplete;

        @end

4.  在 **QSTodoItemViewController.m** 中，將 **viewDidLoad** 的虛設常式實作更新為下列程式碼：

        - (void)viewDidLoad
        {
            [super viewDidLoad];

            UINavigationItem *nav = [self navigationItem];
            [nav setTitle:@"Todo Item"];

            NSDictionary *theItem = [self item];
            [self.itemText setText:[theItem objectForKey:@"text"]];

            BOOL isComplete = [[theItem objectForKey:@"complete"] boolValue];
            [self.itemComplete setSelectedSegmentIndex:(isComplete ? 0 : 1)];

            [self.itemComplete addTarget:self
                                  action:@selector(completedValueChanged:)
                        forControlEvents:UIControlEventValueChanged];
        }

5.  在 **QSTodoItemViewController.m** 中，另外新增四個用來處理數個事件的方法：

        - (BOOL)textFieldShouldEndEditing:(UITextField *)textField {
            [textField resignFirstResponder];
            return YES;
        }

        - (BOOL)textFieldShouldReturn:(UITextField *)textField {
            [textField resignFirstResponder];
            return YES;
        }


        - (void)completedValueChanged:(id)sender {
            [[self view] endEditing:YES];
        }

        - (void)viewWillDisappear:(BOOL)animated {
            [self.item setValue:[self.itemText text] forKey:@"text"];
            [self.item setValue:[NSNumber numberWithBool:self.itemComplete.selectedSegmentIndex == 0] forKey:@"complete"];
        }

### 將待辦事項檢視控制器和 segue 新增至腳本

1.  使用 [專案導覽] 返回 **MainStoryboard\_iPhone.storyboard** 檔案。

2.  在腳本中現有的 [待辦事項清單檢視控制器] 右側，為待辦事項新增檢視控制器。將此檢視控制器的自訂類別設為 [QSTodoItemViewController]。若要深入了解，請參閱[將場景新增至腳本][將場景新增至腳本]。

3.  將 [待辦事項清單檢視控制器] 中的發送 segue 新增至 [待辦事項檢視控制器]，然後將 segue 命名為 **detailSegue**。若要深入了解，請參閱[在腳本中的場景之間新增 Segue][在腳本中的場景之間新增 Segue]。請不要從原始檢視控制器中的任何儲存格或按鈕建立此 segue。您應按住 CTRL，然後在腳本介面中，從 [待辦事項清單檢視控制器] 下的檢視控制器圖示拖曳至 [待辦事項檢視控制器]。如果您不慎從儲存格建立 segue，您將在執行應用程式時觸發 segue 兩次，而導致下列錯誤：

        Nested push animation can result in corrupted navigation bar

4.  將項目文字的文字欄位和完成狀態的分段控制項新增至新的 [待辦事項檢視控制器]，連同標籤在內。在分段控制項中，將 [區段 0] 的標題設為 [是]，並將 [區段 1] 的標題設為 [否]。將這些新欄位連接到程式碼中的出口。若要深入了解，請參閱[建置使用者介面][建置使用者介面]和[分段控制項][分段控制項]。

    ![][4]

5.  將這些新欄位連接到已新增至 **QSTodoItemViewController.m** 的對應出口。將項目文字欄位連接到 [itemText] 出口，並將完成狀態分段控制項連接到 [itemComplete] 出口。若要深入了解，請參閱[建立出口連線][建立出口連線]。

6.  將文字欄位的委派設為檢視控制器。如此，當您最後編輯項目並按 [返回] 時，將會捨棄文字欄位。按住 CTRL，然後在腳本介面中，從文字欄位拖曳至 [待辦事項檢視控制器] 下的檢視控制器圖示，然後選取委派出口；這會向腳本指出此文字欄位的委派就是這個檢視控制器。

7.  確認應用程式可在您目前所做的所有變更下運作。接著，在模擬器中執行應用程式。將項目新增至待辦事項清單，然後按一下這些項目。您將會看見項目檢視控制器 (目前是空的)。

    ![][5]

    ![][6]

### 將事項詳細資料新增至待辦事項檢視控制器

1.  我們將從 **QSTodoListViewController.m** 參考 **QSTodoItemViewController**。因此，我們要在 **QSTodoListViewController.m** 中新增一行，以匯入 **QSTodoItemViewController.h**。

        #import "QSTodoItemViewController.h"

2.  將兩個新屬性新增至 **QSTodoListViewController.m** 中的 **QSTodoListViewController** 介面，以儲存要編輯的項目：

        @property (nonatomic)           NSInteger       editedItemIndex;
        @property (strong, nonatomic)   NSMutableDictionary *editedItem;

3.  在 **QSTodoListViewController.m** 中實作 **tableView:didSelectRowAtIndexPath:**，以儲存所編輯的項目，然後呼叫 segue 以顯示詳細檢視。

          - (void)tableView:(UITableView *)tableView didSelectRowAtIndexPath:(NSIndexPath *)indexPath {
              self.editedItemIndex = [indexPath row];
              self.editedItem = [[self.todoService.items objectAtIndex:[indexPath row]] mutableCopy];

              [self performSegueWithIdentifier:@"detailSegue" sender:self];
          }

4.  在 **QSTodoListViewController.m** 中實作 **prepareForSegue:sender:**，以將項目傳至 [待辦事項檢視控制器]。

        - (void)prepareForSegue:(UIStoryboardSegue *)segue sender:(id)sender {
            if ([[segue identifier] isEqualToString:@"detailSegue"]) {
                QSTodoItemViewController *ivc = (QSTodoItemViewController *)[segue destinationViewController];
                ivc.item = self.editedItem;
            }
        }

5.  確認應用程式可在您目前所做的所有變更下運作。接著，在模擬器中執行應用程式。將項目新增至待辦事項清單，然後按一下這些項目。您會發現項目檢視控制器已不再是空的 -- 它會顯示待辦事項的詳細資料。

    ![][7]

### 新增儲存編輯內容的支援

1.  當您按一下導覽檢視中的 [上一頁] 按鈕時，編輯內容將會遺失。我們已將資料傳送至詳細檢視，但該資料並未傳回至主要檢視。由於我們已將指標傳至項目的複本，因此我們可以使用該指標來擷取對項目所做的更新清單，並在伺服器上加以更新。若要這麼做，請先在 **QSTodoService.m** 中更新 **QSTodoService** 的伺服器包裝函式類別，方法是移除 **completeItem** 作業，然後新增 **updateItem** 作業。這是因為 **completeItem** 只會將項目標示為完成，而 **updateItem** 則會更新項目。

        - (void)updateItem:(NSDictionary *)item atIndex:(NSInteger)index completion:(QSCompletionWithIndexBlock)completion {
            // Cast the public items property to the mutable type (it was created as mutable)
            NSMutableArray *mutableItems = (NSMutableArray *) items;

            // Replace the original in the items array
            [mutableItems replaceObjectAtIndex:index withObject:item];

            // Update the item in the TodoItem table and remove from the items array when we mark an item as complete
            [self.syncTable update:item completion:^(NSError *error) {
                [self logErrorIfNotNil:error];

                NSInteger index = -1;
                if (!error) {
                    BOOL isComplete = [[item objectForKey:@"complete"] boolValue];
                    NSString *remoteId = [item objectForKey:@"id"];
                    index = [items indexOfObjectPassingTest:^BOOL(id obj, NSUInteger idx, BOOL *stop) {
                        return [remoteId isEqualToString:[obj objectForKey:@"id"]];
                    }];

                    if (index != NSNotFound && isComplete)
                    {
                        [mutableItems removeObjectAtIndex:index];
                    }
                }

                // Let the caller know that we have finished
                dispatch_async(dispatch_get_main_queue(), ^{
                    completion(index);
                });
            }];
        }

2.  從 **QSTodoService.h** 中移除 **completeItem** 的宣告，然後為 **updateItem** 新增此宣告：

        - (void)updateItem:(NSDictionary *)item atIndex:(NSInteger)index completion:(QSCompletionWithIndexBlock)completion;

3.  在 **QSTodoListViewController.m** 中新增作業 **viewWillAppear**，以在每次從詳細資料檢視控制器返回後顯示主要檢視時呼叫更新方法。

        - (void)viewWillAppear:(BOOL)animated {
            if (self.editedItem && self.editedItemIndex >= 0) {
                // Returning from the details view controller
                NSDictionary *item = [self.todoService.items objectAtIndex:self.editedItemIndex];

                BOOL changed = ![item isEqualToDictionary:self.editedItem];
                if (changed) {
                    [self.tableView setUserInteractionEnabled:NO];

                    // Change the appearance to look greyed out until we remove the item
                    NSIndexPath *indexPath = [NSIndexPath indexPathForRow:self.editedItemIndex inSection:0];

                    UITableViewCell *cell = [self.tableView cellForRowAtIndexPath:indexPath];
                    cell.textLabel.textColor = [UIColor grayColor];

                    // Ask the todoService to update the item, and remove the row if it's been completed
                    [self.todoService updateItem:self.editedItem atIndex:self.editedItemIndex completion:^(NSUInteger index) {
                        if ([[self.editedItem objectForKey:@"complete"] boolValue]) {
                            // Remove the row from the UITableView
                            [self.tableView deleteRowsAtIndexPaths:@[ indexPath ]
                                                  withRowAnimation:UITableViewRowAnimationTop];
                        } else {
                            [self.tableView reloadRowsAtIndexPaths:[NSArray arrayWithObject:indexPath]
                                                  withRowAnimation:UITableViewRowAnimationAutomatic];
                        }

                        [self.tableView setUserInteractionEnabled:YES];

                        self.editedItem = nil;
                        self.editedItemIndex = -1;
                    }];
                } else {
                    self.editedItem = nil;
                    self.editedItemIndex = -1;
                }
            }
        }

4.  現在，我們要測試應用程式。確認應用程式可在您目前所做的所有變更下運作。接著，在模擬器中執行應用程式。將項目新增至待辦事項清單，然後按一下這些項目。請嘗試編輯某個項目，然後返回。確認項目說明已在應用程式的主要檢視中更新。使用向下拖曳動作重新整理應用程式，然後驗證編輯內容已反映在雲端中。

### 衝突處理問題

1.  我們將查看在兩個不同的用戶端同時嘗試修改同一項資料時，會有什麼狀況。在下方的範例清單中，有一個名為 "Hello world 3" 的項目。我們要在一個裝置上將其變更為 "Hello world 13"，並在另一個裝置上將其變更為 "Hello world 23"。

    ![][8]

2.  在兩個位置上啟動應用程式：在兩個 iOS 裝置上，或在模擬器中和 iOS 裝置上。如果您沒有實體裝置可供測試，請在模擬器中啟動一個執行個體，並使用 REST 用戶端，將 PATCH 要求傳送至行動服務。PATCH 要求的 URL 會反映行動服務的名稱、待辦事項資料表的名稱，以及您所編輯的待辦事項資料表的識別碼，而 x-zumo-application 標頭則是應用程式金鑰：

        PATCH https://todolist.azure-mobile.net/tables/todoitem/D265929E-B17B-42D1-8FAB-D0ADF26486FA?__systemproperties=__version
        Content-Type: application/json
        x-zumo-application: shYOoDFdKhmzLEbnMQqPYrCLhwGOVA10

        {
            "id": "D265929E-B17B-42D1-8FAB-D0ADF26486FA",
            "text": "Hello world 23"
        }

3.  現在，請在應用程式的兩個執行個體中重新整理項目。您會看見 Xcode 的輸出記錄中列出錯誤：

        TodoList[1575:4a2f] ERROR Error Domain=com.Microsoft.WindowsAzureMobileServices.ErrorDomain Code=-1170 "Not all operations completed successfully" UserInfo=0x8dd6310 {com.Microsoft.WindowsAzureMobileServices.ErrorPushResultKey=(
            "The server's version did not match the passed version"
        ), NSLocalizedDescription=Not all operations completed successfully}

這是因為在 completion 區塊上對 **pullWithQuery:completion:** 的呼叫中，錯誤參數將會是 non-nil，而這會導致錯誤透過 **NSLog** 在輸出中列出。

### 更新 QSTodoService 以支援衝突處理

1.  我們要讓使用者在用戶端中處理衝突，而讓他們決定衝突的處理方式。為此，我們將實作 **MSSyncContextDelegate** 通訊協定。在 **QSTodoService.h** 和 **QSTodoService.m** 中，將 \*\*(QSTodoService \*)defaultService;\*\* Factory 方法宣告變更為下列陳述式，以接收作為參數的同步內容委派：

        + (QSTodoService *)defaultServiceWithDelegate:(id)delegate;

2.  在 **QSTodoService.m** 中，依照下列方式變更 **init** 行，而再次接收作為參數的同步內容委派：

        -(QSTodoService *)initWithDelegate:(id)syncDelegate

3.  在 **QSTodoService.m** 中，將 **defaultServiceWithDelegate** 中的 **init** 呼叫變更為 **initWithDelegate**：

        service = [[QSTodoService alloc] initWithDelegate:delegate];

4.  回到 **QSTodoService.m**，並變更 **self.client.syncContext** 的初始化，而為委派傳入 **syncDelegate**，而非 **nil**：

        self.client.syncContext = [[MSSyncContext alloc] initWithDelegate:syncDelegate dataSource:store callback:nil];

### 新增 UI 警示檢視協助程式以支援衝突處理

1.  如果有衝突存在，我們要讓使用者選擇所要保留的版本：

-   保留用戶端版本 (這會覆寫伺服器上的版本)、
-   保留伺服器版本 (這會更新用戶端本機資料表)，或
-   兩個版本都不保留 (取消發送並擱置作業)。

由於在我們顯示提示時可能也會有其他更新發生，因此我們將持續顯示選項，直到伺服器停止傳回失敗回應為止。在我們的程式碼中，我們將使用協助程式類別，以顯示警示檢視，並取用在警示檢視顯示時叫用的委派。首先，我們將定義協助程式類別 **QSUIAlertViewWithBlock**。

1.  使用 Xcode 新增這個新類別 **QSUIAlertViewWithBlock**，然後使用下列內容覆寫 **QSUIAlertViewWithBlock.h**：

        #import <Foundation/Foundation.h>

        typedef void (^QSUIAlertViewBlock) (NSInteger index);

        @interface QSUIAlertViewWithBlock : NSObject <UIAlertViewDelegate>

        - (id) initWithCallback:(QSUIAlertViewBlock)callback;
        - (void) showAlertWithTitle:(NSString *)title message:(NSString *)message cancelButtonTitle:(NSString *)cancelButtonTitle otherButtonTitles:(NSArray *)otherButtonTitles;

        @end

2.  接著，使用下列檔案覆寫 **QSUIAlertViewWithBlock.m**：

        #import "QSUIAlertViewWithBlock.h"
        #import <objc/runtime.h>

        @interface QSUIAlertViewWithBlock()

        @property (nonatomic, copy) QSUIAlertViewBlock callback;

        @end

        @implementation QSUIAlertViewWithBlock

        static const char *key;

        @synthesize callback = _callback;

        - (id) initWithCallback:(QSUIAlertViewBlock)callback
        {
            self = [super init];
            if (self) {
                _callback = [callback copy];
            }
            return self;
        }

        - (void) showAlertWithTitle:(NSString *)title message:(NSString *)message cancelButtonTitle:(NSString *)cancelButtonTitle otherButtonTitles:(NSArray *)otherButtonTitles {
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:title
                                                            message:message
                                                           delegate:self
                                                  cancelButtonTitle:cancelButtonTitle
                                                  otherButtonTitles:nil];

            if (otherButtonTitles) {
                for (NSString *buttonTitle in otherButtonTitles) {
                    [alert addButtonWithTitle:buttonTitle];
                }
            }

            [alert show];

            objc_setAssociatedObject(alert, &key, self, OBJC_ASSOCIATION_RETAIN_NONATOMIC);
        }

        - (void) alertView:(UIAlertView *)alertView didDismissWithButtonIndex:(NSInteger)buttonIndex
        {
            if (self.callback) {
                self.callback(buttonIndex);
            }
        }

        @end

### 將衝突處理常式新增至待辦事項清單檢視控制器

1.  在 **QSTodoListViewController.m** 中，將 **viewDidLoad** 中對 **defaultService** 的呼叫取代為對 **defaultServiceWithDelegate** 的呼叫，如下所示：

        self.todoService = [QSTodoService defaultServiceWithDelegate:self];

2.  在 **QSTodoListViewController.h** 中，將 **\<MSSyncContextDelegate\>** 新增至介面宣告，以實作 **MSSyncContextDelegate** 通訊協定。

        @interface QSTodoListViewController : UITableViewController<MSSyncContextDelegate>

3.  將下列匯入陳述式新增至 **QSTodoListViewController.m** 頂端：

        #import "QSUIAlertViewWithBlock.h"

4.  最後，我們要將下列兩項作業新增至 **QSTodoListViewController.m**，以使用此協助程式類別，並提示使用者以三種方式之一來協調衝突。

        - (void)tableOperation:(MSTableOperation *)operation onComplete:(MSSyncItemBlock)completion
        {
            [self doOperation:operation complete:completion];
        }

        -(void)doOperation:(MSTableOperation *)operation complete:(MSSyncItemBlock)completion
        {
            [operation executeWithCompletion:^(NSDictionary *item, NSError *error) {

                NSDictionary *serverItem = [error.userInfo objectForKey:MSErrorServerItemKey];

                if (error.code == MSErrorPreconditionFailed) {
                    QSUIAlertViewWithBlock *alert = [[QSUIAlertViewWithBlock alloc] initWithCallback:^(NSInteger buttonIndex) {
                        if (buttonIndex == 1) { // Client
                            NSMutableDictionary *adjustedItem = [operation.item mutableCopy];

                            [adjustedItem setValue:[serverItem objectForKey:MSSystemColumnVersion] forKey:MSSystemColumnVersion];
                            operation.item = adjustedItem;

                            [self doOperation:operation complete:completion];
                            return;

                        } else if (buttonIndex == 2) { // Server
                            NSDictionary *serverItem = [error.userInfo objectForKey:MSErrorServerItemKey];
                            completion(serverItem, nil);
                        } else { // Cancel
                            [operation cancelPush];
                            completion(nil, error);
                        }
                    }];

                    NSString *message = [NSString stringWithFormat:@"Client value: %@\nServer value: %@", operation.item[@"text"], serverItem[@"text"]];

                    [alert showAlertWithTitle:@"Server Conflict"
                                      message:message
                            cancelButtonTitle:@"Cancel"
                            otherButtonTitles:[NSArray arrayWithObjects:@"Use Client", @"Use Server", nil]];
                } else {
                    completion(item, error);
                }
            }];
        }

### 測試應用程式

我們要對含有衝突的應用程式進行測試。請在兩個同時執行的不同應用程式執行個體中，編輯相同的項目。現在，請從頂端拖曳，以在應用程式執行個體中執行重新整理動作。您會看見立即協調變更的提示。

### 摘要

為了設定在[開始使用離線資料][開始使用離線資料]中完成的專案以偵測衝突，首先您新增了編輯和更新待辦事項的功能。

為此，您新增了新的項目詳細資料檢視控制器、連接了主要檢視控制器和詳細資料檢視控制器，最後新增了儲存編輯內容並將其發送至雲端的功能。

接著，您了解了發生衝突的狀況。您實作 **MSSyncContextDelegate** 通訊協定，而新增了衝突處理常式的支援。您也透過 **QSTodoService**、**QSTodoListViewController** 的伺服器介面類別和支援的類別，啟用了同步內容委派的支援。

您新增了可對使用者顯示警示的 **QSUIAlertViewWithBlock** 協助程式類別，最後將程式碼新增至 **QSTodoListViewController**，以提示使用者透過三種方式之一來協調衝突。

<!-- URLs. -->

  [開始使用離線資料]: /zh-tw/documentation/articles/mobile-services-ios-get-started-offline-data/
  [Azure 免費試用]: http://www.windowsazure.com/zh-tw/pricing/free-trial/?WT.mc_id=AE564AB28
  [0]: ./media/mobile-services-ios-handling-conflicts-offline-data/update-todo-list-view-controller-1.png
  [1]: ./media/mobile-services-ios-handling-conflicts-offline-data/update-todo-list-view-controller-2.png
  [2]: ./media/mobile-services-ios-handling-conflicts-offline-data/add-todo-item-view-controller-1.png
  [3]: ./media/mobile-services-ios-handling-conflicts-offline-data/add-todo-item-view-controller-2.png
  [將場景新增至腳本]: https://developer.apple.com/library/ios/recipes/xcode_help-IB_storyboard/chapters/StoryboardScene.html
  [在腳本中的場景之間新增 Segue]: https://developer.apple.com/library/ios/recipes/xcode_help-IB_storyboard/chapters/StoryboardSegue.html#//apple_ref/doc/uid/TP40014225-CH25-SW1
  [建置使用者介面]: https://developer.apple.com/library/mac/documentation/ToolsLanguages/Conceptual/Xcode_Overview/Edit_User_Interfaces/edit_user_interface.html
  [分段控制項]: https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/UIKitUICatalog/UISegmentedControl.html
  [4]: ./media/mobile-services-ios-handling-conflicts-offline-data/add-todo-item-view-controller-3.png
  [建立出口連線]: https://developer.apple.com/library/mac/recipes/xcode_help-interface_builder/articles-connections_bindings/CreatingOutlet.html
  [5]: ./media/mobile-services-ios-handling-conflicts-offline-data/add-todo-item-view-controller-4.png
  [6]: ./media/mobile-services-ios-handling-conflicts-offline-data/add-todo-item-view-controller-5.png
  [7]: ./media/mobile-services-ios-handling-conflicts-offline-data/add-todo-item-view-controller-6.png
  [8]: ./media/mobile-services-ios-handling-conflicts-offline-data/conflict-handling-problem-1.png
