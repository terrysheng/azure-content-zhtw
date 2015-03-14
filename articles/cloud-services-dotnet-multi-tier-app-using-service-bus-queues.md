<properties 
	pageTitle=".NET 多層式應用程式 - Azure 教學課程" 
	description="協助您在 Azure 中開發多層式應用程式，以使用服務匯流排佇列在各層之間進行通訊的教學課程。.NET 的範例。" 
	services="cloud-services, service-bus" 
	documentationCenter=".net" 
	authors="sethmanheim" 
	manager="timlt" 
	editor="mattshel"/>

<tags 
	ms.service="service-bus" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/10/2015" 
	ms.author="sethm"/>





# 使用服務匯流排佇列的 .NET 多層式應用程式

使用 Visual Studio 2013 和
免費的 Azure SDK for .NET 開發 Azure，是很容易的。如果您還沒有 Visual
Studio 2013，SDK 會自動安裝 Visual Studio Express 2013，以便您可以開始免費進行 Azure 的開發。
本指南假設您先前沒有使用 Windows
Azure 的經驗。完成本指南後，您將會有一個使用多個 Azure 資源的應用程式
執行於您的本機環境中，
並示範多層式應用程式的運作方式。

您將了解：

-   如何透過單一下載和安裝讓您的電腦能夠進行 Azure 開發
    。
-   如何使用 Visual Studio 進行 Azure 相關開發。
-   如何使用 Web 與背景工作角色，
    在 Azure 中建立多層式應用程式。
-   如何使用服務匯流排佇列，在階層之間進行通訊。

[WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]

