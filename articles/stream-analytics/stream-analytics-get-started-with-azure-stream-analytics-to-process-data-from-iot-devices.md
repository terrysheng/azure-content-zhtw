<properties
	pageTitle="開始使用 Azure 串流分析來處理 IoT 裝置的資料 | 串流分析"
	description="IoT 感應器標記和具有串流分析的資料串流與即時資料處理"
	services="stream-analytics"
	documentationCenter=""
	authors="jeffstokes72"
	manager="paulettm"
	editor="cgronlun"
/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="hero-article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="02/11/2016"
	ms.author="jeffstok"
/>

# 開始使用 Azure 串流分析來處理 IoT 裝置的資料

在本教學課程中，您將學習建立串流處理邏輯，以從物聯網 (IoT) 裝置收集資料。我們將使用真實世界的物聯網 (IoT) 使用案例以示範如何快速且經濟地建置您的解決方案。

## 必要條件

-   [Azure 訂閱](https://azure.microsoft.com/pricing/free-trial/)
-   範例查詢和資料檔案可從 [GitHub](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/GettingStarted) 下載

## 案例

Contoso 是一家工業自動化空間的製造公司，他們已完全自動化其製造程序。這家製造廠的機械裝置具有感應器，可以發出即時資料串流。在此案例中，生產現場經理想要取得感應器資料的即時深入資訊，來尋找模式並且對其採取動作。我們將對感應器資料使用串流分析查詢語言 (SAQL)，以在內送資料串流中找出感興趣的模式。

這裡的資料是從 Texas Instrument Sensor Tag 裝置產生。

![Texas Instruments Sensor Tag](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-01.jpg)

資料的裝載的格式是 JSON，如下所示：

    
	{
    	"time": "2016-01-26T20:47:53.0000000",  
	    "dspl": "sensorE",  
    	"temp": 123,  
	    "hmdt": 34  
	}  
    
在真實世界的案例中，您會有 100 個此類感應器，產生事件作為串流。在理想情況下會有執行一些程式碼的閘道器，將這些事件推送至 [Azure 事件中樞](https://azure.microsoft.com/services/event-hubs/)。串流分析作業會使用事件中樞的這些事件，並且在查詢時執行即時分析，並且將結果傳送至所需的輸出。

在此快速入門指南中，我們提供您從實際 SensorTag 裝置擷取的範例資料檔，您可以對其執行不同的查詢，並查看其結果。在後續教學課程中，您將了解如何將您的作業連接至輸入和輸出，並將它們部署到 Azure 服務。

## 建立串流分析工作

請在 [Azure 入口網站](http://manage.windowsazure.com)中開啟 [串流分析]，然後按一下頁面左下角的 [新增] 來建立新的分析作業。

![](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-02.png)

按一下 [快速建立]。

對於 [區域監視儲存體帳戶] 設定，請選取 [建立新的儲存體帳戶]，並給予它任何唯一的名稱。Azure 串流分析會使用此帳戶來儲存您未來所有工作的監視資訊。

> [AZURE.NOTE] 您應該只對每個區域建立此儲存體帳戶一次，此儲存體會跨該區域中建立的所有串流分析作業共用。

請按一下頁面底部的 [建立串流分析作業]。

![](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-03.jpg)

## Azure 串流分析查詢

按一下 [查詢] 索引標籤以移至 [查詢編輯器]。[查詢] 索引標籤包含會針對傳入資料進行轉換的 SQL 查詢。

## 封存您的原始資料

查詢的最簡單形式是傳遞，此形式會將所有輸入資料封存至其指定的輸出。

![](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-04.png)

立即從 [GitHub](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/GettingStarted) 將範例資料檔案下載至您的電腦中的位置。從 **PassThrough.txt** 檔案複製並貼上查詢。按一下下面的 [測試] 按鈕，然後從您的下載位置選取名為 **HelloWorldASA-InputStream.json** 的資料檔案。

![](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-05.png)

![](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-06.png)

您可以在以下的瀏覽器中看到查詢的結果。

![](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-07.png)

## 根據條件清理資料

讓我們試著根據條件篩選結果。我們想要僅針對來自 "SensorA" 的事件顯示結果。查詢位於 **Filtering.txt** 檔案。

![](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-08.png)

請注意，這裡我們要比較字串值且其區分大小寫。按一下 [重新執行] 按鈕以執行查詢。查詢應該僅針對 1860 個事件傳回 389 個資料列。

![](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-09.png)

## 觸發商務工作流程的警示

現在讓我們的查詢更有趣。對於每一個類型的感應器，如果我們想要監視每 30 秒時段的平均溫度，並且只顯示平均溫度高於 100 度的結果，我們會撰寫下列查詢，然後按一下 [重新執行] 來查看結果。查詢位於 **ThresholdAlerting.txt** 檔案。

![](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-10.png)

如您所見，現在結果只包含 245 個資料列，其中的感應器的平均溫度高於 100。在此查詢中，我們已依據 dspl 分組事件串流，dspl 是感應器名稱，並且有 30 秒的**輪轉視窗**。當我們執行這類暫時查詢時，表明我們想要的進行時間很重要。使用 **TIMESTAMP BY** 子句，我們已指定 [時間] 資料行做為所有暫時計算的進行時間方式。如需詳細資訊，請閱讀我們的[時間管理](https://msdn.microsoft.com/library/azure/mt582045.aspx)和[時間範圍](https://msdn.microsoft.com/library/azure/dn835019.aspx) MSDN 主題。

![](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-11.png)

## 尋找缺少的模式

如何寫入查詢來尋找缺少的模式？ 例如，讓我們找出上次感應器已傳送資料，但是接下來一分鐘未傳送任何事件的情形。查詢位於 **AbsenseOfEvent.txt** 檔案。

![](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-12.png)

此處，我們使用 **LEFT OUTER JOIN** 至相同的資料串流 (自我聯結)。對於內部聯結，只會在找到相符項目時傳回結果。但是對於 **LEFT OUTER** 聯結，如果聯結左側的事件不相符，則會針對右側資料列的所有資料行傳回具有 NULL 的資料列。這項技術對於尋找不存在的事件很有用。如需 [JOIN](https://msdn.microsoft.com/library/azure/dn835026.aspx) 的詳細資訊，請參閱 MSDN 文件。

![](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-13.png)

## 結論

本教學課程基本上可協助您開始撰寫不同的 SAQL 查詢，並且在瀏覽器中查看不同模式資料的結果。但是，這只是剛開始。您還可以使用串流分析執行更多功能。接下來您將學習將串流分析作業連接至輸入和輸出，並且將它部署至 Azure。您可以使用我們的[學習圖](https://azure.microsoft.com/documentation/learning-paths/stream-analytics/)指南開始探索更多串流分析，如需撰寫查詢的詳細資訊，請閱讀[常見查詢模式](./stream-analytics-stream-analytics-query-patterns.md#query-example-detect-the-absence-of-events)上的文章。

<!---HONumber=AcomDC_0224_2016-->