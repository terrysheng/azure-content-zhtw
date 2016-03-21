<properties
   pageTitle="Azure App Service 本機快取概觀"
   description="這篇文章說明如何啟用、調整大小並查詢 Azure App Service 本機快取功能的狀態"
   services="app-service"
   documentationCenter="app-service"
   authors="SyntaxC4"
   manager="yochayk"
   editor=""
   tags="optional"
   keywords="僅供 SEO Champ 使用。使用逗號分隔詞彙。在您變更這篇包含這些詞彙之文章的內容之前，請洽詢您的 SEO Champ。"/>

<tags
   ms.service="app-service"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/04/2016"
   ms.author="cfowler"/>

# Azure App Service 本機快取概觀

Azure Web 應用程式的內容儲存在 Azure 儲存體，並做為內容共用以持久的方式顯示。這項設計是要與各種應用程式搭配使用，並具有下列屬性 ︰

* 內容會跨多個 Web 應用程式的 VM 執行個體共用。 
* 內容具有持久性，且可透過執行 Web 應用程式來修改。 
* 記錄檔和診斷資料檔案會在相同的共用內容資料夾底下提供使用。 
* 發佈新的內容會直接更新內容資料夾，並可立即透過 SCM 網站與執行中的 Web 應用程式來檢視相同的內容 (通常像是 ASP.NET 的一些技術會在某些檔案變更時起始 Web 應用程式重新啟動，以挑選最新的內容)。 

雖然許多 Web 應用程式使用這些功能的其中之一或所有功能，某些 Web 應用程式還是只想要一個可在具備高可用性狀況下執行的高效能唯讀內容存放區。這些應用程式可以從 VM 執行個體特定的內容副本 (稱為「本機快取」) 受益。「本機快取」提供了內容的 Web 角色檢視，而這個內容是在網站啟動時，以非同步方式建立之儲存體內容的 write-but-discard 快取。當快取已準備就緒時，網站會切換為執行快取的內容。在本機快取上執行的 Web 應用程式有下列優點：

* 它們不受存取 Azure 儲存體上的內容時所發生的延遲影響 
* 它們不受提供內容共用之伺服器進行計劃的升級或未規劃的停機 ，以及 Azure 儲存體的任何其他干擾影響 
* 較少應用程式會因為儲存體共用變更而重新啟動 

## 本機快取如何變更 App Service 的行為

* 本機快取是 Web 應用程式之 /site 和 /siteextensions 資料夾的副本，且是在 Web 應用程式啟動時於本機 VM 執行個體上建立。每個 Web 應用程式的本機快取大小，預設是限制為 300 MB，但最多可以增加至 1 GB。 
* 本機快取為可讀寫狀態，不過在 Web 應用程式移動虛擬機器或重新啟動時，將會捨棄任何修改。本機快取不應該用於保存內容存放區中之任務關鍵資料的應用程式。 
* Web 應用程式可以像目前一樣，繼續寫入記錄檔和診斷資料。不過，記錄檔和資料是儲存在本機 VM 上，再定期複製到共用內容存放區。複製到共用內容存放區是最好的狀況，重新寫回可能會因為 VM 執行個體突然當機而遺失。 
* 使用「本機快取」之 Web 應用程式的「記錄檔」和「資料」資料夾的資料夾結構有所變更。目前儲存體「記錄檔」和「資料」資料夾中有子資料夾 (遵循「唯一識別碼」+ 時間戳記的命名模式)。每個子資料夾都對應到其中正在執行或已經執行 Web 應用程式的 VM 執行個體。  
* 透過任何發行機制將變更發行至 Web 應用程式時，將會發佈到共用內容存放區。這是預設行為，因為我們希望發佈的內容具有持久性。若要重新整理 Web 應用程式的本機快取，則必須重新啟動它。看起來像是多餘的步驟嗎？ 請參閱下方讓生命週期更順暢。
* D:\\Home 會指向本機快取。D:\\local 將繼續指向暫存 VM 的特定儲存體。 
* SCM 網站的預設內容檢視將仍是共用內容存放區。若要查看本機快取資料夾的外觀，您可以瀏覽至 https://[site-name].scm.azurewebsites.net/VFS/LocalSiteRoot/LocalCache

## 如何啟用 Azure App Service 中的本機快取

本機快取是使用保留的應用程式設定的組合來設定。這些應用程式設定可以使用下列方法來設定：

