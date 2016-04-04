<properties 
	pageTitle="利用串流分析來建置 IOT 解決方案 | Microsoft Azure" 
	description="串流分析的開始使用教學課程, 收費站案例的 iot 解決方案"
	keywords=""
	documentationCenter=""
	services="stream-analytics"
	authors="jeffstokes72" 
	manager="paulettm" 
	editor="cgronlun"
/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="03/21/2016" 
	ms.author="jeffstok"
/>

# 利用串流分析來建置 IOT 解決方案

## 簡介

在本教學課程中，您將學習如何利用 Azure 串流分析來即時獲得您資料的深入解析。Azure 的串流處理服務可讓開發人員輕鬆地處理不斷變化的資料空間，方法是結合資料流 (例如點選流、記錄檔，以及有歷史記錄或參考資料的裝置產生事件)，來輕鬆、快速地取得深入的業務解析。由 Microsoft Azure 代管的 Azure 串流分析是可完全管理的即時串流計算服務，它提供內建的備援、低延遲及延展性功能，可讓您在幾分鐘之內就立刻上手。

完成本教學課程之後，您將能夠：

-   熟悉 Azure 串流分析入口網站。
-   設定及部署串流工作。
-   使用串流分析查詢語言來表達真實世界的問題，並加以解決。
-   有自信地使用 Azure 串流分析來為客戶開發串流解決方案。
-   利用監視和記錄的經驗來排解問題。

## 先決條件

您必須滿足下列的先決條件，才能順利地完成本教學課程。

