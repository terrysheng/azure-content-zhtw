<properties 
	pageTitle="EntLib 重試連接到 SQL Database | Microsoft Azure"
	description="Enterprise Library 是設計來簡化雲端服務的用戶端程式工作，包括整合暫時性錯誤的重試邏輯。"
	services="sql-database"
	documentationCenter=""
	authors="MightyPen"
	manager="jeffreyg"
	editor="" />


<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="10/14/2015" 
	ms.author="genemi"/>


# 程式碼範例：C&#x23 中用於連接到 SQL Database 的 Enterprise Library 6 重試邏輯


本主題呈現完整的程式碼範例，示範 Enterprise Library (EntLib)。EntLib 簡化用戶端程式與雲端服務 (例如 Azure SQL Database) 互動的許多工作。我們的範例著重在包括暫時性錯誤的重試邏輯這類重要工作。


EntLib 類別是設計用來區分兩種類別的執行階段錯誤：

- 永遠不會自行修正的錯誤，例如拼錯的伺服器名稱。
- 暫時性錯誤，例如在 Azure 系統負載平衡時，伺服器因為接受新的連線而暫停幾秒鐘。

Enterprise Library 6 (EntLib60) 是最新版本，已經在 2013 年 4 月發行。


## 必要條件


#### .NET Framework 4.0 或更高版本


必須安裝 Microsoft .NET Framework 4.0 或更高版本。本文撰寫時，已經有 4.6 版，我們建議使用最新版本。


#### Visual Studio Community 版本 (免費)


