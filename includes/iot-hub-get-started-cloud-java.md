## 建立裝置識別

在本節中，您將建立 Java 主控台應用程式，它會在 IoT 中樞的身分識別登錄中建立新的裝置身分識別。裝置無法連線到 IoT 中樞，除非它在裝置身分識別登錄中具有項目。如需詳細資訊，請參閱 [IoT 中心開發人員指南][lnk-devguide-identity]的**裝置識別登錄**一節。執行這個主控台應用程式時，它會產生唯一的裝置識別碼及金鑰，當裝置向 IoT 中樞傳送裝置對雲端訊息時，可以用來識別裝置本身。

1. 建立稱為 iot-java-get-started 的新的空資料夾。在 iot-java-get-started 資料夾中，於命令提示字元下使用下列命令建立稱為 **create-device-identity** 的新 Maven 專案。請注意，這是一個非常長的命令：

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=create-device-identity -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. 在命令提示字元中，瀏覽到新的 create-device-identity 資料夾。

3. 使用文字編輯器，在 create-device-identity 資料夾中開啟 pom.xml 檔案，並對 [相依性] 節點新增下列相依性。這可讓您在應用程式中使用 iothub-service-sdk 封裝：

    ```
    <dependency>
      <groupId>com.microsoft.azure.iothub-java-client</groupId>
      <artifactId>iothub-java-service-client</artifactId>
      <version>1.0.0</version>
    </dependency>
    ```
    
4. 儲存並關閉 pom.xml 檔案。

5. 使用文字編輯器開啟 create-device-identity\\src\\main\\java\\com\\mycompany\\app\\App.java 檔案。

6. 在此檔案中新增下列 **import** 陳述式：

    ```
    import com.microsoft.azure.iot.service.exceptions.IotHubException;
    import com.microsoft.azure.iot.service.sdk.Device;
    import com.microsoft.azure.iot.service.sdk.RegistryManager;

    import java.io.IOException;
    import java.net.URISyntaxException;
    ```

7. 將下列類別層級變數新增至 [應用程式] 類別，並以您稍早記下的值取代 **{yourhubname}** 和 **{yourhubkey}**。

    ```
    private static final String connectionString = "HostName={yourhubname}.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey={yourhubkey}";
    private static final String deviceId = "javadevice";
    
    ```
    
8. 修改 **main** 方法的簽章以加入如下所示的例外狀況：

    ```
    public static void main( String[] args ) throws IOException, URISyntaxException, Exception
    ```
    
9. 新增下列程式碼做為 **main** 方法的主體。此程式碼會在 IoT 中樞身分識別登錄中建立稱為 *javadevice* 的裝置 (如果還沒有裝置)。然後，它會顯示稍後需要用到的裝置識別碼和金鑰：

    ```
    RegistryManager registryManager = RegistryManager.createFromConnectionString(connectionString);

    Device device = Device.createFromId(deviceId, null, null);
    try {
      device = registryManager.addDevice(device);
    } catch (IotHubException iote) {
      try {
        device = registryManager.getDevice(deviceId);
      } catch (IotHubException iotf) {
        iotf.printStackTrace();
      }
    }
    System.out.println("Device id: " + device.getDeviceId());
    System.out.println("Device key: " + device.getPrimaryKey());
    ```

10. 儲存並關閉 App.java 檔案。

11. 若要使用 Maven 建置 **create-device-identity** 應用程式，請在命令提示字元中於 create-device-identity 資料夾內執行下列命令：

    ```
    mvn clean package -DskipTests
    ```

12. 若要使用 Maven 執行 **create-device-identity** 應用程式，請在命令提示字元中於 create-device-identity 資料夾內執行下列命令：

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

13. 記下**裝置識別碼**和**裝置金鑰**。稍後在建立連線至做為裝置之 IoT 中樞的應用程式時，會需要這些資料。

> [AZURE.NOTE] IoT 中樞身分識別登錄只會儲存裝置身分識別，以啟用對中樞的安全存取。它會儲存裝置識別碼和金鑰，來做為安全性認證，以及啟用或停用旗標，讓您停用個別裝置的存取。如果您的應用程式需要儲存其他裝置特定的中繼資料，它應該使用應用程式專用的存放區。如需詳細資訊，請參閱 [IoT 中樞開發人員指南][lnk-devguide-identity]。

## 接收裝置到雲端的訊息

在本節中，您將建立 Java 主控台應用程式，以讀取來自 IoT 中樞的裝置到雲端訊息。IoT 中樞會公開與[事件中樞][lnk-event-hubs-overview]相容的端點以讓您讀取裝置到雲端訊息。為了簡單起見，本教學課程會建立的基本讀取器不適合用於高輸送量部署。[處理裝置到雲端的訊息][lnk-processd2c-tutorial]教學課程會說明如何大規模處理裝置到雲端的訊息。 [開始使用事件中樞][lnk-eventhubs-tutorial]教學課程則會提供進一步資訊，說明如何處理來自事件中樞的訊息，而且此教學課程也適用於 IoT 中樞事件中樞相容端點。

1. 在*建立裝置識別*一節所建立的 iot-java-get-started 資料夾中，於命令提示字元下使用下列命令建立稱為 **read-d2c-messages** 的新 Maven 專案。請注意，這是一個非常長的命令：

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=read-d2c-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. 在命令提示字元中，瀏覽到新的 read-d2c-messages 資料夾。

