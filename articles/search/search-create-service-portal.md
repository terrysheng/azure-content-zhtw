<properties
	pageTitle="使用 Azure 入口網站建立 Azure 搜尋服務 | Microsoft Azure | 雲端託管搜尋服務"
	description="了解如何使用 Azure 入口網站佈建 Azure 搜尋服務。"
	services="search"
	authors="ashmaka"
	documentationCenter=""/>

<tags
	ms.service="search"
	ms.devlang="NA"
	ms.workload="search"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.date="03/10/2016"
	ms.author="ashmaka"/>

# 使用 Azure 入口網站建立 Azure 搜尋服務

本指南將逐步引導您完成使用 [Azure 入口網站](https://portal.azure.com/)建立 (或佈建) Azure 搜尋服務的程序。

本指南假設您已經有 Azure 訂用帳戶，可以登入 Azure 入口網站。

## I.在 Azure 入口網站中尋找 Azure 搜尋服務
1. 前往 [Azure 入口網站](https://portal.azure.com/)並登入。
1. 按一下左上角的加號 ("+")。
2. 選取 [資料 + 儲存體]
3. 選取 [Azure 搜尋服務]

![](./media/search-create-service-portal/find-search.png)

## II.選取服務名稱和您服務的 URL 端點
1. 您的服務名稱會是 Azure 搜尋服務端點 URL 的一部分，您將依據它進行 API 呼叫，以便管理和使用搜尋服務。
2. 在 [URL] 欄位中輸入您的服務名稱。服務名稱：
  * 只能包含小寫字母、數字或連字號 ("-")
  * 不能使用連字號 ("-") 作為前 2 個字元或最後一個字元
  * 不能包含連續的連字號 ("--")
  * 長度限制在 2 到 60 個字元之間


## III.選取您將保留服務的訂用帳戶
如果您有多個訂用帳戶，您可以選取哪一個訂用帳戶將包含此 Azure 搜尋服務。

## IV.選取服務的資源群組
建立新的資源群組或選取現有的資源群組。資源群組是 Azure 服務和一起使用之資源的集合。例如，如果您使用 Azure 搜尋服務來編製 SQL 資料庫的索引，那麼這兩個服務都應該是相同資源群組的一部分。

## V.選取將裝載您服務的位置
作為 Azure 服務，Azure 搜尋服務可以裝載在世界各地的資料中心。請注意，各地理位置的[價格可能不同](https://azure.microsoft.com/pricing/details/search/)。

## VI.選擇定價層
[Azure 搜尋服務目前提供多個定價層](https://azure.microsoft.com/pricing/details/search/)︰免費、基本或標準。每一層都有自己的[容量和限制](search-limits-quotas-capacity.md)。

在此情況下，我們已為服務選擇標準層。

## VII.選取 [建立] 按鈕，以佈建您的服務

![](./media/search-create-service-portal/create-service.png)

## VIII.調整您的服務

佈建完您的服務之後，您可以調整它以符合您的需求。如果您為 Azure 搜尋服務選擇標準層，則您可以在兩個維度調整服務︰複本和資料分割。如果您選擇基本層，則只能新增複本。

*__資料分割__*允許您的服務儲存及搜尋更多文件。

*__複本__*允許服務處理更高的搜尋查詢負載 - [服務需要 2 個複本才能達到唯讀 SLA，需要 3 個複本才能達到讀寫 SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/)。

1. 在 Azure 入口網站中前往 Azure 搜尋服務的管理刀鋒視窗
2. 在 [設定] 刀鋒視窗中，選取 [調整]。
3. 您可以藉由加入複本或資料分割來調整您的服務。
  * 您無法將服務調整超過 36 個搜尋單位。您的搜尋單位總數是您的複本和資料分割的乘積 (複本 * 資料分割 = 搜尋單位總數)。
  * 如果您選擇基本層，則只能調整到 3 個複本。基本的服務繫結至單一資料分割。

![](./media/search-create-service-portal/scale-service.png)

## 下一步
佈建 Azure 搜尋服務之後，您就可以[定義 Azure 搜尋服務索引](search-what-is-an-index.md)，以便上傳和搜尋您的資料。

<!---HONumber=AcomDC_0316_2016-->