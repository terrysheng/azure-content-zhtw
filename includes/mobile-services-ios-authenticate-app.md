1.  開啟專案檔案 QSTodoListViewController.m，並在 **viewDidLoad** 方法中，移除會將資料重新載入至資料表的下列程式碼：

        [self refresh];

2.  在 **viewDidLoad** 方法的正後方新增下列程式碼：

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

    <div class="dev-callout"><b>注意</b>
<p>如果您使用的身分識別提供者不是 Facebook，請將傳給上述 <strong>loginWithProvider</strong> 的值變更為下列其中一個：<em>microsoftaccount</em>、<em>facebook</em>、<em>twitter</em>、<em>google</em> 或 <em>windowsazureactivedirectory</em>。</p>
</div>

3.  按 [執行] 按鈕以建置專案並在 iPhone 模擬器中啟動應用程式，然後使用您選擇的身分識別提供者登入。

    成功登入後，應用程式應會正確無誤地執行，而且您應能夠查詢行動服務並更新資料。


