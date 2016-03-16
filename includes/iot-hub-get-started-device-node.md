## 建立模擬裝置應用程式

在本節中，您會撰寫 Node.js 主控台應用程式，模擬裝置傳送裝置對雲端訊息至 IoT 中樞。

1. 建立稱為 **simulateddevice** 的新的空資料夾。在 **simulateddevice** 資料夾中，於命令提示字元使用下列命令建立新的 package.json 檔案。接受所有預設值：

    ```
    npm init
    ```

2. 在 **simulateddevice** 資料夾中，於命令提示字元執行下列命令以安裝 **azure-iot-device-amqp** 封裝：

    ```
    npm install azure-iot-device-amqp --save
    ```

3. 使用文字編輯器，在 **simulateddevice** 資料夾中建立新的 **SimulatedDevice.js** 檔案。

4. 在 **SimulatedDevice.js** 檔案開頭新增下列 `require` 陳述式：

    ```
    'use strict';

    var clientFromConnectionString = require('azure-iot-device-amqp').clientFromConnectionString;
    var Message = require('azure-iot-device').Message;
    ```

5. 新增 **connectionString** 變數，並用它來建立裝置用戶端。將 **{youriothubname}** 取代為 IoT 中樞名稱，並將 **{yourdeviceid}** 和 **{yourdevicekey}** 取代為您在*建立裝置身分識別*一節中產生的裝置值：

    ```
    var connectionString = 'HostName={youriothubname}.azure-devices.net;DeviceId={yourdeviceid};SharedAccessKey={yourdevicekey}';
    
    var client = clientFromConnectionString(connectionString);
    ```

6. 新增下列函數來顯示應用程式的輸出：

    ```
    function printResultFor(op) {
      return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ': message sent');
      };
    }
    ```

7. 使用 **setInterval** 函數每秒將新訊息傳送至 IoT 中樞：

    ```
    setInterval(function(){
      var windSpeed = 10 + (Math.random() * 4);
      var data = JSON.stringify({ deviceId: 'myFirstDevice', windSpeed: windSpeed });
      var message = new Message(data);
      console.log("Sending message: " + message.getData());
      client.sendEvent(message, printResultFor('send'));
    }, 1000);
    ```

8. 儲存並關閉 **SimulatedDevice.js** 檔案。

> [AZURE.NOTE] 為了簡單起見，本教學課程不會實作任何重試原則。在實際程式碼中，您應該如 MSDN 文章[暫時性錯誤處理][lnk-transient-faults]所建議，實作重試原則 (例如指數型輪詢)。

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/zh-TW/library/hh680901(v=pandp.50).aspx

<!---HONumber=AcomDC_0128_2016-->