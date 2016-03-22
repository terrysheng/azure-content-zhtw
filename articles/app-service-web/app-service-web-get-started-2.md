<properties 
	pageTitle="在 Azure App Service 中開始使用 Web 應用程式 - 第 2 部分" 
	description="在 App Service 中點按幾下滑鼠，即可將重要的操作功能新增至您的 Web 應用程式。" 
	services="app-service\web"
	documentationCenter=""
	authors="cephalin" 
	manager="wpickett" 
	editor="" 
/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="hero-article"
	ms.date="03/14/2016" 
	ms.author="cephalin"
/>

# 開始使用 Azure App Service - 第 2 部分

在[開始使用 Azure App Service](app-service-web-get-started.md) 中，您已將 Web 應用程式部署至 Azure App Service，所有設定皆可快速、輕鬆更新。在本文中，您將快速地將一些重要的功能新增至您所部署的應用程式，使其保持理想狀態。如此一來，您將了解 App Service 不僅僅是網站主機服務提供者。它可為您絕佳的應用程式提供企業級功能，以便您將創意活力集中在以您的應用程式取悅您的使用者，而不在於符合真實世界的安全性、延展性、效能、管理等需求。

按幾下滑鼠，您將了解如何︰

- 強制執行使用者驗證
- 自動調整您的應用程式
- 接收您應用程式的效能警示。

不論您在前一篇文章中部署哪一個範例應用程式，您都可以遵循以下的作業。

## 驗證您的使用者

現在，讓我們看看將驗證新增至您的應用程式有多麼容易

1. 在您應用程式的刀鋒視窗 (您剛開啟) 中，按一下 [設定] > [驗證 / 授權]。![驗證 - 設定刀鋒視窗](./media/app-service-web-get-started/aad-login-settings.png)
    
2. 按一下 [開啟] 以開啟驗證。![驗證 - 開啟](./media/app-service-web-get-started/aad-login-auth-on.png)
    
4. 在 [驗證提供者] 中，按一下 [Azure Active Directory]。![驗證 - 選取 Azure AD](./media/app-service-web-get-started/aad-login-config.png)

5. 在 [Azure Active Directory 設定] 刀鋒視窗中，按一下 [快速]，然後按一下 [確定]。預設設定會在您的預設目錄中建立新的 Azure AD 應用程式。![驗證 - 快速設定](./media/app-service-web-get-started/aad-login-express.png)

6. 按一下 [儲存]。![驗證 - 儲存設定](./media/app-service-web-get-started/aad-login-save.png)

    成功變更之後，您會看到通知鈴變成綠色，以及一則清楚的訊息。

7. 回到您應用程式的主要刀鋒視窗，按一下 [URL] 連結 (或功能表列中的 [瀏覽])。請注意，此連結是 HTTP 位址。![驗證 - 瀏覽至 URL](./media/app-service-web-get-started/aad-login-browse-click.png) 但是在新的索引標籤中開啟應用程式後，URL 方塊就會重新導向數次並透過 HTTPS 位址到達您的應用程式。您會看到您已使用您的 Azure 訂用帳戶登入 Microsoft 帳戶，而且您已使用該帳戶自動登入應用程式。![驗證 - 已登入](./media/app-service-web-get-started/aad-login-browse-http-postclick.png)所以如果您現在開啟另一個瀏覽器 (以確保您尚未登入)，您將在瀏覽至相同應用程式的 URL 時看到登入畫面：![驗證 - 登入頁面](./media/app-service-web-get-started/aad-login-browse.png)如果您從未使用過 Azure Active Directory，您的預設目錄可能沒有任何 Azure AD 使用者。在此情況下，那裡唯一的帳戶可能是以您的 Azure 訂用帳戶登入的 Microsoft 帳戶，這就是為什麼您先前能在相同的瀏覽器中自動登入此應用程式。您也可以使用相同的 Microsoft 帳戶登入此登入頁面。

恭喜，您已在驗證流向您的網站的所有流量。

您可能已注意到在 [驗證 / 授權] 刀鋒視窗中，您能做的不僅止於此，例如︰

- 啟用社交登入
- 啟用多個登入選項
- 變更當使用者第一次瀏覽至您的應用程式時的預設行為

App Service 會針對某些常見的驗證需求提供周全的解決方案，所以您不需要自行提供驗證邏輯。如需詳細資訊，請參閱 [...](/services/app-service/)。

## 相應增加及相應放大您的應用程式

接下來，調整您的應用程式。調整您的 App Service 應用程式的方法有兩種︰

- [相應增加](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling)︰當您應增加 App Service 應用程式時，您會變更應用程式所屬的 App Service 方案的定價層。除了更多的 CPU、記憶體和磁碟空間，相應增加還可讓您擁有其他的功能，例如專用 VM 執行個體、自動調整，99.95% 的 SLA、自訂網域、自訂 SSL 憑證、部署位置、備份和還原等等。較高層級可提供更多功能給您的 App Service 應用程式。  
- [相應放大](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling)︰當您相應放大 App Service 應用程式時，您會變更您的應用程式 (或相同 App Service 方案中的應用程式) 執行所在的 VM 執行個體數目。在標準層或更高層，您可以啟用根據效能計量自動調整 VM 執行個體。 

我們就不再贅言，馬上為您的應用程式設定自動調整。

