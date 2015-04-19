<properties
   pageTitle="搭配使用事件中心與 Storm on HDInsight 來處理事件 | Azure"
   description="了解如何使用 HDInsight Tools for Visual Studio，來處理包含 Visual Studio 中所建立 C# Storm 拓樸的事件中心資料。"
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="03/03/2015"
   ms.author="larryfr"/>

#搭配使用 Azure 事件中心與 Storm on HDInsight 來處理事件 (C#)

Azure 事件中心可讓您從網站、應用程式和裝置處理巨量資料。事件中心 Spout 可讓您輕鬆地使用 Apache Storm on HDInsight 進而即時分析資料。您也可以使用事件中心 Bolt 將資料從 Storm 寫入事件中心。 

在本文中，您將學習如何使用 HDInsight Tools for Visual Studio 及事件中心 Spout 和 Bolt 來建立兩個混合式 C#/Java 拓撲：

* **EventHubWriter** - 隨機產生資料，並將資料寫入事件中心

* **EventHubReader** - 從事件中心讀取資料，並將資料儲存在 Azure 資料表儲存體

##必要條件

* <a href="../hdinsight-storm-getting-started/" target="_blank">Apache Storm on HDInsight 叢集</a>

* <a href="../service-bus-event-hubs-csharp-ephcs-getstarted/" target="_blank">Azure 事件中心</a>

* <a href="http://azure.microsoft.com/downloads/" target="_blank">Azure .NET SDK</a>

* <a href="../hdinsight-hadoop-visual-studio-tools-get-started/" target="_blank">HDInsight Tools for Visual Studio</a> - 2/17/2015 版本或更新版本

##已完成的專案

