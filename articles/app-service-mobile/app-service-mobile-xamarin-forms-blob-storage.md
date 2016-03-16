<properties
    pageTitle="連接至 Xamarin.Forms 應用程式中的 Azure 儲存體"
    description="將映像新增至 todo 清單 Xamarin.Forms 行動應用程式，方法是連接到 Azure Blob 儲存體"
    documentationCenter="xamarin"
    authors="lindydonna"
    manager="dwrede"
    editor=""
    services="app-service\mobile"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-ios"
    ms.devlang="dotnet"
    ms.topic="article"
	ms.date="02/03/2016"
    ms.author="donnam"/>

#連接至 Xamarin.Forms 應用程式中的 Azure 儲存體

## 概觀

Azure Mobile Apps 用戶端與伺服器 SDK 支援離線同步處理結構化資料與針對 /tables 端點的 CRUD 作業。通常這項資料會儲存在資料庫或類似的存放區，而且這些資料存放區通常無法有效地儲存大型二進位資料。此外，某些應用程式有關聯的資料會儲存在其他地方 (例如，blob 儲存體、檔案共用)，能夠建立 /tables 端點中的記錄與其他資料之間的關聯相當有用。

本主題說明如何將對映像的支援新增至 Mobile Apps todo 清單快速入門。您必須先完成[建立 Xamarin.Forms 應用程式]教學課程。

在本教學課程中，您將建立儲存體帳戶，並將連接字串新增至您的行動應用程式後端。然後您要將來自新的 Mobile Apps 類型 `StorageController<T>` 的新繼承新增至伺服器專案。

