<properties linkid="notification-hubs-how-to-guides-howto-register-user-with-aspnet-webapi-windowsphonedotnet" urlDisplayName="Notify Windows Store app users by using Web API" pageTitle="Register the current user for push notifications by using Web API - Notification Hubs" metaKeywords="Azure registering application, Notification Hubs, Azure push notifications, push notification Windows Store app" description="Learn how to request push notification registration in a Windows Store app with Azure Notification Hubs when registeration is performed by ASP.NET Web API." metaCanonical="" services="notification-hubs" documentationCenter="" title="Register the current user for push notifications by using ASP.NET" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# 使用 ASP.NET 來註冊目前使用者以取得推播通知

<div class="dev-center-tutorial-selector sublanding">
<a href="/zh-TW/documentation/articles/notification-hubs-windows-store-aspnet-register-user-push-notifications/" title="Windows 市集 C#" class="current">Windows 市集 C#</a><a href="/zh-TW/documentation/articles/notification-hubs-ios-aspnet-register-user-push-notifications/" title="iOS">iOS</a>
</div>

本主題將說明以 ASP.NET Web API 執行註冊時，應如何向 Azure 通知中心要求推播通知註冊。這是[使用通知中心來通知使用者][]教學課程的延伸主題。您必須已完成該教學課程中的必要步驟，才能建立已驗證的行動服務。如需通知使用者案例的詳細資訊，請參閱[使用通知中心來通知使用者][]。

1.  使用 Visual Studio 2012，在您完成必要的教學課程[使用通知中心來通知使用者][]時所建立的專案中開啟 app.xaml.cs 檔案。

2.  找出 **InitNotificationsAsync** 方法，並將方法中的程式碼註解化。

    您將會改用 ASP.NET Web API 進行通知的註冊。

3.  在 [方案總管] 中以滑鼠右鍵按一下專案名稱，然後選取 [管理 NuGet 封裝]。

4.  在左窗格中，選取 [線上] 類別、搜尋 `json.net`、按一下 [Json.NET] 封裝上的 [安裝]，然後接受授權合約。

    ![][]

    這會將第三方 Newtonsoft.Json.dll 組件新增至您的專案。

5.  在 [方案總管] 中，使用滑鼠右鍵按一下專案，依序按一下 [新增] 和 [類別]，然後輸入 `LocalStorageManager` 並按一下 [新增]。

    ![][1]

    這會將 **LocalStorageManager** 類別的程式碼檔案新增至專案。

6.  開啟新的 LocalStorageManager.cs 專案檔案，然後新增下列 **using** 陳述式：

        using Windows.Storage;

7.  以下列程式碼取代 **LocalStorageManager** 類別定義：

        class LocalStorageManager
        {
            private static ApplicationDataContainer GetLocalStorageContainer()
            {
                if (!ApplicationData.Current.LocalSettings
                    .Containers.ContainsKey("InstallationContainer"))
                {
                    ApplicationData.Current.LocalSettings
                        .CreateContainer("InstallationContainer",                                                            
                        ApplicationDataCreateDisposition.Always);
                }
                return ApplicationData.Current.LocalSettings
                    .Containers["InstallationContainer"];
            }

            public static string GetInstallationId()
            {
                var container = GetLocalStorageContainer();
                if (!container.Values.ContainsKey("InstallationId"))
                {
                    container.Values["InstallationId"] = Guid.NewGuid().ToString();
                }
                return (string)container.Values["InstallationId"];
            }
        }

    此程式碼會建立並儲存裝置特定的安裝 ID，此 ID 在建立通知時將會當作標籤使用。如果有安裝 ID 存在，則會使用此 ID。

