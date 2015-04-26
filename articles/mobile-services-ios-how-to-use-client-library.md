<properties 
	pageTitle="如何使用 iOS 用戶端程式庫 - Azure 行動服務" 
	description="了解如何使用適用於 Azure 行動服務的 iOS 用戶端程式庫。" 
	services="" 
	documentationCenter="ios" 
	authors="krisragh" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="objective-c" 
	ms.topic="article" 
	ms.date="10/10/2014" 
	ms.author="krisragh"/>




# 如何使用行動服務的 iOS 用戶端程式庫
<div class="dev-center-tutorial-selector sublanding">
  <a href="/zh-tw/develop/mobile/how-to-guides/work-with-net-client-library/" title=".NET Framework">.NET Framework</a><a href="/zh-tw/develop/mobile/how-to-guides/work-with-html-js-client/" title="HTML/JavaScript">HTML/JavaScript</a><a href="/zh-tw/develop/mobile/how-to-guides/work-with-ios-client-library/" title="iOS" class="current">iOS</a><a href="/zh-tw/develop/mobile/how-to-guides/work-with-android-client-library/" title="Android">Android</a><a href="/zh-tw/develop/mobile/how-to-guides/work-with-xamarin-client-library/" title="Xamarin">Xamarin</a>
</div>

本指南說明如何使用適用於 Azure 行動服務的 iOS 用戶端執行一般案例。相關範例是以 objective-C 撰寫，並需要[行動服務 SDK]。本教學課程也需要 [iOS SDK]。涵蓋的案例包括查詢資料、插入、更新和刪除資料、驗證使用者和處理錯誤。如果您不熟悉行動服務，您應考慮先完成[行動服務快速入門][開始使用行動服務]。快速入門教學課程可協助您設定帳戶，並建立您的第一個行動服務。

## 目錄

- [什麼是行動服務][]
- [概念][]
- [設定和必要條件][]
- [做法：建立行動服務用戶端][]
- [做法：建立資料表參考][]
- [做法：查詢行動服務中的資料][]
	- [篩選傳回資料]
    - [使用 MSQuery 物件][作法：使用 MSQuery]
	- [選取特定資料行]
- [做法：將資料插入行動服務]
- [做法：修改行動服務中的資料]
- [做法：將資料繫結到使用者介面]
- [做法：驗證使用者]
- [做法：處理錯誤]

<!--- [做法：設計單位測試]
- [做法：自訂用戶端]
	- [自訂要求標頭]
	- [自訂資料型別序列化]
- [後續步驟][]-->

[AZURE.INCLUDE [mobile-services-concepts](../includes/mobile-services-concepts.md)]

##<a name="Setup"></a>設定和必要條件

本指南假設您已建立包含資料表的行動服務。如需詳細資訊，請參閱[建立資料表]，或重複使用在[開始使用行動服務]教學課程中建立的  `ToDoItem` 資料表。本主題中的範例將使用名為  `ToDoItem` 的資料表，其中包含下列資料行：

+ `id`
+ `text`
+ `complete`
+ `duration`


