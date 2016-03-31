<properties
	pageTitle="Azure Batch 中的簡易應用程式安裝和管理功能 |Microsoft Azure"
	description="使用 Azure Batch 的應用程式封裝功能輕鬆地管理多個應用程式和版本，以便安裝在 Batch 計算節點。"
	services="batch"
	documentationCenter=".net"
	authors="mmacy"
	manager="timlt"
	editor="" />

<tags
	ms.service="batch"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="big-compute"
	ms.date="03/14/2016"
	ms.author="marsma" />

# 使用 Azure Batch 應用程式封裝部署應用程式

Azure Batch 的應用程式封裝功能可為集區中的計算節點提供簡單的應用程式管理和部署能力。透過應用程式封裝，您可以上傳及管理多個版本的工作執行應用程式 (包括二進位檔和支援檔案)，接著將一或多個這種類型的應用程式自動部署到集區中的計算節點。

在本文中，您將了解如何使用 Azure 入口網站來上傳及管理應用程式封裝，然後使用 [Batch .NET][api_net] 程式庫將它們安裝在集區的計算節點上。

> [AZURE.NOTE] 此處所述的應用程式封裝功能取代了舊版服務中的「Batch Apps」功能。

## 應用程式封裝需求

本文所討論的應用程式封裝功能僅能與 2016 年 3 月 10 日後建立的 Batch 集區相容。應用程式封裝將無法部署到在此日期之前建立之集區中的計算節點。

[Batch REST API][api_rest] 2015-12-01.2.2 版和對應的 [Batch .NET][api_net] 程式庫 3.1.0 版引進這項功能。使用 Batch 時，我們建議您一律使用最新的 API 版本。

## 關於應用程式和應用程式封裝

在 Azure Batch 中，**應用程式**是指一組已建立版本的二進位檔，這些檔案可自動下載到集區中的計算節點。**應用程式封裝**指的是這些二進位檔中的一組特定組合，其代表應用程式的特定版本。

![應用程式和應用程式封裝的高階圖表][1]

### 應用程式

Batch 中的每個應用程式都包含一或多個應用程式封裝。它能指定應用程式的組態選項；例如，要安裝在計算節點上的預設應用程式封裝版本，以及應用程式的封裝是否可以更新或刪除。

### 應用程式封裝

應用程式封裝為 ZIP 檔案，其中包含應用程式二進位檔和工作執行所需的支援檔案。每個應用程式封裝都代表特定版本的應用程式。在 Batch 服務中建立集區時，您可以指定一或多個這種類型的應用程式和版本 (選擇性)，當節點加入集區時，這些應用程式封裝就會自動下載及解壓縮到每個節點上。

> [AZURE.IMPORTANT] Batch 帳戶中的應用程式和應用程式封裝數目，以及應用程式封裝的大小上限有其限制。如需這些限制的詳細資料，請參閱 [Azure Batch 服務的配額和限制](batch-quota-limit.md)。

### 應用程式封裝的優點

應用程式封裝可以簡化 Batch 解決方案中的程式碼，也能降低管理工作執行之應用程式所需的額外負荷。

透過應用程式封裝，集區的啟動工作不需要指定在節點上安裝一長串的個別資源檔案。您不需要在 Azure 儲存體中或在節點上手動管理這些檔案的多個版本。再者，您也不必費心產生 [SAS URL](../storage/storage-dotnet-shared-access-signature-part-1.md) 來提供這些檔案在 Azure 儲存體帳戶中的存取權限。

Batch 能在背景處理使用 Azure 儲存體將應用程式封裝儲存及部署到計算節點的詳細資料，因此可以簡化程式碼和管理額外負荷。

## 上傳及管理應用程式

您可以使用 Azure 入口網站來加入、更新和刪除應用程式封裝，以及設定每個應用程式的預設版本。這些作業目前僅支援在 Azure 入口網站中進行。

在接下來的幾個小節中，我們會先討論讓儲存體帳戶與 Batch 帳戶產生關聯，接著再檢閱 Azure 入口網站提供的封裝管理功能。之後，您將了解如何使用 [Batch .NET][api_net] 程式庫將這些封裝部署到計算節點。

