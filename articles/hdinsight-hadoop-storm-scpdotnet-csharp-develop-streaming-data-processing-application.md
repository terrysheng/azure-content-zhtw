<properties title="Develop streaming data processing applications with SCP.NET and C# on Storm in HDInsight" pageTitle="在 Storm 上使用 SCP.NET 開發串流資料處理應用程式 | Azure" description="了解如何在 HDInsight 中的 Storm 上使用 SCP.NET 和 C# 開發串流資料處理應用程式。" services="hdinsight" solutions="" documentationCenter="" authors="Qianlin Xia" videoId="" scriptId="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/15/2014" ms.author="qixia" />

#在 HDInsight 中使用 C# 搭配 Stream Computing Platform 和 Storm 開發串流資料處理應用程式

Stream Computing Platform (SCP) 是一個平台，可使用 .NET 建置即時、可靠、分散式和高效能的資料處理應用程式。它的建置基礎為 [Apache Storm](http://storm.incubator.apache.org/) -- 一種隨 HDInsight 提供的開放原始碼、即時串流處理系統。

在本文中，您將了解：

* 何謂 SCP 以及它如何與 Storm 搭配運作

* 如何建立 SCP 方案

* 如何測試 SCP 方案

* 如何部署 SCP 方案至 HDInsight Storm 叢集

##必要條件

* Azure 訂用帳戶

* HDInsight Storm 叢集

* Visual Studio 2010 或 2013

##目錄

* [SCP 和 Storm](#scpandstorm)

##<a id="scpandstorm"></a>SCP 和 Storm

Apache Storm 是分散式運算系統，在 Hadoop 叢集上執行，並可讓您執行即時資料處理。雖然 Storm 是在 Java Virtual Machine (JVM) 中執行，其設計是要讓方案 (即**拓撲**) 可在各種程式設計語言中實作。您甚至可以建立混合了以多個語言編寫之元件的拓撲。

SCP 提供的程式庫可讓您輕鬆使用 .NET 建立 Storm 方案。Azure HDInsight Storm 叢集包括執行您建立的 SCP 方案所需的必要伺服器端元件。

如需 HDInsight Storm 的詳細資訊，請參閱 [HDInsight Storm 概觀](http://azure.microsoft.com/zh-tw/documentation/articles/hdinsight-storm-overview/)。

###設計 SCP 方案

SCP 提供的介面可讓您建立下列 Storm 元件：

* Spout - 從來源 (檔案、資料庫、API 等) 取用資料，並發出一或多個 Tuple (元素的排序清單) 串流
* Bolt - 取用一或多個串流，並選擇性地發出一或多個串流
* 拓撲 - 定義 Spout 和 Bolt 之間的資料流程、Spout 和 Bolt 的平行處理原則，以及組態資訊

> [AZURE.NOTE] Spout 和 Bolt 也會實作一般處理。例如，Spout 可能會將傳入訊息分成多個 Tuple，或僅發出一個 Tuple，並讓 Bolt 擷取它所需的值。類似地，如果您需要將資料寫入資料存放區，您會在 Bolt 中加以實作。

設計方案牽涉到下列：

* 您將取用的資料來源為何？您必須有可實作此動作的 Spout。
* 必須發生什麼處理？您必須在 Spout 和/或 Bolt 中實作此動作。
* 如果處理分散在多個 Bolt 中，它們之間的流程為何？您必須透過拓撲來加以描述。
* 處理如何在 HDInsight 叢集中的節點中散發？您必須透過拓撲來加以描述。

##安裝 SCP SDK

SCP SDK 是在 HDInsight Storm 叢集上提供。[建立 HDInsight Storm 叢集]之後(http://azure.microsoft.com/zh-tw/documentation/articles/hdinsight-storm-getting-started/)，使用下列步驟，將 SDK 下載至您的本機開發環境。

1. 登入 [Azure 管理入口網站](https://manage.windowsazure.com)。

2. 按一下左窗格上的 [**HDInsight**]。您將會看到已部署的 HDInsight 叢集清單。

3. 按一下想要連線的 HDInsight 叢集。

4. 按一下頁面頂端的 [**組態**]。

5. 按一下頁面底部的 [**啟用遠端**]。

6. 在 [設定遠端桌面] 精靈中，輸入遠端桌面的**使用者名稱**和**密碼**。請在 [**到期日**] 方塊中輸入到期日，然後按一下核取圖示。

7. 遠端桌面啟用之後，在頁面底部按一下 [**連線**]，然後遵循指示。

8. 透過遠端桌面連線至叢集之後，開啟 [**檔案總管**] 然後在網址列輸入 **%storm_home%\examples**。

9. 以滑鼠右鍵按一下 **SDK** 資料夾，然後選取 [**複製**]。

10. 在您的本機開發環境上，開啟 [**檔案總管**] 並瀏覽至您要儲存 SDK 的位置。按一下滑鼠右鍵，然後選取 [**貼上**]。

##建立 SCP 方案

1. 在 Visual Studio 中，為**主控台應用程式**建立新專案。將方案命名為 **WordCount**。

2. 在 [**方案總管**] 中，於 [**參考**] 上按一下滑鼠右鍵，然後按一下 [**加入參考**]。

3. 選取 [**參考管理員**] 頁面底端的 [**瀏覽**] 按鈕，然後瀏覽至您稍早下載的 SDK 資料夾。選取 **Microsoft.SCP.dll** 和 **Microsoft.SCPLogger.dll**，然後按一下 [**加入**]。按一下 [**確定**] 以關閉參考管理員視窗。

###建立 Spout

1. 在 [**方案總管**] 中，於 [**WordCount**] 上按一下滑鼠右鍵，然後按一下 [**加入 | 新增項目**]。選取 [**類別**] 並輸入 **WordSpout.cs** 做為名稱。最後按一下 [**確定**]。

2. 開啟 **WordSpout.cs** 檔案，以下列內容取代現有的程式碼。務必閱讀註解來了解程式碼的作用。

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Text;
        using System.Threading.Tasks;
        using Microsoft.SCP;
        using System.Threading;

        namespace WordCount
        {
            class WordSpout : ISCPSpout
            {
                //Context
                private Context ctx;

                private Random rand = new Random();
                //The sentences to emit
                string[] sentences = new string[] {
                                                  "the cow jumped over the moon",
                                                  "an apple a day keeps the doctor away",
                                                  "four score and seven years ago",
                                                  "snow white and the seven dwarfs",
                                                  "i am at two with nature"};

                //Constructor
                public WordSpout(Context ctx)
                {
                    //Log that we are starting
                    Context.Logger.Info("WordSpout constructor called");
                    //Store the context that was passed
                    this.ctx = ctx;

                    //Define the schema for the emitted tuples
                    Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
                    //In this case, just a string tuple
                    outputSchema.Add("default", new List<Type>() { typeof(string) });
                    //Declare the schema for the stream
                    this.ctx.DeclareComponentSchema(new ComponentStreamSchema(null, outputSchema));
                }

                //Return a new instance of the spout
                public static WordSpout Get(Context ctx, Dictionary<string, Object> parms)
                {
                    return new WordSpout(ctx);
                }

                //Emit the next tuple
                //NOTE: When using data from an external data source
                //such as Service Bus, Event Hub, Twitter, etc.,
                //you would read and emit it in NextTuple
                public void NextTuple(Dictionary<string, object> parms)
                {
                    Context.Logger.Info("NextTuple enter");

                    //Get a random sentence
                    string sentence = sentences[rand.Next(0, sentences.Length - 1)];
                    Context.Logger.Info("Emit: {0}", sentence);
                    //Emit the sentence
                    this.ctx.Emit(new Values(sentence));

                    Context.Logger.Info("NextTuple exit");
                }

                //Ack's are not implemented
                public void Ack(long seqId, Dictionary<string, object> parms)
                {
                    throw new NotImplementedException();
                }

                //Ack's are not implemented, so
                //fail should never be called
                public void Fail(long seqId, Dictionary<string, object> parms)
                {
                    throw new NotImplementedException();
                }
            }
        }

###建立測試

1. 在 [**方案總管**] 中，於 [**WordCount**] 上按一下滑鼠右鍵，然後按一下 [**加入 | 新增項目**]。選取 [**類別**] 並輸入 **LocalTest.cs** 做為名稱。最後按一下 [**確定**]。

2. 開啟 **LocalTest.cs** 檔案，以下列內容取代現有的程式碼。用來測試 Spout 的程式碼與用來測試 Bolt 的程式碼非常類似。

        using Microsoft.SCP;
        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Text;
        using System.Threading.Tasks;

        namespace WordCount
        {
            class LocalTest
            {
                //Run tests
                public void RunTestCase()
                {
                    Dictionary<string, Object> emptyDictionary = new Dictionary<string, object>();
                    //Spout tests
                    {
                        //Get local context
                        LocalContext spoutCtx = LocalContext.Get();
                        //Get an instance of the spout
                        WordSpout spout = WordSpout.Get(spoutCtx, emptyDictionary);
                        //Call NextTuple to emit data
                        for (int i = 0; i < 10; i++)
                        {
                            spout.NextTuple(emptyDictionary);
                        }
                        //Store the stream for the next component
                        spoutCtx.WriteMsgQueueToFile("spout.txt");
                    }
                }
            }
        }

3. 開啟 **Program.cs** 並以下列內容取代現有的程式碼。

        using Microsoft.SCP;
        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Text;
        using System.Threading.Tasks;

        namespace WordCount
        {
            class Program
            {
                static void Main(string[] args)
                {
                    if (args.Count() > 0)
                    {
                        //Code to run on HDInsight cluster will go here
                    }
                    else
                    {
                        //Set log prefix information for the component being tested
                        System.Environment.SetEnvironmentVariable("microsoft.scp.logPrefix", "WordCount-LocalTest");
                        //Initialize the runtime
                        SCPRuntime.Initialize();

                        //If we are not running under the local context, throw an error
                        if (Context.pluginType != SCPPluginType.SCP_NET_LOCAL)
                        {
                            throw new Exception(string.Format("unexpected pluginType: {0}", Context.pluginType));
                        }
                        //Create an instance of LocalTest
                        LocalTest localTest = new LocalTest();
                        //Run the tests
                        localTest.RunTestCase();
                    }
                }
            }
        }

3. 執行應用程式。完成時，請查看 Visual Studio 專案下的 **WordCount\bin\debug** 目錄。應該有一個名為 **spout.txt** 的檔案，其包含在此測試期間 Spout 發出的資料。內容應該會類似下列文字。

        {"__isset":{"streamId":true,"tupleId":true,"evt":true,"data":true},"StreamId":"default","TupleId":"","Evt":1000,"Data":[[97,110,32,97,112,112,108,101,32,97,32,100,97,121,32,107,101,101,112,115,32,116,104,101,32,100,111,99,116,111,114,32,97,119,97,121]]}
        {"__isset":{"streamId":true,"tupleId":true,"evt":true,"data":true},"StreamId":"default","TupleId":"","Evt":1000,"Data":[[116,104,101,32,99,111,119,32,106,117,109,112,101,100,32,111,118,101,114,32,116,104,101,32,109,111,111,110]]}

    > [AZURE.NOTE] 在以上資料行中，"Data" 是 Spout 發出的字串，但是以位元組陣列形式儲存。例如，`[[97,110,32,97,112,112,108,101,32,97,32,100,97,121,32,107,101,101,112,115,32,116,104,101,32,100,111,99,116,111,114,32,97,119,97,121]]` 是 "an apple a day keeps the doctor away"。

###建立 Bolt

1. 在 [**方案總管**] 中，於 [**WordCount**] 上按一下滑鼠右鍵，然後按一下 [**加入 | 新增項目**]。選取 [**類別**] 並輸入 **SplitterBolt.cs** 做為名稱。最後按一下 [**確定**]。重複此步驟來加入名為 **CounterBolt.cs** 的類別。

2. 開啟 **SplitterBolt.cs** 檔案，以下列內容取代現有的程式碼。

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Text;
        using System.Threading.Tasks;
        using Microsoft.SCP;
        using System.Threading;

        namespace WordCount
        {
            class SplitterBolt : ISCPBolt
            {
                //Context
                private Context ctx;

                //Constructor
                public SplitterBolt(Context ctx)
                {
                    Context.Logger.Info("Splitter constructor called");
                    //Set context
                    this.ctx = ctx;
                    //Define the schema for the incoming tuples from spout
                    Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
                    //In this case, just a string tuple
                    inputSchema.Add("default", new List<Type>() {typeof (string)});
                    //Define the schema for tuples to be emitted from this bolt
                    Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
                    //We are also only emitting a string tuple
                    outputSchema.Add("default", new List<Type>() {typeof (string)});
                    //Declare both incoming and outbound schemas
                    this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, outputSchema));
                }

                //Get a new instance
                public static SplitterBolt Get(Context ctx, Dictionary<string, Object> parms)
                {
                    return new SplitterBolt(ctx);
                }

                //Process a tuple from the stream
                public void Execute(SCPTuple tuple)
                {
                    Context.Logger.Info("Execute enter");
                    //Get the incomin tuple value
                    string sentence = tuple.GetString(0);
                    //Split it
                    foreach (string word in sentence.Split(' '))
                    {
                        //Emit each word to the outbound stream
                        Context.Logger.Info("Emit: {0}", word);
                        this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new List<SCPTuple> { tuple }, new Values(word));
                    }
                    Context.Logger.Info("Execute exit");
                }
            }
        }

4. 開啟 **CounterBolt.cs** 檔案，以下列內容取代現有的程式碼。

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Text;
        using System.Threading.Tasks;
        using Microsoft.SCP;

        namespace WordCount
        {
            class CounterBolt : ISCPBolt
            {
                //Context
                private Context ctx;

                //Store each word and a count of how many times it has
                //been emitted by the splitter
                private Dictionary<string, int> counts = new Dictionary<string, int>();

                //Constructor
                public CounterBolt(Context ctx)
                {
                    Context.Logger.Info("Counter constructor called");

                    //Set context
                    this.ctx = ctx;
                    //Define the schema for the incoming tuples from spout
                    Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
                    //Just a string, which will contain a word from the splitter
                    inputSchema.Add("default", new List<Type>() { typeof(string) });
                    //Define the schema for tuples to be emitted from this bolt
                    Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
                    //In this case, a string (containing a word) and an int (containing the count)
                    outputSchema.Add("default", new List<Type>() { typeof(string), typeof(int) });
                    this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, outputSchema));
                }

                //Get an instance
                public static CounterBolt Get(Context ctx, Dictionary<string, Object> parms)
                {
                    return new CounterBolt(ctx);
                }

                //Process a tuple from the stream
                public void Execute(SCPTuple tuple)
                {
                    Context.Logger.Info("Execute enter");
                    //Get the word that was emitted from the splitter
                    string word = tuple.GetString(0);
                    //Have we seen this word before?
                    int count = counts.ContainsKey(word) ? counts[word] : 0;
                    //Increment and store the count for this word
                    count++;
                    counts[word] = count;

                    Context.Logger.Info("Emit: {0}, count: {1}", word, count);
                    //Emit the word and the count
                    this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new List<SCPTuple> { tuple }, new Values(word, count));

                    Context.Logger.Info("Execute exit");
                }
            }
        }

    > [AZURE.NOTE] 此 Bolt 會發出串流，對測試非常實用。在真實世界的方案中，在處理的尾端，您會將資料儲存到資料庫、佇列或其他持續性儲存區。