您需要一種方式編譯此範例的原始程式碼。一種方式是安裝[免費的 Microsoft Visual Studio *Community* 版本](http://www.visualstudio.com/products/free-developer-offers-vs.aspx)。


您可能需要向 MSDN 註冊電子郵件地址。步驟大致如下：


1. [前往 MSDN](http://msdn.microsoft.com/)。
2. 按一下靠近頂端的 [MSDN 訂閱]。
3. 按一下 [立即註冊]。
4. 填寫您的資訊。
5. 按一下底部的 [建立帳戶]。


#### Enterprise Library 6 (EntLib60)


您可以安裝 EntLib60 的方式：


- 在 Visual Studio 中使用 *NuGet* 封裝管理員功能：
 - 在 NuGet 中搜尋 **enterpriselibrary**。


- 在 [EntLib60 的主要文件集主題](http://msdn.microsoft.com/library/dn169621.aspx)，找到標示為 **Downloads** 的列，然後按一下 [Microsoft Enterprise Library 6](http://go.microsoft.com/fwlink/?linkid=290898) 下載二進位檔 .DLL 組件檔案。


EntLib60 有數個 .DLL 組件檔案，名稱以相同的前置詞 **Microsoft.Practices.EnterpriseLibrary.&#x2a;.dll** 為開頭，但此程式碼範例只需要下列兩個組件：

- Microsoft.Practices.EnterpriseLibrary.**TransientFaultHandling**.dll
- Microsoft.Practices.EnterpriseLibrary.**TransientFaultHandling.Data**.dll


## EntLib 類別如何彼此搭配運作


EntLib 類別是用來建構其他 EntLib 類別。在此程式碼範例中，建構和使用順序如下所示：


1. 建構 **ExponentialBackoff** 物件。
2. 建構 **SqlDatabaseTransientErrorDetectionStrategy** 物件。
3. 建構 **RetryPolicy** 物件。輸入的參數有：
 - **ExponentialBackoff** 物件。
 - **SqlDatabaseTransientErrorDetectionStrategy** 物件。
4. 建構 **ReliableSqlConnection** 物件。輸入的參數有：
 - 一個 **String** 物件 - 使用伺服器名稱和其他連接資訊。
5. 透過 **RetryPolicy .ExecuteAction** 方法呼叫進行連接。
6. 呼叫 **ReliableSqlConnection .CreateCommand** 方法。
 - 傳回 **System.SqlClient.Data.DbCommand** 物件，這是 ADO.NET 的一部分。
7. 透過 **RetryPolicy .ExecuteAction** 方法呼叫進行查詢。


## 編譯並執行程式碼範例


Program.cs 原始程式碼範例會在本主題稍後提供。您可以使用下列步驟編譯和執行範例：


1. 在 Visual Studio 中，透過 C# 主控台應用程式範本建立新的專案。

2. 在 [方案總管] 窗格中，編輯幾乎是空白的 Program.cs 檔案，將開頭部分的內容取代為本主題稍後提供的 Program.cs 程式碼。

3. 使用 Visual Studio 中的 [建置] > [建置方案] 功能表編譯您的專案。

4. 在 cmd.exe 命令視窗中，如下所示執行程式。也會顯示執行的實際輸出。


```
[C:\MyVS\EntLib60Retry\EntLib60Retry\bin\Debug]
>> EntLib60Retry.exe

database_firewall_rules_table   245575913
filestream_tombstone_2073058421 2073058421
filetable_updates_2105058535    2105058535

[C:\MyVS\EntLib60Retry\EntLib60Retry\bin\Debug]
>>
```


&nbsp;


## Program.cs 原始程式碼


下列 Program.cs 檔案包含此 EntLib 範例的所有原始程式碼。


```
using     System;   // C#
using G = System.Collections.Generic;
using D = System.Data;
using C = System.Data.SqlClient;
using X = System.Text;
using Y = Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling;

namespace EntLib60Retry
{
   class Program
   {
      static void Main(string[] args)
      {
         Program program = new Program();

         if (program.Run(args) == false)
         {
            Console.WriteLine("Something failed.  :-( ");
         }
      }

      bool Run(string[] _args)
      {
         bool returnBool = false;

         this.InitializeEntLibObjects();

         try
         {
            this.reliableSqlConnection = new Y.ReliableSqlConnection(
               this.sqlConnectionSB.ToString(),
               null, null    // Letting RetryPolicy.ExecuteAction method handle retries.
               );
            this.retryPolicy.ExecuteAction(this.OpenTheConnection_action);  // Open the connection.

            this.dbCommand = this.reliableSqlConnection.CreateCommand();
            this.dbCommand.CommandText = @"
SELECT TOP 3
      ob.name,
      CAST(ob.object_id as nvarchar(32)) as [object_id]
   FROM sys.objects as ob
   WHERE ob.type='IT'
   ORDER BY ob.name;";

            this.retryPolicy.ExecuteAction(this.IssueTheQuery_action);  // Run the query.
         }

         catch (C.SqlException sqlExc)
         {
            if (this.sqlDatabaseTransientErrorDetectionStrategy.IsTransient(sqlExc) == true)
            {
               Console.WriteLine("Appears a transient fault has not yet cleared after max retries, thus program now terminates.");
            }
            throw sqlExc;
         }
         return returnBool;
      }

      void OpenTheConnection()  // Called by .ExecuteAction.
      {
         this.reliableSqlConnection.Open();
      }

      void IssueTheQuery()      // Called by .ExecuteAction.
      {
         X.StringBuilder sBuilder = new X.StringBuilder(256);

         this.dataReader = this.dbCommand.ExecuteReader();

         while (this.dataReader.Read())
         {
            sBuilder.Length = 0;
            sBuilder.Append(this.dataReader.GetString(0));
            sBuilder.Append("\t");
            sBuilder.Append(this.dataReader.GetString(1));

            Console.WriteLine(sBuilder.ToString());
         }
      }

      void InitializeEntLibObjects()
      {
         this.InitializeSqlConnectionStringBuilder();

         this.exponentialBackoff = new Y.ExponentialBackoff(
            "exponentialBackoff",
             3,                    // Maximum number of times to retry, then let exception bubble up.
             TimeSpan.FromMilliseconds(10000), // Minimum interval between retries.
             TimeSpan.FromMilliseconds(30000), // Maximum interval between retries.
             TimeSpan.FromMilliseconds( 2000)  // For random differences in interval between retries.
            );
         this.sqlDatabaseTransientErrorDetectionStrategy =
            new Y.SqlDatabaseTransientErrorDetectionStrategy();
         this.retryPolicy = new Y.RetryPolicy(
               this.sqlDatabaseTransientErrorDetectionStrategy,
               this.exponentialBackoff
               );
         this.OpenTheConnection_action = delegate() { this.OpenTheConnection(); };
         this.IssueTheQuery_action     = delegate() { this.IssueTheQuery(); };
      }

      void InitializeSqlConnectionStringBuilder()
      {
         // Prepare the connection string to Azure SQL Database.
         this.sqlConnectionSB = new C.SqlConnectionStringBuilder();

         // Change these values to your values.
         this.sqlConnectionSB["Server"] = "tcp:myazuresqldbserver.database.windows.net,1433";
         this.sqlConnectionSB["User ID"] = "MyLogin";  // "@yourservername"  as suffix sometimes.
         this.sqlConnectionSB["Password"] = "MyPassword";
         this.sqlConnectionSB["Database"] = "MyDatabase";

         // Leave these values as they are.
         this.sqlConnectionSB["Trusted_Connection"] = false;
         this.sqlConnectionSB["Integrated Security"] = false;
         this.sqlConnectionSB["Encrypt"] = true;
         this.sqlConnectionSB["Connection Timeout"] = 30;
      }

      Program()   // Constructor.
      {
         int[] arrayOfTransientErrorNumbers =
                { 4060, 10928, 10929, 40197, 40501, 40613, 49918, 49919, 49920
                    //,11001   // 11001 for testing, pretend network error is transient.
                    //,18456   // 18456 for testing, purposely misspell database name.
                };
         listTransientErrorNumbers = new G.List<int>(arrayOfTransientErrorNumbers);
      }

      // Fields.
      private G.List<int> listTransientErrorNumbers;

      private Y.ReliableSqlConnection reliableSqlConnection;
      private Y.ExponentialBackoff exponentialBackoff;
      private Y.SqlDatabaseTransientErrorDetectionStrategy
                   sqlDatabaseTransientErrorDetectionStrategy;
      private Y.RetryPolicy retryPolicy;

      private Action OpenTheConnection_action;
      private Action IssueTheQuery_action;

      private C.SqlConnectionStringBuilder sqlConnectionSB;
      private D.IDbCommand dbCommand;
      private D.IDataReader dataReader;
   }
}
```


&nbsp;


## 相關連結


- [Enterprise Library 6 – 2013 年 4 月](http://msdn.microsoft.com/library/dn169621.aspx)提供進一步資訊的許多連結。
 - 如果您想要查看原始程式碼，本主題頂端有一個按鈕可[下載 EntLib60 原始程式碼](http://go.microsoft.com/fwlink/p/?LinkID=290898)。


- Microsoft 提供之 .PDF 格式的免費電子書：[Microsoft Enterprise Library 第 2 版的開發人員指南](http://www.microsoft.com/download/details.aspx?id=41145)。


- [Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling 命名空間](http://msdn.microsoft.com/library/microsoft.practices.enterpriselibrary.transientfaulthandling.aspx)


- [Enterprise Library 6 類別程式庫參考](http://msdn.microsoft.com/library/dn170426.aspx)


- [程式碼範例：使用 ADO.NET 連線到 SQL Database 的 C# 重試邏輯](sql-database-develop-csharp-retry-windows.md)


- [SQL Database 的用戶端快速入門程式碼範例](sql-database-develop-quick-start-client-code-samples.md)

<!---HONumber=Oct15_HO3-->