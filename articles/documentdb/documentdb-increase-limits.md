<properties 
	pageTitle="要求增加 DocumentDB 帳戶限制 | Azure" 
	description="了解如何要求調整 DocumentDB 限制，包括允許的集合數目、預存程序和查詢子句。" 
	services="documentdb" 
	authors="stephbaron" 
	manager="johnmac" 
	editor="monicar" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/04/2015" 
	ms.author="stbaro"/>

# 要求增加 DocumentDB 帳戶限制

[Microsoft Azure DocumentDB](http://azure.microsoft.com/services/documentdb/) 有一組預設限制和配額強制執行。數種配額可以透過聯絡 Azure 支援人員來進行調整。本文說明如何要求提高帳戶限制。

閱讀本文後，您將能夠回答下列問題：

-	哪種 DocumentDB 帳戶配額可以透過聯絡 Azure 支援人員來進行調整？
-	如何要求 DocumentDB 帳戶配額調整？

##<a id="AdjustableQuotas"></a> 可調整的 DocumentDB 帳戶配額

下表說明可以透過聯絡 Azure 支援人員來進行調整的 DocumentDB 配額：

|實體 |配額 (標準優惠)|
|-------|--------|
|資料庫帳戶 |5
|每個集合的預存程序、觸發程序和 UDF 數目 |各 25 個
|每個資料庫帳戶的集合上限 |100
|每個資料庫的文件儲存體上限 (100 個集合) |1 TB
|每個查詢的 UDF 數目上限 |1
|每個查詢的 JOIN 數目上限 |2
|每個查詢的 AND 子句數目上限 |5
|每個查詢的 OR 子句數目上限 |5
|每個 IN 運算式的值數目上限 |100
|每分鐘建立集合的數目上限 |5
|每分鐘縮放作業的數目上限 |5

##<a id="RequestQuotaIncrease"></a> 要求配額調整
下列步驟說明如何要求配額調整。

1. 在 [Azure Preview 入口網站](https://portal.azure.com)中，按一下 [**瀏覽**]，然後按一下 [**說明 + 支援**]。

	![啟動說明和支援的螢幕擷取畫面](media/documentdb-increase-limits/helpsupport.png)

2. 在 [**說明 + 支援**] 刀鋒視窗中，按一下 [**取得支援**]。

	![建立支援票證的螢幕擷取畫面](media/documentdb-increase-limits/getsupport.png)

3. 在 [**新增支援要求**] 刀鋒視窗中，按一下 [**要求類型**]，然後在 [**要求類型**] 刀鋒視窗中，按一下 [**配額**]。

	![支援票證要求類型的螢幕擷取畫面](media/documentdb-increase-limits/supportrequest1.png)

4. 在 [**訂用帳戶**] 刀鋒視窗中，選擇裝載 DocumentDB 帳戶的訂用帳戶。

	![支援票證訂用帳戶選擇器的螢幕擷取畫面](media/documentdb-increase-limits/supportrequest2.png)

5. 在 [**資源**] 刀鋒視窗中，選擇 [**DocumentDB 帳戶**]。

	![支援票證資源選擇器的螢幕擷取畫面](media/documentdb-increase-limits/supportrequest3.png)

6. 在 [**支援計劃**] 刀鋒視窗中，選擇 [**配額免費支援**]。

	![支援票證支援計劃選擇器的螢幕擷取畫面](media/documentdb-increase-limits/supportrequest4.png)

7. 在 [**問題**] 刀鋒視窗中，選擇問題類別 [**配額或核心增加要求 DocumentDB**]。

	![支援票證問題類別選擇器的螢幕擷取畫面](media/documentdb-increase-limits/supportrequest5.png)

8. 在 [**說明**] 刀鋒視窗中，輸入要求的說明。請務必包含您所要求的特定配額調整，以及應該要進行調整的帳戶。

	![支援票證說明文字方塊的螢幕擷取畫面](media/documentdb-increase-limits/supportrequest6.png)

9. 按一下 [建立]。

	![建立支援票證按鈕的螢幕擷取畫面](media/documentdb-increase-limits/supportrequest7.png)

建立支援票證之後，您應該會透過電子郵件收到支援要求編號。您也可以按一下 [**說明 + 支援**] 刀鋒視窗中的 [**支援要求**] 來檢視支援要求。

![支援要求刀鋒視窗的螢幕擷取畫面](media/documentdb-increase-limits/supportrequest8.png)
  

##<a name="NextSteps"></a> 後續步驟
- 若要深入了解 DocumentDB，請按一下[這裡](http://azure.com/docdb)。
 

<!---HONumber=July15_HO4-->