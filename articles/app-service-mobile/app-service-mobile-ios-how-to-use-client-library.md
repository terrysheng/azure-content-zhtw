<properties
	pageTitle="如何使用適用於 Azure Mobile Apps 的 iOS SDK"
	description="如何使用適用於 Azure Mobile Apps 的 iOS SDK"
	services="app-service\mobile"
	documentationCenter="ios"
	authors="krisragh"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="12/30/2015"
	ms.author="krisragh"/>

# 如何使用適用於 Azure Mobile Apps 的 iOS 用戶端程式庫

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]&nbsp;
 
[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

本指南說明如何使用最新的 [Azure Mobile Apps iOS SDK](https://go.microsoft.com/fwLink/?LinkID=266533&clcid=0x409) 執行一般案例。如果您是 Azure Mobile Apps 的新手，請先完成 [Azure Mobile Apps 快速入門]以建立後端、建立資料表及下載預先建置的 iOS Xcode 專案。在本指南中，我們會著重於用戶端 iOS SDK。若要深入了解後端的 .NET 伺服器端 SDK，請參閱[使用 .NET 後端](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)

## 參考文件

iOS 用戶端 SDK 的參考文件位於此處：[Azure Mobile Apps iOS 用戶端參考資料](http://azure.github.io/azure-mobile-services/iOS/v3/)。

##<a name="Setup"></a>設定和必要條件

本指南假設您已建立包含資料表的後端。本指南假設資料表的結構描述與這些教學課程中的資料表相同。本指南也假設您在程式碼中，參考了 `MicrosoftAzureMobile.framework` 並匯入了 `MicrosoftAzureMobile/MicrosoftAzureMobile.h`。

##<a name="create-client"></a>作法：建立用戶端

若要在專案中存取 Azure Mobile Apps 後端，請建立 `MSClient`。以應用程式 URL 取代 `AppUrl`。您可以將 `gatewayURLString` 和 `applicationKey` 留白。如果您設定驗證的閘道器，請將 `gatewayURLString` 填入閘道器 URL。

**Objective-C**：

```
MSClient *client = [MSClient clientWithApplicationURLString:@"AppUrl"];
```

**Swift**：

```
let client = MSClient(applicationURLString: "AppUrl")
```


##<a name="table-reference"></a>作法：建立資料表參考

若要存取或更新資料，請建立後端資料表的參考。將 `TodoItem` 取代為您的資料表名稱

**Objective-C**：

```
MSTable *table = [client tableWithName:@"TodoItem"];
```

**Swift**：

```
let table = client.tableWithName("TodoItem")
```


##<a name="querying"></a>作法：查詢資料

若要建立資料庫查詢，請查詢 `MSTable` 物件。下列查詢會取得 `TodoItem` 中的所有項目並記錄每個項目的文字。

**Objective-C**：

```
[table readWithCompletion:^(MSQueryResult *result, NSError *error) {
		if(error) { // error is nil if no error occured
				NSLog(@"ERROR %@", error);
		} else {
				for(NSDictionary *item in result.items) { // items is NSArray of records that match query
						NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
				}
		}
}];
```

**Swift**：

```
table.readWithCompletion({(result, error) -> Void in
    if error != nil { // error is nil if no error occured
        NSLog("ERROR %@", error!)
    } else {
        for item in (result?.items)! {
            NSLog("Todo Item: %@", item["text"] as! String)
        }
    }
})
```

##<a name="filtering"></a>作法：篩選傳回的資料

若要篩選結果，有許多可用的選項。

若要使用述詞篩選，請使用 `NSPredicate` 和 `readWithPredicate`。下列篩選器傳回的資料只尋找未完成的待辦事項。

**Objective-C**：

```
// Create a predicate that finds items where complete is false
NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];
// Query the TodoItem table 
[table readWithPredicate:predicate completion:^(MSQueryResult *result, NSError *error) {
		if(error) {
				NSLog(@"ERROR %@", error);
		} else {
				for(NSDictionary *item in result.items) {
						NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
				}
		}
}];
```

**Swift**：

```
// Create a predicate that finds items where complete is false
let predicate =  NSPredicate(format:"complete == NO")
// Query the TodoItem table 
table.readWithPredicate(predicate, completion: { (result, error) -> Void in
    if error != nil {
        NSLog("ERROR %@", error!)
    } else {
        for item in (result?.items)! {
            NSLog("Todo Item: %@", item["text"] as! String)
        }
    }
})
```

##<a name="query-object"></a>作法：使用 MSQuery

若要執行複雜的查詢 (包括排序和分頁)，請使用述詞直接建立 `MSQuery` 物件：

**Objective-C**：

```
MSQuery *query = [table query];
MSQuery *query = [table queryWithPredicate: [NSPredicate predicateWithFormat:@"complete == NO"]];
```

**Swift**：

```
let query = table.query()
let query = table.queryWithPredicate(NSPredicate(format:"complete == NO"))
```

`MSQuery` 可讓您控制下列幾種查詢行為。在其上呼叫 `readWithCompletion` 執行 `MSQuery` 查詢，如下一個範例所示。* 指定結果的順序 * 限制要傳回的欄位 * 限制要傳回的記錄數 * 指定回應中的總計數 * 指定要求中的自訂查詢字串參數 * 套用額外函數


## <a name="sorting"></a>做法：使用 MSQuery 排序資料

我們來看一下範例如何排序結果。若要先按照 `text` 欄位遞增排序，然後按照 `completion` 欄位遞減排序，請如下叫用 `MSQuery`：

**Objective-C**：

```
[query orderByAscending:@"text"];
[query orderByDescending:@"complete"];
[query readWithCompletion:^(MSQueryResult *result, NSError *error) {
		if(error) {
				NSLog(@"ERROR %@", error);
		} else {
				for(NSDictionary *item in result.items) {
						NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
				}
		}
}];
```

**Swift**：

```        
query.orderByAscending("text")
query.orderByDescending("complete")
query.readWithCompletion { (result, error) -> Void in
    if error != nil {
        NSLog("ERROR %@", error!)
    } else {
        for item in (result?.items)! {
            NSLog("Todo Item: %@", item["text"] as! String)
        }
    }
}
```


## <a name="selecting"></a><a name="parameters"></a>作法：使用 MSQuery 限制欄位和展開查詢字串參數

若要限制在查詢中傳回的欄位，請在 **selectFields** 屬性中指定欄位的名稱。這僅會傳回文字和已完成欄位：

**Objective-C**：

```
query.selectFields = @[@"text", @"complete"];
```

**Swift**：

```
query.selectFields = ["text", "complete"]
```

若要在伺服器要求中包含額外的查詢字串參數 (例如有某個自訂的伺服器端指令碼使用這些參數)，請如下填入 `query.parameters`：

**Objective-C**：

```
query.parameters = @{
	@"myKey1" : @"value1",
	@"myKey2" : @"value2",
};
```

**Swift**：

```
query.parameters = ["myKey1": "value1", "myKey2": "value2"]
```

##<a name="inserting"></a>作法：插入資料

若要插入新的資料表資料列，請建立新的 `NSDictionary` 並叫用 `table insert`。行動服務會根據 `NSDictionary` 自動產生新的資料欄 (如果未啟用[動態結構描述])。

如果未提供 `id`，則後端會自動產生新的唯一識別碼。提供您自己的 `id`，以使用電子郵件地址、使用者名稱或您自己自訂的值作為識別碼。提供您自己的識別碼可以讓聯結和商務導向的資料庫邏輯變得更容易。

`result` 包含所插入的新項目，視您的伺服器邏輯而定，相較於所傳遞到伺服器的項目，其可能有其他或已修改的資料。

**Objective-C**：

```
NSDictionary *newItem = @{@"id": @"custom-id", @"text": @"my new item", @"complete" : @NO};
[table insert:newItem completion:^(NSDictionary *result, NSError *error) {
	if(error) {
		NSLog(@"ERROR %@", error);
	} else {
		NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
	}
}];
```

**Swift**：

```
let newItem = ["id": "custom-id", "text": "my new item", "complete": false]
table.insert(newItem) { (result, error) -> Void in
    if error != nil {
        NSLog("ERROR %@", error!)
    } else {
        NSLog("Todo Item: %@", result!["text"] as! String)
    }
}
```

##<a name="modifying"></a>作法：修改資料

若要更新現有的資料列，請修改項目並呼叫 `update`：

**Objective-C**：

```
NSMutableDictionary *newItem = [oldItem mutableCopy]; // oldItem is NSDictionary
[newItem setValue:@"Updated text" forKey:@"text"];
[table update:newItem completion:^(NSDictionary *result, NSError *error) {
	if(error) {
		NSLog(@"ERROR %@", error);
	} else {
		NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
	}
}];
```

**Swift**：

```
let newItem = oldItem.mutableCopy() as! NSMutableDictionary // oldItem is NSDictionary
newerItem["text"] = "Updated text"
table.update(newerItem  as [NSObject : AnyObject]) { (result, error) -> Void in
    if error != nil {
        NSLog("ERROR %@", error!)
    } else {
        NSLog("Todo Item: %@", result!["text"] as! String)
    }
}
```

或者，提供資料列識別碼和更新的欄位：

**Objective-C**：

```
[table update:@{@"id":@"custom-id", @"text":"my EDITED item"} completion:^(NSDictionary *result, NSError *error) {
	if(error) {
		NSLog(@"ERROR %@", error);
	} else {
		NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
	}
}];
```

**Swift**：

```
table.update(["id": "custom-id", "text": "my EDITED item"]) { (result, error) -> Void in
    if error != nil {
        NSLog("ERROR %@", error!)
    } else {
        NSLog("Todo Item: %@", result!["text"] as! String)
    }
    
}
```

進行更新時，至少必須設定 `id` 屬性。

##<a name="deleting"></a>作法：刪除資料

若要刪除項目，請叫用 `delete` 搭配項目：

**Objective-C**：

```
[table delete:item completion:^(id itemId, NSError *error) {
	if(error) {
		NSLog(@"ERROR %@", error);
	} else {
		NSLog(@"Todo Item ID: %@", itemId);
	}
}];
```

**Swift**：

```
table.delete(item as [NSObject : AnyObject]) { (itemId, error) -> Void in
	if error != nil {
		NSLog("ERROR %@", error!)
	} else {
		NSLog("Todo Item ID: %@", itemId! as! String)
	}
}
```

或者，提供資料列識別碼來進行刪除：

**Objective-C**：

```
[table deleteWithId:@"37BBF396-11F0-4B39-85C8-B319C729AF6D" completion:^(id itemId, NSError *error) {
	if(error) {
		NSLog(@"ERROR %@", error);
	} else {
		NSLog(@"Todo Item ID: %@", itemId);
	}
}];   
```

**Swift**：

```
table.deleteWithId("37BBF396-11F0-4B39-85C8-B319C729AF6D") { (itemId, error) -> Void in
        if error != nil {
        	NSLog("ERROR %@", error!)
        } else {
        	NSLog("Todo Item ID: %@", itemId! as! String)
        }
}
```

進行刪除時，至少必須設定 `id` 屬性。

##<a name="templates"></a>作法：註冊推送範本以傳送跨平台通知

若要註冊範本，只要在用戶端應用程式中透過 **client.push registerDeviceToken** 方法傳遞範本即可。

**Objective-C**：

```
[client.push registerDeviceToken:deviceToken template:iOSTemplate completion:^(NSError *error) {
	if(error) {
		NSLog(@"ERROR %@", error);
	}
}];
```

**Swift**：

```
client.push!.registerDeviceToken(deviceToken, template: iOSTemplate, completion: { (error) -> Void in
            if error != nil {
                NSLog("ERROR %@", error!)
            }
        })
```

您的範本類型將為 NSDictionary，並且可能包含多個下列格式的範本：

**Objective-C**：

```
NSDictionary *iOSTemplate = @{ @"templateName": @{ @"body": @{ @"aps": @{ @"alert": @"$(message)" } } } };
```

**Swift**：

```
let iOSTemplate: [NSObject : AnyObject] = ["templateName": ["body": ["aps": ["alert": "$(message)"]]]]
```

請注意，所有的標記都將因安全性而移除。若要在安裝中將標記新增至安裝或範本，請參閱[使用適用於 Azure Mobile Apps 的 .NET 後端伺服器 SDK](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#tags)。

若要利用這些已註冊的範本傳送通知，請使用[通知中樞 API](https://msdn.microsoft.com/library/azure/dn495101.aspx)。

##<a name="errors"></a>作法：處理錯誤

呼叫行動服務時，completion 區塊會包含 `NSError` 參數。發生錯誤時，此參數便會傳回非 Nil。您應檢查程式碼中的此參數，並視需要處理錯誤，如上述的程式碼片段所示。

[`<WindowsAzureMobileServices/MSError.h>`](https://github.com/Azure/azure-mobile-services/blob/master/sdk/iOS/src/MSError.h) 檔案定義 `MSErrorResponseKey`、`MSErrorRequestKey` 和 `MSErrorServerItemKey` 常數以取得更多有關錯誤的資料，其可透過如下方式取得：

**Objective-C**：

```
NSDictionary *serverItem = [error.userInfo objectForKey:MSErrorServerItemKey];
```

**Swift**：

```
let serverItem = error?.userInfo[MSErrorServerItemKey]
```

此外，檔案也定義每個錯誤代碼的常數，您可以透過如下所示方式來使用：

**Objective-C**：

```
if (error.code == MSErrorPreconditionFailed) {
```

**Swift**：

```
if (error?.code == MSErrorPreconditionFailed) {
```

## <a name="adal"></a>做法：使用 Active Directory Authentication Library 驗證使用者

您可以使用 Active Directory Authentication Library (ADAL)，利用 Azure Active Directory 將使用者登入應用程式。這樣通常會比使用 `loginAsync()` 方法還適合，因為它提供更原生的 UX 風格，並允許其他自訂。

1. 遵循[如何設定 Active Directory 登入的 App Service](app-service-mobile-how-to-configure-active-directory-authentication.md) 教學課程，針對 AAD 登入設定您的行動應用程式後端。請務必完成註冊原生用戶端應用程式的選擇性步驟。對於 iOS，建議 (但非必要) 重新導向 URI 的格式為 `<app-scheme>://<bundle-id>`。如需詳細資訊，請參閱 [ADAL iOS 快速入門](active-directory-devquickstarts-ios.md#em1-determine-what-your-redirect-uri-will-be-for-iosem)。

2. 使用 Cocoapods 安裝 ADAL。編輯您的 podfile 以納入下列內容，將 **YOUR-PROJECT** 取代為 Xcode 專案的名稱：

	source 'https://github.com/CocoaPods/Specs.git' link\_with ['YOUR-PROJECT'] xcodeproj 'YOUR-PROJECT'
	
	pod 'ADALiOS'

3. 使用終端機，從包含您的專案的目錄執行 `pod install`，然後再開啟產生的 Xcode 工作區 (不是專案)。

4. 根據您使用的語言，將下列程式碼新增至您的應用程式。在每個程式碼中，進行下列取代：

* 將 **INSERT-AUTHORITY-HERE** 取代為您佈建應用程式的租用戶名稱。格式應該是 https://login.windows.net/contoso.onmicrosoft.com。此值可從 [Azure 傳統入口網站] 複製到 Azure Active Directory 的 [網域] 索引標籤以外。

* 將 **INSERT-RESOURCE-ID-HERE** 取代為您的行動應用程式後端的用戶端識別碼。您可以從入口網站中 [Azure Active Directory 設定] 底下的 [進階] 索引標籤取得。

* 將 **INSERT-CLIENT-ID-HERE** 取代為您從原生用戶端應用程式中複製的用戶端識別碼。

* 使用 HTTPS 配置，將 **INSERT-REDIRECT-URI-HERE** 取代為您的網站的 _/.auth/login/done_ 端點。此值應與 \__https://contoso.azurewebsites.net/.auth/login/done_ 類似。

**Objective-C**：

	#import <ADALiOS/ADAuthenticationContext.h>
	#import <ADALiOS/ADAuthenticationSettings.h>
	// ...
	- (void) authenticate:(UIViewController*) parent
	           completion:(void (^) (MSUser*, NSError*))completionBlock;
	{
	    NSString *authority = @"INSERT-AUTHORITY-HERE";
	    NSString *resourceId = @"INSERT-RESOURCE-ID-HERE";
	    NSString *clientId = @"INSERT-CLIENT-ID-HERE";
	    NSURL *redirectUri = [[NSURL alloc]initWithString:@"INSERT-REDIRECT-URI-HERE"];
	    ADAuthenticationError *error;
	    ADAuthenticationContext authContext = [ADAuthenticationContext authenticationContextWithAuthority:authority error:&error];
	    authContext.parentController = parent;
	    [ADAuthenticationSettings sharedInstance].enableFullScreen = YES;
	    [authContext acquireTokenWithResource:resourceId
	                                 clientId:clientId
	                              redirectUri:redirectUri
	                          completionBlock:^(ADAuthenticationResult *result) {
	                              if (result.status != AD_SUCCEEDED)
	                              {
	                                  completionBlock(nil, result.error);;
	                              }
	                              else
	                              {
	                                  NSDictionary *payload = @{
	                                                            @"access_token" : result.tokenCacheStoreItem.accessToken
	                                                            };
	                                  [client loginWithProvider:@"aad" token:payload completion:completionBlock];
	                              }
	                          }];
	}


**Swift**：

	// add the following imports to your bridging header:
	//     #import <ADALiOS/ADAuthenticationContext.h>
	//     #import <ADALiOS/ADAuthenticationSettings.h>
	
	func authenticate(parent:UIViewController, completion: (MSUser?, NSError?) -> Void) {
		let authority = "INSERT-AUTHORITY-HERE"
		let resourceId = "INSERT-RESOURCE-ID-HERE"
		let clientId = "INSERT-CLIENT-ID-HERE"
		let redirectUri = NSURL(string: "INSERT-REDIRECT-URI-HERE")
		var error: AutoreleasingUnsafeMutablePointer<ADAuthenticationError?> = nil
		let authContext = ADAuthenticationContext(authority: authority, error: error)
		authContext.parentController = parent
		ADAuthenticationSettings.sharedInstance().enableFullScreen = true;
		authContext.acquireTokenWithResource(resourceId, clientId: clientId, redirectUri: redirectUri, completionBlock: { (result) -> Void in
			if result.status != AD_SUCCEEDED {
				completion(nil, result.error)
			}
			else {
				let payload:[String:String] = ["access_token":result.tokenCacheStoreItem.accessToken]
				client.loginWithProvider("aad", token: payload, completion: completion)
			}
		})
	}


<!-- Anchors. -->

[What is Mobile Services]: #what-is
[Concepts]: #concepts
[Setup and Prerequisites]: #Setup
[How to: Create the Mobile Services client]: #create-client
[How to: Create a table reference]: #table-reference
[How to: Query data from a mobile service]: #querying
[Filter returned data]: #filtering
[Sort returned data]: #sorting
[Return data in pages]: #paging
[Select specific columns]: #selecting
[How to: Bind data to the user interface]: #binding
[How to: Insert data into a mobile service]: #inserting
[How to: Modify data in a mobile service]: #modifying
[How to: Authenticate users]: #authentication
[Cache authentication tokens]: #caching-tokens
[How to: Upload images and large files]: #blobs
[How to: Handle errors]: #errors
[How to: Design unit tests]: #unit-testing
[How to: Customize the client]: #customizing
[Customize request headers]: #custom-headers
[Customize data type serialization]: #custom-serialization
[Next Steps]: #next-steps
[How to: Use MSQuery]: #query-object

<!-- Images. -->

<!-- URLs. -->
[Azure Mobile Apps 快速入門]: app-service-mobile-ios-get-started.md

[Add Mobile Services to Existing App]: /develop/mobile/tutorials/get-started-data
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Validate and modify data in Mobile Services by using server scripts]: /develop/mobile/tutorials/validate-modify-and-augment-data-ios
[Mobile Services SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Authentication]: /develop/mobile/tutorials/get-started-with-users-ios
[iOS SDK]: https://developer.apple.com/xcode

[Handling Expired Tokens]: http://go.microsoft.com/fwlink/p/?LinkId=301955
[Live Connect SDK]: http://go.microsoft.com/fwlink/p/?LinkId=301960
[Permissions]: http://msdn.microsoft.com/library/windowsazure/jj193161.aspx
[Service-side Authorization]: mobile-services-javascript-backend-service-side-authorization.md
[Use scripts to authorize users]: /develop/mobile/tutorials/authorize-users-in-scripts-ios
[動態結構描述]: http://go.microsoft.com/fwlink/p/?LinkId=296271
[How to: access custom parameters]: /develop/mobile/how-to-guides/work-with-server-scripts#access-headers
[Create a table]: http://msdn.microsoft.com/library/windowsazure/jj193162.aspx
[NSDictionary object]: http://go.microsoft.com/fwlink/p/?LinkId=301965
[ASCII control codes C0 and C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[CLI to manage Mobile Services tables]: ../virtual-machines-command-line-tools.md#Mobile_Tables
[Conflict-Handler]: mobile-services-ios-handling-conflicts-offline-data.md#add-conflict-handling

<!---HONumber=AcomDC_0128_2016-->