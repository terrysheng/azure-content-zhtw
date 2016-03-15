<properties 
	pageTitle="將 iOS WebView 與原生 Mobile Engagement iOS SDK 橋接" 
	description="說明如何在執行 Javascript 的 WebView 與原生的 Mobile Engagement iOS SDK 之間建立橋接器"		
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="piyushjo" 
	manager="erikre" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="objective-c" 
	ms.topic="article" 
	ms.date="02/25/2016" 
	ms.author="piyushjo" />

#將 iOS WebView 與原生 Mobile Engagement iOS SDK 橋接

> [AZURE.SELECTOR]
- [Android 橋接器](mobile-engagement-bridge-webview-native-android.md)
- [iOS 橋接器](mobile-engagement-bridge-webview-native-ios.md)

某些行動應用程式設計為混合式應用程式，其中應用程式本身使用原生 iOS Objective-C 開發方式開發，但部份或甚至所有的畫面是在 iOS WebView 中轉譯。您仍然可以在這類應用程式中使用 Mobile Engagement iOS SDK，而本教學課程將說明做法。

雖然文件沒有記載，但有兩種方法可以達到此目的：

- 第一種方法是此[連結](http://stackoverflow.com/questions/9826792/how-to-invoke-objective-c-method-from-javascript-and-send-back-data-to-javascrip)中所描述，牽涉到在您的網頁檢視上註冊 `UIWebViewDelegate`，然後以 Javascript「捕捉並立即取消」位置變更來完成。 
- 第二種方法依據此 [WWDC 2013 Session (WWDC 2013 會議)](https://developer.apple.com/videos/play/wwdc2013/615)，這個方法比第一種更為簡潔，我們在此指南中將遵循此方法。請注意，這個方法僅適用於 iOS7 和更新版本。 

針對 iOS 橋接的範例，請遵循下列步驟：

1. 首先，您必須確定您已經完成我們的[快速入門教學課程](mobile-engagement-ios-get-started.md)，以在您的混合式應用程式中整合 Mobile Engagement iOS SDK。或者，您也可以如下所示啟用測試記錄，如此當我們從 WebView 觸發 SDK 方法時您就可以檢視它們。 
    
		- (BOOL)application:(UIApplication ​*)application didFinishLaunchingWithOptions:(NSDictionary *​)launchOptions {
		   ....
     		[EngagementAgent setTestLogEnabled:YES];
		   ....
		}

2. 現在請確定您的混合式應用程式上有使用 WebView 的畫面。您可以將它新增到應用程式的 `Main.storyboard`。

3. 將此 WebView 與您的 **ViewController** 關聯，方法是從 View Controller Scene (檢視控制器場景) 按一下 WebView 並拖曳到 `ViewController.h` 編輯畫面，然後將它放在 `@interface` 程式行下方。

4. 您這麼做之後，對話方塊隨即會出現要求輸入名稱。提供名稱 **webView**。`ViewController.h` 檔案看起來應該如下所示：

		#import <UIKit/UIKit.h>
		#import "EngagementViewController.h"
		
		@interface ViewController : EngagementViewController
		@property (strong, nonatomic) IBOutlet UIWebView *webView;
		
		@end

5. 稍後我們將會更新 `ViewController.m` 檔案，但首先我們要建立橋接器檔案，該檔案會透過一些常用的 Mobile Engagement iOS SDK 方法建立包裝函式。建立名為 **EngagementJsExports.h** 的標頭檔案，它會使用前述[會議](https://developer.apple.com/videos/play/wwdc2013/615)中所描述的 `JSExport` 機制來公開原生的 iOS 方法。

		#import <Foundation/Foundation.h>
		#import <JavaScriptCore/JavascriptCore.h>
		
		@protocol EngagementJsExports <JSExport>
		
		+ (void) sendEngagementEvent:(NSString*) name :(NSString*)extras;
		+ (void) startEngagementJob:(NSString*) name :(NSString*)extras;
		+ (void) endEngagementJob:(NSString*) name;
		+ (void) sendEngagementError:(NSString*) name :(NSString*)extras;
		+ (void) sendEngagementAppInfo:(NSString*) appInfo;
		
		@end

		@interface EngagementJs : NSObject <EngagementJsExports>

		@end

6. 接下來我們將建立橋接器檔案的第二個部分。建立名為 **EngagementJsExports.m** 的檔案，其中包含透過呼叫 Mobile Engagement iOS SDK 方法建立實際包裝函式的實作。另外請注意，我們正在貼上的 `extras` 會從 WebView 的 Javascript 傳遞，並放置到 `NSMutableDictionary` 物件中與 Engagement SDK 方法呼叫傳遞。

		#import <UIKit/UIKit.h>
		#import "EngagementAgent.h"
		#import "EngagementJsExports.h"
		
		@implementation EngagementJs
		
		+(void) sendEngagementEvent:(NSString​*)name :(NSString*​)extras {
		   NSMutableDictionary* extrasInput = [self ParseExtras:extras];
		   [[EngagementAgent shared] sendEvent:name extras:extrasInput];
		}
		
		+ (void) startEngagementJob:(NSString*) name :(NSString*)extras {
		   NSMutableDictionary* extrasInput = [self ParseExtras:extras];
		   [[EngagementAgent shared] startJob:name extras:extrasInput];
		}
		
		+ (void) endEngagementJob:(NSString*) name {
		   [[EngagementAgent shared] endJob:name];
		}
		
		+ (void) sendEngagementError:(NSString*) name :(NSString*)extras {
		   NSMutableDictionary* extrasInput = [self ParseExtras:extras];
		   [[EngagementAgent shared] sendError:name extras:extrasInput];
		}
		
		+ (void) sendEngagementAppInfo:(NSString*) appInfo {
		   NSMutableDictionary* appInfoInput = [self ParseExtras:appInfo];
		   [[EngagementAgent shared] sendAppInfo:appInfoInput];
		}
		
		+ (NSMutableDictionary*) ParseExtras:(NSString*) input {
		   NSData *data = [input dataUsingEncoding:NSUTF8StringEncoding];
		   NSError* error = nil;
		   NSMutableDictionary* extras = [NSJSONSerialization JSONObjectWithData:data options:0 error:&error];
		   
		   return extras;
		}
		
		@end

5. 現在我們回到 **ViewController.m** 並使用下列程式碼更新它：
		
		#import <JavaScriptCore/JavaScriptCore.h>
		#import "ViewController.h"
		#import "EngagementJsExports.h"
		
		@interface ViewController ()
		
		@end
		
		@implementation ViewController
		
		- (void)viewDidLoad
		{
		   self.webView.delegate = self;
		   [super viewDidLoad];
		   [self loadWebView];
		}
		
		- (void)loadWebView {
		   NSBundle* mainBundle = [NSBundle mainBundle];
		   NSURL* htmlPage = [mainBundle URLForResource:@"LocalPage" withExtension:@"html"];
		   
		   NSURLRequest* urlReq = [NSURLRequest requestWithURL:htmlPage];
		   [self.webView loadRequest:urlReq];
		}
		
		- (void)webViewDidFinishLoad:(UIWebView*)wv
		{
		   JSContext* context = [wv valueForKeyPath:@"documentView.webView.mainFrame.javaScriptContext"];
		   
		   context[@"EngagementJs"] = [EngagementJs class];
		}
		
		- (void)webView:(UIWebView​*)wv didFailLoadWithError:(NSError*​)error
		{
		   NSLog(@"Error for WEBVIEW: %@", [error description]);
		}
		
		- (void)didReceiveMemoryWarning {
		   [super didReceiveMemoryWarning];
		   // Dispose of any resources that can be recreated.
		}
		
		@end

6. 請注意下列有關 **ViewController.m** 檔案的重點：

	- 在 `loadWebView` 方法中，我們會載入名為 **LocalPage.html** 的本機 HTML 檔案，接著我們會檢閱其程式碼。 
	- 在 `webViewDidFinishLoad` 方法中，我們會抓取 `JsContext` 並將它與我們的包裝函式類別關聯。這會允許使用控制代碼 **EngagementJs** 從 webView 呼叫我們的包裝函式 SDK 方法。 

7. 使用下列程式碼建立名為 **LocalPage.html** 的檔案：

		<!doctype html>
		<html>
		   <head>
		       <style type='text/css'>
		           html { font-family:Helvetica; color:#222; }
		           h1 { color:steelblue; font-size:22px; margin-top:16px; }
		           h2 { color:grey; font-size:14px; margin-top:18px; margin-bottom:0px; }
		       </style>
		       
		       <script type="text/javascript">
		       
		       window.onerror = function(err)
		       {
		           alert('window.onerror: ' + err);
		       }
		       
		       function send(inputId)
		       {
		           var input = document.getElementById(inputId);
		           if(input)
		           {
		               var value = input.value;
		               // Example of how extras info can be passed with the Engagement logs
		               var extras = '{"CustomerId":"MS290011"}';
		           }
		           
		           if(value && value.length > 0)
		           {
		               switch(inputId)
		               {
		                   case "event":
		                   EngagementJs.sendEngagementEvent(value, extras);
		                   break;
		                   
		                   case "job":
		                   EngagementJs.startEngagementJob(value, extras);
		                   window.setTimeout(
		                   function(){
		                       EngagementJs.endEngagementJob(value);
		                   }, 10000 );
		                   break;
		
		                   case "error":
		                   EngagementJs.sendEngagementError(value, extras);
		                   break;
		                   
		                   case "appInfo":
		                   var appInfo = '{"customer_name":"' + value + '"}';
		                   EngagementJs.sendEngagementAppInfo(appInfo);
		                   break;
		               }
		           }
		       }
		       </script>
		       
		   </head>
		   <body>
		       <h1>Bridge Tester</h1>
		       
		       <div id='engagement'>
		           
		           <br/>
		           <h2>Event</h2>
		           <input type="text" id="event" size="35">
		           <button onclick="send('event')">Send</button>
		           
		           <br/>
		           <h2>Job</h2>
		           <input type="text" id="job" size="35">
		           <button onclick="send('job')">Send</button>
		           
		           <br/>
		           <h2>Error</h2>
		           <input type="text" id="error" size="35">
		           <button onclick="send('error')">Send</button
		           
		           <br/>
		           <h2>AppInfo</h2>
		           <input type="text" id="appInfo" size="35">
		           <button onclick="send('appInfo')">Send</button>
		       
		       </div>
		   </body>
		</html>

8. 請注意有關上述 HTML 檔案的重點：

	- 	它包含一組輸入方塊，您可以在當中提供資料，用來做為事件、工作、錯誤，應用程式資訊的名稱。當您按一下它旁邊的按鈕，便會向 Javascript 進行呼叫，這通常會從橋接器檔案中呼叫方法以將此呼叫傳遞到 Mobile Engagement iOS SDK。 
	- 	我們將一些額外的資訊標記到事件、工作，甚至是錯誤，來示範這是如何完成的。此額外資訊會以 JSON 字串傳送，它 (如果您查看 `EngagementJsExports.m` 檔案) 可被解析，並隨傳送的事件、工作、錯誤傳遞。 
	- 	Mobile Engagement 工作會以您在輸入方塊中指定的名稱開始工作，執行 10 秒鐘之後關閉。 
	- 	Mobile Engagement 應用程式資訊或標記會以 'customer\_name' 傳遞作為靜態索引鍵，且您在輸入方塊輸入的值會作為此標記的值。 
 
9. 執行應用程式，然後您會看到下列畫面。現在為測試事件提供一些名稱，如下所示，然後按一下旁邊的 [傳送]。

	![][1]

10. 現在，如果您移至應用程式的 [監視] 索引標籤，並查看 [事件] -> [詳細資料] 底下，您會看到此事件與我們傳送的靜態應用程式資訊一起顯示。

	![][2]

<!-- Images. -->
[1]: ./media/mobile-engagement-bridge-webview-native-ios/sending-event.png
[2]: ./media/mobile-engagement-bridge-webview-native-ios/event-output.png

<!---HONumber=AcomDC_0302_2016-------->