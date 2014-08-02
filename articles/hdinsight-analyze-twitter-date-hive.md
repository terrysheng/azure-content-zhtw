<properties linkid="manage-services-hdinsight-howto-social-data" urlDisplayName="Analyze Data with HDInsight" pageTitle="Analyzing Twitter data with Hive - Windows Azure Services" metaKeywords="" description="In this tutorial you will query, explore, and analyze data from Twitter using the HDInsight Service for Windows Azure and a complex Hive example." metaCanonical="" services="" documentationCenter="" title="Analyzing Twitter Data with Hive" authors="" solutions="" writer="wenming" manager="" editor="" />

使用 Hive 來分析 Twitter 資料
=============================

本教學課程中，您將使用以 Apache Hadoop 為基礎的 Windows Azure HDInsight 服務和一個複雜的 Hive 範例，以查詢、探索和分析來自 Twitter 的資料。社群網站是驅使採用「巨量資料」的其中一個主要動力。像 Twitter 之類的網站所提供的公開 API，是分析和了解流行趨勢的一項實用的資料來源。本教學課程假設已透過 [Windows Azure 入口網站](http://www.windowsazure.com)建立 HDInsight 叢集。

本教學課程包含下列幾節：

1.  [使用 cURL 和 Twitter 串流 API 來取得 Twitter 摘要](#segment1)
2.  [使用 Hive 以互動方式處理 Twitter 資料](#segment2)

## 使用 cURL 和 Twitter 串流 API 來取得 Twitter 摘要

1.  此步驟需要 curl.exe。請根據您的 OS 從 <http://curl.haxx.se/download.html> 下載適合的 curl 檔案 (例如 Win64 二進位 SSL)。

    ![Figure3](./media/hdinsight-analyze-twitter-date-hive/Figure3.png)

2.  將 **curl.exe** 解壓縮到適當的位置 (例如 **C:\\twitterdata**)

3.  在與 **curl.exe** 相同的資料夾中，使用記事本建立 **get\_twitter\_stream.cmd** 和 **twitter\_params.txt** 這兩個檔案，如下所示：

    ![Figure4](./media/hdinsight-analyze-twitter-date-hive/Figure4.png)

4.  編輯 **twitter\_params.txt** 檔案來追蹤您的推文，如下所示：

        track=weather,Azure,WindowsAzure,cloud

    您可以使用您選擇的任何主題或雜湊標籤。

5.  編輯 **get\_twitter\_stream.cmd** 視窗命令指令碼，在下列這一行中，以您的 twitter 使用者名稱來取代 **USER**，以您的密碼來取代 **PASSWORD**：

        curl -d @twitter_parameters.txt -k https://stream.twitter.com/1/statuses/filter.json -u user:{password} >>twitter_stream_seq2.txt

6.  從命令提示字元中，執行 get\_twitter\_stream.cmd 指令碼，如下所示：

    ![Figure5](./media/hdinsight-analyze-twitter-date-hive/Figure5.png)

    您應該會看到如下的結果：

    ![Figure6](./media/hdinsight-analyze-twitter-date-hive/Figure6.png)

7.  您可以按 **Ctrl+C** 來停止工作。然後，您可以重新命名檔案，再重新啟動指令碼。您可以任由程序執行 10 分鐘，甚至數小時。基於本教學課程的目的，請將資料大小限制在幾百 MB 以下。

    Twitter 是以巢狀結構複雜的 JSon 格式儲存。在下一個步驟中，我們不使用慣用的程式設計語言來撰寫太多行程式碼，而是將此巢狀結構轉換成 Hive 資料表，以利用 HQL 這種類似 SQL 的語言來以互動方式來查詢資料表。

## 使用 Hive 以互動方式處理 Twitter 資料

1.  趁著收集推文時，對您已透過 Windows Azure 入口網站建立的 HDInsight 叢集建立 RDP 工作階段。

    **瀏覽**至 HDInsight，**選取**您已建立的叢集。按一下畫面底部的 **[Connect RDP]** 圖示。輸入密碼來登入 RDP 工作階段。登入之後，**開啟**檔案總管視窗並瀏覽至 c:。

    ![Connect using RDP Icon](./media/hdinsight-analyze-twitter-date-hive/twitter-RDPconnect.PNG)

2.  按 **Ctrl+C** 終止 Curl 推文收集程序，並使用 Windows 檔案總管來瀏覽到 twitter 檔案所在的位置。**按一下滑鼠右鍵**以傳送到 [壓縮的 (Zipped 資料夾)]。這樣可節省上傳時間。

3.  現在，在本機電腦的 zip 檔案上按 **Ctrl-C**，然後在遠端桌面工作階段的 C: 上瀏覽至 c:\\。按一下檔案總管 Window (C:) 之後，按 **Ctrl-V**透過 RDP 工作階段上傳 zip 檔案。

    ![Uploading tweets through RDP Session](./media/hdinsight-analyze-twitter-date-hive/twitter-uploadingzip.PNG)

4.  檔案上傳之後，**按一下滑鼠右鍵**，**選取** [解壓縮全部] 至 c:\\，以取回原始的文字檔。**開啟** Hadoop 命令列視窗，開始使用 HIVE 和 Hadoop 命令。

    ![Figure26](./media/hdinsight-analyze-twitter-date-hive/Figure26.png)

5.  第一步是**輸入** C:\\，然後**按 Enter**。這樣會進入 twitter 資料所在的 c:\\ 資料夾。

6.  接下來，在 HDFS (全名為 Hadoop Distributed File System) 中建立資料夾，然後使用 -copyFromLocal 參數，將 twitter 資料複製到我們剛建立的資料夾：

        hadoop fs -mkdir /example/data
        hadoop fs -copyFromLocal c:\twitter_stream_seq2.txt /example/data/

7.  現在，原始 twitter 資料已複製到 HDInsight 叢集的 HDFS 中。下一步是為我們已下載的資料建立簡單的資料表結構。這是暫時的 Hive 結構化資料表，可讓我們保留資料和執行進一步的 ETL 處理。開啟記事本，將下列程式碼貼到記事本並儲存為："c:\\load\_twitter\_raw.hql"

        drop table twitter_raw;

        create table twitter_raw (
            json_response string
        ) 
        partitioned by (filesequence int);

        load data inpath '/example/data/twitter_stream_seq2.txt'
        into table twitter_raw
        partition (filesequence = 1);

8.  儲存檔案之後，在命令視窗中執行 Hive：

        C:\apps\dist\hive-0.9.0\bin\hive -f load_twitter_raw.hql

9.  此程序需要幾秒鐘才會完成：

    ![Twitter load raw results](./media/hdinsight-analyze-twitter-date-hive/twitter-load-raw-results.PNG)

10. 巢狀 JSon 格式的大型 twitter 資料集現在已轉換成暫時的 Hive 資料表結構。

11. 我們必須先執行另一個 ETL 程序，才能使用 Hive 來查詢 twitter 資料集。對於我們已儲存在 "twitter\_raw" 資料表中的資料，我們將定義更詳細的資料表結構描述。這個更複雜的 ETL 程序會花費更久的時間。在命令提示字元中，再次啟動記事本來貼上下列 Hive 查詢程式碼，然後儲存為 "c:\\twitter\_etl.hql.txt"。

		set hive.exec.dynamic.partition = true;
		set hive.exec.dynamic.partition.mode=nonstrict;

		drop table twitter_temp;

		create table twitter_temp
		(
			id bigint,
			created_at string,
			created_at_date string,
			created_at_year string,
			created_at_month string,
			created_at_day string,
			created_at_time string,
			in_reply_to_user_id_str string,
			text string,
			contributors string,
			retweeted string,
			truncated string,
			coordinates string,
			source string,
			retweet_count int,
			url string,
			hashtags array<string>,
			user_mentions array<string>,
			first_hashtag string,
			first_user_mention string,
			screen_name string,
			name string,
			followers_count int,
			listed_count int,
			friends_count int,
			lang string,
			user_location string,
			time_zone string,
			profile_image_url string,
			json_response string
		)
		partitioned by (filesequence int);

		from twitter_raw
		insert overwrite table twitter_temp
		partition (filesequence)
		select
			cast(get_json_object(json_response, '$.id_str') as bigint),

			get_json_object(json_response, '$.created_at'),
			concat(substr (get_json_object(json_response, '$.created_at'),1,10),' ',
				substr (get_json_object(json_response, '$.created_at'),27,4)),

			substr (get_json_object(json_response, '$.created_at'),27,4),

			case substr (get_json_object(json_response, 	'$.created_at'),5,3)
				when "Jan" then "01"
				when "Feb" then "02"
				when "Mar" then "03"
				when "Apr" then "04"
				when "May" then "05"
				when "Jun" then "06"
				when "Jul" then "07"
				when "Aug" then "08"
				when "Sep" then "09"
				when "Oct" then "10"
				when "Nov" then "11"
				when "Dec" then "12" end,

			substr (get_json_object(json_response, '$.created_at'),9,2),

			substr (get_json_object(json_response, '$.created_at'),12,8),

			get_json_object(json_response, '$.in_reply_to_user_id_str'),
			get_json_object(json_response, '$.text'),
			get_json_object(json_response, '$.contributors'),
			get_json_object(json_response, '$.retweeted'),
			get_json_object(json_response, '$.truncated'),
			get_json_object(json_response, '$.coordinates'),
			get_json_object(json_response, '$.source'),
			cast (get_json_object(json_response, '$.retweet_count') as int),
			get_json_object(json_response, '$.entities.display_url'),
			array(	
				trim(lower(get_json_object(json_response, '$.entities.hashtags[0].text'))),
				trim(lower(get_json_object(json_response, '$.entities.hashtags[1].text'))),
				trim(lower(get_json_object(json_response, '$.entities.hashtags[2].text'))),
				trim(lower(get_json_object(json_response, '$.entities.hashtags[3].text'))),
				trim(lower(get_json_object(json_response, '$.entities.hashtags[4].text')))),
			array(
				trim(lower(get_json_object(json_response, '$.entities.user_mentions[0].screen_name'))),
				trim(lower(get_json_object(json_response, '$.entities.user_mentions[1].screen_name'))),
				trim(lower(get_json_object(json_response, '$.entities.user_mentions[2].screen_name'))),
				trim(lower(get_json_object(json_response, '$.entities.user_mentions[3].screen_name'))),
				trim(lower(get_json_object(json_response, '$.entities.user_mentions[4].screen_name')))),
			trim(lower(get_json_object(json_response, '$.entities.hashtags[0].text'))),
			trim(lower(get_json_object(json_response, '$.entities.user_mentions[0].screen_name'))),
			get_json_object(json_response, '$.user.screen_name'),
			get_json_object(json_response, '$.user.name'),
			cast (get_json_object(json_response, '$.user.followers_count') as int),
			cast (get_json_object(json_response, '$.user.listed_count') as int),
			cast (get_json_object(json_response, '$.user.friends_count') as int),
			get_json_object(json_response, '$.user.lang'),
			get_json_object(json_response, '$.user.location'),
			get_json_object(json_response, '$.user.time_zone'),
			get_json_object(json_response, '$.user.profile_image_url'),
			json_response,
			filesequence
		where (length(json_response) > 500);

12. 現在輸入：

        C:\apps\dist\hive-0.9.0\bin\hive -f twitter_etl.hql.txt

13. 這個複雜的 Hive 指令碼會啟動一組冗長的 Map Reduce 工作 (由 Hadoop 叢集執行)。根據資料集和叢集大小而定，這可能需要 10 分鐘。

    ![twitter complex etl](./media/hdinsight-analyze-twitter-date-hive/twitter-etl-complex.PNG)

14. 您也可以按兩下桌面上的捷徑 [Hadoop MapReduce 狀態]，以透過前端節點的工作追蹤頁面來監視工作進度。

    ![Monitoring and Tracking jobs](./media/hdinsight-analyze-twitter-date-hive/twitter_longjob_browser.PNG)

15. ETL 程序完成之後，啟動 Hive：

        C:\apps\dist\hive-0.9.0\bin\hive

16. 您可以試驗一下簡單的 Hive 查詢，例如：

        select name, screen\_name, count(1) as cc from twitter\_temp where text like "%Azure%" group by name,screen\_name order by cc desc limit 10;

1.  此查詢會傳回一份 twitter 使用者清單，這些使用者傳送含有 "Azure" 這個字的最多推文。

    ![Final Result](./media/hdinsight-analyze-twitter-date-hive/twitter_final_query_result.PNG)

## 摘要

本教學課程中，我們看到如何將非結構化 Json 資料集轉換成結構化 Hive 資料表，然後在 Windows Azure 上使用 HDInsight 來查詢、探索和分析來自 Twitter 的資料。您可以在[這裡](https://github.com/wenming/BigDataSamples/tree/master/twittersample)的 GitHub 儲存機制中找到更新和支援檔案。

後續步驟
--------

雖然本文使用 Hadoop 命令列做示範，但您也可以使用 HDInsight 服務互動式主控台來執行工作。如需詳細資訊，請參閱 [指引：HDInsight 互動式 JavaScript 和 Hive 主控台][互動式主控台]。

