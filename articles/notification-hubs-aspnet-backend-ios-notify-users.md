<properties title="Azure Notification Hubs Notify Users" pageTitle="Azure Notification Hubs Notify Users" metaKeywords="Azure push notifications, Azure notification hubs" description="Learn how to send secure push notifications in Azure. Code samples written in Objective-C using the .NET API." documentationCenter="Mobile" metaCanonical="" disqusComments="1" umbracoNaviHide="0" authors="sethm" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="sethm"></tags>

# Azure 通知中心通知使用者

<div class="dev-center-tutorial-selector sublanding"> 
<a href="/zh-tw/documentation/articles/notification-hubs-windows-dotnet-notify-users/" title="Windows Universal">Windows Universal</a><a href="/zh-tw/documentation/articles/notification-hubs-/" title="iOS" class="current">iOS</a>
<a href="/zh-tw/documentation/articles/notification-hubs-aspnet-backend-android-notify-users/" title="Android">Android</a>
</div>

Azure 中的推播通知支援可讓您存取易於使用、多重平台的大規模推播基礎結構，而大幅簡化消費者和企業應用程式在行動平台上的推播通知實作。本教學課程將示範如何使用 Azure 通知中心，來將推播通知傳送到特定裝置上的特定應用程式使用者。ASP.NET WebAPI 後端可用來驗證用戶端並產生通知，如指引主題[從您的應用程式後端註冊][從您的應用程式後端註冊]中所示。本教學課程會以您在**開始使用通知中心**教學課程中所建立的通知中心為基礎。

本教學課程還是**安全推播**教學課程的必要條件。完成本**通知使用者**教學課程後，您可以繼續進行**安全推播**教學課程，該教學課程說明如何修改**通知使用者**程式碼，來以安全的方式傳送推播通知。

> [AZURE.NOTE] 本教學課程假設您已建立並設定通知中心，如[開始使用通知中心 (iOS)][開始使用通知中心 (iOS)] 中所述。

## 建立與設定通知中心

在您開始本教學課程之前，您必須建立 iOS 佈建設定檔和開發推播憑證，然後建立 Azure 通知中心並將它連接到該應用程式。請依照[開始使用通知中心 (iOS)][開始使用通知中心 (iOS)] 中的步驟進行，尤其是從第 1 節到第 5 節。

