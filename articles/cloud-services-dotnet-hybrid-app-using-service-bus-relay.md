<properties urlDisplayName="Hybrid Application" pageTitle="混合式內部部署/雲端應用程式 (.NET) - Azure" metaKeywords="Azure Service Bus tutorial,hybrid .NET" description="了解如何使用 Azure Service Bus Relay 建立 .NET 內部部署/雲端混合式應用程式。" metaCanonical="" services="service-bus" documentationCenter=".NET" title=".NET On-Premises/Cloud Hybrid Application Using Service Bus Relay" authors="sethm" solutions="" manager="timlt" editor="mattshel" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="09/15/2014" ms.author="sethm" />






# 使用服務匯流排轉送的 .NET 內部部署/雲端混合式應用程式

<h2>簡介</h2>

下列工具可讓您輕鬆使用 Azure 來開發混合式雲端應用程式：
Visual Studio 2013 和免費的 Azure SDK for.NET。本指南
假設您從未使用過 Azure。在不到
30 分鐘，您就可讓一個使用多項 Windows
Azure 資源的應用程式在雲端中運作。

您將了解：

-   如何建立或選擇現有的 Web 服務供
    Web 解決方案使用。
-   如何使用 Azure 服務匯流排轉送讓
    Azure 應用程式和別處裝載的 Web 服務共用資料。

[WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]

### 服務匯流排轉送如何協助混合式方案

商務解決方案通常由一組自訂程式碼組成，
目的是滿足新的和獨立的商務需求，並運用
既有的解決方案和系統所提供的功能
。

解決方案架構設計人員開始使用雲端來輕鬆處理
調整需求，並降低營運成本。這麼做之後，他們發現
要用來做為解決方案建置組塊的現有服務資產
都在公司防火牆內，無法供
供雲端解決方案輕易存取。許多內部服務的
建置或裝載方式，使得它們無法輕易地公開在
公司網路邊緣。

*服務匯流排轉送*的設計目的就是要利用現有的 
Windows Communication Foundation (WCF) Web 服務，並讓
公司週邊外的解決方案能夠安全地存取這些服務，
而不需要侵入式變更公司
網路基礎結構。這類服務匯流排轉送服務仍然裝載
於現有的環境內，但將接聽
傳入工作階段和要求的工作委派給雲端裝載的服務匯流排。
服務匯流排也
使用 Azure Active Directory 存取控制，以防止未經授權存取那些服務。

### 方案案例

在本教學課程中，您將建立一個 ASP.NET MVC 4 網站，
可讓您在產品庫存頁面上看到產品清單。

![][0]

本教學課程假設您在現有的
內部部署系統中有產品資訊，而且使用服務匯流排轉送來存取該
系統。這是以一個 Web 服務來模擬，此服務在簡單的
主控台應用程式中執行，且後背有一組記憶體中的產品所支援。您
將能夠在自己的電腦上執行此主控台應用程式，並
將 Web 角色部署到 Azure。如此一來，您將瞭解
Azure 資料中心內執行的 Web 角色實際上如何呼叫
您的電腦，即使幾乎可確定您的電腦
位於至少一個防火牆和網路位址轉譯
(NAT) 層的後方。

下列是已完成之 Web 應用程式的開始頁面螢幕擷取畫面。

![][1]

<h2>設定開發環境</h2>

您必須
取得工具並設定您的開發環境，才可以開始開發 Azure 應用程式。

1.  若要安裝 Azure SDK for .NET，請按一下底下按鈕：

    [取得工具和 SDK (英文)][]

2. 	按一下 [**安裝 SDK**]。

3. 	選擇您所使用的 Visual Studio 版本的連結。本教學課程中的步驟使用 Visual Studio 2013：

	![][42]

4.  當系統提示您執行或儲存 **WindowsAzureSDKForNet.exe** 時，請按一下 [
    **執行**]：

    ![][2]

5.  在 Web Platform Installer 中，按一下 [**安裝**] 以繼續安裝：

    ![][3]

6.  安裝完成之後，就萬事俱備，
    可開始開發。SDK 中的工具可讓您
    輕鬆地在 Visual Studio 中開發 Azure 應用程式。如果您
    未安裝 Visual Studio，則也會安裝免費的
    Visual Studio Express。

<h2>建立服務命名空間</h2>

