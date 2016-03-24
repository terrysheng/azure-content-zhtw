## 佈建 Azure 儲存體帳戶
由於模擬裝置會在 Azure 儲存體 Blob 中上傳檔案，因此您必須擁有 Azure 儲存體帳戶。您可以使用現有帳戶，或是依照[關於 Azure 儲存體]中的指示，另外建立新的帳戶。記下儲存體帳戶連接字串。

## 將 Azure Blob URI 傳送至模擬裝置

在本節中，您將修改在 [使用 loT 中心傳送雲端到裝置訊息] 中建立的 **SendCloudtoDevice** 主控台應用程式，以共用存取簽章將 Azure Blob URI 包含在內。如此一來，雲端後端只會將 Blob 的寫入存取權授與雲端到裝置訊息的收件者。

1. 在 Visual Studio 中，以滑鼠右鍵按一下 **SendCloudtoDevice** 專案，然後按一下 [管理 NuGet 封裝...]。 

    此時會顯示 [管理 NuGet 封裝] 視窗。

2. 搜尋 `WindowsAzure.Storage`，然後按一下 [**安裝**] 並接受使用條款。

    這會下載及安裝參考，並將其加入 [Microsoft Azure 儲存體 SDK](https://www.nuget.org/packages/WindowsAzure.Storage/)。

3. 在 **Program.cs** 檔的頂端，新增下列陳述式：

        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Blob;

4. 在 **Program** 類別中新增下列類別欄位，以替換儲存體帳戶的連接字串。

        static string storageConnectionString = "{storage connection string}";

    接著新增下列方法 (本教學課程使用 **iothubfileuploadtutorial**，不過您可以任何 Blob 容器名稱取而代之)：
   
        private static async Task<string> GenerateBlobUriAsync()
        {
            var storageAccount = CloudStorageAccount.Parse(storageConnectionString);
            var blobClient = storageAccount.CreateCloudBlobClient();
            var blobContainer = blobClient.GetContainerReference("iothubfileuploadtutorial");
            await blobContainer.CreateIfNotExistsAsync();

            var blobName = String.Format("deviceUpload_{0}", Guid.NewGuid().ToString());
            CloudBlockBlob blob = blobContainer.GetBlockBlobReference(blobName);

            SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();
            sasConstraints.SharedAccessStartTime = DateTime.UtcNow.AddMinutes(-5);
            sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
            sasConstraints.Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Write;
            string sasBlobToken = blob.GetSharedAccessSignature(sasConstraints);

            return blob.Uri + sasBlobToken;
        }

    此方法會建立新的 Blob 參考，並利用[透過 Blob 儲存體來建立與使用 SAS](../storage/storage-dotnet-shared-access-signature-part-2.md) 中所述的方式來產生共用存取簽章 URI。請注意，上述方法產生的簽章 URI 的有效時間為 24 小時。如果目標裝置需要更多時間來上傳檔案 (例如裝置不常連接，或上傳大型檔案的連線品質不可靠)，建議您考慮使用到期時間更長的簽章。

5. 依照下列方法修改 **SendCloudToDeviceMessageAsync**：

        private async static Task SendCloudToDeviceMessageAsync()
        {
            var commandMessage = new Message();
            commandMessage.Properties["command"] = "FileUpload";
            commandMessage.Properties["fileUri"] = await GenerateBlobUriAsync();
            commandMessage.Ack = DeliveryAcknowledgement.Full;

            await serviceClient.SendAsync("myFirstDevice", commandMessage);
        }

    這個方法會傳送雲端到裝置訊息，其中包含兩個應用程式屬性：一個會將此訊息識別為上傳檔案的命令，另一個則保留 Blob URI。這也會要求完整的傳遞通知。請注意，這兩個應用程式屬性中的資訊可能會在訊息本文中序列化，但這樣一來，將資訊序列化及還原序列化就需要額外的處理程序。

<!-- Links -->

[關於 Azure 儲存體]: ../storage/storage-create-storage-account.md#create-a-storage-account

[IoT Hub Developer Guide - C2D]: iot-hub-devguide.md#c2d
[Azure IoT - Service SDK NuGet package]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Get started with IoT Hub]: iot-hub-csharp-csharp-getstarted.md

<!-- Images -->

<!----HONumber=AcomDC_0309_2016-->