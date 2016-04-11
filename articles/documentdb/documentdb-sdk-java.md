<properties 
	pageTitle="DocumentDB Java SDK | Microsoft Azure" 
	description="了解所有 Java SDK 相關資訊，包括 發行日期、停用日期及 DocumentDB Java SDK 每個版本之間的變更。" 
	services="documentdb" 
	documentationCenter="java" 
	authors="ryancrawcour" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.date="03/30/2016" 
	ms.author="ryancraw"/>

# DocumentDB SDK

> [AZURE.SELECTOR]
- [.NET SDK](documentdb-sdk-dotnet.md)
- [Node.js SDK](documentdb-sdk-node.md)
- [Java SDK](documentdb-sdk-java.md)
- [Python SDK](documentdb-sdk-python.md)

##DocumentDB Java SDK

<table>
<tr><td>**下載**</td><td>[Maven](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb)</td></tr>
<tr><td>**參與**</td><td>[GitHub](https://github.com/Azure/azure-documentdb-java/)</td></tr>
<tr><td>**說明文件**</td><td>[Java SDK 參考文件](http://azure.github.io/azure-documentdb-java/)</td></tr>
<tr><td>**開始使用**</td><td>[開始使用 Java SDK](documentdb-java-application.md)</td></tr>
<tr><td>**目前支援的執行階段**</td><td>[JDK 7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html)</td></tr>
</table></br>

## 版本資訊

### <a name="1.6.0"/>[1\.6.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.6.0)
- 實作[分割集合](documentdb-partition-data.md)和[使用者定義的效能等級](documentdb-performance-levels.md)。 

### <a name="1.5.1"/>[1\.5.1](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.5.1)
- 修正 HashPartitionResolver 中的錯誤以產生與其他 SDK 一致的 little-endian 雜湊值。

### <a name="1.5.0"/>[1\.5.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.5.0)
- 新增「雜湊和範圍」分割區解析程式來協助將應用程式跨多個分割區分區。

### <a name="1.4.0"/>[1\.4.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.4.0)
- 實作 Upsert。已新增新的 upsertXXX 方法以支援 Upsert 功能。
- 實作以識別碼為基礎的路由。不需變更公用 API，所有變更皆為內部變更。

### <a name="1.3.0"/>1.3.0
- 已略過版本以配合其他 SDK 的版本號碼

### <a name="1.2.0"/>[1\.2.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.2.0)
- 支援地理空間索引
- 驗證所有資源的識別碼屬性。資源的識別碼不能包含 ?、/、#、\\ 等字元，或在結尾處使用空格。
- 將新標頭「索引轉換進度」加至 ResourceResponse。

### <a name="1.1.0"/>[1\.1.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.1.0)
- 實作 V2 索引原則

### <a name="1.0.0"/>[1\.0.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.0.0)
- GA SDK

## 發行和停用日期
Microsoft 至少會在停用 SDK 的 **12 個月**之前提供通知，以供順利轉換至較新/支援的版本。

新的功能與最佳化項目只會新增至目前的 SDK，因此建議您一律盡早升級至最新的 SDK 版本。

使用已停用之 SDK 的任何 DocumentDB 要求都將被服務拒絕。

> [AZURE.WARNING]
適用於 Java 之所有 **1.0.0** 之前的 Azure DocumentDB SDK 版本都將於 **2016 年 2 月 29 日停用**。

<br/>

| 版本 | 發行日期 | 停用日期 
| ---	  | ---	         | ---
| [1\.6.0](#1.6.0) | 2016 年 3 月 29 日 |--- | [1\.5.1](#1.5.1) | 2015 年 12 月 31 日 |--- | [1\.5.0](#1.5.0) | 2015 年 12 月 4 日 |--- | [1\.4.0](#1.4.0) | 2015 年 10 月 5 日 |--- | [1\.3.0](#1.3.0) | 2015 年 10 月 5 日 |--- | [1\.2.0](#1.2.0) | 2015 年 8 月 5 日 |--- | [1\.1.0](#1.1.0) | 2015 年 7 月 9 日 |--- | [1\.0.1](#1.0.1) | 2015 年 5 月 12 日 |--- | [1\.0.0](#1.0.0) | 2015 年 4 月 7 日 |--- | 0.9.5 發行前版本 | 2015 年 3 月 9 日 | 2016 年 2 月 29 日 | 0.9.4 發行前版本 | 2015 年 2 月 17 日 | 2016 年 2 月 29 日 | 0.9.3 發行前版本 | 2015 年 1 月 13 日 | 2016 年 2 月 29 日 | 0.9.2 發行前版本 | 2014 年 12 月 19 日 | 2016 年 2 月 29 日 | 0.9.1 發行前版本 | 2014 年 12 月 19 日 | 2016 年 2 月 29 日 | 0.9.0 發行前版本 | 2014 年 12 月 10 日 | 2016 年 2 月 29 日

## 常見問題集
[AZURE.INCLUDE [documentdb-sdk-faq](../../includes/documentdb-sdk-faq.md)]

## 另請參閱

若要深入了解 DocumentDB，請參閱 [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) 服務頁面。

<!---HONumber=AcomDC_0330_2016-->