您可以下載本文中所建立的專案完整版本，下載位置是 [https://github.com/Blackmist/eventhub-storm-hybrid](https://github.com/Blackmist/eventhub-storm-hybrid)；不過，您仍然必須依照本文件中的步驟提供組態設定。

> [AZURE.NOTE] 使用已完成的專案時，您必須使用 [**NuGet 封裝管理員**] 來還原此方案所需的封裝。

##事件中心 Spout 和 Bolt

事件中心 Spout 和 Bolt 是可讓您輕鬆地從 Apache Storm 使用事件中心的 Java 元件。雖然這些元件是採用 Java 撰寫而成的，但 HDInsight Tools for Visual Studio 可讓您建立混合 C# 和 Java 元件的混合式拓撲。

Spout 和 Bolt 會以名為**eventhubs-storm-spout-0.9-jar-with-dependencies.jar** 的單一 Java 封存 (.jar) 檔案形式散發。

###下載 Jar

**lib** 資料夾底下的 <a href="https://github.com/hdinsight/hdinsight-storm-examples" target="_blank">https://github.com/hdinsight/hdinsight-storm-examples</a> 專案中會包含最新版本的 **eventhubs-storm-spout-0.9-jar-with-dependencies.jar** 檔案。若要下載檔案，請使用下列其中一種方法。

> [AZURE.NOTE] 已提交 Spout 和 Bolt，以將其納入 Apache Storm 專案中。如需詳細資訊，請參閱 [<a href="https://github.com/apache/storm/pull/336/files">提取要求</a>]。

* **下載 ZIP 檔案** - 在 <a href="https://github.com/hdinsight/hdinsight-storm-examples" target="_blank">https://github.com/hdinsight/hdinsight-storm-examples</a> 網站中，選取 [**下載 ZIP**] 按鈕來下載包含專案的 .zip 檔案。

	![download zip button](./media/hdinsight-storm-develop-csharp-event-hub-topology/download.png)

	下載後，您可以將此封存解壓縮，檔案便會出現在 **lib** 目錄中。

* **複製專案** - 如果您已安裝 <a href="http://git-scm.com/" target="_blank">Git</a>，請使用下列命令來本機複製儲存機制，然後在 **lib** 目錄中尋找檔案。

		git clone https://github.com/hdinsight/hdinsight-storm-examples

##設定事件中心

事件中心是此範例的資料中心。請使用下列步驟建立新的事件中心。

1. 在 [Azure 入口網站](https://manage.windowsazure.com)中，依序選取 [**新增 | 服務匯流排 | 事件中心 | 自訂建立**]。

2. 在 [**新增事件中心**] 對話方塊中輸入 [**事件中心名稱**]，選取要建立中心的 [**區域**]，然後建立新的命名空間或選取現有的命名空間。最後，按一下 [**箭頭**]。

	![wizard page 1](./media/hdinsight-storm-develop-csharp-event-hub-topology/wiz1.png)

	> [AZURE.NOTE] 您應該選取相同的 [**位置**] 做為 Storm on HDInsight 伺服器，以便降低延遲和成本。

2. 在 [**設定事件中心**] 對話方塊中，輸入 [**資料分割計數**] 和 [**訊息保留**] 值。在此範例中，資料分割計數使用 10，訊息保留使用 1。請記下資料分割計數，因為您稍後可能會用到這個值。

	![wizard page 2](./media/hdinsight-storm-develop-csharp-event-hub-topology/wiz2.png)

3. 建立事件中心之後，請選取命名空間，然後選取 [**事件中心**]。最後，選取您稍早建立的事件中心。

4. 選取 [**設定**]，然後使用下列資訊建立兩個新的存取原則。

	<table>
	<tr><th>名稱</th><th>權限</th></tr>
	<tr><td>寫入器</td><td>傳送</td></tr>
	<tr><td>讀取器</td><td>接聽</td></tr>
	</table>

	建立權限之後，選取頁面底部的 [**儲存**] 圖示。這樣會建立共用存取原則，可用來傳送 (寫入器) 和接聽 (讀取器) 此事件中心的訊息。

	![policies](./media/hdinsight-storm-develop-csharp-event-hub-topology/policy.png)

5. 儲存原則之後，使用頁面底部的 [**共用存取金鑰產生器**]，來擷取 [**寫入器**] 和 [**讀取器**] 原則的金鑰。儲存這些金鑰，稍後會用到。

##設定資料表儲存體

因為您可以透過 [**伺服器總管**]，輕鬆地從 Visual Studio 內部檢視資料表儲存體，所以會使用資料表儲存體來保存從事件中心讀取的值。請使用下列步驟建立新的資料表儲存體。

1. 在 [Azure 入口網站](https://manage.windowsazure.com)中，依序選取 [**新增 | 資料服務 | 儲存體 | 快速建立**]。

	![quick create storage](./media/hdinsight-storm-develop-csharp-event-hub-topology/storagecreate.png)

2. 在 [儲存體帳戶] 中輸入**名稱**，依序選取 [**位置**] 和 [**核取記號**] 以建立儲存體帳戶。

	> [AZURE.NOTE] 您應該選取相同的 [**位置**] 做為您的事件中心和 Storm on HDInsight 伺服器，以便降低延遲和成本。

3. 佈建新的儲存體帳戶之後，請選取此帳戶，然後使用頁面底部的 [**管理存取金鑰**] 連結，來擷取 [**儲存體帳戶名稱**] 和 [**主要存取金鑰**]。儲存此資訊，稍後會用到。

	![access keys](./media/hdinsight-storm-develop-csharp-event-hub-topology/managekeys.png)

##建立 EventHubWriter

在本節中，您將建立使用事件中心 Bolt 將資料寫入事件中心的拓樸。

1. 如果您尚未安裝最新版本的 HDInsight Tools for Visual Studio，請參閱 [<a href="../hdinsight-hadoop-visual-studio-tools-get-started/" target="_blank">開始使用 HDInsight Tools for Visual Studio</a>]。

2. 開啟 Visual Studio，並依序選取 [**檔案**]、[**新增**] 和 [**專案**]。

3. 從 [**新增專案**] 對話方塊中，依序展開 [**已安裝**] 和 [**範本**]，然後選取 [**HDInsight**]。從範本清單中，選取 [**Storm 應用程式**]。在對話方塊底部，輸入 **EventHubWriter** 做為應用程式名稱。

	![image](./media/hdinsight-storm-develop-csharp-event-hub-topology/newproject.png)

4. 建立專案之後，您應該會有下列檔案：

	* **Program.cs** - 這會定義您專案的拓撲。請注意，預設會建立含有一個 Spout 和一個 Bolt 的預設拓撲

	* **Spout.cs** - spout 範例

	* **Bolt.cs** - bolt 範例。因為您將會使用事件中心 Bolt 寫入事件中心，所以將會刪除此項目。

###組態

1. 在 [**方案總管**] 中，以滑鼠右鍵按一下 **EventHubWriter**，然後選取 [**屬性**]。

2. 在專案屬性中，依序選取 [**設定**] 和 [**這個專案未包含預設的設定檔。請按這裡建立設定檔。**]

3. 輸入下列設定。使用您稍早在 [**值**] 資料行中所建立的事件中心資訊。

	<table>
	<tr><th style="text-align:left">名稱</th><th style="text-align:left">類型</th><th style="text-align:left">Scope</th></tr>
	<tr><td style="text-align:left">EventHubPolicyName</td><td style="text-align:left">字串</td><td style="text-align:left">應用程式</td></tr>
	<tr><td style="text-align:left">EventHubPolicyKey</td><td style="text-align:left">字串</td><td style="text-align:left">應用程式</td></tr>
	<tr><td style="text-align:left">EventHubNamespace</td><td style="text-align:left">字串</td><td style="text-align:left">應用程式</td></tr>
	<tr><td style="text-align:left">EventHubName</td><td style="text-align:left">字串</td><td style="text-align:left">應用程式</td></tr>
	<tr><td style="text-align:left">EventHubPartitionCount</td><td style="text-align:left">int</td><td style="text-align:left">應用程式</td></tr>
	</table>

4. 儲存並關閉 [屬性] 頁面。

###定義拓撲

1. 在 [**方案總管**] 中，以滑鼠右鍵按一下 **Bolt.cs**，然後選取 [**刪除**]。由於您打算使用 Java 事件中心 Bolt，您將不會用到此檔案。

2. 開啟 **Program.cs** 檔案，並在 `TopologyBuilder topologyBuilder = new TopologyBuilder("EventHubWriter");` 行後面緊接著加入下列內容。

		int partitionCount = Properties.Settings.Default.EventHubPartitionCount;
		List<string> javaDeserializerInfo =
            new List<string>() { "microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer", "java.lang.String" };

	第一行會從稍早定義的屬性中讀取資料分割計數。第二行會定義一個還原序列化程式，可用來將 Spout 所產生的 JSON 資料還原序列化成  `java.lang.String`，以便事件中心 Bolt 可以取用該資料。

4. 尋找下列程式碼。

		topologyBuilder.SetSpout(
            "Spout",
            Spout.Get,
            new Dictionary<string, List<string>>()
            {
                {Constants.DEFAULT_STREAM_ID, new List<string>(){"count"}}
            },
            1);

	Replace it with the following.

		topologyBuilder.SetSpout(
            "Spout",
            Spout.Get,
            new Dictionary<string, List<string>>()
            {
                {Constants.DEFAULT_STREAM_ID, new List<string>(){"Event"}}
            },
            partitionCount).
            DeclareCustomizedJavaDeserializer(javaDeserializerInfo);

	這會建立 Spout，並使用事件中心資料分割計數做為此元件的平行處理原則提示。這應該會為每個資料分割建立 Spout 的執行個體。
	
	這也宣告輸出資料流應該使用稍早建立的還原序列化程式。

5. 在先前的程式碼後面緊接著加入下列程式碼。

		JavaComponentConstructor constructor =
            JavaComponentConstructor.CreateFromClojureExpr(
            String.Format(@"(com.microsoft.eventhubs.bolt.EventHubBolt. (com.microsoft.eventhubs.bolt.EventHubBoltConfig. " +
            @"""{0}"" ""{1}"" ""{2}"" ""{3}"" ""{4}"" {5}))",
            Properties.Settings.Default.EventHubPolicyName,
            Properties.Settings.Default.EventHubPolicyKey,
            Properties.Settings.Default.EventHubNamespace,
            "servicebus.windows.net", //suffix for servicebus fqdn
            Properties.Settings.Default.EventHubName, 
			"true"));

	這會為 Java Bolt 建立新的建構函式，在執行階段時可用來設定 Bolt 的新執行個體。在此案例中，您會使用稍早新增的事件中心組態資訊，透過 <a href="http://storm.apache.org/documentation/Clojure-DSL.html" target="_blank">Apache Storm Clojure DSL</a> 來設定 Spout。更具體來說，HDInsight 會在執行階段使用此程式碼執行下列動作：

	* 使用您所提供的事件中心資訊，建立新的執行個體 **com.microsoft.eventhubs.bolt.EventHubBoltConfig**。
	* 建立新的執行個體  **com.microsoft.eventhubs.bolt.EventHubBolt**，在 **EventHubBoltConfig** 執行個體中進行傳遞。

6. 尋找下列程式碼。

		topologyBuilder.SetBolt(
            "Bolt",
            Bolt.Get,
            new Dictionary<string, List<string>>(),
            1).shuffleGrouping("Spout");

	Replace it with the following.

		topologyBuilder.SetJavaBolt(
            "EventHubBolt",
            constructor,
            partitionCount).
            shuffleGrouping("Spout");

	這會指示拓樸使用先前步驟的 **JavaComponentConstructor** 作為 Bolt。在此拓撲中，可將此元件稱為 "EventHubBolt" 的易記名稱。將平行處理原則提示設定為事件中心的資料分割數目，並訂閱 Spout ("Spout") 所產生的資料。

此時，您已完成使用 **Program.cs**。拓撲已定義完成，但現在您必須修改 **Spout.cs**，以便所產生的資料格式會是事件中心 Bolt 可以使用的格式。

###修改 Spout

事件中心 Bolt 預期會收到路由傳送至事件中心的單一字串值。在下列範例中，您將修改預設的 **Spout.cs** 檔案以產生 JSON 字串。

1. 在 [**方案總管**] 中，以滑鼠右鍵按一下 **EventHubWriter** 專案，然後選取 [**管理 NuGet 封裝**]。搜尋 **Json.Net** 封裝，然後將它加入方案。我們便可以輕鬆地建立透過 Bolt 傳送到事件中心的 JSON 資料。

1. 開啟 **Spout.cs**，並在檔案頂端加入下列內容。

		using Newtonsoft.Json;
		using Newtonsoft.Json.Linq;

	我們便可以更輕鬆地使用 JSON 資料。

3. 尋找下列程式碼。

		Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        outputSchema.Add("default", new List<Type>() { typeof(int) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(null, outputSchema));

	使用下列內容將其取代。

		Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        outputSchema.Add("default", new List<Type>() { typeof(string) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(null, outputSchema));
        this.ctx.DeclareCustomizedSerializer(new CustomizedInteropJSONSerializer());

	這會將 Spout 所建立的資料定義變更為使用**字串**資料和**自訂 JSON 序列化程式**。

2. 使用下列內容取代 **NextTuple** 方法。

		public void NextTuple(Dictionary<string, Object> parms)
        {
            JObject eventData = new JObject();
            eventData.Add("deviceId", r.Next(10));
            eventData.Add("deviceValue", r.Next());
            ctx.Emit(new Values(eventData.ToString(Formatting.None)));
        }

	這將會隨機產生裝置識別碼和一個值，然後透過 Json.net 發出使用這些值的 JSON 物件。

3. 儲存 **Spout.cs** 檔案。

此時，您會有一個將產生隨機資料，並使用事件中心 Bolt 將資料儲存到事件中心的基本拓撲。接下來，您將建立讀取器。

##建立 EventHubReader

在本節中，您將建立使用事件中心 Spout 從事件中心讀取資料的拓樸。

2. 開啟 Visual Studio，並依序選取 [**檔案**]、[**新增**] 和 [**專案**]。

3. 從 [**新增專案**] 對話方塊中，依序展開 [**已安裝**] 和 [**範本**]，然後選取 [**HDInsight**]。從範本清單中，選取 [**Storm 應用程式**]。在對話方塊底部，輸入 **EventHubReader** 做為應用程式名稱。

###組態

1. 在 [**方案總管**] 中，以滑鼠右鍵按一下 **EventHubReader**，然後選取 [**屬性**]。

2. 在專案屬性中，依序選取 [**設定**] 和 [**這個專案未包含預設的設定檔。請按這裡建立設定檔。**]

3. 輸入下列設定。使用您稍早在 [**值**] 資料行中所建立的事件中心和儲存體帳戶資訊。

	<table>
	<tr><th style="text-align:left">名稱</th><th style="text-align:left">類型</th><th style="text-align:left">Scope</th></tr>
	<tr><th style="text-align:left">EventHubPolicyName</th><th style="text-align:left">字串</th><th style="text-align:left">應用程式</th></tr>
	<tr><th style="text-align:left">EventHubPolicyKey</th><th style="text-align:left">字串</th><th style="text-align:left">應用程式</th></tr>
	<tr><th style="text-align:left">EventHubNamespace</th><th style="text-align:left">字串</th><th style="text-align:left">應用程式</th></tr>
	<tr><th style="text-align:left">EventHubName</th><th style="text-align:left">字串</th><th style="text-align:left">應用程式</th></tr>
	<tr><th style="text-align:left">EventHubPartitionCount</th><th style="text-align:left">int</th><th style="text-align:left">應用程式</th></tr>
	<tr><th style="text-align:left">StorageConnection</th><th style="text-align:left">(連接字串)</th><th style="text-align:left">應用程式</th></tr>
	<tr><th style="text-align:left">TableName</th><th style="text-align:left">字串</th><th style="text-align:left">應用程式</th></tr>
	</table>

	在 **TableName** 中，輸入您想要儲存事件的資料表名稱。

	拓撲就會使用這些值，與事件中心和資料表儲存體進行通訊。

4. 儲存並關閉 [屬性] 頁面。

###定義拓撲

1. 在 [**方案總管**] 中，以滑鼠右鍵按一下 **Spout.cs**，然後選取 [**刪除**]。由於您打算使用 Java 事件中心 Spout，您將不會用到此檔案。

2. 開啟 **Program.cs** 檔案，並在 `TopologyBuilder topologyBuilder = new TopologyBuilder("EventHubReader");` 行後面緊接著加入下列內容。

		int partitionCount = Properties.Settings.Default.EventHubPartitionCount;
		JavaComponentConstructor constructor = JavaComponentConstructor.CreateFromClojureExpr(
            String.Format(@"(com.microsoft.eventhubs.spout.EventHubSpout. (com.microsoft.eventhubs.spout.EventHubSpoutConfig. " +
                @"""{0}"" ""{1}"" ""{2}"" ""{3}"" {4} ""{5}""))",
                Properties.Settings.Default.EventHubPolicyName,
                Properties.Settings.Default.EventHubPolicyKey,
                Properties.Settings.Default.EventHubNamespace,
                Properties.Settings.Default.EventHubName,
                partitionCount,
                "")); //Last value is the zookeeper connection string - leave empty

	因為會重複使用資料分割計數，所以系統會讀入資料分割計數並指派給本機變數。

	 `JavaComponentConstructor` 會定義如何在執行階段建構 Java Spout。在此案例中，您會使用稍早新增的事件中心組態資訊，透過 <a href="http://storm.apache.org/documentation/Clojure-DSL.html" target="_blank">Apache Storm Clojure DSL</a> 來設定 Spout。更具體來說，HDInsight 會在執行階段使用此程式碼執行下列動作：

	* 使用您所提供的事件中心資訊，建立新的執行個體 **com.microsoft.eventhubs.spout.EventHubSpoutConfig**。
	
	* 建立新的執行個體  **com.microsoft.eventhubs.spout.EventHubSpout**，在 **EventHubSpoutConfig** 執行個體中進行傳遞。

5. 尋找下列程式碼。

		topologyBuilder.SetSpout(
            "Spout",
            Spout.Get,
            new Dictionary<string, List<string>>()
            {
                {Constants.DEFAULT_STREAM_ID, new List<string>(){"count"}}
            },
            1);

	Replace it with the following.

        topologyBuilder.SetJavaSpout(
            "EventHubSpout",
            constructor,
            partitionCount);

	這會指示拓樸使用先前步驟的 **JavaComponentConstructor** 作為 Bolt，並將其命名為 "EventHubSpout"。這也可以將此元件的平行處理原則提示設定為事件中心的資料分割數目。 

2. 在先前的程式碼後面緊接著加入下列內容。

         List<string> javaSerializerInfo = new List<string>() { "microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer" };

	這會建立一個自訂的序列化程式，可用來將 Java Spout 所產生的資料序列化為 JSON 格式。

3. 尋找下列程式碼。

		topologyBuilder.SetBolt(
            "Bolt",
            Bolt.Get,
            new Dictionary<string, List<string>>(),
            1).shuffleGrouping("Spout");

	並使用下列內容將其取代。

		topologyBuilder.SetBolt(
            "Bolt",
            Bolt.Get,
            new Dictionary<string, List<string>>(),
            partitionCount).
            DeclareCustomizedJavaSerializer(javaSerializerInfo).
            shuffleGrouping("EventHubSpout");

	此程式碼會指示拓撲使用 Bolt (於 Bolt.cs 中定義)。新增項目會指示拓樸針對此元件 (來自 **EventHubSpout** - Java Spout) 所取用的資料，使用自訂的序列化程式。

此時，您已完成使用 **Program.cs**。拓撲已定義完成，但您現在必須建立協助程式類別，將資料寫入資料表儲存體，然後您必須修改 **Bolt.cs**，讓它可以了解 Spout 所產生的資料。

###建立協助程式類別

將資料寫入資料表儲存體時，您必須建立類別來說明要寫入的資料。

1. 在 [**方案總管**] 中，以滑鼠右鍵按一下 **EventHubReader** 專案，然後依序選取 [**新增**]、[**新增類別**]。將新類別命名為 **Devices.cs**。

2. 開啟 **Devices.cs** 並以下列內容取代預設的程式碼。

		using System;
		using System.Collections.Generic;
		using System.Linq;
		using System.Text;
		using System.Threading.Tasks;
		using Microsoft.WindowsAzure.Storage.Table;
		
		namespace EventHubReader
		{
		    class Device : TableEntity
		    {
		        public int value { get; set; }
		
		        public Device() { }
		        public Device(int id)
		        {
		            this.PartitionKey = id.ToString();
		            this.RowKey = System.Guid.NewGuid().ToString();
		        }
		    }
		}

	這會在資料表儲存體中建立實體，實體包含資料分割索引鍵 (這將會設定為讀取自事件中心的裝置識別碼)、唯一資料列索引鍵，和一個讀取自事件中心的值。每個實體也會有時間戳記，它會在實體插入資料表時自動建立。

###修改 Bolt

1. 在 [**方案總管**] 中，以滑鼠右鍵按一下 **EventHubReader** 專案，然後選取 [**管理 NuGet 封裝**]。搜尋 **Json.Net** 封裝，然後將它加入方案。我們便可以輕鬆地處理從 Spout 收到的 JSON 資料。並加入 **Windows Azure 儲存體**封裝，以方便我們寫入資料表儲存體。

1. 開啟 **Bolt.cs**，並在檔案頂端加入下列內容。

		using Newtonsoft.Json.Linq;
		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Table;

	我們便可以更輕鬆地使用來自 Bolt 的 JSON 資料，並將資料寫入資料表儲存體。

2. 尋找 `private int count;` 陳述式，並使用下列內容將其取代。

        private CloudTable table;

	您會在連接至資料表時用到此內容。

4. 尋找下列程式碼。

		Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
        inputSchema.Add("default", new List<Type>() { typeof(int) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, null));

	使用下列內容將其取代。

		Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
        inputSchema.Add("default", new List<Type>() { typeof(string) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, null));
        this.ctx.DeclareCustomizedDeserializer(new CustomizedInteropJSONDeserializer());

	這會指示 Bolt 它將會收到**字串**值 (而不是 **int**)，且應使用 **CustomizedInteropJSONDeserialzer** 來還原序列化資料。

3. 在先前的程式碼後面緊接著加入下列程式碼。

		CloudStorageAccount storageAccount = CloudStorageAccount.Parse(Properties.Settings.Default.StorageConnection);
        CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
        table = tableClient.GetTableReference(Properties.Settings.Default.TableName);
        table.CreateIfNotExists();

	這將會使用先前設定的連接字串來連接到 **events** 資料表。建立資料表 (如果不存在)。

2. 尋找 **Execute** 方法，並使用下列內容將其取代。

		public void Execute(SCPTuple tuple)
        {
            Context.Logger.Info("Processing events");
            string eventValue = (string)tuple.GetValue(0);
            if (eventValue != null)
            {
                JObject eventData = JObject.Parse(eventValue);

                Device device = new Device((int)eventData["deviceId"]);
                device.value = (int)eventData["deviceValue"];

                TableOperation insertOperation = TableOperation.Insert(device);

                table.Execute(insertOperation);
            }
        }

	這會使用 Json.net 來解析來自 Spout 的 JSON 資料，然後挑選出 **deviceId** 和 **deviceValue** 欄位。接著會建立新的**裝置**物件，在初始設定過程中使用 **deviceId** 設定資料表的資料分割索引鍵。此值即會被設為 **deviceValue**，最後再將實體插入資料表。

此時，您便會有一個完整拓撲，此拓撲會從事件中心讀取資料，並將資料儲存在資料表儲存體內一個名為 **events** 的資料表中。

##部署拓撲

1. 開啟 **EventHubReader** 方案。在 [**方案總管**] 中，以滑鼠右鍵按一下 **EventHubReader** 專案，然後選取 [**提交到 Storm on HDInsight**]。

	![submit to storm](./media/hdinsight-storm-develop-csharp-event-hub-topology/submittostorm.png)

2. 在 [**提交拓樸**] 對話方塊中，選取您的 [**Storm 叢集**]。展開 [**其他組態**]，並依序選取 [**Java 檔案路徑**]、[**...**]，然後選取包含稍早下載之 **eventhubs-storm-spout-0.9-jar-with-dependencies.jar** 的目錄。最後，選取 [**提交**]。

	![Image of submission dialog](./media/hdinsight-storm-develop-csharp-event-hub-topology/submit.png)

3. 提交拓撲之後，[**Storm 拓撲檢視器**] 便會隨即出現。選取對話方塊左邊的 **EventHubReader** 拓樸，並檢視拓撲的統計資料 。目前，因為事件中心尚未有事件寫入，所以應該什麼都不會發生。

	![example storage view](./media/hdinsight-storm-develop-csharp-event-hub-topology/topologyviewer.png)

4. 開啟 **EventHubWriter** 方案。在 [**方案總管**] 中，以滑鼠右鍵按一下 **EventHubWriter** 專案，然後選取 [**提交到 Storm on HDInsight**]。

2. 在 [**提交拓樸**] 對話方塊中，選取您的 [**Storm 叢集**]。展開 [**其他組態**]，並依序選取 [**Java 檔案路徑**]、[**...**]，然後選取包含稍早下載之 **eventhubs-storm-spout-0.9-jar-with-dependencies.jar** 的目錄。最後，選取 [**提交**]。

5. 提交拓撲之後，請重新整理 [**Storm 拓撲檢視器**] 中的拓撲清單，以確認兩者皆在叢集上執行。

6. 兩者都開始執行後，請選取 [**伺服器總管**]，然後依序展開 [**Azure**]、[**儲存體**]，最後再展開您稍早建立的儲存體帳戶。展開儲存體帳戶底下的 [**資料表**]。最後，按兩下 **events** 資料表以開啟資料表。您應該會看到來自 **EventHubReader** 拓樸並已儲存在資料表中的資料。

	* **EventHubWriter** 拓樸會產生事件，並將這些事件寫入事件中心。

	* **EventHubReader** 會接著從事件中心讀取事件，並將事件儲存到資料表儲存體的 **events** 資料表中。

##停止拓撲

若要停止拓撲，請在 [**Storm 拓撲檢視器**] 中選取每個拓撲，然後選取 [**刪除**]。

![image of killing a topology](./media/hdinsight-storm-develop-csharp-event-hub-topology/killtopology.png)

##摘要

在本文件中，您已經了解如何使用 Java 事件中心 Spout 和 Bolt，利用 C# 拓撲來使用 Azure 事件中心的資料。若要深入了解如何建立 C# 拓撲，請參閱下列內容。

* [使用 Visual Studio 開發 Apache Storm on HDInsight 的 C# 拓撲](hdinsight-storm-develop-csharp-visual-studio-topology.md)

* [HDInsight Storm 範例](https://github.com/hdinsight/hdinsight-storm-examples)








<!--HONumber=47-->
