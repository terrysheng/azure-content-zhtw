<properties 
	pageTitle="利用 C# 重試邏輯連接到 SQL Database | Microsoft Azure" 
	description="C# 範例中包括重試邏輯，可與 Azure SQL Database 穩健互動。" 
	services="sql-database" 
	documentationCenter="" 
	authors="MightyPen" 
	manager="jeffreyg" 
	editor=""/>


<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/01/2015" 
	ms.author="genemi"/>


# 程式碼範例：C# 中用於連接到 SQL Database 的重試邏輯


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


本主題提供可示範自訂重試邏輯的 C# 程式碼範例。重試邏輯的設計目的在於正常處理暫時錯誤，或如果程式等候幾秒並重試後通常會消失的暫時性錯誤。


您用來連接到您的本機 Microsoft SQL Server 的 ADO.NET 類別也可以連接到 Azure SQL Database。不過，ADO.NET 類別本身無法提供在生產環境使用所需的所有穩定性和可靠性。用戶端程式可能會遇到暫時性錯誤，用戶端程式應該會從失敗中無訊息且正常自行復原。


暫時性錯誤的一些範例：


- 透過網際網路的連線受限於短暫的網路中斷，之後可以重新建立連線。

- 雲端運算牽涉到負載平衡，它可以短暫封鎖對連線或查詢的嘗試。


## 識別暫時性錯誤


您的程式必須區分暫時性錯誤與持續性錯誤。如果您的程式對於目標資料庫名稱拼字錯誤，「找不到這類資料庫」錯誤將會持續並且每次重新執行該程式將會再次出現。


分類為暫時性錯誤的錯誤號碼清單可自此取得：


