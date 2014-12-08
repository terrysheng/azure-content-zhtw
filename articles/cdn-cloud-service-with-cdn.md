<properties urlDisplayName="Integrate a cloud service with Azure CDN" pageTitle="整合雲端服務與 Azure CDN" metaKeywords="Azure tutorial, Azure web app tutorial, ASP.NET, CDN, MVC, cloud service" description="A tutorial that teaches you how to deploy a cloud service that serves content from an integrated Azure CDN endpoint" metaCanonical="" services="cdn,cloud-services" documentationCenter=".NET" title="Integrate a cloud service with Azure CDN" authors="cephalin" solutions="" manager="wpickett" editor="tysonn" />

<tags ms.service="cdn" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="10/02/2014" ms.author="cephalin" />

<a name="intro"></a>
# 整合雲端服務與 Azure CDN #

雲端服務可以與 Azure CDN 整合，以從雲端服務的 `~/CDN` 路徑來提供任何內容。此方法提供下列優點：

- 輕鬆地在雲端服務的專案目錄中部署和更新影像、指令碼和樣式表
- 輕鬆地升級雲端服務中的 NuGet 套件，例如 jQuery 或 Bootstrap 版本 
- 完全從相同的 Visual Studio 介面來管理 Web 應用程式和 CDN 提供的內容
- Web 應用程式和 CDN 提供的內容採用統一的部署工作流程
- 整合 ASP.NET 統合和縮製與 Azure CDN

## 學習目標 ##

在本教學課程中，您將了解如何：

