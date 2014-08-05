<properties linkid="develop-net-tutorials-multi-tier-web-site-2-download-and-run" urlDisplayName="Step 2: Download and Run" pageTitle="ASP.NET Multi-tier Web Application with Azure - Step 2: Download and run" metaKeywords="Azure tutorial, deploying email service app, publishing email service" description="The second tutorial in a series that teaches how to configure your computer for Azure development and deploy the Email Service app." metaCanonical="" services="cloud-services,storage" documentationCenter=".NET" title="Configuring and Deploying the Azure Email Service application - 2 of 5" authors="riande,tdykstra" solutions="" manager="wpickett" editor="mollybos" />

設定和部署 Azure 電子郵件服務應用程式 - 第 2 課 (共 5 課)
=========================================================

這是由五個教學課程組成的系列中的第二個教學課程，示範如何建置和部署 Azure 電子郵件服務範例應用程式。如需本應用程式和教學課程系列的詳細資訊，請參閱[本系列的第一個教學課程](/en-us/develop/net/tutorials/multi-tier-web-site/1-overview/)。

本教學課程示範如何設定電腦進行 Azure 開發，以及如何使用下列任一產品，將 Azure 電子郵件服務應用程式部署至 Windows Azure 雲端服務：

-   Visual Studio 2012
-   Visual Studio 2012 Express for Web
-   Visual Studio 2010
-   Visual Web Developer Express 2010

> [WACOM.NOTE] Visual Studio 2013 是在本教學課程撰寫完畢後才發行，而 Azure 管理入口網站和 SDK 已予以更新。如果您是使用 Visual Studio 2013 和最新 SDK，我們已在您必須以不同方式執行作業的地方新增像這樣的注意事項。注意事項是在 2014 年 3 月撰寫的，且經過修訂的程序已利用 SDK 2.3 版測試過。

您可以免費申請 Azure 帳戶，而且如果您還沒有 Visual Studio 2013，SDK 會自動安裝 Visual Studio 2013 for Web Express。如此您就能開始免費進行 Azure 相關開發。

在本教學課程中，您將了解：

-   如何安裝 Azure SDK 來設定電腦進行 Azure 開發。
-   如何在本機電腦上設定和測試 Azure 電子郵件服務應用程式。
-   如何將應用程式發佈至 Azure。
-   如何使用 Visual Studio 或 Azure 儲存體總管，來檢視並編輯 Azure 資料表、佇列和 Blob。
-   如何設定追蹤和檢視追蹤資料。
-   如何增加背景工作角色執行個體的數目來調整應用程式。

**注意**

若要完成此教學課程，您需要 Azure 帳戶。您可以建立[免費試用帳戶](/en-us/pricing/free-trial/)或[啟用 MSDN 訂戶權益](/en-us/pricing/member-offers/msdn-benefits/)。

### 教學課程區段

