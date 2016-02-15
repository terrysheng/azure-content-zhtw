<properties
 pageTitle="IoT 中樞裝置身分識別的匯入匯出 | Microsoft Azure"
 description="大量管理 IoT 中樞裝置身分識別的概念與 .NET 程式碼片段"
 services="iot-hub"
 documentationCenter=".net"
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="02/03/2016"
 ms.author="dobett"/>

# 大量管理 IoT 中樞的裝置身分識別

每個 IoT 中樞都有一個裝置身分識別登錄，您可用來在服務中建立各裝置的資源 (例如含有傳送中雲端到裝置訊息的佇列)，以及允許存取裝置面向端點。本文說明如何在裝置身分識別登錄中大量匯入和匯出裝置身分識別。

匯入和匯出操作會在*作業*的內容中進行，其可讓使用者對 IoT 中樞執行大量服務操作。

**RegistryManager** 類別包含使用**作業**架構的 **ExportDevicesAsync** 和 **ImportDevicesAsync** 方法。這些方法可讓您匯出、匯入和同步處理整個 IoT 中樞裝置登錄。

## 什麼是作業？

裝置身分識別登錄操作會使用**作業**系統的前提是操作符合下列條件時：

*  相較於標準執行階段操作，其執行時間可能很長，或是，
*  會傳回大量資料給使用者。

在這些情況下，與其讓單一 API 呼叫等候或封鎖操作的結果，操作會以非同步方式建立該 IoT 中樞的**作業**，然後操作會立即傳回 **JobProperties** 物件。

下列 C# 程式碼片段示範如何建立匯出作業：

```
// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = await registryManager.ExportDevicesAsync(containerSasUri, false);
```

然後您可以使用 **RegistryManager** 類別來查詢使用所傳回 **JobProperties** 中繼資料之**作業**的狀態。

下列 C# 程式碼片段示範如何每五秒輪詢一次以查看作業是否已執行完成：

```
// Wait until job is finished
while(true)
{
  exportJob = await registryManager.GetJobAsync(exportJob.JobId);
  if (exportJob.Status == JobStatus.Completed || 
      exportJob.Status == JobStatus.Failed ||
      exportJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}
```

## 匯出裝置

