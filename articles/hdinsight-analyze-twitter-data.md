<properties linkid="manage-services-hdinsight-howto-social-data" urlDisplayName="Analyze Twitter data with HDInsight Hadoop" pageTitle="Analyze Twitter data with Hadoop in HDInsight | Azure" metaKeywords="" description="Learn how to use Hive to analyze Twitter data on Hadoop in HDInsight to find the usage frequency of a particular word." metaCanonical="" services="HDInsight" documentationCenter="" title="Analyze Twitter data with Hadoop in HDInsight" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao"></tags>

# 在 HDInsight 上使用 Hadoop 分析 Twitter 資料

了解如何在 HDInsight 中將 Hive 與 Hadoop 搭配使用以分析 Twitter 資料。

社群網站是驅使採用「巨量資料」的其中一個主要動力。像 Twitter 之類的網站所提供的公開 API，是分析和了解流行趨勢的一項實用的資料來源。在本教學課程中，您將使用 Twitter 串流 API 連接到 Twitter Web 服務以取得某些推文，然後使用 Hive，取得傳送了最多內含特定文字之推文的 Twitter 使用者清單。

**預估完成時間：** 30 分鐘

## 本文內容

-   [必要條件][]
-   [取得 Twitter 摘要][]
-   [建立 HiveQL 指令碼][]
-   [使用 Hive 處理資料][]
-   [完成教學課程][]
-   [後續步驟][]

## <span id="prerequisites"></span></a>必要條件

開始進行本教學課程之前，您必須具備下列條件：

-   已安裝並設定 Azure PowerShell 的**工作站**。如需指示，請參閱[安裝並設定 Azure PowerShell][]。若要執行 PowerShell 指令碼，您必須以系統管理員的身分執行 Azure PowerShell，並將執行原則設為 *RemoteSigned*。請參閱[執行 Windows PowerShell 指令碼][]。

    執行 PowerShell 指令碼之前，請確定您已使用下列 Cmdlet 連接到 Azure 訂用帳戶：

        Add-AzureAccount

    如果您有多個 Azure 訂用帳戶，請使用下列 Cmdlet 設定目前的訂用帳戶：

        Select-AzureSubscription <AzureSubscriptionName>

-   **Azure HDInsight 叢集**。如需叢集佈建的指示，請參閱[開始使用 HDInsight][] 或[佈建 HDInsight 叢集][]。進行教學課程時，您將需要下列資料：

    <table>
    <colgroup>
    <col width="25%" />
    <col width="25%" />
    <col width="25%" />
    <col width="25%" />
    </colgroup>
    <thead>
    <tr class="header">
    <th align="left">叢集屬性</th>
    <th align="left">PowerShell 變數名稱</th>
    <th align="left">值</th>
    <th align="left">說明</th>
    </tr>
    </thead>
    <tbody>
    <tr class="odd">
    <td align="left">HDInsight 叢集名稱</td>
    <td align="left">$clusterName</td>
    <td align="left"></td>
    <td align="left">這是您的 HDInsight 叢集名稱。</td>
    </tr>
    <tr class="even">
    <td align="left">Azure 儲存體帳戶名稱</td>
    <td align="left">$storageAccountName</td>
    <td align="left"></td>
    <td align="left">可供 HDInsight 叢集使用的 Azure 儲存體帳戶。在本教學課程中，請使用在叢集佈建程序中指定的預設儲存體帳戶。</td>
    </tr>
    <tr class="odd">
    <td align="left">Azure Blob 容器名稱</td>
    <td align="left">$containerName</td>
    <td align="left"></td>
    <td align="left">在此範例中，請使用預設 HDInsight 叢集檔案系統所使用的 Azure Blob 儲存體容器。根據預設，此容器的名稱會與 HDInsight 叢集名稱相同。</td>
    </tr>
    </tbody>
    </table>

**了解 HDInsight 儲存體**

HDInsight 會使用 Azure Blob 儲存體來儲存資料。我們稱之為 *WASB* 或 *Azure 儲存體 - Blob*。WASB 是 Microsoft 在 Azure Blob 儲存體上的 HDFS 實作。如需詳細資訊，請參閱[搭配 HDInsight 使用 Azure Blob 儲存體][]。

