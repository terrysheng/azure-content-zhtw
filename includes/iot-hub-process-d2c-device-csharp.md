## 從模擬裝置傳送互動式訊息

在本節中，您將會修改您在[開始使用 IoT 中樞]教學課程中建立的模擬裝置應用程式，將裝置對雲端的互動式訊息傳送至 IoT 中樞。

1. 在 Visual Studio 的 **SimulatedDevice** 專案中，將下列方法加入 [程式] 類別。

    ```
    private static async void SendDeviceToCloudInteractiveMessagesAsync()
    {
      while (true)
      {
        var interactiveMessageString = "Alert message!";
        var interactiveMessage = new Message(Encoding.ASCII.GetBytes(interactiveMessageString));
        interactiveMessage.Properties["messageType"] = "interactive";
        interactiveMessage.MessageId = Guid.NewGuid().ToString();

        await deviceClient.SendEventAsync(interactiveMessage);
        Console.WriteLine("{0} > Sending interactive message: {1}", DateTime.Now, interactiveMessageString);

        Thread.Sleep(10000);
      }
    }
    ```

    此方法與 **SimulatedDevice** 專案中的 **SendDeviceToCloudMessagesAsync** 方法非常類似。唯一的差異是您現在是設定 **MessageId** 系統屬性，以及稱為 **messageType** 的使用者屬性。程式碼會指派全域唯一識別碼 (guid) 給 **MessageId** 屬性，服務匯流排可以用它來刪除重複接收的訊息。範例是使用 **messageType** 屬性來區別來自資料點訊息的互動式訊息。應用程式會在訊息屬性中傳遞此資訊，而不是在訊息主體中傳遞，所以事件處理器不需要還原序列化訊息來執行訊息路由。

    > [AZURE.NOTE] 在裝置程式碼中建立用於刪除重複互動式訊息的 **MessageId** 很重要，因為間歇網路通訊或其他失敗可能造成從裝置多次重新傳輸相同訊息。您也可以使用語意式訊息識別碼 (例如相關訊息資料欄位的雜湊) 來取代 guid。

2. 將下列方法加入 **Main** 方法，就在 `Console.ReadLine()` 行之前：

    ````
    SendDeviceToCloudInteractiveMessagesAsync();
    ````

    > [AZURE.NOTE] 為了簡單起見，本教學課程不會實作任何重試原則。在實際程式碼中，您應該如 MSDN 文章 [Transient Fault Handling (暫時性錯誤處理)] 所建議來實作重試原則 (例如指數型輪詢)。

<!-- Links -->
[Transient Fault Handling (暫時性錯誤處理)]: https://msdn.microsoft.com/library/hh675232.aspx
[開始使用 IoT 中樞]: iot-hub-csharp-csharp-getstarted.md

<!---HONumber=AcomDC_0309_2016-->