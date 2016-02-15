<properties 
	pageTitle="整合雲端服務與 Azure CDN" 
	description="指導如何部署雲端服務來提供整合式 Azure CDN 端點內容的教學課程" 
	services="cdn, cloud-services" 
	documentationCenter=".net" 
	authors="camsoper" 
	manager="dwrede" 
	editor="tysonn"/>

<tags 
	ms.service="cdn" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="12/02/2015" 
	ms.author="casoper"/>


# <a name="intro"></a> 整合雲端服務與 Azure CDN 

雲端服務可以與 Azure CDN 整合，從雲端服務的位置提供任何內容。此方法提供下列優點：

- 輕鬆地在雲端服務的專案目錄中部署和更新影像、指令碼和樣式表
- 輕鬆地升級雲端服務中的 NuGet 套件，例如 jQuery 或 Bootstrap 版本 
- 完全從相同的 Visual Studio 介面來管理 Web 應用程式和 CDN 提供的內容
- Web 應用程式和 CDN 提供的內容採用統一的部署工作流程
- 整合 ASP.NET 統合和縮製與 Azure CDN

## 學習目標 ##

在本教學課程中，您將了解如何：

-	[整合 Azure CDN 端點與雲端服務，並從 Azure CDN 提供網頁的靜態內容](#deploy)
-	[在雲端服務中為靜態內容設定快取設定](#caching)
-	[透過 Azure CDN 使用控制器動作提供內容](#controller)
-	[透過 Azure CDN 提供統合和縮製的內容，同時保持良好的 Visual Studio 指令碼偵錯體驗](#bundling)
-	[設定 Azure CDN 離線時的後援指令碼和 CSS](#fallback) 

## 將建置的項目 ##

您將使用預設 ASP.NET MVC 範本來部署雲端服務 Web 角色、加入程式碼從整合式 Azure CDN 提供內容，例如影像、控制器動作結果及預設 JavaScript 和 CSS 檔案，也會撰寫程式碼來為 CDN 離線時提供的套件組合設定後援機制。

## 必要元件 ##

本教學課程有下列先決條件：

-	使用中的 [Microsoft Azure 帳戶](/account/)
-	Visual Studio 2015 (含 [Azure SDK](http://go.microsoft.com/fwlink/?linkid=518003&clcid=0x409))

> [AZURE.NOTE] 您必須有 Azure 帳戶才能完成本教學課程：+ 您可以[免費申請帳戶](/pricing/free-trial/) - 我們會提供可用來試用付費 Azure 服務的點數，即使點數用完了，您仍然可以保留帳戶，使用免費的 Azure 服務，例如網站。 + 您可以[啟用 MSDN 訂閱者權益](/pricing/member-offers/msdn-benefits-details/) - 您的 MSDN 訂用帳戶每個月都會給您可用來試用付費 Azure 服務的點數。

<a name="deploy"></a>
## 部署雲端服務 ##

在本節中，您將在 Visual Studio 2015 中將預設 ASP.NET MVC 應用程式範本部署至雲端服務 Web 角色，然後將其與新的 CDN 端點整合。請遵循下列指示：

1. 在 Visual Studio 2015 中，從功能表列前往 **[檔案] > [新增] > [專案] > [雲端] > [Azure 雲端服務]**，建立新的 Azure 雲端服務。命名並按一下 [確定]。

	![](media/cdn-cloud-service-with-cdn/cdn-cs-1-new-project.PNG)

2. 選取 [**ASP.NET Web 角色**]，按一下 **>** 按鈕。按一下 [確定]。

	![](media/cdn-cloud-service-with-cdn/cdn-cs-2-select-role.PNG)

3. 選取 [**MVC**]，按一下 [**確定**]。

	![](media/cdn-cloud-service-with-cdn/cdn-cs-3-mvc-template.PNG)

4. 現在，將此 Web 角色發行至 Azure 雲端服務。以滑鼠右鍵按一下雲端服務專案，選取 [**發行**]。

	![](media/cdn-cloud-service-with-cdn/cdn-cs-4-publish-a.png)

5. 若您尚未登入 Microsoft Azure，請按一下 [**新增帳戶]** 下拉式清單，然後按一下 [**新增帳戶**] 功能表項目。

	![](media/cdn-cloud-service-with-cdn/cdn-cs-5-publish-signin.png)

6. 在登入頁面中，使用您用來啟用 Azure 帳戶的 Microsoft 帳戶登入。
7. 登入後，按一下 [**下一步**]。

	![](media/cdn-cloud-service-with-cdn/cdn-cs-6-publish-signedin.png)

8. 如果您未建立雲端服務或儲存體帳戶，Visual Studio 會協助您建立兩者。在 [**建立雲端服務與帳戶**] 對話方塊中，輸入想要的服務名稱，並選取需要的區域。然後按一下 [**建立**]。

	![](media/cdn-cloud-service-with-cdn/cdn-cs-7-publish-createserviceandstorage.png)

9. 在發行設定頁面中，驗證設定並按一下 [**發行**]。

	![](media/cdn-cloud-service-with-cdn/cdn-cs-8-publish-finalize.png)

	>[AZURE.NOTE] 雲端服務的發行程序會花費很長時間。[啟用所有 Web 角色的 Web Deploy] 選項可快速 (但暫時) 提供更新給 Web 角色，加速偵測您的雲端服務。如需此選項的詳細資訊，請參閱[使用 Azure Tools 發行雲端服務](http://msdn.microsoft.com/library/ff683672.aspx)。

	當 [**Microsoft Azure 活動記錄**] 的發佈狀態為 [**已完成**] 時，您便可建立與此雲端服務整合的 CDN 端點。
	
	>[AZURE.WARNING] 若在發佈後部署的雲端服務顯示錯誤畫面，原因可能在於您已部署的雲端服務使用[不含 .NET 4.5.2 的客體 OS](../cloud-services/cloud-services-guestos-update-matrix.md#news-updates)。您可[將 .NET 4.5.2 部署為啟動工作](../cloud-services/cloud-services-dotnet-install-dotnet.md)，以解決此問題。

## 建立新的 CDN 設定檔

CDN 設定檔就是 CDN 端點的集合。每個設定檔皆包含一或多個 CDN 端點。您可能會想要使用多個設定檔，依網際網路網域、Web 應用程式或其他準則來組織您的 CDN 端點。

> [AZURE.TIP] 若您已有您想要用於本教學課程的 CDN 設定檔，請繼續參閱「[建立新的 CDN 端點](#create-a-new-cdn-endpoint)」。

**建立新的 CDN 設定檔**

1. 在 [Azure 管理入口網站](https://portal.azure.com)的左上角，按一下 [新增]。在 [新增] 刀鋒視窗中，依序選取 [媒體 + CDN] 和 [CDN]。

    此時會顯示新的 [CDN 設定檔] 刀鋒視窗。
    
    ![新增 CDN 設定檔][new-cdn-profile]

2. 輸入 CDN 設定檔的名稱。

3. 選取 [定價層] 或使用預設值。

4. 選取或建立 [**資源群組**]。此資源群組並不必然是與您儲存體帳戶相同的資源群組。

5. 針對此 CDN 設定檔選取 [**訂用帳戶**]。基於本教學課程用途，此訂用帳戶必須為與儲存體帳戶相同的訂用帳戶。

6. 選取 [**位置**]。此為儲存您 CDN 設定檔資訊的所在 Azure 位置。其不會影響 CDN 端點位置。此位置不一定是與儲存體帳戶相同的位置。

7. 按一下 [建立] 按鈕，以建立新的設定檔。

## 建立新的 CDN 端點

**為儲存體帳戶建立新的 CDN 端點**

1. 在 [Azure 管理入口網站](https://portal.azure.com)中，巡覽至您的 CDN 設定檔。您可能已在先前步驟中將其釘選至儀表板。若否，則您可依序按一下 [瀏覽]、[CDN 設定檔] 尋找該設定檔，然後再按一下您要在其中加入端點的設定檔。

    此時會顯示 [CDN 設定檔] 刀鋒視窗。
    
    ![CDN 設定檔][cdn-profile-settings]
    
2. 按一下 [新增端點] 按鈕。

    ![[加入端點] 按鈕][cdn-new-endpoint-button]

    此時會顯示 [加入端點] 刀鋒視窗。
    
    ![新增端點刀鋒視窗][cdn-add-endpoint]

3. 輸入這個 CDN 端點的 [名稱]。此名稱會用於存取位於網域 `<EndpointName>.azureedge.net` 的快取資源。

4. 在 [**原始類型**] 下拉式清單中，選取 [*雲端服務*]。

5. 在 [**原始主機名稱**] 下拉式清單中，選取您的雲端服務。

6. 針對以下項目保留預設值：[**原始路徑**]、[**原始主機標頭**] 和 [**通訊協定/原始連接埠**]。您至少必須指定一個通訊協定 (HTTP 或 HTTPS)。

7. 按一下 [**新增**] 按鈕，以建立新的端點。

8. 端點建立完畢之後，即會出現在設定檔的端點清單中。此清單檢視會顯示用來存取所快取內容的 URL 以及原始網域。

    ![CDN 端點][cdn-endpoint-success]

    > [AZURE.NOTE] 端點將無法立即可用。註冊可能需要 90 分鐘的處理時間，以透過 CDN 網路傳播。若使用者嘗試立即使用 CDN 網域名稱，則可能會顯示狀態碼 404，直到可透過 CDN 使用內容為止。
	
## 測試 CDN 端點

若發佈狀態為 [**已完成**]，請開啟瀏覽器視窗並瀏覽至 **http://<cdnName>*.azureedge.net/Content/bootstrap.css**。在我的設定中，此 URL 為：

	http://camservice.azureedge.net/Content/bootstrap.css

對應至 CDN 端點上的下列原始 URL：

	http://camcdnservice.cloudapp.net/Content/bootstrap.css

瀏覽至 **http://*&lt;cdnName>*.azureedge.net/Content/bootstrap.css** 時，取決於您的瀏覽器而定，系統會提示您下載或開啟來自已發佈 Web 應用程式的 bootstrap.css。

![](media/cdn-cloud-service-with-cdn/cdn-1-browser-access.PNG)

同樣地，您可以直接從 CDN 端點，以 **http://*&lt;serviceName>*.cloudapp.net/** 存取任何可公開存取的 URL。例如：

-	/Script 路徑中的 .js 檔案
-	/Content 路徑中的任何內容檔案
-	任何控制器/動作 
-	任何含有查詢字串的 URL (若 CDN 端點已啟用查詢字串的話)

實際上，上述組態可讓您從 **http://*&lt;cdnName>*.azureedge.net/** 託管整個雲端服務。若瀏覽至 ****http://camservice.azureedge.net/**，則會從 Home/Index 取得動作結果。

![](media/cdn-cloud-service-with-cdn/cdn-2-home-page.PNG)

然而，這不表示透過 Azure CDN 來提供整個雲端服務一定是好辦法 (或通常是好辦法)。有幾點需要注意：

-	此作法需要公開整個網站，因為目前 Azure CDN 無法提供任何私人內容。
-	除非系統能夠將客戶重新導向至原始 URL **http://*&lt;serviceName>*.cloudapp.net/**，否則假如 CDN 端點離線 (無論是因為排程的維護或使用者錯誤，或者任何其他的原因)，整個雲端服務也會離線。 
-	就算使用自訂的 Cache-Control 設定 (請參閱[在雲端服務中設定靜態檔案的快取選項](#caching))，CDN 端點也無法改善高度動態內容的效能。如果您嘗試從 CDN 端點載入首頁，如上所示，請注意，第一次載入預設首頁 (非常簡單的頁面) 至少需要 5 秒。設想，如果此頁面包含必須每分鐘更新的動態內容，客戶體驗有何影響。從 CDN 端點提供動態內容需要有較短的快取到期時間，這也說明 CDN 端點經常會發生快取遺漏。這會降低雲端服務的效能，也會折損 CDN 的效用。

替代方法是在雲端服務中依個別情況決定從 Azure CDN 提供什麼內容。總之，您已了解如何從 CDN 端點存取個別的內容檔案。我將在[透過 Azure CDN 從控制器動作提供內容](#controller)中說明如何透過 CDN 端點提供特定的控制器動作。

<a name="caching"></a>
## 在雲端服務中設定靜態內容的快取設定 ##

利用雲端服務中的 Azure CDN 整合，您可以指定如何在 CDN 端點中快取靜態內容。若要執行此動作，請從 Web 角色專案 (例如 WebRole1) 開啟 *Web.config*，將 `<staticContent>` 元素加入至 `<system.webServer>`。以下的 XML 將快取設為 3 天過期。

	<system.webServer>
	  <staticContent>
	    <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="3.00:00:00"/>
	  </staticContent>
	  ...
	</system.webServer>

這樣做時，雲端服務中的所有靜態檔案會在您的 CDN 快取中遵守相同規則。若要更精確控制快取設定，請將 *Web.config* 檔案加入至資料夾，並在檔案中新增您的設定。例如，將 *Web.config* 檔案加入至 *\\Content* 資料夾，並將內容改成下列 XML：

	<?xml version="1.0"?>
	<configuration>
	  <system.webServer>
	    <staticContent>
	      <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="15.00:00:00"/>
	    </staticContent>
	  </system.webServer>
	</configuration>

此設定會將 *\\Content* 資料夾中的所有靜態檔案快取 15 天。

如需有關如何設定 `<clientCache>` 元素的詳細資訊，請參閱[用戶端快取 &lt;clientCache>](http://www.iis.net/configreference/system.webserver/staticcontent/clientcache)。

在[透過 Azure CDN 從控制器動作提供內容](#controller)中，我也會說明如何在 CDN 快取中設定控制器動作結果的快取設定。

<a name="controller"></a>
## 透過 Azure CDN 從控制器動作提供內容 ##

整合雲端服務 Web 角色與 Azure CDN 時，透過 Azure CDN 從控制器動作提供內容就非常簡單。除了直接透過 Azure CDN 提供雲端服務 (如上面示範) 這個方法以外，在「[使用 Azure CDN 縮短網路延遲時間](http://channel9.msdn.com/events/TechDays/Techdays-2014-the-Netherlands/Reducing-latency-on-the-web-with-the-Windows-Azure-CDN)」(英文) 中，[Maarten Balliauw](https://twitter.com/maartenballiauw) 示範如何透過有趣的 MemeGenerator 控制器提供雲端服務。我在這裡簡單地重述一次。

假設您想在雲端服務中利用查克羅禮士年輕時的一張相片 ([Alan Light](http://www.flickr.com/photos/alan-light/218493788/) 拍攝) 來引起網路瘋傳：

![](media/cdn-cloud-service-with-cdn/cdn-5-memegenerator.PNG)

您有一個簡單的 `Index` 動作可讓客戶在照片上指定笑梗，再傳給此動作來引起網路瘋傳。因為是查克羅禮士，您預期此頁面一定會在全球造成一股旋風。這就是利用 Azure CDN 來提供半動態內容的一個最佳例子。

請依照上述步驟來設定此控制器動作：

1. 在 *\\Controllers* 資料夾中，建立一個新的 .cs 檔案稱為 *MemeGeneratorController.cs*，並將內容改成下列程式碼。請務必以您的 CDN 名稱取代醒目提示的部分。  

		using System;
		using System.Collections.Generic;
		using System.Diagnostics;
		using System.Drawing;
		using System.IO;
		using System.Net;
		using System.Web.Hosting;
		using System.Web.Mvc;
		using System.Web.UI;
		
		namespace WebRole1.Controllers
		{
		    public class MemeGeneratorController : Controller
		    {
		        static readonly Dictionary<string, Tuple<string ,string>> Memes = new Dictionary<string, Tuple<string, string>>();
		
		        public ActionResult Index()
		        {
		            return View();
		        }
		
		        [HttpPost, ActionName("Index")]
		        public ActionResult Index_Post(string top, string bottom)
		        {
		            var identifier = Guid.NewGuid().ToString();
		            if (!Memes.ContainsKey(identifier))
		            {
		                Memes.Add(identifier, new Tuple<string, string>(top, bottom));
		            }
		
		            return Content("<a href="" + Url.Action("Show", new {id = identifier}) + "">here's your meme</a>");
		        }
		
		        [OutputCache(VaryByParam = "*", Duration = 1, Location = OutputCacheLocation.Downstream)]
		        public ActionResult Show(string id)
		        {
		            Tuple<string, string> data = null;
		            if (!Memes.TryGetValue(id, out data))
		            {
		                return new HttpStatusCodeResult(HttpStatusCode.NotFound);
		            }
		
		            if (Debugger.IsAttached) // Preserve the debug experience
		            {
		                return Redirect(string.Format("/MemeGenerator/Generate?top={0}&bottom={1}", data.Item1, data.Item2));
		            }
		            else // Get content from Azure CDN
		            {
		                return Redirect(string.Format("http://<yourCdnName>.azureedge.net/MemeGenerator/Generate?top={0}&bottom={1}", data.Item1, data.Item2));
		            }
		        }
		
		        [OutputCache(VaryByParam = "*", Duration = 3600, Location = OutputCacheLocation.Downstream)]
		        public ActionResult Generate(string top, string bottom)
		        {
		            string imageFilePath = HostingEnvironment.MapPath("~/Content/chuck.bmp");
		            Bitmap bitmap = (Bitmap)Image.FromFile(imageFilePath);
		
		            using (Graphics graphics = Graphics.FromImage(bitmap))
		            {
		                SizeF size = new SizeF();
		                using (Font arialFont = FindBestFitFont(bitmap, graphics, top.ToUpperInvariant(), new Font("Arial Narrow", 100), out size))
		                {
		                    graphics.DrawString(top.ToUpperInvariant(), arialFont, Brushes.White, new PointF(((bitmap.Width - size.Width) / 2), 10f));
		                }
		                using (Font arialFont = FindBestFitFont(bitmap, graphics, bottom.ToUpperInvariant(), new Font("Arial Narrow", 100), out size))
		                {
		                    graphics.DrawString(bottom.ToUpperInvariant(), arialFont, Brushes.White, new PointF(((bitmap.Width - size.Width) / 2), bitmap.Height - 10f - arialFont.Height));
		                }
		            }
		
		            MemoryStream ms = new MemoryStream();
		            bitmap.Save(ms, System.Drawing.Imaging.ImageFormat.Png);
		            return File(ms.ToArray(), "image/png");
		        }
		
		        private Font FindBestFitFont(Image i, Graphics g, String text, Font font, out SizeF size)
		        {
		            // Compute actual size, shrink if needed
		            while (true)
		            {
		                size = g.MeasureString(text, font);
		
		                // It fits, back out
		                if (size.Height < i.Height &&
		                     size.Width < i.Width) { return font; }
		
		                // Try a smaller font (90% of old size)
		                Font oldFont = font;
		                font = new Font(font.Name, (float)(font.Size * .9), font.Style);
		                oldFont.Dispose();
		            }
		        }
		    }
		}

2. 以滑鼠右鍵按一下預設的 `Index()` 動作，選取 [**加入檢視**]。

	![](media/cdn-cloud-service-with-cdn/cdn-6-addview.PNG)

3.  接受下列設定，按一下 [加入]。

	![](media/cdn-cloud-service-with-cdn/cdn-7-configureview.PNG)

4. 開啟新的 *Views\\MemeGenerator\\Index.cshtml*，將內容改成下列簡單的 HTML 來提交笑梗：

		<h2>Meme Generator</h2>
		
		<form action="" method="post">
		    <input type="text" name="top" placeholder="Enter top text here" />
		    <br />
		    <input type="text" name="bottom" placeholder="Enter bottom text here" />
		    <br />
		    <input class="btn" type="submit" value="Generate meme" />
		</form>

5. 重新發佈雲端服務，然後使用瀏覽器瀏覽至 **http://*&lt;serviceName>*.cloudapp.net/MemeGenerator/Index**。

當您將表單值提交至 `/MemeGenerator/Index` 時，`Index_Post` 動作方法會傳回 `Show` 動作方法的連結及個別的輸入識別碼。按一下連結會執行下列程式碼：

	[OutputCache(VaryByParam = "*", Duration = 1, Location = OutputCacheLocation.Downstream)]
	public ActionResult Show(string id)
	{
		Tuple<string, string> data = null;
		if (!Memes.TryGetValue(id, out data))
		{
			return new HttpStatusCodeResult(HttpStatusCode.NotFound);
		}

		if (Debugger.IsAttached) // Preserve the debug experience
		{
			return Redirect(string.Format("/MemeGenerator/Generate?top={0}&bottom={1}", data.Item1, data.Item2));
		}
		else // Get content from Azure CDN
		{
			return Redirect(string.Format("http://<yourCDNName>.azureedge.net/MemeGenerator/Generate?top={0}&bottom={1}", data.Item1, data.Item2));
		}
	}
	
如果已附加本機偵錯程式，您將經由本機重新導向而享有一般的偵錯體驗。如果是在雲端服務中執行，則會重新導向至：

	http://<yourCDNName>.azureedge.net/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>

對應至 CDN 端點上的下列原始 URL：

	http://<youCloudServiceName>.cloudapp.net/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>


然後，您可以在 `Generate` 方法上使用 `OutputCacheAttribute` 屬性，以指定如何快取 Azure CDN 將接受的動作結果。以下程式碼指定快取到期時間 1 小時 (3,600 秒)。

    [OutputCache(VaryByParam = "*", Duration = 3600, Location = OutputCacheLocation.Downstream)]

同樣地，您可以在雲端服務中透過 Azure CDN，並指定想要的快取選項，從任何控制器動作提供內容。

在下一節，我將說明如何透過 Azure CDN 提供統合和縮製的指令碼與 CSS。

<a name="bundling"></a>
## 整合 ASP.NET 統合和縮製與 Azure CDN ##

指令碼和 CSS 樣式表不常變更，是 Azure CDN 快取的最佳候選項。透過 Azure CDN 提供整個 Web 角色是整合統合和縮製與 Azure CDN 最輕鬆的方法。然而，您可能不想這樣做，我將說明如何這樣做，但同時保留開發人員希望的 ASP.NET 統合和縮製體驗，例如：

-	絕佳的偵錯模式體驗
-	流暢的部署
-	隨著指令碼/CSS 版本升級立即更新用戶端
-	CDN 端點失敗時的後援機制
-	儘可能不修改程式碼

在您於「[整合 Azure CDN 端點與 Azure 網站」，並從 Azure CDN 提供網頁靜態內容](#deploy)所建立的 **WebRole1** 專案中，開啟 *App\_Start\\BundleConfig.cs*，然後查看 `bundles.Add()` 方法呼叫。

    public static void RegisterBundles(BundleCollection bundles)
    {
        bundles.Add(new ScriptBundle("~/bundles/jquery").Include(
                    "~/Scripts/jquery-{version}.js"));
		...
    }

第一個 `bundles.Add()` 陳述式將指令碼套件組合加入至虛擬目錄 `~/bundles/jquery`。然後，開啟 *Views\\Shared\_Layout.cshtml*，查看指令碼套件組合標籤如何轉譯。您應該可以找到下列這一行 Razor 程式碼：

    @Scripts.Render("~/bundles/jquery")

當此 Razor 程式碼在 Azure Web 角色中執行時，它會類似如下轉譯指令碼套件組合的 `<script>` 標籤：

    <script src="/bundles/jquery?v=FVs3ACwOLIVInrAl5sdzR2jrCDmVOWFbZMY6g6Q0ulE1"></script>

不過，當在 Visual Studio 中按 `F5` 執行時，則會個別轉譯套件組合中的每一個指令碼檔案 (在上述案例中，套件組合中只有一個指令碼檔案)：

    <script src="/Scripts/jquery-1.10.2.js"></script>

這樣可讓您在開發環境中進行 JavaScript 程式碼偵錯，同時減少並行的用戶端連線 (統合)，在實際執行環境中提升檔案下載效能 (縮製)。這是 Azure CDN 整合中保留的絕佳功能。此外，由於轉譯的套件組合已包含自動產生的版本字串，您可以仿照此功能，每當您透過 NuGet 更新 jQuery 版本時，就會以最快速度更新用戶端。

請遵循下列步驟來整合 ASP.NET 統合和縮製與 CDN 端點。

1. 回到 *App\_Start\\BundleConfig.cs*，修改 `bundles.Add()` 方法來使用不同的 [Bundle 建構函數](http://msdn.microsoft.com/library/jj646464.aspx) (此函數會指定 CDN 位址)。若要這樣做，請將 `RegisterBundles` 方法定義改成下列程式碼：  

		public static void RegisterBundles(BundleCollection bundles)
		{
		    bundles.UseCdn = true;
		    var version = System.Reflection.Assembly.GetAssembly(typeof(Controllers.HomeController))
		        .GetName().Version.ToString();
		    var cdnUrl = "http://<yourCDNName>.azureedge.net/{0}?v=" + version;
		
		    bundles.Add(new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "bundles/jquery")).Include(
		                "~/Scripts/jquery-{version}.js"));
		
		    bundles.Add(new ScriptBundle("~/bundles/jqueryval", string.Format(cdnUrl, "bundles/jqueryval")).Include(
		                "~/Scripts/jquery.validate*"));
		
		    // Use the development version of Modernizr to develop with and learn from. Then, when you're
		    // ready for production, use the build tool at http://modernizr.com to pick only the tests you need.
		    bundles.Add(new ScriptBundle("~/bundles/modernizr", string.Format(cdnUrl, "bundles/modernizer")).Include(
		                "~/Scripts/modernizr-*"));
		
		    bundles.Add(new ScriptBundle("~/bundles/bootstrap", string.Format(cdnUrl, "bundles/bootstrap")).Include(
		                "~/Scripts/bootstrap.js",
		                "~/Scripts/respond.js"));
		
		    bundles.Add(new StyleBundle("~/Content/css", string.Format(cdnUrl, "Content/css")).Include(
		                "~/Content/bootstrap.css",
		                "~/Content/site.css"));
		}

	請記得將 `<yourCDNName>` 取代為您的 Azure CDN 名稱。

	簡單地說，您正在設定 `bundles.UseCdn = true`，且已在每一個套件組合中加入謹慎建構的 CDN URL。例如，程式碼的第一個建構函式：

		new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "bundles/jquery"))

	就如同：

		new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "http://<yourCDNName>.azureedge.net/bundles/jquery?v=<W.X.Y.Z>"))

	此建構函式告知 ASP.NET 統合和縮製在本機偵錯時轉譯個別指令碼檔案，但使用指定的 CDN 位址來存取所提及的指令碼。不過，對於此謹慎建構的 CDN URL，請注意兩項重要特性：
	
	-	此 CDN URL 的來源是 `http://<yourCloudService>.cloudapp.net/bundles/jquery?v=<W.X.Y.Z>`，事實上就是雲端服務中指令碼套件組合的虛擬目錄。
	-	由於是使用 CDN 建構函式，套件組合的 CDN 指令碼標籤在轉譯的 URL 中已不再包含自動產生的版本字串。指令碼套件組合每次修改時，您都必須手動產生唯一的版本字串，以強制在 Azure CDN 上發生快取遺漏。同時，在部署套件組合之後，此唯一的版本字串在部署的整個存在期間內必須保持不變，讓 Azure CDN 的快取命中率達到最高。
	-	查詢字串 v=<W.X.Y.Z> 會從 Web 角色專案的 *Properties\\AssemblyInfo.cs* 中提取。您的部署工作流程中可以包含每次發佈至 Azure 時就遞增組件版本。或者，您可以直接修改專案中的 *Properties\\AssemblyInfo.cs*，使用萬用字元 '*' 表示每次建置時就自動遞增版本字串。例如：

			[assembly: AssemblyVersion("1.0.0.*")]
	
		在此可採取其他任何策略在部署的存在期間內產生唯一字串。

3. 重新發行雲端服務和存取首頁。
 
4. 檢視頁面的 HTML 程式碼。您應該會看到轉譯的 CDN URL，以及每次將變更重新發行至雲端服務時的唯一版本字串。例如：

		...
		
		<link href="http://camservice.azureedge.net/Content/css?v=1.0.0.25449" rel="stylesheet"/>
		
		<script src="http://camservice.azureedge.net/bundles/modernizer?v=1.0.0.25449"></script>
		
		...
		
		<script src="http://camservice.azureedge.net/bundles/jquery?v=1.0.0.25449"></script>
		
		<script src="http://camservice.azureedge.net/bundles/bootstrap?v=1.0.0.25449"></script>
		
		...

5. 在 Visual Studio 中，按下 `F5` 在 Visual Studio 中偵錯雲端服務。

6. 檢視頁面的 HTML 程式碼。您仍然會看到每一個指令檔以個別方式轉譯，讓您在 Visual Studio 中享有一致的偵錯體驗。

		...
		
		    <link href="/Content/bootstrap.css" rel="stylesheet"/>
		<link href="/Content/site.css" rel="stylesheet"/>
		
		    <script src="/Scripts/modernizr-2.6.2.js"></script>
		
		...
		
		    <script src="/Scripts/jquery-1.10.2.js"></script>
		
		    <script src="/Scripts/bootstrap.js"></script>
		<script src="/Scripts/respond.js"></script>
		
		...   

<a name="fallback"></a>
## CDN URL 的後援機制 ##

當 Azure CDN 端點因故失敗時，您一定希望網頁能夠聰明地存取原始 Web 伺服器，當作後援選項來載入 JavaScript 或 Bootstrap。因 CDN 無法使用而遺失網站的影像就已經夠嚴重，而遺失由指令碼和樣式表所提供的重要頁面功能又更加嚴重。

[Bundle](http://msdn.microsoft.com/library/system.web.optimization.bundle.aspx) 類別包含一個稱為 [CdnFallbackExpression](http://msdn.microsoft.com/library/system.web.optimization.bundle.cdnfallbackexpression.aspx) 的屬性，可讓您設定 CDN 失敗時的後援機制。若要使用此屬性，請遵循下列步驟：

1. 在 Web 角色專案中，開啟 *App\_Start\\BundleConfig.cs* (您已在該檔案中，將 CDN URL 加入每個 [Bundle 建構函式](http://msdn.microsoft.com/library/jj646464.aspx))，透過下列醒目提示的變更將後援機制加入預設套件組合：  

		public static void RegisterBundles(BundleCollection bundles)
		{
		    var version = System.Reflection.Assembly.GetAssembly(typeof(BundleConfig))
		        .GetName().Version.ToString();
		    var cdnUrl = "http://cdnurl.azureedge.net/.../{0}?" + version;
		    bundles.UseCdn = true;
		
		    bundles.Add(new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "bundles/jquery")) 
						{ CdnFallbackExpression = "window.jquery" }
		                .Include("~/Scripts/jquery-{version}.js"));
		
		    bundles.Add(new ScriptBundle("~/bundles/jqueryval", string.Format(cdnUrl, "bundles/jqueryval")) 
						{ CdnFallbackExpression = "$.validator" }
		            	.Include("~/Scripts/jquery.validate*"));
		
		    // Use the development version of Modernizr to develop with and learn from. Then, when you're
		    // ready for production, use the build tool at http://modernizr.com to pick only the tests you need.
		    bundles.Add(new ScriptBundle("~/bundles/modernizr", string.Format(cdnUrl, "bundles/modernizer")) 
						{ CdnFallbackExpression = "window.Modernizr" }
						.Include("~/Scripts/modernizr-*"));
		
		    bundles.Add(new ScriptBundle("~/bundles/bootstrap", string.Format(cdnUrl, "bundles/bootstrap")) 	
						{ CdnFallbackExpression = "$.fn.modal" }
		        		.Include(
			              		"~/Scripts/bootstrap.js",
			              		"~/Scripts/respond.js"));
		
		    bundles.Add(new StyleBundle("~/Content/css", string.Format(cdnUrl, "Content/css")).Include(
		                "~/Content/bootstrap.css",
		                "~/Content/site.css"));
		}

	當 `CdnFallbackExpression` 不是 null 時，指令碼會插入 HTML 中來測試是否已成功載入套件組合，如果不是，則直接從原始 Web 伺服器存取套件組合。此屬性必須設為 JavaScript 運算式來測試個別的 CDN 套件組合是否正確載入。測試每一個套件組合所需的運算式根據內容而不同。在以上的預設套件組合中：
	
	-	`window.jquery` 定義於 jquery-{version}.js 中
	-	`$.validator` 定義於 jquery.validate.js 中
	-	`window.Modernizr` 定義於 modernizer-{version}.js 中
	-	`$.fn.modal` 定義於 bootstrap.js 中
	
	您可能發現到我沒有為 `~/Cointent/css` 套件組合設定 CdnFallbackExpression。這是因為目前 [System.Web.Optimization 中有錯誤](https://aspnetoptimization.codeplex.com/workitem/104)，導致插入後援 CSS 的 `<script>` 標籤，而非預期的 `<link>` 標籤。
	
	不過，[Ember 顧問團](https://github.com/EmberConsultingGroup/StyleBundleFallback) (英文) 提供一套良好的[樣式套件組合後援](https://github.com/EmberConsultingGroup) (英文)。

2. 若要使用 CSS 的因應措施，請在 Web 角色專案的 [*App\_Start*] 資料夾中建立新的 .cs 檔案，命名為 *StyleBundleExtensions.cs*，並將其內容改成 [GitHub 的程式碼](https://github.com/EmberConsultingGroup/StyleBundleFallback/blob/master/Website/App_Start/StyleBundleExtensions.cs)。

4. 在 *App\_Start\\StyleFundleExtensions.cs* 中，將命名空間重新命名為您的 Web 角色名稱 (例如 **WebRole1**)。

3. 回到 `App_Start\BundleConfig.cs`，將最後一個 `bundles.Add` 陳述式修改為下列醒目提示的程式碼：

		bundles.Add(new StyleBundle("~/Content/css", string.Format(cdnUrl, "Content/css"))
		    <mark>.IncludeFallback("~/Content/css", "sr-only", "width", "1px")</mark>
		    .Include(
		          "~/Content/bootstrap.css",
		          "~/Content/site.css"));

	這個新的延伸方法採用相同的概念，將指令碼插入 HTML 中來檢查 DOM，尋找 CSS 套件組合中定義的相符類別名稱、規則名稱和規則值，而如果找不到相符項，則退一步存取原始 Web 伺服器。

4. 重新發行雲端服務和存取首頁。

5. 檢視頁面的 HTML 程式碼。您應該會發現類似下方的插入指令碼：

		...
		
	    <link href="http://az632148.azureedge.net/Content/css?v=1.0.0.25474" rel="stylesheet"/>
		<script>(function() {
		                var loadFallback,
		                    len = document.styleSheets.length;
		                for (var i = 0; i < len; i++) {
		                    var sheet = document.styleSheets[i];
		                    if (sheet.href.indexOf('http://camservice.azureedge.net/Content/css?v=1.0.0.25474') !== -1) {
		                        var meta = document.createElement('meta');
		                        meta.className = 'sr-only';
		                        document.head.appendChild(meta);
		                        var value = window.getComputedStyle(meta).getPropertyValue('width');
		                        document.head.removeChild(meta);
		                        if (value !== '1px') {
		                            document.write('<link href="/Content/css" rel="stylesheet" type="text/css" />');
		                        }
		                    }
		                }
		                return true;
		            }())||document.write('<script src="/Content/css"><\/script>');</script>
		
		    <script src="http://camservice.azureedge.net/bundles/modernizer?v=1.0.0.25474"></script>
		<script>(window.Modernizr)||document.write('<script src="/bundles/modernizr"><\/script>');</script>
		
		... 
		
		    <script src="http://camservice.azureedge.net/bundles/jquery?v=1.0.0.25474"></script>
		<script>(window.jquery)||document.write('<script src="/bundles/jquery"><\/script>');</script>
		
		    <script src="http://camservice.azureedge.net/bundles/bootstrap?v=1.0.0.25474"></script>
		<script>($.fn.modal)||document.write('<script src="/bundles/bootstrap"><\/script>');</script>
		
		...


	請注意，針對 CSS 套件組合插入的指令碼，仍在這一行中包含 `CdnFallbackExpression` 屬性殘留的遊蕩部分：

        }())||document.write('<script src="/Content/css"><\/script>');</script>

	但因為 || 運算式的開頭部分一定會傳回 true (緊鄰的上一行)，所以 document.write() 函數永遠不會執行。

## 相關資訊 ##
- [Azure 內容傳遞網路 (CDN) 概觀](http://msdn.microsoft.com/library/azure/ff919703.aspx)
- [使用 Azure 的 CDN](cdn-how-to-use-cdn.md)
- [ASP.NET 統合和縮製](http://www.asp.net/mvc/tutorials/mvc-4/bundling-and-minification)



[new-cdn-profile]: ./media/cdn-cloud-service-with-cdn/cdn-new-profile.png
[cdn-profile-settings]: ./media/cdn-cloud-service-with-cdn/cdn-profile-settings.png
[cdn-new-endpoint-button]: ./media/cdn-cloud-service-with-cdn/cdn-new-endpoint-button.png
[cdn-add-endpoint]: ./media/cdn-cloud-service-with-cdn/cdn-add-endpoint.png
[cdn-endpoint-success]: ./media/cdn-cloud-service-with-cdn/cdn-endpoint-success.png

<!---HONumber=AcomDC_0204_2016-->