如果這是您第一次建立 iOS 應用程式，請務必在應用程式的 [**Link Binary With Libraries**](https://developer.apple.com/library/ios/recipes/xcode_help-project_editor/Articles/AddingaLibrarytoaTarget.html) 設定中新增  `WindowsAzureMobileServices.framework`。在此步驟中，按一下 [新增其他...]、導覽至下載的 Windows Azure 行動服務 SDK 的所在位置，然後選取該位置。

另外，您必須在適當檔案或在應用程式的 .pch 檔案中新增下列參考。

	#import <WindowsAzureMobileServices/WindowsAzureMobileServices.h>

<h2><a name="create-client"></a>做法：建立行動服務用戶端</h2>

下列程式碼將會建立可用來存取行動服務的行動服務用戶端物件。

	MSClient *client = [MSClient clientWithApplicationURLString:@"MobileServiceUrl" applicationKey:@"AppKey"]

在上述程式碼中，以您行動服務的行動服務 URL 和應用程式金鑰取代  `MobileServiceUrl` 和  `AppKey`。若要確認行動服務的這些設定，請在 Azure 管理入口網站中選取您的行動服務，然後按一下 [**儀表板**]。

您也可以根據 **NSURL** 物件 (此服務的 URL) 建立用戶端，如下所示：

	MSClient *client = [MSClient clientWithApplicationURL:[NSURL URLWithString:@"MobileServiceUrl"] applicationKey:@"AppKey"];

<h2><a name="table-reference"></a>做法：建立資料表參考</h2>

在存取行動服務中的資料之前，您必須取得想要在其中查詢、更新或刪除項目的資料表參考。在下列範例中，資料表名稱為  `ToDoItem`：

	MSTable *table = [client tableWithName:@"ToDoItem"];


<h2><a name="querying"></a>做法：查詢行動服務中的資料</h2>

有了 MSTable 物件之後，您便可以建立查詢。下列簡單查詢將取得 ToDoItem 資料表中的所有項目。

	[table readWithCompletion:^(NSArray *items, NSInteger totalCount, NSError *error) {
		if(error) {
			NSLog(@"ERROR %@", error);
		} else {
			for(NSDictionary *item in items) {
				NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
			}
		}
	}];

請注意，在此案例中，我們僅會將工作文字寫入記錄。

回呼中將會提供下列參數：

+ _items_：共有 **NSArray** 筆符合查詢的記錄。
+ _totalCount_：所有查詢頁面中的項目總計數，而不只是目前頁面中所傳回的這些項目。除非您在要求中明確要求總計數，否則此值會被設為 -1。如需詳細資訊，請參閱[以分頁方式傳回資料]。
+ _error_：發生的任何錯誤；若沒有錯誤，則為  `nil`。

### <a name="filtering"></a>做法：篩選傳回資料

若要篩選結果，您有許多可用選項。

最常見的情況是使用 NSPredicate 來篩選結果。

	[table readWithPredicate:(NSPredicate *)predicate completion:(MSReadQueryBlock)completion];

下列述詞只會傳回 ToDoItem 資料表中的未完成項目：

	NSPredicate *predicate = [NSPredicate predicateWithFormat:@"complete == NO"];
	[table readWithPredicate:predicate completion:^(NSArray *items, NSInteger totalCount, NSError *error) {
		if(error) {
			NSLog(@"ERROR %@", error);
		} else {
			for(NSDictionary *item in items) {
				NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
			}
		}
	}];

您可使用記錄識別碼來擷取單一記錄。

	[table readWithId:[@"37BBF396-11F0-4B39-85C8-B319C729AF6D"] completion:^(NSDictionary *item, NSError *error) {
		if(error) {
			NSLog(@"ERROR %@", error);
		} else {
				NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
		}
	}];

請注意，在此情況下，回呼參數會稍有不同。您可以只取回一筆記錄，而非取回結果陣列與選擇性計數。

### <a name="query-object"></a>使用 MSQuery 物件

當您需要比篩選資料列更為複雜的查詢時 (例如，變更結果的排序順序或限制取回的資料記錄數)，請使用 **MSQuery** 物件。下列兩個範例說明如何建立 MSQuery 物件執行個體：

    MSQuery *query = [table query];

    MSQuery *query = [table queryWithPredicate: [NSPredicate predicateWithFormat:@"complete == NO"]];

MSQuery 物件可讓您控制下列查詢行為：

* 指定傳回的結果順序。
* 限制要傳回的欄位。
* 限制要傳回的記錄數。
* 指定是否要在回應中包括總計數。
* 在要求中指定自訂查詢字串參數。

透過套用一或多個函數，您可以進一步定義查詢。在定義查詢之後，您可以呼叫 **readWithCompletion** 函數來執行查詢。

#### <a name="sorting"></a>排序傳回的資料

下列函數可用來指定用來排序的欄位：

	-(void) orderByAscending:(NSString *)field
	-(void) orderByDescending:(NSString *)field

此查詢會首先按持續時間，然後按工作是否已完成來排序結果：

	[query orderByAscending:@"duration"];
	[query orderByAscending:@"complete"];
	[query readWithCompletion:^(NSArray *items, NSInteger totalCount, NSError *error) {
		//code to parse results here
	}];

#### <a name="paging"></a>以分頁方式傳回資料

行動服務會限制單一回應中可傳回的記錄數。若要控制顯示給使用者的記錄數，您必須實作分頁系統。您可以使用 **MSQuery** 物件的下列三個屬性來執行分頁。

+	`BOOL includeTotalCount`
+	`NSInteger fetchLimit`
+	`NSInteger fetchOffset`


在下列範例中，簡單函數會向伺服器要求 20 筆記錄，然後將這些記錄附加到先前載入記錄的本機集合：

	- (bool) loadResults() {
		MSQuery *query = [table query];

		query.includeTotalCount = YES;
		query.fetchLimit = 20;
		query.fetchOffset = self.loadedIte
	ms.count;

		[query readWithCompletion:^(NSArray *items, NSInteger totalCount, NSError *error) {
			if(!error) {
				// Add the items to our local copy
				[self.loadedItems addObjectsFromArray:items];

				// Set a flag to keep track if there are any additional records we need to load
				self.moreResults = (self.loadedIte
	ms.count < totalCount);
			}
		}];
	}

#### <a name="selecting"></a>限制傳回的欄位

若要限制從查詢中傳回哪些欄位，請直接在 **selectFields** 屬性中指定所需的欄位名稱。下列範例僅會傳回文字和已完成欄位：

	query.selectFields = @[@"text", @"completed"];

#### <a name="parameters"></a>指定其他 querystring 參數

用戶端程式庫允許在對伺服器的要求中包含其他 querystring 參數。您的伺服器端指令碼可能會要求這些參數。下列範例會在要求中新增兩個 querystring 參數：

	query.parameters = @{
		@"myKey1" : @"value1",
		@"myKey2" : @"value2",
	};

這些參數會附加至查詢 URI 作為 `myKey1=value1&myKey2=value2`。
如需詳細資訊，請參閱[做法：存取自訂參數]。

<h2><a name="inserting"></a>做法：將資料插入行動服務</h2>

若要將新資料列插入資料表中，您可以建立新的 [NSDictionary 物件]，並將該物件傳遞至 insert 函數。下列程式碼會將新的 todo 項目插入資料表中：

	NSDictionary *newItem = @{@"text": @"my new item", @"complete" : @NO};
	[table insert:newItem completion:^(NSDictionary *result, NSError *error) {
		// The result contains the new item that was inserted,
		// depending on your server scripts it may have additional or modified
		// data compared to what was passed to the server.
	}];

行動服務支援以唯一的自訂字串值做為資料表識別碼。這可讓應用程式在行動服務資料表的識別碼資料欄中使用自訂的值，例如電子郵件地址或使用者名稱。例如，如果您想要根據電子郵件地址來識別每筆記錄，您可以使用下列 JSON 物件。

	NSDictionary *newItem = @{@"id": @"myemail@emaildomain.com", @"text": @"my new item", @"complete" : @NO};
	[table insert:newItem completion:^(NSDictionary *result, NSError *error) {
		// The result contains the new item that was inserted,
		// depending on your server scripts it may have additional or modified
		// data compared to what was passed to the server.
	}];

如果將新記錄插入至資料表時未提供字串識別碼值，則行動服務會產生唯一值做為識別碼。

支援字串識別碼對開發人員而言有下列好處

+ 不需要往返存取資料庫就能產生識別碼。
+ 輕鬆合併不同資料表或資料庫的記錄。
+ 識別碼值與應用程式邏輯的整合更理想。

您也可以使用伺服器指令碼來設定識別碼值。下列指令碼範例會產生自訂 GUID 並指派給新記錄的識別碼。這類似於您未傳入任何值做為記錄的識別碼時由行動服務產生的識別碼值。

	//Example of generating an id. This is not required since Mobile Services
	//will generate an id if one is not passed in.
	item.id = item.id || newGuid();
	request.execute();

	function newGuid() {
		var pad4 = function(str) { return "0000".substring(str.length) + str; };
		var hex4 = function () { return pad4(Math.floor(Math.random() * 0x10000 /* 65536 */ ).toString(16)); };
		return (hex4() + hex4() + "-" + hex4() + "-" + hex4() + "-" + hex4() + "-" + hex4() + hex4() + hex4());
	}


如果應用程式提供識別碼的值，則行動服務會原封不動地儲存它。這包括開頭或結尾的空白字元。值中的空白字元將不會被去除。

 `id` 的值必須是唯一的，且不可包含下列字集中的字元：

+ 控制字元：[0x0000-0x001F] 和 [0x007F-0x009F]。如需詳細資訊，請參閱 [ASCII 控制碼 C0 和 C1]。
+  可以列印的字元：**"**(0x0022)、**\+** (0x002B)、**/** (0x002F)、**?**(0x003F)、**\\** (0x005C)、**`** (0x0060)
+  識別碼 "." 和 ".."

另外，您也可以在資料表中使用整數識別碼。若要使用整數識別碼，您必須使用 `--integerId` 選項，以  `mobile table create` 命令建立資料表。此命令需要在 Azure 的命令列介面 (CLI) 中執行。如需關於使用 CLI 的詳細資訊，請參閱[使用 CLI 管理行動服務資料表]。

啟用動態結構描述時，行動服務會根據插入或更新要求中的物件欄位自動產生新資料行。如需詳細資訊，請參閱[動態結構描述]。

<h2><a name="modifying"></a>做法：修改行動服務中的資料</h2>

您可以修改先前查詢所傳回的項目，然後呼叫 **update** 函數，以更新現有物件。

	NSMutableDictionary *item = [self.results.item objectAtIndex:0];
	[item setObject:@YES forKey:@"complete"];
	[table update:item completion:^(NSDictionary *item, NSError *error) {
		//handle errors or any additional logic as needed
	}];

進行更新時，您只需要提供更新的欄位以及資料列識別碼，如下列範例所示：

	[table update:@{@"id" : @"37BBF396-11F0-4B39-85C8-B319C729AF6D", @"Complete": @YES} completion:^(NSDictionary *item, NSError *error) {
		//handle errors or any additional logic as needed
	}];


若要刪除資料表中的項目，您只需傳遞項目至 delete 方法，如下所示：

	[table delete:item completion:^(id itemId, NSError *error) {
		//handle errors or any additional logic as needed
	}];

您也可以直接使用其識別碼來刪除記錄，如下列範例所示：

	[table deleteWithId:@"37BBF396-11F0-4B39-85C8-B319C729AF6D" completion:^(id itemId, NSError *error) {
		//handle errors or any additional logic as needed
	}];

請注意，進行更新與刪除時，至少必須設定  `id` 屬性。

<h2><a name="authentication"></a>做法：驗證使用者</h2>

行動服務可讓您使用下列識別提供者來驗證使用者：

- Facebook
- Google
- Microsoft 帳戶
- Twitter
- Azure Active Directory

如需設定身分識別提供者的詳細資訊，請參閱[開始使用驗證]。

行動服務支援下列兩個驗證工作流程：

- 在伺服器管理的登入中，行動服務會代表您的應用程式管理登入程序。用戶端程式庫會顯示提供者特定登入頁面，而行動服務會使用選擇的提供者進行驗證工作。

- 在用戶端管理的登入中，應用程式必須向識別提供者要求權杖，然後向行動服務呈現此權杖以進行驗證。

驗證成功時，系統會傳回包含指定使用者識別碼值和驗證權杖的使用者物件。您可以在伺服器指令碼中使用此使用者識別碼，以驗證或修改要求。如需詳細資訊，請參閱[以指令碼驗證使用者]。權杖本身可以安全地進行快取，以供後續登入使用。

您也可以在資料表上設定權限，限制只有經驗證的使用者可以存取特定作業。如需詳細資訊，請參閱[權限]。

### 伺服器管理的登入

以下是如何使用 Microsoft 帳戶登入的範例。您可以在控制站的 ViewDidLoad 中呼叫或從 UIButton 中手動觸發此程式碼。這會顯示可用來登入識別提供者的標準 UI。

	[client loginWithProvider:@"MicrosoftAccount" controller:self animated:YES
		completion:^(MSUser *user, NSError *error) {
			NSString *msg;
			if(error) {
				msg = [@"An error occured: " stringByAppendingString:error.description];
			} else {
				msg = [@"You are logged in as " stringByAppendingString:user.userId];
			}

			UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Login"
								  message:msg
								  delegate:nil
								  cancelButtonTitle:@"OK"
								  otherButtonTitles: nil];
			[alert show];
	}];