>[AZURE.TIP] 本教學課程有[隨附範例](https://azure.microsoft.com/documentation/samples/app-service-mobile-dotnet-todo-list-files/)可用，可以部署到您自己的 Azure 帳戶。

## 必要條件

若要完成此教學課程，您需要下列項目：

* 使用中的 Azure 帳戶。如果您沒有帳戶，可以註冊 Azure 試用版並取得最多 10 個免費的行動應用程式，即使在試用期結束之後仍可繼續使用這些應用程式。如需詳細資訊，請參閱 [Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。

* [Visual Studio Community 2013] 或更新版本。

* 已安裝 [Xcode](https://go.microsoft.com/fwLink/?LinkID=266532) v7.0 或更新版本，以及 [Xamarin Studio](http://xamarin.com/download) 的 Mac。

* 完成[建立 Xamarin.Forms 應用程式]教學課程。本文使用該教學課程中的已完成應用程式。

>[AZURE.NOTE] 如果您想在註冊 Azure 帳戶之前先開始使用 Azure App Service，請前往[試用 App Service](https://tryappservice.azure.com/?appServiceName=mobile)。您可以於該處，在 App Service 中立即建立短期的入門行動 app - 不需信用卡，不需任何承諾。

## 建立儲存體帳戶

1. 遵循[建立 Azure 儲存體帳戶]教學課程，建立儲存體帳戶。 

2. 在 Azure 入口網站中，瀏覽至新建立的儲存體帳戶，並按一下 [金鑰] 圖示。複製 [主要連接字串]。

3. 瀏覽至行動應用程式後端。在 [所有設定] -> [應用程式設定] -> [連接字串] 底下，建立名為 `MS_AzureStorageAccountConnectionString` 的新金鑰，並使用從儲存體帳戶複製的值。使用 [自訂] 做為金鑰類型。

## 將儲存體控制器新增至您的伺服器專案

1. 在 Visual Studio 中，開啟您的 .NET 伺服器專案。新增 NuGet 封裝 [Microsoft.Azure.Mobile.Server.Files]。請確定選取 [包含發行前版本]。

2. 在 Visual Studio 中，開啟您的 .NET 伺服器專案。以滑鼠右鍵按一下 [控制器] 資料夾，然後選取 [新增] -> [控制器] -> [Web API 2 Controller - Empty]。將控制器命名為 `TodoItemStorageController`。

3. 加入下列 using 陳述式：

        using Microsoft.Azure.Mobile.Server.Files;
        using Microsoft.Azure.Mobile.Server.Files.Controllers;

4. 將基底類別變更為 `StorageController`：
    
        public class TodoItemStorageController : StorageController<TodoItem>

5. 將下列方法加入至類別：

        [HttpPost]
        [Route("tables/TodoItem/{id}/StorageToken")]
        public async Task<HttpResponseMessage> PostStorageTokenRequest(string id, StorageTokenRequest value)
        {
            StorageToken token = await GetStorageTokenAsync(id, value);

            return Request.CreateResponse(token);
        }

        // Get the files associated with this record
        [HttpGet]
        [Route("tables/TodoItem/{id}/MobileServiceFiles")]
        public async Task<HttpResponseMessage> GetFiles(string id)
        {
            IEnumerable<MobileServiceFile> files = await GetRecordFilesAsync(id);

            return Request.CreateResponse(files);
        }

        [HttpDelete]
        [Route("tables/TodoItem/{id}/MobileServiceFiles/{name}")]
        public Task Delete(string id, string name)
        {
            return base.DeleteFileAsync(id, name);
        }

6. 更新 Web API 組態以設定屬性路由。在 **Startup.MobileApp.cs** 中，將下面這行新增到 `ConfigureMobileApp()` 方法中 `config` 變數的定義之後：

        config.MapHttpAttributeRoutes();

7. 將您的伺服器專案發佈至行動應用程式後端。

###由儲存體控制器註冊的路由

新的 `TodoItemStorageController` 會在它管理的記錄下公開兩個子資源：

- StorageToken

    + HTTP POST：建立儲存體權杖
    
        `/tables/TodoItem/{id}/MobileServiceFiles`
    
- MobileServiceFiles

    + HTTP GET：擷取與記錄相關聯的檔案清單
    
        `/tables/TodoItem/{id}/MobileServiceFiles`

    + HTTP DELETE：刪除在檔案資源識別碼中指定的檔案
    
        `/tables/TodoItem/{id}/MobileServiceFiles/{fileid}`

###用戶端和伺服器通訊

請注意，`TodoItemStorageController`「沒有」用於上傳或下載 blob 的路由。這是因為行動用戶端在一開始取得 SAS 權杖 (共用存取簽章) 以安全地存取特定 blob 或容器之後，就會「直接」與 Blob 儲存體互動來執行這些作業。這是很重要的架構設計，否則對於儲存體的存取會受到行動後端的延展性和可用性限制。相反地，透過直接連線到 Azure 儲存體，行動用戶端可以利用其功能，例如自動分割和地理散發。

共用存取簽章可提供您儲存體帳戶中資源的委派存取。這表示您可以在無需分享您帳戶存取金鑰的情況下，將您儲存體帳戶中的物件有限權限授與用戶端，該用戶端便可在指定的時間期間內及使用指定的權限集來進行存取。若要深入了解，請參閱[了解共用存取簽章]。

下圖顯示用戶端與伺服器互動。上傳檔案之前，用戶端會向服務要求 SAS 權杖。服務會使用儲存體連接字串來產生新的 SAS，然後傳回給用戶端。SAS 有時間限制，並且會將權限限制為只有特定檔案或容器。然後行動用戶端會使用此 SAS 和 Azure 儲存體用戶端 SDK，來將檔案上傳至 blob 儲存體。

![要求 SAS 權杖](./media/app-service-mobile-xamarin-forms-blob-storage/storage-token-diagram.png)

## 更新您的用戶端應用程式以新增映像支援

在 Visual Studio 或 Xamarin Studio 中開啟 Xamarin.Forms 快速入門專案。

>[AZURE.NOTE] 本教學課程中只包含 Android、iOS 和 Windows 市集平台的指示，不包含 Windows Phone 的指示。

###新增 NuGet 封裝

在方案上按一下滑鼠右鍵，然後選取 [管理方案的 NuGet 套件]。將下列 NuGet 套件新增至方案中的「所有」專案。請務必選取 [包括發行前版本]。

  - [Microsoft.Azure.Mobile.Client.Files]

  - [Microsoft.Azure.Mobile.Client.SQLiteStore]

  - [PCLStorage]

為了方便起見，這個範例使用 [PCLStorage] 程式庫，但這對 Azure Mobile Apps 用戶端 SDK 來說並非必要。

[PCLStorage]: https://www.nuget.org/packages/PCLStorage/

###新增 IPlatform 介面

在主要可攜式程式庫專案中建立新的介面 `IPlatform`。這會遵循 [Xamarin.Forms DependencyService] 模式，在執行階段載入正確的平台特定類別。您稍後將在每個用戶端專案中新增平台特定實作。

1. 加入下列 using 陳述式：

        using Microsoft.WindowsAzure.MobileServices.Files;
        using Microsoft.WindowsAzure.MobileServices.Files.Metadata;
        using Microsoft.WindowsAzure.MobileServices.Sync;

2. 使用下列項目來取代此實作：

        public interface IPlatform
        {
            Task <string> GetTodoFilesPathAsync();

            Task<IMobileServiceFileDataSource> GetFileDataSource(MobileServiceFileMetadata metadata);

            Task<string> TakePhotoAsync(object context);

            Task DownloadFileAsync<T>(IMobileServiceSyncTable<T> table, MobileServiceFile file, string filename);
        }

###新增 FileHelper 類別

1. 在主要可攜式程式庫專案中建立新的類別 `FileHelper`。加入下列 using 陳述式：

        using System.IO;
        using PCLStorage;
        using System.Threading.Tasks;
        using Xamarin.Forms;

2. 新增類別定義：

        public class FileHelper
        {
            public static async Task<string> CopyTodoItemFileAsync(string itemId, string filePath)
            {
                IFolder localStorage = FileSystem.Current.LocalStorage;

                string fileName = Path.GetFileName(filePath);
                string targetPath = await GetLocalFilePathAsync(itemId, fileName);

                var sourceFile = await localStorage.GetFileAsync(filePath);
                var sourceStream = await sourceFile.OpenAsync(FileAccess.Read);

                var targetFile = await localStorage.CreateFileAsync(targetPath, CreationCollisionOption.ReplaceExisting);

                using (var targetStream = await targetFile.OpenAsync(FileAccess.ReadAndWrite)) {
                    await sourceStream.CopyToAsync(targetStream);
                }

                return targetPath;
            }

            public static async Task<string> GetLocalFilePathAsync(string itemId, string fileName)
            {
                IPlatform platform = DependencyService.Get<IPlatform>();

                string recordFilesPath = Path.Combine(await platform.GetTodoFilesPathAsync(), itemId);

                    var checkExists = await FileSystem.Current.LocalStorage.CheckExistsAsync(recordFilesPath);
                    if (checkExists == ExistenceCheckResult.NotFound) {
                        await FileSystem.Current.LocalStorage.CreateFolderAsync(recordFilesPath, CreationCollisionOption.ReplaceExisting);
                    }

                return Path.Combine(recordFilesPath, fileName);
            }

            public static async Task DeleteLocalFileAsync(Microsoft.WindowsAzure.MobileServices.Files.MobileServiceFile fileName)
            {
                string localPath = await GetLocalFilePathAsync(fileName.ParentId, fileName.Name);
                var checkExists = await FileSystem.Current.LocalStorage.CheckExistsAsync(localPath);

                if (checkExists == ExistenceCheckResult.FileExists) {
                    var file = await FileSystem.Current.LocalStorage.GetFileAsync(localPath);
                    await file.DeleteAsync();
                }
            }
        }

### 新增檔案同步處理常式

在主要可攜式程式庫專案中建立新的類別 `TodoItemFileSyncHandler`。這個類別包含 Azure SDK 的回呼，以在新增或移除檔案時，通知您的程式碼。

Azure 行動用戶端 SDK 不會實際儲存任何檔案資料：此用戶端 SDK 會叫用您的 `IFileSyncHandler` 實作，進而決定是否在本機裝置上儲存檔案及如何儲存。

1. 加入下列 using 陳述式：

        using System.Threading.Tasks;
        using Microsoft.WindowsAzure.MobileServices.Files.Sync;
        using Microsoft.WindowsAzure.MobileServices.Files;
        using Microsoft.WindowsAzure.MobileServices.Files.Metadata;
        using Xamarin.Forms;

2. 以下列項目取代類別定義：

        public class TodoItemFileSyncHandler : IFileSyncHandler
        {
            private readonly TodoItemManager todoItemManager;

            public TodoItemFileSyncHandler(TodoItemManager itemManager)
            {
                this.todoItemManager = itemManager;
            }

            public Task<IMobileServiceFileDataSource> GetDataSource(MobileServiceFileMetadata metadata)
            {
                IPlatform platform = DependencyService.Get<IPlatform>();
                return platform.GetFileDataSource(metadata);
            }

            public async Task ProcessFileSynchronizationAction(MobileServiceFile file, FileSynchronizationAction action)
            {
                if (action == FileSynchronizationAction.Delete) {
                    await FileHelper.DeleteLocalFileAsync(file);
                }
                else { // Create or update. We're aggressively downloading all files.
                    await this.todoItemManager.DownloadFileAsync(file);
                }
            }
        }

###更新 TodoItemManager

1. 在 **TodoItemManager.cs** 中，將 `#define OFFLINE_SYNC_ENABLED` 這行取消註解。

2. 在 **TodoItemManager.cs** 中，新增下列 using 陳述式：

        using System.IO;
        using Xamarin.Forms;
        using Microsoft.WindowsAzure.MobileServices.Files;
        using Microsoft.WindowsAzure.MobileServices.Files.Sync;
        using Microsoft.WindowsAzure.MobileServices.Eventing;

3. 在 `TodoItemManager` 的建構函式中，在對 `DefineTable()` 的呼叫後面新增下列內容：

        // Initialize file sync
        this.client.InitializeFileSyncContext(new TodoItemFileSyncHandler(this), store);

4. 在建構函式中，以下列內容取代對 `InitializeAsync` 的呼叫。這可確保在本機存放區中修改記錄時有回呼。檔案同步處理功能會使用這些回呼來觸發檔案的同步處理常式。

        this.client.SyncContext.InitializeAsync(store, StoreTrackingOptions.NotifyLocalAndServerOperations);

5. 在 `PushAsync()` 中，在對 `SyncAsync()` 的呼叫後面新增下列內容：

        await this.todoTable.PushFileChangesAsync();

6. 將下列方法新增至 `TodoItemManager`：

        internal async Task DownloadFileAsync(MobileServiceFile file)
        {
            var todoItem = await todoTable.LookupAsync(file.ParentId);
            IPlatform platform = DependencyService.Get<IPlatform>();

            string filePath = await FileHelper.GetLocalFilePathAsync(file.ParentId, file.Name); 
            await platform.DownloadFileAsync(this.todoTable, file, filePath);
        }

        internal async Task<MobileServiceFile> AddImage(TodoItem todoItem, string imagePath)
        {
            string targetPath = await FileHelper.CopyTodoItemFileAsync(todoItem.Id, imagePath);
            return await this.todoTable.AddFileAsync(todoItem, Path.GetFileName(targetPath));
        }

        internal async Task DeleteImage(TodoItem todoItem, MobileServiceFile file)
        {
            await this.todoTable.DeleteFileAsync(file);
        }

        internal async Task<IEnumerable<MobileServiceFile>> GetImageFilesAsync(TodoItem todoItem)
        {
            return await this.todoTable.GetFilesAsync(todoItem);
        }

###新增詳細資料檢視

在本節中，您將新增 todo 項目的新的詳細資料檢視。當使用者選取 todo 項目，並可讓新的映像新增至項目時，會建立檢視。

1. 使用下列實作將新類別 **TodoItemImage** 新增至可攜式程式庫專案：

        public class TodoItemImage : INotifyPropertyChanged
        {
            private string name;
            private string uri;

            public MobileServiceFile File { get; private set; }

            public string Name
            {
                get { return name; }
                set
                {
                    name = value;
                    OnPropertyChanged(nameof(Name));
                }
            }

            public string Uri
            {
                get { return uri; }      
                set
                {
                    uri = value;
                    OnPropertyChanged(nameof(Uri));
                }
            }

            public TodoItemImage(MobileServiceFile file, TodoItem todoItem)
            {
                Name = file.Name;
                File = file;

                FileHelper.GetLocalFilePathAsync(todoItem.Id, file.Name).ContinueWith(x => this.Uri = x.Result);
            }

            public event PropertyChangedEventHandler PropertyChanged;

            private void OnPropertyChanged(string propertyName)
            {
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));
            }
        }

2. 編輯 **App.cs**。以下列內容取代 `MainPage` 的初始化：
    
        MainPage = new NavigationPage(new TodoList());

3. 在 **App.cs** 中，新增下列屬性：

        public static object UIContext { get; set; }

4. 在可攜式程式庫專案上按一下滑鼠右鍵，然後選取 [新增] -> [新項目] -> [跨平台] -> [Form Xaml 頁面]。將檢視命名為 `TodoItemDetailsView`。

5. 開啟 **TodoItemDetailsView.xaml**，以下列內容取代 ContentPage 的本文：

          <Grid>
            <Grid.RowDefinitions>
              <RowDefinition Height="Auto"/>
              <RowDefinition Height="Auto"/>
              <RowDefinition Height="*"/>
            </Grid.RowDefinitions>
            <Button Clicked="OnAdd" Text="Add image"></Button>
            <ListView x:Name="imagesList"
                      ItemsSource="{Binding Images}"
                      IsPullToRefreshEnabled="false"
                      Grid.Row="2">
              <ListView.ItemTemplate>
                <DataTemplate>
                  <ImageCell ImageSource="{Binding Uri}"
                             Text="{Binding Name}">
                  </ImageCell>
                </DataTemplate>
              </ListView.ItemTemplate>
            </ListView>
          </Grid>

6. 編輯 **TodoItemDetailsView.xaml.cs** 並新增下列 using 陳述式：

        using System.Collections.ObjectModel;
        using Microsoft.WindowsAzure.MobileServices.Files;

7. 以下列內容取代 `TodoItemDetailsView` 的實作：

        public partial class TodoItemDetailsView : ContentPage
        {
            private TodoItemManager manager;

            public TodoItem TodoItem { get; set; }        
            public ObservableCollection<TodoItemImage> Images { get; set; }

            public TodoItemDetailsView(TodoItem todoItem, TodoItemManager manager)
            {
                InitializeComponent();
                this.Title = todoItem.Name;

                this.TodoItem = todoItem;
                this.manager = manager;

                this.Images = new ObservableCollection<TodoItemImage>();
                this.BindingContext = this;
            }

            public async Task LoadImagesAsync()
            {
                IEnumerable<MobileServiceFile> files = await this.manager.GetImageFilesAsync(TodoItem);
                this.Images.Clear();

                foreach (var f in files) {
                    var todoImage = new TodoItemImage(f, this.TodoItem);
                    this.Images.Add(todoImage);
                }
            }

            public async void OnAdd(object sender, EventArgs e)
            {
                IPlatform mediaProvider = DependencyService.Get<IPlatform>();
                string sourceImagePath = await mediaProvider.TakePhotoAsync(App.UIContext);

                if (sourceImagePath != null) {
                    MobileServiceFile file = await this.manager.AddImage(this.TodoItem, sourceImagePath);

                    var image = new TodoItemImage(file, this.TodoItem);
                    this.Images.Add(image);
                }
            }
        }

###更新主要檢視 

更新主要的檢視，以在選取 todo 項目時，開啟詳細資料檢視。

在 **TodoList.xaml.cs** 中，以下列內容取代 `OnSelected` 的實作：

    public async void OnSelected(object sender, SelectedItemChangedEventArgs e)
    {
        var todo = e.SelectedItem as TodoItem;

        if (todo != null) {
            var detailsView = new TodoItemDetailsView(todo, manager);
            await detailsView.LoadImagesAsync();
            await Navigation.PushAsync(detailsView);
        }

        todoList.SelectedItem = null;
    }

###更新 Android 專案

將平台特定程式碼新增至 Android 專案，包括下載檔案的程式碼，然後使用相機擷取新的映像。

此程式碼會使用 Xamarin.Forms [DependencyService](https://developer.xamarin.com/guides/xamarin-forms/dependency-service/)，在執行階段載入正確的平台特定類別。

1. 將元件 **Xamarin.Mobile** 新增至 Android 專案。

2. 使用下列實作來新增新類別 `DroidPlatform`。使用您的專案的主要命名空間取代 "YourNamespace"。

        using System;
        using System.IO;
        using System.Threading.Tasks;
        using Android.Content;
        using Microsoft.WindowsAzure.MobileServices.Files;
        using Microsoft.WindowsAzure.MobileServices.Files.Metadata;
        using Microsoft.WindowsAzure.MobileServices.Files.Sync;
        using Microsoft.WindowsAzure.MobileServices.Sync;
        using Xamarin.Media;

        [assembly: Xamarin.Forms.Dependency(typeof(YourNamespace.Droid.DroidPlatform))]
        namespace YourNamespace.Droid
        {
            public class DroidPlatform : IPlatform
            {
                public async Task DownloadFileAsync<T>(IMobileServiceSyncTable<T> table, MobileServiceFile file, string filename)
                {
                    var path = await FileHelper.GetLocalFilePathAsync(file.ParentId, file.Name);
                    await table.DownloadFileAsync(file, path);
                }

                public async Task<IMobileServiceFileDataSource> GetFileDataSource(MobileServiceFileMetadata metadata)
                {
                    var filePath = await FileHelper.GetLocalFilePathAsync(metadata.ParentDataItemId, metadata.FileName);
                    return new PathMobileServiceFileDataSource(filePath);
                }

                public async Task<string> TakePhotoAsync(object context)
                {
                    try {
                        var uiContext = context as Context;
                        if (uiContext != null) {
                            var mediaPicker = new MediaPicker(uiContext);
                            var photo = await mediaPicker.TakePhotoAsync(new StoreCameraMediaOptions());

                            return photo.Path;
                        }
                    }
                    catch (TaskCanceledException) {
                    }

                    return null;
                }

                public Task<string> GetTodoFilesPathAsync()
                {
                    string appData = Environment.GetFolderPath(Environment.SpecialFolder.MyDocuments);
                    string filesPath = Path.Combine(appData, "TodoItemFiles");

                    if (!Directory.Exists(filesPath)) {
                        Directory.CreateDirectory(filesPath);
                    }

                    return Task.FromResult(filesPath);
                }
            }
        }

3. 編輯 **MainActivity.cs**。在 `OnCreate` 中，在對 `LoadApplication()` 的呼叫前面新增下列內容：

        App.UIContext = this;

###更新 iOS 專案

將平台特定程式碼新增至 iOS 專案。

1. 將元件 **Xamarin.Mobile** 新增至 iOS 專案。

2. 使用下列實作來新增新類別 `TouchPlatform`。使用您的專案的主要命名空間取代 "YourNamespace"。

        using System;
        using System.Collections.Generic;
        using System.IO;
        using System.Text;
        using System.Threading.Tasks;
        using Microsoft.WindowsAzure.MobileServices.Files;
        using Microsoft.WindowsAzure.MobileServices.Files.Metadata;
        using Microsoft.WindowsAzure.MobileServices.Files.Sync;
        using Microsoft.WindowsAzure.MobileServices.Sync;
        using Xamarin.Media;

        [assembly: Xamarin.Forms.Dependency(typeof(YourNamespace.iOS.TouchPlatform))]
        namespace YourNamespace.iOS
        {
            class TouchPlatform : IPlatform
            {
                public async Task DownloadFileAsync<T>(IMobileServiceSyncTable<T> table, MobileServiceFile file, string filename)
                {
                    var path = await FileHelper.GetLocalFilePathAsync(file.ParentId, file.Name);
                    await table.DownloadFileAsync(file, path);
                }

                public async Task<IMobileServiceFileDataSource> GetFileDataSource(MobileServiceFileMetadata metadata)
                {
                    var filePath = await FileHelper.GetLocalFilePathAsync(metadata.ParentDataItemId, metadata.FileName);
                    return new PathMobileServiceFileDataSource(filePath);
                }

                public async Task<string> TakePhotoAsync(object context)
                {
                    try {
                        var mediaPicker = new MediaPicker();
                        var mediaFile = await mediaPicker.PickPhotoAsync();
                        return mediaFile.Path;
                    }
                    catch (TaskCanceledException) {
                        return null;
                    }
                }

                public Task<string> GetTodoFilesPathAsync()
                {
                    string filesPath = Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.MyDocuments), "TodoItemFiles");

                    if (!Directory.Exists(filesPath)) {
                        Directory.CreateDirectory(filesPath);
                    }

                    return Task.FromResult(filesPath);
                }
            }
        }

3. 編輯 **AppDelegate.cs** 並將對 `SQLitePCL.CurrentPlatform.Init()` 的呼叫取消註解。

###更新 Windows 專案

1. 安裝 Visual Studio 擴充功能 [SQLite for Windows 8.1](http://go.microsoft.com/fwlink/?LinkID=716919)。如需詳細資訊，請參閱[啟用 Windows 應用程式離線同步處理](app-service-mobile-windows-store-dotnet-get-started-offline-data.md)教學課程。 

2. 編輯 **Package.appxmanifest** 並檢查**網路攝影機**功能。

3. 使用下列實作來新增新類別 `WindowsStorePlatform`。使用您的專案的主要命名空間取代 "YourNamespace"。

        using System;
        using System.Threading.Tasks;
        using Microsoft.WindowsAzure.MobileServices.Files;
        using Microsoft.WindowsAzure.MobileServices.Files.Metadata;
        using Microsoft.WindowsAzure.MobileServices.Files.Sync;
        using Microsoft.WindowsAzure.MobileServices.Sync;
        using Windows.Foundation;
        using Windows.Media.Capture;
        using Windows.Storage;
        using YourNamespace;

        [assembly: Xamarin.Forms.Dependency(typeof(WinApp.WindowsStorePlatform))]
        namespace WinApp
        {
            public class WindowsStorePlatform : IPlatform
            {
                public async Task DownloadFileAsync<T>(IMobileServiceSyncTable<T> table, MobileServiceFile file, string filename)
                {
                    var path = await FileHelper.GetLocalFilePathAsync(file.ParentId, file.Name);
                    await table.DownloadFileAsync(file, path);
                }

                public async Task<IMobileServiceFileDataSource> GetFileDataSource(MobileServiceFileMetadata metadata)
                {
                    var filePath = await FileHelper.GetLocalFilePathAsync(metadata.ParentDataItemId, metadata.FileName);
                    return new PathMobileServiceFileDataSource(filePath);
                }

                public async Task<string> GetTodoFilesPathAsync()
                {
                    var storageFolder = ApplicationData.Current.LocalFolder;
                    var filePath = "TodoItemFiles";

                    var result = await storageFolder.TryGetItemAsync(filePath);

                    if (result == null) {
                        result = await storageFolder.CreateFolderAsync(filePath);
                    }

                    return result.Name; // later operations will use relative paths
                }

                public async Task<string> TakePhotoAsync(object context)
                {
                    try {
                        CameraCaptureUI dialog = new CameraCaptureUI();
                        Size aspectRatio = new Size(16, 9);
                        dialog.PhotoSettings.CroppedAspectRatio = aspectRatio;

                        StorageFile file = await dialog.CaptureFileAsync(CameraCaptureUIMode.Photo);
                        return file.Path;
                    }
                    catch (TaskCanceledException) {
                        return null;
                    }
                }
            }
        }

##摘要

這篇文章說明如何使用 Azure 行動用戶端和伺服器 SDK 中的新檔案支援以使用 Azure 儲存體。

- 建立儲存體帳戶並將連接字串新增至您的行動應用程式後端。只有後端擁有 Azure 儲存體金鑰：行動用戶端會在需要存取 Azure 儲存體時隨時要求 SAS 權杖 (共用存取簽章)。若要深入了解「Azure 儲存體」中的 SAS 權杖，請參閱[了解共用存取簽章]。

- 建立將 `StorageController` 分類為子類別的控制站，以處理 SAS 權杖要求，並取得與記錄關聯的檔案。根據預設，會使用記錄識別碼做為容器名稱的一部分，讓檔案與記錄建立關聯；您可以藉由指定 `IContainerNameResolver` 的實作來自訂此行為。也可以自訂 SAS 權杖原則。

- Azure 行動用戶端 SDK 不會實際儲存任何檔案資料。更確切地說，用戶端 SDK 會叫用您的 `IFileSyncHandler`，進而決定如何 (以及是否) 在本機裝置上儲存檔案。同步處理常式會註冊，如下所示：

        client.InitializeFileSync(new MyFileSyncHandler(), store);

      + 當 Azure 行動用戶端 SDK 需要檔案資料時 (例如，做為上傳程序的一部分)，會呼叫 `IFileSyncHandler.GetDataSource`。這可讓您能夠管理如何 (以及是否) 在本機裝置上儲存檔案，並且在需要時傳回該資訊。

      + 進行檔案同步處理流程時會一併叫用 `IFileSyncHandler.ProcessFileSynchronizationAction`。提供檔案參考和 FileSynchronizationAction 列舉值，讓您可以決定應用程式應如何處理該事件 (例如，在檔案建立或更新時自動下載、在檔案於伺服器上刪除時從本機裝置上刪除檔案)。

- `MobileServiceFile` 可以在線上或離線模式下使用，方法是分別透過使用 `IMobileServiceTable` 或 `IMobileServiceSyncTable`。在離線案例中，會在應用程式呼叫 `PushFileChangesAsync` 時進行上傳。這會導致對離線作業佇列進行處理；針對每個檔案作業，Azure 行動用戶端 SDK 會在 `IFileSyncHandler` 執行個體上叫用 `GetDataSource` 方法，以擷取要上傳的檔案內容。

- 為了擷取項目的檔案，請在 `IMobileServiceTable<T>` 或 IMobileServiceSyncTable<T> 執行個體上呼叫 `GetFilesAsync` 方法。這個方法會傳回與提供的資料項目相關聯的檔案的清單。(請注意：這是「本機」作業，而且會根據物件上次同步處理時的狀態，傳回檔案。若要從伺服器取得更新的檔案清單，您應該先起始同步處理作業。)

        IEnumerable<MobileServiceFile> files = await myTable.GetFilesAsync(myItem);

- 檔案同步處理功能會在本機存放區上使用記錄變更通知，以擷取用戶端收到的推送或提取作業的記錄。這是藉由使用 `StoreTrackingOptions` 參數為同步處理內容開啟本機和伺服器通知來達成。

        this.client.SyncContext.InitializeAsync(store, StoreTrackingOptions.NotifyLocalAndServerOperations);

      + 其他存放區追蹤選項可用，例如僅本機或僅伺服器的通知。您可以使用 `IMobileServiceClient` 的 `EventManager` 屬性來新增或擁有自訂回呼：

            jobService.MobileService.EventManager.Subscribe<StoreOperationCompletedEvent>(StoreOperationEventHandler);

- 您可以藉由直接修改 blob 儲存體來新增或移除記錄中的檔案，因為關聯是透過命名慣例來達成。不過，在此情況下，您應該一律**在關聯的 blob 被修改時更新記錄時間戳記**。Azure 行動用戶端 SDK 一律會在新增或移除檔案時更新記錄。

    這項需求的原因是，某些行動用戶端在本機儲存體上已經有記錄。當這些用戶端執行累加式提取時，將不會傳回此記錄，且用戶端不會查詢新的關聯檔案。若要避免這個問題，建議您在執行不會使用 Azure 行動用戶端 SDK 的任何 blob 儲存體變更時，更新記錄時間戳記。

- 用戶端專案會使用 [Xamarin.Forms DependencyService] 模式，在執行階段載入正確的平台特定類別。在此範例中，我們在每個平台特定專案中定義了介面 `IPlatform` 與實作。

<!-- URLs. -->

[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203
[建立 Xamarin.Forms 應用程式]: ../app-service-mobile-xamarin-forms-get-started.md
[Xamarin.Forms DependencyService]: https://developer.xamarin.com/guides/xamarin-forms/dependency-service/
[Microsoft.Azure.Mobile.Client.Files]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client.Files/
[Microsoft.Azure.Mobile.Client.SQLiteStore]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client.SQLiteStore/
[Microsoft.Azure.Mobile.Server.Files]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Files/
[了解共用存取簽章]: ../storage/storage-dotnet-shared-access-signature-part-1.md
[建立 Azure 儲存體帳戶]: ../storage/storage-create-storage-account.md#create-a-storage-account

<!---HONumber=AcomDC_0211_2016-->