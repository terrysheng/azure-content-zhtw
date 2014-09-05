<properties linkid="video-center-index" urlDisplayName="index" pageTitle="Video Center Index" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="How to Use ASP.NET Session State with Azure Web Sites" authors="jroth" solutions="" manager="" editor="" />

如何將 ASP.NET 工作階段狀態與 Azure 網站搭配使用
================================================

本主題說明如何使用 Azure 快取服務 (預覽) 來支援 ASP.NET 工作階段狀態快取。

在沒有外部提供者的狀況下，工作階段狀態會存放在託管該網站的 Web 伺服器處理序中。對 Azure 網站而言，處理序內的工作階段狀態有兩個問題。首先，對於具有多個執行個體的網站而言，存放在某個執行個體中的工作階段狀態將無法由其他執行個體存取。因為使用者要求無法路由至任何執行個體，便無法保證該工作階段資訊會位於該位置。再者，組態中的任何變更都會造成網站於完全不同的伺服器上執行。

快取服務 (預覽) 在網站外部提供了分散式快取服務。此舉可解決處理序內的工作階段狀態問題。如需如何使用工作階段狀態的詳細資訊，請參閱 [ASP.NET 工作階段狀態概觀](http://msdn.microsoft.com/zh-tw/library/ms178581.aspx)。

以下是使用快取服務 (預覽) 以因應工作階段狀態快取需要的基本步驟：

-   [建立快取。](#createcache)
-   [設定 ASP.NET 專案使用 Azure 快取。](#configureproject)
-   [修改 web.config 檔案。](#configurewebconfig)
-   [使用工作階段物件來儲存與擷取快取項目。](#usesessionobject)

建立快取
--------

1.  在 Azure 管理入口網站下方，按一下 [新增] 圖示****。

    ![新圖示](./media/web-sites-dotnet-session-state-caching/CacheScreenshot_NewButton.png)

2.  依序選取 [資料服務]、**[快取]**，然後按一下 **快速建立**。

    ![NewCacheDialog](./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CreateOptions.png)

3.  在 [端點] 文字方塊中，**輸入唯一的快取名稱**。然後為其他快取屬性選取適當的值，並按一下 **[建立新快取]**。

4.  在管理入口網站中選取 **[快取]** 圖示，以檢視所有的快取服務端點。

    ![快取圖示](./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CacheIcon.png)

5.  您可以接著選取其中一項快取服務端點以檢視其屬性。以下章節將使用 **[儀表板]** 索引標籤內的設定來設定 ASP.NET 專案的快取。

設定 ASP.NET 專案
-----------------

1.  首先，確保您[已安裝最新的](http://www.windowsazure.com/zh-tw/downloads/?sdk=net) **Azure SDK for .NET**。

2.  在 Visual Studio 中，在 [解決方案總管] 中以滑鼠右鍵按一下 ASP.NET 專案****，然後選取 **[管理 NuGet 封裝]**。(如果您使用的是 WebMatrix，則改為按一下工具列上的 **[NuGet]** 按鈕)

3.  在 [線上搜尋] 編輯方塊中，輸入 **WindowsAzure.Caching**。

    ![NuGet 對話方塊](./media/web-sites-dotnet-session-state-caching/CachingScreenshot_NuGet.png)

4.  選取 **[Azure 快取]** 封裝，然後按一下 **[安裝]** 按鈕。

修改 Web.Config 檔案
--------------------

除了對快取進行組件參考之外，NuGet 封裝還能在 web.config 檔案中加入虛設常式項目。若要在工作階段狀態中使用快取，必須對 web.config 進行多項修改。

1.  針對 ASP.NET 專案開啟 **web.config** 檔案。

2.  找到現有的 **sessionState** 元素並加上註解 (或加以移除)。

3.  接著取消由 Azure 快取 NuGet 封裝所新增的 **sessionState** 元素註解。最後結果應該會類似下列螢幕擷取畫面。

    ![SessionStateConfig][SessionStateConfig]

4.  接下來，找到 **dataCacheClients** 區段。取消 **securityProperties** 子系元素註解。

    ![快取組態](./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CacheConfig.png)

5.  在 **autoDiscover** 元素中，將 **identifier** 屬性設定到您的快取端點 URL 中。若要找到您的端點 URL，請前往 Azure 管理入口網站中的快取屬性。在 **[儀表板]** 索引標籤中，複製 [quick glance] 區段中的 **ENDPOINT URL** 值****。

    ![端點 URL](./media/web-sites-dotnet-session-state-caching/CachingScreenshot_EndpointURL.png)

6.  在 **messageSecurity** 元素中，將 **authorizationInfo** 屬性設定到您的快取存取金鑰中。若要尋找存取金鑰，請在 Azure 管理入口網站中選取您的快取。然後按一下下方列上的 **[管理金鑰]** 圖示。按一下 **[主要存取金鑰]** 文字方塊旁的複製按鈕。

    ![ManageKeys](./media/web-sites-dotnet-session-state-caching/CachingScreenshot_ManageAccessKeys.png)

在程式碼中使用工作階段物件
--------------------------

最後一步，則是開始在您的 ASP.NET 程式碼中使用工作階段物件。您可以使用 **Session.Add** 方法，將物件新增至工作階段狀態。此方法使用機碼值組，將項目儲存到工作階段狀態快取。

    string strValue = "yourvalue";
    Session.Add("yourkey", strValue);

以下程式碼會從工作階段狀態擷取此值。

    object objValue = Session["yourkey"];
    if (objValue != null)
    strValue = (string)obj; 

如需如何使用 ASP.NET 工作階段狀態的詳細資訊，請參閱 [ASP.NET 工作階段狀態概觀](http://msdn.microsoft.com/zh-tw/library/ms178581.aspx)。