-   最新的 [Azure PowerShell](../powershell-install-configure.md)
-   Visual Studio 2015 或，或是免費的 [Visual Studio Community](https://www.visualstudio.com/products/visual-studio-community-vs.aspx)
-   [Azure 訂用帳戶](https://azure.microsoft.com/pricing/free-trial/)
-   電腦的系統管理員權限
-   從 Microsoft 下載中心下載 [TollApp.zip](http://download.microsoft.com/download/D/4/A/D4A3C379-65E8-494F-A8C5-79303FD43B0A/TollApp.zip)。
-   選擇性：[GitHub](https://github.com/streamanalytics/samples/tree/master/TollApp) 中 TollApp 事件產生器的原始程式碼

## 案例簡介：收費站，你好！


收費站是常見的景象，我們在世界各地的許多高速公路、橋樑及隧道上都會碰到收費站。每個收費站都有好幾個收費亭，它們可能是手動的 (也就是您必須停車來向收費員付通行費)，也可能是自動的 (這代表收費亭頂端有個感應器，當您的車輛經過收費亭時，感應器會掃描貼在您車輛擋風玻璃上的 RFID 卡)。我們可以輕易地把車輛通過這些收費站的情況，想像成其中能執行許多有趣行動的事件串流。

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image1.jpg)

## 傳入的資料

我們將利用 2 個資料流 (分別由安裝在收費站入口及出口的感應器所產生)，以及 1 個擁有車輛登記資料的靜態查詢資料集。

### 入口資料流

入口資料流包含車輛進入收費站的相關資訊。
  
  
| TollID | EntryTime | LicensePlate | State | Make | Model | VehicleType | VehicleWeight | Toll | Tag |
|---------|-------------------------|--------------|-------|--------|---------|--------------|----------------|------|-----------|
| 1 | 2014-09-10 12:01:00.000 | JNB 7001 | NY | Honda | CRV | 1 | 0 | 7 | |
| 1 | 2014-09-10 12:02:00.000 | YXZ 1001 | NY | Toyota | Camry | 1 | 0 | 4 | 123456789 |
| 3 | 2014-09-10 12:02:00.000 | ABC 1004 | CT | Ford | Taurus | 1 | 0 | 5 | 456789123 |
| 2 | 2014-09-10 12:03:00.000 | XYZ 1003 | CT | Toyota | Corolla | 1 | 0 | 4 | |
| 1 | 2014-09-10 12:03:00.000 | BNJ 1007 | NY | Honda | CRV | 1 | 0 | 5 | 789123456 |
| 2 | 2014-09-10 12:05:00.000 | CDE 1007 | NJ | Toyota | 4x4 | 1 | 0 | 6 | 321987654 |
  

以下是每個資料欄的簡短說明：
  
  
| TollID | 用來識別收費站的收費亭唯一識別碼 |
|--------------|----------------------------------------------------------------|
| EntryTime | 車輛進入收費亭的日期及時間 (國際標準時間) |
| LicensePlate | 車輛的牌照號碼 |
| State | 美國的某個洲 |
| Make | 車輛的製造商 |
| Model | 車輛的型號 |
| VehicleType | 1 代表客車，2 代表商用車 |
| WeightType | 車輛的重量，單位為噸；0 代表客車 |
| Toll | 通行費，單位為美元 |
| Tag | 車輛上可用來自動付費的 e-Tag，空白則代表手動付費 |


### 出口資料流

出口資料流包含車輛離開收費站的相關資訊。
  
  
| **TollId** | **ExitTime** | **LicensePlate** |
|------------|------------------------------|------------------|
| 1 | 2014-09-10T12:03:00.0000000Z | JNB 7001 |
| 1 | 2014-09-10T12:03:00.0000000Z | YXZ 1001 |
| 3 | 2014-09-10T12:04:00.0000000Z | ABC 1004 |
| 2 | 2014-09-10T12:07:00.0000000Z | XYZ 1003 |
| 1 | 2014-09-10T12:08:00.0000000Z | BNJ 1007 |
| 2 | 2014-09-10T12:07:00.0000000Z | CDE 1007 |

以下是每個資料欄的簡短說明：
  
  
| 資料欄 | 說明 |
|--------------|-----------------------------------------------------------------|
| TollID | 用來識別收費站的收費亭唯一識別碼 |
| ExitTime | 車輛離開收費亭的日期及時間 (國際標準時間) |
| LicensePlate | 車輛的牌照號碼 |

### 商用車的登記資料

我們將使用商用車登記資料庫的靜態快照。
  
  
| LicensePlate | RegistrationId | Expired |
|--------------|----------------|---------|
| SVT 6023 | 285429838 | 1 |
| XLZ 3463 | 362715656 | 0 |
| BAC 1005 | 876133137 | 1 |
| RIV 8632 | 992711956 | 0 |
| SNY 7188 | 592133890 | 0 |
| ELH 9896 | 678427724 | 1 |                      

以下是每個資料欄的簡短說明：
  
  
| 資料欄 | 說明 |
|--------------|-----------------------------------------------------------------|
| LicensePlate | 車輛的牌照號碼 |
| RegistrationId | RegistrationId |
| Expired | 0 代表車輛登記仍有效，1 代表車輛登記已過期 |


## 設定 Azure 串流分析的環境

如要完成本教學課程，您將需要 Microsoft Azure 訂用帳戶。Microsoft 能讓您免費試用 Microsoft Azure 服務，如下所述。

如果您沒有 Azure 帳戶，可以透過下列連結要求獲得免費試用版：<http://azure.microsoft.com/pricing/free-trial/>。

請注意：如要註冊免費試用，您必須擁有可接收簡訊的行動裝置，以及有效的信用卡。

請務必在這個練習的結尾，依照＜清理您的 Azure 帳戶＞一節中的步驟來進行，讓您能充分利用您 $200 美元的免費 Azure 點數。

## 佈建本教學課程所需的 Azure 資源

本教學課程將需要 2 個 Azure 事件中樞，來接收「入口」及「出口」資料流。我們將使用 Azure SQL Database，來輸出串流分析工作的結果。我們也會使用 Azure 儲存體，來儲存車輛登記的相關參考資料。

GitHub 上 TollApp 資料夾中的 Setup.ps1 指令碼，可用來建立所有必要的資源。為了節省時間，我們建議您執行這個指令碼。如果您想要進一步了解如何在 Azure 入口網站設定這些資源，請參閱附錄＜在 Azure 入口網站中設定教學課程資源＞。

請下載並儲存支援的 [TollApp](http://download.microsoft.com/download/D/4/A/D4A3C379-65E8-494F-A8C5-79303FD43B0A/TollApp.zip) 資料夾和檔案。

請「以系統管理員的身分」開啟 [Microsoft Azure PowerShell] 視窗。如果您沒有 Azure PowerShell，請依照[如何安裝和設定 Azure PowerShell](../powershell-install-configure.md) 中的指示來安裝 Azure PowerShell。

Windows 會自動封鎖從網際網路下載的 ps1、dll 和 exe 檔案，因此我們必須在執行指令碼之前設定執行原則。請確認您是以系統管理員的身分來執行 Azure PowerShell 視窗。請執行「Set-ExecutionPolicy unrestricted」，然後在畫面出現提示時按下 Y 鍵。

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image2.png)

請執行 Get-ExecutionPolicy 來確認命令已正確執行。

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image3.png)

請前往擁有指令碼和產生器應用程式的目錄。

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image4.png)

請輸入「.\\Setup.ps1」來設定您的 Azure 帳戶、建立及設定所有必要的資源，以及開始產生事件。指令碼會隨機挑選一個區域來建立您的資源。如果您要明確指定區域，可以傳遞 -location 參數，如下列範例所示：

**.\\Setup.ps1 -location “Central US”**

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image5.png)

