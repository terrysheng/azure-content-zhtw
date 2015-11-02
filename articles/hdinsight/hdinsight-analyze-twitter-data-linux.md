<properties
	pageTitle="在 HDInsight 上使用 Apache Hive 分析 Twitter 資料 | Microsoft Azure"
	description="了解如何使用 Python 來儲存包含特定關鍵字的推文，然後在 HDInsight 上使用 Hive 和 Hadoop 將未經處理的 TWitter 資料轉換成可搜尋的 Hive 資料表。"
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="paulettm"
	editor="cgronlun"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/19/2015"
	ms.author="larryfr"/>

# 在 HDInsight 中使用 Hive 分析 Twitter 資料

在本文件中，您將會使用 Twitter 串流 API 取得推文，然後在以 Linux 為基礎的 HDInsight 叢集上使用 Apache Hive 處理 JSON 格式化的資料。結果會是傳送最多包含特定文字推文的 Twitter 使用者清單。

> [AZURE.NOTE]本文章中的個別部分適用於以 Windows 為基礎的 HDInsight 叢集 (例如 Python 與 Hive)，許多步驟則適用於以 Linux 為基礎的 HDInsight 叢集。如需 Windows 為基礎的叢集的特定步驟，請參閱[在 HDInsight 中使用 Hive 分析 Twitter 資料](hdinsight-analyze-twitter-data.md)。

###必要條件

開始進行本教學課程之前，您必須具備下列條件：

- __以 Linux 為基礎的 HDInsight 叢集__。如需建立叢集的相關資訊，請參閱[開始使用以 Linux 為基礎的 HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md) 以取得建立叢集的步驟。

- __SSH 用戶端__。如需搭配使用 SSH 與以 Linux 為基礎的 HDInsight 的詳細資訊，請參閱下列文章：

	* [從 Linux、Unix 或 OS X 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-unix.md)

	* [從 Windows 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-windows)

