<properties title="Split and Merge Service Tutorial" pageTitle="Azure SQL 分割及合併服務教學課程" description="Splitting and Merging with Elastic Scale" metaKeywords="sharding scaling, Azure SQL Database sharding, elastic scale, splitting and merging elastic scale" services="sql-database" documentationCenter=""  manager="jhubbard" authors="sidneyh@microsoft.com"/>

<tags ms.service="sql-database" ms.workload="sql-database" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/02/2014" ms.author="sidneyh" />

#Elastic Scale 分割及合併服務教學課程

## 下載分割-合併套件 
1. 從 [NuGet](http://docs.nuget.org/docs/start-here/installing-nuget) 下載最新的 NuGet 版本。 
2. 開啟命令提示字元並瀏覽至您下載 nuget.exe 的目錄。 
3. 使用下列命令將最新的分割-合併套件下載到目前的目錄中： 
`nuget install Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge`  

上述步驟會將分割-合併檔案下載至目前的目錄。檔案會放在名為 **Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge.x.x.xxx.x** 的目錄中，其中 *x.x.xxx.x* 反映版本號碼。在 **content\splitmerge\service** 子目錄中尋找分割-合併服務檔案，而在 **content\splitmerge\powershell** 子目錄中尋找分割-合併 PowerShell 指令碼 (和必要的用戶端 .dll)。

##必要條件 

1. 建立將作為分割-合併狀態資料庫的 Azure SQL DB 資料庫。移至 [Azure 管理入口網站](https://manage.windowsazure.com)。按一下左下方的 [**新增**]，然後按一下 [**資料服務**] -> [**SQL Database**] -> [**自訂建立**]。填入資料庫名稱，並建立新的使用者名稱和密碼。請務必記錄名稱和密碼，以供日後使用。

2. 確定您的 Azure SQL DB 伺服器允許 Windows Azure 服務進行連接。請移至 [Azure 管理入口網站](https://manage.windowsazure.com)，在左窗格中按一下 [**SQL Database**]。然後按一下畫面頂端功能區上的 [**伺服器**]，並選取您的伺服器。然後按一下頂端的 [**設定**]，並確保 [**Windows Azure 服務**] 設定已設為 [**是**]。

    ![Allowed services][1]

3. 建立將用於診斷輸出的 Azure 儲存體帳戶。移至 [Azure 管理入口網站](https://manage.windowsazure.com)。按一下左下方的 [**新增**]，然後依序按一下 [**資料服務**]、[**儲存體**] 和 [**快速建立**]。 

4. 建立將包含您的分割-合併服務的 Azure 雲端服務。移至 [Azure 管理入口網站](https://manage.windowsazure.com)。按一下左下方的 [**新增**]，然後依序按一下 [**計算**]、[**雲端服務**] 和 [**快速建立**]。 


## 設定分割-合併服務 

### 分割-合併服務設定 

1. 在您下載分割/合併位元的資料夾中，建立一份隨附於 **SplitMergeService.cspkg** 的 **ServiceConfiguration.Template.cscfg** 檔案，並稱它為 **ServiceConfiguration.cscfg**。

2. 在您喜愛的文字編輯器中開啟 ServiceConfiguration.cscfg。我們建議使用 Visual Studio，因為它會驗證輸入，例如憑證指紋的格式。 

3. 建立新的資料庫，或選擇現有的資料庫做為分割/合併作業的狀態資料庫，並擷取該資料庫的連接字串。使用 Azure SQL DB，連接字串的格式通常為：

        "Server=myservername.database.windows.net; Database=mydatabasename;User ID=myuserID; Password=mypassword; Encrypt=True; Connection Timeout=30" .
4.    在 ElasticScaleMetadata 設定的 **SplitMergeWeb** 和 **SplitMergeWorker** 角色區段中，輸入 cscfg 檔案中的此連接字串。

5.    設定診斷記錄檔的目標時需要 Azure 中的儲存體帳戶。設定分割/合併時需要 Azure 儲存體帳戶的連接字串。連接字串的格式應該為：

        "DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccessKey" 
    
若要決定存取金鑰，請移至 [Azure 管理入口網站](https://manage.windowsazure.com)，按一下左邊的 [**儲存體**] 索引標籤，選取對應至儲存體帳戶的名稱，然後按一下底部的 [**管理存取金鑰**]。按一下 [**主要存取金鑰**] 的 [**複製**] 按鈕。

![manage access keys][2]

6.    輸入儲存體帳戶的名稱以及在儲存體連接字串的預留位置中提供的其中一個存取金鑰。此連接字串同時使用於 **Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString** 設定的 **SplitMergeWeb** 和 **SplitMergeWorker** 角色區段之下。您可以對不同的角色使用不同的儲存體帳戶。 

### 設定安全性 
如需設定服務安全性的詳細指示，請參閱[Elastic Scale 安全性設定](./sql-database-elastic-scale-configure-security.md)。

為了進行適合完成本教學課程的簡單測試部署，將會執行一組最基本的設定步驟來啟動並執行服務。這些步驟只能讓一個機器/帳戶執行它們來與服務通訊。

### 建立自我簽署憑證 

建立新目錄，並使用 [Visual Studio 的開發人員命令提示字元](http://msdn.microsoft.com/zh-tw/library/ms229859.aspx)視窗從此目錄執行下列命令：

    makecert ^
    -n "CN=*.cloudapp.net" ^
    -r -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.1,1.3.6.1.5.5.7.3.2" ^
    -a sha1 -len 2048 ^
    -sr currentuser -ss root ^
    -sv MyCert.pvk MyCert.cer

系統會要求您輸入密碼來保護私密金鑰。輸入強式密碼並加以確認。接著會提示您輸入之後要再次使用的密碼。按一下結尾的 [**是**]，將它匯入信任憑證授權單位的根存放區。

###    建立 PFX 檔案 

從執行 makecert 所在的相同視窗執行下列命令；使用您用來建立憑證的相同密碼：

    pvk2pfx -pvk MyCert.pvk -spc MyCert.cer -pfx MyCert.pfx -pi <password>

### 將用戶端憑證匯入個人存放區
1. 在 [Windows 檔案總管] 中按兩下 **MyCert.pfx**。
2. 在 [**憑證匯入精靈**] 中選取 [**目前使用者**] 然後按 [**下一步**]。
3. 確認檔案路徑，然後按 [**下一步**]。
4. 輸入密碼，讓 [**包含所有延伸內容**] 保持核取狀態，然後按 [**下一步**]。
5. 讓 [**自動選取憑證存放區[...]**] 保持核取狀態，然後按 [**下一步**]。
6. 按一下 [**完成**]，然後按一下 [**確定**]。

### 將 PFX 檔案上傳至雲端服務

移至 [Azure 管理入口網站](https://manage.windowsazure.com)。

1. 選取 [**雲端服務**]。
2. 選取您先前為分割/合併服務建立的雲端服務。
3. 按一下頂端功能表上的 [**憑證**]。
4. 按一下底列中的 [**上傳**]。
5. 選取 PFX 檔案，並輸入與上述相同的密碼。
6. 完成後，從清單中的新項目複製憑證指紋。

### 更新服務組態檔

將上面複製的憑證指紋複製到這些設定的指紋/值屬性中：

    <Setting name="AdditionalTrustedRootCertificationAuthorities" value="" />
    <Setting name="AllowedClientCertificateThumbprints" value="" />
    <Certificate name="SSL" thumbprint="" thumbprintAlgorithm="sha1" />
    <Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />

請注意，生產部署個別憑證應使用於 CA、伺服器憑證和用戶端憑證。如需詳細指示，請參閱[安全性設定](./sql-database-elastic-scale-configure-security.md)。

### 部署分割-合併服務
1. 移至 [Azure 管理入口網站](https://manage.windowsazure.com)。
2. 按一下左邊的 [**雲端服務**] 索引標籤，並選取您稍早建立的雲端服務。 
3. 按一下 [**儀表板**]。 
4. 選擇預備環境，然後按一下 [**上傳新的預備部署**]。

    ![Staging][3]

5. 在對話方塊中，輸入部署標籤。對於 [封裝] 和 [設定]，按一下 [從本機] 並選擇 **SplitMergeService.cspkg** 檔案以及您先前設定的 .cscfg 檔案。
6. 確定已核選 [**Deploy even if one or more roles contain a single instance**] 核取方塊。
7. 按右下方的勾號按鈕，開始進行部署。預期需要幾分鐘的時間才能完成。

![Upload][4]


## 部署疑難排解
如果您的 Web 角色無法上線，有可能是安全性設定有問題。請檢查 SSL 是否如上所述進行設定。

如果背景工作角色無法上線，但您的 Web 角色卻成功，則很可能是您稍早建立的狀態資料庫發生連線問題。 

* 確定 .cscfg 中的連接字串正確無誤。 
* 請檢查伺服器和資料庫均存在，而且使用者識別碼與密碼皆正確無誤。
* 若為 Azure SQL DB，連接字串的格式應為： 

        "Server=myservername.database.windows.net; Database=mydatabasename;User ID=myuserID; Password=mypassword; Encrypt=True; Connection Timeout=30" .

* 確定伺服器名稱不是以 **https://** 開頭。
* 確定您的 Azure SQL DB 伺服器允許 Windows Azure 服務進行連接。若要這樣做，請開啟 https://manage.windowsazure.com，按一下左邊的 [SQL Database]，按一下頂端的 [伺服器]，然後選取您的伺服器。按一下頂端的 [**設定**]，並確保 [**Windows Azure 服務**] 設定已設為 [是]。(請參閱本文開頭的「必要條件」一節)。

* 檢閱分割/合併服務執行個體的診斷記錄檔。開啟 Visual Studio 執行個體，然後在功能表列中按一下 [**檢視**] 和 [**伺服器總管**]。按一下 [**Windows Azure**] 圖示以連接到您的 Azure 訂閱。然後瀏覽至 [Windows Azure] -> [儲存體] -> <您的儲存體帳戶> -> [資料表] -> [WADLogsTable]。如需詳細資訊，請參閱[使用伺服器總管瀏覽儲存體資源](http://msdn.microsoft.com/zh-tw/library/azure/ff683677.aspx)。

    ![][5]

    ![][6]

## 測試您的分割-合併服務部署
### 使用網頁瀏覽器進行連接

決定分割-合併服務的 Web 端點。移至您雲端服務的 [**儀表板**] 並在右邊的 [**網站 URL**] 下尋找，即可找到此端點。由於預設安全性設定會停用 HTTP 端點，因此以 **https://** 取代 **http://**。將此 URL 的網頁載入瀏覽器。

### 使用 PowerShell 指令碼進行測試

執行內含的範例 PowerShell 指令碼即可測試部署和您的環境。

內含的指令碼檔案：

1. **SetupSampleSplitMergeEnvironment.ps1** - 設定分割/合併的測試資料層 (如需詳細說明，請參閱下面表格)
2. **ExecuteSampleSplitMerge.ps1** - 在測試資料層上執行測試作業 (如需詳細說明，請參閱下面表格)
3. **GetMappings.ps1** - 可印出分區對應目前狀態的最上層範例指令碼。
4. **ShardManagement.psm1** - 可包裝 ShardManagement API 的協助程式指令碼
5. **SqlDatabaseHelpers.psm1** - 用於建立和管理 SQL 資料庫的協助程式指令碼

<table style="width:100%">
  <tr>
    <th>PowerShell 檔案</th>
    <th>步驟</th>
  </tr>
  <tr>
    <th rowspan="5">SetupSampleSplitMergeEnvironment.ps1</th>
    <td>1.    建立分區對應管理員資料庫</td>
  </tr>
  <tr>
    <td>2.    建立 2 個分區資料庫。 
  </tr>
  <tr>
    <td>3.    建立這些資料庫的分區對應 (刪除這些資料庫上任何現有的分區對應)。 </td>
  </tr>
  <tr>
    <td>4.    在這兩個分區中建立小型範例資料表，並在其中一個分區中填入資料表。</td>
  </tr>
  <tr>
    <td>5.    宣告分區資料表的 SchemaInfo。</td>
  </tr>

</table>

<table style="width:100%">
  <tr>
    <th>PowerShell 檔案</th>
    <th>步驟</th>
  </tr>
<tr>
    <th rowspan="4">ExecuteSampleSplitMerge.ps1 </th>
    <td>1.    將分割要求傳送至分割-合併服務的 Web 前端，以將一半資料從第一個分區分割至第二個分區。</td>
  </tr>
  <tr>
    <td>2.    輪詢 Web 前端的分割要求狀態，並等到要求完成為止。</td>
  </tr>
  <tr>
    <td>3.    將合併要求傳送至分割-合併服務的 Web 前端，以將資料從第二個分區移回第一個分區。</td>
  </tr>
  <tr>
    <td>4.    輪詢 Web 前端的合併要求狀態，並等到要求完成為止。</td>
  </tr>
</table>

##使用 PowerShell 來驗證您的部署

1.    開啟新的 PowerShell 視窗，並瀏覽至您下載分割-合併套件的目錄，然後瀏覽到 "powershell" 目錄。
2.    建立 Azure SQL Database 伺服器 (或選擇現有的伺服器)，以便在其中建立分區對應管理員和分區。 

注意：SetupSampleSplitMergeEnvironment.ps1 指令碼預設會在相同的伺服器上建立所有資料庫，以簡化指令碼。這不會限制分割-合併服務本身。

    需要具有 DB 讀取/寫入存取權的 SQL 驗證登入，分割-合併服務才能移動資料和更新分區對應。因為分割-合併服務在雲端執行，所以目前不支援整合式驗證。

    確定 Azure SQL Server 已設為允許來自執行這些指令碼之電腦 IP 位址的存取。您可以在 Azure SQL Server / 設定 / 允許的 IP 位址之下找到這項設定。

3.    執行 SetupSampleSplitMergeEnvironment.ps1 指令碼以建立範例環境。 

    執行這個指令碼將會清除分區對應管理員資料庫上任何現有的分區對應管理資料結構和分區。如果您想要重新初始化分區對應或分區，重新執行指令碼很有用。

    範例命令列：

        .\SetupSampleSplitMergeEnvironment.ps1 `
            -UserName 'mysqluser' `
            -Password 'MySqlPassw0rd' `
            -ShardMapManagerServerName 'abcdefghij.database.windows.net'
    
4.    執行 Getmappings.ps1 指令碼以檢視範例環境中目前存在的對應。

        .\GetMappings.ps1 `
            -UserName 'mysqluser' `
            -Password 'MySqlPassw0rd' `
            -ShardMapManagerServerName 'abcdefghij.database.windows.net'

5.    執行 ExecuteSampleSplitMerge.ps1 指令碼以執行分割作業 (將第一個分區上的一半資料移至第二個分區)，然後執行合併作業 (將資料移回第一個分區)。如果您設定 SSL 並且讓 http 端點停用，請確定您改為使用 https:// 端點。

    範例命令列：

        .\ExecuteSampleSplitMerge.ps1 `
            -UserName 'mysqluser' `
            -Password 'MySqlPassw0rd' `
            -ShardMapManagerServerName 'abcdefghij.database.windows.net' `
            -SplitMergeServiceEndpoint 'https://mysplitmergeservice.cloudapp.net' `
            -CertificateThumbprint '0123456789abcdef0123456789abcdef01234567'

    如果收到下列錯誤，很有可能是 Web 端點的憑證有問題。嘗試以您喜愛的網頁瀏覽器連接到 Web 端點，並檢查是否有憑證錯誤。

        Invoke-WebRequest : The underlying connection was closed: Could not establish trust relationship for the SSL/TLSsecure channel.

    如果成功，輸出應該看起來如下：

        > .\ExecuteSampleSplitMerge.ps1 -UserName 'mysqluser' -Password 'MySqlPassw0rd' -ShardMapManagerServerName 'abcdefghij.database.windows.net' -SplitMergeServiceEndpoint 'http://mysplitmergeservice.cloudapp.net' -CertificateThumbprint 0123456789abcdef0123456789abcdef01234567
        Sending split request
        Began split operation with id dc68dfa0-e22b-4823-886a-9bdc903c80f3
        Polling split-merge request status. Press Ctrl-C to end
        Progress: 0% | Status: Queued | Details: [Informational] Queued request
        Progress: 5% | Status: Starting | Details: [Informational] Starting split-merge state machine for request.
        Progress: 5% | Status: Starting | Details: [Informational] Performing data consistency checks on target     shards.
        Progress: 20% | Status: CopyingReferenceTables | Details: [Informational] Moving reference tables from     source to target shard.
        Progress: 20% | Status: CopyingReferenceTables | Details: [Informational] Waiting for reference tables copy     completion.
        Progress: 20% | Status: CopyingReferenceTables | Details: [Informational] Moving reference tables from     source to target shard.
        Progress: 44% | Status: CopyingShardedTables | Details: [Informational] Moving key range [100:110) of     Sharded tables
        Progress: 44% | Status: CopyingShardedTables | Details: [Informational] Successfully copied key range     [100:110) for table [dbo].[MyShardedTable]
        ...
        ...
        Progress: 90% | Status: Completing | Details: [Informational] Successfully deleted shardlets in table     [dbo].[MyShardedTable].
        Progress: 90% | Status: Completing | Details: [Informational] Deleting any temp tables that were created     while processing the request.
        Progress: 100% | Status: Succeeded | Details: [Informational] Successfully processed request. 
        Sending merge request
        Began merge operation with id 6ffc308f-d006-466b-b24e-857242ec5f66
        Polling request status. Press Ctrl-C to end
        Progress: 0% | Status: Queued | Details: [Informational] Queued request
        Progress: 5% | Status: Starting | Details: [Informational] Starting split-merge state machine for request.
        Progress: 5% | Status: Starting | Details: [Informational] Performing data consistency checks on target     shards.
        Progress: 20% | Status: CopyingReferenceTables | Details: [Informational] Moving reference tables from     source to target shard.
        Progress: 44% | Status: CopyingShardedTables | Details: [Informational] Moving key range [100:110) of     Sharded tables
        Progress: 44% | Status: CopyingShardedTables | Details: [Informational] Successfully copied key range     [100:110) for table [dbo].[MyShardedTable]
        ...
        ...
        Progress: 90% | Status: Completing | Details: [Informational] Successfully deleted shardlets in table     [dbo].[MyShardedTable].
        Progress: 90% | Status: Completing | Details: [Informational] Deleting any temp tables that were created     while processing the request.
        Progress: 100% | Status: Succeeded | Details: [Informational] Successfully processed request.

6.    體驗其他資料類型！所有這些指令碼都採用可供指定金鑰類型的選擇性 -ShardKeyType 參數。預設值為 Int32，但您也可以指定 Int64、Guid 或二進位。 

## 建立自己的要求 

使用 Web UI 或藉由匯入並使用 SplitMerge.psm1 PowerShell 模組，即可使用此服務。

分割-合併服務可以在分區資料表和參考資料表中移動資料。分區資料表具有分區索引鍵資料行，且在每個分區上有不同的資料列資料。參考資料表並未分區，所以每個分區包含相同的資料列資料。參考資料表適合用於不常變更且用來聯結查詢中分區資料表的資料。

若要執行分割-合併作業時，您必須宣告您想要移動的分區資料表和參考資料表。使用 **SchemaInfo** API 可以完成這個動作。此 API 位於 **Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.Schema** 命名空間。

1.    對於每個分區資料表，建立 **ShardedTableInfo** 物件，以便描述資料表的父結構描述名稱 (選用，預設為 "dbo")、資料表名稱，以及該資料表中包含分區索引鍵的資料行名稱。
2.    對於每個參考資料表，建立 **ReferenceTableInfo** 物件，以便描述資料表的父結構描述名稱 (選用，預設為 "dbo") 和資料表名稱。
3.    將上述 TableInfo 物件新增至新的 **SchemaInfo** 物件。
4.    取得 **ShardMapManager** 物件的參考，然後呼叫 **GetSchemaInfoCollection**。
5.    將 **SchemaInfo** 新增至 **SchemaInfoCollection**，並提供分區對應名稱。

在 SetupSampleSplitMergeEnvironment.ps1 指令碼中可以看見這個範例。

請注意，分割-合併服務不會為您建立目標資料庫 (或資料庫中任何資料表的結構描述)。必須在將要求傳送至服務之前預先建立。


## 疑難排解
執行範例 powershell 指令碼時，您可能會看到以下訊息：

    Invoke-WebRequest : The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel.

此錯誤表示未正確設定您的 SSL 憑證。請遵循「使用網頁瀏覽器進行連接」一節中的指示。

[AZURE.INCLUDE [elastic-scale-include](../includes/elastic-scale-include.md)]
 
<!--Image references-->
[1]: ./media/sql-database-elastic-scale-split-and-merge-tutorial/allowed-services.png
[2]: ./media/sql-database-elastic-scale-split-and-merge-tutorial/manage.png
[3]: ./media/sql-database-elastic-scale-split-and-merge-tutorial/staging.png
[4]: ./media/sql-database-elastic-scale-split-and-merge-tutorial/upload.png
[5]: ./media/sql-database-elastic-scale-split-and-merge-tutorial/storage.png
[6]: ./media/sql-database-elastic-scale-split-and-merge-tutorial/logs.png
