## 將訊息傳送至事件中心
在本節中，我們會撰寫一個 Java 主控台應用程式，以將事件傳送至事件中心。我們將利用 [Apache Qpid 專案](http://qpid.apache.org/)中的 JMS AMQP 提供者。這與搭配使用 Service Bus Queues and Topics 與透過 Java 的 AMQP 類似 (如[這裡](http://azure.microsoft.com/zh-tw/documentation/articles/service-bus-java-how-to-use-jms-api-amqp/)所示)。如需詳細資訊，請參閱 [Qpid JMS 文件](http://qpid.apache.org/releases/qpid-0.30/programming/book/QpidJMS.html)和 [Java 訊息服務](http://www.oracle.com/technetwork/java/jms/index.html)。

1. 在 Eclipse 中，建立名為 **Sender** 的新 Java 專案。

2. 從[這裡](http://qpid.apache.org/components/qpid-jms/index.html)下載最新版的 **Qpid JMS AMQP 1.0** 程式庫。

3. 從封存中解壓縮檔案，並將下列 jars 從封存 'qpid-amqp-1-0-client-jms\<version>\lib' 目錄複製到 Eclipse**Sender** 專案。

4. 在 Eclipse 封裝總管中，以滑鼠右鍵按一下 **Sender** 專案，然後選取 [屬性]****。在對話方塊的左窗格中，按一下 [Java Build Path]****，然後依序按一下 [程式庫]**** 索引標籤和 [Add JARs]**** 按鈕。選取所有先前複製的 jar，然後按一下 [確定]****。

	![][8]

5. 在 **Sender** 專案的根目錄中，使用下列內容建立名為 **servicebus.properties** 的檔案。請務必替代您事件中心名稱和命名空間名稱的值 (後者通常是 '{event hub name}-ns')。您也必須替代先前針對 **SendRule** 建立之金鑰的 URL 編碼版本。您可以在[這裡](http://www.w3schools.com/tags/ref_urlencode.asp)對其進行 URL 編碼。

		# servicebus.properties - sample JNDI configuration

		# Register a ConnectionFactory in JNDI using the form:
		# connectionfactory.[jndi_name] = [ConnectionURL]
		connectionfactory.SBCF = amqps://SendRule:{Send Rule key}@{namespace name}.servicebus.windows.net/?sync-publish=false

		# Register some queues in JNDI using the form
		# queue.[jndi_name] = [physical_name]
		# topic.[jndi_name] = [physical_name]
		queue.EventHub = {event hub name}

5. 建立名為 **Sender** 的新類別。新增下列 'import' 陳述式：

		import java.io.BufferedReader;
		import java.io.IOException;
		import java.io.InputStreamReader;
		import java.io.UnsupportedEncodingException;
		import java.util.Hashtable;
		
		import javax.jms.BytesMessage;
		import javax.jms.Connection;
		import javax.jms.ConnectionFactory;
		import javax.jms.Destination;
		import javax.jms.JMSException;
		import javax.jms.MessageProducer;
		import javax.jms.Session;
		import javax.naming.Context;
		import javax.naming.InitialContext;
		import javax.naming.NamingException; 

8. 然後，在其中新增下列程式碼：

		public static void main(String[] args) throws NamingException,
				JMSException, IOException, InterruptedException {
			// Configure JNDI environment
			Hashtable<String, String> env = new Hashtable<String, String>();
			env.put(Context.INITIAL_CONTEXT_FACTORY,
					"org.apache.qpid.amqp_1_0.jms.jndi.PropertiesFileInitialContextFactory");
			env.put(Context.PROVIDER_URL, "servicebus.properties");
			Context context = new InitialContext(env);
	
			ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
	
			Destination queue = (Destination) context.lookup("EventHub");
	
			// Create Connection
			Connection connection = cf.createConnection();
	
			// Create sender-side Session and MessageProducer
			Session sendSession = connection.createSession(false,
					Session.AUTO_ACKNOWLEDGE);
			MessageProducer sender = sendSession.createProducer(queue);
	
			System.out.println("Press Ctrl-C to stop the sender process");
			System.out.println("Press Enter to start now");
			BufferedReader commandLine = new java.io.BufferedReader(
					new InputStreamReader(System.in));
			commandLine.readLine();
	
			while (true) {
				sendBytesMessage(sendSession, sender);
				Thread.sleep(200);
			}
		}
		
		private static void sendBytesMessage(Session sendSession, MessageProducer sender) throws JMSException, UnsupportedEncodingException {
	        BytesMessage message = sendSession.createBytesMessage();
	        message.writeBytes("Test AMQP message from JMS".getBytes("UTF-8"));
	        sender.send(message);
	        System.out.println("Sent message");
	    }



<!-- Links -->
[Azure 管理入口網站]: https://manage.windowsazure.com/


<!-- Images -->
[8]: ./media/service-bus-event-hubs-getstarted/create-sender-java1.png
