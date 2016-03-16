## 建立模擬裝置應用程式

在本節中，您會撰寫 Java 主控台應用程式，模擬裝置傳送裝置對雲端訊息至 IoT 中樞。

1. 在*建立裝置識別*一節所建立的 iot-java-get-started 資料夾中，於命令提示字元下使用下列命令建立稱為 **simulated-device** 的新 Maven 專案。請注意，這是一個非常長的命令：

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. 在命令提示字元中，瀏覽到新的 simulated-device 資料夾。

3. 使用文字編輯器，在 simulated-device 資料夾中開啟 pom.xml 檔案，並對 [相依性] 節點新增下列相依性。這可讓您使用應用程式中的 iothub-java-client 封裝與 IoT 中樞通訊，以及將 Java 物件序列化為 JSON：

    ```
    <dependency>
      <groupId>com.microsoft.azure.iothub-java-client</groupId>
      <artifactId>iothub-java-device-client</artifactId>
      <version>1.0.0</version>
    </dependency>
    <dependency>
      <groupId>com.google.code.gson</groupId>
      <artifactId>gson</artifactId>
      <version>2.3.1</version>
    </dependency>
    ```

4. 儲存並關閉 pom.xml 檔案。

5. 使用文字編輯器開啟 simulated-device\\src\\main\\java\\com\\mycompany\\app\\App.java 檔案。

6. 在此檔案中新增下列 **import** 陳述式：

    ```
    import com.microsoft.azure.iothub.DeviceClient;
    import com.microsoft.azure.iothub.IotHubClientProtocol;
    import com.microsoft.azure.iothub.Message;
    import com.microsoft.azure.iothub.IotHubStatusCode;
    import com.microsoft.azure.iothub.IotHubEventCallback;
    import com.microsoft.azure.iothub.IotHubMessageResult;
    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.security.InvalidKeyException;
    import java.util.Random;
    import javax.naming.SizeLimitExceededException;
    import com.google.gson.Gson;
    ```

7. 將下列類別層級變數新增至 **App** 類別，將 **{youriothubname}** 取代為 IoT 中樞名稱，並將 **{yourdeviceid}** 和 **{yourdevicekey}** 取代為您在*建立裝置識別*一節中產生的裝置值：

    ```
    private static String connString = "HostName={youriothubname}.azure-devices.net;DeviceId={yourdeviceid};SharedAccessKey={yourdevicekey}";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.AMQPS;
    private static boolean stopThread = false;
    ```

    此範例應用程式在具現化 **DeviceClient** 物件時使用 **protocol** 變數。您可以使用 HTTPS 或 AMQPS 通訊協定與 IoT 中樞進行通訊。

8. 在 **App** 類別內新增下列巢狀 **TelemetryDataPoint** 類別，以指定裝置傳送至 IoT 中樞的遙測資料：

    ```
    private static class TelemetryDataPoint {
      public String deviceId;
      public double windSpeed;
        
      public String serialize() {
        Gson gson = new Gson();
        return gson.toJson(this);
      }
    }
    ```

9. 在 **App** 類別內新增下列巢狀 **EventCallback** 類別，以顯示 IoT 中樞在處理來自模擬裝置的訊息時，所傳回的認可狀態。這個方法也會在處理訊息時通知應用程式中的主執行緒：

    ```
    private static class EventCallback implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to message with status " + status.name());
      
        if (context != null) {
          synchronized (context) {
            context.notify();
          }
        }
      }
    }
    ```

10. 在 **App** 類別中新增下列巢狀 **MessageSender** 類別。此類別中的 **run** 方法會產生範例遙測資料以傳送至 IoT 中樞，並先等待認可再傳送下一則訊息：

    ```
    private static class MessageSender implements Runnable {
      public volatile boolean stopThread = false;

      public void run()  {
        try {
          double avgWindSpeed = 10; // m/s
          Random rand = new Random();
          DeviceClient client;
          client = new DeviceClient(connString, protocol);
          client.open();
        
          while (!stopThread) {
            double currentWindSpeed = avgWindSpeed + rand.nextDouble() * 4 - 2;
            TelemetryDataPoint telemetryDataPoint = new TelemetryDataPoint();
            telemetryDataPoint.deviceId = "myFirstDevice";
            telemetryDataPoint.windSpeed = currentWindSpeed;
      
            String msgStr = telemetryDataPoint.serialize();
            Message msg = new Message(msgStr);
            System.out.println(msgStr);
        
            Object lockobj = new Object();
            EventCallback callback = new EventCallback();
            client.sendEventAsync(msg, callback, lockobj);
    
            synchronized (lockobj) {
              lockobj.wait();
            }
            Thread.sleep(1000);
          }
          client.close();
        } catch (Exception e) {
          e.printStackTrace();
        }
      }
    }
    ```

    這個方法會在 IoT 中樞認可先前的訊息一秒後，傳送新的裝置到雲端訊息。此訊息會包含 JSON 序列化物件及 deviceId 與隨機產生的數字，以模擬風向速度感應器。

11. 將 **main** 方法取代為下列程式碼，以建立執行緒來將裝置到雲端訊息傳送至 IoT 中樞：

    ```
    public static void main( String[] args ) throws IOException, URISyntaxException {
    
      MessageSender ms0 = new MessageSender();
      Thread t0 = new Thread(ms0);
      t0.start(); 
    
      System.out.println("Press ENTER to exit.");
      System.in.read();
      ms0.stopThread = true;
    }
    ```

12. 儲存並關閉 App.java 檔案。

13. 若要使用 Maven 建置 **simulated-device** 應用程式，請在命令提示字元中於 simulated-device 資料夾內執行下列命令：

    ```
    mvn clean package -DskipTests
    ```

> [AZURE.NOTE] 為了簡單起見，本教學課程不會實作任何重試原則。在實際程式碼中，您應該如 MSDN 文章[暫時性錯誤處理][lnk-transient-faults]所建議，實作重試原則 (例如指數型輪詢)。

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

<!---HONumber=AcomDC_0204_2016-->