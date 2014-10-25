<properties title="Azure Notification Hubs Notify Users" pageTitle="Azure Notification Hubs Notify Users" metaKeywords="Azure push notifications, Azure notification hubs" description="Learn how to send secure push notifications in Azure. Code samples written in C# using the .NET API." documentationCenter="Mobile" metaCanonical="" disqusComments="1" umbracoNaviHide="0" authors="sethm" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="sethm"></tags>

# Azure 通知中心通知使用者

<div class="dev-center-tutorial-selector sublanding"> 
<a href="/zh-tw/documentation/articles/notification-hubs-windows-dotnet-notify-users/" title="Windows Universal" class="current">Windows Universal</a><a href="/zh-tw/documentation/articles/notification-hubs-aspnet-backend-ios-notify-users/" title="iOS">iOS</a>
<a href="/zh-tw/documentation/articles/notification-hubs-aspnet-backend-android-notify-users/" title="Android">Android</a>
</div>

Azure 中的推播通知支援可讓您存取易於使用、多重平台的大規模推播基礎結構，而大幅簡化消費者和企業應用程式在行動平台上的推播通知實作。本教學課程將示範如何使用 Azure 通知中心，來將推播通知傳送到特定裝置上的特定應用程式使用者。ASP.NET WebAPI 後端可用來驗證用戶端並產生通知，如指引主題[從您的應用程式後端註冊][]中所示。本教學課程會以您在**開始使用通知中心**教學課程中所建立的通知中心為基礎。

本教學課程還是**安全推播**教學課程的必要條件。完成本**通知使用者**教學課程後，您可以繼續進行**安全推播**教學課程，該教學課程說明如何修改**通知使用者**程式碼，來以安全的方式傳送推播通知。

> [AZURE.NOTE] 本教學課程假設您已如[開始使用通知中心 (Windows Store)][] 中所述建立並設定通知中心。
> 並請注意，在本教學課程中，您將建立 Windows Phone 8.1 應用程式。相同的程式碼也可用於 Windows Store 和 Windows Universal 應用程式。這些應用程式都必須使用 Windows (非 Windows Phone) 認證。

## 建立與設定通知中心

在您開始本教學課程之前，您必須保留應用程式名稱，然後建立 Azure 通知中心並將它連接到該應用程式。請依照[開始使用通知中心 (Windows Store)][] 中的步驟進行；尤其是[向 Windows 市集註冊您的應用程式][]一節和[設計您的通知中心][]一節。尤其請確定您已在入口網站的通知中心內，輸入 [設定] 索引標籤中的 [封裝 SID] 和 [用戶端密碼] 值。此組態程序會在[設定您的通知中心][設計您的通知中心]一節中加以說明。這個步驟非常重要：如果入口網站上的認證與您為所選應用程式名稱指定的認證不符，則推播通知將無法順利進行。

[WACOM.INCLUDE [notification-hubs-aspnet-backend-notifyusers][]]

## 建立 Windows Phone 專案

下一個步驟說明如何建立 Windows Phone 應用程式。若要將此專案新增至目前的解決方案，請執行下列動作：

1.  在 **[方案總管]** 中，以滑鼠右鍵按一下解決方案的最上層節點 (在此案例中為 **Solution NotifyUsers**)，然後按一下 [新增]，再按一下 [新增專案]。

2.  展開 **[市集應用程式]**，然後按一下 **[Windows Phone 應用程式]**，再按一下 **[空白應用程式 (Windows Phone)]**。

    ![][]

3.  在 **[名稱]** 方塊中輸入 **NotifyUserWindowsPhone**，然後按一下 **[確定]** 以產生專案。

4.  將此應用程式與 Windows Phone 市集建立關聯。在 **[方案總管]** 中，以滑鼠右鍵按一下 **NotifyUserWindowsPhone (Windows Phone 8.1)**，然後按一下 [市集]，再按一下 [將應用程式與市集建立關聯...]。

    ![][1]

5.  依照精靈中的步驟登入，並將應用程式與市集建立關聯。

    ![][2]

    > [AZURE.NOTE] 請務必記下您在此程序期間所選的應用程式名稱。您必須使用從 [Windows Dev Center][] 中取得的認證，為這個特定保留的應用程式名稱，在入口網站上設定通知中心。此組態程序會在[設定您的通知中心][設計您的通知中心]中加以說明。這個步驟非常重要：如果入口網站上的認證與您為所選應用程式名稱指定的認證不符，則推播通知將無法順利進行。

6.  在 [方案總管] 中，以滑鼠右鍵按一下 **NotifyUserWindowsPhone (Windows Phone 8.1)** 專案，然後按一下 [Manage NuGet Packages]。

7.  在左側，按一下 **[線上]**。

