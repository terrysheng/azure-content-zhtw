<properties
   pageTitle="如何從 Visual Studio 將 Web 應用程式移轉並發佈至 Azure 雲端服務 | Microsoft Azure"
   description="了解如何使用 Visual Studio 將 Web 應用程式移轉並發佈至 Azure 雲端服務。"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="09/30/2015"
   ms.author="tarcher" />

# 作法：從 Visual Studio 將 Web 應用程式移轉並發佈至 Azure 雲端服務

若要利用 Azure 的主機服務和延展性，您可以將 Web 應用程式移轉並發佈至 Azure 雲端服務。只要對現有應用程式做少許變更，即可在 Azure 中執行 Web 應用程式。

>[AZURE.NOTE]本主題是關於部署到雲端服務，而不是網站。如需部署至網站的相關資訊，請參閱[在 Azure App Service 中部署 Web 應用程式](web-sites-deploy.md)。

如需 Visual C# 和 Visual Basic 所支援的特定範本清單，請參閱本主題後面的＜支援的專案範本＞一節。

您必須先從 Visual Studio 啟用 Azure 的 Web 應用程式。下圖顯示藉由新增用於部署的 Azure 專案來發佈現有 Web 應用程式的主要步驟。此程序會將具有必要 Web 角色的 Azure 專案新增至您的方案。根據您所擁有的 Web 專案類型，系統會在服務封裝需要其他組件以用於部署時，一併更新組件的專案屬性。

![將 Web 應用程式發佈至 Microsft Azure](./media/vs-azure-tools-migrate-publish-web-app-to-cloud-service/IC748917.png)

>[AZURE.NOTE] 方案中的 Web 專案才會顯示 [轉換]、[轉換成 Azure 雲端服務專案] 命令。例如，方案中的 Silverlight 專案就不會顯示此命令。
當您建立服務封裝或將應用程式發佈至 Azure 時，可能會出現警告或錯誤。這些警告和錯誤可以幫助您在部署至 Azure 之前先修正問題。例如，您可能會收到遺失組件的警告。如需如何將警告視為錯誤的詳細資訊，請參閱[使用 Visual Studio 設定 Azure 雲端服務專案](vs-azure-tools-configuring-an-azure-project.md)。在建置應用程式、使用計算模擬器在本機執行應用程式，或將應用程式發佈至 Azure 時，您可能會在 [錯誤清單] 視窗中看到下列錯誤：「指定的路徑和 (或) 檔案名稱太長」。如果完整的 Azure 專案名稱長度太長，就會發生此錯誤。包括完整路徑在內的專案名稱長度不能超過 146 個字元。例如，以下是包括針對 Silverlight 應用程式所建立之 Azure 專案的檔案路徑在內的完整專案名稱：`c:\users<user name>\documents\visual studio 2015\Projects\SilverlightApplication4\SilverlightApplication4.Web.Azure.ccproj`。您可能必須將方案移到其他具有較短路徑的目錄，以縮減完整專案名稱的長度。

若要從 Visual Studio 將 Web 應用程式移轉並發佈至 Azure，請遵循下列步驟。

## 啟用 Web 應用程式以部署至 Azure

### 啟用 Web 應用程式以部署至 Azure

1. 若要啟用 Web 應用程式以部署至 Azure，請在方案中開啟 Web 專案的捷徑功能表，然後選擇 [新增 Azure 部署專案]。

    此時會發生下列動作：

    - 方案中新增了應用程式的 Azure 專案，名稱為 `<name of the web project>.Azure`。

    - 此 Azure 專案中已新增 Web 專案的 Web 角色。

    - MVC 2、MVC 3、MVC 4 和 Silverlight 商務應用程式所需的組件皆已將 [複製到本機] 屬性設定為 true。此設定會將這些組件新增到用於部署的服務封裝中。

  >[AZURE.IMPORTANT] 如果此 Web 應用程式有其他必要組件或檔案，您必須手動設定這些檔案的屬性。如需如何設定這些屬性的相關資訊，請參閱本文後面的＜將檔案包含在服務封裝內＞一節。

  >[AZURE.NOTE] 如果方案中的 Azure 專案已有特定 Web 專案的 Web 角色，此 Web 專案的捷徑功能表上就不會顯示 [轉換]、[轉換成 Azure 雲端服務專案]。