3. 開啟 **LocalTest.cs**，並在先前的 Spout 測試區塊之後加入 SplitterBolt 和 CounterBolt 的下列測試。

        //SplitterBolt tests
        {
            LocalContext splitterCtx = LocalContext.Get();
            SplitterBolt splitter = SplitterBolt.Get(splitterCtx, emptyDictionary);
            //Read from the 'stream' emitted by the spout
            splitterCtx.ReadFromFileToMsgQueue("spout.txt");
            List<SCPTuple> batch = splitterCtx.RecvFromMsgQueue();
            foreach (SCPTuple tuple in batch)
            {
                splitter.Execute(tuple);
            }
            //Store the stream for the next component
            splitterCtx.WriteMsgQueueToFile("splitter.txt");
        }
        //CounterBolt tests
        {
            LocalContext counterCtx = LocalContext.Get();
            CounterBolt counter = CounterBolt.Get(counterCtx, emptyDictionary);
            //Read from the 'stream' emitted by the splitter
            counterCtx.ReadFromFileToMsgQueue("splitter.txt");
            List<SCPTuple> batch = counterCtx.RecvFromMsgQueue();
            foreach (SCPTuple tuple in batch)
            {
                counter.Execute(tuple);
            }
            //Store the stream for the next component
            counterCtx.WriteMsgQueueToFile("counter.txt");
        }

