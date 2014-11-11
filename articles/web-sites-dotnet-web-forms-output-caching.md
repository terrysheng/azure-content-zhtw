<properties linkid="video-center-detail" urlDisplayName="details" pageTitle="Video Center Details" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="How to Use ASP.NET Web Forms Output Caching with Azure Websites" authors="sdanie" solutions="" manager="" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="sdanie" />

# 如何將 ASP.NET Web Form 輸出快取與 Azure 網站搭配使用

本主題說明如何使用 Azure 快取服務 (預覽) 來支援 ASP.NET Web Form 的 ASP.NET 頁面輸出快取。頁面輸出快取是一項最佳化作業，可直接傳回特定期間的快取轉譯頁面。在頁面存取頻率高於其往常變更頻率的情況下，這項功能特別有用。請特別注意，ASP.NET MVC 應用程式不支援頁面輸出快取。

快取服務 (預覽) 在網站外部提供了分散式快取服務。如此一來，網站的所有執行個體即可存取頁面的相同快取轉譯。

以下是使用快取服務 (預覽) 以因應頁面輸出快取需要的基本步驟：

-   [建立快取。][建立快取。]
-   [設定 ASP.NET 專案使用 Azure 快取。][設定 ASP.NET 專案使用 Azure 快取。]
-   [修改 web.config 檔案。][修改 web.config 檔案。]
-   [使用輸出快取暫時傳回頁面的快取版本。][使用輸出快取暫時傳回頁面的快取版本。]

## <span id="createcache"></span></a>建立快取

受管理的快取服務中的快取執行個體會使用 PowerShell Cmdlet 來建立。

> 一旦使用 PowerShell Cmdlet 來建立受管理的快取服務執行個體，就能在 [Azure 管理入口網站][Azure 管理入口網站]中檢視這些執行個體。

若要建立受管理的快取服務執行個體，請開啟 Azure PowerShell 命令視窗。

> 如需安裝和使用 Azure PowerShell 的指示，請參閱[如何安裝和設定 Azure PowerShell][如何安裝和設定 Azure PowerShell]。

叫用 [Add-AzureAccount][Add-AzureAccount] Cmdlet，然後輸入與您帳戶相關聯的電子郵件地址和密碼。預設會選擇某一個訂閱並在您叫用 [Add-AzureAccount][Add-AzureAccount] Cmdlet 之後顯示。若要變更該訂閱，請叫用 [Select-AzureSubscription][Select-AzureSubscription] Cmdlet。

> 如果您已使用帳戶的憑證來設定 Azure PowerShell，那麼就可以跳過此步驟。如需將 Azure PowerShell 與 Azure 帳戶連接的詳細資訊，請參閱[如何安裝和設定 Azure PowerShell][如何安裝和設定 Azure PowerShell]。

預設會選擇並顯示某一個訂閱。若要變更該訂閱，請叫用 [Select-AzureSubscription][Select-AzureSubscription] Cmdlet。

叫用 [New-AzureManagedCache][New-AzureManagedCache] Cmdlet，然後指定快取的名稱、地區、快取提供項目及大小。

針對 [名稱]，請輸入要用於快取端點的子網域名稱。端點必須是介於 6 到 20 個字元之間的字串、僅包含小寫數字和字母，而且必須以字母開頭。

針對 [位置]，請指定快取的地區。為獲得最佳效能，請在與快取用戶端應用程式相同的區域中建立快取。

快取大小是由 [SKU] 和 [記憶體] 共同決定。受管理的快取服務共有下列三個階層：

-   基本 - 大小從 128MB 到 1GB 的快取 (每次增量 128MB)，具有一個預設具名快取
-   標準 - 大小從 1GB 到 10GB 的快取 (每次增量 1GB)，支援通知和最多 10 個具名快取
-   高級 - 大小從 5GB 到 150GB 的快取 (每次增量 5GB)，支援通知、高可用性和最多 10 個具名快取

請選擇符合應用程式需求的 [SKU] 和 [記憶體]。請注意，有些快取功能 (例如通知和高可用性) 只有特定快取提供項目才會提供。如需選擇應用程式最適用的快取提供項目和大小的詳細資訊，請參閱 [快取提供項目][快取提供項目]。

在下列範例中，基本 128MB 快取是透過位於美國中南部地理區域、名稱為 contosocache 所建立。

    New-AzureManagedCache -Name contosocache -Location "South Central US" -Sku Basic -Memory 128MB

> 如需在建立快取時可使用的完整參數和值清單，請參閱 [New-AzureManagedCache][New-AzureManagedCache] Cmdlet 文件。

一旦叫用 PowerShell Cmdlet，就會在幾分鐘內建立快取。建立該項快取之後，您的新快取不僅有 `Running` 狀態、可用於預設設定，還可在 [Azure 管理入口網站][Azure 管理入口網站]中檢視和設定。

