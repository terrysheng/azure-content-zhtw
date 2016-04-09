## 建立裝置識別

在本節中，您將建立 Node.js 主控台應用程式，它會在 IoT 中樞的身分識別登錄中建立新的裝置身分識別。裝置無法連線到 IoT 中樞，除非它在裝置身分識別登錄中具有項目。如需詳細資訊，請參閱 [IoT 中心開發人員指南][lnk-devguide-identity]的**裝置識別登錄**一節。執行這個主控台應用程式時，它會產生唯一的裝置識別碼及金鑰，當裝置向 IoT 中樞傳送裝置對雲端訊息時，可以用來識別裝置本身。

1. 建立稱為 **createdeviceidentity** 的新的空資料夾。在 **createdeviceidentity** 資料夾中，於命令提示字元使用下列命令建立新的 package.json 檔案。接受所有預設值：

    ```
    npm init
    ```

2. 在 **createdeviceidentity** 資料夾中，於命令提示字元執行下列命令以安裝 **azure-iothub** 封裝：

    ```
    npm install azure-iothub --save
    ```

3. 使用文字編輯器，在 **createdeviceidentity** 資料夾中建立新的 **CreateDeviceIdentity.js** 檔案。

4. 在 **CreateDeviceIdentity.js** 檔案開頭新增下列 `require` 陳述式：

    ```
    'use strict';
    
    var iothub = require('azure-iothub');
    ```

5. 將下列程式碼加入至 **CreateDeviceIdentity.js** 檔案，並將預留位置的值替換為您在上一節中為 IoT 中樞所建立的連接字串：

    ```
    var connectionString = '{iothub connection string}';
    
    var registry = iothub.Registry.fromConnectionString(connectionString);
    ```

6. 加入下列程式碼以在 IoT 中樞的裝置身分識別登錄建立新的裝置定義。如果登錄中沒有該裝置識別碼，此程式碼會建立新的裝置，否則會傳回現有裝置的金鑰：

    ```
    var device = new iothub.Device(null);
    device.deviceId = 'myFirstDevice';
    registry.create(device, function(err, deviceInfo, res) {
      if (err) {
        registry.get(device.deviceId, printDeviceInfo);
      }
      if (deviceInfo) {
        printDeviceInfo(err, deviceInfo, res)
      }
    });

    function printDeviceInfo(err, deviceInfo, res) {
      if (deviceInfo) {
        console.log('Device id: ' + deviceInfo.deviceId);
        console.log('Device key: ' + deviceInfo.authentication.SymmetricKey.primaryKey);
      }
    }
    ```

7. 儲存並關閉 **CreateDeviceIdentity.js** 檔案。

8. 若要執行 **createdeviceidentity** 應用程式，請在命令提示字元中於 createdeviceidentity 資料夾內執行下列命令：

    ```
    node CreateDeviceIdentity.js 
    ```

9. 記下**裝置識別碼**和**裝置金鑰**。稍後在建立連線至做為裝置之 IoT 中樞的應用程式時，會需要這些資料。

> [AZURE.NOTE] IoT 中樞身分識別登錄只會儲存裝置身分識別，以啟用對中樞的安全存取。它會儲存裝置識別碼和金鑰，來做為安全性認證，以及啟用或停用旗標，讓您停用個別裝置的存取。如果您的應用程式需要儲存其他裝置特定的中繼資料，它應該使用應用程式專用的存放區。如需詳細資訊，請參閱 [IoT 中樞開發人員指南][lnk-devguide-identity]。

## 接收裝置到雲端的訊息

在本節中，您將建立 Node.js 主控台應用程式，以讀取來自 IoT 中樞的裝置到雲端訊息。IoT 中樞會公開與[事件中樞][lnk-event-hubs-overview]相容的端點以讓您讀取裝置到雲端訊息。為了簡單起見，本教學課程會建立的基本讀取器不適合用於高輸送量部署。[處理裝置到雲端的訊息][lnk-processd2c-tutorial]教學課程會說明如何大規模處理裝置到雲端的訊息。[開始使用事件中樞][lnk-eventhubs-tutorial]教學課程則會提供進一步資訊，說明如何處理來自事件中樞的訊息，而且此教學課程也適用於 IoT 中樞事件中樞相容端點。

1. 建立稱為 **readdevicetocloudmessages** 的新的空資料夾。在 **readdevicetocloudmessages** 資料夾中，於命令提示字元使用下列命令建立新的 package.json 檔案。接受所有預設值：

    ```
    npm init
    ```

