<properties linkid="manage-services-web-sites-operating-system-functionality-available-to-applications" urlDisplayName="Operating System Functionality Available to Applications on Azure Web Sites" pageTitle="Operating System Functionality Available to Applications on Azure Web Sites" metaKeywords="" description="Learn about the common baseline operating system functionality that is available to all applications running on Azure Web Sites, including file access, network access, and registry access." metaCanonical="" services="notification-hubs" documentationCenter="" title="Use Notification Hubs to send breaking news" authors="glenga" solutions="" manager="" editor="" />

使用通知中心傳送即時新聞
========================

[Windows 市集 C\#](/en-us/manage/services/notification-hubs/breaking-news-dotnet "Windows 市集 C#")[Windows Phone](/en-us/manage/services/notification-hubs/breaking-news-wp8 "Windows Phone")[iOS](/en-us/manage/services/notification-hubs/breaking-news-ios "iOS")

本主題將說明如何使用 Azure 通知中心，將即時新聞通知廣播至 Windows Phone 應用程式。完成時，您便能夠註冊您所感興趣的即時新聞類別，並僅接收這些類別的推播通知。此情況是許多應用程式的共同模式，這些應用程式必須將通知傳送給先前宣告對通知有興趣的使用者群組，例如，RSS 閱讀程式、供樂迷使用的應用程式等等。

在通知中心內建立註冊時，您可以透過包含一或多個 *tags* 來啟用廣播案例。當標籤收到通知時，所有已註冊此標籤的裝置都會收到通知。由於標籤只是簡單的字串而已，您無需預先佈建標籤。如需標籤的詳細資訊，請參閱[通知中心指引](http://msdn.microsoft.com/en-us/library/jj927170.aspx)。

本教學課程會逐步引導您完成啟用此案例的基本步驟：

1.  [在應用程式中新增類別選項](#adding-categories)
2.  [註冊通知](#register)
3.  [從後端傳送通知](#send)
4.  [執行應用程式並產生通知](#test-app)

本主題會以您在[開始使用通知中心](/en-us/manage/services/notification-hubs/get-started-notification-hubs-wp8/)中所建立的應用程式為基礎。開始本教學課程之前，您必須已完成[開始使用通知中心](/en-us/manage/services/notification-hubs/get-started-notification-hubs-wp8/)。

在應用程式中新增類別選項
------------------------

第一個步驟是在您的現有主頁面上新增 UI 元素，以便使用者選取要註冊的類別。使用者所選取的類別會儲存在裝置上。啟動應用程式時，您的通知中心內會建立以所選取類別作為標籤的裝置註冊。

1.  開啟 MainPage.xaml 專案檔案，然後以下列程式碼取代名為 `TitlePanel` 和 `ContentPanel` 的 **Grid** 元素：

        <StackPanel x:Name="TitlePanel" Grid.Row="0" Margin="12,17,0,28">
            <TextBlock Text="Breaking News" Style="{StaticResource PhoneTextNormalStyle}" Margin="12,0"/>
            <TextBlock Text="Categories" Margin="9,-7,0,0" Style="{StaticResource PhoneTextTitle1Style}"/>
        </StackPanel>

        <Grid Name="ContentPanel" Grid.Row="1" Margin="12,0,12,0">
            <Grid.RowDefinitions>
                <RowDefinition Height="auto"/>
                <RowDefinition Height="auto" />
                <RowDefinition Height="auto" />
                <RowDefinition Height="auto" />
            </Grid.RowDefinitions>
            <Grid.ColumnDefinitions>
                <ColumnDefinition />
                <ColumnDefinition />
            </Grid.ColumnDefinitions>
            <CheckBox Name="WorldCheckBox" Grid.Row="0" Grid.Column="0">World</CheckBox>
            <CheckBox Name="PoliticsCheckBox" Grid.Row="1" Grid.Column="0">Politics</CheckBox>
            <CheckBox Name="BusinessCheckBox" Grid.Row="2" Grid.Column="0">Business</CheckBox>
            <CheckBox Name="TechnologyCheckBox" Grid.Row="0" Grid.Column="1">Technology</CheckBox>
            <CheckBox Name="ScienceCheckBox" Grid.Row="1" Grid.Column="1">Science</CheckBox>
            <CheckBox Name="SportsCheckBox" Grid.Row="2" Grid.Column="1">Sports</CheckBox>
            <Button Name="SubscribeButton" Content="Subscribe" HorizontalAlignment="Center" Grid.Row="3" Grid.Column="0" Grid.ColumnSpan="2" Click="SubscribeButton_Click" />
        </Grid>

2.  在此專案中，建立名為 **Notifications** 的新類別，將 **public** 修飾詞新增至類別定義，然後在新的程式碼檔案中新增下列 **using** 陳述式：

        using Microsoft.Phone.Notification;
        using Microsoft.WindowsAzure.Messaging;
        using System.IO.IsolatedStorage;

3.  將下列程式碼複製到新的 **Notifications** 類別：

		private NotificationHub hub;

		public Notifications()
		{
		    hub = new NotificationHub("<hub name>", "<connection string with listen access>");
		}
		
		public async Task StoreCategoriesAndSubscribe(IEnumerable<string> categories)
		{
		    var categoriesAsString = string.Join(",", categories);
		    var settings = IsolatedStorageSettings.ApplicationSettings;
		    if (!settings.Contains("categories"))
		    {
		        settings.Add("categories", categoriesAsString);
		    }
		    else
		    {
		        settings["categories"] = categoriesAsString;
		    }
		    settings.Save();
		
		    await SubscribeToCategories(categories);
		}
		
		public async Task SubscribeToCategories(IEnumerable<string> categories)
		{
		    var channel = HttpNotificationChannel.Find("MyPushChannel");
		
		    if (channel == null)
		    {
		        channel = new HttpNotificationChannel("MyPushChannel");
		        channel.Open();
		        channel.BindToShellToast();
		    }
		
		    await hub.RegisterNativeAsync(channel.ChannelUri.ToString(), categories);
		}

    本類別會使用本機儲存體來儲存此裝置必須接收的新聞類別。它也包含註冊這些類別的方法。

4.  在上述程式碼中，請使用您的通知中心名稱及先前取得的 *DefaultListenSharedAccessSignature* 連線字串，來取代 `<hub name>` 和 `<connection string with listen access>` 預留位置。

    **注意**

    因為隨用戶端應用程式散佈的憑證通常不安全，您應只將接聽存取權的金鑰隨用戶端應用程式散佈。您的應用程式可透過接聽存取權來註冊通知，但無法修改現有的註冊或無法傳送通知。在安全的後端服務中，會使用完整存取金鑰來傳送通知和變更現有的註冊。

5.  在 App.xaml.cs 專案檔案中，新增下列屬性至 **App** 類別：

        public Notifications notifications = new Notifications();

    此屬性可用來建立並存取 **Notifications** 執行個體。

6.  在 MainPage.xaml.cs 中新增下列程式碼行：

        using Windows.UI.Popups;

7.  在 MainPage.xaml.cs 專案檔案中新增下列方法：

		private async void SubscribeButton_Click(object sender, RoutedEventArgs e)
		{
		    var categories = new HashSet<string>();
		    if (WorldCheckBox.IsChecked == true) categories.Add("World");
		    if (PoliticsCheckBox.IsChecked == true) categories.Add("Politics");
		    if (BusinessCheckBox.IsChecked == true) categories.Add("Business");
		    if (TechnologyCheckBox.IsChecked == true) categories.Add("Technology");
		    if (ScienceCheckBox.IsChecked == true) categories.Add("Science");
		    if (SportsCheckBox.IsChecked == true) categories.Add("Sports");
		
		    await ((App)Application.Current).notifications.StoreCategoriesAndSubscribe(categories);
		
		    MessageBox.Show("Subscribed to: " + string.Join(",", categories));
		}

    此方法會建立一份類別清單，並使用 **Notifications** 類別在本機儲存體中儲存清單，並在通知中心註冊對應標籤。變更類別時，系統會使用新類別重新建立註冊。

您的應用程式現在可以在裝置上的本機儲存體中儲存一組類別，並在使用者每次變更類別選項時在通知中心註冊。

註冊通知
--------

這些步驟會在啟動時，使用已儲存在本機儲存體中的類別在通知中心註冊。

**注意**

由於 Microsoft 推播通知服務 (WPNS) 所指派的通道 URI 可能隨時會變更，因此您應經常註冊通知以避免通知失敗。此範例會在應用程式每次啟動時註冊通知。若是經常執行 (一天多次) 的應用程式，如果距離上次註冊的時間不到一天，則您可能可以略過註冊以保留頻寬。

1.  在 **Notifications** 類別中新增下列程式碼：

		public IEnumerable<string> RetrieveCategories()
		{
		    var categories = (string)IsolatedStorageSettings.ApplicationSettings["categories"];
		    return categories != null ? categories.Split(',') : new string[0];
		}

    這會傳回類別中所定義的類別。

2.  開啟 App.xaml.cs 檔案，並將 **async** 修飾詞新增至 **Application\_Launching** 方法。

3.  在 **Application\_Launching** 方法中，找出您在[開始使用通知中心](/en-us/manage/services/notification-hubs/get-started-notification-hubs-wp8/)中新增的通知中心註冊程式碼，並將其取代為下列程式碼行：

        await notifications.SubscribeToCategories(notifications.RetrieveCategories());

    這會確保應用程式每次啟動時都會從本機儲存體擷取類別，並要求這些類別的註冊。

4.  在 MainPage.xaml.cs 專案檔案中，新增下列實作 **OnNavigatedTo** 方法的程式碼：

		protected override void OnNavigatedTo(NavigationEventArgs e)
		{
		    var categories = ((App)Application.Current).notifications.RetrieveCategories();
		
		    if (categories.Contains("World")) WorldCheckBox.IsChecked = true;
		    if (categories.Contains("Politics")) PoliticsCheckBox.IsChecked = true;
		    if (categories.Contains("Business")) BusinessCheckBox.IsChecked = true;
		    if (categories.Contains("Technology")) TechnologyCheckBox.IsChecked = true;
		    if (categories.Contains("Science")) ScienceCheckBox.IsChecked = true;
		    if (categories.Contains("Sports")) SportsCheckBox.IsChecked = true;
		}

    這會根據原先儲存的類別狀態更新主頁面。

現在已完成此應用程式，且可在裝置本機儲存體中儲存一組類別，以供每次使用者變更類別選項在通知中心註冊時使用。接著，我們會定義可將類別通知傳送至此應用程式的後端。

傳送通知從後端傳送通知
----------------------

[WACOM.INCLUDE [notification-hubs-back-end](../includes/notification-hubs-back-end.md)]

執行應用程式並產生通知
----------------------

1.  在 Visual Studio 中，按 F5 以編譯並啟動應用程式。

    ![](./media/notification-hubs-windows-phone-send-breaking-news/notification-hub-breakingnews.png)

    請注意，應用程式 UI 提供一組切換，可讓您選擇要訂閱的類別。

2.  啟用一或多個類別切換，然後按一下 **[訂閱]**。

    應用程式會將選取的類別轉換成標籤，並在通知中心內為選取的標籤要求新裝置註冊。系統會傳回已註冊類別並顯示在對話方塊中。

    ![](./media/notification-hubs-windows-phone-send-breaking-news/notification-hub-registration.png)

3.  若要從後端傳送新通知，您可以使用下列其中一種方式：

    -   **主控台應用程式：**啟動主控台應用程式。

    -   **行動服務：**依序按一下 **[排程器]** 索引標籤、工作，然後按一下 **[執行一次]**。

    選取的類別通知會以快顯通知方式出現。

    ![](./media/notification-hubs-windows-phone-send-breaking-news/notification-hub-toast.png)

您已完成本主題。

