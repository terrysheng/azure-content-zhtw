先前範例所示範的標準登入，在每次應用程式啟動時，皆需要用戶端連絡身分識別提供者和行動服務。這個方法不能調整且效率不佳。

更好的方法就是快取 Azure Mobile Services 傳回的授權權杖，然後嘗試在使用提供者形式登入前先使用此方法。

1. 建議在 iOS 用戶端上用來加密和儲存驗證權杖的方法是使用 iOS Keychain。我們將使用 [SSKeychain](https://github.com/soffes/sskeychain) -- iOS Keychain 的一種簡單的包裝函式。依照 [SSKeychain] 頁面上的指示，將該包裝函式加入至您的專案。確認已在專案的 [建置設定] ([Apple LLVM - 語言 - 模組] 區段) 中啟用 [啟用模組] 設定。

2. 開啟 **QSTodoListViewController.m**，並加入下列程式碼：


		- (void) saveAuthInfo {
				[SSKeychain setPassword:self.todoService.client.currentUser.mobileServiceAuthenticationToken forService:@"AzureMobileServiceTutorial" account:self.todoService.client.currentUser.userId]
		}


		- (void)loadAuthInfo {
				NSString *userid = [[SSKeychain accountsForService:@"AzureMobileServiceTutorial"][0] valueForKey:@"acct"];
		    if (userid) {
		        NSLog(@"userid:%@", userid);
		        self.todoService.client.currentUser = [[MSUser alloc] initWithUserId:userid];
		         self.todoService.client.currentUser.mobileServiceAuthenticationToken = [SSKeychain passwordForService:@"AzureMobileServiceTutorial" account:userid];

		    }
		}

3. 在 **QSTodoListViewController.m** 中的 **viewDidAppear** 方法結尾，於 `[self refresh]` 這一行之前，加入 **saveAuthInfo** 的呼叫。透過這個呼叫，我們只能儲存使用者識別碼和 token 屬性：

				[self saveAuthInfo];

4. 讓我們也在應用程式啟動時載入使用者識別碼和 token。在 **QSTodoListViewController.m** 的 **viewDidLoad** 方法中，在將 **self.todoService** 初始化之後，加入 loadAuthInfo 的呼叫。

				[self loadAuthInfo];

<!--HONumber=49-->