## 從模擬裝置上傳檔案

在本節中，您將修改在 [透過 IoT 中心傳送雲端對裝置的訊息] 中建立的模擬裝置應用程式，以接收來自 IoT 中心的雲端對裝置訊息。

1. 在 Visual Studio 中，以滑鼠右鍵按一下 **SimulatedDevice** 專案，然後按一下 [管理 NuGet 封裝...]。 

    此時會顯示 [管理 NuGet 封裝] 視窗。

2. 搜尋 `WindowsAzure.Storage`，然後按一下 [**安裝**] 並接受使用條款。

    這會下載及安裝參考，並將其加入 [Microsoft Azure 儲存體 SDK](https://www.nuget.org/packages/WindowsAzure.Storage/)。

3. 在 **Program.cs** 檔的頂端，新增下列陳述式：

        using System.IO;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Blob;

4. 在 **Program** 類別中，以下列方式變更 **ReceiveC2dAsync** 方法：
         
        private static async void ReceiveC2dAsync()
        {
            Console.WriteLine("\nReceiving cloud to device messages from service");
            while (true)
            {
                Message receivedMessage = await deviceClient.ReceiveAsync();
                if (receivedMessage == null) continue;

                if (receivedMessage.Properties.ContainsKey("command") && receivedMessage.Properties["command"] == "FileUpload")
                {
                    UploadFileToBlobAsync(receivedMessage);
                    continue;
                }

                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine("Received message: {0}", Encoding.ASCII.GetString(receivedMessage.GetBytes()));
                Console.ResetColor();
            }
        }

    這會使 **ReceiveC2dAsync** 區別出 `command` 屬性設為 `FileUpload` 的訊息，這些訊息將由 **UploadFileToBlobAsync** 方法加以處理。

    新增以下方法來處理檔案上傳命令。
   
        private static async Task UploadFileToBlobAsync(Message fileUploadCommand)
        {
            var fileUri = fileUploadCommand.Properties["fileUri"];
            var blob = new CloudBlockBlob(new Uri(fileUri));

            byte[] data = new byte[10 * 1024 * 1024];
            Random rng = new Random();
            rng.NextBytes(data);

            MemoryStream msWrite = new MemoryStream(data);
            msWrite.Position = 0;
            using (msWrite)
            {
                await blob.UploadFromStreamAsync(msWrite);
            }
            Console.ForegroundColor = ConsoleColor.Yellow;
            Console.WriteLine("Uploaded file to: {0}", fileUri);
            Console.ResetColor();

            await deviceClient.CompleteAsync(fileUploadCommand);
        }

    此方法會使用 Azure Storage SDK，將隨機產生的 10Mb Blob 上傳至指定的 URI。如需如何上傳 Blob 的詳細資訊，請參閱 [Azure 儲存體 - 如何使用 Blob] (英文)。

> [AZURE.NOTE]請注意，這個模擬裝置的實作，只會在上傳 Blob 後，完成雲端對裝置的訊息。此方法可簡化後端中已上傳檔案的處理作業，因為傳遞通知即代表上傳的檔案已可供處理。如 [IoT 中心開發人員指南][IoT Hub Developer Guide - C2D]中所述，但在「可視性逾時」(通常是 1 分鐘) 前未完成的訊息會放回裝置佇列，而 **ReceiveAsync()** 方法會再次接收該訊息。在檔案上傳時間較長的情況下，可能較適合為模擬裝置將目前的上傳工作保持長期存放。這可讓模擬裝置在檔案上傳完畢之前，完成雲端對裝置的訊息，然後傳送雲端對裝置的訊息，以對後端通知工作已完成。

<!-- Links -->
[IoT Hub Developer Guide - C2D]: iot-hub-devguide.md#c2d
[Azure 儲存體 - 如何使用 Blob]: https://azure.microsoft.com/zh-TW/documentation/articles/storage-dotnet-how-to-use-blobs/#upload-a-blob-into-a-container

<!-- Images -->

<!---HONumber=Oct15_HO1-->