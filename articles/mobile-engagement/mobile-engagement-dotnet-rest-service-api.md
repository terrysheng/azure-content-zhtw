<properties 
	pageTitle="使用 REST API 存取 Azure Mobile Engagement 服務 API" 
	description="描述如何使用 Mobile Engagement REST API 存取 Azure Mobile Engagement 服務 API"		
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="wesmc7777" 
	manager="erikre" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-multiple" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="03/10/2016" 
	ms.author="wesmc" />

#使用 REST 存取 Azure Mobile Engagement 服務 API

Azure Mobile Engagement 提供 [Azure Mobile Engagement REST API](https://msdn.microsoft.com/library/azure/mt683754.aspx)，讓您可以管理裝置、觸達/推送活動等。此範例會直接使用 REST API 來建立公告行銷活動，然後將其啟用並推送給一組裝置。

如果您不想直接使用 REST API，我們也提供 [Swagger 檔案](https://github.com/Azure/azure-rest-api-specs/blob/master/arm-mobileengagement/2014-12-01/swagger/mobile-engagement.json)，可以與工具搭配使用來針對您慣用的語言產生 SDK。我們建議使用 [AutoRest](https://github.com/Azure/AutoRest) 工具從我們的 Swagger 檔案產生 SDK。我們已經以類似的方式建立 .NET SDK，可讓您使用 C# 包裝函式與這些 API 互動，且您不需要自行執行驗證權杖交涉和重新整理。如果您想要逐步了解使用這個包裝函式的類似範例，請參閱[服務 API.NET SDK 範例](mobile-engagement-dotnet-sdk-service-api.md)

此範例會直接使用 REST API 來建立與啟用公告行銷活動。

## 將 user\_name 應用程式資訊新增至 Mobile Engagement 應用程式

這個範例需要將應用程式資訊標記新增至 Mobile Engagement 應用程式。在應用程式的 engagement 入口網站中，您可以藉由按一下 [設定] > [標記 (應用程式資訊)] > [新增標記 (應用程式資訊)] 來新增標記。新增名為 **user\_name** 的新標記做為**字串**類型。

![](./media/mobile-engagement-dotnet-rest-service-api/user-name-app-info.png)



如果您遵循[開始使用 Windows 通用 App 的 Azure Mobile Engagement](mobile-engagement-windows-store-dotnet-get-started.md)，您就可以只藉由覆寫 `MainPage` 類別中的 `OnNavigatedTo()` 來測試傳送 **user\_name**，以便傳送類似下列程式碼的應用程式資訊標記︰

    protected override void OnNavigatedTo(NavigationEventArgs e)
    {
        base.OnNavigatedTo(e);

        String name = "Wesley"; // Prompt the user to provide this in your client app.

        Dictionary<object, object> info = new Dictionary<object, object>();
        info.Add("user_name", name);
        EngagementAgent.Instance.SendAppInfo(info);
    }
 


## 建立服務 API 應用程式

1. 首先，您需要用於此範例的四個驗證參數。這些參數是 **SubscriptionId**、**TenantId**、**ApplicationId** 和 **Secret**。若要取得這些驗證參數，建議您使用[驗證](mobile-engagement-api-authentication.md#authentication)教學課程中單次設定 (使用指令碼) 一節下所述的 PowerShell 指令碼方法。 

2. 我們將使用簡單的 Windows 主控台應用程式，示範使用 REST Service API 建立與啟用新的公告行銷活動。開啟 Visual Studio，建立新的 [主控台應用程式]。

3. 接下來，將 **Newtonsoft.Json** NuGet 封裝新增至您的專案。

4. 在 `Program.cs` 檔案中，新增下列 `using` 陳述式做為下列命名空間︰

		using System.IO;
		using System.Net;
		using Newtonsoft.Json.Linq;
		using Newtonsoft.Json;

5. 接下來您必須在 `Program` 類別中定義下列常數。我們將使用這些常數來驗證您正在建立公告行銷活動之 Mobile Engagement 應用程式並與其互動：


        // Parameters needed for authentication of API calls.
		// These are returned from the PowerShell script in the authentication tutorial. 
		// https://azure.microsoft.com/documentation/articles/mobile-engagement-api-authentication/
        static String SubscriptionId = "<Your Subscription Id>";
        static String TenantId = "<Your TenantId>";
        static String ApplicationId = "<Your Application Id>";
        static String Secret = "<Your Secret>";

		// The token for authenticating with your Mobile Engagement app.
        static String Token = null;

        // This is the Azure Resource group concept for grouping together resources 
        // See: https://azure.microsoft.com/zh-TW/documentation/articles/resource-group-portal/
        static String ResourceGroup = "MobileEngagement";

        // For Mobile Engagement operations
        // App Collection Name from the Azure portal 
        static String Collection = "<Your App Collection Name>";

        // Application Resource Name - make sure you are using the one as specified in the dashboard of the
		// Azure portal. (This is NOT the App Name)
        static String AppName = "WesmcRestTest-windows";

		// New campaign id returned from creating the new campaign and used to activate and push the campaign
		// a set of devices.
        static String NewCampaignID = null;

		// This list will hold the device Ids we choose to push the campaign to.
        static List<String> deviceList = new List<String>();


6. 將下列兩個方法加入至 `Program` 類別。這些方法將用於執行 REST API 並顯示對主控台的回應。

        private static async Task<HttpWebResponse> ExecuteREST(string httpMethod, string uri, string authToken, WebHeaderCollection headers = null, string body = null, string contentType = "application/json")
        {
            //=== Execute the request 
            HttpWebRequest request = (HttpWebRequest)HttpWebRequest.Create(uri);
            HttpWebResponse response = null;

            request.Method = httpMethod;
            request.ContentType = contentType;
            request.ContentLength = 0;

            if (authToken != null)
                request.Headers.Add("Authorization", authToken);

            if (headers != null)
            {
                request.Headers.Add(headers);
            }

            if (body != null)
            {
                byte[] bytes = Encoding.UTF8.GetBytes(body);

                try
                {
                    request.ContentLength = bytes.Length;
                    Stream requestStream = request.GetRequestStream();
                    requestStream.Write(bytes, 0, bytes.Length);
                    requestStream.Close();
                }
                catch (Exception e)
                {
                    Console.WriteLine(e.Message);
                }
            }

            try
            {
                response = (HttpWebResponse)await request.GetResponseAsync();
            }
            catch (WebException we)
            {
                if (we.Response != null)
                {
                    response = (HttpWebResponse)we.Response;
                }
                else
                    Console.WriteLine(we.Message);
            }
            catch (Exception e)
            {
                Console.WriteLine(e.Message);
            }

            return response;
        }


        private static void DisplayResponse(dynamic data, HttpWebResponse response)
        {
            Console.WriteLine("HTTP Status " + (int)response.StatusCode + " : " + response.StatusDescription);
            Console.WriteLine(data + "\n");
        }

    }

7. 將下列程式碼新增至您的 `Main` 方法，以產生具備您收集之驗證參數的驗證權杖︰

        //***************************************************************************
        //*** Get a valid authorization token with your authentication parameters ***
        //***************************************************************************

        String methodUrl = "https://login.microsoftonline.com/" + TenantId + "/oauth2/token";
        String requestBody = "grant_type=client_credentials&client_id=" + ApplicationId +
                            "&client_secret=" + Secret +
                            "&resource=https://management.core.windows.net/";
        Console.WriteLine("Getting authorization token...\n");
        HttpWebResponse response = ExecuteREST("POST", methodUrl, null, null, requestBody, null).Result;
        Stream receiveStream = response.GetResponseStream();
        StreamReader readStream = new StreamReader(receiveStream, Encoding.UTF8);
        dynamic data = JObject.Parse(readStream.ReadToEnd());
        readStream.Close();
        receiveStream.Close();
        DisplayResponse(data, response);

        if (response.StatusCode == HttpStatusCode.OK)
        {
            Token = data.token_type + " " + data.access_token;
            Console.WriteLine("Got authorization token...");
            Console.WriteLine("Authorization : " + Token + "\n");
        }
        else
        {
            Console.WriteLine("*** Failed to get authorization token. Check your parameters for API calls.\n");
            return;
        }

8. 既然我們已經有有效的驗證權杖，我們就可以使用[建立活動](https://msdn.microsoft.com/library/azure/mt683742.aspx) REST API 建立新的觸達活動。新的活動會是簡單的**隨時**及**僅通知**公告行銷活動，包含標題和訊息。這將會是手動推送活動，如下列螢幕擷取畫面所示。這表示它將只會使用 API 推送。


	![](./media/mobile-engagement-dotnet-rest-service-api/manual-push.png)

	將下列程式碼新增至您的 `Main` 方法，以建立公告行銷活動︰

        //*****************************************************************************
        //*** Create a campaign to send a notification using the user-name app-info ***
        //*****************************************************************************

        String newCampaignMethodUrl = "https://management.azure.com/subscriptions/" + SubscriptionId + "/" +
               "resourcegroups/" + ResourceGroup + "/providers/Microsoft.MobileEngagement/appcollections/" +
               Collection + "/apps/" + AppName + "/campaigns/announcements?api-version=2014-12-01";

        String campaignRequestBody = "{ "name": "BirthdayCoupon", " +
                                        ""type": "only_notif", " +
                                        ""deliveryTime": "any", " +
                                        ""notificationType": "popup", " +
                                        ""pushMode":"manual", " +
                                        ""notificationTitle": "Happy Birthday ${user_name}", " +
                                        ""notificationMessage": "Take extra 10% off on your orders today!"}";

        Console.WriteLine("Creating new campaign...\n");
        HttpWebResponse newCampaignResponse = ExecuteREST("POST", newCampaignMethodUrl, Token, null, campaignRequestBody).Result;
        Stream receiveCampaignStream = newCampaignResponse.GetResponseStream();
        StreamReader readCampaignStream = new StreamReader(receiveCampaignStream, Encoding.UTF8);
        dynamic newCampaignData = JObject.Parse(readCampaignStream.ReadToEnd());
        readCampaignStream.Close();
        receiveCampaignStream.Close();
        DisplayResponse(newCampaignData, newCampaignResponse);

        if (newCampaignResponse.StatusCode == HttpStatusCode.Created)
        {
            NewCampaignID = newCampaignData.id;
            Console.WriteLine("Created new campaign...");
            Console.WriteLine("New Campaign Id    : " + NewCampaignID + "\n");
        }
        else
        {
            Console.WriteLine("*** Failed to create birthday campaign.\n");
            return;
        }


9. 在活動推送至任何裝置之前，必須先將其啟用。我們在 `NewCampaignID` 變數中為新的活動儲存識別碼。我們會用它做為 URI 路徑參數，以使用[啟動活動](https://msdn.microsoft.com/library/azure/mt683745.aspx) REST API 來啟用活動。即使活動只能利用 API 手動推送，這樣應該會將活動狀態變更為**已排程**。

	將下列程式碼新增至您的 `Main` 方法，以啟用公告行銷活動︰

        //******************************************
        //*** Activate the new birthday campaign ***
        //******************************************

        String activateCampaignUrl = "https://management.azure.com/subscriptions/" + SubscriptionId + "/" +
                  "resourcegroups/" + ResourceGroup + "/providers/Microsoft.MobileEngagement/appcollections/" +
                   Collection + "/apps/" + AppName + "/campaigns/announcements/" + NewCampaignID +
                   "/activate?api-version=2014-12-01";

        Console.WriteLine("Activating new campaign (ID : " + NewCampaignID + ")...\n");
        HttpWebResponse activateCampaignResponse = ExecuteREST("POST", activateCampaignUrl, Token).Result;
        Stream activateCampaignStream = activateCampaignResponse.GetResponseStream();
        StreamReader activateCampaignReader = new StreamReader(activateCampaignStream, Encoding.UTF8);
        dynamic activateCampaignData = JObject.Parse(activateCampaignReader.ReadToEnd());
        activateCampaignReader.Close();
        activateCampaignStream.Close();
        DisplayResponse(activateCampaignData, activateCampaignResponse);

        if (activateCampaignResponse.StatusCode == HttpStatusCode.OK)
        {
            Console.WriteLine("Activated new campaign...");
            Console.WriteLine("New Campaign State : " + activateCampaignData.state + "\n");
        }
        else
        {
            Console.WriteLine("*** Failed to activate birthday campaign.\n");
            return;
        }

10. 若要推送活動，我們必須提供我們想要接收通知的裝置識別碼給使用者。我們將使用[查詢裝置](https://msdn.microsoft.com/library/azure/mt683826.aspx) REST API 來取得所有裝置識別碼。如果清單有相關聯的 **user\_name** 應用程式資訊，我們會將每個裝置識別碼新增至清單。

	將下列程式碼新增至您的 `Main` 方法，以取得所有裝置識別碼，並填入 deviceList：

        //************************************************************************
        //*** Now that the manualPush campaign is activated, get the deviceIds ***
        //*** that you want to trigger the push campaign on.                   ***
        //************************************************************************

        String getDevicesUrl = "https://management.azure.com/subscriptions/" + SubscriptionId + "/" +
                  "resourcegroups/" + ResourceGroup + "/providers/Microsoft.MobileEngagement/appcollections/" +
                   Collection + "/apps/" + AppName + "/devices?api-version=2014-12-01";

        Console.WriteLine("Getting device IDs...\n");
        HttpWebResponse devicesResponse = ExecuteREST("GET", getDevicesUrl, Token).Result;
        Stream devicesStream = devicesResponse.GetResponseStream();
        StreamReader devicesReader = new StreamReader(devicesStream, Encoding.UTF8);
        dynamic devicesData = JObject.Parse(devicesReader.ReadToEnd());
        devicesReader.Close();
        devicesStream.Close();
        DisplayResponse(devicesData, devicesResponse);

        if (devicesResponse.StatusCode == HttpStatusCode.OK)
        {
            Console.WriteLine("Got devices.");
            foreach (dynamic device in devicesData.value)
            {
                // Just adding all in this example
                if (device.appInfo.user_name != null)
                {
                    Console.WriteLine("Adding device for push campaign...");
                    Console.WriteLine("Device ID    : " + device.deviceId);
                    Console.WriteLine("user_name    : " + device.appInfo.user_name);
                    deviceList.Add((String)device.deviceId);
                }
            }
            Console.WriteLine();
        }
        else
        {
            Console.WriteLine("*** Failed to get devices.\n");
			return;
        }


11. 最後，我們會使用[推送活動](https://msdn.microsoft.com/library/azure/mt683734.aspx) REST API 將活動推送至清單中的所有裝置識別碼。這是**應用程式內**通知。因此應用程式必須在裝置上執行才能讓使用者接收它。

	將下列程式碼新增至您的 `Main` 方法，以將活動推送到 deviceList 中的裝置︰

        //**************************************************************
        //*** Trigger the manualPush campaign on the desired devices ***
        //**************************************************************

        String pushCampaignUrl = "https://management.azure.com/subscriptions/" + SubscriptionId + "/" +
                  "resourcegroups/" + ResourceGroup + "/providers/Microsoft.MobileEngagement/appcollections/" +
                   Collection + "/apps/" + AppName + "/campaigns/announcements/" + NewCampaignID + 
                   "/push?api-version=2014-12-01";

        Console.WriteLine("Triggering push for new campaign (ID : " + NewCampaignID + ")...\n");
        String deviceIds = "{"deviceIds":" + JsonConvert.SerializeObject(deviceList) + "}";
        Console.WriteLine("\n" + deviceIds + "\n");
        HttpWebResponse pushDevicesResponse = ExecuteREST("POST", pushCampaignUrl, Token, null, deviceIds).Result;
        Stream pushDevicesStream = pushDevicesResponse.GetResponseStream();
        StreamReader pushDevicesReader = new StreamReader(pushDevicesStream, Encoding.UTF8);
        dynamic pushDevicesData = JObject.Parse(pushDevicesReader.ReadToEnd());
        pushDevicesReader.Close();
        pushDevicesStream.Close();
        DisplayResponse(pushDevicesData, pushDevicesResponse);

        if (pushDevicesResponse.StatusCode == HttpStatusCode.OK)
        {
            Console.WriteLine("Triggered push on new campaign.\n");
        }
        else
        {
            Console.WriteLine("*** Failed to push campaign to the device list.\n");
        }


12. 建置並執行您的主控台應用程式。您的輸出應該類似如下範例：


		C:\Users\Wesley\AzME_Service_API_Rest\test.exe

		Getting authorization token...
		
		HTTP Status 200 : OK
		{
		  "token_type": "Bearer",
		  "expires_in": "3599",
		  "expires_on": "1458085162",
		  "not_before": "1458081262",
		  "resource": "https://management.core.windows.net/",
		  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBPW
		b3dzLm5ldC8iLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFh
		NzE4LTQ0YzQtOGVjMS0xM2IwODExMTRmM2UiLCJhcHBpZGFjciI6IjEiLCJpZHAiOiJodHRwczovL3N0cy53
		MTdhNGJkIiwic3ViIjoiOWIzZGQ2MDctNmYwOC00Y2Y5LTk2N2YtZmUyOGU3MTdhNGJkIiwidGlkIjoiNzJm
		F5x9gj8JJ4CjtLaH3mm1_U22Qc_AjB9mtbM97dgu3kCiUm19ISatRBoAmVz3JGW6LSv2TyCeg9TGYVrE3OAE
		hl_pY9COXicc7I501_X67GsmUgs9-EedF3STrEoY5cONTiMvwCLfeBgScgXA0ikAu62KpzMu0VFDYu-HASI8
		}
		
		Got authorization token...
		Authorization : Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNN
		aW5kb3dzLm5ldC8iLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYt
		Zi1jNzE4LTQ0YzQtOGVjMS0xM2IwODExMTRmM2UiLCJhcHBpZGFjciI6IjEiLCJpZHAiOiJodHRwczovL3N0
		OGU3MTdhNGJkIiwic3ViIjoiOWIzZGQ2MDctNmYwOC00Y2Y5LTk2N2YtZmUyOGU3MTdhNGJkIiwidGlkIjoi
		iI-oF5x9gj8JJ4CjtLaH3mm1_U22Qc_AjB9mtbM97dgu3kCiUm19ISatRBoAmVz3JGW6LSv2TyCeg9TGYVrE
		vsf3hl_pY9COXicc7I501_X67GsmUgs9-EedF3STrEoY5cONTiMvwCLfeBgScgXA0ikAu62KpzMu0VFDYu-H
		
		Creating new campaign...
		
		HTTP Status 201 : Created
		{
		  "id": 24,
		  "state": "draft"
		}
		
		Created new campaign...
		New Campaign Id    : 24
		
		Activating new campaign (ID : 24)...
		
		HTTP Status 200 : OK
		{
		  "id": 24,
		  "state": "scheduled"
		}
		
		Activated new campaign...
		New Campaign State : scheduled
		
		Getting device IDs...
		
		HTTP Status 200 : OK
		{
		  "value": [
		    {
		      "meta": {
		        "lastSeen": 1458080534371,
		        "firstSeen": 1458080534371,
		        "lastLocation": 1458081389617,
		        "lastInfo": 1458080571603
		      },
		      "appInfo": {
		        "user_name": "Wesley"
		      },
		      "deviceId": "1d6208b8f281203ecb49431e2e5ce6b3"
		    },
		    {
		      "meta": {
		        "lastSeen": 1457990584698,
		        "firstSeen": 1457949384025,
		        "lastLocation": 1457990914895,
		        "lastInfo": 1457990584597
		      },
		      "appInfo": {
		        "user_name": "Wesley"
		      },
		      "deviceId": "302486644890e26045884ee5aa0619ec"
		    }
		  ]
		}
		
		Got devices.
		Adding device for push campaign...
		Device ID    : 1d6208b8f281203ecb49431e2e5ce6b3
		user_name    : Wesley
		Adding device for push campaign...
		Device ID    : 302486644890e26045884ee5aa0619ec
		user_name    : Wesley
		
		Triggering push for new campaign (ID : 24)...
		
		
		{"deviceIds":["1d6208b8f281203ecb49431e2e5ce6b3","302486644890e26045884ee5aa0619ec"]}
		
		HTTP Status 200 : OK
		{
		  "invalidDeviceIds": []
		}
		
		Triggered push on new campaign.
		


<!-- Images. -->

[1]: ./media/mobile-engagement-dotnet-sdk-service-api/include-prerelease.png

<!---HONumber=AcomDC_0323_2016-->