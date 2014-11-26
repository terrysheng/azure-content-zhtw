<properties title="開始使用 Active Directory 驗證" pageTitle="" metaKeywords="Azure, Getting Started, Active Directory" description="" services="active-directory" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="active-directory" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb" />

> [AZURE.SELECTOR]
>
> -   [開始使用][開始使用]
> -   [發生什麼情形][發生什麼情形]

## 開始使用 Azure Active Directory (.NET 專案)

##### 存取控制器之前需要驗證

專案中的所有控制器都加上 **Authorize** 屬性做裝飾。此屬性要求使用者必須經過驗證才能存取這些控制器。若要允許以匿名方式存取控制器，請從控制器中移除此屬性。如果您要以更精確地設定權限，請將此屬性套用至每一個需要授權的方法，而非套用至控制器類別。

##### 加入 SignIn / SignOut 控制項

若要將 SignIn/SignOut 控制項加入至檢視，您可以使用 \*\*\_LoginPartial.cshtml\*\* 部分檢視將此功能加入至您的其中一個檢視。以下是加入至標準 \*\*\_Layout.cshtml\*\* 檢視的功能範例(請注意 div 中具有類別 navbar-collapse 的最後一個元素)：

<pre class="prettyprint">
&lt;!DOCTYPE html&gt; 
&lt;html&gt; 
&lt;head&gt; 
&lt;meta charset=&quot;utf-8&quot; /&gt; 
&lt;meta name=&quot;viewport&quot; content=&quot;width=device-width, initial-scale=1.0&quot;&gt; 
&lt;title&gt;@ViewBag.Title - My ASP.NET Application&lt;/title&gt; 
@Styles.Render(&quot;~/Content/css&quot;) 
@Scripts.Render(&quot;~/bundles/modernizr&quot;) 
&lt;/head&gt; 
&lt;body&gt; 
&lt;div class=&quot;navbar navbar-inverse navbar-fixed-top&quot;&gt; 
&lt;div class=&quot;container&quot;&gt; 
&lt;div class=&quot;navbar-header&quot;&gt; 
&lt;button type=&quot;button&quot; class=&quot;navbar-toggle&quot; data-toggle=&quot;collapse&quot; data-target=&quot;.navbar-collapse&quot;&gt; 
&lt;span class=&quot;icon-bar&quot;&gt;&lt;/span&gt; 
&lt;span class=&quot;icon-bar&quot;&gt;&lt;/span&gt; 
&lt;span class=&quot;icon-bar&quot;&gt;&lt;/span&gt; 
&lt;/button&gt; 
@Html.ActionLink(&quot;Application name&quot;, &quot;Index&quot;, &quot;Home&quot;, new { area = &quot;&quot; }, new { @class = &quot;navbar-brand&quot; }) 
&lt;/div&gt; 
&lt;div class=&quot;navbar-collapse collapse&quot;&gt; 
&lt;ul class=&quot;nav navbar-nav&quot;&gt; 
&lt;li&gt;@Html.ActionLink(&quot;Home&quot;, &quot;Index&quot;, &quot;Home&quot;)&lt;/li&gt; 
&lt;li&gt;@Html.ActionLink(&quot;About&quot;, &quot;About&quot;, &quot;Home&quot;)&lt;/li&gt; 
&lt;li&gt;@Html.ActionLink(&quot;Contact&quot;, &quot;Contact&quot;, &quot;Home&quot;)&lt;/li&gt; 
&lt;/ul&gt; 
@Html.Partial(&quot;_LoginPartial&quot;) 
&lt;/div&gt; 
&lt;/div&gt; 
&lt;/div&gt; 
&lt;div class=&quot;container body-content&quot;&gt; 
@RenderBody() 
&lt;hr /&gt; 
&lt;footer&gt; 
&lt;p&gt;© @DateTime.Now.Year - My ASP.NET Application&lt;/p&gt; 
&lt;/footer&gt; 
&lt;/div&gt; 
@Scripts.Render(&quot;~/bundles/jquery&quot;) 
@Scripts.Render(&quot;~/bundles/bootstrap&quot;) 
@RenderSection(&quot;scripts&quot;, required:false) 
&lt;/body&gt; 
&lt;/html&gt;
</pre>

[深入了解 Azure Active Directory][深入了解 Azure Active Directory]

  [開始使用]: /documentation/articles/vs-active-directory-dotnet-getting-started/
  [發生什麼情形]: /documentation/articles/vs-active-directory-dotnet-what-happened/
  [深入了解 Azure Active Directory]: http://azure.microsoft.com/services/active-directory/
