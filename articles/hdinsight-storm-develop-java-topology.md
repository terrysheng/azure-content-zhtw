<properties
   pageTitle="開發 Apache Storm on HDInsight 的 Java 型拓撲 | Azure"
   description="了解如何建立簡單字數拓撲，以使用 Java 建立 Storm 拓撲。"
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="java"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="02/18/2015"
   ms.author="larryfr"/>

# 開發 Apache Storm on HDInsight 的 Java 型拓撲

了解如何使用 Maven 建立 Apache Storm on HDInsight 之 Java 型拓撲的基本程序。您將逐步進行如何使用 Maven 和 Java 建立基本字數應用程式的程序。雖然提供 Eclipse 的使用指示，但是您還是可以使用所選擇的文字編輯器。

完成這份文件中的步驟之後，就會有可部署到 Apache Storm on HDInsight 的基本拓撲。

## 必要條件

* <a href="https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html" target="_blank">Java Developer Kit (JDK) 第 7 版</a>

* <a href="https://maven.apache.org/download.cgi" target="_blank">Maven</a> - Maven 是 Java 專案的專案建置系統

* 文字編輯器，例如 <a href="http://www.gnu.org/software/emacs/" target="_blank">Emacs<a>、 <a href="http://www.sublimetext.com/" target="_blank">Sublime Text</a>、 <a href="https://atom.io/" target="_blank">Atom.io</a>、 <a href="http://brackets.io/" target="_blank">Brackets.io</a>或記事本。或者，整合式開發環境 (IDE)，例如 <a href="https://eclipse.org/" target="_blank">Eclipse</a> (Luna 版或更新版本)。

	> [AZURE.NOTE] 您的編輯器或 IDE 可能具有處理 Eclipse 的特定功能，但未記載在這份文件中。如需編輯環境功能的詳細資訊，請參閱所使用產品的文件。

## 設定環境變數

安裝 Java 和 JDK 時即會設定下列環境變數；不過，您應該確認它們存在並且包含您系統的正確值

* **JAVA_HOME** - 應該指向已安裝 Java 執行階段環境 (JRE) 的目錄。例如，在 Unix 或 Linux 散發套件上，它的值應該類似 `/usr/lib/jvm/java-7-oracle`。在 Windows 上，它的值類似 `c:\Program Files (x86)\Java\jre1.7`

* **PATH** - 應該包含下列路徑：

	* **JAVA_HOME** 或對等的路徑

	* **JAVA_HOME\bin** 或對等的路徑

	* 已安裝 Maven 的目錄

## 建立新的 Maven 專案

從命令列中，使用下列命令建立名為 **WordCount** 的新 Maven 專案。

	mvn archetype:generate -DarchetypeArtifactId=maven-archetype-quickstart -DgroupId=com.microsoft.example -DartifactId=WordCount -DinteractiveMode=false

這會在目前的位置中建立名為 **WordCount** 的新目錄，內含基本 Maven 專案。

**WordCount** 目錄會包含下列項目：

* **pom.xml** - 包含 Maven 專案的設定

* **src\main\java\com\microsoft\example** - 包含應用程式碼

* **src\test\java\com\microsoft\example** - 包含您應用程式的測試。在此範例中，我們不會建立測試

### 移除範例程式碼

因為我們要從頭建立應用程式，所以請刪除產生的測試和應用程式檔案：

*  **src\test\java\com\microsoft\example\AppTest.java**

*  **src\main\java\com\microsoft\example\App.java**

## 新增相依性

因為這是 Storm 拓撲，所以您必須新增 Storm 元件的相依性。開啟 **pom.xml**，並在 **&lt;dependencies>** 區段中新增下列程式碼。

	<dependency>
	  <groupId>org.apache.storm</groupId>
	  <artifactId>storm-core</artifactId>
	  <version>0.9.2-incubating</version>
	  <!-- keep storm out of the jar-with-dependencies -->
	  <scope>provided</scope>
	</dependency>

在編譯期間，Maven 會使用此資訊來查閱 Maven 儲存機制中的 **storm-core**。它會先查看本機電腦上的儲存機制。如果檔案不存在，則會從公用 Maven 儲存機制進行下載，並將它們儲存在本機儲存機制中。

