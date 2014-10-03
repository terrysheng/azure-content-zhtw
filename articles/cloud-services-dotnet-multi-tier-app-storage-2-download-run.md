<properties linkid="develop-net-tutorials-multi-tier-web-site-2-download-and-run" pageTitle="Azure Cloud Service Tutorial: ASP.NET MVC Web Role, Worker Role, Azure Storage Tables, Queues, and Blobs" metaKeywords="Azure tutorial, Azure storage tutorial, Azure multi-tier tutorial, MVC Web Role tutorial, Azure worker role tutorial, Azure blobs tutorial, Azure tables tutorial, Azure queues tutorial" description="Learn how to create a multi-tier app using ASP.NET MVC and Azure. The app runs in a cloud service, with web role and worker roles, and uses Azure storage tables, queues, and blobs." metaCanonical="" services="cloud-services,storage" documentationCenter=".NET" title="Azure Cloud Service Tutorial: ASP.NET MVC Web Role, Worker Role, Azure Storage Tables, Queues, and Blobs" authors="riande,tdykstra" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="cloud-services" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="riande,tdykstra"></tags>

# 設定和部署 Azure 電子郵件服務應用程式 - 第 2 課 (共 5 課)

這是由五個教學課程組成的系列中的第二個教學課程，示範如何建置和部署 Azure 電子郵件服務範例應用程式。如需應用程式和教學課程系列的詳細資訊，請參閱[系列的第一個教學課程][]。

在本教學課程中，您將了解：

-   如何安裝 Azure SDK 來設定電腦進行 Azure 開發。
-   如何在本機電腦上設定和測試 Azure 電子郵件服務應用程式。
-   如何將應用程式發佈至 Azure。
-   如何使用 Visual Studio 伺服器總管來檢視及編輯 Azure 資料表、佇列和 Blob。
-   如何設定追蹤和檢視追蹤資料。
-   如何增加背景工作角色執行個體的數目來調整應用程式。

### 教學課程區段

-   [設定開發環境][]
-   [下載並執行已完成的方案][]
-   [檢視 Visual Studio 中的開發儲存體][]
-   [建立 Azure 儲存體帳戶][]
-   [建立雲端服務][]
-   [設定 Azure 儲存體的應用程式][]
-   [將應用程式設為使用 SendGrid][]
-   [將應用程式部署至 Azure][]
-   [將應用程式從預備提升至實際執行][]
-   [設定和檢視追蹤資料][]
-   [新增另一個背景工作角色執行個體來處理增加的負載][]

[WACOM.INCLUDE [install-sdk-2013-only][]]

## <a name="downloadcnfg"></a><span class="short-header">下載並執行</span>下載並執行已完成的方案

1.  下載並解壓縮[已完成的方案][] (英文)。

2.  啟動 Visual Studio。

3.  從 [檔案] 功能表中，選擇 [開啟專案]，導覽至方案的下載位置，然後開啟方案檔。

4.  按 CTRL+SHIFT+B 建置解決方案。

    根據預設，Visual Studio 會自動還原未包含在 *.zip* 檔案中的 NuGet 封裝內容。如果封裝未還原，請移至 [管理方案的 NuGet 封裝] 對話方塊，然後按一下右上方的 [還原] 按鈕來手動安裝。

5.  在 [方案總管] 中，確定已選取 **AzureEmailService** 作為啟動專案。

6.  按 CTRL+F5 執行應用程式。

    應用程式首頁隨即出現在瀏覽器中。

7.  在功能表列中按一下 [郵寄清單]。

    (螢幕擷取畫面顯示的是 Visual Studio 2012 專案範本中的網頁樣式，但內容與 Visual Studio 2013 相同。)

    ![Run the App.][]

8.  按一下 [新建]。

9.  輸入部分測試資料，然後按一下 [建立]。

    ![Run the App.][1]

10. 建立幾個郵寄清單項目。

    ![Mailing List Index Page][]

11. 按一下 [訂閱者]，然後新增一些訂閱者。將 [已驗證] 設為 `true`.

    ![Subscriber Index Page][]

12. 準備新增訊息，方法是建立一個 *.txt* 檔案，包含您要傳送的電子郵件本文。然後建立一個 *.htm* 檔案，包含相同文字，但具有一些 HTML (例如，使訊息的其中一個字變為粗體或斜體)。您將在下一個步驟中使用這些檔案。

