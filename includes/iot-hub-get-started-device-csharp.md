## 建立模擬裝置應用程式

在本節中，您會撰寫 Windows 主控台應用程式，模擬裝置傳送裝置對雲端訊息至 IoT 中心。

1. 在目前的 Visual Studio 解決方案中，按一下 [檔案]->[加入]->[專案]，使用 [主控台應用程式] 專案範本建立新的 Visual C# 桌面應用程式專案。將專案命名為 **SimulatedDevice**。

   	![][30]

2. 在 [方案總管] 中，以滑鼠右鍵按一下方案，然後按一下 [**管理方案的 NuGet 封裝...**]。

	這會顯示 [管理 NuGet 封裝] 視窗。

3. 搜尋 `Microsoft Azure Devices Client`，然後按一下 [**安裝**] 並接受使用條款。

	這會下載及安裝參考，並將其加入 [Azure IoT - 裝置 SDK NuGet 封裝]。

4. 在 **Program.cs** 檔案開頭處新增下列 `using` 陳述式：

		using Microsoft.Azure.Devices.Client;
        using Newtonsoft.Json;
        using System.Threading;

5. 在 [程式] 類別加入下列欄位，分別以 IoT 中心 URI 和擷取自**建立 IoT 中心**和**建立裝置身分識別**區段的裝置機碼取代預留位置值：

		static DeviceClient deviceClient;
        static string iotHubUri = "{iot hub URI}";
        static string deviceKey = "{deviceKey}";

6. 將下列方法加入至 **Program** 類別：

		private static async void SendDeviceToCloudMessagesAsync()
        {
            double avgWindSpeed = 10; // m/s
            Random rand = new Random();

            while (true)
            {
                double currentWindSpeed = avgWindSpeed + rand.NextDouble() * 4 - 2;

                var telemetryDataPoint = new
                {
                    deviceId = "myFirstDevice",
                    windSpeed = currentWindSpeed
                };
                var messageString = JsonConvert.SerializeObject(telemetryDataPoint);
                var message = new Message(Encoding.ASCII.GetBytes(messageString));

                await deviceClient.SendEventAsync(message);
                Console.WriteLine("{0} > Sending message: {1}", DateTime.Now, messageString);

                Thread.Sleep(1000);
            }
        }

	這個方法會每秒傳送新的裝置對雲端訊息，包含 JSON 序列化物件及 deviceId 與隨機產生的數字，代表模擬的風速感應器。

7. 最後，將下列幾行加入至 **Main** 方法：

        Console.WriteLine("Simulated device\n");
        deviceClient = DeviceClient.Create(iotHubUri, new DeviceAuthenticationWithRegistrySymmetricKey("myFirstDevice", deviceKey));

        SendDeviceToCloudMessagesAsync();
        Console.ReadLine();

> [AZURE.NOTE]為求簡單，本教學課程不會實作任何重試原則。在實際程式碼中，建議如 MSDN 文章[暫時性錯誤處理]所建議，實作重試原則 (例如指數型輪詢)。

<!-- Links -->

[Azure IoT - 裝置 SDK NuGet 封裝]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[暫時性錯誤處理]: https://msdn.microsoft.com/zh-TW/library/hh680901(v=pandp.50).aspx

<!-- Images -->
[30]: ./media/iot-hub-getstarted-device-csharp/create-identity-csharp1.png

<!---HONumber=Oct15_HO1-->