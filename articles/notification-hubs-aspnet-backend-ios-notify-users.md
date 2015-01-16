<properties title="Azure Notification Hubs Notify Users" pageTitle="Azure 通知中心通知使用者" metaKeywords="Azure push notifications, Azure notification hubs" description="了解如何在 Azure 中傳送安全的推播通知。程式碼範例是以 Objective-C 撰寫並使用 .NET API。" documentationCenter="Mobile" metaCanonical="" disqusComments="1" umbracoNaviHide="0" authors="yuaxu" manager="dwrede" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="11/22/2014" ms.author="yuaxu" />

#Azure 通知中心通知使用者

<div class="dev-center-tutorial-selector sublanding">
    	<a href="/zh-tw/documentation/articles/notification-hubs-aspnet-backend-windows-dotnet-notify-users/" title="Windows Universal">Windows Universal</a><a href="/zh-tw/documentation/articles/notification-hubs-/" title="iOS" class="current">iOS</a>
		<a href="/zh-tw/documentation/articles/notification-hubs-aspnet-backend-android-notify-users/" title="Android">Android</a>
</div>

Azure 中的推播通知支援可讓您存取易於使用、多重平台的大規模推播基礎結構，而大幅簡化消費者和企業應用程式在行動平台上的推播通知實作。本教學課程將示範如何使用 Azure 通知中心，來將推播通知傳送到特定裝置上的特定應用程式使用者。ASP.NET WebAPI 後端可用來驗證用戶端並產生通知，如指引主題[從您的應用程式後端註冊]中所示(http://msdn.microsoft.com/zh-tw/library/dn743807.aspx)。

> [AZURE.NOTE] 本教學課程假設您已建立並設定通知中心，如[開始使用通知中心 (iOS)] 中所述(http://azure.microsoft.com/zh-tw/documentation/articles/notification-hubs-ios-get-started/)。本教學課程亦是[安全推播 (iOS)] 教學課程的必要條件(http://azure.microsoft.com/zh-tw/documentation/articles/notification-hubs-aspnet-backend-ios-secure-push/) 。
> 如果您正在將行動服務做為後端服務，請參閱本教學課程的[行動服務版本](/zh-tw/documentation/articles/mobile-services-javascript-backend-ios-push-notifications-app-users/) 。


## 建立與設定通知中心

請執行[開始使用通知中心 (iOS)] 中的第 1 節到第 5 節(http://azure.microsoft.com/zh-tw/documentation/articles/notification-hubs-ios-get-started/)。如需 iOS 裝置佈建的其他資源，請參閱 [Big Nerd Ranch] 上的指南(http://www.bignerdranch.com/we-teach/how-to-prepare/ios-device-provisioning.html)。

[WACOM.INCLUDE [notification-hubs-aspnet-backend-notifyusers](../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## 修改您的 iOS 應用程式

1. 開啟您依照[開始使用通知中心 (iOS)] 中第 1 節到第 5 節所建立的單一頁面檢視應用程式(http://azure.microsoft.com/zh-tw/documentation/articles/notification-hubs-ios-get-started/)。

> [AZURE.NOTE] 在本節中，我們假設您已使用空白組織名稱設定您的專案。否則，您需要針對所有類別名稱預先考量您的組織名稱。

2. 在您的 Main.storyboard 中，新增下列物件程式庫元件：
	+ 包含預留位置文字 **Username** 的 UITextField
	+ 包含預留位置文字 **Password** 的 UITextField，並在 [Attribute Inspector] 的 [Textfield Return Key] 下核取 [**Secure**] 選項
	+ 標示為 **1 的 UIButton。[登入**]，然後取消核取 [Attributes Inspector] > [控制] > [內容] 中的 [**啟用**] 選項
	+ 標示為 **2 的 UIButton。[傳送推播**]，再取消核取 [**啟用**] 選項

	您的腳本應如下所示：

    ![][IOS1]

3. 在 ViewController.h 的介面部分中，建立 UITextFields 和 UIButtons 的出口

	    @property (weak, nonatomic) IBOutlet UITextField *UsernameField;
		@property (weak, nonatomic) IBOutlet UITextField *PasswordField;
		@property (weak, nonatomic) IBOutlet UIButton *LogInButton;
		@property (weak, nonatomic) IBOutlet UIButton *SendPushButton;

		- (IBAction)LogInAction:(id)sender;
		- (IBAction)SendPushAction:(id)sender;

4. 首先，我們將建立可與後端整合的 RegisterClient 類別。建立名為 RegisterClient (繼承自 NSObject) 的 Objective-C 類別。然後在 RegisterClient.h 介面區段中新增下列程式碼：

		@property (strong, nonatomic) NSString* authenticationHeader;
		-(void) registerWithDeviceToken:(NSData*) token tags:(NSSet*) tags andCompletion: (void(^)(NSError*)) completion;

5. 在 RegisterClient.m 中新增下列介面區段：

		@interface RegisterClient ()

		@property (strong, nonatomic) NSURLSession* session;
		-(void) tryToRegisterWithDeviceToken:(NSData*) token tags:(NSSet*) tags retry: (BOOL) retry andCompletion: (void(^)(NSError*)) completion;
		-(void) retrieveOrRequestRegistrationIdWithDeviceToken: (NSString*) token completion: (void(^)(NSString*, NSError*)) completion;
		-(void) upsertRegistrationWithRegistrationId: (NSString*) registrationId deviceToken: (NSString*) token tags: (NSSet*)tags andCompletion: (void(^)(NSURLResponse*, NSError*)) completion;

		@end

6. 在 RegisterClient.m 實作區段中新增下列程式碼，並將 *{backend endpoint}* 預留位置替換成上一節中用來部署應用程式後端的目的地 URL。

		NSString *const RegistrationIdLocalStorageKey = @"RegistrationId";
		NSString *const BackEndEndpoint = @"{backend endpoint}/api/register";

		- (instancetype)init
		{
		    self = [super init];
		    if (self) {
		        NSURLSessionConfiguration* config = [NSURLSessionConfiguration defaultSessionConfiguration];
		        _session = [NSURLSession sessionWithConfiguration:config delegate:nil delegateQueue:nil];
		    }
		    return self;
		}

		-(void) registerWithDeviceToken:(NSData*) token tags:(NSSet*) tags andCompletion: (void(^)(NSError*)) completion;
		{
		    [self tryToRegisterWithDeviceToken:token tags:tags retry:YES andCompletion:completion];
		}

		-(void) tryToRegisterWithDeviceToken:(NSData*) token tags:(NSSet*) tags retry: (BOOL) retry andCompletion: (void(^)(NSError*)) completion;
		{
		    NSSet* tagsSet = tags?tags:[[NSSet alloc] init];

		    NSString *deviceTokenString = [[token description] stringByTrimmingCharactersInSet:[NSCharacterSet characterSetWithCharactersInString:@"<>"]];
		    deviceTokenString = [[deviceTokenString stringByReplacingOccurrencesOfString:@" " withString:@""] uppercaseString];

		    [self retrieveOrRequestRegistrationIdWithDeviceToken: deviceTokenString completion:^(NSString* registrationId, NSError *error) {
		        NSLog(@"regId: %@", registrationId);
		        if (error) {
		            completion(error);
		            return;
		        }

		        [self upsertRegistrationWithRegistrationId:registrationId deviceToken:deviceTokenString tags:tagsSet andCompletion:^(NSURLResponse * response, NSError *error) {
		            if (error) {
		                completion(error);
		                return;
		            }

		            NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*)response;
		            if (httpResponse.statusCode == 200) {
		                completion(nil);
		            } else if (httpResponse.statusCode == 410 && retry) {
		                [self tryToRegisterWithDeviceToken:token tags:tags retry:NO andCompletion:completion];
		            } else {
		                NSLog(@"Registration error with response status: %ld", (long) httpResponse.statusCode);

		                completion([NSError errorWithDomain:@"Registration" code:httpResponse.statusCode userInfo:nil]);
		            }

		        }];
		    }];
		}

		-(void) upsertRegistrationWithRegistrationId: (NSString*) registrationId deviceToken: (NSData*) token tags: (NSSet*)tags andCompletion: (void(^)(NSURLResponse*, NSError*)) completion;
		{
		    NSDictionary* deviceRegistration = @{@"Platform" : @"apns", @"Handle": token, @"Tags": [tags allObjects]};
		    NSData* jsonData = [NSJSONSerialization dataWithJSONObject:deviceRegistration options:NSJSONWritingPrettyPrinted error:nil];

		    NSLog(@"JSON registration: %@", [[NSString alloc] initWithData:jsonData encoding:NSUTF8StringEncoding]);

		    NSString* endpoint = [NSString stringWithFormat:@"%@/%@", BackEndEndpoint, registrationId];
		    NSURL* requestURL = [NSURL URLWithString:endpoint];
		    NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
		    [request setHTTPMethod:@"PUT"];
		    [request setHTTPBody:jsonData];
		    NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@", self.authenticationHeader];
		    [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];
		    [request setValue:@"application/json" forHTTPHeaderField:@"Content-Type"];

		    NSURLSessionDataTask* dataTask = [self.session dataTaskWithRequest:request completionHandler:^(NSData *data, NSURLResponse *response, NSError *error) {
		        if (!error)
		        {
		            completion(response, error);
		        }
		        else
		        {
		            NSLog(@"Error request: %@", error);
		            completion(nil, error);
		        }
		    }];
		    [dataTask resume];
		}

		-(void) retrieveOrRequestRegistrationIdWithDeviceToken: (NSString*) token completion: (void(^)(NSString*, NSError*)) completion;
		{
		    NSString* registrationId = [[NSUserDefaults standardUserDefaults] objectForKey:RegistrationIdLocalStorageKey];

		    if (registrationId)
		    {
		        completion(registrationId, nil);
		        return;
		    }

		    // request new one & save
		    NSURL* requestURL = [NSURL URLWithString:[NSString stringWithFormat:@"%@?handle=%@", BackEndEndpoint, token]];
		    NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
		    [request setHTTPMethod:@"POST"];
		    NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@", self.authenticationHeader];
		    [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

		    NSURLSessionDataTask* dataTask = [self.session dataTaskWithRequest:request completionHandler:^(NSData *data, NSURLResponse *response, NSError *error) {
		        NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
		        if (!error && httpResponse.statusCode == 200)
		        {
		            NSString* registrationId = [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding];

		            // remove quotes
		            registrationId = [registrationId substringWithRange: NSMakeRange(1, [registrationId length]-2)];

		            [[NSUserDefaults standardUserDefaults] setObject:registrationId forKey:RegistrationIdLocalStorageKey];
		            [[NSUserDefaults standardUserDefaults] synchronize];

		            completion(registrationId, nil);
		        }
		        else
		        {
		            NSLog(@"Error status: %ld, request: %@", (long)httpResponse.statusCode, error);
		            if (error)
		                completion(nil, error);
		            else {
		                completion(nil, [NSError errorWithDomain:@"Registration" code:httpResponse.statusCode userInfo:nil]);
		            }
		        }
		    }];
		    [dataTask resume];
		}

	上述程式碼會實作[從您的應用程式後端註冊]指引文章所說明的邏輯，(http://msdn.microsoft.com/zh-tw/library/dn743807.aspx) 方法是使用 NSURLSession 針對您的應用程式後端執行 REST 呼叫，並使用 NSUserDefaults 來本機儲存通知中心所傳回的 registrationId。

	請注意，此類別需要設定 **authorizationHeader** 屬性，才能正常運作。您可以在登入後，透過 **ViewController** 類別設定此屬性。

7. 在 ViewController.h 中，將裝置權杖和參考的下列宣告新增至 RegisterClient 執行個體：

		@property (strong, nonatomic) NSData* deviceToken;
		@property (strong, nonatomic) RegisterClient* registerClient;

8. 在 ViewController.m 中，將 ViewController 類別製作成 UITextFieldDelegate。接著新增私人方法宣告：

		@interface ViewController () <UITextFieldDelegate>

		// create the Authorization header to perform Basic authentication with your app back-end
		-(void) createAndSetAuthenticationHeaderWithUsername: (NSString*) username AndPassword: (NSString*) password;

		@end

> [AZURE.NOTE] 下列片段不是安全驗證結構描述，您應將 **createAndSetAuthenticationHeaderWithUsername:AndPassword:** 的實作替代成特定的驗證機制，使該機制產生註冊用戶端類別所利用的驗證權杖，例如 OAuth、Active Directory。

9. 接著，在 ViewController.m 的實作區段中新增下列程式碼：

		-(void) setDeviceToken: (NSData*) deviceToken
		{
		    _deviceToken = deviceToken;
		    self.LogInButton.enabled = YES;
		}

		-(void) createAndSetAuthenticationHeaderWithUsername: (NSString*) username AndPassword: (NSString*) password;
		{
		    NSString* headerValue = [NSString stringWithFormat:@"%@:%@", username, password];

		    NSData* encodedData = [[headerValue dataUsingEncoding:NSUTF8StringEncoding] base64EncodedDataWithOptions:NSDataBase64EncodingEndLineWithCarriageReturn];

		    self.registerClient.authenticationHeader = [[NSString alloc] initWithData:encodedData encoding:NSUTF8StringEncoding];
		}

		-(BOOL)textFieldShouldReturn:(UITextField *)textField
		{
		    [textField resignFirstResponder];
		    return YES;
		}

	請留意設定裝置權杖如何啟用登入按鈕。這是因為，做為登入動作的一部分，檢視控制器會向應用程式後端註冊推播通知。因此，在適當設定裝置權杖之前，我們不希望其他人存取「登入」動作。只要登入在推播註冊之前發生，您可能就會想要將前者與後者分開。

10. 在 ViewController.m 中，新增後端端點的常數，並使用下列程式碼片段來實作 UIButtons 的動作方法。以您為後端使用的目的地 URL 來取代預留位置後端端點。

		- (IBAction)LogInAction:(id)sender {
		    // create authentication header and set it in register client
		    NSString* username = self.UsernameField.text;
		    NSString* password = self.PasswordField.text;

		    [self createAndSetAuthenticationHeaderWithUsername:username AndPassword:password];

		    __weak ViewController* selfie = self;
		    [self.registerClient registerWithDeviceToken:self.deviceToken tags:nil andCompletion: ^(NSError* error) {
		        if (!error) {
		            dispatch_async(dispatch_get_main_queue(), ^{
		                selfie.SendPushButton.enabled = YES;

		                UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Success" message:
		                                      @"Registered successfully!" delegate:nil cancelButtonTitle:
		                                      @"OK" otherButtonTitles:nil, nil];
		                [alert show];
		            });
		        }
		    }];
		}

		NSString *const SendNotificationEndpoint = @"{backend endpoint}/api/notifications";

		- (IBAction)SendPushAction:(id)sender {
		    NSURLSession* session = [NSURLSession
		                             sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration]
		                             delegate:nil
		                             delegateQueue:nil];


		    NSURL* requestURL = [NSURL URLWithString:SendNotificationEndpoint];
		    NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
		    [request setHTTPMethod:@"POST"];
		    NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@", self.registerClient.authenticationHeader];
		    [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

		    NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request completionHandler:^(NSData *data, NSURLResponse *response, NSError *error) {
		        NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
		        if (error || httpResponse.statusCode != 200)
		        {
		            NSLog(@"Error status: %d, request: %@", httpResponse.statusCode, error);
		        }
		    }];
		    [dataTask resume];
		}

11. 在 **ViewDidLoad** 函數中，新增下列程式碼以具現化 RegisterClient 執行個體，並設定文字欄位的委派。

		self.UsernameField.delegate = self;
		self.PasswordField.delegate = self;
		self.registerClient = [[RegisterClient alloc] init];

12. 現在於 **AppDelegate.m** 中，移除 **application:didRegisterForPushNotificationWithDeviceToken:** 方法的所有內容，並使用下列內容取代它，確定檢視控制器包含從 APN 擷取的最新裝置權杖：

	    ViewController* rvc = (ViewController*) self.window.rootViewController;
	    rvc.deviceToken = deviceToken;

13. 最後在 **AppDelegate.m** 中，確定您有下列方法：

		- (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
		    NSLog(@"%@", userInfo);
		    UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
		                          [[userInfo objectForKey:@"aps"] valueForKey:@"alert"] delegate:nil cancelButtonTitle:
		                          @"OK" otherButtonTitles:nil, nil];
		    [alert show];
		}

## 執行應用程式

1. 在 XCode 中，在實體 iOS 裝置上執行應用程式 (推播通知無法在模擬器中運作)。

2. 在 iOS 應用程式 UI 中，輸入使用者名稱和密碼。這些可以是任何字串，但必須是相同值。然後按一下 [**登入**]。

3. 您應該會看到註冊成功的快顯通知。按一下 [**確定**]。

4. 按一下 [**傳送推播**] 並按 home 按鈕。隨即出現推播通知。


[IOS1]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users1.png

<!--HONumber=35.1-->
