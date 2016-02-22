<properties
	pageTitle="從行動服務移轉到應用程式服務行動應用程式"
	description="了解如何輕鬆地將您的行動服務應用程式移轉至應用程式服務行動應用程式"
	services="app-service\mobile"
	documentationCenter=""
	authors="adrianhall"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/09/2016"
	ms.author="adrianhall"/>

# <a name="article-top"></a>將您現有的 Azure 行動服務移轉至 Azure App Service

透過 [Azure App Service 的公開上市版]，Azure 行動服務網站將可輕易地就地移轉，以使用 Azure App Service 的所有功能。本文件說明將您的網站從 Azure 行動服務移轉至 Azure App Service 時的情形。

## <a name="what-does-migration-do"></a>移轉對您的網站有何作用

移轉 Azure 行動服務，將會使您的行動服務變成 [Azure App Service] 應用程式，而不會對程式碼造成任何影響。您通知中樞、SQL 資料連接、驗證設定、排定的作業和網域名稱都將保持不變。使用您的 Azure 行動服務的行動用戶端仍可正常運作。移轉會在您的服務轉換為 Azure App Service 之後加以重新啟動。

[AZURE.INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

## <a name="why-migrate"></a>為何您應移轉網站

Microsoft 建議您移轉 Azure 行動服務，以使用 Azure App Service 的各項功能，其中包括：

  *  新的主機功能，包括 [WebJob] 和[自訂網域名稱]。
  *  除了[混合式連接]以外，使用 [VNet] 連接到您的內部部署資源。
  *  使用 New Relic 或 [Application Insights] 進行監視和疑難排解作業。
  *  內建的 DevOps 工具，包括[預備位置]、回復和生產環境測試。
  *  [自動調整]、負載平衡，以及[效能監視]。

若想進一步了解 Azure App Service 的優點，請參閱[比較行動服務與App Service] 主題。

## <a name="why-not-migrate"></a>為何您不應移轉網站

基於幾項原因，您不應立即移轉您的 Azure 行動服務：

  *  您目前處於忙碌期間，無法承受網站重新啟動。
  *  您不想在測試移轉程序之前對生產網站造成影響。
  *  您有多個免費或基本定價層的網站，而不想要同時移轉所有的網站。

在您處於忙碌期間，請規劃在排定的維護期間進行移轉。在移轉程序中會重新啟動您的網站，您的使用者可能會注意到此暫時性的可用性中斷。

此清單中大部分的項目都有因應措施。請參閱以下的[開始之前](#before-you-begin)一節，以取得詳細資料。

## <a name="before-you-begin"></a>開始之前

在移轉您的網站之前，您應遵循下列步驟：

  *  [備份您的行動服務]指令碼和 SQL Database
  *  (選擇性) 將行動服務層提高至標準

如果您想要在移轉生產網站之前先測試移轉程序，請複製您的生產環境 Azure 行動服務 (藉由複製資料來源來完成)，然後以新的 URL 測試移轉。您也需要指向測試網站的測試用戶端實作，以適當測試移轉後的網站。

### <a name="opt-raise-service-tier"></a>(選擇性) 將行動服務層提高至標準

所有共用主控方案的行動服務網站將會同時移轉。免費或基本定價層的行動服務，會與相同定價層和 [Azure 區域]中的其他服務共用主控方案。如果您的行動服務在標準定價層上運作，它會位在本身的主控方案中。如果您想要個別移轉免費或基本定價層中的網站，請將行動服務定價層暫時升級為標準。您可以在 [調整] 功能表中為您的行動服務執行此動作。

  1.  登入 [Azure 傳統入口網站]。
  2.  選取您的行動服務。
  3.  選取 [相應增加] 索引標籤。
  4.  在 [行動服務層] 下，按一下 [標準] 層。按一下位於頁面底部的 [儲存] 按鈕。

移轉之後，請記得將定價層設定為適當的設定。

## <a name="migrating-site"></a>移轉您的網站

若要移轉您的網站：

  1.  登入 [Azure 傳統入口網站]。
  2.  選取您的行動服務。
  3.  按一下 [移轉至 App Service] 按鈕。

    ![移轉按鈕][0]

  4.  閱讀 [移轉至 App Service] 對話方塊。
  5.  在提供的方塊中輸入您的行動服務名稱。例如，如果您的網域名稱是 contoso.azure-mobile.net，請在提供的方塊中輸入 _contoso_。
  6.  按一下刻度按鈕。

如果您移轉免費或基本定價層的行動服務，該定價層中的所有行動服務將會同時移轉。若要避免此狀況，您可以在移轉期間[將您要移轉的行動服務提升至](#opt-raise-service-tier)標準。

您可以在活動監視器中監視移轉的狀態，且您的網站在 Azure 傳統入口網站中會列示為*移轉中*。

  ![移轉活動監視器][1]

每個移轉的行動服務在每次移轉時可能需要 3 到 15 分鐘不等。您的網站在移轉期間仍可使用，但會移轉程序結束時重新啟動。網站在重新啟動程序期間將無法使用，此狀況可能會持續幾秒鐘。

## <a name="finalizing-migration"></a>完成移轉

您應規劃在移轉程序結束後從行動用戶端測試您的網站。請確定您可以執行所有的一般用戶端動作，而不會變更行動用戶端。此外，您也應確定在移轉中所進行的變更 (例如變更定價層) 可在必要時回復。

### <a name="update-app-service-tier"></a>選取適當的 App Service 定價層

在移轉至 Azure App Service 之後，您在價格方面將會有更多彈性。

  1.  登入 [Azure 入口網站]。
  2.  選取 [所有資源] 或 [應用程式服務]，然後按一下您已移轉的行動應用程式的名稱。
  3.  依預設會開啟 [設定] 刀鋒視窗，若未開啟，請按一下 [設定]。
  4.  按一下 [設定] 功能表中的 [App Service 方案]。
  5.  按一下 [定價層] 磚。
  6.  按一下適用於您的需求的磚，然後按一下 [選取]。您可能需要按一下 [檢視全部]，才能檢視可用的定價層。

建議您以下列各項做為起點：

| 行動服務定價層 | App Service 定價層 |
| :-------------------------- | :----------------------- |
| 免費 | F1 免費 |
| 基本 | B1 基本 |
| 標準 | S1 標準 |

請注意，您有相當大的彈性可為應用程式選擇適當的定價層。請參閱 [App Service 價格]，以充分了解新的 App Service 的價格。

> [AZURE.TIP] App Service 標準層包含您可能想要使用之多種功能的存取權，包括[預備位置]、自動備份和自動調整。您可以在相關位置查看新功能。

### <a name="review-migration-scheduler-jobs"></a>檢閱已移轉的排程器作業

排程器作業在移轉後約 30 分鐘內將不會顯示。任何排程的作業將持續在背景中執行。若要檢視已排程的作業：

  1.  登入 [Azure 入口網站]。
  2.  選取 [瀏覽 >]、在 [篩選] 方塊中輸入 **Schedule**，然後選取 [排程器集合]。

移轉後可用的排程器作業數量將有所限制。您應檢閱您的使用情形和 [Azure 排程器方案]。

### <a name="configure-cors"></a>視需要設定 CORS

跨原始資源共用是一項可讓網站存取不同網域之 Web API 的技術。如果您所使用 Azure 行動服務具有相關聯的網站，您就必須在移轉中設定 CORS。如果您從行動裝置以獨佔方式存取 Azure 行動服務，則在絕大多數的情況下都不需要設定 CORS。

您已移轉的 CORS 設定可做為 **MS\_CrossDomainWhitelist** 應用程式設定。若要將您的網站移轉至 App Service CORS 工具：

  1.  登入 [Azure 入口網站]。
  2.  選取 [所有資源] 或 [應用程式服務]，然後按一下您已移轉的行動應用程式的名稱。
  3.  依預設會開啟 [設定] 刀鋒視窗，若未開啟，請按一下 [設定]。
  4.  按一下 API 功能表中的 [CORS]。
  5.  在提供的方塊中輸入任何 [允許的原點]，每輸入一個就按一下 Enter 鍵。
  6.  如果您的 [允許的原點] 清單正確無誤，請按一下 [儲存] 按鈕。

這是選擇性工作，但可為後續提供更好的管理體驗。

> [AZURE.TIP]  使用 Azure App Service 的優點之一，是您可以在相同網站上執行您的網站和行動服務。如需詳細資訊，請參閱[後續步驟](#next-steps)一節。

### <a name="download-publish-profile"></a>下載新的發行設定檔

網站的發行設定檔移轉至 Azure App Service 後，會進行變更。如果您想要從 Visual Studio 發行您的網站，您將需要新的發行設定檔。若要下載新的發行設定檔：

  1.  登入 [Azure 入口網站]。
  2.  選取 [所有資源] 或 [應用程式服務]，然後按一下您已移轉的行動應用程式的名稱。
  3.  按一下 [取得發行設定檔]。

PublishSettings 檔案會下載至您的電腦。此檔案通常名為 _sitename_.PublishSettings。接著，您可以將發行設定匯入現有的專案中：

  1.  開啟 Visual Studio 和您的 Azure 行動服務專案。
  2.  在 [方案總管] 中，以滑鼠右鍵按一下您的專案，然後選取 [發佈...]。
  3.  按一下 [匯入]
  4.  按一下 [瀏覽]，然後選取已下載的發行設定檔案。按一下 [確定]。
  5.  按一下 [驗證連接]，以確保發行設定可運作。
  6.  選擇 [發佈] 以發佈您的網站。


## <a name="working-with-your-site"></a>在移轉後使用您的網站

在移轉之後，您將會在 [Azure 入口網站]中開始使用新的 App Service。以下是您過去在 [Azure 傳統入口網站]中執行之特定作業的某些注意事項，及其 App Service 的對等項目。

### <a name="publishing-your-site"></a>下載和發佈您已移轉的網站

您的網站可透過 git 或 ftp 來使用，而且可透過多種不同的機制重新發佈，包括 WebDeploy、TFS、Mercurial、GitHub 及 FTP。部署認證會隨著網站的其餘部分移轉。如果您未設定部署認證，或您不記得，您可以將其重設：

  1. 登入 [Azure 入口網站]。
  2. 選取 [所有資源] 或 [應用程式服務]，然後按一下您已移轉的行動應用程式的名稱。
  3. 依預設會開啟 [設定] 刀鋒視窗，若未開啟，請按一下 [設定]。
  4. 按一下 [發佈] 功能表中的 [部署認證]。
  5. 在提供的方塊中輸入新的部署認證，然後按一下 [儲存] 按鈕。

您可以使用這些認證透過 git 複製網站，或從 GitHub、TFS 或 Mercurial 設定自動化部署。如需詳細資訊，請參閱 [Azure App Service 部署文件]。

### <a name="appsettings"></a>應用程式設定

已移轉的行動服務大部分的設定都可透過 [應用程式設定] 來使用。您可以從 [Azure 入口網站]取得應用程式設定清單。若要檢視或變更您的應用程式設定：

  1. 登入 [Azure 入口網站]。
  2. 選取 [所有資源] 或 [應用程式服務]，然後按一下您已移轉的行動應用程式的名稱。
  3. 依預設會開啟 [設定] 刀鋒視窗，若未開啟，請按一下 [設定]。
  4. 按一下 [一般] 功能表中的 [應用程式設定]。
  5. 捲動至 [應用程式設定] 區段，並尋找您的應用程式設定。
  6. 按一下應用程式設定的值，以編輯該值。按一下 [儲存] 以儲存該值。

您可以同時更新多個應用程式設定。

> [AZURE.TIP]  您會發現有兩個具有相同值的 [應用程式設定]。例如，您可能會看到 _ApplicationKey_ 和 _MS\_ApplicationKey_。您只需要變更具有前置 **MS\_** 的應用程式設定。不過，同時更新這兩個應用程式設定，是不錯的做法。

### <a name="authentication"></a>驗證

所有的驗證設定都可做為已移轉之網站中的 [應用程式設定]。若要更新您的驗證設定，您必須變更適當的應用程式設定。下表列出您的驗證提供者所適用的應用程式設定：

| 提供者 | 用戶端識別碼 | 用戶端密碼 | 其他設定 |
| :---------------- | :------------------------ | :--------------------------- | :------------------------- |
| Microsoft 帳戶 | **MS\_MicrosoftClientID** | **MS\_MicrosoftClientSecret** | **MS\_MicrosoftPackageSID** |
| Facebook | **MS\_FacebookAppID** | **MS\_FacebookAppSecret** | |
| Twitter | **MS\_TwitterConsumerKey** | **MS\_TwitterConsumerSecret** | |
| Google | **MS\_GoogleClientID** | **MS\_GoogleClientSecret** | |
| Azure AD | **MS\_AadClientID** | | **MS\_AadTenants** |

注意：**MS\_AadTenants** 會儲存為租用戶網域 ([行動服務入口網站] 中的 [允許的租用戶] 欄位) 的逗號分隔清單。

> [AZURE.WARNING] **請不要使用 [設定] 功能表中的驗證機制**
>
> Azure App Service 分別在 [_驗證/授權_設定] 功能表下提供「無程式碼」驗證和授權系統，以及在 [設定] 功能表下提供已被取代的 [_行動驗證_] 選項。這些選項與已移轉的 Azure 行動服務不相容。您可以 [升級您的網站]，以利用 Azure App Service 驗證功能。

### <a name="easytables"></a>資料

行動服務中的 [資料] 索引標籤在 Azure 入口網站中已取代為 [簡單資料表]。若要存取簡單資料表：

  1. 登入 [Azure 入口網站]。
  2. 選取 [所有資源] 或 [應用程式服務]，然後按一下您已移轉的行動應用程式的名稱。
  3. 依預設會開啟 [設定] 刀鋒視窗，若未開啟，請按一下 [設定]。
  4. 按一下 [行動] 功能表中的 [簡單資料表]。

您可以按一下 [新增] 按鈕以新增資料表，或按一下資料表名稱以存取現有的資料表。在此刀鋒視窗中可以執行多項作業，包括：

  * 變更資料表權限
  * 編輯作業指令碼
  * 管理資料表結構描述
  * 刪除資料表
  * 清除資料表內容
  * 刪除資料表的特定資料列

### <a name="easyapis"></a>API

行動服務中的 [API] 索引標籤在 Azure 入口網站中已取代為 [簡單 API]。若要存取簡單 API：

  1. 登入 [Azure 入口網站]。
  2. 選取 [所有資源] 或 [應用程式服務]，然後按一下您已移轉的行動應用程式的名稱。
  3. 依預設會開啟 [設定] 刀鋒視窗，若未開啟，請按一下 [設定]。
  4. 按一下 [行動] 功能表中的 [簡單 API]。

您已移轉的 API 將會列在刀鋒視窗中。您也可以在此刀鋒視窗中新增 API。若要管理特定 API，請按一下該 API。從新的刀鋒視窗中，您可以調整權限，以及編輯 API 的指令碼。

### <a name="on-demand-jobs"></a>排程器作業

所有的排程器作業都可透過 [排程器作業集合] 區段來使用。若要存取您的排程器作業：

  1. 登入 [Azure 入口網站]。
  2. 選取 [瀏覽 >]、在 [篩選] 方塊中輸入 **Schedule**，然後選取 [排程器集合]。
  3. 選取網站的作業集合。它的名稱將是 _sitename_-Jobs。
  4. 按一下 [設定]。
  5. 按一下 [管理] 下的 [排程器作業]。

排程的工作會以您在移轉之前所指定的頻率列出。隨選作業將會停用。若要執行隨選作業：

  1. 選取您想要執行的作業。
  2. 如有必要，請按一下 [啟用] 以啟用作業。
  3. 按一下 [設定]，然後按 [排程]。
  4. 選取 [一次] 的週期性，然後按一下 [儲存]

您的隨需作業會位於 `App_Data/config/scripts/scheduler post-migration`。建議您將所有隨需作業轉換為 [WebJob]。您應撰寫新的排程器作業，做為 [WebJob]。

### <a name="notification-hubs"></a>通知中樞

行動服務會使用通知中樞進行推播通知作業。在移轉之後，會使用下列應用程式設定將通知中樞連結至您的行動服務：

| 應用程式設定 | 說明 |
| :------------------------------------- | :--------------------------------------- |
| **MS\_PushEntityNamespace** | 通知中樞命名空間 |
| **MS\_NotificationHubName** | 通知中樞名稱 |
| **MS\_NotificationHubConnectionString** | 通知中樞連接字串 |
| **MS\_NamespaceName** | MS\_PushEntityNamespace 的別名 |

您的通知中樞將透過 [Azure 入口網站]受到管理。請記下通知中樞名稱 (您可以使用 [應用程式設定] 找到此項目)：

  1. 登入 [Azure 入口網站]。
  2. 選取 [瀏覽>]，然後選取 \[通知中樞]
  3. 按一下與行動服務相關聯的通知中樞名稱。

> [AZURE.NOTE] 您的通知中樞如果是「混合」類型，則不會顯示。「混合」類型的通知中樞會同時使用「通知中樞」和舊版的「服務匯流排」功能。您將必須[轉換混合式命名空間]。轉換完成後，您的通知中樞會出現在 [Azure 入口網站]中。

如需詳細資訊，請檢閱[通知中樞]文件。

> [AZURE.TIP] [Azure 入口網站]中的通知中樞管理功能仍處於預覽階段。[Azure 傳統入口網站]仍可用來管理您所有的通知中樞。

### <a name="app-settings"></a>其他應用程式設定

以下是從您的行動服務移轉的其他應用程式設定，可從 [設定] > [應用程式設定] 來使用：

| 應用程式設定 | 說明 |
| :------------------------------- | :-------------------------------------- |
| **MS\_MobileServiceName** | 您的應用程式名稱 |
| **MS\_MobileServiceDomainSuffix** | 網域前置詞，即 azure-mobile.net |
| **MS\_ApplicationKey** | 您的應用程式金鑰 |
| **MS\_MasterKey** | 您的應用程式主要金鑰 |

應用程式金鑰和主要金鑰必須與您原始行動服務中的應用程式金鑰完全相同。特別是，行動用戶端會傳送應用程式金鑰，以驗證他們對行動 API 的使用。

### <a name="cliequivalents"></a>命令列對等項目

您將無法再使用 _azure mobile _命令來管理您的 Azure 行動服務網站。有許多功能已取代為 _azure site_ 命令。請使用下表來尋找常用命令的對等項目：

| _Azure Mobile_ 命令 | 對等的 _Azure site_ 命令 |
| :----------------------------------------- | :----------------------------------------- |
| mobile locations | site location list |
| mobile list | site list |
| mobile show _name_ | site show _name_ |
| mobile restart _name_ | site restart _name_ |
| mobile redeploy _name_ | site deployment redeploy _commitId_ _name_ |
| mobile key set _name_ _type_ _value_ | site appsetting delete _key_ _name_ <br/> site appsetting add _key_=\_value\_ _name_ |
| mobile config list _name_ | site appsetting list _name_ |
| mobile config get _name_ _key_ | site appsetting show _key_ _name_ |
| mobile config set _name_ _key_ | site appsetting delete _key_ _name_ <br/> site appsetting add _key_=\_value\_ _name_ |
| mobile domain list _name_ | site domain list _name_ |
| mobile domain add _name_ _domain_ | site domain add _domain_ _name_ |
| mobile domain delete _name_ | site domain delete _domain_ _name_ |
| mobile scale show _name_ | site show _name_ |
| mobile scale change _name_ | site scale mode _mode_ _name_ <br /> site scale instances _instances_ _name_ |
| mobile appsetting list _name_ | site appsetting list _name_ |
| mobile appsetting add _name_ _key_ _value_ | site appsetting add _key_=\_value\_ _name_ |
| mobile appsetting delete _name_ _key_ | site appsetting delete _key_ _name_ |
| mobile appsetting show _name_ _key_ | site appsetting delete _key_ _name_ |

藉由更新適當的應用程式設定，可更新驗證或推播通知設定。請編輯檔案，並透過 ftp 或 git 發佈您的網站。

### <a name="diagnostics"></a>診斷和記錄

Azure App Service 通常會停用 [診斷記錄]。若要啟用診斷記錄：

  1. 登入 [Azure 入口網站]。
  2. 選取 [所有資源] 或 [應用程式服務]，然後按一下您已移轉的行動應用程式的名稱。
  3. 依預設會開啟 [設定] 刀鋒視窗，若未開啟，請按一下 [設定]。
  4. 選取 [功能] 功能表下的 [診斷記錄]。
  5. 對下列記錄檔按一下 [開啟]：[應用程式記錄 (檔案系統)]、[詳細錯誤訊息] 和 [失敗要求的追蹤]
  6. 針對 Web 伺服器記錄，按一下 [檔案系統]
  7. 按一下 [儲存]

若要檢視記錄檔：

  1. 登入 [Azure 入口網站]。
  2. 選取 [所有資源] 或 [應用程式服務]，然後按一下您已移轉的行動應用程式的名稱。
  3. 按一下 [工具] 按鈕。
  4. 選取 [觀察] 功能表下的 [記錄資料流]。

記錄檔在產生時會串流至提供的視窗中。您也可以下載的記錄檔，以便後續使用您的部署認證加以分析。如需詳細資訊，請參閱[記錄]文件。

## <a name="next-steps"></a>後續步驟

除了您的應用程式會移轉至 App Service 以外，還有更多功能可供您使用：

  * 部署[預備位置]可讓您預備網站的變更，並執行 A/B 測試。
  * [WebJob] 可取代隨選排定作業。
  * 您可以將網站連結至 GitHub、TFS 或 Mercurial，以[連續部署]網站。
  * 您可以使用 [Application Insights] 監視您的網站。
  * 以相同的程式碼為網站和行動 API 提供服務。

### <a name="upgrading-your-site"></a>將您的行動服務網站升級至 Azure Mobile Apps SDK

  * 對於以 Node.js 為基礎的伺服器專案，新的 [Mobile Apps Node.js SDK] 提供許多新功能。例如，您現在可以執行本機開發和偵錯、使用 0.10 以上的任何 Node.js 版本，以及使用任何 Express.js 中介軟體自訂。

  * 對於以 .NET 為基礎的伺服器專案，新的 [Mobile Apps SDK NuGet 封裝](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/)在 NuGet 相依性具有更大的彈性、支援新的 App Service 驗證功能，而且是由任何 ASP.NET 專案 (包括 MVC) 所組成。若要深入了解升級，請參閱[將您現有的 .NET 行動服務升級為 App Service](app-service-mobile-net-upgrading-from-mobile-services.md)。

<!-- Images -->
[0]: ./media/app-service-mobile-migrating-from-mobile-services/migrate-to-app-service-button.PNG
[1]: ./media/app-service-mobile-migrating-from-mobile-services/migration-activity-monitor.png
[2]: ./media/app-service-mobile-migrating-from-mobile-services/triggering-job-with-postman.png

<!-- Links -->
[App Service 價格]: https://azure.microsoft.com/pricing/details/app-service/
[Application Insights]: ../application-insights/app-insights-overview.md
[自動調整]: ../app-service-web/web-sites-scale.md
[Azure App Service]: ../app-service/app-service-value-prop-what-is.md
[Azure App Service 部署文件]: ../app-service-web/web-sites-deploy.md
[Azure 傳統入口網站]: https://manage.windowsazure.com
[Azure 入口網站]: https://portal.azure.com
[Azure 區域]: https://azure.microsoft.com/regions/
[Azure 排程器方案]: ../scheduler/scheduler-plans-billing.md
[連續部署]: ../app-service-web/web-sites-publish-source-control.md
[轉換混合式命名空間]: https://azure.microsoft.com/blog/updates-from-notification-hubs-independent-nuget-installation-model-pmt-and-more/
[curl]: http://curl.haxx.se/
[自訂網域名稱]: ../app-service-web/web-sites-custom-domain-name.md
[Fiddler]: http://www.telerik.com/fiddler
[Azure App Service 的公開上市版]: /blog/announcing-general-availability-of-app-service-mobile-apps/
[混合式連接]: ../app-service-web/web-sites-hybrid-connection-get-started.md
[記錄]: ../app-service-web/web-sites-enable-diagnostic-log.md
[Mobile Apps Node.js SDK]: https://github.com/azure/azure-mobile-apps-node
[比較行動服務與App Service]: app-service-mobile-value-prop-migration-from-mobile-services.md
[通知中樞]: ../notification-hubs/notification-hubs-overview.md
[效能監視]: ../app-service-web/web-sites-monitor.md
[Postman]: http://www.getpostman.com/
[備份您的行動服務]: ../mobile-services/mobile-services-disaster-recovery.md
[預備位置]: ../app-service-web/web-sites-staged-publishing.md
[VNet]: ../app-service-web/web-sites-integrate-with-vnet.md
[WebJob]: ../app-service-web/websites-webjobs-resources.md

<!---HONumber=AcomDC_0211_2016-->