13. 按一下 [訊息]，然後新增一些訊息。選取您在前一個步驟中建立的檔案。請勿變更預設為未來一週的排程日期。等到您設定 SendGrid 後，應用程式才會傳送訊息。

    ![訊息建立頁面][]

    ![訊息索引頁面][]

    您輸入和檢視的資料會受到 Azure 儲存體模擬器的管理。儲存體模擬器會使用 SQL Server Express LocalDB 資料庫，模擬 Azure 儲存體在雲端中的運作方式。應用程式使用儲存體模擬器的原因是，那是您在下載專案時設定要使用的儲存體。此設定儲存在 **AzureEmailService** 專案的 *.cscfg* 檔案中。*ServiceConfiguration.Local.cscfg* 檔案會決定當您在 Visual Studio 本機執行應用程式時使用的項目，而 *ServiceConfiguration.Cloud.cscfg* 檔案則會決定當您將應用程式部署至雲端時使用的項目。稍後您將看到如何設定應用程式來使用 Azure 儲存體帳戶。

14. 關閉瀏覽器。

## <a name="StorageExpVS"></a>檢視 Visual Studio 中的開發儲存體

[伺服器總管]** 中的 [Azure 儲存體] 瀏覽器，可方便您直接使用 Azure 儲存體資源。

1.  從 Visual Studio 中的 [檢視] 功能表，選擇 [伺服器總管]。

2.  依序展開 [Azure] 節點、[儲存體] 節點，和 [Azure 儲存體] 節點下的 [(開發)] 節點。

    如果您尚未在 Visual Studio 中登入 Azure，系統會提示您提供 Azure 認證。針對具有訂閱、且要用來執行雲端服務的帳戶，輸入認證。

3.  展開 [資料表]，以查看您在先前步驟中建立的資料表。

    ![Server Explorer][]

4.  按兩下 [郵寄清單] 資料表。

    ![VS storage explorer][]

    請注意視窗如何顯示資料表中的不同結構描述。`MailingList` 實體具有`Description` and`FromEmailAddress` 屬性，而`Subscriber` 實體具有`Verified` 屬性 (和`SubscriberGUID` 因為影像不夠寬而未顯示)。在資料表中，所有屬性都有對應的資料欄，而且如果特定的資料表列是用於沒有特定屬性的實體，則該資料格會空白。

另一項可用來使用 Azure 儲存體資源的工具是 [Azure 儲存體總管][] (英文)。

## <a name="createWASA"></a>建立 Azure 儲存體帳戶

在 Visual Studio 中執行範例應用程式時，您可以在雲端中存取 Azure 儲存體模擬器或 Azure 儲存體帳戶中的資料表、佇列和 Blob。在教學課程的這一節當中，您會建立 Azure 儲存體帳戶，並設定 Visual Studio，以在稍後的教學課程中使用。

1.  在瀏覽器中開啟 [Azure 管理入口網站][]。

2.  在 [Azure 管理入口網站][]中，按一下 [儲存體]，然後按一下 [新增]。

    ![New Storage][]

1.  按一下 [快速建立]。

    ![Quick Create][]

1.  在 URL 輸入方塊中，輸入 URL 前置詞。

    此前置詞加上您在方塊下面看到的文字，就是儲存體帳戶的唯一 URL。如果您輸入的前置詞已被他人使用，則您將在文字方塊上面看到「此儲存體名稱已在使用」，而必須選擇不同的前置詞。

2.  將區域設為您要部署應用程式的區域。

3.  將 [複寫] 下拉式方塊設為 [Locally redundant]。

    對儲存體帳戶啟用地理區域複寫時，儲存內容會複寫至次要位置，以便能在主要位置發生嚴重災難時容錯移轉至該位置。地理區域複寫會引發額外成本。對於測試和開發帳戶，您通常不會想要付費使用地理區域複寫功能。如需詳細資訊，請參閱[如何管理儲存體帳戶][]。

4.  按一下 [建立儲存體帳戶]。

    在下面的影像中，已建立儲存體帳戶，其 URL 為 `aestest3.core.windows.net`.

    ![create storage with URL prefix][]

    此步驟可能需要幾分鐘時間才能完成。等待時，您可以重複這些步驟，並建立實際執行儲存體帳戶。具有一個用於本機開發的測試儲存體帳戶、另一個用於 Azure 測試的測試儲存體帳戶，以及一個實際執行儲存體帳戶，通常可為你帶來便利。

