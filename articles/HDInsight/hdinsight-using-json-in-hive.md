<properties
   pageTitle="利用 HDInsight 中的 Hive 分析和處理 JSON 文件 |Microsoft Azure"
   description="了解如何使用 JSON 文件，以集使用 HDInsight 中的 Hive 分析它們。"
   services="hdinsight"
   documentationCenter=""
   authors="rashimg"
   manager="mwinkle"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="04/14/2015"
   ms.author="rashimg"/>


# 了解如何處理和分析 Hive 中的 JSON 文件

JSON 是 web 上最常使用的格式之一。本教學課程將協助您了解使用者在 Hive 中會面對的常見問題之一 – 如何在 Hive 中使用 JSON 文件，然後對其執行分析。

##JSON 範例

讓我們提出一個範例。假設我們有如下所示的 JSON 文件。我們的目標是能夠剖析此 JSON 文件，就能夠對此文件執行查詢，例如查閱索引鍵上的值、彙總等等。

	  {
        "StudentId": "trgfg-5454-fdfdg-4346",
	    "Grade": 7,
        "StudentDetails": [
          {
            "FirstName": "Peggy",
            "LastName": "Williams",
            "YearJoined": 2012
          }
        ],
        "StudentClassCollection": [
          {
            "ClassId": "89084343",
            "ClassParticipation": "Satisfied",
            "ClassParticipationRank": "High",
            "Score": 93,
            "PerformedActivity": false
          },
          {
            "ClassId": "78547522",
            "ClassParticipation": "NotSatisfied",
            "ClassParticipationRank": "None",
            "Score": 74,
            "PerformedActivity": false
          },
          {
            "ClassId": "78675563",
            "ClassParticipation": "Satisfied",
            "ClassParticipationRank": "Low",
            "Score": 83,
            "PerformedActivity": true
          }
        ] 
      }

##簡維 JSON 文件 (只有當您擁有 pretty JSON 時才需要此步驟)

在我們使用任何 Hive 運算子執行分析之前，我們必須預先處理 JSON 文件，使其可供 Hive 使用。

下一節中所列的所有選項都假設 JSON 文件在單一資料列中。換句話說，我們必須將 JSON 文件簡維成字串，這樣就可以供 Hive 運算子使用。

**如果已簡維 JSON 文件，且整個文件都在一行中，您就可以略過此步驟，直接進入與分析 JSON 資料相關的下一節。**