當您佈建 HDInsight 叢集時，會將一個 Blob 儲存體容器指定為預設檔案系統，如同在 HDFS 中一般。除了此容器以外，您也可以在佈建過程中，從相同的 Azure 儲存體帳戶或不同的 Azure 儲存體帳戶新增其他容器。如需有關新增其他儲存體帳戶的詳細資訊，請參閱[佈建 HDInsight 叢集][]。

為簡化本教學課程中使用的 PowerShell 指令碼，所有檔案都會儲存在位於 */tutorials/twitter* 的預設檔案系統容器中。根據預設，此容器的名稱會與 HDInsight 叢集名稱相同。

WASB 語法如下：

    wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [WACOM.NOTE] HDInsight 叢集 3.0 版僅支援 *wasb://* 語法。HDInsight 2.1 和 1.6 支援舊的 *asv://* 語法，但在 HDInsight 3.0 叢集中已不受支援，未來的版本也不加以支援。

> The WASB path is virtual path.如需詳細資訊，請參閱[搭配 HDInsight 使用 Azure Blob 儲存體][]。

儲存在預設檔案系統容器中的檔案，可使用下列任一 URI 從 HDInsight 存取 (範例中使用 tweets.txt)：

    wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/twitter/tweets.txt
    wasb:///tutorials/twitter/tweets.txt
    /tutorials/twitter/tweets.txt

如果您要直接從儲存體帳戶存取檔案，檔案的Blob 名稱為：

    tutorials/twitter/tweets.txt

下表列出本教學課程中使用的檔案：

<table border="1">
<tr><th>檔案</th><th>說明</th></tr>
<tr><td>/tutorials/twitter/data/tweets.txt</td><td>Hive 工作的來源資料。 </td></tr>
<tr><td>/tutorials/twitter/output</td><td>Hive 工作的輸出資料夾。預設檔案 Hive 工作的輸出檔案名稱為 <strong>000000_0</strong>. </td></tr>
<tr><td>tutorials/twitter/twitter.hql</td><td>HiveQL 指令碼檔案。</td></tr>
<tr><td>/tutorials/twitter/jobstatus</td><td>Hadoop 工作狀態。</td></tr>
</table>

## <span id="feed"></span></a>取得 Twitter 摘要

在本教學課程中，您將使用 [Twitter 串流 API][]。您將使用的特定 Twitter 串流 API 為 [statuses/filter][]。

[推文資料][]會以包含複雜巢狀結構的 JSon 格式儲存。您可以不要使用慣用的程式設計語言撰寫多行程式碼，而將此巢狀結構轉換成 Hive 資料表，以利用 HiveQL 這種類似 SQL 的語言來查詢資料表。

Twitter 會使用 OAuth 提供對其 API 的授權存取。OAuth 是一項驗證通訊協定，可讓使用者在無須共用其密碼的情況下，允許應用程式代表他們執行動作。如需詳細資訊，請至 [oauth.net][]，或參考 Hueniverse 的 [OAuth 的入門指南][] (英文)。

使用 OAuth 的第一個步驟，是在 Twitter 開發人員網站上建立新的應用程式。

**建立 Twitter 應用程式**

1.  登入 [][]<https://apps.twitter.com/></a>。如果您沒有 Twitter 帳戶，請按一下 [立即註冊] 連結。
2.  按一下 [建立新的應用程式]。
3.  輸入 [名稱]、[說明]、[網站]。您可以在 [網站] 欄位中自行設定 URL。下表列出部分要使用的範例值：

	<table border="1">
	<tr><th>欄位</th><th>值</th></tr>
	<tr><td>名稱</td><td>MyHDInsightApp</td></tr>
	<tr><td>說明</td><td>MyHDInsightApp</td></tr>
	<tr><td>網站</td><td>http://www.myhdinsightapp.com</td></tr>
	</table>
4.  核取 [Yes, I agree]然後按一下 [Create your Twitter application]。
5.  按一下 [權限] 索引標籤。預設權限為 [唯讀]。本教學課程使用預設值即可。
6.  按一下 [API 金鑰] 索引標籤。
7.  按一下 [Create my access token]。
8.  按一下位於頁面右上角的 [測試 OAuth]。
9.  記下**消費者金鑰**、**消費者密碼**、**存取權杖**和**存取權杖密碼**。稍後在教學課程中會用到這些值。

