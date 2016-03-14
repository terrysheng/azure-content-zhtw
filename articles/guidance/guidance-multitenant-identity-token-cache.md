<properties
   pageTitle="在多組織用戶共享應用程式中快取存取權杖 | Microsoft Azure"
   description="快取用於叫用後端 Web API 的存取權杖"
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/16/2016"
   ms.author="mwasson"/>


# 在多組織用戶共享應用程式中快取存取權杖

本文是[系列文章的其中一篇]。這一系列另外還附有完整的[範例應用程式]。

相對於取得 OAuth 存取權杖，它的成本較高，因為它需要向權杖端點進行 HTTP 要求。因此，盡可能地快取權杖是不錯的做法。[Azure AD 驗證程式庫][ADAL] (ADAL) 會自動快取從 Azure AD 取得的權杖，包括重新整理權杖。

ADAL 提供預設的權杖快取實作。不過，此權杖快取是適用於原生用戶端應用程式，而「不」適用於 Web 應用程式：

-	它是靜態的執行個體，且不具備執行緒安全。
-	它不適用於大量的使用者，因為所有使用者的權杖都會移入相同的字典。
-	它無法在伺服器陣列中的 Web 伺服器之間共用。

相反地，您應該實作衍生自 ADAL `TokenCache` 類別，但適用於伺服器環境，並且在不同的使用者權杖之間提供所需之隔離層級的自訂權杖快取。

`TokenCache` 類別會儲存權杖的字典，並以簽發者、資源、用戶端識別碼和使用者編制索引。自訂權杖快取應該將此字典寫入至備份存放區，例如 Redis 快取。

在 Tailspin Surveys 應用程式中，`DistributedTokenCache` 類別會實作權杖快取。此實作使用 ASP.NET Core 1.0. 的 [IDistributedCache][distributed-cache] 抽象概念。這樣一來，任何的 `IDistributedCache` 實作都可作為備份存放區。

-	根據預設，Surveys 應用程式會使用 Redis 快取。
-	針對單一執行個體的 Web 伺服器，您可以使用 ASP.NET Core 1.0 [In Memory Caching (記憶體內部快取)][in-memory-cache]。(針對在開發期間於本機執行的應用程式，這也是個不錯的選項。)

> [AZURE.NOTE] 目前 Redis 快取不支援 .NET Core。

`DistributedTokenCache` 會將快取資料在備份存放區中儲存為索引鍵/值組。索引鍵是使用者識別碼加上用戶端識別碼，因此備份存放區會為每個唯一的使用者/用戶端組合保存個別的快取資料。

![權杖快取](media/guidance-multitenant-identity/token-cache.png)

備份存放區是由使用者進行分割。針對每個 HTTP 要求，會從備份存放區讀取該使用者的權杖，然後載入至 `TokenCache` 字典。若將 Redis 作為備份存放區，在伺服器陣列中的每個伺服器執行個體會讀取/寫入相同的快取，而此方法適用於許多使用者。

## 加密快取的權杖

權杖是敏感性資料，因為它們會授與使用者資源的存取權限。(此外，不同於使用者密碼，您不能只儲存權杖的雜湊。) 因此，保護權杖不遭到洩漏是非常重要的。Redis 備份的快取是以密碼保護，但如果有人取得密碼，他們就可以取得所有快取的存取權杖。基於該理由，`DistributedTokenCache` 會加密其寫入至備份存放區的所有項目。加密是使用 ASP.NET Core 1.0 [Data Protection (資料保護)][data-protection] API 來完成。

> [AZURE.NOTE] 如果您部署到 Azure 網站，加密金鑰會備份至網路儲存體，並同步處理所有機器 (請參閱 [Key Management (金鑰管理)][key-management])。根據預設，在 Azure 網站中執行時，金鑰不會加密，但您可以[使用 X.509 憑證啟用加密][x509-cert-encryption]。


## DistributedTokenCache 實作

[DistributedTokenCache][DistributedTokenCache] 類別衍生自 ADAL [TokenCache][tokencache-class] 類別。

在建構函式中，`DistributedTokenCache` 會為目前的使用者建立索引鍵，並從備份存放區中載入快取：