[WACOM.INCLUDE [notification-hubs-aspnet-backend-notifyusers](../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## 修改您的 iOS 應用程式

1.  請依照[開始使用通知中心 (iOS)][開始使用通知中心 (iOS)] 第 1 節到第 5 節中的步驟進行，以建立能夠從通知中心接收推播通知的單一頁面檢視應用程式。

> [AZURE.NOTE] 在本節中，我們假設您已設定使用空白組織名稱的專案。如果您的情況並非如此，您必須在下列程式碼中為所有類別名稱加上組織名稱。

1.  在您的 Main.storyboard 中，新增下列物件程式庫元件：

    -   包含預留位置文字 **Username** 的 UITextField
    -   包含預留位置文字 **Password** 的 UITextField，並勾選右側窗格 TextField 屬性群組中的 [Secure Text Entry] 選項
    -   標示為 [1. Log in] 的 UIButton，並取消勾選右側窗格 Accessibility 屬性群組中的 [啟用] 選項
    -   標示為 [2. Send Push] 的 UIButton，並取消勾選右側窗格 Accessibility 屬性群組中的 [啟用] 選項

    您的腳本應如下所示：

    ![][]

2.  在 ViewController.m 的介面部分中，建立 UITextFields 和 UIButtons 的出口

        @property (weak, nonatomic) IBOutlet UITextField *UsernameField;
        @property (weak, nonatomic) IBOutlet UITextField *PasswordField;
        @property (weak, nonatomic) IBOutlet UIButton *LogInButton;
        @property (weak, nonatomic) IBOutlet UIButton *SendPushButton;

3.  在 ViewController.m implementation 中建立兩者按鈕的動作：
    - (IBAction)LogInAction:(id)sender { }
     - (IBAction)SendPushAction:(id)sender { }

4.  首先，我們將建立可與後端整合的 RegisterClient 類別。建立名為 RegisterClient (繼承自 NSObject) 的 Objective-C 類別。然後在 RegisterClient.h 介面區段中新增下列程式碼：

        @property (strong, nonatomic) NSString* authenticationHeader;
        -(void) registerWithDeviceToken:(NSData*) token tags:(NSSet*) tags andCompletion: (void(^)(NSError*)) completion;

5.  在 RegisterClient.m 中新增下列介面區段：

        @interface RegisterClient ()

        @property (strong, nonatomic) NSURLSession* session;
        -(void) tryToRegisterWithDeviceToken:(NSData*) token tags:(NSSet*) tags retry: (BOOL) retry andCompletion: (void(^)(NSError*)) completion;
        -(void) retrieveOrRequestRegistrationIdWithDeviceToken: (NSString*) token completion: (void(^)(NSString*, NSError*)) completion;
        -(void) upsertRegistrationWithRegistrationId: (NSString*) registrationId deviceToken: (NSString*) token tags: (NSSet*)tags andCompletion: (void(^)(NSURLResponse*, NSError*)) completion;

        @end

6.  然後在實作區段中新增下列程式碼，並將 *{backend endpoint}* 預留位置替換成上一節中用來部署應用程式後端的端點。

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

    上面的程式碼會實作指引文章[從您的應用程式後端註冊][從您的應用程式後端註冊]中所說明的邏輯，方法是使用 NSURLSession 來對您的應用程式後端執行 REST 呼叫，然後使用 NSUserDefaults 來本機儲存通知中心傳回的 registrationId。

    請注意，此類別需要設定 **authorizationHeader** 屬性，才能正常運作。您可以在登入後，透過 **ViewController** 類別設定此屬性。

7.  在 ViewController.h 中，新增下列將包含裝置權杖的屬性：

        @property (strong, nonatomic) NSData* deviceToken;

8.  在 ViewController.m 中，將 ViewController 類別設定為 UITextFieldDelegate、新增屬性以參考 RegisterClient 執行個體，並新增私用方法宣告。您的介面區段應如下所示：

        @interface ViewController () <UITextFieldDelegate>
        @property (weak, nonatomic) IBOutlet UITextField *UsernameField;
        @property (weak, nonatomic) IBOutlet UITextField *PasswordField;
        @property (weak, nonatomic) IBOutlet UIButton *LogInButton;
        @property (weak, nonatomic) IBOutlet UIButton *SendPushButton;

        @property (strong, nonatomic) RegisterClient* registerClient;

        -(void) createAndSetAuthenticationHeaderWithUsername: (NSString*) username AndPassword: (NSString*) password;
        @end

    此私用方法可用來建立驗證標頭，以向應用程式後端執行基本驗證。

> [AZURE.NOTE] 這不是安全的驗證結構描述，您應將 **createAndSetAuthenticationHeaderWithUsername:AndPassword:** 的實作替換成您的特定驗證機制 (產生由 OAuth、Active Directory 等註冊用戶端類別取用的驗證權杖)。

1.  接著，在 ViewController.m 的實作區段中新增下列程式碼：

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

    請留意設定裝置權杖如何啟用登入按鈕。這是因為，作為登入動作的一部分，檢視控制器會向應用程式後端註冊推播通知。因此，在正確設定裝置權杖之前，我們要避免使用者按登入按鈕。在您的應用程式中，只要登入在推播註冊之前發生，您可能就會想要將前者與後者分開。

2.  在 ViewController.m 中，新增一個後端端點的常數，並為您的 UIButtons 填入動作方法的實作。請記得使用上一節中所使用的後端端點來取代預留位置。

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

3.  最後在 **ViewDidLoad** 中，新增下列程式碼以具現化 RegisterClient 執行個體，並設定文字欄位的委派。

        self.UsernameField.delegate = self;
        self.PasswordField.delegate = self;
        self.registerClient = [[RegisterClient alloc] init];

4.  現在，在您的 **AppDelegate.m** 中，將 **application:didRegisterForPushNotificationWithDeviceToken:** 方法的所有內容移除，並使用下列程式碼來取代：

        ViewController* rvc = (ViewController*) self.window.rootViewController;
        rvc.deviceToken = deviceToken;

    這可確保檢視控制器包含從 APN 擷取的最新裝置權杖。

5.  仍在 **AppDelegate.m** 中，確定您有下列方法：

        - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
            NSLog(@"%@", userInfo);
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
                                  [[userInfo objectForKey:@"aps"] valueForKey:@"alert"] delegate:nil cancelButtonTitle:
                                  @"OK" otherButtonTitles:nil, nil];
            [alert show];
        }

## 執行應用程式

若要執行應用程式，請執行下列動作：

1.  確定 Azure 中已部署 **AppBackend**。如果使用 Visual Studio，則執行 **AppBackend** Web API 應用程式。[ASP.NET Web] 頁面便會隨即顯示。

2.  在 XCode 中，在實體 iOS 裝置上執行應用程式 (推播通知無法在模擬器中運作)。

3.  在 iOS 應用程式 UI 中，輸入使用者名稱和密碼。這些可以是任何字串，但必須是相同值。

4.  在 iOS 應用程式 UI 中，按一下 [登入]。然後按一下 [傳送推播]。

  [Windows Universal]: /zh-tw/documentation/articles/notification-hubs-windows-dotnet-notify-users/ "Windows Universal"
  [iOS]: /zh-tw/documentation/articles/notification-hubs-/ "iOS"
  [Android]: /zh-tw/documentation/articles/notification-hubs-aspnet-backend-android-notify-users/ "Android"
  [從您的應用程式後端註冊]: http://msdn.microsoft.com/zh-tw/library/dn743807.aspx
  [開始使用通知中心 (iOS)]: http://azure.microsoft.com/zh-tw/documentation/articles/notification-hubs-ios-get-started/
  [notification-hubs-aspnet-backend-notifyusers]: ../includes/notification-hubs-aspnet-backend-notifyusers.md
  []: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users1.png