在本教學課程中，您將使用 PowerShell 發出 Web 服務呼叫。另一項常用來發出 Web 服務呼叫的工具，是 [*Curl*][]。Curl 可從[這裡][]下載取得。

> [WACOM.NOTE] 在 Windows 上使用 curl 命令時，對選項值請使用雙引號，而不要使用單引號。

**取得推文**

1.  開啟 Windows PowerShell ISE (在 Windows 8 的 [開始] 畫面上輸入 **PowerShell\_ISE**，然後按一下 [Windows PowerShell ISE]。請參閱[在 Windows 8 和 Windows 上啟動 Windows PowerShell][])。

2.  將下列指令碼複製到指令碼窗格中：

        Write-Host "Set variables ..." -ForegroundColor Green
        $storageAccountName = "<AzureStorageAccountName>"
        $containerName = "<BlobContainerName>"

        $destBlobName = "tutorials/twitter/data/tweets.txt"

        $trackString = "Azure, Cloud, HDInsight"
        $track = [System.Uri]::EscapeDataString($trackString);
        $lineMax = 100  #about 3 minutes every 100 lines

        $oauth_consumer_key = "<TwitterAppConsumerKey>";
        $oauth_consumer_secret = "<TwitterAppConsumerSecret>";
        $oauth_token = "<TwitterAppAccessToken>";
        $oauth_token_secret = "<TwitterAppAccessTokenSecret>";

        Write-Host "Define the Azure storage connection string ..." -ForegroundColor Green
        $storageAccountKey = get-azurestoragekey $storageAccountName | %{$_.Primary}
        $storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=$storageAccountName;AccountKey=$storageAccountKey"

        Write-Host "Create block blob object ..." -ForegroundColor Green
        $storageAccount = [Microsoft.WindowsAzure.Storage.CloudStorageAccount]::Parse($storageConnectionString)
        $storageClient = $storageAccount.CreateCloudBlobClient();
        $storageContainer = $storageClient.GetContainerReference($containerName)
        $destBlob = $storageContainer.GetBlockBlobReference($destBlobName)

        Write-Host "Define a MemoryStream and a StreamWriter for writing ..." -ForegroundColor Green
        $memStream = New-Object System.IO.MemoryStream
        $writeStream = New-Object System.IO.StreamWriter $memStream

        Write-Host "Format oauth strings ..." -ForegroundColor Green
        $oauth_nonce = [System.Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes([System.DateTime]::Now.Ticks.ToString()));
        $ts = [System.DateTime]::UtcNow - [System.DateTime]::ParseExact("01/01/1970", "dd/MM/yyyy", $null)
        $oauth_timestamp = [System.Convert]::ToInt64($ts.TotalSeconds).ToString();

        $signature = "POST&";
        $signature += [System.Uri]::EscapeDataString("https://stream.twitter.com/1.1/statuses/filter.json") + "&";
        $signature += [System.Uri]::EscapeDataString("oauth_consumer_key=" + $oauth_consumer_key + "&");
        $signature += [System.Uri]::EscapeDataString("oauth_nonce=" + $oauth_nonce + "&"); 
        $signature += [System.Uri]::EscapeDataString("oauth_signature_method=HMAC-SHA1&");
        $signature += [System.Uri]::EscapeDataString("oauth_timestamp=" + $oauth_timestamp + "&");
        $signature += [System.Uri]::EscapeDataString("oauth_token=" + $oauth_token + "&");
        $signature += [System.Uri]::EscapeDataString("oauth_version=1.0&");
        $signature += [System.Uri]::EscapeDataString("track=" + $track);

        $signature_key = [System.Uri]::EscapeDataString($oauth_consumer_secret) + "&" + [System.Uri]::EscapeDataString($oauth_token_secret);

        $hmacsha1 = new-object System.Security.Cryptography.HMACSHA1;
        $hmacsha1.Key = [System.Text.Encoding]::ASCII.GetBytes($signature_key);
        $oauth_signature = [System.Convert]::ToBase64String($hmacsha1.ComputeHash([System.Text.Encoding]::ASCII.GetBytes($signature)));

        $oauth_authorization = 'OAuth ';
        $oauth_authorization += 'oauth_consumer_key="' + [System.Uri]::EscapeDataString($oauth_consumer_key) + '",';
        $oauth_authorization += 'oauth_nonce="' + [System.Uri]::EscapeDataString($oauth_nonce) + '",';
        $oauth_authorization += 'oauth_signature="' + [System.Uri]::EscapeDataString($oauth_signature) + '",';
        $oauth_authorization += 'oauth_signature_method="HMAC-SHA1",'
        $oauth_authorization += 'oauth_timestamp="' + [System.Uri]::EscapeDataString($oauth_timestamp) + '",'
        $oauth_authorization += 'oauth_token="' + [System.Uri]::EscapeDataString($oauth_token) + '",';
        $oauth_authorization += 'oauth_version="1.0"';

        $post_body = [System.Text.Encoding]::ASCII.GetBytes("track=" + $track); 

        Write-Host "Create HTTP web request ..." -ForegroundColor Green
        [System.Net.HttpWebRequest] $request = [System.Net.WebRequest]::Create("https://stream.twitter.com/1.1/statuses/filter.json");
        $request.Method = "POST";
        $request.Headers.Add("Authorization", $oauth_authorization);
        $request.ContentType = "application/x-www-form-urlencoded";
        $body = $request.GetRequestStream();

        $body.write($post_body, 0, $post_body.length);
        $body.flush();
        $body.close();
        $response = $request.GetResponse() ;

        Write-Host "Start stream reading ..." -ForegroundColor Green

        $sReader = New-Object System.IO.StreamReader($response.GetResponseStream())

        $inrec = $sReader.ReadLine()
        $count = 0
        while (($inrec -ne $null) -and ($count -le $lineMax))
        {
            if ($inrec -ne "")
            {
                Write-Host $count.ToString() ", " -NoNewline -ForegroundColor Green
                if ($count%10 -eq 0){
                    write-host " --- " -NoNewline
                    Get-Date -Format hh:mm:sstt
                }

                $writeStream.WriteLine($inrec)
                $count ++
            }

            $inrec=$sReader.ReadLine()
        }

        Write-Host "Write to the destination blob ..." -ForegroundColor Green
        $writeStream.Flush()
        $memStream.Seek(0, "Begin")
        $destBlob.UploadFromStream($memStream) 

        $sReader.close()
        Write-Host "Complete!" -ForegroundColor Green

