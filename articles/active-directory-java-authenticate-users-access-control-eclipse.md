<properties urlDisplayName="Access Control" pageTitle="如何使用存取控制 (Java) - Azure 功能指南" metaKeywords="" description="了解如何在 Azure 中使用 Java 開發和使用存取控制。" metaCanonical="" services="active-directory" documentationCenter="Java" title="How to Authenticate Web Users with Azure Access Control Service Using Eclipse" videoId="" scriptId="" authors="robmcm" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="active-directory" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="09/25/2014" ms.author="robmcm" />

# 如何使用 Eclipse 搭配 Azure 存取控制服務來驗證 Web 使用者

本指南將顯示如何在 Azure Plugin for Eclipse with Java (由 Microsoft Open Technologies 提供) 內使用 Azure 存取控制服務 (ACS)。如需 ACS 的詳細資訊，請參閱[後續步驟](#next_steps)一節。

> [WACOM.NOTE]
> Azure Access Services Control Filter (由 Microsoft Open Technologies 提供) 是社群技術預覽。作為發行前軟體，它尚未得到 Microsoft Open Technologies, Inc. 及 Microsoft 的正式支援。

## 目錄

-   [什麼是 ACS？][]
-   [概念][]
-   [必要條件][]
-   [建立命名空間][]
-   [新增身分識別提供者][]
-   [新增信賴憑證者應用程式][]
-   [建立規則][]
-   [將憑證上傳至您的 ACS 命名空間][]
-   [檢閱應用程式整合頁面][]
-   [建立 Java Web 應用程式][]
-   [將 ACS 篩選器程式庫新增至您的應用程式][]
-   [部署至計算模擬器][]
-   [部署至 Azure][]
-   [後續步驟][]

## <a name="what-is"></a>什麼是 ACS？

大部分開發人員都不是身分識別專家，因而通常不想要
花時間開發其應用程式和服務的驗證與授權機制。
ACS 這項 Azure 服務
提供簡單的方式來驗證需要存取您的 Web
應用程式和服務的使用者，而不需要將複雜的
驗證邏輯加入程式碼中。

以下是 ACS 中提供的功能：

-   與 Windows Identity Foundation (WIF) 整合。
-   支援熱門的 Web 身分識別提供者 (IP)，包括 Windows Live ID、Google、Yahoo! 和 Facebook。
-   支援 Active Directory Federation Services (AD FS) 2.0。
-   一項開放式資料通訊協定 (OData) 型管理服務，可用來對 ACS 設定進行程式設計存取。
-   一個管理入口網站，可用來對 ACS 設定進行系統管理存取。

如需 ACS 的詳細資訊，請參閱＜[存取控制服務 2.0][]＞。

## <a name="concepts"></a>概念

Azure ACS 是以宣告式身分識別為原則來打造 -
以一致的方式為內部部署或雲端中執行的應用程式
建立驗證機制。宣告式身分識別
提供一種常用的方法，讓應用程式和服務
針對其組織、其他組織和網際網路上的使用者，
取得它們所需的身分識別資訊。

若要完成本指南中的工作，您應該了解下列
概念：

**用戶端** - 在本作法指南中，這是指嘗試存取您的 Web 應用程式
的瀏覽器。

**信賴憑證者 (RP) 應用程式** - RP 應用程式是將驗證工作交給
外部授權單位負責的網站或服務。以
身分識別的行話來說，就是 RP 信任該授權單位。本指南
將說明如何將您的應用程式設定為信任 ACS。

**權杖** - 權杖指的是一種安全性資料集合，通常
會在順利驗證使用者時發出。其包含一組*宣告*，即已驗證之使用者的屬性。宣告可以代表
使用者的名稱、使用者所屬角色的識別碼，或使用者的年紀
等等。權杖通常具有數位簽章，表示它一律可以
回溯至其簽發者，而且其內容無法篡改。
使用者需要取得 RP 應用程式的存取權時，
需出示由 RP 應用程式信任的授權單位所簽發的有效權杖。

**身分識別提供者 (IP)** - IP 是負責驗證
使用者身分識別並簽發安全性權杖的授權單位。簽發權杖的實際工作
是透過稱為 Security Token Service (STS) 的特殊服務來實作。
典型的 IP 範例包括 Windows Live ID、
Facebook、商業使用者存放庫 (如 Active Directory) 等等。
當 ACS 已設定為信任某個 IP 時，系統將接受並
驗證該 IP 所簽發的權杖。ACS 可以一次信任多個 IP，
表示當您的應用程式信任 ACS 時，您可以立即
從 ACS 代表您信任的所有 IP，
將您的應用程式提供給所有已驗證的使用者。

**同盟提供者 (FP)** - IP 有使用者的直接資料，
並會使用其認證來驗證他們，
以及簽發關於其身分的宣告。同盟提供者 (FP) 是不同種類的
授權單位：不是直接驗證使用者，而是擔任
一個 RP 與一個或多個 IP 之間的媒介和
代理驗證。IP 和 FP 都會簽發安全性權杖，因此它們都會使用
Security Token Services (STS)。ACS 是一種 FP。

**ACS 規則引擎** - 用來將傳入的權杖從
信任的 IP 轉換為 RP 所耗用之權杖的邏輯，是以簡單的宣告轉換規則格式
來編寫。ACS 包含一個規則引擎，
負責套用您為 RP 指定的宣告轉換
規則。

**ACS 命名空間** - 命名空間是您用於組織設定
之 ACS 的最上層分割區。命名空間會保留一個清單，其列出您
信任的 IP、您要供應的 RP 應用程式、您預期
規則引擎處理傳入權杖時所使用的規則等等。命名空間
會公開應用程式和開發人員將使用的各種端點，
以取得 ACS 來執行其功能。

下圖顯示 ACS 驗證與 Web 應用程式如何搭配運作
：

![ACS flow diagram][acs_flow]

1.  用戶端 (在此案例中是指瀏覽器) 向 RP 要求一個頁面。
2.  因為要求尚未經過驗證，所以 RP 將使用者重新導向至信任的授權單位 (即 ACS)。ACS 列出已為此 RP 指定的 IP，供使用者選擇。使用者選取適當的 IP。
3.  用戶端瀏覽至 IP 的驗證頁面，並提示使用者登入。
4.  在用戶端經過驗證後 (例如，輸入了身分識別認證)，IP 即簽發安全性權杖。
5.  在簽發安全性權杖後，IP 會重新導向用戶端至 ACS，而且用戶端會傳送 IP 所簽發的安全性權杖給 ACS。
6.  ACS 驗證 IP 所簽發的安全性權杖、將此權杖中的身分識別宣告輸入至 ACS 規則引擎、計算輸出的身分識別宣告，然後簽發含有這些輸出宣告的新安全性權杖。
7.  ACS 將用戶端重新導向至 RP。用戶端將 ACS 所簽發的全新安全性權杖傳送給 RP。RP 在 ACS 所簽發的安全性權杖上驗證簽章及宣告，然後傳回原先要求的頁面。

## <a name="pre"></a>必要條件

若要完成本指南中的工作，您需要有下列項目：

- Java Developer Kit (JDK) 1.6 版或更新版本。
- Eclipse IDE for Java EE Developers (Indigo 或更新版本)。這可透過 <http://www.eclipse.org/downloads/> 下載。 
- Java 型 Web 伺服器或應用程式伺服器的散發套件，例如 Apache Tomcat、GlassFish、JBoss Application Server 或 Jetty。
- Azure 訂閱，可從 <http://www.microsoft.com/windowsazure/offers/> 取得。
- Azure Plugin for Eclipse with Java (由 Microsoft Open Technologies 提供) - 2014 年 4 月發行。如需詳細資訊，請參閱[安裝 Azure Plugin for Eclipse with Java (由 Microsoft Open Technologies 提供)](http://msdn.microsoft.com/zh-tw/library/windowsazure/hh690946.aspx) (英文)。
- 要與您應用程式搭配使用的 X.509 憑證。您需要此憑證同時具有公開憑證 (.cer) 和 個人資訊交換 (.PFX) 格式。(本教學課程稍後將描述建立此憑證的選項)。
- 熟悉在[使用 Eclipse 建立 Azure 的 Hello World 應用程式](http://msdn.microsoft.com/zh-tw/library/windowsazure/hh690944.aspx) (英文) 中所討論的 Azure 計算模擬器和部署技術。

## <a name="create-namespace"></a>建立 ACS 命名空間

若要在 Azure 中開始使用存取控制服務 (ACS)，您必須
建立 ACS 命名空間。命名空間可提供在您的應用程式內
定址 ACS 資源的唯一範圍。

1. 登入 [Azure 管理入口網站][]。
2. 按一下 **Active Directory**。 
3. 若要建立新的存取控制命名空間，請依序按一下 [**新增**]、[**應用程式服務**]、[**存取控制**] 和 [**快速建立**]。 
4. 輸入命名空間的名稱。Azure 會確認名稱是否具唯一性。
5. 選取使用命名空間的地區。為了獲得最佳效能，請使用您要在其中部署應用程式的地區。
6. 如果您有多個訂閱，請選取要對 ACS 命名空間使用的訂閱。
7. 按一下 [**建立**]。

Azure 即會建立並啟動命名空間。等到新命名空間的狀態變成 [**作用中**] 之後再繼續。 

## <a name="add-IP"></a>新增身分識別提供者

在此工作中，您會新增 IP，以與您的 RP 應用程式搭配使用
進行驗證。基於示範目的，此工作會顯示如何新增
Windows Live 作為 IP，但是您可以使用 ACS
管理入口網站中列出的任一個 IP。


1.  在 [Azure 管理入口網站][]中，按一下 [**Active Directory**]，選取存取控制命名空間，然後按一下 [**管理**]。即會開啟 ACS 管理入口網站。
2.  在 ACS 管理入口網站的左導覽窗格中，按一下 [**身分識別提供者**]。
3.  依預設會啟用 Windows Live ID，而且無法刪除。基於本教學課程的目的，只會使用 Windows Live ID。不過，本畫面就是您可以按一下 [**新增**] 按鈕來新增其他 IP 的畫面。

現在已啟用 Windows Live ID 作為 ACS 命名空間的 IP。接著，您
可以指定 Java Web 應用程式 (將在稍後建立) 作為 RP。

## <a name="add-RP"></a>新增信賴憑證者應用程式

在此工作中，您可以設定 ACS，將 Java Web
應用程式辨識為有效的 RP 應用程式。

1.  在 ACS 管理入口網站上，按一下 [**信賴憑證者應用程式**]。
2.  在 [**信賴憑證者應用程式**] 頁面上，按一下 [**新增**]。
3.  在 [**Add Relying Party Application**] 頁面上，執行下列動作：
    1.  在 [**名稱**] 中，輸入 RP 的名稱。基於本教學課程的目的，輸入 **Azure Web
        App**。
    2.  在 [**模式**] 中，選取 [**Enter settings manually**]。
    3.  在 [**領域**] 中輸入 URI，供 ACS 所簽發的安全性權杖套用至此 URI。對於此工作，輸入 **http://localhost:8080/**。

        ![Relying party realm for use in compute emulator][relying_party_realm_emulator]
    4.  在 [**傳回 URL**] 中輸入 URL，供 ACS 將安全性權杖傳回到此 URL。對於此工作，輸入 **http://localhost:8080/MyACSHelloWorld/index.jsp**

        ![Relying party return URL for use in compute emulator][relying_party_return_url_emulator]
    5.  在其餘的欄位中接受預設值。

4.  按一下 [**儲存**]。

您現在已順利設定 Java Web 應用程式，當它在 Azure 計算模擬器 (在
http://localhost:8080/ /) 執行時，將成為 ACS 命名空間中的 RP。接著，建立
ACS 用來為 RP 處理宣告的規則。

## <a name="create-rules"></a>建立規則

在此工作中，您可以定義規則，驅動從
IP 傳遞至 RP 的宣告方法。基於本指南的目的，我們只會將 ACS 設定
成直接在輸出權杖中複製輸入
宣告類型和值，而不會篩選或修改它們。

1.  在 ACS 管理入口網站主頁面上，按一下 [**規則群組**]。
2.  在 [**規則群組**] 頁面上，按一下 [**Default Rule Group for Azure Web App**]。
3.  在 [**Edit Rule Group**] 頁面上，按一下 [**產生**]。
4.  在 [**Generate Rules:** 頁面上，確定已核取 Windows Live ID，然後按一下 **[產生]**。	
5.  在 [**Edit Rule Group**] 頁面上，按一下 [**儲存**]。

## <a name="upload-certificate"></a>將憑證上傳至您的 ACS 命名空間

在此工作中，您可以上傳 .PFX 憑證，這將用來簽發 ACS 命名空間所建立的權杖要求。

1.  在 ACS 管理入口網站主頁面上，按一下 [**憑證與金鑰**]。
2.  在 [**憑證與金鑰**] 頁面上，按一下 [**權杖簽署**] 上方的 [**新增**]。
3.  在 [**Add Token-Signing Certificate or Key**] 頁面上：
    1. 在 [**Used for**] 區段中，按一下 [**信賴憑證者應用程式**]，選取 [**Azure Web App**] \(先前設為信賴憑證者應用程式的名稱\)。
    2. 在 [**類型**] 區段中，選取 [**X.509 憑證**]。
    3. 在 [**憑證**] 區段中，按一下瀏覽按鈕，並導覽至您要使用的 X.509 憑證檔案。這將為 .PFX 檔案。選取檔案，按一下 [**開啟**]，  ，然後在 [**密碼**] 文字方塊中輸入憑證密碼。請注意，基於測試目的，您可能使用自我簽署憑證。若要建立自我簽署憑證，請使用 [**ACS Filter Library**] 對話方塊 (稍後將有描述) 中的 [**新增**] 按鈕，或使用 Azure Starter Kit for Java (由 Microsoft Open Technologies 提供) 的[專案網站][]上的 **encutil.exe** 公用程式。
    4. 確定已核取 [**Make Primary**]。您的 [**Add Token-Signing Certificate or Key**] 頁面應該看起來如下。
        ![Add token-signing certificate][add_token_signing_cert]
    5. 按一下 [**儲存**] 以儲存您的設定，並關閉 [**Add Token-Signing Certificate or Key**] 頁面。

接著，檢閱 [應用程式整合] 頁面中的資訊，並
複製您將 Java Web 應用程式設定成
使用 ACS 所需的 URI。

## <a name="review-app-int"></a>檢閱 [應用程式整合] 頁面

您可以找到將 Java Web 應用程式
(RP 應用程式) 設定成在 ASC 管理入口網站的
[應用程式整合] 頁面上使用 ACS 所需的所有資訊和程式碼。
設定 Java Web 應用程式進行同盟驗證時，
您將需要此資訊。

1.  在 ACS 管理入口網站上，按一下 [**應用程式整合**]。  
2.  在 [**應用程式整合**] 頁面上，按一下 [**登入頁面**]。
3.  在 [**Login Page Integration**] 頁面上，按一下 [**Azure Web App**]。

在 [**Login Page Integration:** 頁面中，[Option 1: Link to an ACS-hosted login page]**** 將用於 Java Web 應用程式中。將 Azure Access Control Services Filter 程式庫新增至 Java 應用程式時，您將需要此值。

## <a name="create-java-app"></a>建立 Java Web 應用程式
1. 在 Eclipse 內，於功能表上依序按一下 [**檔案**]、[**新增**] 和 [**Dynamic Web Project**]。(如果在按一下 [**檔案**]、[**新增**] 後未看到 [**Dynamic Web Project**] 列為可用專案，請執行下列動作：依序按一下 [**檔案**]、[**新增**]、[**專案**]、展開 [**Web**]、按一下 [**Dynamic Web Project**]，然後按 [**下一步**]。)基於本教學課程的目的，將專案命名為 **MyACSHelloWorld**。(確定您使用此名稱，本教學課程中的後續步驟預期您的 WAR 檔案將命名為 MyACSHelloWorld)。您的畫面將出現，如下所示：

    ![Create a Hello World project for ACS exampple][create_acs_hello_world]

    按一下 [**完成**]。
2. 在 Eclipse 的專案總管檢視內，展開 [**MyACSHelloWorld**]。在 [**WebContent**] 上按一下滑鼠右鍵，按一下 [**新增**]，然後按一下 [**JSP File**]。
3. 在 [**New JSP File**] 對話方塊中，將檔案命名為 **index.jsp**。將父資料夾保留為 MyACSHelloWorld/WebContent，如下所示：

    ![Add a JSP file for ACS example][add_jsp_file_acs]

    按 [**下一步**]。

4. 在 [**Select JSP Template**] 對話方塊中，選取 [**New JSP File (html)**]，然後按一下 [**完成**]。
5. 在 Eclipse 開啟 index.jsp 檔案時，加入文字以在現有的 `<body>` 元素內顯示 **Hello ACS World!**。您更新的 `<body>` 內容應該出現如下：

        <body>
          <b><% out.println("Hello ACS World!"); %></b>
        </body>
    
    儲存 index.jsp。
  
## <a name="add_acs_filter_library"></a>將 ACS 篩選器程式庫新增至您的應用程式

1. 在 Eclipse 的專案總管中，於 [**MyACSHelloWorld**] 上按一下滑鼠右鍵，按一下 [**Build Path**]，然後按一下 [**Configure Build Path**]。
2. 在 [**Java 組建路徑**] 對話方塊中，按一下 [**程式庫**] 索引標籤。
3. 按一下 [**新增程式庫**]。
4. 按一下 [**Azure Access Control Services Filter (by MS Open Tech)**]，然後按 [**下一步**]。[**Azure Access Control Services Filter**] 對話方塊隨即出現   ([**位置**] 欄位可能具有不同路徑，視您安裝 Eclipse 的位置而定，而且版本號碼也可能不同，視軟體更新而定)。

    ![Add ACS Filter library][add_acs_filter_lib]

5. 在瀏覽器中開啟管理入口網站的 [**Login Page Integration**] 頁面，複製 [**Option 1:Link to an ACS-hosted login page**] 欄位中列出的 URL，並將它貼入 Eclipse 對話方塊的 [**ACS Authentication Endpoint**] 欄位。
6. 在瀏覽器中開啟管理入口網站的 [**Edit Relying Party Application**] 頁面，複製 [**領域**] 欄位中列出的 URL，並將它貼入 Eclipse 對話方塊的 [**Relying Party Realm**] 欄位。
7. 在 Eclipse 對話方塊的 [**安全性**] 區段中，如果您想要使用現有的憑證，請按一下 [**瀏覽**]，導覽至您要使用的憑證並選取它，然後按一下 [**開啟**]。或者，如果您想要建立新憑證，請按一下 [**新增**] 以顯示 [**New Certificate**] 對話方塊，然後為新憑證指定密碼、.cer 檔案的名稱，以及 .pfx 檔案的名稱。
8. 核取 [**Embed the certificate in the WAR file**]。以此方式內含憑證，可將其併入您的部署中，而不需要您將它手動新增為元件。(如果您必須將憑證儲存在 WAR 檔案外，則可以將憑證新增為角色元件，並取消核取 [**Embed the certificate in the WAR file**]。)
9. [選用] 維持核取 [**Require HTTPS connections**]。如果設定此選項，您將需要使用 HTTPS 通訊協定存取應用程式。如果不想要要求 HTTPS 連接，請取消核取此選項。
10. 為了部署至計算模擬器，您的 [**Azure ACS 篩選器**] 設定將看起來如下。

    ![Azure ACS Filter settings for a deployment to the compute emulator][add_acs_filter_lib_emulator]

11. 按一下 [**完成**]。
12. 當呈現一個對話方塊，描述將建立 web.xml 檔案時，請按一下 [**是**]。
13. 按一下 [**確定**] 關閉 [**Java 組建路徑**] 對話方塊。

## <a name="deploy_compute_emulator"></a>部署至計算模擬器

1. 在 Eclipse 的專案總管中，於 [**MyACSHelloWorld**] 上按一下滑鼠右鍵，按一下 [**Azure**]，然後按一下 [**Package for Azure**]。
2. 在 [**專案名稱**] 中，輸入 **MyAzureACSProject**，然後按 [**下一步**]。
3. 選取 JDK 和應用程式伺服器。([在 Eclipse 建立 Azure 的 Hello World 應用程式](http://msdn.microsoft.com/zh-tw/library/windowsazure/hh690944.aspx) (英文) 教學課程中將詳細探討這些步驟。)
4. 按一下 [**完成**]。
5. 按一下 [**在 Azure 模擬器中執行**] 按鈕。
6. 在計算模擬器中啟動 Java Web 應用程式後，請關閉瀏覽器的所有執行個體 (以便任何目前瀏覽器工作階段不會干擾 ACS 登入測試)。
7. 在瀏覽器中開啟 <http://localhost:8080/MyACSHelloWorld/> 以執行應用程式 (或 <https://localhost:8080/MyACSHelloWorld/>，如果已核取 [**Require HTTPS connections**])。系統應該會提示您進行 Windows Live ID 登入，然後會將您帶往為信賴憑證者應用程式指定的傳回 URL。
99.  檢視您的應用程式之後，按一下 [**Reset Azure Emulator**] 按鈕。

## <a name="deploy_azure"></a>部署至 Azure

若要部署至 Azure，您將需要 ACS 命名空間的信賴憑證者領域和傳回 URL。

1. 在 Azure 管理入口網站內，於 [**Edit Relying Party Application**] 頁面中，將 [**領域**] 修改成所部署站台的 URL。將 **example** 取代為您對部署所指定的 DNS 名稱。

    ![Relying party realm for use in production][relying_party_realm_production]

2. 將 [**傳回 URL**] 修改成應用程式的 URL。將 **example** 取代為您對部署所指定的 DNS 名稱。

    ![Relying party return URL for use in production][relying_party_return_url_production]

3. 按一下 [**儲存**]，以儲存您更新的信賴憑證者領域及「傳回 URL」變更。
4. 讓 [**Login Page Integration**] 頁面在瀏覽器中保持開啟，您稍後將需要從中複製。
5. 在 Eclipse 的專案總管中，於 [**MyACSHelloWorld**] 上按一下滑鼠右鍵，按一下 [**Build Path**]，然後按一下 [**Configure Build Path**]。
6. 依序按一下 [**程式庫**] 索引標籤、[**Azure Access Control Services Filter**] 和 [**編輯**]。
7. 在瀏覽器中開啟管理入口網站的 [**Login Page Integration**] 頁面，複製 [**Option 1:Link to an ACS-hosted login page**] 欄位中列出的 URL，並將它貼入 Eclipse 對話方塊的 [**ACS Authentication Endpoint**] 欄位。
8. 在瀏覽器中開啟管理入口網站的 [**Edit Relying Party Application**] 頁面，複製 [**領域**] 欄位中列出的 URL，並將它貼入 Eclipse 對話方塊的 [**Relying Party Realm**] 欄位。
9. 在 Eclipse 對話方塊的 [**安全性**] 區段中，如果您想要使用現有的憑證，請按一下 [**瀏覽**]，導覽至您要使用的憑證並選取它，然後按一下 [**開啟**]。或者，如果您想要建立新憑證，請按一下 [**新增**] 以顯示 [**New Certificate**] 對話方塊，然後為新憑證指定密碼、.cer 檔案的名稱，以及 .pfx 檔案的名稱。
10. 如果您想要將憑證內嵌在 WAR 檔案中，請維持核取 [**Embed the certificate in the WAR file**]。
11. [選用] 維持核取 [**Require HTTPS connections**]。如果設定此選項，您將需要使用 HTTPS 通訊協定存取應用程式。如果不想要要求 HTTPS 連接，請取消核取此選項。
12. 為了部署至計算模擬器，您的 Azure ACS 篩選器設定將看起來如下。

    ![Azure ACS Filter settings for a production deployment][add_acs_filter_lib_production]

13. 按一下 [**完成**] 以關閉 [**編輯程式庫**] 對話方塊。
14. 按一下 [**確定**] 關閉 [**MyACSHelloWorld 的屬性**] 對話方塊。
15. 在 Eclipse 中，按一下 [**Publish to Azure Cloud**] 按鈕。回應提示，類似於[在 Eclipse 建立 Azure 的 Hello World 應用程式](http://msdn.microsoft.com/zh-tw/library/windowsazure/hh690944.aspx) (英文) 主題的**將應用程式部署至 Azure** 一節中的作法。 

在部署了 Web 應用程式後，請關閉任何開啟的瀏覽器工作階段、執行 Web 應用程式，而且系統應該提示您利用 Windows Live ID 認證登入，然後傳送至信賴憑證者應用程式的傳回 URL。

當您使用完 ACS Hello World 應用程式時，請記得刪除部署 (您可以在[在 Eclipse 建立 Azure 的 Hello World 應用程式](http://msdn.microsoft.com/zh-tw/library/windowsazure/hh690944.aspx) (英文) 主題中了解如何刪除部署)。


## <a name="next_steps"></a>後續步驟

若要檢查 ACS 傳回給應用程式的安全性聲明標記語言 (SAML)，請參閱[如何檢視 Azure 存取控制服務傳回的 SAML][]。若要進一步探索 ACS 的功能及試驗其他更複雜的案例，請參閱[存取控制服務 2.0][]。

此範例也使用 [**Embed the certificate in the WAR file**] 選項。此選項可讓您輕易部署憑證。如果您想要改為讓簽署憑證與 WAR 檔案維持分開狀態，則可以使用下列技術：

1. 在 [**Azure Access Control Services Filter**] 對話方塊的 [**安全性**] 區段內，輸入 **${env.JAVA_HOME}/mycert.cer**，然後取消核取 [**Embed the certificate in the WAR file**]。(如果憑證檔案名稱不同，請調整 mycert.cer。)按一下 [**完成**] 以關閉對話方塊。
2. 在部署中將憑證當作元件來複製：在 Eclipse 的專案總管中，展開 [**MyAzureACSProject**]，於 [**WorkerRole1**] 上按一下滑鼠右鍵，按一下 [**屬性**]，展開 [**Azure Role**]，然後按一下 [**元件**]。
3. 按一下 [**加入**]。
4. 在 [**加入元件**] 對話方塊內：
    1. 在 [**匯入**] 區段中：
        1. 使用 [**檔案**] 按鈕，瀏覽至您要使用的憑證。 
        2. 在 [**方法**] 中，選取 [**複製**]。
    2. 在 [**As Name**] 中，按一下文字方塊並接受預設名稱。
    3. 在 [**部署**] 區段中：
        1. 在 [**方法**] 中，選取 [**複製**]。
        2. 在 [**To directory**] 中，輸入 **%JAVA_HOME%**。
    4. 您的 [**加入元件**] 對話方塊應該看起來如下。

        ![Add certificate component][add_cert_component]

    5. 按一下 [**確定**]。

此時，您的憑證將併入部署中。請注意，無論您是否將憑證內嵌在 WAR 檔案中，或將它當作元件新增至部署，都需要依[將憑證上傳至 ACS 命名空間][]一節所述，將憑證上傳至命名空間。

[什麼是 ACS？]: #what-is
[概念]: #concepts
[必要條件]: #pre
[建立 Java Web 應用程式]: #create-java-app
[建立 ACS 命名空間]: #create-namespace
[建立命名空間]: #create-namespace
[新增身分識別提供者]: #add-IP
[新增信賴憑證者應用程式]: #add-RP
[建立規則]: #create-rules
[將憑證上傳至您的 ACS 命名空間]: #upload-certificate
[將憑證上傳至 ACS 命名空間]: #upload-certificate
[檢閱應用程式整合頁面]: #review-app-int
[設定 ACS 與 ASP.NET Web 應用程式之間的信任]: #config-trust
[將 ACS 篩選器程式庫新增至您的應用程式]: #add_acs_filter_library
[部署至計算模擬器]: #deploy_compute_emulator
[部署至 Azure]: #deploy_azure
[後續步驟]: #next_steps
[專案網站]: http://wastarterkit4java.codeplex.com/releases/view/61026
[如何檢視 Azure 存取控制服務傳回的 SAML]: /zh-tw/develop/java/how-to-guides/view-saml-returned-by-acs/
[存取控制服務 2.0]: http://go.microsoft.com/fwlink/?LinkID=212360
[Windows Identity Foundation]: http://www.microsoft.com/download/en/details.aspx?id=17331
[Windows Identity Foundation SDK]: http://www.microsoft.com/download/en/details.aspx?id=4451
[Azure 管理入口網站]: https://manage.windowsazure.com
[acs_flow]: ./media/active-directory-java-authenticate-users-access-control-eclipse/ACSFlow.png

<!-- Eclipse-specific -->
[add_acs_filter_lib]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddACSFilterLibrary.png
[add_acs_filter_lib_emulator]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddACSFilterLibraryEmulator.png
[add_acs_filter_lib_production]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddACSFilterLibraryProduction.png

[relying_party_realm_emulator]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyRealmEmulator.png
[relying_party_return_url_emulator]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyReturnURLEmulator.png
[relying_party_realm_production]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyRealmProduction.png
[relying_party_return_url_production]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyReturnURLProduction.png
[add_cert_component]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddCertificateComponent.png
[add_jsp_file_acs]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddJSPFileACS.png
[create_acs_hello_world]: ./media/active-directory-java-authenticate-users-access-control-eclipse/CreateACSHelloWorld.png
[add_token_signing_cert]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddTokenSigningCertificate.png
