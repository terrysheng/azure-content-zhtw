<properties
	pageTitle="使用 Azure 通知中樞和 Bing 空間資料為推播通知加上地理柵欄 | Microsoft Azure"
	description="在本教學課程中，您將學習如何使用 Azure 通知中樞和 Bing 空間資料來傳遞以位置為基礎的推播通知。"
	services="notification-hubs"
	documentationCenter="windows"
    keywords="推播通知,推播通知"
	authors="dend"
	manager="yuaxu"
	editor="dend"/>

<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows-phone"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="03/16/2016"
	ms.author="dendeli"/>
    
# 使用 Azure 通知中樞和 Bing 空間資料為推播通知加上地理柵欄
 
 > [AZURE.NOTE] 若要完成此教學課程，您必須具備有效的 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 [Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02)。

在本教學課程中，您將學習如何從通用 Windows 平台應用程式，運用 Azure 通知中樞和 Bing 空間資料來傳遞以位置為基礎的推播通知。

##必要條件
首先，您必須確定您擁有所有必要的軟體和服務︰

* [Visual Studio 2015 Update 1](https://www.visualstudio.com/zh-TW/downloads/download-visual-studio-vs.aspx) 或更新版本 ([Community Edition](https://go.microsoft.com/fwlink/?LinkId=691978&clcid=0x409) 也行)。 
* 最新版的 [Azure SDK](https://azure.microsoft.com/downloads/)。 
* [Bing 地圖服務開發人員中心帳戶](https://www.bingmapsportal.com/) (您可以免費建立帳戶並將此帳戶關聯到您的 Microsoft 帳戶)。 

##開始使用

一開始請先建立專案。在 Visual Studio 中，開始 [空白應用程式 (通用 Windows)] 類型的新專案。

![](./media/notification-hubs-geofence/notification-hubs-create-blank-app.png)

建立好專案之後，您應該就能控管應用程式本身。現在讓我們來進行地理柵欄基礎結構的各項設定。由於我們會使用 Bing 服務來進行這項作業，因此會有可供我們查詢特定位置框架的公用 REST API 端點︰

    http://spatial.virtualearth.net/REST/v1/data/
    
您必須指定下列參數以讓端點開始運作︰

* **資料來源識別碼**和**資料來源名稱** – 在 Bing 地圖服務 API 中，資料來源包含了各種分門別類的中繼資料，例如營業據點和營業時間。您可以在此了解其相關資訊。 
* **實體名稱** – 您想要作為通知參考點的實體。 
* **Bing 地圖服務 API 金鑰** – 這是您稍早建立 Bing 開發人員中心帳戶時取得的金鑰。
 
接著讓我們來深入了解上述各個項目的設定。

##設定資料來源

您可以在 Bing 地圖服務開發人員中心進行此設定。請直接按一下上方導覽列中的 [資料來源]，然後選取 [管理資料來源]。

![](./media/notification-hubs-geofence/bing-maps-manage-data.png)

如果您之前未曾用過 Bing 地圖服務 API，裡面很可能不會有任何資料來源，因此您可以直接按一下 [上傳資料到資料來源] 來建立新的資料來源。請確實填寫所有必要欄位︰

![](./media/notification-hubs-geofence/bing-maps-create-data.png)

您可能想知道什麼是資料檔案，以及應該上傳什麼資料？ 就這項測試來說，我們可以直接使用框起舊金山濱水區的管道式範例︰

    Bing Spatial Data Services, 1.0, TestBoundaries
    EntityID(Edm.String,primaryKey)|Name(Edm.String)|Longitude(Edm.Double)|Latitude(Edm.Double)|Boundary(Edm.Geography)
    1|SanFranciscoPier|||POLYGON ((-122.389825 37.776598,-122.389438 37.773087,-122.381885 37.771849,-122.382186 37.777022,-122.389825 37.776598))
    
上述程式碼代表以下實體︰

![](./media/notification-hubs-geofence/bing-maps-geofence.png)

直接將上述字串複製並貼到新檔案、將它另存為 **NotificationHubsGeofence.pipe**，然後上傳到 Bing 開發人員中心。

>[AZURE.NOTE]您可能會收到提示，要求您為 [主要金鑰] 指定不同於 [查詢金鑰] 的新金鑰。請直接透過儀表板建立新的金鑰，然後重新整理資料來源上傳頁面。

上傳資料檔案後，您必須確實發佈資料來源。

移至 [管理資料來源] (方法同前)、在清單中找到您的資料來源，然後按一下 [動作] 欄中的 [發佈]。不用多久，您應該就會在 [已發佈的資料來源] 索引標籤中看到您的資料來源︰

![](./media/notification-hubs-geofence/bing-maps-published-data.png)

如果您按一下 [編輯]，將可以一眼看到我們在其中引進的所有位置︰

![](./media/notification-hubs-geofence/bing-maps-data-details.png)

此時，入口網站並不會顯示我們建立之地理柵欄的邊界，我們只需要確認指定的位置位於適當區域內。

現在您已符合資料來源的所有要求。若要取得 API 呼叫之要求 URL 的詳細資料，請在 Bing 地圖服務開發人員中心按一下 [資料來源]，然後選取 [資料來源資訊]。

![](./media/notification-hubs-geofence/bing-maps-data-info.png)

在這裡我們要找的是 [查詢 URL]。我們可以對此端點執行查詢，以檢查裝置目前是否位於某個位置的邊界內。若要執行這項檢查，我們只需要對查詢 URL 執行 GET 呼叫，並附加下列參數︰

    ?spatialFilter=intersects(%27POINT%20LONGITUDE%20LATITUDE)%27)&$format=json&key=QUERY_KEY

這樣一來，您將會指定取自裝置的目標位置點，而且 Bing 地圖服務會自動執行計算以確定裝置是否位於地理柵欄內。一旦您透過瀏覽器 (或 cURL) 執行要求，您將會收到標準 JSON 回應︰

![](./media/notification-hubs-geofence/bing-maps-json.png)

只有當位置點確實位於指定邊界內時，才會出現此回應。如果不在邊界內，您將會收到空白的 **results** 分類︰

![](./media/notification-hubs-geofence/bing-maps-nores.png)

##設定 UWP 應用程式

現在我們已經備妥資料來源，接下來我們可以開始處理稍早啟動的 UWP 應用程式。

首先，我們必須啟用應用程式的位置服務。若要這樣做，請按兩下 [方案總管]中的 `Package.appxmanifest` 檔案。

![](./media/notification-hubs-geofence/vs-package-manifest.png)

在剛剛開啟的 [封裝屬性] 索引標籤中按一下 [功能]，並確實選取 [位置]：

![](./media/notification-hubs-geofence/vs-package-location.png)

因為已宣告位置功能，請在方案中建立名為 `Core` 的新資料夾，並在其中加入稱為 `LocationHelper.cs` 的新檔案：

![](./media/notification-hubs-geofence/vs-location-helper.png)

`LocationHelper` 類別本身在這個階段的用途相當基本，就只是允許我們透過系統 API 取得使用者位置︰

    using System;
    using System.Threading.Tasks;
    using Windows.Devices.Geolocation;

    namespace NotificationHubs.Geofence.Core
    {
        public class LocationHelper
        {
            private static readonly uint AppDesiredAccuracyInMeters = 10;

            public async static Task<Geoposition> GetCurrentLocation()
            {
                var accessStatus = await Geolocator.RequestAccessAsync();
                switch (accessStatus)
                {
                    case GeolocationAccessStatus.Allowed:
                        {
                            Geolocator geolocator = new Geolocator { DesiredAccuracyInMeters = AppDesiredAccuracyInMeters };

                            return await geolocator.GetGeopositionAsync();
                        }
                    default:
                        {
                            return null;
                        }
                }
            }

        }
    }

若想深入了解如何在 UWP 應用程式中取得使用者位置，請參閱官方的 [MSDN 文件](https://msdn.microsoft.com/library/windows/apps/mt219698.aspx)。

若要確認實際上是否能取得位置，請開啟主頁面的程式碼端 (`MainPage.xaml.cs`)。在 `MainPage` 建構函式中為 `Loaded` 事件建立新的事件處理常式︰

    public MainPage()
    {
        this.InitializeComponent();
        this.Loaded += MainPage_Loaded;
    }

事件處理常式的實作如下︰

    private async void MainPage_Loaded(object sender, RoutedEventArgs e)
    {
        var location = await LocationHelper.GetCurrentLocation();

        if (location != null)
        {
            Debug.WriteLine(string.Concat(location.Coordinate.Longitude,
                " ", location.Coordinate.Latitude));
        }
    }

請注意，因為 `GetCurrentLocation` 不必是即時資訊，並因此需要以非同步方式執行，所以我們將處理常式宣告為非同步。此外，因為在某些情況下我們可能會得到空值的位置 (例如位置服務已停用或應用程式用來存取位置的權限遭到拒絕)，因此我們需要確定它會透過空值檢查進行適當處理。

執行應用程式。請確定您允許其存取位置︰

![](./media/notification-hubs-geofence/notification-hubs-location-access.png)

一旦應用程式啟動，您應該就能在 [輸出] 視窗中看到座標︰

![](./media/notification-hubs-geofence/notification-hubs-location-output.png)

現在您知道能正常取得位置了，接著您可以放心移除用於 Loaded 的測試事件處理常式，因為我們不會再用到它。

下一步是擷取位置變更。為此，讓我們回到 `LocationHelper` 類別，並新增用於 `PositionChanged` 的事件處理常式：

    geolocator.PositionChanged += Geolocator_PositionChanged;

實作後將會在 [輸出] 視窗中顯示位置座標︰

    private static async void Geolocator_PositionChanged(Geolocator sender, PositionChangedEventArgs args)
    {
        await CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
        {
            Debug.WriteLine(string.Concat(args.Position.Coordinate.Longitude, " ", args.Position.Coordinate.Latitude));
        });
    }

##設定後端

從 GitHub 下載 [.NET 後端範例](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/NotifyUsers)。下載完成後，依序開啟 `NotifyUsers` 資料夾和 `NotifyUsers.sln` 檔案。

將 `AppBackend` 專案設定為 [啟始專案] 並加以啟動。

![](./media/notification-hubs-geofence/vs-startup-project.png)

專案已設定為會將推播通知傳送至目標裝置，所以我們只需要做兩件事：交換出用於通知中樞的正確連接字串，並新增邊界識別以便只在使用者位於地理柵欄內時才傳送通知。

若要設定連接字串，請開啟 `Models` 資料夾內的 `Notifications.cs`。`NotificationHubClient.CreateClientFromConnectionString` 函式應該會包含可在 [Azure 入口網站](https://portal.azure.com)內取得之通知中樞的相關資訊 (請查看 [設定] 的 [存取原則] 刀鋒視窗內部)。儲存經過更新的組態檔。

現在我們需要為 Bing 地圖服務 API 結果建立模型。若要這麼做，最簡單的方式是以滑鼠右鍵按一下 `Models` 資料夾，然後選取 [新增] > [類別]。將它命名為 `GeofenceBoundary.cs`完成後，從我們在第一節所討論的 API 回應複製 JSON，然後在 Visual Studio 中使用 [編輯] > [選擇性貼上] > [貼上 JSON 作為類別]。

這樣一來，我們就能確保物件將會確實依其設計目的還原序列化。所產生的類別集應該會與下面類似︰

    namespace AppBackend.Models
    {
        public class Rootobject
        {
            public D d { get; set; }
        }

        public class D
        {
            public string __copyright { get; set; }
            public Result[] results { get; set; }
        }

        public class Result
        {
            public __Metadata __metadata { get; set; }
            public string EntityID { get; set; }
            public string Name { get; set; }
            public float Longitude { get; set; }
            public float Latitude { get; set; }
            public string Boundary { get; set; }
            public string Confidence { get; set; }
            public string Locality { get; set; }
            public string AddressLine { get; set; }
            public string AdminDistrict { get; set; }
            public string CountryRegion { get; set; }
            public string PostalCode { get; set; }
        }

        public class __Metadata
        {
            public string uri { get; set; }
        }
    }

接下來，開啟 `Controllers` > `NotificationsController.cs`。我們需要調整 Post 呼叫以說明目標經緯度。為此，請直接新增下列兩個字串到函式簽章：`latitude` 和 `longitude`。

    public async Task<HttpResponseMessage> Post(string pns, [FromBody]string message, string to_tag, string latitude, string longitude)

在稱為 `ApiHelper.cs` 的專案內建立新類別，我們將使用它來連線到 Bing，以確認位置點邊界的交叉點。實作類似下面的 `IsPointWithinBounds` 函式︰

    public class ApiHelper
    {
        public static readonly string ApiEndpoint = "{YOUR_QUERY_ENDPOINT}?spatialFilter=intersects(%27POINT%20({0}%20{1})%27)&$format=json&key={2}";
        public static readonly string ApiKey = "{YOUR_API_KEY}";

        public static bool IsPointWithinBounds(string longitude,string latitude)
        {
            var json = new WebClient().DownloadString(string.Format(ApiEndpoint, longitude, latitude, ApiKey));
            var result = JsonConvert.DeserializeObject<Rootobject>(json);
            if (result.d.results != null && result.d.results.Count() > 0)
            {
                return true;
            }
            else
            {
                return false;
            }
        }
    }

>[AZURE.NOTE] 請務必將 API 端點替換為您稍早從 Bing 開發人員中心取得的查詢 URL (API 金鑰也要如此處理)。

如果查詢後有得到結果，這表示指定位置點位於地理柵欄邊界內，因此我們會傳回 `true`。如果查詢不到任何結果，Bing 就會告訴我們位置點位於查閱框架外部，因此我們會傳回 `false`。

回到 `NotificationsController.cs`，在 switch 陳述式前建立檢查︰

    if (ApiHelper.IsPointWithinBounds(longitude, latitude))
    {
        switch (pns.ToLower())
        {
            case "wns":
                //// Windows 8.1 / Windows Phone 8.1
                var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" +
                            "From " + user + ": " + message + "</text></binding></visual></toast>";
                outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);

                // Windows 10 specific Action Center support
                toast = @"<toast><visual><binding template=""ToastGeneric""><text id=""1"">" +
                            "From " + user + ": " + message + "</text></binding></visual></toast>";
                outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);

                break;
        }
    }

這樣一來，就只會在位置點位於邊界內時才傳送通知。

##在 UWP 應用程式中測試推播通知

回到 UWP 應用程式，現在我們應該能夠測試通知了。在 `LocationHelper` 類別內建立新函式 `SendLocationToBackend`：

    public static async Task SendLocationToBackend(string pns, string userTag, string message, string latitude, string longitude)
    {
        var POST_URL = "http://localhost:8741/api/notifications?pns=" +
            pns + "&to_tag=" + userTag + "&latitude=" + latitude + "&longitude=" + longitude;

        using (var httpClient = new HttpClient())
        {
            try
            {
                await httpClient.PostAsync(POST_URL, new StringContent(""" + message + """,
                    System.Text.Encoding.UTF8, "application/json"));
            }
            catch (Exception ex)
            {
                Debug.WriteLine(ex.Message);
            }
        }
    }

>[AZURE.NOTE] 將 `POST_URL` 替換為我們在上一節建立的所部署 Web 應用程式的位置。現在您可以在本機加以執行，但是由於您要著手部署公用版本，因此您必須使用外部提供者來進行裝載。

現在，讓我們來確實為 UWP 應用程式註冊推播通知。在 Visual Studio 中，按一下 [專案] > [市集] > [將應用程式與市集建立關聯]。

![](./media/notification-hubs-geofence/vs-associate-with-store.png)

一旦您登入開發人員帳戶，請務必選取現有應用程式或建立新應用程式並讓封裝與其建立關聯。

移至開發人員中心，然後開啟您剛剛建立的應用程式。按一下 [服務] > [推播通知] > [線上服務網站]。

![](./media/notification-hubs-geofence/ms-live-services.png)

記下網站上的 [應用程式密碼] 和 [封裝 SID]。在 Azure 入口網站中需要用到這兩個項目；開啟通知中樞、按一下 [設定] > [通知服務] > [Windows (WNS)]，然後在必要欄位中輸入資訊。

![](./media/notification-hubs-geofence/notification-hubs-wns.png)

按一下 [儲存]。

在 [方案總管] 中以滑鼠右鍵按一下 [參考]，然後選取 [管理 NuGet 封裝]。我們需要新增 **Microsoft Azure 服務匯流排受管理程式庫**的參考；只要搜尋 `WindowsAzure.Messaging.Managed` 並將它新增至專案即可。

![](./media/notification-hubs-geofence/vs-nuget.png)

為了進行測試，我們可以再次建立 `MainPage_Loaded` 事件處理常式，並對其新增下列程式碼片段︰

    var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

    var hub = new NotificationHub("HUB_NAME", "HUB_LISTEN_CONNECTION_STRING");
    var result = await hub.RegisterNativeAsync(channel.Uri);

    // Displays the registration ID so you know it was successful
    if (result.RegistrationId != null)
    {
        Debug.WriteLine("Reg successful.");
    }

上述程式碼使用通知中樞註冊應用程式。現在可以開時進行了！

在 `LocationHelper` 的 `Geolocator_PositionChanged` 處理常式內，您可以新增一段測試程式碼以強制將位置放在地理柵欄內︰

    await LocationHelper.SendLocationToBackend("wns", "TEST_USER", "TEST", "37.7746", "-122.3858");

因為我們不會傳遞實際座標 (目前可能不在邊界內)，而是使用預先定義的測試值，因此會在更新時看到通知出現︰

![](./media/notification-hubs-geofence/notification-hubs-test-notification.png)

##後續步驟

除了上述步驟外，您可能還需要遵循幾個步驟以確定方案已可用於生產環境。

首先，您可能需要確保地理柵欄是動態的。這需要對 Bing API 進行一些額外處理，才能在現有資料來源內上傳新邊界。如需這方面的詳細資訊，請參閱 [Bing 空間資料服務 API 說明文件](https://msdn.microsoft.com/library/ff701734.aspx)。

接著，由於您要確保會對正確的參與者進行傳遞，因此您可以透過[標記](notification-hubs-routing-tag-expressions.md)功能鎖定這些人。

在上述方案所說明的案例中，您可能會有各種不同目標平台，因此我們並未限制只有系統特有功能才能使用地理柵欄。也就是說，通用 Windows 平台內建提供了相關功能來[偵測地理柵欄](msdn.microsoft.com/zh-TW/windows/uwp/maps-and-location/set-up-a-geofence)。

如需有關通知中樞功能的詳細資訊，請查看我們的[說明文件入口網站](https://azure.microsoft.com/documentation/services/notification-hubs/)。

<!---HONumber=AcomDC_0323_2016-->