3.  設定指令碼中的前九個變數：

    | 變數                     | 說明                                                                                                                             |
    |--------------------------|----------------------------------------------------------------------------------------------------------------------------------|
    | $storageAccountName      | 刪除用於預設 HDInsight 叢集檔案系統的 Azure 儲存體帳戶。這是佈建時所指定的儲存體帳戶。請參閱[必要條件][]。                       |
    | $containerName           | 刪除用於預設 HDInsight 叢集檔案系統的 Blob 容器。這是佈建時所指定的容器。請參閱[必要條件][]。                                    |
    | $destBlobName            | 這是輸出 Blob 名稱。預設值為 **tutorials/twitter/data/tweets.txt**。如果您變更預設值，則 PowerShell 指令碼也必須隨之變更。       |
    | $trackString             | Web 服務會傳回這些關鍵字的相關推文。預設值為 **Azure, Cloud, HDInsight**。如果您變更預設值，則 PowerShell 指令碼也必須隨之變更。 |
    | $lineMax                 | 此值會決定指令碼所將讀取的推文數。讀取 100 則推文大約需要三分鐘。您可以設定更大的數目，但下載時間將會較久。                      |
    | $oauth\_consumer\_key    | 這是您先前在建立 Twitter 應用程式時所記下的 Twitter 應用程式**消費者金鑰**。                                                     |
    | $oauth\_consumer\_secret | 這是您先前記下的 Twitter 應用程式**消費者密碼**。                                                                                |
    | $oauth\_token            | 這是您先前記下的 Twitter 應用程式**存取權杖**。                                                                                  |
    | $oauth\_token\_secret    | 這是您先前記下的 Twitter 應用程式**存取權杖密碼**。                                                                              |