注意：如果您使用 Microsoft 帳戶以外的身分識別提供者，請將要傳遞至上述登入方法的值變更為下列其中一個值： `facebook`、 `twitter`、 `google` 或  `windowsazureactivedirectory`。

您也可以取得 MSLoginController 的參考，並使用下列程式碼自行顯示此參考：

	-(MSLoginController *)loginViewControllerWithProvider:(NSString *)provider completion:(MSClientLoginBlock)completion;

### 用戶端管理的登入 (單一登入)

在有些情況下，有可能會在行動服務用戶端外部完成登入程序。這樣做的原因可能是為了啟用單一登入功能，或您的應用程式必須直接連絡識別提供者以取得使用者資訊。在這些情況下，您可以透過提供從支援的識別提供者獨立取得的權杖來登入行動服務。

下列範例會使用 [Live Connect SDK] 來啟用 iOS 應用程式的單一登入。

	[client loginWithProvider:@"microsoftaccount"
		token:@{@"authenticationToken" : self.liveClient.session.authenticationToken}
		completion:^(MSUser *user, NSError *error) {
			self.loggedInUser = user;
			NSString *msg = [@"You are logged in as " stringByAppendingString:user.userId];
			UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Login"
				message:msg
				delegate:nil
				cancelButtonTitle:@"OK"
				otherButtonTitles: nil];
			[alert show];
	}];