4. 執行方案。完成之後，您現在在 **WordCount\bin\debug** 目錄中應該會有下列檔案。

    * **spout.txt** - WordSpout 發出的資料
    * **splitter.txt** - SplitterBolt 發出的資料
    * **counter.txt** - CounterBolt 發出的資料

###加入程式碼以在叢集上執行

1. 開啟 **Program.cs** 並將 `//Code to run on HDInsight cluster will go here` 行以下列取代，然後重新建置專案

        //The component to run
        string compName = args[0];
        //Run the component
        if ("wordspout".Equals(compName))
        {
            //Set the prefix for logging
            System.Environment.SetEnvironmentVariable("microsoft.scp.logPrefix", "WordCount-Spout");
            //Initialize the runtime
            SCPRuntime.Initialize();
            //Run the plugin (WordSpout)
            SCPRuntime.LaunchPlugin(new newSCPPlugin(WordSpout.Get));
        }
        else if ("splitterbolt".Equals(compName))
        {
            System.Environment.SetEnvironmentVariable("microsoft.scp.logPrefix", "WordCount-Splitter");
            SCPRuntime.Initialize();
            SCPRuntime.LaunchPlugin(new newSCPPlugin(SplitterBolt.Get));
        }
        else if ("counterbolt".Equals(compName))
        {
            System.Environment.SetEnvironmentVariable("microsoft.scp.logPrefix", "WordCount-Counter");
            SCPRuntime.Initialize();
            SCPRuntime.LaunchPlugin(new newSCPPlugin(CounterBolt.Get));
        }
        else
        {
            throw new Exception(string.Format("unexpected compName: {0}", compName));
        }

