<properties linkid="video-center-detail" urlDisplayName="details" pageTitle="Video Center Details" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="How to Use ASP.NET Web Forms Output Caching with Azure Web Sites" authors="jroth" solutions="" manager="" editor="" />

如何將 ASP.NET Web Form 輸出快取用於 Azure 網站
===============================================

本主題說明如何使用 Azure 快取服務 (預覽) 來支援 ASP.NET Web Form 的 ASP.NET 頁面輸出快取。頁面輸出快取是一項最佳化作業，可直接傳回特定期間的快取轉譯頁面。在頁面存取頻率高於其往常變更頻率的情況下，這項功能特別有用。請特別注意，ASP.NET MVC 應用程式不支援頁面輸出快取。

快取服務 (預覽) 在網站外部提供了分散式快取服務。如此一來，網站的所有執行個體即可存取頁面的相同快取轉譯。

以下是使用快取服務 (預覽) 以因應頁面輸出快取需要的基本步驟：

-   [建立快取。](#createcache)
-   [設定 ASP.NET 專案使用 Azure 快取。](#configureproject)
-   [修改 web.config 檔案。](#configurewebconfig)
-   [使用輸出快取暫時傳回頁面的快取版本。](#useoutputcaching)

建立快取
--------

1.  在 Azure 管理入口網站下方，按一下 **[新增]** 圖示。

    ![NewIcon](./media/web-sites-web-forms-output-caching/CacheScreenshot_NewButton.PNG)

2.  依序選取 **[資料服務]**、**[快取]**，然後按一下 **[快速建立]**。

    ![NewCacheDialog](./media/web-sites-web-forms-output-caching/CachingScreenshot_CreateOptions.PNG)

3.  在 **[端點]** 文字方塊中，輸入唯一的快取名稱。然後為其他快取屬性選取適當的值，並按一下 **[建立新快取]**。

4.  在管理入口網站中選取 **[快取]** 圖示，以檢視所有的快取服務端點。

    ![CacheIcon](./media/web-sites-web-forms-output-caching/CachingScreenshot_CacheIcon.PNG)

5.  您可以接著選取其中一項快取服務端點以檢視其屬性。以下章節將使用 **[儀表板]** 索引標籤內的設定來設定 ASP.NET 專案的快取。

設定 ASP.NET 專案
-----------------

1.  首先，確保您[已安裝最新的](http://www.windowsazure.com/en-us/downloads/?sdk=net) **Azure SDK for .NET**。

2.  在 Visual Studio 中，在 **[解決方案總管]** 中以滑鼠右鍵按一下 ASP.NET 專案，然後選取 **[管理 NuGet 封裝]**。(如果您使用的是 WebMatrix，則改為按一下工具列上的 **[NuGet]** 按鈕)

3.  在 **[線上搜尋]** 編輯方塊中，輸入 **WindowsAzure.Caching**。

    ![NuGetDialog](./media/web-sites-web-forms-output-caching/CachingScreenshot_NuGet.PNG)

4.  選取 **[Azure 快取]** 封裝，然後按一下 **[安裝]** 按鈕。

修改 Web.Config 檔案
--------------------

除了對快取進行組件參考之外，NuGet 封裝還能在 web.config 檔案中加入虛設常式項目。若要在 ASP.NET 頁面輸出快取中使用快取，必須對 web.config 進行多項修改。

1.  針對 ASP.NET 專案開啟 **web.config** 檔案。

2.  如果您有現有的 **caching** 和 **outputCache** 元素，請為其加上註解 (或加以移除)。

3.  接著取消由 Azure 快取 NuGet 封裝所新增的 **caching** 元素註解。最後結果應該會類似下列螢幕擷取畫面。

    ![OutputConfig](./media/web-sites-web-forms-output-caching/CachingScreenshot_OC_WebConfig.PNG)

4.  接下來，找到 **dataCacheClients** 區段。取消 **securityProperties** 子系元素註解。

    ![CacheConfig](./media/web-sites-web-forms-output-caching/CachingScreenshot_CacheConfig.PNG)

5.  在 **autoDiscover** 元素中，將 **identifier** 屬性設定到您的快取端點 URL 中。若要找到您的端點 URL，請前往 Azure 管理入口網站中的快取屬性。在 **[儀表板]** 索引標籤中，複製 **[quick glance]** 區段中的 **ENDPOINT URL** 值。

    ![EndpointURL](./media/web-sites-web-forms-output-caching/CachingScreenshot_EndpointURL.PNG)

6.  在 **messageSecurity** 元素中，將 **authorizationInfo** 屬性設定到您的快取存取金鑰中。若要尋找存取金鑰，請在 Azure 管理入口網站中選取您的快取。然後按一下下方列上的 **[管理金鑰]** 圖示。按一下 **[主要存取金鑰]** 文字方塊旁的複製按鈕。

    ![ManageKeys](./media/web-sites-web-forms-output-caching/CachingScreenshot_ManageAccessKeys.PNG)

使用輸出快取
------------

最後一個步驟是在 ASP.NET Web Form 應用程式中設定頁面以使用輸出快取。將 **OutputCache** 屬性新增至 .ASPX 原始碼的開頭，即可完成此動作。例如：

    <%@ OutputCache Duration="45" VaryByParam="*" %>

上一個範例會快取頁面長達 45 秒。因為 **VaryByParam** 已設定為 "\*"，所以即使傳入不同的查詢參數，此快取頁面輸出也不會改變。但是下列範例會針對 "UserId" 參數的每個值，快取不同版本的頁面：

    <%@ OutputCache Duration="45" VaryByParam="UserId" %>  