指令碼將會開啟 Microsoft Azure 的 [登入] 頁面。請輸入您的帳戶認證。

請注意，如果您的帳戶能夠存取多個訂用帳戶，系統將會要求您輸入您要用於本教學課程的訂用帳戶名稱。

指令碼可能需要幾分鐘的時間來執行。執行完畢之後，輸出項目應該會類似下方的螢幕擷取畫面。

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image6.PNG)

您也會看到類似下方螢幕擷取畫面的另一個視窗。這個應用程式會把事件傳送至您的事件中樞，因此您需要該應用程式來執行教學課程的練習。所以您在完成本教學課程之前，請務必不要停止這個應用程式，或是關閉這個視窗。

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image7.png)

現在，您應該能夠在 Azure 管理入口網站中看到已建立的所有資源。請前往 <https://manage.windowsazure.com>，並使用您的帳戶認證來登入。

### 事件中樞

請按一下 Azure 管理入口網站左側的 [服務匯流排] 功能表項目，來查看由上一節的指令碼所建立的事件中樞。

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image8.png)

您將在自己的訂用帳戶中看到所有可用的命名空間。請按一下開頭為「TollData」的項目。(在我們的範例中是 TollData4637388511)。請按一下 [事件中樞] 索引標籤。

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image9.png)

您會在這個命名空間中看到 2 個已建立的事件中樞，分別名為「entry」和「exit」。

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image10.png)

### Azure 儲存體容器

請按一下 Azure 管理入口網站左側的 [儲存體] 功能表項目，來查看在實驗室中使用的儲存體容器。

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image11.png)

請按一下開頭為「tolldata」的項目。(在我們的範例中是 tolldata4637388511)。請開啟 [容器] 索引標籤來查看已建立的容器。

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image12.png)

請按一下 [tolldata] 容器來查看已上傳，且擁有車輛登記資料的 JSON 檔案。

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image13.png)

### Azure SQL Database

請按一下 Azure 管理入口網站左側的 [SQL Database] 功能表項目，來查看將在實驗室中使用的 Azure SQL Database。

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image14.png)

請按一下 [TollDataDB]。

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image15.png)

請複製伺服器的名稱，但不用複製連接埠號碼 (例如 [伺服器名稱].database.windows.net)。

## 從 Visual Studio 連線至資料庫

我們將使用 Visual Studio 來存取輸出資料庫中的查詢結果。

如何從 Visual Studio 連線到 Azure 資料庫 (目的地)：

1) 開啟 Visual Studio，然後按一下 [工具]，再按一下 [連接到資料庫] 功能表項目。

2) 如果畫面出現提示，請選取 [Microsoft SQL Server] 來做為資料來源

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image16.png)

3) 在 [伺服器名稱] 欄位中，貼上您在上一節從 Azure 入口網站複製的 SQL Server 名稱 (也就是 [伺服器名稱].database.windows.net)

4) 在 [驗證] 欄位中，選擇 [SQL Server 驗證]

5) [登入名稱] 輸入「tolladmin」，[登入密碼] 輸入「123toll!」

6) 選擇 [TollDataDB] 來做為資料庫

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image17.jpg)
    
7) 按一下 [確定]。

8) 開啟 [伺服器總管]

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image18.png)
  
