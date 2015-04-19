<properties 
	pageTitle="Azure Mobile Engagement Windows Phone SDK 整合" 
	description="如何在 Windows Phone 上整合 Engagement " 					
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="lalathie" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="" 
	ms.topic="article" 
	ms.date="02/12/2015" 
	ms.author="kapiteir" />


<div class="dev-center-tutorial-selector sublanding"><a href="/documentation/articles/mobile-engagement-windows-store-integrate-engagement/" title="Windows Store">Windows 市集</a><a href="/documentation/articles/mobile-engagement-windows-phone-integrate-engagement/" title="Windows Phone" class="current">Windows Phone</a><a href="/documentation/articles/mobile-engagement-ios-integrate-engagement/" title="iOS">iOS</a><a href="/documentation/articles/mobile-engagement-android-integrate-engagement/" title="Android" >Android</a></div>

#如何在 Windows Phone 上整合 Engagement

本程序說明如何啟用您應用程式內 Engagement 的分析與監視功能。

下列步驟便足以啟用計算使用者、工作階段、活動、當機和技術相關的所有統計資料需要的記錄檔之報告。用來計算其他像是事件、錯誤及作業等統計資料，所需的記錄檔之報告必須使用 Engagement API 來手動完成 (請參閱下方的 [如何在您的 Windows Phone 應用程式中使用進階的 Mobile Engagement 標記 API](mobile-engagement-windows-phone-use-engagement-api.md) )，因為這些是應用程式相關的統計資料。

##支援的版本

Engagement Windows Phone SDK 只能與適用於以下作業系統的應用程式整合：

-   Windows Phone OS 8.0
-   Windows Phone OS 8.1 with Silverlight

Engagement Windows Phone SDK 與 Windows Phone 8.0 和 8.1 裝置相容。

##將 Engagement SDK 嵌入您的 Windows Phone 專案

適用於 Mobile Engagement 的 Nuget 封裝還無法在線上取得，因此您需要下載我們的 SDK 封存，將它解壓縮，然後在 Visual Studio 中於您的專案上按一下滑鼠右鍵，以前往 [管理 NuGet 封裝]，[設定]，然後將 [lib] 資料夾新增為封裝來源。