```csharp
public DistributedTokenCache(
    ClaimsPrincipal claimsPrincipal,
    IDistributedCache distributedCache,
    ILoggerFactory loggerFactory,
    IDataProtectionProvider dataProtectionProvider)
    : base()
{
    _claimsPrincipal = claimsPrincipal;
    _cacheKey = BuildCacheKey(_claimsPrincipal);
    _distributedCache = distributedCache;
    _logger = loggerFactory.CreateLogger<DistributedTokenCache>();
    _protector = dataProtectionProvider.CreateProtector(typeof(DistributedTokenCache).FullName);
    AfterAccess = AfterAccessNotification;
    LoadFromCache();
}
```

索引鍵是以使用者識別碼與用戶端識別碼串連建立。這兩者皆取自於使用者的 `ClaimsPrincipal` 中找到的宣告：

```csharp
private static string BuildCacheKey(ClaimsPrincipal claimsPrincipal)
{
    string clientId = claimsPrincipal.FindFirstValue("aud", true);
    return string.Format(
        "UserId:{0}::ClientId:{1}",
        claimsPrincipal.GetObjectIdentifierValue(),
        clientId);
}
```

若要載入快取資料，請從備份存放區讀取序列化的 Blob，並呼叫 `TokenCache.Deserialize` 將 Blob 轉換為快取資料。

```csharp
private void LoadFromCache()
{
    byte[] cacheData = _distributedCache.Get(_cacheKey);
    if (cacheData != null)
    {
        this.Deserialize(_protector.Unprotect(cacheData));
    }
}
```

每當 ADAL 存取快取，它會引發 `AfterAccess` 事件。如果快取資料已變更，`HasStateChanged` 屬性為 true。在該情況下，請更新備份存放區以反映變更，然後將 `HasStateChanged` 設為 false。

```csharp
public void AfterAccessNotification(TokenCacheNotificationArgs args)
{
    if (this.HasStateChanged)
    {
        try
        {
            if (this.Count > 0)
            {
                _distributedCache.Set(_cacheKey, _protector.Protect(this.Serialize()));
            }
            else
            {
                // There are no tokens for this user/client, so remove the item from the cache.
                _distributedCache.Remove(_cacheKey);
            }
            this.HasStateChanged = false;
        }
        catch (Exception exp)
        {
            _logger.WriteToCacheFailed(exp);
            throw;
        }
    }
}
```

TokenCache 會傳送兩個其他事件：

- `BeforeWrite`。在 ADAL 寫入至快取前會立即呼叫。您可以使用此事件來實作並行存取策略
- `BeforeAccess`。在 ADAL 從快取讀取前會立即呼叫。您可以在這裡重新載入快取，以取得最新版本。

在我們的案例中，我們決定不處理這兩個事件。

- 針對並行存取，以最後寫入者為準。這是可行的，因為存取是針對每個使用者+用戶端獨立儲存，因此只有在同一使用者擁有兩個並行登入工作階段時才會發生衝突。
- 針對讀取，我們會在每次要求時載入快取。要求是短時間的。若快取在該時間內遭到修改，下一個要求將會挑選新的值。

<!-- links -->
[ADAL]: https://msdn.microsoft.com/library/azure/jj573266.aspx
[data-protection]: https://docs.asp.net/en/latest/security/data-protection/index.html
[distributed-cache]: https://docs.asp.net/en/latest/fundamentals/distributed-cache.html
[DistributedTokenCache]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.TokenStorage/DistributedTokenCache.cs
[key-management]: https://docs.asp.net/en/latest/security/data-protection/configuration/default-settings.html
[in-memory-cache]: https://docs.asp.net/en/latest/fundamentals/caching.html
[tokencache-class]: https://msdn.microsoft.com/library/azure/microsoft.identitymodel.clients.activedirectory.tokencache.aspx
[x509-cert-encryption]: https://docs.asp.net/en/latest/security/data-protection/implementation/key-encryption-at-rest.html#x-509-certificate
[系列文章的其中一篇]: guidance-multitenant-identity.md
[範例應用程式]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps

<!---HONumber=AcomDC_0302_2016-->