## 將訊息傳送至事件中心

適用於事件中樞的 Java 用戶端程式庫可以在來自 [Maven 中央儲存機制](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22)的 Maven 專案中使用，而且可在您的 Maven 專案檔內使用下列相依性宣告來參考：

``` XML
<dependency> 
    <groupId>com.microsoft.azure</groupId> 
    <artifactId>azure-eventhubs-clients</artifactId> 
    <version>0.6.0</version> 
</dependency>   
 ```
 
對於不同類型的組建環境，您可以明確地從 [Maven 中央儲存機制](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22)或 [GitHub 上的版本發佈點](https://github.com/Azure/azure-event-hubs/releases)取得最新發行的 JAR 檔案。

如果是簡單事件發行者，請針對事件中樞用戶端類別匯入 *com.microsoft.azure.eventhubs* 封裝，以及針對公用程式類別匯入 *com.microsoft.azure.servicebus* 封裝，例如，與 Azure 服務匯流排訊息用戶端共用的常見例外狀況。

針對下列範例，在您最喜愛的 Java 開發環境中，先為主控台/殼層應用程式建立新的 Maven 專案。此類別將稱為 ```Send```。

``` Java

import java.io.IOException;
import java.nio.charset.*;
import java.util.*;
import java.util.concurrent.ExecutionException;

import com.microsoft.azure.eventhubs.*;
import com.microsoft.azure.servicebus.*;

public class Send
{
	public static void main(String[] args) 
			throws ServiceBusException, ExecutionException, InterruptedException, IOException
	{
```

使用您建立事件中樞時所使用的值，來取代命名空間和事件中樞名稱。`sasKeyName` 和 `sasKey` 會對應到您稍早建立之「傳送」規則的名稱和金鑰。您可以利用該資訊來建立連接字串。

``` Java
	final String namespaceName = "----ServiceBusNamespaceName-----";
	final String eventHubName = "----EventHubName-----";
	final String sasKeyName = "-----SharedAccessSignatureKeyName-----";
	final String sasKey = "---SharedAccessSignatureKey----";
	ConnectionStringBuilder connStr = new ConnectionStringBuilder(namespaceName, eventHubName, sasKeyName, sasKey);
```

接著將字串轉換為它的 utf-8 位元組編碼方式，藉以建立單一事件。然後我們會從連接字串建立新的事件中樞用戶端執行個體，然後傳送訊息。

``` Java 
				
	byte[] payloadBytes = "Test AMQP message from JMS".getBytes("UTF-8");
	EventData sendEvent = new EventData(payloadBytes);
	
	EventHubClient ehClient = EventHubClient.createFromConnectionStringSync(connStr.toString());
	ehClient.sendSync(sendEvent);
	}
}

``` 

<!---HONumber=AcomDC_0323_2016-->