### 連結儲存體帳戶

若要使用應用程式封裝，您必須先連結 Azure 儲存體帳戶與 Batch 帳戶。如果您還沒有為 Batch 帳戶設定儲存體帳戶，Azure 入口網站會在您第一次按一下 Batch 帳戶刀鋒視窗中的 [應用程式] 圖格時顯示警告。

![Azure 入口網站中的未設定儲存體帳戶警告][9]

Batch 服務會在應用程式封裝的儲存和擷取作業中使用相關聯的儲存體帳戶。一旦連結兩個帳戶後，Batch 便能將儲存在連結之儲存體帳戶中的封裝自動部署到計算節點。按一下 [警告] 刀鋒視窗中的 [儲存體帳戶設定]，然後再按一下 [儲存體帳戶] 刀鋒視窗上的 [儲存體帳戶]，以將現有儲存體帳戶連結至 Batch 帳戶。

![Azure 入口網站中的選擇儲存體帳戶刀鋒視窗][10]

您可以使用訂用帳戶中的現有儲存體帳戶，或為應用程式封裝儲存體建立新的儲存體帳戶。如需建立儲存體帳戶的詳細資料，請參閱[關於 Azure 儲存體帳戶](../storage/storage-create-storage-account.md)中的「建立儲存體帳戶」。建立儲存體帳戶之後，您可以使用 [儲存體帳戶] 刀鋒視窗將它連結到 Batch 帳戶。

> [AZURE.WARNING] 由於 Batch 會使用 Azure 儲存體儲存應用程式封裝，所以我們會針對區塊 Blob 資料向您收取[標準費用][storage_pricing]。請務必考量應用程式封裝的大小和數目，並定期移除過時的封裝以降低成本。

### 檢視目前的應用程式

若要檢視 Batch 帳戶中的應用程式，請按一下 Batch 帳戶刀鋒視窗中的 [應用程式] 圖格。

![應用程式圖格][2]

這會開啟 [應用程式] 刀鋒視窗︰

![列出應用程式][3]

[應用程式] 刀鋒視窗會顯示帳戶中每個應用程式的識別碼，以及下列屬性︰

* **封裝** - 與此應用程式相關聯的版本號碼。
* **預設版本** – 如果您在設定集區的應用程式時未指定版本，系統會安裝此版本。這項設定是選擇性的。
* **允許更新** – 如果將此屬性設定為 [否]，應用程式的封裝更新和刪除會遭到停用，您只能加入應用程式封裝版本。預設值為 [是]。

### 檢視應用程式詳細資料

在 [應用程式] 刀鋒視窗中按一下應用程式時，該應用程式的詳細資料刀鋒視窗隨即會出現。

![應用程式詳細資料][4]

在應用程式詳細資料刀鋒視窗中，您可以配置應用程式的以下設定。

* **允許更新** - 指定是否可更新或刪除應用程式的應用程式封裝 (請參閱下文中的「更新或刪除應用程式封裝」)。
* **預設版本** - 指定要部署至計算節點的預設應用程式封裝。
* **顯示名稱** - 這是在顯示應用程式相關資訊時 (例如，透過 Batch 提供給客戶之服務的 UI 中)，Batch 解決方案可以使用的「易記」名稱。

### 加入新的應用程式

若要建立新應用程式，請使用新的唯一應用程式識別碼加入應用程式封裝。使用新應用程式識別碼加入的第一個應用程式封裝也會建立新的應用程式。

按一下 [應用程式] 刀鋒視窗中的 [加入] 以開啟 [新增應用程式] 刀鋒視窗。

![Azure 入口網站中的新增應用程式刀鋒視窗][5]

[新增應用程式] 刀鋒視窗提供以下欄位，供您指定新應用程式和應用程式封裝的設定。

**中繼資料**

