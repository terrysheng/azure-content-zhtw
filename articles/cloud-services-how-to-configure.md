<properties linkid="manage-services-how-to-configure-a-cloud-service" urlDisplayName="How to configure" pageTitle="How to configure a cloud service - Azure" metaKeywords="Configuring cloud services" description="Learn how to configure cloud services in Azure. Learn to update the cloud service configuration and configure remote access to role instances." metaCanonical="" services="cloud-services" documentationCenter="" title="How to Configure Cloud Services" authors="davidmu" solutions="" manager="" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="davidmu" />

# <span id="configurecloudservice"></span></a>如何設定雲端服務

[WACOM.INCLUDE [disclaimer](../includes/disclaimer.md)]

您可以在 Azure 管理入口網站中設定雲端服務的最常用設定。或者，如果您想要直接更新組態檔，可以下載要更新的服務組態檔、上傳更新過的檔案，然後將雲端服務更新為使用這些組態變更。使用上述任一種方式，都會將組態更新推送到所有角色執行個體。

您也可以對雲端服務中執行的一個或所有角色啟用遠端桌面連線。遠端桌面可讓您存取執行中應用程式的桌面，然後疑難排解和診斷問題。即使您未在應用程式開發期間對服務定義檔 (.csdef) 設定遠端桌面，還是可以對您的角色啟用遠端桌面連線。您不需要重新部署應用程式，就可以啟用遠端桌面連線。

要讓 Azure 確保服務在組態更新期間有 99.95% 的可用性，每個角色都至少必須有兩個角色執行個體 (虛擬機器)。如此才能讓一個虛擬機器在受到更新時，還有另一個虛擬機器可以處理用戶端要求。如需詳細資訊，請參閱[服務等級協定][服務等級協定]。

## 目錄

-   [作法：更新雲端服務組態][作法：更新雲端服務組態]
-   [作法：設定對角色執行個體的遠端存取][作法：設定對角色執行個體的遠端存取]

## <span id="update"></span></a>作法：更新雲端服務組態

1.  在 [Azure 管理入口網站][Azure 管理入口網站]中，按一下 [雲端服務]。然後按一下雲端服務的名稱以開啟儀表板。

2.  按一下 [設定]。

    在 [設定] 頁面上，您可以設定監視、更新角色設定，以及選擇角色執行個體 (虛擬機器) 的客體作業系統和系列。

    ![Configuration Page][Configuration Page]

3.  在監視設定中，將監視層級設定為 [詳細資訊] 或 [最小]，並設定進行詳細資訊監視時所需的診斷連接字串。如需指示，請參閱[如何監視雲端服務][如何監視雲端服務]。

4.  針對服務角色 (依角色分組)，您可以更新下列設定：

    > -   **設定** 修改服務組態檔 (.cscfg) 之 *ConfigurationSettings* 元素中所指定的其他組態設定值。

    > -   **憑證** 變更要在角色之 SSL 加密中使用的憑證指紋。若要變更憑證，您必須先上傳新的憑證 (在 [憑證] 頁面上)。然後，更新角色設定中所顯示憑證字串中的指紋。

5.  在 [作業系統設定] 中，您可以變更角色執行個體 (虛擬機器) 的作業系統系列或版本，或選擇 [自動] 恢復自動更新目前的作業系統版本。作業系統設定會套用於 Web 角色和背景工作角色，但是不會影響先前的 Azure 管理入口網站中已新增至代管服務的 VM 角色。

    當您部署新的雲端服務時，可以選擇 Windows Server 2008 R2、Windows Server 2008 Service Pack 2 (SP2) 或 Windows Server 2012 作業系統。部署期間會在所有角色執行個體上安裝最新作業系統版本，而且預設會自動更新作業系統。

    如果您因為程式碼中的相容性需求而需要在不同的作業系統版本上執行雲端服務，則可以選擇作業系統系列和版本。當您選擇特定作業系統版本時，雲端服務的自動作業系統更新會暫停。您需要確保作業系統收到更新。

    如果您解決了應用程式對最新作業系統版本的所有相容性問題，則可以將作業系統版本設定為 [自動] 恢復自動更新作業系統。

    ![OS Settings][OS Settings]

6.  若要儲存組態設定，並將之推送到角色執行個體中，請按一下 [儲存]。(按一下 [捨棄] 可取消變更。) 在您變更設定之後，命令列中即會新增 [儲存] 和 [捨棄]。

### 手動更新雲端服務組態檔

1.  下載含有最新組態的雲端服務組態檔 (.cscfg)。在雲端服務的 [設定] 頁面上，按 [下載]。然後按一下 [儲存]，或按一下 [另存新檔] 儲存檔案。

2.  在您更新服務組態檔之後，請上傳和套用組態更新：

    a. 在 [設定] 頁面上，按 [上傳]。

    [上傳新的組態檔] 隨即開啟。

    ![Upload Configuration][Upload Configuration]

    b. 在 [組態檔] 中，使用 [瀏覽] 選取更新過的 .cscfg 檔案。

    c. 如果您的雲端服務包含任何只有一個執行個體的角色，請選取 [Apply configuration even if one or more roles contain a single instance] 核取方塊，讓角色的組態更新能夠繼續。

    除非每個角色都定義至少兩個執行個體，否則 Azure 無法保證雲端服務在服務組態更新期間至少有 99.95% 的可用性。如需詳細資訊，請參閱[服務等級協定][1]。

    d. 按一下 [確定] (打勾記號)。