9) 在 TollDataDB 資料庫中看到 4 個已建立的資料表。
  
![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image19.jpg)
  
## 事件產生器：TollApp 範例專案

PowerShell 指令碼會自動利用 TollApp 範例應用程式來開始傳送事件，因此您不需要執行任何額外的步驟。

然而，如果您對實作的細節有興趣，可以在 GitHub 的 [samples/TollApp](https://github.com/streamanalytics/samples/tree/master/TollApp) 之下找到 TollApp 應用程式的原始程式碼。

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image20.png)

##建立串流分析工作

請在 Azure 入口網站中開啟 [串流分析]，然後按一下頁面左下角的 [新增] 來建立新的分析工作。

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image21.png)

請按一下 [快速建立]，然後選取指令碼所建立的其他資源所在的相同區域。

對於 [區域監視儲存體帳戶] 設定，請選取 [建立新的儲存體帳戶]，並給予它任何唯一的名稱。Azure 串流分析會使用此帳戶來儲存您未來所有工作的監視資訊。

請按一下頁面底部的 [建立串流分析工作]。

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image22.png)

## 定義輸入來源

請按一下入口網站中已建立的分析工作。

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image23.jpg)

請開啟 [輸入] 索引標籤以定義來源資料。

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image24.jpg)

請按一下 [加入輸入]。

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image25.png)

請選取第一頁的 [資料流]。

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image26.png)

請選取精靈的第二頁上的 [事件中樞]。

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image27.png)

請輸入「EntryStream」來做為輸入別名。

請按一下 [事件中樞] 下拉式清單，然後選取開頭為「TollData」的項目 (例如 TollData9518658221)。

請選取 [entry] 來做為事件中樞的名稱，並選取 [all] 來做為事件中樞原則名稱。

您的設定將看起來會像是：

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image28.png)

請前往下一頁，然後選取 [JSON] 及 [UTF8] 編碼。

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image29.png)

請按一下對話方塊底部的 [確定] 來完成精靈。

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image30.jpg)

您必須依照相同順序的步驟，為 Exit 事件的串流建立第二個事件中樞輸入。請確認在第 3 頁輸入下方螢幕擷取畫面中的值。

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image31.png)

現在您已經定義了 2 個輸入資料流：

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image32.jpg)

接下來，我們將利用車輛登記資料，來為 Blob 檔案新增「參考」資料輸入。

請按一下 [加入輸入]，然後選取 [參考資料]。

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image33.png)

請在下一個頁面選取開頭為「tolldata」的儲存體帳戶。容器名稱必須是「tolldata」，且 [路徑格式] 底下的 Blob 名稱應該是「registration.json」。這個檔案名稱會區分大小寫，且應該全是小寫。

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image34.png)

請在下一頁選取下圖所示的值，然後按一下 [確定] 來完成精靈。

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image35.png)

現在，所有輸入都已經定義了。

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image36.jpg)

## 定義輸出

請前往 [輸出] 索引標籤，然後按一下 [加入輸出]。

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image37.jpg)

請選擇 [SQL Database]。

請選取在＜從 Visual Studio 連線至資料庫＞一節中所使用的伺服器名稱。資料庫名稱應該是 TollDataDB。

請輸入「tolladmin」來做為使用者名稱，以及「123toll!」來做為密碼。資料表名稱應該已經設定為「TollDataRefJoin」。

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image38.jpg)

## Azure 串流分析查詢

[查詢] 索引標籤包含會針對傳入資料進行轉換的 SQL 查詢。

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image39.jpg)

在本教學課程中，我們會嘗試回答幾個通行費資料相關的業務問題，並建構可在 Azure 串流分析中使用的串流分析查詢來提供相關的答案。

在開始我們的第一個 Azure 串流分析工作之前，讓我們來探索幾個案例和查詢語法。

## Azure 串流分析查詢語言簡介
-----------------------------------------------------

假設我們需要計算進入某個收費亭的車輛數目。由於這是連續的事件串流，我們必須定義一個「時段」。因此我們必須把問題修改成「每 3 分鐘進入某個收費亭的車輛數目」。這通常稱為輪轉計數。

