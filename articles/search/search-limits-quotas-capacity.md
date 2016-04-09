<properties
	pageTitle="Azure 搜尋服務中的服務限制 | Microsoft Azure | 雲端託管搜尋服務"
	description="容量計劃中使用的服務限制，以及 Azure 搜尋服務要求和回應的最大限制。"
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags="azure-portal"/>

<tags
	ms.service="search"
	ms.devlang="NA"
	ms.workload="search"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.date="02/28/2016"
	ms.author="heidist"/>

# Azure 搜尋中的服務限制

儲存體與工作負載的最大限制，以及索引、文件和其他物件的數量上限，皆取決於您是否在**免費**、**基本**，還是**標準**定價層中新增 Azure 搜尋服務。

- **免費**是 Azure 訂用帳戶隨附的多租用戶共用服務。此為針對現有訂用帳戶提供的免費選項，無須額外費用，可讓您試驗服務後再註冊專用資源。 
- **基本 (預覽)**會針對生產工作負載提供規模較小的專用計算資源。此層級目前為預覽狀態，[在預覽期間享有五折的優惠費率](https://azure.microsoft.com/pricing/details/search/)。
- **標準**是在專用的機器上執行，在各層級具有更多的儲存和處理容量，包括最小組態。標準有兩個層級 (S1 和 S2)。 

所有層級都可以在[入口網站佈建](search-create-service-portal.md)，但 S2 除外，這需要支援票證。將電子郵件傳送至 azuresearch_contact@microsoft.com 即可開始使用 S2。

## 層級限制

[AZURE.INCLUDE [azure-search-limits](../../includes/azure-search-limits-all.md)]

## API 金鑰限制

API 金鑰可用於服務驗證。有兩種類型。系統管理金鑰是在要求標頭中指定，並會授與完整的服務讀寫存取。查詢金鑰為唯讀並在 URL 上指定，且通常會發佈到用戶端應用程式。

- 每個服務最多 2 個系統管理金鑰
- 每個服務最多 50 個查詢金鑰

## 要求限制

- 每個要求最多 16 MB <sup>1</sup>
- 最長 8 KB 的 URL 長度
- 每個索引上傳、合併、或刪除批次最多包含 1000 個文件
- $orderby 子句中最多 32 個欄位
- 最大搜尋詞彙的大小是 utf-8 編碼文字的 32766 個位元組 (32 KB 減 2 個位元組)

## 回應限制

- 每一頁搜尋結果最多傳回 1000 個文件
- 每個建議 API 要求最多傳回 100 個建議

<sup>1</sup> 在 Azure 搜尋服務中，要求的主體最大上限為 16 MB，賦予個別欄位或「集合」特定限制則不受理論上的限制 (如需欄位的組合和限制的詳細資訊，請參閱[支援的資料類型](https://msdn.microsoft.com/library/azure/dn798938.aspx))。

## 每秒查詢次數

雖然粗略的估計值會在定價頁面和以上提供的[層級限制](#TierLimits)圖表中提供，但是實際的每秒查詢次數 (QPS) 難以判斷，尤其是在根據可用的頻寬和系統資源的爭用而輸送量有所不同的免費共用服務中。備份共用服務的計算和儲存體資源由多個訂閱者共用，因此解決方案的 QPS 會因有多少其他工作負載在同時執行而隨時不同。

在標準層級中，由於可控制較多的參數，所以能更準確地估計 QPS。請參閱[管理搜尋解決方案](search-manage.md)中的最佳做法一節，取得如何計算工作負載 QPS 的指引。

<!---HONumber=AcomDC_0316_2016-->