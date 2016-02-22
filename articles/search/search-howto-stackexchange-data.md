<properties
	pageTitle="如何使用 Azure 搜尋服務搜尋 StackExchange 資料 | Microsoft Azure | 雲端託管搜尋服務"
	description="了解如何使用 Azure 搜尋服務 (Microsoft Azure 上的雲端託管搜尋服務) 執行 REST 搜尋。"
	services="search"
	documentationCenter=""
	authors="liamca"
	manager="pablocas"
	editor=""/>

<tags
	ms.service="search"
	ms.devlang="rest-api"
	ms.workload="search"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.date="02/09/2016"
	ms.author="liamca"/>

# 如何使用 Azure 搜尋服務來搜尋 StackExchange 資料

本文逐步解說一些可透過 [Azure 搜尋服務](https://azure.microsoft.com/services/search/)完成的核心全文檢索搜尋功能。文中運用 Stack Exchange [提供](https://archive.org/details/stackexchange)給 Creative Commons 使用並包含下列[出處標示](http://blog.stackoverflow.com/2009/06/attribution-required/)的資料。

## 開始使用

為了強調某些功能，我已建立可供您使用的 Azure 搜尋服務索引，其中包含從 2015 年 10 月 14 日起來自 Programmer StackExchange 的資料。注意：我稍後也將示範如何使用此資料建立您自己的 Azure 搜尋服務索引。

讓我們從極為簡單的全文檢索搜尋查詢開始，使用者可能會在其中輸入 "azure" 這個字來尋找任何有關 Azure 的 StackExchange 文章。嘗試按一下此連結，即可觀看實作示範：

> [http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=stackexchange&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28%26search=azure](http://fiddle.jshell.net/liamca/gkvfLe6s/?index=stackexchange&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28%26search=azure)

在此範例中，我們只需傳遞 "azure" 這個字做為搜尋參數並顯示傳回的 JSON 格式化結果。以下是一些您可以嘗試的其他查詢範例。

-	`Faceting`：一旦使用者搜尋資料集，能夠篩選資料是幫助他們瀏覽結果的好方法。若要這樣做，您通常會從顯示給使用者的一組類別 (facet) 著手。以下是我們可能想利用的一些 facet 範例：
  -	**標記**：許多問題都有與其相關聯的標記，可讓使用者向下鑽研至特定類別
  -	**日期**：使用者可能只想查看在特定時間範圍內提問或回答的問題
  -	**使用者**：您可能想要查看或限制特定使用者的結果。在此範例中，我們將搜尋 "azure"，但傳回 tagsCollection 和 acceptedAnswerDisplayName 使用者名稱的 facet 計數。

> <http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=stackexchange&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28%26search=azure%26facet=tagsCollection%26facet=acceptedAnswerDisplayName>

-	`Filtering`：在使用者選擇 facet 之後，您接著會想執行另一個搜尋，但運用篩選器將結果限制為該 facet 值。例如，搜尋 "Azure" 但將結果限制為有以遞減順序依 viewCount 排序之 “architecture” 標記的地方：

> <http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=stackexchange&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28%26search=azure%26$filter=tagsCollection/any(t:+t+eq+'architecture')%26$orderby=viewCount+desc>

-	`Fuzzy Search`：我們對 [Lucene 查詢運算式](https://msdn.microsoft.com/library/mt589323.aspx)的新支援也可讓您執行一些很特別的查詢，例如模糊比對結果以及將搜尋限制為特定欄位。此範例會搜尋標題欄位中的 “visualize” 這個字，但是 ~ 表示模糊比對，這表示也會傳回 visualise 和 visualizing 等結果。

> <http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=stackexchange&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28&search%3Dtitle%3Avisualise~%26querytype%3Dfull%26searchMode%3Dall%26%24select%3Dtitle>

-	`Scoring and Tuning`: [評分設定檔](https://msdn.microsoft.com/library/azure/dn798928.aspx)相當有用，可協助您微調搜尋服務所傳回的結果。事實上，我們現在也使用 [Lucene 查詢運算式](https://msdn.microsoft.com/library/mt589323.aspx)將評分即時套用至個別的欄位和詞彙。例如，如果我們想要在標題欄位中搜尋 “visualize” 或 “chart” 等字，還提供更多加權給其中有 “chart” 這個字的項目，我們可以這麼做：

> <http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=stackexchange&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26search%3Dtitle%3Avisualise+OR+title%3Achart%5E3+%26querytype%3Dfull%26searchMode%3Dall%26%24select%3Dtitle>

  此資料集中有許多其他欄位可用來讓使用者的相關結果增多。例如，我可以使用：

  -	**量級**評分，將其用於 answerCount、commentCount、favoriteCount 和 viewCount 等數值欄位，以增加搜尋結果 (如果搜尋結果的計數恰巧很高)。
  -	**有效性**評分，將其用於 creationDate 和 lastActivityDate 等 DateTime 欄位，讓近期建立或使用中的項目增加
  -	**欄位權數**，指出是否在問題本文中找到搜尋文字，然而這比在答案中找到時還要相關。

其他您可能想要使用的項目包括：

-	[`Suggestions`](https://msdn.microsoft.com/library/azure/mt131377.aspx)：當使用者在搜尋方塊中輸入資料時，可使用 Title、Tags 和 UserName 等欄位以便自動完成。  

-	`Recommendations`：您通常需要 Apache Mahout 或 Azure 機器學習等工具來協助您建立一些建議，讓您顯示用者可能有興趣檢視的類似問題，但此資料集幸好已經有一些建議。

歡迎隨時使用此 JSFiddle 頁面，嘗試不同類型的查詢。如果您想要深入了解如何建立此索引，請繼續閱讀。也歡迎您透過我的 Twitter 帳戶 [@liamca](https://twitter.com/liamca) 直接與我聯繫。

## 如何建立此 Azure 搜尋服務索引？

Brent 已藉由示範如何將資料預備至 SQL Database，進行了很多困難的工作。如果您想要自行逐步完成預備資料的這個程序，請查看他的[教學課程](http://www.brentozar.com/archive/2014/01/how-to-query-the-stackexchange-databases/)。否則，您可以只運用我從 2015 年 10 月 15 日起以一些資料預先填入的 Azure SQL Database，或者可以嘗試我所建立的 Azure 搜尋服務索引。詳細資料如下面的「從預備的 Azure SQL Database 匯入資料」一節所述。我做超過 Brent 所述的唯一一件事就是在 Azure SQL Database 中建立一個檢視，以供 [Azure 搜尋服務索引器](https://msdn.microsoft.com/library/azure/dn946891.aspx)從我有興趣使用的一組資料表中編目和擷取資料。以下是此檢視的定義方式。

    CREATE VIEW StackExchangeSearchData as
    SELECT
          PQ.[Id]
          ,PQ.[AcceptedAnswerId]
          ,PQ.[AnswerCount]
          ,PQ.[Body]
          ,PQ.[ClosedDate]
          ,PQ.[CommentCount]
          ,PQ.[CommunityOwnedDate]
          ,PQ.[CreationDate]
          ,PQ.[FavoriteCount]
          ,PQ.[LastActivityDate]
          ,PQ.[LastEditDate]
          ,PQ.[LastEditorDisplayName]
    	  ,PUQ.DisplayName OwnerDisplayName
    	  ,PUQ.Reputation OwnerReputation
          ,PQ.[Score]
          ,reverse(REPLACE(LTRIM(REPLACE(reverse(REPLACE(REPLACE (PQ.[Tags], '>' , '],' ), '<' , '[' )), ISNULL(',', '0'), ' ')), ' ', ISNULL(',', '0'))) TagsCollection
          ,PQ.[Title]
          ,PQ.[ViewCount]
    	  ,PA.[Body] AcceptedAnswerBody
    	  ,PA.[Score] AcceptedAnswerScore
    	  ,PUQ.DisplayName AcceptedAnswerDisplayName
    	  ,PUQ.Reputation AcceptedAnswerReputation
    	  ,PA.[FavoriteCount] AcceptedAnswerFavoriteCount
    	  ,PL.[RelatedPostId]
      FROM [StackExchange].[dbo].[Posts] PQ
      LEFT OUTER JOIN [StackExchange].[dbo].[Posts] PA
      and PQ.AcceptedAnswerId = PA.Id
      LEFT OUTER JOIN [StackExchange].[dbo].[PostLinks] PL
      on PQ.Id = PL.Id
      LEFT OUTER JOIN [StackExchange].[dbo].[Users] PUQ
      on PQ.OwnerUserId = PUQ.Id
      LEFT OUTER JOIN [StackExchange].[dbo].[Users] PUA
      on PA.[OwnerUserId] = PUA.Id
      WHERE PQ.PostTypeId = 1

一旦完成，您就可以使用 [Azure 傳統入口網站](https://portal.azure.com)從上述 Azure SQL 檢視「匯入資料」，然後根據此檢視中的欄位結構描述建立 Azure 搜尋服務索引。如果您想要使用我所預備的 Azure SQL Database，以下是您可以使用的唯讀連接字串：

    Server=tcp:azs-playground.database.windows.net,1433;Database=StackExchange;User ID=reader@azs-playground;
    Password=EdrERBt3j6mZDP;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;

<!---HONumber=AcomDC_0211_2016-->