此程式碼假設您先前已在控制站中建立名為  `liveClient` 的 **LiveConnectClient** 執行個體，且使用者已登入。

###<a name="caching-tokens"></a>做法：快取驗證權杖

若要避免使用者在每次使用/執行應用程式時都必須進行驗證，您可以在使用者登入後快取目前使用者識別。接著您可以使用此資訊，來直接建立使用者並略過登入程序。若要這樣做，您必須在本機上儲存使用者識別碼和驗證權杖。在下列範例中，會在 [KeyChain] 中安全地快取權杖：

	- (NSMutableDictionary *) createKeyChainQueryWithClient:(MSClient *)client andIsSearch:(bool)isSearch
	{
		NSMutableDictionary *query = [[NSMutableDictionary alloc] init];
		[query setObject:(__bridge id)kSecClassInternetPassword forKey:(__bridge id)(kSecClass)];
		[query setObject:client.applicationURL.absoluteString forKey:(__bridge id)(kSecAttrServer)];

		if(isSearch) {
			// Use the proper search constants, return only the attributes of the first match.
			[query setObject:(__bridge id)kSecMatchLimitOne forKey:(__bridge id)kSecMatchLimit];
			[query setObject:(id)kCFBooleanTrue forKey:(__bridge id)kSecReturnAttributes];
			[query setObject:(id)kCFBooleanTrue forKey:(__bridge id)kSecReturnData];
		}

		return query;
	}

	- (IBAction)loginUser:(id)sender {
		NSMutableDictionary *query = [self createKeyChainQueryWithClient:self.todoService.client andIsSearch:YES];
		CFDictionaryRef cfresult;

		OSStatus status = SecItemCopyMatching((__bridge CFDictionaryRef)query, (CFTypeRef *)&cfresult);
		if (status == noErr) {
			NSDictionary * result = (__bridge_transfer NSDictionary*) cfresult;

			//create an MSUser object
			MSUser *user = [[MSUser alloc] initWithUserId:[result objectForKey:(__bridge id)(kSecAttrAccount)]];
			NSData *data = [result objectForKey:(__bridge id)(kSecValueData)];
			user.mobileServiceAuthenticationToken = [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding];
			[self.todoService.client setCurrentUser:user];

		} else if (status == errSecItemNotFound) {
			//we need to log the user in
			[self.todoService.client loginWithProvider:@"MicrosoftAccount" controller:self animated:YES
				completion:^(MSUser *user, NSError *error) {
					NSString *msg = [@"You are logged in as " stringByAppendingString:user.userId];
					UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Login"
											message:msg delegate:nil
											cancelButtonTitle:@"OK"
											otherButtonTitles: nil];
					[alert show];

					//save the user id and token to the KeyChain
					NSMutableDictionary *newItem = [self createKeyChainQueryWithClient:self.todoService.client
															andIsSearch:NO];
					[newItem setObject:user.userId forKey:(__bridge id)kSecAttrAccount];
					[newItem setObject:[user.mobileServiceAuthenticationToken dataUsingEncoding:NSUTF8StringEncoding]
                                                    forKey:(__bridge id)kSecValueData];

					OSStatus status = SecItemAdd((__bridge CFDictionaryRef)newItem, NULL);
					if(status != errSecSuccess) {
						//handle error as needed
						NSAssert(NO, @"Error caching password.");
					}
			}];
		}