4. 建立名為 **WordCount.spec** 的新檔案，並使用下列做為內容。這可定義拓撲 - 元件、要在叢集的節點中建立多少個執行個體，以及它們間的資料流程。其編寫是使用[拓撲規格語言](#spec)。

        {
          :name "WordCount"
          :topology
            (nontx-topolopy
              "WordCount"

              {
                "spout"

                (spout-spec
                  (scp-spout
                    {
                      "plugin.name" "WordCount.exe"
                      "plugin.args" ["wordspout"]
                      "output.schema" {"default" ["sentence"]}
                    })

                  :p 1)
              }

              {
                "splitter"

                (bolt-spec
                  {
                    "spout" :shuffle
                  }

                  (scp-bolt
                    {
                      "plugin.name" "WordCount.exe"
                      "plugin.args" ["splitterbolt"]
                      "output.schema" {"default" ["word"]}
                    })

                  :p 1)

                "counter"

                (bolt-spec
                  {
                    "splitter" :global
                  }

                  (scp-bolt
                    {
                      "plugin.name" "WordCount.exe"
                      "plugin.args" ["counterbolt"]
                      "output.schema" {"default" ["word" "count"]}
                    })

                  :p 1)
              })

          :config
            {
              "topology.kryo.register" ["[B"]
            }
        }

4. 使用遠端桌面連接到 HDInsight Storm 叢集，並將您的本機 WordCount 專案 **bin\debug** 資料夾複製到 HDInsight Storm 叢集。例如，將它複製到 **%storm_home%\examples** 資料夾，並重新命名為 **WordCount**。

3. 也將 **WordCount.spec** 複製到 HDInsight 伺服器。將它放置在 **%storm_home%\examples** 目錄。

4. 在 HDInsight Storm 叢集上，使用桌面上的 [**Storm 命令列**] 圖示來開啟命令列，然後使用下列命令來啟動 WordCount 拓撲。

        bin\runspec examples\WordCount.spec temp examples\WordCount

    這將建立名為 **temp** 的新資料夾，並使用 WordCount 方案的規格與檔案來建立 **WordCount.jar** 檔案，然後將此檔案提交至 Storm。

5. 一旦命令完成，請從桌面開啟 **Storm UI**。應該會列出 **WordCount** 拓撲。您可以從 **Storm UI** 選取拓撲以檢視統計資料。

6. 若要停止拓撲，請在 **Storm UI** 中，選取 **WordCount**，然後選取 [**拓撲動作**] 的 [**刪除**]。

##摘要

在以上步驟中，您已了解如何使用 Stream Computing Platform 建立、測試及部署基本的字數統計拓撲。如需更多範例，請參閱您的 HDInsight 叢集上的 **%storm_home%\examples** 目錄。如需 SCP 的詳細資訊，請參閱下列參考。

##SCP 參考

###SCP 外掛程式介面

SCP 應用程式 (即外掛程式) 會插入到 Storm 管線。外掛程式為 .NET 主控台應用程式，其實作一或多個下列介面。  

-	**ISCPSpout** - 實作非交易式 Spout 時使用
- 	**ISCPTxSpout** - 實作交易式 Spout 時使用
-	**ISCPBolt** - 實作非交易式 Bolt 時使用
-	**ISCPBatchBolt** - 實作交易式 Bolt 時使用

####ISCPPlugin

ISCPPlugin 是所有其他 SCP 介面繼承的來源基礎介面。目前為虛擬介面。  

    public interface ISCPPlugin
    {
    }  

####ISCPSpout

ISCPSpout 為非交易式 spout 的介面。  

    public interface ISCPSpout : ISCPPlugin
    {
        void NextTuple(Dictionary<string, Object> parms);
        void Ack(long seqId, Dictionary<string, Object> parms);
        void Fail(long seqId, Dictionary<string, Object> parms);
    }  

呼叫 `NextTuple()` 時，程式碼可能發出一或多個 Tuple。如果沒有資料可發出，此方法應該返回而不發出任何資料。

只有在規格檔案中啟用 Ack 時，才會呼叫 `Ack()` 和 `Fail()`。`seqId` 用來識別已認可或失敗的 Tuple。如果非交易式拓撲中啟用認可，則 spout 中應該使用下列 emit 函數：  

    public abstract void Emit(string streamId, List<object> values, long seqId);  

如果非交易式拓撲中不支援認可，則 `Ack()` 和 `Fail()` 可保持為空白函數。  

這些函數的 `parms` 參數為空白的 Dictionary 物件，並且保留供未來使用。  

> [AZURE.NOTE] `NextTuple()`、`Ack()` 和 `Fail()` 都是在單一執行緒上的緊密迴圈中呼叫。如果沒要發出的 Tuple，對短時間 (例如 10 毫秒) 請考慮使用 `sleep`，以節省 CPU 週期。

####ISCPTxSpout

ISCPTxSpout 為交易式 spout 的介面。  

    public interface ISCPTxSpout : ISCPPlugin
    {
        void NextTx(out long seqId, Dictionary<string, Object> parms);
        void Ack(long seqId, Dictionary<string, Object> parms);
        void Fail(long seqId, Dictionary<string, Object> parms);
    }  

呼叫 `NextTx()` 以啟動新的交易。使用 `seqId` 來識別交易，也用於 `Ack()` 和 `Fail()`。從 `NextTx()` 發出的資料將儲存在 ZooKeeper 以支援重播。因為 ZooKeeper 的儲存體容量極為有限，您在交易式 spout 中應該只發出中繼資料，而非大量資料。  

Storm 會自動重播交易 (若失敗)，所以正常情況下應該不會呼叫 Fail()。但是，如果 SCP 可以檢查交易式 spout 所發出的中繼資料，若中繼資料無效則可以呼叫 Fail()。

這些函數的 `parms` 參數為空白的 Dictionary 物件，並且保留供未來使用。

> [AZURE.NOTE] `NextTx()`、`Ack()` 和 `Fail()` 都是在單一執行緒上的緊密迴圈中呼叫。如果沒要發出的 Tuple，對短時間 (例如 10 毫秒) 請考慮使用 `sleep`，以節省 CPU 週期。

####ISCPBolt

ISCPBolt 為非交易式 bolt 的介面。  

    public interface ISCPBolt : ISCPPlugin
    {
        void Execute(SCPTuple tuple);
    }

有新的 Tuple 可用時，將會呼叫 `Execute()` 函數來處理它。

####ISCPBatchBolt

ISCPBatchBolt 為交易式 bolt 的介面。  

    public interface ISCPBatchBolt : ISCPPlugin
    {
        void Execute(SCPTuple tuple);
        void FinishBatch(Dictionary<string, Object> parms);
    }  

有新的 Tuple 可用時，將會呼叫 `Execute()` 函數來處理它。此交易結束時，將呼叫 `FinishBatch()`。

這些函數的 `parms` 參數為空白的 Dictionary 物件，並且保留供未來使用。

> [AZURE.NOTE] 實作 `ISCPBatchBolt` 的 Bolt 可從 `parms` 取得 `StormTxAttempt`。`StormTxAttempt` 可用來判斷 Tuple 是原始或重播的嘗試。這通常是在認可 bolt 上完成，**HelloWorldTx** 範例中會示範。  

###物件模型

SCP.NET 也提供一組簡單的關鍵物件供開發人員在程式設計中使用。

* `Context` - 對應用程式提供執行中環境的相關資訊

* `StateStore` 提供中繼資料服務、單調數列產生和免等待協調。高階分散式並行抽象可根據 `StateStore` 來建置，包括分散式鎖定、分散式佇列、屏障和交易服務。

* `SCPRuntime` - 在 Storm 上執行方案時初始化執行階段環境，並啟動外掛程式

* `LocalContext` - 在 Visual Studio 中本機測試應用程式時，提供將發出的 Tuple 序列化和還原序列化為本機檔案的方法

####Context

每個 ISCPPlugin 執行個體 (ISCPSpout/ISCPBolt/ISCPTxSpout/ISCPBatchBolt) 都有一個對應的 Context 執行個體。Context 提供的功能分成兩部分

* **Static** -  在整個 C# 處理序中可用
* **Dynamic** - 對特定執行個體可用  

**靜態內容**

* `public static ILogger Logger = null;` - 針對記錄用途提供  

* `public static SCPPluginType pluginType;` - 取得 C# 處理序的外掛程式類型。如果 C# 程序在本機測試模式 (無 Java) 中執行，則外掛程式類型為 "SCP_NET_LOCAL"

        public enum SCPPluginType
        {
            SCP_NET_LOCAL = 0,
            SCP_NET_SPOUT = 1,
            SCP_NET_BOLT = 2,
            SCP_NET_TX_SPOUT = 3,
            SCP_NET_BATCH_BOLT = 4
        }  

* `public static Config Config { get; set; }` - 從 JVM 取得組態參數。初始化外掛程式時，會從 JVM 傳遞參數。`Config` 包含兩個字典

    * `public Dictionary<string, Object> stormConf { get; set; }` - 包含 Storm 定義的參數

    * `public Dictionary<string, Object> pluginConf { get; set; }` - 包含 SCP 定義的參數

* `public static TopologyContext TopologyContext { get; set; } ` - 取得拓撲內容。對於具有多個平行處理原則的元件最實用。以下示範如何存取拓撲內容

        if (Context.pluginType != SCPPluginType.SCP_NET_LOCAL)
        {
            Context.Logger.Info("TopologyContext info:");
            TopologyContext topologyContext = Context.TopologyContext;
            Context.Logger.Info("taskId: {0}", topologyContext.GetThisTaskId());
            taskIndex = topologyContext.GetThisTaskIndex();
            Context.Logger.Info("taskIndex: {0}", taskIndex);
            string componentId = topologyContext.GetThisComponentId();
            Context.Logger.Info("componentId: {0}", componentId);
            List<int> componentTasks = topologyContext.GetComponentTasks(componentId);
            Context.Logger.Info("taskNum: {0}", componentTasks.Count);
        }

**動態內容**

* `public void DeclareComponentSchema(ComponentStreamSchema schema);` - 宣告串流的輸出和輸入結構描述。以下範例宣告的輸入結構描述包含單一字串 Tuple，而輸出結構描述則包含字串 Tuple 和整數 Tuple

        this.ctx = context;
        Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
        inputSchema.Add("default", new List<Type>() { typeof(string) });
        outputSchema.Add("default", new List<Type>() { typeof(string), typeof(int) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, outputSchema));

* `public abstract void Emit(List<object> values);` - 發出一或多個 Tuple 至預設串流。以下範例會發出兩個 Tuple 至預設串流

        this.ctx.Emit(new Values(word, count));

* `public abstract void Emit(string streamId, List<object> values);` - 發出一或多個 Tuple 至指定串流。以下範例會發出兩個 Tuple 至名為 'mystream' 的串流

        this.ctx.Emit("mystream", new Values(word, count));

使用已啟用 Ack 的非交易 Spout 和 Bolt 時，請使用下列。

* `public abstract void Emit(string streamId, List<object> values, long seqId);` - **從 spout** 發出一或多個 Tuple 和一個序列識別碼至指定串流。以下範例會發出一個 Tuple 和一個序列識別碼至預設串流

        this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new Values(word), lastSeqId);

