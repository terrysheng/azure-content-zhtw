**Objective-C**：

1. 在 Mac 上，開啟 Xcode 中的 _QSTodoListViewController.m_，並加入下列方法。如果您不使用 Google 作為識別提供者，請將 _google_ 變更為 _microsoftaccount_、_twitter_、_facebook_ 或 _windowsazureactivedirectory_。如果您使用 Facebook，[您將需要在應用程式中將 Facebook 網域列入白名單](https://developers.facebook.com/docs/ios/ios9#whitelist)。

            - (void) loginAndGetData
            {
                MSClient *client = self.todoService.client;
                if (client.currentUser != nil) {
                    return;
                }
            
                [client loginWithProvider:@"google" controller:self animated:YES completion:^(MSUser *user, NSError *error) {
                    [self refresh];
                }];
            }


2. 使用以下項目取代 _QSTodoListViewController.m_ 中 `viewDidLoad` 中的 `[self refresh]`：

            [self loginAndGetData];

3. 按下 [_執行_] 以啟動應用程式，然後登入。當您登入時，應該能夠檢視待辦事項清單並進行更新。

**Swift**：

1. 在 Mac 上，開啟 Xcode 中的 _ToDoTableViewController.swift_，並加入下列方法。如果您不使用 Google 作為識別提供者，請將 _google_ 變更為 _microsoftaccount_、_twitter_、_facebook_ 或 _windowsazureactivedirectory_。如果您使用 Facebook，[您將需要在應用程式中將 Facebook 網域列入白名單](https://developers.facebook.com/docs/ios/ios9#whitelist)。
        
            func loginAndGetData() {
                
                guard let client = self.table?.client where client.currentUser != nil else {
                    return
                }
                
                client.loginWithProvider("google", controller: self, animated: true) { (user, error) in
                    self.refreshControl?.beginRefreshing()
                    self.onRefresh(self.refreshControl)
                }
            }


2. 移除 _ToDoTableViewController.swift_ 中 `viewDidLoad()` 結尾處的 `self.refreshControl?.beginRefreshing()` 和 `self.onRefresh(self.refreshControl)` 行。在其位置新增呼叫至 `loginAndGetData()`：

            loginAndGetData()

3. 按下 [_執行_] 以啟動應用程式，然後登入。當您登入時，應該能夠檢視待辦事項清單並進行更新。

<!---HONumber=AcomDC_0204_2016-->