您可以直接將值輸入 [應用程式識別碼] 和 [版本] 文字方塊中，以利用手動方式提供應用程式中繼資料，也可以上傳包含這些中繼資料的 JSON 檔案。若要手動指定應用程式識別碼和版本，只要將 [中繼資料] 下拉式清單選取器保持為 [輸入中繼資料] \(預設值)，然後手動將值輸入 [應用程式識別碼] 和 [版本] 文字方塊即可。

若要指定包含識別碼和封裝版本的 JSON 格式中繼資料檔案，請從 [中繼資料] 下拉式清單選取 [上傳中繼資料檔案]︰

![上傳中繼資料檔案下拉式清單選取器][6]

接下來，在出現的 [中繼資料檔案] 文字方塊旁按一下資料夾圖示，瀏覽至包含 JSON 資料的本機檔案。在此範例中，使用者已選取要上傳 `litware_1.1001.2b.json` 檔案，而 [應用程式識別碼] 和 [版本] 文字方塊已自動填入檔案中的資訊︰

![中繼資料檔案選取項目詳細資料][13]

在檔案中使用以下 JSON 格式來指定應用程式封裝中繼資料︰

```
{
    "id": "litware",
    "version": "1.1001.2b"
}
```

> [AZURE.NOTE] 如果您上傳識別碼和版本的 JSON 中繼資料檔案，便「不」需要編輯 [應用程式識別碼] 或 [版本] 文字方塊，因為系統會自動填入 JSON 檔案中的資料。

**應用程式識別碼**

它能指定新應用程式的識別碼，其須符合標準 Azure Batch 識別碼驗證規則的規範︰

* 可包含英數字元的任何組合，包括連字號和底線。
* 不能包含超過 64 個字元。
* 在 Batch 帳戶內必須是唯一的。
* 保留大小寫且不區分大小寫。

**版本**

指定要上傳的應用程式封裝版本。版本字串須符合以下驗證規則的規範︰

* 可包含英數字元的任何組合，包括連字號、底線和句點。
* 不能包含超過 64 個字元。
* 在應用程式內必須是唯一的。
* 保留大小寫且不區分大小寫。

**應用程式封裝**

指定包含應用程式二進位檔和執行應用程式所需之任何支援檔案的 ZIP 檔案。按一下 [選取檔案] 文字方塊或資料夾圖示，以瀏覽及選取包含應用程式檔案的 ZIP 檔案。

一旦選取檔案後，請按一下 [確定] 以開始上傳到 Azure 儲存體。當上傳作業完成時，系統會通知您且刀鋒視窗會關閉。請注意，因上傳之檔案的大小和網路連線速度不盡相同，這項作業可能需要花費一些時間。

> [AZURE.WARNING] 上傳作業完成之前，請勿關閉 [新增應用程式] 刀鋒視窗，否則上傳程序將會中止。

### 加入新應用程式封裝

若要加入現有應用程式的新應用程式封裝版本，請在 [應用程式] 刀鋒視窗中選取應用程式，再依序按一下 [封裝] 和 [加入] 以顯示 [加入封裝] 刀鋒視窗。

![Azure 入口網站中的加入應用程式封裝刀鋒視窗][8]

如您所見，除了 [應用程式識別碼] 文字方塊已停用之外，欄位與 [新增應用程式] 刀鋒視窗中的欄位相符。依照上述方式，指定新封裝的 [版本]，瀏覽至 [應用程式封裝] ZIP 檔案，然後按一下 [確定] 以上傳封裝。

### 更新或刪除應用程式封裝

若要更新或刪除現有的應用程式封裝，請開啟應用程式的詳細資料刀鋒視窗，按一下 [封裝] 以顯示 [封裝] 刀鋒視窗，按一下要修改之應用程式封裝資料列中的 [省略符號]，然後選取要執行的動作。

![在 Azure 入口網站中更新或刪除封裝][7]

**更新**

當您按一下 [更新] 時，[更新封裝] 刀鋒視窗隨即會出現。此刀鋒視窗與 [新增應用程式封裝] 刀鋒視窗相似，只不過封裝選取欄位已啟用，因此您可以指定要上傳的新 ZIP 檔案。

![Azure 入口網站中的更新封裝刀鋒視窗][11]

**刪除**