* `public abstract void Emit(string streamId, IEnumerable<SCPTuple> anchors, List<object> values);` - **從 Bolt** 發出一或多個 Tuple 和序列識別碼至指定串流。發出的 Tuple 會錨定到指定為 `anchors` 的傳入 Tuple。這可讓認可流程遵循傳入/傳出 Tuple 鏈結備份管線。以下範例會發出一個 Tuple 和一個序列識別碼至預設串流，並對 `tuple` 中包含的傳入 Tuple 錨定發出的 Tuple

        this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new List<SCPTuple> { tuple }, new Values(word));

* `public abstract void Ack(SCPTuple tuple);` - 認可 Tuple。叫用原始產生 Tuple 之 Spout 的 `ISCPSpout.Ack` 函數

* `public abstract void Fail(SCPTuple tuple);` - 使 Tuple 失敗。叫用原始產生 Tuple 之 Spout 的 `ISCPSpout.Fail` 函數

####StateStore

SCP 應用程式可使用 `State` 物件將資訊保存在 ZooKeeper 中，特別是針對交易式拓撲。這可讓交易式 Spout 接收來自 ZooKeeper 的狀態，並在發生當機時重新啟動管線。  

`StateStore` 物件提供下列方法。

* `public static StateStore Get(string storePath, string connStr);` - 取得指定路徑和連接字串的 `StateStore`

