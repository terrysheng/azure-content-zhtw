<properties linkid="manage-services-hdinsight-recommendation-engine-using-mahout" urlDisplayName="Hadoop Recommendation Engine" pageTitle="Hadoop recommendation engine (.NET) | Azure" metaKeywords="Azure Apache Mahout, Azure recommendation example, Azure recommendation tutorial, Azure recommendation engine" description="A tutorial that teaches how to use the Apache Mahout recommendation engine with Azure to create song suggestions based on listening habits." disqusComments="1" umbracoNaviHide="1" title="Simple recommendation engine using Apache Mahout" authors="jgao" manager="paulettm" editor="cgronlun" />

使用 Apache Mahout 的簡單建議引擎
=================================

Apache Mahout™ 是針對用於可延展機器學習應用程式所建置的機器學習庫。建議者引擎是目前最常用的其中一種機器學習應用程式類型，而且有許多顯著的行銷應用程式。

Apache Mahout 提供項目型共同作業篩選的內建實作。此方式廣泛用來進行建議資料採礦。項目型共同作業篩選是由 Amazon.com 所開發。這裡的想法是可以利用使用者喜好設定 (呈現項目喜好設定之間的相互關聯) 上的資料來猜想類似群組之未來使用者的喜好。

在本教學課程中，您使用 [Million Song Dataset](http://labrosa.ee.columbia.edu/millionsong/tasteprofile) (英文) 網站，並下載[資料集](http://labrosa.ee.columbia.edu/millionsong/sites/default/files/challenge/train_triplets.txt.zip) (英文) 以根據使用者過去的聽歌習慣來建立使用者的歌曲建議。

您將了解：

-   如何使用建議引擎

本教學課程包含下列區段：

1.  [設定和組態](#setup)
2.  [檢查和格式化資料](#segment1)
3.  [安裝 Mahout](#Segment2)
4.  [執行 Mahout 工作](#segment2)

設定和組態
----------

本教學課程假設您已設定 Azure 和 HDinsight Preview，而且您已建立可在其上執行範例的 HDInsight 叢集。如果您尚未這麼做，請參閱[開始使用 Azure HDInsight](/en-us/manage/services/hdinsight/get-started-hdinsight/) 教學課程，以取得如何滿足這些必要條件檢查的指示。

檢查和格式化資料
----------------

此範例處理使用者表達對特定歌曲喜好的方式。假設使用者聽歌的次數是一種度量，表示該使用者對歌曲的喜好。您可以使用在喜好設定資料中偵測到的模式，根據使用者的一些表達出來的音樂喜好來預測未來使用者的喜好。您可以在 [Echo Nest Taste Profile Subset](http://labrosa.ee.columbia.edu/millionsong/tasteprofile) 網頁 (英文) 的＜說明＞****(英文) 小節中檢視此資料集的範例：

![The Echo Nest Taste Profile Subset](./media/hdinsight-hadoop-recommendation-engine/the-echo-nest-taste-profile-subset.png)

### Million Song Dataset 中的範例資料

若要搭配使用此資料集與 Mahout，您需要執行兩件事：

1.  將歌曲和使用者的 ID 轉換為整數值。
2.  將新值與其排名儲存至逗點分隔檔。

如果您未安裝 Visual Studio 2010，請跳過此步驟，並移至＜執行 Mahout 工作＞小節以取得預先產生的版本。

請啟動 Visual Studio 2010 來開始使用。在 Visual Studio 中，請選取 [檔案] -\> [新增] -\> **[專案]**。在 **[已安裝的範本]** 窗格的 **[Visual C\#]** 節點內，選取 **[視窗]** 類別，然後從清單中選取 **[主控台應用程式]**。將專案命名為 "ConvertToMahoutInput"，然後按一下 **[確定]** 按鈕。

![creating a console application](./media/hdinsight-hadoop-recommendation-engine/creating-a-console-application.png)

### 建立主控台應用程式

1.  建立應用程式之後，請開啟 **Program.cs** 檔案，並將下列靜態成員新增至 **Program** 類別：

        const char tab = '\u0009';
        static Dictionary<string, int> usersMapping = new Dictionary<string, int>();
        static Dictionary<string, int> songMapping = new Dictionary<string, int>(); 

2.  接著，新增 `using System.IO;` 陳述式，並將下列程式碼填入 **Main** 方法中：

        var inputStream = File.Open(args[0], FileMode.Open);
        var reader = new StreamReader(inputStream);

        var outStream = File.Open("mInput.txt", FileMode.OpenOrCreate);
        var writer = new StreamWriter(outStream);

        var i = 1;

        var line = reader.ReadLine();
        while (!string.IsNullOrWhiteSpace(line))
        {

            i++;
            if (i \> 5000)
            break;
            var outLine = line.Split(tab);

            int user = GetUser(outLine[0]); 
            int song = GetSong(outLine[1]);

            writer.Write(user);
            writer.Write(',');
            writer.Write(song);
            writer.Write(',');
            writer.WriteLine(outLine[2]);

            line = reader.ReadLine();

        }

        Console.WriteLine("saved {0} lines to {1}", i, args[0]);

        reader.Close();
        writer.Close();

        SaveMapping(usersMapping, "users.csv");
        SaveMapping(songMapping, "mInput.csv");

        Console.WriteLine("Mapping saved");
        Console.ReadKey();

3.  現在，請建立 **GetUser** 和 **GetSong** 函數 (可將 ID 轉換為整數)：

        static int GetUser(string user)
        {

            if (!usersMapping.ContainsKey(user))
                usersMapping.Add(user, usersMapping.Count + 1);

            return usersMapping[user];
        }

        static int GetSong(string song)
        {
            if (!songMapping.ContainsKey(song))
                songMapping.Add(song, songMapping.Count + 1);

            return songMapping[song];
        }

4.  最後，建立可實作 SaveMapping 方法的公用程式，而 SaveMapping 方法會將 Bot 對應字典儲存至 .csv 檔案。

        static void SaveMapping(Dictionary<string, int> mapping, string fileName)
        {
            var stream = File.Open(fileName, FileMode.Create);
            var writer = new StreamWriter(stream);

            foreach (var key in mapping.Keys)
            {
                writer.Write(key);
                writer.Write(',');
                writer.WriteLine(mapping[key]);
            }

            writer.Close();
        }

5.  從[此連結](http://labrosa.ee.columbia.edu/millionsong/sites/default/files/challenge/train_triplets.txt.zip)下載範例資料。下載之後，請開啟 **train\_triplets.txt.zip**，並解壓縮 **train\_triplets.txt**。

    執行此公用程式時，請包括含有 **train\_triplets.txt** 位置的命令列引數。若要這麼做，請在 **[方案總管]** 的 **[ConvertToMahoutInput]** 專案節點上按一下滑鼠右鍵，然後選取 **[屬性]**。在專案屬性頁面上，選取左側的 **[偵錯]** 索引標籤，然後將 &lt;localpath\>train\_triplets.txt 的路徑新增至 **[命令列引數]** 文字方塊：

    ![setting command line arguments](./media/hdinsight-hadoop-recommendation-engine/setting-command-line-arguments.png)

### 設定命令列引數

-   按 **F5** 鍵執行程式。完成之後，請從專案儲存位置中開啟 **bin\\Debug** 資料夾，並檢視公用程式的輸出。您應該會發現 users.txt 和 mInput.txt。

安裝 Mahout
-----------

-   開啟 HDInsight 叢集連接埠，然後按一下 **[遠端桌面]** 圖示。

    ![The Manage Cluster Icon](./media/hdinsight-hadoop-recommendation-engine/the-manage-cluster-icon.png)

### 遠端桌面圖示

HDInsight 預設不會包括 Mahout。但是，因為它是 Hadoop 生態系統的一部分，所以可以從 [Mahout](http://mahout.apache.org/) 網站進行下載。最新版本是 0.7，但是這組指示與 0.5 或 0.7 版相容。

1.  首先，將 [Mahout 0.7 版](http://www.apache.org/dyn/closer.cgi/mahout/) (英文) 下載至您的本機電腦。

2.  接著，將它複製至叢集，方法是選取本機 ZIP 檔案，並按 Ctrl+V 進行複製，然後將它貼到 Hadoop 叢集。

    ![Uploading Mahout](./media/hdinsight-hadoop-recommendation-engine/uploading-mahout.PNG)

### 將 Mahout 複製至前端節點

1.  最後，複製程序完成之後，請在 ZIP 檔案上按一下滑鼠右鍵，並將 Mahout 配送解壓縮至 C:\\apps\\dist。您現在已在 C:\\apps\\dist\\mahout-distriution-0.7 中安裝 mahout。

2.  為求簡化，請將資料夾重新命名為 c:\\apps\\dist\\mahout-0.7。

### 執行 Mahout 工作

1.  將 **mInput.txt** 檔案從 **bin\\Debug** 資料夾複製至遠端叢集上的 **c:\\**。複製此檔案之後，請將它解壓縮。如上一節所述，將檔案複製至遠端 RDP 工作階段的方式，是在選取檔案之後，在本機電腦上按 Ctrl+C，然後按 Ctrl+V 貼到 RDP 工作階段視窗。

2.  建立檔案，內含將為其產生建議的使用者 ID。若要這樣做，只需要在 **c:\\** 中建立稱為 **users.txt** 的文字檔 (內含單一使用者的 ID)。

**注意**

將使用者的 ID 放到不同的行，就可以產生更多使用者的建議。如果您在產生 mInput.txt 和 users.txt 時發生問題，則可能是下載此 github [儲存機制](https://github.com/wenming/BigDataSamples/tree/master/mahout) (英文) 上的預先產生版本。 最方便的方式是將所有項目下載為一個 [ZIP 檔案](https://github.com/wenming/BigDataSamples/archive/master.zip) (英文)。請尋找 users.txt 和 mInput.txt，並將它們複製至資料夾 c:\\ 中的遠端叢集。

此時，您應該開啟 Hadoop 終端機視窗，並瀏覽到含有 users.txt 和 mInput.txt 的資料夾。

![Mahout command window](./media/hdinsight-hadoop-recommendation-engine/mahout-commandwindow.PNG)

### Hadoop 命令視窗

1.  接著，將 **mInput.txt** 和 **users.txt** 複製至 HDFS。若要這樣做，請開啟 **[Hadoop Command Shell]**，然後執行下列命令：

        hadoop dfs -copyFromLocal c:\mInput.txt input\mInput.txt
        hadoop dfs -copyFromLocal c:\users.txt input\users.txt

2.  驗證檔案已複製至 HDFS：

        hadoop fs -ls input/

    這應該會顯示：

        Found 2 items
        -rwxrwxrwx   1 writer supergroup      53322 2013-03-08 20:32 /user/writer/input/mInput.txt
        -rwxrwxrwx   1 writer supergroup        353 2013-03-08 20:33 /user/writer/input/users.txt

3.  現在，我們可以使用下列命令來執行 Mahout 工作：

        c:\apps\dist\mahout-0.7\bin>hadoop jar c:\Apps\dist\mahout-0.7\mahout-core-0.7-job.jar org.apache.mahout.cf.taste.hadoop.item.RecommenderJob -s SIMILARITY_COOCCURRENCE --input=input/mInput.txt --output=output --usersFile=input/users.txt

    建議引擎可以使用許多其他 "distance" 函數來比較不同使用者的功能因素，因此您可能會試驗 Similarity 類別，和將其變更為 SIMILARITY\_COOCCURRENCE、SIMILARITY\_LOGLIKELIHOOD、SIMILARITY\_TANIMOTO\_COEFFICIENT、SIMILARITY\_CITY\_BLOCK、SIMILARITY\_COSINE、SIMILARITY\_PEARSON\_CORRELATION、SIMILARITY\_EUCLIDEAN\_DISTANCE。基於本教學課程的目的，我們不會討論 Mahout 的詳細資料科學部分。

4.  建立輸出檔案之後，Mahout 工作應該會執行數分鐘。請執行下列命令來建立輸出檔案的本機複本：

        hadoop fs -copyToLocal output/part-r-00000 c:\output.txt

5.  從 **c:\\** 根資料夾中開啟 **output.txt** 檔案，然後檢查其內容。檔案的結構如下：

        user    [song:rating,song:rating, ...]

6.  如果您想要在叢集上使用 Mahout 的其他部分，則應該將 Mahout.cmd 的複本儲存至 Mahout 配送的 bin 目錄中。

摘要
----

建議者引擎提供許多現代社交網路網站、線上購物、串流媒體和其他網際網路網站的重要功能。Mahout 提供的預設建議引擎簡單易用、包含許多有用的功能，而且可以在 Hadoop 上延展。

後續步驟
--------

雖然本文示範如何使用 Hadoop 命令列，但是您還是可以使用 HDInsight 互動式主控台來執行工作。如需詳細資訊，請參閱 [指引：HDInsight 互動式 JavaScript 和 Hive 主控台][interactive-console]。