我們來看看能回答這個問題的 Azure 串流分析查詢：

    SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*) AS Count
    FROM EntryStream TIMESTAMP BY EntryTime
    GROUP BY TUMBLINGWINDOW(minute, 3), TollId

如上所示，Azure 串流分析使用類似 SQL 的查詢語言，再加上幾個其他的擴充功能，來啟用查詢在時間方面的指定功能。

如需詳細資訊，請參閱 MSDN 中的[時間管理](https://msdn.microsoft.com/library/azure/mt582045.aspx)，以及查詢中所用的[時段](https://msdn.microsoft.com/library/azure/dn835019.aspx)建構。

## 測試 Azure 串流分析查詢

既然我們已經編寫了第一個 Azure 串流分析查詢，現在該利用位於您 TollApp 資料夾中下列路徑的範例資料檔案來測試查詢了：

**..\\TollApp\\TollApp\\Data**

這個資料夾包含下列檔案：

1.  Entry.json

2.  Exit.json

3.  Registration.json

## 問題 1：進入某個收費亭的車輛數目

請開啟 Azure 管理入口網站，然後瀏覽至您建立的 Azure 串流分析工作。請開啟 [查詢] 索引標籤，然後複製/貼上前一節中的查詢。

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image40.png)

如要針對範例資料來驗證這個查詢，請按一下 [測試] 按鈕。請在隨後開啟的對話方塊中瀏覽至 Entry.json，這個檔案包含來自 EntryTime 事件串流的範例資料。

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image41.png)

請驗證查詢的輸出與下列預期的結果相同：

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image42.jpg)

## 問題 2：回報每輛車通過收費亭的總時數

我們想知道車輛經過付費亭所需的平均時間，以評估付費亭的效率和客戶經驗。

因此我們必須聯結包含 EntryTime 的串流與包含 ExitTime 的串流。我們將會聯結 TollId 和 LicencePlate 資料欄的串流。JOIN 運算子需要指定時間性的彈性時間，來說明已聯結事件之間可接受的時間差。我們將使用 DATEDIFF 函式來指定事件之間的時間差不能超過 15 分鐘。我們也會將 DATEDIFF 函式套用到 Exit 及 Entry 時間，以便計算車輛經過收費亭的實際時間。請注意在 JOIN 條件相較之下，當 DATEDIFF 用在 SELECT 陳述式中時，DATEDIFF 使用方式的差異。

    SELECT EntryStream.TollId, EntryStream.EntryTime, ExitStream.ExitTime, EntryStream.LicensePlate, DATEDIFF (minute , EntryStream.EntryTime, ExitStream.ExitTime) AS DurationInMinutes
    FROM EntryStream TIMESTAMP BY EntryTime
    JOIN ExitStream TIMESTAMP BY ExitTime
    ON (EntryStream.TollId= ExitStream.TollId AND EntryStream.LicensePlate = ExitStream.LicensePlate)
    AND DATEDIFF (minute, EntryStream, ExitStream ) BETWEEN 0 AND 15

如要測試此查詢，請更新您工作中 [查詢] 索引標籤上的查詢：

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image43.jpg)

請按一下 [測試]，並指定 EntryTime 和 ExitTime 的範例輸入檔。

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image44.png)

請按一下核取方塊來測試查詢並檢視輸出：

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image45.png)

## 問題 3：回報所有車輛登記已過期的商用車

Azure 串流分析可使用靜態的資料快照，來與時間資料流聯結。如要示範該功能，我們將使用下列的範例問題。

如果商用車已經向收費公司登記，就直接通過收費亭，不用停車接受檢查。我們將使用商用車登記查詢資料表，來辨識所有車輛登記已過期的商用車。

    SELECT EntryStream.EntryTime, EntryStream.LicensePlate, EntryStream.TollId, Registration.RegistrationId
    FROM EntryStream TIMESTAMP BY EntryTime
    JOIN Registration
    ON EntryStream.LicensePlate = Registration.LicensePlate
    WHERE Registration.Expired = '1'

請注意，如要測試利用參考資料的查詢，就必須定義參考資料的輸入來源，而我們已經在步驟 5 完成這項作業。

若要測試此查詢，請把查詢貼到 [查詢] 索引標籤，然後按一下 [測試]，並指定 2 個輸入來源：

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image46.png)

