## 建立模擬裝置應用程式

在本節中，您會撰寫 Windows 主控台應用程式，模擬裝置傳送裝置對雲端訊息至 IoT 中樞。

1. 在 Visual Studio 中，使用**主控台應用程式**專案範本將新的 Visual C# Windows 傳統桌面專案加入至目前的方案。將專案命名為 **SimulatedDevice**。

   	![][30]

2. 在 [方案總管] 中，以滑鼠右鍵按一下 **SimulatedDevice** 專案，然後按一下 [管理 NuGet 封裝]。

3. 在 [NuGet 封裝管理員] 視窗中，搜尋 [Microsoft Azure 裝置用戶端]，按一下 [安裝] 並接受使用規定。

	這會下載及安裝參考，並將其加入 [Azure IoT - 裝置 SDK NuGet 封裝][lnk-device-nuget]。

4. 在 **Program.cs** 檔案開頭處新增下列 `using` 陳述式：

		using Microsoft.Azure.Devices.Client;
        using Newtonsoft.Json;
        using System.Threading;

5. 在 [程式] 類別加入下列欄位，以*建立 IoT 中樞*小節中擷取的 IoT 中樞主機名稱和*建立裝置身分識別*小節中擷取的裝置金鑰取代預留位置值：

		static DeviceClient deviceClient;
        static string iotHubUri = "{iot hub hostname}";
        static string deviceKey = "{device key}";

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

	這個方法會每秒傳送新的裝置對雲端訊息。此訊息會包含 JSON 序列化物件及 deviceId 與隨機產生的數字，以模擬風向速度感應器。

7. 最後，將下列幾行加入至 **Main** 方法：

        Console.WriteLine("Simulated device\n");
        deviceClient = DeviceClient.Create(iotHubUri, new DeviceAuthenticationWithRegistrySymmetricKey("myFirstDevice", deviceKey));

        SendDeviceToCloudMessagesAsync();
        Console.ReadLine();

  根據預設，**Create** 方法會建立一個使用 AMQP 通訊協定的 **DeviceClient** 來和 IoT 中樞通訊。若要使用 HTTPS 通訊協定，請使用可讓您指定通訊協定的 **Create** 方法的覆寫。若您選擇使用 HTTPS 通訊協定，您也應該將 **Microsoft.AspNet.WebApi.Client** NuGet 封裝新增至您的專案，以包含 **System.Net.Http.Formatting** 命名空間。


> [AZURE.NOTE] 為了簡單起見，本教學課程不會實作任何重試原則。在實際程式碼中，您應該如 MSDN 文章[暫時性錯誤處理][lnk-transient-faults]所建議，實作重試原則 (例如指數型輪詢)。

<!-- Links -->

[lnk-device-nuget]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

<!-- Images -->
[30]: ./media/iot-hub-getstarted-device-csharp/create-identity-csharp1.png

<!---HONumber=AcomDC_0309_2016-->