-	[整合 Azure CDN 端點與雲端服務，並從 Azure CDN 提供網頁的靜態內容](#deploy)
-	[在雲端服務中設定靜態內容的快取設定](#caching)
-	[透過 Azure CDN 從控制器動作提供內容](#controller)
-	[透過 Azure CDN 提供統合和縮製的內容，同時保留 Visual Studio 中的指令碼偵錯體驗](#bundling)
-	[設定當 Azure CDN 離線時後援的指令碼和 CSS](#fallback) 

## 將建置的項目 ##

您將使用預設 ASP.NET MVC 範本來部署雲端服務 Web 角色、加入程式碼從整合式 Azure CDN 提供內容，例如影像、控制器動作結果及預設 JavaScript 和 CSS 檔案，也會撰寫程式碼來為 CDN 離線時提供的套件組合設定後援機制。

## 必要元件 ##

本教學課程有下列先決條件：

-	使用中的 [Microsoft Azure 帳戶](http://azure.microsoft.com/zh-tw/account/)
-	Visual Studio 2013 (含 [Azure SDK](http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409))

<div class="wa-note">
  <span class="wa-icon-bulb"></span>
  <h5><a name="note"></a>要完成此教學課程，您必須要有 Azure 帳戶：</h5>
  <ul>
    <li>您可以<a href="http://azure.microsoft.com/zh-tw/pricing/free-trial/?WT.mc_id=A261C142F">免費申請 Azure 帳戶</a> - 您將取得可試用付費 Azure 服務的額度，且即使在額度用完後，您仍可保留帳戶，並使用免費的 Azure 服務，例如「網站」。</li>
    <li>您可以<a href="http://azure.microsoft.com/zh-tw/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F">啟用 MSDN 訂戶權益</a> - 您的 MSDN 訂閱每月會提供您額度，您可以用在 Azure 付費服務。</li>
  <ul>
</div>

<a name="deploy"></a>
## 部署具有整合式 CDN 端點的雲端服務 ##

在本節中，您將在 Visual Studio 2013 中將預設 ASP.NET MVC 應用程式範本部署至雲端服務 Web 角色，然後將它與新的 CDN 端點整合。請遵循下列指示：

1. 在 Visual Studio 2013 中，從功能表列中移至 **[檔案] > [新增] > [專案] > [雲端] > [Microsoft Azure 雲端服務]**，以建立新的 Azure 雲端服務。命名並按一下 [**確定**]。

	![](media/cdn-cloud-service-with-cdn/cdn-cs-1-new-project.PNG)

2. 選取 [**ASP.NET Web 角色**]，然後按一下 **>** 按鈕。按一下 [確定]。

	![](media/cdn-cloud-service-with-cdn/cdn-cs-2-select-role.PNG)

3. 選取 [**MVC**]，然後按一下 [**確定**]。

	![](media/cdn-cloud-service-with-cdn/cdn-cs-3-mvc-template.PNG)

4. 現在，將此 Web 角色發行至 Azure 雲端服務。以滑鼠右鍵按一下雲端服務專案，選取 [**發行**]。

	![](media/cdn-cloud-service-with-cdn/cdn-cs-4-publish-a.png)

5. 如果您尚未登入 Microsoft Azure，請按一下 [**登入**] 按鈕。

	![](media/cdn-cloud-service-with-cdn/cdn-cs-5-publish-signin.png)

6. 在登入頁面中，使用您用來啟用 Azure 帳戶的 Microsoft 帳戶登入。
7. 登入後，按 [**下一步**]。

	![](media/cdn-cloud-service-with-cdn/cdn-cs-6-publish-signedin.png)

8. 如果您未建立雲端服務或儲存體帳戶，Visual Studio 會協助您建立兩者。在 [**Create Cloud Service and Account**] 對話方塊中，輸入想要的服務名稱。然後按一下 [**建立**]。

	![](media/cdn-cloud-service-with-cdn/cdn-cs-7-publish-createserviceandstorage.png)

9. 在發行設定頁面中，驗證設定並按一下 [**發行**]。

	![](media/cdn-cloud-service-with-cdn/cdn-cs-8-publish-finalize.png)

	>[WACOM.NOTE] 雲端服務的發行程序會花費很長時間。[啟用所有 Web 角色的 Web Deploy] 選項可快速 (但暫時) 提供更新給 Web 角色，加速偵測您的雲端服務。如需此選項的詳細資訊，請參閱[使用 Azure Tools 發行雲端服務](http://msdn.microsoft.com/zh-tw/library/ff683672.aspx)。

	當 [**Windows Azure Activity Log**] 顯示發行狀態為 [**已完成**] 時，您將建立與此雲端服務整合的 CDN 端點。 

1. 若要建立 CDN 端點，請登入 [Azure 管理入口網站](http://manage.windowsazure.com/)。 
2. 按一下 [**新增**] > [**應用程式服務**] > [**CDN**] > [**快速建立**]。選取 **http://*<servicename>*.cloudapp.net/cdn/**，然後按一下 [**建立**]。

	![](media/cdn-cloud-service-with-cdn/cdn-cs-10-createcdn.png)

	>[WACOM.NOTE] 建立 CDN 端點之後，Azure 入口網站會顯示其 URL 及與它整合的原始網域。不過，需要花費一些時間，新的 CDN 端點的設定才能完全傳播至所有 CDN 節點位置。 

	請注意，CDN 端點會繫結至雲端服務的路徑 **cdn/**。您可以在 **WebRole1** 專案中**cdn** 資料夾，或使用 URL 重寫來刪除此路徑的所有連入連結。在本教學課程中，您將採取後者的作法。

3. 回到 Azure 入口網站，在 [**CDN**] 索引標籤中，按一下您剛建立的 CDN 端點的名稱。

	![](media/cdn-cloud-service-with-cdn/cdn-cs-11-disablequerya.png)

3. 按一下 [**啟用查詢字串**]，在 CDN 快取中啟用查詢字串。啟用此選項後，將以個別項目來快取以不同查詢字串存取的相同連結。

	![](media/cdn-cloud-service-with-cdn/cdn-cs-12-disablequeryb.png)

	>[WACOM.NOTE] 雖然本節教學課程並不需要啟用查詢字串，但為了方便起見，請儘早這樣做，因為此處的任何變更需要花費很長時間才能傳播至所有 CDN 節點，您不希望任何未啟用查詢字串的內容塞滿 CDN 快取 (稍後會討論更新 CDN 內容)。

3. Ping 您的 CDN 端點，確定已傳播至 CDN 節點。可能要等待將近 1 小時，Ping 才有回應。

	![](media/cdn-cloud-service-with-cdn/cdn-cs-13-testcdn.png)

2. 回到 Visual Studio 2013，開啟 **WebRole1** 專案中的 **Web.config**，將下列程式碼加入至 `<system.webServer>` 標籤：  
	<pre class="prettyprint">
	<system.webServer>
	  <mark><rewrite>
	    <rules>
	      <rule name=&quot;RewriteIncomingCdnRequest&quot; stopProcessing=&quot;true&quot;>
	        <match url=&quot;^cdn/(.*)$&quot;/>
	        <action type=&quot;Rewrite&quot; url=&quot;{R:1}&quot;/>
	      </rule>
	    </rules>
	  </rewrite></mark>
      ...
	</system.webServer>
	</pre>

4. 重新發行雲端服務。以滑鼠右鍵按一下雲端服務專案，選取 [**發行**]。

	![](media/cdn-cloud-service-with-cdn/cdn-cs-4-publish-a.png)

1. 當發行狀態為 [**已完成**] 時，請開啟瀏覽器視窗並瀏覽至 **http://*<cdnName>*.vo.msecnd.net/Content/bootstrap.css**。在我的設定中，此 URL 為：

		http://az632148.vo.msecnd.net/Content/bootstrap.css

	對應至 CDN 端點上的下列原始 URL：

		http://cephalinservice.cloudapp.net/cdn/Content/bootstrap.css

	當我的 Web 應用程式中經過 URL 重寫之後，實際放入 CDN 快取中的檔案為：

		http://cephalinservice.cloudapp.net/Content/bootstrap.css

	瀏覽至 **http://*<cdnName>*.vo.msecnd.net/Content/bootstrap.css** 時，將提示您下載來自發行的 Web 應用程式的 bootstrap.css。 

	![](media/cdn-cloud-service-with-cdn/cdn-1-browser-access.PNG)

同樣地，您可以直接從 CDN 端點，以 **http://*<serviceName>*.cloudapp.net/** 存取任何可公開存取的 URL。例如：

-	/Script 路徑中的 .js 檔案
-	/Content 路徑中的任何內容檔案
-	任何控制器/動作 
-	任何含有查詢字串的 URL (若 CDN 端點已啟用查詢字串的話)

實際上，以上述設定來說，您可以從 **http://*<cdnName>*.vo.msecnd.net/** 代管整個雲端服務。如果我瀏覽至 **http://az632148.vo.msecnd.net/**，我可以從 Home/Index 取得動作結果。

![](media/cdn-cloud-service-with-cdn/cdn-2-home-page.PNG)

然而，這不表示透過 Azure CDN 來提供整個雲端服務一定是好辦法 (或通常是好辦法)。有幾點需要注意：

-	此作法需要公開整個網站，因為 Azure CDN 無法提供任何私人內容。
-	如果 CDN 端點因故離線，不論是排定的維護或使用者錯誤，則整個雲端服務都會離線，除非能夠將客戶重新導向至原始 URL **http://*<serviceName>*.cloudapp.net/**。 
-	就算使用自訂的 Cache-Control 設定 (請參閱[在雲端服務中設定靜態內容的快取設定](#caching))，CDN 端點也無法改善高度動態內容的效能。如果您嘗試從 CDN 端點載入首頁，如上所示，請注意，第一次載入預設首頁 (非常簡單的頁面) 至少需要 5 秒。設想，如果此頁面包含必須每分鐘更新的動態內容，客戶體驗有何影響。從 CDN 端點提供動態內容需要有較短的快取到期時間，這也說明 CDN 端點經常會發生快取遺漏。這會降低雲端服務的效能，也會折損 CDN 的效用。

替代方法是在雲端服務中依個別情況決定從 Azure CDN 提供什麼內容。總之，您已了解如何從 CDN 端點存取個別的內容檔案。我將在[透過 Azure CDN 從控制器動作提供內容](#controller)中說明如何透過 CDN 端點提供特定的控制器動作。

您可以指定更嚴格的 URL 重寫規則，以限制可透過 CDN 端點存取的內容。例如，若要將 URL 重寫限制在 *\Scripts* 資料夾，請如下所示變更上述重寫規則：   
<pre class="prettyprint">
<rule name=&quot;RewriteIncomingCdnRequest&quot; stopProcessing=&quot;true&quot;>
  <match url=&quot;^cdn/<mark>Scripts/</mark>(.*)$&quot;/>
  <action type=&quot;Rewrite&quot; url=&quot;<mark>Scripts/</mark>{R:1}&quot;/>
</rule>
</pre>

<a name="caching"></a>
## 在雲端服務中設定靜態內容的快取設定 ##

利用雲端服務中的 Azure CDN 整合，您可以指定如何在 CDN 端點中快取靜態內容。若要這樣做，請從 Web 角色專案 (例如 WebRole1) 開啟 *Web.config*，將 `<staticContent>` 元素加入至 `<system.webServer>`。以下的 XML 將快取設為 3 天過期。  
<pre class="prettyprint">
<system.webServer>
  <mark><staticContent>
    <clientCache cacheControlMode=&quot;UseMaxAge&quot; cacheControlMaxAge=&quot;3.00:00:00&quot;/>
  </staticContent></mark>
  ...
</system.webServer>
</pre>

這樣做時，雲端服務中的所有靜態檔案會在您的 CDN 快取中遵守相同規則。若要更精確控制快取設定，請將 *Web.config* 檔案加入至資料夾，並在檔案中新增您的設定。例如，將 *Web.config* 檔案加入至 *\Content* 資料夾，並將內容改成下列 XML：

	<?xml version="1.0"?>
	<configuration>
	  <system.webServer>
	    <staticContent>
	      <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="15.00:00:00"/>
	    </staticContent>
	  </system.webServer>
	</configuration>

此設定會將 *\Content* 資料夾中的所有靜態檔案快取 15 天。

如需有關如何設定 `<clientCache>` 元素的詳細資訊，請參閱[用戶端快取 <clientCache>](http://www.iis.net/configreference/system.webserver/staticcontent/clientcache) (英文)。

在[透過 Azure CDN 從控制器動作提供內容](#controller)中，我也會說明如何在 CDN 快取中設定控制器動作結果的快取設定。

<a name="controller"></a>
## 透過 Azure CDN 從控制器動作提供內容 ##

整合雲端服務 Web 角色與 Azure CDN 時，透過 Azure CDN 從控制器動作提供內容就非常簡單。[Maarten Balliauw](https://twitter.com/maartenballiauw) 在[使用 Windows Azure CDN 縮短網路延遲時間](http://channel9.msdn.com/events/TechDays/Techdays-2014-the-Netherlands/Reducing-latency-on-the-web-with-the-Windows-Azure-CDN) (英文) 中以一個有趣的 MemeGenerator 控制器來說明作法，而不直接透過 Azure CDN 提供雲端服務 (如上所示)。我在這裡簡單地重述一次。

假設您想在雲端服務中利用查克羅禮士年輕時的一張相片 ([Alan Light](http://www.flickr.com/photos/alan-light/218493788/) 拍攝) 來引起網路瘋傳：

![](media/cdn-cloud-service-with-cdn/cdn-5-memegenerator.PNG)

您有一個簡單的 `Index` 動作可讓客戶在照片上指定笑梗，再傳給此動作來引起網路瘋傳。因為是查克羅禮士，您預期此頁面一定會在全球造成一股旋風。這就是利用 Azure CDN 來提供半動態內容的一個最佳例子。 

請依照上述步驟來設定此控制器動作：

1. 在 *\Controllers* 資料夾中，建立一個新的 .cs 檔案稱為 *MemeGeneratorController.cs*，並將內容改成下列程式碼。請記得將反白部分換成您的 CDN 名稱。  
	<pre class="prettyprint">
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
	        static readonly Dictionary<string, Tuple<string ,string&gt;> Memes = new Dictionary<string, Tuple<string, string&gt;>();

	        public ActionResult Index()
	        {
	            return View();
	        }
	
	        [HttpPost, ActionName(&quot;Index&quot;)]
        	public ActionResult Index_Post(string top, string bottom)
	        {
	            var identifier = Guid.NewGuid().ToString();
	            if (!Memes.ContainsKey(identifier))
	            {
	                Memes.Add(identifier, new Tuple<string, string>(top, bottom));
	            }
	
	            return Content(&quot;<a href=\&quot;&quot; + Url.Action(&quot;Show&quot;, new {id = identifier}) + &quot;\&quot;>here&#39;s your meme</a>&quot;);
	        }


	        [OutputCache(VaryByParam = &quot;*&quot;, Duration = 1, Location = OutputCacheLocation.Downstream)]
	        public ActionResult Show(string id)
	        {
	            Tuple<string, string> data = null;
	            if (!Memes.TryGetValue(id, out data))
	            {
	                return new HttpStatusCodeResult(HttpStatusCode.NotFound);
	            }
	
	            if (Debugger.IsAttached) // Preserve the debug experience
	            {
	                return Redirect(string.Format(&quot;/MemeGenerator/Generate?top={0}&bottom={1}&quot;, data.Item1, data.Item2));
	            }
	            else // Get content from Azure CDN
	            {
	                return Redirect(string.Format(&quot;http://<mark><yourCdnName></mark>.vo.msecnd.net/MemeGenerator/Generate?top={0}&bottom={1}&quot;, data.Item1, data.Item2));
	            }
	        }

	        [OutputCache(VaryByParam = "*", Duration = 3600, Location = OutputCacheLocation.Downstream)]
	        public ActionResult Generate(string top, string bottom)
	        {
	            string imageFilePath = HostingEnvironment.MapPath(&quot;<mark>~/Content/chuck.bmp</mark>&quot;);
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
	            return File(ms.ToArray(), &quot;image/png&quot;);
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
	</pre>

2. 以滑鼠右鍵按一下預設的 `Index()` 動作，選取 [**加入檢視**]。

	![](media/cdn-cloud-service-with-cdn/cdn-6-addview.PNG)

3.  接受下列設定，按一下 [**加入**]。

	![](media/cdn-cloud-service-with-cdn/cdn-7-configureview.PNG)

4. 開啟新的 *Views\MemeGenerator\Index.cshtml*，將內容改成下列簡單的 HTML 來提交笑梗：

		<h2>Meme Generator</h2>
		
		<form action="" method="post">
		    <input type="text" name="top" placeholder="Enter top text here" />
		    <br />
		    <input type="text" name="bottom" placeholder="Enter bottom text here" />
		    <br />
		    <input class="btn" type="submit" value="Generate meme" />
		</form>

5. 重新發行雲端服務，然後在瀏覽器中瀏覽至 **http://*<serviceName>*.cloudapp.net/MemeGenerator/Index**。 

當您將表單值提交至 `/MemeGenerator/Index` 時，`Index_Post` 動作方法會傳回 `Show` 動作方法的連結，以及個別的輸入識別碼。按一下連結會執行下列程式碼：  
<pre class="prettyprint">
[OutputCache(VaryByParam = &quot;*&quot;, Duration = 1, Location = OutputCacheLocation.Downstream)]
public ActionResult Show(string id)
{
    Tuple<string, string> data = null;
    if (!Memes.TryGetValue(id, out data))
    {
        return new HttpStatusCodeResult(HttpStatusCode.NotFound);
    }

    if (Debugger.IsAttached) // Preserve the debug experience
    {
        return Redirect(string.Format(&quot;/MemeGenerator/Generate?top={0}&bottom={1}&quot;, data.Item1, data.Item2));
    }
    else // Get content from Azure CDN
    {
        return Redirect(string.Format(&quot;http://<mark><cdnName></mark>.vo.msecnd.net/MemeGenerator/Generate?top={0}&bottom={1}&quot;, data.Item1, data.Item2));
    }
}
</pre>

如果已附加本機偵錯程式，您將經由本機重新導向而享有一般的偵錯體驗。如果是在雲端服務中執行，則會重新導向至：

	http://<yourCDNName>.vo.msecnd.net/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>

對應至 CDN 端點上的下列原始 URL：

	http://<youCloudServiceName>.cloudapp.net/cdn/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>

先前套用 URL 重寫規則之後，實際快取至 CDN 端點的檔案為：

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

在您於[整合 Azure CDN 端點與 Azure 網站，並從 Azure CDN 提供網頁的靜態內容](#deploy)中所建立的 **WebRole1** 專案中，開啟 *App_Start\BundleConfig.cs*，然後查看 `bundles.Add()` 方法呼叫。

    public static void RegisterBundles(BundleCollection bundles)
    {
        bundles.Add(new ScriptBundle("~/bundles/jquery").Include(
                    "~/Scripts/jquery-{version}.js"));
		...
    }

第一個 `bundles.Add()` 陳述式將指令碼套件組合加入至虛擬目錄 `~/bundles/jquery`。然後，開啟 *Views\Shared\_Layout.cshtml*，查看指令碼套件組合標籤如何轉譯。您應該可以找到下列這一行 Razor 程式碼：

    @Scripts.Render("~/bundles/jquery")

當此 Razor 程式碼在 Azure Web 角色中執行時，它會類似如下轉譯指令碼套件組合的 `<script>` 標籤： 

    <script src="/bundles/jquery?v=FVs3ACwOLIVInrAl5sdzR2jrCDmVOWFbZMY6g6Q0ulE1"></script>

不過，當在 Visual Studio 中按 `F5` 執行時，則會個別轉譯套件組合中的每一個指令碼檔案 (在上述案例中，套件組合中只有一個指令碼檔案)：

    <script src="/Scripts/jquery-1.10.2.js"></script>

這樣可讓您在開發環境中進行 JavaScript 程式碼偵錯，同時減少並行的用戶端連線 (統合)，在實際執行環境中提升檔案下載效能 (縮製)。這是 Azure CDN 整合中保留的絕佳功能。此外，由於轉譯的套件組合已包含自動產生的版本字串，您可以仿照此功能，每當您透過 NuGet 更新 jQuery 版本時，就會以最快速度更新用戶端。

請遵循下列步驟來整合 ASP.NET 統合和縮製與 CDN 端點。

1. 回到 *App_Start\BundleConfig.cs*，修改 `bundles.Add()` 方法來使用不同的 [Bundle 建構函式](http://msdn.microsoft.com/zh-tw/library/jj646464.aspx) (此建構函式指定 CDN 位址)。若要這樣做，請將 `RegisterBundles` 方法定義改成下列程式碼：  
	<pre class="prettyprint">
	public static void RegisterBundles(BundleCollection bundles)
	{
	    <mark>bundles.UseCdn = true;
	    var version = System.Reflection.Assembly.GetAssembly(typeof(Controllers.HomeController))
	        .GetName().Version.ToString();
	    var cdnUrl = &quot;http://<yourCDNName>.vo.msecnd.net/{0}?v=&quot; + version;</mark>
	
	    bundles.Add(new ScriptBundle(&quot;~/bundles/jquery&quot;<mark>, string.Format(cdnUrl, &quot;bundles/jquery&quot;)</mark>).Include(
	                &quot;~/Scripts/jquery-{version}.js&quot;));
	
	    bundles.Add(new ScriptBundle(&quot;~/bundles/jqueryval&quot;<mark>, string.Format(cdnUrl, &quot;bundles/jqueryval&quot;)</mark>).Include(
	                &quot;~/Scripts/jquery.validate*&quot;));
	
	    // Use the development version of Modernizr to develop with and learn from. Then, when you&#39;re
	    // ready for production, use the build tool at http://modernizr.com to pick only the tests you need.
	    bundles.Add(new ScriptBundle(&quot;~/bundles/modernizr&quot;<mark>, string.Format(cdnUrl, &quot;bundles/modernizer&quot;)</mark>).Include(
	                &quot;~/Scripts/modernizr-*&quot;));
	
	    bundles.Add(new ScriptBundle(&quot;~/bundles/bootstrap&quot;<mark>, string.Format(cdnUrl, &quot;bundles/bootstrap&quot;)</mark>).Include(
	                &quot;~/Scripts/bootstrap.js&quot;,
	                &quot;~/Scripts/respond.js&quot;));
	
	    bundles.Add(new StyleBundle(&quot;~/Content/css&quot;<mark>, string.Format(cdnUrl, &quot;Content/css&quot;)</mark>).Include(
	                &quot;~/Content/bootstrap.css&quot;,
	                &quot;~/Content/site.css&quot;));
	}
	</pre>

	請記得將 `<yourCDNName>` 換成您的 Azure CDN 名稱。

	簡單地說，您正在設定 `bundles.UseCdn = true`，且已在每一個套件組合中加入謹慎建構的 CDN URL。例如，程式碼的第一個建構函式：

		new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "bundles/jquery"))

	就如同： 

		new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "http://<yourCDNName>.vo.msecnd.net/bundles/jquery?v=<W.X.Y.Z>"))

	此建構函式告知 ASP.NET 統合和縮製在本機偵錯時轉譯個別指令碼檔案，但使用指定的 CDN 位址來存取所提及的指令碼。不過，對於此謹慎建構的 CDN URL，請注意兩項重要特性：
	
	-	此 CDN URL 的來源是 `http://<yourCloudService>.cloudapp.net/bundles/jquery?v=<W.X.Y.Z>`，實際上就是雲端服務中指令碼套件組合的虛擬目錄。
	-	由於是使用 CDN 建構函式，套件組合的 CDN 指令碼標籤在轉譯的 URL 中已不再包含自動產生的版本字串。指令碼套件組合每次修改時，您都必須手動產生唯一的版本字串，以強制在 Azure CDN 上發生快取遺漏。同時，在部署套件組合之後，此唯一的版本字串在部署的整個存在期間內必須保持不變，讓 Azure CDN 的快取命中率達到最高。
	-	查詢字串 v=<W.X.Y.Z> 會從 Web 角色專案的 *Properties\AssemblyInfo.cs* 中提取。您的部署工作流程中可以包含每次發佈至 Azure 時就遞增組件版本。或者，您可以直接修改專案中的 *Properties\AssemblyInfo.cs*，使用萬用字元 '*' 表示每次建置時就自動遞增版本字串。例如：
	
			[assembly: AssemblyVersion("1.0.0.*")]
	
		在此可採取其他任何策略在部署的存在期間內產生唯一字串。

3. 重新發行雲端服務和存取首頁。
 
4. 檢視頁面的 HTML 程式碼。您應該會看到轉譯的 CDN URL，以及每次將變更重新發行至雲端服務時的唯一版本字串。例如：  
	<pre class="prettyprint">
	...

    <link href=&quot;http://az632148.vo.msecnd.net/Content/css?v=1.0.0.25449&quot; rel=&quot;stylesheet&quot;/>

    <script src=&quot;http://az632148.vo.msecnd.net/bundles/modernizer?v=1.0.0.25449&quot;></script>

	...

    <script src=&quot;http://az632148.vo.msecnd.net/bundles/jquery?v=1.0.0.25449&quot;></script>

    <script src=&quot;http://az632148.vo.msecnd.net/bundles/bootstrap?v=1.0.0.25449&quot;></script>

	...</pre>

5. 在 Visual Studio 中，按 `F5`，在 Visual Studio 中進行雲端服務偵錯。 

6. 檢視頁面的 HTML 程式碼。您仍然會看到每一個指令碼檔案個別地轉譯，所以在 Visual Studio 中享有一致的偵錯體驗。  
	<pre class="prettyprint">
	...

    &lt;link href=&quot;http://az632148.vo.msecnd.net/Content/css?v=1.0.0.25449&quot; rel=&quot;stylesheet&quot;/&gt;

    &lt;script src=&quot;http://az632148.vo.msecnd.net/bundles/modernizer?v=1.0.0.25449&quot;&gt;&lt;/script&gt;

	...

    &lt;script src=&quot;http://az632148.vo.msecnd.net/bundles/jquery?v=1.0.0.25449&quot;&gt;&lt;/script&gt;

    &lt;script src=&quot;http://az632148.vo.msecnd.net/bundles/bootstrap?v=1.0.0.25449&quot;&gt;&lt;/script&gt;

	...</pre>

<a name="fallback"></a>
## CDN URL 的後援機制 ##

當 Azure CDN 端點因故失敗時，您一定希望網頁能夠聰明地存取原始 Web 伺服器，當作後援選項來載入 JavaScript 或 Bootstrap。由於 CDN 無法使用而遺失網站的影像就已經夠嚴重，而遺失由指令碼和樣式表提供的重要頁面功能又更加嚴重。

[Bundle](http://msdn.microsoft.com/zh-tw/library/system.web.optimization.bundle.aspx) 類別包含一個稱為 [CdnFallbackExpression](http://msdn.microsoft.com/zh-tw/library/system.web.optimization.bundle.cdnfallbackexpression.aspx) 的屬性，可讓您設定 CDN 失敗時的後援機制。若要使用此屬性，請遵循下列步驟：

1. 在 Web 角色專案中，開啟 *App_Start\BundleConfig.cs* (其中，您已在每一個 [Bundle 建構函式](http://msdn.microsoft.com/zh-tw/library/jj646464.aspx)中加入 CDN URL)，使用下列反白的變更將後援機制加入至預設套件組合：  
	<pre class="prettyprint">
	public static void RegisterBundles(BundleCollection bundles)
	{
	    var version = System.Reflection.Assembly.GetAssembly(typeof(BundleConfig))
	        .GetName().Version.ToString();
	    var cdnUrl = &quot;http://cdnurl.vo.msecnd.net/.../{0}?&quot; + version;
	    bundles.UseCdn = true;
	
	    bundles.Add(new ScriptBundle(&quot;~/bundles/jquery&quot;, string.Format(cdnUrl, &quot;bundles/jquery&quot;)) 
					<mark>{ CdnFallbackExpression = &quot;window.jquery&quot; }</mark>
	                .Include(&quot;~/Scripts/jquery-{version}.js&quot;));
	
	    bundles.Add(new ScriptBundle(&quot;~/bundles/jqueryval&quot;, string.Format(cdnUrl, &quot;bundles/jqueryval&quot;)) 
					<mark>{ CdnFallbackExpression = &quot;$.validator&quot; }</mark>
	            	.Include(&quot;~/Scripts/jquery.validate*&quot;));
	
	    // Use the development version of Modernizr to develop with and learn from. Then, when you&#39;re
	    // ready for production, use the build tool at http://modernizr.com to pick only the tests you need.
	    bundles.Add(new ScriptBundle(&quot;~/bundles/modernizr&quot;, string.Format(cdnUrl, &quot;bundles/modernizer&quot;)) 
					<mark>{ CdnFallbackExpression = &quot;window.Modernizr&quot; }</mark>
					.Include(&quot;~/Scripts/modernizr-*&quot;));
	
	    bundles.Add(new ScriptBundle(&quot;~/bundles/bootstrap&quot;, string.Format(cdnUrl, &quot;bundles/bootstrap&quot;)) 	
					<mark>{ CdnFallbackExpression = &quot;$.fn.modal&quot; }</mark>
	        		.Include(
		              		&quot;~/Scripts/bootstrap.js&quot;,
		              		&quot;~/Scripts/respond.js&quot;));
	
	    bundles.Add(new StyleBundle(&quot;~/Content/css&quot;, string.Format(cdnUrl, &quot;Content/css&quot;)).Include(
	                &quot;~/Content/bootstrap.css&quot;,
	                &quot;~/Content/site.css&quot;));
	}</pre>

	當 `CdnFallbackExpression` 不是 null 時，指令碼會插入 HTML 中來測試是否已成功載入套件組合，如果不是，則直接從原始 Web 伺服器存取套件組合。此屬性必須設為 JavaScript 運算式來測試個別的 CDN 套件組合是否正確載入。測試每一個套件組合所需的運算式根據內容而不同。在以上的預設套件組合中：
	
	-	`window.jquery` 定義於 jquery-{version}.js 中
	-	`$.validator` 定義於 jquery.validate.js 中
	-	`window.Modernizr` 定義於 modernizer-{version}.js 中
	-	`$.fn.modal` 定義於 bootstrap.js 中
	
	您可能發現到我沒有為 `~/Cointent/css` 套件組合設定 CdnFallbackExpression。這是因為目前 [System.Web.Optimization 中有錯誤](https://aspnetoptimization.codeplex.com/workitem/104)，導致插入後援 CSS 的 `<script>` 標籤，而非預期的 `<link>` 標籤。
	
	不過，[Ember 顧問團](https://github.com/EmberConsultingGroup) (英文) 提供一套良好的[樣式套件組合後援](https://github.com/EmberConsultingGroup/StyleBundleFallback) (英文)。 

2. 若要使用 CSS 的解決方案，請在 Web 角色專案的 *App_Start* 資料夾中，建立新的 .cs 檔案，稱為 *StyleBundleExtensions.cs*，並將內容改成 [GitHub 提供的程式碼](https://github.com/EmberConsultingGroup/StyleBundleFallback/blob/master/Website/App_Start/StyleBundleExtensions.cs)。 

4. 在 *App_Start\StyleFundleExtensions.cs* 中，將命名空間重新命名為您的 Web 角色名稱 (例如 **WebRole1**)。 

3. 回到 `App_Start\BundleConfig.cs`，將最後一個 `bundles.Add` 陳述式修改為下列反白的程式碼：  
	<pre class="prettyprint">
	bundles.Add(new StyleBundle("~/Content/css", string.Format(cdnUrl, "Content/css"))
	    <mark>.IncludeFallback("~/Content/css", "sr-only", "width", "1px")</mark>
	    .Include(
	          "~/Content/bootstrap.css",
	          "~/Content/site.css"));
	</pre>

	這個新的延伸方法採用相同的概念，將指令碼插入 HTML 中來檢查 DOM，尋找 CSS 套件組合中定義的相符類別名稱、規則名稱和規則值，而如果找不到相符項，則退一步存取原始 Web 伺服器。

4. 重新發行雲端服務和存取首頁。 
5. 檢視頁面的 HTML 程式碼。您應該會看到插入的指令碼類似如下：    
	<pre class="prettyprint">...
	
		&lt;link href=&quot;http://az632148.vo.msecnd.net/Content/css?v=1.0.0.25474&quot; rel=&quot;stylesheet&quot;/&gt;
	<mark>&lt;script&gt;(function() {
	                var loadFallback,
	                    len = document.styleSheets.length;
	                for (var i = 0; i &lt; len; i++) {
	                    var sheet = document.styleSheets[i];
	                    if (sheet.href.indexOf(&#39;http://az632148.vo.msecnd.net/Content/css?v=1.0.0.25474&#39;) !== -1) {
	                        var meta = document.createElement(&#39;meta&#39;);
	                        meta.className = &#39;sr-only&#39;;
	                        document.head.appendChild(meta);
	                        var value = window.getComputedStyle(meta).getPropertyValue(&#39;width&#39;);
	                        document.head.removeChild(meta);
	                        if (value !== &#39;1px&#39;) {
	                            document.write(&#39;&lt;link href=&quot;/Content/css&quot; rel=&quot;stylesheet&quot; type=&quot;text/css&quot; /&gt;&#39;);
	                        }
	                    }
	                }
	                return true;
	            }())||document.write(&#39;&lt;script src=&quot;/Content/css&quot;&gt;&lt;\/script&gt;&#39;);&lt;/script&gt;</mark>
	
	    &lt;script src=&quot;http://az632148.vo.msecnd.net/bundles/modernizer?v=1.0.0.25474&quot;&gt;&lt;/script&gt;
	<mark>&lt;script&gt;(window.Modernizr)||document.write(&#39;&lt;script src=&quot;/bundles/modernizr&quot;&gt;&lt;\/script&gt;&#39;);&lt;/script&gt;</mark>
	
	...	
	
	    &lt;script src=&quot;http://az632148.vo.msecnd.net/bundles/jquery?v=1.0.0.25474&quot;&gt;&lt;/script&gt;
	<mark>&lt;script&gt;(window.jquery)||document.write(&#39;&lt;script src=&quot;/bundles/jquery&quot;&gt;&lt;\/script&gt;&#39;);&lt;/script&gt;</mark>
	
	    &lt;script src=&quot;http://az632148.vo.msecnd.net/bundles/bootstrap?v=1.0.0.25474&quot;&gt;&lt;/script&gt;
	<mark>&lt;script&gt;($.fn.modal)||document.write(&#39;&lt;script src=&quot;/bundles/bootstrap&quot;&gt;&lt;\/script&gt;&#39;);&lt;/script&gt;</mark>
	
	...
	</pre>

	請注意，針對 CSS 套件組合插入的指令碼，仍在這一行中包含 `CdnFallbackExpression` 屬性殘留的遊蕩部分：

        }())||document.write('<script src="/Content/css"><\/script>');</script>

	但因為 || 運算式的開頭部分一定會傳回 true (緊鄰的上一行)，所以 document.write() 函數永遠不會執行。

# 相關資訊 #
- [Azure 內容傳遞網路 (CDN) 概觀](http://msdn.microsoft.com/library/azure/ff919703.aspx)
- [在 Web 應用程式中從 Azure CDN 提供內容](http://azure.microsoft.com/zh-tw/Documentation/Articles/cdn-serve-content-from-cdn-in-your-web-application/)
- [整合 Azure 網站與 Azure CDN](http://azure.microsoft.com/zh-tw/documentation/articles/cdn-websites-with-cdn/)
- [ASP.NET 統合和縮製](http://www.asp.net/mvc/tutorials/mvc-4/bundling-and-minification)
- [使用 Azure 的 CDN](http://azure.microsoft.com/zh-tw/documentation/articles/cdn-how-to-use/)