> [AZURE.NOTE] 請記下我們在區段中新增的 `<scope>provided</scope>` 行。這會告訴 Maven 從我們建立的任何 Jar 檔案中排除 storm-core，因為系統將會予以提供。這可讓您建立的封裝更小，並確保它們使用 Storm on HDInsight 叢集上所含的 storm-core 位元。

## 建置組態

Maven 外掛程式可讓您自訂專案的建置階段 (例如，如何編譯專案，或如何將它封裝到 jar 檔案)。開啟 **pom.xml**，並直接在 `</project>` 行上方新增下列內容。

	<build>
	  <plugins>
	  </plugins>
	</build>

此區段將用來新增外掛程式和其他建置組態選項。

### 新增外掛程式

針對 Storm 拓撲， <a href="http://mojo.codehaus.org/exec-maven-plugin/" target="_blank">Exec 外掛程式</a> 十分有用，因為它可讓您輕鬆地在開發環境上本機執行拓撲。將下列內容新增至 **pom.xml** 的 `<plugins>` 區段，以包括 Exec 外掛程式。

	<plugin>
      <groupId>org.codehaus.mojo</groupId>
      <artifactId>exec-maven-plugin</artifactId>
      <executions>
        <execution>
        <goals>
          <goal>exec</goal>
        </goals>
        </execution>
      </executions>
      <configuration>
        <executable>java</executable>
        <includeProjectDependencies>true</includeProjectDependencies>
        <includePluginDependencies>false</includePluginDependencies>
        <classpathScope>compile</classpathScope>
        <mainClass>${storm.topology}</mainClass>
      </configuration>
    </plugin>

另一個有用的外掛程式是 <a href="http://maven.apache.org/plugins/maven-compiler-plugin/" target="_blank">Compiler 外掛程式</a>，這用來變更編譯選項。我們需要此外掛程式的主要原因是要變更 Maven 用於您應用程式之來源和目標的 Java 版本 (需要的是 1.7)。

在 **pom.xml** 的 `<plugins>` 區段中新增下列內容，以包括 Compiler 外掛程式並將來源和目標版本設定為 1.7。

	<plugin>
      <groupId>org.apache.maven.plugins</groupId>
      <artifactId>maven-compiler-plugin</artifactId>
      <configuration>
        <source>1.7</source>
        <target>1.7</target>
      </configuration>
    </plugin>

## 建立拓撲

Java 型 Storm 拓撲包含您必須編寫或參照為相依性的三個元件。

* **Spout** - 讀取外部來源中的資料，並發出進入拓撲的資料流

* **Bolt** - 對 Spout 或其他 Bolt 所發出的資料流執行處理，並發出一個或多個資料流

* **拓撲** - 定義如何排列 Spout 和 Bolt，並提供拓撲的進入點。

### 建立 Spout