1. 首先，相應增加以啟用自動調整。在您應用程式的刀鋒視窗中，按一下 [設定] > [相應增加 (App Service 方案)]。![相應增加 - 設定刀鋒視窗](./media/app-service-web-get-started/scale-up-settings.png)

2. 捲動並選取 [S1 標準] 層 (這是支援自動調整的最低層 (下面所圈選))，然後按一下 [選取]。請注意，這一層將消耗您的免費試用額度。![相應增加 - 選擇層次](./media/app-service-web-get-started/scale-up-select.png)

    您已完成相應增加。
    
3. 接下來，設定自動調整。在您應用程式的刀鋒視窗中，按一下 [設定] > [相應放大 (App Service 方案)]。![相應放大 - 設定刀鋒視窗](./media/app-service-web-get-started/scale-out-settings.png)

4. 將 [調整依據] 變更為 [CPU 百分比]。下拉式清單之下的滑桿會跟著變更。然後，請方塊中輸入資料或移動滑桿，以定義介於 1 與 2 之間的 [執行個體] 範圍，以及介於 40 與 80 之間的 [目標範圍]。![相應放大 - 設定自動調整](./media/app-service-web-get-started/scale-out-configure.png)
    
    根據此設定，當 CPU 使用率高於 80% 時，您的應用程式會自動相應放大 (至最多 2 個執行個體)，而當 CPU 使用率低於 40% 時則會相應縮小 (至最少 1 個執行個體)。
    
5. 按一下功能表列中的 [儲存]。

恭喜，您的應用程式已在自動調整。

您可能已注意到在 [調整設定] 刀鋒視窗中，您能做的不僅止於此，例如︰

- 手動調整成特定的執行個體數目
- 依其他效能計量 (例如記憶體百分比或磁碟佇列) 調整
- 自訂觸發效能規則時的調整行為
- 根據排程自動調整
- 設定未來事件的自動調整行為

如需有關相應增加應用程式的詳細資訊，請參閱[在 Azure App Service 中調整定價層](../app-service/app-service-scale.md)。如需有關相應放大的詳細資訊，請參閱[手動或自動調整執行個體計數](../azure-portal/insights-how-to-scale.md)。

## 接收應用程式的警示

您的應用程式現在會自動相應放大，當它達到最大執行個體計數 (「標準」層為 10 個執行個體) 且 CPU 使用率超過所需的百分比 (80%) 時，會發生什麼狀況？ 您可以設定警示來通知您這種情況，因此您可以進一步相應增加您的應用程式 (舉例來說)。為此迅速設定警示。

1. 在您應用程式的刀鋒視窗中，按一下 [工具] > [警示]。![警示 - 設定刀鋒視窗](./media/app-service-web-get-started/alert-settings.png)

2. 按一下 [新增警示]。然後，在 [資源] 方塊中，選取結尾是 (serverfarms) 的資源。這就是您的 App Service 方案。![警示 - 新增 App Service 方案的警示](./media/app-service-web-get-started/alert-add.png)

3. 將 [名稱] 指定為 `CPU Maxed`、[計量] 指定為 [CPU 百分比]，以及 [臨界值] 指定為 `90`，然後選取 [電子郵件擁有者、參與者和讀者]，再按一下 [確定]。![警示 - 設定警示](./media/app-service-web-get-started/alert-configure.png)
    
    當 Azure 完成警示建立時，您會在 [警示] 刀鋒視窗中看見該警示。![警示 - 已完成的檢視](./media/app-service-web-get-started/alert-done.png)

恭喜，您現已接收警示。

此警示設定現在會每隔 5 分鐘檢查一次 CPU 使用率。如果該數字高於 90%，您以及獲得授權的人員都會收到電子郵件警示。若要查看有權接收警示的人員，請回到您應用程式的刀鋒視窗，然後按一下 [存取] 按鈕。![查看有誰收到警示](./media/app-service-web-get-started/alert-rbac.png)

您應該會看到 [訂用帳戶管理員] 已經是應用程式的 [擁有者]。如果您是 Azure 訂用帳戶 (如您的試用訂用帳戶) 的帳戶管理員，您就包含在內。如需 Azure 角色型存取控制的詳細資訊，請參閱 [Azure 角色型存取控制](../active-directory/role-based-access-control-configure.md)。

## 後續步驟

當您設定警示時，您可能已注意到 [工具] 刀鋒視窗中有一組豐富的工具，可讓您排解疑難問題、監視效能、測試弱點、管理資源、與 VM 主控台互動，以及新增實用的擴充功能。我們邀請您按一下每一個工具，輕鬆探索這些簡單卻功能強大的工具。

此外，了解如何以您部署的應用程式執行更多作業。以下是部分清單︰

- [購買並設定自訂網域名稱](custom-dns-web-site-buydomains-web-app.md)
- [設定預備環境](web-sites-staged-publishing.md)
- [設定連續部署](web-sites-publish-source-control.md)
- [備份您的應用程式](web-sites-backup.md)
- [啟用診斷記錄](web-sites-enable-diagnostic-log.md)
- [存取內部部署資源](web-sites-hybrid-connection-get-started.md)
- [了解 App Service 的運作方式](../app-service/app-service-how-works-readme.md) 

<!---HONumber=AcomDC_0316_2016-->