4.  按 **F5** 以執行指令碼。如果發生問題，請選取所有程式碼行，然後按 **F8**，以解決問題。
5.  輸出的結尾應會顯示「完成！」。錯誤訊息會顯示為紅色。

在驗證程序中，您可以使用 Azure 儲存體總管或 Azure PowerShell，在您的 Azure Blob 儲存體上查看輸出檔案 **/tutorials/twitter/data/tweets.txt**。如需列出檔案的範例 PowerShell 指令碼，請參閱[搭配 HDInsight 使用 Blob 儲存體][]。

## <span id="script"></span></a>建立 HiveQL 指令碼

使用 Azure PowerShell 可讓您逐一執行多個 HiveQL 陳述式，或將 HiveQL 陳述式封裝到指令碼檔案中。在本教學課程中，您將會建立 HiveQL 指令碼。指令碼檔案必須上傳至 WASB。在下一節中，您將使用 Azure PowerShell 執行指令碼檔案。

HiveQL 指令碼將執行下列作業：

1.  **捨棄 tweets\_raw 資料表** (若此資料表已存在)。
2.  **建立 tweets\_raw Hive 資料表**。這個暫時的 Hive 結構化資料表會保存要進一步進行 ETL 處理的資料。如需資料分割的相關資訊，請參閱 [Hive 教學課程][]。
3.  從來源資料夾 /tutorials/twitter/data **載入資料**。巢狀 JSon 格式的大型 Tweets 資料集現在已轉換成暫時的 Hive 資料表結構。
4.  **捨棄 tweets 資料表** (若此資料表已存在)；
5.  **建立 tweets 資料表**。您必須先執行另一個 ETL 程序，才能使用 Hive 來查詢 Tweets 資料集。此 ETL 程序針對您在 "twitter\_raw" 資料表中儲存的資料，定義了更詳細的資料表結構描述。
6.  **插入覆寫資料表**。這個複雜的 Hive 指令碼會啟動一組冗長的 Map Reduce 工作 (由 Hadoop 叢集執行)。根據資料集和叢集大小而定，這可能需要 10 分鐘。
7.  **插入覆寫目錄**。執行查詢，並將資料集輸出至檔案。此查詢會傳回一份 twitter 使用者清單，這些使用者傳送了最多含有 "Azure" 一字的推文。

**建立 Hive 指令碼並將其上傳至 Azure**

1.  開啟 Windows PowerShell ISE。
2.  將下列指令碼複製到指令碼窗格中：

        Write-Host "Define variables ..." -ForegroundColor Green
        $storageAccountName = "<AzureStorageAccountName>"
        $containerName = "<BlobContainerName>"

        $sourceDataPath = "/tutorials/twitter/data"
        $outputPath = "/tutorials/twitter/output"

        $hqlScriptFile = "tutorials/twitter/twitter.hql"

        $hqlStatements = @"
        set hive.exec.dynamic.partition = true;
        set hive.exec.dynamic.partition.mode = nonstrict;

        DROP TABLE tweets_raw;
        CREATE TABLE tweets_raw (
            json_response STRING
        ) 
        PARTITIONED BY (filesequence INT);

        LOAD DATA INPATH '$sourceDataPath'
        INTO TABLE tweets_raw
        PARTITION (filesequence = 1);

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
        )
        PARTITIONED BY (filesequence INT);

        FROM tweets_raw
        INSERT OVERWRITE TABLE tweets
        PARTITION (filesequence)
        SELECT
            cast(get_json_object(json_response, '$.id_str') as BIGINT),
            get_json_object(json_response, '$.created_at'),
            concat(substr (get_json_object(json_response, '$.created_at'),1,10),' ',
            substr (get_json_object(json_response, '$.created_at'),27,4)),
            substr (get_json_object(json_response, '$.created_at'),27,4),
            case substr (get_json_object(json_response, '$.created_at'),5,3)
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
            json_response,
            filesequence
        WHERE (length(json_response) > 500);

        INSERT OVERWRITE DIRECTORY '$outputPath'
        SELECT name, screen_name, count(1) as cc 
            FROM tweets 
            WHERE text like "%Azure%" 
            GROUP BY name,screen_name 
            ORDER BY cc DESC LIMIT 10;
        "@

        Write-Host "Define the connection string ..." -ForegroundColor Green
        $storageAccountKey = get-azurestoragekey $storageAccountName | %{$_.Primary}
        $storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=$storageAccountName;AccountKey=$storageAccountKey"

        Write-Host "Create block blob objects referencing the hql script file" -ForegroundColor Green
        $storageAccount = [Microsoft.WindowsAzure.Storage.CloudStorageAccount]::Parse($storageConnectionString)
        $storageClient = $storageAccount.CreateCloudBlobClient();
        $storageContainer = $storageClient.GetContainerReference($containerName)
        $hqlScriptBlob = $storageContainer.GetBlockBlobReference($hqlScriptFile)

        Write-Host "Define a MemoryStream and a StreamWriter for writing ... " -ForegroundColor Green
        $memStream = New-Object System.IO.MemoryStream
        $writeStream = New-Object System.IO.StreamWriter $memStream
        $writeStream.Writeline($hqlStatements)

        Write-Host "Write to the destination blob ... " -ForegroundColor Green
        $writeStream.Flush()
        $memStream.Seek(0, "Begin")
        $hqlScriptBlob.UploadFromStream($memStream)

        Write-Host "Complete!" -ForegroundColor Green