2. 在 **readdevicetocloudmessages** 資料夾的命令提示字元中，執行下列命令以安裝 **amqp10** 和 **bluebird** 封裝：

    ```
    npm install amqp10 bluebird --save
    ```

3. 使用文字編輯器，在 **readdevicetocloudmessages** 資料夾中建立新的 **ReadDeviceToCloudMessages.js** 檔案。

4. 在 **ReadDeviceToCloudMessages.js** 檔案開頭新增下列 `require` 陳述式：

    ```
    'use strict';

    var AMQPClient = require('amqp10').Client;
    var Policy = require('amqp10').Policy;
    var translator = require('amqp10').translator;
    var Promise = require('bluebird');
    ```

5. 新增下列變數宣告，將預留位置取代為您先前記下的值。**{您的事件中樞相容命名空間}** 預留位置的值來自入口網站中的 [事件中樞相容端點] 欄位，其形式為 **namespace.servicebus.windows.net** (沒有 **sb://* 首碼)。

    ```
    var protocol = 'amqps';
    var eventHubHost = '{your event hub-compatible namespace}';
    var sasName = 'iothubowner';
    var sasKey = '{your iot hub key}';
    var eventHubName = '{your event hub-compatible name}';
    var numPartitions = 2;
    ```

    > [AZURE.NOTE] 此程式碼假設您已在 F1 (免費) 層建立 IoT 中樞。免費 IoT 中樞有 "0" 和 "1" 這兩個資料分割。如果您使用另一種定價層建立 IoT 中樞，則應調整程式碼來為每個資料分割建立 **MessageReceiver**。

6. 新增下列篩選器定義。在建立開始執行後只會讀取傳送到 IoT 中樞之訊息的收件者時，此應用程式會使用篩選器。這很適合測試環境，因為如此一來您就可以看到目前的訊息集，但在生產環境中，您的程式碼應該要確定它能處理所有訊息，如需詳細資訊，請參閱[如何處理 IoT 中樞裝置到雲端訊息][lnk-processd2c-tutorial]教學課程。

    ```
    var filterOffset = new Date().getTime();
    var filterOption;
    if (filterOffset) {
      filterOption = {
      attach: { source: { filter: {
      'apache.org:selector-filter:string': translator(
        ['described', ['symbol', 'apache.org:selector-filter:string'], ['string', "amqp.annotation.x-opt-enqueuedtimeutc > " + filterOffset + ""]])
        } } }
      };
    }
    ```

7. 新增下列程式碼來建立接收位址和 AMQP 用戶端：

    ```
    var uri = protocol + '://' + encodeURIComponent(sasName) + ':' + encodeURIComponent(sasKey) + '@' + eventHubHost;
    var recvAddr = eventHubName + '/ConsumerGroups/$default/Partitions/';
    
    var client = new AMQPClient(Policy.EventHub);
    ```

8. 新增下列兩個會將輸出列印到主控台的函數：

    ```
    var messageHandler = function (partitionId, message) {
      console.log('Received(' + partitionId + '): ', message.body);
    };
    
    var errorHandler = function(partitionId, err) {
      console.warn('** Receive error: ', err);
    };
    ```

9. 新增下列函數，以做為使用篩選器的給定資料分割接收者：

    ```
    var createPartitionReceiver = function(partitionId, receiveAddress, filterOption) {
      return client.createReceiver(receiveAddress, filterOption)
        .then(function (receiver) {
          console.log('Listening on partition: ' + partitionId);
          receiver.on('message', messageHandler.bind(null, partitionId));
          receiver.on('errorReceived', errorHandler.bind(null, partitionId));
        });
    };
    ```

10. 新增下列程式碼，以連接到事件中樞相容端點並啟動接收者：

    ```
    client.connect(uri)
      .then(function () {
        var partitions = [];
        for (var i = 0; i < numPartitions; ++i) {
          partitions.push(createPartitionReceiver(i, recvAddr + i, filterOption));
        }
        return Promise.all(partitions);
    })
    .error(function (e) {
        console.warn('Connection error: ', e);
    });
    ```

11. 儲存並關閉 **ReadDeviceToCloudMessages.js** 檔案。

<!-- Links -->

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide.md#identityregistry
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md
[lnk-processd2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

<!---HONumber=AcomDC_0323_2016-->