若要開始在 Azure 中使用服務匯流排功能，您必須先
建立服務命名空間。服務命名空間提供範圍
容器，供您的應用程式內定址服務匯流排資源。 

您可以使用 [Azure 管理入口網站][]或 Visual Studio 伺服器總管來管理命名空間和服務匯流排訊息實體，但是只能從入口網站內建立新的命名空間。

###使用入口網站建立服務命名空間：

1.  登入 [Azure 管理入口網站][]。

2.  在管理入口網站的左側瀏覽窗格中，按一下 [
    **服務匯流排**]。

3.  在管理入口網站的下方窗格中，按一下 [**建立**]。   
    ![][5]

4.  在 [**加入新的命名空間**] 對話方塊中，輸入命名空間名稱。
    系統會立即檢查此名稱是否可用。   
    ![][6]

5.  確定命名空間名稱可用之後，請選擇
    應該裝載您的命名空間的國家或地區 (
    確定您使用的國家/地區與您部署
    計算資源的國家/地區相同)。

    重要事項：請挑選您想要選擇來部署應用程式的**相同區域**
    。這樣可以獲得最佳效能。

6.	按一下核取記號。系統現在會建立您的服務
    命名空間並啟用它。您可能要稍候幾分鐘的時間，
    讓系統為您的帳戶佈建資源。

	![][38]

您所建立的命名空間會顯示在管理入口網站，
需要一點時間來啟動。等到狀態變成 [**作用中**] 之後
再繼續。

<h2>取得命名空間的預設管理認證</h2>

若要在新的命名空間上執行管理作業，例如建立佇列，
您必須取得命名空間的管理認證
。

1.  在主視窗中，按一下服務命名空間的名稱。   

	![][39]

2.  按一下 [**連接資訊**]。   

	![][40]

3.  在 [**Access connection information**] 窗格中，找出 [**預設簽發者**] 和 [**預設金鑰**] 項目。   

4.  記下金鑰，或將它複製到剪貼簿。

###使用 Visual Studio 伺服器總管來管理服務命名空間：

