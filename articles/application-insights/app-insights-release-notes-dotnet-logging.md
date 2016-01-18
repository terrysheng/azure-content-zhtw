<properties 
	pageTitle="Application Insights 記錄配接器的版本資訊" 
	description="最新的更新。" 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>
<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/21/2015" 
	ms.author="abaranch"/>
 
# .NET 記錄配接器的版本資訊

如果您已經使用 log4net、NLog 或 System.Diagnostics.Trace 等紀錄架構，您可以擷取這些記錄檔，並將其傳送至 [Application Insights](http://azure.microsoft.com/services/application-insights/)，在這邊讓記錄追蹤與使用者動作、例外狀況和其他事件相互關聯。


#### 若要開始使用

請參閱 [Application Insights 中 ASP.NET 的記錄檔、例外狀況和自訂診斷](app-insights-search-diagnostic-logs.md)。

## 1\.2.6 版

- 錯誤修正
- log4Net：收集 log4net 屬性做為自訂屬性。UserName 不再是自訂屬性 (它會集中為 telemetry.Context.User.Id)。Timestamp 不再是自訂屬性。
- NLog：收集 NLog 屬性做為自訂屬性。SequenceID 不再是自訂屬性 (它會集中為 telemetry.Sequence)。Timestamp 不再是自訂屬性。 

## 1\.2.5 版
- 第一個開放原始碼版本：參考 Application Insights API 1.2.3 版或更高版本。

<!---HONumber=AcomDC_0107_2016-->