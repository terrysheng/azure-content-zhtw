<properties
	pageTitle="透過 C# 查詢連接到 SQL Database | Microsoft Azure"
	description="以 C# 撰寫程式以查詢和連接到 SQL Database。有關 IP 位址、連接字串、安全登入和免費 Visual Studio 的資訊。"
	services="sql-database"
	keywords="c# 資料庫查詢, c# 查詢, 連接到資料庫, SQL C#"
	documentationCenter=""
	authors="MightyPen"
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="get-started-article"
	ms.date="10/09/2015"
	ms.author="genemi"/>


# 以 & #x 23; 撰寫程式以查詢和連接到 SQL Database

> [AZURE.SELECTOR]
- [C#](sql-database-connect-query.md)
- [SSMS](sql-database-connect-query-ssms.md)
- [Excel](sql-database-connect-excel.md)

了解如何以 C# 撰寫程式來查詢和連接到雲端的 Azure SQL Database。

本文會為 Azure SQL Database、C# 和 ADO.NET 的每位新手描述每一個步驟。對 Microsoft SQL Server 和 C# 有豐富經驗的人可略過一些步驟並著重於專屬於 SQL Database 的步驟。


## 必要條件


若要執行 C# 查詢程式碼範例，您必須具備：


- Azure 帳戶和訂用帳戶。您可以註冊[免費試用](https://azure.microsoft.com/pricing/free-trial/)。


- Azure SQL Database 服務上的 **AdventureWorksLT** 示範資料庫。
 - 在數分鐘內[建立示範資料庫](sql-database-get-started.md)。


- Visual Studio 2013 Update 4 (或更新版本)。Microsoft 現在*免費*提供 Visual Studio Community。
 - [Visual Studio Community，下載](http://www.visualstudio.com/products/visual-studio-community-vs)
 - [免費 Visual Studio 的更多選項](http://www.visualstudio.com/products/free-developer-offers-vs.aspx)
 - 或是讓本主題中稍後的[步驟](#InstallVSForFree)描述 [Azure 入口網站](https://portal.azure.com/)如何引導您安裝 Visual Studio。


<a name="InstallVSForFree" id="InstallVSForFree"></a>

&nbsp;

## 步驟 1：免費安裝 Visual Studio Community


如果您需要安裝 Visual Studio，您可以：

- 將瀏覽器瀏覽至提供免費下載及其他選項的 Visual Studio 網頁以免費安裝 Visual Studio Community。或
- 讓 [Azure 入口網站](https://portal.azure.com/)引導您下載網頁，將於稍後描述。


### 透過 Azure 入口網站的 Visual Studio


1. 透過 [Azure 入口網站](https://portal.azure.com/)登入，位於 http://portal.azure.com/。

2. 按一下 [全部瀏覽] > [SQL Database]。刀鋒視窗會開啟該資料庫的搜尋。

3. 在靠近頂端的篩選文字方塊中，開始輸入 **AdventureWorksLT** 資料庫的名稱。

4. 當您看到伺服器上資料庫的資料列時，按一下資料列。刀鋒視窗會為您的資料庫開啟。

5. 為了方便起見，請按一下前述每個刀鋒視窗上的最小化控制項。

6. 按一下資料庫刀鋒視窗頂端附近的 [在 Visual Studio 中開啟] 按鈕。關於 Visual Studio 的新刀鋒視窗會利用連結開啟以安裝 Visual Studio 的位置。

	![[在 Visual Studio 中開啟] 按鈕][20-OpenInVisualStudioButton]

7. 按一下 [社群 (免費)] 連結或類似的連結。新的網頁會隨即新增。

8. 使用新網頁上的連結安裝 Visual Studio。

9. 安裝 Visual Studio 之後，於**在 Visual Studio 中開啟**刀鋒視窗上按一下 [在 Visual Studio 中開啟] 按鈕。Visual Studio 隨即開啟。

10. 以其 [SQL Server 物件總管] 窗格的優點而言，Visual Studio 會要求您填寫對話方塊中的連接字串欄位。
 - 選擇 [SQL Database 驗證]，而非 [Windows 驗證]。
 - 請記得要指定您的 **AdventureWorksLT** 資料庫 (對話方塊中的 [選項] > [連接屬性])。

11. 在 [SQL Server 物件總管] 中，展開資料庫的節點。


## 步驟 2：在 Visual Studio 中建立新專案


在 Visual Studio 中，建立以 C# > Windows > **主控台應用程式**之入門範本為基礎的新專案。


1. 按一下 [**檔案**] > [**新增**] > [**專案**]。**** 對話方塊隨即顯示。

2. 在 [已安裝] 之下，展開 C# 和 Windows，讓**主控台應用程式**選項出現在中間窗格。

	![[新增專案] 對話方塊][30-VSNewProject]

2. 針對 [名稱] 輸入 **ConnectAndQuery\_Example**。按一下 [確定]。


## 步驟 3：為 config 處理新增組件參考


我們的 C# 範例會使用 .NET Framework 組件 **System.Configuration.dll**，因此讓我們新增對它的參考。


1. 在 [方案總管] 窗格中，以滑鼠右鍵按一下 [參考] > [新增參考]。[參考管理員] 視窗隨即出現。

2. 展開 [組件] > [架構]。

3. 捲動然後按一下以反白顯示 **System.Configuration**。確定是否選取其核取方塊。

4. 按一下 [確定]。

5. 藉由功能表 [建置] > [建置方案] 編譯您的程式。


## 步驟 4：取得連接字串


使用 [Azure 入口網站](https://portal.azure.com/)來複製連接到 SQL 資料庫所需的連接字串。

您的第一次使用會將 Visual Studio 連接到您的 Azure SQL Database **AdventureWorksLT** 資料庫。


[AZURE.INCLUDE [sql-database-include-connection-string-20-portalshots](../../includes/sql-database-include-connection-string-20-portalshots.md)]


## 步驟 5：將連接字串新增至 App.config 檔案


1. 在 Visual Studio 中，從 [方案總管] 窗格開啟 App.config 檔案。

2. 新增 **&#x3c;configuration&#x3e; &#x3c;/configuration&#x3e;** 元素，如下列範例的 App.config 程式碼範例所示。
 - 以您的實際值取代 *{your\_placeholders}*：

```
	<?xml version="1.0" encoding="utf-8" ?>
	<configuration>
	    <startup>
	        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
	    </startup>

		<connectionStrings>
			<clear />
			<add name="ConnectionString4NoUserIDNoPassword"
			connectionString="Server=tcp:{your_serverName_here}.database.windows.net,1433; Database={your_databaseName_here}; Connection Timeout=30; Encrypt=True; TrustServerCertificate=False;"
			/>
		</connectionStrings>
	</configuration>
```

3. 儲存 App.config 變更。

4. 在 [方案總管] 窗格中，以滑鼠右鍵按一下 [App.config] 節點，然後按一下 [屬性]。

5. 將**複製到輸出目錄**設定為**永遠複製**。
 - 這會使 App.config 檔案的內容取代 &#x2a;.exe.config 檔案的內容，其位於 &#x2a;.exe 檔案建置所在的目錄中。每次您重新編譯 &#x2a;.exe 時都會出現取代情形。
 - &#x2a;.exe.config 檔案會在我們的範例 C# 程式執行時讀取。

	![複製到輸出目錄 = 永遠複製][50-VSCopyToOutputDirectoryProperty]


## 步驟 6：在範例 C# 程式碼中貼上


1. 在 Visual Studio 中，使用 [方案總管] 窗格開啟您的 **Program.cs** 檔案。

	![在範例 C# 查詢程式碼中貼上。][40-VSProgramCsOverlay]

2. 在下列範例 C# 程式碼中貼上以覆寫 Program.cs 中的所有起始程式碼。
 - 如果您想要較短的程式碼範例，您可以將整個連接字串指派給變數 **SQLConnectionString** 當做常值。然後您可以清除這兩個方法 **GetConnectionStringFromExeConfig** 和 **GatherPasswordFromConsole**。


```
using System;  // C#
using G = System.Configuration;   // System.Configuration.dll
using D = System.Data;            // System.Data.dll
using C = System.Data.SqlClient;  // System.Data.dll
using T = System.Text;

namespace ConnectAndQuery_Example
{
	class Program
	{
		static void Main()
		{
			string connectionString4NoUserIDNoPassword,
				password, userName, SQLConnectionString;

			// Get most of the connection string from ConnectAndQuery_Example.exe.config
			// file, in the same directory where ConnectAndQuery_Example.exe resides.
			connectionString4NoUserIDNoPassword = Program.GetConnectionStringFromExeConfig
				("ConnectionString4NoUserIDNoPassword");
			// Get the user name from keyboard input.
			Console.WriteLine("Enter your User ID, without the trailing @ and server name: ");
			userName = Console.ReadLine();
			// Get the password from keyboard input.
			password = Program.GatherPasswordFromConsole();

			SQLConnectionString = "Password=" + password + ';' +
				"User ID=" + userName + ";" + connectionString4NoUserIDNoPassword;

			// Create an SqlConnection from the provided connection string.
			using (C.SqlConnection connection = new C.SqlConnection(SQLConnectionString))
			{
				// Formulate the command.
				C.SqlCommand command = new C.SqlCommand();
				command.Connection = connection;

				// Specify the query to be executed.
				command.CommandType = D.CommandType.Text;
				command.CommandText = @"
					SELECT TOP 9 CustomerID, NameStyle, Title, FirstName, LastName
					FROM SalesLT.Customer;  -- In AdventureWorksLT database.
					";
				// Open a connection to database.
				connection.Open();

				// Read data returned for the query.
				C.SqlDataReader reader = command.ExecuteReader();
				while (reader.Read())
				{
					Console.WriteLine("Values:  {0}, {1}, {2}, {3}, {4}",
						reader[0], reader[1], reader[2], reader[3], reader[4]);
				}
			}
			Console.WriteLine("View the results here, then press any key to finish...");
			Console.ReadKey(true);
		}
		//----------------------------------------------------------------------------------

		static string GetConnectionStringFromExeConfig(string connectionStringNameInConfig)
		{
			G.ConnectionStringSettings connectionStringSettings =
				G.ConfigurationManager.ConnectionStrings[connectionStringNameInConfig];

			if (connectionStringSettings == null)
			{
				throw new ApplicationException(String.Format
					("Error. Connection string not found for name '{0}'.",
					connectionStringNameInConfig));
			}
				return connectionStringSettings.ConnectionString;
		}

		static string GatherPasswordFromConsole()
		{
			T.StringBuilder passwordBuilder = new T.StringBuilder(32);
			ConsoleKeyInfo key;
			Console.WriteLine("Enter your password: ");
			do
			{
				key = Console.ReadKey(true);
				if (key.Key != ConsoleKey.Backspace)
				{
					passwordBuilder.Append(key.KeyChar);
					Console.Write("*");
				}
				else  // Backspace char was entered.
				{
					// Retreat the cursor, overlay '*' with ' ', retreat again.
					Console.Write("\b \b");
					passwordBuilder.Length = passwordBuilder.Length - 1;
				}
			}
			while (key.Key != ConsoleKey.Enter); // Enter key will end the looping.
			Console.WriteLine(Environment.NewLine);
			return passwordBuilder.ToString();
		}
	}
}
```


### 編譯您的程式


1. 在 Visual Studio 中，依序按一下功能表 [建置] > [建置方案] 來編譯您的程式。


### 範例程式中的動作摘要


1. 從組態檔讀取大多數的 SQL 連接字串。

2. 從鍵盤收集使用者名稱和密碼，並將其新增以完成連接字串。

3. 使用連接字串和 ADO.NET 類別來連接至 SQL Database 上的 **AdventureWorksLT** 示範資料庫。

4. 發出 SQL **SELECT** 以從 **SalesLT** 資料表讀取。

5. 將傳回的資料列列印到主控台。


我們試著讓 C# 範例保持簡短。但是我們加入可讀取組態檔的程式碼以滿足像您這樣的客戶所提出的數個要求。我們同意生產品質程式應該使用組態檔而不是 .exe 中的常值硬式編碼。


> [AZURE.WARNING] 以程式碼簡潔度而言，我們選擇不包含程式碼以在此教育範例中進行例外狀況處理和重試邏輯。不過，與雲端資料庫互動的生產程式應該包含兩者。
>
> [這裡](sql-database-develop-csharp-retry-windows.md)是具有重試邏輯之程式碼範例的連結。


## 步驟 7：加入伺服器防火牆中允許的 IP 位址範圍


在用戶端電腦的 IP 位址加入 SQL Database 防火牆之前，您的用戶端 C# 程式無法連接到 SQL Database。您的程式將會失敗並出現一個好用的錯誤訊息，陳述必要的 IP 位址。


您可以使用 [Azure 入口網站](https://portal.azure.com/)新增 IP 位址。



[AZURE.INCLUDE [sql-database-include-ip-address-22-v12portal](../../includes/sql-database-include-ip-address-22-v12portal.md)]



如需詳細資訊，請參閱：<br/> [作法：在 SQL Database 上進行防火牆設定](sql-database-configure-firewall-settings.md)。



## 步驟 8：執行程式


1. 在 Visual Studio 中，藉由功能表 [偵錯] > [開始偵錯] 執行您的 C# 查詢程式。主控台視窗隨即顯示。

2. 依照指示輸入您的使用者名稱和密碼。
 - 幾個連接工具需要您的使用者名稱附加 "@{your\_serverName\_here}"，但這個後置詞對於 ADO.NET 而言是選擇性的。請務必輸入此後置詞。

3. 資料列會隨即顯示。


## 相關連結


- [SQL Database 的用戶端快速入門程式碼範例](sql-database-develop-quick-start-client-code-samples.md)

- 如果您的用戶端程式是在 Azure VM 上執行，請於下文深入了解 1433 以外的 TCP 連接埠：<br/>[供 ADO.NET 4.5 和 SQL Database V12 使用之 1433 以上的連接埠](sql-database-develop-direct-route-ports-adonet-v12.md)。



<!-- Image references. -->

[20-OpenInVisualStudioButton]: ./media/sql-database-connect-query/connqry-free-vs-e.png

[30-VSNewProject]: ./media/sql-database-connect-query/connqry-vs-new-project-f.png

[40-VSProgramCsOverlay]: ./media/sql-database-connect-query/connqry-vs-program-cs-overlay-g.png

[50-VSCopyToOutputDirectoryProperty]: ./media/sql-database-connect-query/connqry-vs-appconfig-copytoputputdir-h.png

<!---HONumber=AcomDC_0128_2016-->