若要使用 Visual Studio 而非管理入口網站來管理命名空間並取得連線資訊，請遵循[這裡](http://http://msdn.microsoft.com/zh-tw/library/windowsazure/ff687127.aspx)所述的程序 (**從 Visual Studio 連接到 Azure** 一節)。當您登入 Azure 時，在 [伺服器總管] 中，[**Microsoft Azure**] 樹狀結構下的 [**服務匯流排**] 節點中會自動填入您已建立的任何命名空間。在任一個命名空間上按滑鼠右鍵，然後按一下 [**屬性**]，即可在 Visual Studio [**屬性**] 窗格中，查看與此命名空間相關聯的連接字串及其他中繼資料。 

![][44]

請記下 **SharedAccessKey** 值，或將它複製到剪貼簿。


<h2>建立內部部署伺服器</h2>

首先，您將建置 (模擬) 內部部署產品目錄系統。
非常簡單，您可以將這視為實際的
內部部署產品目錄系統，具有我們試著整合的完整服務介面
。

此專案將啟動為 Visual Studio 主控台應用程式。
專案使用服務匯流排 NuGet 封裝來包含服務匯流排
程式庫和組態設定。NuGet Visual Studio 擴充功能
可讓您在 Visual Studio 和 Visual Studio Express 中輕鬆地安裝及更新程式庫和工具
。服務匯流排 NuGet 封裝是最簡單
的方法，可讓您取得服務匯流排 API，並為您的應用程式設定
所有服務匯流排相依性。如需有關使用 NuGet 和
服務匯流排封裝的詳細資訊，請參閱[使用 NuGet 服務匯流排封裝][]。

### 建立專案

1.  使用系統管理員權限，啟動 Microsoft Visual
    Studio 2013 或 Microsoft Visual Studio Express。若要
    以系統管理員權限啟動 Visual Studio，請以滑鼠右鍵按一下
    **Microsoft Visual Studio 2013 (或 Microsoft Visual Studio Express)**，然後按一下 [**以系統管理員身分執行**]。
2.  在 Visual Studio 中，按一下 [**檔案**] 功能表上的 [**新增**]，然後
    按一下 [**專案**]。

    ![][10]

3.  從 [**已安裝的範本**]，在 [**Visual C#**] 下按一下 [**主控台
    應用程式**]。在 [**名稱**] 方塊中，輸入名稱
    **ProductsServer**：

    ![][11]

4.  按一下 [**確定**] 以建立 **ProductsServer** 專案。

5.  在 [**方案總管**] 中以滑鼠右鍵按一下 [**ProductsServer**]，然後
    按一下 [**屬性**]。
6.  按一下左邊的 [**應用程式**] 索引標籤，然後確定 [**.NET
    Framework 4**] 或 [**.NET Framework 4.5**] 出現在 [**目標 Framework:**]下拉式清單中。如果沒有，請從下拉式清單中選取它，當系統提示您重新載入專案時，按一下 [**是**]
    。

    ![][12]

7.  如果已安裝 Visual Studio 的 NuGet 套件管理員，請跳至下一個步驟。否則，請造訪 [NuGet][] (英文)，然後按一下 [[安裝 NuGet]](http://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) (英文)。按照提示安裝 NuGet 套件管理員，然後重新啟動 Visual Studio。

7.  在 [**方案總管**] 中以滑鼠右鍵按一下 [**參考**]，然後按一下 [
    **管理 NuGet 封裝**...]
8.  在 NuGet 對話方塊的左欄中，按一下 [**線上**]。

9. 	在右欄中，按一下 [**搜尋**] 方塊，輸入 "**WindowsAzure**"，然後選取 [**Microsoft
    Azure 服務匯流排**] 項目。按一下 [**安裝**] 以完成
    安裝，然後關閉此對話方塊。

    ![][13]

    請注意，現在會參考必要的用戶端組件。

9.  加入用於產品連絡人的新類別。在 [**方案總管**] 中，
    以滑鼠右鍵按一下 [**ProductsServer**] 專案，按一下 [**加入**]，然後按一下 [
    **類別**]。

    ![][14]

10. 在 [**名稱**] 方塊中，輸入名稱 **ProductsContract.cs**。然後
    按一下 [**新增**]。
11. 在 **ProductsContract.cs** 中，將命名空間定義換成
    下列程式碼，其中定義服務的合約：

        namespace ProductsServer
        {
            using System.Collections.Generic;
            using System.Runtime.Serialization;
            using System.ServiceModel;

            // Define the data contract for the service
            [DataContract]
            // Declare the serializable properties
            public class ProductData
            {
                [DataMember]
                public string Id { get; set; }
                [DataMember]
                public string Name { get; set; }
                [DataMember]
                public string Quantity { get; set; }
            }

            // Define the service contract.
            [ServiceContract]
            interface IProducts
            {
                [OperationContract]
                IList<ProductData> GetProducts();

            }

            interface IProductsChannel : IProducts, IClientChannel
            {
            }
        }

12. 在 Program.cs 中，將命名空間定義換成下列
    程式碼，以加入其設定檔服務和主機：

        namespace ProductsServer
        {
            using System;
            using System.Linq;
            using System.Collections.Generic;
            using System.ServiceModel;

            // Implement the IProducts interface
            class ProductsService : IProducts
            {
                
                // Populate array of products for display on Website
                ProductData[] products = 
                    new []
                        {
                            new ProductData{ Id = "1", Name = "Rock", 
                                             Quantity = "1"},
                            new ProductData{ Id = "2", Name = "Paper", 
                                             Quantity = "3"},
                            new ProductData{ Id = "3", Name = "Scissors", 
                                             Quantity = "5"},
                            new ProductData{ Id = "4", Name = "Well", 
                                             Quantity = "2500"},
                        };

                // Display a message in the service console application 
                // when the list of products is retrieved
                public IList<ProductData> GetProducts()
                {
                    Console.WriteLine("GetProducts called.");
                    return products;
                }

            }

            class Program
            {
                // Define the Main() function in the service application
                static void Main(string[] args)
                {
                    var sh = new ServiceHost(typeof(ProductsService));
                    sh.Open();

                    Console.WriteLine("Press ENTER to close");
                    Console.ReadLine();

                    sh.Close();
                }
            }
        }

13. 在 [**方案總管**] 中，按兩下 [**app.config**] 檔案，
    在 **Visual Studio** 編輯器中開啟它。將
    **<system.ServiceModel>** 的內容換成下列 XML 程式碼。請務必
    將 *yourServiceNamespace* 換成您的服務
    命名空間名稱，將 *yourIssuerSecret* 換成您先前
    從 Azure 管理入口網站擷取的金鑰：

        <system.serviceModel>
          <extensions>
             <behaviorExtensions>
                <add name="transportClientEndpointBehavior" type="Microsoft.ServiceBus.Configuration.TransportClientEndpointBehaviorElement, Microsoft.ServiceBus, Version=2.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
              </behaviorExtensions>
              <bindingExtensions>
                 <add name="netTcpRelayBinding" type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Version=2.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
              </bindingExtensions>
          </extensions>
          <services>
             <service name="ProductsServer.ProductsService">
               <endpoint address="sb://yourServiceNamespace.servicebus.windows.net/products" binding="netTcpRelayBinding" contract="ProductsServer.IProducts"
        behaviorConfiguration="products"/>
             </service>
          </services>
          <behaviors>
             <endpointBehaviors>
               <behavior name="products">
                 <transportClientEndpointBehavior>
                    <tokenProvider>
                       <sharedSecret issuerName="owner" issuerSecret="yourIssuerSecret" />
                    </tokenProvider>
                 </transportClientEndpointBehavior>
               </behavior>
             </endpointBehaviors>
          </behaviors>
        </system.serviceModel>

14. 按 **F6**，或從 [**建置**] 功能表按一下 [**建置方案**]，以建置應用程式，確認工作到目前為止都正確。

<h2>建立 ASP.NET MVC 應用程式</h2>

在本節中，您將建置簡單的 ASP.NET 應用程式，
以顯示從產品服務擷取的資料。

### 建立專案

1.  確定 Microsoft Visual Studio 2013 是以系統管理員權限來執行。如果不是，若要
    以系統管理員權限啟動 Visual Studio，請以滑鼠右鍵按一下
    **Microsoft Visual Studio 2013 (或 Microsoft Visual Studio Express)**，然後按一下 [**以系統管理員身分執行**]。Microsoft
    Azure 計算模擬器 (本指南稍後討論) 需要
    以系統管理員權限啟動 Visual Studio。

2.  在 Visual Studio 中，按一下 [**檔案**] 功能表上的 [**新增**]，然後
    按一下 [**專案**]。

3.  從 [**已安裝的範本**]，在 [**Visual C#**] 下按一下 [**ASP.NET Web 應用程式**]。將專案命名為 **ProductsPortal**。然後
    按一下 [**確定**]。

    ![][15]

4.  從 [**選取範本**] 清單中，按一下 [**MVC**]，
    然後按一下 [**確定**]。

    ![][16]

5.  在 [**方案總管**] 中以滑鼠右鍵按一下 [**模型**]，按一下 [**加入**]，
    然後按一下 [**類別**]。在 [**名稱**] 方塊中，輸入名稱
    **Product.cs**。然後按一下 [**新增**]。

    ![][17]

### 修改 Web 應用程式

1.  在 Visual Studio 中，將 Product.cs 檔案中現有的
    命名空間定義換成下列程式碼：

        // Declare properties for the products inventory
        namespace ProductsWeb.Models
        {
            public class Product
            {
                public string Id { get; set; }
                public string Name { get; set; }
                public string Quantity { get; set; }
            }
        }

2.  在 Visual Studio 的 HomeController.cs 檔案中，
    將現有的命名空間定義取代為下列程式碼：

        namespace ProductsWeb.Controllers
        {
            using System.Collections.Generic;
            using System.Web.Mvc;
            using Models;

            public class HomeController : Controller
            {
                // Return a view of the products inventory
                public ActionResult Index(string Identifier, string ProductName)
                {
                    var products = new List<Product> 
                        {new Product {Id = Identifier, Name = ProductName}};
                    return View(products);
                }

            }
        }

3.  在 [**方案總管**] 中，展開 [Views\Shared]：

    ![][18]

4.  接著，按兩下 _Layout.cshtml，以在 Visual Studio 編輯器中開啟它。

5.  將所有出現的 **My ASP.NET Application** 變更為 **LITWARE's Products**。

6. 移除 [**首頁**]、[**關於**] 和 [**連絡人**] 連結。刪除反白顯示的程式碼：

	![][41]

7.  在 [**方案總管**] 中，展開 [Views\Home]：

    ![][20]

8.  按兩下 Index.cshtml，以在 Visual Studio 編輯器中開啟它。
    將檔案的整個內容取代為下列程式碼：
	
		@model IEnumerable<ProductsWeb.Models.Product>

		@{
    		ViewBag.Title = "Index";
		}

		<h2>Prod Inventory</h2>

		<table>
    		<tr>
        		<th>
            		@Html.DisplayNameFor(model => model.Name)
        		</th>
                <th></th>
        		<th>
            		@Html.DisplayNameFor(model => model.Quantity)
        		</th>
    		</tr>
	
		@foreach (var item in Model) {
    		<tr>
        		<td>
            		@Html.DisplayFor(modelItem => item.Name)
        		</td>
        		<td>
            		@Html.DisplayFor(modelItem => item.Quantity)
        		</td>
    		</tr>	
		}

		</table>


9.  若要確認工作到目前為止都正確，您可以按 **F6** 或
    **Ctrl+Shift+B** 以建置專案。


### 在本機執行應用程式

執行應用程式以驗證它是否能正常運作。

1.  確定 **ProductsPortal** 為作用中專案。以滑鼠右鍵按一下
    [**方案總管**] 中的專案名稱，然後選取 [**設為
    啟始專案**]
2.  在 **Visual Studio** 內，按 **F5**。
3.  您的應用程式應該就會出現在瀏覽器中並正在執行：

    ![][21]

    <h2>將應用程式準備好部署至 AZURE</h2>

    您可以將您的應用程式部署到 Azure 雲端服務或 Azure 網站。若要深入了解網站與雲端服務之間的差異，請參閱 [Azure 執行模型][executionmodels]。若要了解如何將應用程式部署至 Azure 網站，請參閱[將 ASP.NET Web 應用程式部署至 Azure 網站](http://www.windowsazure.com/zh-tw/develop/net/tutorials/get-started/) (英文)。本節包含將應用程式部署至 Azure 雲端服務的詳細步驟。

    若要將應用程式部署至雲端服務，您需要新增雲端服務專案部署專案至方案。
    部署專案中包含
    在雲端中正確執行應用程式所需的組態資訊
    。

    1.  若要將您的應用程式設為可部署至雲端，請以滑鼠右鍵按一下
        [**方案總管**] 中的 [**ProductsPortal**] 專案，
        按一下 [**轉換**]，然後按一下 [**轉換成 Azure 雲端服務專案**]。

        ![][22]

    2.  若要測試應用程式，請按 **F5**。
    3.  這將啟動 Azure 計算模擬器。計算
        模擬器會使用本機電腦來模擬在 Azure 中執行您的應用程式
        。您可以
        查看系統匣來確認模擬器已啟動：

        ![][23]

    4.  瀏覽器仍會顯示您的應用程式在本機執行，
        且它的外觀及功能與您
        稍早將它當成一般 ASP.NET MVC 4 應用程式來執行時一樣。

    <h2>組合在一起</h2>

    下一步是將內部部署產品伺服器與
    ASP.NET MVC 應用程式連結起來。

    1.  在 Visual Studio 中，重新開啟 (如果尚未開啟)
        **ProductsPortal** 專案 (您在「建立
        ASP.NET MVC 應用程式」一節中建立)。

    2.  類似「建立內部伺服器 」一節中的步驟，
        將 NuGet 封裝加入至專案 [參考]。在 [
        方案總管] 中以滑鼠右鍵按一下 [**參考**]，然後按一下 [
        **管理 NuGet 封裝**]。

    3.  搜尋 "WindowsAzure.ServiceBus" 並選取 [**Microsoft
        Azure 服務匯流排**] 項目。然後完成安裝程序並
        關閉此對話方塊。

    4.  在 [方案總管] 中以滑鼠右鍵按一下 [**ProductsPortal**
        ] 專案，然後按一下 [**加入**]，再按一下 [**現有項目**]。

    5.  瀏覽至 **ProductsContract.cs** 檔案 (從 
        **ProductsServer** 主控台專案)。按一下以反白顯示
        ProductsContract.cs。按一下 [**加入**]，然後
        按一下 [**加入做為連結**]。

        ![][24]

    6.  現在，在 Visual Studio 編輯器中開啟 **HomeController.cs** 檔案，
        將命名空間定義換成下列
        程式碼。請務必將 *yourServiceNamespace* 換成
        您的服務命名空間名稱，將 *yourIssuerSecret* 換成您的金鑰。
        這樣可讓用戶端呼叫內部部署服務，
        並傳回呼叫的結果。

            namespace ProductsWeb.Controllers
            {
                using System.Linq;
                using System.ServiceModel;
                using System.Web.Mvc;
                using Microsoft.ServiceBus;
                using Models;
                using ProductsServer;

                public class HomeController : Controller
                {
                    // Declare the channel factory
                    static ChannelFactory<IProductsChannel> channelFactory;

                    static HomeController()
                    {
                        // Create shared secret token credentials for authentication 
                        channelFactory = new ChannelFactory<IProductsChannel>(new NetTcpRelayBinding(), 
                            "sb://yourServiceNamespace.servicebus.windows.net/products");
                        channelFactory.Endpoint.Behaviors.Add(new TransportClientEndpointBehavior { 
                            TokenProvider = TokenProvider.CreateSharedSecretTokenProvider(
                                "owner", "yourIssuerSecret") });
                    }

                    public ActionResult Index()
                    {
                        using (IProductsChannel channel = channelFactory.CreateChannel())
                        {
                            // Return a view of the products inventory
                            return this.View(from prod in channel.GetProducts()
                                             select
                                                 new Product { Id = prod.Id, Name = prod.Name, 
                                                     Quantity = prod.Quantity });
                        }
                    }
                }
            }

    7.  在 [方案總管] 中以滑鼠右鍵按一下 [**ProductsPortal**
        ] 方案，按一下 [**加入**]，然後按一下 [**現有專案**]。

    8.  瀏覽至 **ProductsServer** 專案，然後按兩下 
         **ProductsServer.csproj** 方案檔以加入它。

    9.  在 [方案總管] 中以滑鼠右鍵按一下 [**ProductsPortal**
        ] 方案，然後按一下 [**屬性**]。

    10. 在左側，按一下 [**啟始專案**]。在
        右側，按一下 [**多個啟始專案**]。確定
        **ProductsServer**、**ProductsPortal.Azure** 和
        **ProductsPortal** 依序出現，且 [**啟動**] 設為
         **ProductsServer** 和 **ProductsPortal.Azure** 的動作，
        ，[**無**] 設為 **ProductsPortal** 的動作。
        例如：

        ![][25]

    11. 仍在 [屬性] 對話方塊中，按一下左側的 [**ProjectDependencies**]
        。

    12. 在 [**專案**] 下拉式清單中，按一下 [
        **ProductsServer**]。確定未核取 [**ProductsPortal**]，
        ，但已核取 [**ProductsPortal.Azure**]。然後按一下 [**確定**]：

        ![][26]

    <h2>執行應用程式</h2>

    1.  從 Visual Studio 的 [**檔案**] 功能表，按一下 [**全部儲存**]。

    2.  按 **F5** 以建置並執行應用程式。內部部署
        伺服器 (**ProductsServer** 主控台應用程式) 應該會先啟動
        ，接著，**ProductsWeb** 應用程式應該會在
        瀏覽器視窗中啟動，如以下螢幕擷取畫面所示。這次您
        將會看到從
        產品服務內部部署系統擷取的產品庫存清單資料。

        ![][1]

    <h2>將應用程式部署至 AZURE</h2>

    1.  在 [**方案總管**] 中以滑鼠右鍵按一下 [**ProductsPortal**] 專案，
        ，然後按一下 [**發佈至 Azure**]。

    2.  您可能必須登入，才能看到所有訂閱。

        按一下 [**Sign in to see more subscriptions**]：

        ![][27]

    3.  使用 Microsoft 帳戶登入。


    8.  按 [**下一步**]。如果您的訂用帳戶尚未包含任何代管
        服務，系統將要求您建立服務。代管服務
        做為您的 Microsoft
        Azure 訂用帳戶內的應用程式容器。輸入
        應用程式的識別名稱，並選擇可讓應用程式達到最佳化的
        (當使用者從這個地區存取時，
        載入時間會較快)。

        ![][32]

    9.  選取要將應用程式發佈至其中的代管服務。
        其餘設定請保留如下所示的預設值
        。按 [**下一步**]：

        ![][33]

    10. 在最後一個頁面上，按一下 [**發行**] 以啟動部署
        程序：

        ![][34]

        這將需要大約 5-7 分鐘的時間。由於這是
        您第一次發行，Azure 會佈建
        虛擬機器 (VM)、執行安全性強化、在 VM 上建立 Web
        角色來裝載應用程式、將您的程式碼部署至
        該 Web 角色，最後還設定負載平衡器和
        網路，以公開發行您的應用程式。

    11. 在進行發行時，您可以
        在 [**Azure 活動記錄檔**] 視窗中監視活動，此視窗
        通常停駐於 Visual Studio 或 Visual Web
        Developer 底部：

        ![][35]

    12. 當部署完成時，您可以
        按一下監視視窗中的 [**網站 URL**] 連結以檢視您的網站。

        ![][36]

        您的網站依賴內部部署伺服器，因此您必須
        在本機執行 **ProductsServer** 應用程式，網站
        才能正常運作。當您在雲端網站上執行要求時，
        您會看到要求進入內部部署主控台
        應用程式，如下列螢幕擷取畫面中的 "GetProducts called" 輸出所示
        。

        ![][37]

若要深入了解網站與雲端服務之間的差異，請參閱 [Azure 執行模型][executionmodels]。

<h2>停止並刪除您的應用程式</h2>

部署您的應用程式後，您可能想要停用它，以便您
在 750 小時/月 (31 天/月) 的免費伺服器時間內，再建置及部署其他應用程式
。

Azure 會依耗用的每小時伺服器時間對 Web 角色執行個體計費。
您的應用程式部署之後就會開始耗用伺服器時間，
即使執行個體未執行且處於停止狀態也一樣。
免費帳戶有 750 小時/月 (31 天/月) 的專用
虛擬機器伺服器時間可託管這些 Web 角色執行個體。

下列步驟示範如何停止並刪除您的
升級版本。

1.  登入 [Azure 管理入口網站]，
        按一下 [雲端服務]，然後按一下您的服務名稱。

2.  按一下 [**儀表板**] 索引標籤，然後按一下 [**停止**]，以暫時停用您的應用程式。您
        只要按一下 [啟動] 即可重新啟動它。按一下 [**刪除**] 可從 Azure 中完全移除應用程式，
        而且無法還原。

	![][43]

<h2><a name="nextsteps"></a>後續步驟</h2>  

若要深入了解服務匯流排，請參閱下列資源：  
  
* [Azure 服務匯流排][sbmsdn]  
* [服務匯流排作法][sbwacom]  
* [如何使用服務匯流排佇列][sbwacomqhowto]  


  [0]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hybrid.png
  [1]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/App2.png
  [取得工具和 SDK (英文)]: http://go.microsoft.com/fwlink/?LinkId=271920
  [NuGet]: http://nuget.org
  [2]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-3.png
  [3]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-4-2-WebPI.png
  
  
  [Azure 管理入口網站]: http://manage.windowsazure.com
  [5]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/sb-queues-03.png
  [6]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/sb-queues-04.png
  
  
  
  [使用 NuGet 服務匯流排封裝]: http://go.microsoft.com/fwlink/?LinkId=234589
  [10]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-1.png
  [11]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-con-1.png
  [12]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-con-3.png
  [13]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-13.png
  [14]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-con-4.png
  [15]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-2.png
  [16]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-4.png
  [17]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-7.jpg
  [18]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-10.jpg
  
  [20]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-11.png
  [21]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/App1.png
  [22]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-21.png
  [23]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-22.png
  [24]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-12.png
  [25]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-13.png
  [26]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-14.png
  [27]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-33.png
  
  
  [30]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-36.png
  [31]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-37.png
  [32]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-38.png
  [33]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-39.png
  [34]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-40.png
  [35]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-41.png
  [36]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/App2.png
  [37]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-service1.png
  [38]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-27.png
  [39]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/sb-queues-09.png
  [40]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/sb-queues-06.png
  [41]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-40.png
  [42]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-41.png
  [43]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-43.png
  [44]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/VSProperties.png

  [sbmsdn]: http://msdn.microsoft.com/zh-tw/library/windowsazure/ee732537.aspx  
  [sbwacom]: /zh-tw/documentation/services/service-bus/  
  [sbwacomqhowto]: /zh-tw/develop/net/how-to-guides/service-bus-queues/
  [executionmodels]: http://www.windowsazure.com/zh-tw/develop/net/fundamentals/compute/
