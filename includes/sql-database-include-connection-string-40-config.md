
<!--
includes/sql-database-include-connection-string-40-config.md

Latest Freshness check:  2015-09-04 , GeneMi.

## Connection string
-->


### 連接字串安全性的範例組態檔


將連接字串做為常值放在 C# 程式碼中並不妥當。將連接字串放在組態檔中會比較好。您可以隨時在那裡編輯字串而不需要重新編譯。

假設已編譯的 C# 程式名為 **ConsoleApplication1.exe**，而此 .exe 位於 **bin\\debug** 目錄中。

在此範例中，連接字串中大部分的組件會儲存在名稱恰好為 **ConsoleApplication1.exe.config** 的組態檔中。此組態檔也必須位在 **bin\\debug** 中。

在下列組態檔的 XML 中您會看到名為 **ConnectionString4NoUserIDNoPassword** 的連接字串。C# 程式碼會尋找此字串。

您必須編輯預留位置中的實際名稱：

- {your\_serverName\_here}
- {your\_databaseName\_here}



		<?xml version="1.0" encoding="utf-8" ?>
		<configuration>
		    <startup> 
		        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
		    </startup>
		
		    <connectionStrings>
		        <clear />
		        <add name="ConnectionString4NoUserIDNoPassword"
		        providerName="System.Data.ProviderName"
		
		        connectionString=
				"Server=tcp:{your_serverName_here}.database.windows.net,1433;
				Database={your_databaseName_here};
				Connection Timeout=30;
				Encrypt=True;
				TrustServerCertificate=False;" />
		    </connectionStrings>
		</configuration>



此圖中我們選擇省略兩個參數：

- User ID={your\_userName\_here};
- Password={your\_password\_here};


您可以加以包含，但有時候讓程式從使用者的鍵盤輸入來取得這些值會比較好。這要看狀況。



<!--
These three includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-connection-string-20-portalshots.md
includes/sql-database-include-connection-string-30-compare.md
includes/sql-database-include-connection-string-40-config.md
-->

<!---HONumber=Sept15_HO2-->