假設跨越多個資料列的未簡維 JSON 文件出現在 Azure Blob 存放區中預設容器下的 */json/input/* 的資料夾下。以下程式碼會建立資料表 jsonexample，指向原始的未簡維 JSON 文件。

    drop table jsonexample;
    CREATE EXTERNAL TABLE jsonexample (textcol string) stored as textfile location '/json/input/';

接下來，我們要建立新的資料表，稱為 one_line_json，它會指向已簡維的 JSON 文件，並儲存在 Azure Blob 存放區中預設容器下的 */json /temp/* 資料夾下。

    drop table if exists one_line_json;
    create external table one_line_json
    (
      json_body string
    )
    stored as textfile location '/json/temp';

最後，我們將以簡維 JSON 資料填入此資料表。

    insert overwrite table one_line_json
    select concat_ws(' ',collect_list(textcol)) as singlelineJSON 
      from (select INPUT__FILE__NAME,BLOCK__OFFSET__INSIDE__FILE,textcol from jsonexample distribute by INPUT__FILE__NAME sort by BLOCK__OFFSET__INSIDE__FILE ) 
      x group by INPUT__FILE__NAME;

此時，如果您看到新建立的資料表 one_line_json，您應該會看到它包含位於單一行中的 JSON 文件

    select * from one_line_json;

以下是此查詢的輸出：

![JSON 文件簡維。][image-hdi-hivejson-flatten]

##分析 Hive 中 JSON 文件的選項

既然我們已經有位於資料表中單一資料行的 JSON 文件，表示我們已準備好使用 Hive 執行此資料的查詢。Hive 提供三種不同的機制，可在 JSON 文件上執行查詢：

1.	使用使用者定義函式 (UDF)：get_json_object
2.	使用 UDF：json_tuple
3.	使用自訂 SerDe

讓我們一起討論每一種函式的詳細資料。

##get_json_object UDF
Hive 提供內建的 UDF，稱為 [get_json_object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object)，可在執行階段期間執行 JSON 查詢。此方法採用兩個引數 – 資料表名稱和方法名稱，後者具有簡維 JSON 文件和必須剖析的 JSON 欄位。讓我們看看此 UDF 如何運作的範例。

取得每個學生的姓氏與名字

    select get_json_object(one_line_json.json_body, '$.StudentDetails.FirstName'), get_json_object(one_line_json.json_body, '$.StudentDetails.LastName') from one_line_json;

以下是在主控台視窗中執行此查詢時的輸出。

![get_json_object UDF][image-hdi-hivejson-getjsonobject]

此 UDF 有幾項限制。


- 其中一個主要限制是此 UDF 的效能不高，因為查詢中的每個欄位都需要重新剖析查詢，所以會影響效能。
- 其次，get_json_object() 會傳回陣列的字串表示。因此若要將此陣列轉換成 Hive 陣列，您必須使用規則運算式來取代方括號 ‘[‘ 和 ‘]’，然後呼叫分割以取得陣列。


這是 Hive wiki 建議使用我們接下來要討論之 json_tuple 的原因。


##json_tuple UDF

Hive 所提供的另一個 UDF 稱為 [json_tuple](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-json_tuple)，其效能比 [get_json_object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object) 更高。這個方法會採用一組索引鍵和 JSON 字串，並使用一個函式傳回值的 tuple。讓我們看看此 UDF 如何運作的範例。

讓我們從 JSON 文件取得學生識別碼和年級

    select q1.StudentId, q1.Grade 
      from one_line_json jt
      LATERAL VIEW json_tuple(jt.json_body, 'StudentId', 'Grade') q1
        as StudentId, Grade;

我們使用 Hive 中的[橫向檢視](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView)語法，可讓 json_tuple 將 UDT 函式套用到原始資料表的每個資料列，以建立一個虛擬資料表。

我們來看看這個指令碼在 Hive 主控台的輸出：

![json_tuple UDF][image-hdi-hivejson-jsontuple]

此 UDF 的主要缺點之一是複雜 JSONs 重複使用橫向檢視，因此變得太不便使用。事實上，此 UDF 無法處理巢狀 JSON。

##使用自訂 SerDe

若要剖析巢狀 JSON 文件，SerDe 是**最佳的選擇**，因為您可以定義 JSON 結構描述，這樣就可以很容易對它執行查詢。

我們將討論如何使用其中一個已由 [rcongiu](https://github.com/rcongiu) 開發的熱門 SerDe。

步驟 1：確定您已安裝 [Java SE Development Kit 7u55 JDK 1.7.0_55](http://www.oracle.com/technetwork/java/javase/downloads/java-archive-downloads-javase7-521261.html#jdk-7u55-oth-JPR) (請注意：如果您已安裝 JDK 1.8，它將無法與此 SerDe 一起運作)。


- 如果您要使用 HDInsight 的 Windows 部署，請選擇 x64 Windows 版本的 JDK。
- 安裝完成之後，請移至 [控制台] --> [新增環境] 變數，並新增 JAVA_HOME 環境，變數會指向 C:\Program Files\Java\jdk1.7.0_55 或安裝 JDK 的任何位置。下列螢幕擷取畫面顯示如何設定環境變數。

![為 JDK 設定正確的組態值][image-hdi-hivejson-jdk]

步驟 2：移至[這個](http://mirror.olnevhost.net/pub/apache/maven/maven-3/3.3.1/binaries/apache-maven-3.3.1-bin.zip)連結並下載 Maven 3.3.1。解壓縮您想要儲存二進位檔所在的封存。在我的案例中，我會將它解壓縮至 C:\Program Files\Maven。移至 [控制台] --> [編輯帳戶的系統變數]  環境變數，即可新增 bin 資料夾至您的路徑。以下螢幕擷取畫面顯示如何執行此動作。

![設定 Maven][image-hdi-hivejson-maven]

步驟 3：從 [Hive-JSON-SerDe](https://github.com/sheetaldolas/Hive-JSON-Serde/tree/master) github 網站複製專案。您可以按一下 [下載 Zip] 按鈕即可完成，如以下螢幕擷取畫面所示。

![複製專案][image-hdi-hivejson-serde]

步驟 4：移至您已下載此封裝和類型「mvn 封裝」的資料夾。這樣應該會建立您稍後會複製到叢集的必要 jar 檔案。

步驟 5：接下來，請移至您下載封裝之根資料夾下的目標資料夾。上傳 json-serde-1.1.9.9-Hive13-jar-with-dependencies.jar 檔案到叢集的前端節點。我通常會將它放在 hive 二進位資料夾底下：C:\apps\dist\hive-0.13.0.2.1.11.0-2316\bin 或類似位置。
 
步驟 6：在 hive 提示字元中，輸入 “add jar /path/to/json-serde-1.1.9.9-Hive13-jar-with-dependencies.jar”。因為在我的案例中，jar 位於 C:\apps\dist\hive-0.13.x\bin 資料夾中，所以我可以直接以此名稱新增 jar，如下所示：

    add jar json-serde-1.1.9.9-Hive13-jar-with-dependencies.jar;

![將 JAR 新增至您的專案][image-hdi-hivejson-addjar]

現在，我們已準備好使用 SerDe 對 JSON 文件執行查詢。

讓我們先建立附帶 JSON 文件結構描述的資料表。請注意，我們可以處理更豐富的類型，包括對應、陣列和結構。在 Hive 主控台中輸入下列程式碼，讓 Hive 建立名為 json_table 的資料表，可以根據下方的結構描述讀取 JSON 文件。

    drop table json_table;
    create external table json_table (
      StudentId string, 
      Grade int,
      StudentDetails array<struct<
          FirstName:string,
          LastName:string,
          YearJoined:int
          >
      >,
      StudentClassCollection array<struct<
          ClassId:string,
          ClassParticipation:string,
          ClassParticipationRank:string,
          Score:int,
          PerformedActivity:boolean
          >
      >
    ) row format serde 'org.openx.data.jsonserde.JsonSerDe'
    location '/json/temp';

定義了結構描述之後，讓我們來執行一些範例，看看我們如何使用 Hive 查詢 JSON 文件：

a) 列出學生的姓氏與名字

    select StudentDetails.FirstName, StudentDetails.LastName from json_table;

以下是 Hive 主控台的結果。

![SerDe 查詢 1][image-hdi-hivejson-serde_query1]

b) 此查詢會計算 JSON 文件的分數總和

    select sum(scores)
    from json_table jt
      lateral view explode(jt.StudentClassCollection.Score) collection as scores;
       
上述查詢會使用[橫向檢視切割](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView) UDF 展開分數的陣列，讓它們可以加總。

以下是 Hive 主控台的輸出。

![SerDe 查詢 2][image-hdi-hivejson-serde_query2]

c) 尋找特定學生分數超過 80 分的科目，選取 jt.StudentClassCollection.ClassId from json_table jt 橫向檢視分割 (jt.StudentClassCollection.Score) 集合做為分數，其中分數 > 80；
      
上述查詢傳回的 Hive 陣列與傳回字串的 get_json_object 不同。

![SerDe 查詢 3][image-hdi-hivejson-serde_query3]

如果您想要刪除格式不正確的 JSON，然後依照此 SerDe 之 [wiki 頁面](https://github.com/sheetaldolas/Hive-JSON-Serde/tree/master) 的說明內容，您可以輸入下列程式碼即可達成：

    ALTER TABLE json_table SET SERDEPROPERTIES ( "ignore.malformed.json" = "true");


##其他選項
除了下面所列的選項之外，您也可以使用 Python 或其他專屬於您的案例的語言，撰寫自己的自訂程式碼。您備妥 python 指令碼之後，請參閱[本文章][hdinsight-python]以了解如何利用 Hive 執行您自己的 Python 程式碼。

##摘要
總而言之，您在 Hive 中選擇的 JSON 運算子類型取決於您的案例。如果您有一個簡單的 JSON 文件，且您只需要查閱一個欄位 – 您就可以選擇使用 Hive UDF get_json_object。如果您有多個索引鍵需要查閱，則您可以使用 json_tuple。如果您有巢狀文件，您應該使用 JSON SerDe。

HDInsight 致力於讓您在 Hive 中更輕鬆地使用更多原生的格式，而不需花費大量的心力。如果我們有更多分享內容，我們會利用詳細資料更新本教學課程。

[hdinsight-python]: hdinsight-python.md

[image-hdi-hivejson-flatten]: ./media/hdinsight-using-json-in-hive/flatten.png
[image-hdi-hivejson-getjsonobject]: ./media/hdinsight-using-json-in-hive/getjsonobject.png
[image-hdi-hivejson-jsontuple]: ./media/hdinsight-using-json-in-hive/jsontuple.png
[image-hdi-hivejson-jdk]: ./media/hdinsight-using-json-in-hive/jdk.png
[image-hdi-hivejson-maven]: ./media/hdinsight-using-json-in-hive/maven.png
[image-hdi-hivejson-serde]: ./media/hdinsight-using-json-in-hive/serde.png
[image-hdi-hivejson-addjar]: ./media/hdinsight-using-json-in-hive/addjar.png
[image-hdi-hivejson-serde_query1]: ./media/hdinsight-using-json-in-hive/serde_query1.png
[image-hdi-hivejson-serde_query2]: ./media/hdinsight-using-json-in-hive/serde_query2.png
[image-hdi-hivejson-serde_query3]: ./media/hdinsight-using-json-in-hive/serde_query3.png
[image-hdi-hivejson-serde_result]: ./media/hdinsight-using-json-in-hive/serde_result.png
 

<!---HONumber=62-->