如果您的 Web 應用程式中有多個 Web 專案，而您想要為每個 Web 專案建立 Web 角色，則必須為每個 Web 專案執行此程序中的步驟。這麼做會為每個 Web 角色建立個別的 Azure 專案。您就可以個別發佈每一個 Web 專案。或者，您可以手動對 Web 應用程式中的現有 Azure 專案新增其他 Web 角色。若要這麼做，請開啟 Azure 專案中 [角色] 資料夾的捷徑功能表，然後依序選擇 [新增]、[方案中的 Web 角色專案] 和要新增做為 Web 角色的專案，然後選擇 [確定] 按鈕。

## 將 Azure SQL Database 用於應用程式

如果 Web 應用程式的連接字串使用內部部署的 SQL Server Database ，您必須變更此連接字串，使其改用 Azure 裝載之 SQL Database 的執行個體。

>[AZURE.IMPORTANT]您的訂用帳戶必須能讓您使用 SQL Database。如果您從 Azure 管理入口網站存取訂用帳戶，您可以確定訂用帳戶所提供的服務。下列指示適用於已發行的管理入口網站。如果您是使用預覽管理入口網站，請跳至下一個程序。

### 對連接字串使用 Web 角色中的 SQL Database 執行個體

1. 若要在 Azure 管理入口網站中建立 SQL Database 的執行個體，請遵循下列文章中的步驟：[建立 SQL Database 伺服器](http://go.microsoft.com/fwlink/?LinkId=225109)。

    >[AZURE.NOTE]在設定 SQL Database 執行個體的防火牆規則時，您必須選取 [允許其他 Azure 服務存取此伺服器] 核取方塊。

1. 若要建立用於連接字串的 SQL Database 執行個體，請遵循下列文章中下一節的步驟：[建立 SQL Database](http://go.microsoft.com/fwlink/?LinkId=225110)。

1. 若要複製 ADO.NET 連接字串以用於您的連接字串，請在 Azure 管理入口網站中執行下列步驟。

  1. 選擇 [資料庫] 按鈕，然後開啟您用來建立 SQL Database 執行個體之訂用帳戶的節點。

  1. 若要顯示可用的 SQL Database 執行個體，請選擇 [SQL Database] 節點。

  1. 若要顯示資料庫的屬性，請選擇資料庫。[屬性] 檢視隨即出現。

      >[AZURE.NOTE]如果 [屬性] 檢視未出現，您可能需要使用分隔線來加以開啟。

  1. 若要顯示連接字串，請選擇 [檢視] 旁的省略符號 (...) 按鈕。

      [連接字串] 對話方塊隨即會出現。

  1. 若要複製 ADO.NET 連接字串，請醒目提示文字，然後選擇 Ctrl + C 鍵。

  1. 若要關閉對話方塊，請選擇 [關閉] 按鈕。

1. 若要取代 web.config 檔中的連接字串以使用此 SQL Database 執行個體，請開啟 web.config 檔、醒目提示現有連接字串項目，然後選擇 Ctrl + V 鍵。SQL Database 執行個體的 ADO.NET 連接字串即會取代現有連接字串。

1. 您也必須在連接字串中加入 `MultipleActiveResultSets=True` 參數。連接字串應有如下格式：

    ```
    connectionString=”Server=tcp:<database_server>.database.windows.net,1433;Database=<database_name>;User ID=<user_name>@<database_server>;Password=<myPassword>;Trusted_Connection=False;Encrypt=True;MultipleActiveResultSets=True"
    ```

1. (選用) 另一種在 web.config 檔中直接變更連接字串的方法是，依據您用來建立服務封裝的建置組態，在其中一個 web.config 轉換檔中加入一個區段。開啟 Web.Debug.Config 檔案或 Web.Release.Config 檔案。在此檔案中加入下列區段：

    ```
    XMLCopy<connectionStrings><addname="DefaultConnection"connectionString="Server=tcp:<database_server>.database.windows.net,1433;Database=<database_name>;User ID=<user_name>@<database_server>;Password=<myPassword>;Trusted_Connection=False;Encrypt=True;MultipleActiveResultSets=True"xdt:Transform="SetAttributes"xdt:Locator="Match(name)"/></connectionStrings>
    ```

1. 儲存修改過的檔案，然後重新發佈應用程式。

### 透過 Azure 管理入口網站使用 SQL Database 執行個體

1. 在 [Azure 管理入口網站](http://go.microsoft.com/fwlink/?LinkID=213885)中，選擇 [SQL Databases] 節點。

  - 如果您想要使用的 SQL Database 執行個體出現，請進行選擇來將其開啟。

  - 如果您尚未建立任何執行個體，請選擇適當連結，然後建立執行個體。

1. 開啟或建立資料庫執行個體後，選擇 [連接字串] 連結。

1. 在頁面底部選擇連結以進行防火牆設定，並接受預設值或設定您需要的值。

1. 複製 ADO.NET 連接字串、將它貼到 web.config 檔案，蓋掉舊的內部部署資料庫連接字串，並務必加上 `MultipleActiveResultSets=True`。

## 將 Web 應用程式發佈至 Azure

### 將 Web 應用程式發佈至 Azure

1. 若要在本機開發環境中使用 Azure 計算模擬器測試應用程式，請開啟 Web 角色的 Azure 專案捷徑功能表，然後選擇 [設定為啟始專案]。接下來選擇 [偵錯]、[開始偵錯]\(鍵盤：**F5**)。

    [啟動 Azure 偵錯環境] 對話方塊隨即開啟，並且會在瀏覽器中啟動應用程式。如需關於如何在計算模擬器中啟動每一種 Web 應用程式的特定詳細資訊，請參閱本節資料表。

1. 若要設定您的應用程式要發佈至 Azure 的服務，您必須擁有 Microsoft 帳戶和 Azure 訂用帳戶。使用下列主題中的步驟設定服務：[準備從 Visual Studio 發佈或部署 Azure 應用程式](vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md)。

1. 若要將 Web 應用程式發佈至 Azure，請開啟 Web 專案的捷徑功能表，然後選擇 [發佈至 Azure]。

    [發佈 Azure 應用程式] 對話方塊隨即開啟，且 Visual Studio 會開始部署程序。如需如何發佈應用程式的詳細資訊，請參閱[使用 Azure Tools 發佈雲端服務](vs-azure-tools-publishing-a-cloud-service.md)中的＜從 Visual Studio 發佈 Azure 應用程式＞。

    >[AZURE.NOTE]您也可以從 Azure 專案發佈 Web 應用程式。若要這麼做，請開啟 Azure 專案的捷徑功能表，然後選擇 [發佈]。

1. 若要查看部署進度，您可以檢視 [Azure 活動記錄檔] 視窗。部署程序開始時會自動顯示這個記錄檔。您可以展開活動記錄檔中的細目以顯示詳細資訊，如下圖所示：

    ![VST\_AzureActivityLog](./media/vs-azure-tools-migrate-publish-web-app-to-cloud-service/IC744149.png)

1. (選用) 若要取消部署程序，請開啟活動記錄檔中該細目的捷徑功能表，然後選擇 [取消並移除]。這會停止部署程序，並從 Azure 中刪除部署環境。

    >[AZURE.NOTE]若要在部署此部署環境後將其移除，您必須使用 Azure 管理入口網站。

1. (選用) 角色執行個體啟動後，Visual Studio 會自動在 [雲端總管] 或 [伺服器總管] 的 [Azure 計算] 節點中顯示部署環境。您可以從這裡檢視個別角色執行個體的狀態。

    下圖顯示 [伺服器總管] 中仍處於初始化狀態的角色執行個體：

    ![VST\_DeployComputeNode](./media/vs-azure-tools-migrate-publish-web-app-to-cloud-service/IC744134.png)

1. 若要在部署後存取應用程式，請在 [Azure 活動記錄檔] 中出現 [已完成] 狀態時，選擇部署旁邊的箭號。這會顯示 Web 應用程式在 Azure 中的 URL。請參閱下表，以取得如何從 Azure 啟動特定類型的 Web 應用程式的詳細資料。

    下表所列內容詳述如何從 Azure 啟動特定 Web 應用程式，或如何使用 Azure 計算模擬器在本機執行或偵錯 Web 應用程式：

    |Web 應用程式類型|使用計算模擬器在本機執行/偵錯|在 Azure 中執行|
    |---|---|---|
    |ASP.NET Web 應用程式|在功能表列上，選擇 [偵錯]、\[開始偵錯] (鍵盤：選擇 **F5** 鍵)。|選擇 [Azure 活動記錄檔] 的 [部署] 索引標籤中顯示的 URL 超連結，以在瀏覽器中載入起始頁。|
    |ASP.NET MVC 2 Web 應用程式|在功能表列上，選擇 [偵錯]、\[開始偵錯] (鍵盤：選擇 **F5** 鍵)。|選擇 [Azure 活動記錄檔] 的 [部署] 索引標籤中顯示的 URL 超連結，以在瀏覽器中載入起始頁。|
    |ASP.NET MVC 3 Web 應用程式|在功能表列上，選擇 [偵錯]、\[開始偵錯] (鍵盤：選擇 **F5** 鍵)。|選擇 [Azure 活動記錄檔] 的 [部署] 索引標籤中顯示的 URL 超連結，以在瀏覽器中載入起始頁。|
    |ASP.NET MVC 4 Web 應用程式|在功能表列上，選擇 [偵錯]、\[開始偵錯] (鍵盤：選擇 **F5** 鍵)。|選擇 [Azure 活動記錄檔] 的 [部署] 索引標籤中顯示的 URL 超連結，以在瀏覽器中載入起始頁。|
    |ASP.NET 空白 Web 應用程式|您必須在應用程式中加入設定做為 Web 專案起始頁的 .aspx 網頁。然後在功能表列上，選擇 [偵錯]、\[開始偵錯] (鍵盤：選擇 **F5** 鍵)。|如果應用程式中有預設的 .aspx 網頁，請選擇 [Azure 活動記錄檔] 的 [部署] 索引標籤中顯示的 URL 超連結，瀏覽器就會載入此網頁。如果您有不同的 .aspx 網頁，則必須使用下列格式的 URL 來瀏覽到此特定網頁：`<url for deployment>/<name of page>.aspx`|
    |Silverlight 應用程式|在功能表列上，選擇 [偵錯]、\[開始偵錯] (鍵盤：選擇 **F5** 鍵)。|您必須使用下列格式的 URL 來瀏覽到應用程式的特定網頁：`<url for deployment>/<name of page>.aspx`|
    |Silverlight 商務應用程式|在功能表列上，選擇 [偵錯]、\[開始偵錯] (鍵盤：選擇 **F5** 鍵)。|您必須使用下列格式的 URL 來瀏覽到應用程式的特定網頁：`<url for deployment>/<name of page>.aspx`|
    |Silverlight 瀏覽應用程式|在功能表列上，選擇 [偵錯]、\[開始偵錯] (鍵盤：選擇 **F5** 鍵)。|您必須使用下列格式的 URL 來瀏覽到應用程式的特定網頁：`<url for deployment>/<name of page>.aspx`|
    |WCF 服務應用程式|您必須將 .svc 檔案設定做為 WCF 服務專案的起始頁。然後在功能表列上，選擇 [偵錯]、\[開始偵錯] (鍵盤：選擇 **F5** 鍵)。|您必須使用下列格式的 URL 來瀏覽到應用程式的 svc 檔案：`<url for deployment>/<name of service file>.svc`|
    |WCF 工作流程服務應用程式|您必須將 .svc 檔案設定做為 WCF 服務專案的起始頁。然後在功能表列上，選擇 [偵錯]、\[開始偵錯] (鍵盤：選擇 **F5** 鍵)。|您必須使用下列格式的 URL 來瀏覽到應用程式的 svc 檔案：`<url for deployment>/<name of service file>.svc`|
    |ASP.NET 動態實體|在功能表列上，選擇 [偵錯]、\[開始偵錯] (鍵盤：選擇 **F5** 鍵)。|您必須更新連接字串 (請參閱下一節)。您還必須使用下列格式的 URL 來瀏覽到應用程式的特定網頁：`<url for deployment>/<name of page>.aspx`|
    |ASP.NET 動態資料 Linq to SQL|在功能表列上，選擇 [偵錯]、\[開始偵錯] (鍵盤：選擇 **F5** 鍵)。|您必須遵循以下程序中的步驟：將 SQL Azure 資料庫用於應用程式 (請參閱本主題前面的章節)。您還必須使用下列格式的 URL 來瀏覽到應用程式的特定網頁：`<url for deployment>/<name of page>.aspx`|

## 更新 ASP.NET 動態實體的連接字串

### 更新 ASP.NET 動態實體的連接字串

1. 若要建立可用於 ASP.NET 動態實體 Web 應用程式的 SQL Azure 資料庫，請遵循本主題前面的＜將 SQL Azure 資料庫用於應用程式＞程序的步驟。

1. 從 Azure 管理入口網站新增此資料庫所需的資料表和欄位。

1. 在 web.config 檔案中，此類型應用程式的連接字串具有下列格式：

    ```
    <addname="tempdbEntities"connectionString="metadata=res://*/Model1.csdl|res://*/Model1.ssdl|res://*/Model1.msl;provider=System.Data.SqlClient;provider connection string=";data source=<server name>\SQLEXPRESS;initial catalog=<database name>;integrated security=True;multipleactiveresultsets=True;App=EntityFramework";"providerName="System.Data.EntityClient"/>
    ```

    使用 SQL Azure 資料庫的 ADO.NET 連接字串更新 *connectionString* 值，如下所示：

    ```
    XMLCopy<addname="tempdbEntities"connectionString="metadata=res://*/Model1.csdl|res://*/Model1.ssdl|res://*/Model1.msl;provider=System.Data.SqlClient;provider connection string=";Server=tcp:<SQL Azure server name>.database.windows.net,1433;Database=<database name>;User ID=<user name>;Password=<password>;Trusted_Connection=False;Encrypt=True;multipleactiveresultsets=True;App=EntityFramework";"providerName="System.Data.EntityClient"/>
    ```

1. 若要在 web.config 檔案中儲存您對連接字串所做的變更，請在功能表列上選擇 [檔案]、[儲存 web.config]。

## 支援的專案範本

若要將 Web 應用程式發佈至 Azure，應用程式必須使用下表所列 C# 或 Visual Basic 的其中一個專案範本。

|專案範本群組|專案範本|
|---|---|
|Web|ASP.NET Web 應用程式|
|Web|ASP.NET MVC 2 Web 應用程式|
|Web|ASP.NET MVC 3 Web 應用程式|
|Web|ASP.NET MVC4 Web 應用程式|
|Web|ASP.NET 空白 Web 應用程式|
|Web|ASP.NET MVC 2 空白 Web 應用程式|
|Web|ASP.NET 動態資料實體 Web 應用程式|
|Web|ASP.NET 動態資料 Linq to SQL Web 應用程式|
|Silverlight|Silverlight 應用程式|
|Silverlight|Silverlight 商務應用程式|
|Silverlight|Silverlight 瀏覽應用程式|
|WCF|WCF 服務應用程式|
|WCF|WCF 工作流程服務應用程式|
|工作流程|WCF 工作流程服務應用程式|

## 後續步驟
如需有關發佈的詳細資訊，請參閱 [準備從 Visual Studio 發佈或部署 Azure 應用程式](vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md)。另請參閱[設定具名的驗證認證](vs-azure-tools-setting-up-named-authentication-credentials.md)。

<!-----HONumber=AcomDC_1217_2015-->