* `public State Create();` - 在此狀態存放區執行個體中建立新的 `State` 物件

* `public IEnumerable<State> GetUnCommitted();` - 取得未認的所有 `State` 物件，排除中止的狀態

* `public IEnumerable<State> States();` - 取得 `StateStore` 中的所有 'State' 物件


* `public T Get<T>(string info = null);` - 取得 `State` 或 `Registry` 物件

    * `info` - 要取得的 `Registry` 名稱。僅在擷取 `Registry` 物件時使用

    * `T` - `State` 或 `Registry` 的類型

* `public IEnumerable<Registry> Commited();` - 取得包含認可的 `State` 的 `Registry` 物件

* `public IEnumerable<Registry> Aborted();` - 取得包含中止的 `State` 的 `Registry` 物件。

* `public State GetState(long stateId)` - 取得指定的狀態識別碼的 `State` 物件

 **State** 提供下列方法。

* `public void Commit(bool simpleMode = true);` - 設定要認可的 `State` 物件的狀態

    > [AZURE.NOTE] 當 `simpleMode` 設為 true，它只會刪除 ZooKeeper 中對應的 ZNode。否則會刪除目前的 ZNode，並在 `COMMITTED_PATH` 中加入新的節點。

* `public void Abort();` - 將 `State` 物件的狀態設為中止

* `public void PutAttribute<T>(string key, T attribute);` - 設定指定機碼的屬性值

    * `key` - 設定屬性的機碼

    * `attribute` - 屬性值

* `public T GetAttribute<T>(string key);` - 取得指定機碼的屬性值

####SCPRuntime

SCPRuntime 提供下列方法。

* `public static void Initialize();` - 初始化 SCP 執行階段環境。呼叫時，C# 處理序會連接到 JVM，並取得組態參數和拓撲內容

* `public static void LaunchPlugin(newSCPPlugin pluginDelegate);` - 啟動訊息處理迴圈。在此迴圈中，C# 外掛程式將收到來自 JVM 的訊息 (包括 Tuple 和控制訊號)，然後處理訊息

    * `pluginDelegate` - 委派可傳回實作 ISCPSpout/IScpBolt/ISCPTxSpout/ISCPBatchBolt 的物件

####LocalContext


* `List<SCPTuple> RecvFromMsgQueue();` - 從佇列接收 Tuple

* `void WriteMsgQueueToFile(string filepath, bool append = false);` - 保存 Tuple 至檔案

* `void ReadFromFileToMsgQueue(string filepath);` - 從檔案讀取 Tuple


###<a id="spec"></a>拓撲規格語言

