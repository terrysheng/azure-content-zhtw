<properties
	pageTitle="使用 REST 備份及還原 Azure Web Apps"
	description="了解如何使用 RESTful API 呼叫來備份和還原 Azure App Service 中的 Web 應用程式"
	services="app-service"
	documentationCenter=""
	authors="nking92"
	manager="edlauare"
    editor="" />

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/18/2015"
	ms.author="nicking"/>

# 使用 REST 備份及還原 Azure Web Apps
[Azure Web Apps](https://azure.microsoft.com/services/app-service/web/) 可以備份為 Azure 儲存體中的 blob。此備份中也可以包含應用程式的資料庫。如果不小心刪除應用程式，或需要將應用程式還原成先前的版本，則可以從任何先前的備份加以還原。您可以視需要隨時進行備份，或排定在間隔適當時間後進行備份。

本文將說明如何透過 RESTful API 要求來備份和還原 Azure Web 應用程式。如果您想要透過 Azure 入口網站以圖形方式建立和管理 Web 應用程式的備份，請參閱[在 Azure App Service 中備份 Web 應用程式](web-sites-backup.md)

<a name="gettingstarted"></a>
## 開始使用
若要傳送 REST 要求，您必須知道 Web 應用程式的**名稱**、**資源群組**和**訂用帳戶識別碼**。在 [Azure Preview 入口網站](https://portal.azure.com)的 [Web Apps] 刀鋒視窗中按一下 Web 應用程式，即可找到這些資訊。在本文的範例中，我們將會設定 `backuprestoreapiexamples.azurewebsites.net` 網站。它儲存在 Default-Web-WestUS 資源群組中，並在識別碼為 00001111-2222-3333-4444-555566667777 的訂用帳戶上執行。

![範例網站資訊][SampleWebsiteInformation]

<a name="backup-restore-rest-api"></a>
## 備份和還原 REST API
現在我們要討論幾個範例，說明如何使用 REST API 來備份和還原 Azure Web 應用程式。每個範例都包含 URL 和 HTTP 要求本文。範例 URL 中包含以大括號括住的預留位置，例如 {subscriptionId}。請以對應的 Web 應用程式資訊取代這些預留位置。以下有範例 URL 中會出現之預留位置的說明可供您參考。

* subscriptionId – 包含 Web 應用程式之 Azure 訂用帳戶的識別碼
* resourceGroupName – 包含 Web 應用程式之資源群組的名稱
* sitename – Azure Web 應用程式的名稱
* backupId – Web 應用程式備份的識別碼

如需 API 的完整說明文件，包括可在 HTTP 要求內加入的幾個選用參數，請參閱 [Azure 資源總管](https://resources.azure.com/)。

<a name="backup-on-demand"></a>
## 視需要備份 Web 應用程式
若要立即備份 Web 應用程式，請傳送 **POST** 要求到 `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/sites/{sitename}/backup/`。

使用範例網站後的 URL 看起來就像這樣：`https://management.azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples/backup/`

您必須在要求本文中提供 JSON 物件，以指定要用來儲存備份的儲存體帳戶。JSON 物件必須有名為 **storageAccountUrl** 的屬性以保存 [SAS URL](../storage/storage-dotnet-shared-access-signature-part-1.md)，此 URL 會授與保有備份 blob 之 Azure 儲存體容器的寫入權限。如果您想要備份資料庫，則還必須提供包含所要備份之資料庫的名稱、類型和連接字串的清單。

```
{
    "properties":
    {
        "storageAccountUrl": “https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl”,
        “databases”: [
            {
                “databaseType”: “SqlAzure”,
                “name”: “MyDatabase1”,
                "connectionString": "<your database connection string>"
            }
        ]
    }
}
```

系統一收到要求就會立即開始備份 Web 應用程式。備份程序可能需要很長的時間才能完成。HTTP 回應中會有識別碼可供您用於其他要求以查看備份狀態。以下是備份要求之 HTTP 回應本文的範例。

```
{
    "id": "/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples",
    "name": " backuprestoreapiexamples ",
    "type": "Microsoft.Web/sites",
    "location": "WestUS",
    "properties":    {
        "id": 1,
        "storageAccountUrl": “https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl”,
        "blobName": “backup_201509291825.zip”,
        "name": "backup_201509291825",
        "status": 4,
        "sizeInBytes": 0,
        "created": "2015-09-29T18:25:26.3992707Z",
        "log": null,
        "databases": [
            {
                "databaseType": "SqlAzure",
                "name": "MyDatabase1",
                "connectionString": "<your database connection string>"
            }
        ],
        "scheduled": false,
        "correlationId": "ea730f4d-dd06-4f7f-a090-9aa09k662h36",
    }
}
```

>[AZURE.NOTE]在 HTTP 回應的記錄檔屬性中可以找到錯誤訊息。

<a name="schedule-automatic-backups"></a>
## 排程自動備份
除了視需要備份 Web 應用程式，您也可以排程自動備份。

### 設定新的自動備份排程
若要設定備份排程，請傳送 **PUT** 要求給 `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/sites/{name}/config/backup`。

範例網站的 URL 看起來就像這樣：`https://management.azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples/config/backup`

要求本文必須有指定備份組態的 JSON 物件。以下是具有所有必要參數的範例。

```
{
    "location": "WestUS",
    "properties": // Represents a web app restore request
    {
        "backupSchedule": { // Required for automatically scheduled backups
            "frequencyInterval": "7",
            "frequencyUnit": "Day",
            "keepAtLeastOneBackup": "True",
            "retentionPeriodInDays": "10",
        },
        "enabled": "True", // Must be set to true to enable automatic backups
        "name": “mysitebackup”,
        "storageAccountUrl": "https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl"
    }
}
```

這個範例會將應用程式設定為每 7 天自動備份一次。**frequencyInterval** 和 **frequencyUnit** 參數會一起決定備份的執行頻率。**frequencyUnit** 的有效值為**小時**和**天**。例如，若要每 12 小時備份一次應用程式，請將 frequencyInterval 設為 12，並將 frequencyUnit 設為小時。

系統會自動移除儲存體帳戶中的舊有備份。您可以藉由設定 **retentionPeriodInDays** 參數來控制舊有備份的保留期。如果您想要永遠儲存至少一個備份，不論它已存在多久，請將 **keepAtLeastOneBackup** 設為 true。

### 取得自動備份排程
若要取得 Web 應用程式的備份組態，請傳送 **POST** 要求到以下 URL：` https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/sites/{name}/config/backup/list`。

範例網站的 URL 是 `https://management.azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples/config/backup/list`。

<a name="get-backup-status"></a>
## 取得備份狀態
視 Web 應用程式的大小而定，備份程序可能需要一些時間才能完成。備份程序也可能失敗、逾時或是部分成功。若要查看所有 Web 應用程式的備份狀態，請傳送 **GET** 要求到以下 URL：`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/sites/{name}/backups`。

若要查看特定備份的狀態，請傳送 GET 要求到以下 URL：`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/sites/{name}/backups/{backupId}`。

範例網站的 URL 看起來就像這樣：`https://management.azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples/backups/1`

回應本文會包含類似此範例的 JSON 物件。

```
{
    "properties":  {
        "id": 1,
        "storageAccountUrl": "https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl",
        "blobName": "backup_201509291734.zip",
        "name": "backup_201509291734",
        "status": 2,
        "sizeInBytes": 151973,
        "created": "2015-09-29T17:34:57.2091605",
        "scheduled": false,
        "lastRestoreTimeStamp": null,
        "finishedTimeStamp": "2015-09-29T17:35:11.3293602",
        "correlationId": "2379fc13-418a-4b9c-920d-d06e73c5028d",
        "websiteSizeInBytes": 209920
    }
}
```

備份狀態是列舉的類型。以下是各個可能的狀態。

* 0 – 進行中：備份已啟動但尚未完成。
* 1 – 失敗：備份未能成功。
* 2 – 成功：已成功完成備份。
* 3 – 逾時：備份未能及時完成，因此已取消。
* 4 - 已建立：備份要求已排入佇列，但尚未啟動。
* 5 - 略過：未進行備份，因為排程觸發了太多備份。
* 6 – 部分成功：備份成功，但某些檔案因為無法讀取而未能予以備份。會發生此狀況通常是因為檔案已遭到獨佔鎖定。
* 7 – 正在進行刪除：已要求刪除備份，但系統尚未刪除。
* 8 - 刪除失敗：無法刪除備份。會發生此狀況通常是因為用來建立備份的 SAS URL 已過期。
* 9 - 已刪除：已成功刪除備份。

<a name="restore-web-app"></a>
## 從備份還原 Web 應用程式
如果 Web 應用程式已遭到刪除，或如果您想要將 Web 應用程式還原為舊版，您可以從備份還原應用程式。若要叫用還原，請傳送 **POST** 要求到以下 URL：`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/sites/{name}/backups/{id}/restore`。

範例網站的 URL 看起來就像這樣：`https://management.azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples/backups/1/restore`

在要求本文中，傳送包含還原作業屬性的 JSON 物件。以下是包含所有必要屬性的範例：

```
{
    "location": "WestUS",
    "properties":
    {
        "blobName": "backup_201509280431.zip",
        "databases": [ // Not required unless you’re restoring databases
            {
                “databaseType”: “SqlAzure”,
                “name”: “MyDatabase1”
        }],
        "overwrite": "true",
        "storageAccountUrl": "https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl" // SAS URL to storage container containing your website backup
    }
}
```

### 還原成新的 Web 應用程式
在還原備份時，有時您可能會想要建立新的 Web 應用程式，而非覆寫現有的 Web 應用程式。若要這麼做，請將要求 URL 變更為指向所要建立的新 Web 應用程式，並將 JSON 中的 **overwrite** 屬性變更為 **false**。

<a name="delete-app-backup"></a>
## 刪除 Web 應用程式備份
如果您想要刪除備份，請傳送 **DELETE** 要求給以下 URL：`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/sites/{name}/backups/{backupId}`。

範例網站的 URL 看起來就像這樣：`https://management.azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples/backups/1`

<a name="manage-sas-url"></a>
## 管理備份的 SAS URL
Azure Web Apps 會嘗試使用建立備份時所提供的 SAS URL 來刪除 Azure 儲存體中的備份。如果此 SAS URL 不再有效，就無法透過 REST API 刪除備份。不過，您可以傳送 **POST** 要求給以下 URL 來更新與備份相關聯的 SAS URL：`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/sites/{name}/backups/{id}/list`。

範例網站的 URL 看起來就像這樣：`https://management.azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples/backups/1/list`

在要求本文中，傳送包含新的 SAS URL 的 JSON 物件。範例如下。

```
{
    "properties":
    {
        "storageAccountUrl": "https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl"
    }
}
```

>[AZURE.NOTE]為了確保安全，在傳送特定備份的 GET 要求時，並不會傳回與該備份相關聯的 SAS URL。如果您想要檢視與該備份相關聯的 SAS URL，請傳送 POST 要求給上述的同一個 URL，並只在要求本文中包含空的 JSON 物件。伺服器所傳回的回應中便會包含該備份的所有資訊，包括其 SAS URL。

<!-- IMAGES -->
[SampleWebsiteInformation]: ./media/websites-csm-backup/01siteconfig.png

<!---HONumber=AcomDC_1210_2015-->