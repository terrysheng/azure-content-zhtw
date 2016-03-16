<properties pageTitle="Azure Active Directory B2C 預覽：從 iOS 應用程式呼叫 Web API | Microsoft Azure" description="本文將示範如何建立 iOS「待辦事項清單」應用程式，以使用 OAuth 2.0 持有人權杖呼叫 Node.js Web API。iOS 應用程式與 Web API 將會使用 Azure Active Directory B2C 管理使用者身分識別與驗證使用者。" services="active-directory-b2c" documentationCenter="ios" authors="brandwe" manager="mbaldwin" editor=""/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="objectivec"
	ms.topic="hero-article"
	ms.date="02/17/2016"
	ms.author="brandwe"/>

# Azure AD B2C 預覽：從 iOS 應用程式呼叫 Web API

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

Azure Active Directory (Azure AD) B2C 可讓您將強大的自助式身分識別管理功能加入至 iOS 應用程式和 Web API，只要幾個簡短步驟即可完成。本文將示範如何建立 iOS「待辦事項清單」應用程式，以使用 OAuth 2.0 持有人權杖呼叫 Node.js Web API。iOS 應用程式與 Web API 將會使用 Azure AD B2C 管理使用者身分識別與驗證使用者。

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

> [AZURE.NOTE]
	為了讓此快速入門能夠完整運作，您必須已經擁有受 Azure AD B2C 保護的 Web API。我們已經建立了一個適用於 .NET 和 Node.js 的 Web API 供您使用。本逐步解說假設您已設定好 Node.js Web API 範例。如需詳細資訊，請參閱[適用於 Node.js 範例的 Azure Active Directory Web API](active-directory-b2c-devquickstarts-api-node.md)。

> [AZURE.NOTE]
	本文不涵蓋如何使用 Azure AD B2C 實作登入、註冊和管理設定檔，而是著重在如何在使用者通過驗證後呼叫 Web API。您應該先從 [.NET Web 應用程式使用者入門教學課程](active-directory-b2c-devquickstarts-web-dotnet.md)開始 (如果還沒有進行)，以了解 Azure AD B2C 的基本概念。

## 取得 Azure AD B2C 目錄

您必須先建立目錄或租用戶，才可使用 Azure AD B2C。目錄為所有使用者、應用程式、群組等項目的容器。如果您還沒有此資源，請先[建立 B2C 目錄](active-directory-b2c-get-started.md)再繼續進行。

## 建立應用程式

接著，您必須在 B2C 目錄中建立應用程式。這會提供必要資訊給 Azure AD，讓它與應用程式安全地通訊。在此案例中，因為應用程式與 Web API 會組成一個邏輯應用程式，所以將由單一**應用程式識別碼**代表。若要建立應用程式，請遵循[這些指示](active-directory-b2c-app-registration.md)。請務必：

- 在應用程式中加入 **Web 應用程式/Web API**。
- 在 [回覆 URL] 中輸入 `http://localhost:3000/auth/openid/return`。這是此程式碼範例的預設 URL。
- 為您的應用程式建立**應用程式密碼**，並複製起來。稍後您將會用到此資訊。
- 複製指派給應用程式的**應用程式識別碼**。稍後您也會需要此資訊。

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## 建立您的原則

在 Azure AD B2C 中，每個使用者經驗皆由[原則](active-directory-b2c-reference-policies.md)所定義。此應用程式包含三種身分識別體驗：註冊、登入，以及使用 Facebook 登入。您必須為每個類型建立一個原則，如[原則參考文章](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy)所述。建立這三個原則時，請務必：

- 在註冊原則中，選擇 [顯示名稱] 和註冊屬性。
- 在每個原則中，選擇 [顯示名稱] 和 [物件識別碼] 應用程式宣告。您也可以選擇其他宣告。
- 建立每個原則之後，請複製原則的**名稱**。其前置詞應該為 `b2c_1_`。稍後您將需要這些原則名稱。

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

