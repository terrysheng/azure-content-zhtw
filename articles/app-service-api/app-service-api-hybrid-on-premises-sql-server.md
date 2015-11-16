<properties 
	pageTitle="使用混合式連線從 Azure App Service 內的 API 應用程式連線至內部部署 SQL Server" 
	description="在 Microsoft Azure 上建立 API 應用程式，並將它連接到內部部署 SQL Server 資料庫"
	services="app-service\api" 
	documentationCenter="" 
	authors="TomArcher" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/03/2015" 
	ms.author="tdykstra"/>

# 使用混合式連線從 Azure App Service 內的 API 應用程式連線至內部部署 SQL Server

「混合式連線」可將 [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) API 應用程式連接到使用靜態 TCP 連接埠的內部部署資源。支援的資源包括 Microsoft SQL Server、MySQL、HTTP Web API、行動服務和大部分的自訂 Web 服務。

在此教學課程中，您將學習如何在使用新的「混合式連線」連線至本機內部部署 SQL Server 資料庫的 [Azure Preview](http://go.microsoft.com/fwlink/?LinkId=529715)中建立 App Service API 應用程式。本教學課程假設您沒有使用 Azure 或 SQL Server 的經驗。

>[AZURE.NOTE]如果您想在註冊 Azure 帳戶前開始使用 Azure App Service，請移至[試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751)，即可在 App Service 中立即建立短期入門 Web 應用程式。不需要信用卡；沒有承諾。

## 必要條件

若要完成本教學課程，您將需要下列產品。所有產品都有免費版本可使用，因此您可以免費進行 Azure 解決方案開發。

- **Azure 訂閱** - 如需免費訂閱，請參閱 [Azure 免費試用](/pricing/free-trial/)。 

- **Visual Studio** - 若要下載 Visual Studio 2013 或 Visual Studio 2015 的免費試用版，請參閱 [Visual Studio 下載](http://www.visualstudio.com/downloads/download-visual-studio-vs)。在繼續之前安裝其中一種。(本教學課程中是使用 Visual Studio 2013 的螢幕擷取畫面)

- **SQL Server 2014 Express with Tools** - 請在 [Microsoft Web Platform Database 頁面](https://www.microsoft.com/zh-TW/download/details.aspx?id=42299)下載免費的 Microsoft SQL Server Express。在本教學課程稍後，您會看到如何[安裝 SQL Server](#InstallSQLDB) 以確保正確設定。

- **SQL Server Management Studio Express** - 此項目隨附於前述的 SQL Server 2014 Express with Tools 下載中，但您必須個別加以安裝，您可以從 [SQL Server Express 下載頁面](https://www.microsoft.com/zh-TW/download/details.aspx?id=42299)加以下載並安裝。

本教學課程假設您具有 Azure 訂閱、您已安裝 Visual Studio 2013，並且已安裝或啟用 .NET Framework 3.5。本教學課程將說明如何在可與 Azure 混合式連線功能妥善搭配運作的組態中安裝 SQL Server 2014 Express (使用靜態 TCP 連接埠的預設執行個體)。在開始本教學課程之前，如果您尚未安裝 SQL Server，請先從前述位置下載 (但不要安裝) SQL Server 2014 Express with Tools。

### 注意事項
若要透過混合式連線使用內部部署 SQL Server 或 SQL Server Express 資料庫，必須在靜態連接埠上啟用 TCP/IP。SQL Server 上的預設執行個體會使用靜態連接埠 1433，但指定的執行個體則否。

安裝內部部署混合式連線管理員代理程式的電腦：

- 必須有透過下列連接埠的 Azure 輸出連線：

> <table border="1">
    <tr>
       <th><strong>連接埠</strong></th>
        <th>理由</th>
    </tr>
    <tr>
        <td>80</td>
        <td><strong>必要</strong>可供 HTTP 連接埠進行憑證驗證以及可供進行資料連線 (選用)。</td>
    </tr>
    <tr>
        <td>443</td>
        <td><strong>選用</strong>可供進行資料連線。如果無法使用 443 的輸出連線，則使用 TCP 連接埠 80。</td>
    </tr>
	<tr>
        <td>5671 和 9352</td>
        <td><strong>建議</strong>但可供進行資料連線 (選用)。請注意，此模式通常會產生較高的輸送量。如果無法使用這些連接埠的輸出連線，則使用 TCP 連接埠 443。</td>
	</tr>
</table>

- 必須能夠連繫內部部署資源的 *hostname*:*portnumber*。 

本文中的步驟假設您使用將主控內部部署混合式連線代理程式之電腦中的瀏覽器。

如果您已在組態和符合前述條件的環境中安裝 SQL Server，您可以直接開始[在內部部署中建立 SQL Server 資料庫](#CreateSQLDB)。

<a name="InstallSQL"></a>
## 在內部部署中安裝 SQL Server Express、啟用 TCP/IP 及建立 SQL Server 資料庫

本節說明如何安裝 SQL Server Express、啟用 TCP/IP 及建立資料庫，讓您的 API 應用程式可在 [Azure Preview 入口網站](https://portal.azure.com)中運作。

<a name="InstallSQLDB"></a>
### 安裝 SQL Server Express

1. 若要安裝 SQL Server Express，請下載 **SQLEXPRWT\_x64\_ENU.exe** (64 位元版本) 或 **SQLEXPR\_x86\_ENU.exe** (32 位元版本) 檔案，並將其解壓縮至您要的資料夾。 

2. 在您解壓縮 SQL Server Express 安裝檔案後，執行 **setup.exe**。

3. 當 [SQL Server 安裝中心] 顯示時，選擇 [新增 SQL Server 獨立安裝或將功能加入至現有安裝]。

	![SQL Server 安裝中心](./media/app-service-api-hybrid-on-premises-sql-server/sql-server-installation-center.png)

4. 遵循指示接受預設選項和設定，直到您進入 [執行個體組態] 頁面。
	
5. 在 [執行個體組態] 頁面上，選擇 [預設執行個體]，然後按 [下一步]。
	
	![執行個體組態](./media/app-service-api-hybrid-on-premises-sql-server/instance-configuration.png)
	
	SQL Server 的預設執行個體會在靜態連接埠 1433 上接聽來自 SQL Server 用戶端的要求，而這正是混合式連線功能的需求。指定的執行個體會使用動態連接埠和 UDP，而混合式連線並不加以支援。
	
6. 接受 [伺服器組態] 頁面上的預設值，然後按 [下一步]。
	
7. 在 [資料庫引擎組態] 頁面上的 [驗證模式] 下，選擇 [混合模式 (SQL Server 驗證和 Windows 驗證)]，並提供密碼。
	
	![資料庫引擎組態](./media/app-service-api-hybrid-on-premises-sql-server/database-engine-configuration.png)
	
	在本教學課程中，您將使用 SQL Server 驗證。請務必記住您提供的密碼，因為後續將會用到。
	
8. 逐步完成精靈的其餘部分 - 接受預設值以完成安裝。

9. 關閉 [SQL Server 安裝中心] 對話方塊。

### 啟用 TCP/IP
若要啟用 TCP/IP，您必須使用您在安裝 SQL Server Express 時所安裝的 SQL Server 組態管理員。請先執行[為 SQL Server 啟用 TCP/IP 網路通訊協定](http://technet.microsoft.com/library/hh231672%28v=sql.110%29.aspx)中的步驟，再繼續作業。

<a name="CreateSQLDB"></a>
### 在內部部署中建立 SQL Server 資料庫

1. 在 [SQL Server Management Studio] 中，連接到您剛剛安裝的 SQL Server。針對 [**伺服器類型**]，選擇 [**資料庫引擎**]。對於 [伺服器名稱]，您可以使用 **localhost** 或您要使用之電腦的名稱。選擇 [SQL Server 驗證]，然後以您先前建立的 `sa` 使用者名稱和密碼登入。

	![連接到伺服器](./media/app-service-api-hybrid-on-premises-sql-server/connect-to-server.png)
	
	如果 [連線到伺服器] 對話方塊未自動出現，請導覽至左窗格中的 [物件總管]，依序按一下 [連接] 和 [資料庫引擎]。
	
2. 若要使用 SQL Server Management Studio 建立新資料庫，請在 [物件總管] 中以滑鼠右鍵按一下 [資料庫]，然後按一下 [**New Database**]。
	
	![建立新的資料庫功能表](./media/app-service-api-hybrid-on-premises-sql-server/new-database-menu.png)
	
3. 在 [新增資料庫] 對話方塊中，輸入 `LocalDatabase` 做為資料庫名稱，然後按一下 [確定]。
	
	![建立新的資料庫](./media/app-service-api-hybrid-on-premises-sql-server/new-database.png)
	
### 建立並填入 SQL Server 資料表

1. 在 SQL Server Management Studio 的 [物件總管] 中，展開 `LocalDatabase` 項目。

	![展開的資料庫](./media/app-service-api-hybrid-on-premises-sql-server/local-database-expanded.png)

2. 以滑鼠右鍵按一下 [資料表] 並選取操作功能表中的 [資料表...] 選項。

	![新的資料表功能表](./media/app-service-api-hybrid-on-premises-sql-server/new-table-menu.png)

3. 當資料表設計工具方格出現時輸入資料行資訊，如下圖所示。

	![新的資料表資料行](./media/app-service-api-hybrid-on-premises-sql-server/table-def.png)

4. 按下 **&lt;Ctrl>S** 來儲存新的資料表定義。系統將提示您輸入資料表名稱。輸入 `Speakers` 並按 [確定]。

	![儲存新的資料表](./media/app-service-api-hybrid-on-premises-sql-server/save-new-table.png)

5. 在 [物件總管] 中，以滑鼠右鍵按一下新建立的 `Speakers` 資料表，然後選取操作功能表中的 [編輯前 200 個資料列]。

	![編輯前 200 個資料列](./media/app-service-api-hybrid-on-premises-sql-server/edit-top-200-rows.png)

6. 當輸入/修改資料表資料的方格出現時，輸入部分測試資料，如下圖所示。

	![測試資料](./media/app-service-api-hybrid-on-premises-sql-server/speakers-data.png)

## 建立示範 API 應用程式並部署到 Azure 

本節將引導您逐步建立示範 API 應用程式。

1. 開啟 Visual Studio 2013，然後選取 [檔案] > [新增] > [專案]。 

2. 選取 [Visual C#] > [Web] > [ASP.NET Web 應用程式] 範本、清除 [將 Application Insights 加入至專案] 選項、將專案命名為 *SpeakersList*，然後按一下 [確定]。

	![](./media/app-service-api-hybrid-on-premises-sql-server/new-project.png)

3. 在 [新增 ASP.NET 專案] 對話方塊上，選取 [Azure API 應用程式] 專案範本，然後按一下 [確定]。

	![](./media/app-service-api-hybrid-on-premises-sql-server/new-project-api-app.png)

4. 在 [方案總管] 中，以滑鼠右鍵按一下 [模型] 資料夾，然後選取 [新增] > [類別...] 操作功能表選項。

	![](./media/app-service-api-hybrid-on-premises-sql-server/new-model-menu.png)

5. 將新檔案命名為 *Speaker.cs*，然後按一下 [新增]。

	![](./media/app-service-api-hybrid-on-premises-sql-server/new-model-class.png)

6. 將 `Speaker.cs` 檔案的整個內容取代為下列程式碼。

		namespace SpeakersList.Models
		{
			public class Speaker
			{
				public int Id { get; set; }
				public string Name { get; set; }
				public string EmailAddress { get; set; }
			}
		}

7. 在 [方案總管] 中，以滑鼠右鍵按一下 [控制器] 資料夾，然後選取 [新增] > [控制器...] 操作功能表選項。

	![](./media/app-service-api-hybrid-on-premises-sql-server/new-controller.png)

8. 在 [新增 Scaffold] 對話方塊中，選取 [Web API 2 控制器 - 空白] 選項，然後按一下 [新增]。

	![](./media/app-service-api-hybrid-on-premises-sql-server/add-scaffold.png)

9. 將控制器命名為 **SpeakersController** 並按一下 [新增]。

	![](./media/app-service-api-hybrid-on-premises-sql-server/add-controller-name.png)

10. 將 `SpeakersController.cs` 檔案中的程式碼取代為下列程式碼。請務必在 `connectionString` 中為 &lt;serverName> 與 &lt;password> 預留位置指定您自己的值。&lt;serverName> 值是 SQL Server 所在位置的機器名稱，而 &lt;password> 值則是當您安裝與設定 SQL Server 時所設定的值

	> [AZURE.NOTE]下列程式碼片段包含密碼資訊。這麼做是為了簡化示範。在實際生產環境中，您不應該在程式碼中儲存認證。相反地，請參閱[將密碼 (和其他機密資料) 部署到 ASP.NET 和 Azure 的最佳做法](http://www.asp.net/identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure)。

		using System;
		using System.Collections.Generic;
		using System.Linq;
		using System.Net;
		using System.Net.Http;
		using System.Web.Http;
		using SpeakersList.Models;
		using System.Data.Sql;
		using System.Data.SqlClient;
		
		namespace SpeakersList.Controllers
		{
		    public class SpeakersController : ApiController
		    {
		        [HttpGet]
		        public IEnumerable<Speaker> Get()
		        {
					// Instantiate List object that will be populated and returned
					// from this method.
		            List<Speaker> speakers = new List<Speaker>();
	
					// Build database connection string.
		            string connectionString = "Server=<serverName>;" +
		                                      "Initial Catalog=LocalDatabase;" +
		                                      "User Id=sa;Password=<password>;" +
		                                      "Asynchronous Processing=true;";
		
					// Build query string to select all three columns from the Speakers table.
		            string queryString = "SELECT Id, EmailAddress, Name FROM dbo.Speakers;";

					// Instantiate the SqlConnection object using the connection string.
		            using (SqlConnection connection = new SqlConnection(connectionString))
		            {
						// Instantiate the SqlCommand object using the connection and query strings.
		                SqlCommand command = new SqlCommand(queryString, connection);

						// Open the connection to the database.
		                connection.Open();

						// Execute the command and return a SqlDataReader object
						// that can be iterated.
		                SqlDataReader reader = command.ExecuteReader();
		                try
		                {
							// Read a row from the SqlDataReader object
		                    while (reader.Read())
		                    {
								// For each row, use the returned data to instantiate
								// and add to the List a new Speaker object.
		                        speakers.Add(new Speaker 
		                            { 
		                                Id = Convert.ToInt32(reader[0]), 
		                                EmailAddress = reader[1].ToString(), 
		                                Name = reader[2].ToString() 
		                            }
		                        );
		                    }
		                }
		                finally
		                {
							// Close the SqlDataReader object.
		                    reader.Close();
		                }
		            }
					// Return the List of Speaker objects.
		            return speakers;
		        }
		    }
		}

### 啟用 Swagger UI

啟用 Swagger UI 可讓您輕鬆測試您的 API 應用程式，而不需要撰寫用戶端程式碼來呼叫它。

1. 開啟 *App\_Start/SwaggerConfig.cs* 檔案，並搜尋 **EnableSwaggerUI**：

	![](./media/app-service-api-hybrid-on-premises-sql-server/swagger-ui-disabled.png)

2. 將下列幾行程式碼取消註解：

	        })
	    .EnableSwaggerUi(c =>
	        {

3. 當您完成時，請確認檔案看起來像下列影像。

	![](./media/app-service-api-hybrid-on-premises-sql-server/swagger-ui-enabled.png)

### 測試 API 應用程式

1. 若要檢視 API 測試頁，可透過 **&lt;Ctrl>F5** 在本機執行應用程式。您應該會看到類似下列影像的錯誤。

	![](./media/app-service-api-hybrid-on-premises-sql-server/error-forbidden.png)

2. 在瀏覽器的網址列中，於 URL 結尾加入 `/swagger`，然後按一下 **&lt;Enter>**。這會顯示您在上一節中啟用的 Swagger UI。

	![](./media/app-service-api-hybrid-on-premises-sql-server/swagger-ui.png)

3. 按一下 [Speakers] 區段加以展開。

4. 按一下 [GET /api/speakers] 以展開該區段。

5. 按一下 [Try it out!] 來檢視您先前輸入資料庫的資料。

	![](./media/app-service-api-hybrid-on-premises-sql-server/try-it-out.png)

### 部署 API 應用程式

既然您已經在本機測試過應用程式，就可以開始將應用程式部署至 Azure。

1. 在 [**方案總管**] 中，以滑鼠右鍵按一下專案 (而非方案)，然後按一下 [**發佈...**]。 

	![專案發佈功能表選項](./media/app-service-api-hybrid-on-premises-sql-server/publish-menu.png)

2. 按一下 [**設定檔**] 索引標籤，然後按一下 [**Microsoft Azure API 應用程式 (預覽)**]。

	![專案發佈功能表選項](./media/app-service-api-hybrid-on-premises-sql-server/publish-web.png)

3. 按一下 [新增] 在您的 Azure 訂用帳戶中佈建新的 API 應用程式。

	![選取現有的 API 服務對話方塊](./media/app-service-api-hybrid-on-premises-sql-server/publish-to-existing-api-app.png)

4. 在 [**建立 API 應用程式**] 對話方塊中，輸入下列項目：

	- 在 [**API 應用程式名稱**] 之下，輸入應用程式的名稱。 
	- 如果您有多個 Azure 訂用帳戶，請選取您要使用的訂用帳戶。
	- 在 [App Service 方案] 下，從現有的 App Service 方案選取，或選取 [建立新的 App Service 方案] 並輸入新方案的名稱。 
	- 在 [**資源群組**] 之下，從現有的資源群組進行選取，或選取 [**建立新的資源群組**] 並輸入名稱。此名稱必須是唯一的。請考慮使用應用程式名稱做為前置詞並附加一些個人資訊，例如 Microsoft ID (不含 @ 符號)。  
	- 在 [**存取層級**] 之下，選取 [**可供任何人使用**]。此選項可讓您的 API 完全公開，這在本教學課程沒有問題。稍後您可以透過 [Azure 預覽入口網站](https://portal.azure.com)限制存取。
	- 選取區域。

	按一下 [確定] 以在您的訂用帳戶中建立 API 應用程式。

	![設定 Microsoft Azure Web 應用程式對話方塊](./media/app-service-api-hybrid-on-premises-sql-server/publish-new-api-app.png)

5. 此程序可能會花費幾分鐘，因此 Visual Studio 會顯示一個對話方塊，通知您此程序已起始。按一下確認對話方塊上的 [確定]。

	![已開始建立 API 服務確認訊息](./media/app-service-api-hybrid-on-premises-sql-server/create-api-app-confirmation.png)

6. 當佈建程序在您的 Azure 訂用帳戶中建立資源群組和 API 應用程式時，Visual Studio 會在 [Azure App Service 活動] 視窗中顯示進度。

	![透過 Azure App Service 活動視窗的狀態通知](./media/app-service-api-hybrid-on-premises-sql-server/app-service-activity.png)

7. 佈建 API 應用程式後，以滑鼠右鍵按一下 [**方案總管**] 中的專案，並選取 [**發佈**] 來重新開啟 [發佈] 對話方塊。在上一個步驟中建立的發佈設定檔應會預先選取。按一下 [**發佈**] 開始部署程序。

	![部署 API 應用程式](./media/app-service-api-hybrid-on-premises-sql-server/publish2.png)

[Azure App Service 活動] 視窗會顯示部署進度，並在發佈程序完成時指示。

## 建立混合式連線和 BizTalk 服務 ##

1. 在瀏覽器中導覽至 [Azure 預覽入口網站](https://portal.azure.com)。 

2. 按一下左側的 [瀏覽全部] 選項。

3. 在 [瀏覽] 刀鋒視窗中，選取 [API 應用程式]。

4. 在 [API 應用程式] 刀鋒視窗中，找到您的 API 應用程式並按一下它。

5. 在您的 API 應用程式的刀鋒視窗中，按一下 [API 主控處理程序] 底下的值。
 
	![API 應用程式刀鋒視窗](./media/app-service-api-hybrid-on-premises-sql-server/api-app-blade-api-app-host.png)

6. 當 [API 主控處理程序] 刀鋒視窗顯示時，向下捲動至 [網路] 區段並按一下 [混合式連線]。
	
	![混合式連線](./media/app-service-api-hybrid-on-premises-sql-server/api-app-host-blade-hybrid-connections.png)
	
7. 在 [混合式連接] 刀鋒視窗上，按一下 [新增] > [新增混合式連線]。
	
8. 在 [建立混合式連線分頁] 上：
	- 在 [名稱] 中，提供連線的名稱。
	- 針對 [主機名稱]，輸入您的 SQL Server 主機電腦的電腦名稱。
	- 針對 [連接埠]，輸入 `1433` (SQL Server 的預設連接埠)。
	- 按一下 [Biz Talk 服務] 並輸入 BizTalk 服務的名稱。
	
	![建立混合式連線](./media/app-service-api-hybrid-on-premises-sql-server/create-biztalk-service.png)
		
9. 按兩次 [確定]。

	程序完成時，[通知] 區域會閃爍綠色 [成功]，而且 [混合式連線] 刀鋒視窗會顯示新的混合式連線，且狀態為 [未連線]。
	
	![已建立混合式連線](./media/app-service-api-hybrid-on-premises-sql-server/hybrid-not-connected-yet.png)
	
至此，您已完成雲端混合式連線基礎結構的重要部分。接下來，您將建立對應的內部部署部分。

<a name="InstallHCM"></a>
## 安裝內部部署混合式連線管理員以完成連線

[AZURE.INCLUDE [app-service-hybrid-connections-manager-install](../../includes/app-service-hybrid-connections-manager-install.md)]

混合式連線基礎結構已完成，就可以開始測試應用程式。

<a name="CreateASPNET"></a>
## 在 Azure 上測試已完成的 API 應用程式

1. 在 Azure Preview 入口網站中，回到 API 主控處理程序刀鋒視窗，並按一下 [URL] 底下的值。
	
2. 當您的瀏覽器顯示 API 應用程式的主控頁面時，請將 `/swagger` 附加至您瀏覽器網址列中的 URL，並按一下 **&lt;Enter>**。
	
3. 按一下 [Speakers] 區段加以展開。

4. 按一下 [GET /api/speakers] 以展開該區段。

5. 按一下 [Try it out!] 來檢視您先前輸入資料庫的資料。

	![](./media/app-service-api-hybrid-on-premises-sql-server/try-it-out-azure.png)
	
**恭喜！** 您已經建立一個可在 Azure 上執行，並且使用混合式連線連線至本機內部部署 SQL Server 資料庫的 API 應用程式。

## 另請參閱
[混合式連線概觀](http://go.microsoft.com/fwlink/p/?LinkID=397274)

[Josh Twist 介紹混合式連線 (第 9 頻道視訊)](http://channel9.msdn.com/Shows/Azure-Friday/Josh-Twist-introduces-hybrid-connections)

[混合式連線概觀](/services/biztalk-services/)

[BizTalk 服務：儀表板、監視器、調整、設定和混合式連線索引標籤](../biztalk-dashboard-monitor-scale-tabs/)

[透過絕佳的應用程式可攜性建置真實的混合式雲端 (第 9 頻道視訊)](http://channel9.msdn.com/events/TechEd/NorthAmerica/2014/DCIM-B323#fbid=)

[使用混合式連線從 Azure 行動服務連線至內部佈署 SQL Server](../mobile-services-dotnet-backend-hybrid-connections-get-started.md)

[使用混合式連線從 Azure 行動服務連線到內部部署 SQL Server (第 9 頻道視訊)](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Connect-to-an-on-premises-SQL-Server-from-Azure-Mobile-Services-using-Hybrid-Connections)

[ASP.NET 身分識別概觀](http://www.asp.net/identity)

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

<!---HONumber=Nov15_HO2-->