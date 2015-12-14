<properties pageTitle="Azure AD B2C 預覽：從 iOS 應用程式中呼叫 Web API | Microsoft Azure" description="本文將示範如何建立使用 OAuth 2.0 持有人權杖呼叫 node.js Web API 的 iOS「待辦事項清單」應用程式。iOS 應用程式與 Web API 將會使用 Azure AD B2C 管理使用者身分識別與驗證使用者。" services="active-directory-b2c" documentationCenter="ios" authors="brandwe" manager="msmbaldwin" editor=""/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="objectivec"
	ms.topic="article"
	ms.date="09/22/2015"
	ms.author="brandwe"/>

# Azure AD B2C 預覽：從 iOS 應用程式中呼叫 Web API

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

有了 Azure AD B2C，您可以將強大的自助式身分識別管理功能加入至 iOS 應用程式和 Web API，只要幾個簡短步驟即可完成。本文將示範如何建立使用 OAuth 2.0 持有人權杖呼叫 node.js Web API 的 iOS「待辦事項清單」應用程式。iOS 應用程式與 Web API 將會使用 Azure AD B2C 管理使用者身分識別與驗證使用者。

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]
	
> [AZURE.NOTE]使用此快速入門的先決條件為，您必須擁有受 Azure AD (含 B2C) 保護的 Web API，才可完整運作。我們已經為 .NET 與 node.js 建立一個 Web API 供您使用。本逐步解說假設您已設定 node.js Web API 範例。請參閱 [Node.js 的 Azure Active Directory Web API 範例](active-directory-b2c-devquickstarts-api-node.md`)。

> [AZURE.NOTE]本文不涵蓋如何使用 Azure AD B2C 實作登入、註冊和管理設定檔。而會著重在如何在使用者已通過驗證後呼叫 Web API。您應該先從 [.NET Web App 使用者入門教學課程](active-directory-b2c-devquickstarts-web-dotnet.md)開始 (如果還沒有進行)，以了解 Azure AD B2C 的基本概念。

## 1\.取得 Azure AD B2C 目錄

您必須先建立目錄或租用戶，才可使用 Azure AD B2C。目錄為所有使用者、應用程式、群組等項目的容器。如果您尚未擁有目錄，請先[建立 B2C 目錄](active-directory-b2c-get-started.md)，再繼續下一個步驟。

## 2\.建立應用程式

您現在需要在 B2C 目錄中建立應用程式，以提供一些必要資訊給 Azure AD，讓它與應用程式安全地通訊。在此案例中，因為應用程式與 Web API 會組成一個邏輯應用程式，所以將由單一**應用程式識別碼**代表。若要建立應用程式，請遵循[這些指示](active-directory-b2c-app-registration.md)。請務必

- 在應用程式中加入 **Web 應用程式/Web API**
- 輸入 `http://localhost:3000/auth/openid/return` 作為**回覆 URL** -它是此程式碼範例的預設 URL。
- 為您的應用程式建立**應用程式密碼**，並複製下來。稍後您將會用到此資訊。
- 複製指派給應用程式的**應用程式識別碼**。稍後您也會用到此資訊。

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## 3\.建立您的原則

在 Azure AD B2C 中，每個使用者經驗皆是由某個[**原則**](active-directory-b2c-reference-policies.md)所定義。此應用程式包含三種身分識別體驗 - 註冊、登入，以及使用 Facebook 登入。您必須為每個類型建立一個原則，如[原則參考文章](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy)所述。建立您的三個原則時，請務必：

- 在註冊原則中選擇 [顯示名稱] 和其他一些註冊屬性。
- 在每個原則中選擇 [顯示名稱] 和 [物件識別碼] 應用程式宣告。您也可以選擇其他宣告。
- 建立每個原則後，請複製原則的 [名稱]。其前置詞應該為 `b2c_1_`。稍後您將需要這些原則名稱。 

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

當您成功建立三個原則後，就可以開始建置您的應用程式。

請注意，本文不會說明如何使用您剛才建立的原則。如果您想要了解 Azure AD B2C 中的原則如何運作，應該從 [.NET Web 應用程式使用者入門教學課程](active-directory-b2c-devquickstarts-web-dotnet.md)開始。

## 4\.下載程式碼

本教學課程的程式碼保留在 [GitHub](https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS) 上。若要遵循指示建立範例，您可以[下載 .zip 格式的基本架構專案](https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS/archive/skeleton.zip)，或複製基本架構：

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS.git
```

> [AZURE.NOTE]**若要完成此教學課程，必須下載基本架構。** 由於在 iOS 上實作完整運作的應用程式有其複雜性，因此**基本架構**所包含的 UX 程式碼將於您完成下方教學課程之後執行。如此將能節省開發人員的時間。UX 程式碼與加入 B2C 至 iOS 應用程式的主題無關。

完整的應用程式也提供 [.zip 格式](https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS/archive/complete.zip)；您也可以在相同儲存機制的 `complete` 分支取得。


現在使用 Cocoapods 載入該 Podfile。這會建立您將載入的新 XCode Workspace。如果您沒有 Cocoapods，請瀏覽[網站安裝 Cocoapods](https://cocoapods.org)。

```
$ pod install
...
$ open Microsoft Tasks for Consumers.xcworkspace
```

## 5\.設定 iOS 工作應用程式

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

## 6\.取得存取權杖和呼叫工作 API

本節將示範如何在 Web 應用程式中使用 Microsoft 的程式庫和架構完成 OAuth 2.0 權杖交換。如果您不熟悉**授權碼**和**存取權杖**，建議您瀏覽 [OAuth 2.0 通訊協定參考](active-directory-b2c-reference-protocols.md)。

#### 建立標頭檔並加入我們將使用的方法。

我們將需要這些方法來取得權杖與我們選取的原則，然後呼叫我們工作伺服器。現在就讓我們進行這些設定。

在 XCode 專案中的 /Microsoft Tasks 下，建立名為 `samplesWebAPIConnector.h` 的檔案

加入下列程式碼來定義我們需要做的事：

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

您會看到這些是我們 API 上的簡單 CRUD 作業，還有 `doPolicy` 方法可讓您取得權杖和您想要的原則。

您也會看到還有另外兩個標頭檔需要定義，其中將保存我們的「工作項目」和「原則資料」。讓我們馬上建立它們：

建立名為 `samplesTaskItem.h` 的檔案並加入下列程式碼：

```
#import <Foundation/Foundation.h>

@interface samplesTaskItem : NSObject

@property NSString *itemName;
@property NSString *ownerName;
@property BOOL completed;
@property (readonly) NSDate *creationDate;

@end
```

接著再建立 `samplesPolicyData.h` 檔案來保存原則資料：

```
#import <Foundation/Foundation.h>

@interface samplesPolicyData : NSObject

@property (strong) NSString* policyName;
@property (strong) NSString* policyID;

+(id) getInstance;

@end
```
#### 加入工作和原則項目的實作

既然我們已經有了標頭檔，我們需要撰寫一些程式碼，以儲存我們將用於範例的資料。

建立名為 `samplesPolicyData.m` 的檔案並加入下列程式碼：

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

#### 撰寫設定程式碼來呼叫 ADAL for iOS

UI 方面用來儲存物件的快速程式碼已完成。現在需要使用我們放入 `settings.plist` 檔案中的參數，撰寫程式碼來存取 ADAL for iOS，以取得存取權杖來提供給工作伺服器。過程可能很冗長，請保持注意力。

我們所有的工作都將在 `samplesWebAPIConnector.m` 中完成。

首先，建立我們在 `samplesWebAPIConnector.h` 標頭檔中撰寫的 `doPolicy()` 實作：

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

您會看到方法相當簡單。它會接收我們不久之前建立的 `samplesPolicyData` 物件、父 ViewController 和回呼以做為輸入。此回呼很有趣，我們將逐步解說。

1. 您會看到 `completionBlock` 的類型為 ADProfileInfo，其將隨著 `userInfo` 物件一起傳回。ADProfileInfo 這個類型保存來自伺服器的所有回應，特別是宣告。 
2. 您會看到我們有 `readApplicationSettings`。這會讀取我們在 `settings.plist` 中提供的資料
3. 最後，有一個很大型的 `getClaimsWithPolicyClearingCache` 方法。這就是我們真正需要撰寫來呼叫 ADAL for iOS 的方法。我們稍後會這樣做。

現在來撰寫大型方法 `getClaimsWithPolicyClearingCache`。這大到足以自成一個段落

#### 建立對 ADAL for iOS 的呼叫

如果您從 GitHub 下載基本架構，您會看到我們已準備好幾個部分可協助執行範例應用程式。它們全都遵循 `get(Claims|Token)With<verb>ClearningCache` 的模式。採用 Objetive C 慣例時，讀起來就很像英文。例如，"get a Token with extra parameters I provide you and clear the cache" (以我為您提供的額外參數取得權杖並清除快取)。這就是 `getTokenWithExtraParamsClearingCache()`。很簡單。

我們將撰寫 "get Claims and a token With the policy I provide you and don't clear the cache" (以我提供給您的原則取得宣告和權杖但不清除快取) 或 `getClaimsWithPolicyClearingCache`。我們永遠從 ADAL 取回權杖，因此不需要在方法中指定「宣告和權杖」。不過，有時您只想要權杖，而不想額外剖析宣告，因此我們在基本架構中提供一個沒有「宣告」的方法，稱為 `getTokenWithPolicyClearingCache`。

現在讓我們撰寫這段程式碼：

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

第一個部分看起來應該很熟悉。我們載入 `Settings.plist` 中提供的設定，並指派給 `data`。接著設定 `ADAuthenticationError`，以接收任何來自 ADAL for iOS 的錯誤。我們也會建立 `authContext` 來設定對 ADAL 的呼叫。一切從傳遞 *authority* 給它開始。我們也提供父控制器的參考給 `authContext`，以便能夠返回它。此外，還會將 `settings.plist` 中的字串 `redirectURI` 轉換成 ADAL 所需的 NSURL 類型。最後是設定 `correlationId`，這只是一個 UUID，可追蹤往返於用戶端和伺服器之間的呼叫。這有助於偵錯。

現在，我們要實際呼叫 ADAL，在此，呼叫將迴異於您先前使用 ADAL for iOS 時所看見的情況：

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

您在這裡可以看到呼叫相當簡單。

**SCOPES** - 是我們傳給伺服器的範圍，而在使用者登入時，我們想要向伺服器要求此範圍。在 B2C 預覽中，我們傳遞 client\_id。不過，這在未來將寫成 scopes。屆時會更新本文件。**addtionalScopes** - 這些是您可能想要用於應用程式的其他範圍。這將在之後使用。**clientId** - 您從入口網站取得的應用程式識別碼。**redirectURI** - 我們預期會回傳權杖的重新導向。**identifier** - 這是識別使用者的方式，可讓我們判斷快取中是否有可用的權杖，還是一律要向伺服器要求另一個權杖。您會看到這包含在名為 `ADUserIdentifier` 的類型中，我們可以指定要用來做為識別碼的項目。您應該使用使用者名稱。**promptBehavior** - 這已被取代，現在應該是 AD\_PROMPT\_ALWAYS。**extraQueryParameters** - 您想要以 URL 編碼格式傳遞給伺服器的任何額外參數。**policy** - 您叫用的原則。本逐步解說的後續重要部分。

您可以在 completionBlock 中看到我們傳遞 `ADAuthenticationResult`，其中包含我們的權杖和設定檔資訊 (如果呼叫成功)

使用上面的程式碼可以為您所提供的原則取得權杖。我們將使用此權杖來呼叫 API。

#### 建立對伺服器的工作呼叫

既然我們已經有權杖，我們需要將它提供給 API 進行授權。

如果您還記得，我們有三個方法需要實作：

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

`getTasksList` 會提供陣列，代表伺服器中的工作。`addTask` 和 `deleteTask` 會執行後續動作，如果成功執行，即會傳回 TRUE 或 FALSE。

讓我們先撰寫 `getTaskList`：

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

討論「工作」程式碼已超出本逐步解說的範圍，但您可能注意到一個有趣的地方：一個取用工作 URL 的 `craftRequest` 方法。我們利用這個方法，以我們收到的存取權杖，對伺服器建立要求。現在我們就來撰寫：

讓我們將下列程式碼加入至 'samplesWebAPIConnector.m' 檔案：

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

您可以看到這會取用 Web URI、在 HTTP 中以 `Bearer` 標頭加入權杖，然後傳回給我們。我們會呼叫 `getTokenClearingCache` API，乍看之下可能很奇怪，但我們只是使用這個呼叫從快取中取得權杖，並確定它仍然有效 (getToken* 呼叫會替我們詢問 ADAL)。我們在每次呼叫時都使用這段程式碼。現在讓我們回去撰寫其他的「工作」方法。

讓我們撰寫 `addTask`：

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

這會遵循相同模式，但引進另一個 (也是最後一個！) 需要實作的方法：`convertTaskToDictionary`，此方法會取用我們的陣列並轉換成字典物件，這樣就能更容易地隨著需要傳給伺服器的查詢參數而變化。此程式碼非常簡單：

```
// Here we have some converstion helpers that allow us to parse passed items in to dictionaries for URLEncoding later.

+(NSDictionary*) convertTaskToDictionary:(samplesTaskItem*)task
{
    NSMutableDictionary* dictionary = [[NSMutableDictionary alloc]init];
    
    if (task.itemName){
        [dictionary setValue:task.itemName forKey:@"task"];
    }
    
    return dictionary;
}

```

最後，讓我們撰寫 `deleteTask`：

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

### 將登出加入至應用程式。

最後一件要做的事就是實作應用程式的「登出」。這相當簡單。同樣在 `sampleWebApiConnector.m` 檔案內：

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

## 9\.執行範例應用程式

最後，在 xCode 中建置並執行應用程式。註冊或登入應用程式，並為登入的使用者建立工作。登出後，再以不同使用者重新登入，並為該使用者建立工作。

請注意每位使用者的工作如何儲存於 API，因為 API 會從它收到的存取權杖中擷取使用者的身分識別。

為了方便參考，您可以[在此處取得 .zip 格式](https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS/archive/complete.zip)的完整範例，或者從 GitHub 中複製：

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS```

## 後續步驟

您現在可以進入更進階的 B2C 主題。您可以嘗試：

[從 node.js Web 應用程式呼叫 node.js Web API >>]()

[自訂 B2C 應用程式的 UX >>]()

<!---HONumber=AcomDC_1203_2015-->