3.  設定指令碼中的前兩個變數：

    | 變數                | 說明                                                                                                       |
    |---------------------|------------------------------------------------------------------------------------------------------------|
    | $storageAccountName | 刪除用於預設 HDInsight 叢集檔案系統的 Azure 儲存體帳戶。這是佈建時所指定的儲存體帳戶。請參閱[必要條件][]。 |
    | $containerName      | 刪除用於預設 HDInsight 叢集檔案系統的 Blob 容器。這是佈建時所指定的容器。請參閱[必要條件][]。              |
    | $sourceDataPath     | Hive 查詢將從中讀取資料的 WASB 位置。您無須變更此變數。                                                    |
    | $outputPath         | Hive 查詢將輸出結果的 WASB 位置。您無須變更此變數。                                                        |
    | $hqlScriptFile      | HiveQL 指令碼檔案的位置和檔案名稱。                                                                        |

4.  按 **F5** 以執行指令碼。如果發生問題，請選取所有程式碼行，然後按 **F8**，以解決問題。
5.  輸出的結尾應會顯示「完成！」。錯誤訊息會顯示為紅色。

在驗證程序中，您可以使用 Azure 儲存體總管或 Azure PowerShell，在您的 Azure Blob 儲存體上查看輸出檔案 **/tutorials/twitter/twitter.hql**。如需列出檔案的範例 PowerShell 指令碼，請參閱[搭配 HDInsight 使用 Blob 儲存體][]。

## <a name="process"></a> 使用 Hive 處理 Twitter 資料

您已完成所有準備工作。現在，您可以叫用 Hive 指令碼，並查看結果。

### 提交 Hive 工作

使用下列 PowerShell 指令碼可執行 Hive 指令碼。您必須設定第一個變數。

    Write-Host "Set the variables ... " -ForegroundColor Green
    $clusterName = "<HDInsightClusterName>"

    $hqlScriptFile = "/tutorials/twitter/twitter.hql"
    $statusFolder = "/tutorials/twitter/jobstatus"

    Write-Host "Invoke Hive ... " -ForegroundColor Green
    Use-AzureHDInsightCluster $clusterName
    $response = Invoke-Hive -file $hqlScriptFile -StatusFolder $statusFolder -OutVariable $outVariable

    Write-Host "Display the standard error log ... " -ForegroundColor Green
    $jobID = ($response | Select-String job_ | Select-Object -First 1) -replace ‘\s*$’ -replace ‘.*\s’
    Get-AzureHDInsightJobOutput -cluster $clusterName -JobId $jobID -StandardError 

### 查看結果

