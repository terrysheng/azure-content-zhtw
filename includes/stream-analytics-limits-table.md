<properties 
   pageTitle="串流分析限制資料表"
   description="描述串流分析元件和連線的系統限制及建議大小。"
   services="stream-analytics"
   documentationCenter="NA"
   authors="jeffstokes72"
   manager="paulettm"
   editor="cgronlun" />
<tags 
   ms.service="stream-analytics"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="big-data"
   ms.date="10/22/2015"
   ms.author="jeffstok" />

| 限制識別碼 | 限制 | 註解 |
|----------------- | ------------|--------- |
| 每個區域的每個訂用帳戶串流單位數目上限 | 50 | 如需要求將訂用帳戶的串流單位數目增加至 50 個以上，請連絡 [Microsoft 支援](https://support.microsoft.com/zh-TW)。 |
| 串流單位的最大輸送量 | 1 MB/秒* | 每個 SU 的最大輸送量取決於視案例。實際的輸送量可能較低，而且取決於查詢複雜性和資料分割。可在[調整 Azure 串流分析工作以增加輸送量](../articles/stream-analytics/stream-analytics-scale-jobs.md)文章中找到進一步的詳細資料。 |
| SELECT 陳述式查詢限制 | 每個查詢 5 個輸出 | 未來可能會增加此限制。 |

<!---HONumber=Nov15_HO1-->