## <span id="remoteaccess"></span></a>作法：設定對角色執行個體的遠端存取

遠端桌面可讓您存取 Azure 內執行中角色的桌面。您可以使用遠端桌面連線來疑難排解和診斷執行中應用程式的問題。您可以在應用程式設計期間或在將應用程式部署至 Azure (角色執行時) 之後，於角色中啟用遠端桌面連線。若要透過管理入口網站在執行中角色內啟用遠端桌面連線，您並不需要重新部署應用程式。若要驗證遠端桌面連線，您可以使用先前上傳的憑證，也可以建立新的憑證。

在雲端服務的 [設定] 頁面上，您可以啟用遠端桌面，或者變更用來與虛擬機器連線的本機 Administrator 帳戶或密碼、驗證中使用的憑證，或是到期日。

<div class="dev-callout"> 
<b>注意</b> 
<p>如果您的雲端服務是由兩個以上已連線的 Windows Server 型虛擬機器所組成，則您不需要設定遠端存取，因為這些虛擬機器已自動設定成使用遠端桌面。</p> 
</div>

### 在服務定義檔中設定遠端存取

在服務定義檔 (.csdef) 中新增 **Import** 元素，以將 RemoteAccess 和 RemoteForwarder 模組匯入至服務模型。當這些模組存在時，Azure 會將遠端桌面的組態設定新增至服務組態檔。若要完成遠端桌面的設定，您需要將憑證匯入至 Azure，並在服務組態檔中指定該憑證。如需詳細資訊，請參閱[在 Azure 中設定角色的遠端桌面連線][在 Azure 中設定角色的遠端桌面連線] (英文)。

### 在管理入口網站中啟用或修改對角色執行個體的遠端存取

1.  登入[管理入口網站][Azure 管理入口網站]，然後按一下 [雲端服務]。然後按一下雲端服務的名稱以開啟儀表板。

2.  開啟雲端服務的 [設定] 頁面，然後按一下 [遠端]。

    [設定遠端桌面] 會顯示當初部署雲端服務時新增至服務組態檔的設定 (如果有的話)，如下圖所示。

    ![Cloud services remote][Cloud services remote]

> [WACOM.NOTE]
> **警告：**當您首次啟用遠端桌面並按一下 [確定] (打勾記號) 時，所有角色執行個體都會重新啟動。若要防止重新啟動，角色上必須安裝用來將密碼加密的憑證。如果未安裝憑證，則您會看到下列選項：
> ![CloudServices\_CreateNewCertDropDown][CloudServices\_CreateNewCertDropDown]

    To prevent a restart, install a certificate and then return to this dialog (see [Using Remote Desktop with Azure Roles][Using Remote Desktop with Azure Roles] for more information). If you choose an existing certificate, then a configuration update will be sent to all the instances in the role.

1.  在 [角色] 中，選取想要更新的服務角色，或選取 [全部] 代表所有角色。

2.  進行下列任一變更：

-   若要啟用遠端桌面，請選取 [啟用遠端桌面] 核取方塊。若要停用遠端桌面，請清除此核取方塊。

-   建立用來對角色執行個體進行遠端桌面連線的帳戶。

-   更新現有帳戶的密碼。

-   選取要用於驗證的已上傳憑證 (使用 [憑證] 頁面上的 [上傳] 來上傳憑證)，或建立新的憑證。

-   變更遠端桌面組態的到期日。

1.  完成組態更新時，按一下 [確定] (打勾記號)。

2.  連線到角色執行個體：

    a. 按一下 [執行個體] 以開啟 [執行個體] 頁面。

    b. 選取已設定遠端桌面的角色執行個體。

    c. 按一下 [連線]，然後依照指示開啟虛擬機器的桌面。

    d. 按一下 [開啟]，然後按一下 [連線] 以啟動遠端桌面連線。

### 在管理入口網站中停用對角色執行個體的遠端存取

1.  登入[管理入口網站][Azure 管理入口網站]，然後按一下 [雲端服務]。然後按一下雲端服務的名稱以開啟儀表板。

2.  開啟雲端服務的 [設定] 頁面，然後按一下 [遠端]。

3.  在 [角色] 中，選取想要更新的服務角色，或選取 [全部] 代表所有角色。

4.  取消核取 (或清除) [啟用遠端桌面] 核取方塊。

5.  按一下 [確定] (核取記號)。

  [服務等級協定]: https://www.windowsazure.com/zh-tw/support/legal/sla/
  [作法：更新雲端服務組態]: #update
  [作法：設定對角色執行個體的遠端存取]: #remoteaccess
  [Azure 管理入口網站]: http://manage.windowsazure.com/
  [Configuration Page]: ./media/cloud-services-how-to-configure/CloudServices_ConfigurePage1.png
  [如何監視雲端服務]: ../how-to-monitor-a-cloud-service/
  [OS Settings]: ./media/cloud-services-how-to-configure/CloudServices_ConfigurePage_OSSettings.png
  [Upload Configuration]: ./media/cloud-services-how-to-configure/CloudServices_UploadConfigFile.png
  [1]: http://www.windowsazure.com/zh-tw/support/legal/sla/
  [在 Azure 中設定角色的遠端桌面連線]: http://msdn.microsoft.com/zh-tw/library/windowsazure/hh124107.aspx
  [Cloud services remote]: ./media/cloud-services-how-to-configure/CloudServices_Remote.png
