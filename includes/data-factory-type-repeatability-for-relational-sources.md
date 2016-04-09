## 在複製期間的重複性

複製資料自/至關聯式存放區時，您需要留意重複性以避免非預期的結果。

**注意：**可以根據指定的重試原則在 Azure Data Factory 中自動重新執行配量。建議您設定重試原則，以防止暫時性失敗。由此可知，重複性是移動資料時的重要層面。

**做為來源：**
> [AZURE.NOTE] 下面的範例是針對 Azure SQL，但是也適用於任何支援矩形資料集的資料存放區。您可能必須針對資料存放區調整來源的 [類型] 和 **query** 屬性 (例如：query 而不是 sqlReaderQuery)。   

在多數情況下，從關聯式存放區讀取時，您會希望只讀取對應該配量的資料。使用 Azure Data Factory 中提供的 WindowStart 和 WindowEnd 變數即可達到此目的。請在此[「排程和執行」](../articles/data-factory/data-factory-scheduling-and-execution.md)文章中閱讀關於 Azure Data Factory 中的變數和函數。範例：
	
	  "source": {
	    "type": "SqlSource",
	    "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm\\'', WindowStart, WindowEnd)"
	  },

上述查詢會讀取落在配量持續時間範圍的「MyTable」資料。重新執行此配量也能確保此行為。

在其他情況下，您可能想要閱讀整份資料表 (假設只是單次移動)，並可能會如下所示定義 sqlReaderQuery：

	
	"source": {
	            "type": "SqlSource",
	            "sqlReaderQuery": "select * from MyTable"
	          },
	

<!----HONumber=AcomDC_0224_2016-->