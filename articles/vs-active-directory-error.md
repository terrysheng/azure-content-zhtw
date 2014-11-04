<properties title="驗證偵測期間發生錯誤" pageTitle="驗證偵測期間發生錯誤" metaKeywords="" description="" services="active-directory" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="active-directory" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb"></tags>

### 驗證偵測期間發生錯誤

偵測先前的驗證程式碼時，精靈偵測到不相容的驗證類型。

##### 檢查什麼？

精靈會嘗試偵測舊版 Visual Studio 所設定的驗證程式碼版本。如果收到此錯誤，表示您的專案包含不相容的驗證類型。精靈會從舊版 Visual Studio 中偵測下列驗證類型：

-   Windows 驗證
-   個別使用者帳戶
-   組織帳戶

為偵測 MVC 專案中的「Windows 驗證」，精靈會在您的 **web.config** 檔案中尋找 `authentication` 元素。

<pre class="prettyprint">
&lt;configuration&gt;
&lt;system.web&gt;
&lt;authentication mode=&quot;Windows&quot; /&gt;
&lt;/system.web&gt;
&lt;/configuration&gt;
</pre>

為偵測 Web API 專案中的「Windows 驗證」，精靈會在您專案的 **.csproj** 檔案中尋找 `IISExpressWindowsAuthentication` 元素：

<pre class="prettyprint">
&lt;Project&gt;
&lt;PropertyGroup&gt;
&lt;IISExpressWindowsAuthentication&gt;enabled&lt;/IISExpressWindowsAuthentication&gt;
&lt;/PropertyGroup&gt;
&lt;/Project&gt;
</pre>

為偵測「個別使用者帳戶」驗證，精靈會在您的 **Packages.config** 檔案中尋找 package 元素。

<pre class="prettyprint">
&lt;packages&gt;
&lt;package id=&quot;Microsoft.AspNet.Identity.EntityFramework&quot; version=&quot;2.1.0&quot; targetFramework=&quot;net45&quot; /&gt;
&lt;/packages&gt;
</pre>

為偵測舊式「組織帳戶」驗證，精靈會在 **web.config** 中尋找下列元素：

<pre class="prettyprint">
&lt;configuration*gt;
&lt;appSettings&gt;
&lt;add key=&quot;ida:Realm&quot; value=&quot;***&quot; /&gt;
&lt;/appSettings&gt;
&lt;/configuration&gt;
</pre>

若要變更驗證類型，請移除不相容的驗證類型，然後重新執行精靈。

如需詳細資訊，請參閱 [Azure AD 的驗證案例][Azure AD 的驗證案例]。

  [Azure AD 的驗證案例]: http://msdn.microsoft.com/library/azure/dn499820.aspx