8.  在 **[搜尋]** 方塊中，輸入 **Http Client**。

9.  按一下結果清單中的 **Microsoft HTTP Client Libraries**，然後按一下 [安裝]。完成安裝。

10. 回到 NuGet **[搜尋]** 方塊，輸入 **Json.net**。安裝 **Json.NET** 套件，然後關閉 [NuGet Package Manager] 視窗。

11. 在 [方案總管] 的 **NotifyUserWindowsPhone (Windows Phone 8.1)** 專案中，連按兩下 **MainPage.xaml**，在 Visual Studio 編輯器中開啟該檔案。

12. 在 **MainPage.xaml** XML 程式碼中，使用下列程式碼來取代 `<Grid>` 區段：

        <Grid>
            <Grid.RowDefinitions>
                <RowDefinition Height="Auto"/>
                <RowDefinition Height="*"/>
            </Grid.RowDefinitions>

            <TextBlock Grid.Row="0" Text="Secure Push" HorizontalAlignment="Center" FontSize="48"/>

            <StackPanel Grid.Row="1" VerticalAlignment="Center">
                <Grid>
                    <Grid.RowDefinitions>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="*"/>
                    </Grid.RowDefinitions>
                    <TextBlock Grid.Row="0" Text="Username" FontSize="24" Margin="20,0,20,0"/>
                    <TextBox Name="UsernameTextBox" Grid.Row="1" Margin="20,0,20,0"/>
                    <TextBlock Grid.Row="2" Text="Password" FontSize="24" Margin="20,0,20,0" />
                    <PasswordBox Name="PasswordTextBox" Grid.Row="3" Margin="20,0,20,0"/>

                    <Button Grid.Row="4" HorizontalAlignment="Center" VerticalAlignment="Center" Content="1. Login and register" Click="LoginAndRegisterClick" />

                    <Button Grid.Row="5" HorizontalAlignment="Center" VerticalAlignment="Center" Content="2. Send push" Click="PushClick" />
                </Grid>
            </StackPanel>
        </Grid>

13. 在 [方案總管] 中，以滑鼠右鍵按一下 **NotifyUserWindowsPhone (Windows Phone 8.1)** 專案，然後按一下 [新增]，再按一下 [類別]。將類別命名為 **RegisterClient.cs**，然後按一下 [確定]**OK** 以產生類別。為註冊推播通知，此元件會實作連絡應用程式後端所需的 REST 呼叫。它也會在本機儲存通知中心所建立的 *registrationIds*，如[從您的應用程式後端註冊][]中的詳細說明。請注意，當您按一下 [Log in and register] 按鈕時，系統便會使用儲存在本機儲存體中的授權權杖。

14. 在 `RegisterClient` 類別定義中新增下列程式碼。請確定使用在上一節中所取得的後端端點來取代 `{backend endpoint}`：

        private string POST_URL = "{backend endpoint}/api/register";

        private class DeviceRegistration
        {
            public string Platform { get; set; }
            public string Handle { get; set; }
            public string[] Tags { get; set; }
        }

        public async Task RegisterAsync(string handle, IEnumerable<string> tags)
        {
            var regId = await RetrieveRegistrationIdOrRequestNewOneAsync();

            var deviceRegistration = new DeviceRegistration
            {
                Platform = "wns",
                Handle = handle,
                Tags = tags.ToArray<string>()
            };

            var statusCode = await UpdateRegistrationAsync(regId, deviceRegistration);

            if (statusCode == HttpStatusCode.Gone)
            {
                // regId is expired, deleting from local storage & recreating
                var settings = ApplicationData.Current.LocalSettings.Values;
                settings.Remove("__NHRegistrationId");
                regId = await RetrieveRegistrationIdOrRequestNewOneAsync();
                statusCode = await UpdateRegistrationAsync(regId, deviceRegistration);
            }

            if (statusCode != HttpStatusCode.Accepted)
            {
                // log or throw
            }
        }

        private async Task<HttpStatusCode> UpdateRegistrationAsync(string regId, DeviceRegistration deviceRegistration)
        {
            using (var httpClient = new HttpClient())
            {
                var settings = ApplicationData.Current.LocalSettings.Values;
                httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string) settings["AuthenticationToken"]);

                var putUri = POST_URL + "/" + regId;

                string json = JsonConvert.SerializeObject(deviceRegistration);
                                var response = await httpClient.PutAsync(putUri, new StringContent(json, Encoding.UTF8, "application/json"));
                return response.StatusCode;
            }
        }

        private async Task<string> RetrieveRegistrationIdOrRequestNewOneAsync()
        {
            var settings = ApplicationData.Current.LocalSettings.Values;
            if (!settings.ContainsKey("__NHRegistrationId"))
            {
                using (var httpClient = new HttpClient())
                {
                    httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string)settings["AuthenticationToken"]);

                    var response = await httpClient.PostAsync(POST_URL, new StringContent(""));
                    if (response.IsSuccessStatusCode)
                    {
                        string regId = await response.Content.ReadAsStringAsync();
                        regId = regId.Substring(1, regId.Length - 2);
                        settings.Add("__NHRegistrationId", regId);
                    }
                    else
                    {
                        throw new Exception();
                    }
                }
            }
            return (string)settings["__NHRegistrationId"];

        }