使用下列 PowerShell 指令碼可查看 Hive 工作輸出。您必須設定前兩個變數。

    Write-Host "Set the variables ... " -ForegroundColor Green
    $storageAccountName = "<AzureStorageAccountName>"   # The storage account used for the default file system specified at provision.
    $containerName = "<BlobStorageContainerName>"  # The default file system container has the same name as the cluster.
    $blob = "tutorials/twitter/output/000000_0" # The name of the blob to be downloaded.

    Write-Host "Create a context object ... " -ForegroundColor Green
    $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
    $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

    Write-Host "Download the blob ..." -ForegroundColor Green
    Get-AzureStorageBlobContent -Container $ContainerName -Blob $blob -Context $storageContext -Force

    Write-Host "Display the output ..." -ForegroundColor Green
    cat "./$blob"

> [WACOM.NOTE] Hive 資料表會使用 \\001 做為欄位分隔符號。此分隔符號不會顯示在輸出中。

分析結果列示在 WASB 之後，您可以將資料匯出至 Azure SQL Database/SQL Server，使用 Power Query 將資料匯出至 Excel，或使用 Hive ODBC 驅動程式將應用程式連接到資料。如需詳細資訊，請參閱[搭配 HDInsight 使用 Sqoop][]、[使用 HDInsight 分析航班延誤資料][]、[使用 Power Query 將 Excel 連接到 HDInsight][] 和[使用 Microsoft Hive ODBC 驅動程式將 Excel 連接到 HDInsight][]。

## <span id="cleanup"></span></a>完成教學課程

如果您要重新執行教學課程，您必須：

-   **重新建立 Tweets 資料檔案**。Hive 工作已移除來源 Tweets 資料檔案。您必須產生新檔案。檔案名稱為 **tutorials/twitter/data/tweets.txt**。請參閱[取得 Twitter 摘要][]。

## <span id="nextsteps"></span></a>後續步驟

本教學課程中，我們看到如何將非結構化 Json 資料集轉換成結構化 Hive 資料表，然後在 Azure 上使用 HDInsight 來查詢、探索和分析來自 Twitter 的資料。若要深入了解，請參閱：

-   [開始使用 HDInsight][]
-   [使用 HDInsight 分析班機延誤資料][使用 HDInsight 分析航班延誤資料]
-   [使用 Power Query 將 Excel 連接到 HDInsight][]
-   [使用 Microsoft Hive ODBC 驅動程式將 Excel 連接到 HDInsight][]
-   [搭配 HDInsight 使用 Sqoop][]

  [必要條件]: #prerequisites
  [取得 Twitter 摘要]: #feed
  [建立 HiveQL 指令碼]: #script
  [使用 Hive 處理資料]: #process
  [完成教學課程]: #cleanup
  [後續步驟]: #nextsteps
  [安裝並設定 Azure PowerShell]: ../install-configure-powershell
  [執行 Windows PowerShell 指令碼]: http://technet.microsoft.com/en-us/library/ee176949.aspx
  [開始使用 HDInsight]: ../hdinsight-get-started/
  [佈建 HDInsight 叢集]: ../hdinsight-provision-clusters/
  [搭配 HDInsight 使用 Azure Blob 儲存體]: ../hdinsight-use-blob-storage/
  [Twitter 串流 API]: https://dev.twitter.com/docs/streaming-apis
  [statuses/filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter
  [推文資料]: https://dev.twitter.com/docs/platform-objects/tweets
  [oauth.net]: http://oauth.net/
  [OAuth 的入門指南]: http://hueniverse.com/oauth/
  []: https://apps.twitter.com/
  [*Curl*]: http://curl.haxx.se
  [這裡]: http://curl.haxx.se/download.html
  [在 Windows 8 和 Windows 上啟動 Windows PowerShell]: http://technet.microsoft.com/en-us/library/hh847889.aspx
  [搭配 HDInsight 使用 Blob 儲存體]: ../hdinsight-use-blob-storage/#powershell
  [Hive 教學課程]: https://cwiki.apache.org/confluence/display/Hive/Tutorial
  [搭配 HDInsight 使用 Sqoop]: ../hdinsight-use-sqoop/
  [使用 HDInsight 分析航班延誤資料]: ../hdinsight-analyze-flight-delay-data/
  [使用 Power Query 將 Excel 連接到 HDInsight]: ../hdinsight-connect-excel-power-query/
  [使用 Microsoft Hive ODBC 驅動程式將 Excel 連接到 HDInsight]: ../hdinsight-connect-excel-hive-ODBC-driver/