* [Azure 入口網站](#Configure-Local-Cache-Portal)
* [Azure 資源管理員](#Configure-Local-Cache-ARM)

### 作法：使用 Azure 入口網站設定本機快取
<a name="Configure-Local-Cache-Portal"></a>

使用 AppSetting. `WEBSITE_LOCAL_CACHE_OPTION` = `Always`，以每個 Web 應用程式為基礎啟用本機快取

![Azure 入口網站應用程式設定︰本機快取](media/app-service-local-cache/app-service-local-cache-configure-portal.png)

### 作法：使用 Azure Resource Manager 設定本機快取
<a name="Configure-Local-Cache-ARM"></a>

```
...

{
    "apiVersion": "2015-08-01",
    "type": "config",
    "name": "appsettings",
    "dependsOn": [
        "[resourceId('Microsoft.Web/sites/', variables('siteName'))]"
    ],
    "properties": {
        "WEBSITE_LOCAL_CACHE_OPTION": "Always",
        "WEBSITE_LOCAL_CACHE_SIZEINMB": "300" 
    }
}

...
```

## 如何變更 App Service 本機快取的大小？

根據預設，本機快取大小是 **300 MB**。這包含了網站、從內容存放區複製的 SiteExtensions 資料夾，以及任何本機建立的記錄檔和資料夾。若要增加此限制，請使用 AppSetting `WEBSITE_LOCAL_CACHE_SIZEINMB`。大小最多可以增加到每個 Web 應用程式各 **1 GB** (1000 MB)。

## 使用 App Service 本機快取的最佳作法

建議您搭配[預備環境](../app-service-web/web-sites-staged-publishing.md)功能使用本機快取。

* 將「黏性」Appsetting `WEBSITE_LOCAL_CACHE_OPTION` 與值 `Always` 新增至您的「生產環境位置」。如果使用 `WEBSITE_LOCAL_CACHE_SIZEINMB`，也將它做為黏性設定新增至您的生產環境位置。 
* 建立預備環境位置，並發佈至您的預備環境位置。預備環境位置通常不會在針對預備環境使用本機快取來獲得順暢的建置-部署-測試生命週期時，又能同時獲得生產環境位置之「本機快取」提供的好處。 
*	針對預備位置測試您的網站。  
*	準備好後，請在您的「預備環境」和「生產環境」位置之間發出[交換作業](../app-service-web/web-sites-staged-publishing.md#to-swap-deployment-slots)。  
*	黏性設定是依名稱和黏性位置而定。因此當預備環境位置交換到生產環境，它會繼承本機快取應用程式設定。新交換的生產環境位置會在幾分鐘後針對「本機快取」執行，並會在交換後的位置準備就緒時一併就緒。因此完成位置交換時，將會針對「本機快取」執行您的生產環境位置。

## 常見問題集

### 如何知道「本機快取」是否適用於我的 Web 應用程式？ 

如果您的 Web 應用程式需要高效能、可靠的內容存放區，且不使用內容存放區在執行階段時寫入重要資料並且大小總計小於 1 GB，那麼答案就是肯定的！ 若要取得您「網站」和「網站擴充功能」資料夾的大小總計，您可以使用網站擴充功能「Azure Web Apps 磁碟使用量」。
 
### 如何啟用本機快取？ 

請參閱上節的「使用本機快取時的最佳作法」。
 
### 如何知道我的網站是否已切換成使用本機快取？ 

如果搭配預備環境使用「本機快取」，在本機快取準備就緒之前將無法完成交換作業。若要檢查您的網站是否正在執行本機快取，您可以檢查工作者處理序環境變數 `WEBSITE_LOCALCACHE_READY`。使用這裡的指示，在多個執行個體上存取工作者處理序環境變數。
 
### 我剛剛發佈了新的變更 - 但我的 Web 應用程式似乎沒有變更。理由 
如果您的 Web 應用程式使用「本機快取」，則您需要重新啟動您的網站，以取得最新的變更。不想對生產網站這麼做嗎？ 請參閱上述的位置選項。
 
### 我的記錄檔在哪裡？ 

使用本機快取，您的記錄檔和資料的資料夾看起來會有點不同。不過，子資料夾的結構會保持相同，不同之處在於它們都位在子資料夾，格式為「唯一 VM 識別碼」+ 時間戳記。
 
### 我已啟用「本機快取」，但我的 Web 應用程式仍然重新啟動。這是為什麼？ 我以為「本機快取」有助於頻繁的應用程式重新啟動。 

「本機快取」確實有助於防止儲存體相關 Web 應用程式重新啟動。不過，您的 Web 應用程式仍有可能在已規劃好的 VM 基礎結構升級期間重新啟動 。啟用「本機快取」後應該會較少發生整體應用程式重新啟動。

<!---HONumber=AcomDC_0309_2016-->