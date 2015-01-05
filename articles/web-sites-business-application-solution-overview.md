<properties urlDisplayName="Create a Line-of-Business Application on Azure Websites" pageTitle="在 Azure 網站建立企業營運系統應用程式" metaKeywords="Web Sites" description="This guide provides a technical overview of how to use Azure Websites to create intranet, line-of-business applications. This includes authentication strategies, service bus relay, and monitoring." umbracoNaviHide="0" disqusComments="1" editor="mollybos" manager="wpickett" title="Create a Line-of-Business Application on Azure Websites" authors="jroth" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/01/2014" ms.author="jroth" />



# 在 Azure 網站建立企業營運系統應用程式

本指南提供如何使用 Azure 網站來建立企業營運系統應用程式的技術概觀。為因應本文件的目的，將假設這些應用程式為內部網路應用程式，應該受保護以供內部業務使用。商務應用程式有兩個不同的特性。這些應用程式需要驗證，一般是針對企業目錄。而且它們通常需要與內部部署資料和服務進行一些存取和整合。本指南著重於在 [Azure 網站][websitesoverview]上建置商務應用程式。不過，在某些情況下，[Azure 雲端服務][csoverview]或 [Azure 虛擬機器][vmoverview]會較符合您的需求。檢閱下列主題中這些選項之間的差異相當重要：[Azure 網站、雲端服務及 VM：使用時機][chooseservice]. 

本指南涵蓋下列領域：