建立您的三個原則後，就可以開始建置您的應用程式。

請注意，本文不涵蓋如何使用您剛才建立的原則。如需了解 Azure AD B2C 中的原則如何運作，請從 [.NET Web 應用程式使用者入門教學課程](active-directory-b2c-devquickstarts-web-dotnet.md)開始。

## 下載程式碼

本教學課程的程式碼保留在 [GitHub](https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS) 上。若要遵循指示建立範例，您可以[下載基本架構專案的 .zip 檔案](https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS/archive/skeleton.zip)。您也可以複製基本架構：

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS.git
```

> [AZURE.NOTE] **您必須下載基本架構才能完成本教學課程。** 由於在 iOS 上實作完整運作的應用程式有其複雜性，因此**基本架構**所包含的 UX 程式碼將於您完成教學課程之後執行。如此能節省開發人員的時間。UX 程式碼與如何將 B2C 加入至 iOS 應用程式的主題無關。

完整的應用程式也[提供 .zip 檔案格式](https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS/archive/complete.zip)，或放在相同儲存機制的 `complete` 分支中。

接下來，使用 CocoaPods 載入 `podfile`。這會建立您將載入的新 XCode Workspace。如果您沒有 CocoaPods，請[瀏覽網站以進行設定](https://cocoapods.org)。

```
$ pod install
...
$ open Microsoft Tasks for Consumers.xcworkspace
```

## 設定 iOS 工作應用程式

為了讓 iOS 工作應用程式與 Azure AD B2C 通訊，您必須提供一些通用參數。在 `Microsoft Tasks` 資料夾中，開啟專案根目錄中的 `settings.plist` 檔案，並取代 `<dict>` 區段中的值。這些值將用於整個應用程式。

```
<dict>
	<key>authority</key>
	<string>https://login.microsoftonline.com/<your tenant name>.onmicrosoft.com/</string>
	<key>clientId</key>
	<string><Enter the Application Id assigned to your app by the Azure portal, e.g.580e250c-8f26-49d0-bee8-1c078add1609></string>
	<key>scopes</key>
	<array>
		<string><Enter the Application Id assigned to your app by the Azure portal, e.g.580e250c-8f26-49d0-bee8-1c078add1609></string>
	</array>
	<key>additionalScopes</key>
	<array>
	</array>
	<key>redirectUri</key>
	<string>urn:ietf:wg:oauth:2.0:oob</string>
	<key>taskWebAPI</key>
	<string>http://localhost/tasks:3000</string>
	<key>emailSignUpPolicyId</key>
	<string><Enter your sign up policy name, e.g.g b2c_1_sign_up></string>
	<key>faceBookSignInPolicyId</key>
	<string><your sign in policy for FB></string>
	<key>emailSignInPolicyId</key>
	<string><Enter your sign in policy name, e.g. b2c_1_sign_in></string>
	<key>fullScreen</key>
	<false/>
	<key>showClaims</key>
	<true/>
</dict>
</plist>
```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

## 取得存取權杖和呼叫工作 API

本節將討論如何在 Web 應用程式中使用 Microsoft 的程式庫和架構完成 OAuth 2.0 權杖交換。如果您不熟悉授權碼和存取權杖，請在 [OAuth 2.0 通訊協定參考](active-directory-b2c-reference-protocols.md)中進行深入了解。

### 使用方法建立標頭檔

您需要有方法來取得具有所選原則的權杖，然後呼叫工作伺服器。現在就請您設定這些方法。

在 Xcode 專案的 `/Microsoft Tasks` 底下建立名為 `samplesWebAPIConnector.h` 的檔案。

在其中新增下列程式碼以定義所需執行的工作：

```
#import <Foundation/Foundation.h>
#import "samplesTaskItem.h"
#import "samplesPolicyData.h"
#import "ADALiOS/ADAuthenticationContext.h"

