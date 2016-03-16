<properties 
	pageTitle="Data Factory 函式與系統變數 | Microsoft Azure" 
	description="提供 Azure Data Factory 函式與系統變數清單" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"
	services="data-factory"
/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/18/2016" 
	ms.author="spelluru"/>

# Azure Data Factory - 函式與系統變數
本文提供 Azure Data Factory 支援之函式和變數的相關資訊。
  
## Data Factory 系統變數

變數名稱 | 說明 | 物件範圍 | JSON 範圍和使用案例
------------- | ----------- | ------------ | ------------------------
WindowStart | 目前活動執行時段的時間間隔開始 | 活動 | <ol><li>指定資料選取範圍查詢。請參閱[資料移動活動](data-factory-data-movement-activities.md)文章中參考的連接器文章。</li><li>傳遞參數給 Hive 指令碼 (如上所示的範例)。</li>
WindowEnd | 目前活動執行時段的時間間隔結束 | 活動 | 同上
SliceStart | 所產生之資料配量的時間間隔開始 | 活動<br/>資料集 | <ol><li>指定使用 [Azure Blob](data-factory-azure-blob-connector.md) 和[檔案系統資料集](data-factory-onprem-file-system-connector.md)時的動態資料夾路徑與檔案名稱。</li><li>使用 Data Factory 函式在活動輸入集合中指定輸入相依性。</li></ol>
SliceEnd | 所產生之目前資料配量的時間間隔結束 | 活動<br/>資料集 | 同上。 

> [AZURE.NOTE] 目前 Data Factory 需要活動中指定的排程與輸出資料集之可用性中指定的排程完全相符。這表示 WindowStart、WindowEnd 和 SliceStart 與 SliceEnd 一律對應到相同的時間期間和單一輸出配量。
 
## Data Factory 函式 

您可以針對下列目的搭配使用 Data Factory 中的函式與上述系統變數：

1.	指定資料選取範圍查詢 (請參閱[資料移動活動](data-factory-data-movement-activities.md)文章中參考的連接器文章)。

	針對資料選取範圍查詢和活動與資料集中的其他屬性，叫用 Data Factory 函式的語法是：**$$<function>**。  
2. 使用 Data Factory 函式在活動輸入集合中指定輸入相依性 (請參閱上述範例)。

	指定輸入相依性運算式不需要 $$。

在下列範例中，JSON 檔案中的 **sqlReaderQuery** 屬性指派給 **Text.Format** 函式所傳回的值。此範例也會使用名為 **WindowStart** 的系統變數，它代表活動執行時段的開始時間。
	
	{
	    "Type": "SqlSource",
	    "sqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartTime = \\'{0:yyyyMMdd-HH}\\'', WindowStart)"
	}

### 函式

下表列出 Azure Data Factory 中的所有函式：

