## 從應用程式後端傳送雲端到裝置訊息

在本節中，您會撰寫 Windows 主控台應用程式，將雲端到裝置訊息傳送至模擬裝置應用程式。

1. 在目前的 Visual Studio 方案中，使用 [主控台應用程式] 專案範本建立新的 Visual C# 桌面應用程式專案。將專案命名為 **SendCloudToDevice**。

   	![][20]

2. 在 [方案總管] 中，以滑鼠右鍵按一下方案，然後按一下 [**管理方案的 NuGet 封裝...**]。

	此時會顯示 [管理 NuGet 封裝] 視窗。

3. 搜尋 `Microsoft Azure Devices`，然後按一下 [**安裝**] 並接受使用條款。

	這會下載及安裝參考，並將其加入 [Azure IoT - 服務 SDK NuGet 封裝]。

4. 在 **Program.cs** 檔案開頭處新增下列 `using` 陳述式：

		using Microsoft.Azure.Devices;

5. 將下列欄位加入至 **Program** 類別，並將預留位置的值替換為 IoT 中心名稱，以及[開始使用 IoT 中心] 中的 IoT 中心連接字串：

		static ServiceClient serviceClient;
        static string connectionString = "{iot hub connection string}";

6. 將下列方法加入至 **Program** 類別：

		private async static Task SendCloudToDeviceMessageAsync()
        {
            var commandMessage = new Message(Encoding.ASCII.GetBytes("Cloud to device message."));
            await serviceClient.SendAsync("myFirstDevice", commandMessage);
        }

	這個方法會將新的雲端到裝置訊息傳送至識別碼為 `myFirstDevice` 的裝置。若您修改了[開始使用 IoT 中心]內的方法，則需一併變更此參數。

7. 最後，將下列幾行加入至 **Main** 方法：

        Console.WriteLine("Send Cloud-to-Device message\n");
        serviceClient = ServiceClient.CreateFromConnectionString(connectionString);

        Console.WriteLine("Press any key to send a C2D message.");
        Console.ReadLine();
        SendCloudToDeviceMessageAsync().Wait();
        Console.ReadLine();

8. 在 Visual Studio 中，以滑鼠右鍵按一下您的方案，然後選取 [設定啟始專案...]。選取 [多個啟始專案]，然後同時針對 **ProcessDeviceToCloudMessages**、**SimulatedDevice** 以及 **SendCloudToDevice** 選取 [啟動] 動作。

9.  按下 **F5**，您應該會看到這三個應用程式啟動。選取 [SendCloudToDevice] 視窗，然後按下 **Enter**：您應該會看到模擬應用程式收到訊息。

    ![][21]

## 接收傳遞意見反應
您可以從 IoT 中心要求每個雲端到裝置訊息的傳遞 (或到期) 通知。如此一來，雲端後端就能輕鬆地通知重試或補償邏輯。如需雲端到裝置意見反應的詳細資訊，請參閱 [IoT 中心開發人員指南][IoT Hub Developer Guide - C2D]。

在本節中，您將修改 **SendCloudToDevice** 應用程式以要求意見反應，並接收來自 IoT 中心的意見反應。

1. 在 Visual Studio 的 **SendCloudToDevice** 專案中，將下列方法加入至 **Program** 類別。
   
        private async static void ReceiveFeedbackAsync()
        {
            var feedbackReceiver = serviceClient.GetFeedbackReceiver();

            Console.WriteLine("\nReceiving c2d feedback from service");
            while (true)
            {
                var feedbackBatch = await feedbackReceiver.ReceiveAsync();
                if (feedbackBatch == null) continue;

                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine("Received feedback: {0}", string.Join(", ", feedbackBatch.Records.Select(f => f.StatusCode)));
                Console.ResetColor();

                await feedbackReceiver.CompleteAsync(feedbackBatch);
            }
        }

    請注意，這裡的接收模式，與用來從裝置應用程式接收雲端到裝置訊息的模式相同。

2. 將下列方法加入 **Main** 方法的 `serviceClient = ServiceClient.CreateFromConnectionString(connectionString)` 行之後：

        ReceiveFeedbackAsync();

3. 為了要求針對傳遞雲端到裝置訊息的意見反應，您必須在 **SendCloudToDeviceMessageAsync** 方法中指定屬性。將以下行加入 `var commandMessage = new Message(...);` 行之後：

        commandMessage.Ack = DeliveryAcknowledgement.Full;

4.  按下 **F5** 以執行應用程式，您應該會看到這三個應用程式啟動。選取 [SendCloudToDevice] 視窗，然後按下 **Enter**：您應該會看到模擬應用程式收到訊息，並在幾秒鐘後，您的 **SendCloudToDevice** 應用程式收到意見反應訊息。

    ![][22]

> [AZURE.NOTE]為了簡單起見，本教學課程不會實作任何重試原則。在實際程式碼中，建議如 MSDN 文章[暫時性錯誤處理]所建議，實作重試原則 (例如指數型輪詢)。

<!-- Links -->

[IoT Hub Developer Guide - C2D]: iot-hub-devguide.md#c2d
[Azure IoT - 服務 SDK NuGet 封裝]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[暫時性錯誤處理]: https://msdn.microsoft.com/zh-TW/library/hh680901(v=pandp.50).aspx
[開始使用 IoT 中心]: iot-hub-csharp-csharp-getstarted.md

<!-- Images -->
[20]: ./media/iot-hub-c2d-cloud-csharp/create-identity-csharp1.png
[21]: ./media/iot-hub-c2d-cloud-csharp/sendc2d1.png
[22]: ./media/iot-hub-c2d-cloud-csharp/sendc2d2.png

<!---HONumber=Oct15_HO1-->