在本教學課程中，您將在 Azure 雲端服務中建置和執行多層式應用程式。前端將為 ASP.NET MVC Web 角色，而後端將為背景工作角色。您可以建立相同的多層式應用程式，並使其前端作為部署至 Azure 網站而非雲端服務的 Web 專案。如需 Azure 網站前端不同作法的指示，請參閱[後續步驟](#nextsteps)一節。

以下顯示完整應用程式的螢幕擷取畫面：

![][0]

**附註** Azure 也會提供儲存體佇列功能。如需 Azure 儲存體佇列和服務匯流排佇列的詳細資訊，請參閱 [Azure 佇列和 Azure 服務匯流排佇列 - 比較和對比 ][sbqueuecomparison]。  

<h2>案例概觀：內部角色通訊</h2>

若要提交處理順序，執行於 Web 角色中的前端 UI 元件
必須與執行於背景工作角色中的中間層邏輯
互動。此範例使用服務匯流排代理的傳訊進行
各層之間的通訊。

在 Web 層與中間層之間使用代理的通訊，可使這兩個元件
彼此脫鉤。相較於直接傳訊 (亦即，TCP 或 HTTP)
web 層不會直接連接到中間層；而是會以訊息的形式
將工作單位發送至服務匯流排中，而可靠地加以保留，
直到中間層準備好加以取用和處理為止。

服務匯流排提供兩個實體來支援代理的傳訊，也就是：
佇列和主題。使用佇列時，每個傳送至佇列的訊息
都會由單一接收者取用。主題支援每個已發行的訊息
已可供使用主題註冊的每個訂閱使用的
發行/訂閱模式。每個訂閱都會以邏輯方式
維護其本身的訊息佇列。訂閱也可以
使用篩選規則來設定，以將傳至
訂閱佇列的訊息集限定為符合篩選條件的訊息。此範例使用
服務匯流排佇列。

![][1]

相較於直接傳訊，此通訊機制具有數個優點，
即：

-   **暫時分離。** 透過非同步傳訊模式，
    產生者和取用者不需要同時處於線上。服務
    流排會可靠地保管訊息，直到取用方準備好接收訊息
    為止。這可讓分散式應用程式的元件
    在中斷連線時不會對系統造成整體性的影響，
    無論是人為中斷 (例如為了進行維護)，或是由於
    元件損毀。此外，取用方應用程式可能只
    需要在一天的某些時間上線。

-   **負載調節**。在許多應用程式中，系統負載會隨著時間而改變，
    而每個工作單位所需的處理時間則
    通常是固定的。使用佇列中介訊息生產者與取用者，
    表示取用方應用程式 (背景工作) 的
    佈建只需要因應平均負載，而非尖峰
    負載。佇列的深度會隨著連入負載的改變而增加和
    縮短。這在為應用程式負載提供服務所需的基礎結構方面，
    將可直接節省經費。

-   **負載平衡。** 隨著負載增加，您可以新增更多背景工作處理序
    來讀取佇列。每則訊息只會由其中一個
    背景工作處理序來處理。此外，這個提取型負載平衡
    可讓背景工作電腦的使用率最佳化，即使
    背景工作電腦在處理能力方面因為會以其本身的
    最大速率提取訊息而有所不同，仍是如此。此模式通常
    稱為競爭取用者模式。

    ![][2]

下列幾節討論實作此架構的
程式碼。

<h2>設定開發環境</h2>

您必須先取得工具並設定開發環境，
才可以開始開發 Azure 應用程式。

1.  若要安裝 Azure SDK for .NET，請按一下底下按鈕：

    [取得工具和 SDK (英文)][]

2. 	按一下 [**安裝 SDK**]。

3. 	選擇您所使用的 Visual Studio 版本的連結。本教學課程中的步驟使用 Visual Studio 2013：

	![][32]

4. 	當系統提示您執行或儲存安裝檔案時，按一下 [
    **執行**]：

    ![][3]

5.  在 Web Platform Installer 中按一下 [**安裝**]，然後繼續進行安裝：

    ![][33]

6.  安裝完成之後，您將具備開始開發
    所需的所有項目。SDK 中的工具可讓您
    在 Visual Studio 中輕鬆開發 Azure 應用程式。如果您
    未安裝 Visual Studio，則會安裝免費的 
    Visual Studio Express for Web。

<h2>設定服務匯流排命名空間</h2>

下一步是建立服務命名空間，並取得共用
密鑰。服務命名空間可為每個透過服務匯流排公開的
應用程式提供應用程式界限。共用密鑰
會在服務務命名空間建立時，由系統
自動產生。服務命名空間和共用密鑰的組合，
可為服務匯流排提供用來驗證應用程式存取權的認證。
升級版本。

請注意，您也可以使用 Visual Studio 伺服器總管，來管理命名空間和服務匯流排傳訊實體，但是只能從入口網站內建立新的命名空間。 

###使用管理入口網站設定命名空間

1.  登入 [Azure 管理入口網站][]。

2.  在管理入口網站的左側瀏覽窗格中，按一下 [
    **服務匯流排**]。

3.  在管理入口網站的下方窗格中，按一下 [**建立**]。

    ![][6]

4.  在 [**加入新的命名空間**] 對話方塊中，輸入命名空間名稱。
    系統會立即檢查此名稱是否可用。   
    ![][7]

5.  確定命名空間名稱可用之後，請選擇
    您的命名空間應裝載在哪個國家或地區 (請
    確實使用您部署運算資源所在的相同
    國家/地區)。

    重要事項：請挑選您想要選擇來部署應用程式的**相同區域**
    。這樣可以獲得最佳效能。

6.  按一下核取記號。系統此時會建立您的服務
    命名空間，並加以啟用。您可能要稍候幾分鐘的時間，
    讓系統為您的帳戶佈建資源。

	![][27]

7.  在主視窗中，按一下服務命名空間的名稱。

	![][30]

8. 按一下 [**連線資訊**]。

	![][31]

9.  在 [**存取連線資訊**] 窗格中，找出 [**預設簽發者**] 和 [**預設金鑰**] 值。

10.  記下金鑰，或將它複製到剪貼簿。

###使用 Visual Studio 伺服器總管來管理命名空間與傳訊實體

若要使用 Visual Studio 而非管理入口網站來管理命名空間並取得連線資訊，請遵循[這裡](http://http://msdn.microsoft.com/library/windowsazure/ff687127.aspx)所述的程序 (**從 Visual Studio 連接到 Azure** 一節)。當您登入 Azure 時，在伺服器總管中的 [**Microsoft Azure**] 樹狀目錄下，[**服務匯流排**] 節點將會自動填入您已建立的任何命名空間。在任一個命名空間上按滑鼠右鍵，然後按一下 [**屬性**]，即可在 Visual Studio [**屬性**] 窗格中，查看與此命名空間相關聯的連接字串及其他中繼資料。 

請記下 **SharedAccessKey** 值，或將它複製到剪貼簿：

![][34]

<h2>建立 Web 角色</h2>

在本節中，您將建置應用程式的前端。您
您將先建立應用程式顯示的各個頁面。
之後，您會新增程式碼，以將項目提交至服務匯流排
佇列並顯示佇列的相關狀態資訊。

### 建立專案

1.  使用系統管理員權限，啟動 Microsoft Visual Studio
    2013 或 Microsoft Visual Studio Express。若要使用系統管理員權限啟動 Visual
    Studio，請以滑鼠右鍵按一下 **Microsoft Visual
    Studio 2013 (或 Microsoft Visual Studio Express)**，
    然後按一下 [**以系統管理員身分執行**]。要使用 Azure 計算模擬器 
    (將於本指南稍後討論)，必須以系統管理員權限啟動 Visual Studio
    。

    在 Visual Studio 中的 [**檔案**] 功能表上，按一下 [**新增**]，然後
    按一下 [**專案**]。

    ![][8]


2.  在 [**已安裝的範本**] 中的 [**Visual C#**] 下，按一下 [**雲端**]，
    然後按一下 [**Azure 雲端服務**]。將專案命名為 
    **MultiTierApp**。然後按一下 [**確定**]。

    ![][9]

3.  在 **.NET Framework 4.5** 角色中，按兩下 **ASP.NET Web
    角色**。

    ![][10]

4.  將滑鼠停留在 [**Azure 雲端服務解決方案**] 下的 **WebRole1** 上方，按一下
    鉛筆圖示，然後將 Web 角色重新命名為 **FrontendWebRole**。然後按一下 [**確定**]。(請確定您輸入 "Frontend" (e 為小寫)，而不是 "FrontEnd"。)

    ![][11]

5.  在 [**New ASP.NET Project**] 對話方塊的 [**選取範本**] 清單中，按一下 [**MVC**]，
    然後按一下 [**確定**]。

    ![][12]

6.  在 [**方案總管**] 中，以滑鼠右鍵按一下 [**參考**]，然後按一下 [
[    **管理 NuGet 封裝...**] 或 [**新增程式庫封裝參考**]。

7.  選取對話方塊左手邊的 [**線上**]。搜尋
    "**WindowsAzure**"，並選取 [**Azure 服務
    匯流排**] 項目。然後完成安裝並關閉此對話方塊。

    ![][13]

8.  請注意，現在會參考必要的用戶端組件，
    並且已新增某些新的程式碼檔案。

9.  在 [**方案總管**] 中，以滑鼠右鍵按一下 [**模型**]，按一下 [**新增**]，
    然後按一下 [**類別**]。在 [名稱] 方塊中，輸入名稱
    **OnlineOrder.cs**。然後按一下 [**新增**]。

### 撰寫 Web 角色的程式碼

在本節中，您將建立應用程式顯示的各個
頁面。

1.  在 Visual Studio 的 **OnlineOrder.cs** 檔案中，
    將現有的命名空間定義取代為下列程式碼：

        namespace FrontendWebRole.Models
        {
            public class OnlineOrder
            {
                public string Customer { get; set; }
                public string Product { get; set; }
            }
        }

2.  在 [**方案總管**] 中，按兩下
    **Controllers\HomeController.cs**。在檔案最上方新增下列 **using**
    陳述式，為您剛剛建立的
    模型和服務匯流排加入命名空間：

        using FrontendWebRole.Models;
        using Microsoft.ServiceBus.Messaging;
        using Microsoft.ServiceBus;

3.  同時在 Visual Studio 的 **HomeController.cs** 檔案中，
    將現有的命名空間定義取代為下列程式碼。此程式碼
    包含將項目提交給佇列的處理方法：

        namespace FrontendWebRole.Controllers
        {
            public class HomeController : Controller
            {
                public ActionResult Index()
                {
                    // Simply redirect to Submit, since Submit will serve as the
                    // front page of this application
                    return RedirectToAction("Submit");
                }

                public ActionResult About()
                {
                    return View();
                }

                // GET: /Home/Submit
                // Controller method for a view you will create for the submission
                // form
                public ActionResult Submit()
                {
                    // Will put code for displaying queue message count here.

                    return View();
                }

                // POST: /Home/Submit
                // Controller method for handling submissions from the submission
                // form 
                [HttpPost]
				// Attribute to help prevent cross-site scripting attacks and 
				// cross-site request forgery  
    			[ValidateAntiForgeryToken] 
                public ActionResult Submit(OnlineOrder order)
                {
                    if (ModelState.IsValid)
                    {
                        // Will put code for submitting to queue here.
                    
                        return RedirectToAction("Submit");
                    }
                    else
                    {
                        return View(order);
                    }
                }
            }
        }

4.  在 [**建置**] 功能表中，按一下 [**建置方案**]。

5.  現在，您將為上面建立的 **Submit()** 方法
    建立檢視。在 Submit() 方法內按一下滑鼠右鍵，然後選擇
    **新增檢視**

    ![][14]

6.  隨即出現對話方塊，供您建立檢視。選取
    **OnlineOrder** 類別 (位於 [**模型類別**] 下拉式清單中)，然後選擇
    **建立** (位於 [**範本**]下拉式清單中。

    ![][15]

7.  按一下 [**新增**]。

8.  現在，您將變更應用程式的顯示名稱。在
[    **方案總管**] 中，按兩下
    **Views\Shared\\_Layout.cshtml** 檔案，在 Visual
    Studio 編輯器中加以開啟。

9.  將找到的所有 **My ASP.NET MVC Application** 取代為
    **LITWARE'S Awesome Products**。

10.	將 **"your logo here"** 取代為 **LITWARE'S Awesome Products**：

	![][16]

11. 移除 [**首頁**]、[**關於**] 和 [**連絡人**] 連結。刪除反白顯示的程式碼：

	![][28]
  

12. 最後，修改提交頁面，以納入佇列的一些
    相關資訊。在 [**方案總管**] 中，按兩下
    **Views\Home\Submit.cshtml** 檔案，在 Visual Studio
    編輯器中加以開啟。在 **<h2>Submit</h2>** 後面新增下列一行。現在，
    **ViewBag.MessageCount** 是空的。稍後您將在其中填入資料。

        <p>Current Number of Orders in Queue Waiting to be Processed: @ViewBag.MessageCount</p>
             

13. 您現在已實作 UI。您可以按 **F5** 執行您的
    應用程式，並確認看起來如預期般。

    ![][17]

### 撰寫增程式碼以提交項目給服務匯流排佇列

現在，您將新增程式碼以提交項目給佇列。首先，您將
建立一個類別，其中包含服務匯流排佇列連線
資訊。接著，您將從
**Global.aspx.cs** 初始化連線。最後，您將更新
您先前在 **HomeController.cs** 中建立的提交程式碼，以將項目實際提交至
服務匯流排佇列。

1.  在 [方案總管] 中，以滑鼠右鍵按一下 **FrontendWebRole** (以滑鼠右鍵按一下專案，而非角色)。按一下 [**新增**]，然後按一下 [**類別**]。

2.  將類別命名為 **QueueConnector.cs**。按一下 [**新增**] 以建立類別。

3.  現在請貼入程式碼，其中封裝了連線資訊
    並包含用來初始化服務匯流排佇列連線的方法
    。在 QueueConnector.cs 中，貼入下列程式碼，並輸入
    **Namespace**、**IssuerName** 和 **IssuerKey** 的值。您可以
    從[管理入口網站][Azure 管理入口網站]取得這些值，或從**服務匯流排**節點下的 Visual Studio 伺服器總管取得。

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Web;
        using Microsoft.ServiceBus.Messaging;
        using Microsoft.ServiceBus;

        namespace FrontendWebRole
        {
            public static class QueueConnector
            {
                // Thread-safe. Recommended that you cache rather than recreating it
                // on every request.
                public static QueueClient OrdersQueueClient;

                // Obtain these values from the Management Portal
                public const string Namespace = "your service bus namespace";
                public const string IssuerName = "issuer name";
                public const string IssuerKey = "issuer key";

                // The name of your queue
                public const string QueueName = "OrdersQueue";

                public static NamespaceManager CreateNamespaceManager()
                {
                    // Create the namespace manager which gives you access to
                    // management operations
                    var uri = ServiceBusEnvironment.CreateServiceUri(
                        "sb", Namespace, String.Empty);
                    var tP = TokenProvider.CreateSharedSecretTokenProvider(
                        IssuerName, IssuerKey);
                    return new NamespaceManager(uri, tP);
                }

                public static void Initialize()
                {
                    // Using Http to be friendly with outbound firewalls
                    ServiceBusEnvironment.SystemConnectivity.Mode = 
                        ConnectivityMode.Http;

                    // Create the namespace manager which gives you access to 
                    // management operations
                    var namespaceManager = CreateNamespaceManager();

                    // Create the queue if it does not exist already
                    if (!namespaceManager.QueueExists(QueueName))
                    {
                        namespaceManager.CreateQueue(QueueName);
                    }

                    // Get a client to the queue
                    var messagingFactory = MessagingFactory.Create(
                        namespaceManager.Address, 
                        namespaceManager.Settings.TokenProvider);
                    OrdersQueueClient = messagingFactory.CreateQueueClient(
                        "OrdersQueue");
                }
            }
        }

4.  現在，您將確定自己的 **Initialize** 方法會受到呼叫。在 [**方案總管**] 中，按兩下 **Global.asax\Global.asax.cs**。

5.  將下列一行新增至 **Application_Start** 方法底部
    method:

        FrontendWebRole.QueueConnector.Initialize();

6.  最後，您會更新您先前建立的 Web 程式碼，以
    將項目提交至佇列。在 [**方案總管**] 中，
    按兩下您先前建立的 **Controllers\HomeController.cs**
    。

7.  依照下列方式更新 **Submit()** 方法，以取得佇列的訊息計數
    ：

        public ActionResult Submit()
        {            
            // Get a NamespaceManager which allows you to perform management and
            // diagnostic operations on your Service Bus Queues.
            var namespaceManager = QueueConnector.CreateNamespaceManager();

            // Get the queue, and obtain the message count.
            var queue = namespaceManager.GetQueue(QueueConnector.QueueName);
            ViewBag.MessageCount = queue.MessageCount;

            return View();
        }

8.  依照下列方式更新 **Submit(OnlineOrder order)** 方法，以將順序資訊提交至佇列
    ：

        public ActionResult Submit(OnlineOrder order)
        {
            if (ModelState.IsValid)
            {
                // Create a message from the order
                var message = new BrokeredMessage(order);
                
                // Submit the order
                QueueConnector.OrdersQueueClient.Send(message);
                return RedirectToAction("Submit");
            }
            else
            {
                return View(order);
            }
        }

9.  現在您可以重新執行應用程式。每次提交順序時，
    訊息計數就會增加。

    ![][18]

<h2>雲端組態管理員</h2>

Azure 支援一組受管理 API，其提供一致的方式，跨 Microsoft 雲端服務建立 Azure 服務用戶端 (例如服務匯流排) 的新執行個體。這些 API 可讓您具現化這些用戶端 (例如，**CloudBlobClient**、**QueueClient**、**TopicClient**)，不管應用程式是裝載在何處 (在內部部署、在 Microsoft 雲端服務、在網站，或在永續性 VM 角色中) 都一樣。您也可以使用這些 API，擷取具現化這些用戶端所需的組態資訊，以及變更組態，而不必重新部署呼叫端應用程式。這些 API 位於 [Microsoft.WindowsAzure.Configuration.CloudConfigurationManager][] 類別中。用戶端也有 API。

### Connection string

若要具現化用戶端 (例如，服務匯流排 **QueueClient**)，您可以以連接字串代表組態資訊。在用戶端，有個 **CreateFromConnectionString()** 方法可以使用該連接字串來具現化該用戶端類型。例如，假設有下列組態區段：

	<ConfigurationSettings>
    ...
    	<Setting name="Microsoft.ServiceBus.ConnectionString" value="Endpoint=sb://[yourServiceNamespace].servicebus.windows.net/;SharedSecretIssuer=[issuerName];SharedSecretValue=[yourDefaultKey]" />
	</ConfigurationSettings>

下列程式碼會擷取連接字串、建立佇列，以及具現化對佇列的連線：

	QueueClient Client; 

	string connectionString =
     CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");
	
    var namespaceManager =
     NamespaceManager.CreateFromConnectionString(connectionString); 

	if (!namespaceManager.QueueExists(QueueName))
    {
        namespaceManager.CreateQueue(QueueName);
    }

	// Initialize the connection to Service Bus Queue
	Client = QueueClient.CreateFromConnectionString(connectionString, QueueName);

下節中的程式碼會使用這些組態管理 API。

<h2>建立背景工作角色</h2>

您現在將建立背景工作角色來處理所提交的
順序。本範例使用 **Worker Role with Service Bus Queue** Visual Studio 專案範本。首先，您將使用 Visual Studio 中的伺服器總管來取得必要的認證。

1. 如果您已依**使用 Visual Studio 伺服器總管設定命名空間**一節中所述，將 Visual Studio 連線至您的 Azure 帳戶，請直接跳到步驟 5。 

3. 從 Visual Studio 中的功能表列，選擇 [**檢視**]，然後按一下 [**伺服器總管**]。[**服務匯流排**] 節點會出現在伺服器總管階層內的 **Azure** 下方，如下圖所示。

	![][21]

2. 在 [伺服器總管] 中，展開 **Azure**、以滑鼠右鍵按一下 [**服務匯流排**]，然後按一下 [**Add New Connection**]。

3. 在 [**Add Connection**] 對話方塊中，輸入服務命名空間的名稱、簽發者名稱，以及簽發者金鑰。然後按一下 [**確定**] 進行連接。

	![][22]

4.  在 Visual Studio 的 [**方案總管**] 中，以滑鼠右鍵按一下 [
    **Roles**] 資料夾 (位於 [**MultiTierApp**] 專案下。

5.  按一下 [**新增**]，然後按一下 [**新的背景工作角色專案**]。[**加入新的角色專案**] 對話方塊隨即出現。

	![][26]

6.  在 [**加入新的角色專案**] 對話方塊中，按一下 [**Worker Role with Service Bus Queue**]，如下圖所示：

	![][23]

7.  在 [**名稱**] 方塊中，將專案命名為 **OrderProcessingRole**。然後按一下 [**新增**]。

8.  在 [伺服器總管] 中，於您的服務命名空間的名稱上按一下滑鼠右鍵，然後按一下 [**屬性**]。在 Visual Studio 的 [**屬性**] 窗格中，第一個項目包含已填入服務命名空間端點 (其含必要的授權認證) 的連接字串。例如，請參閱下圖。按兩下 **ConnectionString**，然後按 **Ctrl+C** 將此字串複製到剪貼簿。

	![][24]

9.  在 [方案總管] 中，以滑鼠右鍵按一下在步驟 7 中建立的**OrderProcessingRole** (請確定您是在 [**角色**] 下而不是類別下的 **OrderProcessingRole** 上按一下滑鼠右鍵)。然後按一下 [**屬性**]。

10.  在 [**屬性**] 對話方塊的 [**設定**] 索引標籤中，按一下 **Microsoft.ServiceBus.ConnectionString** 的 [**值**] 方塊內部，如然後貼上您在步驟 8 中複製的端點值。

	![][25]

11.  建立 **OnlineOrder** 類別，以代表您從佇列處理它們時的順序。您可以重複使用已建立的類別。在 [方案總管] 中，以滑鼠右鍵按一下 **OrderProcessingRole** 專案 (在專案而不是角色上按一下滑鼠右鍵)。按一下 [**新增**]，然後按一下 [**現有項目**]。

12. 瀏覽至 **FrontendWebRole\Models** 的子資料夾，並按兩下 **OnlineOrder.cs** 將其新增至此專案。

13. 在 WorkerRole.cs 中，將 **WorkerRole.cs** 中的 **QueueName** 變數值從 `"ProcessingQueue"` 取代為 `"OrdersQueue"`，如下列程式碼所示：

		// The name of your queue
		const string QueueName = "OrdersQueue";

14. 在 WorkerRole.cs 檔案頂端新增下列 using 陳述式：

		using FrontendWebRole.Models;

15. 在 `Run()` 函數的 `OnMessage` 呼叫內，於 `try` 子句內新增下列程式碼：

		Trace.WriteLine("Processing", receivedMessage.SequenceNumber.ToString());
		// View the message as an OnlineOrder
		OnlineOrder order = receivedMessage.GetBody<OnlineOrder>();
		Trace.WriteLine(order.Customer + ": " + order.Product, "ProcessingMessage");
		receivedMessage.Complete();

16.  您已完成應用程式。您可以依照先前的方式完整測試
    應用程式，只要按 F5 即可。請注意，訊息計數不會增加，因為背景工作角色會處理佇列中的項目，並將它們標示為完成。您可以檢視 Azure 計算模擬器 UI，以查看背景工作角色的追蹤輸出
    。您
    可以在工作列的通知區域中以滑鼠右鍵按一下通知區域中的模擬器圖示，
    然後選取 [**顯示計算模擬器 UI**]，以執行此動作。

    ![][19]

    ![][20]

<h2><a name="nextsteps"></a>後續步驟</h2>  

若要深入了解服務匯流排，請參閱下列資源：  
  
* [Azure 服務匯流排][sbmsdn]  
* [服務匯流排作法][sbwacom]  
* [如何使用服務匯流排佇列][sbwacomqhowto]  

若要深入了解多層式案例，或了解如何將應用程式部署至雲端服務，請參閱：  

* [使用儲存體資料表、佇列與 Blob 的 .NET 多層式應用程式][mutitierstorage]  

您可能想要在 Azure 網站而非 Azure 雲端服務中實作多層式應用程式的前端。若要深入了解網站與雲端服務之間的差異，請參閱 [Azure 執行模型][executionmodels]。

若要以標準 Web 專案而非雲端服務 Web 角色的形式實作您在本教學課程中建立的應用程式，請在遵循本教學課程中的步驟時注意下列差異：

1. 當您建立專案時，請選擇 **Web** 類別中的 **ASP.NET MVC 4 Web Application** 專案範本，而不是 [**雲端**] 類別中的 [**雲端服務**] 範本。然後，遵循建立 MVC 應用程式的相同指示，直到**雲端組態管理員**一節。

2. 建立背景工作角色時，請在新的個別方案中建立，類似於 Web 角色的原始指示。不過，您現在只在雲端服務專案中建立背景工作角色。然後，遵循建立背景工作角色的相同指示。

3. 您可以個別測試前端與後端，也可以同時在不同 Visual Studio 執行個體中執行這兩者。

若要了解如何將前端部署至 Azure 網站，請參閱[將 ASP.NET Web 應用程式部署至 Azure 網站](http://azure.microsoft.com/develop/net/tutorials/get-started/)。若要了解如何將後端部署至 Azure 雲端服務，請參閱[使用儲存體資料表、佇列與 Blob 的 .NET 多層式應用程式][mutitierstorage]。


  [0]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-01.png
  [1]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-100.png
  [sbqueuecomparison]: http://msdn.microsoft.com/library/windowsazure/hh767287.aspx
  [2]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-101.png
  [取得工具和 SDK (英文)]: http://go.microsoft.com/fwlink/?LinkId=271920
  [3]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-3.png
  
  
  
  [Azure 管理入口網站]: http://manage.windowsazure.com
  [6]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/sb-queues-03.png
  [7]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/sb-queues-04.png
  [8]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-09.png
  [9]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-10.jpg
  [10]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-11.png
  [11]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-02.png
  [12]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-12.png
  [13]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-13.png
  [14]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-33.png
  [15]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-34.png
  [16]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-35.png
  [17]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-36.png
  [18]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-37.png
  
  [19]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-38.png
  [20]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-39.png
  [21]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/SBExplorer.png
  [22]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/SBExplorerAddConnect.png
  [23]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/SBWorkerRole1.png
  [24]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/SBExplorerProperties.png
  [25]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/SBWorkerRoleProperties.png
  [26]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/SBNewWorkerRole.png
  [27]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-27.png
  [28]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-40.png
  [30]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/sb-queues-09.png
  [31]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/sb-queues-06.png
  [32]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-41.png
  [33]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-4-2-WebPI.png
  [34]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/VSProperties.png
  [sbmsdn]: http://msdn.microsoft.com/library/windowsazure/ee732537.aspx  
  [sbwacom]: /zh-tw/documentation/services/service-bus/  
  [sbwacomqhowto]: /zh-tw/develop/net/how-to-guides/service-bus-queues/  
  [mutitierstorage]: /zh-tw/develop/net/tutorials/multi-tier-web-site/1-overview/ 
  [executionmodels]: http://azure.microsoft.com/develop/net/fundamentals/compute/

<!--HONumber=46--> 