-   [設定開發環境](#setupdevenv)
-   [設定免費 Azure 帳戶](#setupwindowsazure)
-   [建立 Azure 儲存體帳戶](#createWASA)
-   [安裝 Azure 儲存體總管](#installASE)
-   [建立雲端服務](#createcloudsvc)
-   [下載並執行已完成的方案](#downloadcnfg)
-   [檢視 Visual Studio 中的開發人員儲存體](#StorageExpVS)
-   [設定 Azure 儲存體的應用程式](#conf4azureStorage)
-   [將應用程式部署至 Azure](#deployAz)
-   [將應用程式從預備提升至實際執行](#swap)
-   [將應用程式設為使用 SendGrid](#sendGrid)
-   [設定和檢視追蹤資料](#trace)
-   [新增另一個背景工作角色執行個體來處理增加的負載](#addRole)

> [WACOM.NOTE] 在關於 SDK 安裝的下節中，如果您是使用 Visual Studio 2013，則正確連結為 <http://go.microsoft.com/fwlink/?LinkID=324322>。

[WACOM.INCLUDE [install-sdk-2012-only](../includes/install-sdk-2012-only.md)]

建立儲存體帳戶建立 Azure 儲存體帳戶
-----------------------------------

在 Visual Studio 執行範例應用程式時，您可以存取 Azure 開發儲存體中的資料表、佇列和 Blob，或雲端中的 Azure 儲存體帳戶。開發儲存體會使用 SQL Server Express LocalDB 資料庫，來模擬 Azure 儲存體在雲端的運作方式。在本教學課程中，您會從使用開發儲存體來開始著手，然後了解如何設定應用程式，讓它在 Visual Studio 執行時使用雲端儲存體帳戶。在教學課程的這一節當中，您會建立 Azure 儲存體帳戶，並設定 Visual Studio，以在稍後的教學課程中使用。

1.  在瀏覽器中開啟 [Azure 管理入口網站](http://manage.windowsazure.com)。

2.  在 [Azure 管理入口網站](http://manage.windowsazure.com)中，按一下 **[儲存體]**，然後按一下 **[新增]**。

    ![New Storage](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-portal-new-storage.png)

3.  按一下 **[快速建立]**。

    ![Quick Create](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-storage-quick.png)

4.  在 URL 輸入方塊中，輸入 URL 前置詞。

    此前置詞加上您在方塊下面看到的文字，就是儲存體帳戶的唯一 URL。如果您輸入的前置詞已被他人使用，則您將在文字方塊上面看到「此儲存體名稱已在使用」，而必須選擇不同的前置詞。

5.  將區域設為您要部署應用程式的區域。

6.  將 **[複寫]** 下拉式方塊設為 **[Locally redundant]**。

    對儲存體帳戶啟用地理區域複寫時，儲存內容會複寫至次要位置，以便能在主要位置發生嚴重災難時容錯移轉至該位置。地理區域複寫會引發額外成本。對於測試和開發帳戶，您通常不會想要付費使用地理區域複寫功能。如需詳細資訊，請參閱[如何管理儲存體帳戶](/en-us/manage/services/storage/how-to-manage-a-storage-account/)。

7.  按一下 **[建立儲存體帳戶]**。

    在下面的影像中，已建立 URL 為 `aestest3.core.windows.net` 的儲存體帳戶。

    ![create storage with URL prefix](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-create-storage-url-test.png)

    此步驟可能需要幾分鐘時間才能完成。等待時，您可以重複這些步驟，並建立實際執行儲存體帳戶。具有一個用於本機開發的測試儲存體帳戶、另一個用於 Azure 測試的測試儲存體帳戶，以及一個實際執行儲存體帳戶，通常可為你帶來便利。

8.  按一下您在前一個步驟中建立的測試帳戶，然後按一下 **[Manage Access Keys]** 圖示。

    ![Manage Keys](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-manage-keys.png)

    ![Keys GUID](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-guid-keys.PNG)

    在本教學課程中，您將需要 **[主要存取金鑰]** 或 **[次要存取金鑰]**。您可以在儲存體連接字串中使用上述任何一個金鑰。

    金鑰有兩個，這樣您才能定期變更使用的金鑰，而不會中斷對即時應用程式的服務。您可以重新產生未使用的金鑰，然後在應用程式中變更連接字串，使用重新產生的金鑰。如果金鑰只有一個，則在重新產生金鑰時，應用程式將會失去與儲存體帳戶的連接。影像中顯示的金鑰已不再有效，因為在擷取影像後已予以重新產生。

9.  將其中一個金鑰複製到剪貼簿，以用於下一節。

安裝 ASE安裝 Azure 儲存體總管
-----------------------------

**Azure 儲存體總管** (ASE) 是可用來查詢和更新 Azure 儲存體資料表、佇列和 Blob 的工具。您將會在這些教學課程中一路使用它，以確認資料是否有正確更新，並建立測試資料。

1.  安裝 [Azure 儲存體總管](http://azurestorageexplorer.codeplex.com/) (英文)。

    螢幕擷取畫面顯示 ASE 4，但是如果喜歡的話，您可以安裝 ASE 5。

2.  啟動 **[Azure 儲存體總管]**，然後按一下 **[新增帳戶]**。

    ![Add ASE Account](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-ase-add.png)

3.  輸入測試儲存體帳戶的名稱，並貼上您先前複製的金鑰。

4.  按一下 **[Add Storage Account]**。

    ![Add ASE Account](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-ase-add2.png)

可使用 Azure 儲存體的工具不只這項。如需詳細資訊，請參閱 [Azure 儲存體總管 (2014)](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx) (英文)。

建立雲端服務建立雲端服務
------------------------

1.  在瀏覽器中開啟 [Azure 管理入口網站](http://manage.windowsazure.com)。

2.  按一下 **[雲端服務]**，然後按一下 **[新增]** 圖示。

    ![Quick Cloud](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-new-cloud.png)

3.  按一下 **[快速建立]**。

4.  在 URL 輸入方塊中，輸入 URL 前置詞。

    如同儲存體 URL，此 URL 必須是唯一的，而且如果他人已使用您選擇的前置詞，您會收到錯誤訊息。

5.  將區域設為您要部署應用程式的區域。

    您應該在先前建立儲存體帳戶的相同區域中建立雲端服務。當雲端服務與儲存體帳戶位於不同的資料中心 (不同的區域) 時，延遲時間會增加，而且您將由於使用資料中心外的頻寬而付費。資料中心內的頻寬則是免費的。

    Azure 同質群組提供一種機制，可將資料中心內的資源之間的距離縮至最短，因而可以減少延遲時間。本教學課程不會使用同質群組。如需詳細資訊，請參閱[如何在 Azure 中建立同質群組](http://msdn.microsoft.com/en-us/library/jj156209.aspx) (英文)。

6.  按一下 **[建立雲端服務]**。

    在下面的影像中，已建立 URL 為 aescloud.cloudapp.net 的雲端服務。

    ![create storage with URL prefix](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-create-cloud.png)

    您可以移至下一個步驟，無需等待此步驟完成。

下載並執行下載並執行已完成的方案
--------------------------------

1.  下載並解壓縮[已完成的方案](http://code.msdn.microsoft.com/Windows-Azure-Multi-Tier-eadceb36) (英文)。

2.  以提高的權限啟動 Visual Studio。

    可讓 Visual Studio 在本機執行 Azure 專案的運算模擬器需要使用提高的權限。

    > [WACOM.NOTE] 使用 SDK 2.3 和更新版本時，預設 Azure 運算模擬器不需要使用提高的權限，但是此專案仍會設定為使用原始運算模擬器，因此仍需要使用提高的權限。

3.  還原 NuGet 套件。

    為了讓下載的方案內容不要太大，所提供的已完成方案將沒有已安裝的 NuGet 套件的組件或其他內容。當您開啟並建置方案時，NuGet 會自動取得所有套件內容。為了讓此作業能夠運作，您必須在 Visual Studio 啟用 NuGet 套件還原選項。如果尚未啟用 NuGet 套件還原選項，請執行下列步驟。

    > [WACOM.NOTE] 在 Visual Studio 2013 中，預設設定為啟用自動套件還原選項，但因為您是開啟 Visual Studio 2012 專案，所以還原作業不會自動進行。請稍候片刻，並於方案開啟後，依下列指示開啟 **[Manage NuGet Packages]** 對話方塊。您將在右上方看到 **[還原]** 按鈕：按一下該按鈕以還原套件。

         1. From the **Tools** menu, click **Library Package Manager**, and then click **Manage NuGet Packages for Solution**. 

        2. In the lower left corner of the **Manage NuGet Packages** dialog, click **Settings**.

        3. In the left pane of the **Options** dialog box, select **General** under **Package Manager**.

        4. Select **Allow NuGet to download missing packages during build**.

    ![Enabling NuGet package restore](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/NuGetPkgRestore.png)

4.  從 **[檔案]** 功能表中，選擇 **[開啟專案]**，導覽至方案的下載位置，然後開啟方案檔。

5.  在 **[方案總管]** 中，確定已選取 **AzureEmailService** 作為啟動專案。

6.  按 CTRL+F5 執行應用程式。

    > [WACOM.NOTE] 為了能夠建置方案，您將需要新增現行 SDK 組件的參考。在 [方案總管] 的 MvcWebRole 專案上按一下滑鼠右鍵，然後按一下 **[新增]** -- **[參考]**。在 **[組件]** 下按一下 **[擴充]**。選取 *Microsoft.WindowsAzure.Diagnostics* 和 *Microsoft.WindowsAzure.ServiceRuntime*，然後按一下 **[確定]**。對 WorkerRoleA 和 WorkerRoleB 專案執行相同的動作。

    應用程式首頁隨即出現在瀏覽器中。

    ![Run the App.](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-mailinglist1.png)

7.  按一下 **[新建]**。

8.  輸入部分測試資料，然後按一下 **[建立]**。

    ![Run the App.](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-create1.png)

9.  建立幾個郵寄清單項目。

    ![Mailing List Index Page](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-mailing-list-index-page.png)

10. 按一下 **[訂閱者]**，然後新增一些訂閱者。將 **[已驗證]** 設為 `true`。

    ![Subscriber Index Page](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-subscribers-index-page.png)

11. 準備新增訊息，方法是建立一個 *.txt* 檔案，包含您要傳送的電子郵件本文。然後建立一個 *.htm* 檔案，包含相同文字，但具有一些 HTML (例如，使訊息的其中一個字變為粗體或斜體)。您將在下一個步驟中使用這些檔案。

12. 按一下 **[訊息]**，然後新增一些訊息。選取您在前一個步驟中建立的檔案。請勿變更預設為未來一週的排程日期。等到您設定 SendGrid 後，應用程式才會傳送訊息。

    ![Message Create Page](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-message-create-page.png)
	<br/><br/>
     ![Message Index Page](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-message-index-page.png)

您輸入和檢視的資料將會儲存在 Azure 開發儲存體中。開發儲存體會使用 SQL Server Express LocalDB 資料庫，來模擬 Azure 儲存體在雲端的運作方式。應用程式使用開發儲存體的原因是，那是您在下載專案時設定要使用的儲存體。此設定儲存在 **AzureEmailService** 專案的 *.cscfg* 檔案中。*ServiceConfiguration.Local.cscfg* 檔案會決定當您在 Visual Studio 本機執行應用程式時使用的項目，而 *ServiceConfiguration.Cloud.cscfg* 檔案則會決定當您將應用程式部署至雲端時使用的項目。稍後您將看到如何設定應用程式，來使用您先前建立的 Azure 儲存體帳戶。

開發人員儲存體檢視 Visual Studio 中的開發人員儲存體
---------------------------------------------------

**[伺服器總管]** 中的 **Azure 儲存體**瀏覽器可提供便利的 Azure 儲存體資源唯讀檢視。

1.  從 Visual Studio 中的 **[檢視]** 功能表，選擇 **[伺服器總管]**。

2.  展開 **[Azure 儲存體]** 節點下的 **[(開發)]** 節點。

    > [WACOM.NOTE] 在目前的 SDK 中，展開 **[Azure]** / **[儲存體]** / **[(開發)]**。

3.  展開 **[資料表]**，以查看您在先前步驟中建立的資料表。

    ![Server Explorer](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-serverExplorer.png)

4.  按兩下 **[郵寄清單]** 資料表。

    ![VS storage explorer](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-wasVSdata.png)

    請注意視窗如何顯示資料表中的不同結構描述。`MailingList` 實體具有 `Description` 和 `FromEmailAddress` 屬性，而 `Subscriber` 實體具有 `Verified` 屬性 (以及 `SubscriberGUID` 屬性，影像寬度不夠所以未顯示此屬性)。在資料表中，所有屬性都有對應的資料欄，而且如果特定的資料表列是用於沒有特定屬性的實體，則該資料格會空白。

您無法使用 Visual Studio 中的儲存體瀏覽器，來更新或刪除 Azure 儲存體資源。您可以使用 [Azure 儲存體總管](http://azurestorageexplorer.codeplex.com/) (英文) 來更新或刪除開發儲存體資源。(若要設定 Azure 儲存體總管來使用開發儲存體，請按一下 **[Add Storage Account]** 對話方塊中的 **[Developer Storage]** 核取方塊。)

> [WACOM.NOTE] 在最新的 SDK 中，您可以更新 **[伺服器總管]** 中的開發儲存體。

使用您的儲存體帳戶設定應用程式來使用您的 Azure 儲存體帳戶
---------------------------------------------------------

接著，您將看到如何設定應用程式，以便它能夠在 Visual Studio 中執行時使用您的 Azure 儲存體帳戶，而不是使用開發儲存體。想在 Visual Studio 中進行此作業有新舊兩種作法，新作法是在 SDK 1.8 版中引進，舊作法則需要從 Azure 管理入口網站複製和貼上設定。下列步驟示範新作法，也就是配置儲存體帳戶設定。

1.  在 **[方案總管]** 中，於 **AzureEmailService** 專案的 **[角色]** 下的 **MvcWebRole** 上按一下滑鼠右鍵，然後按一下 **[屬性]**。

    ![Right Click Properties](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-rt-prop.png)

2.  按一下 **[設定]** 索引標籤。在 **[服務組態]** 下拉式方塊中，選取 **[本機]**。

3.  選取 **StorageConnectionString** 項目，該行右端就會出現省略符號 (**...**) 按鈕。按一下省略符號按鈕，開啟 **[儲存體帳戶連接字串]** 對話方塊。

    ![Right Click Properties](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-elip.png)

4.  在 **[Create Storage Connection String]** 對話方塊中，按一下 **[Your subscription]**，然後按一下 **[Download Publish Settings]**。

    > [WACOM.NOTE] 在最新的 SDK 中，此對話方塊可讓您登入 Azure，而且只選取您要使用的儲存體帳戶。

    ![Right Click Properties](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-enter.png)

    Visual Studio 會以 Azure 入口網站下載發佈設定頁面的 URL，啟動新的預設瀏覽器執行個體。如果您未登入入口網站，系統會提示您登入。一旦登入，您的瀏覽器就會提示您儲存發佈設定。請記下設定的儲存位置。

    ![publish settings](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-3.png)

5.  在 **[Create Storage Connection String]** 對話方塊中，按一下 **[匯入]**，然後瀏覽到前一個步驟中儲存的發佈設定檔案。

6.  選取想要使用的訂閱和儲存體帳戶，然後按一下 **[確定]**。

    ![select storage account](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-5.png)

7.  依照用於 `StorageConnectionString` 連接字串的相同程序，設定 `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString` 連接字串。

    您不需要重新下載發行設定檔案。當您按一下 `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString` 連接字串的省略符號時，會發現 **[Create Storage Connection String]** 對話方塊記得您的訂閱資訊。當您按一下 **[Your subscription]** 選項按鈕時，只需要選取先前選取的相同 **[訂閱]** 和 **[帳戶名稱]**，然後按一下 **[確定]**。

8.  遵循您對 MvcWebRole 角色的兩個連接字串所使用的相同程序，來設定 WorkerRoleA 角色和 workerRoleB 角色的連接字串。

### 儲存體帳戶認證的手動設定方法

下列程序示範如何手動設定儲存體帳戶設定。如果您已使用前一個程序中示範的自動方法，則可跳過此程序，您也可以閱讀此程序的內容，以了解自動方法在背後為您做了哪些工作。

1.  在瀏覽器中開啟 [Azure 管理入口網站](http://manage.windowsazure.com)。

2.  按一下 **[儲存體]** 索引標籤，再按一下您在前一個步驟中建立的測試帳戶，然後按一下 **[管理金鑰]** 圖示。

    ![Manage Keys](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-manage-keys.png)

    ![Keys GUID](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-guid-keys.PNG)

3.  複製主要或次要存取金鑰。

4.  在 **[方案總管]** 中，於 **AzureEmailService** 專案的 **[角色]** 下的 **MvcWebRole** 上按一下滑鼠右鍵，然後按一下 **[屬性]**。

    ![Right Click Properties](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-rt-prop.png)

5.  按一下 **[設定]** 索引標籤。在 **[服務組態]** 下拉式方塊中，選取 **[本機]**。

6.  選取 **StorageConnectionString** 項目，該行右端就會出現省略符號 (**...**) 按鈕。按一下省略符號按鈕，開啟 **[儲存體帳戶連接字串]** 對話方塊。

    ![Right Click Properties](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-elip.png)

7.  在 **[Create Storage Connection String]** 對話方塊中，選取 **[Manually entered credentials]** 選項按鈕。輸入儲存體帳戶名稱，以及您從入口網站複製的主要或次要存取金鑰。

8.  按一下 **[確定]**。

您可以使用相同程序，來設定背景工作角色的設定，也可以編輯組態檔，將 Web 角色設定傳播至背景工作角色。下列步驟說明如何編輯組態檔。(這仍是儲存體認證的手動設定方法的一部分，因此，如果已使用自動方法將設定傳播至背景工作角色，則不必進行此步驟。)

1.  開啟位於 **AzureEmailService** 專案的 **ServiceConfiguration.Local.cscfg** 檔案。

    在`MvcWebRole` 的 `Role` 元素中，您將看到 `ConfigurationSettings` 元素，此元素具有使用 Visual Studio UI 更新的設定。

           <Role name="MvcWebRole">
        <Instances count="1" />
        <ConfigurationSettings>
        <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=[name];AccountKey=[Key]" />
        <Setting name="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=aestest;AccountKey=[Key]" />
        </ConfigurationSettings>
        </Role>

    在兩個背景工作角色的 `Role` 元素中，您會看到相同的兩個連接字串。

2.  從 `WorkerRoleA` 和 `WorkerRoleB` 元素中刪除這兩個連接字串的 `Setting` 元素，然後從 `MvcWebRole` 元素中複製 `Setting` 元素，並在這些元素的位置貼上複製的內容。

如需組態檔的詳細資訊，請參閱[設定 Azure 專案](http://msdn.microsoft.com/en-us/library/windowsazure/ee405486.aspx)。

### 測試已設定為使用儲存體帳戶的應用程式

1.  按 CTRL+F5 執行應用程式。輸入一些資料，方法為按一下 **[郵寄清單]**、**[訂閱者]** 和 **[訊息]** 連結，如同您先前在本教學課程中所做一般。

現在您可以使用 **[Azure 儲存體總管]** 或 **[伺服器總管]** 來檢視應用程式在 Azure 資料表中輸入的資料。

### 使用 Azure 儲存體總管來檢視已輸入至儲存體帳戶的資料

1.  開啟 **[Azure 儲存體總管]**。

2.  選取您先前為其輸入認證的儲存體帳戶。

3.  在 **[儲存體類型]** 下，選取 **[資料表]**。

4.  選取 `MailingList` 資料表，然後按一下 **[查詢]**，以查看您在應用程式的 **[郵寄清單]** 和 **[訂閱者]** 頁面上輸入的資料。

	![ASE](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-ase1.png)

### 使用伺服器總管來檢視已輸入至儲存體帳戶的資料

1.  在 **[伺服器總管]** (或 **[資料庫總管]**) 中，於 **[Azure 儲存體]** 上按一下滑鼠右鍵，然後按一下 **[加入新的儲存體帳戶]**。

2.  遵循您先前使用的相同程序，設定儲存體帳戶認證。

3.  展開 **[Azure 儲存體]** 下的新節點，以檢視 Azure 儲存體帳戶中儲存的資料。

    ![ASE](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-se3.png)

### 停用 Azure 儲存體模擬器自動啟動的選用步驟

如果您不是使用儲存體模擬器，則可以停用 Azure 儲存體模擬器的自動啟動，來減少專案啟動時間，並減少使用的本機資源。

1.  在 **[方案總管]** 的 **AzureEmailService** 雲端專案上按一下滑鼠右鍵，然後選取 **[屬性]**。

    ![Selecting cloud project properties](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-aesp.png)

2.  選取 **[開發]** 索引標籤。

3.  將 **[Start Azure storage emulator]** 設為 **False**。

    ![停用儲存體模擬器自動啟動](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-1.png)

    **注意**：只有在不是使用儲存體模擬器時，才可將此屬性設為 false。

    此視窗也提供一種方法，將您在本機執行應用程式時使用的 **[服務組態]** 檔案，從 **[本機]** 變更為 **[雲端]** (從 *ServiceConfiguration.Local.cscfg* 變更為 *ServiceConfiguration.Cloud.cscfg*)。

4.  在 Windows 系統匣的運算模擬器圖示上按一下滑鼠右鍵，然後按一下 **[Shutdown Storage Emulator]**。

    ![ASE](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-se4.png)

SendGrid將應用程式設為使用 SendGrid
-----------------------------------

範例應用程式會使用 SendGrid 來傳送電子郵件。為了能使用 SendGrid 來傳送電子郵件，您必須設定 SendGrid 帳戶，然後必須利用 SendGrid 認證來更新組態檔。

**注意：**如果不想使用 SendGrid，或無法使用 SendGrid，您可以輕易地替換為自己的電子郵件服務。在背景工作角色 B 的兩種方法中，使用 SendGrid 的程式碼都有區隔開來。[教學課程 5][tut5] 會說明您必須變更什麼，才能實作不同的電子郵件傳送方法。如果您想這麼做，則可跳過此程序，並繼續本教學課程的後續內容；應用程式除了不能實際傳送電子郵件外，其他一切功能都將正常運作 (網頁、電子郵件排程等)。

### 建立 SendGrid 帳戶

1.  遵循[如何在 Azure 使用 SendGrid 來傳送電子郵件](http://www.windowsazure.com/en-us/develop/net/how-to-guides/sendgrid-email-service/ "SendGrid") (英文) 中的指示，來註冊免費帳戶。

### 更新背景工作角色屬性中的 SendGrid 認證

稍早當您在教學課程中設定 Web 角色及兩個背景工作角色的儲存體帳戶認證時，您可能已經注意到，背景工作角色 B 具有三個 Web 角色或背景工作角色 A 所沒有的設定。現在您可以使用該相同 UI，設定那三個設定 (在 **[服務組態]** 下拉式清單中選取 **[雲端]**)。

下列步驟顯示替代的屬性設定方法，那就是透過編輯組態檔。

1.  編輯 `AzureEmailService` 專案中的 *ServiceConfiguration.Cloud.cscfg* 檔案，並將您在前一個步驟取得的 SendGrid 使用者名稱和密碼值輸入至具有三個設定的 `WorkerRoleB` 元素。下列程式碼顯示 WorkerRoleB 元素。

    ![SendGridSettings](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-sg.png)

2.  還有一個 AzureMailServiceURL 設定。將此值設為您在建立 Azure 雲端服務時選取的 URL，例如："http://aescloud.cloudapp.net"。

您可以更新雲端組態檔，藉此來設定應用程式在雲端執行時將使用的設定。如果想要應用程式在本機執行時傳送電子郵件，則您也須更新 *ServiceConfiguration.Local.cscfg* 檔案。

部署至 Azure將應用程式部署至 Azure
----------------------------------

若要部署應用程式，您可以在 Visual Studio 建立一個套件，然後使用 Azure 管理入口網站來上傳它，或可從 Visual Studio 直接發佈。在本教學課程中，您將使用發佈方法。

首先您將發佈應用程式至預備環境，之後再將預備部署提升至實際執行。

### 實作 IP 限制

當部署至預備環境時，任何知道此 URL 的使用者都可以公開存取此應用程式。因此，第一步要實作 IP 限制，以確定未獲授權的人員不得使用它。在實際執行應用程式中，您將實作驗證與授權機制，如 ASP.NET 成員資格系統，但是範例應用程式已略過這些功能，以確保設定、部署和測試簡單易行。

1.  開啟位於 `MvcWebRole` 專案根資料夾的 *Web.Release.config* 檔案，並將 **ipAddress** 屬性值 127.0.0.1 取代為您的 IP 位址。(若要在 **[方案總管]** 中查看 **Web.Release.config** 檔案，您必須展開 *Web.config* 檔案。)

    您可以利用 [Bing](http://www.bing.com/search?q=find+my+IP&qs=n&form=QBLH&pq=find+my+ip&sc=8-10&sp=-1&sk= "find my IP") 或其他搜尋引擎搜尋 "Find my IP"，來尋找您的 IP 位址。

    當應用程式發佈時，即會套用 *Web.release.config* 檔案中指定的轉換，並在部署至雲端的 *web.config* 檔案中更新 IP 限制元素。在建立套件之後，您可以在 *AzureEmailService\\MvcWebRole\\obj\\Release\\TransformWebConfig\\transformed* 資料夾中檢視已轉換的 *web.config* 檔案。

### 將應用程式設為在雲端執行時使用儲存體帳戶

稍早當您在教學課程中設定 Web 角色及兩個背景工作角色的儲存體帳戶認證時，您將認證設為在本機執行應用程式時使用。現在您需要將儲存體帳戶認證設為在雲端執行應用程式時使用。

在這個測試回合中，您將對用於本機執行的雲端使用相同認證。如果您是部署實際執行應用程式，則用於實際執行的帳戶通常會不同於用於測試的帳戶。實際執行還有一個最佳作法，那就是對診斷連接字串和儲存體連接字串使用不同的帳戶，但是在這個測試回合中，您會使用相同帳戶。

您可以使用相同 UI，來設定連接字串 (只需確定您在 **[服務組態]** 下拉式清單中選取 **[雲端]**)。或者，您可以依下列步驟所述編輯組態檔。

1.  開啟 **AzureEmailService** 專案中的 *ServiceConfiguration.Local.cscfg* 檔案，並複製 `StorageConnectionString` 和 `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString` 的 `Setting` 元素。

2.  開啟 **AzureEmailService** 專案中的 *ServiceConfiguration.Cloud.cscfg* 檔案，並將複製的元素貼入 `MvcWebRole`、`WorkerRoleA` 和 `WorkerRoleB` 的 `Configuration Settings` 元素，取代原本在那的 `Setting` 元素。

3.  確認 Web 角色和兩個背景工作角色元素全都定義相同的連接字串。

### 發佈應用程式

1.  以系統管理員身分啟動 Visual Studio，並開啟 **AzureEmailService** 方案 (如果尚未開啟)。

2.  在 **AzureEmailService** 雲端專案上按一下滑鼠右鍵，然後選取 **[發佈]**。

    ![Package](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-6.png)

    **[Publish Azure Application]** 對話方塊隨即出現。

    ![Cloud Package](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-16.png)

3.  如果先前已使用自動方法來匯入儲存體帳戶認證，則下拉式清單中會有 Azure 訂閱可供您加以選取，然後按 **[下一步]**。否則，按一下 **[Sign in to download credentials]**，並遵循[設定 Azure 儲存體的應用程式](#conf4azureStorage)中的指示，下載並匯入您的發佈設定。

4.  在 **[一般設定]** 索引標籤中，驗證 **[雲端服務]** 下拉式清單中的設定。

5.  在 **[環境]** 下拉式清單中，將 **[實際執行]** 變更為 **[預備]**。

    ![Dashboard](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-7.png)

6.  保留 **[組建組態]** 的預設 **[發行]** 設定，以及 **[服務組態]** 的 **[雲端]** 設定。

    **[進階]** 索引標籤中的預設設定適用於本教學課程。**[進階]** 索引標籤上有幾個對開發和測試有用的設定。如需進階索引標籤的詳細資訊，請參閱[發佈 Windows Azure 應用程式精靈](http://msdn.microsoft.com/library/windowsazure/hh535756.aspx "pub wiz")。

7.  按 **[下一步]**。

8.  在精靈的 **[摘要]** 步驟中，按一下 **[儲存]** 圖示 (顯示在 [目標設定檔] 下拉式清單右邊的磁片圖示) 來儲存發佈設定。

    下次發佈應用程式時就會使用儲存的設定，不必再重新執行發佈精靈。

9.  檢閱設定，然後按一下 **[發佈]**。

    ![pub](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-8.png)

    **[Azure 活動記錄檔]** 視窗即會在 Visual Studio 開啟。

10. 按一下向右箭頭圖示以展開部署詳細資料。

    ![pub](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-11.png)
<br/><br/>
     ![pub](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-9.png)

    完成部署可能需要大約 5 分鐘或更久的時間。

11. 當部署狀態為完成時，按一下 **[網站 URL]** 來啟動應用程式。

    ![Dashboard](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-c55.png)

12. 在 **[郵寄清單]**、**[訂閱者]** 和 **[訊息]** 網頁中輸入一些資料來測試應用程式。

    **注意**：應用程式測試完成後，請予以刪除，以免需要支付您未使用的資源。如果您是使用 [Azure 免費試用帳戶](http://www.windowsazure.com/en-us/pricing/free-trial/ "免費試用帳戶")，已部署的三個角色會在數週內用光您的每月限制。 若要使用 Azure 管理入口網站刪除部署，請選取雲端服務，並按一下頁面底部的 **[刪除]**，然後選取實際執行或預備部署。

    ![pub](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-19.png)

13. 在 Visual studio 的 [Azure 活動記錄檔] 中，選取 **[在伺服器總管中開啟]**。

    在 **[伺服器總管]** 的 **[Azure 運算]** 下，您可以監視部署。如果已在 **[Publish Azure Application]** 精靈中選取 **[Enable Remote Desktop for all roles]**，則可在角色執行個體上按一下滑鼠右鍵，然後選取 **[Connect using Remote Desktop]**。

    ![pub](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-12.png)

實際執行將應用程式從預備提升至實際執行
--------------------------------------

1.  在 [Azure 管理入口網站](http://manage.windowsazure.com)中，按一下左窗格中的 **[雲端服務]** 圖示，然後選取您的雲端服務。

2.  按一下 **[交換]**。

3.  按一下 **[是]** 以完成 VIP (虛擬 IP) 交換。此步驟可能需要幾分鐘時間才能完成。

    ![Dashboard](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-c6.png)

4.  交換完成時，請按一下左窗格中的 **[雲端服務]** 圖示，然後選取您的雲端服務。

5.  將 **[實際執行]** 部署的 **[儀表板]** 索引標籤向下捲動至頁面右下方的 **[quick glance]** 區段。請注意，**[網站 URL]** 已從 GUID 前置詞變更為您的雲端服務名稱。

    ![Dashboard](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-c7.png)

6.  按一下 **[網站 URL]** 下的連結，或將它複製並貼到瀏覽器，以在實際執行環境中測試應用程式。

    如果還沒變更儲存體帳戶設定，則當您在雲端執行應用程式時，即會顯示測試應用程式的預備版本時所輸入的資料。

追蹤設定和檢視追蹤資料
----------------------

追蹤工具非常適合用於偵錯雲端應用程式。在教學課程的這一節當中，您將看到如何檢視追蹤資料。

1.  確認診斷連接字串已設為使用 Azure 儲存體帳戶，而非開發儲存體。

    如果您已遵循教學課程中的先前指示，它們將會相同。您可以使用 Visual Studio UI (角色的 **[屬性]** 中的 **[設定]** 索引標籤)，或查看 *ServiceConfiguration.\*.cscfg* 檔案，以確認它們是否相同。

    **注意：**最佳作法是對追蹤資料和實際執行資料使用不同的儲存體帳戶，但在本教學課程中為求簡化，您已對追蹤工具設定相同帳戶。

2.  在 Visual Studio 中，開啟 **WorkerRoleA** 專案中的 *WorkerRoleA.cs*、搜尋 `ConfigureDiagnostics`，然後檢查 `ConfigureDiagnostics` 方法。

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

    在此程式碼中，`DiagnosticMonitor` 會設為最多儲存 500 MB 的追蹤資訊 (超過 500 MB 後會覆寫存在最久的資料)，並設為儲存所有追蹤訊息 (LogLevel.Verbose)。`ScheduledTransferPeriod` 會每分鐘將追蹤資料傳送至儲存體。您必須設定 `ScheduledTransferPeriod` 以便儲存追蹤資料。

    每一個背景工作和 Web 角色中的 `ConfigureDiagnostics` 方法會將追蹤接聽程式設為在呼叫追蹤 API 時記錄資料。如需詳細資訊，請參閱[在 Azure 雲端應用程式中使用追蹤](http://blogs.msdn.com/b/windowsazure/archive/2012/10/24/using-trace-in-windows-azure-cloud-applications-1.aspx "在 Azure 中使用追蹤") (英文)。

3.  在 **[伺服器總管]** 中，按兩下 **WADLogsTable** (展開 **[儲存體]** / **[yourstorageaccountname]** / **[資料表]**)，以取得先前新增的儲存體帳戶。您可以輸入[為資料表設計工具建構篩選條件字串](http://msdn.microsoft.com/en-us/library/windowsazure/ff683669.aspx "WCF 篩選條件")，以限制顯示的實體。下列影像只顯示警告和錯誤訊息。

    ![Dashboard](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-trc.png)

新增角色執行個體新增另一個背景工作角色執行個體來處理增加的負載
--------------------------------------------------------------

有兩種方法可擴充 Azure 角色中的運算資源，那就是指定[設定雲端服務的大小](http://msdn.microsoft.com/en-us/library/windowsazure/ee814754.aspx "虛擬機器大小") 和 (或) 指定執行中虛擬機器的執行個體計數。

虛擬機器 (VM) 大小是指定在 *ServiceDefinition.csdef* 檔案中 `WebRole` 或 `WorkerRole` 元素的 `vmsize` 屬性中。預設設定為 `Small`，此設定會提供一個核心和 1.75 GB 的 RAM。對於使用大量記憶體、磁碟和頻寬的多執行緒應用程式，您可以增加虛擬機器大小以提高效能。例如，`ExtraLarge` 虛擬機器有 8 個 CPU 核心和 14 GB 的 RAM。對單一電腦增加記憶體、CPU 核心、磁碟和頻寬即為「擴充 (硬體資源)」**。適合進行擴充的候選對象包括使用[非同步方法](http://www.asp.net/mvc/tutorials/mvc-4/using-asynchronous-methods-in-aspnet-mvc-4 "非同步 MVC") (英文)的 ASP.NET Web 應用程式。如需每一個虛擬機器大小所提供資源的說明，請參閱[設定雲端服務的大小](http://msdn.microsoft.com/en-us/library/windowsazure/ee814754.aspx "虛擬機器大小")。

此應用程式中的背景工作角色 B 為高負載下的限制元件，因為它會執行傳送電子郵件的工作。(背景工作角色 A 只建立佇列訊息，不會大量耗用資源。)因為背景工作角色 B 不是多執行緒，而且沒有大量記憶體，因此不適合進行擴充。背景工作角色 B 可藉由增加執行個體計數，以線性方式進行擴充 (亦即，執行個體加倍時，效能也幾乎加倍)。增加運算執行個體數目即為「擴充 (機器數量)」**。每一個執行個體都需要成本，因此請在應用程式有需要時再擴充。

您可以更新 Visual Studio UI 中的設定，或直接編輯 *ServiceConfiguration.\*.cscfg* 檔案，來擴充 Web 角色或背景工作角色。執行個體計數是指定在角色 **[屬性]** 視窗的 **[組態]** 索引標籤中，以及指定在 *.cscfg* 檔案的 `Instances` 元素中。更新設定時必須部署已更新的組態檔，變更才能生效。或者，對於短暫增加的負載，您可以變更 Azure 管理入口網站中的角色執行個體數目。您也可以使用 Azure 管理 API 設定執行個體數目。最後，您可以使用[自動擴充應用程式區塊](/en-us/develop/net/how-to-guides/autoscaling/) (英文)，來自動擴充以因應增加的負載。如需自動擴充的詳細資訊，請參閱[此系列中最後一個教學課程](/en-us/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/)結尾處的連結。

在教學課程的這一節當中，您將使用管理入口網站擴充背景工作角色 B，但是您會先看到如何在 Visual Studio 執行此作業。

若要在 Visual Studio 執行此作業，您需要在雲端專案的 **[角色]** 下，對某個角色按一下滑鼠右鍵，並選取 **[屬性]**。

![Right Click Properties](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-rt-prop.png)

然後，您需要選取左邊的 **[組態]** 索引標籤，並在 **[服務組態]** 下拉式清單中選取 **[雲端]**。

![執行個體計數](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-instanceCnt.png)

請注意，您也可以在此索引標籤中設定虛擬機器大小。

下列步驟說明如何使用 Azure 管理入口網站來進行擴充。

1.  在 Azure 管理入口網站中，選取您的雲端服務，然後按一下 **[擴充]**。

2.  增加背景工作角色 B 的執行個體數目，然後按一下 **[儲存]**。

    ![增加執行個體](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-in3.png)

    佈建新的虛擬機器可能需要數分鐘的時間。

3.  選取 **[執行個體]** 索引標籤，以在應用程式中查看每一個角色執行個體。

    ![view instances](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-in2.png)

後續步驟後續步驟
----------------

現在您已看到如何設定、部署及擴充已完成的應用程式。後續的教學課程會示範如何從頭組建應用程式。在[下一個教學課程](/en-us/develop/net/tutorials/multi-tier-web-site/3-web-role/)中，您將組建 Web 角色。

如需使用 Azure 儲存體資料表、佇列和 Blob 的其他資源連結，請參閱[本系列的最後一個教學課程](/en-us/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/)結尾。

[教學課程 3](/en-us/develop/net/tutorials/multi-tier-web-site/3-web-role/)

