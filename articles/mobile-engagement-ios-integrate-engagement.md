<properties 
	pageTitle="Azure Mobile Engagement iOS SDK 整合" 
	description="iOS SDK for Azure Mobile Engagement 的最新更新和程序"
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="kpiteira" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="objective-c" 
	ms.topic="article" 
	ms.date="02/12/2015" 
	ms.author="kapiteir" />


<div class="dev-center-tutorial-selector sublanding"><a href="/documentation/articles/mobile-engagement-windows-store-integrate-engagement/" title="Windows Store">Windows 市集</a><a href="/documentation/articles/mobile-engagement-windows-phone-integrate-engagement/" title="Windows Phone">Windows Phone</a><a href="/documentation/articles/mobile-engagement-ios-integrate-engagement/" title="iOS" class="current">iOS</a><a href="/documentation/articles/mobile-engagement-android-integrate-engagement/" title="Android" >Android</a></div>


#如何在 iOS 上整合 Engagement

> [AZURE.IMPORTANT] Engagement SDK 需要 iOS4 以上：應用程式的部署目標必須至少為 iOS 4。

此程序描述在您的 iOS 應用程式中，啟動 Engagement 的分析和監視功能時，最簡單的方法。

下列步驟足以啟動記錄檔報告，來計算使用者、工作階段、活動、當機和技術相關的所有統計資料。計算其他統計資料，例如事件、錯誤和工作所需的記錄檔報告必須使用 Engagement API 以手動方式進行 (請參閱 ios-sdk-engagement-advanced) ，因為這些統計資料與應用程式有關。

##將 Engagement SDK 嵌入您的 iOS 專案

將 Engagement SDK 加入您的 iOS 專案：在 Xcode 4 中，以滑鼠右鍵按一下專案，然後選取 [**新增檔案至**]，再選擇 `EngagementSDK` 資料夾。

Engagement 需要額外的架構才能運作：在 [專案總管] 中，開啟專案窗格並選取正確的目標。然後，開啟 [**建置階段**] 索引標籤，在 [**連結二進位檔與程式庫**] 功能表加入下列架構：

> -   `AdSupport.framework` - 將連結設為 `選用`
> -   `SystemConfiguration.framework`
> -   `CoreTelephony.framework`
> -   `CFNetwork.framework`
> -   `CoreLocation.framework`
> -   `libxml2.dylib`

> [AZURE.NOTE] AdSupport 架構可以移除。Engagement 需要此架構來收集 IDFA。但您可以停用 IDFA 收集 \<ios-sdk-engagement-idfa\>，以符合關於此識別碼的 Apple 原則。

##初始化 Engagement SDK

您需要修改應用程式委派：

-   在實作檔案頂端匯入 Engagement 代理程式：

			[...]
			#import "EngagementAgent.h"

-   在 '**applicationDidFinishLaunching:**' 或 '**application:didFinishLaunchingWithOptions:**' 方法內初始化 Engagement：

			- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
			{
			  [...]
			  [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}"];
			  [...]
			}

##基本報告

### 建議使用的方法： 多載 `UIViewController` 類別

若要啟動 Engagement 需要的所有記錄檔報告，來計算使用者、工作階段、活動、當機和技術統計資料，您只需要讓所有 `UIViewController` 子類別繼承自 `EngagementViewController` 類別 ( `UITableViewController` -\> `EngagementTableViewController` 也是相同的規則)。

**沒有 Engagement：**

			#import <UIKit/UIKit.h>
			
			@interface Tab1ViewController : UIViewController<UITextFieldDelegate> {
			  UITextField* myTextField1;
			  UITextField* myTextField2;
			}
			
			@property (nonatomic, retain) IBOutlet UITextField* myTextField1;
			@property (nonatomic, retain) IBOutlet UITextField* myTextField2;

**有 Engagement：**

			#import <UIKit/UIKit.h>
			#import "EngagementViewController.h"
			
			@interface Tab1ViewController : EngagementViewController<UITextFieldDelegate> {
			  UITextField* myTextField1;
			  UITextField* myTextField2;
			}
			
			@property (nonatomic, retain) IBOutlet UITextField* myTextField1;
			@property (nonatomic, retain) IBOutlet UITextField* myTextField2;

### 替代方法：手動呼叫 `startActivity()`

如果您無法或不想要多載 `UIViewController` 類別，可以改為直接呼叫 `EngagementAgent` 的方法來開始活動。

> [AZURE.IMPORTANT] iOS SDK 會在應用程式關閉時呼叫 `endActivity()` 方法。因此， *高度* 建議您只要使用者的活動變更就呼叫 `startActivity` 方法，且 *絕不* 呼叫 `endActivity` 方法，因為呼叫此方法會強制結束目前的工作階段。

##位置報告

Apple 服務條款不允許應用程式只為了統計資料的目的而使用位置追蹤。因此，建議您只有當應用程式也會因為另一個原因而使用位置追蹤時才啟用位置報告。

從 iOS 8 開始，您必須提供應用程式如何使用位置服務的描述，方法是在應用程式的 Info.plist 檔案中設定索引鍵 [NSLocationWhenInUseUsageDescription] 或 [NSLocationAlwaysUsageDescription] 的字串。如果您想要在背景以 Engagement 報告位置，請加入 NSLocationAlwaysUsageDescription 索引鍵。在其他情況下，請加入 NSLocationWhenInUseUsageDescription 索引鍵。

### 延遲區域位置報告