5.  按一下您在前一個步驟中建立的測試帳戶，然後按一下 [Manage Access Keys] 圖示。

    ![Manage Keys][]

    ![Keys GUID][]

    Visual Studio 會在您選取儲存體帳戶時自動以其中一個金鑰設定連接字串。您也可以手動更新連接字串。

    金鑰有兩個，這樣您才能定期變更使用的金鑰，而不會中斷對即時應用程式的服務。您可以重新產生未使用的金鑰，然後在應用程式中變更連接字串，使用重新產生的金鑰。如果金鑰只有一個，則在重新產生金鑰時，應用程式將會失去與儲存體帳戶的連接。影像中顯示的金鑰已不再有效，因為在擷取影像後已予以重新產生。

## <a name="createcloudsvc"></a><span class="short-header">建立雲端服務</span>建立雲端服務

1.  在 [Azure 管理入口網站][]中按一下 [雲端服務]，然後按一下 [新增] 圖示。

    ![Quick Cloud][]

2.  按一下 [快速建立]。

3.  在 URL 輸入方塊中，輸入 URL 前置詞。

    如同儲存體 URL，此 URL 必須是唯一的，而且如果他人已使用您選擇的前置詞，您會收到錯誤訊息。

4.  將區域設為您要部署應用程式的區域。

    您應該在先前建立儲存體帳戶的相同區域中建立雲端服務。當雲端服務與儲存體帳戶位於不同的資料中心 (不同的區域) 時，延遲時間會增加，而且您將由於使用資料中心外的頻寬而付費。資料中心內的頻寬則是免費的。

    Azure 同質群組提供一種機制，可將資料中心內的資源之間的距離縮至最短，因而可以減少延遲時間。本教學課程不會使用同質群組。如需詳細資訊，請參閱[如何在 Azure 中建立同質群組][] (英文)。

5.  按一下 [建立雲端服務]。

    在下面的影像中，已建立 URL 為 aescloud.cloudapp.net 的雲端服務。

    ![create storage with URL prefix][2]

## <a name="conf4azureStorage"></a><span class="short-header">使用您的儲存體帳戶</span>設定應用程式來使用您的 Azure 儲存體帳戶

接著，您將看到如何設定應用程式，以便它能夠在 Visual Studio 中執行時使用您的 Azure 儲存體帳戶，而不是使用開發儲存體。

1.  在 [方案總管] 中，於 **AzureEmailService** 專案的 [角色] 下的 **MvcWebRole** 上按一下滑鼠右鍵，然後按一下 [屬性]。

    ![Right Click Properties][]

2.  在 [MvcWebRole [角色]] 視窗中，按一下 [設定] 索引標籤。

3.  在 [服務組態] 下拉式方塊中，選取 [本機]。

4.  選取 **StorageConnectionString** 項目，該行右端就會出現省略符號 (**...**) 按鈕。按一下省略符號按鈕，開啟 [儲存體帳戶連接字串] 對話方塊。

    ![Right Click Properties][3]

5.  在 [Create Storage Connection String] 對話方塊中，按一下 [Your subscription]，然後選擇 [訂閱] 和您的儲存體 [帳戶名稱]。

    ![Right Click Properties][4]

6.  遵循您用於`StorageConnectionString` 連接字串的相同程序，來設定`Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString` 連接字串。

7.  遵循您對 MvcWebRole 角色的兩個連接字串所使用的相同程序，來設定 WorkerRoleA 角色和 workerRoleB 角色的連接字串。

8.  開啟位於 **AzureEmailService** 專案的 **ServiceConfiguration.Local.cscfg** 檔案。

    在`Role` 元素 (屬於`MvcWebRole` ) 中，您會看見`ConfigurationSettings` 元素，且其設定已使用 Visual Studio UI 進行更新。

          <Role name="MvcWebRole">
            <Instances count="1" />
            <ConfigurationSettings>
              <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=[name];AccountKey=[Key]" />
              <Setting name="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=aestest;AccountKey=[Key]" />
            </ConfigurationSettings>
          </Role>

    在`Role` 元素 (屬於兩個背景工作角色) 中，您會看到相同的兩個連接字串。

    您可以直接編輯這些檔案，而不要使用 Visual Studio [角色] 視窗。如需組態檔的詳細資訊，請參閱[設定 Azure 專案][]。

### 測試已設定為使用儲存體帳戶的應用程式

1.  按 CTRL+F5 執行應用程式。輸入一些資料，方法為按一下 [郵寄清單]、[訂閱者] 和 [訊息] 連結，如同您先前在本教學課程中所做一般。