@interface samplesWebAPIConnector : NSObject<NSURLConnectionDataDelegate>

+(void) getTaskList:(void (^) (NSArray*, NSError* error))completionBlock
             parent:(UIViewController*) parent;

+(void) addTask:(samplesTaskItem*)task
         parent:(UIViewController*) parent
completionBlock:(void (^) (bool, NSError* error)) completionBlock;

+(void) deleteTask:(samplesTaskItem*)task
            parent:(UIViewController*) parent
   completionBlock:(void (^) (bool, NSError* error)) completionBlock;

+(void) doPolicy:(samplesPolicyData*)policy
         parent:(UIViewController*) parent
completionBlock:(void (^) (ADProfileInfo* userInfo, NSError* error)) completionBlock;

+(void) signOut;

@end
```

上述程式碼是會在 API 上執行建立、讀取、更新和刪除 (CRUD) 等工作的簡單作業，以及 `doPolicy` 方法。使用此方法，您就能取得具有所要原則的權杖。

請注意，您還需要另外定義兩個標頭檔。這兩個標頭檔會保留工作項目和原則資料。現在就來建立這兩個檔案：

使用下列程式碼建立 `samplesTaskItem.h` 檔案：

```
#import <Foundation/Foundation.h>

@interface samplesTaskItem : NSObject

@property NSString *itemName;
@property NSString *ownerName;
@property BOOL completed;
@property (readonly) NSDate *creationDate;

@end
```

此外，建立 `samplesPolicyData.h` 檔案來保留原則資料：

```
#import <Foundation/Foundation.h>

@interface samplesPolicyData : NSObject

@property (strong) NSString* policyName;
@property (strong) NSString* policyID;

+(id) getInstance;

@end
```
### 新增用於工作和原則項目的實作

您已備妥標頭檔，接下來可以撰寫程式碼來儲存範例中要使用的資料。

使用下列程式碼建立 `samplesPolicyData.m` 檔案：

```
#import <Foundation/Foundation.h>
#import "samplesPolicyData.h"

@implementation samplesPolicyData

+(id) getInstance
{
    static samplesPolicyData *instance = nil;
    static dispatch_once_t onceToken;

    dispatch_once(&onceToken, ^{
        instance = [[self alloc] init];
        NSDictionary *dictionary = [NSDictionary dictionaryWithContentsOfFile:[[NSBundle mainBundle] pathForResource:@"settings" ofType:@"plist"]];
        instance.policyName = [dictionary objectForKey:@"policyName"];
        instance.policyID = [dictionary objectForKey:@"policyID"];


    });

    return instance;
}


@end
```

### 撰寫設定程式碼來呼叫 ADAL for iOS

用來儲存 UI 之物件的快速程式碼現在已完成。接下來，您必須使用在 `settings.plist` 中所放入的參數，撰寫用來存取 Active Directory Authentication Library (ADAL) for iOS 的程式碼。這個程式碼會取得存取權杖以提供給工作伺服器。

所有工作都會在 `samplesWebAPIConnector.m` 中完成。

首先，建立您在 `samplesWebAPIConnector.h` 標頭檔中所撰寫的 `doPolicy()` 實作：

```
+(void) doPolicy:(samplesPolicyData *)policy
         parent:(UIViewController*) parent
completionBlock:(void (^) (ADProfileInfo* userInfo, NSError* error)) completionBlock
{
    if (!loadedApplicationSettings)
    {
        [self readApplicationSettings];
    }

    [self getClaimsWithPolicyClearingCache:NO policy:policy params:nil parent:parent completionHandler:^(ADProfileInfo* userInfo, NSError* error) {

        if (userInfo == nil)
        {
            completionBlock(nil, error);
        }

        else {

            completionBlock(userInfo, nil);
        }
    }];

}