SCP 拓撲規格是特定領域的語言，用來描述和設定 SCP 拓撲。它的基於 Storm 的 [Clojure DSL](http://storm.incubator.apache.org/documentation/Clojure-DSL.html)。  

使用下列項目來定義拓撲。

|新函數|	參數|	描述
|-------------|-----------|-----------
|**tx-topolopy**|	topology-name<br> spout-map<br> bolt-map|	 以拓撲名稱、spout 定義對應和 bolt 定義對應來定義交易式拓撲
|**scp-tx-spout**|	exec-name<br> args<br> fields|	定義交易式 spout。它會使用 ***args*** 搭配 ***exec-name*** 來執行應用程式。<br><br> ***fields*** 是 spout 的輸出欄位
|**scp-tx-batch-bolt**|	exec-name<br> args<br> fields| 	定義交易式批次 Bolt。它會使用 ***args*** 搭配 ***exec-name*** 來執行應用程式。<br><br>fields 是 bolt 的輸出欄位。
|**scp-tx-commit-bolt**|	exec-name<br>args<br>fields|	定義交易式認可者 Bolt。它會使用 args 搭配 ***exec-name*** 來執行應用程式。<br><br> ***fields*** 是 bolt 的輸出欄位
|**nontx-topolopy**|	topology-name<br> spout-map<br>bolt-map|	以拓撲名稱、spout 定義對應和 bolt 定義對應來定義非交易式拓撲
|**scp-spout**|	exec-name<br>args<br>fields<br>parameters|	定義非交易式 spout。它會使用 ***args*** 搭配 ***exec-name*** 來執行應用程式。<br><br> ***fields*** 是 spout 的輸出欄位<br><br> ***parameters*** 為選用，使用它來指定一些參數，例如 "nontransactional.ack.enabled"。
|**scp-bolt**|	exec-name<br>args<br>fields<br>parameters|	定義非交易式 Bolt。它會使用 ***args*** 搭配 ***exec-name*** 來執行應用程式。<br><br> ***fields*** 是 bolt 的輸出欄位<br><br> ***parameters*** 為選用，使用它來指定一些參數，例如 "nontransactional.ack.enabled"。

下列是定義拓撲時可使用的關鍵字

|關鍵字|	描述
|---------|------------
|**:name**|	定義拓撲名稱
|**:topology**|	使用上述函數和內建函數來定義拓撲。
|**:p**|	為每個 spout 或 bolt 定義平行處理提示。
|**:config**|	定義設定參數或更新現有的設定參數
|**:schema**|	定義串流的結構描述。

常用參數

|參數|	描述
|---------|------------
|**"plugin.name"**|	C# 外掛程式的 exe 檔名
|**"plugin.args"**|	外掛程式引數
|**"output.schema"**|	輸出結構描述
|**"nontransactional.ack.enabled"**|	非交易式拓撲是否啟用認可

透過 ***runspec*** 命令，拓撲規格可直接提交至 Storm 叢集供執行，其位於 HDInsight Storm 叢集上的 **%storm_home%\bin** 目錄。  

    usage: runSpec [spec-file target-dir [resource-dir] [-cp classpath]]
     ex: runSpec examples\HelloWorld\HelloWorld.spec target examples\HelloWorld\Target

> [AZURE.NOTE]  ***resource-dir*** 參數為選用，當您要執行 C# 應用程式時需要加以指定，此目錄將包含應用程式、依存性和組態。  

 ***classpath*** 參數也是選用。用來指定 Java 類別路徑 (如果規格檔包含 Java Spout 或 Bolt)。  

###其他功能

####輸入和輸出結構描述宣告

呼叫 `Emit()` 時，平台會將 Tuple 序列化為位元組陣列，並將它傳送至 JVM。Storm 會接著將此 Tuple 傳送至目標。Bolt 會接著收到 Tuple。針對 C# Bbolt，會從 JVM 收到 Tuple，並轉換回原始類型。

為了支援此序列化和還原序列化，您必須宣告輸入和輸出的結構描述。這些是定義為 `Dictionary<string, List<Type>` 物件，其中機碼為串流識別碼，而值為將發出的 Tuple 的 `Types`。元件可宣告多個串流。

    public class ComponentStreamSchema
    {
        public Dictionary<string, List<Type>> InputStreamSchema { get; set; }
        public Dictionary<string, List<Type>> OutputStreamSchema { get; set; }
        public ComponentStreamSchema(Dictionary<string, List<Type>> input, Dictionary<string, List<Type>> output)
        {
            InputStreamSchema = input;
            OutputStreamSchema = output;
        }
    }

`Context` 物件會提供 `DeclareComponentSchema`，可用來宣告序列化/還原序列化的結構描述。

     public void DeclareComponentSchema(ComponentStreamSchema schema)

> [AZURE.NOTE] 您必須確定發出的 Tuple 遵守該串流所定義的結構描述，否則您會收到執行階段例外狀況。  

####多重串流支援

您可以發出至多個串流，方法是進行對 `Emit()` 的多個呼叫，指定要寫入的每個串流的 `streamId` 參數。

> [AZURE.NOTE] 發出給不存在的串流會造成執行階段例外狀況。

####欄位分組

Strom 中內建的欄位群組功能在 SCP.NET 中無法正常運作。將資料處理至 JVM 時，所有欄位資料類型實際上為 `byte[]`，而欄位群組會使用 `byte[]` 物件雜湊碼來執行群組。`byte[]` 物件雜湊碼為此物件在記憶體中的位址，因此具有相同內容但不同位址的兩個 `byte[]` 物件的群組會出現錯誤。

若要解決此問題，請在規格中使用 `scp-field-group`。這將使用位元組的內容[]來執行群組。以下是在規格中使用此動作的範例。  

    (bolt-spec
        {
            "spout_test""" (scp-field-group :non-tx [0,1])
        }
        ...
    )  

此範例會執行下列動作。

* `scp-field-group` - 使用自訂群組  
* `:tx` 或 `:non-tx` - 指出這是交易或非交易
* `[0,1]` - 使用欄位識別碼的 Hashset，從 0 開始。  

####混合式拓撲

最原生的 Storm Spout、Bolt 和拓撲是在 Java 中實作。為了支援在使用 C# 元件的方案中重複使用這些元件，SCP 可讓您在規格中建立/定義混合式拓撲。

* **Java Spout 或 Bolt** - 在規格檔案中，`scp-spout` 和 `scp-bolt` 也可用來指定 Java Spout 和 Bolt。下列範例示範指定類別名稱為 `microsoft.scp.example.HybridTopology.Generator` 的 Java Spout

        (spout-spec
          (microsoft.scp.example.HybridTopology.Generator.)
          :p 1)

* **Java 類別路徑** - 使用 Java Spout 或 Bolt 時，您應該先將 Spout 或 Bolt 編譯為 JAR 檔案。然後，在使用 **runSpec** 命令時使用 `-cp` 參數加入 Java 類別路徑。下列範例包含的 JAR 檔案位於 **examples\HybridTopology\java\target** 目錄下。  

        bin\runSpec.cmd examples\HybridTopology\HybridTopology.spec specs examples\HybridTopology\net\Target -cp examples\HybridTopology\java\target\*  


* **Java 和 C# 之間的序列化和還原序列化** - 若要序列化 Java 和 C# 之間的物件，請使用 `CustomizedInteropJSONSerializer`

    > [WACOM.NOTE] 目前的 `CustomizedInteropJSONSerializer` 僅支援 **Java Spout** 和 **C# Bolt**。

    * 在規格檔案中指定 Java 元件的序列化程式

            (scp-bolt
              {
                "plugin.name" "HybridTopology.exe"
                "plugin.args" ["displayer"]
                "output.schema" {}
                "customized.java.serializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer"]
              })  

    * 在您的 C# 元件中使用序列化程式

            Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
            inputSchema.Add("default", new List<Type>() { typeof(Person) });
            this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, null));
            this.ctx.DeclareCustomizedDeserializer(new CustomizedInteropJSONDeserializer());  

    只要資料類型不要太複雜，預設實作應該能夠因應大多數的情況。如果資料類型太複雜，或因為預設實作的效能不符合您的需求，您可以使用下列介面來建立自訂實作，以滿足您的需求

    **Java 中的序列化介面**  

            public interface ICustomizedInteropJavaSerializer {
                public void prepare(String[] args);
                public List<ByteBuffer> serialize(List<Object> objectList);
            }  

    **C# 中的還原序列化介面**

            public interface ICustomizedInteropCSharpDeserializer
            {
                List<Object> Deserialize(List<byte[]> dataList, List<Type> targetTypes);
            }  

