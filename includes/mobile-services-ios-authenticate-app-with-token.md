
先前範例所示範的標準登入，在每次應用程式啟動時，皆需要用戶端連絡身分識別提供者和行動服務。這個方法不只效率不彰，而且如果同時有許多用戶試圖啟用您的應用程式時，還可能遇到使用量相關的問題。更好的方法就是快取行動服務傳回的驗證權杖，然後嘗試在使用提供者形式登入前先使用此方法。


>[WACOM.NOTE] 無論您使用用戶端管理或服務管理驗證，皆可以快取行動服務發行的權杖。本教學課程使用服務管理驗證。

1. 建議在 iOS 用戶端上用來加密和儲存驗證權杖的方法是使用 Keychain。若要這樣做，請建立類別 KeychainWrapper，從 [LensRocket 範例](https://github.com/WindowsAzure-Samples/iOS-LensRocket/blob/master/source/client/LensRocket/Misc/KeychainWrapper.m)複製 [KeychainWrapper.m](https://github.com/WindowsAzure-Samples/iOS-LensRocket/blob/master/source/client/LensRocket/Misc/KeychainWrapper.h) 和 [KeychainWrapper.h](https://github.com/WindowsAzure-Samples/iOS-LensRocket)。我們使用這個 KeychainWrapper 作為 Apple 文件中定義的 KeychainWrapper，並不會說明自動參考計數 (ARC)。


2. 開啟專案檔 **QSTodoListViewController.m**，然後新增下列程式碼：

		
		- (void) saveAuthInfo{
		    [KeychainWrapper createKeychainValue:self.todoService.client.currentUser.userId
				 forIdentifier:@"userid"];
		    [KeychainWrapper createKeychainValue:self.todoService.client.currentUser.mobileServiceAuthenticationToken
				 forIdentifier:@"token"];
		}
		
		
		- (void)loadAuthInfo {
		    NSString *userid = [KeychainWrapper keychainStringFromMatchingIdentifier:@"userid"];
		    if (userid) {
		        NSLog(@"userid: %@", userid);
		        self.todoService.client.currentUser = [[MSUser alloc] initWithUserId:userid];
		        self.todoService.client.currentUser.mobileServiceAuthenticationToken = [KeychainWrapper keychainStringFromMatchingIdentifier:@"token"];
		    }
		}
		


3. 在 **QSTodoListViewController.m** 中的 **viewDidAppear** 方法結尾，新增 saveAuthInfo 的呼叫。透過這個呼叫，我們只需儲存 userId 和 token 屬性。  



		- (void)viewDidAppear:(BOOL)animated
		{
		    MSClient *client = self.todoService.client;
		
		    if (client.currentUser != nil) {
		        return;
		    }
		
		    [client loginWithProvider:@"facebook" controller:self animated:YES completion:^(MSUser *user, NSError *error) {
		
		        [self saveAuthInfo];
		        [self refresh];
		    }];
		}

  
4. 現在，我們已經了解如何快取使用者權杖和識別碼，讓我們來看看如何在應用程式啟動時加以載入。在 **QSTodoListViewController.m** 的 **viewDidLoad** 方法中，在將 **self.todoService** 初始化之後，新增 loadAuthInfo 的呼叫。 
		
		- (void)viewDidLoad
		{
		    [super viewDidLoad];
		    
		    // Create the todoService - this creates the Mobile Service client inside the wrapped service
		    self.todoService = [QSTodoService defaultService];

			[self loadAuthInfo];
		    
		    // Set the busy method
		    UIActivityIndicatorView *indicator = self.activityIndicator;
		    self.todoService.busyUpdate = ^(BOOL busy)
		    {
		        if (busy)
		        {
		            [indicator startAnimating];
		        } else
		        {
		            [indicator stopAnimating];
		        }
		    };
		    
		    // have refresh control reload all data from server
		    [self.refreshControl addTarget:self
		                            action:@selector(onRefresh:)
		                  forControlEvents:UIControlEventValueChanged];
		
		    // load the data
		    [self refresh];
		}

5. 如果應用程式向您應通過的行動服務提出要求 (因為使用者已通過驗證)，但您收到 401 回應 (未經授權的錯誤)，則表示您所忽略的使用者權杖已經到期。在適用於可用來與行動服務互動的每個方法的完成處理常式中，我們應該檢查 401 回應，或者可在某一個位置處理一些事項：MSFilter 的 handleRequest 方法。如需查看處理這個案例的方式，請參閱[這個部落格文章](http://www.thejoyofcode.com/Handling_expired_tokens_in_your_application_Day_11_.aspx)