15. 在 RegisterClient.cs 檔案開頭處新增下列 `using` 陳述式：

        using Windows.Storage;
        using System.Net;
        using System.Net.Http;
        using System.Net.Http.Headers;
        using Newtonsoft.Json;

16. 在 MainPage.xaml.cs 中新增按鈕的程式碼。**Log in and register** 的回呼會在本機儲存體中儲存基本驗證權杖 (請注意，這代表驗證結構描述使用的任何權杖)，然後使用 `RegisterClient` 來呼叫後端。**AppBackend** 的回呼會呼叫後端，以觸發安全通知到這位使用者的所有裝置。

    將下列程式碼新增至 MainPage.xaml.cs 中的 `OnNavigatedTo()` 方法後面。請確定使用在上一節中所取得的後端端點來取代 `{backend endpoint}`：

        private async void PushClick(object sender, RoutedEventArgs e)
        {
            var POST_URL = "{backend endpoint}/api/notifications";

            using (var httpClient = new HttpClient())
            {
                var settings = ApplicationData.Current.LocalSettings.Values;
                httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string) settings["AuthenticationToken"]);

                await httpClient.PostAsync(POST_URL, new StringContent(""));
            }
        }

        private async void LoginAndRegisterClick(object sender, RoutedEventArgs e)
        {
            SetAuthenticationTokenInLocalStorage();

            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
            await new RegisterClient().RegisterAsync(channel.Uri, new string[] { "myTag" });

            var dialog = new MessageDialog("Registered as: " + UsernameTextBox.Text);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }

        private void SetAuthenticationTokenInLocalStorage()
        {
            string username = UsernameTextBox.Text;
            string password = PasswordTextBox.Password;

            var token = Convert.ToBase64String(System.Text.Encoding.UTF8.GetBytes(username + ":" + password));
            ApplicationData.Current.LocalSettings.Values["AuthenticationToken"] = token;
        }

17. 在 MainPage.xaml.cs 檔案開頭處新增下列 `using` 陳述式：

        using System.Net.Http;
        using Windows.Storage;
        using System.Net.Http.Headers;
        using Windows.Networking.PushNotifications;
        using Windows.UI.Popups;

## 執行應用程式

若要執行應用程式，請執行下列動作：

1.  在 Visual Studio 中，執行 **AppBackend** Web API 應用程式。[ASP.NET Web] 頁面便會隨即顯示。

2.  在 Visual Studio 中，執行 **NotifyUserWindowsPhone (Windows Phone 8.1)** Windows Phone 應用程式。Windows Phone 模擬器便會執行，並自動載入此應用程式。

3.  在 **NotifyUserWindowsPhone** 應用程式 UI 中，輸入使用者名稱和密碼。這些可以是任何字串，但必須是相同值。

4.  在 **NotifyUserWindowsPhone** 應用程式 UI 中，按一下 [Log in and register]。然後按一下 **[傳送推播]**。

  [Windows Universal]: /zh-tw/documentation/articles/notification-hubs-windows-dotnet-notify-users/ "Windows Universal"
  [iOS]: /zh-tw/documentation/articles/notification-hubs-aspnet-backend-ios-notify-users/ "iOS"
  [Android]: /zh-tw/documentation/articles/notification-hubs-aspnet-backend-android-notify-users/ "Android"
  [從您的應用程式後端註冊]: http://msdn.microsoft.com/en-us/library/dn743807.aspx
  [開始使用通知中心 (Windows Store)]: http://azure.microsoft.com/zh-tw/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
  [向 Windows 市集註冊您的應用程式]: http://azure.microsoft.com/zh-tw/documentation/articles/notification-hubs-windows-store-dotnet-get-started/#register
  [設計您的通知中心]: http://azure.microsoft.com/zh-tw/documentation/articles/notification-hubs-windows-store-dotnet-get-started/#configure-hub
  [notification-hubs-aspnet-backend-notifyusers]: ../includes/notification-hubs-aspnet-backend-notifyusers.md
  []: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push9.png
  [1]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push10.png
  [2]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push11.png
  [Windows Dev Center]: http://go.microsoft.com/fwlink/p/?linkid=266582&clcid=0x409
