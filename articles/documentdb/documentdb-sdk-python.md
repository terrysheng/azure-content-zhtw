<properties 
	pageTitle="DocumentDB Python SDK | Microsoft Azure" 
	description="了解所有 Python SDK 相關資訊，包括 發行日期、停用日期及 DocumentDB Python SDK 每個版本之間的變更。" 
	services="documentdb" 
	documentationCenter="python" 
	authors="ryancrawcour" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="11/16/2015" 
	ms.author="ryancraw"/>

# DocumentDB SDK

> [AZURE.SELECTOR]
- [.NET SDK](documentdb-sdk-dotnet.md)
- [Node.js SDK](documentdb-sdk-node.md)
- [Java SDK](documentdb-sdk-java.md)
- [Python SDK](documentdb-sdk-python.md)

##DocumentDB Python SDK

<table> <tr><td>**下載**</td><td>[PyPI](https://pypi.python.org/pypi/pydocumentdb)</td></tr> <tr><td>**參與**</td><td>[GitHub](https://github.com/Azure/azure-documentdb-python)</td></tr> <tr><td>**文件**</td><td>[Python SDK 參考文件](http://azure.github.io/azure-documentdb-python/)</td></tr> <tr><td>**開始使用**</td><td>[ Python SDK](documentdb-python-application.md)</td></tr> <tr><td>**目前支援的平台**</td><td>[Python 2.7](https://www.python.org/download/releases/2.7/)</td></tr> </table></br>

## 版本資訊

### <a name="1.4.2"/>[1\.4.2](https://pypi.python.org/pypi/pydocumentdb/1.4.2)
- 實作 Upsert。已新增新的 UpsertXXX 方法以支援 Upsert 功能。
- 實作以識別碼為基礎的路由。不需變更公用 API，所有變更皆為內部變更。

### <a name="1.2.0"/>[1\.2.0](https://pypi.python.org/pypi/pydocumentdb/1.2.0)
- 支援地理空間索引。
- 驗證所有資源的識別碼屬性。資源的識別碼不能包含 ?、/、#、\\ 等字元，或在結尾處使用空格。
- 將新標頭「索引轉換進度」加至 ResourceResponse。

### <a name="1.1.0"/>[1\.1.0](https://pypi.python.org/pypi/pydocumentdb/1.1.0)
- 實作 V2 索引原則

### <a name="1.0.1"/>[1\.0.1](https://pypi.python.org/pypi/pydocumentdb/1.0.1)
- 支援 Proxy 連線

### <a name="1.0.0"/>[1\.0.0](https://pypi.python.org/pypi/pydocumentdb/1.0.0)
- GA SDK

## 發行和停用日期
Microsoft 將至少在 SDK 停用之前 **12 個月** 提供通知，以供順利轉換至較新/支援的版本。

新的功能與最佳化項目只會新增至目前的 SDK，因此建議您一律盡早升級至最新的 SDK 版本。

使用已停用之 SDK 的任何 DocumentDB 要求都將被服務拒絕。

> [AZURE.WARNING]適用於 Python 之所有 **1.0.0** 之前的 Azure DocumentDB SDK 版本都將於 **2016 2 月 29 日停用**。

<br/>

| 版本 | 發行日期 | 停用日期 
| ---	  | ---	         | ---
| [1\.4.2](#1.4.2) | 2015 年 10 月 6 日 |---
| [1\.4.1](#1.4.1) | 2015 年 10 月 6 日 |---
| [1\.2.0](#1.2.0) | 2015 年 8 月 6 日 |---
| [1\.1.0](#1.1.0) | 2015 年 7 月 9 日 |---
| [1\.0.1](#1.0.1) | 2015 年 5 月 25 日 |---
| [1\.0.0](#1.0.0) | 2015 年 4 月 7 日 |---
| 0.9.4-prelease | 2015 年 1 月 14 日 | 2016 年 2 月 29 日
| 0.9.3-prelease | 2014 年 12 月 9 日 | 2016 年 2 月 29 日
| 0.9.2-prelease | 2014 年 11 月 25 日 | 2016 年 2 月 29 日
| 0.9.1-prelease | 2014 年 9 月 23 日 | 2016 年 2 月 29 日
| 0.9.0-prelease | 2014 年 8 月 21 日 | 2016 年 2 月 29 日

## 常見問題集
[AZURE.INCLUDE [documentdb-sdk-faq](../../includes/documentdb-sdk-faq.md)]

## 另請參閱

若要深入了解 DocumentDB，請參閱 [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) 服務頁面。

<!---HONumber=AcomDC_1203_2015-->
