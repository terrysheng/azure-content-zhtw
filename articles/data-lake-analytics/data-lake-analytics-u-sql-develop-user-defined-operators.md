<properties 
   pageTitle="針對 Azure 資料湖分析工作開發 U-SQL 使用者定義運算子 | Azure" 
   description="深入了解如何開發使用者定義運算子，以在資料湖分析工作中使用和重複使用。" 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="mumian" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="10/22/2015"
   ms.author="jgao"/>


# 針對 Azure 資料湖分析工作開發 U-SQL 使用者定義運算子

深入了解如何開發使用者定義運算子，以在資料湖分析工作中使用和重複使用。您將開發自訂運算子以轉換國家/地區名稱。

##必要條件

- 已安裝 Visual Studio 2015、Visual Studio 2013 更新 4，或具有 Visual C++ 的 Visual Studio 2012 
- Microsoft Azure SDK for .NET 2.5 版或更新版本。使用 Web Platform Installer 來進行安裝。
- 資料湖分析帳戶。請參閱[使用 Azure 入口網站開始使用 Azure 資料湖分析](data-lake-analytics-get-started-portal.md)。
- 請參閱[開始使用 Azure 資料湖分析 U-SQL Studio](data-lake-analytics-u-sql-studio-get-started.md) 教學課程。
- 連接至 Azure，請參閱[開始使用 Azure 資料湖分析 U-SQL Studio](data-lake-analytics-u-sql-studio-get-started.md#connect-to-azure)。 
- 上傳來源資料，請參閱[開始使用 Azure 資料湖分析 U-SQL Studio](data-lake-analytics-u-sql-studio-get-started.md#upload-source-data-files)。 

## 在 U-SQL 中定義和使用使用者定義運算子

**建立和提交 U-SQL 工作**

1. 從 [檔案] 功能表中，按一下 [新增]，再按 [專案]。
2. 選取 [U-SQL 專案] 類型。

	![新的 U-SQL Visual Studio 專案](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-new-project.png)

3. 按一下 [確定]。Visual studio 會建立具有 Script.usql 檔案的解決方案。
4. 在 [方案總管] 中展開 Script.usql，然後按兩下 **Script.usql.cs**。
5. 將下列程式碼貼到檔案中：

		using Microsoft.Analytics.Interfaces;
		using System.Collections.Generic;
		
		namespace USQL_UDO
		{
			public class CountryName : IProcessor
			{
				private static IDictionary<string, string> CountryTranslation = new Dictionary<string, string>
				{
					{
						"Deutschland", "Germany"
					},
					{
						"Schwiiz", "Switzerland"
					},
					{
						"UK", "United Kingdom"
					},
					{
						"USA", "United States of America"
					},
					{
						"中国", "PR China"
					}
				};
		
				public override IRow Process(IRow input, IUpdatableRow output)
				{
		
					string UserID = input.Get<string>("UserID");
					string Name = input.Get<string>("Name");
					string Address = input.Get<string>("Address");
					string City = input.Get<string>("City");
					string State = input.Get<string>("State");
					string PostalCode = input.Get<string>("PostalCode");
					string Country = input.Get<string>("Country");
					string Phone = input.Get<string>("Phone");
		
					if (CountryTranslation.Keys.Contains(Country))
					{
						Country = CountryTranslation[Country];
					}
					output.Set<string>(0, UserID);
					output.Set<string>(1, Name);
					output.Set<string>(2, Address);
					output.Set<string>(3, City);
					output.Set<string>(4, State);
					output.Set<string>(5, PostalCode);
					output.Set<string>(6, Country);
					output.Set<string>(7, Phone);
		
					return output.AsReadOnly();
				}
			}
		}

5. 啟動 Script.usql，並貼上下列 U-SQL 指令碼：

		@drivers =
			EXTRACT UserID      string,
					Name        string,
					Address     string,
					City        string,
					State       string,
					PostalCode  string,
					Country     string,
					Phone       string
			FROM "/Samples/Data/AmbulanceData/Drivers.txt"
			USING Extractors.Tsv(Encoding.Unicode);
		
		@drivers_CountryName =
			PROCESS @drivers
			PRODUCE UserID string,
					Name string,
					Address string,
					City string,
					State string,
					PostalCode string,
					Country string,
					Phone string
			USING new USQL_UDO.CountryName();    
		
		OUTPUT @drivers_CountryName
			TO "/Samples/Outputs/Drivers.csv"
			USING Outputters.Csv(Encoding.Unicode);

6. 從 [方案總管] 中，在 [Script.usql] 上按一下滑鼠右鍵，然後按一下 [建置指令碼]。
6. 從 [方案總管] 中，在 [Script.usql] 上按一下滑鼠右鍵，然後按一下 [提交指令碼]。
7. 如果您尚未連接到您的 Azure 訂用帳戶，系統會提示您輸入您的 Azure 帳戶認證。
7. 按一下 [提交]。提交作業完成時，提交結果和工作連結都可以在 [結果] 視窗中取得。
8. 您必須按一下 [重新整理] 按鈕，才能查看最新的作業狀態並重新整理畫面。

**查看作業輸出**

1. 從 [伺服器總管] 依序展開 [Azure]、[資料湖分析]、您的資料湖分析帳戶、[儲存體帳戶]，以滑鼠右鍵按一下 [預設儲存體]，然後按一下 [總管]。 
2. 展開範例、展開輸出，然後按兩下 [Drivers.csv]。


##另請參閱

- [使用 PowerShell 開始使用資料湖分析](data-lake-analytics-get-started-powershell.md)
- [使用 Azure 入口網站開始使用資料湖分析](data-lake-analytics-get-started-portal.md)
- [使用適用於 Visual Studio 的資料湖工具來開發 U-SQL 應用程式](data-lake-analytics-data-lake-tools-get-started.md)

<!---HONumber=AcomDC_1203_2015-->