2.  在 Visual Studio 中，開啟 [伺服器總管]。

3.  展開 [Azure] 節點下的 [儲存體] 節點，然後展開應用程式依設定要使用之儲存體帳戶的節點。

4.  展開 [資料表]，然後按兩下`MailingList` 資料表，以查看您在應用程式的 [郵寄清單] 和 [訂閱者] 頁面上輸入的資料。

### 停用 Azure 儲存體模擬器自動啟動的選用步驟

如果您不是使用儲存體模擬器，則可以停用 Azure 儲存體模擬器的自動啟動，來減少專案啟動時間，並減少使用的本機資源。

1.  在 [方案總管] 的 **AzureEmailService** 雲端專案上按一下滑鼠右鍵，然後選取 [屬性]。

    ![Selecting cloud project properties][]

2.  選取 [開發] 索引標籤。

3.  將 [Start Azure storage emulator] 設為 **False**。

    ![停用儲存體模擬器自動啟動][]

    **注意**：只有在不是使用儲存體模擬器時，才可將此屬性設為 false。

    此視窗也提供一種方法，將您在本機執行應用程式時使用的 [服務組態] 檔案，從 [本機] 變更為 \[雲端\] (從 *ServiceConfiguration.Local.cscfg* 變更為 *ServiceConfiguration.Cloud.cscfg*)。

4.  在 Windows 系統匣的運算模擬器圖示上按一下滑鼠右鍵，然後按一下 [Shutdown Storage Emulator]。

    ![ASE][]

## <a name="sendGrid"></a><span class="short-header">SendGrid</span>將應用程式設為使用 SendGrid

範例應用程式會使用 SendGrid 來傳送電子郵件。為了能使用 SendGrid 來傳送電子郵件，您必須設定 SendGrid 帳戶，然後必須利用 SendGrid 認證來更新組態檔。

<div class="note"><p><strong>注意：</strong>如果不想使用 SendGrid，或無法使用 SendGrid，您可以輕易地替換為自己的電子郵件服務。在背景工作角色 B 的兩種方法中，使用 SendGrid 的程式碼都有區隔開來。[教學課程 5][tut5] 會說明您必須變更什麼，才能實作不同的電子郵件傳送方法。如果您想這麼做，則可跳過此程序，並繼續本教學課程的後續內容；應用程式除了不能實際傳送電子郵件外，其他一切功能都將正常運作 (網頁、電子郵件排程等)。</p></div>

### 建立 SendGrid 帳戶

1.  遵循[如何在 Azure 使用 SendGrid 來傳送電子郵件][] (英文) 中的指示，來註冊免費帳戶。

### 更新背景工作角色屬性中的 SendGrid 認證

稍早當您在教學課程中設定 Web 角色及兩個背景工作角色的儲存體帳戶認證時，您可能已經注意到，背景工作角色 B 具有三個 Web 角色或背景工作角色 A 所沒有的設定。現在您可以使用該相同 UI，設定那三個設定 (在 [服務組態] 下拉式清單中選取 [雲端])。

下列步驟顯示替代的屬性設定方法，那就是透過編輯組態檔。

1.  編輯 *ServiceConfiguration.Cloud.cscfg* 檔案 (位於`AzureEmailService` 專案中)，然後將您在上一個步驟中取得的 SendGrid 使用者名稱和密碼值，輸入具有這些設定的`WorkerRoleB` 元素中。下列程式碼顯示 WorkerRoleB 元素。

    ![SendGridSettings][]

2.  還有一個 AzureMailServiceURL 設定。將此值設為您在建立 Azure 雲端服務時選取的 URL，例如："<http://aescloud.cloudapp.net>"。

您可以更新雲端組態檔，藉此來設定應用程式在雲端執行時將使用的設定。如果想要應用程式在本機執行時傳送電子郵件，則您也須更新 *ServiceConfiguration.Local.cscfg* 檔案。

## <a name="deployAz"></a><span class="short-header">部署至 Azure</span>將應用程式部署至 Azure

若要部署應用程式，您可以在 Visual Studio 建立一個套件，然後使用 Azure 管理入口網站來上傳它，或可從 Visual Studio 直接發佈。在本教學課程中，您將使用發佈方法。

首先您將發佈應用程式至預備環境，之後再將預備部署提升至實際執行。

### 實作 IP 限制