若要減少設定外部資料來源的需求，下列 Spout 只會發出隨機的句子。它是 Storm-Starter 範例 (<a href="https://github.com/apache/storm/blob/master/examples/storm-starter/" target="_blank">https://github.com/apache/storm/blob/master/examples/storm-starter/</a>) 所提供 Spout 的修改過版本。

> [AZURE.NOTE] 如需從外部資料來源讀取之 Spout 的範例，請參閱下列其中一個範例。
>
> * <a href="https://github.com/apache/storm/blob/master/examples/storm-starter/src/jvm/storm/starter/spout/TwitterSampleSpout.java" target="_blank">TwitterSampleSpout</a> - 從 Twitter 讀取的範例 Spout
>
> * <a href="https://github.com/apache/storm/tree/master/external/storm-kafka" target="_blank">Storm Kafka</a> - 從 Kafka 讀取的 Spout

針對 Spout，在 **src\main\java\com\microsoft\example** 目錄中建立名為 **RandomSentenceSpout.java** 的新檔案，並使用下列內容做為內容。

    /**
     * Licensed to the Apache Software Foundation (ASF) under one
     * or more contributor license agreements.  See the NOTICE file
     * distributed with this work for additional information
     * regarding copyright ownership.  The ASF licenses this file
     * to you under the Apache License, Version 2.0 (the
     * "License"); you may not use this file except in compliance
     * with the License.  You may obtain a copy of the License at
     *
     * http://www.apache.org/licenses/LICENSE-2.0
     *
     * Unless required by applicable law or agreed to in writing, software
     * distributed under the License is distributed on an "AS IS" BASIS,
     * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
     * See the License for the specific language governing permissions and
     * limitations under the License.
     */

     /**
      * Original is available at https://github.com/apache/storm/blob/master/examples/storm-starter/src/jvm/storm/starter/spout/RandomSentenceSpout.java
      */

    package com.microsoft.example;

    import backtype.storm.spout.SpoutOutputCollector;
    import backtype.storm.task.TopologyContext;
    import backtype.storm.topology.OutputFieldsDeclarer;
    import backtype.storm.topology.base.BaseRichSpout;
    import backtype.storm.tuple.Fields;
    import backtype.storm.tuple.Values;
    import backtype.storm.utils.Utils;

    import java.util.Map;
    import java.util.Random;

    //This spout randomly emits sentences
    public class RandomSentenceSpout extends BaseRichSpout {
      //Collector used to emit output
      SpoutOutputCollector _collector;
      //Used to generate a random number
      Random _rand;

      //Open is called when an instance of the class is created
      @Override
      public void open(Map conf, TopologyContext context, SpoutOutputCollector collector) {
      //Set the instance collector to the one passed in
        _collector = collector;
        //For randomness
        _rand = new Random();
      }

      //Emit data to the stream
      @Override
      public void nextTuple() {
      //Sleep for a bit
        Utils.sleep(100);
        //The sentences that will be randomly emitted
        String[] sentences = new String[]{ "the cow jumped over the moon", "an apple a day keeps the doctor away",
            "four score and seven years ago", "snow white and the seven dwarfs", "i am at two with nature" };
        //Randomly pick a sentence
        String sentence = sentences[_rand.nextInt(sentences.length)];
        //Emit the sentence
        _collector.emit(new Values(sentence));
      }

      //Ack is not implemented since this is a basic example
      @Override
      public void ack(Object id) {
      }

      //Fail is not implemented since this is a basic example
      @Override
      public void fail(Object id) {
      }

      //Declare the output fields. In this case, an sentence
      @Override
      public void declareOutputFields(OutputFieldsDeclarer declarer) {
        declarer.declare(new Fields("sentence"));
      }
    }

請用一些時間閱讀程式碼註解，以了解此 Spout 的運作方式。

> [AZURE.NOTE] 雖然此拓撲只使用一個 Spout，但是其他拓撲可能會有將資料從不同來源送入拓撲的數個 Spout。

### 建立 Bolt

Bolt 會處理資料的處理。針對此拓撲，我們會有兩個：

* **SplitSentence** - 會將 RandomSentenceSpout 所發出的句子分割成個別單字

* **WordCount** - 計算每個單字的出現次數

> [AZURE.NOTE] Bolt 可以逐字進行任何作業：計算、持續性、與外部元件交談等。

在 **src\main\java\com\microsoft\example** 目錄中，建立兩個新的檔案：**SplitSentence.java** 和 **WordCount.Java**。使用下列內容做為檔案的內容。

**SplitSentence**

    package com.microsoft.example;

    import java.text.BreakIterator;

    import backtype.storm.topology.BasicOutputCollector;
    import backtype.storm.topology.OutputFieldsDeclarer;
    import backtype.storm.topology.base.BaseBasicBolt;
    import backtype.storm.tuple.Fields;
    import backtype.storm.tuple.Tuple;
    import backtype.storm.tuple.Values;

    //There are a variety of bolt types. In this case, we use BaseBasicBolt
    public class SplitSentence extends BaseBasicBolt {

      //Execute is called to process tuples
      @Override
      public void execute(Tuple tuple, BasicOutputCollector collector) {
        //Get the sentence content from the tuple
        String sentence = tuple.getString(0);
        //An iterator to get each word
        BreakIterator boundary=BreakIterator.getWordInstance();
        //Give the iterator the sentence
        boundary.setText(sentence);
        //Find the beginning first word
        int start=boundary.first();
        //Iterate over each word and emit it to the output stream
        for (int end=boundary.next(); end != BreakIterator.DONE; start=end, end=boundary.next()) {
          //get the word
          String word=sentence.substring(start,end);
          //If a word is whitespace characters, replace it with empty
          word=word.replaceAll("\\s+","");
          //if it's an actual word, emit it
          if (!word.equals("")) {
            collector.emit(new Values(word));
          }
        }
      }

      //Declare that emitted tuples will contain a word field
      @Override
      public void declareOutputFields(OutputFieldsDeclarer declarer) {
        declarer.declare(new Fields("word"));
      }
    }

**WordCount**

    package com.microsoft.example;

    import java.util.HashMap;
    import java.util.Map;

    import backtype.storm.topology.BasicOutputCollector;
    import backtype.storm.topology.OutputFieldsDeclarer;
    import backtype.storm.topology.base.BaseBasicBolt;
    import backtype.storm.tuple.Fields;
    import backtype.storm.tuple.Tuple;
    import backtype.storm.tuple.Values;

    //There are a variety of bolt types. In this case, we use BaseBasicBolt
    public class WordCount extends BaseBasicBolt {
      //For holding words and counts
        Map<String, Integer> counts = new HashMap<String, Integer>();

        //execute is called to process tuples
        @Override
        public void execute(Tuple tuple, BasicOutputCollector collector) {
          //Get the word contents from the tuple
          String word = tuple.getString(0);
          //Have we counted any already?
          Integer count = counts.get(word);
          if (count == null)
            count = 0;
          //Increment the count and store it
          count++;
          counts.put(word, count);
          //Emit the word and the current count
          collector.emit(new Values(word, count));
        }

        //Declare that we will emit a tuple containing two fields; word and count
        @Override
        public void declareOutputFields(OutputFieldsDeclarer declarer) {
          declarer.declare(new Fields("word", "count"));
        }
      }

請用一些時間閱讀程式碼註解，以了解每個 Bolt 的運作方式

### 建立拓撲

拓撲會將 Spout 和 Bolt 一起繫結至圖形，其中定義元件之間的資料流動方式。它也會提供 Storm 在叢集內建立元件的執行個體時所使用的平行處理原則提示。

以下是此拓撲之元件圖的基本圖

![diagram showing the spouts and bolts arrangement](./media/hdinsight-storm-develop-java-topology/wordcount-topology.png)

若要實作拓撲，請在 **src\main\java\com\microsoft\example** 目錄中建立名為 **WordCountTopology.java** 的新檔案。使用下列內容做為檔案的內容。

	package com.microsoft.example;

    import backtype.storm.Config;
    import backtype.storm.LocalCluster;
    import backtype.storm.StormSubmitter;
    import backtype.storm.topology.TopologyBuilder;
    import backtype.storm.tuple.Fields;

    import com.microsoft.example.RandomSentenceSpout;

    public class WordCountTopology {

      //Entry point for the topology
      public static void main(String[] args) throws Exception {
      //Used to build the topology
        TopologyBuilder builder = new TopologyBuilder();
        //Add the spout, with a name of 'spout'
        //and parallelism hint of 5 executors
        builder.setSpout("spout", new RandomSentenceSpout(), 5);
        //Add the SplitSentence bolt, with a name of 'split'
        //and parallelism hint of 8 executors
        //shufflegrouping subscribes to the spout, and equally distributes
        //tuples (sentences) across instances of the SplitSentence bolt
        builder.setBolt("split", new SplitSentence(), 8).shuffleGrouping("spout");
        //Add the counter, with a name of 'count'
        //and parallelism hint of 12 executors
        //fieldsgrouping subscribes to the split bolt, and
        //ensures that the same word is sent to the same instance (group by field 'word')
        builder.setBolt("count", new WordCount(), 12).fieldsGrouping("split", new Fields("word"));

        //new configuration
        Config conf = new Config();
        conf.setDebug(true);

        //If there are arguments, we are running on a cluster
        if (args != null && args.length > 0) {
          //parallelism hint to set the number of workers
          conf.setNumWorkers(3);
          //submit the topology
          StormSubmitter.submitTopology(args[0], conf, builder.createTopology());
        }
        //Otherwise, we are running locally
        else {
          //Cap the maximum number of executors that can be spawned
          //for a component to 3
          conf.setMaxTaskParallelism(3);
          //LocalCluster is used to run locally
          LocalCluster cluster = new LocalCluster();
          //submit the topology
          cluster.submitTopology("word-count", conf, builder.createTopology());
          //sleep
          Thread.sleep(10000);
          //shut down the cluster
          cluster.shutdown();
        }
      }
    }

請用一些時間閱讀程式碼註解，以了解拓撲的定義方式，然後提交至叢集。

## 在本機測試拓撲

儲存檔案之後，請使用下列命令在本機測試拓撲。

	mvn compile exec:java -Dstorm.topology=com.microsoft.example.WordCountTopology

執行時，拓撲會顯示啟動資訊，然後開始顯示與下面類似的行，因為句子是從 Spout 發出，然後由 Bolt 進行處理。

    15398 [Thread-16-split] INFO  backtype.storm.daemon.executor - Processing received message source: spout:10, stream: default, id: {}, [an apple a day keeps thedoctor away]]
    15398 [Thread-16-split] INFO  backtype.storm.daemon.task - Emitting: split default [an]
    15399 [Thread-10-count] INFO  backtype.storm.daemon.executor - Processing received message source: split:6, stream: default, id: {}, [an]
    15399 [Thread-16-split] INFO  backtype.storm.daemon.task - Emitting: split default [apple]
    15400 [Thread-8-count] INFO  backtype.storm.daemon.executor - Processing received message source: split:6, stream: default, id: {}, [apple]
    15400 [Thread-16-split] INFO  backtype.storm.daemon.task - Emitting: split default [a]
    15399 [Thread-10-count] INFO  backtype.storm.daemon.task - Emitting: count default [an, 53]
    15400 [Thread-12-count] INFO  backtype.storm.daemon.executor - Processing received message source: split:6, stream: default, id: {}, [a]
    15400 [Thread-16-split] INFO  backtype.storm.daemon.task - Emitting: split default [day]
    15400 [Thread-8-count] INFO  backtype.storm.daemon.task - Emitting: count default [apple, 53]
    15401 [Thread-10-count] INFO  backtype.storm.daemon.executor - Processing received message source: split:6, stream: default, id: {}, [day]
    15401 [Thread-16-split] INFO  backtype.storm.daemon.task - Emitting: split default [keeps]
    15401 [Thread-12-count] INFO  backtype.storm.daemon.task - Emitting: count default [a, 53]