類別 | 函式 | 參數 | 說明
-------- | -------- | ---------- | ----------- 
時間 | AddHours(X,Y) | X：DateTime <p>Y：int</p> | 將 Y 小時新增至指定時間 X。<p>範例：9/5/2013 12:00:00 PM + 2 小時 = 9/5/2013 2:00:00 PM</p>
時間 | AddMinutes(X,Y) | X：DateTime <p>Y：int</p> | 將 Y 分鐘新增至 X。<p>範例：9/15/2013 12: 00:00 PM + 15 分鐘 = 9/15/2013 12: 15:00 PM</p>
時間 | StartOfHour(X) | X：Datetime | 取得由 X 的小時元件代表的小時開始時間。<p>範例：9/15/2013 05: 10:23 PM 的 StartOfHour 是 9/15/2013 05: 00:00 PM</p>
Date | AddDays(X,Y) | X：DateTime<p>Y：int</p> | 將 Y 天數新增至 X。<p>範例：9/15/2013 12:00:00 PM + 2 天 = 9/17/2013 12:00:00 PM</p>
Date | AddMonths(X,Y) | X：DateTime<p>Y：int</p> | 將 Y 月數新增至 X。<p>範例：9/15/2013 12:00:00 PM + 1 個月 = 10/15/2013 12:00:00 PM</p> 
Date | AddQuarters(X,Y) | X：DateTime <p>Y：int</p> | 將 Y * 3 個月新增至 X。<p>範例：9/15/2013 12:00:00 PM + 1 季 = 12/15/2013 12:00:00 PM</p>
Date | AddWeeks(X,Y) | X：DateTime<p>Y：int</p> | 將 Y * 7 天新增至 X<p>範例：9/15/2013 12:00:00 PM + 1 週 = 9/22/2013 12:00:00 PM</p>
Date | AddYears(X,Y) | X：DateTime<p>Y：int</p> | 將 Y 年新增至 X。<p>範例：9/15/2013 12:00:00 PM + 1 年 = 9/15/2014 12:00:00 PM</p>
Date | Day(X) | X：DateTime | 取得 X 的日元件。<p>範例：9/15/2013 12:00:00 PM 的日期是 9。</p>
Date | DayOfWeek(X) | X：DateTime | 取得 X 的週元件。<p>範例：9/15/2013 12:00:00 PM 的星期幾是星期日。</p>
Date | DayOfYear(X) | X：DateTime | 取得 X 的年元件代表的一年當中的日期。<p>範例：<br/>12/1/2015：2015 年的第 335 天<br/>12/31/2015：2015 年的第 365 天<br/>12/31/2016：2016 年的第 366 天 (閏年)</p>
Date | DaysInMonth(X) | X：DateTime | 取得參數 X 的月元件代表的月份中的日期。<p>範例：9/15/2013 的 DaysInMonth 是 30，因為 9 月份有 30 天。</p>
Date | EndOfDay(X) | X：DateTime | 取得 X 的結尾天數 (日元件) 代表的日期時間。<p>範例：9/15/2013 05:10:23 PM 的 EndOfDay 是 9/15/2013 11:59:59 PM。</p>
Date | EndOfMonth(X) | X：DateTime | 取得由參數 X 的月元件代表的月底。<p>範例：9/15/2013 05:10:23 PM 的 EndOfMonth 是 9/30/2013 11:59:59 PM (代表 9 月份月底的日期時間)</p>
Date | StartOfDay(X) | X：DateTime | 取得參數 X 的日元件代表的日期開始。<p>範例：9/15/2013 05:10:23 PM 的 StartOfDay 是 9/15/2013 12:00:00 AM。</p>
DateTime | From(X) | X：字串 | 將字串 X 剖析為日期時間。
DateTime | Ticks(X) | X：DateTime | 取得參數 X 的刻度屬性。一個刻度等於 100 奈秒。這個屬性的值代表從 0001 年 1 月 1 日午夜 12:00:00 經過的刻度數。 
文字 | Format(X) | X：字串變數 | 格式化文字。

#### Text.Format 範例

	"defines": { 
	    "Year" : "$$Text.Format('{0:yyyy}',WindowStart)",
	    "Month" : "$$Text.Format('{0:MM}',WindowStart)",
	    "Day" : "$$Text.Format('{0:dd}',WindowStart)",
	    "Hour" : "$$Text.Format('{0:hh}',WindowStart)"
	}

請參閱說明可使用的各種格式化選項 (例如：yy 與 yyyy) 的[自訂日期和時間格式字串](https://msdn.microsoft.com/library/8kb3ddd4.aspx)主題。

> [AZURE.NOTE] 在另一個函式中使用函式時，您不需要針對內部函式使用 **$$** 前置詞。例如：$$Text.Format('PartitionKey eq \\'my\_pkey\_filter\_value\\' and RowKey ge \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(SliceStart, -6))。在此範例中，請注意 **$$** 前置詞不能用於 **Time.AddHours** 函式。
  

<!---HONumber=AcomDC_0224_2016-->