使用 **ExportDevicesAsync** 方法將整個 IoT 中樞裝置登錄匯出到使用[共用存取簽章](https://msdn.microsoft.com/library/ee395415.aspx)的 [Azure 儲存體](https://azure.microsoft.com/documentation/services/storage/) Blob 容器。

這個方法可讓您在所控制的 Blob 容器中建立可靠的裝置資訊備份。

**ExportDevicesAsync** 方法需要兩個參數：

*  包含 Blob 容器 URI 的*字串*。此 URI 必須包含可授與容器寫入權限的 SAS 權杖。作業會在這個容器中建立用來儲存序列化匯出裝置資料的區塊 Blob。SAS 權杖必須包含這些權限：
    
    ```
    SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Delete
    ```

*  指出是否要在匯出資料中排除驗證金鑰的*布林值*。若為 **false**，驗證金鑰會包含在匯出輸出中；否則會將金鑰匯出為 **null**。

下列 C# 程式碼片段示範如何啟動匯出作業，然後執行輪詢以完成作業：

```
// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = await registryManager.ExportDevicesAsync(containerSasUri, false);

// Wait until job is finished
while(true)
{
    exportJob = await registryManager.GetJobAsync(exportJob.JobId);
    if (exportJob.Status == JobStatus.Completed || 
        exportJob.Status == JobStatus.Failed ||
        exportJob.Status == JobStatus.Cancelled)
    {
    // Job has finished executing
    break;
    }

    await Task.Delay(TimeSpan.FromSeconds(5));
}
```

作業會在所提供的 Blob 容器中將其輸出儲存為名稱是 **devices.txt** 的區塊 Blob。輸出資料包含 JSON 序列化裝置資料，每行代表一個裝置。

以下是輸出資料的範例：

```
{"id":"Device1","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device2","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device3","eTag":"MA==","status":"disabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device4","eTag":"MA==","status":"disabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device5","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
```

如果您需要存取程式碼中的這項資料，可以使用 **ExportImportDevice** 類別輕鬆地將此資料還原序列化。下列 C# 程式碼片段示範如何讀取先前匯出至區塊 Blob 的裝置資訊：

```
var exportedDevices = new List<ExportImportDevice>();

using (var streamReader = new StreamReader(await blob.OpenReadAsync(AccessCondition.GenerateIfExistsCondition(), RequestOptions, null), Encoding.UTF8))
{
  while (streamReader.Peek() != -1)
  {
    string line = await streamReader.ReadLineAsync();
    var device = JsonConvert.DeserializeObject<ExportImportDevice>(line);
    exportedDevices.Add(device);
  }
}
```

> [AZURE.NOTE]  您也可以使用 **RegistryManager** 類別的 **GetDevicesAsync** 方法，來擷取裝置清單。不過，這個方法有所傳回的裝置物件數目最多只能有 1000 個的上限。**GetDevicesAsync** 方法的預期使用案例適用於開發案例，其目的是要協助偵錯，因此不建議用於生產工作負載。

## 匯入裝置

**RegistryManager** 類別中的 **ImportDevicesAsync** 方法可讓您在 IoT 中樞裝置登錄中執行大量匯入和同步處理操作。和 **ExportDevicesAsync** 方法相同，**ImportDevicesAsync** 方法會使用作業架構。

請謹慎使用 **ImportDevicesAsync** 方法，因為除了會在裝置身分識別登錄中佈建新裝置外，此方法也會更新和刪除現有裝置。

> [AZURE.WARNING]  匯入操作是無法復原的。請一律先使用 **ExportDevicesAsync** 方法將現有資料備份到另一個 Blob 容器，再對裝置身分識別登錄進行大量變更。

**ImportDevicesAsync** 方法需要兩個參數：

*  包含 [Azure 儲存體](https://azure.microsoft.com/documentation/services/storage/) Blob 容器 URI 以做為作業之*輸入*的*字串*。此 URI 必須包含可授與容器讀取權限的 SAS 權杖。此容器必須包含名稱為 **devices.txt** 的 Blob，而此 Blob 中包含要匯入到裝置身分識別登錄的序列化裝置資料。匯入資料必須包含 **ExportImportDevice** 作業所建立之相同 JSON 格式的裝置資訊。SAS 權杖必須包含這些權限：

    ```
    SharedAccessBlobPermissions.Read
    ```

*  包含 [Azure 儲存體](https://azure.microsoft.com/documentation/services/storage/) Blob 容器 URI 以做為作業之*輸出*的*字串*。作業會在此容器中建立區塊 Blob，以儲存來自已完成之匯入**作業**的任何錯誤資訊。SAS 權杖必須包含這些權限：
    
    ```
    SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Delete
    ```

> [AZURE.NOTE]  這兩個參數可以指向相同的 Blob 容器。參數不同只會讓您更能掌控資料，因為輸出容器需要其他權限。

下列 C# 程式碼片段示範如何啟動匯入作業：

```
JobProperties importJob = await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);
```

## 匯入行為

您可以使用 **ImportDevicesAsync** 方法，在您的裝置身分識別登錄中執行下列大量操作：

-   大量註冊新裝置
-   大量刪除現有裝置
-   大量變更狀態 (啟用或停用裝置)
-   大量指派新的裝置驗證金鑰
-   大量自動重新產生裝置驗證金鑰

您可以在單一 **ImportDevicesAsync** 呼叫中執行上述操作的任意組合。比方說，您可以同時間註冊新裝置並刪除或更新現有裝置。搭配 **ExportDevicesAsync** 方法一起使用時，您可以將某個 IoT 中樞內的所有裝置移轉到另一個 IoT 中樞。

您可以在每個裝置的匯入序列化資料中使用選擇性的 **importMode** 屬性控制每個裝置的匯入程序。**ImportMode** 屬性具有下列選項：

| importMode | 說明 |
| -------- | ----------- |
| **createOrUpdate** | 如果不存在具有指定**識別碼**的裝置，則表示是新註冊的裝置。<br/>如果裝置已存在，則會以所提供的輸入資料覆寫現有資訊，而不管 **ETag** 值為何。 |
| **create** | 如果不存在具有指定**識別碼**的裝置，則表示是新註冊的裝置。<br/>如果裝置已存在，則會在記錄檔中寫入錯誤。 |
| **update** | 如果已存在具有指定**識別碼**的裝置，則會以所提供的輸入資料覆寫現有資訊，而不管 **ETag** 值為何。<br/>如果裝置不存在，則會在記錄檔中寫入錯誤。 |
| **updateIfMatchETag** | 如果已存在具有指定**識別碼**的裝置，則當 **ETag** 相符時，才會以所提供的輸入資料覆寫現有資訊。<br/>如果裝置不存在，則會在記錄檔中寫入錯誤。<br/>如果 **ETag** 不相符，則會在記錄檔中寫入錯誤。 |
| **createOrUpdateIfMatchETag** | 如果不存在具有指定**識別碼**的裝置，則表示是新註冊的裝置。<br/>如果裝置已存在，則當 **ETag** 相符時，才會以所提供的輸入資料覆寫現有資訊。<br/>如果 **ETag** 不相符，則會在記錄檔中寫入錯誤。 |
| **delete** | 如果已存在具有指定**識別碼**的裝置，則會遭到刪除，而不管 **ETag** 值為何。<br/>如果裝置不存在，則會在記錄檔中寫入錯誤。 |
| **deleteIfMatchETag** | 如果已存在具有指定**識別碼**的裝置，則只會在 **ETag** 相符時予以刪除。如果裝置不存在，則會在記錄檔中寫入錯誤。<br/>如果 ETag 不相符，則會在記錄檔中寫入錯誤。 |

> [AZURE.NOTE] 如果序列化資料未明確定義裝置的 **importMode** 旗標，則會在匯入操作期間預設為 **createOrUpdate**。

## 匯入裝置範例 - 大量裝置佈建 

下列 C# 程式碼範例說明如何產生多個包含驗證金鑰的裝置身分識別，將該裝置資訊寫入至 Azure 儲存體區塊 Blob，然後再將裝置匯入到裝置身分識別登錄：

```
// Provision 1,000 more devices
var serializedDevices = new List<string>();

for (var i = 0; i < 1000; i++)
{
// Create a new ExportImportDevice
  var deviceToAdd = new ExportImportDevice()
  {
    Id = Guid.NewGuid().ToString(),
    Status = DeviceStatus.Enabled,
    Authentication = new AuthenticationMechanism()
    {
      SymmetricKey = new SymmetricKey()
      {
        PrimaryKey = CryptoKeyGenerator.GenerateKey(32),
        SecondaryKey = CryptoKeyGenerator.GenerateKey(32)
      }
    },
    ImportMode = ImportMode.Create
  };

  // Add device to existing list
  serializedDevices.Add(JsonConvert.SerializeObject(deviceToAdd));
}

// Write this list to the Azure storage blob
var sb = new StringBuilder();
serializedDevices.ForEach(serializedDevice => sb.AppendLine(serializedDevice));
await blob.DeleteIfExistsAsync();

using (CloudBlobStream stream = await blob.OpenWriteAsync())
{
  byte[] bytes = Encoding.UTF8.GetBytes(sb.ToString());
  for (var i = 0; i < bytes.Length; i += 500)
  {
    int length = Math.Min(bytes.Length - i, 500);
    await stream.WriteAsync(bytes, i, length);
  }
}

// Call import using the same storage blob to add new devices!
// This normally takes 1 minute per 100 devices the normal way
JobProperties importJob = await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);

// Wait until job is finished
while(true)
{
  importJob = await registryManager.GetJobAsync(importJob.JobId);
  if (importJob.Status == JobStatus.Completed || 
      importJob.Status == JobStatus.Failed ||
      importJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}
```

## 匯入裝置範例 - 大量刪除

下列程式碼範例示範如何刪除使用前一個程式碼範例所新增的裝置：

```
// Step 1: Update each device's ImportMode to be Delete
sb = new StringBuilder();
serializedDevices.ForEach(serializedDevice =>
{
  // Deserialize back to an ExportImportDevice
  var device = JsonConvert.DeserializeObject<ExportImportDevice>(serializedDevice);

  // Update property
  device.ImportMode = ImportMode.Delete;

  // Re-serialize
  sb.AppendLine(JsonConvert.SerializeObject(device));
});

// Step 2: Write the new import data back to the block blob
await blob.DeleteIfExistsAsync();
using (CloudBlobStream stream = await blob.OpenWriteAsync())
{
  byte[] bytes = Encoding.UTF8.GetBytes(sb.ToString());
  for (var i = 0; i < bytes.Length; i += 500)
  {
    int length = Math.Min(bytes.Length - i, 500);
    await stream.WriteAsync(bytes, i, length);
  }
}

// Step 3: Call import using the same storage blob to delete all devices!
importJob = await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);

// Wait until job is finished
while(true)
{
  importJob = await registryManager.GetJobAsync(importJob.JobId);
  if (importJob.Status == JobStatus.Completed || 
      importJob.Status == JobStatus.Failed ||
      importJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}

```

## 取得容器 SAS URI


下列程式碼範例示範如何產生具有 Blob 容器之讀取、寫入和刪除權限的 [SAS URI](../storage/storage-dotnet-shared-access-signature-part-2.md)：

```
static string GetContainerSasUri(CloudBlobContainer container)
{
  // Set the expiry time and permissions for the container.
  // In this case no start time is specified, so the
  // shared access signature becomes valid immediately.
  var sasConstraints = new SharedAccessBlobPolicy();
  sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
  sasConstraints.Permissions = 
    SharedAccessBlobPermissions.Write | 
    SharedAccessBlobPermissions.Read | 
    SharedAccessBlobPermissions.Delete;

  // Generate the shared access signature on the container,
  // setting the constraints directly on the signature.
  string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);

  // Return the URI string for the container,
  // including the SAS token.
  return container.Uri + sasContainerToken;
}

```

## 後續步驟

在本文中，您已了解如何對 IoT 中樞內的裝置身分識別登錄執行大量操作。您可以利用下列文章繼續探索 IoT 中樞功能和其他 IoT 案例：

- [以程式設計方式建立 IoT 中樞](iot-hub-rm-template.md)
- [IoT 中樞用量度量](iot-hub-metrics.md)
- [IoT 中樞作業監視](iot-hub-operations-montoring.md)

<!---HONumber=AcomDC_0204_2016-->