請檢視查詢的輸出：

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image47.png)

## 啟動串流分析工作


既然我們已經編寫了第一個 Azure 串流分析查詢，現在該結束設定並啟動工作了。儲存問題 3 的查詢，這會產生符合我們輸出資料表 **TollDataRefJoin** 結構描述的輸出。

瀏覽至工作儀表板，然後按一下 [啟動]。

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image48.jpg)

在隨後出現的對話方塊上，把 [開始輸出] 時間變更為 [自訂時間]，然後編輯小時的數值，並把它設定在 1 小時前。這確保我們會從教學課程開頭時我們開始產生事件的那一刻，開始處理事件中樞的所有事件。現在，請按一下核取記號來啟動工作。

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image49.png)

啟動工作可能需要幾分鐘的時間，串流分析最上層的頁面會顯示狀態。

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image50.jpg)

## 在 Visual Studio 中查看結果

請開啟 Visual Studio 的伺服器總管，然後用滑鼠右鍵按一下 [TollDataRefJoin] 資料表。請選取 [顯示資料表資料] 來查看您工作的輸出。

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image51.jpg)

## 相應放大 Azure 串流分析工作

我們將 Azure 串流分析設計成能進行彈性調整，且能夠處理大量資料。Azure 串流分析查詢可以使用 **PARTITION BY** 子句，來告訴系統此步驟將會相應放大。PartitionId 是系統新增的特殊資料欄，且它與輸入 (事件中樞) 的分割識別碼相符。

    SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*)AS Count
    FROM EntryStream TIMESTAMP BY EntryTime PARTITION BY PartitionId
    GROUP BY TUMBLINGWINDOW(minute,3), TollId, PartitionId    

請停止目前的工作，然後更新 [查詢] 索引標籤中的查詢，再開啟 [調整] 索引標籤。

串流處理單元會定義工作能夠接收之計算能力的數量。

請將滑桿移動到 6。

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image52.jpg)

請前往 [輸出] 索引標籤，然後把 SQL 資料表名稱變更為 「TollDataTumblingCountPartitioned」。

現在，如果您啟動工作，Azure 串流分析將能夠把工作分散到更多的計算資源上，並達到更高的輸送量。請注意，TollApp 應用程式也會同時傳送已依照 TollId 來分割的事件。

## 監控

[監視] 索引標籤包含執行中工作的統計資料。

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image53.png)

您可以從 [儀表板] 索引標籤來存取作業記錄檔。

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image54.jpg)

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image55.png)

如要查看特定事件的其他資訊，請選取該事件，然後按一下 [詳細資料] 按鈕。

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image56.png)

## 結論

在本教學課程中，我們提供了 Azure 串流分析服務的簡介。我們示範了如何為串流分析工作設定輸入和輸出。我們還利用付費資料的案例，來解釋在資料空間會不斷變化時所引發的常見問題類型，以及如何在 Azure 串流分析中利用類似 SQL 的簡單查詢來解決這些問題。我們說明了要用來處理時間資料的 SQL 延伸模組建構。我們示範了如何聯結不同的資料流，以及如何利用靜態的參考資料來豐富資料流的內容。我們解釋了如何相應放大查詢，來達到更高的輸送量。

雖然本教學課程簡介的涵蓋範圍相當大，但它絕對不是完整的說明。您可以在[這裡](stream-analytics-stream-analytics-query-patterns.md)找到更多利用 SAQL 語言的查詢模式。如要深入了解 Azure 串流分析，請參閱[線上文件](https://azure.microsoft.com/documentation/services/stream-analytics/)。

## 清理您的 Azure 帳戶

請從 Azure 入口網站停止串流分析工作。

Setup.ps1 指令碼會建立 2 個 Azure 事件中樞，以及 Azure SQL 資料庫伺服器。下列指示將協助您在本教學課程的結尾清理資源。

請在 PowerShell 視窗中輸入「.\\Cleanup.ps1」，來啟動會刪除本教學課程所使用資源的指令碼。

請注意，資源是依照名稱來識別。請確認您在確認刪除之前，已仔細檢閱每個項目。

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image57.png)

<!---HONumber=AcomDC_0323_2016-->