當部署至預備環境時，任何知道此 URL 的使用者都可以公開存取此應用程式。因此，第一步要實作 IP 限制，以確定未獲授權的人員不得使用它。在實際執行應用程式中，您將實作「ASP.NET 身分識別」之類的驗證與授權機制，但範例應用程式略過了這些功能，以確保設定、部署和測試易於執行。

1.  開啟 *Web.Release.config* 檔案 (位於`MvcWebRole` 專案的根資料夾中)，然後將 **ipAddress** 屬性值 127.0.0.1 取代為您的 IP 位址。(若要在 [方案總管] 中查看 **Web.Release.config** 檔案，您必須展開 *Web.config* 檔案。)

    您可以利用 [Bing][] 或其他搜尋引擎搜尋 "Find my IP"，來尋找您的 IP 位址。

    當應用程式發佈時，即會套用 *Web.release.config* 檔案中指定的轉換，並在部署至雲端的 *web.config* 檔案中更新 IP 限制元素。在建立套件之後，您可以在 *AzureEmailService\\MvcWebRole\\obj\\Release\\TransformWebConfig\\transformed* 資料夾中檢視已轉換的 *web.config* 檔案。

### 將應用程式設為在雲端執行時使用儲存體帳戶

稍早當您在教學課程中設定 Web 角色及兩個背景工作角色的儲存體帳戶認證時，您將認證設為在本機執行應用程式時使用。現在您需要將儲存體帳戶認證設為在雲端執行應用程式時使用。

在這個測試回合中，您將對用於本機執行的雲端使用相同認證。如果您是部署實際執行應用程式，則用於實際執行的帳戶通常會不同於用於測試的帳戶。實際執行還有一個最佳作法，那就是對診斷連接字串和儲存體連接字串使用不同的帳戶，但是在這個測試回合中，您會使用相同帳戶。

您可以使用相同 UI，來設定連接字串 (只需確定您在 [服務組態] 下拉式清單中選取 [雲端])。或者，您可以依下列步驟所述編輯組態檔。

