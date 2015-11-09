<properties 
   pageTitle="快取 ASP.NET 工作階段狀態提供者"
   description="了解如何使用 Azure Redis 快取來儲存 ASP.NET 工作階段狀態"
   services="redis-cache"
   documentationCenter="na"
   authors="steved0x"
   manager="dwrede"
   editor="tysonn" />
<tags 
   ms.service="cache"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="cache-redis"
   ms.workload="tbd"
   ms.date="10/23/2015"
   ms.author="sdanie" />

# Azure Redis 快取的 ASP.NET 工作階段狀態提供者

Azure Redis 快取提供工作階段狀態提供者，可讓您用來將工作階段狀態儲存在快取中，而不是記憶體內或 SQL Server 資料庫中。若要使用快取工作階段狀態提供者，請先設定快取，再使用「Redis 快取工作階段狀態 NuGet 套件」設定 ASP.NET 應用程式的快取。

在實際的雲端應用程式中，避免儲存使用者工作階段某種形式的狀態通常並非理想做法，但某些方法會比其他方法更加影響效能和延展性。如果您需要儲存狀態，最好的方法是將狀態的數量控制得較低，並將其儲存在 Cookie 中。如果此方法不可行，次佳的方法是使用 ASP.NET 工作階段狀態搭配提供者，進行分散式的記憶體中快取。從效能和延展性的觀點來看，最差的解決方法是使用資料庫備份的工作階段狀態提供者。本主題提供使用 Azure Redis 快取的 ASP.NET 工作階段狀態提供者的指引。如需其他工作階段狀態選項的相關資訊，請參閱 [ASP.NET 工作階段狀態選項](#aspnet-session-state-options)。

## 將 ASP.NET 工作階段狀態儲存在快取中

若要在 Visual Studio 中使用「Redis 快取工作階段狀態 NuGet 封裝」來設定用戶端應用程式，請在 [方案總管] 中的專案上按一下滑鼠右鍵，然後選擇 [管理 NuGet 封裝]。

![Azure Redis 快取管理 NuGet 封裝](./media/cache-asp.net-session-state-provider/IC729541.png)

在 [線上搜尋] 文字方塊中輸入 **RedisSessionStateProvider**，從結果中選取後按一下 [安裝]。

>[AZURE.IMPORTANT]如果您使用高階層的叢集功能，則必須使用 [RedisSessionStateProvider](https://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider) 2.0.0 或更高版本，否則會擲回例外狀況。這是一項重大變更。如需詳細資訊，請參閱 [v2.0.0 重大變更詳細資料](https://github.com/Azure/aspnet-redis-providers/wiki/v2.0.0-Breaking-Change-Details) (英文)。

![Azure Redis 快取工作階段狀態提供者](./media/cache-asp.net-session-state-provider/IC751730.png)

「Redis 工作階段狀態提供者 NuGet 封裝」對「StackExchange.Redis.StrongName 封裝」有相依性。如果「StackExchange.Redis.StrongName 封裝」不在專案中，將會予以安裝。請注意，除了強式名稱的「StackExchange.Redis.StrongName 封裝」之外，另外還有 StackExchange.Redis 非強式名稱的版本。如果您的專案是使用非強式名稱的 StackExchange.Redis 版本，您必須在安裝「Redis 工作階段狀態提供者 NuGet 封裝」之前或之後將它解除安裝，否則專案中會出現命名衝突。如需這些封裝的相關詳細資訊，請參閱[設定 .NET 快取用戶端](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients)。

NuGet 封裝會下載並加入需要的組件參考，並將下列區段加入至您的 web.config 檔案，該檔案包含 ASP.NET 應用程式使用 Redis 快取工作階段狀態提供者所需的組態。

    <sessionStatemode="Custom" customProvider="MySessionStateStore">
        <providers>
        <!--
		<add name="MySessionStateStore"
     	  	host = "127.0.0.1" [String]
    		port = "" [number]
    		accessKey = "" [String]
    		ssl = "false" [true|false]
    		throwOnError = "true" [true|false]
    		retryTimeoutInMilliseconds = "0" [number]
    		databaseId = "0" [number]
    		applicationName = "" [String]
    		connectionTimeoutInMilliseconds = "5000" [number]
    		operationTimeoutInMilliseconds = "5000" [number]
		/>
        -->
		<add name="MySessionStateStore"type="Microsoft.Web.Redis.RedisSessionStateProvider"host="127.0.0.1"accessKey="" ssl="false"/>
        </providers>
    </sessionState>

標示註解的區段可提供屬性的範例和每個屬性的範例設定。

以來自 Microsoft Azure Preview 入口網站之快取刀鋒視窗的值來設定屬性，並視需要設定其他值。如需存取快取屬性的指示，請參閱[設定 Redis 快取設定](cache-configure.md#configure-redis-cache-settings)。

-	**主機** – 指定您的快取端點。
-	**連接埠** – 使用您的非 SSL 連接埠或 SSL 連接埠，依 ssl 設定而定。
-	**accessKey** – 用於快取的主要或次要金鑰。
-	**ssl** – 如果您想要使用 ssl 保護快取/用戶端通訊則為 true，否則為 false。請務必指定正確的連接埠。
	-	預設會為新快取停用非 SSL 連接埠。請於此設定指定為 true，使用 SSL 連接埠。如需啟用非 SSL 連接埠的相關詳細資訊，請參閱[設定快取](cache-configure.md)主題中的[存取連接埠](cache-configure.md#access-ports)一節。
-	**throwOnError** – true (如果您想在事件失敗時擲出例外狀況) 或 false (如果您想在作業失敗時為無訊息模式)。您可以核取靜態 Microsoft.Web.Redis.RedisSessionStateProvider.LastException 屬性以檢查失敗。預設值是 true。
-	**retryTimeoutInMilliseconds** – 會在此間隔期間 (以毫秒指定) 重試失敗的作業。第一次重試會在 20 毫秒後發生，然後每秒進行重試，直到 retryTimeoutInMilliseconds 間隔到期為止。緊接著此間隔之後，作業會進行最後一次重試。如果作業仍失敗，會視 throwOnError 設定將例外狀況擲回給呼叫者。預設值為 0，表示不會重試。
-	**databaseId** – 指定快取輸出資料所使用的資料庫。若未指定，就會使用預設值 0。
-	**applicationName** – 金鑰在 redis 中會儲存為 `{<Application Name>_<Session ID>}_Data`。這可讓多個應用程式共用同一金鑰。此參數是選擇性的，如果您未提供，將會使用預設值。
-	**connectionTimeoutInMilliseconds** – 此設定可讓您覆寫 StackExchange.Redis 用戶端中的 connectTimeout 設定。若未指定，將會使用預設的 connectTimeout 設定為 5000。如需詳細資訊，請參閱 [StackExchange.Redis 設定模型](http://go.microsoft.com/fwlink/?LinkId=398705) (英文)。
-	**operationTimeoutInMilliseconds** – 此設定可讓您覆寫 StackExchange.Redis 用戶端中的 syncTimeout 設定。若未指定，將會使用預設的 syncTimeout 設定為 1000。如需詳細資訊，請參閱 [StackExchange.Redis 設定模型](http://go.microsoft.com/fwlink/?LinkId=398705) (英文)。
							
如需這些屬性的相關詳細資訊，請參閱[發佈 Redis 的 ASP.NET 工作階段狀態提供者](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx) (英文) 上的原始部落格文章公告。

別忘記備註您 web.config 中的標準 InProc 工作階段狀態提供者區段。

    <!-- <sessionState mode="InProc" 
         customProvider="DefaultSessionProvider">
         <providers>
            <add name="DefaultSessionProvider" 
                  type="System.Web.Providers.DefaultSessionStateProvider, 
                        System.Web.Providers, Version=1.0.0.0, Culture=neutral, 
                        PublicKeyToken=31bf3856ad364e35" 
                  connectionStringName="DefaultConnection" />
          </providers>
    </sessionState> -->

一旦執行了這些步驟，您的應用程式就會設定為使用 Redis 快取工作階段狀態提供者。當您在應用程式中使用工作階段狀態時，會儲存在 Azure Redis 快取執行個體中。

>[AZURE.NOTE]請注意，儲存在快取中的資料必須可序列化，這一點與可以儲存在預設記憶體中 ASP.NET 工作階段狀態提供者的資料不同。使用 Redis 的工作階段狀態提供者時，請確定儲存在工作階段狀態中的資料類型為可序列化。

## ASP.NET 工作階段狀態選項

- 記憶體中工作階段狀態提供者 – 此提供者會將工作階段狀態儲存在記憶體中。使用此提供者的好處是它既簡單又快速。不過，您在使用記憶體中提供者時將無法調整 Web Apps，因為它不是分散式的。

- SQL Server 工作階段狀態提供者 – 此提供者會將工作階段狀態儲存在 SQL Server 中。如果您想要在永續性儲存體中保存工作階段狀態，應使用此提供者。您可以調整您的 Web 應用程式，但將 SQL Server 用於工作階段，將對您 Web 應用程式的效能造成影響。

- 分散式記憶體中工作階段狀態提供者，例如 Redis 快取工作階段狀態提供者 – 此提供者可讓您同時兼顧兩方面。您的 Web 應用程式可擁有簡單、快速而可調整的工作階段狀態提供者。不過，您必須考量到，此提供者會將工作階段狀態儲存在快取中，且您的應用程式必須考量與分散式記憶體中快取 (例如暫時性網路失敗) 通訊時的所有相關特性。如需使用快取的最佳作法，請參閱 Microsoft 模式和作法 [Azure 雲端應用程式設計和實作指引](https://github.com/mspnp/azure-guidance)中的[快取指引](https://github.com/mspnp/azure-guidance/blob/master/Caching.md) (英文)。

如需工作階段狀態和其他最佳作法的相關詳細資訊，請參閱 [Web 開發最佳作法 (使用 Azure 建置實際的雲端應用程式)](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/web-development-best-practices) (英文)。

## 後續步驟

請查看 [Azure Redis 快取的 ASP.NET 輸出快取提供者](cache-asp.net-output-cache-provider.md)。

<!---HONumber=Nov15_HO1-->