> [AZURE.NOTE] 權杖屬於敏感資料且必須加密儲存，以防在裝置遺失或遭竊時洩漏。

使用快取權杖時，使用者在權杖到期之前無需重新登入。當使用者嘗試使用過期的權杖登入時，系統便會傳回 401 未經授權的回應。此時，使用者必須重新登入以取得新的權杖，並重新快取權杖。您可以使用篩選來避免每當應用程式呼叫行動服務，就得撰寫程式碼以處理過期權杖的情況。篩選可讓您攔截行動服務的呼叫和來自行動服務的回應。篩選中的程式碼會測試 401 的回應、觸發登入程序 (如果權杖已過期)，然後重試產生 401 的要求。如需詳細資料，請參閱[處理過期權杖]。

<h2><a name="errors"></a>做法：處理錯誤</h2>

呼叫行動服務時，完成區塊包含  `NSError *error` 參數。發生錯誤時，此參數便會傳回非 null 值。您應檢查程式碼中的此參數，並視需要處理錯誤。

發生錯誤時，您可以透過在程式碼中包含 MSError.h 檔案來取得更多資訊：

    #import <WindowsAzureMobileServices/MSError.h>

此檔案會定義下列可用來從 `[error userInfo]` 存取其他資料的常數：

+ **MSErrorResponseKey**：與錯誤相關聯的 HTTP 回應資料
* **MSErrorRequestKey**：與錯誤相關聯的 HTTP 要求資料