3. 使用文字編輯器，在 read-d2c-messages 資料夾中開啟 pom.xml 檔案，並對 [相依性] 節點新增下列相依性。這可讓您在應用程式中使用 eventhubs-client 封裝，以從事件中樞相容端點進行讀取：

    ```
    <dependency>
      <groupId>com.microsoft.eventhubs.client</groupId>
      <artifactId>eventhubs-client</artifactId>
      <version>1.0</version>
    </dependency>
    ```

4. 儲存並關閉 pom.xml 檔案。

5. 使用文字編輯器開啟 read-d2c-messages\\src\\main\\java\\com\\mycompany\\app\\App.java 檔案。

6. 在此檔案中新增下列 **import** 陳述式：

    ```
    import java.io.IOException;
    import com.microsoft.eventhubs.client.Constants;
    import com.microsoft.eventhubs.client.EventHubClient;
    import com.microsoft.eventhubs.client.EventHubEnqueueTimeFilter;
    import com.microsoft.eventhubs.client.EventHubException;
    import com.microsoft.eventhubs.client.EventHubMessage;
    import com.microsoft.eventhubs.client.EventHubReceiver;
    import com.microsoft.eventhubs.client.ConnectionStringBuilder;
    ```

7. 將下列類別層級變數新增至 **App** 類別：

    ```
    private static EventHubClient client;
    private static long now = System.currentTimeMillis();
    ```

8. 在 **App** 類別中新增下列巢狀類別。應用程式會建立兩個執行緒來執行 **MessageReceiver**，以從事件中樞讀取兩個資料分割內的訊息：

    ```
    private static class MessageReceiver implements Runnable
    {
        public volatile boolean stopThread = false;
        private String partitionId;
    }
    ```

9. 將下列建構函式新增到 **MessageReceiver** 類別：

    ```
    public MessageReceiver(String partitionId) {
        this.partitionId = partitionId;
    }
    ```

10. 將下列 **run** 方法新增到 **MessageReceiver** 類別。這個方法會建立 **EventHubReceiver** 執行個體以從事件中樞資料分割進行讀取。它會不斷重複，並列印訊息詳細資料到主控台，直到 **stopThread** 為 true。

    ```
    public void run() {
      try {
        EventHubReceiver receiver = client.getConsumerGroup(null).createReceiver(partitionId, new EventHubEnqueueTimeFilter(now), Constants.DefaultAmqpCredits);
        System.out.println("** Created receiver on partition " + partitionId);
        while (!stopThread) {
          EventHubMessage message = EventHubMessage.parseAmqpMessage(receiver.receive(5000));
          if(message != null) {
            System.out.println("Received: (" + message.getOffset() + " | "
                + message.getSequence() + " | " + message.getEnqueuedTimestamp()
                + ") => " + message.getDataAsString());
          }
        }
        receiver.close();
      }
      catch(EventHubException e) {
        System.out.println("Exception: " + e.getMessage());
      }
    }
    ```

    > [AZURE.NOTE] 在建立開始執行後只會讀取傳送到 IoT 中樞之訊息的收件者時，這個方法會使用篩選器。這很適合測試環境，因為如此一來您就可以看到目前的訊息集，但在生產環境中，您的程式碼應該要確定它能處理所有訊息，如需詳細資訊，請參閱[如何處理 IoT 中樞裝置到雲端訊息][lnk-processd2c-tutorial]教學課程。

11. 修改 **main** 方法的簽章以加入如下所示的例外狀況：

    ```
    public static void main( String[] args ) throws IOException
    ```

12. 在 **App** 類別中對 **main** 方法新增下列程式碼。此程式碼會建立 **EventHubClient** 執行個體以連線到 IoT 中樞上的事件中樞相容端點。然後，它會建立兩個執行緒以從兩個資料分割進行讀取。以先前記下的值取代 **{youriothubkey}**、**{youreventhubcompatiblenamespace}** 和 **{youreventhubcompatiblename}**。**{youreventhubcompatiblenamespace}** 預留位置的值來自 **事件中樞相容端點**，其形式為 **xxxxnamespace.servicebus.windows.net**。

    ```
    String policyName = "iothubowner";
    String policyKey = "{youriothubkey}";
    String namespace = "{youreventhubcompatiblenamespace}";
    String name = "{youreventhubcompatiblename}";
    try {
      ConnectionStringBuilder csb = new ConnectionStringBuilder(policyName, policyKey, namespace);
      client = EventHubClient.create(csb.getConnectionString(), name);
    }
    catch(EventHubException e) {
        System.out.println("Exception: " + e.getMessage());
    }
    
    MessageReceiver mr0 = new MessageReceiver("0");
    MessageReceiver mr1 = new MessageReceiver("1");
    Thread t0 = new Thread(mr0);
    Thread t1 = new Thread(mr1);
    t0.start(); t1.start();

    System.out.println("Press ENTER to exit.");
    System.in.read();
    mr0.stopThread = true;
    mr1.stopThread = true;
    client.close();
    ```

    > [AZURE.NOTE] 此程式碼假設您已在 F1 (免費) 層建立 IoT 中樞。免費 IoT 中樞有 "0" 和 "1" 這兩個資料分割。如果您使用另一種定價層建立 IoT 中樞，則應調整程式碼來為每個資料分割建立 **MessageReceiver**。

13. 儲存並關閉 App.java 檔案。

14. 若要使用 Maven 建置 **read-d2c-messages** 應用程式，請在命令提示字元中於 read-d2c-messages 資料夾內執行下列命令：

    ```
    mvn clean package -DskipTests
    ```



<!-- Links -->

[lnk-eventhubs-tutorial]: event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide.md#identityregistry
[lnk-event-hubs-overview]: event-hubs-overview.md
[lnk-processd2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

<!---HONumber=AcomDC_0218_2016-->