1.  開啟 **AzureEmailService** 專案中的 *ServiceConfiguration.Local.cscfg* 檔案，然後複製`Setting` 元素 (屬於`StorageConnectionString` and `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString`.

2.  開啟 **AzureEmailService** 專案中的 *ServiceConfiguration.Cloud.cscfg* 檔案，然後將複製的元素貼到`Configuration Settings` 元素 (屬於`MvcWebRole`, `WorkerRoleA`和`WorkerRoleB`) 中，以取代原本的`Setting` 元素。

3.  確認 Web 角色和兩個背景工作角色元素全都定義相同的連接字串。

### 發行應用程式

1.  啟動 Visual Studio，並開啟 **AzureEmailService** 方案 (如果尚未開啟)。

2.  在 **AzureEmailService** 雲端專案上按一下滑鼠右鍵，然後選取 [發佈]。

    ![Package][]

    [Publish Azure Application] 對話方塊隨即出現。

    ![Cloud Package][]

3.  如果先前已使用自動方法來匯入儲存體帳戶認證，則下拉式清單中會有 Azure 訂閱可供您加以選取，然後按 **[下一步]**。否則，按一下 **[Sign in to download credentials]**，並遵循[設定 Azure 儲存體的應用程式][]中的指示，下載並匯入您的發佈設定。

4.  在 [一般設定] 索引標籤中，驗證您的雲端服務在 [雲端服務] 下拉式清單中已被選取。

5.  在 [環境] 下拉式清單中，將 [實際執行] 變更為 [預備]。

    ![儀表板][]

6.  保留 [組建組態] 的預設 [發行] 設定，以及 [服務組態] 的 [雲端] 設定。

    [進階] 索引標籤中的預設設定適用於本教學課程。[進階] 索引標籤上有幾個對開發和測試有用的設定。如需進階索引標籤的詳細資訊，請參閱[發佈 Windows Azure 應用程式精靈][]。

7.  按 [下一步]。

8.  在精靈的 [摘要] 步驟中，按一下 [儲存] 圖示 (顯示在 [目標設定檔] 下拉式清單右邊的磁片圖示) 來儲存發佈設定。

    下次發佈應用程式時就會使用儲存的設定，不必再重新執行發佈精靈。

9.  檢閱設定，然後按一下 [發佈]。

    ![pub][]

    [Azure 活動記錄檔] 視窗即會在 Visual Studio 開啟。

1.  按一下向右箭頭圖示以展開部署詳細資料。

    ![pub][5]
	<br/><br/>
    ![pub][6]

    完成部署可能需要大約 5 分鐘或更久的時間。

2.  當部署狀態為完成時，按一下 [網站 URL] 來啟動應用程式。

    ![儀表板][7]

3.  在 [郵寄清單]、[訂閱者] 和 [訊息] 網頁中輸入一些資料來測試應用程式。

    **注意**：應用程式測試完成後，請予以刪除，以免需要支付您未使用的資源。如果您是使用 [Azure 免費試用帳戶][]，已部署的三個角色會在數週內用光您的每月限制。若要使用 Azure 管理入口網站刪除部署，請選取雲端服務，並按一下頁面底部的 [刪除]，然後選取實際執行或預備部署。

    ![pub][8]

4.  在 Visual studio 的 [Azure 活動記錄檔] 中，選取 [在伺服器總管中開啟]。

    在 [伺服器總管] 的 [Azure 運算] 下，您可以監視部署。如果已在 [Publish Azure Application] 精靈中選取 [Enable Remote Desktop for all roles]，則可在角色執行個體上按一下滑鼠右鍵，然後選取 [Connect using Remote Desktop]。

    ![pub][9]

## <a name="swap"></a>將應用程式從預備提升至實際執行

1.  在 [Azure 管理入口網站][]中，按一下左窗格中的 [雲端服務] 圖示，然後選取您的雲端服務，再按一下 \[儀表板\] 索引標籤。

2.  按一下 [交換]。

3.  按一下 [是] 以完成 VIP (虛擬 IP) 交換。此步驟可能需要幾分鐘時間才能完成。

    ![儀表板][10]

4.  當交換完成時，請將 [實際執行] 部署的 \[儀表板\] 索引標籤向下捲動至頁面右下方的 [quick glance] 區段。請注意，[網站 URL] 已從 GUID 前置詞變更為您的雲端服務名稱。

    ![儀表板][11]

5.  按一下 [網站 URL] 下的連結，或將它複製並貼到瀏覽器，以在實際執行環境中測試應用程式。

    如果還沒變更儲存體帳戶設定，則當您在雲端執行應用程式時，即會顯示測試應用程式的預備版本時所輸入的資料。

## <a name="trace"></a>設定和檢視追蹤資料

追蹤工具非常適合用於偵錯雲端應用程式。在教學課程的這一節當中，您將看到如何檢視追蹤資料。

1.  確認診斷連接字串已設為使用 Azure 儲存體帳戶，而非開發儲存體。

    如果您已遵循教學課程中的先前指示，它們將會相同。您可以使用 Visual Studio UI (角色的 [屬性] 中的 [設定] 索引標籤)，或查看 <i>ServiceConfiguration.\*.cscfg</i> 檔案，以確認它們是否相同。

     **注意：**最佳作法是對追蹤資料和實際執行資料使用不同的儲存體帳戶，但在本教學課程中為求簡化，您已對追蹤工具設定相同帳戶。

1.  在 Visual Studio 中，開啟 **WorkerRoleA** 專案中的 *WorkerRoleA.cs*、搜尋`ConfigureDiagnostics`，然後檢查`ConfigureDiagnostics` 方法。

        private void ConfigureDiagnostics()
        {
            DiagnosticMonitorConfiguration config = DiagnosticMonitor.GetDefaultInitialConfiguration();
            config.ConfigurationChangePollInterval = TimeSpan.FromMinutes(1d);
            config.Logs.BufferQuotaInMB = 500;
            config.Logs.ScheduledTransferLogLevelFilter = LogLevel.Verbose;
            config.Logs.ScheduledTransferPeriod = TimeSpan.FromMinutes(1d);

            DiagnosticMonitor.Start(
                "Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString",
                config);
        }

    在此程式碼中，`DiagnosticMonitor` 設定為最多儲存 500 MB 的追蹤資訊 (超過 500 MB 後會覆寫存在最久的資料)，並設為儲存所有追蹤訊息 (LogLevel.Verbose)。 `ScheduledTransferPeriod` 會每分鐘將追蹤資料傳送至儲存體。您必須設定`ScheduledTransferPeriod` 以儲存追蹤資料。

    每一個背景工作和 Web 角色中的`ConfigureDiagnostics` 方法，會將追蹤接聽程式設定為在您呼叫追蹤 API 時記錄資料。如需詳細資訊，請參閱[在 Azure 雲端應用程式中使用追蹤][] (英文)。

2.  在 **[伺服器總管]** 中，按兩下 **WADLogsTable** (展開 **[Azure]** / **[儲存體]** / **[yourstorageaccountname]** / **[資料表]**)，以取得先前新增的儲存體帳戶。您可以輸入[為資料表設計工具建構篩選條件字串][]，以限制顯示的實體。下列影像只顯示警告和錯誤訊息。

    ![儀表板][12]

## <a name="addRole"></a>新增另一個背景工作角色執行個體來處理增加的負載

有兩種方法可擴充 Azure 角色中的運算資源，那就是指定[設定雲端服務的大小][] 和 (或) 指定執行中虛擬機器的執行個體計數。

虛擬機器 (VM) 大小指定於`vmsize` 屬性中 (屬於`WebRole` 或`WorkerRole` 元素，位於 *ServiceDefinition.csdef* 檔案中)。預設設定為`Small` 此設定會提供一個核心和 1.75 GB 的 RAM。對於使用大量記憶體、磁碟和頻寬的多執行緒應用程式，您可以增加虛擬機器大小以提高效能。例如，`ExtraLarge` 虛擬機器有 8 個 CPU 核心和 14 GB 的 RAM。對單一電腦增加記憶體、CPU 核心、磁碟和頻寬即為「擴充 (硬體資源)」。適合進行擴充的候選對象包括使用[非同步方法][] (英文)的 ASP.NET Web 應用程式。如需每一個虛擬機器大小所提供資源的說明，請參閱[設定雲端服務的大小][]。

此應用程式中的背景工作角色 B 為高負載下的限制元件，因為它會執行傳送電子郵件的工作。(背景工作角色 A 只建立佇列訊息，不會大量耗用資源。)因為背景工作角色 B 不是多執行緒，而且沒有大量記憶體，因此不適合進行擴充。背景工作角色 B 可藉由增加執行個體計數，以線性方式進行擴充 (亦即，執行個體加倍時，效能也幾乎加倍)。增加運算執行個體數目即為「擴充 (機器數量)」。每一個執行個體都需要成本，因此請在應用程式有需要時再擴充。

您可以更新 Visual Studio UI 中的設定，或直接編輯 *ServiceConfiguration.\*.cscfg* 檔案，來擴充 Web 角色或背景工作角色。執行個體計數指定於角色 \[屬性\] 視窗的 \[組態\] 索引標籤中，以及`Instances` 元素中 (位於 *.cscfg* 檔案中)。更新設定時必須部署已更新的組態檔，變更才能生效。或者，對於短暫增加的負載，您可以手動變更角色執行個體數目，或設定 Azure 以根據您指定的準則自動變更執行個體數目。如需自動調整的詳細資訊，請參閱[本系列的最後一個教學課程][]。

在教學課程的這一節當中，您將使用管理入口網站擴充背景工作角色 B，但是您會先看到如何在 Visual Studio 執行此作業。

若要在 Visual Studio 執行此作業，您需要在雲端專案的 [角色] 下，對某個角色按一下滑鼠右鍵，並選取 [屬性]。

![Right Click Properties][]

然後，您需要選取左邊的 [組態] 索引標籤，並在 [服務組態] 下拉式清單中選取 [雲端]。

![執行個體計數][]

請注意，您也可以在此索引標籤中設定虛擬機器大小。

下列步驟說明如何使用 Azure 管理入口網站來進行擴充。

1.  在 Azure 管理入口網站中，選取您的雲端服務，然後按一下 [擴充]。

2.  增加背景工作角色 B 的執行個體數目，然後按一下 [儲存]。

    ![增加執行個體][]

    佈建新的虛擬機器可能需要數分鐘的時間。

3.  選取 [執行個體] 索引標籤，以在應用程式中查看每一個角色執行個體。

    ![view instances][]

## <a name="nextsteps"></a>後續步驟

現在您已看到如何設定、部署及擴充已完成的應用程式。後續的教學課程會示範如何從頭組建應用程式。在[下一個教學課程][]中，您將組建 Web 角色。

如需使用 Azure 儲存體資料表、佇列和 Blob 的其他資源連結，請參閱[本系列的最後一個教學課程][13]。

<div><a href="/en-us/develop/net/tutorials/multi-tier-web-site/3-web-role/" class="site-arrowboxcta download-cta">教學課程 3</a></div>

  [系列的第一個教學課程]: /en-us/develop/net/tutorials/multi-tier-web-site/1-overview/
  [設定開發環境]: #setupdevenv
  [下載並執行已完成的方案]: #downloadcnfg
  [建立 Azure 儲存體帳戶]: #createWASA
  [建立雲端服務]: #createcloudsvc
  [設定 Azure 儲存體的應用程式]: #conf4azureStorage
  [將應用程式設為使用 SendGrid]: #sendGrid
  [將應用程式部署至 Azure]: #deployAz
  [將應用程式從預備提升至實際執行]: #swap
  [設定和檢視追蹤資料]: #trace
  [新增另一個背景工作角色執行個體來處理增加的負載]: #addRole
  [install-sdk-2013-only]: ../includes/install-sdk-2013-only.md
  [已完成的方案]: http://code.msdn.microsoft.com/Windows-Azure-Multi-Tier-eadceb36
  [Run the App.]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-mailinglist1.png
  [1]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-create1.png
  [Mailing List Index Page]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-mailing-list-index-page.png
  [Subscriber Index Page]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-subscribers-index-page.png
  [訊息建立頁面]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-message-create-page.png
  [訊息索引頁面]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-message-index-page.png
  [Server Explorer]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-serverExplorer.png
  [VS storage explorer]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-wasVSdata.png
  [Azure 儲存體總管]: http://azurestorageexplorer.codeplex.com/
  [Azure 管理入口網站]: http://manage.windowsazure.com
  [New Storage]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-portal-new-storage.png
  [Quick Create]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-storage-quick.png
  [如何管理儲存體帳戶]: /en-us/manage/services/storage/how-to-manage-a-storage-account/
  [create storage with URL prefix]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-create-storage-url-test.png
  [Manage Keys]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-manage-keys.png
  [Keys GUID]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-guid-keys.PNG
  [Quick Cloud]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-new-cloud.png
  [如何在 Azure 中建立同質群組]: http://msdn.microsoft.com/en-us/library/jj156209.aspx
  [2]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-create-cloud.png
  [Right Click Properties]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-rt-prop.png
  [3]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-elip.png
  [4]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-enter.png
  [設定 Azure 專案]: http://msdn.microsoft.com/en-us/library/windowsazure/ee405486.aspx
  [Selecting cloud project properties]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-aesp.png
  [停用儲存體模擬器自動啟動]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-1.png
  [ASE]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-se4.png
  [如何在 Azure 使用 SendGrid 來傳送電子郵件]: http://www.windowsazure.com/en-us/develop/net/how-to-guides/sendgrid-email-service/ "SendGrid"
  [SendGridSettings]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-sg.png
  [Bing]: http://www.bing.com/search?q=find+my+IP&qs=n&form=QBLH&pq=find+my+ip&sc=8-10&sp=-1&sk= "find my IP"
  [Package]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-6.png
  [Cloud Package]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-16.png
  [儀表板]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-7.png
  [發佈 Windows Azure 應用程式精靈]: http://msdn.microsoft.com/library/windowsazure/hh535756.aspx "pub wiz"
  [pub]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-8.png
  [5]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-11.png
  [6]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-9.png
  [7]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-c55.png
  [Azure 免費試用帳戶]: http://www.windowsazure.com/en-us/pricing/free-trial/ "免費試用帳戶"
  [8]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-19.png
  [9]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-12.png
  [10]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-c6.png
  [11]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-c7.png
  [在 Azure 雲端應用程式中使用追蹤]: http://blogs.msdn.com/b/windowsazure/archive/2012/10/24/using-trace-in-windows-azure-cloud-applications-1.aspx "在 Azure 中使用追蹤"
  [為資料表設計工具建構篩選條件字串]: http://msdn.microsoft.com/en-us/library/windowsazure/ff683669.aspx "WCF 篩選條件"
  [12]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-trc.png
  [設定雲端服務的大小]: http://msdn.microsoft.com/en-us/library/windowsazure/ee814754.aspx "虛擬機器大小"
  [非同步方法]: http://www.asp.net/mvc/tutorials/mvc-4/using-asynchronous-methods-in-aspnet-mvc-4 "非同步 MVC"
  [本系列的最後一個教學課程]: /en-us/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/
  [執行個體計數]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-instanceCnt.png
  [增加執行個體]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-in3.png
  [view instances]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-in2.png
  [下一個教學課程]: /en-us/develop/net/tutorials/multi-tier-web-site/3-web-role/
  [13]: /en-us/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/#nextsteps
