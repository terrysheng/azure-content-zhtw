## 在模擬裝置上接收訊息

在本節中，您將修改在 [IoT 中樞入門] 中建立的模擬裝置應用程式，以接收來自 IoT 中樞的雲端對裝置訊息。

1. 在 Visual Studio 的 **SimulatedDevice** 專案中，將下列方法加入至 **Program** 類別。

        private static async void ReceiveC2dAsync()
        {
            Console.WriteLine("\nReceiving cloud to device messages from service");
            while (true)
            {
                Message receivedMessage = await deviceClient.ReceiveAsync();
                if (receivedMessage == null) continue;

                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine("Received message: {0}", Encoding.ASCII.GetString(receivedMessage.GetBytes()));
                Console.ResetColor();

                await deviceClient.CompleteAsync(receivedMessage);
            }
        }

    `ReceiveAsync` 方法會以非同步方式，在裝置收到訊息時，傳回收到的訊息。它會在可指明的逾時期間過後傳回 *null* (在本例中，使用的是預設值 1 分鐘)。當發生這種情況時，我們想讓程式碼繼續等待新訊息。所以加上 `if (receivedMessage == null) continue` 行。

    `CompleteAsync()` 的呼叫會通知 IoT 中樞，說明已成功處理訊息，且可以安全地從裝置佇列中移除該訊息。如果因故導致裝置應用程式無法完成訊息處理作業，IoT 中樞會再傳遞一次；因此裝置應用程式中的訊息處理邏輯必須是「等冪」，換言之，接收多次相同的訊息應該會產生相同的結果。應用程式也可以暫時 `Abandon` 訊息，讓 IoT 中樞將訊息保留在佇列中，以供之後取用；或是 `Reject` 訊息，以永久從佇列移除訊息。如需雲端到裝置訊息生命週期的詳細資訊，請參閱 [IoT 中樞開發人員指南][IoT Hub Developer Guide - C2D]。

> [AZURE.NOTE]使用 HTTP/1 而不使用 AMQP 做為傳輸時，`ReceiveAsync` 會立即傳回。使用 HTTP/1 的雲端到裝置訊息，其支援模式是間歇連接至裝置，且不常檢查訊息 (換言之，比每隔 25 分鐘更少)。發出更多 HTTP/1 接收會導致 IoT 中樞節流要求。如需 AMQP 和 HTTP/1 支援間的差異，以及 IoT 中樞節流的詳細資訊，請參閱 [IoT 中樞開發人員指南][IoT Hub Developer Guide - C2D]。

2. 在 **Main** 方法中，於 `Console.ReadLine()` 行之前，加入下列方法：

        ReceiveC2dAsync();

> [AZURE.NOTE]為了簡單起見，本教學課程不會實作任何重試原則。在實際程式碼中，建議如 MSDN 文章 [暫時性錯誤處理] 所建議，實作重試原則 (例如指數型輪詢)。

<!-- Links -->
[IoT Hub Developer Guide - C2D]: iot-hub-devguide.md#c2d

<!-- Images -->

<!---HONumber=Nov15_HO2-->