當您按一下 [刪除] 時，系統會要求您確認是否要刪除封裝版本，隨後 Batch 會從 Azure 儲存體中刪除該封裝。如果您刪除應用程式的預設版本，系統會移除應用程式的預設版本設定。

![刪除應用程式][12]

## 將應用程式安裝在計算節點上

既然我們已探討如何使用 Azure 入口網站來上傳及管理應用程式封裝，接下來我們可以討論如何將它們實際部署到計算節點，以及如何利用 Batch 工作予以執行。

若要將應用程式封裝安裝在集區中的計算節點上，您需要為集區指定一或多個應用程式封裝參考。在 Batch .NET 中，您可以在建立集區時指定一或多個 [CloudPool][net_cloudpool].[ApplicationPackageReferences][net_cloudpool_pkgref]，或將它們加入現有集區。

[ApplicationPackageReference][net_pkgref] 類別能指定要安裝在集區之計算節點上的應用程式識別碼和版本。

```csharp
// Create the unbound CloudPool
CloudPool myCloudPool =
    batchClient.PoolOperations.CreatePool(poolId: "myPool",
                                          osFamily: "4",
                                          virtualMachineSize: "small",
                                          targetDedicated: "1");

// Specify the application and version to install on the compute nodes
myCloudPool.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference {
        ApplicationId = "litware",
        Version = "1.1001.2b" }
};

// Commit the pool so that it's created in the Batch service. As the nodes join
// the pool, the specified application package will be installed on each.
await myCloudPool.CommitAsync();
```

## 執行安裝的應用程式

當每個計算節點加入集區 (抑或是重新啟動或重新安裝映像)，您指定的封裝會下載及解壓縮到節點上 `AZ_BATCH_ROOT_DIR` 內指定的目錄中。Batch 也會為您的工作命令列建立環境變數，以在呼叫應用程式二進位檔時使用；此變數會遵守下列命名配置︰

`AZ_BATCH_APP_PACKAGE_appid#version`

例如，如果您指定要安裝應用程式 blender 的 2.7 版，您的工作可以透過在其命令列中參考下列環境變數來存取應用程式二進位檔︰

`AZ_BATCH_APP_PACKAGE_BLENDER#2.7`

如果應用程式指定預設版本，您可以參考不含版本字串尾碼的環境變數。例如，如果您已在 Azure 入口網站中指定應用程式 blender 的預設版本 2.7，您的工作可以參考下列環境變數︰

`AZ_BATCH_APP_PACKAGE_BLENDER`

下列程式碼片段示範當您已指定應用程式 blender 的預設版本時，該如何設定工作。

```csharp
string taskId = "blendertask01";
string commandLine = @"cmd /c %AZ_BATCH_APP_PACKAGE_BLENDER%\blender.exe -my -command -args";
CloudTask blenderTask = new CloudTask(taskId, commandLine);
```

> [AZURE.TIP] 如需計算節點環境設定的詳細資訊，請參閱 [Batch功能概觀](batch-api-basics.md)的「工作的環境設定」。

## 更新集區的應用程式封裝

如果您已設定現有集區的應用程式封裝，可以指定集區的新封裝。加入集區的所有新節點都會安裝新指定的封裝，任何重新啟動或重新安裝映像的現有節點也是如此。在更新封裝參考時已存在集區中的計算節點不會自動安裝新應用程式封裝。

在此範例中，現有集區已將應用程式 blender 的 2.7 版設定為其中一個 [CloudPool][net_cloudpool].[ApplicationPackageReferences][net_cloudpool_pkgref]。若要將集區的節點更新為 2.76b 版，請將 [ApplicationPackageReference][net_pkgref] 指定為新版本並認可變更。

```csharp
string newVersion = "2.76b";
CloudPool boundPool = await batchClient.PoolOperations.GetPoolAsync("myPool");
boundPool.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference {
        ApplicationId = "blender",
        Version = newVersion }
};
await boundPool.CommitAsync();
```

既然您已設定新版本，任何加入集區的「新」節點都會部署 2.76b 版。若要將 2.76b 安裝在「已存在」集區中的節點上，請重新啟動節點 (或重新安裝映像)。請注意，重新啟動的節點會保留前次封裝部署的檔案。