```

這個方法很簡單。它會將您建立的 `samplesPolicyData` 物件、父系 `ViewController` 和回呼做為輸入。回呼是個有趣的動作，因此我們會對它進行更深入的了解。

- 請注意，`completionBlock` 將 `ADProfileInfo` 做為會使用 `userInfo` 物件傳回的類型。`ADProfileInfo` 這個類型會保留伺服器的所有回應，包括宣告。
- 另請注意 `readApplicationSettings`。這個項目會讀取您在 `settings.plist` 中提供的資料。
- 最後，您還有一個大型的 `getClaimsWithPolicyClearingCache` 方法。這才是您真正需要撰寫來呼叫 ADAL for iOS 的方法。我們稍後會再回來討論這個方法。

接下來，撰寫您的大型方法 `getClaimsWithPolicyClearingCache`。這個方法夠大，值得我們另闢一個章節來討論它。

### 建立對 ADAL for iOS 的呼叫

從 GitHub 下載基本架構後，您會看到其中已經有幾個可協助執行範例應用程式的呼叫。它們全都遵循 `get(Claims|Token)With<verb>ClearningCache` 的模式。透過使用 Objetive C 的慣例，這些呼叫的組成非常類似英文語句。例如，"Get a token with extra parameters that I provide to you and clear the cache" 是 `getTokenWithExtraParamsClearingCache()`。

您將會撰寫 "Get claims and a token with the policy that I provide to you and don't clear the cache" 或 `getClaimsWithPolicyClearingCache`。您一定可從 ADAL 取回權杖，因此不需要在方法中指定 "claims and a token"。不過，有時您只想要不需額外剖析宣告的權杖，因此我們在基本架構中也提供了沒有宣告的方法，其名稱為 `getTokenWithPolicyClearingCache`。

現在讓我們來撰寫這個程式碼：

```
+(void) getClaimsWithPolicyClearingCache  : (BOOL) clearCache
                           policy:(samplesPolicyData *)policy
                           params:(NSDictionary*) params
                           parent:(UIViewController*) parent
                completionHandler:(void (^) (ADProfileInfo*, NSError*))completionBlock;
{
    SamplesApplicationData* data = [SamplesApplicationData getInstance];


    ADAuthenticationError *error;
    authContext = [ADAuthenticationContext authenticationContextWithAuthority:data.authority error:&error];
    authContext.parentController = parent;
    NSURL *redirectUri = [[NSURL alloc]initWithString:data.redirectUriString];

    if(!data.correlationId ||
       [[data.correlationId stringByTrimmingCharactersInSet:[NSCharacterSet whitespaceAndNewlineCharacterSet]] length] == 0)
    {
        authContext.correlationId = [[NSUUID alloc] initWithUUIDString:data.correlationId];
    }

    [ADAuthenticationSettings sharedInstance].enableFullScreen = data.fullScreen;
    [authContext acquireTokenWithScopes:data.scopes
                      additionalScopes: data.additionalScopes
                              clientId:data.clientId
                           redirectUri:redirectUri
                            identifier:[ADUserIdentifier identifierWithId:data.userItem.profileInfo.username type:RequiredDisplayableId]
                            promptBehavior:AD_PROMPT_ALWAYS
                  extraQueryParameters: params.urlEncodedString
                                policy: policy.policyID
                       completionBlock:^(ADAuthenticationResult *result) {

                           if (result.status != AD_SUCCEEDED)
                           {
                               completionBlock(nil, result.error);
                           }                              else
                              {
                                  data.userItem = result.tokenCacheStoreItem;
                                  completionBlock(result.tokenCacheStoreItem.profileInfo, nil);
                              }
                          }];
}