- __Python__ 與 [pip](https://pypi.python.org/pypi/pip) (英文)

- __Azure CLI__。如需詳細資訊，請參閱[安裝和設定 Azure CLI](../xplat-cli-install.md)

##取得 Twitter 摘要

Twitter 可讓您透過 REST API 抓取[每則推文資料](https://dev.twitter.com/docs/platform-objects/tweets) (英文)，做為 JavaScript 物件標記法 (JSON)。API 驗證需要 [OAuth](http://oauth.net) (英文)。您也必須建立包含用來存取 API 之設定的 _Twitter 應用程式_。

###建立 Twitter 應用程式

1. 從網頁瀏覽器登入 [https://apps.twitter.com/](https://apps.twitter.com/)。如果您沒有 Twitter 帳戶，請按一下 [**立即註冊**] 連結。
2. 按一下 [建立新的應用程式]。
3. 輸入 [名稱]、[說明]、[網站]。您可以在 [**網站**] 欄位中自行設定 URL。下表列出部分要使用的範例值：

	| 欄位 | 值 |
	|:----- |:----- |
	| 名稱 | MyHDInsightApp |
	| 說明 | MyHDInsightApp |
	| 網站 | http://www.myhdinsightapp.com |
	
4. 核取 [Yes, I agree]然後按一下 [Create your Twitter application]。
5. 按一下 [權限] 索引標籤。預設權限為 [唯讀]。本教學課程使用預設值即可。
6. 按一下 **[金鑰和存取權杖**] 索引標籤。
7. 按一下 [Create my access token]。
8. 按一下位於頁面右上角的 [**測試 OAuth**]。
9. 記下**消費者金鑰**、**消費者密碼**、**存取權杖**和**存取權杖密碼**。您稍後將需要這些值。

>[AZURE.NOTE]在 Windows 上使用 curl 命令時，對選項值請使用雙引號，而不要使用單引號。

###下載的推文

下列 Python 程式碼會從 Twitter 下載 10,000 則推文，並儲存到名為 __tweets.txt__ 的檔案。

> [AZURE.NOTE]由於已安裝 Python，下列步驟會在 HDInsight 叢集上執行。

1. 使用 SSH 連線到 HDInsight 叢集

		ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
		
	如果您已經使用密碼保護您 SSH 使用者帳戶的安全，系統會提示您輸入密碼。如果您使用的是公開金鑰，您可能必須使用 `-i` 參數來指定對應的私密金鑰。例如，`ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`。
		
	如需搭配使用 SSH 與以 Linux 為基礎的 HDInsight 的詳細資訊，請參閱下列文章：
	
	* [從 Linux、Unix 或 OS X 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-unix.md)

	* [從 Windows 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-windows)
	
2. 根據預設，HDInsight 前端節點上未安裝 __pip__ 公用程式。使用以下命令安裝，並更新此公用程式：

		sudo apt-get install python-pip
		sudo pip install --upgrade pip

3. 用來下載推文的程式碼依賴 [Tweepy](http://www.tweepy.org/) 和 [Progressbar](https://pypi.python.org/pypi/progressbar/2.2)。若要安裝這些，請使用以下命令：

		sudo apt-get install python-dev libffi-dev libssl-dev
		sudo apt-get remove python-openssl
		sudo pip install tweepy==3.2.0 progressbar pyOpenSSL requests[security]
		
	> [AZURE.NOTE]有關移除 python-openssl、安裝 python-dev、libffi-dev、libssl-dev、pyOpenSSL 和 requests[security] 的部分 ，是為了在透過 SSL 從 Python 連線到 Twitter 時避免 InsecurePlatform 警告。
	>
	> Tweepy 3.2.0 版是用來避免處理推文時可能發生的[錯誤](https://github.com/tweepy/tweepy/issues/576) (英文)。

4. 使用以下命令建立名為 __gettweets.py__ 的新檔案：

		nano gettweets.py

5. 使用以下命令做為 __gettweets.py__ 檔案的內容。使用您 Twitter 應用程式的資訊，取代 __consumer/\_secret__、__consumer/\_key__、__access/\_token__ 和 __access/\_token/\_secret__ 的預留位置資訊。

		#!/usr/bin/python
		
		from tweepy import Stream, OAuthHandler
		from tweepy.streaming import StreamListener
		from progressbar import ProgressBar, Percentage, Bar
		import json
		import sys
		
		#Twitter app information
		consumer_secret='Your consumer secret'
		consumer_key='Your consumer key'
		access_token='Your access token'
		access_token_secret='Your access token secret'
		
		#The number of tweets we want to get
		max_tweets=10000
		
		#Create the listener class that will receive and save tweets
		class listener(StreamListener):
		    #On init, set the counter to zero and create a progress bar
		    def __init__(self, api=None):
		        self.num_tweets = 0
		        self.pbar = ProgressBar(widgets=[Percentage(), Bar()], maxval=max_tweets).start()
		
		    #When data is received, do this
		    def on_data(self, data):
		        #Append the tweet to the 'tweets.txt' file
		        with open('tweets.txt', 'a') as tweet_file:
		            tweet_file.write(data)
		            #Increment the number of tweets
		            self.num_tweets += 1
		            #Check to see if we have hit max_tweets and exit if so
		            if self.num_tweets >= max_tweets:
		                self.pbar.finish()
		                sys.exit(0)
		            else:
		                #increment the progress bar
		                self.pbar.update(self.num_tweets)
		        return True
		
		    #Handle any errors that may occur
		    def on_error(self, status):
		        print status
		
		#Get the OAuth token
		auth = OAuthHandler(consumer_key, consumer_secret)
		auth.set_access_token(access_token, access_token_secret)
		#Use the listener class for stream processing
		twitterStream = Stream(auth, listener())
		#Filter for these topics
		twitterStream.filter(track=["azure","cloud","hdinsight"])

6. 依序按 __Ctrl + X__，然後 __Y__ 儲存檔案。

7. 使用以下命令執行檔案，並下載推文：

		python gettweets.py

	應會顯示進度列指示器，且在下載推文並儲存至檔案時顯示 100%。

###上傳資料

若要將資料上傳至 WASB (HDInsight 所使用的分散式檔案系統)，請使用以下命令：

	hadoop fs -mkdir -p /tutorials/twitter/data
	hadoop fs -copyFromLocal tweets.txt /tutorials/twitter/data/tweets.txt

這會將資料儲存在一個叢集中所有節點都能存取的位置。

##執行 HiveQL 工作


1. 使用以下命令建立包含 HiveQL 陳述式的檔案：

		nano twitter.hql
	
	使用下列項目做為檔案的內容：

		set hive.exec.dynamic.partition = true;
		set hive.exec.dynamic.partition.mode = nonstrict;
		-- Drop table, if it exists
		DROP TABLE tweets_raw;
		-- Create it, pointing toward the tweets logged from Twitter
		CREATE EXTERNAL TABLE tweets_raw (
			json_response STRING
		)
		STORED AS TEXTFILE LOCATION '/tutorials/twitter/data';
		-- Drop and recreate the destination table
		DROP TABLE tweets;
		CREATE TABLE tweets
		(
			id BIGINT,
			created_at STRING,
			created_at_date STRING,
			created_at_year STRING,
			created_at_month STRING,
			created_at_day STRING,
			created_at_time STRING,
			in_reply_to_user_id_str STRING,
			text STRING,
			contributors STRING,
			retweeted STRING,
			truncated STRING,
			coordinates STRING,
			source STRING,
			retweet_count INT,
			url STRING,
			hashtags array<STRING>,
			user_mentions array<STRING>,
			first_hashtag STRING,
			first_user_mention STRING,
			screen_name STRING,
			name STRING,
			followers_count INT,
			listed_count INT,
			friends_count INT,
			lang STRING,
			user_location STRING,
			time_zone STRING,
			profile_image_url STRING,
			json_response STRING
		);
		-- Select tweets from the imported data, parse the JSON,
		-- and insert into the tweets table
		FROM tweets_raw
		INSERT OVERWRITE TABLE tweets
		SELECT
			cast(get_json_object(json_response, '$.id_str') as BIGINT),
			get_json_object(json_response, '$.created_at'),
			concat(substr (get_json_object(json_response, '$.created_at'),1,10),' ',
			substr (get_json_object(json_response, '$.created_at'),27,4)),
			substr (get_json_object(json_response, '$.created_at'),27,4),
			case substr (get_json_object(json_response,	'$.created_at'),5,3)
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
			cast (get_json_object(json_response, '$.retweet_count') as INT),
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
			cast (get_json_object(json_response, '$.user.followers_count') as INT),
			cast (get_json_object(json_response, '$.user.listed_count') as INT),
			cast (get_json_object(json_response, '$.user.friends_count') as INT),
			get_json_object(json_response, '$.user.lang'),
			get_json_object(json_response, '$.user.location'),
			get_json_object(json_response, '$.user.time_zone'),
			get_json_object(json_response, '$.user.profile_image_url'),
			json_response
		WHERE (length(json_response) > 500);
		
		
3. 依序按 __Ctrl + X__，然後 __Y__ 儲存檔案。

4. 使用以下命令執行包含於檔案中的 HiveQL：

		hive -i twitter.hql		
		
	這會載入 Hive 殼層，執行 __twitter.hql__ 檔案中的 HiveQL，最後會傳回 `hive >` 提示字元。
	
5. 從 `hive >` 提示字元使用以下命令，以確認您可以選取 HiveQL 在 __twitter.hql__ 檔案中建立的 __tweets__ 資料表的資料：
		
		SELECT name, screen_name, count(1) as cc
			FROM tweets
			WHERE text like "%Azure%"
			GROUP BY name,screen_name
			ORDER BY cc DESC LIMIT 10;

	這會傳回最多 10 則訊息內容中包含文字 __Azure__ 的推文。

##後續步驟

本教學課程中，我們看到如何將非結構化 JSON 資料集轉換成結構化 Hive 資料表，然後在 Azure 上使用 HDInsight 來查詢、探索和分析來自 Twitter 的資料。若要深入了解，請參閱：

- [開始使用 HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md)
- [使用 HDInsight 分析航班延誤資料](hdinsight-analyze-flight-delay-data-linux.md)

[curl]: http://curl.haxx.se
[curl-download]: http://curl.haxx.se/download.html

[apache-hive-tutorial]: https://cwiki.apache.org/confluence/display/Hive/Tutorial

[twitter-streaming-api]: https://dev.twitter.com/docs/streaming-apis
[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter

<!---HONumber=Oct15_HO4-->