- [SQL Database 用戶端程式的錯誤訊息](sql-database-develop-error-messages.md#bkmk_connection_errors)
 - 請參閱＜暫時性錯誤、連線中斷錯誤＞一節。


## 程式碼範例


目前主題中的 C# 程式碼範例包含可處理暫時性錯誤的自訂偵測和重試邏輯。


程式碼範例會遵循幾個基本指導方針或適用的建議，而不論用來與 Azure SQL Database 互動的技術為何。您可以在此處看到一般建議：


- [連接到 SQL Database：連結、最佳作法和設計方針](sql-database-connect-central-recommendations.md)


C# 程式碼範例包含一個名為 Program.cs 的檔案。範例的程式碼會貼在下一個區段。


### 擷取與編譯程式碼範例


您可以使用下列步驟編譯此範例：


1. 在[免費的 Visual Studio Community 版本](https://www.visualstudio.com/products/visual-studio-community-vs)中，透過 C# 主控台應用程式範本建立新的專案。
 - [檔案] > [新增] > [專案] > [已安裝] > [範本] > [Visual C#] > [Windows] > [傳統桌面] > [主控台應用程式]
 - 將專案命名為 **RetryAdo2**。

2. 開啟 [方案總管] 窗格。
 - 查看專案的名稱。
 - 查看 Program.cs 檔案的名稱。

3. 開啟 Program.cs 檔案。

4. 使用下列程式碼區塊中的程式碼，取代 Program.cs 檔案的所有內容。

5. 按一下 [建置] 功能表 > [建置解決方案]。


#### 要貼上的 C# 原始程式碼


將此程式碼到貼到您的 **Program.cs** 檔案中。


接著，您必須編輯伺服器名稱、密碼等項目的字串。您可以在名為 **GetSqlConnectionStringBuilder** 的方法中找到這些字串。


```
using System;   // C#
using G = System.Collections.Generic;
using D = System.Data;
using C = System.Data.SqlClient;
using X = System.Text;
using H = System.Threading;

namespace RetryAdo2
{
	class Program
	{
		static void Main(string[] args)
		{
			Program program = new Program();
			bool returnBool;

			returnBool = program.Run(args);
			if (returnBool == false)
			{
				Console.WriteLine("Something failed.  :-( ");
			}
			return;
		}

		bool Run(string[] _args)
		{
			C.SqlConnectionStringBuilder sqlConnectionSB;
			C.SqlConnection sqlConnection;
			D.IDbCommand dbCommand;
			D.IDataReader dataReader;
			X.StringBuilder sBuilder = new X.StringBuilder(256);
			int retryIntervalSeconds = 10;
			bool returnBool = false;

			Program program = new Program();

			for (int tries = 1; tries <= 5; tries++)
			{
				try
				{
					if (tries > 1)
					{
						H.Thread.Sleep(1000 * retryIntervalSeconds);
						retryIntervalSeconds = Convert.ToInt32(retryIntervalSeconds * 1.5);
					}

					program.GetSqlConnectionStringBuilder(out sqlConnectionSB);

					sqlConnection = new C.SqlConnection(sqlConnectionSB.ToString());

					dbCommand = sqlConnection.CreateCommand();
					dbCommand.CommandText = @"
SELECT TOP 3
      ob.name,
      CAST(ob.object_id as nvarchar(32)) as [object_id]
   FROM sys.objects as ob
   WHERE ob.type='IT'
   ORDER BY ob.name;";

					sqlConnection.Open();
					dataReader = dbCommand.ExecuteReader();

					while (dataReader.Read())
					{
						sBuilder.Length = 0;
						sBuilder.Append(dataReader.GetString(0));
						sBuilder.Append("\t");
						sBuilder.Append(dataReader.GetString(1));

						Console.WriteLine(sBuilder.ToString());
					}
					returnBool = true;
					break;
				}

				catch (C.SqlException sqlExc)
				{
					if (this.m_listTransientErrorNumbers.Contains(sqlExc.Number) == true)
					{ continue; }
					else
					{ throw sqlExc; }
				}
			}
			return returnBool;
		}

		void GetSqlConnectionStringBuilder(out C.SqlConnectionStringBuilder _sqlConnectionSB)
		{
			// Prepare the connection string to Azure SQL Database.
			_sqlConnectionSB = new C.SqlConnectionStringBuilder();

			// Change these values to your values.
			_sqlConnectionSB["Server"] = "tcp:myazuresqldbserver.database.windows.net,1433";
			_sqlConnectionSB["User ID"] = "MyLogin";  // "@yourservername"  as suffix sometimes.
			_sqlConnectionSB["Password"] = "MyPassword";
			_sqlConnectionSB["Database"] = "MyDatabase";

			// Leave these values as they are.
			_sqlConnectionSB["Trusted_Connection"] = false;
			_sqlConnectionSB["Integrated Security"] = false;
			_sqlConnectionSB["Encrypt"] = true;
			_sqlConnectionSB["Connection Timeout"] = 30;
		}

		Program()   // Constructor.
		{
			int[] arrayOfTransientErrorNumbers =
				{ 4060, 10928, 10929, 40197, 40501, 40613, 49918, 49919, 49920
					//,11001   // 11001 for testing, pretend network error is transient.
				};
			m_listTransientErrorNumbers = new G.List<int>(arrayOfTransientErrorNumbers);
		}

		private G.List<int> m_listTransientErrorNumbers;
	}
}
```


### 執行程式


**RetryAdo2.exe** 可執行檔不會輸入任何參數。在 Visual Studio 中執行.exe：


1. 在 **Main** 方法的 **return;** 陳述式上設定中斷點。

2. 按一下綠色的開始箭號按鈕。主控台視窗隨即顯示。

3. 偵錯工具停止在 **Main** 的結尾時，切換至主控台視窗。

4. 查看三個資料列，其內容可能與下列相同：


```
database_firewall_rules_table   245575913
filestream_tombstone_2073058421 2073058421
filetable_updates_2105058535    2105058535
```


## 測試您的重試邏輯


測試重試邏輯的一種現成方法如下：


1. 暫時將 **11001** 加入 **SqlConnection.Number** 值的集合中，這些值應會被視為暫時性錯誤。

2. 重新編譯您的程式。

3. 中斷用戶端電腦的網路連線。

4. 在偵錯工具中執行您的程式，並在迴圈中設定中斷點。
 - 第一次迴圈會在發生錯誤 11001 時失敗。

5. 當程式在第二次迴圈期間停在中斷點時，請將電腦重新連線至網路。

6. 繼續執行您的程式。程式會在第二次迴圈期間成功執行。


### 另一種測試選項


另一種方式，是將邏輯加入您的程式，以辨識 "test" 的命令列參數值。為了回應參數，程式會執行以下動作：


1. 暫時附加多餘的字母，刻意拼錯 SQL Database 伺服器名稱。

2. 暫時將 **40615** 加入暫時性錯誤清單中。

3. 開始第二次迴圈 (也就是第一次重試迴圈) 時，程式會執行以下動作：
 - 復原伺服器的拼字錯誤。
 - 從暫時性錯誤清單中移除 40615。


以 "test" 參數執行程式，並確認程式雖然在第一次迴圈中失敗，但到了第二次迴圈已能順利執行。


## 相關連結


- [SQL Database 的用戶端快速入門程式碼範例](sql-database-develop-quick-start-client-code-samples.md)

<!---HONumber=Oct15_HO1-->