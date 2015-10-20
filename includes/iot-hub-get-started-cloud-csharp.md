## 建立裝置識別

在本節中，您將撰寫 Windows 主控台應用程式，以便在 IoT 中心建立新的裝置識別。如需詳細資訊，請參閱 [IoT 中心開發人員指南][IoT Hub Developer Guide - Identity Registry]的**裝置識別登錄**一節。執行此主控台應用程式後，您將擁有一組識別碼和金鑰，做為裝置識別使用，以便將裝置到雲端訊息傳送至 IoT 中心。

1. 在 Visual Studio 中，使用**主控台應用程式**專案範本建立新的「Visual C# 桌面應用程式」專案。將專案命名為 **CreateDeviceIdentity**。

	![][10]

2. 在 [方案總管] 中，以滑鼠右鍵按一下方案，然後按一下 [**管理方案的 NuGet 封裝...**]。

	[NuGet 封裝管理員] 視窗隨即出現。

3. 請確認已選取 [包含發行前版本] 選項。然後搜尋 `Microsoft Azure Devices`，按一下 [安裝] 並接受使用條款。

	![][11]

4. 這會下載及安裝參考，並將其加入 [Microsoft Azure 裝置 SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices/) NuGet 封裝。

4. 在 **Program.cs** 檔案開頭處新增下列 `using` 陳述式：

		using Microsoft.Azure.Devices;
        using Microsoft.Azure.Devices.Common.Exceptions;

5. 將下列欄位加入至 **Program** 類別，並將預留位置的值替換為您在上一節中所建立的 IoT 中樞名稱，同時使用其連接字串：

		static RegistryManager registryManager;
        static string connectionString = "{iothub connection string}";

6. 將下列方法加入至 **Program** 類別：

		private async static Task AddDeviceAsync()
        {
            string deviceId = "myFirstDevice";
            Device device;
            try
            {
                device = await registryManager.AddDeviceAsync(new Device(deviceId));
            }
            catch (DeviceAlreadyExistsException)
            {
                device = await registryManager.GetDeviceAsync(deviceId);
            }
            Console.WriteLine("Generated device key: {0}", device.Authentication.SymmetricKey.PrimaryKey);
        }

	這個方法會建立新的裝置識別，其識別碼為 **myFirstDevice** (要是已存在識別碼相同的裝置識別，系統將會直接擷取)。接著，應用程式會顯示該識別的主要金鑰。模擬裝置會使用此金鑰來連接至 IoT 中心。

7. 最後，將下列幾行加入至 **Main** 方法：

		registryManager = RegistryManager.CreateFromConnectionString(connectionString);
        AddDeviceAsync().Wait();
        Console.ReadLine();

8. 執行此應用程式，並記下裝置金鑰。

    ![][12]

> [AZURE.NOTE]請務必注意，IoT 中心識別登錄只會用來存放裝置識別，以達到安全存取的目的，亦即儲存安全性認證以及啟用/停用個別裝置的存取權。裝置應用程式的中繼資料應儲存在應用程式專用的存放區中。如需詳細資訊，請參閱 [IoT 中樞開發人員指南][IoT Hub Developer Guide - Identity Registry]。

## 接收裝置到雲端的訊息

在本節中，您將建立 Windows 主控台應用程式，以讀取來自 IoT 中心的裝置到雲端訊息。IoT 中樞會公開[事件中樞][Event Hubs Overview]相容的端點以讀取裝置到雲端訊息。為了簡單起見，本教學課程只會建立簡化的讀取器，這不適合在高輸送量部署中使用。如需深入了解如何處理 IoT 中心的裝置到雲端訊息，請參閱 [處理裝置到雲端訊息] 教學課程。如需深入了解如何處理事件中心的訊息，您可以參考[開始使用事件中樞]教學課程。

1. 在目前的 Visual Studio 解決方案中，按一下 [檔案]->[加入]->[專案]，使用 [主控台應用程式] 專案範本建立新的 Visual C# 桌面應用程式專案。將專案命名為 **ReadDeviceToCloudMessages**。

    ![][10]

2. 在 [方案總管] 中，以滑鼠右鍵按一下方案，然後按一下 [**管理 NuGet 封裝**]。

    [**管理 NuGet 封裝**] 對話方塊隨即出現。

3. 搜尋 `WindowsAzure.ServiceBus`，然後按一下 [**安裝**] 並接受使用條款。

    這會下載及安裝參考，並將其連同所有相依性加入 [Azure 服務匯流排](https://www.nuget.org/packages/WindowsAzure.ServiceBus)。

4. 在 **Program.cs** 檔案開頭處新增下列 `using` 陳述式：

        using Microsoft.ServiceBus.Messaging;

5. 將下列欄位加入至 **Program** 類別，並將預留位置的值替換為您在上一節中所建立的 IoT 中樞名稱，同時使用其連接字串：

        static string connectionString = "{iothub connection string}";
        static string iotHubD2cEndpoint = "messages/events";
        static EventHubClient eventHubClient;

6. 將下列方法加入至 **Program** 類別：

        private async static Task ReceiveMessagesFromDeviceAsync(string partition)
        {
            var eventHubReceiver = eventHubClient.GetDefaultConsumerGroup().CreateReceiver(partition, DateTime.Now);
            while (true)
            {
                EventData eventData = await eventHubReceiver.ReceiveAsync();
                if (eventData == null) continue;

                string data = Encoding.UTF8.GetString(eventData.GetBytes());
                Console.WriteLine(string.Format("Message received. Partition: {0} Data: '{1}'", partition, data));
            }
        }

    這個方法會使用 EventHub 用戶端，以接收來自 IoT 中心的所有裝置對雲端接收資料分割。建立 EventHubReceiver 時，請注意 `DateTime.Now` 參數的傳遞方式。以此方式建立的接收者，只會接收參數啟動後傳送的訊息。

7. 最後，將下列幾行加入至 **Main** 方法：

        Console.WriteLine("Receive messages\n");
        eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, iotHubD2cEndpoint);

        var d2cPartitions = eventHubClient.GetRuntimeInformation().PartitionIds;

        foreach (string partition in d2cPartitions)
        {
           ReceiveMessagesFromDeviceAsync(partition);
        }  
        Console.ReadLine();


<!-- Links -->

[Azure IoT - Service SDK NuGet package]: https://www.nuget.org/packages/Microsoft.Azure.Devices/

[開始使用事件中樞]: event-hubs-csharp-ephcs-getstarted.md
[IoT Hub Developer Guide - Identity Registry]: iot-hub-devguide.md#identityregistry

[Event Hubs Overview]: event-hubs-overview.md
[Scaled out event processing]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Event-Hub-45f43fc3
[Azure Storage account]: storage-create-storage-account.md
[EventProcessorHost]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost(v=azure.95).aspx

[Azure Preview Portal]: https://portal.azure.com/


<!-- Images -->
[10]: ./media/iot-hub-getstarted-cloud-csharp/create-identity-csharp1.png
[11]: ./media/iot-hub-getstarted-cloud-csharp/create-identity-csharp2.png
[12]: ./media/iot-hub-getstarted-cloud-csharp/create-identity-csharp3.png

<!---HONumber=Oct15_HO3-->