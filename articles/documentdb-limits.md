<properties title="DocumentDB limits for the preview release" pageTitle="預覽版本的 DocumentDB 限制 | Azure" description="Learn about the limits and quota enforcements of DocumentDB for the preview release." metaKeywords="" services="documentdb" solutions="data-management"  authors="bradsev" manager="jhubbard" editor="cgronlun"  videoId="" scriptId="" />

<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/20/2014" ms.author="spelluru" />


#預覽版本的 DocumentDB 限制
下表描述預覽版本期間 DocumentDB 的限制和配額強制執行。大多數情況下，我們會想辦法取得您的意見或是根據目前的容量條件約束，來強制執行限制。如果您有需要放寬限制的商務需求，請連絡我們，我們會在公開提供項目的條件約束內盡可能進行調整。    

|實體 |配額 (預覽版本的標準供應項目)|
|-------|--------|
|資料庫帳戶     |5
|每個資料庫帳戶的資料庫數目     |100
|每個資料庫帳戶的使用者數目 - 跨所有資料庫 |500,000
|每個資料庫帳戶的權限數目 - 跨所有資料庫   |2,000,000
|每個資料庫帳戶的附件儲存體      |2 GB
|每個資料庫帳戶的容量單位數目上限       |5
|每個容量單位的集合數目      |3
|最少有 1 份文件的每個集合的配置儲存體下限    |3.3 GB
|最少有 1 份文件的每個集合的配置儲存體下限 |667 RU
|集合的彈性    |0-10 GB
|每個集合的每秒要求單位數上限   |2,000
|每個集合的預存程序、觸發程序和 UDF 數目       |每個 25 個
|預存程序和觸發程序的執行時間上限     |5 秒
|佈建的文件儲存體/容量單位 |10 GB
|佈建的要求單位/秒/容量單位     |2,000
|每個資料庫的文件儲存體上限 (5 個容量單位)    |50 GB
|Id 屬性的長度上限    |255 個字元
|每個頁面的預設項目數     |100
|每個頁面的項目數上限        |1,000
|文件和附件的要求大小上限       |256KB
|預存程序、觸發程序和 UDF 的要求大小上限        |256KB
|回應大小上限 |1MB
|每個集合的唯一路徑數目上限       |100
|字串 |所有字串都必須符合 UTF-8 編碼。因為 UTF-8 是寬度不一定的編碼，所以字串大小是使用 UTF-8 位元組來決定。
|屬性或值的長度上限  |沒有實際限制
|每個查詢的 UDF 數目上限     |1
|每個查詢的 JOIN 數目上限    |2
|每個查詢的 AND 子句數目上限      |5
|每個查詢的 OR 子句數目上限       |5