您可以在 Azure PowerShell 視窗中監視建立進度。一旦快取可供使用，[New-AzureManagedCache][New-AzureManagedCache] Cmdlet 將會顯示快取資訊，如下列範例所示。

    PS C:\> Add-AzureAccount
    VERBOSE: Account "user@domain.com" has been added.
    VERBOSE: Subscription "MySubscription" is selected as the default subscription.
    VERBOSE: To view all the subscriptions, please use Get-AzureSubscription.
    VERBOSE: To switch to a different subscription, please use Select-AzureSubscription.
    PS C:\> New-AzureManagedCache -Name contosocache -Location "South Central US" -Sku Basic -Memory 128MB
    VERBOSE: Intializing parameters...
    VERBOSE: Creating prerequisites...
    VERBOSE: Verify cache service name...
    VERBOSE: Creating cache service...
    VERBOSE: Waiting for cache service to be in ready state...


    Name     : contosocache
    Location : South Central US
    State    : Active
    Sku      : Basic
    Memory   : 128MB



    PS C:\>

以下章節將使用 [儀表板] 索引標籤內的設定來設定 ASP.NET 專案的快取。

## <span id="configureproject"></span></a>設定 ASP.NET 專案

1.  首先，確保您[已安裝最新的][已安裝最新的] **Azure SDK for .NET**。

2.  在 Visual Studio 中，在 [解決方案總管] 中以滑鼠右鍵按一下 ASP.NET 專案，然後選取 [管理 NuGet 封裝]。(如果您使用的是 WebMatrix，則改為按一下工具列上的 [NuGet] 按鈕)

3.  在 [線上搜尋] 編輯方塊中，輸入 **WindowsAzure.Caching**。

    ![NuGetDialog][NuGetDialog]

4.  選取 [Azure 快取] 封裝，然後按一下 [安裝] 按鈕。

## <span id="configurewebconfig"></span></a>修改 Web.Config 檔案

除了對快取進行組件參考之外，NuGet 封裝還能在 web.config 檔案中加入虛設常式項目。若要在 ASP.NET 頁面輸出快取中使用快取，必須對 web.config 進行多項修改。

1.  針對 ASP.NET 專案開啟 **web.config** 檔案。

2.  如果您有現有的 **caching** 和 **outputCache** 元素，請為其加上註解 (或加以移除)。

3.  接著取消由 Azure 快取 NuGet 封裝所新增的 **caching** 元素註解。最後結果應該會類似下列螢幕擷取畫面。

    ![OutputConfig][OutputConfig]

4.  接下來，找到 **dataCacheClients** 區段。取消 **securityProperties** 子系元素註解。

    ![CacheConfig][CacheConfig]

5.  在 **autoDiscover** 元素中，將 **identifier** 屬性設定到您的快取端點 URL 中。若要找到您的端點 URL，請前往 Azure 管理入口網站中的快取屬性。在 [儀表板] 索引標籤中，複製 [quick glance] 區段中的 **ENDPOINT URL** 值。

    ![EndpointURL][EndpointURL]

6.  在 **messageSecurity** 元素中，將 **authorizationInfo** 屬性設定到您的快取存取金鑰中。若要尋找存取金鑰，請在 Azure 管理入口網站中選取您的快取。然後按一下下方列上的 [管理金鑰] 圖示。按一下 [主要存取金鑰] 文字方塊旁的複製按鈕。

    ![ManageKeys][ManageKeys]

## <span id="useoutputcaching"></span></a>使用輸出快取

最後一個步驟是在 ASP.NET Web Form 應用程式中設定頁面以使用輸出快取。將 **OutputCache** 屬性新增至 .ASPX 原始碼的開頭，即可完成此動作。例如：

    <%@ OutputCache Duration="45" VaryByParam="*" %>

上一個範例會快取頁面長達 45 秒。因為 **VaryByParam** 已設定為 "\*"，所以即使傳入不同的查詢參數，此快取頁面輸出也不會改變。但是下列範例會針對 "UserId" 參數的每個值，快取不同版本的頁面：

    <%@ OutputCache Duration="45" VaryByParam="UserId" %>   

  [建立快取。]: #createcache
  [設定 ASP.NET 專案使用 Azure 快取。]: #configureproject
  [修改 web.config 檔案。]: #configurewebconfig
  [使用輸出快取暫時傳回頁面的快取版本。]: #useoutputcaching
  [Azure 管理入口網站]: https://manage.windowsazure.com/
  [如何安裝和設定 Azure PowerShell]: http://go.microsoft.com/fwlink/?LinkId=400494
  [Add-AzureAccount]: http://msdn.microsoft.com/zh-TW/library/dn495128.aspx
  [Select-AzureSubscription]: http://msdn.microsoft.com/zh-TW/library/dn495203.aspx
  [New-AzureManagedCache]: http://go.microsoft.com/fwlink/?LinkId=400495
  [已安裝最新的]: http://www.windowsazure.com/zh-TW/downloads/?sdk=net
  [NuGetDialog]: ./media/web-sites-web-forms-output-caching/CachingScreenshot_NuGet.PNG
  [OutputConfig]: ./media/web-sites-web-forms-output-caching/CachingScreenshot_OC_WebConfig.PNG
  [CacheConfig]: ./media/web-sites-web-forms-output-caching/CachingScreenshot_CacheConfig.PNG
  [EndpointURL]: ./media/web-sites-web-forms-output-caching/CachingScreenshot_EndpointURL.PNG
  [ManageKeys]: ./media/web-sites-web-forms-output-caching/CachingScreenshot_ManageAccessKeys.PNG