## 列出 Batch 帳戶中的應用程式

您可以使用 [ApplicationOperations][net_appops].[ListApplicationSummaries][net_appops_listappsummaries] 方法列出 Batch 帳戶中的應用程式和應用程式封裝。

```csharp
// List the applications and their application packages in the Batch account.
List<ApplicationSummary> applications = await batchClient.ApplicationOperations.ListApplicationSummaries().ToListAsync();
foreach (ApplicationSummary app in applications)
{
    Console.WriteLine("ID: {0} | Display Name: {1}", app.Id, app.DisplayName);

    foreach (string version in app.Versions)
    {
        Console.WriteLine("  {0}", version);
    }
}
```

## 總結

透過應用程式封裝，當客戶使用您提供的啟用 Batch 服務來處理作業時，您可以利用更輕鬆的方式讓他們選取作業的應用程式，以及指定要使用的確切版本。您也可以在服務中提供讓客戶上傳及追蹤其應用程式的功能。

## 後續步驟

* [Batch REST API][api_rest] 也提供應用程式封裝的使用支援。例如，請參閱[將集區加入至帳戶][rest_add_pool]中的 [applicationPackageReferences][rest_add_pool_with_packages] 項目，以了解如何使用 REST API 來指定要安裝的封裝。如需使用 Batch REST API 來取得應用程式資訊的詳細資料，請參閱[應用程式][rest_applications]。

* 了解如何以程式設計方式[使用 Batch Management .NET 管理 Azure Batch 帳戶和配額](batch-management-dotnet.md)。[Batch Management .NET][api_net_mgmt] 程式庫可以啟用 Batch 應用程式或服務的帳戶建立和刪除功能。

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_mgmt]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[storage_pricing]: https://azure.microsoft.com/pricing/details/storage/
[net_appops]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationoperations.aspx
[net_appops_listappsummaries]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationoperations.listapplicationsummaries.aspx
[net_cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_cloudpool_pkgref]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.applicationpackagereferences.aspx
[net_pkgref]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationpackagereference.aspx
[rest_applications]: https://msdn.microsoft.com/library/azure/mt643945.aspx
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[rest_add_pool_with_packages]: https://msdn.microsoft.com/library/azure/dn820174.aspx#bk_apkgreference

[1]: ./media/batch-application-packages/app_pkg_01.png "應用程式封裝高階圖表"
[2]: ./media/batch-application-packages/app_pkg_02.png "Azure 入口網站中的應用程式圖格"
[3]: ./media/batch-application-packages/app_pkg_03.png "Azure 入口網站中的應用程式刀鋒視窗"
[4]: ./media/batch-application-packages/app_pkg_04.png "Azure 入口網站中的應用程式詳細資料刀鋒視窗"
[5]: ./media/batch-application-packages/app_pkg_05.png "Azure 入口網站中的新增應用程式刀鋒視窗"
[6]: ./media/batch-application-packages/app_pkg_06.png "上傳中繼資料檔案下拉式清單選取器"
[7]: ./media/batch-application-packages/app_pkg_07.png "Azure 入口網站中的更新或刪除封裝下拉式清單"
[8]: ./media/batch-application-packages/app_pkg_08.png "Azure 入口網站中的新增應用程式封裝刀鋒視窗"
[9]: ./media/batch-application-packages/app_pkg_09.png "沒有連結的儲存體帳戶警示"
[10]: ./media/batch-application-packages/app_pkg_10.png "Azure 入口網站中的選擇儲存體帳戶刀鋒視窗"
[11]: ./media/batch-application-packages/app_pkg_11.png "Azure 入口網站中的更新封裝刀鋒視窗"
[12]: ./media/batch-application-packages/app_pkg_12.png "Azure 入口網站中的刪除封裝確認對話方塊"
[13]: ./media/batch-application-packages/app_pkg_13.png "中繼資料檔案選取項目詳細資料"

<!---HONumber=AcomDC_0316_2016-->