```

第一個部分看起來應該很熟悉。

- 載入 `settings.plist` 中提供的設定並指派給 `data`。
- 設定 `ADAuthenticationError` 以擷取來自 ADAL for iOS 的任何錯誤。
- 建立 `authContext` 以設定對 ADAL 的呼叫。將授權傳遞給呼叫以讓程序開始進行。
- 提供父控制器的參考給 `authContext` 以便能夠返回。
- 將 `settings.plist` 中的字串 `redirectURI` 轉換成 ADAL 所需的 NSURL 類型。
- 設定 `correlationId`。這是可用來追蹤往返用戶端和伺服器間之呼叫的 UUID。這有助於偵錯。

接下來您會來到對 ADAL 的實際呼叫。呼叫在這邊開始變得和您先前使用 ADAL for iOS 時所看見的情況不同：

```
[authContext acquireTokenWithScopes:data.scopes
                      additionalScopes: data.additionalScopes
                              clientId:data.clientId
                           redirectUri:redirectUri
                            identifier:[ADUserIdentifier identifierWithId:data.userItem.profileInfo.username type:RequiredDisplayableId]
                            promptBehavior:AD_PROMPT_ALWAYS
                  extraQueryParameters: params.urlEncodedString
                                policy: policy.policyID
                       completionBlock:^(ADAuthenticationResult *result) {

```

您可以看到這個呼叫相當簡單。

`scopes`：您傳給伺服器的範圍，而您會想要為登入的使用者向伺服器要求此範圍。在 B2C 預覽版本中，您會傳遞 `client_id`。不過，未來這應該會變為 read scopes。我們計劃到時候再更新此文件。`additionalScopes`：這些是您可能想要用於應用程式的其他範圍。未來應該會用到這些範圍。`clientId`：從入口網站取得的應用程式識別碼。`redirectURI`：您希望將權杖張貼回到的重新導向位置。`identifier`：用來識別使用者以查看快取中是否有可用權杖的方法。這可避免一直向伺服器索要其他權杖。這會透過名為 `ADUserIdentifier` 的類型來進行，而且您可以指定要用來做為識別碼的項目。您應該使用 `username`。`promptBehavior`：這個項目已被取代。現在應該是 `AD_PROMPT_ALWAYS`。`extraQueryParameters`：您想要以 URL 編碼格式傳遞給伺服器的任何額外參數。`policy`：您要叫用的原則。這是本逐步解說最重要的部分。

如您所見，您在 `completionBlock` 中所傳遞的是 `ADAuthenticationResult`。這個項目中有您的權杖和設定檔資訊 (如果呼叫成功的話)。

透過使用上面的程式碼，您可以為您提供的原則取得權杖。接著，您可以使用此權杖來呼叫 API。

### 建立對伺服器的工作呼叫

您已經擁有權杖，因此接下來必須將它提供給 API 以進行授權。

您必須實作三個方法：

```
+(void) getTaskList:(void (^) (NSArray*, NSError* error))completionBlock
             parent:(UIViewController*) parent;

+(void) addTask:(samplesTaskItem*)task
         parent:(UIViewController*) parent
completionBlock:(void (^) (bool, NSError* error)) completionBlock;

+(void) deleteTask:(samplesTaskItem*)task
            parent:(UIViewController*) parent
   completionBlock:(void (^) (bool, NSError* error)) completionBlock;
```

`getTasksList` 會提供代表伺服器中工作的陣列。`addTask` 和 `deleteTask` 會執行後續動作並在執行成功時傳回 `true` 或 `false`。

先撰寫 `getTaskList`：

```

+(void) getTaskList:(void (^) (NSArray*, NSError*))completionBlock
             parent:(UIViewController*) parent;
{
    if (!loadedApplicationSettings)
    {
        [self readApplicationSettings];
    }

    SamplesApplicationData* data = [SamplesApplicationData getInstance];

    [self craftRequest:[self.class trimString:data.taskWebApiUrlString]
                parent:parent
     completionHandler:^(NSMutableURLRequest *request, NSError *error) {

        if (error != nil)
        {
            completionBlock(nil, error);
        }
        else
        {

            NSOperationQueue *queue = [[NSOperationQueue alloc]init];

            [NSURLConnection sendAsynchronousRequest:request queue:queue completionHandler:^(NSURLResponse *response, NSData *data, NSError *error) {

                if (error == nil && data != nil){

                    NSArray *tasks = [NSJSONSerialization JSONObjectWithData:data options:0 error:nil];

                    //each object is a key value pair
                    NSDictionary *keyValuePairs;
                    NSMutableArray* sampleTaskItems = [[NSMutableArray alloc]init];

                    for(int i =0; i < tasks.count; i++)
                    {
                        keyValuePairs = [tasks objectAtIndex:i];

                        samplesTaskItem *s = [[samplesTaskItem alloc]init];
                        s.itemName = [keyValuePairs valueForKey:@"task"];

                        [sampleTaskItems addObject:s];
                    }

                    completionBlock(sampleTaskItems, nil);
                }
                else
                {
                    completionBlock(nil, error);
                }

            }];
        }
    }];

}

```

工作程式碼不在本逐步解說的討論範圍內。但您可能已經注意到一些有趣的東西，那就是用來取得工作 URL 的 `craftRequest` 方法。您就是使用這個方法，透過收到的存取權杖對伺服器建立要求。現在就撰寫該方法。

在 `samplesWebAPIConnector.m` 檔案中新增下列程式碼：

```
+(void) craftRequest : (NSString*)webApiUrlString
               parent:(UIViewController*) parent
    completionHandler:(void (^)(NSMutableURLRequest*, NSError* error))completionBlock
{
    [self getClaimsWithPolicyClearingCache:NO parent:parent completionHandler:^(NSString* accessToken, NSError* error){

        if (accessToken == nil)
        {
            completionBlock(nil,error);
        }
        else
        {
            NSURL *webApiURL = [[NSURL alloc]initWithString:webApiUrlString];

            NSMutableURLRequest *request = [[NSMutableURLRequest alloc]initWithURL:webApiURL];

            NSString *authHeader = [NSString stringWithFormat:@"Bearer %@", accessToken];

            [request addValue:authHeader forHTTPHeaderField:@"Authorization"];

            completionBlock(request, nil);
        }
    }];
}
```

這個程式碼會取得 Web 統一資源識別項 (URI)、在 HTTP 中以 `Bearer` 標頭將權杖加入 URI 中，然後再將此 URI 傳回給您。您會呼叫 `getTokenClearingCache` API。雖然看起來很奇怪，但您只是使用這個呼叫來取得快取中的權杖，並確保其仍然有效 (`getToken` 呼叫會透過要求 ADAL 來幫您執行此工作)。 您將會在每個呼叫時使用這個程式碼。接下來請建立其他工作方法。

撰寫 `addTask`：

```
+(void) addTask:(samplesTaskItem*)task
         parent:(UIViewController*) parent
completionBlock:(void (^) (bool, NSError* error)) completionBlock
{
    if (!loadedApplicationSettings)
    {
        [self readApplicationSettings];
    }

    SamplesApplicationData* data = [SamplesApplicationData getInstance];
    [self craftRequest:data.taskWebApiUrlString parent:parent completionHandler:^(NSMutableURLRequest* request, NSError* error){

        if (error != nil)
        {
            completionBlock(NO, error);
        }
        else
        {
            NSDictionary* taskInDictionaryFormat = [self convertTaskToDictionary:task];

            NSData* requestBody = [NSJSONSerialization dataWithJSONObject:taskInDictionaryFormat options:0 error:nil];

            [request setHTTPMethod:@"POST"];
            [request addValue:@"application/json" forHTTPHeaderField:@"Content-Type"];
            [request setHTTPBody:requestBody];

            NSString *myString = [[NSString alloc] initWithData:requestBody encoding:NSUTF8StringEncoding];

            NSLog(@"Request was: %@", request);
            NSLog(@"Request body was: %@", myString);

            NSOperationQueue *queue = [[NSOperationQueue alloc]init];

            [NSURLConnection sendAsynchronousRequest:request queue:queue completionHandler:^(NSURLResponse *response, NSData *data, NSError *error) {

                NSString* content = [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding];
                NSLog(@"%@", content);

                if (error == nil){

                    completionBlock(true, nil);
                }
                else
                {
                    completionBlock(false, error);
                }
            }];
        }
    }];
}
```

這個方法遵循相同的模式，但它也會引入您需要實作的最後一個方法：`convertTaskToDictionary`。這個方法會取得您的陣列，並使它成為字典物件。此物件可更輕鬆地變更為您需要傳遞到伺服器的查詢參數。這個程式碼很簡單：

```
// Here we have some conversation helpers that allow us to parse passed items into dictionaries for URLEncoding later.

+(NSDictionary*) convertTaskToDictionary:(samplesTaskItem*)task
{
    NSMutableDictionary* dictionary = [[NSMutableDictionary alloc]init];

    if (task.itemName){
        [dictionary setValue:task.itemName forKey:@"task"];
    }

    return dictionary;
}

```

接下來撰寫 `deleteTask`：

```
+(void) deleteTask:(samplesTaskItem*)task
            parent:(UIViewController*) parent
   completionBlock:(void (^) (bool, NSError* error)) completionBlock
{
    if (!loadedApplicationSettings)
    {
        [self readApplicationSettings];
    }

    SamplesApplicationData* data = [SamplesApplicationData getInstance];
    [self craftRequest:data.taskWebApiUrlString parent:parent completionHandler:^(NSMutableURLRequest* request, NSError* error){

        if (error != nil)
        {
            completionBlock(NO, error);
        }
        else
        {
            NSDictionary* taskInDictionaryFormat = [self convertTaskToDictionary:task];

            NSData* requestBody = [NSJSONSerialization dataWithJSONObject:taskInDictionaryFormat options:0 error:nil];

            [request setHTTPMethod:@"DELETE"];
            [request addValue:@"application/json" forHTTPHeaderField:@"Content-Type"];
            [request setHTTPBody:requestBody];

            NSLog(@"%@", request);

            NSOperationQueue *queue = [[NSOperationQueue alloc]init];

            [NSURLConnection sendAsynchronousRequest:request queue:queue completionHandler:^(NSURLResponse *response, NSData *data, NSError *error) {

                NSString* content = [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding];
                NSLog(@"%@", content);

                if (error == nil){

                    completionBlock(true, nil);
                }
                else
                {
                    completionBlock(false, error);
                }
            }];
        }
    }];
}
```

### 在應用程式中加入登出

最後就是要為應用程式實作登出程序。步驟很簡單。在 `sampleWebApiConnector.m` 檔案內：

```
+(void) signOut
{
    [authContext.tokenCacheStore removeAll:nil];

    NSHTTPCookie *cookie;

    NSHTTPCookieStorage *storage = [NSHTTPCookieStorage sharedHTTPCookieStorage];
    for (cookie in [storage cookies])
    {
        [storage deleteCookie:cookie];
    }
}
```

## 執行範例應用程式

最後，在 Xcode 中建置並執行應用程式。註冊或登入應用程式，並為登入的使用者建立工作。登出後，再以不同使用者身分重新登入，然後為該使用者建立工作。

請注意，這些工作會依每位使用者儲存於 API，因為 API 會從它收到的存取權杖中擷取使用者的身分識別。

為了方便參考，[會以 .zip 檔案提供](https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS/archive/complete.zip)完整的範例。您也可以從 Github 複製它：

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS```

## 後續步驟

您現在可以進入更進階的 B2C 主題。您可以嘗試：

[從 Node.js Web 應用程式呼叫 Node.js Web API]()

[自訂 B2C 應用程式的 UX]()

<!---HONumber=AcomDC_0302_2016-->