> [AZURE.IMPORTANT] NuGet 是 .net 專案主要的封裝管理員。它將會下載、新增並複製使用 Engagement SDK 所需的任何項目。在 WP 8 Engagement 上，NuGet 會自動詢問您是否要安裝協力廠商的程式庫 (Microsoft.Bcl.Build v1.0.10 和 Microsoft.Bcl portability pack v1.1.3)，以達到跨平台可攜性的目的。您可以在 [NuGet 網站](http://docs.nuget.org/docs/start-here/overview).上找到詳細資訊

##新增功能

Engagement SDK 需要一些 Windows Phone SDK 的功能以正常運作。

開啟 `WMAppManifest.xml`  檔案，並確定 [功能] (`Capabilities` ) 面板中有宣告下列功能：

-   `ID_CAP_NETWORKING`
-   `ID_CAP_IDENTITY_DEVICE`

##初始化 Engagement SDK

### Engagement 組態

Engagement 的組態集中在您專案的 `Resources\EngagementConfiguration.xml` 檔案中。

編輯此檔案來指定：

-   您應用程式在 `<connectionString>` 和 `<\connectionString>` 標記之間的連接字串。

若想要改為在執行階段指定它，您可以在 Engagement 代理程式初始化之前呼叫下列方法：

			/* Engagement configuration. */
			EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
			engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

			/* Initialize Engagement agent with above configuration. */
			EngagementAgent.Instance.Init(engagementConfiguration);

您應用程式的連接字串會顯示在 Azure 入口網站。

### Engagement 初始化

當您建立新的專案時會產生一份 `App.xaml.cs` 檔案。這個類別繼承自 `Application` ，且包含的許多重要的方法。它將會用來初始化 Engagement SDK。

修改 `App.xaml.cs`：

-   將加入至您的 `using` 陳述式：

			using Microsoft.Azure.Engagement;

-   將 `EngagementAgent.Instance.Init` 插入 `Application_Launching` 方法：

			private void Application_Launching(object sender, LaunchingEventArgs e)
			{
			  EngagementAgent.Instance.Init();
			}

-   將 `EngagementAgent.Instance.OnActivated` 插入 `Application_Activated` 方法：

			private void Application_Activated(object sender, ActivatedEventArgs e)
			{
			   EngagementAgent.Instance.OnActivated(e);
			}

> [AZURE.WARNING] 我們強烈地建議您不要在應用程式的其他地方加入 Engagement 初始化。不過請注意，`EngagementAgent.Instance.Init` 方法執行於專用的執行緒，而非 UI 執行緒。

##基本報告

### 建議使用的方法：多載您的`PhoneApplicationPage` 類別

為了啟用 Engagement 計算使用者、工作階段、活動、當機和技術的統計資料所需的所有記錄檔之報告，您可讓所有的 `PhoneApplicationPage` 子類別從 `EngagementPage` 類別繼承。

以下是如何在您應用程式其中一個頁面使用此方法的範例。您可以將相同的方法用於您應用程式的所有頁面。

#### C\# 來源檔案

修改您的頁面 `.xaml.cs` 檔案：

-   加入您的 `using` 陳述式：

			using Microsoft.Azure.Engagement;

-   將 `PhoneApplicationPage` 用 `EngagementPage` 取代：

**沒有 Engagement：**

			namespace Example
			{
			  public partial class ExamplePage : PhoneApplicationPage
			  {
			    [...]
			  }
			}

**有 Engagement：**

			using Microsoft.Azure.Engagement;
			
			namespace Example
			{
			  public partial class ExamplePage : EngagementPage 
			  {
			    [...]
			  }
			}

> [AZURE.WARNING] 如果您的頁面繼承自 `OnNavigatedTo` 方法，請注意要讓 `base.OnNavigatedTo(e)` 呼叫。否則，不會報告該活動。事實上，`EngagementPage` 是在 `OnNavigatedTo` 方法內呼叫  `StartActivity`。

#### XAML 檔案

修改您的頁面 `.xaml` 檔案：

-   加入至命名空間宣告：

			xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"

-   將 `phone:PhoneApplicationPage` 以 `engagement:EngagementPage` 取代：

**沒有 Engagement：**

			<phone:PhoneApplicationPage>
			    <!-- layout -->
			</phone:PhoneApplicationPage>

**有 Engagement：**

			<engagement:EngagementPage 
			    xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP">
			
			    <!-- layout -->
			</engagement:EngagementPage >

#### 覆寫預設行為

根據預設，頁面的類別名稱會在報告時做為活動名稱 (沒有額外的名稱)。如果類別使用 "Page" 尾碼，Engagement 也會移除它。

如果想要覆寫名稱的預設行為，您只需將下列加入您的程式碼：

			// in the .xaml.cs file
			protected override string GetEngagementPageName()
			{
			   /* your code */
			   return "new name";
			}

如果想要報告活動的一些額外資訊，您可以將下列加入您的程式碼：

			// in the .xaml.cs file
			protected override Dictionary<object,object> GetEngagementPageExtra()
			{
			   /* your code */
			   return extra;
			}

[這裡]可以找到額外資訊的進一步資訊(../mobile-engagement-windows-phone-use-engagement-api/#extras-parameters)。

這些方法會從您頁面的 `OnNavigatedTo` 方法呼叫。

### 替代方法：手動呼叫 `StartActivity()`

如果您無法或不想要多載您的 `PhoneApplicationPage` 類別，您可以改為透過直接呼叫 `EngagementAgent` 方法來啟動活動。

我們建議您於 PhoneApplicationPage 的 `OnNavigatedTo` 方法內呼叫  `StartActivity`。

			protected override void OnNavigatedTo(NavigationEventArgs e)
			{
			   base.OnNavigatedTo(e);
			   EngagementAgent.Instance.StartActivity("MyPage");
			}

> [AZURE.IMPORTANT] 請確定您正確地結束工作階段。
>
> 應用程式關閉時 Windows Phone SDK 會自動呼叫 `EndActivity` 方法。因此，「強烈」建議****每當使用者的活動變更時便叫呼叫 `StartActivity` 方法，並且「絕對不要」****呼叫 `EndActivity` 方法，因為呼叫此方法會強制結束目前的工作階段。

##進階報告

(選擇性) 您可能想要報告應用程式特定的事件、錯誤和作業，若要這樣做，請使用 `EngagementAgent` 類別中找到的其他方法。Engagement API 允許使用所有 Engagement 的進階功能。

如需詳細資訊，請參閱[如何在您的 Windows Phone 應用程式中使用進階的 Mobile Engagement 標記 API](mobile-engagement-windows-phone-use-the-engagement-api.md)。

##進階組態

### 停用自動當機報告

您可以停用 Engagement 的自動當機報告功能。然後，發生未處理的例外狀況時，Engagement 將不會執行任何動作。

> [AZURE.WARNING] 如果您打算停用此功能，請注意當您的應用程式終將發生為處理的當機時，Engagement 將不會傳送該當機，「且」****它將不會關閉工作階段和作業。

若要停用自動當機報告，只要依照您宣告的方式自訂您的組態即可：

#### 從 `EngagementConfiguration.xml` 檔案

在 `<reportCrash>` 和 `</reportCrash>` 標記之間，將報告當機設為 `false`。

#### 從執行階段的 `EngagementConfiguration` 物件

使用 EngagementConfiguration 物件將報告當機設為 false。

			/* Engagement configuration. */

			EngagementConfiguration engagementConfiguration = new EngagementConfiguration(); engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
			/\* Disable Engagement crash reporting. \*/ engagementConfiguration.Agent.ReportCrash = false;

### 高載模式 [beta]

根據預設，Engagement 會即時報告記錄檔。如果應用程式報告記錄檔的頻率很高，最好先緩衝處理記錄檔，再定期報告它們 (此稱為「高載模式」)。

若要這樣做，請呼叫方法：

			EngagementAgent.Instance.SetBurstThreshold(int everyMs);

該引數以「毫秒」****為單位。如果您想要重新啟動及時記錄，您隨時可以呼叫該方法，而不需要使用任何參數 (或使用 0 為值)。

高載模式可以稍微延長電池使用時間但對 Engagement 監視器會有影響： 所有工作階段和工作持續時間將調整為高載閾值 (因此，可能將看不到時間比高載閾值短的工作階段和作業)。建議使用低於 30000 (30 秒) 的閾值。

> [AZURE.WARNING] 高載閾值無法設定為小於一秒的時間間隔。如果您嘗試這樣做，SDK 會顯示含錯誤訊息的追蹤，並且會自動重設為預設值 (0 秒)。這樣會觸發 SDK 以即時的方式報告記錄檔。

<!--HONumber=47-->