- [考量優點](#benefits)
- [選擇驗證策略](#authentication)
- [建立支援驗證的 Azure 網站](#createintranetsite)
- [使用服務匯流排與內部部署資源整合](#servicebusrelay)
- [監視應用程式](#monitor)

<div class="dev-callout">
<strong>注意</strong>
<p>本指南呈現一些與公用端 .COM 網站開發整合的最常見領域和工作。不過，還有其他 Azure 網站功能可供您在特定的實作中使用。若要檢閱這些功能，另請參閱 <a href="http://www.windowsazure.com/zh-tw/manage/services/web-sites/global-web-presence-solution-overview/">全域網站空間</a> 和 <a href="http://www.windowsazure.com/zh-tw/manage/services/web-sites/digital-marketing-campaign-solution-overview">數位行銷促銷活動</a>。</p>
</div>

##<a name="benefits"></a>考量優點
由於企業營運系統應用程式一般是以公司使用者為目標，因此您應該考量使用雲端之理由與使用內部部署公司資源及基礎結構之理由的比較。首先，移到雲端有一些典型的優點，例如能夠隨著動態工作負載來相應擴充或縮減規模。例如，考量有一個處理年度績效審查的應用程式。在一年中大部分的時候，這類應用程式要處理的流量都非常少。但是在審查期間，大公司的流量就會激增到高峰層級。Azure 提供調整選項，可讓公司一方面在高流量審查期間相應擴充規模以處理負載，另一方面又可在一年中其餘的時候調整回來以節省開支。雲端的另一項優點就是能夠較著重於應用程式開發，而較無須費心留意基礎結構的取得和管理。

除了這些標準的優點之外，將商務應用程式放在雲端也為員工和合作夥伴在從任何位置使用應用程式方面提供更大的支援。使用者不須連線到公司網路，即可使用應用程式，而 IT 群組也可避免使用複雜的反向 Proxy 方案。有數個驗證選項可以確保公司應用程式的存取受到保護。這些選項將於本指南的下列各節中討論。

##<a name="authentication"></a>選擇驗證策略
對於商務應用程式案例來說，您的驗證策略會是其中一項最重要的決定。有數個選項：

- 使用 [Azure Active Directory 服務][adoverview]。您可以使用這個做為獨立目錄，或是將它與內部部署的 Active Directory 同步處理。應用程式會接著與 Azure Active Directory 互動來驗證使用者。如需這個方法的概觀，請參閱[使用 Azure Active Directory][adusing]。
- 使用 Azure 虛擬機器和虛擬網路來安裝 Active Directory。這可提供您將內部部署 Active Directory 安裝延伸到雲端的選項。您也可以選擇使用 Active Directory Federation Services (ADFS) 將身分識別要求透過同盟傳回到內部部署 AD 處理。Azure 應用程式的驗證會透過 ADFS 傳送給內部部署 Active Directory。如需有關這個方法的詳細資訊，請參閱[在 VM 中執行 Windows Server Active Directory][adwithvm] 和[在 Azure 虛擬機器中部署 Windows Server Active Directory 的指導方針][addeployguidelines]。 
- 使用中繼服務 (例如 [Azure 存取控制服務][acs2] (ACS)) 以使用多個身分識別服務來驗證使用者。這提供一個透過 Active Directory 或透過其他身分識別提供者進行驗證的抽象概念。如需詳細資訊，請參閱[使用 Azure Active Directory 存取控制][acsusing]。

對於此商務應用程式案例來說，第一個使用 Azure Active Directory 的案例為您的應用程式提供了實作驗證策略的最快途徑。本指南的其餘部分著重於 Azure Active Directory。不過，視您的業務需求而定，您可能會發現其他兩個方案的其中之一較為適用。例如，若允許您將身分識別資訊同步至雲端，則 ADFS 方案可能是較理想的選項。或者，如果您必須支援其他身分識別提供者 (例如 Facebook)，則 ACS 方案較為適用。

在您開始設定新的 Azure Active Directory 之前，您應該注意到 Office 365 或 Windows Intune 等現有服務已經使用 Azure Active Directory。在該情況下，您需將現有的訂用帳戶與 Azure 訂用帳戶建立關聯。如需詳細資訊，請參閱 [Azure AD 租用戶是什麼？][adtenant]。

如果您目前還沒有任何已經使用 Azure Active Directory 的服務，您可以在管理入口網站中建立一個新目錄。使用管理入口網站的 [**Active Directory**] 區段建立新目錄。

![BusinessApplicationsAzureAD][BusinessApplicationsAzureAD]

建立完成後，您可以選擇是否要建立及管理使用者、已整合的應用程式和網域。

![BusinessApplicationsADUsers][BusinessApplicationsADUsers]

如需這些步驟的完整逐步解說，請參閱[使用 Azure AD 為 Web 應用程式新增登入][adsso]。如果您將這個新目錄當做獨立資源來使用，則下一步就是開發與這個目錄整合的應用程式。不過，如果您具有內部部署 Active Directory 身分識別，您通常會想要將那些身分識別與新的 Azure Active Directory 同步處理。如需如何進行這項作業的詳細資訊，請參閱[目錄整合][dirintegration]。

建立並填入目錄之後，您必須建立會要求驗證的 Web 應用程式，然後將它們與目錄整合。這些步驟將於接下來的兩節中說明。

##<a name="createintranetsite"></a>建立支援驗證的 Azure 網站
在全域網站空間案例中，我們討論了各種建立及部署新網站的選項。如果您是 Azure 網站新手，建議您[檢閱該文件][scenarioglobalweb]。Visual Studio 中的 ASP.NET 應用程式是使用 Windows 驗證之內部網路 Web 應用程式的常用選項。這樣選擇的其中一個原因，就是它能夠緊密整合及支援由 ASP.NET 和 Visual Studio 所提供的這個案例。

例如，在 Visual Studio 中建立 ASP.NET MVC 4 專案時，您可以在專案建立對話方案中選擇建立 [**內部網路應用程式**]。

![BusinessApplicationsVSIntranetApp][BusinessApplicationsVSIntranetApp]

這會變更專案設定以支援 Windows 驗證。明確而言，web.config 檔案之 **authentication** 元素的 **mode** 屬性會設為 **Windows**。如果您建立不同的 ASP.NET 專案 (例如 Web Form 專案)，或是您使用現有的專案，就必須手動進行這項變更。

如果是 MVC 專案，您還必須在專案屬性視窗中一併變更兩個值。將 [**Windows 驗證**] 設為 [**啟用**]，並將 [**匿名驗證**] 設為 [**停用**]。

![BusinessApplicationsVSProperties][BusinessApplicationsVSProperties]

為了以 Azure Active Directory 進行驗證，您必須向目錄登錄此應用程式，然後必須修改該應用程式組態以進行連線。這可以在 Visual Studio 中以兩種方法完成：

- [適用於 Visual Studio 的身分識別與存取工具](#identityandaccessforvs)
- [適用於 Azure Active Directory 的 Microsoft ASP.NET 工具](#aspnettoolsforwaad)

###<a name="identityandaccessforvs"></a>適用於 Visual Studio 的身分識別與存取工具：
其中一個方法是使用[身分識別與存取工具][identityandaccess] (可供下載並安裝)。這個工具會在專案操作功能表與 Visual Studio 整合。下列指示和螢幕擷取畫面來自於 Visual Studio 2012。以滑鼠右鍵按一下專案，然後選取 [**身分識別與存取**]。有三個必須設定的項目。在 [**提供者**] 索引標籤上，您必須提供 **SAS 中繼資料文件的路徑**和 **APP ID URI** (若要取得這些值，請參閱[在 Azure Active Directory 登錄應用程式]一節)(#registerwaadapp)。

![BusinessApplicationsVSIdentityAndAccess][BusinessApplicationsVSIdentityAndAccess]

最後必須進行的組態變更是在 [**身分識別和存取**] 對話方塊的 [**組態**] 索引標籤中執行。您必須選取 [**Enable web farm cookies**] 核取方塊。如需這些步驟的詳細逐步解說，請參閱[使用 Azure AD 為 Web 應用程式新增登入][adsso]。

####<a name="registerwaadapp"></a>在 Azure Active Directory 中登錄應用程式：
為了填寫 [**提供者**] 索引標籤，您必須向 Azure Active Directory 登錄您的應用程式。在 Azure 管理入口網站的 [**Active Directory**] 區段選取您的目錄，然後移至 [**應用程式**] 索引標籤。這可讓您選擇依 URL 新增 Azure 網站。請注意，逐步進行這些步驟時，您一開始會將 URL 設定為針對在 Visual Studio 進行本機偵錯而提供的 localhost 位址。之後在部署時，您會將這項設定變更為您網站的實際 URL。

![BusinessApplicationsADIntegratedApps][BusinessApplicationsADIntegratedApps]

新增之後，入口網站就會提供 STS 中繼資料文件路徑 (即**同盟中繼資料文件 URL**) 和 **APP ID URI**。這些值會在 Visual Studio 中用於 [**[身分識別和存取**] 對話方塊的 [**提供者**] 索引標籤上。 

![BusinessApplicationsADAppAdded][BusinessApplicationsADAppAdded]

###<a name="aspnettoolsforwaad"></a>適用於 Azure Active Directory 的 Microsoft ASP.NET 工具：
完成前述步驟的替代方法是使用[適用於 Azure Active Directory 的 Microsoft ASP.NET 工具][aspnettools]。若要使用這個方法，請從 Visual Studio 中的 [**專案**] 功能表按一下 [**啟用 Azure 驗證**] 項目。這會顯示一個要求輸入您 Azure Active Directory 網域 (非應用程式的 URL) 的簡單對話方塊。

![BusinessApplicationsVSEnableAuth][BusinessApplicationsVSEnableAuth]

如果您是 Active Directory 網域的系統管理員，請選取 [**Provision this application in the Azure AD**] 核取方塊。這會執行向 Active Directory 登錄應用程式的工作。如果您不是系統管理員，則取消核取該方塊，並將顯示的資訊提供給系統管理員。該系統管理員可以使用管理入口網站，使用先前在「身分識別與存取」工具上的步驟建立一個整合的應用程式。如果有關如何使用適用於 Azure Active Directory 之 ASP.NET 工具的詳細步驟，請參閱 [Azure 驗證][azureauthtutorial]。

管理您的企業營運系統應用程式時，您可以使用任何支援的原始程式碼控制系統來進行部署。不過，由於本案例中的 Visual Studio 整合較為高階，因此您選擇的原始檔控制系統比較可能是 Team Foundation Service (TFS)。如果是這樣，您應該注意到 Azure 網站有提供與 TFS 整合的功能。在管理入口網站中，移至您網站的 [**儀表板**] 索引標籤。然後選取 [**Set up deployment from source control**]。請依照指示來使用 TFS。 

![BusinessApplicationsDeploy][BusinessApplicationsDeploy]

##<a name="servicebusrelay"></a>使用服務匯流排與內部部署資源整合
需多企業營運系統應用程式都必須與內部部署資料及服務整合。有多個原因導致無法將特定類型的資料移到雲端。這些原因可能是實務性或管制性的原因。如果您正處於決定哪些資料要裝載於 Azure 及哪些資料應保持在內部部署的規劃階段，檢閱 [Azure 信任中心][trustcenter]上的資源就相當重要。混合式 Web 應用程式可在 Azure 中執行，並可存取必須保持在內部部署的資源。

在使用虛擬機器或雲端服務時，您可以使用虛擬網路將 Azure 中的應用程式與公司網路連線。不過，網站並不支援虛擬網路，因此執行這類與網站整合的最佳方式就是透過使用 [Azure 服務匯流排轉送服務][sbrelay]。服務匯流排轉送服務可讓雲端的應用程式安全地連線至在公司網路上執行的 WCF 服務。服務匯流排可允許在不開啟防火牆連接埠的情況下進行這項通訊。 

在下圖中，雲端應用程式和內部部署 WCF 服務都是透過先前建立的命名空間與服務匯流排進行通訊。內部部署 WCF 服務能夠存取無法移至雲端的內部資料和服務。WCF 服務會在命名空間中登錄一個端點。在 Azure 中執行的網站也會連線至服務匯流排中的這個端點。它們只要能夠發出連出的公用 HTTP 要求，即可完成這個步驟。

![BusinessApplicationsServiceBusRelay][BusinessApplicationsServiceBusRelay]

服務匯流排會接著將雲端應用程式連線至內部部署 WCF 服務。這提供一個基本架構，可讓您建立可同時使用 Azure 上及內部部署之服務和資源的混合式應用程式。如需詳細資訊，請參閱[如何使用服務匯流排轉送服務][sbrelayhowto]和[服務匯流排轉送訊息教學課程。][sbrelaytutorial]。如需示範這項技術的範例，請參閱 [Enterprise Pizza - 使用服務匯流排將網站連線至內部部署][enterprisepizza]。

##<a name="monitor"></a>監視應用程式
商務應用程式受益於標準網站功能，例如調整和監視。對於會在特定的日子或小時經歷多變負載的商務應用程式來說，自動調整 (預覽) 功能可協助網站相應擴充或縮減規模以更有效使用資源。監視選項包括端點監視和配額監視。如需所有這些案例的詳細資訊，請參閱[全域網站空間][scenarioglobalweb]和[數位行銷活動][scenariodigitalmarketing]案例。

不同的企業營運系統應用程式對業務的重要性層級各有不同，因此監督需求也不同。對於在任務執行上較具關鍵性的應用程式，請考量投入協力廠商監視方案，例如 [New Relic][newrelic]。

企業營運系統應用程式通常是由 IT 職員管理。當發生意外的錯誤或行為時，IT 工作者可以啟用較詳細的記錄，然後分析產生的資料來判斷問題所在。在管理入口網站中，移至 [**設定**] 索引標籤，然後檢閱 [**應用程式診斷**] 和 [**網站診斷**] 區段中的選項。 

![BusinessApplicationsDiagnostics][BusinessApplicationsDiagnostics]

請使用各種應用程式記錄和網站記錄來疑難排解網站的問題。請注意，有些選項會指定 [**檔案系統**]，這會將記錄檔放在您網站的檔案系統上。透過 FTP、Azure PowerShell 或 Azure 命令列工具，即可存取這些記錄檔。其他選項會指定 [**儲存體**]。這會將資訊傳送給您指定的 Azure 儲存體帳戶。針對 [**Web 伺服器記錄**]，您還可以選擇指定檔案系統的磁碟配額，或是儲存體選項的保留原則。這可讓您避免儲存一個資料量會無限制增加的儲存記錄資料。

![BusinessApplicationsDiagRetention][BusinessApplicationsDiagRetention]

如需有關這些記錄設定的詳細資訊，請參閱[作法：設定診斷功能及下載網站記錄][configurediagnostics]。

除了透過 FTP 或儲存體公用程式 (例如 Azure 儲存體總管) 檢視原始記錄之外，您也可以在 Visual Studio 中檢視記錄資訊。如需有關使用記錄進行案例疑難排解的詳細資訊，請參閱[在 Visual Studio 中進行 Azure 網站疑難排解][troubleshootwebsites]。

##<a name="summary"></a>摘要
Azure 可讓您將安全的內部網路應用程式裝載在雲端。為了只允許貴組織的成員存取應用程式，Azure Active Directory 提供了驗證使用者的功能。服務匯流排轉送服務則提供一項機制，讓 Web 應用程式能夠與內部部署服務及資料進行通訊。這項混合式應用程式方法使得將商務應用程式發佈至雲端變得更容易，因為不需要移轉所有相依資料和服務。部署之後，商務應用程式便能利用 Azure 網站所提供的標準調整和監視功能。如需詳細資訊，請參閱下列技術文章。

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="top">領域</th>
   <th align="left" valign="top">資源</th>
</tr>
<tr>
   <td valign="middle"><strong>規劃</strong></td>
   <td valign="top">- <a href="http://www.windowsazure.com/zh-tw/manage/services/web-sites/choose-web-app-service">Azure 網站、雲端服務和 VM：使用時機</a></td>
</tr>
<tr>
   <td valign="middle"><strong>建立和部署</strong></td>
   <td valign="top">- <a href ="http://www.windowsazure.com/zh-tw/develop/net/tutorials/get-started/">將 ASP.NET Web 應用程式部署至 Azure 網站</a><br/>- <a href="http://www.windowsazure.com/zh-tw/develop/net/tutorials/web-site-with-sql-database/">將安全 ASP.NET MVC 應用程式部署至 Azure</a></td>
</tr>
<tr>
   <td valign="middle"><strong>驗證</strong></td>
   <td valign="top">- <a href ="http://www.windowsazure.com/zh-tw/manage/windows/fundamentals/identity/">了解 Azure 身分識別選項</a><br/>- <a href="http://www.windowsazure.com/zh-tw/documentation/services/active-directory/">Azure Active Directory 服務</a><br/>- <a href="http://technet.microsoft.com/zh-tw/library/jj573650.aspx">什麼是 Azure AD 租用戶？</a><br/>- <a href="http://msdn.microsoft.com/library/windowsazure/dn151790.aspx">使用 Azure AD 為您的 Web 應用程式新增登入</a><br/>- <a href="http://www.asp.net/aspnet/overview/aspnet-and-visual-studio-2012/windows-azure-authentication">Azure 驗證教學課程</a></td>
</tr>
<tr>
   <td valign="middle"><strong>Service Bus 轉送</strong></td>
   <td valign="top">- <a href="http://www.windowsazure.com/zh-tw/develop/net/how-to-guides/service-bus-relay/">如何使用服務匯流排轉送服務</a><br/>- <a href="http://msdn.microsoft.com/zh-tw/library/windowsazure/ee706736.aspx">服務匯流排轉送訊息教學課程</a></td>
</tr>
<tr>
   <td valign="middle"><strong>監視</strong></td>
   <td valign="top">- <a href ="http://www.windowsazure.com/zh-tw/manage/services/web-sites/how-to-monitor-websites/">如何監視網站</a><br/>- <a href="http://msdn.microsoft.com/library/windowsazure/dn306638.aspx">作法：在 Azure 中接收警示通知及管理警示規則</a><br/>- <a href="http://www.windowsazure.com/zh-tw/manage/services/web-sites/how-to-monitor-websites/#howtoconfigdiagnostics">作法：設定診斷功能及下載網站記錄</a><br/>- <a href="http://www.windowsazure.com/zh-tw/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/">疑難排解 Visual Studio 中的 Azure 網站</a></td>
</tr>
</table>

  [websitesoverview]:/zh-tw/documentation/services/web-sites/
  [csoverview]:/zh-tw/documentation/services/cloud-services/
  [vmoverview]:/zh-tw/documentation/services/virtual-machines/
  [chooseservice]:/zh-tw/manage/services/web-sites/choose-web-app-service
  [scenarioglobalweb]:/zh-tw/manage/services/web-sites/global-web-presence-solution-overview/
  [scenariodigitalmarketing]:/zh-tw/manage/services/web-sites/digital-marketing-campaign-solution-overview
  [adoverview]:/zh-tw/documentation/services/active-directory/
  [adusing]:/zh-tw/manage/windows/fundamentals/identity/#ad
  [adwithvm]:/zh-tw/manage/windows/fundamentals/identity/#adinvm
  [addeployguidelines]:http://msdn.microsoft.com/zh-tw/library/windowsazure/jj156090.aspx
  [acs2]:http://msdn.microsoft.com/library/windowsazure/hh147631.aspx
  [acsusing]:/zh-tw/manage/windows/fundamentals/identity/#ac
  [adtenant]:http://technet.microsoft.com/zh-tw/library/jj573650.aspx
  [adsso]:http://msdn.microsoft.com/library/windowsazure/dn151790.aspx
  [dirintegration]:http://technet.microsoft.com/zh-tw/library/jj573653.aspx
  [identityandaccess]:http://visualstudiogallery.msdn.microsoft.com/e21bf653-dfe1-4d81-b3d3-795cb104066e
  [aspnettools]:http://go.microsoft.com/fwlink/?LinkID=282306
  [azureauthtutorial]:http://www.asp.net/aspnet/overview/aspnet-and-visual-studio-2012/windows-azure-authentication
  [trustcenter]:/zh-tw/support/trust-center/
  [sbrelay]:http://msdn.microsoft.com/zh-tw/library/windowsazure/jj860549.aspx
  [sbrelayhowto]:/zh-tw/develop/net/how-to-guides/service-bus-relay/
  [sbrelaytutorial]:http://msdn.microsoft.com/zh-tw/library/windowsazure/ee706736.aspx
  [enterprisepizza]:http://code.msdn.microsoft.com/windowsazure/Enterprise-Pizza-e2d8f2fa
  [newrelic]:http://newrelic.com/azure
  [configurediagnostics]:/zh-tw/manage/services/web-sites/how-to-monitor-websites/#howtoconfigdiagnostics
  [troubleshootwebsites]:/zh-tw/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/
  [BusinessApplicationsAzureAD]: ./media/web-sites-business-application-solution-overview/BusinessApplications_AzureAD.png
  [BusinessApplicationsADUsers]: ./media/web-sites-business-application-solution-overview/BusinessApplications_AD_Users.png
  [BusinessApplicationsVSIntranetApp]: ./media/web-sites-business-application-solution-overview/BusinessApplications_VS_IntranetApp.png
  [BusinessApplicationsVSProperties]: ./media/web-sites-business-application-solution-overview/BusinessApplications_VS_Properties.png
  [BusinessApplicationsVSIdentityAndAccess]: ./media/web-sites-business-application-solution-overview/BusinessApplications_VS_IdentityAndAccess.png
  [BusinessApplicationsADIntegratedApps]: ./media/web-sites-business-application-solution-overview/BusinessApplications_AD_IntegratedApps.png
  [BusinessApplicationsADAppAdded]: ./media/web-sites-business-application-solution-overview/BusinessApplications_AD_AppAdded.png
  [BusinessApplicationsVSEnableAuth]: ./media/web-sites-business-application-solution-overview/BusinessApplications_VS_EnableAuth.png
  [BusinessApplicationsDeploy]: ./media/web-sites-business-application-solution-overview/BusinessApplications_Deploy.png
  [BusinessApplicationsServiceBusRelay]: ./media/web-sites-business-application-solution-overview/BusinessApplications_ServiceBusRelay.png
  [BusinessApplicationsDiagnostics]: ./media/web-sites-business-application-solution-overview/BusinessApplications_Diagnostics.png
  [BusinessApplicationsDiagRetention]: ./media/web-sites-business-application-solution-overview/BusinessApplications_Diag_Retention.png
  
  
  
  
  
  
  
  
  
  
  
  
  
  




  




<!--HONumber=35.1-->