您可以從此輸出看到發生下列情況：

1. Spout 發出 "an apple a day keeps the doctor away"

2. Split Bolt 開始發出句子中的個別單字

3. Count Bolt 開始發出每個單字和其發出次數

查看 Count Bolt 所發出的資料， 'apple' 已發出 53 次。只要拓撲執行，計數就會持續增加，因為會隨機反覆發出相同的句子，而且永不會重設計數。

## Trident

Trident 是 Storm 所提供且支援可設定狀態處理的高階抽象。Trident 的主要優點是它可以保證進入拓撲的每個訊息只會處理一次 - 這在原始 Java 拓撲中很難達成，但保證訊息至少會處理一次。還有其他差異，例如可以使用的內建元件，而不是建立 Bolt。事實上，Bolt 會完整取代為較不一般的元件 (例如篩選、投影和函數)。

您可以利用上面的相同基本步驟，但只有程式碼不同，並使用 Maven 專案來建立 Trident 應用程式。

如需 Trident 的詳細資訊，請參閱 <a href="http://storm.apache.org/documentation/Trident-API-Overview.html" target="_blank">Trident API 概觀</a>。

如需 Trident 應用程式的範例，請參閱[含 Apache Storm on HDInsight 的 Twitter 趨勢主題](../hdinsight-storm-twitter-trending/)

## 後續步驟

您已經了解如何使用 Java 建立 Storm 拓撲，現在要了解如何：

* [部署和管理 HDInsight 上的 Apache Storm 拓撲](../hdinsight-storm-deploy-monitor-topology/)

* [使用 Visual Studio 開發 Apache Storm on HDInsight 的 C# 拓撲](../hdinsight-storm-develop-csharp-visual-studio-topology/)

* [分析含 Apache Storm on HDInsight 的 Twitter 趨勢主題](../hdinsight-storm-twitter-trending)

<!--HONumber=45--> 