###SCP 主機模式

主機模式可讓您將專案編譯為 DLL，並使用 **SCPHost.exe** 加以管理。這是在生產設定中管理您的方案的建議方式。使用主機模式時，規格檔案會將 `SCPHost.exe` 列為外掛程式。

以下是對 HelloWorld 範例使用主機模式之規格檔案的範例。

    (scp-spout
      {
        "plugin.name" "SCPHost.exe"
        "plugin.args" ["HelloWorld.dll" "Scp.App.HelloWorld.Generator" "Get"]
        "output.schema" {"default" ["sentence"]}
      })

* **HelloWorld.dll** - 包含 Spout 和 Bolt 的組件
* **Scp.App.HelloWorld.Generator** - **HelloWorld.dll** 中包含的 Spout 的類別名稱
* **Get** - 叫用以取得 Spout 的執行個體之方法


##SCP 程式設計範例

以下以 SCP 編寫的範例應用程式可在您的 HDInsight Storm 叢集上找到，位於 **%storm_home%\examples**。

* **HelloWorld** - HelloWorld 是 SCP.Net 非常簡易的範例，並且與本文中稍早使用的字數統計範例類似。它使用非交易拓撲，具有一個名為 **generator** 的 Spout，以及兩個名為 **splitter** 和 **counter** 的 Bolt。Spout **generator** 會隨機產生一些句子，並發出這些句子給 **splitter**。Bolt **splitter** 會將句子分割成單字，再發出這些單字給 **counter** Bolt。Bolt **counter** 使用字典來記錄每個單字出現的次數。

    此範例有兩個規格檔：**HelloWorld.spec** 和 **HelloWorld_EnableAck.spec**。使用 **HelloWorld_EnableAck.spec** 時，範例將認可傳遞經過 Bolt 的 Tuple，不過 **splitter** 的設計則會隨機使某些 Bolt 失敗，以示範非交易拓撲中的失敗處理。

* **HelloWorldTx** - 如何實作交易拓撲的範例。它具有一個名為 **generator** 的 Spout，一個名為 **partial-count** 的批次 Bolt，以及一個名為 **count-sum** 的認可 Bolt。還有三個預先建立的 txt 檔案可與此拓撲搭配使用：**DataSource0.txt**、**DataSource1.txt** 和 **DataSource2.txt**。

    在每個交易中，Spout 會隨機選擇兩個檔案，然後發出這兩個檔名給 **partial-count** Bolt。Bolt 會先從收到的 Tuple 中取得檔名，然後開啟檔案並計算此檔案中的字數。最後再發出字數給 **count-sum** Bolt。 **count-sum** bolt 將計算總數。  

    為了符合「剛好一次」語意，**count-sum** Bolt 需要判斷是否在處理重播的交易。在此範例中，它有一個靜態成員變數：  

        public static long lastCommittedTxId = -1;  

    建立 Bolt 的執行個體時，它會從輸入參數中取得交易嘗試 (`txAttempt`)：  

        public static CountSum Get(Context ctx, Dictionary<string, Object> parms)
        {
            /* for transactional topology, we can get txAttempt from the input parms */
            if (parms.ContainsKey(Constants.STORM_TX_ATTEMPT))
            {
                StormTxAttempt txAttempt = (StormTxAttempt)parms[Constants.STORM_TX_ATTEMPT];
                return new CountSum(ctx, txAttempt);
            }
            else
            {
                throw new Exception("null txAttempt");
            }
        }  

    呼叫 `FinishBatch()` 時，將會更新 `lastCommittedTxId` (如果不是重播的交易)。  

        public void FinishBatch(Dictionary<string, Object> parms)
        {
            /* judge whether it is a replayed transaction? */
            bool replay = (this.txAttempt.TxId <= lastCommittedTxId);

            if (!replay)
            {
                /* If it is not replayed, update the toalCount and lastCommittedTxId vaule */
                totalCount = totalCount + this.count;
                lastCommittedTxId = this.txAttempt.TxId;
            }
            ... ...
        }  

* **HybridTopology** - 此拓撲包含 Java Spout 和 C# Bolt。它採用 SCP 提供的預設序列化和還原序列化實作。請參閱 **%storm_home%examples\HybridTopology\HybridTopology.spec** 以取得詳細資料，以及 **SubmitTopology.bat** 以取得在提交拓撲時如何指定 Java 類別路徑的詳細資訊。  


[1]: ./media/hdinsight-hadoop-storm-scpdotnet-csharp-develop-streaming-data-processing-application/hdinsight-hadoop-storm-scpdotnet-csharp-develop-streaming-data-processing-application-01.png


<!--HONumber=35.1-->