延遲區域位置報告允許報告國家、地區以及與裝置相關聯的位置。這類位置報告只會使用網路位置 (根據基地台識別碼或 WIFI)。每個工作階段最多報告一次裝置區域。絕不會使用 GPS，因此這類位置報告對於電池的影響很小 (但不是沒有)。

報告的區域用來計算關於使用者、工作階段、事件與錯誤的地理統計資料。它們也可用來做為 Reach 活動的準則。針對裝置報告的最後已知區域可以藉由 [裝置 API] 來擷取。

若要啟用延遲區域位置報告，請在初始化 Engagement 代理程式之後加入下面這一行：

			- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
			{
			  [...]
			  [[EngagementAgent shared] setLazyAreaLocationReport:YES];
			  [...]
			}

### 即時位置報告

即時位置報告允許報告與裝置相關聯的女緯度和經度。根據預設，這類位置報告只會使用網路位置 (根據基地台識別碼或 WIFI)，且只會在應用程式在前景中執行 (也就是在工作階段) 時，報告才會在作用中。

即時位置 *不* 用來計算統計資料。其唯一目的是要允許即時使用
Reach 活動中的 geo-fencing \<Reach-Audience-geofencing\> 準則。

若要啟用即時位置報告，請在初始化 Engagement 代理程式之後加入下面這一行：

			[[EngagementAgent shared] setRealtimeLocationReport:YES];

#### GPS 式報告

根據預設，即時位置報告只會使用網路位置。若要啟用 GPS 位置 (精準度大為提高)，請加入：

			[[EngagementAgent shared] setFineRealtimeLocationReport:YES];

#### 背景報告

根據預設，即時位置報告只在應用程式在前景中執行 (也就是在工作階段) 時才會在作用中。若要也在背景中啟用報告，請加入：

			[[EngagementAgent shared] setBackgroundRealtimeLocationReport:YES withLaunchOptions:launchOptions];

> [AZURE.NOTE] 當應用程式在背景中執行，即使啟用 GPS，也只會報告網路位置。

此函式的實作在應用程式進入背景時呼叫 [startMonitoringSignificantLocationChanges]。請注意，如果新的位置事件抵達，它會自動重新啟動您的應用程式到背景。

##進階報告

此外，如果您想要報告應用程式特定的事件、錯誤和工作，您必須透過 `EngagementAgent` 類別的方法來使用 Engagement API。此類別的物件可以藉由呼叫 `[EngagementAgent shared]` 靜態方法進行擷取。

Engagement API 允許使用所有 Engagement 的進階功能，詳情請見＜如何
在 iOS 上使用 Engagement API＞(以及 `EngagementAgent` 類別的技術文件)。

##停用 IDFA 集合

根據預設，Engagement 會使用 [IDFA] 來唯一識別使用者。但是，如果您未在應用程式中的其他地方使用廣告，您可能會遭到應用程式市集檢視程序拒絕。IDFA 集合可以藉由在 pch 檔案加入前置處理器巨集 `ENGAGEMENT_DISABLE_IDFA` 進行停用 (或是在應用程式的  `建置設定`])。如此可確保應用程式建置中沒有任何對 `ASIdentifierManager`、 `advertisingIdentifier` 或 `isAdvertisingTrackingEnabled` 的參考。

**prefix.pch** 檔案中的整合：

			#define ENGAGEMENT_DISABLE_IDFA
			...

您可以藉由檢查 Engagement 測試記錄檔，確認 IDFA 集合已在應用程式中正確停用。請參閱 Integration Test\<ios-sdk-engagement-test-idfa\> 文件以取得進一步資訊。

##停用記錄報告

### 使用方法呼叫

如果您想要 Engagement 停止傳送記錄檔，可以呼叫：

			[[EngagementAgent shared] setEnabled:NO];

這個呼叫是持續性的：它會使用 `NSUserDefaults` 來存放資訊。

您可以用 `YES` 呼叫相同函式，重新啟用記錄報告。

### 設定組合中的整合

如不呼叫此函式，您也可以直接在現有 `Settings.bundle` 檔案中整合這個設定。字串 `engagement_agent_enabled` 必須當做喜好設定識別碼，而且必須與切換開關相關聯 (`PSToggleSwitchSpecifier`)。

 `Settings.bundle` 的下列範例示範如何實作它：

			<dict>
			    <key>PreferenceSpecifiers</key>
			    <array>
			        <dict>
			            <key>DefaultValue</key>
			            <true/>
			            <key>Key</key>
			            <string>engagement_agent_enabled</string>
			            <key>Title</key>
			            <string>Log reporting enabled</string>
			            <key>Type</key>
			            <string>PSToggleSwitchSpecifier</string>
			        </dict>
			    </array>
			    <key>StringsTable</key>
			    <string>Root</string>
			</dict>

<!-- URLs. -->
[裝置 API]: http://go.microsoft.com/?linkid=9876094
[NSLocationWhenInUseUsageDescription]:https://developer.apple.com/library/prerelease/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW26
[NSLocationAlwaysUsageDescription]:https://developer.apple.com/library/prerelease/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW18
[startMonitoringSignificantLocationChanges]:http://developer.apple.com/library/IOs/#documentation/CoreLocation/Reference/CLLocationManager_Class/CLLocationManager/CLLocationManager.html#//apple_ref/occ/instm/CLLocationManager/startMonitoringSignificantLocationChanges
[IDFA]:https://developer.apple.com/library/ios/documentation/AdSupport/Reference/ASIdentifierManager_Ref/ASIdentifierManager.html#//apple_ref/occ/instp/ASIdentifierManager/advertisingIdentifier

<!--HONumber=47-->