8.  開啟 MainPage.xaml 專案檔案，然後以下列 XAML 程式碼取代根 **Grid** 元素：

        <Grid Background="{StaticResource ApplicationPageBackgroundThemeBrush}">
            <Grid Margin="120, 58, 120, 80" >
                <Grid.RowDefinitions>
                    <RowDefinition />
                    <RowDefinition/>
                    <RowDefinition/>
                    <RowDefinition/>
                    <RowDefinition/>
                </Grid.RowDefinitions>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition MaxWidth="200"/>
                    <ColumnDefinition/>
                </Grid.ColumnDefinitions>
                <TextBlock Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="2"  
                               TextWrapping="Wrap" Text="Push To Users with Notification Hubs" 
                               FontSize="42"  VerticalAlignment="Top"/>
                <TextBlock Grid.Row="1" Grid.Column="0" Text="Installation Id:" 
                               FontSize="24" VerticalAlignment="Center" />
                <TextBlock Grid.Row="1" Grid.Column="1" Name="InstId" FontSize="24" 
                               VerticalAlignment="Center"/>
                <TextBlock Grid.Row="2" Grid.Column="0" Text="Username:" FontSize="24" 
                               VerticalAlignment="Center"/>
                <TextBox Grid.Row="2" Grid.Column="1" Name="User" FontSize="24" 
                             Width="300" Height="40" HorizontalAlignment="Left"/>
                <TextBlock Grid.Row="3" Grid.Column="0" Text="Password:" FontSize="24" 
                               VerticalAlignment="Center" />
                <PasswordBox Grid.Row="3" Grid.Column="1" Name="Password" FontSize="24" 
                                 Width="300" Height="40" HorizontalAlignment="Left"/>
                <Button Name="Login" Grid.Row="4" Grid.Column="0" Grid.ColumnSpan="2" 
                            Content="Login and Register for Push" FontSize="24" Click="Login_Click" />
            </Grid>
        </Grid>

    This code creates a UI for collecting a username and password and displaying the installation ID.

9.  回到 MainPage.xaml.cs 檔案，並新增下列 **using** 指示詞：

        using Newtonsoft.Json;
        using System.Net.Http;
        using System.Text;
        using Windows.Networking.PushNotifications;
        using Windows.UI.Popups;

10. 在 **MainPage** 類別中新增下列定義：

        private string registerUri = "https://<SERVICE_ROOT_URL>/api/register";

    使用您在＜**使用通知中樞來通知使用者**＞中建立之 Web API 的根 URI 來取代 *`<SERVICE_ROOT_URL>`*。

11. 將下列方法新增至 **MainPage** 類別：

        private async void Login_Click(object sender, RoutedEventArgs e)
        {
            // Get the info that we need to request registration.
            var installationId = InstId.Text = LocalStorageManager.GetInstallationId();
            var channel = await PushNotificationChannelManager
                .CreatePushNotificationChannelForApplicationAsync();
            var user = User.Text;
            var pwd = Password.Password;

            // Define a registration to pass in the body of the POST request.
            var registration = new Dictionary<string, string>()
                                   {{"platform", "windows"},
                                   {"instId", installationId.ToString()},
                                   {"channelUri", channel.Uri}};

            // Create a client to send the HTTP registration request.
            var client = new HttpClient();
            var request =
                new HttpRequestMessage(HttpMethod.Post, new Uri(registerUri));

            // Add the Authorization header with the basic login credentials.
            var auth = "Basic " +
                Convert.ToBase64String(Encoding.UTF8.GetBytes(user + ":" + pwd));
            request.Headers.Add("Authorization", auth);
            request.Content = new StringContent(JsonConvert.SerializeObject(registration),
                Encoding.UTF8, "application/json");

            string message;

            try
            {
                // Send the registration request.
                HttpResponseMessage response = await client.SendAsync(request);

                // Get and display the response, either the registration ID
                // or an error message.
                message = await response.Content.ReadAsStringAsync();
                message = string.Format("Registration ID: {0}", message);
            }
            catch (Exception ex)
            {
                message = ex.Message;
            }

            // Display a message dialog.
            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }

    This method gets both an installation ID and channel for push notifications and sends it, along with the device type, to the authenticated Web API method that creates a registration in Notification Hubs.此 Web API 定義於[使用通知中心來通知使用者][]中。

現在，用戶端應用程式已更新，請回到[使用通知中心來通知使用者][]，並更新行動服務，以使用通知中心傳送通知。

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [Windows 市集 C#]: /zh-TW/documentation/articles/notification-hubs-windows-store-aspnet-register-user-push-notifications/ "Windows 市集 C#"
  [iOS]: /zh-TW/documentation/articles/notification-hubs-ios-aspnet-register-user-push-notifications/ "iOS"
  [使用通知中心來通知使用者]: /zh-TW/manage/services/notification-hubs/notify-users-aspnet
  []: ./media/notification-hubs-windows-store-aspnet-register-user-push-notifications/notification-hub-add-nuget-package-json.png
  [1]: ./media/notification-hubs-windows-store-aspnet-register-user-push-notifications/notification-hub-create-aspnet-class.png
