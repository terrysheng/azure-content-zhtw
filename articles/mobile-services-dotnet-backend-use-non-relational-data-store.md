<properties linkid="mobile-services-dotnet-backend-use-non-relational-data-store" urlDisplayName="Build a Service Using a Non-Relational Data Store" pageTitle="Build a Service Using a Non-Relational Data Store - Azure Mobile Services" metaKeywords="" description="Learn how to use a non-relational data store such as MongoDB or Azure Table Storage with your .NET based mobile service" metaCanonical="" services="" documentationCenter="Mobile" title="Build a Service Using a Non-Relational Data Store" authors="yavorg, mahender" solutions="" manager="" editor="mollybos" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="yavorg, mahender"></tags>

# 使用 .NET 後端以 MongoDB 作為資料存放區來建置服務

本主題說明如何在您的行動服務中使用非關聯式資料存放區。在本教學課程中，您將修改行動服務快速入門專案，以使用 MongoDB 作為資料存放區，而不使用 SQL。

本教學課程會逐步引導您完成下列步驟以設定非關聯式存放區：

1.  [建立非關聯式存放區][建立非關聯式存放區]
2.  [修改資料和控制器][修改資料和控制器]
3.  [測試應用程式][測試應用程式]

要執行此教學課程，必須先完成[開始使用行動服務][開始使用行動服務]或[開始使用資料][開始使用資料]教學課程。

## <a name="create-store"></a>建立非關聯式存放區

1.  在 [Azure 管理入口網站][Azure 管理入口網站]中，按一下 [新增]，然後選取 [存放區]。

2.  選取 [MongoLab] 附加元件，然後導覽精靈以註冊帳戶。如需 MongoLab 的詳細資訊，請參閱 [MongoLab 附加元件頁面][MongoLab 附加元件頁面]。

    ![][]

3.  帳戶設定完成後，請選取 [連線資訊]，然後複製連線字串。

4.  導覽至入口網站的 [行動服務] 區段，然後選取 [設定] 索引標籤。

5.  在 [應用程式設定] 下，使用金鑰 "MongoConnectionString" 輸入連線字串，然後按一下 [儲存]。

    ![][1]

6.  將下列程式碼新增至 `TodoItemController`：

        static bool connectionStringInitialized = false;

        private void InitializeConnectionString(string connectionStringName)
        {
            if (!connectionStringInitialized)
            {
                connectionStringInitialized = true;
                if (!this.Services.Settings.Connections.ContainsKey(connectionStringName))
                {
                    var connectionString = this.Services.Settings[connectionStringName];
                    var connectionSetting = new ConnectionSettings(connectionStringName, connectionString);
                    this.Services.Settings.Connections.Add(connectionStringName, connectionSetting);
                }
            }
        }

    此程式碼會載入應用程式設定，並指示行動服務將其視為可供 `TableController` 使用的連線。您後續將會在叫用 `TodoItemController` 時呼叫此方法。

## <a name="modify-service"></a>修改資料和控制器

1.  安裝 **WindowsAzure.MobileServices.Backend.Mongo** NuGet 封裝。

2.  將 `TodoItem` 修改成衍生自 `DocumentData`，而不是 `EntityData`。

        public class TodoItem : DocumentData
        {
            public string Text { get; set; }

            public bool Complete { get; set; }
        }

3.  在 `TodoItemController` 中，使用下列項目來取代 `Initialize` 方法：

        protected override async void Initialize(HttpControllerContext controllerContext)
        {
            base.Initialize(controllerContext);
            string connectionStringName = "MongoConnectionString";
            string databaseName = "<YOUR-DATABASE-NAME>";
            string collectionName = "todoItems";
            InitializeConnectionString(connectionStringName);
            DomainManager = new MongoDomainManager<TodoItem>(connectionStringName, databaseName, collectionName, Request, Services);
        }

4.  在前述 `Initialize` 方法的程式碼中，使用您在佈建 MongoLab 附加元件時選擇的名稱來取代 **YOUR-DATABASE-NAME**。

## <a name="test-application"></a>測試應用程式

1.  重新發佈您的行動服務後端專案。

2.  執行您的用戶端應用程式。請注意，您在快速入門教學課程中不會看見任何先前儲存在 SQL Database 中的項目。

3.  建立新項目。應用程式應會照原本的方式運作，差別在於，現在您的資料將會送到非關聯式存放區。

<!-- Anchors. --> <!-- Images. --> <!-- URLs. -->

  [建立非關聯式存放區]: #create-store
  [修改資料和控制器]: #modify-service
  [測試應用程式]: #test-application
  [開始使用行動服務]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started
  [開始使用資料]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data
  [Azure 管理入口網站]: https://manage.windowsazure.com/
  [MongoLab 附加元件頁面]: /zh-tw/gallery/store/mongolab/mongolab
  []: ./media/mobile-services-dotnet-backend-use-non-relational-data-store/create-mongo-lab.png
  [1]: ./media/mobile-services-dotnet-backend-use-non-relational-data-store/mongo-connection-string.png
