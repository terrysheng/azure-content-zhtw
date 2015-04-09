1. 開啟 **QSTodoListViewController.m**，並在 **viewDidLoad** 方法中，移除以下一行：

        [self refresh];

2.	在 **viewDidLoad** 方法的正後方新增下列程式碼：  

        - (void)viewDidAppear:(BOOL)animated
        {
            MSClient *client = self.todoService.client;

            if (client.currentUser != nil) {
                return;
            }

            [client loginWithProvider:@"facebook" controller:self animated:YES completion:^(MSUser *user, NSError *error) {
                [self refresh];
            }];
        }

    > [AZURE.NOTE] 如果您使用的身分識別提供者不是 Facebook，請變更傳遞給 **loginWithProvider** 的值。支援的值包括：_microsoftaccount_、_facebook_、_twitter_、_google_ 或 _windowsazureactivedirectory_。

3. 按下 [執行] 以啟動應用程式，然後以您選擇的身分識別提供者登入。當您登入時，應該能夠檢視待辦清單並進行更新。

<!--HONumber=49-->