另外，會為每個錯誤碼定義一個常數。您可以在 MSError.h 檔案中找到這些程式碼的說明。

如需執行驗證和處理任何資料的範例，請參閱[使用伺服器指令碼驗證及修改行動服務中的資料]。在此主題中，伺服器端驗證是透過伺服器指令碼進行實作的。提交無效資料時，系統會傳回錯誤回應，且用戶端會處理此回應。

<!--
<h2><a name="#unit-testing"></a>做法：設計單位測試</h2>

_(選用) 本節說明使用用戶端程式庫時如何撰寫單元測試 (資訊來自 Yavor)。_

<h2><a name="#customizing"></a>做法：自訂用戶端</h2>

_(選用) 本節說明如何傳送自訂的用戶端行為。_

###<a name="custom-headers"></a>做法：自訂要求標頭

_(選用) 本節說明如何傳送自訂要求標頭。_

如需詳細資訊，請參閱有關在伺服器端處理標頭的新主題。

###<a name="custom-serialization"></a>做法：自訂序列化

_(選用) 本節說明如何使用屬性來自訂資料型別的序列化方式。_

如需詳細資訊，請參閱有關在伺服器端處理標頭的新主題。

## <a name="next-steps"></a>後續步驟
-->

<!-- Anchors. -->

[什麼是行動服務]: #what-is
[概念]: #concepts
[設定和必要條件]: #Setup
[做法：建立行動服務用戶端]: #create-client
[做法：建立資料表參考]: #table-reference
[做法：查詢行動服務中的資料]: #querying
[篩選傳回資料]: #filtering
[排序傳回的資料]: #sorting
[以分頁方式傳回資料]: #paging
[選取特定資料行]: #selecting
[做法：將資料繫結到使用者介面]: #binding
[做法：將資料插入行動服務]: #inserting
[做法：修改行動服務中的資料]: #modifying
[做法：驗證使用者]: #authentication
[快取驗證權杖]: #caching-tokens
[做法：上傳影像和大型檔案]: #blobs
[做法：處理錯誤]: #errors
[做法：設計單位測試]: #unit-testing
[做法：自訂用戶端]: #customizing
[自訂要求標頭]: #custom-headers
[自訂資料型別序列化]: #custom-serialization
[後續步驟]: #next-steps
[做法：使用 MSQuery]: #query-object

<!-- Images. -->

<!-- URLs. -->
[開始使用行動服務]: /zh-tw/develop/mobile/tutorials/get-started-ios
[使用伺服器指令碼驗證和修改行動服務中的資料]: /zh-tw/develop/mobile/tutorials/validate-modify-and-augment-data-ios
[行動服務 SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[開始使用驗證]: /zh-tw/develop/mobile/tutorials/get-started-with-users-ios
[iOS SDK]: https://developer.apple.com/xcode

[處理過期權杖]: http://go.microsoft.com/fwlink/p/?LinkId=301955
[Live Connect SDK]: http://go.microsoft.com/fwlink/p/?LinkId=301960
[權限]: http://msdn.microsoft.com/library/windowsazure/jj193161.aspx
[使用指令碼為使用者授權]: /zh-tw/develop/mobile/tutorials/authorize-users-in-scripts-ios
[動態結構描述]: http://go.microsoft.com/fwlink/p/?LinkId=296271
[做法：存取自訂參數]: /zh-tw/develop/mobile/how-to-guides/work-with-server-scripts#access-headers
[建立資料表]: http://msdn.microsoft.com/library/windowsazure/jj193162.aspx
[NSDictionary 物件]: http://go.microsoft.com/fwlink/p/?LinkId=301965
[ASCII 控制碼 C0 和 C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[使用 CLI 管理行動服務資料表]: http://azure.microsoft.com/manage/linux/other-resources/command-line-tools/#Mobile_Tables



<!--HONumber=42-->
